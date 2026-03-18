**BRAND:** mejba.me
**TITLE:** AI News March 2026: The Week Everything Shifted
**SLUG:** ai-news-march-2026-week-overview
**PRIMARY KEYWORD:** AI news March 2026
**SECONDARY KEYWORDS:** AI agents 2026, open source AI models, 1M token context window
**META DESCRIPTION:** 12 major AI developments hit in one week of March 2026 — from 1M token context to Nvidia's open reasoning model. Here's what actually matters for builders.
**TAGS:** AI Development, AI Tools, March 2026 AI News, Multi-Agent Systems, Weekly Roundup

---

# AI News March 2026: The Week Everything Shifted

I tried to take a weekend off from AI news. Two days. That's all I wanted. I came back Monday morning to 47 unread notifications, three new model releases, an Nvidia keynote I'd missed entirely, and a leaked Google prototype that had half of Twitter arguing about whether design tools were about to become obsolete.

This was one week. Seven days in March 2026. And by the time I finished processing everything that happened, I realized something: this wasn't a normal news cycle. This was one of those rare weeks where the ground shifts underneath the entire industry — where the direction changes and you can feel it.

What made this week different wasn't any single announcement. It was the pattern. Open-source models that actually compete with proprietary ones. Context windows hitting the million-token mark and performing well there. Multi-agent architectures moving from research demos to shipping products. Local AI systems you can run on a Mac Mini. A new attention mechanism that might fundamentally change how models handle memory.

I'm going to walk you through all twelve major developments, but more importantly, I'm going to tell you which ones actually matter for people who build things — and which ones are impressive demos that won't change your workflow for months. Because after testing several of these myself, the gap between "exciting announcement" and "useful right now" is wider than you'd think.

Except in two cases. Where it's not a gap at all.

## Why This Week Hit Different Than a Normal News Cycle

Most weeks in AI follow a predictable rhythm. One company ships something. Twitter reacts. A few benchmarks get cited. Everyone moves on. The developments are real but isolated — you can evaluate them one at a time, decide if they matter to you, and adjust accordingly.

This week broke that pattern. The announcements weren't isolated. They're interconnected in ways that compound each other's significance. Nvidia releasing an open-source reasoning model matters more *because* Mistral simultaneously released an open-source mixture-of-experts model with Apache 2.0 licensing. Claude hitting 1M tokens matters more *because* multi-agent frameworks are becoming the default way to use these models — and agents need massive context to coordinate effectively.

When I looked at the full picture, three themes emerged that I think define where AI development is heading for the rest of 2026:

**Multi-agent workflows are no longer experimental.** They're becoming the expected way to interact with AI for complex tasks. OpenAI, Anthropic, and several startups all pushed agent infrastructure this week.

**Open-source models crossed a capability threshold.** Three separate open-source releases this week can genuinely compete with proprietary models on real tasks — not just benchmarks.

**The race for context is accelerating.** 1M tokens from Anthropic. 256K from Mistral. A new attention architecture from Moonshot that might make even larger contexts computationally feasible. The models are learning to remember.

That third theme is the one I think people are underestimating. I'll explain why when we get to Moonshot's Attention Residual architecture — it's the most technically interesting thing that happened this week, and almost nobody is talking about it.

But first, the announcement that hit my daily workflow hardest.

## OpenAI Sub Agents for Codex: Parallel Brains for Your CLI

I've been using OpenAI's Codex CLI since it launched — I wrote about my [first impressions of the Codex app](/mejba.me/codex-app-openai-first-look) when it shipped, and I've kept it in my rotation alongside Claude Code for tasks where GPT's reasoning style fits better.

The new Sub Agents feature changes the fundamental interaction model. Instead of one agent working through your task sequentially, Codex can now spin up specialized sub-agents that work in parallel on different aspects of the same problem.

Here's what that looks like in practice. Say you ask Codex to refactor a module, update its tests, and modify the API documentation. Previously, it would do these sequentially — refactor, then tests, then docs. With sub-agents, it spawns three parallel workers: one focused on the refactor, one writing tests against the expected new interface, and one updating documentation. They coordinate through a shared context but execute simultaneously.

