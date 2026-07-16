**BRAND:** mejba.me
**TITLE:** Deploy Autonomous Agents with Claude Code: A Real Guide
**META TITLE:** Deploy Autonomous Agents with Claude Code (2026 Guide)
**SLUG:** deploy-autonomous-agents-claude-code-guide
**PRIMARY KEYWORD:** deploy autonomous agents with Claude Code
**META DESCRIPTION:** Three real ways to deploy autonomous agents with Claude Code — cron loops, cloud routines, and Modal/Trigger.dev. What I learned running each.
**TAGS:** Claude Code, AI Agents, Automation, Claude Agent SDK, DevOps

---

The first agent I ever deployed "autonomously" ran for exactly nine hours before my laptop went to sleep and killed the whole thing. I woke up to a paused terminal, a half-finished SEO audit, and the slow realization that "autonomous" is a word people throw around without checking whether the machine running the loop has a screensaver.

That was the moment I stopped chasing the agent and started chasing the *deployment*. Because here is the thing nobody tells you when you start building agents with Claude Code: the agent itself is the easy part. The hard part is figuring out *where* it lives, *how* it survives, and *who pays the bill* when it loops 4,000 times in a weekend because you forgot to set a stop condition.

This is the guide I wish I had on day one. Three real deployment methods. What each one actually costs. What breaks. When to use which. And a framework — WAT — that I now use to evaluate every agent before I let it touch production.

If you have ever wondered why your "24/7 agent" only runs when you are awake, this is for you.

## The WAT Framework: Why Most Agent Deployments Fail

Before deciding *where* to deploy, you have to decide *what* you are deploying. I borrowed a framework from a friend who runs a small AI consultancy and have not stopped using it since.

**WAT stands for Workflow, Agent, Tools.**

- **Workflow** — the sequence of steps. Deterministic. Predictable. "First do A, then B, then C."
- **Agent** — the autonomous decision-making layer. Looks at state, picks the next move, can deviate from the plan.
- **Tools** — the things the agent or workflow can call. APIs, MCP servers, slash commands, scripts, shell commands.

Every system you build is a mix of these three. The mix determines where you should deploy. A purely deterministic content-publishing pipeline is mostly Workflow plus Tools — you barely need an Agent. A research bot that decides which sources to read next is heavily Agent plus Tools — the Workflow is loose by design.

There is a second axis I added after watching too many people overpay for autonomy they did not need: **autonomy degree versus environment availability**. Some agents need to run on your local machine because they touch your file system, your IDE, your local secrets. Some agents need to run in the cloud because you want them awake at 3 a.m. on a Sunday while you are asleep.

Once you can name where on those two axes your agent lives, picking a deployment method stops being a religious debate. It becomes a checklist.

But before we get into the three methods, I have to confess something. I was wrong about which method was best for almost a full year. I will tell you which one and why in Method 3.

## Method 1: Cron Loops Inside Claude Code (The 90% Starter)

This is the method I tell every developer to try first. It is the cheapest, fastest, and least technical way to deploy an autonomous agent. And for most people, it is enough.

