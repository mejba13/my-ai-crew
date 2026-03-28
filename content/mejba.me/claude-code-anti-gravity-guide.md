---
title: "Claude Code Anti-Gravity: The IDE Setup That Changed How I Build"
slug: claude-code-anti-gravity-guide
tags:
  - Claude Code
  - AI Development
  - Anti-Gravity IDE
  - BLAST Framework
  - Workflow Guide
meta_description: "How I combined Claude Code with Google's Anti-Gravity IDE to build faster, manage context better, and automate entire workflows using the BLAST framework and Claude Skills."
---

# Claude Code Anti-Gravity: The IDE Setup That Changed How I Build

I was halfway through a client project last month when my terminal-only Claude Code workflow hit a wall. Not a technical wall -- a cognitive one. I had six terminal tabs open, three different context windows burning through tokens, and I'd lost track of which agent was working on which feature. The code was fine. My ability to manage it was falling apart.

That same week, I stumbled on Jack Roberts' deep-dive tutorial about pairing Claude Code with Google's Anti-Gravity IDE, and something clicked. Not just "oh that's neat" clicked -- more like "I've been doing this wrong for months" clicked. Within a weekend, I'd restructured my entire AI coding workflow around this combination, and the difference was jarring enough that I need to write about it.

Here's the part that surprised me most: the terminal isn't the bottleneck. The terminal is great. Claude Code in a raw terminal is still one of the most powerful development tools I've ever used. The bottleneck was everything around the terminal -- file navigation, task tracking, context management, visual feedback on what the AI actually changed. Anti-Gravity solved problems I didn't realize I was compensating for.

But the IDE is only half the story. The real transformation came from a prompting framework called BLAST and a feature called Claude Skills that turned repetitive workflows into one-command automations. I'll break down both -- and share exactly how I set everything up -- but first, you need to understand why the standard Claude Code workflow starts leaking productivity at scale.

## Why Terminal-Only Claude Code Breaks Down at Scale

I love the terminal. My Ghostty setup is dialed in, my tmux config is pristine, and I can navigate a codebase without touching a mouse. So when I first heard people talking about running Claude Code inside an IDE, my gut reaction was skepticism. Why add visual overhead to something that works perfectly in text?

The answer became obvious once I started managing more than two concurrent tasks.

Claude Code in a terminal is phenomenal for focused, single-stream work. You give it a task, it executes, you review, you iterate. That loop is tight and fast. The problem appears when your project involves coordinating multiple agents, tracking changes across dozens of files, and switching between different types of work -- debugging here, building a new feature there, configuring deployment somewhere else.

In a terminal, all of that context lives in your head. You're the router, the task manager, and the quality gate all at once. After about four hours of this, my decision quality drops noticeably. I start approving changes I should scrutinize. I forget which files an agent modified twenty minutes ago. I lose the thread of what the original goal was for a particular feature branch.

Anti-Gravity changes this equation by externalizing that cognitive load into a visual interface. File changes get highlighted in a sidebar. Task management happens in dedicated panels. Multiple interaction modes -- sidebar chat, terminal, extensions -- let you pick the right tool for the specific type of work you're doing at that moment.

This isn't about Anti-Gravity being "better" than a terminal. It's about pairing two tools that complement each other in ways I didn't expect. The terminal handles execution speed. The IDE handles situational awareness.

And that pairing matters more than most developers think, because of something most Claude Code tutorials barely mention: context window degradation.

## The Context Window Problem Nobody Warns You About

Here's a fact that changed how I structure every Claude Code session: once your context window crosses roughly 50% capacity, output quality starts degrading. Not crashing -- degrading. The responses get slightly less precise. The code gets slightly more generic. Hallucinations creep in around the edges. If you're not paying close attention, you won't notice until you're debugging phantom issues that trace back to a context-bloated session.

I confirmed this through a crude but effective experiment. I took the same coding task -- building a REST API endpoint with validation, error handling, and tests -- and ran it at different context utilization levels. At 20% context, the generated code was tight, well-structured, and matched my project conventions almost perfectly. At 60%, the code worked but missed some of my naming patterns and included a few unnecessary imports. At 80%, it generated a working endpoint but with a completely different error handling pattern than what existed in the rest of the codebase.

