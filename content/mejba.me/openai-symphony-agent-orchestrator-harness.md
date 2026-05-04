**BRAND:** mejba.me
**TITLE:** OpenAI Symphony: The Coding Agent Orchestrator Tested
**META TITLE:** OpenAI Symphony Agent Orchestrator: Hands-On Review
**SLUG:** openai-symphony-agent-orchestrator-harness
**PRIMARY KEYWORD:** OpenAI Symphony agent orchestrator
**META DESCRIPTION:** I tested OpenAI Symphony agent orchestrator on Linear. Here's how the spec, Ralph loops, and harness engineering actually scale Codex and Claude Code.
**TAGS:** AI Agents, Claude Code, OpenAI Codex, Agent Orchestration, Harness Engineering

---

The first time Symphony picked up one of my Linear tickets and shipped a working pull request without me touching a keyboard, I sat there for a full minute trying to figure out what I was supposed to do next.

My ticket said: *"Add rate limiting middleware to the public API endpoints, default 60 req/min, allow per-route overrides."* I had moved it into "In Progress" out of habit, fired up the Symphony devbox, and switched tabs to answer a Slack message. Eleven minutes later a draft PR existed. The agent had spun up an isolated workspace, read my codebase, written the middleware, added route-level overrides, written three tests, and pushed a branch. The diff wasn't perfect — I rejected one test and tightened a comment — but the work was real. The work was *done*.

That ticket was the moment the **OpenAI Symphony agent orchestrator** stopped being a demo to me and started being a tool. It also forced me to confront something uncomfortable: the way I had been using Codex and Claude Code for the last year — one terminal, one prompt at a time, one human babysitting one agent — was about to look as outdated as SSH-ing into a single server to deploy a web app in 2014.

This is the post I wish someone had handed me before I spent two weekends rewiring how I think about agent infrastructure. I'll show you what Symphony actually is (it's smaller and stranger than the press releases imply), how it fits into the broader pattern people are calling "harness engineering," what I broke when I tried to bend it to my own stack, and the one mental shift that made everything click.

A heads-up before we go any further: there is a number being passed around — OpenAI's claim that some internal teams saw a **500% increase in landed pull requests** within three weeks of adopting Symphony ([OpenAI](https://openai.com/index/open-source-codex-orchestration-symphony/)). I'll come back to that number later in this post, because the way you read it determines whether you build the right thing or the wrong thing.

## What OpenAI Symphony Actually Is (And What It Isn't)

Let me kill a misconception up front: **Symphony is not a product**. It's not a SaaS. It's not a closed binary. There's no dashboard you log into.

Symphony is a `SPEC.md` file. That's the entire core artifact. OpenAI open-sourced it under Apache 2.0 on March 5, 2026, and as of late April 2026 the [openai/symphony GitHub repo](https://github.com/openai/symphony) had crossed **15,000 stars** ([Help Net Security](https://www.helpnetsecurity.com/2026/04/28/openai-symphony-codex-orchestration-linear/)). The spec describes — in plain English, with state diagrams — how a long-running orchestrator should turn an issue tracker into a control plane for autonomous coding agents.

