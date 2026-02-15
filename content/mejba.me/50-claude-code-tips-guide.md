**BRAND:** mejba.me
**TITLE:** 50 Claude Code Tips I Wish I Knew From Day One
**SLUG:** 50-claude-code-tips-guide
**TAGS:** AI Development, Claude Code, Developer Productivity, Automation, Guide

---

I burned through $400 in API tokens during my first month with Claude Code. Not because I was building anything complex — because I had no idea how to manage context windows, when to clear them, or that a bloated `CLAUDE.md` file was silently eating 30% of my available tokens on every single request.

Six months later, my token usage dropped by roughly 60% while my output tripled. Same tool. Same subscription. Completely different approach.

The gap between a developer who's "using Claude Code" and one who's *actually productive* with it comes down to maybe fifty specific habits and techniques. Most of them aren't documented anywhere obvious. You pick them up through painful trial and error, or you learn them from someone who already burned through the painful part.

I've spent the last six months using Claude Code daily — building agent systems, shipping client projects, automating workflows across four websites. Along the way, I collected every trick, shortcut, and workflow pattern that made a measurable difference. What follows is the distilled version — fifty tips organized by how I actually think about them in practice, not alphabetically or by some arbitrary category.

Some of these will save you minutes. A few will save you hours. And one of them — the context engineering section — might fundamentally change how you approach AI-assisted development. But we'll get there.

## The Foundation Most People Skip

Here's a pattern I see constantly: someone installs Claude Code, opens a project, and immediately starts prompting. No setup. No initialization. Just raw commands fired into the void. Then they wonder why the AI doesn't understand their project structure, suggests wrong file paths, or generates code that conflicts with their existing patterns.

The first fifteen minutes with Claude Code on any new project should be setup. Not coding. Setup.

### Always Run From Root

This sounds so basic it's almost insulting to mention, but I see it broken all the time. Always launch Claude Code from your project's root directory. When you start a session, Claude Code packages your project context — file structure, dependencies, configuration files — and sends it along with your prompts. If you launch from a subdirectory, you're giving the AI a partial map and expecting it to navigate the full territory.

I learned this the hard way on a monorepo where I kept launching from `/packages/api` instead of the root. Claude Code couldn't see the shared types package, kept suggesting duplicate interfaces, and generated import paths that were wrong every single time. Moved to root, problem vanished.

### The `/init` Command Is Not Optional

Running `/init` on a new project does something most people don't realize — it performs a full structural analysis of your codebase and generates a `CLAUDE.md` file tailored to what it finds. Framework detection, test runner identification, build tool configuration, directory conventions. All of it captured automatically.

I used to skip this and write my own `CLAUDE.md` from scratch. That's like refusing GPS directions because you "know the area." Sure, you might get there eventually, but you'll miss turns. The auto-generated file catches project details I would have forgotten to mention — like the fact that my Laravel project uses Pest instead of PHPUnit, or that the TypeScript config extends a shared base.

Run `/init`. Review what it generates. Then customize from there. That order matters.

### Your `CLAUDE.md` Is Either Your Superpower or Your Bottleneck

This is the single most important file in your Claude Code workflow, and almost everyone gets it wrong in the same way: they make it too long.

The `CLAUDE.md` file operates as persistent context — it gets loaded into every single conversation. Every token in that file gets counted against your context window on every request. A 500-line `CLAUDE.md` with detailed architectural documentation, coding standards, example patterns, and historical decisions sounds thorough. It's actually wasteful. You're burning context budget before you even ask your first question.

Keep it under 300 lines. Ideally closer to 200.

What belongs in `CLAUDE.md`:
- Project architecture at a glance (framework, language, key directories)
- Build and test commands (so the AI can validate its own work)
- Hard rules — "never modify migration files directly," "always use the repository pattern for database access," "tests must pass before suggesting the task is complete"
- Domain-specific context that would be impossible to infer from code alone

What doesn't belong:
- Code examples (the AI can read your actual codebase)
- Lengthy explanations of why decisions were made
- Documentation that duplicates your README
- Anything that hasn't been relevant in the last two weeks

Think of `CLAUDE.md` as a lint configuration for AI behavior. Short rules. Clear constraints. No essays. I update mine roughly once a week — and the update process itself is automated. I just tell Claude Code "add a rule that all new API endpoints must include rate limiting middleware" and it edits the file. No manual editing needed.

One more thing about `CLAUDE.md` that took me embarrassingly long to figure out — it supports hierarchy. You can have a root-level file for project-wide rules and directory-level files for subsystem-specific context. Your `/api` directory can have its own `CLAUDE.md` with API-specific conventions without bloating the root file. Use this. It's how you scale context without scaling token cost.

## Keyboard Shortcuts That Changed My Speed

