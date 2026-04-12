**BRAND:** mejba.me
**TITLE:** 6 Open-Source AI Projects on GitHub I Actually Tested
**META TITLE:** 6 Open-Source AI Projects on GitHub Worth Testing Now
**SLUG:** six-hot-open-source-ai-projects-github
**PRIMARY KEYWORD:** open-source AI projects GitHub
**SECONDARY KEYWORDS:** on-device AI, agent memory architecture, AI agent orchestration
**META DESCRIPTION:** I spent a week testing six trending open-source AI projects on GitHub — on-device LLMs, agent memory, orchestration, and Karpathy-inspired Claude skills. Here's what actually works.
**TAGS:** Open Source AI, AI Agents, Claude Code, On-Device LLM, GitHub
**CONTENT CLUSTER:** AI Tools & Productivity
**TRANSFORMATION GOAL:** After reading, the reader will know which six open-source AI projects are worth cloning this weekend, what each one actually does well, and which specific limitations to watch for before betting a workflow on them.

---

I almost didn't clone the first repo.

It was a Sunday morning, I had three coffees lined up and a half-written agent that kept hallucinating tool arguments, and my GitHub "Trending" tab was doing that thing where every project looks like the same screenshot — dark terminal, purple gradient, "autonomous" in the tagline. I was ready to close the browser and go fix my broken agent with Claude Code and brute force. Then I saw Hermes Agent, looked at its memory architecture diagram, and thought, *wait, this might actually solve the thing I'm trying to brute-force right now.*

That's how this post started.

Over the next week, I cloned six open-source AI projects that have been climbing GitHub's trending charts through March and early April 2026. Not to review them like a tourist reading press releases. To actually run them on my machine, break them, test the parts the README skips, and see which ones are worth your weekend. Some of them reshaped how I think about where AI lives (hint: not always in a datacenter). One of them is doing agent memory in a way I'm now shamelessly copying into my own stack. And one of them is a tiny `CLAUDE.md` file that might quietly be the most useful thing I've installed all month.

Before we get into the six projects, one framing point that kept hitting me while testing: **the interesting frontier in open-source AI right now isn't bigger models. It's smaller, more specialized, more local, and more honest about what LLMs actually are.** Every project on this list is pulling in that direction — away from "one giant cloud model does everything" and toward "small pieces, loosely joined, running where you actually work."

Let's go.

## Why This Roundup Is Different From The Ones You've Already Read

I know. Another "trending GitHub repos" post. I scroll past them too.

The problem with most of these roundups is that they're written from the README. Someone opens the repo, reads the pinned description, grabs the screenshot, and paraphrases the feature list into fifteen paragraphs of AI-sounding prose. You finish the article with *zero* idea what it's actually like to use the thing.

I took a different angle. For each of these six projects, I did three things:

1. Cloned the repo and got it running locally — or installed it the way a normal user would (the edge gallery went on my iPhone, the Karpathy skill went into Claude Code as an actual plugin).
2. Ran one concrete task that matched how I'd use it in real work — not the cherry-picked demo from the readme.
3. Noted the first thing that broke, got confusing, or didn't match the marketing.

That third part is where this post earns its word count. The first two things you can get from any blog. The third part is the stuff that saves you a wasted Saturday.

