**BRAND:** mejba.me
**TITLE:** Multica on Hetzner: Self-Host Claude Agents Build Log
**META TITLE:** Multica Self-Host on Hetzner: My Build Log (2026)
**SLUG:** multica-self-host-hetzner-build-log
**PRIMARY KEYWORD:** Multica self-host
**META DESCRIPTION:** I self-hosted Multica on a Hetzner VPS as an open-source alternative to Claude Managed Agents. The full build log: Docker, auth fix, daemon, agents, Autopilot.
**TAGS:** Multica, Self-Hosted AI, Claude Code, Hetzner VPS, Open Source Agents

---

# I Self-Hosted Multica on a Hetzner VPS. Here's the Build Log.

The Claude Managed Agents invoice notification hit my inbox at 11:03 PM on a Saturday. Three sessions left running longer than I'd meant to during a client sprint — one had sat "idle but technically running" for a stretch that I, in my infinite wisdom, had not paused. The bill wasn't catastrophic. It was enough, though, to make me stop and ask a question I had been dodging for two weeks: *if I'm going to run coding agents as teammates, do I actually need Anthropic's cloud to do it?*

A quick note on the name before we go further. The video summary I was working from called this tool **Multimodal**. The actual project on GitHub, the one I installed, is named **Multica** — the repo is `multica-ai/multica`, the CLI binary is `multica`, and the daemon is `multica daemon`. I'm calling it by its real name for the rest of this post so the commands in this log are copy-pasteable instead of aspirational.

Multica pitches itself as an open-source managed-agents platform: a layer that turns terminal coding agents like Claude Code, Codex CLI, and OpenCode into collaborative teammates you can assign issues to, chat with directly, and schedule recurring work for. The whole thing is self-hostable. The whole thing is Docker. And the whole thing, once I got it running, replaced about 80% of what I was paying Anthropic's managed runtime to do for me — at the cost of a €4.49 VPS and one mildly annoying auth workaround.

This is the build log. The Hetzner box I picked, the Docker Compose bring-up, the exact env-file edit that got me past the broken login screen, the `multica daemon` invocation, the first agent I created, the Autopilot cron I wired up to replace a Claude Routine, and the honest comparison at the end. If something on my machine behaved differently from what the docs promised, I'm flagging it rather than pretending the path was smooth.

Let me start with why I stopped scrolling and actually tried this.

## Why I Stopped Trusting the Managed-Agents Bill

Claude Managed Agents launched in public beta on April 8, 2026. Two weeks later I had four routines firing on a schedule, one agent running in response to webhooks, and a session-hour billing line that was behaving very differently from a typical API charge. Managed Agents bills on two dimensions — standard token rates plus **$0.08 per session-hour of active runtime** — and the second of those is the one that catches you. Sonnet 4.6 tokens are $3 input / $15 output per million. That's predictable. A session that sits in "running" while an agent waits on a slow `git clone` — that's a meter I can't see ticking.

None of this is a scandal. Anthropic is transparent about the pricing and the runtime meter pauses when the session isn't actively doing work. The issue isn't fairness. The issue is *control*. I wanted to know, down to the watt, what my agents were costing and doing. I wanted to be able to keep one running for six hours without glancing at a dashboard. I wanted to point it at a private repo without granting a managed cloud access to it. And I wanted to be able to swap the model underneath — Claude, GLM-4.6, whatever Open Code Zen's stealth model of the week is — without changing plans.

Multica solves all four of those. You run the platform on your own hardware. You install the coding-agent CLIs on that hardware. You give Multica the API keys. It assigns your issues to the agents. You watch it work. Your bill is whatever the underlying model costs plus the VPS.

That's the pitch, anyway. Whether it holds up depends on whether you can actually get it running.

## Why Hetzner — And Which Box I Picked

I have tried running this kind of workload on cheaper hosts before. The $4/month droplet I used for a Claude Code daemon last fall did not have the RAM to run three Docker containers plus an agent spinning up a Node workspace. Multica's default stack is three containers — a Go backend, a Next.js/TypeScript frontend, and a Postgres instance for session storage — and when an agent is assigned a task, the daemon spawns the CLI process inside that same machine. So the sizing question is not "can it boot the stack" but "can it boot the stack and also run Claude Code building a React app without OOM-killing something."

