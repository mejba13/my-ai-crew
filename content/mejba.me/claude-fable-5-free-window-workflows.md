**BRAND:** mejba.me
**TITLE:** Claude Fable 5: 8 Ways to Use the Free Window
**META TITLE:** Claude Fable 5: 8 Workflows Before Free Credits End
**SLUG:** claude-fable-5-free-window-workflows
**PRIMARY KEYWORD:** Claude Fable 5
**META DESCRIPTION:** Claude Fable 5 is back with a free 50% usage window through July 7. Here are the 8 workflows I'm running now, from code review to /loop security audits.
**TAGS:** Claude Fable 5, Claude Code, AI Workflows, Automation, Practitioner Guide

---

You have until July 7. That's the part nobody is saying loudly enough.

Claude Fable 5 came back on July 1, 2026, after three weeks of being completely dark, and Anthropic didn't just quietly re-enable it. For everyone on Pro, Max, Team, and select Enterprise plans, Fable 5 is included for **up to 50% of your weekly usage limit — free — through July 7**. After that, it moves to usage credits at API pricing, and Fable 5's API pricing is not cheap. So there's a five-day window where the most capable model Anthropic ships is effectively sitting in your account waiting to be used, and most people are going to burn it on the same throwaway prompts they'd send to any model.

I don't want to do that, and I don't want you to either. So I spent the first day it came back mapping out exactly where Fable 5 earns its keep versus where I'd be wasting a free tank of the good stuff. This is that map. Eight workflows, ordered roughly by how much leverage they give you per token, with the setup details for each one and an honest note on where I'd pump the brakes.

Let me back up for a second, because the *why* behind this window matters for how you should treat it.

## What actually happened, and why the free window exists

If you tried to use Fable 5 or Mythos 5 in mid-June and hit a wall, you weren't imagining it. On June 12, the U.S. government applied export controls to both models — the concern being jailbreak-assisted misuse — and because the order took effect immediately with no reliable way to verify a user's nationality in real time, Anthropic suspended access to both models for *everyone*. Not throttled. Off.

Then on June 30 the Trump administration lifted the controls, and Fable 5 shipped back globally the next day across the Claude Platform, Claude.ai, Claude Code, and Claude Cowork. I wrote up the messier context of that return — the distillation claims, the talent shuffles, what's actually real versus press-release noise — in my [breakdown of that wild AI week](/ai-news-fable-5-return-distillation-deepmind-june-2026). This post is the opposite of that one. That was analysis. This is a to-do list.

Here's the thing about the free window that changes how you should use it: the model comes back under noticeably tighter safety protocols. Fable 5 now falls back to Opus automatically for tasks it reads as disallowed or borderline — anything that smells like offensive security, unethical requests, that whole category. In my first day poking at it, that fallback triggered more often than I expected, sometimes on legitimate defensive-security work that just *looked* aggressive to the classifier. I'll come back to what that means for the security workflows below, because it's a real constraint, not a footnote.

So the game is simple. Between now and July 7, spend your free 50% on the handful of jobs where Fable 5's ceiling is genuinely higher than Opus — and don't waste it on the stuff Opus already handles fine. Let's go through where that line actually falls.

## 1. Point it at every line of code your weaker models shipped

This is the first thing I did, and it's the one I'd tell you to do before anything else.

Think about how much code you've merged over the last month that was written by a model that isn't your best one. In my case that's a lot — I run most day-to-day work through Opus and cheaper models because until this week that was the ceiling I had. All of that code is *fine*. It passes tests. It ships. But "passes tests" and "the best version of this that could exist" are very different bars.

So I did a bulk review pass. I pulled the diffs from my recent merged pull requests and pointed Fable 5 at them in Claude Code with a single instruction: *find the optimizations, the security holes, the places where a stronger model would have made a different call.* Not "does this work" — it already works. "Where is this leaving quality on the table."

A few specifics that matter for the setup:

- **Use the `high` effort setting for this, not `ultra`.** Code review is a reasoning task with a clear target, and in my testing `high` gave me the depth I wanted without torching my free allowance on marginal gains. Save `ultra` for genuinely open-ended problems where you need the model to explore.
- **Scope it to real diffs, not the whole repo.** Feeding it your entire codebase burns tokens on files nobody touched. Feed it the last N merged PRs and let it focus.
- **Ask for a ranked list with reasoning, not just fixes.** "Rank these by impact and tell me why" turns the output into something you can triage in ten minutes instead of a wall of suggested edits.

Whether Fable 5 is "10x better at code" the way some of the launch chatter claims — I can't measure that cleanly, and neither can anyone throwing that number around. What I *can* say is that on the diffs I fed it, it caught a class of issues Opus had waved through: subtle N+1 query patterns, a couple of missing authorization checks on endpoints that "worked," and some genuinely nicer refactors of logic I'd stopped looking at. That alone paid for the exercise.

Do this first because it's the highest-certainty win. You already have the code. The model is free this week. The output is directly shippable.

## 2. Let it test your app in a real browser the way a confused user would

Here's a workflow that felt like cheating the first time it ran.

Fable 5 can drive an actual Chrome browser through the Claude for Chrome extension — reading the page, clicking, navigating, filling forms, the whole flow — which means it can walk through your app's user journey like a real person and then tell you where that person got lost. Not unit tests. Not "does the button exist." Actual "I tried to sign up and the flow confused me at step three" feedback, generated by something that just did the flow.

The extension went into beta for all paid plans earlier this year, and Anthropic did real work on the safety side — they report cutting browser-specific attack success rates from 35.7% down to 0% through layered protections, with hard blocks on financial and sensitive-data entry. That matters, because handing a model control of a live browser is exactly where things go wrong if the guardrails are thin. If you want the deeper background on how browsers are being rewired for AI agents generally, I got into that in my [piece on WebMCP and Chrome](/webmcp-chrome-ai-agents).

For UX testing specifically, here's how I run it:

1. **Install the Claude for Chrome extension** and grant it site permissions for your own app — start with "Ask Before Acting" so you approve the plan before it executes, then loosen to "Act Without Asking" once you trust it on a given site.
2. **Drive it from Claude Code on desktop, not the CLI.** Managing a browser session, watching what it does, and iterating on the prompt is genuinely easier in the desktop app. I tried it CLI-first and kept losing the thread of what the agent was seeing.
3. **Give it the paths explicitly.** "Go through signup, then onboarding, then create your first project, then invite a teammate — and at each step, note anywhere a first-time user would hesitate."
4. **Ask for a report, then iterate.** Get the friction report, fix the top one or two things, and run it again. The second pass tells you whether your fix actually helped or just moved the confusion somewhere else.

The honest caveat: this reads *perceived* friction, not conversion data. It can't tell you that removing a form field lifts signups by 8% — only that the field felt unnecessary. Pair it with real analytics, don't replace them. But as a way to catch the obvious stuff before you ship, having a fresh set of eyes that never gets used to your own UI is worth a lot.

## 3. Use it as the business strategist it's weirdly good at being

I was skeptical of this one. "AI business consultant" is a phrase that usually means generic MBA mush. Fable 5 changed my mind, and the trick is entirely in the prompt.

Don't ask it for business ideas. Ask it to *interview you first.*

The prompt I use goes something like: *"Before you recommend anything, interview me. Ask me one question at a time about my goals, my ambitions, what I actually enjoy doing versus tolerate, who my audience is, what skills and assets I already have, and how much time and money I can put in. Keep asking until you genuinely understand my situation. Then, and only then, give me tailored product and business recommendations with a rough plan for each."*

That one structural change — interview before advice — is the difference between a listicle you could've generated in 2023 and a plan that actually fits your life. When the model has to earn its recommendations by understanding your constraints, the output stops being generic. It knows you have a design skill you're underusing, that you hate cold outreach, that you've got a small existing audience in a specific niche. The recommendations bend around that.

