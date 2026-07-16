**BRAND:** mejba.me
**TITLE:** I Built My AI Operating System With Claude Code
**META TITLE:** Build Your AI Operating System With Claude Code (2026)
**SLUG:** build-ai-operating-system-claude-code
**PRIMARY KEYWORD:** AI operating system Claude Code
**META DESCRIPTION:** I built a personal AI operating system with Claude Code — the full blueprint, Three M's, Four C's audit, skills, routines, and a Karpathy-style wiki.
**TAGS:** Claude Code, AI Operating System, Productivity, Skills, Automation

---

For nine years I tried every productivity stack on the market. Notion. ClickUp. Obsidian on top of Notion. Sunsama wired into Calendar. A retainer of seven SaaS subscriptions that all promised to be the "single source of truth" and none of them ever were. The honest truth is that none of those tools actually thought with me — they just stored things I'd already thought.

That changed three months ago when I rebuilt my entire workflow inside a single tool: Claude Code. Not as a coding assistant. As an operating system. A real one — with skills, routines, scheduled cloud agents, a markdown knowledge wiki, and live dashboards that pull data from ClickUp, Stripe, QuickBooks, Fireflies and Slack on demand.

This is the full blueprint. The frameworks I used (the Three M's and the Four C's), the seven data buckets I started with, the exact repo structure, the skills I shipped first, and the audit scoring system I run on myself every Sunday morning to see whether the AI operating system Claude Code is supposed to be is actually behaving like one.

If you've been trying to glue ChatGPT plus Zapier plus seven dashboards together and wondering why you still feel busy — this is what I'd build instead, knowing what I know now.

## Why Productivity Stacks Failed Me in 2026

Here's what nobody told me when I bought my fourth Notion template. The bottleneck in 2026 isn't information storage. It's information *retrieval at the moment of decision*. When a client emails at 4:47 PM asking why deliverable three is two days late, I don't need to "open my second brain." I need an answer in nine seconds.

Traditional productivity tools all share the same fatal flaw — they assume *I* am the engine. The tool stores. I think. I cross-reference. I synthesize. I write the email. The Notion page doesn't draft the reply. The ClickUp task doesn't reconcile against the Stripe invoice. The Fireflies transcript doesn't surface the one decision from a 47-minute call that actually matters this week.

When the first useful agent runtimes shipped — Anthropic dropped the [Claude Agent SDK in late 2025](/anthropic-agent-sdk-guide), then opened up [skills as a first-class primitive](/agent-skills-advanced-claude-code), then released cloud-hosted [routines on April 14, 2026](/automate-seo-checks-with-claude-code-routines) — the cost of building your own custom system collapsed. What used to take a $40K Zapier-plus-Make-plus-three-engineers stack now fits in a single repo of markdown files.

So I deleted half my SaaS stack and started over. The result is what I'm calling an AIOS: an AI operating system that thinks alongside me, not behind me.

## What an AIOS Actually Is (And Isn't)

An operating system has three jobs. Manage resources. Mediate between you and your hardware. Run programs on your behalf when you ask.

An AI operating system does the same thing — but the resources it manages are *cognitive*. Your contexts. Your decisions. Your relationships. Your projects. The hardware it mediates is your brain plus the dozen SaaS APIs your business runs on. The programs it runs are skills, routines, and scheduled tasks that execute whether you're at your desk or asleep.

That's the part people miss. An AIOS isn't a chatbot with memory. It isn't a Notion replacement. It's a runtime that holds your context, owns your connections, exposes your capabilities as named skills, and runs them on a cadence you set.

Four properties separate a real AIOS from a fancy prompt template:

1. **It holds your context across sessions.** No re-explaining who you are.
2. **It connects to live data.** It pulls from your real tools, not stale exports.
3. **It exposes capabilities as named, reusable skills.** Not one-off prompts.
4. **It executes on its own cadence.** Some skills you trigger; some run themselves at 6 AM.

If a system you're building doesn't do all four, you've got a chatbot. Useful — but not an OS.

The good news: Claude Code already gives you the runtime for free. The work is in the architecture you build inside it. Which brings me to the two frameworks that organized everything for me.

## The Three M's: Mindset, Method, Machine

