**BRAND:** mejba.me
**TITLE:** I Tested MiniMax M2.7 — The Self-Evolving Agent Model
**SLUG:** minimax-m2-7-agentic-ai-review
**PRIMARY KEYWORD:** MiniMax M2.7
**SECONDARY KEYWORDS:** agentic AI model, self-evolving AI, MiniMax M2.7 review
**META DESCRIPTION:** I tested MiniMax M2.7 across 7 real tasks. It self-improved through 100+ rounds, hits Opus-level benchmarks, and costs $0.30/M input tokens. Here's what happened.
**TAGS:** AI Models, Agentic AI, MiniMax M2.7, AI Model Review, Hands-On Review
**CONTENT TYPE:** Practitioner Review
**CONTENT CLUSTER:** AI Model Reviews
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly where MiniMax M2.7 excels, where it falls short, and whether it belongs in their AI toolkit — with specific use cases and cost comparisons to make the decision.

---

The slot machine was the thing that broke my brain.

I'd been testing MiniMax M2.7 for about three hours at that point — running it through my standard gauntlet of frontend challenges, game builds, and creative coding tasks. Most of it was good. Some of it was very good. But the slot machine crossed a line I wasn't expecting from a model at this price point. Full state management. Smooth reel animations with independent timing. Randomness logic that actually felt random. Visual feedback on wins with particle effects and screen shake. The kind of polished interactivity that I'd expect from a senior frontend developer — not a model that costs fifty times less than Opus to run.

I sat there clicking the spin button for a solid two minutes before I remembered I was supposed to be reviewing the thing, not playing it.

MiniMax M2.7 dropped on March 18, 2026, and the headline feature isn't the benchmarks or the pricing — though both are wild. The headline is that this model improved itself. Over 100 autonomous rounds of analyzing its own failures, modifying its own code, running evaluations, and deciding whether to keep or revert changes. No human touching the keyboard. The result was a 30% performance gain that the model essentially gave itself.

That's the claim. I wanted to see what that self-evolution actually produced in practice — so I spent the better part of four days throwing everything I could at it. Here's exactly what I found, what impressed me, what disappointed me, and whether this model deserves a spot in your workflow alongside the models you already trust.

<!-- IMAGE: Terminal screenshot showing MiniMax M2.7 model info on OpenRouter with pricing details. Alt text: "MiniMax M2.7 model page on OpenRouter showing $0.30/M input token pricing". Caption: "M2.7's OpenRouter listing — that pricing is not a typo." -->

## What MiniMax Actually Claims — And Why the Self-Evolution Part Matters

Before I get into my test results, you need to understand what makes M2.7 different from every other model drop this month. Because there have been a lot of model drops this month.

MiniMax is a Chinese AI company that's been building steadily since their M2 series launched. The M2.7 specifically was trained using what they call a "recursive self-improvement" pipeline. Here's how that works in plain terms: the model ran its own reinforcement learning workflow. It would attempt a task, analyze why it failed, modify its approach, re-run the evaluation, compare the results, and either keep the change or revert it. Then it did that again. And again. Over 100 times — handling between 30 and 50 percent of its own development workflow without a human engineer intervening.

