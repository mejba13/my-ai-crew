**BRAND:** mejba.me
**TITLE:** Super Skills in Claude Code: My Real Build Log
**META TITLE:** Super Skills Claude Code: Build Log and Honest Verdict
**SLUG:** super-skills-claude-code-karpathy-memory
**PRIMARY KEYWORD:** super skills Claude Code
**META DESCRIPTION:** I rebuilt my Claude Code skills with memory, Karpathy principles, and a refinement loop. Here is the exact setup, what broke, and what actually compounded.
**TAGS:** Claude Code, Claude Skills, AI Memory, Pinecone, AI Agents

---

I had 47 skills installed in `~/.claude/skills/` last Friday. I deleted 39 of them on Sunday morning.

Not because they were broken. They worked exactly the way the documentation said they would. A skill named `meeting-summary` produced a meeting summary. A skill named `seo-audit` produced an SEO audit. A skill named `linkedin-post` produced a LinkedIn post. They behaved. They returned the right shape of output. They never threw errors.

They also never got better.

Every Monday I would write the same correction notes in the same Claude Code session. "Don't use the word leverage. Stop opening every paragraph with a question. Cut the em-dashes by half." Tuesday morning, the skill would do all three things again, exactly the way it did the week before. The skill had no idea Monday had happened.

That is the gap Jack Roberts was pointing at in the video summary that landed in my inbox last week. He calls it the difference between a *utility skill* and a *super skill*, and he frames the whole thing through Andrej Karpathy's mental models for working with coding agents. I watched it twice, then I spent four days rebuilding my entire skills directory around the idea. This is the build log.

