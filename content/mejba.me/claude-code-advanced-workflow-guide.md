**BRAND:** mejba.me
**TITLE:** The Claude Code Workflow That Replaced My Team
**META TITLE:** Claude Code Advanced Workflow: Plan, Validate, Ship
**SLUG:** claude-code-advanced-workflow-guide
**PRIMARY KEYWORD:** Claude Code workflow
**SECONDARY KEYWORDS:** Claude Code plan mode, Claude Code subagents, Claude Code validation hooks
**META DESCRIPTION:** The 9-step Claude Code workflow used by senior engineers at Google and Amazon. Plan mode, validation loops, subagents, and parallel sessions explained.
**TAGS:** Claude Code, AI Development, Developer Productivity, Workflow Automation, Tutorial
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will be able to implement a structured 9-step Claude Code workflow that uses plan mode, validation hooks, slash commands, subagents, and parallel sessions to ship production code autonomously.

---

Six weeks ago, I watched a senior engineer ship a complete authentication system, a payment integration, and a dashboard redesign — all in a single afternoon. Not prototypes. Not throwaway demos. Production code with tests, type safety, and clean Git history. She had three Claude Code sessions running simultaneously, each in its own worktree, each handling a different feature while she reviewed pull requests from the previous batch.

I asked her how long it took to build this workflow. "About a week of deliberate practice," she said. "But the real answer is I stopped treating Claude like a chatbot and started treating it like a junior engineer who needs structure to do their best work."

That framing stuck with me. I'd been using Claude Code for months at that point — shipping projects, building agent systems, writing about it constantly. But my workflow was still mostly reactive. Prompt, generate, review, fix, prompt again. The cycle worked, but it was leaving massive productivity on the table.

Then I came across Maddy's breakdown. Maddy is a senior software engineer who's worked at Google, Amazon, IBM, and Microsoft — not someone prone to hype. Her approach to Claude Code isn't about clever prompts or hidden features. It's a complete methodology: nine interlocking practices that transform Claude from a code-generating chatbot into something that functions like an autonomous engineering team. I've spent the last month implementing every piece of it, and the results forced me to rethink how I structure every project.

Here's the system, piece by piece, with everything I learned trying to make it work in practice.

## Why Most Developers Hit a Ceiling With Claude Code

There's a pattern I see in developer communities that's so consistent it might as well be a law of nature. Someone discovers Claude Code. They generate a few components, feel the rush of AI-assisted development, and start telling everyone it's the future. Two weeks later, they're frustrated. Bugs multiply. The AI suggests functions that don't exist. Context windows overflow and Claude starts contradicting its own earlier suggestions.

The standard reaction is to blame the model. "It's not smart enough for real projects" or "it works for toy examples but not production code." I said both of those things at various points.

The actual problem is almost never the model. It's the absence of structure around the model.

Think about it this way: if you hired the most talented junior engineer on the planet and gave them zero onboarding, no coding standards, no review process, and no test suite — they'd produce chaos. Not because they lack skill, but because skill without structure produces inconsistent results. Claude Code is the same. The raw intelligence is extraordinary. But intelligence without workflow produces the exact symptoms developers complain about: misaligned implementations, growing bug counts, and the creeping feeling that you're spending more time fixing AI output than you would have spent writing the code yourself.

Maddy's methodology fixes this by wrapping Claude Code in the same kind of engineering discipline you'd apply to a human team. Every step exists to prevent a specific failure mode I've personally experienced. And the compound effect — all nine practices working together — is what produces the "this can't be real" productivity gains that sound like marketing until you experience them.

The catch? You have to implement them deliberately. Skipping steps doesn't just reduce the benefit — it undermines the whole system. I learned that the hard way when I tried to skip plan mode for "simple" tasks and spent three hours debugging what should have been a fifteen-minute feature.

## Step 1: Build a CLAUDE.md That Actually Works

Every structured Claude Code workflow starts with `CLAUDE.md`, and Maddy's approach to this file is more disciplined than what I typically see.

When you run `/init` on any new codebase, Claude performs a structural analysis and generates this file automatically. It captures your project description, file structure, key paths, development commands, coding conventions, and tech stack. The auto-generated version is a solid starting point — dramatically better than writing it from scratch, which is what I used to do before I accepted that Claude's self-analysis catches details I'd forget.

