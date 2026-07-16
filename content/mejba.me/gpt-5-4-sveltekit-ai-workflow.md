**BRAND:** mejba.me
**TITLE:** GPT 5.4 Changed My Shipping Workflow. Here's How.
**SLUG:** gpt-5-4-sveltekit-ai-workflow
**PRIMARY KEYWORD:** GPT 5.4 workflow
**SECONDARY KEYWORDS:** SvelteKit Convex stack, AI coding tools 2026, shipping products with AI
**META DESCRIPTION:** How I use GPT 5.4 and Claude 4.6 together with SvelteKit and Convex to ship four products in six weeks. My full AI-powered dev workflow breakdown.
**TAGS:** AI Development, Software Engineering, GPT 5.4 SvelteKit, AI Coding Workflow, Builder's Guide

---

Three weeks ago I shipped a product that lets strangers fork my codebase, talk to an AI agent, build a feature I never thought of, and submit it back to me as a pull request -- with a live preview attached.

That sentence would have sounded unhinged two years ago. It sounds only slightly less unhinged now. But the app works, real people are using it, and it took me a fraction of the time I would have needed twelve months ago.

Here's the part that surprised me most: the bottleneck wasn't coding. It wasn't design. It wasn't deployment. The bottleneck was *deciding what to build* -- and that's where GPT 5.4 quietly became the most useful tool in my stack. Not for writing code. For thinking clearly about what code to write.

I'm in the middle of launching four products in about six weeks, and the way I'm pulling this off has almost nothing to do with working harder. It has everything to do with a workflow I've finally gotten right -- the right models for the right tasks, a tech stack that stays out of my way, and a set of tools that make the gap between "idea in my head" and "thing people can use" shorter than it's ever been.

I want to walk through all of it. The products, the stack, the AI models, the tools, and the honest trade-offs I've hit along the way. Because the interesting part isn't any single tool -- it's how they fit together.

## Four Products in Six Weeks (and Why That's Not as Crazy as It Sounds)

A year ago, shipping one polished product in six weeks felt ambitious. Shipping four would have felt delusional. So what changed?

Two things. First, my tech stack got radically simpler. I stopped fighting tools that wanted me to configure everything and started using tools that assumed reasonable defaults. Second, I stopped using AI as a replacement for thinking and started using it as a *thinking partner* -- something that helps me plan better, not just type faster.

The four products I'm working on right now are genuinely different from each other, which is part of what makes this a good stress test:

**Feedback AI** -- this is the one I described in the opening. It's a system where users of your web app can submit feature requests by actually *building* the feature with an AI agent. Not just filing a ticket. Not just writing a description. They fork your repo into a sandbox, work with a coding agent to implement what they want, and submit the result as a PR with a live preview. I think of it as PR 2.0.

**PassBuddy** -- a live app for creating and sharing Apple Wallet passes with custom branding, push notifications, and an analytics dashboard. This one started as a friend's idea and turned into something I genuinely enjoy working on.

**A voice agent deployment platform** -- still in early beta, but actual customers are already using it. You build a voice agent, deploy it, and people can call it. That's the pitch. The implementation is more nuanced, but the core is that simple.

**A fourth product** I'm keeping under wraps for now.

The point isn't to brag about volume. The point is that the workflow enabling this pace is repeatable. And the biggest unlock wasn't a single AI model or a single tool -- it was finding the right combination. Which brings me to the part most people actually care about.

But first, let me talk about what I rebuilt my entire stack around -- because the framework choice made everything else possible.

## Why I Left Next.js for SvelteKit (and Haven't Looked Back)

I know. Everyone has a "why I switched frameworks" post, and most of them are boring. I'll try to make this one not boring.

I'd been building with React and Next.js for years. It worked. I shipped real products with it. But there was this persistent friction I kept ignoring -- the amount of boilerplate, the state management headaches, the build times that crept upward with every dependency. None of it was catastrophic. All of it was slow.

