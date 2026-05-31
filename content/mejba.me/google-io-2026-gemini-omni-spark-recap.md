**BRAND:** mejba.me
**TITLE:** Google IO 2026 Recap: Gemini Omni, Spark, Agent Pivot
**META TITLE:** Google IO 2026 Recap: Gemini Omni, Spark, Antigravity 2
**SLUG:** google-io-2026-gemini-omni-spark-recap
**PRIMARY KEYWORD:** Google IO 2026 recap
**META DESCRIPTION:** My field notes from Google IO 2026 — Gemini Omni, 3.5 Flash, Spark at $100, Antigravity 2.0, Audio Glasses, and what I'd actually test this week.
**TAGS:** Google IO 2026, Gemini AI, AI Agents, Google DeepMind, Event Recap

---

# Google IO 2026 Recap: The Year Google Stopped Selling Chatbots

I watched the IO 2026 keynote on a second monitor with a fresh terminal open on the first one, half-expecting to ignore most of it.

That's not arrogance. That's just what the last two years of AI keynotes have trained me to expect. Marketing-grade demos, a parade of model names that all blur into each other, a feature reel that won't ship until "later this year." I had the volume low. I was working on something else. And then about eighteen minutes in, a DeepMind engineer named Varun Mohan stood up and casually said his team had built a working operating system from scratch in twelve hours using Gemini 3.5 Flash and Antigravity — 93 subagents, 15,000 model requests, 2.6 billion tokens processed, under $1,000 in API credits — and then he loaded Doom onto it and started playing.

I muted my music. I closed my other tab. I picked up a notebook.

That moment told me everything about Google IO 2026 in a way the slide decks never could. Google didn't show up to Mountain View on May 19, 2026 to sell another chatbot. They showed up to sell agents — and to do it, they restructured the entire AI Ultra pricing stack, shipped a brand new "any-to-any" multimodal model, dropped a desktop application designed entirely around multi-agent orchestration, previewed a 24/7 personal AI that lives on a Google Cloud VM, and quietly cut the top-tier plan from $250 to $200 per month so they could squeeze in a new $100 tier underneath it.

If you're a builder, this is the most consequential keynote Google has run since the original Gemini launch. I've spent the days since digging through every announcement, cross-referencing the technical claims against independent reporting, and mapping out which pieces I'd actually deploy this week versus which are demo-ware. Here's the field report — what was real, what was theater, and what I think it means for anyone shipping software with AI in 2026.

## The Frame Shift Nobody's Talking About

Before I get into specific announcements, you have to understand the framing because every product reveal flows out of it.

For the last three years, the AI industry's center of gravity has been the chatbot. You type a question. The model answers. You evaluate the answer. Pricing was structured around prompts and tokens. The user experience was structured around messages. The benchmarks were structured around single-turn quality.

Google IO 2026 was the moment that frame broke publicly.

The pricing change is the tell. Google's Gemini app is moving away from daily prompt limits and toward a "compute-used" model, where a simple text reply consumes a tiny fraction of your monthly allowance and a complex video edit or coding agent run consumes a much larger one. That's not a chatbot pricing model. That's a workload pricing model. It only makes sense if the company expects the average paying user to start running things that are closer to background jobs than conversations.

And every other announcement reinforces it. Gemini Spark is described as "a 24/7 AI agent" that lives on your behalf — not a model you talk to, but one that runs while you sleep. Antigravity 2.0 is described as an "agent-first" desktop application for orchestrating multi-agent work in parallel. Search is getting "autonomous Gemini-powered agents capable of monitoring information continuously and taking actions on behalf of users." Universal Cart is a shopping *agent*, not a shopping search.

You'll hear me come back to this lens repeatedly because it's the only one that makes the keynote make sense. Once you see it, every announcement clicks into place. So let me walk through what they actually shipped — and what I'd actually test.

## Gemini Omni: One Model, Every Modality, One Watermark

