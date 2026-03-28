**BRAND:** mejba.me
**TITLE:** Ollama Scheduled Prompts in Claude Code Changed My Mornings
**SLUG:** ollama-scheduled-prompts-claude-code
**TAGS:** AI Development, Workflow Automation, Ollama Claude Code, Scheduled Prompts, Deep Dive Guide

---

I woke up yesterday to a terminal that had already done thirty minutes of work for me.

Not in a creepy, Skynet kind of way. More like — I opened my laptop, switched to my Claude Code session, and there it was: a clean summary of overnight CI failures across three projects, a digest of AI news from the last 24 hours, and a reminder that I'd promised a client a deployment estimate by noon. All sitting there, waiting, generated while I was asleep.

I hadn't written a cron job. I hadn't set up some elaborate n8n workflow. I hadn't duct-taped together three SaaS tools with webhooks. I typed one line the night before — `/loop Give me the latest AI news every morning` — and Ollama running inside Claude Code just... did it.

This is one of those features that sounds small when you describe it and feels enormous when you use it. Ollama can now run scheduled prompts directly inside Claude Code, and after a week of building my entire morning routine around it, I'm convinced this is how developer environments are going to work from now on. Not as tools you pick up and put down, but as ambient systems that work alongside you — sometimes ahead of you.

Here's everything I've learned setting this up, what actually works, what's still rough, and why I think this matters way more than it looks.

## How I Stumbled Into This (and Why I Almost Missed It)

I'd been running Ollama locally for months. Mostly for quick local inference — testing prompts offline, running smaller models when I didn't want to burn API credits, that kind of thing. Useful, but nothing that fundamentally changed my workflow.

Then a colleague mentioned, almost offhandedly, that Ollama had integrated with Claude Code's scheduling system. My first reaction was skepticism. Scheduling prompts sounded like a gimmick — something you'd demo at a conference but never actually use day-to-day.

I was wrong. Spectacularly wrong.

The integration works like this: you launch Ollama through Claude Code using `ollama launch claude`, which spins up an Ollama instance that's aware of your Claude Code environment — your project context, your file system, your running processes. Then you use the `/loop` command to schedule recurring prompts that run at intervals you define.

That's the mechanics. The magic is in what you do with it.

Because once your dev environment can run prompts on a schedule — unprompted, in the background, while you're doing other things or not even at your desk — the entire relationship between you and your tools shifts. You stop thinking of AI as something you query and start thinking of it as something that watches, checks, and reports. An ambient layer of intelligence sitting on top of your workflow.

But I'm getting ahead of myself. Let me walk you through the actual setup, because the details matter more than the concept.

## Getting Ollama Running Inside Claude Code

The prerequisite is straightforward: you need Ollama installed locally and Claude Code running in your terminal. If you've got both, you're ninety percent of the way there.

**Step 1: Launch Ollama through Claude Code.**

Open your Claude Code session and run:

```bash
ollama launch claude
```

This isn't the same as running `ollama serve` in a separate terminal. The `ollama launch claude` command creates a bridge between Ollama's local model runner and Claude Code's agent framework. Your Ollama instance inherits context awareness — it knows what project you're in, what files exist, what git branch you're on. This context is what makes scheduled prompts actually useful instead of just generic.

**Step 2: Verify the connection.**

You should see confirmation that Ollama is running within your Claude Code environment. Try a quick test prompt to make sure everything's wired up:

```
/loop What time is it? every 5 minutes
```

If you see a response confirming the loop is scheduled, you're good. Kill that test loop before it clutters your session — we're about to set up real ones.

**Step 3: Understand the `/loop` syntax.**

The `/loop` command follows a natural language pattern:

```
/loop [your prompt] every [interval]
```

Intervals can be specified in minutes, hours, or time-of-day anchors. Here are examples that actually work:

```
/loop Summarize my git diff every 2 hours
/loop Check for failing tests every 30 minutes
/loop Give me a standup summary every morning at 9am
/loop Review open PRs and flag stale ones every day at 3pm
```

The natural language parsing is surprisingly flexible. I've thrown some oddly phrased schedules at it and it generally figures out what I mean. "Every weekday morning" works. "Twice a day" works. "Every Monday" works.

Here's the thing that makes this different from just setting up a cron job with a curl command to an API. The prompts run *inside* your Claude Code context. They can see your project files. They can read your git history. They can check your running processes. They can reference previous loop outputs. A cron job is stateless and blind. A `/loop` prompt is contextual and aware.

