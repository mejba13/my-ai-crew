**BRAND:** mejba.me
**TITLE:** OpenClaw + Hermes Agent: The Two-Agent System I Run Daily
**META TITLE:** OpenClaw + Hermes Agent: Two-Agent AI Workflow Guide
**SLUG:** openclaw-hermes-multi-agent-workflow
**PRIMARY KEYWORD:** OpenClaw Hermes agent multi-agent workflow
**SECONDARY KEYWORDS:** multi-agent AI system, AI agent backup workflow, shared memory AI agents
**META DESCRIPTION:** How I run OpenClaw and Hermes as a two-agent AI system for backup, supervision, monitoring, and shared memory. Real workflows, real cost breakdowns.
**TAGS:** AI Agents, OpenClaw, Multi-Agent Systems, Hermes Agent, Workflow Guide
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand how to pair OpenClaw and Hermes as complementary agents and implement four specific workflows — backup, supervisor-builder, monitor, and shared memory — to eliminate single points of failure in their AI setup.

---

My OpenClaw instance crashed at 11:43 PM on a Wednesday. Not a graceful failure — a hard crash after an update pushed a broken API key configuration that silently invalidated every active session. I'd been running a content pipeline that was mid-execution. Three articles queued, research pulled, outlines drafted. All of it frozen in a dead process.

Six months ago, that crash would have cost me a full morning of recovery. Reconnecting sessions, re-feeding context, re-running the pipeline from scratch. I've done that dance enough times to know the exact flavor of frustration it produces — the kind where you're not angry at the tool, you're angry at yourself for trusting a single point of failure.

But this time, something different happened. Within four seconds of OpenClaw going dark, my Hermes agent detected the failure. It inspected the error logs, identified the broken API key config, patched the configuration file, and restarted the OpenClaw process. By the time I checked my phone after hearing the Telegram notification, the pipeline was already running again. Total downtime: eleven seconds.

That moment — watching one AI agent diagnose and repair another AI agent while I did nothing — fundamentally changed how I think about building AI workflows. Not because the technology was new, but because I'd finally set it up correctly. Two agents, complementary strengths, working as a unit instead of operating in isolation.

This is the system I'm going to break down for you. Not the theory of multi-agent workflows — the actual implementation I run every day, including the four specific workflow patterns that make OpenClaw and Hermes dramatically more powerful together than either one is alone.

But first, you need to understand why these two tools specifically, and why the pairing matters more than either tool's individual capabilities.

## Why Two Agents Beat One — And Why These Two Specifically

I've tested multi-agent setups before. I wrote about [OpenClaw's multi-agent team configuration](/openclaw-agent-team-configuration) months ago, and the takeaway from that experience was clear: running multiple instances of the same agent creates coordination overhead that often cancels out the productivity gains. Four OpenClaw agents doing different jobs still share the same failure modes, the same update cycles, and the same model dependencies.

The OpenClaw-Hermes pairing works because these two tools were built with fundamentally different design philosophies, and those differences turn out to be complementary in ways that matter practically — not just architecturally.

**OpenClaw is a gateway-first tool.** It wraps an agent around a messaging infrastructure. Connect it to Slack, Telegram, Discord, SMS — it doesn't care. It handles multi-channel orchestration, scheduling, skill libraries (over 44,000 as of the April 2026 update), and persistent session management. When I need an agent that plans complex tasks, coordinates across platforms, and maintains long-running sessions, OpenClaw is the one I reach for. It's the project manager of my AI stack.

**Hermes is an agent-first tool.** Built by Nous Research and released in February 2026, it wraps a gateway around a learning agent. The core differentiator is what Hermes calls its learning loop: execute, evaluate, extract, refine, retrieve. Every task Hermes completes gets evaluated against its own output, the useful patterns get extracted into reusable skills, and those skills get refined over repeated use. The agent literally gets better at tasks the more times it performs them.

That distinction — gateway-first versus agent-first — creates a natural division of labor. OpenClaw excels at the high-level orchestration work: deciding what needs to happen, when, and in what order. Hermes excels at the execution work: doing the actual task quickly, cheaply, and with improving quality over time.

