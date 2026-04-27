**BRAND:** mejba.me
**TITLE:** DeepSeek V4 Pro Review: 1.6T Open-Source Tested
**META TITLE:** DeepSeek V4 Pro Review: 1.6T Open-Source AI Tested
**SLUG:** deepseek-v4-pro-open-source-ai-review
**PRIMARY KEYWORD:** DeepSeek V4 Pro review
**META DESCRIPTION:** I spent a weekend running DeepSeek V4 Pro through real builds. Here's what the 1.6T open-source model gets right, where it breaks, and the honest cost math.
**TAGS:** DeepSeek V4 Pro, Open Source AI, AI Model Review, OpenCode, Mixture of Experts

---

The moment I realized DeepSeek V4 Pro actually mattered was at 11:47 PM on a Thursday. I had four terminal windows open, each running a separate instance of the model through Open Code, and all four were simultaneously solving different parts of a side project I'd been procrastinating on for weeks. A 3D visualizer. A landing page. A Python data pipeline. A browser extension. My Open Code dashboard said I'd spent $0.19 in compute so far.

Nineteen cents.

The same workload on Claude Opus 4.7 would have burned through roughly $42 of API credits by that point. On GPT-5.5 Pro, we'd be closer to $160. I checked the numbers three times because the math felt broken. It wasn't. The math was fine. The industry was the thing that had shifted underneath me while I wasn't paying attention.

That's the headline I want to start with, because if you only read the first paragraph of this DeepSeek V4 Pro review, I want you to leave with the right takeaway: open-source AI just caught up on *cost* in a way that changes the calculus for every indie developer, every small agency, and every founder who's been silently dreading their monthly Anthropic invoice. The benchmarks aren't quite there at the very top. The long context is shakier than the spec sheet suggests. The censorship is real. But the cost collapse is the story, and most of the takes I've read so far miss it because they're too busy arguing about benchmark leaderboards.

I spent a full weekend running the 1.6 trillion parameter model through actual work — not toy benchmarks, not curated demos, real code I was going to ship anyway. This is what I found.

## What DeepSeek V4 Pro Actually Is

Let me get the spec dump out of the way quickly because you've probably already seen it scattered across ten different sites since the April 24 release.

DeepSeek V4 Pro is a 1.6 trillion parameter Mixture-of-Experts model with roughly 49 billion active parameters per token. That "active" number is the one that matters for inference cost — you're not paying to run 1.6T worth of compute on every message, you're paying for the narrow slice of experts the router wakes up for your specific prompt. It's about 60% bigger than the previous largest serious open-source release, and it's the first open-weights model where I genuinely think "matches frontier" is a defensible claim rather than marketing.

The context window is advertised at one million tokens. We'll come back to that number because the full story is more complicated than the marketing. The practical ceiling I hit in testing was closer to 128K before quality visibly degraded, and the cliff gets steep past about 180-200K. That's still excellent — it's just not the "one million tokens" the homepage promises.

Architecturally, the model introduces a hybrid attention scheme called Compressed Sparse Attention (CSA) paired with Heavily Compressed Attention (HCA). The result is that V4 Pro's 1M-token configuration uses roughly 27% of the single-token inference FLOPs and 10% of the KV cache compared to V3.2. That's the engineering trick behind the low price. DeepSeek didn't just scale up — they rewrote the attention stack so that every token costs dramatically less to process, and then they passed almost all of that saving directly to the API price.

The training story is the part that's going to get written about for years. V4 Pro was trained on a mix of Huawei Ascend 950PR chips and older Nvidia hardware (A100s, reportedly some H100s that made it past export controls). The run took roughly 14-16 months, including a full restart after a major training failure partway through. Reuters confirmed in April that the final model was validated on both Nvidia and Ascend NPU platforms. Total compute cost for the run landed around $5.6 million on a 16,000-GPU cluster. For context, that's a rounding error compared to what the American frontier labs are spending per generation, and it was done partly on domestic Chinese chips because ASML export controls didn't leave any other option.

