**BRAND:** mejba.me
**TITLE:** MiniMax M2.7, Muse Spark, Codex Super App: AI This Week
**META TITLE:** MiniMax M2.7, Meta Muse Spark, OpenAI Codex App — AI Updates
**SLUG:** ai-weekly-minimax-muse-spark-codex-app
**PRIMARY KEYWORD:** MiniMax M2.7
**SECONDARY KEYWORDS:** Meta Muse Spark, OpenAI Codex super app, AI model updates April 2026
**META DESCRIPTION:** I break down five major AI releases: MiniMax M2.7's self-evolving open-source model, Meta's Muse Spark, OpenAI's unified Codex app, and more.
**TAGS:** AI Models, Open Source AI, MiniMax M2.7, Meta Muse Spark, AI Weekly
**CONTENT CLUSTER:** AI Model Reviews
**TRANSFORMATION GOAL:** Help developers and builders understand which of this week's five major AI releases actually matter for their workflows.

---

I was scrolling through my feed at midnight on Saturday when MiniMax's announcement stopped me cold. They'd open-sourced a model — M2.7 — that had run over 100 autonomous improvement cycles on itself, tuning its own hyperparameters, detecting its own failure modes, and boosting its own performance by 30%. No human in the loop for most of it.

I stared at that number for a full minute. Thirty percent. From a model improving itself.

Then I checked what else had shipped that same week. Meta dropped Muse Spark — their first model built from scratch under Alexandr Wang's new Superintelligence Labs. OpenAI merged ChatGPT, Codex, and Atlas into a single super app. Google started bolting voice control onto their AI canvas tool. And a startup called Runnable quietly crossed $2 million ARR by letting people delegate entire projects to an AI agent living in their Slack.

Five announcements. Any one of them would've been the biggest AI story in a normal week. This week, they all landed at once. Here's what actually matters — and what's just noise.

## MiniMax M2.7: The Open-Source Model That Improves Itself

Let me start with the one that kept me up past 2 AM.

MiniMax — a Chinese AI company that most Western developers still underestimate — just open-sourced M2.7, their strongest model to date. Full weights on Hugging Face. Mixture-of-experts architecture. And performance numbers that put it in direct competition with Opus 4.6 and GPT-5.4 on real engineering tasks.

I've been [tracking Chinese AI models](/glm5-pony-alpha-tested) since GLM4, and M2.7 is the first open-source release that genuinely made me reconsider my production stack. Not because of any single benchmark — because of what the benchmarks collectively represent.

Here's the scorecard that matters:

| Benchmark | Score | What It Actually Tests |
|-----------|-------|----------------------|
| SWE-Pro | 56.22% | Real engineering: debugging, security, logs |
| Terminal Bench 2 | 57.0% | Command-line fluency and system operations |
| SWE-Multilingual | 76.5% | Engineering across languages and frameworks |
| MultiSWE-Bench | 52.7% | Broader software engineering challenges |
| Vibe Pro | 55.6% | Full repo-level code generation (web, mobile, sim) |
| NL2 Repo | 39.8% | Understanding and navigating complete codebases |

Those aren't toy benchmarks. SWE-Pro throws real production scenarios at models — the kind where you're staring at 3 AM server logs trying to figure out why your deployment broke. Terminal Bench 2 tests whether a model can actually operate a system, not just write code about operating one. And Vibe Pro evaluates repo-level generation across platforms including web, Android, iOS, and simulation environments.

But what genuinely sets M2.7 apart isn't any individual score. It's the story behind them.

### Self-Evolution: When the Model Becomes Its Own Engineer

Here's where things get philosophically uncomfortable.

MiniMax designed M2.7 to improve itself. Not in some vague "reinforcement learning from feedback" sense — in a concrete, measurable way. The model autonomously ran over 100 optimization cycles on its own code scaffold. It tuned temperature settings. Adjusted repetition penalties. Built loop detection mechanisms to catch when it was going in circles. Added new capabilities to its own toolchain.

The result: a 30% performance improvement on internal benchmarks. From a model that was already competitive with frontier systems.

