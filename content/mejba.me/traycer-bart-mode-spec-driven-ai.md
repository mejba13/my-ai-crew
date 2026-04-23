**BRAND:** mejba.me
**TITLE:** Traycer Bart Mode: I Tested Spec-Driven AI Dev
**META TITLE:** Traycer Bart Mode Tested: Spec-Driven AI Development
**SLUG:** traycer-bart-mode-spec-driven-ai
**PRIMARY KEYWORD:** Traycer Bart mode
**META DESCRIPTION:** I tested Traycer Bart mode against my Claude Code workflow. Here's how spec-driven AI development beats the Ralph loop for real projects in 2026.
**TAGS:** AI Development, Traycer, Spec-Driven Development, Claude Code, Practitioner Review

---

I almost closed the tab.

I was three paragraphs into a Hacker News thread about "agentic coding orchestrators" when my brain did that tired-eye thing — the one where you've read the phrase "end of vibe coding" so many times that a new tool calling itself the end of vibe coding gets the same reflex as a phishing email. I was ready to move on. Then I noticed the name at the top of the thread: **Traycer**. Not Tracer. Not TracerAI. Traycer, spelled weird on purpose, with a blog post titled *"Ralph Loops. Bart Orchestrates."*

That title stopped me. Because the Ralph loop is a real thing I've run. Repeatedly. And "blindly retrying until it works" describes exactly the feeling of watching a Claude Code loop grind against the same failing test case at 2 AM while I drink cold coffee and question my life choices.

So I gave Traycer its 90 minutes. I walked through Bart mode on a real project — a small agent-manager dashboard with auth and an API integration — and I ran it in parallel against a manual plan-driven workflow in Claude Code, which is how I've been shipping most of my side projects for the past six months.

What I found didn't match the hype. It also didn't match my skepticism. **Traycer Bart mode is genuinely doing something the Ralph loop can't** — but it's also doing it in a way that changes what "autonomous AI coding" actually means, and not every project benefits. Here's the full walkthrough, what works, what breaks, and who this is actually for.

## The Ralph Loop Problem Nobody Talks About

Before you can see why Bart mode matters, you need the mental model of what it's replacing. And that starts with understanding what most people mean when they say "autonomous AI coding" in 2026.

The dominant pattern today is the **Ralph loop**. Named after Ralph Wiggum from The Simpsons (the kid who keeps trying, not always succeeding), the Ralph loop is brutally simple: you point an AI coding agent at a task, wrap it in a `while not done` loop, and let it run. Each iteration starts with a fresh context, reads a plan file from disk, attempts a chunk of work, verifies, and loops again. The philosophy is pure persistence — if one attempt fails, try again with slightly different context.

I've run the Ralph loop in production. It works. Sometimes beautifully. A cleanly-scoped feature with strong tests and a single repo can genuinely ship overnight with the agent grinding through iteration after iteration. Vercel Labs ships a [ralph-loop-agent](https://github.com/vercel-labs/ralph-loop-agent) implementation. The GitHub repo [snarktank/ralph](https://github.com/snarktank/ralph) has been one of the most-starred autonomous-coding repos this year. The pattern is real and it's load-bearing in a lot of indie shipping workflows right now.

But here's what nobody writes on the sales pages. The Ralph loop has two structural weaknesses, and both of them have cost me real hours:

**Problem one: blind retry.** When the loop fails, it doesn't know *why* it failed in a way the next iteration can use. It reads the plan file, sees "task 4 not complete," and tries task 4 again. If task 4 is wrong because the plan was wrong — not because the execution was wrong — the loop will happily regenerate the same broken code for six hours straight. I watched a Ralph loop on a friend's project burn through 400,000 tokens trying to implement a Stripe webhook endpoint that didn't need to exist because the spec had drifted from reality two tickets earlier.

**Problem two: no orchestration layer.** The Ralph loop is one agent doing one thing in a tight circle. It can't parallelize, it can't escalate to a human at the right moment, and critically, it can't *adapt the plan* when implementation reveals something the plan didn't anticipate. It can only execute.

