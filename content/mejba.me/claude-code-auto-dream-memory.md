**BRAND:** mejba.me
**TITLE:** Claude Code Auto Dream: Your AI Agent Sleeps Now
**SLUG:** claude-code-auto-dream-memory
**PRIMARY KEYWORD:** Claude Code Auto Dream
**SECONDARY KEYWORDS:** Claude Code memory consolidation, auto memory management, AI agent memory
**META DESCRIPTION:** Claude Code's hidden Auto Dream feature consolidates messy AI memories like REM sleep. I tested it. Here's how the 4-phase system actually works.
**TAGS:** AI Development, Claude Code, Memory Management, AI Agents, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand how Auto Dream's 4-phase memory consolidation works, why it fixes memory bloat, and how to check if it's active in their own Claude Code setup.

---

I found the feature by accident at 11 PM on a Wednesday.

I'd been deep in a Claude Code session -- my 30th or so on the same project -- and something felt different. The agent was referencing architectural decisions I'd made weeks earlier with a precision that didn't match my recent experience. For the past few sessions, Claude had been tripping over its own notes. Contradicting itself. Citing a database schema I'd refactored out two sprints ago. The memory files that were supposed to help had become a liability.

But this session? Clean. Coherent. Like someone had gone through Claude's notebook overnight and organized it.

Someone had. Or rather, some*thing* had. A background sub-agent I'd never heard of had quietly reviewed every memory file in my project, pruned the stale entries, resolved the contradictions, and reorganized everything into clean topic files. The system prompt for this sub-agent starts with a line that stopped me cold: "You are performing a dream -- a reflective pass over your memory files."

Claude Code Auto Dream. An undocumented feature that gives your AI agent something remarkably close to sleep.

## The Memory Problem Nobody Warned Me About

If you've been using Claude Code for any serious project work, you already know about Auto Memory -- the system that lets Claude take its own notes between sessions. I wrote about building a [second brain with Claude Code](/content/mejba.me/claude-code-second-brain-automation.md) a while back, and Auto Memory was a massive upgrade to that workflow. Instead of manually updating context files after every session, Claude started saving notes for itself: build commands, debugging insights, architecture decisions, code style preferences.

Brilliant in theory. Messy in practice.

Here's what actually happens after about 20 sessions on a real project. The memory files start accumulating noise. Not garbage exactly -- each individual note made sense when Claude wrote it. But over time, the collection becomes a contradiction factory. Session 8 says "API uses Express." Session 14 says "Migrated API to Fastify." Both entries sit there, side by side, confusing future Claude into hedging its responses or picking the wrong one. Relative timestamps like "yesterday we refactored the auth module" become meaningless three weeks later. Debugging notes for bugs you fixed months ago still take up space, eating into the context window and displacing information that actually matters.

I hit this wall around session 22 on a Laravel project. Claude started giving me suggestions that contradicted decisions we'd made together. It recommended Sanctum for auth -- a migration we'd explicitly moved away from in session 15. The memory file had both entries, and Claude couldn't tell which one was current.

I tried the obvious fix: system prompts that instructed Claude to keep memories tidy. "Always update rather than append." "Remove outdated entries." "Resolve contradictions." It helped marginally. The core problem is structural. Auto Memory writes notes forward -- it adds information session by session. Nothing goes back to review what's already there. Nothing checks whether Tuesday's notes still make sense on Friday.

That's not how human memory works either, if you think about it. Your brain doesn't just accumulate everything from the day and stack it on top of yesterday's pile. It processes. It consolidates. It does this during sleep -- specifically during REM cycles, when short-term memories get reorganized into long-term storage. Important connections strengthen. Irrelevant details fade. Contradictions resolve.

Anthropic apparently had the same thought. And they built the solution.

## What Auto Dream Actually Is (And Why the Name Matters)

Auto Dream is a background sub-agent that runs between your Claude Code sessions. Its job is to do for Claude's memory files what REM sleep does for your brain: review everything that's been accumulated, keep what matters, discard what doesn't, and organize the rest so future sessions start from a clean foundation.

The naming here is deliberate. This isn't marketing fluff. The [leaked system prompt](https://github.com/Piebald-AI/claude-code-system-prompts/blob/main/system-prompts/agent-prompt-dream-memory-consolidation.md) explicitly instructs the agent: "You are performing a dream -- a reflective pass over your memory files. Synthesize what you've learned recently into durable, well-organized memories so that future sessions can orient quickly."