Anthropic shipped native cron support inside Claude Code in March 2026 — three tools called `CronCreate`, `CronList`, and `CronDelete`. They are wrapped by the `/loop` and `/schedule` skills, but you can call them directly with natural language. According to [Anthropic's official documentation](https://code.claude.com/docs/en/scheduled-tasks), a single session can hold up to 50 scheduled tasks at once, and the cron syntax is the standard five-field format you already know from Linux: minute, hour, day-of-month, month, day-of-week.

### How I actually use it

A typical session looks like this. I open Claude Code in my terminal. I type:

```
Every 30 minutes, check my GitHub repo for new PRs assigned to me,
summarize the diff, and post the summary to my Slack workspace via
the slack-mcp tool. Stop after 12 iterations.
```

Claude Code parses that, calls `CronCreate` with the cron expression `*/30 * * * *`, attaches the prompt as the task body, sets a kill-after counter of 12, and tells me the task ID. I can run `/cron list` (or just say "show my scheduled tasks") and see it sitting there. I can kill it with `/cron delete <id>` at any time.

What I love about this: every iteration is a full agentic loop. The cron task injects the prompt into a fresh-ish Claude Code context, and from there Claude can use *every* tool you have configured — MCP servers, custom slash commands, hooks, skills, all of it. You are not running a deterministic script. You are running an agent that happens to be triggered by a clock.

### Where it breaks

Three places. I learned all of them the hard way.

**One — your machine has to stay on.** This is a local cron. It lives inside the Claude Code session on your laptop. If your computer sleeps, the cron sleeps. If you close the terminal in the wrong way, the cron dies. I now keep a dedicated `caffeinate -d -i` process running on my Mac whenever a long-lived loop is active, and I disable display sleep for the duration. Even then, terminal-based loops can persist up to seven days; desktop app loops cap at three days. After that, Anthropic kills the session to free resources.

**Two — jitter.** Anthropic adds up to 30 minutes of random delay to every scheduled task. This is intentional, not a bug. It prevents thousands of users from hammering the API on round-number minutes. But if you build something that *must* fire at exactly 9:00 a.m. — a market-open trading signal, a "good morning" email at a precise time — cron loops will betray you. Plan for the jitter or pick a different method.

**Three — the clear command kills desktop loops.** If you run `/clear` to reset the chat history in the desktop app, every scheduled task dies with the chat. Terminal-based Claude Code is different — `/clear` only wipes the visible history, and your loops survive. I now do all serious cron work in the terminal for exactly this reason. [The Better Stack guide on Claude Code recurring tasks](https://betterstack.com/community/guides/ai/claude-code-loop/) is the cleanest writeup I have found on this distinction if you want the deep version.

### When to pick Method 1

Pick it when you want to test a workflow idea before committing engineering time to it. Pick it when the task is bursty — you need it for a week, not forever. Pick it when you genuinely have a machine that is always on, like a home server or a dedicated Mac mini.

Do not pick it for anything that must be reliable across machine failures. Do not pick it for tasks that need second-level timing precision. And do not pick it for tasks that need to survive a Claude Code update — every time the CLI updates, you re-validate.

If you want a deeper look at how I think about the cron loop pattern across different agent workloads, I covered it from a slightly different angle in [my breakdown of Claude Code's loop and cron scheduling primitives](https://www.mejba.me/blog/claude-code-loop-cron-scheduling).

That said, even after a year of building agents, this is still where 60% of my "autonomous" setups live. It is not glamorous. It works.

Now let me show you the upgrade path — because eventually, you will outgrow it.

<!-- IMAGE: Terminal screenshot showing /cron list output with three active scheduled agent tasks, each with a task ID, cron expression, and next-fire timestamp. Alt text: "deploy autonomous agents with Claude Code cron list showing three scheduled tasks in terminal". Caption: "My current always-on tasks — repo monitor, content audit, and a daily SEO crawl." -->

## Method 2: Desktop Scheduled Tasks and Cloud Routines (The 24/7 Upgrade)

This is the method I switched to the moment Anthropic launched Claude Code Routines on April 14, 2026. The Register's [coverage of the launch](https://www.theregister.com/2026/04/14/claude_code_routines/) is a little snarky about whether routines are genuinely useful, but having used them for a month, I can tell you exactly where they shine and where they do not.

There are two flavors here. They look similar from the outside. They are completely different underneath.

### Desktop scheduled tasks

These are persistent local schedules that survive across Claude Code restarts. They run in the desktop app. They fire a fresh session every time they trigger — no shared state, no leftover context — which I actually prefer for clean reasoning. If your machine is off when the schedule was supposed to fire, the task plays catch-up the next time the app boots. There are no explicit daily caps beyond your existing subscription limits. The big difference from Method 1 is *persistence* — these tasks live in app config, not in a session, so closing the app does not kill them.

I use desktop scheduled tasks for things like a daily 9 a.m. inbox triage agent. The desktop app is open anyway because I am working. The agent fires, summarizes my inbox, drafts replies into a Notion doc, and shuts down. Total wall time: maybe 90 seconds. Total cognitive load on me: zero.

### Cloud routines

This is the real game-changer. Routines run on Anthropic's cloud infrastructure. Your machine can be off. Your Claude Code session can be closed. The routine fires anyway.

Routines can be triggered by:

- **Time** — standard cron expressions, but with a *minimum* one-hour interval (this is the big limit)
- **Webhook** — POST to a unique URL, the routine fires
- **GitHub event** — a push, a PR comment, an issue label change, all of these can fire a routine

According to [the MindStudio writeup on Claude Code Routines](https://www.mindstudio.ai/blog/claude-code-routines-scheduled-agents), the daily run limits scale with plan: roughly 5 runs/day on Pro, climbing toward 25 runs/day on the Max tiers. These limits are *separate* from your interactive Claude Code usage, which is one of the most underrated features. Your routine running at 3 a.m. is not eating into the budget you need to ship code at 11 a.m.

But the limit story changed dramatically in May 2026. [Anthropic announced](https://venturebeat.com/technology/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch) that starting June 15, 2026, programmatic Claude Code usage — including Agent SDK calls and `claude -p` headless mode — moves to a separate dedicated credit pool funded by an amount equal to your subscription fee. Routines sit closer to the interactive side of that line, but if your routines run heavy SDK-flavored workloads, watch the upcoming billing changes carefully.

### What I actually run in routines right now

- A SEO health check that pings my four brand sites every morning at 6 a.m. and emails me anomalies. (I wrote about the workflow in detail in [my guide to automating SEO checks with Claude Code Routines](https://www.mejba.me/blog/automate-seo-checks-with-claude-code-routines).)
- A weekly content audit that scans my mejba.me archive for posts that have dropped in rankings and queues them for refresh.
- A GitHub-triggered routine that runs a security review whenever a PR is opened against my Laravel boilerplate.

The thing that makes routines feel different from Method 1: I do not think about them. I forgot one of them was even running for two weeks. It just kept doing its job.

### Where routines break

**The one-hour minimum interval is a real constraint.** If you need anything that fires more frequently — say, a stock-tracking agent that checks every 15 minutes during market hours — routines cannot do it. You are back to Method 1 (with the jitter pain) or Method 3.

**Autonomous prompt scoping is dangerous.** Because each routine fires a fresh, full-tool Claude Code session, a sloppy prompt can do real damage. I once wrote a routine that said "review the latest PRs and clean up anything that looks broken." It interpreted "clean up" generously. It pushed force-push commits to a feature branch. The branch was, thankfully, mine. Lesson burned in: routines should have *narrow* prompts with explicit constraints, not vague instructions that assume context.

**The daily run cap is real.** On a Pro plan with five routine runs per day, you cannot run an hourly job. Plan around the cap, or pay up.

### When to pick Method 2

Pick desktop scheduled tasks when you want simple reliability and your machine is already on most of the time. Pick cloud routines when you need genuine 24/7 execution with no local dependency. The handoff point in my experience is when an agent needs to run while you sleep, while you travel, or while your machine is rebooting.

But there is a third option that handles workloads neither of these can. And this is where I want to spend the most time, because this is the method I underrated for a full year.

## Method 3: Modal and Trigger.dev with the Claude Agent SDK (The Production Path)

Here is the confession I owe you from earlier. For most of 2025, I treated Modal and Trigger.dev as "for people building real products" — as if my own agents were not real. I kept everything in Method 1 and Method 2 long after I had outgrown them. The result: weekly outages, mysterious silent failures, a Claude Code session that kept getting killed at hour 168.

When I finally migrated three of my heaviest agents to Modal, two things happened. My uptime went to essentially 100%. And my monthly costs went *up* by about $40 — which, given the reliability gain, was the best $40 I have ever spent on infrastructure.

Let me walk through both platforms because they are genuinely different.

### Modal — Python-first, sandboxed, serverless

[Modal](https://modal.com) is a serverless platform built around Python. You write a function. You decorate it with `@app.function()` or `@app.schedule(cron="0 9 * * *")`. You run `modal deploy`. Done. The function runs in a gVisor-isolated sandbox, scales horizontally, and bills per second of CPU/GPU time.

What makes Modal interesting for agents: their sandbox is *fast to start* and *isolated by default*. According to [Modal's own analysis of agent sandboxes](https://modal.com/resources/best-sandbox-claude-agent-sdk), they have benchmarked their infrastructure specifically for Claude Agent SDK workloads and report support for over 50,000 concurrent sandboxed sessions. That kind of horizontal scale is not something you get out of a cron loop on your laptop.

The basic pattern looks like this:

```python
import modal
from anthropic import Anthropic

app = modal.App("autonomous-research-agent")
image = modal.Image.debian_slim().pip_install("anthropic", "claude-agent-sdk")

@app.function(
    image=image,
    schedule=modal.Cron("0 */6 * * *"),  # every 6 hours
    secrets=[modal.Secret.from_name("anthropic-api-key")],
    timeout=3600,  # max 1 hour per run
)
def research_loop():
    from claude_agent_sdk import Agent
    agent = Agent(
        system_prompt="You are a research agent. Find new AI papers...",
        tools=["web_search", "file_write"],
        max_turns=20,
    )
    result = agent.run("Find five new papers on agentic RAG published this week.")
    # ship result to wherever — Slack, Notion, S3, etc.
    return result
```

That is a fully autonomous agent running in the cloud on a six-hour schedule, with no local machine involved. The Claude Agent SDK handles the agent loop — reasoning, tool calls, multi-step decisions — and Modal handles the runtime. As [Anthropic's engineering team describes the Agent SDK](https://www.anthropic.com/engineering/building-agents-with-the-claude-agent-sdk), this is the "brain plus hands" model: the SDK is the brain, the platform is the hands.

The cost story matters here. The Agent SDK does *not* use your Claude Code subscription. It uses a separate Anthropic API key, billed per token. Opus 4.6 is roughly $5 per million input tokens and $25 per million output tokens; Sonnet 4.6 is closer to $3/$15. A heavily autonomous agent doing 20 turns with deep context can burn through $0.50 to $2 per run. Run it every six hours and you are looking at $60 to $240 a month per agent. Not nothing.

### Trigger.dev — TypeScript-first, durable workflows

[Trigger.dev](https://trigger.dev) is what I reach for when the agent lives in a TypeScript codebase or when I want richer workflow semantics. Their v3 release leaned into agentic patterns — long-running tasks, real-time log streaming, "waitpoints" that pause a workflow waiting for a human approval or an external event. According to [the MindStudio breakdown of Trigger.dev as an agentic workflow platform](https://www.mindstudio.ai/blog/what-is-trigger-dev-agentic-workflow-platform), it is genuinely a different shape from Modal: less "function-in-a-sandbox," more "durable workflow engine."

A Trigger.dev task with the Claude Agent SDK looks roughly like this:

```typescript
import { task } from "@trigger.dev/sdk/v3";
import { ClaudeAgent } from "@anthropic-ai/claude-agent-sdk";

export const seoRefreshAgent = task({
  id: "seo-refresh-agent",
  schedule: { cron: "0 3 * * *" },  // 3 a.m. UTC daily
  run: async (payload, { logger }) => {
    const agent = new ClaudeAgent({
      apiKey: process.env.ANTHROPIC_API_KEY,
      maxTurns: 25,
      tools: ["web_search", "github", "filesystem"],
    });

    const result = await agent.run({
      prompt: "Audit content/mejba.me. Find posts that dropped in rank...",
    });

    logger.info("Audit complete", { suggestionsFound: result.suggestions.length });
    return result;
  },
});
```

What I like about Trigger.dev for agent work: the durability. If a step fails halfway through, Trigger.dev resumes from the last checkpoint instead of starting over. For an agent that has already used $0.80 in tokens by step 14, that resumability is real money saved.

### When Claude Code makes Method 3 dramatically easier

Here is the part that surprised me. Even though Modal and Trigger.dev are "serverless platforms you deploy to," I do almost all of my work on them *from inside Claude Code*. Claude Code understands `.env` files, handles secret management cleanly, runs `modal deploy` or `npx trigger.dev@latest deploy` in its shell, and reads back the deployment output to diagnose failures. The Trigger.dev team even built [an official MCP server](https://trigger.dev/) so Claude Code can search their docs, trigger runs, and monitor jobs without leaving the terminal.

So in practice, my workflow is: write the agent in Claude Code, deploy with Claude Code, monitor logs with Claude Code, debug failures with Claude Code. The platform is in the cloud. The control surface is still my terminal.

For a complementary deep dive on the Agent SDK itself — what it actually does, when to reach for it versus headless Claude Code — see [my full guide to the Anthropic Agent SDK](https://www.mejba.me/blog/anthropic-agent-sdk-guide).

### Where Method 3 breaks

**The cost ceiling is real.** Token billing scales linearly. A misbehaving agent that loops on itself can burn $50 in an hour. I now ship every Method 3 agent with a hard `max_turns` and a budget alarm on the Anthropic dashboard.

**You are now a platform engineer.** Modal and Trigger.dev are *good* tools, but they are still tools. You have to think about deployment versioning, secret rotation, log retention, observability. If you do not enjoy any of that, stay in Method 2.

**The Agent SDK has a learning curve.** It is not just "Claude Code but in the cloud." It is a lower-level primitive. The tool-call loop, the memory management, the hooks — all of it is exposed. According to [SitePoint's deep dive on Claude Code as an autonomous agent](https://www.sitepoint.com/claude-code-as-an-autonomous-agent-advanced-workflows-2026/), you should expect to spend two to three weeks getting genuinely comfortable with the SDK before deploying mission-critical workloads.

### When to pick Method 3

Pick it when reliability is more important than convenience. Pick it when the agent runs constantly enough that local execution becomes a bottleneck. Pick it when you need horizontal scale — one agent becoming twenty agents under load. Pick it when the workload has clear ROI and the token cost is justified by the output.

Do not pick it for prototyping. Do not pick it for one-off tasks. And honestly, do not pick it until you have actually run an agent in Method 1 or Method 2 first, because building production-grade agents without that intuition leads to expensive over-engineering.

<!-- IMAGE: Diagram showing the three deployment methods on two axes — autonomy degree (X-axis) and environment availability (Y-axis). Cron loops sit bottom-left (low autonomy, local). Routines sit top-middle (moderate autonomy, cloud). Modal/Trigger.dev with Agent SDK sit top-right (high autonomy, fully cloud). Alt text: "deploy autonomous agents with Claude Code decision matrix by autonomy and runtime environment". Caption: "How I pick deployment methods — the further right and up, the more production-grade and the more expensive." -->

## The Honest Comparison Table

Reading three method sections is a lot. Here is the table I actually keep open in a tab.

| Dimension | Method 1: Cron Loops | Method 2: Routines / Desktop | Method 3: Modal / Trigger.dev |
|---|---|---|---|
| Machine required on | Yes | Desktop: yes / Cloud: no | No |
| Setup time | 60 seconds | 5–10 minutes | 1–4 hours |
| Typical monthly cost | Included in subscription | Included | $20–$300+ token-billed |
| Minimum interval | ~every minute (with jitter) | 1 hour (cloud routines) | Down to seconds |
| Full Claude Code tools | Yes | Yes | Limited to Agent SDK tools |
| Survives 7+ days | No (7-day cap) | Yes | Yes |
| Best for | Prototyping, short bursts | Daily / hourly cloud tasks | Production, scale, custom workflows |
| Worst for | Long-lived agents | Sub-hour cadence | One-offs and quick tests |

## What About Managed Agents and Hooks?

Two related questions come up constantly when I talk about agent deployment, so let me address them directly.

### Anthropic's Managed Agents

In early 2026 Anthropic shipped [Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview) — a fully hosted autonomous agent service that gives you a runtime, tool execution, and an environment without you having to wire any of it together. On paper, it looks like the dream version of Method 3 with none of the platform engineering.

I tried it. I want to be enthusiastic. I am not. The current iteration locks you into Anthropic's environment, has limited flexibility around custom tools, and prices itself in a way that competes uncomfortably with running the Agent SDK on Modal yourself. For specific use cases — companies that want zero infrastructure surface area and are happy to live inside Anthropic's runtime — it is a real option. For most builders I know, it is not yet a replacement for self-hosted Method 3. I wrote a more detailed walkthrough of my experience in [my Anthropic Managed Agents review](https://www.mejba.me/blog/anthropic-managed-agents-walkthrough), and I will revisit it as the product matures.

### Hooks — the deterministic layer underneath everything

The other thing nobody talks about enough: **hooks**. Claude Code hooks are event-driven shell commands that fire on specific lifecycle events — `UserPromptSubmit`, `PreToolUse`, `PostToolUse`, `SessionStart`, `SessionEnd`, and (as of the March 2026 update) about 25 distinct lifecycle points total.

Hooks are *not* a deployment method. They are the deterministic glue *inside* a deployment. The clearest way to think about it: [as Anthropic's own hooks guide explains](https://code.claude.com/docs/en/hooks-guide), hooks react to events *inside* the Claude Code session, while routines react to events *outside* it.

I layer hooks on every method above. In Method 1, I use a `PostToolUse` hook to log every tool call my cron agent makes — that is how I caught the force-push routine before it did real damage. In Method 2, I use a `SessionStart` hook to verify environment variables before any routine begins doing work. In Method 3, I use the Agent SDK's hooks API to enforce a "before each turn, check the budget" gate — if the agent has burned more than $1 in this run, hard stop.

If you have never set up a hook, this is the highest-leverage 30 minutes you can spend on agent reliability. I covered the pattern library in [my full Claude Code hooks deep dive](https://www.mejba.me/blog/claude-code-hooks-event-driven-automation) — start there.

## How I'd Actually Build Your First Production Agent

If I were starting from zero with the goal of one genuinely useful autonomous agent running in production within a week, here is the path:

**Day 1.** Pick a workflow that is currently costing you 30+ minutes a week. Inbox triage. Repo monitoring. Daily report generation. Write the prompt for it in plain English. Run it manually in Claude Code three times. Notice what fails.

**Day 2.** Wrap it in Method 1. Use `/loop` or `/cron` to schedule it. Let it run for 24 hours on your machine while you watch the logs. Tweak the prompt every time it does something dumb. By the end of day two, the prompt should be tight.

**Day 3.** Migrate to Method 2 — desktop scheduled task or cloud routine. Let it run for three days untouched. If it survives without intervention, you have a real candidate.

**Day 4-5.** Decide if Method 3 is worth it. Ask: does this need to fire more than once an hour? Does it need to scale? Does the workload justify the token cost? If yes, port to Modal or Trigger.dev. If no, stay in Method 2.

**Day 6-7.** Add hooks. Add budget guards. Add observability — every agent should log every tool call somewhere you can query. I use a simple SQLite file written by a `PostToolUse` hook and it has saved me more than once.

That is it. One week, one agent, one real piece of automation that runs without you. Then repeat.

If you want a parallel reference for what a fully built-out autonomous coding agent looks like end-to-end — including some of the harder runtime questions — [my breakdown of Hermes, a Claude Code agent deployed to a VPS via Discord](https://www.mejba.me/blog/hermes-claude-code-vps-discord-deployment), walks through a real example from architecture to launch.

## The Lesson That Took Me a Year

The agent industry talks about deployment like it is a graduation — you start with cron loops, "level up" to routines, "ascend" to the Agent SDK on Modal. That framing is wrong. After two years of building these, here is what I actually believe.

There is no winning method. There is only the method that matches the WAT shape of your specific agent on a specific Tuesday.

The job is not to deploy agents *bigger*. The job is to deploy them *fit-for-purpose*. The cron loop is not training wheels — for the right workload, it is the destination. The Agent SDK on Modal is not the goal — for the right workload, it is a liability waiting to bill you $300 because you forgot a recursion limit.

Pick the smallest method that works. Run it for a week. Look at the failures honestly. Upgrade only when the failure mode is structural, not stylistic. Repeat.

The deployment is the agent. Get it right, and the rest is just prompts.

## Frequently Asked Questions

### How do I deploy autonomous agents with Claude Code without keeping my computer on?
Use Claude Code Cloud Routines, which run on Anthropic's cloud infrastructure independent of your local machine. Routines support time-based, webhook, and GitHub event triggers, with a one-hour minimum interval and daily run caps that scale with your subscription plan. See the Method 2 section above for the full setup walkthrough.

### What is the difference between Claude Code cron jobs and cloud routines?
Cron jobs run inside a local Claude Code session via the `CronCreate`/`CronList`/`CronDelete` tools — they need your machine and session to stay alive, and they cap at seven days in the terminal. Cloud routines run on Anthropic's infrastructure, persist independently, and trigger from time, webhook, or GitHub events with a one-hour minimum interval.

### Does the Claude Agent SDK use my Claude Code subscription?
No — the Claude Agent SDK is billed separately against a Claude API key, with token-based pricing (around $5/$25 per million input/output tokens for Opus 4.6 as of May 2026). Starting June 15, 2026, Anthropic moves Agent SDK usage to a dedicated credit pool separate from interactive subscription limits.

### Should I use Modal or Trigger.dev for Claude agents?
Use Modal if your agent is Python-centric and you want a serverless sandbox model with fast horizontal scaling — Modal has benchmarked specifically for Agent SDK workloads. Use Trigger.dev if your agent lives in a TypeScript codebase and benefits from durable workflows, waitpoints, and resumable steps. Both integrate cleanly with Claude Code for deployment and observability.

### How much does it cost to run an autonomous agent in production?
Method 1 (cron loops) is effectively free beyond your Claude Code subscription. Method 2 (routines) is also subscription-covered up to your daily run cap. Method 3 (Modal or Trigger.dev with the Agent SDK) typically runs $20–$300+ per month per agent depending on token usage — set hard `max_turns` and budget alarms before deploying.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
