**BRAND:** mejba.me
**TITLE:** MCP for Claude Code: What to Install, What to Cut
**META TITLE:** MCP for Claude Code: What to Install, What to Cut
**SLUG:** mcp-claude-code-install-cut
**PRIMARY KEYWORD:** MCP for Claude Code
**META DESCRIPTION:** My working Claude Code MCP setup — what stays installed, what got cut, real .mcp.json examples, and how the 10% tool-search threshold changed everything.
**TAGS:** Claude Code, MCP, AI Agents, Developer Tools, Tutorial

---

# MCP for Claude Code: What to Install, What to Cut

Last Tuesday I opened a fresh Claude Code session, typed `/mcp`, and counted twelve servers loaded. Filesystem. GitHub. Playwright. Notion. Linear. Sentry. A Supabase server I'd installed for a project that wrapped six months ago. Two custom STDIO scripts I couldn't remember writing. A Slack server I'd disabled in three other projects but somehow re-enabled here. And — embarrassingly — two duplicates of the same Postgres server I'd added at different scopes.

I ran the session anyway. Context utilization at session start: roughly 14% of the 200K window. Empty conversation. No code read. No file opened. Nothing actually *done*. Just twelve servers worth of tool definitions sitting there, taxing every single message I'd send for the rest of the session.

That's the part nobody mentions when they pitch you on MCP for Claude Code. The setup is genuinely beautiful — a single command to wire Claude into your tools, no API keys to copy-paste, no middleware to write. But the protocol charges rent. Every server you install pays in context tokens whether you use it that day or not. Stack twelve of them carelessly and you've kneecapped your model before the conversation starts.

I want to walk you through the working setup I use right now — what I keep installed, what I cut, why the three scopes matter more than most write-ups admit, and how the 10% tool-search threshold quietly changed the calculus on which servers are worth their weight. This is the post I wanted six months ago when I was trying to figure out what to actually run, not what to demo at a conference.

Let me start with the part most articles skip: why MCP exists in the first place, and what changes when you understand that clearly.

## The "Agent Without Arms" Problem

A frontier model on its own is brilliant in a specific, narrow way. It can reason about your code. It can rewrite a function. It can explain why a regex is failing and propose three alternatives. What it cannot do — without help — is run the regex against your actual repo, file the bug, ping the on-call engineer in Slack, and update the Linear ticket.

Anthropic shipped the Model Context Protocol in late 2024 to fix exactly this gap. Their pitch was simple. The model is a brain. Tools are arms and legs. MCP is the standardized nervous system that lets the brain reach into your filesystem, your GitHub repo, your browser, your database, your Slack workspace — without you writing custom integration code for every connection.

The mechanic is straightforward. An MCP server exposes one or more tools. Each tool advertises a JSON schema describing its name, parameters, and what it does. When you start a Claude Code session, the runtime injects every available tool's schema into the model's context. The model picks the right tool, generates a structured call, and the runtime executes it on the model's behalf. Add a server. Get capabilities. Repeat.