Before any code, any folder structure, any skill — three layers. Skip them and the build collapses in week two. I learned this the hard way after my first attempt turned into 31 disorganized markdown files that even Claude couldn't navigate.

### Mindset

The mindset shift is small but load-bearing: **the AI is not a vending machine. It's a mentor.**

A vending machine takes a coin and gives a snack. You ask, you get. A mentor takes the question, asks you three back, makes you defend your reasoning, and sometimes tells you the question itself is wrong. That's the relationship you want with your AIOS. You don't want a system that always says yes. You want one that says "before we draft this proposal, are you sure pricing tier B fits this client's stage? Their last invoice was $2,400."

This sounds philosophical. It is *deeply* practical. Every skill you write either defaults to "execute the request" or defaults to "interrogate the request first." A mentor system is built on the second mode.

### Method

Method is the playbook for *how* you and the AIOS work together. Mine has four rules:

- **Plan before execute.** Every non-trivial skill returns a plan first. I approve the plan, then it executes.
- **Cite the source.** Every claim it makes is tied to a file, a transcript, or an API response. No vibes.
- **Update the wiki.** Every meaningful decision becomes a markdown file that future sessions can read.
- **Audit on a cadence.** Weekly Four C's score (more on that below). The number doesn't lie.

These rules live in the root `CLAUDE.md` of my AIOS repo. Every Claude Code session reads them automatically.

### Machine

The machine is the actual stack — the IDE, the repo, the skills, the connectors, the cron schedule. We'll build the machine in a minute. The point of the Three M's is that *Mindset and Method come first*. If you go straight to Machine you'll build a faster vending machine. Which is precisely what I did the first time.

<!-- IMAGE: Diagram showing the Three M's stacked as a pyramid — Mindset (foundation), Method (middle), Machine (top). Alt text: "AI operating system Claude Code Three M's framework — Mindset, Method, Machine pyramid". Caption: "The three layers I work through before touching code. Skip Mindset and the system becomes a faster vending machine." -->

## The Four C's: The Real Architecture

Where the Three M's are about *how you think*, the Four C's are about *what you build*. Context. Connections. Capabilities. Cadence. In that exact order. They have a sequential dependency — you cannot skip ahead.

### Context (the foundation)

Context is everything the system knows about you. Your business, your clients, your goals, your tone, your decisions, your constraints. Without context, the AIOS treats every prompt like a first date.

Context lives in markdown. Always markdown. Karpathy was right when he said the LLM-friendly format is plain text in a folder you control — there's a [70x token efficiency gain over RAG and vector databases](/super-skills-claude-code-karpathy-memory) for personal-scale knowledge, and it's easier to debug because you can read the files yourself.

### Connections

Once the system knows who you are, it needs to reach the world. Connections are your APIs and integrations — ClickUp, Google Workspace, Fireflies, Slack, Stripe, QuickBooks. Without connections, the AIOS is a smart notebook. With connections, it can read your live calendar, pull yesterday's revenue, scan this morning's transcripts, and act.

### Capabilities

Capabilities are skills — named, reusable competencies the system can perform. "Draft a daily plan." "Audit my Four C's score." "Generate next week's content calendar from my YouTube comments." Each one is a folder with a `SKILL.md` file. Anthropic's [progressive disclosure pattern](https://code.claude.com/docs/en/skills) means hundreds of skills can sit in your repo and only the relevant one loads into context for any given task.

### Cadence

Cadence is *when* things run. Some skills you trigger ("/daily-plan"). Some run on a cron ("every Monday 6:00 AM, audit last week"). Some live in the cloud and respond to GitHub events. Cadence is what turns a clever assistant into an operating system that works while you sleep.

The sequential dependency is what most people get wrong. You cannot do useful Cadence without Capabilities. You cannot build useful Capabilities without Connections. You cannot use Connections intelligently without Context. So we build them in order. One at a time. No skipping.

This is also where the audit comes in — but I'll save the scoring rubric for the end, after we've actually built the thing.

## Step 1: The Plan Data — Seven Tier-One Buckets

Before any folder, any code, any skill — I sat down and listed every kind of information my work touches. Then I ruthlessly collapsed it into seven buckets. Seven, because that's roughly the limit before the system starts feeling like a maze. Yours might be six or eight. The exercise matters more than the number.

