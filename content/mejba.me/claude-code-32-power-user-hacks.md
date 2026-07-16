**BRAND:** mejba.me
**TITLE:** 32 Claude Code Hacks I Use Every Single Day
**META TITLE:** 32 Claude Code Hacks That Changed How I Ship
**SLUG:** claude-code-32-power-user-hacks
**PRIMARY KEYWORD:** Claude Code hacks
**META DESCRIPTION:** 32 Claude Code hacks I use daily — ultrathink, sub-agents, hooks, /statusline, /loop, Context7 MCP, and the settings.json tweaks that make me 5x faster.
**TAGS:** Claude Code, AI Development, Developer Productivity, Workflow Automation, Power User Tips

---

I almost closed the YouTube tab when the title said "31 Claude Code hacks." I've watched fifty of those videos. Most of them are someone reading the official docs out loud while a screen recorder runs. Then I noticed the timestamps. Beginner. Intermediate. Advanced. Each section had ten plus tips. By the time I got halfway through, I'd already paused twice to update my own `~/.claude/settings.json`, install a Context7 MCP server, and rebuild a sub-agent that was burning through my Opus budget for no reason.

There were thirty-two hacks, not thirty-one. The creator miscounted. Three of them I'd been using wrong for weeks. Two of them I'd never seen before, and one of those is now the single biggest reason my agent runs cost 60% less than they did in March.

