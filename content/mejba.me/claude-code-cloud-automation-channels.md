**BRAND:** mejba.me
**TITLE:** Claude Code Cloud Automation and Channels: Tested
**SLUG:** claude-code-cloud-automation-channels
**PRIMARY KEYWORD:** Claude Code cloud automation
**SECONDARY KEYWORDS:** Claude Channels Telegram, Claude Code scheduled tasks, Claude Code Discord bot
**META DESCRIPTION:** I tested Claude Code's new cloud automation and Channels features. Here's how scheduled tasks, Telegram bots, and voice control actually work in practice.
**TAGS:** Claude Code, AI Automation, Cloud Computing, Telegram Integration, Hands-On Review
**CONTENT TYPE:** Practitioner Review
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will be able to set up both cloud-scheduled coding tasks and a working Telegram/Discord channel for remote Claude Code interaction, understanding exactly where each feature shines and where it falls short.

---

My laptop was closed. Lid down. Sitting on my desk at 3 AM while I slept. And when I woke up, Claude had already reviewed my pull request, flagged two issues in a utility function I'd pushed at midnight, and left a summary in my Slack channel.

That shouldn't have been possible a month ago.

Claude Code's cloud automation — the ability to schedule recurring tasks that actually run without your machine being awake — has been the single most requested feature since Anthropic shipped scheduled tasks in Cowork. The local-only limitation was the elephant in the room. You could schedule a daily code review, sure, but only if your MacBook stayed open with the lid up and the desktop app running. Miss a sleep cycle, and your "automated" workflow just... didn't run.

That limitation is gone now. And the timing of this release alongside Claude Channels — which lets you control Claude Code sessions from Telegram and Discord — creates something that feels like a genuine inflection point. Not because either feature is individually earth-shattering, but because together they solve a problem I've been hacking around with janky workarounds for months: reliable, remote, always-on AI coding assistance.

I spent the last week testing both features across three active projects. I scheduled cloud tasks, set up a Telegram bot, sent voice notes to Claude from my phone at a coffee shop, and deliberately tried to break everything. Here's what actually works, what doesn't, and the specific setup that made the difference between "neat demo" and "this is now part of my daily workflow."

There's a trick with the `.claude-md` configuration that makes cloud-scheduled tasks dramatically smarter — I'll get to that when we hit the implementation section, because it changes the quality of output more than the model selection does.

## The Problem Cloud Automation Actually Solves

If you've been using Claude Code's [scheduled tasks in Cowork](/claude-cowork-scheduled-tasks-automation), you already know the power and the frustration.

The power: you describe a task once — "review all PRs opened today and summarize issues" or "scrape competitor pricing and update the tracking spreadsheet" — pick a cadence, and Claude runs it automatically. Each execution loads your full project context, reads your configuration files, and operates with the same intelligence as a live session.

The frustration: all of it depended on your physical machine. Scheduled tasks in Cowork only run while your computer is awake and the Claude Desktop app is open. Close the lid, let the machine sleep, lose power during a storm — and your "automated" pipeline silently skips its run. According to the Claude Help Center documentation, when your machine wakes back up, Cowork checks the last seven days for missed runs and triggers one catch-up execution. Better than nothing. But "catch-up at some random point when your laptop comes back online" is not the same as "runs at 6 AM every morning regardless."

I learned this the hard way. I had a scheduled task running daily at 7 AM to organize my document inbox — client contracts, invoices, project briefs — and it worked beautifully for a week. Then I traveled for a conference. Laptop in my bag, lid closed, for three days. When I opened it at the hotel on day three, Cowork ran one catch-up session and processed everything in a single batch. The report it generated was a mess because it tried to sort seventy-two documents in one go instead of the usual eight to twelve.

Cloud automation fixes this by moving the execution environment off your machine entirely.

## How Cloud Automation Works (The Architecture That Matters)

