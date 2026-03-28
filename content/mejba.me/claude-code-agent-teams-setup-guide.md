**BRAND:** mejba.me
**TITLE:** Claude Code Agent Teams: Setup, Tips, and Use Cases
**SLUG:** claude-code-agent-teams-setup-guide
**PRIMARY KEYWORD:** Claude Code agent teams
**SECONDARY KEYWORDS:** multi-agent orchestration, Claude Code sub-agents, agent team best practices
**META DESCRIPTION:** 30+ battle-tested tips for Claude Code agent teams. Setup, model assignment, task planning, cost control, and 6 real use cases from production workflows.
**TAGS:** AI Development, Claude Code, Agent Teams, Multi-Agent Systems, Practitioner Guide
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know exactly how to set up Claude Code agent teams, avoid the 10 most expensive mistakes, and pick the right orchestration pattern for their project type.

---

I killed a $47 token budget in nineteen minutes. Four agents, all running Opus 4.6, all working on the same Next.js codebase, all editing `layout.tsx` at the same time.

The orchestrator didn't flag the conflict. The front-end agent overwrote the back-end agent's auth wrapper. The QA agent tested a version of the file that no longer existed. And the design agent -- the one I'd spun up to "polish the UI" -- introduced a Tailwind class that broke the responsive grid the front-end agent had spent eight minutes building.

Nineteen minutes. Forty-seven dollars. A codebase worse than when I started.

That was February 2026. I'd just enabled Claude Code agent teams for the first time, and I made every mistake the documentation warns you about -- plus a few it doesn't mention. Since then, I've run agent teams on over thirty real projects, from SaaS dashboards to API migrations to a content pipeline that now powers the blog you're reading. The feature went from "expensive disaster" to the most powerful tool in my development workflow.

This is the guide I wish existed when I started. Not the marketing version. Not the three-paragraph quickstart. The full picture -- setup, configuration, 30+ hard-won tips, and the six use cases where agent teams genuinely outperform everything else I've tried.

## What Agent Teams Actually Are (and Why Sub-Agents Aren't Enough)

Before touching any configuration, you need a mental model that's more precise than "multiple agents working together." The distinction between sub-agents and agent teams is the difference between hiring freelancers and building a squad -- and confusing the two will cost you real money.

**Sub-agents** are independent Claude Code instances spawned by your main session. Each gets its own context window. Each executes its task and reports back to the parent. The critical detail: sub-agents can't talk to each other. They report up to the orchestrator, period. If Agent A discovers that the database schema needs a new column, Agent B won't know about it unless you manually relay that information.

**Agent teams** add a communication layer on top of this. Teammates can send messages directly to each other. The front-end agent can ask the back-end agent "what format does the `/users` endpoint return?" and get an answer without routing through the orchestrator. One agent acts as the team lead -- coordinating, delegating, synthesizing -- while teammates work independently but stay connected through message passing.

Here's the analogy that finally made it click for me: sub-agents are email. You send instructions, they send back results. Agent teams are Slack. Everyone's in the same channel, messages flow in real time, and the project lead keeps things on track.

The implications are practical and immediate. Sub-agents work perfectly for tasks that are genuinely independent -- run linting, generate documentation, scaffold tests. No coordination needed, no context shared, no messages exchanged. Fast and cheap.

Agent teams shine when the work is interdependent. When the front-end needs to know the API contract before building fetch calls. When the QA agent needs the actual component structure to write meaningful tests. When a database schema change should ripple through every agent's understanding of the system.

I'll walk through the exact setup process next, but this distinction should inform every decision you make from here on. If your tasks don't need coordination, agent teams are an expensive way to do something sub-agents handle for a fraction of the cost.

## The Complete Setup: From Zero to Running Team

Agent teams are still flagged as experimental in Claude Code as of March 2026. That means you need to opt in, and the feature's behavior can change between releases. Here's the exact sequence I follow on a fresh setup.

### Step 1: Enable the Experimental Flag

Open your Claude Code settings file. On macOS, it lives at `~/.claude/settings.json`. Add the experimental agent teams flag:

```json
{
  "experimental": {
    "agentTeams": true
  }
}
```

You can also set the environment variable `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` if you prefer not to touch the settings file. I use the settings file because it persists across terminal sessions without polluting my shell profile.

### Step 2: Choose Your Display Mode

This decision has a bigger impact on your workflow than you'd expect. Agent teams support two rendering modes:

**Tmux split-pane mode** gives each teammate its own terminal pane. You see all agents working simultaneously, side by side. You can click into any pane and interact with that specific agent directly. This is my preferred setup for complex projects -- the visual feedback is invaluable.

Requirements: tmux installed (`brew install tmux` on macOS), and you must launch Claude Code from inside a tmux session. Start with `tmux new -s dev`, then launch Claude Code inside that session.

**In-process mode** runs everything inside a single terminal window. Teammate output appears inline, and you interact with agents through the lead. Less visual overhead, works everywhere, but harder to track what each agent is doing in real time.

Configure this in your settings:

```json
{
  "teammateMode": "tmux"
}
```

Options are `"tmux"`, `"in-process"`, or `"auto"` (which defaults to tmux if it detects a tmux session, in-process otherwise).

**One gotcha that bit me:** split-pane mode doesn't work in VS Code's integrated terminal, Windows Terminal, or Ghostty. If you're running one of those, stick with in-process or switch to a standalone terminal. I use iTerm2, which also supports split panes natively through its Python API -- install the `it2` CLI and enable the Python API in iTerm2 settings.

### Step 3: Define Team Roles Using Natural Language

This is where agent teams diverge from traditional orchestration frameworks. You don't write configuration files for each agent. You describe your team to the lead agent in plain English.

Here's a real prompt I used for a recent SaaS project:

```
I need a team of 4 agents for this project:

1. Lead (you) - Architecture decisions, task delegation, integration review
2. Frontend specialist - React, TypeScript, Tailwind, component architecture
3. Backend specialist - Node.js, Express, PostgreSQL, API design
4. QA specialist - Testing, validation, integration checks

Rules:
- Frontend and backend must agree on API contracts before building
- QA writes tests only after receiving the actual implementation
- Nobody touches files outside their domain without lead approval
```

The lead agent spawns teammates based on your description. Each teammate gets its own context window (roughly 200K tokens as of Opus 4.6) and its assigned specialty.

### Step 4: Assign Models Strategically

This is the single biggest cost lever you have. Not every agent needs Opus 4.6. My standard model assignment:

| Role | Model | Why |
|------|-------|-----|
| Lead/Orchestrator | Opus 4.6 | Makes architectural decisions, needs maximum reasoning |
| Complex implementation | Sonnet 4.6 | Strong coding, 60-70% cheaper than Opus |
| Straightforward tasks | Sonnet 4.5 | Reliable execution, lowest practical cost |
| Testing/validation | Haiku 4.5 | Pattern-following work, cheapest option |

A four-agent team running all Opus costs roughly 4x what a mixed-model team costs. The quality difference for execution-level tasks -- writing React components, building CRUD endpoints, scaffolding tests -- is negligible between Opus and Sonnet. Save Opus for the thinking that actually requires it.

### Step 5: Plan Before You Execute

This is non-negotiable. Before the team writes a single line of code, use plan mode to map out the work. Plan mode is cheap -- it's one agent thinking through the approach. Execution mode with a full team is expensive -- it's four agents consuming tokens in parallel.

My workflow: engage plan mode with the lead agent, get a complete task breakdown and architecture plan, review it, adjust it, and only then switch to execution with the full team. This creates a checkpoint before you commit real tokens.

Think of it this way: plan mode is the architectural blueprint. Agent team execution is the construction crew. You wouldn't send a crew to a site without blueprints. Same principle, different medium.

## 30+ Tips That Saved Me Thousands in Tokens

I've been collecting these since February. Some come from my own expensive mistakes. Others from patterns I noticed across dozens of projects. I've grouped them by category because a flat list of 30 items is impossible to act on.

### Team Composition

**1. Start with 3 agents, not 5.** The coordination overhead between agents scales non-linearly. Three agents can coordinate efficiently. Five agents spend a meaningful percentage of their tokens just talking to each other. I start every project at three and only add a fourth if the workload clearly demands it.

**2. Every agent needs a clear domain boundary.** "Front-end agent" is clear. "Helper agent" is not. If you can't describe an agent's domain in one sentence, the role isn't defined well enough and the agent will drift into other agents' territory.

**3. The lead agent should be your most expensive model.** The lead makes architectural decisions, resolves conflicts between teammates, and maintains the holistic view of the project. Cheap thinking here causes expensive mistakes everywhere else.

**4. Match model capability to task complexity.** Writing unit tests doesn't require Opus-level reasoning. Designing a database schema with complex relationships does. Assign models based on the cognitive demand of the role, not a blanket "everything gets the best model."

