**BRAND:** mejba.me
**TITLE:** Engineering With AI Agents: The 5-Skill Pipeline I Run
**META TITLE:** Engineering With AI Agents — The 5-Skill Pipeline
**SLUG:** engineering-with-ai-agents-skills-pipeline
**PRIMARY KEYWORD:** engineering with AI agents
**META DESCRIPTION:** Engineering with AI agents the right way: the five Claude Code skills I run — Grill Me, PRD, Issues, TDD, Architecture Refactor — to ship real software.
**TAGS:** Claude Code, AI Agents, Software Engineering, Skills, Workflow

---

I lost a Sunday to a Laravel feature that should have taken four hours.

Not because the code was hard. The code was easy. I lost the Sunday because I let Claude Code start writing before I'd answered the questions that would have shaped the design. Halfway through the second prompt I realized the agent had assumed a one-to-many relationship that needed to be many-to-many, generated thirty tests against the wrong contract, and was now confidently refactoring its way deeper into the wrong abstraction. By dinner I'd rolled the branch back to its first commit and opened a blank file.

That's the failure mode of **engineering with AI agents** that nobody puts on the marketing page. The agent will happily write whatever you ask. It will not stop you from asking the wrong thing. And because every new chat starts with no memory of the last one, the same mistake is sitting there waiting for you on Monday morning unless something in your workflow forces the decisions to happen before the code does.

A few weeks after that lost Sunday I watched an engineer named John Lindquist walk through the exact pipeline he uses to avoid this, built around five tightly-scoped skills that run in a fixed order. It clicked. I rebuilt my own workflow around the same five skills, ran it against three projects in April, and the difference was the kind of thing I'd have laughed at if someone described it to me six months ago. Less code rewritten. More features shipped. Fewer Sundays lost.

This is the pipeline. Five skills, the order they run in, what each one is actually doing under the hood, and the parts that don't show up in the demos.

<!-- IMAGE: A horizontal flow diagram showing five skill blocks left-to-right — Grill Me, Write a PRD, PRD to Issues, TDD, Improve Codebase Architecture — with arrows and a curved loop arrow from Architecture back to Grill Me. Alt text: "engineering with AI agents five-skill pipeline workflow diagram". Caption: "The five-skill pipeline — linear on the way out, cyclical on every architecture refactor." -->

## The One Mental Model That Changes How You Use Agents

Before the skills, the mental model. Skip this and the rest of the article is just commands.

The agent is not a junior engineer who will get better next sprint. The agent is a **memoryless expert** — a senior engineer with no recollection of yesterday, no knowledge of your codebase that wasn't pasted into the current context window, and no ability to ask a follow-up question three days from now. Every session is the first day on the job.

This sounds like a disadvantage. It's actually the constraint that makes the pipeline work. Because the agent has no memory, every decision has to be made explicit. Every assumption has to be surfaced. Every requirement has to be written down somewhere durable, in a file the next session will be able to read. The pipeline I'm about to walk through is, underneath, just a system for producing those durable artifacts in the right order.

There's a deeper idea behind it that comes from Frederick P. Brooks's *The Design of Design*. Brooks describes engineering as the exploration of a **design tree** — every decision branches into more decisions, and you don't really understand the design until you've walked the branches far enough to see which ones close down and which ones open up. Most failed software comes from cutting the tree off too early. You committed to a leaf before you understood the trunk.

A memoryless agent will cut the tree off at the very first branch unless something forces it to walk further. That something is the first skill in the pipeline.

## Skill 1: Grill Me — Interview the Idea Before You Code It

The Grill Me skill is the most counterintuitive thing on the list. You'd think the first move with an AI agent is to give it a prompt and watch it write code. Grill Me does the opposite. You hand the agent a rough idea — three sentences, a paragraph, a half-formed feature request — and it interviews *you*.

The interview is structured around Brooks's design tree. The agent identifies every decision point your idea implies but doesn't resolve, and walks you through them one at a time. For a short feature it might ask sixteen questions. For something complex — a multi-tenant billing system, a content pipeline with branching workflows, a new auth surface — I've watched it generate thirty to fifty questions before it's satisfied. Most of those questions are ones I should have been asking myself and wasn't.

Here's what a Grill Me session looked like on a real project last month. I told it I wanted to add "team workspaces" to a SaaS dashboard. It came back with:

1. Is a workspace owned by a single user or a team?
2. If a team, can a user belong to multiple teams simultaneously?
3. Do workspaces inherit permissions from the parent organization or define their own?
4. What happens to a workspace's resources when its owner leaves the organization?
5. Is workspace billing rolled into the organization's invoice or billed separately?

