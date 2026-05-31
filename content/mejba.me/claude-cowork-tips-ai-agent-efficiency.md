**BRAND:** mejba.me
**TITLE:** 12 Claude Co-work Tips That Quietly Doubled My Output
**META TITLE:** Claude Co-work Tips: 12 Ways to Run a Lean AI Agent
**SLUG:** claude-cowork-tips-ai-agent-efficiency
**PRIMARY KEYWORD:** Claude Co-work tips
**META DESCRIPTION:** 12 Claude Co-work tips I learned the hard way — folder strategy, claude.md, Skills, Connectors, Dispatch, off-peak scheduling, and what to prune.
**TAGS:** Claude Co-work, AI Productivity, AI Agents, Workflow Automation, Tutorial

---

The first month I ran Claude Co-work seriously, I burned through my entire weekly limit by Wednesday morning.

I had instructions stuffed into four different Projects. I had skills enabled that I'd never used. I had three connectors live that I'd authorized "just to try them." I was running long, chatty sessions during what turned out to be peak demand on the US East Coast. And I was asking Co-work to generate slide decks inside the same conversation where I'd already burned 80,000 tokens organizing files.

The output was good. The cost — measured in usage hits and wasted hours — was embarrassing.

The fix wasn't a better prompt. It was a structural reset. I tore the whole setup down, rebuilt it around twelve specific patterns, and now I run roughly the same amount of work on roughly half the limits. None of these are exotic. Most of them are obvious in hindsight. But every single one was a lesson that cost me something to learn.

If you're paying for Claude Pro or Max and using Co-work as a glorified ChatGPT clone, this is the article I wish someone had handed me on day one.

## Why Most Co-work Setups Quietly Bleed Tokens

Claude Co-work shipped to general availability on macOS and Windows on April 9, 2026, and the adoption curve has been the steepest of any Anthropic product so far. New users get the desktop app, click around, and immediately start replicating their ChatGPT habits — long conversations, instructions pasted directly into Projects, every connector turned on because why not.

That habit was the entire reason I was hitting limits early. Co-work isn't a chat interface that happens to touch files. It's a workspace execution engine where every architectural choice — where your instructions live, how your folders are structured, which skills are loaded, what time of day you run heavy tasks — directly affects how many tokens you burn and how much real work you ship.

Most people I've coached through this had the same three problems I had. Their Projects were doing too much. Their skills sprawl was eating context on every prompt. And they treated dispatch and scheduling like marketing features instead of cost-control tools.

Each tip below maps to one of those failure modes. Read them in order — they build on each other. The boring-sounding ones at the start unlock the powerful ones at the end.

## Tip 1: Treat Projects as Folders, Not as Instruction Containers

The biggest mistake I made early was treating Co-work Projects like a place to store everything — files, instructions, context, references, the works.

It feels right. Anthropic's UI even encourages it. You create a Project called "Client Work," paste in a long block of "You are a senior consultant who…" instructions, drop in your reference files, and start chatting. For one project, this is fine. For ten projects across two clients and three personal initiatives, this is how you end up rewriting the same paragraph of instructions twelve times — and forgetting which copy is the canonical one.

I now treat Projects as pure organizational containers. Folders for chats. Nothing more. The Project name tells me what work the chats inside relate to. That's the only job a Project has in my setup.

The reason this matters has nothing to do with aesthetic preference. It's portability. The same instructions I use in Co-work I also use in Claude Code for development work. Some I use in Claude Design when I'm building decks. If those instructions live inside a Co-work Project, they're trapped there. The moment I switch tools, I'm copying and pasting again — and inevitably drifting between versions.

Pull instructions out of Projects. Put them in files. The next tip explains where.

## Tip 2: Move Every Instruction Into a claude.md File

Anthropic's own documentation now treats `claude.md` as the canonical place to write persistent project context, and once you've worked this way for a week, going back feels barbaric.

