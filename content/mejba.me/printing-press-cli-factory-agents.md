**BRAND:** mejba.me
**TITLE:** Printing Press: The CLI Factory My Agents Now Run On
**META TITLE:** Printing Press CLI Factory for AI Agents (Tested)
**SLUG:** printing-press-cli-factory-agents
**PRIMARY KEYWORD:** Printing Press CLI factory
**META DESCRIPTION:** I tested Printing Press, the CLI factory that builds agent-native command-line tools for any site — even ones without an API. Here's what beat MCP.
**TAGS:** AI Agents, Claude Code, CLI Tools, Developer Productivity, Tool Review

---

# Printing Press: The CLI Factory My Agents Now Run On

I had a Claude Code agent burning roughly 40,000 tokens just to answer a single question about my Linear workspace.

Forty thousand tokens. To find out which tickets were assigned to me this sprint. Most of those tokens were the MCP tool definitions Claude had to load before it could even *consider* making the call — JSON schemas describing parameters my agent would never use, enums for filters I wasn't filtering on, descriptions of operations that had nothing to do with the question I asked. By the time the actual API request fired, the agent had already chewed through more context than the entire response would ever return.

I'd been hand-waving this away for weeks. "Token cost is the price of capability." Every agent builder I know was saying the same thing. Then someone on X dropped a link to a tool called Printing Press with a benchmark image attached. The image showed a CLI returning the same data for a fraction of the tokens — not 20% less, not half, but in some cases more than thirty times less. I clicked through expecting marketing fluff.

What I found was a different mental model for how agents should talk to the outside world.

I spent the next four days rebuilding three of my Claude Code agents to use Printing Press CLIs instead of their MCP servers. The agents got faster. The token bills dropped. And one of them — the one that used to fail roughly a quarter of the time on complex multi-step queries — stopped failing entirely.

Here's what I learned, what I broke along the way, and why I think the **Printing Press CLI factory** is one of the most useful agent infrastructure tools to ship in the past six months.

---

## Why CLIs Beat MCP for Agent Workflows (the Part That Surprised Me)

You have to understand what changed in 2026 before any of this makes sense.

When MCP first dropped, the pitch was beautiful — give your agent a single connector to anything that supports the protocol, and it can use the tools without you writing wrapper code. Slack, GitHub, Linear, Notion, Stripe. Plug in the server, the agent figures out the rest. I bought it. I built three production agents on MCP servers in late 2025. They worked.

But I noticed something about six weeks in. My monthly Anthropic bill kept climbing in a way that didn't match my usage growth. I was running fewer agent sessions, not more, and the cost per session was rising. When I dug into the breakdown, the answer was uncomfortable. A single MCP-backed Linear query was averaging 38,000 input tokens. The actual answer Claude needed back from Linear? About 600 tokens. Everything else was overhead — tool definitions, parameter schemas, retry context, error envelopes.

A team at Scalekit ran a controlled benchmark on this exact problem and published the numbers on March 11, 2026. They tested five read-only GitHub tasks against the `anthropic-sdk-python` repository, 25 runs per task per approach, three approaches: pure CLI, CLI + skill instructions, and MCP. The results were embarrassing for MCP.