My seven tier-one buckets:

1. **Revenue** — Stripe payments, QuickBooks ledger, MRR, AR aging
2. **Customer** — client list, status, last contact, lifetime value, current deliverables
3. **Calendar** — Google Calendar feed, this week, next two weeks, recurring blocks
4. **Communication** — Gmail threads, Slack DMs, comment notifications worth tracking
5. **Tasks** — ClickUp lists by project, today's list, this week's list, blocked items
6. **Meetings** — Fireflies transcripts, decisions extracted, action items extracted
7. **Knowledge** — the wiki itself: clients, products, decisions, references, lessons

Every skill I write touches at least one of these buckets. Often three or four. The bucket list became the contract: if a question can't be answered from one of these seven, my AIOS isn't allowed to make it up. It either tells me which bucket to fill in, or it asks. No hallucination because the data shape is finite and named.

This sounds boring. It's the single most important step. If you skip it, you'll build skills that pull from random places and your context becomes mush. The buckets force discipline.

## Step 2: VS Code, Claude Code, and the Repo Structure

The machine itself is embarrassingly simple. VS Code as the editor. Claude Code (`npm install -g @anthropic-ai/claude-code`) as the runtime. A single Git repository as the entire OS.

Here is the exact folder structure I use:

```
aios/
├── CLAUDE.md                    # root system prompt: mindset + method
├── .env                          # API keys (gitignored)
├── .gitignore
├── .cloud/                       # cloud-routine configs (synced to Anthropic cloud)
│   └── routines/
├── context/                      # the seven buckets, as markdown
│   ├── revenue.md
│   ├── customers.md
│   ├── calendar.md
│   ├── communication.md
│   ├── tasks.md
│   ├── meetings.md
│   └── knowledge/                # the LLM wiki lives here
│       ├── clients/
│       ├── products/
│       ├── people/
│       └── _index.md
├── decisions/                    # one .md per major decision, dated
│   └── 2026-04-19-pricing-tier-update.md
├── references/                   # static reference: SOPs, brand guides, prompts
│   └── brand-voice-mejba-me.md
├── archives/                     # things older than ~90 days
└── .claude/
    ├── skills/                   # personal skills (local)
    │   ├── onboard/
    │   ├── daily-plan/
    │   ├── four-cs-audit/
    │   ├── linkedin-post/
    │   └── youtube-comment-analysis/
    └── agents/                   # subagents
```

Five rules govern this layout, and I've broken every one of them once and regretted it:

- **Root `CLAUDE.md` is short.** Under 200 lines. It loads on every session — bloat it and you bleed tokens.
- **`context/` files are the buckets.** One file per bucket, max 800 lines each, then split.
- **`decisions/` is append-only.** Never edit a decision file. New decision, new file, dated.
- **`archives/` exists for a reason.** Anything older than ~90 days that's not actively referenced moves there. The system should not re-read your January thoughts every May.
- **`.cloud/` syncs to Anthropic.** Only put cloud routines and the skills you want runnable in cloud sessions there. Local skills stay in `.claude/skills/`.

The `.cloud/` distinction matters. Anthropic's [cloud routines](https://claude.com/blog/introducing-routines-in-claude-code) (research preview opened April 14, 2026) only access *project-level skills committed to the repo*. Personal skills in `~/.claude/skills/` don't travel. If you want a routine to run a skill on Anthropic's infrastructure overnight, that skill must be checked into the repo.

If you've never set up a Claude Code repo before, my [agent teams setup guide](/claude-code-agent-teams-setup-guide) walks through the basics in more depth. From here I'll assume you have `claude` working in a fresh folder.

## Step 3: The Onboarding Skill — Teaching the System Who You Are

The first skill I built does nothing impressive. It interviews me. That's it.

I called it `onboard`. When I run `/onboard`, Claude walks through about 40 questions across the seven buckets — what's my business model, who are my top five clients, what's my hourly rate, what's my brand voice, what does a "good week" look like, what am I deliberately not pursuing, what are my non-negotiables. Every answer goes into the corresponding context file as structured markdown.

