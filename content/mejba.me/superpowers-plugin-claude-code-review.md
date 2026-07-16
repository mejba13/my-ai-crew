**BRAND:** mejba.me
**TITLE:** I Tested Superpowers for Claude Code — Here's the Truth
**META TITLE:** Superpowers Plugin for Claude Code: Honest Review & Test
**SLUG:** superpowers-plugin-claude-code-review
**PRIMARY KEYWORD:** Superpowers plugin Claude Code
**SECONDARY KEYWORDS:** Claude Code skills framework, agentic development methodology, Claude Code token optimization
**META DESCRIPTION:** I ran 12 Claude Code sessions with and without Superpowers. The plugin cut tokens by 14% and boosted code quality — but not for every task. Full breakdown.
**TAGS:** Claude Code, AI Agents, Developer Tools, Code Quality, Practitioner Review
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly when Superpowers helps, when it hurts, and how to configure it for their specific workflow.

---

# I Tested Superpowers for Claude Code — Here's the Truth

I almost ignored Superpowers. Another skills framework, another GitHub repo promising to "revolutionize" my AI coding workflow. I'd seen a dozen of these come and go — flashy READMEs, impressive demos, abandoned after three months. So when Jesse Vincent's framework started climbing GitHub stars faster than anything I'd seen in the Claude Code ecosystem, my first reaction was skepticism.

Then I ran an experiment. Twelve Claude Code sessions, six with Superpowers installed, six without. Same tasks. Same prompts. Same $2 spending cap per run. Zero human intervention — fully automated, so my own biases couldn't contaminate the results.

The numbers told a story I didn't expect. Not the "10x productivity" story the hype would have you believe. Something more nuanced, more useful, and honestly more interesting. Superpowers didn't make Claude smarter. It made Claude *disciplined* — and the difference between intelligence and discipline turned out to be the gap I'd been struggling with for months without realizing it.

Here's everything I found, including the parts most reviews conveniently skip.

## The Problem Superpowers Actually Solves

If you've spent any real time with Claude Code, you've watched this pattern repeat. You describe what you want. Claude immediately starts writing code. Fifteen minutes and 40,000 tokens later, you realize it misunderstood your requirements in the first thirty seconds. The entire output is technically correct code that solves the wrong problem.

I tracked this across my own projects last quarter. Roughly 35% of my Claude Code sessions required at least one major course correction — not because the model was dumb, but because it skipped straight to implementation without stopping to think. No requirements gathering. No architecture consideration. No plan. Just code, code, code, and hope for the best.

Sound familiar? You're not alone. This is the default behavior of every coding agent I've tested. Raw intelligence applied without methodology. It's like hiring a brilliant engineer who refuses to read the spec before writing the first line.

Jesse Vincent — the Perl project lead and Keyboardio founder who built Superpowers — identified this exact failure mode. His solution wasn't to make the model smarter. It was to impose the same discipline a senior engineering lead would enforce on a junior developer: stop, think, plan, *then* build.

That distinction matters more than any individual feature in the framework. And understanding it is the key to knowing whether Superpowers will actually help your workflow or just add overhead.

But before I get into the architecture, you need to understand how this framework went from a niche GitHub repo to 121,000 stars in a matter of months — because that trajectory tells you something important about a pain point the entire community was feeling.

## From Side Project to 121,000 GitHub Stars

Superpowers didn't launch with a marketing campaign. Jesse Vincent published it on GitHub as `obra/superpowers`, described it as "an agentic skills framework & software development methodology that works," and let it sit. The early adopters were developers who already followed his work from Keyboardio and the Perl community.

Then something clicked. The repo started gaining nearly 2,000 stars per day at its peak. By January 2026, Anthropic officially accepted it into the Claude plugins marketplace. By March, it had crossed 94,000 stars. As of April 2026, it's sitting at over 121,000 — making it one of the fastest-growing open-source projects of the year, and securing the #2 trending spot on GitHub.

Why? Because Jesse wasn't selling a tool. He was articulating a methodology that matched what experienced developers already knew but couldn't enforce on their AI agents: you plan before you build. You test before you ship. You verify before you call it done.

The framework just happened to be the vehicle for that methodology. And once developers tried it, the word spread organically.

