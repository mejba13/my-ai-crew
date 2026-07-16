**BRAND:** mejba.me
**TITLE:** Claude Code 1M Context: How I Stop Context Rot
**META TITLE:** Claude Code 1M Context Window: Stop Context Rot in 2026
**SLUG:** claude-code-1m-context-management
**PRIMARY KEYWORD:** Claude Code 1M context window
**META DESCRIPTION:** I tested Claude Code's 1M context window for 30 days. Context rot starts at 300k tokens — here's the management playbook that actually keeps Opus 4.7 sharp.
**TAGS:** Claude Code, Context Management, Opus 4.7, AI Workflow, Deep Dive

---

# Claude Code 1M Context: How I Stop Context Rot

The session counter said 612,000 tokens. Opus 4.7 had been working for almost four hours. It had read 38 files, refactored a Laravel service layer, written tests, and was now confidently telling me to update a method on a class I had deleted ninety minutes earlier.

Not hallucinated. Deleted. By Claude itself. With a tool call I could scroll back and see in the same session.

I sat there staring at the terminal — coffee gone cold, the cursor blinking under a fix that would have shipped a regression — and felt the same dread every Claude Code power user knows. The model wasn't getting dumber. The model was drowning. Somewhere between token 300k and token 600k, the context had quietly turned from an asset into a liability.

This is the part nobody puts in the marketing copy for the **Claude Code 1M context window**. The window is real. It works. I have receipts. But "1 million tokens" is not the same as "1 million tokens of clear, focused, reliable reasoning." The gap between those two things is where most of my recent debugging has lived.

Over the last 30 days I've burned through enough Opus 4.7 sessions on Max 20x to map exactly where context rot kicks in, what triggers it, and the five specific moves that keep long-running Claude Code sessions sharp instead of soggy. None of it is theoretical. All of it came from breaking real builds first.

If you've ever watched Claude forget a constraint you set in message three, this is for you.

## What "1M Context" Actually Means In Claude Code Right Now

Quick housekeeping, because the version numbers matter and most posts get them wrong.

The 1 million token window in Claude Code did not arrive with Opus 4.5. Opus 4.5 (November 2025) topped out at roughly 250k. The 1M window shipped with **Claude Opus 4.6 on February 5, 2026** — and then went generally available across Claude Code on **March 13, 2026** for both Opus 4.6 and Sonnet 4.6. The current model as I write this is **Opus 4.7**, released April 16, 2026, which inherits the same 1M ceiling.

Access still depends on your plan:

- **Max, Team, Enterprise** — 1M is automatic with Opus 4.6/4.7, no extra charge, no flag to flip
- **Pro ($20/mo)** — you have to opt in by typing `/extra-usage` inside Claude Code, and tokens beyond the standard window get billed against extra-usage credits
- **API** — pricing for context above 200k uses the long-context tier; check the current Anthropic pricing page before you build a workflow that assumes the cheap rate

The context window itself includes **everything the model can see at once**: the system prompt, your CLAUDE.md, conversation history, every file Claude has read with the Read tool, every tool output it has produced, every Glob/Grep result, and the current message you just typed. All of it counts. All of it competes for attention.

That last part is the whole story. Hold on to it.

## The Number Nobody Quotes: Context Rot Starts At 300k, Not 1M

The marketing pitch for any large-context model implies a flat performance curve — that token 999,000 is as useful as token 9,000. It is not. There is a published name for what actually happens, and it deserves to be on every Claude Code user's vocab list.

**Context rot** is the measurable degradation in model performance as input context grows. It is not a bug specific to Anthropic. Chroma's 2025 study tested 18 frontier models — GPT-4.1, Claude Opus 4, Gemini 2.5, all of them — and found context rot at every input length increment they measured. Anthropic's own engineering team has written about it openly, framing context as a "finite attention budget" that depletes with every new token, the same way human working memory does.

Here's the practical number that matters for Claude Code: **degradation starts being felt around 300,000 to 400,000 tokens.** That's roughly 30–40% of the 1M ceiling — far earlier than the headline number suggests. On the MRCR multi-needle retrieval benchmark, Opus 4.6 still scores 76% accuracy at the full 1M, which is excellent for a frontier model. But "76% accurate retrieval" is a nightmare property when you're letting an agent edit production code unsupervised. The other 24% has your name on it.

What rot looks like in practice is rarely a hallucination dramatic enough to spot. It's quieter. It's subtler. It's the four failure modes below — and once you can name them, you can hunt them.