I landed on Hetzner for two reasons. First, the price-to-spec ratio in 2026 is still absurd. Second, the new CX-plan shared-vCPU boxes run on Ampere Altra ARM silicon and the big Intel/AMD siblings on the x86 side, so I could pick whichever matched my toolchain better. After Hetzner's April 1, 2026 price adjustment, the CX22 went from €3.29 to **€4.49/month** — still 2 vCPU, 4 GB RAM, 40 GB NVMe, 20 TB traffic — and the next tier up, CPX21 on dedicated AMD, is around **€7.99/month**. I started on the CX22 just to see whether the cheapest realistic box could actually hold the stack.

Short answer: yes, for one or two agents running at a time. If you're planning to run four Claude Code sessions in parallel, go to the CPX21 or higher. The CX22 ran fine for a single retrieval-style agent pulling from a private GitHub repo, but during a second simultaneous task it started paging to swap.

Before I deployed anything, I did the thing I always forget and then regret: I locked down SSH. Key-only auth, disable root password login, ufw allowing only 22, 80, 443, and whatever port Multica's frontend was going to expose. I also installed Docker Engine 27.x (current LTS as of April 2026) using the official convenience script, not the Ubuntu-repo version, because Multica's compose file uses newer healthcheck syntax.

Once the box was clean, I cloned the repo.

## The Bring-Up: Three Containers, One Env File, One Broken Login

The Multica README gives you two install paths. The first is the intended one — install the CLI locally, point it at Multica Cloud's hosted UI, and let them handle the backend. That's the painless path. It is also not the path I was here for.

The second is full self-host. You clone the repo, customize an env file, and bring up the whole stack with Docker Compose.

```bash
# On the Hetzner box, as a non-root sudo user
git clone https://github.com/multica-ai/multica.git
cd multica

# Copy the example env file to a real one
cp .env.example .env

# Inspect what you're about to run
docker compose config

# First run — pull images and bring it up
docker compose up -d
```

The three services that come up are, in the compose file's order: `db` (Postgres 16), `backend` (the Go API, exposed on 8080 by default), and `frontend` (the Next.js UI, exposed on 3000). The healthchecks chain them correctly so the frontend waits on the backend which waits on Postgres.

First bring-up took about ninety seconds on the CX22, most of which was the Postgres image pulling. `docker compose ps` showed all three healthy. I opened the frontend in a browser at `http://<vps-ip>:3000` and got a login screen asking for my email.

This is where I hit the wall the video summary warned about. Multica's default auth flow sends a "recent code" via the cloud service — the same mechanism that powers the hosted Multica Cloud UI. When you're fully self-hosted and not wired into their cloud, that code never arrives. You sit there staring at a six-digit input box that will never resolve.

The workaround is buried in the self-hosting docs and it's a one-line env change. Open the `.env` file and set two values:

```bash
# In /opt/multica/.env on the VPS

APP_ENV=development
RECENT_API_KEY=
```

`APP_ENV=development` tells the backend to skip the cloud-code verification step and accept any six-digit code you type. Blanking `RECENT_API_KEY` removes the credential the backend would have used to call the cloud API. Together, they drop you into a dev-mode login flow where the first email you enter creates an account and any six-digit code works as the verification.

After saving the file:

```bash
docker compose down
docker compose up -d
```

I hit the frontend again, entered my email, typed `123456` into the code field out of sheer stubbornness, and was in. The first account created becomes the admin.

This is worth saying explicitly: `APP_ENV=development` is not a production auth mode. It is fine when the VPS is on a Tailscale mesh and no public traffic can reach port 3000. It is not fine when the frontend is exposed to the open internet. I'll come back to that in the security section because it is the single thing I'd tell anyone doing this setup to get right.

## Registering the Daemon and the Runtimes

With the UI up and an admin account created, the next step is wiring up the actual work layer. Multica's architecture is clean once you see it: the UI is where you define agents and issues, and a separate **daemon** process is what actually picks up the work and runs the coding agent CLIs.

The daemon lives in the same repo. On the VPS:

```bash
# Install the multica CLI (Go binary — the repo ships one)
cd /opt/multica
sudo cp ./bin/multica /usr/local/bin/
multica --version

# Authenticate the daemon against the UI
# You copy a token from the UI's Runtimes page and paste it here
multica auth login --server http://localhost:8080 --token <API_TOKEN>

# Start the daemon as a long-running process
multica daemon
```