**5. Don't create a team for sequential work.** If Task B can't start until Task A finishes, you don't need two agents -- you need one agent doing two things. Agent teams pay off when tasks can run in parallel.

### Task Planning

**6. Cap tasks at 5-6 per agent.** I've found this is the sweet spot. Fewer than 3 and you're underutilizing the agent. More than 7 and the agent loses track of its own progress, starts repeating work, or forgets earlier decisions.

**7. Write task descriptions like you're briefing a new contractor.** Include what to build, what constraints apply, what files to touch, and what files to avoid. Ambiguous tasks produce ambiguous results -- and those results cost tokens to fix.

**8. Define success criteria for each task.** "Build the auth system" is vague. "Build JWT-based auth with refresh tokens, returning a 401 for expired tokens and supporting role-based access for admin/user/viewer roles" tells the agent exactly when it's done.

**9. Plan the integration points before splitting work.** The lead agent should produce a shared contract -- API schemas, database models, type definitions, event formats -- before any teammate starts building. This eliminates the majority of integration issues.

**10. Front-load the thinking, back-load the building.** Spend 20% of your session on planning and 80% on execution. Most people invert this ratio and pay for it in rework.

### Communication and Context

**11. Context sharing is limited to message passing.** Agents don't share memory or context windows. If Agent A discovers something Agent B needs to know, that information must be explicitly sent as a message. Nothing is automatic.

**12. Seed teammates with critical context upfront.** When you define team roles, include the key architectural decisions, tech stack, and constraints in the initial prompt. Every token spent on context seeding saves ten tokens in confused agent behavior later.

**13. Set explicit communication protocols.** Don't let agents message each other freely. Define handoff points: "Front-end agent sends API expectations to back-end agent before writing fetch calls." "Back-end agent shares the final endpoint contract with QA before tests are written." Structure prevents noise.

**14. Use the lead agent as a router, not a bottleneck.** The lead should delegate and review, not relay every message between teammates. Direct agent-to-agent messaging exists for a reason -- if every communication routes through the lead, you've recreated the sub-agent model but with more overhead.

**15. Keep messages between agents concise.** An agent sending its entire code output to another agent wastes tokens on both sides. Messages should contain decisions, contracts, and interface definitions -- not full implementations.

### File and Conflict Management

**16. Never let two agents edit the same file simultaneously.** This is the mistake that cost me $47 in nineteen minutes. File conflicts in agent teams are silent -- there's no merge conflict dialog. One agent simply overwrites another's work. Assign file ownership explicitly.

**17. Create a file ownership map.** Before execution starts, the lead agent should produce a clear mapping: "Front-end agent owns everything in `/src/components` and `/src/hooks`. Back-end agent owns `/src/api` and `/src/db`. QA owns `/tests`." Grey areas cause collisions.

**18. Use a shared types or contracts directory.** I create a `/src/shared` or `/src/contracts` directory that both front-end and back-end agents read from but only the lead agent writes to. This prevents contract drift.

**19. If an agent needs to modify another agent's file, route through the lead.** The lead reviews the change, confirms it doesn't conflict, and either makes the change itself or explicitly approves the requesting agent to proceed. This adds a few seconds of latency but prevents minutes of debugging.

### Cost Control

**20. Monitor token usage actively, not passively.** Don't wait until the session ends to check your bill. Claude Code shows token consumption in real time. If an agent is burning through tokens faster than expected, something is wrong -- it's probably stuck in a loop or generating excessive output.

**21. Kill idle agents immediately.** An agent that's finished its tasks but still running is still consuming resources. The orchestrator handles cleanup, but I've seen agents sit idle for minutes before being terminated. Manual shutdown is faster and cheaper.

**22. Use sub-agents for independent tasks, teams for interdependent ones.** I can't stress this enough. Running four coordinating agents to do four independent tasks is like scheduling a meeting that could have been four emails. Use sub-agents for isolated work. Reserve agent teams for work that genuinely requires coordination.

**23. Batch related tasks together.** Instead of spawning a new agent for every small task, group related tasks under one agent. An agent handling "build all three API endpoints for user management" is more efficient than three agents each building one endpoint.

**24. Run a cost estimate before committing.** Quick math: a 3-agent team running for 30 minutes on Opus 4.6 consumes roughly 200K-300K tokens. At current pricing, that's $15-25. On a mixed model team (one Opus, two Sonnet), the same session runs $6-10. Know your numbers before you start.

### Monitoring and Steering