I spent my first two months clicking through menus and typing full commands. Then I watched someone who was genuinely fast with Claude Code, and the difference was almost entirely keyboard shortcuts. Three of them matter more than the rest combined.

### Shift + Tab — The Mode Toggle

This single shortcut toggles between Plan Mode and Edit Mode. If you're not using Plan Mode, you're making a category of mistakes that's completely avoidable.

Plan Mode tells Claude Code to analyze and propose changes without executing anything. Edit Mode tells it to actually make changes. The workflow I've settled on: start every non-trivial task in Plan Mode. Let the AI explain what it intends to do. Verify the approach makes sense. Then switch to Edit Mode and let it execute.

I used to skip straight to Edit Mode and then spend twenty minutes undoing changes that went in the wrong direction. Plan Mode costs a few extra tokens upfront but saves dramatically on wasted cycles. For simple stuff — renaming a variable, fixing a typo — sure, skip it. For anything involving more than two files? Plan first.

### Escape — The Interrupt Button

When Claude Code is mid-generation and you can already see it heading the wrong direction, hit Escape. It stops the AI immediately. You don't have to sit through a 2,000-token response you're going to throw away.

I use this probably ten times a day. The AI starts suggesting a React class component when my project uses functional components exclusively? Escape. It's generating a SQL query when my app uses an ORM? Escape. Fast interruption means fast course correction.

Double-tap Escape does something different — it clears your current input or rewinds to a previous context state. Useful when you've typed a long prompt and realize you need to approach the question differently.

### The Slash Commands You Actually Need

There are a lot of slash commands. Most of them you'll use rarely. Here are the ones I use daily, in order of frequency:

**`/clear`** — Nukes the current context. Fresh start. I do this between unrelated tasks within the same session. Moving from debugging an API endpoint to writing a new component? Clear first. Stale context from the previous task will confuse the AI on the new one.

**`/context`** — Shows you exactly what's in your current context window and how many tokens each piece is consuming. This is your diagnostic tool. When responses start getting weird or the AI seems to "forget" your instructions, run `/context`. Nine times out of ten, you'll find a massive MCP response or a large file read that's crowding out your `CLAUDE.md` rules.

**`/compact`** — Summarizes and compresses your current context without losing the important bits. Use this when you're deep into a task and don't want to `/clear`, but the context is getting heavy. It's a middle ground — keep the thread alive but shed the weight.

**`/models`** — Lists available models and lets you switch. Default to Opus for complex architectural work and reasoning. Sonnet for straightforward implementation tasks. Haiku for quick questions and simple edits. Matching the model to the task complexity saves real money over time.

**`/resume`** — Recovers a lost session. Accidentally closed your terminal? Machine crashed? `/resume` brings back your last session state. I've needed this exactly three times, and each time it saved me from re-establishing thirty minutes of context.

**`/mcp`** — Manages your Model Context Protocol plugins. More on this in a minute, but knowing how to check which MCPs are active and how many tokens they're consuming is essential for context hygiene.

## Context Engineering — The Skill Nobody Teaches

Here's the section I mentioned at the top. The one that might change how you think about AI-assisted development entirely.

Most developers treat Claude Code like a really smart autocomplete. Type a prompt, get a response, type another prompt. Linear. Sequential. Wasteful.

The developers I've seen get genuinely impressive results treat it more like managing a team member's working memory. They're constantly thinking about what the AI knows right now, what it needs to know, and what's cluttering its attention.

### Fresh Context Beats Bloated Context Every Time

This is counterintuitive. You'd think that the more context the AI has — more conversation history, more file reads, more background — the better its responses would be. The opposite is true past a certain point.

When your context window fills up, the AI starts making trade-offs about what to prioritize. Your carefully crafted `CLAUDE.md` rules? They get diluted by the weight of everything else in context. That important architectural constraint you mentioned thirty messages ago? It's competing with fifty subsequent messages for attention.

I run `/context` obsessively. If my token usage is above 60% and I'm starting a new subtask, I clear and start fresh. The two minutes of re-establishing context is cheaper than the degraded quality of working in a bloated window.

### Validation Loops Are Everything

This is the single most powerful technique I've adopted. In your `CLAUDE.md`, define validation commands — build, test, lint, type-check — and instruct Claude Code to run them after making changes.

Mine looks something like this: "After modifying any TypeScript file, run `npm run typecheck`. After modifying any test file, run the relevant test suite. If validation fails, fix the issue before reporting the task as complete."

What this creates is a self-correcting loop. The AI writes code, validates it, finds the error, fixes it, validates again. Without this, you get code that looks right but fails on the first run. With it, you get code that's been through at least one cycle of automated quality checking before you even look at it.