I don't want to turn this into a geopolitics blog, but you can't honestly review DeepSeek V4 Pro without acknowledging that the entire existence of this model is a direct response to the hardware restrictions of the last three years. The efficiency tricks in the architecture, the hybrid chip training pipeline, the aggressive pricing — all of it is shaped by the fact that DeepSeek could not simply buy a hundred thousand H200s and throw them at the problem. They had to be clever instead. And now clever is threatening to beat expensive.

That's the context I took into testing.

## The Setup: How I Actually Tested This

I'll be specific about my setup because I want you to be able to reproduce any of this if you're curious.

I ran DeepSeek V4 Pro through three different access points over the weekend:

First, the Open Code Go subscription. Five dollars for the first month, ten per month after that, with access to V4 Pro, V4 Flash, and a handful of other open-weights models. This is the one I'd recommend for anyone reading this who wants to kick the tires without touching the raw API. Four parallel instances running simultaneously, low/medium/high/max reasoning effort toggles, and a usable agent harness that handles tool calls correctly.

Second, the DeepSeek API directly. This is the bare metal option — you get whatever wrappers you build yourself, you pay per token, and you're responsible for the agent scaffolding. The pricing here is where the "7x cheaper than Opus 4.7" and "40x cheaper than GPT-5.5 Pro" numbers come from. Decrypt put the V4 Pro spread at roughly 98% cheaper than GPT-5.5 Pro for comparable output workloads, which lines up with what I measured.

Third, local inference through Ollama, using the 284B V4 Flash variant rather than the full Pro model. The full 1.6T Pro model is technically downloadable but not practically runnable on anything a solo developer owns — you're talking about a multi-hundred-gigabyte weight set and enough VRAM to make a small data center cry. Flash is the one you can actually run locally if you have a serious workstation, and I included it because a lot of the "is this usable?" question for open-weights models depends on the fallback story when the API goes down.

My test workload had four pieces. I wanted tasks that represented real work, not leaderboard bait.

The first task was an interactive DeepSeek architecture explainer — a single-page web app that visualizes how the Compressed Sparse Attention layers route tokens through the expert mixture. I chose this deliberately because explaining your own architecture is the kind of task where a model should have home-field advantage. If V4 Pro couldn't build a correct diagram of its own internals, that would be a tell.

The second task was an SVG plant-growth animation, frame-accurate, with a timeline controller. This is a surprisingly good test of a model's ability to hold a coherent visual system in its head across many small geometry decisions.

The third task was an HTML5 karting game with keyboard controls, a lap counter, and basic AI opponents. Game logic is where a lot of models quietly fall apart because you need consistent state management across events.

The fourth task was an exoplanet visualizer that pulled live data from the NASA Exoplanet Archive and rendered orbital distances to scale. This one tested API integration, data wrangling, and the model's ability to reason about real numbers from a real source.

I ran each task on V4 Pro and a parallel run on Claude Opus 4.7 through Claude Code, with the same prompts. I also re-ran the first two tasks on GPT-5.5 through Codex for a third comparison point, because my [GPT-5.5 vs Opus 4.7 comparison](/blog/gpt-5-5-vs-opus-4-7-comparison) set my baseline for what "good" looks like at the frontier.

Total wall-clock time across everything: about four hours. Total Open Code spend: roughly twenty cents. That twenty cents number is the one I can't stop thinking about.

## Test One: The Architecture Explainer

The first thing V4 Pro did that surprised me was get the routing diagram almost completely right on the first attempt. I asked for "an interactive explainer of how Compressed Sparse Attention routes tokens through your mixture-of-experts layers — clickable, with a live token counter, and it should visually show which experts activate for a given input." I gave it no reference code.

What came back was a working React component with a tokenizer simulation, a router visualization, and a pretty clean animation showing which experts fired for each token. It was not perfect — the expert count it displayed was off by a factor of two, and the animation glitched slightly when you paused mid-token — but it worked, and the architecture was correct.

Opus 4.7 produced a visually more polished version of the same app. Cleaner typography, better-organized component tree, smarter default state. But Opus also took longer (about 3x) and cost approximately $1.80 in Claude Code credits versus four cents on Open Code.

