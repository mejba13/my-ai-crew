**BRAND:** mejba.me
**TITLE:** 7 Claude Code + Opus 4.7 Tips From Boris Cherny
**META TITLE:** 7 Claude Code + Opus 4.7 Tips From Boris Cherny
**SLUG:** boris-cherny-opus-47-seven-tips
**PRIMARY KEYWORD:** Claude Code Opus 4.7 tips
**META DESCRIPTION:** I retested Boris Cherny's 7 Claude Code + Opus 4.7 tips on real work — auto mode, frontloading, /recap, effort levels, notifications, and adaptive thinking.

**TAGS:** Claude Code, Opus 4.7, AI Development, Developer Productivity, Deep Dive

---

I was halfway through a Linear-clone build when Boris Cherny's new seven-tip video landed in my feed, and I almost ignored it.

My excuse sounded reasonable. I've been using Claude Code daily for over a year. I've written more than eighty posts about it on this blog. I've tested every effort level, every model bump, every plugin that crossed my terminal. What could the guy who built Claude Code possibly tell me that I hadn't already run to ground myself?

Then he said one sentence that stopped me: "On Opus 4.7, I don't touch the extended thinking toggle anymore. It's gone." I paused the video, opened my API console, and confirmed it. He was right. The `thinking: {type: "enabled", budget_tokens: N}` parameter I'd been tuning for months now returns a 400 error on Opus 4.7. Anthropic quietly removed it. Adaptive thinking is the only thinking-on mode now, and you steer it with prompt phrasing — not a toggle.

That was one sentence. The video had seven tips. If I was already this wrong about one of them, I clearly owed the other six a fair hearing.

So I spent the next four days rebuilding my Linear clone from scratch, running each of Boris's techniques against real work — not toy prompts, not demo-day scripts. Projects, tasks, status, priority, assignees, a Next.js UI. The same spec he demoed. And I tracked what changed, what didn't, and which tips I'm keeping permanently versus which ones I'm quietly walking back.

Here's what I learned. All seven tips. The things I was doing wrong. And the exact phrasing that made Opus 4.7 behave.

## Quick note on the source: it really is Boris Cherny

Before we get into it — the transcripts of this talk floating around call him "Churnney" and "Cherny" and a few spellings in between. The person who created Claude Code is **Boris Cherny**, former Meta engineer, now Head of Claude Code at Anthropic. He joined Anthropic in 2024, shipped Claude Code as a side project in his first month, and the product crossed $1 billion in run-rate revenue by early 2026. When he talks about how to use this tool, it's worth slowing down to listen, because he's also the person who decides what ships next.

One more source correction while we're being careful: the second tool he compares Claude Code against in the talk is **OpenCode** — not "OpenClaw," which is how I first heard it in the transcript. OpenCode is the open-source terminal agent that routes to 75+ LLM providers. Keep that name straight; we'll come back to it.

Now — the seven tips, retested.

## Tip 1: Auto Mode is the Default Now. Stop Fighting the Permission Cycle.

I'll admit it — I've been a "plan mode → acceptEdits → approve manually" guy for most of 2025. The idea of letting Claude Code run bash commands without my eyes on every one of them felt reckless. Like handing someone the keys to production with the engine running.

Auto mode killed that hesitation for me.

Here's the actual mechanic. Claude Code has four permission modes that you cycle through with **Shift+Tab**: `default` → `acceptEdits` → `plan` → `auto`. The auto mode slot only appears in the cycle if your account qualifies and you've enabled it. On a Max or Team plan, `claude --enable-auto-mode` flips it on, and from there a third Shift+Tab press lands you in auto.

What makes auto mode different from acceptEdits isn't the interface — it's the classifier. Before every tool call fires, a separate Sonnet 4.6-based safety model reviews the action. If it smells like mass file deletion, data exfiltration, or prompt-injection-driven escalation, it blocks. Everything else runs. Anthropic launched this mode on March 24, 2026, specifically to handle the class of permission fatigue that most power users had been hacking around with custom allowlists.

When I retested Boris's Linear-clone build in auto mode, the difference wasn't in the code quality — it was in my attention. I wasn't watching the terminal. I was in the Claude desktop app drafting the UI copy for the tasks page while Claude Code wired up the Prisma schema, ran migrations, seeded test data, and spun up the dev server. Twenty-three tool calls happened without a single permission prompt. None of them needed me.

