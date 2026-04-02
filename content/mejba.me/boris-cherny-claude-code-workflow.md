**BRAND:** mejba.me
**TITLE:** How Claude Code's Creator Actually Uses It Daily
**SLUG:** boris-cherny-claude-code-workflow
**PRIMARY KEYWORD:** Boris Cherny Claude Code workflow
**SECONDARY KEYWORDS:** Claude Code best practices, Claude Code plan mode, Claude Code productivity
**META DESCRIPTION:** Boris Cherny ships 10-30 PRs daily with Claude Code. Here's his 6-step workflow — plan mode, minimal CLAUDE.md, verification loops, and parallel sessions.
**TAGS:** AI Development, Claude Code, Developer Productivity, Workflow Optimization, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will adopt Boris Cherny's 6 core Claude Code strategies — plan mode interviews, minimal CLAUDE.md, verification loops, parallel sessions, slash command skills, and future-proof thinking — and immediately improve their own AI-assisted coding workflow.

---

Boris Cherny hasn't manually written a single line of code since November 2025.

Let that land for a second. The person who *built* Claude Code — who architected the tool that hundreds of thousands of developers now use daily — stopped typing code by hand over four months ago. And his output didn't drop. It accelerated. He ships between 10 and 30 pull requests every single day, runs up to 15 Claude Code sessions in parallel across his terminal and browser, and occasionally kicks off coding sessions from his phone before bed and reviews the finished work over morning coffee.

