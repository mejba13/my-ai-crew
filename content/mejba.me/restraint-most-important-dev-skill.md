**BRAND:** mejba.me
**TITLE:** Restraint Is the Most Important Dev Skill in 2026
**SLUG:** restraint-most-important-dev-skill
**PRIMARY KEYWORD:** restraint software development
**SECONDARY KEYWORDS:** spec-driven development, plan mode AI coding, feature bloat
**META DESCRIPTION:** AI lets you build anything in a weekend. The skill that separates great products from bloated ones isn't speed — it's knowing what NOT to build.
**TAGS:** AI Development, Software Engineering, Product Strategy, Claude Code, Opinion
**CONTENT TYPE:** Opinion
**CONTENT CLUSTER:** Vibe Coding & Automation
**TRANSFORMATION GOAL:** After reading, the reader will adopt a pre-planning framework that forces strategic product decisions before touching any AI coding tool.

---

# Restraint Is the Most Important Dev Skill in 2026

Last month, a friend showed me his client portal product. Clean interface. Customers loved it. Simple deliverable sharing, approval workflows, a focused tool that did one thing extremely well. He was proud of it. He should have been.

Then he opened his backlog and showed me the feature requests that had come in over the past quarter. Invoicing. Time tracking. Project management with Gantt charts. A built-in chat system. A reporting dashboard. He could build every single one of them — most in a weekend, using Claude Code and a well-structured prompt. The AI didn't just make these features possible. It made them trivially easy.

He built the invoicing module first. Then the time tracker. Within six weeks, his clean client portal had become a bloated project management suite that competed poorly with Asana, poorly with FreshBooks, and poorly with Slack — all simultaneously. Onboarding time tripled. Support tickets shifted from "how do I share a deliverable?" to "where did the approval button go?" His original customers — the ones who chose his product specifically because it was focused — started leaving.

The tool that let him build faster than ever was the same tool that let him destroy his product faster than ever. And the skill he was missing had nothing to do with code.

---

## The Bottleneck Nobody Talks About Anymore

For years, the constraint on software teams was execution speed. You had more ideas than capacity. The backlog was a graveyard of features that would never get built because there simply wasn't enough engineering time. Planning took maybe 20% of the cycle — the rest was heads-down implementation, wrestling with dependencies, debugging edge cases, shipping.

That constraint is gone.

AI coding tools in 2026 have removed the execution bottleneck so completely that the old planning-to-building ratio has inverted. Where teams used to spend 80% of their time building and 20% planning, the teams I work with now spend the majority of their time on a question that barely existed three years ago: *should we build this at all?*

This isn't a subtle shift. It's a fundamental change in what makes a software developer valuable. The Standish Group's Chaos Report found that 50% of features in custom software are rarely or never used. That statistic is from an era when building features was expensive and slow. When building is cheap and fast, the percentage of unused features doesn't go down — it goes *up*, because there's no natural friction to prevent overbuilding.

The Project Management Institute reports that roughly half of all projects suffer from scope creep. Again — that's from an era of constraints. Remove the constraints, and scope creep doesn't just persist. It accelerates.

Here's what I've realized after watching this play out across my own projects and the teams I advise: the most valuable skill in software development is no longer the ability to build. It's the discipline to decide what *not* to build. And that discipline has a name.

Restraint.

---

## Why AI Can't Replace This Skill

