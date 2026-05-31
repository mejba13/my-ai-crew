**BRAND:** mejba.me
**TITLE:** I Tested Claude Code's Ultra Plan — Here's the Truth
**META TITLE:** Claude Code Ultra Plan Tested: What Actually Works
**SLUG:** claude-code-ultra-plan-tested
**PRIMARY KEYWORD:** Claude Code Ultra Plan
**SECONDARY KEYWORDS:** ultraplan cloud planning, Claude Code planning mode, multi-agent planning
**META DESCRIPTION:** I tested Claude Code's Ultra Plan across 10 prompts. Here's what the cloud planning mode does, how it compares to local plan mode, and when it's worth it.
**TAGS:** AI Development, Claude Code, Ultra Plan, Cloud Planning, Practitioner Review
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly when Ultra Plan outperforms local planning, how its hidden multi-agent system works, and whether to adopt it into their workflow today.

---

I was halfway through a dependency migration — swapping tRPC v10 for v11 across a monorepo with 47 route definitions — when a friend dropped a message in our Discord: "Have you tried `/ultraplan` yet?"

My honest first reaction? Skepticism. I'd been using Claude Code's local plan mode religiously for months. Shift+Tab, review the plan, switch to edit mode, execute. The rhythm was second nature. Why would I hand that process off to some cloud-based planning session when my terminal workflow was already fast?

Then I ran `/ultraplan migrate all tRPC v10 routes to v11 with backward-compatible type exports` and watched something I didn't expect. Within seconds, my terminal handed me a URL. I opened it in the browser. And sitting there was a structured implementation plan that didn't just list the files that needed changing — it mapped dependency chains between routes, flagged three breaking type signatures I hadn't considered, and generated a Mermaid diagram showing the migration order that would minimize test failures along the way.

My local plan mode had never done that. Not once.

That was two weeks ago. Since then, I've run Ultra Plan across ten different prompts — simple model swaps, complex refactors, greenfield scaffolding, security audits — testing it against local plan mode on every single one. What I found was more nuanced than "Ultra Plan is better." The truth involves three hidden planning modes, an AB testing system most users don't know exists, and a very specific set of scenarios where Ultra Plan is genuinely transformative versus where it's just a fancier UI for the same output.

Here's everything I learned.

## What Ultra Plan Actually Is (And Why It Exists)

Before I get into the test results, you need the mental model. Because Ultra Plan isn't just "plan mode but in the cloud." The architecture is fundamentally different, and understanding that difference changes how you use it.

When you run local plan mode with Shift+Tab in Claude Code, the planning happens inside your terminal session. Same context window. Same model instance. Same constraints. The AI reads your codebase, thinks through the changes, and presents a plan — all within the boundaries of your current conversation.

Ultra Plan breaks that model entirely. When you type `/ultraplan` followed by your prompt, Claude Code spins up a dedicated planning session in Anthropic's Cloud Container Runtime — what they call CCR. That remote session gets Opus 4.6, up to 30 minutes of dedicated compute time, and access to your repository through a cloud-synced snapshot. The planning happens off your machine, in an environment purpose-built for deep analysis.

The practical difference? Your terminal stays free. While Ultra Plan is crunching through your migration strategy in the cloud, you can keep coding, run tests, or spin up another Ultra Plan session for a different task entirely. I've had three Ultra Plan sessions running simultaneously while I was debugging a CSS issue locally. Try doing that with local plan mode.

Once the plan is ready, you get a session URL that opens in your browser. And this is where the experience diverges sharply from anything in the terminal.

## The Web UI That Changes How You Review Plans

I'll be direct: the web interface is the single biggest quality-of-life improvement Ultra Plan brings. And I say that as someone who lives in the terminal.

When a plan lands in your browser, you're looking at a rich document — not a wall of markdown scrolling past in your terminal. Headers. Collapsible sections. Inline code blocks with syntax highlighting. And critically, two features that make collaborative planning actually work: inline commenting on specific parts of the plan, and emoji reactions for quick signaling.

That sounds trivial. It isn't.