But here's where Maddy diverges from the typical advice: she keeps `CLAUDE.md` between 100 and 200 lines. Not 300. Not 500. One hundred to two hundred.

That constraint felt aggressive to me at first. My own `CLAUDE.md` files had grown past 400 lines on some projects — architectural decisions, coding patterns, example snippets, historical context. It felt thorough. It was actually wasteful. Every token in `CLAUDE.md` gets loaded into every single conversation. A 500-line file silently eats a significant chunk of your context window before you've typed your first prompt. I covered this in detail in my [50 Claude Code tips guide](https://mejba.me/50-claude-code-tips-guide), but the short version is: bloated context files are one of the top reasons Claude's output quality degrades mid-session.

What belongs in the file: project description and core functionality, file structure and key paths, build/test/lint commands (so Claude can validate its own work), coding conventions and tech stack, and hard rules that Claude must never violate. What doesn't belong: code examples (Claude can read your actual codebase), lengthy explanations of past decisions, anything that duplicates your README, and anything that hasn't been relevant in the last two weeks.

The 100-200 line constraint forces you to prioritize. Every line has to earn its place. And that ruthless prioritization is exactly what makes the context window efficient enough for the rest of this workflow to function.

One more thing Maddy emphasizes that I now practice religiously: treat `CLAUDE.md` as a living document. When Claude makes a mistake and you correct it, add a rule that prevents that mistake from recurring. Over time, the file becomes a distilled record of every lesson your project has taught the AI. Boris Cherny, the creator of Claude Code, [does exactly this with a `lessons.md` file](https://mindwiredai.com/2026/03/25/claude-code-creator-workflow-claudemd/) — every correction becomes a permanent rule. The file literally teaches itself to be better at your specific codebase.

## Step 2: Plan Mode Before Every Change

This is the single highest-impact practice in Maddy's entire workflow, and the one most developers skip because it feels like it's slowing them down.

Plan mode (toggled with `Shift+Tab` in the CLI) tells Claude to analyze your codebase and draft an implementation plan before writing any code. It reviews which files need to change, what logic paths are affected, where the risks are, and how the change fits into the existing architecture. You get a clear breakdown of what Claude intends to do — and crucially, you get to approve, modify, or reject that plan before a single line of code is touched.

The practical threshold I've settled on: use plan mode for any task that touches more than one file. For a single-file change — renaming a variable, fixing a typo, adding a comment — just let Claude execute directly. For anything structural, plan first.

Here's why this matters so much in practice. Without plan mode, Claude will sometimes write perfectly functional code in completely the wrong location. I once asked it to add rate limiting to an API and it created a new middleware file instead of adding to the existing one that already handled authentication. The code was correct. The architecture was wrong. I didn't catch it until three features later when I realized I had two middleware chains running independently.

With plan mode, I would have seen "Create new file: middleware/rateLimiter.js" in the plan and immediately corrected it to "Modify existing file: middleware/auth.js — add rate limiting logic." Ten seconds of review saved three hours of refactoring.

The plan-review-execute cycle that Maddy advocates — and that I've now adopted as non-negotiable — looks like this:

1. Describe the task in detail (the more specific, the better)
2. Toggle plan mode and let Claude analyze the codebase
3. Review the plan: correct file paths, challenge architectural decisions, add constraints
4. Approve the plan and let Claude execute
5. Review the output against the original plan

Some engineers take this further. I've seen workflows where one Claude session drafts the plan, then a second session reviews it "as a senior engineer" before execution begins. I tried this for a complex database migration and the review session caught two edge cases the planning session missed. Overkill for most tasks. Invaluable for anything touching production data.

<!-- IMAGE: Terminal screenshot showing Claude Code in plan mode, displaying a structured implementation plan with file paths and proposed changes. Alt text: "Claude Code workflow plan mode showing implementation breakdown before code execution". Caption: "Plan mode gives you a reviewable blueprint before Claude writes a single line." -->

## Step 3: Clear Context After Every Task

This one sounds almost too simple to be important. It's not. It might be the most underrated practice in the entire workflow.

After completing each discrete task, run `/clear` to reset Claude's context window. Every file Claude reads, every command output it processes, every message in the conversation — all of it accumulates in a shared context budget. When that budget fills up, Claude starts losing track of earlier instructions. The symptoms are subtle at first: slightly less precise code, occasional inconsistencies with your coding standards, suggestions that don't quite align with what you asked for. Then they compound until you're debugging AI output that contradicts its own suggestions from twenty minutes ago.

I used to treat `/clear` as something you do when things go wrong. Maddy treats it as hygiene — something you do after every successful task, before things go wrong. The difference is preventive versus reactive, and the preventive approach is dramatically more efficient.

The workflow becomes: complete task, verify it works, commit, then `/clear` before starting the next task. Clean slate. Full context budget available for the next problem. No accumulated noise from the previous conversation.

One practical consideration: if your next task depends heavily on context from the current one, you can carry forward the essential details in your next prompt instead of relying on Claude to remember them. "I just added rate limiting to the auth middleware. Now I need to add corresponding tests for the rate limiting behavior" gives Claude exactly the context it needs without the baggage of everything else that happened in the previous session.

This practice alone eliminated roughly 40% of the "Claude is being weird" moments I used to experience. And it pairs perfectly with plan mode — you're always starting each task with a clear context and a deliberate plan, rather than building on top of accumulated noise.

## Step 4: Slash Commands for Repetitive Prompts

Every developer has prompts they type over and over. "Review this code for security vulnerabilities." "Write unit tests for this function." "Refactor this to follow the repository pattern." Typing these repeatedly is not just tedious — it introduces variation. Your Thursday prompt for code review won't be worded identically to your Monday prompt, and that variation produces different (often worse) results.

Slash commands solve this by turning your best prompts into reusable triggers. They're Markdown files stored in `.claude/commands/` (or `.claude/skills/` — as of early 2026, [these are effectively unified](https://www.morphllm.com/claude-code-skills-mcp-plugins)) that Claude executes when you type the corresponding slash command.

Here's a practical example. I have a command called `/security-review` that contains a detailed prompt I refined over dozens of iterations:

```markdown
# Security Review

Review the current changes for security vulnerabilities. Check for:

1. SQL injection vectors in any database queries
2. XSS vulnerabilities in rendered output
3. Authentication/authorization bypass opportunities
4. Sensitive data exposure in logs or error messages
5. CSRF protection on state-changing endpoints
6. Input validation gaps on user-supplied data

For each issue found:
- Classify severity (Critical / High / Medium / Low)
- Show the vulnerable code
- Provide the fixed version
- Explain the attack vector

If no issues found, confirm the code passes review with a brief summary.
```

Now instead of trying to remember all six check categories every time, I type `/security-review` and get consistent, thorough results. The prompt has been battle-tested. The output is reliable. And I saved the three minutes I'd otherwise spend composing the instruction from memory.

Maddy recommends building slash commands for every prompt you've typed more than three times. I'd go further — build them for any prompt where consistency matters more than creativity. Code review, test generation, documentation updates, API endpoint scaffolding, database migration planning. These are workflows where you want predictable, high-quality output every single time.

The commands are just Markdown files, so they're version-controlled. You can share them across your team. You can iterate on them over time — and every improvement benefits every future invocation. I maintain about fifteen active slash commands across my projects. The three I use most often probably save me thirty minutes a day.

## Step 5: Validation Hooks That Let Claude Self-Correct

Here's where the workflow shifts from "structured prompting" to something that genuinely feels like working with an autonomous engineer.

Validation hooks are automated checks that run after every edit Claude makes. Build the project. Run the test suite. Execute the type checker. If anything fails, Claude sees the error output and automatically attempts to fix it — without you intervening. The AI enters a self-correction loop: edit, validate, see failure, fix, validate again, repeat until everything passes.

This is the feature Maddy credits for the largest single quality improvement in her workflow. And [Boris Cherny, Claude Code's creator, argues](https://code.claude.com/docs/en/common-workflows) that giving Claude a feedback loop improves final output quality by two to three times compared to generate-and-pray.

Setting up hooks in your Claude Code configuration looks like this:

```json
{
  "hooks": {
    "postEdit": [
      "npm run build",
      "npm run test",
      "npm run typecheck"
    ]
  }
}
```

Every time Claude modifies a file, these three commands run automatically. If the build breaks, Claude sees the error. If a test fails, Claude sees which assertion didn't pass and why. If the type checker catches an incompatible type, Claude sees the exact file and line.

The key insight: without hooks, Claude verifies its work using its own judgment. Which sounds reasonable until you remember that Claude's judgment degrades as context fills up. Tests and build checks are an external source of truth that stays accurate regardless of context pressure. Each red-to-green cycle gives Claude unambiguous, machine-verified feedback it can act on without waiting for you.

I want to be honest about the limitations here. Hooks add time to every edit cycle. If your test suite takes 90 seconds, that's 90 seconds of waiting after every change. For rapid iteration on UI components where the feedback is visual, I sometimes disable hooks temporarily. But for anything touching business logic, API endpoints, or data models — hooks stay on. The time they add is a fraction of the debugging time they prevent.

One more nuance that Maddy emphasizes and I've validated in practice: hooks are deterministic. `CLAUDE.md` is advisory — Claude follows it roughly 80% of the time. Hooks execute 100% of the time. If something must happen after every edit without exception — formatting, linting, security checks — make it a hook, not a `CLAUDE.md` rule.

## Step 6: Parallel Sessions and Git Worktrees

This is where the productivity multiplier gets absurd.

Most developers run one Claude Code session at a time. One task, one branch, one sequential pipeline. Maddy runs multiple sessions simultaneously — some in terminal tabs, some in IDE panels — each handling a completely independent task. The secret that makes this work without descending into Git conflict chaos: every session operates in its own [Git worktree](https://mejba.me/claude-code-git-worktrees-parallel-agents).

A worktree is a separate working directory linked to the same repository. Each gets its own branch checked out. Changes in one don't affect the others. Claude Code has native worktree support — you can spawn a session in its own isolated workspace with a single command.

The practical workflow:

1. Create a worktree for each feature: `git worktree add ../auth-feature feature/auth`
2. Open a Claude Code session in each worktree
3. Give each session its task and let them work independently
4. Review and merge when each feature is complete

I typically run three parallel sessions. More than that and the review overhead starts eating into the time savings. Three feels like the sweet spot where each session gets enough attention without becoming a context-switching nightmare for me.

The mental shift here is significant. You stop thinking of yourself as a developer who codes with AI help. You start thinking of yourself as an engineering manager directing a team of AI developers. Your job isn't to write code — it's to plan work, assign it to the right sessions, review output, and make architectural decisions. The actual code generation happens in parallel, across multiple independent workstreams.

If you want the full deep dive on setting up worktrees with Claude Code — including the subtle gotcha where commits can land on `main` when you think they're going to a feature branch — I wrote a [complete walkthrough](https://mejba.me/claude-code-git-worktrees-parallel-agents) that covers every edge case I've hit.

If you'd rather have someone set up this kind of multi-agent development environment for your team, I take on workflow automation engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Step 7: Subagents for Isolated Subtasks

Parallel sessions handle separate features. Subagents handle separate concerns within a single feature.

A subagent is a lightweight, isolated AI agent spawned by your main Claude session to handle a specific subtask. The critical word is "isolated" — each subagent gets its own system prompt, its own tool permissions, and its own context window. It does its job and reports back without polluting the parent session's context.

Claude Code ships with [three built-in subagent types](https://code.claude.com/docs/en/sub-agents): Explore (read-only, optimized for speed and cost), Plan (gathers context before presenting a strategy), and a general-purpose agent that handles tasks requiring both exploration and modification.

But the real power comes from custom subagents. Maddy's example that clicked for me: spawning a security-focused subagent that reviews code changes through a security lens while the main session continues building features. The security agent has its own system prompt loaded with OWASP Top 10 checks and your organization's security policies. It operates independently, flags issues, and its findings don't clutter the main session's context.

Here's how I use subagents in practice:

- **Security review agent:** Runs in parallel with feature development, checks every change for vulnerabilities
- **Documentation agent:** Generates and updates docs as the codebase evolves, isolated from the implementation context
- **Test generation agent:** Writes test cases for completed features while I move on to the next task with the main session

The isolation is what makes subagents fundamentally different from just asking Claude to "also check for security issues" in your main prompt. That approach eats context. It divides Claude's attention. And it produces shallow security analysis because the model is juggling implementation and review simultaneously. A dedicated subagent focuses entirely on its assigned concern, with a system prompt optimized for that specific task.

One thing I learned through experimentation: subagents work best for tasks that are clearly separable from the main workflow. If the subtask requires heavy back-and-forth with the main session — "wait, check this file before I continue" — it's better handled in the main session. Subagents shine when you can fire-and-forget.

## Step 8: Skills — Encoding Your Team's Knowledge

Slash commands handle single-action triggers. Skills handle multi-step workflows.

The distinction matters. A slash command says "do this one thing." A skill says "here's a complete procedure for solving this category of problem, including decision trees, edge cases, and quality criteria." I wrote extensively about this architecture in my [guide to agent skills](https://mejba.me/claude-code-agent-skills-guide), but the short version: skills are how you encode institutional knowledge into reusable, AI-executable playbooks.

Maddy frames it as the difference between giving someone a shortcut and giving them training. Both are useful. Training is what compounds.

A skill is a Markdown file (stored in `.claude/skills/`) that contains a multi-step workflow. Here's an abbreviated example of a code review skill:

```markdown
# Comprehensive Code Review

## Step 1: Architecture Assessment
- Check if changes align with existing patterns in the codebase
- Verify new files are in correct directories per project convention
- Flag any architectural decisions that should be documented

## Step 2: Logic Review
- Trace the happy path through the new code
- Identify edge cases that aren't handled
- Check error handling completeness

## Step 3: Performance Check
- Flag N+1 query patterns
- Check for unnecessary re-renders in React components
- Verify database indexes exist for new query patterns

## Step 4: Security Scan
- Check input validation on all user-supplied data
- Verify authentication/authorization checks
- Review for injection vulnerabilities

## Step 5: Test Coverage Assessment
- Verify critical paths have test coverage
- Check that edge cases from Step 2 have corresponding tests
- Flag any untested error handling paths

## Output Format
For each finding:
- **File:** [path]
- **Line:** [number]
- **Severity:** Critical | High | Medium | Low | Info
- **Finding:** [description]
- **Recommendation:** [specific fix]
```

When you invoke this skill, Claude doesn't just run a quick scan. It executes a structured, five-step review process that catches categories of issues a casual review would miss. And because the skill is version-controlled Markdown, your entire team benefits from every refinement.

The compounding effect Maddy describes — and that I've experienced firsthand — is that skills accumulate your team's hard-won knowledge over time. Every edge case you catch gets added to the relevant skill. Every mistake becomes a check. Six months into this practice, your skills contain the distilled expertise of every project you've worked on. New team members inherit that knowledge instantly.

I currently maintain around twenty skills across my projects. The ones that deliver the most value: code review (five-step process), API endpoint scaffolding (generates route, controller, validation, tests, and documentation in one pass), database migration planning (analyzes existing schema, suggests migration steps, flags data integrity risks), and deployment checklist (runs through environment variables, secrets, DNS, SSL, and monitoring before any production push).

## Step 9: MCP Servers — Extending Claude Beyond Your Codebase

The final piece of Maddy's workflow connects Claude to the rest of your development environment.

MCP (Model Context Protocol) servers are standalone programs that expose tools and resources to Claude Code over a standardized protocol. The concept is straightforward: Claude already reads files and runs commands. MCP servers let it also interact with GitHub, Slack, databases, APIs, and any other external service your workflow depends on.

The [practical setup is simpler than it sounds](https://alexop.dev/posts/understanding-claude-code-full-stack/). You add MCP servers with scope configuration — `user` scope for servers available across all projects, `local` scope for per-project tools, and `project` scope (written to `.mcp.json`) for tools shared with your team via Git.

One thing that surprised me when I first connected MCP servers: Claude Code doesn't load every tool from every server into context at once. It uses tool search to discover tools on demand, pulling in schemas only when needed. This reduces context usage by roughly 95% compared to clients that dump all tool definitions upfront. Smart design decision that makes running multiple MCP servers practical instead of context-prohibitive.

The MCP servers I use daily:

- **GitHub MCP:** Claude can read PR comments, create issues, review diffs, and post review feedback — all without leaving the terminal session
- **Filesystem MCP:** Extended file operations beyond what Claude Code natively supports
- **Slack MCP:** Notifications when tasks complete, error alerts, and status updates posted directly to project channels

Maddy's point about MCP servers resonated with me: they evolve Claude from a coding assistant into something closer to a semi-autonomous engineer that operates across your entire development environment. When Claude can read a GitHub issue, plan the implementation, write the code, run the tests, create the PR, and post a summary in Slack — the human bottleneck shifts from writing code to making decisions.

That's the right bottleneck to have.

## The Compound Effect: Why All Nine Steps Matter Together

I want to be direct about something: implementing any single step from this workflow will improve your Claude Code experience. Plan mode alone is worth the read. Validation hooks alone will reduce your debugging time. Slash commands alone will make your daily workflow more consistent.

But the real transformation happens when all nine steps work as a system.

Here's how they compound. `CLAUDE.md` gives Claude project understanding. Plan mode gives it architectural awareness. `/clear` prevents context degradation. Slash commands ensure consistent input quality. Validation hooks provide automated feedback loops. Parallel sessions multiply throughput. Subagents provide specialized, isolated analysis. Skills encode institutional knowledge. MCP servers extend reach beyond the codebase.

Remove any one piece and the system still works. But it works the way a car works with a flat tire — technically functional, noticeably degraded. The pieces are designed to compensate for each other's limitations. Plan mode catches architectural mistakes that `CLAUDE.md` alone can't prevent. Validation hooks catch implementation errors that plan mode can't foresee. Context clearing prevents the kind of progressive quality degradation that makes everything else less reliable over time.

The workflow I've settled into after a month of practice:

1. Start each project with a lean `CLAUDE.md` (under 150 lines)
2. Start each task with plan mode — review and approve before execution
3. Let validation hooks catch errors automatically during execution
4. Clear context after each completed task
5. Use slash commands for every repeated workflow
6. Run 2-3 parallel sessions on independent features via worktrees
7. Spawn subagents for security review and test generation
8. Maintain skills for multi-step workflows that the team shares
9. Connect MCP servers for GitHub, Slack, and any project-specific integrations

The learning curve is real. The first week of deliberate practice felt slower than my old workflow. I was constantly toggling plan mode, writing slash commands, setting up hooks — overhead that produced no immediate visible output. By the second week, the overhead had become automatic. By the third, I was shipping more in a morning than I used to ship in a day.

## What I Got Wrong — And What I'd Do Differently

I'll be honest about the mistakes I made implementing this workflow, because they'll save you time if you're starting fresh.

**Mistake 1: Implementing everything at once.** I read Maddy's full methodology and tried to adopt all nine practices on day one. The cognitive overhead was brutal. I was so focused on process that I barely wrote any code. Better approach: start with `CLAUDE.md` + plan mode + `/clear`. Add validation hooks in week two. Add slash commands and parallel sessions in week three. Save subagents, skills, and MCP for week four.

**Mistake 2: Overcomplicating slash commands.** My first batch of slash commands were essentially essays. Three hundred words of instructions each. They worked, but they consumed massive context. I've since trimmed them to be as concise as my `CLAUDE.md` — every word has to earn its place. A focused 50-word command outperforms a verbose 300-word one almost every time.

**Mistake 3: Running too many parallel sessions.** I got excited and tried running five simultaneous sessions. The review overhead destroyed the productivity gains. Three is my sweet spot. Two if the tasks are complex. Five was chaos.

**Mistake 4: Forgetting to clear context.** Old habits die hard. I'd get into a flow, complete three tasks without clearing, and wonder why Claude's suggestions got progressively worse. I eventually added a physical sticky note to my monitor: "Did you /clear?" Inelegant. Effective.

**Mistake 5: Using subagents for tasks that needed main-session context.** I tried spawning a subagent to refactor a function that depended heavily on the conversation I'd been having with the main session about architectural decisions. The subagent had none of that context and produced a refactor that contradicted everything we'd discussed. Subagents work for independent tasks. For context-heavy work, keep it in the main session.

None of these mistakes were catastrophic. But each one cost me time I didn't need to lose. The workflow is genuinely powerful — and it's most powerful when you respect its constraints instead of trying to shortcut past them.

## The Shift in How You Think About Your Role

There's a bigger idea underneath all nine steps that took me a while to articulate.

When you implement this workflow fully, your role changes. You spend less time writing code and more time making decisions. You plan architecture instead of implementing it. You review output instead of generating it. You design systems instead of typing them.

Some developers find this uncomfortable. Coding is identity for a lot of us. The feeling of building something line by line — that flow state where your fingers and your brain are perfectly synchronized — is genuinely satisfying. Giving that up, even partially, feels like a loss.

I felt that way for the first two weeks. Then I realized that the decisions I was making — the architectural choices, the priority calls, the quality judgments — were harder and more valuable than the code I used to write. The code was execution. The decisions were strategy. And strategy is what actually determines whether software succeeds.

Maddy's framework doesn't replace engineering skill. It redirects it. Your understanding of algorithms, data structures, design patterns, and system architecture becomes more important, not less — because you're the one guiding an AI that can execute at superhuman speed but can't make strategic decisions without you.

The engineers I've seen struggle with this workflow are the ones who can't let go of the keyboard. The ones who thrive are the ones who always wished they had more time to think about architecture and less time typing boilerplate. This workflow gives you exactly that trade.

## Where This Is Heading

I don't think we're done. The gap between what Claude Code can do today and what it'll do in twelve months is probably larger than most developers expect.

The pattern I'm watching: each of these nine practices exists because of a current limitation. Plan mode exists because Claude sometimes writes correct code in the wrong place. Validation hooks exist because Claude can't always verify its own work internally. Context clearing exists because context windows have finite capacity. Subagents exist because a single context can't hold every concern simultaneously.

As context windows expand, as models get better at self-verification, as tool use becomes more sophisticated — some of these practices will become less critical. Plan mode might become unnecessary when the model's architectural reasoning is reliable enough to skip the explicit step. Validation hooks might become redundant when the model's internal verification matches external test accuracy.

But the meta-practice — building structure around AI capabilities — will only become more important. The models will get better. The engineers who know how to architect workflows around those models will extract the most value. That's the real skill this methodology teaches: not how to use Claude Code specifically, but how to think about AI-augmented development as a discipline.

Maddy's nine steps are the best starting point I've found for building that discipline. Start with `CLAUDE.md` and plan mode. Add one practice per week. In a month, you'll have a workflow that makes your current approach feel like driving with the parking brake on.

The question worth sitting with tonight isn't whether this workflow is worth implementing — the evidence is overwhelming that it is. The question is which of your current habits are so comfortable that they're preventing you from making the shift.

## Frequently Asked Questions

### How do I set up plan mode in Claude Code?

Toggle plan mode with `Shift+Tab` in the Claude Code CLI before describing your task. Claude will analyze your codebase and produce an implementation plan for review before writing any code. Use it for any change touching more than one file.

### What's the difference between slash commands and skills in Claude Code?

Slash commands are single-action prompts stored as Markdown files in `.claude/commands/`. Skills are multi-step workflows stored in `.claude/skills/` that encode complex procedures with decision trees and quality criteria. As of 2026, both use the same slash-command interface.

### How many parallel Claude Code sessions should I run?

Start with two parallel sessions using Git worktrees for isolation. Three sessions is the practical sweet spot for most developers. Running more than three typically creates review overhead that cancels out the productivity gains from parallelization.

### Do Claude Code validation hooks slow down development?

Hooks add execution time equal to your build and test duration after every edit. For projects with test suites under 30 seconds, the overhead is negligible compared to the debugging time they prevent. For longer test suites, consider running a subset of relevant tests as hooks and the full suite before commits.

### What MCP servers should I install for Claude Code?

Most developers need two to three MCP servers: GitHub (for PR and issue management), a filesystem extension, and one domain-specific server. Add them with scope configuration — `user` for global availability, `project` for team-shared tools via `.mcp.json` in your repo.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
