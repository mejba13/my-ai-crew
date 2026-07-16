**BRAND:** mejba.me
**TITLE:** Claude Code and Codex Dual-Stack: My Real Playbook
**META TITLE:** Claude Code + Codex Dual-Stack Setup (2026 Playbook)
**SLUG:** claude-code-codex-dual-stack-playbook
**PRIMARY KEYWORD:** Claude Code Codex dual-stack
**META DESCRIPTION:** Run Claude Code and Codex on the same project without duplicating files. My exact AGENTS.md, .codex layout, conversion prompt, and Session Handoff skill.
**TAGS:** Claude Code, Codex CLI, AGENTS.md, AI Agents, Developer Workflow

---

The first time I tried to run Claude Code and Codex on the same project, I made it about a week before the two of them started silently disagreeing with each other. Claude Code thought we were using pnpm. Codex thought it was npm. Claude Code had a "never commit without running the lint script" rule baked into `CLAUDE.md`. Codex was on a separate `AGENTS.md` that hadn't been updated in eleven days. I'd duplicated my skills folder, badly. Two SKILL.md files that should have been identical had quietly drifted because I edited one and forgot the other.

By day eight I had a Frankenstein repo where the same prompt produced different code depending on which CLI I happened to launch. Which is exactly the failure mode that makes most developers give up on multi-tool setups and retreat to a single vendor.

The mistake wasn't running both tools. The mistake was treating them as two separate worlds. Claude Code and Codex already agree on more than 80% of how a coding agent should be configured in 2026. They both read a project-level markdown file at startup. They both support the same SKILL.md open standard. They both have a global vs project-local config hierarchy. The differences are real, but they're narrow and predictable — and once you wire them up correctly, the result is a single project that two completely different AI coding agents can drop into and immediately understand.

That's what this post is. The exact file layout I run. The natural-language prompt I use to bootstrap a Codex config from an existing Claude Code one (or vice versa). A worked example of a Session Handoff skill that survives the switch between CLIs. And the four things you have to manually keep in sync to stop the drift problem before it starts.

I've been running this dual-stack setup for about six weeks now in my main content repo. Aria — my content agent — runs on Claude Code. Codex handles repair work, parallel batch jobs, and any task where I want a second-vendor opinion. Same project, same brain, two interchangeable CLIs. Here's how it's wired.

## Why Single-Vendor Lock-In Is Now an Unnecessary Risk

Before the file layout, the philosophy — because the wiring only matters if you understand why you'd bother.

For most of 2025, picking a single CLI agent made sense. Each tool had its own personality, its own config quirks, and the switching cost of learning two ecosystems wasn't obviously worth it. I was a Claude Code loyalist. I told people that out loud. Anthropic shipped an update, I installed it. Anthropic had an outage, I waited.

Two shifts in 2026 changed my mind.

**Shift one: outages got expensive.** Every major model provider had at least one rough day this year. OpenAI had a multi-hour incident in February. Anthropic had API stutters in March. Google's Gemini routing got flaky for several hours in April before anyone outside the engineer-on-call channel noticed. If your entire workflow lives behind one vendor's CLI, one outage takes your whole day. That's not a technology problem. It's a single-point-of-failure problem. Multi-vendor setups are a basic resilience pattern in every other part of infrastructure — databases, CDNs, payment processors — and they're finally becoming standard for coding agents too.

**Shift two: the ruts.** Sometimes Claude Code gets stuck. Not "broken" stuck — *creatively* stuck. It picks an approach, runs with it, and when that approach doesn't work, it keeps refining the same approach harder. I've watched Opus 4.7 spend forty minutes trying to fix a flaky test by adding retries when the actual fix was rewriting the test in a different style. A second agent — not as a replacement, but as a second opinion — usually breaks the rut in one prompt. Switching CLIs is the cheapest way I've found to get fresh model perspective without losing project context.

The goal isn't "pick the best CLI." The goal is **tool agnosticism**. Build a setup where Claude Code and Codex can both work on the same codebase, with the same project knowledge, and you can move between them without losing your place. The CLI becomes interchangeable. The project context is what's sacred.

