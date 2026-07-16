**BRAND:** mejba.me
**TITLE:** 10 Claude Code Features Most Developers Never Find
**SLUG:** claude-code-hidden-features-guide
**PRIMARY KEYWORD:** Claude Code features
**SECONDARY KEYWORDS:** Claude Code commands, Claude Code tips, Claude Code automation
**META DESCRIPTION:** 10 powerful Claude Code features most developers miss -- from /insights reports to parallel agent teams. Real tests, real workflows, real results.
**TAGS:** Claude Code, AI Development, Developer Productivity, Automation, Guide
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will know how to use 10 advanced Claude Code features that dramatically change their daily workflow -- from automated code reviews to multi-agent orchestration.

---

I thought I knew Claude Code. Six months of daily use, hundreds of sessions, an entire content operation running through it. I'd written a [50-tip guide](/content/mejba.me/50-claude-code-tips-guide.md) that covered everything from keyboard shortcuts to context engineering. I was the guy other developers asked when they got stuck.

Then someone showed me `/insights` and I realized I'd been leaving half the tool on the table.

The report it generated wasn't a generic summary. It was a forensic breakdown of my last thirty days -- every duplicated effort, every wasted token loop, every pattern where I kept re-explaining something Claude should have already known. One finding hit particularly hard: I'd been manually running the same three-step code review process on every PR when a single command could have done it automatically with better coverage. That discovery alone saved me roughly four hours a week.

What followed was a two-week deep dive into every command, mode, and capability I'd somehow missed. Some of these features shipped quietly in updates I hadn't read the changelogs for. Others had been there all along, hiding behind slash commands I'd never tried. A few were so powerful I genuinely couldn't believe I'd been working without them.

Here are ten features that changed how I use Claude Code. Not tips. Not shortcuts. Entire capabilities that most developers either don't know exist or haven't figured out how to use properly. I've tested every single one on real projects, and I'll tell you exactly what worked, what surprised me, and where the rough edges still show.

The first one already rewrote my workflow within twenty minutes of running it.

## 1. The Insights Report That Exposes Your Blind Spots

Most developers have zero visibility into how they actually use Claude Code. You know roughly how many sessions you run. You have a vague sense of your token consumption. But the specific patterns -- where you waste time, which tasks you repeat unnecessarily, what habits are costing you -- remain invisible.

The `/insights` command changes that. It reads your last thirty days of session data and compiles a detailed HTML report covering your usage patterns, cost breakdown, tool utilization, and workflow inefficiencies. Think of it as a performance review, except the reviewer has access to every single interaction you've had with the tool.

When I first ran it, the report surfaced three patterns I would never have caught on my own.

The first was duplication. I'd been asking Claude to set up the same testing boilerplate across different projects -- identical Vitest configurations, identical mock setups, identical assertion patterns. The insights report flagged this and suggested creating a custom skill that would handle it in one command. I built that skill in fifteen minutes. It now runs automatically on every new project.

The second finding was error clustering. About 40% of my session errors came from the same root cause: context window exhaustion during large file edits. The report didn't just identify the problem -- it recommended specific hooks to auto-compact context before it hit the ceiling. I implemented the hook that afternoon, and those errors dropped to near zero.

The third was the one that stung: the report showed I was spending an average of twelve minutes per session re-explaining project conventions that should have been in my `CLAUDE.md` file. Twelve minutes. Across five to six sessions a day. That's over an hour of daily waste on something a twenty-line addition to my config file would fix.

The insights report also breaks down your spending by model tier, shows which tools you use most (and which you never touch), and identifies sessions where you could have used a cheaper model without quality loss. For anyone spending serious money on Claude Code -- Max subscribers burning through tokens on complex projects -- this data pays for itself immediately.

Run `/insights` once. Read the full report. Then run it monthly. The patterns it catches aren't obvious from inside individual sessions. You need the thirty-day view to see them.

But identifying inefficiencies is only half the equation. The next feature lets you control exactly how hard Claude thinks about your problems -- and most people leave it on the wrong setting.

## 2. Effort Control: Stop Overpaying for Simple Tasks

Here's something I got wrong for months: I was running every single prompt at the same processing intensity. A simple variable rename got the same deep reasoning chain as a complex architectural refactoring. That's like hiring a structural engineer to hang a picture frame.

The `/effort` command lets you dial Claude's reasoning intensity up or down across five levels: `low`, `medium`, `high`, `max`, and `auto`. Each level changes how deeply Claude analyzes your request before responding -- and critically, how many tokens it burns in the process.