Same prompt. Same model. The only variable was how much context was already loaded.

This is why the "one window per task" discipline matters so much. Before I understood this, I'd use a single Claude Code session for hours, loading file after file, asking question after question, building feature after feature. The first hour was magic. By hour three, I was spending more time correcting the AI than it was saving me.

Anti-Gravity helps here because its interface makes context usage visible. You can see how much of your window is consumed, which makes it natural to close a session and start fresh when things start getting crowded. In a terminal, context usage is invisible -- you only feel it when the outputs get worse, and by then you've already wasted cycles.

The practical rule I follow now: one context window per task. Finish a feature, close the session, start a new one for the next feature. It feels wasteful -- like you're throwing away "memory" the AI built up. But the memory past 50% is more harmful than helpful. Fresh context with a clear, focused prompt produces better results than a deep context polluted by three hours of unrelated conversations.

That realization alone saved me more time than any single tool or technique. But the real productivity unlock came when I learned how to structure prompts for this constraint using the BLAST framework.

## The BLAST System: A Prompting Framework That Actually Scales

Most prompting advice boils down to "be specific" and "provide context." Useful, but vague. The BLAST framework -- which Jack Roberts demonstrated in his Anti-Gravity tutorial -- gives that advice a concrete structure I can repeat consistently across projects.

BLAST stands for Blueprint, Linkages, Architecture, Stylize, and Trigger. Each component addresses a specific failure mode I've seen in AI-assisted development. Let me walk through how I actually use each one, because the theory is less interesting than the practice.

**Blueprint** is the project specification. Not a vague description -- a structured document that describes what you're building, why it exists, and what success looks like. I keep my blueprints in a `BLUEPRINT.md` file at the project root, and I reference it in my Claude Code system prompts using global rules. A good blueprint answers three questions: what does this project do, who is it for, and what are the non-negotiable technical constraints?

For a recent SaaS dashboard project, my blueprint was about 400 words. It specified the user persona (operations managers at mid-size logistics companies), the core workflow (monitoring delivery exceptions in real-time), the tech stack (Next.js 15, Supabase, Tailwind), and three constraints (must load under 2 seconds on 3G, must support offline mode, must integrate with their existing Slack notification system). When Claude has this blueprint in context, every generated component aligns with these constraints without me re-specifying them in every prompt.

**Linkages** define how components connect. This is the part most developers skip, and it's the part that causes the most rework. Linkages describe your data flow, API contracts, and component dependencies. I write these as simple relationship maps: "The dashboard component consumes data from the exceptions API, which queries the Supabase `delivery_exceptions` table, which gets populated by the webhook handler connected to the client's logistics platform."

Without linkages, Claude builds components that work in isolation but don't connect cleanly. The dashboard might expect data in one shape while the API returns it in another. By specifying linkages upfront, I've cut integration bugs by what feels like 70% -- I don't have hard metrics on that, but my git history tells a clear story of fewer "fix integration" commits since I started using this approach.

**Architecture** covers your file structure, naming conventions, and organizational patterns. Where do new components go? How are routes structured? What's the testing pattern? I feed Claude a summary of the existing architecture so it matches the codebase conventions rather than inventing its own.

**Stylize** handles code style -- but not just formatting. It covers patterns like error handling conventions, logging standards, how you structure async operations, and whether you prefer composition or inheritance. My stylize section for most projects is a list of about fifteen specific rules, things like "use early returns instead of nested conditionals" and "all API errors must include a machine-readable error code and a human-readable message."

**Trigger** is the actual prompt -- the specific instruction for the current task. Because the other four components handle context, the trigger can be short and focused. "Build the delivery exceptions API endpoint that returns paginated results with filtering by date range and severity level." That's it. Claude has the blueprint, linkages, architecture, and style rules already loaded. The trigger just points it at the specific work.

Here's what makes BLAST work inside Anti-Gravity specifically: you can store the Blueprint, Linkages, Architecture, and Stylize components as global or local rules in the IDE's configuration. Global rules apply across all projects. Local rules live in your project directory and apply only to that codebase. The Trigger is the only thing you type fresh each time.