Here's why. When I review a plan in the terminal, my feedback loop looks like this: read the plan, scroll back up to the part I disagree with, type "actually, for step 3, I'd rather use the repository pattern instead of direct queries," wait for the AI to regenerate the entire plan, re-read the whole thing. With the web UI, I click on step 3, type my comment inline, and ask for a targeted revision. The AI updates that section without touching the rest. The iteration speed is dramatically faster.

The Mermaid diagrams are inconsistent — sometimes they appear, sometimes they don't (more on why in a moment). But when they do show up, they're genuinely useful. For the tRPC migration, Ultra Plan generated a dependency flowchart showing which routes depended on shared type definitions, which meant I could see at a glance that migrating `userRouter` before `authRouter` would break three downstream consumers. That visual would have taken me twenty minutes to map manually.

After you approve a plan — with or without revisions — you get three options:

1. **Execute in the cloud** — the plan runs remotely and can open a PR directly
2. **Implement here** — teleport the plan back to your current terminal session
3. **Start new session** — clear your current conversation and begin fresh with the plan as context

I use option 2 roughly 80% of the time. The plan arrives in my terminal as structured context, and I continue execution locally where I have full control over file operations, test runs, and Git workflow. Option 1 is tempting for straightforward tasks, but I've found that execution still benefits from local oversight — especially when the plan involves files that have changed since the cloud snapshot was taken.

That said, the cloud execution path is where Ultra Plan is heading. And for teams that want a planning-to-PR pipeline with minimal human intervention, it's already close.

## Ten Prompts, Two Modes, One Honest Comparison

Talking about features is easy. What matters is performance. So I ran the same ten prompts through both Ultra Plan and local plan mode, comparing the output across four dimensions: speed, plan quality, risk detection, and actionability.

Here's what I tested:

**Simple tasks:**
1. Swap Qwen 3.5 for Gemma 4 as the local inference model
2. Add a dark mode toggle to an existing React component
3. Rename a database column with migration

**Medium complexity:**
4. Refactor authentication middleware from session-based to JWT
5. Add rate limiting to all API endpoints with configurable thresholds
6. Migrate a Prisma schema from PostgreSQL to MySQL

**High complexity:**
7. Upgrade tRPC v10 to v11 across a monorepo
8. Implement a multi-tenant data isolation layer
9. Add end-to-end encryption for user messages
10. Audit a codebase for OWASP Top 10 vulnerabilities

The results split cleanly along complexity lines — but not the way I expected.

### Where Ultra Plan Matched Local Plan (And Nothing More)

For prompts 1 through 3 — the simple tasks — Ultra Plan provided zero meaningful advantage over local plan mode. The plans were structurally similar. The same files were identified. The same steps were proposed. The only difference was presentation: Ultra Plan's output looked prettier in the browser.

Swapping Qwen 3.5 for Gemma 4 generated nearly identical plans in both modes. Local plan: change the model config, update the inference endpoint, adjust the tokenizer settings, test. Ultra Plan: same steps, slightly more verbose descriptions, a note about checking compatibility — but nothing I wouldn't have caught myself.

For simple tasks, the extra round-trip to the cloud adds latency without adding insight. Local plan mode handled these in 15-30 seconds. Ultra Plan took 45-90 seconds for the same outcome, because the cloud session needs to spin up, sync your repo snapshot, and render the web UI.

My recommendation: if you can describe the change in one sentence and you already know which files need touching, stay in local plan mode. The speed advantage is real.

### Where Ultra Plan Pulled Ahead — And By How Much

Prompts 4 through 6 — medium complexity — showed the first meaningful gap. Ultra Plan started catching things that local plan mode missed.

The JWT migration (prompt 4) is a good example. Local plan mode gave me a reasonable plan: create the JWT utility, update the auth middleware, modify the login endpoint to issue tokens, add token refresh logic. Solid. Correct. But it missed the session cleanup problem — existing users had active sessions that would need to be invalidated during the migration window. It also didn't mention the need to update CORS configuration for the new Authorization header pattern.

Ultra Plan caught both. The plan included a dedicated migration step for active sessions, a rollback strategy if JWT verification failed for a threshold of users, and a CORS update section. The risk detection was noticeably deeper.