Why interview-driven? Because if I sat down to write `customers.md` from scratch, I'd procrastinate for three weeks. Conversation is frictionless. The skill turns me running my mouth into structured context the system can use forever.

Here's the abbreviated `SKILL.md`:

```markdown
---
name: onboard
description: Interview the user across the seven AIOS buckets and populate the context/ folder with structured markdown. Use when the user types /onboard or asks to set up their AIOS for the first time.
---

# Onboarding Skill

You are interviewing the user to populate their AI operating system's context.
Work through the seven buckets in order: Revenue, Customer, Calendar,
Communication, Tasks, Meetings, Knowledge.

For each bucket:
1. Ask 4-7 specific questions, one at a time.
2. After each answer, restate what you heard in one sentence.
3. When the bucket is done, write the markdown file to context/[bucket].md.
4. Show the user the file. Ask if anything's wrong before continuing.

Rules:
- Never invent answers. If the user doesn't know, write "TBD" with a date.
- Cite the source of every fact (e.g., "per user, 2026-04-15").
- Keep each context file under 800 lines. If longer, split.

Output format: structured markdown with H2 sections per topic, bulleted facts.
```

That's it. ~25 lines including frontmatter. The skill loads only when called, costs essentially zero tokens at session start, and produces the foundation everything else depends on. After running `/onboard` once, every future session starts knowing exactly who I am and what I'm working on.

This is also the most under-celebrated benefit of the [skills primitive](/claude-code-agent-skills-guide): you can write a skill whose entire job is to *populate the context that future skills will read*. The system bootstraps itself.

## Step 4: The Connections Layer — Wire Up Your Real Tools

Here's where most personal AIOS builds stall. People imagine they need a custom MCP server for every tool. They don't. In 90% of cases the right answer is a direct API call from a skill, with credentials in `.env`.

I'll explain why I went API-first over MCP, then walk through the actual integrations.

### Why API > MCP for personal-scale connections

MCP servers are wonderful for distributed agent systems where multiple clients need a shared protocol. For a personal AIOS where you control both ends, MCP servers add token overhead — every server's tool schema gets loaded into context whether you use it or not. A direct `curl` or `fetch` call inside a skill costs nothing until the skill runs.

Rule of thumb I landed on: if a tool is used by more than three skills *and* I want it accessible from any session without prompting, write an MCP wrapper. Otherwise, raw API call from inside the relevant skill. For a solo AIOS, that means almost everything stays as raw API.

### The actual integrations I wired

| Tool | Purpose | How I connect |
|------|---------|---------------|
| ClickUp | Tasks, projects | REST API v2, personal token in `.env` |
| Google Workspace | Calendar, Gmail, Drive | Google Workspace CLI + service account |
| Fireflies | Meeting transcripts | GraphQL API, bearer token |
| Slack | Notifications, DMs | Slack Web API, bot token in dedicated AIOS workspace |
| Stripe | Revenue, customer billing | REST API, restricted key (read-only on most resources) |
| QuickBooks | Accounting ledger | OAuth 2.0, refresh token stored encrypted |

### The .env discipline

Every API key in `.env`. `.env` is in `.gitignore`. No exceptions. If you ever pushed a `.env` to GitHub once like I did in 2022 — you know that GitHub's secret scanner will catch you within minutes, your key gets revoked, and you spend a Saturday rotating credentials. Don't be 2022 me.

For Stripe and QuickBooks I use restricted/read-only keys for anything the AIOS does autonomously. Write operations require an interactive session where I approve the action. This isn't paranoia — it's the same principle as not giving a junior engineer prod write access on day one.

### Dedicated AIOS accounts

This is the part that took me longest to figure out. For Slack, Fireflies, and (where possible) Google Workspace, I created a *dedicated service account* used only by the AIOS. Why? Because the moment you let your AIOS post to Slack as you, every Slack notification routes back through your audit log as actions you took. That's a mess for accountability and a worse mess if you ever need to debug whether *you* sent the message or your AIOS did.

The dedicated account is named "Mejba (AIOS)" and has a clearly different avatar. When it posts in a channel, everyone knows it's the AIOS. When I post, it's me. Compliance teams and future-you both thank present-you.

For more depth on locking down agent credentials and webhooks, my [secure AI agent onboarding guide](/secure-ai-agent-onboarding-guide) covers the threat model in detail.