So I sat down and ran every single hack against my actual workflow. The agent farm I run for [my mejba.me content pipeline](https://mejba.me/automate-seo-content-claude-code), the [Ramlit client builds](https://mejba.me/ai-agency-retainer-model-2026-claude-code), the security automations I'm prototyping. Some of these hacks are obvious once you know them. A few are the kind of thing only people who've shipped hundreds of Claude Code sessions stumble into. All thirty-two are below, ordered the way I'd teach them to a developer sitting next to me — beginner first, then intermediate, then the advanced moves that actually scale.

If you only read one section, make it the advanced one. That's where the cost optimization lives.

## Why Most Developers Use Claude Code at 30% of Its Potential

Here's what nobody tells you about Claude Code: the gap between a casual user and a power user isn't talent. It's about ten specific commands and a couple of mental model shifts. The casual user types prompts and waits. The power user has a status line showing live context burn, a sub-agent on Haiku doing exploration in parallel, a hook auto-formatting on every save, and a `/loop` running in the background while they sleep.

I learned this the hard way. For six months I was generating decent code at maybe a third of the throughput I'm at now. Same model. Same skill. The difference was workflow density — how many useful Claude Code primitives I was stacking inside a single session. Once I started treating every keyboard shortcut, every slash command, and every settings.json key as a tool worth mastering, the productivity curve went vertical.

This guide is the version of that curve I wish someone had handed me last year.

## Beginner: The Ten Hacks Every Claude Code User Should Know in Week One

These are non-negotiable. If you're skipping any of them, you're paying tax for nothing.

### 1. Run `/init` Before You Type a Single Prompt

The first thing I do in any new repo is run `/init`. It walks the codebase, identifies the stack, and writes a `CLAUDE.md` that captures conventions, file structure, build commands, and the kind of context that would otherwise eat your first three prompts. Skipping it means Claude starts every conversation cold, asking questions you've already answered.

What surprised me: `/init` is good enough that I rarely rewrite the file from scratch anymore. I edit it. I trim it. But the bones are usually right. If you've been writing `CLAUDE.md` files manually since 2025, stop. Let `/init` draft it. Then you can sharpen it.

### 2. Configure `/statusline` So You Can See What's Actually Happening

This one changed my workflow more than any other beginner tip. Run `/statusline` and Claude will set up a custom dashboard at the bottom of your terminal showing the current model, working directory, remaining context window, session cost, git branch, and anything else you want to surface. I run a `claude-pace`-style status line that shows percent context used, dollar-per-session burn, and 5-hour rate limit usage.

Why does this matter? Because Claude Code without a status line is like driving without a fuel gauge. You only realize you've burned 80% of your context when responses start getting weird. With a status line, I can see context creep in real time and `/compact` before it bites me.

### 3. Use Voice Input for the Long Prompts

Apple's built-in dictation, Whisper Flow, or [SuperWhisper](https://superwhisper.com) — pick one. Anything that turns spoken thought into a five-hundred-word prompt. I dictate plans, bug reports, and feature briefs because typing them takes four times longer and I leave out details when I'm typing.

The shift in quality is real. Voice prompts are denser. They include the half-thoughts and edge cases I'd have skipped if I were typing on a Tuesday afternoon with three Slack windows open.

### 4. Keep Your Context Window Tiny by Default

Every file Claude reads, every tool result, every conversation turn — it all lives in the context window. That window is finite. The more you stuff in early, the less room there is for actual reasoning later. My rule: I don't load a file unless I have a specific reason. I don't dump entire directories. I let Claude pull what it needs through grep and read tools, not through me preemptively pasting code.

I learned this the hard way during a Laravel refactor when I pasted a 2,000-line controller "for context" and Claude proceeded to forget the original task ten turns later. Tiny context, sharper output. Always.

### 5. Run `/context` When Things Start Feeling Off

`/context` shows you exactly what's eating your token budget. Conversation history. Tool results. CLAUDE.md content. System prompt. MCP server outputs. Each gets a percentage. The first time I ran it on a misbehaving session I discovered a Playwright MCP server was holding 18% of my context with browser snapshots from forty turns ago. Killed the snapshot. Problem solved.

I run `/context` whenever Claude does something inexplicably dumb. Eight times out of ten the issue is context pollution, not the model.

### 6. Use `/compact` at 60-70%, `/clear` Between Tasks

The folk wisdom is "compact at 80%." That's too late. By 80% Claude is already getting sloppy because the context-to-attention ratio has degraded. I `/compact` at 60-70% with a focused argument: `/compact retain the schema and the failing test cases`. The compact summary becomes the new working context.

When I switch tasks entirely — say, I'm done with a feature and now I'm debugging a deploy — I `/clear`. No compact. Full reset. Mixing two unrelated tasks in one session is one of the fastest ways to make Claude hallucinate APIs that don't exist.

### 7. Plan Mode Is Shift+Tab. Use It for Anything Touching More Than One File

`Shift+Tab` toggles plan mode. In plan mode, Claude analyzes your codebase and produces an implementation plan without writing any code. You review the plan, edit it, approve it, then execution starts.

I've made this non-negotiable for any change that touches more than one file. The ten seconds you spend reading the plan saves you the three hours of debugging when Claude decides to add rate limiting in a new middleware file instead of the auth middleware that already exists. (Actual experience. Actual three hours. Never again.) [I broke the full plan-validate-ship cycle down here](https://mejba.me/claude-code-advanced-workflow-guide).

### 8. Treat Claude Like a Smart Junior Developer, Not a Senior

This is a mental model shift, not a command. Junior developers are brilliant but they need structure. They need clear specs, code review, and someone catching architectural decisions before they become tech debt. Treat Claude the same way. Brief it like a junior. Review its output like a junior. Don't trust it on architecture without checking.

The developers who get burned by Claude Code are the ones who treat it like a senior — push a vague prompt and walk away. The ones who ship are the ones who write tight specs and review the diff.

### 9. Force Clarifying Questions Until Confidence Hits 95%

Add a single line to your `CLAUDE.md`: *"Before writing code, ask clarifying questions until you are 95% confident in the requirements. Do not guess. Do not assume."* The behavior shift is dramatic. Instead of generating a half-right implementation that takes thirty minutes to fix, Claude asks the four questions that pin down the spec before writing anything.

I tested this on a complex Stripe integration last month. Without the rule, Claude assumed the wrong subscription model and shipped sixty lines of code that needed to be rewritten. With the rule, it asked three questions, got the spec right, and shipped working code first try.

### 10. Self-Checking Todo Lists With Visual Verification

When I assign a multi-step task, I tell Claude to maintain a todo list and verify each step before marking it complete. For UI work that means a screenshot. For backend work that means hitting the endpoint and showing me the response. For database work that means running a query and pasting the result.

The change in quality is sharp. Without verification, Claude marks things "done" because it wrote the code. With verification, "done" means it actually works. There's a massive difference between those two states, and most agent failures live in the gap between them.

That's the beginner stack. Get those ten dialed in and you're already operating above 80% of Claude Code users. The intermediate hacks are where the real leverage starts.

## Intermediate: The Twelve Hacks That Separate Casual From Serious

This is the layer where Claude Code stops being a chatbot and starts being a system you architect.

### 11. Deploy Sub-Agents in Parallel for Anything Bigger Than a Single Feature

Sub-agents are spawned-off Claude instances with their own context window, their own tool access, and (critically) their own model assignment. You define them in `~/.claude/agents/[name].md` or `.claude/agents/[name].md` for project-scoped ones.

The pattern that broke through for me: when I'm building anything with three or more independent components, I delegate each component to a sub-agent. While I'm reviewing the auth implementation, the database sub-agent is finalizing migrations and the frontend sub-agent is wiring components. Three things happening in parallel where I used to do one.

[I documented the agent team architecture I run here](https://mejba.me/claude-agent-teams-guide). Once you experience parallel sub-agents, going back to single-threaded feels like dial-up.

### 12. Build Custom Skills in `~/.claude/skills/`

Skills are reusable instruction packs Claude loads automatically when their description matches the task. Each skill is a directory with a `SKILL.md` file. The frontmatter tells Claude when to use it. The body tells Claude what to do.

I have skills for SEO content generation, Laravel testing patterns, and the specific way I write commit messages. Whenever I trigger a matching task, the skill activates without me typing anything. It's like having permanent CLAUDE.md fragments that only load when relevant. [I covered the deeper skill patterns in this guide](https://mejba.me/agent-skills-advanced-claude-code).

The win: skills don't pollute context the way a giant CLAUDE.md does. They load on demand and unload when done. That's the right shape for specialized knowledge.

### 13. Route Sub-Agents to Haiku to Cut Costs by Half

This is the single biggest cost optimization in Claude Code. Sub-agents inherit the parent model unless you specify otherwise. If you set `model: haiku` in the sub-agent frontmatter, that sub-agent runs on `claude-haiku-4-5` instead of Opus. Haiku is roughly 15x cheaper per token than Opus and on tasks that don't require deep reasoning — file searching, log parsing, codebase exploration, JSON formatting — the quality gap is essentially zero.

My current setup: planning and architecture run on Opus. Implementation runs on Sonnet. Exploration, log analysis, and routine refactors run on Haiku. Three-tier routing dropped my average session cost from $2.02 to $0.98 according to the math I ran in March. That tracks with the 40-60% reduction the broader Claude Code community is reporting.

### 14. Refresh `CLAUDE.md` Constantly. Keep It Under 200 Lines

Every line in `CLAUDE.md` gets loaded into every conversation. A 500-line file is silently eating context before you've typed a prompt. The discipline that works: cap the file at 150-200 lines and treat anything below that ceiling as a forcing function for prioritization.

What stays: project description, key file paths, build/test commands, coding conventions, hard rules Claude must never violate. What goes: code examples (Claude can read your code), historical context, anything that duplicates the README, anything that hasn't been touched in two weeks.

I refresh `CLAUDE.md` roughly every Friday on active projects. Five minutes of pruning, ten minutes of adding new lessons learned that week. The compound benefit is huge.

### 15. Route `CLAUDE.md` to Linked Subdirectory Files

For larger projects, the trick is to keep the root `CLAUDE.md` as a router, not a manual. The root file says "see `docs/conventions.md` for our code style, see `docs/architecture.md` for the system design, see `docs/deploy.md` for deployment notes." Claude reads the router, then pulls only the linked file relevant to the current task.

This pattern is what lets a sprawling Laravel monorepo keep a 120-line root CLAUDE.md while still having deep, specific guidance available on demand. Modular context. Loaded only when needed.

### 16. Exit Early and Re-Ask When Things Drift

If a Claude session is going sideways — wrong direction, hallucinated API, repeated mistakes — don't try to correct it inside the same session. Exit. Open a fresh session. Re-ask with a sharper prompt and the lessons you learned from the bad run.

The reason: once a session has drifted, the bad context is poisoning every subsequent turn. Trying to course-correct often makes it worse. A fresh session with a tighter prompt is almost always faster than five turns of "no, like this."

### 17. Challenge Claude's Output Aggressively

When Claude returns something that "looks right," ask it to find three problems with what it just wrote. Or tell it: *"Critique this implementation as if you were a senior engineer in code review. What would you push back on?"*

The output quality jumps. Claude is genuinely good at finding flaws in code when you frame the task as critique instead of generation. I caught a race condition in a payment flow last month using exactly this prompt. The original implementation passed tests. The critique pass found the timing bug.

### 18. `/rewind` Is Your Quick-Undo Button

Press `Esc` twice or run `/rewind` and you get a checkpoint menu showing every previous state of the conversation. Pick a checkpoint, restore. The 2026 update added the option to restore conversation-only or code-only — meaning you can roll back the messages while keeping file changes, or vice versa.

I use this constantly when I realize Claude went down the wrong path five turns ago. Instead of re-explaining everything, I rewind to before the wrong turn and try again with a better prompt.

### 19. `/hooks` for Notifications, Validation, and Auto-Format

Hooks are deterministic shell commands the harness runs at specific lifecycle points. Pre-tool-use. Post-tool-use. Stop. Notification. They run regardless of what Claude decides to do — that's the whole point.

My current hook stack: a post-tool-use hook that runs `prettier` on every TS file Claude edits, a stop hook that fires a macOS notification when a long-running task completes, and a pre-tool-use hook that blocks Bash commands matching `rm -rf` outside specific directories. The auto-format hook alone saves me ten minutes of cleanup per session.

Run `/hooks` to manage them inside the CLI. Don't memory-prompt your way to repeatable behaviors. Codify them as hooks.

### 20. Screenshots for Visual Self-Check

When Claude edits UI, I tell it to take a screenshot of the running page and verify the change matches the spec. With Playwright MCP installed, this is one command. The shift in quality is huge: instead of "I added the gradient" with no proof, you get "here's the gradient, here's the screenshot, here's what I see in the screenshot."

Catches alignment bugs, color drift, and the dozen tiny visual issues that text-only verification misses every time.

### 21. Chrome DevTools Integration for Live Debugging

Connect Claude to Chrome via the Playwright or DevTools MCP and you can have it open a browser, navigate to your dev server, inspect the DOM, read console errors, and verify behavior end to end. I do this for any frontend bug that doesn't reproduce on the first prompt.

The session feels like pair programming with someone who has a browser open at all times. They click. They check the console. They report back. Massive level-up over guessing from code alone.

### 22. Clone Inspiration Sites by Screenshot

This one is pure power-user territory. Take a screenshot of a site you want to mimic, hand it to Claude, ask it to reproduce the layout in your stack. With a vision-capable model and good design tokens in your project, you get a working clone in fifteen minutes that would have taken a frontend dev half a day.

I've used this for landing pages, dashboards, and pricing tables. The output isn't pixel-perfect — but it's close enough that the manual polish is fifteen minutes instead of three hours. [I went deeper on the visual cloning workflow here](https://mejba.me/ai-website-cloning-recurring-revenue).

That's the intermediate stack. With these twelve dialed in, you're operating like a senior who's been using Claude Code for a year. Now the advanced moves — the ones that take you into the territory where Claude Code stops being a tool and starts being infrastructure.

## Advanced: The Ten Hacks That Turn Claude Code Into Infrastructure

This is the layer where serious operators live. Most Claude Code users will never touch any of this. The ones who do operate at a multiple of throughput.

### 23. Parallel Sessions With Git Worktrees

`git worktree add ../feature-payments feature/payments` creates an isolated working directory tied to a branch. You launch a separate Claude Code session in that worktree, completely isolated from your main session — different files, different state, no conflicts. Boris Cherny, the creator of Claude Code, [reportedly runs ten to fifteen parallel sessions](https://mejba.me/boris-cherny-claude-code-workflow) using this exact pattern.

My current ceiling is four parallel worktree sessions. Beyond that I lose track of what's happening where. Four is enough to feel superhuman. While auth ships in worktree A, the payment integration is being built in worktree B, the dashboard redesign is rendering in worktree C, and the Stripe webhook handler is running tests in worktree D. I'm reviewing PRs in a fifth window. That's a feature week compressed into an afternoon.

### 24. Hit API Endpoints Directly Instead of Loading the MCP Server

MCP servers are amazing. They're also expensive in tokens. Every MCP server registers tools that get loaded into Claude's context whether you use them or not. A heavyweight MCP can eat 5-10% of your context budget on tool definitions alone.

The hack: for one-off API interactions, skip the MCP and have Claude call the endpoint directly with `curl` or a simple HTTP client. You spend a few hundred tokens on a single tool call instead of ten thousand on persistent MCP tool definitions. I keep MCP servers for tools I use in 50%+ of sessions. Everything else goes through direct API calls.

### 25. `/loop` for Recurring Background Tasks

`/loop` lets you run a prompt or a slash command on a recurring interval. *"/loop 30m check the deploy logs and ping me if there's an error"* runs every thirty minutes. Omit the interval and Claude self-paces. The harness can keep loops alive for up to three days.

I run loops for SEO checks, content publishing pings, security scans, and a build-status babysitter that watches CI and tells me when something breaks. The trick is to keep loop prompts narrow — a loop with a vague mandate becomes expensive fast. Narrow scope. Clear exit condition. Specific report format.

### 26. Host Claude Code on a VPS for Always-On Agents

If you want loops running 24/7 without your laptop being open, deploy Claude Code on a VPS. A cheap DigitalOcean droplet or Hetzner box runs a `tmux` session with Claude Code in it, your loops fire on schedule, and you SSH in to check status from anywhere.

I have a $20/month Hetzner box running my content monitoring loop and a security scan loop. Both have been alive for six weeks. I check in once a day. The VPS becomes a persistent agent runner instead of a disposable session.

### 27. Remote Control Claude From Your Phone Via Browser

Tunnel your VPS Claude session through `ttyd`, `gotty`, or a similar terminal-in-browser tool, lock it behind HTTPS and basic auth, and you can drive Claude Code from your phone. I've shipped fixes from a coffee shop, an airport, and once from a bus on the way home.

Not for heavy work. Perfect for quick "hey, restart that loop" or "check the deploy status" interactions when you're away from the laptop.

### 28. Query NoSQL and BigQuery in Plain English Through the CLI

Install a database MCP server (Firebase, Supabase, BigQuery, MongoDB — most have one) and you can ask Claude things like *"how many signups in the last 24 hours from US users on the pro plan?"* It writes the query, runs it, parses the result, and gives you a one-line answer.

The shift is from SQL-as-skill to data-as-conversation. I still write hand-tuned queries for production analytics. For exploratory questions during a meeting? Plain English through Claude. Ten times faster than opening the BigQuery console.

### 29. Ultrathink for the Hard Problems

Anthropic explicitly recommends magic words that scale Claude's thinking budget. The hierarchy: `think` → `think hard` → `think harder` → `ultrathink`. Each step allocates more thinking tokens. `ultrathink` triggers roughly 32K thinking tokens — basically the maximum reasoning Claude will deploy on a single response.

Use sparingly. `ultrathink` is expensive and slow. But for genuinely hard problems — architectural decisions, gnarly bugs, security analysis on a complex auth flow — it's the difference between a surface-level answer and one that catches the things a senior engineer would catch.

My rule: I use `ultrathink` maybe twice a session, on problems where the wrong answer costs more than the extra tokens.

### 30. Edit Permissions in `settings.json` to Pre-Approve Safe Commands

Stop saying yes to every prompt. Open `~/.claude/settings.json` and add:

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(pytest *)",
      "Bash(prettier *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(git push --force *)",
      "Bash(curl * | sh)"
    ],
    "ask": [
      "Bash(git push *)",
      "Bash(npm publish *)"
    ]
  }
}
```

Allow rules let Claude run safe commands without prompting. Deny rules block destructive commands no matter what (deny always wins over allow). Ask rules force confirmation for sensitive operations.

The difference is night and day. With a good permission file, my agent loops run autonomously for hours without me clicking "approve" twenty times. Without it, every loop iteration grinds to a halt waiting for my attention. [The full settings.json deep dive is here](https://mejba.me/anthropic-agent-sdk-guide).

### 31. Build Agent Teams With Shared Context

This is where it gets architectural. An "agent team" is a collection of specialized sub-agents — a planner, a coder, a tester, a reviewer — each with its own role, model, and tool access. They communicate through a shared markdown file (`team-state.md` or similar) that each agent reads at the start of its turn and writes at the end.

The planner reads the spec, drafts a plan, writes it to `team-state.md`. The coder reads the plan, implements it, writes the diff to `team-state.md`. The tester reads the diff, runs the tests, writes the results. The reviewer reads everything, signs off or sends back. All four agents are different Claude Code sub-agents on appropriate models — Opus for the planner, Sonnet for the coder, Haiku for the tester. [I covered the team architecture in detail here](https://mejba.me/anthropic-agent-teams-ai-coding).

This is the architecture pattern that scales. One human. Four specialized agents. A shared context file. Throughput that genuinely feels unfair.

### 32. Context7 MCP for Version-Specific Documentation

Last one. The single biggest hack I've adopted in the last sixty days. The Context7 MCP from Upstash injects up-to-date, version-specific library documentation directly into Claude's context the moment you reference a library.

Without Context7, Claude generates code based on whatever it remembers from training — which means deprecated APIs, wrong import paths, and functions that don't exist in the version you're using. With Context7, Claude pulls the actual current docs for the exact version of the package in your `package.json` and writes code that works first try.

Install it once:

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp
```

