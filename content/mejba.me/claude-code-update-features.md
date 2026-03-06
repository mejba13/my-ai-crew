**BRAND:** mejba.me
**TITLE:** Claude Code Just Got Scary Good — Here's Why
**SLUG:** claude-code-update-features
**TAGS:** AI Development, Developer Tools, Claude Code, AI Agents, Deep Dive

---

I almost mass-deleted three custom scripts last Tuesday. Not because they were broken — because Claude Code's latest update made them completely unnecessary overnight.

I'd spent weeks fine-tuning shell aliases, building wrapper functions around API calls, stitching together a Slack notification pipeline for my deployment workflow. Good work. Solid engineering. Then Anthropic dropped this update, and I sat there watching Claude Code do everything my scripts did — plus things I hadn't even thought of — directly from the terminal.

That's a strange feeling. Equal parts "this is amazing" and "what just happened to my weekend project."

Here's what I know after spending a full week stress-testing every new feature: this isn't a minor patch. Anthropic fundamentally changed what Claude Code can do, and if you're building anything with AI agents right now, you need to understand what shifted. Some of these features will change how you work daily. One of them — and I'll get to it — might change how you think about AI skills entirely.

But first, the one that hit me hardest.

## The Skill Creator Turned My Agents Into Self-Improving Systems

I've been building custom skills for Claude Code since the early days. The process was always the same: write a skill definition, test it manually, tweak the prompt, test again, push it, discover it broke something else, and repeat for days. Functional? Sure. Efficient? Not even close.

The upgraded skill creator changes this loop completely. And I don't mean it got a nicer interface — I mean it introduced built-in evolve generation with benchmarking and multi-agent testing. Those words might sound like marketing copy, but stick with me because this is genuinely different.

Here's what that means in practice. You write a skill, and instead of manually testing it against various scenarios, you can now generate what Anthropic calls "evol tests" — automated benchmarks that measure pass rates, runtime, and token usage across multiple test scenarios. The skill creator spins up multiple agents, throws your skill at different edge cases, and gives you hard numbers on how it performs.

I tested this with a PDF form-filling skill I'd been fighting with for weeks. The original version would extract text from form fields and fill them, but it kept misaligning data — putting addresses in phone number fields, that kind of chaos. Manual debugging wasn't getting anywhere because the failures were inconsistent.

With the new skill creator, I ran an evolve test and immediately saw the problem: my text extraction wasn't anchored to precise coordinates. The skill was guessing field positions based on labels, and label positioning varied across different PDF templates. The benchmarking data showed a 62% pass rate — way worse than I'd assumed from my handful of manual tests.

The fix was straightforward once I could see the data. I anchored extraction to exact coordinates instead of fuzzy label matching. Pass rate jumped to 94% on the next evolve run. What would've taken me another week of manual debugging took about forty minutes with actual metrics to guide me.

But here's the part that really caught my attention — and this connects to something I'll show you in the implementation section. Skills can now automatically evolve when new models release. So when Anthropic ships a model update, your skills don't just keep working at the same level. They can adapt and potentially improve without you touching them. I used to dread model updates because half my prompts would need rewriting. That friction is mostly gone now.

## Ultra Mode and Why I Stopped Worrying About Complex Codebases

I'll be honest — when Ultra Mode first appeared months ago, I thought it was mostly hype. Bigger reasoning budget, longer thinking time, sure. But did it actually produce meaningfully better output?

After this update: yes. Unambiguously yes.

Ultra Mode now supports Opus 4.6 and Sonnet 4.6, and the difference isn't subtle. The extended reasoning budget means Claude Code can hold larger chunks of your codebase in its working memory while solving problems. For small scripts and single-file edits, you won't notice much. For anything touching multiple files, complex state management, or multi-step debugging — it's a different experience entirely.

