**BRAND:** mejba.me
**TITLE:** I Built a Second Brain That Writes My PRs For Me
**SLUG:** claude-code-second-brain
**TAGS:** AI Development, Developer Productivity, Claude Code, Workflow Automation, Guide

---

Last Thursday I shipped a feature that touched 14 files across three services. The PR description was two paragraphs of clean, specific context. The status update to my team was already drafted in Slack before I finished my coffee. The handoff notes for the next engineer were sitting in a markdown file, perfectly structured, waiting to be shared.

I didn't write any of it.

Not a single word. And before you assume I'm using some fancy template system or copying from ChatGPT — no. My Claude Code setup wrote all of it because it already knew what I'd been building, why I'd made certain architectural decisions, what trade-offs I'd considered, and what the next person picking this up would need to understand. It knew all of this because I'd spent the last six months teaching it — not through elaborate prompting, but through 30-second updates at the end of each work session.

I call it my second brain. That sounds dramatic until you watch it generate a PR description that's better than anything I'd write manually, referencing decisions I made three weeks ago that I'd already forgotten about. The gap between "AI that helps with code" and "AI that handles the cognitive overhead of being an engineer" is enormous — and most developers are stuck on the wrong side of it.

Here's the thing nobody talks about when they rave about AI coding tools: writing code is maybe 40% of what engineers actually do. The other 60% is communication. Status updates. PR descriptions. Documentation. Handoff notes. Incident reports. Architecture decision records. All of that writing requires context — deep, specific, up-to-date context about what you're building and why. And that's exactly where every AI tool I tried before Claude Code completely fell apart.

Let me show you how I fixed this. But first, I need to explain why the obvious solutions don't work — because I wasted months on them before finding what does.

## Why Throwing AI at Writing Tasks Doesn't Work (At First)

I was an early adopter of using AI for engineering communication. The moment ChatGPT could hold a conversation about code, I started pasting diffs and asking for PR descriptions. The results were... fine. Generic, mostly accurate, but missing every piece of context that makes a PR description actually useful.

The problem is structural, not a quality issue with the AI. When you paste a diff into any chatbot, it can see *what* changed. It cannot see *why* it changed. It doesn't know that you refactored the auth middleware because the security audit last month flagged a session fixation vulnerability. It doesn't know that you chose Redis over Memcached because the team already runs Redis for the job queue. It doesn't know that this PR is part three of a five-part migration that started two sprints ago.

So what do you do? You explain the context. Every single time. You type out three paragraphs of background, paste the diff, and then the AI gives you a reasonable PR description. Congratulations — you just spent five minutes providing context to save yourself three minutes of writing. Net savings: negative two minutes. Plus the cognitive load of context-switching from code to "explain my entire project to a chatbot."

I tried building prompt templates. "Given this project uses Laravel 11 with Redis caching and Postgres..." — pre-filled context blocks I'd paste before every request. Helped a little. But the context went stale within days. Projects evolve fast. What was true Monday isn't true Thursday. Maintaining those templates became its own chore.

Then I tried AI tools with built-in memory. ChatGPT's memory feature, specifically. It remembered that I prefer TypeScript and work on web applications. Incredibly shallow. It's like hiring an assistant who remembers your name but forgets every project conversation you've ever had.

The fundamental issue with all of these approaches: **the context lives in your head, not in the system.** Every time you want AI to do something useful, you have to extract relevant context from your brain, format it for the AI, and verify the output. That extraction step is the bottleneck, and no amount of prompt engineering eliminates it.

What eliminates it is giving the AI its own persistent, local, continuously-updated knowledge base. A second brain that it maintains alongside you as you work. That's what I accidentally built with Claude Code — and it changed my entire relationship with engineering communication.

## How Claude Code's Local Context System Actually Works

Six months ago, I switched to Claude Code primarily as a coding agent. It's genuinely excellent at writing and refactoring code — but that's not what hooked me. What hooked me was discovering that it maintains local, persistent context through markdown files that live right in your project directory.

The mechanism is simple. Claude Code reads and writes markdown files — typically `CLAUDE.md` at your project root and additional context files you create. These files persist between sessions. They don't disappear when you close your terminal. They don't get lost in a cloud somewhere. They sit in your repo, version-controlled alongside your code, growing richer with every work session.

Here's what my project context file looks like after six months of accumulated knowledge:

```markdown
# Project Context

## Architecture
- Laravel 11 monolith with Vue 3 SPA frontend
- PostgreSQL primary DB, Redis for caching and queues
- Deployed on AWS ECS with Terraform-managed infrastructure
- Auth: Custom JWT implementation (migrated from Sanctum in Sprint 14)

## Current Sprint (Sprint 22)
- Payment processing refactor: Stripe → multi-provider abstraction
- Migrating webhook handlers from sync to async queue processing
- Performance: targeting p99 < 200ms on checkout flow

## Recent Decisions
- Chose adapter pattern over strategy for payment providers (2024-01-15)
  - Reason: Need runtime provider switching per merchant config
- Rejected event sourcing for payment state (too complex for current scale)
- Redis cluster mode enabled for horizontal scaling prep

## Known Tech Debt
- Legacy order model still uses soft deletes (migration planned Sprint 24)
- Test coverage on payment module: 67% (target: 85% before launch)
```

That file didn't appear magically. I built it up over time, 30 seconds at a time. At the end of each work session, I tell Claude Code: "Update the project context with what we worked on today." It reviews the session — the files we changed, the decisions we discussed, the problems we solved — and appends relevant updates to the context file.

Thirty seconds. That's the investment. And the return compounds massively.

Because the next time I sit down and ask Claude Code to write a PR description, it doesn't need me to explain anything. It reads the context file, sees the current sprint goals, understands the architectural decisions, knows the recent changes, and generates a description that reads like it was written by someone who's been on the project for months.

The difference is night and day. Compare these two PR descriptions — one from a cold AI interaction, one from my second brain:

**Cold AI (pasted diff, no context):**
> "This PR updates the payment processing module. Changes include modifications to the PaymentService class, addition of new provider interfaces, and updates to related tests."

**Second brain (with accumulated context):**
> "Implements the adapter layer for multi-provider payment abstraction (Sprint 22). Introduces PaymentProviderAdapter interface with Stripe as the first concrete implementation. Webhook handlers remain synchronous in this PR — async migration follows in the next PR per our queue-first approach. Merchant-level provider config reads from the existing settings table with a new payment_provider column (migration included)."

Same diff. Same AI model. The only difference is context — persistent, accumulated, local context that I didn't have to re-explain.

This changes everything about how I interact with AI for engineering tasks. And it's about to get even more powerful when you add skills into the mix.

## Building Your Second Brain — The 30-Second Ritual That Changes Everything

Alright, let me walk you through exactly how I set this up, because the implementation is embarrassingly simple. The value isn't in the complexity — it's in the consistency.

**Step 1: Create your initial context file.**

At the start of any project (or right now, for existing projects), spend 10 minutes having Claude Code build your baseline context. Open your terminal in the project root and say:

"Look at the codebase structure, the recent git history, and any existing documentation. Create a CLAUDE.md file summarizing the project architecture, tech stack, current focus areas, and any patterns you can identify."

Claude Code will scan your project, read key files, check git logs, and generate a comprehensive context document. Review it, correct anything it got wrong, and you've got your foundation.

**Step 2: The end-of-session update (30 seconds).**

This is the habit that makes the system work. At the end of every work session — before you close your terminal — say:

"Update the project context with what we worked on today."

That's it. Claude Code reviews the session, identifies what's worth remembering, and updates the context file. New decisions get recorded. Completed tasks get marked. Tech debt gets noted. The context file grows, but it stays focused because Claude Code is good at distinguishing between "important project knowledge" and "temporary debugging details."

**Step 3: Periodic condensation.**

Every few weeks, the context file gets long. When that happens, I ask Claude Code to condense it: "The context file is getting large. Consolidate it — keep everything that's still relevant, archive or remove anything that's outdated."

This keeps the file tight and fast to parse. My current context file is about 400 lines for a project I've been working on for six months. That's lean. And every line is relevant.

**Pro tip:** I keep a separate `decisions.md` file for architectural decisions that I want preserved long-term, even if they're no longer actively relevant to the current sprint. This is gold for onboarding new team members or remembering why you chose a specific approach six months later.

The ritual sounds trivial. Thirty seconds at the end of a session. But think about what happens after a month. You've got 20+ updates accumulated. The AI knows your coding patterns, your architectural preferences, your project's evolution, the trade-offs you've considered and rejected. It doesn't just know *what* the code does — it knows *why* you built it that way.

That's not a chatbot. That's an institutional memory that never forgets, never goes on vacation, and never needs a refresher meeting.

Now here's where things get genuinely exciting — because once you have rich context, you can build workflows that would be impossible without it.

## Skills: Teaching Your AI to Repeat Your Best Work

A skill in Claude Code is essentially a saved, repeatable workflow. Think of it as a macro, but intelligent — it adapts to the current context instead of blindly replaying steps.

The concept is beautifully simple: you do a task once with Claude Code, and at the end you say, "Turn this into a skill." Claude Code examines what you did — the sequence of steps, the decisions you made, the format of the output — and saves it as a reusable workflow. Next time you need to do the same type of task, you trigger the skill and it handles everything.

**Creating your first skill — automated PR descriptions:**

