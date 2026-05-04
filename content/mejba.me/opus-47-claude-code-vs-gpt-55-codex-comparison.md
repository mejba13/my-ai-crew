**BRAND:** mejba.me
**TITLE:** Opus 4.7 vs GPT-5.5 Codex: Which Coder Won My Week?
**META TITLE:** Opus 4.7 vs GPT-5.5 Codex: Real Coding Comparison
**SLUG:** opus-47-claude-code-vs-gpt-55-codex-comparison
**PRIMARY KEYWORD:** Opus 4.7 vs GPT-5.5 Codex
**META DESCRIPTION:** I ran Opus 4.7 (Claude Code) vs GPT-5.5 (Codex) on the same builds for a week. Token counts, planning times, debugging behavior — here's what actually held up.
**TAGS:** Claude Code, Codex, AI Coding, Opus 4.7, GPT-5.5

---

I had two terminal windows open for six straight days. Left side: Claude Code running Opus 4.7. Right side: Codex CLI running GPT-5.5. Same prompts. Same repos. Same coffee. The plan was simple — stop reading benchmarks, stop watching YouTube reviews, and just *use* both of them on the kind of work I actually ship. A small SaaS dashboard. A scraper that kept breaking. A Stripe webhook handler that needed a rewrite. A landing page refactor.

By Wednesday afternoon I had to admit something I didn't expect to admit. The model I'd been defending in every Discord conversation for the last six months wasn't winning every category. Not even close on a few of them. But it was still winning the categories I cared about most — and that distinction turned out to be the entire story.

