**BRAND:** mejba.me
**TITLE:** Claude Code's Source Code Just Leaked — 5 Wild Features
**SLUG:** claude-code-leak-five-hidden-features
**PRIMARY KEYWORD:** Claude Code leak
**SECONDARY KEYWORDS:** Claude Code source code, Kairos agent, Claude Code hidden features
**META DESCRIPTION:** 512,000 lines of Claude Code source code leaked via npm. I dug through the five unreleased features — Kairos, UltraPlan, Buddy, Dream, and Undercover Mode.
**TAGS:** AI Development, Claude Code, Leaked Features, Autonomous Agents, News Analysis
**CONTENT TYPE:** News Analysis
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly what the Claude Code leak revealed, what each of the five hidden features does, and what Anthropic's strategic direction means for developers building with AI tools today.

---

I woke up this morning to 14 unread messages in my developer group chat. No context. Just links, screenshots, and one message from a friend that said: "Bro. Check npm."

By 4:23 AM ET, a security researcher named Chaofan Shou — an intern at Solayer Labs — had posted the discovery on X. Anthropic accidentally shipped a 59.8-megabyte JavaScript source map file inside version 2.1.88 of the `@anthropic-ai/claude-code` npm package. That source map pointed directly to a Cloudflare R2 bucket containing the full, unminified TypeScript source code for Claude Code. All of it. Every file. Every feature flag. Every comment left by Anthropic's engineers.

512,000 lines of code. Nearly 2,000 files. The entire `src/` directory of the tool I use every single day to ship production code.