The biggest *model* announcement of the day wasn't 3.5 Flash. It was Gemini Omni, Google's new "any-to-any" multimodal model that takes any combination of text, image, audio, and video as input and produces any combination as output.

Omni Flash — the first public model built on the Omni framework — is the one Google is actually shipping. According to the reporting, it can generate short AI video clips from text prompts, animate still images, edit generated scenes conversationally, and respond to combined text, audio, and image inputs in real time. It's described publicly as the next chapter of the work that produced Nano Banana (the image edit/generate model from last year) and Genie (Google's generative interactive world model).

Here's what made me sit up about this announcement: Google didn't ship it quietly into a beta. They shipped it alongside the most aggressive content provenance push I've seen from any major AI lab.

Every video generated by Omni carries Google's SynthID digital watermark. SynthID has now marked more than 100 billion AI-generated images and videos, and as of IO 2026, NVIDIA, OpenAI, ElevenLabs, and Kakao are all adopting the standard. C2PA Content Credentials are being expanded across Google's generative tools, and there's now an AI Content Detection API on Google Agent Platform that lets businesses identify AI-generated content from Google's models and from other popular models.

Read that paragraph again. OpenAI is adopting Google's watermark standard. That alone is the most significant industry alignment story since the original GPT API launched, and it barely got mentioned in the recap coverage because it's not a flashy demo. It's an admission from the entire frontier-model industry that an unmarked synthetic-media internet is unworkable, and they're collectively conceding ground to a shared technical standard to head off regulators.

If you build anything that consumes user-generated content — moderation, ad platforms, journalism tools, social media, brand monitoring — this is the most important developer-facing announcement from the entire keynote. The C2PA + SynthID stack is now broad enough that "is this AI-generated?" is becoming a real, queryable signal in your data pipeline. I covered the [AI training data crisis](/content/mejba.me/ai-training-data-crisis-simula-euphan-hermes.md) earlier this year and the open question I left unresolved was provenance. Omni's launch is the first time I've seen a credible answer.

What I'd test this week: pipe a chunk of user-submitted media through the AI Content Detection API and see what the false-positive and false-negative rates actually look like in the wild. Don't trust the demo numbers. Test it on your data.

## Gemini 3.5 Flash: The Model Built for Agents

Gemini 3.5 Flash is the model that produced the Doom-on-a-new-OS moment, and the technical story behind it is more interesting than the demo.

Per Google's own claims and independent reporting, Gemini 3.5 Flash:

- Outperforms Gemini 3.1 Pro across nearly every benchmark while running roughly four times faster than other frontier models on output tokens per second
- Scores 76.2% on Terminal-bench 2.1 (coding evaluation)
- Scores 1656 on GDPval-AA (real-world agentic benchmark)
- Is co-optimized with the Antigravity harness — meaning the model and the multi-agent runtime were trained and tuned together, not bolted together at release

That last point is the one most coverage missed. When you train a frontier model to be good at agentic work, and you simultaneously train the harness it runs in to surface the right tool calls and context windows at the right moments, you get behavior that isn't reproducible by just slotting in a different model. This is the same architectural insight Anthropic has been pursuing with Claude Code — the model and the runtime are not independent products — and it's a big part of why I've been [running Claude Code as my daily driver](/content/mejba.me/anthropic-long-running-agent-harness.md) for the past year. Google has now publicly committed to the same philosophy.

The 4x output speed claim deserves its own moment. If Gemini 3.5 Flash actually delivers four times the tokens-per-second of other frontier models at comparable quality, the math for agentic workflows changes entirely. A multi-step agent doing 15-20 tool calls per task isn't bottlenecked by reasoning quality past a certain point — it's bottlenecked by latency. Cut latency in a meaningful way and the agent can recover from mistakes, replan, and re-execute within the same wall-clock budget that previously fit only the first attempt. That's a different ceiling.

