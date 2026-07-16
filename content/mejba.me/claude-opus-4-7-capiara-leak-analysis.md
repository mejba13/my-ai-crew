**BRAND:** mejba.me
**TITLE:** Claude Opus 4.7 Leaked: What Capiara Reveals
**META TITLE:** Claude Opus 4.7 Leaked: What the Capiara Leak Reveals
**SLUG:** claude-opus-4-7-capiara-leak-analysis
**PRIMARY KEYWORD:** Claude Opus 4.7 leak
**SECONDARY KEYWORDS:** Capiara Capybara tier, Anthropic Mythos roadmap, Claude model architecture
**META DESCRIPTION:** Anthropic's Claude Opus 4.7 leaked via npm and CMS errors. Here's what the Capiara codename, Capybara tier, and benchmark hints reveal about what's coming.
**TAGS:** AI Development, Claude Code, AI Models, Anthropic, News Analysis
**CONTENT CLUSTER:** AI Model Reviews
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly what the Opus 4.7 and Capiara leaks revealed, what's confirmed vs. speculative, and how to position their workflows for what Anthropic ships next.

---

I was scrolling through a GitHub discussion thread at 11 PM on a Monday when someone posted a screenshot that made me put down my coffee. It was a snippet from Claude Code's npm package — the actual source code, not a mock-up — showing version strings for models that don't exist yet. Claude Opus 4.7. Claude Sonnet 4.8. And a tier called Capybara that sat above everything Anthropic has ever shipped publicly.

Within hours, that screenshot was everywhere. And within days, a second leak — this time from Anthropic's own content management system — dumped nearly 3,000 unpublished documents into public view, including draft benchmark comparisons and internal architecture notes for something codenamed Capiara.

Two accidental leaks in five days. From a company whose entire brand is built on safety and careful control. The irony was so thick you could cut it with a `git revert`.

I've spent the past week piecing together what these leaks actually tell us — separating the confirmed facts from the speculation, the genuine signal from the noise. Because buried inside 500,000 lines of exposed source code and a pile of draft marketing documents is a surprisingly clear picture of where Anthropic is heading. And if you're building anything on Claude's API right now, you need to understand this before it drops.

## What Actually Leaked — And How

Two separate incidents. Same company. Same week. Different vulnerabilities. The timeline matters because it tells you something about the scale of what was exposed.

**Leak one: the npm packaging error (March 31, 2026).** A faulty update to Claude Code's npm package shipped with source files that should have been stripped before publication. Roughly 500,000 lines of code across 1,900 files went live. The community found it within minutes. Anthropic issued takedown requests, but the code had already been mirrored, forked, and analyzed by developers on three continents.

Inside that code: references to Opus 4.7 and Sonnet 4.8 as "forbidden version strings" in what appears to be an internal testing mode called Undercover Mode. The models weren't runnable from the leaked code — there were no weights, no endpoints, no way to actually use them. But the version identifiers confirmed they exist as internal builds. That's not speculation. It's string literals in production code.