Then I tried SvelteKit on a side project and something clicked. The apps felt faster -- not just to build, but to use. Snappier. More responsive. The DX was cleaner. And here's the thing that tipped the scale for me: AI-generated Svelte code is *better* than AI-generated React code.

That might sound like a strange reason to pick a framework, but think about it. If you're using AI coding assistants for a significant chunk of your frontend work -- and in 2026, if you're not, you're leaving speed on the table -- the quality of AI-generated code in your framework matters enormously. Svelte's syntax is closer to plain HTML and JavaScript. There's less abstraction for the model to get confused by. Fewer foot-guns. The output needs less cleanup.

I paired SvelteKit with **Convex** for the backend, and that combination has become the core of everything I'm shipping right now.

<!-- IMAGE: Screenshot of a SvelteKit + Convex project structure in a code editor showing the clean file organization. Alt text: "SvelteKit Convex project structure showing clean file organization for AI-assisted development". Caption: "The SvelteKit + Convex project structure that powers all four products." -->

### What Makes Convex Different From Every Other Backend

I've used Firebase. I've used Supabase. I've used raw PostgreSQL with ORMs. Convex is something different, and the difference matters for the way I work now.

Convex is a reactive backend -- you define your data schema and your server functions, and the frontend subscribes to queries that automatically update when the underlying data changes. No manual refetching. No WebSocket plumbing. No cache invalidation headaches. You write a query, you use it in your component, and it just... stays current.

But the feature that sold me was the **component system**. Convex has a growing ecosystem of pre-built components that plug into your backend like Lego pieces. I'm currently using:

- **Stripe component** -- payment processing without writing a webhook handler from scratch
- **Autumn component** -- usage-based billing (PassBuddy's pricing model runs on this)
- **Resend component** -- transactional email
- **WorkOS component** -- OAuth authentication

Each of these would normally be a half-day integration minimum. With Convex components, I had all four running in under two hours total. That time savings compounds across four products.

The combination of SvelteKit's clean frontend DX and Convex's reactive backend with plug-and-play components is what makes the "four products in six weeks" timeline realistic instead of reckless. I'm not reinventing infrastructure every time I start something new. I'm assembling proven pieces and spending my time on the parts that are actually unique to each product.

Now here's where the AI models enter the picture -- and where things get really interesting.

## GPT 5.4 vs Claude 4.6: How I Actually Use Both

I know the internet loves a "which AI model is best?" debate. I'm going to sidestep it entirely, because the honest answer is: I use both, for different things, and the combination is better than either one alone.

Here's my split:

**GPT 5.4 handles planning, architecture, and functionality decisions.** When I need to think through how a complex feature should work -- what the data model looks like, what the edge cases are, how different components interact -- GPT 5.4 produces higher quality plans. The reasoning is more structured. The output is cleaner. It catches considerations I might miss on my first pass.

**Claude 4.6 handles frontend development and rapid iteration.** It's faster for interactive work. When I'm building out UI components, tweaking layouts, experimenting with different approaches -- Claude 4.6 feels more fluid. More fun to work with, honestly. There's a responsiveness to the interaction that makes the back-and-forth of frontend development feel like a conversation rather than a series of requests.

I ran an experiment last week that crystallized this. I had a complex feature for Feedback AI -- the sandbox environment that forks a repo, installs dependencies, and spins up an AI coding agent for the user. This feature had a lot of moving parts: container orchestration, file system access, terminal emulation, and a preview build system.

I gave the same feature spec to both models. GPT 5.4's plan was more thorough. It anticipated authentication edge cases I hadn't considered. It suggested a caching strategy for the forked repos that would save significant compute costs. The architecture was just... tighter.

Then I did something interesting: I showed GPT 5.4's plan to Claude 4.6 and asked it to evaluate it. Claude's response was essentially "yeah, this plan is better than what I would have produced." That kind of honesty from a model is useful, because it saves me time. I don't have to A/B test every decision. I can use the right model for the right phase and trust the results.

<!-- IMAGE: Side-by-side comparison of terminal windows showing GPT 5.4 and Claude 4.6 running in Semox with different tasks. Alt text: "GPT 5.4 and Claude 4.6 AI models running side-by-side in Semox terminal for development workflow". Caption: "My actual setup: GPT 5.4 on the left for planning, Claude 4.6 on the right for building." -->

### The Side-by-Side Workflow in Practice

Here's what a typical feature build looks like for me now:

1. **I describe the feature to GPT 5.4.** Not the code -- the *intent*. What the user should experience. What constraints exist. What I'm worried about.

2. **GPT 5.4 generates a plan.** Data models, API endpoints, component structure, error handling strategy. I review it, push back on parts I disagree with, and iterate until the plan feels solid.

3. **I hand the plan to Claude 4.6 for implementation.** Claude works through the plan step by step, generating the actual code. Because the plan is already solid, the code quality is higher than if Claude had been planning and coding simultaneously.

4. **I review, test, and iterate with Claude 4.6.** Frontend tweaks, bug fixes, visual polish -- this is where Claude's speed and interactivity shine.

5. **Before merging, I run the PR through a code review tool.** This is where Grail (the AI code reviewer I mentioned earlier) catches things both models might have missed -- security oversights, performance issues, patterns that don't match the rest of the codebase.

This workflow sounds like a lot of steps, but in practice it's remarkably fast. The planning phase with GPT 5.4 takes maybe 15-20 minutes for a complex feature. The implementation with Claude 4.6 might take an hour or two. The review catches issues before they reach production. Total time from "I have an idea" to "it's deployed and working": often less than a day for features that would have taken me a week a year ago.

The productivity gain isn't 2x. It's closer to 5x. And most of that gain comes not from the coding being faster, but from the *planning* being better. Bad plans executed quickly are still bad. Good plans executed quickly are what actually ships products.

That said, I've made mistakes with this workflow. Let me tell you about the one that almost cost me a product launch.

## The Security Mistake That Almost Shipped to Production

I was building the API for PassBuddy -- the Apple Wallet pass creation tool. The API needed authentication, obviously. But during development, I'd stripped out the auth middleware so I could test endpoints quickly without dealing with tokens.

Standard practice. Every developer does this. The dangerous part is remembering to put it back.

I forgot.

GPT 5.4 had generated the plan. Claude 4.6 had written the code. I'd tested everything. The endpoints worked perfectly. I was about to deploy. And then Grail flagged my pull request with a confidence score of 1 out of 5.

The flag said, roughly: "These API endpoints appear to have no authentication middleware. This is likely a critical security issue."

I stared at it for a good ten seconds before my stomach dropped. Because Grail was right. I'd removed the auth for testing and never added it back. Claude hadn't caught it -- the model was following the plan, which didn't include "re-add the auth you removed." GPT 5.4 hadn't caught it because the plan was written before I'd removed auth for testing. Neither model had the context to know what I'd done.

This is the thing about AI-assisted development that I don't think gets talked about enough: **AI models don't have persistent memory of your ad-hoc decisions.** They know what you tell them. If you make a change outside of a conversation and don't mention it, it doesn't exist in their context. That's not a flaw -- it's a fundamental limitation of the current architecture. And it means you need safety nets.

For me, that safety net is a code review tool that looks at the *actual code* being submitted, not the conversation that produced it. It doesn't care what the plan said. It doesn't care what the AI intended. It looks at what's actually in the diff and flags problems.

I've shipped hundreds of PRs through this workflow since. That one catch alone justified every minute I've spent setting up the review pipeline.

If you're building with AI coding assistants and you don't have an automated review step, you're playing with fire. I learned that the hard way so you don't have to.

Speaking of tools that save me -- let me talk about the development environment I've landed on, because it took me a while to get here.

## My Development Tools: Semox, Cursor, and Why I Use Both

I used to be a single-editor person. VS Code for everything. Then Cursor. Then Ghostty as my terminal. Each switch felt like an upgrade. But my current setup is the first time I've used *multiple* tools simultaneously and felt like the combination was genuinely better than any single tool.

**Semox** is my primary environment now. It's a terminal-first editor that's built around AI agent interaction. Think of it as a terminal that understands it's going to be running Claude Code or Codex most of the time, and optimizes for that use case. The workflow is faster. Switching between agent conversations, file editing, and terminal commands feels seamless in a way that bolted-on AI features in traditional editors don't.

**Cursor** runs alongside Semox for a specific reason: I want to see the code and talk to an agent *at the same time*. Cursor's split view -- agent conversation on one side, code on the other -- is still the best implementation of that I've found. When Claude 4.6 is making changes to a file, I can watch the changes happen in real time in Cursor's code pane. That visual feedback loop is invaluable for frontend work where you need to evaluate changes instantly.

**Vim** shows up occasionally when I just need to read code. No AI. No agent. Just me and a file. Sometimes that's the fastest way to understand something, and I've learned not to feel guilty about it. Not every moment needs to be AI-augmented.

The combination sounds chaotic, but it's actually quite focused:
- Semox for agent-driven work (building features, running commands, iterating)
- Cursor for visual code review alongside agent output
- Vim for quiet reading when I need to think without assistance

Each tool does one thing better than the others. I stopped trying to find the one tool that does everything and started using each tool for its strength. My shipping speed went up noticeably.

Now let me get specific about the product that taught me the most -- because the implementation details reveal things the overview can't.

## Building Feedback AI: Where the Workflow Gets Real

Feedback AI is the product I'm most excited about, and the one that stress-tested my workflow the hardest. The concept is simple to explain and surprisingly complex to build.

Here's the problem it solves: when users of your app want a feature, they file a ticket. Maybe they describe it well. Maybe they don't. Either way, someone on your team has to interpret the request, figure out the implementation, build it, and hope it matches what the user actually wanted. The feedback loop is long and lossy.

Feedback AI flips this. Instead of describing what they want, users *build* what they want -- with help from an AI agent -- and submit the result as a PR with a live preview.

### How the Architecture Works

When a user submits feedback (through a Slackbot, Discord bot, or an embedded widget in your app), here's what happens behind the scenes:

**Step 1:** Your app's repository gets forked into a sandboxed environment. I'm using **Daytona** for this -- it spins up isolated dev environments quickly and reliably. Each sandbox is a full development environment with your project's dependencies pre-installed.

**Step 2:** An AI coding agent gets installed in the sandbox. The user can choose between OpenCode, Claude Code, or Codex. Each has different strengths, and giving users the choice means the system works for different preferences and use cases.

**Step 3:** The user interacts with the AI agent through a chat interface. They describe what they want. The agent asks clarifying questions. They iterate together. The agent writes code, the user provides feedback, and gradually the feature takes shape.

**Step 4:** Once the user is satisfied, the system generates a preview build. This is a running version of the app with their changes applied. They can click around, test it, make sure it actually does what they intended.

**Step 5:** The user submits the result. What you receive as the app owner is a pull request that includes the code changes, the full conversation history between the user and the AI agent, and a link to the live preview.

Compare that to a Jira ticket that says "it would be nice if the dashboard had a dark mode." Night and day.

<!-- IMAGE: Feedback AI interface showing the file explorer, terminal, and diagnostics panel in the sandbox environment. Alt text: "Feedback AI sandbox environment with file explorer terminal and AI agent diagnostics panel". Caption: "The Feedback AI sandbox: file system, terminal, and agent diagnostics in one view." -->

### The Reality Check

I need to be honest about something: this product isn't for everyone. It works best for products with highly engaged users -- the kind of people who would contribute to an open-source project, who think about features in terms of implementation, not just outcomes. If your users are casual consumers who just want things to work, Feedback AI is overkill.

But for developer tools, internal platforms, and prosumer apps? The potential is enormous. Imagine your most engaged users not just telling you what they want, but showing you. With working code. And a preview you can click through. The quality of information you get is incomparably richer than a support ticket.

I built the first version of Feedback AI's sandbox system using GPT 5.4 for the architecture plan and Claude 4.6 for the implementation. The Daytona integration alone had about fifteen edge cases I hadn't anticipated -- container startup timing, dependency installation failures, file system permissions in the sandbox. GPT 5.4 caught about ten of those during planning. The other five showed up during testing. That ratio -- catching two-thirds of edge cases before writing a line of code -- is why the planning phase matters so much.

If you're building something with this level of complexity and you'd rather have someone handle the architecture and implementation, I take on projects like this through my Fiverr profile -- [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD). But honestly, the workflow I'm describing here is learnable. The tools are accessible. The expensive part isn't the technology -- it's the judgment about when to use which tool.

That brings me to PassBuddy, which taught me something different.

## PassBuddy: Usage-Based Billing With Autumn and Convex

PassBuddy is a simpler product than Feedback AI, but it has one piece that was surprisingly tricky: the monetization model.

The app lets you create Apple Wallet passes with custom branding -- your logo, your colors, your URL, your message. You share a link, someone opens it on their iPhone, and the pass drops into their wallet. You can then send push notifications to everyone who installed your pass. It's like having a direct channel to your audience's lock screen.

The free tier gives you one pass and 15,000 push notifications per month. The paid tier is $29/month for three passes. Simple enough pricing, but the implementation was interesting because it's **usage-based billing** -- you're paying for what you use, not for a flat feature set.

This is where **Autumn** came in. Autumn is a billing component that plugs into Convex and handles usage-based pricing out of the box. You define your pricing tiers, your usage meters (number of passes created, number of notifications sent), and Autumn tracks everything automatically. No custom webhook handlers. No manual usage counting. No reconciliation nightmares.

I migrated PassBuddy's backend to Convex specifically for this. The original version used a different stack, and the billing logic was a mess -- custom code tracking usage, manual Stripe invoice adjustments, edge cases around usage resets at billing period boundaries. Autumn eliminated all of that.

The migration itself was another test of the dual-model workflow. I used GPT 5.3 (this was before 5.4 dropped) and Codex together for the code migration, which is a specific kind of task that benefits from having two models work on it. One model maps the old code to the new patterns, the other reviews the mapping for correctness. It's slower than using one model, but the error rate drops dramatically.

**Image processing** was the other tricky part. Apple has absurdly strict specifications for pass images -- specific pixel dimensions, specific file formats, specific color spaces. Get any of these wrong and the pass either looks terrible or just doesn't load. I'm using **ImageKit** for processing uploaded images to meet Apple's specs automatically. Users upload whatever they have, ImageKit transforms it to Apple's requirements, and the pass looks correct every time.

The analytics dashboard rounds out the product -- created passes, installation counts, notification delivery stats. All powered by Convex's reactive queries, so the numbers update in real time as people install passes and receive notifications. No polling. No refresh button. The data just appears.

## What I'd Tell Someone Starting This Workflow From Scratch

I've been iterating on this setup for months, and I've hit enough walls to have opinions about what matters and what doesn't. Here's the honest version.

**Start with the stack, not the models.** I wasted weeks early on trying to optimize which AI model I used before I had a tech stack that was easy for AI to work with. Once I switched to SvelteKit and Convex, the output quality from *every* model improved because the framework was simpler and less ambiguous. The stack matters more than the model.

**Use two models, not one.** I know this adds complexity. I know it costs more. Do it anyway. The planning model and the implementation model serve genuinely different cognitive functions. Trying to make one model do both is like trying to make one person be both the architect and the contractor. Some can do it. Most produce better results when the roles are separated.

**Invest in your review pipeline before you need it.** Don't wait until you ship a security bug to production. Set up automated code review on day one. The cost is minimal. The downside protection is enormous. I use Grail for this, but the specific tool matters less than having *something* that reviews AI-generated code before it ships.

**Get comfortable with multiple editors.** The "one tool for everything" dream is appealing but limiting. Each tool in my stack -- Semox, Cursor, Vim -- does one thing better than the others. The overhead of switching between them is tiny compared to the productivity gain of using each one at its best.

**AI is a thinking partner, not a replacement for thinking.** This is the one I keep relearning. The moment I start treating GPT 5.4 or Claude 4.6 as a "just code this for me" button, the quality drops. The best results come from genuine collaboration -- me bringing context and judgment, the AI bringing speed and breadth. Neither one alone is as good as both together.

Here's what I'm measuring across the four products to know this workflow is actually working: time from feature idea to deployed code, number of bugs caught before production, and how often I have to rewrite AI-generated code versus simply adjusting it. Across the board, the trend lines are moving in the right direction. Features ship faster. Fewer bugs escape review. Less rewriting.

The pattern I've observed in my own projects -- and in talking to other builders using similar setups -- is that the first product takes the longest because you're establishing the workflow. The second takes half the time. By the third and fourth, you're in a rhythm where the tools disappear and you're just building.

That's where I am now. The tools have disappeared. I'm just building.

## What Changes When Shipping Gets This Fast

There's a second-order effect of this workflow that I didn't anticipate: it changes what you're willing to build.

When shipping a product takes three months, you think very carefully about what to build. You do market research. You validate demand. You make sure the idea is worth the investment. All of that makes sense when the cost of building is high.

But when shipping a product takes two weeks? The calculus changes completely. PassBuddy started as a friend's casual idea. "Wouldn't it be cool if you could make custom Apple Wallet passes?" In the old world, I'd have said "yeah, that would be cool" and moved on. In this world, I just... built it. And it's live. And people are using it. And it has paying customers.

The voice agent platform started the same way -- a small experiment that turned into something real because the cost of trying was so low.

I think this is where the AI-assisted development conversation should be focused. Not on "will AI replace developers?" (it won't), but on "what happens when the cost of building drops by 80%?" The answer is: people build more. They experiment more. They ship things they wouldn't have bothered with before. Some of those things fail. Some of them find an audience nobody expected.

Four products in six weeks. A year ago, that would have been four products in six months. The workflow made the difference. GPT 5.4 for planning. Claude 4.6 for building. SvelteKit and Convex for the foundation. Semox and Cursor for the environment. Grail for the safety net.

None of these tools are magic. All of them together, used deliberately, with good judgment about when to apply each one -- that's as close to magic as shipping software gets right now.

What are you building this month that you would have said "no" to last year?

## Frequently Asked Questions

### Is GPT 5.4 better than Claude 4.6 for coding?

They excel at different things. GPT 5.4 produces better architectural plans and catches more edge cases during the design phase. Claude 4.6 is faster and more interactive for actual code implementation, especially frontend work. I use both in sequence -- GPT for planning, Claude for building. For a deeper look at how I split tasks between them, see the "GPT 5.4 vs Claude 4.6" section above.

### Why switch from Next.js to SvelteKit in 2026?

SvelteKit's syntax is closer to plain HTML and JavaScript, which means AI coding assistants generate cleaner code with less cleanup needed. Combined with Convex's reactive backend, the DX is faster and the apps feel snappier. The AI-readability of the framework was the deciding factor for me.

### What is Convex and how is it different from Supabase or Firebase?

Convex is a reactive backend where frontend queries automatically update when data changes -- no manual refetching or WebSocket setup. Its component system lets you plug in pre-built integrations (Stripe, auth, email) in minutes instead of hours. See the full stack breakdown in "What Makes Convex Different" above.

### How does Feedback AI work for feature requests?

Users fork your repo into a sandbox, collaborate with an AI coding agent to build the feature they want, then submit a PR with working code and a live preview. It replaces traditional feature request tickets with actual implementations. The architecture details are in "Building Feedback AI" above.

### What tools do you use alongside AI coding assistants?

Semox as my primary terminal/editor for agent-driven work, Cursor for visual code review alongside agent output, Vim for quiet code reading, and Grail for automated PR review. The multi-tool approach is faster than any single editor trying to do everything.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