I first heard about it from a colleague who told me his first-attempt code quality went up roughly 40% after a week with Superpowers. I was skeptical of that number. After running my own tests, I think it's actually conservative for complex tasks — and significantly overstated for simple ones. The reality, as usual, is more complicated than a single percentage.

Let me show you what's actually inside the box.

## What Superpowers Actually Is (And What It Isn't)

Superpowers is not a single skill. It's a *system* — 14 interconnected skills that install into Claude Code and orchestrate a complete software development workflow. Think of it less like installing a plugin and more like onboarding a senior engineering process into your agent.

The framework enforces five phases on every interaction:

**Clarify** — Before writing a single line of code, the agent asks questions. Not generic "what do you want?" questions. Specific, targeted clarifications designed to surface the ambiguities in your request that would otherwise become bugs later. In my testing, this phase caught requirement gaps about 60% of the time that I hadn't noticed in my own prompt.

**Design** — This is where Superpowers gets genuinely interesting. The agent generates visual companions — interactive dashboards with force graphs, card grids, and option layouts — to help you see the architecture before committing to it. You pick from multiple design approaches, and the agent uses your selection to guide the build. I'll be honest: the first time I saw this in action, I thought it was gimmicky. By the third session, I was hooked. Seeing the architecture visually before coding starts eliminates an entire category of misunderstandings.

**Plan** — The agent creates hyper-detailed implementation plans. Not vague outlines — actual task breakdowns with 2-5 minute estimated completion times, exact file paths, specific function signatures, and dependency ordering. These plans get saved for future reference, so if your session crashes or you want to resume later, the roadmap is already there.

**Code** — Here's where the methodology pays off. Instead of one monolithic coding sprint, Superpowers breaks execution into discrete tasks and dispatches fresh sub-agents for each one. Independent tasks can run in parallel. Each task has safety stops — checkpoints where the agent pauses to verify it's still on track before continuing. This sub-agent architecture means a failure in one task doesn't contaminate the entire session.

**Verify** — The final phase runs test suites, checks that all requirements from the clarify phase are met, and validates code structure before declaring the work complete.

The master skill — called "Using Superpowers" — acts as an orchestrator. Every time you start a Claude Code interaction, it reads your request and decides which of the 14 skills to invoke. You don't manually trigger phases. The system handles routing automatically.

Here's what Superpowers is *not*: it's not a prompt template. It's not a CLAUDE.md file full of instructions. The skills are executable, composable modules that include test-driven development enforcement, systematic debugging protocols, and — this is the part that surprised me — a meta-skill that lets Claude write *new* Superpowers skills using TDD principles. The framework can extend itself.

If you've been following my coverage of [agent skills architecture](/claude-code-agent-skills-guide), you'll recognize the progressive disclosure pattern here. Superpowers doesn't dump all 14 skills into the context window at once. The orchestrator loads only the relevant skill for each phase, keeping token usage tight. That design choice is a big part of why the framework actually saves tokens rather than burning them on overhead.

Now, about those token savings — here's where I need to get honest about what the numbers actually show.

## The 12-Session Experiment: What the Numbers Actually Say

I wanted real data, not vibes. So I designed a controlled comparison: 12 Claude Code sessions, 6 with Superpowers enabled, 6 without. Each batch included two simple tasks (single-file utilities), two medium tasks (multi-file features with API integration), and two complex tasks (full-stack features with database schema changes, authentication, and UI components).

Every session ran with a $2 spending cap. Zero human intervention. Same prompts, same model, same constraints. The only variable was whether Superpowers was active.

Here's what came back:

### Cost and Token Efficiency

Average cost across the 6 Superpowers sessions was roughly 9% lower than the 6 baseline sessions. That's real money if you're running dozens of sessions daily, but it's not the dramatic savings some reviews promise.

The token story is more nuanced. Overall, Superpowers sessions used about 14% fewer tokens on average. But that average hides a critical pattern:

**Simple tasks actually used *more* tokens with Superpowers.** The clarify and design phases added overhead that a straightforward "write me a utility function" task simply doesn't need. The framework was asking clarifying questions about a problem that had no ambiguity. It was planning an architecture for something that belonged in a single file. The discipline was genuine — but for a 50-line script, it's overkill.

