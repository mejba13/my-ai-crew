**BRAND:** mejba.me
**TITLE:** Claude Code /advisor Command: A Second Brain for Models
**META TITLE:** Claude Code /advisor Slash Command Tested (2026)
**SLUG:** claude-code-advisor-slash-command
**PRIMARY KEYWORD:** Claude Code advisor slash command
**SECONDARY KEYWORDS:** Claude Code /advisor, Sonnet Opus advisor pattern, Claude Mythos advisor
**META DESCRIPTION:** I tested Claude Code's new /advisor slash command with Sonnet 4.6 and Opus 4.6. Here's how the meta-cognitive layer actually works and when it's worth enabling.
**TAGS:** Claude Code, AI Development, Sonnet 4.6, Opus 4.6, Agent Workflows
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the developer will understand exactly when to enable /advisor, which model pairing fits their workflow, and why this command is the scaffolding for the upcoming Mythos release.

---

I spent a full Saturday trying to break Claude Code's new `/advisor` command.

Not in the "find the bug and file a ticket" sense. In the "I've been burned enough times by shiny new slash commands that I refuse to trust any of them until I've thrown ugly production code at them" sense. My test bed was a billing service I've been refactoring for weeks — a Laravel 11 app with the kind of edge cases that expose every weakness in an AI coding model. Prorated upgrades. Mid-cycle downgrades. Dunning logic that has to survive a partial Stripe webhook failure.

The result surprised me. Not because `/advisor` saved me from catastrophe — it didn't. But because it caught two issues I'd been staring at for three days and couldn't see. A logic flaw in the proration window and a subtle timezone bug in the renewal calculation. Sonnet 4.6 was running as my main executor. When it hit the stall pattern on my third pass, it called Opus 4.6 through `/advisor` and came back with a two-paragraph analysis that read like a senior engineer's code review.

That's when I realized this command isn't really about advice. It's about Anthropic building scaffolding for Mythos.

Let me explain what I mean — and why the `/advisor` slash command might be the most important feature Claude Code has shipped this quarter, even though most developers will write it off as a minor quality-of-life update.

---

## What /advisor Actually Does (And What It Doesn't)

The `/advisor` command lets you configure a secondary model that gets invoked when your primary model hits trouble. That's the elevator pitch. The reality is more nuanced, and the nuance matters.

Here's the current setup. You run `/advisor` inside Claude Code, pick a model from the list (right now that's Sonnet 4.6 or Opus 4.6 — no other options, no custom endpoints, no Haiku), and from that point forward Claude Code has the ability to self-invoke that model when it decides it needs a second opinion. You don't call the advisor manually. You don't type "ask the advisor about this." The primary model makes the judgment call autonomously based on context signals.

That last part tripped me up the first time. I kept expecting a prompt or a confirmation dialog. There isn't one. The advisor call happens silently, mid-response, and the advice gets folded back into the session context as if it had always been there.

Three things you need to understand about how this actually works:

**The advisor has full conversational context.** When the primary model calls `/advisor`, the secondary model receives the entire transcript — your original request, every tool call, every file read, every command output, every reasoning step. No parameters. No filtering. No cherry-picking. It sees what your main model saw, in the same order, with the same constraints.

**The advisor cannot touch the outside world.** No file system access. No shell commands. No web fetches. No MCP servers. The advisor exists purely to reason over the conversation history and return guidance. This is a hard architectural boundary, not a permission you can flip.

**The advice becomes part of shared memory.** Once the advisor responds, its analysis persists in session context. Later advisor calls see earlier advice. If empirical results contradict what the advisor recommended, Claude Code surfaces the conflict instead of silently overriding the guidance. You get an explicit "the advisor said X, but the test output shows Y — should we consult again?" moment.

That third behavior is the one I didn't expect, and it's the one that changed my mind about the feature.

---

## The Four Triggers That Call the Advisor

I spent the first hour of testing trying to figure out *when* Claude Code actually reaches for the advisor. Docs are thin. The behavior is partly emergent. So I ran a bunch of controlled scenarios and watched the logs.

