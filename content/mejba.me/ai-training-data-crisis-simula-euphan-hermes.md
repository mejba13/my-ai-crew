**BRAND:** mejba.me
**TITLE:** The AI Data Crisis: Simula, Euphan, Hermes Tested
**META TITLE:** AI Training Data Crisis 2026: Simula, Euphan, Hermes
**SLUG:** ai-training-data-crisis-simula-euphan-hermes
**PRIMARY KEYWORD:** AI training data synthetic data generation
**META DESCRIPTION:** I dug into Google's Simula, OpenAI's Euphan, and the Hermes agent platform. Here is what the AI training data crisis looks like in April 2026 — and the fix.
**TAGS:** AI Agents, Synthetic Data, AI Training, OpenAI, Google Research

---

The paper that made me stop everything I was doing last week wasn't about a new frontier model. It wasn't a benchmark leak. It wasn't even an announcement, technically — it was a research drop from Google titled something dry enough that most people scrolled past it. "Orchestrating Synthetic Datasets with Reasoning." I almost scrolled past it too.

Then I read the line that lodged in my head for three days: *models trained on the synthetic data sometimes outperformed the ones trained on real-world datasets.* Not matched. Outperformed. In specialized domains — cybersecurity, legal reasoning, medical — where "real" data is either locked behind privacy law or too expensive to collect, a team at Google had built a system that reasoned its way to training data better than the real thing.

That paper — which introduced a framework called Simula — is half of what I want to break down in this post. The other half is what OpenAI has been shipping in the same window: a log-interpretation surface that insiders keep calling "Euphan," and a persistent-agent platform codenamed Hermes that's quietly sitting in ChatGPT's preloaded frontend modules waiting for a release flag to flip.

Three tools. One underlying shift. And that shift — which I'll argue is the most important thing happening in AI right now — is that the bottleneck has moved. It's not compute anymore. It's not even model size. It's the data pipeline feeding the model, the observability layer watching the agent, and the runtime holding the agent alive between sessions. All three are broken. All three are being fixed, simultaneously, by the two labs that have the most to lose if they aren't.

Here's what I found when I dug into each one — what's real, what's vaporware, and what the combination means for anyone building on top of these models in 2026.

## Why the Data Crisis Is Worse Than Most Builders Realize

The conventional story about AI progress is a scaling story. Bigger models, more parameters, more compute, better results. That story worked right up until about a year ago. Then something changed, and nobody in the consumer press really caught it.

The teams training frontier models ran out of quality internet. Not the quantity — there's still plenty of text on the internet. But the signal-to-noise ratio of what's left to scrape has collapsed. The high-quality text was consumed in earlier training runs. What's left is duplicated, machine-generated, or too shallow to teach a model anything it doesn't already know.

For general-purpose chat models like GPT, Gemini, and Claude, this shows up as diminishing returns on scale. Each doubling of training tokens produces a smaller improvement than the one before. The curve is bending.

For specialized domains, the problem is structurally different and much worse. If you want to train a model that's genuinely good at cybersecurity reasoning, you need data that represents the actual taxonomy of attacks, threat actors, vulnerability classes, and mitigation strategies. That data exists — but it's fragmented across proprietary threat intelligence feeds, closed incident reports, and the tacit knowledge of senior analysts who will never write it down.

Legal reasoning? Sealed under privilege, locked behind paywalls, or buried in jurisdiction-specific case law that no single source has aggregated cleanly.

Medical? HIPAA exists for good reasons. The data you'd want to train on is exactly the data you can't legally use at scale.

I've felt this personally. When I've tried to get mainstream models to reason carefully about security vulnerabilities in specific frameworks, they produce confident-sounding output that falls apart under audit. Not because the models are bad — but because the data they were trained on doesn't cover the real terrain. They learned the Wikipedia version of cybersecurity, not the version a working penetration tester operates in.

This is the wall. And the way around the wall — the thing every serious AI lab has been working on for two years — is synthetic data. Not the 2023 version of synthetic data, which was basically "ask GPT-4 to write more training examples and hope for the best." The 2026 version, which is what Simula represents.

## Simula: What Reasoning-First Data Generation Actually Looks Like

I'll admit, when I first skimmed the Simula paper, I thought it was going to be another variation on the "LLM generates synthetic examples, then an LLM filters them" pattern. That pattern has been around since 2023 and it has a well-known failure mode — the generator collapses into a narrow distribution of examples that feel diverse but actually cover a thin slice of the possibility space. The technical term is **mode collapse**, and it's the reason most synthetic data pipelines quietly underperform real data despite generating millions of examples.