I put it through the most punishing test I could think of: building a full-stack calendar application from scratch. React frontend, TypeScript throughout, proper component architecture, state management, API integration. Not a toy demo — a real application with recurring events, timezone handling, drag-and-drop rescheduling.

With standard mode, Claude Code would occasionally lose context between components. It'd generate a beautiful EventCard component, then create a CalendarGrid that expected slightly different props. Small mismatches that I'd catch in review and fix manually. Normal AI coding experience.

With Ultra Mode active (you enable it by typing `ultrathink` in Claude Code), those mismatches essentially vanished. The generated components fit together cleanly because the model had enough reasoning space to hold the entire component tree in mind simultaneously. The calendar app came out with consistent prop interfaces, proper TypeScript types flowing through the whole stack, and even sensible error boundaries I didn't explicitly ask for.

Does it use more tokens? Absolutely. Is it slower? Slightly. Worth it for complex work? Without question. I've started defaulting to Ultra Mode for anything beyond single-file edits, and my review-and-fix cycles dropped dramatically.

There's a practical trade-off I should mention, though. Ultra Mode burns through your API budget faster. For a complex session building that calendar app, I used roughly 3x the tokens compared to standard mode. If you're on a tight budget, save Ultra Mode for the work that genuinely needs it — multi-file refactors, complex debugging, architectural decisions. Use standard mode for quick edits and simple generations.

That budget consideration leads naturally into the next feature, because Anthropic clearly thought about how people actually use Claude Code throughout their day — not just when they're sitting at a desk.

## Remote Control: My Phone Became a Build Dashboard

This one surprised me more than anything else in the update. Remote control lets you start, monitor, and interact with Claude Code sessions from your phone or any secondary device. Available on all paid Anthropic plans, no additional setup beyond what you'd expect.

My first thought was "that's a gimmick." My second thought, after using it for three days, was "how did I live without this?"

Here's my actual use case. I kicked off a large refactoring task before leaving for a meeting — reorganizing a monorepo's shared utilities into proper packages. The kind of task where Claude Code needs to make hundreds of file changes across dozens of directories. Previously, I'd either wait at my desk until it finished or come back to find it had hit an error forty minutes ago and stopped.

With remote control, I watched the progress on my phone during the meeting. When Claude Code hit an ambiguous import path and asked for clarification, I responded from my phone in about fifteen seconds. The refactor continued. By the time I got back to my desk, the entire reorganization was done and passing tests.

I also used it for something simpler but equally useful: monitoring long-running test suites. Start the tests from Claude Code, walk away, get a notification on your phone when they finish or fail. It turns Claude Code from a "sit at your desk" tool into something that works around your schedule.

One limitation worth knowing: complex interactions — like reviewing large diffs or writing detailed prompts — are still better on a full keyboard. The phone interface works great for approvals, short responses, and monitoring. Don't expect to do deep coding sessions from your phone. That's not the point, and honestly, it shouldn't be.

Now, speaking of interacting with Claude Code in ways I didn't expect — there's a feature rolling out right now that completely changes the input model.

## Voice Mode Changed How I Think About Prompting

Only about 5% of users have access to voice mode right now, and I happened to be in that group. Activated by typing `/voice` in your Claude Code session, it turns on real-time speech-to-text transcription for your commands.

My initial reaction was skepticism. I type fast. Why would I talk to my terminal?

Three days later, I get it. The value isn't speed — it's expressiveness.

When I type a prompt, I tend to be terse. "Refactor the auth middleware to handle token refresh." Clean, precise, efficient. But also missing context that would help Claude Code make better decisions.

When I speak, I naturally add that context: "Hey, so the auth middleware is getting messy because we added token refresh last sprint but it's tangled up with the session validation logic. I want to pull the refresh flow into its own middleware that runs before the session check, and make sure we're not hitting the token endpoint more than once per request cycle."

Same request. Way more context. Dramatically better output from Claude Code because it understood not just what I wanted, but why.

I've started using voice mode specifically for complex architectural requests where context matters, and typing for quick, precise edits. The hybrid approach works better than either method alone.