Four patterns trigger an advisor call reliably. If you understand these, you'll know whether `/advisor` is worth enabling for your workflow or whether it's just going to sit there consuming context tokens it never uses.

**Trigger 1: Before substantive writing or interpretation.** When the model is about to write a significant chunk of code, generate a new file, or make an interpretive leap from requirements to implementation, it pauses and asks the advisor for a sanity check first. This is the "measure twice, cut once" pattern. Simple reactive tasks — rename a variable, fix a typo, adjust a margin — skip this entirely.

**Trigger 2: At the perceived finish line.** When the main model believes the task is complete, it calls the advisor to double-check the output before declaring success. This caught a bug in my billing code that Sonnet 4.6 had convinced itself was resolved. The advisor flagged a missing edge case in the proration logic, and Sonnet went back in and fixed it without me saying a word.

**Trigger 3: Recurring errors or non-converging loops.** If the model tries the same fix three times and the tests keep failing, or if it's bouncing between two incompatible approaches, the advisor gets pulled in. This is the pattern I care most about because it's the one that used to drain my entire afternoon. You know the loop — the model fixes one thing, breaks another, fixes that, regresses the first, and you eventually have to step in manually and untangle the whole mess.

**Trigger 4: Multi-step task checkpoints.** For any task with multiple phases (the kind where Claude Code builds a todo list at the start), the advisor gets called at least twice. Once before committing substantive work. Once at completion. This is the default safety net for the workflows where things most often go wrong.

What won't trigger an advisor call? Minor UI tweaks. One-shot refactors. Reactive fixes to straightforward problems. Anything the primary model is confident about. The system is deliberately conservative — it doesn't want to burn tokens on problems that don't need a second opinion.

But there's a catch with that conservatism. If your primary model is *over*confident — and any of us who've used Sonnet 4.6 for long enough know it can be — the advisor won't get called when it should. The judgment to invoke the advisor lives inside the same model whose judgment you're trying to audit. That's a design tension I don't think is fully resolved yet, and I'll come back to it in the "real talk" section.

For now, let me show you what the actual model pairings look like in practice.

---

## The Three Pairings I Tested (And Which One Wins)

There are really only three ways to configure `/advisor` right now, and I ran all three on the same billing refactor over the course of the weekend. Here's what I found.

### Pairing 1: Sonnet 4.6 Main + Opus 4.6 Advisor

This is the pairing Anthropic seems to be nudging users toward, and after testing, I understand why. Sonnet 4.6 runs as your execution model. It's fast, it's cheap, it's good enough for 85% of the coding work I do day to day. When it gets stuck, Opus 4.6 drops in as the advisor and brings its deeper reasoning to bear on the problem.

The cost math on this is genuinely interesting. Sonnet 4.6 costs roughly half of what Opus 4.6 costs per million tokens. The advisor only gets invoked on specific triggers, so you're not paying Opus rates for the whole session — just the stuck moments. In my Saturday session, I ran about 42 turns of Sonnet 4.6 with exactly 6 advisor calls to Opus 4.6. The token breakdown came out cheaper than running Opus 4.6 as the main model for the same session, by a margin that would matter at scale.

Quality-wise? I got results comparable to what a pure Opus 4.6 session would have given me, because every moment that mattered had Opus in the loop anyway.

### Pairing 2: Opus 4.6 Main + Sonnet 4.6 Advisor

This is the inverted pairing, and I tested it mostly out of curiosity. The result: not great. Sonnet 4.6 is a capable model, but when Opus 4.6 is already struggling, asking Sonnet to audit the reasoning is like asking a junior engineer to code-review a senior engineer's architecture decision. The advisor calls came back with either agreement ("your approach looks correct") or surface-level suggestions that Opus had already considered and discarded.

I don't recommend this configuration. If Opus is your main model, you're better off spawning a subagent with its own context window to handle the stuck moments — which is, incidentally, the approach I default to when I'm running Opus as my executor.

### Pairing 3: Opus 4.6 Main + Opus 4.6 Advisor

Yes, you can set the same model as both main and advisor. No, it's not useless — but it's not as powerful as the pairings that use distinct models, because you lose the fresh-eyes effect. The advisor's advantage comes partly from approaching the problem from a different angle. When the advisor is literally the same model with the same biases, the novel perspective shrinks.