Simula is not that. The architecture is genuinely different, and the difference is where the interesting performance gains come from. Here's the actual mechanism, in the order the framework executes it.

**Step one: structured domain mapping.** Before generating anything, Simula constructs a hierarchical taxonomy of the target domain. For cybersecurity, that taxonomy includes attack types (phishing, injection, supply chain, social engineering, etc.), threat actor categories (nation-state, organized crime, hacktivist, insider), vulnerability classes (buffer overflow, logic flaw, race condition, misconfiguration), and mitigation strategies. Each branch has sub-branches. The taxonomy is the map of what good coverage looks like.

This step alone is more structure than most synthetic data pipelines have. Most pipelines assume the generator model will naturally produce diverse examples. It won't. It'll produce examples biased toward whatever was overrepresented in its own training data.

**Step two: controlled sampling.** Instead of letting the generator pick topics at random, Simula samples deliberately across the taxonomy. If cybersecurity has 400 nodes, the sampler ensures each node gets appropriate representation — including the rare, complex cases that a random sampler would visit once in ten thousand examples. This is the mode-collapse antidote. You can't collapse into a narrow distribution if the sampler is explicitly forcing you to cover the whole space.

**Step three: metaprompt generation.** Here's where Simula gets interesting. The system doesn't directly generate training examples. It generates **prompts that will generate training examples**. These metaprompts include constraints on format, difficulty, angle, and reasoning depth. The metaprompt layer introduces variation that a direct-generation system can't match because the metaprompts themselves are diverse.

Think about the difference. Direct generation: "Write a cybersecurity Q&A about SQL injection." You get a thousand variations of essentially the same answer. Metaprompt generation: "Write twenty different prompt templates that would each elicit a high-quality training example about SQL injection — one from the perspective of an incident responder, one as a code review, one as a compliance audit, one as a red-team report, one as a junior engineer's first exposure, etc." Now the generator is producing diverse angles by design, not by accident.

**Step four: complexity parameterization.** Simula treats complexity as an independent axis from diversity. You can generate simple examples across the full taxonomy, or complex examples across the full taxonomy, or a deliberate mix. The Google researchers reported that **tuning the complexity parameter upward gave up to a 10% performance boost on math reasoning benchmarks** — as long as the underlying generator model was strong enough to handle the complexity. Weak generators with cranked-up complexity produced plausible-but-wrong examples at scale, which actively hurt the student model.

That's a crucial caveat. Complexity is a multiplier, not a solve. It multiplies the quality of the generator — in both directions.

**Step five: dual critic quality control.** Every generated example passes through two evaluators. The first asks "is this correct?" The second, separately, asks "is this incorrect?" Phrasing matters here. Asking the same critic "is this correct?" twice will produce correlated answers. Phrasing the second evaluation as a contradiction forces an independent reasoning pass. The two answers are combined into a validation score. Examples that pass both filters survive. Examples that one critic considers correct and the other considers incorrect get flagged for review. This two-question structure is what catches the plausible-but-wrong outputs that single-critic systems miss.

The team tested this pipeline using Gemini 2.5 Flash as the teacher model and Gemma-3 4B as the student, generated up to 512,000 data points per domain, and evaluated across five domains. The headline result — that synthetic data matched or exceeded real-world data in specialized domains — held across multiple evaluations.

The honest caveat the paper includes, which nobody on Twitter quoted: there is no single optimal configuration. The relationship between "good" synthetic data and downstream model performance is what the researchers called **deeply idiosyncratic**. Different domains need different complexity mixes, different taxonomy depths, different critic weights. Simula gives you the knobs. It doesn't tell you where to set them.

But the point isn't that Simula is a silver bullet. The point is that the paradigm has shifted. The question used to be "how much data can you collect?" The question now is "how well can you design the data?" And the winners of the next wave of specialized AI won't be the labs with the most scrapers. They'll be the labs with the best taxonomists.

## Euphan: Why OpenAI Quietly Built a Log Viewer

Let me pause here, because the second tool in this story needs context that the first one didn't.

If you've never built an agent-style AI system, the concept of a "log" probably sounds like something you'd find in a datacenter dashboard. Something boring. Something ops people care about. Let me correct that impression, because it's wrong in a way that's costing builders real time.

