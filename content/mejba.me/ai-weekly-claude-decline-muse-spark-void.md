**BRAND:** mejba.me
**TITLE:** AI Weekly: Claude's Decline, Muse Spark, Netflix VOID
**META TITLE:** AI Weekly April 2026: Claude Decline, Muse Spark, VOID
**SLUG:** ai-weekly-claude-decline-muse-spark-void
**PRIMARY KEYWORD:** AI weekly updates April 2026
**SECONDARY KEYWORDS:** Claude performance decline, Meta Muse Spark, Netflix VOID, Anthropic Broadcom deal
**META DESCRIPTION:** My take on the week's biggest AI news — Claude's measured performance drop, Meta's Muse Spark launch, Netflix VOID, and the $42B Anthropic compute deal.
**TAGS:** AI Weekly, Claude Code, Meta Muse Spark, Netflix VOID, AI Infrastructure
**CONTENT CLUSTER:** AI Tools & Productivity
**TRANSFORMATION GOAL:** Help builders separate signal from hype across thirteen AI announcements in one week, and know exactly which three to act on this weekend.

---

I opened my terminal on Monday morning and Claude asked me to confirm an edit to a file it hadn't read. Not a file it read once. A file it hadn't opened at all. It was going to pattern-match off the filename, guess at the imports, and ship a change to code it had never seen.

I cancelled the action. Stared at the screen for a second. Then I remembered the AMD report that dropped the week before — the one where Stella Laurenzo's team at AMD analyzed 6,852 Claude Code sessions and found that Claude's "reads per edit" had collapsed from 6.6 down to 2.0. A 70% drop in how much context Claude gathers before it starts changing your code.

I'd been feeling it for weeks. The data finally named it.

That's the story that dominated AI Twitter this week, but it's not the only one. Meta shipped Muse Spark — their first model from the new Superintelligence Labs. Netflix quietly open-sourced a video model that understands physics. Anthropic signed a 3.5-gigawatt compute deal with Google and Broadcom that analysts now estimate at $42 billion for 2027 alone. Microsoft Word started shipping Claude inside the document pane. And Andrej Karpathy casually dropped an open-source fix for Claude's memory problem that uses Obsidian as the storage layer.

Thirteen announcements. One week. Here's my personal breakdown of what I tested, what surprised me, and what actually matters for people who build things.

## The Claude Performance Story Everyone Was Whispering About

Let me start with the one that hit closest to home.

If you use Claude Code daily, you've probably felt what I'm about to describe. Edits shipping before reads. Plans that used to span six files now scoped to two. The model taking the cheapest available action instead of the correct one. I'd been blaming my prompts. I'd been blaming my context files. I'd been blaming myself.

Then the receipts landed.

Stella Laurenzo, head of AMD's AI team, published a data analysis of 6,852 Claude Code sessions containing 234,760 tool calls and 17,871 thinking blocks. The numbers are stark. Between January 30 and February 12 — the "good" period — Claude read 6.6 files for every file it edited. Between March 8 and March 23, that number crashed to 2.0. Thinking depth dropped 67% over the same window.

The behavioral pattern Laurenzo described matches my exact experience: Claude moved from "research-first" to "edit-first." When thinking gets shallow, the model defaults to whatever action is cheapest. It edits without reading. It stops without finishing. It takes the simplest fix rather than the correct one. It dodges responsibility for failures it caused.

Anthropic's response pointed at two known changes: Opus 4.6 moved to adaptive thinking by default on February 9, and on March 3 the default effort was dropped from high to medium. They say the `redact-thinking-2026-02-12` header is a UI-only change. Users aren't buying that full explanation — Fortune covered the backlash in a piece this week titled around Anthropic's "lack of transparency" on silent model updates.

Here's the part that matters for builders: you can fight this.

What's working for me right now:

- Explicitly set `thinking_effort: high` in every Claude Code session that touches production code
- Front-load context manually — paste the relevant files into the conversation instead of hoping Claude will go find them
- Use stricter agent definitions. Generic "read the codebase" instructions get shallow reads. Specific "read these four files, then diff them against the target behavior" instructions still work
- For anything architectural, switch to Opus 4.6 with extended thinking and accept the token cost

I'll cover the full config I run in a dedicated post. But the short version: trust in silent defaults is gone. Every serious session needs explicit thinking budget and explicit context. The era of "fire and forget" Claude Code sessions just ended.

## Meta Muse Spark: The Multi-Agent Model Hiding In Your WhatsApp

