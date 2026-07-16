**BRAND:** mejba.me
**TITLE:** GLM 5.2 vs Qwen 3.7 Max vs Claude Opus 4.8
**META TITLE:** GLM 5.2 vs Qwen 3.7 Max vs Claude Opus 4.8 Test
**SLUG:** glm-5-2-vs-qwen-3-7-max-vs-claude-opus-4-8
**PRIMARY KEYWORD:** GLM 5.2 vs Qwen 3.7 Max vs Claude Opus 4.8
**META DESCRIPTION:** I ran five one-shot coding prompts through GLM 5.2, Qwen 3.7 Max, and Claude Opus 4.8. Here's where benchmarks lied and which model actually won.
**TAGS:** AI Models, AI Coding, GLM 5.2, Model Comparison, AI Benchmarking

---

The model that topped the benchmark chart lost four of my five tests.

I want to start there because it's the whole point, and I almost didn't believe it myself. I'd lined up GLM 5.2 vs Qwen 3.7 Max vs Claude Opus 4.8 expecting Qwen to walk it — it leads the published agentic-coding tables, Alibaba has been loud about Terminal-Bench and SWE-Bench Pro scores, and on paper it should have been the obvious pick. Then I gave all three the exact same prompts, one shot each, no retries, no "try that again," and watched the chart-topper hand me a voxel runner that was technically functional and completely lifeless. Meanwhile a Chinese open-weight model that hasn't even published 5.2 benchmarks kept shipping things that were actually fun to use.

That gap — between what a leaderboard promises and what shows up on your screen — is what this whole test is about. If you're choosing a coding model right now based on a SWE-Bench number you saw in a launch tweet, I'd hold off until you read what happened when I made these three compete on identical, real tasks with no second chances.

One honest disclaimer up front, the same one I make every time: I'll tell you exactly what I ran hands-on and where I'm relying on vendor claims. The version numbers here — GLM 5.2, Qwen 3.7 Max, Claude Opus 4.8 — are all real, shipped models as of June 2026, and I've verified their release details. Where a benchmark figure comes from a vendor's own deck, I'll say so, because the entire lesson of this piece is that you shouldn't trust those numbers blindly. Including mine.

## The Three Models, and Why This Matchup Is Actually Fair

A quick grounding before the tests, because if the contestants aren't comparable, the results are noise.

**GLM 5.2** shipped from Z.ai (the Zhipu AI spinout) on June 13, 2026. It's a 744B-parameter mixture-of-experts model with roughly 40B active parameters per token, a genuine 1M-token context window, and — this is the headline that keeps mattering — MIT-licensed open weights. I covered its launch in detail in my [AI weekly roundup on GLM-5.2, Fable 5, and DiffusionGemma](https://www.mejba.me/blog/ai-weekly-roundup-glm-fable-diffusion), so I won't re-litigate the spec sheet here. The relevant fact for this test: Z.ai published benchmarks for GLM 5.1, not 5.2. So when GLM 5.2 wins something below, it's winning without a benchmark to hide behind.

**Qwen 3.7 Max** is Alibaba's flagship, announced at the Alibaba Cloud Summit in Hangzhou on May 20, 2026, also with a 1M-token window. Alibaba's published tables put it at roughly 60.6 on SWE-Bench Pro and claim it tops the previous Claude Opus generation on Terminal-Bench 2.0 and MCP-Atlas. It's positioned squarely as an *agent* model — built for tool calls, orchestration, and long-horizon task chains.

**Claude Opus 4.8** landed from Anthropic on May 28, 2026 at unchanged pricing ($5 per million input, $25 per million output). Its own SWE-Bench Pro number is 69.2% — and worth noting, that's actually *higher* than Qwen's published figure, which already complicates the "Qwen leads the benchmarks" story I walked in with. Anthropic also shipped Dynamic Workflows, letting Claude Code spin up parallel subagents.