I'd been about to prompt Claude with "add team workspaces to the dashboard." If I had, the agent would have made five hidden assumptions for me, written code against those assumptions, and I'd have discovered the mismatch around question 4, in production, when somebody left a team. The grill made the assumptions visible *before* a line of code existed.

The pattern is the same every time. Most of the questions feel obvious in hindsight, which is exactly why they're worth surfacing — obvious-in-hindsight questions are the ones we skip because our brains tell us we already know the answer. The agent doesn't have that bias. It just walks the tree.

You can think of the output as a transcript, but the transcript isn't the deliverable. The deliverable is the **shared understanding** — a state where you and the agent agree on every consequential decision the feature implies. Once you have that, the next skill takes over.

## Skill 2: Write a PRD — Lock Shared Understanding Into Durable Form

The interview output dies the moment the chat window closes. That's the memoryless-agent tax I mentioned earlier. The Write a PRD skill exists to solve that problem in a single move: it converts the Grill Me transcript into a Product Requirements Document, formatted for an AI reader, and submits it as an issue to your project's issue tracker — usually a GitHub issue.

There are three things to notice about the way this skill is structured.

**First, it's written for the next agent, not for you.** A traditional PRD reads like a sales document. This one reads like a function signature with prose. Each requirement is testable. Each constraint is explicit. Each design decision from the grill is captured with the reasoning behind it. The next session will not have access to your memory of why you chose many-to-many over one-to-many — but it will have access to the PRD, and the PRD will tell it.

**Second, it lives in the issue tracker.** Not in a `docs/` folder, not in Notion, not pasted into a chat. The reason matters: the issue tracker is the place where every other tool — humans, agents, CI, downstream skills — will look for the source of truth on this feature. Putting the PRD anywhere else creates a fork in the design history that will haunt you in six weeks.

**Third, it commits you.** Once the PRD is on GitHub, you've turned a conversation into an artifact. Future-you can re-read it. Future-Claude can re-read it. A teammate joining the project next week can re-read it. The conversation, with its branching tangents and half-formed thoughts, is gone. What survives is the resolved design.

I used to dismiss PRDs as something product managers wrote to justify their salaries. Watching a memoryless agent fly through a complex feature using a tight one-page PRD as its only context input rewired that opinion completely. The PRD isn't bureaucracy. It's the agent's working memory between sessions.

## Skill 3: PRD to Issues — Vertical Slices, Not Horizontal Layers

Here's the part where most AI workflows fall apart. You have a PRD. You hand it to the agent. The agent decides to "implement the feature." Forty-five minutes later you have three hundred lines of half-built abstraction and no working software.

The fix is the PRD to Issues skill, and it borrows directly from a pattern Andy Hunt and Dave Thomas called **tracer bullets** in *The Pragmatic Programmer*. A tracer bullet is a vertical slice — a thin, end-to-end implementation that touches every layer of the system from UI to database, does one small thing all the way through, and proves the design works end-to-end before any layer is built out completely.

PRD to Issues takes the PRD and decomposes it into vertical slices, each scoped to a single tracer bullet. For the team-workspaces feature I mentioned earlier, the skill split the PRD into four issues:

1. **Issue 1 (no blockers):** Create the `workspace` model, migration, and a single endpoint that lets an authenticated user create a workspace for themselves. UI: one button. Tests: model creation, endpoint returns 201, button POSTs the right payload.
2. **Issue 2 (blocked by Issue 1):** Add multi-membership — let a user belong to multiple workspaces, with a `workspace_user` join table and a workspace-switcher in the navbar.
3. **Issue 3 (blocked by Issue 1, parallel with Issue 2):** Add the permission inheritance rules from the PRD. Pure domain logic with its own test suite.
4. **Issue 4 (blocked by Issues 2 and 3):** Add the billing rollup logic and the invoice-line generation.

Two things to notice about that decomposition. First, Issue 1 is a complete, working feature on its own — if you stopped after Issue 1, you'd have shippable software. That's the tracer-bullet promise: every slice is end-to-end usable. Second, the blocking graph is explicit. Issues 2 and 3 can run in **parallel**, which means I can spawn two agents, one for each issue, and they'll work simultaneously without stepping on each other.