As of March 2026, Anthropic changed the default effort level for Max and Team subscribers from `high` to `medium`. If you hadn't noticed that change, you might have felt like Claude got slightly less thorough recently. It did. Intentionally. Because `medium` handles the majority of development tasks perfectly well, and `high` was burning tokens unnecessarily on straightforward work.

Here's how I've calibrated my usage after testing each level extensively:

**Low effort** works for file searches, simple renames, formatting fixes, and quick lookups. Claude skims the request and responds fast. Token cost is minimal. I use this maybe 20% of the time.

**Medium effort** handles most coding tasks: implementing well-defined features, writing tests against existing code, refactoring with clear patterns. This is my default, and it's the right default for probably 60% of development work.

**High effort** is where Claude starts doing genuine deep reasoning. Complex bug investigation across multiple files. Architectural decisions with trade-offs. Security reviews. Code that requires understanding subtle interactions between systems. I switch to high for critical reviews, production deployments, and anything where a missed edge case would be expensive. Maybe 15% of my work.

**Max effort** triggers the deepest reasoning available -- extended thinking with full chain-of-thought. I reserve this for genuine hard problems: debugging race conditions, designing systems from ambiguous requirements, reviewing security-critical code. Using it for routine tasks is wasteful. Maybe 5% of my sessions.

**Auto** lets Claude decide based on the complexity it detects in your prompt. I've tested this extensively, and it's surprisingly accurate at matching effort to task complexity. If you don't want to think about effort levels at all, `auto` is a solid hands-off choice.

The practical impact is real. After switching from an implicit `high` default to deliberate effort management, my token consumption dropped by roughly 30% with no measurable change in output quality for routine tasks. The expensive reasoning only fires when it's actually needed.

One workflow pattern that works particularly well: start a complex investigation on `high`, get the diagnosis, then switch to `medium` for the implementation. The hard thinking happens once. The execution runs lean.

Effort management is about precision. But the next feature is about freedom -- specifically, the freedom to walk away from your desk without losing a session.

## 3. Remote Control: Your Terminal Session, Anywhere

I wrote a [full deep dive on Remote Control](/content/mejba.me/claude-code-remote-control-phone.md) when it launched in February 2026, so I won't repeat every detail here. But it belongs on this list because most developers I talk to either haven't tried it or misunderstand what it actually does.

The short version: type `/rc` in any active Claude Code session. A QR code appears. Scan it with the Claude app on your phone. You now have full bidirectional control of that terminal session from your mobile device. Read messages, approve file edits, send new instructions -- all while your local environment keeps running exactly as it was.

The architecture matters. Your session stays local. Your filesystem, MCP servers, project configuration, custom tools -- nothing moves to the cloud. Remote Control opens a secure window into your existing session over TLS, with short-lived credentials scoped to that connection. It's not a cloud IDE. It's a remote viewport.

Where this changed my workflow: long-running tasks. I'll kick off a complex refactoring or a full test suite run, then step away to grab lunch, walk the dog, or move to a different room. From my phone, I can monitor progress, approve or reject changes, and send follow-up instructions. When I get back to my desk, the session is exactly where I left it.

The limitation is real, though. Your machine has to stay on and your terminal has to stay open. Close the lid on your laptop, and the session ends. This isn't "start a task and come back tomorrow." It's "step away from your desk without breaking flow." That distinction trips people up.

Remote Control requires a Max subscription and works through claude.ai/code and the Claude iOS/Android apps. If you're on a Pro plan, Anthropic has indicated broader access is coming, but as of March 2026 it's still Max-only.

For those who do have access, though, it quietly eliminates one of the most frustrating patterns in AI-assisted development: the forced choice between being productive and being present at your desk. I use it almost daily, and the freedom it creates is hard to overstate.

Walking away from your desk is one kind of freedom. The next feature gives you a different kind -- the ability to throw massive amounts of work at Claude all at once.

## 4. Batch Command: Parallel Execution at Scale

The `/batch` command is where Claude Code stops feeling like a coding assistant and starts feeling like a deployment pipeline.

Here's the scenario that made me understand its power. I had a codebase with 23 React components that needed migrating from an old styling approach to a new design token system. Each component was independent -- no shared state, no cross-dependencies. In the old workflow, I'd have done them sequentially: open component, explain the migration pattern, let Claude refactor, review, move to the next one. At maybe ten minutes per component, that's nearly four hours of tedious, repetitive work.