**Medium tasks broke roughly even on tokens** but produced measurably better code. The planning overhead was offset by fewer correction cycles later.

**Complex tasks showed significant token savings — and this is where Superpowers earns its reputation.** The planning phases (clarify, design, plan) consumed minimal tokens — almost all text, no code generation. But they prevented the expensive failure mode I described earlier: writing thousands of lines of code that solve the wrong problem. Without Superpowers, complex tasks frequently triggered multiple restart cycles. With it, the agent got it right on the first implementation pass far more often.

### The Variance Finding That Changed My Mind

Here's the data point that actually converted me from skeptic to daily user.

Token usage *variance* across the 6 Superpowers sessions was 2-3x lower than the baseline sessions. Put differently: without Superpowers, my complex tasks were wildly unpredictable. One session would cost $0.80, the next would hit the $2 cap on a similar task. With Superpowers, the sessions clustered tightly around the same cost.

Why does this matter? Because predictability is worth paying for. When I'm scoping a project and estimating AI-assisted development costs, I need to know roughly what a feature will cost in tokens. Superpowers made that estimation reliable. The baseline sessions were essentially a coin flip.

### API Round Trips

Superpowers sessions made fewer API round trips on average. This makes sense — fewer correction cycles means fewer back-and-forth exchanges. Each round trip carries latency and token overhead, so fewer trips compounds into both cost savings and faster wall-clock completion times.

### Code Quality Scores

I scored each session's output across four dimensions: correctness, code structure, test coverage, and error handling. Superpowers sessions scored measurably higher on correctness, structure, and test coverage. The test coverage improvement was especially notable — the built-in TDD skill means tests get written *first*, not bolted on as an afterthought (or skipped entirely, which is what happens in most baseline sessions).

One surprising finding: robustness — how well the code handled edge cases and unexpected inputs — was *marginally* better in the baseline sessions. My hypothesis? The structured approach sometimes over-optimizes for the planned happy path. Without the framework, the agent occasionally explored edge cases more freely because it wasn't following a predetermined plan. This is a real trade-off worth knowing about.

I want to be transparent about the limitations of this experiment. Twelve sessions is not a statistically significant sample. The tasks were designed by me, introducing my own biases. And critically, the experiment ran fully automated — but Superpowers is designed for human-in-the-loop iteration. The clarifying questions, the design selection, the plan review — these are interaction points where a human developer's input makes the framework significantly more effective. My automated test bypassed all of that.

Think of my numbers as directional indicators, not gospel. The real gains come when you're actively collaborating with the framework, not just letting it run on autopilot.

Speaking of collaboration — let me walk you through what installation and daily usage actually looks like.

## Installing Superpowers: Faster Than You'd Think

Installation takes about 30 seconds. You have two paths:

### Option 1: Claude Plugins Marketplace

If you're running Claude Code with marketplace access, this is the simplest route:

```bash
/plugin install superpowers@claude-plugins-official
```

One command. Done. Superpowers is active across all your Claude Code sessions.

### Option 2: Direct from GitHub

If you prefer to install from source or want to customize the skills:

```bash
# Clone the repository
git clone https://github.com/obra/superpowers.git

# Install globally at user level (recommended)
claude plugins install --global ./superpowers
```

Jesse recommends installing globally at the user level rather than per-project. I agree — you want the methodology available everywhere, not just in specific repositories. The skills are general enough that they improve any coding workflow.

### Option 3: VS Code Terminal

If you're using Claude Code through the VS Code terminal (which I prefer for the visibility it gives into the workflow):

```bash
# Open VS Code terminal and run
claude plugin add obra/superpowers
```

Once installed, Superpowers runs automatically in the background. You don't need to explicitly invoke it. The master "Using Superpowers" skill intercepts your requests and routes them through the appropriate phases. If you want extra assurance, you can add "use any relevant superpower skills" to your prompts — but in my experience, the auto-detection is reliable enough that this is unnecessary.

One thing I appreciated: Superpowers doesn't fight with other skills or plugins you've already installed. I run it alongside my [custom SEO skills](/claude-seo-toolkit-guide) and several project-specific skills without conflicts. The orchestrator is smart enough to know when a different skill should handle the request.

