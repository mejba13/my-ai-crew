**BRAND:** mejba.me
**TITLE:** "Coding Is Solved"? The Flickering Bug Says No
**META TITLE:** "Coding Is Solved" Is AI Hype — Here's The Proof
**SLUG:** coding-is-solved-ai-hype-claude-code
**PRIMARY KEYWORD:** coding is solved
**META DESCRIPTION:** "Coding is solved, just write loops" is the loudest claim in AI right now. A year-long Claude Code bug is the quiet evidence it's overhyped. My honest take.
**TAGS:** AI Coding, Claude Code, Developer Burnout, AI Hype, Opinion

---

Here's the thing nobody pushing the "coding is solved" line wants to put next to their slide deck: the most talked-about AI coding tool on earth shipped with a screen-flickering bug, and it took roughly a year of patches, a rollback, and finally a workaround-that's-still-behind-a-feature-flag before anyone could call it handled.

Not a hardware problem. Not an infrastructure problem. Not some gnarly distributed-systems race condition that needed a research team. A terminal redraw bug. The kind of thing a competent engineer fixes in an afternoon — except this one outlived two model generations.

I want to sit with that gap for a minute, because it's the whole argument.

The story I keep hearing — on X, in podcasts, in those breathless "the era of the software engineer is ending" threads — goes like this: manual prompting is dead, coding is basically a solved problem, and the smart move now is to stop writing code and start writing *loops* that run an AI through prompt cycles until some win condition is hit. The hard stuff that's left, the story says, is infrastructure, user feedback, and hardware. Coding? That's the easy part. That's done.

I use AI coding tools every single day. I'm not here to tell you they don't work — they absolutely do, and I'll show you exactly where. But "coding is solved" is the kind of claim that sounds smart in a keynote and falls apart the second you point at the receipts. And the cleanest receipt I have is a bug so boring it almost makes the point by itself.

Let's get into it.

## What "coding is solved" actually claims (and who's saying it)

Before I argue with a position, I want to state it as strongly as the people who hold it would. Steelmanning first. It's the only honest way to do this.

