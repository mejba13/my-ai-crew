**BRAND:** mejba.me
**TITLE:** OpenSpec Review: My New Daily Driver for AI Coding
**META TITLE:** OpenSpec Review: My Daily Driver for AI Spec-Driven Coding
**SLUG:** openspec-ai-coding-framework-spec-driven-development
**PRIMARY KEYWORD:** OpenSpec AI coding framework
**META DESCRIPTION:** I tested OpenSpec for two weeks across three real projects. Here's why this spec-driven AI coding framework replaced my Claude Code planning workflow.
**TAGS:** AI Development, Spec-Driven Development, Claude Code, Practitioner Review, OpenSpec

---

The first time I ran `openspec apply` on a real project, I stepped away to refill my coffee and forgot about it. Two hours and seventeen minutes later, I came back to a working dashboard, a passing test suite, and a `changes/` folder full of markdown that explained — to a level of detail I'd never have written by hand — exactly what had just been built and why.

That's not the impressive part. The impressive part is what happened the next morning when I needed to add a second feature. I didn't open the code. I opened the specs folder. I read three short markdown files. And I knew, with a kind of clarity I usually only get after a week of context-loading, exactly where the new feature should plug in and what it would touch.

That moment is when I understood what the OpenSpec AI coding framework is actually doing — and why I think it's the first spec-driven tool that survives contact with my real workflow.

I've been running it as my daily driver for two weeks across three projects: a Laravel admin tool I'm refactoring for a client, a Next.js side project I started from scratch, and an existing Python codebase I inherited that has roughly the documentation hygiene of a hostage note. OpenSpec earned its place in all three. But not for the reasons the marketing pages list.

This is the full breakdown — what OpenSpec is, where it sits in the AI coding framework landscape, the exact commands I run, and the honest places where it doesn't help. If you've already burned hours watching a Claude Code session generate code against a spec that was wrong from the start, the next 4,000 words are going to feel personal.

## The Three Categories of AI Coding Frameworks (And Why It Matters)

Before OpenSpec makes sense, you need the map of the territory it lives in. I made this mistake the first time I evaluated it — I compared it against tools it was never trying to beat, and I almost dismissed it for the wrong reasons.

There are three distinct categories of AI coding frameworks shipping in 2026, and they solve different problems:

| Category | Tools | Primary Artifact | Human Role | Best For |
|---|---|---|---|---|
| **Spec-driven** | OpenSpec, GitHub Spec Kit, Kiro | The spec itself | Orchestrator | Complex features, brownfield refactors, vibe coding with guardrails |
| **SDLC enforcement** | Obra Superpowers, Agent Skills, Mattpocock skills | Process discipline (TDD, code review, planning) | Reviewer | Teams who already know what to build but want quality gates |
| **Autonomous pipelines** | BMAD-METHOD, GSD, Taskmaster AI | Working code, shipped autonomously | Spec writer + sign-off | Sprint execution, multi-feature backlogs, side projects you want to ship while you sleep |

These categories don't compete head-to-head. They stack.

