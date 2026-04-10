**BRAND:** mejba.me
**TITLE:** Top 10 Claude Code Skills, Plugins & CLIs for 2026
**META TITLE:** Top 10 Claude Code Skills, Plugins & CLIs for 2026
**SLUG:** top-10-claude-code-skills-plugins-clis-2026
**PRIMARY KEYWORD:** Claude Code skills plugins CLIs 2026
**SECONDARY KEYWORDS:** best Claude Code plugins, Claude Code skill stack, Firecrawl Claude Code skill, Playwright CLI Claude Code
**META DESCRIPTION:** The 10 Claude Code skills, plugins, and CLIs I actually run every day in 2026 — tested against real projects, with the trade-offs most tutorials skip.
**TAGS:** Claude Code, AI Development, Developer Tools, Skills & Plugins, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know exactly which Claude Code skills, plugins, and CLIs to install for their specific workflow, how to wire them together, and which ones to skip entirely.

---

I keep a running note on my desktop called `cc-stack.md`. It's the list of every Claude Code skill, plugin, and CLI I've installed, tested, and either kept or ripped out over the last six months. As of this week, the "kept" list has exactly ten entries. Ten tools out of probably sixty or seventy I've tried.

That number isn't a coincidence. Every time the list creeps past ten, something breaks — usually context, sometimes the plugin marketplace, sometimes my attention. I've learned the hard way that a Claude Code setup is a system, not a collection. Adding a tool always has a cost, and most of the tools people write breathless blog posts about don't earn their keep.

So this isn't a "top 10" list in the YouTube listicle sense. It's my actual working stack in April 2026 — the Claude Code skills, plugins, and CLIs I run every day, the ones that have survived at least three real projects, and the ones I'd reinstall on a fresh machine before I wrote a single line of code. I'll tell you what each one does, why I use it, what it cost me to figure out, and — importantly — which ones you should *not* install if your situation is different from mine.

Fair warning: at least two of these tools will sound underwhelming until you understand *why* they're on the list. The most powerful entry is number eight, and it's the one nobody in my network was talking about until about a month ago.

## Why Your Claude Code Stack Matters More Than Your Model Choice

Here's something I'd argue is counterintuitive but true. The gap between Opus 4.6 and Sonnet 4.6 is smaller than the gap between a Claude Code stack with the right tools wired in and one without. I've watched engineers spend hours arguing about model selection while ignoring the fact that their Claude Code setup had zero web access, no persistent memory, no browser automation, and no structured knowledge base. A smarter model can't save you from a blind agent.

Every tool in this list solves a specific capability gap. Some extend what Claude Code can *see* — the web, your inbox, your notebooks. Some extend what it can *do* — run browsers, scrape sites, review code adversarially. Some extend what it can *remember* — markdown vaults, graph RAG systems, skill benchmarks. The stack isn't the point. The *capabilities you unlock* are the point.

I'll walk through all ten in order, starting with the one I installed first and recommend to literally every new Claude Code user. But before we get to tool number one, I need to flag the question that should be running in the back of your head the entire time you read this: *which of these gaps actually matter for what I'm building?* Because you don't need all ten. I'll come back to that at the end with a decision framework.

## 1. The Codex Plugin — My Adversarial Second Opinion