But here's where I'm going to be honest about the limits of one-day reporting. The Terminal-bench 2.1 and GDPval-AA scores look strong on paper, but I haven't put 3.5 Flash through my personal coding harness yet. When I [stress-tested Gemini 3 Deepthink](/content/mejba.me/gemini-3-deepthink-tested.md) earlier this year, the published benchmarks held up reasonably well, but the failure modes weren't visible until I threw real codebase problems at it. So treat the headline numbers as directional, not gospel. I'll have a separate full review after I've run my own evals.

What I'd test this week: take a real agentic workflow you currently run on Claude or GPT and re-run it on Gemini 3.5 Flash through the Antigravity harness. Pay specific attention to recovery behavior on tool-call failures, not just first-attempt success. That's where co-optimization usually shows up.

## Antigravity 2.0: The Standalone Agent Platform

I wrote about the original [Anti-Gravity IDE](/content/mejba.me/anti-gravity-ide-ai-agents.md) a few months ago and walked through how I shipped a full-stack finance app inside it in 47 minutes. That product was an IDE — an editor wrapped around AI agents.

Antigravity 2.0, announced at IO 2026, is something different. It's a standalone desktop application designed fully around an agent-optimized experience. Per the developer highlights from Google, it ships with a CLI, an SDK, managed execution, and enterprise support. Developers can orchestrate multiple agents in parallel and execute tasks across long-horizon workflows.

The structural shift from 1.x to 2.0 is the part that matters. The original Anti-Gravity put agents inside an editor. The new version inverts the relationship — the agent platform is the primary surface, and the editor is just one of the tools an agent can use. That's a meaningfully different design philosophy.

The Doom-on-a-new-OS demo was the proof point. 93 subagents working in parallel, 15,000 model requests, 2.6 billion tokens, 12 hours of wall time, under $1,000 in API spend. If you've ever tried to orchestrate that many concurrent agents by hand, you know the failure modes — agents stepping on each other's filesystem changes, deadlocked tool calls, context windows exploding from cross-talk. The fact that Google demoed it without visible chaos suggests the orchestration layer is doing real work, not just spawning subprocesses and hoping.

The CLI and SDK matter even more than the desktop app for serious builders. A CLI is what you bolt into CI. A CLI is what you script. A CLI is what you run on a server overnight. A desktop app is what you show to executives. Antigravity 2.0 having both means Google is serious about the platform sticking around as production infrastructure, not just as a launch-week demo.

I've spent the last year building most of my agentic work inside Claude Code and routing it through Anthropic's Agent SDK. Antigravity 2.0 is the first competing platform I've seen that looks structurally ready to host real production workloads — not because the marketing says so, but because the shape of the product (CLI + SDK + managed execution + enterprise support) is the shape you build when you're expecting other people to run your platform in production.

What I'd test this week: run the same agent task in Antigravity 2.0 and Claude Code side by side. Don't just measure quality — measure failure recovery, observability, and what the trace looks like when an agent goes off the rails. That's where production-readiness lives.

## Gemini Spark: The $100 Bet on Personal Agents

Here's the announcement that triggered the most heated debate in my group chats. Gemini Spark.

Spark is, in Google's own words, a 24/7 AI agent that lives on Google Cloud VMs and runs continuously on your behalf. It integrates with MCP (Model Context Protocol — the Anthropic-originated standard that's quietly become the industry default for tool calling). It will have a Chrome integration coming this summer. And it's the headline feature behind a major restructuring of the AI Ultra pricing tier.

The pricing math:

- The previous top Ultra plan was $250 per month. That tier still exists but is now $200 per month, with higher usage limits and more storage.
- A new $100 per month Ultra tier was introduced under it. The $100 plan includes 5x higher usage limits in the Gemini app compared to the $20 AI Pro tier, 20 terabytes of cloud storage, YouTube Premium, and beta access to Gemini Spark for US subscribers.
- Spark itself is rolling out to trusted testers in the week after the keynote, and to Google AI Ultra subscribers in the US the week after that as a beta.

