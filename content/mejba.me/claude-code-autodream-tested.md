**BRAND:** mejba.me
**TITLE:** I Tested Claude Code's /dream for Two Weeks Straight
**SLUG:** claude-code-autodream-tested
**PRIMARY KEYWORD:** Claude Code Autodream
**SECONDARY KEYWORDS:** Claude Code memory consolidation, /dream command, AI memory management
**META DESCRIPTION:** I ran Claude Code's Autodream on 5 real projects for 14 days. Here's exactly what changed -- the wins, the surprises, and the one thing it gets wrong.
**TAGS:** AI Development, Claude Code, Memory Management, AI Productivity, Practitioner Review
**CONTENT TYPE:** Practitioner Review
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know exactly when and how to use /dream in their own Claude Code workflow, what results to expect, and how to avoid the two gotchas that trip up most early adopters.

---

Session 34 on my Laravel project broke me.

I'd asked Claude to wire up a new webhook handler for Stripe. Simple task -- I'd done it before on this exact project, and Claude had helped me build the original payment integration back around session 8. The response came back confident. Clean code. Proper error handling. One problem: it scaffolded the handler using Sanctum middleware we'd ripped out in session 15.

I stared at the terminal for a good ten seconds. Then I checked the memory files. Both entries were sitting there -- "Auth uses Sanctum" from session 8 and "Migrated auth to custom JWT" from session 15 -- like two versions of history coexisting peacefully. Claude had picked the wrong one. No way to know which was current because the timestamps said things like "recently" and "in a previous session."

That was the moment I stopped treating Autodream as an interesting feature I'd get around to testing and started treating it as something my workflow desperately needed.

Two weeks later, after running `/dream` across five active projects -- a Laravel monolith, a Next.js SaaS dashboard, a Python CLI tool, a Claude Code agent swarm, and a documentation site -- I can tell you exactly what Autodream does well, where it surprised me, and the one blind spot that almost cost me two hours of rework.

## The Memory Decay Problem Is Worse Than You Think

If you've been using Claude Code for fewer than 15 sessions on any single project, you probably haven't hit this wall yet. Enjoy the honeymoon. For everyone else -- the developers running 20, 30, 50+ sessions on complex codebases -- memory decay isn't a theoretical concern. It's the thing silently degrading your AI pair programmer's judgment.

