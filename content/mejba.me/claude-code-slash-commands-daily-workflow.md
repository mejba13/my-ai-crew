**BRAND:** mejba.me
**TITLE:** Claude Code Slash Commands I Actually Use Daily
**META TITLE:** Claude Code Slash Commands I Use Daily (2026)
**SLUG:** claude-code-slash-commands-daily-workflow
**PRIMARY KEYWORD:** Claude Code slash commands
**META DESCRIPTION:** The Claude Code slash commands I reach for every day — /clear, /rewind, /resume, plan mode, /statusline, and the custom commands that make Claude plan first.
**TAGS:** Claude Code, Slash Commands, Developer Productivity, AI Workflows, Deep Dive

---

The fastest speed-up I got in Claude Code this year wasn't a model upgrade. It was learning to type a single forward slash and then four letters.

I'd been using Claude Code daily for over a year before I admitted something embarrassing: I was leaving most of its leverage on the table. I'd open a session, dump a paragraph of instructions, watch it work, hit a wall, and start a brand-new session by quitting the terminal entirely. Every. Single. Time. I was paying for context I kept throwing away, re-explaining the same project from zero, and burning usage on conversations that had drifted so far off-task that Claude was basically guessing.

Then I started actually using the slash commands. Not the way the docs list them — alphabetically, flatly, like a glossary nobody reads. I mean the four or five I now reach for so reflexively that my fingers move before my brain finishes the thought. The ones that decide whether a two-hour build feels like flow or feels like fighting the tool.

This is the practical version of that. The Claude Code slash commands I genuinely use every day, grouped by the actual problem each one solves — context that's gone stale, a build that went sideways, a plan I needed to see before any code got written. For each one: what it does, the exact way I invoke it, and the specific moment I reach for it on real work.

A couple of the "commands" floating around in tutorials and videos turned out to be community shorthand or features that work a little differently than people describe. I'll flag those honestly as we go, because knowing the *accurate* picture is the whole point — a command you think exists but doesn't is worse than no command at all.

Let's start where every good session starts and ends: with context.

## What are slash commands in Claude Code?

Slash commands are shortcuts you trigger by typing `/` in the Claude Code prompt, which opens an autocomplete menu of actions you can run without writing out long instructions. They control the *session itself* — clearing context, rewinding code, resuming old conversations, configuring the interface — rather than being prompts you send to the model.

That distinction matters more than it sounds. A normal message asks Claude to *do something in your codebase*. A slash command asks Claude Code *to do something to the session you're working in*. One edits files; the other manages the environment those edits happen in.

The autocomplete shows up the moment you type `/` — in the terminal and in the desktop app — so you don't need to memorize exact spellings. Type `/re` and you'll see `/rewind` and `/resume` surface together. That's also how I discover commands I'd forgotten existed, which is half the reason I bother typing the slash at all instead of reaching for a keyboard shortcut I half-remember.

Here's the thing most cheat sheets get wrong: they treat all forty-odd commands as equally worth learning. They're not. I use maybe six of them daily and the rest almost never. So I'm not going to list everything. I'm going to walk you through the handful that earn their place in muscle memory — and tell you which ones the internet gets wrong.

The first one is the command I should have learned on day one.

## Context management: /clear, /resume, and /rewind

Most of the pain I used to feel in Claude Code traced back to a single root cause: I was bad at managing context. Either I had too much of it (a bloated conversation where Claude kept tripping over old, irrelevant instructions) or I'd lost the context I actually wanted (a session I closed too early and couldn't get back). Three commands fixed nearly all of it.

### /clear — the reset I avoided for far too long

`/clear` wipes the current conversation history from the context window and starts you fresh — without deleting your project memory.

That second half is the part I got wrong for months, and it's worth being precise about because it changes how aggressively you should use the command. When you run `/clear`, it removes everything from the active context window: the back-and-forth, the files Claude read, the dead ends. But it does **not** touch your `CLAUDE.md` file. CLAUDE.md is loaded fresh at the start of every session by design, so it survives a `/clear` and gets re-injected. Your project briefing stays. Only the conversational baggage goes.

I used to think clearing meant "lose everything and re-explain the whole project." It doesn't — not if you've put the durable stuff (architecture, conventions, the database schema) into CLAUDE.md where it belongs. That realization is what finally made me clear constantly instead of dreading it.