Set the price changes aside for a second. The interesting question is what Spark actually is at the architectural level — because if it's what Google is implying, it's a different category of product than anything ChatGPT or Claude currently ships.

Most current "AI agents" run in response to a user prompt. You ask, it acts, it returns. The session is bounded by the conversation. Even Claude's projects and ChatGPT's GPTs are still fundamentally request-response — they hold context across sessions, but they don't run when you're not looking at them.

Spark runs on a VM. It has a continuous existence. It can monitor things, take actions, and check in with you based on its own scheduling — not because you opened the app, but because the world changed and it noticed.

If that's actually how it works, the unlock is substantial. The use cases are obvious — flight price tracking, restock alerts, calendar babysitting, email triage, monitoring a project board, watching a competitor's pricing page — but they're obvious in the same way that "having a phone in your pocket" was obvious in 2007. The fact that you can list them doesn't mean we know how the product reshapes daily behavior.

Here's where I'm skeptical. The $100 entry price puts Spark firmly in the "power user" tier — that's 5x the price of AI Pro and well above what most consumer SaaS lives at. For a product that needs to demonstrate value across many ambient tasks, charging $100/month before anyone knows whether it works is bold. The drop from $250 to $200 on the top tier softens it (and signals real competitive pressure from Anthropic and OpenAI's Pro plans), but Spark itself is gated behind a price most people won't pay until the case is overwhelmingly clear.

I'll be testing Spark the week it lands in the US beta. The specific question I'm bringing to it is whether the 24/7 framing is real product behavior or marketing language for "we kept the context window between sessions." There's a difference. The first is a new category. The second is a chatbot with better memory.

If you've been following the broader pricing war, you'll recognize this as the same dynamic I covered in my piece on [AI subscription commoditization](/content/mejba.me/ai-subscription-commoditization-application-layer.md) — the application layer is where the money lives, and the model labs are racing to capture it before the application layer captures them. Spark is Google's most explicit move in that race.

## Docs Live, Ask Maps, Ask YouTube: The Workspace Pivot

The consumer surface announcements got less keynote stage time but they're the ones that will reach the most people. Three matter most.

**Docs Live** is voice-driven Google Docs editing — you can tell Docs to move sections, format text bold or italic, and restructure documents using voice commands. It's rolling out to Android and iOS this summer with Google AI Pro and Ultra in English globally. The framing in the keynote emphasized accessibility — and the use case for users with motor impairments or visual impairments is genuinely meaningful — but the broader unlock is that voice editing is finally accurate enough to be a real productivity surface, not a gimmick. Apple has been trying to ship this for a decade. Google is shipping it because the underlying speech-to-intent model finally crossed the quality threshold.

**Ask Maps** turns Google Maps into a conversational search surface. You can ask questions about places the way you'd ask a local — not just "find me coffee near here" but "find me a quiet coffee shop with reliable WiFi and outdoor seating where I can take a video call." Same trick as ChatGPT search, but with Google's mapping data underneath, which is a meaningful moat.

**Ask YouTube** lets you query video content conversationally. The killer use case here isn't watching videos differently — it's research. I've done this manually for years with a custom pipeline that pulls transcripts and runs them through Claude. Ask YouTube does it natively. The implication for content creators is significant: discoverability now flows through conversational query, not just search-bar keywords, which means how you structure your video content (chapters, transcripts, on-screen explanations) directly affects whether AI surfaces it.

All three of these features sit downstream of one architectural fact: Google's training data advantage on maps, video, and document collaboration is enormous, and conversational AI is finally the right interface to monetize that advantage at scale. I covered the [Google framework for agentic AI transformation](/content/mejba.me/agentic-ai-transformation-google-framework.md) back in February, and the through-line from that piece to IO 2026 is the same — Google's edge isn't model quality, it's the data graph the model gets to sit on top of.

## Intelligent Search, Universal Cart, and the Agent-in-the-SERP

This is the announcement bundle that will actually reshape SEO and e-commerce, and it deserves a lot more attention than it's getting.