The person whose workflow this is based on credits this exact interview-first approach with quitting his job and building several online businesses. I'm not going to repeat that as a promise, because your mileage is your mileage and a chatbot didn't build his company — he did. But I'll say this: as a *thinking partner* for figuring out what to build, it's the best I've used, and Fable 5's reasoning ceiling makes the interview genuinely sharp instead of a scripted quiz. If you want a grounded look at what businesses actually pay real money for, I mapped that out in [5 AI automations businesses actually pay for](/ai-automations-businesses-pay-for) — pair the two and you've got both the "what should I build" and the "what will people buy" halves.

## 4. Schedule a daily security audit with /loop — the Batman move

This is my favorite one, and it's the workflow where Fable 5's cost and its capabilities pull in opposite directions in an interesting way.

`/loop` is the bundled skill in Claude Code that runs a prompt on a repeating interval. You give it a cadence and a task, and it re-runs that task every interval while the session is alive. I wrote a full walkthrough of it in [Claude Code Loop: cron scheduling inside your IDE](/claude-code-loop-cron-scheduling), so I won't re-explain the mechanics here — but the short version is `/loop 24h [your prompt]` gives you a task that fires once a day.

The security application is the good part. Point a daily loop at your API:

> `/loop 24h Scan every API endpoint in this project for vulnerabilities — auth gaps, injection risks, exposed data, broken access control. Report anything new since yesterday.`

Now you have a security guard that never sleeps, quietly checking your endpoints every 24 hours and flagging regressions the moment they appear. It's the closest thing your app has to Batman — patient, always watching, only shows up when something's wrong.

Three things you need to know before you lean on this:

- **Once a day is the right cadence for Fable 5, not more.** Given the cost after the free window, a scan every few minutes would be lighting money on fire for almost zero added safety. Daily catches regressions fast enough for the vast majority of apps.
- **`/loop` is session-scoped and expires.** The loop lives in your current Claude Code session and stops when the session ends; recurring tasks also auto-expire seven days after creation as a safety catch. For a *permanent* watchdog that survives your laptop closing, you'd graduate to Routines running on Anthropic's cloud — but for the free window this week, a session loop is perfect for proving the pattern.
- **Watch the Opus fallback here.** Remember that tightened-security behavior I mentioned? Defensive scanning sometimes trips it, and Fable 5 hands off to Opus for the "iffy" bits. In practice that's fine — Opus is a strong security reviewer — but don't be surprised if part of your audit is quietly running on a different model. Frame your prompt as *defensive* ("find and report vulnerabilities so I can fix them") to keep it on the right side of the classifier.

The person whose approach this mirrors credits a daily security loop with zero security incidents over a year and a half on a live app. I can't verify someone else's incident log, so take it as an anecdote rather than a guarantee — but the underlying logic is sound. If you want something more robust than a prompt, I built a dedicated [Claude Code security scanner agent for OWASP](/claude-code-security-scanner-agent) that packages this as a proper Skill plus sub-agent, and you can wire *that* into the loop instead of a raw prompt.

## 5. Mine Twitter/X for product ideas with XMCP

This one got a lot more interesting on June 30, when X launched its official hosted MCP server — XMCP — exposing over 200 X API endpoints as tools any MCP-compatible agent can call. That's the same day Fable 5 came back, which is a nice coincidence for anyone doing market research this week.

The idea: connect XMCP to Claude Code, authorize it, and now Fable 5 can read your tweet history and the broader conversation on X as live data. What do you do with that?

- **Analyze your own history for aligned product ideas.** "Look at everything I've tweeted over the last year. Based on what I clearly care about and what my audience responds to, what SaaS or app ideas would fit *me* specifically?" It surfaces patterns you can't see because you're too close to your own timeline.
- **Find what people are actually asking for.** "Search X for people complaining about [problem in my niche] in the last month. What are the recurring pain points, and which ones look like a product?" That's real-time customer research pulled straight from the source instead of guessed at.

One honest thing to flag on cost, because it stacks on top of Fable 5's pricing: the X API itself moved to pay-per-use in January 2026 — reading a tweet runs about $0.005, a lookup or post around $0.01 — and the hosted MCP routes through that same meter. So a broad research session that reads thousands of tweets has its own bill *separate from* your Claude usage. Scope your searches tightly. Ask for the specific thing, not "read everything about my industry." This is data-driven ideation, and the discipline is in reading exactly what you need and no more.