## The Four Failure Modes I Now Name Out Loud

Before I had vocabulary for these, every "Claude is acting weird" moment felt like the same vague problem. Now I diagnose them in seconds. Each one needs a different fix.

### 1. Context Pollution

Pollution is irrelevant information crowding out the signal. You ran a Grep that returned 800 matches. You let Claude read a 4,000-line log file "just in case." You attached the entire `node_modules` types directory because you weren't sure which file held the type. None of that is wrong by itself. All of it together turns Claude's attention into soup.

The model doesn't know what you'd consider relevant. It treats every token as potentially load-bearing. The more noise you stuff in, the more cognition gets spent re-evaluating noise.

**Tell-tale sign:** Claude starts referencing files you forgot you ever pulled in.

### 2. Goal Drift

Goal drift is the slow erosion of original intent. You started the session with "rewrite the auth middleware to support OAuth 2.1, keep all existing JWT flows working, do not touch the user model." Three hours later, after Claude has read 22 files, fixed eight unrelated lint errors, and refactored a logging helper, you ask it to add a new claim to the JWT payload and it modifies the user model.

It didn't ignore your constraint. The constraint just decayed. With 400k tokens between the original instruction and the current turn, the system prompt's signal-to-noise ratio collapsed.

**Tell-tale sign:** Claude does the right thing for the immediate ask but violates a rule from the original brief.

### 3. Memory Corruption

This is the one that scared me into writing this post. Memory corruption happens when the agent's internal model of the world drifts away from reality. The file Claude *thinks* exists at `app/Services/UserService.php` was deleted in turn 47. Claude's still using the version it cached in working memory. You read its plan, it looks coherent, you approve — and the patch lands on a file that no longer exists, or worse, on a stale version of one that does.

I caught the worst case of this on a Multica refactor: Opus had patched a service three times across the session, and on the fourth patch it generated a diff against the *first* version of the file. The intermediate patches existed in the conversation history. They just weren't being weighted.

**Tell-tale sign:** Tool calls reference state that doesn't match the current filesystem.

### 4. Decision Inaccuracy

Decision inaccuracy is the consistency tax. Early in the session you decided "all errors in this service throw `DomainException` and bubble up; the handler logs to Sentry." Later, deep in context, Claude writes a new method that catches everything as `\Exception`, logs to `Log::error`, and returns a 500.

It's not wrong code. It's just not *your* code. Different decisions, contradictory patterns, no consistent design language.

**Tell-tale sign:** Code style and architectural choices stop matching the rest of the codebase even though Claude has read the rest of the codebase.

Each of these has a fix. None of them is "use a smaller model." All of them are about how you manage the window.

## The Five Moves I Use Every Day

When a Claude Code session crosses 300k tokens — or when I see one of the four failure modes above — I have five options. The trick is knowing which one to pick. They are not interchangeable.

### Option 1: Continue (And Why I Almost Never Do)

The default move. Just keep going. The temptation is real because you're "in the flow" and switching costs feel high.

I rarely choose this anymore unless I'm under 300k tokens AND the work is shallow (single-file, single-concern). Past that line, continuing is gambling — every new turn compounds the rot. The cost of a bad patch landing in production dwarfs the cost of pausing and resetting context.

### Option 2: Compact, But Do It Manually

`/compact` summarizes the existing conversation into a shorter recap and keeps going. In theory, it's the magic eraser for context bloat. In practice, autocompact — the version that fires automatically when you hit the window ceiling — is unreliable. Claude's compaction logic prioritizes recent turns and can quietly discard older critical context: the original brief, the architectural decisions, the constraints from message three.

So I never let autocompact run. I run `/compact` manually, proactively, around the 300k mark, and I give it explicit instructions about what to keep:

```
/compact Preserve: (1) the original brief at the top of the session,
(2) all architectural decisions made in turns 1–15,
(3) the list of files we've already modified and why,
(4) any constraint that starts with "DO NOT" or "MUST".
Drop: tool output bodies, intermediate Grep results, file reads we won't need again.
```

That single instruction changes the quality of the compaction dramatically. You go from "summarize the chat" to "produce a structured handoff document." Big difference.

### Option 3: Clear And Restart

`/clear` wipes the context completely. It's the nuclear option. It's also the right option more often than I used to think.

I use `/clear` whenever I'm switching to genuinely unrelated work — finishing the auth refactor and moving to a billing webhook fix. There's zero benefit to dragging the auth context into the billing session, and there's massive downside (pollution, drift, every problem above).