The one place I'd push back on Boris's framing: auto mode is not the right default for every project. If I'm touching a client's production repo, or anything where an accidental `rm -rf` would cost money, I'm still in `acceptEdits`. The classifier is good, but "good" at this scale still means occasional misses. For greenfield builds, for my own side projects, for anything where git reset is cheap — auto mode is where I live now.

If you've been Shift+Tab-ing between `default` and `plan` for the last six months, that's the habit to break. One extra press and you're in a mode designed for flow.

## Tip 2: Frontload the Entire Spec. Opus 4.7 Rewards You For It.

This is the tip that changed my output quality the most, and it's the one I most actively fought.

My habit — and probably yours — was to prompt incrementally. Build the database schema. Then the API routes. Then the UI. Then wire them together. Each step, I'd check the output, tweak, and feed the next instruction. It felt like control. It felt responsible.

It was neither. It was a habit left over from GPT-4-era models that couldn't hold a large spec in their heads.

Boris's demo was a direct rebuke. He opened Claude Code and handed Opus 4.7 this, roughly, as a single prompt:

> Build a Linear clone. Projects contain tasks. Each task has a status (todo, in-progress, done), a priority (low, medium, high, urgent), an assignee (from a users table), and support for tags. Use Next.js App Router, Prisma with SQLite for local dev, Tailwind for styling, and shadcn/ui components. The UI should have a project sidebar, a task board view, and a task detail modal. Don't ask me questions — make sensible defaults and ship it.

Six minutes later he had a working Linear clone. Not a skeleton. A working app, running on localhost, with seed data, a functional Kanban board, and a task detail modal that opened cleanly.

When I tried it on my retest, I went in skeptical. My first attempt took about seven and a half minutes. My second attempt, with a tighter spec, took under six. Both times the output was genuinely usable. Not perfect — I had to fix a shadcn import path and one Tailwind class that referenced an undefined color token — but 85-90% of the way to production-ready on the first shot.

The lesson Boris is driving at here is subtle. Opus 4.7's context window, paired with its adaptive thinking, means it can hold the *entire* architecture in mind while it builds any single piece. When you prompt incrementally, you're hiding information from it. You're forcing it to reconstruct context on every message. Worse, you're signaling that the pieces are independent — which means it builds them as if they are, and the integration phase becomes its own mess.

Frontloading means: write the full spec before you press enter. Include the UI framework. Include the data model. Include what it should *not* do. Include the aesthetic direction. Every piece of context you provide upfront is a piece Claude doesn't have to guess at.

My rule of thumb now: if my first prompt is under 150 words, I haven't thought hard enough about what I'm asking for.

## Tip 3: Claude Code vs OpenCode — Use the Right Tool for the Job

This tip is where Boris gets surprisingly honest for someone whose job is selling Claude Code.

He doesn't say "use Claude Code for everything." He draws a line. Claude Code, in his framing, is for deep, complex, consumer-grade work — the kind of build where you care about the output being production-quality, where architectural decisions matter, where you'd want a senior engineer to have made the calls. **OpenCode** is for quick prototypes and lightweight agent tooling where the speed of iteration matters more than the polish of the output.

I've been running both for a couple of months, and his framing tracks.

OpenCode — the open-source terminal agent, not Codex, not OpenAI's thing — has a real advantage for prototyping. It routes to 75+ LLM providers, so I can spin up a throwaway agent on a cheap model for rapid iteration and pay per-token rather than committing to a subscription. When I'm building a one-off CLI tool that I'll use for a week and throw away, OpenCode is faster to reach a working state because I don't care about the code quality of something I'll never maintain.

Claude Code is the opposite tradeoff. It's opinionated about quality. It's tuned for Anthropic's models, which means it gets Opus 4.7's best output by default. It has the deeper plugin ecosystem, the mature hooks system, the skills framework that I rely on daily. When I'm building something I'll maintain for six months, that opinionation is a feature.

The tip inside the tip: stop trying to find the One True Agent. You want two. One for "I need this working by dinner and I don't care if the code is ugly." One for "I'll be debugging this app at 2 AM six months from now, and the architectural choices I make today will be haunting me." Match the tool to the job.

## Tip 4: `/recap` — The Command You Didn't Know You Needed

This one I had genuinely missed. Somehow. It's been shipping since April 2026, it's in the docs, and I'd never typed it once.

