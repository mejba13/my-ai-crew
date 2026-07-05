**BRAND:** mejba.me
**TITLE:** Loop Engineering: How I Built My First Loop
**META TITLE:** Loop Engineering: Build Your First AI Agent Loop
**SLUG:** loop-engineering-build-your-first-loop
**PRIMARY KEYWORD:** loop engineering
**META DESCRIPTION:** Loop engineering means designing self-running AI loops instead of prompting once. Here's the four-condition test, the four building blocks, and how I built mine.
**TAGS:** Loop Engineering, AI Automation, Claude Code, AI Agents, Workflow

---

I stopped prompting Claude two weeks ago. Not because I got lazy — because I finally understood that every prompt I typed was me doing a job the machine should be doing itself.

Here's the moment it clicked. I was on my fourth round of "okay now fix the spacing on the mobile breakpoint, run the visual test again, show me the screenshot." Fourth round. Same shape of instruction, same shape of reply, me sitting there like a human cron job feeding the agent its next line. And I thought: I am the loop. I am standing inside a `while` loop, typing the condition by hand, every single iteration.

That's the whole insight behind **loop engineering**, and it's the biggest shift in how I use AI since I learned to write a decent prompt in the first place. The idea spread through engineering Twitter in June 2026 and it has a name now because two people I pay attention to said the quiet part out loud. Boris Cherny, who heads up Claude Code at Anthropic, put it bluntly in a CNBC interview: "I don't prompt Claude anymore. I write loops and the loops do the work. My job is to write loops." Peter Steinberger — the guy who bootstrapped PSPDFKit to a €100M exit, built the open-source agent OpenClaw to 180,000+ GitHub stars, and now engineers at OpenAI — said you shouldn't be prompting coding agents anymore, you should be designing loops that prompt your agents. Addy Osmani then wrote the long post that gave the discipline its skeleton.

I'm not going to summarize their posts and call it a tutorial. I'm going to show you how I actually built my first working loop, what broke, and the exact framework I now use to decide whether a task even deserves a loop. Three parts: what a loop really is, the four pieces every loop needs, and a build-along you can follow even if you've never written a line of code. By the end you'll have the mental model and a concrete first project.

## What a loop actually is (and what it is not)

A normal prompt runs once. You ask, it answers, the transaction is over. If the answer is wrong, *you* notice, *you* type the correction, *you* press enter again. The intelligence doing the looping is you.

A loop runs until a goal is met. You define the goal once, you give the agent a way to check its own work, and then it iterates — act, verify, act, verify — without you babysitting each turn. The agent finds the next piece of work, does it, checks whether it's done, records what happened, and decides whether to keep going or stop.

The cleanest way I've found to explain the difference: a prompt is a question, a loop is a thermostat. You don't walk over to the heater every five minutes and decide whether to nudge it. You set the target temperature once and the system drives toward it on its own, correcting as the room changes. Loop engineering is building the thermostat instead of being the thermostat.

This is not the same thing as a longer prompt or a multi-step agent run. An agent that does ten steps and stops is still a single run — impressive, but one-shot. A loop is a recursive goal: it keeps re-entering itself until a stopping condition is satisfied. That stopping condition is the entire game, and most people skip it, which is exactly why their "automations" either quit too early or run forever burning tokens on nothing.

Here's the catch that took me an embarrassingly long time to internalize: **not every task should be a loop.** A loop is powerful and a loop is expensive, and pointing it at the wrong task is how you wake up to a $40 bill for a job you could have done in one prompt. So before I build anything, I run a candidate task through a four-condition test.

## The four-condition test: does this task even deserve a loop?

I learned this the hard way by building loops for tasks that didn't need them. Now nothing gets a loop until it passes all four conditions. If it fails even one, I write a normal prompt or a one-shot agent run and move on.

**Condition 1 — The task repeats.** A loop only earns its complexity if the task happens many times, not once. "Refactor this one file" is a prompt. "Keep refactoring files until the whole module passes lint" is a loop. If you'd only ever run it a single time, the orchestration overhead isn't worth it. Repetition is what justifies building the machine instead of just doing the job.

**Condition 2 — There's a clear definition of done.** You must be able to state exactly when the task is finished, and the agent must be able to check it. "Make the site faster" fails this — faster than what, measured how? "Every page loads in under two seconds in Lighthouse" passes. If you can't write the stopping condition as something checkable, the loop has no idea when to quit, and an agent with no off switch is just an expensive way to drift.

**Condition 3 — Some wasted work is acceptable.** Loops burn tokens. They re-run, they re-check, they occasionally redo something that was already fine. That's the cost of autonomy. The task has to tolerate that — either the value is high enough to justify the spend, or you cap it (run for 20 iterations max, run only on a schedule, run on a cheaper model). If every token is precious and every retry is a problem, don't loop.

