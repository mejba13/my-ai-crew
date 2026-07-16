**BRAND:** mejba.me
**TITLE:** MCP Is Quietly Dead. Corsair Shows What Replaces It.
**META TITLE:** MCP Is Dead: Corsair and the RAG-Tool Future
**SLUG:** mcp-is-dead-corsair-rag-tools
**PRIMARY KEYWORD:** MCP is dead
**META DESCRIPTION:** I tested MCP at 40+ tools and watched it collapse. Here's why MCP is dead at scale and how Corsair's RAG approach actually fixes the context bloat problem.
**TAGS:** AI Agents, MCP, RAG, Open Source, AI Development

---

# MCP Is Quietly Dead. Corsair Shows What Replaces It.

I had forty-three tools wired into a single agent and I was watching it lose its mind in real time.

The task was stupid simple. "Pull the last ten emails from my Gmail and summarize anything related to the proposal I sent on Tuesday." A two-step job. Read email. Summarize. That's it. Instead, the agent called a Slack search tool. Then a calendar tool I'd connected for a different project. Then it tried to invoke `gmail_get_messages` — except that wasn't the function name. The actual tool was `read_gmail_inbox`. The agent had hallucinated a plausible-sounding API call from a schema it half-remembered through 38,000 tokens of upfront tool definitions.

I killed the run, opened the input log, and counted. Before my prompt had even reached the model, MCP had injected over 40,000 tokens of tool schemas into the context window. Forty thousand tokens. To answer a question about ten emails. The agent never had a chance.

That was the moment I stopped defending MCP and started looking for what comes next. I'm going to walk through what I found — including a small open-source project called Corsair that I think points at the real architecture for tool use in 2026. But first, you need to understand what actually broke. Because "MCP is dead" isn't hype. It's what the math says when you push the protocol past the toy demos.

## What MCP Was Supposed to Do

Anthropic shipped the Model Context Protocol in late 2024 with a clean, ambitious pitch. LLMs are brains without hands and legs. They can reason brilliantly about code, language, and strategy, but they can't post a tweet, read your inbox, or update a spreadsheet without external plumbing. MCP was supposed to be that plumbing — standardized, vendor-neutral, and universal.

The mechanic was straightforward. You expose a tool. The tool advertises a JSON schema describing its name, parameters, and what it does. The MCP client injects every available tool's schema into the model's context window at the start of the conversation. The model picks the right tool, generates a structured call, and the runtime executes it. Add tool. Get capability. Repeat.

