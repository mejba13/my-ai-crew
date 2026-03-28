**BRAND:** mejba.me
**TITLE:** AI Weekly Roundup: The Week Everything Shifted
**SLUG:** ai-weekly-roundup-march-28-2026
**PRIMARY KEYWORD:** AI weekly roundup March 2026
**SECONDARY KEYWORDS:** Claude Mythos leak, ARC AGI 3 benchmark, Gemini 3.1 Flash Live
**META DESCRIPTION:** This week's AI news broke records. Claude Mythos leaked, ARC AGI 3 humbled every model, Codex got plugins, and Sora died. Here's my full breakdown.
**TAGS:** AI News, Weekly Roundup, Claude Code, AI Models, Analysis
**CONTENT TYPE:** News Analysis
**CONTENT CLUSTER:** AI Tools & Productivity
**TRANSFORMATION GOAL:** After reading, the reader will understand the strategic implications of this week's eight major AI developments and know which ones demand immediate action in their workflow.

---

I woke up Monday morning to a notification from a security researcher I follow on X. He'd found something in Anthropic's content management system — nearly 3,000 unpublished assets sitting in the open, including draft blog posts describing a model that Anthropic called "by far the most powerful AI model we've ever developed." By Tuesday, Fortune had the story. By Wednesday, every AI newsletter on the planet was running with it.

And that wasn't even the biggest story of the week.

This was one of those weeks where every morning brought another announcement that would have dominated an entire news cycle on its own. Anthropic accidentally leaked details on two unreleased models. OpenAI killed Sora and went all-in on a mysterious model called Spud. Google shipped real-time multimodal voice agents. An open-source model from China scored within 5% of Opus 4.6 on coding benchmarks. A new intelligence benchmark made every frontier model look like it was running on dial-up. And Claude Code got features that fundamentally change how I work with PRs.

I've been tracking AI developments daily for two years now, and this might be the densest seven-day stretch I've seen. Not because of hype — because of actual, testable, workflow-altering releases shipping alongside strategic moves that reshape the competitive map for the rest of 2026.

Here's what happened, what it actually means, and what I'm changing in my own workflow because of it.

## The Anthropic Leak: Claude Mythos and Capybara Are Real

Let's start with the story that broke the internet — and broke it in the most ironic way possible.