I've measured the difference. Without validation loops, roughly 40% of Claude Code's generated code needed manual fixes before it would run. With validation loops, that dropped to under 10%. Same model, same prompts, dramatically different outcomes.

### The Second Brain Approach

My `CLAUDE.md` isn't just rules and commands — it's a curated knowledge base that makes the AI smarter about my specific projects over time.

When I discover a pattern that works, I add it as a rule. When the AI makes a recurring mistake, I add a constraint preventing it. When a new team convention is established, it goes into the file.

The effect compounds. After six months, my `CLAUDE.md` files encode hundreds of micro-decisions about how code should be written, structured, and validated for each project. A new developer joining the project gets the benefit of all that accumulated wisdom just by having Claude Code read the file. It's like pair programming with someone who has perfect memory of every architectural decision ever made — because that's literally what it is.

But only if you keep the file lean. I audit mine monthly and remove any rule that hasn't been relevant in the past four weeks. Accumulation without curation leads to the bloated context problem I described earlier.

## Parallel Development — The Multiplier

This is where Claude Code starts feeling less like a tool and more like having a small development team.

### Running Multiple Instances

Nothing stops you from opening multiple terminal windows and running Claude Code in each one. I routinely run two or three instances simultaneously — one building a feature, one writing tests, one refactoring a related module.

The mental model is more real-time strategy game than traditional coding. You're dispatching tasks, monitoring progress, switching between windows to provide guidance when an instance gets stuck. It's a different kind of cognitive load than writing code yourself, but once you develop the pattern, the throughput increase is substantial.

My setup: iTerm2 with split panes. Three Claude Code instances max — more than that and context-switching overhead eats the productivity gains. Each instance gets a clear, scoped task. "Build the user authentication endpoint." "Write integration tests for the payment module." "Refactor the notification service to use the new event bus."

### Git Worktrees Make This Safe

Running multiple Claude Code instances on the same codebase is asking for merge conflicts. Git worktrees solve this cleanly — each worktree is an independent checkout of your repo tied to a different branch. Instance one works in `worktree-auth`, instance two in `worktree-tests`, instance three in `worktree-refactor`. No file conflicts. No stepping on each other's changes.

```bash
git worktree add ../project-auth feature/auth
git worktree add ../project-tests feature/tests
git worktree add ../project-refactor refactor/notifications
```

When the tasks are complete, merge the branches normally. I've been using this workflow for three months and it's the closest thing to 3x productivity I've found that isn't just hype.

## Skills, MCPs, and Sub-Agents — The Composability Layer

Claude Code's power isn't just in the base tool. It's in the extension ecosystem that lets you build custom workflows on top of it.

### Skills — Reusable Workflows

A skill is a saved workflow you can trigger with a slash command. I have one that fetches the latest Hacker News front page, summarizes the top ten posts, and saves the summary to a markdown file. Another monitors a GitHub repo for new issues and generates response drafts. A third runs my full deployment checklist — build, test, type-check, version bump, changelog update — in sequence.

Creating a skill is conversational. Tell Claude Code what you want the workflow to do, ask it to save it as a skill, give it a trigger name. Next time you need it, just type the slash command. The skills themselves are stored as markdown files, so you can version control them, share them with your team, or port them between machines.

The mental shift: stop thinking of Claude Code as a tool you prompt and start thinking of it as a platform you configure. Every repetitive task is a skill waiting to be created.

### MCPs — The Plugin System

Model Context Protocols extend Claude Code's capabilities by connecting it to external services and tools. GitHub MCP gives it direct access to PRs and issues. Database MCPs let it query your data layer. Browser MCPs enable web interaction.

Here's the warning nobody gives you upfront: **MCPs consume context tokens**. Every installed MCP adds its interface definition to your context window. Five MCPs with verbose schemas can eat 15-20% of your available context before you've even started working.

Install only what you need for your current workflow. Use `/mcp` to audit what's active. Remove MCPs between projects if they're not relevant. Context hygiene applies to plugins too.

### Sub-Agents — Parallel Task Runners

Sub-agents are lightweight Claude Code instances spawned for specific atomic tasks. They don't share context with your main session — that's both their strength and their limitation.

Good for: generating a single file, formatting data, fetching and summarizing external content, running isolated calculations.

Bad for: anything that requires understanding your full project context, running tests that depend on multiple interconnected modules, making changes that need to be consistent across files.

I use sub-agents for side tasks that would clutter my main context. "Summarize this API documentation" or "Generate TypeScript types from this JSON schema" are perfect sub-agent tasks. "Refactor the authentication module" is not — that needs full project context to avoid breaking things.

## Advanced Techniques for the Obsessed

If you've made it this far, you're either already productive with Claude Code and looking for the next level, or you're building a mental model before diving in. Either way, these are the techniques that separate casual users from power users.