`/recap` is a slash command that prints a one-line-per-action summary of everything that happened in your current Claude Code session. Which files were touched. Which commands ran. Which tests passed or failed. It's configurable in `/config`, and there's a `CLAUDE_CODE_ENABLE_AWAY_SUMMARY` env variable that controls whether it auto-fires when you return to a stale session.

The reason it matters isn't the feature itself — it's the workflow it unlocks.

I have a habit that I suspect a lot of you share. I'll kick off a long Claude Code session, walk away, come back forty minutes later with a cold coffee, and have to spend three or four minutes reading the scrollback to figure out where we were. What had Claude finished? What was still broken? Did that migration actually run? Which test file was the one failing?

`/recap` answers all of those in about two seconds. One line per action. I scan it, I know where we are, I prompt the next move.

The other place it saved me this week: I was juggling three parallel Claude Code sessions (a habit Boris himself runs at 5-10 parallel instances on a normal day). Context-switching between them was killing my flow. `/recap` in each window became my equivalent of walking up to a whiteboard to get re-oriented. Under ten seconds to know what a session had been doing.

If you haven't typed it yet, try it the next time you return to a running session. The first time will feel like finding a feature that shouldn't exist.

## Tip 5: Effort Levels — Extra High and Max Are Real, and They're Different Tools

Here's where I have to correct a mistake I made in my own head before this retest.

I'd assumed "max effort" and "extra high effort" were marketing distinctions. They're not. They're genuinely different modes with different tradeoffs, and Boris's framing cleared it up for me.

Claude Code has five effort levels: `low`, `medium`, `high`, `xhigh` (extra high), and `max`. On Opus 4.7, the default is xhigh for all plans and providers — though Anthropic quietly dropped the default from high to medium on Pro and Max subscriptions in March 2026, which is worth knowing if your output has felt a little thinner this spring than it used to.

The difference between xhigh and max matters. `xhigh` persists across sessions. It's the level you can set permanently and forget about. `max` doesn't persist — it's a current-session-only mode, unless you pin it with the `CLAUDE_CODE_EFFORT_LEVEL` environment variable. Max removes all token constraints on thinking. There's no cap. Claude will spend as much as the task seems to need.

Boris's rule, which matches my retest almost exactly:

- **Low / Medium**: trivial work. One-file edits. Small refactors. Plans you're confident are correct.
- **High**: standard feature work where you want solid reasoning without breaking the bank.
- **Extra High (xhigh)**: feature work where the architecture matters, or where Claude's first instinct is often subtly wrong.
- **Max**: large, frontloaded prompts. My Linear-clone build. Full-system migrations. The kind of task where you're handing Opus 4.7 a spec and expecting a near-complete output.

For the Linear clone retest, I ran three versions. One at high, one at xhigh, one at max. High produced a working app with three obvious bugs. Xhigh produced a working app with one cosmetic issue. Max produced a working app with zero issues on the first pass, but took about 40% longer and spent roughly 3x the tokens.

The cost curve is real. Don't run max on everything. But don't be afraid of it for the tasks that justify it — a frontloaded build prompt is exactly that kind of task.

If you'd rather have someone do this kind of architecture-first build for you — whether it's a Linear-style internal tool, a SaaS prototype, or an AI-integrated product — I take on custom builds via [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD). But you can absolutely pull this off yourself with the workflow in this post.

## Tip 6: Notifications — Stop Babysitting the Terminal

This tip is dumb-simple and fixed more of my workflow than it had any right to.

Boris sets up task-completion notifications so he can leave long Claude Code jobs running without staring at the terminal. Then he works on something else — brainstorming in the Claude desktop app, writing specs, grabbing coffee — until his Mac pings him that Claude is either done or needs input.

The mechanism is a **Stop hook** in Claude Code's hooks system. Stop hooks fire when Claude finishes its current task. You can attach any shell command you want to them — including `osascript` to trigger a macOS notification, a Slack webhook, or a terminal-notifier call. There's also a **Notification hook** that fires on permission prompts, idle prompts, or elicitation dialogs, which is useful if you're in a mode that still asks for approvals.