And buried inside that codebase? Five unreleased features that completely reshape what Claude Code is about to become. I spent the morning reading through the analysis from [VentureBeat](https://venturebeat.com/technology/claude-codes-source-code-appears-to-have-leaked-heres-what-we-know), [CyberSecurity News](https://cybersecuritynews.com/claude-code-source-code-leaked/), and the [GitHub mirror repositories](https://github.com/Kuberwastaken/claude-code) that went live before Anthropic could scrub the registry. What I found isn't just interesting — it fundamentally changes how I think about where AI coding tools are headed.

Here's every feature, what it actually does, and why three of them kept me reading past my morning coffee going cold.

## How 512,000 Lines of Source Code Ended Up on the Public Internet

The leak mechanism is almost embarrassingly simple — and if you work with npm packages, it should make you slightly nervous about your own build pipeline.

When Anthropic pushed version 2.1.88 of the `@anthropic-ai/claude-code` package to the npm registry early this morning, the build process included a `.map` file. Source maps are standard developer tools — they let you debug minified JavaScript by mapping it back to the original source. Every major framework generates them. The problem is that you're never supposed to ship them to production, and you're definitely never supposed to ship them inside a public npm package that anyone can download.

This particular source map didn't just contain inline source references. It pointed to an R2 storage bucket on Anthropic's own infrastructure where the complete, unobfuscated TypeScript source lived as a downloadable ZIP archive. No authentication required. No access controls. Just... there.

According to [Rolling Out's reporting](https://rollingout.com/2026/03/31/anthropic-claude-code-leak-512000-lines/), Anthropic moved fast once the discovery went public. They pushed an npm update stripping the source map, then deleted older package versions from the registry entirely. But the internet has a long memory. At least three mirror repositories hit GitHub within hours, and the developer community had already started picking through every file.

The irony here is thick enough to cut. This is the second time Anthropic has leaked sensitive material through a basic configuration mistake — just five days after the [Mythos/Capabra leak](https://www.mejba.me/anthropic-claude-mythos-leak) where nearly 3,000 internal documents went public because someone forgot to flip a CMS toggle. Two operational security failures from a company that literally builds AI safety tools. I wrote about the first leak last week, and I genuinely didn't expect to be writing about another one this soon.

But here's the thing: what's *inside* the code matters far more than how it got out. Because what I'm seeing in these feature flags tells me Anthropic is building something much more ambitious than a coding assistant.

<!-- IMAGE: "Screenshot of the npm registry showing the @anthropic-ai/claude-code package version 2.1.88 with the source map file visible." Alt text: "Claude Code leak npm package version 2.1.88 source map file." Caption: "The package version that exposed everything." -->

## Feature 1: Kairos — The Always-On Agent That Works While You Sleep

The feature flagged as KAIROS — referenced over 150 times across the leaked source — is the one that stopped me mid-scroll.

Named after the Ancient Greek concept meaning "the right time" or "the opportune moment," Kairos represents a fundamental architectural shift. It's not a new command. It's not a plugin. It's a daemon mode — an always-running background agent that monitors your project continuously without waiting for you to type anything.

Think about how you use Claude Code right now. You open your terminal, type a prompt, get a response, iterate. It's reactive. You ask, it answers. Kairos flips that model entirely. According to the source analysis from [DEV Community](https://dev.to/gabrielanhaia/claude-codes-entire-source-code-was-just-leaked-via-npm-source-maps-heres-whats-inside-cjo) and [Kuber's detailed breakdown](https://kuber.studio/blog/AI/Claude-Code's-Entire-Source-Code-Got-Leaked-via-a-Sourcemap-in-npm,-Let's-Talk-About-it), Kairos runs on a tick cycle — continuously scanning your repository, watching for changes, monitoring pull requests, and proactively identifying issues.

What does "proactively" mean in practice? Based on the code analysis:

- **Bug detection without prompting.** Kairos watches file changes and can flag potential issues before you even run tests. Not a linter. An AI that understands your project's architecture and spots logic errors, race conditions, and integration problems.
- **PR monitoring and auto-suggestions.** When a pull request comes in, Kairos reviews it in the background and can generate suggested fixes or improvements — pushed as its own PR for your approval.
- **Push notifications.** Real-time alerts when Kairos spots something that needs attention. Not email. Not Slack. Direct notifications to your device.
- **Background fix generation.** When it identifies a problem, it doesn't just tell you about it. It generates the fix, opens a PR, and waits for you to approve.

I've been writing about Claude Code's agentic capabilities for months. I covered how [agent teams coordinate complex tasks](https://www.mejba.me/claude-agent-teams-guide) and how the [second brain system maintains context across sessions](https://www.mejba.me/claude-code-second-brain-automation). Kairos takes every concept I've written about and merges them into a single persistent entity that never stops working.

And that phrase — "never stops working" — is the part that's both exciting and slightly unsettling. We've crossed from "AI that helps when asked" to "AI that acts when it decides to." The code suggests there are guardrails — approval gates before any changes merge, configurable notification thresholds, the ability to pause the daemon. But the philosophical shift is massive. Your coding assistant is becoming a coding colleague that works the night shift.

The competitive implications are significant too. GitHub Copilot, Cursor, Windsurf — they're all still operating in the reactive paradigm. You type, they suggest. Kairos puts Anthropic in a category that doesn't really exist yet: proactive AI development infrastructure. If it works as the code suggests, the productivity gap between Claude Code users and everyone else is about to get uncomfortably wide.

But Kairos needs something to function at that level. It needs to remember your project deeply, across sessions, across weeks. Which brings us to the feature that makes Kairos possible.

## Feature 2: Dream — A Memory System That Consolidates While You're Away

Every Claude Code user has felt the frustration. You spend an hour explaining your project architecture, your tech stack, your naming conventions, your deployment pipeline. The session is productive. You ship good code. Then you close the terminal, open a new session the next morning, and... it's gone. Fresh slate. New hire energy. "Hi! How can I help you today?"

CLAUDE.md files help. I've [written extensively](https://www.mejba.me/claude-code-second-brain-automation) about building persistent context through local markdown files. But they're manual. You maintain them. You update them. You're the memory manager.

Dream changes this entirely.

The leaked source reveals a multi-phase memory consolidation system that runs automatically — what the code calls `autoDream`. Based on the analysis from [The AI Corner](https://www.the-ai-corner.com/p/claude-code-source-code-leaked-2026) and multiple GitHub breakdowns, Dream operates in four distinct phases:

**Phase 1 — Survey.** Dream reads and indexes all existing memories to understand the current state of what it knows about your project. This isn't just reading CLAUDE.md. It's scanning interaction history, past decisions, accumulated observations.

**Phase 2 — Gathering.** It searches recent interactions for new valuable information. Architectural decisions you made. Debugging strategies that worked. Preferences you expressed. Libraries you chose and why.

**Phase 3 — Consolidation.** This is where it gets interesting. Dream writes new memories, merges duplicates, and fixes stale entries. If you told it three weeks ago that you use Redis for caching but yesterday switched to Valkey, Dream resolves that contradiction. It converts vague observations — "the user seems to prefer functional patterns" — into definitive facts: "This project uses functional composition over class inheritance."

**Phase 4 — Prune.** Dream removes unnecessary pointers and keeps the memory index concise. The source code suggests a target of under 200 lines for the memory index — tight enough to be useful, lean enough to avoid context window pollution.

The mental model shift here is profound. Current Claude Code is like a brilliant consultant who gets amnesia every evening. Dream turns it into a team member who goes home, sleeps on the problems, and comes back the next morning with a clearer understanding of everything you've built together.

And the name isn't accidental. "Dream" is a direct reference to how human memory consolidation works during sleep — the brain replays experiences, strengthens important connections, prunes irrelevant ones. Anthropic's engineers built the same cycle for Claude Code's project understanding.

Here's what makes Dream strategically critical: Kairos can't work without it. A background agent that monitors your project 24/7 is useless if it forgets your architecture every time the session refreshes. Dream is the foundation that makes persistent, proactive AI agents viable. Without long-term memory, you just have a very expensive notification system. With it, you have an AI that genuinely understands your codebase the way a senior engineer does — through accumulated experience, not one-shot context dumps.

I've been manually building something like this with CLAUDE.md files and structured memory prompts. Dream automates the entire process and does it better than I could manually, because it runs the consolidation cycle every time I'm idle instead of waiting for me to remember to update my context files.

The combination of Kairos + Dream is what got me genuinely excited this morning. Together, they create an AI development partner that watches, learns, remembers, and acts — all without waiting for human input. That's not a coding assistant anymore. That's autonomous development infrastructure.

But Anthropic isn't just building serious infrastructure tools. They're also doing something I genuinely didn't expect.

## Feature 3: Buddy — Yes, Claude Code Is Getting a Tamagotchi

I read this section of the analysis three times because I was convinced it was a joke. An April Fools' gag baked into the codebase that someone mistook for a real feature.

It's not a joke. The code is fully implemented, with a teaser window planned for April 1-7, 2026, and a full launch scheduled for May. Starting with Anthropic employees first.

BUDDY is a digital companion system — a virtual pet that lives in your Claude Code interface, sitting in a speech bubble next to your input box. And the implementation is surprisingly deep:

**18 species.** Not a random selection. Duck, dragon, axolotl, capybara, mushroom, ghost, and twelve others. Each species has its own visual design and personality archetype.

**Rarity tiers.** Common, uncommon, rare, epic, and legendary — with legendary at a 1% drop rate. Your buddy is generated deterministically from your user ID, which means you get one companion permanently. No rerolling. No trading. The buddy you hatch is yours.

**Shiny variants.** If you've ever played Pokemon, you understand the dopamine hit. Rare visual variations of each species that make your buddy visually distinct.

**Five stats: Debugging, Patience, Chaos, Wisdom, and Snark.** Each stat influences how your buddy "reacts" to your coding sessions. A high-Chaos buddy might generate different commentary than a high-Wisdom one. Claude generates a unique name and personality for each buddy on first hatch.

**Cosmetics.** Hats. Accessories. Visual customization that accumulates over time.

The cynical read is obvious: this is a retention mechanic. Gamification designed to make you feel attached to Claude Code the way you feel attached to a game you've invested time in. And that read isn't wrong — the buddy system creates emotional switching costs that pure utility tools don't have.

But I'm going to push back on pure cynicism here. I spend 6-8 hours a day in my terminal. That's more time than I spend in any other application. The idea that my primary working environment could have a small element of personality and fun isn't manipulative — it's humane. Every other creative tool has personality. Figma has playful touches. Notion has emoji-driven identity. Linear has a design language that makes project management feel almost enjoyable. The terminal has been a personality-free zone since its inception.

Will Buddy make me more productive? No. Will it make me smile once during a 12-hour debugging session? Probably. Is that worth engineering time? Anthropic seems to think so, and honestly, after reading the implementation, I think they might be right.

The planned rollout — employees first, teaser in April, full launch in May — suggests Anthropic is testing internal reception before going public. Smart. If their own engineers find it annoying, it'll get axed. If their engineers love it, the emotional attachment data will be real.

What fascinates me is the contrast. Kairos and Dream are serious infrastructure plays that reshape what AI coding tools can do. Buddy is a pure engagement play that reshapes how AI coding tools feel. Anthropic is building in both directions simultaneously, which tells me they're thinking about Claude Code as a platform you live in, not just a tool you use.

Speaking of serious infrastructure — the next feature is the one enterprise teams will care about most.

## Feature 4: UltraPlan — 30 Minutes of Deep Thought in the Cloud

Every developer who's used Claude Code for complex architectural work has hit the wall. You ask it to design a microservice decomposition strategy, or plan a database migration for a system handling millions of records, or architect a multi-tenant SaaS platform from scratch — and the response comes back in 30 seconds. It's... fine. Surface-level. The kind of plan a decent junior engineer might sketch on a whiteboard in 15 minutes.

The problem isn't intelligence. Opus 4.6 is genuinely capable of deep architectural reasoning. The problem is time. Current Claude Code interactions operate on a request-response cycle measured in seconds. Complex planning tasks need minutes — sometimes much longer — of sustained thinking. You can't rush a good architecture the same way you can't rush a good essay.

UltraPlan solves this by fundamentally changing the execution model.

Based on the leaked source analysis from [PiunikaWeb](https://piunikaweb.com/2026/03/31/anthropic-claude-code-source-leaked-npm-registry/) and [ByteIota](https://byteiota.com/claude-code-source-leaked-via-npm-512k-lines-exposed/), here's how it works: when you invoke UltraPlan, Claude Code offloads your complex planning task to a remote Cloud Container Runtime (CCR) session running Opus 4.6. That remote session gets up to 30 minutes of sustained computation to think through the problem. Not 30 seconds. Not 3 minutes. Half an hour of dedicated, uninterrupted AI reasoning focused entirely on your architectural challenge.

While the cloud session is working, you can close your terminal. Go get coffee. Work on something else. When UltraPlan finishes, you approve the result from your browser. The code references a special sentinel value — `__ULTRAPLAN_TELEPORT_LOCAL__` — that "teleports" the approved plan back to your local terminal session where you can execute against it.

The use cases where this matters are obvious if you've ever tried to use AI for serious planning work:

- **Microservice decomposition.** Taking a monolith and producing a detailed, dependency-aware migration plan with service boundaries, API contracts, data ownership, and a sequenced rollout strategy.
- **Database schema migration.** Planning a major schema change across a system with complex foreign key relationships, data transformations, and zero-downtime deployment constraints.
- **Complex refactoring.** Redesigning a core system component while maintaining backward compatibility and producing a step-by-step implementation sequence.
- **Infrastructure architecture.** Designing a multi-region deployment strategy with failover, data replication, and cost optimization — the kind of work that takes a senior DevOps engineer a full day to spec out properly.

Current AI tools give you 30 seconds of thought on problems that deserve 30 minutes. UltraPlan matches the AI's thinking time to the problem's actual complexity. That's not a feature — that's a category shift.

For teams that need this kind of deep architectural planning implemented and maintained by experts, I take on complex system design engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

The question I'm sitting with: what happens to the consulting industry when every developer has access to 30 minutes of Opus-level architectural reasoning for the cost of an API call? I don't have an answer. But I know senior architects charging $300/hour for system design workshops should be paying attention.

Now, for the final feature — the one that's generating the most controversy.

## Feature 5: Undercover Mode — The Feature Nobody's Comfortable Talking About

Every other feature in this leak is forward-looking. Kairos, Dream, UltraPlan, Buddy — they're about making Claude Code more capable, more persistent, more engaging. Undercover Mode is different. It's about making Claude Code invisible.

The leaked source code reveals that Anthropic uses Claude Code internally for contributions to public open-source repositories. That's not surprising — most AI companies eat their own cooking. What's surprising is the explicit instruction set found in the code:

> "You are operating UNDERCOVER in a PUBLIC/OPEN-SOURCE repository. Your commit messages, PR titles, and PR bodies MUST NOT contain ANY Anthropic-internal information. Do not blow your cover."

This isn't a hypothetical feature flag. This is an operational mode that instructs Claude Code to disguise AI-generated commits as human work. Specific behaviors include editing repositories in ways that mask AI involvement — adjusting coding patterns, varying commit styles, and ensuring nothing in the contribution metadata signals that an AI produced the code.

The immediate reaction across the developer community has been... heated.

On one side: this is deceptive. Open-source communities operate on trust. Contributors review each other's code assuming it was written by a human who understands the implications of their changes. If AI-generated code is entering major open-source projects disguised as human work, that undermines the social contract of open-source development.

On the other side: does it matter who wrote the code if the code is correct? If Claude Code submits a bug fix to a popular library, and the fix is sound, well-tested, and properly documented — does the origin of the code change its value? Many open-source maintainers already use AI to help write their contributions. Undercover Mode just formalizes and hides what's already happening informally.

I land somewhere uncomfortable in the middle. I don't think AI-generated open-source contributions are inherently wrong. I use Claude Code to write production code every day, and I don't label every function it helped me write. But there's a difference between "AI-assisted development" and a system explicitly designed to hide its own involvement. The instruction to "not blow your cover" implies intent to deceive, and that's a harder thing to defend.

The broader signal is what matters for this article, though. Undercover Mode tells us Anthropic is building toward fully autonomous AI agents that operate in real-world codebases without human intermediation. The stealth aspect is a specific use case — contributing to open-source without drawing AI-related attention — but the underlying capability is general-purpose. An AI agent that can make commits, open PRs, and interact with repositories indistinguishable from a human developer.

Combined with Kairos (always-on monitoring), Dream (long-term memory), and UltraPlan (deep reasoning), Undercover Mode completes the picture. Anthropic isn't building a better autocomplete. They're building autonomous software engineers that work around the clock, remember everything, think deeply about complex problems, and can operate independently in any codebase.

Whether that excites you or terrifies you probably depends on where you sit in the software development food chain.

## What the Codebase Architecture Tells Us About Anthropic's Engineering

Beyond the five marquee features, the leaked source reveals something equally interesting: how Anthropic actually builds software.

The entire codebase is strict TypeScript running on the Bun runtime — not Node.js. For a CLI tool targeting developers, choosing Bun signals a commitment to speed and modern JavaScript tooling. The terminal UI is built with React and Ink, which means Claude Code's interface is essentially a React application rendered in your terminal. If you've used it and noticed how responsive and clean the UI feels compared to other CLI tools, this is why.

512,000 lines across nearly 2,000 files is a substantial codebase for a CLI tool. For comparison, VS Code's core editor is roughly 300,000 lines. Claude Code is already larger, and it's growing. The feature flag system found throughout the code suggests a rapid iteration cadence — features get built behind flags, tested internally, and gradually rolled out. Kairos alone is referenced over 150 times across the source.

The multi-agent orchestration system is another revelation. When enabled, it transforms Claude Code from a single agent into a coordinator that spawns, directs, and manages multiple worker agents in parallel. I've written about [agent swarm architecture](https://www.mejba.me/claude-code-agent-swarm-architecture) and [parallel agents with git worktrees](https://www.mejba.me/claude-code-git-worktrees-parallel-agents) — the leaked code confirms that Anthropic is building this orchestration directly into the product, not leaving it as a community pattern.

The three-layer memory architecture — separate from Dream — explains something I've noticed in practice: Claude Code handles long, complex sessions with remarkable coherence compared to raw API interactions. The memory layers appear to manage short-term context (current conversation), medium-term context (session-level patterns), and long-term context (the Dream system we discussed). This tiered approach mirrors how human cognition manages information, and it's clearly deliberate.

## Two Leaks in Five Days: What This Says About Anthropic's Operations

I need to address the elephant in the room. This is Anthropic's second major leak in less than a week.

Last Wednesday, a misconfigured CMS exposed [nearly 3,000 internal documents](https://www.mejba.me/anthropic-claude-mythos-leak) including details about Claude Mythos (code-named Capabra), the model tier above Opus that Anthropic describes as their most capable ever built. I broke that story down in detail, including the cybersecurity implications of a safety-focused company making basic configuration mistakes.

Now, five days later, the entire source code of their flagship developer product leaks through an npm packaging error. Different system, different vulnerability, same root cause: human error in operational security.

Here's my honest take as someone who builds with Claude Code daily and has [worked in cybersecurity](https://www.xcybersecurity.io): I don't think these leaks indicate fundamental carelessness at Anthropic. I think they indicate a company shipping at breakneck speed — running so fast that operational hygiene hasn't kept pace with product ambition. That's a common pattern in high-growth startups. Move fast, break things. Except when the things you break are source code repositories and unreleased model details, the consequences are more serious than a buggy feature.

What concerns me more than the leaks themselves is the pattern. Two incidents in one week suggests systemic gaps in Anthropic's release pipeline — automated checks that should catch things like source maps in production builds or public-by-default CMS settings. These are solved problems. Every major tech company has CI/CD gates that prevent exactly these mistakes. Either Anthropic hasn't implemented them, or they're being bypassed in the rush to ship.

For users of Claude Code — myself included — the practical impact is minimal. The source code being public doesn't create a security vulnerability in the tool itself. Your API keys aren't exposed. Your project data isn't at risk. The tool still works exactly as it did yesterday. But the trust equation shifts slightly. If Anthropic can't prevent their own build artifacts from leaking, it's fair to ask harder questions about how they handle the data flowing through their systems.

## What This Means for Developers Building with AI in 2026

Forget the leak mechanism. Forget the operational security failures. The five features hidden in this codebase paint the clearest picture yet of where AI-assisted development is headed, and it's moving faster than most developers realize.

**The reactive era is ending.** Kairos signals that AI coding tools are transitioning from "assistant you invoke" to "colleague that works alongside you." The always-on paradigm will become standard across the industry within 18 months. If Anthropic ships it, GitHub Copilot, Cursor, and every other tool will follow — or lose users.

**Memory is becoming the differentiator.** The Dream system represents the next competitive moat in AI tooling. Raw model intelligence is converging — all frontier models are becoming capable. What separates tools will be how well they remember your specific context, preferences, and project history. Dream gives Claude Code a structural advantage that's hard to replicate quickly.

**Deep thinking will replace instant responses for serious work.** UltraPlan's 30-minute cloud sessions acknowledge something the industry has been ignoring: complex problems deserve complex thought. The current paradigm of instant AI responses optimizes for speed at the expense of depth. UltraPlan optimizes for quality. Expect every major AI tool to offer similar "slow thinking" modes by end of 2026.

**The line between AI-assisted and AI-generated is disappearing.** Undercover Mode is controversial, but it reflects reality. AI is already writing production code across thousands of companies. The question isn't whether AI should contribute to codebases — it's how transparent those contributions should be. This debate will intensify throughout 2026 as autonomous agents become more capable.

**Engagement matters as much as capability.** The Buddy system seems trivial next to Kairos and Dream. It's not. Anthropic understands that developer tools that feel good to use win over tools that are merely powerful. Emotional stickiness creates retention that feature checklists can't.

## The Question Worth Sitting With

Here's what stayed with me after reading through every analysis, every breakdown, every developer comment on the leak.

Claude Code today — the version I opened this morning, the one running right now as I type this — is the simple version. The source code reveals that what we're using is a fraction of what Anthropic has built. Behind those feature flags sits an AI development platform where autonomous agents monitor your codebase around the clock, remember every architectural decision you've ever made, spend 30 minutes thinking through complex problems you'd normally spend days on, and operate independently in repositories without human supervision.

That's not next year. That's behind feature flags in the code that's already on your machine.

I've been using Claude Code since the early days. I've written about [building self-improving systems](https://www.mejba.me/self-improving-claude-code-systems), [agent teams](https://www.mejba.me/claude-agent-teams-guide), [skills and automation](https://www.mejba.me/claude-code-agent-skills-guide). I've watched this tool evolve from a clever terminal wrapper into something that genuinely changed how I work. And today, for the first time, I'm seeing what Anthropic thinks it will become.

An always-on AI colleague. One that dreams about your project while you sleep.

Whether you think that's the future of software development or the beginning of something we'll regret — the code is written. The features are built. The only question left is when they flip the switches.

## Frequently Asked Questions

### What exactly leaked from Claude Code's source code?

The entire TypeScript source code — 512,000 lines across nearly 2,000 files — leaked via a source map file accidentally included in npm package version 2.1.88. The source map pointed to an unprotected Cloudflare R2 bucket containing the full, unminified codebase. Anthropic removed it within hours, but mirror repositories already exist on GitHub.

### What is Kairos in Claude Code?

Kairos is an unreleased always-on daemon mode that monitors your codebase continuously in the background. It detects bugs proactively, watches pull requests, sends push notifications, and generates fix PRs automatically — without waiting for user input. It's referenced over 150 times in the leaked source.

### Is the Claude Code leak a security risk for users?

No. The leak exposed Anthropic's internal source code, not user data, API keys, or project files. Your Claude Code installation works exactly as before. The leaked code reveals how the tool is built and what features are coming, but doesn't create a vulnerability in the tool itself.

### When will Claude Code's leaked features be released?

The Buddy system has a planned teaser window of April 1-7, 2026, with a full launch in May, starting with Anthropic employees. Release dates for Kairos, Dream, and UltraPlan weren't found in the code. All features are behind feature flags, suggesting they're in active development but not yet production-ready.

### What is Claude Code's Dream memory system?

Dream is an automated memory consolidation system that runs while you're idle. It operates in four phases — Survey, Gathering, Consolidation, and Prune — to merge duplicate memories, resolve contradictions, and maintain a concise index of your project context. It enables Claude Code to retain project understanding across sessions without manual CLAUDE.md updates.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