## 6. Build an actual 3D game with Unreal Engine 5.8 MCP

If you've ever generated a browser game with Three.js and thought "this is cool but it looks like 2011," this is the upgrade, and it went from impossible to plausible for non-specialists almost overnight.

Unreal Engine 5.8 shipped on June 17, 2026, and the headline feature for our purposes is the Unreal MCP plugin — it embeds an MCP server *inside* the Unreal Editor so an agent like Claude Code can actually drive the editor. Not "look at a screenshot and suggest things." Drive it: spawn actors, edit Blueprints, author materials, build UI widgets, script animation sequences, configure Niagara VFX, run automation tests — hundreds of tools across 30-plus toolsets, all callable from your terminal.

The rough setup, from the Epic docs:

1. Enable the **ModelContextProtocol** and **AllToolsets** plugins in Unreal Editor.
2. Start the server from the console with `ModelContextProtocol.StartServer`.
3. Point Claude Code at the local MCP endpoint, and now Fable 5 can build inside your editor.

From there you describe what you want in plain English — the character, the weapons, how they behave, the level layout — and the model generates the game logic and wires it into the engine. The visual jump from a Three.js scene to a real UE5.8 project is not subtle; you get proper lighting, materials, and geometry instead of flat-shaded primitives.

Now the real-talk part, because I don't want to oversell this. "No technical expertise needed" is the marketing line, and it's genuinely more true than it's ever been — but "I described a game and Fable 5 built it" and "I shipped a good game" are separated by a large amount of iteration, taste, and debugging. What this actually removes is the *engine-fluency* barrier — you no longer need to know Blueprints cold to place actors and wire logic. What it doesn't remove is game design. Treat it as the most capable junior engineer you've ever had inside Unreal, not as a game studio. With that framing, it's genuinely one of the most fun things you can point your free Fable 5 credits at this week.

## 7. Reverse-prompt your own day into an automation list

This is the smallest workflow here and possibly the highest return on effort, and I've started running it weekly.

At the end of a working day, write down everything you did. Every email you answered, every report you pulled together, every repetitive thing you clicked through, every "I do this every Tuesday" chore. Don't filter — just dump the list.

Then hand the whole thing to Fable 5 and ask: *"Here's everything I did today. For each item, tell me how you could automate it, manage it for me, or do it better than I did. Be specific about what you'd need from me to take it over."*

I call it reverse prompting because you're not asking the model to do a task — you're asking it to find the tasks. And a strong reasoning model is *good* at this. It'll spot that three of your items are really one workflow, that a chunk of your email is templatable, that the report you build by hand every week could be a scheduled job. It reads your day and hands you back an automation backlog, ranked.

The reason this belongs in the free-window list specifically: the quality of the suggestions scales hard with the quality of the model. A weaker model gives you obvious "use a calendar reminder" advice. Fable 5's ceiling means it connects dots across your whole day and proposes automations you wouldn't have thought of. Run it once this week and you'll likely find a couple of things worth building — some of which are the exact [automations businesses will pay you to build for them](/ai-automations-businesses-pay-for), not just for yourself.

## 8. Treat the free window itself as the workflow

The eighth move isn't a task — it's a strategy, and it's the one that decides whether the other seven actually pay off.

You have 50% of your weekly usage on Fable 5, free, until July 7. After that, continuing means manually enabling extra usage in settings and depositing money that converts to usage credits, billed at Fable 5's API rate — which is genuinely expensive. So the correct way to think about this week is: *what's the highest-value work I can front-load into a free tank of the best model?*

That means being deliberate. Don't send Fable 5 the prompts Opus handles fine — quick edits, boilerplate, routine questions. Every one of those spent from your free allowance is a code review or a security audit you didn't run. Batch the Fable-5-worthy work: the deep code passes, the strategic thinking, the UX audit, the game prototype you've been putting off. Then let Opus carry the routine load.

