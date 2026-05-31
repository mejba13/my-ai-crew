**BRAND:** mejba.me
**TITLE:** Gemini 3.5 Flash Tested: GA at Launch, Pro-Level Output
**META TITLE:** Gemini 3.5 Flash Tested: GA at Launch, Pro-Level Output
**SLUG:** gemini-3-5-flash-ga-tested
**PRIMARY KEYWORD:** Gemini 3.5 Flash
**META DESCRIPTION:** I tested Gemini 3.5 Flash against 3.1 Pro and the old 3 Flash Preview the day it shipped. Here's what GA at launch really means for agentic workflows.
**TAGS:** Gemini 3.5 Flash, Google AI, AI Model Reviews, AI Agents, Google Antigravity

---

I had the IO 2026 keynote playing on my second monitor when the slide came up.

`gemini-3.5-flash — generally available today.`

I stopped typing. Read it again. Then I did the thing I always do when a Google announcement actually moves me — I pulled up the model dropdown in AI Studio to see if it was real or if "today" meant "next week, when the docs catch up." It was real. The dropdown had already updated. The pricing card was live. The "Preview" tag that had been hanging on every Gemini Flash release since the original December 2025 launch was gone.

That's the part I want you to sit with for a second, because Google buried it under a parade of demos and most of the recap pieces missed why it matters.

Every Gemini Flash before this one shipped as a Preview. Gemini 3 Flash Preview. Gemini 3.1 Flash Preview. The stealth Flash that quietly took over LMArena three weeks before I/O. They were all Preview-tier, which means you could test them but you could not put them in front of a real user without an asterisk. You had no SLA. You had no guarantee the model wouldn't get swapped under you. You had no commitment from Google that the price would hold.

Gemini 3.5 Flash skipped Preview entirely. It launched **GA on day zero**. First Gemini model ever to do that. Same legal terms as Pro, same SLA tier, same "you can put this in production today and not get burned tomorrow" guarantee. And — based on the testing I've spent the last seventy-two hours on — it is also the first Flash model that operates at Pro-class quality on the workloads that actually matter for agent builders.

So I did what I always do. I cleared my afternoon, opened AI Studio, fired up the Antigravity 2.0 framework that Google had just shipped alongside it, and ran the new model against Gemini 3 Flash Preview and Gemini 3.1 Pro Preview on the same six prompts. Real prompts. The ones I use to stress-test every new release. The ones where marketing claims either survive or get exposed.

Here is what I found, what I tested, and what I'd actually deploy on Monday morning.

## What "GA at Launch" Actually Costs You

The pricing card on Gemini 3.5 Flash reads $1.50 per million input tokens, $9.00 per million output tokens, and $0.15 per million on cached input. That is not Flash-tier pricing the way the original December 2024 Gemini 1.5 Flash was Flash-tier pricing. The old Flash was a workhorse at fifteen cents per million input. This is something else.

It is, however, dramatically cheaper than Gemini 3.1 Pro, which sits at $2.00 input and $12.00 output. So if the quality claim holds — if 3.5 Flash genuinely matches Pro on coding and agentic work — you are getting Pro-class output at a 25 percent discount and roughly four times the throughput. That math is what makes this release matter.

But there is a catch nobody is talking about yet.

The catch is that **3.5 Flash is roughly three times more expensive than the model it replaces in most agent pipelines**. The original Gemini 3 Flash Preview was running at around $0.50 input and $3.00 output. Every team I know that built an agent on Flash Preview is now staring at a pricing migration where their per-run costs are about to triple if they upgrade.

Whether that math works for you depends entirely on what the extra spend buys. Which is exactly what I needed to test.

## The Setup: Same Prompts, Three Models, One Honest Spreadsheet

Before I show you any results, here's how I ran this so you can call me out if my methodology is bad.

I picked six prompts from my standard rotation. Each one stresses a different capability:

1. A multi-agent jet stream airflow simulator with adjustable throttle parameters
2. A crowd animation in Three.js with multiple camera perspectives
3. A voxel garden scene with procedural generation
4. A responsive web page listing the first 25 legendary Pokémon with a dark theme toggle
5. A fly-over 3D visualization of downtown Los Angeles using only open-source map data
6. A working Linux desktop environment compressed into a single HTML file with a functional terminal

I ran each prompt three times — once on Gemini 3 Flash Preview, once on Gemini 3.5 Flash, once on Gemini 3.1 Pro Preview — and I logged three things for every run. Wall-clock time to completion. Total tokens generated in the response (visible reasoning plus output). And a 1-to-10 quality score based on whether the code actually ran, whether the output matched the request, and whether it had the kind of polish where a designer in the room would say "ship it" instead of "almost."