That distinction is everything, and it's what I didn't appreciate until I started building real automations around it.

## The Seven Loops That Actually Changed My Workflow

I experimented with dozens of scheduled prompts over the past week. Most were useless — novelty experiments that didn't survive day two. But seven of them stuck, and they've genuinely restructured how I start, run, and end my workday. Let me walk through each one, because the specific prompts matter.

### Loop 1: The Morning Briefing

```
/loop Give me a morning briefing: summarize overnight git commits across all projects in this workspace, list any CI/CD failures, and flag PRs that have been open more than 48 hours. every morning at 8:30am
```

This is the one that hooked me. Before I even open Slack, before I check email, I have a clear picture of what happened overnight. For someone running multiple projects — which, if you're a freelance developer or a small team lead, you probably are — this eliminates the first twenty minutes of context-switching every morning.

The output isn't just a raw git log dump. Because the prompt runs inside Claude Code with full context, it actually summarizes *what changed* in human-readable terms. "The auth module got three commits from the feature branch — looks like session handling was refactored" is infinitely more useful than a list of commit hashes.

### Loop 2: The Test Watchdog

```
/loop Run the test suite and report only failures with a brief diagnosis of what might be wrong. every 2 hours
```

I used to run tests manually or rely on CI to catch failures after I'd already pushed. Now my local environment runs the suite every two hours and gives me a heads-up before bad code leaves my machine. The "brief diagnosis" part is key — it doesn't just say "test_user_auth failed," it says "test_user_auth failed because the mock database isn't returning the expected session token format. Looks like the schema change in migration 047 might not have been applied locally."

Is the diagnosis always right? No. Maybe 70% of the time it's spot-on, 20% it's in the right neighborhood, and 10% it's completely off. But even a wrong diagnosis gives me a starting point, which is better than staring at a stack trace cold.

### Loop 3: The Dependency Auditor

```
/loop Check package.json and requirements.txt for any dependencies with known vulnerabilities or major version updates available. every day at noon
```

Security hygiene is one of those things everyone agrees is important and nobody does consistently. This loop handles it passively. Once a day, I get a quick report: "lodash has a patch available, no security issues. The jsonwebtoken package is two major versions behind and has a moderate CVE." I can act on it or note it for later, but at least I'm aware.

### Loop 4: The PR Nagger

```
/loop Check all open pull requests in this repo. For any PR older than 24 hours without a review, remind me and suggest what to prioritize reviewing first based on the diff size and files changed. every day at 2pm
```

This one is surprisingly effective for team dynamics. I'm terrible at remembering to review PRs promptly — I get deep into my own work and forget. Now, every afternoon, I get a nudge with actual context about which review is most important. Small diff touching a critical file? That gets flagged as high priority. Large refactor with no test changes? That gets flagged as "needs careful review."

### Loop 5: The Documentation Drift Detector

```
/loop Compare the current API routes in the codebase with the API documentation file. Flag any endpoints that exist in code but aren't documented, or documented but no longer exist in code. every 3 days
```

Documentation rot is a real problem on every project I've worked on. This loop catches it before it gets bad. Every three days, I find out if the docs still match reality. The interval is long enough that it's not noisy, short enough that drift doesn't accumulate for weeks.

### Loop 6: The End-of-Day Summary

```
/loop Summarize what I worked on today based on git commits, file changes, and terminal history. Format it as bullet points I could paste into a standup message. every weekday at 5:30pm
```

This is pure laziness optimization, and I love it. I hate writing standup updates. Loathe it. This loop generates a first draft that's 80% accurate, and I spend thirty seconds tweaking it instead of five minutes trying to recall what I did seven hours ago.

### Loop 7: The AI News Digest

```
/loop Give me the latest AI news and notable releases from the last 24 hours, focused on developer tools, LLM updates, and open-source AI projects. every morning at 8am
```

This was actually the first loop I set up — the one from the opening of this post. Staying current in AI is genuinely hard when the field moves this fast. A daily digest that's waiting for me before I start work means I'm never more than 24 hours behind on something important.

The quality of these digests depends on the model's knowledge cutoff and what it can access, which is a limitation I'll talk about honestly in a few sections. But even imperfect news summaries beat the alternative, which is doomscrolling Twitter for thirty minutes trying to figure out what happened yesterday.