The person whose workflow inspired a lot of this reportedly bought $1,000 of credits for $700 to keep going past the free window, betting that the revenue edge is worth it. I can't confirm that specific deal, and I'd caution against copying a spend decision from a stranger's video — but the underlying logic is worth sitting with. If Fable 5 genuinely helps you ship a product or land work, the credits pay for themselves. If you're using it to summarize articles, they don't. **The model's value is entirely a function of what you point it at.** This week, for free, is your chance to find out which of those two you are — before there's a price tag attached.

## Where I'd actually pump the brakes

I've spent this whole post telling you to use the thing, so let me be straight about the friction, because a workflow list that pretends there's no downside is just an ad.

**The Opus fallback is real and it's frequent.** Fable 5 hands off to Opus for anything the safety layer reads as borderline, and after this return the classifier is tuned tight. I hit it on legitimate defensive-security work more than once. This isn't a dealbreaker — Opus is excellent — but if you're specifically paying for Fable 5's ceiling on a security task and it quietly routes to Opus, you're paying premium for a model you could've used directly. Watch which model is actually answering.

**The cost cliff after July 7 is steep.** This is not a "well, it's a bit pricier" situation. Fable 5 at API pricing is expensive enough that casual use will drain a credit balance fast. If you enable extra usage, set a mental budget before you deposit, not after.

**Not everything needs the best model, and treating Fable 5 as your default is how you go broke.** The whole discipline here is triage. The eight workflows above are worth it *because* they're high-leverage. Your average prompt is not.

And the honest meta-point: a lot of the boldest claims flying around this launch — the exact performance multipliers, the "it built my whole business" stories — are unverifiable, and I've tried to flag them as such throughout rather than launder them into facts. Use the free window to build your *own* evidence about where this model helps you. That's the only benchmark that matters for your work.

## What to measure this week

Before July 7 hits, run at least three of these eight and track something concrete for each, so you actually know whether Fable 5 earns a credit purchase or not:

- **Code review:** How many real issues did it catch that your normal model missed? Count them.
- **Security loop:** Did it flag anything a static scan didn't? One real finding justifies the pattern.
- **Business interview / reverse prompting:** Did you walk away with a build you're actually going to start? Yes or no.
- **UX testing:** Did the friction report change something you shipped?

If two of those come back positive, Fable 5 is a tool worth paying for in your workflow. If none do, you just saved yourself a credit deposit — and you learned that for free, which is exactly what the window is for.

That's the whole point of this week. Not to marvel that Fable 5 is back. To find out, on Anthropic's dime, whether the best model they make is the best model *for you* — while the answer still costs you nothing.

Five days. The code you'd review, the app you'd audit, the game you'd prototype — they're not getting cheaper on July 8. What are you pointing it at first?

## Frequently Asked Questions

### Is Claude Fable 5 free right now?

Through July 7, 2026, Fable 5 is included free for up to 50% of your weekly usage limit on Pro, Max, Team, and select Enterprise plans. After that window, continued use requires enabling extra usage in settings and paying via usage credits at Fable 5's API pricing, which is expensive. Plan your heaviest Fable 5 work before the deadline.

### What platforms is Claude Fable 5 available on?

Fable 5 returned July 1, 2026, across the Claude Platform, Claude.ai, Claude Code, and Claude Cowork, available globally after U.S. export controls were lifted on June 30. For the setup-heavy workflows like browser UX testing and Unreal Engine game building, the Claude Code desktop app is the most comfortable place to run it.

### Why does Claude Fable 5 sometimes switch to Opus?

Fable 5 automatically falls back to Opus for tasks its tightened safety layer reads as disallowed or borderline — including some legitimate defensive-security work that just looks aggressive to the classifier. It's expected behavior after this return. Frame security prompts explicitly as defensive to reduce how often the handoff triggers. See the [/loop security audit section](#4-schedule-a-daily-security-audit-with-loop--the-batman-move) above.

### Is Claude Fable 5 worth paying for after the free credits end?

It depends entirely on what you point it at. For high-leverage work — deep code review, strategic planning, security audits — the ceiling can justify the cost. For routine prompts that Opus handles fine, it doesn't. Use the free window through July 7 to run real tasks and measure the results before depositing any money into credits.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