Now the rule I follow: **the moment I finish one logical task and move to an unrelated one, I clear.** Done wiring up auth, about to build the billing page? `/clear`. Two reasons. First, a stale context window full of auth details makes Claude worse at the billing work — it pattern-matches on the wrong things, references files that don't matter, and occasionally "helpfully" edits something I never asked it to touch. Second, every token of that dead context is a token I'm paying for and a token eating into my usage limits. A long, drifting session is expensive in both senses.

If you only adopt one command from this entire post, make it this one. Clear between tasks. Your output quality goes up and your usage goes down at the same time, which almost never happens together.

There's a sibling worth knowing here: `/compact`. Where `/clear` throws the conversation away entirely, `/compact` summarizes it into a compressed form so you keep the gist while freeing context. I reach for `/compact` mid-task when a single piece of work has gotten long but I still need its history; I reach for `/clear` when I'm genuinely done and switching gears. Different tools for different moments.

### /resume — getting back a conversation you thought was gone

`/resume` lets you reopen a past Claude Code conversation by selecting it from a list, so a session you closed — or even cleared — isn't necessarily lost.

This is the one that saved me real grief. Picture the scenario: I clear a session, switch tasks, and twenty minutes later realize I needed something from the conversation I just wiped — a decision we made, a snippet Claude generated, the reasoning behind an approach. Pre-`/resume`, that was gone and I felt it.

With `/resume`, I run the command, get a list of recent sessions, and pick the one I want back. It's distinct from rewinding within an active conversation — `/resume` reaches *across* sessions to recover a prior one entirely. Think of it as the difference between undoing your last few edits in a document versus reopening a file you closed yesterday.

The workflow I've settled into: I clear aggressively *because* I know `/resume` has my back. The fear of losing context was what made me hoard it. Once I trusted that I could pull an old session back when I genuinely needed it, clearing stopped feeling risky and started feeling like hygiene.

### /rewind — undo for code, not just conversation

`/rewind` rolls your session back to an earlier checkpoint, and it can restore your code, your conversation, or both — independently.

This is the one that changed how ambitiously I let Claude work. Claude Code automatically creates a checkpoint capturing your code's state before each edit, every time you send a prompt. So when a multi-file change goes sideways — and on a big refactor, sometimes it does — I don't panic and start manually reverting files. I open `/rewind`.

You can trigger it two ways: type `/rewind`, or press **Esc twice** when the prompt input is empty. Either opens the rewind menu showing your recent checkpoints. Then you choose what to restore:

- **Restore code and conversation** — revert both to that point, like the last few minutes never happened.
- **Restore conversation** — rewind the chat to an earlier message but keep your current code. Useful when the *code* is fine but the conversation went down a confusing path.
- **Restore code** — revert the file changes but keep talking. This is the one I use most: "that approach was wrong, undo the files, but let's keep discussing why."

There are also "summarize from here" / "summarize up to here" options that compress part of the conversation to reclaim context — a nice overlap with the `/compact` idea.

One limitation you absolutely need to know, because it has burned people: **`/rewind` only tracks edits Claude made through its file-editing tools. Bash commands are not checkpointed.** If Claude ran `rm`, `mv`, or `cp`, those changes are permanent — rewind won't bring them back. Checkpoints also persist across sessions and clean themselves up automatically after 30 days. So rewind is a safety net for *edits*, not a substitute for git. I still commit often. Rewind is for the in-between moments; git is for the ground truth.