If you'd rather have someone set up your entire Claude Code development environment — Superpowers, custom skills, project-specific configurations — I take on these kinds of engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

Now let's talk about the feature that surprised me most during testing.

## The Visual Companion: Why I Went From Skeptic to Convert

I dismissed the visual companion as a gimmick for the first two sessions. Interactive dashboards? Force graphs? Card grids? This felt like UI sugar on top of what should be a terminal-first workflow.

Then I hit a complex task — a multi-tenant SaaS feature with role-based access control, audit logging, and a custom permissions system. The kind of feature where you could interpret the requirements three different ways and each interpretation leads to a fundamentally different architecture.

Superpowers generated a visual companion showing three design approaches as interactive cards. Each card laid out the architecture visually — data models, API endpoints, component hierarchy, permission flow. I could see the trade-offs at a glance. Option A was simpler but wouldn't scale past 50 tenants. Option B handled scale but added significant database complexity. Option C split the difference with a caching layer.

Without the visual companion, here's what would have happened: Claude picks whichever interpretation it deems "most likely," codes the entire thing, and I discover the architectural mismatch 2,000 lines of code later. With the visual companion, I spotted the right approach in about 45 seconds and the agent built exactly what I needed on the first pass.

The clarifying questions work similarly. Superpowers doesn't just ask "do you want feature X?" It surfaces the specific ambiguities in your request that a senior developer would catch in a code review. During one session, it asked me whether my "user authentication" requirement meant session-based auth, JWT tokens, or OAuth2 — and then explained the implications of each for the rest of the architecture. I hadn't specified because I hadn't thought about it yet. That single question saved an entire implementation cycle.

This is the human-in-the-loop design I mentioned earlier. The framework is designed for these interaction moments. Skip them (like my automated test did) and you lose a significant chunk of the value.

## The 14 Skills: What Each One Actually Does

Most reviews list the skills without explaining when they fire or why they matter. Here's the breakdown based on what I observed across my testing:

### The Orchestrator

**Using Superpowers** — The master dispatcher. It reads every prompt you send and decides which skills to invoke. This runs automatically on every interaction. You never call it directly; it's the traffic controller.

### Design Phase Skills

**Brainstorming** — Generates design options with visual companions. Produces detailed checklists that the planning phase uses as input. This skill fires before any implementation work begins and is responsible for the interactive card grids and force graphs I described above.

### Planning Phase Skills

**Writing Plans** — Creates the hyper-detailed implementation plans. Each plan breaks the work into tasks estimated at 2-5 minutes, includes exact file paths and function signatures, specifies dependencies between tasks, and gets saved as a referenceable document. I've started using these saved plans as lightweight technical specifications for my projects.

**Executing Plans** — Takes a written plan and coordinates the execution. This is where the sub-agent architecture kicks in — the skill dispatches fresh Claude Code agents for each task in the plan, manages their outputs, and handles the integration of completed tasks.

### Execution Phase Skills

**Subagent-Driven Development** — Dispatches independent sub-agents for parallel task execution. If your plan has three tasks with no dependencies between them, this skill runs all three simultaneously. The speed improvement on complex projects is noticeable — wall-clock time drops substantially when independent tasks don't have to wait for each other.

**Dispatching Parallel Agents** — The coordination layer for parallel execution. Handles state management across concurrent sub-agents, ensures completed tasks don't conflict, and manages the merge of parallel outputs.

### Quality Gate Skills

**Test-Driven Development** — This skill enforces TDD methodology: write failing tests first, then write the minimal code to make them pass, then refactor. In my testing, this was the single biggest contributor to improved code quality scores. Without it, Claude Code writes implementation code first and tests second (if at all). With it, every feature starts with a clear definition of "done" expressed as executable tests.

**Systematic Debugging** — When something breaks, this skill enforces a four-phase debugging protocol: identify root cause, analyze related systems, generate hypotheses, and test the fix. It prevents the "shotgun debugging" pattern where the agent makes random changes hoping something sticks. I've seen this skill save entire sessions that would otherwise have spiraled into token-burning fix cycles.

**Verification Before Completion** — The final quality gate. Before Superpowers declares any work complete, this skill requires running the test suite, verifying all requirements from the clarify phase are met, and confirming the code compiles and runs. No more "I'm done" followed by immediate failures.