The transcription accuracy is solid — it handles technical terms like "middleware," "TypeScript," and specific package names better than I expected. Not perfect, but good enough that I rarely need to correct it. I did notice it occasionally struggles with variable names that sound like common words (`handler` vs `handler` is fine, but a variable named `reed` gets transcribed as "read" sometimes). Minor friction.

If you don't have access yet, it's worth requesting. And while you're waiting, there's a feature available right now that directly impacts how fast you can build with external APIs.

## The Claude API Skill Eliminated My Documentation Tab Problem

I used to keep three browser tabs open at all times: one for Anthropic's API docs, one for whatever SDK reference I needed, and one for Stack Overflow. The Claude API skill makes at least two of those tabs unnecessary.

Here's what it does. When you're writing code that interacts with the Claude API — or really any supported API — the skill automatically detects your programming language and selects the best interface (direct API, tool use, or SDK). Then it loads the relevant documentation directly into your coding session.

Not a summary. Not a "here's a link." The actual documentation context, available as the model works on your code.

I was building a batch processing pipeline in Python last week. Instead of switching to the browser to look up the batch API endpoint format, I just described what I needed in Claude Code. The API skill detected I was working with Python, pulled in the Anthropic SDK docs for batch requests, and generated working code that included proper error handling, retry logic, and rate limiting — all based on current documentation, not training data that might be outdated.

It handles streaming responses, structured outputs, tool use configurations, and model-specific defaults. The key benefit is eliminating context switching. Every time you alt-tab to a documentation page, you lose a few seconds of focus. Over a full coding session, that adds up to significant cognitive overhead. Having the docs embedded in the coding context removes that friction entirely.

One thing I appreciate: it's smart about model defaults. When I specified Opus 4.6 as my target model, the skill automatically adjusted the token limits, pricing estimates, and available features in its suggestions. Small detail, but it prevented me from writing code that assumed capabilities the model doesn't have.

Alright — if you've followed along this far, you already have a solid understanding of the major features. The next two are smaller but punch above their weight in daily workflow improvement.

## The Simplify Command Caught Mistakes I Missed

After any code change session, running `/simplify` triggers an automated review of your modified code. It scans for duplicated logic, reusable patterns, naming inconsistencies, unnecessary complexity, and structural issues.

I was skeptical. Code review is nuanced work — could an automated pass really catch meaningful issues?

It caught something in my second test that would have caused a production bug.

I'd refactored a data validation module and, in the process, created two nearly identical validation functions — one in the user service and one in the order service. Same logic, different variable names. The kind of duplication that happens naturally when you're moving fast and focused on making tests pass.

The simplify command flagged both functions, showed me the overlap, and suggested extracting a shared validator. After I approved the fix, it also noticed that my error messages used inconsistent formatting — some had error codes, some didn't. Not a bug, but the kind of inconsistency that creates confusion in logs.

The default routing agent for simplify appears to be Haiku 4.5, which means it runs fast and cheap. I've started using it as a habit after every significant code change session. Think of it as a first-pass code review before your human reviewers see the PR. It won't catch architectural issues or question your design decisions, but it's excellent at surface-level quality — the stuff that's easy to miss when you're deep in problem-solving mode.

Pro tip: run `/simplify` before committing, not after. Fixing duplication and naming issues before they enter your git history keeps your commits cleaner and your reviewers happier.

This pairs nicely with the last major feature — because clean code means nothing if your team can't communicate about it effectively.

## Slack Integration Made Claude Code a Team Player

I run a small distributed team, and our coordination lives in Slack. Previously, sharing Claude Code context with teammates meant copying terminal output, pasting it into Slack, losing formatting, and then explaining what the output meant. Clunky at best.

The new Slack plugin connects Claude Code directly to your Slack workspace. You can search messages, send updates, create documents, and pull Slack context into your coding session without leaving the terminal.

