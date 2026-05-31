**BRAND:** mejba.me
**TITLE:** 16 Claude Hacks for Productivity and Business Growth
**META TITLE:** 16 Claude Hacks That Actually Work (May 2026)
**SLUG:** 16-claude-hacks-productivity-business-growth
**PRIMARY KEYWORD:** Claude hacks for productivity
**META DESCRIPTION:** I tested 16 Claude hacks Dan Martell shared. Here's which ones genuinely changed my workflow, which are demo-ware, and the stack I run daily.
**TAGS:** Claude AI, Productivity, AI Workflow, Claude Cowork, Business Automation

---

# 16 Claude Hacks for Productivity and Business Growth

The first time I watched a productivity guru list out sixteen AI tricks, I rolled my eyes and closed the tab. The second time was different. It was Dan Martell, the Buy Back Your Time guy, walking through sixteen specific Claude features in one sitting. And I'd already been using most of them for months.

So I did the thing I almost never do. I sat down with a notepad open and rated every single hack against my actual workflow. Not "does this look cool in a demo" — does it move the needle when you're shipping work for clients on a Tuesday afternoon and the deadline is six hours out.

What I'm sharing here is that rating. The full sixteen, each one with my real verdict — which ones changed how I work, which ones are well-designed demo-ware that don't survive contact with a real business, and where Claude is genuinely starting to feel less like a chatbot and more like a co-founder. By the end of this, you'll know which hacks to set up tonight, which to skip, and the three-feature stack that makes Claude actually feel like an employee instead of a search box.

A few of these will surprise you. One of them is the thing I now reach for ten times a day and almost nobody outside Anthropic's power-user circle talks about. We'll get there.

## Why I'm Bothering To Rank These At All

Most Claude tutorials in May 2026 fall into two camps. There's the breathless "ChatGPT killer" hype that doesn't survive a real workflow, and there's the academic feature-tour that lists everything Claude can do without ever telling you what's actually worth your time.

I run four brands through Claude. mejba.me ships multiple long-form posts a week. Ramlit takes on engineering retainers. ColorPark handles design. xCyberSecurity runs audits. No employees. One operator. That means every feature gets evaluated on the same brutal question — does this give me leverage, or does it give me another dashboard to babysit?

The 16 hacks below come from Dan Martell's walkthrough. The verdicts are mine. I'll mark each one with one of three tags: **Daily driver** (I use this constantly), **Situational** (genuinely useful but not every day), or **Demo-ware** (looks great in a video, less great in production).

Before we dig in, the boring but important context. As of May 2026, Anthropic's lineup is Opus 4.7 at $5/$25 per million input/output tokens, Sonnet 4.6 at $3/$15, and Haiku 4.5 at $1/$5. Pro is $20/month, Max 5x is $100, Max 20x is $200, Team Standard is $25/seat, Team Premium is $125/seat. Opus 4.7 and Sonnet 4.6 both support 1M token context windows. Hold those numbers in your head — three of the hacks below only make sense once you know what each model actually costs.

Now let's get into it.

## Hack 1: Memory Import — The 90-Second Migration That Pays Off For Months

**Verdict: Daily driver (but only because I did it once and forgot about it).**

This is the hack most people skip and shouldn't. If you've been using ChatGPT for any length of time, it has built up an internal profile of you — your industry, your projects, your communication style, the names of your clients, your weird preference for em dashes over semicolons. All of that is gone the moment you switch tools. Unless you migrate it.

The hack is embarrassingly simple. You open ChatGPT, paste in the prompt "give me a complete export of everything you know about me, my projects, my preferences, my contacts, and my communication style, formatted as a structured document I can import into another AI", then take that output and drop it into Claude as the first message in a new Project. Done.

I tested this with two years of ChatGPT history. The export ran about 4,800 words. I cleaned it up to roughly 2,400 words and pasted it into a Claude Project labeled "About Me". Within a week, Claude started referencing details from that import without me prompting — knowing which brand voice I was switching into, remembering my client roster, picking up on the fact that I prefer practitioner reviews over listicles.

Total time spent: under two minutes. Recurring value: every conversation since.

If you're switching from ChatGPT or running both in parallel, do this tonight. It's the closest thing to a free upgrade I've found in Claude.

