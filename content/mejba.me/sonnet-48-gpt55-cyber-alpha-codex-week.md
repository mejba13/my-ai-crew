**BRAND:** mejba.me
**TITLE:** Sonnet 4.8, GPT-5.5 Cyber, Alpha & Codex: My Week
**META TITLE:** Sonnet 4.8, GPT-5.5 Cyber, Alpha & Codex: My Week
**SLUG:** sonnet-48-gpt55-cyber-alpha-codex-week
**PRIMARY KEYWORD:** Sonnet 4.8 GPT-5.5 Cyber AI week
**META DESCRIPTION:** Sonnet 4.8 leaks, GPT-5.5 Cyber, OpenRouter Alpha mystery models, and Codex going super app — my honest take on the AI week that actually mattered.
**TAGS:** AI News May 2026, Claude Sonnet 4.8, GPT-5.5 Cyber, OpenRouter Alpha, OpenAI Codex

---

# Sonnet 4.8, GPT-5.5 Cyber, Alpha, and a Codex That's Eating My Workflow: My Week

I almost let this week slide past me.

I had a client deliverable burning, two repos in mid-migration, and a Codex routine that had gone slightly feral overnight. So when my Slack lit up at 7:14 AM Wednesday with a screenshot of an Anthropic source-code reference to "Sonnet 4.8" — the same week the UK AI Security Institute dropped a public eval where GPT-5.5 either matched or outperformed Claude Mythos in offensive cybersecurity, the same week a stealth model called "Alpha" started topping OpenRouter charts, and the same week OpenAI quietly turned Codex into something that looks suspiciously like a super app — I almost did the responsible thing and ignored all of it until the weekend.

I didn't. I spent two evenings testing what I could actually get my hands on, reading the leak coverage, and pulling apart the AISI eval line by line. What I found is more interesting than the headlines suggest, and the headlines were already loud.

This is my week-in-review for the seven days where the AI roadmap I thought I understood quietly got rewritten. If you've been reading my [signal-vs-noise breakdown of April 2026](https://www.mejba.me/ai-news-week-april-2026-signal-vs-noise), this is the natural follow-up. The signal-to-noise ratio this week is much higher. Almost every drop on this list will affect how I work next month.

Four threads. Let me walk through each one in the order it actually changed my thinking.

## Thread 1: The Sonnet 4.8 Leak Isn't Really About Sonnet 4.8

Anthropic had a rough March. Two separate security incidents — a publicly accessible internal CMS exposed on March 26, then 512,000 lines of Claude Code's TypeScript source code accidentally published to npm a few days later — combined to give the outside world the closest look at Anthropic's roadmap I've ever seen, and probably more than the company ever intended to share. Fortune broke both stories. The npm leak was particularly painful because it included references to a model family Anthropic hadn't formally named yet.

