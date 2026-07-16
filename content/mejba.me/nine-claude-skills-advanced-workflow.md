**BRAND:** mejba.me
**TITLE:** 9 Claude Skills That Run My Entire Workflow in 2026
**META TITLE:** 9 Claude Skills Advanced Workflow Patterns (2026)
**SLUG:** nine-claude-skills-advanced-workflow
**PRIMARY KEYWORD:** Claude skills advanced workflow
**META DESCRIPTION:** The 9 Claude skills I actually run every week, from skill-creator to Superpowers and learnings.md, plus the one pattern that made my skills self-improving.

**TAGS:** Claude Code, AI Skills, Workflow Automation, AI Agents, Productivity

---

I almost wrote a different version of this article. The original outline was "10 Claude skills you should install." I had the list. I had the install commands. I was three paragraphs in when I realized I'd written that post twice already — once as a [skills install guide](https://www.mejba.me/blog/claude-code-skills-worth-installing) and once as a [33-skill ecosystem breakdown](https://www.mejba.me/blog/top-33-claude-skills-ecosystem). The world doesn't need a third install list from me.

What it might need is the actual answer to a question I keep getting asked: *which skills do you run every single day, and how do they connect to each other?* Because installing skills is trivial. The hard part — the part nobody seems to write about — is how a small set of skills, used together, becomes the operating system of a working solo engineer.

This is that breakdown. Nine **Claude skills** that survived my filtering, grouped by where they live in my workflow. Some are obvious. One is non-obvious. One isn't a skill at all in the technical sense — it's a pattern called `learnings.md` that I think is the single biggest unlock of 2026, and I'll save it for the end because it changes how you should think about every other skill on the list.

Before we go further: there are 500,000+ skills in circulation as of May 2026, and I've watched developers install thirty of them in an afternoon, panic at the noise, and uninstall everything by Friday. The number you actually need is closer to nine. Maybe less.

<!-- IMAGE: A three-tier diagram showing Claude Desktop skills at the top, Claude Code skills in the middle, and the learnings.md pattern at the base. Alt text: "Claude skills advanced workflow architecture three tiers". Caption: "The three layers of a working Claude skills stack — and the pattern that connects them." -->

## Why Most People's Skill Stacks Are Broken

Quick reality check before the list. I've audited the Claude setups of about twenty people in the last two months — friends, clients, a few engineers in Discord who asked for help. The pattern is almost universal.

They have eighteen skills installed. They use three. The other fifteen are dead weight that bloat their context window and create decision fatigue when Claude has to choose which one applies.

The good setup looks different. Five to nine skills, each with a specific job, each tested against a real workflow the user actually has. The skills don't compete with each other — they hand off. A research skill feeds a writing skill. A writing skill feeds a fact-checking skill. A fact-checking skill feeds a humanizing skill. There's a flow, not a pile.

That's the lens I want you to use as you read the list. Don't ask "should I install this?" Ask "where does this fit between the skills I already use?"

## Tier 1: The Four Skills That Live in Claude Desktop

The first four don't require code. They run in Claude Desktop, work with any plan that supports skills, and most of them are install-once-use-forever tools. If you're not a developer, this tier is where you start.

### 1. Skill Creator — The Skill That Builds Skills

I used to write skills the hard way. I'd open a markdown editor, copy a template, fight with YAML frontmatter, install it, realize I'd structured the triggers wrong, edit, reinstall, repeat. It was the kind of friction that made me avoid creating skills even when I had a clear repetitive workflow that needed one.

Then Anthropic shipped [the skill-creator skill](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md). It's exactly what it sounds like — a skill whose only job is to interview you about a workflow and generate a working skill from the conversation. No template editing. No YAML. You describe what you do repeatedly, it asks clarifying questions, and it writes the skill file.

The non-obvious part is the testing. Before it commits the skill, skill-creator generates eval cases that compare Claude's output *with* the skill versus *without* it. If the skill doesn't measurably improve output on the test cases, you find out before you install. I cannot overstate how much wasted time this prevents. Most skills I would have shipped six months ago wouldn't have passed their own eval.

The way I use it: every time I catch myself giving Claude the same setup instructions for the third time in a week, I open skill-creator and walk through the interview. If the evals pass, the skill goes in. If they don't, I've at least learned that the workflow wasn't as repeatable as I thought.