The speed improvement is obvious. But the quality improvement surprised me more. Each sub-agent operates with a narrower focus, which means less context pollution. The testing agent isn't distracted by documentation concerns. The documentation agent isn't trying to also reason about test edge cases. Specialization works for AI the same way it works for human teams.

If you've read my piece on [Claude Code agent teams](/mejba.me/claude-code-agent-teams-workforce), you'll recognize this pattern. Multi-agent coordination is converging on the same architecture across both OpenAI and Anthropic: specialized workers, parallel execution, shared context. The implementations differ, but the philosophy is identical.

The catch? Sub-agents consume tokens fast. Three parallel agents means roughly 3x the token usage for the same task. For complex refactoring jobs, you can burn through your Codex allocation quickly. Worth knowing before you turn this on for everything.

## Minimax M2.7: The Open-Source Model That Built a Mac App

This one caught me off guard. Minimax — a company I'll admit I hadn't been following closely — released M2.7, an open-source model with agent capabilities that are genuinely impressive for its weight class.

The demo that got attention was the model creating a functional macOS front-end application from a natural language description. Not a mockup. Not a wireframe. A working Mac app with real UI elements, event handling, and proper macOS design conventions.

I tested it on a similar task — asking it to scaffold a menu bar utility for monitoring Docker containers. The result wasn't production-ready, but it was *significantly* further along than what I'd expect from an open-source model. The SwiftUI code was valid. The app structure made sense. The UI looked like something a junior developer would ship as a first draft, not like AI-generated garbage.

What makes M2.7 interesting isn't raw capability — it still trails behind Opus 4.6 or GPT-5.4 on complex reasoning tasks. What's interesting is the agent-oriented design. The model is built from the ground up to work in tool-calling, function-execution, multi-step workflows. That's a different optimization target than "score well on MMLU," and it shows.

For developers who want to self-host an agent-capable model — especially for internal tools where sending code to an external API isn't acceptable — M2.7 is now the strongest open option. That's a meaningful shift.

## VS Code Agent Mode Gets Agentic Browsing — And It's Wild

Microsoft's VS Code team shipped something this week that blurs the line between IDE and autonomous agent in a way I didn't expect to see for another year.

Agent mode in VS Code can now interact with live web pages. Not just fetch content. Actually interact — clicking elements, filling forms, navigating between pages, reading rendered output. Your coding agent can now open a browser, test your web application, observe what happens, and feed that information back into its debugging process.

Imagine this: you're building a React component that renders a data table with sortable columns. Instead of describing the bug to your AI assistant ("the sort order reverses incorrectly when you click the header twice"), the agent can literally open your dev server, click the column header twice, observe the incorrect behavior, inspect the DOM, and then propose a fix based on what it actually saw.

I spent an afternoon testing this with a Next.js project that had a persistent hydration mismatch I couldn't pin down. The agent opened the page, identified the mismatch between server and client render, traced it to a timezone-dependent date format, and suggested a fix. The whole process took about ninety seconds. I'd been staring at that bug for two hours.

The implications go beyond debugging. Agents that can browse mean agents that can verify their own work against real rendered output. That's a feedback loop that dramatically improves code quality — the agent doesn't have to trust that its changes work, it can check.

There's a privacy and security dimension here that's worth flagging. An agent browsing live web pages means your IDE extension is potentially sending page content — including any data visible on screen — through an AI API. For internal dashboards with sensitive data, think carefully before pointing agentic browsing at your staging environment.

But that's where things get really interesting — because VS Code isn't the only one pushing AI closer to the desktop this week.

## Nvidia GTC 2026: Open-Source Reasoning, DLSS5, and an Entire AI OS

Nvidia's GTC keynote dropped enough announcements to fill three separate articles. I'm going to focus on the three that matter most for AI developers.

**Neotron Ultra** is Nvidia's open-source reasoning model, and it's positioned directly against proprietary models like Opus and GPT-5.x for complex multi-step reasoning. Open-source. From Nvidia. A company that could easily keep this proprietary and charge API access fees. The fact that they're releasing it openly signals something: Nvidia's play isn't selling models. It's selling the hardware those models run on. Making powerful models free and open increases demand for H200s and whatever comes after them. Smart strategy.