The meaningful comparison isn't "which one is better." It's "what's the marginal value of the polish?" If you're shipping this to a client, the Opus polish is probably worth it. If you're prototyping an internal tool, or you're an indie dev iterating fast, DeepSeek's output was perfectly acceptable and the economics are on a completely different planet.

One concrete difference I want to flag: V4 Pro's code was less opinionated about structure. It wrote components that worked but didn't anticipate future modifications the way Opus does. If you're going to maintain this code for two years, Opus's output is easier to extend. If you're going to delete this code in two weeks, V4 Pro's output saves you money without costing you anything you'll remember.

## Test Two: The SVG Plant Animation

This is where V4 Pro first hit a wall that I want to be honest about.

The animation itself worked. The plant grew, the timeline scrubber functioned, the SVG paths were mathematically reasonable. But when I asked for "a second species with different branching behavior — something more fractal, less symmetric," the model's second pass partially clobbered the first. It re-wrote sections of the original species' growth logic in ways that introduced subtle regressions.

Opus 4.7, given the same follow-up, produced a clean additive diff. It added the second species without touching the first, which is what a senior engineer would do.

This is the pattern I kept seeing across the weekend. V4 Pro is an excellent one-shot coder — you describe a thing, it builds that thing, and the thing works. It is a meaningfully less sophisticated iterative coder. When you need it to hold a large mental model of existing code and make surgical changes without breaking adjacent systems, it's closer to a junior engineer than a staff engineer. For context, this is roughly where Kimi K2.6 landed when I ran it through similar tests in my [Kimi K2.6 open-source review](/blog/kimi-k-2-6-open-source-ai-coding-model) — the open-source tier is clearly converging on a "strong one-shot, weaker on iteration" profile.

I don't want to overstate this weakness. On two of my four tasks, V4 Pro's iterative behavior was fine. On the SVG animation and the karting game, it was noticeably worse than Opus. The pattern seemed to be: larger files, more state, more parallel systems to track — that's when V4 Pro started cutting corners.

## Test Three: The Karting Game

This one was the most fun to build and the most instructive comparison.

V4 Pro produced a working kart racer in a single prompt. Keyboard input, three laps, a timer, three AI opponents with reasonable behavior, a finish screen. The code was about 900 lines of HTML, CSS, and JavaScript, all in a single file. It ran. It was fun to play for about ninety seconds.

Then I asked for two follow-up changes: "add a drift mechanic with a visual skid trail" and "the AI opponents should get harder over each lap." This is the kind of layered feature request that's normal in real game development.

V4 Pro nailed the drift mechanic on the first try — the physics were actually better than I expected, with momentum preservation that felt right. But the AI difficulty scaling got tangled up with the existing AI behavior logic. The model introduced a new difficulty variable, wired it into the steering code, and then mysteriously also changed the lap counter to use the same variable, which broke lap detection.

I asked it to fix the lap counter. It fixed the lap counter but reintroduced the AI difficulty bug. This is the thing that happens with models that don't have a strong enough internal representation of the whole codebase — every edit is locally correct and globally unstable.

Opus 4.7, on the same prompts, produced fewer but more careful diffs. It also got the drift mechanic right, and its AI difficulty scaling worked without breaking anything else. It also cost about $3.40 for the full sequence versus eight cents on V4 Pro.

Eight cents versus three dollars forty. For a kart racer with drift. In 2026. I'm still mentally adjusting.

## Test Four: The Exoplanet Visualizer

This was the task where V4 Pro pleasantly surprised me. Pulling live data from the NASA Exoplanet Archive, parsing the TAP query format, rendering a scaled solar-system view with accurate orbital distances — this is the kind of task I thought might trip up an open-weights model because it requires knowing real API conventions and real astronomical units.

V4 Pro nailed it. The TAP query was correctly formatted. The unit conversions (AU to pixels, logarithmic scaling for visibility) were sensible. It even added a detail I didn't ask for: a filter to hide planets with unreliable mass estimates, because the model apparently knew that the NASA archive contains a lot of speculative data.