That parallelism is the unlock that most teams haven't internalized yet. When you stop thinking of agents as a single worker and start thinking of them as a worker pool with explicit dependencies, the throughput of an engineering session changes shape. I wrote up the broader mental model for this in [the agent-swarm architecture for Claude Code](https://www.mejba.me/blog/claude-code-agent-swarm-architecture) — PRD to Issues is the concrete artifact that makes swarm-style work safe instead of chaotic.

The other thing PRD to Issues prevents is the worst failure mode of AI engineering: the **horizontal slice trap**. Without vertical slicing, an agent will tend to build all the models first, then all the controllers, then all the views, then all the tests. Halfway through, your branch has a complete data layer that nothing uses, a UI mocked against the wrong contract, and zero working software. With vertical slicing, you always have a working system; you just have a smaller working system than the final feature.

<!-- IMAGE: A four-cell diagram showing Issue 1 as a green vertical bar spanning UI/API/DB layers, Issue 2 and Issue 3 as parallel vertical bars to the right, and Issue 4 as a wider final bar. Alt text: "vertical slice tracer bullet issue decomposition AI agents". Caption: "Four vertical slices, two of them parallel — the shape that lets agents work without stepping on each other." -->

## Skill 4: TDD — Red, Green, Refactor (And Why the Refactor Hurts)

Now you have the PRD, you have the issues, and you're ready to write code. This is where the TDD skill takes over, and where the pipeline starts revealing how AI agents actually behave under pressure.

The TDD skill runs the classic red/green/refactor loop, but adapted for agents:

1. **Red:** The agent writes a failing test for the next behavior in the current issue. Run the test. Confirm it fails for the right reason. (This sub-step matters — agents will sometimes write a test that fails for the wrong reason, and you'll find out two hours later when "passing" doesn't mean what you thought.)
2. **Green:** The agent writes the minimum code required to make the test pass. Run the test. Confirm it passes.
3. **Refactor:** The agent improves the code without changing behavior. Run the test. Confirm it still passes.

Steps 1 and 2 work beautifully with agents. Claude is excellent at writing focused tests, equally excellent at writing minimal implementations to make those tests pass. The first time I watched it tear through a Laravel service class in twenty minutes, with every method covered by a test that was written before the method existed, I genuinely felt like I'd been doing my job wrong for a decade.

Step 3 is where the trouble lives.

Here's the honest part of this whole article. AI agents are **deeply reluctant to refactor their own code** inside the same context. The pattern looks like this: you write a green test, the agent writes the minimum code, you tell it to refactor, and it returns the same code with a comment that says "this is already well-structured." It's not lying. From its own context, the code does look fine. The problem is that the agent has been staring at its own logic for an hour and has lost the perspective required to see the smell.

The workaround that's working for me — and that the TDD skill bakes in — is to break the refactor step out into a fresh agent session. New context window. No history of writing the original code. You hand the new session the failing-then-passing test and the green implementation, and you ask it to refactor. With the original-author bias absent, it's willing to rip the code apart. The test gives it a contract to refactor against. The refactor finishes. You commit.