The mistake people make with `/clear` is treating it as failure. It's not. A fresh 12k-token session of focused work outperforms a stale 600k-token session of murky reasoning every single time.

### Option 4: Clear + JSON Save (My Default For Complex Work)

This is the move I lean on hardest. Before clearing, I have Claude write a structured JSON file that captures the state of the work in a form I can re-prime a fresh session with. Something like:

```json
{
  "objective": "Migrate auth middleware to OAuth 2.1 while preserving JWT flows",
  "constraints": [
    "Do not modify app/Models/User.php",
    "All new endpoints must keep /api/v1 prefix",
    "Existing JWT consumers must continue working"
  ],
  "files_modified": [
    {"path": "app/Http/Middleware/Authenticate.php", "status": "complete"},
    {"path": "app/Services/Auth/OAuthHandler.php", "status": "in-progress"}
  ],
  "open_decisions": [
    "Refresh token rotation strategy still TBD",
    "Need to confirm if old JWT format gets a deprecation header"
  ],
  "next_step": "Implement refresh token rotation in OAuthHandler::refresh()"
}
```

Then I `/clear`, start a fresh session, and the first message is: "Read `.claude/state.json`, confirm you understand the current objective and constraints, then continue from `next_step`."

That handoff is leagues more reliable than any `/compact` I've ever run. The new session starts at maybe 15k tokens with perfect signal. No drift. No corrupted memory. No half-remembered constraints.

### Option 5: Sub Agents For Anything That Will Flood Context

Sub agents are Claude Code's escape hatch for tasks that would otherwise dump huge tool outputs into your main context. The agent runs in its own isolated context window, does the work, and returns only the final result to your main session.

The mental test I use — and that Anthropic's own team uses — is: **"Will I need this tool output again, or just the conclusion?"**

Need the conclusion only? Sub agent. Examples:

- "Search the entire codebase for every place we call the legacy payment gateway and report back the file list and line numbers" — perfect sub agent task. The Grep results would otherwise eat 40k tokens of your main context. The final list is 200 tokens.
- "Read these 12 documentation pages and tell me which one has the rate limit info" — sub agent. The 12 pages would pollute. The answer is one sentence.
- "Run the test suite and report only the failures with stack traces" — sub agent. You don't need the 8,000 lines of pass output.

I configure these as proper Claude Code subagents in `.claude/agents/` so the orchestration is repeatable. The first time I retro-fitted a 90-minute session to use sub agents for the search-heavy steps, the main context dropped from 400k to 90k and Opus stayed sharp through the whole job.

## The Three Practices That Quietly Matter More Than Any Command

Beyond the five options, three habits make the biggest day-to-day difference. None of them are flashy.

### Rewind Instead Of Reprompting

When Claude makes a wrong decision and you correct it, that wrong decision still sits in the context forever. Three turns later you correct another related mistake. By turn ten, the conversation is half real work and half "no, not that, do this instead." The rot accelerates.

The cleaner move is to **rewind** — back the conversation up to before the bad turn, and re-prompt with a better initial instruction that incorporates what you learned. Claude Code lets you edit prior messages. Use it. The rewound session is dramatically cleaner, compacts better, and propagates fewer downstream errors.

I now treat the third "no, that's still not right" as an automatic trigger to rewind, not to keep correcting.

### Periodic Recaps Aren't Optional

Every 50–75k tokens of substantive work, I ask: "Recap the current objective, the constraints we agreed to, and the work completed so far." Two paragraphs, max.

This sounds wasteful. It is the opposite. The recap forces Claude to re-anchor on the brief, which dramatically reduces goal drift in the turns that follow. It also gives me a fast way to spot when *Claude's* understanding has already drifted — if the recap is wrong, I know the rot is already in motion and it's time for `/compact` or `/clear`.

Think of recaps as the cheap version of compaction. Compaction restructures memory. Recaps reinforce it.

### Explicit Compaction Instructions Beat Default Behavior, Always

I covered this in Option 2 but it's worth pulling out as a standalone rule. If you let `/compact` run with no instructions, you're trusting Claude's defaults to know what's load-bearing in your work. It often doesn't. The constraint you wrote in message three is just text to the summarizer.

Always tell `/compact` what to preserve, what to drop, and how to structure the output. Treat it like writing a handoff doc for a teammate, not invoking a magic command.

## What This Looks Like End-To-End: A Real Session

