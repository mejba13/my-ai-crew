**BRAND:** mejba.me
**TITLE:** I Tested Gemini 3.1 Flashlight — Google's Speed Beast
**SLUG:** gemini-flashlight-speed-test
**TAGS:** AI Models, Developer Tools, Gemini 3.1 Flashlight, AI Benchmarks, Review

---

Three hundred and sixty-three tokens per second.

I read that number on the spec sheet and thought it was a typo. I've been working with AI models for long enough to know that speed claims almost always come with asterisks, fine print, and benchmark conditions nobody actually encounters in real-world use. So when Google dropped Gemini 3.1 Flashlight — positioned as their fastest, most cost-efficient model yet — I did what any skeptical developer would do. I opened my terminal, pulled up the API, and started throwing real tasks at it.

What happened over the next few hours genuinely surprised me. Not because the model is perfect — it absolutely isn't — but because it fundamentally changed how I think about which model belongs in which part of my development stack. There's a specific class of work where Flashlight doesn't just compete with bigger models. It embarrasses them.

But I'm getting ahead of myself. Before I tell you what blew me away (and what fell flat), you need to understand what Google was actually trying to build here — and why it matters if you're shipping code for a living.

## Why Another "Light" Model Actually Matters This Time

Here's something I've noticed about the AI model landscape over the past year: the gap between "flagship" and "efficient" models keeps shrinking in weird, unpredictable ways. We used to think of smaller models as clearly inferior — useful for quick classification tasks or chatbot filler, but nothing you'd trust with real engineering work.

That assumption is breaking down. Fast.

Gemini 3.1 Flashlight sits in a category Google is calling their speed-optimized tier within the Gemini 3 family. The pitch is straightforward: take the intelligence gains from Gemini 3, strip away the overhead that makes large models expensive and slow, and deliver something developers can actually afford to run at scale — without feeling like they've downgraded.

The pricing tells part of the story: $25 per million input tokens, with output tokens ranging from $0.50 to $1.50 per million depending on your configuration. That's aggressive. Not "free tier hobby project" cheap, but well within range for production workloads where you're making thousands of API calls daily.

What's more interesting is the performance data. On the GPQA benchmark — which tests graduate-level reasoning — Flashlight scored 86.9%. The MMU Pro benchmark, which evaluates multimodal understanding, came in at 76.8%. And on the Arena leaderboard, it posted an ELO score of 1400. Those aren't "light model" numbers. Those are numbers that would've been flagship-tier eighteen months ago.

I've been building AI-powered tools long enough to know that benchmarks only tell you half the story, though. The other half lives in what happens when you actually put the model to work. That's where things got really interesting.

## The Speed Test That Changed My Mind

My first real test was simple but revealing. I asked Flashlight to generate a 360-degree product viewer component — the kind of thing you'd see on a high-end e-commerce site. Multiple camera angles, smooth rotation, and a color-changing capability. Not trivial, but not rocket science either.

The response came back in seconds. Not "kind of fast" seconds. The kind of speed where you wonder if the model actually processed your request or just cached a template.

It hadn't cached anything. The code was specific to my prompt, properly structured, and — here's the part that caught me off guard — more visually compelling than what I'd gotten from Gemini 3.1 Pro for a similar task. A lighter, cheaper model producing better front-end output than its bigger sibling. That's not supposed to happen.

I ran the component in my browser. Smooth rotation. Working camera angles. The color-switching needed a small fix, but the foundation was solid. For rapid prototyping, this was exactly the kind of output that saves me an hour of scaffolding.

Now, before you think I'm about to tell you Flashlight is perfect at everything — it isn't. When I pushed the complexity higher, cracks started showing. A multi-component dashboard with interdependent widgets? It got about 70% there. Some instruction-following gaps crept in where the model seemed to lose track of constraints I'd set in the prompt.

But here's what I keep coming back to: the cost for that imperfect output was negligible. When you're iterating fast, an 70% solution at 1/10th the price and 3x the speed is often more valuable than a 95% solution that takes longer and costs more to generate. You fix the gaps faster than you wait for the expensive model.

That trade-off isn't always right. But for the workflows I use daily — rapid prototyping, component generation, UI exploration — it's a game-changer at this price point.

## The "Thinking Level" Feature Nobody's Talking About

Here's something most reviews of Flashlight are glossing over, and honestly, I think it's the most important feature of the entire model.