This is the moment a lot of people miss when they say "AI can't really do TDD." It can — but only if you treat each phase of the red/green/refactor loop as a separately scoped agent session, not a single conversation. The skill enforces that scoping. The principle is the same one I covered in [why precise prompting beats clever prompting](https://www.mejba.me/blog/ai-prompting-rules-reduce-guessing) — the agent is only as good as the context you give it, and that includes the context you *don't* give it.

There's a second subtle thing the TDD skill handles. It refuses to let the agent skip the red step. If you let an agent write a test against existing code, it will write a test that passes on the first run — and you'll have no idea whether the test is actually exercising the behavior you care about. The skill blocks this by enforcing the failing-first state. You don't move forward until the test fails for a reason you can articulate.

## Skill 5: Improve Codebase Architecture — When the Pipeline Loops Back

The four skills above will get you a working feature. They will not, by themselves, prevent your codebase from rotting. Over five or ten features, you'll accumulate the kind of structural debt that doesn't show up in any individual PR but slowly makes every future feature harder to build. The fifth skill is what catches that drift.

Improve Codebase Architecture is different from the others. It doesn't run inside the linear pipeline — it runs **periodically**, between feature cycles, as a cleanup pass. Its job is to look at the current codebase and propose deliberate refactors, then turn the best ones into new issues that re-enter the pipeline at the top.

The way it works is unlike anything in the rest of the workflow. The skill spawns three or more parallel sub-agents, each prompted to propose a radically different interface design for the area of the codebase being refactored. Three sub-agents because one would just confirm the status quo and two would settle into a binary — three is the smallest number that forces genuine alternatives to surface.

I ran this last month on a content-pipeline module that had grown messy. The three sub-agents came back with:

- **Proposal A:** A pure functional pipeline of composable steps, each a pure function over a typed payload.
- **Proposal B:** An event-driven design with a queue and a set of independent consumers.
- **Proposal C:** A traditional service-class hierarchy with a base class and three concrete strategies.

The parent agent then evaluated all three against the actual constraints of the codebase — test coverage, deployment shape, the existing data model — and recommended **a hybrid of A and B**: pure functional steps inside, dispatched onto the existing queue infrastructure. None of the three pure proposals was the right answer. The hybrid was. And I would never have arrived at the hybrid on my own, because my brain had been anchored to the existing service-class shape for months.

The output of the skill is an RFC, also filed as a GitHub issue. The RFC describes the proposed refactor, the alternatives that were considered and rejected, the reasoning for the recommendation, and the suggested incremental migration path. Once the RFC is approved, it re-enters the pipeline at step 1 — you Grill Me the refactor proposal, write a PRD for it, decompose into issues, TDD it.

That cycle is the part that's hardest to communicate without seeing it run. The pipeline isn't really linear. It's a loop. Features go through 1→2→3→4. Periodic architecture refinements go through 5→1→2→3→4. Over a quarter, the codebase compounds in a direction you chose rather than drifting in a direction it found.

## The Timeline — How One Real Feature Moves Through the Pipeline

Let me walk through what this actually looks like in time, for the team-workspaces feature I keep referencing, so the abstraction has shape.

**Monday morning, 45 minutes.** I run Grill Me on the rough idea. Twenty-two questions. I answer them. By the end of the session, the design tree has been walked far enough that I can describe the feature in a single paragraph without hedging.

**Monday morning, 20 minutes.** I run Write a PRD. The skill generates the document from the grill transcript, I review it, edit two sentences, and submit it as a GitHub issue. Issue #341.

**Monday morning, 15 minutes.** I run PRD to Issues. It decomposes #341 into the four sub-issues I described above, with the blocking graph attached. Issues #342, #343, #344, #345.

**Monday afternoon through Tuesday.** I run TDD on issue #342 (the foundational slice). About four hours total. Red, green, refactor on each behavior. I keep the refactor step in a separate context window. Issue closes.

**Wednesday.** I spawn two parallel TDD sessions, one on #343 and one on #344. They're independent issues by design. About three hours each, running in parallel windows. Both close.

**Thursday morning.** I run TDD on #345, the billing rollup. About five hours, because the test surface is wider. Closes by end of day.

**Friday.** I run Improve Codebase Architecture against the workspace module. The three sub-agents propose three structures. The parent recommends a small refactor to extract the permission-checking logic into a pure module. RFC filed as issue #346. I decide it's worth doing, run the loop again, and ship the refactor by Friday afternoon.

Total elapsed: about thirty hours of focused work over five days, for a feature I would have estimated at two weeks before I started running this pipeline. The reason it compressed isn't that the agents are faster than me at typing. They are — but typing was never the bottleneck. The reason is that the pipeline removed almost all of the rework cycles that used to eat the middle three days of the week.

## The Honest Part — Where This Pipeline Falls Apart

I've been telling you what works. The article doesn't earn its trust unless I also tell you where it breaks.

**The pipeline assumes you can write good answers during Grill Me.** If you can't articulate what you actually want, the questions just expose the gap. The skill is not a substitute for thinking — it's a forcing function for thinking. I've seen developers try to use Grill Me as a way to "figure out what they want" mid-session, and the result is a transcript full of "I don't know, what do you think?" which produces a PRD that's effectively the agent's preferences, dressed up as the user's. That PRD will then be the contract every downstream agent is held to, and you'll discover at issue #4 that the agent's preferences were not yours.

**The TDD refactor problem doesn't fully resolve, even with a fresh session.** Sometimes the new agent will look at the code and produce a marginal cleanup that misses the deeper structural issue. The pattern I've landed on: I run the refactor twice, in two separate fresh sessions, and look at both. If they agree, I commit. If they disagree, the disagreement is usually where the real refactor lives, and I'll either pick the better of the two or hand both back to a third agent to reconcile. This is slower than I'd like.

**Parallel issues are not actually free.** When I run two TDD sessions in parallel on independent issues, I'm splitting my own attention — and that's the real cost, not the agent compute. The blocking graph in PRD to Issues prevents the agents from interfering with each other on the code, but it can't prevent me from context-switching badly. I cap myself at two parallel sessions for that reason. Three breaks me.

**The architecture skill can over-refactor.** The first time I ran Improve Codebase Architecture against a healthy module, it generated three serious refactor proposals for code that didn't need refactoring. The skill is biased toward finding work. You're the brake. If none of the three proposals make the codebase materially better, the right answer is to skip the refactor and re-run the skill in two months. Discipline matters more here than in any other step.

**Skill length is not skill quality.** The two best skills in this pipeline — Grill Me and PRD to Issues — are short. Maybe a hundred lines each. The skill writers I've seen produce the worst results are the ones who treat skill length as a proxy for thoroughness. Precision in *when* a skill triggers and *what* it produces matters far more than how much instruction text it contains. I covered this pattern in [how Claude skills actually run a workflow](https://www.mejba.me/blog/nine-claude-skills-advanced-workflow) — the same principle applies here, doubled.

## How This Reshapes What an Engineer Does All Day

Step back from the five skills for a minute. The shape of work the pipeline produces is genuinely different from what I was doing in 2024, and it's worth saying out loud what changed.

I write less code. I write more **contracts**. The PRD is a contract. The issues are contracts. The tests are contracts. The RFCs are contracts. The actual implementation is increasingly something an agent produces against a contract I authored, and my time-spent-per-feature has shifted heavily toward the contract-writing end.

I think more in **decision trees**, less in syntax. The first three skills in the pipeline are all about resolving the design tree before any code exists. The two after that are about executing cleanly against the resolved tree. Once I noticed that pattern, my engineering habits outside the pipeline started shifting too — I'll catch myself, mid-conversation about a feature, asking the kind of questions Grill Me asks, before any tool runs.

I rely on **parallelism** in a way I never used to. Two simultaneous TDD sessions on independent issues sounds like a productivity hack. It's actually a different mode of engineering. The mental skill it requires is the ability to design the blocking graph correctly upstream — if you mis-decompose the issues, parallel sessions collide. If you decompose them well, parallelism is almost free. The pipeline punishes bad decomposition and rewards good decomposition, which is a feedback loop I never had before.

I treat **memory** as an artifact, not an assumption. Every important decision lives in a place future-Claude can read it. The PRD, the issues, the test names, the commit messages, the RFCs — all of it is structured as if a memoryless senior engineer might land on the repo tomorrow with no context, because in practice, that's exactly what happens every time I open a fresh agent session.

If you want a deeper look at how the underlying skills system enables this kind of compounding, the [agent-skills architecture breakdown](https://www.mejba.me/blog/claude-code-agent-skills-guide) is the post I'd point you at next. The pipeline I'm describing here is what skills look like when you wire them together with intent.

## The Course I'm Actually Watching

I'd be dishonest if I didn't mention where most of these patterns surfaced for me. Matt Pocock — the engineer behind a lot of the TypeScript education I've quietly relied on for years — ran a two-week cohort called **Claude Code for Real Engineers** from March 30 through April 13, 2026, and the curriculum maps almost exactly to the pipeline I just described. Plan/Execute/Clear protocol. Tracer-bullet implementation. PRD writing for AI readers. The autonomous-loop pattern at the end of week two.

The course is $795 on AI Hero. I'm not affiliated, I'm not getting a kickback, and I haven't enrolled in the live cohort myself — the on-demand version is what's available now, and I'm evaluating whether the structure justifies the cost given that most of the patterns are public and reproducible if you're willing to assemble them. The honest take: if you want to compress the learning curve from months to weeks and you'd rather not piece together the five-skill pipeline from blog posts like this one, the cohort is a reasonable bet. If you're patient and the pipeline above gives you enough to run with, you can probably get there yourself.

What I will say is that the *category* this course occupies — structured engineering practice around AI agents, as opposed to "tips and tricks" content — is the one that's going to matter most over the next two years. The marketplace is going to bifurcate into engineers who treat AI as a typing accelerator and engineers who treat it as a memoryless collaborator that needs a real pipeline. The second group is going to ship circles around the first.

## Beyond the Five Skills — Where I'm Pushing Next

The pipeline as it stands is solid. It's not where I want it to end up.

The thing I'm experimenting with now is **autonomous agent integration** at the edges. Specifically: a scheduled agent that re-runs Improve Codebase Architecture against the repo every two weeks, files RFCs as issues, and tags me for review. I don't have to remember to run the skill. The skill runs itself. The RFCs land in my queue. I either approve and re-enter the pipeline, or I close as wontfix. The architecture-drift problem becomes a passive checkbox instead of an active habit.

The other piece is **context window discipline** across the pipeline. Every step produces an artifact that becomes the input context for the next step. If the artifact is too verbose, the next step's context fills up and the agent gets dumber. The art is making each artifact as tight as it can be while still being a complete contract. I've been trimming my PRD template over the last month, and the downstream code quality has measurably improved every time I cut a paragraph that wasn't pulling its weight. That principle — the idea that less precisely-chosen context beats more loosely-curated context — is the single biggest lever in **engineering with AI agents** that nobody talks about in the tutorials.

The third piece is **language-agnostic application**. I've been running this pipeline against Laravel, against Next.js, against a Python data pipeline, and against a Bash-heavy infrastructure repo. It works on all four. The skills don't know what language they're operating on; they operate on the design tree, the PRD, the issues, the tests, and the architectural patterns. That language-agnosticism is the property that makes me think this pipeline is the right one to invest in for the next two years, regardless of what stack I end up working in.

## What to Do Before Monday Morning

If you've read this far and you're sold on the idea, here's the order I'd suggest you try it in. Don't install all five skills at once. The pipeline is sequential for a reason, and trying to adopt it as a Big Bang will overwhelm whatever workflow you currently have.

1. **This week:** Install Grill Me. Use it on the next feature you'd otherwise just prompt Claude to write. Notice the questions it asks. Notice which ones you couldn't have answered without it. That's the value.
2. **Next week:** Add Write a PRD. Pipe the Grill Me output through it. File the PRD as a GitHub issue. Get used to the artifact living somewhere durable.
3. **The week after:** Add PRD to Issues. Watch your features decompose into tracer bullets. Learn to spot good vertical slices from bad ones — bad ones come back as a single issue that won't close.
4. **Week four:** Add TDD. Be ready for the refactor-step friction. Solve it by running the refactor in a separate session.
5. **Week six:** Add Improve Codebase Architecture. Run it once. See if you trust the output enough to act on it.

By week eight you'll have a working pipeline. By week twelve you'll be designing your own variations. By month six the workflow will feel as natural as the workflow you had before agents existed, except faster, more deliberate, and harder to break.

The Sunday I lost in Laravel was the one I needed to lose. It was the cost of learning, in my bones, that **engineering with AI agents** is not about typing prompts and watching code appear. It's about producing the right artifacts in the right order so that a memoryless expert can pick up the work at any point and continue it without losing the design. The pipeline is how you make that possible.

I haven't lost a Sunday like that one since.

## Frequently Asked Questions

### What does "engineering with AI agents" actually mean in practice?
Engineering with AI agents means treating the agent as a memoryless senior engineer who needs every decision, requirement, and constraint captured as a durable artifact before code is written. In practice that's a fixed pipeline — Grill Me, PRD, Issues, TDD, Architecture Refactor — where each step produces the context the next step depends on. For the full walkthrough, see the pipeline sections above.

### Do these skills only work in Claude Code?
The five skills are language-agnostic and concept-agnostic — the underlying patterns (design tree, vertical slices, red/green/refactor, parallel sub-agents) work with any agent runtime that supports skills or equivalent scoped instructions. Claude Code is where I run them because the skills system there is the most mature, but the same pipeline can be reproduced on other harnesses with custom slash commands or system prompts.

### Why do AI agents refuse to refactor their own code?
Agents struggle to refactor inside the same context because they've been reasoning about the code for the entire session and lose the perspective required to see the smell. The fix is to open a fresh agent session with no prior context, hand it only the failing-then-passing test and the green implementation, and ask it to refactor against the test contract. The principle is covered in the TDD section above.

### What's a "vertical slice" or "tracer bullet" in this context?
A vertical slice is an end-to-end implementation that touches every layer of the system — UI, API, domain logic, database — but does only one small thing all the way through. It originated as the "tracer bullet" pattern in *The Pragmatic Programmer* and is the unit of work that PRD to Issues decomposes a feature into. The result is that every issue you close ships working software, not a half-built layer.

### Is the Claude Code for Real Engineers course worth $795?
The cohort covers the same patterns described in this article — Plan/Execute/Clear, PRD writing for AI readers, tracer-bullet implementation, autonomous loops — in a structured two-week format. It's worth the cost if you want to compress the learning curve and prefer guided instruction over assembling the pipeline from public sources. The on-demand version is what's currently available; live cohorts are periodically re-run.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