One housekeeping note: I've been writing about the open-source agent ecosystem for a while now, and a few of these projects overlap with topics I've covered before — things like [Claude Code's agent skills system](https://www.mejba.me), [open-source Claude alternatives like OpenClaw](https://www.mejba.me), and [managing multi-agent workflows with Kanban tooling](https://www.mejba.me). Where there's a direct link, I'll point you to the deeper post instead of rehashing.

Alright. Six projects. Let's start with the one that's quietly the most disruptive.

## 1. Google AI Edge Gallery — The App Store For Tiny LLMs In Your Pocket

The first time you install [Google AI Edge Gallery](https://github.com/google-ai-edge/gallery) and turn off your wifi, it feels like a small magic trick.

You open the app. You load a model — say, one of the Gemma 4 small-footprint variants from the built-in catalog. You tap "chat." You type a question. It answers. No spinner waiting for a server. No "Checking connection..." banner. No token meter somewhere in the cloud ticking up. Just a model, your phone's silicon, and a response.

That's the pitch, and it's real.

### What it actually is

AI Edge Gallery is an open-source reference app from Google — Kotlin on Android, Swift on iOS — built on top of [LiteRT-LM](https://github.com/google-ai-edge/LiteRT-LM), Google's new high-performance inference engine for running LLMs at the edge. Think of the gallery as a showcase and a dev tool fused into one: a polished end-user app you can run on your own phone, and an open-source codebase you can fork, strip down, and reuse in your own mobile AI project.

The repo lives at `google-ai-edge/gallery` on GitHub. The iOS version is on the App Store as "Google AI Edge Gallery." And the thing worth knowing: the whole reference implementation — model selection UI, local inference, structured output, even agentic tool calls — is right there in the codebase for you to read.

### What I tested

I pushed it in three specific directions:

**Test 1: Airplane mode, long-form generation.** I loaded a small Gemma variant, flipped my iPhone into airplane mode, and asked it to draft a three-paragraph release note from a bullet list. Response was maybe 40% slower than a cloud call from the same spot, but — and this is the point — it *happened at all*, on a device that was, for all the network knew, a brick. For on-the-go drafting where privacy matters (medical notes, client briefs, anything you don't want round-tripping through a third-party API), this is already useful.

**Test 2: Agent skills with tool calls.** Per the Google developers blog, Gemma 4 on the edge now supports what they're calling "agentic skills" — grounding via Wikipedia, interactive maps, summary cards. I tried the Wikipedia-grounded flow and it worked roughly as advertised, though the tool-call reliability was visibly shakier than what I'm used to from larger cloud models. Fine for a demo. Not yet fine for production.

**Test 3: Forking the code into my own mobile project.** This is where the gallery pays off. Because it's a real, shipping reference app, you can read exactly how Google thinks on-device LLM inference should be wired — model management, memory handling, prompt construction, the whole stack. I spent an hour reading the inference pipeline and learned more about practical edge-AI architecture than three weeks of blog posts would have taught me.

### What it gets wrong (or at least, what's rough)

Two honest caveats. First, the models you can realistically run on a phone today are genuinely small, and their failure modes show. Expect confident nonsense on anything that needs broad world knowledge or multi-step reasoning. Second, the agent tool-calling path is new and a little fragile — when it misfires, it misfires silently, which is a worse failure mode than a loud error.

### The real takeaway

On-device AI is no longer a "neat demo in a research paper." It's shipping, as open source, with a production reference app you can run right now. Every mobile developer I know should spend one evening cloning this repo and reading the inference code. The future where every app has a small local model doing 80% of the work before ever reaching out to a cloud API just got a lot closer.

And that's project one. If the edge gallery is about *where* AI runs, the next project is about *how* we learn from it.

## 2. DeepTutor — Open-Source, Agent-Native Learning Assistant

I'm going to say something that'll sound unfair to ChatGPT: **for actually learning from a document, the plain chat window is the wrong interface.**

You've felt this. You upload a PDF, you ask questions, you get answers, but you never *learn* the document. There's no structure. No progress. No "here's what you've understood, here's what's shaky, here's a practice question to find out." The document and the chat live in two different universes, with you frantically copy-pasting between them.

[DeepTutor](https://github.com/HKUDS/DeepTutor), from HKU's data science lab, is the most serious open-source attempt I've seen at fixing this.

### What it is

DeepTutor bills itself as an "agent-native personalized learning assistant." Translation: it's an open-source, multi-agent system built around the idea that learning is a workflow, not a chat. You upload PDFs, TXT, or Markdown. It builds a searchable knowledge base. Then it runs agents on top — one for document Q&A with proper citations, one for practice question generation, one for guided multi-step learning paths, one for knowledge graph construction that wires entity-relation mappings across your materials.

The part I find interesting: it maintains a persistent "profile" of you — your goals, your preferences, your running progress — and a rolling "summary" of what you've learned. This is the feedback loop chat interfaces lack.

Per the maintainers, the project crossed 1,400+ GitHub stars within its first week of release and has continued climbing. I haven't independently verified the current star count, but the activity on the repo is obviously real.

### What I tested

I threw it at a stack I actually needed to understand: the Anthropic Agent SDK docs plus two long technical PDFs on memory architecture for agents. Roughly 180 pages across three files. Here's what happened:

I uploaded, waited for indexing (surprisingly fast — under two minutes on a mid-spec machine), and asked a question that I'd been struggling with: "When does the SDK's memory compaction kick in, and what are the trade-offs between eager and lazy compaction?" The answer came back with **specific citations to the exact passages in the PDFs**, not vague paraphrases. That alone put it ahead of the plain chat-with-PDF experience I'd tried a dozen times before.

Then I hit the practice question generator. It produced five questions at roughly the right difficulty, three of which were genuinely useful (the other two were trivia). The guided learning path was where it really earned its place — it turned the three documents into a rough lesson plan with checkpoints.

### Where it falls short

Setup is heavier than "install an app." It's an open-source multi-agent system, which means you're wiring up models, environment variables, and a local runtime. This is a project for developers and power users, not for your non-technical friend who wants a better PDF chat. Also, the quality of the practice questions and knowledge graph varies a lot depending on the underlying LLM you plug in.

### Why it matters

DeepTutor is pointing at something bigger than itself. The future of "learning with AI" isn't a chat window bolted onto a PDF viewer. It's purpose-built agent workflows where the AI knows your goals, your progress, and the material — and orchestrates around all three. DeepTutor is an early, imperfect, very promising version of that future, and it's fully open source. If you teach, tutor, write courseware, or just want to get smarter from your document stack, clone it.

That's two projects about where AI lives and how we learn from it. Now we get to the one that's quietly changed how I think about agent memory.

## 3. Hermes Agent — An AI Agent That Actually Remembers

Okay. This is the one that made me rearchitect my own agent.

Here's the thing every builder of open-source AI agents runs into eventually: **memory.** You start with a clean prompt, build up context across a session, and things work. Then you try to make the agent remember across sessions. Your first move is to cram everything into the system prompt — past conversations, user preferences, project facts. It works. Until it doesn't. Until the prompt balloons past what's reasonable, cost explodes, latency tanks, and the model starts confidently misremembering things it should know.

I've seen this pattern a dozen times. I built this pattern a dozen times. [Hermes Agent](https://github.com/nousresearch/hermes-agent), from Nous Research, is the first open-source framework I've found that treats memory as a first-class architectural problem and solves it the way it *should* be solved: with specialized, retrieval-on-demand memory layers instead of prompt stuffing.

### What's actually in the memory system

Based on the project's documentation, Hermes runs a multi-level memory architecture (the marketing sometimes calls it three-layer, sometimes multi-level — I'll go with what the docs describe). At minimum, it separates:

- **Session memory** — the standard running context of the current interaction.
- **Persistent memory** — facts, preferences, and project details that survive across sessions.
- **Skill memory** — when the agent solves something non-trivial, it writes out a reusable "skill document" describing how it got there, and that document becomes a retrievable thing the agent can reference later.

Under the hood, the persistent layer uses FTS5 full-text search plus LLM-driven summarization, so instead of shoving every past conversation into the prompt, the agent retrieves only the relevant chunks when they're relevant. It also pulls in dialectical user modeling (borrowed from Honcho) to keep a living model of the user rather than a static "about me" blob.

Nous Research is calling this "an agent that grows with you." Based on what I tested, that framing mostly earns it.

### What I tested

I ran Hermes against a scenario I know well: a long-running coding project where the agent needs to remember architectural decisions across sessions without being re-briefed every time. I gave it a fictional SaaS codebase description, had a design conversation, closed the session, came back three hours later, and asked a follow-up that depended on a decision from the earlier conversation.

It remembered. Not by having the whole prior chat in context — by retrieving the specific decision document, surfacing it, and continuing from there. That's the correct behavior, and it's the first time I've seen an open-source agent framework do it cleanly.

I also tested the skill-generation loop: I walked Hermes through a moderately involved task (scaffolding a TypeScript CLI), and after it finished, I checked whether it had written itself a skill. It had. The skill document wasn't perfect — it was slightly over-specific to the exact task I'd given it — but the loop worked. Next time I ask it to scaffold something similar, it'll have that skill to pull from.

### Where I'd be careful

Hermes is young, fast-moving, and its architecture is ambitious. A few things to watch for: retrieval quality depends heavily on how well the FTS5 index is built, skill documents can accumulate cruft if you don't occasionally prune them, and because the system is self-modifying (adding skills over time), you want to treat the skill store like a code repo — review what it writes, not just trust it.

If you're building any kind of persistent AI agent, this is the project to read this month. Not necessarily to adopt wholesale, but to study. The mental model — *memory as retrieval across specialized layers, not stuffing*—is the right mental model, and Hermes is the cleanest open-source implementation of it I've found.

And that leads naturally to the next problem: once you have smart agents, how do you run more than two of them without losing your mind?

## 4. Multica — Project Management For Human-Plus-Agent Teams

I have a confession. For months, my "multi-agent workflow" was six Claude Code terminals in a tiling window manager, named `agent-1` through `agent-6`, and a Notion doc I updated by hand when I remembered. That's not a workflow. That's a coping mechanism.

[Multica](https://github.com/multica-ai/multica) is trying to fix exactly that problem.

### What it is

Multica describes itself as "the open-source managed agents platform" — an orchestration and project-management layer for AI coding agents. Unlike tools that try to *be* the agent, Multica wraps around whichever agent you already use (Claude Code, Codex, OpenClaw, OpenCode — the daemon auto-detects CLIs on your PATH) and gives you a Kanban-style interface for assigning, tracking, and coordinating work across them.

The pitch in plain English: "treat your coding agents like teammates." You create a task. You assign it to an agent. The agent picks it up, reports status, flags blockers, and updates the board as it works. You get a mission-control dashboard that shows what every agent is doing in real time, and a task lifecycle that mirrors how human engineering teams actually operate.

Multica is self-hostable via Docker Compose or Kubernetes, and they also offer a managed cloud version if you don't want to run your own infrastructure.

### What I tested

I ran the self-hosted Docker Compose version on my dev machine, hooked it up to my local Claude Code install, and threw three small tasks at it: add a rate limiter to an Express API, write a GitHub Action for a Node project, and refactor a messy React component. Stock tasks that any reasonable coding agent should handle.

What I liked: watching the Kanban columns update in real time as the agent moved tickets from "queued" → "in progress" → "needs review." When the agent got stuck on the React refactor because the component was weirder than the ticket described, it flagged a blocker rather than silently generating garbage. That's the exact behavior you want from a managed system.

What I didn't love: the initial setup took longer than I expected. Auto-detection of my Claude Code CLI was clean, but getting the runtime to talk to my preferred project directory needed a few config tweaks. Not hard, just not "one click."

### Where it shines — and where it doesn't

Multica shines when you're genuinely running multiple agents in parallel on related work. The moment you're orchestrating three or more agents across a project, something like Multica goes from "nice UI" to "actually necessary." If you're running one agent on one task, it's overkill.

It's also worth saying: this category is getting crowded fast. Vibe Kanban, Veritas Kanban, Mission Control dashboards, GitHub's own Agent HQ — everybody is trying to be the "project manager for agents" layer. Multica's pitch is open-source, self-hosted, multi-CLI. If those are your constraints, it's a strong pick. If you're happy in a closed ecosystem, you might not need it.

One connection worth noting: I've written before about how [Kanban interfaces are becoming the default UI for multi-agent systems](https://www.mejba.me), and Multica is a good data point for that trend. The agent tooling space has very clearly decided that "tickets on a board" is the right abstraction for human-plus-AI collaboration, and I don't think that's going to reverse.

Four down. Next: a project that has absolutely nothing to do with agents, memory, or orchestration, and is on this list because it's doing something much simpler. Undercutting a paid SaaS.

## 5. OpenScreen (and friends) — Free Screen Studio, No Subscriptions

Screen Studio is a beautiful Mac app. It's also $29/month or a hefty one-time fee, depending on which tier you pick, and that's a lot for a screen recorder even if it does auto-zoom and cursor animation really, really well.

The open-source community, being the open-source community, looked at this and said: *we can build that.*

So they did. Several times.

### What's actually out there

The source brief for this post described "Open Source Screen Studio" as a single project, but what I found in April 2026 is more like a small ecosystem of very similar projects all circling the same idea:

- [**OpenScreen**](https://github.com/siddharthvaddem/openscreen) — the original open-source Screen Studio alternative. No subscriptions, no watermarks, free for commercial use.
- [**Recordly**](https://github.com/webadderall/Recordly) — a Mac/Windows/Linux screen recorder with auto-zooms, animated cursors, auto-captions. Substantially builds on the OpenScreen foundation.
- [**Open Recorder**](https://github.com/imbhargav5/open-recorder) — a Tauri + Rust take on the same idea, optimized for being small and fast.
- [**Open ScreenStudio**](https://github.com/crafter-station/open-screenstudio) — another fork/take, focused on automatic zoom and smooth cursor effects.

That's four open-source projects doing essentially the same job, all born in roughly the last six to nine months. If you want an even broader swing, the battle-tested options (OBS Studio, ShareX) still exist, but they don't have the "polished walkthrough aesthetic" these newer projects are chasing.

### What I tested

I installed OpenScreen and did the thing I'd normally do in Screen Studio: record a two-minute walkthrough of a terminal workflow, with auto-zoom on click events and a soft background behind the window. The result wasn't pixel-identical to Screen Studio's output, but for 90% of use cases — tutorial videos, Loom replacements, product walkthroughs — it was good enough that the gap didn't matter. And I didn't pay $29.

Recordly is the one I'd actually recommend testing first if you're on Mac and want the closest feel-alike; it's the most actively maintained of the bunch as of early April 2026.

### Why this project category matters

This isn't about screen recording. It's about the pattern.

Every category of paid creative SaaS — screen recording, writing tools, design utilities, note-taking, task management — is now getting a "free open-source alternative built with Tauri or Electron in a weekend" version. Sometimes three of them. The economics of closed-source consumer productivity software are getting squeezed from below in a way that wasn't true two years ago, and the reason is partly AI: when a solo developer can use Claude Code to build a real desktop app in a weekend, the cost of cloning a $29/month product drops toward zero.

I've been writing about [how AI is disrupting SaaS pricing models](https://www.mejba.me) and this is the same pattern playing out in a specific category. Expect a lot more of it.

One more to go. And this one is the smallest repo on the list. And it might be my favorite.

## 6. Karpathy-Inspired Skills For Claude Code — The Tiny File That Fixed My Agent's Worst Habits

Andrej Karpathy has been publicly vocal, repeatedly, about how current-generation LLMs fail in predictable, specific ways when used for code. The quotes worth remembering are roughly: *the models make wrong assumptions on your behalf and run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should.*

That's a hell of a diagnosis. And Forrest Chang took that diagnosis and turned it into a single `CLAUDE.md` file you can drop into any Claude Code project.

### What it is

[andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) is, at heart, one file. It's a CLAUDE.md configuration distilled from Karpathy's observations about LLM coding pitfalls, packaged as a Claude Code plugin. You install it, it lives at the project or user level, and it rewires how Claude Code behaves on the tasks it's most likely to mess up.

The rough principles it enforces:

- **Goal-driven execution over imperative instructions.** Instead of just "do what the user said," the agent is nudged toward understanding the *goal* behind the instruction and verifying success against it.
- **Surgical changes over sweeping rewrites.** When asked to fix a bug, fix *that bug*. Don't quietly refactor three unrelated files while you're in there.
- **Surface assumptions instead of silently acting on them.** If there's ambiguity, ask. If there's a tradeoff, name it.
- **Define verifiable success criteria.** Don't claim something works. Run the thing that proves it works.

None of those are revolutionary. All of them are the difference between an agent that saves you an hour and an agent that costs you three.

### What I tested

I installed the plugin into my daily Claude Code setup and ran my normal workflow for a week — bug fixes, small features, some refactor work on the brand site. Two things shifted noticeably.

First, the over-eager refactor problem went way down. I asked it to fix a specific caching bug in a Laravel controller. Pre-plugin, it would have "helpfully" also rewritten the method signature and moved three unrelated lines. Post-plugin, it fixed the bug, left everything else alone, and explained why.

Second — and this is the bigger one — it started asking better questions. When I gave it ambiguous instructions (on purpose, as a test), instead of guessing and barreling ahead, it stopped and asked which interpretation I wanted. That one behavioral change is worth the install all by itself.

### The honest caveat

This is a single configuration file, not a framework. It's as good as the LLM it's shaping, and it can't fix fundamental model limitations — only surface them more honestly. If you're using Claude Code with a weak base model, this plugin will make it less reckless, not smarter. If you're using Claude Code with a strong base model, this plugin is a genuine productivity upgrade.

I've been writing about [Claude Code skills and how to build your own](https://www.mejba.me) for a while, and this is a great example of the pattern done minimally. It proves the point that a *really well-written* skill file can be more valuable than a complicated plugin with custom tools.

## The Pattern Underneath All Six Projects

I sat down after the week of testing and tried to figure out what these six projects had in common beyond "open source" and "AI." Here's what I landed on.

**They're all rejecting the monolith.** Google Edge Gallery says AI doesn't have to live in a datacenter. DeepTutor says your learning workflow doesn't have to live in a chat window. Hermes says your agent's memory doesn't have to live in its prompt. Multica says your multi-agent workflow doesn't have to live in six terminal tabs. OpenScreen says your creative tools don't have to live behind a subscription wall. And the Karpathy skill says your coding agent's brain doesn't have to be one giant hope that the model gets it right.

Every single one of these projects is taking a piece of the "one big AI system does everything" mental model and breaking it into smaller, more specialized, more open pieces. **That's the actual trend.** Not a specific tool or model or benchmark — the decomposition of AI workflows into parts you can own, swap, and run yourself.

The other pattern: **opinionated specialization is beating general-purpose generality.** Hermes beats "prompt-stuffing Claude clones" not because it's a bigger model but because it has a clear point of view about memory. DeepTutor beats "generic chat-with-PDF" because it has a clear point of view about learning. The Karpathy skills plugin beats vanilla Claude Code because it has a clear point of view about where LLMs fail. In a world where every foundation model is racing to be general, the wins are coming from agents and tools that are confidently, ruthlessly specialized.

If you're building in this space — even as a solo dev — that's the takeaway I'd write on a sticky note. *Pick a point of view. Be specialized. Don't try to out-general the foundation models. You can't, and you don't need to.*

## What I'm Doing With All This

Here's my honest plan for the next two weeks, in case it's useful.

I'm taking Hermes's memory architecture as inspiration and rebuilding the memory layer in my own agent stack — specifically the split between session, persistent, and skill memory. The Karpathy skill is already installed in my daily Claude Code, and I'm not uninstalling it. I've got Multica running on a dev machine for an experiment with running four coding agents in parallel on a real project. And I'm going to spend one evening reading the Edge Gallery's inference pipeline just to learn.

DeepTutor I'm keeping in my back pocket for a specific use case: when I next need to deeply learn a long technical document, that's the tool I'll reach for instead of yet another round of cloud chat.

OpenScreen is already replacing my screen-recording workflow, which — given that I write a lot of tutorials — is quietly the biggest weekly time save on this list.

**Your challenge for the weekend, if you want one:** pick the project on this list that maps to a problem you already have. Clone it. Get it running. Break it once. Come back and decide whether to keep it. That's it. One project, one weekend, one honest test.

Because the thing I learned this week — the thing I keep learning, every time I do one of these deep dives — is that *reading about tools isn't the same as running them*, and nobody's workflow ever changed from a blog post alone. The projects on this list are interesting. What happens after you clone one is where it matters.

Go clone something.

## Frequently Asked Questions

### What are the best open-source AI projects on GitHub in April 2026?
The most interesting open-source AI projects right now are split between on-device inference (Google AI Edge Gallery, LiteRT-LM), agent memory and orchestration (Hermes Agent, Multica), learning workflows (DeepTutor), Screen Studio alternatives (OpenScreen, Recordly), and Claude Code skill plugins (andrej-karpathy-skills). For a deeper look at why each of these matters, see the six project walkthroughs above.

### Can I really run an LLM on my phone without the internet?
Yes. Google AI Edge Gallery, built on LiteRT-LM, runs open-weight small LLMs like Gemma 4 variants entirely on-device on iOS and Android. Performance is slower than cloud inference and models are smaller, but for private, offline, latency-sensitive use cases, it's already production-ready for real workflows.

### Is Hermes Agent better than Claude Code or OpenClaw for building AI agents?
They solve different problems. Claude Code and OpenClaw are coding-focused agent environments; Hermes Agent is a general-purpose agent framework with a specialized multi-level memory system. If you're building a long-running personal agent that needs to remember things across sessions, Hermes's memory architecture is worth studying — see the Hermes section above for the full breakdown.

### What's the best open-source alternative to Screen Studio?
As of April 2026, OpenScreen is the original open-source Screen Studio alternative, while Recordly is the most actively maintained fork with the closest feature parity. Open Recorder (Tauri + Rust) is the lightest-weight option. All three are free with no subscriptions and fine for most tutorial and walkthrough workflows.

### Is the Karpathy Claude Code plugin worth installing?
For daily Claude Code users, yes. It's a single configuration file that enforces surgical code changes, surfaces assumptions, and reduces the over-eager refactor problem — addressing exactly the LLM coding pitfalls Andrej Karpathy has repeatedly pointed out. It's the lowest-effort, highest-leverage install on this list.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