Here's the minimal `~/.claude/settings.json` snippet I'm running:

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude finished\" with title \"Claude Code\" sound name \"Glass\"'"
          }
        ]
      }
    ],
    "Notification": [
      {
        "matcher": "permission_prompt",
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Needs your input\" with title \"Claude Code\" sound name \"Ping\"'"
          }
        ]
      }
    ]
  }
}
```

Two different sounds. Two different states. I know which is which without looking at the screen.

The async hooks that Anthropic shipped in January 2026 matter here too — they let notification commands run in the background without blocking Claude's execution, which means no weird pause when a notification fires mid-task.

The meta-point Boris is making with this tip isn't about the notification itself. It's about the mental model shift. If you're watching Claude Code work in real-time, you're the bottleneck. You're not doing something else. You're not thinking ahead. You're a spectator at your own job. Notifications let you graduate from spectator to conductor — and a conductor running two or three Claude sessions in parallel is genuinely a different kind of developer than one grinding through a single terminal in real time.

### The workflow multiplier: brainstorm in the Claude desktop app while Claude Code runs

Boris mentions this as an afterthought in the video, but it's one of the highest-leverage pieces in the whole talk.

While Claude Code is running a long job, he opens the Claude desktop app — a totally separate product, not Claude Code — and uses it as a brainstorming surface. No tool calls. No file edits. Just a chat context where he's thinking through the next feature, drafting spec language, sanity-checking architecture choices.

When his Mac pings him that Claude Code finished, he has a fully-formed next prompt ready to fire. No "let me think about what to ask next" dead time.

I copied this pattern for the retest. My Linear-clone build had three natural pause points. In each one, instead of staring at the terminal, I opened the desktop app and drafted the next spec. The total wall-clock time for the build dropped from what would have been a half-day project to about an hour and forty minutes of real work.

## Tip 7: Adaptive Thinking — The Toggle is Dead. Long Live the Prompt.

This is the one I got wrong. Let me explain what actually changed.

On Opus 4.6 and earlier, you controlled extended thinking with an explicit parameter: `thinking: {"type": "enabled", "budget_tokens": N}`. You set a token budget. Claude thought for up to that budget. Straightforward.

**On Opus 4.7, that parameter returns a 400 error.** Extended-thinking budgets are gone. Replaced by **adaptive thinking** — a mode where Claude itself decides when to think harder and how much to spend, based on the complexity of each request.

Adaptive thinking is off by default on Opus 4.7. You enable it with `thinking: {type: "adaptive"}` in the API, or — and this is the key for Claude Code users — you steer it with prompt phrasing.

Two phrases that actually move the needle:

- **"Think carefully step by step"** — signals to Claude that this is a task worth burning thinking tokens on. It will spend more per-step reasoning.
- **"Prioritize responding quickly"** — signals the opposite. Claude shortens its reasoning and ships faster, at the cost of some depth.

Here's the nuance that took me a day to fully internalize: **effort and thinking are separate knobs now**. Effort level is the *total resource budget* for a task — how much work Claude is willing to put in overall, across tool calls, file reads, and retries. Thinking is the *per-step reasoning depth* — how hard Claude thinks before making any one decision.

You can have high effort with low thinking (lots of actions, each decided quickly) or low effort with high thinking (few actions, each carefully considered). For my Linear-clone retest, max effort + "think carefully step by step" phrasing produced the cleanest output. For a quick bug fix in an API route, xhigh effort + "prioritize responding quickly" finished in about a third the time without sacrificing correctness.

The other change worth knowing about: starting with Opus 4.7, **thinking content is omitted from the response by default**. Thinking blocks still appear in the response stream, but their content is empty unless you explicitly opt in. That's a silent change — no error, no warning — and it slightly improves response latency. If you had tooling that parsed thinking output, check whether it still works.

## What I'm Keeping, What I'm Walking Back

Four days of retesting. Here's my honest score.

**Keeping permanently:**
- **Auto mode** for greenfield and personal work. The classifier is good enough. The flow is worth it.
- **Frontloaded specs** for any build that isn't a trivial one-file edit. This one was the biggest quality jump.
- **/recap** as a muscle memory on every session resume. Zero-cost win.
- **Notifications via Stop hooks**, always. I'm embarrassed I didn't set this up a year ago.
- **Prompt-phrasing adaptive thinking** because I don't have a choice — the toggle is gone.

**Keeping with caveats:**
- **Max effort level**, but only for the right tasks. It's not a "more is better" setting. Use it when the task justifies it.
- **Claude Code vs OpenCode** framing — I agree with the split, but I still reach for Claude Code first on most days, because the ecosystem compounds.

**Walking back:**
- Nothing, honestly. Every tip held up under real work. Which is why I'm publishing this post instead of the "here's where Boris is wrong" rant I half-expected to write when I started the retest.

If you've read my previous deep-dive on [Boris Cherny's daily workflow](https://www.mejba.me/boris-cherny-claude-code-workflow) or my breakdown of [Opus 4.7's practical impact](https://www.mejba.me/opus-4-7-analysis), you'll notice these seven tips overlap with the principles Boris has been publicly sharing for months. What's new is the specific Opus 4.7 behavior — adaptive thinking, removed extended-thinking toggle, max effort as a real tier — and the way auto mode has matured from an experimental flag into something I now recommend as a default for greenfield work.

## The Linear Clone Test — What Actually Shipped

Back to the build I started with.

Four days, three retests, seven techniques applied cleanly. The Linear clone I ended up with has projects with nested tasks, a drag-and-drop Kanban board, status and priority fields, user assignees pulled from a seed database, tag support, a project sidebar, and a task detail modal — exactly the spec Boris demoed. Next.js App Router, Prisma with SQLite, Tailwind, shadcn/ui components.

Total human editing time: about forty minutes, spread across the three builds. Total Claude Code time: about three and a half hours across all three runs. Total tokens on the best run: somewhere around 450K in and 180K out. The best run was the one with max effort, frontloaded spec, auto mode, adaptive-thinking-aware prompt phrasing, and Stop-hook notifications.

Every single one of Boris's seven tips contributed something. Drop any one of them and the build gets worse, slower, or more interrupted.

## Frequently Asked Questions

### Is `/recap` a real Claude Code command?
Yes. `/recap` shipped in Claude Code in April 2026 and prints a one-line summary of your current session's actions. It's configurable through `/config`, and you can force-enable the auto-firing behavior with the `CLAUDE_CODE_ENABLE_AWAY_SUMMARY` environment variable. See the full effort-level and session breakdown above.

### What's the difference between Claude Code's "high" and "max" effort levels?
High effort caps thinking and tool-call depth at a reasonable ceiling; max effort removes the cap entirely and applies only to the current session (unless you pin it with `CLAUDE_CODE_EFFORT_LEVEL`). Use max for frontloaded, architecturally-complex builds. Use high for standard feature work. The full decision framework is in Tip 5.

### How do I enable auto mode in Claude Code?
Run `claude --enable-auto-mode` to unlock it for your account, then press Shift+Tab three times to cycle through `default` → `acceptEdits` → `plan` → `auto`. Auto mode requires a Team, Enterprise, or API plan and uses a Sonnet 4.6-based classifier to block risky tool calls before they run.

### Did Anthropic really remove extended thinking in Opus 4.7?
Yes. Setting `thinking: {"type": "enabled", "budget_tokens": N}` on Opus 4.7 returns a 400 error. Adaptive thinking (`thinking: {type: "adaptive"}`) is now the only thinking-on mode, and Claude decides thinking depth dynamically. You steer it with prompt phrasing like "think carefully step by step" or "prioritize responding quickly." Details in Tip 7.

### Should I use Claude Code or OpenCode?
Both. Use Claude Code for deep, production-quality builds where architecture and code quality matter. Use OpenCode for quick prototypes, throwaway tooling, or when you need to route to non-Anthropic models. Boris's framing in Tip 3 matches my own experience across both tools.

## One Last Thing

The reason Boris's seven tips work isn't that they're clever. Most of them, individually, are small. `/recap` is one slash command. A Stop hook is ten lines of JSON. Auto mode is one flag.

The reason they work is that together, they shift you from being the person typing into Claude Code to being the person *directing* it — and directing two or three instances of it at once, while you're thinking ahead to the next problem. Every tip in the list either removes a babysitting task, raises the output quality, or helps you spec the right thing before you press enter.

I've spent four days rebuilding the same Linear clone three ways, and the tip I keep coming back to isn't any of the seven. It's the meta-tip underneath all of them: the developers winning with Claude Code right now aren't the ones who learned more prompts. They're the ones who stopped acting like they're in a conversation with an AI and started acting like they're running a small engineering team of one.

Shift+Tab three times. Frontload the spec. Let it run. Go draft the next prompt in the desktop app while the notifications handle the updates.

That's the whole game.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