Now here's where it gets really interesting — because these seven loops are just the obvious use cases. The architectural implications go much deeper.

## The Real Shift: From Reactive Tools to Ambient Intelligence

I want to zoom out for a second, because I think most coverage of features like this misses the bigger picture.

For the entire history of software development, our tools have been reactive. You open your editor — it waits. You type a command — it executes. You ask a question — it answers. The tool does nothing until you initiate. Every interaction starts with you.

Scheduled prompts break that pattern fundamentally.

Your dev environment is now doing things *without being asked*. It's monitoring, analyzing, summarizing, and alerting on its own schedule. Not in a "set it and forget it" automation way — this isn't a bash script running on cron. The prompts are contextual, adaptive, and intelligent. They understand your codebase. They can reason about what they find.

This is what ambient intelligence looks like in practice. Not some sci-fi holographic assistant floating next to your desk. Just a terminal that's quietly doing useful work in the background while you focus on the hard problems.

Think about what this means for a typical workday. You used to start your morning by manually checking CI status, reviewing overnight commits, scanning for open PRs, and catching up on what changed. That's 30-45 minutes of context-loading before you write a single line of code.

With scheduled loops, that context is pre-loaded. You sit down, glance at the summaries, and you're already oriented. You start coding immediately, at full speed, with full awareness.

Over a week, that's 2-4 hours saved. Over a month, it's a full day or more. And that's just the time savings — the cognitive savings are harder to quantify but arguably more valuable. Every context switch you eliminate is mental energy preserved for actual problem-solving.

I've been thinking about this in terms of what I call "developer attention debt." Every manual check, every status review, every "let me see what's happening with the build" is a small withdrawal from your daily attention budget. Individually trivial. Collectively brutal. Scheduled prompts pay off that debt automatically.

But — and this is where my enthusiasm gets tempered by honesty — the current implementation has real limitations that you should know about before you rebuild your entire workflow around this.

## What Doesn't Work Yet (Honest Assessment)

I'd be doing you a disservice if I painted this as flawless. After a week of heavy use, here's what's rough.

**The scheduling isn't perfectly reliable.** Loops occasionally skip a cycle, especially if your machine sleeps or if Claude Code's session gets interrupted. I've had my morning briefing not fire because my laptop lid was closed at 8:30 AM. This is a local execution model — there's no cloud scheduler backing it up. If the process isn't running, the loop doesn't execute. You can't treat it like a server-side cron job that fires regardless.

**Long prompts sometimes produce inconsistent output.** My more complex loops — the ones with multi-part instructions like the morning briefing — occasionally focus on one part and skip another. The PR check might be thorough while the CI failure summary gets a single sentence. Shorter, more focused prompts are more reliable than kitchen-sink prompts. I've started splitting complex briefings into two or three separate loops rather than one mega-prompt.

**Context window pressure is real.** Every loop execution consumes context. If you're running six loops throughout the day and also doing active development work in the same Claude Code session, you can hit context limits faster than you'd expect. I've had sessions where my afternoon loops produced noticeably worse output because the context window was saturated with morning loop results plus my actual work. The workaround is managing your session actively — clearing context when it gets heavy, or running loops in a dedicated session separate from your development work.

**Model knowledge boundaries apply.** The AI news digest loop is useful but inherently limited by what the model knows. It can't browse the internet in real time (unless you've set up web access tools). So "latest AI news" really means "what I can infer or recall up to my training cutoff, plus anything in your local files." For truly current news, you'd need to pair the loop with a web fetch tool or RSS integration. I'm working on this, but it's not seamless yet.

**No built-in loop management UI.** There's no dashboard showing your active loops, their last execution time, or their output history. It's all in your terminal scroll buffer. If you want to review what a loop reported three days ago, you're hunting through terminal history. I've started logging loop outputs to files as a workaround:

```
/loop Check for failing tests and append results to ./logs/test-watchdog.log every 2 hours
```

This works but feels like a hack. A proper loop management interface — list active loops, pause/resume them, view execution history — would make this feature dramatically more useful.

**Resource consumption adds up.** Each loop execution uses compute. Running Ollama locally means your CPU and RAM are handling inference. Six loops firing throughout the day on a laptop can make the fans spin and drain battery noticeably. I've learned to be selective about loop frequency. Not everything needs to run every 30 minutes. Most things are fine at 2-hour or daily intervals.

