**BRAND:** mejba.me
**TITLE:** Practical AGI Is Already Here: Anthropic's Own Numbers
**META TITLE:** Practical AGI Is Here: Anthropic's Own Numbers (2026)
**SLUG:** practical-agi-anthropic-ai-builds-itself
**PRIMARY KEYWORD:** practical AGI
**META DESCRIPTION:** Anthropic says Claude writes 80% of its code and open-ended task success hit 76%. Here's why practical AGI is already here — read by someone who builds daily.
**TAGS:** AGI, Anthropic, Claude, AI Agents, AI Strategy

---

I read Anthropic's report twice before I admitted what it was actually saying.

The first read, I did the thing every engineer does — I went straight for the numbers, screenshotted the charts, and filed it under "interesting, will reference later." The second read, on a Saturday morning with no Slack open and nothing to ship, it landed differently. Because the report Anthropic published on June 5, 2026 — "When AI builds itself" — isn't a capabilities flex. It's a quiet, slightly nervous admission that the thing we've spent a decade arguing about in the abstract has already happened in their building, on their machines, with the model I use every single day.

Practical AGI is here. Not the sci-fi kind. Not a conscious mind in a server rack. The boring, load-bearing kind — a system that autonomously solves open-ended problems that have no predefined answer. And the reason I'm confident saying that isn't a vibe or a hype thread. It's [Anthropic's own internal data](https://www.anthropic.com/institute/recursive-self-improvement), the company that has every incentive to be cautious about exactly this claim.

I want to walk you through what the report actually says — the verified numbers, not the breathless paraphrases — and then I want to give you my honest read as someone who's spent the last year building production systems on top of these models. By the end you'll have a definition of AGI that's actually useful, a clear sense of which of three futures we're standing in right now, and one uncomfortable question about your own work that I haven't been able to shake. Let me start by killing the word that's been poisoning this whole conversation.

## Why "AGI" Has Been the Wrong Word All Along

Here's the trap we all fell into. We let "AGI" mean *the singularity* — a machine that wakes up, wants things, and out-thinks all of humanity at once. That definition is great for movies and terrible for actually noticing what's happening, because it sets a bar so high and so vague that no real system will ever obviously clear it. You can always say "but it's not *conscious*," and the conversation dies there.

Anthropic's report sidesteps the philosophy entirely, and that's the smartest move in the whole document. It draws a hard line between two things.

**Narrow AI** does one bounded task well. A chess engine. A spam filter. A model that classifies images. It can be superhuman at its one job and completely useless one inch outside it. We've had this for years and nobody panicked.

**General intelligence** — the practical kind — is the ability to take a problem nobody has pre-solved, with no answer key, no clean reward function, no "correct output" sitting in a dataset, and make real progress on it anyway. That's it. That's the whole definition. Not consciousness. Not desire. Just: *can it work on the open-ended stuff?*

Once you accept that framing, the question stops being "is it alive?" and becomes "how often does it succeed on problems with no defined solution?" And that's a question you can actually measure. Anthropic measured it. The number is what made me put my coffee down.

But before the number, you need the buckets — because not all "open-ended" work is the same, and the report is careful about this in a way the headlines weren't.

## The Four Task Buckets — And the One That Just Broke Open

Anthropic sorts the work its own engineers do into four tiers of difficulty. I find this framing useful enough that I've started mentally sorting my own week the same way.

- **Trivial** — rename a variable, fix a typo, write a one-line guard. The model has done this perfectly for two years.
- **Routine** — implement a well-specified function, wire up a known API, write tests for existing logic. Solved.
- **Substantial** — build a feature across several files, refactor a module, debug something with a knowable cause. This is where 2025's models got genuinely good.
- **Open-ended** — "figure out why training runs are silently degrading," "design an experiment to test this hypothesis," "improve this system when nobody knows what the improvement even looks like." No spec. No answer key. This is the AGI tier.

For years, that fourth bucket was where AI fell apart. It could autocomplete your function but it couldn't *do research*. It needed a human to define the problem so tightly that the open-endedness was already drained out of it.

That's the bucket that just broke open. According to Anthropic's report, Claude's success rate on most open-ended tasks **reached 76% in May 2026 — up 50 percentage points in six months.** Read that again. Half the gap, closed, in two quarters, on the exact category of work that was supposed to be the human-only moat.

I've felt this from the application side without having the number to attach to it. A year ago, when [Opus 4.6 quietly fixed a rendering bug I couldn't crack](https://www.mejba.me/blog/opus-4-6-hands-on-review) by trying three approaches on its own, that felt like a glimpse. Now I understand it was the leading edge of a curve that was about to go nearly vertical. The bug-fix wasn't the story. The *autonomy on a problem I hadn't fully specified* was the story.

And success rate is only half of it. The other half is how long the model can stay on its own feet before it needs you.

## The Doubling Trend That Should Actually Scare You

Here's the chart from the report I keep coming back to, because it's the one with the cleanest extrapolation — and the most unsettling one.

Anthropic measured the length of tasks its models can reliably complete autonomously, the kind of work where you hand it a goal and walk away. The progression:

- **March 2024** — Claude Opus 3 handled software tasks that take a human about **four minutes.**
- **March 2025** — Claude Sonnet 3.7 handled tasks taking about **an hour and a half.**
- **March 2026** — Claude Opus 4.6 managed **12-hour tasks.**

Four minutes to twelve hours in two years. But the number that matters isn't any single point — it's the *slope.* The report states this capacity is now **doubling roughly every four months, up from an earlier trend of doubling every seven months.** The curve isn't just steep. It's getting steeper.

Sit with the four-month doubling for a second, because the extrapolation does something to your stomach. Twelve-hour autonomous tasks in spring 2026. Double it — roughly a full day by autumn. Double again — multi-day. I'm not going to draw the line out three years and pretend I can predict a specific date, because that's exactly the kind of false precision this topic doesn't need. But the direction is not ambiguous. The thing that could work alongside you for fifteen minutes is becoming the thing that can work alongside you for a week.

This is the part that reframed [agent-native development for me](https://www.mejba.me/blog/going-agent-native-2026-opus-codex). I used to think the skill was prompting well. It isn't. The skill is *delegating well to something that can run for hours* — scoping the goal, setting the guardrails, then getting out of the way. The longer the leash gets, the more that scoping skill becomes the entire job. We'll come back to that, because it's where your value is heading.

Now — does longer and more-successful mean *better*? Because there's a version of this where the AI just does more mediocre work faster. The report has a direct answer to that, and it's the claim I had to verify three times.

## When the Machine Makes the Better Call

This is the line that moved the report from "impressive" to "genuinely new" for me.

Anthropic's researchers tracked how often Claude's suggested next research step was judged *better than the human researcher's own choice.* Not faster. Not cheaper. **Better.** The number went from **51% in November 2025 to 64% in April 2026.**

Pause on what 51% even means. It means that as of late 2025, on the judgment-heavy question of "what should we try next in this research direction," the model was already a coin-flip against trained experts. By spring 2026 it was winning roughly two times in three. This isn't code completion. This is *taste* — the thing we told ourselves was irreducibly human.

It shows up in raw capability too. On a code-optimization task, Claude Opus 4 hit a roughly **3x speedup in May 2025.** By April 2026, Claude Mythos Preview — Anthropic's gated frontier model, the one they've deliberately kept out of general release — reached **about 52x** on the same kind of work. (Mythos is real, by the way, and worth knowing about; it's the model behind [Project Glasswing](https://red.anthropic.com/2026/mythos-preview/), Anthropic's effort to harden critical infrastructure, and it scored 97.6% on the 2026 USA Math Olympiad against Opus 4.6's 42.3%.) Three-x to fifty-two-x in under a year on a single optimization benchmark.

And then the example that crystallizes all of it: on a supervision task where models were set loose to recover lost ground against human researchers, the agents **recovered 97% of the gap. The humans recovered roughly 23%.** When the work itself was AI research, the AI was the one closing the distance — and the humans were the ones falling behind.

If you want a more grounded read on what these jumps mean for individual model releases rather than the macro trend, I went deep on the [Opus 4.7 capability analysis](https://www.mejba.me/blog/opus-4-7-analysis) — but the macro trend is the point here. The machine isn't just doing more. On the open-ended, judgment-heavy work, it's increasingly doing it *better.* Which raises the only question that actually matters: what happens next?

## The Three Futures — And Which One We're Standing In

Anthropic doesn't predict. It scenarios. The report lays out three ways this goes, and the honest move is that they don't tell you which one is right. I'll tell you which one I think we're already in.

**Scenario 1 — The Plateau.** The trend stalls. The four-month doubling hits a wall, the curves flatten, and we're left with today's capabilities — which then diffuse widely across the economy. Powerful, but bounded. No runaway. In this world the existing tools are the ceiling, and the next decade is about deployment, not breakthrough.

**Scenario 2 — Human-Guided Compounding.** AI labs keep seeing compounding efficiency gains. Each generation of models helps build the next one a little faster, with humans still firmly in the loop — directing, reviewing, approving. The acceleration is real but it runs through human hands at every step. The 80% of Anthropic's code that Claude writes still gets merged by a human who says yes.

**Scenario 3 — Recursive Self-Improvement.** AI systems become capable of *fully* designing and developing their own successors. The human steps out of the loop not because they choose to but because they can no longer keep up. The model improves the model, which improves the model, and the doubling curve stops being a metaphor.

Here's my read, and I hold it loosely: **we are unmistakably in Scenario 2 right now.** Claude writing 80% of merged code with humans approving it is the literal definition of human-guided compounding. The Mythos-driven 52x, the 64% better-than-human research calls — those are loops accelerating *with* a human still holding the pen. That's not speculation. That's Tuesday at Anthropic, and on a smaller scale, it's Tuesday in my own workflow.

The thing about Scenario 2 is that it sits right next to Scenario 3. The boundary isn't a cliff you'd see coming. It's the moment the human in the loop becomes a rubber stamp — still technically there, no longer actually deciding. And the report's deepest anxiety is that you might not notice when you cross it. That's the part nobody wants to talk about, so let's talk about it.

## The Quiet Risk Nobody Puts on a Thumbnail

When people imagine AI risk, they imagine a dramatic moment. A red light. A system going rogue. Killer robots. Anthropic's report points at something far less cinematic and, to me, far more plausible: **the slow erosion of oversight you don't notice happening.**

The mechanism is compounding misalignment. If a model has some subtle flaw in its values or judgment — not malice, just a slight miscalibration — and that model helps design the next model, the flaw doesn't get caught. It gets *inherited,* and possibly amplified. Anthropic says plainly that how the alignment problem gets solved in this future "is something we are least certain about." That's a striking thing for a safety-focused lab to admit in print.

Pair that with the interpretability problem. As models get more capable and start building their successors, our ability to look inside and understand *why* they do what they do degrades. We're already at the point where the systems are too complex to fully audit by hand. The risk isn't a machine that hates us. It's a machine we increasingly can't read, making decisions we increasingly can't check, inside loops moving faster than we can review.

That erosion is quiet. There's no alarm. One day the human review is meaningful, and some later day it's theater, and there's no klaxon marking the transition. That's why Anthropic is doing something I've never seen a frontier lab do: arguing, publicly, for the ability to hit a brake.

And the brake, it turns out, is the hardest part of the whole thing.

## Why "Just Pause It" Doesn't Work

The intuitive fix is obvious: if this gets dangerous, slow down. Anthropic agrees in principle and then explains, with uncomfortable clarity, why it's nearly impossible in practice.

A credible pause can't be one lab acting alone — that just hands the lead to whoever doesn't pause. It would require multiple well-resourced labs agreeing to stop under the same conditions, *and verifying that everyone actually stopped.* And here's the line that stuck with me: training runs are far easier to conceal than missile silos.

Think about nuclear arms control. It's hard, but it works partly because you can *see* the enrichment facilities, count the warheads, fly the satellites. There's a physical footprint. A frontier training run has no such footprint. It's a cluster in a data center that looks exactly like every other cluster in every other data center. The classic "trust but verify" framework that underpins every arms treaty runs straight into a wall, because the "verify" half has almost nothing to grab onto.

So the safety conversation isn't really "should we be able to pause." It's "*could* we even tell if someone didn't." That's a genuinely unsolved problem, and Anthropic putting it in writing is the report quietly admitting the brake pedal might not be connected to anything yet.

If you're building real systems, this is where I'd resist the urge to spiral and instead get practical. I think a lot about how to keep [meaningful human oversight inside self-improving loops](https://www.mejba.me/blog/self-improving-claude-code-systems) on the small scale — even a reflection loop that rewrites its own prompts needs a human checkpoint that's real, not ceremonial. The macro problem is the same problem, just without anyone able to enforce the checkpoint. Which brings the whole thing back to you and me.

## What Your Job Actually Becomes

Here's the part I'd care about most if I were reading this, because it's the part with a decision in it.

If the model writes the code, runs the experiments, and increasingly makes the better call on what to try next — what's left for the human? The honest answer is that the center of human value is sliding, fast, from **execution to judgment.**

For years, being a great engineer meant being a great *doer.* You could implement the thing. You knew the syntax, the patterns, the gotchas. That skill is rapidly commoditizing — not worthless, but no longer the thing that makes you valuable, because the model does it faster and, on open-ended work, often better. What doesn't commoditize is knowing *which problems are worth solving,* having a vision the agent can be pointed at, and exercising the taste to recognize when its 76%-successful output is in the wrong 24%.

I felt this shift personally before I had words for it. I wrote about [killing a working product because it was AI-added instead of AI-first](https://www.mejba.me/blog/ai-first-company-2026-solo-operator) — and the lesson underneath that decision was exactly this. My value wasn't in building the Laravel app. The model could do that. My value was in the judgment to see it shouldn't exist. Vision over velocity. Direction over dexterity.

This is also where the report's most quotable claim earns its keep: **100-person companies could do the work of 10,000- or 100,000-person organizations.** That's not a productivity-software promise. It's a statement about leverage so extreme that the bottleneck stops being labor and becomes *taste* — the rare ability to point overwhelming capability at the right target. And it implies a widening gap I'm watching open in real time: between people who treat these models as a fancier autocomplete, and people who've reorganized their entire workflow around directing fleets of agents. That gap is going to define careers this decade. The casual user gets a nice productivity bump. The agent-native operator gets a 100x organization.

So where does that leave the AGI question we started with?

## So — Is It AGI?

By the practical definition — autonomously making real progress on open-ended problems with no answer key — the honest answer is yes. Not coming. Here. A 76% success rate on the work that was supposed to be the human-only tier, a four-month doubling on autonomous task length, a model winning the "what should we try next" call against experts two times in three. If that's not general intelligence in the only sense that affects your life, the word has lost all meaning.

Think back to where we started — me reading the report twice and only catching it the second time. The reason I almost missed it is the same reason I think most of the industry is missing it: we were all waiting for the dramatic version. The conscious machine. The red light. We were so busy scanning the sky for the sci-fi AGI that we didn't notice the practical one had already moved into the building and started merging code.

The worst possible response to this report is the one I see most: a shrug and a "that's just hype, it's not *really* AGI." That dismissal isn't skepticism. It's a refusal to update on direct evidence from the lab with the most reason to downplay it. Skepticism is good. Reading the numbers and choosing not to believe them is something else.

Here's my challenge for you, and it'll take less than an hour. Open whatever AI tool you use most. Hand it a genuinely open-ended task from your actual work — not a typo fix, something with no clean answer. Scope it well, set a guardrail, and let it run. Then watch what it does, and ask yourself one question: *am I directing this, or am I just clicking accept?* Your honest answer to that is the difference between being on the right side of the gap that's about to open — and the wrong one. I know which side I'm building toward. The only question is whether you start before the curve doubles again.

## Frequently Asked Questions

### Is AGI actually here in 2026?

By a practical definition — autonomously solving open-ended problems with no predefined answer — yes, the capability is already here as of 2026. Anthropic's June 2026 report shows Claude reaching a 76% success rate on most open-ended tasks. It is not the conscious, sci-fi version of AGI, but it is general problem-solving in the only sense that affects real work.

### What does "When AI builds itself" actually claim?

Anthropic's report claims AI is now writing more than 80% of the code merged into its own codebase and is increasingly making better research decisions than human experts. For the full breakdown of every verified statistic, see the sections above. The core argument is that human-guided compounding is real today, with recursive self-improvement as a possible — but not inevitable — next step.

### What is the difference between narrow AI and AGI?

Narrow AI does one bounded task well, like a chess engine or spam filter, and is useless outside that task. Practical AGI makes real progress on open-ended problems with no answer key, reward function, or correct output to copy. The shift from narrow to general is measured by success rate on undefined problems, not by consciousness.

### Why can't we just pause AI development if it gets dangerous?

A credible pause would require multiple well-resourced labs stopping under the same conditions and verifying that everyone actually complied. As Anthropic notes, training runs are far easier to conceal than missile silos, so the "verify" half of "trust but verify" has almost nothing to grab onto. That verification gap, not the decision to pause, is the hard part.

### How should developers respond to AI writing most code?

Shift your value from execution to judgment — knowing which problems matter, scoping work for agents that can now run autonomously for hours, and recognizing when AI output lands in the wrong 24%. The doer-to-director transition is the central career move of this decade, explored in my piece on building an AI-first company above.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