Restart Claude Code. Done. Now whenever you say "build a Next.js 15 server action" or "write a Drizzle ORM migration," Context7 fetches the current docs in the background and Claude codes against them. The hallucinated-API problem essentially disappears for any library Context7 supports.

This is the hack I'd recommend to a Claude Code user who only has time to implement one thing from this entire post.

## What Actually Changes When You Run All 32 Together

Here's the part that matters. Any one of these hacks is incremental. A few percent better. Five minutes saved. Slightly cheaper sessions. Implement all thirty-two and the curve goes nonlinear.

My personal numbers, March vs April: average session cost down 60%, throughput up roughly 4x, hallucination rate down by an order of magnitude (mostly thanks to Context7 plus tighter CLAUDE.md hygiene), and time spent clicking "approve" down to near zero because of the settings.json permission rules.

That's not because any single hack is magic. It's because the hacks compound. Better context discipline plus cheaper sub-agents plus auto-formatting hooks plus parallel worktrees plus permission pre-approval equals an environment where Claude Code is actually running unsupervised for hours at a time and producing work I'd ship without rewriting.

The mental model shift is real. Stop treating Claude Code like a chatbot. Start treating it like a developer environment you architect. Every hack in this post is a piece of that architecture.

If you've made it this far, here's the one piece of advice I wish I'd given myself a year ago: don't try to implement all thirty-two at once. Pick three from the beginner section, get them dialed in over a week. Then add three from intermediate. Then ratchet up to advanced. The compound effect builds layer by layer, not in a single weekend.

