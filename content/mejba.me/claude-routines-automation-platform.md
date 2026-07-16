**BRAND:** mejba.me
**TITLE:** Claude Routines: I Replaced My N8N Workflows in an Afternoon
**META TITLE:** Claude Routines Review: Anthropic's N8N Killer Tested
**SLUG:** claude-routines-automation-platform
**PRIMARY KEYWORD:** Claude Routines
**SECONDARY KEYWORDS:** Claude Code automation, N8N alternative, AI workflow automation
**META DESCRIPTION:** I tested Claude Routines against my N8N stack. Here's what Anthropic's new automation platform gets right, where it falls short, and who should switch now.
**TAGS:** Claude Code, Automation, AI Agents, N8N, Workflow
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know when Claude Routines beats N8N/Make.com, how to set up their first scheduled AI workflow in under 10 minutes, and which existing automations they should migrate first.

---

My N8N instance has 47 active workflows. I know because I just counted them, reluctantly, after spending a Saturday afternoon with Anthropic's new Routines feature and realizing maybe half of those workflows don't need to exist anymore.

That's the part nobody is saying out loud yet. When Anthropic launched Claude Routines on April 14, 2026, most of the coverage framed it as "Claude Code can now run on a schedule." Accurate, but boring. What's actually happening is bigger: Anthropic quietly shipped a direct competitor to N8N, Make.com, and Zapier — and the switch from "drag these 18 nodes together" to "write what you want in English" is not a small upgrade. It's a different category of tool.

I've been running automation platforms for years. Zapier in 2020. Make.com (back when it was Integromat) in 2022. Then N8N self-hosted on a $12 Hetzner VPS for the last eighteen months. I know the pain of staring at a canvas at 1 AM trying to figure out which node is silently dropping my payload. So when a friend DM'd me a screenshot of a routine that summarizes his Linear bugs, drafts a fix, and opens a draft PR — all before he wakes up — I closed the tab I was working in and spent the rest of the day testing this thing.

Here's what I found. The wins, the cracks, the surprises, and the specific workflows you should move over first.

## What Claude Routines Actually Is (Beyond the Marketing)

Strip the press release language and Routines is three things bolted together: a **scheduler**, a **trigger router**, and an **isolated Claude Code runtime**. You define a routine once — prompt, repo, connectors, trigger — and Anthropic runs it in a cloud container on whatever cadence or event you specify. Your Mac doesn't need to be open. No self-hosted runner. No webhook plumbing. No `pm2` keeping a Node process alive.