For the Prisma migration (prompt 6), local plan mode proposed a straightforward schema rewrite. Ultra Plan flagged four MySQL-specific gotchas: the `@db.Text` type difference, the lack of native array support requiring a join table, the case sensitivity difference in string comparisons, and an index length limitation that would break one of my composite indexes. Three of those four would have caused runtime errors that I'd have spent hours debugging.

The gap widened further at high complexity. The tRPC v10-to-v11 migration (prompt 7) was where Ultra Plan really earned its keep. Local plan produced a reasonable migration guide. Ultra Plan produced what I can only describe as an engineering document — complete with dependency ordering, type compatibility analysis, a list of deprecated APIs I was using with their v11 replacements, and a phased rollout strategy that let me migrate route-by-route instead of all-at-once.

The security audit (prompt 10) showed the most dramatic difference. Local plan identified surface-level issues: missing input validation, a few SQL injection vectors, hardcoded secrets. Ultra Plan found those plus three deeper issues: an insecure direct object reference in the user profile endpoint, a race condition in the password reset flow that could allow token reuse, and a missing rate limit on the login endpoint that made it vulnerable to credential stuffing. The local plan flagged 6 issues. Ultra Plan flagged 11.

But here's the thing — and this is where the story gets complicated.

## The Three Hidden Modes Nobody Tells You About

After running these tests, the inconsistency bothered me. Why did Ultra Plan generate Mermaid diagrams for some prompts but not others? Why did the security audit produce a multi-perspective analysis while the model swap produced basically the same output as local mode?

I dug into the Claude Code source — specifically the leaked system prompts that surfaced after the npm source map incident in March 2026 — and found something that explains everything.

Ultra Plan doesn't run one planning mode. It runs three. And Anthropic assigns them dynamically, invisibly, based on server-side configuration.

**Simple Plan** is the baseline. It's structurally similar to local plan mode — a straightforward analysis that identifies files, proposes steps, and outputs a clean plan. No diagrams. No multi-perspective analysis. When you get a Simple Plan, you're essentially getting local plan mode with a nicer UI and cloud execution. This is what I was getting for the simple tasks.

**Visual Plan** adds diagramming. Same analytical depth as Simple Plan, but with Mermaid and Asy diagram generation layered on top. The dependency flowchart I got for the tRPC migration? That was a Visual Plan. The diagrams aren't decoration — they encode structural relationships that plain text struggles to communicate. But the analysis itself isn't deeper than Simple Plan.

**Deep Plan** is the one that changes the game. And it's the one that explained my security audit results.

Deep Plan activates a multi-agent system. Not one model thinking through your problem — multiple specialized sub-agents, each focused on a different dimension:

- **One agent analyzes code and architecture** — examining the structural implications of the change
- **One agent locates all files that need modification** — not just the obvious ones, but downstream consumers and test files
- **One agent identifies risks, edge cases, and dependency conflicts** — the "what could go wrong" specialist
- **One agent reviews the complete plan** for logical consistency, missing mitigations, and execution order

These agents run in parallel, contribute their findings to a shared context, and the system synthesizes their outputs into a unified plan. The security audit that caught 11 issues instead of 6? That was Deep Plan. The risk agent specifically flagged the race condition in the password reset flow — something a single-pass analysis consistently misses because it requires reasoning about concurrent state.

Here's the part that frustrated me: you don't get to choose which mode you receive.

## You're Part of an AB Test (And Anthropic Knows It)

The mode assignment isn't random in the coin-flip sense. Anthropic controls it through server-side configurations — essentially a feature flag system that determines which planning variant each user receives for each session. The leaked source confirms this is a deliberate AB testing framework.

Anthropic is measuring several things through this system:

- **User acceptance rates** for each planning variant — do people approve and execute Simple Plans at the same rate as Deep Plans?
- **Planning prompt effectiveness** — which system prompts produce plans that users actually follow through on?
- **Model performance** — the infrastructure could be used to test upcoming model versions against each other in live planning scenarios

This explains why my experience was inconsistent across runs. Some of my Ultra Plan sessions were hitting Simple Plan (nearly identical to local mode), while others were hitting Deep Plan (dramatically better). I wasn't comparing "Ultra Plan vs Local Plan" in a controlled way. I was comparing "whichever variant Anthropic's AB system assigned me" against local plan.