The wiring is what makes that possible. Let's get into it.

## The Mental Model: One Shared Brain, Two Skins

Here's the framing that finally made the dual-stack thing click for me. Every coding agent in 2026 is built from three layers:

1. **Project knowledge** — what this codebase is, what the conventions are, what tools to use. This is the markdown file the agent reads at startup. `CLAUDE.md` for Claude Code. `AGENTS.md` for Codex. Same job, different filename.
2. **Skills** — reusable capability packs. SKILL.md is now an open cross-platform standard adopted by Claude Code, Codex CLI, Gemini CLI, Cursor, and most of the newer agents. A well-written skill works in any of them. This is the part of the dual-stack setup that's genuinely portable.
3. **Agents / sub-agents and configuration** — the tool-specific layer. Claude Code uses markdown files with YAML frontmatter in `.claude/agents/`. Codex uses TOML files in `.codex/agents/`. Configuration lives in `.claude/settings.json` for one and `~/.codex/config.toml` for the other. This is the layer that has to be maintained separately.

Once you draw the line between what's portable and what isn't, the dual-stack workflow stops feeling like duplication and starts feeling like — two front doors to the same building. The library inside is the same. The skills on the shelves are the same. Only the lock on each door is different.

So the wiring problem reduces to four questions:
- Where do I put the shared project knowledge, and how do both CLIs find it?
- Where do skills live so both tools can read them?
- Where do tool-specific agent files go without colliding?
- What's the minimal config each CLI needs in its own format?

Let me walk through the answer to each one, with the actual file layout I run in production.

## The Shared Brain: AGENTS.md as the Canonical File

The first decision is which markdown file is the source of truth. Both Claude Code and Codex read a project-level file at startup. Codex reads `AGENTS.md` natively. Claude Code reads `CLAUDE.md` natively. But here's the move that makes everything easier: **Claude Code supports `@import` syntax inside CLAUDE.md.** That means you can write a one-line CLAUDE.md that imports AGENTS.md, and Claude Code now reads the exact same project briefing as Codex.

In my repos, AGENTS.md is canonical. CLAUDE.md is a thin wrapper that imports it and adds Claude-specific notes when I genuinely need them.

```
my-project/
├── AGENTS.md              # the real project knowledge — canonical
├── CLAUDE.md              # one-line @import of AGENTS.md + Claude-only notes
├── .claude/               # Claude Code-specific
├── .codex/                # Codex-specific
├── skills/                # shared SKILL.md files (cross-platform)
├── docs/                  # shared reference material both agents read
└── src/
```

The `CLAUDE.md` is dead simple:

```markdown
# Project: my-ai-crew

@AGENTS.md

## Claude Code-specific notes
- Use the `aria` subagent in `.claude/agents/aria.md` for any
  content-generation work.
- Plan mode is fine for refactors; skip it for new file creation.
```

That's the whole file. Claude Code loads AGENTS.md inline at session start, then adds the Claude-specific block. Codex reads AGENTS.md directly. One source of truth. No drift. If I update conventions in AGENTS.md, both CLIs see the change on next startup.

For comparison, here's the relevant chunk of my real `AGENTS.md` for the content repo:

```markdown
# Project: my-ai-crew

## What this is
Multi-brand content generation system. Four brands, each with a
distinct voice. Posts are saved to content/{brand}/[slug].md. No
build system, no tests — the workflow is agent-driven.

## Conventions
- All article files start with `**BRAND:**` — never YAML frontmatter.
- META DESCRIPTION must be 150-160 characters. Count before saving.
- Word count floor: 3,000.
- Brand voices live in .claude/agents/aria.md and .codex/agents/aria.toml
  (keep in sync — they're the same persona in two formats).

## Tools available
- WebSearch — use for fact verification, never guess versions/pricing
- Glob — scan content/{brand}/*.md before writing
- Read, Write, Edit — standard file ops

## Never
- Commit without running `npm run lint` (the project doesn't have lint
  enforcement in CI yet — agents must self-enforce).
- Create files with names like `untitled.md` or `temp.md`.
- Delete a content/{brand}/*.md file without explicit user instruction.
```