If you want the deeper mechanics on how I structure token usage around all this clearing and compacting, I broke that down separately in my [guide to cutting Claude Code token costs with the caveman approach](https://www.mejba.me/caveman-claude-code-token-optimization) — context discipline and cost discipline are the same skill wearing two hats.

That's context handled. The next group is about quality — making Claude think before it acts.

## Planning and quality: plan mode and clarify-then-plan

The single biggest jump in my output quality didn't come from a context command. It came from forcing Claude to *plan before it writes a single line.* There are two ways I do this — one built in, one I build myself — and the second one is the centerpiece of this whole post.

### Plan mode — see the approach before any code gets written

Plan mode is a read-only state where Claude analyzes your codebase and proposes a full implementation plan *before* touching any files, so you can review and adjust the approach before execution begins.

Quick accuracy note, because this trips people up: a lot of videos and transcripts call this "the `/plan` command," and that's only half right. Plan mode has been built into Claude Code's permission cycle for a long time — you activate it by pressing **Shift+Tab twice** to land on `⏸ plan mode on` at the bottom of your terminal. As of Claude Code v2.1.0, there's *also* a literal `/plan` command that flips the same mode on, plus you can launch with `claude --permission-mode plan` or make it a project default in `.claude/settings.json`. So if you heard "/plan" and it didn't seem to do anything in an older version — that's why. The Shift+Tab path always works.

Here's what plan mode actually does. While it's active, Claude keeps full access to its *read* tools — Read, Glob, Grep, WebSearch, WebFetch — but every *write* tool is blocked: no Edit, no Write, no Bash execution. It reads your codebase, maps dependencies, reasons through the whole approach, and hands you a numbered plan. You read it. You edit it if it's wrong. Then you approve, and only then does it execute.

The moment I reach for plan mode: any task that touches more than two or three files, or anything where I'm not 100% sure Claude and I share the same mental model of *how* the change should happen. A database migration. A refactor across components. Wiring a new API into an existing flow. For those, watching Claude propose the plan catches the misunderstanding *before* it becomes thirty minutes of wrong code I then have to rewind.

The mistake I see people make is using plan mode for everything, including trivial one-file edits, where it just adds friction. Plan mode earns its keep on ambiguity and scale. For "add a console.log here," skip it.

Plan mode is powerful, but it's reactive — Claude plans based on whatever I told it. The next technique fixes the deeper problem: what happens when *my prompt itself* was incomplete.

### Custom slash commands — building a "clarify, then plan, then execute" command

This is the section I'd tell you to read twice. Custom slash commands are the highest-leverage feature in Claude Code that most people never touch, and they're genuinely simple to build.

A custom slash command is just a Markdown file. You create a file in `.claude/commands/` (project-scoped, shared with anyone on the repo) or `~/.claude/commands/` (personal, follows you across every project). The filename becomes the command name. The file's contents become the prompt that gets sent to Claude when you invoke it. That's the entire mechanism.

So if I run this:

```bash
mkdir -p .claude/commands
```

and create a file called `.claude/commands/build.md`, then typing `/build` in any session inside that project injects whatever I wrote in that file as my prompt. The command shows up in the `/` autocomplete menu automatically.

Now — *why* does this matter? Because the biggest quality killer in AI coding isn't the model. It's the gap between what I asked for and what I actually meant. I write a vague prompt, Claude makes reasonable-but-wrong assumptions to fill the gap, and I get confident, clean code that solves the wrong problem.

The fix is a command that forces Claude to close that gap *before* it writes anything. Here's the actual command I keep in my repos. Call it `.claude/commands/scope.md`:

```markdown
# Clarify the request, then plan, then build.

You are about to implement: $ARGUMENTS

Do NOT write any code yet. Work through these steps in order:

1. CLARIFY. Ask me up to 5 specific questions about anything ambiguous
   in the request — data shapes, edge cases, naming, where this fits in
   the existing architecture, what "done" looks like. If something is
   genuinely unambiguous, don't pad the list — ask only what you need.

2. PLAN. Once I answer, restate the task in one sentence, then give me a
   numbered implementation plan: the files you'll create or change, the
   order you'll do them in, and any decision you're making that I should
   veto now rather than after the code exists.

3. WAIT. Stop and let me approve or correct the plan before you touch
   a single file.

Only after I approve do you start implementing.
```

The `$ARGUMENTS` placeholder is the real trick — whatever I type after the command name gets dropped in there. So I run `/scope add team invites to the settings page` and Claude takes "add team invites to the settings page" as the thing to clarify and plan around.

What does this buy me in practice? The clarifying questions are where the magic lives. Half the time, Claude's questions surface something I hadn't decided yet — "should invited users get a role immediately or stay pending until they accept?" — and answering it *out loud, before any code exists* means I never get the wrong implementation in the first place. The plan step then lets me veto an approach cheaply, while it's still words on a screen instead of files on disk.

I can't give you a clean percentage on how much this improves output — and I want to be honest about that, because I've seen the claim floating around that custom clarify-then-plan commands boost quality "by up to 43% based on internal reporting," and I could never find a real basis for that number. So I won't pretend. What I *can* tell you from daily use is qualitative and consistent: forcing the clarify-then-plan-then-execute sequence meaningfully cuts the number of "that's not what I meant" rewrites I do. The rework I save is the whole return on investment. The number being unverifiable doesn't make the technique less real — it just means I'm not going to dress it up in a stat I can't stand behind.

If you want to go deeper on building these, I wrote a full breakdown of [the /advisor custom slash command and the meta-cognitive layer it adds](https://www.mejba.me/claude-code-advisor-slash-command) — same building blocks, different purpose. And the reason this whole approach works ties directly into [why context engineering is becoming a core, future-proof skill](https://www.mejba.me/ai-skills-future-proof-career-2026): the developers who win with these tools aren't the ones who type fastest, they're the ones who structure context and intent best.

If you'd rather have someone build out a full custom-command and agent setup tuned to your stack rather than assembling it yourself, that's the kind of work I take on directly — you can see what I've built at [my Fiverr profile](https://www.fiverr.com/s/EgxYmWD).

A quick word on a command people *ask* about that doesn't exist the way they think.

### About "/goal" — a pattern you build, not a button you press

I get asked about a "`/goal`" command that supposedly lets you set a task plus a definition of "done" and have Claude keep working until a second agent verifies it's complete. I dug into this carefully, and here's the honest picture: there is **no built-in `/goal` slash command in Claude Code** that does this. I couldn't confirm it in the current documentation, and you shouldn't go looking for it as a stock feature.

What's real is the *pattern* behind it — and it's a good one. You can absolutely build a verification-driven workflow yourself: a custom command that hands Claude a task plus explicit "definition of done" criteria, paired with a subagent whose only job is to check the work against those criteria before declaring it finished. That's a buildable thing, not a built-in thing. (OpenAI's Codex, separately, ships a literal `/goal` command for autonomous runs — I tested it and wrote up [how Codex's /goal command actually behaves](https://www.mejba.me/codex-goal-command-autonomous-coding). Don't confuse the two; they're different tools.)

The takeaway: if a tutorial promises you a magic Claude Code `/goal` button, be skeptical. The capability is yours to assemble with custom commands and subagents — which is more flexible anyway.

Now for the small stuff that quietly improves every session.

## Environment and UX: /statusline and learning the tool

These don't change *what* Claude does. They change how much I can see while it does it — and how fast I discover features I didn't know existed.

### /statusline — a one-time setup that pays off every session

`/statusline` configures the status line at the bottom of your terminal, letting you display things like the active model, context usage, and cost — so you can see at a glance what's happening without guessing.

Again, an accuracy note: people write it as "/status line" (two words). The real command is `/statusline`, one word, in the autocomplete. You run it once, configure what you want shown, and then it's just *there* every session.

Why I bother: context usage and cost visibility. When my status line shows me how full the context window is getting, I know it's time to `/clear` or `/compact` *before* Claude starts degrading — instead of noticing the degradation after the fact. Seeing the active model reminds me whether I'm on the right tier for the task. It's a small, one-time configuration that turns a bunch of invisible state into something glanceable. For the deeper "see and steer everything Claude does" philosophy, I went into the full visual-intelligence layer in [my breakdown of the agentic OS visual layer](https://www.mejba.me/agentic-os-visual-intelligence-layer-claude-code).

### /powerup and /radio — the two I'll mention but won't oversell

Two more real commands, included for completeness and honesty.

`/powerup` (introduced in Claude Code v2.1.90) is an interactive, in-terminal tutorial — animated lessons that each teach one thing Claude Code can do that most people miss. It's genuinely useful for discovering features, and I gave it its own full first look in [my piece on what /powerup actually teaches](https://www.mejba.me/claude-code-powerup-command), so I won't repeat that here.

`/radio` is real too, and it's exactly what it sounds like: it opens Claude FM, a lo-fi radio station Anthropic runs, in your browser. Is it a productivity command? No. Do I use it while doing the boring parts of a build? Sometimes. I'm including it because I told you I'd give you the accurate picture, and the accurate picture is that it exists and it's a fun little nothing. Treat it accordingly.

There's also `/btw` — a lighter-weight way to ask a side question or add context without it bloating your main conversation thread, which keeps long sessions leaner. It's a real mechanism worth knowing if you find yourself wanting to clarify something mid-task without derailing the flow.

That's the honest roster. Now let me show you what this looks like strung together on real work.

## What a real session looks like with these strung together

Commands in a list are abstract. Here's how they actually chain on a normal build day for me.

I open Claude Code in a project. CLAUDE.md loads automatically, so Claude already knows the stack and conventions — I don't re-explain anything. I want to add a feature, so I type `/scope add CSV export to the reports page`. My custom command kicks in: Claude asks me four questions (which columns, what date format, server-side or client-side generation, what the filename should be), I answer, it hands me a five-step plan, I tweak step three, I approve.

It builds. Halfway through, the export logic gets tangled with the existing pagination and the output isn't right. I don't manually unpick it — I open `/rewind`, restore the *code* to before the tangle while keeping the conversation, and say "that approach conflicted with pagination, let's generate the full dataset separately." Clean recovery, maybe ninety seconds lost.

Feature done. I `/clear`. Fresh window, project memory intact, zero stale context bleeding into the next task. My status line — configured once with `/statusline` weeks ago — shows I'm barely using any context, which is exactly how a fresh task should start.

Three tasks later I realize I need a decision from a session I cleared earlier. `/resume`, pick it from the list, grab what I needed, get back to work.

Five commands. None of them wrote code. All of them shaped the conditions under which the code got written well. That's the entire point — the slash commands aren't the work, they're the workspace.

## The honest part: where I was wrong, and what's worth your time

Let me be straight about the corrections, because watching a practitioner walk back their own assumptions is more useful than a clean list that pretends everything's confirmed.

I spent too long treating `/clear` as a loss instead of hygiene — that was a genuine workflow tax I paid for months. "/plan" as a standalone command is newer (v2.1.0) than the Shift+Tab plan mode it sits on top of, so if you're on an older build, reach for the keyboard shortcut. "/statusline" is one word, not two. And the "`/goal` command" people describe as built-in to Claude Code isn't — it's a pattern you assemble, and conflating it with Codex's actual `/goal` command muddies the water. The "43% quality boost" figure attached to custom commands has no basis I could find, so I dropped it and told you why rather than laundering an unverifiable number into a confident claim.

What's genuinely worth your time, in priority order: **`/clear` between tasks** (biggest, easiest win), **a custom clarify-then-plan command** (biggest quality win), **`/rewind` to recover from bad edits** (biggest stress reduction), and **plan mode for anything ambiguous or multi-file** (biggest "wrong code" preventer). The rest — `/resume`, `/statusline`, `/compact`, `/btw` — are real, useful, and worth knowing, but they're the supporting cast.

If your Claude Code sessions currently feel like long, drifting conversations that get worse the longer they run, the fix isn't a better model. It's a slash, four letters, and the discipline to clear what you don't need. Go build one custom command this week — the `/scope` example above, copied straight into `.claude/commands/scope.md` — and use it on your next real task. The first time Claude's clarifying questions catch a wrong assumption before it becomes wrong code, you'll understand why I type that slash before my brain finishes the thought.

## Frequently Asked Questions

### What's the difference between /clear and /rewind in Claude Code?
`/clear` wipes your current conversation history to start a fresh task while keeping your CLAUDE.md project memory, whereas `/rewind` rolls back to an earlier checkpoint within an active session and can restore your code, conversation, or both. Use `/clear` when switching to an unrelated task; use `/rewind` when an edit went wrong and you need to undo it. See the context management section above for the full breakdown.

### Does /clear delete my CLAUDE.md project memory?
No — `/clear` only removes the active conversation from the context window; your CLAUDE.md file is reloaded fresh at the start of every session, so it survives clearing. That's exactly why you can clear aggressively between tasks without losing your project's durable context, as long as the important stuff lives in CLAUDE.md.

### How do I create a custom slash command in Claude Code?
Create a Markdown file in `.claude/commands/` (project-scoped) or `~/.claude/commands/` (personal), and the filename becomes the command name while the file's contents become the prompt sent to Claude. Use the `$ARGUMENTS` placeholder to pass input after the command name. The full `/scope` clarify-then-plan example is in the planning section above.

### Is there a /plan command or is it plan mode in Claude Code?
Both — plan mode has long been built into the permission cycle (press Shift+Tab twice), and as of Claude Code v2.1.0 there's also a literal `/plan` command that activates the same read-only planning state. If `/plan` does nothing on your setup, you're likely on an older version; the Shift+Tab shortcut always works.

### Is there a built-in /goal command in Claude Code?
No, there is no stock `/goal` command in Claude Code that runs a task to a definition of "done" with automatic verification. That capability is a pattern you build yourself using a custom command plus a verification subagent. OpenAI's Codex ships a separate `/goal` command, but that's a different tool entirely.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