The first tool I install on any new Claude Code machine is the [Codex plugin](https://github.com/openai/codex-plugin-cc) from OpenAI. Yes, OpenAI. I know. A year ago the idea of running a GPT-family model inside a Claude Code session would have sounded absurd. In 2026 it's the single highest-leverage plugin I use, and I'm not subtle about recommending it.

Here's what it does. The plugin adds `/codex:review`, `/codex:adversarial-review`, and `/codex:rescue` slash commands to your Claude Code session. They delegate to your local Codex CLI and run in a completely separate process — no context window contention, no token juggling, no duplicate configuration. You fire off an adversarial review, keep working in the foreground with Opus, and check the results when Codex is done.

The reason it's first on the list is simple: every AI model has systematic blind spots. Opus leans architectural. Codex leans execution-level. When I ran both against the same production codebase on a Saturday night incident last month, Codex caught four high-severity issues, Opus caught eight, and only one overlapped. That's seven bugs a single-model review would have missed — on code I was about to ship to users.

I wrote up the full breakdown of that incident in [my Codex plugin adversarial review deep dive](https://www.mejba.me/blog/codex-plugin-claude-code-adversarial-review), so I won't repeat the whole story here. The short version: if you're shipping anything that users touch, a single AI reviewer is a liability. The Codex plugin is how I stopped pretending that wasn't true.

**When to install this:** Day one. Before any other plugin. It's free (ChatGPT free tier works), takes about five minutes to set up, and the first time it catches a bug Opus missed you'll understand why it's not optional.

**When to skip it:** If you're doing research, content work, or automation with no user-facing code involved. The adversarial review is specifically about defensive code quality. If you're not shipping software, it's overkill.

## 2. Obsidian + The Obsidian Skill — My Working Memory

I used Obsidian for personal notes for about two years before I realized it was secretly the best lightweight RAG system I could give Claude Code. The breakthrough came when I installed the Obsidian skill from the community marketplace and pointed Claude Code at my vault.

The setup is almost embarrassingly simple. Obsidian stores everything as plain markdown files in a folder you own. The skill teaches Claude Code how to navigate that folder structure, follow wiki links, read tags, and use the folder hierarchy as retrieval context. No vector database. No embedding pipeline. No infrastructure to maintain. Just markdown files and a skill that knows how to read them.

Here's what that unlocks. Every client project I work on has a dedicated vault folder. Every meeting note, every architectural decision, every "thing that went wrong and how I fixed it" log — all of it lives in markdown, linked by topic. When I start a new Claude Code session for that project, the agent can pull any of it on demand. It's the difference between explaining the project from scratch every session and picking up exactly where we left off last time.

Is it as powerful as a real graph-based RAG system? No. I'll get to Light RAG later for that. But for personal knowledge management, client project memory, and "stuff I wrote down last month and forgot about," Obsidian plus the skill is the fastest path from "I have notes" to "my agent can read my notes." And it's free — the base Obsidian app, the vault format, the skill.

I go deeper on this exact workflow in [my Obsidian second-brain setup guide](https://www.mejba.me/blog/obsidian-claude-code-second-brain) if you want the step-by-step. The key insight for this list: if you're a solo dev or small team, you probably don't need a database-backed RAG system yet. You need a vault and a skill that reads it.

## 3. Auto Research — The Skill That Improves Itself

Here's where the list starts getting weird in a way I like. Auto Research is a skill that runs experiments on your *other* skills and automatically optimizes them based on performance data.

Let me explain what that means in practice. Say you've built a skill called `summarize-video` that takes a YouTube URL and produces a structured summary. You use it a few times and it's fine, but the outputs feel inconsistent — sometimes great, sometimes too long, sometimes missing key points. Traditionally you'd iterate manually: tweak the prompt, run it on a few samples, tweak again, repeat. Hours gone.

Auto Research automates that loop. You point it at the skill, give it a set of test inputs and a scoring rubric, and it runs iterations — varying the prompt, measuring outputs against the rubric, committing improvements, discarding regressions. When you come back, the skill is measurably better and you have a commit history showing exactly what changed and why.

I wrote a more detailed walkthrough in [my auto-research strategy post](https://www.mejba.me/blog/auto-research-claude-code-strategy) including the exact config I use. The piece I want you to take from this list is: if you're building custom skills and iterating on them manually, you're leaving compound improvement on the table. Auto Research is the closest thing I've found to "set it and forget it" for skill quality.

**A fair warning:** This tool burns tokens. A single optimization run on a medium-complexity skill can chew through meaningful API credits because it runs dozens of variations. I only use it on skills I'll call hundreds of times — where the compounding improvement is worth the upfront cost. Don't point it at a skill you'll use twice.

## 4. awesome-design-md — The Front-End Design Fix

Here's an unpopular take. Claude Code, out of the box, is a pretty mediocre visual designer. Opus 4.6 writes clean component code and handles Tailwind elegantly, but left to its own instincts it builds interfaces that look like every other AI-generated landing page on the internet. Gradient hero. Feature grid. Testimonials. CTA. Same font pairings, same spacing, same vibe.

The fix isn't a better model. It's giving the model a *design system to follow*. That's what [VoltAgent's awesome-design-md](https://github.com/VoltAgent/awesome-design-md) repository provides — a growing collection of DESIGN.md files that capture the visual systems of popular websites. Typography. Color palettes. Spacing rules. Component patterns. Layout principles.

You drop one of these files into your project, tell Claude Code to use it as the reference design system, and suddenly the output stops looking generic. I've used the Linear-inspired DESIGN.md for a SaaS admin panel, the Stripe-inspired one for a pricing page, and a custom one I built for my own portfolio based on editorial magazines. All three produced code that didn't look like AI defaults.

<!-- IMAGE: Side-by-side comparison of a Claude Code-generated landing page without a DESIGN.md file and the same prompt with a DESIGN.md file loaded, showing the dramatic shift from generic AI aesthetics to intentional design. Alt text: "Claude Code frontend design comparison with and without awesome-design-md file". Caption: "The DESIGN.md file on the right is the only thing that changed between these two outputs." -->

The repository has been one of the fastest-growing Claude Code resources I've watched this year. It works because it attacks a specific, well-defined weakness — front-end design taste — with a specific, well-defined fix: explicit design grammar the model can follow.

**When to install this:** You build front-end anything. Landing pages, dashboards, marketing sites, product UIs. It costs nothing, installs in seconds, and improves your output the first time you use it.

**When to skip it:** You're working on backend systems, CLI tools, data pipelines, or any project where visual design isn't part of the deliverable.

## 5. Firecrawl CLI + Firecrawl Skill — Real Web Access

This is the one I tell everyone about and most people nod politely and don't install. They should. Firecrawl is the difference between an agent that can *kind of* read the web and an agent that can actually extract structured data from any site on the internet — including the ones with anti-bot protection, JavaScript-heavy rendering, and login walls.

The [Firecrawl CLI](https://github.com/firecrawl/cli) is a command-line tool. The Firecrawl skill is the companion piece that teaches Claude Code how to use the CLI correctly — which flags to pass, what output formats to request, how to handle rate limiting, and how to avoid the common pitfalls. One install command, and your Claude Code session has a complete web data toolkit: scrape pages to clean markdown, search the web and scrape results in one step, launch persistent browser sessions, crawl entire sites, and map URLs on a domain.

What I use it for, concretely:
- Scraping competitor product pages into structured markdown for content research
- Pulling documentation from sites that don't have clean export formats
- Building training data from public sources for custom skills
- Running "what's changed on this page since last week" diffs as part of a monitoring loop

Before Firecrawl, I was stitching together fetch calls, HTML parsing, and fallback logic for anti-bot protection. It was fragile and I spent more time maintaining the scrape code than using the data. Firecrawl handles all of that underneath a single CLI. I haven't touched my old scraping code in three months.

I wrote about this in more depth in [my Firecrawl agentic web access breakdown](https://www.mejba.me/blog/firecrawl-agentic-web-access-claude) — if you're serious about agents that touch the web, that's the longer read. The short version for this list: if your agent needs to read the internet, you need Firecrawl. Full stop.

## 6. Playwright CLI — Browser Automation That Actually Scales

I used Playwright MCP for a month before switching to the Playwright CLI. The switch saved me roughly 75% of the tokens I was burning on browser automation tasks. That's not a typo.

Here's what's happening under the hood. Playwright MCP returns accessibility trees inline in the tool response — so every action the agent takes comes back with a verbose snapshot of the page structure glued to the response. On a moderately complex page, a single click plus snapshot can run 5,000 to 10,000 tokens. Across a full automation sequence, you're easily into six figures.

The [Playwright CLI approach](https://playwright.dev/docs/getting-started-cli) works differently. The CLI saves snapshots and screenshots to files on disk. The agent invokes CLI commands, reads only what it needs from the saved files, and references the rest by path. Actual benchmark from testing I've seen: a browser automation task that consumed ~114,000 tokens with MCP dropped to ~27,000 tokens with the CLI. Roughly 4x reduction, consistent across multiple runs.

What I use Playwright CLI for: end-to-end test generation against real apps, scraping sites that Firecrawl can't handle (usually because they require complex multi-step login flows), and building visual regression checks for front-end work. The accessibility tree approach means Claude Code can reference elements by role and name — "click the Save button" — instead of guessing pixel coordinates from screenshots. It's precise, deterministic, and cheap compared to pixel-based automation.

**Bonus detail nobody tells you:** Playwright CLI integrates cleanly with skills. You can build a skill that wraps a common automation flow — login, navigate, extract, logout — and call it from any Claude Code session without reloading the whole Playwright tool schema. That's how I run my content distribution automation across multiple social platforms.

## 7. Notebook LM + Pine CLI — Offloading Heavy Reading

I didn't expect this one to make the list. I started using Notebook LM as a separate tool for research projects — upload a batch of PDFs, let Google do the heavy lifting on document analysis, ask questions in the web UI. It worked well as a standalone tool but felt disconnected from my Claude Code workflow.

The Pine CLI changed that. It's a command-line interface that connects Claude Code to the Notebook LM web app for programmatic access — batch document uploads, slide revisions, full-text extraction, and automated sharing. The strategic value isn't just the convenience. It's that Notebook LM's analysis runs on Google's servers, not against my Claude API quota. That means I can throw hundreds of pages of reference material at a research project without inflating my Claude Code token usage.

The workflow I've settled on: for any project with a heavy reference corpus — a client's existing documentation, a regulatory framework I need to understand, a codebase I'm auditing — I upload everything to Notebook LM via Pine, run the initial analysis and summarization there, and pull the compressed outputs back into Claude Code for the actual implementation work. Claude Code never has to load the full corpus into context. It only sees the distilled insights.

I walked through this hybrid workflow in [my Notebook LM Claude Code dev workflow post](https://www.mejba.me/blog/notebooklm-claude-code-dev-workflow), and I think it's one of the most underrated patterns in 2026. The meta-lesson: not every task should run through your primary agent. Sometimes the right move is offloading the analysis to a tool that's better suited to heavy reading, then feeding the results back in.

## 8. Skill Creator Skill — The Meta-Skill That Changes Everything

This is the entry I promised would be underwhelming until you understood it. Skill Creator is a meta-skill — a skill whose entire purpose is to create, benchmark, and A/B test other skills.

Here's why that matters. The moment you start building custom Claude Code skills for your own workflows, you run into a quality measurement problem. How do you know if version two of your skill is actually better than version one? You *think* it is. The outputs *feel* better. But "feels better" isn't a number you can optimize against, and without real measurement, you end up iterating on vibes. I did this for months and it cost me. Some of the skills I thought I was improving were actually getting worse on real tasks — I just didn't have the evidence to see it.

The Skill Creator skill gives you a structured framework. You define the skill's purpose. You define a test set of representative inputs. You define a scoring rubric — what "good" output looks like in measurable terms. Then the skill runs both versions against the test set, scores the outputs, and tells you which version wins, by how much, and on which specific inputs. Quantitative data instead of gut feel.

I run every custom skill I build through this process now. It catches regressions I'd never have spotted manually. It tells me when a "small tweak" actually made things worse. And because it's integrated with the Claude Code marketplace via the `/plugin` command, the setup is trivial — no external tooling, no custom benchmarking code, no spreadsheets.

The broader shift this unlocks is important. Before Skill Creator, custom skills were craft work — you made them by feel and hoped they held up. After Skill Creator, skills are engineered artifacts with performance data attached. If you're building anything you'll use more than a few times, this is non-negotiable.

I covered the full workflow in [my deep dive on Skill Creator testing and optimization](https://www.mejba.me/blog/claude-skill-creator-testing-optimization) — if you build skills at all, that's the post to read next.

## 9. Light RAG — When Obsidian Isn't Enough

Obsidian plus the skill is great for personal knowledge management and small project vaults. But when you hit a certain scale — thousands of documents, complex entity relationships, client projects with deep interconnected references — a markdown vault starts creaking under its own weight. Retrieval gets imprecise. The agent misses obvious connections. You end up paging through notes manually to find what you need.

That's when I reach for [Light RAG](https://github.com/HKUDS/LightRAG). It's a production-ready, open-source RAG framework from the University of Hong Kong's data science department, presented at EMNLP 2025. What makes it different from conventional vector-only RAG systems is the graph component. Light RAG extracts entities and relationships from your documents and builds a knowledge graph on top of the vector index. When you query it, you get both semantic similarity results *and* graph-based context — related entities, upstream/downstream references, cross-document connections the pure vector search would miss.

In practice, the difference is dramatic on large document sets. I ran the same query against an Obsidian vault and a Light RAG index built from the same corpus (about 4,000 markdown files and technical PDFs from a compliance audit project). Obsidian returned three relevant files via keyword and link matching. Light RAG returned the same three plus eleven more related by entity relationships I hadn't explicitly linked. That extra context changed the analysis meaningfully.

**Important trade-off:** Light RAG is not plug-and-play like Obsidian. You'll spend time on initial setup, storage backend configuration (OpenSearch support was added in March 2026 and I'd recommend it), and ingestion tuning. For a solo dev with a few hundred notes, that overhead isn't worth it — stick with Obsidian. For client projects, compliance work, or any knowledge base north of a thousand documents, Light RAG earns its complexity. It's the difference between an agent that can search your files and an agent that actually understands them.

## 10. Google Workspace CLI — Claude Code as a Personal Assistant

Last on the list, and the one that changed my daily workflow in the most unexpected way. The [Google Workspace CLI](https://github.com/googleworkspace/cli) is an official Google-built command-line tool that gives your terminal direct access to Gmail, Calendar, Drive, Sheets, Docs, Chat, and admin functions. The "AI agent skills" modifier in the repo name isn't marketing — Google built this one with agents in mind.

Once it's installed and authenticated, Claude Code can use GWS CLI like any other terminal command. No MCP server to configure. No custom integration code. The agent reads the CLI documentation via skills, knows how to invoke the right commands, and treats your Google Workspace as an extension of its capabilities.

What this actually unlocks is a Claude Code session that doubles as a personal operations assistant. Concrete examples from the last two weeks of my own usage:

- **Morning inbox triage.** Claude Code reads my entire unread Gmail, categorizes by urgency and sender type, drafts replies to routine messages, flags the ones that need my attention, and archives the noise. What used to be thirty minutes of inbox wrangling is now about four.
- **Meeting prep.** Before each call on my calendar, Claude pulls the meeting details, searches Gmail for previous thread history with the attendees, finds related docs in Drive, and compiles a pre-read with context I actually need. I stopped going into calls cold.
- **Document workflows.** Drafting a proposal, pulling data from a sheet, updating a doc, sharing with a client — all of it runs from the same Claude Code session I use for engineering work. No context switching between tools.

The setup does involve enabling services in Google Cloud and authenticating the CLI, which is the most annoying step in this whole list. Budget twenty minutes for it and follow the official docs carefully. Once it's done, you won't touch it again.

**Who this is for:** Anyone who spends meaningful time in Google Workspace every day. Solo founders, consultants, content creators, engineers who manage clients. If you live in Gmail and Calendar, this plugin alone justifies the Claude Code subscription.

**Who should skip it:** If you're not already a Workspace user, there's no value here. Don't migrate to Google just to install this.

## The Decision Framework — Which of These Should You Actually Install?

Here's the honest answer I promised earlier. Not all ten of these belong in every stack. The right subset depends on what you're building, and installing tools you don't need is worse than not installing anything — they create cognitive overhead, plugin conflicts, and context pollution.

Use this breakdown as a starting point:

**If you're new to Claude Code (first 30 days):** Install Codex plugin, Obsidian + skill, and Auto Research. That's it. Three tools. Learn them deeply before adding anything else. The temptation to install everything on day one is the single biggest mistake I see new users make.

**If you're primarily a front-end developer:** Add awesome-design-md and Playwright CLI to the starter three. The design system files will fix your output quality. The CLI gives you real browser testing without the MCP token overhead.

**If your work involves web scraping, research, or automation:** Add Firecrawl CLI + skill and Playwright CLI to the starter three. These two together cover roughly 95% of web interaction needs — Firecrawl for structured data extraction, Playwright for complex interactive flows.

**If you manage large document corpora (legal, compliance, research, consulting):** Add Light RAG and the Notebook LM Pine CLI. The combination of graph-based retrieval plus offloaded heavy reading transforms how you work with reference-heavy projects.

**If you're building custom skills seriously:** Skill Creator is non-negotiable. Install it the day you write your first custom skill. Don't spend months iterating on vibes like I did.

**If you're running Claude Code as a daily driver for operations:** Google Workspace CLI is the highest-leverage install on the list. Not because it's the most technically impressive — it isn't — but because the hours it saves you compound every single day.

Most of you reading this fall into two or three of those categories. That means your realistic starting stack is probably five or six of these ten tools, not all ten. Start small, measure what's actually helping, and be willing to uninstall things that aren't earning their keep.

## What I'm Not Telling You (Yet)

There are three tools that almost made this list and didn't. I want to flag them briefly because I know someone in the comments will ask.

One is a persistent memory plugin I've been testing for about a month. It's promising but not stable enough yet, and I've had two sessions corrupted by it. I'll write it up when it earns a place in the stack.

Another is a local-first vector database that integrates cleanly with Claude Code skills. It's elegant and the author is doing great work, but for my use cases Light RAG does everything it does plus the graph layer, and I can't justify running both.

The third is a voice interface plugin that's fun to demo and nearly useless for real work. It saves you from typing. I type fast. Your mileage may vary.

The omissions tell you something important about how I build this list. A tool has to earn its slot, every month, against real projects. The moment a tool stops pulling its weight, it comes out of the stack. The ten above have earned their slots for the last six months running.

## The Sunday Morning Test

Here's how I decided whether each of these tools made the final cut. I asked myself: if I lost my entire Claude Code setup on a Sunday morning and had to rebuild from scratch before Monday, which tools would I reinstall before I started working?

The ten tools in this list are the answer. Codex plugin before I write any code. Obsidian and the skill before I open a project. Firecrawl and Playwright CLI before I touch anything web-related. Skill Creator before I build my first custom skill. Google Workspace CLI before I check my inbox.

The rest of the tools I've tried — and there have been dozens — don't pass the Sunday morning test. They're nice to have. They're interesting to experiment with. But I wouldn't stop to reinstall them before getting to work, which means they're not actually essential. They're accessories.

Your Sunday morning test list will be different from mine. That's the point. Build your own list. Be ruthless about what earns a slot. And every time you add something new, ask yourself whether you'd rebuild it from scratch at 8 AM on a Sunday before doing anything else. If the answer is no, it doesn't belong in your stack.

One last thing. Go look at your current Claude Code plugin list right now — the actual list, not the one you think you have. Count the ones you've used in the past seven days. For most of you, that number will be under five, which means half your installed tools are dead weight. Start by uninstalling the ones you haven't touched. The best Claude Code stack in 2026 isn't the biggest one. It's the one where every entry pays rent.

## Frequently Asked Questions

### What's the best Claude Code plugin to install first in 2026?
The Codex plugin for Claude Code is the highest-leverage first install for anyone shipping production code. It adds adversarial code review from a second AI model in a separate process, catching bugs single-model reviews miss without consuming your context window. For the full install walkthrough, see the Codex plugin section above.

### Do I really need both Firecrawl CLI and Playwright CLI?
Yes, if your work involves web data. Firecrawl handles structured scraping, markdown extraction, and bulk crawling efficiently. Playwright CLI handles complex interactive flows like multi-step logins, visual regression tests, and sites that require real browser state. They cover different problems and work well together.

### Is Light RAG worth the setup complexity over Obsidian?
Only if you're working with thousands of documents or need graph-based entity relationships. For solo developers with a few hundred notes, Obsidian plus its skill is faster, simpler, and free. Light RAG earns its complexity on large-scale document corpora, compliance work, or client projects with deep interconnected references.

### How many Claude Code plugins should I actually install?
Most working stacks land at five to eight tools total. More than ten becomes hard to maintain and creates plugin conflicts or context pollution. Start with three core tools (Codex, Obsidian, Auto Research) and add only what your actual workflow demands. Uninstall anything you haven't used in seven days.

### Does the Google Workspace CLI need an MCP server?
No. The Google Workspace CLI runs as a standard terminal tool, which Claude Code invokes natively through bash. There's no MCP server to configure. You only need to enable the Google Cloud services you want and authenticate the CLI once.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