That said, this pairing still outperformed unchaperoned Opus on my recurring-error test, because the advisor call forces a reflective pause that the main model wouldn't have taken on its own. The meta-cognitive step matters even when the cognition is coming from the same source.

The winner for most developers: Sonnet 4.6 main, Opus 4.6 advisor. Cheaper than pure Opus. Smarter than pure Sonnet. Cleanest workflow I've tested.

---

## Walking Through a Real Advisor Call, Step by Step

Let me show you exactly what this looks like in practice, because the abstract description doesn't capture the experience. I'll use the billing bug scenario because it was my cleanest test case.

**Step 1: Configure the advisor.**

Inside Claude Code, I ran the command:

```bash
/advisor
```

The prompt came back asking which model to configure. I selected Opus 4.6. Claude Code confirmed: *"Advisor configured. Opus 4.6 will be consulted for complex decisions and task completion checks."* That's it. No further config. No sampling parameters. No prompt customization.

**Step 2: Launch the main task.**

I asked Claude Code (running Sonnet 4.6 as main) to audit the proration logic in my `SubscriptionBillingService` class and fix any bugs it found. Sonnet 4.6 read the file, traced the flow through three dependent classes, and proposed a fix for what it identified as an off-by-one error in the pro-rated days calculation.

**Step 3: Sonnet applies the fix and runs tests.**

The fix passed 14 of the 16 existing tests. Two tests failed — both related to timezone handling on renewal boundaries. Sonnet tried a second fix. Same two tests failed. Tried a third approach. Same failure pattern.

This is where the recurring-error trigger fires.

**Step 4: Sonnet calls /advisor.**

The console output made the call explicit:

```
[advisor invoked: recurring test failure on timezone-sensitive cases]
```

No interaction on my end. Sonnet made the call autonomously. Opus 4.6 received the full transcript — my original request, the file contents, the test failures, all three attempted fixes — and returned a structured analysis.

**Step 5: The advice comes back.**

Opus 4.6's response identified two issues Sonnet had missed. First: the proration logic was using the server's local timezone instead of the user's subscription timezone, which meant renewals near UTC midnight were calculated in the wrong billing period. Second: the test fixtures Sonnet had been updating were themselves wrong, which is why Sonnet's fixes kept looking correct but the tests kept failing — the tests were validating the bug, not the fix.

Neither issue was obvious from Sonnet's solo analysis. Both were obvious in retrospect once Opus pointed them out.

**Step 6: Sonnet integrates the advice.**

Sonnet 4.6 didn't just copy the advisor's response verbatim. It took the guidance, rewrote the proration logic to use the user's timezone, then independently flagged the test fixture issue for my review — *"The advisor noted the fixtures may be validating incorrect behavior. I recommend reviewing `tests/Unit/ProrationTest.php` lines 84-110 before I modify them, since this changes test semantics."*

That last part is the behavior that sold me on `/advisor`. Sonnet didn't blindly apply the advice. It paused at the place where the advice had implications I should weigh in on.

**Step 7: Verification.**

I approved the fixture review. Sonnet updated the tests, reran the suite, and all 16 tests passed. Total time from the first failed fix to the green test run: under four minutes. Manual debugging time I would have spent on the same issue, based on how long I'd already been stuck: probably another two hours.

---

## The Pro Tips Nobody's Talking About Yet

A few things I learned in testing that aren't in the announcement docs.

**Tip 1: Advisor calls show up in the token ledger, but as a separate line item.** You can see exactly how much the advisor cost versus the main model. This is surprisingly useful for calibrating whether the feature is earning its keep on any given project. I'm tracking the ratio across my test projects and will probably publish the data in a follow-up post.

**Tip 2: The advisor persists across `/compact` operations.** If you compact your conversation to free up context, the advisor's prior guidance survives the compaction. This is a small detail with big implications for long sessions — your advisor effectively becomes a cumulative knowledge layer.

**Tip 3: You can re-run `/advisor` to switch models mid-session.** I didn't realize this at first, but you can swap your advisor configuration partway through a session without restarting. I used this to move from Opus-as-advisor to a fresh Opus-as-advisor after a `/compact` to reset the advisor's accumulated state.