I've written before about [self-improving AI systems](/self-improving-claude-code-systems), and what strikes me about M2.7 is how operational this has become. This isn't a research paper. MiniMax says the model currently automates 30-50% of its own reinforcement learning team's workflow, with humans stepping in mainly for critical decisions and final validation.

Think about that for a second. The model is doing half the work of training itself. The humans are becoming the reviewers, not the builders. That's a structural shift in how AI development works — and it's happening at an open-source company that just gave away the weights for free.

### Where M2.7 Actually Competes With Frontier Models

I want to be specific here because "competes with GPT-5.4 and Opus 4.6" gets thrown around loosely. MiniMax put M2.7 through machine learning competitions (MLE-Bench Light) running on a single A30 GPU — not a rack of H100s — and it pulled:

- 9 gold medals
- 5 silver medals
- 1 bronze medal
- Average medal rate: 66.6%

That's competitive with models running on orders of magnitude more compute. A single A30 GPU. I've got projects that burn more GPU than that on inference alone.

On professional office work — financial analysis, report generation, earnings call processing — M2.7 scored an ELO of 1,495 on GDPval-AA, ranking it as the highest open-source model for business tasks. That means it can read an annual report, build a revenue forecast, and produce a presentation deck at a level comparable to what a junior analyst would deliver.

And on multi-step tool usage (Toolathon benchmark: 46.3%) and complex skill compliance (MM-Claw: 97% adherence over 2,000+ token tasks), M2.7 demonstrates something I've only seen from the top proprietary models: sustained reliability across long, complex workflows.

The production debugging capability is what sold me hardest. MiniMax demonstrated M2.7 analyzing live production logs, correlating monitoring spikes with deployment timelines, and suggesting targeted fixes — bringing recovery time under three minutes. That's Site Reliability Engineer work. From an open-source model.

### What This Means for Your Stack

If you're running [multi-agent systems](/claude-code-agent-swarm-architecture) and need a capable model you can self-host, M2.7 just became the obvious candidate. The mixture-of-experts architecture means you're only activating the parameters you need per task, so inference costs stay manageable. The open weights mean no API dependency. And the benchmark profile covers exactly the kind of work that agent systems need to do — code generation, debugging, tool usage, and long-context task completion.

I'm not saying it replaces [Opus 4.6](/opus-4-6-hands-on-review) for every use case. On pure reasoning depth and instruction following, Anthropic's model still has an edge I can feel in daily use. But for the kinds of tasks you'd delegate to specialized sub-agents — code scaffolding, log analysis, documentation generation, test writing — M2.7 running locally on your own hardware is now a serious option. And that changes the economics of agentic AI in a way that matters.

## Meta Muse Spark: Built Different — Literally

Meta's timing was impeccable. The same week MiniMax dropped an open-source bomb, Meta shipped Muse Spark — the first model out of their new Superintelligence Labs, the division led by Alexandr Wang (yes, that Alexandr Wang, from Scale AI).

What makes Muse Spark interesting isn't the benchmarks — though those are solid. It's the architecture decision that underpins everything else.

Most multimodal AI models start as text-only systems and bolt vision capabilities on later. GPT-5 did this. Claude did this. You train a language model, then fine-tune it to understand images. It works, but there's always a seam. Vision tasks feel like a second-class citizen compared to text.

Meta said no to that approach entirely. Muse Spark was built from scratch — ground up — to process text and images natively in the same architecture. No bolting. No fine-tuning a text model to see. The visual understanding is baked into the foundation.

And you can feel the difference in the numbers:

| Benchmark | Muse Spark | Opus 4.6 Max | GPT 5.4 Pro | Gemini 3.1 |
|-----------|-----------|-------------|-------------|------------|
| Screen Spot Pro | 72.2% (84.1% w/ tools) | 57.7% | 39.0% | — |
| Health Bench Hard | 42.8% | 14.8% | 40.1% | 20.6% |
| Frontier Science | 38.3% | — | 36.7% | 23.3% |
| Humanity's Last Exam | 58.4% (w/ tools) | — | 58.7% | 53.4% |
| SWE Bench Verified | 77.4% | 80.8% | — | 80.6% |