These aren't dealbreakers. Every one of these limitations is solvable, and I'd bet most of them get addressed in coming updates. But knowing about them upfront saves you the frustration of discovering them mid-workflow, which is exactly the frustration I went through so you don't have to.

The limitations are real, but they don't change the fundamental value proposition. Which brings me to the part I'm most excited about.

## Building a Full Ambient Workflow: My Current Setup

After a week of iteration, here's my complete loop configuration. I'm sharing the exact prompts because the specific wording matters — vague prompts produce vague results.

**Session 1: Development Environment (primary Claude Code session)**

```
/loop Run the test suite for the current project, report failures only with one-line explanations. every 2 hours

/loop Scan the current git diff and warn me if I'm about to commit any hardcoded secrets, API keys, or credentials. every 30 minutes
```

I keep the development session lean — only two loops that are directly relevant to whatever I'm actively building. The secrets scanner alone has saved me twice from committing an `.env` value that snuck into a config file.

**Session 2: Project Operations (separate Claude Code session, runs in a background terminal tab)**

```
/loop Morning briefing: overnight commits summary, CI status, stale PRs. every weekday at 8:30am

/loop Scan dependencies for known CVEs and available security patches. every day at noon

/loop End-of-day summary: what changed today in bullet point format. every weekday at 5:30pm
```

This session handles the operational overhead. I glance at it a few times a day, but it doesn't compete for context with my actual coding work.

**Session 3: Learning and Awareness (third session, lightweight)**

```
/loop AI development news and tool releases from the last 24 hours. every morning at 8am
```

One loop, one purpose. Keeping the learning session separate means it never crowds out work context.

Three sessions, six loops total. My CPU handles it fine on an M-series MacBook, though I notice the fans if all three sessions are active and I'm also running Docker. The resource footprint is manageable but not negligible.

Here's how this plays out on a typical Tuesday:

**8:00 AM** — AI news digest is waiting. I skim it while coffee brews. Two minutes.

**8:30 AM** — Morning briefing drops. I know that CI is green, there are two open PRs (one from yesterday, one from overnight), and the auth refactor branch got three commits from a collaborator. Five minutes of reading, and I'm fully oriented.

**9:00 AM** — I start coding with complete project awareness. No ramp-up time. No "wait, what was I working on yesterday?" No checking CI manually. I just... start.

**10:30 AM** — The secrets scanner runs. Nothing flagged. Good. I don't even break flow.

**11:00 AM** — Test watchdog fires. Two tests failing — both related to a mock I forgot to update after yesterday's schema change. The diagnosis is accurate. I fix both in ten minutes.

**12:00 PM** — Dependency audit lands in the ops session. One low-severity CVE in a transitive dependency. I note it for the weekly update cycle.

**1:00 PM** — Test watchdog fires again. All green now. Confidence that my morning fixes held.

**2:00 PM** — PR nagger (I added this to the ops session after day three). One PR from a collaborator has been open 36 hours — it's a small diff touching the payment module. I review it immediately. Fifteen minutes well spent.

**3:00 PM** — Test watchdog. All green. Secrets scanner. Clean.

**5:30 PM** — End-of-day summary generates. I copy it, tweak two bullet points, paste into the team Slack. Done in under a minute.

Total time spent on operational overhead: roughly 35 minutes, scattered across the day in small, low-friction interactions. Compare that to my pre-loop routine where I'd spend 30+ minutes just on the morning context-load, plus another 20-30 minutes across the day on manual checks and status reviews.

The savings compound. And more importantly, the cognitive load dropped dramatically. I'm not carrying "did I check the CI?" or "are there PRs I'm ignoring?" in the back of my mind. The system handles that.

That's the real unlock — not time savings, though those are real, but *attention* savings. My brain has fewer open tabs.

## Advanced Patterns I'm Exploring

The basic loops cover the obvious use cases. But I've started experimenting with more sophisticated patterns that hint at where this is going.

**Chained Loops.** Using one loop's output as input for another. For example, the test watchdog detects a failure and writes it to a log file. A second loop monitors that log file and, when it detects a new entry, attempts an automated fix and runs the tests again. I've gotten this working for simple cases — missing imports, unclosed brackets, forgotten mock updates. The success rate on auto-fixes is maybe 40%, but when it works, a failing test gets fixed without me touching the keyboard.