I spend most of my working hours inside Claude Code. I've written about how it's [changed my entire development workflow](https://mejba.me/blog/vibe-coding-is-dead), how [agent teams can tackle complex projects in parallel](https://mejba.me/blog/claude-code-agent-swarm-architecture), how [task management across sessions has transformed multi-file refactors](https://mejba.me/blog/claude-code-task-management). I'm not an AI skeptic. I'm deep in this ecosystem.

But here's what I've noticed after months of shipping with these tools: AI is extraordinarily good at the *how* and genuinely terrible at the *whether*.

Ask Claude Code to build an invoicing module for a client portal. It'll generate clean data models, a solid UI, proper validation, edge case handling — the whole thing. What it won't do — what it *can't* do — is tell you that adding invoicing will confuse your core users, cannibalize your product's identity, and put you in direct competition with FreshBooks, a company with a decade of domain expertise and $100M in annual revenue.

That's not a technical decision. That's a product decision. And it requires something AI fundamentally lacks: an understanding of your customer's context, your competitive position, and the second-order consequences of adding surface area to your product.

I've started thinking about this as the difference between capability and judgment. AI gives you near-infinite capability. Judgment is the thing that tells you which capabilities to actually deploy. And judgment only comes from understanding the *people* who use your software — their frustrations, their workflows, the reason they chose your tool over the twelve alternatives.

No model, no matter how large the context window, can replicate that understanding. Not yet. Maybe not ever.

---

## The Client Portal Problem — A Pattern I Keep Seeing

The client portal story I opened with isn't a one-off. I've watched this pattern repeat across half a dozen products in the past year, and it always follows the same arc.

**Phase 1: Focus.** The product starts clean. It solves one problem well. Customers love it because it's simple, opinionated, and fast. The team gets positive reviews specifically because the product *doesn't* try to do everything.

**Phase 2: Feature Pressure.** Customers start requesting adjacent features. "Can you add invoicing?" "What about time tracking?" "It would be great if we could manage projects here too." Each request is reasonable in isolation. Each feature is buildable in days or even hours with AI assistance.

**Phase 3: The Build Trap.** The team says yes to everything because saying yes is easy when building is cheap. They ship feature after feature, each one adding complexity to the UI, the data model, the onboarding flow, and the support burden.

**Phase 4: Identity Crisis.** The product that was "the best tool for sharing deliverables" is now "a mediocre tool that also does six other things." New users open it and feel overwhelmed. Original users can't find the features they came for. The product has lost its reason to exist.

**Phase 5: Decline.** Churn increases. The team responds by building *more* features to retain users, which makes the problem worse. It's a death spiral powered by the very speed that AI provides.

The fix isn't building slower. The fix is deciding better.

<!-- IMAGE: Diagram showing the five-phase arc from focused product to identity crisis, with "restraint" as the intervention point between Phase 2 and Phase 3. Alt text: "restraint software development lifecycle showing feature pressure leading to identity crisis". Caption: "The build trap follows a predictable arc. Restraint is the intervention between Phase 2 and Phase 3." -->

---

## What Restraint Actually Looks Like in Practice

Restraint isn't saying no to everything. That's paralysis, not strategy. Restraint is having a framework for deciding *which* things to build and — critically — having a process that forces you to make that decision before you touch any code.

Here's the framework I've adopted, and it's reshaped how I approach every project.

### Step 1: The Pre-Planning Conversation

Before I open Claude Code, before I write a single line of a spec, I have what I call a shaping conversation. Sometimes this is with a co-founder or a product lead. Sometimes it's just me and Claude in a separate conversation window — not in code mode, but in thinking mode.

The goal of this conversation is not "how do we build this." The goal is "should we build this, and if so, what exactly are we building?"

I use Claude as a strategic thought partner here. Not by giving it a rigid list of questions — that produces formulaic answers. Instead, I describe the feature idea and ask Claude to pressure test it. Challenge my assumptions. Surface trade-offs I haven't considered. Ask me questions I don't have good answers to yet.

A typical prompt looks something like this:

```
I'm considering adding [feature] to [product]. The product currently does [core function]
for [target customer]. Before I build anything, I want you to act as a critical product
strategist. Ask me clarifying questions about:

- The core problem this feature solves
- Who specifically is asking for it and why
- What they're currently doing instead
- Whether this strengthens or dilutes the product's identity
- What we'd have to say no to in order to do this well

Don't give me a rigid questionnaire. Have a real conversation. Push back when my
reasoning is weak.
```

What comes out of this conversation is clarity. Sometimes the feature makes it through intact. Sometimes it gets scoped down dramatically. Sometimes I realize it shouldn't be built at all — at least not as a native feature.

### Step 2: The Integration-First Alternative

One of the most powerful forms of restraint is choosing *integration over implementation*. Instead of building invoicing into the client portal, offer a Stripe or FreshBooks integration. Instead of building project management, connect to Linear or Asana via API.

This approach has a real advantage beyond product focus: your users get best-in-class tools for each function instead of a mediocre built-in version. And your engineering team maintains a smaller, more focused codebase.

In the agent-skills architecture that's emerging across AI coding tools, this maps perfectly. Rather than building monolithic features, you build focused agent skills that can interface with external services. Each skill does one thing. Each skill is testable, maintainable, and replaceable independently.

I've been building with this pattern in Claude Code, and the [agent skills approach](https://mejba.me/blog/claude-code-agent-skills-guide) makes it especially natural. A skill that connects to Stripe's API is cleaner, more maintainable, and more powerful than a half-baked invoicing module you built yourself.

### Step 3: The Scope Boundary Document

Before any feature gets a spec, it gets a scope boundary. This is a short document — usually less than a page — that explicitly states what's in scope and what's out of scope. Not as a formality, but as a commitment.

Here's what mine look like:

| Section | Content |
|---|---|
| Feature | Daily standup summary for Team Ping |
| In Scope | Auto-generated summaries from async standups, shareable with team leads |
| Out of Scope | Real-time chat, video recording, calendar integration, direct Slack replacement |
| Why Out of Scope | These dilute the async-first identity; existing tools handle them better |
| Integration Points | Slack webhook for delivery, optional export to Notion |

The "Why Out of Scope" column is the important one. It forces you to articulate the *reason* something shouldn't be built, which makes it much harder to silently add it later when someone asks nicely.

---

## Plan Mode Is Industry Standard Now — But It's Not Enough

Something interesting happened across AI coding tools in early 2026. Claude Code, Cursor, and Codex all converged on the same architectural pattern: a dedicated plan mode that separates thinking from building.

In Claude Code, you enter plan mode by pressing Shift+Tab or typing `/plan`. The model switches to read-only — it explores your codebase, asks clarifying questions, and generates an implementation plan without writing a single file. Cursor has a similar mechanism. So does [Codex](https://mejba.me/blog/codeex-app-openai-first-look), with its own variation on the pattern.

This convergence isn't coincidental. The toolmakers all arrived at the same conclusion: developers who plan before building produce better outcomes. Spec-driven development — where the specification is the source of truth, not the code — has become the [industry standard workflow](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/) for serious AI-assisted development.

But here's what most people miss about plan mode: it solves the *how to build* problem. It doesn't solve the *what to build* problem.

Plan mode kicks in after you've already decided to build something. It helps you build it well. It helps you think through architecture, data flows, dependencies, edge cases. That's genuinely valuable — I use it on every significant feature.

What plan mode doesn't do is question whether the feature should exist in the first place. It takes your decision to build as a given and optimizes the execution. That's like having a brilliant navigator who can find the fastest route to any destination but never asks whether you're going to the right city.

The pre-planning conversation I described earlier is the missing layer. It sits *above* plan mode in the workflow hierarchy. First you decide *what* to build (pre-planning). Then you decide *how* to build it (plan mode). Then you build it (implementation).

Skip the first step, and plan mode just helps you build the wrong thing faster.

---

## The Spec-Driven Development Workflow That Actually Works

Here's the complete workflow I've settled on after months of iteration. It combines the strategic restraint conversation with the tactical planning that tools like Claude Code provide.

### Phase 1: Shape (Human + AI as thought partner)

This is the pre-planning conversation. You're not writing code. You're not writing specs. You're thinking — out loud, with AI as a sounding board.

**Inputs:** A raw feature idea, customer feedback, market signal, or competitive pressure.

**Process:**
1. Describe the idea to Claude in a non-coding context
2. Let Claude ask clarifying questions (don't feed it a rigid template)
3. Define the core "job to be done" — what problem does this solve?
4. Identify the target customer and their current workaround
5. Pressure test scope — what's in, what's out, and why
6. Map the user flow *before* touching technical details

**Output:** A lightweight Product Requirements Document (PRD) with problem statement, scope boundaries, user flows, and explicit decisions about what you're *not* building.

The key move here: the PRD should contain at least as much about what you decided *against* as what you decided *for*. That documentation of restraint is what prevents scope creep later.

### Phase 2: Plan (AI coding tool in plan mode)

Now you take the PRD into Claude Code, Cursor, or your tool of choice and enter plan mode.

**Inputs:** The PRD from Phase 1.

**Process:**
1. Feed the PRD to Claude Code in plan mode (`/plan` or Shift+Tab)
2. Let the model analyze your existing codebase against the requirements
3. Generate an architectural plan: data models, API endpoints, component structure
4. Review the plan for scope creep — does it introduce anything not in the PRD?
5. Iterate until the plan matches the scope boundaries exactly

**Output:** A detailed implementation plan with file-by-file changes, dependency order, and estimated complexity.

### Phase 3: Build (AI coding tool in implementation mode)

Only now do you write code. And because you've done the strategic work upfront, the implementation is focused, fast, and disciplined.

**Inputs:** The approved plan from Phase 2.

**Process:**
1. Execute the plan step by step
2. Use parallel task execution for independent workstreams
3. Check each completed component against the scope boundary document
4. Stop when the plan is complete — resist the temptation to add "one more thing"

**Output:** Working code that matches the spec exactly. Nothing more, nothing less.

### Phase 4: Validate (Human judgment)

After the build is done, loop back to the strategic layer.

Does this feature strengthen or dilute the product's identity? Does the user flow feel right? Is there anything you built that, in retrospect, should have been an integration rather than a native feature?

This is where you catch the scope creep that snuck in during implementation. It happens even with good planning. The discipline is catching it before it ships.

If you'd rather have someone build this planning-to-implementation pipeline from scratch, I take on architecture and workflow consulting engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

---

## The Blurring Line Between Builder and Product Manager

Here's something I didn't expect to be writing about a year ago: the role of "developer" and the role of "product manager" are merging.

When building was the bottleneck, you needed dedicated people to make product decisions (PMs) and dedicated people to execute those decisions (engineers). The division made sense because the execution was so time-consuming that mixing in product thinking would slow everything down.

Now that AI handles the bulk of execution, the builder who can't make product decisions is... limited. You're fast at building, sure. But fast at building *what*? If you need someone else to tell you what's worth building, you're operating at half capacity.

The most effective solo developers and small teams I know in 2026 have internalized product management skills. They think about customer segments, competitive positioning, feature trade-offs, and market timing — not because they read a book about it, but because the AI took away the excuse for not thinking about it. When you can build anything in a weekend, the "I'm too busy coding to think about product strategy" defense evaporates.

This is an unfair advantage for builders who develop the discipline. While your competitor is using AI to ship twelve features a month, you're using AI to ship three — but the three you ship are the ones that actually matter. Your product stays focused. Your users stay happy. Your codebase stays maintainable.

That's restraint. And it's the thing that separates products people love from products people tolerate.

---

## What I Got Wrong About Velocity

I need to be honest about something. For the first few months after Claude Code became my primary development tool, I fell into exactly the trap I'm warning about.

The speed was intoxicating. I'd think of a feature at 9 AM and have it deployed by lunch. My weekly output tripled. I was measuring my productivity in features shipped, and that number was going up every week. I felt incredibly productive.

Then I looked at my analytics. Time-on-page for my documentation had dropped. User activation rates hadn't improved despite all the new features. Support tickets had increased — not because things were broken, but because users couldn't find what they needed in an increasingly cluttered interface.

I was shipping more and accomplishing less. The AI was amplifying my output, but my output was unfocused. Speed without direction is just vibration.

The correction was painful. I spent a full week doing nothing but removing features. Deleting code that worked perfectly well. Simplifying flows that had too many steps. Going back to the focused version that users had originally signed up for.

That week of subtraction produced better engagement metrics than the previous month of addition. And it taught me something I should have known from the start: the value of a product isn't measured by what it can do. It's measured by how well it does the thing the user came for.

---

## A Practical Template for the Pre-Planning Conversation

I promised a framework, so here's the actual template I use for the shaping conversation before any feature work. This isn't a rigid questionnaire — it's a starting structure that the conversation evolves from.

**Opening prompt to Claude (or your team):**

```
I'm considering adding [feature] to [product]. Before I plan or build anything,
I want to pressure test this decision. Here's the raw context:

- Product: [what it does today]
- Target customer: [who uses it]
- Feature idea: [what's being considered]
- Source of request: [customer feedback / competitive pressure / internal idea]

Act as a critical product strategist. Your job is to help me decide IF this should
be built, not HOW. Ask me questions in these areas:

1. Core problem: What job is this feature doing for the user?
2. Customer context: Who specifically wants this? Are they our target customer?
3. Current workarounds: What are users doing today? Is the gap painful enough?
4. Competitive reality: Who already does this well? Can we do it better?
5. Scope risk: Does this expand the product's surface area in a way that dilutes identity?
6. Integration alternative: Could this be solved through an integration instead?

Have a real conversation. Push back when my reasoning is thin.
```

**What the output should contain:**

| Section | Purpose |
|---|---|
| Problem Statement | One paragraph on the specific problem being solved |
| Target User | Who benefits, with enough specificity to exclude users who don't |
| Scope Boundaries | What's in, what's out, and the reasoning for each exclusion |
| User Flow | How the user interacts with this — experience first, technical details second |
| Integration Assessment | Whether this should be built natively or connected to an existing tool |
| Decision | Build / Integrate / Don't Build — with clear reasoning |

The decision column is what makes this different from a traditional PRD. Most PRDs assume the feature will be built — the document exists to describe *how*. This document starts from the assumption that the feature might *not* be built, and requires a positive case before moving forward.

---

## Where This Breaks Down (And Where It Doesn't)

I'd be dishonest if I presented this as a flawless framework. Restraint has failure modes too.

**When restraint becomes paralysis.** There's a version of this where you analyze every feature request so thoroughly that nothing gets built. Analysis paralysis is real, and the shaping conversation can feed it if you're not careful. The fix is time-boxing: give the pre-planning conversation a maximum of 2-3 hours for any single feature. If you can't reach a decision in that window, the problem isn't the feature — it's insufficient customer understanding. Go talk to users instead.

**When you need to explore.** Some features need to be built before you know whether they're good. Prototyping has value. The framework I've described works best for production features that will ship to real users. For internal experiments and prototypes, a lighter process makes sense. Build it, test it with a small group, and make the restraint decision based on real usage data rather than pre-planning alone.

**When the market moves fast.** In genuinely competitive markets where speed to feature parity determines survival, excessive restraint can cost you the game. The framework still applies, but the shaping conversation should be shorter and more focused on competitive necessity than ideal product vision.

**Where it works consistently:** products with a defined audience, products past the initial product-market-fit phase, and products where user satisfaction matters more than feature checkboxes. That describes most B2B SaaS, most developer tools, and most consumer products with retention-driven business models.

The failure mode I'm least worried about is someone adopting too much restraint. In my experience, the gravitational pull toward building is so strong that any framework which forces a pause — even a brief one — produces better outcomes than the default.

---

## The Unfair Advantage Nobody's Selling

There's a reason nobody talks about restraint on tech Twitter. It doesn't demo well. "I decided not to build this feature" isn't a tweet that gets engagement. "I shipped a full SaaS in 6 minutes" does. The incentive structure of our industry's public conversation is biased toward speed, output, and capability — not judgment, focus, or discipline.

But talk to the founders who've been shipping for five or ten years. The ones with products that still have passionate user bases. Ask them what their most important product decision was, and almost none of them will point to a feature they built. They'll point to a feature they *didn't* build. A direction they *didn't* take. A moment where they looked at what was possible and chose focus over capability.

AI makes that choice harder than it's ever been. When you can build anything in a weekend, saying "no" feels like waste. It feels like leaving value on the table. Every fiber of your builder instinct screams to ship it.

The builders who resist that instinct — who treat restraint as a skill to develop, not an obstacle to overcome — are the ones building products that last.

Your AI tools will keep getting faster. The models will keep getting smarter. The execution cost of any feature will keep approaching zero. The only thing that won't change is the cost of building the wrong thing: confused users, bloated products, mounting maintenance burden, and a slow erosion of the focus that made your product worth using in the first place.

The next time you open Claude Code or Cursor or Codex with a new feature idea, try something before you type the first prompt. Close the coding tool. Open a blank conversation. And ask yourself the question that AI cannot answer for you:

Should this exist at all?

## Frequently Asked Questions

### What is restraint in software development?

Restraint is the discipline of deciding what *not* to build, even when you have the capability to build it. In 2026, with AI tools making execution nearly instant, restraint means evaluating whether a feature strengthens your product's identity and serves your core users before committing to implementation.

### How does plan mode work in Claude Code?

Claude Code's plan mode activates via Shift+Tab or the `/plan` command. It switches the AI to read-only mode where it explores your codebase, asks clarifying questions, and generates an implementation plan without writing files. For a deeper look at Claude Code workflows, see my [crash course guide](https://mejba.me/blog/mastering-claude-code-crash-course).

### What is spec-driven development?

Spec-driven development treats the specification — not the code — as the source of truth. You write a detailed spec first, and AI tools generate, test, and validate code against it. GitHub released an [open-source spec-kit](https://github.com/github/spec-kit) to support this workflow, and major tools like Claude Code, Cursor, and Codex have all adopted plan-first patterns.

### How do you prevent feature bloat when AI makes building fast?

Use a pre-planning conversation before any spec work. Define scope boundaries explicitly — what's in, what's out, and *why*. Choose integration over implementation for non-core features. And measure product success by user outcomes, not feature count.

### Why can't AI replace product strategy decisions?

AI excels at execution — generating code, optimizing architecture, handling implementation details. But strategic product decisions require understanding customer context, competitive positioning, and second-order consequences that current models cannot reliably assess. The *whether to build* question remains a human responsibility.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