Once I realized this, I went back and re-ran the high-complexity prompts multiple times. The tRPC migration produced noticeably different plans across three runs — one with diagrams, one without, one with the multi-agent risk analysis. Same prompt. Same repo state. Different planning variants.

For users who care about consistency — and if you're building production software, you should — this is the most important thing to understand about Ultra Plan right now. The quality ceiling is genuinely high. The quality floor is basically local plan mode with extra latency. And you have no control over which one you get.

That uncertainty is why, for now, I've landed on a specific workflow that gives me the best of both worlds. But before I share that, there's one more technical detail worth understanding.

## How Ultra Plan Reads Your Codebase (And Where It Falls Short)

When you trigger `/ultraplan`, Claude Code doesn't upload your entire repository to Anthropic's cloud. It creates a snapshot — a point-in-time copy synced to the CCR environment where the planning session runs. The planning agent then reads from this snapshot as if it were a local codebase.

This creates a subtle but important limitation: the cloud session doesn't see changes you make after launching Ultra Plan. If you trigger an Ultra Plan for a database migration, then modify the schema locally while the plan is being generated, the plan will be based on the old schema. I got bitten by this once — approved a plan that referenced a column I'd already renamed during the planning window.

The 30-minute compute window is generous for most tasks. My longest Ultra Plan session — the full OWASP audit — completed in about 8 minutes. But the window matters for extremely large repositories where the initial codebase analysis takes significant time. Anthropic hasn't published repo size limits for CCR, but in my testing, repos under 500MB of source code (excluding node_modules and build artifacts) planned without issues.

One more thing about the snapshot approach: Ultra Plan works best with GitHub-hosted repositories. The sync relies on your repo's remote, which means local-only repos or repos with large uncommitted changes may produce plans based on incomplete context. I learned to always commit and push before running `/ultraplan` for anything important.

If you'd rather have someone set up an optimized Claude Code workflow from scratch — complete with Ultra Plan integration, custom skills, and agent orchestration — I take on those kinds of engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## My Actual Workflow — How I Use Ultra Plan Today

After two weeks of testing, here's the workflow I've settled on. It's not "always use Ultra Plan" or "skip it entirely." It's conditional, and the conditions are specific.

**For tasks I can describe in one sentence and where I already know the affected files:** Local plan mode. No cloud round-trip needed. Shift+Tab, review, execute. Fast.

**For tasks involving more than 5 files, dependency chains, or breaking changes:** Ultra Plan. The multi-agent analysis (when you get Deep Plan) catches risks that single-pass planning consistently misses. The web UI makes iteration faster. Even when I get Simple Plan, the browser-based review is more comfortable for complex plans.

**For security audits and architecture reviews:** Ultra Plan, always. The Deep Plan variant is dramatically better at finding non-obvious vulnerabilities. And since these are high-stakes assessments where missing an edge case has real consequences, I'd rather take the latency hit for the chance at a deeper analysis.

**For time-sensitive changes where every second matters:** Local plan mode. Ultra Plan's cloud round-trip adds 30-90 seconds before you even see the plan. When production is down and I need a fix fast, that latency is unacceptable.

**For multitasking:** Ultra Plan is unbeatable. I regularly launch two or three Ultra Plan sessions for different tasks, then review and approve them as they complete. This parallel planning workflow is something local mode simply cannot do — each local plan blocks your terminal until it finishes.

### The Deep Plan Skill Workaround

Here's the most tactical thing in this entire post.

Since Anthropic doesn't let you choose which planning variant you get, and Deep Plan produces the best results by a significant margin, I extracted the Deep Plan system prompt structure and turned it into a custom Claude Code skill.

The approach: create a skill that mimics Deep Plan's multi-agent analysis pattern locally. The skill instructs Claude Code to analyze the prompt through four sequential lenses — architecture impact, file identification, risk assessment, and plan review — before synthesizing a unified plan. It's not identical to the real Deep Plan (which runs true parallel agents in CCR), but it approximates 80% of the quality improvement in my testing.

