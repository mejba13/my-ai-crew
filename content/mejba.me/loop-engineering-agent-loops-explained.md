**BRAND:** mejba.me
**TITLE:** Loop Engineering: How to Design Agent Loops
**META TITLE:** Loop Engineering: How to Design Agent Loops Right
**SLUG:** loop-engineering-agent-loops-explained
**PRIMARY KEYWORD:** loop engineering
**META DESCRIPTION:** Loop engineering is the new discipline behind autonomous agents. I break down trigger/action/stop-condition anatomy — and when a loop is the wrong tool.
**TAGS:** AI Agents, Claude Code, Loop Engineering, Agent Architecture, AI Automation

---

The first time I built a loop with no real stop condition, it cost me $12 and ran for 28 minutes before I killed it. The agent wasn't broken. It did exactly what I told it to: keep going until the task is "complete." The problem was I never defined "complete" in a way a machine could check. So it kept generating, kept refining, kept burning tokens — agreeing with itself on repeat while my wallet quietly emptied. That single bad run taught me more about loop engineering than any tutorial.

Here's the shift that's happening right now, and most builders haven't caught up to it yet. The skill that matters in 2026 isn't writing a clever prompt. It's writing the *loop* that prompts the model for you — and knowing exactly how that loop decides it's done. Boris Cherny, the creator and head of Claude Code at Anthropic, put it bluntly on stage at Acquired Unplugged on June 2, 2026: "I don't prompt Claude anymore. I have loops running that prompt Claude and figuring out what to do. My job is to write loops."

That's not a throwaway line. That's a job description changing in real time.

