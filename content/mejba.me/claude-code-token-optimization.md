**BRAND:** mejba.me
**TITLE:** Claude Code Token Optimization: Double Your Session
**META TITLE:** Claude Code Token Optimization: Double Your Session
**SLUG:** claude-code-token-optimization
**PRIMARY KEYWORD:** Claude Code token optimization
**SECONDARY KEYWORDS:** Claude Code token usage, Claude Code context management, Claude Code session limits
**META DESCRIPTION:** I cut my Claude Code token usage by 60% using four commands and a handful of settings. Here's the exact strategy to double your productive session time.
**TAGS:** Claude Code, Token Optimization, AI Development, Context Management, Guide
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know exactly which commands, settings, and project structures reduce Claude Code token waste — and be able to double their effective session length starting today.

---

# Claude Code Token Optimization: How I Doubled My Productive Session Time

I hit my Claude Pro limit 47 minutes into a session last Tuesday. Not a complex session. Not a massive refactor. I was building a simple CRUD dashboard — the kind of thing that should take maybe 20 minutes of focused Claude Code work. But somewhere between the third retry on a broken component and the context window silently ballooning past 150,000 tokens, the session just... stopped.

The message was polite about it. "You've reached your usage limit. Try again in approximately 4 hours and 13 minutes." Four hours. For what should have been a 20-minute task.

That was the moment I stopped treating Claude Code like an infinite resource and started treating it like a budget. A token budget. And the difference that shift made was staggering — within a week, I was accomplishing roughly twice the work per 5-hour window, on the same Pro plan, without changing what I was building.

The fix wasn't one thing. It was a combination of four specific commands most people underuse, a set of configuration changes that eliminate hidden token drains, and a project structuring philosophy that keeps context lean by default. I'll walk through all of it, starting with the thing that surprised me most: how much of my token budget was being wasted on stuff I never even saw.

---

## What's Actually Eating Your Tokens

Before I started optimizing, I assumed my tokens were going where you'd expect — to my prompts and Claude's responses. Turns out, that's maybe 40% of the story. The rest disappears into what I call "invisible context": the stuff Claude Code loads, retains, and processes that you never explicitly asked for.

Here's what I found was silently consuming my token budget:

**The CLAUDE.md tax.** Every single message you send, Claude Code reads your `CLAUDE.md` file. If that file is 800 lines of detailed instructions, architecture notes, and coding standards — which mine was — that's thousands of tokens loaded on every turn. Not once per session. Every. Single. Turn. I was effectively paying a 3,000-token toll booth on every prompt I typed.

**Retry pollution.** When Claude generates a response and you don't like it, you prompt again. Reasonable. But the failed response doesn't disappear. It stays in context. So now you're paying for the bad output AND the new prompt AND the new output. Three turns where you expected two. Compound this across five or six retries on a tricky function, and you've burned 30,000 tokens on code you threw away.

**Skill injection bloat.** Claude Code injects available skill listings into context even when they're completely irrelevant to your task. If you have a dozen MCP servers connected, each one adds tool definitions to every context window. I measured this on one of my projects — MCP tool definitions alone were consuming roughly 15,000 tokens per turn. For tools I wasn't even using.

**Truncated response echo.** This one is particularly wasteful. When Claude's response gets cut off mid-generation and you ask it to continue, the partial response stays in context alongside the completed one. You end up carrying duplicate content forward — sometimes 50-70% overlap — paying double for the same code.

The pattern across all four: token waste scales with session length. A fresh session is lean. Thirty minutes in, you're dragging a bloated context behind every new prompt like a parachute on a drag racer.

Understanding this changed my entire approach. The goal isn't just to write shorter prompts — it's to actively manage what lives inside that context window throughout the session.

---

## The Four Commands That Changed Everything

Claude Code has built-in commands specifically designed for context management, but most developers either don't know about them or use them wrong. I went from ignoring all four to using them as reflexes — and the impact was immediate.

### /clear — The Hard Reset