## Hack 2: Model Selector — The $80/Month Mistake Most Users Make

**Verdict: Daily driver.**

Most Claude users default to whatever model is selected when they open the app. That's a mistake worth real money over the course of a year. Here's the framework I use after burning more API credit than I'd like to admit in the early days.

**Haiku 4.5** is for volume work where you don't need depth. Summarizing fifty meeting transcripts. Classifying emails. Tagging support tickets. Generating quick variants. At $1 input / $5 output per million tokens, it's roughly five times cheaper than Opus on input and about the same multiplier on output. If your task is "process this list and return structured output", Haiku is almost always the right answer.

**Sonnet 4.6** is the daily driver for actual work. Writing first drafts. Editing. Research synthesis. Conversations that need real reasoning but aren't bet-the-business decisions. I'd estimate 70% of my Claude usage runs on Sonnet. It's the model that does the most invisible heavy lifting in my workflow.

**Opus 4.7** is for the hard stuff. Architectural decisions in a complex codebase. Long-form articles where the quality bar is publication-grade. Strategic analysis where I'd otherwise hire a consultant. The price tag — $5/$25 per million tokens — is real, so I treat it like calling in a specialist. About 15-20% of my Claude usage.

Adaptive mode picks for you, which is great if you're newer to the platform. Once you've been using Claude for a few months and have a sense of when each model shines, switching manually is a small but real optimization. I have my keyboard shortcuts set up to swap models in under a second.

The hack here isn't the existence of the selector. It's the discipline to use it. If you're putting Opus on every single conversation, you're overpaying by roughly 5x. If you're using Haiku for nuanced writing, you're underdelivering on quality. Match the model to the task. It compounds.

## Hack 3: Gmail Connector — Useful But Not Magic

**Verdict: Situational.**

Connecting Claude to Gmail sounds transformative in a demo. In practice, it's a real productivity unlock for two specific use cases and a mild annoyance for everything else.

What it actually does well: bulk triage and draft generation. "Show me every email from a paying client in the last seven days that I haven't replied to" — that query returns in seconds and the answer is correct. "Draft a polite no to the three vendor pitches in my inbox from this week" — perfect. "Summarize what changed in the [Project X] thread since I last checked" — saves me real time.

What it doesn't do well: anything that requires reading deep email history with high recall, or anything that touches threads with mixed sender importance. I once asked it to find a specific contract clause buried in an email exchange from three months back, and it confidently returned the wrong thread. Twice.

The other limitation is that connectors run inside Claude's permissions model, which is fine for security but adds friction for cross-tool workflows. If you need email plus calendar plus a spreadsheet in one query, the connector chain is slower than you'd hope.

I use Gmail Connector roughly twice a day — morning triage and end-of-day reply pass. Worth setting up. Not the headline feature people will tell you it is.

## Hack 4: Calendar Connector — The Underrated Sibling

**Verdict: Daily driver.**

Calendar Connector turned out to be the connector I actually use most, which surprised me because I assumed Gmail would win. The reason is simple — calendar queries are deterministic in a way email queries aren't.

The four queries I run constantly:

1. "What's my next free 90-minute block this week?" — for deep work scheduling
2. "Find me a 30-minute slot with [client name] that works in both our timezones this week" — pulls their public availability via the connector
3. "What does tomorrow look like and what should I prep tonight?" — turns into an end-of-day briefing
4. "How many hours did I spend in meetings last week vs. focused work?" — a quarterly sanity check that I started doing after one particularly nightmarish March

That last one matters more than it sounds. The honest pattern I caught from the data is that whenever my meeting load crosses about 18 hours a week, output collapses. Calendar Connector turned a vague feeling into a number I can actually defend when I push back on a meeting request.

Two-minute setup. Pays back every single day.

## Hack 5: Artifacts — The One Demo-Ware That Earned Its Place

**Verdict: Situational, but creeping toward daily driver.**

I was skeptical of Artifacts when they launched. Generating interactive mini-apps inside a chat felt like the kind of feature that gets a lot of stage applause and then dies in real work. I was about half right.

For most use cases, I don't need a tiny custom app — I need a clean document or a code file. Artifacts are overkill there. But there's a specific class of problems where Artifacts genuinely changed my workflow.