Here's a stripped-down version of how a long Claude Code session actually flows for me now, after a month of tightening this loop:

1. **Session start** — `CLAUDE.md` is tight, the brief is in the first message, constraints are explicit. Token count: ~5k.
2. **First 200k tokens** — straight work. I'm not optimizing anything. Reading files, writing code, running tests. Context is healthy.
3. **300k mark** — first checkpoint. I ask for a recap. Confirms direction. No `/compact` yet if the work is still focused.
4. **400k mark** — second checkpoint. If I'm still mid-task, I run a manual `/compact` with explicit preserve/drop instructions. Token count drops back to ~80k. Continue.
5. **Anything that would dump >20k of tool output** — sub agent. Always.
6. **End of a discrete chunk of work** — write a JSON state file, `/clear`, fresh session, re-prime from JSON. The transition costs me 3 minutes and saves me from compounding rot.
7. **First "no, that's still not right" loop** — pause. Diagnose which of the four failure modes is happening. Pick the right fix. Don't keep reprompting blind.

That's it. No magic. No new tools. Just disciplined use of what Claude Code already ships with.

The result: I now run productive Claude Code sessions that span an entire workday on the same project without the model going sideways. A month ago I couldn't get past a Tuesday afternoon without a hallucination cascade.

## What I Got Wrong For The First Three Weeks

I owe you the honest version of how I figured this out, because I wasted a lot of money getting there.

I assumed bigger context was strictly better. I would deliberately stuff more files into context "so Claude has full visibility." Wrong. Every irrelevant file was a small tax on every subsequent turn. Less is more. Read what you need, not what might be useful.

I trusted autocompact. For three weeks I let it fire whenever it wanted. The drift was so gradual I blamed myself, my prompts, my project structure — anything but the actual culprit. The first time I disabled autocompact and ran manual compactions with explicit instructions, the difference was night and day.

I avoided sub agents because the orchestration felt like overhead. I was wrong about that too. Setting up a search subagent or a test-runner subagent takes ten minutes once. It saves you from 50k-token tool dumps every session afterward.

And I treated `/clear` as defeat. It isn't. A clean session is not a failed session. A clean session is a tool. Use it.

## Frequently Asked Questions

### When does context rot start in Claude Code?
Context rot in Claude Code becomes measurable around 300,000 to 400,000 tokens — roughly 30–40% of the 1M window. Performance does not collapse at that point, but goal drift, memory corruption, and decision inconsistency become noticeably more likely. Treat 300k as your first checkpoint, not 1M as your ceiling.

### Should I use /compact or /clear in Claude Code?
Use `/compact` when the current task is still active and the recent context is genuinely needed; use `/clear` when switching to unrelated work or when context is so polluted that summarizing it would still leave noise. For complex in-progress work, the strongest pattern is to write a JSON state file, `/clear`, and re-prime a fresh session from the JSON.

### Does the 1M context window cost extra in Claude Code?
On Max, Team, and Enterprise plans, the 1M context window is included with no extra charge for Opus 4.6 and Opus 4.7. On the Pro plan ($20/month), you have to opt in with `/extra-usage` and tokens above the standard window are billed against extra-usage credits. API pricing uses the long-context tier above 200k — verify current rates before building a workflow on that assumption.

### What is the difference between /compact and sub agents?
`/compact` summarizes the existing conversation in your current session to free up tokens; sub agents prevent the bloat from happening at all by running side tasks in their own isolated context windows and returning only the final result. Use sub agents for any task whose tool output you don't need to keep referencing — search, log analysis, test runs.

### Why does Claude Code start hallucinating in long sessions?
Long Claude Code sessions hallucinate because of context rot — as the input grows, the model's attention budget thins out and older constraints, file contents, and decisions get under-weighted. The fix is not a bigger window; it is active management: manual `/compact` with explicit instructions, periodic recaps, sub agents for high-output tasks, and `/clear`+JSON handoffs between major work chunks.

So: your terminal is open. The session is at 280k tokens. Opus 4.7 just suggested editing a file you're 80% sure you deleted an hour ago.

What do you do in the next 60 seconds?

If your answer is "ask Claude to double-check," you're still managing context the old way. If it's "rewind to the last clean turn, write a JSON state file, `/clear`, and re-prime" — welcome to the version of Claude Code that actually scales to a full workday. The 1M window is real. The reliability inside it is something you have to engineer.

I'd rather engineer it than blame the model.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
