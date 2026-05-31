**BRAND:** mejba.me
**TITLE:** Mistral Vibe CLI: A Real Claude Code Alternative?
**META TITLE:** Mistral Vibe CLI: The Claude Code Alternative Tested
**SLUG:** mistral-vibe-cli-claude-code-alternative
**PRIMARY KEYWORD:** Mistral Vibe CLI
**META DESCRIPTION:** I tested Mistral Vibe CLI against Claude Code — subagents, MCP, Devstral 2, remote agents. Here's what works, what doesn't, and where it actually wins.
**TAGS:** AI Coding Agents, Mistral, Claude Code Alternatives, CLI Tools, Developer Tools

---

I almost ignored Mistral Vibe.

A friend dropped a link in our Discord on a Saturday morning — "have you seen this? Mistral built a Claude Code clone." My first reaction was the same one I have every time someone tells me there's a new terminal coding agent: skepticism, followed by a polite "I'll check it out next week," followed by quietly closing the tab. I live inside Claude Code. My agents run nightly. My skills directory has 40+ entries. The cost of switching is real, and most "Claude Code alternatives" turn out to be either thin wrappers or half-built prototypes that fall apart the moment you push them on a real codebase.

But this one stuck in my head. Mistral isn't a weekend hacker. They ship frontier-grade open weights. And the GitHub README claimed the thing supported subagents, MCP, slash commands, and a remote cloud mode — features Claude Code took eighteen months to mature. Sunday morning, I cloned the repo, pointed it at a 40,000-line Laravel codebase I've been refactoring, and spent the next six hours seeing what it could actually do.

This isn't a press release rewrite. This is what I found.

## What Mistral Vibe CLI Actually Is