That last detail was the kind of moment where a model stops feeling like a code generator and starts feeling like a collaborator who's actually thought about what you're trying to build. I've had that experience dozens of times with Opus 4.7. This was the first time I had it with an open-weights model. That's the shift I'm trying to communicate in this whole DeepSeek V4 Pro review.

## The Long-Context Reality Check

Now for the part of the review where I have to flag the biggest gap between spec-sheet and reality.

DeepSeek V4 Pro's one-million-token context is technically real. You can paste in a million tokens and the model will respond. But the quality of that response drops off a cliff somewhere past 180,000-200,000 tokens, and the decline is sharp enough that I would not trust this model for any task that requires coherent reasoning over truly long inputs.

I tested this with a single 340K-token codebase dump — a real project, not synthetic text. V4 Pro could answer questions about the first 150K tokens accurately. Around the 200K mark, answers started containing references to files that didn't exist but "sounded right" based on patterns in the earlier content. By the time I was asking about code near the end of the dump, the model was essentially confabulating.

Opus 4.7, on the same 340K dump, handled it cleanly all the way through. I wrote about exactly this kind of workload in [my Opus 4.6 million-token context breakdown](/blog/opus-4-6-million-token-context) — the frontier closed-source models are genuinely leveraging their long context, not just tolerating it.

This is a real limitation. If your workflow involves dumping large codebases into context and asking for architectural analysis across the whole thing, V4 Pro is not the model for you. Use it for shorter, punchier tasks. Use Opus or Gemini for long-context work.

Practical ceiling: I'd plan for about 128K tokens of reliable working context. That's still a lot — it's more than enough for most real tasks — but it's not a million.

## The Censorship Thing

I have to say this part plainly because every review of a Chinese model tiptoes around it and readers deserve the truth.

DeepSeek V4 Pro has aggressive filtering on CCP-sensitive topics. I tested it deliberately. Ask about Taiwan's political status and you get back diplomatic non-answers. Ask about Tiananmen Square and the model either refuses outright or produces CCP-line responses. Ask about Xinjiang and it dodges.

If you are doing any kind of work that touches Chinese politics, human rights, historical events the Chinese government finds inconvenient, or geopolitical analysis involving China — this is not your model. Full stop.

For most coding work this simply doesn't come up. You're not asking your autocomplete about Tiananmen. But I want it on record in this review because I've seen too many takes that gloss over this as a minor quirk. It's not a minor quirk. It's a value alignment with a specific government, and you should know that before you pipe business-critical analysis through the model.

The local-inference workaround is worth mentioning: if you run V4 Flash through Ollama on your own hardware, the censorship layer is significantly weaker, because you're not going through the hosted API that enforces the stricter filter. The model weights still reflect the training data's biases, but the explicit refusal behavior is mostly an API-layer thing. For most users this distinction won't matter. For some it will.

## Where V4 Pro Actually Wins

Let me be specific about the tasks where I'd reach for V4 Pro before Opus 4.7 or GPT-5.5:

**High-volume automation.** If you're running an agent that processes thousands of documents, refactors hundreds of files in a batch, or generates large volumes of boilerplate, the cost math is so dramatically in V4 Pro's favor that the quality difference barely matters. You're trading a small quality delta for a 40x cost reduction. Take the trade.

**Prototype-and-discard work.** Anything where you're iterating fast on throwaway code, building internal tools nobody will maintain, or exploring design space before committing to a direction. The speed-to-working for V4 Pro in one-shot tasks is genuinely competitive with Opus, and the price lets you try more things.

**Terminal-heavy agent workflows.** V4 Pro is actually quite good on terminal-based tasks — it beats Opus on Terminal Bench and is only slightly behind on SWE Pro. If your agent spends most of its time running shell commands, reading files, and executing tool calls, this is a great fit.

