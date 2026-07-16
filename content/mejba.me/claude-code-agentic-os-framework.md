**BRAND:** mejba.me
**TITLE:** Claude Code Agentic OS Framework: How I Built Mine
**META TITLE:** Claude Code Agentic OS Framework: Build Guide 2026
**SLUG:** claude-code-agentic-os-framework
**PRIMARY KEYWORD:** Claude Code agentic OS framework
**META DESCRIPTION:** How to build a Claude Code agentic OS framework that closes the memory, consistency, and access gaps most setups miss. Architecture, skills, and a build path.
**TAGS:** Claude Code, AI Agents, Agentic OS, Obsidian, Automation

---

I spent six months treating Claude Code like a very fast intern. Smart, tireless, occasionally brilliant — and forgetful in the exact moments that cost me the most time. Every session started with me re-explaining the same brand voice, the same folder structure, the same rules I'd written down in three other places. Every skill I built lived in isolation. Every automation I wired up worked perfectly until I needed to hand it to someone who didn't live in a terminal.

Then I stopped building one-off tools and started building a system.

What I'm going to walk through here is the Claude Code agentic OS framework I landed on after tearing down and rebuilding my stack three times in Q1 2026. It's not a product. It's not a plugin. It's an architectural pattern that turns Claude Code from a coding assistant into something closer to a personal operating system — one that remembers, executes consistently, and can be handed off to clients who have never opened a terminal in their lives.

The framing that unlocked it for me was simple: most Claude Code setups fail in the same three places. Memory, consistency, and access. Close all three gaps and the tool stops being a tool. It becomes infrastructure.

---

## The Three Gaps Every Claude Code Setup Has

Before the architecture makes sense, the problem it solves has to be specific. Because if you've been using Claude Code for more than a few weeks, you've probably felt all three of these without naming them.

**Gap one: memory.** Claude Code is stateless between sessions unless you give it somewhere to store what it learns. CLAUDE.md helps, but it's a single file doing the job of a filing cabinet. The moment you need the agent to remember a client's preferences, a project's history, a decision you made three weeks ago — you're back to re-explaining. Most people hear "memory" and assume the answer is a full RAG pipeline with Pinecone or Supabase vector embeddings. For 90% of workflows, that's overkill. You need persistence, not semantic search across a million documents.

**Gap two: consistency.** Ask Claude Code to write a blog post on Monday and you'll get one thing. Ask it the same way on Friday and you'll get something else — different tone, different structure, different footer. The variance isn't the model's fault. It's a prompt engineering problem dressed up as a model problem. Without structured skills that encode *how* you want the work done, every invocation is a fresh coin flip. Skills and automations solve this, but only if they're organized like a real operation, not dumped into a flat `~/.claude/skills/` folder.

**Gap three: access.** This is the gap nobody talks about because technical founders don't feel it. But if you've ever tried to hand Claude Code off to a non-technical teammate, a client, or even your own brain at 10pm when you don't want to type `claude --continue --session-id foo` — you've felt it. The terminal is a moat. For you it's a feature. For everyone else, it's a wall.

The agentic OS framework is what you get when you stop solving these three gaps separately and start solving them together.

---

## What An Agentic OS Actually Is

The phrase "agentic OS" gets thrown around loosely. Here's what I mean by it in this specific context: an architecture where Claude Code is the execution engine, a persistent memory layer gives it continuity, a hierarchical skill system gives it consistency, and a dashboard gives non-terminal users a way in.

Four layers. That's the whole thing.

```
┌─────────────────────────────────────────────┐
│  Dashboard / Command Center (Next.js)       │  ← access
├─────────────────────────────────────────────┤
│  Skills + Automations (local + remote)      │  ← consistency
├─────────────────────────────────────────────┤
│  Memory Layer (Obsidian vault, Markdown)    │  ← memory
├─────────────────────────────────────────────┤
│  Claude Code (engine)                       │  ← execution
└─────────────────────────────────────────────┘
```

The reason this architecture works is that each layer has a single job, and none of them fight each other. Claude Code does what it's already good at — reasoning, writing code, executing multi-step tasks. Obsidian handles persistence because it's already a filesystem of Markdown files, which is exactly the format Claude Code reads and writes natively. Skills and automations turn ad-hoc prompts into reliable workflows. The dashboard wraps all of it in something clickable.

I want to flag the thing that surprised me most while building this: **the dashboard is the piece that changes the math for non-technical users, but it's also the piece that changed how I work.** More on that later. Keep it in mind as we go.

---