### Code Review Skills

**Requesting Code Review** — Triggers when implementation is complete. Runs a structured review checking correctness, style, performance, and security before the code gets committed.

**Receiving Code Review** — Handles feedback from code reviews with what Jesse calls "technical rigor, not performative agreement." The skill evaluates review comments critically rather than blindly implementing every suggestion. This prevents the pattern where review feedback makes code worse because the agent doesn't push back on bad suggestions.

### Git Workflow Skills

**Using Git Worktrees** — Creates isolated git worktrees for feature development. Keeps your main workspace clean while experiments run in separate branches. Smart directory selection and safety verification prevent the worktree sprawl that manual git worktree management often creates.

**Finishing a Development Branch** — Guides completion of development work by presenting structured options: merge to main, create a PR, or clean up the branch. This prevents the common failure of leaving half-finished branches littering your repository.

### The Meta Skill

**Writing Skills** — This is the one that gets AI framework enthusiasts excited. Superpowers can write new Superpowers skills using TDD principles. You describe the capability you want, and the framework creates a tested, verified skill that integrates with the rest of the system. The framework extends itself. I've used this to create project-specific skills that follow Superpowers' conventions and integrate with its orchestration layer.

## The Honest Assessment: Where Superpowers Falls Short

Every review I've read about Superpowers focuses on the wins. Here's what they don't tell you.

**Simple tasks get slower, not faster.** If you need a quick utility function, a one-off script, or a simple refactor, the clarify-design-plan overhead adds time without adding value. I've started prefixing simple requests with "quick task, skip planning:" and the orchestrator respects this most of the time. But out of the box, Superpowers doesn't distinguish between a 10-line fix and a 10,000-line feature. It applies the full methodology to both.

**Domain knowledge doesn't improve.** Superpowers makes Claude more disciplined, not more knowledgeable. If the model doesn't understand your specific framework, your business domain, or your proprietary APIs, Superpowers won't fix that. It'll just plan more carefully around the knowledge gaps — which is better than coding blindly, but the gaps still exist. You still need domain-specific context in your prompts or CLAUDE.md.

**Spec compliance is unchanged.** If your requirements are wrong or incomplete, Superpowers will faithfully plan and execute against wrong or incomplete requirements. The clarifying questions help — they catch some gaps — but they can't replace a well-written specification. I watched the framework build a perfectly planned, perfectly executed feature that was exactly what I asked for and entirely not what I actually needed. The methodology is only as good as the inputs it receives.

**Token spikes happen.** I found one session where Superpowers consumed tokens aggressively during the brainstorming phase, generating an elaborate design companion for a task that didn't warrant it. The GitHub issues confirm this isn't unique to my experience — issue #953 on the repo describes a similar pattern. It's rare, but it happens, and if you're running on a tight token budget, you should know about it.

**The learning curve is real for teams.** If you're a solo developer, Superpowers works out of the box. If you're trying to roll this out across a team, expect questions. The visual companions confuse developers who are used to terminal-only workflows. The clarifying questions frustrate developers who "just want it to code." The TDD enforcement annoys developers who don't write tests (and aren't ready to start). Adoption requires buy-in, not just installation.

These aren't dealbreakers. They're trade-offs. And knowing them upfront lets you decide whether the trade-off makes sense for your specific situation.

## When to Use Superpowers (And When to Skip It)

After a month of daily use, here's my decision framework:

**Use Superpowers when:**
- The task involves multiple files, services, or architectural decisions
- You're building a feature that needs to integrate with existing code
- The requirements are ambiguous or complex enough to be misinterpreted
- You care about test coverage and code structure, not just "does it run"
- You're estimating project costs and need predictable token consumption
- You're working on a codebase you'll maintain long-term

**Skip Superpowers when:**
- You need a quick one-off script or utility
- The task is a simple bug fix with an obvious solution
- You're exploring or prototyping and don't want the overhead of planning
- Token budget is extremely tight and the task is straightforward

The sweet spot — where Superpowers delivers the most value per token spent — is medium-to-complex tasks in active, maintained codebases. That's where the planning prevents expensive rework, the TDD catches regressions, and the sub-agent architecture speeds up parallel implementation.

