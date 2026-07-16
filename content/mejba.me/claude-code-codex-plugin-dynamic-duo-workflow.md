**BRAND:** mejba.me
**TITLE:** Claude Code + Codex: The Dynamic Duo Workflow That Ships
**META TITLE:** Claude Code + Codex Plugin: Dynamic Duo Workflow (2026)
**SLUG:** claude-code-codex-plugin-dynamic-duo-workflow
**PRIMARY KEYWORD:** Claude Code Codex plugin
**META DESCRIPTION:** I run Claude Code and Codex together via the native Codex plugin. Plan with Claude, review with Codex, ship cleaner. Real commands, real workflows, real bills.
**TAGS:** Claude Code, OpenAI Codex, AI Coding Agents, Vibe Coding, AI Workflows

---

I almost didn't install it. The Codex plugin notification hit Claude Code on a Friday afternoon, and my immediate reaction was the same one most engineers have when a competitor's tool shows up inside their primary tool: "this is going to be a janky bridge that breaks every other release." I had a Laravel deployment to babysit and three blog posts queued through the agent stack. Plugin installs were not on the list.

Then Saturday morning I watched Claude Code happily plan a five-table schema migration, ship the migration file, and miss a foreign-key constraint that would have nuked staging on Monday. Not because Claude is bad. Because Claude was in "build mode," not "skeptic mode." That's the gap the [Codex plugin for Claude Code](https://github.com/openai/codex-plugin-cc) is designed to close, and that Saturday is the day I stopped picking sides in the model debate.

I've been running both models in tandem for six weeks now. Claude Code on Opus 4.7 as the primary builder. Codex on GPT 5.5 as the resident skeptic. Different roles, same terminal, one workflow. The cost math works. The output quality jumped in a way I didn't expect. And the dumb model-tribal arguments I was wasting energy on dissolved the moment the first `/codex:review` came back with three issues I would have shipped without thinking twice.

This is the workflow. The actual commands, the actual handoffs, the actual settings I run on real client builds and on my own multi-brand content stack. Stay with me through pattern three — that's where the cost savings flip from "nice" to "this changes my plan tier next month."

## Why The Single-Model Debate Is The Wrong Question

The AI Twitter loop has been stuck on the same argument for two years: which coding model is the best one. GPT 5.5 vs Opus 4.7. Codex vs Claude Code. Cursor vs Windsurf vs Zed. Pick a tribe, defend it on timeline, repeat next release cycle.

Here's what I noticed once I stopped playing that game. The two leading models have genuinely different shapes. Claude Code, especially on Opus 4.7, is a creative generalist. It plans well across architecture, copy, design tokens, and product flow. It writes confidently. It hallucinates confidently too — which is the trade-off you sign up for when you want a model that ships rather than stalls. I covered the model-level differences in [GPT 5.5 vs Opus 4.7 tested on real coding builds](https://www.mejba.me/gpt-5-5-vs-opus-4-7-comparison) and in [my hands-on review of GPT 5.5 inside Codex](https://www.mejba.me/gpt-5-5-codex-hands-on-review). The short version: they're complements, not substitutes.

Codex on GPT 5.5 is a different animal. It's token-stingy. It's surgical. It loves a focused diff. Ask it to refactor a single function and it produces a clean patch with reasoning about edge cases. Ask it to plan a SaaS dashboard from scratch and it writes shorter, less ambitious scaffolding than Claude does. Neither of those is a flaw — they're personality.

The dynamic duo idea is straightforward: stop asking which one is "better" and start asking which one to put in which seat. Builder vs reviewer. Drafter vs editor. Optimist vs skeptic. The native Codex plugin makes that handoff fast enough that you actually do it, instead of swearing you'll do it and never opening the second tool.

There's a mental shift here that takes a week or two to settle. You stop thinking of "the AI" as a single thing you hand work to. You start thinking of "the team" — two specialists who disagree productively, with you adjudicating. That framing alone changed how much code I shipped clean on the first deploy.

But before any of that works, you need the plugin installed and the right slash commands at your fingertips. That's where most of the early adoption pain happens.

## Installing The Codex Plugin (The Two-Minute Version)

The plugin is published by OpenAI directly. That matters because it sets the support model — this isn't a community bridge that breaks every other Claude Code release. It's an officially maintained integration that gets updated on both ends.