The fullest version of the argument comes from inside Anthropic itself. Boris Cherny — the creator of Claude Code — said in mid-2026 interviews that AI has *practically solved* coding, and that he hadn't written a line of code by hand in eight months ([Fortune, June 2026](https://fortune.com/2026/06/11/anthropic-claude-boris-cherny-doesnt-write-code-by-hand-anymore/)). The framing that grew up around that is sharper still: stop prompting Claude one message at a time, the argument goes — *build loops*. Construct a harness that lets the model observe, plan, act, and reflect over hours, sometimes days, iterating against tests and acceptance metrics until it converges on a passing state ([explainx.ai's writeup of the harness-engineering thesis](https://explainx.ai/blog/anthropic-engineer-loops-prompts-ai-coding-harness-engineering-2026)).

In that worldview, the human's job shifts. You don't write functions. You write the *scoring function* — the win condition — and the loop grinds toward it. Picking a better model is optimizing the wrong variable; building better feedback infrastructure is the real game. I've actually written about the mechanics of this approach in my own [breakdown of long-running agent harnesses](https://www.mejba.me/anthropic-long-running-agent-harness), and I'll say plainly: as a *technique*, it's real and it's powerful.

Then there's the number everyone quotes. Anthropic reported that lines of code merged per active contributor hit roughly **8x the pre-2025 baseline** by Q2 2026 — framed in some retellings as "two years of coding output, every quarter, per person" ([OfficeChai](https://officechai.com/ai/anthropic-says-that-their-employees-are-using-ai-to-write-8x-more-code-compared-to-18-months-ago/)). By May 2026, Claude was reportedly authoring over 80% of merged production code internally ([VentureBeat](https://venturebeat.com/technology/anthropic-says-80-of-its-new-production-code-is-now-authored-by-claude-how-your-enterprise-can-keep-up)).

That is a genuinely staggering set of claims. If I stopped here, you'd close the tab convinced coding was finished.

So why am I not convinced? Because of a detail buried in Anthropic's own announcement — and because of a bug. Let's take the detail first.

## The caveat Anthropic put in its own footnote

Here's a thing the hype threads almost never include, and it's the single most honest sentence in the whole conversation.

When Anthropic published the 8x figure, the company's own blog post cautioned that the number was **"almost certainly an overstatement,"** because counting lines of code rewards volume, not quality — and the per-PR line counts had to be capped at the 99th percentile just to filter out outlier commits ([as reported in the productivity coverage](https://officechai.com/ai/anthropic-says-that-their-employees-are-using-ai-to-write-8x-more-code-compared-to-18-months-ago/)).

Read that again. The organization *making* the strongest "coding is solved" case attached a warning label to its own headline metric. Lines of code is a famously broken proxy — every engineer who's been around knows that more code is often *worse*, not better. A tool that lets you generate eight times the diff is not self-evidently a tool that solved coding. It might just be a tool that solved *typing*.

And that's the seam I want to pull on. Because there's a difference between three claims that get blurred together when people say "coding is solved":

1. **AI accelerates code production.** True. Obviously, measurably true.
2. **AI improves code quality when used well.** Often true, with real caveats.
3. **Coding, as a hard human problem, is finished.** This is the leap. And it doesn't survive contact with the evidence.

The first two are real advances. I'd fight anyone who told a junior dev to ignore these tools. But the third claim — the *finished* claim — is where the marketing outruns the machine. And the proof isn't some philosophical objection about creativity or craft. It's much more embarrassing than that. It's a flicker.

## The flickering bug nobody could quietly fix

If coding were solved — if you could just point a loop at a problem and let it grind to a win condition — then the most resourced AI coding team on the planet, dogfooding its own tool, writing 80% of its code with the very model in question, should be able to crush a terminal rendering bug without breaking stride.

That is not what happened.

Claude Code is a terminal app — agentic AI coding that lives in your command line, released February 2025. From early on, it had a notorious screen-flickering problem. The terminal content would rapidly scroll, shake, and flash text from earlier in the session — caused by the renderer redrawing the entire terminal buffer on every streaming update instead of doing line-specific, incremental updates. You can read the trail yourself across a graveyard of GitHub issues: [#769](https://github.com/anthropics/claude-code/issues/769), and a long tail of follow-ups like [#16939](https://github.com/anthropics/claude-code/issues/16939), [#18084](https://github.com/anthropics/claude-code/issues/18084), and [#18827](https://github.com/anthropics/claude-code/issues/18827), spanning Windows Terminal, VS Code, Cursor, and Android Studio's integrated terminals.

Now watch the timeline, because the timeline *is* the argument:

- **Early 2025 onward:** Flicker reported repeatedly. Especially brutal in xterm.js-based terminals (VS Code, Cursor), where the full-buffer redraw 2–3 times a second made the screen strobe.
- **Around December 2025:** Anthropic addresses it publicly, reportedly claiming a large flicker reduction with a rewritten differential renderer — roughly a third of sessions still seeing at least one flicker, per community reports of the rollout.
- **Shortly after:** Instability. A flicker-free rendering change shipped, and a regression followed — [issue #41965](https://github.com/anthropics/claude-code/issues/41965) documents v2.1.89's "flicker-free" alt-screen rendering *destroying terminal scrollback by default*. The fix created a new problem. Things got rolled back. End of 2025: still not cleanly solved.
- **Around April 1, 2026:** A **"no flicker mode"** arrives — `CLAUDE_CODE_NO_FLICKER=1` — using an **alternate-screen-buffer** approach, the same trick Vim uses when it takes over your terminal and gives it back untouched when you quit ([piunikaweb](https://piunikaweb.com/2026/04/02/anthropic-no-flicker-mode-claude-code/)). It works. But it's a *circumvention*, not a root-cause fix: it sidesteps the redraw problem by rendering somewhere else entirely. And it shipped as a **research preview, behind a feature flag**, with a companion flag to disable mouse capture because that introduced its own friction.
- **Late May 2026:** A newer terminal interface still surfaced uninformative, "mysterious" error messages — the error-handling itself unpolished.

One independent engineer titled their writeup ["The Fix a Year in the Making."](https://slyapustin.com/blog/claude-code-no-flicker.html) Someone else shipped a third-party patch called *Claude Chill* just to deal with the flicker, and it [hit the Hacker News front page](https://news.ycombinator.com/item?id=46699072). When users are building external tools to fix your rendering, the rendering is not solved.

Here's why this one bug carries so much weight: **there is no hardware excuse.** No infrastructure excuse. No "the real hard part is somewhere else" escape hatch. Screen flickering in a terminal is a pure, self-contained software problem — exactly the category the "coding is solved" narrative says is *the easy part, the finished part*. And it took a year, a rollback, and a flag-gated workaround at the company best positioned on Earth to fix it.

If coding were solved, this bug would have died in a weekend. It didn't. That's not a gotcha. That's data.

## "Just write loops" works — until it meets the unglamorous bug

I want to be fair to the loop thesis, because I genuinely like it. I run loop-based automation in my own work — I've written about [scheduling Claude Code in cron loops](https://www.mejba.me/claude-code-loop-cron-scheduling) and about the broader shift from a manual SDLC to an [agentic development lifecycle](https://www.mejba.me/adlc-vs-sdlc-agentic-development-lifecycle). When the problem has a crisp, machine-checkable win condition — tests pass, types check, the benchmark goes green — a well-built loop is a beautiful thing to watch. It converges. It's the closest thing to "set the scoring function and walk away" that I've experienced in fifteen years of writing software.

But notice the shape of the problems where loops shine: **they all have a clear win condition.**

A flickering terminal does not. What's the test that goes green? "It looks less janky to a human eyeball across forty different terminal emulators, each with its own renderer, on three operating systems, at varying refresh rates"? There's no assertion for that. No `expect(screen).not.toFlicker()`. The win condition is fuzzy, environment-dependent, and partly subjective — which is *precisely* why a year of loops couldn't grind it out. The loop is only as smart as the scoring function you can write, and some of the most important software problems are exactly the ones you *can't* score cleanly.

That's the part the "coding is solved" framing skips. It quietly assumes every remaining problem looks like a unit test. Most of the hard ones don't. They look like flicker. They look like "the error message is technically correct but tells the user nothing." They look like the [project-load vulnerability researchers found in the source leak](https://www.mejba.me/claude-code-leak-five-hidden-features), where API requests fired *before* the trust prompt appeared — a design oversight no test caught because nobody thought to score for it.

> If you'd rather have someone build and maintain these agentic loops properly — with the guardrails and the boring-but-critical error handling that the demos skip — that's a chunk of what I take on. You can see what I've shipped at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

So the loop isn't wrong. It's *narrow*. It's a fantastic tool for the well-specified middle of the problem space and useless at the fuzzy edges — and the fuzzy edges are where software actually breaks in production. Calling that "solved" is calling the part you can measure the whole job.

## The human cost of pretending the hard part is over

Now I want to talk about the part that actually bothers me, because the bug is just the evidence — this is the *stakes*.

The "coding is solved, just ship into prod, write the loop, hit the win condition" narrative isn't landing in a vacuum. It's landing on developers who are, right now, more burnt out and more anxious about their jobs than I've seen them in a long time. And the messaging is making both things worse.

Here's the cruel mechanism, and it's well-documented. When organizations decide AI made coding trivial, they don't bank the time saved as slack — they treat every minute saved as a minute owed back as *more output*. PR volume jumps 40–60%, and the bottleneck just moves downstream to *review*. Now the same humans are drowning in code they didn't write, can't fully trust, and are pressured to approve fast. That's not less burnout. It's [a new, worse kind of burnout, and it's hitting the people who embraced AI hardest](https://www.infoworld.com/article/4141358/the-ai-coding-hangover.html). There's even [a whole genre of "AI was supposed to fix developer burnout"](https://kotrotsos.medium.com/ai-was-supposed-to-fix-developer-burnout-b3f04ca31ee3) post-mortems now, which tells you how the experiment is going.

Put the two halves together and the dishonesty sharpens. Management hears "coding is solved" and sets expectations as if it were true. Developers live in the gap between that claim and the flickering, error-spewing, occasionally-broken reality — and get blamed for the gap. You're told the hard part is over while you spend your Tuesday debugging why the loop confidently shipped something subtly wrong, with an error message that explained nothing.

There's a blunt metaphor making the rounds for this kind of messaging — that someone is doing one thing to your leg and insisting it's the weather. I'll keep it tasteful, but the sentiment is right. Telling burnt-out engineers that their hard, real, still-necessary work is "the easy part" — while they're cleaning up after the very tools being called a solution — isn't optimism. It's gaslighting dressed as a productivity stat.

And I say this as someone who's openly, almost [embarrassingly addicted to Claude Code](https://www.mejba.me/claude-code-developer-addiction-honest). I'm not the resistance. I'm a heavy user telling you the marketing is writing checks the tooling can't cash yet.

## What's genuinely solved, what's genuinely not

Let me be specific, because vague pessimism is as useless as vague hype. Here's my honest ledger after using these tools daily through 2025 and into 2026.

**Genuinely accelerated by AI today:**
- Boilerplate, scaffolding, glue code, config — near-instant, near-flawless.
- Well-specified refactors with strong test coverage — loops crush these.
- Translating intent into a first draft — what took 45 minutes takes 6.
- Exploring an unfamiliar codebase or API — faster than docs.

**Genuinely improved, with caveats:**
- Code quality, *when* you've built the harness (tests, lint, types, acceptance metrics) for the loop to score against. No harness, no quality gain — just faster mess.

**Genuinely NOT solved:**
- Fuzzy, unscoreable problems — rendering across heterogeneous environments, UX polish, "this is technically right but feels wrong."
- Error handling and failure-mode design — the unglamorous 80% that demos never show, and where Claude Code itself still stumbles.
- Knowing *what* to build, and *whether* it should exist at all.
- The judgment to override the loop when its win condition is subtly the wrong target.

Notice that the "not solved" column is exactly where the flicker bug lives. That's not a coincidence. The bug isn't an outlier — it's a representative sample of the work the hype declared finished.

So is coding solved? **No — and the honest answer is more useful than the hype.** Coding is *accelerated*, sometimes dramatically. The well-specified parts are increasingly automatable through loops. But the hard, fuzzy, judgment-heavy core — the part that makes software engineering a profession and not a typing speed contest — is exactly as unsolved as it was, and the tools' own bug trackers prove it.

## Frequently Asked Questions

### Is coding actually solved by AI in 2026?
No — coding is dramatically accelerated, not solved. AI tools excel at well-specified, machine-checkable tasks (boilerplate, refactors, tests passing) but still struggle with fuzzy, unscoreable problems like cross-environment rendering, UX polish, and failure-mode design. The proof is that even AI coding tools ship with long-lived bugs their own loops can't fix.

### What does "just write loops" mean in AI coding?
"Just write loops" refers to harness engineering — instead of prompting an AI one message at a time, you build a system that runs the model through repeated observe-plan-act-reflect cycles until it hits a defined win condition like passing tests. It's a genuinely powerful technique, but it only works where you can write a clear scoring function, which excludes many real-world problems.

### Why is the Claude Code flickering bug significant?
The flickering bug is a pure software problem with no hardware or infrastructure excuse, yet it took roughly a year, a rollback, and a flag-gated workaround to address — at the company best positioned to fix it. That makes it the cleanest counterexample to the claim that "coding is the easy part" of modern software. For the full timeline, see the section above.

### Is AI coding causing developer burnout?
Reports and post-mortems through 2026 suggest yes — not because AI does less work, but because organizations convert time saved into more output, spiking PR volume 40–60% and shifting the load to overwhelmed code review. The "coding is solved" narrative compounds this by setting expectations that don't match the messier reality developers actually face.

## The receipt I keep coming back to

I opened with a flickering terminal, so let me close there.

When someone tells you coding is solved, ask them one question: *if that's true, why did the people who say it loudest spend a year fixing a screen that wouldn't stop shaking — and then ship the fix behind a feature flag?* There's no hardware to blame. No infrastructure to hide behind. Just a hard, unglamorous software problem doing what hard software problems have always done: resisting the people who underestimated it.

Use the tools. Build the loops. Ship faster — I do, every day, and I'm better for it. But hold the line on the truth: coding isn't solved, it's *amplified*. The hard part didn't disappear. It moved to where the loops can't reach, and it's still yours.

And honestly? That's good news. Because it means the judgment you've spent years building is the part that didn't get automated — it's the part that got *more* valuable. Don't let a productivity slide convince you otherwise.

Tonight, here's the one thing worth doing: next time you hit a bug your AI confidently failed to fix, don't feel behind. Screenshot it. That bug is the part of the job that's still yours — and it's the part that pays.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