The `/clear` command does exactly what it sounds like: it wipes the entire conversation history. Every prompt, every response, every file read — gone. You get a fresh context window, which typically frees up around 150,000 tokens in a loaded session.

When I first heard about `/clear`, my reaction was "why would I throw away all that context?" It felt like rebooting my computer to fix a slow browser — technically effective but brutally wasteful. I was wrong about this. Completely wrong.

Here's what I learned: context from a completed task doesn't help you with the next task. It hurts you. Claude is spending tokens processing old code decisions, previous error messages, and resolved conversations — none of which are relevant to what you're doing now. Worse, stale context can actively confuse the model, causing it to reference patterns from the previous task that conflict with the current one.

**My rule now:** `/clear` after every completed task phase. Finished implementing a feature? Clear. Moving from backend to frontend? Clear. Done debugging and starting new work? Clear. Each phase gets its own clean context.

This single habit probably accounts for 30% of my token savings. The mental model shift is treating a Claude Code session not as one long conversation, but as a series of short, focused sprints.

### /compact — The Smart Compression

Where `/clear` is a sledgehammer, `/compact` is a scalpel. It takes your entire conversation history — every prompt, response, file read, and code generation — and compresses it into a dense summary. The summary retains the essential decisions, current code state, and important context while discarding the verbose back-and-forth that got you there.

A typical `/compact` saves around 40,000 tokens. That's significant. On a Pro plan, that's the difference between hitting your limit and getting another 20-30 minutes of productive work.

But here's the trick most people miss: you can steer what `/compact` retains by passing instructions with it.

```
/compact Focus on the authentication module and the database schema decisions we made
```

This tells Claude which context matters most, so the compression preserves what you actually need going forward. Without instructions, `/compact` makes its own judgment about what's important — and it's usually decent, but not perfect. When I started giving it explicit focus instructions, the post-compaction context was noticeably more useful.

**My rule:** `/compact` when you're mid-task but the context feels heavy. Specifically, I compact when I notice Claude starting to repeat suggestions it already made, or when responses slow down noticeably. Both are signals that the context window is getting bloated.

Don't wait for auto-compaction to kick in. By the time Claude Code auto-compacts, you've already been paying the bloat tax for several turns. I compact proactively at around 50% window utilization — before it becomes a problem.

### /by the way — The Side Channel

This is the command I slept on longest, and it might be the most elegant solution to a problem I didn't even realize I had.

When you're deep in an implementation and a quick question pops up — "wait, what's the correct syntax for a TypeScript generic constraint?" or "how does the Prisma `upsert` method handle conflicts?" — your instinct is to just ask Claude in the current session. It's right there. It knows your project.

The problem: that question and its answer now live in your main context permanently. A 200-token question and a 500-token answer just cost you 700 tokens of context space that has nothing to do with the feature you're building. Do this four or five times in a session, and you've wasted 3,000-4,000 tokens on sidebar questions.

`/by the way` (or just `btw`) runs your question in a separate context window. The answer comes back to you, but it never touches your main conversation. Your implementation context stays pristine.

**My rule:** Any question that starts with "quick question" or "how do I" or "what's the syntax for" goes through `/by the way`. If it's not directly about the specific code I'm writing right now, it doesn't belong in my main context.

### /rewind — The Undo Button

Here's a scenario that used to cost me thousands of tokens: Claude generates a solution that's completely off-base. Wrong approach. Bad architecture. Doesn't match what I asked for. My old workflow was to explain what went wrong, ask it to try again, and hope the next attempt was better. But the bad attempt stays in context, consuming tokens and potentially influencing the retry.

`/rewind` solves this by reverting the conversation to a previous checkpoint. The bad output gets removed from context entirely — it's as if it never happened. You can also trigger it by pressing Escape twice.

The token savings here are asymmetric. A single bad response from Claude might be 2,000 tokens. Without `/rewind`, you pay for those 2,000 tokens plus the correction prompt plus the new response. With `/rewind`, you pay only for the new attempt. On complex tasks where the first attempt frequently misses the mark, this adds up fast.