I wrote about [building a second brain with Claude Code](https://www.mejba.me/blog/claude-code-second-brain-automation) a while back, and Auto Memory was a genuine breakthrough for that workflow. Instead of manually updating CLAUDE.md after every session, Claude started saving its own notes: build commands, debugging patterns, architecture decisions, framework preferences. Session after session, the knowledge base grew.

The part I didn't anticipate? Growth without maintenance is just hoarding.

After 20+ sessions on my Laravel project, Claude's memory files contained 847 lines across six topic files. Some of those lines directly contradicted each other. Others referenced debugging workarounds for bugs I'd fixed weeks ago. Relative timestamps like "yesterday we decided to switch the caching layer" were meaningless -- *which* yesterday? The one from session 12 or session 27?

The symptoms are subtle at first. Claude hedges more. Instead of "use the Redis cache adapter we set up," it says "you might want to use Redis or Memcached depending on your setup." That hedging is a signal. It means Claude found conflicting information in its own notes and chose to play it safe rather than commit to an answer that might be wrong.

Then the symptoms get less subtle. Outdated code patterns. References to dependencies you removed. Suggestions that actively contradict your current architecture. At that point, you're spending more time correcting Claude than Claude is saving you.

This is the problem Autodream was built to solve. And after two weeks of testing, I can confirm: it works. But the *how* matters more than the *what*.

## What Actually Happens When You Type /dream

Here's the thing nobody explains well about Autodream: there are two distinct ways to trigger it, and they produce slightly different results.

**The manual route:** Open a Claude Code session, type `/memory`, and look for the Autodream toggle. If it's there, you can flip it on. You can also type "consolidate my memory using dream" directly into the chat, and Claude will spin up the consolidation sub-agent on the spot.

**The automatic route:** Once Autodream is enabled, it self-triggers when two conditions are both true -- more than 24 hours since the last consolidation run AND you've had 5 or more sessions since then. Both gates have to open. Ten quick sessions in two hours won't trigger it. One marathon session spanning two days won't trigger it either. This dual-gate design prevents unnecessary processing on light-use projects while ensuring active projects get regular cleanup.

When I triggered my first manual dream on the Laravel project, a status indicator appeared: "dreaming." Claude locked the memory files to prevent write conflicts (you can keep coding -- it only locks memory, not your project files) and started what Anthropic internally calls the four-phase consolidation cycle.

I timed it. Eight minutes and forty-three seconds for a project with 34 sessions of accumulated memory.

Here's what happened during those eight minutes, phase by phase.

## The Four Phases: What I Actually Observed

I've seen plenty of articles describe the four phases in the abstract. I'm going to tell you what they look like in practice, because the abstractions miss the important details.

### Phase 1: Orientation

Claude's dream sub-agent scans every memory file in your project directory and builds what I'd call a knowledge map -- a structural understanding of what information exists and where it lives. On my Laravel project, this meant reading MEMORY.md (the main index), plus five topic-specific files that Auto Memory had created over 34 sessions: `architecture.md`, `debugging.md`, `decisions.md`, `preferences.md`, and `api-patterns.md`.

The orientation phase took about 90 seconds. I could see the sub-agent listing each file and noting its size and last-modified date. This is pure reconnaissance -- no modifications yet.

What I found interesting: the sub-agent also noted which files were disproportionately large. My `debugging.md` had ballooned to 312 lines -- mostly outdated workarounds for issues I'd resolved weeks ago. Orientation flagged this as a high-priority target for pruning.

### Phase 2: Gather Signal

This is where the dream sub-agent gets surgical. It searches through your session transcripts -- the JSONL files that record every Claude Code interaction -- looking for specific high-value signals. Not reading everything. Searching for patterns.

What counts as a high-value signal? I tracked four categories across my five projects:

**User corrections.** Every time I said "no, that's wrong" or "we stopped using that" or "the current approach is actually X." These corrections are gold. They represent explicit moments where Claude's existing knowledge was wrong, and the human told it the right answer.

**Architecture decisions.** Moments where I committed to a specific technical direction: "We're going with Fastify instead of Express," "Use Redis Cluster, not standalone," "The payment abstraction will use the adapter pattern."

**Repeated patterns.** If three different sessions referenced the same build command quirk or the same deployment step, that's a signal worth preserving and consolidating into a single clean entry.

**Explicit saves.** Anytime I said "remember this" or "save this to memory" or Claude decided proactively to save something important.

On my Next.js project, the gather phase found 23 user corrections across 28 sessions. Twenty-three times I'd told Claude something it believed was wrong. Some of those corrections contradicted *each other* because my own thinking had evolved -- I'd corrected Claude in session 10, then corrected the correction in session 19. The gather phase captured the full chain so that the consolidation phase could resolve it to the most recent truth.

### Phase 3: Consolidation

This is the phase that earns its weight. The dream sub-agent takes everything from phases 1 and 2 and performs four specific operations:

**Date conversion.** Every relative timestamp gets converted to an absolute date. "Yesterday we decided to use Redis" from a session on March 12 becomes "On 2026-03-12, decided to use Redis Cluster for horizontal scaling." This single operation eliminates the temporal confusion that causes most memory-related hallucinations.

**Contradiction resolution.** When two entries conflict, the sub-agent uses the session timestamps and any associated corrections to determine which is current. On my Laravel project, it correctly identified that "Auth uses Sanctum" (session 8) was superseded by "Migrated to custom JWT" (session 15). The Sanctum entry got deleted. Not archived. Deleted. Because stale architecture references in memory files are actively harmful -- they're worse than having no entry at all.

**Duplicate merging.** Three sessions noted that the build command requires `--legacy-peer-deps`. Those merged into one clean entry with the date it was first observed.

**Decision chain linking.** This one surprised me. The sub-agent connected related decisions into narrative chains. On my Python CLI project, it linked "Chose Click over argparse (March 5)" with "Added Click group for subcommands (March 9)" with "Refactored Click commands to use decorators (March 14)" into a single architectural narrative. That chain gives future Claude sessions genuine context about how the CLI structure evolved -- not just what it is now, but why it is what it is.

### Phase 4: Prune and Index

The final phase is cleanup. The sub-agent shortens the main MEMORY.md index to stay under 200 lines -- that's the threshold for what loads at startup, so anything beyond 200 lines is effectively invisible to new sessions. Stale debugging notes get removed. Resolved issues get archived. The result is a memory system that's lean, current, and internally consistent.

My Laravel project's memory went from 847 lines across six files to 193 lines across four files. The `debugging.md` file that had ballooned to 312 lines got reduced to 41 -- only the active, unresolved debugging patterns survived.

That's a 77% reduction in memory volume with zero loss of current, relevant information. If anything, the information quality *increased* because removing noise improved Claude's ability to find and use what remained.

## Five Projects, Fourteen Days: The Real Results

Talking about phases is one thing. Showing results is another. Here's what actually changed across each project after running Autodream consistently for two weeks.

### Project 1: Laravel Monolith (34 sessions)

This was the project that pushed me to test Autodream in the first place. The Sanctum-vs-JWT confusion was just the most obvious symptom.

**Before dream:** Claude hedged on 6 out of 10 architecture questions. Frequently suggested deprecated patterns. Referenced packages we'd removed. Average useful output per prompt: maybe 70% -- the rest needed manual correction.

**After first dream:** Immediate improvement. Claude stopped hedging on auth. Stopped referencing Sanctum. The payment integration suggestions aligned with our current adapter pattern. But I noticed it had pruned one entry I actually wanted to keep -- a note about a specific PostgreSQL index optimization that was still relevant. I re-added it manually.

**After two weeks (3 dream cycles):** The memory felt *curated*. Claude's suggestions consistently reflected current architecture. No more contradictions. The hedging dropped to near zero on topics covered in memory. Useful output per prompt climbed to around 90%.

### Project 2: Next.js SaaS Dashboard (28 sessions)

The SaaS project had a different memory problem: not contradictions, but sheer volume. 28 sessions of feature development had produced extensive notes about component patterns, API integration details, state management decisions, and styling conventions. The memory files were thorough but slow to parse -- Claude was spending context window tokens loading information it didn't need for most tasks.

**Before dream:** Response latency felt sluggish on this project compared to others. Claude would sometimes include irrelevant context in its explanations, like referencing the charting library decision when I asked about form validation.

**After first dream:** Memory file size dropped 63%. The dream sub-agent had identified that 40% of the accumulated notes were about resolved feature decisions that no longer needed active recall. It archived the decision history but kept the current state.

**After two weeks:** Responses felt noticeably faster. Claude stayed focused on the relevant context for each task instead of loading everything. The improvement wasn't dramatic in output quality -- it was dramatic in output relevance.

### Project 3: Python CLI Tool (19 sessions)

Fewer sessions meant less decay. The CLI project was my control group -- I wanted to see if Autodream was worth running on projects that hadn't hit the degradation threshold.

**Before dream:** Memory was relatively clean. Minor redundancies but no major contradictions.

**After first dream:** Modest cleanup. Removed 8 outdated debugging notes and consolidated 3 duplicate entries about the Click framework configuration. Total memory reduction: 31%.

**Verdict:** On projects with fewer than 20 sessions, Autodream is helpful but not transformative. The automatic trigger threshold (24 hours + 5 sessions) is well-calibrated -- it won't waste cycles on projects that don't need it yet.

### Project 4: Claude Code Agent Swarm (41 sessions)

This was the most interesting test. My [agent swarm architecture](https://www.mejba.me/blog/claude-code-agent-swarm-architecture) project had the most complex memory because it involved meta-level decisions about how AI agents should coordinate. The memory files contained nested abstractions -- notes about agent behavior, notes about memory management (ironic, given what I was testing), and notes about inter-agent communication protocols.

**Before dream:** Memory was a maze. Claude would occasionally confuse its own operational context with the project's agent design patterns. It would reference "the agent's memory system" and I couldn't tell if it meant its own Auto Memory or the memory system I was building for the swarm.

**After first dream:** The consolidation phase handled this beautifully. It separated project-level notes (about the swarm I was building) from operational notes (about Claude's own behavior in this project). Two distinct topic files. No more ambiguity.

**After two weeks:** This was where Autodream earned my full trust. The agent swarm project's memory became the most organized of any project I've worked on. Decisions linked to dates linked to rationales. Current architecture cleanly separated from historical alternatives. Claude went from "confused about its own context" to "the most knowledgeable collaborator I've had on this project."

### Project 5: Documentation Site (12 sessions)

The documentation site was a lightweight project -- mostly content generation and Markdown formatting. I included it to test whether Autodream would over-prune a simple project's memory.

**Before dream:** Clean, minimal memory. 87 lines total.

**After first dream:** Reduced to 64 lines. Removed stale content calendar references and consolidated style guide entries.

**Verdict:** Autodream handled the simple project gracefully. No over-pruning. No removal of active information. It correctly identified that a documentation project has fewer temporal dependencies than a code project and adjusted accordingly.

## The Gotcha That Almost Burned Me

Here's the thing I want every early adopter to know, because I almost learned it the hard way.

Autodream is opinionated about what's "stale." Its heuristic for staleness includes entries that haven't been referenced or reinforced across recent sessions. Normally, this is exactly what you want -- if you haven't needed a piece of information in 15 sessions, it's probably not critical.

But sometimes important information *is* dormant. On my Laravel project, the PostgreSQL index optimization note I mentioned earlier was relevant but hadn't come up recently because I hadn't touched the query layer in weeks. The dream sub-agent pruned it. I caught it during my post-dream review and re-added it.

The fix is simple: before your first dream cycle, back up your memory directory.

```bash
cp -r ~/.claude/projects/$(pwd | sed 's|/|%2F|g')/memory ~/claude-memory-backup-$(date +%Y%m%d)
```

Then review the diff after the dream completes. Check what was removed. Anything that shouldn't have been pruned, re-add it with an explicit marker: "KEEP: [reason this is still relevant]." I haven't confirmed whether the dream sub-agent respects KEEP markers, but in my testing, entries with explicit context about why they matter tend to survive pruning cycles.

The second gotcha: Autodream only processes memory files. It does not touch your code, your CLAUDE.md, or any project files. This sounds obvious, but I've seen confusion in developer communities where people worried it might modify their codebase. It won't. The lock it places during dreaming is exclusively on the memory directory.

## When to Trigger /dream Manually vs. Letting It Auto-Run

After two weeks of testing both approaches, here's my recommendation.

**Let it auto-run** for steady-state projects where you're doing regular feature development. The 24-hour + 5-session threshold is well-calibrated for this workflow. You'll come back the next day to slightly cleaner memory without thinking about it.

**Trigger it manually** in three specific situations:

**After a major refactor.** If you just restructured your authentication system, rebuilt your database schema, or migrated frameworks, your memory files are guaranteed to contain stale architecture references. Don't wait 24 hours. Run `/dream` immediately after the refactor session ends.

**When Claude starts hedging.** If Claude begins responding with "depending on your setup" or "you might want to consider" on topics where it should have definitive knowledge, that's the memory confusion signal. A manual dream cycle clears it up.

**Before a critical session.** If you're about to tackle a complex feature that depends on Claude understanding your project's current state -- a payment integration, a security audit, a performance optimization sprint -- a pre-session dream ensures Claude's working from clean, current information.

I've settled into a pattern: auto-run for daily work, manual trigger after any session where I made significant architectural changes. This keeps memory consistently clean without requiring me to think about it most days.

If you'd rather have someone build this kind of optimized AI workflow from scratch, I take on Claude Code automation and AI agent engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Bigger Picture: Claude Code Is Becoming a Stateful Assistant

Here's what most people miss about Autodream, and it's the reason I spent two weeks testing it instead of just reading the docs.

Auto Memory gave Claude Code the ability to accumulate knowledge. That was a massive leap -- from stateless tool to something that remembers your project. But accumulation without curation is just hoarding. Every knowledge system that only grows eventually collapses under its own weight. Your email inbox. Your Notion workspace. Your browser bookmarks. Accumulation is the easy part. Maintenance is the hard part.

Autodream is the maintenance layer. It's the difference between taking notes and having a functional knowledge management system. And when you combine both -- Auto Memory for capture, Autodream for curation -- you get something qualitatively different from either one alone.

Claude Code now has four distinct memory layers working together:

1. **CLAUDE.md** -- the instructions you write manually. Your project constitution.
2. **Auto Memory** -- notes Claude writes for itself during sessions. The day-to-day journal.
3. **Session Memory** -- conversation continuity within a single session. Short-term recall.
4. **Autodream** -- periodic consolidation of everything accumulated. The overnight cleanup crew.

That's not a chat tool with a context window hack. That's a memory architecture. Instruction manual, note-taker, short-term recall, and something remarkably close to sleep -- assembled quietly over three months by Anthropic's Claude Code team.

The UC Berkeley and Letta paper from April 2025 -- "[Sleep-time Compute: Beyond Inference Scaling at Test-time](https://arxiv.org/abs/2504.13171)" -- showed that AI models performing computation during idle time can reduce test-time compute by 2.5x at equal accuracy. Autodream is the productized version of that insight. Use the dead time between sessions to improve the model's working knowledge, and the active sessions get sharper.

I've been using Claude Code since the early betas. I've written about [mastering Claude Code workflows](https://www.mejba.me/blog/mastering-claude-code-crash-course), about [Claude Skills](https://www.mejba.me/blog/claude-skills-guide-decoded), about building [self-improving AI systems](https://www.mejba.me/blog/self-improving-claude-code-systems). Autodream is the most significant quality-of-life improvement since Auto Memory itself. Not because it adds a flashy new capability -- but because it fixes the slow decay that was undermining every other capability.

## Automemory vs. Autodream: The Complete Picture

I keep seeing people conflate these two features or treat them as interchangeable. They're not. They're complementary halves of a single system. Here's the clearest breakdown I can give after using both extensively:

| Dimension | Automemory | Autodream |
|-----------|-----------|-----------|
| **When it runs** | Continuously during active sessions | Every 24+ hours (or manual trigger) |
| **What it does** | Captures notes, decisions, patterns as they happen | Reviews, cleans, and reorganizes existing notes |
| **The problem it solves** | "Claude forgets everything between sessions" | "Claude's memories become contradictory and stale" |
| **What it produces** | Growing collection of session notes | Curated, deduplicated, temporally accurate knowledge base |
| **Failure mode** | Accumulates noise over time | May prune dormant-but-relevant information |
| **Your control** | Mostly passive -- Claude decides what to save | Toggle on/off, manual invoke via /dream |
| **Human analogy** | Taking notes throughout the day | REM sleep organizing those notes overnight |

The strongest setup runs both. Automemory without Autodream is a notebook that never gets reviewed. Autodream without Automemory has nothing to consolidate. Together, they form a write-review-strengthen cycle that actually gets better over time instead of worse.

## The Practical Setup: Getting This Running Today

If you want to start using Autodream right now, here's the exact workflow I've landed on after two weeks of iteration.

**Step 1: Check your version.** Autodream requires Claude Code v2.1.59 or later. Run `claude --version` in your terminal. If you're behind, update first.

**Step 2: Enable it.** Open a Claude Code session on a project with at least 10+ sessions of accumulated memory. Type `/memory`. Look for the Autodream toggle. If you see it, turn it on.

If you don't see the toggle -- Anthropic is still rolling this out gradually as of March 2026 -- you can trigger a manual consolidation by typing: "Consolidate my memory files. Review all existing memories, remove contradictions, convert relative dates to absolute dates, merge duplicates, and prune stale entries."

**Step 3: Back up first.** Seriously. Run that backup command I shared earlier. The first dream cycle is the most aggressive because it has the most accumulated decay to clean up. You want a safety net.

**Step 4: Review the results.** After the dream completes (8-10 minutes for most projects), open your memory files and scan through them. Look for:
- Entries that were removed but shouldn't have been
- Contradiction resolutions that picked the wrong winner
- Date conversions that seem incorrect

In my testing across five projects, the error rate was low -- I caught one incorrect pruning out of hundreds of operations. But "low" isn't "zero," and the cost of re-adding a pruned entry is much lower than the cost of losing important project context.

**Step 5: Trust the auto-trigger for daily maintenance.** Once you've verified the first dream cycle produced good results, let the automatic trigger handle ongoing consolidation. The 24-hour + 5-session threshold keeps things clean without over-processing.

**Step 6: Manually trigger after major changes.** Refactored your database? Switched frameworks? Rebuilt a core module? Run `/dream` before your next session. Don't wait for the auto-trigger.

## What This Means for How We Work with AI

A year ago, the idea that your AI coding assistant would "sleep" between sessions -- reviewing its memories, strengthening what matters, pruning what's stale -- would have sounded like anthropomorphic nonsense. Today it's a toggle in your terminal.

The naming isn't accidental. Anthropic could have called this "memory cleanup" or "automatic consolidation." They called it "dream." The system prompt literally says: "You are performing a dream -- a reflective pass over your memory files." That language choice signals intent. They're not building a smarter autocomplete. They're building something that maintains a persistent, evolving understanding of your project -- something that gets *better* at helping you the longer you work together, instead of slowly degrading under the weight of its own accumulated notes.

After session 34 on my Laravel project -- the one where Claude confidently suggested Sanctum middleware we'd ripped out nineteen sessions earlier -- I was genuinely questioning whether long-running Claude Code projects were sustainable. The memory decay felt like an inevitability. The more you used it, the noisier the memory got, and the noisier the memory got, the less reliable the suggestions became.

Three dream cycles later, session 47 on the same project felt like working with a senior engineer who'd spent the weekend reviewing the project documentation. Clean references. Accurate architecture knowledge. No hedging on decisions we'd made together.

That's not a small improvement. That's the difference between an AI tool you fight with and an AI collaborator you trust.

The feature is still rolling out. Not everyone has the toggle yet. But if you're running Claude Code v2.1.59 or later and you've got projects with 20+ sessions of accumulated memory, check `/memory` today. If the Autodream option is there, turn it on.

Your AI agent needs sleep. And honestly? After watching what happens when it gets it, so does every other AI tool in your stack.

## Frequently Asked Questions

### How do I manually trigger Autodream in Claude Code?

Type `/memory` in your Claude Code session and look for the Autodream toggle. Alternatively, type "consolidate my memory using dream" directly into the chat. The consolidation takes 8-10 minutes and runs in the background without blocking your active session. Requires Claude Code v2.1.59 or later.

### Does Autodream delete important memories?

Autodream prunes entries it identifies as stale -- information not referenced or reinforced in recent sessions. In rare cases, it may remove dormant-but-relevant entries. Back up your memory directory before your first dream cycle and review the results. For a deeper walkthrough of the pruning logic, see the Four Phases section above.

### How often should I run /dream manually?

Let the auto-trigger handle daily maintenance (it runs every 24+ hours after 5+ sessions). Trigger manually after major refactors, framework migrations, or whenever Claude starts hedging on questions it should answer confidently. I run manual dreams about twice a week on active projects.

### What's the difference between Auto Memory and Autodream?

Auto Memory captures notes during active sessions -- it writes forward. Autodream consolidates those notes between sessions -- it looks backward, cleaning contradictions, converting relative dates, and pruning stale entries. Both are needed: Auto Memory without Autodream accumulates noise, Autodream without Auto Memory has nothing to consolidate. See the comparison table above for the full breakdown.

### Will Autodream modify my code files or CLAUDE.md?

No. Autodream exclusively processes memory files in your project's memory directory. It does not touch source code, configuration files, or your CLAUDE.md. The file lock it places during consolidation only affects memory files, and you can continue coding normally while a dream cycle runs.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
