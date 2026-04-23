**BRAND:** mejba.me
**TITLE:** Claude Routines + Opus 4.7: My First Build Log
**META TITLE:** Claude Routines + Opus 4.7: First Build Log (2026)
**SLUG:** claude-routines-opus-4-7-first-build
**PRIMARY KEYWORD:** Claude Routines
**META DESCRIPTION:** I shipped my first Claude Routines automation on Opus 4.7. Here's what ran, what broke, what the research preview still misses, and what to build first.
**TAGS:** Claude Routines, Opus 4.7, AI Automation, Claude Code, Agentic Workflows

---

# Claude Routines and Opus 4.7: What I Actually Shipped in a Week

It was 6:47 AM on a Friday and my phone was face-down on the nightstand. In the kitchen, something I had not run, on a machine that was not mine, was reading my last 24 hours of email, sorting it into three piles, drafting replies to the five that needed one, and dropping a Slack summary in `#morning-brief` with the subject lines and a one-line urgency score for each.

By the time I walked in for coffee, the summary had been sitting in Slack for 31 minutes. Two draft replies were already in my Gmail drafts folder, needing about nine words of editing each before I hit send. A third draft was flagged by the routine itself as "needs your context — do not send." The laptop on my desk had been closed since 11:14 PM the night before.

This is the first week I have been able to say that sentence — "something ran while I slept on a machine that was not mine" — about a workflow I built myself, in under three minutes, without writing a line of cron syntax or spinning up an EC2 box. That is what shipped when Anthropic released **Claude Routines** on April 14, 2026, two days before they dropped Opus 4.7 on April 16. The two launches are inseparable. Routines are the container. Opus 4.7 is what makes the work inside the container actually useful instead of just scheduled.

I have now run six Routines across seven days. Two I shipped to production. Two I rebuilt from scratch after they failed in ways the documentation does not warn you about. One I deleted entirely. And one is running right now, in the background, while I write this sentence.

What follows is not a re-announcement. If you want the press release, Anthropic's own post is fine. What follows is what it actually feels like to build with Claude Routines on Opus 4.7 in the first week they exist — the patterns that worked, the failure modes I hit, the security posture you need to decide on before you give a scheduled job access to your inbox, and the specific workflows I would build next if I had another free weekend.

Let me start with what Routines actually are, because half the coverage I have read this week is getting one detail consistently wrong.

## What Routines Actually Are (And What They Are Not)

A Routine is a saved Claude Code configuration — a prompt, a set of repositories, a set of connectors, and a trigger — that runs on Anthropic's own infrastructure on a schedule, an API call, or a GitHub webhook event. When the trigger fires, Anthropic spins up a fresh Claude Code session, feeds it your saved prompt, gives it access to the connectors you approved, lets it run to completion, and then tears the session down. Your local machine is not involved. Your laptop can be closed. You can be on a plane with airplane mode on.

That last point is the one that changed how I think about these things. Every scheduled AI workflow I have ever built before — every cron job wrapping a Python script calling an API, every GitHub Action firing Claude through the SDK — depended on a machine I owned being on, online, and healthy. Routines are the first time that dependency is gone for me.

Here is what Routines are not. They are not an n8n replacement. They are not a Zapier killer. They are not a visual workflow builder where you drag boxes and connect arrows. They are a saved prompt, plus triggers, plus tool permissions, plus a place where Claude can run for as long as the work takes. The visual surface is the desktop app's Routines panel. The actual intelligence is whatever Claude decides to do when the trigger fires and it reads your prompt.

That distinction matters because the whole design philosophy is different. A Zapier flow fails the moment an API response changes shape. A Routine, in theory, reads the new shape and adapts. Whether it actually does that in practice is where Opus 4.7 comes in.

### Triggers, Tools, and the Three Numbers That Define the Feature

Routines support exactly three trigger types, and each one has sharp edges you need to understand before you commit a workflow to it.