For three or four servers, this feels like magic. I wrote about exactly that magic last year in [the three MCPs that turned Claude into my operations hub](https://www.mejba.me/blog/must-have-mcps-claude-code) — Canva, Zapier, and Stripe wired into one conversation. With a small server count, the protocol disappears and you just talk to your tools.

The trouble starts when "small" becomes "I added one more last week and now I have fifteen." That's where the working knowledge of MCP actually lives. Not in the demo. In the cleanup.

## The Two Server Types You'll Run

Before we get to the install/cut decisions, you need a clean mental model of how MCP servers actually run. There are two transport types, and the choice matters more than most setups admit.

**STDIO servers** run as a local process on your machine. Claude Code launches the server when the session opens, communicates with it over standard input/output, and shuts it down when the session ends. The Filesystem server is STDIO. So is the official GitHub MCP server in its local mode. So is anything you `npx -y` into existence.

**HTTP servers** are remote — hosted by a provider, accessed over the network. You authenticate once (usually OAuth), and Claude calls the server as a regular API would. Anthropic's Connectors Directory leans heavily on HTTP. So do most vendor-hosted MCPs — Notion, Linear, Slack, the official Stripe connector.

The trade-off looks academic until you've been burned by it. STDIO is fast — local, no network hop, no auth round-trip per call — but the install friction is real. You're running someone else's code on your machine, with whatever permissions Claude inherits. HTTP is hands-off — the provider handles updates, scaling, and security — but you pay in latency and you've now made your dev session depend on someone else's uptime.

My rule of thumb after a year of this: STDIO for anything that touches your local environment (filesystem, your local Postgres, your shell, browsers spun up by Playwright). HTTP for anything you're already authenticating to in the cloud (GitHub, Notion, Linear, Sentry). Mixing them is fine. Mixing them *without thinking* is how you end up with three servers all trying to read your repo from different angles.

There's a third category worth flagging — STDIO servers that are really HTTP wrappers in disguise. The `npx -y airtable-mcp-server` pattern, for example, runs locally but immediately dials out to Airtable's API on every call. That's mostly fine, but you should know it. You're paying STDIO install costs and HTTP latency.

We'll get to which servers are worth either cost in a minute. First, the command surface.

## The Three Commands You'll Actually Use

For all the pages of MCP documentation floating around, Claude Code's MCP surface comes down to three commands you'll touch every week. Master these and you're 90% of the way there.

### `claude mcp add`

This is how you install a server. The full form looks like this:

```bash
claude mcp add [options] <name> -- <command> [args...]
```

The double-dash matters. Everything before it configures Claude Code's view of the server (transport type, environment variables, scope, headers). Everything after it is the actual command Claude runs to start the server.

A real STDIO install:

```bash
claude mcp add --transport stdio --scope user filesystem \
  -- npx -y @modelcontextprotocol/server-filesystem /Users/mejba/Code
```

A real HTTP install:

```bash
claude mcp add --transport http --scope user notion \
  -- https://mcp.notion.com/mcp
```

The most common mistake I see — including from me, multiple times — is forgetting that all flags must come *before* the server name. Put `--scope user` after `filesystem` and the CLI either errors out or silently treats your scope flag as part of the server's argv. Watch the order.

### `/mcp`

Inside an active Claude Code session, type `/mcp` at the prompt. You get a live view of every server attached to the current session, its status (connected, failed, disabled), and a menu to disable any server you don't need for the current task without uninstalling it.

This is the most underused command in the entire Claude Code surface. Most people install ten servers and never look at this screen again. That's wrong. `/mcp` is your weekly hygiene check. You should be opening it, looking at the list, and asking yourself: "What am I actually doing in this session, and does each of these earn its keep?"

The disable action is non-destructive. The server stays installed at whatever scope. It just doesn't load tool schemas into context for *this* session. When you switch projects, the disable resets. When you re-enable, you don't reinstall anything. It's the cleanest possible "context diet" lever, and it's hidden in plain sight.

### `claude mcp remove`

Permanent uninstall. Use it when you're sure. I run this monthly on user-scope servers I haven't touched in a quarter. The list always surprises me — there's always a server I installed for one experiment and forgot to clean up.

That's the entire CLI surface for 95% of MCP work. Add. List. Remove. Plus `/mcp` for live management. Anything more elaborate — global config files, scope migrations, environment variables for auth — is rare enough that you can look it up when you need it.

Now the part that actually changes how you architect your setup.

## The Three Scopes Are a Workflow Decision

Claude Code lets you install an MCP server at one of three scopes, and most people pick the default without thinking. That's fine until you join a team, or run multiple projects, or try to keep your laptop's setup in sync with your travel machine.

Pick the wrong scope and you're either re-typing the same install command in every project, or you've leaked a personal API key into a config file that's now in git history.

### Local scope (default)

Per-user, per-project. The server is available only to you, only in this project. Claude stores the configuration in a project-local file that *isn't* checked in.

I use Local for:

- Project-specific Postgres or MySQL servers (each project has its own database)
- One-off experimental servers I'm evaluating
- Anything with a credential I don't want bleeding into other projects

The Local default is well-chosen. If you're not sure what scope to pick, Local is almost always the right answer. It scopes the blast radius of a misbehaving server to one repo.

### User scope

Available globally — every project you open inherits it. The configuration lives in your home directory, not in the project, so it never touches git.

User scope is for the servers that are *yours*, not the project's. My short list:

- **Filesystem** — pointed at my code root, scoped to read-only by default
- **GitHub MCP** (HTTP) — I use it across every project that touches GitHub
- **Playwright** (STDIO) — I want browser automation available everywhere
- **A small custom STDIO server** I wrote that exposes my notes vault

That's it. Four user-scoped servers. The temptation to add more is constant, and I resist it because anything user-scoped pays its context tax in *every* project, every session, until I uninstall it.

### Project scope

The interesting one. Project scope writes the server configuration to `.mcp.json` at the project root, which means it's checked into git. When a teammate clones the repo and opens Claude Code, they get the same MCP servers wired up automatically.

This is the unlock for team consistency, and it's the scope most write-ups underweight. If you're working alone, you can ignore Project scope and live a happy life. The moment you have a teammate — or a future you who'll open this repo on a different laptop — Project scope is how you ship a reproducible Claude Code environment along with your code.

A `.mcp.json` I'd actually commit to a real project looks like this:

```json
{
  "mcpServers": {
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    },
    "sentry": {
      "type": "http",
      "url": "https://mcp.sentry.dev/mcp"
    },
    "linear": {
      "type": "http",
      "url": "https://mcp.linear.app/mcp"
    }
  }
}
```

Three servers. Each one earns its place by the team's actual workflow. Playwright because we run browser smoke tests Claude can drive. Sentry because production errors are part of the dev loop. Linear because tickets are the source of truth for what's being built. Notice what's *not* in there — no GitHub server (we use the `gh` CLI, more on that in a minute), no Slack server (overkill for code work), no database server (every dev runs a different local Postgres).

The `.mcp.json` file is your team's MCP contract. It's small, it's reviewable in a PR, and it locks the server lineup the same way `package.json` locks dependencies. Onboarding a new dev becomes "clone the repo, run Claude Code, approve the servers." That's it.

One concrete warning: never put credentials in `.mcp.json`. If a server needs an API key, the right pattern is to reference an environment variable (`${env:LINEAR_API_KEY}`) and document the env in the README. The OAuth-based HTTP servers (Sentry, Linear, Notion) sidestep this entirely — each developer authenticates once, in their own browser, and Claude stores the token locally.

## Context Window Economics: The Real Cost of an Idle Server

This is the section I wish someone had hit me with two months into my Claude Code journey. Skip the rest of the article if you have to. Read this one.

Every MCP server you install advertises one or more tools. Every tool advertises a JSON schema. At session start, Claude Code packages all of those schemas and injects them into the model's context window so the model can decide which tool to call when. That injection happens whether you use any of the tools that session or not.

Servers vary wildly in how much context they cost. The official Filesystem server is small — maybe 1-2K tokens of schema. A full-fat GitHub MCP server is enormous — multiple thousands of tokens, sometimes more, depending on which surface area is enabled. A poorly-designed server can blow through ridiculous numbers. CodeRabbit's engineering team measured single MCP servers eating 55,000+ tokens of schema upfront in production setups. That's not a typo. Fifty-five thousand tokens consumed before the user types anything.

Stack four or five servers carelessly and you can be 5-10% of context utilization deep before the session begins. Stack ten and you're past 10%. Stack twenty — which is easier than it sounds — and you've put yourself in a hole the model has to dig out of for every single request.

The math shows up in two places. First, raw token cost — every prompt now ships those schemas to the API again, multiplied across every turn of the conversation. Second, and more importantly, *attention dilution*. The model has to spread its attention across every tool definition you've loaded. With three tools that's trivial. With forty, the model's tool-selection accuracy starts degrading — sometimes catastrophically. I went deep on this in [why MCP is quietly dead at scale](https://www.mejba.me/blog/mcp-is-dead-corsair-rag-tools), but the short version is that the architecture wasn't designed for the server counts people are actually accumulating.

Once you internalize this, the install/cut decision becomes a context budget question, not a "is this server cool" question. You have, roughly, 20K tokens of comfortable schema budget in a 200K window before things start getting weird. Spend it carefully.

## The 10% Tool-Search Threshold Changed the Game

In January 2026, Anthropic shipped a feature that quietly rewrote the calculus on every section above. They called it MCP Tool Search.

The mechanic: when your installed MCP tool schemas would consume more than 10% of your context window, Claude Code stops loading every schema upfront. Instead, it loads a lightweight search index. When the model needs a tool, it searches the index, fetches only the relevant tool's full schema, and uses it for that call. Schemas come in on-demand, not in bulk.

The numbers from real setups are striking. Running Meta MCP, Shopify AI Toolkit, and Higgsfield MCP simultaneously eats around 12K tokens at session start without Tool Search. With Tool Search active, it drops to roughly 600 tokens. That's a 95% reduction on a three-server lineup most ecommerce operators would want loaded together. On my own twelve-server setup at peak, the savings were more dramatic — I went from ~28K tokens of upfront schema to roughly 1.4K.

It sounds like free wins. It mostly is. There are two catches.

**Catch one: latency.** When the model needs to call a tool that hasn't been pre-loaded, it adds a search-and-fetch hop. For most tools you'd want to call once or twice in a session, this is invisible — sub-second, indistinguishable from normal latency. For tools you're hammering in a loop (Filesystem, Playwright during scraping runs), the per-call overhead compounds.

**Catch two: tool discoverability.** Pre-loaded schemas don't just teach the model how to call a tool — they remind the model that the tool *exists*. With Tool Search, some tools become invisible to the model unless the user's prompt contains a strong hint that suggests searching for them. A tool you'd previously rely on the model to "remember" might now require you to say its name explicitly.

In practice, neither catch matters for 90% of work. Tool Search is enabled by default as of 2026, and unless you're doing something specialized, you should leave it on. But understand what changed: with Tool Search active, the marginal cost of leaving an unused server installed dropped from "real" to "almost zero." That's good news for anyone with a sprawling setup.

It's also bad news, because it removes the forcing function. Without the context tax, there's no natural pressure to clean up. Servers accumulate. The list grows. And then one day you switch to a model run that hits a quirk where Tool Search degrades, and you're suddenly carrying twenty-three servers worth of weight again.

The discipline still matters. The tool just makes it cheaper to be sloppy. Don't be sloppy.

## Skills: The Lighter Cousin Worth Knowing About

Anthropic shipped Agent Skills around the same time as Tool Search, and they fundamentally changed the "should I install an MCP server" decision for a chunk of common workflows.

A Skill is, in the simplest framing, a markdown file with a name, a description, and instructions for Claude. The crucial difference from MCP: only the *name and description* of a Skill load into context until Claude decides to invoke it. The full instructions (which can include code, examples, and detailed protocols) only load when triggered.

Compare that to an MCP server, which loads every tool schema upfront. A 50-line Skill that does what a small MCP server does costs roughly 30-50 tokens of context until activated. The MCP equivalent might cost 2-3K. That's a 50-100x context efficiency advantage in favor of Skills for most workflow-style tasks.

The trade-off: Skills can't actually *call* external services on their own. They're instruction sets. If a Skill needs to hit an API, it does it through tools the model already has — Bash, WebFetch, or another MCP server that's loaded. So Skills are great for *workflows* (a deployment checklist, an SEO audit protocol, a code review pattern). MCP servers are still essential for *new capabilities* (a connection to a system Claude has no other way to reach).

The decision tree I use:

- Does this need to talk to an external service Claude can't reach today? → MCP server
- Is this a workflow on top of capabilities Claude already has? → Skill
- Could I do this with a CLI command Claude already runs through Bash? → CLI, no MCP, no Skill

I covered the Skill side in depth in [Claude Code Skills worth installing](https://www.mejba.me/blog/claude-code-skills-worth-installing) — most of the productivity wins I've gotten in the last six months were Skills, not MCP servers. The lesson: don't reach for MCP first. Reach for the lightest tool that solves the problem.

## CLI Beats MCP for Most Things

This is the section that's going to ruffle feathers, and I'll defend it with my actual setup.

For a huge percentage of the work you'd reach for an MCP server to do, a regular command-line tool — invoked through Claude Code's Bash tool — is the better choice. The math is stark.

A CLI tool sits *outside* the context window. The schema for the Bash tool itself is small and is going to be loaded anyway. When Claude invokes `gh pr list --state open` through Bash, no GitHub schema needs to be in context — the `gh` CLI knows how to talk to GitHub, parses the result, and returns text that Claude reads. The model didn't need to know GitHub's API. It only needed to know how to invoke a command and read its output.

Compare that to a GitHub MCP server. Even a well-designed one carries thousands of tokens of schema covering the GitHub surface — issues, PRs, files, branches, commits, releases, comments. All of that schema sits in context whether you're actually using it or not. Tool Search helps, but the per-call overhead of search-and-fetch starts to compete with just letting Claude write a `gh` command and read the JSON output.

My current CLI-first lineup that replaces MCP servers I've actively cut:

- **`gh`** for everything GitHub. PRs, issues, releases, branch operations. Nothing the GitHub MCP server can do that `gh` can't, except for niche fine-grained API calls I rarely need.
- **`aws`** and **`gcloud`** for cloud operations. The cloud-vendor MCP servers I tried were either limited or huge. The CLIs are battle-tested.
- **`docker`** for containers. Self-explanatory.
- **`psql`** and **`mysql`** for databases. Faster, more transparent, no schema bloat. Claude can write a query, run it, read the result, iterate. Database MCP servers existed in my setup for two months and added nothing.
- **`ffmpeg`**, **`yt-dlp`**, **`exiftool`** for media. The MCP servers in this category are mostly thin wrappers around these CLIs anyway.

I went deep on this philosophy in [10 CLI tools I use daily with Claude Code](https://www.mejba.me/blog/cli-tools-claude-code) and I haven't changed my mind in the months since. The CLI-first instinct keeps your MCP server count low, your context budget healthy, and your dependency surface manageable. Every CLI you already know is a context-free capability for Claude.

The MCP servers worth installing are the ones with no good CLI equivalent — Playwright, Notion, Linear, custom internal services. For everything else, learn the CLI.

## My Actual Working Setup

Time to put a stake in the ground. Here, with no editorializing, is the MCP setup I'm running on May 10, 2026, with the rationale for each.

**User scope** (loaded in every project):

1. **Filesystem** — STDIO. Pointed at my code root with read-only default. Cost: ~1.5K tokens. Earns its keep on every session that reads multiple files. The Filesystem server is the closest thing MCP has to a universal default.

2. **Playwright** — STDIO via `npx -y @playwright/mcp@latest`. Cost: ~3K tokens. Browser automation, accessibility tree snapshots, scraping. There's no CLI substitute that gives Claude the same level of structured page understanding. Worth every token.

3. **GitHub** (HTTP) — Disabled by default, enabled per-session via `/mcp` when I'm doing PR-heavy work. Cost when active: meaningful. Most of the time I lean on `gh` instead. The MCP server earns its keep when I'm doing complex multi-repo operations or fine-grained API work, and gets disabled the rest of the time.

**Project scope** (committed to `.mcp.json` per repo, varies):

4. **Sentry** — HTTP. On for any project with production traffic. Off otherwise. Sentry's MCP is one of the cleanest implementations I've used.

5. **Linear** — HTTP. On for client projects where Linear is the source of truth. Off for personal stuff.

6. **A custom STDIO server** I wrote for one specific client that exposes their internal API. Lives in their repo. Never spreads beyond it.

**Local scope** (one-offs, evaluated, often killed):

7. Whatever I'm experimenting with this week. Currently nothing — I cleared the local list two days ago when I noticed I had three MCPs installed for projects that had ended.

What I cut:

- **The GitHub MCP at user scope.** Replaced with `gh`. Save: meaningful tokens on every session.
- **A Slack MCP I'd run for two months.** Realized I was using Slack as a notification destination, not a data source. Replaced with a tiny script that posts to a webhook when I want a ping.
- **A Notion MCP at user scope.** I rarely need Notion in coding sessions. Moved to Project scope on the one project that genuinely needs it.
- **A Postgres MCP server.** Replaced with `psql`. Faster, more transparent.
- **Three different "AI memory" MCPs.** None of them did anything that disciplined Skills and good CLAUDE.md files don't do better.
- **A weather MCP.** I have no idea why I installed a weather MCP. I have no idea what I was thinking.

The total user-scope context cost of my setup is roughly 5-6K tokens at session start. With Tool Search active, the effective cost when none of those tools are needed drops to a few hundred tokens. That's a working budget that leaves room for the actual work.

## Best Practices I Wish I'd Followed Earlier

A handful of habits that took me longer than they should have to internalize:

**Run `/mcp` on every fresh project.** Treat it as a setup checklist. What's loaded? Does each one earn its keep for *this* project? Disable anything that doesn't.

**Audit user-scope monthly.** User scope is the hidden tax. Every server you add there costs you forever. Once a month, run `claude mcp list` (or your CLI's equivalent), look at the user-scope servers, and uninstall anything you haven't used in 30 days.

**Prefer Project scope for team consistency.** If you're working with anyone — including future you on a different laptop — Project scope and a committed `.mcp.json` is how you ship a reproducible Claude Code environment. It's the smallest, cheapest piece of team infrastructure you'll ever ship.

**Never put credentials in `.mcp.json`.** Use env var references. Document the required env in the README. Make the OAuth-based HTTP servers your default for anything authenticated.

**Don't install a server you saw on Twitter without testing the context cost.** This is the trap that caught me dozens of times. Someone screenshots a cool MCP demo. You install it. It does the cool thing once, and then sits in your context tax for the next four months. Test every server's context cost before you commit it to user scope. If you can't tell, install at Local scope and move it to User only after you've used it ten times.

**Disable before you uninstall.** When you think a server is dead weight, disable it via `/mcp` first. Run for a week without it. If you don't notice, *then* remove it. This prevents the "I'll need it eventually" reinstall churn.

**Watch for duplicates across scopes.** If a server is at User scope and also in `.mcp.json`, you can end up with subtle conflicts. The CLI mostly handles this gracefully, but the safe rule is: pick one scope per server and stick with it.

## What MCP Is Good For (And What It's Not)

After fifteen months of running MCP in production, here's the honest sort.

MCP is excellent for:

- **Filesystem and code-aware tools.** This is the original use case and it's still the best one. Filesystem, Git, Playwright — these earn their context cost.
- **Vendor-hosted business services with no good CLI.** Notion, Linear, Sentry. The HTTP MCP versions are clean, OAuth-based, and offer capabilities that would be painful to script from scratch.
- **Custom internal tools at companies.** This is the underrated use case. If your team has internal services, building a small MCP server that exposes them is genuinely the best way to give Claude controlled access. Project-scoped `.mcp.json` ships the connection along with the code.

MCP is the wrong choice for:

- **Anything a mature CLI already does well.** GitHub, AWS, GCP, Docker, Postgres, MySQL, Redis. Use the CLI.
- **Workflows that are really instruction sets, not capabilities.** Use a Skill.
- **Services you'll only call twice in your career.** Just write a one-off script. The MCP install cost isn't justified.
- **Anything where the schema is enormous and the API is small.** Some MCP servers are absurd in their schema-to-utility ratio. Inspect before you commit.

The pattern that consistently delivers: a small, intentional MCP lineup at User scope (3-5 servers), Project-scoped servers in `.mcp.json` for team-shared tooling, aggressive use of CLI tools and Skills for everything else, and `/mcp` as a regular hygiene habit.

## The Real Test

Here's the one-line gut check I run before installing any MCP server now: *Will I use this server's tools at least once a week, in this project, for the next month?*

If the answer is no, the server doesn't go to User scope. Maybe it goes to Project scope if it's a team thing. Maybe it stays at Local for a week-long evaluation. Maybe — most often — it doesn't get installed at all, because there's a CLI command, a Skill, or a small script that does the same job for a fraction of the context cost.

The reflex most engineers have around MCP is the same reflex we have around npm packages. See cool thing. Install. Move on. That reflex is wrong here. MCP servers aren't dependencies you install once and forget about. They're tenants in your context window who pay rent in tokens, and every one of them is up for review every time you start a session.

Run `/mcp` right now. Look at the list. Ask yourself, server by server, "Did this earn its rent this week?" The ones that didn't — disable them. You'll feel the difference on the next conversation.

## Frequently Asked Questions

### How do I add an MCP server to Claude Code?

Use `claude mcp add [options] <name> -- <command> [args...]` from your terminal. Pick the right scope (`--scope user` for global, `--scope project` for team-shared via `.mcp.json`, default for local-only). All flags must come before the server name; the double dash separates Claude's config from the actual server start command. See the section on [the three commands you'll actually use](#the-three-commands-youll-actually-use) above.

### What's the difference between HTTP and STDIO MCP servers?

STDIO servers run as local processes on your machine — fast, low-latency, but they execute someone's code locally with whatever permissions Claude has. HTTP servers are remote, hosted by a provider, accessed over the network — hands-off but you depend on uptime and pay latency. Use STDIO for anything touching your local environment, HTTP for cloud-authenticated services.

### What is the 10% MCP tool-search threshold?

When your installed MCP tool schemas would consume more than 10% of Claude Code's context window, the runtime stops loading every schema upfront and switches to a search index. The model fetches schemas on-demand instead of carrying all of them. This typically reduces session-start token cost by 90%+ on heavy setups. It's enabled by default in 2026.

### Should I use MCP or a CLI tool?

CLI when one exists and is well-supported (`gh`, `aws`, `docker`, `psql`). CLIs sit outside the context window — Claude invokes them through Bash and reads the output. MCP when no good CLI exists or you need structured tool access (Playwright, Notion, Linear). MCP servers cost context tokens upfront; CLIs don't.

### How do I share MCP servers with my team?

Use Project scope: `claude mcp add --scope project <name> ...`. The configuration lands in `.mcp.json` at your repo root, which you commit to git. Teammates clone the repo, open Claude Code, and the servers wire up automatically. Never put credentials in `.mcp.json` — reference environment variables instead.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