On April 8, Meta shipped Muse Spark — the first model from Alexandr Wang's Superintelligence Labs since Meta spent $14 billion to bring him over.

I tested it the same night.

The headline feature: Muse Spark can launch multiple specialized subagents in parallel to handle different parts of a single request. Ask it to plan a trip, and it spins up a flight agent, a hotel agent, and an activities agent simultaneously, then consolidates the results. It also ships with what Meta calls a "visual chain of thought" — step-by-step reasoning over images, shown explicitly in the output.

On the Intelligence Index, Muse Spark scored 52. That puts it behind Gemini 3.1 Pro and GPT-5.4 (both 57) and just under Claude Opus 4.6 (53). Not a leader on general reasoning. But Meta isn't really aiming at general reasoning.

Where Muse Spark genuinely shines is multimodal and health-related tasks. I ran it through a medical image interpretation test and a nutrition-planning workflow. On both, it held its own against Claude Opus 4.6 — and in one specific case (interpreting a photo of a food plate and building a macro breakdown), it was measurably better. The visual chain of thought made its reasoning legible in a way Claude's image responses usually aren't.

The distribution story is the real play, though. Muse Spark is rolling out inside WhatsApp, Instagram, Facebook, Messenger, and Meta's AI glasses. That's a reach Claude, ChatGPT, and Gemini combined don't have. My mom uses WhatsApp. She's never opened Claude.me in her life. In six months, she'll have a multi-agent AI sitting inside her messaging app by default.

One honest note: Muse Spark is proprietary. Meta said they "hope to open-source future versions." Given how they burned open-source trust with Llama 4's delayed weights, I'll believe that when the weights land.

## Netflix VOID: Video Editing That Finally Respects Physics

This one genuinely surprised me.

Netflix's AI research team — working with INSAIT at Sofia University — open-sourced a model called VOID (Video Object and Interaction Deletion). On paper, it's a video object-removal tool. In practice, it's the first model I've tested that actually understands what happens to a scene when an object disappears.

Remove a person holding a guitar from a video, and most tools either leave the guitar floating in the air or hallucinate a blurry mess where the person was. VOID removes the person *and* the physical influence they had on the guitar — the guitar drops. A hand pressing down on a pillow is removed, and the pillow decompresses back to its resting shape. A spinning top is deleted, and surrounding dust settles correctly.

The technical approach is clever. VOID is built on CogVideoX and uses a 4-value quadmask (0, 63, 127, 255) that encodes not just what to remove, but which surrounding scene regions get physically affected. It runs two-pass inference: pass 1 handles the main removal, pass 2 fixes the object-morphing artifacts that plague every video diffusion model I've tried. The training data came from Blender physics simulations and Google's Kubric framework — synthetic paired data with ground-truth physics.

It beats ProPainter, DiffuEraser, Runway, MiniMax-Remover, and Gen-Omnimatte on real and synthetic benchmarks. And — this is the part I still can't believe — it's on Hugging Face and GitHub under Apache 2.0.

Netflix just gave away the tool that lets small studios do post-production edits that used to require reshoots. If you're building anything in video production, creator tools, or synthetic data pipelines, this is the release of the quarter.

## Claude Inside Microsoft Word: The Beta That Changes Legal Work

There's a beta rolling out that I didn't expect to care about — and then I tried it on a real contract.

Microsoft shipped Claude integration inside Word. Open a document, summon Claude in the side pane, and it can summarize, analyze, and edit the document in tracked changes. But the specific feature that matters: it flags the critical changes and negotiable points in legal and contract documents automatically.

I fed it a draft services agreement I'd been procrastinating on. In under a minute, Claude had highlighted three clauses that were non-standard, explained why each was unusual, and proposed alternate language with tracked changes I could accept or reject. It was like having a junior lawyer do the first pass — except it actually read every word.

Word has 1.2 billion monthly users. Claude now lives inside it. That's not an AI feature launch. That's a distribution shift.

For anyone who deals with contracts, proposals, or long documents in Word — this is worth the beta wait.

## Google's Quieter Launches: Notebooks, Managed Agents, Simulations, AI Shopping

Google shipped four things this week. None of them dominated a headline. Together, they're a pattern.

**Notebooks** inside the Gemini app let you organize chats by project with attached source files. Think NotebookLM, but integrated with your regular Gemini conversations. I moved my content research workflow over and it cut the friction of "where did I save that research thread" to almost zero.