Installation is plugin-marketplace style. Inside Claude Code, you add the OpenAI marketplace, then install the codex plugin from it. The [Codex plugin GitHub repo](https://github.com/openai/codex-plugin-cc) has the current command, but the gist is two lines of `/plugin marketplace add` and `/plugin install`. After that you've got a fresh set of slash commands sitting under the `codex:` namespace.

The first thing I'd do is run `/codex:setup` to confirm the local Codex CLI is reachable. The plugin shells out to the Codex CLI under the hood — it's not a standalone API client, it's a wrapper that pipes Claude Code's context into Codex sessions and pipes the results back. If your Codex CLI isn't logged in or isn't on the right plan, the plugin will tell you, and you'll fix it once.

A note on plans, because this is where most people get the math wrong. Claude Code lives on the Anthropic plan you already pay for. Codex lives on its own OpenAI plan. The combo is what makes the duo cost-effective, and I'll get into that pricing math in a few sections — but you do need both, and you do need them on tiers that fit how aggressively you're going to use each one.

The slash command surface area you actually use day to day is small. The two big ones are `/codex:review` for inline code review on whatever's in your current Claude Code context, and `/codex:rescue` for full-codebase audits or delegated tasks running in the background. Around those sit `/codex:status`, `/codex:result`, and `/codex:cancel` for managing async runs. There's also a setup-level review gate flag — `/codex:setup --enable-review-gate` — that turns Codex into a Stop-hook reviewer on every Claude turn. That last one is powerful, expensive, and the one I'll tell you exactly when to flip on.

That's the inventory. Now the actual workflows.

## Workflow 1: The Adversarial Planning Loop

This is the pattern I run most often, and the one that produces the largest token savings downstream.

The setup is simple. Claude Code drafts the implementation plan first — schema, modules, contract surfaces, sequence of changes. I let it go long here. A 30-minute to 90-minute planning session with Claude in plan mode produces a dense markdown brief with specific file paths, function signatures, and migration steps. This is Claude's home turf. It's good at architecture and at narrative explanation of why the architecture is shaped that way.

Then, before I write a line of implementation, I trigger Codex. The native command is `/codex:review` against the plan document, but in practice I prompt it adversarially: "review this plan as if you're the engineer who'll inherit this code in six months and is hostile to the original author." That framing matters. A polite review gives you cosmetic feedback. An adversarial framing gives you the bugs you actually care about.

What comes back is the kind of feedback you'd pay a senior engineer for. On the Laravel migration I mentioned at the top, Codex flagged the missing foreign-key constraint, a soft-delete column that was on the parent table but missing on the child, and a unique index that would have triggered a deadlock under concurrent writes. None of those would have been visible from the migration file alone — Codex inferred them from the broader schema and the controller logic that touched the same tables.

The loop then runs: Claude updates the plan, Codex reviews again, Claude updates again. Two rounds is usually enough. Three rounds means the plan needed to be rewritten from a different angle and Claude was patching around the original mistake. When I see a third round needed, I scrap the plan and restart with a different decomposition.

Here's the cost insight that took me a month to internalize. Adversarial planning sessions burn tokens, but they burn cheap tokens — Codex review on a markdown plan is a few thousand input tokens and a few thousand output tokens. Implementation, by contrast, is the expensive phase. Every bug you catch in planning is a bug you don't pay to write, debug, and fix in code. My rough internal benchmark on a typical feature build: 60 to 90 minutes of adversarial planning saves something on the order of 3 to 5 hours of implementation iteration. The token math follows the same shape.

The bridge to the next pattern is this: planning loops are great for greenfield work, but most days you're not greenfield. You're inside an existing codebase that already has its own opinions, and you need a different shape of help.

## Workflow 2: Incremental Build With Background Codex Audits

This is the pattern I use on existing client codebases — Laravel apps, agency SaaS dashboards, the content stack at [Ramlit](https://www.ramlit.com), all of it.

The structure: Claude Code does the active building in the foreground. I'm in flow. Codex runs `/codex:rescue` audits in the background on whatever subsystem I'm currently touching.

Background mode is the unlock here. When you fire `/codex:rescue` with a non-trivial scope, the plugin kicks the run off as a background job and returns control to Claude Code immediately. You keep building. Some minutes later — usually somewhere between three and twelve, depending on the scope — the audit completes. You check `/codex:status` to see what's done, then `/codex:result` to read the findings.

The crucial behavior: this doesn't break your flow. The number-one productivity killer in agentic coding isn't bad model output, it's context-switch tax. Every time you stop building to review, you pay a switching cost on the way out and another one on the way back in. Background Codex audits eliminate that entirely. You're still building when the review is happening.

The shape of the audits I run most often: security review on any module that handles auth or input parsing, scalability review on any controller that touches a write-heavy table, and code-hygiene review on anything I expect to hand off to another developer. Each of those is a parameterized rescue request — you tell Codex what you want it to look for, scope it to the relevant directory, and let it grind.

A practical note on context size. The plugin pipes the relevant working set into Codex, but for full-codebase audits you sometimes need to pass it scope hints — which directories matter, which to ignore, which packages are vendored. Codex on GPT 5.5's 1M context window swallows medium codebases whole, but on larger monorepos you still want to scope. I usually point it at the directory I'm actively working in plus the directly adjacent dependencies (the models a controller touches, the migrations a model touches), and skip everything else.

If you'd rather have a team that runs this entire dual-agent setup as a service for your codebase, this is exactly the kind of engagement [Ramlit](https://www.ramlit.com/services) takes on for clients running production Laravel and Node stacks — same patterns, different scale.

The next pattern is the one that turns this from a nice workflow into a genuine production-quality gate.

## Workflow 3: Pre-Release Final Audit

There's a reason senior engineers ship cleaner code than juniors, and it isn't that they write fewer bugs in their first pass. It's that they have a final-pass habit baked in — the moment of stopping, scrolling back to the top of the diff, and reading it as if they'd never written it. Most AI coding workflows skip that step entirely because the model just keeps going.

The pre-release audit pattern restores it.

The mechanic: the day before deploy, or the moment a feature branch is functionally complete, fire `/codex:rescue` against the entire diff with a security-and-hygiene focus. I phrase the request as "review this branch as if you're the on-call engineer who'll get paged if anything in this PR breaks production." Same adversarial framing as the planning loop, applied to finished code.

The findings I get from this final pass tend to fall into four buckets. First — security holes Claude wouldn't flag because Claude wrote them and felt good about them. Cross-site request forgery gaps, missing authorization checks on routes that look like read endpoints but actually mutate state, log statements that quietly print user-supplied input. Second — privacy leaks in error messages. Stack traces returning database column names. Validation messages that confirm whether an email exists in the system. Third — performance footguns. N+1 queries inside a Blade loop, a JSON column being deserialized inside a tight inner loop, an Eloquent relation that's set to lazy-load when it should be eager. Fourth — hygiene. Dead code, comments that contradict the implementation, function names that lie about what the function does.

None of these are catastrophic on their own. All of them are the kind of stuff that a year from now, when you're trying to read your own codebase, makes you want to set the laptop on fire.

There's a configuration choice that matters here. You can run the pre-release audit as a one-shot rescue, or you can flip the review gate on with `/codex:setup --enable-review-gate` and have Codex Stop-hook every Claude response on the branch. The Stop-hook approach is more aggressive — Codex reviews each Claude turn, blocks the stop if it finds issues, and forces Claude to address them before you can move on. That's the `/codex-auto-review` continuous-loop pattern in spirit. It's also the most token-expensive mode in the plugin. I only flip it on for the last 24 hours before a production deploy on sensitive code paths, and I flip it off the moment the deploy lands. The cost of leaving it on full-time would melt my plan limits inside a week.

This is also the pattern that pairs well with [the agent skills stack I built around Claude Code](https://www.mejba.me/claude-code-skills-stack-bookz-ai-senior-engineer) — pre-release audits, review gates, and skill-driven hooks all sit at the same architectural layer.

## Workflow 4: Delegated Execution When Claude Hits A Wall

Most of the time the duo is Claude-builds, Codex-reviews. But there are domains where Claude struggles in ways that aren't worth fighting. When that happens, you flip the polarity: Claude becomes the planner and Codex becomes the executor.

The most common case for me is anything Python-heavy that needs careful type-handling — async generators, complex dataclass hierarchies, anything where the type system's quirks matter. Claude Code on Opus 4.7 is fully capable here, but I've noticed that on tasks where the failure mode is subtle type-mismatches that compile but break at runtime, Codex on GPT 5.5 produces tighter first-pass code. Maybe it's the tokenizer efficiency. Maybe it's just where GPT 5.5's training corpus lives. I don't have a clean explanation, only the pattern.

The delegation mechanic is `/codex:rescue` with an explicit "implement, don't just review" framing. The plugin lets you delegate work and run it as a background job — you fire the request, you keep working in Claude Code on a different module, and you check back when Codex returns the implementation. The handoff back into the main Claude Code session happens through `/codex:result`, which dumps the diff into your working context where Claude can pick it up, review it, and integrate it.

There's a discipline that matters here. Don't delegate work to Codex that you can't review yourself. The whole point of dual-agent coding is that you're the adjudicator — when both models agree, you ship; when they disagree, you decide. If you delegate something so far outside your expertise that you can't tell whether the result is correct, you're back to single-model trust, except now you're trusting the model you delegated to without having anything to compare against. That's strictly worse than not delegating at all.

The cases where delegation works cleanly are domains where you understand the problem well enough to spot a wrong answer, but the implementation grunt work isn't where you want to spend your morning. That's a much smaller surface than "any task Claude is mediocre at," and the discipline of staying inside that smaller surface is what keeps the workflow honest.

## Workflow 5: The Continuous Loop For Sensitive Builds

This is the pattern I reach for maybe one build in ten — the configuration where every part of the previous four workflows runs simultaneously on the same branch.

Setup looks like this. Plan mode in Claude Code, paired with adversarial Codex review on the plan. As soon as the plan stabilizes, implementation begins. Background `/codex:rescue` audits run continuously on the modules being built. The review gate is on, so Codex Stop-hooks every Claude response. At the end of each working day, a final `/codex:rescue` runs against the full diff. The next morning starts with reading the overnight audit results and deciding what gets fixed before new building starts.

This is the maximum-paranoia configuration. It's slow. It's expensive. And on the right kind of build, it's worth it.

The right kind of build is anything where shipping a bug has real cost beyond "I have to push a hotfix." Payment flows. Authentication systems. Anything touching customer data. Migrations that aren't trivially reversible. Compliance-relevant code paths.

I ran this configuration on a HIPAA-adjacent client build last month — a healthcare CRM where the audit log behavior had to be provably correct. The continuous-loop config caught two things that would have made it to production otherwise: a session-token leak in error responses, and a retention-window calculation that was off by a day-of-week boundary. Either of those would have been a regulatory mess. Both came out of Codex review passes that Claude wouldn't have flagged on its own, because Claude wrote them and considered them done.

Outside of those high-stakes cases, the continuous loop is overkill. The token bill alone makes it untenable for routine work. But knowing when to flip it on — and being disciplined about flipping it back off — is the thing that separates "engineers who use AI" from "engineers who use AI well."

## A Real Demo: The URL Shortener Build

Let me put it on a concrete project so the abstraction lands.

I built a URL shortener — the kind of "small SaaS project" that's actually full of edge cases. Bitly-style. Custom slugs, expiration dates, click analytics, rate limiting, the works. Stack: Next.js 15 frontend, Postgres backend, deployed on a $20-a-month VPS.

Claude Code did the initial scaffold. Three hours, single Opus 4.7 session, clean output. App ran on first deploy. The auth worked. The shortening worked. The analytics dashboard rendered. By any reasonable measure, the build was done.

Then I ran `/codex:rescue` on the whole codebase, scoped to "find me the things that will hurt me when this hits real traffic."

What came back was uncomfortable. Codex flagged six issues. The slug generation was using `Math.random()` for short codes, which is fine until you start hitting collisions in the shortlink table — at which point the retry logic was a tight loop with no backoff. The expiration-date handling assumed UTC everywhere but the input form accepted local time without conversion, which meant any link expiring "today" could resolve as already-expired depending on which side of midnight the user lived in. The rate limiter was per-IP but there was no upstream proxy header check, so a single Cloudflare IP could exhaust the bucket for everyone behind it. And so on.

Then I ran `/codex:rescue` again, this time scoped specifically to the link-expiration feature with adversarial framing — "challenge this design from the perspective of someone trying to break it." Codex came back with edge cases I hadn't even mentally modeled: timezone offsets on the expiration check, links expiring exactly at midnight on the boundary day, the question of what "expired" means when the click and the check happen 30 seconds apart across a daylight-saving transition.

None of those would have shipped clean from Claude alone. None of those would have surfaced from a single human review either, because human reviewers tend to focus on the code that's there, not the code that's missing. Codex's adversarial-review mode is structurally good at finding the missing code — the validation that should have been there, the case that should have been handled, the assumption that should have been challenged.

The shortener went to production with all six issues fixed. Cost of the dual-agent run: a few dollars in Codex tokens against the Anthropic plan I was already paying for. Cost if I'd shipped the original Claude-only build and dealt with the bugs in production: at least a weekend of patching, possibly a security incident, almost certainly a customer support fire.

That math is the whole pitch.

## The Pricing And Plan Math

Here's where the dual-agent setup actually makes financial sense, because the obvious worry is that running two paid AI tiers doubles your bill. It doesn't — if you set the plans up right.

My current setup: Claude Code on the $100-a-month Anthropic plan, with Opus 4.7 set as the default model. That's the workhorse. Most of the actual code generation, planning, and conversation happens here, and the higher tier is justified because Opus runs are the expensive ones.

Codex sits on the $20-a-month OpenAI plan. The plugin uses Codex selectively — for review passes, background audits, occasional delegated execution. Token consumption on the Codex side is meaningfully lower than on the Claude side, because Codex isn't doing the heavy generation. It's doing critique. Critique is cheaper than creation. The $20 plan handles the volume comfortably for a one-developer workflow on a healthy build cadence.

The combined monthly spend is $120. Compared to the $100 Claude-only setup I was running before, that's a 20 percent increase in tooling cost. The output quality jumped enough that on a single client engagement the math more than pays back — one production bug that doesn't ship is worth more than a year of the differential.

There's a configuration to be careful about: the review gate, when left on, can chew through Codex tokens faster than the $20 plan can comfortably absorb. If you're running review-gate-on as your default mode, you'll need a higher OpenAI tier. I keep review gate off by default and flip it on only for the final 24 hours before a sensitive deploy. That keeps the plan math honest.

The contrarian implication: you should not run Codex on a higher plan than Claude unless your workflow is inverted (Codex doing primary generation, Claude doing review). For most builds, Claude is the heavy generator and Codex is the surgical reviewer, and the plan tiers should reflect that asymmetry.

## What This Means For Non-Engineers

If you build content stacks, agent workflows, or operations automation rather than production code, the dual-agent pattern still applies — and I run it on my own multi-brand content pipeline, which is what most of [mejba.me](https://www.mejba.me) and the brand family runs through.

The shape is the same. Claude drafts the agent prompt, the workflow definition, the content generation logic. Codex reviews the prompt for ambiguity, the workflow for failure modes, the content logic for edge cases. When I'm building a new content agent, I usually ask Claude to write the system prompt, then ask Codex to find every way that prompt could be misread. The number of latent bugs that surface is consistently higher than I expect.

There's a related pattern in how I run the agent-team scaffolding: planning skills in Claude Code, executor skills delegated to Codex when the task wants surgical precision. I covered the foundational skills layer in [my piece on the top Claude Code skills for business efficiency](https://www.mejba.me/top-claude-code-skills-business-efficiency), and the dual-agent extension of that stack is where the workflow lives now.

The principle that holds across both engineering and ops work: don't ask one model to be the builder and the critic. The job descriptions are different. The cognitive frames are different. The outputs you get from a model in "build mode" are structurally different from the outputs you get from the same model in "review mode." Splitting the role across two specialists with two different personalities produces strictly better results than asking a single specialist to wear both hats.

## The Limits Worth Knowing About

A workflow this good needs the honest disclaimer.

First, the plugin is still young. It shipped recently and the slash command surface is going to evolve. The exact command names and flags I'm using today will probably get refactored in the next few months. Treat the [official Codex plugin repo](https://github.com/openai/codex-plugin-cc) as your source of truth for current syntax — what I've described here is the pattern, not necessarily the exact incantation forever.

Second, the two models will sometimes disagree in ways that aren't productive. You'll get cases where Codex flags an issue that Claude correctly built around, or where Claude implements something Codex would have built differently but neither version is wrong. The workflow assumes you, the human, are the adjudicator. If you can't tell which of the two models is correct on a given disagreement, the dual-agent pattern degrades into "two AIs argue while you watch." Pick the disagreements you can actually resolve.

Third, there's a real risk of decision fatigue. Running adversarial reviews on everything turns every feature into a debate. The pattern works because the reviews are scoped — adversarial planning at the start, audits at the end, normal reviews on demand in the middle. If you flip review-gate-on as your default mode and never turn it off, the friction starts compounding and the productivity gain reverses. Discipline about when each pattern fires is the difference between "this is the workflow" and "this is the reason I quit using AI."

Fourth, costs can sneak up. The plan math I described is what I observe on my workload — one engineer, multiple brands, a few dozen builds a month. If you're running it across a team, or if you're building constantly, you'll need to track token consumption directly rather than trusting that the plan tiers will absorb it. The plugin's `/codex:status` and `/codex:result` are observability tools as much as workflow tools. Use them to keep the bill visible.

Fifth, this isn't a substitute for code review by a human who actually understands your domain. Both models are pattern-matchers operating on training data. They're catching the kind of bugs they've seen before. A novel architectural mistake — something that's wrong in a way nobody has documented — will get past both of them. The dual-agent pattern raises your floor; it doesn't raise your ceiling. Senior human review still matters, especially on the calls that aren't in the training corpus yet.

## The One Thing To Try This Week

If you want a single concrete experiment that will tell you whether this workflow fits your build style: install the plugin tonight, and tomorrow morning, run `/codex:review` on whatever you ship at the end of the day.

That's it. Don't restructure your workflow. Don't enable the review gate. Don't run adversarial planning loops. Just take whatever Claude Code helps you build tomorrow, and right before you commit, ask Codex what it sees.

If the review comes back with nothing — Codex agrees the code is clean, no notes — you've spent ten cents and confirmed Claude got it right. That's worth something on its own. Confidence is a deliverable.

If the review comes back with three things you didn't see — and on most builds it will — you've just learned what kind of bugs your current workflow is shipping, and you've learned it for the cost of one Codex run. Either outcome is information you couldn't have gotten without the second model in the loop.

The workflow gets richer from there. The planning loops, the background audits, the pre-release gates, the continuous loops on sensitive code — all of those compound from the same starting move. Two models, one terminal, builder and skeptic in the same workflow.

The model debate was the wrong debate. The real question was always: what's the right team? It turns out the team is two specialists who disagree productively, and you are the engineer who decides who's right. That's a better job than being the engineer who picks one model and defends it on Twitter.

Saturday morning, six weeks ago, I was almost the engineer who didn't install the plugin. I'm glad I'm not.

## Frequently Asked Questions

### What is the Codex plugin for Claude Code?
The Codex plugin is OpenAI's official integration that lets you run Codex slash commands inside Claude Code without leaving the session. It exposes Codex as a code reviewer, codebase auditor, and delegated executor, with commands like `/codex:review`, `/codex:rescue`, `/codex:status`, `/codex:result`, and `/codex:cancel`. For the full workflow patterns, see the five workflows above.

### Do I need separate plans for Claude Code and Codex?
Yes — Claude Code uses your Anthropic plan and Codex uses your OpenAI plan. The duo is cost-effective because Codex is the lighter-spend tool in a builder/reviewer split. My current setup runs the $100 Anthropic plan with Opus 4.7 as primary and the $20 OpenAI plan for Codex review work, totaling $120 a month for one developer.

### When should I enable the Codex review gate?
Enable the review gate via `/codex:setup --enable-review-gate` only for the final 24 hours before a production deploy on sensitive code paths — payments, auth, anything compliance-relevant. Leaving it on as your default mode burns Codex tokens faster than the standard plan absorbs and slows iteration meaningfully. Disable it the moment the deploy lands.

### Does the plugin work for non-coding workflows like content or agent stacks?
Yes — the dual-agent pattern translates directly to content pipelines and agent workflows. Claude drafts prompts, system messages, and workflow definitions; Codex reviews them for ambiguity, failure modes, and edge cases. I run this on my multi-brand content stack and it surfaces latent bugs the single-model setup never caught.

### Will the slash command names change as the plugin updates?
Likely yes — the plugin is new and command syntax will evolve over the next few release cycles. Treat the [official Codex plugin GitHub repo](https://github.com/openai/codex-plugin-cc) as your source of truth for current commands. The workflow patterns described here will remain stable even when individual command names change.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