When I first heard this on his [Lenny's Podcast appearance](https://www.lennysnewsletter.com/p/head-of-claude-code-what-happens), my reaction was skepticism. Thirty PRs a day? From someone who doesn't write code? That sounds like a LinkedIn flex wrapped in a productivity guru costume.

Then I studied his actual workflow. And the thing that surprised me wasn't the volume — it was the simplicity. Boris's system is almost aggressively minimal. No complex prompt libraries. No elaborate scaffolding. No twenty-page instruction files. His entire CLAUDE.md configuration is roughly 100 lines. About 2,500 tokens. That's it.

The gap between my Claude Code productivity and his wasn't about some secret technique I hadn't discovered. It was about six principles I'd been ignoring, overcomplicating, or doing backward. After spending the past two weeks restructuring my own workflow around his approach, I can tell you — the results are real. My debugging time dropped by roughly half. My sessions produce usable output on the first attempt about 3x more often. And I finally stopped that maddening cycle of Claude confidently building the wrong thing while I watch, too deep in sunk-cost to interrupt.

Here's the full breakdown of what Boris Cherny actually does — and more importantly, why each piece matters more than it looks.

## "Move Slow to Move Fast" — Why 80% of Sessions Start in Plan Mode

This was the first thing that changed how I work, and honestly, it's the one I resisted longest.

I used to open Claude Code and immediately start prompting. Describe the feature. Watch the code appear. Feel productive. Except I wasn't productive — I was *busy*. There's a critical difference, and Boris nails it in a single observation: AI solves problems fast, but not necessarily the *right* problems. Without clear planning, Claude will confidently sprint in the wrong direction and build something technically impressive that completely misses the actual goal.

Boris starts roughly 80% of his sessions in plan mode. If you're not familiar, plan mode (activated by pressing Shift+Tab twice in Claude Code) tells the AI to think and analyze without writing any code or running any commands. It reads files, asks questions, proposes approaches — but touches nothing until you explicitly approve the plan.

The specific technique Boris uses is what he calls the "interview prompt." Before any code gets generated, he asks Claude something like this:

```
Interview me about this. What core problems does this solve? 
Who is this for? What does success look like? What should this 
NOT do? Summarize it back to me before writing any code.
```

That prompt changed my entire workflow. Here's why it's more powerful than it looks.

When I first tried it, Claude asked me five clarifying questions about a feature I thought I'd described clearly. Two of those questions revealed assumptions I'd made that were flat-out wrong. One of them — "Should this modify the existing user record or create a new activity log entry?" — would have led to a database architecture mistake that might have taken hours to untangle if Claude had just started building.

Boris shared a specific example that stuck with me: a client's bug fix where Claude, without planning, went straight to modifying database values instead of fixing the UI display logic. The "fix" technically resolved the reported symptom but broke three other features that depended on those database values. The kind of bug that passes a quick manual test and then detonates in production two days later.

Plan mode would have caught it in sixty seconds. The interview would have surfaced that the problem was display-only. Claude would have proposed a UI-layer fix. No database changes. No cascade of breakage.

I've been running this interview-first approach for two weeks now, and the pattern is consistent: about 4-5 minutes of planning saves 20-40 minutes of rework. The math isn't even close.

One detail that's easy to miss — plan mode is also significantly faster and cheaper per interaction. Since Claude isn't executing tools, writing files, or running commands during planning, responses come back lightning-fast and consume fewer tokens. You're essentially getting the AI's best thinking at a discount before committing to the expensive execution phase.

After Claude presents its plan, Boris reviews it, sometimes edits it directly using Ctrl+G (which opens the plan in your text editor), and only then switches to auto-accept mode and lets Claude execute. In most cases, he says, Claude one-shots the implementation after a good planning session. One shot. No iterations. No "that's close but actually I meant..." corrections.

That alone is worth the planning overhead. But the next principle is what makes the planning stick across sessions.

## The 100-Line CLAUDE.md — Why Less Context Beats More

Here's where Boris's approach directly contradicted what I'd been doing for months.

I had a CLAUDE.md file that was approaching 500 lines. Detailed architectural documentation. Coding standards with examples. Historical context about why certain decisions were made. Pattern libraries. It felt thorough. Professional. Like a proper engineering document.

It was also silently eating 30% of my available context window on every single request. Every time I asked Claude a question — even a simple one — those 500 lines got loaded first. Tokens spent before the actual work even began.

Boris's CLAUDE.md is approximately 100 lines. Around 2,500 tokens. And it outperforms every bloated instruction file I've ever built.

His philosophy is almost zen-like in its restraint: the file should contain only rules that fix recurring mistakes. Not documentation. Not architecture explainers. Not coding standards that Claude already knows from its training data. Just corrections. Guardrails. The specific things Claude gets wrong *in your project* that it wouldn't know without being told.

The process is reactive, not proactive. Boris and his team at Anthropic follow a simple rule: "Anytime we see Claude do something incorrectly, we add it to CLAUDE.md so it doesn't repeat next time." The file is checked into git, shared across the entire team, and updated multiple times per week. But things get *removed* too. As models improve, rules that were necessary six months ago become redundant. Claude learned them on its own.

This maps to something I covered in my [50 Claude Code tips guide](https://www.mejba.me/50-claude-code-tips-guide) — your CLAUDE.md is either your superpower or your bottleneck, and the dividing line is length. But Boris takes it further than I did. He doesn't just recommend keeping it short. He recommends *deleting and recreating it from scratch* when it gets bloated or contradictory, rather than trying to surgically clean it up.

The reasoning: accumulated instructions develop internal contradictions over time. Rule 7 says "always use async/await" but Rule 43 says "use callbacks for database operations." A human reading the file might catch the conflict. Claude might not — or worse, it might try to satisfy both simultaneously, producing bizarre hybrid code.

Starting fresh eliminates the contradictions, the duplicate instructions, and the rules written for model capabilities that no longer apply. It's the Marie Kondo approach to AI configuration: if a rule doesn't fix a currently-active problem, it shouldn't be in the file.

I tried this last week. Deleted my 500-line CLAUDE.md and rebuilt it from scratch with only the rules I could justify from the past two weeks of usage. Ended up with 87 lines. My token consumption dropped noticeably, and — here's the part I didn't expect — Claude's code quality actually improved. Less conflicting context meant more coherent output.

There's a structural detail worth mentioning too. Boris takes advantage of CLAUDE.md hierarchy: a root-level file for project-wide rules and directory-level files for subsystem-specific context. Your `/api` directory gets its own focused rules without inflating the root file. This is something I also use heavily, and it scales beautifully on monorepos.

If you're sitting there with a CLAUDE.md that's grown past 200 lines, I'd strongly recommend Boris's nuclear option. Delete it. Rebuild from recent pain points only. The file you end up with will be smaller and better.

## Verification Loops — The 2-3x Quality Multiplier Nobody Uses

This is the principle that, honestly, made me feel a bit foolish for not implementing sooner.

Boris's claim is specific: giving Claude the ability to verify its own work improves output quality by 2-3x. Not "a little better." Not "somewhat more reliable." Two to three times better. And after implementing his approach, I believe the number is accurate.

The concept is straightforward. Most people use Claude Code in a generate-and-review cycle: Claude writes code, you read it, you spot problems, you ask for fixes. This works, but it puts the entire quality burden on you. You're the verification layer. And you're human, which means you miss things — especially during long sessions when attention fades.

Boris's approach flips this. He gives Claude the tools and instructions to check its own output *before presenting it as done*. Two steps:

1. Give Claude a method to verify its work (a test suite, a browser preview, a linting command, a type checker)
2. Tell Claude about that method explicitly (in CLAUDE.md or in the prompt)

The practical implementation looks different depending on what you're building. For a web application, it might mean telling Claude: "After making changes, run the test suite and open the browser to visually confirm the UI matches the requirement." For an API endpoint, it might be: "After implementing, run the integration test and confirm the response shape matches the schema." For content generation, it might be: "After writing, review against the brand guidelines document in /docs/style-guide.md."

Boris goes a step further — he adds verification prompts directly to his CLAUDE.md file. Something like:

```
Before marking any task as complete, propose a verification plan.
Describe what you'll check and how you'll confirm correctness.
Then execute that plan.
```

This single rule, added to your instruction file, forces Claude to think about verification *before* it starts building. And it changes the output dramatically. Claude starts suggesting test cases you hadn't considered. It catches edge cases during implementation instead of after. It runs type checks and linters proactively rather than waiting for you to ask.

I added this rule to my own CLAUDE.md eight days ago. In that time, I've had exactly two instances where Claude submitted code with a bug I didn't catch during review. In the eight days *before* adding the rule, the number was closer to nine or ten. Small sample size, sure. But the directional shift is unmistakable.

The key insight here is that Claude doesn't lack the ability to verify — it lacks the *instruction* to verify. Left to its own defaults, Claude optimizes for speed and completion. It wants to give you an answer. Verification slows that down, so it skips it unless you explicitly build it into the workflow. Boris's genius is recognizing that one line in CLAUDE.md unlocks a capability that was always there but dormant.

This connects to something broader about how I think about [Claude Code agent skills](https://www.mejba.me/claude-skills-guide-decoded) — the best configurations don't add new capabilities to the AI. They activate capabilities the AI already has but doesn't use by default. Verification is the most impactful example of that pattern.

## Multiply Yourself — Parallel Sessions That Actually Work

Boris runs 5 Claude Code instances locally in his terminal and another 5-10 on Anthropic's website. Simultaneously. On different tasks.

When I first heard this, my reaction was "that sounds chaotic." Multiple AI sessions working on the same codebase? That's a recipe for merge conflicts, contradictory changes, and code that doesn't integrate.

Except Boris doesn't run them on the same task. Each session gets a distinct, independent piece of work. One might be building a new API endpoint. Another is writing tests for an existing feature. A third is refactoring a utility module. A fourth is investigating a bug report. They never overlap. They never touch the same files. They run in parallel because they *can* — the work is naturally partitioned.

The trick that makes this work at a practical level: each local session uses its own git checkout rather than branches or worktrees within the same repository. This eliminates filesystem conflicts entirely. Each Claude instance thinks it's working on a standalone project. No lock files fighting each other. No half-written changes in shared directories.

For remote sessions, Boris starts them with `&` from the CLI and sometimes uses `--teleport` to move sessions back and forth between his terminal and the browser interface. He'll kick off a session before bed, and it'll be waiting with a completed pull request when he checks in the morning.

I've scaled up to running three parallel sessions — my hardware and attention span aren't quite at Boris-level yet — and even at that modest scale, the productivity difference is significant. Three independent tasks that would take me 45 minutes each sequentially get done in about 50-55 minutes total. Not perfect parallelism, but close enough that it fundamentally changes how I plan my workday.

There's a subtler benefit Boris mentions that I've confirmed through experience: fresh sessions bring fresh perspectives. When you're deep in a session that's been working on a problem for 30 minutes, the context window is full of that problem's assumptions and prior failed approaches. Starting a second session to look at the same issue — but from scratch — sometimes produces a better solution in under five minutes because it doesn't carry the baggage of prior attempts.

I now do this routinely for tough bugs. If my first session hasn't solved it in 15 minutes, I open a fresh session with a clean description of the problem. The fresh session catches it about half the time. Not because it's smarter — because it's unburdened.

For anyone interested in the git worktree approach to parallel Claude sessions, I wrote about this in more detail in my [Claude Code git worktrees parallel agents guide](https://www.mejba.me/claude-code-git-worktrees-parallel-agents). The short version: worktrees give you isolated working directories from a single repository, which is slightly more efficient than full clones but achieves the same goal Boris describes.

## Systemize Inner Loops — Slash Commands as Muscle Memory

Here's where Boris's workflow shifts from "productive individual" to "production system."

Every developer has inner loops — the tasks they repeat multiple times per day. Running tests. Generating boilerplate. Creating pull requests. Formatting code reviews. Writing commit messages. Updating documentation. These tasks aren't complex individually, but they add up. If you spend 3 minutes on each one and do twenty of them per day, that's an hour of repetitive work.

Boris automates these with Claude Code slash commands and skills. Not occasionally. *Obsessively.* Every workflow he does more than twice becomes a command.

The analogy he uses is perfect: regular prompts are like telling a basketball player "dribble the ball down the court, look for an open teammate, and pass if you see one." Slash commands are specific plays — precise sequences executed the same way every time. "Pick and roll from the left wing." No ambiguity. No variation. Just execution.

In Claude Code, slash commands live in `.claude/commands/` as markdown files. Each file describes a specific workflow. When you type `/my-command`, Claude reads the file and executes that workflow. No re-explaining. No "here's what I need you to do..." preamble. Just the trigger and the result.

Boris's real power move is asking Claude itself to suggest which commands he should create:

```
Based on this project, what Claude skills should I create? 
Look at my recent git history, my CLAUDE.md, and the types 
of changes I make most frequently. Suggest 5-7 slash commands 
that would automate my most common workflows.
```

I ran this prompt on one of my projects and Claude suggested seven commands. Five of them were workflows I was doing manually every day. Two were workflows I hadn't even thought to automate because they involved multiple steps across different tools. I built all seven, and the time savings compound daily.

The key difference between a slash command and a saved prompt is durability. Prompts live in your clipboard or a notes file. They get lost, edited inconsistently, and forgotten. Slash commands live in your repository. They're version-controlled. They're shared with your team via git. When you improve a workflow, everyone gets the improvement on their next `git pull`.

Skills take this a step further — they're not just commands *you* trigger, but capabilities Claude can invoke autonomously during reasoning. A skill with the right YAML frontmatter activates automatically when Claude encounters a relevant situation. "When the user asks for a status report, use this workflow." "When generating an API endpoint, follow this template." It's the difference between having a recipe book and having a chef who already knows all the recipes.

For a deeper dive into building effective Claude Skills, my [Claude Skills guide](https://www.mejba.me/claude-skills-guide-decoded) covers the YAML structure, the five patterns that actually matter, and the mistakes I made during my first week of building them. Boris's approach confirms what I found independently: start with the workflows you repeat most, automate those first, and let complexity grow organically.

## Build for the Future — The Bitter Lesson Applied to Your Workflow

This is the principle that ties everything else together, and it's the most philosophical of Boris's six strategies. But don't skip it — it's the one that'll save you from a trap I've watched dozens of developers fall into.

Boris references Rich Sutton's famous 2019 essay, "[The Bitter Lesson](http://www.incompleteideas.net/IncssaBittrLsson.html)," which argues that in the history of AI research, general methods that leverage computation have always eventually outperformed approaches based on human-engineered, domain-specific knowledge. Every time researchers tried to hand-code intelligence — chess opening books, speech recognition rules, translation grammars — they were eventually beaten by systems that simply learned from massive amounts of data.

Boris applies this to Claude Code workflows: don't over-engineer your prompts or scaffolding. The model is getting better fast. That elaborate prompt chain you spent three days perfecting? In six months, a simple one-sentence instruction will produce better results because the model improved that much.

I've seen this firsthand. Techniques I documented in early 2025 for getting Claude to produce clean TypeScript — specific formatting instructions, example patterns, explicit type annotations in the prompt — are now unnecessary. Claude generates clean TypeScript by default. The hours I spent on those prompts were wasted effort, invested in infrastructure that model improvements made obsolete.

Boris's practical advice: focus on feeding the model quality data and context rather than optimizing prompt engineering. Your time is better spent on a well-structured codebase with clear naming conventions, comprehensive tests, and good documentation than on perfecting the seventeen-step mega-prompt that coerces Claude into writing code the way you want. Because Claude will *learn* to write code the way you want. Your job is to make sure it has the right context — not the right constraints.

This connects back to the minimal CLAUDE.md philosophy. A 500-line instruction file is over-engineering for the current model's limitations. A 100-line file focused on project-specific corrections will remain relevant even as the model improves, because it's teaching Claude things it genuinely *can't* know without being told — your team's conventions, your project's quirks, your business domain's specific requirements.

Boris frames it as a question of where to invest your marginal hour. You could spend it:

- **Option A:** Refining a prompt until Claude generates slightly better code right now
- **Option B:** Improving your codebase, your test coverage, or your CLAUDE.md with a correction that will compound over months

Option B wins every time. And it wins by a wider margin with each model update, because the better the model gets, the less prompt engineering matters and the more context quality matters.

I've restructured my own approach accordingly. When I find myself tweaking a prompt for the third time, I stop and ask: "Is this a prompt problem or a context problem?" Almost always, it's context. The codebase is ambiguous. The test suite doesn't cover the edge case. The CLAUDE.md is missing a critical project convention. Fix the context, and the prompt can stay simple.

## What Changed in My Own Workflow

After two weeks of implementing Boris's six principles, here's what shifted.

My sessions are shorter. Not because I'm doing less — because the planning phase eliminates the false starts that used to eat 30-40% of my session time. I used to spend 90 minutes on a feature with 30 minutes of that being "no, that's not what I meant, let me re-explain." Now the interview prompt catches misalignment in the first five minutes.

My CLAUDE.md is 87 lines. Down from nearly 500. And my output quality went *up*, not down. Fewer instructions, less confusion, more coherent code. I review it weekly and ask myself two questions: "Has Claude made this mistake recently?" and "Would Claude make this mistake without this rule?" If the answer to either is no, the rule gets deleted.

I run three parallel sessions daily. One for the primary feature I'm building. One for tests and documentation. One for bug investigation or refactoring. They don't interfere. They don't share context. And the combined throughput is roughly 2.5x what I was doing in serial.

Every workflow I've repeated three times is now a slash command. I have fourteen of them across my main projects. The time savings feel small individually — maybe 2-3 minutes each — but at twenty invocations per day, that's nearly an hour reclaimed. An hour I spend on the work that actually requires judgment, not execution.

And I stopped optimizing prompts. When Claude produces bad output, I fix the context: update CLAUDE.md, improve the codebase, add a missing test. I haven't written a "better prompt" in ten days. I've written better code. And the AI's output improved as a result.

## The Uncomfortable Implication

There's something in Boris's approach that's worth sitting with — something most Claude Code content creators (myself included) tend to dance around.

The six principles above are not complex. Plan before you build. Keep instructions minimal. Let the AI verify its own work. Run tasks in parallel. Automate repetitive workflows. Bet on the model improving.

None of this requires technical sophistication. A junior developer can implement all six on day one. The barrier isn't knowledge — it's ego. Planning feels slow when you want to start building. Deleting your carefully-crafted 500-line CLAUDE.md feels like throwing away work. Trusting Claude to verify its own output feels like giving up control. Running parallel sessions feels like losing the thread.

Boris's results suggest that the developers who'll thrive with AI-assisted coding aren't the ones with the cleverest prompts. They're the ones willing to change how they think about the work itself. To treat AI not as a faster typewriter but as a collaborator that needs clear direction, clean context, and the autonomy to check its own work.

The person who built the tool is telling us exactly how to use it. And his advice is not "use more features" or "write better prompts." His advice is: plan more, configure less, verify everything, and trust that the tool will keep getting better.

I'm two weeks into this approach. My output is up. My frustration is down. And for the first time since I started using Claude Code, I feel like I'm working *with* the tool instead of wrestling it into submission.

If you try one thing from this article today, make it the interview prompt. Open your next Claude Code session in plan mode and paste this before anything else:

```
Interview me about this. What core problems does this solve? 
Who is this for? What does success look like? What should this 
NOT do? Summarize it back to me before writing any code.
```

Answer Claude's questions honestly. Watch it summarize your intent back to you. Catch the misalignment *before* a single line of code exists. And then tell me that five minutes wasn't worth it.

## Frequently Asked Questions

### Who is Boris Cherny and what is his role at Anthropic?

Boris Cherny is the creator and head of Claude Code at Anthropic. He built the original terminal-based prototype and now leads the team developing it. He ships 10-30 pull requests daily without manually writing code, using his own tool for all development work. For context on the tool itself, see my [Claude Code tutorial for beginners](https://www.mejba.me/claude-code-tutorial-beginners-guide).

### How do I activate plan mode in Claude Code?

Press Shift+Tab twice in the Claude Code terminal to activate plan mode. Claude will then analyze files and propose approaches without writing code or executing commands. You can also use the `/plan` command in Claude Code v2.1.0 or later. See "Move Slow to Move Fast" above for the full workflow.

### How long should my CLAUDE.md file be?

Boris Cherny keeps his at roughly 100 lines (about 2,500 tokens). The file should contain only rules that fix recurring mistakes — not documentation, architecture explanations, or coding standards Claude already knows. Delete rules that haven't been relevant in the past two weeks. For a full breakdown, see my [50 Claude Code tips guide](https://www.mejba.me/50-claude-code-tips-guide).

### Can I run multiple Claude Code sessions at the same time?

Yes. Boris runs 5 sessions locally and 5-10 in the browser simultaneously. The key is giving each session independent work on separate git checkouts to avoid file conflicts. Start remote sessions with `&` from the CLI and use `--teleport` to move sessions between terminal and browser.

### What are Claude Code slash commands and how do I create them?

Slash commands are reusable workflow templates stored as markdown files in `.claude/commands/`. Type `/command-name` to trigger them. Create one by writing a markdown file that describes the workflow steps, then save it in the commands directory. Ask Claude: "What slash commands should I create for this project?" to get started.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