**Managed Agents** is Google's answer to Claude's agent SDK — Gemini-powered autonomous agents that run background tasks. If you've seen [my walkthrough of Anthropic's Managed Agents](/anthropic-managed-agents-walkthrough), this is the parallel offering. Early days, but the integration with Google Workspace means these agents can touch Gmail, Calendar, and Drive natively. That's a footprint Claude can't match today.

**Interactive Simulations** is the sleeper. Gemini Pro can now generate live, running physics simulations — I tested it with the classic double-pendulum example and it built a working simulation I could parameterize in real time. This is going to eat a chunk of the educational content space inside 12 months.

**AI Shopping for India** rolled out natural-language product comparisons using Gemini + Lens. Regional-first launches are becoming Google's pattern — they're testing complex shopping workflows in markets where Amazon dominance is weaker before bringing them West.

None of these are "the story of the week" on their own. Stacked together, Google just added four distribution surfaces for Gemini in seven days.

## The Ghost Murmur Story: Where I Had To Stop And Check

I want to flag this one because it's the kind of story that sounds like AI hype until you dig in — and then it sounds like something different.

On April 3, a US pilot was rescued in Iran. Reports surfaced that his location was pinned using a classified AI tool called "Ghost Murmur" — described as capable of detecting individual heartbeat electromagnetic signatures from miles away. The pilot was apparently located without traditional surveillance assets.

Physicists I trust pushed back hard. The electromagnetic field generated by a human heartbeat is measurable in a lab with a SQUID magnetometer at centimeter distances. Detecting it at miles, through buildings and electromagnetic noise, would break several known laws of physics. The likelier explanation is either classified sensor tech that isn't what the leak describes, or a cover story for a human intelligence source.

I'm not going to adjudicate the physics in a blog post. What I'll say is this: when unverified AI capabilities get attached to classified operations, take the claim with a very large grain of salt. We're in the era where "AI did it" has replaced "a source did it" as the default explanation for outcomes nobody wants to explain. Apply the same skepticism you'd apply to any other intelligence leak.

## The One Fix I'm Actually Using: Karpathy's Personal Wikipedia

If you only try one thing from this week's news, make it this.

Andrej Karpathy quietly open-sourced a Claude memory fix this week that uses Obsidian as the storage layer. I've been running it for four days and it has already replaced three tools in my workflow.

The setup is simple:

1. Install Obsidian and the Web Clipper extension
2. Connect Obsidian to Claude Desktop via filesystem MCP
3. Load the provided wiki schema (topics, sources, connections)
4. Start feeding research docs, clips, and notes into your vault
5. Claude reads the whole vault, synthesizes connected knowledge graphs across topics, and writes back new synthesis pages as you add more material

I tested it with three unrelated papers I'd been saving: one on LLM capability degradation, one on enterprise automation adoption, and one on the labor economics of software engineering. I asked Claude to find the connections. Inside a minute, it had written a synthesis note arguing that silent AI model degradation (paper one), rapid enterprise automation (paper two), and restructured software teams (paper three) are the three surfaces of a single underlying shift — compute-driven cost optimization is being pushed down the stack, and the second-order effect is quiet capability loss in tools that knowledge workers depend on.

That's a post I could write. Claude wrote the outline for me, with citations back to my own source notes.