The cloud scheduling interface lives at claude.ai/code — not in the desktop app, not in the terminal. This distinction is intentional. When you create a cloud-scheduled task, you're telling Anthropic's infrastructure to execute that task on their servers at the specified time, against your connected repositories.

Here's the mental model that clicked for me after some initial confusion: think of it as GitHub Actions that speaks Claude. You select a repository, define the prompt (what you want Claude to do), set the schedule, and optionally configure where results get delivered — Slack, email, or both. The task runs in a cloud sandbox with access to your repo contents, and Claude operates with the same intelligence it would in a local session, minus direct filesystem access to your local machine.

That "minus" is worth pausing on. Cloud-scheduled tasks can read and analyze your repository code, generate reports, open PRs, leave code review comments, and push changes to branches. What they can't do is access files on your laptop, interact with local MCP servers, or use tools that depend on your local environment. If your workflow needs to read a local database or access a service running on localhost, cloud automation won't work — you need desktop scheduled tasks with the machine awake.

For the majority of what I actually schedule, though? Repository operations cover it. Code reviews, dependency audits, changelog generation, data scraping from public sources, daily standup summaries built from commit history. None of that needs my local filesystem.

The scheduling granularity is solid: daily, weekly, weekdays only, hourly, or custom cron expressions for those of us who think in `0 6 * * 1-5` format. Each task gets its own execution context, and you can assign different models to different tasks. I run quick repo checks on Sonnet 4.6 because speed matters more than depth for "did any new PRs come in overnight?" questions. My weekly architecture review runs on Opus because it needs to synthesize across multiple files and reason about design patterns.

One thing that surprised me — each cloud task execution appears in your claude.ai/code dashboard with a full transcript. You can see exactly what Claude did, what files it read, what conclusions it reached, and what output it produced. This isn't a black box. When my daily code review flagged something I disagreed with, I could trace the exact reasoning chain and adjust my prompt accordingly. That feedback loop is what separates "set and forget" from "set, monitor, and improve."

## Claude Channels: Your Phone Becomes a Terminal

Here's where things shift from "useful background automation" to "I'm controlling my coding agent from the grocery store checkout line."

Claude Channels, announced on March 20, 2026 as a research preview, connects a running Claude Code session to messaging platforms. Telegram and Discord shipped first, with more platforms coming through the plugin architecture. When you send a message through Telegram, it enters your running Claude session as an event. Claude can write code, run tests, fix bugs, and send the response back through the same channel.

The architecture is different from cloud automation in a crucial way. Channels don't run in the cloud. They relay messages to a Claude Code session running on your machine. Your terminal needs to stay open, your machine needs to stay awake, and the session needs to keep running. If Claude Code needs permission for something — like writing to a file it hasn't touched before — you'll need to grant that permission from the actual terminal, not from Telegram.

I know. After I just spent five paragraphs celebrating cloud automation for eliminating machine dependency, that sounds like a step backward. But Channels and cloud automation serve fundamentally different purposes. Cloud automation handles scheduled, predictable, repository-scoped tasks. Channels handle ad-hoc, conversational, full-environment interactions. The scheduled tasks don't need your machine because they operate against remote repositories. Channels need your machine because they're operating against your entire local development environment — filesystem, running services, MCP servers, everything.

Once I understood that distinction, both features locked into place in my workflow.

## Setting Up Cloud Scheduled Tasks: The Exact Steps

Here's the setup I've been running for the past week. I'm going to be specific because the configuration details — especially around how you write the task prompt — determine whether you get useful output or generic noise.

**Step 1: Access the Cloud Scheduler**

Navigate to claude.ai/code in your browser. You'll see a "Scheduled Tasks" section (it might be under an "Automation" tab depending on your account type). Click "New Task."

**Step 2: Connect Your Repository**

Select the GitHub repository you want the task to operate against. Claude needs read access at minimum, and write access if you want it to push changes or open PRs. I connected three repos: my main SaaS project, a client's Laravel application, and this blog's content repository.