Security researchers Roy Paz of LayerX Security and Alexandre Pauwels of the University of Cambridge [discovered exposed data](https://fortune.com/2026/03/26/anthropic-says-testing-mythos-powerful-new-ai-model-after-data-leak-reveals-its-existence-step-change-in-capabilities/) in Anthropic's content management system. A configuration error — "human error," according to Anthropic — left close to 3,000 unpublished blog assets publicly accessible. Among them: draft posts describing two unreleased models.

**Claude Mythos** is the top-tier model. The leaked drafts describe it as a "step change" in capabilities, with dramatically higher scores than anything Anthropic has released on tests of software coding, academic reasoning, and cybersecurity. Anthropic confirmed the model exists and that they're testing it with a small group of early access customers.

**Capybara** sits between Mythos and the current Opus flagship — a new model tier that's more capable than Opus 4.6 but less expensive to run than Mythos.

Here's what makes this genuinely significant rather than just interesting gossip. The leaked internal documents specifically warn that Mythos could ["significantly heighten cybersecurity risks"](https://fortune.com/2026/03/27/anthropic-leaked-ai-mythos-cybersecurity-risk/) by rapidly finding and exploiting software vulnerabilities. Anthropic's own safety team flagged the potential for accelerating a cyber arms race. That's not marketing language — that's an internal risk assessment that was never meant to be public.

The planned rollout strategy tells you everything about where Anthropic's head is at. They might release intermediate versions — an Opus 5 or Sonnet 5 — before putting Mythos in anyone's hands. The model is expensive to run and "not yet ready for general release," according to the drafts.

My take? Two things stand out. First, the safety concerns are real and specific — this isn't vague hand-wraving about "potential risks." Anthropic's own team is worried about what this model can do with code-level vulnerability analysis. Second, the fact that they're building a tier *between* Opus and Mythos (Capybara) suggests the capability gap is large enough that they need a stepping stone. That's unusual. When the jump is incremental, you just ship the upgrade.

If you're building anything that depends on Claude's current capability ceiling — agentic workflows, automated security auditing, autonomous code generation — this week's leak is your signal to start planning for a significant capability jump in the next few months. I've already started [designing my agent architectures](/claude-code-agent-swarm-architecture) with headroom for models that are meaningfully smarter than Opus 4.6.

The irony of a safety-focused AI company accidentally leaking its most dangerous model's details through a CMS misconfiguration is not lost on anyone. But let's move on, because OpenAI dropped its own bomb this week.

## OpenAI's "Spud" — And Why They Killed Sora to Build It

While everyone was parsing Anthropic's leak, OpenAI was making moves that tell you exactly where their priorities are shifting.

CEO Sam Altman sent an internal memo — [later reported by The Information](https://www.theinformation.com/articles/openai-ceo-shifts-responsibilities-preps-spud-ai-model) — confirming that pre-training on a new model codenamed "Spud" is complete. Altman told employees to expect a "very strong model" in "a few weeks" that can "really accelerate the economy." Whether Spud becomes GPT-5.5 or GPT-6 remains unclear.

But here's the part that made me sit up straight. To free compute capacity for Spud and other priorities, [OpenAI is shutting down Sora](https://www.cnn.com/2026/03/24/tech/openai-sora-video-app-shutting-down). The web and app version goes dark on April 26, 2026, with the API following on September 24.

Sora — the AI video generation tool that launched to massive hype — peaked at roughly 3.3 million downloads in November 2025 before declining to 1.1 million by February 2026. Disney pulled its planned $1 billion investment in OpenAI alongside the announcement. That's not a pivot. That's a full retreat from video generation to pour everything into language model capability.

The strategic signal is unmistakable. OpenAI is betting that raw model intelligence — the kind that can "accelerate the economy" — matters more than flashy creative tools. They're consolidating around what they believe is a winner, and they're willing to kill a product with over a million active users to make it happen.

Altman also relinquished direct oversight of OpenAI's safety and security teams to focus on "building datacenters at unprecedented scale." Make of that what you will.

For those of us in the developer tools space, Spud matters for a practical reason: it may serve as the foundation for OpenAI's planned desktop "superapp" combining ChatGPT, Codex, and the browser Atlas into a single environment. If that ships, it changes the competitive dynamics of the entire AI-assisted coding market.

Speaking of Codex — it got a major upgrade this week too.

## Codex Gets Plugins: From Coding Tool to Execution Platform

OpenAI introduced a [plugin system for Codex](https://siliconangle.com/2026/03/27/openai-introduces-plugins-codex-programming-assistant/) on March 27, and this one deserves more attention than it's getting.

Plugins in Codex aren't simple add-ons. They're installable bundles that package skills, app integrations, and MCP (Model Context Protocol) server configurations into reusable workflows. The curated directory includes integrations with Slack, Notion, Figma, Gmail, and Google Drive — more than a dozen prepackaged options at launch.

What this means in practice: Codex is no longer just a coding agent. It's becoming an execution environment where you can launch pre-built, runnable AI workflows — iOS app development, data analysis, report generation — with minimal setup. Users can install a plugin and get a complete workflow running without writing custom prompts or configuring tools themselves.

The numbers back up the momentum. [Codex hit 1.6 million weekly active users](https://www.unite.ai/openai-adds-plugin-marketplace-to-codex/) as of early March 2026 — more than tripling since February's GPT-5.3 Codex launch. Enterprise customers including Cisco, NVIDIA, Ramp, Rakuten, and Harvey are deploying it across teams.

This is a direct shot at Claude Code's plugin and skills ecosystem. I [wrote about Claude Code's plugin system](/claude-cowork-plugins-guide) a few weeks ago, and the timing of OpenAI's move feels deliberate. The plugin war is officially on.

My honest assessment? Codex's plugin approach is more polished for non-technical users who want turnkey workflows. Claude Code's approach gives more control to developers who want to build custom agent pipelines. Both are viable strategies, and the winner probably depends on which user base grows faster. I'm watching adoption numbers closely over the next quarter.

But while the proprietary labs were fighting over plugins, something happened in the open-source world that deserves serious attention.

## GLM 5.1: The Open-Source Model That's 94.6% of Opus

Z.ai (formerly Zhipu AI) made GLM 5.1 available to all Coding Plan users on March 27, and [the benchmark numbers](https://help.apiyi.com/en/glm-5-1-coding-plan-claude-opus-alternative-api-guide-en.html) are striking.

Using Claude Code as the testing tool — which is a nicely controlled comparison environment — GLM 5.1 scored **45.3 points** on coding benchmarks. Opus 4.6 scored **47.9**. That's 94.6% of Opus's performance. A 28% improvement over GLM 5's score of 35.4.

| Model | Coding Score | Gap to Opus 4.6 | Architecture |
|---|---|---|---|
| Claude Opus 4.6 | 47.9 | — | Proprietary |
| GLM 5.1 | 45.3 | -2.6 points (5.4%) | 744B MoE, 40B active |
| GLM 5.0 | 35.4 | -12.5 points | 745B MoE, 44B active |

I [tested the previous GLM 5 (Pony Alpha)](/glm5-pony-alpha-tested) when it first appeared as a stealth release on Open Router, and even then I said the gap was closing faster than most Western developers realized. GLM 5.1 proves the point. A 28% jump in a single iteration — on the same underlying 744 billion parameter Mixture-of-Experts architecture — means Z.ai's training pipeline is maturing rapidly.

The pricing makes this particularly interesting. GLM Coding Plans start at $3/month promotional, $10/month standard. Compare that to Opus 4.6 API costs. For teams running high-volume agentic workloads where 94.6% accuracy is acceptable, GLM 5.1 is a legitimate option at a fraction of the price.

Z.ai hasn't open-sourced GLM 5.1 yet, but they've teased it's coming — and their track record supports the claim. GLM 4.7 sits on Hugging Face under the MIT License right now. If GLM 5.1 follows the same path, we'll have a near-frontier coding model available as open weights for anyone to run locally.

The gap between open-source and proprietary AI models is no longer measured in generations. It's measured in single-digit percentage points. That shift happened faster than most people expected, and it changes the economics of every AI-dependent product.

Now for something completely different — and genuinely humbling.

## ARC AGI 3: The Benchmark That Made Every AI Look Stupid

ARC AGI 3 [launched on March 25, 2026](https://arcprize.org/arc-agi/3), and it might be the most important benchmark release of the year. Not because AI did well — because AI did catastrophically badly.

This is the first interactive reasoning benchmark. Previous benchmarks test whether a model can answer questions or generate code. ARC AGI 3 tests whether a model can explore a novel environment, figure out what it's supposed to do without any instructions, build a working mental model of the world, and then solve tasks on the first attempt.

The results are sobering:

- **Google Gemini 3.1 Pro:** 0.37%
- **OpenAI GPT 5.4:** 0.26%
- **Anthropic Opus 4.6:** 0.25%
- **Humans:** 100%

Read those numbers again. The best AI system on earth — Google's flagship model — solved less than half a percent of the tasks. Humans solved all of them.

[The prize pool is $850,000](https://the-decoder.com/arc-agi-3-offers-2m-to-any-ai-that-matches-untrained-humans-yet-every-frontier-model-scores-below-1/) for the ARC AGI 3 track alone, with a $700,000 grand prize for the first agent to score 100%. Competition runs through December 2026, with milestone checkpoints on June 30 and September 30.

Why does this matter beyond academic interest? Because ARC AGI 3 measures something fundamentally different from what current AI is good at. Current models excel at pattern matching within distributions they've trained on. ARC AGI 3 requires genuine *learning* — the ability to encounter something truly novel and figure it out from scratch, without prior examples, without instructions, without the ability to try multiple times.

This is the gap between "AI that automates known tasks" and "AI that can handle genuinely novel situations." Every frontier lab knows this gap exists, but ARC AGI 3 puts a precise number on how wide it is. And that number — sub-1% versus 100% — suggests we're not as close to general intelligence as the hype cycle implies.

For practitioners, this is a useful reality check. The AI tools we're using — Claude Code, Codex, Gemini — are extraordinarily powerful within their training distributions. They can write code, analyze data, generate content, and automate workflows with remarkable skill. But they cannot yet *learn* in the way humans do. Building your AI strategy around that distinction — leveraging AI for what it's great at while keeping humans in the loop for genuinely novel problems — remains the right call for 2026.

That said, Google's position at the top of even this brutal benchmark is worth noting. Which brings us to their other big announcement this week.

## Gemini 3.1 Flash Live: Real-Time Voice and Vision Goes Live

Google DeepMind [released Gemini 3.1 Flash Live](https://blog.google/innovation-and-ai/technology/developers-tools/build-with-gemini-3-1-flash-live/) on March 26, and this is the kind of infrastructure release that doesn't generate headlines but quietly changes what's buildable.

Flash Live is a real-time multimodal voice and vision model. It processes audio natively — not through transcription, but by understanding acoustic nuances directly. It handles video frames alongside audio. And it does all of this over WebSocket connections with full-duplex communication, meaning it supports "barge-in" (user interruptions) during responses.

The practical implication: you can now build conversational AI agents that see and hear in real-time, with latency low enough for natural conversation. Not "good enough for a demo" latency — [production-grade latency](https://www.marktechpost.com/2026/03/26/google-releases-gemini-3-1-flash-live-a-real-time-multimodal-voice-model-for-low-latency-audio-video-and-tool-use-for-ai-agents/) designed for deployed applications.

Google says they spent over a year focusing on infrastructure and developer experience for this release. The model supports a 128K token context window and is available through the Gemini API and Google AI Studio via the Live API.

I haven't built anything with Flash Live yet — it launched two days ago — but I can already see three use cases I want to test: a real-time code review assistant that watches my screen and comments on code as I write it, a voice-controlled Claude Code wrapper that lets me dictate tasks while my hands are full, and an interactive tutorial system that adapts based on what it sees on the student's screen.

If you're building anything involving voice or video interaction, this is the model to watch. The combination of native audio processing, real-time video understanding, and WebSocket-based full-duplex communication is a capability stack that didn't exist in production-ready form a week ago.

## Claude Code: Three Updates That Change My Daily Workflow

Anthropic shipped three Claude Code updates this week that I'm already using daily. Let me walk through each one.

### Auto-Fix in the Cloud

This is the one that genuinely changed how I work with pull requests. [Claude Code can now watch your PRs remotely](https://x.com/noahzweben/status/2037219115002405076) — fixing CI failures, addressing review comments, and pushing fixes while you're away from your keyboard.

When CI fails, Claude reads the error, investigates the root cause, pushes a fix, and explains what it did. For clear review comments, Claude makes the change, pushes, and replies to the thread. This happens on Anthropic's cloud infrastructure, so it keeps running even when your laptop is closed.

I submitted three PRs on Wednesday afternoon and went to make dinner. When I came back, two were green — Claude had fixed a failing test caused by a timezone edge case and resolved a reviewer's request to rename a variable for clarity. The third had a more complex issue that Claude flagged but couldn't resolve automatically, which is exactly the right behavior.

The time savings are real, but the bigger win is eliminating context-switching. The PR review cycle used to fragment my deep work sessions — I'd be in the middle of building something new when a review comment would pull me back to yesterday's code. Now Claude handles the routine fixes, and I only get pulled in for the genuinely complex stuff.

### Auto Mode

[Launched on March 24](https://claude.com/blog/auto-mode), auto mode introduces a built-in AI classifier that reviews every tool call before execution. Safe actions proceed automatically. Risky ones — mass file deletions, data exfiltration attempts, malicious code execution — get blocked, and Claude takes a different approach.

This is the middle path between "approve every single action" (safe but exhausting) and "skip all permissions" (fast but terrifying). I've been running it in a Docker container for four days, and the classifier's judgment has been solid. It correctly auto-approved file reads, git operations, and test runs while blocking an rm -rf that would have nuked a directory I didn't intend to delete.

Anthropic is being appropriately cautious — it's a research preview available on Team plans, with Enterprise and API access coming soon. They explicitly recommend running it in isolated environments. Smart approach for a feature that gives an AI agent more autonomous control.

### Peak Hours Session Limits

The less exciting but practically important update: [Anthropic adjusted session limits during peak hours](https://www.theregister.com/2026/03/26/anthropic_tweaks_usage_limits/) (5:00-11:00 AM PT on weekdays) across Free, Pro, and Max plans. Five-hour sessions can burn faster during peak because token costs are higher.

The silver lining? Off-peak hours got a temporary promotion — doubled usage limits — that ran through March 28. My workflow adjustment: I've been shifting my heaviest Claude Code sessions to early morning or evening, and it's actually been a productivity win. Fewer Slack distractions during those hours anyway.

If you'd rather have someone set up a Claude Code workflow with auto-fix, auto mode, and optimized session management from scratch, I take on those kinds of integrations. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Supporting Cast: Five More Stories Worth Tracking

Not every story this week was a headliner, but several deserve a mention because they'll matter more in the coming weeks.

### Mistral's Voxtral TTS — Open-Weight Voice That Rivals ElevenLabs

Mistral [released Voxtral TTS](https://mistral.ai/news/voxtral-tts) on March 26, a 4 billion parameter open-weight text-to-speech model that supports nine languages and can clone a voice from just three seconds of reference audio. Human evaluations show it achieves superior naturalness compared to ElevenLabs Flash v2.5.

The critical detail: Mistral is releasing the full model weights. You can download Voxtral TTS, run it on your own servers — or even a smartphone — and never send audio data to a third party. For developers building voice-enabled applications with privacy requirements, this is a genuine alternative to API-dependent services.

### Anthropic's Operon — Claude Gets a Biology Lab

Operon is a new dedicated mode in the Claude desktop app, [designed specifically for biology and health research](https://www.testingcatalog.com/anthropic-tests-claude-operon-for-scientific-research-in-biology/). It sits alongside Chat, Code, and Cowork as a separate workspace with project management tools and multi-session capabilities tailored to scientific research workflows.

This signals Anthropic's push into vertical AI applications. Alongside partnerships with the Allen Institute and Howard Hughes Medical Institute, Operon suggests Anthropic sees scientific research — particularly biology — as a high-value use case where Claude's reasoning capabilities can deliver outsized impact.

### ElevenLabs CLI Goes Agent-First

ElevenLabs [rolled out sweeping updates](https://elevenlabs.io/docs/changelog/2026/3/9) to its CLI in March, making it non-interactive and agent-first by default. The tool now treats voice agents as code — you manage them through configuration files, version control, and CI/CD pipelines rather than through dashboard interactions.

For anyone building automated audio workflows — podcast production, voice agent deployment, content narration — this is the kind of infrastructure change that unlocks new automation patterns. I'm already thinking about how to integrate this with my [Claude Code-powered audio workflows](/claude-code-remotion-video-creation).

### The Cursor Composer 2 Scandal

This one's a slow burn. On March 19, Cursor launched Composer 2 claiming it scored 61.7 on Terminal-Bench 2.0 — beating Claude Opus 4.6 — at $0.50 per million input tokens. Impressive, right?

Then [a developer named Fynn found something](https://venturebeat.com/technology/cursors-composer-2-was-secretly-built-on-a-chinese-ai-model-and-it-exposes-a/) in Cursor's OpenAI-compatible API response: a model identifier revealing that Composer 2 is actually **Kimi K2.5**, an open-weight model from Beijing-based Moonshot AI, fine-tuned with reinforcement learning. Cursor eventually confirmed that roughly one-quarter of the pre-training comes from Kimi K2.5's base.

The licensing problem? Kimi K2.5's Modified MIT License requires any commercial product with over $20 million monthly revenue to prominently display "Kimi K2.5" in its UI. Cursor's revenue reportedly exceeds that threshold by roughly 8x. As of late March, Cursor hadn't addressed the compliance question publicly.

This story matters beyond the drama. It reveals how the lines between "proprietary" and "open-source" models are blurring in commercial products — and how attribution and licensing in AI are becoming serious legal and ethical issues.

### Sora's Quiet Death

I covered the Sora shutdown earlier in the context of OpenAI's Spud pivot, but it's worth noting the timeline for anyone with active Sora projects. [The app goes dark April 26, 2026](https://www.cnn.com/2026/03/24/tech/openai-sora-video-app-shutting-down). The API follows September 24. If you have work on Sora, export it now. Don't wait.

## What This Week Actually Means — My Read

Step back from the individual stories and three patterns emerge.

**Pattern 1: The capability ceiling is rising fast, but unevenly.** Mythos and Spud suggest the next generation of frontier models will be dramatically more powerful for coding, reasoning, and analysis. But ARC AGI 3 shows that "more powerful" doesn't mean "generally intelligent." We're building increasingly impressive specialists, not generalists. Plan accordingly.

**Pattern 2: The open-source gap is nearly closed.** GLM 5.1 at 94.6% of Opus. Voxtral TTS matching ElevenLabs. Kimi K2.5 competitive enough that Cursor built a commercial product on it. The era of proprietary models maintaining a massive quality advantage is ending. The differentiator is shifting to ecosystem, developer experience, and integrated tooling — which is exactly why both OpenAI and Anthropic are investing heavily in plugins, auto-fix, and cloud-based workflows.

**Pattern 3: The AI market is consolidating around language models.** OpenAI killed its video product to focus on Spud. Anthropic is building vertical applications (Operon) on top of Claude rather than diversifying into new modalities. Google is shipping multimodal capability that feeds into its language model ecosystem. The consensus is clear: the core language/reasoning model is the platform. Everything else is a feature.

For my own workflow, here's what I'm changing this week:

1. **Shifted to auto mode in Claude Code** inside Docker containers for all non-production work
2. **Enabled PR auto-fix** on three active repositories — reclaiming roughly 30-40 minutes per day of context-switching
3. **Started testing GLM 5.1** via the Coding Plan for high-volume, lower-stakes agentic tasks where 94.6% accuracy is plenty
4. **Bookmarked the ARC AGI 3 leaderboard** as a reality check whenever AI hype gets too loud

One week. Eight major developments. And we're only three months into 2026.

The question I keep coming back to isn't whether AI is moving fast — that's obvious. The question is whether we're building workflows and skills that remain valuable as the capability floor rises. The engineers who'll thrive aren't the ones who can prompt a current model perfectly. They're the ones who understand the *pattern* of how these tools evolve — and position themselves to ride the next wave before it breaks.

This week made the next wave a lot more visible.

## Frequently Asked Questions

### What is Claude Mythos and when will it be released?

Claude Mythos is Anthropic's unreleased next-generation AI model, accidentally revealed through a CMS configuration error in March 2026. Anthropic describes it as a "step change" beyond current Opus models with dramatically higher coding, reasoning, and cybersecurity scores. No public release date has been announced — Anthropic is testing it with a small group of early access customers and may release intermediate models first.

### How does ARC AGI 3 differ from previous AI benchmarks?

ARC AGI 3 is the first interactive reasoning benchmark that requires AI agents to explore novel environments, infer goals without instructions, and solve tasks on the first attempt. Unlike traditional benchmarks that test memorized knowledge or pattern matching, ARC AGI 3 measures genuine learning ability. The best frontier AI model scored 0.37% while humans achieved 100%, revealing a massive gap in adaptive reasoning.

### Is GLM 5.1 open source?

GLM 5.1 is not yet open-source as of March 28, 2026, but Z.ai has signaled an upcoming open-weight release. The model is currently available through Z.ai's Coding Plan starting at $3/month. Z.ai has a strong track record of open-sourcing its models — GLM 4.7 is available on Hugging Face under the MIT License.

### What happened to OpenAI's Sora app?

OpenAI is shutting down Sora to redirect computing resources toward its new "Spud" language model. The Sora web and mobile app closes April 26, 2026, with the API following September 24, 2026. Downloads had declined from 3.3 million in November 2025 to 1.1 million by February 2026. Disney also ended its planned $1 billion investment in OpenAI alongside the announcement.

### What is Claude Code auto mode?

Claude Code auto mode, launched March 24, 2026, uses a built-in AI safety classifier to automatically approve low-risk actions while blocking potentially destructive operations like mass file deletions or data exfiltration. It's a middle ground between approving every action manually and skipping all permissions. Available as a research preview on Team plans, with Enterprise access coming soon.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