**25. Check in every 10-15 minutes.** Agent teams are not set-and-forget. I review progress at regular intervals, catch drift early, and adjust task assignments if one agent is blocked while another is idle.

**26. You can interact with any teammate directly.** In tmux split-pane mode, click into an agent's pane and give it direct instructions. You're not limited to talking through the lead. When I notice a specific agent heading in the wrong direction, I step in directly rather than relaying the correction through the orchestrator.

**27. Use hooks for lifecycle events.** Claude Code supports hooks that fire when teammates go idle, complete tasks, or encounter errors. I've wired these to Slack notifications so I get pinged when an agent finishes or gets stuck -- no need to watch the terminal constantly.

**28. Watch for infinite loops.** Occasionally, two agents will get into a ping-pong pattern -- Agent A asks Agent B a question, Agent B responds with a question back, and they loop. If you see token consumption spiking with no visible progress, intervene immediately.

### Lifecycle and Cleanup

**29. Agents auto-stop when their task queue empties.** The orchestrator manages shutdown, but the timing isn't always immediate. If you're done with the session, explicitly tell the lead agent to wrap up.

**30. One team per session.** A lead agent can only manage one team at a time. If you need a different team composition for a different project, start a new session.

**31. Teammates can't spawn their own teams.** The hierarchy is flat: one lead, multiple teammates. No sub-teams, no recursive orchestration. If your project needs that level of nesting, you're probably better off with multiple independent sessions.

**32. Clean up agent processes after crashes.** If a session crashes mid-team, orphaned agent processes can linger. Check for stale processes and terminate them manually. This avoids both resource waste and potential conflicts when you start a new session.

**33. Document what worked.** After each team session, I spend two minutes noting which model assignments, team sizes, and communication patterns worked best for that project type. This library of templates now lets me spin up optimized teams in under three minutes.

## Six Use Cases Where Agent Teams Crush Solo Agents

Theory is fine. What actually matters is knowing when to reach for this tool. After thirty-plus projects, these six scenarios consistently justify the coordination overhead.

### 1. Full-Stack Application Development

This is the canonical use case, and it genuinely delivers. Three agents -- architecture lead, front-end specialist, back-end specialist -- working in parallel on a shared codebase. The lead produces the API contract first, both implementation agents build against that contract simultaneously, and integration issues surface through agent-to-agent messaging rather than after-the-fact debugging.

On a recent project -- a multi-tenant dashboard with role-based access and real-time WebSocket updates -- the agent team delivered in 35 minutes what would have taken a solo agent close to 90 minutes. Not because the agents typed faster, but because the front-end agent was building components while the back-end agent was building endpoints. True parallel execution on interdependent work.

The key: the contract-first pattern I described in tip #9 is mandatory here. Without a shared API contract, the agents build against assumptions that diverge immediately.

### 2. Code Review With Specialized Lenses

This use case surprised me with how well it works. Assign three agents to review the same PR, each with a different focus: security vulnerabilities, performance bottlenecks, and test coverage gaps. Each agent examines the code through its specialized lens and produces a focused report. The lead agent synthesizes the three reports into a single review.

The result is more thorough than any single-pass review, and the specialized focus means each agent catches things a generalist would miss. A security-focused agent flagged a SQL injection vector in a parameterized query that looked correct at first glance -- the parameter escaping didn't handle array inputs. A performance-focused agent caught an N+1 query hidden behind an ORM abstraction. Neither finding would have surfaced in a standard review.

The cost for a three-agent code review runs about $3-5 in tokens. For critical PRs, that's a bargain.

### 3. Debugging With Competing Hypotheses

When a bug has multiple possible root causes, the traditional approach is sequential: investigate hypothesis A, if that's not it, try hypothesis B. Agent teams let you investigate all hypotheses simultaneously.

I had a production issue where API response times spiked from 200ms to 3 seconds every few hours. Three possible causes: database connection pool exhaustion, memory leak in a background worker, or an upstream service throttling. I assigned each hypothesis to a different agent and let them investigate in parallel. Twenty minutes later, Agent C found the upstream throttling -- a rate limit on a geocoding API that nobody had documented. The other two agents found no evidence for their hypotheses, which was equally valuable because it eliminated two days of potential wild goose chasing.

### 4. Writing and Editing Workflows

This one maps directly to the content pipeline I use for this blog. Three agents with distinct roles: researcher (gathers sources, data points, current information), writer (produces the draft following brand voice and structure), and editor (reviews for quality, accuracy, consistency, and SEO).