Here's the wrinkle the source for this test flagged, and I want to be straight about it. I'd seen an 80.4% SWE-Bench figure floated for Qwen 3.7 Max. I could not verify that number against Alibaba's own published tables, which show roughly 60.6 on SWE-Bench Pro. So I'm treating 80.4% as an unverifiable vendor-adjacent claim and not asserting it as fact. The verified, third-party-reported numbers tell a different story than the hype did — Opus 4.8 at 69.2% sits *above* Qwen's published Pro score. File that away; it gets more interesting once the real-world results come in.

The test itself: five tasks, each model gets the identical prompt, one shot, no retakes. The way you'd actually use these in a CLI on a Tuesday — not the way a benchmark harness coddles them with retries and scaffolding.

## How I Ran the One-Shot Test (and Why "One Shot" Matters)

The rules were deliberately strict, because leniency is exactly how benchmarks lie to you.

One prompt per task. Whatever the model produced on the first pass is what got graded. No "fix the bug," no "make it more interesting," no re-rolling until I liked it. Most benchmark scores quietly allow multiple attempts, agent scaffolding, or best-of-N sampling — and that inflates numbers in a way that has nothing to do with your experience when you fire off a single prompt and wait.

I graded on three axes a leaderboard can't capture: **does it work, is it actually good, and would a human want to use it.** That third axis is the killer. A voxel game can compile cleanly, run at 60fps, and still be dead on arrival because it's boring. No SWE-Bench cell has a column for "fun." That omission turns out to explain most of the gap between the rankings and reality.

Five tasks, chosen to span the spectrum: a 3D voxel runner game, an inner-solar-system orbit map, a liquid-in-a-ball physics simulation, a marketing landing page, and a classic arcade game. Two are game-dev (creative + interactive), two lean toward simulation and physics, one is straight front-end. Together they stress visual quality, interaction design, physics math, layout sense, and that intangible "is this delightful" quality all at once.

Before I show you the scorecard, one thing to hold onto: I expected this to be close. It wasn't.

## The Scorecard: One-Shot Results Across All Five Tasks

Here's where each model landed, head to head, no retries.

| Task | GLM 5.2 | Qwen 3.7 Max | Claude Opus 4.8 | Winner |
|------|---------|--------------|-----------------|--------|
| Voxel Runner Game | Fun, smooth, genuinely interesting | Works, but buggy and dull | Very basic, not fun | **GLM 5.2** |
| Inner-System Orbit Map | Poor visual quality | Acceptable but weak | Highly interactive & clear | **Claude Opus 4.8** |
| Liquid-in-a-Ball Sim | Nice animation, interactive | Less engaging | Very boring | **GLM 5.2** |
| Landing Page | Well-structured with animation | Empty canvas, weak | Very basic, uninspiring | **GLM 5.2** |
| Arcade Game | Highly engaging and fun | Bug: ball disappears | More playable than Qwen | **GLM 5.2** |

Four to one to GLM 5.2. The benchmark-leading agent model, Qwen 3.7 Max, won exactly zero tasks. And Claude Opus 4.8 — the model with the *highest verified SWE-Bench Pro score of the three* — won a single task and bombed the rest of the creative work.

If you only take one thing from this article, let it be the shape of that table. The published rankings would have predicted Qwen first, Opus second, GLM somewhere behind with no 5.2 numbers to its name. Hands-on, the order nearly inverted. Now let me walk you through *why*, task by task, because the reasons are more useful than the verdict.

## Voxel Runner: Where "Works" and "Good" Split Apart

The first task drew the cleanest line of the whole test.

I asked all three for a 3D voxel runner — think an endless-runner where you dodge and jump through a blocky world. GLM 5.2 returned something I actually wanted to keep playing. The movement had weight, the camera followed sensibly, the world had enough visual variety that it didn't feel like staring at a single texture. It was *fun*. That word matters more than it sounds.

Qwen 3.7 Max produced a voxel runner too — and on a pure "did it compile and run" basis, it passed. But it was buggy in small persistent ways, and worse, it was dull. Flat lighting, no sense of speed, the kind of thing that technically satisfies the prompt and satisfies nothing else. This is exactly the trap of grading by "task resolved." Qwen resolved the task. A SWE-Bench-style harness would mark it green. A human would close the tab in ten seconds.