A `claude.md` file is a plain markdown text file you drop into a folder on your computer. Co-work reads it automatically whenever you point a task at that folder. Anything you want Claude to remember — tone, conventions, file naming rules, the way you like your tables formatted — goes in there. One file. One source of truth. Portable across tools.

Here's the rule I follow: target under 200 lines per `claude.md` file. Longer than that and the file starts eating context budget on every single prompt, even when you only need a tiny fraction of what's in it. Use markdown headers and bullets, not dense paragraphs. Be concrete. "Use 2-space indentation" is a usable instruction. "Format things nicely" is not.

My setup has a top-level `claude.md` in my main work folder that covers global rules — tone, the brands I write for, file naming conventions. Each sub-folder has its own narrower `claude.md` for that specific context. When I open a task pointed at the sub-folder, Co-work picks up both files automatically. When I open a task pointed at the parent, it just gets the global one.

This is also a cross-tool benefit. The same `claude.md` patterns I use in Co-work map almost one-to-one onto Claude Code projects. I wrote about that overlap in my [Claude Code workflow guide](https://www.mejba.me/claude-code-32-power-user-hacks), and the same logic applies here. Instructions belong in files, not in product-specific UIs.

If a piece of context is worth giving Claude, it's worth keeping outside Anthropic's walled garden.

## Tip 3: Attach Multiple Folders to a Single Task

This one I missed for weeks. I knew I could attach a folder to a Co-work task. I didn't realize I could attach several.

When you start a new task in Co-work, there's a folder picker. Most people pick one. But the picker accepts multiple selections, and the AI is smart enough to pull only the relevant files from each location without getting overwhelmed.

Why this matters in practice. My weekly newsletter pulls from three folders: my content archive, my research notes, and my analytics exports. Before I knew about multi-folder attachment, I'd either copy files between folders (gross) or run three separate tasks and stitch the output (slow). Now I attach all three folders to one task, write a single prompt, and Co-work handles the cross-referencing inside one conversation.

The key word is *relevant*. Co-work doesn't ingest every file in every folder you attach. It scans, picks what the task needs, and works from there. That means you can attach broader folders without worrying about token bloat — the model decides what to actually read. I've attached a 400-file research archive alongside a current-project folder and it pulled exactly the four references that mattered.

The catch — and there's always a catch — is that more folders also mean more places for sensitive files to live. If you're attaching a client folder alongside personal files, double-check what's inside. Co-work is going to read whatever's there.

## Tip 4: Set a Default Folder So Every New Task Starts Right

This is a one-time setting that's saved me at least an hour a week.

In Co-work settings, you can specify a default parent folder for new tasks. Pick the folder where 80% of your work happens — for me, that's my main `Claude-Work` directory. Now every new task auto-opens with that folder as its context. No fumbling through file pickers. No accidentally starting a task with no folder attached and getting context-free responses.

You can still override it per task. If I'm doing one-off research that doesn't belong in my main folder, I just swap the folder before sending the first message. But the default-folder behavior means the common case is friction-free, and only the exceptions require thought.

This pairs perfectly with the `claude.md` strategy. If your default folder has a well-written `claude.md`, every new task starts with the right context loaded automatically. Zero setup overhead. The AI knows who you are, what you're working on, and how you like things done — before you've typed a single word.

## Tip 5: Know What Dispatch Can and Cannot Do

I tested Dispatch hard for two weeks before I figured out where it actually belongs in my workflow.

Dispatch is the feature that lets you message your Co-work session from your phone while your computer keeps running the work. You pair them with a QR code, and now you've got a remote control for Co-work in your pocket. Fortune ran a great deep-dive on it in late April, calling out exactly the limitations I want to flag here.

Three things to know before you build a workflow around it.

First, your computer has to stay on and Co-work has to stay running. Dispatch isn't a cloud service — it's a remote interface to your desktop session. Laptop closed, laptop asleep, Co-work quit, anything that interrupts the desktop session also kills Dispatch. I learned this the hard way trying to trigger a task from the train while my laptop was in my bag at home, lid closed. Dispatch told me the session was unreachable. The MacBook had gone to sleep three minutes earlier.

Second, it's a single continuous conversation. You don't get the full Project tree from your phone. You get one ongoing thread that maintains context across messages. For quick prompts — "Pull yesterday's analytics and draft the summary" — this is perfect. For complex multi-Project workflows where you need to switch between contexts, it's the wrong tool.

Third, it works best for simple tasks. Drafting an email, kicking off a content draft, asking for a status check on a long-running task, organizing a folder you remembered while in line for coffee. Anything that needs deep file manipulation or precise multi-step coordination is better triggered from your desktop where you can see what's happening.

The mental model that finally clicked for me: Dispatch is the SMS interface to Co-work. Great for short, decisive requests. Wrong for nuanced work. Set expectations accordingly and it earns its place. Expect a full mobile experience and you'll be frustrated within a day.

## Tip 6: Use Voice Dictation for the Messy First Draft

Command + D toggles dictation on macOS. That's the entire onboarding for what's quietly become my favorite Co-work input method.

Here's the thing about typing prompts. We unconsciously try to structure them. We add transitions. We over-explain. We waste tokens being "clear" in a way that's actually just verbose. Voice dictation, weirdly, fixes this. When I'm talking through a problem, I ramble in a more direct way than I write. The AI then organizes my rambling into a coherent output, which it turns out is exactly the right job for an LLM.

My pattern looks like this. I open a new task. I hit Command + D. I talk for ninety seconds about what I want — the messy version, the way I'd describe it to a colleague over coffee. Stuff like: "Okay so I need to put together the post on Claude Co-work tips, here are the twelve tips, the brand voice is first-person, I want it to feel like I'm telling someone what I wish I'd known, focus on the structural stuff in the first half and the advanced stuff later, oh and make sure to mention the 5 to 11 AM PST off-peak window because that's a big one." Hit send. Co-work converts the ramble into a structured plan and asks the clarifying questions I'd have missed if I'd tried to write the prompt cleanly.

It's faster than typing. It's more thorough than typing. It produces better prompts than typing. And it costs zero extra tokens because the dictation is just transcription.

This is also where dictation pairs with the `claude.md` setup from earlier. When your folder has solid persistent context, you can dictate sloppy prompts and still get sharp output, because the standing instructions fill in the gaps you didn't articulate.

## Tip 7: Schedule Heavy Tasks for Off-Peak Hours

This is the single biggest cost-saving tip in this article. If you read nothing else, read this.

Claude's usage limits reset on a rolling five-hour window. But the effective generosity of those limits varies by time of day, because Anthropic throttles harder during peak demand. As of early May 2026, peak hours on Anthropic's infrastructure run roughly 8 AM to 2 PM Eastern Time — that's 5 AM to 11 AM Pacific. Outside that window, on weekdays, you get noticeably more headroom. On weekends, the more generous limits apply around the clock.

There's been some good news on this front. On May 6, 2026, Anthropic announced that peak-hour throttling has been eliminated entirely for Pro and Max subscribers, with the 5-hour rate limits effectively doubled for both tiers. That softens the urgency of this tip a bit for paid users. But the underlying logic still holds — server load is real, latency is worse during peak demand, and queueing long-running tasks during the quiet window still gives you the best experience.

Co-work has a Scheduled Tasks feature on Pro and Max plans. You give it a prompt, attach the connectors and folders it needs, and tell it when to run. The task fires at the scheduled time, runs to completion, and the output is waiting for you when you check in. The compounding move is to schedule heavy generative work — newsletter drafts, weekly research summaries, deep analyses of accumulated files — to run overnight or pre-dawn, when the infrastructure is quiet and you're asleep anyway.

My current schedule has three recurring tasks: a 4 AM newsletter research pull, a 5 AM content idea generation against the prior day's saved notes, and a Sunday-night weekly retrospective that consolidates everything I worked on. By 7 AM Monday, I've got three substantial outputs sitting in my outputs folder, ready to refine. None of them ate into my morning working session.

I covered the scheduling pattern in more depth in my [Claude Co-work daily workflow system](https://www.mejba.me/claude-cowork-daily-workflow) post — the recurring tasks are the backbone of how I run the brand.

## Tip 8: Replace Long Instructions With Skills

Once you've moved instructions into `claude.md` files (Tip 2), you're going to hit the next wall: those files start to get long. You'll want to add instructions for slide decks, and instructions for newsletters, and instructions for client briefs, and instructions for analytics reports, and suddenly your top-level `claude.md` is 800 lines and burning context on every prompt — most of which the current task doesn't need.

Skills fix this. Skills are modular, on-demand instruction packs that Co-work loads only when the task actually needs them. The slide-deck skill loads when you're building a deck. The newsletter skill loads when you're drafting a newsletter. Each one stays out of context the rest of the time.

The structural shift is this. Your `claude.md` becomes lean — only the always-relevant context lives there. Tone. Brand voice. File naming conventions. Things that apply to every task. Anything task-specific (how to format a deck, how to structure a research brief, how to outline a YouTube video) gets pulled into a Skill that loads only when invoked.

In practice this gives you two wins. First, every prompt is cheaper because you're not loading 800 lines of context for a task that needed 80. Second, your specialized instructions get sharper because they live in their own file and don't have to coexist with twenty other unrelated rules.

I've written separately about [how I structure Claude Skills for advanced workflows](https://www.mejba.me/agent-skills-advanced-claude-code) — the patterns transfer almost directly from Code to Co-work, since Anthropic uses a similar Skills concept across both products.

The rule of thumb: if an instruction set is task-specific and longer than 20 lines, it belongs in a Skill, not in your `claude.md`.

## Tip 9: Use Connectors Intentionally, Not Aspirationally

Co-work now supports 38+ connectors — Gmail, Google Calendar, Notion, HubSpot, Slack, Drive, Canva, the whole modern productivity stack. The temptation when you first see the list is to authorize all of them. Resist that temptation.

Every active connector adds a small amount of overhead to every Co-work session. The agent has to know what tools are available, what each tool can do, and which to reach for. When that surface area is small and curated, the agent picks the right tool quickly. When it's bloated with five connectors you authorized three weeks ago and never used, the agent has to think harder about every routing decision.

My current active set is four connectors. Gmail (for drafting and triaging email). Google Calendar (for scheduling and availability checks). Notion (where my actual knowledge base lives). Google Drive (where output files land). That's it. Everything else got disconnected.

The discipline I'd recommend is to add connectors only when you have a specific recurring workflow that needs them. "I want to try the HubSpot connector" is not a reason. "Every Monday I need Co-work to pull last week's deal-stage changes and summarize them" is a reason. Authorize the connector when the workflow is concrete, not when the connector sounds interesting.

There's a tip-10 pairing here that compounds the value: when Co-work *can* delegate work to a connector instead of generating it from scratch, that's almost always the cheaper move. Pulling structured data from Notion costs less than asking Claude to remember what you told it last week.

## Tip 10: Offload Media Creation to Claude Design

This was the unlock I didn't see coming. For the first month I was using Co-work to draft slide decks. It worked — barely — and it ate massive amounts of usage every time. Slide generation is generation-heavy. Every layout decision, every text block, every visual element burns tokens.

Then Anthropic shipped Claude Design on April 17, 2026, and it changed my math entirely.

Claude Design is a separate Anthropic Labs product, included with the Pro, Max, Team, and Enterprise subscriptions you're already paying for. It's purpose-built for visual work — branded websites, pitch decks, one-pagers, motion graphics, launch videos, even interactive prototypes. Powered by Opus 4.7, with deep design-system awareness that learns your colors, typography, and components on first use.

Here's the cost play. Slide decks I used to draft in Co-work — burning Co-work usage — now get built in Claude Design. The Co-work session writes the outline and the content. Claude Design handles the visual execution. Two products, two pools of usage, one project shipped without exhausting either limit.

The export options matter too. Claude Design exports to PPTX, PDF, Canva, and standalone HTML. So nothing's trapped in the tool. The deck comes out, lands in my outputs folder, and Co-work picks it up from there if there's downstream work to do.

If you're paying for the subscription, you already have access. The question is whether you're actually using it. Most people I've talked to didn't realize Claude Design existed, or assumed it was a separate paid upgrade. Check your account. The login uses the same credentials. You're leaving leverage on the table if you're not splitting media work out of Co-work.

## Tip 11: Use Obsidian as Your File Viewer

This one isn't about Co-work directly. It's about what happens to the files Co-work generates after they land on your disk.

Co-work outputs markdown by default. Markdown files for content drafts. Markdown for notes. Sometimes HTML for sharable artifacts. Sometimes structured text for tables and reports. The default macOS or Windows file viewer is fine for opening these one at a time, but it falls apart fast when you've got fifty files in an outputs folder and you want to triage them quickly.

Obsidian is what fills that gap. It's a free, cross-platform markdown app that treats a folder of files as a vault. Point it at your Co-work outputs folder and now every file Co-work generates is instantly viewable, searchable, and linkable in a unified interface. You can open three files side-by-side. You can search across the entire folder for a phrase. You can preview rendered markdown without losing the source.

The workflow this enables is something like: Co-work runs a scheduled morning task and drops six markdown files in my outputs folder. I open Obsidian. I skim all six in five minutes. I tag the two I'm going to ship, archive the rest, and move on. Total triage time, low single-digit minutes.

There are other markdown viewers. Typora is solid. Bear is fine if you live in Apple's ecosystem. The reason I'd point first-time users to Obsidian specifically is the vault-based mental model — it treats folders the way Co-work treats folders, which keeps your mental architecture consistent across both tools.

## Tip 12: Prune Skills and Connectors Monthly

Everything in this article that you set up — folders, `claude.md` files, Skills, Connectors — accumulates over time. The setup that was lean and intentional in month one becomes bloated by month four because you kept adding without taking anything away.

Once a month, I run a fifteen-minute audit. I look at my enabled Skills and ask which ones I've actually invoked in the last thirty days. The ones I haven't get disabled. I look at my authorized Connectors and ask which ones have been read or written to in the last thirty days. The ones that haven't get disconnected. I look at my `claude.md` files and skim for any instructions that no longer reflect how I actually work.

This sounds obsessive. It isn't. It's the difference between a Co-work setup that gets faster and sharper over time and one that gradually accumulates entropy until you're back where I was in month one — burning the weekly limit by Wednesday because every prompt drags ten unused systems along for the ride.

The pattern that surprised me: Skills I thought were essential turned out to be invoked maybe twice a quarter. They were sitting in context budget the whole time, costing me on every prompt, for occasional use that I could just as easily handle ad-hoc. Disabling them improved both speed and cost without affecting output.

Treat your Co-work setup like a garden. Some things grow, some things die. Prune accordingly.

## The Honest Limitations Nobody Talks About

I owe you a real-talk section. Three things that I haven't seen written about clearly enough in the existing Co-work coverage, and that you'll hit eventually if you use the tool seriously.

The first is that Co-work still has a hard ceiling on how long a single session can productively run. After about ninety minutes of continuous heavy use, output quality starts to drift. The model gets less precise. It forgets earlier instructions. Whether this is a context-window thing, a memory-decay thing, or just LLMs being LLMs, the practical advice is the same: end sessions deliberately rather than letting them sprawl. Save outputs, start fresh.

The second is that the Skills marketplace is still thin. Anthropic seeded it with a strong starter set — research, briefings, slide decks, note-taking — but if your workflow is unusual, you're probably writing your own Skills rather than installing community ones. That's not a problem if you're technical enough to write them. It is a problem if you were expecting an App Store of pre-built workflows.

The third is that Dispatch, as great as it is for short remote prompts, has a frustrating habit of dropping the session if your desktop machine sleeps for even a moment. I've watched this happen mid-train-ride twice. The fix is to configure your laptop to never sleep when plugged in, but that's a non-default setting that nobody tells you to change on day one.

None of these are dealbreakers. All of them are real. If you're going to invest in Co-work as a primary tool — and I think you should — go in knowing where the rough edges are.

## What This Looks Like When the System Is Working

The endpoint of all twelve tips, working together, is a workspace where you barely notice the AI is there until you need it.

In my current setup, I open my MacBook in the morning. Co-work is running in the background — it didn't shut down overnight because I configured it not to. Three scheduled tasks have already run in the small hours. Three markdown files are waiting in my outputs folder. I open Obsidian, skim them, pick the two I want to ship today.

I open a new Co-work task. It opens against my default folder. The `claude.md` there gives it global context. The folder's sub-`claude.md` gives it the specifics for whatever I'm working on. I hit Command + D and dictate a sloppy ninety-second prompt. Co-work converts it into a clean plan, executes it across the folders I attached, and uses only the Skills it actually needs.

If I need a slide deck out of the output, I switch to Claude Design and feed the draft in. If I need to trigger a quick follow-up from my phone later, I use Dispatch.

The whole thing feels less like operating a tool and more like working alongside someone who's already loaded all my context, knows my preferences, and quietly handles execution while I make the decisions that actually require a human.

That's the destination. Twelve tips. Maybe a weekend of setup. The compounding gains start the following Monday and don't stop.

If you're going to do one thing today, set up a single `claude.md` file in your main work folder and put your default Project's instructions in it. Move those instructions out of any Projects you've stuffed them into. Run a few tasks from there for a week. Then come back to this list and pick the next tip.

The leverage isn't in any one of these patterns. It's in the way they compound.

## Frequently Asked Questions

### What's the difference between Claude, Claude Code, and Claude Co-work?
Claude (web/mobile) is the chat interface for general conversation. Claude Code is the terminal-based development tool for production software work. Claude Co-work is the desktop workspace agent for daily productivity tasks — files, emails, scheduling, content. They share an account and credentials but serve different jobs. Most people I work with use all three.

### Do these Claude Co-work tips work on the free plan?
Most don't. Scheduled Tasks and Dispatch require Pro or Max. Skills are available across plans but with throughput limits on free. Multi-folder attachment and `claude.md` work everywhere. If you're on free, you'll hit usage limits before these tips compound meaningfully — Pro at minimum is the realistic floor for the workflow described here.

### When are Claude Co-work off-peak hours?
Off-peak hours run roughly 5 AM to 11 AM Pacific Time on weekdays (outside the 8 AM to 2 PM Eastern peak window). Weekends operate on more generous limits all day. As of the May 6, 2026 update, Pro and Max subscribers no longer face explicit peak-hour throttling, but server latency is still lower outside peak hours, so scheduling heavy tasks for off-peak windows is still the smart move.

### How long should a claude.md file be?
Under 200 lines is the working ceiling. Longer files consume more context budget on every prompt and the model starts to lose adherence to instructions buried deep in the file. If you're approaching 200 lines, split task-specific content into Skills and keep `claude.md` for global rules only.

### Is Claude Design included with my Claude subscription?
Yes, for Pro, Max, Team, and Enterprise subscribers. Claude Design launched April 17, 2026 in research preview and uses the same account credentials as Co-work. If you're already paying for a Pro or higher plan, you have access right now — most people just don't realize they do.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