The researcher finishes first and passes its findings to the writer. The writer produces the draft and hands it to the editor. The editor reviews and sends revision notes back. This sequential-with-communication flow produces noticeably better output than a single agent trying to research, write, and self-edit simultaneously -- because each agent focuses on one cognitive task without context switching.

### 5. Parallel Research and Exploration

When you need to evaluate multiple tools, approaches, or architectures before making a decision, agent teams let you explore all paths simultaneously. I used this when evaluating three different state management approaches for a React project -- one agent tested Zustand, one tested Jotai, one tested the native React Context API. Each agent built a small proof-of-concept, documented the trade-offs, and reported back to the lead.

The critical rule for research tasks: keep the agents read-only during the exploration phase. Let them read code, run benchmarks, and write throwaway prototypes -- but don't let them modify the main codebase until the lead has reviewed the findings and picked a direction. Otherwise you get three different approaches half-implemented in your production code.

### 6. Cross-Platform Feature Parity

If you're shipping the same feature on multiple platforms -- iOS and Android, web and mobile, or even different microservices that need synchronized behavior -- agent teams maintain parity by having each platform agent share their implementation details with the others. When the Android agent implements a date formatting utility, it messages the iOS agent with the exact format string so both platforms produce identical output.

I've only used this twice, but both times it caught divergences that would have become user-facing bugs. The coordination overhead is high for this pattern, so I only recommend it for features where platform inconsistency would be a serious problem -- payment flows, data serialization, anything where "slightly different behavior on iOS vs Android" means a support ticket.

## The Workflow Phases: How a Session Actually Unfolds

For those who want the end-to-end picture, here's how a typical agent team session flows from start to finish. I'll use a real example -- building an internal tool for tracking content pipeline status.

**Phase 1 -- Initialization.** I open a tmux session, launch Claude Code, and verify the experimental agents flag is active. I describe the project to the lead agent in detail: the tech stack (Next.js 15, Prisma, PostgreSQL), the requirements (dashboard showing content status, author assignments, publication dates), and the constraints (must integrate with the existing auth system, must not modify the shared database schema).

**Phase 2 -- Team Creation.** I tell the lead agent what team I need. In this case: a front-end agent for the dashboard UI, a back-end agent for the API layer and database queries, and a QA agent. The lead spawns three teammates, each appearing in its own tmux pane. I can see all four agents simultaneously.

**Phase 3 -- Task Planning.** Before anyone writes code, the lead agent produces a task plan. It outlines every deliverable, assigns tasks to specific agents, defines the API contract between front-end and back-end, and sets the integration test criteria. I review this plan, adjust two task assignments that didn't make sense, and approve.

**Phase 4 -- Parallel Execution.** The agents get to work. Front-end starts building the dashboard components. Back-end creates the Prisma schema and API routes. I can watch both agents' progress in real time through the tmux panes. When the front-end agent needs clarification on the response shape for the `/content` endpoint, it messages the back-end agent directly.

**Phase 5 -- Monitoring and Steering.** About twelve minutes in, I notice the front-end agent building a complex filter component that wasn't in the spec. I click into its pane and redirect: "Skip the advanced filters for now. Focus on the status board and author view." The agent adjusts immediately.

**Phase 6 -- Integration and QA.** Once both implementation agents signal completion, the QA agent activates. It reads the front-end components and back-end endpoints, writes integration tests, and runs them. Two failures: a date formatting mismatch and a missing error boundary. The QA agent reports these to the relevant agents, who fix them in under two minutes.

**Phase 7 -- Summary and Cleanup.** The lead agent compiles a summary: what was built, which tests pass, what's pending. I review the output, confirm everything works, and close the session. Total time: 28 minutes. Total token cost with the mixed-model setup: roughly $8.

The whole sequence felt less like "using a tool" and more like managing a small dev team. Which, in a meaningful sense, is exactly what it is.

## The Honest Trade-Offs Nobody Talks About

I'd be doing you a disservice if I painted agent teams as universally superior. They're not. Here's what I've learned about their real limitations after months of daily use.

**Cost scales linearly, but value doesn't.** Three agents cost three times as much as one. But they don't always deliver three times the value. For projects under moderate complexity -- most CRUD apps, most single-page tools, most scripts -- a solo agent is faster, cheaper, and produces output that's easier to debug because one brain wrote it.

