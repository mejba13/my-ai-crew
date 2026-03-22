**BRAND:** mejba.me
**TITLE:** 9 GitHub Repos That Made My Claude Code 10x Faster
**SLUG:** best-github-repos-claude-code
**PRIMARY KEYWORD:** best GitHub repos Claude Code
**SECONDARY KEYWORDS:** Claude Code plugins, Claude Code skills, Claude Code tools
**META DESCRIPTION:** 9 battle-tested GitHub repos that transformed my Claude Code workflow — from persistent memory to autonomous spec-driven development. Tested and ranked.
**TAGS:** Claude Code, GitHub Repos, AI Tools, Developer Productivity, Resource Guide
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will have a curated, tested stack of Claude Code extensions they can install today to eliminate context loss, automate workflows, and ship production-quality code faster.

---

Three weeks ago I hit a wall that had nothing to do with Claude Code's intelligence. My agent was brilliant — Opus 4.6 running with 1M context, subagents spawning in parallel, code shipping at a pace that would have seemed absurd eighteen months ago. The problem wasn't capability. The problem was that every single session started from scratch.

New context window. No memory of yesterday's architectural decisions. No recollection that we'd already tried and rejected a Redis caching approach. No awareness that the project had a specific design system with exact color tokens and spacing scales. I was spending the first fifteen minutes of every session re-teaching Claude Code things it had already learned — and forgotten.

That frustration sent me down a rabbit hole. I started hunting for repos, plugins, skills, and tools that other Claude Code power users had built to solve the exact problems I was hitting. Not theoretical tools. Not proof-of-concepts with twelve stars and a README that says "coming soon." Production-grade repos that people were actually using to ship real software.

I found nine that fundamentally changed how I work. Some of them I'd heard of. A few surprised me. One of them solved a problem I didn't even know I had — and it's the one I now can't imagine working without.

Here's every repo, what it actually does in practice, and specifically where it fits in your workflow. I'm ranking them by how much they changed my daily output, not by GitHub stars.

---

## The Repo That Fixed Claude Code's Worst Problem

I'm starting with the one that eliminated the wall I described in the opening. If you've ever re-explained your project architecture to Claude Code for the third time in a week, this is your fix.

### Claude Mem — Persistent Memory Across Sessions