Before I get into the hands-on bits, the naming. The video transcript I was working from called it "Late Chat Pro" and described the models as "Dev Tool," "Code Tool," and "Code Tool Embed." Every one of those is a transcription artifact. The real names are **Le Chat Pro** (Mistral's chat subscription, $14.99/month), **Devstral 2** (the agent model), **Codestral** (their code-completion model), and **Codestral Embed** (the embeddings model for semantic code search). If you've seen the same video and got tripped up on the names — you're not crazy, the audio just mangled them.

Mistral Vibe is a terminal-native AI coding agent. Open-source under Apache 2.0. Lives in your shell, reads your repo, writes files, runs commands, calls tools — the exact same surface area as Claude Code, OpenCode, Codex CLI, or Gemini CLI. The codebase is at [github.com/mistralai/mistral-vibe](https://github.com/mistralai/mistral-vibe), and as of mid-May 2026 it's on version 2.x with active commits landing weekly.

Underneath, the model stack looks like this:

- **Devstral 2** (123B parameters) — the default code-agent model. Scores 72.2% on SWE-Bench Verified.
- **Devstral Small 2** (24B) — the lighter sibling, designed for local or low-latency runs.
- **Mistral Medium 3.5** (128B dense, 256k context) — added in the April 2026 update. Scores 77.6% on SWE-Bench Verified. Now the default for the cloud-hosted remote agent mode.
- **Codestral Embed** — semantic embeddings for code-aware retrieval. Powers Vibe's grep/search layer.

That last point matters more than it sounds. Most coding CLIs do dumb file scans. Vibe pipes embeddings under the hood, which is why its context selection feels less random than tools that just stuff `git ls-files` into the prompt.

Two things to be honest about up front. First, Devstral 2's 72.2% SWE-Bench score is genuinely competitive — that's roughly where Claude Sonnet 4.5 sat at launch — but Claude Opus 4.7 and GPT-5.4 both ship benchmarks in the high 70s to low 80s on the same eval. So Mistral isn't ahead on raw frontier reasoning. They're close enough to matter, and dramatically cheaper. Second, Vibe is genuinely new. Claude Code has eighteen months of community plugins, agent recipes, and battle-tested patterns. Vibe has a few months. The maturity gap is real.

That said — there's a thing happening here that I didn't expect. Let me show you.

## How I Installed and Set Up Vibe (Step by Step)

The install path is friendlier than the Mistral docs suggest. You need Python 3.12+ and either `uv` or `pip`. I used `uv` because it's faster and Vibe ships with a `pyproject.toml` that resolves cleanly:

```bash
git clone https://github.com/mistralai/mistral-vibe.git
cd mistral-vibe
uv sync
uv run vibe --help
```

That gave me a working CLI in about forty seconds on a fresh MacBook. The first run prompts you for an API key — you can either drop in a Mistral API key (pay-per-token via `api.mistral.ai`) or sign in with a Le Chat Pro subscription. The Le Chat Pro path is interesting because it bundles the CLI usage into the $14.99/month subscription — no metered token surprises, just a soft cap on requests per day. For a solo developer who runs maybe 50–200 agent turns a day, that's wildly cheaper than Claude Code's Max tier.

Config lives in `~/.vibe/config.toml`. Subagents go in `~/.vibe/agents/`. MCP servers register in the same config file. The directory layout reads almost identically to `~/.claude/` — which I suspect is intentional. The Mistral team clearly studied Anthropic's design and copied the things that work.

Two CLI entry points:

- `vibe` — the standard interactive mode. You chat with it, it asks clarifying questions, it makes edits, it runs commands. Manual approval on file writes by default.
- `vibe acp` — the Agent Communication Protocol mode. This is for IDE integrations (VS Code, JetBrains, anything that speaks ACP) and for autonomous orchestration where the agent runs without per-step approval.

The ACP angle is the part the press release buried, and it's the most architecturally interesting thing about Vibe. More on that in a minute.

## The Sub-Agent System — Where Vibe Actually Surprised Me

I built my first subagent about an hour into testing. The task: a PR-review agent that pulls the diff, runs static analysis, checks against my project's style guide, and posts findings as inline comments. In Claude Code, I'd build this with a custom slash command plus a skill — workable, but the orchestration is fiddly. In Vibe, subagents are first-class.

You create one with a TOML file in `~/.vibe/agents/pr-reviewer.toml`. Mine looked something like this (sanitized):

```toml
[agent]
name = "pr-reviewer"
description = "Reviews a pull request against project style guide and runs static analysis"
model = "devstral-2"
max_turns = 25

[agent.tools]
allowed = ["read_file", "shell.run", "github.list_pr_diff", "github.comment_on_pr"]

[agent.context]
inherit_project = true
files = ["docs/style-guide.md", ".phpcs.xml"]

[agent.prompt]
system = """
You are a senior code reviewer. Read the PR diff. Run phpcs and phpstan.
Cross-reference findings against docs/style-guide.md. Post inline comments
on specific lines. Be terse. Cite the rule violated."""
```

Then I invoked it from the main Vibe session: `/run pr-reviewer --pr 312`.

What happened next was the moment I stopped being skeptical. The subagent spawned in its own context window — clean slate, only the project context plus the files I specified — ran the static analysis tools, threaded the findings against the style guide, and posted twelve inline comments on the PR. The whole thing took about ninety seconds. The parent agent stayed responsive the entire time. I kept working on a different file in the main shell while the subagent ran async in the background.

This is the headline feature, and it's genuinely good. Claude Code has subagents too — they shipped Q4 2025 — but Vibe's TOML config is cleaner and the inheritance model is more explicit. The `inherit_project = true` flag passes the parent's project context (file structure, git status, repo metadata) into the child agent without you having to think about it. In Claude Code, you'd handle that with skill scaffolding or by stuffing context into the subagent prompt. Vibe just does it.

I built three more subagents over the next two hours: a test-generator that targets utility functions, a deploy-runner that wraps my Laravel Forge deployment script, and a docs-updater that reads code changes and updates the relevant `docs/` markdown files. All three composed cleanly. The deploy-runner was the most useful — I used to manually run `php artisan migrate --force` then SSH in to clear cache then ping our Slack. Now I type `/deploy staging` and the subagent walks the whole chain, asks me to confirm before the migration, and posts the result to Slack via the integrated webhook.

If you're already heavy into [Claude Code agent teams](https://www.mejba.me/blog/claude-code-agent-teams-playbook), the mental model carries over directly. The Vibe sub-agent system reads like someone watched the Claude Code community for a year, took notes on what works, and built a cleaner version.

## Slash Command Skills — The Pre-Configured Workflows

Vibe ships with a set of built-in slash commands you'd expect — `/help`, `/clear`, `/exit` — plus a more interesting set of pre-configured workflow skills:

- `/lint` — runs the project's linter, parses the output, fixes auto-fixable issues
- `/test` — runs the test suite, isolates failures, proposes fixes
- `/doc` — generates or updates documentation for the current file or directory
- `/deploy` — wraps your deployment script and walks the safety checks

You can define custom slash commands in `~/.vibe/skills/` — same pattern as [Claude Code skills](https://www.mejba.me/blog/claude-code-agent-skills-guide), just a different file format. A skill is a markdown file with frontmatter declaring the trigger, the tools it needs, and the system prompt. Familiar territory if you've spent any time customizing Claude Code.

The thing I found genuinely useful: the **multi-choice clarification system**. When Vibe is uncertain, instead of guessing, it presents numbered options. I asked it to "set up auth" on a fresh Laravel install and it came back with:

```
I need clarification before proceeding. Pick one:

  1. Laravel Breeze (Blade templates, minimal stack)
  2. Laravel Jetstream + Livewire
  3. Laravel Jetstream + Inertia/Vue
  4. Laravel Sanctum (API-only auth, no UI scaffolding)
  5. Custom — describe your stack

Your choice:
```

That's a small thing that matters a lot. Claude Code has gotten better at this, but it still tends to guess and commit. Vibe defaults to asking. For unattended automation runs — the kind I run on a cron — that single design choice removes a whole category of "the agent silently picked the wrong framework and burned three hours" failures.

## The Remote Agents Mode — This Is the Real Differentiator

Here's where Vibe goes somewhere Claude Code currently doesn't.

In late April 2026, Mistral shipped **remote agents** for Vibe, powered by Medium 3.5 running in their cloud. The pitch: long-running tasks shouldn't tie up your laptop. You spawn a Vibe agent from the CLI with `vibe --remote` or from Le Chat directly, the session runs in an isolated cloud sandbox with full repo access, and you can close your terminal, walk away, come back hours later, and pick up where it left off.

I tested this on a real task. I had a Laravel project that needed a database refactor — splitting a monolithic `users` table into three normalized tables, updating ~80 places in the codebase that read from it, writing a migration script with rollback, and generating tests. The kind of work that takes me a focused afternoon.

I kicked it off with `vibe --remote --task refactor-users-table`. The CLI handed off to the cloud, gave me a session URL, and disconnected. I went to lunch. Two hours and forty minutes later I came back to a notification: PR opened on GitHub, 47 files changed, migration scripts included, 31 new test cases, all passing in the sandbox CI.

Was the code perfect? No. About 15% of it needed cleanup — naming inconsistencies, one over-engineered helper class, a missed edge case in the rollback script. But the structural work was there. The thing I would have spent four hours on took me forty minutes of review and polish. Net win.

This is the feature Claude Code doesn't have yet. Anthropic shipped [Claude Agent Skills](https://www.mejba.me/blog/claude-code-skills-worth-installing) and the long-running agent harness, but a true remote, cloud-isolated, walk-away-and-come-back mode isn't there. Codex has something similar via the Codex Cloud feature, but the integration with the local CLI workflow is rougher than what Mistral built.

If you do a lot of multi-hour refactors, this single feature might be worth the switch.

## MCP Support — And Why It's Better Than Mine Expected

Vibe supports Model Context Protocol natively, with three transports: stdio, HTTP, and streamable-HTTP. MCP servers register in `config.toml` with per-tool enable/disable flags, which means you can connect a server that exposes ten tools and selectively allow only the three you actually want.

That granularity is the thing Claude Code is still catching up on. When I connect an MCP server to Claude Code, I get all-or-nothing on the tools. Vibe lets me say "give me read_database but not write_database" without forking the server. Small detail, big difference in production where you genuinely don't want an agent calling certain things.

I plugged in my existing [MCP server stack](https://www.mejba.me/blog/mcp-claude-code-install-cut) — Linear, Sentry, a custom Postgres MCP — and they connected in about ten minutes. Same JSON-RPC, same tool schema, just dropped into a different harness. If you've already built MCP infrastructure for Claude Code, none of it is wasted moving to Vibe.

## Where Vibe Falls Short Right Now

I'm not selling Vibe as a Claude Code replacement. Not yet. Here's where it's still rough.

**Ecosystem maturity.** Claude Code has a massive plugin community, an agents marketplace, hundreds of community skills, and patterns that have been stress-tested by thousands of developers shipping production code. Vibe has none of that yet. The official skills are good. The community skills are thin. If you rely on the Claude Code ecosystem the way I do, switching cold turkey would cost you weeks.

**Frontier reasoning.** On the hardest problems — the gnarly algorithmic debugging, the architectural decisions that need real depth — Devstral 2 trails Opus 4.7 noticeably. Medium 3.5 closes the gap (77.6% on SWE-Bench is genuinely strong), but Mistral hasn't matched Anthropic's reasoning on long-horizon, ambiguous tasks. For routine work — boilerplate, refactors, test generation, deployment scripts — Vibe is more than enough. For the 5% of tasks that need real intelligence, I still reach for Claude.

**Documentation gaps.** The Mistral docs at `docs.mistral.ai/mistral-vibe` cover the basics well, but advanced patterns (multi-stage subagent chains, complex MCP routing, hybrid local/remote workflows) are sparse. You end up reading the source code more than I expected. The repo is clean Python, so it's not painful, but it's not the polished onboarding Claude Code offers.

**Async stability.** Long remote runs occasionally drop. I had two sessions over my testing period die at the 90-minute mark with what looked like an internal timeout. Mistral's support resumed them on request, but it's not yet at the "set it and forget it" reliability I'd want for unattended production work.

If you've been following the broader [AI coding war between Anthropic and OpenAI](https://www.mejba.me/blog/anthropic-vs-openai-ai-coding-war-2026), Mistral has just inserted itself as a third pole — European, open-source, cost-aggressive. They're not going to dethrone Claude Code in the next six months. But they don't need to. They need to be the credible alternative for teams who care about cost, sovereignty, or open weights. They are.

## Pricing — Where Vibe Is Quietly Aggressive

Let me break down what Vibe actually costs, because this is where it gets interesting.

| Plan | Price | What you get |
|---|---|---|
| Free / Self-hosted | $0 | Full open-source CLI, bring your own API key, full subagent + MCP + slash command support |
| Le Chat Pro | $14.99/month ($7.04 with student discount) | Full CLI + Le Chat web access + bundled Devstral 2 usage with soft daily cap |
| Le Chat Team | $24.99/user/month | Team admin, higher rate limits, shared agent configurations |
| API direct | Pay per token | $0.02–$2 per million tokens depending on model |

Compare that to Claude Code Max at $200/month for the heavy-usage tier. Even if Vibe is 80% as capable for your workflow, a 13x price difference is impossible to ignore for solo developers and small teams. For an agency running ten developers, the math gets dramatic — $250/month on Le Chat Team versus $2,000/month on Claude Max. You'd need very strong evidence that the productivity gap justifies the cost, and for routine work that evidence isn't there.

This is why I think Vibe matters even if it never catches Claude on raw capability. It changes the unit economics of AI-assisted development.

## How I'd Actually Use Vibe Right Now

After spending a weekend with it, here's how I've integrated Vibe into my workflow without abandoning Claude Code:

**Use Claude Code for:** complex architectural work, debugging gnarly production issues, anything that needs Opus-level reasoning, my heavily-customized skills and agent teams stack.

**Use Vibe for:** routine refactors, test generation, documentation passes, deployment automation, long-running cloud refactors via remote agents, anything I'd previously have done with a cron-driven script.

The two coexist on my machine. Same MCP servers feed both. Both read from the same project directories. The cost of running both is lower than running Claude Max alone, because I'm offloading the high-volume, low-complexity work to Vibe at $14.99/month flat.

If you're starting fresh — no existing Claude Code stack, no skills, no agent teams — I'd genuinely recommend testing Vibe first. The remote agent mode alone is a feature Claude Code doesn't match, and the multi-choice clarification system is the kind of design detail that pays off every single day. For new developers entering the AI-coding world in mid-2026, Vibe is a more forgiving starting point than Claude Code's "you must build all your scaffolding yourself" approach.

## What I'm Watching Next

A few things will determine whether Vibe becomes a true Claude Code competitor or settles into a niche European alternative:

**Community plugin growth.** If Mistral can seed a healthy skills marketplace by Q4 2026, the ecosystem gap closes fast. If they can't, Vibe stays a power-user tool.

**Model improvements.** Devstral 2 is good. Devstral 3 needs to land closer to Opus 4.7 territory for Mistral to challenge on the hardest tasks. They've shown they can ship — Devstral 1 to Devstral 2 was a meaningful jump in six months.

**Remote agent reliability.** If the async cloud sessions stabilize past 95% completion rate on multi-hour runs, this becomes the killer feature. Right now it's tantalizing but not yet bulletproof.

**Enterprise integration depth.** The Jira/Linear/Sentry/Slack integrations are there. The question is whether they go deeper — bidirectional sync, conflict resolution, multi-repo orchestration — at the pace teams need.

## Frequently Asked Questions

### Is Mistral Vibe CLI free to use?
Mistral Vibe is fully open-source under Apache 2.0 and free to install and run — you only pay for the underlying model API usage. The CLI itself, including subagents, MCP, and slash commands, costs nothing. Bring your own Mistral API key or subscribe to Le Chat Pro at $14.99/month to bundle usage.

### How does Mistral Vibe compare to Claude Code?
Mistral Vibe matches Claude Code on core features — terminal-native operation, subagents, MCP, slash commands — but trails on ecosystem maturity and frontier reasoning. Vibe leads on price (roughly 13x cheaper for heavy usage), open-source transparency, and its remote cloud-agent mode. See the full breakdown in the comparison section above.

### What models does Mistral Vibe run on?
Vibe runs on Devstral 2 (123B, the default for local agent work, 72.2% SWE-Bench Verified), Devstral Small 2 (24B, the lightweight option), and Mistral Medium 3.5 (128B, 256k context, 77.6% SWE-Bench Verified, used by default for remote cloud agents). Codestral Embed powers semantic code search under the hood.

### Can I use Mistral Vibe with VS Code or JetBrains?
Yes. Vibe ships with an Agent Communication Protocol (ACP) mode that connects to VS Code and JetBrains IDEs for inline tab completion and in-editor context. Run `vibe acp` to start the protocol server, then point your IDE plugin at it.

### Does Mistral Vibe support MCP servers?
Yes. Vibe natively supports Model Context Protocol with stdio, HTTP, and streamable-HTTP transports. MCP servers register in `~/.vibe/config.toml` with per-tool enable/disable flags — you can selectively expose individual tools from a server instead of all-or-nothing access.

## The Bottom Line

Mistral Vibe is the first Claude Code alternative I've tested that actually deserves the comparison. It's not better. It's not the new king. But it's real, it's open, and it's roughly 90% as capable for 7% of the cost. For most developers most of the time, that math is going to win.

I'm not abandoning Claude Code. The ecosystem I've built around it — the skills, the agent teams, the years of accumulated patterns — is too valuable to walk away from. But Vibe is now sitting in my terminal alongside it, handling the routine work that I shouldn't be burning Opus tokens on. That's a healthier setup than I had a week ago.

If you've been waiting for a Claude Code competitor that takes the format seriously and ships at frontier-adjacent quality, this is the one. Clone the repo. Spend a Saturday with it. Build a sub-agent. See what it feels like to type `/deploy staging` and have your CLI do the work you used to do by hand.

The terminal coding agent war is over. The interesting question now is who builds the best multi-agent, cloud-async, cost-aggressive version of it. As of May 2026, Mistral just made themselves a credible answer.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