### Browser Automation With `/chrome`

The `/chrome` command opens a browser instance that Claude Code can control directly. Navigate pages, fill forms, scrape content, take screenshots, interact with web applications — all through natural language commands.

I use this for testing web applications when an API doesn't exist. "Go to the staging site, log in with the test account, navigate to the dashboard, and tell me if the chart renders correctly." The AI controls the browser, interprets the visual output, and reports back. It's not a replacement for Playwright test suites, but for ad-hoc verification and exploration, it's remarkably useful.

### Hooks for Automated Safety

Pre-execution and post-execution hooks work like Git hooks but for Claude Code actions. A pre-hook can prevent destructive commands — "never run `rm -rf` without explicit confirmation." A post-hook can auto-format generated code or run linting after every file modification.

My hook setup:
- Pre-hook: blocks any command that modifies production environment variables
- Post-hook: runs Prettier on any modified `.ts` or `.tsx` file
- Post-hook: runs ESLint with auto-fix on changed files

The automation ensures consistent code quality without me having to remember to format and lint every time. Claude Code creates these hooks conversationally — describe what you want, and it generates the configuration.

### Dangerously Skip Permissions — With Caution

There's a flag that bypasses all permission prompts — no confirmations for file writes, deletions, or command execution. In a disposable development environment (Docker container, temporary branch you're going to delete anyway), this removes friction and lets Claude Code operate at full speed.

In any environment where data loss matters? Don't touch it. I use it exclusively inside Docker containers spun up for experimental work. The speed gain is real — maybe 30% faster for complex multi-file tasks — but the risk of an AI deciding to "clean up" your project structure is also real.

### Notifications When Work Completes

When running long tasks across multiple instances, configure Claude Code to notify you on completion. A simple terminal notification or text-to-speech summary — "Authentication module complete, all tests passing" — means you don't have to visually monitor each window.

I set this up using a post-completion hook that triggers a macOS notification. Small quality-of-life improvement, but when you're juggling three instances, knowing immediately when one finishes lets you dispatch the next task without delay.

## The Composability Framework

Step back and look at the full picture: slash commands, skills, MCPs, sub-agents, hooks, and plugins. These aren't separate features — they're composable building blocks.

A skill can invoke slash commands. A hook can trigger after a skill completes. An MCP can feed data into a skill that dispatches sub-agents. A plugin bundles all of these into a shareable, installable package.

The developers getting the most out of Claude Code aren't writing better prompts. They're building better systems around the tool — automated pipelines that handle the repetitive work, validation loops that catch errors, notification systems that manage attention, and shared configurations that scale best practices across teams.

Anthropic maintains a plugin repository where the community shares these configurations. Before building a custom workflow from scratch, check if someone's already packaged what you need. The ecosystem is growing fast, and most plugins are well-documented with clear installation instructions.

## What Six Months Taught Me That Documentation Can't

The tips above are practical — keyboard shortcuts, commands, configuration patterns. But the deeper lesson from six months of daily Claude Code usage is about workflow philosophy.

**Start every task by asking what the AI needs to know, not what you want it to do.** The quality of output is directly proportional to the quality of context. Spend thirty seconds framing the problem, specifying constraints, and pointing to relevant files. That investment returns tenfold in the response quality.

**Treat context like RAM — it's finite and precious.** Don't accumulate. Don't hoard conversation history hoping it'll be useful later. Clear aggressively. Rebuild context cheaply. A fresh, focused session will outperform a stale, bloated one every time.

**Automate the meta-work.** Updating `CLAUDE.md`, installing MCPs, configuring hooks, creating skills — all of this can be done conversationally through Claude Code itself. The tool that builds your workflows should also build its own configuration. That's not recursive nonsense — it's how you avoid spending your time on infrastructure instead of implementation.

**Plan before you execute. Always.** The five minutes you spend in Plan Mode verifying approach and assumptions will save you thirty minutes of unwinding incorrect changes in Edit Mode. This isn't a Claude Code tip — it's a development discipline that the tool's mode system happens to enforce beautifully.

I started this piece by telling you about the $400 I wasted in my first month. That money bought me an education in context engineering, workflow design, and the difference between using a tool and building a system around one. If even five of these fifty tips save you from similar expensive lessons, then this post did its job.

The real question isn't whether Claude Code is good enough to change how you work. It is — I've tested it across enough real projects to say that confidently. The question is whether you're willing to invest the setup time to work *with* the tool instead of just *at* it. The developers who build the right `CLAUDE.md`, configure the right validation loops, and develop the right parallel workflow patterns don't just code faster. They code differently. And once you've experienced that difference, going back feels like switching from a sports car to a bicycle.

What would your development workflow look like if you spent one afternoon — just one — implementing every tip in this post that applies to your setup?

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