**Message passing isn't free context sharing.** Agents can send messages to each other, but those messages consume tokens on both the sending and receiving side. Heavy communication between agents can cost more than the actual coding. I've seen sessions where 30% of total token usage was inter-agent messaging.

**The orchestrator is a single point of failure.** If the lead agent gets confused about the project state -- and it happens -- the entire team drifts. The lead's context window is finite, and tracking the status of five teammates' work across dozens of tasks can overwhelm even Opus 4.6. This is why I cap teams at 3-4 agents and tasks at 5-6 per agent.

**Debugging team output is harder than debugging solo output.** When something breaks in a solo agent's output, you know who wrote it. When something breaks in a team's output, you need to trace which agent produced the offending code, what context it had when it made that decision, and whether another agent's message influenced the choice. The blast radius of a mistake is larger.

**The feature is still experimental.** Session resumption is unreliable. Teammate shutdown doesn't always happen cleanly. Tmux pane indexing can break if your config uses non-default base indices. These are rough edges, and they're worth tolerating for complex projects -- but they mean agent teams aren't production-grade tooling yet. They're a power feature for people willing to manage the friction.

Here's my honest position after three months: agent teams are the right choice for maybe 15-20% of the projects I work on. But for that 15-20%, they're dramatically better than the alternative. The skill is knowing which 15-20%.

If you'd rather have someone build and configure this kind of multi-agent workflow for your project, I take on custom AI automation engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## How I'd Start If I Were Setting This Up Today

If I could go back to February and give myself a five-step playbook, this is what I'd hand over.

**First session: solo agent, plan mode only.** Don't touch agent teams. Build your project plan with a single Claude Code instance. Get the architecture right, define the API contracts, map the file structure. This costs almost nothing and produces the blueprint your team will execute.

**Second session: two agents, one small task.** Spawn a lead and one teammate. Give them a contained, low-risk piece of the project -- a single feature with clear boundaries. Watch how communication flows. Learn the tmux interface. Get comfortable with the rhythm.

**Third session: three agents, real work.** Now you know enough to be effective. Lead on Opus, two teammates on Sonnet, clear domain boundaries, shared contracts, 5-6 tasks per agent. This is your production setup for 90% of team-worthy projects.

**Every session after that: refine your templates.** Document which model assignments, team sizes, and communication patterns work for each project type. After ten sessions, you'll have a library of team templates that makes setup nearly instant.

**The one thing I'd change:** I'd start by reading the official Anthropic documentation on agent teams at [code.claude.com/docs/en/agent-teams](https://code.claude.com/docs/en/agent-teams) instead of learning through trial and error. The docs are genuinely good -- they just didn't exist when I started experimenting.

The biggest shift in my thinking over these three months isn't about the technology. It's about the role. When I use agent teams, I stop being a developer who uses AI and start being a technical lead who manages AI developers. The skills that matter change: clear requirements writing, task decomposition, progress monitoring, integration planning. The same skills that make a good engineering manager make a good agent team operator.

Your terminal isn't a text editor anymore. It's a war room. And the agents are waiting for orders.

## Frequently Asked Questions

### How do I enable Claude Code agent teams?

Add `"agentTeams": true` to the `"experimental"` section of `~/.claude/settings.json`, or set the environment variable `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`. You need a Claude Pro or Max subscription with access to Opus 4.6 for the lead agent role.

### How many agents should I use in a Claude Code team?

Start with 3 teammates for most projects -- one lead, two specialists. Three agents balance parallel throughput against coordination overhead. Teams of 5 or more spend a disproportionate amount of tokens on inter-agent messaging. For a deeper breakdown, see the Team Composition tips above.

### Do Claude Code agent teams cost more than sub-agents?

Yes. A 3-agent team uses roughly 3-4x the tokens of a single session doing the same work. You can reduce costs by assigning cheaper models (Sonnet 4.5 or Haiku) to execution-focused teammates while keeping Opus on the lead. Agent teams are only cost-effective when the project's complexity genuinely demands real-time coordination.

### Can Claude Code teammates edit the same file?

They can, but they absolutely should not. Agent teams have no merge conflict detection -- one agent silently overwrites another's changes. Assign explicit file ownership to each agent before execution starts, and route any cross-domain file changes through the lead agent.

### What's the difference between agent teams and sub-agents in Claude Code?

Sub-agents are isolated workers that report results back to the parent session -- no inter-agent communication. Agent teams add direct message passing between teammates, enabling real-time coordination on interdependent tasks. Use sub-agents for independent parallel work, agent teams for collaborative work that requires shared context.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