An agent running even a moderately complex task — say, researching a topic, extracting structured data, writing a draft, and posting it somewhere — generates a log that looks nothing like a traditional server log. It's a nested tree of tool calls, model responses, reasoning traces, intermediate outputs, retry attempts, permission grants, and state transitions. One agent run can produce thousands of lines of JSON. Most of it is noise. A small percentage of it is the actual story of what the agent did and why.

When that agent misbehaves — and they all misbehave eventually — your job as the developer is to find the point in that log where the reasoning went wrong. In a well-structured log, that's tedious. In a raw JSON dump, it's hours of scrolling. I've spent entire evenings grepping through agent logs trying to understand why a tool call that should have fired didn't, or why a model hallucinated a parameter that wasn't in the prompt.

This is the problem space OpenAI has been quietly filling. The internal name floating around is Euphan, though publicly the company has shipped most of the same functionality through its Agents SDK tracing dashboard and the new workspace-agents surface in ChatGPT. Whatever you call the underlying system, the job it does is specific and important: it takes raw agent logs and turns them into something a human can read fast.

What that looks like in practice:

- **Clean structured timelines.** Instead of nested JSON, you get a linear sequence of steps, each with a clear role label (planner, retriever, tool caller, responder), a timestamp, and a one-line summary. Click any step to expand into full detail. Skim the timeline to find the inflection point.
- **Role and tool inspection.** Every tool call shows which agent invoked it, what parameters were passed, what came back, and how long it took. If a tool returned a 429 rate-limit error forty minutes into a run, the timeline surfaces it without you having to hunt.
- **Decision reasoning visibility.** Modern agents emit reasoning traces — the model's internal justification for picking one action over another. A readable log surface renders those traces as inline annotations on the timeline, so you can see not just what the agent did but why it thought it was the right call.
- **Direct log editing.** This is the feature that surprised me most. You can edit a log entry, save the modified state, and replay the run from that point forward. It's like `git rebase` for agent history. I haven't seen this done cleanly anywhere else.
- **Filtering, search, and metadata queries.** When you're dealing with runs that span hours and thousands of steps, grep isn't enough. You need structured queries. "Show me all tool calls with `status != 200`." "Show me the reasoning traces where confidence dropped below 0.6." That layer is there.

The reason I keep coming back to this tool, even though it's less glamorous than a new frontier model, is that it solves a real problem I hit every week. When I built out a personal agent stack using the [Anthropic Agent SDK](/anthropic-agent-sdk-guide), the hardest part wasn't writing the agent. It was debugging the agent when it misbehaved. I ended up building a janky homemade log viewer in a Notion database because I couldn't stand reading raw JSON anymore. If I'd had something like Euphan — or the equivalent surface for Anthropic logs — I would have shipped a week earlier.

This is developer infrastructure. It's not a consumer feature. It won't get a keynote demo. But the teams that ship production agents will look back on log-interpretation tools as the moment the entire workflow became tractable.

Now, the third piece.

## Hermes: The Shift From Chatbot to Always-On Teammate

Hermes is the codename that leaked out of OpenAI's internal builds over the past few weeks, and I want to be careful here because there's name confusion in the market. The open-source community already has a tool called Hermes Agent from Nous Research — I've written about pairing it with [OpenClaw in a two-agent workflow](/openclaw-hermes-multi-agent-workflow). OpenAI's Hermes is a different thing entirely. Same name, different company, different architecture. Context matters.

OpenAI's Hermes — at least based on the leaked frontend resources and the preloaded modules sitting in the ChatGPT web app — is a platform for **persistent agents**. Not one-off task runners. Not "run this task and return." Agents that you define once and that continue to exist across sessions, running scheduled work, responding to triggers, staying connected to external tools, and reporting back when something relevant happens.

If that sounds familiar, it's because it's the same architectural direction Anthropic moved with its managed agents surface, and the same direction a dozen smaller companies (including the Hermes Agent from Nous Research I just mentioned) have been building toward. Persistent agents are not a single-vendor idea. They're the consensus next step.

What's different about OpenAI doing it is the distribution. ChatGPT has hundreds of millions of users. When persistent agents ship inside that product — not as a separate developer surface, but as a feature any Plus user can turn on — the default behavior of "chatting with an AI" changes in a way that will feel ordinary in retrospect and radical right now.