This isn't a benchmark post. The benchmarks are everywhere. [DataCamp's frontier comparison](https://www.datacamp.com/blog/gpt-5-5-vs-claude-opus-4-7) and [MindStudio's coding deep-dive](https://www.mindstudio.ai/blog/gpt-55-vs-claude-opus-47-coding-comparison) already cover SWE-bench Pro, Terminal-Bench 2.0, and the rest of the leaderboard theater. I'm here to tell you what it felt like to live inside both tools for a full work week, what each one cost me in tokens, where each one quietly saved me, and where one of them made me close the terminal in frustration.

If you've been on the fence about whether to keep your Anthropic subscription or move your daily driver to Codex, this is the breakdown I wish someone had handed me on Monday morning.

## Why this comparison hits different in May 2026

Six months ago, asking "Claude Code or Codex?" was a non-question. Claude was the obvious answer. Codex existed but felt like a science project — slow, brittle, occasionally brilliant in a way that made you suspect it was fluking it.

Then OpenAI rewrote the entire CLI in Rust. According to [the codex-rs architecture writeup](https://codex.danielvaughan.com/2026/03/28/codex-rs-rust-rewrite-architecture/), the project has shipped over 640 tagged releases since launch and accumulated 5,000+ commits from 400+ contributors. That's roughly one release per day. They didn't just port it — they fixed the long-session compaction bug where summaries were recursively summarizing previous summaries, and they made the install zero-dependency.

Meanwhile Anthropic shipped Opus 4.7 and reclaimed the SWE-bench crown — [64.3% on SWE-bench Pro and 87.6% on SWE-bench Verified](https://www.mindstudio.ai/blog/gpt-55-vs-claude-opus-47-coding-comparison). On paper, Opus is still the king. In my terminal, the story got more interesting.

The version I tested was Claude Code 2.1.0 — the same release that broke a chunk of the rendering pipeline and made a lot of long-time users start complaining loudly about flickering output and terminal lockups. I'll get to that in a minute. It matters.

Before we go deeper, here's the open loop I want you to hold onto: there's one specific category in this comparison where Codex doesn't just match Claude — it makes Claude look like it's working with one hand tied behind its back. I'll mark it when we get there.

## The setup — what I actually tested

Nine categories, same builds in both tools. No cherry-picking. If I tested a feature in one CLI, I tested it in the other. If a project failed halfway through with one model, I let it fail and recorded what happened.

The categories:

1. Usability and stability
2. Cost and token efficiency
3. Planning depth and speed
4. Implementation behavior on greenfield builds
5. Debugging and self-correction
6. Memory and context handling
7. Code review quality
8. Ecosystem and tooling
9. Multi-agent and sub-agent workflows

The test projects were a project management dashboard with Kanban + calendar views (the kind of thing both vendors love to demo), a Node.js scraper that pulls product data from three e-commerce sites, and a refactor of an existing Laravel codebase I've been maintaining for a client. The dashboard was greenfield — both tools started from an empty directory. The scraper was a real bug I'd been putting off. The refactor was the kind of work where a model has to actually understand existing patterns before touching anything.

I'm going to walk through each category, then give you the two summary tables at the end. Skip ahead if you want — but the categories are ordered roughly by how surprised I was by the result.

## Usability — the category where I changed my mind

I genuinely thought I was going to give Claude Code the win here. It's the tool I know cold. I have hooks configured. I have aliases. I have muscle memory.

Then I spent three days inside Claude Code 2.1.0 and another three inside Codex CLI v0.56, and I had to update my priors.

Claude Code 2.1.0 had visible problems. The terminal would render glitchy artifacts during long streaming responses. Twice in the same session, the CLI hung in a way that required a force-quit. The permission system has tightened to the point where I was approving the same shell command four times in one task because each invocation triggered a fresh prompt — and there's no way to globally bypass it the way you used to be able to. I get the security reasoning. It still slowed me down.

Codex CLI was, frankly, boring in the best way. The Rust rewrite shows. I ran it through a six-hour session on Tuesday — refactoring the Laravel codebase, with maybe forty tool calls — and it never crashed, never glitched, never hung. The YOLO mode (Codex's permission bypass) is the kind of thing that's dangerous in the wrong hands and a productivity multiplier in the right ones. I used it for the scraper rebuild and didn't have to babysit a single confirmation prompt.

Personality customization was another small surprise. Codex has a settings-based way to dial down sycophancy — you tweak a config and the model genuinely stops saying "great question!" before every response. With Claude, I've been pasting the same anti-sycophancy block into CLAUDE.md for months and the model still occasionally tells me my approach is "really clever" when it isn't.

If you're new to either tool and trying to figure out where to start, I covered the broader Claude Code 2.1.0 update in [Claude Code 2.1.x: what changed and what broke](content/mejba.me/claude-code-2-1-101-update.md). Worth reading before you upgrade if you haven't already.

## Cost and tokens — the gap is wider than you think

I built the same project management app in both tools. Identical prompt: a Kanban board, a calendar view, a dashboard, and a user settings page.

Claude burned **173,000 tokens** to ship the working app. Codex shipped roughly the same app for **82,000 tokens**.

That's not a small gap. That's Codex doing the same job for less than half the input. And this matches what [MindStudio's analysis](https://www.mindstudio.ai/blog/gpt-55-vs-claude-opus-47-coding-comparison) describes as a 72% reduction in output tokens on equivalent tasks — most of the cost difference between the two tools isn't per-token pricing, it's that Codex generates dramatically less.

Why does Claude burn more? A few reasons I noticed firsthand. It re-reads files more often. It writes longer plans before implementing. It tends to over-explain its reasoning in chat output that ultimately doesn't ship. And on retries, it sometimes regenerates entire files when a patch would have worked.

The pricing reality also bites harder than the per-token rates suggest. According to [Anthropic's own usage docs](https://support.claude.com/en/articles/11145838-using-claude-code-with-your-pro-or-max-plan), Claude Code on Pro and Max plans shares its usage budget with claude.ai conversations — meaning if you've been using Claude on the web all morning, you're already eating into your coding budget before you open the CLI. There are five-hour session resets, weekly token caps, and a separate Opus-only weekly cap on top of the rolling window.

Codex has a free tier. It's limited, but it exists. Claude has nothing equivalent — even on Max 20x, you can hit the cap and the only way through is buying overflow at standard API rates.

I wrote a longer breakdown of how Claude's token economy actually works in [Claude token limits and context hygiene](content/mejba.me/claude-token-limits-context-hygiene.md) — if you're paying for Pro or Max and not actively managing your context window, that post will probably save you real money this month.

## Planning depth versus planning speed

This was the most philosophically interesting split.

I gave both models the same brief: "Build a project management web app with Kanban, calendar, dashboard, and settings. Plan first, then implement."

Claude spent **24 minutes planning**. The plan was deep. It thought about the user experience first — how the Kanban columns should feel, where the calendar view's empty state lives, what the settings hierarchy should look like. It mapped out component structure, design tokens, accessibility requirements. It asked itself questions about edge cases. By the time it started implementing, it had a blueprint that would have impressed a senior engineer.

Codex spent **8 minutes planning**. The plan was simpler. It identified the core data model, sketched the API endpoints, listed the four routes, and started writing code. No UX agonizing. No accessibility audit. Just structure.

Both apps shipped. Claude's looked better — visibly better, no contest. Better spacing, better empty states, better color hierarchy on the Kanban cards. Codex's app worked, but it looked like a Bootstrap demo from 2019.

Here's the thing though. For the *scraper* — backend code with no UI — Codex's faster planning style was the right answer. There was nothing to agonize over. A 24-minute UX-deep plan would have been pure waste. Claude's depth is a feature when UX matters and a tax when it doesn't.

This is also where I noticed the greenfield behavior diverge. On a brand new project with no existing patterns, Codex tends to skip the explicit planning phase entirely and start implementing. It's faster but riskier — you sometimes have to redirect it after it's already 200 lines deep into the wrong abstraction. Claude refuses to start without a plan, which means slower starts but fewer wasted tokens on bad first drafts.

## Implementation — the fallback story

This is where I found the open loop I promised you. The category where Codex makes Claude look like it's working with one hand tied behind its back.

Fallback handling.

I gave both tools the scraper rebuild. The script needed three API keys — one of which I deliberately left out of the `.env` file to see what would happen.

Claude noticed the missing key on its first run, stopped, and asked me to provide it before continuing. Reasonable behavior. But this was a multi-hour autonomous task — I wasn't sitting at the keyboard. By the time I came back, the run had been blocked for forty minutes waiting for input.

Codex noticed the missing key, wrote a fallback that gracefully degraded that data source to a "skip and log" path, finished the rest of the scraper, and left a clear note in the commit message about what was disabled and why. When I came back, the script was running. I added the key, it picked up the third source, done.

That's not a small difference. That's the difference between a tool you can leave running overnight and a tool you have to babysit.

I'd seen the pattern before — Codex implementing graceful degradation by default when Claude wants explicit confirmation — but the scraper test made it concrete. If your work involves long autonomous runs, this single behavioral gap is going to matter more than any benchmark number.

For the build-everything-functional-fast workflow this enables, I went deeper in [Building a stock app with ChatGPT Codex](content/mejba.me/build-stock-app-chatgpt-codex.md) — that post showed me the pattern, this week confirmed it.

## Debugging — autonomy versus collaboration

When something broke, the two tools approached it completely differently.

Codex has a built-in agent browser skill. When the dashboard failed to render correctly, Codex spun up a headless browser, navigated to the local dev URL, took a screenshot, inspected the DOM, identified that a CSS grid template was wrong, fixed it, retested, and reported back. I didn't touch the keyboard during any of it.

Claude, on the same bug, asked me to describe what I was seeing. When I gave it the symptom, it added a few `console.log` statements and asked me to run the code and paste the output. Three iterations later we had the fix. Total elapsed time: roughly the same. Total tokens spent: more (because Claude was reading the file each iteration). Total amount of *me* required: significantly more.

If you're a vibe coder who wants the model to drive, Codex's debugging autonomy is a meaningful win. If you're a careful reviewer who wants visibility into every step, Claude's collaborative style might actually be what you prefer.

## Memory and context — different philosophies, both valid

Claude's context handling is in-session focused. When you compact a session in Claude Code, it intelligently removes redundancy — duplicate file reads, repeated tool outputs, that kind of thing. But across sessions, Claude is mostly stateless. Your CLAUDE.md files persist, project memory persists, but the actual conversational context resets.

Codex compacts the entire conversation but preserves the last 20,000 tokens uncompressed. More importantly, Codex builds *global memory* — patterns and preferences it learned from previous sessions, even across projects. You start a new repo on Wednesday and Codex remembers the formatting conventions you preferred on Monday's repo.

For a freelancer or agency operator working across many client projects, Codex's global memory is genuinely useful. For someone deep inside a single codebase for weeks at a time, Claude's project-scoped focus might actually be safer — you don't want a pattern from your hobby project leaking into client code.

## Code review quality

I had both tools review a Laravel pull request I was about to ship. Same diff. Same prompt: "Review this PR. Flag bugs, security issues, and code quality problems."

Claude's review was comprehensive. It flagged a SQL injection risk I hadn't considered (real catch), suggested three refactors with code snippets, called out two naming inconsistencies, and prioritized the issues by severity. It also flagged seven things that were technically correct but completely out of scope for the PR — making the actually critical SQL injection finding harder to spot in the noise.

Codex's review was tight. It only flagged issues inside the diff, referenced exact line numbers, didn't include code snippets (just descriptions of what to change), and finished in roughly a third of the time. It missed the SQL injection nuance — caught the obvious version, missed the edge case Claude found.

The honest verdict: Claude's reviews are deeper but noisier. Codex's reviews are focused but shallower. For a high-stakes security review, Claude. For a normal PR review where you want signal not noise, Codex.

## Ecosystem and hooks

Claude Code's ecosystem is years ahead. Desktop app, web interface, browser extensions, mobile, seamless session transfer between devices, and most importantly the **hook system** — custom scripts that fire at lifecycle points. I have hooks that block unsafe shell commands, hooks that auto-format on file save, hooks that prevent commits without a linked issue. There's no equivalent in Codex.

Codex has a desktop app now and a web version, but the hook ecosystem doesn't exist. You can write skills (and Codex ships with a built-in skill creator, which is a nice touch), but you can't intercept the lifecycle the way Claude lets you.

If you've invested in a complex Claude Code setup with hooks and custom skills, ripping that out to switch to Codex is going to hurt. I built one of those setups myself and documented it in [Build an AI operating system with Claude Code](content/mejba.me/build-ai-operating-system-claude-code.md) — three months of work I'd have to redo from scratch on Codex.

## Sub-agents — opposite philosophies

This is where the two tools made deliberately opposite design choices, and which one is "better" depends entirely on your workflow.

Claude pioneered sub-agents and has the more mature integration. Claude can spawn sub-agents automatically when it detects a task that benefits from parallelization. Each sub-agent gets **strict context isolation** — only the immediate prompt, no parent conversation history. This is great for safety (no context bleeding) and great for parallelism (you can run many sub-agents at once without ballooning token costs).

Codex sub-agents are different. They **inherit the full conversation history** plus the parent prompt. They have to be invoked explicitly — Codex doesn't auto-spawn them. The inheritance means Codex sub-agents perform much better on research tasks where continuity matters, because they actually know what the parent was working on.

For pure parallel implementation work, Claude wins. For research-heavy delegation where the sub-agent needs to know the broader context, Codex wins.

If you want to go deeper on the sub-agent architecture, I broke down Claude's specific approach in [Forked sub-agents in Claude Code](content/mejba.me/forked-subagents-claude-code-anthropic.md), and the parallel Codex pattern surfaced in my [Codex consumer agent / chief of staff writeup](content/mejba.me/codex-consumer-agent-chief-of-staff.md).

## The two big tables

Here's the full nine-category breakdown in compact form, then the project-level metrics.

### Category-by-category comparison

| Category | Claude Code (Opus 4.7) | Codex CLI (GPT-5.5) |
|---|---|---|
| Usability | UI degraded after v2.1.0 — rendering glitches, terminal hangs | Rust-based, smoother, no crashes after long sessions |
| Permissions | Restrictive, no global bypass | YOLO mode for permissive autonomous runs |
| Personality | Instruction-dependent, sycophancy hard to remove | Settings-based, easier to dial down |
| Pre-installed skills | Manual install required | Agent browser skill + built-in skill creator |
| Unique features | Rewinding (undo steps), reasoning visible mid-task | `--attempt` flag (multiple implementations, pick best), real image gen via OpenAI image models |
| Planning depth | 24 min, deeper, UX-focused | 8 min, simpler, faster, backend-focused |
| Greenfield behavior | Always plans before implementing | Skips explicit planning, starts coding directly |
| Fallback strategies | Requires all inputs upfront, blocks on missing keys | Implements graceful degradation automatically |
| Debugging style | Iterative, asks user for symptoms | Autonomous via agent browser skill |
| Init command output | Verbose CLAUDE.md with architecture (some redundancy) | Refined setup with commit/PR/security guidelines |
| Code review | Detailed, includes snippets, prioritized — but reports out-of-scope issues | Focused on diff, line numbers, no snippets |
| Memory | Session-scoped, project-bound, mostly stateless across sessions | Global memory across sessions and projects |
| Context compaction | Removes redundancy in-session | Compacts whole conversation, preserves last 20k tokens uncompressed |
| Ecosystem | Desktop, web, mobile, hooks system, seamless session transfer | Web + newer desktop app, no hook equivalent |
| Sub-agents | Auto-spawnable, strict context isolation | Explicit invocation, inherit full conversation history |
| Pricing | No free tier, restrictive limits even on Pro/Max | Free tier with limited usage |

### Sample app metrics — same project, both tools

| Metric | Claude Code (Opus 4.7) | Codex CLI (GPT-5.5) |
|---|---|---|
| Total tokens consumed | 173,000 | 82,000 |
| Planning time | 24 minutes | 8 minutes |
| Planning style | UX-focused, deep | Backend-focused, fast |
| Implementation approach | Functionality + UX balance | Functionality + speed |
| Fallback handling | Required user input for missing API key | Auto-degraded gracefully and continued |
| Debug interventions needed | 3 (Claude added logs, asked for output) | 0 (Codex used agent browser autonomously) |
| Final UI quality | Visibly better | Functional but plain |

## What I picked, and what I'd pick if I were you

I'm keeping both. That's the honest answer.

Claude Code is still my default when I'm building something where the UX matters, where I'm working on a single codebase deeply, or where I want the hook system enforcing my conventions. The depth of planning, the ecosystem, and the sub-agent maturity are still ahead.

Codex is now my default for autonomous overnight runs, anything backend-only, anything where I'm token-budget-constrained, and anything where I genuinely don't want to be in the loop for debugging. The Rust-based stability alone makes it the right tool for long sessions.

If I had to pick *one* — and I had to pick today, May 2026, with current pricing and current versions — and I were a solo developer or freelancer running on a budget, I'd pick Codex. The token efficiency and free tier matter more than the polish gap when you're paying out of pocket. I covered the broader cost case in [Codex vs Claude Code subscription](content/mejba.me/codex-vs-claude-code-subscription.md) and the trends pointing this direction in [Sonnet 4.8, GPT-5.5, Cyber Alpha — the Codex week](content/mejba.me/sonnet-48-gpt55-cyber-alpha-codex-week.md).

If I were leading an engineering team at a company that already had Claude Max subscriptions and a working hook setup, I'd stay on Claude. The switching cost is real, the ecosystem advantage is real, and the planning depth pays for itself on production code where bugs cost more than tokens.

The era of "Claude is obviously the answer" is over. The era of "it depends, and you should probably run both" started this quarter.

## Frequently Asked Questions

### Is Opus 4.7 still better than GPT-5.5 for coding?
Opus 4.7 still leads on SWE-bench Verified (87.6% vs GPT-5.5's lower score) and produces visibly better UI code. But GPT-5.5 leads on Terminal-Bench 2.0, runs 53% more token-efficient on equivalent tasks in my testing, and handles autonomous debugging without user intervention. Different leaders per category — see the full table above.

### How much cheaper is Codex than Claude Code in real usage?
On the same project I built in both tools, Codex used 82,000 tokens versus Claude's 173,000 — roughly 53% less. Combined with Codex's free tier and Claude's shared usage budget across claude.ai and Claude Code on Pro/Max plans, the practical cost gap is even larger than the per-token pricing suggests.

### Did Claude Code 2.1.0 actually have stability problems?
Yes. I hit visible rendering glitches, two terminal hangs requiring force-quit, and the tightened permission system added friction during multi-step tasks. Codex CLI's Rust rewrite ran a six-hour session with forty tool calls and no crashes.

### Which one handles long autonomous runs better?
Codex, by a meaningful margin. Its automatic fallback handling means it doesn't block on missing inputs, and its built-in agent browser skill lets it debug autonomously. Claude tends to stop and ask for confirmation, which is fine when you're at the keyboard but a problem for overnight runs.

### Should I switch from Claude Code to Codex CLI?
Don't fully switch — run both. Use Claude for UX-driven full-stack work, complex codebases, and anywhere your hook setup matters. Use Codex for backend work, autonomous tasks, token-constrained situations, and long sessions where stability beats polish. The right answer for May 2026 is a two-tool workflow, not picking sides.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