There's a cost dimension here too, and it's significant. OpenClaw runs best on high-capability models. I use Opus 4.6 for my primary OpenClaw instance because the planning and review tasks demand strong reasoning. That's expensive — token costs on Opus add up fast when you're running persistent sessions. Hermes, on the other hand, runs well on lighter models. I run mine on ChatGPT's $20 plan for most tasks, and for some batch processing I use GLM-5 which costs a fraction of what Opus charges.

The result: I get Opus-level planning and quality review on the tasks that need it, with budget-friendly execution on everything else. My overall AI spend dropped roughly 40% after switching from an all-OpenClaw setup to the paired system — while my actual throughput went up.

That's the theory. Here's what it looks like in practice, starting with the workflow that saved my Wednesday night.

## Workflow 1: The Backup System That Eliminated My Downtime

OpenClaw updates frequently. Painfully frequently. The development team ships improvements at a pace that would be admirable if every update didn't carry a non-trivial chance of breaking something. In February 2026 alone, the repository hit 100,000 GitHub stars partly because the tool is genuinely powerful — but the update cycle means that "genuinely powerful" comes with "occasionally breaks at inconvenient moments."

Before the Hermes pairing, an OpenClaw crash meant manual recovery. SSH into the Mac Mini, read the logs, identify the issue, apply the fix, restart the process. Best case: fifteen minutes. Worst case: an hour of debugging some obscure configuration conflict introduced by the latest update.

The backup workflow is elegant in its simplicity. Hermes runs a lightweight health check against OpenClaw's process every thirty seconds. Not a heavy API call — just a process status ping that costs almost nothing in tokens. If the ping fails three consecutive times (to avoid false alarms from momentary hiccups), Hermes escalates to a diagnostic sequence:

1. **Read the error logs** from OpenClaw's last session
2. **Classify the failure type** — is it a config issue, a model provider error, a memory corruption, or something else?
3. **Apply the appropriate fix** from its growing library of repair patterns
4. **Restart OpenClaw** and verify the process is healthy
5. **Send me a notification** via Telegram with a summary of what happened and what was fixed

The fix library is where Hermes's learning loop pays off beautifully. The first time Hermes encountered an API key error after an OpenClaw update, it took about forty seconds to diagnose and repair. The third time — because Hermes had extracted and refined the pattern — it took eleven seconds. That number keeps improving as Hermes encounters and catalogs new failure types.

This works in both directions, by the way. OpenClaw monitors Hermes's health with the same approach. If Hermes crashes (rare, but it happens), OpenClaw handles the recovery. The system has no single point of failure, which is exactly the property you want from any production-grade infrastructure.

Here's the setup I used to configure the health check on the Hermes side. The configuration lives in Hermes's task scheduler:

```yaml
# hermes-tasks/openclaw-health-check.yml
name: openclaw_health_monitor
schedule: "*/30 * * * * *"  # every 30 seconds
model: chatgpt  # lightweight model for cost efficiency
task: |
  Check if the OpenClaw process is running and responsive.
  If unresponsive for 3+ consecutive checks:
  1. Read /var/log/openclaw/latest.log
  2. Identify the root cause
  3. Apply fix from repair-patterns library
  4. Restart openclaw service
  5. Notify via Telegram: summary of failure + fix applied
retry_on_failure: true
max_retries: 3
```

**Pro tip:** Don't set the health check interval below 30 seconds. I tried 10-second intervals initially and the token cost was noticeable — not catastrophic, but unnecessary. Thirty seconds gives you fast-enough detection without burning budget on redundant pings.

The backup workflow alone justified the Hermes setup. But the next workflow — the one I use for actual project work — is where the real productivity multiplication happens.

## Workflow 2: The Supervisor-Builder Pattern That Changed How I Ship

This is the workflow I use most often, and it's the one that produces the most dramatic results. The concept is borrowed from traditional software team structures: one person plans and reviews, another person builds.

In this pattern, OpenClaw plays the supervisor role. It takes a high-level objective — "build a Next.js dashboard for the scanner monitoring system" — and breaks it down into a detailed implementation plan. Component architecture, file structure, data flow, API endpoints, specific libraries and versions to use. OpenClaw running on Opus 4.6 excels at this kind of architectural thinking. It considers edge cases, anticipates integration problems, and produces plans that are genuinely production-ready.

Then Hermes gets the plan and builds it.