**Solo dev, small agency, indie founder.** If you're currently spending $5,000-$6,000 a month on Anthropic or OpenAI credits, you can drop to $500-$1,000 on V4 Pro with most of your workflow intact and a few specific tasks routed back to the frontier models. That's a real business outcome. I've been helping small agencies [run this exact cost audit](/blog/ai-agency-retainer-model-2026-claude-code) for clients who are getting crushed by frontier-model pricing.

**Multi-instance parallel work.** The Open Code $10/month plan with four parallel instances is genuinely absurd value. I had four agents working on four different projects simultaneously for hours, and my total spend was less than a coffee.

## Where V4 Pro Loses

Equally specific about where I would not use this model:

**Long-context architectural analysis.** See the 180K cliff above. If you need a model to reason coherently across a full large codebase, V4 Pro isn't it.

**Surgical refactoring of complex existing code.** The iterative coding weakness is real. For careful incremental work in a large codebase, Opus is still meaningfully better.

**Production agent harnesses without DSML tooling.** V4 Pro doesn't have the same plug-and-play tool-calling ergonomics as Claude or OpenAI models. You need to use its DSML XML-style tool-call format, which most agent frameworks don't support natively yet. Open Code handles this for you; if you're rolling your own harness, expect integration work.

**Anything touching Chinese politics.** Already covered. Just flagging again because the review isn't complete without it.

**Latency-critical applications.** At 1.6T parameters, even with sparse activation, V4 Pro is slower than frontier closed-source models at inference. If your app needs sub-second responses, this isn't your model.

## The Hardware Story Nobody Talks About Correctly

One more thing I want to get right in this DeepSeek V4 Pro review, because most of the takes I've read either overstate or understate it.

V4 Pro was trained partly on Huawei Ascend 950PR chips. This is genuinely new. A year ago, the assumption in the Western AI world was that serious frontier-scale training required Nvidia hardware, full stop. DeepSeek has demonstrated that assumption was wrong, or at least no longer fully true. They still used Nvidia H100s and A100s for parts of the run — the exact split is murky and DeepSeek hasn't fully disclosed it — but Ascend handled significant portions, especially in the reinforcement learning phase.

What this means practically: Chinese AI labs now have a domestic hardware path that works. Not as efficient as Blackwell, but workable. The ASML export controls that were supposed to cap Chinese model development have instead forced the development of an alternative compute stack. That stack is maturing fast.

What this does not mean: DeepSeek has caught up to OpenAI or Anthropic in overall research capability. V4 Pro is excellent and it's the best open-weights release I've tested, but on the hardest benchmarks it's still slightly behind GPT-5.4 Extra High and Opus 4.6. The gap on the very top benchmarks is real. It's also narrower than it's been at any point in the last three years, and the gap is closing, not opening.

The geopolitical takeaway, if you want one, is that the compute-export-control strategy has accelerated Chinese AI independence rather than slowing it. That's a discussion for a different article, but you can't review V4 Pro honestly without acknowledging it.

## The Cost Math, One More Time

Let me close the loop on the pricing story because it's the thing I keep coming back to.

Rough API pricing for comparable tasks, based on my actual weekend usage:

- DeepSeek V4 Pro via direct API: pennies per task for most work. My full weekend — four non-trivial builds plus the 340K context test — cost roughly $1.80 total on the direct API.
- DeepSeek V4 Pro via Open Code Go: $10/month flat, with four parallel instances and generous limits. This is the one I'm actually using.
- Claude Opus 4.7 via Claude Code: roughly $60-80 for the same weekend workload, paid through API credits.
- GPT-5.5 Pro via Codex: roughly $180-220 for equivalent usage.

The order-of-magnitude gap is real. The 98%-cheaper-than-GPT-5.5-Pro framing that Decrypt used isn't marketing — it's what I measured. And for many practical workloads, the quality delta just doesn't justify the cost gap anymore.

This is the part I want every indie dev and small agency to internalize. You do not have to run everything on frontier models. You can route the top 20% of your work — the nuanced architectural thinking, the long-context analysis, the client-facing polish — to Opus or GPT-5.5, and run the other 80% on V4 Pro. Your bill drops by 70-80% and your output quality stays roughly the same because the frontier is doing the work where frontier quality actually shows up.

