**BRAND:** mejba.me
**TITLE:** Handoff Skill: The Claude Code Fix for Context Bloat
**META TITLE:** Handoff Skill for Claude Code: Multi-Session Workflow
**SLUG:** handoff-skill-claude-code-multi-session
**PRIMARY KEYWORD:** handoff skill Claude Code
**META DESCRIPTION:** How the handoff skill split my Claude Code work into clean multi-session flows, kept context sharp past 120K tokens, and replaced lossy /compact entirely.
**TAGS:** Claude Code, AI Agents, Skills, Context Management, Workflow

---

The session was at 142,000 tokens when I noticed Claude had started repeating itself.

I'd been deep in a planning conversation about a new Aria content pipeline — three brands, four post types, a shared research protocol, the works. Halfway through, I asked it to spec out a small refactor of an unrelated cron skill that had nothing to do with the pipeline. Forty-five minutes later, Claude was politely contradicting decisions we'd locked in two hundred messages earlier, mixing the cron logic into the content spec, and quoting my own ADRs back to me slightly wrong. The 1M context window was technically still open. Practically, the model was working in fog.

That session is why I picked up the handoff skill. And once I started using `handoff` instead of `/compact` for these moments, the difference wasn't subtle — it was the difference between a focused engineer who finishes a task cleanly and a tired one who keeps reopening the same Slack thread.

This is the post I wish someone had handed me six weeks ago. We're going to take the handoff skill apart: how it works, why it beats compaction for multi-thread work, what the markdown file actually contains, when to reach for it, and how I've folded it into my own Aria + Claude Code workflow on this site. By the end you'll know exactly where in your current sessions you should be writing a handoff and where you shouldn't.

## The context window is bigger, and that made the problem worse

Let me set the scene properly, because the framing matters.

Claude Code now ships with a 1M token context window. That sounds like a solved problem — pour everything in, the model will figure it out. It is not a solved problem. Anthropic's own documentation confirms it: accuracy and recall degrade as context fills. Independent testing from teams running Claude Code in production puts the practical degradation point much earlier than the ceiling — quality starts slipping around the **120,000-token mark**, well before the window is technically full. Some teams report measurable quality loss as early as 50% of capacity.

I think of every context window as two layers stacked on top of each other:

- **The smart zone** — the early tokens, the system prompt, the freshest exchanges. Attention is sharp here. The model knows what you asked, what it answered, and what's on the table right now.
- **The dumb zone** — the later tokens, the stale middle, the parts the attention mechanism has to fight to weight properly. It's still "in context." It's just not getting the focus you think it is.

Once a session crosses into the dumb zone, you don't always notice. The replies still sound confident. They might still cite earlier exchanges. But the precision drops. Decisions you made get forgotten or quietly reversed. Tool selections get mushy. Code starts looking like a composite of three different design choices you had nearly converged on.

The honest version of "1M context" is more like: 1M ceiling, ~120K of dependable smart zone, then a long degradation curve. Budgeting attention inside the window is as important as the ceiling itself — and I'd argue more important. I wrote about this trade-off in detail in [my breakdown of Claude Code's 1M context management](/blog/claude-code-1m-context-management) and in [the context hygiene piece on token limits](/blog/claude-token-limits-context-hygiene). Both still apply.

What handoff does, in one line: it gives you a clean way to split work across multiple sessions so each one stays in its own smart zone instead of pretending the dumb zone doesn't exist.

## What the handoff skill actually does

Here's the workflow shift that clicked for me.

When the handoff skill is invoked, the current Claude Code session compresses everything relevant — what we were trying to do, what we decided, what we tried, what's still open, which files we touched, which skills the next session should grab — into a single Markdown file. That file gets saved to the OS temp directory (so it doesn't clutter the workspace), and then a fresh Claude Code session opens that file and continues the work without inheriting the bloat.

A few details that took me a couple of tries to appreciate:

**The handoff file is purpose-tailored, not generic.** The skill accepts an argument describing the next session's focus. "Continue the API design" produces a very different handoff than "build a UI prototype for the design we just sketched" — even when both come from the same parent session. The compression is intentional, not a dumb summary.