I bought into it completely. Last year I wrote about [the three MCPs that turned Claude into my operations hub](https://www.mejba.me/blog/must-have-mcps-claude-code) — Canva, Zapier, and Stripe wired together changed how I worked for about six months. With three or four tools, MCP feels magical. The protocol disappears. You ask for things in plain English and they happen.

But three or four tools is the toy version. The moment you scale up — the moment you try to wire together the actual stack a working professional uses — the architecture cracks open in three specific ways.

## The Context Window Bloat Problem

Here is the number that ended the romance for me.

According to a 2025 benchmark from Scalekit, the same operation that took 1,365 tokens through a CLI cost 44,026 tokens through MCP. That's roughly 32x the token overhead, and almost all of it was schema injection — 43 tool definitions packed into context before the agent had read a single character of the user's actual question. Every other reputable analysis I've read since lands in the same neighborhood. CodeRabbit's engineering team measured single MCP servers eating 55,000+ tokens of schema upfront. Cyclr's research found that at 50+ tools, schemas can consume 5-7% of a 200K context window before the conversation starts.

Read those numbers carefully. Five to seven percent of your context — gone — before anyone has typed anything.

The problem is architectural, not implementation-level. MCP was designed around the assumption that the model needs to *see* every tool to *use* every tool. That assumption was reasonable when agents had four or five tools. It is catastrophic when they have forty. And forty is not the upper bound — that's roughly what a single working developer accumulates between GitHub, Slack, Linear, Sentry, their database, their email, their calendar, and their CMS.

I've watched my own setups balloon past sixty tools without trying. Each one looks "free" when you add it. Each one taxes every single future request whether you use it or not.

## When the Hallucinations Start

Here's the part most write-ups bury. Token cost is the boring problem. The interesting problem is what happens to the model's *reasoning* when its attention has to spread across dozens of tool schemas.

There's a pattern I've now seen repeatedly across both my own logs and the published research. Once context utilization crosses about 70%, the model's tool-selection accuracy collapses. It starts conflating parameters between similar tools. It calls the right tool with arguments from a different tool's schema. It invents tools that don't exist but sound like they should. And — this one is genuinely unnerving — it does all of this with full confidence. The hallucinated calls look exactly like the real ones.

The Scalekit-style benchmarks line up with academic work too. The RAG-MCP paper out of arXiv (2505.03275) ran a stress test where they fed an LLM a growing pool of MCP tool descriptions and watched selection accuracy fall off a cliff. With the full schema dump approach — the way MCP works today — they measured 13.62% tool-selection accuracy. With retrieval-based selection, the same model on the same queries hit 43.13%. More than triple the accuracy with less than half the prompt tokens.

That's not a tuning problem. That's a "the entire approach is wrong" problem.

I'll give you the most concrete example from my own testing. I had two tools wired in: `send_email` (via Gmail MCP) and `send_message` (via Slack MCP). Same verb structure. Different platforms. Different parameter shapes. About one in seven runs, the agent would generate a call to `send_email` with Slack's `channel` and `text` parameters. The runtime would reject it. The agent would apologize, retry, and sometimes succeed and sometimes fail in a different way. Every retry burned tokens. Every failure mode was unique. Debugging it felt like chasing ghosts.

When I trimmed back to twelve tools, the failure rate dropped to near zero. Twelve tools — for one agent doing one job. That's the practical ceiling MCP gives you in production.

## The Schema Fragmentation Tax

While MCP's spec is technically standardized, the reality of running it across providers in 2026 is messier than the marketing suggests.

I've now connected MCP servers from at least eight different vendors, and I can tell you with absolute certainty that "JSON schema" hides an ocean of inconsistency. Some servers return errors as structured objects. Some return errors as strings stuffed inside successful responses. Some servers paginate. Some don't, and silently truncate. Some servers honor the optional/required field distinction. Some treat everything as required and break if you omit anything. Authentication ranges from clean OAuth to "paste this token into a config file and pray."

Each one of these inconsistencies forces either the model or the developer to write defensive logic. Multiply that by the number of MCP servers you're running, and the "unified protocol" promise turns into "JSON schema soup with adapter code in the middle."

The deeper issue is that MCP standardized the *transport* but not the *semantics*. Two servers can both be valid MCP and behave nothing like each other. That's fine when you have one or two. It's an integration tax when you have twenty.

## More Builders Than Users

Here's the signal nobody in the MCP marketing materials wants to talk about. Look at the registries. Pulse MCP. Anthropic's Connectors Directory. Smithery. Glama. There are now thousands of MCP servers. Most of them have a handful of GitHub stars and almost no actual users.

The community is full of people *building* MCP servers. It's surprisingly empty of people running them in production at scale. The reason isn't mystery — it's the three problems I just walked through. The first time you try to wire fifteen of these things into one agent, you hit the wall. You quietly retreat to three or four tools, or you abandon MCP entirely and go back to direct API calls, or you start writing aggressive context-management code to compress what MCP injects.

I wrote about exactly this pattern in my piece on [how Context Mode fixed my Claude Code memory problem](https://www.mejba.me/blog/context-mode-claude-code-bloat). Context Mode is a clever fix for the symptom — it strips MCP tool outputs from context after they're consumed. But it doesn't fix the upstream problem of every schema being injected at startup. It just keeps the bleeding from killing the patient.

When the workaround ecosystem becomes bigger than the protocol itself, the protocol is in trouble.

## The Mental Model Shift: From Library Card to Encyclopedia

Here's the framing that finally clicked for me. MCP treats tools like books you carry around. Every time you start a conversation, you load every book you might possibly need into your backpack, and then you reason while crushed under their weight. The model has to consider all of them, all the time, just in case.

There's an obvious better way. Don't carry the books. Carry an index. When you need information, look up which book has it, fetch only that book, and read.

This is the encyclopedia model. The model knows what books *exist* — their titles, a one-line description, the rough domain — and fetches the full schema only when a query genuinely calls for it. This is exactly the architecture RAG (retrieval-augmented generation) brought to document Q&A several years ago. We just didn't apply it to tool selection because the original MCP design didn't anticipate the scale.

Apply RAG to tools instead of documents and the math inverts. Instead of every conversation starting with 40,000 tokens of schema, it starts with maybe 200 tokens of tool *index*. When the user asks for something, a vector search retrieves the two or three relevant tool schemas, those get injected into context, and the model picks one. Hallucination rates drop because the model isn't drowning in similar-sounding options. Token costs drop because you're paying for what you use, not what you might use. Tool count becomes effectively unlimited.

Karpathy's been pointing at related ideas for a while — I covered some of his RAG architecture thinking when I wrote about [building a personal RAG knowledge base in Obsidian](https://www.mejba.me/blog/karpathy-obsidian-rag-knowledge-base). Tools are just another kind of retrievable artifact. We should treat them that way.

## Enter Corsair

Corsair is an open-source project on GitHub (github.com/corsairdev/corsair) that implements exactly this pattern. I'm not going to pretend it's a polished product yet — it isn't. The repo is young, the docs are thin, and the community is small. But the architecture is the most honest answer I've seen to the questions MCP can't answer.

Here's how it works at a mechanical level.

You install Corsair as a layer between your agent and your tools. It ships with a catalog of plugin definitions for common services — Slack, Gmail, GitHub, Google Calendar, and more being added. Each plugin's metadata lives in a vector index. When your agent gets a query, Corsair runs a semantic search over the plugin catalog first, retrieves only the relevant plugin's tool schemas, and exposes those to the model. Everything else stays out of context.

To the agent, Corsair looks like a tiny number of meta-tools — search the catalog, fetch a plugin, execute a call. To you as the developer, exposing twenty integrations is roughly one line of code. The complexity sits inside Corsair's runtime, where it belongs.

The credential model is the part I appreciated most. Corsair stores authentication locally in an on-file database. No mandatory cloud relay. No third-party dashboard managing your OAuth tokens. If you want to wire your personal Gmail and your client's GitHub into the same agent, the secrets stay on your machine. For anyone who's built agents touching sensitive systems, this matters more than any feature spec line.

## The Dev Experience Difference

Let me make the contrast concrete with how a typical wiring job feels under each approach.

Under MCP, adding a service is a multi-step ceremony. Find the right MCP server. Read its README. Configure it in your client (Claude Desktop, Cursor, custom runtime — they all differ slightly). Authenticate. Restart your client. Test. Realize a parameter is named slightly differently than the docs say. Debug. Realize the server's response format doesn't match the standard. Write defensive parsing. Restart again. Now do this for the next service. And the next.

Under the Corsair-style retrieval pattern, adding a service is closer to "register the plugin, store the credential, done." The agent doesn't need to be reconfigured. The context window doesn't grow. Nothing else in the system has to know.

I want to be precise here because I'm genuinely trying not to oversell. Corsair specifically is early. The plugin catalog is small. You will hit rough edges if you try to use it for something niche today. But the *pattern* — RAG-driven tool retrieval — is what every serious agent infrastructure project I've looked at is converging on. Anthropic itself published research recently on lazy schema loading and dynamic tool gating. The arXiv paper "Tool Attention Is All You Need" (2604.21816) makes the same architectural argument from a different angle. The direction is set, even if the leading implementation hasn't fully crystallized yet.

## Where MCP Still Makes Sense

I want to be fair, because I think a lot of "X is dead" posts overstate their case and lose credibility. MCP is not useless. It's just badly suited for the scale most of us are pushing it toward.

MCP is genuinely good when you have a small, fixed, curated set of tools — say, three to seven — that you want every conversation to have access to. The schema-upfront model is fine at that scale. Latency is low. The model's attention has room to spread without breaking. The protocol's standardization advantages outweigh its overhead. If you're building a focused agent that does one thing — a code reviewer with three tools, a writing assistant with four — MCP is fine. It's possibly the right answer.

MCP also makes sense as a *backend*. You can imagine retrieval systems like Corsair speaking MCP under the hood for the actual tool invocation, while the discovery layer above it operates on retrieval principles. The protocol becomes infrastructure rather than the user-facing model. That's likely where this all lands long-term.

What MCP is *not* good for is the actual job most ambitious agents need to do — coordinate across dozens of services, dynamically scope which tools are relevant to which task, and stay below the context-utilization threshold where reasoning collapses. For that workload, the schema-injection model is structurally wrong, and no amount of context compression saves it.

## What I'm Doing Now

I've split my own agent infrastructure into two tracks. For tightly scoped, single-purpose agents — my code review bot, my screenshot-to-CSS converter, my email triage agent — I'm staying on MCP. Three to five tools each. No retrieval needed. The protocol works.

For my general-purpose operations agent — the one that needs to touch email, calendar, GitHub, my CMS, my analytics, my CRM, and a dozen other systems — I've moved to a retrieval-based architecture. Corsair-style today, possibly something more mature in six months. The token bill alone justified the migration. The sharp drop in hallucinated tool calls justified it twice over.

The mental model I now apply to every new agent is this: how many tools will it need? If the answer is "five or fewer, ever," MCP is fine. If the answer is "I genuinely don't know and probably more than ten," I reach for retrieval. The crossover point is somewhere around eight tools in my experience, and it's not graceful — performance degrades fast once you cross it.

That single decision point has saved me hours of debugging hallucinated calls and reasoning collapses. I wish someone had handed it to me a year ago.

## Frequently Asked Questions

### Is MCP actually dead or just struggling at scale?
MCP is not dead for small, focused agents with three to seven tools — it works fine there. It is functionally dead for general-purpose agents that need access to dozens of services, because schema injection causes context bloat and tool-selection hallucinations that retrieval-based approaches cleanly solve. Most production teams are quietly hybridizing.

### What is Corsair and is it production-ready?
Corsair is an open-source integration layer for AI agents (github.com/corsairdev/corsair) that uses RAG to dynamically retrieve only relevant tool schemas per query instead of injecting all of them upfront. It is early — small plugin catalog, evolving docs — but the architectural pattern it represents is what serious agent infrastructure is converging on.

### Why does adding more MCP tools make agents worse?
Each MCP tool injects 550-1,400 tokens of schema into context at conversation start. Past 50 tools, this consumes 5-7% of a 200K context window before the user's question even arrives. Once context utilization crosses roughly 70%, the model's attention fragments across similar-looking tools, hallucination rates climb, and tool-selection accuracy collapses.

### How does RAG-MCP improve tool selection accuracy?
The RAG-MCP paper (arXiv 2505.03275) showed that retrieval-based tool selection achieved 43.13% accuracy versus 13.62% for the schema-injection baseline on the same benchmark — more than triple the accuracy with less than half the prompt tokens. The model only sees the tools relevant to the current query, so its attention isn't fragmented.

### Should I migrate from MCP to a retrieval-based approach right now?
If your agent uses fewer than eight tools and works well, stay. If you're hitting hallucination bursts, climbing token costs, or planning to scale past ten tools, start prototyping a retrieval layer like Corsair now. The crossover point isn't graceful — performance degrades fast once you cross it, and the migration is easier before you have production traffic depending on the old architecture.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