**Tip 4: The advisor trigger can be nudged.** While you can't force a call, you can make the main model more likely to invoke the advisor by being explicit in your prompt. Phrases like *"double-check this with the advisor before committing"* or *"if you get stuck, consult the advisor"* measurably increased the call rate in my tests. Not a guarantee — but a useful lever.

**Tip 5: Watch for advisor-loop behavior.** On one test, Sonnet 4.6 called Opus 4.6 three times in a row on the same problem, each time getting slightly different advice and each time feeling less sure about its direction. This is the failure mode to watch for. If you see the advisor getting called repeatedly without progress, step in manually. The meta-cognitive layer is valuable, but it's not a substitute for human judgment when the model is genuinely lost.

---

## Real Talk: Why I Still Default to Subagents for Opus Workflows

Here's the honest part. For my actual daily workflow — where I'm running Opus 4.6 as my main model on complex agent architectures — I still prefer subagents with independent context windows over `/advisor`. Let me explain why, because the trade-off matters.

When I spawn a subagent, that agent starts with a clean context. No accumulated conversation. No biased framing from the main model's prior attempts. Just the prompt I give it and the tools I authorize. That cleanliness is often the difference between useful guidance and advice that's just echoing the main model's blind spots back at it.

The `/advisor` command, by contrast, feeds the full transcript to the advisor every time. If the main model is stuck because it's framing the problem wrong, the advisor sees the same wrong framing and may or may not escape it. Opus is better at escaping framing traps than Sonnet, which is why the Sonnet-main + Opus-advisor pairing works so well — Opus brings enough horsepower to reframe. But when Opus is your main model and the framing is the problem, the advisor can get pulled into the same trap.

I tested this directly. I gave Opus 4.6 a deliberately mis-framed architectural problem and configured Opus 4.6 as the advisor. The advisor agreed with the framing. I then spawned a fresh Opus 4.6 subagent with only the original requirements (no transcript) and asked the same question. The subagent immediately reframed the problem and proposed a different solution.

Same model. Same problem. Different results based on context freshness.

So here's my actual rule of thumb: use `/advisor` when your main model is Sonnet and you want an Opus safety net. Use subagents when your main model is Opus and you want genuine parallel reasoning. Use both when you're working on something high-stakes.

And be honest with yourself about which mode you're in. The feature's defaults are smart, but they're not magic.

---

## Why /advisor Is Really About Mythos

Now for the part I've been building up to.