**Schedule triggers** run on a cron-style cadence with one hard constraint: the minimum interval is one hour. You pick from four presets in the UI — hourly, daily, weekdays, weekly — and if you need a custom cadence like "every two hours" or "the first Monday of every month," you configure the preset closest to what you want and then run `/schedule update` in the CLI to set a specific cron expression. Schedules more frequent than hourly are rejected. If you were hoping to run a Routine every five minutes as a polling job, it is not going to happen inside Routines today.

**Webhook triggers** fire when your Routine receives an API call with the right key. This is the one I keep coming back to. It means any tool that can POST to a URL can fire a Routine — your CRM, your PM board, your contact form, your own scripts. It is the generic escape hatch. If the schedule cadence does not fit, almost any cadence problem can be solved by having some other system POST a webhook on the schedule you actually want.

**GitHub triggers** fire on GitHub webhook events — pushes, PR openings, comments, releases — against repositories you connect through the Claude GitHub App. During the research preview, these events are subject to per-routine and per-account hourly caps. If you push ten times in five minutes, not all ten pushes will fire the Routine; some will be dropped until the hourly window resets. Worth knowing before you build a "Claude reviews every PR" workflow and wonder why half the PRs go unreviewed.

Three numbers define how much of Routines you actually get, depending on your plan. Pro accounts can run up to **5 Routine executions per day**. Max accounts can run **15**. Team and Enterprise accounts can run **25**. Overages can be billed on an opt-in basis. These caps are per day, not per Routine, so a Pro user running five different Routines that each fire once per day is at the cap. A Pro user running one Routine that fires hourly hits the cap before 11 AM.

That last math is the thing most people will get wrong the first time they try to scale a Routine past the demo stage. If you are on Pro and you want hourly execution, you have 5 runs per day, meaning a real hourly job is not possible on Pro without overage billing turned on. This is a research preview constraint, almost certainly temporary, but it is the constraint today.

Here is where the feature gets interesting, though — and here is where the model behind it starts to matter more than the plumbing.

## Opus 4.7 Is Why Routines Are Actually Useful

Anthropic shipped Claude Opus 4.7 on April 16, 2026, two days after the Routines research preview opened. I do not think that is a coincidence. Every published benchmark points the same direction: Opus 4.7 is better at long-running, multi-step, tool-heavy work than any model before it. Routines are, by design, long-running, multi-step, tool-heavy work.

The numbers I trust most, because they come from Anthropic's own engineering partner evaluations rather than marketing curves, are these. On Box's internal production evaluations, **Opus 4.7 achieved a 56% reduction in model calls and a 50% reduction in tool calls compared to Opus 4.6**, responded 24% faster in the same evaluations, and used 30% fewer AI Units in end-to-end work. On Rakuten's production coding benchmark, **Opus 4.7 resolves roughly three times more production tasks than Opus 4.6**, with double-digit gains in both Code Quality and Test Quality. On CursorBench, which measures real IDE-integrated coding workflows rather than synthetic problem solving, **Opus 4.7 jumps from 58% (the Opus 4.6 score) to 70%** — a 12-point improvement on the benchmark that most closely resembles the actual work developers do.

The benchmark that matters most for agentic workloads, though, is SWE-bench Verified. Opus 4.6 scored 80.8%. Opus 4.7 scores **87.6%**. That is nearly a seven-point lift on a leaderboard where single-point gains at the top of the curve are hard-won, and it puts Opus 4.7 ahead of Gemini 3.1 Pro (80.6%) and meaningfully ahead of GPT-5.4 on the same task set.

There is one more thing worth calling out, because it is the single most important flag to know before you configure a Routine: Opus 4.7 ships with a new effort level called **xhigh**. Not "extra high," not "extended," not "high-plus" — the literal flag name in the API and the CLI is `xhigh`. It is the recommended default for Opus 4.7 for complex reasoning and agentic work, and it is turned on by default inside Claude Code for this model. What xhigh does, under the hood, is allocate more tokens to the model's own internal reasoning and tool-exploration loop before it reports back. For a one-shot chat, it is overkill. For a Routine that has to pull email, categorize it, draft replies, and send a Slack summary without you there to course-correct, it is exactly the tier you want.

