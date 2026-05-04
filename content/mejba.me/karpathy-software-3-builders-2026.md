**BRAND:** mejba.me
**TITLE:** Karpathy's Software 3.0: What I'm Building in 2026
**META TITLE:** Karpathy Software 3.0: What I'm Building in 2026
**SLUG:** karpathy-software-3-builders-2026
**PRIMARY KEYWORD:** Karpathy Software 3.0
**META DESCRIPTION:** Karpathy Software 3.0 reframed how I build software. Inside: the 4 things AI builders should actually ship in 2026, the MenuGen test, and projects I killed.
**TAGS:** AI Development, Software 3.0, Agentic Engineering, Vibe Coding, Builder Strategy

---

I killed three side projects last weekend.

A meal-planner that turned grocery photos into recipes. A "second brain" voice-note summarizer. A Chrome extension that rewrote LinkedIn posts in your voice. All of them half-built. All of them sitting in my `~/projects/abandoned/` graveyard now, with a single `KILLED.md` file in each one explaining why.

The reason was the same in all three cases: a single multimodal LLM prompt could already do 80% of what the app did. I wasn't building software. I was building plumbing around capabilities the model already shipped natively.

What pushed me to actually open those folders and run `mv ~/projects/[name] ~/projects/abandoned/` wasn't a new model release. It was Andrej Karpathy's Sequoia AI Ascent 2026 talk — the one where he pronounced vibe coding "obsolete" twelve months after he coined the term, declared agentic engineering as the new default, and said something that stuck with me for days: *"Most existing apps shouldn't exist."*

I've watched a lot of AI keynotes this year. Most of them are vendor pitches dressed as vision. Karpathy's was different. He didn't tell me what to use — he gave me a framework to evaluate every line of code I'm writing right now. **Karpathy Software 3.0** is not a product. It's a lens. And once I started looking through it, I couldn't stop seeing dead apps everywhere.

This is what I've been processing. What I'm now building. What I'm refusing to build. And the one test — the **MenuGen test** — I now run on every project before I write a line of code.

## What Karpathy Actually Said (And Why It Matters Right Now)

Let me clear up the framing first, because the "Software 3.0" phrase has been kicked around so much it's losing its edge.

In Karpathy's [June 2025 YC AI Startup School keynote](https://x.com/karpathy/status/1935518272667217925), he laid out a three-stage history of software:

- **Software 1.0** — code humans write. Explicit rules, deterministic logic, the entire pre-2012 internet. The thing my CS degree taught me.
- **Software 2.0** — neural network weights trained on data. The model learns the function instead of the engineer specifying it. Image classifiers, recommender systems, the whole "machine learning" decade.
- **Software 3.0** — large language models as programmable computers. You "program" them in English (or any language), with prompts, examples, tools, and context. The prompt is the source code. The LLM is the interpreter.

That third category is the one that broke everyone's brain. We've been writing 1.0 for fifty years and 2.0 for a decade. Now there's a third thing — and it doesn't compile, doesn't have type-checking, doesn't fit in any of our existing IDEs or version-control mental models.