Here's the skeleton:

```yaml
---
name: deep-plan
description: >
  Multi-perspective planning for complex code changes.
  Trigger when user asks for a detailed plan, migration
  strategy, or architecture review.
---
```

```markdown
## Deep Plan — Multi-Agent Analysis Skill

### Phase 1: Architecture Analysis
Analyze the requested change from a structural perspective.
Identify affected modules, data flows, and integration points.
Map dependencies between components.

### Phase 2: File Discovery
Locate ALL files that need modification — not just the obvious
targets. Include test files, type definitions, configuration
files, and downstream consumers.

### Phase 3: Risk Assessment
For each proposed change, identify:
- Edge cases that could cause runtime failures
- Breaking changes for existing consumers
- Race conditions or state management issues
- Security implications
- Rollback complexity

### Phase 4: Plan Synthesis & Review
Combine findings from all phases into a unified implementation
plan. Order steps by dependency chain. Flag any phase where
findings conflict. Include rollback procedures for each step.

### Output Format
Use numbered steps with sub-items. Include file paths.
Generate a Mermaid dependency diagram if more than 5 files
are affected. End with a risk summary table.
```

This gives me Deep Plan-quality analysis on demand, without the AB testing lottery. The trade-off is speed — running this locally takes longer than a single-pass plan because it's four sequential analysis passes. But for complex tasks, the extra two minutes of planning saves hours of debugging.

I still use the actual `/ultraplan` command for multitasking scenarios and when I want the web UI review experience. But for my highest-stakes planning — the ones where I need guaranteed depth — the custom skill gives me control that Ultra Plan currently doesn't.

## What This Tells Us About Where Claude Code Is Heading

Step back from the tactical details for a moment and look at what Ultra Plan reveals about Anthropic's roadmap.

The CCR infrastructure isn't just for planning. It's a general-purpose cloud execution environment. Today it runs planning sessions. Tomorrow — and this is speculation based on the architecture, not insider knowledge — it could run full implementation sessions, test suites, or even continuous integration pipelines powered by Claude Code agents.

The AB testing framework is equally telling. Anthropic is using live Ultra Plan sessions to optimize planning prompts and measure which approaches produce plans that users actually execute. This is a live feedback loop for improving the model's planning capability. Every time you approve or reject an Ultra Plan, you're contributing data to that optimization process.

The multi-agent Deep Plan architecture is the most forward-looking piece. Today, it runs three to four specialized sub-agents for planning. The pattern generalizes to any complex task: implementation agents, testing agents, documentation agents, security review agents — each with specialized system prompts, running in parallel, synthesizing their outputs. Claude Code's agent teams feature already does some of this locally. Ultra Plan's infrastructure suggests Anthropic is building the cloud backbone to run it at scale.

I expect three things in the next six months:

1. **User-selectable planning modes** — the AB testing phase will end, and we'll get to choose Simple, Visual, or Deep Plan explicitly
2. **Cloud execution improvements** — executing plans in CCR with direct PR creation will become the default path for teams
3. **Persistent planning sessions** — the ability to bookmark, share, and return to Ultra Plan sessions across team members

Whether those predictions land or not, the direction is clear: Claude Code is becoming a planning-first tool where execution is the easy part.

## The Honest Trade-Offs You Should Know

Ultra Plan isn't a pure upgrade. Here are the limitations I've hit in real usage, and none of the promotional material mentions them.

**The snapshot lag is real.** Your cloud session plans against a frozen copy of your repo. If you're actively developing while the plan generates, you'll get recommendations based on stale code. Always commit and push before launching Ultra Plan for critical tasks.

**You can't control the planning variant.** This is my biggest frustration. Deep Plan is significantly better than Simple Plan, but the system decides which one you get. For a feature that's supposed to help with high-stakes planning, the randomness feels like a design flaw — even if the AB testing rationale makes sense from Anthropic's perspective.

**The web UI requires context switching.** Jumping from terminal to browser to review a plan breaks flow. I'm a keyboard-driven developer. Every time I reach for the mouse to click on a plan section in the browser, a small part of me dies. The inline commenting is great once you're there, but the transition from terminal to browser is jarring.

