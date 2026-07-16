**BRAND:** mejba.me
**TITLE:** GPT 5.5 Codex Hands-On: The Agentic Leap Tested
**META TITLE:** GPT 5.5 Codex Hands-On: Tested on Real Builds (2026)
**SLUG:** gpt-5-5-codex-hands-on-review
**PRIMARY KEYWORD:** GPT 5.5 Codex
**META DESCRIPTION:** I ran GPT 5.5 through Codex on an SVG unicorn, a retro macOS game, and a 3D dungeon. Here's what shipped, what broke, and what it means for AI agents.
**TAGS:** GPT 5.5, OpenAI Codex, AI Coding Agents, AI Model Reviews, Agentic AI

---

The OpenAI announcement dropped at 9:04 AM on April 23, 2026. I had just opened a terminal to push a Laravel migration. Twenty minutes later the migration was still sitting in my staging branch, untouched, because I was staring at the [GPT 5.5 introduction page](https://openai.com/index/introducing-gpt-5-5/) trying to decide whether this was the release that actually justified its own hype or another six-week incremental bump dressed up as a leap.

The number that stopped me was this one: 82.7% on Terminal-Bench 2.0. That's state-of-the-art on the benchmark that tests whether a model can actually plan, iterate, and coordinate tools in a shell — which is the thing agentic coding either succeeds or embarrassingly fails at. Opus 4.7 sits at 69.4% on the same benchmark per the [digitalapplied comparison](https://www.digitalapplied.com/blog/gpt-5-5-vs-claude-opus-4-7-frontier-comparison). That's a 13-point gap. Thirteen points is the distance between "promising" and "please use this in production."

But benchmarks lie. Not on purpose — they just measure the thing they measure, which is rarely the thing you care about. What I care about is whether GPT 5.5 inside Codex actually shortens my Wednesday. So I spent the next two days running it against three builds I'd have paid a senior engineer to handle: an absurdly detailed SVG, a native macOS retro arcade game with AI-generated sprites, and a first-person 3D dungeon arena rendered in a quarter viewport. Real work. Real runs. Real bills.

Stay with me through the dungeon test. That's where my "GPT 5.5 is just a faster 5.4" assumption collapsed in real time, and I had to rewrite the framing of this entire post.

## Why This Release Actually Matters (And Why Codex Changed)

The release cadence in this industry has gone completely untethered. GPT 5.4 shipped in February. Opus 4.7 landed in mid-April. GPT 5.5 arrived a week later. We are now getting a new frontier coding model roughly every six to eight weeks, and each one is supposed to be "the one that changes everything."

Most of the time, that's marketing. This time, the framing feels different — and not because OpenAI says so. It's because of three specific changes.

First, GPT 5.5 is [the first fully retrained base model since GPT-4.5](https://ofox.ai/blog/gpt-5-5-release-guide-2026/). Everything between 4.5 and 5.4 was iteration on the same underlying foundation. GPT 5.5 is a new foundation. That's not a minor distinction — it means the pretraining corpus, the architecture decisions, and the agent-oriented objectives were redesigned from the ground up with autonomous work as the target, not conversational response quality.

Second, the context window jumped to 1M tokens in the API. GPT 5.4's API topped out at 512K. The doubling isn't just a bigger buffer — it's a different category of work. A 1M context window means an agent can hold an entire medium-sized codebase, its test suite, and the relevant documentation in a single session without truncation games. On [OpenAI's MRCR v2 8-needle retrieval benchmark at 512K-1M range](https://www.digitalapplied.com/blog/gpt-5-5-vs-claude-opus-4-7-frontier-comparison), GPT 5.5 hits 74.0% while Opus 4.7 hits 32.2%. That's not a gap — that's two different capabilities.

Third, the Codex integration got a real upgrade. You can now pick between medium, high, and extra-high reasoning effort on a per-task basis. Medium is the default. High is for non-trivial refactors. Extra high is what you reach for when a task genuinely needs extended reasoning — large migrations, security audits, architecture decisions. Per [Artificial Analysis](https://artificialanalysis.ai/models/comparisons/gpt-5-2-vs-gpt-5-codex), GPT 5.5 (xhigh) currently leads their intelligence index at 60, with GPT 5.5 (high) at 59. The knob matters because you can tune the compute spend against the task difficulty in ways that were hands-off before.

Before I get to the tests — one thing you need to understand about the pricing and positioning, because it reframes everything that comes after.

## The Pricing Math And What It Says About The Strategy

GPT 5.5 ships at $5 per million input tokens and $30 per million output tokens. GPT 5.4 was $2.50 and $15 respectively. A straight doubling. If you're running a high-volume agentic stack, that doubling hits your cost sheet immediately.

Here's the pitch that makes the math work: GPT 5.5 uses significantly fewer tokens to complete the same Codex tasks. [Per OpenAI's own messaging](https://openai.com/index/introducing-gpt-5-5/), the per-token latency matches GPT 5.4 while the intelligence level jumps materially. In other words — the model is more efficient, so the raw price-per-task should come out roughly flat or better even at the doubled unit cost.

Compare that to Opus 4.7 at $5 per million input and $25 per million output. On paper, Opus 4.7 is 17% cheaper on output tokens. In practice, Opus 4.7 ships with a tokenizer change that inflates token usage by roughly 35% on some workloads per [the axios coverage](https://www.axios.com/2026/04/16/anthropic-claude-opus-model-mythos). So the "cheaper per token" claim starts evaporating the moment your tokenizer is burning more tokens per identical task.

This is the actual GPT 5.5 vs Opus 4.7 economic fight: whose tokens cost what they say they cost? And right now, nobody running real workloads has complete data. I've started logging every Codex run against its equivalent Claude Code run specifically because nobody I trust has published the real unit economics yet. (If you want the head-to-head I ran on four production builds, I wrote that up separately in [GPT 5.5 vs Opus 4.7 tested on real coding builds](https://www.mejba.me/gpt-5-5-vs-opus-4-7-comparison).)

Now — the tests. Starting with the easiest one, because even the easy one surprised me.

## Test One: The Absurd SVG Unicorn

This is the test Simon Willison popularized — ask a model to produce an SVG of something specific, no external tools, just raw text generation of vector paths. It's a brutal test because SVG requires the model to mentally render coordinates and curves before emitting them. There's no DOM to reference, no image model to offload to. Just geometry, in head, straight to output.

I gave GPT 5.5 a single prompt in Codex: "Produce a detailed SVG of a unicorn rearing on its hind legs, with flowing mane and visible musculature. Pure SVG, no external references."

Inference effort: medium.

The output took 38 seconds. It was 1,847 lines of SVG. When I dropped it into a browser, what rendered was... actually a unicorn. A rearing one. With a flowing mane. The musculature wasn't anatomically correct — the front leg bend was slightly off and the rear haunch looked more goat than horse — but the composition read correctly at a glance. I could identify the subject without being told what it was.

I ran the same prompt on GPT 5.4 for comparison. It took 52 seconds, produced 2,340 lines, and the output looked like a unicorn that had been drawn by someone who once saw a horse in a book. The mane ended at weird angles. The horn was disconnected from the skull at certain zoom levels.

Same prompt, worse output, more tokens, slower runtime. That's the efficiency pitch making itself on the simplest possible test.

But I didn't believe it yet. SVG generation is the kind of task where the training corpus matters enormously, and if GPT 5.5 has seen more SVG examples in pretraining, this result tells me about the data — not about reasoning. So I moved to the test that actually stresses autonomous decomposition.

## Test Two: Native macOS Retro Arcade Game With AI Sprites

The prompt: "Build a native macOS app — Swift and SpriteKit — implementing a retro arcade-style library game. The player controls a librarian restocking bookshelves while avoiding falling books. Use GPT Image 2.0 to generate all sprite assets at runtime. Package it as a runnable Xcode project."

This is a real stress test. It requires Codex to:

1. Scaffold a native macOS Xcode project correctly
2. Design a sprite-based game loop with collision detection
3. Call GPT Image 2.0 via the API for sprite generation
4. Handle async image loading into SpriteKit textures
5. Package the whole thing so it builds on first run

I set inference effort to high, because medium on this task would be reckless optimism.

Codex ran autonomously for about 11 minutes. The first thing I noticed — and this was genuinely new behavior — is that Codex ran its own test cycles. It built the project, tried to launch the game, hit a SpriteKit initialization error, diagnosed the error by inspecting its own build output, modified the initialization code, rebuilt, and re-ran. It did this three times without intervention. On GPT 5.4, this same task would have required me to play error-message ping-pong at least twice. On GPT 5.5, I watched the terminal scroll and drank coffee.

The final build ran. The librarian sprite moved with arrow keys. Books fell from the top of the screen. Collision detection worked. The game loop was roughly 30 frames per second — not because that was the target, but because sprite loading from GPT Image 2.0 was rate-limiting the whole pipeline.

And that's where the first real limit showed up. Every sprite generation call hit the image API, which took between 8 and 14 seconds per sprite. By the time the game had its full asset set loaded, I'd spent more time waiting for textures than waiting for code. The generated sprites themselves looked dark and a little chaotic — the librarian's face rendered differently on each load, because sprite generation was happening at runtime without a seed. It worked. It wasn't shippable. Somewhere between a tech demo and a prototype.

What's interesting here isn't that the game was rough. It's that Codex owned the entire loop — scaffolding, implementation, API integration, autonomous debug cycles — without needing me to break it into steps. That's the thing the release notes mean when they say "agentic coding." It's not that the model writes better code. It's that the model runs its own work.

Pro tip: if you're testing any model's agentic capacity, pick a task that requires tool-use autonomy in an environment the model can actually observe. A pure code-generation task doesn't measure agent behavior — it measures translation. Give it build errors it has to read and resolve, and you'll see whether the autonomy is real or performative.

Now — the test where my assumptions got publicly humiliated.

## Test Three: First-Person 3D Dungeon Arena

The prompt: "Build a first-person 3D dungeon arena prototype. Three.js, TypeScript. Render the 3D scene in the top-left quarter of the viewport only. The remaining three quadrants show a HUD: minimap, health, inventory. Combat against basic enemies. Ship it as a runnable web prototype."

The quarter-viewport rendering is deliberate. Most 3D game tutorials assume full-viewport rendering. Constraining the render to a quadrant forces the model to understand the Three.js camera, viewport, and scissor APIs — it can't copy-paste a tutorial scaffold and move on.

Inference effort: extra high. I wanted to see the ceiling.

Codex ran for 23 minutes. During that window it:

- Scaffolded a Vite + TypeScript + Three.js project correctly
- Implemented pointer-lock controls for first-person movement
- Set up the scissor/viewport logic for quarter-viewport rendering
- Built enemy meshes and a basic pathfinding loop
- Wired a minimap that canvas-renders the player's position
- Implemented a combat system with raycasting for hit detection
- Fixed three separate TypeScript errors autonomously

When it finished, I opened localhost. The 3D scene rendered in the top-left quadrant. I could move with WASD. The minimap worked. Enemies existed and reacted when I got close. The combat raycasting registered hits. The HUD was... rough. The health bar was a grey rectangle. The inventory panel was empty placeholder text. The enemy meshes were cubes with face textures that didn't quite fit.

It worked. It was playable in the literal sense. It was not shippable in any meaningful sense. The gap between "playable prototype" and "actual game" is exactly the gap that humans spend weeks closing.

Here's the part that changed my framing. Partway through the run, Codex decided on its own to add a debug overlay showing the scissor rectangles. I didn't ask for it. It added the overlay, used it to verify its own rendering was correct, and then left it in the final output. That's not code generation. That's a tool use decision that suggests the model has an internal model of its own workflow — it inserted a diagnostic because it needed one to verify its own correctness.

Whether you think that's meaningful or marketing language depends on how much time you've spent with agent stacks. For me, it's the tell. The models that feel genuinely agentic aren't the ones that write more code. They're the ones that insert their own diagnostic steps into the loop without being told.

If you'd rather have someone build this kind of Codex-powered autonomous workflow into your team's dev pipeline from scratch, I take on engagements exactly like this — you can see what I've shipped at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## What GPT 5.5 Actually Gets Right

Three things, from two days of real work.

**The autonomous debug cycle is real.** This is the single biggest shift. GPT 5.4 in Codex would generate code, fail, and hand me the error. GPT 5.5 in Codex generates code, fails, reads the failure, fixes it, and continues. For iteration-heavy work — anything involving builds, tests, or runtime errors — this compounds savagely. A task that used to be "five rounds of prompt/error/re-prompt" becomes one uninterrupted run.

**Token efficiency is not a marketing claim.** I tracked output token counts across both models on four equivalent tasks. GPT 5.5 averaged 34% fewer output tokens for equivalent functional output. The code wasn't shorter — it was less explanatory. Fewer inline comments. Tighter whitespace. Less "here's what I'm about to do" preamble. Whether that's a stylistic win or loss depends on whether you're reading the code or just shipping it.

**The 1M context window changes what you can ask for.** I dropped the full source of a Laravel application — 240 files, roughly 680K tokens — into Codex and asked it to audit the authentication flow. It read the whole thing and produced an audit that referenced specific method signatures across 14 different files. Opus 4.7 on the same task hit its context ceiling and produced a vaguer audit against a subset. This isn't about raw ability — it's about what tasks are addressable without pre-processing.

## What GPT 5.5 Still Gets Wrong

Three honest limits.

**Complex creative tasks still need supervision.** The dungeon prototype worked in the sense that it ran. It didn't work in the sense that a person could play it. The gap between "technically executing" and "shippable" is still fully human-sized on anything that requires taste or game-feel judgment.

**Extra-high inference is expensive and slow.** The dungeon task at xhigh burned through serious compute and took 23 minutes. If you're building a tight feedback loop, xhigh is not your daily driver. Medium is the default for a reason. I'd reach for xhigh on migrations, security audits, and architecture decisions — not on feature work.

**Image generation integration has latency problems.** The macOS game test was bottlenecked by GPT Image 2.0's 8-14 second per-sprite generation time. If your workflow relies on runtime image generation, you're at the mercy of the image API, not the language model. That's not a GPT 5.5 problem — but it is a Codex-workflow problem you'll hit immediately.

## What This Means For Anthropic, Claude, And The Broader Game

I want to be careful here because speculation about compute allocation at frontier labs is mostly nonsense, and the charitable interpretation is usually correct. But the pattern is hard to miss.

Opus 4.7 shipped with [regressions that a vocal subset of power users reported on immediately](https://www.axios.com/2026/04/16/anthropic-claude-opus-model-mythos) — a tokenizer change that inflates usage, reduced default reasoning depth, and shifts in instruction-following behavior. Mythos, Anthropic's more capable unreleased model, is gated behind restricted access — banking and government pilots. Anthropic has publicly denied that compute reallocation is driving these decisions. I have no reason to doubt that.

But here's what's observable. GPT 5.5 shipped widely to paying users with a 1M context window and an aggressive NVIDIA-backed inference stack running on [GB200 NVL72 systems capable of 50x higher token throughput per megawatt than prior generations](https://blogs.nvidia.com/blog/openai-codex-gpt-5-5-ai-agents/). That is a serious compute flex. If you're in a capacity race, and your competitor just shipped a model that's broadly available, cheaper per output token after tokenizer effects, and faster per equivalent task — the pressure is real whether or not anyone wants to admit it publicly.

For me, as a builder, the practical takeaway is this: bet on the model that's actually shipping to production users today, not the model with the strongest hypothetical capability. That's GPT 5.5 right now for most coding-agent work. Opus 4.7 is still my pick for long-form writing, subtle code review, and architecture conversations. Mythos is irrelevant to my workflow because I cannot use it. The model I can't run cannot help me ship.

## Is GPT 5.5 Codex Worth The Subscription?

Depends on your workload. If you run Codex daily, the token efficiency plus autonomous debug loops justifies the price doubling within the first week. If you're a casual user, the jump from 5.4 to 5.5 isn't going to feel dramatic on single-shot prompts — it shines on multi-step autonomous work. If you're running an agent stack at scale, the 1M context and the xhigh reasoning setting unlock work categories that were previously impossible, and those categories tend to be the high-value ones.

The subscription question I'd actually ask: what's the marginal cost of the task you're asking the model to do right now? If the answer is "senior engineer time at $150/hour," the subscription is trivial. If the answer is "I'm learning on the free tier," the calculus is different. For me, the Codex subscription was paid back in the first week on builds I'd have otherwise outsourced.

## Frequently Asked Questions

### When was GPT 5.5 released and who can use it?

GPT 5.5 was released on April 23, 2026, to paying ChatGPT users on Plus, Pro, Business, and Enterprise tiers, with API availability at $5 per million input tokens and $30 per million output tokens. It ships integrated with Codex, OpenAI's agentic coding environment. See the release overview section above for the full context window and pricing breakdown.

### What is the difference between GPT 5.5 medium, high, and extra high inference?

Medium is the default Codex setting, suitable for most tasks. High activates deeper reasoning chains for complex refactors and multi-file work. Extra-high (xhigh) produces the highest-quality output on problems that genuinely require extended reasoning — large migrations, security analysis, architecture decisions — at significantly higher latency and cost. Per Artificial Analysis, GPT 5.5 xhigh leads their intelligence index at 60 vs 59 for high. See the dungeon arena test above for how xhigh performs in practice.

### How does GPT 5.5 compare to Claude Opus 4.7 for coding?

GPT 5.5 leads on agentic coding benchmarks (82.7% on Terminal-Bench 2.0 vs 69.4% for Opus 4.7) and long-context retrieval. Opus 4.7 leads on SWE-Bench Pro (64.3% vs 58.6%) and MCP-Atlas. The practical split: GPT 5.5 for autonomous workflow execution, Opus 4.7 for careful codebase refactoring and code review. I ran a full head-to-head on four real builds in [GPT 5.5 vs Opus 4.7 comparison](https://www.mejba.me/gpt-5-5-vs-opus-4-7-comparison).

### Is GPT 5.5 Codex worth the subscription cost?

For daily Codex users, yes — the token efficiency and autonomous debug loops pay back the subscription within the first week on non-trivial work. For casual use, the upgrade is less dramatic. The model shines on multi-step agentic tasks where it can run its own build/test/fix cycles without your intervention. See the worth-it section above for the full cost-benefit analysis.

### Can GPT 5.5 actually build games or just prototypes?

Based on hands-on testing, GPT 5.5 builds playable prototypes that run without errors, but the gap between "technically executing" and "shippable" is still human-sized on creative tasks requiring taste or game-feel judgment. The dungeon arena test produced a working 3D prototype in 23 minutes at xhigh reasoning — but the HUD, textures, and overall polish required the kind of iteration that only a human game designer can drive.

## The One Thing You Can Do Today

Forget the benchmarks for a second. Here's the test I'd actually run if you're trying to decide whether GPT 5.5 Codex belongs in your stack.

Pick one task you've been dreading. Something multi-step. Something that normally takes you a focused afternoon. A migration, a refactor, a feature that touches three modules. Open Codex. Set reasoning to high. Write the task as a single prompt. Walk away for fifteen minutes.

When you come back, you'll know exactly what I know: whether the autonomous loop is real for your workflow or whether it's hype. That's not a benchmark question. That's a Tuesday-afternoon question. And Tuesday afternoons are where careers get built.

The rearing unicorn SVG I generated on day one is still sitting in a folder on my laptop. I keep it there as a reminder. Six weeks ago, that level of one-shot output would have been a viral tweet. Today, it's the floor. The ceiling is somewhere I haven't hit yet — and the only way to find out where it is, is to keep pushing harder prompts into the loop until something breaks.

So go break something. And then tell me what you found.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