A caveat the blog posts will not emphasize: xhigh is expensive. Combined with a new tokenizer in Opus 4.7 that counts roughly 1.0 to 1.35 times more tokens for the same text as 4.6, and the natural output inflation that comes with deeper internal reasoning, **your API bills can rise 20% to 50% per equivalent task compared to the 4.6 era**. The work is better. The work is more expensive per run. Both things are true. If you are running Routines on the API rather than inside Claude Code's included usage, do the math on a low-stakes test job before you wire xhigh into a Routine that runs 25 times a day.

With the feature and the model mapped out, let me tell you what I actually built.

## The Routine I Shipped: Morning Email Triage That Actually Works

I built the email triage Routine first for the same reason everyone else will: the demo Anthropic shipped with the launch was an email triage Routine, and the shape of the problem — predictable inputs, bounded outputs, easy to validate — makes it the cleanest first-build in the feature. What I was not expecting was how much the quality of the output would depend on writing the prompt as if I were handing off a task to a contractor I had never met.

Here is the prompt I ended up with after three iterations. It is long, and that is the point.

```text
You are running an unattended email triage job at 6:45 AM local time.
I cannot answer questions while you run. Make the best judgment call
you can at every decision point. When in doubt, draft don't send.

STEP 1 — FETCH
Pull every unread message from my Gmail inbox received in the last
24 hours. Exclude anything in Promotions, Social, or Updates.

STEP 2 — CATEGORIZE
Sort each message into exactly one of these three buckets:
  - URGENT — a specific named person is blocked on my reply, or a
    deadline is within 48 hours, or money/contracts are involved
  - NEEDS_REPLY — a response is expected but nothing is blocked and
    nothing is on fire
  - NO_ACTION — newsletters, receipts, FYIs, no reply expected

STEP 3 — DRAFT
For every URGENT and NEEDS_REPLY message, draft a reply and save it
to my Gmail drafts folder. Do not send. Match my voice from the
last ten replies in my Sent folder (short sentences, no "Hope you're
well," sign off with "— M"). If you cannot confidently draft because
you are missing context I have, draft a reply that says exactly what
context you need and mark the draft [NEEDS_CONTEXT] in the subject.

STEP 4 — SUMMARIZE
Post a Slack message to #morning-brief with this format:
  URGENT (N): one-line sender + one-line subject + your draft verdict
  NEEDS_REPLY (N): same shape
  NO_ACTION (N): count only, no detail

STEP 5 — FAILURE HANDLING
If any step fails, post to Slack #morning-brief with @here and the
error. A silent failure is the worst possible outcome. I would rather
see a red message than get nothing.

STEP 6 — CONTEXT RETRIEVAL FROM PRIOR RUNS
Before starting Step 1, read the pinned message in #morning-brief
from yesterday's run. If any URGENT items from yesterday are still
unaddressed in my Sent folder, surface them in today's summary as
CARRIED_OVER.
```

Six numbered steps. Explicit failure handling. Explicit instructions for how to pull context from the previous run. No assumptions about what Claude will figure out on its own.

That last step — the context retrieval from prior runs — is the one that took me two rebuilds to learn. **Routines have no memory between runs by default.** Each execution starts with a fresh session. If you want yesterday's run to inform today's, you have to tell Claude exactly where to look — a Slack channel, a Google Doc, a GitHub gist, a Notion page, anywhere it can read and write through a connector. If you skip this step, the Routine will happily re-draft a reply to the same email three days in a row, because from its perspective every morning is the first morning.

The failure-handling instruction is the other lesson that cost me a rebuild. My first Routine failed on a single malformed MIME message and died without a word. I did not know it had failed until I noticed I had not gotten a Slack summary at 7 AM. By the time I rebuilt it with the `@here` failure callback, I had missed two actual urgent emails because I assumed the quiet morning meant there was nothing to reply to. A silent failure is the single worst outcome in any unattended workflow. Write the failure callback before you write the happy path.

### The First Week's Results

Here is what the data actually looked like across seven days on this one Routine. I am giving you what I observed, not a curated case study.