Screen Spot Pro is the one that jumps out. An 84.1% score on visual UI understanding — compared to 57.7% for Opus 4.6 Max and 39.0% for GPT 5.4 — means Muse Spark can look at a screen and understand what's on it with almost human-level precision. For anyone building [computer-use agents](/anthropic-computer-use-ai-automation) or visual testing tools, that's a massive deal.

Health Bench Hard is the other standout. Meta collaborated with over 1,000 physicians to curate training data specifically for medical reasoning. The result: 42.8%, which is the global number one. If you're building health-adjacent AI applications, Muse Spark is now the model to evaluate first.

### The Efficiency Story Nobody's Talking About

Here's the stat that technical builders should care about most: Muse Spark achieves comparable capabilities to Llama 4 Maverick with over 10x less compute. That's not an incremental improvement — it's a rebuilt pre-training stack delivering order-of-magnitude efficiency gains.

Meta accomplished this through three innovations working together:

**Pre-training optimization** — a fundamentally reworked training pipeline that squeezes more learning per compute dollar.

**Reinforcement learning with stable gains** — RL that actually improves the model consistently instead of the noisy, plateau-prone training curves most teams deal with.

**Test-time reasoning improvements** — including thought compression (solving problems with fewer tokens, meaning faster and cheaper inference) and what Meta calls "contemplating mode," where parallel agents produce and refine answers simultaneously.

That contemplating mode caught my attention. It's essentially multi-agent reasoning at inference time — the model spawns parallel reasoning paths and then selects or combines the best output. I've been [building exactly this kind of architecture](/opus-agent-teams-complete-guide) manually with Claude agent teams. Meta is baking it into the model itself.

### Where Muse Spark Falls Short

I wouldn't be doing my job if I only highlighted the wins. Muse Spark has a clear weakness, and it's a significant one for certain use cases.

ARC AGI 2 — the abstract reasoning benchmark — shows Muse Spark at 42.5%, while both Gemini and GPT-5.4 score above 76%. That's not a small gap. It suggests that the natively multimodal architecture, while incredible for visual and applied reasoning, may sacrifice something in pure abstract pattern recognition.

SWE Bench Verified tells a similar story. At 77.4%, Muse Spark is strong but trails Opus (80.8%) and [Gemini 3.1](/gemini-3-1-pro-real-power) (80.6%) on verified software engineering tasks. If your primary use case is agentic coding, Muse Spark isn't the frontrunner yet.

It also won't be open-source — at least not initially. Meta said there's "hope to open-source future versions," which is the most non-committal language they could have chosen. Given that they built this to power the Meta AI app, WhatsApp, Instagram, and Messenger integrations, I'm not holding my breath for open weights.

## OpenAI's Super App: Everything in One Window

While MiniMax and Meta were playing the model game, OpenAI made an infrastructure play that might matter more long-term.

On April 6th, OpenAI launched what they're calling the unified super app — a single desktop application that merges ChatGPT, Codex (the coding agent), and Atlas (their AI browser) into one interface. Alongside it, they released ChatGPT 5.5, a bridge model between GPT-5.4 and whatever comes next (internally nicknamed "Spud," which is reportedly GPT-6).

I've been using [OpenAI's Codex](/gpt-5-3-codex-first-look) since the early CLI days, and the fragmentation has always been a pain point. Want to chat? Open ChatGPT. Want to code? Open Codex. Want to browse and research? Open Atlas. Three different interfaces, three different context windows, three different sets of capabilities that don't talk to each other.

The super app kills that friction. Everything lives in one window. And more importantly, the agents can hand tasks to each other seamlessly.

### The Scratchpad Changes How I Think About Multi-Tasking

The headline feature is what OpenAI calls the "scratchpad" — an interface that lets you trigger multiple parallel Codex tasks from a single view. Think of it as a task manager for AI agents. You write three coding tasks, kick them all off simultaneously, and each one runs in its own sandboxed environment. While one agent is refactoring your authentication module, another is writing tests for your payment flow, and a third is generating API documentation.