According to [VentureBeat's coverage](https://venturebeat.com/technology/new-minimax-m2-7-proprietary-ai-model-is-self-evolving-and-can-perform-30-50), this isn't just automation of simple tasks. The model was optimizing its own programming performance by analyzing failure trajectories and planning code modifications across those iterative loops. [MiniMax's own technical blog](https://www.minimax.io/news/minimax-m27-en) describes the vision as AI self-evolution that will "gradually transition towards full autonomy, coordinating data construction, model training, inference architecture, evaluation, and other stages without human involvement."

That's a bold claim. But here's why I'm not dismissing it: the benchmark results actually back it up.

| Benchmark | MiniMax M2.7 | Context |
|---|---|---|
| SWE-Bench Pro | 56.22% | Approaches Opus-level; surpassed Gemini 3.1 Pro |
| VIBE-Pro | 55.6% | End-to-end project delivery capability |
| TerminalBench 2 | 57.0% | Deep system-level understanding |
| MLE Bench Lite | 66.6% medal rate | Ties with Gemini 3.1 across 22 ML competitions |
| GDPval-AA | 1495 Elo | Highest among open-source-accessible models |
| Hallucination Rate | 34% | Lower than Sonnet 4.6 (46%) and Gemini 3.1 Pro (50%) |

That hallucination rate caught my eye. 34% versus Sonnet 4.6's 46%? I was skeptical. But across my testing, I did notice M2.7 was less likely to fabricate function names or invent API parameters that don't exist. It's not hallucination-free — no model is — but the reduction is real and noticeable during extended coding sessions.

The model supports 50+ skills and 100+ features with what MiniMax describes as "stable instruction following and reliable tool usage." It ships with a 24,000-token context window — smaller than what I'm used to with Claude's 200K or Gemini's million-token contexts, but more than sufficient for the kind of focused task execution M2.7 is designed for.

And then there's the pricing. This is where my ears perked up. I'll get into the full cost breakdown later, but the short version: $0.30 per million input tokens and $1.20 per million output tokens. To put that in perspective, [Opus 4.6 runs at roughly $6 per million input tokens](https://artificialanalysis.ai/models/minimax-m2-7). M2.7 is delivering benchmark scores that approach Opus territory at a fraction — sometimes 1/50th — of the cost.

The question isn't whether the benchmarks are good. They clearly are. The question is whether those numbers translate into real outputs that I'd actually want to use. So I ran seven tests. Let me walk you through every single one.

## Test 1: The macOS Browser Desktop — Where M2.7 Flexed Hard

My first test is always ambitious. I ask the model to build a macOS-styled browser operating system — a full desktop environment running in the browser with dynamic backgrounds, working applications, a dock, window management, the works. This test separates the serious models from the pretenders because it requires simultaneous competence in layout architecture, state management, animation, and creative design.

M2.7 delivered something I'd rate 9 out of 10.

The desktop background had a dynamic gradient that shifted subtly over time — not the cheap CSS animation you get from most models, but a smooth, GPU-accelerated transition that looked genuinely polished. The dock at the bottom was functional with hover magnification effects. Window management worked: you could drag windows, minimize them to the dock, and resize them with proper snapping behavior.

The individual applications were what surprised me most. A calculator that actually worked with keyboard input. A notes app with persistent state during the session. A settings panel that let you change the wallpaper and accent colors — and those changes propagated across the entire UI immediately. The attention to detail was the kind of thing that makes you forget you're looking at generated code.

Where it fell short: the file manager was mostly cosmetic. You could see folder icons and navigate a directory tree, but there was no actual file creation or persistence. And the "terminal" app was a fake — it accepted input but didn't process commands. Purely decorative.

Still. For a single-prompt generation at this price point, 9/10 is fair. I've seen Opus produce similar quality, but I've also seen Opus stumble on the state management for something this complex. M2.7 handled it cleanly.

## Test 2: Landing Pages With Shader Rendering — The Frontend Muscle

My second test pushes frontend capability specifically. I asked M2.7 to generate a dynamic landing page for a fictional AI product — hero section with animated shader background, feature cards with micro-interactions, a pricing table with annual/monthly toggle, and a testimonials section with a carousel.

The shader background was the standout. M2.7 produced a WebGL-powered gradient mesh that responded to mouse movement — subtle enough to feel premium rather than gimmicky. The performance was solid too. No frame drops on my M3 MacBook Pro even with the animations running.

The feature cards had hover states with smooth elevation transitions and icon color shifts. The pricing toggle worked correctly with cross-fade animations between monthly and annual rates. The testimonials carousel auto-rotated and paused on hover.

What really got my attention was the typography choices. M2.7 selected font pairings that actually looked intentional — a geometric sans-serif for headings paired with a humanist sans for body copy. Most models just slap Inter on everything and call it a day. M2.7 made a design decision, and it was a good one.

The code structure was clean too. Proper component separation, semantic HTML, CSS custom properties for the color system, and no inline styles dumped everywhere. If a junior developer submitted this as a pull request, I'd approve it with minor comments.

I ran a Lighthouse audit on the output: 94 performance, 100 accessibility, 92 best practices. Those numbers are real. That's better than what I get from some hand-built production sites.

## Test 3: The Minecraft Clone — Infinite Terrain, Missing Blocks

This is where things got interesting — and where M2.7 showed its first real limitation.

I asked for a Minecraft-style voxel world with infinite terrain generation, textures, an inventory bar, and basic block interaction. The terrain generation was impressive: Perlin noise-based heightmaps that created convincing rolling hills, valleys, and the occasional cliff face. Different biomes blended together smoothly. Grass, dirt, stone, and sand textures were applied correctly based on altitude and biome type.

The inventory bar at the bottom of the screen looked right. Selectable slots with highlighted borders. Different block types represented with proper icons.

But block breaking — the core mechanic of Minecraft — was absent. You could look at blocks, you could see the crosshair, you could select different block types in the inventory. You just couldn't interact with the world. No breaking. No placing. The model built a beautiful voxel landscape viewer, not a game.

I tried prompting M2.7 to add the interaction layer in a follow-up. It added a raycasting system for block selection (correct approach) but the actual removal and placement logic was buggy. Blocks would disappear from the wrong position, or placement would offset by one unit in the Y axis. After three iterations, it got block breaking working but placement was still inconsistent.

This is the kind of task where [Opus 4.6's persistence](https://www.mejba.me/blog/opus-4-6-hands-on-review) — trying three or four independent solutions before giving up — would have eventually cracked it. M2.7 kept circling the same approach with minor variations rather than fundamentally rethinking the raycast-to-voxel-coordinate mapping.

Terrain generation: 9/10. Block interaction: 4/10. If you need a voxel renderer, this is great. If you need a playable Minecraft clone, you'll need to iterate more than I expected.

## Test 4: The Casino Slot Machine — Where M2.7 Beat Opus

This was the test that stopped me in my tracks. And I need to be specific about why, because "it made a good slot machine" doesn't capture what actually happened.

I gave M2.7 a single prompt: build an interactive casino slot machine with animations, randomness logic, visual feedback, and a credit system. No additional context. No reference images. One shot.

The reels spun independently with realistic deceleration curves — each reel stopping slightly after the one before it, creating that satisfying cascade effect you get in real slot machines. The symbols were distinct and well-designed (rendered as SVG, not emoji). The randomness wasn't just `Math.random()` — M2.7 implemented a weighted probability system where certain symbol combinations were rarer than others.

The win detection was the part that impressed me most. It checked horizontal lines, diagonal lines, and even had a special animation for three-of-a-kind versus two-pair combinations. Win amounts were calculated correctly based on the rarity of the combination. Credits updated with a smooth counting animation rather than an instant number swap.

And the visual feedback. Screen shake on big wins. Particle confetti for jackpots. A subtle glow effect on winning symbols. Sound-ready event hooks (no actual audio, but the code had properly placed callbacks where sound effects would slot in).

I ran the same prompt through Opus 4.6 for comparison. Opus produced a functional slot machine — correct logic, clean code, working state management. But the animations were simpler. No independent reel timing. No weighted probabilities. No particle effects. The Opus version was a solid B+. M2.7's version was an A.

For a model at 1/50th the cost to produce objectively better output on a creative-interactive task? That's not an incremental improvement. That's a different conversation entirely.

## Test 5: The 360-Degree Product Viewer — Best-in-Class Output

I asked M2.7 to build a 360-degree product viewer for a pair of headphones — the kind of interactive widget you see on premium e-commerce sites where you can rotate the product, zoom in, and click on features for annotation popups.

The result was one of the best single-prompt generations I've received from any model this year.

Smooth rotation on drag with momentum and inertia — release the mouse and the product keeps spinning, gradually slowing to a stop. Pinch-to-zoom on trackpad with proper bounds so you couldn't zoom in infinitely or zoom out to a speck. Feature annotation dots positioned at key points on the product (ear cups, headband adjustment, controls panel) that expanded into info cards on click.

The info cards had clean typography, proper z-index management so they never clipped behind the product, and a nice fade-in animation. Close buttons worked. Clicking a new annotation auto-closed the previous one.

The code used CSS transforms for the rotation — no heavy 3D library required. This means it would run smoothly on mobile without any optimization work. I tested it on my phone through a quick local server, and the touch interactions felt native.

If you're building an e-commerce site and you need a product showcase component, the output from this single prompt would save you a full day of development. Maybe two.

## Test 6: The Animated Butterfly and the Gold Miner Game

Two smaller tests that reveal different aspects of M2.7's capability.

The animated butterfly prompt — my standard SVG generation test — produced an 8/10 result. Layered wing geometry with gradient fills, CSS keyframe animation with natural easing, and a convincing flight pattern. Compared to [what I got from GLM5 on the same test](https://www.mejba.me/blog/glm5-pony-alpha-tested), M2.7's butterfly was slightly less refined in the gradient transitions but had better animation timing. The wings moved with a subtle asymmetry that made the flight look organic rather than mechanical.

The casual cartoon Gold Miner game was a bigger surprise. I expected a bare-bones claw-drops-down mechanic. What I got was a full game with selectable modes: story, arcade, versus, and co-op (the latter two being split-screen on a single browser window). An audio settings menu with sliders for music, SFX, and ambient volume. A shop system where you could spend earned gold on upgrades — stronger claw, faster retraction, magnet attachment. And an upgrade tree that persisted between rounds.

The game logic was solid. The claw swung with proper pendulum physics. Different objects (gold nuggets, rocks, diamonds, dynamite) had different weights that affected retraction speed. The scoring system was balanced enough that early rounds felt achievable while later rounds required strategic upgrades.

Was it ready for the App Store? No. The collision detection had edge cases where the claw would clip through objects at certain angles. The versus mode had a timing synchronization issue where Player 2's claw would occasionally get a slight speed advantage. But as a prototype generated from a single prompt? The scope and completeness were remarkable.

## The Cost Math That Changes Everything

Here's where I need to talk numbers, because the benchmarks and demo quality only matter if you can afford to use the model in production.

MiniMax M2.7 pricing on [OpenRouter](https://openrouter.ai/minimax/minimax-m2.7):

| Metric | MiniMax M2.7 | Opus 4.6 | Ratio |
|---|---|---|---|
| Input tokens (per 1M) | $0.30 | ~$6.00 | **20x cheaper** |
| Output tokens (per 1M) | $1.20 | ~$12.00 | **10x cheaper** |
| Context window | 24,000 tokens | 200,000 tokens | Opus: 8x larger |

There's also a "fast mode" that doubles the cost for lower latency — $0.60 input and $2.40 output. Even at fast mode pricing, you're still running at a fraction of what Opus or GPT-5.3-Codex would cost.

To put this in real-world terms: a typical coding session where I send 50,000 input tokens and receive 30,000 output tokens would cost me roughly $0.051 with M2.7. The same session with Opus 4.6 would run about $0.66. Over a month of heavy daily usage, that's the difference between a $15 bill and a $200 bill.

The 24,000-token context window is the clear trade-off. If you're working with massive codebases or feeding in long documents for analysis, you'll hit that ceiling fast. For focused, single-task executions — generate this component, build this game, create this landing page — 24K is plenty. But for the kind of extended agent workflows where I need the model to hold context across dozens of files and hundreds of function signatures, I'd still reach for Opus or [Sonnet 4.6 with its million-token beta window](https://www.mejba.me/blog/claude-sonnet-4-6-tested).

The model is accessible through multiple channels. [OpenRouter's API](https://openrouter.ai/minimax/minimax-m2.7) is the most straightforward for developers. [Kilo Code](https://blog.kilo.ai/p/minimax-m27) — an open-source CLI tool — offers integration with free credits included, which is a great way to test without committing any money. MiniMax also offers their own chatbot interface for free access, and there are pay-as-you-go token plans if you want to go through their platform directly. The MiniMax team has been offering a 12% discount on token plans for new users, which makes the already-cheap pricing even more accessible.

If you'd rather have someone build production-grade AI integrations for you — agent systems, API pipelines, or multi-model architectures — I take on those kinds of projects. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Where M2.7 Falls Short — The Honest Assessment

I've been enthusiastic so far. Time to pump the brakes.

**Context window is a real limitation.** 24,000 tokens sounds like a lot until you're debugging a React component that imports from fifteen other files. I ran into the wall during a multi-file refactoring task where M2.7 simply couldn't hold enough context to understand the full dependency chain. Opus handles this without breaking a sweat. M2.7 needs you to be more surgical about what you feed it.

**Iterative debugging hits a ceiling.** The Minecraft test exposed this. When M2.7's first approach to a problem doesn't work, its second and third attempts tend to be minor variations of the same strategy. Opus and [GPT-5.3-Codex](https://www.mejba.me/blog/gpt-5-3-codex-first-look) will try fundamentally different approaches. M2.7 tends to tunnel-vision on its initial hypothesis. For straightforward bugs, this is fine — the first approach is usually close. For complex architectural issues, you'll spend more rounds steering the model toward alternative solutions.

**The self-evolution is impressive but opaque.** MiniMax claims 100+ rounds of autonomous improvement with a 30% performance gain. I believe the results — the benchmark numbers and my own testing support it. But the process itself is a black box. We don't know which specific capabilities improved, which trade-offs were made during self-optimization, or whether the model sacrificed performance in areas that weren't measured by the internal evaluation sets. The self-evolution story is compelling, but it requires a degree of trust in MiniMax's evaluation methodology.

**No vision or multimodal capabilities.** This is a text-in, text-out model. You can't feed it screenshots of a design and ask it to replicate the layout. You can't show it an error message and ask it to debug from the image. For developers who've gotten used to multimodal workflows with Claude or GPT, this is a step backward in flexibility.

**Chinese company, geopolitical considerations.** I'm going to be direct about this because I think it matters for certain use cases. MiniMax is based in China. For personal projects, open-source work, and general development, this is irrelevant — the code it generates runs locally and the API calls contain your prompts, same as any other model provider. But for enterprise deployments involving sensitive intellectual property or government-adjacent work, some organizations will have compliance requirements that factor in the provider's jurisdiction. Know your constraints.

## The Multi-Agent Architecture — M2.7's Hidden Strength

Here's something that didn't come through in any of my individual tests but became obvious when I zoomed out and looked at the pattern.

M2.7 was specifically trained for multi-agent orchestration. That means it's not just good at executing tasks — it's good at *planning* tasks, breaking complex workflows into steps, and coordinating between different execution stages. MiniMax calls these "Agent Teams" — clusters of AI agents that collaborate with distinct roles.

In practice, what this means for developers using M2.7 through tools like [Kilo Code](https://blog.kilo.ai/p/minimax-m27) or OpenRouter is that the model excels at structured, multi-step workflows. Research → analysis → generation → review. It naturally decomposes problems into stages and maintains consistency across steps.

I tested this by giving M2.7 a complex prompt: "Research the top 5 project management tools, create a comparison matrix, generate a recommendation report, and build a slide deck summarizing the findings." The model didn't just dump all of this in one response. It broke the task into clear phases, referenced its own earlier outputs when building subsequent stages, and maintained a consistent analytical framework throughout.

The research quality was reasonable — not as deep or current as what you'd get from a model with web access, but the structural thinking was strong. The comparison matrix was well-organized with consistent criteria. The report cited specific findings from the matrix. The slide deck (rendered as HTML/CSS) pulled key visuals and data points from the report.

MiniMax participated in 22 ML competitions through MLE Bench Lite and achieved a 66.6% medal rate — tying with Gemini 3.1. That's not a coding benchmark. That's a measure of end-to-end problem solving: understanding the task, designing an approach, implementing it, and iterating until the results are competitive. The fact that M2.7 matches Gemini on this metric tells me the multi-agent training is doing real work.

## Who Should Actually Use This Model

After four days of testing, I've landed on a clear mental model for where M2.7 fits.

**Use M2.7 when:**
- You need high-quality frontend generation and the task fits within 24K context
- You're building prototypes, demos, or MVPs where speed and cost matter more than architectural perfection
- You want creative-interactive outputs (games, visualizations, product viewers) — this is where M2.7 genuinely surprised me
- You're running high-volume batch operations where per-token cost directly impacts your budget
- You need multi-step task planning and workflow decomposition
- You're evaluating models for agentic applications and want Opus-tier reasoning at a radically different price point

**Stick with Opus/Sonnet when:**
- You need large context windows (24K vs 200K is a real gap for complex codebases)
- You're doing iterative debugging on architecturally complex problems where the model needs to try fundamentally different approaches
- You need multimodal input (screenshots, images, diagrams)
- You require the deepest instruction following across 60+ exchange conversations
- Enterprise compliance requires a US-based model provider

**The sweet spot** is using M2.7 alongside your primary model, not instead of it. I've started routing my quick generation tasks — landing pages, UI components, creative demos, game prototypes — through M2.7 and saving Opus for the complex debugging, long-context architecture work, and multi-file refactoring sessions. The cost savings are significant enough that this hybrid approach pays for itself within a week.

## What the Self-Evolution Means for Where This Is Heading

I want to end on the thing that's actually been keeping me up at night since I started testing M2.7. Not the benchmarks. Not the pricing. The self-improvement loop.

A model that ran 100+ rounds of autonomous optimization and came out 30% better is not just a product update. It's a proof of concept for a fundamentally different development paradigm. Traditional AI development goes: humans collect data, humans design training runs, humans evaluate results, humans decide what to change. M2.7's pipeline replaced the human at 30-50% of those stages — and the results were competitive with models built entirely by human-led teams.

According to [MiniMax's technical blog](https://www.minimax.io/news/minimax-m27-en), their vision is to "gradually transition towards full autonomy" in the model development pipeline. What happens when the next version handles 70%? 90%? When the iteration count goes from 100 rounds to 10,000?

I've been building [self-improving AI systems](https://www.mejba.me/blog/self-improving-claude-code-systems) for a while now, and I can tell you from experience — the first time you see a system genuinely improve itself without your input, it changes how you think about what AI development means. M2.7 is the first commercially available model where the model itself was a meaningful participant in its own creation.

That's not a gimmick. That's a trajectory.

Right now, today, MiniMax M2.7 is an extremely cost-effective model that punches well above its weight class on creative coding, frontend generation, and multi-step task execution. It has clear limitations — the context window, the iterative debugging ceiling, the lack of multimodal input. I wouldn't replace my Opus workflow with it.

But I'm adding it to my toolkit. The slot machine test, the 360-degree product viewer, the gold miner game — these weren't outputs from a budget model trying to keep up. These were outputs from a model that, in specific domains, is already leading.

The question that keeps rattling around in my head: if a self-evolving model at $0.30 per million input tokens is producing this quality today, what does version M2.8 look like? And who builds it — the MiniMax team, or M2.7 itself?

## Frequently Asked Questions

### Is MiniMax M2.7 free to use?

Yes, you can access M2.7 for free through the MiniMax Agent web chatbot, OpenRouter's free tier, and Kilo Code CLI with included credits. Paid API access starts at $0.30 per million input tokens through OpenRouter or MiniMax's own platform.

### How does MiniMax M2.7 compare to Claude Opus 4.6?

M2.7 approaches Opus-level performance on coding benchmarks (56.22% SWE-Bench Pro vs Opus's top tier) at roughly 1/20th the input cost. Opus wins on context window (200K vs 24K tokens), iterative debugging persistence, multimodal input, and instruction following across long conversations. For a detailed Opus breakdown, see my [Opus 4.6 hands-on review](https://www.mejba.me/blog/opus-4-6-hands-on-review).

### What does "self-evolving AI" mean for MiniMax M2.7?

MiniMax M2.7 autonomously ran 100+ rounds of self-improvement — analyzing its own failures, modifying its code, evaluating results, and keeping or reverting changes — without human intervention. This process produced a 30% performance gain and represents an early proof of concept for AI systems that participate in their own development.

### What is MiniMax M2.7's context window size?

M2.7 has a 24,000-token context window. This is sufficient for focused single-task generation (components, games, landing pages) but limiting for large codebase analysis or extended multi-file refactoring sessions that require holding context across many files simultaneously.

### Can I use MiniMax M2.7 with coding tools like Kilo Code?

Yes. MiniMax has provided official integration documentation for Kilo Code (VS Code extension and CLI), Claude Code, Cursor, and other major developer tools. Kilo Code offers free credits for M2.7 usage, making it one of the easiest ways to start testing the model in a real development workflow.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