Google Search is being redesigned around a multimodal search box (text, image, video, voice), 24/7 AI search agents that monitor topics and notify you of changes, and — the part that genuinely surprised me — agentic coding inside search results that can generate dynamic UIs and widgets on demand. That's right. The search result page itself becomes a runtime that can spawn small interactive applications based on your query.

If you searched "compare these three running shoes by midsole drop" today, you'd get articles. In the new Search, you'd get a comparison widget that pulled live data and rendered as a small interactive table inside the SERP. Generated by an agent. At query time.

This is going to live globally and for free starting summer 2026. That timeline matters because it means the SEO landscape changes within months, not years. I've been writing about this shift in pieces of dribs and drabs — [generative engine optimization](/content/mejba.me/automate-seo-checks-with-claude-code-routines.md), passage-level citability, the death of the listicle — and IO 2026 is the moment it becomes concrete. The SERP is no longer a list of links. It's an agent runtime.

Universal Cart is the e-commerce companion. A Gemini-powered shopping cart that operates across Search, YouTube, and Gmail simultaneously. It finds deals, tracks price history, alerts on restocks, and — the genuinely useful part — flags incompatible product combinations. Try to add a motherboard and a processor that don't share a socket and Universal Cart notices.

US rollout is summer 2026. If you sell anything online, the implication is the same one as for content publishers: your product surface is no longer a website that people visit. It's a structured data feed that an agent reads on the user's behalf. Schema, structured product data, real-time inventory, and price transparency suddenly become the parts that determine whether an agent recommends you. Conversion optimization stops being about your landing page and starts being about your data layer.

I covered a version of this thesis in my piece on [AI agents reshaping work](/content/mejba.me/ai-tools-agents-reshaping-work.md), but Universal Cart makes it concrete in retail. If you run an e-commerce shop and you don't have clean structured product data by Q3 2026, you'll be invisible to a meaningful share of buying intent.

## Audio Glasses, Gemini App Redesign, and the Workspace Layer

Google saved the hardware reveal for the tail end of the keynote, which tells you something about how confident they are in software pulling the demand. The intelligent eyewear announcement is real but modest: audio glasses with onboard speakers and cameras, made by Samsung and Qualcomm, with designs from Gentle Monster and Warby Parker, shipping fall 2026 and working on both Android and iOS.

These are not Meta's Ray-Bans with a screen. These are voice-first, hands-free Gemini access, with cameras that can capture and pass context to the assistant. The demos showed Gemini navigating to places the user had visited before and ordering items through integrated apps while remembering user preferences. Wrist integration with Pixel Watch is included.

I'm not a glasses person, and I'm skeptical that audio-only AI eyewear becomes a mass product. But the strategic logic is correct — Google needs an ambient hardware surface for Gemini before Apple ships something competitive, and the partnership structure (Samsung + Qualcomm + fashion brands) is how you turn a tech product into something normal people will actually wear. Fall 2026 is when we'll know if the consumer market actually responds.

The Gemini app itself is getting a redesign — Google is calling the new design language "Neural Expressive" — and a new Google Pix tool for image editing inside Workspace. Flow Music is shipping as an audio generation surface. None of these are individually load-bearing, but they collectively say something about Google's commitment to making Gemini the daily-use surface across consumer products, not just an API. I traced some of this thread in my [NotebookLM and Gemini app integration](/content/mejba.me/notebooklm-gemini-app-integration.md) piece, and the pattern continues — Google is treating Gemini as the operating layer for everything they ship, not as a feature in any one product.

## Code Mender, SynthID Expansion, and the Quiet Safety Story

I want to spend a paragraph on the safety announcements because they got buried under the consumer reveals and they shouldn't be.