**Messages processed:** 294 unread emails across 7 days (roughly 42 per day).
**Categorization accuracy, human-judged by me at 9 AM each morning:** 91% of the time I agreed with Claude's bucket. The 9% disagreements were almost always Claude over-classifying "URGENT" — usually because a sender's tone read as urgent but the actual content was just a status update.
**Draft quality:** 6 of every 10 drafts I sent after editing fewer than 15 words. 2 of 10 I rewrote substantially. 1 of 10 I discarded and wrote from scratch. 1 of 10 was correctly flagged `[NEEDS_CONTEXT]` and I answered it myself.
**Silent failures:** Zero after I added the callback. One (the MIME crash) before.
**Time saved, roughly estimated:** My email triage took me about 25-35 minutes every morning before this. With the Routine, the review-and-send pass takes about 7-10 minutes. That is roughly three hours per week back.

One caveat worth underlining: the Routine is only this good because the prompt is this detailed. I tested a shorter version — "Triage my email, draft replies, post a Slack summary" — and it worked about 60% as well. Vague prompts produce vague routines. Treat the prompt as a specification document, not a chat message.

## The Trusted Versus Full Tool Mode Decision You Cannot Skip

Before any Routine can touch your tools, you choose one of two modes for it: **trusted** or **full**. This is the single most consequential security decision you make in the entire setup, and it deserves more than the two paragraphs the documentation gives it.

In **trusted mode**, the Routine can only use tools you have explicitly approved for it — Gmail, Slack, Google Drive, whatever you individually toggled on. If the prompt asks Claude to do something that requires an unapproved tool, the Routine will either refuse the step or fail. This is the default. This is also the right starting point for every workflow you build.

In **full mode**, the Routine can reach for any tool that has a connector configured for your account. If halfway through its run it decides it needs to search the web, write a file, or query a new API, it can do that without asking you. Full mode is how you unlock the most ambitious kinds of autonomy. It is also how you accidentally give an unattended AI agent the authority to email your entire customer list because the prompt was ambiguous and the model interpreted "reach out" more broadly than you intended.

My rule after one week: **build every Routine in trusted mode first, run it for at least three executions, audit the tool calls, and only promote to full mode if you can name a specific capability you need that trusted mode cannot reach.** Every Routine I run in production today is in trusted mode. The one Routine I experimented with in full mode is one I deleted, because it did exactly the kind of thing that makes you never want to run an unattended agent again — it decided that the best way to "follow up on stale leads" was to email seventeen people I had not talked to in two years.

There is no good technical fix for prompt ambiguity in full mode. The only real fix is to write a tighter prompt and run in trusted mode.