That's the same file both Codex and Claude Code see. Notice how it says nothing about either CLI's specific syntax. The shared brain stays vendor-agnostic. Tool-specific behavior goes in `.claude/` or `.codex/`.

This is the single most important pattern in the dual-stack setup. Get this right and everything else falls into place. Get it wrong — write two separate files and try to keep them in sync by hand — and you'll be back to my day-eight drift problem within a week.

## The Side-by-Side File Layout

Here's the comparison I wish I'd had when I started. Same job in each row, different file path or format depending on which agent. Bookmark this table — it answers about 70% of "where does this go" questions you'll have over the first month.

| Concern | Claude Code | Codex CLI |
|---|---|---|
| Project knowledge file | `CLAUDE.md` (imports `@AGENTS.md`) | `AGENTS.md` |
| Global / user knowledge | `~/.claude/CLAUDE.md` | `~/.codex/AGENTS.md` |
| Project config | `.claude/settings.json` | `.codex/config.toml` |
| Local-only config (gitignored) | `.claude/settings.local.json` | `.codex/config.local.toml` |
| Global / user config | `~/.claude/settings.json` | `~/.codex/config.toml` |
| Sub-agents — project | `.claude/agents/*.md` (markdown + YAML) | `.codex/agents/*.toml` |
| Sub-agents — global | `~/.claude/agents/*.md` | `~/.codex/agents/*.toml` |
| Skills (the portable standard) | `.claude/skills/<skill-name>/SKILL.md` | `~/.agents/skills/<skill-name>/SKILL.md` (or `.codex/skills/`) |
| Slash commands / custom commands | `.claude/commands/*.md` | Codex uses skills + agents instead |
| Hooks | `.claude/hooks/*` | Limited equivalent in `config.toml` |
| Sub-agent invocation | Auto-invoked by main agent based on description match | **Explicit only** — main agent must be told to spawn |

A few things worth pulling out of that table because they're easy to miss and they're where most of the dual-stack confusion lives.

