**BRAND:** mejba.me
**TITLE:** How Anthropic's Agent Teams Changed My Coding Workflow
**SLUG:** anthropic-agent-teams-ai-coding
**TAGS:** AI Agents, Claude Code, Multi-Agent Systems, AI Development, Technical Deep Dive

---

Three weeks ago, I was halfway through a massive refactor — migrating an authentication system across fourteen microservices — when Claude just... lost the plot. Not dramatically. Not with errors. Worse. It started producing code that was technically correct but contextually wrong. Variable names from service A were bleeding into service B. Configuration details from the first hour of the session had evaporated. The AI equivalent of a surgeon who's been in the operating room too long.

I'd hit the wall that every serious AI-assisted developer eventually hits: context degradation. And honestly? I almost gave up on using AI agents for anything beyond single-file tasks.

Then Anthropic shipped agent teams. And I need to tell you what happened next, because it fundamentally rewired how I think about AI-assisted development. Not in the "slap an AI label on everything" way — in the "this solves a real engineering problem I couldn't crack" way.

But before I get to the architecture and how I actually set this up, you need to understand why the obvious solution — the one most of us tried first — doesn't work. That part surprised me more than anything.

## The Problem Nobody Warned Me About

Here's what the AI coding hype cycle doesn't tell you. Single-agent AI assistants degrade. Not sometimes — predictably, reliably, every single time on long sessions.

I noticed this pattern about six months into heavy Claude Code usage. The first 20-30 minutes of any coding session? Brilliant. The AI remembered every architectural decision, caught edge cases I missed, and wrote code that felt like a senior engineer had reviewed it. But push past the hour mark on a complex project, and something shifted.

Details started blurring. The model would "forget" that we'd agreed on a specific error handling pattern three prompts ago. Code quality didn't crater — it gently sloped downward in ways that were easy to miss and expensive to fix later.

I tracked this across twelve separate projects over two months. My rough numbers: quality held steady for about 30-40 minutes of complex work, showed measurable degradation around the 60-minute mark, and became unreliable for nuanced decisions after 90 minutes. Your mileage will vary, but the pattern is real.

This isn't a Claude-specific problem, by the way. It's a fundamental constraint of how large language models manage context windows. Every token of conversation history competes for attention with the actual task at hand. The longer the session, the noisier the signal.

So what do you do when you need an AI to help with work that takes hours, not minutes? That's the question Anthropic's been wrestling with. Their first answer was sub-agents. Their better answer came later — and it's the one worth paying attention to.

## Sub-Agents Were the Band-Aid, Not the Cure

When Anthropic introduced sub-agents, I got genuinely excited. The concept was clean: instead of one AI maintaining a massive, degrading context, you spin up lightweight helper agents for specific tasks. They do their work in isolation, return a summary, and die. The main orchestrator agent stays lean.

I used sub-agents heavily for about three months. They worked beautifully for isolated tasks — "go analyze this file and tell me the dependency tree," "write unit tests for this function," "refactor this component to use TypeScript generics." Clean inputs, clean outputs, no cross-contamination.

The cracks appeared when tasks weren't actually isolated.

Picture this scenario: I'm building a REST API with a frontend dashboard. I spin up one sub-agent to handle the API endpoint for user permissions. Another sub-agent works on the frontend component that calls that endpoint. Both agents are working in the same codebase.

Sub-agent A decides the permission check should return a flat boolean. Sub-agent B, with zero knowledge of A's decision, builds the frontend expecting a structured object with role details. Neither agent did anything wrong in isolation. Together, they created a mess that took me longer to untangle than if I'd just written both pieces myself.

This happened three times in one week before I admitted the truth: sub-agents can't communicate with each other. They're brilliant individual workers with zero collaboration skills. Like hiring five contractors who never talk and wondering why the plumbing doesn't connect to the kitchen.

That limitation isn't a bug — it's a design choice. Sub-agents are meant to be cheap, fast, and disposable. Communication between them would add complexity and token cost. For focused tasks, they're still my go-to.

But real software development isn't a collection of focused tasks. It's an interconnected web where decisions in one file ripple across dozens of others. I needed something that could handle that reality.

Here's where agent teams enter the picture — and where things get genuinely interesting from an architecture standpoint.

## Inside Anthropic's Agent Teams: The Architecture That Actually Works

Agent teams aren't just "more sub-agents." The architecture is fundamentally different, and understanding the distinction matters if you're going to use them effectively.

Here's the mental model that clicked for me: sub-agents are like sending individual emails to separate contractors. Agent teams are like putting everyone in the same Slack workspace with shared project boards.

The system has four core components, and each one solves a specific problem I'd been banging my head against.

**The Team Lead** is your main Claude Code session — the one you're actually typing into. It creates the team, assigns tasks, and coordinates the overall effort. Think of it as the tech lead who delegates but doesn't write the code themselves. I'll come back to why that "doesn't write the code" part is critical.

