**BRAND:** mejba.me
**TITLE:** Claude Token Limits: It's Context Rot, Not a Cap
**META TITLE:** Claude Token Limits: Fix Context Rot, Stop the Bill
**SLUG:** claude-token-limits-context-hygiene
**PRIMARY KEYWORD:** Claude token limits
**META DESCRIPTION:** Claude token limits aren't a cap — they're context rot. Here's the math, the research, and the exact tactics I use to cut session costs and keep accuracy high.
**TAGS:** Claude Code, Token Optimization, AI Productivity, Developer Tools, Tutorial

---

# Claude Token Limits: It's Context Rot, Not a Cap

The bill that finally made me sit down and do the math was $73.41 — for a single Tuesday afternoon of Claude Code. I hadn't done anything unusual. One feature branch. A handful of file edits. Some debugging. The kind of session that should have cost me eight or nine bucks.

I opened the cost breakdown expecting to find a runaway agent or a forgotten loop. What I found was worse. Every single message I'd sent that afternoon had reread the entire conversation history. Not summarized it. Not skipped to the relevant parts. *Reread it*. By message thirty, the model was processing more tokens to understand what we'd already discussed than to generate the next answer.

That's when I realized something that I think most people using Claude — including a lot of seasoned developers — have completely backwards. The "token limit" people complain about isn't a cap Anthropic imposes to throttle you. It's not a billing trick. It's not even really a limit in the way most of us think about limits.

It's [context rot](https://research.trychroma.com/context-rot). And once you understand what's actually happening inside a Claude session, the strategies for fixing it stop being random tips you read on Twitter and start being a coherent system.

I've been running that system for about eight weeks now across four different projects. My average daily cost dropped roughly 60%. The output quality went *up*, not down. And the most counterintuitive part — the thing that took me the longest to accept — is that almost none of the wins came from "using Claude less." They came from using Claude *cleaner*.

This is the full breakdown. The math. The research. The exact slash commands I use, when I use them, and the ones I had to unlearn. By the end, you'll be able to look at any Claude session and tell, within thirty seconds, whether it's healthy or about to start hemorrhaging tokens.

---

## The Math Nobody Shows You

Here's the thing every Claude tutorial dances around but never quite says outright: every time you send a new message, the model reprocesses the entire conversation up to that point. System prompt. Every file you've referenced. Every tool call. Every assistant response. All of it, every turn.

That sounds expensive. It is.

