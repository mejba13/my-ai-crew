**BRAND:** mejba.me
**TITLE:** Claude Code Ultra Review: I Watched It Hunt Bugs
**META TITLE:** Claude Code Ultra Review Tested: Multi-Agent Bug Hunting
**SLUG:** claude-code-ultra-review-tested
**PRIMARY KEYWORD:** Claude Code Ultra Review
**SECONDARY KEYWORDS:** multi-agent code review, Claude Code bug detection, pull request review AI
**META DESCRIPTION:** I tested Claude Code's hidden Ultra Review feature — a multi-agent system that finds and verifies bugs in PRs. Here's how it works and what it actually caught.
**TAGS:** Claude Code, AI Code Review, Multi-Agent Systems, Developer Tools, Tutorial
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand Ultra Review's four-stage architecture, know how it differs from the standard /review command, and be able to evaluate whether multi-agent verification should be part of their own PR workflow.

---

# Claude Code Ultra Review: I Watched It Hunt Bugs Across an 11,000-Line PR

I was reviewing a pull request — a voice calling feature, roughly 11,000 lines of changed code — when I noticed something odd in Claude Code's interface. A new option I hadn't seen before. Not the standard `/review` command I'd been using for months. Something called Ultra Review, sitting behind what looked like a feature flag that hadn't been fully hidden.

Naturally, I clicked it.

What happened over the next seventeen minutes changed how I think about automated code review entirely. Not because it found bugs — any decent linter finds bugs. Because it found bugs, then proved they were real before telling me about them. And that second part? That's the part nobody else is doing.