## Layer One: Memory Without RAG

Let me start with the piece that caused me the most rework.

I spent two weekends in February building a proper RAG memory layer for Claude Code. Supabase as the vector store, OpenAI embeddings, a custom MCP server that let Claude query it with semantic search. It worked. It was also wildly over-engineered for what I actually needed, which was "remember what we decided last Tuesday."

I tore it out and replaced it with an Obsidian vault. The whole layer took about 40 minutes to set up.

Here's why Obsidian works as an AI memory layer for Claude Code specifically:

- **Obsidian vaults are just folders of Markdown files.** No proprietary format. No database. No sync layer you have to babysit. Claude Code reads and writes Markdown natively, which means the "memory system" is literally just filesystem access.
- **Wiki-style linking (`[[note-name]]`) gives you structure without schema.** Claude can walk links the same way a human would, following threads across notes.
- **It's free, local, and portable.** If I decide to swap Claude Code out for Codex CLI or whatever ships next month, my memory layer comes with me. No vendor lock-in.
- **Obsidian itself renders the vault beautifully for humans.** When I want to review what the agent wrote, I don't need to query a database. I open Obsidian and read.

My vault structure looks roughly like this:

```
~/vault/
├── _claude/
│   ├── CLAUDE.md              # global context, loaded every session
│   ├── session-logs/          # what happened in each run
│   └── decisions/             # why I made specific choices
├── clients/
│   └── [client-name]/
│       ├── brief.md
│       ├── brand-voice.md
│       └── delivered/
├── projects/
│   └── [project-name]/
└── knowledge/
    ├── claude-code-patterns.md
    └── skills-registry.md
```

The critical piece is `_claude/CLAUDE.md`. That file is Claude Code's entry point into the vault — a table of contents for the agent. It tells Claude where client briefs live, where to write session logs, which files hold decisions that should never be contradicted. Every session the agent reads this file first, then walks into whatever part of the vault it needs.