With `/batch`, I described the migration pattern once and told Claude to apply it across all 23 components in parallel. It decomposed the work into independent units, spawned isolated worktree agents for each one, and ran the migrations simultaneously. Each agent worked in its own git worktree, so there was zero risk of conflicting edits. When the batch completed, I had 23 pull requests -- one per component -- ready for review.

Total time: about eighteen minutes. Not four hours. Eighteen minutes.

The command supports both parallel and sequential execution modes. Parallel is for independent tasks like the migration above. Sequential is for tasks with dependencies -- where step 2 needs the output of step 1. You specify the mode, describe the work, and Claude handles the orchestration.

For content creators, batch processing is equally powerful. I've used it to generate multiple blog post drafts simultaneously, each targeting a different keyword cluster, with separate research contexts for each. The agents don't share context, so there's no cross-contamination between topics.

The GitHub integration is the part that makes it production-ready. Each batch unit can automatically create a branch, commit changes, and open a PR. For a migration affecting dozens of files, this means you get a clean PR per logical unit of work instead of one massive, unreviewable pull request. Code reviewers appreciate this more than you might think.

Batch processing has rough edges. Complex tasks with subtle interdependencies sometimes need manual intervention when Claude's dependency detection misses a connection. And the token cost scales linearly -- spawning 23 agents means paying for 23 agents. But for work that's genuinely parallelizable, the time savings are transformative.

Batch handles quantity. The next feature handles quality -- and it does it by running three critics on your code simultaneously.

## 5. Simplify: Three-Agent Code Review in One Command

I used to do code reviews the manual way: read through changes, check for duplications, look for bugs, think about performance. It took real cognitive effort, and I'll be honest -- I missed things. Everyone does. Human attention is inconsistent, especially on the third review of the day.

`/simplify` replaces that process with a three-agent pipeline that runs simultaneously. Each agent has a distinct focus:

**Agent 1: Duplication Detection.** Scans for repeated logic, copy-pasted patterns, and opportunities to extract shared functions. This agent caught something in my codebase that I'd walked past for weeks -- three separate API route handlers that each implemented their own rate-limiting logic instead of using the middleware I'd already built.

**Agent 2: Bug and Error Analysis.** Looks for logic errors, edge cases, null reference risks, and incorrect assumptions. It operates more like a security-minded reviewer than a linter -- it's not checking syntax, it's checking reasoning. On a recent project, it flagged a race condition in my database transaction handler that would only manifest under concurrent writes. I never would have caught that in a manual review.

**Agent 3: Efficiency Review.** Evaluates performance characteristics, identifies unnecessary computations, spots N+1 queries, and suggests structural improvements. This agent recommended converting a synchronous file processing pipeline to streaming, which reduced memory usage on large uploads by roughly 70%.

The three agents run in parallel, compile their findings, and then -- this is the key part -- `/simplify` automatically applies the fixes. Not just suggestions. Actual code changes. You review the diffs afterward, which is far more efficient than reading a list of recommendations and implementing them yourself.

I've integrated `/simplify` into my pre-PR workflow. Before I open any pull request, I run it once. The agents consistently find issues I missed. Not always critical ones -- sometimes it's just a helper function that could be extracted, or a variable name that's misleading. But the cumulative effect is cleaner, more maintainable code shipping to production.

One caveat: `/simplify` is designed as a quality gate before PRs, not as a development-time tool. Running it mid-implementation creates unnecessary noise because it reviews code that isn't finished yet. Wait until you're ready to commit, then run it. The findings are more meaningful against completed work.

Three agents reviewing your code is impressive. The next feature takes automation further -- it turns Claude Code into a scheduled worker that runs without you.

## 6. Loop: Cron Jobs Inside Your Terminal

The `/loop` command turned Claude Code from a tool I use into a tool that works for me while I do other things.

The concept is simple: you give Claude a prompt and a time interval, and it executes that prompt repeatedly on schedule for as long as your session stays active. It's essentially a cron job that runs inside your Claude Code terminal.

```
/loop 3h check my email inbox via the Gmail MCP and summarize any new messages from clients
```

That runs every three hours. Claude checks for new emails, summarizes them, and presents the results. I set this up on Monday mornings and it catches client messages I might not get to for hours otherwise.

But the more powerful use cases are development-focused. Here are the loops I run regularly:

**Deployment monitoring:** `/loop 30m check the production error logs for any new 500 errors and summarize the stack traces`. This runs every thirty minutes during business hours. When an error hits production, I know about it before customers report it.