**My rule:** If Claude's response is more than 30% wrong, don't try to correct it — rewind it. Corrections are expensive because you're paying for the error, the correction prompt, and the corrected output. Rewinding removes the error entirely and gives you a clean retry.

---

## The CLAUDE.md Diet That Saved Me 60% Context

This is the optimization that made me genuinely angry at my past self. My `CLAUDE.md` file had grown to 847 lines over three months. It contained project architecture explanations, coding standards, file naming conventions, API documentation references, deployment procedures, testing guidelines, and a "do's and don'ts" section that was longer than most blog posts.

I was proud of it. Look at how thorough my project configuration is! Look at how much Claude knows about my project!

Then I did the math. At roughly 4 tokens per word, my 847-line `CLAUDE.md` was approximately 3,400 tokens. Loaded on every single turn. Over a 50-turn session, that's 170,000 tokens consumed by project documentation alone — before I'd written a single prompt.

The fix was painful but necessary: I gutted it.

### What Belongs in CLAUDE.md (Under 300 Lines)

Your `CLAUDE.md` should contain only things Claude doesn't already know that apply to every single task. That's a much shorter list than you think.

**Keep:**
- Project-specific naming conventions that differ from standard practices
- Things Claude should NOT do (these prevent expensive mistakes and rewinds)
- Your specific development workflow (test before commit, lint before push, etc.)
- The tech stack with versions — but just the stack, not explanations of how each technology works
- File structure overview — the map, not the territory