## Step 5: Capabilities — The Skills That Actually Earn Their Keep

This is the section that took me the longest in the actual build, and the one most people ask me about. Skills are where the AIOS stops being theoretical and starts saving you hours. I'll show the structure, then walk through five real skills I shipped.

### SKILL.md anatomy

Per Anthropic's [official skills documentation](https://code.claude.com/docs/en/skills), every skill is a folder with at minimum a `SKILL.md` file containing:

```markdown
---
name: kebab-case-name
description: One-sentence trigger description. Claude reads this to decide when to use the skill.
allowed-tools: Read, Write, Bash, WebSearch  # optional
---

# Skill Name

[Instructions Claude follows when this skill loads]

## When to use
## What to do
## Output format
## Constraints
```

Keep `SKILL.md` under 500 lines per Anthropic's recommendation. Need more? Add `REFERENCE.md`, `EXAMPLES.md`, or executable scripts in the same folder. Claude only loads the additional files when it actually needs them — that's the [progressive disclosure pattern](/agent-skills-advanced-claude-code) and it's the reason you can run hundreds of skills without bleeding tokens.

### Skill 1: `/daily-plan`

The skill that earned its keep first. Every morning at 6:30 AM (cron-triggered) it produces a one-page daily plan that:

- Reads `context/calendar.md` and the live Google Calendar feed for today
- Reads `context/tasks.md` and pulls the top 8 ClickUp tasks weighted by deadline + priority
- Scans yesterday's Fireflies transcripts for any committed action items
- Cross-references against `decisions/` for anything I said I'd revisit this week
- Drafts a 3-block plan: deep work block, comms block, admin block
- Outputs it to `daily/2026-04-19.md` and Slacks me a summary at 7:00 AM

Time saved per day: ~20 minutes of "what should I work on first" overhead, with measurably better priority calls because the system actually knows what I committed to in last Tuesday's client call.

### Skill 2: `/linkedin-post`

I write a LinkedIn post most days. The first version of this skill produced generic mush. The current version reads:

- `context/knowledge/brand-voice-mejba-me.md` (my voice rules)
- The last 30 days of LinkedIn posts I actually published (pulled via export)
- Today's `daily/[date].md` plan, plus the most recent decision file
- Optional URL the user passes in

It returns three drafts in three different angles — contrarian, story, tactical — and tags which prior post each draft most resembles in style. I pick one, edit, post. ~12 minutes for a finished post versus the 35 it used to take.

### Skill 3: `/youtube-comment-analysis`

I get a few hundred YouTube comments a week across videos. Most are noise. Some are gold for content ideas. This skill pulls the last 7 days of comments via the YouTube Data API, clusters them into themes, surfaces the three threads with the most engagement, and proposes content angles for each. Runs Sunday night. Tuesday's video idea is usually picked from this output.

### Skill 4: `/slide-deck`

For client proposals. Reads the client's file in `context/knowledge/clients/[slug].md`, the deal scope from `context/customers.md`, and a slide template in `references/`. Outputs a structured outline as markdown, then generates Marp slides. Took a 90-minute deck workflow down to 25.

### Skill 5: `/four-cs-audit`

This is the skill that audits the AIOS itself. We'll dig into it in a moment because it's the one that turns the whole system into a feedback loop. For now: it scores Context, Connections, Capabilities, and Cadence each out of 25 and writes the result to `audits/[date].md`.

### Skill 6: `/level-up`

Twin to the audit. Reads the latest audit file, identifies the lowest-scoring dimension, and proposes the three highest-leverage moves to improve that score before next week's audit. This is how the system tells me what to build next.

I have ~22 skills total now. The list grows by 1-3 per week. Some die quickly because they don't earn their keep. That's fine. A skill is a 30-line markdown file — the cost of writing one is so low that "delete and rewrite" beats "carefully plan."

## Step 6: Cadence — Routines, Cron, and the Cloud

Skills you can trigger. Routines run themselves. This is what graduates an AIOS from helpful to autonomous.

### Local vs cloud routines

Two flavors of scheduled execution exist as of May 2026:

- **Local scheduled tasks** — your laptop runs them via cron or a launchd job that invokes `claude` headlessly. Work great if your laptop is on. Don't work if it isn't.
- **Cloud routines** — Anthropic-hosted, opened in research preview April 14, 2026. Run on Anthropic's infrastructure, keep working when your laptop is closed, configured at `claude.ai/code/routines` or via `/schedule`.

Per Anthropic's own [routines docs](https://code.claude.com/docs/en/routines), each routine is a saved Claude Code configuration — a prompt, one or more repos, and a set of connectors. Triggers can be scheduled (cron or natural language like "every Monday 6 AM"), API (HTTP POST with a bearer token), or GitHub events (PRs, releases). Plan limits as of the preview: Pro 5/day, Max 15/day, Team and Enterprise 25/day.

### My current cadence

| Cadence | What runs | Where |
|---------|-----------|-------|
| 6:30 AM daily | `/daily-plan` | Local |
| 8:00 AM Mon | Weekly client status sweep | Cloud routine |
| Hourly 9-6 weekdays | New transcript ingestion (Fireflies) | Local |
| Sunday 7:00 AM | `/four-cs-audit` + `/level-up` | Cloud routine |
| On PR open in `aios` repo | Repo health check | Cloud (GitHub trigger) |
| Every 3 days max | Loop skill (long-running tasks) | Local |

The 3-day loop limit deserves a note. Long-running tasks that loop indefinitely will burn token budget faster than you expect. I cap any loop skill at 3 days max, with a hard exit condition. For genuinely long-horizon work, schedule a fresh routine — don't let one session run for a week.

### Natural-language scheduling

The `/schedule` command takes natural language. "Run /four-cs-audit every Sunday at 7 AM and DM me the result on Slack." That maps to a cron expression behind the scenes. You don't need to memorize cron syntax anymore. (Though knowing `0 7 * * 0` won't hurt.)

### Monitoring and debugging

Every routine writes a run log to `.cloud/runs/[date]/[routine-name].md` (or its cloud equivalent visible in `claude.ai/code/routines`). I review failed runs once a week. The most common failure mode by far is API rate limits — Stripe is the biggest culprit. The fix is almost always "add a 200ms delay between calls" or "batch the request."

If a routine fails three times in a row, I get pinged in Slack. Silent failure is worse than loud failure.

For more on automating recurring work specifically with Claude Code's scheduling layer, my walkthrough on [SEO checks via routines](/automate-seo-checks-with-claude-code-routines) shows the same pattern applied to a single domain.

## The LLM Knowledge Wiki — My Karpathy-Style Second Brain

This is the part of my AIOS that surprised me most. I expected the skills and routines to be the win. What actually changed how I think is the wiki.

