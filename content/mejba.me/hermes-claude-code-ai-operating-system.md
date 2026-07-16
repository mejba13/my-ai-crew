**BRAND:** mejba.me
**TITLE:** Hermes + Claude Code: My Unified AI Operating System
**META TITLE:** Hermes Claude Code Integration: Unified AI OS Setup
**SLUG:** hermes-claude-code-ai-operating-system
**PRIMARY KEYWORD:** Hermes Claude Code integration
**META DESCRIPTION:** I wired Hermes Agent into Claude Code as one shared-memory AI OS. Pantheon personas, Obsidian, cron dreaming, Apollo leads. Real config, honest tradeoffs.
**TAGS:** Claude Code, Hermes Agent, AI Agents, Multi-Agent Systems, AI Workflow

---

The first time my Hermes agent woke me up at 7:14 AM with a morning brief, I almost closed the Telegram notification before reading it. Then the second line caught my eye: *"Yesterday in Claude Code you spent 41 minutes debugging a Vercel KV race condition. I checked the Anthropic skills repo at 03:22 — there's a new caching pattern that maps exactly to what you were trying to solve. Want me to draft a refactor task for Claude Code?"*

It wasn't a generic summary. It wasn't a calendar dump. It was an agent that had been awake while I slept, reading my actual Claude Code session logs from the night before, comparing them against fresh information it had pulled overnight, and offering a specific, traceable next step that referenced *the exact bug I'd been wrestling with at 11 PM*.

That single Telegram message did something three months of "AI productivity stacks" had failed to do. It made me feel like I had an assistant who actually knew what I was working on — not in the marketing-deck sense, but in the "checked your terminal history and your Obsidian notes and the GitHub repo you opened yesterday" sense. The silo was gone.

I want to walk you through how I built this. Not the pitch version. The actual install, the config files, the Pantheon personas I run, the cron jobs that produce the morning brief, the Apollo lead pipeline I bolted on for client work, and the parts that broke or annoyed me along the way. By the end of this post you'll either want to build the same stack or you'll know — with specifics — why it's not worth it for you yet. Both outcomes are wins.

## Why two great AI tools were making me *less* productive

For about four months I was running two AI environments in parallel. Claude Code on my Mac for engineering work — code, repos, deploys, debugging. A separate personal agent (I'd been cycling through OpenClaw, Cursor's background agents, and a homegrown Codex wrapper) for everything else: research, drafting, calendar prep, email triage.

On paper this looked like a sensible separation. In practice it was death by a thousand context switches.

The pattern was always the same. I'd spend an hour in Claude Code working through a bug. Then I'd open the personal agent to ask a question about the same project — what did I decide about that auth flow last week? — and get a blank stare. The personal agent had no memory of the Claude Code session. So I'd retype the context. Then later I'd be back in Claude Code, ask it to remember a research finding I'd surfaced in the personal agent, and get another blank stare. I was the human router between two amnesiac assistants, and the cognitive tax was real.

The fix wasn't a better single tool. The fix was making the two tools share one brain.

That's the bet I made with Hermes. Hermes Agent — the open-source self-improving agent from Nous Research that crossed 60,000 GitHub stars this spring — isn't trying to *replace* Claude Code. It's designed to *wrap* it. Hermes runs as the operating system: it owns the memory layer, the schedulers, the personas, the messaging gateways, and the long-running state. Claude Code runs as one of the most powerful tools inside that OS, handling anything code-shaped. The integration point is shared memory, plus a small bridge that lets Hermes read what Claude Code did and send Claude Code new work.

The result, after about three weeks of tuning, is what I've started calling my AI OS. One mental model. One memory. Two assistants finally pulling in the same direction.

But before I show you the install, you need to understand the architecture — because the install only makes sense if you know what each piece is doing.

## The shared-memory architecture, in plain terms

Hermes and Claude Code talk through three layers. Get the layers right and the rest of this stack is paint-by-numbers. Get them wrong and you'll end up with two agents writing to two different memory stores and you'll be back where you started.