Here's where it actually helped me this week. A teammate posted a bug report in our `#backend-issues` channel with a stack trace and some context about when it started happening. Instead of copying the stack trace into Claude Code manually, I pulled the Slack message directly into my session. Claude Code had the full context — the stack trace, the teammate's description, and the thread discussion where another dev mentioned a recent config change that might be related.

With all that context loaded, Claude Code identified the issue in about two minutes: a connection pool configuration that was valid in our staging environment but caused timeout cascades under production load. The teammate's mention of "started happening after Tuesday's deploy" was the key context that pointed Claude Code toward the right config diff.

I also use it for sending status updates. When a long build or refactoring session completes, I have Claude Code post a summary to the relevant Slack channel. No context switching, no copy-paste, no forgetting to update the team.

One workflow I've developed: at the start of each coding session, I pull the latest messages from my project's Slack channel into Claude Code's context. This gives the AI awareness of recent team decisions, blockers, and priorities. It's like giving Claude Code a standup meeting before it starts working.

## The Cloud Code Guide Sub-Agent — Your Built-In Documentation Brain

There's one more addition worth mentioning: the Cloud Code guide sub-agent. This is an internal, non-file-based agent that answers questions about the Claude Code CLI, the Agent SDK, and the Claude API itself.

Think of it as a knowledgeable colleague who's read every page of Anthropic's documentation and can answer questions without you leaving the terminal. It uses search, web fetch, and other tools to find answers but — and this is important — it doesn't edit or write files. It's purely informational.

I find myself using it most when I hit edge cases. "Can the Agent SDK handle streaming tool use responses?" Instead of searching documentation, I ask the guide sub-agent and get an answer with relevant context in seconds. For developers who are deep in the Anthropic ecosystem, this is a significant time saver.

## Setting Everything Up: My Recommended Configuration

Here's exactly how I configured my environment to use all these features effectively. This took some trial and error, so let me save you the debugging.

**Step 1: Update Claude Code to the latest version.** This sounds obvious, but the features require the most recent release. Verify you're on the latest build before troubleshooting missing features.

**Step 2: Install the skill creator plugin.** Go to the Claude Code plugins tab, find the skill creator, and install it. Restart Claude Code after installation — the plugin won't activate until you do. I missed this the first time and spent twenty minutes wondering why nothing worked.

**Step 3: Enable Ultra Mode selectively.** Type `ultrathink` in any Claude Code session to activate Ultra Mode. My recommendation: use it for multi-file tasks and complex debugging. Don't leave it on permanently unless your token budget is generous. I set up a simple mental rule — if the task touches three or more files, I enable Ultra Mode.

**Step 4: Configure Slack integration.** Install the Slack plugin from the plugins tab. You'll need to authorize it with your Slack workspace. Once connected, test with a simple message search to verify the connection works. I had to re-authorize once because my initial OAuth token had limited scopes.

**Step 5: Test voice mode availability.** Type `/voice` in your Claude Code session. If it activates, you're in the 5% rollout. If not, you'll get a message saying it's not yet available for your account. No workaround for this — it's a server-side flag.

**Step 6: Run your first evolve test.** Pick a simple existing skill and generate an evolve test for it. This familiarizes you with the benchmarking output format — pass rate, runtime, token usage — before you need it for serious skill development. Start small, understand the metrics, then apply it to complex skills.

**Step 7: Build `/simplify` into your workflow.** After every significant code change, run the simplify command before committing. It takes seconds and catches the kind of low-level issues that clutter code reviews. I added a mental checklist: write code, run tests, run `/simplify`, review suggestions, commit.

Common gotcha: if the Slack plugin shows "connection refused" errors, check that your workspace admin hasn't restricted third-party integrations. This bit me on a client project where the workspace had strict app approval policies.

## What I Got Wrong About This Update — And What Most People Will Miss