**Team Members** are independent Claude Code instances, each with their own context window. This is the key insight — they get fresh context windows. No degradation from the lead's long conversation history. They start clean and focused on their specific assignment.

**The Shared Task List** is a collaborative to-do system visible to every team member. This might sound mundane, but it's the feature that makes everything else work. When team member A finishes building the API endpoint and updates the task list with the response schema they chose, team member B can see that before they build the frontend component that consumes it.

**The Mailbox** is the communication layer. Team members can message each other directly and message the team lead. "Hey, I noticed you're using camelCase for the API response fields — should I match that in the database schema, or are we transforming at the API layer?" That kind of real-time coordination was impossible with sub-agents.

I want to be specific about what this looks like in practice. When I spin up an agent team, my terminal splits into multiple panes — I use iTerm2, but Tmux works too. Each pane shows a different team member working in real time. I can watch them coordinate, and I can jump into any individual pane to give direct instructions.

That visibility was an unexpected benefit. With sub-agents, the work happened in a black box and I got a summary. With agent teams, I can see the thinking process unfold. When something goes sideways, I catch it in real time instead of discovering the damage in the summary.

But there's a catch nobody talks about in the announcement blog posts. We'll get there — first, I want to show you how I actually set this up and the workflow that's been working.

## Setting Up Agent Teams: What I Wish Someone Had Told Me

Agent teams are still experimental. Anthropic hasn't enabled them by default, which is actually a good sign — it means they're being careful about rolling out a feature that can burn through tokens fast.

Here's how I enable and configure them. Open your Claude Code settings (either through the settings menu or the command line) and look for the agent teams experimental feature flag. Turn it on. You'll also want to configure how many team members you want — you can either specify a number or let Claude decide based on the task complexity.

My current setup uses three team members for most projects. I tried five early on and the coordination overhead wasn't worth it. Two felt too constrained. Three hits a sweet spot where you get real parallelism without drowning in inter-agent communication.

**Step 1: Frame the task at the team lead level.** This is where most people go wrong. Don't give the team lead implementation details — give it the project scope. "We need to add a user notification system that includes email, in-app notifications, and a preference management UI" is good. "Write a function that sends emails using SendGrid" is bad — that's a team member task, not a team lead task.

**Step 2: Let the team lead decompose the work.** Watch what it assigns to each team member. If the decomposition looks wrong, intervene early. I once saw the team lead assign the database migration and the API endpoint to the same team member while giving another member just the CSS styling. Rebalancing before work starts saves enormous pain.

**Step 3: Monitor the shared task list.** As team members complete work and update the task list, check that the coordination signals are accurate. If member A says "API returns a paginated response with `next_cursor` field" and member B is building the frontend pagination, you want to verify B actually received that information.

**Step 4: Use the mailbox for course corrections.** When I spot a divergence, I message the specific team member directly. "Check the task list update from member A about the cursor-based pagination — update your implementation to match." Direct, specific, actionable.

**Pro tip:** Assign file ownership explicitly. Tell each team member which files they own and which files they should read but not modify. "You own everything in `/src/notifications/email/`. You can read `/src/notifications/types.ts` but don't edit it — that's member B's file." This single practice eliminated 80% of the merge conflicts I was getting.

One more thing that tripped me up early: team members don't inherit the team lead's conversation history. If you spent ten minutes discussing architectural preferences with the team lead before spinning up the team, those preferences aren't automatically shared. Include task-specific context in each member's assignment. "Use TypeScript strict mode, prefer functional components, error handling follows our Result<T, E> pattern" — put that in the assignment, not in a pre-team-creation chat.

If you've followed me this far, you already know more about the practical setup than most of the tutorial content I've found. The next part is where I share the real numbers — what agent teams actually cost and whether the results justify it.

## The Real Numbers: Cost, Speed, and Output Quality

I'm going to be honest about something the AI productivity crowd doesn't like to discuss: agent teams are expensive.

Running three team members in parallel means three separate Claude Code instances, each consuming tokens independently. Plus the team lead's coordination tokens, plus the shared task list updates, plus the mailbox messages. My rough tracking over the past month shows agent team sessions cost 3-4x what an equivalent single-agent session costs.

Here's my actual data from a representative project — building a webhook management system with a REST API, database layer, admin UI, and test suite:

**Single agent approach:** About 2.5 hours of active session time, moderate quality degradation in the final hour, three bugs caught in code review that traced back to context loss. Token cost around $12.

**Agent team approach (3 members):** About 55 minutes of wall-clock time (the parallelism is real), minimal quality degradation since each member had a focused context window, one coordination bug where two members had slightly different assumptions about error codes. Token cost around $38.