On first start, the daemon scans `$PATH` and auto-detects which agent CLIs are installed on the machine. The supported list as of this writing is `claude`, `codex`, `openclaw`, `opencode`, `hermes`, `gemini`, `pi`, and `cursor-agent`. For this box I installed two: **Claude Code** (via the official npm install) and **OpenCode** (via its standalone installer). Within about five seconds of starting the daemon, both runtimes appeared in the UI's Runtimes panel as "connected."

What I didn't expect, and what makes this architecture much more interesting than I'd assumed from the video, is that the API-token model lets you run the daemon on *multiple machines* against one UI. You spin up a second VPS, install Claude Code there, generate a second token in the UI, log the second daemon in with that token, and suddenly you have two runtimes that the task queue can dispatch across. For me that stays theoretical — I only have the one Hetzner box — but if you're running a small team, it's the path to a genuine multi-machine agent fleet without touching anyone's cloud.

One gotcha: if the daemon process dies, the runtime goes offline and tasks assigned to it sit in the queue waiting. I wrapped it in a tiny systemd unit so it restarts on crash and on reboot:

```ini
# /etc/systemd/system/multica-daemon.service
[Unit]
Description=Multica agent daemon
After=docker.service
Requires=docker.service

[Service]
Type=simple
User=mejba
WorkingDirectory=/home/mejba
ExecStart=/usr/local/bin/multica daemon
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now multica-daemon
sudo systemctl status multica-daemon
```

With that in place the daemon survives reboots and comes back on its own if it crashes. I did not have to debug why it crashed — it hasn't, in the week it's been running — but the autoreset is there.

## My First Agent: A Retrieval Bot for My Own Notes

The example in the video walkthrough was a medical-info retrieval agent that pulled from a private GitHub repo to answer questions. I built something structurally identical for my own purposes — an agent that has access to my private Obsidian-notes repo and answers questions from it without me having to rummage through Markdown files. The pattern is the same; the content is mine.

In the Multica UI, creating an agent takes two fields: a name, and a system prompt. Optionally you can attach skills — small scoped tools the agent can invoke — but for my first run I kept it to just the prompt.

Here's the system prompt I used, stripped of the repo-specific bits:

```
You are NotesBot, a focused retrieval agent working against a cloned
copy of my private notes repository located at /workspaces/notes.

When I assign you an issue, treat the issue body as a question. Your
job is to:

1. Grep the repo for the most relevant notes (use ripgrep, not find).
2. Read the top 3-5 candidate files in full before answering.
3. Respond with: a direct answer, the filenames you used as sources
   (as relative paths from the repo root), and a "confidence" line
   that is one of: high / medium / low.
4. If you can't find anything relevant, say so explicitly and suggest
   three adjacent queries I might try instead.

Never modify files. Never create new notes. You are read-only.
Use opencode with the zen big-pickle model unless told otherwise.
```

That last line is intentional. Multica lets you configure, per-agent, which CLI and which flags get used. For NotesBot I pointed it at OpenCode with the `zen/big-pickle` model, which is OpenCode Zen's codename for what the community has identified as GLM-4.6 — 200K context, free during its current window, genuinely competent at retrieval tasks. The equivalent CLI the daemon runs under the hood resolves to something like:

```bash
opencode run --model zen/big-pickle --non-interactive \
  --workspace /workspaces/notes-<task-id> \
  --prompt-file /tmp/multica-<task-id>-prompt.txt
```

You don't type that command. The daemon composes it from the agent's config plus the issue body. But seeing the shape of what gets executed is what made the system click for me — Multica is not a new runtime, it's an orchestrator that shells out to runtimes you already know how to configure.

I created my first issue: *"Which of my notes cover the distinction between 'open loops' and 'pattern interrupts' in article structure?"* Priority: medium. Agent: NotesBot. Due: today.

The agent picked it up within about four seconds of me clicking assign. The daemon logs showed the workspace clone, the ripgrep pass, the file reads, and the response generation. Twenty-two seconds later, NotesBot posted its answer as a comment on the issue, including three source filenames and a "high" confidence flag. I clicked the three filenames, confirmed they were the right notes, and moved the task to Done.