If you read [my earlier piece on Obsidian as Claude's second brain](/obsidian-claude-code-second-brain), this is the same architecture, now formalized into a schema and workflow anyone can replicate. It's the closest thing to durable Claude memory that works today.

## The Infrastructure Story That Prices Everything Else

Behind every model launch this week sits one number: Anthropic just signed a deal for 3.5 gigawatts of Google TPU capacity through Broadcom, starting in 2027.

For context: the original Google Cloud agreement from October brought 1 gigawatt online in 2026. This new deal nearly quadruples that. Mizuho analysts estimate Broadcom will pull $21 billion in AI revenue from Anthropic in 2026 and $42 billion in 2027.

Anthropic's annual revenue run rate passed $30 billion — up from about $9 billion at the end of 2025. That's a 3.3x growth in under a year on a base that was already the fastest-scaling in enterprise software history.

Two things follow.

First, compute is the moat now. Not model architecture, not training data — raw gigawatts of power-efficient inference. Whoever owns the TPU pipeline owns the default API for the next three years. Google just wrote themselves into the infrastructure layer of every frontier model that isn't their own.

Second, the Claude performance story I opened with has to be read against this backdrop. Anthropic is running into real compute constraints while scaling faster than any SaaS company in history. The silent defaults dropping from high effort to medium effort aren't a betrayal — they're a rationing system. The 3.5-gigawatt deal is the long-term fix. Explicit thinking budgets are the short-term workaround.

## The Smaller Releases That Deserve One Line Each

A few things shipped that don't need their own section but shouldn't go unmentioned:

- **OpenClaw Native Video Generation** unified video gen from nine AI models (OpenAI, Google, Alibaba, others) into a single interface. Worth installing if you generate video regularly.
- **Cursor Remote Laptop Control via Phone** — kick off AI coding tasks on your laptop from your phone. Niche, but if you context-switch away from your machine often, this solves a real pain.
- **Offline Gemini 4 via OpenClaw** — Google published a three-step guide to run Gemini 4 fully offline. No cloud. No subscription. The fact that Google is actively teaching users to unplug their own model tells you something about where the compute economics are heading.

## What This Week Actually Means

Zoom out on the thirteen announcements and three patterns land hard.

**Pattern one: silent degradation is now a category.** The Claude story is the first well-documented case, but it won't be the last. Every major lab is compute-constrained. Every major lab has levers it can pull silently to manage load. Users have to assume that the defaults they were using six months ago are not the defaults they're using today. Explicit configuration is survival.

**Pattern two: distribution is eating intelligence.** Muse Spark isn't the smartest model. It's the one most people will actually use, because it's inside WhatsApp. Claude inside Word isn't the most advanced integration. It's the one 1.2 billion people will touch. For builders, the question "whose API do I use?" is quickly becoming less important than "where does my user already live?"

**Pattern three: the open-source layer is moving up the stack.** Netflix open-sourced a physics-aware video model. Karpathy open-sourced a knowledge-management layer. MiniMax open-sourced a self-improving LLM last week. The things that would have been proprietary moats 18 months ago are now public weights. If you're building a thin SaaS wrapper around a closed API, your moat is evaporating. If you're building workflows, pipelines, and integrations, you're building exactly where the value is accruing.

Before I close — one action item. Pick the single thing from this list that would change your Tuesday morning if you tried it, and block 30 minutes tomorrow to try it. For me, it was Karpathy's Obsidian fix. For you, it might be the Word beta, or explicit thinking budgets in Claude Code, or downloading VOID and running it on a video clip that's been annoying you for a year.

The gap between people who read the news and people who test the news compounds weekly. A year from now, the builders who tested three things per week will be untouchable. The ones who kept reading will still be reading.

It's Tuesday. Pick one.

## Frequently Asked Questions

### Is Claude Code actually getting worse, or is it just my prompts?
Both can be true, but the AMD data makes the model-side regression undeniable. Stella Laurenzo's analysis of 6,852 sessions showed "reads per edit" dropped from 6.6 to 2.0 and thinking depth fell 67% between February and March 2026. Anthropic confirmed the default thinking effort was lowered from high to medium on March 3. Set `thinking_effort: high` explicitly and front-load context manually.

### What is Meta Muse Spark and should I switch to it?
Muse Spark is Meta's first model from Superintelligence Labs, launched April 8, 2026. It scored 52 on the Intelligence Index, behind GPT-5.4 and Gemini 3.1 Pro. Don't switch your main workflow to it, but it's genuinely strong on multimodal health tasks and ships inside WhatsApp, Instagram, and Messenger — which means it matters for consumer reach more than for builder tooling.

### Is Netflix VOID actually open source?
Yes. VOID is available on Hugging Face and GitHub under the Apache 2.0 license. It requires a GPU with sufficient VRAM to run CogVideoX inference, but the model, weights, and training methodology are all public. This makes it the strongest open video object removal tool available as of April 2026.

### What was Anthropic's compute deal with Google and Broadcom?
Anthropic signed an agreement for approximately 3.5 gigawatts of next-generation Google TPU capacity through Broadcom, starting in 2027. Mizuho analysts estimate Broadcom will generate $21 billion in 2026 and $42 billion in 2027 from Anthropic alone. This is in addition to the 1 gigawatt already coming online in 2026 under the original Google Cloud agreement.

### How do I set up the Karpathy Obsidian + Claude memory system?
Install Obsidian and the Web Clipper extension, then connect Obsidian to Claude Desktop via a filesystem MCP. Load the public wiki schema Karpathy shared, then feed your research documents and web clips into the vault. Claude reads the whole vault on request and synthesizes connected knowledge across notes. For the full walkthrough, see my post on Obsidian as Claude's second brain.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