**PR review reminder:** `/loop 2h check our GitHub repo for any open PRs that have been waiting for review for more than 24 hours and list them`. This keeps our review queue from going stale. The team has gotten noticeably faster at reviewing since I set this up.

**Dependency checks:** `/loop 6h scan package.json for any dependencies with known security vulnerabilities using the npm audit tool`. Once daily is probably enough for most teams, but I run it more frequently on projects with rapid dependency changes.

The critical limitation: loops only run while your session is active. Close the terminal, and the loop stops. This isn't a background service. It's a session-scoped automation. For persistent scheduled tasks, you'd want actual cron jobs or a tool like the `/schedule` command for remote triggers. But for workday automation -- the eight to ten hours you're actively developing -- `/loop` is remarkably useful.

I've also chained loops with hooks (which we'll cover next) to create automated workflows. A loop checks for a condition, and when the condition is met, a hook triggers an action. Loop detects a new PR, hook runs `/simplify` on it automatically. That combination turned code review from a manual process into a semi-automated one.

There's one behavior to watch for: loops consume tokens on every execution. A prompt that uses 5,000 tokens per run, looped every thirty minutes over an eight-hour day, costs 80,000 tokens. That adds up. I keep my loop prompts lean and focused to avoid surprise bills.

Loops automate on a schedule. The next feature automates on events -- and it's one of the most underutilized capabilities in the entire platform.

## 7. Hooks: The Automation Layer Most People Ignore

Hooks are configurable actions that fire before or after Claude Code uses specific tools. They're defined in your `.claude/settings.json` file, and they can fundamentally change how your entire workflow operates.

Think of hooks like Git hooks, but for AI tool usage. You can trigger shell commands, enforce rules, run validations, or perform automated actions every time Claude reads a file, writes code, runs a command, or interacts with any of its nineteen supported tool events.

Here's a practical example. I have a hook that runs before every file write:

```json
{
  "hooks": {
    "preToolUse": [
      {
        "matcher": "write",
        "command": "echo 'Checking file size and backup...'"
      }
    ]
  }
}
```

That's a simplified version. My actual hook checks the target file's size, creates a timestamped backup in a `.backups` directory, and validates that the write won't exceed a configured maximum file length. All of this happens automatically before Claude writes a single line.

The hooks I've found most valuable in practice:

**Brand voice enforcement.** For my content workflow, I have a post-write hook that scans generated content for banned phrases (the AI-sounding ones like "in today's fast-paced world" or "it's important to note") and flags them before the file is saved. This catches voice inconsistencies that would otherwise require manual editing.

**Token cost management.** A pre-tool hook that estimates the token cost of the current operation and warns me if it exceeds a threshold. This prevents runaway token consumption during large batch operations. I set the threshold at 50,000 tokens per single tool call -- anything above that gets a confirmation prompt.

**Automatic formatting.** A post-write hook that runs Prettier on any TypeScript or JavaScript file Claude modifies. The code ships formatted without me thinking about it.

**Test validation.** A post-write hook on test files that automatically runs the relevant test suite after Claude writes or modifies tests. If the tests fail, the hook feeds the failure output back into the conversation so Claude can fix the issue immediately. This creates a tight write-test-fix loop without manual intervention.

The [self-improving systems approach](/content/mejba.me/self-improving-claude-code-systems.md) I wrote about previously can be partially implemented through hooks alone. A hook that logs every session's key decisions, paired with a loop that periodically reviews those logs for patterns, creates a lightweight feedback system that continuously improves your workflow.

Hooks are powerful precisely because they're invisible during normal use. Once configured, they run silently in the background, enforcing your rules and automating your processes without requiring any thought. The setup cost is a one-time investment. The return is permanent.

Speaking of invisible -- the next feature is so subtle most developers don't even know the command exists.

## 8. Rewind Mode: Undo Without the Panic

Every developer who uses AI-assisted coding has had this moment: Claude makes a series of changes across multiple files, you realize halfway through that the approach is wrong, and now you're staring at a mess of modified files trying to figure out what changed where.

Git can help. But reverting individual commits when Claude made changes across a single conversation turn is tedious. And if you haven't committed yet, `git checkout` becomes a blunt instrument that might undo things you wanted to keep.

Rewind Mode offers a surgical alternative. Double-tap `Esc` to open the rewind menu, and you get two options:

**Rewind conversation only** -- rolls back the conversation to a previous state while keeping all code changes. Useful when Claude went down a wrong explanatory path but the actual code modifications were fine.