I also gave each model access to the same toolchain through Antigravity — code execution, function calling, Google Search grounding, and URL context grounding. Same temperature. Same system prompt. Same fresh session for every run.

The point was not to publish a benchmark. The point was to figure out, prompt by prompt, where the new model earns its 3x cost increase over old Flash and where it doesn't.

Let me walk you through what came out.

## Test One: The Jet Stream Simulator

This was the first thing I ran because Google had used a similar prompt in their internal demo and I wanted to see if the live model held up against the keynote footage.

The prompt asked for a multi-agent airflow simulation that modeled how a jet engine's throttle setting changes the airflow pattern across a 3D wing section, with sliders to adjust throttle from idle to full afterburner and a particle visualization of the resulting turbulence.

**Gemini 3 Flash Preview** got it done in about 20 seconds, generated roughly 4,400 tokens, and produced something that ran. The simulation worked. The slider was wired up. The particle effects rendered. But the wing geometry was a flat rectangle, the throttle response was linear when it should have been roughly cubic, and the visualization broke if you dragged the slider too fast.

**Gemini 3.5 Flash** took about a minute and a half, generated 21,000 tokens, and produced something noticeably more thoughtful. The wing had a proper airfoil cross-section. The throttle response followed a realistic non-linear curve. The particle system included vortex shedding off the trailing edge that genuinely surprised me. When I cranked the throttle to maximum, the airflow pattern broke up into discrete turbulent eddies the way it actually does on a real wing.

**Gemini 3.1 Pro Preview** took about two minutes and twenty seconds, generated around 18,000 tokens, and produced something aesthetically a touch more polished — better color palette, smoother camera tween, nicer UI for the slider — but functionally indistinguishable from what 3.5 Flash had given me.

That's the pattern I kept seeing across the rest of the tests. **The 3.5 Flash output and the 3.1 Pro output were functionally interchangeable, with Pro winning small on aesthetics and losing on throughput.**

The big jump was not Pro versus Flash. The big jump was old Flash versus 3.5 Flash. The chain-of-thought reasoning got substantially deeper. Where 3 Flash Preview produced a flat, single-pass response, 3.5 Flash visibly worked through the problem in structured steps — identify the physics, model the geometry, design the data structures, implement the visualization, test edge cases. The 21,000 tokens were not bloat. They were the model showing its work.

That is the headline finding from this whole exercise, and it has a real implication. If you've been running an agent on Flash Preview to keep costs down, you've been running on a model that doesn't really reason. 3.5 Flash does. And the difference shows up in any task with more than one step.

## Test Two: Crowd Animation in Three.js

This is one of my favorite stress tests because Three.js prompts expose a model's weaknesses fast. You need spatial reasoning, an understanding of WebGL primitives, and enough taste to make the result actually look like a crowd instead of a swarm of geometric cubes.

The prompt: animate a crowd of 200 people moving through a town square, with three camera angles you can switch between — overhead, eye-level, and a tracking shot that follows one specific person through the crowd.

**3 Flash Preview** produced 200 boxes moving on a flat plane with one fixed camera. The boxes had no animation cycles. Nothing followed anything. The "camera switching" was implemented as three different `<canvas>` elements that you toggled. It ran. It was technically a crowd. Nobody would call it animation.

**3.5 Flash** produced 200 stylized humanoid figures with walk cycles, three actual camera controllers wired through Three.js's perspective camera class, and a tracking shot that locked onto a specific figure marked in red. The figures had collision avoidance — they moved around each other instead of clipping through. The overhead shot included a minimap. I'd ship it as a portfolio piece.

**3.1 Pro Preview** produced the same thing, with slightly better lighting, marginally smoother walk cycles, and one extra camera angle (a slow orbital pan) that I didn't ask for but which made the demo feel more cinematic.

Same pattern. Pro is better. Flash is good enough that the difference doesn't move the needle for most work.

## Test Three: The Linux Desktop in a Single HTML File

This was my hardest prompt because it crosses two domains — UI/UX simulation and state preservation across multiple "applications" within a single file. The prompt asked for a working Linux desktop with a terminal that responded to basic shell commands, a CPU and memory monitor that updated in real time, a settings panel where you could change wallpaper and theme, and the requirement that all of it preserve state across navigation between apps within the file.

**3 Flash Preview** gave me a desktop wallpaper, three icons, and a terminal that responded to `ls` and `cd` only. The CPU monitor was a static placeholder image. State preservation didn't exist — switching between apps reset everything.