In his Sequoia 2026 fireside, Karpathy went further: [he flagged December 2025 as the inflection point](https://analyticsdrift.com/andrej-karpathy-agentic-engineering-software-3/) — the month where AI-generated code stopped being "helpful but messy" and started being consistently good enough to trust in production. His own ratio inverted that month. In November he was writing roughly 80% of his code by hand. By December he was delegating 80% to agents.

I noticed the same thing on my own machine — I just didn't have language for it. Around mid-December, I stopped reviewing every Claude Code diff line-by-line. I stopped doing the "hover over the function and read it character-by-character" ritual I had built up over years. The diffs were just... right. Not always perfect, but right often enough that the cost of full review exceeded the benefit.

That's the moment the floor moved.

And here's the thing nobody is saying clearly enough: **the floor moving doesn't mean the ceiling went up.** It means most of the apps people are currently building no longer need to exist.

Which brings us to the test.

## The MenuGen Test (My New Pre-Build Filter)

Karpathy's example was MenuGen — an app he built that took a photo of a restaurant menu and rendered images of each dish so you could see what you were ordering. Useful idea. He shipped it.

Then Gemini's Nano Banana update happened. You could now upload the menu photo, type "show me what each dish looks like," and get the same result inline. No app. No backend. No API key management. No App Store distribution. Just a single multimodal prompt.

[MenuGen became obsolete](https://travis.media/blog/vibe-coding-agentic-engineering-karpathy/) the moment a frontier model could do the same job natively.

I now run what I call the **MenuGen test** on every project idea before I commit to it:

> *"If a single multimodal LLM prompt — to GPT-5.4, Claude Opus 4.7, or Gemini 3 — can do 80% of what this app does, the app shouldn't exist as a standalone app. It should exist as a prompt, a system message, a saved skill, or it shouldn't exist at all."*

That's it. That's the filter.

Here's how my three killed projects scored:

**Meal-planner from grocery photos.** A user uploads a photo of their fridge contents → app extracts ingredients → suggests recipes. *MenuGen test:* Drop a fridge photo into Gemini and type "give me 3 recipes I can make from what's visible." It works. Better than my app, actually, because Gemini knows about cooking techniques and I would have spent two months on a flaky vision API integration. **Killed.**

**Voice-note summarizer with second-brain integration.** Record voice memo → transcribe → summarize → file into Obsidian-style hierarchy. *MenuGen test:* Claude with audio input and a single MCP server pointed at my Obsidian vault. Done. The "app" was just three lines of orchestration glue. **Killed.**

**LinkedIn post rewriter in your voice.** Paste post → rewrite in user's tonal style based on past posts. *MenuGen test:* This one I had to think about. The actual *prompt* could be done in a single LLM call — feed past posts as style examples, paste the new draft, get a rewrite. The work I was building wasn't the rewriting. It was the auth, the LinkedIn API integration, the scheduling, the team workspace, the analytics. None of which the user actually asked for. They asked for "rewrite this post in my voice." **Killed.**

That third one is where the test hurts. Because what I was *actually* building was a SaaS wrapper around a 12-line prompt. And I would have shipped it. I would have charged for it. And six months later, when ChatGPT or Claude added a "match my writing style from a connected document" feature, my SaaS would have died overnight along with about 4,000 other "AI X for Y" wrappers.

The MenuGen test isn't pessimism. It's a survival filter. If your product can be replicated by a single prompt, you're not building software — you're building a feature for someone else's product.

That doesn't mean nothing should be built. It means we have to build different things.

Which is what the rest of this is about.

## What Vibe Coding Got Right (And Why Karpathy Buried It)

Before we get to what to build, we need to talk about the workflow shift — because most people I know are still vibe coding, and Karpathy just declared it a junior-league activity.

[Vibe coding](https://www.mejba.me/blog/vibe-coding-is-dead), to be fair, got a lot right. It raised the floor. Anyone with taste and patience can now ship working apps. I've watched non-engineers ship Shopify store integrations, internal team dashboards, and personal tools in a single weekend. That floor-raising is real. It hasn't gone away.

But Karpathy's argument in 2026 is that vibe coding has a ceiling — and it's a low one. You can vibe-code to a working prototype. You can vibe-code to a v1 that demos well. You cannot vibe-code to production reliability. You cannot vibe-code through a security audit. You cannot vibe-code an integration that handles 10,000 concurrent users with eventual consistency requirements across three databases.

That's where **agentic engineering** comes in. The discipline he's now pushing has a specific shape:

- **Specs before code.** Write what the system should do, not just what you want it to look like. ([My OpenSpec writeup](https://www.mejba.me/blog/openspec-ai-coding-framework-spec-driven-development) covers the version of this I actually use day-to-day.)
- **Plans before edits.** Have the agent propose changes before it makes them. Review the plan. Then execute.
- **Tests as the primary signal.** Not vibes. Not "looks right." Failing-then-passing tests as the proof that something works.
- **CI loops on every change.** Lint, test, type-check, security scan — automated, every commit, no exceptions.
- **Diff inspection.** Read what the agent changed before you accept it. Especially for anything touching auth, billing, or user data.
- **Permission isolation.** Don't give an agent root on your machine. Use [git worktrees](https://www.mejba.me/blog/claude-code-git-worktrees-parallel-agents) for parallel work. Sandbox what you can.

If that list looks familiar, it's because it's just... software engineering. The stuff senior engineers have always done. The version of the practice that survived a decade of "move fast and break things." Karpathy is essentially saying: AI didn't kill the engineering discipline. It made the engineering discipline more important, because now your collaborator is a fluent-but-not-careful junior who needs your judgment, not your typing speed.

The shift in 2026 is not "AI replaces engineers." It's "engineers who can supervise AI replace engineers who only type code."

There's a thing I tell every dev I work with now: **the value of your judgment just went up. The value of your typing went down.** If you're still selling typing, you're already obsolete. If you can spec, plan, evaluate, and review at high signal — you just got a 10x leverage multiplier.

And critically — Karpathy made one cautionary note that almost everyone is glossing over.

## The "10 Agents" Trap (Where I Got Burned)

There's a meme going around right now: "I have 20 Claude Code agents running in parallel, here's my workflow." Karpathy looked at that and said, essentially, *don't.*

His framing: most builders trying to orchestrate 10 to 20 simultaneous agents are running ahead of what current models can reliably support. The supervisory load grows non-linearly. By the time you're managing 15 agents, you're spending all your time as a context-switching middle manager and producing worse output than a single agent under careful supervision.

I learned this one the hard way. Two months ago I tried to set up a fully parallel content-generation pipeline: one agent for research, one for outlines, one for drafts, one for editing, one for SEO checks, one for distribution copy. Six agents. All running simultaneously. All "autonomous."

What actually happened: every agent was producing work the next agent couldn't quite use, because none of them had full context. The research agent surfaced facts the outline agent didn't know how to weight. The draft agent invented framings the editor agent then half-rewrote. The SEO agent stuffed keywords into prose the editor had already polished. By the time content reached me, I was spending more time reconciling six agents' outputs than I would have spent doing the whole thing with one Aria-style agent under tight context control.

I killed the pipeline. Now I run **one agent at a time**, deeply, with rich context — exactly the [context-over-configuration approach](https://www.mejba.me/blog/ai-agent-context-beats-configuration) I wrote about earlier this year. The work ships faster. The quality is higher. The cognitive load on me is lower.

Karpathy's caution maps perfectly to what I observed: fewer agents, managed carefully, with review loops, beats more agents managed loosely. Until models get materially better at multi-agent coordination — which they will, but they aren't there yet — the right move is to optimize the single-agent loop.

This is one of those moments where the social-media discourse and the actual practitioner experience diverge sharply. Twitter wants you to think the future is 50 agents in a swarm. Karpathy — who has more context on model capability than basically anyone on the planet — is saying: not yet. Not for most builders. Not in 2026.

I trust him over Twitter. So should you.

## The Four Things Worth Building Right Now

Okay. So most apps shouldn't exist. Vibe coding is the warmup. Multi-agent orchestration is premature. What's actually worth building?

Karpathy gave four pillars in his Sequoia talk. Each one passes the MenuGen test, because each one is *additive* to what frontier models do natively — not a wrapper around what they already do.

### 1. Tools That Sharpen Understanding (Not Just Speed)

The first wave of AI tools sold speed. Faster code. Faster emails. Faster designs. That race is largely over — every model from every vendor is now fast enough.

The next wave sells **strategic clarity**. Tools that help you think better, decide better, see more clearly, and avoid mistakes you wouldn't have spotted on your own.

The pattern: instead of "I'll write this for you," it's "let me show you what you're missing." Instead of generating output, the tool surfaces the questions you haven't asked, the assumptions you're making implicitly, the decisions hiding inside what looks like a single choice.

I'm building exactly this kind of tool right now for my own content workflow. Aria — the agent that writes for [my brand network](https://www.ramlit.com) — doesn't just produce posts. It runs a self-evaluation rubric on every draft, scores ten retention dimensions, and refuses to ship until the rubric passes. The output isn't faster. It's *clearer*, because the agent surfaces what's weak before I have to.

That's the pattern. Not "I'll do it for you." But "I'll show you what you couldn't see, and let you decide."

If you're building in 2026, ask yourself: *am I selling speed, or am I selling clarity?* Speed is a commodity. Clarity is a moat.

### 2. Agent-First Infrastructure

Here's where it gets fun. We've spent thirty years building software for humans — keyboards, mice, screens, GUIs. Every API has a "developer dashboard." Every product has an onboarding flow. Every database has a query builder UI.

Now agents are the customers. And agents don't want UIs. They want APIs. They want clean metadata. They want machine-readable schemas. They want predictable error responses they can recover from. They want documentation that's structured, not prose-heavy.

The shift is enormous, and most product teams haven't internalized it yet. If your product is going to be used by an AI agent on behalf of a user, every UI element is a friction point. Every "click here to confirm" is a place the agent has to bridge between machine intent and human-form interface.

The concrete moves to make right now:

- **Ship an `llms.txt` file.** Adoption is [around 10% as of early 2026](https://www.allmo.ai/articles/llms-txt). Citation uplift evidence is mixed. But it takes 1–4 hours to implement and there's no downside if the spec ends up adopted broadly. It's a low-cost option on a high-impact future.
- **Expose a Markdown variant of every page.** Agentic crawlers — GPTBot, ClaudeBot, PerplexityBot — consistently prefer Markdown over HTML when both are offered. This is real, observed, and showing up in citation rates already.
- **Document your APIs the way you'd document them for an LLM.** Clear inputs, clear outputs, idempotent operations, predictable errors. Skip the marketing-flavored prose. An LLM doesn't need to be sold; it needs to be told.
- **Ship MCP servers for your product.** If your tool can be called from inside Claude Code, ChatGPT, or any other agent runtime, you've just made it 10x more useful for builders. ([My take on must-have MCPs](https://www.mejba.me/blog/must-have-mcps-claude-code) covers what's actually working.)
- **Treat agents as first-class users.** Not a side-channel for power users. Not a "future roadmap item." Treat the agent persona the same way you treat the mobile persona today.

The companies that get this right early are going to look obvious in retrospect. The ones that keep optimizing UIs for human clicks while their competitors quietly become AI-agent-native are going to wonder why their growth flatlined.

### 3. Verifiable-Domain Apps (Where RL Has a Moat)

This is the deepest pillar, and the least understood. Karpathy argued that the real defensible moats over the next decade aren't going to be in pure-language tasks — those are getting commoditized fast. They're going to be in **verifiable domains**: areas where you can mechanically check whether the model's output is correct.

Code is the obvious one. You can run tests. You can lint. You can type-check. You can deploy and observe. Every one of those is a feedback signal that lets reinforcement learning make the model genuinely better at code over time.

But code isn't the only verifiable domain. Look at the list:

- **Algorithmic trading.** P&L is a verifiable signal. Backtests are reproducible. The market is brutal but quantifiable.
- **Supply chain.** Inventory levels, delivery times, cost-per-unit — all measurable, all verifiable, all amenable to RL fine-tuning.
- **Data cleaning and ETL.** Schema correctness, type validation, referential integrity — these are verifiable. The model can be trained against ground-truth pipelines.
- **Compliance and audit.** Regulatory requirements are rules. Either the data meets them or it doesn't. That's a verification signal.
- **Scientific simulation.** Physics, chemistry, materials science — anywhere you have a reference model that can validate predictions.

If you're building in a domain where correctness is verifiable, you have a real moat. Because the data you collect from production becomes training data that makes your specific model better at your specific task — and your competitors who are calling vanilla GPT-5.4 with a clever prompt will plateau at the same place every other vanilla-prompt competitor plateaus.

If you're building in a domain where correctness is *not* verifiable — pure creative tasks, "feels right" decisions, soft judgment calls — the moat is much weaker. The frontier model will catch up to your prompt engineering eventually, and probably soon.

This is the single most important strategic insight from Karpathy's talk, and almost nobody is repeating it. **Verifiability is the moat.** If your product has a measurable correctness signal, you can compound. If it doesn't, you're a wrapper.

### 4. Software 3.0-Native Apps (Not Faster Spreadsheets)

The fourth pillar is the hardest because it asks for imagination, not just engineering.

Most "AI features" shipping in 2026 are Software 1.0 apps with an AI bolt-on. Spreadsheet with a sidebar that explains formulas. Email client with a "summarize this thread" button. Project tracker with a "draft my standup update" command.

These are useful. They are not Software 3.0.

A Software 3.0-native app is one that *couldn't have existed* before LLMs were the substrate. It assumes the LLM the way a 1.0 app assumes a CPU. The model isn't a feature inside the app — the model is the runtime the app runs on.

Look at [monday.com's Vibe App Builder](https://monday.com/w/vibe). Think about what it actually is: a system where a non-developer describes an app in natural language, and the platform generates a working internal tool — UI, data connections, permissions, the works. monday shipped 19+ features and 26 A/B tests for Vibe Apps in Q1 2026 alone. The scaling pace tells you they've found something real.

The interesting part isn't that it generates apps. It's that the *interaction model* is fundamentally different from no-code tools of the past. There's no drag-and-drop. There's no visual flowchart. The user describes what they want in English, the LLM proposes a working app, the user refines through chat. The app doesn't exist as static code — it exists as a living conversation between intent and execution.

That's Software 3.0-native. The English description is the source code. The LLM is the compiler. The app is the executable.

If you're building right now, the highest-leverage move is to ask: *what becomes possible when the LLM is the substrate, not a feature?* Not "how do I add AI to my app" — but "what app could only exist because LLMs exist?"

Examples I'm watching closely:

- **Personal context engines** — apps that learn your specific patterns deeply enough that they generate working tools tailored to you, not to "users." See [Karpathy's own CLAUDE.md skills approach](https://www.mejba.me/blog/karpathy-claude-md-skills-install-guide) for the early version of this.
- **Ephemeral apps** — applications that exist for one workflow and dissolve after. No installation, no signup. The model spins them up in response to a need.
- **Agent-managed services** — products where the entire customer-facing layer is an agent, not a UI, and the "product" is the agent's reasoning loop.
- **Continuous-spec systems** — software where the spec lives next to the code and changes propagate through both layers, automatically. ([Traycer's Bart mode](https://www.mejba.me/blog/traycer-bart-mode-spec-driven-ai) is a glimpse of this.)

These aren't science fiction. They're being built right now, in small numbers, by builders who skipped the "let me add an AI feature to my SaaS" trap.

## How I'm Restructuring My Own Work

Enough theory. Here's what I actually changed in my workflow after sitting with the talk for a week.

**One.** I deleted three side projects and added a `MenuGen test` checklist to my project intake template. Every new idea now has to answer the question: *can a single multimodal prompt do 80% of this?* If yes, it doesn't get built. It gets saved as a prompt in my Claude Skills folder.

**Two.** I shifted my agent setup from parallel-experimental to single-agent-deep. I scrapped a six-agent content pipeline and went back to running one Aria instance at a time, with rich context, careful supervision, and tight review loops. Output quality went up immediately. The post you're reading right now was produced this way.

**Three.** I added an `llms.txt` to mejba.me, ramlit.com, colorpark.io, and xcybersecurity.io. Took maybe 90 minutes for all four. Adoption is moderate, citation evidence is mixed, but the cost-of-skipping is high if AI search keeps growing the way it's been growing. Cheap optionality.

**Four.** I started auditing every active project against the four pillars. If a project doesn't fit one of them — clarity tool, agent-first infra, verifiable-domain, or Software 3.0-native — it's on the chopping block. So far one more project has been killed and two have been restructured.

**Five.** I'm aggressively investing in [the discipline side of AI development](https://www.mejba.me/blog/restraint-most-important-dev-skill) — specs, plans, reviews, tests — and de-emphasizing typing speed, prompt-tweaking, and tool-collecting. The leverage is in judgment now. I act accordingly.

**Six.** I'm watching for the next inflection. Karpathy was right about December 2025. The next big shift — when running 20 agents in parallel actually works, when models can self-spec reliably, when verifiable-domain RL produces genuine super-human capability in a niche — is coming. I want to be set up to recognize it within a week, not a quarter. That means staying close to the practitioner edge, not the keynote edge.

## The One Thing Worth Sitting With

If you take one thing from Karpathy's talk and from this whole post, take this:

> *The job isn't to build software. The job is to build the things that software now makes possible.*

For thirty years, "build software" was the goal because software was the constraint. You needed an app because you couldn't get the thing done without one. The app was the bottleneck.

Software is no longer the bottleneck. The LLM-as-runtime is the bottleneck — and the bottleneck is moving faster than any single product team can. So building "an app" inside a category that's already commoditized at the model layer is a losing game. You'll ship, and three months later the model will subsume you.

The winning game is to build *for the model*, *on top of the model*, or *in domains where the model alone can't win*. Clarity tools. Agent-first infrastructure. Verifiable-domain expertise. Software 3.0-native experiences. Those are the four bets that look obvious in five years.

Everything else — including most of the projects I had open in my IDE last week — was MenuGen. And MenuGen is a feature now, not a product.

Go look at your `~/projects/` folder tonight. Run the test on every one of them. Be honest. Most of us are building plumbing around capabilities that already exist in the model. Some of us are building the thing that only exists because the model exists.

The difference between those two is the difference between a side project and a career-defining one. Karpathy just handed us the test to tell them apart. The rest is on us.

What did you kill this week?

## Frequently Asked Questions

### What is Karpathy's Software 3.0 in plain English?
Software 3.0 is when LLMs become the runtime and natural language becomes the source code. Software 1.0 was hand-written code. Software 2.0 was trained neural network weights. Software 3.0 is prompting an LLM that interprets your intent and executes it. For the full mental model, see the opening section above.

### Is vibe coding dead in 2026?
No — vibe coding still works for prototypes, weekend projects, and floor-raising for non-engineers. What Karpathy retired was vibe coding as the *production* approach. For shippable software, agentic engineering — specs, plans, tests, CI, and diff review — is now the standard. See "What Vibe Coding Got Right" above.

### What is the MenuGen test?
The MenuGen test asks: if a single multimodal LLM prompt can do 80% of what your app does, the app shouldn't exist as a standalone product. It comes from Karpathy's MenuGen example — a real app he built that became obsolete the moment Gemini's Nano Banana update could do the same job in one prompt.

### Should I build a multi-agent system in 2026?
Probably not yet. Karpathy explicitly cautioned against running 10–20 simultaneous agents — current models don't coordinate that well, and the supervisory load destroys quality. One agent under careful supervision beats six loosely-managed ones. Revisit this in 6–12 months.

### What is agent-first infrastructure?
Agent-first infrastructure is software designed for AI agents as primary users — clean APIs, machine-readable metadata, llms.txt files, Markdown-first documentation, MCP servers, and predictable error responses. The shift is treating agents as a first-class persona, not a side channel. See pillar 2 above for concrete moves.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