For most people reading this, if you're trying to decide between "build a RAG system" and "set up an Obsidian vault" — start with the vault. You can always add RAG later when you actually hit a scale where semantic search matters. I haven't hit it yet, and I've been running this setup for three months across four brands and 230+ pieces of content. If you want the deeper version of this argument, I've written before about [Obsidian as Claude Code's persistent memory](https://www.mejba.me/obsidian-claude-code-persistent-memory) and [why a flat knowledge vault beats a RAG pipeline for most workflows](https://www.mejba.me/karpathy-obsidian-rag-knowledge-base).

One note before we move on — the memory layer isn't optional in this framework. Without it, the skills layer has nothing to anchor to. Consistency requires something to be consistent *with*. That's the bridge to the next piece.

---

## Layer Two: Skills And Automations, Structured Like An Org Chart

This is the layer where most agentic OS attempts collapse into chaos.

The mistake I see repeatedly — and made myself for a while — is dumping every skill into a flat folder. `~/.claude/skills/post-writer`, `~/.claude/skills/research`, `~/.claude/skills/social-scheduler`, thirty more. Then trying to remember which one does what when you need it six weeks later.

The fix is to stop thinking of skills as scripts and start thinking of them as roles in a team.

Here's the hierarchy I settled on:

**Functions** are the broad domains of work. "Content production." "Research." "Client ops." "Brand monitoring." These aren't skills — they're categories a skill belongs to.

**Skills** are atomic, reusable jobs inside a function. Inside "content production" I have `blog-post-writer`, `image-brief-generator`, `social-distribution-package`. Each skill does one thing well. Each has its own SKILL.md file describing when Claude should invoke it, what inputs it needs, and what it produces.

**Sub-skills** handle the specialist work a parent skill delegates to. `blog-post-writer` has sub-skills for research-phase, voice-matching, SEO-structuring, and self-evaluation. The parent orchestrates; the sub-skills execute.

**Automations** are skills with triggers. Same skill, but now it runs on a schedule or fires automatically when something happens. Scheduled automations are cron-driven. On-demand automations are triggered by a button click from the dashboard or a hook in Claude Code itself.

Claude Code shipped a skill-creator plugin in the official marketplace earlier this year that handles most of the scaffolding. Running `/plugin install skill-creator` from inside Claude Code gives you a guided workflow: describe the skill, test it against real inputs, iterate on the prompt, commit it to your skills folder. The ecosystem around this is huge — the community marketplaces at `tonsofskills.com` and `claudemarketplaces.com` list thousands of plugins and agents as of April 2026, and most of them will happily slot into this hierarchy if you organize them by function.

The reason the hierarchical structure matters isn't aesthetic. It's that Claude Code's own routing logic gets better when the skills are organized. When the agent has to decide which skill to invoke, it's doing that decision inside its context window. A flat folder of 60 skills wastes tokens on disambiguation. A hierarchy of four functions, each with six to eight skills, each with named sub-skills, lets the agent narrow down fast.

If you want more depth on the hierarchy itself, I've broken it down in [my Claude Code agent teams playbook](https://www.mejba.me/claude-code-agent-teams-playbook) and [the advanced agent skills guide](https://www.mejba.me/agent-skills-advanced-claude-code). Both are cluster posts to this one.

---

## Local vs Remote Automation: The Decision That Actually Matters

Once skills exist, the next question is where they run. This is where a lot of builds get confused, because the distinction between local and remote automation isn't about complexity — it's about what the skill needs to touch.

**Local automations** run on your machine. They have access to your filesystem, your installed CLIs, your local Obsidian vault, your git repos, your screen recordings, whatever you've got sitting there. They need you to be logged in. If your laptop is asleep, they're asleep.

**Remote automations** run in the cloud — typically through Claude Routines, which Anthropic shipped as part of the $20/month plan and expanded significantly in the April 2026 update. They run independent of your machine. No filesystem access, no local CLIs, but also no dependency on you being online.

The decision tree I use is literally this question: does this skill need to touch something local? If yes, local. If no, remote. No agonizing.

Concrete examples:

**Local automation candidates:**
- **Deep research workflows** that chain Firecrawl CLI for scraping, Notebook LM for synthesis, and writing the output to an Obsidian note. These need local CLIs and local filesystem — has to be local.
- **Video-to-blog pipelines** where I download a video, run it through a local transcription step, pass the transcript to Claude Code, and save the post to `content/mejba.me/[slug].md`. Files are local. Automation is local.
- **Codebase refactors** where Claude Code is editing actual repository files on disk.
- **Screenshot-based design review** skills that capture a local browser window and feed it to the agent.

**Remote automation candidates:**
- **Daily web search + report** that searches for AI news every morning at 6am, compiles a digest, and pushes it as a Markdown file into a GitHub repo. Nothing local involved. Runs in the cloud. I wake up, `git pull`, read the digest.
- **Scheduled brand monitoring** — search for mentions of a client's brand across the web each hour, log anything new to a Notion database. Pure cloud flow.
- **Newsletter drafting from an RSS feed** every Sunday night, delivered as a draft in Ghost or Beehiiv via API. No local state.
- **Lead research and outreach drafting** — nightly pull from a lead list, research each company, draft a personalized outreach email, leave it in drafts for human review.

The reason this distinction matters so much is cost and reliability. Remote automations cost you nothing when they fail at 3am — they just retry. Local automations that fail at 3am are a dead task until you wake up. But local automations can do things remote ones fundamentally can't, because they're sitting inside your working environment.

I split the framework roughly 60/40 local-to-remote, but that's a function of what I do. If your work is mostly SaaS operations, CRM, email, and cloud-native tools, you'll skew much more remote. I've written separately about [Claude Code's cloud automation channels](https://www.mejba.me/claude-code-cloud-automation-channels) and [the first Claude Routines build I shipped on Opus 4.7](https://www.mejba.me/claude-routines-opus-4-7-first-build) if you want to go deeper on either side.

---

## Layer Four: The Dashboard Is The Whole Point

Here's where I want to pause and make a confession.

When I first heard "build a dashboard on top of Claude Code," my reaction was the engineer's reflex: *why would I put a UI on a CLI tool I use fluently?* The terminal is faster. I can pipe things. I can alias things. A dashboard sounds like extra friction dressed up as accessibility.

That reflex was wrong. Building the dashboard changed how I use Claude Code more than any other piece of the framework.

Here's what I missed. The terminal is optimized for the moment you're *starting* a task. Cold start, full focus, coffee in hand — you type the command, you're off. But the terminal is terrible at the moments between tasks. Checking what the overnight automations produced. Monitoring a long-running research job. Handing a skill invocation to a teammate who doesn't write code. Glancing at what ran yesterday while you're eating lunch. For every one of those moments, a dashboard wins.

What I built is a simple Next.js 15 app that runs locally and exposes five things:

1. **Skill launcher** — every skill in my hierarchy shows up as a button, grouped by function. Click it, fill in a tiny form (client, project, any parameters), hit go. The dashboard shells out to Claude Code with the right invocation.
2. **Upcoming routines** — shows everything scheduled to run in the next 24 hours. I catch mistakes before they fire, not after.
3. **Recent activity** — a feed of what Claude did in the last 48 hours, pulled from the session-logs folder in my Obsidian vault. Each entry links to the full log.
4. **System usage** — token spend per skill per day, per model. Helps me catch automations that are quietly burning Opus tokens when Haiku would do.
5. **Output inbox** — anything Claude produced that's waiting for me to review. Blog drafts, research reports, draft emails.

The dashboard is roughly 800 lines of TypeScript. It's not complicated. What's transformative isn't the code — it's the operational change. When everything has a button, I delegate differently. I delegate more. I delegate to a teammate who could never have used Claude Code from a terminal.

And that last part is where the access gap actually closes. I can point a client at the dashboard, say "click this to generate a draft of your weekly update," and they get value from Claude Code without ever knowing what Claude Code is. That's the shift from "tool" to "infrastructure."

If you're a solo operator, build the dashboard for yourself before you worry about clients. You'll be surprised how much your own behavior changes once the friction drops.

---

## A Practical Build Path You Can Actually Follow

Enough architecture. Here's the sequence I'd run if I were starting over today, calibrated for someone who already uses Claude Code a few times a week.

**Week one: memory.** Install Obsidian (free, obsidian.md). Create a vault. Scaffold the folders I showed above. Write your `_claude/CLAUDE.md` with five sections: who you are, what brands/projects you work on, where files live, how the agent should write, what it must never do. Don't overthink it. You'll rewrite it three times in the first month anyway. Then point Claude Code at the vault — either by launching it from inside the vault directory, or by referencing the vault path in your global CLAUDE.md. Run a few real tasks and watch what the agent writes back.

**Week two: two skills.** Pick the two tasks you do most often. For me it was "write a blog post from a video summary" and "generate a social distribution package from an article." Install the skill-creator plugin via `/plugin` inside Claude Code. Use it to scaffold each skill. Test each skill five times on real inputs. Iterate the prompt until the output is consistent. Commit the skills to your personal skills directory.

Two skills done is enough to know if the framework works for your actual workflow. Don't build more until these are reliable.

**Week three: one automation.** Pick one skill that would benefit from running on a schedule or on-demand. If it needs local files, wire it up as a local cron job calling Claude Code headless. If it doesn't, use Claude Routines to schedule it in the cloud. One automation. Let it run for a week. Tune the schedule. Fix the edge cases.

**Week four: dashboard v0.** Build the dumbest possible version. Five buttons that shell out to Claude Code. No auth, no database, runs on `localhost:3000`. If you're not a frontend person, the frontend-design skill in Claude Code will scaffold the whole thing from a description. My v0 was a single React component. The polish came later.

**Week five onward: extend what earns its place.** Add a skill when a repeated pattern emerges. Add an automation when you notice yourself manually running the same skill on the same schedule. Add a dashboard button when you want to hand something off or stop typing the same invocation. Don't extend for extension's sake. The framework rewards restraint — every skill you add is a prompt-context cost the agent pays on every dispatch decision.

If you want a more detailed walkthrough of the agent-teams side of this, [my Claude Code agent teams setup guide](https://www.mejba.me/claude-code-agent-teams-setup-guide) covers the skill hierarchy in much more depth than I can here.

---

## What Most Build Guides Get Wrong

I want to flag three things I see repeated in other agentic OS write-ups that I think are wrong, or at least incomplete, based on three months of actually running this.

**"You need RAG."** No, you don't. Not at the scale most individual operators or small teams work at. A well-organized Obsidian vault with 500 notes will serve you better than a vector database with a million embeddings, because the organization itself is the retrieval system. RAG earns its keep when you're searching across tens of thousands of documents where you can't predict the structure. If you can predict the structure, structure beats vectors. When I eventually hit the scale where I need semantic search, I'll add it as one more layer. Not before.

**"Build every skill yourself."** The community ecosystem around Claude Code in 2026 is enormous and most people writing guides under-represent it. The official Anthropic marketplace has verified plugins. Community marketplaces list thousands more. Before you write a skill from scratch, search for it. I've swapped out three of my first hand-built skills for community versions that were better. The framework is about your hierarchy and your memory layer — the individual skills can come from anywhere.

**"The dashboard is for non-technical users."** Half true. It's for non-technical users *and* for you during every moment you're not sitting at your keyboard with full focus. I use my own dashboard more than anyone else on my team does. The framing of "accessibility for clients" undersells what a good command-center UI does for the person who built it.

---

## Where This Framework Breaks

Honesty compels me to name the limits.

The framework assumes you have a workflow worth encoding. If you're doing one-off experimental work — research spikes, novel problems, genuinely creative exploration — the overhead of skills and automations will actually slow you down. For creative work I still just open Claude Code with no CLAUDE.md, no skills, no vault, and let it run raw. The agentic OS is for *repeatable* work. Make sure what you're encoding is actually repeatable before you encode it.

It also assumes you'll maintain it. Skills drift. Prompts that were tuned for Opus 4.5 might need retuning for Opus 4.7. Automations break when external APIs change. I spend about two hours a week maintaining the framework itself — mostly updating prompts when model behavior shifts and pruning skills I stopped using. That maintenance cost is real. If you build the framework and walk away for three months, expect to find rot.

And it assumes you trust Claude Code to act on its own. The dashboard makes it easier to trigger skills without thinking. Automations run without supervision. If you're not building review checkpoints into the skills themselves, you will eventually discover the agent shipped something it shouldn't have. The guardrails have to live inside the skill definitions — self-evaluation steps, output review gates, explicit stop conditions. A framework that runs fast without checks is a framework that will embarrass you at scale.

---

## The Shift That Actually Matters

Six months ago my Claude Code usage was a stack of terminal tabs and a CLAUDE.md that kept growing by 40 lines a week. It worked. It also didn't compound. Every new task was a fresh context load, every new client was another file in a folder I rarely opened, every good prompt I wrote lived in my shell history until it scrolled off.

The agentic OS framework changed the compounding math. Memory in the vault means what I learned in March is still usable in April. Skills mean the prompt I tuned for one client becomes the prompt I run for the next twenty. Dashboards mean work can move from "Mejba has to run this himself at 10pm" to "anyone on the team can click this button."

The gap between a powerful tool and useful infrastructure is rarely about the tool. Claude Code has been capable of this since its early releases. What was missing was the architecture around it. Memory. Consistency. Access. Close those three and the math on what you can run solo — or with a tiny team — shifts dramatically.

If you build nothing else from this post, build the Obsidian vault. That one move unlocked more for me than the other three layers combined, because it's the foundation every other layer rests on. The skills need memory to be consistent against. The dashboard needs session logs to display. The automations need a place to write outputs other people can read.

Your Claude Code works fine today. The question is whether the hundredth time you use it will be more valuable than the first — or just the same thing, a hundred times over.

## Frequently Asked Questions

### What is an agentic OS framework for Claude Code?
An agentic OS framework is an architecture that wraps Claude Code in four layers — persistent memory, hierarchical skills, local and remote automations, and a dashboard — so the agent remembers context across sessions, executes tasks consistently, and can be used by non-technical teammates. It turns Claude Code from a CLI tool into operational infrastructure. The full architectural breakdown is in the "What An Agentic OS Actually Is" section above.

### Do I need a RAG pipeline to give Claude Code memory?
No, most workflows don't need RAG. An Obsidian vault of structured Markdown notes, with a `_claude/CLAUDE.md` entry point, gives Claude Code persistent memory without the complexity of vector embeddings. Claude Code reads Markdown natively, so the vault is both storage and retrieval. Add RAG only when you need semantic search across tens of thousands of unstructured documents.

### What's the difference between local and remote Claude Code automations?
Local automations run on your machine and can access the filesystem, local CLIs, and Obsidian vaults — good for research pipelines, codebase work, and anything involving local files. Remote automations run in the cloud through Claude Routines and work independently of your machine — good for scheduled web searches, brand monitoring, and cloud-API workflows. The decision rule: if the skill needs to touch something local, run it locally.

### How are Claude Code skills different from plugins?
Plugins are the distribution format; skills are the content. A plugin is a folder with a `plugin.json` manifest that can bundle one or more skills (SKILL.md files) along with slash commands and hooks. You install a plugin via the `/plugin` command inside Claude Code, which pulls in its skills automatically. As of April 2026, the official Anthropic marketplace and community marketplaces like tonsofskills.com host thousands of plugins and skills.

### Can non-technical users actually use Claude Code through a dashboard?
Yes, and this is the practical point of building one. A local Next.js dashboard with clickable skill buttons, recent activity, and upcoming routines lets anyone trigger Claude Code workflows without touching the terminal. The dashboard shells out to Claude Code behind the scenes — the user sees buttons, clicks, and outputs. It's how I hand skills off to clients and teammates who've never opened a terminal.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