This was my first skill, and it's still the one I use most. Here's how I built it:

1. I manually walked Claude Code through writing a PR description for a real change I'd made.
2. During the process, I corrected its output: "No, don't just list the files changed — explain the *why* behind each change group."
3. I adjusted the format: "Use this structure: Summary, Motivation, Changes, Testing Notes, Follow-up Items."
4. When the output matched what I'd want to submit, I said: "Save this as a skill called 'pr-description'. Every time I ask for a PR description, follow this exact approach."

Now, after committing code, I trigger the skill. It reads the diff, cross-references it with the project context, and generates a PR description in my preferred format. The output is consistently better than what I'd write manually — because it never forgets to include testing notes or follow-up items, and it always has the full project context available.

**The skill that saved me during on-call: SEV investigation.**

This one surprised me with how powerful it became. I created a skill that, given a release branch or deployment timestamp, does the following:

1. Pulls the git log for that timeframe
2. Identifies all commits included in the release
3. Cross-references them with the project context to understand what each change was *supposed* to do
4. Generates a ranked list of "theories" — potential root causes for whatever issue triggered the alert

During a production incident at 2 AM last month, I triggered this skill instead of manually scrolling through commit logs with bleary eyes. In about 40 seconds, it gave me three theories ranked by likelihood. The second theory was correct — a race condition in the webhook handler we'd refactored the previous week. Without the skill, that investigation would have taken me 20-30 minutes of manual log review and mental context reconstruction. At 2 AM. Half asleep.

**PR splitting — the skill I didn't know I needed:**

Large PRs are review killers. Every experienced engineer knows that a 500-line PR gets better review attention when split into three focused PRs of 150-200 lines each. But actually splitting a large diff into logical, independently-reviewable chunks? That's tedious work.

I built a skill that analyzes a large diff and suggests how to split it. It considers:
- Logical groupings (all database migration changes together, all API endpoint changes together)
- Dependency ordering (which changes need to land first)
- My personal preference for PR sizing (I like 200-300 lines max)

The skill doesn't just suggest the split — it generates the git commands to create each PR with the correct commits. I review the proposed split, adjust if needed, and execute. What used to take 30 minutes of careful manual work now takes about 3 minutes.

**Status update automation — the one my manager loves:**

End of day, I trigger a skill that reviews my git activity, any issues I've updated, and the project context, then generates a status update in the format my team uses. Three bullet points: what I did today, any blockers, what I'm planning tomorrow. My manager specifically commented that my updates have been "really clear lately." They have no idea an AI is writing them.

Here's the critical insight about skills that most people miss: **skills get better as your context gets richer.** A PR description skill running with one week of context produces decent output. The same skill with six months of context produces output that genuinely understands the project's history and trajectory. The second brain and the skills system feed each other in a virtuous cycle.

## The Part About Prompt Engineering That's Mostly Wrong

I need to push back on something the AI productivity space gets dangerously wrong. Everyone's obsessed with prompt engineering. "Use this magic prompt template." "Here's the perfect system prompt for code review." "Add these 47 instructions for better output."

Look, prompt quality matters. I'm not saying it doesn't. But the obsession with prompts distracts from the actual bottleneck: **context quality.**

A mediocre prompt with excellent context produces great output. A perfect prompt with no context produces generic garbage. I've tested this extensively. My PR description skill uses a remarkably simple prompt — basically "write a PR description for this diff using the project context." No elaborate instructions, no role-playing directives, no multi-shot examples. The output is excellent because the context is excellent.

Think about it through the lens of onboarding a junior engineer. If you hired someone new and they asked, "Can you explain the project architecture, the current sprint goals, the recent decisions, and why we chose these specific technologies?" — you'd spend an hour bringing them up to speed. But after that one-time investment, they'd be able to write competent PR descriptions, status updates, and documentation on their own. Not because you taught them *how to write* — because you gave them the *context to write well*.

Your AI second brain works the same way. The one-time investment is building the context. After that, it's just 30-second maintenance. The prompts are almost irrelevant because the hard part — having deep, current, specific project knowledge — is already handled.

This reframing changed how I approach every AI workflow. Instead of spending time crafting elaborate prompts, I spend time managing context. Instead of searching for the perfect instructions, I make sure the knowledge base is fresh. The returns are incomparably better.

I'll be honest about one thing, though — there's a catch to this entire approach that I glossed over until now.

## What I Got Wrong and What Still Frustrates Me

**The context file can lie to you.** If you update your context after a session where you explored an approach but ultimately abandoned it, you might accidentally record that abandoned approach as a decision. I've had Claude Code generate PR descriptions referencing architectural choices that I'd *considered* but not *implemented*. The fix is reviewing context updates before accepting them, which sometimes I skip when I'm in a rush. Discipline matters here.

