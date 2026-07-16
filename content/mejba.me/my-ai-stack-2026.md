**BRAND:** mejba.me
**TITLE:** My AI Stack 2026: How I Stay Sane in a Tool Storm
**META TITLE:** My AI Stack 2026: Tools, Tiers, and Mindset
**SLUG:** my-ai-stack-2026
**PRIMARY KEYWORD:** my AI stack 2026
**META DESCRIPTION:** My AI stack 2026 — the S, A, B, C tier tools I actually use, the ones I cut, and the mental model that keeps me from drowning in launches.
**TAGS:** AI Tools, Productivity, Workflow, Claude Code, Personal Stack

---

# My AI Stack 2026: How I Survive the Tool Storm Without Drowning

I counted on a Tuesday morning in early May.

Forty-three. That was the number of AI tools I had either bookmarked, signed up for, or actively paid for in the last twelve months. Forty-three. I'd lost track somewhere between the GPT-5.5 rollout and the third "Claude Code killer" that promised to replace my entire workflow.

The number wasn't the disturbing part. The disturbing part was that I could only name about nine of them off the top of my head. The rest had quietly drifted into a graveyard of half-tested apps and abandoned subscriptions, each one chosen during a wave of FOMO at some point in the last year.

This is the actual problem with the 2026 AI ecosystem. It's not that the tools aren't good. They're terrifyingly good. The problem is that there are too many of them, they ship too fast, and the cost of keeping up has quietly become a full-time job — one that nobody's paying you for.

So I did something I should have done six months earlier. I sat down for three hours, ranked every AI tool I'd touched into honest tiers, and asked one question of each: *Did this actually move work forward, or did I just enjoy testing it?*