Here's what the leaks actually exposed, based on what's been corroborated across [Fortune's reporting](https://fortune.com/2026/03/31/anthropic-source-code-claude-code-data-leak-second-security-lapse-days-after-accidentally-revealing-mythos/), [decoder coverage](https://decodethefuture.org/en/claude-opus-4-7-sonnet-4-8-capybara-model-roadmap-leaked/), and follow-up analysis from independent researchers:

- **Opus 4.7** — already shipped in mid-April 2026, public, documented
- **Sonnet 4.8** — referenced in code, expected May 2026, vision and instruction-following improvements implied
- **Mythos** — the next-generation family above the current Opus/Sonnet split, currently in restricted preview
- **Capybara** — a leaked tier name positioned *above* Opus, suggesting the family tree is about to gain a new top end
- **Undercover Mode** — a flag I have not seen explained anywhere in official documentation
- **44 feature flags** — the kind of detail nobody outside Anthropic was supposed to read

The headline interpretation in most of the press has been "Anthropic accidentally revealed Sonnet 4.8 is coming in May." That part is technically true. It's also the least interesting part.

What I keep coming back to is the *shape* of the roadmap. Two and a half years ago, Anthropic was shipping one model family with a small/medium/large split. Today's leaked structure shows at least four named tiers in active development simultaneously: a workhorse Sonnet line iterating on a roughly six-to-eight-week cadence, an Opus line being kept deliberately ahead of it, a Mythos line representing what Anthropic itself called a "step change" in capability, and a *Capybara* tier sitting above Opus that nobody in the analyst community has fully figured out yet.

When I dug into the [Mythos leak coverage](https://fortune.com/2026/03/27/anthropic-leaked-ai-mythos-cybersecurity-risk/), what struck me was how seriously Anthropic itself appears to be taking the cybersecurity implications of its own model. The leaked documents acknowledge Mythos could "significantly heighten cybersecurity risks by rapidly finding and exploiting software vulnerabilities" — language that reads less like marketing and more like a regulatory filing. That framing matters because it sets up the next thread of this week's story.

Sonnet 4.8 will probably ship boring. Better vision, better instruction following, same $3/$15 per million token pricing, the usual incremental coding bench gains. I'll test it the day it lands. But the model nobody outside Project Glasswing partners is testing — Mythos — is the one I keep thinking about.

There's a fuller treatment of the leak in my [Anthropic Claude Mythos leak post](https://www.mejba.me/anthropic-claude-mythos-leak) and a longer cybersecurity-specific analysis in [Claude Mythos cybersecurity impact](https://www.mejba.me/claude-mythos-cybersecurity-impact). I'm not going to retread that ground here. What I want to focus on is what happened next.

Because what happened next is that OpenAI dropped a model that punched Mythos in the mouth on the only public benchmark either of them has been measured against.

## Thread 2: GPT-5.5 Wasn't Supposed to Be the Cybersecurity Story

The UK AI Security Institute (AISI) is one of the few organizations on the planet running real cybersecurity evaluations against frontier models with public methodology and credible technical depth. Their evaluation suite uses 95 capture-the-flag tasks across four difficulty levels — easy, medium, hard, and expert — covering reverse engineering, exploit development for various memory-safety bugs, cryptographic attacks, network pivoting, and unpacking obfuscated malware. These aren't toy problems. The "expert" tier is calibrated against tasks human security professionals consider non-trivial.

[AISI published their GPT-5.5 evaluation on April 30, 2026](https://www.aisi.gov.uk/blog/our-evaluation-of-openais-gpt-5-5-cyber-capabilities). The headline number, the one [decoder ran with](https://the-decoder.com/gpt-5-5-matches-claude-mythos-in-cyber-attack-tests-uk-ai-security-institute-finds/), is that GPT-5.5 hit a 71.4% success rate on the expert-tier tasks — putting it in a statistical dead heat with Claude Mythos Preview, the model Anthropic has been so worried about that it restricted access through Project Glasswing.

I read the AISI report twice. Three things jumped out that the headline coverage glossed over.

**First, the "Last Ones" result is the actual story.** Buried in the eval is a 32-step end-to-end corporate network attack simulation called "The Last Ones." A human expert needs about 20 hours to chain it through. GPT-5.5 completed the full chain in 2 of 10 attempts. Mythos Preview did it in 3 of 10. Both results are *individually* alarming. Read together, they're a marker that we have crossed into a regime where a frontier model can autonomously execute multi-step offensive operations that previously required senior penetration testers.

**Second, the cost-and-latency numbers are the underrated part of the eval.** When GPT-5.5 succeeds on these tasks, it succeeds *fast*. The Last Ones run cost is measured in single-digit dollars per attempt and minutes of wall-clock time. The same chain done by a human expert costs whatever a senior pentester earns over 20 hours plus the coordination overhead. The economic asymmetry is the part that should be keeping CISOs awake.

**Third, AISI found a universal jailbreak.** The same report notes that AISI red-teamers identified a single universal prompt that elicited violative content across every malicious cyber query OpenAI provided for testing. The attack took six hours of expert red-teaming to develop. Six hours. For a universal jailbreak. On the model that just matched Mythos on offensive cyber.

That last finding is why the *next* announcement landed differently than I think people processed.

### GPT-5.5 Cyber and the Distribution Question

On April 30, the same day the AISI report dropped, [Sam Altman announced GPT-5.5 Cyber](https://openai.com/index/scaling-trusted-access-for-cyber-defense/) — a specialized variant fine-tuned for security workflows, going first to a vetted pool of "critical cyber defenders" via OpenAI's new Trusted Access for Cyber program. Government entities, critical infrastructure operators, security vendors, cloud providers, and financial institutions get it first. Broader rollout is staged.

The framing OpenAI used is fascinating. Two weeks earlier, Altman had publicly criticized Anthropic's Project Glasswing approach to Mythos as overly restrictive. Now OpenAI was rolling Cyber out via a vetting program. [TechCrunch](https://techcrunch.com/2026/04/30/after-dissing-anthropic-for-limiting-mythos-openai-restricts-access-to-cyber-too/) and [The Register](https://www.theregister.com/2026/05/01/openai_locks_gpt55cyber_behind_velvet/) both pointed out the inconsistency. I think the inconsistency is actually the most honest thing either lab has done on cyber distribution.

Here's the thing nobody on either side has been willing to say cleanly: there is no good distribution policy for a frontier offensive cyber model. Restrict it and the bad actors get there anyway via open-source models that follow six months later. Open-source it and you've handed every threat actor a force multiplier. Sell it under enterprise license and you've created a class system in defensive security where Fortune 100 banks have access to vulnerability discovery tools that municipal water systems do not.

Watching Anthropic and OpenAI converge on roughly the same restrictive answer despite their public posturing tells me both companies have run the math and arrived at the same conclusion. That conclusion is "we don't actually know what to do, so we'll start narrow and widen carefully." I think that's the honest position. I also think the open-source labs are about to make it irrelevant within twelve months.

For my own work, the practical implication is clear. I'm not going to get hands-on access to Mythos or GPT-5.5 Cyber. Neither are most readers of this post. What we *will* get is the slipstream — the public Sonnet 4.8 and GPT-5.5 baseline models that benefit from the same training advances, minus the offensive-cyber fine-tunes. Those are the models that will sit in our IDEs and our terminals over the next quarter. They are getting meaningfully better at code reasoning as a side effect of the cyber work, and that's worth paying attention to even if you never run an exploit in your life.

For deeper context on how I think about agentic coding capability creep, my [GPT-5.5 vs Opus 4.7 comparison](https://www.mejba.me/gpt-5-5-vs-opus-4-7-comparison) covers the model-vs-model side, and my earlier [Mythos and DeepSeek V4 autonomy piece](https://www.mejba.me/mythos-deepseek-v4-glm5-autonomy) gets at the open-source question.

## Thread 3: Alpha Is the Most Interesting Mystery Model OpenRouter Has Hosted Yet

OpenRouter has been running stealth-model launches as a regular cadence for over a year now. Quasar Alpha was the first one I noticed. Optimus Alpha came after that. Pony Alpha tore through the rankings in February 2026, processing over 40 billion tokens on its first day before [Zhipu AI quietly confirmed](https://apidog.com/blog/pony-alpha-deepseek-or-glm-model/) it was their GLM-5 system. I wrote about that whole arc in [GLM-5 Pony Alpha tested](https://www.mejba.me/glm5-pony-alpha-tested), and the pattern has been consistent: a Chinese lab uses OpenRouter as a low-key public soak test before formally announcing the model under its real name.

This week, a new stealth listing appeared on OpenRouter labeled simply "Alpha" — distinct from the previous animal-codename releases. Capabilities pitch on the listing reads like a wishlist: high-performance foundation model, strong agentic workloads, tool-calling accuracy, long context, code generation, automated workflows, compatibility with Claude Code and OpenCode and similar productivity tools.

I gave it three hours on Wednesday night. Here's what I observed.

The model is *fast*. Tool-calling latency is closer to GPT-5.5-mini than to Opus 4.7 on the same workflows. Code generation quality is in the ballpark of Sonnet 4.6 — clearly behind Opus 4.7 on hard reasoning, but well ahead of last year's open-source baselines. Long-context comprehension feels real but I didn't push it past 400K tokens, so I cannot verify the 1M-context claim with confidence. Agentic workflows held up across a four-step research-and-summarize task that some smaller models drop in the middle.

What I cannot tell you is who built it. The candidate list, based on the [established pattern](https://www.technology.org/2026/03/18/mystery-ai-model-is-it-deepseek-v4/) and on response-style analysis people have been doing on OpenRouter, includes:

- **DeepSeek V4** — long-rumored, would explain the agentic-tool focus
- **Zhipu AI's next iteration above GLM-5** — if Pony Alpha was GLM-5, this could be GLM-6
- **MiniMax M2.x** — MiniMax has been on a roll and the naming convention fits
- **Qwen 3.x update** — Alibaba's Qwen team has been quiet, possibly too quiet
- **A Western lab** — less likely given the OpenRouter stealth pattern, but not impossible

My gut says Chinese open-weights lab, probably Zhipu or MiniMax, probably a response either to DeepSeek's positioning or to the GPT-5.5 release. The reason I think it matters is not the model itself but the cadence. Open-source-aligned labs are now shipping frontier-adjacent capability roughly four to six months behind the closed labs. The compression is real. The Mythos-vs-Cyber distribution question I framed above gets resolved by this trend, not by policy debates. Within a year, the offensive-cyber capability that's currently restricted to Project Glasswing partners and TAC-approved enterprises will be running on someone's laptop via a Hugging Face download.

If you want to test Alpha yourself, it's still listed at the time of writing and free to query. I would not put production traffic on it — stealth listings disappear without notice and the provenance is unverified — but for capability calibration it's worth the thirty minutes.

## Thread 4: Codex Quietly Became a Super App, and I Think OpenAI Won the Quarter

I've been running OpenAI Codex as a daily driver alongside Claude Code for months. My honest hands-on review is in [openai-codex-super-app-tested](https://www.mejba.me/openai-codex-super-app-tested). The April update mattered. The May update is bigger.

Here's what changed, based on [OpenAI's own announcement](https://openai.com/index/codex-for-almost-everything/) and the reporting that followed:

- **Computer Use shipped on macOS.** Codex now has a cursor of its own. It clicks, types, reads the screen, and operates background windows while you keep working.
- **Plugin marketplace grew past 90 integrations.** Gmail, Google Drive, Docs, Sheets, Slack, Notion, the full Microsoft 365 suite (Outlook, Excel, Word, PowerPoint, Teams, SharePoint), Atlassian Rovo, Jira, Confluence, GitLab, GitHub, Linear, CircleCI, CodeRabbit, Figma, Render, Neon, Salesforce, HubSpot, Zendesk. The list reads like every B2B tool you've ever signed up for. [The decoder has a good summary](https://the-decoder.com/openais-codex-gets-a-plugin-marketplace-for-slack-notion-figma-and-more/).
- **Chronicle memory system is on by default.** Codex now remembers context across days. The agent that started reviewing a PR on Tuesday picks up the same thread on Thursday without re-explaining the codebase.
- **Multi-day automations are first-class.** Recurring tasks — month-end finance reconciliation, weekly project briefs, pipeline reviews — get scheduled and run autonomously.
- **Role-based setup wizards** for finance, marketing, operations, legal, HR, and engineering, each with pre-configured tool integrations and prompt templates.

The role-based setup wizards are the part nobody is talking about correctly. OpenAI used to position Codex as a developer tool. The April-into-May update repositioned it explicitly as a knowledge-worker tool with developer features still attached. That repositioning shows up in the marketing copy ("Codex for almost everything"), in the role wizards, and most importantly in the integration coverage — Excel, PowerPoint, and Outlook are not developer integrations.

The competitive read on this is interesting. Codex is now positioned head-to-head against:

- **Claude Code with Routines and Computer Use** — Anthropic's equivalent stack, currently more polished on coding workflows but less broad on integrations
- **Microsoft 365 Copilot** — which has the integration moat but a weaker reasoning core
- **Google Workspace Gemini** — strong on Google Workspace, weak everywhere else
- **Custom enterprise agents** built on Workspace Agents, OpenAI's enterprise framework

I've been running both Codex and Claude Code in parallel for over a year. My honest take, after this update: Codex has overtaken Claude Code on breadth, while Claude Code is still ahead on raw coding workflow polish. If you only get one, pick based on whether you need depth or breadth. If you can run both — and I do — you should. My [Codex plus Claude Code two-agent workflow](https://www.mejba.me/claude-code-codex-two-agent-workflow) post lays out how I split work between them.

The update I keep noticing in practice is the Slack plugin specifically. Codex pulls channel context, drafts replies, summarizes long threads, and can moderate channels. That last capability is a tell. OpenAI is no longer building a coding assistant. They are building an operations agent that happens to write code when needed.

For broader coverage on the super-app angle, my [Codex AI super app GPT-5.5 workflow test](https://www.mejba.me/codex-ai-super-app-gpt-55-workflow-test) goes deeper on the multi-day-automation pattern and how I've been using it for client work.

## How These Four Threads Connect (And Why It Matters For Your Workflow)

Read together, this week's news is one story, not four.

The Sonnet 4.8 leak shows Anthropic's roadmap accelerating across four model tiers simultaneously. The GPT-5.5 cybersecurity benchmarks show that frontier capability is *spilling over* from coding into offensive cyber as a side effect of better reasoning and tool use. The Alpha mystery model on OpenRouter shows that open-source-aligned labs are compressing the gap to under six months. The Codex super-app update shows the closed labs are racing to lock in distribution before the open labs catch up.

The structural read: closed labs are sprinting on capability and distribution simultaneously, knowing the open labs will commoditize the capability layer within a year. Their bet is that distribution — the integration moats with Slack, Microsoft, Google, the role-based workflows, the multi-day memory — is the thing that won't get commoditized.

If you build software for a living, that bet has direct consequences for how you should be spending the next ninety days. I see four:

**One: stop optimizing your prompts and start optimizing your tool integrations.** The model is going to get better. Your prompt-engineering skill will compound less than your skill at wiring tools, MCPs, and integrations together. I'm spending two-to-one on integration plumbing versus prompt design now. Six months ago that ratio was reversed.

**Two: assume your IDE and your work calendar will be one surface by year-end.** Codex Computer Use plus Chronicle memory plus role-based agents plus 90+ plugins is the prototype. Anthropic has the same stack in slightly different packaging. The unified work-and-code surface is no longer a 2027 prediction. It's shipping now.

**Three: take cybersecurity capability spillover seriously.** If you ship code and you don't have a security review step in your agent pipeline, this is the quarter to add one. The same models that will improve your dev productivity are improving attacker productivity at the same rate. I added a security-review subagent to my own pipeline two weeks ago. It is paying for itself.

**Four: try at least one stealth model per month.** Alpha will not be the last one. The cadence on OpenRouter is monthly now. Spending thirty minutes a month testing whatever's on the platform keeps your capability calibration honest, and it is the cheapest possible insurance against being blindsided by an open-source model that suddenly matches the closed frontier.

The week I almost ignored turned out to be one of the most important weeks of the year so far. The Sonnet 4.8 leak rewrote my mental model of Anthropic's roadmap. The AISI eval rewrote my mental model of how close we are to autonomous offensive cyber. Alpha rewrote my mental model of the open-source gap. The Codex update rewrote my mental model of what an AI coding tool even *is* in 2026.

Four rewrites. One week. If you're still running the same tool stack and the same workflow you ran in February, you are running an architecture that's now demonstrably out of date. I will be testing Sonnet 4.8 the day it ships, running Cyber the day I qualify for TAC access (I won't), and pulling Alpha through my full workflow benchmark this weekend.

What I would do tonight if I were you: open the AISI report, read the Last Ones section, and ask yourself one question. If a frontier model can autonomously execute a 32-step offensive chain in 11 minutes for under two dollars, what does *your* infrastructure look like to it?

That's the question I haven't been able to put down all week. I doubt you will either.

## Frequently Asked Questions

### When does Claude Sonnet 4.8 release?
Sonnet 4.8 is expected to release in May 2026 based on references found in the leaked Claude Code source code. Anthropic has not confirmed an exact date publicly. Pricing is rumored to remain at $3 per million input tokens and $15 per million output tokens, matching Sonnet 4.6.

### Is GPT-5.5 better than Claude Mythos at cybersecurity?
According to the UK AI Security Institute's April 30, 2026 evaluation, GPT-5.5 achieved a 71.4% success rate on expert-tier offensive cyber tasks — statistically tied with Claude Mythos Preview. GPT-5.5 also completed the 32-step "Last Ones" attack chain in 2 of 10 attempts versus Mythos's 3 of 10. The gap is within statistical margin of error.

### What is the Alpha model on OpenRouter?
Alpha is an unnamed stealth foundation model listed on OpenRouter in early May 2026, claiming strong performance on agentic workloads, code generation, and long context. Its origin has not been confirmed, though community speculation points to a Chinese open-weights lab such as Zhipu, MiniMax, or DeepSeek based on OpenRouter's prior stealth-launch pattern.

### What is GPT-5.5 Cyber and who can access it?
GPT-5.5 Cyber is a specialized variant of GPT-5.5 fine-tuned for cybersecurity workflows including penetration testing, vulnerability identification, and malware reverse engineering. OpenAI is rolling it out first to vetted "critical cyber defenders" through its Trusted Access for Cyber program, prioritizing government entities, critical infrastructure operators, security vendors, and major cloud and financial institutions.

### Can OpenAI Codex replace Claude Code now?
Codex's April-May 2026 update added macOS Computer Use, 90+ plugin integrations, multi-day Chronicle memory, and role-based wizards — putting it ahead of Claude Code on breadth. Claude Code remains stronger on raw coding workflow polish. Most serious users run both in parallel rather than choosing one. See the section on Codex above for my detailed comparison.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