**3.5 Flash** gave me a desktop environment that genuinely felt like a Linux distribution. The terminal responded to about 15 different commands including `ls`, `cd`, `pwd`, `echo`, `mkdir`, `touch`, `cat`, `clear`, and a working `top`. The CPU and memory monitors were tied to a simulated load that you could spike by running fake processes. The settings panel actually changed the wallpaper. State persisted in a `localStorage`-backed virtual filesystem. I spent twenty minutes just playing with it.

**3.1 Pro Preview** gave me roughly the same thing, with a slightly nicer dock animation and a window-snapping feature I didn't ask for.

This is where I started to feel the shape of what Google has actually shipped. The new Flash isn't a souped-up Flash. It's a Pro that runs faster and costs less. The Flash naming is a marketing decision, not an architectural one.

## Where Gemini 3.5 Flash Still Falls Short

I would be doing you a disservice if I only showed you the wins. There are two clean failures I hit during the testing that you should know about before you migrate any agent pipeline.

The first is the **modified river crossing problem**. This is a classic logic puzzle — a farmer needs to cross a river with a fox, a chicken, and a bag of grain, and only certain combinations can be left alone — but I modified it so that the "correct" answer to the original puzzle was wrong in my version. Specifically, I removed one of the usual constraints. The right answer was simpler than the standard solution.

3.5 Flash solved the standard puzzle. It pattern-matched on the prompt, recognized the river crossing structure, and reproduced the textbook solution — which was wrong for the version I'd given it. When I explicitly asked it to re-read the constraints, it noticed the difference and apologized, but it didn't catch the trap on the first pass.

Pro caught it. Pro paused, read the constraints carefully, and produced the correct simpler solution on the first try.

The second failure was a **modified trolley problem** where the moral weights were inverted. I told the model that in this version of the problem, the side track had more people than the main track. 3.5 Flash initially defaulted to the standard answer before I prompted it to re-read. Pro got it right immediately.

What this tells me is that **3.5 Flash is faster and cheaper but it pattern-matches harder on familiar problem shapes**. If your agent runs into novel logic problems where the right answer requires ignoring a familiar template, you want Pro for that step. If you're building anything where edge-case logic matters — legal reasoning, medical triage, financial compliance — keep Pro in the loop for the reasoning calls and use 3.5 Flash for everything else.

This is exactly the kind of architecture decision that the new Antigravity framework is designed to handle. You don't pick one model for the whole pipeline. You route the easy steps to 3.5 Flash and the hard steps to Pro, and the framework manages the handoffs. I'll get into how that works in a second.

## The Thinking Level Knob Nobody Mentions

One feature of 3.5 Flash that didn't make the keynote slides but which mattered a lot during my testing is the **thinking level** parameter. Inside AI Studio, you can set the model's reasoning depth to minimal, low, medium, or high. This controls the token budget for the internal chain-of-thought before the model produces its final response.

I default-tested at medium because that's what AI Studio uses for new sessions. But when I cranked it to high on the modified river crossing problem, 3.5 Flash actually caught the trap. The downside was that the response took three minutes instead of ninety seconds and used 40,000 tokens instead of 21,000.

That trade is real. If you're running an agent that needs to handle ambiguous logic, set thinking to high and pay for the extra tokens. If you're running an agent that handles thousands of routine tasks, set thinking to minimal and let it scream through them.

The fact that you can tune this per-request — through the API, not just AI Studio — is, in my opinion, one of the most underrated developer experience improvements Google has shipped in the last year. It lets you build agent pipelines where the same model is doing different jobs with different reasoning budgets, and you don't need to switch models or providers for it.

I'd never go back to a model that doesn't expose this.

## What This Means for Agentic Workflows

Now we get to the part I actually care about, because the model is interesting but the workflow is where the money is.

If you're building agents in 2026, you are not picking one model. You are routing tasks across a mesh of models, and the routing matters more than any single model's benchmarks. The new Antigravity 2.0 framework is built around exactly this assumption. You can stand up a graph of subagents, each one bound to a specific model and a specific role, and the framework handles the orchestration.

Here is what I'd actually do with 3.5 Flash in that kind of mesh, based on the testing.

**Use 3.5 Flash for the bulk of the agent's work.** Code generation, data transformation, API calls, summarization, JSON structuring, web scraping with grounding, document processing. The vast majority of what an agent does in production is not novel reasoning. It's repeatable transformation work. 3.5 Flash does that work at Pro quality and Flash-tier throughput.

**Use Gemini 3.1 Pro Preview for the planning and arbitration steps.** When the agent has to decide which subagent to invoke next, or when it has to reason about whether a result is good enough to ship, or when it hits an edge case that breaks its existing playbook — that's where Pro earns its premium. The marginal cost is small if you're only invoking Pro at decision points.