## Superpowers vs. The Alternatives: Quick Comparison

Superpowers isn't the only structured framework for Claude Code. Two notable alternatives are GSD (Get Stuff Done) and gstack. Here's how they differ at a high level:

| Dimension | Superpowers | GSD | gstack |
|-----------|------------|-----|--------|
| Philosophy | Full software methodology | Rapid execution focus | Stack-aware development |
| Planning Overhead | High (clarify/design/plan) | Low (minimal planning) | Medium (context-aware) |
| TDD Enforcement | Built-in, mandatory | Optional | Not included |
| Sub-agent Support | Yes, with parallel execution | No | Limited |
| Best For | Complex, maintained projects | Quick tasks, prototypes | Stack-specific workflows |
| Token Profile | Higher upfront, lower total | Low upfront, variable total | Moderate throughout |

The choice isn't "which is best" — it's "which matches your task." I run Superpowers as my default and occasionally disable it for quick tasks where GSD's lightweight approach is more appropriate. They're complementary tools, not competitors.

## What This Means For How I Work Now

A month in, Superpowers has changed my Claude Code workflow in three specific ways.

First, I stopped treating Claude Code as a code generator and started treating it as a development partner. The clarify and design phases force a conversation that didn't exist before. My prompts have gotten shorter and more focused because I know the framework will ask the right follow-up questions. I'm not trying to front-load every requirement into a single prompt anymore.

Second, my project estimation accuracy improved. The predictable token usage means I can scope AI-assisted features with confidence. "This feature will take approximately X tokens to implement" is a statement I can now make and be right about within a reasonable margin. Before Superpowers, that estimate had error bars wide enough to drive a truck through.

Third — and this surprised me — I'm writing better specifications. The clarifying questions taught me what information the agent actually needs versus what I was including out of habit. My specs are shorter, more precise, and result in fewer iterations. The framework trained me as much as it trained the agent.

Is Superpowers the right tool for everyone? No. If you're doing simple tasks, prototyping, or working in a domain where planning overhead doesn't pay off, it'll slow you down. But if you're building real software — features that need to work, integrate, and be maintained — the five-phase discipline isn't overhead. It's how professional software gets built. The framework just enforces what good engineers already do and gives it to an AI that desperately needed the structure.

The 121,000 GitHub stars aren't hype. They're 121,000 developers who hit the same wall I did — brilliant AI, zero discipline — and found the same answer.

Install it. Run it on your next complex task. Then decide for yourself. That's what I did, and the experiment spoke louder than any review.

## Frequently Asked Questions

### Does Superpowers work with all Claude Code models?

Superpowers works with any model available through Claude Code, including Opus 4.6 and Sonnet 4.6. The skills are model-agnostic — they modify the workflow, not the underlying model capabilities. Performance improvements scale with model capability, so Opus sessions typically show larger quality gains than Sonnet sessions.

### How do I disable Superpowers for simple tasks?

Prefix your prompt with "quick task" or "skip planning" and the orchestrator typically bypasses the full clarify-design-plan cycle. You can also temporarily disable the plugin with `/plugin disable superpowers` and re-enable it with `/plugin enable superpowers`. For more granular control, individual skills can be toggled in the plugin settings.

### Does Superpowers conflict with other Claude Code plugins or skills?

In my testing across four months of daily use, Superpowers coexists cleanly with other plugins and custom skills. The orchestrator is designed to recognize when another skill should handle a request and steps aside accordingly. If you experience conflicts, check that your other skills don't define overlapping trigger conditions with Superpowers' core phases.

### Is Superpowers free?

Yes. Superpowers is fully open source under the MIT license, hosted at [github.com/obra/superpowers](https://github.com/obra/superpowers). There are no paid tiers, no premium features, no usage limits. The entire framework — all 14 skills — is free to install, use, and modify.

### How much does Superpowers reduce token costs?

Based on my 12-session experiment: approximately 9% cost reduction and 14% token reduction on average, with savings concentrated on medium and complex tasks. Simple tasks may actually use more tokens due to planning overhead. The more valuable metric is consistency — token usage variance dropped 2-3x, making project cost estimation far more reliable.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