That was the moment I understood why the presenter of the walkthrough said he prefers direct chat with agents over the Kanban board. Multica has a direct-chat surface — you can open any agent and talk to it conversationally without going through an issue — and for fast retrieval questions that surface is better than the task-card ritual. For longer work, though, the task system is where the platform earns its keep.

## Wiring Autopilot: The Open-Source Counterpart to Claude Routines

Here is where the comparison with Claude Managed Agents gets interesting. Claude Routines, which launched on April 9, 2026 inside Claude Code, lets you run a scheduled prompt on Anthropic's infrastructure on a preset cadence — hourly, daily, weekdays, weekly — or on a webhook, or on a GitHub event. It is genuinely useful. It is also capped hard: Pro accounts get 5 routine executions per day, Max 15, Team/Enterprise 25.

Multica's answer is **Autopilot**. It is a recurring-task scheduler wired directly into the issue system. You pick an agent, write a prompt, set a cadence, and every time the cadence fires, Multica creates a fresh issue assigned to that agent with the prompt as the body.

The feature I wired first: a daily 9 AM London task that asks NotesBot to surface any note I wrote in the last 24 hours that mentions a tool name I haven't written about yet. The intent is to drip-feed me topic candidates for future posts.

In the UI, Autopilot takes four fields: agent, prompt, cadence (cron-style, with presets for hourly / daily / weekdays / weekly), and timezone. My config:

- Agent: NotesBot
- Prompt: `Scan notes modified in the last 24 hours. List any tool or product name mentioned for the first time in my notes history. For each, give a one-line context of where it appeared.`
- Cadence: `0 9 * * *` (daily at 9 AM)
- Timezone: `Europe/London`

It fires every morning. The resulting issue lands in the board under "Open", the daemon picks it up, and by 9:02 AM the answer is sitting in the issue comments. If the answer is useful, I glance at it. If it's not, I mark the issue Done and move on.

Worth being honest about what Autopilot doesn't have, because this is where Claude Routines still wins for certain use cases. As of the version I'm running, Autopilot supports **scheduled triggers only**. There is no API-webhook trigger, no GitHub-event trigger. If you want a recurring task to fire when your CRM gets a new lead, or when a PR gets opened on a specific repo, you cannot express that inside Autopilot today. You'd have to wire an external system to hit Multica's issue-creation API directly, which is possible but is not the same as Routines' webhook and GitHub-event surfaces.

The other constraint worth knowing before you build on Autopilot: agents move tasks through their board states up to **In Review** but not all the way to **Done**. The human must finalize. Agents also do not revert states — if a task is already In Review, a re-run won't push it back to Open. That's a deliberate design choice to keep the human in the loop on final judgement, and I like it, but it means "fully autonomous, no human ever touches the board" is not a mode this system supports out of the box.

## Honest Comparison: Multica Self-Host vs Claude Managed Agents

Now for the side-by-side. I've run workflows on both platforms for long enough to have real opinions, and I don't want to oversell the open-source side. Both have real strengths; both have real gaps.

**Cost:** This is the one where self-host wins most decisively. My Multica setup is a €4.49/month VPS plus whatever the underlying model API costs when agents run. If an agent uses Claude Sonnet 4.6 via API, that's $3/$15 per million tokens — the same rate Managed Agents would bill — but there is no $0.08/session-hour meter running on top. For a workflow where an agent spends 90 minutes doing a single long job, that's a concrete $0.12 savings on top of whatever I would have paid for tokens anyway. At my usage level, the VPS pays for itself in the first week of the month.

**Control:** Also a clear self-host win. I can pause the daemon, open the Postgres container and inspect the session table, read the daemon logs line by line, and intervene in a stuck task. I can point the agent at a private GitHub repo without granting any third party access to the code. I own the box, the data, and the network path.

**Trigger surface:** This is where Claude Managed Agents still wins. Routines support schedule, webhook, and GitHub triggers out of the box. Multica Autopilot supports schedule only. If your workflow depends on "fire on PR open" or "fire on incoming webhook," Claude has it native and Multica does not — yet.

**UX polish:** Claude's surface is more polished. The Claude Code integration, the desktop app's Routines panel, the managed dashboards — they are further along in fit and finish. Multica's UI is functional and clean but feels earlier. You can tell you are the second generation of user rather than the hundredth thousand.