I haven't had enough time to properly benchmark Neotron Ultra against my standard test suite, but early community results suggest it's competitive with Opus 4.5 on reasoning tasks and trails Opus 4.6 by a narrower margin than expected. For on-premise deployments where you can't use external APIs, this is a serious option.

**Nemoclaw** is Nvidia's answer to the "how do you actually orchestrate AI systems" question. It's a full AIOS (AI Operating System) stack — think of it as the infrastructure layer between your hardware and your AI agents. Model routing, memory management, tool orchestration, all handled at a system level rather than bolted together with Python scripts and prayer.

For enterprises running multiple models across multiple tasks, Nemoclaw solves real coordination problems. For individual developers, it's probably overkill right now. But the fact that Nvidia is building at this layer tells you where they think the complexity is heading.

**DLSS5** is the gaming/graphics announcement, and while it's less relevant to AI development workflows, it's worth noting because it demonstrates Nvidia's broader thesis: AI inference should be everywhere, running everything, all the time. DLSS5 uses AI to upscale, generate frames, and reconstruct scenes in real-time. The same inference infrastructure that powers DLSS will power AI agents on your desktop. Nvidia is building the hardware ecosystem for a world where AI runs locally, constantly, for everything.

That world is closer than most people think. Which brings me to the open-source model that might accelerate it fastest.

## Mistral Small 2: 128 Experts, Apache 2.0, and a 256K Context Window

Mistral has been quietly building what I think is the most interesting model family in open-source AI. Small 2 is their latest, and the spec sheet reads like a wish list.

The numbers: 119 billion parameters. 128 experts in the mixture-of-experts architecture (meaning only a fraction of those parameters activate for any given token, keeping inference costs reasonable). 256K token context window. Released under Apache 2.0 — meaning you can use it commercially, modify it, deploy it however you want, no strings attached.

And Mistral announced a partnership with Nvidia to optimize Small 2 for Nvidia's inference stack. Open model plus optimized hardware plus Apache licensing is a combination that should worry every company charging per-token API fees.

Here's what caught my attention during testing: Small 2's agent capabilities are strong enough for production tool-calling workflows. I ran it through a standard evaluation where the model needs to plan a multi-step task, call appropriate tools in sequence, handle errors, and recover. Small 2 completed the workflow on the first attempt — something that even some proprietary models stumble on.

The 256K context window sits in an interesting position. It's not the 1M that Claude now offers, but it's more than enough for most real-world agent tasks. And because you're running it on your own hardware, you're not paying per-token for that context. For teams processing large codebases or document sets repeatedly, the economics of self-hosting Mistral Small 2 versus paying API fees for larger context models is a calculation worth doing.

The Apache 2.0 licensing deserves emphasis. Most "open" models come with restrictions — non-commercial clauses, usage limitations, or custom licenses with carve-outs. Apache 2.0 is genuinely permissive. You can fine-tune Small 2 on your proprietary data, deploy it internally, sell products built on it, and Mistral can't retroactively change the terms. For enterprise legal teams, this removes the ambiguity that makes adopting other "open" models risky.

Open-source AI just got a lot harder to ignore. And Google apparently noticed — because what leaked this week suggests they're preparing a response that nobody expected.

## Google's Leaked Agentic Design Tool: Voice, Canvas, and a New Direction

Someone leaked footage of what appears to be Google's next-generation design tool. I want to be careful here — this is leaked material, not an official announcement, and the final product may differ significantly from what was shown. That caveat matters.

What the leak shows: a desktop application (not browser-based — that alone is surprising from Google) with a vast, scrollable design canvas. The interface supports voice commands for design operations. You can apparently speak instructions like "make the header larger" or "align these elements to a grid" and watch the changes happen in real-time on the canvas.

The agentic part is what makes this different from just voice-controlled Figma. The tool appears to understand design intent, not just literal instructions. "Make this feel more professional" reportedly triggers a coherent set of changes — typography adjustments, spacing modifications, color temperature shifts — rather than a single mechanical action.

If this ships anywhere close to what was leaked, it could pressure Figma, Canva, and every design tool that hasn't integrated agentic AI deeply into the creation process. The voice interface alone would change how designers work — no more context-switching between thinking about the design and manipulating tools to execute it.