### 2. Prompt Master — Cleaning Up Messy Prompts Before They Hit a Model

This is the one most people sleep on. Prompt Master takes a messy, half-formed prompt and rewrites it into a structured prompt optimized for the model you're actually targeting. It detects intent across nine dimensions — what you want, who it's for, what format, what constraints, what tone — and asks up to three clarifying questions if anything is ambiguous.

The reason this matters: most "AI gave me a bad answer" complaints are actually "I gave AI a bad prompt" problems in disguise. I covered the underlying mechanic in [why precise prompting beats clever prompting](https://www.mejba.me/blog/ai-prompting-rules-reduce-guessing) — Prompt Master is the operationalized version of that idea. It refuses to send a vague prompt to a model.

I integrate it into Claude Desktop as a pre-processor for any non-trivial prompt. The workflow is: I type my rough thought, Prompt Master rewrites it, I approve or tweak, the rewritten prompt goes to Claude. The first time I tried this against a research task I'd been struggling with, the rewritten prompt was eleven lines longer than mine — and the output was the kind of thing I'd have spent forty-five minutes refining manually.

### 3. Fact Checker — The Non-Negotiable for Anything Public-Facing

Here's the rule I've made non-negotiable in my workflow: nothing leaves my desk for a public audience until Fact Checker has touched it.

What it does is mechanical and unglamorous. It pulls every factual claim out of a piece of text, cross-references each one against external sources via web search, and produces a report that buckets every claim into *confirmed*, *unverifiable*, or *false*. For a 3,000-word post, the report is usually ten to twenty claims, and the typical run flags two or three that need attention.

The reason this skill matters more in 2026 than it did six months ago: AI-generated content is everywhere, hallucinations still happen, and your professional reputation is downstream of being right. I've watched two friends — both smart, both careful — ship posts with confidently-stated stats that turned out to be invented by the model. The fix isn't to write more carefully. It's to bolt a verification step onto your pipeline so the careful part happens automatically.

What I'd do differently if I were starting now: install Fact Checker before you install anything else on this list. Not because it's the most exciting — it's not — but because it's the one skill whose absence will eventually cost you something you can't easily get back.

### 4. Humanizer — The Final Step Before Anything Ships

The other half of the public-facing pipeline. Humanizer takes AI-generated text and strips the patterns that scream "this was written by a model." Inflated symbolism. Overused em-dashes. Vague corporate jargon ("leverage," "robust," "navigate the landscape"). Sentences that all start with the same structure.

What separates Humanizer from the generic "make this sound human" prompts you've probably tried: it analyzes samples of *your* writing first, builds a profile of your sentence rhythm and word choices, then rewrites the input to match. Done well, the output sounds like a slightly tired version of you on a Thursday afternoon — which is to say, it sounds human.

This is where I want to make a confession: I run every piece of public writing — including this article — through a humanizing pass before I publish. Not because I didn't write it. I did. But because if you write enough with an AI in the room, certain patterns leak in whether you notice or not. The pass catches them.

There's an irony I'm aware of here. Using AI to make AI-assisted writing sound less like AI. But that's the world we're in, and I'd rather be honest about the workflow than pretend the assist isn't there.

## Tier 2: The Four Skills That Live in Claude Code

Now we leave Claude Desktop and go to the terminal. These four are the skills I run when I'm doing engineering work — building features, shipping code, reviewing what I've built before merging. If you're a developer using [Claude Code as your daily driver](https://www.mejba.me/blog/mastering-claude-code-crash-course), this tier is where most of the leverage lives.

### 5. Playwright Skill + Superpowers — The Combo That Catches What Humans Miss

I'm grouping these because they're best used together, but they do very different things.

The [Playwright skill](https://github.com/lackeyjb/playwright-skill) gives Claude direct browser control. Instead of writing Playwright tests as code, you describe what you want tested, and Claude opens a visible browser window and runs the interaction. You watch it happen. The model clicks the button, fills the form, navigates the flow, and reports back what it found.

The kind of bug this catches is the kind that human testers and unit tests both miss. Last month I had a button that was technically in the DOM, technically clickable, and technically present in every test snapshot. It rendered behind a sticky header on the iPad viewport. A human eyeballing the page would have noticed. Unit tests didn't. Playwright simulating a real user on iPad caught it in eleven seconds.

[Superpowers](https://github.com/obra/superpowers) — the plugin from Jesse Vincent that got accepted into the official Anthropic plugin marketplace in January 2026 — sits on top of that and enforces discipline. It pushes Claude through a five-phase loop: clarify, design, plan, code, verify. No "just start coding" shortcuts. I wrote the full breakdown in [my Superpowers plugin review](https://www.mejba.me/blog/superpowers-plugin-claude-code-review) if you want the deep dive.

The combo is what makes them dangerous. Superpowers forces planning and verification. Playwright provides the actual verification mechanism. Together they catch the class of bugs that quietly ship to production and surface a week later when a user complains.

### 6. Sub-Agent Pack — Ten Specialists Instead of One Generalist

The pattern here changed how I think about complex tasks. Instead of asking Claude to wear ten hats sequentially — planner, architect, lawyer, marketer, support lead — you deploy ten sub-agents in parallel, each with a defined role and a defined scope. They don't compete for context. They don't have to be re-briefed. They run their analyses simultaneously, and you get ten perspectives back at once.

I use this most heavily for product decisions where I'd normally have to consult three or four humans. Should I launch this feature? The marketing sub-agent looks at positioning. The legal sub-agent flags risk. The customer experience sub-agent walks the user journey. The architecture sub-agent flags the technical debt I'm taking on. The planning sub-agent sequences the work.

The result is the closest thing I've found to having a senior team for one person. I went deep on this pattern in [Anthropic's agent teams playbook](https://www.mejba.me/blog/anthropic-agent-teams-ai-coding) — if you've read that one, you already know the architecture. The pack is just the productized version.

There's a paid version of this floating around for $49 and a free version that does most of the same work. I run the free version for routine work and only reach for the paid pack when the stakes justify the extra polish.

### 7. Code Review — `/review` and `/security-review`

This is the unsexy skill that pays for itself faster than anything else on the list.

`/review` runs a code review on your pending changes. Bugs, edge cases, design flaws, style issues. `/security-review` is the security-focused variant — it specifically hunts for vulnerabilities, injection risks, auth gaps, secrets leaking into logs. Both run locally. Both are free. Both should run on every meaningful change before you push.

The reason this works better than a human review for most cases: it doesn't get tired. A human reviewer on PR #14 of the day is not the same reviewer they were on PR #2. The model is the same reviewer every time. It catches the same class of issue at 5pm that it would at 9am.

This is the skill I've recommended most aggressively to clients. Half of them ignored me. The other half wrote back two weeks later telling me they'd caught a SQL injection bug in a PR that had three human approvals on it. I'm not going to pretend the model is better than a careful senior engineer — it's not. I am going to say that the marginal cost of running `/review` on every PR is zero, and the expected value of catching one production bug before it ships is high.

If you'd rather have someone audit your codebase and set up these review workflows for you, I take on these engagements through [my Fiverr](https://www.fiverr.com/s/EgxYmWD) — but honestly, this one you can install yourself in five minutes.

### 8. Context Manager — The Skill That Stops Claude From Forgetting

Every long Claude Code session eventually runs into the same wall: the context window fills up, the model starts forgetting decisions you made forty minutes ago, and the quality of output silently degrades. You don't notice until Claude contradicts itself or suggests a fix you've already rejected.

The conventional advice — "just run `/compact` when you're running out of room" — is wrong in one important way. By the time you're at 90% context, you've already lost quality. The current best practice, [confirmed across multiple 2026 Claude Code guides](https://www.mindstudio.ai/blog/claude-code-compact-command-context-management), is to compact proactively at around 60% utilization. Before quality starts to slip. Not after.

A Context Manager skill operationalizes that. It tracks your current context utilization, surfaces it visibly, and prompts you to compact when you cross the 60% threshold. It also supports `/compact <instructions>` — meaning you can tell it what to preserve. *"Compact this session but keep the schema decisions and the auth flow we worked out."*

The rule I live by now: every 20-30 minutes on a long session, I compact. Every time I finish a discrete sub-task and before I start the next one, I compact. The cost is twenty seconds. The benefit is the difference between Claude remembering what we built and Claude inventing a fresh architecture every time my window fills up.

The deeper rule, which I've written about in [Claude Code's 1M context management](https://www.mejba.me/blog/claude-code-1m-context-management): more context isn't the answer. *Better-managed* context is. The Context Manager skill is the discipline.

## Tier 3: The Pattern That Makes Every Other Skill Compound

If you're going to take one thing from this article, take this section.

### 9. The `learnings.md` Pattern

This isn't a skill in the technical sense. There's no install command. There's no SKILL.md file you download. It's a pattern — a single markdown file you create inside your project — that turns every skill you've installed into a self-improving system.

Here's how it works. You create a file called `learnings.md` at the root of your project. The first time you run any skill, you instruct it to read `learnings.md` before it does anything else, and to append to `learnings.md` after it finishes — specifically anything it learned about your preferences, your style, your corrections, your errors.

Run one. The skill reads an empty file, does its thing, and appends a few observations. *"User prefers em-dashes over commas for parenthetical asides. User flagged the word 'leverage' as banned. User wants first-person voice in mejba.me posts."*

Run two. The skill reads the file from run one, applies those observations, and adds new ones. *"User prefers Markdown headers H2 and H3 only, not H4. User cuts sentences over 30 words. User adds personal anecdotes in section openers."*

By run twenty, the file has 80 to 100 bullet points describing exactly how you work, what you've corrected, what's worked, what hasn't. The skill that started as a generic template is now a precision instrument calibrated to you. And every other skill that reads the same file inherits that calibration.

The quote I keep coming back to, from one of the better breakdowns of this pattern: *"It's the difference between a calculator and a full-on computer."* A calculator does the same thing every time. A computer remembers what you taught it. Skills without `learnings.md` are calculators. Skills with it are computers.

The maintenance: once a week, or whenever the file exceeds about 100 bullets, you consolidate. Read everything, merge duplicates, remove obsolete entries, and rewrite the patterns into synthesized principles. Skip this step and the file becomes noise. Do it weekly and it becomes the most valuable file in your project.

I run a version of this pattern on `@aria`, my content agent. The first month it generated posts that needed heavy editing. Six months in, it produces posts that need light editing — because every correction I made got captured. The compounding isn't dramatic week-to-week. It's dramatic when you compare month one to month six.

## What Most Articles On This Topic Get Wrong

I want to be honest about a few things before we wrap.

The first is that the "install nine skills and your life changes" framing is misleading. Installing skills changes nothing. *Integrating* skills into a workflow changes a lot. Most people install skills the way they collect Chrome extensions — eagerly, then ignore them.

The second is that the skill ecosystem is moving faster than any article can keep up with. The nine I've described will probably look different in three months. New skills will replace some of these. The patterns — pre-prompt cleanup, fact-checking before publish, sub-agent specialization, proactive context management, persistent learnings — those won't change. Tools change. Patterns compound.

The third is that I'm wrong about some of this. I've changed my mind about Claude skills three times in the last year. I was skeptical at the start. I was over-enthusiastic in the middle. I'm somewhere in between now. If you read this article in six months and think "this aged badly," you're probably right about specific tools and probably wrong about the underlying patterns.

## Frequently Asked Questions

### How many Claude skills should I actually install?
Five to nine, max. The right setup has skills that hand off to each other — research feeds writing, writing feeds fact-checking, fact-checking feeds humanizing. More than nine and you create decision fatigue for both yourself and the model. For the full reasoning, see "Why Most People's Skill Stacks Are Broken" above.

### What is the learnings.md pattern in Claude Code?
The learnings.md pattern is a persistent markdown file your skills read at the start of every session and append to at the end, accumulating corrections, preferences, and patterns over time. It turns generic skills into personalized ones without retraining. See "The learnings.md Pattern" section above for the full implementation.

### When should I run /compact in Claude Code?
At 60% context utilization, not 90%. Proactive compaction preserves output quality; reactive compaction after quality has already degraded is too late. Use `/compact <instructions>` to control what gets preserved. See the Context Manager section for the full protocol.

### Do I need Superpowers if I already use Claude Code?
Only if you want to enforce a planning-and-verification loop on every task. Superpowers adds a five-phase discipline (clarify, design, plan, code, verify) that prevents "just start coding" mistakes. It's optional but high-leverage on engineering work.

### Is skill-creator better than writing skills manually?
For most workflows, yes. Skill-creator generates eval test cases that prove a skill improves Claude's output before you commit to using it — manual skill writing skips that verification step and often results in skills that don't actually help.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