Code Mender is the part that matters most for builders. It's a DeepMind AI agent that automatically detects, patches, and rewrites vulnerable code. It uses a debugger, source code browser, fuzzing, and theorem provers to find root causes, then autonomously generates and validates patches against regressions and style guidelines before surfacing them for human review. In the six months leading up to the IO announcement, Code Mender's team upstreamed 72 security fixes to open source projects, including codebases as large as 4.5 million lines.

Read that number again. 72 real security patches, on real open source, validated by humans, accepted upstream. That's not a benchmark. That's a deployed agent doing security engineering at production scale.

The code APIs for Code Mender are in tester preview as of IO. If you maintain a meaningful codebase — open source or proprietary — this is the announcement I'd watch most carefully over the next quarter. The economics of having a continuously running security agent attached to your repo are very different from running a periodic third-party audit. I wrote about this category transition in my piece on [AI zero-day discovery](/content/mejba.me/ai-zero-day-discovery-debate.md), and Code Mender is the most concrete production example I've seen.

The SynthID expansion is the other half of the safety story. The watermark standard now spans NVIDIA, OpenAI, ElevenLabs, and Kakao, with 100+ billion images and videos marked. C2PA content credentials are expanding across Google's generative tools. The AI Content Detection API is now available on Agent Platform. These are not glamorous announcements. They are slow, infrastructure-level standardization moves. They are also exactly what a maturing industry produces when regulators start showing up at hearings.

## Gemini for Science: The Long Bet

Google saved the most ambitious announcements for the parts of the keynote that won't directly affect anyone's quarterly roadmap. Gemini for Science includes two pieces worth marking.

AlphaEarth Foundations is now positioned publicly as a digital twin of Earth — a virtual satellite that ingests optical imagery, radar, LiDAR, and climate data and compresses the entire terrestrial surface into a queryable embedding at 10x10 meter cell resolution, updated annually. The model reduces storage requirements by a factor of 16 compared to other AI systems tested by Google. They're working with 50+ organizations on real-world applications — food security, deforestation, urban planning, water resources. Pairing AlphaEarth with Gemini's reasoning capability is the next step, which would let analysts ask plain-language questions like "where in the Amazon has crop encroachment accelerated in the last three years" and get a data-backed answer.

Isomorphic Labs — Alphabet's drug-discovery company built on the foundations of AlphaFold — is on track to put its first AI-designed drugs into clinical trials by end of 2026. They raised $2.1 billion in Series B funding earlier this month to accelerate the Isomorphic AI Drug Design Engine.

Neither of these is going to ship into a developer SDK in the next six months. But the pattern — frontier AI applied to large physical-world data graphs (Earth, biology) — is the long bet that justifies the entire compute investment Alphabet has been making since 2023. Most of what Google announced at IO 2026 is about capturing the application layer. AlphaEarth and Isomorphic are about justifying the underlying infrastructure investment over a decade-long horizon.

## What I'm Actually Doing This Week

Let me close with the practitioner's view, because that's what most of you came here for.

Of everything Google shipped at IO 2026, here's what I'd actually test in the next seven days, ranked by what I think will affect your day-to-day work:

**Run Gemini 3.5 Flash through your real agentic workflow.** Not a toy benchmark. Take whatever multi-step agent task you currently run on Claude or GPT, port it to Gemini 3.5 Flash via the Antigravity harness, and measure latency, recovery behavior, and total cost per completed task. The 4x speed claim is the single most testable number from the keynote. Test it.

**Install Antigravity 2.0 and try the parallel agent orchestration.** If you've been running sequential agent chains, the parallel model is a meaningfully different design pattern. Build something small — a research task with three concurrent subagents (gather, synthesize, format) — and see how the trace tooling holds up when one of them fails.

**Wire SynthID/C2PA detection into your content pipeline.** If you're shipping anything that ingests user-uploaded media, the AI Content Detection API on Agent Platform is the most important infrastructure announcement in the entire keynote. It's not glamorous. It's also a feature your users and your legal team will care about within a year. I covered the [content provenance problem](/content/mejba.me/mythos-ai-curl-vulnerabilities.md) tangentially before — this is the answer.