This separation -- persistent context in rules, fresh context in triggers -- maps perfectly onto the context window constraint I mentioned earlier. Your rules load efficiently because they're structured and consistent. Your trigger uses minimal context because it doesn't need to re-explain the project. The result is more of your context window available for actual code generation.

I tried working without BLAST for a week after adopting it, just to calibrate the difference. The quality gap was significant enough that I went back within two days.

But a framework is only as good as your ability to execute it consistently, and that's where Claude Skills changed the game for me.

## Claude Skills: Turning Repetitive Workflows Into One-Command Automations

If the BLAST framework is the strategy, Claude Skills are the tactics. Skills are custom automations you define once and trigger repeatedly -- reusable instructions that Claude follows when activated. Think of them as saved workflows that encode your best practices so you don't have to re-explain them in every session.

There are two types of skills, and the distinction matters more than it might seem.

**Static skills** are fixed instruction sets. They don't change between executions. "When I say 'new component,' create a React component in `/src/components/` with a TypeScript interface for props, a Storybook file, and a test file using Vitest" -- that's a static skill. Same instructions, same output structure, every time. I have about a dozen of these for common patterns in my projects: creating API endpoints, setting up database migrations, scaffolding test suites, generating documentation stubs.

**Dynamic skills** adapt based on input or context. This is where things get genuinely powerful. A dynamic skill can inspect the current project, read existing files, make decisions based on what it finds, and execute differently depending on the situation. Jack Roberts demonstrated one in his tutorial that blew my mind: a website cloning and enhancement skill.

The skill worked like this: give it a URL, and it would use Firecrawl to scrape the site's structure and content, analyze the design patterns, then rebuild the site with improvements using modern tooling -- all through a single command. It pulled in the Nano Banana 2 API for enhanced image generation when the original site's images needed upgrading, and used ImageB for image processing. The entire pipeline -- scrape, analyze, redesign, generate assets, build -- happened in one skill activation.

I adapted a simplified version of this for my own work. My version doesn't clone competitor sites (the ethics of that get murky fast), but it does analyze an existing page I'm redesigning, extracts the content structure and key UI patterns, and generates a new implementation that preserves the content while modernizing the interface. It saves me about two hours per page compared to manual analysis and rebuild.

Here's how I built that skill in Anti-Gravity. You access the Skill Creator through the Claude Code interface, and it walks you through defining the skill's trigger phrase, input parameters, instruction set, and any external tools or APIs it needs. The key insight is treating skill creation like writing a very detailed function signature: clear inputs, clear outputs, clear side effects.

My redesign skill takes three inputs: the URL of the page to analyze, the target tech stack (defaulting to Next.js + Tailwind), and a style reference (usually a screenshot or Figma link of the desired aesthetic). The instruction set tells Claude to first analyze the source page's content hierarchy, then map that hierarchy to the target tech stack's component patterns, then generate the components with the style reference applied. Each step produces intermediate output I can review before the next step executes.

The intermediate review step is critical. I learned the hard way that fully autonomous multi-step skills produce impressive results about 70% of the time and spectacular failures the other 30%. Adding review checkpoints between steps drops the failure rate to near zero, at the cost of some manual intervention. Worth the trade-off every time.

For API key management across skills -- since many of them call external services -- I use environment variables stored in a `.env` file that Anti-Gravity loads automatically. Firecrawl API key, image generation API keys, database connection strings -- they all live in environment variables rather than being hardcoded in skill definitions. This keeps sensitive credentials out of your skill configurations and makes it trivial to switch between development and production environments.

One more thing about skills that took me a while to appreciate: they compound. A skill that scaffolds a component can be combined with a skill that writes tests, which chains into a skill that generates documentation. Building a library of composable skills is like building a library of shell scripts, except each script has access to an AI reasoning engine that can adapt to the current context.

The developers I know who are most productive with Claude Code all have one thing in common: a rich skill library they've built over time. The upfront investment in creating each skill is maybe 20-30 minutes. The ongoing time savings multiply with every use.