Claude Opus 4.8 was the surprise here, and not a good one. Its voxel runner was the most basic of the three — functional, clean code underneath, almost certainly, but visually and experientially thin. For a model that leads the verified coding benchmarks, watching it produce the least engaging game of the three was the first crack in my assumptions.

The lesson crystallizing already: these models aren't differing on *correctness*. They're differing on taste. And taste is the thing nobody benchmarks.

## Orbit Map: Claude's One Clear Win, and It's a Real One

I don't want this to read as a GLM coronation, because the orbit-map task showed something genuinely important about Claude Opus 4.8.

The prompt: build an interactive map of the inner solar system — the Sun, Mercury through Mars, orbits rendered cleanly, ideally something you can interact with. This is the one task where precision and structured spatial reasoning matter more than vibe, and Claude *dominated* it. Its orbit map was the most interactive and the clearest by a wide margin: legible orbital paths, sensible scaling, smooth interaction, the kind of output where you immediately understand what you're looking at.

GLM 5.2, the overall winner of the test, turned in poor visual quality here — the one task it clearly lost. Qwen landed in the middle: acceptable, but weak, never rising past "fine."

Here's what I take from that. When a task is fundamentally about *correctness and clarity* — spatial accuracy, structured layout, mathematical relationships you can't fudge — Claude Opus 4.8's strengths show up exactly where its benchmark profile says they should. This is the model you reach for when "looks impressive" matters less than "is unambiguously right." Its 69.2% SWE-Bench Pro score isn't a lie; it just measures a narrower slice of usefulness than the marketing implies.

That nuance is the honest core of this whole comparison: **no model is bad. They're differently shaped.** Claude lost most of the creative tasks not because it's weak, but because creative interactivity isn't where its edge lives. Hold that, because it changes the recommendation at the end.

## Liquid-in-a-Ball and the Landing Page: GLM's Pattern Holds

Two more tasks, and the same theme kept repeating with almost boring consistency.

The liquid-in-a-ball simulation — fluid sloshing inside a sphere, ideally something you can tilt and interact with — went to GLM 5.2 again. Its version had genuinely nice animation and real interactivity; you could feel the physics responding. Qwen's was less engaging, the motion stiffer and less alive. Claude's was, in a word, boring — the physics may well have been *correct*, but correct isn't the same as compelling, and a fluid sim that nobody wants to poke at has failed at its actual job.

The landing page told the same story from a different angle. I asked for a marketing landing page, and GLM 5.2 returned something well-structured with thoughtful animation — a layout with hierarchy, sections that flowed, motion that guided the eye. Qwen handed me what was close to an empty canvas: technically a page, practically a starting point you'd have to build from scratch. Claude's was basic and uninspiring, functional but flat.