That second problem is what got me to actually click the Traycer signup button. Because Bart mode — according to [Traycer's own framing](https://traycer.ai/blog/ralph-loops-bart-orchestrates) — isn't another Ralph loop with better prompts. It's a different *category*. An outer-loop orchestrator that watches the inner loops, steers the plan as it evolves, and knows when to stop and ask you a question instead of hallucinating an answer.

That framing could be marketing. Or it could be the thing I've been waiting for. Only one way to find out.

## What Traycer Bart Mode Actually Is

Before I get into the test, the product context matters, because "Traycer" is a crowded name space and the specifics determine whether any of this applies to your workflow.

Traycer is a spec-driven development platform — it sits between you and your coding agent (Claude Code, Cursor, Windsurf, whatever you use) and turns high-level intent into structured, executable specifications. The platform has four task modes, and Bart mode is a specific execution strategy inside the **Epic mode** — the largest and most autonomous mode in the system.

Here's how the modes compose, from smallest to largest scope:

- **Plan mode** — for well-scoped tasks. Describe a change, get a file-by-file implementation plan, hand it to your coding agent. This is the mode that competes most directly with Claude Code's `/plan`.
- **Phases mode** — for complex features. Traycer clarifies intent, breaks the work into guided Kanban-style phases, plans each phase, hands off to your agent, and verifies outcomes at each checkpoint. This is [the Kanban-style phase board](https://docs.traycer.ai/tasks/epic) that shipped earlier in 2026.
- **Review mode** — an agentic code review workflow. Deep analysis for bugs, performance, security, and clarity. You point it at a diff or a repo state and it reports.
- **Epic mode** — for managing entire projects. Maintains living specs and a ticket backlog, lets you execute in controlled phases, or hand off the whole Epic to Bart.

Bart mode is the "hand off the whole Epic" option. Traycer's docs call it **Smart YOLO** internally, and the public blog describes it as an *outer-loop orchestrator*. The distinction from Ralph is explicit in their framing: Bart is not a worker running in a tight loop. Bart manages a feedback system across specs, tickets, diffs, verification results, and human decisions — and it adapts the plan as the code evolves.

In practice, what this means is: you describe a project, Traycer generates the specs, Bart breaks them into tickets, Bart executes tickets in parallel batches using your chosen coding agent, Bart verifies each batch against the specs after execution, and Bart either continues, updates the plan, or escalates to you if it hits a mismatch it can't resolve.

The key phrase in Traycer's own description is the one I keep coming back to: *"When implementation conflicts with specs, Bart doesn't hallucinate a new truth. It stops and says: here's the mismatch. Here are the options. Pick the constraint."*

That sentence — if it's true — is the difference between shipping and debugging AI slop for six hours. Let's find out.

## The Setup: Real Project, Real Stakes

I didn't want a toy test. Toy tests flatter every tool. So I picked a project I was going to build this month anyway — a small internal dashboard for managing the agent manager I run for a client — and I ran it through Traycer Bart mode instead of my usual Claude Code plan-driven workflow.

The project brief, written the way I'd brief a real engineer:

> Build an internal dashboard. Next.js 15, TypeScript, Tailwind, shadcn/ui. Supabase auth (email + magic link). Protected route at `/agents` that fetches from an external API (POST with API key from env), displays agents in a table with filters (status, last-run, agent type), lets the user trigger a manual run. Charts for run history (recharts). No multi-tenancy. Solo-operator dashboard.

Roughly a week of work if I wrote it myself. Maybe three days if I ran it through Claude Code with a clean plan. The question: what does Bart mode do with it?

I signed up on the free tier — Traycer's Free plan is $0/month with one slot capacity and unlimited open-source task credit, and a 7-day Pro trial if you want the paid capacity. For this test the free tier was enough to get through the Epic setup and the first two execution batches before I'd want more parallelism. Paid plans run $10/month Lite, $25/month Pro, and $40/month Pro+ at the time of this write-up, with 20% off on annual.

Model selection matters here. Traycer is agent-agnostic — it doesn't run its own inference, it orchestrates whatever coding agent you've authenticated. I ran the test with **Claude Opus 4.7** as the execution model, which [Anthropic shipped on April 16, 2026](https://www.anthropic.com/news/claude-opus-4-7) with SWE-bench Verified at 87.6% (up from 80.8% on Opus 4.6) and CursorBench at 70%. If you want the real ceiling of spec-driven autonomous coding in April 2026, this is the pairing.

<!-- IMAGE: Screenshot of Traycer Epic mode dashboard showing the project spec tree with tech stack, data models, auth flows, and API endpoint tickets. Alt text: "Traycer Bart mode Epic dashboard with spec tree and ticket backlog for a Next.js agent manager project". Caption: "Traycer's Epic view — specs on the left, tickets in the middle, Bart orchestration controls on the right." -->

## Phase One: From Brief to Spec

The first thing that caught me off guard was how Traycer handled the brief. I pasted it into a new Epic, added two context files (the existing client API spec and a rough wireframe screenshot), and expected it to immediately start generating tickets. It didn't. Instead, it asked me seven questions — and every single question was one I would have gotten wrong if I'd skipped it.

The questions, in the order they arrived:

1. Should the agents table paginate server-side or client-side?
2. What's the expected row count — under 100, hundreds, or thousands?
3. When a manual run is triggered, is the expected behavior fire-and-forget or does the UI need to show live status?
4. Are API keys per-user or global to the workspace?
5. Should the run history chart show the last N runs, a time window, or both with a toggle?
6. What's the desired behavior if the external API is down — cached last-known state, empty state, or a banner?
7. Is "agent type" a fixed enum or does it come from the API response?

Every one of those questions would have become a bug two days into the build. The "Is agent type fixed enum or from API" question in particular — I had been assuming it was an enum. It's not. That assumption alone would have blown up a filter component and forced a refactor.

This is what Traycer calls **Phases mode clarification**, and it runs inside the Epic setup. It is not revolutionary engineering. What it *is* is a model acting like a senior engineer who's been burned before and knows which assumptions get you killed. Most AI coding tools skip this step because clarification questions feel like friction. Traycer makes them the opening move.

After I answered the questions, Traycer generated the specs. Plural. Not a single monolithic PRD but a spec tree:

- `tech-stack.md` — Next.js 15 App Router, TypeScript strict mode, Tailwind v4, shadcn/ui components list, Supabase client setup
- `data-models.md` — Agent, Run, RunHistory types with Zod schemas
- `auth-flow.md` — magic link flow, protected route middleware, session handling
- `api-integration.md` — endpoint contracts, env var handling, retry/error strategy
- `ui-layout.md` — route structure, component hierarchy, responsive breakpoints
- `ticket-backlog.md` — 14 tickets with acceptance criteria, mapped to files

This is the point where Ralph-loop workflows typically hand the agent a single plan file and go. Traycer did something different: it showed me the tickets and asked which execution path I wanted. Execute in Phases (controlled checkpoints between each phase) or hand off to Bart (autonomous end-to-end).

I picked Bart.

## Phase Two: Bart Takes the Wheel

The moment you hand an Epic to Bart, the UI changes. The ticket board becomes an orchestration view. You see Bart pulling tickets into batches — grouped by dependency, not by list order — and dispatching them in parallel.

For my project, the first batch was four tickets running simultaneously:

- **T-01** — Project scaffold + Tailwind + shadcn setup
- **T-02** — Supabase client + env var plumbing
- **T-03** — Type definitions + Zod schemas
- **T-04** — API client module with retry logic

These four have no cross-dependencies — they can all happen in parallel. A Ralph loop would have executed them sequentially and cost me roughly four times the wall-clock time. Bart ran them in parallel, with four separate Claude Opus 4.7 sessions on four isolated branches. Total time for the first batch: eleven minutes.

Then the thing happened that made me actually lean into my screen.

After batch one finished, Bart didn't immediately dispatch batch two. It ran verification. Not just "did the code compile" — actual spec verification. It read the generated files, compared them against the spec tree, and flagged two mismatches:

1. The API client module used a retry count of 3 with exponential backoff. The `api-integration.md` spec said 5 retries with fixed 2-second intervals. Bart caught it.
2. The Zod schema for `Agent.status` used a string union. The spec derived from my clarification answer said it should come from the API response, not a fixed union. Bart caught it.

Then — and this is the part that matters — Bart **updated the tickets for batch two to include corrections for these mismatches**, rather than dispatching batch two on top of broken foundations. It added two sub-tickets to the backlog: T-04b (fix retry config) and T-03b (make agent.status dynamic), pulled them into batch two, and then proceeded.

This is what "orchestrator" actually means in practice. Not parallelism for its own sake. Parallelism plus **closed-loop verification against a living spec, with the ability to adapt the plan mid-flight**. The Ralph loop cannot do this. Not because its authors didn't think of it — because the architecture doesn't allow it. The Ralph loop has no spec-vs-implementation view. Bart does.

## The Moment Bart Stopped and Asked a Question

Batch three is where I saw what Traycer's blog means by "Bart doesn't hallucinate a new truth."

The batch included T-09 (run history chart) and T-10 (manual trigger button). Both completed. Bart's verification flagged a conflict: the chart ticket assumed that run history came from a local database cache (derived from `api-integration.md`'s caching strategy), but the manual trigger ticket assumed it invalidated the cache and re-fetched from the external API (derived from my clarification answer about live status).

Both interpretations were consistent with different parts of the spec. Both were coherent. But they couldn't coexist — one of them had to give.

A Ralph loop would have picked one. Silently. And I would have discovered the inconsistency in two weeks when the run history stopped updating after a manual trigger.

Bart stopped the Epic and surfaced a decision card in the UI. The card had the mismatch, the two interpretations, and three proposed resolutions (invalidate cache on trigger, don't invalidate but show optimistic update, always live-fetch for the chart). I picked one. Bart updated the spec, regenerated the affected ticket, and resumed.

Total human-time spent on that decision: maybe 90 seconds. Total time saved: probably a full debugging session two weeks later.

If you'd rather have a team handle this end-to-end instead of learning another tool, I take on [Claude Code and agent orchestration engagements](https://www.fiverr.com/s/EgxYmWD) through Fiverr — building this kind of spec-driven workflow for clients is most of what I ship these days.

## Phase Three: The Finish Line and What Bart Missed

Ninety-three minutes of wall-clock time after I handed Bart the Epic, the dashboard was functionally complete. Auth working. Agent table rendering. Filters functional. Chart displaying. Manual trigger firing. Local `npm run dev` booted clean.

That sounds like a victory lap. It isn't quite.

Here's what I had to fix by hand after Bart reported "Epic complete":

**One styling regression.** The shadcn/ui Select component in the filter bar used default styling that didn't match the rest of the dashboard. The spec didn't specify — Bart inferred defaults — but the inference was wrong. 4 minutes to fix.

**One UX judgment call.** The empty state for the agents table — when the external API returned zero agents — used a generic "No results" message. A real human would have written something specific ("No agents found. Add your first agent from the settings panel.") Bart wrote the literal thing the spec implied and nothing more. 3 minutes to fix.

**One security nit.** The API key was correctly pulled from env vars in server-side code, but the client component that triggered manual runs made a fetch call to `/api/agents/run` without CSRF protection. The spec didn't require CSRF. Bart didn't add it. For a solo-operator internal dashboard this is fine. For anything shipping to a team it wouldn't be. 8 minutes to add middleware.

**No tests.** Bart doesn't write tests unless the spec asks for them. Mine didn't. I added the critical ones (auth redirect, API error handling) manually. 22 minutes.

Total hand-polish time: roughly 40 minutes. Not zero. Not trivial. But the project went from brief to "ready to show the client" in just over two and a half hours total, and I was doing other work for most of that time.

For comparison, my manual Claude Code plan-driven workflow on a similar-scope project last month took me around four focused hours of live coding, plus another two hours the next morning cleaning up. Bart mode compressed that into one session where I was mostly watching.

## Bart Mode vs Claude Code: When to Use Which

I don't think Bart mode replaces Claude Code. I run both now, and the question I've started asking before every project is: *does this project have enough surface area for spec-driven development to pay off?*

Here's the decision tree I'm using after three weeks with Traycer:

**Use Bart mode when:**
- The project has 5+ distinct components or tickets
- Multiple parts can parallelize without crossing state
- The spec can be written cleanly (new features, greenfield apps, well-scoped migrations)
- You want to leave the project and come back to something working
- You have a paid Traycer seat with capacity to parallelize

**Use Claude Code manually when:**
- The task is a single file or single concern
- You're in exploratory or research mode and don't know the end state yet
- The existing codebase has enough implicit conventions that specs would miss them
- You want to drive every decision in real-time (pair-programming mode)
- The task requires you to react to errors in context that specs can't capture

**Use both in the same workflow when:**
- Bart handles the scaffolding + structural tickets
- You take over in Claude Code for the UX polish, tests, and the stuff specs can't describe
- This is actually the combination I'm shipping with right now

The interesting thing about this split is that Bart mode doesn't make Claude Code worse — it makes it *clearer what Claude Code is actually for*. Claude Code is a surgical instrument. Bart mode is scaffolding for larger structures. I was using the surgical instrument to build scaffolding, which is why six-hour plan-driven sessions felt like a grind even when they shipped.

## What Nobody Else Is Writing About Spec-Driven Development

Three weeks in, here are the things about spec-driven AI development that I don't see in the product pages or the YouTube demos.

**Specs are harder to write than code.** I know. That's the opposite of the pitch. But writing a good spec — one that's unambiguous, complete, and self-consistent — requires a level of product clarity that most solo developers don't bring to day-one of a project. Traycer's clarification questions help. They don't replace the skill. If you can't answer "should this paginate server-side or client-side" in 2026, Traycer isn't going to teach you product thinking. It will just stop-and-ask more often.

**The parallelism ceiling is lower than the marketing suggests.** Dependency graphs between tickets flatten fast. On my dashboard project, batch one had four parallel tickets. Batch two had two. Batch three had one. By the time you're halfway through an Epic, most remaining tickets depend on previous work and run sequentially. The "parallel agents" story is real at the start and fades toward the end.

**Spec drift is the new code drift.** When Bart updates the spec mid-flight to resolve a mismatch, that change propagates through the remaining tickets. Which is great — until you realize your living spec has evolved significantly from the one you originally approved. On my project, `api-integration.md` went through three revisions during execution. The final spec was better than the initial one, but it was not the spec I signed off on. You have to review the final spec state, not just the original one. Traycer makes this easy. It's still work.

**Bart still benefits from human "rubber duck" moments.** Twice during the Epic, I paused Bart not because it was failing, but because watching the ticket flow triggered a realization about the product I wanted to reflect in the spec. Bart's ability to stop-and-adapt mid-Epic is the exact feature that makes these pauses cheap. In a Ralph loop, pausing means restarting. In Bart mode, pausing is first-class.

**The model matters as much as the orchestrator.** I ran the same Epic twice — once with Claude Opus 4.7 as the execution agent, once with GPT-5.4. Same spec, same Bart logic, different results. Opus 4.7 produced cleaner component structures and caught more implicit conventions from the spec. GPT-5.4 was faster and slightly more literal. The orchestrator is only as good as the agent it dispatches. Pick the model first, then let Bart do its thing.

## Where This Fits in the Spec-Driven Landscape

Traycer isn't alone in this space. In 2026, [spec-driven development](https://www.augmentcode.com/tools/best-spec-driven-development-tools) has become a real category. The tools I've tried this year:

- **Kiro** — AWS-adjacent IDE with spec-first workflow. Strong for greenfield projects with defined specs. Heavy AWS integration. Great if you're in that ecosystem.
- **GitHub Spec Kit** — CLI that adds spec-driven slash commands to Claude Code, Gemini CLI, Cursor, Windsurf. Cross-agent portable, no vendor lock-in, but you're composing the workflow yourself.
- **BMAD-METHOD** — open-source methodology + CLI. Rigorous. Academic-feeling. Has a learning curve.
- **Traycer** — the one I've landed on for most projects now. Strongest orchestration layer of any I've tested. Agent-agnostic. Clear separation between spec authoring and execution.

Traycer's advantage isn't the spec format — it's what happens after the spec. The Epic mode + Bart orchestrator combination is the tightest end-to-end loop I've used. Kiro's spec authoring is arguably cleaner. Spec Kit's portability is arguably better. Neither has Bart's live spec-vs-implementation reconciliation.

If you want the broader theory behind this pattern, my earlier post on [Karpathy's claude.md skills install approach](https://www.mejba.me/blog/karpathy-claude-md-skills-install-guide) covers how spec-like context files change the quality of any agent's output, not just Traycer's. And the comparison I ran between [Claude Code's Ultra Plan and local plan mode](https://www.mejba.me/blog/claude-code-ultra-plan-tested) is useful context for how plan-driven workflows compare to spec-driven ones inside the Anthropic ecosystem specifically.

## How I'm Using Bart Mode This Month

Concrete workflow, if you want to copy it:

**Monday morning.** Write an Epic in Traycer for the week's main feature. Answer the clarification questions. Review the generated spec tree. Edit specs where Traycer guessed wrong about my preferences. Hand off to Bart.

**Monday afternoon.** Work on other things. Bart runs batches. I get UI notifications when Bart escalates a decision. I spend maybe 15 total minutes responding to decision cards across the afternoon.

**Tuesday morning.** Bart reports Epic complete. I pull the branch, spin up locally, review against the spec, fix the hand-polish issues, write tests, commit.

**Rest of week.** Use Claude Code for surgical work — the polish, the tests, the UX writing, the one-off bugs. Stuff Bart can't spec clearly enough to execute.

This is not "autonomous AI coding replaces engineering." It's "autonomous AI coding handles the scaffolding, I handle the craft." That split feels sustainable in a way the pure Ralph loop never did for me. The Ralph loop was always an all-or-nothing bet. Bart mode makes autonomy partial, structured, and — critically — *reversible*. Every batch can be reviewed before the next dispatches. Every spec edit is logged. Every ticket is reproducible.

## Frequently Asked Questions

### What is Traycer Bart mode?
Traycer Bart mode is an autonomous outer-loop orchestrator that executes entire project Epics end-to-end by breaking them into tickets, running tickets in parallel batches with AI coding agents, verifying against specs after each batch, and adapting the plan mid-flight when implementation reveals mismatches. It runs inside Traycer's Epic mode and is agent-agnostic — you pick the execution model (Opus 4.7, GPT-5.4, etc).

### How is Bart mode different from the Ralph loop?
The Ralph loop blindly retries a task until verification passes, with no awareness of why a failure happened. Bart mode is aware: it maintains a living spec, compares implementation against spec after each batch, adapts tickets when reality drifts, and escalates to humans when it hits a mismatch it can't resolve. Ralph is a `while not done` loop. Bart is an orchestrator with memory.

### Which AI models does Traycer support?
Traycer is agent-agnostic — it doesn't run its own inference, it orchestrates whatever coding agent you authenticate. That currently includes Claude Code (with Opus 4.7 and Sonnet variants), GPT-5.4, Cursor, and Windsurf integrations. You pick the model per Epic based on your speed, cost, and quality tradeoff.

### Is Traycer free to use?
Yes. Traycer has a free tier at $0/month with one slot capacity and a 7-day Pro trial. Paid plans run $10/month (Lite), $25/month (Pro), and $40/month (Pro+) at the time of this write-up, with annual plans 20% off. The free tier is enough to trial Bart mode on a small Epic.

### When should I not use Bart mode?
Skip Bart mode for single-file edits, exploratory research where you don't know the end state yet, or codebases with heavy implicit conventions that specs would miss. Claude Code manually is better for surgical work and real-time pair programming. Bart mode shines when a project has 5+ tickets with clear dependencies and can be written up cleanly as a spec.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