I've started doing exactly this. My workflow now has two tiers: Opus for thinking-heavy work, V4 Pro for executing-heavy work. My AI spend has dropped by almost two-thirds and I haven't noticed a difference in the quality of anything I've shipped.

## The Honest Verdict

If you're looking for a single takeaway from this DeepSeek V4 Pro review, here it is: this is the first open-weights model I'd confidently deploy in a small-business production workflow, with the caveats I've laid out above.

It's not the best model available. Opus 4.7 is still better. GPT-5.5 Pro is still better on the hardest tasks. If your budget allows for frontier models and your work demands frontier quality, keep using them.

But if your budget does not allow for frontier models, or if large chunks of your workload don't genuinely need frontier quality, V4 Pro is a step-change improvement over anything else in the open-weights tier. It's better than Kimi K2.6 on most of my tests. It's better than [Qwen 3.6 on agentic coding](/blog/qwen-3-6-plus-agentic-coding), meaningfully so on longer tasks. It's ahead of [Gemma 4 for serious work](/blog/gemma-4-free-local-ai-model-review), though Gemma is still my local-first pick for total offline use.

The uncomfortable truth for frontier labs is that "good enough, ten times cheaper" is a devastating competitive position, and DeepSeek V4 Pro is the first open-weights model that genuinely occupies it. The pricing pages at the American labs are going to have to move. I don't know how fast, but they're going to have to.

And here's the thing I keep circling back to from that Thursday night at 11:47 PM with four terminals running and a twenty-cent bill. The future I thought we were five years away from — capable open-source AI that you can run four instances of in parallel for the cost of a coffee — isn't five years away. It's a hosted subscription with a "$5 for your first month" button on the homepage.

If you've been waiting to take open-source AI seriously because it wasn't quite good enough yet, the wait is over. Go download it. Go run it. Route your throwaway work to it and keep your frontier budget for the work that actually needs it. You will be shocked how little you miss the expensive models for 80% of what you build.

That's the real headline. Everything else is commentary.

## Frequently Asked Questions

### Is DeepSeek V4 Pro actually open source?

DeepSeek V4 Pro is released under an open-weights license, meaning the model weights are downloadable and runnable locally, though the training data and full training code are not fully published. For most practical purposes — self-hosting, fine-tuning, local inference — it behaves as open source. The 1.6T Pro weights are impractical to run on consumer hardware, but the 284B V4 Flash variant is runnable via Ollama on serious workstations.

### How does DeepSeek V4 Pro compare to GPT-5.5 and Opus 4.7 for coding?

V4 Pro is slightly behind Opus 4.7 and GPT-5.5 Pro on the hardest coding benchmarks but beats Opus on Terminal Bench and is only marginally behind GPT-5.4 on SWE Pro. For one-shot coding tasks it's competitive; for complex iterative refactoring across large codebases, the frontier closed-source models are still meaningfully better. See the test walkthroughs above for specific comparisons.

### What's the real long-context performance of DeepSeek V4 Pro?

Despite the advertised one-million-token context, practical quality degrades noticeably past 180,000-200,000 tokens. I measured a reliable working ceiling of roughly 128K tokens in real codebase tests before confabulation starts. For long-context architectural analysis, Opus 4.7 or Gemini remain better choices.

### Is DeepSeek V4 Pro cheaper than Claude and GPT?

Yes, dramatically. API pricing runs roughly 7x cheaper than Opus 4.7 and about 40x cheaper than GPT-5.5 Pro for comparable workloads. The Open Code Go plan at $10/month with four parallel instances is the most cost-effective way to access it for most solo developers. My full weekend of testing cost under $2 in total spend.

### Does DeepSeek V4 Pro have censorship?

Yes. The hosted API enforces CCP-line content filtering on topics like Taiwan's political status, Tiananmen Square, and Xinjiang. For coding work this almost never comes up, but for any analytical work touching Chinese politics or human rights, route to a different model. Local inference via Ollama has weaker filtering because it bypasses the API layer.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