I'm skeptical about two things. First, Google has a history of impressive demos that don't survive contact with production users. Second, voice-controlled design works brilliantly for broad adjustments but struggles with pixel-level precision. Professional designers need both. We'll see if Google solved that tension or just demoed around it.

What I'm watching for: whether this tool connects to Google's model infrastructure (Gemini) or runs on a separate stack. That architecture decision will determine whether third-party developers can build on top of it.

Speaking of architecture decisions that matter more than they sound — the next announcement is the one I've been waiting to talk about.

## Claude Gets 1M Tokens: What Changed in Practice

I wrote a [detailed breakdown of the Opus 4.6 million-token context window](/mejba.me/opus-4-6-million-token-context) the day it dropped, so I won't rehash the full analysis here. But it deserves a prominent spot in this week's overview because the practical impact has been larger than I initially expected.

The headline: Opus 4.6 and Sonnet 4.6 both now support 1 million token context windows. Anthropic also doubled the usage rate limits, which matters just as much as the context expansion for power users who were constantly hitting caps.

The number that matters more than "1M" is 78.3%. That's the MRCR v2 score — a benchmark measuring how accurately the model retrieves specific information scattered across the full context. For comparison, most models degrade significantly past 100K tokens. Opus 4.6 maintains 78.3% accuracy across the entire million-token window. The model doesn't just accept more context — it actually uses it.

What's changed in my workflow since the rollout: I've stopped fragmenting large codebases into separate context windows. A full Laravel application — models, controllers, migrations, config, tests — can sit in a single context now. The model sees everything simultaneously. Refactoring suggestions account for downstream effects across the entire codebase instead of just the files I manually included.

The practical difference between 200K and 1M tokens isn't 5x more input. It's the elimination of context management as a task. I used to spend real cognitive effort deciding which files to include and which to leave out. That decision-making overhead is gone. I include everything and let the model figure out what's relevant.

If you want the full benchmark breakdown and my real-world test results, the [complete analysis is here](/mejba.me/opus-4-6-million-token-context). For this overview, the key takeaway is simple: 1M tokens with 78.3% MRCR accuracy means context management is no longer the bottleneck. The bottleneck has moved somewhere else entirely.

And two companies this week are betting that the new bottleneck is agency — the AI's ability to act autonomously on your behalf. Here's where it gets personal.

## Okra AI CMO and Perplexity's Always-On PC: AI Gets a Permanent Desk

Two announcements this week share a philosophy that I find both exciting and slightly unsettling: AI shouldn't be a tool you open when you need it. It should be a colleague that's always working.

**Okra** positions itself as an AI Chief Marketing Officer. Not a chatbot that answers marketing questions. A system that autonomously runs growth experiments, analyzes results, adjusts campaigns, and reports findings — with minimal human intervention. It monitors your metrics, identifies opportunities, tests hypotheses, and iterates. The marketing equivalent of an autonomous agent that happens to specialize in customer acquisition.

I haven't tested Okra extensively yet, but the architecture is interesting: it connects to your analytics, ad platforms, and CMS, then operates on a continuous loop of observation, hypothesis, action, measurement. Think of it as the marketing version of what CI/CD did for deployment — the machine runs the feedback loop faster than humans can.

**Perplexity's Personal Computer AI System** takes the "always-on" concept even more literally. It's a Mac Mini-based local system that runs Perplexity's AI 24/7 on your desk. Always listening, always processing, always ready. Your personal AI that doesn't live in a browser tab — it lives on your network, accumulating context about your work, your preferences, your patterns.

The privacy implications are significant — and I mean that in both directions. Having your AI run locally means your data never leaves your network. That's a massive advantage for anyone working with sensitive information. But "always on" also means "always monitoring," and the line between helpful assistant and surveillance system depends entirely on the implementation details Perplexity hasn't fully disclosed.

What these two announcements share is a bet that AI's next form factor isn't a chat window. It's a persistent presence. An always-available intelligence that works alongside you — or on your behalf — without you having to initiate every interaction.

If you'd rather have someone build AI-powered automation systems like these into your business workflows, I take on exactly these kinds of integration projects. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

I'm still figuring out how I feel about this direction. The productivity potential is real. The dependency risk is also real. Outsourcing your marketing strategy to an AI means you need to deeply trust both the AI's judgment and your ability to audit its decisions. Most businesses aren't ready for that level of trust yet.