**Repo:** [github.com/thedotmack/claude-mem](https://github.com/thedotmack/claude-mem)

Claude Mem is a plugin that automatically captures everything Claude does during your coding sessions, compresses it using the Claude Agent SDK, and injects relevant context back into future sessions. Not a summary. Not a log file you have to manually reference. Actual semantic memory that loads automatically when you start a new session.

The architecture is smarter than a simple conversation logger. It runs five lifecycle hooks — SessionStart, UserPromptSubmit, PostToolUse, Summary, and SessionEnd — that capture the meaningful moments of your interaction. Then it compresses those observations and stores them in a vector database (ChromaDB with SQLite3 backing). When your next session starts, it performs a semantic search against your project context and injects the most relevant memories.

I installed it with two commands:

```bash
/plugin marketplace add thedotmack/claude-mem
/plugin install claude-mem
```

After restarting Claude Code, the difference was immediate. My next session opened with Claude already knowing our database schema, the authentication flow we'd settled on, and the three approaches we'd rejected for the notification system. That fifteen-minute re-onboarding ritual? Gone.

The version I'm running — 9.0.17 — includes search and planning skills on top of the core memory system. You can ask Claude to recall specific decisions from past sessions, and it pulls them from the vector store instead of hallucinating something plausible.

One caveat worth mentioning: the worker service runs an Express API on port 37777. If you're already using that port, you'll need to configure an alternative. Minor inconvenience for what is genuinely the single biggest quality-of-life improvement I've added to my Claude Code setup.

But memory is only half the problem. The other half is what Claude does with that context — and the next repo changed that equation entirely.

---

## The Framework That Turned Claude Code Into a Senior Engineer

### Superpowers — Agentic Skills Framework and Development Methodology

**Repo:** [github.com/obra/superpowers](https://github.com/obra/superpowers)

I'd been hearing about Superpowers for months before I actually installed it. The name sounded like marketing. The reality is that it's arguably the most important repo in the entire Claude Code ecosystem — and with over 42,000 GitHub stars and official acceptance into the Anthropic marketplace as of January 2026, the community clearly agrees.

Superpowers isn't a single tool. It's a development methodology encoded as a skills framework. It transforms Claude Code from a reactive code generator into something that behaves like a senior developer who follows test-driven development, breaks work into atomic tasks, and actually thinks before writing code.

The three pillars that make it work:

**Socratic Brainstorming.** Before Claude writes a line of code, Superpowers forces it through a structured thinking process. It asks clarifying questions. It identifies assumptions. It maps out edge cases. I've watched it catch requirements gaps that I missed in my own spec — and I wrote the spec.

**Micro-Task Planning.** Instead of tackling an entire feature in one context window (where Claude inevitably loses the thread halfway through), Superpowers breaks work into small, verifiable chunks. Each chunk gets its own focused execution. The result is more consistent code with fewer "wait, why did it do that?" moments.

**Test-Driven Development.** This is the one that sold me. Superpowers has Claude write tests before implementation. Not as an afterthought. Not as a checkbox. As the actual driving force behind every piece of code it produces. The tests define the behavior, and Claude writes code until the tests pass.

Installation is straightforward through the marketplace:

```bash
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

The ecosystem around Superpowers has grown fast. There's [superpowers-lab](https://github.com/obra/superpowers-lab) for experimental skills, [superpowers-skills](https://github.com/obra/superpowers-skills) for community-contributed skills, and even [superpowers-chrome](https://github.com/obra/superpowers-chrome) for direct browser control via DevTools Protocol.

What makes Superpowers fundamentally different from just writing a better CLAUDE.md is the methodology. A good CLAUDE.md tells Claude what to do. Superpowers teaches Claude *how to think about work*. That distinction sounds philosophical until you see the output quality difference — then it sounds obvious.

The thing is, Superpowers works best when Claude already has good context about your project. Combine it with Claude Mem, and you get an agent with persistent memory *and* professional development habits. That pairing alone puts you ahead of most Claude Code users I know.

But what if you need Claude to work autonomously for hours without supervision? That requires a different kind of framework entirely.

---

## The Autonomous Workhorse

### GSD (Get Shit Done) — Spec-Driven Development System

**Repo:** [github.com/gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done)

GSD solves a problem that every heavy Claude Code user eventually hits: context rot. You start a complex feature. Claude's doing great for the first thirty minutes. By minute sixty, the context window is bloated with previous tool calls, failed attempts, and intermediate outputs. By minute ninety, Claude's making decisions that contradict what it decided an hour ago because the relevant context has scrolled out of its effective attention window.

GSD's solution is brutally pragmatic. It forces all work into small, checkable plans and runs each plan in a fresh context window. Every completed task gets an atomic git commit. If something goes wrong, you can bisect your way back to the exact point where the agent went off track.

The system ships as an installer you run via npx, and it supports Claude Code, OpenCode, Gemini CLI, and Codex. But here's where it gets interesting — GSD v2 is a standalone CLI built on the Pi SDK, which gives it direct TypeScript access to the agent harness. That means it can:

- Clear context between tasks automatically
- Inject exactly the right files at dispatch time
- Manage git branches per feature
- Track cost and token usage
- Detect stuck loops and recover from crashes
- Auto-advance through an entire milestone without human intervention

That last point is the one that changed my workflow. I can define a milestone — say, "build the user authentication system with email verification, OAuth, and role-based access" — and GSD will break it into tasks, execute each one in a clean context, commit after each success, and keep going until the milestone is complete. I've left it running overnight and come back to a working feature branch with clean commits.

```bash
npx gsd init
```

The `/gsd:do` command accepts freeform text and routes it to the right task, and `/gsd:note` captures ideas without breaking the current workflow. Small touches, but they show a team that actually uses their own tool daily.

Is it perfect? No. Complex tasks with heavy interdependencies sometimes need manual intervention to reorder the execution plan. And the initial setup takes more thought than "just start coding" — you need to write decent specs for GSD to work well. But that's the point. The spec-writing step is where you catch the design problems *before* the agent burns tokens on a flawed approach.

If Superpowers teaches Claude how to think like a senior developer, GSD teaches it how to work like an autonomous team. Different tools for different scales of ambition.

Speaking of ambition — let me show you the repo that made me stop apologizing for AI-generated UIs.

---

## The Design Intelligence Layer

### UI UX Pro Max — Design System Generator for Claude Code

**Repo:** [github.com/nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)

I've written before about [Claude Code's UI capabilities](https://www.mejba.me/claude-code-ui-design-challenge), and my honest assessment has always been: technically impressive, aesthetically mediocre. Claude can build functional interfaces fast, but left to its own devices, the design choices range from "developer placeholder" to "Bootstrap circa 2019."

UI UX Pro Max changes that equation by injecting genuine design intelligence into Claude Code's decision-making. This isn't a theme. It's a reasoning engine loaded with 50+ styles, 161 color palettes, 57 font pairings, 99 UX guidelines, and 25 chart types across 10 technology stacks.

The flagship feature in v2.0 is the Design System Generator. You describe your project — "a SaaS dashboard for financial analytics, targeting enterprise CFOs" — and it generates a complete design system: color tokens, typography scale, spacing system, component patterns, all tailored to your specific use case and audience. Claude then uses that system as its source of truth for every UI decision.

Installation works globally via CLI:

```bash
npm install -g uipro-cli
uipro init --ai claude
```

The skill activates automatically when you request any UI/UX work. I tested it with a project management app — requested a Kanban board with drag-and-drop, a calendar view, and a settings panel. Without UI UX Pro Max, Claude produced a functional but visually flat interface. With it, the same prompt generated an interface with deliberate visual hierarchy, consistent spacing, accessible color contrast ratios, and typography that actually communicated content priority.

The supported stacks cover the ground most developers need: HTML/Tailwind, React, Next.js, Vue, Nuxt.js, ShadCN, Svelte, Flutter, SwiftUI, React Native, and as of v2.1.0, Jetpack Compose for Android.

One thing I particularly respect about this repo: it includes 161 product types with reasoning rules. That means when you tell it you're building a healthcare dashboard versus a gaming social platform, the design decisions shift accordingly. Color psychology, information density, interaction patterns — all contextual. That's the difference between a template library and actual design intelligence.

If you'd rather have someone build this kind of polished UI from scratch, I take on full-stack builds and AI integration projects. You can see what I've shipped at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

Now — the repos I've covered so far all enhance what happens inside Claude Code. The next two extend its reach into entirely different domains.

---

## Beyond Code: Connecting Claude Code to Everything

### n8n-MCP — Workflow Automation From Your Terminal

**Repo:** [github.com/czlonkowski/n8n-mcp](https://github.com/czlonkowski/n8n-mcp)

If you're not using n8n yet, the short version: it's an open-source workflow automation platform — think Zapier, but self-hosted and infinitely more flexible. I use it to automate everything from lead notifications to deployment pipelines to content distribution.

n8n-MCP is an MCP server that gives Claude Code deep knowledge of n8n's node ecosystem. We're talking structured access to 1,239 workflow automation nodes (809 core + 430 community), with 87% documentation coverage and 2,646 pre-extracted configurations from popular templates.

The practical impact: I can describe a workflow in natural language — "when a new GitHub issue is labeled 'urgent', send a Slack notification to the on-call channel, create a Linear ticket, and start a 30-minute timer that escalates to email if the ticket isn't assigned" — and Claude Code builds the entire n8n workflow. Not a pseudocode sketch. A real, deployable JSON workflow that I import directly into n8n.

Setup is minimal. In Claude Code, paste the MCP config:

```json
{
  "mcpServers": {
    "n8n-mcp": {
      "command": "npx",
      "args": ["-y", "@czlonkowski/n8n-mcp"]
    }
  }
}
```

The creator also built [n8n-skills](https://github.com/czlonkowski/n8n-skills) — a dedicated Claude Code skillset that complements the MCP server with deeper workflow-building patterns. Together, they turn Claude Code into the fastest n8n workflow builder I've ever used.

Before n8n-MCP, building a complex workflow in n8n took me an hour of dragging nodes, configuring fields, and testing connections. Now it takes a conversation. The time savings compound fast when you're building automations for clients.

### Obsidian Skills — Your Knowledge Base as a Development Resource

**Repo:** [github.com/kepano/obsidian-skills](https://github.com/kepano/obsidian-skills)

This one comes from Steph Ango — the CEO of Obsidian — and it turns your Obsidian vault into a first-class resource that Claude Code can read, write, and reason about.

The repo contains three skills that follow the Agent Skills specification:

**obsidian-markdown** teaches Claude Code to create and edit `.md` files using Obsidian Flavored Markdown. Not generic Markdown — Obsidian-specific syntax including callouts, dataview queries, and internal wiki links.

**obsidian-bases** enables Claude to create and edit `.base` files, which define database-like views over your vault notes. Think Notion databases, but stored as plain files in your Obsidian vault.

**json-canvas** lets Claude create and edit `.canvas` files for visual canvases, mind maps, and flowcharts. I've used this to generate architecture diagrams directly from code — Claude reads the codebase, understands the component relationships, and produces a Canvas file I can open in Obsidian.

Installation means dropping the repo contents into a `.claude` folder at the root of your Obsidian vault:

```bash
git clone https://github.com/kepano/obsidian-skills .claude
```

What makes this repo uniquely valuable is bidirectionality. Claude Code doesn't just write to your vault — it reads from it. My vault contains architectural decision records, API documentation, meeting notes, and project specs. With Obsidian Skills installed, Claude Code can pull from all of that context when making development decisions. My [second brain setup with Obsidian and Claude Code](https://www.mejba.me/obsidian-claude-code-second-brain) goes deeper into this workflow if you want the full picture.

The combination of Obsidian Skills + Claude Mem is particularly powerful. Claude Mem handles session-to-session memory automatically. Obsidian Skills gives Claude access to your long-term, curated knowledge base. Together, they give Claude Code both working memory and reference memory — which is how human developers actually operate.

These two repos expand Claude Code's reach beyond the terminal. But the next two are about going deeper into the ecosystem itself.

---

## The Knowledge Amplifiers

### LightRAG — Knowledge Graphs for Your Codebase

**Repo:** [github.com/hkuds/lightrag](https://github.com/hkuds/lightrag)

LightRAG isn't Claude Code-specific — it's a retrieval-augmented generation framework that integrates graph-based text indexing with dual-level retrieval. But when you connect it to Claude Code via MCP, it solves a problem that even large context windows can't fix: understanding relationships across massive codebases and documentation sets.

The framework, published at EMNLP 2025, uses LLMs to extract entities — names, dates, concepts, modules, functions — and maps the relationships between them into a knowledge graph. Then it offers two retrieval modes:

**Low-level retrieval** finds precise information about specific entities and their direct relationships. "What function handles authentication token refresh?" — that kind of query.

**High-level retrieval** captures broader themes and patterns across your documentation. "What's the overall approach to error handling across the API layer?" — queries that require synthesizing information from multiple sources.

The practical setup involves running a LightRAG server and connecting it via MCP:

```bash
pip install lightrag-hku
lightrag-server --port 9621
```

Then configure the MCP connection in Claude Code to point at `localhost:9621`. The [LightRAG MCP server](https://github.com/desimpkins/daniel-lightrag-mcp) provides 22 tools across four categories: document management, querying, knowledge graph operations, and system management.

As of March 2026, LightRAG integrated OpenSearch as a unified storage backend and added a setup wizard that simplifies the initial configuration significantly.

Where I find this most valuable: onboarding onto existing codebases. I loaded an entire client project — 400+ files, internal documentation, API specs, database schemas — into LightRAG. Claude Code could then query the knowledge graph to understand how components related to each other, rather than trying to hold all 400 files in context simultaneously. The quality of its architectural suggestions improved noticeably because it had structural understanding, not just text proximity.

Is it overkill for a small project? Absolutely. For anything with more than 50 files and meaningful internal documentation, it's a force multiplier.

### Everything Claude Code — The Kitchen Sink (That's Actually Well-Organized)

**Repo:** [github.com/affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code)

Some repos try to do one thing well. This one tries to do everything — and the surprising part is that it mostly succeeds.

Built at the Cerebral Valley x Anthropic Claude Code Hackathon in February 2026, Everything Claude Code is an agent harness performance optimization system. It ships with production-ready agents, skills, hooks, commands, rules, and MCP configurations that took over 10 months of intensive daily use to evolve. The numbers back up the quality: 1,282 tests, 98% coverage, 102 static analysis rules.

What you get out of the box:

- **10 language-specific agents** including TypeScript, Python, Java, Kotlin, and PyTorch reviewers and build resolvers
- **Domain-specific skills** covering everything from documentation lookup to Next.js Turbopack optimization to MCP server patterns
- **Security hardening** with an AgentShield system, attack vector documentation, and sandboxing configurations
- **Three levels of documentation**: a shortform guide for setup, a longform guide covering token optimization and memory persistence, and a security guide covering real CVEs

The installation gives you two paths — plugin marketplace or direct config:

```bash
/plugin marketplace add affaan-m/everything-claude-code
/plugin install everything-claude-code
```

I won't pretend I use every feature in this repo. I don't need the Java build resolver or the Kotlin reviewer. But the TypeScript reviewer catches issues my linter misses, the documentation lookup skill saves me from context-switching to browser tabs, and the security configurations give me peace of mind when running Claude Code on client projects.

The longform guide alone is worth cloning the repo for. It covers parallelization strategies, memory persistence patterns, and eval frameworks that I haven't seen documented this clearly anywhere else — including Anthropic's own docs.

Think of this repo as a buffet. You don't eat everything. You load your plate with the pieces that match your stack, and you come back later when your needs change.

---

## The Discovery Engine

### Awesome Claude Code — The Community-Curated Index

**Repo:** [github.com/hesreallyhim/awesome-claude-code](https://github.com/hesreallyhim/awesome-claude-code)

I saved this one for near the end deliberately. Every other repo on this list solves a specific problem. Awesome Claude Code solves the meta-problem: how do you find the tools you don't know you need?

With 28,500+ stars and 1,900+ forks, this is the definitive curated list of Claude Code skills, hooks, slash commands, agent orchestrators, applications, and plugins. It's where I found three of the repos on this list — and where I continue to find new tools every week.

The curation model is unusual. Only Claude is allowed to submit PRs to the repo. Humans recommend resources through an automated system, but the actual PR creation and submission is handled by Claude. It's a fitting meta-touch for a Claude Code resource directory.

The categories cover:

- Slash commands and CLAUDE.md configurations
- CLI tools and workflow enhancers
- Skills and plugins (organized by domain)
- Tutorials and learning resources
- Agent orchestration frameworks

I check it every Monday morning. Not because I'm looking for something specific — because the Claude Code ecosystem is moving fast enough that genuinely useful tools appear weekly. The repo I needed last Tuesday didn't exist the Tuesday before.

---

## How to Stack These Repos Without Creating Chaos

Here's what I learned the hard way: installing all nine repos at once is a mistake. They're individually excellent, but layering too many skills, plugins, and MCP servers simultaneously creates context pollution, port conflicts, and plugin initialization races.

My recommended installation order, based on dependencies and impact:

**Week 1: Memory and methodology.**
Install Claude Mem first. Use it for a week. Let it build up session history. Then add Superpowers. The combination of persistent memory + structured development methodology is the foundation everything else builds on.

**Week 2: Design and autonomy.**
Add UI UX Pro Max if you build interfaces. Add GSD if you want autonomous milestone execution. Don't add both in the same session — test each independently first.

**Week 3: External connections.**
Add n8n-MCP if you use n8n. Add Obsidian Skills if you use Obsidian. These are domain-specific — skip them if they don't match your workflow.

**Week 4: Advanced tooling.**
LightRAG for large codebases. Everything Claude Code for language-specific agents and security hardening. Awesome Claude Code as your ongoing discovery feed.

The critical principle: each repo should solve a problem you've actually experienced. Installing a tool because it sounds cool is how you end up with a bloated Claude Code setup that's slower than vanilla. Install it because you hit a wall and need a ladder.

---

## What Changed After Four Weeks

I want to be honest about the results because overpromising is the fastest way to lose credibility.

My session startup time dropped from roughly fifteen minutes of re-explaining context to under two minutes, thanks to Claude Mem. That's real, measurable, and it compounds across every session.

The code quality from Superpowers' TDD approach caught bugs that would have shipped to production. I found three authentication edge cases in one project that my manual testing missed. Test-first development works whether a human or an agent is writing the code — Superpowers just makes it the default behavior.

GSD let me ship a complete user management system — authentication, authorization, role management, audit logging — in a single overnight run. Not perfect on first pass. I spent two hours the next morning fixing edge cases and adjusting the UI. But the structural work was done. Two hours of cleanup versus two days of building — I'll take that trade every time.

UI UX Pro Max's design system generator eliminated the "developer UI" problem that was my biggest embarrassment when showing clients early prototypes. The interfaces still need a real designer's eye for final polish, but the gap between "AI-generated prototype" and "production-ready UI" shrank dramatically.

The combination of all nine repos didn't make me 10x faster at everything. That's a clickbait number and I won't pretend otherwise. But it made me meaningfully faster at the specific bottlenecks that were eating my time: context re-establishment, design quality, autonomous task execution, and workflow automation. For those specific problems, the improvement was closer to 5-8x. And that's with conservative measurement.

---

## The Repo I'm Watching Next

The Claude Code ecosystem is moving at a pace where any "best of" list has a shelf life of maybe six weeks. By the time you read this, there might be a repo that obsoletes two of these. That's the nature of building on a platform that ships major updates monthly.

What I'm watching most closely: the convergence of memory systems, skill frameworks, and autonomous execution into unified platforms. Right now, I'm using Claude Mem for memory, Superpowers for methodology, and GSD for autonomy as three separate tools. The first repo that integrates all three into a cohesive system — where persistent memory informs autonomous planning which executes through structured methodology — will be the one that actually delivers on the 10x promise without the asterisk.

Until then, these nine repos are the best toolkit I've found. Not because each one is perfect. Because together, they fill the gaps that Claude Code's raw intelligence can't fill on its own. Intelligence without memory is amnesia. Intelligence without methodology is chaos. Intelligence without tools is untapped potential.

Your move. Pick the one repo from this list that solves your most painful problem. Install it tonight. Use it for a week. Then come back and tell me I was wrong about the ranking — because I probably am. The best repo is always the one that fixes *your* specific bottleneck, and I can't know what that is from here.

---

## Frequently Asked Questions

### Which Claude Code plugin should I install first?

Start with Claude Mem for persistent memory across sessions. It eliminates the most common pain point — re-explaining your project context every time you start a new session. Once memory is working, add Superpowers for structured development methodology. For the full installation sequence, see the stacking guide above.

### Can I use Superpowers and GSD together?

Yes, but with intention. Superpowers handles per-task methodology (TDD, brainstorming, micro-planning) while GSD manages multi-task orchestration across milestones. Use Superpowers for individual coding sessions and GSD when you want Claude to execute an entire feature autonomously across multiple context windows.

### Does LightRAG work with Claude Code out of the box?

Not directly — you need to run a LightRAG server and connect it via MCP. The setup involves installing lightrag-hku, starting the server on port 9621, and configuring the MCP connection. The LightRAG MCP server provides 22 tools for document management, querying, and knowledge graph operations. Worth the setup for codebases with 50+ files.

### Are these repos free to use?

All nine repositories are open source and free. Claude Mem is AGPL-3.0 licensed, LightRAG is MIT, and most others use MIT or similar permissive licenses. The repos themselves cost nothing — your only expense is the Claude Code API usage they generate through additional tool calls and context injection.

### How do I avoid plugin conflicts when running multiple repos?

Install incrementally — one repo per week — and test each in isolation before combining. Watch for port conflicts (Claude Mem uses port 37777), context window bloat from too many skills loading simultaneously, and plugin initialization order issues. The stacking guide in this article provides a tested four-week rollout sequence.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