**Project knowledge files can be unified via `@import`.** Verified directly in the [Claude Code docs](https://code.claude.com/docs/en/claude-directory) and the [AGENTS.md vs CLAUDE.md guide](https://blink.new/blog/agents-md-vs-claude-md). Codex has no equivalent import mechanism — but it also doesn't need one, because AGENTS.md is already its native file. That's why I make AGENTS.md the source of truth: it's the side that *has* to be the original.

**Config files are completely separate.** This is the place where unification isn't possible. `.claude/settings.json` is JSON. `.codex/config.toml` is TOML. They configure different things — tool permissions, allowed paths, model preferences — in vendor-specific ways. You'll maintain both. The good news: once they're set up, you rarely touch them again.

**Skills are the genuinely portable layer.** SKILL.md started as Anthropic's spec for Claude Code and was [adopted as an open cross-platform standard](https://www.agensi.io/learn/agent-skills-open-standard) by Codex CLI, Gemini CLI, and Cursor. The structure — a YAML frontmatter block with `name` and `description`, then markdown instructions below — works in all of them. You can put your skills in a shared folder, point both CLIs at it, and the same skill works in either.

**Sub-agent invocation is the biggest behavioral difference.** Claude Code's main agent automatically routes work to a relevant sub-agent if the description matches. Codex won't. Codex only spawns sub-agents when you explicitly ask. This is documented in the [official Codex subagents reference](https://developers.openai.com/codex/subagents) and confirmed in [Simon Willison's writeup](https://simonwillison.net/2026/Mar/16/codex-subagents/). It's not a bug. It's a deliberate design choice — Codex's sub-agents are tuned for parallel batch workloads where you want explicit control over what spawns when. But it means a workflow you wrote assuming auto-invocation in Claude Code will fall flat in Codex unless you adjust.

Now the parts you actually have to build.

## The Conversion Prompt: From Claude Code to Codex in One Shot

Most engineers who already have a working Claude Code setup don't want to hand-translate it to Codex. They want the second CLI to bootstrap itself by reading the first one. This is exactly the kind of work an agent is good at — and the prompt below is what I use as the starting point in any repo where I'm adding Codex to an existing Claude Code setup.

Drop this into a fresh Codex session in the project root. It assumes AGENTS.md already exists (or that CLAUDE.md exists and is going to become AGENTS.md). Adjust the paths for your project, and tweak the "skills to convert" list to match your repo.

```text
You are setting up Codex CLI in a project that already runs Claude Code.

Goal: create a complete .codex/ directory that mirrors the existing
.claude/ setup, using Codex's native formats (TOML for agents, TOML
for config, SKILL.md for skills), without duplicating shared project
knowledge.

Do this in order:

1. Read AGENTS.md. If it doesn't exist, read CLAUDE.md, strip any
   Claude-specific blocks, and write the result to AGENTS.md. Then
   rewrite CLAUDE.md to a thin file that does `@AGENTS.md` plus any
   Claude-only notes.

2. Read every agent definition in .claude/agents/*.md. For each one,
   create the equivalent in .codex/agents/<same-name>.toml.
   Preserve the agent's name, description, model preference,
   tools available, and system prompt. Use the official Codex agent
   TOML spec from developers.openai.com/codex/subagents — do not
   invent fields.

3. Inventory every skill in .claude/skills/. SKILL.md is already a
   cross-platform open standard, so copy each skill folder verbatim
   into the shared skills/ directory at repo root. Update both CLIs'
   config to point at that shared location.

4. Read .claude/settings.json. Create .codex/config.toml with the
   equivalent settings: allowed/denied tools, sandbox preferences,
   default model, MCP servers. Where Claude Code has no Codex
   equivalent (hooks, slash commands), note them in a TODO comment
   inside config.toml — do NOT silently drop them.

5. Verify by listing the contents of .codex/ and confirming that the
   AGENTS.md you wrote (or kept) is readable by Codex. Run
   `codex --version` and report it. Do NOT run any actual agent
   workflows yet — this is bootstrap only.

Research before writing. Pull the current Codex CLI agent TOML
schema, config.toml reference, and skills location from the official
OpenAI Codex developer docs. The spec changes; do not rely on training
data.

Report a clear summary at the end of:
- Files created (with paths)
- Files modified (with paths and what changed)
- Things that don't map cleanly between Claude Code and Codex (with
  recommended workarounds)
- Anything you weren't sure about and want me to verify
```

I've run this prompt against four projects so far. In two of them it produced a clean, working `.codex/` directory on the first try. In one it got the config.toml slightly wrong on MCP server syntax — Codex's MCP block uses a different structure than Claude Code's and the agent guessed. The fourth project had a custom hook that didn't have a Codex equivalent; the agent flagged it as a TODO instead of trying to fake it, which was the right call.

The prompt works because it tells the agent three things most ad-hoc translation prompts skip: research the current spec before writing (the spec changes), flag things that don't map cleanly instead of silently dropping them, and don't run any actual workflows in the bootstrap pass. That last constraint matters — you don't want the agent to "test" the new Codex setup by triggering your real production agents.

Reverse direction works too. If you're starting with a Codex repo and want to add Claude Code, the same prompt structure works — just swap the source and target. I've done that once and it was the cleaner of the two directions, mostly because AGENTS.md is already the side with no Claude-specific syntax to strip.

## A Real Codex Sub-Agent in TOML (Worked Example)

The sub-agent format is the place where the two CLIs diverge most. Claude Code uses markdown with YAML frontmatter. Codex uses TOML. Here's the same agent — a focused code reviewer — in both formats, so you can see what a clean conversion looks like.

**`.claude/agents/code-reviewer.md`** (Claude Code):

```markdown
---
name: code-reviewer
description: Reviews pull request diffs for security issues, style
  violations, and missing test coverage. Auto-invoke when the user
  asks for a code review or mentions PR #.
model: opus
tools:
  - Read
  - Grep
  - Bash
---

You are a senior code reviewer. Your job is to scan the diff and
produce a focused review.

Priorities in this order:
1. Security issues (auth, injection, secrets in code, OWASP Top 10)
2. Correctness bugs (off-by-one, null derefs, race conditions)
3. Missing or weak tests
4. Style and convention violations

Output format: a markdown report with sections for each category.
Use code blocks to show problematic lines. Suggest specific fixes.

Do NOT recommend wholesale refactors. Stay scoped to what the diff
touches.
```

**`.codex/agents/code-reviewer.toml`** (Codex CLI):

```toml
name = "code-reviewer"
description = "Reviews pull request diffs for security issues, style violations, and missing test coverage. Spawn explicitly when the user requests a code review."
model = "gpt-5.4"
sandbox_mode = "workspace-write"

developer_instructions = """
You are a senior code reviewer. Your job is to scan the diff and
produce a focused review.

Priorities in this order:
1. Security issues (auth, injection, secrets in code, OWASP Top 10)
2. Correctness bugs (off-by-one, null derefs, race conditions)
3. Missing or weak tests
4. Style and convention violations

Output format: a markdown report with sections for each category.
Use code blocks to show problematic lines. Suggest specific fixes.

Do NOT recommend wholesale refactors. Stay scoped to what the diff
touches.
"""

[agents]
max_threads = 4
max_depth = 1
job_max_runtime_seconds = 600
```

A few things to notice when you compare them side by side.

The TOML version has a `sandbox_mode` field that the markdown version doesn't. Codex makes sandboxing explicit at the agent level. Claude Code handles equivalent permissions in `.claude/settings.json` at the global or project level. Neither is wrong; they're just different places for the same control.

The description in the Codex version explicitly says "Spawn explicitly when the user requests a code review." That's not boilerplate — it's a real adjustment for how Codex actually behaves. Because Codex sub-agents don't auto-invoke, the description is mostly a label for the human, not a routing signal. In Claude Code, the description is doing real work — the main agent reads it and decides whether to delegate. In Codex, the description is documentation. Don't waste energy crafting it for routing; craft it for clarity when you explicitly call the agent.

The `[agents]` block at the bottom is Codex-specific and controls how many parallel threads the agent can fan out into. Claude Code's sub-agents don't have a directly equivalent parameter; the main agent decides parallelism dynamically. If you have a Codex agent that's tuned for batch parallel work, you'll lose that capability in the Claude Code conversion unless you write a slash command that explicitly orchestrates parallel calls.

## The Session Handoff Skill (Worked Example)

This is the skill I built specifically for the dual-stack workflow, and it's the one that makes the whole setup actually usable in practice rather than theoretical. When I want to move work from Claude Code to Codex (or back), I invoke this skill at the end of the current session. It writes a compact handoff file to a shared location. The next session — in whichever CLI — reads that file first and picks up exactly where the last one stopped.

Because SKILL.md is cross-platform, this skill lives once in the shared `skills/` directory and works identically in both CLIs.

**`skills/session-handoff/SKILL.md`:**

```markdown
---
name: session-handoff
description: Summarize the current session into a structured handoff
  document so another agent (or another CLI) can pick up exactly
  where this one left off. Invoke at the end of a session before
  switching CLIs or closing the terminal.
---

# Session Handoff

Your job is to produce a handoff document that lets another coding
agent — possibly in a different CLI — resume this work without
losing context.

## When to invoke

The user will explicitly run this skill at the end of a working
session. Do NOT invoke yourself proactively.

## What to capture

Write a single markdown file to:
  `.handoff/session-{YYYY-MM-DD-HHmm}.md`

The file MUST have these sections, in this order:

### 1. Goal
One paragraph: what was the user trying to accomplish this session?
Pull from the earliest user messages and any explicit "the goal is"
or "we're trying to" statements.

### 2. What was done
Bulleted list. Each bullet: a concrete action taken (file created,
function modified, test added, command run). Reference file paths
with backticks. Do NOT include exploratory reads.

### 3. Open decisions
Bulleted list of unresolved questions or decisions deferred to later.
Each one with a one-sentence framing of the trade-off.

### 4. Next steps
Numbered list of the next 3-5 concrete actions. Specific enough that
a fresh agent could execute them without asking clarifying questions.

### 5. Files in flight
Bulleted list of files that were modified but not yet committed, or
files that were partially edited and need to be finished.

### 6. Active context the next agent needs
Anything the next agent has to know that isn't obvious from reading
the code — design decisions made in conversation, constraints from
the user, tool limitations encountered.

## Format rules

- Maximum 600 words total. Be ruthless about cutting noise.
- Use second-person ("you") for the next agent. They are the audience.
- Do NOT include code blocks longer than 10 lines. Reference files
  by path instead.
- End with a single line: `Last CLI: claude-code` or `Last CLI: codex`
  so the next agent knows which environment produced the handoff.

## How the next agent uses it

On startup, both CLIs should be told (via CLAUDE.md / AGENTS.md) to
check `.handoff/` for the most recent file. If one exists less than
24 hours old, read it before doing anything else. This is how
continuity survives the CLI switch.
```

And the corresponding line I add to `AGENTS.md` so both CLIs know to check for a handoff at session start:

```markdown
## Session continuity

At the start of every session, check `.handoff/` for the most recent
`session-*.md` file. If one exists and was modified in the last 24
hours, read it before doing anything else. It contains the previous
session's open decisions, in-flight files, and next steps. Treat it
as authoritative until the user explicitly says otherwise.
```

This pair — the skill plus the AGENTS.md instruction — is what makes the dual-stack workflow feel like one continuous environment instead of two disconnected tools. I switched from Claude Code to Codex mid-task last week, ran the handoff skill, opened Codex in the same repo, and Codex started its first response with "Picking up from the handoff: I'll continue with the WooCommerce webhook validation you were working on." That's the experience you want.

Two implementation notes from running this for six weeks. First, `.handoff/` should be gitignored. It's session state, not project state, and committing it just creates noise. Second, prune the directory. I have a weekly cron job that deletes handoff files older than fourteen days. Otherwise it gets crowded.

## The Four Things You Have to Maintain Manually

I'd love to tell you the dual-stack setup is fully automated and never drifts. It isn't, and it does. Four maintenance tasks are non-negotiable, and if you skip any of them for more than a week or two, you'll be back to the day-eight chaos I described at the top.

**One: when AGENTS.md changes, verify both CLIs see it on the next session.** This is usually automatic — both tools read the file at startup. But if you're using Pattern A from my [side-by-side same-repo post](https://www.mejba.me/blog/claude-code-codex-side-by-side-same-repo) (symlinking CLAUDE.md to AGENTS.md), make sure the symlink survived your last refactor. I've broken it once by accidentally deleting CLAUDE.md and recreating it as a new file, which silently severed the link.

**Two: when you add or modify a sub-agent in one CLI, decide consciously whether to mirror it in the other.** Not every agent has a sensible equivalent. The `aria` content agent in my repo exists in both `.claude/agents/aria.md` and `.codex/agents/aria.toml` because I genuinely want both CLIs to be able to write content. A specialized Claude Code agent that uses Anthropic-specific tools (like the Computer Use API) doesn't have a Codex equivalent and shouldn't be force-fit into one. Decide once per agent. Document the decision in the agent file itself with a one-line comment.

**Three: when you update a skill in the shared `skills/` directory, test it in both CLIs.** SKILL.md is portable but not bulletproof. Sometimes a skill that works in Claude Code references tools or context that Codex handles differently. The fix is usually small — adjust an instruction to be CLI-agnostic — but you have to actually run the skill in both to catch the difference.

**Four: when you change `.claude/settings.json`, decide whether the equivalent change belongs in `.codex/config.toml`.** These are the two files that can't be unified, and they're also the two files that tend to drift the most because they're vendor-specific and you're often editing one in response to a CLI-specific problem. I keep a comment block at the top of each file listing the cross-references — "this corresponds to X in the other CLI" — so future me knows where to look.

Set a weekly fifteen-minute review. Read both AGENTS.md and CLAUDE.md. Diff the agent directories. Run one trivial prompt in each CLI to confirm both still launch cleanly. That's it. Fifteen minutes a week is the maintenance cost of avoiding three-hour debugging sessions when the two tools start producing contradictory work.

## The Sharp Edges You Should Know About

Six weeks of running this setup has surfaced four things that aren't obvious from the documentation. Worth knowing before you hit them.

**Two CLIs editing the same file simultaneously will overwrite each other.** Neither tool has cross-CLI file locking. If you have Claude Code in one terminal pane refactoring `src/auth.ts` and Codex in another pane editing the same file, the second write wins and the first edit is gone — no warning, no merge, no recovery unless you have git uncommitted. The fix is operational: agree with yourself that each CLI owns a different part of the repo during a working session, or commit obsessively. Don't run both agents on overlapping files.

**Codex sub-agents won't auto-invoke even when you really want them to.** I've already covered this above, but it bears repeating because it's the single most common "wait, why didn't that work" moment in the first week. A workflow you wrote in Claude Code that assumes the main agent will route to a `code-reviewer` sub-agent when it sees a PR mention won't behave the same way in Codex. You have to explicitly say "use the code-reviewer agent for this." Some users wire this up with a Codex skill that always calls a specific sub-agent when its trigger matches — a workaround that gets close to Claude Code's behavior — but the default is explicit.

**Some Codex CLI features ship behind config flags that aren't documented prominently.** I won't list specific examples because the spec evolves fast, but assume that when you read a blog post mentioning a Codex capability, you may need to check `~/.codex/config.toml` for the right flag to enable it. The [official Codex config reference](https://developers.openai.com/codex/config-reference) is the canonical source, and it's worth keeping a tab open on it.

**Global settings live in different places and have different precedence rules.** Claude Code: `~/.claude/CLAUDE.md` and `~/.claude/settings.json` apply to every project. Codex: `~/.codex/AGENTS.md` and `~/.codex/config.toml` apply globally, but project-level `.codex/config.toml` overrides global. The hierarchy is similar but not identical. If you put a global rule in Claude Code's global file and assume Codex will respect the same rule from its global file, you're correct — but you have to write it in both. Global config is the one place where there's no avoiding duplication.

These aren't dealbreakers. They're things to know so you don't waste a Tuesday figuring out the same gotcha I did.

## What Six Weeks of Dual-Stack Actually Got Me

Some honest before-and-after, because numbers matter and I don't want to leave you with vibes.

Before the dual-stack setup, my single-CLI Claude Code workflow had two recurring failure modes: outage-driven downtime (anywhere from ten minutes to two hours per incident, hit me roughly twice a month) and creative-rut downtime (sessions where the agent kept refining the wrong approach until I gave up and started a fresh chat, costing me probably twenty to forty minutes a week in lost context and re-explanation). Add those up across a month and I was losing somewhere in the range of three to five working hours to single-vendor friction.

Six weeks in with the dual-stack setup, outage downtime is effectively zero. When Claude Code's CLI is sick or Anthropic's API is degraded, I switch to Codex on the same repo, with the same project context, in under thirty seconds. The handoff skill makes the context transfer painless. Creative-rut downtime is significantly reduced — most ruts break the moment I run the same prompt in the other CLI, because a different model often picks a different approach. Not zero, but rare enough that I'm not tracking it as a recurring problem anymore.

What I didn't expect: the parallelism. Some afternoons I have Claude Code doing a long-horizon refactor in one pane and Codex doing a parallel batch job in another, both reading the same AGENTS.md, both writing into the same repo. That's twice the throughput on independent tasks without doubling my mental load. As long as the two agents are working on different files (see the sharp-edge warning above), it just works.

What I'd push back on if I'm being honest: the maintenance cost is real. Fifteen minutes a week sounds trivial, and it is, but it's a discipline you actually have to enforce. If you skip the weekly review for a month, drift will accumulate. The dual-stack workflow is not zero-maintenance. It's low-maintenance, and only if you treat it that way.

The other thing worth being honest about: this is more useful if you genuinely need both vendors. If you write occasional scripts and Claude Code by itself works fine, you don't need this. Where it earns its keep is when you're running an actual production workflow — content pipelines, agent swarms, anything where downtime translates directly to lost work. For those use cases, single-vendor dependency is now a bigger risk than the cost of running two CLIs.

## What I'd Do Differently if I Were Starting Today

Three things I'd change about my own rollout, in case you're starting cleaner than I did.

Start with AGENTS.md, not CLAUDE.md. Even if Claude Code is the agent you use most. AGENTS.md as the canonical file means you never have to do the "strip the Claude-specific bits to share with Codex" cleanup later. The first time I migrated a repo from CLAUDE.md to AGENTS.md I spent a couple of hours untangling Claude-specific syntax from project knowledge. If you start with AGENTS.md, that work never happens.

Put skills in a shared `skills/` directory at repo root from day one, even before you actually need them shared. Both CLIs can be configured to read from a custom skills location. Doing it on day one costs nothing. Doing it on month three means moving every skill file and updating both CLIs' config to point to the new location — fiddly and easy to get wrong.

Write the Session Handoff skill on day one too. It's the smallest skill in my repo and the highest-leverage one. The first time you switch CLIs mid-task and the new one greets you with "I see we were in the middle of debugging the auth webhook — let me continue from where you left off," you'll wonder how you ever worked without it.

The dual-stack workflow isn't a hedge against picking the wrong tool. It's an acknowledgment that the right tool is "whichever one is healthy and unstuck right now." If you've been treating Claude Code or Codex as the foundation your work depends on, consider what your workday looks like the next time that foundation has a bad afternoon. Then spend the two hours it takes to set up the other one. The second tool isn't a competitor to the first one. It's the insurance policy that lets the first one stay your favorite.

## Frequently Asked Questions

### Can Claude Code read AGENTS.md natively without the @import trick?
Not as a primary context file — Claude Code's primary context file is `CLAUDE.md`, and it must be uppercase. The `@import` syntax inside CLAUDE.md is the supported way to pull AGENTS.md into Claude Code's startup context. This pattern is documented in the [AGENTS.md vs CLAUDE.md guide](https://blink.new/blog/agents-md-vs-claude-md) and is the cleanest path to a single source of truth.

### Do Codex sub-agents auto-invoke like Claude Code sub-agents do?
No. Codex sub-agents require explicit invocation by the main agent or the user. This is by design — Codex is tuned for parallel batch workloads where explicit control matters more than automatic routing. A Claude Code workflow that relies on auto-routing will need to be adjusted when you port it to Codex; explicit "use the X agent" instructions become mandatory.

### Are SKILL.md files truly identical across Claude Code and Codex?
Mostly. SKILL.md is an open cross-platform standard, and the file structure (YAML frontmatter with `name` and `description`, markdown instructions below) is identical. But skills that reference CLI-specific tools or context can behave differently. Always test a shared skill in both CLIs before assuming it works in either.

### Where do global settings live for each tool?
Claude Code: `~/.claude/CLAUDE.md` for global knowledge and `~/.claude/settings.json` for global config. Codex: `~/.codex/AGENTS.md` for global knowledge and `~/.codex/config.toml` for global config. Project-level files in `.claude/` or `.codex/` override global where they overlap. Global is the one place where some duplication is unavoidable.

### What happens if I run Claude Code and Codex on the same file at the same time?
The second write wins and the first edit is overwritten with no warning. Neither CLI has cross-tool file locking. Operational discipline is the only fix — agree to scope each CLI to different files during a session, or commit changes aggressively so git can recover the lost work.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