The handoff happens through a shared workspace directory — more on that in workflow 4. OpenClaw writes the plan as a structured markdown file. Hermes picks it up, parses the requirements, and starts executing. Because Hermes is running on a lighter model, the execution costs a fraction of what it would cost if OpenClaw were doing the building itself.

But here's the part most people miss: after Hermes completes the build, OpenClaw reviews the output. It checks the code against the original plan, identifies gaps, suggests improvements, and either approves or sends specific revision requests back to Hermes.

This review step is critical. Without it, you're trusting a lighter model's output without quality verification. With it, you get the cost efficiency of a cheap execution model combined with the quality assurance of a premium review model. The output quality is consistently higher than what either agent produces alone.

A real example from last week. I needed a dashboard to monitor a network scanner fleet — status indicators for each scanner, error logs, uptime metrics, and a simple alert system. Here's the abbreviated version of how it played out:

**OpenClaw's plan** (generated in about 90 seconds on Opus 4.6):
- Next.js 15 app with App Router
- Four main components: ScannerGrid, StatusCard, ErrorLog, AlertPanel
- Server-side data fetching with 30-second revalidation
- Tailwind CSS with a dark theme matching my existing design system
- WebSocket connection for real-time scanner status updates
- Specific file structure, component props, and data schemas

**Hermes's build** (completed in about six minutes on ChatGPT):
- All four components built and functional
- Layout and routing configured
- API routes for scanner data implemented
- Basic styling applied

**OpenClaw's review** (about 45 seconds):
- Caught a missing error boundary around the WebSocket connection
- Suggested adding a reconnection strategy with exponential backoff
- Noted that the StatusCard component wasn't handling the "scanner offline for 24+ hours" state
- Approved the overall architecture and component structure

**Hermes's revision** (about two minutes):
- Applied all three fixes
- Added the reconnection logic
- Handled the extended-offline state

Total time from objective to working dashboard: roughly ten minutes. Total cost: approximately $0.85 in API tokens. If I'd had OpenClaw do everything — planning, building, and reviewing on Opus 4.6 — the same work would have cost roughly $3.40 and taken about fifteen minutes. Same quality, 75% lower cost.

That ratio holds across most of my projects. The supervisor-builder pattern isn't just a productivity hack — it's a structural cost optimization that compounds over time.

If you'd rather have someone build this kind of multi-agent setup from scratch, I take on AI infrastructure engagements through my [Fiverr profile](https://www.fiverr.com/s/EgxYmWD).

The next workflow handles something the backup and supervisor patterns don't address: continuous oversight of long-running processes.

## Workflow 3: The Monitor System That Watches While I Sleep

Some tasks don't need active building. They need watching. My scanner fleet, for example, runs 24/7 across multiple targets. I can't personally check every scanner's status every few hours. And I don't need to — that's exactly the kind of repetitive, cadence-based work that an AI agent handles perfectly.

The monitor workflow puts Hermes in a watchdog role. Every two hours (configurable, obviously), Hermes runs a scheduled check against predefined monitoring targets. It pulls scanner statuses, checks for error conditions, compares performance against baseline metrics, and sends me a summary via Telegram.

The key design decision: Hermes handles the monitoring, not OpenClaw. There are two reasons for this.

First, cost. A monitoring check every two hours, 24/7, adds up to 84 checks per week. Running those on Opus 4.6 would cost noticeably more than running them on ChatGPT or GLM-5. The monitoring task doesn't require Opus-level reasoning — it's pattern matching and threshold comparison. The lighter model handles it perfectly.

Second, availability. If OpenClaw is busy with a complex planning or review task, you don't want your monitoring to queue behind it. Hermes running independently means monitoring never gets delayed by other work. The two agents operate on separate resource pools.

Here's what a typical monitoring cycle looks like:

```
[Hermes Monitor — 2026-04-14 02:00 UTC]
Scanner Fleet Status Check:
  ├── Scanner Alpha: ✅ Online (uptime: 99.7%, last 24h)
  ├── Scanner Beta: ✅ Online (uptime: 98.2%, last 24h)  
  ├── Scanner Gamma: ⚠️ Degraded (response time 340ms → 890ms, investigating)
  └── Scanner Delta: ✅ Online (uptime: 100%, last 24h)

Action taken: Opened investigation on Scanner Gamma latency spike.
Root cause: DNS resolution delay from upstream provider.
Recommendation: No immediate action needed — monitoring for resolution.

Next check: 2026-04-14 04:00 UTC
```