## Setting Up the Full Stack: Anti-Gravity, Claude Code, and Everything In Between

Alright, let me walk through the actual setup process because a few details tripped me up and they'll probably trip you up too.

**Step 1: Claude Code installation and plan selection.** Claude Code itself is free to start with, but the free tier has meaningful limitations on usage volume and model access. The Pro plan at $20/month unlocks higher rate limits and access to Claude Opus, which is the model I use for complex architectural decisions and multi-file refactors. For most coding tasks, Claude Sonnet handles the work fine, but when I need the model to reason about system design or untangle a particularly gnarly bug, Opus is worth every penny. Install the Claude Code app from Anthropic's site -- it's a standalone application that also integrates into your terminal.

**Step 2: Anti-Gravity IDE setup.** Anti-Gravity is Google's IDE that sits on top of the standard code editing experience but adds AI-native features. Download it from Google's developer tools page. The key integration point is that Anti-Gravity recognizes Claude Code as a backend AI provider, so you get Claude's reasoning capabilities inside Google's IDE interface. The IDE gives you multiple interaction modes right out of the gate.

First, there's the **sidebar chat** -- a persistent conversation panel that stays open while you code. I use this for quick questions, code explanations, and small modifications. It doesn't consume a separate context window from your main terminal session, which is important for the context management strategy I described earlier.

Second, you get **extensions** that add Claude-powered features to specific file types and workflows. The extensions I use most are the code review extension (highlights potential issues before I commit) and the refactoring extension (suggests improvements to selected code blocks with one-click application).

Third -- and this is where most of the heavy lifting happens -- you still have the **terminal** embedded directly in the IDE. This is your full Claude Code experience, identical to what you'd get in a standalone terminal, but now surrounded by the visual context of your project's file tree, open editors, and task panels.

**Step 3: Configure global and local rules.** This maps directly to the BLAST framework. Global rules go in your Claude Code configuration directory and apply to every project you work on. Mine include general coding preferences: TypeScript strict mode, prefer functional patterns, always include error handling, write self-documenting code. Local rules live in a `.claude/` directory at your project root and contain project-specific instructions: the tech stack, naming conventions, API patterns, and any constraints unique to that codebase.

Anti-Gravity reads both rule sets and applies them automatically when you interact with Claude through any mode -- sidebar, extension, or terminal. You configure rules once, and every Claude interaction respects them. No more copying pasting the same "remember to use TypeScript strict mode" line into every prompt.