A quick note before we go deep, because two things share the same word. If you want the hands-on, this-is-what-broke review of the *skill* that productizes loop-building, I covered that separately in my breakdown of [Anthropic's Launch Your Agent skill and the runaway agent run it produced](https://www.mejba.me/launch-your-agent-claude-code-skill-tested). That post is a tool review. This one is about the engineering underneath — the discipline of designing the loop itself, whichever tool you pour it into. The two are adjacent, not the same. Read this one for the *why* and the *shape*; read that one for the *how it felt to run*.

By the end of this, you'll be able to design a loop with a real, checkable stop condition — and, just as important, you'll know when a loop is the wrong tool entirely. That second part is where most people get burned.

## What is loop engineering?

**Loop engineering is the practice of designing the trigger, action, and stop condition of an autonomous agent loop so it can run, verify its own work, and stop on objective criteria — instead of relying on a single human-written prompt.** It treats the loop, not the prompt, as the unit of work.

Think about how you've been using AI coding agents. You write a prompt. You read the output. You write another prompt. You're the loop. Your eyeballs are the verification step, your judgment is the stop condition, and your fingers are the thing that re-triggers the next iteration. Loop engineering moves all three of those out of your head and into code.

Cherny described his own evolution in three stages, and it maps cleanly onto what most serious builders are living through. About a year ago, he wrote code by hand with autocomplete helping at the margins. Then he shifted to running five to ten Claude sessions in parallel, manually prompting each one — tab-switching like a short-order cook. Now he writes loops that prompt Claude for him; a couple hundred agents read his GitHub, his Slack, and his Twitter, and decide what to build next. The human went from typing code, to typing prompts, to typing the *machinery that types prompts*.

The term itself crystallized in early June 2026 — "write loops, not prompts" — and once you internalize the framing, you can't unsee it. Peter Steinberger, who built OpenClaw (the most-starred new repo in GitHub history), posted it even more starkly on June 7, 2026: "You shouldn't be prompting coding agents anymore."

Strong claim. Mostly right. But "mostly" is carrying weight, and we'll get to where it breaks.

## The reason → act → observe → evaluate cycle

Every agent loop, stripped to its bones, is the same four-beat rhythm running on repeat. The names vary — some people say Perceive/Decide/Act/Observe, some say Observe/Think/Act/Verify — but the music is identical. I think about it as: **reason → act → observe → evaluate the stop condition**, then loop back.

Reason: the agent looks at the current state and decides what to do next. Act: it performs an operation — writes a file, runs a command, calls an API. Observe: it captures what changed — the command output, the error, the new file contents, the screenshot. Evaluate: it checks that observation against the stop condition. Done? Exit. Not done? Reason again with the new information in hand.

The naming wars don't matter. What matters is the fourth beat. Most failed loops I've seen — including my $12 disaster — have a strong reason-act-observe cycle and a *fake* evaluate step. The agent observes its own output and asks itself, "Good enough?" And of course it says yes, because it's grading its own homework with no rubric. A loop with nothing to push back is just the agent agreeing with itself on repeat.

The whole game is making the fourth beat real.

That's why I now design loops backward. Before I write the trigger, before I write a single action, I write down the stop condition and I ask one question: *what concrete thing in the world will tell me this is done, that the agent cannot fake?* A test that passes. A type-checker that goes green. A CI pipeline that turns from red to passing. An HTTP 200 from an endpoint that was 500 a minute ago. If I can't name that thing, I don't build the loop yet. The loop isn't ready — the *definition* isn't ready.

So let's break the anatomy down properly, because each of the three parts has its own failure modes.

## Trigger, action, stop condition: the anatomy of an agent loop

A loop has exactly three load-bearing parts. Get any one wrong and the whole thing wobbles.

**The trigger** is what kicks the loop off. It can be a human command ("refactor this module"), a schedule (every fifteen minutes), an event (a new GitHub issue, a Slack message, a failing test in CI), or another agent handing off work. Cherny's couple-hundred-agent setup is triggered by his own activity — his commits, his messages, his posts become signals that agents pick up and act on. The trigger answers: *when does this loop have a right to exist and start spending tokens?*

**The action** is the set of operations the agent is allowed to perform inside each iteration. This is where you draw the blast radius. An action might be "edit files in this directory and run the test suite." It might be "generate an image and save it." The tighter and more specific the action space, the more predictable the loop. The vaguer it is — "do whatever it takes" — the more creative, and the more dangerous. Most token-burning runaways I've watched come from an action space that was too wide for the stop condition's ability to catch a wrong turn.

**The stop condition** is the verification gate — the objective criteria for "done." This is the part everyone underbuilds. It has to be something external to the agent's own opinion. Matthew Berman, who launched the Loop Library on June 18, 2026, frames verification cleanly: it can be "a unit test passing, a CI pipeline green, or an LLM saying 'yes, that's complete.'" Notice the order of trust there. A unit test is a fact. A green pipeline is a fact. An LLM judging completion is an *opinion* — useful, but the weakest of the three, and the one most likely to rubber-stamp slop.

Here's the design illustration I keep in my head. Say I want a loop that fixes failing tests in a repo. Trigger: a scheduled run, or a push that turns CI red. Action: read the failing test, edit the source, re-run the suite — and *only* those operations. Stop condition: the full suite passes, full stop. That last clause is the entire point. The loop cannot declare victory by telling itself the code looks correct. It declares victory when `npm test` exits zero. The test is the thing in the loop that can say *no*. Without something that can say no, you don't have a loop — you have an expensive yes-man.

That distinction — between a stop condition that's a fact and one that's an opinion — turns out to be the whole ballgame. Which brings me to the part nobody talks about enough.

## Why verification fidelity makes or breaks a loop

Not all stop conditions are created equal, and the gap between a good one and a bad one is the single biggest predictor of whether a loop produces something useful or something that merely *looks* finished.

I call this verification fidelity: how faithfully your stop condition measures the thing you actually care about. High fidelity means the gate checks the real goal. Low fidelity means the gate checks a proxy that's easy to satisfy and easy to fool.

Berman's Loop Library is a goldmine for seeing this in action, and I want to be precise here — these are loops *he and his contributors* built and battle-tested, not ones I personally ran. But they illustrate the fidelity problem better than anything I could construct.

Take his thumbnail-creation loop. The setup: generate ten thumbnails, score them against MrBeast-style reference thumbnails, iterate on the top three. Roughly 27 minutes per run. The trigger and action are crisp. But the stop condition? "Score them against MrBeast thumbnails." That's *subjective*. There's no `npm test` for "is this thumbnail compelling." The verification is an LLM looking at an image and forming an aesthetic opinion, and aesthetic opinions are squishy. The loop runs, it produces thumbnails, but the "done" definition is soft — which means the loop can converge on something the model *thinks* is good while a human creator might disagree entirely. Low fidelity isn't a bug in the code. It's a bug in what "done" means.

Now look at his three.js loop — building a 3D plane with three.js, around 37 minutes, with iterative visual verification by rendering in a browser. Higher fidelity than thumbnails, because the agent can actually *render the scene and look at it* each iteration. But it still didn't fully nail see-through transparency. The verification could confirm "a plane exists and renders," but "the transparency looks right" was harder to pin to an objective check. The loop got close. Close isn't done.

Then there's the Beatles Abbey Road recreation in HTML/CSS — capped at eight attempts, about seven iterations actually run, verified by screenshot comparison. It improved incrementally each pass and ended up *far from perfect*. And honestly, that example is the most instructive of the three, because it shows the loop's limit so plainly. Screenshot verification is medium fidelity: it can catch "the layout is broadly wrong" but struggles with "this specific gradient is slightly off." The hard cap of eight attempts is the unsung hero here — it's a *secondary* stop condition that prevents an infinite, unsatisfiable chase. When your primary verification is fuzzy, a hard iteration cap is what stops the loop from becoming my $12 disaster.

The lesson stacks up cleanly. Objective stop conditions (a passing test, an exit code, an HTTP status) produce loops you can trust to run unattended. Subjective stop conditions (does this look good, is this compelling) produce loops that need a human in the seat, or at minimum a hard attempt cap so they fail fast instead of failing expensive.

If you want one rule from this whole section: **match the loop's autonomy to its verification fidelity.** High-fidelity gate? Let it run. Low-fidelity gate? Cap the attempts and keep a hand on the kill switch.

So far we've talked about one agent in one loop. But the most powerful patterns — and the ones Cherny is actually running — involve agents checking *each other*.

## Maker-checker and nested fleets: scaling past a single loop

A single agent doing reason-act-observe-evaluate works fine for bounded tasks. The interesting architecture starts when you separate the *making* from the *checking* — and when loops start spawning loops.

The **maker-checker pattern** is the cleanest upgrade you can make to a low-fidelity loop. Instead of one agent both producing the work and judging whether it's done (the homework-grading problem), you split the roles. One agent makes — writes the code, generates the design, drafts the copy. A second, separate agent checks — scores it, grades it against criteria, hunts for the flaw. The checker's whole job is to find reasons to say *no*. Because it didn't produce the work, it has no ego invested in calling it finished. That separation is what gives the loop a real adversary, and a loop with a real adversary is a loop that can actually converge on quality.

I lean on this constantly now. When a stop condition has to be subjective — say, "is this API design clean" — I don't ask the maker to self-assess. I spin a second agent with a sharp rubric and a mandate to be harsh. The output quality jumps, because now there's something in the loop built to push back.

Then there are **nested fleets** — managers directing sub-agents, loops orchestrating loops. This is what Cherny's "couple hundred agents reading my GitHub and Slack and deciding what to build" actually is. A top-level loop observes his activity and reasons about priorities. It dispatches sub-loops to handle specific builds. Each sub-loop has its own trigger, action space, and stop condition, and reports back up. The manager loop's stop condition isn't "did I write code" — it's "did my fleet ship the right things." It's loops all the way down, with verification gates at every layer.

If you're trying to architect something at this scale, the orchestration patterns deserve their own deep treatment — I walked through how to structure managers, sub-agents, and the message-passing between them in my [breakdown of Claude Code agent swarm architecture](https://www.mejba.me/claude-code-agent-swarm-architecture). The short version: nested fleets multiply both your throughput *and* your failure surface. Every layer that lacks a real stop condition is a layer where slop can enter and propagate upward unnoticed.

And the harness underneath all of this matters more than the agents themselves. The way Anthropic designs its long-running agent harness — how state persists across iterations, how context gets managed so the loop doesn't drown in its own history — fundamentally shaped how I think about loop durability; I unpacked that in my piece on [Anthropic's agent harness design](https://www.mejba.me/anthropic-long-running-agent-harness). A loop is only as good as the harness it runs in.

If you're nodding along thinking "great, I'll loop everything" — stop. This is exactly where I have to apply the brakes, because the most important loop-engineering skill is knowing when *not* to build one.

## When NOT to write a loop

Here's the uncomfortable data that the "stop prompting, start looping" crowd tends to skip past. A 2025 survey of 306 practitioners found that **68% of production agents run ten steps or fewer before a human steps in.** Read that again. The agent systems that actually work in production aren't autonomous swarms of two hundred. They're small. They're supervised. They run a handful of steps and then a human takes the wheel.

That's not a failure of the technology. That's the technology being used by people who learned the hard way where loops break.

The failure mode has a name now: **agent slop.** It's what you get when you automate past the point where you can still vouch for the output. The loop keeps producing, the volume keeps climbing, and quality quietly degrades because nothing in the system was built to catch the drift. You end up with a thousand commits you can't trust and didn't read. Slop isn't bad code — it's *unvouchable* code, generated faster than any human can verify it.

So here's my honest checklist for when to skip the loop entirely:

**Skip it if you're a solo builder on a consumer plan.** Loops spend tokens on every iteration, and a runaway loop on a metered plan is a real bill. (Ask me how I know — $12 in 28 minutes, and that was a *small* one.) If you're cost-sensitive, the math on autonomous loops gets ugly fast. I broke down the full token economics in my [guide to AI agent cost optimization](https://www.mejba.me/ai-agent-cost-optimization-guide), and the headline is simple: without a hard gate, loops fail *quietly* and keep spending. Silent failure plus metered billing is the worst combination in this whole field.

**Skip it if your code has no automated verification.** No tests, no type checker, no CI? Then your only possible stop condition is an LLM's opinion — the lowest-fidelity gate there is. You don't have a loop; you have an expensive way to generate plausible-looking diffs. Build the tests first. The verification infrastructure *is* the loop infrastructure. A loop without a hard gate to push back is the agent agreeing with itself on repeat, and that's precisely the slop machine.

**Skip it if your real bottleneck is review capacity, not typing speed.** This one's subtle and it catches good engineers. Loops make *production* faster. They do nothing for *review*. If you're already drowning in PRs you can't review fast enough, adding a loop that generates ten times more code doesn't help — it buries you. The constraint just moved. You optimized the part that wasn't the problem. Before you build a loop, ask honestly: is typing my bottleneck, or is *vouching* my bottleneck? If it's vouching, a loop makes things worse.

The unifying principle: a loop is only as trustworthy as the thing in it that can say no. No test, no type check, no real error to react to — no loop. Just an agent nodding at itself while the meter runs.

## What this actually changes about your work

So where does this leave you, practically, this week?

The honest read on the "write loops, not prompts" movement is that it's directionally correct and tactically overstated. The future genuinely is loops — Cherny isn't wrong that his job is now writing the machinery, not the prompts. But the 68% number is the reality check: the loops that survive contact with production are small, gated, and supervised. The dream of a two-hundred-agent fleet running unattended is real for the people who've built bulletproof verification around every layer. For everyone else, it's a slop generator with a credit card.

What I'd actually do, starting today: take one repetitive task you do with an AI agent — the one where you keep typing the same kind of prompt over and over. Write down its stop condition *first*, before anything else. Make it a fact, not an opinion: a test, an exit code, a status check. If you can't name that fact, you've just discovered the task isn't loop-ready, and you've saved yourself a $12 lesson. If you *can* name it, you've got the hardest part of the loop already built. The trigger and action are the easy half.

The mental model I want you to carry out of here is small enough to fit on a sticky note: **a loop is a machine for repeating reason-act-observe until a thing that can say no says yes.** Design that "thing that can say no" first. Everything else is plumbing.

That runaway loop that cost me $12 wasn't a failure of the agent. It was a failure of *definition* — I built the plumbing before I built the gate. Build the gate first. Then you can let the loop run, and actually trust what it hands you when it stops.

## Frequently Asked Questions

### What is loop engineering?

Loop engineering is the discipline of designing an autonomous agent's trigger, action, and stop condition so it can run, verify its own output, and halt on objective criteria — rather than depending on a single human-written prompt. The unit of work shifts from the prompt to the loop itself. For the full anatomy, see the trigger/action/stop-condition section above.

### Is loop engineering the same as prompt engineering?

No. Prompt engineering optimizes a single instruction you hand the model; loop engineering optimizes the machinery that prompts the model repeatedly and decides when the work is done. As Boris Cherny put it, "My job is to write loops" — the prompt becomes an internal detail of the loop, not the thing you craft by hand.

### When should you not use an agent loop?

Skip an agent loop if you're a solo builder on a consumer plan (token costs compound on every iteration), if your code has no automated verification (tests, types, CI) to serve as an objective stop condition, or if your real bottleneck is review capacity rather than typing speed. A 2025 survey of 306 practitioners found 68% of production agents run ten steps or fewer before a human intervenes — small and supervised beats autonomous and unvouchable.

### What is the maker-checker pattern in AI agents?

The maker-checker pattern splits an agent loop into two roles: one agent produces the work and a separate agent grades it against criteria. Because the checker didn't create the output, it has no incentive to rubber-stamp it — giving the loop a real adversary that can push back. It's the cleanest fix for loops whose stop condition would otherwise be subjective.

### What makes an agent loop's stop condition reliable?

Verification fidelity. An objective gate — a passing unit test, a green CI pipeline, an HTTP 200 — is a fact the agent can't fake, so the loop can run unattended. A subjective gate, like an LLM judging whether an image "looks good," is an opinion that's easy to fool, so it needs a human in the seat or a hard cap on attempts. Match the loop's autonomy to how faithfully its gate measures the real goal.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
