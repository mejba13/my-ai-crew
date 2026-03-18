# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**My AI Crew** is a multi-brand AI content creation system using Claude Code agents. It generates SEO-optimized long-form content (3,000+ words) for four websites with distinct brand voices. There is no build system, test suite, or package manager — the entire workflow is agent-driven through Claude Code.

## Architecture

```
.claude/agents/aria.md       # Agent definition (triggers, model, inline system prompt)
aria/aria-system-prompt.md   # Standalone copy of Aria's full system prompt
content/{brand}/             # Generated posts, one .md per article
  mejba.me/
  ramlit.com/
  colorpark.io/
  xcybersecurity.io/
.claude/agents/kfc/          # Spec workflow agents (requirements, design, tasks, impl, test, judge)
.claude/system-prompts/      # Shared system prompts for spec workflows
```

The **Aria agent** (`.claude/agents/aria.md`) is the primary workhorse. It contains the full system prompt inline and is configured with `model: opus`. Aria is a research-driven content agent that uses WebSearch, Glob, and Read tools to research topics before writing. The separate `aria/aria-system-prompt.md` is a reference copy — the agent definition in `.claude/agents/` is what Claude Code actually loads.

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

## Hard Constraints

- No table of contents in posts
- No AI filler phrases: "In today's fast-paced world...", "Furthermore", "Moreover", "In conclusion", "It's important to note...", "Revolutionary", "Game-changing"
- Always identify the target brand before generating content
- Brand-specific footer/CTA is mandatory on every post
- Article slugs used as filenames: `content/[brand]/[slug].md`
