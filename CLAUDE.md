# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**My AI Crew** is a multi-brand AI content creation system using Claude Code agents. It generates SEO-optimized long-form content (3,000+ words) for four websites with distinct brand voices. There is no build system, test suite, or package manager — the entire workflow is agent-driven through Claude Code.

## Architecture

```
.claude/agents/aria.md       # Agent definition (triggers, model, inline system prompt)
.claude/agents/kfc/          # Spec workflow agents (requirements, design, tasks, impl, test, judge)
.claude/system-prompts/      # Shared system prompts for spec workflows
aria/aria-system-prompt.md   # Reference copy of Aria's system prompt (not loaded by Claude Code)
content/{brand}/             # Generated posts, one .md per article
  mejba.me/                  # Primary brand (~176 posts)
  ramlit.com/                # ~7 posts
  colorpark.io/              # ~7 posts
  xcybersecurity.io/         # ~6 posts
```

### Aria Agent

The **Aria agent** (`.claude/agents/aria.md`) is the primary workhorse. It is configured with `model: opus` and contains the full system prompt inline. The separate `aria/aria-system-prompt.md` is a reference copy only — the agent definition in `.claude/agents/` is what Claude Code loads.

Aria is a research-driven content agent. Before writing any article, it executes a mandatory research protocol:
1. `Glob content/[brand]/*.md` — scan existing posts for voice matching and internal linking
2. `WebSearch` for the primary keyword — analyze competition and find content gaps
3. `WebSearch` for current data — statistics, tool versions, pricing
4. `Write` to save the completed article to `content/[brand]/[slug].md`

### Spec Workflow (KFC Agents)

Seven sub-agents in `.claude/agents/kfc/` implement a structured spec development workflow: requirements → design → tasks → implementation → testing → judging. The workflow is bootstrapped via `.claude/system-prompts/spec-workflow-starter.md`.

## Usage

**Content generation:**
```
@aria Create a blog post for [brand] about [topic]
```

**Save output:**
```
save the blog post to content/[brand]/[slug].md
```

**Revisions:**
```
@aria [specific revision instruction]
```

## Content Output Format

Every generated post follows this exact structure:
```
**BRAND:** [website]
**TITLE:** [< 60 chars, primary keyword in first half]
**SLUG:** [3-5 words, lowercase, hyphenated]
**PRIMARY KEYWORD:** [exact target phrase]
**SECONDARY KEYWORDS:** [2-3 related terms]
**META DESCRIPTION:** [150-160 characters]
**TAGS:** [Exactly 5: 2 broad + 2 specific + 1 format]
**CONTENT TYPE:** [Deep Dive / Review / Comparison / Opinion / Case Study / News Analysis]
**CONTENT CLUSTER:** [cluster name]
**TRANSFORMATION GOAL:** [one sentence]

---

[Article body — 3,000+ words]
```

Aria also generates a social distribution package (Twitter, LinkedIn, Newsletter snippets) after each article.

## Brand Voices

| Brand | Voice | Perspective | Example phrasing |
|-------|-------|-------------|-------------------|
| mejba.me | Personal, passionate | First-person | "I built...", "When I tested..." |
| ramlit.com | Professional, corporate | Third-person | "Ramlit delivers...", "Our team..." |
| colorpark.io | Creative, bold, opinionated | Brand voice | "ColorPark believes...", "Great design is a business decision" |
| xcybersecurity.io | Authoritative, security-focused | Expert voice | "Safeguard your business...", "Our security team..." |

## Brand Color Themes (for image prompts)

| Brand | Background | Primary | Style |
|-------|-----------|---------|-------|
| mejba.me | #0F172A → #1E293B | #8B5CF6 → #3B82F6 → #06B6D4 | Futuristic, tech-forward, neon glows |
| ramlit.com | #1A1A1A → #2D2D2D | #DC2626 → #EA580C | Professional, corporate, clean lines |
| colorpark.io | #0A0A0A → #1F1F1F | #EF4444 → #F97316 | Creative, dynamic, artistic motion |
| xcybersecurity.io | #0F172A → #1E1B4B | #06B6D4 → #3B82F6 | Secure, trustworthy, digital fortress |

## Hard Constraints

- No table of contents in posts
- No AI filler phrases: "In today's fast-paced world...", "Furthermore", "Moreover", "In conclusion", "It's important to note...", "Revolutionary", "Game-changing"
- Always identify the target brand before generating content
- Brand-specific footer/CTA is mandatory on every post
- Article slugs used as filenames: `content/[brand]/[slug].md`
