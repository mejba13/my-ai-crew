**BRAND:** mejba.me
**TITLE:** Claude Co-work vs Claude Code: Which One Do You Need?
**SLUG:** claude-cowork-vs-claude-code
**PRIMARY KEYWORD:** Claude Co-work vs Claude Code
**SECONDARY KEYWORDS:** Claude desktop app comparison, Anthropic AI tools 2026, Claude Code CLI
**META DESCRIPTION:** I tested Claude Co-work and Claude Code side by side for three weeks. Here's exactly when to use each — and why combining both changed my workflow.
**TAGS:** Claude AI, AI Productivity, Claude Code, Anthropic, Comparison
**CONTENT TYPE:** Comparison
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know exactly which tool fits their workflow, understand how to combine both through shared folders and CLAUDE.md files, and have a decision framework they can apply immediately.

---

# Claude Co-work vs Claude Code: Which One Do You Need?

Last Tuesday at 11 PM, I was sitting at my desk with both tools open. Claude Code was running in my terminal, halfway through refactoring an authentication module for a client project. Claude Co-work was open on my desktop, pulling competitor pricing data from three different websites and dropping it into a formatted spreadsheet in my Documents folder.

Two Anthropic tools. Same underlying intelligence. Completely different jobs.

I caught myself switching between them every few minutes — terminal for code, desktop app for research — and it hit me that most people I talk to are still picking one or the other. They hear "Claude Co-work" and think it's a lighter version of Claude Code. Or they hear "Claude Code" and assume it handles everything Co-work does, just in a terminal. Both assumptions are wrong, and they're costing people hours every week.

I've been running both tools daily since Co-work launched on the desktop app. Not casually — I mean structuring my entire work day around them, pushing their limits, finding the edges where one breaks and the other shines. What I've landed on is an 80/20 split that would have sounded ridiculous to me six months ago.

Here's the honest breakdown of what each tool actually does, where they overlap, and the workflow pattern that made me retire three other productivity apps.

---

## The Mental Model Most People Get Wrong

Before comparing features, I need to correct something I see repeated in every forum thread about these tools.

Claude Co-work is not "Claude Code for beginners." Claude Code is not "Co-work for developers." They're fundamentally different tools built for different types of work, and thinking of them as tiers on a ladder misses the point entirely.

Here's the clearest way I can frame it: **Claude Code is an engineer. Claude Co-work is an executive assistant.**

An engineer builds things. They write code, run tests, manage version control, architect systems, debug production issues. You give them a spec and they ship software. That's Claude Code — a CLI tool that lives in your terminal, reads your codebase, executes shell commands, and modifies files with the precision of a senior developer.

An executive assistant manages things. They research topics, organize files, draft communications, schedule recurring tasks, connect with your apps, and keep your day running without you micromanaging every step. That's Claude Co-work — a desktop application that accesses your file system, connects to your software stack, and executes multi-step workflows through a visual interface.

You wouldn't ask your engineer to organize your inbox. You wouldn't ask your executive assistant to refactor your database schema. Same principle here.

The confusion exists because both tools use the same underlying Claude models — Opus 4.6, Sonnet 4.6 — and both can technically handle text-based tasks. But their environments, permissions, interfaces, and intended workflows are designed for completely different people solving completely different problems.

That distinction saved me from weeks of frustration. It'll save you too.

---

## What Claude Co-work Actually Does (And Who It's For)

Co-work runs inside the Claude desktop application on macOS. No terminal. No command line. No coding knowledge required. You open the app, start a conversation, and Co-work gets to work.

The interface feels like project management software crossed with a chat window. You create projects, organize them in folders, and Claude maintains memory across sessions within each project. Ask it to research a competitor on Monday, and on Thursday it remembers what it found without you re-explaining the context.

But here's where it gets interesting — and where most surface-level comparisons miss the real power.

### The Sandbox Model

Co-work operates inside what Anthropic calls a sandbox environment. Think of it as a sealed room. Claude can see and interact with whatever you put inside that room — specific folders you grant access to, connectors you authorize, files you share — but it can't wander your entire system unsupervised.

This is a feature, not a limitation. When I set up Co-work for a client's marketing team, the sandbox meant their non-technical staff could give Claude broad instructions ("organize all the Q1 campaign assets and create a summary document") without any risk of Claude accidentally deleting system files or modifying something outside the project scope.

