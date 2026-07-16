**BRAND:** mejba.me
**TITLE:** Anthropic's Agent Harness Design Changed How I Think
**SLUG:** anthropic-long-running-agent-harness
**PRIMARY KEYWORD:** long-running agent harness
**SECONDARY KEYWORDS:** agent harness design, context anxiety AI agents, adversarial evaluation agents
**META DESCRIPTION:** Anthropic's long-running agent harness turns raw AI models into reliable systems. Here's the architecture, the failure modes, and what it means for builders.
**TAGS:** AI Agents, Anthropic, Claude Agent SDK, Agent Architecture, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand the three-agent harness architecture (planner, generator, evaluator), recognize context anxiety and self-evaluation failure modes in their own agent workflows, and have a concrete mental model for building long-running autonomous systems.

---

# Anthropic's Agent Harness Design Changed How I Think About AI Systems

I was four hours into watching an AI build a digital audio workstation. Not a toy demo — a browser-based DAW with the Web Audio API, multi-track editing, and a waveform visualizer. The agent had been running autonomously the entire time. No hand-holding. No mid-session corrections. Just an AI grinding through feature after feature, committing code, testing its own work, and moving forward.

Then it stopped.

Not because it crashed. Not because it ran out of context. It stopped because a separate AI — the evaluator — told it the audio playback timing was off by 12 milliseconds and the waveform rendering didn't match the actual frequency data. The generator agent went back, fixed both issues, and submitted again. The evaluator tested it a second time, confirmed the fixes, and greenlit the sprint.