Gemini 3.1 Flashlight includes an adjustable "thinking level" setting. You can dial the reasoning depth up or down depending on your task. Simple chat response? Turn it down. Complex multi-step code generation? Crank it up.

Why does this matter? Because it means you're not paying for reasoning you don't need.

Every AI developer has had this experience: you send a simple formatting request to a powerful model, it "thinks" for way too long, burns through tokens, and delivers an answer that's way more elaborate than necessary. The thinking level control eliminates that waste.

I tested this across three scenarios:

**Low thinking level** — reformatting JSON data and generating basic type definitions. Response times dropped dramatically, costs were minimal, and the quality was exactly what I needed. No overthinking.

**Medium thinking level** — generating React components with specific styling requirements and state management. Good balance of speed and accuracy. This became my default setting for most development tasks.

**High thinking level** — multi-file code generation with complex business logic and error handling. Slower, but the output quality jumped noticeably. The model caught edge cases it missed at lower settings.

The ability to dynamically adjust this per request is genuinely powerful for production systems. Imagine routing simple user queries to low-thinking mode and complex analytical requests to high-thinking mode — all from the same model, with the same API endpoint. Your infrastructure stays simple, your costs stay optimized, and your users get appropriate response quality for their specific needs.

I haven't seen enough developers talking about this pattern, but it's going to become standard practice. Mark my words.

## Real Tasks, Real Results: The Full Breakdown

Talk is cheap. Benchmarks are interesting. But what matters is whether the model does useful work. I spent a full day throwing increasingly difficult tasks at Flashlight, and here's what I found.

### Front-End Development: Surprisingly Strong

This is where Flashlight genuinely shines — and I want to be specific about what I mean.

For single-component generation (cards, modals, navigation bars, product displays, interactive widgets), Flashlight produces output that's on par with or occasionally better than models costing 5-10x more. The code is clean, the CSS is modern, and the components actually work when you drop them into a project.

I generated a full product showcase page with animated transitions, responsive breakpoints, and accessible markup. The model nailed the responsive behavior on the first try. The animations needed tweaking — it chose a spring-based transition where I wanted a linear ease — but the structure was sound.

Where it struggles: multi-page layouts with complex routing logic, deeply nested state management patterns, and tasks where the prompt includes more than 6-7 distinct requirements. The model starts dropping constraints at that complexity level.

**My verdict:** If you're doing front-end prototyping, component library development, or UI exploration, Flashlight should be your default model. Switch to something heavier only when you actually need it.

### Code Generation Beyond the Browser

SVG generation? Excellent. I asked for a butterfly with wing-flap animations, and the output was genuinely impressive — proper SVG paths, smooth CSS keyframe animations, and appropriate color gradients. The whole thing rendered beautifully.

General utility functions, API endpoint scaffolding, data transformation pipelines — all solid. Flashlight writes competent, readable code for bread-and-butter programming tasks.

Where I noticed the ceiling: complex algorithm implementation. When I asked for an optimized graph traversal algorithm with specific memory constraints, the first attempt was correct but naive. A more powerful model would've reached for the optimal solution immediately.

### Game and Simulation Generation: The Wild Card

This is where things got fun — and where my expectations needed the most calibration.

I prompted Flashlight to build a 2D racing game with animation and real-time rendering. What came back was... actually pretty good? The car physics used clever mathematical shortcuts to simulate momentum and drift. The rendering loop was smooth. The track design was basic but functional.

Then I pushed harder. A 3D Formula 1 car drifting simulation. Here's where Flashlight hit its real limits. The simulation was technically functional — the car moved, the physics had some basis in reality, and it rendered without crashing. But visually? It looked like a PlayStation 1 game. Not in a charming retro way. In a "this clearly hit the model's complexity ceiling" way.

Honestly, I expected that. Generating convincing 3D graphics from a single prompt is still an incredibly hard problem, and Flashlight is explicitly optimized for speed, not for pushing the boundaries of what AI-generated code can do visually. The fact that it produced anything functional at all is noteworthy.

**My honest take:** Don't use Flashlight for complex game development or rich 3D simulations. Use it for 2D games, interactive demos, and visual prototypes where speed of iteration matters more than visual polish.

### Long-Form Text Generation: Better Than Expected

I asked Flashlight to write an analytical essay on the thematic depth of *The Lord of the Rings*, focusing on narrative impact and philosophical underpinnings. Why this topic? Because literary analysis requires sustained coherent reasoning, thematic threading, and structural awareness — the exact kind of thing "light" models usually bungle.