The standard `/review` in Claude Code is already solid. It dispatches multiple agents to scan your diff, and on large PRs — anything over 1,000 lines — [Anthropic's own data shows](https://claude.com/blog/code-review) 84% of reviews surface findings, averaging 7.5 issues per review. Those are strong numbers. But there's a problem baked into any system that finds bugs without verifying them: false positives. Every false positive erodes trust. After the third time you investigate a flagged issue only to discover it's not actually a problem, you start ignoring the tool. That's human nature, and it's the reason most automated review tools eventually get turned off.

Ultra Review exists to solve that exact failure mode. And after watching it work on a real, messy, production-scale PR, I'm convinced the verification step isn't just a nice addition — it's the architectural insight that makes multi-agent review actually trustworthy.

Here's everything I learned from testing it, breaking it down, and reverse-engineering how it works under the hood.

---

## What Ultra Review Actually Is — And Why It Exists

Ultra Review is a cloud-powered, multi-stage code review system that goes significantly beyond what the standard `/review` command does. As of April 2026, it's not broadly available — it was discovered through reverse engineering of Claude Code's source, particularly after the [now-famous source map leak on March 31, 2026](https://www.geeky-gadgets.com/claude-code-undercover-mode/), where a 59.8MB source map file accidentally shipped in npm package `@anthropic-ai/claude-code` v2.1.88, exposing 1,884 TypeScript source files and a catalogue of unreleased features.

Ultra Review was one of those features. And unlike some of the more experimental discoveries from that leak — like BUDDY the AI pet or Undercover Mode — Ultra Review solves a real, pressing engineering problem.

The core insight is simple but powerful: finding bugs and confirming bugs are two fundamentally different tasks. The standard review bundles them together. Ultra Review separates them into distinct stages with independent agents handling each one. This separation is what makes the difference between a tool that generates a list of "possible issues" and a tool that hands you a list of "confirmed bugs with evidence."

Before I walk through the architecture, you need to understand the scale of what this thing processes. The PR I tested it on — that voice calling feature — wasn't a clean, isolated addition. It touched authentication flows, WebRTC configuration, UI components, state management, and error handling across multiple services. Eleven thousand lines of code across dozens of files. The kind of PR that makes senior engineers groan when it lands in their review queue on a Friday afternoon.

Ultra Review didn't groan. It spun up its agents and got to work.

---

## The Four Stages: How Ultra Review Hunts Bugs

The entire process runs on Anthropic's cloud infrastructure — not on your local machine. This matters because the computational cost of running multiple agents simultaneously would demolish your local token budget. By offloading to the cloud, Ultra Review can spin up agent fleets without you worrying about consumption from your rolling usage window.

Here's how the four stages break down.

### Stage 1: Setup

The review session initializes and provisions cloud resources. Ultra Review spawns its sub-agent fleet — a default of 5 agents, though the system supports up to 20 (likely reserved for Enterprise tier customers based on the configuration flags I found). Each agent gets its own context window and its own perspective on the codebase.

This setup phase is fast. On my 11,000-line PR, it took roughly 90 seconds before the agents were dispatched and working. You see a progress indicator in Claude Code's interface showing the fleet spinning up, which is a nice touch — it gives you confidence that something meaningful is happening, not just a loading spinner hiding dead time.

### Stage 2: Find

This is where things get interesting. The fleet of sub-agents independently explores different paths through the codebase to detect potential bugs. "Independently" is the key word here. Each agent isn't just scanning different files — they're exploring different execution paths, different orderings, different angles of the same code.

Why does ordering matter? Because certain bugs only reveal themselves when you read the code in a specific sequence. If you start with the authentication module and work toward the WebRTC handler, a race condition might be obvious. But if you start with the UI components and work backward, that same race condition is invisible because you haven't built up the necessary mental model of the auth state.

By having five agents approach the code from different directions — potentially with different "personas" focusing on different concern domains like billing, security, or data integrity — Ultra Review catches bugs that any single-pass review would miss.

On my test PR, the Find stage identified **64 candidate bugs**. Sixty-four. That number initially made me skeptical. No way a single PR has 64 real bugs, even at 11,000 lines. And I was right to be skeptical — but that's exactly what the next stage addresses.

### Stage 3: Verify

This is Ultra Review's secret weapon. A separate set of sub-agents — distinct from the ones that found the candidates — independently verify each bug for validity. Each verification agent receives a candidate bug description along with the full context needed to evaluate it: the PR title, the PR description, the relevant code sections, and the claimed issue.

The verification agent's job is straightforward but critical: determine with high confidence whether this is a real bug or a false positive. It's essentially an adversarial system — the Find agents are optimized to be sensitive (catch everything, even if some are wrong), while the Verify agents are optimized to be specific (confirm only what's actually broken).

According to [Anthropic's documentation on their review system](https://code.claude.com/docs/en/code-review), they use Opus-class sub-agents for bugs and logic issues, and Sonnet-class agents for things like CLAUDE.md violations and style concerns. This model-matching makes sense — you want your heaviest reasoning capability aimed at the hardest verification problems.

On my PR, the Verify stage took those 64 candidates and confirmed a subset as genuine issues. The rest were either false positives, stylistic concerns that didn't rise to the level of bugs, or edge cases that were actually handled elsewhere in the codebase. That filtering is the entire value proposition. Without it, I'd be staring at a list of 64 items, manually triaging each one. With it, I got a curated, high-confidence list of things that genuinely needed fixing.

### Stage 4: Dedup

The final stage merges duplicate findings. When five agents independently explore the same codebase, they'll inevitably discover the same bug from different angles. Agent 1 might flag a null pointer issue from the caller's perspective. Agent 3 might flag the same issue from the callee's perspective. They're the same bug, reported twice with different framing.

Deduplication combines these into a single, enriched finding that includes context from multiple discovery paths. This actually makes the final bug report more useful — instead of a single perspective on the issue, you get a multi-angle view that often makes the root cause more obvious.

The whole process — Setup through Dedup — took 17 minutes on my 11,000-line PR. Compare that to the standard `/review`, which would have completed in 3 to 4 minutes but without the verification layer. I'll take the extra 13 minutes every time for a PR of this size.

---

## How It Stacks Against the Standard /review

I've been using Claude Code's standard `/review` command since it launched in March 2026. It's good. On small PRs under 50 lines, it's fast and catches obvious issues — [Anthropic reports a 31% finding rate on small PRs](https://claude.com/blog/code-review), averaging 0.5 issues, which feels about right based on my usage. For quick feature additions or config changes, it's the right tool.

But the standard review has a trust problem at scale.

On larger PRs, it flags more issues — that 84% finding rate I mentioned earlier. The problem is that when you're looking at 7 or 8 flagged issues on a big PR, you need to manually verify each one. Some are real. Some are the agent misunderstanding context. Some are technically correct but practically irrelevant because another part of the system handles the edge case. That manual triage takes time. Often more time than the review itself saved.

Here's where the two approaches diverge sharply:

**Speed vs. Accuracy Tradeoff.** Standard review prioritizes speed — 3 to 4 minutes and you have results. Ultra Review prioritizes accuracy — 10 to 20 minutes, but the results you get have been independently verified. For a quick PR on a feature branch? Standard review. For a 2,000-line PR that touches payment processing? Ultra Review. Every time.

**False Positive Handling.** Standard review leaves false positive filtering to you. Ultra Review builds it into the pipeline. According to Anthropic's own stats, less than 1% of findings from the full review system are marked incorrect by engineers. That's a remarkable accuracy rate, and the verification stage is the reason.

**Resource Usage.** Standard review runs on your existing Claude Code session resources. Ultra Review runs entirely on Anthropic's cloud infrastructure with dedicated compute. You don't pay per-session from your rolling window — though the current pricing model for code review runs approximately [$15 to $25 per review depending on code complexity](https://code.claude.com/docs/en/code-review).

**Depth of Analysis.** Standard review scans the diff and immediate context. Ultra Review's multi-agent fleet performs what I'd call "lifecycle analysis" — agents trace data flow across module boundaries, follow function calls through multiple layers of abstraction, and evaluate state management implications that span files. This depth is what catches the subtle bugs that surface-level scanning misses.

If you're thinking "I'll just run standard review first, then Ultra Review for the big PRs" — that's exactly the workflow I'd recommend. Quick review for fast feedback, deep review for critical changes. They're complementary, not competing.

---

## What the Sub-Agent Architecture Reveals About the Future of Code Review

The most interesting thing about Ultra Review isn't the feature itself. It's the architectural pattern it establishes.

The idea of using multiple independent agents with different perspectives, followed by a separate verification layer, is transferable to almost any analysis task. Bug detection is just the first application. The same pattern could work for security audits, performance analysis, accessibility reviews, documentation completeness checks — any domain where finding issues and confirming issues are separable concerns.

I found this pattern compelling enough that I started experimenting with my own version. I built a custom fleet review skill that combines agents from different providers — Claude Code agents alongside [OpenAI's Codex](https://mejba.me/blog/codeex-app-openai-first-look) — with a verification stage that requires consensus across models before flagging an issue. Cross-model consensus is a powerful signal. If Claude and Codex independently agree that something is a bug, the confidence level goes through the roof compared to a single model's assessment.

The fleet size flexibility is worth noting too. Ultra Review defaults to 5 sub-agents, but the configuration supports up to 20. For a standard PR, 5 agents provide good coverage. But imagine running 20 agents against a critical infrastructure change — a database migration, a payment system refactor, or a security-sensitive authentication rewrite. The thoroughness scales with the risk.

Enterprise teams will likely get access to those larger fleet sizes first. If your organization runs on the Team or Enterprise plan — currently the only tiers where Code Review is available as a research preview — you're already positioned to use this when it goes broader.

This multi-agent verification pattern also has implications for how we think about [AI agent orchestration](https://mejba.me/blog/claude-code-agent-swarm-architecture) more broadly. The agent swarm architecture I wrote about previously focuses on task parallelization — multiple agents working on different subtasks simultaneously. Ultra Review adds a new dimension: agents working on the same task independently, then cross-checking each other's work. It's the difference between division of labor and peer review. Both are valuable. Combining both is where things get powerful.

---

## Practical Setup: Running Ultra Review Today

Let me be direct about availability. As of April 2026, Ultra Review is not a publicly documented feature with a big "Enable" button. It was discovered through source code analysis and is accessible to a limited number of users. The broader Code Review feature — which shares much of the same multi-agent architecture — is available in research preview for Claude Code Team and Enterprise customers.

Here's what you need to know if you want to use the review capabilities that are available right now.

**Step 1: Ensure you're on a qualifying plan.** Code Review requires Team or Enterprise. The Max 20x plan at $200/month gives you priority access to new features, which is relevant here. If you're on Pro ($20/month) or Max 5x ($100/month), you'll need to upgrade or wait for broader availability.

**Step 2: Have an admin enable Code Review for your organization.** This isn't a per-user toggle — it's an org-level setting. Once enabled, reviews can trigger automatically on PR open, on every push, or on manual request, depending on your repository's configured behavior.

**Step 3: Use the /review command in Claude Code.** For the standard review, this is straightforward — run it against your current branch or a specific PR. The system handles agent provisioning, analysis, and reporting automatically.

**Step 4: For larger PRs, allocate time.** Standard reviews finish in 3 to 4 minutes. The deeper multi-agent review with verification takes 10 to 20 minutes. Don't start it five minutes before a meeting. Start it, go grab coffee, come back to verified results.

**Pro tip:** If you're running reviews on PRs that touch critical systems — anything involving payments, authentication, data access controls, or infrastructure configuration — the 10-to-20-minute wait for verified results is not optional. It's the minimum responsible approach. I'd rather spend 20 minutes getting verified findings than 3 hours debugging a production issue that a surface-level review missed.

If you'd rather have someone set up a comprehensive code review workflow with multi-agent verification tailored to your team's codebase, I take on exactly these kinds of automation engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

---

## The Honest Assessment: Where Ultra Review Falls Short

I'd be doing you a disservice if I pretended this was flawless. It's not. Here's what I noticed during testing.

**The time cost is real.** Seventeen minutes for a single review is fine when you're doing final checks on a major PR. It's not fine when you're iterating quickly on a feature branch and pushing five commits in an hour. For that workflow, the standard review — or even just your IDE's built-in analysis — is the right tool. Ultra Review is a scalpel, not a hammer.

**Limited availability kills the value proposition for most developers.** If you're a solo developer on the Pro plan, you can't use this yet. The Team and Enterprise requirements make sense from Anthropic's perspective — cloud-side multi-agent compute isn't cheap — but it means the developers who would benefit most from automated review (solo devs without a team to review their code) are the ones least likely to have access.

**The fleet size default may be conservative.** Five sub-agents worked well on my 11,000-line PR, but I suspect certain categories of bugs — particularly distributed system issues, subtle concurrency problems, or cross-service data consistency bugs — would benefit from more agents exploring more paths. The configuration supports up to 20, but I haven't been able to test larger fleets to confirm the improvement.

**It doesn't replace human review for architectural decisions.** Ultra Review is excellent at finding bugs — logic errors, null pointer risks, unhandled edge cases, security vulnerabilities. What it doesn't evaluate is whether the overall approach is right. Should this feature use WebRTC at all, or would WebSockets suffice? Should this state be managed client-side or server-side? Those are judgment calls that require understanding the product roadmap, the team's capabilities, and business constraints. A human reviewer still needs to make those calls.

**The cost adds up.** At $15 to $25 per review, running Ultra Review on every PR gets expensive fast. A team pushing 10 PRs a day is looking at $150 to $250 daily — roughly $3,000 to $5,000 monthly just for code review. That's worth it if it catches even one production bug per month that would have cost more to fix post-deployment. But it requires a conscious cost-benefit decision, not a blanket "review everything" policy.

---

## What This Means for Your Review Workflow

Here's the framework I've landed on after testing this for a week.

**Tier 1 — Every PR:** Run the standard `/review` command. Three to four minutes, catches the obvious stuff, builds the habit of automated review as part of your workflow. Think of it as your smoke detector — always on, catches the common fires.

**Tier 2 — Large or critical PRs:** Run Ultra Review (or the full multi-agent review when it's available on your plan). Any PR over 500 lines, any PR touching authentication or payments, any PR that makes you nervous. The 10-to-20-minute investment is cheap insurance against the kind of bugs that wake you up at 3 AM.

**Tier 3 — Infrastructure changes:** Run the deepest review available with the largest agent fleet you can access. Database migrations, API versioning changes, security policy updates. These changes have blast radiuses that justify maximum scrutiny.

This tiered approach also aligns with the [token optimization strategies](https://mejba.me/blog/claude-code-token-optimization) I've written about before. You're spending your most expensive resources (cloud compute, larger agent fleets, longer review times) on the changes with the highest risk. Standard changes get standard review. Critical changes get the full treatment.

The verification pattern Ultra Review introduces is, I believe, going to become standard practice in AI-assisted development within the next 12 months. Not just in Anthropic's tools — across the industry. Once developers experience the difference between "here are possible bugs" and "here are confirmed bugs with evidence," there's no going back to the unverified approach.

---

## The Pattern That Changes Everything Isn't the Feature — It's the Verification

I want to leave you with the insight that stuck with me most after testing Ultra Review.

The find-verify-dedup pipeline isn't just a code review technique. It's a general-purpose pattern for making AI systems trustworthy. Any time you have an AI generating claims — whether those claims are "this code has a bug" or "this marketing copy is off-brand" or "this financial model has an error" — running a separate, independent AI to verify those claims before presenting them to a human dramatically changes the reliability of the output.

The standard approach to AI tools is: AI generates output, human evaluates output. Ultra Review adds a middle step: AI generates output, different AI verifies output, human evaluates verified output. That middle step filters out the noise that makes humans stop trusting AI tools.

When I triggered Ultra Review on that 11,000-line voice calling PR, I was expecting a better version of the review I already knew. What I got was a fundamentally different relationship with the tool. I trusted the results in a way I'd never trusted automated review before. Not because the AI was smarter. Because the system was designed to prove its own findings before showing them to me.

That's the shift. Not smarter models — smarter systems built from multiple models checking each other's work. And if you take one thing from this entire breakdown, let it be this: the next time you build anything with AI agents, add a verification stage. Don't just let agents find things. Make them prove what they found. The difference in output quality will surprise you.

---

## Frequently Asked Questions

### What is Claude Code Ultra Review and how does it differ from /review?

Ultra Review is a multi-stage, cloud-powered code review system that adds independent bug verification and deduplication on top of the standard /review's multi-agent detection. The key difference is the verification stage — separate agents confirm each candidate bug before reporting it, reducing false positives to under 1%. Standard /review takes 3-4 minutes; Ultra Review takes 10-20 minutes but delivers verified results.

### How many sub-agents does Ultra Review use?

Ultra Review defaults to a fleet of 5 sub-agents for the Find stage, with the system supporting up to 20 agents. Each agent independently explores different execution paths through the codebase. Larger fleet sizes appear reserved for Enterprise-tier customers based on configuration flags discovered in the source code.

### Is Claude Code Ultra Review available on the Pro plan?

Not currently. The broader Code Review feature requires a Team or Enterprise plan and is available as a research preview as of April 2026. The Max 20x plan ($200/month) provides priority access to new features. Ultra Review itself was discovered through reverse engineering and remains limited to a small number of users.

### How much does a Claude Code review cost?

Anthropic prices code reviews on a token basis, with costs varying by code complexity. The estimated range is $15 to $25 per review on average. Reviews on small PRs under 50 lines cost less, while large PRs with thousands of lines of changes sit at the higher end of that range.

### Should I run Ultra Review on every pull request?

No. Use a tiered approach: standard /review for every PR (3-4 minutes, catches common issues), Ultra Review for large or critical PRs over 500 lines (10-20 minutes, verified results), and maximum-fleet reviews for infrastructure changes like database migrations or security updates. Match review depth to change risk.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
