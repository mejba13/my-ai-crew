**BRAND:** mejba.me
**TITLE:** Claude Code + Codex: My Two-Agent Workflow That Never Breaks
**META TITLE:** Claude Code + Codex Two-Agent Workflow (2026 Guide)
**SLUG:** claude-code-codex-two-agent-workflow
**PRIMARY KEYWORD:** Claude Code Codex two-agent workflow
**SECONDARY KEYWORDS:** multi-agent AI coding, Claude Code self-repair, Obsidian agent memory
**META DESCRIPTION:** How I pair Claude Code (Opus 4.7) with Codex CLI as a supervisor-builder team — near-zero downtime, lower token costs, and shared Obsidian memory across agents.
**TAGS:** Claude Code, Codex, Multi-Agent Workflow, AI Automation, Opus 4.7
**CONTENT CLUSTER:** Claude Code and AI Agents
**TRANSFORMATION GOAL:** After reading this, you will be able to set up a two-agent Claude Code + Codex workflow where one agent supervises and plans while the other executes and monitors — with shared Obsidian memory and automatic self-repair when Claude Code breaks.

---

It was 2:17 AM on a Tuesday in March. I was mid-content-run across all four of my brands — Aria, my content agent, was ten posts deep into a batch. Then Claude Code auto-updated. Silently. In the background. The way it always does.

The next prompt I sent returned an error I'd never seen before. `ENOENT: no such file or directory`. Something about a ghost npm package. Ten minutes of googling told me I wasn't alone — half the Claude Code community hits this at some point when the CLI's auto-updater collides with a leftover temp directory from a prior failed install. The fix isn't hard. It's just tedious: nuke the node_modules cache, run `claude doctor`, reinstall the package globally. Ten to forty minutes of downtime, depending on what else breaks along the way.

That night I lost two hours. Aria sat there, frozen mid-batch, waiting on a CLI that couldn't start. The worst part? I had a Codex CLI sitting in another terminal tab, perfectly healthy, running GPT-5.4, and completely idle. It could have fixed Claude Code in under a minute if I'd wired things up right. I hadn't. I do now.

This post is the workflow I've been running since. One main agent. One assistant. Two terminals. A shared Obsidian vault holding the memory between them. And a set of rules about who does what — so the expensive model does the thinking, the cheap model does the typing, and when one of them breaks, the other one quietly puts it back on its feet.

If you run Claude Code daily and you've ever lost a morning to a CLI that stopped working at the worst possible moment, this is the fix. It's also the reason my token bill dropped by roughly 40% last month while my throughput went up. Let me show you how the pieces fit.

## Why a Single Agent Is More Fragile Than You Think

Most developers I talk to run Claude Code as a solo agent. One terminal. One session. One model — usually Opus 4.7 now that it's the default (it shipped April 16, 2026 and replaced Opus 4.6 as the flagship). The workflow looks clean on paper. In practice, it's three failure modes waiting to happen.

**Failure mode one: the CLI itself breaks.** Claude Code ships updates aggressively. Sometimes daily. Most of the time the updater works perfectly. Sometimes it doesn't, and you get a zombie install that can't launch until you hand-clean it. If Claude Code is the only agent you have running, you *are* the repair process. Which means your day stops until you find the right StackOverflow thread.

**Failure mode two: the model loses the plot.** Opus 4.7 is genuinely sharp — 87.6% on SWE-bench, three times the vision resolution of 4.6, the new xhigh effort level for the gnarliest tasks. But no model is bulletproof across a six-hour build. Long horizons drift. Context gets compacted. Decisions made in message three quietly stop mattering by message sixty. With one agent, there's nobody checking the work except you.

**Failure mode three: the math gets ugly fast.** Opus 4.7 is $5 per million input tokens and $25 per million output tokens. That pricing is fair for frontier reasoning work. It's genuinely wasteful when the task is "rename 14 variables across these files" or "apply the plan you just wrote" or "run the test suite and report what fails." You're paying Opus prices to do Haiku work.

A two-agent setup doesn't eliminate these problems. It turns each one into someone else's job. The CLI breakage becomes Codex's responsibility. The long-horizon drift gets caught by a supervisor loop. The expensive-model-doing-cheap-work problem disappears because you split the roles.

And before we get to the setup, I want to kill one assumption right up front: this isn't about replacing Claude Code. Claude Code is still the main agent. Codex is the partner that makes Claude Code reliable enough to run in production.

## The Two Agents — And Why This Specific Pairing