The breakthrough use case for me is editable lookup tables. I had a pricing model that needed to handle six product tiers, three currencies, and four discount rules. I asked Claude to generate it as an Artifact. What came back was a fully editable spreadsheet where I could change any variable and instantly see all downstream calculations update. No Excel formulas. No Google Sheets shared link. Just an interactive object that lived inside my chat and could be exported when I needed it elsewhere.

I now use Artifacts for any deliverable a client needs to interact with — calculators, decision frameworks, quick-reference matrices. Maybe 3-5 per month. Not daily, but high enough leverage that I'd notice if it disappeared.

## Hack 6: Interactive Visuals — Cool, Not Yet Essential

**Verdict: Demo-ware (for now).**

Interactive Visuals are clickable visualizations that link to Slack, files, or other resources. They demo beautifully. In practice, I've used them maybe four times in the past two months.

The issue isn't quality. The visualizations themselves are sharp. The issue is that the situations where I genuinely need a clickable visualization linked to my live data are rare. Most of the time I want a static chart I can drop into a deck or a doc, and Interactive Visuals are overengineered for that.

If you're running a team where someone needs to navigate live dashboards stitched across tools, this might be more useful to you than it is to me. As a one-operator brand, I'd skip it for now and check back in six months.

## Hack 7: Projects — The Most Underrated Organization Feature

**Verdict: Daily driver. This is the foundation everything else sits on.**

I cannot stress this enough — Projects are the single most important Claude feature for anyone using it for real work, and almost nobody talks about it like that. Most coverage treats Projects as "folders for your chats." That description is correct and completely misses the point.

A Project in Claude is a context container. You give it files (style guides, brand docs, past work). You give it instructions (how to write for this brand, what tone to use, what to avoid). And every conversation inside that Project inherits all of it without you having to repeat yourself.

I have separate Projects for each of my four brands. Inside each, the instructions file tells Claude exactly how to write for that brand, what links to reference, what the audience cares about, what mistakes to never make. The result is that I can open a conversation in the mejba.me Project, say "draft me an outline on [topic]", and what comes back already sounds like me. Not because Claude is psychic — because the Project has 8,000 words of context loaded before I type the first word.

If you're using Claude for a business and you don't have at least one well-configured Project, that's where to start tonight. Everything else in this list is roughly 3x more powerful inside a Project than outside one.