The simplest task in the suite — "what language is this repo written in?" — cost the CLI agent 1,365 tokens. The MCP agent burned 44,026 tokens to answer the same question. That's a 32x multiplier on a one-line answer. Across the full benchmark suite, the token gap ranged from 4x on the cheapest task up to 32x on the most expensive. Reliability was even more brutal: the CLI approach completed 25 of 25 runs successfully. The MCP approach completed 18 of 25 — a 72% success rate driven mostly by TCP timeouts hitting GitHub's MCP server. ([Scalekit's full benchmark](https://www.scalekit.com/blog/mcp-vs-cli-use) is worth reading if you want every task broken out.)

At 10,000 monthly operations, the same workload cost $3.20 on CLI and $55.20 on MCP. A 17x cost difference for identical output.

That benchmark only covers GitHub. But the underlying mechanic is the same for every protocol-based tool surface: MCP requires the agent to load the entire tool catalog into context before deciding what to call. CLIs don't. CLIs are how shells have worked for fifty years — you type `gh pr list --state merged --author me` and you get back exactly the rows you asked for, in 200 tokens of clean text, with no preamble describing what `gh` could theoretically do.

Agents trained on the public web have already seen millions of shell sessions. They know how CLIs work natively. MCP is the unfamiliar protocol they have to be taught.

So the real question stops being "MCP or CLI?" The real question becomes: where do I get a CLI for every site I want my agent to touch?

That's the gap **Printing Press CLI factory** is built to fill.

---

## What Printing Press Actually Is

Printing Press is two things stacked on top of each other.

The first thing is a **library** — 63 pre-built, agent-native CLIs covering everything from Linear and Shopify to ESPN, Hacker News, Craigslist, Notion, Substack, and a flight aggregator that combines Google Flights and Kayak nonstop search. Each CLI in the library has been generated, tested, and either endorsed by the maintainer or community-contributed through a public GitHub repo. You install the ones you need, and your agent gets a token-efficient interface to that platform.

The second thing — the part that genuinely changed how I think about this category — is a **factory**. You point Printing Press at any site, API spec, or HAR file, and it generates a complete CLI for that target in a few minutes. From a Swagger spec, you get a typed Go binary with subcommands matching the API surface. From a website with no public API at all, you get a CLI that scrapes the relevant endpoints intelligently. From a HAR file you captured in DevTools, you get a CLI that replays those exact authenticated calls.

The output of the factory isn't just a CLI either. Each generated CLI ships as four artifacts in a single command:

1. A **Go binary** you can install via `go install`
2. A **Claude Code skill** with usage hints baked in
3. An **OpenClaw skill** for that ecosystem
4. An **MCP server** wrapper for teams that still prefer the MCP control plane

The official tagline on [printingpress.dev](https://printingpress.dev) is "Print the best agent-designed CLI of all time," and after using it for two weeks, I think that framing is more accurate than it has any right to be. These CLIs aren't human-first tools that happen to be agent-callable. They're designed from the first character for an LLM to read.

Three design choices give that away the moment you use one.

**Compact output.** Every Printing Press CLI accepts a `--compact` flag that drops to high-gravity fields only, returning 60-80% fewer tokens than the full response. When my Linear agent calls `linear issues list --compact`, it gets back five fields per ticket — id, title, status, assignee, priority — instead of the 40+ fields the API exposes. The agent never needed the other 35 fields. Now it doesn't pay for them.

**Typed exit codes.** Every command returns a known set of integer exit codes: 0 for success, 2 for usage error, 3 for not found, 4 for auth failure, 5 for upstream API error, 7 for rate limited. Agents can branch on these without parsing English error text. When my agent hits a 4, it triggers an auth refresh skill. When it hits a 7, it backs off. No prompt-engineering to "interpret what the error means."

**Local SQLite mirror.** This is the one I underestimated. Printing Press CLIs sync the relevant data they fetch into a local SQLite database, so the next query doesn't have to hit the network. Compound queries — "find me all merged PRs by these three contributors in the last 30 days, grouped by repository" — run in milliseconds against the local mirror instead of fanning out to multiple API calls. For agents that ask the same kind of question repeatedly across a session, this changes the response time profile from seconds to instants.

That last detail is the one that turns a CLI factory into something more interesting. Most agent tooling treats every call as stateless. Printing Press treats the agent's session as a stateful conversation with a local cache of the world.

---

## The Setup (and the One Thing That Tripped Me Up)

The install path is straightforward — but there's a Go version requirement that took me ten minutes to debug, so let me save you the trip.

Printing Press is a Go program. You need Go installed to use it, and as of May 2026 the project requires **Go 1.26.3 or newer**. I had Go 1.24.5 sitting on my Mac from a previous project. The install command failed with a cryptic generics error that didn't say "your Go version is too old," it just complained about a constraint type. A fresh `brew install go` and a terminal restart fixed it.

Once Go is current, the install is a single command:

```bash
go install github.com/mvanhorn/cli-printing-press/v4/cmd/printing-press@latest
```

That installs the factory binary itself. You verify it landed correctly:

```bash
printing-press --version
```

If that prints a version string, you're set. If your shell can't find the binary, your `$GOPATH/bin` (or `$GOBIN`) isn't on your `PATH` — add it and re-source your shell config.

To install one of the 63 pre-built CLIs from the library, you run:

```bash
printing-press install linear
printing-press install espn
printing-press install hackernews
```

Each install pulls the CLI source from the [official library repo](https://github.com/mvanhorn/printing-press-library), compiles it locally, and drops the binary in your path. Authentication is handled per-CLI — Linear uses an API key stored in an env var, ESPN works without auth, the Shopify CLI walks you through an OAuth flow on first use, and a few of the scraping-based CLIs (Craigslist, certain school community sites) use Chrome session cookies because the underlying sites have no public API.

That auth diversity is intentional and worth understanding. The factory generates whatever auth flow is appropriate for the target — API key when there's a documented API, OAuth when the site supports it, cookie auth when scraping is the only option. You don't have to know in advance which one you'll get. The factory inspects the target and picks.

There's a built-in diagnostic command that's worth running once you've installed a few CLIs:

```bash
printing-press auth doctor
```

That walks through every installed CLI, checks the relevant env vars and tokens, and reports which ones are missing or expired. The first time I ran it after rebuilding my agent stack, it caught two stale tokens I'd forgotten to rotate. Tiny detail. Saved me a debugging session a week later when the agent would have failed silently.

If you've read my piece on [advanced Claude Code agent skills](/agent-skills-advanced-claude-code), you'll recognize the same design philosophy here — make it easy for the agent to self-diagnose, because the alternative is the agent quietly failing mid-task and you finding out from a missed deadline.

---

## What It's Like to Actually Use One

Theory is theory. Let me walk you through what running an agent on Printing Press CLIs feels like in practice, because the experience is qualitatively different from MCP and that difference is what convinced me.

I'll use the Hacker News CLI for this, since it's free, requires no auth, and shows the pattern clearly.

After installing it:

```bash
printing-press install hackernews
```

You can run it directly from your terminal to confirm it works:

```bash
hackernews top --compact --limit 5
```

The output is dense, structured, and roughly 200 tokens for five stories — id, title, score, author, comments count, url. No JSON envelope. No metadata wrapper. Just the rows.

```
1 "Why CLI Tools Are Beating MCP for AI Agents" 487 dang 142 https://...
2 "Show HN: I Built a Local-First Search Engine" 312 jstewart 89 https://...
3 "The Forgotten 1980s Database That Powered..." 298 patio11 67 https://...
4 "GPT-7 Internal Benchmark Leak" 264 swyx 213 https://...
5 "What I Learned Reviewing 1000 Pull Requests" 251 gergely 41 https://...
```

Now here's where it gets interesting. To wire that into a Claude Code agent, you don't write tool wrappers. You don't define schemas. You don't run an MCP server. You just tell Claude that the CLI exists.

The simplest version is a one-line skill in your `.claude/skills/hackernews.md`:

```markdown
---
name: hackernews
description: Query Hacker News for top stories, comments, and user activity.
---

The `hackernews` CLI is installed. Common commands:
- `hackernews top --compact --limit N` — top N stories
- `hackernews story <id>` — full story with comments
- `hackernews user <username>` — user submissions and karma

Always use `--compact` unless the user explicitly asks for full output.
```

That's it. That's the entire integration. When Claude needs Hacker News data, it sees this skill, calls `hackernews top --compact --limit 10` from the bash tool, and pipes the 200-ish tokens of output back into context. No 40,000-token tool catalog. No MCP overhead. No JSON parsing.

Compare this to the equivalent MCP setup, which would require running a Hacker News MCP server, registering it with your agent runtime, exposing every operation as a separate tool definition, and paying the token cost of those definitions on every single agent session whether the agent uses them or not.

This is what Printing Press' creator means when he says CLIs have "muscle memory" for agents. Claude has read millions of shell transcripts. It knows what `--compact`, `--limit`, and `top` mean intuitively. It does not need to be taught what `hackernews_get_top_stories(limit: int)` means — it has to *parse* that.

---

## Building a Custom CLI With the Factory (the Live Demo I Ran)

The library is the easy win. The factory is where the unfair advantage hides.

I'd been wanting an agent-callable interface to a local school community site that one of my projects integrates with. The site has no API. There's no Swagger spec. The only way to get data out historically was to load the page in a browser, parse the HTML, and pray nothing changed in the markup. That's exactly the kind of brittle integration agents handle terribly.

So I tried the factory.

```bash
printing-press https://[community-site].example
```

Printing Press fired up a browser session, walked through the public surface of the site, identified the patterns it could scrape reliably, asked me a few clarifying questions about which routes I cared about (posts, comments, member directory), and then generated a complete Go CLI in about four minutes.

The output was four artifacts dropped into a `./cli/` folder:

- `community.go` — the CLI source
- `claude-skill.md` — a Claude Code skill describing the commands
- `openclaw-skill.json` — equivalent for OpenClaw
- `mcp-server.go` — an MCP wrapper for anyone who still wants to use it that way

I compiled the binary, set up the cookie auth (the site uses session cookies — the factory detected that and generated a `community auth login` flow that walks you through it), and within ten minutes had an agent reading and posting to that site.

For a more standard test, I pointed the factory at the public Hacker News API spec. That generated a CLI that was nearly identical to the one in the official library — same compact output, same exit codes, same SQLite mirror — proving the factory output is consistent with the curated library quality.

If you want a heavier real-world demo, [Matt VanHorn's launch thread](https://x.com/dotta/status/2052455379494441032) walks through building a Contact GOAT CLI that does verified email lookups in a single factory run.

The thing I want you to internalize is this: every site your agent currently can't talk to because there's no API is now a five-minute factory run away from being agent-callable. That changes the universe of automations worth building.

---

## What I Got Wrong on My First Build (the Honest Part)

I want to share a mistake I made the first week, because if you're going to use this tool, you'll probably be tempted to make the same one.

My instinct, coming from MCP-land, was to install every Printing Press CLI I might ever need into a single global agent setup. Linear, ESPN, Hacker News, Notion, Shopify, GitHub, Substack, the works. Tool-bloat thinking. "More capability = better agent."

That's exactly wrong here, and the reason is subtle.

The whole point of CLIs over MCP is **lazy discovery**. The agent doesn't load every tool's definition upfront — it discovers the CLI exists when a relevant skill mentions it, then runs `command --help` to learn the operations on demand. If you stuff a project's `.claude/skills/` folder with 15 different CLI skill files, you've recreated the MCP problem with extra steps. Every session loads every skill description, and your token savings evaporate.

The right pattern is **per-project CLI installation**. The agent for my SaaS project gets the Linear CLI and the Stripe CLI and nothing else. The agent for my newsletter gets the Substack CLI and the Convertkit CLI and nothing else. The agent for the SEO automation I wrote about in [my SEO content automation post](/automate-seo-content-claude-code) gets the Ahrefs CLI, the GSC CLI, and the Notion CLI — three tools, scoped to one job.

That scoping is the practical version of what Anthropic engineers describe when they talk about context as the agent's working memory. Bloat the memory, and the model spends more cycles deciding what to ignore than what to do. I covered the broader version of this in [my agent cost optimization guide](/ai-agent-cost-optimization-guide) — same mechanic, different surface.

The second mistake I made was relying on the local SQLite mirror without setting an explicit refresh cadence. The mirror is fast, but it can go stale on data that changes minute-to-minute (Linear ticket states, Stripe transactions, anything live). Each Printing Press CLI exposes a `--fresh` flag that bypasses the cache and forces a network call. For agents handling time-sensitive data, you want skills that explicitly use `--fresh` for those operations — and the cached path for everything else.

Once I tightened scoping and refresh cadence, the agents got faster *and* more accurate. Which is normally the kind of trade-off that doesn't exist.

---

## The Real Question: When Do You Still Want MCP?

I'm not going to tell you MCP is dead. The Scalekit benchmark authors are explicit that "MCP wins on security, multi-user auth, and enterprise governance." That's correct, and it matters.

If you're building an agent that runs server-side, handles auth for multiple users, needs centralized policy enforcement, and operates inside an enterprise that has compliance requirements, MCP is still the right choice. The protocol exists for a reason. Auth delegation through MCP is genuinely cleaner than wrapping CLI binaries in a multi-tenant context.

But that's a narrow slice of agent work. The bigger slice — the slice most builders reading this fall into — is single-user agents running locally or on a personal server, talking to platforms the user already has accounts on. For that slice, the math from the benchmarks is overwhelming: CLI is faster, cheaper, and more reliable on every dimension that matters.

Most production agent stacks I respect — Claude Code itself, Cursor, the Gemini CLI — already use both. They're CLI-first for the heavy lifting (file system, git, build commands, custom tooling) and reach for MCP only when there's a specific protocol-level reason. Printing Press fits that pattern. It gives you a CLI for every external service that doesn't already have one, while still emitting an MCP wrapper for the cases where governance demands it.

The framing I've settled on: CLI is the default. MCP is an exception you justify, not the other way around.

---

## How I'm Using It in Production Right Now

Two weeks in, here's where Printing Press has landed in my actual stack.

Every Claude Code agent I run now has a project-scoped `.claude/skills/` directory. Inside, one skill file per CLI it actually uses, with the command surface documented in plain language and `--compact` set as the default. Every CLI I install through the factory or library lives in `$GOBIN`, which is on my path, so the agent's bash tool can call them directly without any wrapper.

For the agents that need network-fresh data, skills explicitly call `--fresh`. For everything else, the SQLite mirror handles repeat queries instantly.

For services that don't have a Printing Press CLI yet — and there are still gaps; the library is at 63 CLIs as of May 2026 and growing weekly — I either fall back to the official CLI (`gh` for GitHub, `aws` for AWS, etc., which the agent already handles natively) or run the factory against the API spec myself and contribute the result back to the library if it's good enough.

Total token cost on the agent that used to burn 40,000 tokens per Linear query? Down to roughly 1,200 tokens. Reliability up from "fails one in four runs" to "haven't seen a failure in two weeks." And the agent feels faster in a way that's hard to quantify but obvious when you use it side by side with the old MCP version. That feeling is the round-trip latency disappearing because the local SQLite mirror is answering the question before the network knew there was a question.

If you're already deep in Claude Code workflows — and the patterns I've covered in posts like [the 1M context management deep dive](/claude-code-1m-context-management) are part of your toolkit — Printing Press slots in cleanly without forcing you to rethink the rest of your stack. It's a substitution, not a rewrite.

---

## Frequently Asked Questions

### What is Printing Press and who built it?
Printing Press is an open-source CLI factory and library that generates agent-native command-line tools from API specs, websites, or HAR files, built by Matt VanHorn and released as MIT-licensed code at [github.com/mvanhorn/cli-printing-press](https://github.com/mvanhorn/cli-printing-press). It outputs four artifacts per CLI: a Go binary, a Claude Code skill, an OpenClaw skill, and an MCP server wrapper. For the full setup walkthrough, see the Setup section above.

### Do I need to install Go to use Printing Press?
Yes — Printing Press is a Go program and requires Go 1.26.3 or newer to install, since it ships via `go install github.com/mvanhorn/cli-printing-press/v4/cmd/printing-press@latest`. Older Go versions will fail with a generics constraint error. On macOS, `brew install go` will install the latest stable release.

### How much do CLIs really save versus MCP for agents?
Scalekit's March 2026 benchmark recorded token savings ranging from 4x on the simplest task to 32x on the most expensive, with the CLI approach completing 25 of 25 test runs successfully versus 18 of 25 (72%) for MCP. At 10,000 monthly operations, the same workload cost roughly $3.20 on CLI versus $55.20 on MCP — a 17x cost difference. The numbers come from controlled, public testing — not vendor marketing.

### Can Printing Press build a CLI for a site that doesn't have an API?
Yes — the factory accepts a website URL or HAR file as input and generates a CLI that handles the underlying scraping or replay logic, including cookie-based auth when the target site has no public API. I tested this against a school community site with no documented API and had a working agent integration in under fifteen minutes.

### Should I replace all my MCP servers with Printing Press CLIs?
For single-user local agents, almost certainly yes — the cost, speed, and reliability gains are decisive. For multi-tenant production agents that need centralized auth governance and compliance enforcement, keep MCP for those specific surfaces and use Printing Press for everything else. Most production stacks today run a CLI-first hybrid.

### What pre-built CLIs ship with the library today?
The official library ships 63 CLIs as of May 2026, covering Linear, Shopify, ESPN, Hacker News, Notion, Substack, Slack, Stripe, Craigslist, Amazon, TikTok Shops, a Google Flights and Kayak nonstop aggregator, Ahrefs, Food52, and dozens more. The full current list lives in the [printing-press-library repo](https://github.com/mvanhorn/printing-press-library) and grows weekly through community contribution.

---

## What This Means for the Next Year of Agent Building

I want to leave you with a bigger frame than "this tool is good."

The era of agent tooling we just lived through — late 2024 through early 2026 — was defined by MCP. Every tool maker built an MCP server. Every agent runtime wired in MCP support. Every "AI agent integration" shipped as an MCP package. That made sense at the time. The protocol gave us a common language for tool discovery before agents had been deployed widely enough for the cost and reliability problems to surface.

Those problems are surfacing now. Anyone running production agents at any real volume has felt them. The benchmarks are catching up to the experience.

What Printing Press represents — and what tools like it will represent over the next year — is the swing back toward primitives the operating system already gives you for free. Shells, pipes, exit codes, local databases. None of these are new. All of them are agent-native in a way no protocol invented in 2024 can match, because they were invented for a world where the consumer was a program reading text streams. Which is exactly what an LLM agent is.

If you build agents, the practical move this week is small: pick one MCP-backed integration in your stack, install or generate the equivalent Printing Press CLI, and rebuild the skill around it. Measure the token cost. Measure the reliability. Decide for yourself whether the math holds.

I'd wager you'll come back to this article with the same reaction I had after the first migration. Forty thousand tokens, gone. The agent suddenly feels like it's running on a faster machine. And the question that won't leave your head: what else have I been overpaying for?

Print the answer. Then read it.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