This is eerily similar to what I've been building manually with [Claude Code agent teams](/anthropic-agent-teams-ai-coding) — but OpenAI is productizing it into a consumer-friendly interface. The managed agents handle multi-step workflows autonomously, check in periodically for approval on critical decisions, and maintain persistent "heartbeat" connections that support long-running background processes.

There was speculation about a new model release codenamed "Glacier" — possibly GPT-5.5 — aligning with the app launch. OpenAI ended up calling it ChatGPT 5.5 instead, positioning it as an improved memory management and task continuity model rather than a raw intelligence upgrade. Available immediately for Plus and Pro subscribers, with a limited free-tier rollout to follow.

### Why This Matters More Than Another Model Bump

Here's my take: OpenAI is betting that the next competitive advantage isn't model intelligence — it's platform cohesion. When everything lives in one app, context doesn't get lost between tools. Your chat conversation informs your coding agent which informs your browser research which feeds back into your chat. That flywheel effect is powerful, and it's something you can't replicate by duct-taping separate tools together.

The parallels to what [Anthropic is building with Conway](/claude-cowork-daily-workflow-system) and what Runnable is doing with their agent platform are striking. The entire industry is converging on the same insight: the future of AI isn't a chatbot you talk to. It's an agent system that works alongside you.

## Google Mixboard: When Your Canvas Listens to You

Google's contribution this week is smaller in scope but fascinating in direction.

Mixboard started as an AI-powered image canvas — drag, drop, remix, and generate visuals on a collaborative board. Think Miro meets Midjourney. But Google is evolving it into something more ambitious: a full hybrid collaborative workspace with voice control.

The new experimental features include stickers, voice notes, geometric shapes, and markers that overlay on top of AI-generated images. But the real play is voice mode — the ability to manipulate the entire board through speech. Generate an image. Move it left. Swap the background. Add a text layer. All by talking.

Google built this on the same infrastructure as their Stitch voice interaction tool, and if it works as demonstrated, it bridges a gap that's been bugging me about every AI creative tool I've tried: the input bottleneck. Even the best AI canvas is limited by how fast you can type prompts and click buttons. Voice removes that friction entirely.

The PDF export feature is the quiet killer. Imagine running a brainstorm session on Mixboard — collaborators throwing ideas, generating images, arranging concepts — and then exporting the entire board as a structured document with one click. That bridges the gap between "ideation session" and "deliverable" in a way that no other tool I've used does cleanly.

Google hasn't confirmed integration details or a firm shipping date. Given the Google I/O window (May 19-20), I'd expect an official announcement there, likely tied to Gemini or Google Workspace. For now, it's available as an experiment in Google Labs.

## Runnable Run Claw: The AI Teammate Living in Your Chat

The last announcement is the one that sneaks up on you.

Runnable shipped Run Claw — a cloud-based AI agent that lives inside Slack, Telegram, and Discord. You message it like a coworker. It asks clarifying questions. Plans the work. Executes it autonomously. Reports back when it's done.

I've been [covering AI agents in chat platforms](/openclaw-agents-scale-business) for months, and what makes Run Claw different isn't the concept — it's the execution maturity. This isn't a chatbot with API integrations bolted on. It's a full autonomous agent with:

- **File uploads** for providing context (drop a design mockup, get a website)
- **Chat mode** for research and brainstorming
- **Plan mode** for complex multi-step builds
- **Model selection** so you can pick the right AI for each task
- **Memory** for learning your preferences over time
- **Connectors** for Google, Slack, Notion, GitHub, Shopify, and more

The multi-modal output is what sets it apart from similar tools. Run Claw doesn't just write text. It builds live websites with databases, payment processing (Stripe integration), SEO optimization, analytics, version control, and even AI-powered voice agents. From a Slack message.

Runnable recently crossed $2 million in annual recurring revenue and ships product updates daily. Those are the metrics that tell me this isn't a weekend project — it's a company building real infrastructure with real traction.

### The Broader Pattern: AI Agents as Coworkers