For a deeper walkthrough of how I structure these, see [my breakdown of running a business through Claude Cowork in five phases](https://www.mejba.me/claude-cowork-five-phases-business-os).

## Hack 8: Voice Mode — Better Than I Expected

**Verdict: Situational.**

Voice input on desktop and mobile, auto-transcribed into the chat. I was prepared to dismiss this as a gimmick. Then I started using it for a specific use case — talking through a problem I haven't fully thought through yet — and it became one of my favorite ways to start a Claude session.

The pattern I've fallen into: open Claude on my phone while walking, hit voice mode, and just ramble for two or three minutes about a decision I'm wrestling with. The transcript is usually messy. Claude doesn't care — it reads through the rambling, asks two or three sharpening questions, and helps me think more clearly than if I'd sat at my desk and tried to type my way to clarity.

It's not for output generation. It's for thinking. That distinction matters. If you're using voice to generate emails or articles, you'll find the workflow slower than typing. If you're using it to externalize a thought before you've sharpened it, voice is a real unlock.

## Hack 9: Chrome Extension — The Browser Automation Gateway

**Verdict: Daily driver.**

The Claude Chrome extension lets you scrape, analyze, and automate against any web page you're currently on. This is the feature that turned my browser from "research surface" to "data extraction layer".

Concrete uses from the last week:

- Pulled all pricing tiers from six competitor sites and dropped them into a comparison table — about four minutes of work that would have taken thirty manually
- Asked Claude to summarize a 9,000-word industry report I was halfway through reading and tell me which sections were worth my time
- Had it extract every internal link from a competitor's blog so I could see their content cluster structure at a glance

The extension also chains with the rest of the Claude stack, which is the part most people miss. The page you're scraping can be referenced inside a Project. The output can become an Artifact. The whole workflow happens inside one tool.

Install it. Even if you only use it for the "summarize this page and tell me what to skip" workflow, it pays for itself in saved reading time within a week.

## Hack 10: Cowork (Computer Use) — The Real Inflection Point

**Verdict: Daily driver. This is the feature that changed my actual relationship with Claude.**

Cowork is the desktop application that lets Claude directly control your computer — opening apps, clicking buttons, filling forms, running multi-step workflows across tools. It reached general availability on macOS and Windows in April 2026.

I'll be honest about something here. When Cowork was in research preview earlier this year, I tried it and was unimpressed. It was slow, it occasionally got confused, and the failure modes were annoying. I went back to using regular Claude for a few weeks.

Then I tried it again post-GA. The difference was significant. The model is more careful about asking for confirmation before destructive actions. The execution speed improved. And the integration with Skills (which we'll get to) meant I could give Cowork a 12-step workflow once and have it execute that exact workflow reliably from then on.

What I use Cowork for daily:

- Pulling data from three SaaS dashboards into one weekly summary
- Running a multi-step client onboarding sequence (create folder, draft welcome email, generate kickoff doc, populate project tracker)
- Doing the boring 20-minute admin work I used to dread each morning, in roughly three minutes of supervised execution

Cowork isn't perfect. About one in fifteen multi-step workflows still hits an edge case where I need to intervene. But the leverage on the other fourteen is enormous.

For the full breakdown of what Cowork looks like end-to-end, my [Claude Cowork desktop automation walkthrough](https://www.mejba.me/claude-cowork-desktop-automation) has the screenshots and step-by-step.

## Hack 11: Schedule Task — Cron For Your AI Employee

**Verdict: Daily driver. Combined with Cowork, this is what makes Claude feel like a real assistant.**

Scheduled tasks let you tell Cowork to run a workflow on a schedule — every morning at 7 AM, every Friday at 5 PM, every first of the month. They run in the cloud now, which means your laptop doesn't need to be open.

My current scheduled tasks:

- **Daily 6:30 AM**: Pull overnight emails, summarize what's urgent, draft replies to anything obvious, prepare my morning briefing in Markdown
- **Weekly Friday 5 PM**: Generate a week-in-review across all four brands — what shipped, what's blocked, what's the priority for next week
- **Monthly first**: Pull SEO performance data, summarize what's working and what's declining, flag any posts that need refreshing

The first scheduled task took me about forty minutes to set up and tune. Every one after that took ten minutes. The ROI is silly. I'm getting a daily executive briefing assembled by an AI that's read my context, knows my priorities, and adapts the format to whatever I asked for last week.

The honest caveat — scheduled tasks fail occasionally. Not often, maybe once every ten runs. The failure modes are usually a connector hiccup, not a model error. Build a habit of skimming the output before relying on it.

For more on how I structure these, [my scheduled tasks deep-dive](https://www.mejba.me/claude-cowork-scheduled-tasks-automation) has the full setup.

## Hack 12: Claude Dispatch — The Mobile Bridge

**Verdict: Situational, with potential to become daily driver.**

Dispatch is the feature that lets you control Claude Cowork on your desktop from your phone. You pair the mobile app with the desktop app via QR code, and from then on you can fire commands at your computer from anywhere.

I've been using Dispatch for about six weeks. Honest summary — the killer use case for me is queueing up work while I'm out of the office. I'll be at a coffee shop, think of three things I want my desktop to handle, fire them off via Dispatch, and by the time I'm back at my desk, they're done.

What it isn't yet, in my workflow — a remote control for active sessions. I rarely sit in a meeting and reach for Dispatch to do something live. The latency is good but not great, and I'd rather just open my laptop.

The Cowork + Schedule Task + Dispatch combination is what Anthropic seems to be building toward — a persistent AI employee that runs on your machine, executes scheduled work autonomously, and can be redirected from anywhere. As of May 2026, that vision is roughly 70% real. By the end of this year, I'd guess it's 90%.

## Hack 13: Claude Code — The 10x Multiplier I'd Pay 5x More For

**Verdict: Daily driver. This is the most valuable software I've used in a decade.**

I've written close to a quarter-million words about Claude Code at this point, so I'll keep this section short and pointed. Claude Code is the terminal-native version of Claude that turns plain English into working software. It reads your codebase, writes new features, debugs failures, ships pull requests.

I shipped four production features last week using Claude Code, two of which I would have estimated as a full day of work each. Total time spent: about six hours. That's not a one-off — it's the new normal. If you do anything technical and you're not using Claude Code, you're leaving the largest productivity gain in modern software on the table.

The thing most coverage gets wrong is treating Claude Code as a smarter autocomplete. It isn't. It's a junior-to-mid engineer that works at 5x speed, never gets tired, and asks clarifying questions when it's unsure. The mental model that unlocked this for me was treating it like a teammate I delegate to, not a tool I use.

For the full unpacked workflow, see [my 32 Claude Code power-user hacks](https://www.mejba.me/claude-code-32-power-user-hacks) — it's the post I send to anyone who asks me where to start.

## Hack 14: Claude Channels — The Underused Bridge

**Verdict: Situational.**

Channels connect Claude Code to messaging platforms — iMessage, Telegram, Discord. The pitch is that you can fire commands at your AI engineer from anywhere, and the output comes back to you in the channel you're already living in.

I set this up last month for iMessage. Verdict so far is "useful but not transformative." The use case where it shines is asking Claude to do something while I'm away from my desk and not wanting to wait until I get back — "kick off the test suite", "draft a reply to this client thread", "summarize what changed in the repo in the last 24 hours."

Where it falls down is anything that requires me to see the full output. Reading a long response in iMessage is painful. So I use Channels almost exclusively for fire-and-forget commands that don't need a deep review.

If you live in a particular messaging app and want Claude embedded there, set it up. If you're already on your laptop most of the day, it's lower priority.

## Hack 15: Claude Skills — The Productivity Multiplier Most People Miss

**Verdict: Daily driver. This is the most under-discussed feature in the entire stack.**

Skills are reusable, named workflows that you teach Claude once and invoke forever. Think of them as functions for your AI. You write a Skill called "weekly SEO report" and define exactly what data to pull, how to analyze it, what format to output, and what to flag. From then on, you just say "run the weekly SEO report" and Claude executes the entire workflow.

I have roughly 30 Skills set up across my brands. Some examples:

- **brand-voice-mejba** — applies my mejba.me voice rules to any draft
- **client-onboarding** — handles the full kickoff sequence end-to-end
- **seo-content-audit** — pulls a page, scores it against a 50-point rubric, returns actionable fixes
- **refresh-old-post** — identifies a stale post, finds what's outdated, suggests surgical edits

The reason Skills is the under-discussed feature is that it's the layer that turns Claude from "smart text box" into "system you actually run a business on." A Skill is institutional knowledge encoded into the AI. The more Skills you build, the more your business is running on a system you defined rather than on whatever Claude decides to do that day.

Anthropic launched a Skills marketplace earlier this year — legal, financial, HR, writing categories. The marketplace skills are a decent starting point. The real value is building your own.

If you only do one thing from this entire post, build three Skills for your three most-repeated workflows. The ROI compounds within a week.

For a deeper walkthrough, see [my Claude Code Skills guide](https://www.mejba.me/claude-code-agent-skills-guide).

## Hack 16: Claude Design — Cautiously Optimistic

**Verdict: Situational, watching it closely.**

Claude Design is the newer offering — an AI design tool for pitch decks, one-pagers, landing pages, motion graphics, with Adobe integration on the way. I've been testing it for about a month.

Honest assessment as of May 2026: It's the most promising "AI design tool" I've used, and it's still not where it needs to be for high-end client work. For internal decks, quick one-pagers, and rapid iteration on layout ideas, it's genuinely useful. For pitch decks I'd send to a client paying $20K for a brand identity, I'm still in Figma.

The trajectory is what matters here. Claude Design six months from now is probably going to be significantly better, and the Adobe integration is a real moat once it ships. I'd put this on the watchlist rather than the daily-driver list, but I expect that to shift before the end of the year.

For now, my [comparison of Claude Design against Google Stitch](https://www.mejba.me/google-stitch-vs-claude-design-tested) has the unfiltered side-by-side.

## The Three-Feature Stack That Actually Changed Everything

If I had to pick three of these sixteen hacks to keep and throw the rest away, I'd keep Projects, Skills, and Cowork. In that order.

Projects give you context. Without context, every Claude conversation starts at zero. With it, every conversation starts at "I know who you are, what you're building, and how you write." That's the foundation everything else sits on.

Skills give you leverage. They convert one-time prompts into reusable workflows that compound over time. The 30 Skills I've built represent maybe 80 hours of upfront investment and they save me roughly that many hours every single month.

Cowork (plus Scheduled Tasks plus Dispatch) gives you autonomy. This is where Claude crosses the line from "tool I use" to "employee I delegate to". The other features in this list make Claude more capable. This combination is what makes Claude operational.

The other thirteen hacks are real and useful. But if you're building a system rather than collecting features, those three are the foundation. Build those first, deeply, before adding anything else. Most people get this exactly backward — they bolt on connectors and try voice mode and experiment with Artifacts, while leaving the Project file empty and never writing a single Skill.

Don't make that mistake.

## What I'd Tell Someone Starting Tomorrow

If you're sitting at zero today and want to be operational by next week, here's the order I'd run.

**Tonight (60 minutes):** Memory import from ChatGPT. Set up your first Project with a basic instructions file and 2-3 reference documents. Pick your model defaults — Sonnet for daily, Haiku for volume, Opus for hard.

**This week (3-4 hours total):** Connect Gmail and Calendar. Install the Chrome extension. Run Cowork on your first repeatable workflow — pick the boring 20-minute admin task you hate most. Get one Skill built and working end-to-end.

**Next two weeks (compound investment):** Add Scheduled Tasks for your daily briefing. Build your second and third Skills. If you write code, install Claude Code and ship one feature with it. Set up Dispatch if you spend real time away from your desk.

**Month two:** Look back at what you actually used and what you didn't. Cut the features you're not touching. Deepen the ones you are. By the end of month two, you should have a system that runs itself for at least two hours of work per day with you in supervisor mode rather than operator mode.

That's not a hypothetical timeline. That's the path I ran, compressed and cleaned up after watching enough other people stall in the wrong order.

## The One Question Worth Sitting With

Here's what nobody told me about working with Claude this seriously for the last year. The hardest part isn't the features. It's the shift in how you think about your own work.

When you have a real AI assistant, the question stops being "how do I do this faster?" and becomes "should I be doing this at all, or should the AI be doing it?" That's a harder question. It forces you to look at what you're actually good at versus what you're just used to doing. It exposes the work you've been hiding behind.

The sixteen hacks above are tactical. Set them up. Run them. They work. But the real return — the part that compounds for years — is when Claude becomes the surface that forces you to answer the bigger question.

So tonight, after you set up the Memory Import and your first Project, ask yourself one thing. Of everything you did today, which 30% should have been delegated to an AI an hour into the morning? Make that list. Tomorrow, build a Skill for the first item on it.

That's where this stops being a tool stack and starts being a different way of operating.

## Frequently Asked Questions

### What are the best Claude hacks for productivity in 2026?
The three highest-leverage Claude hacks are Projects (for persistent context), Skills (for reusable workflows), and Cowork combined with Scheduled Tasks (for autonomous execution). These three turn Claude from a chatbot into an operational system. For the full breakdown of how to combine them, see The Three-Feature Stack section above.

### Which Claude model should I use for daily work?
Use Sonnet 4.6 for most daily tasks at $3/$15 per million tokens — it handles writing, research, and reasoning at roughly 70% of the workload. Reserve Opus 4.7 ($5/$25) for complex strategic work and Haiku 4.5 ($1/$5) for high-volume processing. Matching the model to the task can cut your costs by 3-5x.

### Is Claude Cowork worth using over the regular Claude app?
Yes, if you have repeatable workflows you can automate. Cowork lets Claude directly control your computer, which combined with Scheduled Tasks and Dispatch turns it into a persistent AI assistant. For one-off conversations, the regular Claude app is fine. For running a business, Cowork is the inflection point.

### What is Claude Dispatch and how does it work?
Dispatch is the mobile bridge that lets you control Claude Cowork on your desktop from your phone via a QR code pairing. You queue commands from anywhere, and your desktop Claude executes them. It's most useful for firing off work while away from your office that you want completed by the time you return.

### Are Claude Skills worth building?
Yes — Skills are the single most under-discussed productivity multiplier in the Claude stack. Each Skill encodes a repeated workflow once and lets you invoke it forever. Building 3-5 Skills for your most-repeated tasks typically returns 10-20 hours per month in saved time within the first few weeks.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