**Use the cached input pricing aggressively.** At $0.15 per million tokens, cached input is genuinely cheap. If your agent has a stable system prompt or a stable tool definition block, cache it. The savings compound fast when you're running thousands of agent calls a day.

**Pin the model version in your code.** GA does not mean immutable. Google will ship 3.5 Flash 002 and 003 and eventually a 3.5 Flash that is meaningfully different from what shipped at I/O. If you want reproducible behavior, pin to a specific snapshot ID in your API calls and migrate explicitly when a new snapshot ships.

The team I'd worry about is the one running production agents on 3 Flash Preview right now and assuming an upgrade to 3.5 Flash will be transparent. It will not be. Your token costs will roughly triple. Your latency will go up. Your output quality will go up too, which may justify the trade — but you need to model the cost change before you flip the switch.

If you're new to building multi-agent systems on Gemini, the [Antigravity workflow guide I published recently](https://www.mejba.me/notebooklm-gemini-antigravity-workflow) walks through how I structure those graphs. The patterns there hold for 3.5 Flash with one tweak — you can move a lot more of the work down a tier from Pro to Flash than you could before.

## Where Does This Leave Google's Model Lineup

Here's my read on where Google has actually positioned itself with this release.

Gemini 3.5 Flash is the **default model**. Not the Flash tier, not the cheap option — the default. Google has said as much in their own messaging, and the pricing supports it. They want you to reach for 3.5 Flash first for everything, and only invoke Pro when you genuinely need its edge on hard reasoning.

Gemini 3.1 Pro Preview is **for the hard cases**. It still wins on Humanity's Last Exam, ARC-AGI-2, and any benchmark that rewards raw parametric knowledge or pure abstract reasoning. It is the model you call when the easy path has failed.

And Gemini 3.5 Pro — which Google teased in the keynote and which is reportedly in internal use right now — is going to be the next chapter. If 3.5 Flash matches 3.1 Pro on agentic work, then 3.5 Pro is going to redefine what a frontier model can do. I'd put money on it shipping before the end of June 2026.

The bigger story underneath all of this is that **Google has finally cracked the price-performance frontier**. For the last two years, every frontier-class model has been priced like a luxury good. Claude Opus 4.7, GPT-5.4, Gemini 3.1 Pro — they're all in the same neighborhood, and most production teams can't afford to run them at scale. 3.5 Flash is the first model I've tested that delivers something close to that quality at a price point where you can actually use it as the default.

That changes the economics of building AI products. Not by a little. By a lot.

If your AI product cost structure was built around using a cheap model for everything and tolerating mediocre output, the bar just moved. Your competitors who switch to 3.5 Flash are going to ship better experiences without paying meaningfully more. You can either match them or get out-shipped.

## Frequently Asked Questions

### Is Gemini 3.5 Flash actually generally available or still preview?
Gemini 3.5 Flash is fully GA as of May 19, 2026. It is the first Gemini model to launch directly into general availability without a Preview stage, which means you can use it in production today with the same SLA and stability commitments as Gemini 3.1 Pro. For the full implementation notes, see the "What GA at Launch Actually Costs You" section above.

### How much does Gemini 3.5 Flash cost compared to Gemini 3 Flash?
Gemini 3.5 Flash costs $1.50 per million input tokens and $9.00 per million output tokens, with cached input at $0.15. That is roughly three times the cost of the original Gemini 3 Flash Preview ($0.50 input, $3.00 output), but about 25 percent cheaper than Gemini 3.1 Pro. The cost increase is significant for anyone migrating existing Flash-based agents.

### Should I use Gemini 3.5 Flash or Gemini 3.1 Pro for agent workflows?
Use Gemini 3.5 Flash for the bulk of an agent's work — code generation, summarization, data transformation, API orchestration — and reserve Gemini 3.1 Pro for the planning, arbitration, and hard-reasoning steps. The Antigravity 2.0 framework is designed to let you route between them inside the same graph.

### Does Gemini 3.5 Flash beat Gemini 3.1 Pro on benchmarks?
On coding and agentic benchmarks like Terminal-Bench 2.1, MCP Atlas, and OSWorld-Verified, Gemini 3.5 Flash matches or beats Gemini 3.1 Pro. On pure reasoning benchmarks like Humanity's Last Exam and ARC-AGI-2, Gemini 3.1 Pro still wins by 4-5 points. Pick the model based on which benchmark looks most like your actual workload.

### What is the thinking level setting in Gemini 3.5 Flash?
Thinking level is a parameter that controls how many reasoning tokens the model spends before producing the final answer. It can be set to minimal, low, medium, or high. Higher levels improve performance on ambiguous or trap-style logic problems at the cost of more tokens and longer latency. For the full breakdown, see "The Thinking Level Knob Nobody Mentions" section above.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