**Step 4: Model selection.** Anti-Gravity supports multiple models, including Gemini 3.1 (Google's model) and Claude Opus. I've tested both extensively for coding tasks, and here's my honest take: Gemini 3.1 is strong for code completion, inline suggestions, and quick edits. Claude Opus is stronger for multi-file reasoning, architectural decisions, and tasks that require understanding the relationships between components. My setup uses Gemini for the sidebar chat (fast, lightweight interactions) and Claude Opus for terminal sessions (deep, complex work). You can configure this in Anti-Gravity's model settings -- different models for different interaction modes.

**Step 5: Connect external services.** This is where the setup gets genuinely exciting. Anti-Gravity supports connectors for Gmail, Notion, Google Calendar, and other services. I connected my Notion workspace so Claude can reference my project documentation and task boards directly. I connected my calendar so scheduled agents (more on this in a moment) can check for conflicts before booking focus time blocks for long-running tasks.

The connector setup involves OAuth flows for each service -- standard stuff, nothing exotic. The payoff is that Claude's context now extends beyond your codebase into your broader work environment. When I ask Claude to "check the current sprint tasks and pick the highest priority backend ticket," it reads directly from my Notion board and generates a plan based on the actual ticket description. No copy-pasting ticket details into prompts.

**Step 6: GitHub and Vercel integration.** These two deserve special mention because they close the loop from development to deployment. The GitHub integration lets Claude create branches, commit changes, and open pull requests without leaving the IDE. The Vercel integration lets it trigger deployments and check preview URLs. My typical flow now: Claude builds a feature in a terminal session, creates a PR through the GitHub connector, Vercel automatically deploys a preview, and I review the live preview alongside the code diff. All within Anti-Gravity. The deployment feedback loop that used to take 15 minutes of tab-switching now happens in about 90 seconds.

One gotcha with the GitHub integration: make sure your Git credentials are configured via SSH keys or a credential helper before connecting. The first time I tried it with HTTPS credentials, the auth flow failed silently and Claude just reported "unable to push" without useful error context. SSH keys solved it immediately.

## Multi-Agent Management: Running Parallel Workflows Without Losing Your Mind

This is the section I wish existed when I first started scaling Claude Code usage. Running a single Claude agent is straightforward. Running three or four concurrently on different tasks within the same project is where most people's workflows collapse.

The core problem is coordination. Agent A is refactoring the authentication module while Agent B is building a new dashboard component that depends on the auth module's API. If they both modify shared files without awareness of each other, you get merge conflicts at best and subtle data flow bugs at worst.

Anti-Gravity's task manager helps here by maintaining a registry of active agents and their current scope. You can see which files each agent is working on, which files are "locked" by active modifications, and which tasks are queued versus in progress. This visibility alone prevents most coordination failures -- I can see that Agent A is modifying `auth.ts` and hold Agent B's task until that modification completes.

The agent scheduling feature takes this further. I can define a sequence of tasks -- "first refactor auth, then build dashboard, then write integration tests" -- and the scheduler executes them in order, passing relevant context between stages. Each task gets a fresh context window (remember the 50% degradation rule), but the scheduler carries forward a summary of what previous tasks accomplished and which files they modified.

For truly independent tasks -- work on unrelated features that don't share files or APIs -- I run agents in parallel without scheduling. Anti-Gravity can manage concurrent sessions in separate terminal panes, each with their own context window and task scope. The task manager shows me a unified view of all active work, so I can monitor progress without switching between terminals.

My typical daily pattern: I start the morning by queuing three to four tasks in the scheduler based on my sprint priorities. The first one or two are sequential (usually dependent features). The rest are parallel (independent features or bug fixes). I review output as each task completes, approve or request revisions, and the scheduler moves to the next item. On a productive day, this setup generates more reviewed, tested, committed code by lunch than I used to produce in a full day of manual coding.

One honest caveat: the multi-agent approach requires good test coverage to catch integration issues. I run the full test suite after each agent completes its task, and I've caught subtle regressions that looked fine in isolation but broke when combined. Without tests, parallel agents would create more problems than they solve. This isn't a shortcut around engineering discipline -- it's a multiplier on top of it.

## What I Got Wrong and What I'd Do Differently

I want to be straight about the mistakes I made while building this workflow, because the tutorials make everything look smooth and the reality has friction.

**Mistake one: over-automating too early.** I got excited about Claude Skills and tried to create automations for everything in the first week. Most of those early skills were too rigid -- they assumed specific project structures and broke when I used them on different codebases. The skills I use daily now are the ones I built after a month of manual repetition, when I deeply understood the workflow I was automating. Build skills from patterns you've proven manually. Don't try to automate workflows you haven't done by hand at least ten times.

**Mistake two: ignoring the context window until it was too late.** For the first two weeks, I treated the 50% context rule as a guideline rather than a hard boundary. "I'll just push through this session, it's almost done." The code generated in those over-extended sessions cost me more debugging time than starting a fresh session would have. Now I treat it as a hard rule. When my context feels heavy, I close and restart. Every time.

**Mistake three: using the wrong model for the wrong task.** I defaulted to Claude Opus for everything because it felt like using the "best" model would give the best results. Turns out, Opus is overkill for simple file modifications and actually slower than Sonnet for straightforward tasks. The overhead of Opus's deeper reasoning adds latency without proportional quality improvement when the task is "add a loading spinner to this button." Match model complexity to task complexity. Use Opus for architecture and design decisions. Use Sonnet (or even Gemini 3.1 for inline completions) for implementation tasks where the direction is already clear.

**Mistake four: not investing enough in local rules.** My first projects with this setup had thin local rules -- maybe five or six lines. The AI constantly made assumptions that didn't match my project's conventions, and I spent time correcting it. Now my local rules files are 40-60 lines and include specific examples of preferred patterns alongside the rules themselves. Showing Claude an example of the error handling pattern you want is ten times more effective than describing it in abstract terms.

**What I'd do differently if starting over:** I'd spend the entire first day just setting up rules and testing them against sample prompts before writing any real code. The configuration time pays for itself within the first week, but I was too impatient to front-load it and paid the price in correction cycles for a month.

## The Measurable Impact: Before and After This Setup

I want to share specific numbers because vague claims about "productivity gains" are useless without context.

**Before (terminal-only Claude Code, no framework):**
- Average time to build a CRUD feature with tests: 3.5 hours
- Context-related debugging per week: roughly 4 hours
- Failed deployments due to integration issues: 2-3 per week
- Skills/automations: zero, everything manual

**After (Anti-Gravity + BLAST + Claude Skills, 6 weeks in):**
- Average time to build a CRUD feature with tests: 1.2 hours
- Context-related debugging per week: under 30 minutes
- Failed deployments due to integration issues: maybe 1 per month
- Active skills in my library: 14 (8 static, 6 dynamic)

The CRUD feature metric is the most reliable comparison because it's the most standardized task. Other work -- complex integrations, novel features, architecture design -- is harder to benchmark because no two tasks are identical. But subjectively, the improvement feels consistent across all task types.

The context-related debugging metric is the most satisfying improvement. Four hours a week of "why is Claude generating weird code" evaporated almost entirely once I adopted the one-window-per-task discipline and started monitoring context usage through Anti-Gravity's interface.

The deployment metric improved partly because of better code quality from fresh context windows, and partly because the GitHub-to-Vercel integration catches issues in preview deployments before they hit production. Both factors stem from the Anti-Gravity setup.

One metric I can't quantify but feel strongly: cognitive fatigue. The old workflow left me mentally drained by 3 PM. Managing everything in my head -- which agent did what, which files changed, where I left off -- consumed energy that should have gone toward actual engineering decisions. Offloading that management to the IDE freed up mental bandwidth that I notice most in the quality of my afternoon work.

Quick wins to expect in the first week: the global and local rules alone will noticeably improve Claude's output quality. The sidebar chat will reduce your context switching between browser docs and terminal. The file change visibility will catch unintended modifications that terminal-only workflows miss.

Longer-term gains (weeks 3-6): your skill library starts compounding. Multi-agent workflows become practical. The BLAST framework becomes muscle memory rather than a conscious process. The full system -- IDE, framework, skills, agents -- starts feeling like a single integrated tool rather than a collection of parts.

## Where I Think This Is Heading

Six months ago, I was writing Claude Code prompts in a terminal and feeling productive. Now I'm orchestrating multiple AI agents through a visual interface with custom automations and integrated deployment pipelines. The pace of change in this space is genuinely disorienting, and I think we're still in the early chapters.

The combination of Claude Code's reasoning capabilities with Anti-Gravity's visual management layer points toward a future where the IDE isn't just a place to write code -- it's a command center for directing AI agents that write code on your behalf. Your job shifts from implementation to specification, review, and orchestration.

That shift scares some developers. I get it. But from where I'm sitting, it doesn't make engineering skills less valuable. It makes them more valuable. The developers who understand system design, who can write clear specifications, who know how to structure a project for maintainability -- they're the ones who get the most out of these tools. The AI handles the typing. You handle the thinking.

If you take one thing from this post, make it the context window discipline. One window per task. Fresh context for fresh work. Everything else I described builds on that foundation, and it costs nothing to implement right now in whatever setup you're already using.

The tools will keep evolving. Google will ship updates to Anti-Gravity. Anthropic will improve Claude's coding capabilities. New frameworks and techniques will emerge. But the principle of managing your AI's context like a scarce resource -- that's a permanent insight. And it's the one that made the biggest difference in my work.

What does your Claude Code workflow look like right now? I'm genuinely curious whether others have found different solutions to the context management problem, or whether the one-window-per-task approach is as universal as it's been in my experience.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