**Rewind code only** -- reverts file changes while preserving the conversation history. This is the one I use most. Claude tried an approach, the code didn't work, but the conversation context (the problem description, the constraints, the failed attempt) is valuable for the next attempt. I rewind the code, keep the context, and redirect: "That approach had X problem. Try Y instead."

The combination is powerful for experimental development. I'll tell Claude to try a risky refactoring, knowing that if it doesn't work, I can rewind the code changes in seconds. This changes how you approach speculative work -- the cost of trying something drops to nearly zero when undoing it is instant.

Before rewind existed, I was doing this manually: committing before every major Claude operation, then cherry-picking or reverting if things went wrong. That worked, but it cluttered my git history with checkpoint commits that had no business being there. Rewind keeps experiments out of your version history entirely.

One thing to understand: rewind operates on conversation turns, not individual file changes. If Claude modified five files in a single turn, rewinding rolls back all five. You can't selectively rewind file three while keeping files one, two, four, and five. For that granularity, you still need git.

But for the common case -- "that whole approach was wrong, let's try something different" -- rewind is exactly the right tool. Fast, clean, and far less stressful than manual rollbacks.

Rewind handles mistakes after they happen. The next feature handles questions that pop up while you're in the middle of something else.

## 9. The /btw Command: Side Questions Without Derailing Your Session

This one launched on March 10, 2026, with Claude Code v2.1.72, and it solved a frustration I'd been living with for months without realizing there was a fix.

The scenario: you're deep in a debugging session. Claude is tracking a complex chain of function calls across multiple files. You've built up fifteen turns of context, and the conversation is focused and productive. Then a thought hits you -- "wait, how many context tokens do I have left?" or "what's the recommended approach for X?" -- but asking that question in the main thread would derail the entire debugging flow.

Before `/btw`, you had two bad options. Ask the question and accept that Claude's attention would shift to answering it, potentially losing the thread of the debug session. Or open a new terminal, start a fresh session, and ask there -- losing context and paying for a whole new session initialization.

`/btw` creates a side channel. You ask your question, Claude answers it, and the main conversation continues from exactly where it was. The side question doesn't become part of the primary thread. It doesn't influence Claude's reasoning about the main task. It's a true parenthetical -- acknowledged, answered, and set aside.

```
/btw how many context tokens are remaining in this session?
```

Claude answers. Then your next prompt continues the debugging session as if the side question never happened.

I use `/btw` for three things consistently:

**Context monitoring.** Checking remaining tokens mid-session without disrupting flow. This is especially important during long sessions where I need to know if I should compact or continue.

**Quick clarifications.** "What's the syntax for X in this framework?" when I need a fast answer but don't want to break the problem-solving chain.

**Meta-questions about the session.** "Are you tracking the three constraints I mentioned earlier?" -- a sanity check that Claude's context is intact, without polluting the main thread with meta-discussion.

It's a small feature. The kind of thing that doesn't make headlines. But it removes a genuine friction point from long sessions, and long sessions are where the most valuable work happens. Anything that protects the integrity of a deep, focused conversation is worth knowing about.

We've covered nine features so far. The tenth one is the biggest. It's not a command -- it's a paradigm shift in how Claude Code works. And if you've been using Claude as a solo assistant, this will change your mental model entirely.

## 10. Parallel Agent Teams: From Solo Assistant to Full Dev Team

Everything up to this point treats Claude Code as a single entity. One agent, one conversation, one stream of work. Parallel agents break that model completely.

When you spin up agent teams, Claude Code doesn't just run one session. It launches multiple independent sessions -- each with its own context window, its own role, and its own slice of the work. A lead agent coordinates the team, delegates tasks, and synthesizes results. Teammate agents execute independently, report back, and even communicate with each other.

This isn't the same as sub-agents. The distinction matters, and I got confused about it initially, so let me be precise.

**Sub-agents** operate within a single Claude Code session. The parent agent spawns them to handle specific tasks, but they share the session's overall context and operate within its constraints. They're useful for parallelizing work within a bounded scope -- like the [agent swarm architecture](/content/mejba.me/claude-code-agent-swarm-architecture.md) I covered previously.

**Parallel agent teams** are fundamentally different. Each teammate is a fully independent Claude Code session with its own 200K-token context window. They don't share context with the lead agent or with each other -- they communicate through explicit message passing. This means a teammate can hold an entire codebase's worth of context for its specific domain without consuming tokens from anyone else's window.

I tested this on a project that needed three things simultaneously: a backend API redesign, a frontend component refactor, and a comprehensive test suite update. In the old model, I'd tackle these sequentially -- API first, then frontend, then tests -- because a single agent couldn't hold enough context for all three simultaneously.