**Latency matters for iterative work.** When I'm in a tight build-test-debug cycle, local plan mode's 15-second turnaround beats Ultra Plan's 45-90 seconds every time. Ultra Plan is a deep-thinking tool, not a quick-feedback tool.

**The 30-minute window is mostly theoretical.** None of my Ultra Plan sessions exceeded 10 minutes. But if you're working with an exceptionally large codebase or asking for an analysis that requires reading thousands of files, the window could become a constraint. I haven't personally hit it.

**Requires Claude Code on the web.** Ultra Plan needs a connected account and a GitHub-hosted repository. If you're working with local-only repos, private GitLab instances, or in an air-gapped environment, Ultra Plan isn't an option. Local plan mode is your only path.

These aren't dealbreakers. They're the kind of friction points that matter when you're deciding whether to adopt a new tool for production work versus just playing with it on side projects. Know them going in.

## So Should You Actually Use Ultra Plan?

Two weeks ago, I would have given a simple answer. Now it's more honest.

If you're doing complex refactors, dependency upgrades, security audits, or architecture changes — and you can tolerate the AB testing lottery — Ultra Plan is worth adding to your workflow. The quality ceiling when you hit Deep Plan is meaningfully higher than anything local plan mode produces. The web UI makes plan review and iteration faster for non-trivial changes. And the parallel planning capability is genuinely unique.

If most of your work involves targeted changes to known files, local plan mode is still faster and more predictable. Don't add cloud latency for tasks that don't need cloud-scale analysis.

If you want guaranteed Deep Plan quality without the randomness, build the custom skill I described above. Run it locally. Accept the speed trade-off for the depth guarantee.

And if you're the kind of developer who tracks your tools' behavior closely — which, if you've read this far, you probably are — keep watching the AB testing patterns. Run the same prompt multiple times. Notice when you get diagrams versus when you don't. Notice when the risk analysis is surface-level versus surgical. Understanding which variant you're receiving helps you calibrate your trust in the output.

The thing nobody's saying about Ultra Plan is this: it's not a finished feature. It's a research preview that doubles as a live optimization platform for Anthropic's planning capabilities. You're simultaneously using a tool and training it. That's not a criticism — it's the reality of building at the frontier of AI-assisted development. But it means the Ultra Plan you use today won't be the Ultra Plan you use in three months. And that future version, informed by millions of real planning sessions, will almost certainly be better than what any of us can build with custom skills.

I'm keeping `/ultraplan` in my daily rotation. I'm keeping my Deep Plan skill as a backup. And I'm running both, comparing outputs, and filing every difference away as a data point.

Because the engineers who understand their tools deeply — not just what the tools do, but how they work underneath — are the ones who get the most out of them. That's been true since the first compiler, and it's still true now.

## Frequently Asked Questions

### How do I run Ultra Plan in Claude Code?
Type `/ultraplan` followed by your planning prompt in the Claude Code terminal. The session spins up in Anthropic's cloud, and you receive a browser URL to review the generated plan. You need a Claude Code on the web account and a GitHub-hosted repository.

### Is Ultra Plan faster than local plan mode?
For planning computation, Ultra Plan runs up to 2x faster because it uses dedicated cloud resources with Opus 4.6. But the total round-trip — including cloud spin-up and browser review — adds 30-90 seconds compared to local plan mode's 15-30 second turnaround for simple tasks.

### Can I choose between Simple Plan, Visual Plan, and Deep Plan?
Not currently. Anthropic assigns planning variants through server-side configuration as part of their AB testing framework. You can approximate Deep Plan quality by building a custom skill that replicates its multi-agent analysis pattern locally.

### Does Ultra Plan work with private repositories?
Ultra Plan requires a GitHub repository accessible through your Claude Code on the web account. Local-only repos, GitLab-hosted repos, and air-gapped environments are not supported. For those setups, local plan mode remains the only option.

### What happens to my code during Ultra Plan sessions?
Claude Code creates a read-only snapshot of your repository synced to the cloud. The planning agent analyzes this snapshot — it cannot modify your local files. Changes only happen after you approve the plan and choose an execution path (cloud or local).

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