The trade-off is real, though. The sandbox occasionally blocks legitimate operations. I ran into this when trying to use Co-work for image generation through an external API — the sandbox restricted the outbound call. Claude Code, running with full system access, handled the same task without blinking. More on workarounds for this later.

### Connectors: The App Integration Layer

Co-work connects to third-party applications through what Anthropic calls connectors — pre-built plugins for services like Gmail, Slack, Notion, Google Calendar, and a growing marketplace of others. The setup is genuinely simple: browse the marketplace, click to install, authorize with OAuth, done. I connected my Gmail and Notion workspace in under two minutes.

Claude Code can connect to the same services, but through MCP (Model Context Protocol) — which means writing or finding a configuration file, setting up server definitions, and sometimes debugging JSON formatting issues. For a developer, that's fifteen minutes of mild annoyance. For a marketing manager or project lead, it's a brick wall.

This is the core audience split in action. Same capability, wildly different accessibility.

I wrote a detailed walkthrough of the connector system in my [Claude Co-work plugins guide](/claude-cowork-plugins-guide) — worth reading if you're evaluating the integration depth.

### Scheduled Tasks and Automation

One of Co-work's strongest features is scheduled task automation through a simple UI. You define what you want Claude to do, set the frequency (daily, weekly, every Monday at 9 AM), and Co-work handles it.

My daily briefing runs at 7:30 AM every weekday. Co-work checks my email for anything flagged urgent, pulls unread Slack messages from three channels, scans my Google Calendar for the day's meetings, and generates a dashboard-style summary saved to my morning briefings folder. I wake up, open one file, and know exactly what needs attention.

Setting that up in Co-work took about ten minutes of natural language instruction. Setting up something equivalent in Claude Code would mean configuring cron jobs, writing scripts to interface with each service's API, and building a formatting template. Doable — I've built similar systems in Code — but the effort-to-value ratio for a daily briefing heavily favors Co-work.

For the full breakdown of how I built my daily system, check out my post on [building a Claude Co-work daily workflow](/claude-cowork-daily-workflow-system).

### Dispatch Mode: Your Phone Becomes a Remote

Dispatch is the feature that changed how I think about Co-work entirely. It pairs your iPhone with your desktop through a QR code scan, creating a persistent conversation thread between both devices. You message Co-work from your phone, and it executes on your desktop.

I was at a coffee shop last week when a client asked for an updated project timeline. I pulled out my phone, told Co-work to pull the latest data from our shared Notion board and generate a formatted PDF, and by the time I finished my espresso, the file was sitting in my Dropbox ready to share.

Your Mac needs to be awake with the Claude app open — close the lid and Dispatch goes dark. That's the main limitation. But for the hours when your machine is running, having phone-to-desktop task execution is genuinely powerful.

---

## What Claude Code Actually Does (And Who It's For)

Claude Code is a command-line tool that runs in your terminal. It reads your entire codebase, executes shell commands, writes and modifies files, manages git workflows, runs tests, and builds production software. If you're a developer, this is the tool that feels like hiring a senior engineer who never sleeps.