But the tooling to build that trust is improving fast. Which brings us to two releases focused on giving developers more control over their AI tools.

## Stitch TypeScript SDK and Manis Desktop Agent: Developer Control Layer

Two developer-focused releases this week deserve attention even though they got less social media buzz than the bigger announcements.

**Stitch TypeScript SDK** is the official TypeScript SDK for design-to-development workflows. If you've used Stitch's platform, the SDK gives you programmatic access to the same design translation capabilities — pull design tokens, generate component code, sync design changes to your codebase, all from TypeScript.

Why this matters: the gap between design tools and code has been a source of friction for as long as both have existed. Designers create in Figma. Developers translate to CSS. Discrepancies multiply. Stitch's SDK automates the translation layer. For teams running continuous design-to-code pipelines, this removes a manual step that introduces errors every single time.

**Manis Desktop AI Agent** is positioned as a local, private alternative to cloud-based agent systems like OpenClaw. It runs entirely on your desktop — no API calls, no data leaving your machine. The trade-off is obvious: you need hardware powerful enough to run the underlying model locally, and the model you can run locally will be smaller than what's available via cloud APIs.

But for developers working on proprietary code, internal tools, or anything covered by strict data governance policies, Manis solves a real problem. Your AI assistant sees your code, plans modifications, and executes changes — all without any data touching an external server. That's a compliance guarantee no cloud AI can match.

The pattern connecting Stitch and Manis: developer tooling is moving toward giving builders more control over where their AI runs, how it connects to their workflow, and what data it can access. The era of "send everything to an API and hope for the best" is ending. Developers want AI that respects their infrastructure boundaries.

One more release from this week reinforces that theme — and it might be the most technically significant of everything we've covered.

## Moonshot's Attention Residual: The Architecture Nobody Is Talking About

Save this name: Attention Residual. It's a new attention mechanism from Moonshot AI, and I believe it's the most technically important announcement of the week — even though it got a fraction of the attention that the flashier releases received.

Here's the problem it solves. Standard transformer attention treats every previous token with roughly equal computational importance. The model attends to everything in its context — useful tokens, irrelevant tokens, noise. As context windows grow larger (hello, 1M tokens), this becomes increasingly wasteful. You're spending compute attending to context that doesn't matter for the current generation step.

Attention Residual introduces selectivity. The mechanism learns to identify which previous context is actually useful for the current prediction and allocates compute accordingly. Think of it as the model learning to skim — not reading every word with equal intensity, but focusing deeply on the parts that matter and glancing past the rest.

The results on Moonshot's 48B parameter model: 1.25x compute efficiency. That means you get the same output quality for 80% of the computational cost. Or — and this is the interpretation I find more exciting — you get better output quality for the same compute budget, because the model spends its compute on relevant context instead of distributing it uniformly across everything.

Why this matters beyond a single model: if Attention Residual (or architectures inspired by it) gets adopted broadly, it changes the economics of large context windows. Right now, 1M token contexts are expensive to serve. A 1.25x efficiency gain at the attention layer cascades through the entire inference pipeline. It makes large contexts cheaper, which makes them more accessible, which means more developers can build systems that use them.

The implications for multi-agent systems are particularly interesting. Agents coordinating through shared context windows are limited by how expensive that shared context is to maintain. More efficient attention means more affordable coordination, which means more complex multi-agent workflows become economically viable.

I'll be honest — I haven't had time to test Attention Residual directly. The paper dropped mid-week and the implementation isn't publicly available yet. I'm working from the published results and the architecture description. But the theoretical foundation is sound, and the efficiency gains they're reporting align with what you'd expect from a mechanism that replaces uniform attention with selective attention.

This is the kind of infrastructure improvement that doesn't make headlines but shapes the next two years of what's possible. The flashy releases get the tweets. The architectural innovations get the impact.

## What This Week Actually Means for Builders

Here's my honest read on the week, stripped of hype.

**If you build with AI daily:** The Claude 1M context window and Codex sub-agents are immediately useful. Update your workflows. Stop fragmenting context manually. Start experimenting with parallel agent execution. These aren't future promises — they're shipping features you can use today.