What I expect Hermes to look like when it ships, based on the leaked resources and the pattern every other persistent-agent platform has followed:

- **Agent definitions that persist across chats.** You create an agent once — give it a role ("my research assistant"), a set of skills, a task list, maybe some access permissions. The agent becomes addressable from any conversation. You don't have to re-prime it every time.
- **Scheduled and triggered execution.** The agent runs on its own schedule (every morning at 7 AM, summarize overnight news) or on triggers (when a new entry appears in my Notion database, draft a response). This is the shift from reactive to proactive.
- **Tool connections that stay alive.** Agents maintain authenticated connections to external services — Gmail, Calendar, GitHub, Slack, whatever the permission model allows. The tool doesn't re-authenticate on every run. It's already in.
- **Long-running memory.** Agents remember prior runs, prior outputs, prior feedback. If you told the agent last week that you prefer three-bullet summaries over paragraph summaries, the agent should remember that forever unless you explicitly reset it.

There's no release date. The feature is still in internal testing, visible only through frontend resource inspection. That said, OpenAI has been pushing in this direction publicly — workspace agents shipped in April 2026 for Business, Enterprise, and Edu plans, which share most of the architectural assumptions Hermes would build on. The pattern is clear. The question is when, not if.

Here's the thing I keep coming back to, though. Persistent agents don't work without the first two pieces.

Persistent agents need **specialized capability** — an agent that runs continuously in a specific domain (legal research, security monitoring, financial analysis) needs a model trained on data for that domain. General-purpose models fail on specialized tasks in ways that compound when the agent is running unattended. That's the Simula problem.

Persistent agents need **observability** — an agent running 24/7 produces orders of magnitude more log output than a session-based agent. Without a tool like Euphan, debugging a production persistent agent is hours of log-diving every time something goes sideways. That's the Euphan problem.

Persistent agents need **runtime** — which is what Hermes itself provides.

You can't just ship the third piece. All three have to work together, or the whole stack collapses under its own weight. Which brings me to the thing I actually want you to take away from this post.

## What This Combination Actually Means For Builders

Zoom out for a second. What we're watching, in real time, is the full AI development pipeline getting rebuilt from the data layer up.

The **data layer** is getting rebuilt with systems like Simula, where synthetic generation with reasoning and taxonomy-driven sampling produces training data that's better than what can be scraped.

The **observability layer** is getting rebuilt with log-interpretation tools like Euphan, where messy multi-agent traces become readable timelines you can actually debug.

The **runtime layer** is getting rebuilt with persistent-agent platforms like Hermes, where agents stop being one-shot tasks and start being long-running teammates.

Each of these is individually significant. The combination is the thing that matters.

If you're building anything serious on top of these models in 2026 — a SaaS product, an internal tool, a content pipeline, an automation — here's the practical implication. You should stop assuming that the bottleneck is the model. Check your assumptions against these three questions:

1. **Is my use case specialized enough that a general-purpose model is underperforming?** If yes, the fix isn't switching to the newest frontier model. The fix is either waiting for a specialized model trained on Simula-style data, or building one yourself with synthetic generation on your own taxonomy.
2. **When my agent misbehaves, how long does it take me to figure out why?** If the answer is "hours" or "I usually just restart and hope," you need better observability, not a better agent. Start adopting log-interpretation tooling now, even if it's rough, because it compounds.
3. **Am I rebuilding state on every session?** If you're still running agents as one-off chat conversations, you're on the losing side of the curve. Start designing for persistence even before Hermes ships — decouple your agent's state from the chat surface, store it somewhere durable, and your migration cost when persistent-agent platforms land will be near zero.

None of this is easy. I've been working through these exact questions on my own stack — some of which I wrote about in the [train AI agents skills autonomously post](/train-ai-agents-skills-autonomously) — and the answers keep pulling me toward more infrastructure and less magic. More taxonomy, more logging, more state management. Less "throw it at the model and see what happens."

Which, now that I think about it, is the same lesson that every serious engineering discipline eventually learns. The magic is in the boring parts.

## Real Talk: Where I Think This Breaks

I don't want to leave you thinking these three tools are the whole answer, because they aren't. Here's what I'd flag as genuine limitations and open problems.

**Simula works because the underlying models are strong enough.** The moment you try to apply the same pipeline with a weaker generator, complexity parameterization amplifies errors instead of quality. Most teams don't have access to Gemini 2.5 Flash as their teacher model. What Simula-equivalent pipelines look like on a tighter budget is still unsolved.