In late 2025 Andrej Karpathy published the LLM Wiki gist — a pattern where instead of stuffing notes into a vector database, you maintain plain markdown files in a folder and let the LLM read them directly. [VentureBeat covered the approach](https://venturebeat.com/data/karpathy-shares-llm-knowledge-base-architecture-that-bypasses-rag-with-an) shortly after. The benchmarked claim, repeated across implementations, is roughly **70x more token-efficient than RAG** for personal-scale knowledge bases that fit in a model's context window.

I host my wiki under `context/knowledge/` and view it through Obsidian for the graph view. Claude Code reads the same files directly through the filesystem.

### What goes in the wiki

- **Clients** — one file per client, with all relevant facts, history, decisions, communication norms
- **Products** — every product/service I sell, with positioning, pricing tiers, common objections
- **People** — referrals, vendors, collaborators, with relationship context
- **Lessons** — things I learned the hard way, written so future-me doesn't repeat the mistake
- **Decisions** — every non-trivial decision, dated, with reasoning preserved

### Why markdown beats a vector DB at this scale

Three reasons that matter in daily use:

1. **You can read it.** When something looks wrong, you `cat` the file. With a vector DB you're querying embeddings.
2. **The LLM can rewrite it cleanly.** Karpathy's whole insight was that LLMs are now good enough to *maintain* a wiki, not just query one. Mine reorganizes its own files weekly.
3. **It loads exact text into context.** No embedding similarity hand-waving. The model sees the file and reasons over it.

For deeper coverage of this specific pattern, see [Obsidian + Claude Code as persistent memory](/obsidian-claude-code-persistent-memory) and the [super-skills approach](/super-skills-claude-code-karpathy-memory) that combines Karpathy's wiki with Claude Code's skill primitives.

The wiki took me about three weekends to populate. It's now the most-referenced part of my AIOS and the part I'd lose last.

## Artifacts and Dashboards — Real-Time Pulls

Sometimes I don't want a chat. I want a dashboard. Skills can return Claude artifacts — interactive HTML/JS panels that render in the chat view and refresh on demand by re-running the underlying API calls.

My five most-used dashboards:

- **MRR Pulse** — Stripe revenue last 30/60/90 days, churn rate, top contributing customers
- **AR Aging** — outstanding invoices grouped by 0-30 / 31-60 / 60+ days
- **Client Health** — for each active client: days since last contact, current deliverable status, any open ClickUp blockers
- **Calendar Density** — next 14 days, % time in meetings vs deep work, color-coded
- **Decision Backlog** — every "TBD" or "revisit by X" tag from `decisions/` that's overdue

Each is a skill that fetches live data and renders an artifact. No SaaS dashboard tool. No third-party service. Data lives where it lives; the dashboard is generated on demand. The total cost is measured in API calls, not subscription fees.

## The Daily and Weekly Loop

Here's the rhythm that emerged after about six weeks. I'll show it because the loop is the system. Without it, you have a fancy folder of markdown.

### Daily

| Time | What happens | Trigger |
|------|--------------|---------|
| 6:30 | `/daily-plan` runs, writes `daily/[date].md` | Local cron |
| 7:00 | Slack DM with summary | Routine |
| 7:30 | I read it, edit if needed, commit changes | Manual |
| Throughout day | I trigger skills as needed (`/linkedin-post`, `/slide-deck`, etc) | Manual |
| 5:30 PM | `/end-of-day` skill: log what got done, what didn't, why | Manual |
| Hourly 9-6 | Fireflies transcripts ingest, action items extracted | Local cron |

### Weekly

| Time | What happens | Trigger |
|------|--------------|---------|
| Sun 7:00 AM | `/four-cs-audit` runs, scores the AIOS | Cloud routine |
| Sun 7:15 | `/level-up` reads the audit, proposes 3 improvements | Cloud routine |
| Sun 8:00 | I review the audit + proposals, pick what to build that week | Manual |
| Mon 8:00 | Weekly client status sweep | Cloud routine |
| Fri 4:00 PM | `/weekly-review` skill: summarize the week against goals | Manual |

The system runs whether I'm engaged or not. The audits force me to engage on Sunday morning. That's the whole loop.

## The Four C's Audit Scoring — How I Know It's Actually Working

Every Sunday at 7 AM the `/four-cs-audit` skill runs and gives me a number out of 100. Twenty-five points each for Context, Connections, Capabilities, Cadence. The number is brutal because it should be.

### Scoring rubric (simplified)

**Context (out of 25)**
- All 7 buckets populated and updated within 14 days: 15 pts
- Wiki has ≥1 file per active client/product: 5 pts
- No "TBD" tags older than 30 days: 5 pts

**Connections (out of 25)**
- Each integration tested in last 14 days: 4 pts each (max 20)
- All credentials in `.env`, none in skills: 5 pts

**Capabilities (out of 25)**
- ≥10 working skills: 5 pts
- Each top-5-most-used skill ran successfully ≥3 times this week: 15 pts
- ≥1 new skill shipped in last 14 days: 5 pts

**Cadence (out of 25)**
- ≥1 daily routine running: 5 pts
- ≥1 weekly routine running: 5 pts
- Audit + level-up running automatically: 10 pts
- Zero silent failures (every failure pinged you): 5 pts

### My first audit (real numbers)

When I scored my AIOS the first time — six weeks into the build — it landed at **54.5/100**:

- Context: **18/25** — buckets were populated but the wiki had three "TBD" client files older than a month
- Connections: **16/25** — Stripe and ClickUp were solid, QuickBooks OAuth had been failing for 9 days and I hadn't noticed
- Capabilities: **15.5/25** — 12 skills built, but 4 of them I'd never actually used since week one
- Cadence: **5/25** — I had cron jobs but no proper cloud routines yet, and the silent QuickBooks failure proved my monitoring was broken

54.5 out of 100. After six weeks of work. That number was the most useful piece of feedback the system has ever given me. It told me exactly where to focus week seven (fix monitoring, set up cloud routines, kill the four dead skills).

By week ten I was at 81. The audit is the feedback loop that makes the whole thing improve over time. Without it, I'd have kept building skills that nobody — including me — actually used.

## My Take — The Lessons That Took Me Three Months to Earn

Six weeks in I almost quit. Productivity got *worse* before it got better. I was spending 12 hours a week on the AIOS itself instead of on client work, and the early skills weren't yet earning their keep. This is the productivity dip and it's real. Plan for it.

The shape is roughly:

- **Weeks 1-3:** Net negative. You're building, debugging, populating context, learning Claude Code's quirks.
- **Weeks 4-7:** Break-even. The first few skills start saving real time. The system knows enough about you to be useful.
- **Weeks 8+:** Compounding gains. Each new skill takes less time to build because you're reusing context. Each routine adds margin to your week.

By week ten I was clawing back ~14 hours a week of administrative overhead. By week twelve, ~19. The dip is the entry fee. Pay it on purpose, not by accident.

Three other lessons I'd tattoo on my forearm if I could:

**1. Aim for 30-75% automation per task, not 100%.** Trying to fully automate creative work produces flat output. Trying to fully automate decision-making produces brittle systems. The sweet spot is automating the *grind* parts of a task — research, drafting, formatting, status sync — and keeping the judgment parts human. A 50%-automated workflow run consistently beats a 100%-automated workflow that breaks every third week.

**2. Treat the AIOS as a mentor, not a vending machine.** Already said it. Saying it again. The skills that taught me the most were the ones that pushed back. `/daily-plan` is great because it sometimes refuses to schedule what I asked it to schedule and explains why ("you committed to deep work on the Acme deliverable in Tuesday's call — moving the meeting to that block contradicts that commitment"). A vending machine would have just moved the meeting.

**3. The audit beats the build.** I am genuinely embarrassed by how much I built before I built the audit skill. The audit is what turned the AIOS from a clever toolkit into a system that improves itself. If you build only one skill from this entire post, build the audit.

There's also a meta-lesson worth saying out loud — building this didn't make me more productive overnight. It made me more *honest*. The Sunday score doesn't lie. You can't spin yourself into believing you had a great week when the audit says Cadence is 5/25 and three of your routines silently failed.

That, more than the time saved, is why I'd never go back.

## Frequently Asked Questions

### What does AIOS stand for?
AIOS stands for AI Operating System — a personal runtime built on top of Claude Code that holds your context, connects to your live tools, exposes capabilities as named skills, and runs on a cadence you set. Unlike a chatbot, it manages cognitive resources across sessions and can execute autonomously.

### Do I need to be a developer to build an AIOS in Claude Code?
You need basic comfort with the terminal, Git, and editing markdown files. You do not need to write production code — every skill is a markdown file with a YAML header. If you've shipped a Wordpress site or used VS Code, you have enough background.

### How is this different from Notion AI or ChatGPT with memory?
Three things: Claude Code reads your real filesystem (no upload step), skills run as code with API access (not just text generation), and routines execute on a schedule whether you're at your laptop or not. ChatGPT memory is one of the four pillars (Context). An AIOS is all four.

### How much does running a personal AIOS cost?
Claude Pro at $20/month covers most personal use including 5 cloud routines per day; Max plans extend to 15 routines/day. API costs for connected tools (Stripe, ClickUp, etc.) are typically free at personal volume. Total monthly cost for me runs ~$45-80 depending on routine load.

### How long does it take to build one?
Plan on 30-50 hours over 4-8 weeks of part-time work to reach a useful baseline. Expect a productivity dip in weeks 1-3, break-even by week 5-7, and compounding gains after week 8. The full step-by-step is in the [build sections above](#step-1-the-plan-data--seven-tier-one-buckets).

### Can I run cloud routines on a Free plan?
No — cloud routines require Pro, Max, Team, or Enterprise as of May 2026. The Free plan can still run local skills and use cron-driven scheduling on your own machine.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