**Skills are not set-and-forget.** My PR description skill has been modified seven times since I created it. Each time, I noticed a pattern in the output I didn't like — too verbose, missing test coverage notes, wrong tone for internal vs. open-source PRs. Building the initial skill takes one session. Refining it to match your standards takes ongoing iteration. Not a lot of effort, but more than zero.

**This only works if you actually use it consistently.** I went on a two-week vacation and came back to a stale context file. The first PR description it generated after my return was based on outdated sprint goals and completed work items. It took me about 10 minutes to update the context and get everything current again. Not terrible, but it reminded me that the system requires regular feeding.

**Team adoption is harder than personal adoption.** I've tried getting my team to maintain shared context files. The results are mixed. People who already take notes adapt quickly. People who don't take notes see it as extra overhead, even when it's only 30 seconds. I'm still working on this — maybe it needs to be more automated, maybe it needs to be framed differently. Open question.

**The hardest problem I haven't solved yet:** context across multiple projects. My second brain works beautifully within a single project. But I work on three projects simultaneously, and the context is siloed. When I switch between projects, each one has great local context — but cross-project knowledge (like shared libraries or infrastructure patterns) doesn't transfer. I have ideas for solving this with a global context file, but haven't built it yet.

These are real limitations. I mention them because every tool breakdown I read online makes everything sound perfect, and that's not helpful. This system has rough edges. The core value proposition — persistent context that eliminates repeated explanations and enables powerful automation — is genuine and significant. But it requires care and feeding to stay valuable.

## What Actually Changed in My Engineering Workflow

Numbers help. Here's what I tracked over four months of consistent use:

**PR descriptions:** Average writing time dropped from 8 minutes to under 1 minute. Quality — measured by the number of reviewer clarification questions — improved by roughly 60%. Reviewers ask fewer questions because the AI-generated descriptions include the *why* behind changes, not just the *what*.

**Status updates:** From 5 minutes daily to about 30 seconds (trigger the skill, scan the output, send). Over a month, that's roughly 2 hours reclaimed. Not life-changing on its own, but it eliminates a task I actively dreaded.

**Incident investigation:** My SEV skill has been triggered eight times. Average time-to-first-theory dropped from 25 minutes of manual investigation to about 90 seconds. Three of those eight times, the first theory was correct. The other five times, the generated theories at least narrowed the search space significantly.

**Documentation:** I now document architectural decisions that I previously wouldn't have bothered writing up. The second brain captures them automatically during work sessions, and a quick "generate an ADR for the caching decision we made today" produces a draft in 15 seconds. My project's documentation went from sparse to comprehensive without me actually *writing* documentation.

**Total estimated time savings:** 4-6 hours per week. Not from spectacular individual moments, but from hundreds of small friction points eliminated. Death by a thousand cuts, reversed.

The savings aren't just about time, though. There's a cognitive load reduction that's harder to quantify but equally important. I no longer carry the mental burden of "I need to remember to document why we chose Redis" or "I should write up the incident findings before I forget the details." The system captures everything as it happens. My brain is freed up for the work that actually requires human judgment — architecture decisions, code review, problem-solving.

That feels like the real unlock. Not just faster writing, but less mental overhead about the writing I should be doing but am not.

## The Shift That's Coming and Most Engineers Will Miss

Here's my prediction, and I'm willing to be wrong about the timeline but not the direction: within two years, engineers who maintain AI-augmented context systems will have a measurable productivity advantage over those who don't. Not because they're better engineers, but because they've eliminated an entire category of cognitive overhead.

The engineers who dismiss this as "just AI hype" are the same ones who dismissed version control, CI/CD, and containerization. Each of those technologies eliminated a category of work that engineers used to do manually. Context management is next.

The interesting part isn't the automation itself — it's what it frees you up to do. When you're not spending mental energy on PR descriptions, status updates, and documentation, that energy goes somewhere. In my case, it went into deeper code review, more thoughtful architecture discussions, and — ironically — better writing when writing actually mattered (like this post).

My challenge to you is specific: pick one project you're actively working on. Spend 10 minutes today creating a context file. Then commit to the 30-second end-of-session update for two weeks. Don't build skills yet. Don't optimize anything. Just accumulate context.

After two weeks, ask Claude Code to write a PR description for your next change. Compare it to what you would have written manually.

That comparison is going to tell you everything you need to know about whether this approach is worth investing in. And I'm betting — based on what happened for me and every engineer I've shown this to — that you won't go back.

The question isn't whether AI will handle engineering communication. It's whether you'll build the context system that makes it actually good, or whether you'll keep pasting diffs into chatbots and wondering why the output feels hollow.

Your second brain is waiting to be built. It only needs 30 seconds a day.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