**Euphan-style log interpretation is only as good as the underlying log structure.** If the agent framework you're using emits sloppy, unstructured logs — and plenty still do — no interpretation layer can conjure clarity from garbage input. The log format itself has to be designed for observability from the start.

**Persistent agents raise a security question nobody has a great answer for yet.** An agent that runs 24/7 with authenticated connections to your Gmail, your GitHub, your calendar, is a large attack surface. If the agent's reasoning can be manipulated — through prompt injection in an incoming email, or a poisoned search result — the blast radius is the entire authenticated graph. Every persistent-agent platform I've seen is aware of this and none have fully solved it. This is why security tooling for AI agents is going to be a massive category, and why I keep coming back to it.

The honest summary is that these three tools are the direction of travel, not the destination. The next eighteen months of building in AI are going to be spent figuring out how to make this stack actually work end-to-end in production, for use cases that aren't "generate a blog post" or "summarize a document." Real work. Real consequences. Real uptime requirements.

And that's exactly the kind of problem I want to be working on.

## What I'm Watching Next

Three specific things I'm tracking over the next quarter.

First, **open-source replications of Simula**. The paper is detailed enough that a motivated team could rebuild the pipeline outside Google's infrastructure. I expect the first serious open-source implementation within 60 days, and whoever ships it cleanly is going to become the default tool for specialized-domain synthetic data in the open-source world.

Second, **log-interpretation standards**. Right now every agent framework has its own log format. OpenAI's is different from Anthropic's is different from LangChain's is different from Nous Research's. That fragmentation is going to force some kind of common trace standard — probably something built on OpenTelemetry conventions — and the first framework that ships good interoperability wins a lot of developer goodwill.

Third, **Hermes's release window**. I'm giving this a 60% probability of shipping publicly before the end of Q3 2026, based on how deep the frontend integration already is. If it ships, the "always-on agent" pattern goes from being a niche developer thing to being the default for hundreds of millions of ChatGPT users within weeks. That's a category-defining event.

Pay attention to all three. One of them will probably be the biggest AI story of the summer.

That paper I mentioned at the start — the Simula paper I almost scrolled past — is the reason I now believe the most important AI work in 2026 isn't happening at the model layer. It's happening in data design, observability, and runtime. Boring. Infrastructure-shaped. Enormously consequential.

If you only read one thing from this post, read that sentence twice. Then go look at your own stack and ask which of the three layers is weakest. Fix that one first. Everything else follows.

## Frequently Asked Questions

### What is synthetic data generation in AI training?
Synthetic data generation is the process of using AI models to produce training examples that didn't exist in the original dataset. Modern systems like Google's Simula use reasoning-first taxonomies and dual-critic validation so the generated data can match or exceed real-world data in specialized domains. For the full mechanism, see the Simula section above.

### Why is synthetic data better than real data for specialized AI domains?
Real data in domains like cybersecurity, legal, and medical is often locked behind privacy laws, fragmented across proprietary sources, or simply doesn't exist at the volume needed for training. Well-designed synthetic data can cover the full taxonomy of the domain — including rare cases — with controlled quality. In Google's Simula benchmarks, synthetic-trained models sometimes outperformed real-data-trained models on specialized tasks.

### What is mode collapse in synthetic data and how do you prevent it?
Mode collapse happens when a generator model produces repetitive, narrow examples that feel diverse on the surface but cover a thin slice of the real distribution. Simula prevents it by sampling across a structured taxonomy and using metaprompts — prompts that generate prompts — to force genuine variation in angle, format, and reasoning depth.

### When will OpenAI's Hermes persistent agent platform release?
OpenAI has not announced a release date for the Hermes persistent agent feature. Frontend resources and preloaded modules spotted in the ChatGPT web app suggest active development, and related features like workspace agents already shipped in April 2026 for Business, Enterprise, and Edu plans. A public Hermes release during 2026 appears likely based on the pattern.

### What is an AI agent log interpreter and why do developers need one?
An AI agent log interpreter is a tool that converts raw, nested agent logs — tool calls, reasoning traces, retries, state transitions — into readable structured timelines. OpenAI's Euphan-style surface and the Agents SDK tracing dashboard show each step, the role that ran it, the tools called, and the reasoning behind each decision. Without this layer, debugging a misbehaving agent means scrolling thousands of lines of raw JSON.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
