**BRAND:** mejba.me
**TITLE:** Hermes + Claude Code: My Discord-Driven VPS Build System
**META TITLE:** Hermes + Claude Code: VPS Discord Deployment Workflow
**SLUG:** hermes-claude-code-vps-discord-deployment
**PRIMARY KEYWORD:** Hermes Claude Code VPS deployment
**META DESCRIPTION:** I rebuilt my dev workflow around Hermes Agent orchestrating Claude Code on a VPS, controlled from Discord, deploying to Vercel. Real commands, real tradeoffs.
**TAGS:** Claude Code, AI Agents, Hermes Agent, DevOps Automation, AI Workflow

---

It was 11:51 PM on a Tuesday when I typed `/build a Next.js task tracker with SQLite, deploy it to Vercel` into a Discord channel and walked away to make a coffee. By the time the kettle clicked off, my Hermes agent had spun up Claude Code as a sub-agent on my Hostinger VPS, scaffolded the project, pushed an initial commit to a fresh GitHub repo, and triggered a Vercel deploy.

Then it failed.

Not catastrophically. Just the specific, predictable way Next.js apps fail when you ship them to a serverless runtime carrying SQLite assumptions from local development. The build went green. The deploy completed in 31 seconds. The first request to `/api/tasks` returned a 500 with a write-permission error from the read-only filesystem Vercel hands every Lambda. I hadn't told Claude Code anything about runtime constraints — I'd just said "deploy it." The agent did exactly what I asked. The deployment platform had other ideas.

What happened next is the part I want to tell you about. Hermes received the failure log via the Discord webhook, fed it back into Claude Code as a follow-up task, and Claude Code spent the next four minutes refactoring the data layer to use Vercel KV instead of SQLite. New commit. New deploy. Green. Working endpoint. I came back from the kitchen to a Discord message that said, in essence, "ran into a serverless filesystem issue, swapped to KV, redeployed. Demo here." The whole loop — original prompt to working production deploy — took eight minutes and forty-three seconds. I wrote zero lines of code.

That's the system I want to break down. Not the marketing version. The actual implementation, the actual commands, and the parts that bit me — including the security tradeoff most tutorials skip over and the question I've been asking myself every week: when is this orchestration layer actually worth it, and when am I just adding moving parts?

## Why Hermes Sits Between Me and Claude Code

Let me address the obvious skeptic question first, because I asked it myself for about three weeks before I switched: if Claude Code can already plan, write, run, and deploy code, why am I putting another agent on top of it?

The short answer is that Claude Code is a brilliant terminal session and Hermes is a brilliant operations layer. They solve different problems.

Claude Code's strength is depth inside a single task. Hand it a codebase, a goal, and a terminal, and it'll plan, write, test, and iterate with a quality of reasoning that's genuinely hard to beat right now. But Claude Code, by itself, doesn't naturally do the messy operational glue. It doesn't watch its own deployments and react to failures. It doesn't accept commands from Discord. It doesn't run on a VPS while you're asleep, juggle three projects in parallel, or store API tokens encrypted on disk so you stop pasting them into chat boxes. You can build all of that around Claude Code with shell scripts and cron jobs. I've tried. The result is a fragile pile of glue you have to maintain yourself.

Hermes Agent is the glue, but a self-improving version of it. Built by Nous Research and released on February 25, 2026, it crossed 95,600 GitHub stars within seven weeks and is north of 103,000 as of this writing — the kind of trajectory you only see when something solves a problem people actually have. Hermes ships with native integrations for WhatsApp, Telegram, Discord, Slack, and now QQBot, encrypted token storage via `hermes config set`, a plugin system, an orchestrator mode that supervises sub-agents, and a learning loop that turns one-off tasks into reusable skills. It's MIT-licensed and runs on a $5 VPS.

When Hermes treats Claude Code as a sub-agent, you get the best of both. Claude Code does the actual engineering. Hermes handles the orchestration: where the work runs, who's allowed to trigger it, how failures get retried, where the secrets live, and which messaging platform you happen to be in when you want to check status. I run my orchestrator on the same VPS pattern I described in [my OpenClaw and Hermes two-agent system](/openclaw-hermes-multi-agent-workflow), and the architecture has held up under real load.