**Leak two: the CMS misconfiguration (approximately April 1-2, 2026).** A configuration error in Anthropic's content management system made close to 3,000 unpublished assets publicly accessible. According to [Fortune's reporting](https://fortune.com/2026/03/31/anthropic-source-code-claude-code-data-leak-second-security-lapse-days-after-accidentally-revealing-mythos/), this came just days after the npm incident. The exposed documents included draft blog posts, benchmark comparisons, and architecture descriptions for a model generation called Mythos — with the tier name Capybara (also referenced as Capiara in some internal docs).

Anthropic's response was swift but minimal. They attributed the npm leak to a packaging error. They locked down the CMS. They issued takedowns. They did not confirm specific timelines, benchmarks, or release plans for any unreleased model. The company's official position as of April 9, 2026: Mythos exists, early access customers are testing it, no public launch date.

That's the confirmed foundation. Everything I'm about to discuss builds on what was found in those two data sets.

## Decoding the Naming Convention: Mythos, Capybara, Capiara, Opus 4.7

The naming situation confused almost every early analysis I read. Let me untangle it, because the distinction between these terms reveals how Anthropic thinks about its product roadmap.

**Mythos** is a generation name. Think of it like how Apple uses "iPhone 16" — it's the product family. Mythos represents the next generation of Claude models, succeeding the current Claude 4.x family.

**Capybara** is a tier name. Anthropic currently has three tiers: Haiku (fast and cheap), Sonnet (balanced), and Opus (powerful and expensive). Capybara would be a fourth tier — sitting above Opus. Larger, more capable, and significantly more expensive. This is the first time we've seen evidence that Anthropic is expanding beyond three tiers.

**Capiara** appears in some leaked documents as an alternate spelling or codename — possibly an early internal name before Capybara was formalized. Whether it's a typo, a codename for a specific Capybara variant, or a distinct internal project isn't clear from the leaked materials.

**Opus 4.7** is a version within the current generation's Opus tier. It would be the next Opus release after 4.6, following the pattern of Opus 4.5 (November 2025) and Opus 4.6 (February 2026).

Here's what that means in practice: Opus 4.7 and Capybara/Mythos are likely different things. Opus 4.7 is the next incremental Opus update — still within the Claude 4.x family. Capybara/Mythos is a new tier entirely, potentially representing a generational leap. The leaked code referenced both, which means Anthropic is working on at least two unreleased model upgrades simultaneously.

That's a significant resource allocation signal. Anthropic isn't just iterating on the current architecture — they're building a new category above it while continuing to improve the existing line. The two-track strategy mirrors what we've seen from [OpenAI with their o-series reasoning models](/blog/gpt-5-3-codex-first-look) running alongside GPT releases, but Anthropic's approach formalizes it as a permanent tier rather than a parallel product line.

## What the Benchmarks Suggest — With a Heavy Asterisk

I need to be careful here, because none of the benchmark numbers from the leaks have been independently verified. Anthropic hasn't confirmed them. The leaked documents are drafts — not published results. Treat everything in this section as "leaked claims," not proven performance.

With that caveat firmly in place: the numbers are remarkable.

The leaked draft documents claim Opus 4.7 will outperform Opus 4.6 by "large margins" on multi-step reasoning, complex code understanding, debugging, and planning tasks. No specific percentages were included in the documents that have been publicly analyzed, but the language is stronger than typical internal draft material. Anthropic engineers don't casually throw around "large margins" in documents they expect their boss to read.

For context on what Opus 4.6 already achieves: it scores [80.8% on SWE-bench Verified](https://www.mindstudio.ai/blog/gpt-54-vs-claude-opus-46-vs-gemini-31-pro-benchmarks), making it one of the top coding models available. It scored 65.4% on SE-bench (a different, harder software engineering benchmark). It sits at 91.3% on GPQA Diamond for scientific reasoning. These are frontier-class numbers.

If Opus 4.7 improves on these by "large margins," we're looking at a model that could push SWE-bench Verified past 85% — territory that was considered near-ceiling six months ago. On math and science reasoning, the leaked documents suggest Opus 4.7 will compete at or above Gemini 3's level, which scored 1501 ELO on LM Arena and showed a 41% improvement on ARC-AGI-2.

The coding claim is the one I find most credible, and here's why. Anthropic has been on a tear with coding benchmarks. Opus 4.6 leapfrogged GPT-4.1's 54.6% SE-bench score with its 65.4%. The [Mythos preview — which is the Capybara tier, not Opus 4.7 — already hit 93.9% on SWE-bench Verified](/blog/mythos-deepseek-v4-glm5-autonomy). If the Capybara tier is hitting 93.9%, it's entirely plausible that Opus 4.7 — a less powerful model from the same research pipeline — lands somewhere meaningfully above Opus 4.6 but below Capybara.

My rough prediction, and this is pure speculation based on the pattern: Opus 4.7 SWE-bench Verified in the 85-90% range, with SE-bench potentially cracking 75%. If those numbers hold, it would make Opus 4.7 the second most capable coding model Anthropic has ever tested — behind only the restricted Capybara/Mythos tier.

But here's the asterisk on the asterisk. Benchmarks are not real-world performance. I've [tested Opus 4.6 extensively](/blog/opus-4-6-hands-on-review) on actual coding tasks — building games, producing podcasts, generating presentations — and the real-world experience often diverges from benchmark scores. A model that scores 5% higher on SWE-bench might feel identical in daily use. Or it might be dramatically better at the specific tasks that benchmark doesn't measure well, like long-range planning or architectural decisions. Until I can actually use Opus 4.7, the benchmarks are interesting data points, not conclusions.

## The Architecture Under the Hood

The leaked documents describe Opus 4.7's architecture as a **dense decoder transformer** — no sparse mixture of experts (MoE). This is the same fundamental approach as Opus 4.6, and it's a deliberate philosophical choice that sets Claude apart from models like GLM-5.1 (which uses MoE with 754 billion parameters but only activates a fraction at inference).

Dense transformers use all their parameters for every token they process. MoE models route different inputs through different "expert" subnetworks, activating only a subset of total parameters. The trade-off is straightforward: dense models are more computationally expensive per token but tend to produce more consistent reasoning quality. MoE models are cheaper to run but can be uneven — brilliant on some inputs, mediocre on others.

Anthropic's bet on dense architecture says something about their priorities. They're optimizing for reliability and depth over inference cost. When you're charging $5 per million input tokens and $25 per million output tokens (Opus 4.6 pricing), you can afford to run a heavier model because the price point supports the compute. The premium pricing subsidizes the premium architecture.

The parameter count hasn't been officially disclosed for any Claude model — Anthropic has been more secretive about this than almost any other lab. Analyst estimates for Opus 4.6 land in the "hundreds of billions" range. Opus 4.7, according to the leaked drafts, is "even larger." Whether that means 10% larger or 3x larger is unknown.

What I find more interesting than the raw parameter count is the training data description. The leaks reference training on "trillions of tokens from internet text, code, books, and licensed datasets." The "licensed datasets" part is significant — it suggests Anthropic has expanded its data licensing agreements, potentially giving Opus 4.7 access to high-quality proprietary text and code that competitors training primarily on web scrapes don't have. Data quality is often more impactful than model size, and Anthropic has been aggressive about securing exclusive data partnerships.

The context window is expected to maintain or exceed the 1 million token capacity introduced with Opus 4.6. Given that both GPT-4.1 and Gemini 3 already support 1 million tokens, dropping below that threshold would be a competitive regression Anthropic can't afford.

## The Release Cadence Tells a Story

Pull back from the technical details and look at the release timeline:

- **Opus 4.5:** November 2025
- **Opus 4.6:** February 2026 (3 months later)
- **Opus 4.7 (expected):** Mid to late 2026 (4-6 months later)

The cadence is accelerating in capability while slightly extending in timeline. Opus 4.5 to 4.6 was a 3-month gap and introduced the 1 million token context window — a massive architectural change. If Opus 4.7 takes 4-6 months, it's because the scope of the upgrade is larger, not because Anthropic is slowing down.

Anthropic's CEO has confirmed early testing with select customers and described the results as a "step change" in reasoning and coding performance. That language — step change — is specific and deliberate. It means the improvement isn't incremental. Something fundamental shifted in the training or architecture that produces qualitatively different outputs.

I've seen "step change" language from AI labs before. Sometimes it's marketing. But Anthropic has historically been conservative with claims — they're more likely to underpromise and overdeliver than the reverse. When Dario Amodei says "step change," I take it seriously.

The controlled rollout pattern also tells a story. Opus 4.6 launched with broad API access on day one. The leaked documents suggest Opus 4.7 will follow a more guarded release — early access for security teams and select partners first, broader API availability later. This mirrors what happened with Mythos/Capybara, which as of April 2026 is still restricted to a small group of early access customers chosen by Anthropic.

Why the caution? Two possibilities. First, the model is powerful enough that Anthropic wants security researchers to stress-test it before public access — the same rationale behind [Project Glasswing's restricted deployment of Mythos](/blog/mythos-deepseek-v4-glm5-autonomy). Second, Anthropic is managing compute capacity. A larger, denser model requires more GPU time per request. Controlling access lets them scale infrastructure before opening the floodgates.

My money is on both factors simultaneously.

## Safety at ASL-3: What It Actually Means for You

Anthropic operates its frontier models under [AI Safety Level 3 (ASL-3)](https://www.anthropic.com/news/activating-asl3-protections) — their highest internal safety standard. This framework was activated alongside the Claude 4 family and covers specific risk categories: chemical, biological, radiological, and nuclear (CBRN) weapons development; autonomous cyber operations; and model self-replication.

For Opus 4.7, ASL-3 means several concrete things:

**Increased red-team testing before release.** Every frontier Claude model goes through adversarial testing where teams actively try to make it do dangerous things — synthesize bioweapons instructions, write exploit code for critical infrastructure, plan autonomous actions outside its sandbox. Opus 4.6 passed these tests with some edge-case concerns flagged but no major novel risks identified. Opus 4.7 will face stricter scrutiny because it's more capable.

**Controlled deployment tiers.** Not everyone gets access at the same time. Security teams and trusted partners first. Broader developer access later. Consumer access last. This is a deliberate pattern Anthropic has been refining — and the Opus 4.7 rollout is expected to formalize it more than any previous release.

**Ongoing monitoring post-deployment.** ASL-3 doesn't just govern the launch. Anthropic monitors for emergent behaviors after deployment — patterns of misuse, unexpected capability discoveries, or alignment drift that only appears at scale. They've committed to publishing risk reports, like the one they released for [Mythos in April 2026](https://www.anthropic.com/claude-mythos-preview-risk-report).

In February 2026, Anthropic updated their Responsible Scaling Policy to version 3.0, which requires publishing Frontier Safety Roadmaps with detailed safety goals and Risk Reports that quantify risk across all deployed models. Opus 4.7 will be the first standard-tier Opus release under these expanded disclosure requirements.

Here's my honest take on what ASL-3 means practically for developers. If you're building standard applications — chatbots, code assistants, content tools, data analysis pipelines — ASL-3 won't affect your day-to-day experience with the model. The safety measures are primarily aimed at preventing catastrophic misuse, not at limiting routine developer workflows. You won't notice the guardrails in normal operation.

Where you might notice them: if you're building security tools, autonomous agents with network access, or systems that interact with physical infrastructure. The boundaries on what Claude will and won't do in those domains are tighter than competitors, and they'll get tighter with Opus 4.7. Whether that's a feature or a limitation depends entirely on what you're building.

## Where Opus 4.7 Fits in the Competitive War

The AI model landscape in April 2026 is the most crowded and competitive it's ever been. Opus 4.7 isn't releasing into a vacuum — it's landing in the middle of a four-way fight.

**GPT-5.4** from OpenAI leads on several coding benchmarks, particularly tasks involving recursion, error handling, and edge-case logic. It scored 83% on GDPval (matching industry professionals across 44 occupations) and 75% on OSWorld, surpassing human performance on desktop tasks. OpenAI's ecosystem advantage — ChatGPT's consumer base, widespread API integration, partnerships with Microsoft — gives GPT-5.4 distribution that no other model matches.

**Gemini 3.1 Pro** from Google wins on abstract reasoning (77.1% on ARC-AGI-2, more than double Gemini 3 Pro's score) and scientific reasoning (94.3% on GPQA Diamond, the highest of any model). Its multimodal capabilities — video, audio, spatial reasoning — occupy territory Claude doesn't compete in. And at $2 input / $12 output per million tokens, it significantly undercuts Anthropic's pricing.

**Meta's Llama 3 (405B)** offers something none of the closed models can: complete data sovereignty. Open-source, self-hostable, fine-tunable. For organizations that can't send data to external APIs — healthcare, defense, financial services — Llama is often the only option regardless of benchmark scores.

So where does Opus 4.7 fit?

Claude's moat has always been the intersection of three things: deep multi-step reasoning, complex software engineering, and massive document comprehension. If I need an AI to read a 200,000-token codebase and suggest an architectural refactor, Claude is my first call. If I need it to chain together a debugging session across twelve files with interdependent logic, Claude outperforms everything else I've tested.

Opus 4.7 appears designed to widen that specific moat rather than chase competitors into their strengths. The leaked benchmarks don't mention video understanding, audio processing, or spatial reasoning — areas where Gemini dominates. They don't highlight consumer-facing features or ecosystem integrations — OpenAI's territory. They focus relentlessly on reasoning depth, code quality, and planning capability.

This is a deliberate strategic choice, and I think it's the right one. In a market where every lab is trying to be everything to everyone, Anthropic is betting that being definitively the best at complex analytical and engineering tasks is enough to build a massive business. Given that their customers are overwhelmingly developers and enterprises — not consumers — that bet seems well-calibrated.

The wildcard is pricing. Opus 4.7 is expected to cost the same as or more than Opus 4.6's $5/$25 per million tokens. When Gemini 3.1 Pro offers competitive coding performance at less than half the price, the value proposition depends entirely on whether Opus 4.7's quality advantage justifies the premium. For my workflow — complex agentic coding, multi-file refactoring, long-context analysis — it absolutely does. I've lost more money debugging bad AI output from cheaper models than I ever spent on Claude tokens.

But I acknowledge that's not everyone's calculus. If you're running high-volume, lower-complexity tasks, the pricing gap matters.

## New API Capabilities: Chyros and Autodream

Buried in the leaked CMS documents were references to two new API capabilities codenamed **Chyros** and **Autodream**. These weren't part of the npm code leak — they appeared in what looks like internal product roadmap documents.

Details are sparse, but the fragments that have been analyzed suggest:

**Chyros** appears to involve advanced agent primitives — tools for building AI agents that can delegate subtasks, manage their own execution state, and operate persistently across sessions. If this is what it sounds like, it's a direct response to the booming agent ecosystem. Right now, building persistent Claude agents requires stitching together conversation management, tool calling, and state tracking yourself. Chyros could provide that infrastructure natively.

I've been building [agent swarm architectures with Claude](/blog/claude-code-agent-swarm-architecture) for months, and the biggest pain point is always state management between sessions. If Anthropic ships native persistent memory and task delegation through the API, it would collapse weeks of custom infrastructure into API calls. That alone would justify upgrading to Opus 4.7 for any serious agent builder.

**Autodream** is even more cryptic. The name suggests something related to autonomous operation or planning — possibly a capability where the model can generate and refine plans before executing them, similar to extended thinking but applied to multi-step agent workflows rather than single-turn reasoning.

I'm speculating heavily here. The leaked fragments don't give enough to draw firm conclusions. But the direction is consistent with everything else we're seeing: Anthropic is building toward AI systems that can operate with less moment-to-moment human supervision. Mythos demonstrated that capability in a dramatic way. Chyros and Autodream might be the developer-facing tools that make it accessible and controllable.

## What This Means for Developers Building on Claude Right Now

Here's where I stop analyzing leaks and start talking about what you should actually do with this information.

**Don't wait. Don't freeze.** The worst response to an upcoming model release is to stop building. Opus 4.6 is excellent. It's not going to get worse when 4.7 arrives. Build on 4.6 now, and design your system to swap models with a single configuration change. If your code is hardcoded to a specific model version, that's a problem regardless of what ships next.

**Design for the context window jump.** Opus 4.6's 1 million token context window was a game-changer for how I build. Opus 4.7 will maintain or exceed it. If you haven't rebuilt your workflows around long-context capabilities — feeding entire codebases, full document sets, complete conversation histories — you're leaving the most valuable capability on the table. Start now. The skills transfer directly to whatever comes next.

**Budget for premium pricing.** Nothing in the leaks suggests Opus 4.7 will be cheaper than 4.6. If anything, the "similar or higher" pricing signals from the draft documents suggest a modest increase. Build your cost models around $5-7 per million input tokens and $25-30 per million output tokens. If the actual pricing comes in lower, you'll have margin. If it comes in higher, you won't be caught off guard.

**Watch for the Capybara tier as a separate product.** This is the sleeper insight from the leaks. If Anthropic ships a fourth tier above Opus, it means there will be tasks where even Opus isn't the right tool. Capybara/Mythos pricing will be dramatically higher — possibly $15-25 per million input tokens based on the token costs reported for Mythos preview access. For most use cases, Opus remains the sweet spot. But if you're building systems that need the absolute ceiling of reasoning capability — security auditing, complex legal analysis, scientific research — start thinking about how a Capybara tier fits into your architecture.

**Get serious about safety boundaries.** The controlled rollout pattern isn't going away. Each successive Claude release will have more guardrails, more tiered access, more monitoring. If your application depends on pushing the edges of what Claude is willing to do — autonomous network access, code execution in production environments, security testing — build those boundaries explicitly into your system design. Don't rely on the model being willing. Build redundancy around the restrictions.

If you'd rather have someone build this infrastructure from scratch — the agent architecture, the model-switching layer, the cost optimization pipeline — I take on exactly these kinds of engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Uncomfortable Question About Anthropic's Leak Problem

I can't write about these leaks without addressing the elephant in the room. Anthropic — the company that positions itself as the responsible, safety-first AI lab — had two major data exposures in the same week. An npm packaging error that shipped 500,000 lines of source code. A CMS misconfiguration that exposed 3,000 internal documents.

These aren't sophisticated attacks. They're operational mistakes. The kind of mistakes that happen when shipping velocity outpaces security hygiene.

I don't think this undermines Anthropic's safety credentials on the AI alignment side. Model safety — preventing Claude from helping someone build a bioweapon — is a fundamentally different discipline from operational security — preventing your own source code from leaking via npm. Different teams, different processes, different failure modes. The people building RLHF alignment systems aren't the same people configuring CMS permissions.

But the optics are brutal. When your brand promise is "we build AI responsibly and carefully," two back-to-back leaks shake confidence in the "carefully" part. Especially when the leaked content includes details about models that could find zero-day vulnerabilities autonomously. The disconnect between "we're so security-conscious we won't release Mythos publicly" and "we accidentally published our entire source code through a packaging error" is jarring.

Anthropic needs to address this gap — not just with incident responses and takedown notices, but with a visible improvement in operational security practices. The technical talent is clearly there. The process discipline, on this evidence, needs work.

For what it's worth, every major AI lab has had similar incidents. OpenAI's internal Slack was compromised in 2024. Google had Gemini-related leaks through their bug tracker. The speed of AI development creates organizational pressure that routinely defeats standard security practices. It's not an excuse — it's context.

## What I'm Watching Next

Three signals will tell me whether the Opus 4.7 leak paints an accurate picture:

**Signal one: early access announcements.** When Anthropic starts giving selected partners access to Opus 4.7 — and they will, because they need the testing data — the NDA-compliant hints will start flowing. Watch for developers suddenly talking about "improved reasoning" or "better long-context performance" without naming the model. That's the tell.

**Signal two: pricing changes to the current lineup.** When Anthropic is about to add a new tier, they typically adjust the existing tiers' pricing. If Opus 4.6 gets a price cut in the next 2-3 months, it likely means Opus 4.7 (or Capybara) is about to take the premium position. Haiku and Sonnet price adjustments would signal the same thing.

**Signal three: the Responsible Scaling Policy report.** Under RSP 3.0, Anthropic has committed to publishing risk reports for frontier models. When the Opus 4.7 risk report drops — and it will drop before or at launch — the gap between the leaked draft benchmarks and the real numbers will become clear.

My prediction: Opus 4.7 arrives between June and August 2026. The coding improvements will be real and significant. The reasoning gains will be meaningful but less dramatic than the leaks suggest. The Capybara tier will launch separately, later, and at a price point that makes most developers blink. And the AI race will keep accelerating, because that's the only direction it knows.

The leaked code showed me version strings. The leaked documents showed me benchmark hints and architecture notes. But the thing that actually told me the most about Opus 4.7? It was the word "step change" from Anthropic's CEO. Because when a company known for understatement starts using language that strong, the model behind it is usually better than the leaks suggest.

## Frequently Asked Questions

### When will Claude Opus 4.7 be released?

Anthropic has not confirmed a public release date for Claude Opus 4.7 as of April 2026. Based on the 3-4 month update cadence (Opus 4.5 in November 2025, Opus 4.6 in February 2026), a mid-to-late 2026 launch is most likely. Early access for select partners is already underway.

### What is the difference between Claude Mythos and Opus 4.7?

Claude Mythos is a generation/product name, while Opus 4.7 is a version within the current Claude 4.x Opus tier. Mythos uses the Capybara tier — a new fourth tier above Opus. Opus 4.7 and Capybara/Mythos are separate models at different capability and price levels. For the full breakdown, see the naming convention section above.

### How much will Claude Opus 4.7 cost?

Leaked documents suggest pricing similar to or higher than Opus 4.6's $5 per million input tokens and $25 per million output tokens. Optimizations like prompt caching and batch execution may reduce effective costs. The Capybara/Mythos tier will be priced significantly higher.

### Is Claude Opus 4.7 better than GPT-5.4 for coding?

Leaked benchmarks suggest Opus 4.7 will outperform GPT-4.1 significantly and compete closely with GPT-5.4 on complex software engineering tasks. Claude's traditional strengths — multi-step reasoning, long-context code understanding, and debugging — are expected to see the largest improvements. Independent benchmarks will determine the final standings.

### What was exposed in the Claude Code source leak?

Approximately 500,000 lines of code across 1,900 files were exposed through an npm packaging error on March 31, 2026. The code revealed references to Opus 4.7 and Sonnet 4.8 as internal version strings, along with testing infrastructure details. No model weights or API keys were exposed.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