**Layer 1 — Obsidian vault as the canonical knowledge base.** Hermes treats an Obsidian vault as its primary long-term memory. Every meaningful interaction — decisions, learnings, project notes, drafts, finished outputs — lands in a Markdown file in that vault. Crucially, the same vault is the one I point Claude Code at via its filesystem access. Two agents, one source of truth. When Hermes writes a decision note about how I want client invoices structured, Claude Code reads it next time I ask for invoice code. When Claude Code writes a project plan, Hermes references it the next morning.

**Layer 2 — Hermes's own SQLite memory for conversation state.** This is faster than the vault and lives next to the Hermes process. It's where short-term context, recent task queues, and skill execution traces sit. You don't manage this manually — Hermes does the housekeeping. But it matters that it exists, because this is what lets Hermes resume a conversation across days without re-reading the whole vault every time.

**Layer 3 — The Claude OS Bridge.** This is the small but critical piece. Claude Code writes its own session logs and usage data to `~/.claude/projects/...` on disk. The bridge skill in Hermes knows how to read those logs, parse them, and ingest them into Hermes's awareness. That's the trick that lets my morning brief reference what I did in Claude Code last night without me having to summarize anything. Hermes literally reads the same files Claude Code wrote.

Once those three layers are wired up, the silo problem disappears. Anything either agent learns becomes available to the other within minutes. Anything I ask either agent benefits from the full history of both.