That report arrived while I was asleep. When I woke up at 7 AM, the Gamma issue had already resolved itself — and Hermes had logged the resolution in the shared memory system, including the root cause and timeline. If it happens again, both agents know the pattern and can respond faster.

The cron job configuration for this is straightforward:

```yaml
# hermes-tasks/scanner-monitor.yml
name: scanner_fleet_monitor
schedule: "0 */2 * * *"  # every 2 hours
model: chatgpt
task: |
  Check status of all active scanners.
  For each scanner, verify:
  - Process is running
  - Response time is within baseline (< 500ms)
  - No error logs in last 2 hours
  - Uptime percentage for trailing 24h
  Report any anomalies with severity rating.
  If critical: alert immediately via Telegram.
  If warning: include in next summary report.
  Log all findings to shared memory workspace.
escalation_threshold: critical
notify_channel: telegram
```

The monitor workflow pairs naturally with the backup workflow. If Hermes detects that OpenClaw itself is the source of an issue — maybe OpenClaw kicked off a task that's consuming excessive resources — Hermes can flag it before the problem cascades. You end up with a system where nothing fails silently, which is the most dangerous kind of failure in any automated system.

But all three workflows I've described so far share a limitation: the agents learn independently. OpenClaw develops its own understanding of problems and solutions. Hermes develops its own. Neither benefits from what the other has learned.

That's what the fourth workflow solves.

## Workflow 4: Shared Memory — Where Both Agents Get Smarter Together

This is the workflow that elevates the OpenClaw-Hermes pairing from "useful" to "compounding." The concept is deceptively simple: both agents read from and write to a shared knowledge base. In practice, it creates something close to organizational memory — institutional knowledge that persists regardless of which agent is active.

I implement this through Obsidian. Not because Obsidian is the only option — any shared file system would work — but because Obsidian's linked markdown structure makes the knowledge base browsable by humans too. I can open the vault, search through it, see connections between entries, and understand what my agents have been learning. That transparency matters when you're trusting autonomous systems with real work.

The shared memory workspace lives in a synced folder that both agents can access. The structure looks like this:

```
shared-memory/
├── logs/
│   ├── openclaw/
│   │   └── 2026-04-14-session.md
│   └── hermes/
│       └── 2026-04-14-monitor.md
├── mistakes/
│   ├── api-key-rotation-failure.md
│   ├── websocket-reconnection-missing.md
│   └── scanner-dns-resolution-delay.md
├── patterns/
│   ├── repair-patterns.md
│   ├── build-review-patterns.md
│   └── monitoring-baselines.md
├── decisions/
│   └── architecture-decisions.md
└── context/
    ├── project-scanner-fleet.md
    └── project-content-pipeline.md
```

Every time an agent encounters something worth remembering — a mistake, a successful repair pattern, a decision about how to handle a specific scenario — it writes an entry to the appropriate folder. The entry includes what happened, why it happened, what the agent did about it, and what it would do differently next time.

Here's a real entry from my mistakes folder, written by Hermes after a monitoring false alarm:

```markdown
# False Alarm: Scanner Gamma Timeout — 2026-04-11

## What happened
Hermes flagged Scanner Gamma as "critical — unresponsive" at 14:22 UTC.
Sent immediate Telegram alert. OpenClaw initiated emergency diagnostic.

## What actually happened
Scanner Gamma was responding normally. The monitoring check timed out due to
a network blip on the monitoring host, not the scanner itself.

## Impact
Unnecessary alert. Wasted ~$0.12 in diagnostic token costs. Disrupted Mejba's
afternoon with a false emergency notification.

## Fix applied
Added retry logic: 3 consecutive failures before escalating to "critical."
Single failure now logs as "investigating" without alerting.

## Pattern extracted
Network-layer issues on the monitoring host can mimic target failures. Always
retry before escalating. Check monitoring host health as part of the
diagnostic sequence.
```

That entry is now available to both agents. When OpenClaw runs its own monitoring tasks, it has access to this pattern. When Hermes encounters a similar situation in the future, it doesn't repeat the same mistake. The system learned once and both agents benefit.

