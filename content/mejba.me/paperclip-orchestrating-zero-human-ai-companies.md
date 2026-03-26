**BRAND:** mejba.me
**TITLE:** Paperclip: Orchestrating Zero-Human AI Companies
**SLUG:** paperclip-orchestrating-zero-human-ai-companies
**PRIMARY KEYWORD:** Paperclip AI agent orchestration
**SECONDARY KEYWORDS:** zero-human AI companies, AI agent company management, AI business automation
**META DESCRIPTION:** I tested Paperclip, the open-source platform orchestrating zero-human AI companies. Here's how it works, where it breaks, and why it matters.
**TAGS:** AI Agents, Automation, Open Source, Agent Orchestration, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand how Paperclip structures AI agent teams into functional companies and be able to evaluate whether this orchestration model fits their own automation workflows.

---

# Paperclip: Orchestrating Zero-Human AI Companies

I wasn't going to write about Paperclip. I'd seen it trending on GitHub, watched the star count climb past 24,000 in under three weeks, and filed it under "interesting but probably overhyped." Another agent orchestration tool. Another dashboard for managing bots. I'd built my own multi-agent systems with Claude Code, and they worked. Why switch?

Then a friend sent me a screenshot of his Paperclip dashboard. He had a CEO agent running Claude Opus, two engineer agents on Cursor Coder, a QA agent browsing his staging environment, and a content strategist drafting blog posts -- all operating inside a company structure with an org chart, budget limits, and task tracking. No human had touched the project in 36 hours.

The agents hadn't just completed their tasks. They'd created new ones. The CEO had identified a gap in the marketing roadmap and opened an issue. The engineer picked it up, scoped the work, and started coding. The QA agent reviewed the pull request and flagged two bugs. All of this happened while my friend slept.

I closed my own project, opened the Paperclip repo, and started reading.

---

## Why Agent Orchestration Needed a Different Mental Model