That interaction — two AI agents arguing about quality like a developer and a QA engineer — is the core of what Anthropic published in their [harness design for long-running application development](https://www.anthropic.com/engineering/harness-design-long-running-apps). And it completely rewired how I think about building autonomous systems.

The model isn't the product. The harness is.

---

## Why Your AI Agent Keeps Failing at Complex Tasks

Here's a pattern I've lived through dozens of times. You give an AI agent a complex project — build a full-stack app, refactor a large codebase, create a multi-page design system. The agent starts strong. First 20 minutes look promising. Then somewhere around the 45-minute mark, things start going sideways. Steps get skipped. Quality drops. The agent declares the project "complete" when it's maybe 60% done.

I used to blame the model. "Sonnet isn't smart enough for this." "I need better prompts." "The context window is too small."

Anthropic's research flipped that diagnosis entirely. The model was rarely the bottleneck. The *harness* was.

An agent harness is the software framework that wraps around an AI model — managing prompts, orchestrating tools, enforcing constraints, handling feedback loops, and validating output. Think of it this way: the AI model is an engine. Raw horsepower. The harness is the car. Steering wheel, brakes, transmission, GPS. Without the car, the engine just sits on a workbench making noise.

This distinction matters more than most AI engineers realize. You can swap a V6 for a V8 (Sonnet for Opus), and you'll get incremental improvement. But redesign the car — change how the engine connects to the wheels, add a navigation system, install better brakes — and the entire driving experience transforms.

That's what Anthropic demonstrated. Not a better model. A better system around the model. And the results were dramatic enough that I rebuilt my own agent workflows within a week of reading their paper.

But before I get into what they built, you need to understand what keeps going wrong in the first place — because these failure modes are hiding in every long-running agent system I've seen, including ones I built myself.

---

## The Two Failure Modes Nobody Talks About Honestly

Anthropic identified two critical failure modes in long-running agents. I've experienced both so many times that reading their analysis felt like someone had installed cameras in my development environment.

### Context Anxiety: When Your Agent Panics

Context anxiety is what happens when an AI model senses it's approaching the limits of its context window. The behavior is subtle and insidious. The agent doesn't crash. It doesn't throw an error. Instead, it starts rushing. Steps get compressed. Explanations become terse. Complex sub-tasks that should take careful planning get hand-waved away. The agent starts wrapping up prematurely — declaring victory on a half-finished project because some internal signal is screaming "you're running out of room."

I first noticed this with Sonnet 4.5 during a refactoring project. Around the 80K token mark, the agent shifted from careful, methodical work to what I can only describe as anxious speed-running. It stopped reading files before editing them. It skipped test runs. It started outputting comments like "the remaining items are straightforward and follow the same pattern" — which is agent-speak for "I'm giving up and hoping you don't notice."

Anthropic's solution involves two complementary techniques.

**Context compaction** summarizes and condenses the conversation history, preserving the essential information while freeing up token space. The AI essentially writes itself a compressed briefing of everything that's happened so far, then works from that briefing instead of the raw history. It's lossy — some detail gets dropped — but a good compaction strategy preserves the critical decisions and current state.

**Context resets** go further. Instead of compressing the existing context, you give the agent a completely fresh context window for each sprint or sub-task. The agent starts clean, with only the information it needs for the current piece of work. Progress doesn't live in the model's memory — it lives in files, git commits, and structured artifacts on disk.

Here's the breakthrough that matters for builders: Opus 4.6 with its 1 million token context window has largely eliminated context anxiety as a practical concern. The window is so enormous that most multi-hour autonomous sessions never come close to filling it. Anthropic found they could drop context resets entirely with Opus 4.6 and rely on compaction alone. What used to require a complex multi-session handoff system now runs as one continuous session.

That doesn't mean context management is solved forever. Push Opus 4.6 hard enough — a full-day autonomous session with extensive file reading — and you'll hit limits. But the threshold moved from "a serious problem after 45 minutes" to "an edge case after 6+ hours." For most real-world tasks, that's the difference between needing a harness workaround and not needing one at all.

### Self-Evaluation Failure: The Agent That Thinks Everything It Makes Is Great

The second failure mode is more dangerous because it's harder to detect.

When you ask an AI agent to evaluate its own work, it lies. Not deliberately — but consistently. The pattern is predictable: the agent generates output, reviews it, and concludes that the output is good. Even when, to a human observer, the quality is obviously mediocre.

I've watched this happen in real time. An agent builds a landing page with misaligned elements, inconsistent spacing, and a color scheme that looks like it was chosen by random number generator. You ask the agent to evaluate the result. "The design is clean and professional, with good visual hierarchy and consistent spacing." It's describing the design it *intended* to build, not the one it actually built.

This isn't a prompting problem. You can add "be critical" and "look for flaws" to the system prompt, and the agent will nod, pretend to be critical, and then approve its own work with a few token qualifications. "While there's room for improvement in the color palette, the overall design effectively communicates the brand message." Translation: "I'm pretending to be critical while changing nothing."

Anthropic's diagnosis is blunt and, in my experience, accurate: making a generator self-critical is a fundamentally harder problem than building a separate, dedicated critic. The architectural solution isn't to make the generator better at self-evaluation. It's to separate the roles entirely.

Which brings us to the architecture that actually works.

---

## The Three-Agent Architecture: Planner, Generator, Evaluator

Anthropic's final harness design uses three specialized agents working in concert. If you've ever worked on a team with a product manager, a developer, and a QA engineer — this will feel familiar. Because that's exactly the dynamic they replicated.

### The Planner

The Planner takes a simple prompt — "build a project management app with a Kanban board" — and expands it into a detailed product specification. Feature lists. User stories. Technical requirements. Visual design direction.

The Planner is deliberately ambitious. Anthropic found that conservative planning led to underwhelming results. When the Planner aimed high, the Generator produced more creative, more complete applications — even if it didn't hit every target. A spec that asks for "a beautiful, museum-quality interface with 3D elements" produces better results than one that asks for "a clean, functional UI." The high bar pulls the output upward.

One thing Anthropic learned the hard way: the Planner should focus on *what* and *why*, not *how*. When the Planner included granular technical implementation details, those details would cascade errors through the Generator's work. A Planner that says "implement real-time collaboration" is better than one that says "use WebSockets with a pub/sub pattern on Redis" — because the Generator might find a better approach that the Planner's premature specificity would have prevented.

### The Generator

The Generator works in sprints, implementing one feature at a time. Each sprint has a clear scope derived from the Planner's spec, and the Generator commits code incrementally — meaning progress is preserved in git regardless of what happens to the agent's context.

This sprint-based approach is where I see the strongest parallel to how I already use Claude Code. When I'm working with [Claude Code's agent swarm architecture](/content/mejba.me/claude-code-agent-swarm-architecture.md), the same principle applies: break complex work into discrete chunks, make progress visible through artifacts (files, commits, task graphs), and never rely on the model's memory as the source of truth.