That sort matters now more than ever. Claude Code [shipped its May 2026 update](https://code.claude.com/docs/en/changelog) with subprocess sandboxing, plugin archive support, and a new Monitor tool for streaming background scripts. OpenAI dropped GPT-5.5 inside Codex and pushed Pro $200 to 20x Plus on an ongoing basis. Hermes Agent hit v0.12.0 on April 30 with 118 bundled skills. Perplexity made Comet fully free across iOS, Android, Windows, and Mac. Every single one of those things landed in roughly six weeks.

If you've been feeling the same low-grade panic I have — that you're behind, that you should have tested the new model, that your stack is somehow already obsolete — this post is the antidote. Here's the stack I actually run in May 2026, sorted into S, A, B, and C tiers based on real usage, plus the five mindset rules that keep me from spiraling every time a new launch hits X.

## Why Most "AI Stack" Posts Are Useless

Before I get into tools, I want to be honest about something.

Most "my AI stack 2026" posts you'll read this year are written by people optimizing for affiliate revenue, not workflow. They list twenty tools, give every one of them a glowing review, and never mention the ones they cut. That's not a stack. That's a catalog.

A real stack has a graveyard attached to it. The tools you stopped using are at least as informative as the tools you kept — usually more so, because they reveal what kind of work you actually do versus what kind of work you *thought* you did.

So I'll tell you upfront which tools I phased out, why, and what replaced them. ChatGPT regular chat: phased out for everything except occasional GPT-5.5 sanity checks. Cursor: replaced by Claude Code inside VS Code. NotebookLM: replaced by Hermes Agent for personal knowledge work. WhisperFlow: replaced by Glydo. Poppy AI and Anytten: phased out as my voice-to-text needs simplified. OpenClaw: an experiment I built that Hermes effectively superseded.

Every one of those was a tool I genuinely liked. Some of them I evangelized in old posts. The fact that they're gone isn't a knock on them — it's a knock on the assumption that any tool, no matter how good, deserves permanent space in a stack that lives in a market this volatile.

That brings me to the first rule of running an AI stack in 2026.

## Rule One: Your Stack Is a Harness, Not a Religion

I keep one project directory on my Mac. Inside that directory, three different AI agents work interchangeably — Claude Code, Codex, and Hermes — each one operating on the same files, the same context, the same `.claude/` and `.codex/` config folders.

This isn't a hack. This is the mindset shift that took me the longest to internalize.

For about a year, I treated AI tools like operating systems. Pick one, commit to it, build everything around its quirks. That worked when there were two coding agents that mattered. It stopped working the day I realized I was rewriting the same prompt scaffolding three times — once for Cursor, once for Claude Code, once for whatever new agent I was testing that week.

Now the project itself is the source of truth. Clean directory structure. Modular code. A `CLAUDE.md` file that any agent can read for context. The agents are harnesses I plug *into* the project, not the other way around. When Claude Code 2.x shipped, I switched in 90 seconds. When Codex got the persisted `/goal` system, I tested it on the same project without rebuilding anything.

If you're going to take one mental model away from this post, take that one. Tools change. Projects shouldn't have to.

That principle is what makes the rest of my stack make sense.

## The S Tier: Daily Drivers I'd Cry Over Losing

S tier is the smallest tier on purpose. These are the three tools I touch every working day, multiple times per day, and whose disappearance would force me to redesign my workflow from scratch within 24 hours.

### Claude Code (inside VS Code)

This is the engine. Period.

I run Claude Code as my primary AI development surface, embedded inside VS Code as my main editor. Not Cursor. Not Antigravity. Not the Claude Code desktop app. The VS Code + Claude Code combination is what I open when I sit down to actually ship code.

The May 2026 update added a few things that landed harder than I expected. Subprocess sandboxing with PID namespace isolation on Linux means I can run agents in genuinely isolated environments. The new Monitor tool streams events from background scripts as notifications, which sounds boring until you realize it changes how long-running agent loops feel — you stop checking on them and start trusting them. The `claude project purge` command finally gave me a clean way to nuke project state when an experiment goes sideways. And `--plugin-dir` now accepts `.zip` archives, which has done more for my plugin testing speed than any feature in the last six months.

I'm not going to relitigate why Claude Code beats Cursor for my workflow — I [wrote about that here](https://www.mejba.me/codex-claude-code-replace-cursor) and the short version is autonomy, agent loops, and the file-level surgical edits. The bigger reason it sits in S tier is the harness shape. Claude Code respects my project. Cursor wanted to be my editor. There's a difference, and a year of actual use made it obvious which mattered more.

What still drives me crazy: occasional context blow-ups on long sessions, even with the 1M context window patches Anthropic shipped earlier this year. I covered the workarounds in my [Claude Code 1M context management post](https://www.mejba.me/claude-code-1m-context-management) and they help, but they're workarounds, not fixes.

S tier anyway. By a wide margin.

### VS Code

This one is going to feel boring. I don't care.

VS Code is my main OS-of-thought. Every project, every script, every blog post draft, every prompt experiment lives inside VS Code. It's the surface where Claude Code runs, where my local Hermes scripts execute, where I edit `CLAUDE.md` files between agent sessions. The thing I love about VS Code in 2026 is that it has *not* tried to become an AI product. It stayed an editor. It got better at being an editor. The AI happens inside it through Claude Code and other extensions, not as some bolted-on side panel that fights for screen real estate.

Antigravity tempted me. I tested it for two weeks in April and wrote about it [in this Antigravity walkthrough](https://www.mejba.me/anti-gravity-ide-ai-agents). It's clever. The visual planning canvas is genuinely interesting. But I kept finding myself ALT-TAB'ing back to VS Code to actually finish the work, and after fourteen days I admitted what was already obvious — the editor I trust at 2 AM when something is broken matters more than the editor with the better demo video.

VS Code stays. S tier.

### Glydo (Speech-to-Text)

The third member of the S tier is the one most people are going to skip past, and they shouldn't.

I dictate. A lot. Drafts of articles, replies to long emails, prompts that would otherwise take five minutes to type — all of it goes through voice now. For most of 2024 and 2025 I used WhisperFlow, which was excellent. Then I tried Glydo and didn't go back.

Glydo lives in your menu bar (Mac for now, Windows support is on the roadmap), listens for a hotkey, and converts speech to text in any app you're focused on. The accuracy difference versus Whisper-based competitors is real but small — what actually shifts the experience is latency. Sub-second turnaround, even on long dictations. When you're using voice as a thinking tool instead of just a typing replacement, that latency window is the difference between flow and friction.

I'm going to be honest about a friction point: I almost dropped Glydo after the first day. The setup felt awkward, the hotkey conflicted with a Raycast binding I'd had for two years, and I was annoyed. Then I gave it 48 more hours and it clicked. The 20% productivity dip rule (which I'll get to below) almost cost me my favorite voice tool of 2026.

Three S-tier tools. Notice what's not on that list: any LLM chat product. That's deliberate.

## The A Tier: Weekly Workhorses

A tier is the tools I use multiple times per week — not every day, but often enough that switching away from them would noticeably hurt. There are five.

### Codex (with GPT-5.5)

Codex is the second coding agent in my rotation, and I run it as a deliberate counterweight to Claude Code rather than a competitor.

The pricing situation in May 2026 actually made the math easier. Codex Pro $200 [now includes 20x Plus on an ongoing basis](https://chatgpt.com/codex/pricing/), with higher 5-hour limits at 25x Plus through May 31. GPT-5.5 was tuned to deliver better results with fewer tokens than GPT-5.4 for most users. For teams running both ChatGPT and Codex, that pricing rebalance means having a Codex subscription alongside Claude is no longer the obvious financial loss it used to be.

What Codex does well: reasoning-heavy refactors, multimodal tasks where I need image-aware code generation, and the persisted `/goal` system that lets one chat track work across multiple sessions. What it does badly compared to Claude Code: long autonomous loops on my codebase, file-level surgical edits without overwriting context, and integration with the VS Code workflow I've built. I [tested both extensively in this two-agent workflow post](https://www.mejba.me/claude-code-codex-two-agent-workflow) — the conclusion holds: use Codex for reasoning and second opinions, use Claude Code for shipping.

A tier, comfortably. Not S because if Codex disappeared tomorrow I'd be sad, not stranded.

### Claude Chat

Claude Chat is the tool I use for thinking *about* work, not doing it.

When I'm stuck on a strategy question — pricing a new offer, structuring a long-form post, sequencing a launch — Claude Chat is where I run the conversation. It's better than Claude Code for that because Code wants to *do* something. Chat is content to sit with the question, push back, and reason through tradeoffs. The 1M context window patches earlier this year mean I can dump a year of project context into a single thread and ask coherent strategic questions against it.

I don't use Claude Chat for code. That's what Claude Code is for. The split keeps both of them sharp.

### Hermes Agent

Hermes is the wildcard on this list and the tool I'm most likely to evangelize at parties (sorry, friends).

[Hermes Agent](https://hermes-agent.nousresearch.com/) is an open-source self-improving agent built by Nous Research. The v0.12.0 release on April 30 ships with 118 bundled skills out of the box and was built by 213 community contributors across 550 merged PRs in a single release window. The number that matters isn't the contributor count — it's that Hermes maintains a `MEMORY.md` file of agent-curated facts about you, your projects, and your workflows, and writes to that file with periodic nudges to persist durable knowledge.

I run Hermes on a cloud VM and talk to it through Telegram. Voice memos get auto-transcribed. Scheduled task results land in my Telegram thread. Group chats can include Hermes as a participant. It's the closest thing I've found to a personal knowledge agent that actually *remembers* across sessions instead of pretending to.

It replaced NotebookLM in my stack. It replaced an experimental tool I'd built called OpenClaw. It might replace more things by the time you read this. The reason it's in A tier and not S is honesty — I've only been running it daily for about six weeks. S tier requires earned trust, and Hermes hasn't been around long enough to fully earn it. Yet.

### Perplexity (with Comet)

Perplexity is my research entry point. Has been for two years. The reason it stays in A tier in 2026 is Comet.

[Comet](https://www.perplexity.ai/grow/comet) — the Perplexity browser — went fully free across iOS, Android, Windows, and Mac during 2026. It launched as a $200/month desktop product in mid-2025 and spent the year going free across every platform. Free is the right word: actually free, not free-with-an-asterisk. The browser agent is now powered by Opus 4.5 by default for Max subscribers, with reasonable agent capabilities for Pro users.

What I use Comet for: any research task that requires me to *act* on what I find, not just read it. Booking, form-filling, comparison-tab triage. Regular Perplexity Pro at $20/month covers my Deep Research needs (20 queries per day) and unlimited Pro Search.

What I do not use Comet for: as my primary browser. I tried. Two weeks. I went back to Arc. The agent features are great when I need them, but I want my main browser to stay out of the way when I'm not asking it to do anything, and Comet's surface area is bigger than I want for default use.

A tier as a research tool. C tier as a primary browser. Both can be true.

### Groq (for X/Twitter Research)

Groq's role in my stack is narrow but important.

When I need fast answers from current X/Twitter content — what's a specific person saying about a topic this week, who's discussing a launch, what's the actual thread sentiment versus the headline — Groq's speed and X integration are unmatched for that specific job. I don't use Groq for anything else. I don't try to make Groq a general-purpose LLM. It does one thing extremely well, and that's why it's in A tier instead of dropped entirely.

This is what I mean when I say specialists earn their keep. A tool that does one thing better than anything else and stays in its lane is more valuable than a tool that does five things adequately.

That principle is the entire B tier.

## The B Tier: Specialists for Specific Jobs

B tier tools are the ones I reach for when a specific micro-task demands them. They're not in my daily rotation. They are absolutely in my workflow when the moment calls for them.

### Apify (Automation & Scraping)

When I need data from a website that doesn't have an API, Apify is the tool. Pre-built actors for the common cases, custom actors when I need something weird, and a billing model that hasn't bitten me yet. I use Apify maybe twice a month. Those two times save me five hours each.

### GPT Image 2 (Production Imagery)

For thumbnails, blog hero images, and any image where text rendering matters, GPT Image 2 is my default. The prompt adherence and text rendering wins are real — when I need a thumbnail with a specific phrase rendered cleanly, GPT Image 2 is [the only model that does this reliably](https://evolink.ai/blog/gpt-image-2-vs-nano-banana-2-2026). Nano Banana 2 is faster and prettier for some categories. GPT Image 2 is the one I trust for shipping.

### Nano Banana 2 (Image Editing & Speed)

Nano Banana 2 is in B tier specifically for image editing tasks and speed-critical generation. When I need batch variations, when I need anime-leaning illustration, when I need pro-level results in 4 to 6 seconds — that's the call. The two image models share a slot in my brain: GPT Image 2 for ship-to-customer outputs, Nano Banana 2 for iterate-and-explore.

### Kie.ai (Image/Video Gen Integration)

Kie.ai is how I run multi-model image and video generation through one API surface. Not glamorous. Saves hours of integration work. B tier for exactly that reason.

### HeyGen (Video Avatars)

[HeyGen pricing in 2026](https://help.heygen.com/en/articles/9204682-heygen-pricing-plans-and-subscriptions-explained-what-you-need-to-know) ranges from $29 to $89 per month for AI avatar video with synced voice. Avatar IV costs 20 credits per minute, with the Creator plan giving roughly 10 minutes per month. I use HeyGen when I need a presenter-style video for a course module or a launch explainer and don't have time to film. I do not use HeyGen for thought leadership content where being on camera myself matters. The line between those two use cases is bright and I respect it.

### ElevenLabs (Voice Cloning)

ElevenLabs handles every voice job in my pipeline that isn't live presenting. [Pricing tiers](https://gptprompts.ai/ai-pricing/elevenlabs-pricing) span Free through Scale ($330/month). I run on Creator at $22/month, which covers my volume. The clone quality on a 30-second to 2-minute sample is good enough for narration; the professional clone on a longer sample is good enough for projects I care about. HeyGen integrates with ElevenLabs natively, which made the decision to keep both stupidly easy.

### OpenRouter

OpenRouter is the universal API switchboard I use for prototype agents that need to swap models without rewriting code. It's not in A tier because I don't use it daily, but the times I do use it, the value is enormous. Every freelancer and indie hacker building agents in 2026 should know OpenRouter exists.

That's the B tier. Each one solves a specific problem extremely well. None of them are trying to be my whole workflow.

## The C Tier: Experimenting, Watching, Not Committing

C tier is where I park tools that I'm interested in but haven't given a real role yet. The honest framing: these are tools I'm watching, not tools I'm using.

**Gemini.** The 2.5 line is genuinely impressive. The integration with Google Drive and the file-creation features are impressive. The reason Gemini isn't higher in my stack is that my work doesn't live in the Google ecosystem — I'm not in Docs, not in Sheets, not in Drive as my primary document layer. For someone who is, Gemini probably belongs in A tier or higher. I'm watching it for that reason. The day my workflow shifts to Google-first, Gemini moves up.

**Antigravity.** Tested it for two weeks in April. Smart product. The visual planning canvas had moments of real clarity. Did not displace VS Code + Claude Code for me. I'm keeping an eye on the next two releases — there's a version of this product that becomes legitimately interesting if they nail one specific UX problem (the agent-handoff between visual canvas and editor surface).

**Ollama (Local Models).** I run Ollama for offline experiments and any task where I need to keep a model on-device for privacy reasons. Not part of my daily stack. Permanent C tier with a clear job.

**Manifold.** Currently testing for a specific multi-agent orchestration use case. Too early to tell where it lands.

The thing about C tier is that it's *supposed* to stay small. The mistake most people make with AI tool experimentation is that everything new lives in C tier forever, and the tier becomes a graveyard rather than a holding pattern. C tier should rotate — tools either get promoted or removed within a quarter.

## The Mindset Rules That Actually Matter

The tools are the easy part. The mindset is what determines whether your stack helps you or owns you. Here are the five rules I run on.

### Rule 1: Define Your North Star

My north star is clear and singular: *test AI tools and share what I learn.* Every tool I add to my stack either serves that mission or it doesn't. If a tool would be amazing for someone whose work is different from mine, that's fine — for them. I'm not in their stack. They shouldn't be in mine.

This rule alone will cut your tool list in half. Most "FOMO" purchases happen because we forget who we are. A new tool launches that's clearly excellent for a copywriter, and if I'm not a copywriter, it doesn't matter how excellent it is. Pass.

### Rule 2: The 20% Productivity Dip Is Real

Every time you switch a tool, your productivity drops about 20% for the first stretch. This is not a guess — it's a pattern I've watched in myself across maybe twenty tool switches in two years. The question is never "is this new tool better?" The question is "is this new tool *enough* better that it justifies losing 20% of next week's output?"

Most of the time the honest answer is no. That's how you end up keeping good tools instead of chasing slightly-better ones.

### Rule 3: Save Tools for Later, Don't Test Everything Now

When a new launch hits and I'm tempted to drop everything and test it, I run two questions: *Does this solve a current pain point?* and *If no, can I save it for later?*

If yes, I test it within the week, in real scenarios, fast. If no, it goes into a notes file called `tools-to-revisit.md` with one line — what the tool is, why I might need it eventually, and what pain point would trigger me to test it. That file gets reviewed monthly. Most entries never get tested. That's the point.

### Rule 4: Productivity Is Measured by Needle Moved, Not Hours Worked

Four focused hours with the right stack beats twelve unfocused hours with the wrong one. I've stopped tracking time as a productivity metric and started tracking *output that ships.* The question at the end of a day is "what moved that wouldn't have moved without me?" If the answer is "a lot," the day was good regardless of how many hours I worked. If the answer is "honestly not much," twelve hours of effort doesn't fix it.

This rule kills tool-testing rabbit holes faster than anything else. An hour spent testing the latest model launch *can* be productive if it leads to a stack change. An hour spent testing it because you saw a tweet is just lost time wearing the costume of work.

### Rule 5: Different Tools for Different Micro-Tasks

A single video production cycle in my workflow looks like this. Research goes through Perplexity. Scripting happens in Claude Code (inside VS Code). Strategic questions about angle and framing run through Claude Chat. Thumbnail design lives in GPT Image 2. Visual variations come from Nano Banana 2. Voiceover happens in ElevenLabs. Avatar segments (when needed) go through HeyGen. Final edit happens in DaVinci Resolve.

That's nine tools for one video. None of them are trying to do all nine jobs. Each one does its piece extraordinarily well, and the workflow stitches them together.

The lesson: stop looking for the one AI that does everything. Build a chain of specialists. The handoff between them is where your judgment lives, and that judgment is the thing AI can't replace.

## The Decision Framework I Use for Every New Tool

When something new lands, I run it through five questions before I touch it.

**1. Does this solve a current pain point I have right now?** If yes, continue. If no, save the link and move on.

**2. Is the pain point big enough that the 20% dip is worth taking?** Some pain points are real but small. Not every friction is worth a tool change.

**3. Can I test it in real scenarios within the next seven days?** Not "can I read about it" — can I actually use it on real work? If not, save for later.

**4. After 7 days of real use, does it earn a spot in my daily stack?** Three outcomes: yes (promote to A or S tier), maybe (park in B or C), no (remove and move on).

**5. If it earned a spot, what comes out?** Tools don't just enter the stack. They displace something. Naming what they displace forces honesty about whether the new tool is actually better or just newer.

This framework is the entire reason I have nine tools in active rotation instead of forty-three. The framework runs by itself once you internalize it. Most weeks I run it without realizing I'm running it.

## Supporting (Non-AI) Tools That Still Run My Day

It would be dishonest to pretend my stack is only AI tools. The non-AI tools that hold the rest of the workflow together:

**ClickUp** for project management — every project, every client, every personal task. The reason ClickUp survived an AI-tool wave that ate Notion in my workflow is that ClickUp's structure rewards consistency and AI tools make consistency cheap. The two compound.

**Hostinger VPS** for the cloud machines that run Hermes Agent and any deployed agent infrastructure. Cheap, fast, and the support team has actually helped me solve problems at 2 AM.

**Fireflies** for meeting transcription. I tried Granola. I tried Otter. Fireflies wins for me because of the way it surfaces action items inside threads I already use. It stays.

The non-AI layer is the chassis. The AI layer is the engine. Both have to work or the car doesn't move.

## What Comes Next

If I had to predict where this stack is in November 2026, here's my honest guess.

S tier will probably hold. Claude Code, VS Code, and Glydo are sticky in a way that is hard to displace. A tier will rotate — Hermes either earns S tier or gets demoted, depending on how the next two releases land. Codex's position depends on whether OpenAI ships another pricing reset that changes the math. Perplexity Comet probably gets more aggressive with agent features.

B tier will get more crowded. Specialist tools are the easiest to add without disrupting workflow, and the rate of specialist launches is accelerating. C tier will rotate the most — that's its whole purpose.

What I don't expect to change is the framework. The S/A/B/C model, the five mindset rules, the decision framework — those are stable because they're about how I think, not what I use. Tools come and go. Mental models compound.

That's the part I want you to take with you.

Don't copy my stack. Build your own using the same logic. Pick your north star. Know what's S tier for *you*. Run the 20% rule on every tempting launch. Park experiments in C tier with intent. Measure output, not hours. Build chains of specialists, not monoliths.

The forty-three tools I bookmarked over a year? Most of them weren't bad tools. They just weren't *my* tools. The day I started running this framework instead of chasing launches was the day I stopped feeling like AI was happening to me and started feeling like I was using it.

If you're reading this on a Tuesday morning with your own count of bookmarked-but-untested tools haunting the back of your brain — pick one. Run the framework on it. Promote it, demote it, or remove it. Then do the same to one more tomorrow. Six weeks from now you'll have a stack you can name, defend, and trust.

That's worth more than any new model launch.

## Frequently Asked Questions

### How many AI tools should I actually use daily?
Three to five tools in daily rotation is the realistic upper bound for most people. My S tier is exactly three, my A tier adds five more for weekly use, and that combined eight is plenty. If you're touching more than ten AI tools every day, you're testing — not working. For my full breakdown of what daily use actually looks like, see the S Tier section above.

### What's the best AI coding stack in May 2026?
Claude Code as your primary agent inside VS Code, with Codex as a secondary agent for reasoning-heavy work and second opinions. The May 2026 Claude Code update added subprocess sandboxing and the Monitor tool for background scripts. GPT-5.5 inside Codex now offers 20x Plus on Pro $200 plans. Both together cost under $300/month and outperform any single-tool setup I've tested.

### Should I use Gemini, Claude, or ChatGPT as my main AI?
None of them — pick the right tool per micro-task. Claude Code for shipping code. Claude Chat for strategic thinking. Codex for reasoning-heavy refactors. Gemini if you live in Google Drive. ChatGPT regular chat is the one I've largely phased out except for occasional GPT-5.5 sanity checks. The "main AI" question is the wrong frame in 2026.

### How do I avoid AI tool overwhelm?
Run a five-question decision framework on every new launch: Does it solve a current pain point? Is the 20% productivity dip worth it? Can I test it in real scenarios within seven days? After seven days, does it earn a daily spot? If yes, what does it displace? This framework alone cuts your tool list dramatically. See "The Decision Framework I Use for Every New Tool" above for the full version.

### What does it mean to use AI tools as a "harness"?
A harness is something you plug into a project, not something you build the project around. My main project directory has clean modular structure and a `CLAUDE.md` context file. Any AI agent — Claude Code, Codex, Hermes — can read that directory and work on it. The agents change. The project doesn't. This is why my workflow survives every tool launch.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