There's a deeper reason this pairing works, and it's about cost discipline. Claude Code on a long autonomous task burns tokens. Hermes is happy to run on cheaper models for the orchestration layer — most of my Hermes traffic flows through OpenRouter on lighter models, while Claude Sonnet 4.6 (currently $3 per million input tokens, $15 per million output tokens on OpenRouter) handles only the actual coding work. The split saves me real money on every project, and the savings compound as I run more parallel tasks.

But none of that matters if you can't get the thing running in the first place. So let's wire it up.

## The VPS Foundation: Why I Stopped Running Agents Locally

I ran Claude Code locally on my Mac for the first six months of using it. The pattern was fine for solo work but it broke the moment I wanted three things: long-running tasks that survive me closing the laptop, a stable address for Discord webhooks to hit, and a place where multiple parallel agent sessions could run without competing for my CPU during video calls. A VPS solves all three at once.

I use Hostinger. Their KVM 2 plan currently runs $6.99 per month introductory (renews around $11.99/month, which is the part the marketing pages are quiet about) and gives you 2 vCPUs, 8GB of RAM, NVMe SSD storage, and a dedicated IP. That's plenty for orchestrating a few Claude Code sessions in parallel. Their KVM 1 at $4.99/month with 4GB RAM works too if you're only running a single agent at a time — Hermes itself is shockingly light on resources because most of the heavy lifting happens inside the model providers, not on the box.

If you're a Hetzner or DigitalOcean person, those work fine too. The only requirements that matter are SSH access, Ubuntu 22.04 or newer, Node.js 18+, and outbound network access to your model provider. I'm specifying Hostinger because the SSH-via-browser console it ships with means you can install Hermes from any machine, including the iPad I'm writing this paragraph on.

Once you've got the VPS, the install command is genuinely one line. SSH in, then run:

```bash
curl -fsSL https://hermes-agent.dev/install.sh | bash
```

That bootstraps Node, installs Hermes globally, creates a `~/.hermes` config directory, and drops you into a first-run wizard that asks for your model provider keys. I always pick OpenRouter at this stage instead of going direct to Anthropic. The reason is simple: OpenRouter lets me swap models per-task without changing config, which matters when I'm routing planning work to a cheap model and execution to Claude Sonnet 4.6 or Opus 4.7.

Set the OpenRouter key with:

```bash
hermes config set OPENROUTER_API_KEY <your-key-here>
```

That command stores the key encrypted in `~/.hermes/secrets.enc`, not in plaintext, which matters more than people realize. I've audited too many tutorials where someone tells you to `export ANTHROPIC_API_KEY=sk-...` in your `.bashrc` and calls it good. That key now lives in your shell history, your tmux scrollback, and any backup of your home directory. Hermes's encrypted store keeps it out of all three places.

Now the part that took me the longest to get right: installing Claude Code as a sub-agent on the same VPS.

## Claude Code as a Hermes Sub-Agent

Claude Code is a Node.js application, distributed via npm under `@anthropic-ai/claude-code`. The standard install on the VPS is straightforward:

```bash
sudo apt update && sudo apt install -y nodejs npm
npm install -g @anthropic-ai/claude-code
claude-code --version
```

The version check matters. If you're seeing anything below 1.4.x as of May 2026, you're missing the sub-agent improvements that landed earlier this year, and the rest of this workflow won't behave the way I'm describing it.

The interesting part is authenticating Claude Code without an interactive desktop. The first time you run `claude-code` on a fresh VPS, it'll print an OAuth URL and wait for you to complete the flow in a browser. On a headless VPS, that's mildly annoying but not blocking — the URL works from any device. Open it on your laptop, complete the OAuth handshake, and Claude Code stores the token in `~/.config/claude-code/credentials.json` on the VPS. From that moment forward, your VPS has a logged-in Claude Code that survives reboots.

Now you wire Claude Code to Hermes as a registered sub-agent. The Hermes config for this lives at `~/.hermes/agents.toml`:

```toml
[agents.claude_code]
type = "subagent"
command = "claude-code"
args = ["--print", "--no-interactive"]
working_dir = "/home/mejba/projects"
allowed_tools = ["Read", "Write", "Edit", "Bash", "Glob", "Grep", "WebSearch"]
max_concurrent = 3
timeout_seconds = 1800
```

The `max_concurrent = 3` is the line I want to draw your attention to. It controls how many parallel Claude Code sessions Hermes is allowed to spawn. I started at 1 and worked up. At 3, my KVM 2 box runs warm but stable. At 5, I've seen Node OOM under load when all three sessions hit `npm install` at the same moment. Tune this to your VPS, not to whatever the docs example shows.

The `timeout_seconds = 1800` value means Hermes will kill any single Claude Code task that runs longer than thirty minutes. That sounds aggressive until you've watched Claude Code wedge itself in an infinite plan-execute-replan loop on a vague prompt. Better to fail fast and surface the problem than to let a stuck agent burn tokens overnight.

## GitHub Integration Without Pasting Tokens Into Chat

This is the section where most tutorials lose me. They tell you to paste your GitHub personal access token directly into the agent's chat window so it can clone repos and push commits. That's a recipe for accidental token leakage — into chat logs, into model provider history, into screenshots you take while debugging.

Hermes handles this differently. You generate a fine-grained personal access token from GitHub's developer settings — limit it to the specific repositories you want the agent to touch, and grant only `Contents: read/write`, `Pull requests: write`, and `Metadata: read`. Then store it via the same encrypted config command:

```bash
hermes config set GITHUB_TOKEN <your-fine-grained-token>
```

Inside your Claude Code working directory, you tell git to use the token via Hermes's environment injection rather than embedding it in a remote URL. My git config on the VPS looks like this:

```bash
git config --global credential.helper '!f() { echo "username=mejba"; echo "password=$GITHUB_TOKEN"; }; f'
```

That little shell function pulls the token from the environment Hermes injects at runtime, so your remote URLs stay clean (`https://github.com/user/repo.git`, no embedded credentials), your token never appears in `git config --list` output, and rotating the token is a one-line `hermes config set` away.

The fine-grained token piece matters. I've seen developers grant their automation a classic personal access token with full `repo` scope, which gives the agent write access to every private repository on their account, including client work and side projects. Don't do that. Generate a separate fine-grained token per project, scoped to that project's repository only. If the agent goes rogue or the VPS is compromised, your blast radius is one repo, not your entire GitHub presence.

## The Discord Bot That Drives Everything

The Discord layer is what makes this workflow feel like science fiction instead of cron jobs. I can be in a meeting, on the train, on my couch — anywhere I have Discord open — and ship code by typing natural language to a bot.

Setting it up takes about ten minutes the first time. Walk through this exactly:

1. Open the [Discord Developer Portal](https://discord.com/developers/applications), click "New Application," name it whatever you want.
2. In the left nav, click "Bot." On that page, scroll down to **Privileged Gateway Intents** and toggle on **Message Content Intent**. This is the one most tutorials forget. Without it, your bot can read that a message exists but cannot read what's in it. You'll spend an hour debugging "why does my bot ignore me" before you find this checkbox.
3. While you're on the Bot page, click "Reset Token" and copy the token immediately — Discord only shows it once.
4. Move to "OAuth2" → "URL Generator." Under **Scopes**, check `bot` and `applications.commands`. Under **Bot Permissions**, check `Send Messages`, `Read Message History`, `Use Slash Commands`, and `Attach Files`. Discord generates an invite URL at the bottom of the page.
5. Open that URL, pick the server you want the bot in, authorize it. It'll show up offline because we haven't started the process yet.

Back on the VPS, store the bot token in Hermes:

```bash
hermes config set DISCORD_BOT_TOKEN <token-you-just-copied>
```

Then enable the Discord transport in `~/.hermes/transports.toml`:

```toml
[transports.discord]
enabled = true
allowed_channel_ids = ["1198347...your-channel-id"]
allowed_user_ids = ["29384...your-discord-user-id"]
command_prefix = "/"
```

The `allowed_channel_ids` and `allowed_user_ids` are the security layer that nobody's blog post seems to highlight. Without those allowlists, anyone who can DM your bot can drive Claude Code on your VPS. With them, the bot only obeys you in the channels you've explicitly authorized. Get your Discord user ID by enabling Developer Mode in Discord settings, then right-click your username → Copy User ID.

Restart Hermes (`hermes restart`), and the bot comes online. Type `/status` in your authorized channel. If you see a response, the loop is closed. From this point forward, every command you type in Discord goes Discord → Hermes → Claude Code → back to Discord. You're driving production builds from a chat app.

## Vercel Auto-Deploy: The Last Mile

Vercel is the deployment target I default to for anything Next.js or static-frontend, and the integration is gloriously boring once it's set up. Connect your GitHub account to Vercel, give it access to the repository Claude Code will be pushing to, and set Vercel to auto-deploy on push to `main`. Done.

The Hobby plan is the one I run most experiments on, and the limits are worth knowing because they bite you in specific ways. As of May 2026, Hobby gives you 100GB of bandwidth per month, 1 million edge requests, and 100,000 serverless function invocations. The function timeout is hard-capped at 60 seconds, and bandwidth has no overage option — hit 100GB and your deployments stop until the next billing cycle. For experimentation and demos, all of that is fine. For anything where uptime actually matters, upgrade to Pro before you hit the wall, not after.

The Vercel piece adds one more lap to the loop: I type `/build` in Discord → Hermes routes to Claude Code → Claude Code commits and pushes to GitHub → GitHub webhook fires Vercel → Vercel builds and deploys → Hermes polls the Vercel deploy URL and reports back to Discord with the status. End-to-end, a clean deploy lands in around thirty seconds. The first time you watch that loop close on a project you scaffolded from a single chat message, it's hard not to laugh.

## The SQLite Mistake: A Tutorial in Why Serverless Architecture Bites

Now to the failure I described in the opening, because it's the most useful five minutes of this entire post.

When Claude Code scaffolded my Next.js task tracker, it did something perfectly reasonable: it picked SQLite for storage. SQLite is the right answer for almost every small Node.js project. It's zero-config, ships as a single file, and removes the whole "spin up a database" step. If I'd been deploying that app to a Hostinger VPS or a long-running container, it would have worked beautifully.

Vercel is not a long-running container. Vercel is serverless. Every API route runs inside an AWS Lambda function, and Lambda has rules: the filesystem is read-only except for `/tmp`, which is ephemeral and not shared across invocations. SQLite needs to write to its database file. The database file in my project lived at `./data/tasks.db`. When the Lambda tried to open that file for writing, the runtime returned `EROFS: read-only file system`, my API route returned a 500, and the deployment "succeeded" while being completely non-functional.

This is the specific failure mode you have to internalize before you let an autonomous agent ship code to serverless platforms: the agent doesn't know the runtime topology of your target unless you tell it. Claude Code knew Next.js. It knew SQLite. It did not, by default, know that this particular Vercel deployment forbids filesystem writes outside of `/tmp`.

The fix that Hermes's retry loop produced was correct: swap SQLite for a managed key-value store that's compatible with serverless cold starts. Vercel KV (or Upstash Redis, which is what backs it under the hood) gives you persistent storage that works across function invocations, with a free tier that comfortably handles demo traffic. Claude Code refactored the data layer in about four minutes, the redeploy went green, and the app worked.

But here's the deeper lesson, and it's the one I now bake into every Hermes prompt I write for production deploys: **tell the agent the runtime constraints up front**. My current default prompt template includes a target-specific paragraph for Vercel projects:

> Target deployment: Vercel serverless. Constraints: no filesystem writes outside /tmp, function timeout 60 seconds, no long-running background workers, prefer Vercel KV or external managed services for state. Use edge runtime where latency-sensitive.

That single block of context, prepended to any Vercel-targeted task, has eliminated the SQLite-on-serverless category of failure entirely. The agent isn't psychic. Tell it what the production environment requires.

## The Security Conversation Most Tutorials Skip

I'm going to be direct here because the breezy "look how easy it is" version of this article would be irresponsible. When you give Hermes full terminal access on a VPS — and that's exactly what you're doing when you let it spawn Claude Code with `Bash` in its allowed tools list — you're creating an automated agent that can run arbitrary shell commands on a machine that holds your GitHub token, your model provider keys, and write access to your production deploys.

Three things should make you stop and think before you flip this switch.

First, prompt injection is real and not theoretical. If your Discord bot is configured to read messages and your agent has terminal access, anyone who can convince your agent to execute a malicious command via crafted input has root-equivalent access to your VPS. The `allowed_user_ids` allowlist in your Hermes Discord config is your single most important defense here. If a stranger can DM your bot, you've already lost. Lock the channel and user allowlists down hard.

Second, a compromised VPS is a compromised everything. Treat the VPS the same way you'd treat any production server. Disable password SSH (key-only). Enable a firewall — `ufw enable && ufw allow 22 && ufw default deny incoming` is the absolute minimum. Run unattended-upgrades for security patches. Keep your `~/.hermes/secrets.enc` backed up encrypted, and rotate keys quarterly. None of that is paranoid. It's the baseline cost of running an automated agent that can shell into things.

Third, scope the agent's tools deliberately. The `allowed_tools` array in my Hermes config is `["Read", "Write", "Edit", "Bash", "Glob", "Grep", "WebSearch"]`. Notice what's not there: anything that could exfiltrate data outside the approved channels. I do not give the sub-agent access to arbitrary HTTP POST capabilities, mail clients, or cloud-provider CLIs that aren't relevant to the project. The narrower the tool surface, the smaller the blast radius if something goes wrong.

For client work specifically, I run a separate VPS per high-trust project. The cost is twelve dollars a month for two boxes instead of one, and the isolation means a single compromised project can't cross-contaminate the others. That's a cheap insurance policy.

The honest summary is this: this workflow gives an AI agent significant operational power. The productivity gains are real, but they're paired with risk that has to be managed actively. If you're not willing to do the security work, run Claude Code locally and accept the limitations.

## When Is Hermes Orchestration Actually Worth It?

I want to answer this directly because it's the question I get most often, and the answer isn't "always."

**Hermes orchestration is worth it when you have any two of the following:**

- You want to drive coding work from your phone or from messaging apps while you're away from your dev machine.
- You're running multiple agent sessions in parallel — three projects, four projects — and you need a supervisor that distributes work and surfaces failures in one place.
- You're doing scheduled or recurring agent tasks (nightly content generation, hourly market scrapes, deploy monitoring) where a cron-managed Hermes run is more reliable than reopening Claude Code locally.
- You need encrypted secret management because you're rotating client tokens, working across multiple GitHub orgs, or paranoid about credential hygiene.
- You're building automation that needs to react to external events — webhooks, deploy failures, schedule triggers — without you sitting at the keyboard.

**Hermes orchestration is overkill when:**

- You're working on a single project, at your desk, with reasonable working hours. Just open Claude Code in a terminal. The orchestration layer adds operational complexity you won't benefit from.
- You don't have any of the parallel-task or remote-control needs above. The infrastructure cost (VPS, time spent learning Hermes config, security maintenance) outweighs the convenience gains.
- You're early in your Claude Code journey. Learn Claude Code well first, then layer Hermes on top once you've identified specific friction points it would solve. Don't start with the orchestration layer — you won't know what to ask of it.

For me personally, the moment Hermes earned its place was when I started running Claude Code on three client projects in parallel and needed something to remember which session was which, route the right Discord messages to the right project channel, and keep the deploy logs separated. Before that, I was just adding moving parts.

## The Speed Numbers, Honestly

The David Andre tutorial I learned this workflow from claims 4x to 8x development speed gains. I want to be careful with that number because the way you measure it matters.

For greenfield projects where the work is "scaffold a CRUD app and ship it to a free tier" — yes, the speedup is real. What used to take me an evening of setup, scaffolding, and deploy debugging now takes under fifteen minutes from prompt to working URL. That's something like a 10x compression on that specific category of work.

For project work where the bottleneck is design decisions, product judgment, or domain understanding — the speedup is much smaller, maybe 1.5x to 2x. The agent isn't faster than I am at deciding what to build. It's faster at the typing-and-glue parts. When the constraint shifts to thinking, the orchestration layer doesn't help much.

For maintenance and refactoring on existing codebases — the gains are somewhere in the middle. Maybe 3x. Faster than manual, slower than greenfield because the agent has to load and reason about more existing context, and because I review more carefully on production code.

The composite, across the mix of work I actually do, has been a roughly 2.5x throughput increase since I moved to this stack. Real, but not the headline 8x.

## Where I'm Taking This Next

The pattern I'm experimenting with right now is voice-driven instead of text-driven. Hermes supports a Telegram voice-note transport, and I've started recording quick voice memos describing what I want built while I'm walking or driving. Whisper transcribes, Hermes routes the transcribed task to Claude Code on the VPS, and by the time I sit down at a real keyboard the scaffolding work is done. It feels less like commanding a tool and more like talking to a junior engineer who never sleeps.

I'll write that one up once I've shaken out the rough edges. For now, this is the system. Hermes on a $7 VPS. Claude Code as a sub-agent doing the actual coding. Discord as the cockpit. GitHub and Vercel as the rails. Encrypted tokens, allowlisted users, scoped tool access. A retry loop that learns from its mistakes.

If you're going to try this, start small. Don't wire up the whole stack on day one. Spin up the VPS, install Hermes, get a single Discord `/status` command working. Then add Claude Code as a sub-agent and run one trivial task — "create a hello-world Express app and commit it." Then add the Vercel auto-deploy. Each layer has its own failure modes, and you want to learn them in isolation before they compound.

There's one question worth sitting with as you decide whether to build this: in twelve months, how much of the engineering work currently sitting in your editor will be sitting in a chat window instead? My honest bet is: more than you think. Get fluent in this pattern now, while the cost of being wrong is just an evening's experimentation. Get fluent in it later, and you'll be paying tuition in client deadlines.

## Frequently Asked Questions

### What does Hermes Agent do that Claude Code can't do alone?
Hermes handles the orchestration layer around Claude Code: messaging-platform integrations (Discord, Telegram, WhatsApp, Slack), encrypted token storage, parallel sub-agent supervision, scheduled task execution, and cross-platform notifications. Claude Code remains the engineering brain; Hermes is the operations layer. For the full architecture breakdown, see the section above on why Hermes sits between you and Claude Code.

### How much does it cost to run Hermes plus Claude Code on a VPS?
A workable setup runs around $7 to $12 per month for the VPS (Hostinger KVM 2 introductory pricing), plus your model usage on OpenRouter or direct Anthropic billing. Claude Sonnet 4.6 currently costs $3 per million input tokens and $15 per million output tokens via OpenRouter, so per-task costs vary by complexity. Most days I spend $2 to $5 in model fees across the entire Hermes stack.

### Is it safe to give an AI agent terminal access on my VPS?
It's manageable, not free of risk. Lock down Discord channel and user allowlists, use fine-grained GitHub tokens scoped per repository, run a firewall, disable password SSH, and rotate keys quarterly. The security tradeoffs section above covers the specific controls I run. Treat the VPS as production infrastructure, not a sandbox.

### Why did the SQLite deployment fail on Vercel?
Vercel runs every API route on AWS Lambda, where the filesystem is read-only outside of `/tmp`. SQLite needs to write to a database file, so the runtime returns `EROFS: read-only file system` on every write. The fix is to use a managed store like Vercel KV or Upstash Redis. Always pass runtime constraints to the agent in the initial prompt — see the SQLite section for the exact prompt template I use now.

### When should I skip Hermes and just use Claude Code directly?
If you're working a single project at your desk during working hours, skip Hermes — just open Claude Code in a terminal. The orchestration layer pays off when you need parallel agents, remote chat-driven control, scheduled tasks, or encrypted multi-token management. For most solo developers in their first month with Claude Code, the local terminal is the right answer.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