The Generator also includes a built-in self-evaluation step before handing off to the Evaluator — a quick sanity check. This catches the obvious issues (syntax errors, missing imports, broken builds) without relying on the Evaluator for things the Generator should catch itself. Think of it as a developer running their own tests before opening a PR. You still need code review, but you shouldn't be wasting the reviewer's time on issues your linter would catch.

### The Evaluator: Where the Real Innovation Lives

The Evaluator is what makes this architecture genuinely different from anything I've built before.

Most AI evaluation systems review code statically — they read the source files, analyze the structure, and give feedback. Anthropic's Evaluator doesn't just read code. It *uses* the application. Through Playwright browser automation, the Evaluator navigates the running application, clicks buttons, fills forms, takes screenshots, tests API endpoints, and probes database states. It interacts with the live product the way a human QA engineer would.

The evaluation criteria aren't vague either. Anthropic defined four specific dimensions:

**Design Quality** — Does the visual output meet professional standards? Layout, typography, color, spacing — measured against the Planner's spec, not abstract ideals.

**Originality** — Does the output look like generic AI slop, or does it have genuine creative identity? This criterion exists specifically to fight the "everything AI builds looks the same" problem. When I read this, I immediately thought of the [AI design systems guide](/content/mejba.me/ai-app-design-systems-guide.md) I wrote — the problem of AI-generated interfaces all converging on the same aesthetic is real, and Anthropic is explicitly grading against it.

**Craft** — Technical execution quality. Clean code, proper error handling, responsive layouts, performance. The stuff a senior engineer would notice during code review.

**Functionality** — Does the feature actually work? Not "does the code look correct" — does the button do what it's supposed to do when you click it?