If you have read my [Karpathy CLAUDE.md install notes](https://www.mejba.me/karpathy-claude-md-skills-install-guide), you already know I take Karpathy's coding-agent principles seriously. This post is the next layer up — what happens when you stop applying those principles to your code and start applying them to the skills themselves.

## Why 99% of Claude Skills Are Static, Forgetful, and Quietly Worthless

Open `~/.claude/skills/` on any Claude Code user's machine right now and you will find roughly the same picture. A folder per skill. A `SKILL.md` file inside each one. YAML frontmatter at the top with a name and a description. A few hundred words of instructions underneath. Maybe a `templates/` directory with some boilerplate.

That is the official format. The [Claude Code skills documentation](https://code.claude.com/docs/en/skills) describes it exactly that way: a `SKILL.md` with frontmatter that tells Claude when to load the skill, plus markdown content that Claude reads when the skill is invoked. The Skill tool surfaces it. The harness loads the file. The model reads the instructions. The skill runs.

It works. It is also where 99% of Claude Code users stop building.

Here is what a static skill cannot do. It cannot remember what you told it last session. It cannot pull current data from a Gmail thread or a Figma file or last week's web research. It cannot grade its own output and rewrite itself when the output keeps coming back wrong. It is a frozen prompt with a clever name. The model running underneath gets smarter every six months. The skill stays exactly as dumb as the day you wrote it.

Roberts' framing was the thing that finally made it click for me. A static skill is a *utility* — it solves one task, the same way, every time, like a Bitly URL shortener that takes a long URL and spits out a short one. Useful. Predictable. Forgettable. A *super skill* is something different. It has memory it can recall. It pulls in the right tools and the right data sources for the specific task in front of it. And it grades its own output and refines itself based on your feedback.

Three properties. Memory. Tooling. Self-improvement. Without those, you are typing prompts with extra steps.

Most of my 47 skills had zero of the three.

## The Karpathy Frame That Changed How I Wrote Every SKILL.md

Before I touched a single skill file, I had to fix the writing itself. Because the second mistake I had been making — the one underneath the memory and tooling problem — was that my skill instructions read like over-eager interns trying to impress a manager. Long. Speculative. Stuffed with "consider also" branches and "you may want to" suggestions. Every skill was 800 words of hedged guidance.

Karpathy's public observations on LLM coding agents have been kicking around X for over a year, and Forrest Chang's `andrej-karpathy-skills` repo distilled them into four principles that 60,000 developers have now bookmarked, [according to the Robonuggets writeup](https://www.robonuggets.blog/p/the-karpathy-claudemd-file-that-43000). The four principles:

1. **Think before coding.** Clarify assumptions. State the goal. Name the constraints. Do not start typing until you can articulate what success looks like.
2. **Simplicity first.** Minimum code that solves the problem. Nothing speculative. No features beyond what was asked.
3. **Surgical edits.** Touch only what you must. Do not "improve" adjacent code, comments, or formatting. Every changed line traces to the request.
4. **Goal-driven execution.** Define the success criteria up front. Loop until verified. Stop when done.

I had been treating these as rules for the *code* Claude writes. Roberts' framing flipped that. They are also rules for the *skill* itself.

Every `SKILL.md` I rewrote got shorter. The "meeting-summary" skill went from 612 words to 184. The "seo-audit" skill went from 891 words to 240. I cut speculative branches. I cut "in case you also want to". I named the success criteria explicitly at the top of each file. The skill files themselves now follow surgical-edit discipline — they say what they need to say and nothing more.

The output got measurably better the same week. Not because the model got smarter. Because the model finally had less ambiguous instruction to parse.

That was the foundation. Now we could build the three super-skill properties on top of it.

## Building Property One: Memory That Actually Recalls

A skill with no memory is a goldfish in a fishbowl. Every Monday it meets you for the first time. Every Tuesday it asks you the same context questions. Every Wednesday it makes the same mistakes you corrected on Monday.

Roberts' Memory OS is the architecture I rebuilt around, and it is the single change that compounded the most. Three buckets. Each bucket has a clear job, a clear lifecycle, and a clear rule for what goes in.

### Bucket One: Session Memory

This is the conversation log itself, archived at the end of every working session. The mechanism is a simple skill — Roberts calls it a "wrap-up" skill — that runs at the close of every Claude Code session and does three things. It writes the conversation summary to a dated markdown file. It pulls out the corrections you gave the model and saves them as labeled examples. It updates a running session count.

I wired mine to fire on `/wrap` at the end of every working block. The output goes to `~/Documents/claude-memory/sessions/2026-04-28-skills-rebuild.md`. The whole thing takes about 40 seconds. The next morning, when I start a new session, my Profile skill (more on that below) reads the latest session file before doing anything else. The model walks in already knowing what we did yesterday.

This is the bucket I had completely missed for a year. Every other memory experiment I had run — Obsidian vaults, Notion databases, the [Pinecone unlimited memory system](https://www.mejba.me/unlimited-ai-memory-pinecone-claude) I built three weeks ago — all of them solved long-term retrieval. None of them solved "remember what we just talked about". Session memory closes that gap with one wrap-up skill and a folder.

### Bucket Two: Knowledge Base

This is the immutable bucket. Long-form, durable, doesn't change. My knowledge base holds: my own writing (every blog post on this site, every newsletter), transcripts of every video and podcast I have published, the books I have annotated, and a few hundred technical references I keep coming back to. It does not hold scratch notes. It does not hold half-thoughts. If it goes in the knowledge base, it stays there.

I tested two storage backends head to head. Obsidian via the [Obsidian-Claude Code persistent memory setup](https://www.mejba.me/obsidian-claude-code-persistent-memory) and Pinecone serverless. The verdict was the one most people don't want to hear: Obsidian is great when your knowledge base fits on a screen. Once you cross a couple hundred long markdown files, the token cost of letting Claude scan vault contents on every relevant query gets ugly fast. I ran a week of dual logging and the Obsidian path was costing me roughly 4x the tokens of the Pinecone path on the same questions, because Obsidian retrieval ends up loading whole files where Pinecone returns ranked chunks.

Pinecone wins for scale. Pinecone's [public pricing page](https://www.pinecone.io/pricing/) lists the Starter tier as free with 5 indexes, 2GB storage, 2M write units and 1M read units per month, and the Standard tier at $50/month with multi-cloud and production features. I am running on Standard. For a knowledge base under 500 documents you can stay on Starter. The serverless model means there is no idle compute charge, which matters because most of my retrieval traffic is bursty — a flurry of queries on Tuesday morning, near zero on Saturday afternoon.

If you have under 200 long files and you don't mind the higher token spend, stay on Obsidian. Past 200, Pinecone serverless saves real money.

### Bucket Three: Profile and Strategy

This is the mutable bucket — and it is the one I underestimated. One markdown file. Lives at `~/.claude/profile.md`. Updated at the end of every session. Contains: my current focus (the project I am working on this week), the active goals (what I am trying to ship), the constraints (what is blocking me), and the recent decisions (what we settled on yesterday so we don't relitigate it today).

This file is the thing my super skills read first. Before any task, the skill checks the profile, knows what I am working on, and skips the "what are you trying to do" questions. The transformation in pacing is enormous. A LinkedIn-post skill that used to ask three setup questions now produces a draft on the first turn because it already knows I am writing about Claude Code skills this week.

The dashboard view of all this — Roberts shows it in his video — is what makes the Memory OS feel like an operating system instead of a pile of folders. Session counts. Customer insights aggregated from session memory. Subscriber-growth notes. Heatmaps of which topics I work on most. I built a stripped-down version using a `dashboard` skill that just reads the three buckets and renders a markdown summary. Not pretty, but enough to see the shape of my own work week.

## Building Property Two: The Right Tools and Data Sources Per Task

Memory is half the equation. The other half is the skill knowing what to pull in for the specific task in front of it.

A static skill operates on whatever you happen to paste into the prompt. A super skill knows: this task needs Gmail, this task needs the Figma file, this task needs the latest pricing page from a competitor's site. It pulls those things itself.

The Claude desktop app's built-in connectors handle most of the easy cases. Gmail, Google Calendar, Google Drive, Notion, Slack, Figma — all available as MCP-style tools the skill can declare it needs. When the connector exists, you wire the skill to it directly and the data shows up at runtime.

Where it gets interesting is the cases the connectors don't cover. Web research is the big one. If your skill needs the current pricing page for a tool, or last month's release notes from a vendor, or the top 5 competitor blog posts on a topic, you are scraping. And scraping with raw fetch eats tokens — every page comes back as 30KB of HTML, navigation, footers, and inline scripts. The model has to wade through 80% noise to find 20% signal.

[Firecrawl](https://www.firecrawl.dev/pricing) is the AI-optimized scraper I switched to. It returns clean markdown. It strips navigation and ads. The scrape that used to be a 30KB HTML blob comes back as 4KB of actual content, which means roughly 7x fewer tokens per page on web-research tasks. Their public pricing as of April 2026 lists Free at 500 one-time credits, Hobby at $16/month for 3,000 credits, Standard at $83/month for 100,000 credits, and Growth at $333/month for 500,000 credits. Standard scraping is 1 credit per page. Search is 1 credit per result. JSON extraction adds 4 credits per page on top.

I am on Hobby. 3,000 credits a month is enough for a personal research workflow with a few daily web-pull skills. If you are running an agency content team, Standard is the realistic floor.

For the connectors that don't exist directly, Zapier is the universal bridge. Anything you can wire to a Zap, you can wire to a Claude skill via webhook. I have a "post to X with image" skill that uses Zapier as the bridge because there is no native X connector worth using yet. Two-step setup. Works.

The skill creator built into Claude is the fastest way to spin a new one up — it walks you through declaring intention, expected outcome, required tools, data sources, and output format. I covered the testing side of this in [my testing-skills walkthrough](https://www.mejba.me/claude-skill-creator-testing-optimization), and the same testing discipline matters even more for super skills, because once you give a skill memory and tools, the surface area for surprise behavior triples.

## Building Property Three: The Refinement Loop That Compounds

This is the property that turns a skill into something that actually gets better over time. And it is the one most people skip because it feels like overengineering until you have lived without it.

The refinement loop is simple in concept. After the skill produces output, it grades itself against the success criteria you defined in the `SKILL.md`. If the grade is below threshold, it asks you for the specific correction. The correction gets logged. Periodically — weekly is what I run — the skill reads its own correction log and rewrites the relevant sections of its `SKILL.md` to encode the lesson.

In practice my version is a `refine` skill that I trigger explicitly on Friday afternoons. It reads the corrections from the past week's session memory, groups them by which skill they applied to, and proposes a diff for each affected `SKILL.md`. I review the diffs. I accept the ones that match what I actually want. The skill rewrites itself. Next Monday, the skill walks in remembering the correction.

This is the part of the system where compounding shows up. After three weeks of refinement, my LinkedIn-post skill stopped opening every post with a question. After two weeks, my SEO-audit skill stopped padding the report with summary sections I never read. After one week, my meeting-summary skill stopped putting action items at the top because I told it I read them last and want them last. None of those changes required me to touch the `SKILL.md` directly. I gave the correction once in conversation. The wrap-up skill captured it. The refine skill encoded it. The skill behaved differently the next session.

That is what Roberts means when he says super skills compound. The static skill is the same on day 1 and day 90. The super skill on day 90 is meaningfully better than the same skill on day 1 — not because the model got smarter, but because the skill ate three months of your corrections.

If you want to see this pattern in action without the memory layer, my older [self-improving Claude Code systems writeup](https://www.mejba.me/self-improving-claude-code-systems) walks through a smaller version of the same loop. The Memory OS turns it from a single-skill trick into a system property.

## What I Got Wrong On The First Build

Three days in, I had to tear most of it down and restart. Worth flagging the mistakes in case you walk into the same walls.

**Mistake one: I made the wrap-up skill too smart.** The first version tried to summarize, extract corrections, update the profile, and refresh the dashboard in a single pass. It took 4 minutes per session-end and the summaries kept missing things because the model was juggling four jobs. I broke it into three smaller skills — `wrap` (just the conversation archive), `extract-corrections` (just the correction pull), `update-profile` (just the profile diff). Each one runs in 30-50 seconds. None of them drop information.

**Mistake two: I dumped everything into the knowledge base.** First two days I was indexing chat scratchpad notes, half-finished drafts, and random Slack snippets. The Pinecone retrieval got noisy because the embeddings were polluted with low-quality material. I scrubbed it on day three and now there is a hard rule: nothing enters the knowledge base unless it is something I would link a stranger to. The retrieval quality jumped immediately.

**Mistake three: I tried to skip the refinement loop because "I'll just edit the SKILL.md directly when I notice issues".** I told myself this and then proceeded to never edit a SKILL.md directly for a full week, because every time I noticed an issue I was mid-task and not in the mood to context-switch into editing prompt files. The refine skill works because it batches the editing into a single Friday session. Without that batching mechanism, the skills don't get better. They just collect grievances.

**Mistake four: I underestimated profile drift.** The profile file goes stale fast if you don't update it. After a week without an update the model started giving me advice for last week's project instead of this week's. I now have an automation that nudges me with a notification if the profile hasn't been touched in 48 hours. Without the nudge, the whole system slowly degrades back into static-skill territory.

If you would rather have someone build this kind of system from scratch for your team rather than DIY it, I take on Claude Code engagements through [my Fiverr](https://www.fiverr.com/s/EgxYmWD). Skills, Memory OS, Pinecone wiring, the refinement loop — the same stack I run on my own work, configured for yours.

## What Roberts' Course Adds That Self-Teaching Misses

Quick context on where this all came from. Roberts runs a Claude Code course that walks from foundations through monetization, and the super-skills section is roughly the middle of it. I have not taken the full course. I picked up the super-skills frame from his public video and rebuilt it from there. The thing I would say honestly: the frame is the valuable part. Once you have the three properties named (memory, tooling, self-improvement) and the Memory OS architecture (session, knowledge base, profile), the rest is a matter of doing the work.

If you are the kind of builder who likes a guided sequence with feedback loops baked in, a paid course is going to save you the four days I spent thrashing on the wrap-up skill design. If you would rather read a build log, learn the frame, and grind through the implementation yourself, this post is roughly that.

Either path gets you there. The point is to stop running 47 utility skills that all forget you exist between Mondays.

## The One Move That Changed Everything

If you take exactly one thing from this post, take this:

Build the wrap-up skill first. Before the Pinecone integration. Before the Firecrawl wiring. Before the dashboard. Before you rewrite a single existing skill.

The wrap-up skill is the cheapest thing on this list and it is also the foundation everything else rests on. Without it, your sessions don't feed into anything. Your corrections evaporate. Your profile goes stale. The refinement loop has nothing to refine *from*. The Memory OS has session memory because the wrap-up skill writes it.

It is 50 lines of `SKILL.md`. It runs on `/wrap`. It writes one markdown file per session into a folder. That is the entire MVP. Build that this week. Run it for two weeks. Then layer in the profile file. Then layer in the knowledge base. Then layer in the refinement loop. Each layer becomes possible because the previous layer is feeding it.

The 47-skills-that-forget-you-exist problem is not solved by writing skill #48. It is solved by giving the skills you already have a way to remember Monday on Tuesday.

That is the difference between a utility and a super skill. And it starts with one wrap-up file you write today.

## Frequently Asked Questions

### Where do Claude Code skills live on my machine?
Claude Code skills live in two locations: `~/.claude/skills/` for personal skills available across every project, and `.claude/skills/` inside a repo for project-scoped skills committed to git. Each skill is a folder containing a `SKILL.md` file with YAML frontmatter and markdown instructions. The Claude Code harness loads them automatically when relevant.

### What makes a super skill different from a regular Claude skill?
A super skill has three properties a regular skill lacks: indexed memory it can recall across sessions, the ability to pull in the right tools and data sources per task, and a self-improvement loop that grades its own output and refines its own instructions over time. Regular skills are static markdown that produce the same output on day 1 and day 90.

### Do I need Pinecone or can I use Obsidian for the knowledge base?
Obsidian works fine for knowledge bases under roughly 200 long markdown files. Past that, Pinecone serverless saves significant token cost because it returns ranked chunks instead of loading whole files. Pinecone Starter is free for small workloads; Standard is $50/month for production use. For details on the Pinecone setup, see my [unlimited AI memory writeup](https://www.mejba.me/unlimited-ai-memory-pinecone-claude).

### How much does Firecrawl cost for a personal Claude Code workflow?
Firecrawl Hobby is $16/month for 3,000 credits and is enough for a personal workflow with a few daily web-research skills. Standard is $83/month for 100,000 credits. Standard scraping is 1 credit per page, search is 1 credit per result, JSON extraction adds 4 credits per page.

### What is the single most important super-skill component to build first?
The wrap-up skill. It runs at the end of every session, archives the conversation, extracts your corrections, and updates the profile file. Without it, no other piece of the Memory OS has data to work with. Build it before Pinecone, Firecrawl, or the refinement loop.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
