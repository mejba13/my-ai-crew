# Repository Guidelines

## Project Structure & Module Organization
This repository is an agent-driven content workspace, not a traditional application. Core configuration lives in `.claude/`: `agents/aria.md` defines the primary writing agent, `agents/kfc/` contains spec workflow agents, and `system-prompts/` stores shared prompts. Published drafts live in `content/<brand>/` as one Markdown file per article, for example `content/mejba.me/gpt-5-4-ai-coding-model-review.md`. The `aria/` directory holds a reference copy of Aria's prompt and is not the runtime source of truth.

## Build, Test, and Development Commands
There is no package manager, build pipeline, or automated test suite in this repo. The working loop is repository inspection plus Claude Code usage:

```bash
claude
/agents
@aria Create a blog post for mejba.me about [topic]
```

Use `rg --files content` to scan existing posts and `git status` before and after edits to confirm only intended files changed.

## Coding Style & Naming Conventions
Write content and docs in Markdown with clear headings, short paragraphs, and consistent metadata blocks when applicable. Keep filenames lowercase and hyphenated: `content/<brand>/<slug>.md`. Brand folders should stay limited to the supported sites: `mejba.me`, `ramlit.com`, `colorpark.io`, and `xcybersecurity.io`. When editing agent configs, preserve existing formatting and keep prompt changes narrowly scoped.

## Testing Guidelines
Quality checks are manual. Before committing, verify the target brand, slug, and file path; confirm the article matches the brand voice; and review for banned filler phrasing noted in [CLAUDE.md](/Users/mejba/Local%20Storage/AI%20Development/ai-agents-team/CLAUDE.md). Preview Markdown in your editor if layout matters, and use `rg "TODO|TBD"` to catch placeholders.

## Commit & Pull Request Guidelines
Recent history favors short, direct commit subjects such as `save new article` and `content: add new blog posts for mejba.me`. Prefer imperative messages that state the outcome, and include the brand when useful, for example `content: add mejba.me article on agent workflows`. PRs should summarize changed brands/topics, list any prompt or agent updates, and include screenshots only when visual assets or rendered Markdown presentation were changed.

## Agent-Specific Notes
Treat `.claude/agents/aria.md` as the active runtime configuration. If you update both the runtime agent and `aria/aria-system-prompt.md`, keep them aligned in the same change.