**It's a real Markdown file, not a hidden JSON blob.** I can open it, read it, edit it, add a paragraph, remove a section, redact a token before passing it on. That's a property I underestimated until I tried to do the same thing with `/compact`'s summary, which is opaque and lossy in ways you can't audit.

**It points instead of duplicates.** If we'd written a GitHub issue or an ADR for the work, the handoff file references it instead of pasting the contents. Sounds obvious — except `/compact` does the opposite. It re-summarizes everything, so the next session ends up with a fuzzy paraphrase of the issue you'd already written precisely.

**It includes a "suggested skills" section.** This is the part I want every framework to copy. The current session knows what tools, skills, or sub-agent patterns the next session will probably need — TDD, brainstorming, worktrees, verification — and it writes that hint into the handoff. The fresh session arrives already pointed at the right toolbox.

**Sensitive data gets redacted before saving.** API keys, secrets, personal info — the skill strips them before the file lands on disk. I still scan handoffs manually before I pass them around, but having that as a built-in default beats hoping I remembered.

If you've been using Obra's superpowers framework (around 195k stars on GitHub at the time I'm writing this, growing fast), this is going to feel native to you — handoff is exactly the kind of disciplined, methodology-driven skill that makes that whole ecosystem work. I covered the broader pattern in [my superpowers plugin review](/blog/superpowers-plugin-claude-code-review). The handoff skill is the piece I underused for the first few weeks until the multi-session math caught up with me.

## Compaction vs handoff: the comparison that changed how I work

`/compact` and `handoff` look similar from a distance. Both produce a compressed view of where you've been. They solve very different problems.

Here's the head-to-head as I use them now:

| Dimension | `/compact` (compaction) | `handoff` skill |
|---|---|---|
| **Session topology** | Single long-running session | Multiple purpose-specific sessions |
| **What it compresses** | The full history of the current session | Only what the *next* session needs to know |
| **Where the output goes** | Back into the same session's context | A Markdown file in the OS temp directory |
| **Audit-ability** | Opaque summary, can't edit | Human-readable file, edit before passing |
| **Cross-session continuity** | Same conversation, just shorter | Fresh attention, scoped focus, smart zone resets |
| **Cross-tool portability** | None — locked to that session | Markdown works across Claude Code, Codex CLI, Copilot CLI |
| **Sensitive data handling** | None by default | Redaction step before save |
| **Pointer vs duplicate** | Re-summarizes everything | References existing artifacts (issues, ADRs, plans) |
| **Best for** | Trimming one session that's run long on a single coherent task | Splitting unrelated work, prototyping side-quests, cross-agent flows |
| **Failure mode when misused** | Lossy compression of work you'll still need | Two sessions drifting if the handoff doc isn't tight |

Read that table sideways for a second. Compaction is a memory tool — it tries to make a single thread fit. Handoff is a workflow tool — it splits threads so each one fits naturally. The first one is a band-aid; the second one is structural.

If your task is "keep refining this same API spec for three more hours, just less verbosely" — compact. If your task is "I need to spike a prototype to answer a question that came up while specing the API" — handoff. Mixing them up is the mistake I made for weeks.

There's an even simpler heuristic I now use. Ask: "Will the next part of this conversation share 80%+ of the context that's currently in the window?" If yes, compact. If no, handoff. Most of the times I used to reach for compact, the honest answer was no.

## When to reach for a handoff

Three patterns earned a permanent slot in my workflow.

### 1. The mid-session refactor temptation

I'm deep in building feature A. I notice something in a shared module that's obviously misdesigned — a function doing three things, a config flag with the wrong default, a test that's been quietly skipped for six commits. Old me would have fixed it right then. The current session would inherit twenty messages about that refactor, half of which would still be in the window when I came back to feature A and had to remember what we'd decided about *its* edge cases.

New me writes a handoff. "Refactor `RoutePlanner.normalize()` to split path validation from formatting. Tests at `tests/router/normalize.test.ts` already cover the cases. Skills: brainstorming, TDD." The fresh session picks it up, ships the refactor, comes back clean. Feature A's session stays in the smart zone the whole time.

This is the cheapest single workflow win I've gotten from any AI tooling change this year. The cost of polluting a deep session with an unrelated refactor is much higher than the cost of writing a handoff file.

### 2. Grilling sessions that branch

Grilling sessions — interactive question-driven exploration where I'm letting Claude pressure-test a plan or design — are where handoff really earns its keep. A good grilling session goes wide on purpose. It pokes at edge cases. It surfaces sub-questions. And every so often, one of those sub-questions is going to need its own focused session to actually answer.

Example from last week. I was grilling a plan for a new content-cluster automation. Halfway in, Claude asked: "Have you confirmed the markdown renderer handles nested admonitions when the post is loaded through your CMS?" I had not. The answer was a 90-minute prototyping detour I did not want to run inside the grilling session.

Handoff. "Prototype: feed three nested-admonition test posts through the local CMS preview and capture rendering output. Skills: prototype, verify. Return findings to the grilling session as a one-paragraph result." The prototype session runs separately, dumps a markdown summary, the grilling session reads that summary and continues without ever having loaded the test posts or the renderer dependencies into its own context.

That second handoff — the one going *back* from the prototype to the grilling session — is the part that surprised me. Handoffs are bi-directional. The prototype session writes its findings into a handoff doc and hands them back. The grilling session reads three paragraphs of distilled answer, not 90 minutes of trial-and-error.

### 3. Planning sessions splitting from build sessions

The other pattern I lean on hard: separating *what to build* from *how to build it*. Planning sessions are about decisions — what's in scope, what's the data model, which trade-offs matter. Build sessions are about execution — write the code, run the tests, verify the output.

These two activities pollute each other badly when they share a window. Planning conversations spawn dozens of small "what if we did X" branches that bloat the context but don't survive the decision. Build sessions accumulate test output, error messages, and file diffs that have nothing to do with the original spec.

I run them as two sessions now. Planning session produces a handoff: the locked decisions, the open questions, the structural plan. Build session takes that handoff, executes, and — if anything during the build invalidates a planning decision — writes a handoff back. The planning session re-opens, ingests the feedback, and refines. Loop until the build session has nothing left to push back.

This is the same iteration pattern I described in [the spec workflow agents post](/blog/engineering-with-ai-agents-skills-pipeline), just made portable across sessions. Handoff is what makes that loop run without anyone's context window filling up.

## What goes inside a good handoff file

If you only ever read one section of this post, make it this one.

The handoff doc is doing a specific job: give a fresh session enough to continue the work without dragging in the noise that the parent session accumulated. Get the contents wrong and you've just rebuilt `/compact`'s problem in a new file. Get it right and the fresh session arrives like a senior engineer joining a project mid-sprint — briefed, oriented, productive in ten minutes.

Here's the structure I now use for every handoff, whether the skill generates it or I'm editing by hand:

| Section | What goes in it | What does NOT go in it |
|---|---|---|
| **Goal** | One sentence stating what the next session is responsible for finishing | Background story of how we got here |
| **Context anchor** | Links to ADRs, GitHub issues, design docs, prior handoffs — not the contents, just the pointers | Pasted contents of those documents |
| **Where we are** | Current state: branch, files touched, what's deployed, what's reverted | Step-by-step history of every change |
| **Locked decisions** | Things already decided that the next session must not relitigate | The conversation that produced the decisions |
| **Open questions** | The 2–5 things still unresolved that the next session needs to answer | Speculation about every possible question |
| **What we tried (and why it didn't work)** | Dead ends worth avoiding, written as one-liners | Long failure transcripts or stack traces |
| **Suggested skills** | TDD, brainstorming, verify, prototype, worktrees — whichever skills the next session will likely use | "Maybe try this approach" prose |
| **Quick start** | The first command, the first file to open, the first question to answer | A full tutorial |
| **Sensitive data redactions** | Marker showing what was redacted and where to find it | The actual sensitive data |

Two patterns I see people miss when they write handoffs by hand:

**Don't restate what's in the issue.** If there's a GitHub issue with the user story, the acceptance criteria, and the design rationale, the handoff file should say "see issue #142" — not paraphrase the issue. Paraphrasing is how truth drifts.

**Be honest about open questions.** The temptation is to make the handoff sound complete. "All we need is to ship this." If there are open questions, list them. The next session will discover them anyway, and you want it to discover them in the smart zone of the new context, not after it's already committed to a wrong direction.

I keep a small mental template now. Goal, anchor, state, locked, open, dead-ends, skills, start, redactions. Nine sections. Most of mine end up under 600 words. The whole point is that they're disposable — small enough to read in a minute, focused enough to act on immediately.

## How I use handoffs in my own workflow

Let me make this concrete with how I actually use this on mejba.me and the broader Aria content system.

My typical content production session has at least three phases. Research the topic. Plan the article. Write the article. Each of those phases wants different tools, different context, different attention. The research phase pulls in web search results, scraped competitor URLs, and statistics. The plan phase wants the brand voice file, the cluster taxonomy, and the existing internal-linking map. The write phase wants the plan, the research, and an empty editor.

A few months ago I tried to do all three in one Claude Code session. By the time I was writing the third article in a cluster, the context had ~80,000 tokens of research from articles one and two still floating around, plus the plans for both, plus all three brand voice loads, plus my running notes. Quality was visibly slipping by the second article.

The new flow looks like this:

1. **Research session** — pull current data, find gaps, scan existing posts for internal links. Produces a handoff: "Plan an article about X using these 6 verified facts, these 3 internal link targets, and this competitive angle." File saved to temp dir.
2. **Planning session** — fresh window. Loads the research handoff. Brand voice and cluster map come in clean. Produces another handoff: "Write article about X following this outline, using these specific stats, with these internal links, hitting these emotional beats."
3. **Writing session** — fresh window again. Loads the planning handoff. Writes the article. No research debris, no planning debris, just the plan and a target.

Each session stays under ~60K tokens, deep inside the smart zone, focused on its job. The output quality is markedly better than the single-session approach, and the failure modes — when something goes wrong — are easier to debug because I can read each handoff file and see exactly what was passed.

For code work, I lean on the same split. Planning the architecture is a session. Building the first component is a session. Building the second is a session. If a component build surfaces a question the architecture didn't anticipate, that's a handoff back to the planning session. This is the same logic that makes [git worktrees with parallel agents](/blog/claude-code-git-worktrees-parallel-agents) and [forked sub-agents](/blog/forked-subagents-claude-code-anthropic) feel so natural — they're all the same principle: split work along boundaries the model can keep distinct, instead of forcing the model to keep distinct boundaries inside one bloated context.

<!-- IMAGE: Diagram showing three sequential Claude Code sessions connected by handoff Markdown files, each session staying within the smart zone (sub-120K tokens), versus a single session ballooning into the dumb zone. Alt text: "handoff skill Claude Code multi-session workflow diagram showing context staying in smart zone". Caption: "Three focused sessions with handoff files beat one long session that drifts into the dumb zone every time." -->

## Cross-tool handoffs: why Markdown matters more than I expected

I almost dismissed the Markdown-as-substrate choice as obvious. It's the biggest practical lever in the whole skill.

Markdown is portable. A handoff file generated by Claude Code can be read by Codex CLI without modification. It can be passed to Copilot CLI. It can be loaded into Gemini CLI. I've moved work between three different agent tools in a single project just by handing the same Markdown file around. No format conversion, no glue code, no agent SDK gymnastics.

This is where adversarial review patterns get interesting. I've written before about [running Codex adversarial review against Claude Code's output](/blog/codex-plugin-claude-code-adversarial-review). The handoff file is the perfect input for that pattern. Claude Code produces the work and a handoff describing what it did and what's still open. Codex picks up the handoff, runs critique, produces its own handoff describing what it found. Claude Code resumes with the critique in hand. Each agent works in its smart zone. The Markdown file is the only thing that has to cross boundaries.

Same logic for DIY sub-agents. You don't need a fancy multi-agent orchestrator to run specialized tasks in parallel. You need a way to brief a sub-agent, let it work, and reintegrate its results. Markdown handoffs do that without a framework. The "framework" is the file.

The other thing Markdown gives you: review-before-send. Every handoff I write gets a 30-second scan before I pass it on. I check for the obvious things — did the redaction catch all the secrets, are the locked decisions actually locked, did it list any dead-ends that turned out to be the right path after all. That review step has caught at least three bad handoffs in the last month. JSON or binary blobs don't let you do that.

## What handoff is not

Worth being honest about the limits, because the skill isn't a universal answer.

**Handoff doesn't replace good in-session discipline.** If you're letting one session sprawl across six unrelated topics without ever splitting, the handoff skill won't save you. It'll just give you a slightly cleaner sprawling-session-summary at the end. The discipline of recognizing when to split is yours — the skill just makes the split cheap once you commit.

**Handoff isn't for trivially short tasks.** If the whole job fits in 20K tokens and one session, you're better off finishing it than writing a handoff. The overhead of the handoff format isn't free. I use it when the work is genuinely going to span multiple sessions, not as ceremony.

**Handoff doesn't fix bad upstream artifacts.** If your ADRs are wrong, your issue templates are vague, and your plans are hand-wavy, the handoff will reflect that. Pointers are only as good as what they point at. I noticed my own ADRs got sharper after I started writing handoffs that referenced them — knowing the next session would read those documents cold made me write them better.

**Handoff isn't a substitute for verification.** A handoff says "we got here." It doesn't prove the code works. Fresh sessions should still run tests, still verify before claiming completion. The handoff describes intent and state. Reality still has to be checked.

The honest summary: handoff is a coordination tool. It coordinates work across sessions that would otherwise share context badly. It doesn't replace the work itself, the verification of the work, or the upstream documents the work depends on.

## What changes when you start working this way

A few patterns I've noticed in my own work since handoff became routine.

I plan more before I build. Knowing I'll need to write a handoff at the end of the planning session forces me to actually finish the plan instead of drifting into "let me just try the first thing." If I'm going to hand this to a build session, the plan needs to be complete enough to act on. That's a forcing function the single-session approach didn't have.

I notice scope creep faster. Mid-session, when I catch myself thinking "let me just also fix this thing real quick" — that thought now reflexively becomes "let me write a handoff for that thing." The cost of a side-quest in the current session is high. The cost of writing a handoff and continuing my current work is low. The math tilts toward focus.

My sessions are shorter. I used to run hour-long Claude Code sessions as a matter of course. Now most sessions are 20–40 minutes. The work is the same; the sessions just match the actual scope of the task instead of bundling three tasks into one window.

I trust my agents more. When a fresh session loads a tight handoff and continues the work cleanly, the output feels more reliable than when a single session has been running for an hour and the model is half-remembering what we decided. The smart zone is real. Keeping work inside it is a quality investment, not a tax.

## Frequently Asked Questions

### What is the handoff skill in Claude Code?
The handoff skill compresses a Claude Code session's relevant context into a Markdown file that a fresh session can use to continue the work without inheriting context bloat. It saves the file to the OS temp directory, references existing documents instead of duplicating them, redacts sensitive data, and suggests which skills the next session should use. For full setup and usage patterns, see the workflow section above.

### How is handoff different from /compact?
`/compact` summarizes the current session and replaces its history with the summary, keeping you in the same conversation. `handoff` produces a portable Markdown file scoped to the next session's specific focus, then lets you start fresh. Compact is for trimming one long task; handoff is for splitting unrelated work across multiple sessions.

### When should I use handoff instead of just continuing the session?
Use handoff when the next chunk of work shares less than 80% of the context currently in your window — mid-session refactors of unrelated code, prototyping spikes that branch off a grilling session, or splitting planning from execution. If the work is a direct continuation of what you're already doing, compaction is usually the better choice.

### What's the practical context window before Claude Code starts degrading?
Claude Code's 1M token ceiling is the marketing number. Practical quality degradation typically starts around 120,000 tokens, with some teams reporting noticeable drift as early as 50% of the window. Budgeting attention inside the smart zone matters more than the ceiling itself.

### Can I use handoff across different AI coding agents?
Yes. The handoff output is plain Markdown, which makes it portable across Claude Code, Codex CLI, Copilot CLI, Gemini CLI, and any other agent that reads text. This is what enables cross-agent patterns like running Codex adversarial review against Claude Code's output without writing custom glue code.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