So the agent team was 2.7x faster in wall-clock time, produced fewer context-related bugs, but cost roughly 3.2x more in tokens.

Was it worth it? For that project, absolutely. The three bugs from the single-agent approach took me 45 minutes each to diagnose and fix — that's over two hours of debugging time saved. When I factor in my hourly rate, the $26 token premium paid for itself several times over.

But here's the honest caveat: for simpler projects — things I can complete in a single focused session without context degradation — agent teams are overkill. I still reach for a single agent (or sub-agents for truly isolated tasks) when the work fits comfortably in one context window.

The decision framework I use now:

- **Single task, single file, under 30 minutes?** Regular Claude Code session.
- **Multiple isolated tasks with no interdependencies?** Sub-agents. They're cheaper and faster for parallel-but-independent work.
- **Complex, interconnected work spanning multiple files and requiring coordination?** Agent teams. The cost premium is justified by time savings and quality improvement.

That middle category — the stuff that seems like it needs agent teams but actually doesn't — is where most people waste money. A good test: if you can describe each sub-task without referencing any other sub-task, you don't need agent teams. You need sub-agents.

The real power of agent teams shows up when tasks are deeply intertwined. And there's a design pattern I've been using that amplifies that power dramatically — but first, I need to tell you about the mistakes that almost made me abandon the whole approach.

## Mistakes I Made So You Don't Have To

Mistake number one was letting the team lead implement code. This sounds counterintuitive — why wouldn't you let the lead contribute? Because the moment the team lead starts writing code, it stops coordinating. I watched it happen in real time: the lead would get absorbed in implementing a database query and completely miss that two team members had sent mailbox messages asking for clarification.

The fix was simple: I now explicitly tell the team lead "Your job is coordination only. Do not write any implementation code. Delegate everything." This single instruction transformed the quality of team output.

Mistake number two was creating too many team members. My first experiment used five members for a task that really needed three. The result was chaos — members were messaging each other constantly, the shared task list became a wall of updates, and I spent more time monitoring coordination than I would have spent just writing the code myself. More agents isn't better. The right number of agents is the minimum needed for meaningful parallelism.

Mistake number three — and this one cost me real money — was not setting file ownership boundaries. Two team members both decided to "improve" the same utility file. They each made changes that were individually sensible but completely incompatible. The merge conflict was a nightmare because both sets of changes were deeply intertwined with each member's other work. Rolling back either one meant cascading changes across their entire contribution.

After that incident, I established a hard rule: every file has exactly one owner. If a team member needs to change a file they don't own, they send a mailbox message to the owner requesting the change. Is this slower? Slightly. Does it prevent catastrophic merge conflicts? Completely.

Mistake number four was underestimating coordination overhead. Agent teams have a real cost beyond tokens — the human time spent monitoring, course-correcting, and reviewing is non-trivial. For my first five agent team sessions, I spent almost as much time managing the team as I saved from the parallelism. It took about ten sessions before I developed the instincts to manage efficiently.

Here's what I should have done from the start: treat the first few agent team sessions as learning investments, not productivity tools. Set expectations low, focus on understanding the coordination dynamics, and build up your management skills before tackling time-sensitive projects.

There's one more limitation I haven't mentioned: you can only run one team per session, and nesting teams (a team member spawning their own sub-team) isn't supported. That constraint shapes how you decompose work in ways I didn't anticipate. More on what I do about that in a moment.

## The Workflow Pattern That Made Everything Click

After all those mistakes, I developed a workflow pattern I call "Focused Specialists with a Watchful Lead." It's not complicated, but it's the accumulation of every lesson I learned the hard way.

**Before starting the team session,** I spend 5-10 minutes writing a project brief. Not for the AI — for me. I identify the 3-4 major work streams, map the dependencies between them, and decide which files belong to which work stream. This upfront planning is the single highest-ROI activity in the entire process.

**The team lead gets a structured prompt** with three things: the overall goal, the decomposition into work streams, and explicit rules about file ownership. I also include coordination triggers — "If any team member encounters a shared type definition that needs changing, they must message all other team members before making the change."

**Each team member gets a focused brief** with their specific deliverables, their owned files, the files they can read but not modify, and any constraints that apply to their work stream. I include the technical standards (TypeScript strict, error handling patterns, naming conventions) directly in each member's brief because they won't see the lead's conversation history.

**During execution,** I check the shared task list every 3-5 minutes. I'm looking for two things: completed tasks that unblock other work streams, and dependency signals that team members might miss. When I spot something, I nudge the relevant team member through the mailbox.

**At the end,** I review the combined output as a whole before accepting any of it. This is where you catch the subtle integration issues — maybe the error codes are consistent across all three work streams but the error message format differs slightly. Catching that before merging saves significant cleanup.