**Condition 4 — The tools to verify exist.** This is the one people forget. The loop needs a way to actually *check* its own output, not just produce it. For code that's easy — you can compile it, run the tests, take a screenshot, hit the endpoint. Steinberger's framing is the sharpest version of this: "Code works well with AI because it's verifiable. You can compile it, run it, test it. That's the loop. You have to close the loop." If you can't give the agent a verification tool, it's working blind, and a blind loop will confidently declare victory over broken work.

Run your candidate task through those four. Repeats, has a clear done state, tolerates some waste, has verification tools. All four green? Build the loop. Any red? You just saved yourself money and a debugging session.

## The four building blocks of a working loop

Every loop I've built that actually works has the same four parts. Skip one and it breaks in a predictable way. I'll tell you the failure mode for each so you know what you're looking at when it goes wrong.

### Block 1 — The trigger

Something has to start the loop. On your local machine, that's `/loop` in Claude Code — it's built for exactly this, quick polling and re-running a prompt during a session. For work that should run reliably whether or not your laptop is open, that's `/schedule`, which creates cloud scheduled tasks: `/schedule every weekday at 9am: check the CI dashboard and summarize any failures`. Claude Code gives you three flavors here — cloud tasks for reliability without your machine, Desktop tasks when the job needs your local files and tools, and `/loop` for quick in-session polling. For anything more involved, you wrap the whole thing in a custom orchestration skill that you kick off with a single command.

The trigger is the cheapest block to get right and the easiest to over-engineer. Start with `/loop` and a manual kickoff. You do not need a cron schedule on day one.

*Failure mode if you skip it:* you don't have a loop, you have a prompt you keep retyping. Which is where we started.

### Block 2 — The execution skills

This is the part that separates a reliable loop from a dice roll. The loop should not be improvising how to do the task each iteration. It should call **skills** — pre-written, battle-tested instructions saved as `SKILL.md` files that tell the agent exactly how to perform a specific job. The orchestration layer decides *what* to do next; the skills define *how* each thing gets done.