The triggers, straight from [Claude Code's routine docs](https://code.claude.com/docs/en/routines), are the three you'd expect: **scheduled** (cron-style cadence), **API** (HTTP POST to a per-routine endpoint with a bearer token), and **GitHub** (repo events like PRs, pushes, issues, workflow runs). Webhooks, essentially, with good defaults.

The configuration is flat. A routine has:

- A name
- A description written in plain English (this is the SOP — it becomes the prompt)
- A repository (or none, if you're doing pure connector work)
- A model choice (I've been using Opus 4.6 with the 1M context window for the harder ones, Sonnet for simple triage)
- A cloud environment with your API keys and env vars baked in
- At least one trigger
- OAuth connectors (Gmail, Slack, Linear, Google Drive, GitHub, and more landing weekly)

That's it. No nodes. No canvases. No "connect output of Filter to input of HTTP Request." You describe what you want, attach the services it needs access to, and tell it when to run.

If you've built even one N8N workflow with a Gmail trigger → filter → OpenAI node → Slack message, you're already seeing why this is interesting. We'll get to the direct comparison in a minute. First, let me show you the exact routine that convinced me.

## The Mailbox Routine That Sold Me

Anthropic's demo example is a morning email triage. I thought it sounded toylike. Every SaaS shows off an email demo. Then I built a harder version of it and watched it run.

Here's what I gave it, written exactly like this in the description field:

> "Every weekday at 7:30 AM, check my Gmail for unread emails from the last 16 hours. For each one, classify it as: client, prospect, newsletter, internal, or noise. For clients and prospects, draft a reply in Gmail drafts (do not send) matching my writing style from my sent folder. Skip newsletters and noise entirely. After processing, post a single Slack message to #morning-brief summarizing: number of emails processed, number of drafts created, and any email flagged as urgent based on the sender or subject language."

I picked Opus 4.6. Attached Gmail and Slack OAuth. Set the schedule. Hit "run now" to test before committing to the cron.

Three minutes later, Slack lit up. Twelve emails processed. Four drafts in my Gmail draft folder. One flagged as urgent (a client's staging site was down — legit urgent, caught by the subject line). I opened the drafts. They weren't perfect. But they were 80% of the way there, and — this is the part that matters — they sounded like me. Because Claude had read my sent folder to learn the voice.

To build the equivalent in N8N, I'd need: Gmail Trigger node → Loop Over Items → OpenAI Classification node → IF node branching on classification → Gmail: Search for tone reference node → OpenAI Draft node → Gmail: Create Draft node → Aggregate node → Slack: Send Message node. Probably 40 minutes of wiring, two hours of debugging the classification logic, and another hour of writing the tone-matching prompt as a separate sub-workflow.

In Routines, I wrote four sentences.

That's the real pitch. Not "AI automation." Not "autonomous agents." It's: **the cognitive overhead of automation just dropped by an order of magnitude.**

But before you cancel your N8N subscription, there's a catch worth understanding. Several, actually.

## The Setup Flow, Step by Step

Let me walk through exactly how a routine gets built, because the docs gloss over a few gotchas I hit.

### Step 1: Name the routine
Use a prefix convention. I'm using `[triage]`, `[report]`, `[agent]`, `[ops]` to group mine. You'll regret not doing this by routine #8.

### Step 2: Write the description
This is your prompt. Treat it like an SOP you'd give a new hire. Be precise about: what to do, what NOT to do (the "do not send" in my example is critical), what to output, and what tools to use for which step. Vague prompts produce vague automations.

**What I learned the hard way:** if you write "check my email and reply to urgent ones," Claude might actually send replies. The word "reply" in the description, without "draft," is dangerous. Be explicit about read-only vs. write actions.

### Step 3: Select a repository (or skip)
Routines can run with or without a repo. Connector-only routines (Gmail → Slack, Linear → Discord) don't need one. Coding routines (fix a bug, generate a report from repo data) do. You can attach multiple.

### Step 4: Pick a model
- **Opus 4.6 (1M context):** complex reasoning, long documents, multi-step planning. Expensive. Worth it for high-leverage routines.
- **Sonnet 4.6:** classification, triage, standard ops. My default for routines that run more than twice a day.
- **Haiku:** I haven't used it for routines yet — save it for volume-heavy, simple tasks.

### Step 5: Configure the cloud environment
Drop in the API keys and env vars the routine needs. Stripe key, Fireflies token, whatever your connectors don't cover. These live encrypted in the routine config.

### Step 6: Set the trigger
Three options, as covered. You can attach multiple to the same routine — I have one that runs on a schedule AND accepts manual API triggers, so I can force-run it from a Shortcuts button on my phone.

### Step 7: Attach OAuth connectors
Click, authorize, done. Gmail, Slack, Linear, Google Drive, GitHub are first-party. More are being added. This is the part N8N makes you configure per node, per workflow. In Routines, you authorize once and the connector is available to any routine.

### Step 8: Run and test
Hit "Run now." Live logs stream in the UI. You see every tool call, every reasoning step, every output. When it fails — and it will, on the first iteration — the logs tell you exactly why. This is dramatically better than N8N's "Error in node 'HTTP Request4'" which means nothing.

Alright, that's the build flow. Now let's talk about the part that decides whether you should actually switch.

## Claude Routines vs N8N: The Honest Comparison

I've seen a few "Claude Code vs N8N" takes floating around, and most of them are written by people who've touched one of the two for twenty minutes. Here's the comparison from someone running production workflows on both.

| Dimension | Claude Routines | N8N |
|---|---|---|
| **UI** | Natural language description | Drag-and-drop node canvas |
| **Setup time** | Minutes | Hours |
| **Environment** | Managed cloud containers | Cloud, self-hosted, or local |
| **Triggers** | Schedule, API, GitHub events | Schedule, webhook, 400+ node triggers |
| **Integrations** | OAuth connectors (growing) | 1,000+ community nodes |
| **Editing** | Rewrite the description | Adjust nodes, mappings, expressions |
| **Monitoring** | Calendar view + per-run logs | Execution dashboard with per-node data |
| **Cost model** | Per-run token + compute cost | Per-execution (cloud) or self-host free |
| **Versioning** | Description history | Full workflow JSON versioning, git-compatible |
| **Branching logic** | Implicit (Claude decides) | Explicit (IF/Switch nodes) |

**Where Routines wins outright:**
- Anything that requires reasoning, classification, summarization, or drafting
- Workflows where the data shape varies (Claude handles it; N8N makes you schema-wrangle)
- Rapid prototyping — you can build, test, and ship a new routine in ~10 minutes
- Multi-step coding tasks triggered by GitHub events (the "fix the bug while I sleep" pattern)

**Where N8N still wins:**
- Deterministic workflows where you need to guarantee the same output every time
- High-volume, low-complexity ops (moving data between DBs, sync jobs)
- Integrations that don't have first-party connectors yet (still a long list)
- Cost: a $12/month N8N VPS handles 10,000 executions. A Routine burning Opus 4.6 tokens on 10,000 runs is a different bill entirely
- Workflows that need explicit branching you can reason about and debug visually

**The honest take:** Routines isn't replacing N8N. It's replacing the *reasoning layer* of your N8N workflows. The routines I'm migrating first are the ones where I had OpenAI nodes doing classification or drafting — those entire 15-node workflows collapse into a 4-sentence routine. The routines I'm NOT migrating are the pure data plumbing jobs. N8N is still the right tool for moving rows from Airtable to Postgres on a schedule.

## Before you build your first routine

If the "replace the reasoning layer" pattern sounds like your entire stack, you're probably looking at a bigger rebuild than a single routine. That's a pattern I help teams design end-to-end — you can see the kind of work at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD). Otherwise, let's keep going.

## The Five Use Cases Worth Building First

After a week of testing, these are the routines earning their keep. If you're evaluating whether Routines makes sense for you, build one of these first and judge from the result.

### 1. Morning Email Triage
Already covered above. Highest daily-use-to-effort ratio of anything I've built. If your inbox is a source of anxiety, this routine alone is worth the Pro plan.

### 2. Post-Call Proposal Drafts from Fireflies Transcripts
This one changed my week. Fireflies records every client call. Previously, I'd spend 45 minutes the next morning writing up a recap and proposal. Now: Fireflies webhook fires when a call ends → Routine pulls the transcript → Drafts a proposal in my format → Saves to Google Drive → Pings me in Slack.

Time saved per call: 40 minutes. Calls per week: ~6. Math is obvious.

### 3. Contract Signing → Onboarding Cascade
This is a post-sale automation I used to run in Make.com. Now: Stripe webhook → Routine creates client folder in Drive, generates onboarding doc from template + client details, schedules kickoff call via Google Calendar connector, drafts welcome email in Gmail, posts to #new-clients in Slack.

The old Make.com scenario had 23 modules. The routine is one prompt. It's also more reliable because Claude handles the "the client's preferred timezone is in their intake form" logic that I had to build as a custom code module before.

### 4. Hacker News → Slack Morning Brief
Every weekday at 7 AM: scrape HN front page, filter for items relevant to my stack (Claude, Laravel, AI agents, DevOps), summarize each in two sentences with the link, post to #my-morning in Slack. Replaces me opening HN and scrolling for 15 minutes. I still open HN because I enjoy it, but now I know which 5 items actually matter before I do.

### 5. GitHub Issue → Draft PR Overnight
The one everyone's excited about. A routine triggered by a GitHub label (`needs-first-pass`). Claude pulls the issue, opens the repo, attempts a fix, writes tests, opens a draft PR with a note about its confidence level. I review in the morning.

This is not "AI replaces developers." It's "AI does the first 30% so I start the day at hour one instead of hour zero." The PRs need real review. But the ones I've shipped from this pattern — maybe 40% make it to merge with only minor edits — pay for the entire subscription.

## The Parts That Still Feel Rough

I'm not going to pretend this is polished. It's a research preview, and it shows in three places.

### The daily run caps are painful
Pro gets 5 runs per day. Max gets 15. Team/Enterprise gets 25. For a morning triage routine that runs once daily, fine. For a GitHub-triggered routine that might fire 10 times during an active day of PRs? You'll hit the cap before lunch. I'm on Max and I've already been rate-limited twice this week.

This will almost certainly loosen over time. But right now, plan your trigger volume carefully or you'll blow through your quota at 11 AM.

### Cost visibility is vague
N8N self-hosted costs me $12/month, flat. A Routine burning Opus tokens is a variable I can't predict yet. Anthropic shows per-run token usage in the logs, but there's no "you've used $X this month on routines" dashboard. Coming, presumably. Not here yet.

### The "run once to test, then schedule" workflow has a foot-gun
If your routine modifies real state (creates Drive docs, sends Slack messages, opens PRs), "run now" does all of it. There's no dry-run mode. I created three duplicate onboarding folders before I learned to add "if this is a test run (TEST=true in env), only log what you would do but take no actions" to every routine description. This should be a platform feature, not a prompt convention I have to remember.

### Connector depth varies
First-party connectors work well. For anything not on the official list (Notion, Airtable, Stripe, most niche SaaS), you're back to API keys and raw HTTP calls in the prompt. Claude handles it, but it's less elegant than N8N's dedicated nodes.

### The N8N JSON import skill is a tease
Anthropic shipped a specialized skill that converts N8N workflow JSON into routine descriptions. I tested it on five of my workflows. Results: two converted cleanly, two needed heavy edits, one was a mess that did the wrong thing. Useful as a starting point, not a one-click migration.

## How I'm Deciding Which Workflows to Migrate

After a week, I've landed on a simple rule: **if more than 30% of an N8N workflow's value comes from AI/reasoning nodes, it's a Routine candidate.** If it's pure data plumbing, it stays in N8N.

I built a checklist for myself:

- [ ] Does the workflow use an OpenAI/Anthropic node? → Routine candidate
- [ ] Does it require branching based on text classification? → Routine candidate
- [ ] Does it generate content (emails, docs, summaries)? → Routine candidate
- [ ] Is the data shape unpredictable? → Routine candidate
- [ ] Does it run more than 25 times per day? → Keep in N8N (cap issue)
- [ ] Is it pure DB-to-DB sync? → Keep in N8N
- [ ] Does it need sub-second latency? → Keep in N8N (Routines have warm-up overhead)

Running that checklist, I'm migrating 19 of my 47 workflows. Another 6 will become hybrid — N8N handles the trigger and data plumbing, then calls a Routine via its API endpoint for the reasoning step.

## What This Means for the Automation Landscape

Zapier is cooked, long-term. Make.com has more runway because their visual builder is still the best in the category for non-technical users, but they need an AI-first product shipping this year or they get squeezed. N8N survives and thrives because self-hosted + open-source + AI-agent-friendly is a genuinely different value prop.

And Anthropic? They just quietly became an automation platform company without calling themselves one. That's the bigger story. The model lab is building the stack. [Agent SDK](https://www.mejba.me) last year. [Claude Code](https://www.mejba.me) as a coding environment. Skills. Now Routines. Each piece standalone is interesting. Stacked together, they're building an entire platform where the LLM is the execution engine, not just the chat interface.

If you run an automation agency — and a lot of my readers do — this is the moment to re-evaluate the stack you're selling. Clients who used to pay $3K for a custom N8N build might be better served by a $500 Routine setup. The margin math changes. The service offering changes. Ignore this at your peril.

## The Specific 24-Hour Challenge

Here's what I want you to do before you close this tab.

Open Claude Code on the web. Build one routine. Not a complex one. The simplest one you can think of that would save you 5 minutes a day. A morning HN brief. A weekly calendar summary. A "what's in my Gmail that needs a reply" check.

Build it. Run it. Let it run tomorrow morning while you're making coffee.

That moment — when you get the Slack ping before you've opened your laptop, and the work you used to do manually is already done — is when you understand what's actually shifting here. It's not "AI can do my job." It's "the cognitive overhead of setting up automation just collapsed, so I'll automate ten times more of my life than I used to."

I thought I was going to spend Saturday evaluating a new feature. I spent it rebuilding my operating system.

## Frequently Asked Questions

### What is Claude Routines and how does it work?
Claude Routines is Anthropic's new cloud-based automation platform that lets you trigger Claude Code workflows on a schedule, via API, or on GitHub events. You write a plain-English description instead of wiring nodes, attach OAuth connectors, and Anthropic runs it in an isolated cloud container. Launched April 14, 2026 as a research preview for Pro, Max, Team, and Enterprise subscribers.

### How much does Claude Routines cost?
Routines are included in existing Claude Code subscriptions with daily run caps: Pro gets 5 runs/day, Max gets 15, Team/Enterprise get 25. Token and compute costs for each run count against your usage. Anthropic hasn't published a separate Routines pricing tier as of April 2026.

### Can Claude Routines replace N8N or Make.com?
It can replace the reasoning-heavy parts of your automation stack, not the entire thing. Workflows involving classification, drafting, summarization, or flexible data handling are better in Routines. Pure data-plumbing workflows (DB syncs, high-volume simple ops) stay better in N8N or Make. I'm migrating roughly 40% of my N8N workflows.

### What triggers do Claude Routines support?
Three trigger types: scheduled (cron-style cadence), API (HTTP POST to a per-routine endpoint with a bearer token), and GitHub events (pushes, PRs, issues, workflow runs). You can attach multiple triggers to a single routine. Webhooks from other services work via the API trigger pattern.

### Can I import my existing N8N workflows into Claude Routines?
Partially. Anthropic shipped a skill that converts N8N JSON into routine descriptions, but in my testing only 2 of 5 workflows converted cleanly. Treat it as a starting point, not a one-click migration. Simple workflows with clear AI steps convert best; complex multi-branch workflows usually need manual rewrites.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)

---

## Social Distribution Package

**Twitter/X (280 chars):**
Anthropic just quietly became a Zapier/N8N competitor.

Claude Routines launched yesterday. I spent Saturday migrating 19 of my 47 N8N workflows to it.

The honest breakdown — what it replaces, what it doesn't, and the 5 routines worth building first →

**LinkedIn (700 chars):**
Anthropic shipped Claude Routines this week — and the automation platform market just changed.

I've run N8N self-hosted for 18 months. 47 active workflows. After one weekend testing Routines, I'm migrating 19 of them.

The pitch isn't "AI automation." It's that the cognitive overhead of building automation just dropped by an order of magnitude. Four sentences of plain English replace a 15-node N8N canvas.

But it doesn't replace everything. Pure data plumbing stays in N8N. Here's the honest breakdown — what wins, where Routines still falls short, and the exact checklist I used to decide which workflows to migrate.

**Newsletter Snippet:**
Anthropic just launched Claude Routines, and it's a bigger deal than the press coverage suggests — this is a direct competitor to N8N, Make.com, and Zapier. I spent the weekend testing it against my production automation stack and wrote up the honest comparison: which of my 47 workflows I'm migrating, which stay in N8N, and the 5 routines worth building first. If you run automations (or sell them as a service), read this before you touch your stack next week.