With parallel agents, I spun up three teammates:

- **Backend Agent:** Full context on the API layer, database schemas, and route handlers
- **Frontend Agent:** Full context on the React component tree, state management, and UI patterns
- **Test Agent:** Full context on the existing test infrastructure, mock setup, and coverage requirements

The lead agent coordinated: "Backend is redesigning the user endpoint. Frontend, wait for the new API contract before updating the user profile component. Test agent, start writing integration test stubs based on the current spec -- we'll fill in the assertions once the API is finalized."

Each agent worked in its own worktree. No merge conflicts. No context contamination. The backend agent could load the entire API layer into its context window because it wasn't also holding frontend code. The test agent could analyze the full test suite without crowding out the implementation context.

The result was roughly 3x throughput compared to sequential single-agent work. Not because each individual agent was faster, but because three agents working simultaneously with full context in their respective domains is categorically different from one agent context-switching between domains.

The communication model is what makes it work. Teammates don't have direct access to each other's context, but they can send structured messages through the lead agent. When the backend agent finalized the new API contract, it sent the endpoint specifications to the lead, who forwarded them to the frontend and test agents. Each agent incorporated the information into its own context without losing the domain-specific knowledge it had already built up.

This is the feature that made me rethink what Claude Code actually is. It's not an assistant anymore. It's a platform for running AI development teams. The mental model shift -- from "I have a really smart helper" to "I'm managing a team of specialists" -- changes how you plan work, how you decompose problems, and what you consider feasible for a single development session.

If you'd rather have someone set up this kind of multi-agent workflow from scratch, I take on Claude Code automation and agent architecture engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

The token cost scales with the number of agents, obviously. Three teammates means roughly three times the token consumption. But for complex projects where the alternative is sequential work across multiple sessions anyway, the total cost is often comparable -- you're just spending it in parallel instead of spread across days.

## What These Features Share: A Philosophy of Compound Returns

Individually, each of these ten features solves a specific problem. Insights shows you where you're wasting effort. Effort control prevents overspending on simple tasks. Remote Control untethers you from your desk. Batch processing parallelizes repetitive work. Simplify catches bugs you'd miss. Loop automates recurring checks. Hooks enforce rules silently. Rewind eliminates the cost of experimentation. /btw protects deep focus. Parallel agents multiply your throughput.

Stacked together, they transform Claude Code from a smart autocomplete into something closer to an engineering department.

The compounding effect is where the real value lives. Hooks trigger on loop-detected conditions. Insights data informs your effort calibration. Batch operations benefit from simplify running as a post-processing step. Parallel agents use rewind independently when individual approaches fail. Each feature amplifies the others.

Most developers I talk to use maybe three or four Claude Code commands regularly. They know how to prompt, how to review, how to approve edits. That's like buying a professional camera and only using auto mode. The capabilities are there. The investment to learn them is measured in hours. The return is measured in weeks of recovered time.

My challenge to you: pick one feature from this list that you haven't used. Try it on your next project. Not on a toy example -- on real work. Give it one honest attempt and see what changes.

Based on what I've seen from my own workflow -- and from the insights report that started this whole journey -- you won't go back to working without it.

## Frequently Asked Questions

### How do I run the Claude Code insights report?

Type `/insights` in any active Claude Code session. It analyzes your last 30 days of usage and generates an HTML report covering patterns, costs, tool usage, and workflow inefficiencies. No configuration required -- it reads your existing session history automatically.

### What is the difference between Claude Code sub-agents and parallel agent teams?

Sub-agents operate within a single session and share context with the parent agent. Parallel agent teams launch fully independent sessions, each with its own 200K-token context window, communicating through explicit message passing. Teams handle larger, more complex projects where domain isolation matters.

### Does Claude Code Remote Control work on mobile?

Yes. Remote Control works through claude.ai/code and the Claude iOS and Android apps. Your session continues running locally on your machine while you interact through the mobile interface. It requires a Max subscription as of March 2026.

### How much do parallel agents cost in tokens?

Token cost scales linearly with the number of agents. Three parallel agents consume roughly three times the tokens of a single agent. However, total project cost is often comparable to sequential work since you're consolidating what would otherwise be multiple separate sessions.

### Can I run /loop commands in the background permanently?

No. Loop commands only execute while your Claude Code session is active. Closing the terminal stops all loops. For persistent scheduled automation, use the `/schedule` command which creates remote triggers that run on a cron schedule independently of your local session.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