This matters because consistency is the whole point of a loop. If the agent reinvents its approach every iteration, you get drift — twenty slightly different solutions to the same problem. A good execution skill locks the method so iteration 50 works the same way iteration 1 did. I wrote a whole piece on why this works in [the Claude Skills guide decoded](https://www.mejba.me/blog/claude-skills-guide-decoded), and the short version is: skills give the model judgment, not just access. A loop without skills has access and no judgment, which is exactly the new-hire-with-admin-rights problem.

Build your skills first, from things you already trust. Don't write a loop on top of an untested skill — you'll be debugging two new things at once and won't know which one is lying to you.

*Failure mode if you skip it:* the loop works on Tuesday and produces garbage on Wednesday, because nothing pinned down the method.

### Block 3 — The goal and its verification

Every loop pairs a goal with a way to confirm the goal is met. These are two separate things and you need both. The goal says what done looks like. The verification proves it's actually done. Without verification, the agent grades its own homework, and language models are relentless optimists about their own work.

For technical tasks this is concrete: the goal is "the landing page loads in under two seconds and passes the visual regression test," and verification is the agent actually running Lighthouse and the test suite and reading the result — not asserting it from vibes. I lean on a separate check here whenever I can, because an agent verifying its own output has an obvious bias. A second agent, a plugin, or a plain test runner checking the work reduces that bias a lot.

For non-technical or fuzzy goals — "write a draft that sounds like me," "make this argument tighter" — you can't compile the thing. So you break the goal into smaller checkpoints that *are* checkable, or you point a separate review skill at the output to score it against criteria. The trick for anything you can't measure directly is to decompose it until each piece has a yes/no answer. Vague goals are where loops drift hardest; specific checkpoints are the guardrails.

*Failure mode if you skip it:* the loop confidently announces success over work that's broken, because nothing ever checked.

### Block 4 — The output and memory

The loop produces something, and it has to *remember* what it produced. This is the block beginners skip and then can't figure out why their loop keeps redoing finished work or repeating the same mistake.

Language models don't retain memory between runs. Osmani's definition is the one I keep coming back to: external memory is anything that exists outside a single conversation, used to record what was done and what the next step is. Because the model forgets the moment a run ends, progress has to live somewhere outside it. The good news is it doesn't need to be fancy — a markdown file, a log, a checklist the loop reads at the start and updates at the end. The line I have taped to this whole idea: *the agent forgets, the repo doesn't.*

Memory is also what makes a loop get *better*. When each run records its outcome, the next run can read past attempts, skip what's done, and avoid the mistake it made last time. No memory means no learning and a lot of duplicated tokens. I treat the memory file as a first-class part of the design now, not an afterthought — it's the same instinct behind building [an AI operating system instead of one-off prompts](https://www.mejba.me/blog/grill-me-skill-claude-code-knowledge-extraction).

*Failure mode if you skip it:* the loop has amnesia — it redoes completed work, repeats errors, and never improves.

Put the four blocks side by side and the architecture is obvious: the trigger starts it, the skills do it, the verification checks it, and the memory remembers it. Every reliable loop I've built has all four. Every flaky one was missing exactly one — and once you know the four failure modes, you can diagnose a broken loop in about thirty seconds by asking which block went quiet.

## Building your first loop, step by step

Here's the build-along. You don't need to be a developer for the thinking part, though you'll want Claude Code or a similar agent to run the technical version. I'll keep it concrete.

### Step 1 — Start from a skill you already trust

Do not start by writing a loop. Start by picking one small, proven skill — something you've already run by hand a few times and know works. Maybe it's a skill that formats a blog draft to your house style, or one that runs your test suite and summarizes failures. The loop is going to call this skill over and over, so it has to be solid before it gets automated. A loop is a force multiplier, and a force multiplier on a shaky skill just multiplies the shakiness.

### Step 2 — Run your task through the four-condition test

Before you build anything, check: Does it repeat? Is "done" clearly defined and checkable? Can you tolerate some wasted tokens? Do you have the tools to verify the result? If all four are yes, continue. If any is no, stop and write a normal prompt — you'll thank yourself.

A good first candidate: "Go through every blog post in this folder, check each one against my formatting skill, fix the ones that fail, and stop when all of them pass." That repeats (many posts), has a clear done state (all pass), tolerates waste (a re-check is cheap), and is verifiable (the formatting skill is the check). Perfect first loop.

### Step 3 — Wrap it in an orchestration skill

This is the part that sounds intimidating and isn't. You write one more skill — the orchestrator — whose job is to run the loop: find the next item, call the execution skill on it, check the result, record the outcome, decide whether to continue. The point of the orchestration skill is that it hides the complexity. Once it exists, you run the entire loop with a single command instead of managing each turn.

You don't have to write that orchestration logic from scratch. Describe the loop you want to Claude Code in plain language — the goal, the execution skill to call, the stopping condition, where to write memory — and have it generate the orchestration skill for you. Then you read it, sanity-check it, and save it. You're directing the build, not hand-coding the control flow.

### Step 4 — Run it in loop training mode first

This is the single best habit I picked up and it has saved me real money. Before you let a loop run free, run it in **training mode**: pause after each step and approve the output before the next step fires. You watch the first iteration, confirm the agent understood the task and verified correctly, then approve. Watch the second. Approve. A few clean iterations and you trust it enough to let it run.

Training mode catches the expensive mistakes — a misunderstood goal, a verification that isn't actually checking anything, a memory file that isn't being written — before they compound across fifty iterations. Letting an unverified loop off the leash is how the horror-story token bills happen. Three minutes of supervised stepping prevents almost all of them.

### Step 5 — For fuzzy goals, break the task into checkpoints

If your loop's goal can't be measured with a test — anything involving taste, voice, or judgment — don't hand the agent one big abstract target and hope. It will drift. Decompose the goal into smaller checkpoints that each have a yes/no answer, and have the loop clear them one at a time. "Make this essay better" becomes "tighten every paragraph over 120 words," "cut every banned filler phrase," "confirm each section delivers one idea." Each of those is checkable. Stacked together they add up to the fuzzy goal, without the drift.

And where you can, let a *different* agent or plugin do the checking. Self-verification is biased toward declaring success. A second set of eyes — even an automated one — keeps the loop honest. This is the same parallel-verification instinct I leaned on in [running Claude Code and Codex in parallel](https://www.mejba.me/blog/for-goal-claude-code-codex-parallel-build): two systems checking each other beat one system marking its own paper.

## Why this is a real shift and not a buzzword

I've watched enough AI trends come and go to be skeptical of anything that trends for a week. Loop engineering earned my attention because it changed what I actually do all day, not just what I tweet about.

The shift is from *prompting once* to *designing a self-correcting process*. The skills you build are the muscle. The verification is the conscience. The memory is the long-term learning. And the trigger is just the ignition. Put those four together and you stop being the slow human in the loop and start being the person who designs loops — which is a much better job, and the one Cherny was describing when he said his job now is to write loops.

You don't need to start big. Start with one proven skill, one task that passes the four-condition test, training mode on so you don't get burned, and a markdown file for memory. Get one loop running end to end and the whole paradigm stops being abstract. Then you optimize: tighten the verification, enrich the memory, raise the iteration cap. That's the path I took, and two weeks in, I'm not going back to typing the condition by hand.

The machine was always capable of running the loop. I was just standing in the way.

---

*Building your own AI workflows and want them to actually run without you babysitting every turn? I write about this stuff — agents, skills, loops, and the messy reality of making them reliable — at [mejba.me](https://www.mejba.me). Come build with me.*