**Step 3: Write a Specific Task Prompt**

This is where most people go wrong. A vague prompt like "review my code" produces vague output. Here's the exact prompt I use for my daily PR review:

```
Review all pull requests opened in the last 24 hours. For each PR:

1. Check for security issues: SQL injection, XSS, exposed credentials,
   missing input validation
2. Check for performance issues: N+1 queries, missing indexes,
   unnecessary database calls
3. Check for code quality: dead code, duplicated logic, missing error
   handling, inconsistent naming
4. Check test coverage: are new functions tested? Do existing tests
   still pass with the changes?

Output format:
- One section per PR with the PR number and title as heading
- Flag issues as [CRITICAL], [WARNING], or [SUGGESTION]
- For each issue, include the file path, line number, and a specific
  fix recommendation
- End with a summary: total PRs reviewed, total issues by severity

If no PRs were opened in the last 24 hours, output:
"No new PRs to review. Repository quiet."
```

Notice the specificity. I'm telling Claude exactly what to look for, how to categorize findings, and what format to use. I'm even handling the edge case of no new PRs so I don't get a confused response on quiet days.

**Step 4: Set the Schedule and Model**

I set this task to run daily at 6:30 AM UTC (that's 12:30 PM in my timezone — right before I typically start my afternoon coding session). Model: Sonnet 4.6 for speed. For tasks requiring deeper analysis, switch to Opus.

**Step 5: Configure Notifications**

Connect Slack or email to receive the output. I send results to a dedicated `#claude-reviews` Slack channel so the whole team sees them. The Slack integration requires adding the Claude Code Slack connector from your workspace settings — it takes about two minutes.

**Pro tip that nobody seems to talk about:** If your repository has a `.claude-md` file in the root, cloud-scheduled tasks will read and respect it. This means your project-level instructions, coding standards, and contextual information carry over to automated runs. I added a section to my `.claude-md` specifically for automated reviews:

```markdown
## Automated Review Context
This project uses Laravel 12 with Inertia.js and Vue 3.
Key patterns to enforce:
- All database queries must use Eloquent scopes, not raw queries
- API responses must follow our ResponseFormatter class
- New routes require corresponding FormRequest validation classes
- Frontend components must use TypeScript, not plain JavaScript
```

That single addition made my automated code reviews go from "generic best practices" to "project-specific, actually useful feedback." The difference in output quality was dramatic — Claude started flagging violations of our specific conventions, not just generic code smells.

## Setting Up Claude Channels with Telegram: From Zero to Voice Control

The Channels setup has more moving parts than cloud scheduling, but the payoff is different — you get real-time, conversational access to your coding agent from your phone. Here's the exact process I followed, including the parts that weren't obvious.

**Prerequisites:**

- Claude Code v2.1.80 or later (run `claude --version` to check)
- Bun runtime installed on your machine (Channels plugins use Bun)
- A claude.ai account (API keys don't work with Channels)
- Telegram installed on your phone

**Step 1: Create Your Telegram Bot**

Open Telegram, search for `@BotFather`, and start a conversation. Send `/newbot`. BotFather asks for a display name — I used "Claude Dev Assistant." Then it asks for a username ending in `bot` — I used `mejba_claude_bot`. BotFather replies with a token that looks like `123456789:AAHfiqksKZ8...` — copy the entire string including the number and colon.

**Step 2: Install the Telegram Plugin**

In your Claude Code terminal session, run:

```bash
/plugin install telegram@claude-plugins-official
```

Then reload plugins:

```bash
/reload-plugins
```

This writes your bot token configuration to `~/.claude/channels/telegram/.env`. If the automatic token prompt doesn't appear, you can manually edit that file and add `TELEGRAM_BOT_TOKEN=your_token_here`.

**Step 3: Whitelist Your Telegram User ID**

This is the security step you should not skip. Your Telegram user ID (a numeric ID, not your username) needs to be whitelisted so random people can't send commands to your bot. Find your ID by messaging `@userinfobot` on Telegram, then add it to the plugin configuration.

**Step 4: Launch Claude with Channels Enabled**

Exit your current session and start a new one with the channel flag:

```bash
claude --channels plugin:telegram@claude-plugins-official
```

DM your bot on Telegram. It replies with a 6-character pairing code. Enter that code in your terminal to link the connection. After pairing, your next message to the bot reaches Claude directly.

**Step 5: Test It**

I sent "What files changed in the last commit?" from my phone while standing in my kitchen. Eight seconds later, Claude responded with the exact diff summary — file names, lines added, lines removed. I followed up with "Create a new file called test-from-telegram.md with today's date as the title." Claude created it. I checked my filesystem from the terminal — the file was there.

That moment is when Channels stopped being a novelty and started being a tool.

**Step 6 (Optional): Enable Voice Transcription**

Here's where it gets interesting. Claude Channels can process voice messages, but transcription isn't built in by default. You need to install the Whisper plugin:

```bash
/plugin install whisper
```

This uses whisper.cpp for local transcription — your voice never leaves your machine. After installation, you can hold the microphone button in Telegram, record your message, and Claude receives a text transcription of what you said.

I tested this from my car (parked, engine off — let's not encourage distracted driving). Recorded a 15-second voice note asking about the status of a specific function in my project. Claude transcribed accurately and responded with the function's current implementation, recent changes, and a suggestion for refactoring it. The transcription quality was solid for clear speech in a quiet environment. Background noise degrades it noticeably.

If you'd rather have someone build out this entire automation setup from scratch — cloud tasks, Channels, the whole pipeline configured for your specific repos — I take on exactly these kinds of engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Honest Limitations (And What Caught Me Off Guard)

I've painted a rosy picture so far. Here's the reality check.

**Claude Channels has no persistent memory.** Every Telegram session starts fresh. If you had a detailed conversation about your authentication module at 2 PM and come back at 5 PM after the session restarted, Claude has no recollection of that conversation. It reads your project files and `.claude-md` for context, but the conversational thread is gone. I expected some form of chat history persistence, even basic. There isn't any unless you manually store transcripts on your local machine.

**Telegram's 4,096-character limit is real and frustrating.** Ask Claude to explain a complex function or generate a longer code snippet, and the response gets truncated mid-sentence. You learn to phrase requests for concise output: "Summarize in under 500 characters" or "Give me just the function signature, not the full implementation." It works, but it requires adapting your communication style.

**The 20 MB file transfer cap matters more than you'd think.** I tried to send a screenshot of an error for Claude to analyze. Fine — screenshots are small. But when I tried to send a database export for Claude to analyze, the 20 MB limit stopped me. For anything beyond quick screenshots and small text files, you need to use the terminal directly.

**Machine dependency for Channels is absolute.** Close the terminal, close the laptop lid, let the machine sleep — the channel dies. I tested this explicitly. Sent a Telegram message, then closed my MacBook lid. The message just hung in "sending" limbo until I reopened the lid, at which point I had to restart the Claude session entirely. The pairing doesn't reconnect automatically.

For Mac users who want Channels running for extended periods, two things help:

1. Go to System Settings > Displays > Advanced and enable "Prevent automatic sleeping when the display is off"
2. Run `caffeinate -d` in a separate terminal tab — this prevents display sleep until that terminal session ends

I've been running this combo for four days straight without the Channels connection dropping. But it means dedicating a machine to being "always on," which isn't ideal. A server-based solution — running Claude Code in a tmux session on a VPS — would be better, though I haven't tested that configuration yet.

**Cloud scheduled tasks can't access local resources.** I mentioned this earlier, but it bears repeating because I made this mistake. I tried to schedule a task that needed to read a local SQLite database for my personal project. The task failed silently — Claude couldn't find the file because it was executing in a cloud sandbox, not on my machine. Anything that lives only on your local filesystem needs desktop scheduled tasks, not cloud ones.

**Search within Channels doesn't exist.** On Telegram, you can't search back through your Claude conversation history. Every important response you want to reference later needs to be forwarded to your "Saved Messages" or copied manually. For a coding assistant where you're constantly asking "what did Claude say about that function yesterday?" — this is a real gap.

## My Actual Daily Workflow After One Week

Here's how cloud automation and Channels have settled into my routine after enough time to move past the "shiny new toy" phase.

**6:30 AM — Cloud task runs automatically.** Reviews overnight PRs across three repos. Results land in Slack before I wake up. I check the `#claude-reviews` channel while drinking coffee and flag anything that needs human attention.

**9:00 AM — Coding session starts locally.** I work in Claude Code on my terminal as usual. Full agent capabilities, full filesystem access. This is still the primary workflow for serious development.

**Throughout the day — Telegram for quick queries.** When I step away from my desk — walking the dog, making lunch, grabbing coffee — I keep the Channels session running. From my phone, I ask quick questions: "What's the status of the auth migration?" or "Show me the last error in the test suite." I also use voice notes for longer queries when typing on my phone feels slow.

**2:00 PM — Cloud task generates a daily dependency report.** Another scheduled task checks all three repos for outdated packages, known vulnerabilities (via npm audit / composer audit), and available updates. The report includes severity ratings and links to changelogs. This used to take me 20 minutes manually across three projects. Now it's waiting for me after lunch.

**Evening — Review and adjust.** I check the cloud task transcripts on claude.ai/code, tweak any prompts that produced suboptimal output, and occasionally adjust schedules. The feedback loop takes about five minutes and makes tomorrow's automated runs sharper.

The compound effect after a week is significant. I estimate I'm saving 45-60 minutes daily on tasks that were either manual or semi-automated with fragile local scheduling. The real win isn't the time savings, though — it's the reliability. I no longer wonder "did my morning review run?" or "did the dependency check actually execute while I was traveling?" The cloud tasks run. Period. And Channels means I'm never more than a Telegram message away from my coding agent, even when my desk is in another room or another building.

## What Cloud Automation Gets Right That Local Scheduling Didn't

I used to think of scheduled tasks as "cron jobs with better prompts." Cloud automation forced me to rethink that.

The execution environment being isolated from my machine turns out to be a feature, not a limitation. When a cloud task runs, it operates in a clean sandbox every time. No leftover state from previous runs. No interference from other processes on my machine. No risk of a runaway task eating my local CPU while I'm trying to record a video call. The consistency of execution is noticeably higher than desktop-scheduled tasks, where I occasionally saw degraded performance when my machine was under heavy load.

The dashboard at claude.ai/code is genuinely well-designed for this use case. Seeing all my scheduled tasks in one view — their status, last run time, next scheduled run, and the model assigned to each — gives me confidence that things are working. With desktop-scheduled tasks, I had to check the Cowork interface, and even then the status indicators were minimal. Cloud automation's transcript view makes debugging straightforward. When my weekly architecture review missed a pattern I expected it to catch, I read the transcript and realized Claude had focused on the wrong directory. Adjusted the prompt, fixed in one iteration.

The notification integrations — Slack and email — sound simple but make the workflow seamless. Results show up where my team already communicates. Nobody needs to check a separate dashboard. The PR review results appear in the channel where we discuss PRs. The dependency report appears in the channel where we track maintenance work. Information flows to where decisions happen.

## What I'd Change and What's Coming Next

This is a research preview. Some of what I'm about to say might be fixed by the time you read this, but as of March 2026, here's my wish list.

**Channels need automatic reconnection.** If my machine sleeps and wakes up, I want the Telegram connection to re-establish itself without manual intervention. Right now, any disruption means opening the terminal and restarting the session.

**Cloud tasks need local hybrid mode.** Some of my workflows need both cloud reliability and local filesystem access. The ideal architecture would be a cloud scheduler that triggers execution on my machine when it's available and queues tasks for catch-up when it's not. Desktop scheduled tasks already do the catch-up part — merging that with cloud scheduling would be powerful.

**Channels need message persistence.** Even basic session logging — "save the last 50 messages and reload them on reconnect" — would transform Channels from a quick-query tool into a genuine mobile development environment.

**Voice transcription quality needs improvement for noisy environments.** Whisper.cpp works well in quiet rooms. On a busy street or in a cafe, accuracy drops enough that I'd rather type. Server-side transcription using a more robust model (even at the cost of privacy) should be an option.

**Discord-specific features show the direction.** Discord Channels already support history retrieval and attachment downloads that Telegram doesn't have yet. This suggests Anthropic is iterating on per-platform capabilities, which means Telegram will likely catch up. But if you're choosing between platforms today and have existing Discord infrastructure, Discord offers more functionality.

## What This Means For How We Build With AI

Zoom out from the features for a second.

Twelve months ago, Claude Code was a terminal tool. You typed, it responded, you coded together. It was powerful but tethered — tethered to your desk, your terminal, your attention, your waking hours.

Cloud automation cuts the time tether. Your AI coding agent works while you sleep, works while you travel, works on a schedule you set once and forget.

Channels cut the location tether. Your AI coding agent is wherever your phone is.

Put them together and something shifts in how you think about AI-assisted development. It stops being "a tool I use when I sit down to code" and starts being "an always-available member of my engineering team that handles the stuff I'd rather not do manually."

I've already started rethinking which of my manual workflows could become cloud tasks. Things I never bothered automating before because the local-only limitation made the automation unreliable. Daily changelogs from commit history. Weekly performance benchmarks against the staging environment. Automated documentation updates when API endpoints change. The list keeps growing.

If you've been using [Claude Code's remote control feature](/claude-code-remote-control-phone) to stay connected while away from your desk, Channels is the next evolution of that same idea — but with messaging platforms you already have open all day. And if you've been frustrated by the local-only limitation of [Cowork's scheduled tasks](/claude-cowork-scheduled-tasks-automation), cloud automation is the direct answer.

The gap between "AI coding assistant" and "AI coding team member" just got a lot smaller. And honestly? After a week of waking up to completed code reviews and sending voice notes to my coding agent from a park bench, I'm not sure I can go back.

## Frequently Asked Questions

### Do Claude Code cloud scheduled tasks run when my laptop is off?

Yes. Cloud-scheduled tasks execute on Anthropic's infrastructure against your connected GitHub repositories, independent of your local machine. They run at the scheduled time regardless of whether your laptop is powered on, asleep, or closed. Local desktop scheduled tasks still require your machine to be awake.

### How do I set up Claude Channels with Telegram?

Install the Telegram plugin via `/plugin install telegram@claude-plugins-official` in Claude Code v2.1.80+, create a bot through Telegram's BotFather, configure the bot token, and launch Claude with `claude --channels plugin:telegram@claude-plugins-official`. For the full step-by-step walkthrough, see the Telegram setup section above.

### Can Claude Channels transcribe voice messages?

Yes, after installing the Whisper plugin via `/plugin install whisper`. Voice transcription runs locally using whisper.cpp, so your audio never leaves your machine. Accuracy is strong in quiet environments but degrades with background noise. See the voice transcription setup section above for details.

### What are the main limitations of Claude Channels on Telegram?

Telegram imposes a 4,096-character message limit, 20 MB file transfer cap, and no searchable message history. Claude Channels also has no persistent memory between sessions and requires your local machine to stay awake with the terminal session running. For a detailed breakdown, see the limitations section above.

### Can I use Claude Code cloud automation with private repositories?

Yes. You grant Claude access to specific repositories through the claude.ai/code interface, including private repos. Claude operates within a sandboxed cloud environment with read and write access scoped to the repositories you explicitly connect.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