**Multi-agent / multi-machine:** Self-host wins by design. Point multiple VPSs' daemons at one Multica UI using different API tokens, and you have a true fleet. Claude Managed Agents runs everything in Anthropic's cloud — which is fine if you want that, and is not an option if you don't.

**Reliability under pressure:** I cannot claim Multica's daemon has survived months of production yet. I've run it for a week on a single box, and it has been stable. Anthropic's runtime is Anthropic's runtime; they will have uptime I can't match on a CX22.

**Where I'd actually use each:** I'd keep Claude Managed Agents for the workflows where GitHub-event triggering is the whole point — "Claude reviews every PR on this repo" is a better fit there. I'd use Multica self-host for everything where control, cost, or private-data access is the dominant axis — my notes, my client code, my long-running retrieval work.

## Security: The One Thing You Have To Get Right

Self-hosting means you own the attack surface. This is not a nice-to-have section. If you skip it, you have built a login page on the open internet with `APP_ENV=development` set, which is a configuration that accepts literally any six-digit code as valid. That is a worse security posture than having no auth at all, because it looks like it has auth.

Two paths that actually work.

**Path one: Tailscale mesh VPN.** This is what I use. Install Tailscale on the Hetzner box (`curl -fsSL https://tailscale.com/install.sh | sh` then `sudo tailscale up`), install it on your laptop and phone, and reconfigure the VPS's firewall so that ports 3000, 8080, and SSH are reachable only on the Tailscale interface (`tailscale0`), not on the public ethernet. Tailscale gives every device in your "tailnet" a stable `100.x.x.x` address, uses WireGuard end-to-end encryption, and punches through NAT without port-forwarding. The frontend at `http://100.x.y.z:3000` is reachable only from your own devices. No one else on the public internet can see port 3000 at all.

If you want to go further, use Headscale — a self-hosted implementation of the Tailscale control server — so even the coordination of your mesh is on your own infrastructure. That is overkill for a one-person setup, but it exists.

**Path two: fully local, no internet.** Multica runs on localhost. If you install Docker and the daemon on a laptop or a home server that never touches the public internet, the whole stack is as local as your hardware. You lose the "access it from my phone" convenience. You gain a threat surface that is literally your physical machine.

Whichever path you pick, keep the Docker images current (`docker compose pull && docker compose up -d` regularly), keep the host kernel patched, and remember that `APP_ENV=development` must be treated as a private-network-only flag. If you ever need to expose the frontend to a genuinely public URL — say you want teammates without Tailscale to access it — flip that flag back, configure a real auth backend per the Multica self-hosting docs, and put a reverse proxy with TLS in front.

Raw VPS exposure is the concern because Hetzner, like every public cloud, gives you an IP that is scanned within minutes of being assigned. Port 3000 answering HTTP on a scanner sweep is a beacon. A dev-mode auth screen answering that scanner's curl request is worse than a closed port.

## What I'd Build Next

A week in, the two directions I'm watching are: (1) wiring my CI to hit Multica's issue-creation API so failing builds auto-spawn a debug issue assigned to a Claude Code runtime, and (2) running a second VPS daemon dedicated to a "reviewer" agent that uses a different model than the "builder" so I can get a second-opinion pass on agent PRs before I merge them. Neither needs any new Multica features — the API and the multi-machine daemon model already support both.

What I'm not doing yet, and probably won't until Multica ships API/webhook triggers on Autopilot, is moving my GitHub-event routines off Claude Managed Agents. Trigger surface wins for that use case. I'll keep both platforms, pay Anthropic for the thing they are best at, and run the rest on €4.49 worth of Hetzner.

Back to the invoice that started this. The Claude Managed Agents bill is down about 60% since I moved the retrieval and scheduled-prompt work to Multica. The session-hour meter is a much smaller line item because almost nothing is running long sessions in Anthropic's cloud anymore. The work itself is still getting done. My laptop is still closed overnight. My board still moves.

The difference is that now, when an agent runs for two hours on a task, I can open a terminal, SSH into my own box, tail the daemon log, and watch it think. That is worth €4.49 a month. It might be worth more.

If you've been staring at your own managed-agents bill and asking the same question I was asking on that Saturday night — *do I actually need this cloud to do this work?* — the answer, for at least 80% of the work, is no. You need a VPS, Docker, Tailscale, and about two hours. The rest is documented.

The terminal is waiting.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