**Don't pay for Gemini Spark until you've seen real usage data.** I'll be testing it the week it lands in the US beta. The $100 entry price is a bet on a product category that doesn't have an installed base yet. Watch independent reviews. Wait two weeks. Then decide.

**Audit your structured product/content data before Universal Cart and Intelligent Search hit globally.** Summer 2026 is closer than it sounds. If you sell anything online or publish anything that depends on search traffic, the agent layer is reading your structured data, not your marketing copy. Make sure it's clean.

**Skip the glasses for now.** Audio-only intelligent eyewear is going to be a niche product for the first generation. Fall 2026 is when the category gets real — that's the cycle to evaluate, not the launch.

## What the Keynote Was Really About

The single insight I'd carry away from Google IO 2026 — the one I'll be thinking about for the next quarter — is that the pricing structure tells the whole story.

When a company moves from prompt-based pricing to compute-based pricing, they're telling you they expect their average user to start running workloads, not conversations. When they cut the top tier from $250 to $200 and slot a new tier underneath at $100, they're telling you the previous price ceiling was too high to recruit the next wave of users they need. When they ship a 24/7 personal agent at the same time as a desktop application built around multi-agent orchestration, they're telling you the next two years of competition are going to happen at the agent layer, not the model layer.

Google didn't ship a smarter chatbot at IO 2026. They shipped the infrastructure for a world where chatbots aren't the main product anymore. The model is fast and cheap. The harness is built for parallelism. The pricing assumes background work. The hardware is ambient. The detection layer assumes synthetic media is everywhere.

That's the pivot. And whether or not you ship anything with Google's stack, the rest of the industry is going to follow them into the same shape. Anthropic's already there. OpenAI is moving. The application layer is where the next year of AI competition gets decided, and Google just declared they're not going to lose it without a fight.

I'll be writing a deep-dive on Antigravity 2.0 once I've shipped a real project through it. Until then, the question I'd leave you with is this: when you look at your current AI workflow — your prompts, your subscriptions, your tools — how much of it still assumes the chatbot is the product? If the answer is "most of it," you've got six months to rebuild before the rest of the industry catches up to Google's frame.

The agent era didn't start on May 19, 2026. But that's the date the biggest company in software stopped pretending otherwise.

## Frequently Asked Questions

### What is Gemini Omni?
Gemini Omni is Google's new any-to-any multimodal model that takes any combination of text, image, audio, and video as input and produces any combination as output. Omni Flash is the first public model on the framework. Every Omni-generated video carries Google's SynthID watermark. For the full breakdown, see the Gemini Omni section above.

### How fast is Gemini 3.5 Flash compared to other frontier models?
Gemini 3.5 Flash runs roughly four times faster than other frontier models on output tokens per second while outperforming Gemini 3.1 Pro across nearly every benchmark. It scored 76.2% on Terminal-bench 2.1 and 1656 on GDPval-AA, and was co-optimized with the Antigravity harness for agentic workflows.

### What does the new Google AI Ultra plan cost?
The new Google AI Ultra plan starts at $100 per month, replacing the previous $250 top tier with a $200 tier above it. The $100 plan includes 5x higher usage limits than AI Pro, 20 terabytes of cloud storage, YouTube Premium, and beta access to Gemini Spark for US subscribers.

### What is Gemini Spark?
Gemini Spark is a 24/7 AI agent that runs on Google Cloud VMs on a user's behalf, integrates with MCP (Model Context Protocol), and will gain Chrome integration in summer 2026. It rolls out as a beta for Google AI Ultra subscribers in the US starting the week after IO 2026.

### When do the Google IO 2026 features launch?
Most consumer announcements ship in summer 2026 (Docs Live, Universal Cart, Intelligent Search globally, Chrome integration for Spark). The intelligent audio glasses by Samsung and Qualcomm ship fall 2026. Antigravity 2.0 and Gemini 3.5 Flash are available immediately to developers.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