The essay came back well-organized, with a clear thesis, supporting arguments that actually built on each other, and specific textual references. The prose wasn't going to win a Pulitzer, but it was considerably better than what Gemini 3 Flash produced for the same prompt — and it arrived noticeably faster.

For developers building content tools, summarization pipelines, or documentation generators, this matters. Speed plus acceptable quality at scale is the whole game.

## The Kilo Code Integration: Where Speed Meets Workflow

One of the things that impressed me most wasn't Flashlight itself — it was how well it works within the Kilo Code CLI tool and VS Code extension.

I plugged Flashlight into Kilo Code's autonomous mode, where the model handles multi-step reasoning chains: reading files, running verification, using external tools, and generating output in a continuous workflow. The experience was remarkably smooth.

Here's a specific example. I asked it to generate a Mac OS-style browser interface with basic functional apps — a calculator, notepad, and file browser. From prompt to running output: 35 seconds. Total cost: 8 cents.

Eight cents. For a multi-component, multi-app interface with basic interactivity.

Now, were the apps fully polished? No. The calculator worked. The notepad was functional but basic. The file browser was more of a static mockup than a real file system interface. But as a starting point for iteration? That's an incredible cost-to-value ratio.

The integration also handles live data verification, which means the model can check its own outputs against real data sources during generation. That's a subtle but powerful capability for building reliable autonomous workflows.

Pro tip: Kilo Code currently offers $25 in free usage credits for their CLI tool. If you're going to test Flashlight in a real development environment — and you should — this is the lowest-friction way to do it.

## What Most People Are Getting Wrong About This Model

I want to be real about something. After testing Flashlight extensively, I think the developer community is misjudging it in two opposite directions.

**The hype crowd** is treating it like a model that can replace Pro-tier options for everything. It can't. Complex multi-file refactoring, deep architectural reasoning, and nuanced instruction following still require more powerful models. If you swap Flashlight into a workflow that genuinely needs deep reasoning, you'll get frustrated.

**The skeptics** are dismissing it as "just another small model" that's only useful for toy projects. This is equally wrong. For the specific workloads it's optimized for — rapid generation, high-volume tasks, real-time applications, and front-end prototyping — Flashlight is genuinely the best option available at its price point.

The smart move isn't choosing Flashlight OR a bigger model. It's building routing logic that sends the right tasks to the right model. Simple generation tasks, UI prototyping, content formatting, basic code scaffolding — route these to Flashlight. Complex reasoning, multi-constraint problems, architectural decisions — route those to Pro.

I learned this the hard way. My first instinct was to run everything through Flashlight because the speed was intoxicating. Within a day, I'd hit enough edge cases to realize that model selection needs to be task-specific, not one-size-fits-all.

Here's the uncomfortable truth nobody wants to admit: the model that's "best" depends entirely on what you're doing right now, not what scored highest on a leaderboard. Flashlight's 1400 ELO doesn't matter if your task needs capabilities it doesn't have. And Pro's superior reasoning doesn't matter if your task is simple enough that you're just burning money on unnecessary compute.

## The Pricing Reality Check

I mentioned Flashlight costs $25 per million input tokens. That's worth examining more carefully.

When I first saw this pricing, I had a mixed reaction. On one hand, for the performance you're getting, it's competitive. On the other, I was expecting Google to be more aggressive given the "light" positioning. Models in this efficiency tier from other providers can come in lower.

Here's my breakdown after a day of actual usage:

- **Light prototyping session** (15-20 component generations, low thinking level): roughly $0.30-0.50
- **Heavy development session** (complex multi-step tasks, high thinking level): roughly $2.00-4.00
- **The Mac OS browser project** (autonomous multi-step with Kilo Code): $0.08

For individual developers and small teams, these costs are trivial. For production systems making thousands of calls per hour, the math gets more interesting — and this is where Flashlight's speed advantage really compounds. 2.5x faster time-to-first-token means your users wait less, your infrastructure handles more concurrent requests, and your per-request compute costs stay lower.

My rough calculation: switching appropriate workloads from a Pro-tier model to Flashlight saved me about 60-70% on API costs while actually improving response times. The quality trade-off existed but was acceptable for the tasks I routed to it.

If Google drops the price even 20-30% — and competitive pressure suggests they might — this becomes a no-brainer default for the majority of developer API calls.

## The Real Benchmark: My Production Workflow

Benchmarks tell you what a model can do in controlled conditions. What I actually care about is what it does in my messy, real-world development workflow.