**If you're evaluating self-hosted models:** Mistral Small 2 and Nvidia's Neotron Ultra just changed the equation. The performance gap between open-source and proprietary narrowed significantly this week. Run your own benchmarks on your specific use cases, but don't assume proprietary models are automatically better anymore. For many production workloads, they're not.

**If you're a technical leader making architecture decisions:** The multi-agent pattern is converging across every major provider. If your current AI architecture is "one model, one prompt, one response," you're already behind the curve. Start prototyping agent-based workflows. The tools are ready. The models are capable. The only bottleneck is organizational willingness to rethink how AI fits into your systems.

**If you're watching the long game:** Pay attention to Attention Residual and similar architectural innovations. The current generation of foundation models is compute-bound. Architectural improvements that make inference more efficient will determine which context lengths, agent complexities, and model sizes become economically viable at scale. The company that solves efficient attention at 10M+ tokens wins the next round.

One thing I got wrong last month: I predicted the open-source vs. proprietary gap would take until late 2026 to close for agent-capable models. This week proved me wrong by about six months. Minimax M2.7, Mistral Small 2, and Neotron Ultra collectively moved that timeline forward in ways I didn't anticipate.

The pace isn't slowing down. If anything, the feedback loops between hardware improvements, architectural innovations, and model capabilities are accelerating. Each advance makes the next one easier.

## The Pattern I Can't Stop Thinking About

Twelve announcements in seven days. That's the surface-level observation. The deeper pattern is what keeps pulling at me.

Every major announcement this week pointed in the same direction: AI is becoming ambient. Not a tool you open. Not a chat window you type into. An intelligence woven into your IDE, your design tools, your marketing stack, your desktop — running continuously, acting autonomously, coordinating with other AI systems to handle complexity that no single agent could manage alone.

A year ago, the question was "how good is the AI?" Now the question is "how much of my workflow is the AI already handling without me noticing?" The shift from capability to integration happened faster than I expected. This week accelerated it further.

I started this overview trying to rank these twelve developments by importance. I can't. They're not twelve separate stories. They're twelve facets of the same story: AI development in 2026 is less about any single model or product and more about the ecosystem of agents, architectures, and infrastructure that makes autonomous AI work actually useful.

If you took anything from this breakdown, here's my ask: pick one announcement from this list that's relevant to your work. Not all twelve. One. Go test it this week. Build something small with it. The difference between reading about AI developments and experiencing them firsthand is the difference between watching someone swim and getting in the water.

The water is warm right now. And it's getting deeper fast.

## Frequently Asked Questions

### What is the biggest AI development in March 2026?

Claude's Opus 4.6 and Sonnet 4.6 reaching 1 million token context windows with 78.3% MRCR v2 accuracy is the most immediately impactful development for working developers. It eliminates context management as a bottleneck for the first time. For the full benchmark breakdown, see [my detailed analysis](/mejba.me/opus-4-6-million-token-context).

### Is Mistral Small 2 better than GPT-5.4 or Claude Opus 4.6?

Mistral Small 2 trails both on general reasoning benchmarks but competes effectively on agent and tool-calling tasks. Its real advantage is Apache 2.0 licensing and self-hosting capability — you own the deployment entirely. For teams with data governance requirements, it may be the better practical choice despite lower peak capability.

### What is Attention Residual and why does it matter?

Attention Residual is a new transformer attention mechanism from Moonshot AI that selectively attends to relevant context instead of processing all tokens equally. It achieves 1.25x compute efficiency on their 48B parameter model, which could make large context windows significantly cheaper to serve if the approach is adopted broadly.

### Can I run AI agents locally without cloud APIs in 2026?

Yes — several tools now support fully local AI agent workflows. Manis Desktop AI Agent runs entirely on your machine with no external API calls. Combined with open-source models like Mistral Small 2 or Minimax M2.7, you can build capable agent systems that never send data off your hardware.

### How do OpenAI Sub Agents for Codex compare to Claude Agent Teams?

Both implement the same core pattern: specialized sub-agents working in parallel on different aspects of a task, coordinating through shared context. OpenAI's implementation focuses on CLI-based development workflows while Claude's agent teams operate across broader task types. Token consumption is higher with both — roughly proportional to the number of parallel agents.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)