Let me show you what it actually looks like with realistic numbers. Assume each message exchange (your prompt + Claude's reply + any tool output) adds roughly 500 tokens to the conversation. Modest. Reasonable. Probably *under*-counting if you're working with code.

| Message # | New tokens added | Total context reread | Cumulative tokens processed |
|---|---|---|---|
| 1 | 500 | 500 | 500 |
| 5 | 500 | 2,500 | 7,500 |
| 10 | 500 | 5,000 | 27,500 |
| 20 | 500 | 10,000 | 105,000 |
| 30 | 500 | 15,000 | 232,500 |
| 50 | 500 | 25,000 | 637,500 |
| 100 | 500 | 50,000 | 2,525,000 |

Look at message 10 versus message 1. Same-sized prompt. Ten times the cost. By message 30, you've already burned through more cumulative tokens than the first fifteen combined. By message 100, the model has reread the conversation so many times that 98.5% of the tokens it's processed for that session were spent re-understanding old context — not generating new output.

This isn't a Claude problem. It's a [transformer architecture property](https://platform.claude.com/docs/en/build-with-claude/context-windows). Every frontier LLM works this way. But Claude makes the cost visible in a way most people don't notice until the bill arrives.

And here's where it gets worse — because cost isn't the only thing that compounds.

---

## What Context Rot Actually Looks Like

In 2025, [Chroma published research](https://research.trychroma.com/context-rot) that quietly broke a lot of assumptions about how long-context models behave. They tested 18 frontier LLMs — Claude included — on retrieval tasks at varying input lengths. The conventional wisdom said: bigger context window = better performance, full stop.

The data said something else.

Every single model performed worse as input length grew. Some held steady at 95% accuracy and then nosedived to around 60% once the input crossed a certain threshold. The drop wasn't gradual. It was a cliff. And it didn't happen at the marketed context window limit — it happened well before, often around 200k–300k tokens, even on models advertising 1M context.

The mechanism is something researchers call the [lost-in-the-middle effect](https://arxiv.org/html/2510.05381v1). Transformer attention is U-shaped. The model attends well to the start of the context (the system prompt, your initial setup) and to the end (your most recent message). The middle? Increasingly fuzzy. A 2023 Stanford study found that with just twenty retrieved documents — about 4,000 tokens — accuracy on QA tasks dropped from 70-75% down to 55-60%. And that's before you add multi-turn conversation history on top.

Pair that with the cost curve we just looked at, and you get the real picture of what happens in a long Claude session:

- Tokens 1 through ~50k: model is sharp, accurate, and relatively cheap per turn
- Tokens 50k through ~200k: cost climbs fast, accuracy starts drifting, hallucinations creep in
- Tokens 200k+: you're paying premium rates for degraded output

This is why "just keep going in the same chat" is the single most expensive habit you can have with Claude. You're not just paying more — you're paying more for *worse*.

The fix isn't to cap how much you use Claude. It's to recognize that your conversation has a quality half-life, and to manage it deliberately. Context hygiene, not throttling.

Let me show you exactly how I do that.

---

## Nine Tips That Cut My General Claude Costs in Half

These are the habits I use across the regular Claude apps — claude.ai, mobile, the API. Not Claude Code yet. We'll get there.

### 1. Edit and regenerate instead of writing follow-up corrections

This one feels obvious in hindsight and I missed it for months. When Claude gets something wrong, my instinct used to be to write a follow-up message: "actually, I meant X, not Y." That follow-up creates two new turns in the conversation that will be reread on every future message.

The cleaner move is to edit the original prompt and regenerate. Same outcome. Zero added context weight. If you do this five times across a long session, you've saved yourself maybe 3,000–5,000 tokens of permanent overhead — every single message after that point.

### 2. Batch multiple requests in one prompt

Three messages, each asking one thing, costs roughly three times the context overhead of one message asking three things. The model is genuinely good at handling multi-part prompts. Use that.

Instead of:
- "Write the API endpoint."
- "Now write the test."
- "Now write the README section."

Send: "Write the API endpoint, the corresponding test, and a README section explaining the endpoint. Use H2 headings for each."

You'll get the same quality. One turn of context cost. This is the single biggest behavior change I made and it probably accounts for a third of my savings.

### 3. Start fresh chats every 15-20 messages

I treat 15-20 messages as the soft ceiling for a single conversation. Past that, both cost and accuracy start declining noticeably. When I hit it, I do a deliberate handoff:

> "Summarize everything we've established in this conversation — the goal, decisions made, files touched, current blockers, and what's next. Format it as a brief I can paste into a new session."

Then I open a fresh chat and paste that summary as the first message. New session, same context, fraction of the token weight.

### 4. Pick the right model for the task

[Claude's current lineup](https://platform.claude.com/docs/en/about-claude/models/overview) — Haiku 4.5, Sonnet 4.6, and Opus 4.7 — isn't a "good, better, best" hierarchy. It's a speed-versus-depth spectrum, and using Opus for what Haiku can handle is a quiet money pit.

A rough rubric:

- **Haiku 4.5** ($1/$5 per million tokens): classification, routing, summarization, simple lookups, glue tasks. Anything where you'd say "this is annoying but not hard."
- **Sonnet 4.6** ($3/$15): your default. Code, writing, analysis, multi-step reasoning. According to [BenchLM's 2026 numbers](https://benchlm.ai/blog/posts/claude-api-pricing), Sonnet 4.6 sits within 1.2 points of Opus on SWE-bench at 60% of the cost.
- **Opus 4.7** ($5/$25): genuinely hard reasoning, novel problems, ambiguous specs, the stuff where you need the model to *think*, not just produce.

Most people I watch over Discord screen-shares are running Opus by default for tasks that Sonnet would crush. That's a 67% premium for output that, in many cases, is functionally identical.

### 5. Keep extended thinking off by default

Extended thinking generates internal reasoning tokens before the model produces its visible output. Useful for hard problems. Expensive for everything else, because [thinking tokens are billed at output rates](https://www.finout.io/blog/anthropic-api-pricing) — and output is 5x the cost of input on Opus and Sonnet.

A response with 500 visible tokens and 2,000 thinking tokens costs roughly 5x what the same response would cost without thinking. That's the actual multiplier. For most tasks — drafting, summarizing, refactoring — extended thinking is paying premium rates for marginal accuracy gains.

I leave it off and turn it on deliberately when the task warrants it. Architecture decisions. Debugging weird bugs. Anything where a wrong answer costs me real time downstream.

### 6. Convert files to Markdown before uploading

A 30-page PDF can run 40,000+ tokens. The same content as clean Markdown often fits in 8,000–12,000. PDFs carry an enormous amount of formatting and metadata weight that adds nothing to what the model can extract. Same for HTML — half the tokens are tag soup.

If I'm going to reference a document repeatedly across a session, I convert it once with a tool like `pdftotext` or `markitdown` and upload the Markdown version. The accuracy actually goes up because the model isn't fighting layout noise.

### 7. Use Projects to cache repeated documents

If you're hitting the same documentation, codebase context, or reference material across many sessions, [Projects](https://www.anthropic.com/news/projects) lets that material live in the project knowledge base instead of being repasted into every chat. The cached portion gets re-injected efficiently. Same idea as [prompt caching on the API](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) — cache reads cost roughly 10% of standard input pricing.

I have a "Mejba's Codebase Context" project that holds my coding standards, architectural patterns, and a few dozen reference files. Every code-related chat happens inside that project. The model walks in already knowing how I work.

### 8. The 5-hour session reset trick

Claude.ai's usage windows reset on a rolling 5-hour basis, and the timer starts on your first message of a session. If you wake up at 8am and your first message is a real working session, the next reset window starts then. If you fire a tiny throwaway message at 7am — "good morning" or whatever — the reset shifts, and you can fit two full work sessions into your day instead of one.

Petty? Maybe. But on weeks where I'm grinding hard, it's earned me an extra working window more than once.

### 9. Work off-peak when you can

Anthropic has, at times, throttled responses or slowed inference during peak hours. I don't have hard data on this — Anthropic doesn't publish it — but the anecdotal pattern across my own sessions is clear. Early morning and late evening sessions feel snappier. Midday US-business-hours sessions sometimes feel sluggish.

If your work allows it, schedule heavy Claude work outside the 10am–4pm Pacific window. Worst case, you get the same speed. Best case, you finish 20% faster.

Those nine tips alone will move the needle. But if you're using Claude Code, there's a separate set of moves that matter more — because Claude Code has a different cost profile, and a different set of tools for managing it.

---

## Eight Claude Code Tips That Matter More Than the General Ones

[Claude Code](https://code.claude.com/docs/en/overview) is where token costs get genuinely scary if you don't manage them, because the context surface area is bigger. CLAUDE.md gets injected on every turn. MCP tool schemas get injected on every turn. File reads accumulate. Sub-agent calls return their full output into your main context. The compounding is real.

These are the eight habits that turned my Claude Code bills from "I should probably look at this" into "boring background expense."

### 1. Run `/context` early — before you start the work

The single most useful command Claude Code shipped, and probably the one most people don't use. [`/context`](https://code.claude.com/docs/en/context-window) shows you a colored grid of your current context usage — what's loaded, how much each piece is consuming, where the budget is going.

Run it as the *first* thing you do in a fresh session. Not after you've worked for an hour. The first thing.

What you'll often find is something like this:

```
System prompt: 4,200 tokens
CLAUDE.md: 18,400 tokens
MCP tool schemas: 47,300 tokens
Loaded files: 0 tokens
---
Total: 69,900 tokens (35% of 200k context window)
```

That's 35% of your budget gone before you've typed a single instruction. If your CLAUDE.md is bloated and you've got four MCP servers loaded, you can be at 22%–40% before the session even starts. According to [Scott Spence's MCP context analysis](https://scottspence.com/posts/optimising-mcp-server-context-usage-in-claude-code), one developer measured their MCP tools alone consuming 66,000+ tokens of context overhead.

Knowing this number changes how you work. You stop being surprised by token bills.

### 2. Disconnect MCP servers you're not actively using

Each connected MCP server injects its full tool schema — every tool name, every description, every parameter definition — into the context on every single message. Not once at startup. Every. Turn.

If you have a MCP server with 20 tools and you're not using it for the current task, you're paying for its schema on every message regardless. Disconnect it. You can reconnect with one command when you actually need it. The savings can be enormous — easily 15,000–40,000 tokens per session for someone with multiple servers loaded.

The Anthropic team has been working on lazy-loading tool schemas (only loading the schema for a tool when it's actually called), and as of [advanced tool use](https://www.anthropic.com/engineering/advanced-tool-use), some progress has been made. But the safe assumption, until you've verified otherwise on your own setup with `/context`, is that connected MCP = consumed tokens.

### 3. Replace MCP servers with CLIs where you can

This one took me a while to internalize. MCP servers are convenient but verbose. A CLI tool that does the same thing — invoked via Claude Code's bash tool — typically uses far less context, because you're just sending the command and parsing the output, not loading a full schema definition.

I've replaced three MCP servers with equivalent CLI workflows. The token savings averaged around 35%–40% per session. The tradeoff: slightly more friction when invoking the tool, because Claude has to construct the command rather than calling a typed function. For me, that tradeoff is worth it nine times out of ten.

If you've already covered the basics, my [deeper guide to Claude Code token management](https://www.mejba.me/claude-code-token-management-hacks) walks through the specific MCP-to-CLI swaps that earned me the most.

### 4. Use `/clear` between unrelated tasks

[`/clear`](https://code.claude.com/docs/en/commands) wipes the conversation history and starts fresh. Most people use it as a "start over" button when something goes wrong. That's not the highest-value use.

The highest-value use is between *unrelated tasks*. You finish refactoring the auth module. The next thing on your list is updating the README. There's zero overlap. The auth conversation contributes nothing to the README task — but it would be reread on every README turn if you don't clear.

Hit `/clear`. Start the README task fresh. You just saved yourself thousands of tokens of irrelevant context, plus you reset the lost-in-the-middle accuracy degradation that was about to bite you.

### 5. `/compact` proactively at ~50% context usage

[`/compact`](https://code.claude.com/docs/en/commands) summarizes your conversation history and replaces the full transcript with a condensed version. The official guidance says to use it when context exceeds 80%. I use it earlier — typically around 50%.

Why earlier? Because by the time you hit 80%, you're already in the context-rot danger zone. Accuracy is already drifting. Compacting at 80% is damage control. Compacting at 50% is maintenance.

You can pass instructions to `/compact` to steer what it preserves: `/compact focus on the auth module decisions and current test failures`. Use that. The default summary is fine for most cases, but for complex sessions, telling it what to keep makes a real difference.

If `/compact` makes a mistake — drops something important — `/resume` lets you rewind to a previous session state. Don't be afraid of `/compact` because of mistakes; the rewind path is real.

### 6. Session handoff at ~60% — full summary, fresh start

For really long working sessions, `/compact` isn't always enough. There's a point where the conversation has accumulated so many decisions, file references, and context shifts that even a summary can't fully untangle it.

When I hit ~60% usage on something complex, I do a manual handoff:

> "Generate a complete handoff document for this session. Include: the goal, all architectural decisions made, all files modified, current state of the work, blockers, and the next 3-5 actions. Format it as a Markdown brief I can paste into a new session."

Then I save that brief, run `/clear`, and paste it as the opening message of a new session. The new session walks in with full context at maybe 8,000 tokens of overhead instead of 120,000.

### 7. Use sub-agents for heavy tasks

Sub-agents run in separate context windows. If I dispatch a sub-agent to "research the Stripe webhook integration patterns and report back with three options," all that research — every doc page, every example, every dead-end exploration — happens in the sub-agent's context, not mine. I get the summary. The research weight stays out of my main session.

This is one of the biggest unlocks Claude Code offers. For any task that involves heavy reading, exploration, or research before producing a deliverable, sub-agents are nearly always the right answer.

### 8. Clean setup: CLAUDE.md under 200 lines, settings.json tuned

The single biggest constant overhead in any Claude Code session is [CLAUDE.md](https://thepromptshelf.dev/blog/claude-md-token-budget-optimization/). It's injected on every turn. If yours is 600 lines, you're paying for those 600 lines on every message of every session for the rest of the project.

The Prompt Shelf's analysis recommends keeping CLAUDE.md under 200 lines. I'd argue under 150 if you can swing it. Five rules. Three file pointers. The shape of the project, not the documentation of it.

Pair that with a well-tuned `settings.json`:
- `autocompact_threshold` set to your preferred trigger (I use 0.65)
- `deny` rules for `node_modules`, `.next/cache`, `dist`, `build`, and any other directories you never want Claude reading into context

This setup alone — lean CLAUDE.md plus aggressive deny rules — moved my baseline session overhead from around 35,000 tokens down to around 9,000.

That's the daily work of cost management. But there's a layer above all of these tactics that, once you adopt it, makes most of them feel automatic.

---

## Four Collaboration Habits That Compound Over Time

The tips above are tactical. These four are structural. They change the *shape* of how you work with Claude, not just the parameters.

### 1. Point Claude at a clean, dedicated folder

This sounds trivial. It's not. If you point Claude Code at the root of a 40,000-file monorepo, even with deny rules, you're inviting noise. Claude will, occasionally, read into directories you didn't intend. Tool calls will return larger payloads than you expected. Searches will surface irrelevant matches.

The clean version: create a working folder for the specific task. Symlink in only what's needed. Point Claude at that folder. Now every read, every search, every glob is operating on a focused surface.

### 2. Local memory files: `instructions.md` + `memory.md`

CLAUDE.md is global to the project. But for long-running work, I've started maintaining two additional files in the working directory:

- **`instructions.md`**: rules, tone, formatting preferences, "always do X, never do Y." Updated rarely. This is how I like to work.
- **`memory.md`**: project-specific facts, decisions made, current state, what's next. Updated at the end of every session.

At the start of every new session, my opening prompt is roughly: "Read instructions.md and memory.md, then await my next message." Total cost: ~2,000–4,000 tokens. What I get back: a Claude that walks in already knowing the project state, the conventions, and what we were last working on. No re-explaining. No re-deciding. The memory survives session boundaries.

This pattern compounds. Three weeks into a project, your `memory.md` is doing the work that would otherwise take a 50-message conversation to recreate.

### 3. Offload research to other tools

Not everything Claude can do is something Claude *should* do. Heavy web research, scraping, multi-source comparison — these tasks consume enormous amounts of context for outputs that other tools produce more cheaply.

I now route most research-style work through Perplexity or Gemini, then feed the distilled output back to Claude for the actual building work. A 40,000-token research session becomes a 3,000-token brief. Claude focuses on what it's best at — code, structured output, technical reasoning — instead of chewing through tokens on tasks where it's not the optimal tool.

This is one of those moves that feels heretical until you try it. Then it just feels obvious.

### 4. Encode repeatable tasks as Skills

Anything I do more than three times across different sessions — code review, content audits, deploy checklists, security reviews — gets encoded as a [Claude Skill](https://www.mejba.me/agent-skills-advanced-claude-code) with the process pre-loaded. The skill carries its own minimal context: the steps, the standards, the output format.

Instead of explaining the process every time, I trigger the skill. The skill knows what to do. My main context stays light. I get consistent output. And the skill keeps improving — every session that surfaces a better approach gets folded back into the skill definition.

This is where the system gets compounding returns. The first time you build a skill, it's a 30-minute investment for marginal savings. The hundredth time you use it, it's saving you ten minutes and 20,000 tokens per invocation, on top of producing better, more consistent output than ad-hoc instructions ever did.

---

## Real Talk: What I Got Wrong About All of This

I want to be honest about something. For the first six months I was using Claude seriously, I treated all of this as accounting. Cost optimization. Penny-pinching. The boring side of using AI tools.

That framing was wrong, and it cost me real money and real output quality before I figured it out.

Context hygiene isn't accounting. It's *quality control*. The same tactics that cut my costs — clean sessions, lean CLAUDE.md, aggressive `/clear` and `/compact`, sub-agents for heavy work — also made the model dramatically more accurate. Because the same conditions that drive cost up (long context, accumulated cruft, drifting topics) drive accuracy down.

I had it backwards in my head. I thought there was a tradeoff: spend more, get better output. The actual relationship, in my experience, is the opposite. Sessions that cost a lot were almost always sessions where the output was getting worse, and I was just shoveling more tokens at the problem trying to compensate.

Now I treat a climbing token bill the way a doctor treats a fever — as a *symptom*. Something is wrong with the session. Compact it. Clear it. Hand it off. The bill comes down and the output goes up at the same time.

There are limits to this, and I want to name them. Context hygiene won't save you if your problem is genuinely hard and requires Opus-level reasoning across a large codebase. Some sessions are expensive because the work is expensive. That's fine. The goal isn't to minimize spend — it's to make sure that when you do spend, you're spending on signal, not on rereading old conversation noise.

---

## What This Looks Like Eight Weeks In

Here's what changed for me, concretely, after running this system across four projects for two months.

Average daily Claude Code spend dropped roughly 60%. Some days more, some less. The variance came down too — fewer surprise $70 days.

Session length on a single conversation dropped from "until something breaks" to "until `/context` shows ~50%." That sounds like a downgrade. It isn't. The new sessions are sharper end-to-end. The old long sessions had a quality cliff in the second half I was just absorbing.

CLAUDE.md across my projects shrank from an average of around 400 lines to under 150. Nothing important got lost. A lot of things I thought were important turned out to be cruft I'd been paying to inject on every turn.

The number of MCP servers I keep connected by default went from six to two. The other four get connected on demand for specific tasks and disconnected when the task ends.

And the part I genuinely didn't expect: the work I produce in Claude Code is noticeably better. Cleaner code. Fewer hallucinations. More focused architectural decisions. Not because the model got better — Sonnet 4.6 and Opus 4.7 are the same models I was using before. Because the *sessions* got better. Less context rot. Less middle-of-the-window drift. More signal, less noise.

That's the part of this I want to leave you with. Token limits aren't a budget you have to live under. They're a quality signal you should listen to. When the bill climbs, the model is telling you something — that the session has gone past its useful life, that the context has accumulated more than it can handle cleanly, that it's time for a hard reset.

Listen to it. Compact. Clear. Hand off. Start fresh.

The cost goes down. The output goes up. And eventually, after enough cycles, you stop thinking of this as "managing tokens" at all. You think of it as just — working with Claude well.

---

## Frequently Asked Questions

### Why does Claude reread the entire conversation every message?

Transformer-based LLMs process the full context window on every inference call — there's no internal memory of previous turns the way humans recall conversations. Each new message reprocesses the system prompt, all prior turns, and any loaded files as a single input. This is an architectural property, not a Claude-specific design choice. For the full token math, see "The Math Nobody Shows You" above.

### What's the difference between `/clear` and `/compact` in Claude Code?

`/clear` wipes the conversation history entirely and starts fresh — use it between unrelated tasks. `/compact` summarizes the existing history into a condensed version while preserving key facts — use it proactively at around 50% context usage to extend a productive session. Both are documented in the [Claude Code commands reference](https://code.claude.com/docs/en/commands).

### Is the 1M token context window worth using on Claude Code?

Sometimes, but rarely as a default. Research on context rot shows accuracy degrading well before the 1M limit — often around 200k–300k tokens, even on models advertising 1M context. Use the larger window for genuinely large inputs (full codebases, long documents) but expect quality to drop in the middle of the context. For most working sessions, a clean 50k context outperforms a sprawling 800k one.

### Should I always use Opus 4.7 for coding?

No. Sonnet 4.6 sits within ~1.2 SWE-bench points of Opus at 60% of the cost, according to [BenchLM's 2026 benchmarks](https://benchlm.ai/blog/posts/claude-api-pricing). Use Sonnet as your default and reserve Opus for genuinely hard reasoning — novel problems, ambiguous specs, complex debugging. Most coding tasks don't need Opus-level depth.

### How do I check how many tokens my Claude Code session is using?

Run `/context` in Claude Code. It displays a colored grid of current context usage, broken down by system prompt, CLAUDE.md, MCP tool schemas, and loaded files. Run it as the first thing you do in a session — not after an hour of work. Knowing your starting overhead changes how you work.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