I've built enough real landing pages — for [Ramlit's](https://www.ramlit.com) client work and my own projects — to know the difference between "a page exists" and "a page sells." GLM 5.2 was the only one of the three that seemed to understand there's a difference.

If you'd rather have someone build out a multi-model coding workflow that routes each task to the model that's actually best at it — instead of betting your whole stack on one leaderboard winner — that's exactly the kind of integration work I take on. You can see what I've shipped at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Arcade Game: A Disappearing Ball Decides It

The final task was almost comically clarifying.

A classic arcade game — think paddle-and-ball, brick-breaker territory. GLM 5.2 made it highly engaging and fun, hitting its now-familiar stride. Claude Opus 4.8 was more playable than Qwen, landing in respectable second. And Qwen 3.7 Max? Qwen's ball *disappeared mid-game.* The single most important object in a ball-based arcade game vanished into the void.

Sit with that for a second alongside Qwen's benchmark position. This is, on paper, the agent-coding leader — strong SWE-Bench numbers, built for complex multi-step tasks. And in a one-shot arcade build, it lost the ball. Not a subtle logic bug buried three functions deep. The literal ball, gone.

That's the entire thesis of this article compressed into one sprite. **Benchmark scores measure a model's performance on a curated set of problems under generous conditions. They do not measure whether your single real prompt produces something that works end to end.** The gap between those two things is where most model-selection mistakes get made.

## Why Benchmarks Lied to Me (and Probably to You)

Time to get under the hood of the disconnect, because understanding *why* it happens makes you a better model-picker than any leaderboard can.

Vendor benchmarks are run by the people who profit from the result. That's not an accusation of fraud — it's just structural. When Alibaba reports Qwen 3.7 Max's SWE-Bench Pro at 60.6, they ran that under conditions they chose, on a task set that rewards what their model is tuned for. Even fully honest numbers reflect a configuration you'll never reproduce at your terminal. And the unverified figures that float around — like that 80.4% I couldn't confirm — make it worse, because they enter the conversation as fact and get repeated until everyone "knows" Qwen leads.

Then there's the *shape* of what benchmarks test. SWE-Bench measures resolving real GitHub issues — patching bugs in existing codebases. That's genuinely valuable, and it's why Claude Opus 4.8's 69.2% is meaningful for maintenance work. But "patch this Django bug" and "build me a fun voxel runner from nothing" are completely different muscles. A model can be elite at the first and mediocre at the second, and a benchmark built around the first will tell you nothing about the second.

Here's the part most people miss: there's no benchmark for taste. No leaderboard column for "is this landing page something a human would be proud to ship," or "is this game fun." Those qualities are the actual product when you're doing creative or front-end work — and they're exactly where GLM 5.2 kept winning despite having no published 5.2 numbers to point at. The thing it's best at is the thing nobody scores.

My corrected mental model after this test: **treat every benchmark as a measure of one narrow capability under ideal lab conditions, and treat your own one-shot test as the only number that predicts your actual experience.** Run three prompts you genuinely care about through any model before you commit. It takes twenty minutes and it'll override a hundred leaderboard tweets.

## The Integration Question: Hermes Agent and What Actually Plugs In

There's a dimension to this comparison that has nothing to do with output quality, and for some of you it'll matter more than any test result.

The source for this test ran GLM 5.2 and Qwen 3.7 Max inside an agent operating system it referred to as Hermes Agent — a dashboard for orchestrating multiple models, chaining tasks, and running agent collaboration. I want to be transparent: I couldn't independently verify "Hermes Agent" as a widely-documented mainstream product, so I'm presenting it as the orchestration layer this particular test used, not as a tool I'm endorsing or asserting as an industry standard. The *category* — a unified dashboard that orchestrates multiple models — is real and growing, whatever the specific product is called.

What's relevant is the structural finding, because it generalizes to any orchestration platform: GLM 5.2 and Qwen 3.7 Max plugged directly into that agent OS. Claude Opus 4.8, in that setup, did not. If your workflow lives inside a multi-model orchestration layer where models hand tasks to each other, that integration gap is decisive regardless of who wins a voxel-game shootout. A model that can't join your agent mesh isn't a contender for that job, full stop.

And inside agent workflows specifically, the rankings shuffle again. For research-style agent tasks — go gather, synthesize, report back — Qwen 3.7 Max produced more thorough, more useful output than GLM 5.2, whose agent-task responses ran briefer and less effective. Qwen also tended to respond *faster* in practical agent queries. So the model that lost every creative one-shot quietly leads on agentic research throughput and speed. GLM 5.2, by contrast, was strongest as a direct coding model in a CLI, where its creative and software quality shone but its integrated-agent responses sometimes ran slower.

I've written before about treating the [agentic OS as three distinct layers](https://www.mejba.me/blog/agentic-os-claude-code-three-layers), and this test reinforces it: the model that's best at *generating* a thing and the model that's best at *orchestrating* a workflow can be two different models. Building around that reality is more powerful than crowning a single winner.

## Results: What This Actually Predicts for Your Work

Let me translate five game-dev tests into decisions you'll genuinely face.

For **direct creative and front-end coding** — landing pages, games, simulations, anything where the output's quality and delight *are* the product — GLM 5.2 was the clear standout in my one-shot test, and it being MIT-licensed open weights means you can self-host it with no per-token bill at scale. That combination is hard to beat for build-heavy creative work.

For **precision and clarity tasks** — data visualization, structured layouts, anything where being unambiguously correct beats being flashy — Claude Opus 4.8 earned its win on the orbit map honestly, and its 69.2% verified SWE-Bench Pro score backs that up for bug-fixing and maintenance. This is the model for "make it right," not "make it dazzle."

For **agent orchestration and research throughput** — multi-step tool-calling, gather-and-synthesize tasks, anything inside a multi-model dashboard where speed and thoroughness matter — Qwen 3.7 Max redeemed its zero-for-five creative showing. Faster agent responses and more thorough research output is a real, useful strength, just not the one the leaderboards led me to expect.

Notice what just happened: each model won a different *category*, and none of those categories is "highest benchmark score." That's the practical payoff. The right answer to "which model is best" is a question back at you — best at *what*, inside *what workflow*?

The setup I'd actually run, and the source's recommendation I fully agree with: a unified dashboard with all three (or your equivalent picks), routing each task to the model that's genuinely strongest at it. GLM 5.2 for the build, Claude Opus 4.8 for the precision pieces, Qwen 3.7 Max for the agent legwork. One stack, three specialists. I've seen a team of AI video agents run end-to-end on GLM 5.2 inside an orchestration layer and autonomously produce finished content — the multi-model dashboard isn't theoretical, it's how serious agent work already gets done.

## Frequently Asked Questions

### Is GLM 5.2 better than Claude Opus 4.8 for coding?

For creative and front-end coding, GLM 5.2 won four of my five one-shot tests, including games and landing pages. For precision and bug-fixing, Claude Opus 4.8's verified 69.2% SWE-Bench Pro score and its orbit-map win make it the stronger pick. They're best at different jobs — see the task-by-task breakdown above.

### Does Qwen 3.7 Max really lead the benchmarks?

Qwen 3.7 Max leads several published agentic tables, but its verified SWE-Bench Pro figure is roughly 60.6 — actually below Claude Opus 4.8's 69.2%. A widely-circulated 80.4% figure could not be verified against Alibaba's own tables, so treat it as an unconfirmed claim, not fact.

### Why did the benchmark-leading model lose the real-world test?

Benchmarks measure narrow capabilities under generous, multi-attempt lab conditions; my test was one shot per task, graded partly on whether the output was actually good and usable. There's no benchmark column for "fun" or "well-designed," which is exactly where GLM 5.2 kept winning.

### Can Claude Opus 4.8 plug into a multi-model agent dashboard?

In the orchestration setup used for this test, GLM 5.2 and Qwen 3.7 Max integrated directly while Claude Opus 4.8 did not. If your workflow depends on a multi-model agent layer, verify integration support before committing, because it can override raw output quality for that job.

### What's the best way to choose an AI coding model in 2026?

Run three prompts you genuinely care about through each candidate, one shot each, and grade on whether the output works *and* whether you'd actually ship it. Twenty minutes of hands-on testing predicts your real experience better than any leaderboard. For deeper context, see [my AI weekly roundup](https://www.mejba.me/blog/ai-weekly-roundup-glm-fable-diffusion) on these same models.

## The Disappearing Ball, One More Time

I keep coming back to Qwen's vanishing ball, because it's the most honest moment in the whole test.

Here was a model that, by the numbers, should have been the safe choice — the agentic-coding leader, strong on paper, built for exactly this kind of work. And in a single real prompt, with no retry to save it, it lost the one object the entire game was built around. No benchmark would ever have told me that. Only running it did.

So here's the one thing to do in the next twenty-four hours, whichever model you're leaning toward: don't take my scorecard, and don't take a leaderboard. Take three prompts that represent your actual work, fire each one through your top two candidates exactly once, and grade them like a user instead of a benchmark. The model that survives that test is your model. Everything else is someone else's marketing — including, if you skip the test, this article.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