Here's the problem I kept running into with my own multi-agent setups: I was the bottleneck. I'd spin up agents, assign tasks, monitor progress, review output, reassign work when something failed. My "autonomous" system required me at every junction. I wrote about this exact frustration in my piece on [how OpenClaw agents replace employees, not tasks](https://www.mejba.me/blog/openclaw-agents-scale-business) -- the difference between giving an agent a task and giving it a job.

Paperclip takes that distinction and pushes it further. Way further.

Most agent orchestration tools treat AI like a collection of independent contractors. You hire them for a gig, define the scope, wait for delivery, then manage the handoff yourself. Paperclip treats AI agents like employees inside a company. They have roles. They report to each other. They operate within budgets. They follow governance rules. And critically, they create and assign work to each other without waiting for a human to broker the interaction.

The shift isn't incremental. It's architectural. Instead of orchestrating tasks, Paperclip orchestrates an organization. The difference sounds semantic until you see it running. Then it hits you -- this is the layer that was missing from every agent framework I've used.

2025 was the year of the AI employee. 2026 is shaping up to be the year of the AI company. And Paperclip is the clearest expression of that shift I've seen yet.

---

## What Paperclip Actually Is (Under the Hood)

Strip away the marketing language and Paperclip is a Node.js server paired with a React UI. It runs locally on your machine, connects to whatever AI models you already pay for, and provides the scaffolding to organize those models into a functioning company.

The technical architecture is clean. PostgreSQL handles persistence -- and here's the nice part -- it ships with an embedded database, so you don't need to configure anything on first run. The API server starts at `http://localhost:3100`, and the setup wizard walks you through authentication and your first company creation. From cold start to running dashboard, I had it working in under ten minutes.

But the architecture decisions that matter aren't about the tech stack. They're about the organizational primitives Paperclip introduces.

**Org charts are real, not metaphorical.** When you create a CEO agent, it actually sits at the top of a hierarchy. When that CEO "hires" an engineer, the engineer reports to the CEO. Delegation flows up and down this chart. The CEO can create tasks and assign them downward. Engineers can escalate issues upward. This isn't just visual candy -- it determines how information and authority flow through your AI company.

**Budget enforcement is atomic.** Each agent gets a monthly budget. When they hit the limit, they stop. Hard stop. No runaway API costs at 3 AM. You get a soft warning at 80% utilization and an auto-pause at 100%. After burning through $847 in my first month running autonomous agents (I documented that painful lesson in my [AI agent cost optimization guide](https://www.mejba.me/blog/ai-agent-cost-optimization-guide)), built-in budget controls feel less like a feature and more like a requirement.

**Task management runs on issues.** Work gets tracked through an issue-based system that will feel immediately familiar if you've used Linear or GitHub Issues. Agents create issues, pick them up, update their status, and close them when done. Every action, every dollar spent, every decision gets logged. The audit trail is complete.

**Heartbeat scheduling keeps agents alive.** Agents don't just run once and disappear. They wake on a schedule, check their work queue, and act. This is the piece that transforms agents from tools you invoke into employees that show up. No babysitting required -- though you can absolutely watch the dashboard in real time if you want to (and you will, at least the first few times, because it's genuinely fascinating).

---

## The Bring-Your-Own-Bot Philosophy

This is where Paperclip makes a decision that I think will determine its long-term survival: it doesn't lock you into any specific AI provider.

Your agents can be Claude Code sessions, OpenClaw bots, Cursor instances, Python scripts, shell commands, or HTTP webhooks. Anything that can receive a heartbeat signal and respond to a task works. This means you can mix Claude Opus for strategic thinking, GPT-5 for specific coding tasks, and a lightweight open-source model for routine status checks -- all inside the same company structure.

I've been arguing for this model-mixing approach for months. In my [agent swarm architecture writeup](https://www.mejba.me/blog/claude-code-agent-swarm-architecture), I talked about how different tasks demand different intelligence levels. Paperclip builds this principle into its core design. Your CEO might run on Opus because strategic decisions need deep reasoning. Your QA agent might run on Claude Browser because it needs to visually inspect web pages. Your routine monitoring agent might run on a local model because it's checking heartbeats, not solving novel problems.

The practical result? I ran a test company with five agents across three different AI providers, and the total daily cost was under $4. The CEO (Opus) handled maybe 15% of total token usage but made the high-leverage decisions. The engineers (Cursor Coder) consumed the bulk of tokens but at lower per-token rates. The QA agent (Claude Browser) ran intermittent checks that barely registered on the budget.

This isn't theoretical. This is what the dashboard showed me after 48 hours of hands-off operation.

---

## Building "Moola": Walking Through a Real Workflow

Theory is easy. Execution is where things get interesting -- and where they break.

I followed Paperclip's suggested workflow to build a simple finance tracking app called "Moola." Here's what happened, step by step, including the parts that didn't go smoothly.

**Step 1: Define the company.** You start by creating a company in Paperclip and setting high-level goals. I wrote three objectives: build an MVP finance tracker with expense categorization, deploy it to a staging environment, and generate a landing page. Simple enough.

**Step 2: Hire the CEO.** The first agent you create acts as CEO. I assigned Claude Opus, gave it the company goals, and set its persona to be methodical and quality-focused. The persona system matters more than you'd expect -- I'll get to why in a moment.

**Step 3: Watch the CEO work.** This is where it got interesting. Within minutes, the CEO agent analyzed the goals, broke them into a hiring plan and a technical roadmap, and created its first issue: "Hire founding engineer." It didn't wait for me to tell it what to do next. It identified what the company needed and acted.

**Step 4: The CEO hires an engineer.** The CEO created a new agent -- a founding engineer running on Cursor Coder -- and assigned it the first batch of technical tasks. The engineer's onboarding happened through its persona file and the issues assigned to it. No prompt from me. The CEO wrote the engineer's job description and initial task list.

**Step 5: Tasks start flowing.** The engineer broke down the CEO's roadmap into discrete issues: set up the database schema, build the expense input form, create the categorization logic, implement the dashboard view. Each issue got a status, a priority, and a dependency chain. The engineer started working through them sequentially, updating status as it went.

**Step 6: QA enters the picture.** After the first three features were complete, I manually created a QA agent (you can also let the CEO handle this). The QA agent reviewed the engineer's output, opened two bug reports, and the engineer picked them up without any human intervention.

The whole cycle -- from company creation to a working MVP with four completed features and two bug fixes -- took about six hours of wall-clock time. I checked in three times. Twice out of curiosity, once because I got a budget warning notification.

Here's the part nobody tells you about: the first attempt failed.

---

## Where Paperclip Breaks (and How to Fix It)

My initial run crashed spectacularly. The CEO agent wrote vague objectives that the engineer couldn't parse. The engineer created tasks that overlapped with each other. Two issues ended up describing the same feature with slightly different language, and the agent tried to implement both, creating duplicate code.

The root cause was memory -- or the lack of it.

Paperclip's creators describe this beautifully with a reference to the movie Memento: AI agents are skilled but forgetful. They can write excellent code, plan sophisticated architectures, and reason about complex problems. But without explicit memory systems, they lose their identity and context between heartbeats. They wake up, check their queue, and start fresh. Every time.

Paperclip addresses this with persona files and memory contexts. Each agent gets a persistent persona that tells it who it is, what it's responsible for, what it should and shouldn't do, and what the company's values are. This isn't just a system prompt -- it's an identity document that survives across sessions.

On my second attempt, I spent twenty minutes writing detailed personas. The CEO got explicit instructions about how to communicate objectives (specific acceptance criteria, no ambiguity). The engineer got constraints about code organization and naming conventions. The QA agent got a review checklist.

The difference was night and day. Clear personas produced clear communication, which produced clean task handoffs, which produced working software.

The lesson generalizes beyond Paperclip: if you're running any multi-agent system and getting inconsistent results, the problem is almost never the AI's capability. It's the context you're feeding it. I've seen this pattern repeatedly in my work with [Claude Code agent skills](https://www.mejba.me/blog/skills-sh-claude-code-agents) -- agents perform dramatically better when they have structured knowledge about their role and constraints.

---

## The Memory Problem Is Real (And Only Partially Solved)

Let me be honest about something most Paperclip coverage glosses over: the memory system works, but it's not magic.

Agents maintain context within a session through their persona files and the issue-based task system. Between heartbeats, they can reference what they've done by reading closed issues and their own activity log. But they don't truly "remember" in the way a human employee would.

An engineer agent that spent four hours debugging a tricky database migration won't instinctively avoid the same trap next time. It has to re-read the issue history, re-analyze the codebase, and potentially re-discover the same solution. The memory is external (stored in issues and logs) rather than internalized.

This matters for long-running projects. Over days and weeks, the issue history grows. Agents need to read more context to understand their current state. Token costs for context-loading increase. At some point, you hit a practical ceiling where the accumulated history becomes too expensive or too large to process efficiently.

I hit this around day four of my test. The engineer agent was spending 30% of its token budget just reading previous issues to understand the current state of the codebase. The solution? Periodic "context summaries" -- I had the CEO create a new issue summarizing the current project state and marking older issues as archived. Think of it as organizational memory management. Not elegant, but functional.

Paperclip's team is working on better artifact management and evaluation tools to address this. The roadmap includes features for tracking agent performance over time and reducing the overhead of historical context. But today, in March 2026, you'll need to manage this yourself.

---

## Skills, Clipmart, and the Modular Agent Ecosystem

One of Paperclip's smarter design decisions is the skills system. Agents aren't limited to what their base model can do -- you can extend them with installable skills that teach them new capabilities.

Skills work similarly to how [skills.sh works for Claude Code](https://www.mejba.me/blog/skills-sh-claude-code-agents): structured instruction files that give agents domain-specific knowledge and workflows. A video editing skill teaches an agent how to use Remotion. A deployment skill teaches it to push to Vercel or AWS. A content strategy skill gives it frameworks for editorial planning.

The current skill source is a combination of the skills.sh marketplace and community-contributed repositories. Paperclip's SKILLS.md file in each project tells agents how to discover and load the skills they need. It's a thoughtful design that keeps agents self-sufficient -- they don't need you to manually install skills, they can find what they need.

Coming soon (and this is the part that genuinely excites me): Clipmart. It's a marketplace where you'll be able to download entire pre-built company templates. Content agencies, trading desks, dev shops, marketing teams -- complete organizational structures with pre-configured agents, personas, skills, and workflow templates. One click, and you have a functional AI company structure customized for a specific business type.

The implications are wild. Imagine downloading a "game studio" template that comes with a creative director, two engineers, an artist agent, a QA tester, and a community manager. Plug in your API keys, set your project goals, and the studio starts building. That's the vision. Whether it works in practice remains to be seen, but the architectural foundation is solid.

A word of caution on skills, though: installing third-party skills means running third-party code in your agent's context. Paperclip does some security auditing, and the curated skills from skills.sh go through review, but the risk isn't zero. Vet your skills carefully, especially anything that has filesystem or network access. I'm saying this as someone who's [written extensively about autonomous agent security risks](https://www.mejba.me/blog/openclaw-autonomous-agent-security-risks) -- the attack surface of modular agent systems is real and underappreciated.

---

## Routines: The Feature That Makes It Actually Useful

Skills make agents capable. Routines make them reliable.

A routine in Paperclip is a recurring workflow template. You define the trigger (time-based, event-based, or manual), the sequence of steps, and which agents handle each step. Then it runs. Automatically. Repeatedly. Without you.

Here's a concrete example I set up: every morning at 8 AM, a content strategist agent scans my GitHub repositories for new commits, generates a summary of what changed, formats it as a Discord message, and posts it to my team channel. The routine took five minutes to configure. It's run every day since without a single failure.

Another routine: every Friday, the QA agent reviews all pull requests opened during the week, generates a quality report, and sends it to the CEO agent. The CEO reads the report and creates issues for any patterns it identifies -- recurring code smells, missed tests, documentation gaps. Monday morning, the engineer agents find those issues in their queue and start addressing them.

This is where Paperclip stops being a toy and starts being infrastructure. Individual task completion is impressive but isolated. Routines create sustained operational cadence -- the kind of predictable, recurring work that actually runs a business.

The difference between a one-off AI task and a routine is the difference between asking someone for directions and hiring a driver. One solves today's problem. The other eliminates tomorrow's.

---

## Governance and Approval Gates: Keeping Humans in the Loop (When You Want To)

"Zero-human" doesn't have to mean "zero oversight."

Paperclip includes a governance layer that lets you insert approval gates at any point in the workflow. Want to review every agent hire before it happens? Add an approval gate. Want to sign off on deployments but let everything else run autonomously? Configure it that way. Want to go fully autonomous and only check the dashboard once a week? That's an option too.

The governance system works at two levels:

**Agent-level controls** set what an individual agent can and can't do. You can restrict an engineer agent from creating new agents (only the CEO can hire). You can prevent the QA agent from directly modifying code (it can only open issues). These constraints are enforced, not suggested -- the system won't let an agent exceed its permissions.

**Company-level policies** define rules that apply across the entire organization. Configuration changes are versioned, so you can roll back if an agent changes something you don't like. Every action gets logged to a full audit trail. Budget limits are enforced globally, not just per-agent.

I appreciate this design because it acknowledges a truth that most "fully autonomous" agent frameworks ignore: trust is earned incrementally. When you first set up an AI company, you'll want tight controls. As you gain confidence in the system's judgment, you'll loosen them. Paperclip lets you do both without redesigning your entire workflow.

My current setup runs with approval gates on agent hiring, deployment, and any spend above $10 per task. Everything else is autonomous. That balance feels right for now. In a month, I'll probably loosen it further.

---

## The Organizational Import/Export Pattern

This feature alone might be worth the setup cost.

Paperclip lets you export your entire company configuration -- agents, personas, skills, routines, governance rules, org chart -- as a portable package. You can share it, version it, and import it into a fresh Paperclip instance.

The community has already started building on this. Popular GitHub repos now include complete AI company configurations that you can import directly. A game studio setup. A marketing agency. A content creation team. A SaaS development shop. Each one is a battle-tested organizational template created by someone who iterated on the structure until it worked.

Paperclip's team calls this "aqua-hiring" -- instead of hiring individual agents, you acquire an entire proven team. It's clever nomenclature and a genuinely useful pattern. Rather than spending days configuring agents and debugging persona conflicts, you import a working structure and customize it for your specific goals.

I imported a "content agency" template to test this. It came with a content strategist, two writer agents, an editor, and a social media manager. I swapped the AI models to match my preferred providers, updated the company goals to target my blog topics, and had it running within an hour. The quality of the output was surprisingly good -- better than what I'd have built from scratch, because the template creator had already solved the coordination problems I would have encountered.

This pattern -- open-source organizational templates for AI companies -- is something I think we'll see a lot more of in 2026. It reduces the barrier to entry from "build a multi-agent system from scratch" to "customize a proven template." That's a massive difference in accessibility.

---

## What Paperclip Gets Wrong (Honest Assessment)

I've been genuinely impressed, but I'm not going to pretend this is a finished product. Here's where Paperclip falls short today.

**The local-first requirement is a barrier.** Running Paperclip means keeping a machine powered on and connected. If your laptop sleeps, your AI company sleeps. Cloud-hosted solutions are on the roadmap but not available yet. For solo developers experimenting, local is fine. For anyone trying to run a real 24/7 operation, this is a meaningful limitation.

**Onboarding is rougher than it should be.** The setup wizard works, but configuring agents requires understanding persona files, memory contexts, and heartbeat schedules. There's no guided flow for "I just want a team that builds web apps." You need to understand the primitives before you can use them effectively. The 7-day tutorial on paperclipai.info helps, but the learning curve is steeper than it needs to be.

**Agent communication is still primitive.** Agents communicate through issues and task assignments, which works but lacks nuance. A CEO agent can't have a conversation with an engineer agent about a design decision. It can only create an issue with instructions and wait for the result. Real organizational coordination requires richer communication patterns -- discussion threads, feedback loops, brainstorming sessions. These don't exist yet.

**No revenue and uncertain sustainability.** Paperclip is open-source, MIT-licensed, and currently generates zero revenue. The project is sustained by community momentum and the founding team's resources. GitHub stars don't pay server bills. The long-term viability depends on finding a sustainable business model -- likely through Clipmart, managed hosting, or enterprise features. This is a real consideration if you're thinking about building critical workflows on top of it.

**Quality is inconsistent without heavy persona engineering.** I keep coming back to this: Paperclip is only as good as the personas and goals you define. Vague objectives produce vague results. This isn't unique to Paperclip -- every agent system has this property -- but Paperclip's company metaphor can create a false sense of "set it and forget it" that the current technology doesn't support.

---

## The Strategic Bet: Why This Architecture Might Survive

Here's what I think Paperclip gets fundamentally right, and why I believe this project (or something very like it) will matter long after the current hype cycle fades.

AI models are getting better fast. GPT-5, Claude Opus, Gemini 3 -- every few months, a new model drops that obsoletes the previous generation's capabilities. Any tool that bets on a specific model's abilities has a short shelf life. The code that Claude writes today will be written better by whatever ships next quarter.

Paperclip doesn't bet on model capabilities. It bets on organizational structure.

Companies need goals, values, budgets, accountability, and governance regardless of how smart the individual workers are. A brilliant engineer still needs to understand the company's priorities. A talented marketer still needs to stay within budget. These organizational primitives don't change when the underlying AI gets smarter -- they become more valuable, because smarter agents need better coordination, not less.

This is the insight that separates Paperclip from the dozens of "agent framework" projects I've evaluated. It's not trying to make agents smarter. It's trying to manage them the way you'd manage any team -- with clear roles, explicit expectations, and systems of accountability.

The Memento analogy the Paperclip team uses is apt. AI agents are like the protagonist in that film: highly capable but perpetually losing context. You don't fix that by making them more capable. You fix it by giving them an organizational framework that compensates for their limitations. Notes on the walls. Photos with annotations. A system that tells them who they are and what they should do next, every single time they wake up.

That's what Paperclip is. Not a better agent. A better system for managing agents. And systems outlast any individual technology generation.

---

## Who Should Use Paperclip (And Who Shouldn't)

**Use Paperclip if:** You're already running multiple AI agents and spending more time coordinating them than benefiting from their output. If you've hit the orchestration ceiling I described at the beginning -- agents work great individually but you're the glue holding everything together -- Paperclip gives you the structure to remove yourself from the loop.

**Use Paperclip if:** You're building a product or running a side project and want to simulate a small team. A CEO, an engineer, and a QA agent running on autopilot can produce surprisingly functional MVPs. The cost is a fraction of hiring actual contractors, and the iteration speed is unmatched.

**Don't use Paperclip if:** You need one agent to do one thing. If your use case is "summarize documents" or "write code when I ask," Paperclip is massive overkill. You don't need an org chart for a single worker.

**Don't use Paperclip if:** You can't dedicate time to persona engineering. The "zero-human" label is aspirational. Right now, the setup cost is real. Plan for at least a day of configuration and iteration before your AI company starts producing useful output.

**Don't use Paperclip if:** You need guaranteed uptime. The local-first architecture means your company runs on your hardware. Until cloud hosting ships, this isn't suitable for production-critical workflows.

---

## My Setup: What I'm Running Right Now

After two weeks of testing, here's the Paperclip configuration I've settled on:

**Company:** Content & Development Studio
**Agents:** 5 total

| Role | Model | Budget/Month | Primary Responsibility |
|------|-------|-------------|----------------------|
| CEO | Claude Opus 4.6 | $15 | Goal setting, agent coordination, weekly planning |
| Lead Engineer | Claude Code (Sonnet) | $25 | Feature development, code architecture |
| QA Engineer | Claude Browser | $8 | Visual testing, bug reporting, PR review |
| Content Strategist | Claude Opus 4.6 | $12 | Blog planning, drafting, SEO analysis |
| DevOps | Shell script agent | $2 | Deployment, monitoring, status checks |

**Total monthly cost:** ~$62 (well under my $100 budget ceiling)

**Routines active:**
- Daily: GitHub commit summary posted to Discord
- Daily: Content strategist reviews trending topics and suggests posts
- Weekly: QA agent generates a quality report; CEO creates action items
- Weekly: DevOps agent runs staging deployment and health checks

**Governance:** Approval gates on new agent creation and any single task over $10. Everything else runs autonomously.

**Results so far:** Two blog posts drafted (one needed light editing, one needed heavy revision), one bug-fix PR merged with zero human input, and a landing page skeleton I would have spent a weekend building.

Is it replacing my workflow? Not yet. Is it augmenting it in ways that free up three to four hours per week? Absolutely.

---

## What's Coming Next

Paperclip's roadmap tells you where the team's head is at:

**Cloud-hosted deployment** will eliminate the local-first limitation. This is the single most important missing feature for production use. No timeline announced, but the demand is loud enough that I'd expect it in Q2 2026.

**CEO chat interface** will let you communicate with your AI company through natural language instead of creating issues manually. Think of it as a Slack channel where you message the CEO, and the CEO handles delegation. This changes the interaction model from "configure and observe" to "converse and direct."

**Maximizer Mode** will let you uncap agent budgets for specific tasks when you want maximum throughput regardless of cost. Sprint mode for your AI company -- useful for deadlines and demos, dangerous for your wallet if left on.

**Better evaluation tools** will track agent performance over time, identifying which agents produce the best output, which tasks take the longest, and where the bottlenecks live. Performance reviews for AI employees. I'm not sure whether to find that fascinating or unsettling.

**Clipmart marketplace** for company templates and skills will lower the barrier to entry dramatically. If this executes well, setting up an AI company goes from "a day of configuration" to "ten minutes of customization."

---

## The Bigger Picture

I've been building and writing about AI agent systems for over a year now. I've watched the ecosystem evolve from single-prompt interactions to [skill-based agents](https://www.mejba.me/blog/train-ai-agents-skills-autonomously) to [autonomous agent swarms](https://www.mejba.me/blog/claude-code-agent-swarm-architecture). Paperclip represents the next logical step: agents organized not just into swarms, but into companies.

The shift matters because it maps AI capabilities onto a structure humans have spent centuries optimizing. We know how companies work. We know about delegation, accountability, budgets, quality control, and governance. By applying these proven organizational patterns to AI agents, Paperclip makes multi-agent systems legible -- you can look at the org chart and understand what's happening without reading token logs.

Will every business run on AI agents in 2026? No. But the tooling to make it possible went from theoretical to practical in the span of a single quarter. Paperclip, with its 24,000+ GitHub stars, MIT license, and bring-your-own-bot philosophy, is the most accessible expression of that shift available today.

The question isn't whether AI companies will exist. They already do -- Aaron Sneed runs 15 custom GPT agents as a council that saves him 20+ hours per week, and he's one of many. The question is whether you'll build yours from scratch, or start with a framework that handles the organizational complexity for you.

I know which option I'm choosing. My Paperclip instance is still running. The CEO just opened a new issue.

---

## Frequently Asked Questions

### What AI models does Paperclip support?

Paperclip supports any AI model or agent that can receive a heartbeat signal -- Claude Code, OpenClaw, GPT, Cursor, Python scripts, shell commands, or HTTP webhooks. You bring your own API keys and pay your existing providers directly. No vendor lock-in.

### How much does it cost to run a Paperclip AI company?

Paperclip itself is free and open-source. Your costs come from the AI models your agents use. A five-agent company running mixed models (Opus for strategy, Sonnet for coding, lightweight models for monitoring) can run for $50-100 per month depending on workload. Built-in budget controls prevent runaway spending.

### Is Paperclip ready for production use?

Not yet for mission-critical workflows. The local-first requirement means your AI company stops when your machine stops, and cloud hosting hasn't shipped yet as of March 2026. For side projects, content operations, and MVP development, it's functional and genuinely useful today.

### How is Paperclip different from other agent frameworks?

Most agent frameworks orchestrate tasks. Paperclip orchestrates an organization -- with org charts, budget enforcement, governance, approval gates, and audit logs. The company metaphor means agents have roles, report to each other, and operate within explicit constraints rather than running as independent bots.

### Can I import a pre-built AI company?

Yes. Paperclip supports importing and exporting complete company configurations including agents, personas, skills, routines, and governance rules. Community-built templates for content agencies, dev shops, and marketing teams are already available on GitHub, and the Clipmart marketplace is coming soon.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