The Evaluator grades each sprint against these criteria. If the grades don't meet the threshold, the Generator goes back and iterates. This creates a feedback loop that's structurally identical to how [GANs (Generative Adversarial Networks)](https://en.wikipedia.org/wiki/Generative_adversarial_network) work — a generator trying to produce output that fools the discriminator, and a discriminator getting better at catching deficiencies.

Here's what surprised me: getting the Evaluator right took multiple iterations. Anthropic's initial Claude-based evaluators had the same problem as self-evaluation — they were too lenient. They'd approve mediocre work with encouraging feedback. It took deliberate prompt engineering to make the Evaluator genuinely adversarial. A skeptical system prompt. Explicit instructions to look for specific types of failure. Permission to reject work and demand revisions.

This is the key insight I keep coming back to: **tuning a dedicated evaluator to be skeptical is a fundamentally more tractable problem than making a generator self-critical.** The evaluator has one job — find problems. The generator has a conflicting incentive — it wants to be done. Separating these roles isn't just good architecture. It's necessary.

---

## The Sprint Contract: Aligning Generator and Evaluator Before Work Begins

One detail from Anthropic's design that I haven't seen discussed enough elsewhere is the sprint contract.

Before each sprint begins, the Generator and Evaluator negotiate a contract. The Generator proposes what it will deliver. The Evaluator confirms it understands the acceptance criteria. Both agents agree on what "done" means before a single line of code gets written.

This sounds bureaucratic. It's not. It's the difference between a PR that gets approved on the first review and one that bounces back and forth five times because the developer and reviewer had different expectations.

Without the contract, I've watched evaluator agents reject work for not meeting standards that were never communicated. The Generator builds a feature one way, the Evaluator wanted it another way, and the iteration loop burns tokens arguing about requirements that should have been settled upfront.

The contract solves this by making expectations explicit. "Sprint 3 will implement the level editor with drag-and-drop sprite placement, a tile palette of at least 20 items, and undo/redo support. The Evaluator will verify functionality by creating a test level, placing sprites, and confirming undo works for at least 5 sequential operations."

That specificity — naming exact acceptance tests — eliminates an entire class of wasted iterations. And it's a pattern I've started borrowing for my own agent workflows, even outside of Anthropic's harness design.

---

## What the Experiments Actually Showed

Theory is one thing. Anthropic ran three experiments that put this architecture under real pressure. The results tell different stories depending on which one you look at.

### Experiment 1: The Dutch Art Museum Website

The task: design a website for a fictional Dutch art museum. This is a subjective, design-heavy challenge where "good" is hard to define and harder to evaluate.

The harness ran through more than 10 feedback iterations between the Generator and Evaluator. Early versions were generic — the kind of template-looking pages any AI produces. By iteration 7 or 8, the design had evolved into something genuinely unusual: a 3D room visualization with a checkered floor, artwork displayed on virtual walls, and navigation that felt like walking through a gallery.

The point isn't that the final design was perfect. It's that the iterative adversarial loop pushed the output past the "good enough" plateau that single-pass AI generation always hits. The Generator kept reaching for more creative solutions because the Evaluator kept rejecting the safe ones. That dynamic — pressure to exceed, not just meet, standards — doesn't happen when an agent evaluates itself.

### Experiment 2: The 2D Retro Game Maker

This was the most telling experiment. Two runs of the same task: build a 2D retro game maker with a level editor, sprite editor, and behavior system.

**Run 1 (solo harness — Generator only):** The output looked visually acceptable in screenshots. But when you actually tried to use it, the game didn't work. Sprites didn't move. The level editor couldn't save levels. It was a Potemkin village of a game maker — presentable from the outside, hollow on the inside.

**Run 2 (full harness — Planner + Generator + Evaluator):** The Planner created detailed specs with explicit definitions of done for each sprint. The Evaluator tested every feature interactively using Playwright, catching functional failures the Generator would have declared "complete." The result: a working game maker. Not AAA quality, but functional. You could create sprites, build levels, assign behaviors, and actually play the result.

The delta between these two runs is the entire argument for harness engineering in one example. Same model. Same task. Same compute budget. The only difference was the system wrapped around the model.

### Experiment 3: The Browser DAW

The most ambitious test: build a digital audio workstation in the browser using the Web Audio API, running on Opus 4.6.

This experiment completed in approximately 4 hours at a cost of roughly $125 in API calls. The result was functional — multi-track audio, waveform visualization, basic editing — but not professional-grade. More importantly, this experiment demonstrated how model improvements simplify harness requirements.

With Opus 4.6's million-token context window, Anthropic dropped sprints entirely. No context resets. No contract negotiation between Generator and Evaluator. Just one continuous session with automatic compaction handling context growth. The harness that Sonnet 4.5 needed — complex, multi-session, carefully managing context boundaries — became unnecessary.

This is the most important takeaway for builders: **your harness assumptions have to evolve with the model.** A harness designed for Sonnet 4.5's limitations will over-engineer the orchestration layer when running on Opus 4.6. And a harness designed for Opus 4.6's capabilities will break when the next model introduces new behaviors or eliminates old ones. Harness engineering isn't a one-time setup. It's a continuous practice.

If you'd rather have someone build this kind of multi-agent harness system for your use case, I take on custom AI agent development engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

---

## How This Connects to Patterns You Already Know

Anthropic's harness design doesn't exist in a vacuum. It sits alongside two other patterns that every serious agent builder should understand — because they solve adjacent pieces of the same puzzle.

### The Ralph Wiggum Loop

Created by [Geoffrey Huntley](https://github.com/ghuntley/how-to-ralph-wiggum) and formalized by Boris Cherny (Anthropic's Head of Claude Code) in late 2025, the Ralph Wiggum Loop is elegant in its simplicity. You run an agent in a bash loop. After each iteration, you check the output against something that can't lie — a linter, a type checker, a test suite. If it passes, you stop. If it fails, you loop.

The key insight: **progress lives in files and git history, not in the model's context window.** Each iteration starts with fresh context. The agent reads the current state of the codebase, works on it, and commits changes. If the loop needs to continue, the next iteration reads the updated files with zero accumulated context debt.

This pattern is now a [first-class plugin in Claude Code itself](https://github.com/anthropics/claude-code/blob/main/plugins/ralph-wiggum/README.md). You can run `/ralph-loop "Fix all TypeScript errors" --max-iterations 20 --completion-promise "ALL_FIXED"` and walk away.

Where the Ralph Wiggum Loop differs from Anthropic's three-agent harness: it works best for objectively verifiable tasks. "Fix all linting errors" has a binary ground truth — the linter passes or it doesn't. "Build a beautiful, functional game maker" doesn't. That subjective gap is exactly what the adversarial evaluator fills.

### Spec-Driven Development

The third piece of the puzzle is structuring requirements before the agent starts coding. Frameworks like [GitHub's Spec Kit](https://github.com/github/spec-kit), [BMAD-METHOD](https://medium.com/@ap3617180/steering-the-agentic-future-a-technical-deep-dive-into-bmad-spec-kit-and-openspec-in-the-sdd-4f425f1f8d2b), and OpenSpec all solve the same problem: agents that start coding before they understand what they're building.

This maps directly to the Planner agent in Anthropic's design. The Planner is essentially an automated spec writer. But you don't need a dedicated Planner agent to get the benefit. Writing a clear spec — features, acceptance criteria, definition of done — before handing work to an agent produces dramatically better results than throwing a vague prompt at a model and hoping for the best.

The spec-driven approach also explains why the game maker experiment showed such different results between solo and full harness runs. The solo run had no spec. The agent made it up as it went, scoping down ambitions with each step until "game maker" became "some UI elements that look game-related." The full harness run had an explicit spec with definitions of done — and an evaluator that enforced them.

I've been using a version of this in my own work since building the [Anthropic Agent SDK guide](/content/mejba.me/anthropic-agent-sdk-guide.md). Every complex agent task starts with a written spec now. Not because the agent can't work without one — but because the spec is the anchor that prevents scope drift, premature completion, and the slow death of ambition that afflicts every long-running AI session.

---

## What This Means for How You Build Agents Right Now

Here's where I want to be practical. Anthropic's research is built on the Claude Agent SDK, but the principles apply regardless of which model or framework you're using. If you're building anything that runs autonomously for more than 15 minutes, here's what I'd take from this work.

### Separate generation from evaluation. Always.

This is the single highest-leverage change you can make to any agent workflow. If your agent generates output and evaluates that output in the same context, you have a reliability problem. It might not show up on simple tasks. But the moment complexity increases — multi-file changes, subjective quality requirements, feature interdependencies — self-evaluation fails silently.

You don't need a sophisticated adversarial system to start. A second agent with a skeptical system prompt that reviews the first agent's output is enough to catch the most egregious self-approval failures. I run a version of this in my content pipeline, and the evaluator rejects roughly 30% of first drafts — drafts that, left unchecked, would have shipped with structural issues I'd catch during manual review.

### Use artifacts, not memory, as your source of truth

Every piece of progress should exist outside the model's context window. Git commits. JSON task files. Markdown specs. Structured logs. If your agent's session crashes and the only record of what happened lives in the conversation history — you've built a fragile system.

This is the same principle behind Claude Code's [persistent task graph architecture](/content/mejba.me/claude-code-agent-swarm-architecture.md). The task graph lives on disk. Agents read it, update it, and write back to it. Context windows come and go. The task graph persists.

### Match your harness complexity to your model's capabilities

Anthropic's own evolution proves this point. The harness they built for Sonnet 4.5 — with context resets, sprint boundaries, contract negotiation — was necessary because the model needed it. The harness they built for Opus 4.6 dropped half of those components because the model's million-token context and improved consistency made them unnecessary.

Audit your own harness. Are you running complex context management because the model actually needs it, or because you designed the system six months ago when the model *did* need it? Over-engineering the orchestration layer wastes tokens, adds latency, and introduces failure points that the current model could simply handle on its own.

### Define "done" before you start

The sprint contract pattern — where Generator and Evaluator agree on acceptance criteria before work begins — applies to every agent interaction, not just multi-agent systems. When you prompt an agent to "build a dashboard," define what the dashboard must include, how you'll verify each feature, and what quality bar you expect. The more explicit your definition of done, the less room the agent has to declare premature victory.

---

## The Honest Limitations: What Harness Design Can't Fix

I'd be doing you a disservice if I let you walk away thinking harness engineering is a silver bullet. It isn't. After spending weeks implementing these patterns, here are the walls I've hit.

**Cost adds up fast.** The browser DAW experiment cost $125 in API calls for ~4 hours of autonomous work. A three-agent system burns roughly 3x the tokens of a single-agent approach because you're running three models (or three sessions of the same model) in coordination. For hobby projects and experiments, this is fine. For production systems running 24/7, the economics need careful modeling.

**Evaluator quality is the ceiling.** Your system can only be as good as your evaluator's ability to detect problems. If the evaluator can't identify a subtle UI misalignment or a race condition in async code, those issues ship. Anthropic acknowledged this — their initial evaluators missed subtle bugs and approved shallow implementations. Getting the evaluator right took iteration, not just prompt tweaking.

**Subjective quality remains hard.** The Dutch museum experiment showed impressive improvement through adversarial iteration. But "impressive for AI" and "impressive compared to a human designer with 10 years of experience" are different bars. Harness design narrows that gap. It doesn't close it. For subjective creative work — design, writing, music — human evaluation remains necessary at the final stage.

**Harness debugging is its own skill.** When a single agent produces bad output, debugging is straightforward: read the conversation, find where it went wrong, fix the prompt. When a three-agent system produces bad output, the failure could be in the Planner's spec, the Generator's implementation, the Evaluator's criteria, or the interaction between any two of them. I've spent more time debugging harness interactions than I ever spent debugging individual agent prompts.

These are real limitations. They don't invalidate the approach — the game maker experiment proves the three-agent architecture produces dramatically better results than solo generation. But they mean harness engineering is a practice that demands iteration, monitoring, and continuous refinement. Not a pattern you implement once and forget.

---

## Where This Goes Next

Anthropic published two articles on this topic — [the first in November 2025](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) focused on the initializer-and-coding-agent pattern, and [the March 2026 follow-up](https://www.anthropic.com/engineering/harness-design-long-running-apps) introduced the full three-agent architecture with adversarial evaluation. The trajectory tells you where this is heading.

Model improvements simplify harness design. Opus 4.6 eliminated the need for context resets. The next generation might eliminate the need for explicit context compaction. But model improvements don't eliminate the need for *orchestration and evaluation*. Even a hypothetically perfect model — one with infinite context, perfect memory, and zero reasoning errors — would still produce better output with a separate evaluator challenging its work. That's not a model limitation. That's a fundamental insight about how quality emerges from adversarial pressure.

The practical prediction I'd make: within 12 months, harness-as-a-service platforms will be as common as RAG-as-a-service platforms are today. Tools like [Vercel's Ralph Loop Agent](https://github.com/vercel-labs/ralph-loop-agent) and the growing ecosystem around spec-driven development frameworks like GitHub's Spec Kit are early signals. The raw material — models, tool-use capabilities, evaluation APIs — is already production-ready. What's missing is the packaged orchestration layer that makes it accessible to engineers who don't want to build harness infrastructure from scratch.

For builders working in this space right now, the opportunity is clear. The harness is the product. The model is a commodity. The teams and engineers who get good at harness design — who learn to decompose tasks, build reliable evaluators, manage context effectively, and iterate their orchestration patterns alongside model improvements — will build AI systems that work in production while everyone else is still fighting with single-agent prompts that fall apart after 30 minutes.

That DAW session I watched? Four hours of autonomous coding that produced a functional application. Not because the model was magic. Because the system around it was engineered to keep the model honest, keep it focused, and keep it iterating until the work was actually done.

That's the lesson. Not better AI. Better systems around AI.

---

## Frequently Asked Questions

### What is an agent harness in AI development?

An agent harness is the software framework that wraps around an AI model to manage prompts, tools, feedback loops, and validation — turning a raw model into a reliable autonomous system. Think of it as the car that makes the engine useful: steering, brakes, navigation, and all. For a deeper breakdown, see the "Why Your AI Agent Keeps Failing" section above.

### How does context anxiety affect AI agents?

Context anxiety occurs when AI models sense they're approaching context window limits and begin rushing, skipping steps, or declaring tasks complete prematurely. Sonnet 4.5 exhibited this behavior around 80K tokens. Opus 4.6's million-token window largely eliminates the problem for sessions under 6 hours. See the failure modes section for mitigation strategies.

### What is the difference between context compaction and context reset?

Context compaction summarizes conversation history to free token space while preserving key information — lossy but continuous. Context reset starts the agent with a completely fresh context window for each sub-task, relying on files and artifacts for state. Newer models with larger context windows favor compaction over resets.

### How does adversarial evaluation improve AI agent output?

Adversarial evaluation separates content generation from quality assessment using two distinct agents — inspired by GAN architecture. The evaluator agent uses tools like Playwright to interactively test running applications, catching functional failures that code review alone misses. Anthropic's experiments showed this produces working applications where solo generation produces visually similar but non-functional output.

### Can I implement agent harness patterns without the Claude Agent SDK?

Yes. The principles — task decomposition, artifact-based state, separated evaluation, sprint contracts — are model-agnostic and framework-agnostic. The Claude Agent SDK provides convenient abstractions, but you can implement the same patterns with any model API, a Python orchestration layer, and standard tools like Playwright for evaluation.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