If you've read my deep-dive on [why Obsidian fixed Claude Code's biggest weakness](https://www.mejba.me/obsidian-claude-code-persistent-memory), the vault piece will feel familiar — it's the same memory pattern, just extended with a second agent reading from the same well.

That's the theory. Here's the install.

## Step 1 — Install Hermes and pick your model spine

Hermes installs from the terminal in about ten minutes if your machine is already a developer machine. I run it on macOS; the same flow works on Linux. The official repo is `nousresearch/hermes-agent` and the docs live at `hermes-agent.nousresearch.com/docs`.

```bash
# Clone and bootstrap
git clone https://github.com/NousResearch/hermes-agent.git
cd hermes-agent
./install.sh

# Launch the interactive setup
hermes init
```

The interactive setup is the only part that matters here, so slow down. It asks four questions in sequence:

1. **Provider.** OpenAI, Anthropic, OpenRouter, or local via Ollama. I picked OpenRouter because it lets me route different Pantheon personas to different model backends without juggling separate API keys. Routing one persona to GPT-5.5 and another to Claude Opus 4.7 from the same config file is a quality-of-life win that compounds fast.
2. **Default model.** The model Hermes uses when a persona doesn't override it. I run `anthropic/claude-opus-4.7` here for reasoning quality. If you're cost-sensitive, `anthropic/claude-sonnet-4.7` works fine for most of what Hermes does day to day.
3. **Max iterations and compression threshold.** These are the two config knobs that actually matter for behavior. Max iterations caps how many tool calls Hermes will chain before pausing for a human check. I set mine to 60 — enough for ambitious tasks, not so high that a runaway loop bills me $40 before I notice. Compression threshold sets when Hermes summarizes older context to fit in the window. 0.8 is the sweet spot I landed on after a week of testing. Lower than that and Hermes loses too much nuance; higher and it bloats the context until model latency degrades.
4. **Session reset mode.** Three options: `never`, `daily`, or `per-task`. I use `daily` — Hermes resets short-term state at midnight but keeps everything in the Obsidian vault. This gives the morning brief a clean conversational starting point without amnesia about who I am.

Once setup completes, you have a working Hermes CLI. You can already chat with it. But the magic doesn't happen until you wire the gateways.

## Step 2 — Wire Telegram so the AI lives in your pocket

A terminal-only assistant is half an assistant. The whole point of Hermes is that it follows you around — desk, kitchen, phone in line at the coffee shop. Telegram is how it does that.

The Telegram setup is twenty minutes the first time, two minutes every time after. Open Telegram, search `@BotFather`, run `/newbot`, give it a name, copy the API token. Then back in your Hermes config:

```bash
hermes gateway add telegram
# Paste your bot token
# Enter your Telegram user ID (use @userinfobot to find it)
# Enable: yes
```

The user ID part is the one people skip and regret. Hermes lets you whitelist exactly which Telegram accounts can talk to your bot. If you don't whitelist your own ID, the bot is open to anyone who finds it — including the entire spam-bot economy of the internet. Set it. Set it twice. Then run the gateway in foreground for the first session to confirm:

```bash
hermes gateway start telegram --foreground
```

Send the bot a "hi." It should reply within a few seconds. Once you see the round trip, kill it, restart it as a background service, and you're done. From this point on, anything Hermes can do in the terminal it can do from your phone.

I genuinely did not realize how much of my AI use was getting bottlenecked by "I'd have to open my laptop for that" until the bottleneck disappeared.

## Step 3 — Build your Pantheon

This is the part of Hermes that took me longest to understand and then became my favorite feature. Pantheon is Hermes's persona system. You define multiple named AI characters — each with a specific system prompt, a specific model, and a specific job — and Hermes routes incoming work to whichever persona fits best, or you can call them by name.

I run four right now. The names are mythological because Nous Research leaned into the theme and I found the metaphor genuinely helpful for keeping their roles distinct.

**Philosopher** — runs on Opus 4.7. I use Philosopher for strategic decisions. "Should I take this client?" "How should I price this engagement?" "Is this feature actually worth building?" Its system prompt tells it to slow down, surface assumptions, name tradeoffs, and never give the answer in the first paragraph. When I'm tempted to make a fast decision I shouldn't, Philosopher is the friction layer.

**Researcher** — runs on a model with web access (I have it pointed at Perplexity's API for this). Researcher's job is exactly what it sounds like: pull recent information on a named topic, cross-reference at least three sources, and produce a brief in a fixed structure (claim, evidence, counter-evidence, confidence level). This is what generates the "what's new in your stack" portion of my morning brief.

**Mercury** — runs on Sonnet 4.7 for cost reasons. Mercury handles fast operational tasks: drafting reply emails, summarizing meeting transcripts, generating quick first-pass copy. Anything where speed matters more than depth.

**Labyrinth** — the codename I gave to the persona that orchestrates Claude Code. Labyrinth's system prompt is short and surgical: read the task, decide if it's code-shaped, if yes delegate to Claude Code via the bridge skill with a precisely-written task brief, monitor the run, and report back. Labyrinth is the persona that makes Hermes feel like an engineering manager rather than just a writer.

Each persona is a tiny YAML file in `~/.hermes/pantheon/`. The format is roughly:

```yaml
name: Philosopher
model: anthropic/claude-opus-4.7
system_prompt: |
  You are Philosopher. You are slow on purpose.
  Surface assumptions before answering...
tools:
  - vault.read
  - vault.write
  - web.search
```

The thing I underestimated about Pantheon is how much *cognitive offload* this creates. I used to context-switch between "I need a careful answer" and "I need a fast answer" by adjusting my prompts. Now I just @-mention the persona in Telegram. Philosopher gets the careful tone for free. Mercury skips the preamble and gets to the point. The personas are doing pre-loaded prompt engineering that I would otherwise be paying for in tokens and friction every single message.

If you've ever set up sub-agents in [Claude Code's agent teams workflow](https://www.mejba.me/claude-agent-teams-guide), Pantheon will feel like the same idea promoted to OS-level — and shared across both Hermes and Claude Code, not locked inside one tool.

## Step 4 — Point Hermes at your Obsidian vault

This is the step that turns Hermes from "neat terminal toy" into "agent with a memory longer than your own."

Hermes ships with a built-in Obsidian integration. The skill is called `vault` and it does three things: read notes by path, write new notes, and full-text search the vault. The config is one line:

```bash
hermes config set vault.path "/Users/yourname/Documents/Obsidian/MyVault"
```

If you're new to Obsidian, the [Karpathy-inspired RAG knowledge base setup I wrote about](https://www.mejba.me/karpathy-obsidian-rag-knowledge-base) is a faster on-ramp than the official Obsidian docs and gets you exactly the folder structure Hermes wants.

A few details that bit me the first time:

- **Folder structure matters.** Hermes is smarter when your vault has a logical structure. I run six top-level folders: `01-projects`, `02-clients`, `03-decisions`, `04-people`, `05-areas`, `06-archive`. When Philosopher writes a strategy note it lands in `03-decisions` automatically because the persona's system prompt tells it where decisions live. That sounds trivial; it's why retrieval actually works.
- **Pick your daily-note convention and stick to it.** I use `daily/YYYY-MM-DD.md`. Hermes uses this for the morning brief output. If your daily notes are scattered, the brief gets weird.
- **Don't sync the vault to a flaky cloud while Hermes is writing.** I had a fun afternoon debugging "missing notes" that turned out to be iCloud's sync racing Hermes's writes. Pause sync during heavy Hermes use, or move the vault to a non-synced location and snapshot it elsewhere.

Once the vault is wired, the Obsidian piece is essentially done. From here on, every meaningful Hermes interaction is depositing material into a knowledge base that survives sessions, model changes, even Hermes upgrades. That's the long-term memory that makes the rest of this stack feel like more than the sum of its parts.

## Step 5 — The Claude OS Bridge: where the silo actually breaks

If steps 1–4 set up Hermes, step 5 is where Hermes and Claude Code stop being two tools and start being one OS. This is the integration most write-ups gloss over, so I'm going to go slow.

Claude Code, since the 2.x line, writes session data, usage data, and conversation logs to `~/.claude/projects/<project-slug>/`. Inside each project folder you'll find a `memory/` directory, a `sessions/` directory, and a usage file. This is the data Hermes wants.

The Claude OS Bridge is a Hermes skill — install it from the skills hub or clone it manually:

```bash
hermes skill install claude-os-bridge
hermes skill enable claude-os-bridge
hermes config set claude.projects_dir "/Users/yourname/.claude/projects"
```

Once enabled, the bridge gives Hermes three new capabilities:

1. **Read recent Claude Code sessions.** Hermes can pull the last N sessions across all projects, summarize what was worked on, and reference specific tool calls or files touched. This is the data my morning brief uses.
2. **Inspect Claude Code memory files.** Hermes can read `CLAUDE.md` files across projects so its understanding of each codebase stays current. If I update a `CLAUDE.md` while working, Hermes picks it up on the next cron pass.
3. **Dispatch tasks back into Claude Code.** This is the closing of the loop. Hermes (specifically my Labyrinth persona) can write a task brief to a known queue file and trigger a fresh Claude Code session to pick it up. The hand-off is async — Hermes doesn't wait — but the result lands back in the vault when Claude Code is done.

The first time the bridge worked end to end, I tested it with a deliberately small task: "Hermes, look at what I did in the `client-portal` project yesterday, identify the most obvious test gap, and ask Claude Code to write a test for it." Forty-eight seconds later I had a Telegram message confirming Hermes had read three sessions, identified that the `invoices.ts` module had no failure-path tests, written a precise task brief, and queued it for Claude Code. Eleven minutes after that, a new commit appeared on the project branch. Tests for the failure path. Green build.

That's the experience this stack is built to deliver. And it's also the moment where I had to stop and think hard about safety — because an agent that can read your code, write new code, and trigger your other agent to run that code is exactly the kind of system that needs sober guardrails. I'll come back to that.

For a deeper look at how I let Claude Code automate higher-stakes work without losing sleep, my [auto-research strategy post](https://www.mejba.me/auto-research-claude-code-strategy) covers the constraint pattern I reuse here.

## Step 6 — Cron jobs: the overnight dreaming layer

This is the step that turns the stack from "useful" to "feels alive."

Hermes ships with a built-in scheduler. You can register tasks to run on cron expressions, with full access to every skill and persona. Mine runs three jobs.

**Job 1 — Nightly dream (00:30).** Hermes wakes up at 00:30 every night, reads the day's vault entries plus the day's Claude Code session logs, and writes a "dream note" into `archive/dreams/`. The dream is structured: what I worked on, what got finished, what got stuck, what patterns it noticed across the day. This is where the self-improvement actually happens — Hermes is summarizing my own behavior back to me in a way I'd never have the discipline to do manually.

```yaml
# ~/.hermes/cron/nightly-dream.yml
schedule: "30 0 * * *"
persona: Philosopher
prompt: |
  Read today's daily note and today's Claude Code sessions.
  Produce a dream entry in archive/dreams/{{date}}.md following
  the dream template. Be concise. Surface 1-2 patterns.
```

**Job 2 — Research sweep (05:00).** Researcher wakes up at 5 AM and pulls fresh information on three rolling topics I keep in a `config/watch-topics.md` file. Right now those are: AI agent frameworks, Claude Code release notes, Vercel runtime changes. Results land as a single morning research brief in `daily/{{date}}-research.md`.

**Job 3 — Morning brief (07:00).** This is the one that pings my Telegram. The persona is Philosopher again, because I want the morning brief to feel like a thoughtful chief of staff, not a notification spam machine. The job reads last night's dream, this morning's research brief, today's calendar, and any open tasks, and produces a 250-word brief sent via the Telegram gateway. That's the message that woke me up with the Vercel KV insight.

I tuned the timing carefully. Between 00:30 and 07:00 there are roughly six hours of "agent work" happening while I sleep — and because each job runs in sequence and dumps into the vault, the morning brief has rich material to draw from without me having to do anything.

This is the piece people mean when they talk about [Claude Code's auto-dream memory pattern](https://www.mejba.me/claude-code-autodream-memory-system) — except I've moved the schedule out of Claude Code and into Hermes, which is the right layer for it. Claude Code is great at *doing*; Hermes is great at *remembering and noticing*. The cron jobs put the noticing on autopilot.

## Step 7 — Apollo: where the AI OS starts paying for itself

Up to this point everything I've described is about personal productivity. Step 7 is where the same stack starts producing client revenue, and it's the step that pushed Hermes from "neat side project" into "I genuinely cannot work without this."

Apollo.io is a B2B contact database — 275M+ contacts according to Apollo's 2026 marketing — plus a set of sales engagement features. The API gives you programmatic access to leads, intent signals, and campaign actions. As of the Q1 2026 pricing refresh, the relevant plans are Free, Basic ($49/mo), Professional ($79/mo), and Organization ($119+/mo). Full API access lives at the Organization tier and above. Worth verifying current numbers on Apollo's pricing page before you commit — Apollo's credit math has bitten more than one person who didn't read the fine print, and the warmly/lindy/cloudtalk pricing breakdowns are the honest external reads.

I integrated Apollo via a Hermes skill called `apollo-leads`. The skill exposes three actions: `search`, `score_intent`, and `draft_sequence`. Researcher uses them like this:

1. I drop a target ICP definition into `02-clients/icp/<icp-name>.md` — industry, headcount, role titles, geo, signals I care about.
2. A weekly cron job (Sunday 22:00) tells Researcher to pull a fresh batch of 50 matches from Apollo, score each by intent, and write a ranked prospecting brief into `02-clients/briefs/{{date}}.md`.
3. Mercury reads the top 10 from the brief and drafts a personalized outreach sequence for each. Those drafts land in `02-clients/outbound-drafts/`.
4. I review on Monday morning over coffee. Approved drafts get sent manually. The whole pipeline produces about an hour of high-leverage outbound from maybe ten minutes of my actual time.

This is the workflow that turned the AI OS into a thing I'd pay for even if it were a paid product. The fact that it's open source and runs on my own hardware against my own API keys is the icing.

A word of caution: do not turn this on without a clear ICP. Apollo's API is happy to burn through credits searching the entire B2B universe if you ask it imprecise questions. The most expensive bug I had in the first week was Researcher running a poorly-scoped search every night for a week and chewing through about 4,000 credits before I noticed. Cron + API + vague prompt is a wallet hazard. Tight ICPs, tight prompts, tight cron schedules. Always.

## Step 8 — Gmail and Calendar with brakes on

The last integration is the one that most rewards being paranoid. Hermes can connect to Gmail and Google Calendar through Zapier's MCP gateway — and yes, the principle here is the same whether you call it Zapier MCP, Zapier AI Actions, or whatever the latest branding is. The point is granular action scoping with least-privilege.

I gave Hermes exactly three Gmail capabilities and zero more: read inbox, create draft, search threads. **Not send.** Never send. There is no Send Email action wired up anywhere in my Hermes config and there isn't going to be. Hermes can compose the most beautiful reply in the world; the human in the loop hits the actual send button. Always.

Calendar is similar: read events, propose events (which creates a tentative entry), find free slots. Not delete. Not modify-without-confirmation. Not invite-others-without-approval.

This is the least-privilege model in practice. The agent gets enough capability to do useful work — Mercury can read my inbox each morning, surface threads that look like they need attention, draft replies, propose meeting slots based on my calendar — and zero capability to do irreversible work without me explicitly approving the action.

I want to be honest about why this matters. Most AI agent demos you see online disable safety scopes to make the demo flashier. "Watch the agent send the email!" But real workflows you run for months break in ways that demos don't. A prompt injection in a malicious email. A hallucinated recipient. A model deciding to "be helpful" with an action you wouldn't have authorized. Least-privilege is not paranoia. It's the difference between an AI OS you trust enough to leave running while you sleep and one you don't.

Combine the Telegram whitelist (Step 2), the read-only Claude OS Bridge defaults (Step 5), and the draft-only Gmail scope (Step 8), and you have a stack that can do meaningful work autonomously without ever doing irreversible work autonomously. That's the line I won't cross, and it's the line I'd recommend everyone draw before they start playing with this stack.

## Where this stack actually wins — and where it doesn't

I've been running this AI OS as my daily driver for about ten weeks now. Some honest observations.

**Where Hermes + Claude Code clearly wins:**

- **Cross-day continuity.** The morning brief reading last night's Claude Code sessions is the single feature that delivered the most subjective lift. The Groundhog Day amnesia of single-tool AI is just gone.
- **Cognitive offload via Pantheon.** I no longer think about prompt structure mid-day. Mercury, Philosopher, Researcher, and Labyrinth have absorbed that work. I think about *which persona*, not *how do I phrase it*.
- **Autonomous overnight work.** The cron + Apollo + Researcher combination produces real artifacts while I sleep. Briefs, drafts, dreams, leads. Not all of it is great. Enough of it is great that the marginal cost of the rest is fine.
- **One memory, many entry points.** Whether I'm in Telegram, Claude Code, or directly in Obsidian, I'm reading and writing to the same vault. The cognitive simplification is enormous.

**Where it's still rough or wrong-tool-for-the-job:**

- **Setup is real engineering.** This is not a tool you install in five minutes and use. The first weekend was a build weekend. If you're allergic to YAML files, terminal config, and reading docs, this isn't ready for you yet. Pick a polished SaaS instead and check back in six months.
- **Cost can surprise you.** Pantheon makes it really easy to wire Opus 4.7 to a chatty persona, and the cron jobs make it really easy to forget how often those personas are running. My first month of token spend was double what I expected. The fix is per-persona model routing — Mercury on Sonnet, Researcher with a strict context cap, Philosopher reserved for moments that genuinely warrant Opus.
- **Hermes is young.** The 60K-star github velocity is real, but so is the rate of breaking changes. I've had two skill APIs change shape in ten weeks and one cron-config format break across an upgrade. If you can't tolerate occasionally fixing a config file on a Sunday afternoon, this isn't the right stack today.
- **Not a replacement for orchestration frameworks.** If you're building a multi-tenant production system, LangGraph or n8n still win because Hermes is built for *your* agent, not a fleet of customer agents. Hermes is the personal AI OS. LangGraph is the production agent runtime. Different jobs.
- **The "AI dreams" framing is marketing.** What Hermes actually does overnight is run scheduled summarization and research jobs. It is not literally dreaming. Call it what it is — async background work — and you'll set expectations correctly with anyone you describe this to.

**Who should build this stack right now:**

- Solo operators and small teams where one person sets the agent strategy and lives with the consequences.
- Engineers who already use Claude Code and have an Obsidian or markdown-vault muscle memory.
- Anyone whose work is research-and-deciding heavy, not just code-shipping heavy. Pantheon and the morning brief shine here.

**Who should skip for now:**

- Non-technical users. The 8-step install is too sharp an edge.
- Teams that need centralized administration, audit logs, SSO. Hermes is single-user, full stop.
- People expecting a Claude.ai-style UI. The whole stack is terminal-first with messaging gateways. If you want a beautiful web app, this isn't it.

## What I'd build next

If I had another weekend to spend on this stack, the next thing I'd wire is a feedback loop from my actual outputs back into the personas. Right now Pantheon learns slowly — the personas only improve when I edit their system prompts. The version of this I want is one where every approved draft, every rejected draft, every "actually re-do this differently" message I send becomes structured training signal for the persona that produced the original. Hermes's learning loop hints in this direction but doesn't quite close it yet for custom personas. The minute it does, this stack stops being a productivity layer and starts being a personal model.

The other thing I'd add is a second human-in-the-loop checkpoint for any Apollo action that costs more than 100 credits in one call. The "$40 burned overnight" failure mode is the only one in this stack that hurts when it happens, and a tiny rate-limit-with-approval gate would eliminate it.

But here's the bigger arc this stack pointed me at. The interesting AI tooling of 2026 isn't going to be a better chatbot or a better coding agent. It's going to be the layer that connects the chatbots and the coding agents and the schedulers and the messaging gateways into one shared-memory operating system. The pieces already exist. Hermes is the most ambitious open-source attempt to bolt them together I've used. The fact that two months ago I would have written off "personal AI OS" as a buzzword and now I genuinely cannot work without one tells me the category is real, even if the tools are early.

If you take one thing from this post, take this: the silo between your AI assistants is a tax you're paying every day, even if you've stopped noticing it. The fix isn't a better single tool. The fix is shared memory. Whether you build that with Hermes, with a homegrown setup, or with whatever ships next — pick a memory layer, point every agent you use at it, and watch the cognitive load you've been quietly carrying just dissolve.

That Telegram notification at 7:14 AM didn't feel like an agent talking. It felt like the first morning I'd ever woken up with a real assistant.

## Frequently Asked Questions

### What is the Hermes Claude Code integration actually doing?
The Hermes Claude Code integration gives both agents a shared memory layer so they stop operating in silos. Hermes owns scheduling, personas, and long-term memory in an Obsidian vault, while Claude Code handles code execution. The Claude OS Bridge skill lets Hermes read Claude Code's session logs and dispatch tasks back into Claude Code. For the full architecture walkthrough, see the shared-memory architecture section above.

### Do I need Claude Code to use Hermes?
No. Hermes runs as a standalone personal AI agent without Claude Code. But pairing the two — Hermes for OS-level memory and scheduling, Claude Code for code work — is what produces the unified workflow described in this post. If you don't write code, the Pantheon, vault, and cron pieces still work as a standalone stack.

### How much does the full Hermes + Claude Code + Apollo stack cost per month?
Realistic monthly cost for a solo operator: $20–60 for Claude Code (Anthropic), $0–80 for OpenRouter or Anthropic API usage by Hermes Pantheon personas (varies wildly with cron volume), and $49–119 for Apollo depending on tier. Expect $100–250/month all-in once you tune persona-to-model routing. Untuned, expect double.

### Is Hermes safe to leave running while I sleep?
Yes, if you configure least-privilege scopes. Whitelist your Telegram user ID, keep Gmail set to draft-only with no send permission, use read-only defaults on the Claude OS Bridge, and rate-limit any paid API skills like Apollo. The full safety configuration is covered in step 8 above.

### How is Hermes different from LangGraph, AutoGen, or n8n?
Hermes is built for one user running one personal agent stack with shared memory, scheduling, and messaging gateways. LangGraph is a production multi-agent orchestration framework with state machines. AutoGen is a research framework for multi-agent conversations. n8n is a workflow automation platform. They overlap on automation but solve different problems. Use Hermes for personal AI OS, LangGraph for production agent products, n8n for cross-app no-code workflows.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