I spent a few weeks testing different combinations. Claude Code + Gemini CLI. Claude Code + GLM as a local open-weight assistant. Claude Code + a second Claude Code instance running Haiku. They all work to varying degrees. The pairing I settled on — and the one I'm recommending here — is **Claude Code (Opus 4.7) + Codex CLI (GPT-5.4)**.

Here's why that specific combination.

**Claude Code (Opus 4.7) as the main agent.** This is the brain. It handles architectural decisions, long-horizon planning, code review, anything that needs deep reasoning over a large context window. Opus 4.7 is currently the strongest general-purpose coding model I've tested — it made visible jumps on the hardest SWE-bench tasks compared to 4.6, and it handles my custom Aria agents better than any other model I've tried. Anthropic still keeps Opus 4.6 available during the transition window, so if you have a workflow pinned to 4.6, you're not forced to migrate yet. Most of my pipelines are on 4.7 now.

**Codex CLI (GPT-5.4) as the assistant.** Codex has had an interesting evolution. GPT-5.3-Codex shipped last year as a coding specialist. Then in April 2026, OpenAI rolled out GPT-5.4 — the first mainline reasoning model that folds the frontier coding capabilities of GPT-5.3-Codex into the general model line. It's available in ChatGPT, the API, and the Codex CLI as `gpt-5.4`, with native computer-use capabilities and up to 1M tokens of context. There's also a smaller sibling, **GPT-5.3-Codex-Spark**, that runs at more than 1000 tokens per second — essentially real-time, ideal for quick execution tasks. Codex itself is stable, has a solid TUI, good MCP support, and a changelog that shows active weekly shipping. The important thing for our workflow: Codex is a different CLI from a different vendor running on a different model. When Claude Code's CLI breaks because of an Anthropic update, Codex doesn't care. It keeps running.

Why this pairing over running two Claude Code instances? Because the failure mode I care most about is *CLI-level breakage during auto-update*. If both agents are Claude Code, they both break at the same time for the same reason. Cross-vendor pairing is the whole point. You want heterogeneity at the CLI layer.

You don't have to use Codex specifically. The key constraint is: **the assistant must be a different CLI from a different vendor.** Gemini CLI works. GLM or a local open-weight setup works (and cuts the cost even further if you have the hardware). The Codex Plugin for Claude Code — which OpenAI shipped as a way to call Codex directly from inside Claude Code — is a shortcut if you want the tightest possible integration. I run both agents as independent CLIs in separate terminals because I like being able to see what each is doing. That's a preference, not a requirement.

## The Four Use Cases That Justify the Setup

Once I had both CLIs running, the real question was: what do I actually assign to which agent? After a few weeks of experimentation, four patterns emerged. These are the ones I'd keep even if I had to throw everything else away.

### Use Case 1: Self-Repair When Claude Code Breaks

This is the one that paid the setup cost back in a single incident.

The symptom: Claude Code throws `ENOENT` or refuses to launch after an auto-update, or a ghost temp directory blocks the installer. The fix is mechanical — it's the same three or four commands every time. `claude doctor` to diagnose. `rm -rf` on the ghost node_modules directory. `npm uninstall -g @anthropic-ai/claude-code`. `npm install -g @anthropic-ai/claude-code`. Verify with a test prompt.

A human can do this in three minutes. A well-prompted Codex agent can do it in about twenty seconds. Here's the repair prompt I keep as a Codex slash command:

```
You are the Claude Code repair agent. Claude Code is broken on this
machine and needs to be restored. Follow this sequence and report each
step:

1. Run `claude doctor` and capture the output.
2. If a ghost package directory is flagged (usually
   ~/.nvm/versions/node/*/lib/node_modules/@anthropic-ai/.claude-code-*),
   remove it with `rm -rf`.
3. Run `npm uninstall -g @anthropic-ai/claude-code`.
4. Run `npm install -g @anthropic-ai/claude-code`.
5. Confirm with `claude --version` and a test prompt.

Do NOT interrupt any running npm operation. If you see a stale lockfile,
wait before retrying. Log every command and its output to
~/obsidian-vault/agents/codex/repair-log-{date}.md.
```