This pattern has reduced my agent team management time from "barely worth it" to about 15-20% of the total session time. That leaves me with a net time savings of roughly 40-60% compared to single-agent approaches on complex, multi-file projects.

One thing that surprised me: the quality of the individual work streams is often higher than what I get from a single long agent session. Fresh context windows make a real difference. Each team member is operating at peak capability because they haven't accumulated an hour of conversation history diluting their attention.

The real magic isn't the parallelism — it's the context isolation. Every team member gets to be the "first 30 minutes of a fresh session" version of Claude, which is demonstrably the best version.

## What This Means for How We'll Build Software

I've been thinking a lot about where this pattern leads. Not in a hype-cycle, "everything changes tomorrow" way — in a practical, "what should I invest time learning now" way.

Agent teams are the first AI coding tool I've used that maps to how actual engineering teams work. A tech lead who coordinates but doesn't implement. Specialists who own specific domains. Communication channels for cross-cutting concerns. Shared visibility into project progress.

That mapping isn't a coincidence. It works because the same coordination principles that make human engineering teams effective also make AI agent teams effective. Clear ownership, explicit communication, focused context, and centralized coordination.

What I expect to see over the next year: nested teams (team members who can spawn their own sub-teams for deeply complex work streams), persistent teams that survive across sessions and build up project context over time, and better tooling for the human oversight layer — dashboards that surface coordination problems before they become merge conflicts.

I also expect the cost to come down significantly. Token costs have been dropping consistently, and as Anthropic optimizes the coordination protocol, the overhead tokens should decrease. My rough prediction: within a year, agent teams will cost 1.5-2x a single agent instead of 3-4x.

The engineers who'll benefit most are the ones who start building their team management skills now, while the feature is experimental and the patterns are still being discovered. By the time this goes mainstream, having two dozen agent team sessions under your belt will be a genuine competitive advantage.

But I want to be careful about overpromising. Agent teams don't make bad engineers good. They amplify existing skills. If you can't decompose a problem into clean work streams, agent teams won't magically do it for you. If you don't understand the code your agents produce, the coordination problems will eat you alive. The fundamental skills of software engineering — problem decomposition, system design, code review — become more important with agent teams, not less.

And here's an unpopular opinion I'll stake my reputation on: agent teams will make the gap between senior and junior engineers wider, not narrower. Seniors who can effectively decompose and coordinate will see 3-5x productivity gains. Juniors who can't yet decompose complex problems will struggle with agent teams the same way they struggle with large codebases — the tool amplifies the underlying skill.

## What I'd Do Differently If I Started Over Today

If I could go back to the day agent teams shipped and start fresh, here's exactly what I'd do.

First, I'd spend the first three sessions on small, low-stakes projects. Not to test the feature's capability — to test my own coordination skills. Managing an AI team is a learnable skill with a real learning curve, and paying that tuition on a throwaway project beats paying it on a deadline-driven one.

Second, I'd establish my file ownership rules before creating the first team. Writing that project brief with clear work stream boundaries isn't optional overhead — it's the foundation that everything else rests on. Skip it and you'll pay in merge conflicts.

Third, I'd start with two team members, not three. Two is enough to learn the coordination dynamics without the complexity of triangular communication. Add a third member once the two-member pattern feels natural.

Fourth, I'd keep a log of coordination failures. Every time two team members produce incompatible work, I'd write down what happened and what signal I missed. After ten sessions, that log becomes a personal playbook for anticipating coordination problems before they manifest.

Fifth — and this is the one I wish someone had told me from day one — I'd benchmark every agent team session against the single-agent approach. Not to justify the feature's existence, but to build an honest intuition for when it's worth the overhead and when it isn't. That intuition is the most valuable thing you can develop, and it only comes from comparative data.

## Pick One Project This Week

That authentication migration I mentioned at the beginning? The one where my single agent lost the plot after an hour? I re-ran it with agent teams. Three members: one for the authentication service itself, one for the dependent microservices, and one for the integration tests. The team lead coordinated the interface contracts between them.

It took 47 minutes. Zero context degradation bugs. One coordination issue — a team member used a different JWT library than the one specified — caught in real time through the mailbox and corrected before it propagated.

The difference wasn't just speed. It was confidence. For the first time in months, I merged AI-generated code across multiple services without that nagging feeling that something subtle was wrong somewhere I hadn't checked.

So here's my challenge to you: pick one project this week that you've been avoiding because it's too complex for a single agent session. Something that touches multiple files, requires coordinated decisions, and would normally take you a full afternoon. Set up an agent team. Follow the patterns I've described. Accept that your first session will be messy — mine certainly was.

Then measure the result. Not against the hype, not against the theoretical best case — against what you would have produced working solo or with a single agent. Let the data speak.

Because the question isn't whether AI agent teams are the future of coding. The question is whether you'll figure out how to use them effectively before everyone else does.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