Run Claw, OpenAI's super app, Anthropic's Conway system — they're all converging on the same vision. The AI isn't a tool you open when you need help. It's a persistent presence in your workflow that handles tasks the way a capable colleague would. You delegate. It executes. You review. It iterates.

We're watching the transition from "AI as search engine" to "AI as teammate" happen in real time. And the companies that figure out the UX for delegation — not prompting, but genuine task delegation — will own the next phase.

## What This Week Actually Tells Us

Five announcements. Five different strategies. Here's the pattern I see underneath all of them:

**Open source is accelerating faster than proprietary.** MiniMax M2.7 matching frontier models on real engineering tasks — and open-sourcing the weights — puts pressure on every company charging premium API rates. When a self-hosted model can do 80% of what GPT-5.4 does for a fraction of the cost, the economics of AI development shift permanently.

**Natively multimodal is the new baseline.** Meta building Muse Spark from the ground up for vision and text — rather than bolting vision onto a text model — signals where architecture is heading. Expect every major model release going forward to be natively multimodal. The "add vision later" approach is dead.

**Platforms beat models.** OpenAI merging everything into one app, Runnable embedding agents in Slack, Google adding voice to their canvas — these are platform plays, not model plays. The raw intelligence of the underlying model matters less when the integration layer is seamless.

**Self-improvement is no longer theoretical.** MiniMax's model running 100 autonomous optimization cycles isn't a research demo. It's production infrastructure. When models can meaningfully improve themselves, the pace of AI development stops being limited by human engineering bandwidth.

**Health and science are the new frontier applications.** Both Muse Spark and M2.7 showed strong performance on medical and scientific benchmarks. The "AI for coding" phase isn't over, but the next wave of billion-dollar applications will likely come from AI that can reason about biology, chemistry, and clinical medicine.

I've been covering AI tools for long enough to know that most weekly updates don't matter. Most model releases are incremental. Most product launches are premature.

This week wasn't that. Five different companies — spanning open source to big tech — each shipped something that shifts the landscape in a measurable way. The question isn't whether AI development is accelerating. It's whether the rest of us can keep up with the tools that are building themselves.

---

**What I'm Watching Next:** MiniMax M2.7 running in my local agent pipeline (expect a hands-on review), Google I/O for the Mixboard and Gemini updates, and whether OpenAI's super app actually holds up when real workflows hit it at scale. I'll report back.

---

*If you're building with AI agents and want the unfiltered take on what actually works, I share my experiments, workflows, and honest tool reviews right here on mejba.me. No sponsored content. No hype. Just what I learn by building.*

---

### Social Distribution Package

**Twitter/X:**
MiniMax just open-sourced a model that improved itself by 30%.

Meta built a multimodal AI from scratch that's #1 on health reasoning.

OpenAI merged ChatGPT + Codex + Atlas into one app.

Google added voice control to their AI canvas.

And a Slack agent startup hit $2M ARR.

My breakdown of the 5 biggest AI drops this week 🧵

**LinkedIn:**
Five major AI releases landed in a single week — and they tell a clear story about where the industry is heading.

MiniMax M2.7 proves open-source models can match frontier systems while self-improving. Meta's Muse Spark shows natively multimodal architecture is the future. OpenAI's unified app demonstrates that platforms beat individual models. Google Mixboard hints at voice-first collaboration. And Runnable's $2M ARR proves AI agents as persistent teammates isn't a theory anymore.

The pattern: we're moving from "AI as tool" to "AI as coworker." The companies that solve delegation UX — not just prompting — will win the next phase.

Full analysis on mejba.me.

**Newsletter:**
Subject: The Week AI Started Building Itself

This week, five companies shipped five different visions of AI's future — all at once.

The highlight: MiniMax open-sourced a model that ran 100 autonomous improvement cycles on itself, boosting its own performance by 30%. That's not a research paper. That's a production system where the AI is doing half the work of training itself.

I break down all five announcements — MiniMax M2.7, Meta's Muse Spark, OpenAI's super app, Google Mixboard, and Runnable's chat agent — with the honest take on what matters for builders and what's just marketing.

Read the full breakdown → [link]