A real production stack looks more like: OpenSpec writes the spec, [Obra Superpowers](https://www.mejba.me/superpowers-plugin-claude-code-review) enforces TDD during the build, BMAD orchestrates the multi-agent pipeline that ships PRs while you sleep. Same problem from three angles. Different layers of the same machine.

Where most "spec-driven vs autonomous" articles go wrong is treating these as alternatives. They aren't. They're tiers. And OpenSpec is the bottom tier — the one that decides what the other two tiers will be working on.

That's why getting this layer right matters more than the layers above it. A perfect TDD enforcement skill won't save you if the spec it's enforcing was wrong on day one. An autonomous pipeline that ships 40 PRs in a sprint will simply ship 40 wrong PRs. Spec quality is the upstream choke point. Everything else amplifies whatever decision was made there — for better or worse.

So the question isn't "OpenSpec or BMAD?" The question is "is OpenSpec good enough to be the source of truth that everything downstream trusts?" That's what I went in to test.

## What OpenSpec Actually Is

OpenSpec is an open-source spec-driven development framework, shipped as the npm package `@fission-ai/openspec` and maintained by Fission-AI. It runs in any project as a thin layer on top of your AI coding assistant — Claude Code, Cursor, Codex, Windsurf, Continue, GitHub Copilot, Amazon Q, and around twenty others according to the [supported tools doc](https://github.com/Fission-AI/OpenSpec/blob/main/docs/supported-tools.md).

The core idea is brutal in its simplicity. The specification is the artifact. The code is the compile target. Every feature lives in a markdown file in your repo before a single line of code is written, and that file becomes the contract that AI assistants execute against.

Three things about that idea matter more than the idea itself.

**First, the specs live in your repo.** Not in a SaaS dashboard. Not in a Notion doc that drifts the moment someone hits "ship it." In a `openspec/` folder, in git, with diffs you can review in PRs like any other code change. This is the part most teams underestimate until they need to reconstruct what a feature was supposed to do six months later. With OpenSpec, you `git log` the spec.

**Second, the specs are deltas.** OpenSpec invented something I haven't seen done well in any other framework: the proposal isn't a fresh document, it's a *change* against the existing spec. Each proposal explicitly tags additions, modifications, and removals. When the change ships, the deltas merge into the master spec. This is the brownfield magic — it's how OpenSpec stays usable on legacy codebases instead of forcing you to greenfield a 200,000-line app to use it.

**Third, the workflow is fluid, not waterfall.** The official Fission-AI framing is "fluid not rigid, iterative not waterfall" — and after two weeks I can confirm that's not marketing. You can drop into the workflow at any phase, you can loop back when implementation reveals something the spec missed, and you can run `archive` to commit a partial change and start a new one. It feels less like a process and more like a notebook that happens to enforce structure.

The clearest comparison is GitHub's [Spec Kit](https://github.com/github/spec-kit). I tested both. Spec Kit is heavier — its specs run around 800 lines per feature according to the comparison work [Hashrocket published](https://hashrocket.com/blog/posts/openspec-vs-spec-kit-choosing-the-right-ai-driven-development-workflow-for-your-team), versus OpenSpec's roughly 250. Spec Kit treats every feature as its own independent file. OpenSpec consolidates into a single living source of truth that mutates over time. For a clean greenfield enterprise feature with formal review gates, Spec Kit's heavier output is justified. For everything else I do — refactors, side projects, agency client work — OpenSpec's lighter footprint wins because the spec is something I'll actually re-read.

That's the philosophical layer. Here's what running it looks like in practice.

## Installing OpenSpec and the First-Run Experience

The setup is one command. Not "one command after you install three dependencies." One command.

```bash
npm install -g @fission-ai/openspec
cd your-project
openspec init
```

Node 20.19.0 or higher is required. The init command asks you which AI assistant you're using — I picked Claude Code on the Laravel project, Cursor on the Next.js one, Codex on the Python codebase — and it writes the appropriate slash command bindings into your project's config so the assistant knows how to invoke OpenSpec.

This is where I noticed the first thing that made me trust the tool. On a fresh init, OpenSpec doesn't dump you in front of a blank document and say "good luck." It runs an onboarding skill that walks you through your first proposal interactively. It asks you what you're building. It asks who the user is. It asks what the success criteria are. It does this in markdown that lives in your repo, so the first artifact you have isn't a tutorial — it's the start of a real spec for a real feature.

Compare that to my experience starting with Spec Kit. The Spec Kit onboarding assumes you already know the four-phase model (`/specify`, `/plan`, `/tasks`, `/implement`) and drops you straight into the `/specify` step. If you haven't used spec-driven development before, you write a spec that's wrong in shape, and you find out three commands later when `/tasks` produces nonsense.

OpenSpec's onboarding is the difference between learning by reading and learning by doing the right thing the first time. It's a small detail. It's also why I've now recommended OpenSpec to two friends who'd never touched spec-driven development before.

## The Command Reference: What Each One Does and When I Use It

OpenSpec ships with a base command set and an expanded command set called `opsx` that you opt into via `openspec config profile`. I've been running with the expanded profile because I want every tool the framework offers. Here's the full table, with what I actually use each one for in production:

| Command | What It Does | When I Use It |
|---|---|---|
| `openspec init` | Initialize OpenSpec in a project, choose AI assistant integration | Once per project, on day one |
| `/openspec:propose` | Create a new proposal: proposal.md, design.md, specs/, tasks.md | Standard new-feature flow |
| `/opsx:new` | Start a new change workflow with the expanded profile | When I want the iterative path, not the one-shot proposal |
| `/opsx:explore` | Pre-proposal exploration — clarify ambiguities, investigate, surface unknowns | Before *every* non-trivial change |
| `/opsx:continue` | Advance iteratively through proposal → spec → tasks, one slice at a time | Large changes I want to break up |
| `/opsx:ff` (fast-forward) | Auto-run the remaining proposal/spec/tasks steps once the plan is accepted | After exploration confirms the direction |
| `/openspec:apply` | Implement the tasks defined in the proposal | The build phase — runs ~2hr autonomous, 3-4hr with my interruptions |
| `/opsx:verify` | Validate the implementation against the spec, including visual UI checks via Chrome integration | Design system migrations, UI-heavy features |
| `/openspec:archive` | Merge the change deltas into the master spec, commit the living source of truth | Every shipped feature, no exceptions |
| `/opsx:bulk-archive` | Archive multiple completed changes in one pass | End of a sprint when I've shipped 3-5 features |
| `/opsx:sync` | Sync the master specs folder with what's actually in the codebase | When I suspect drift between code and spec |
| `/opsx:onboard` | Re-run the interactive onboarding skill | Bringing a teammate or a new project into the OpenSpec workflow |

The full command list lives in the official [OpenSpec commands doc](https://github.com/Fission-AI/OpenSpec/blob/main/docs/commands.md), and the expanded profile reference is in the [opsx doc](https://github.com/Fission-AI/OpenSpec/blob/main/docs/opsx.md). What's not in the docs is the order I run them in or which ones I skip — and that's the next section.

## The Workflow I Actually Run

Here's the exact sequence I followed shipping a "user activity timeline" feature into the Laravel admin tool last week. The whole thing took about four hours of wall-clock time, of which maybe forty minutes was me and the rest was OpenSpec working in the background.

### Step 1: Explore Before You Propose

The first command I run on any change above "trivial" is `/opsx:explore`. This is the single most underrated feature in the framework, and it's the one thing OpenSpec does that GitHub Spec Kit explicitly refuses to do.

Spec Kit's design assumes you walk into `/specify` already knowing what you want to build. That assumption is wrong roughly 70% of the time in my experience. What I usually have is a fuzzy idea, three constraints I half-remember, and a hunch that there's a complication I haven't surfaced yet. If I write a spec from that state, the spec will be wrong, and I'll find out four steps later.

`/opsx:explore` gives me a phase to be wrong out loud. The agent asks me clarifying questions. It investigates the codebase. It surfaces ambiguities I didn't know existed. On the activity-timeline feature, exploration caught three things I would have missed if I'd jumped straight to a proposal: the existing audit log table had a column ambiguity that would have caused query collisions, two of my "user actions" were actually two different events I'd been mentally collapsing, and the UI requirement implied a real-time component I hadn't budgeted for.

That's roughly two days of rework that exploration prevented in fifteen minutes.

If I'm being honest, I almost never used `/opsx:explore` for the first three days. It felt like overhead. Then I tried it on a refactor that turned out to have a hidden dependency on a deprecated cache layer, and I haven't skipped it since. The pattern I've internalized: if I can't explain the change in one sentence with no qualifications, I run `explore` first.

### Step 2: Propose, or Continue, or Fast-Forward

Once exploration has cleared the fog, I run one of three commands depending on the change size:

- **`/openspec:propose`** for small, well-scoped changes where I want everything generated in one pass.
- **`/opsx:continue`** for larger changes I want broken into vertical slices — proposal first, then spec, then tasks, with a review gate between each.
- **`/opsx:ff`** when exploration was thorough and I want OpenSpec to fast-forward through the remaining proposal/spec/tasks steps without making me click through each gate.

The output of any of these is the same: a `changes/[change-name]/` folder containing `proposal.md` (the why and what), `design.md` (the how, including system design), one or more files under `specs/` (the functional scenarios that act as the contract), and `tasks.md` (the work breakdown the assistant will execute).

This is the moment in the workflow where the spec stops being mine and becomes the team's. Even if the team is just me. The proposal is reviewable in a PR. The design.md captures the architectural decisions in language a future me can understand. The specs/ folder is the part the AI assistant will treat as the contract during implementation — and crucially, it's the part that survives the change and merges into the master spec on archive.

A note on what makes OpenSpec specs different. Each spec describes functional scenarios — given/when/then style narratives that describe behavior. Not implementation details. Not "the controller calls the repository." Behavior. This is the shape that survives codebase changes. When I refactor the persistence layer six months from now, the spec is still correct because the spec was never about how the persistence layer worked. It was about what the user saw and could do.

### Step 3: Apply (Or: How I Stopped Watching the Build)

`/openspec:apply` is the command that builds the feature. This is where most of the runtime lives — about two hours of autonomous work on the activity-timeline feature, three to four hours when I'm interrupting to clarify things mid-build.

The thing I had to learn here is when to interrupt and when to walk away. My first instinct was to babysit the apply phase like I babysit a Claude Code session — read every diff, approve every file change, second-guess every decision. That instinct is wrong with OpenSpec. The spec already encoded my decisions. The apply phase is just executing the contract. If I find myself wanting to interrupt to "make a different choice," the right move is almost always to stop the apply, go back to the proposal, fix the spec, and re-apply.

Once I internalized that, I started leaving the apply phase running in the background while I worked on something else. Tab open, terminal visible, but I'm not staring at it. The first time I did this was uncomfortable. The fifth time, it was the normal pattern. The tenth time, I caught myself having shipped two features in parallel by running apply in two terminals against two different proposals.

This is the moment that changed my mental model of AI coding. The bottleneck stopped being execution. It became spec quality. Which means my time started going to the part of the work that actually requires human judgment, and the rest got pushed to the agent.

### Step 4: Verify (The Underrated Killer Feature)

`/opsx:verify` is the command nobody talks about, and it's the one that made OpenSpec irreplaceable on the Next.js side project.

The Next.js project involved migrating an old design system to a new one — same components, new tokens, new spacing scale, new typography. This is a nightmare class of work for AI coding tools because the code can be technically correct and visually completely wrong. A button can render. A button can also render at the wrong size, in the wrong font, with the wrong border radius, and pass every test you wrote.

`/opsx:verify` ships with a Chrome extension integration that does visual fidelity checks against the spec. It loads the page, captures the rendered state, and compares it against the design intent encoded in the spec. On the design-system migration, it caught seven visual regressions that the test suite missed. Spacing inconsistencies. A wrong font weight on a heading variant. One component that had inherited a margin from the old system and was overflowing its grid cell at certain breakpoints.

This is the feature that doesn't exist in any other AI coding framework I've tested. Spec Kit doesn't have it. BMAD doesn't have it. Obra Superpowers can enforce that you wrote tests, but it can't tell you the test passes while the page looks wrong. For UI-heavy work, especially design system migrations, `/opsx:verify` is the difference between shipping and shipping correctly.

The honest caveat: it's not magic. The Chrome extension needs your dev server running. The visual checks are only as good as the design intent you encoded in the spec. And it can't catch interaction bugs — only static rendering issues. But for the class of bugs it catches, nothing else does.

### Step 5: Archive (The Living Documentation Trick)

This is the step I almost skipped the first week. It's also the step that's quietly the most important.

`/openspec:archive` takes the change you just shipped and merges its deltas into the master `specs/` folder. The proposal moves to a `changes/archive/` directory. The master spec — the living source of truth — gets the new functionality merged in, the modifications applied, and the removals removed.

The result is that after every shipped feature, your repo contains a current, accurate, machine-readable description of how the entire system behaves. Not an outdated README. Not a Confluence page that hasn't been touched in eighteen months. The actual current state.

I tested what this means six days into using OpenSpec by asking Claude Code to add a new feature to the Laravel project — without giving it any context. I told it: "read `openspec/specs/`, then propose a change that adds X." It read the specs. It proposed a change that correctly identified two existing features it would need to interact with. It did this with no prompting from me about the codebase architecture, because the architecture was already in the spec.

This is the first time I've seen "living documentation" actually be a load-bearing part of an AI coding workflow instead of a nice-to-have. With most projects, you spend the first five minutes of every Claude Code session re-loading context. With OpenSpec, the context loads itself.

If `archive` ever feels like overhead, I can promise you it isn't. Skipping archive is how spec-driven projects rot. Every team I've talked to that abandoned spec-driven development did it because their specs drifted from their code. Archive is the discipline that prevents drift. Treat it as part of "feature done," not as an optional cleanup step.

## Where OpenSpec Falls Short (The Honest Section)

I'm two weeks in and a fan, but a fan who's run this in three projects with very different shapes. Here's where it doesn't help.

**Tiny changes.** If the change is "fix this typo" or "rename this variable," the OpenSpec workflow is overhead. Don't run a proposal for a one-line fix. The framework doesn't pretend otherwise — Fission-AI explicitly recommends against it — but I've watched newcomers force every change through the proposal flow and end up with a `changes/` folder that looks like a graveyard of trivial PRs. Use OpenSpec for things that benefit from being thought about. Skip it for things that don't.

**Solo coding sessions where you genuinely don't know what you want.** If you're in pure exploration mode — sketching, prototyping, throwing code at the wall — the proposal phase will slow you down. The right pattern here is to vibe-code first, then once you know what you've built, write the spec retroactively and `archive` it as the first proposal. OpenSpec is fine with this flow. But trying to spec something you haven't designed yet is the worst of both worlds.

**The two-hour apply runtime is real.** Most apply runs land in the 90-180 minute range for non-trivial features. If you need a feature shipped in twenty minutes, OpenSpec is not the right tool. This isn't a flaw — the spec quality is what makes the apply phase reliable, and that quality takes time to encode and execute. But manage your expectations. OpenSpec is a tool for shipping right, not shipping fast.

**Brownfield onboarding has rough edges.** On the inherited Python codebase, my first attempt to introduce OpenSpec resulted in a master spec that was about 40% accurate to the actual code. The framework was trying to infer specs from code that didn't have any guiding principles, and the inferences were necessarily fuzzy. The fix was running `/opsx:onboard` properly, taking time to write the master spec by hand for the core flows, and *then* using OpenSpec for new changes. If you walk in expecting to drop OpenSpec onto a legacy codebase and have it understand the codebase by Friday, you'll be disappointed. Plan a real onboarding week.

**The community is small.** Compared to BMAD's GitHub presence or Spec Kit's enterprise backing, OpenSpec is the smaller ecosystem. The docs are good. The release cadence is healthy. But there isn't yet a deep stack of tutorials, plugins, or third-party integrations. If you need something the framework doesn't natively do, you'll be writing it yourself. For me, this is fine — I'd rather have a small focused tool than a large bloated one — but it's worth knowing.

## Where OpenSpec Fits in My Stack Now

After two weeks, OpenSpec sits at the very top of my AI coding workflow — above Claude Code, above the [agent skills](https://www.mejba.me/agent-skills-advanced-claude-code) I run, above the planning notes I used to keep in Obsidian. It's the first thing I touch on a non-trivial change and the last thing I touch when shipping it.

The full stack looks like this:

1. **OpenSpec** writes and maintains the spec — the contract for what's being built
2. **Claude Code** with [Obra Superpowers](https://www.mejba.me/superpowers-plugin-claude-code-review) executes the spec with TDD enforcement during the apply phase
3. **Standard git review** catches what TDD missed
4. **Archive** locks the change into the living spec, ready for the next iteration

Each tool does the thing it's best at. OpenSpec doesn't try to enforce TDD — that's Superpowers' job. Superpowers doesn't try to manage the spec — that's OpenSpec's job. Claude Code doesn't try to remember everything that's been built — that's the master spec's job.

This separation of concerns is what makes the stack actually scale. When something breaks, I know which layer to debug. When I want to upgrade one piece, I can swap it without rebuilding the others. The composability is the win.

## Should You Use OpenSpec?

Three questions to answer honestly:

**Do you ship features that take more than two hours of focused work?** If yes, OpenSpec pays for itself by the second feature. If you're shipping micro-changes only, skip it.

**Do you ever come back to a project after two weeks and need to reload context?** If yes, the living spec is going to save you hours every month. If your projects are all current and your memory is perfect, the value is lower.

**Do you trust your AI assistant more when it has a clear contract to execute against?** If yes, OpenSpec is the contract layer. If you'd rather just chat with the assistant and steer turn-by-turn, the proposal flow will feel like friction.

I answered yes to all three, and OpenSpec has been the most consequential addition to my workflow since I started using Claude Code. Not because it does any one thing dramatically better than the alternatives, but because it makes every other tool in my stack more reliable. The spec is the upstream choke point. OpenSpec made the choke point clean.

## Frequently Asked Questions

### What is OpenSpec used for?
OpenSpec is a spec-driven development framework for AI coding assistants. You write a markdown specification of a feature, and the framework coordinates with your AI assistant (Claude Code, Cursor, Codex, and 20+ others) to implement the spec, validate the result, and merge the change into a living source of truth. It's used most heavily for medium-to-large features in brownfield codebases where spec quality has compounding value.

### How is OpenSpec different from GitHub Spec Kit?
OpenSpec consolidates specs into a single living document with delta-based changes, while Spec Kit maintains separate spec files per feature. OpenSpec produces lighter specs (around 250 lines vs Spec Kit's 800), supports brownfield codebases natively, and offers an iterative workflow with the `/opsx:explore` phase. Spec Kit's heavier four-phase model fits formal greenfield enterprise work better. For a deeper breakdown, see "What OpenSpec Actually Is" above.

### Does OpenSpec work with Claude Code?
Yes — Claude Code is one of 20+ supported AI coding assistants. During `openspec init`, you select Claude Code as your assistant and OpenSpec writes the corresponding slash command bindings into your project. Cursor, Codex, Windsurf, Continue, GitHub Copilot, Amazon Q, Gemini CLI, and others are also fully supported.

### How long does an OpenSpec apply run take?
A typical `openspec apply` run lands in the 90-180 minute range for non-trivial features when run autonomously. Adding human checkpoints and clarification mid-build extends this to 3-4 hours of wall-clock time. Most of that time is the AI assistant working, not waiting — you can leave it running in the background while you work on something else.

### Can OpenSpec replace BMAD or Obra Superpowers?
No, and you shouldn't try to make it. OpenSpec is a spec-driven framework. BMAD is an autonomous pipeline orchestrator. Obra Superpowers is an SDLC enforcement layer. They solve different problems and stack well together: OpenSpec writes the spec, Superpowers enforces TDD during the build, BMAD orchestrates multi-feature pipelines. For a full breakdown of these three categories, see the comparison table near the top of this article.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