I want to be straight about something. When I first saw the feature list, I thought Anthropic was doing the classic tech company move — shipping a bunch of incremental improvements and calling it a major update. I was wrong, and here's why my initial read missed the point.

The individual features are solid but not earth-shattering on their own. Voice mode? Cool but niche. Slack integration? Useful but not groundbreaking. Remote control? Convenient.

The real shift is what happens when you combine them.

I'm now running sessions where I start a complex task at my desk using Ultra Mode, monitor it from my phone during a meeting via remote control, respond to clarification questions using voice mode on my walk back, and then have Claude Code post the results to Slack for my team — all without breaking my flow or losing context.

That's not an incremental improvement. That's a fundamentally different relationship with the tool. Claude Code stopped being something I sit down and use. It became something that works alongside me throughout my day, across devices, across communication channels.

The skill creator with evolve testing is the sleeper feature that most people will underestimate. Right now, "AI skills" feel like prompt engineering with extra steps. But self-improving skills that benchmark themselves and adapt to new models? That's infrastructure. That's the kind of thing that compounds. Six months from now, the developers who invested in building robust, tested skills will have a significant advantage over those who are still writing one-off prompts.

One honest concern: the token usage adds up. Running Ultra Mode, evolve tests, and the API skill simultaneously can burn through your budget fast. I've had sessions that used 10x my normal token consumption. Anthropic needs to work on making this more transparent — I'd love a real-time token budget dashboard in Claude Code. Right now, you find out you've overspent after the fact.

## The Numbers After One Week

Here's what my workflow looked like before and after the update, measured across a typical work week:

Context switches to browser documentation: dropped from roughly 40 per day to about 8. The Claude API skill and guide sub-agent handle most of what I used to look up manually.

Code review iterations before merge: decreased from an average of 3 rounds to 1.5 rounds. The simplify command catches surface-level issues before human reviewers see the code, so review discussions focus on architecture and design instead of formatting and duplication.

Time spent debugging skills: reduced by approximately 70%. Hard to measure precisely, but the evolve testing gives me confidence numbers instead of vibes. I know when a skill works and when it doesn't, which eliminates the "is it broken or am I testing wrong?" uncertainty.

Team communication overhead: noticeably lower. Slack integration means I'm sharing context and updates without leaving the terminal. Less copy-paste, fewer "let me explain what this output means" messages.

Token costs: up about 180% from my pre-update baseline. This is the trade-off. I'm spending more on API usage but reclaiming hours of manual work. For my workflow, the math works out clearly in favor. Your mileage depends on your specific usage patterns and budget.

The biggest change isn't in any single metric. It's the feeling of continuity. Before, using Claude Code meant sitting at my terminal, focused, doing one thing. Now it's woven into my entire workday. That shift is hard to quantify but impossible to ignore once you experience it.

## What I'd Build First If I Were Starting Today

If you told me six months ago that I'd be building self-benchmarking AI skills, controlling coding sessions from my phone, and talking to my terminal — I'd have laughed. But here I am, and here's what I'd prioritize if I were setting this up fresh.

Start with the simplify command. Zero configuration, immediate value, builds good habits. Run it after every session for a week and you'll never stop.

Next, invest time in the skill creator. Build one real skill — not a toy example — and run evolve tests on it. Understand the benchmarking output. This investment pays compounding returns as you build more skills.

Then explore Ultra Mode for your most complex workflows. Don't use it for everything — use it strategically, and you'll see exactly when the extended reasoning budget makes a difference.

Everything else — voice mode, remote control, Slack — layer in as they become relevant to your specific workflow. They're powerful additions, but the core productivity gains come from simplify, the skill creator, and Ultra Mode.

The gap between developers who treat Claude Code as a fancy autocomplete and those who treat it as an autonomous development partner is about to widen significantly. This update is Anthropic making it very clear which side of that gap they're building for.

So here's my question for you: what's the first skill you'd build if your AI agent could test, benchmark, and improve itself?

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