If you have already built production agents with the Agent SDK, most of this will feel familiar — the same capability-boundary discipline applies here, with less code and a slightly different surface. For a deeper breakdown of those boundaries, see [my walkthrough on the Anthropic Agent SDK](https://www.mejba.me/anthropic-agent-sdk-guide).

## What Broke, And What I Had to Rebuild

Not everything I tried worked. The two Routines that failed the hardest are more useful to talk about than the ones that succeeded, because the failure modes are almost certainly going to be the failure modes other people hit in their first week.

**Failure 1: The PR review Routine that fired too often.**
I wired a GitHub trigger on a mid-size Laravel repo — every PR open, every push to an open PR, run a code review and drop it as a PR comment. On a busy Tuesday I pushed 11 commits to a single PR across about 90 minutes. The Routine fired six times and then silently stopped. I thought it had broken. What had actually happened is I had hit the per-routine hourly cap for GitHub webhook events during the research preview, and the remaining five events had been dropped. The documentation mentions this as a single line; I missed it on the first read. My fix: debounce the trigger to "on PR open" and "on PR ready-for-review" rather than every push, which reduced the firing rate from six-per-PR to one-per-PR and stopped dropping events entirely.

**Failure 2: The weekly reporting Routine that pulled the wrong window.**
I built a Routine to run every Monday at 8 AM, pull my Stripe payouts and Fiverr earnings for the prior week, summarize the trend line, and post to a private Slack channel. The first run returned a number that was roughly 40% too low. The model had interpreted "last week" as "the last seven days from now" rather than "the previous Monday-to-Sunday calendar week." Because each Routine execution starts fresh with no memory of what last week means, it had no anchor to calibrate against. The fix was explicit: "Pull data for the calendar week starting Monday [date math in ISO-8601] and ending Sunday [date math in ISO-8601]. Not the last seven days. The prior calendar week." After that, the numbers matched my Stripe dashboard exactly.

Both failures share a shape. Both were failures of assumed context. In both cases, I had written a prompt that would have worked fine for a human colleague who knows how I work, and would have been ambiguous to a brand-new contractor on their first day. Routines are always on their first day, every time they run. Write prompts for a first-day contractor and most of these failures disappear before they happen.

If you already write long-running agentic workflows and you want a deeper framework for this kind of prompt construction, the one I reached for this week is the one I wrote up in [my Opus 4.6 hands-on review](https://www.mejba.me/opus-4-6-hands-on-review) — the principles translate cleanly to 4.7, with the added benefit that 4.7 tolerates more ambiguity before it goes off-script. "Tolerates more" is not "eliminates." Tight prompts still win.

## Where Routines Still Fall Short

This is the section most launch-week coverage will skip, so I am going to spend time on it. There are four real limitations I hit in week one that are worth naming clearly, because they should inform what you build and what you do not.

**1. No cross-run memory by default.** I covered this above but it deserves repeating. Every Routine execution is a brand-new Claude Code session with zero memory of its own history. The workaround is explicit context retrieval — point Claude at a Slack channel, a Google Doc, a database row, anywhere it can read yesterday's output before it starts today's work. This is solvable, but it is manual, and forgetting to solve it is how you end up with three days of duplicated replies.

**2. One-hour minimum cadence.** If the real work needs to run every five minutes — a status-check polling job, a live-signal monitor, anything trader-time — Routines cannot do it inside the schedule trigger. The workaround is a webhook trigger fired by an external scheduler, but now you are back to managing infrastructure somewhere else, which partially defeats the pitch. For true sub-hourly cadence, Routines are not the right tool yet.

**3. Security posture of full mode is not mature.** Full mode is powerful and dangerous in roughly equal measure. There is no sandbox mode that simulates a full-mode run before it hits production tools. There is no per-tool cost cap to prevent a runaway workflow from burning tokens on a bad loop. There is no rate-limiter you can configure per connector. The only real safeguard is the quality of your prompt plus starting in trusted mode. Professional teams running Routines in full mode against real customer data should expect to build their own guardrails around the Routine — approval-queue patterns, side-channel audit logs, circuit breakers on spend.

**4. Research preview means the API can change.** Everything I wrote in this post is true on April 21, 2026. Anthropic has explicitly flagged that request and response shapes, rate limits, and token semantics can change while Routines are in research preview. If you build mission-critical infrastructure on top of Routines this month, budget time to rewrite some of it within the next six months. This is not a flaw — it is the honest tradeoff of shipping against a research preview — but it is worth naming because I have watched teams treat a research preview like a stable API and lose days to a shape change they did not notice.

None of these are dealbreakers. All of them are things you should know before you commit a workflow to Routines and promise someone else it will stay working.

If you are already running scheduled automations in Claude Code's desktop app rather than Routines, you might find the comparison I did between the two in [the Claude CoWork scheduled-tasks guide](https://www.mejba.me/claude-cowork-scheduled-tasks-automation) useful — the surfaces look similar on the outside, but the execution model is fundamentally different, and the choice matters depending on whether your laptop being on is a constraint.

## The Five Routines I Would Build Next

If I had the weekend back and I wanted to get the most leverage out of Routines before the cap kicks in, here is the list, ranked by ROI-per-setup-minute.

**1. Morning email triage + drafts.** The one I already built. If you do nothing else, build this one. The time back is immediate and the failure modes are bounded.

**2. Weekly business reporting.** Stripe, Fiverr, whatever your revenue surfaces are, pulled every Monday morning, summarized as a trend, posted to a private Slack channel. The compound benefit is that you stop thinking "I should look at the numbers" because the numbers come to you. For sole operators this is arguably more valuable than the email triage.

**3. Inbound lead auto-triage with personalized first-response drafts.** Webhook trigger from your website contact form. Claude pulls the message, researches the sender's company via their domain in about forty seconds, drafts a reply that references something real about their business, saves the draft, pings you in Slack. You review and send. Response times drop from hours to minutes without sacrificing the human touch that closes the lead.

**4. Daily changelog digest for a public repo.** GitHub trigger on every merge to main. Claude pulls the diff, writes a user-facing changelog entry in your voice, appends to CHANGELOG.md, opens a PR. You review weekly. Hours of documentation work per month, reduced to minutes of review.

**5. Research brief for tomorrow's meetings.** Schedule trigger daily at 8 PM. Claude reads your next day's calendar, pulls the attendees' LinkedIn headlines, recent public writing, and last email thread with each, produces a one-page brief per meeting, drops them in a Google Doc. Walk into every meeting tomorrow with context you did not have to spend 30 minutes gathering.

None of these are novel ideas. All of them have been technically buildable for years using cron plus a language model plus an API wrapper. The reason I had not built any of them is that every one of them required infrastructure I did not want to own — a server to run the cron, a wrapper to call the model, a queue to handle failures, a monitor to tell me when the queue was broken. Routines collapses that stack into one saved configuration that runs on someone else's infrastructure. The barrier to shipping was not the idea. The barrier was the yak-shave.

That is what is genuinely new here. Not the automation. The absence of the yak-shave. For a deeper read on what the broader April 2026 model landscape looks like around this launch, my [April 2026 AI model roundup](https://www.mejba.me/ai-model-tool-roundup-april-2026-kimi-spud-grok) has the full picture on Kimi K2.6, GPT-5.5 Spud rumors, and how Opus 4.7 actually sits against the rest of the field on real workloads.

## Frequently Asked Questions

### What are Claude Routines and how do they work?
Claude Routines are saved Claude Code configurations — a prompt, repos, connectors, and a trigger — that run on Anthropic's cloud infrastructure when a schedule, API call, or GitHub event fires. Your local machine does not need to be online. Each run starts a fresh Claude Code session with no memory of previous runs unless you explicitly tell the prompt where to fetch prior context. For the full setup walkthrough, see the Morning Email Triage section above.

### What is the minimum schedule interval for a Claude Routine?
One hour. Schedule triggers support four presets — hourly, daily, weekdays, weekly — and reject any cron expression that runs more frequently than once per hour. For sub-hourly cadence, use a webhook trigger fired by an external scheduler, though this reintroduces the external-infrastructure problem Routines are meant to eliminate.

### Is Opus 4.7 better than Opus 4.6 for agentic work?
Yes, meaningfully. On Anthropic's partner evaluations, Opus 4.7 made 56% fewer model calls and 50% fewer tool calls than Opus 4.6 for equivalent tasks, resolved roughly three times more production tasks on Rakuten's coding benchmark, and jumped from 58% to 70% on CursorBench. The new `xhigh` effort level is the default for 4.7 inside Claude Code and is the tier you want for multi-step unattended work.

### What is xhigh in Claude Opus 4.7?
`xhigh` is the new top-tier effort level in Opus 4.7 — the literal flag name used in the API and CLI. It allocates more tokens to internal reasoning and tool-exploration loops before the model reports back. It is the default effort level for Opus 4.7 in Claude Code and is recommended for complex reasoning and agentic work. It does cost more per task because of the deeper reasoning loop plus a new tokenizer, so expect 20% to 50% higher bills per equivalent task versus 4.6.

### How many Routines can I run per day on the Pro plan?
Five Routine executions per day on Claude Pro. Claude Max users get 15 per day. Team and Enterprise users get 25 per day. Overage billing can be opted into if you need more, but the caps are per-account totals across all your Routines, not per-Routine limits — so a Pro user running five separate daily Routines is already at the cap.

### Should I use trusted or full tool mode for a new Routine?
Trusted mode, always, for a new Routine. Trusted mode limits Claude to the specific tools you have individually approved for that Routine. Full mode lets Claude reach for any connector on your account mid-run, which is useful for the most ambitious workflows but is also how unattended agents end up doing things you did not intend. Start in trusted mode, run three clean executions, audit the tool calls, and promote to full mode only if you can name a specific capability trusted mode cannot reach.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