I don't think `/advisor` exists because the Claude Code team wanted to ship a meta-cognitive layer for Sonnet and Opus. I think `/advisor` exists because Anthropic is preparing the infrastructure for Mythos — the model that started leaking into public conversations a few weeks ago and that I've already covered in [the Claude Mythos leak analysis](https://www.mejba.me) — and they needed a way to make a high-cost, high-power model economically viable inside Claude Code.

Think about it. If Mythos lands at the price point the leaks suggest — significantly above Opus 4.6 — running it as your main model for an entire session will be prohibitive for most developers. Even for agency work, the margins won't make sense on every task. So how do you give users access to Mythos's power without forcing them to pay Mythos rates for every token?

You let them configure it as an advisor.

Imagine the pairing: Sonnet 4.6 as your execution model, running fast and cheap for the majority of your tokens. Mythos as your advisor, invoked only at the critical moments — before major commits, when the tests keep failing, when the model is about to make an interpretive leap. You get Mythos-level judgment on the decisions that matter most, at a cost that's a fraction of running Mythos as the main model.

This is the pattern. `/advisor` is the scaffolding. The Sonnet/Opus pairing we have today is the beta test for the Sonnet/Mythos pairing that's coming.

I could be wrong about this. It's possible `/advisor` is just a standalone feature and Mythos integration will look completely different. But the architecture choices tell a story. The hard model allowlist (currently two models). The automatic context sharing. The conflict-detection behavior that surfaces advisor disagreements instead of deferring to them. These are all design decisions that make perfect sense if your endgame is a tiered model system where the highest-tier model is expensive enough that you only want it consulted on the hardest problems.

And if I'm right about this, the developers who learn `/advisor` now will have a massive head start when Mythos lands. The workflow patterns you build around Sonnet-main + Opus-advisor will transfer almost directly to Sonnet-main + Mythos-advisor, with only the configuration changing. The prompting habits, the trigger awareness, the subagent-vs-advisor decision framework — all of it carries forward.

If you want a deeper look at what Mythos might bring, I've written about the implications in [my Mythos deep dive on model autonomy](https://www.mejba.me). But the short version: whatever Mythos turns out to be, it's going to be expensive, and `/advisor` is how you'll access it without going broke.

---

## What I'd Tell You to Do Today

If you're already on Claude Code and running Sonnet 4.6 for most of your work, enable `/advisor` with Opus 4.6 as the advisor right now. Run it on one real project this week. Pay attention to how often the advisor gets called and what kinds of problems trigger it. That pattern recognition is the real value — not the first few times it saves you from a bug, but the gradual understanding of when your model is likely to need a second opinion.

If you're running Opus 4.6 as your main model, I'd still recommend enabling `/advisor` with Opus as the advisor, but in parallel with your existing subagent workflows. Don't replace subagents. Augment them. Use `/advisor` for the lightweight, inline second opinions and subagents for the heavier parallel reasoning.

If you haven't upgraded to Sonnet 4.6 or Opus 4.6 yet, do that first. The advisor command requires them. Older models aren't in the allowlist and won't be — the feature is explicitly designed for the current generation.

Here's one more thing. If you're on my email list or you follow my blog, I'm going to be tracking `/advisor` usage patterns across the next few weeks and publishing the data. Cost ratios, trigger frequencies, real outcomes on real projects. The kind of data that tells you whether this feature earns its place in your workflow or whether it's a nice-to-have you can ignore. If you want that data before it's public, the best way to get it is to start running your own tests now — because by the time I publish my findings, Mythos will probably be weeks away from changing the equation entirely.

The `/advisor` command isn't the biggest feature Claude Code has shipped this year. It might not even be the most useful feature on any given day. But it is the most *structurally important* feature, because it's the first time Anthropic has built explicit infrastructure for multi-model collaboration inside a single session, and that infrastructure is going to matter enormously once the model tier above Opus actually exists.

The billing bug that took me three days to find? Sonnet and Opus caught it in four minutes of advisor-assisted analysis. That's not the end of the story. That's the beginning of a workflow pattern I'll be using for years.

Start building the habit now. You'll be glad you did when the next model drops.

---

## Frequently Asked Questions

### What is the Claude Code /advisor slash command?
The `/advisor` slash command lets you configure a secondary Claude model that gets auto-invoked by your primary model when it hits complex decisions, recurring errors, or task completion checkpoints. It currently supports Sonnet 4.6 and Opus 4.6 only. The advisor receives the full conversation transcript and returns guidance that folds back into the session's shared context.

### Which model pairing should I use with /advisor?
For most developers, use Sonnet 4.6 as your main model and Opus 4.6 as the advisor. This pairing gives you Sonnet's speed and cost efficiency for routine work while reserving Opus's deeper reasoning for the moments that matter. I tested all three viable pairings and this one produced the best cost-to-quality ratio on real refactoring tasks.

### Does /advisor work with subagents?
Yes, but they serve different purposes. `/advisor` feeds the full transcript to a secondary model inline, while subagents run with clean, independent context windows. Use `/advisor` when your main model is Sonnet. Use subagents when your main model is Opus and you need a fresh perspective free from the main model's framing.

### Can the advisor access files or run commands?
No. The advisor has no access to the file system, shell, web, or MCP servers. It only reasons over the conversation history it receives from the main model. This is a hard architectural boundary in the current implementation, not a configurable permission.

### Is /advisor cheaper than running Opus 4.6 as the main model?
In my testing, yes — a 42-turn Sonnet 4.6 session with 6 Opus 4.6 advisor calls came out cheaper than running Opus 4.6 as the main model for the same workload. The savings come from using the cheaper model for the majority of tokens and paying premium rates only on the advisor invocations.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