The reference implementation ships in [Elixir](https://github.com/openai/symphony/blob/main/elixir/README.md). Yes, Elixir. The same language Discord and WhatsApp use under the hood. OpenAI picked it because BEAM (Elixir's runtime) gives you supervisor trees, lightweight processes, and crash-recovery semantics for free — exactly what you want when you're running ten or twenty coding agents that may each take twenty minutes per task and any one of which might silently die.

But here's the part that surprised me. The OpenAI team had Codex generate the Elixir implementation **in one shot from the spec**, and then asked Codex to re-implement the same spec in TypeScript, Go, Rust, Java, and Python — using each implementation as a forcing function to find ambiguities in the spec itself ([InfoWorld](https://www.infoworld.com/article/4164173/openais-symphony-spec-pushes-coding-agents-from-prompts-to-orchestration.html)). The spec *is* the product. The Elixir code is just the most polished example.

That matters because it tells you what kind of tool you're really evaluating. Symphony isn't "OpenAI's orchestrator framework." It's a **shared vocabulary** for what good orchestration of coding agents looks like, with a working reference you can either run as-is or steal from.

### The Core Loop, In One Paragraph

Symphony polls Linear every 30 seconds. When it sees a ticket move into a configured "ready" state, it claims that ticket, spins up an isolated workspace (a fresh git worktree on a devbox), boots a Codex agent in that workspace with a structured prompt that includes the ticket body, lets the agent run continuously until it produces a PR or fails, and then either marks the ticket "ready for review" or "blocked" with a reason. Default concurrency is 10 agents. State is in-memory in a GenServer; on restart it just rebuilds from Linear, so there's no database to operate ([allthings.how](https://allthings.how/symphony-openais-open-source-orchestrator-that-turns-linear-into-a-codex-command-center/)).

That's it. That's the whole thing. Read that paragraph again, because the simplicity is the point.

## Why the Hype Caught Me Off Guard

I'll be honest. When the OpenAI blog post landed in early March, I half-skimmed it and moved on. "Another agent orchestrator" was the cynical thought. I had already played with [Steve Yegge's Gas Town](https://github.com/steveyegge/gastown), I had been running [Archon](https://github.com/coleam00/archon) on a side project for three weeks, and I'd built my own [long-running Claude Code harness](/anthropic-long-running-agent-harness/) for a client. None of those tools needed a fourth competitor.

What I missed — what most coverage missed — is that Symphony isn't really *competing* with those tools. It's competing with the version of you that opens a terminal, types `codex` or `claude`, and watches one agent work on one task. **It's competing with manual supervision itself.** Once I understood that, my entire week changed.

To explain why, I need to take a detour into the term that's quietly become the most important concept in AI-assisted software delivery this year: harness engineering.

## Harness Engineering: The Vocabulary That Made Everything Click

In April 2026, [Birgitta Böckeler](https://birgitta.info/) — Thoughtworks' Global Lead for AI-Assisted Software Delivery — published a long, careful piece on [martinfowler.com titled "Harness engineering for coding agent users"](https://martinfowler.com/articles/harness-engineering.html). It's the article that gave us the canonical taxonomy. (The video summary I worked from phonetically rendered her name as "Vetta Berkeler" — same person, same framework.)

The framing is deceptively simple:

> **Agent = Model + Harness**

The model is the LLM. Everything else — the prompts, the tools, the sandbox, the loop, the validators, the orchestrator — is the harness. And once you start naming the parts of the harness, you can start engineering them deliberately instead of accidentally.

Böckeler splits the harness into two layers, and Symphony lives squarely in one of them.

### Inner Harness — What's Inside the Agent

The **inner harness** is the code and capabilities that ship inside the agent itself. When you run Claude Code, you get the inner harness for free: the tool-calling protocol, the file-reading and shell-executing tools, the planning prompts, the sub-agent spawning, the permission gates, the hooks system, the skills you can install. Same with Codex. Same with Cursor.

You don't usually write the inner harness. You configure it. You enable hooks. You install skills. You write a `CLAUDE.md` or `AGENTS.md`. You set permissions in `settings.json`. The inner harness is what makes a model into a coding agent at all.

### Outer Harness — What Surrounds the Agent

The **outer harness** is everything *outside* the agent that controls its lifecycle, manages its context, decides when it runs, what it runs on, what counts as "done," and what happens when it fails. This is where Symphony lives. This is where Gas Town, Archon, and Ralph loops live too.

The outer harness is what turns one terminal session of Claude Code into a fleet of twenty parallel agents that you actually trust.

Here's the mental picture I draw on a whiteboard when I explain this to clients:

```
┌──────────────────────────────────────────┐
│  OUTER HARNESS                           │
│  Symphony / Gas Town / Archon / Ralph    │
│  - lifecycle, queues, isolation, retries │
│  ┌──────────────────────────────────┐    │
│  │  INNER HARNESS                   │    │
│  │  Claude Code / Codex / Cursor    │    │
│  │  - tools, hooks, skills, perms   │    │
│  │  ┌────────────────────────┐      │    │
│  │  │      MODEL             │      │    │
│  │  │   GPT-5.x / Sonnet /   │      │    │
│  │  │   Opus / etc.          │      │    │
│  │  └────────────────────────┘      │    │
│  └──────────────────────────────────┘    │
└──────────────────────────────────────────┘
```

Once you see the layers, every "AI agent product" snaps into a position on this picture. And once that happens, the *real* question stops being "which agent should I use?" and becomes "what does my outer harness need to do that nobody else's can?"

That's the question Symphony forces you to answer.

## Guides And Sensors — The Two Levers In Every Harness

Inside both layers, Böckeler introduces another distinction that I now think about every time I write an agent prompt or set up a CI step. Every meaningful piece of a harness is doing one of two jobs.

**Guides** steer the agent *before* it acts. They're feedforward control. Your `CLAUDE.md`. The skills you install. The playbook you paste into the prompt. The example commits you reference. The architecture decision records the agent reads before writing code. Guides increase the probability the agent gets it right on the first attempt.

**Sensors** observe the agent *after* it acts and decide whether what it produced is acceptable. They're feedback control. Sensors come in two flavors:

- **Computational sensors** — deterministic, fast, cheap. Linters. Type checkers. Unit tests. Schema validators. Build outputs. They run in milliseconds to seconds and give you a binary answer ([Martin Fowler](https://martinfowler.com/articles/harness-engineering.html)).
- **Inferential sensors** — non-deterministic, slow, expensive. LLM-as-judge code reviews. Semantic similarity checks. Style critiques. They run on a GPU, take seconds to minutes, and give you probabilistic answers.

Here's the part that took me embarrassingly long to internalize: **most teams I've worked with under-use computational sensors and over-rely on inferential ones.** They'll set up an "AI reviewer" before they've configured `eslint --max-warnings 0` in a pre-commit hook. They'll have an LLM-as-judge grade test coverage before they've added a coverage threshold to CI.

Computational sensors are basically free. They're proven. They've been running in CI pipelines for fifteen years. The reason they get skipped in agent workflows is that practitioners forget the agent can read their output and self-correct. The moment you wire `npm test` into the agent's loop and feed failures back as context, your error rate drops by a factor I genuinely cannot estimate without lying. It's a lot.

I'll come back to this when I show you what a real Symphony workflow file looks like, because guides-and-sensors is the entire vocabulary you use to author one. For now, sit with this: **a harness is a carefully chosen set of guides and sensors arranged around a model.** The orchestrator is just the thing that runs the harness on a queue.

## Where Symphony Fits In The Outer Harness Family

Symphony isn't the only outer harness in the wild, and it isn't always the right answer. Let me walk through the four I've used in production work this year, because the trade-offs matter.

### 1. Symphony — Issue-Tracker-Native Orchestration

Symphony's defining choice is that **the issue tracker is the queue**. Linear tickets *are* the work units. There is no separate Symphony UI to manage. You move a ticket into "Ready for Codex," Symphony picks it up, an agent runs, a PR appears linked to the ticket, and you review the ticket as you always did. The cognitive overhead of adopting it is roughly zero, because you're not learning a new project management tool.

The trade-off is that Symphony's surface area is small. It does one thing — turn tickets into runs — and it does it well. If your work doesn't fit into discrete tickets (long-running research, multi-week refactors, exploratory spikes), Symphony isn't your tool.

### 2. Gas Town — Multi-Agent Colonies, Steve Yegge Style

[Gas Town](https://github.com/steveyegge/gastown) is Steve Yegge's framework for running 20-30 parallel Claude Code agents organized into roles (Mayors orchestrate, Polecats execute), with state persisted in Git via the MEOW stack ([Cloud Native Now](https://cloudnativenow.com/features/gas-town-what-kubernetes-for-ai-coding-agents-actually-looks-like/)). Where Symphony assumes "one ticket, one agent, one PR," Gas Town assumes coordinated swarms working on related work with explicit handoffs.

I covered the "swarm-style" pattern in more detail in my [Claude Code agent swarm architecture write-up](/claude-code-agent-swarm-architecture/) — if you're building anything where multiple agents need to coordinate on the *same* task rather than parallel tasks, that's the post to read after this one.

### 3. Archon — Deterministic Workflows Around Any Coding Agent

[Archon](https://github.com/coleam00/archon) bills itself as "the first open-source harness builder" — the framing is exact. Where Symphony hardcodes the workflow (poll Linear → run agent → PR), Archon lets you author the workflow as a YAML pipeline with explicit phases, validation gates, and artifacts. Each run gets its own git worktree. You can run five fixes in parallel with no conflicts ([MindStudio](https://www.mindstudio.ai/blog/what-is-archon-harness-builder-ai-coding)).

I reach for Archon when the work has known phases — "investigate, plan, implement, test, document" — and I want each phase to be a separate validated step rather than one long agent run. This is also the closest cousin to spec-driven approaches like the one I wrote about in my [Traycer BART-mode spec-driven AI piece](/traycer-bart-mode-spec-driven-ai/).

### 4. Ralph Loops — The Minimum Viable Outer Harness

The [Ralph Wiggum loop](https://ralph-wiggum.ai/) (yes, named after the Simpsons character — "I'm helping!") is the simplest possible outer harness: a `while true` loop that re-invokes the agent until a deterministic check passes. There's an [official Anthropic plugin in the Claude Code repo](https://github.com/anthropics/claude-code/blob/main/plugins/ralph-wiggum/README.md) and [Vercel Labs ships ralph-loop-agent](https://github.com/vercel-labs/ralph-loop-agent) for the AI SDK.

The Ralph philosophy: don't aim for perfect on the first try. Let the loop refine. The agent reads a plan file, makes progress, the loop checks completion against criteria, and if it's not done, the agent runs again with the latest state ([beuke.org](https://beuke.org/ralph-wiggum-loop/)).

Ralph isn't competitive with Symphony — it's *complementary*. A Symphony ticket can run a Ralph loop inside its workspace. Symphony handles the queue and isolation. Ralph handles the iteration until correctness. Wire them together and you have something genuinely powerful.

## Setting Up Symphony On A Real Repo

Enough theory. Let me show you exactly what I did to get Symphony running on a side project last week. The whole thing took me about 90 minutes from `git clone` to first PR, and most of that was Linear configuration, not Symphony itself.

### Step 1: Get The Linear API Key

In Linear, go to **Settings → Security & access → Personal API keys** and create a new key. Set it as `LINEAR_API_KEY` in your environment. Symphony uses this token through its `linear_graphql` dynamic tool — it doesn't expose the raw token to sub-agents, which is a small but thoughtful security choice ([allthings.how](https://allthings.how/symphony-openais-open-source-orchestrator-that-turns-linear-into-a-codex-command-center/)).

### Step 2: Clone The Repo And Pick An Implementation

```bash
git clone https://github.com/openai/symphony.git
cd symphony
```

The repo has the spec at `SPEC.md` and the Elixir reference under `elixir/`. If you have Elixir installed (`brew install elixir` on macOS), you can be running in two minutes. If you don't, the simplicity of the spec means rewriting the orchestrator in a language you know is genuinely tractable — that's the whole point of distributing a spec instead of a binary.

For the rest of this walkthrough I'll show the Elixir path because it's what I used.

```bash
cd elixir
mix deps.get
mix compile
```

### Step 3: Configure Your Workflow

Copy the `WORKFLOW.md` template into the *target repo* (the one you want agents to work on, not the Symphony repo itself). This is where the harness vocabulary pays off, because `WORKFLOW.md` is essentially your guide-and-sensor specification for every agent run. A trimmed version of mine looked like this:

```markdown
# Workflow for {{ project }}

## Before you start
- Read README.md and ARCHITECTURE.md
- Run `npm install` and `npm test` to confirm baseline passes

## Implementation
- Make the smallest change that satisfies the ticket
- Add or update tests for any new behavior
- Run `npm test` and `npm run lint` before considering done
- Run `npm run typecheck` — zero errors required

## Definition of done
- All tests pass locally
- Lint and typecheck pass with zero warnings
- PR title matches the ticket title
- PR description references the Linear ticket ID
```

That document is doing serious work. The "Before you start" section is a **guide**. The four sensor commands (`npm test`, `npm run lint`, `npm run typecheck`, plus the ticket-link convention) are **computational sensors**. There's not a single LLM-as-judge in there yet, and the system already works. Don't reach for inferential sensors until your computational ones are saturated.

### Step 4: Point Symphony At Linear

In your `.env`:

```bash
LINEAR_API_KEY=lin_api_your_key_here
SYMPHONY_TARGET_REPO=/Users/you/code/your-project
SYMPHONY_LINEAR_TEAM_ID=your_team_id
SYMPHONY_TRIGGER_LABEL=ready-for-codex
SYMPHONY_MAX_CONCURRENT=4
```

I cap concurrency at 4 on my laptop because Codex sessions are not free and I'd rather watch four serious runs than ten degraded ones. On a devbox you'd raise this.

### Step 5: Run It

```bash
mix run --no-halt
```

That's the full setup. Symphony is now polling Linear every 30 seconds. Tag any ticket with `ready-for-codex` and an agent will claim it.

The first time I did this I created a deliberately small ticket — *"Add a `/health` endpoint that returns `{ status: 'ok', uptime_seconds: <number> }`"* — and watched. The agent picked it up in 31 seconds. It read the codebase. It found my Express app. It wrote the route, wrote a test, ran the test (which passed on the second try after fixing a tiny TypeScript inference issue), opened a PR, and tagged the Linear ticket as "review needed." Total wall time: 4 minutes 18 seconds. I read the diff. I merged.

I sat there and stared at the screen.

## What I Got Wrong On The Way

I want to spend real time on this section because it's where I learned the most, and because every "first look" article I've read on Symphony glosses over it. I made four mistakes worth telling you about.

### Mistake 1: I Treated Symphony Like Claude Code

For the first two days I kept opening the Symphony devbox terminal expecting to *talk* to the running agents. Symphony doesn't work that way. The agents are headless. You communicate with them through tickets. If you want to give an agent more context, you edit the Linear ticket description (or add a comment) and let the next polling cycle pick up the change. If you want to redirect an agent mid-flight, the answer in 90% of cases is *don't* — kill the run, edit the ticket, let it start fresh.

This was a mental shift. It took me a couple of days to stop treating agents as collaborators I was pair-programming with and start treating them as workers I was issuing tickets to. That shift, by the way, is exactly what Symphony's design is trying to force on you. Engineer your tickets like product specs, not like Slack messages.

### Mistake 2: I Wrote Vague Tickets

My early tickets were the same kind of one-liners I'd give a senior teammate: "Refactor the auth middleware." A senior teammate fills in the gaps with shared context. An agent does not. When I wrote *"Refactor the auth middleware to extract the JWT validation into a separate function, keep the existing public API of `requireAuth(req, res, next)`, add tests for the extracted function, and don't change route registrations,"* the agent shipped a clean PR on the first run.

The principle: every ambiguity in your ticket becomes a coin flip in your agent's behavior. Tickets are guides. The more you front-load the guide, the less you'll need the sensors to reject bad output.

### Mistake 3: I Skipped Computational Sensors

My target repo had `npm test` and `npm run lint`, but I had not added them to the `WORKFLOW.md` definition of done. The agent technically ran tests when it felt like it. About one in three PRs had failing tests on arrival, which I had to bounce back. The fix was thirty seconds of editing `WORKFLOW.md` to make sensor commands non-negotiable. Failure rate dropped to roughly one in fifteen, in line with what I see when I run Codex manually.

You will not believe how often the answer is "add a deterministic check to your workflow file."

### Mistake 4: I Tried To Run Symphony Without Worktree Isolation

Out of laziness, I pointed two parallel runs at the same checkout of the same repo. Predictable carnage. Symphony's design assumes — and the Elixir reference enforces — git worktree isolation per ticket. Don't fight it. Each agent gets its own working copy. That's how five agents touching different parts of your codebase don't end up stomping on each other's WIP.

This is also where Symphony's design starts to look a lot like Archon's "every workflow run gets its own git worktree" approach. The convergence isn't an accident. Worktree-per-run is becoming the load-bearing convention of all serious outer harnesses.

If you'd rather hand this kind of setup to someone who has done it before, [Ramlit Limited](https://www.ramlit.com) builds and operates exactly these orchestration pipelines for engineering teams that want the throughput without the eight-weekend learning curve. But the path is genuinely walkable on your own — that's most of what I'm trying to show you here.

## How Should You Read The 500% Number?

I told you earlier I'd come back to this. OpenAI's headline metric is that some internal teams saw landed pull requests rise by 500% in the first three weeks of using Symphony ([OpenAI](https://openai.com/index/open-source-codex-orchestration-symphony/)).

Here's the honest read.

That number is real, in the narrow sense. PR throughput went up. That's measurable, repeatable, and not in dispute. But "landed PRs" is a *generation* metric, and as several analysts have pointed out, **generation scales effortlessly. Validation does not.** ([opentools.ai](https://opentools.ai/news/openai-symphony-turns-linear-boards-into-autonomous-coding-agent-orchestration)).

In my own (small) sample over three weeks of side-project work with Symphony, my PR throughput went up by something like 3-4x on the kinds of tickets that Symphony is good at — well-scoped, single-feature, test-coverable. On ambiguous tickets, performance was worse than me-with-Claude-Code, because every coin-flip in the spec compounded.

The mental model I've settled on: Symphony moves the bottleneck from "writing the code" to "writing the spec and reviewing the PR." That's a real productivity gain *if and only if* your spec-writing and review process can keep up. If review becomes the bottleneck and you start rubber-stamping PRs to clear the queue, you've engineered yourself a faster way to produce technical debt.

So when you see "500%," translate it to: "this team had spec-writing and review processes mature enough that 5x more code was actually shippable." That's the question to ask of yourself before you adopt Symphony — not "can I run more agents?" but "can I review more agents' output without quality collapsing?"

## What This Means For How I'm Building Now

The week after I got Symphony running, I rewrote the way I structure work on my main client project. Three concrete changes.

**One — every issue I open now ends with a "Definition of done" section.** Not because Symphony will pick it up (the client project doesn't run Symphony yet) but because writing tickets in that shape is now my baseline. The discipline a coding agent demands is the same discipline a junior engineer benefits from.

**Two — I added `npm test`, `npm run lint`, and `npm run typecheck` as *required* steps in my agent workflow files everywhere.** No exceptions. Computational sensors are free. Use them.

**Three — I stopped distinguishing between "AI tools I use" and "AI infrastructure I run."** That distinction is dead. Codex, Claude Code, Cursor — these are inner harnesses. They sit inside something. The question is what *that* something looks like. Mine is going to look more and more like Symphony plus a Ralph-style inner loop with computational sensors. Yours might look different. But it's going to be *something*.

If you want a deeper read on the broader shift toward agent-as-infrastructure, [my piece on Anthropic's long-running agent harness](/anthropic-long-running-agent-harness/) covers the inner-harness side from a Claude angle, and the [Paperclip case study on orchestrating zero-human AI companies](/paperclip-orchestrating-zero-human-ai-companies/) shows what happens when you push the outer harness pattern to its logical conclusion. For the practical Codex side, [my comparison of Codex Claude Code as a Cursor replacement](/codex-claude-code-replace-cursor/) lays out where Codex genuinely beats other coding agents in 2026.

## A Word On Where This Goes Next

Three predictions, ranked from "I'm confident" to "I'd bet a coffee."

**Confident.** Within twelve months, every serious engineering org will have something Symphony-shaped in production. It might be Symphony itself, it might be Gas Town, it might be Archon, it might be a homegrown wrapper. The shape — issue-tracker-as-queue, isolated-workspace-per-task, deterministic-sensors-in-the-loop, human-review-at-the-end — is the future. The vocabulary is already converging. The implementations will follow.

**Reasonably confident.** The bottleneck for most teams won't be the orchestrator. It'll be the harness *inside* the agent — the prompts, the skills, the playbooks, the sensor commands. Teams that already invest in `CLAUDE.md`-style discipline will adopt Symphony faster than teams that don't, and the gap will widen. (If you've been ignoring [agent skills](/claude-code-agent-skills-guide/), now is the time to stop.)

**Bet a coffee.** Within six months, Linear will ship native Symphony-style support and either OpenAI or a third party will release a hosted Symphony runtime that abstracts the devbox part away. The current "rent your own devbox" pattern is too operationally heavy to be the long-term answer.

But here's the deeper point. Symphony isn't important because it's the best orchestrator. It's important because it gave us a *shared spec* — a vocabulary anyone can implement, fork, or steal from. That's the move that turns a tool into a category.

Go back to the moment I described in the opening — the eleven-minute Linear ticket that shipped without me. That moment isn't impressive because of what one agent did. It's impressive because of what *one shared spec, run as an outer harness, around any inner harness, around any model* makes possible at scale.

If you read only one thing today, read the [Symphony SPEC.md](https://github.com/openai/symphony/blob/main/SPEC.md) cover to cover. It's twelve pages. By the time you finish it, you'll see your current AI workflow differently. And that — not the 500%, not the 15,000 stars — is the actual shift worth optimizing for.

Now go assign yourself a real ticket. The one you've been putting off. Write it like a spec. Add a definition of done. Imagine an agent you've never met is going to read it.

Then ask yourself: *would the work get done?*

If the answer is no, the problem was never the agent.

## Frequently Asked Questions

### What is OpenAI Symphony and how does it work?
Symphony is an open-source spec from OpenAI that turns an issue tracker like Linear into a control plane for autonomous coding agents. It polls the tracker every 30 seconds, claims tickets matching a trigger label, spins up an isolated git worktree per ticket, runs a Codex or Claude Code agent in that workspace until it produces a PR, and links the PR back to the ticket. The reference implementation is in Elixir, but the spec is language-agnostic. See [Setting Up Symphony On A Real Repo](#setting-up-symphony-on-a-real-repo) above for the full walkthrough.

### How is Symphony different from Gas Town, Archon, and Ralph loops?
Symphony assumes one ticket, one agent, one PR — and the issue tracker is the queue. Gas Town runs coordinated colonies of 20-30 parallel agents with explicit roles. Archon authors deterministic YAML workflows around any coding agent with explicit phase gates. Ralph loops are the minimum viable inner loop — `while not done: run agent again with latest state`. They're complementary: a Symphony ticket can wrap a Ralph loop inside its workspace.

### Does the OpenAI Symphony 500% PR increase claim hold up in practice?
The claim is real for narrowly-scoped tickets on teams with mature spec-writing and review processes — generation throughput rises sharply. But "landed PRs" is a generation metric, and validation doesn't scale the same way. In my own testing I saw 3-4x on well-scoped tickets and worse-than-baseline on ambiguous ones. The honest read: Symphony moves the bottleneck from coding to spec-writing and review.

### What is harness engineering and why does it matter for coding agents?
Harness engineering is the discipline of designing everything around the model that turns it into a reliable agent — guides (feedforward steering: prompts, skills, playbooks) and sensors (feedback validation: linters, tests, type checkers, LLM-as-judge). Birgitta Böckeler's [Martin Fowler article](https://martinfowler.com/articles/harness-engineering.html) is the canonical reference. The framework distinguishes inner harness (inside the agent) from outer harness (around the agent), and Symphony is squarely an outer harness.

### Do I need to know Elixir to use Symphony?
No. The Elixir implementation is a reference, not a requirement. The spec is the actual artifact, and OpenAI demonstrated that Codex can re-implement the spec in TypeScript, Go, Rust, Java, and Python. If your team already runs one of those languages, reimplementing the orchestrator is a tractable weekend project. If you're comfortable with Elixir or willing to learn the basics, the reference runs out of the box.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