**Remove:**
- Explanations of common frameworks (Claude knows how React, Next.js, and Laravel work)
- Standard coding practices (Claude already follows clean code principles)
- Architecture documentation (move it to separate files and reference only when needed)
- API documentation (link to it, don't embed it)
- Generic instructions like "write clean code" or "follow best practices" — these waste tokens on instructions Claude would follow anyway

### Progressive Loading: The Real Power Move

Here's the technique that brought the biggest improvement: instead of putting everything in `CLAUDE.md`, I split project-specific knowledge into separate documents and referenced them contextually.

```
# CLAUDE.md (lean version — 127 lines)

## Project Rules
- Never modify files in /core without explicit approval
- Always run tests after changing any service class
- Use snake_case for database columns, camelCase for TypeScript

## Tech Stack
- Next.js 15.2, TypeScript 5.7, Prisma 6.4, PostgreSQL 16

## Documentation References
- API specs: see /docs/api-spec.md (load only when working on API routes)
- Database schema: see /docs/schema.md (load only when modifying migrations)
- Component library: see /docs/components.md (load only when building UI)
```

The key phrase is "load only when." Claude Code is smart enough to pull in referenced files when they're relevant to the current task, but it won't load them on every turn. Instead of paying 3,400 tokens per turn for everything, I'm paying maybe 600 tokens per turn for the core rules, plus the occasional load of a specific doc when it's actually needed.

The difference over a session is enormous. My `CLAUDE.md` went from 847 lines to 127. My per-turn context overhead dropped by roughly 80%. And Claude's responses actually improved — with less noise in the context, the model focuses better on the actual task.

If you'd rather have someone set up this kind of optimized project structure from scratch, I take on Claude Code workflow optimization engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

---

## Settings That Stop the Silent Bleed

Beyond commands and project structure, Claude Code has a set of configuration options that most users never touch. Several of these control background processes that consume tokens without any visible output. Turning them off is free performance.

### Disable Auto-Memory When You Don't Need It

Claude Code has a background memory consolidation feature that periodically processes your conversation and saves observations to `MEMORY.md`. Useful in theory. Expensive in practice. Each memory consolidation cycle consumes tokens — and it runs automatically, whether you asked for it or not.

For focused development sessions where I'm building something specific, I disable it:

```json
// ~/.claude/settings.json
{
  "autoMemoryEnabled": false
}
```

Or set the environment variable: `export CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`

I still use memory for long-running projects where continuity across sessions matters. But for a single-session task — a quick feature build, a debugging session, a code review — auto-memory is pure overhead.

### Set Your Model Deliberately

This is the single highest-impact setting for token consumption. The model you choose determines how many tokens each message consumes from your quota.

Opus consumes approximately 3x the tokens per message compared to Sonnet. That's not a marginal difference — it's the difference between 45 effective messages per window and 135.

Here's my model selection framework after six months of daily use:

| Task Type | Model | Why |
|---|---|---|
| Complex multi-file refactors | Opus | Needs deep architectural reasoning |
| New feature implementation | Sonnet | Handles 90% of feature work perfectly |
| Writing tests | Sonnet | Pattern matching, not deep reasoning |
| Bug fixes from error messages | Sonnet | Usually straightforward once you show it the error |
| Quick syntax questions | Haiku | Fastest, cheapest, sufficient for lookups |
| Simple file edits | Haiku | Doesn't need a powerful model to change a string |

The mental model: Opus is for thinking. Sonnet is for building. Haiku is for answering. Match the model to the cognitive demand of the task, and you'll stretch your budget dramatically.

### Control Thinking Depth

Claude's extended thinking feature lets the model reason through complex problems before responding. Powerful for hard tasks. Wasteful for simple ones.

The effort level setting — which defaults to "auto" — determines how much reasoning Claude does. For trivial tasks, override it to "low":

```
/model sonnet low
```

This reduces the thinking budget, which directly reduces token consumption per response. For a simple rename or a straightforward CSS fix, you don't need Claude contemplating the philosophical implications of your variable names.

For tasks where you truly don't need any internal reasoning — updating a config file, adding an import statement — you can disable thinking entirely:

```
export CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING=1
```

I use this sparingly, but for batch operations where I'm making twenty similar small changes, it saves a meaningful number of tokens.

### Disable Unused MCP Servers

If you've connected MCP servers to Claude Code — Figma, Notion, Slack, database tools, whatever — each one injects its tool definitions into every context window. I had nine MCP servers connected. Seven of them were irrelevant to the project I was working on.

Those seven servers were adding roughly 10,000-15,000 tokens of tool definitions to every single turn. Not because I was using them. Just because they were connected.

Disconnect any MCP server you're not actively using in your current project. Reconnect when you need it. The context savings are significant, and the reconnection takes seconds.

### Prompt Caching: Leave It On

One setting you should NOT disable: prompt caching. Claude Code automatically caches common prefixes — your system prompt, `CLAUDE.md` contents, and repeated context — so you're not charged for the same tokens on subsequent turns.

If you see `DISABLE_PROMPT_CACHING` in your environment, remove it. Prompt caching is one of the few things working in your favor by default. According to Anthropic's documentation, cached tokens are significantly cheaper than fresh tokens, and the caching happens automatically without any quality degradation.

### Set Max Output Tokens

If you know a task should produce a short response — a quick answer, a small code snippet, a configuration change — set a token limit on the output:

```
export MAX_OUTPUT_TOKENS=2048
```

This prevents Claude from generating a 4,000-token response when you only needed 500. I use this for repetitive tasks where I know the expected output size. For open-ended work, I leave it at the default — you don't want to truncate a complex implementation mid-function.

---

## The Session Strategy: Sprints, Not Marathons

All the commands and settings above are tactical. This section is strategic — it's about how you structure your entire work session to minimize waste.

### Understand the 5-Hour Window

Claude's paid plans use a 5-hour rolling window for usage limits. This window starts when you send your first message and resets 5 hours later. The key word is "rolling" — it's not a fixed schedule. If you send your first message at 2:15 PM, your window resets at 7:15 PM.

Here's what most people miss: **the window runs whether you're working or not.** If you send five messages at 2:15 PM and then go to lunch for two hours, you don't get those two hours back. The window is still counting down. This means front-loading your most token-intensive work at the beginning of a window — when your full budget is available — is strategically important.

The approximate message limits per 5-hour window as of early 2026:

| Plan | Approximate Messages | Monthly Cost |
|---|---|---|
| Pro | ~45 messages | $20 |
| Max 5x | ~225 messages | $100 |
| Max 20x | ~900 messages | $200 |

These numbers vary based on model choice, task complexity, and server load. Anthropic has acknowledged that during peak usage times, limits can be reached faster than the advertised numbers suggest. The March 2026 community backlash — when users reported hitting limits 40-50% faster than expected — led Anthropic to commit to better transparency around usage tracking.

### The Sprint Protocol

Based on four months of tracking my own usage, here's the session structure that maximizes output per token:

**Sprint 1: Plan (5-10 minutes)**
Start every session by giving Claude a complete task brief. One message. Everything it needs to know about what you're building, your constraints, and your expected output. This single well-crafted prompt replaces the five-message back-and-forth of "no, I meant..." and "actually, can you also..." that burns tokens on clarification.

**Sprint 2: Build (15-25 minutes)**
Execute the plan. This is where most tokens go, and they're well-spent. Let Claude work through the implementation with minimal interruption. If you need to ask a side question, use `/by the way`. If a response is wrong, `/rewind` instead of correcting.

**Sprint 3: Clear and Shift (30 seconds)**
Task complete? `/clear`. Move to the next task with a clean context. Don't carry implementation details from Sprint 2 into Sprint 3 — they'll just waste tokens and potentially confuse the model.

**Sprint 4: Repeat**
Start the next task brief fresh. The cycle continues until your window runs out or your work is done.

This sprint approach typically gets me through 3-4 complete task cycles per session on a Pro plan, compared to 1-2 when I was treating the session as one continuous conversation.

### Avoid Token-Heavy Frameworks

Some project management frameworks — BMAD, SpecKit, and similar multi-file systems — are designed for AI-assisted development but can be catastrophic for token budgets on constrained plans. These frameworks often load multiple specification files, architecture documents, and workflow definitions into context, consuming tens of thousands of tokens before you've even started working.

If you're on the Pro plan ($20/month), avoid these entirely. The token overhead of loading their documentation structures can eat 20-30% of your session budget. Instead, use a minimal `CLAUDE.md` with progressive loading as described above.

On the Max 20x plan, these frameworks are usable — you have enough headroom to absorb the overhead. But even then, I'd argue that a lean project structure produces better results because it reduces noise in the context window.

---

## How Do You Know If Your Optimization Is Working?

You can't manage what you can't measure. After implementing these changes, I tracked three metrics across two weeks:

**Tasks completed per window.** Before optimization: 1.5 average. After: 3.2 average. This was the most visible improvement — I was simply getting more done before hitting limits.

**Messages before limit.** Before: 28-35 messages per window on Pro. After: 55-65 messages. The token savings from `/clear`, lean `CLAUDE.md`, and model selection roughly doubled my effective message count.

**Rewind frequency.** Before: 0 rewinds per session (I didn't know the command existed). After: 2-3 rewinds per session. Counterintuitively, rewinding more often correlates with fewer total tokens used, because each rewind prevents a costly correction cycle.

The patterns I've observed are consistent with what other developers report. According to a community analysis by Sabrina Dev, developers who actively manage their context window report 40-60% improvements in effective session length. A separate technical breakdown on Mintlify's Everything Claude Code guide documented similar gains specifically from the `/compact` and `/clear` workflow.

These aren't magical numbers. They're the predictable result of stopping the invisible token drains that most users don't even know exist.

---

## The Things Nobody Mentions About Token Optimization

I want to be honest about the trade-offs here, because most optimization guides present this stuff as pure upside with no cost.

**Context loss is real.** Every time you `/clear`, you lose context. If you cleared too early and need to reference something from the previous phase, you're either re-explaining it (spending tokens) or re-reading files (spending tokens). The sprint protocol helps, but it requires discipline about what constitutes a "complete task." I've cleared prematurely a handful of times and regretted it.

**Model selection isn't always obvious.** I said Sonnet handles 90% of work. That's true for my workflow. But some tasks that look simple actually require deep reasoning — complex debugging where the root cause isn't obvious, refactoring that touches shared state across many files. I've wasted tokens by starting on Haiku, failing, switching to Sonnet, failing again, and finally pulling in Opus. Starting on Sonnet would have been cheaper. You develop intuition for this over time, but there's a learning curve.

**Peak usage timing matters more than Anthropic admits.** The token limits aren't fixed numbers — they're influenced by server load. I consistently get 20-30% more work done during off-peak hours (early morning US time, weekends). This isn't documented anywhere officially, but the pattern is unmistakable after four months of tracking.

**These optimizations compound.** No single technique doubles your session. The `/clear` discipline saves maybe 30%. The lean `CLAUDE.md` saves another 20%. Model selection saves another 15-25%. MCP cleanup saves another 5-10%. Stack them, and the total improvement is dramatic. But skip any one, and you're leaving significant headroom on the table.

I'm also watching what Anthropic does with pricing and limits over the next few months. The March 2026 backlash led to promises of better usage visibility. If they deliver on that — actual token counters visible to users in real-time — the optimization game changes because you'll be able to measure impact directly instead of inferring it from behavior.

---

## Your Token Optimization Checklist

Here's every change I made, in priority order. You could implement all of these in under an hour.

**Immediate wins (do today):**
1. Audit your `CLAUDE.md` — cut it under 300 lines. Move detailed docs to separate files with contextual references.
2. Disconnect unused MCP servers.
3. Start using `/clear` between task phases.
4. Switch your default model to Sonnet. Use Opus only when you genuinely need it.

**Habit changes (build this week):**
5. Use `/by the way` for side questions instead of main context.
6. Use `/rewind` when responses miss the mark instead of correction prompts.
7. Proactively `/compact` at ~50% context utilization with focus instructions.
8. Write complete task briefs as single messages instead of conversational back-and-forth.

**Configuration (set once and forget):**
9. Disable auto-memory for single-session tasks: `"autoMemoryEnabled": false` in settings.json.
10. Ensure prompt caching is enabled (remove `DISABLE_PROMPT_CACHING` if set).
11. Set effort level to "low" for trivial tasks.
12. Set `MAX_OUTPUT_TOKENS` for tasks with known short responses.

I spent three months learning these lessons through trial, error, and a lot of frustrated staring at "usage limit reached" messages. You now have the condensed version. The hour you spend implementing this list will pay for itself in the first session — and every session after that.

The developers who get the most out of Claude Code in 2026 won't be the ones on the most expensive plan. They'll be the ones who treat every token like it costs money. Because it does.

## Frequently Asked Questions

### How many messages can I send on Claude Pro before hitting the limit?

Claude Pro allows approximately 45 messages per 5-hour rolling window, though this varies by model choice and task complexity. Using Sonnet instead of Opus can effectively triple your message count since Opus consumes roughly 3x the tokens per response.

### What's the difference between /clear and /compact in Claude Code?

`/clear` completely wipes your conversation history, freeing around 150,000 tokens — best used between unrelated tasks. `/compact` compresses your history into a summary, saving around 40,000 tokens while retaining key decisions and context. Use `/compact` mid-task, `/clear` between tasks.

### Does CLAUDE.md affect token usage on every message?

Yes. Claude Code reads your `CLAUDE.md` file on every single turn, not just at session start. A 500-line file can consume 2,000+ tokens per message. Keeping it under 300 lines and moving detailed documentation to separate referenced files significantly reduces per-turn token overhead.

### Should I use Opus or Sonnet for coding tasks?

Default to Sonnet for roughly 90% of coding work — feature implementation, test writing, bug fixes, and standard code generation. Reserve Opus for tasks requiring deep architectural reasoning, complex multi-file refactors, or nuanced problem-solving. For quick lookups and simple edits, Haiku is the most efficient choice.

### How do I check my remaining Claude Code token budget?

Anthropic doesn't currently provide a real-time token counter within Claude Code sessions. You can infer usage by tracking message count and watching for slowdowns that indicate approaching limits. The community has pushed for better visibility, and Anthropic committed to improving usage transparency following the March 2026 feedback cycle.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