The last hack is the only one that doesn't fit on this list: keep adding to it. Every week you'll find a new pattern, a new MCP server, a new hook that saves another fifteen minutes. The thirty-second hack is the meta-hack — the discipline of treating your own Claude Code workflow as a thing worth optimizing every week.

Mine is unrecognizable from what it was three months ago. Yours will be too.

## Frequently Asked Questions

### What is the single best Claude Code hack for cutting costs?
Routing sub-agents to Haiku (`model: haiku-4-5` in the sub-agent frontmatter) is the single biggest cost lever. It typically cuts session costs by 40-60% with negligible quality loss on exploration, search, and routine tasks. For full implementation see hack #13 above.

### How do I trigger ultrathink in Claude Code?
Type `ultrathink` literally in your prompt. Anthropic recognizes a hierarchy of magic words — `think`, `think hard`, `think harder`, `ultrathink` — that scale the thinking token budget. `ultrathink` allocates roughly 32K thinking tokens, the maximum. Use it for architectural decisions and hard bugs only.

### What is the difference between /compact and /clear?
`/compact` summarizes your current conversation to free context while keeping continuity — useful when you hit 60-70% context usage and want to keep working on the same task. `/clear` wipes the conversation entirely — use it when switching to a new, unrelated task. Mixing the two incorrectly is a top cause of Claude hallucinating mid-session.

### Should I use Context7 MCP for every project?
Yes, if you work with rapidly-changing libraries (Next.js, React, Drizzle, Supabase, anything in active development). Context7 fetches version-specific documentation on demand and essentially eliminates hallucinated APIs. Install once with `claude mcp add context7 -- npx -y @upstash/context7-mcp`. See hack #32.

### How do I run Claude Code in parallel without conflicts?
Use git worktrees. `git worktree add ../feature-name feature/branch-name` creates an isolated working directory, then launch a separate Claude Code session inside it. Each session has its own files and state. Boris Cherny runs ten to fifteen parallel sessions this way; four is a sustainable ceiling for most developers. See hack #23.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