The logging step matters more than it looks. Every repair writes to the shared Obsidian vault (we'll get to that), so across weeks I can see patterns: which updates broke things, what the symptoms were, what the actual fix was. That log has saved me three times when the same failure pattern recurred.

Downtime on the most recent breakage: 31 seconds, from the moment Claude Code returned an error to the moment the test prompt came back clean. I didn't even notice it happened until I checked the log the next morning.

### Use Case 2: The Supervisor-Builder Pattern

This is the cost saver. It's also the pattern that most changes how I think about Claude Code workflows.

The idea is simple: **Opus plans, Codex executes, Opus reviews.**

Claude Code (Opus 4.7) is genuinely excellent at architecture — breaking a problem down, writing a spec, laying out a file structure, deciding which patterns to apply. But once the plan exists, the actual implementation is mostly typing. Function bodies. Boilerplate. Import statements. Tests that exercise the interface the plan defined.

So the workflow goes like this:

1. **Plan in Claude Code.** I give Opus 4.7 the feature request. It produces a detailed plan — file-by-file breakdown, data flow, interface shapes, acceptance criteria — and writes it to `~/obsidian-vault/agents/shared/plans/{slug}.md`.

2. **Execute in Codex.** I point Codex at the plan file and tell it to implement. `gpt-5.4` or `gpt-5.3-codex-spark` does the typing. For most execution tasks, Spark is enough — it's faster and cheaper. Heavier implementation work goes to `gpt-5.4`.

3. **Review in Claude Code.** Codex writes the code and logs what it did. Opus 4.7 reads the diff, checks against the plan, flags anything that drifted, and either signs off or sends revisions back.

The token economics work out roughly like this on my content pipeline. Before the split, a typical feature was 60K input tokens and 25K output tokens of Opus 4.7 — call it $0.93. After the split, planning and review together are maybe 20K input / 8K output of Opus ($0.30) plus 80K input / 30K output of Codex at GPT-5.4 pricing (which, for my actual mix, lands around $0.35 to $0.45 depending on how much Spark I can use). So roughly $0.70 instead of $0.93, which doesn't sound huge until you multiply it by a week of work. My last month's token bill was about 40% lower than the month before.

The quality is at least as good. In some cases better — because Opus reviewing Codex's output catches things Opus would have missed if it had written the code itself. Different models see different bugs.

The trick that makes this actually work: the plan has to be specific enough that Codex can execute it without making architectural decisions. Vague plans produce drift. I've built up a template for how Opus writes plans — interface signatures first, file list second, step-by-step implementation order third, test plan fourth — and I make Opus stick to that template. When the plan is good, Codex's execution is nearly mechanical.

### Use Case 3: The Monitor Loop

This one runs on cron. It's the quietest part of the setup and the one I'd miss the most.

Codex runs every fifteen minutes. Its job is to check the state of things Claude Code is working on — long-running builds, deployed services, scheduled content batches, test suites that get flaky over time. It's a supervisor that never sleeps and charges almost nothing because GPT-5.3-Codex-Spark handles the basic checks.

Sample monitor tasks:

- Tail the latest `npm test` run, parse the results, and flag any new failures compared to yesterday's baseline.
- Check the health endpoint of each deployed service across my four brands and report anything non-200.
- Scan the last 24 hours of Aria's content output for any posts that look structurally wrong (missing footer, wrong word count, banned phrases).
- Check for new Claude Code CLI versions and, if one shipped, run a quick smoke test before I start my next session.

Each check writes to `~/obsidian-vault/agents/codex/monitor/{check-name}-{date}.md`. Anything that trips a threshold also pings me on Telegram (I'll cover the Telegram piece in a minute).

The cost of running this every 15 minutes, seven days a week, is genuinely tiny — low single-digit dollars per month. The value is that I find out about drift *before* it becomes a problem. Twice this quarter, the monitor caught a test regression that had been sitting there for hours before I'd have noticed it manually.

Before we get to the memory layer, a quick honesty check. If you're not running multiple long-lived projects across multiple brands, the monitor loop is probably overkill. This piece of the setup earns its keep because I'm running a content agency, not a single codebase. If your world is one project, skip the monitor and just run use cases 1 and 2.

### Use Case 4: Shared Obsidian Memory

The piece that ties everything together — and the piece most two-agent guides skip entirely.

Both agents read and write to the same Obsidian vault. Obsidian is just a folder of markdown files on disk. No cloud lock-in, no proprietary format, no API gate. Any CLI agent with filesystem access can read and write to it directly. For the MCP layer, I use the `obsidian-mcp` server so both Claude Code and Codex can query the vault structurally (find-by-tag, graph traversal) in addition to raw file reads.

My vault structure for agent memory looks like this:

```
~/obsidian-vault/agents/
├── claude-code/
│   ├── session-logs/           # per-session decisions and context
│   ├── plans/                   # architectural plans Opus produced
│   └── retrospectives/         # what worked, what didn't, per project
├── codex/
│   ├── repair-log/             # every CLI repair event
│   ├── monitor/                # cron check outputs
│   └── execution-logs/         # what Codex actually executed
└── shared/
    ├── context/                # project-level state both agents read
    ├── decisions/              # architecture decision records
    ├── plans/                  # handoff plans (Claude writes → Codex reads)
    └── glossary.md             # terms, conventions, project vocab
```

The `shared/` folder is the important part. When Claude Code finishes a planning session, it writes the plan to `shared/plans/`. When Codex starts executing, it reads from that exact file. When Codex finishes, it appends an execution log. When Claude Code returns to review, it reads both the plan and the execution log. Neither agent needs to be told what the other did — the vault is the handoff.

Three conventions I learned the hard way:

**Date every file.** Agent memory rots fast without timestamps. Every file gets a YYYY-MM-DD prefix or a frontmatter date.

**Keep each file single-purpose.** One decision per decision record. One plan per plan. One retrospective per project phase. When a file grows beyond a few hundred lines, it stops being useful memory and starts being a document nobody reads.

**Write for the other agent, not for yourself.** This took me a while to internalize. When Claude Code writes a plan for Codex to execute, it's not writing notes for a human. It's writing a spec for a different model. The more explicit and machine-friendly the structure, the cleaner the execution.

The recursive learning angle is real. Because both agents are writing their reasoning to disk, and because I can query the vault, I can go back weeks later and see how decisions were made, what repair patterns recurred, which plans turned into good code and which ones didn't. Over time, the vault becomes a training corpus for the agents themselves — you can feed Claude Code last month's successful plans as context for this month's planning session.

I covered the Obsidian + Claude Code integration in detail in my [guide on turning Obsidian into an AI second brain](https://www.mejba.me/blog/obsidian-claude-code-second-brain), and most of the techniques there carry over cleanly. The difference in a two-agent setup is that the vault has to serve two very different readers.

## The Hardware and Config Side

Enough philosophy. Here's the actual setup.

**Terminal layout.** I use Ghostty with a split-pane layout — Claude Code on the left, Codex on the right, a third pane at the bottom for shell work. Tmux works equally well. The point is visibility: when I glance at my screen, I want to see what both agents are doing at a glance. If you've read my [Ghostty + Claude Code workflow post](https://www.mejba.me/blog/ghostty-terminal-claude-code-workflow), this is a direct extension of that layout.

**MCP servers.** Both agents share:

- `obsidian-mcp` for vault access
- Filesystem MCP for project file reads
- A custom `git-mcp` for safe git operations (staged diffs only, no force-push)

Claude Code also has: Figma MCP (for design workflows), Playwright MCP (for browser tests), and a set of brand-specific content MCPs I built for Aria.

Codex only has the shared three plus shell execution. Kept minimal on purpose — Codex's job is execution, not expansion into every integration.

**Slash commands.** The repair flow, the monitor check, the plan-execute-review cycle — all wrapped as slash commands. `/repair-claude-code` on Codex side, `/plan` and `/review` on Claude Code side. Slash commands reduce the cognitive overhead of switching between agents.

**Telegram bridge (optional).** I have a small Node service that listens for writes to `~/obsidian-vault/agents/codex/alerts/` and forwards them to Telegram. When something trips the monitor at 3 AM, I get a ping on my phone. I cover the bigger remote-control pattern in my post on [remote-controlling Claude Code from your phone](https://www.mejba.me/blog/claude-code-remote-control-phone) — the Telegram bridge here is a simpler version of the same idea.

## Where This Setup Is Wrong for You

I'm going to do the honest thing and talk you out of this setup if it's not a fit.

**If you're not running Claude Code daily, skip it.** The two-agent setup has real setup overhead — a weekend of tuning, then ongoing maintenance as both CLIs update on their own schedules. If Claude Code is a tool you use a few times a week, a single agent is fine. You'll spend more time maintaining the two-agent rig than you'd ever save.

**If your projects are small and short-lived, skip most of it.** The shared memory layer pays off because my projects span days or weeks. If your work is a lot of one-off tasks, the overhead of writing to the vault outweighs the benefit of having agents read from it later. Use case 1 (self-repair) is still worth the five minutes to set up. The rest is overkill.

**If you're cost-sensitive and already on a Claude subscription, do the math carefully.** Claude Code Pro/Max plans bundle Opus usage in ways that can make the expensive-model-does-typing problem less acute than pure API pricing suggests. I moved to the supervisor-builder pattern partly because I'm on API-pay-as-you-go across multiple accounts. If you're on a fat subscription tier, the savings from handing execution to Codex might not justify the complexity.

**The context sync friction is real.** Both agents are writing to the vault at the same time, and sometimes they step on each other. I've had Codex mid-write when Claude Code tried to read, and the read came back with a half-written file. I solved it with atomic writes (write to `file.tmp`, then mv to `file.md`) and a lightweight file-lock convention. It works, but it's one more thing to manage.

**When a single agent is still the right call.** Tight feedback loops where you're iterating on the same file every two minutes. Exploratory work where you don't know what the plan is yet. Any task where the overhead of serializing a plan to markdown costs more than just doing the thing. Don't over-engineer when the job fits in one agent's context.

## What Actually Changes Day-to-Day

The single biggest shift isn't cost or speed. It's *resilience*.

When Claude Code broke on me last month, I didn't notice until the next morning because Codex repaired it in the background and logged the whole incident. That used to be a two-hour derail. Now it's a diff in the Obsidian log.

When I kick off a content batch, I'm not watching it the whole time. The monitor catches drift. The shared memory means I can step away for three hours and come back knowing exactly where both agents left off. My Saturday used to involve babysitting Aria through a batch of twenty posts. Now I queue the batch, take my daughter to the park, and check the Telegram ping feed when I'm back.

And the token bill — which I mentioned is down roughly 40% — isn't just savings. It's freedom to run the pipeline more aggressively. At the old cost per run, I had to be careful about which work I assigned to Opus. At the new cost, I can afford to plan more, review more, and iterate more.

There's a version of this story where I tell you the two-agent setup is the future of AI coding workflows and everyone needs it. That's overblown. The truth is narrower: if you run Claude Code as a critical part of your daily workflow, and you've ever lost a morning to a CLI that broke at exactly the wrong moment, you owe it to yourself to add a second CLI you can fall back on. Everything else — the supervisor-builder pattern, the monitor loop, the shared Obsidian memory — is optional elaboration on that core idea.

Back to that 2:17 AM Tuesday I opened with. The reason Aria sat frozen for two hours wasn't because Claude Code broke. Claude Code breaks sometimes. Everything breaks sometimes. The reason I lost the night was that I'd built a system where one CLI was a single point of failure for my entire content operation across four brands. That was the actual bug. The two-agent workflow is the patch.

Set up the repair loop tonight. You don't need the rest of it yet. Just get Codex installed in a second terminal, write the five-line repair prompt, and save it as a slash command. The next time Claude Code breaks after an auto-update — and it will — you'll have a 30-second recovery instead of a two-hour scramble. That alone pays for the setup. Everything else you add after is gravy.

## Frequently Asked Questions

### Can I use Gemini CLI or GLM instead of Codex as the second agent?
Yes — the key requirement is that the assistant is a different CLI from a different vendor so it doesn't break at the same time as Claude Code. Gemini CLI works well for the monitor and repair loops. GLM or other open-weight local setups can drive the cost even lower if you have the hardware. The supervisor-builder pattern works with any capable coding model; Codex is my pick because GPT-5.4 and GPT-5.3-Codex-Spark hit a sweet spot for execution tasks.

### Is Opus 4.6 still available in April 2026, or do I have to use Opus 4.7?
Opus 4.7 became the default Opus model on April 16, 2026, but Opus 4.6 remains available during a transition period across the Anthropic API, Amazon Bedrock, Google Cloud Vertex AI, and Microsoft Foundry. Pricing is identical ($5 input / $25 output per million tokens). For the supervisor-builder pattern I recommend Opus 4.7 — the SWE-bench gains on hard tasks are worth it.

### How much does the two-agent workflow cost compared to running Claude Code alone?
For my content pipeline, the two-agent split reduced token spend by roughly 40% month-over-month because Codex handles execution work that doesn't need Opus-grade reasoning. The exact savings depend on your workload mix. If you're already on a flat-rate Claude subscription, the savings shrink; if you're on API pay-as-you-go, the split pays for itself quickly.

### Will the Codex Plugin for Claude Code replace this two-CLI setup?
The Codex Plugin for Claude Code lets you call Codex directly from inside Claude Code, which is a tighter integration for the supervisor-builder pattern specifically. It doesn't solve the CLI-level failure mode — if Claude Code's CLI itself breaks, the plugin inside it can't repair it. For that, you still need Codex running in a separate terminal as an independent process.

### What should my Obsidian vault structure look like for two agents?
Three top-level folders under `agents/`: one for each agent's private logs, and a `shared/` folder with plans, decisions, and context both agents read and write. Date every file, keep each file single-purpose, and use atomic writes (temp file + rename) to avoid race conditions when both agents write simultaneously. The full structure is covered in the Shared Obsidian Memory section above.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