After a week of integration testing, here's how Flashlight earned a permanent spot in my toolkit:

**Morning prototyping sessions:** When I'm exploring ideas, trying different UI approaches, or quickly scaffolding new features, Flashlight is now my default. The speed means I can iterate through 5-6 variations in the time it used to take to generate one. That's not a minor improvement — it fundamentally changes how I approach design exploration.

**Client demo preparation:** When I need to quickly build interactive prototypes to show clients, Flashlight plus Kilo Code gets me from concept to clickable demo in minutes. Not polished production code, but convincing enough for a design review.

**Documentation and content pipelines:** For generating structured content, reformatting data, and building documentation from code comments, Flashlight handles 90% of my needs at a fraction of the cost.

**Where I still reach for bigger models:** Complex debugging sessions where I need the model to reason about subtle interaction patterns. Multi-file refactoring where context across many files matters. Architectural recommendations where I want the model's "best thinking," not its fastest thinking.

The combination of fast-and-cheap plus slow-and-powerful is genuinely more effective than either alone. It's like having both a sports car and a truck. You don't drive the truck to the grocery store, and you don't haul lumber in the sports car.

## Where Flashlight Fits in the Gemini 3 Family

For context, here's how I'd position the current Gemini 3 lineup based on my testing:

**Gemini 3.1 Pro** — your heavy lifter. Complex reasoning, multi-constraint problems, tasks where you need the model's absolute best output and cost is secondary. I reach for this when the task is hard and the stakes are high.

**Gemini 3.1 Flashlight** — your speed runner. High-volume generation, rapid prototyping, real-time applications, and any task where iteration speed matters more than first-attempt perfection. This is my default for 60-70% of daily development tasks.

**Gemini 3 Flash** — the previous generation efficient model. Still has its place, but Flashlight outperforms it on speed and quality in virtually every test I ran. If you're still using Flash, upgrade.

The 45% faster output speed and 2.5x improvement in time-to-first-token over Flash aren't incremental improvements. They cross a threshold where the model feels genuinely real-time. Prompts that used to feel like "waiting for the AI" now feel like "having a conversation."

That perceptual shift matters more than any benchmark number, because it changes how developers interact with the model — and ultimately, what they build with it.

## My Prediction: The Thinking Level Pattern Goes Everywhere

I mentioned the adjustable thinking level earlier, and I want to come back to it because I genuinely believe this is the most forward-looking feature in Flashlight — and one that every major model provider will copy within the next 12 months.

The idea is simple: not every request needs the same amount of reasoning. A model that can dynamically adjust its computational effort per request isn't just more efficient — it's more useful. You're essentially getting multiple models in one API endpoint.

I think we're heading toward a world where model selection isn't "pick a model" but "pick a reasoning budget." You'll set a thinking level for each API call based on the expected complexity, and the model will allocate compute accordingly.

Flashlight is the first model I've used where this isn't just a theoretical concept but a practical, production-ready feature. And once you start building with it, going back to fixed-reasoning models feels wasteful.

The developers who figure out dynamic reasoning allocation first will have a meaningful cost and speed advantage. This is the pattern to watch.

## What I'd Tell a Developer Considering Flashlight

If you're on the fence, here's my honest recommendation.

Start by identifying the 30-40% of your current AI API calls that are "simple" — formatting, basic generation, single-component code, data transformation. Route those to Flashlight today. Measure the cost savings and speed improvements. You'll see immediate returns.

Then gradually expand. Test it on your prototyping workflows. Try it for documentation generation. Push it into your CI/CD pipeline for automated code review comments or test generation.

You'll find the boundary where Flashlight's quality drops below your threshold. That boundary is your switching point — everything below it goes to Flashlight, everything above it stays with your current model.

That boundary is higher than you think. I expected to hit Flashlight's limits quickly. Instead, it handled about 65% of my daily workload better or comparably to the heavier models I was using before — and at a fraction of the cost and latency.

The model is available through Google AI Studio, the Gemini API, OpenRouter, and the Kilo Code CLI/VS Code extension. My recommendation: start with Kilo Code's free credits to test it in your actual development environment, then move to direct API access once you've validated the fit.

Three hundred and sixty-three tokens per second. I started this post thinking that number was marketing hype. After a week of shipping actual code with Flashlight as my primary generation engine, I think it might be the most practically relevant spec in AI right now. Not because speed is everything — but because once generation is fast enough to feel instant, you stop waiting and start building. And that changes what's possible.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