This is what I mean by recursive self-improvement. It's not the Hollywood version where the AI suddenly becomes superintelligent. It's the boring, practical version: the system accumulates operational knowledge over time, and that knowledge makes it more reliable, more accurate, and less expensive to run.

Hermes has a native advantage here thanks to its built-in learning loop. The Hermes Memory Keep-Alive plugin for Obsidian syncs its internal memory with the vault, so core memories, entity data, and conversation history all flow into the shared workspace automatically. OpenClaw requires a bit more manual configuration to write to the shared folder — I use a custom skill that triggers after each session to dump relevant learnings — but the end result is the same: both agents contribute to and draw from the same knowledge base.

After three months of running this system, my shared memory vault contains over 400 entries. The agents reference these entries autonomously during their work. I've watched OpenClaw cite a Hermes mistake entry while planning a build, adjusting its architecture to avoid a known failure mode. That's the kind of cross-agent learning that makes the whole system feel less like two separate tools and more like a single team with shared institutional knowledge.

## The Cost Equation Nobody Talks About Honestly

I'm going to be direct about costs because most articles about multi-agent setups either ignore them or make them sound trivially cheap.

Running OpenClaw on Opus 4.6 is not cheap. A persistent session with active planning and review tasks runs me roughly $80-120 per month in API costs, depending on workload. That's the premium you pay for top-tier reasoning on complex tasks.

Running Hermes on ChatGPT's $20 plan covers most of my execution and monitoring workload. For batch processing tasks where I need even lower costs, GLM-5 handles it at roughly $0.001-0.003 per 1K tokens — negligible for monitoring and simple execution tasks.

My total monthly spend on the two-agent system: approximately $130-160.

For context, my previous setup — running everything through OpenClaw on Opus — cost me roughly $200-280 per month with fewer total capabilities and zero redundancy. The paired system costs less and does more. The supervisor-builder pattern alone saves enough in avoided Opus token costs to cover Hermes's entire operating expense.

But there's a cost most people don't account for: setup time. Getting the two agents properly configured, the shared memory system working, the health checks calibrated, and the supervisor-builder handoff reliable took me about two full weekends. That's real time investment. If you're running a single agent for simple tasks and it's working fine, the multi-agent setup might be over-engineering the problem.

The breakeven point, in my experience, is somewhere around the moment you catch yourself saying "I need to check if my agent is still running." If you're monitoring your agent instead of your agent monitoring your work, you need a second agent.

## What This System Gets Wrong — And What I'm Still Figuring Out

Three months in, the system isn't perfect. Being honest about the gaps matters more than pretending they don't exist.

**Context drift between agents.** Even with shared memory, OpenClaw and Hermes sometimes develop slightly different understandings of the same project. OpenClaw might plan a feature using one architectural pattern while Hermes has already evolved a different pattern through its learning loop. The shared memory helps, but it doesn't eliminate divergence entirely. I do a manual alignment check roughly once a week where I review the shared workspace and explicitly sync the agents' understanding of active projects.

**Update conflicts.** Both OpenClaw and Hermes push updates regularly. When both update in the same week — which happens more often than I'd like — there's a non-trivial chance that one update breaks compatibility with the other. I've started staggering my updates: OpenClaw on Mondays, Hermes on Thursdays. It adds management overhead, but it means I never have both agents down simultaneously.

**The debugging paradox.** When something goes wrong in the two-agent system, it's sometimes harder to diagnose than a single-agent failure. Did OpenClaw give Hermes a bad plan? Did Hermes misexecute a good plan? Did the shared memory contain a faulty pattern that led both agents astray? The debugging surface area is larger. Good logging mitigates this, but doesn't eliminate it.

**Model dependency risk.** My current setup depends on Opus 4.6 for OpenClaw and ChatGPT for Hermes. If either model provider has an outage, half my system goes down. I'm experimenting with fallback configurations — OpenClaw on Sonnet 4.6 as a degraded-but-functional backup, Hermes on GLM-5 as its fallback — but I haven't fully tested the quality implications of those fallbacks under real workloads yet.

These are solvable problems, not fundamental flaws. And the productivity gain from the paired system outweighs every one of them by a wide margin. But you should go in with realistic expectations about what "multi-agent" actually means in daily practice: it's not set-and-forget. It's set-and-maintain, with the maintenance cost decreasing over time as the shared memory grows.

## How to Start: The 30-Minute Setup That Gets You 80% of the Value