```
/loop If ./logs/test-watchdog.log has new failures in the last 2 hours, attempt to fix them and re-run the affected tests. Report what you tried and whether it worked. every 2 hours offset by 15 minutes
```

The "offset by 15 minutes" gives the test watchdog time to write its results before the auto-fixer reads them. Crude coordination, but it works.

**Conditional Loops.** Prompts that only produce output when something interesting happens. My secrets scanner is technically a conditional loop — it only alerts me when it finds something. But you can apply this pattern broadly:

```
/loop Check if any new issues have been opened in this repo. Only tell me if there are new ones since the last check. every 4 hours
```

Silence means everything's fine. Output means something needs attention. This inverts the default — instead of you checking for updates, updates announce themselves.

**Reflective Loops.** This one's experimental and honestly a bit weird, but I'm fascinated by it. A loop that reviews your recent code and offers unsolicited architectural suggestions:

```
/loop Review the last 3 hours of git commits in this project. If you notice any patterns that suggest emerging technical debt, architectural drift, or inconsistency with the existing codebase patterns, flag them. Be specific and give suggestions. every day at 4pm
```

The output ranges from genuinely insightful ("You've added four different error handling patterns across these commits — consider standardizing on the Result type pattern you used in the auth module") to completely useless ("Your code looks good, no issues detected"). About one in three runs produces something I actually act on. That hit rate is low, but the insights are high-value enough that it's worth keeping.

These patterns are rough. They need better tooling, better chaining mechanisms, better state management between loops. But they sketch out a future where your dev environment isn't just passively helpful — it's actively participating in code quality, project management, and architectural coherence.

## What This Means for Developer Productivity (The Bigger Argument)

I want to make a claim that might sound hyperbolic, so let me build to it carefully.

The most productive developers I know aren't the fastest coders. They're the ones with the best *systems*. They have dashboards that surface problems early, habits that prevent context-switching, routines that front-load awareness. They spend less time wondering "what should I work on?" and more time actually working.

Scheduled prompts are a systems-level upgrade to developer productivity. Not because any single loop is transformative, but because the aggregate effect of six or seven well-designed loops eliminates an entire category of friction from your workday. The category I'd call "operational awareness" — knowing what's happening across your projects, your team, your dependencies, your pipeline.

Most developers handle operational awareness through a combination of manual checks, Slack notifications, email alerts, and dashboard-hopping. It's scattered, reactive, and interruptive. Scheduled loops consolidate it into a single, quiet, contextual stream that's waiting for you when you want it and doesn't interrupt you when you don't.

Here's my bigger claim: **we're watching the dev environment evolve from a tool into a teammate.** Not a teammate that writes code for you — that's the current AI conversation, and it's important, but it's only half the story. A teammate that handles the operational periphery. That watches the things you'd forget to check. That notices patterns you'd miss because you're too deep in implementation details.

The `/loop` command is a small feature. The paradigm it enables is enormous.

Think about where this goes in twelve months. Loops that coordinate across team members' environments. Loops that learn your patterns and adjust their frequency. Loops that escalate based on severity — a minor dependency update gets logged quietly, a critical CVE triggers an immediate alert. Loops that interface with external services — your project management tool, your monitoring stack, your communication channels.

Your terminal becomes a command center, not a command line.

That's not a prediction. It's an extrapolation of what already works today, just with rougher edges. And if you start building the habit now — start setting up loops, start thinking about your workflow as something that can be partially automated and continuously monitored — you'll be positioned to take advantage of every improvement as it ships.

## The One-Hour Challenge

If you've read this far, you already understand the value. So here's what I'd do if I were you, today, in the next hour.

Open Claude Code. Run `ollama launch claude`. Set up exactly two loops:

```
/loop Summarize my git activity for the day in bullet points. every weekday at 5pm

/loop Check for any failing tests and explain what's wrong. every 3 hours
```

Two loops. Simple. Low commitment. Run them for a week.

I'm betting that by day three, you'll start thinking of new loops. By day five, you'll wonder how you managed without them. By the end of the week, you'll be building the kind of ambient workflow I described — not because I told you to, but because once you experience a dev environment that works *for* you in the background, going back to a purely reactive setup feels like driving without mirrors.

Your terminal is already open. Your AI is already running. The only question is whether it's working while you are — or only when you remember to ask.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