I covered the complete setup and workflow in my [Claude Code beginner's guide](/claude-code-tutorial-beginners-guide), so I won't repeat the basics here. Instead, I want to focus on what makes Code fundamentally different from Co-work as a working tool.

### Full System Access

Where Co-work operates in a sandbox, Claude Code operates with the permissions of your user account. It can read any file you can read, execute any command you can execute, install packages, start servers, interact with databases, run Docker containers — the full range of what a developer does on their machine.

This is both its superpower and its responsibility. Claude Code can do things Co-work literally cannot: compile code, run test suites, deploy to staging environments, interact with package managers, execute database migrations. The operations that make software development possible require system-level access that a sandbox environment can't provide.

The flip side: mistakes have real consequences. An incorrect `rm -rf` command or a botched database migration can cause genuine damage. Claude Code mitigates this with its permission system and Ask mode (where it confirms every action before executing), but the fundamental trust model is different from Co-work's sealed sandbox.

For any developer reading this — and you probably already know this — always use git before letting any AI tool modify your codebase. I commit before every major Claude Code session. If something goes sideways, `git checkout` gets me back to safety in seconds.

### The Terminal-Native Advantage

Claude Code's terminal-first design isn't just an interface choice. It puts the AI exactly where code lives — inside the same environment where you'd normally write, test, and ship software.

When I'm working on a Laravel project, Claude Code can see my directory structure, read my route files, understand my database schema from migrations, check my `.env` configuration, and run `php artisan` commands to verify its work. It doesn't need me to copy-paste code into a chat window. It's already there, in the project, with full context.

This matters enormously for complex tasks. When I asked Claude Code to add a role-based permission system to a client's application last month, it didn't just write the migration and model files. It read the existing auth system, identified the middleware patterns already in use, matched the coding style of existing controllers, wrote feature tests that followed the project's existing test conventions, and ran the test suite to confirm everything passed. One prompt, twenty-two files modified, zero broken tests.

Co-work can't do any of that. Not because it's less intelligent — it's the same Claude model — but because it doesn't have the environment access that makes codebase-level reasoning possible.

### MCP Configuration: Power at the Cost of Setup

Where Co-work has its marketplace connectors, Claude Code uses MCP (Model Context Protocol) for external integrations. The capability ceiling is higher — you can connect to virtually any service with an API — but the floor requires technical knowledge.

Setting up an MCP server means creating a JSON configuration file, specifying the server command, defining available tools, and sometimes writing custom server code. I've configured MCP connections for GitHub, Linear, Notion, and several custom internal tools. Each one took between ten minutes and an hour, depending on complexity.

The payoff is worth it for developers. My Claude Code setup has deep integration with my entire development stack. But I wouldn't hand this configuration process to someone who doesn't know what JSON is.

### Remote Control vs Dispatch

Claude Code has its own remote capability — Remote Control mode — which lets you send coding instructions from your phone through the Claude app. It's conceptually similar to Co-work's Dispatch, but oriented toward development tasks.

I've used it to kick off deployments while away from my desk, trigger test runs, and even review pull requests from my phone. The encrypted channel means your code never leaves your machine; only the chat messages travel through Anthropic's servers.

I wrote a full breakdown of this feature in my [Claude Code remote control guide](/claude-code-remote-control-phone) — it's become one of my most-used features for managing builds while away from my desk.

### Agent Teams and Parallel Execution

As of March 2026, Claude Code supports Agent Teams — the ability for a lead agent to spawn multiple specialized sub-agents that work in parallel on different parts of your codebase. One agent handles the frontend refactor while another writes API tests while a third updates documentation.

This is pure developer capability. Co-work has nothing equivalent because the use case doesn't exist outside software development. If you're managing a codebase with fifty-plus files and need coordinated changes across multiple modules, Agent Teams turns what used to be a day of work into an hour of supervised AI execution.

---

## The Side-by-Side Breakdown

I've been running both tools daily for weeks. Here's what I've observed — not from documentation, but from actual use.

| Dimension | Claude Co-work | Claude Code |
|-----------|---------------|-------------|
| **Who it's built for** | Non-technical users, knowledge workers, managers, creators | Developers, engineers, technical architects |
| **Interface** | Desktop app with visual project management | Terminal CLI with code editor integration |
| **Coding required** | None | Yes — terminal literacy is the minimum |
| **System access** | Sandboxed — only folders and apps you authorize | Full user-level access to files, terminal, packages |
| **App connections** | Marketplace connectors, one-click OAuth setup | MCP configuration files, manual JSON setup |
| **Project organization** | Visual folders, persistent memory, scheduled tasks | File system folders, CLAUDE.md for memory |
| **Scheduled automation** | Simple UI — set frequency in natural language | Cron jobs or `/loop` command, manual setup |
| **Mobile access** | Dispatch mode — phone-to-desktop task execution | Remote Control — phone-to-terminal coding |
| **Output format** | Interactive dashboards, formatted documents, slideshows | Code files, terminal output, HTML, test results |
| **Skills system** | Browse and manage in-app, visual interface | Markdown files in `.claude/` directory |
| **Safety model** | Sandbox isolation, approval before actions | Permission system, Ask mode, git as safety net |
| **Learning curve** | Minutes to productive | Hours to days, depending on terminal experience |

The pattern is clear. Co-work optimizes for accessibility and safety. Code optimizes for power and precision. Neither is better in absolute terms — they serve different jobs.

---

## Where Each Tool Wins (And Where It Doesn't)

### Co-work wins when...

**The task involves research and synthesis.** Pulling information from multiple sources, summarizing it, and producing a formatted deliverable. I use Co-work for competitor analysis, content research, meeting prep, and market trend reports. The sandbox keeps things safe, and the connector ecosystem means I can pull from Gmail, Notion, Slack, and web sources without leaving the conversation.

**The person doing the work isn't technical.** I set up a Co-work system for a client's content team. Three writers, zero coding experience. Within a day, they were using scheduled tasks to pull trending topics every morning and generating content briefs automatically. If I'd tried to hand them Claude Code, the terminal alone would have been a dealbreaker.

**The output is a document, not code.** Reports, slideshows, briefs, email drafts, spreadsheet analysis, formatted PDFs — Co-work handles these natively. The output is visual and polished. Claude Code can generate documents too, but the output is raw files that need a separate viewer.

**You want set-and-forget automation.** Scheduled tasks in Co-work are genuinely effortless to create. "Check my email every morning and flag anything from clients" — done. No scripting, no cron syntax, no debugging why the scheduled job didn't trigger.

### Code wins when...

**The task involves writing, testing, or modifying software.** This is non-negotiable. If you're building an application, refactoring a codebase, writing tests, managing deployments, or doing anything that touches a compiler, interpreter, or runtime — Claude Code is the only option. Co-work physically cannot execute code or interact with development toolchains.

**You need precision control over output.** Claude Code lets you specify exactly how files are structured, which patterns to follow, which conventions to maintain. The Ask mode means every file modification gets your approval before it's written. For production codebases where a wrong change can break things, this precision matters.

**The project requires deep codebase understanding.** Claude Code reads your entire project structure, understands relationships between files, traces import chains, and reasons about your architecture holistically. When I ask it to add a feature, it doesn't just generate isolated code — it integrates with what already exists. Co-work doesn't have this capability because it doesn't operate within a codebase context.

**You need parallel agent execution.** Agent Teams in Claude Code can split a large task across multiple sub-agents working simultaneously. For complex development projects — rewriting a test suite, migrating an API, refactoring a component library — this parallel execution cuts hours off the timeline.

### Neither wins when...

**You need real-time collaboration with other humans.** Neither tool replaces Google Docs for simultaneous multi-user editing, or Figma for collaborative design, or GitHub's pull request review workflow for code review. They're AI agents, not collaboration platforms.

**The task requires visual or audio content creation.** Co-work's sandbox can block external API calls for image generation. Claude Code can interact with image generation APIs but doesn't have native visual editing capabilities. For design-heavy work, both tools are better used as preparation and planning layers, not creation tools.

---

## The Hybrid Workflow: Using Both Together

Here's where things get genuinely interesting — and where I think most comparison articles stop short.

Co-work and Claude Code aren't competitors. They're teammates. And Anthropic designed a specific mechanism for them to collaborate: shared folders and CLAUDE.md files.

### The Shared Folder Pattern

Both tools can access the same directories on your machine. I set up a shared project folder that both Co-work and Code can read from and write to. The structure looks like this:

```
~/Projects/client-name/
├── CLAUDE.md          # Shared context file
├── research/          # Co-work writes here
│   ├── competitor-analysis.md
│   ├── market-trends.md
│   └── user-feedback-summary.md
├── specs/             # Co-work writes, Code reads
│   ├── feature-requirements.md
│   └── api-design-notes.md
├── src/               # Code writes here
│   ├── app/
│   ├── tests/
│   └── config/
└── docs/              # Both write here
    ├── changelog.md
    └── deployment-notes.md
```

The `CLAUDE.md` file is the bridge. Both tools read it for context — project goals, conventions, current status, important decisions. When Co-work finishes a research task, it updates `CLAUDE.md` with a summary. When Claude Code ships a feature, it logs the change there. Each tool picks up where the other left off.

### A Real Example: Building a SaaS Feature

Here's how a recent project actually flowed:

**Phase 1 — Research (Co-work):** I asked Co-work to analyze five competing products' pricing pages, identify common patterns, and draft a requirements document for our own pricing tier system. Co-work pulled data from each site, created a comparison matrix, and saved a detailed spec to the `specs/` folder.

**Phase 2 — Build (Claude Code):** I opened Claude Code in the same project directory. It read the spec Co-work had written, understood the requirements, and built the pricing tier system — database migration, model, controller, API endpoints, and frontend components. Twenty-eight files created or modified, all following the project's existing patterns.

**Phase 3 — Documentation (Co-work):** After Code finished the build, I had Co-work generate user-facing documentation, update the changelog, and draft the announcement email for the client. Co-work read the code changes (through the shared folder) and produced documentation that accurately reflected what was built.

**Phase 4 — Testing and Polish (Claude Code):** Back to Code for writing integration tests, fixing edge cases identified during manual testing, and preparing the deployment configuration.

The entire cycle — research, build, document, test — used both tools without any manual file copying, context re-explanation, or workflow friction. The shared folder and `CLAUDE.md` file were the only coordination mechanism needed.

### The Zapier MCP Workaround

One friction point with Co-work is the connector marketplace. If your app isn't listed, you're stuck — unlike Claude Code where you can write a custom MCP server for anything with an API.

The workaround I found: use Zapier's MCP integration as a bridge. Zapier connects to over 6,000 apps, and if you set up a Zapier MCP server, Co-work can trigger Zapier automations that reach services the native connector marketplace doesn't support yet.

It's not elegant. It adds a middleman. But it works, and for non-technical users who need Co-work to reach an unsupported app, it's the best option available until Anthropic expands the marketplace.

---

## Skills: Same Concept, Different Execution

Both tools support Skills — reusable markdown files that define instructions, workflows, or capabilities Claude can invoke. The concept is identical. The experience of creating and managing them is not.

In Co-work, you browse Skills through a visual interface. Install them with a click, manage them in a settings panel, see what each one does at a glance. My Co-work has a slideshow generation skill, a meeting notes skill, and a content brief skill — all installed through the marketplace and customized through the UI.

In Claude Code, Skills are markdown files sitting in your `.claude/` directory or referenced in your project configuration. You write them in a text editor. There's no marketplace browse experience — you either write your own or find community-shared skill files on GitHub. The upside is unlimited customization. The downside is the learning curve.

I covered the skill system in depth in my [Claude Skills guide](/claude-skills-guide-decoded) — including how to write custom skills that work across both platforms.

The interesting thing: because Skills are just markdown files, a Skill written for one platform can often work on the other with minor adjustments. The slideshow generation skill I built for Co-work also runs in Claude Code with one path change. This portability is a smart design decision from Anthropic — it means your investment in building Skills pays off regardless of which tool executes them.

---

## The 80/20 Split: How I Actually Divide My Work

After three weeks of running both tools daily, I've settled into a pattern that might surprise developers: **I use Co-work for roughly 80% of my tasks and Claude Code for 20%.**

Before you close this tab in disgust — hear me out. I'm a software engineer. I build production applications. Claude Code is objectively the more powerful tool for what I do professionally. So why does Co-work get most of my time?

Because most of my time isn't spent writing code.

I spend maybe 20% of my working hours in actual development. The other 80% is research, communication, documentation, project management, content creation, client coordination, and administrative tasks. Co-work handles all of that faster and more naturally than Claude Code ever could.

When I sit down for focused development work — building features, fixing bugs, refactoring systems, writing tests — Claude Code is unmatched. I wouldn't touch Co-work for those tasks. But those tasks are a fraction of my actual week.

The 80/20 split isn't about capability ranking. It's about matching each tool to the work that fills your day. If you're a full-time developer who spends eight hours a day writing code, your split might be 30/70 favoring Code. If you're a project manager who occasionally needs to modify a configuration file, it might be 95/5 favoring Co-work.

The key insight: **your split should match your actual work distribution, not your identity.** I'm an engineer, but my days aren't 100% engineering — and my tool usage reflects reality, not job title.

---

## What Would I Recommend? A Decision Framework

Forget "which is better." The right question is: "What does your typical work day look like?"

### Start with Co-work if...

- You don't write code professionally
- Your work involves research, documentation, communication, and project management
- You want automation without scripting
- You value safety over power (the sandbox is a genuine advantage for non-technical users)
- Your app stack is covered by the connector marketplace
- You want to be productive in minutes, not hours

### Start with Claude Code if...

- You write code daily
- You need to build, test, and deploy software
- You want full system access and aren't intimidated by the terminal
- Your integrations require custom MCP configurations
- You need parallel agent execution for large codebases
- You're comfortable with git as your safety net

### Use both if...

- You're a developer who also does significant non-coding work (most of us)
- Your projects have research, planning, building, and documentation phases
- You want the shared folder pattern for seamless handoffs between tools
- You have team members with different technical skill levels working on the same projects

### The pricing reality

Both tools are included in the Claude Pro plan at $20/month. You don't choose one financially — you get both. The Max plan at $100/month increases usage limits for heavy users. If you're evaluating whether to subscribe, the question isn't "which tool justifies the cost" — it's whether the combined value of both tools, plus Claude's chat interface, is worth twenty dollars a month.

For me, that's not even a question. Co-work alone saves me three to four hours per week on research and administrative tasks. Claude Code saves me twice that on development work. At $20/month, the ROI is absurd.

---

## What's Coming Next (And Why It Matters)

Co-work is evolving fast. The Dispatch feature, computer use capabilities, and connector marketplace all shipped in rapid succession during early 2026. Anthropic's trajectory suggests they're building Co-work into a general-purpose AI operating system for knowledge work.

Claude Code is evolving in a different direction — toward deeper development capabilities. Agent Teams, voice mode, the `/loop` command for recurring tasks, and enhanced code review features all point toward a tool that's becoming less of a coding assistant and more of a full engineering department.

The gap between them is narrowing in some areas. Co-work's computer use feature — where it can control your Mac's UI directly, clicking buttons and navigating apps like a human would — is blurring the line between "sandbox assistant" and "full system agent." If Anthropic continues this trajectory, Co-work may eventually handle lightweight scripting and automation tasks that currently require Claude Code.

But I don't think they'll converge fully. The mental model of engineer vs. executive assistant holds up because the audiences genuinely need different things. A marketing manager shouldn't need to understand terminal permissions to automate their weekly reports. A senior engineer shouldn't need to click through a visual UI to configure a build pipeline.

The smart bet is learning both tools now, building the shared folder workflow into your projects, and being ready to leverage whichever direction Anthropic pushes each tool next.

---

## The Real Answer Nobody Wants to Hear

People keep asking me "Claude Co-work or Claude Code?" like it's a binary choice. Like picking a side in a rivalry.

The real answer is boring: use the one that matches the task in front of you. Then use the other one when the task changes. They're twenty dollars total, they share the same brain, and they coordinate through a markdown file in a folder.

I started this comparison expecting to crown a winner. What I found instead was a workflow — two specialized tools that cover each other's blind spots so completely that using only one feels like working with half a toolkit.

That Tuesday night at my desk, terminal in one window and desktop app in the other, I realized I wasn't switching between competing products. I was watching an engineer and an executive assistant divide the work exactly the way a well-run team should.

Pick the tool that matches your next task. Start building. The comparison matters a lot less than the work you ship with either one.

---

## Frequently Asked Questions

### Can I use Claude Co-work and Claude Code at the same time?

Yes — both tools run independently and can access the same project folders simultaneously. Use a shared `CLAUDE.md` file to coordinate context between them. Co-work handles research and documentation while Code handles the build.

### Do I need separate subscriptions for Co-work and Code?

No. Both are included with the Claude Pro plan at $20/month. The Max plan at $100/month offers higher usage limits but isn't required to access either tool. For a full pricing breakdown, check [Anthropic's pricing page](https://claude.com/pricing).

### Can Claude Co-work write and run code?

Co-work can generate code snippets and create files, but it cannot execute code, run test suites, compile applications, or interact with development toolchains. For anything requiring code execution, use Claude Code.

### Is Claude Co-work available on Windows?

As of March 2026, Co-work is available on macOS through the Claude desktop application. Windows support is in development but hasn't shipped yet. Claude Code works on macOS, Linux, and Windows.

### What's the best way to connect unsupported apps to Co-work?

Use the Zapier MCP workaround — set up a Zapier MCP server that bridges Co-work to any of Zapier's 6,000+ app integrations. This bypasses the native connector marketplace limitation for services that don't have built-in support yet.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