And the theoretical backing is real. A [UC Berkeley and Letta paper from April 2025](https://arxiv.org/abs/2504.13171) -- "Sleep-time Compute: Beyond Inference Scaling at Test-time" -- demonstrated that models which pre-compute during idle time can reduce test-time compute by 2.5x at equal accuracy, with measurable accuracy gains on reasoning tasks. The researchers found that predictability of user queries correlates directly with how effective sleep-time processing is. In other words: the more your AI can anticipate what you'll need based on past sessions, the better it performs when you actually show up to work.

Claude Code's Auto Dream takes that research and applies it to memory management. Instead of burning context tokens on contradictory, stale notes during your active session, it processes those notes while you're away. You come back to a cleaner, more organized knowledge base. The agent orients faster. The responses are sharper.

The difference between Auto Memory and Auto Dream maps almost perfectly onto how neuroscientists describe the difference between encoding and consolidation. Auto Memory encodes -- it captures information as it happens. Auto Dream consolidates -- it processes, organizes, and strengthens those memories during downtime. You need both. One without the other eventually breaks down.

But here's what makes this genuinely interesting, and why I think most coverage is underselling it: Auto Dream isn't just a cleanup script. It's a cognitive architecture decision. Anthropic is building AI agents that mirror biological memory systems -- not as a metaphor, but as an engineering pattern. The four phases of Auto Dream map onto documented stages of memory consolidation in neuroscience. That's either a remarkable coincidence or a sign of where agentic AI is headed.

Let me walk you through those four phases, because the implementation details reveal a lot about how Anthropic thinks about long-running agent systems.

## Inside the Four Phases: How Auto Dream Actually Works

Auto Dream operates as a dedicated sub-agent that runs in a restricted environment. It has read access to your project code but can only write to memory files. This is a smart safety boundary -- the dreaming agent can't accidentally modify your source code while reorganizing its notes. It also uses a lock file mechanism to prevent multiple Auto Dream processes from running simultaneously on the same project.

The whole process follows four distinct phases.

### Phase 1: Orientation

The dream agent starts by reading the current state of its memory. It lists the memory directory at `~/.claude/projects/<project>/memory/`, reads the main `MEMORY.md` index file, and skims each existing topic file to understand what's already stored and how it's organized.

Think of this as waking up and reviewing your notebook before starting to reorganize it. The agent needs to understand what it's working with before it can improve anything. This phase prevents a common failure mode in automated systems -- blindly processing without understanding the starting state, which leads to duplicated entries or lost information.

What's notable here is that the agent reads the *index* first. `MEMORY.md` functions as a table of contents for all the topic-specific memory files. The agent uses this to map the current knowledge structure before touching any individual file. It's looking for gaps, overlaps, and organizational problems at the structural level before diving into content-level issues.

### Phase 2: Gathering Signal

This is where the real work begins. The dream agent scans all previous session transcripts -- stored locally as JSONL files -- looking for patterns worth preserving.

It's searching for specific signals: user corrections (moments where you told Claude it was wrong or redirected its approach), recurring themes (topics or tools that keep coming up across sessions), important architectural decisions, and shifts in project direction. Daily logs, if present, get reviewed too.

The key insight in this phase is what it *doesn't* do. It doesn't treat every session equally. A session where you and Claude debated three different authentication approaches before settling on one is weighted differently from a session where you asked Claude to add a console.log statement. The agent is pattern-matching for high-signal moments -- the decisions, corrections, and preferences that shape future sessions.

This is also where the agent checks for drift. If a memory file says "project uses PostgreSQL" but recent sessions show interactions with MySQL, the agent flags that contradiction for resolution in the next phase.

### Phase 3: Consolidation

Consolidation is the core transformation step. The agent takes everything it gathered in Phase 2 and merges it into the existing memory structure. Specifically, it:

**Converts relative dates to absolute dates.** "Yesterday we switched from REST to GraphQL" becomes "On 2026-03-12, the project migrated from REST to GraphQL." This seems small but it's huge. Three weeks from now, "yesterday" is meaningless. An absolute date remains accurate forever. This single operation eliminates one of the biggest sources of memory confusion in long-running projects.

**Deletes contradicted facts.** If session 8 says "API uses Express" and session 14 says "Migrated to Fastify," the Express entry gets removed. The agent doesn't hedge or keep both -- it identifies the temporal sequence and preserves the most recent ground truth.

**Merges overlapping entries.** If three different sessions noted the same quirk about the build command, those consolidate into one clean entry. This directly reduces context window consumption. Instead of three slightly different descriptions of the same issue eating 300 tokens, you get one precise description using 80.

**Organizes by topic.** Rather than a chronological dump, memories get organized into topic files that mirror how you'd actually want to reference them. Architecture decisions in one file. Tooling preferences in another. Active debugging contexts in a third.

The consolidation phase is where the REM sleep analogy holds up best. During human REM sleep, the hippocampus replays recent experiences while the neocortex integrates them into existing knowledge structures. That's almost exactly what's happening here -- recent session data (hippocampus replay) gets integrated into the organized topic files (neocortex storage).

### Phase 4: Pruning and Indexing

The final phase enforces a hard constraint: keep `MEMORY.md` under 200 lines.

This isn't arbitrary. The memory index gets injected into every Claude Code session's context. A 500-line index file consumes context tokens that should be available for actual work. The 200-line cap forces the agent to prioritize ruthlessly -- stale entries get deleted, verbose descriptions get compressed, and the index gets restructured to reference topic sub-files rather than inlining everything.

After pruning, the agent rebuilds the index. Each topic file gets a one-line reference in `MEMORY.md` with a brief description. Future sessions can then load only the topic files relevant to the current conversation rather than ingesting the entire memory corpus.

In one documented case, Auto Dream consolidated 913 sessions of accumulated memory into a clean, organized structure in under 9 minutes. That's 913 sessions worth of notes, corrections, decisions, and preferences -- distilled into a coherent knowledge base while the developer was presumably asleep or getting coffee.

## How to Check If Auto Dream Is Active (And What to Do If It's Not)

Here's where things get a bit murky. As of March 2026, Auto Dream is in a quiet rollout. It's not in Anthropic's official documentation. The infrastructure is built and present in the Claude Code binary (v2.1.59+), but not every user has access yet.

To check your status, run the `/memory` command inside any Claude Code session. Look for a line that says "Auto-dream: on" or "Auto-dream: off." If you see the toggle, the feature is available in your installation -- even if it's currently set to off.

If you don't see the Auto-dream option at all, you're on a version that hasn't received the rollout yet. Update to the latest Claude Code version and check again.

For those who see "Auto-dream: off" -- the toggle appears but [reportedly can't be switched on](https://dev.to/akari_iku/does-claude-code-need-sleep-inside-the-unreleased-auto-dream-feature-2n7m) through the UI for all users yet. Anthropic appears to be gating the full activation.

There's a workaround that several developers have confirmed works: you can manually trigger a dream cycle by telling Claude directly. Type "dream," "auto dream," or "consolidate my memory files" in your session. Claude will execute the consolidation process inline. It takes several minutes -- 8 to 9 minutes in my testing on a project with about 40 sessions -- but it doesn't block your normal workflow. You can keep working in Claude Code while the dream runs in a separate process.

<!-- IMAGE: Terminal screenshot showing the /memory command output with "Auto-dream: off" toggle visible. Alt text: "Claude Code Auto Dream toggle in /memory command showing off state". Caption: "The Auto-dream toggle appears in the /memory menu, though activation is still rolling out." -->

If you want to replicate the full Auto Dream experience without waiting for the official rollout, there's an [open-source dream skill on GitHub](https://github.com/grandamenium/dream-skill) that replicates the four-phase consolidation process with a 24-hour auto-trigger. I haven't tested it extensively, but the architecture mirrors what Anthropic built internally.

### When Does Auto Dream Actually Run?

When fully active, Auto Dream fires automatically only when two conditions are met:

1. More than 24 hours have passed since the last consolidation
2. More than 5 new sessions have occurred since the last consolidation

Both conditions must be true. This prevents unnecessary processing on days when you only open Claude Code for a quick question, and it ensures there's enough new material to justify a consolidation pass. Efficient resource usage -- the system doesn't dream unless there's something worth dreaming about.

The conditional trigger also means Auto Dream won't fire on projects you haven't touched in weeks. It's looking for active, growing memory -- not dormant archives.

## What This Means for Your Claude Code Workflow

I've been running with consolidated memories for about two weeks now -- partially through manual triggers, partially through what appears to be the auto system kicking in on its own. The difference is stark enough that I want to be specific about what changed.

**Session start time dropped noticeably.** Claude Code used to spend the first few exchanges of every session re-orienting itself. I'd ask about the payment module and it would reference an outdated schema, I'd correct it, and then we'd get to actual work. After consolidation, it orients on the first try. The clean, organized memory files mean the relevant context loads correctly the first time.

**Contradictions disappeared.** The Sanctum-vs-custom-JWT confusion I mentioned earlier? Gone. The memory file now has a single, dated entry: "Auth migrated from Sanctum to custom JWT implementation (2026-02-03)." No ambiguity. No hedging.

**Context window efficiency improved.** This one is harder to quantify but I felt it. With a cleaner, smaller memory footprint, Claude had more room for actual problem-solving. The 200-line cap on the memory index means less context gets consumed by orientation and more gets allocated to the work I'm actually asking for.

**Cross-session continuity got better.** Claude now references decisions from 15 sessions ago with the same confidence as decisions from last session. The consolidation process creates a flat temporal structure -- everything that's still relevant is equally accessible, regardless of when it was originally recorded.

If you've been working with Claude Code on any project longer than a couple of weeks, I'd strongly recommend triggering a manual dream cycle. Even if you don't have the automatic feature yet, the one-time consolidation is worth the 9 minutes.

For teams that need this kind of persistent memory architecture built into production AI systems, [I take on Claude Code integration projects on Fiverr](https://www.fiverr.com/s/EgxYmWD) -- building out the memory management, agent workflows, and automation layers that turn Claude Code from a coding assistant into a genuine team member.

## The Bigger Picture: AI Agents Are Learning to Sleep

Step back from the implementation details for a moment and consider what Anthropic is actually doing here.

They're building an AI agent that works during the day and processes its experiences at night. It accumulates knowledge through active sessions, then consolidates that knowledge during downtime through a structured review process that mirrors biological memory systems. The four phases -- orient, gather signal, consolidate, prune -- aren't arbitrary engineering choices. They map onto documented stages of memory consolidation in neuroscience research.

This matters beyond Claude Code. The [Sleep-time Compute paper](https://arxiv.org/abs/2504.13171) from UC Berkeley and Letta demonstrated that offline processing during idle periods can reduce active inference costs by 2.5x while maintaining accuracy. The researchers specifically studied this in the context of agentic software engineering tasks -- exactly the domain Claude Code operates in.

The implications extend to any long-running AI agent system. Customer support agents that consolidate interaction patterns overnight. Code review agents that synthesize feedback trends across hundreds of PRs. Project management agents that distill weeks of standup notes into actionable project health summaries. The pattern is the same: accumulate during active use, consolidate during downtime, return sharper.

What Anthropic shipped here isn't just a feature. It's a paradigm for how persistent AI agents should manage knowledge over time. And the fact that they modeled it on biological memory systems rather than traditional database optimization suggests they're thinking about agent cognition at a fundamentally different level than most AI companies.

I wrote about [self-improving AI systems](/content/mejba.me/self-improving-claude-code-systems.md) a while back -- systems that evaluate their own performance and autonomously improve. Auto Dream is a complementary capability. Self-improvement changes what the agent *does*. Memory consolidation changes what the agent *knows*. Together, they create agents that get genuinely better over time without constant human maintenance.

## The Honest Limitations

I'd be doing you a disservice if I painted this as a solved problem. Auto Dream has real limitations worth knowing about.

**It's still experimental.** The feature is undocumented, partially rolled out, and could change significantly before official release. Building critical workflows that depend on Auto Dream's current behavior is risky. Anthropic hasn't committed to the API surface, the trigger conditions, or even the four-phase structure.

**The 200-line cap is a blunt instrument.** For complex projects with deep architectural history, 200 lines isn't enough. I've worked on projects where the architecture decisions alone would fill 200 lines. The pruning agent has to make judgment calls about what's "stale" vs. what's "foundational," and those judgment calls aren't always right. I've seen it prune a database migration note that was still relevant because the last reference to it was 30 sessions ago.

**Manual triggers don't perfectly replicate auto behavior.** When you tell Claude to "dream" inline, it runs in your current session's context. The actual Auto Dream sub-agent runs in a dedicated, restricted environment with specific permissions and isolation. The inline version works, but it's not identical.

**There's no undo.** If the dream agent prunes something you needed, it's gone. The memory files get overwritten in place. There's no version history, no rollback mechanism. If you're on a project where memory accuracy is critical, consider git-tracking your `~/.claude/projects/<project>/memory/` directory before triggering a consolidation. I do this now as a standard practice.

**It doesn't handle multi-developer projects gracefully yet.** The memory directory is per-project based on git repository path, which means all developers sharing a repo share the same memory directory. Auto Dream consolidates based on all sessions, not just yours. In a team setting, this could consolidate memories that reflect different developers' conflicting preferences. The [GitHub issue tracking this](https://github.com/anthropics/claude-code/issues/38493) identifies identity, accuracy, and transparency as the three primary gaps.

These are real limitations. They don't negate the value -- even with these caveats, consolidated memory is dramatically better than unchecked memory bloat. But go in with clear eyes.

## What I'd Recommend Right Now

If you're using Claude Code on any project longer than 10 sessions, here's what I'd do today:

**Step 1:** Run `/memory` in your next Claude Code session. Check whether you see the Auto-dream toggle. If it's there and set to "on," you're already benefiting. If it's "off" or missing, proceed to step 2.

**Step 2:** Before triggering any consolidation, back up your memory directory. Run:

```bash
cp -r ~/.claude/projects/$(pwd | sed 's|/|%2F|g')/memory ~/claude-memory-backup-$(date +%Y%m%d)
```

**Step 3:** In your Claude Code session, type: "Consolidate my memory files. Review all existing memories, remove contradictions, convert relative dates to absolute dates, merge duplicates, and prune stale entries."

**Step 4:** Wait 8-10 minutes. You can keep working on other things -- the consolidation runs in the background.

**Step 5:** After completion, review the updated memory files. Check that nothing critical was pruned. If something important is missing, restore from your backup and re-run with more specific instructions about what to preserve.

**Step 6:** Repeat monthly, or whenever Claude Code starts showing signs of memory confusion -- contradicting earlier decisions, referencing outdated code, or hedging on facts it should know confidently.

If you want to automate this, the [dream-skill project](https://github.com/grandamenium/dream-skill) on GitHub implements a 24-hour auto-trigger that mimics the official Auto Dream behavior. It's community-built, not Anthropic-official, but the architecture is sound.

## Your AI Agent Has a Subconscious Now

The session that started this whole investigation -- the one at 11 PM where Claude suddenly felt sharper -- ended up being one of the most productive I've had on that project. Claude referenced a caching strategy I'd dismissed in session 6 but that had become relevant again after our architecture changes in session 19. It didn't just remember the strategy existed. It understood the timeline: originally dismissed because we were on a single server, now relevant because we'd moved to a distributed setup.

That kind of temporal reasoning across 19 sessions of accumulated context is exactly what consolidation enables. The raw memory files had both entries -- "dismissed caching strategy" and "moved to distributed architecture" -- but they were disconnected notes buried in noise. After consolidation, they were organized, dated, and connected.

Your AI coding agent now dreams. It reviews its experiences while you sleep. It wakes up sharper, with cleaner memories and better judgment. A year ago that sentence would have sounded like science fiction. Today it's a feature you can toggle in your terminal.

The question isn't whether this changes how you use Claude Code. It does. The question is what happens when every AI agent in your workflow gets this capability -- when your code reviewer, your project planner, your deployment monitor, and your documentation writer all consolidate their experiences overnight and show up to work the next morning a little smarter than they were the day before.

That future isn't a year away. The infrastructure is already in the binary.

## Frequently Asked Questions

### What is Claude Code Auto Dream?

Auto Dream is an experimental Claude Code feature that consolidates memory files between sessions, similar to how REM sleep organizes human memories. It runs a background sub-agent that prunes stale entries, resolves contradictions, converts relative dates to absolute timestamps, and reorganizes memories into clean topic files. Access it through the `/memory` command in any Claude Code session.

### How do I enable Auto Dream in Claude Code?

Run `/memory` in your Claude Code session to check for the Auto-dream toggle. If the toggle shows but won't activate, you can manually trigger consolidation by typing "consolidate my memory files" directly to Claude. The feature requires Claude Code v2.1.59 or later and is still in a phased rollout as of March 2026.

### Does Auto Dream delete my Claude Code memories?

Auto Dream prunes stale and contradicted entries but preserves current, relevant information. It removes outdated facts (like references to frameworks you've since migrated away from), merges duplicate entries, and keeps the memory index under 200 lines. Back up your memory directory before first use -- there's no built-in undo mechanism.

### How long does Claude Code Auto Dream take to run?

A typical consolidation cycle takes 8-10 minutes depending on the number of accumulated sessions. It runs in the background without blocking your active Claude Code session. The process only triggers automatically when more than 24 hours and more than 5 sessions have passed since the last consolidation.

### What's the difference between Auto Memory and Auto Dream?

Auto Memory captures information during active sessions -- it encodes notes about decisions, preferences, and project details as they happen. Auto Dream consolidates those notes between sessions -- reviewing, organizing, and pruning accumulated memories so they remain useful over time. Auto Memory writes forward; Auto Dream looks backward. Both are necessary for effective long-term agent memory.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
