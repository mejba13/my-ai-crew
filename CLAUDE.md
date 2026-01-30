# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**My AI Crew** is a multi-brand AI content creation system using Claude Code agents. It generates SEO-optimized blog posts (2,500-3,000 words) for four websites with distinct brand voices.

## Architecture

This is an **agent-based content system** with no traditional build/test infrastructure. The core execution model is Claude Code's agent framework.

**Key Components:**
- `.claude/agents/aria.md` - Agent configuration (triggers, model selection)
- `aria/aria-system-prompt.md` - Complete system prompt with brand guidelines
- `content/[brand]/` - Generated blog posts organized by brand

**Supported Brands:**
| Brand | Voice | Perspective |
|-------|-------|-------------|
| mejba.me | Personal, passionate | First-person |
| ramlit.com | Professional, corporate | Third-person |
| colorpark.io | Creative, bold | Brand voice |
| xcybersecurity.io | Authoritative, security-focused | Expert voice |

## Usage

**Content Generation:**
```
@aria Create a blog post for [brand] about [topic]
```

**Save Output:**
```
save the blog post to content/[brand]/[slug].md
```

**Revisions:**
```
@aria [specific revision instruction]
```

**Agent Management:**
- `/agents` - View all agents
- `/agents → Select agent → Edit agent` - Modify agent

## Content Requirements

Every generated post must include:
- Brand identifier
- Title (< 60 characters, primary keyword in first half)
- Slug (3-5 words, lowercase, hyphenated)
- Exactly 5 tags (2 broad + 2 specific + 1 format)
- Meta description (150-160 characters)
- Article body (2,500-3,000 words strictly)
- Brand-specific footer/CTA (mandatory)
- Featured image prompt matching brand colors

**Constraints:**
- No table of contents
- No AI-sounding filler phrases ("In today's fast-paced world...", "Furthermore", "In conclusion")
- Always identify target brand before generating
- Match brand voice and color theme exactly