If you want to try this without committing a full weekend, here's the minimal viable version. You can expand from here once you've seen the value.

**Step 1: Install both agents (10 minutes).**
OpenClaw runs on any system with Node.js. The GitHub repo (openclaw/openclaw) has a one-command install. Hermes installs similarly from NousResearch/hermes-agent. Both support Mac, Linux, and Windows.

**Step 2: Configure the backup workflow (10 minutes).**
Set up Hermes to monitor OpenClaw's process health every 60 seconds (start conservative, optimize later). This alone gives you the most immediately valuable workflow — automatic recovery from crashes.

**Step 3: Create the shared memory folder (5 minutes).**
A simple directory structure: logs, mistakes, patterns. Point both agents at it. You can add Obsidian later if you want the browsable interface.

**Step 4: Run your first supervisor-builder task (5 minutes).**
Give OpenClaw a small planning task. Have it write the plan to the shared folder. Tell Hermes to execute the plan. Review the output. That's the workflow.

You don't need the monitoring cron jobs on day one. You don't need elaborate shared memory schemas. You don't need perfect cost optimization. Start with backup and supervisor-builder. Those two patterns deliver the majority of the value, and they'll teach you how the agents interact before you add complexity.

## Where Multi-Agent Systems Are Heading

The OpenClaw-Hermes pairing I run today is, I suspect, a preview of how most developers will work with AI within the next twelve months. Not one monolithic agent doing everything, but specialized agents with defined roles, shared context, and complementary strengths.

The signals are already visible. Databricks shipped a supervisor agent architecture for enterprise workflows. CrewAI and LangGraph are building orchestration layers for multi-agent coordination. OpenClaw's own roadmap includes deeper inter-agent communication protocols. Hermes's learning loop — the idea that an agent should get measurably better at repeated tasks — is showing up in other frameworks too.

The competitive advantage right now isn't having the best single agent. It's having the best agent team. A system where planning happens on a model that's good at planning, execution happens on a model that's good at execution, and monitoring happens continuously without human intervention.

I wrote about [how OpenClaw agents can scale a business](/openclaw-agents-scale-business) a few weeks ago, and the shift from tasks to jobs. The two-agent system takes that one step further: it's not just about giving agents jobs — it's about giving agents colleagues. An agent with a backup, a reviewer, and a shared knowledge base is qualitatively different from an agent working alone.

Set up the backup workflow tonight. Watch one agent fix the other for the first time. That eleven-second recovery I described at the beginning of this post? It's not the ceiling of what this system can do. It's the floor.

## Frequently Asked Questions

### Can I run OpenClaw and Hermes on the same machine?
Yes. Both agents are lightweight enough to run on a single Mac Mini M4 or equivalent Linux machine with 16GB RAM. I run both on one machine — OpenClaw as the primary process and Hermes as a background service. The key is ensuring they use separate model providers so API rate limits don't conflict.

### How much does the OpenClaw and Hermes two-agent setup cost per month?
Expect $130-160 per month total — roughly $80-120 for OpenClaw on Opus 4.6 and $20-40 for Hermes on ChatGPT or GLM-5. The exact cost depends on workload intensity. The supervisor-builder pattern typically saves 40-75% compared to running everything on a premium model alone.

### Do I need Obsidian for the shared memory system?
No. Any shared folder that both agents can read and write to works. Obsidian adds a browsable, linked-note interface that makes the knowledge base human-readable, but a plain directory of markdown files delivers the same functional benefit to the agents. I recommend starting with a simple folder and adding Obsidian later if you want to audit and explore the shared memory manually.

### What happens if both agents crash at the same time?
It's rare but possible — usually caused by a system-level issue like a power outage or OS crash rather than an agent-level failure. I run a simple systemd watchdog (launchd on macOS) that restarts both agents if their processes die. This third layer of redundancy costs nothing and handles the edge case cleanly.

### Can I use different models than Opus 4.6 and ChatGPT?
Absolutely. OpenClaw supports 50+ model providers including Gemini, GLM-5, Sonnet 4.6, and local models through Ollama. Hermes works with any OpenAI-compatible API. The Opus + ChatGPT pairing is my recommendation for the best balance of quality and cost, but you can run both on cheaper models if budget is the primary constraint — just expect lower quality on complex planning tasks.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
