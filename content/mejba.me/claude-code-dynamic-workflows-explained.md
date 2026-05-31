**BRAND:** mejba.me
**TITLE:** Claude Code Workflows: 41 Agents, 5M Tokens, Tested
**META TITLE:** Claude Code Workflows Explained: 41 Agents Tested (2026)
**SLUG:** claude-code-dynamic-workflows-explained
**PRIMARY KEYWORD:** Claude Code workflows
**META DESCRIPTION:** I ran a Claude Code workflow that spun up 41 agents and burned 5M tokens. Here's how dynamic workflows compare to skills, sub agents, and agent teams.
**TAGS:** Claude Code, Dynamic Workflows, AI Agents, Claude Opus 4.8, Agent Orchestration

---

Forty-one agents. That's how many Haiku instances one of my Claude Code workflows spun up last week, all at once, to audit and score every skill I'd installed. I watched the counter climb in the terminal — 12, 28, 41 — each one a full, independent Claude call grading a different recipe against criteria I'd handed the orchestrator. The whole thing chewed through roughly **5 million input tokens** before it was done.

That number stopped me. Five million. On most pricing math, that sounds like a panic-inducing bill. But here's the twist that reframed the entire feature for me: the *output* was tiny. A ranked report, a few hundred lines. All that token spend went into reading — crawling, parsing, scoring — not generating. Computationally heavy, sure. Excessively expensive? Not really. Haiku input tokens are cheap, and 41 of them reading in parallel finished in a fraction of the wall-clock time a single agent would've needed to slog through the same pile sequentially.

That's the moment Claude Code workflows clicked for me. Not as a buzzword from the Opus 4.8 launch. As a specific tool with a specific shape — one that's genuinely different from skills, sub agents, and agent teams, and one that's wildly easy to misuse if you don't understand that shape.

So that's what this post is. Not a feature announcement. A field guide. By the end you'll know exactly which of the five orchestration primitives to reach for — skill, sub agent, agent team, workflow, or the `/goal` loop — and roughly what each one costs you in tokens and complexity. I learned most of this the slightly expensive way. You don't have to.

## What Anthropic Actually Shipped With Workflows on May 28

Dynamic workflows landed on May 28, 2026, bundled into the Claude Opus 4.8 release as a research preview. If you've already read my [breakdown of Opus 4.8's effort levels](https://www.mejba.me/blog/claude-opus-4-8-effort-levels-review), think of workflows as the other half of that release — the model got a thinking dial, and Claude Code got a way to fan that thinking across hundreds of agents at once.

You need Claude Code **v2.1.154 or later** to run them. They work in the CLI, the desktop app, and the VS Code extension. And the way you trigger one is almost suspiciously casual: you just put the word *workflow* somewhere in your prompt. Say "run a workflow to audit every API route for missing auth checks" and Claude does something it has never done before — it writes a JavaScript orchestration script on the fly, hands it to a background runtime, and that runtime spins up the agents.

Two hard limits worth tattooing on the inside of your eyelids: the runtime runs up to **16 agents concurrently**, and it caps a single workflow at **1,000 agents total**. My 41-agent skill audit didn't come close to the ceiling. But it's very easy to write a prompt that does — "analyze every file in this monorepo" against a few thousand files will saturate that cap fast and keep the queue churning. We'll come back to that, because it's the single biggest way people light money on fire with this feature.

Here's the architectural detail that actually matters, though. The one that makes workflows *different* and not just "sub agents but more." Let me show you.

## The One Thing That Makes Workflows Different: The Plan Lives Outside Claude's Head

Every other orchestration tool in Claude Code keeps its plan *inside* the model's context window. The main session remembers what it delegated, tracks what came back, holds the running state in its own working memory. That's fine for a handful of tasks. It falls apart at scale, because context windows are finite and every delegated result you stuff back in eats room you need for actual reasoning.

Workflows break that rule completely. **The plan and the execution state live in an external JavaScript file**, not in Claude's context.

Read that again, because it's the whole game. When you kick off a workflow, Claude doesn't just *decide* to spawn agents — it writes a real script: loops, branching logic, how many agents to launch, what each one gets, how to combine the results, which verification passes to run. That script gets saved to a folder you specify. A separate runtime executes it in an isolated environment, completely apart from your chat session. The intermediate results — every agent's raw output, every scratch calculation — stay in the script's variables. They never touch your conversation.

What comes back to your session is only the final, combined answer.

Picture the difference physically. With sub agents, your main session is a manager holding a clipboard, personally tracking every report as it lands. With a workflow, your main session writes a *program*, hands it to a build server, walks away, and comes back to a single finished artifact. The clipboard never fills up. That's why my 41-agent audit didn't blow out the context window even though it processed 5 million tokens of input — 99% of those tokens lived and died inside the script's variables. My session only ever saw the ranked report at the end.

This has two consequences I didn't appreciate until I'd run a few. First, because the orchestration is a saved file, **workflows are rerunnable and version-controllable**. You can commit the script, diff it, hand it to a teammate. Save a useful one and it becomes a slash command — your branch-review workflow turns into `/my-review`, repeatable forever. Second, and this is critical for the mental model: **the spawned agents do not talk to each other.** Each is a fully independent Claude call with its own isolated context. They fan out, do their one job, return a summary, and that's it. No cross-chatter. No debate. The combining happens in the script's logic, not in any conversation between agents.

Hold onto that "agents don't talk" detail. It's the exact line that separates a workflow from an agent team — and getting that line wrong is how people pick the wrong tool and pay for it.

## Skill vs Sub Agent vs Agent Team vs Workflow vs /goal: The Mental Model

Right. Here's the part you came for. Five primitives, and the honest truth is most people only ever needed two of them and reached for the expensive ones out of excitement. I did. Let me walk each one the way I actually use it now, cheapest to priciest, because cost and complexity climb together in a clean ladder: **skills → sub agents → agent teams → workflows.** The `/goal` loop sits off to the side as a different axis entirely, and I'll explain why.

### What is a skill in Claude Code?

A skill is a reusable recipe that runs inside your personal Claude Code session. It's a single-agent automation — a saved set of instructions Claude can call on demand, by you or by other tools, without spinning up anything parallel.

That's the bottom rung, and it should be your default. A skill doesn't fan out. It doesn't get its own separate context. It runs right there in your session, like a function you can call by name. My SEO-check routine, my commit-message formatter, my "audit this file for N+1 queries" recipe — all skills. Cheap to run, trivial to maintain, and reusable across everything. I wrote a whole argument for [building skills before you ever reach for agents](https://www.mejba.me/blog/claude-code-agent-skills-guide), and I stand by it harder now than when I published it. The vast majority of "I need an agent for this" instincts are actually "I need a skill for this."

Use a skill when the task is small, repeatable, and self-contained. If you can describe it as a recipe, it's a skill. Done.

### What is a sub agent?

A sub agent runs parallel to your main session but does **not** share its context window, cannot talk to other sub agents, and reports its result back only to the main session.

This is the next rung up, and the key word is *offload*. A sub agent is for when you want a side task handled without it cluttering your main thread's memory. Say I'm deep in a refactor and I want the test suite explanation summarized without derailing my context — I hand it to a sub agent. It goes off, does the thing, comes back with an answer, and my main session's working memory stays clean. The trade-off it eliminates is *communication overhead*. A sub agent doesn't coordinate, doesn't negotiate, doesn't loop anyone else in. It's a one-way errand. That's a feature, not a limitation — it makes sub agents cheap and predictable.

Use a sub agent when you have a simple, independent side task and you want it out of your main context. No collaboration needed. Just "go handle this and report back."

### What is an agent team?

An agent team is a small group of agents that *communicate*, share tasks, and collaborate toward a goal, inside their own shared context window — agents debate, coordinate, and build on each other's work.

Now we're at the expensive rung, and the distinguishing word is *talk*. Unlike sub agents, the members of an agent team can see each other and trade information. They share context. One agent's finding informs another's. They argue, they hand off, they converge. I dug into exactly [how and when agents should talk to each other](https://www.mejba.me/blog/claude-agent-teams-guide) in a dedicated post, and the short version is: that conversation is the whole point, and it's also why teams cost real money. Shared context plus back-and-forth means more tokens, more rounds, more compute.

Use an agent team when the task is genuinely collaborative — when the *discussion* between agents produces something no single agent could, and when context-sharing between them is vital. Architecture debates. Multi-perspective reviews where one agent's critique sharpens another's proposal. Not for throughput. For *deliberation*.

### What is a workflow?

A workflow is a JavaScript-orchestrated system that spins up many independent agents — possibly hundreds — running in parallel on different pieces of a task, then combines their results in script logic. The agents don't communicate; the plan lives in an external file, not Claude's context.

Top rung. Most powerful, most complex, most expensive. Everything I described two sections ago. The defining trait, the thing that separates it from an agent team: **width without conversation.** A team is a few agents *talking*. A workflow is many agents *not* talking — each grinding on its own slice, results merged by code. My 41 Haiku scorers were a textbook workflow: 41 independent jobs, zero cross-talk, one combined ranking at the end.

Use a workflow when a task naturally shatters into many independent, parallelizable pieces. Crawling an entire codebase. Scoring a large dataset. Broad research across dozens of angles. The kind of job where the pieces *don't need to know about each other* — they just need to all get done, fast, and rolled up.

### What does the /goal command do?

`/goal` runs a looped process where an agent keeps iterating on the same problem until a completion condition is met — it may run many cycles and take a long time.

Here's why I said `/goal` sits on a different axis. Everything above is about *how many agents and whether they talk*. `/goal` is about *how many times one effort iterates*. It's a loop. You hand it a target and a definition of done, and it grinds — try, evaluate, refine, try again — until the condition is satisfied. It might run a dozen cycles. It might run for a long while. That's expected.

Use `/goal` when the task needs **depth** — iterative refinement toward a hard target — rather than breadth.

And that word, depth, is the key to the whole map. Let me make it concrete.

## Width vs Depth: The Frame That Finally Made This Stick

Here's the single sentence that reorganized how I think about all of this:

**Workflows are width. `/goal` is depth.**

A workflow spreads *out* — many agents, each handling a different slice, all at once. Width. You use it when the work is wide: a hundred files to scan, fifty claims to verify, a big flat pile of independent tasks. The win is parallelism. You're trading tokens for wall-clock time and getting a broad job done fast.

The `/goal` loop drills *down* — one effort, refined over and over, until it's right. Depth. You use it when the work is deep: a single thorny problem that needs to be hammered through cycle after cycle until it passes a bar. The win is persistence. You're trading time for quality on one hard thing.

Once I had that frame, picking the tool stopped being guesswork. Wide and shallow? Workflow. Narrow and deep? `/goal`. Need both — a broad job where each piece also needs iterative refinement? That's where you carefully combine them, and I'll be honest about how that goes in a minute, because it's powerful and it's a great way to burn a fortune.

This width-versus-depth lens also explains the two headline features Anthropic shipped on top of workflows. Both are workflows under the hood, aimed at the two ends of that spectrum.

## Ultra Code and /deep-research: Workflows With the Gloves Off

Two things sit on top of the raw workflow engine, and you should know both exist before you decide whether you need them.

**Ultra Code** (`/effort ultracode`) is the maximum setting: highest reasoning effort *plus* automatic workflow orchestration. Flip it on and Claude decides, for every substantial task in the session, whether to plan a workflow for it. A single request can fan into several workflows in a row — one to understand the code, one to make the change, one to verify it. It is the most capable mode Claude Code has. It is also, unsurprisingly, the most expensive thing you can run. Highest effort burns the most thinking tokens, and wrapping that in automatic orchestration multiplies the agent count. I reach for ultracode when I'm doing something genuinely hard and genuinely worth it. I do not leave it on by default. That's how you get a surprising bill.

**`/deep-research`** is the built-in workflow aimed at the *research* shape. Ask it a question and it fans web searches out across multiple angles, fetches and cross-checks the sources, has agents vote on competing claims, and synthesizes a single cited report. It's a workflow purpose-built for breadth of investigation — width applied to knowledge instead of code. If you've used the various deep-research tools floating around, this is that pattern, native to Claude Code, riding the same orchestration engine as my 41-agent audit.

You manage all of it with one command: **`/workflows`**. Run it any time to see what's running, what's finished, and to open a progress view — or to stop a workflow that's clearly going off the rails. I've hit that stop button. More than once. Which brings me to the part of this post I most want you to read.

## What I Got Wrong: The Token Mistakes Nobody Warns You About

I'll be straight with you — my first instinct with workflows was to throw them at everything, and that was a mistake that cost me tokens and taught me the real rules.

**Mistake one: I used a workflow for a job that wasn't wide.** Early on I fired a workflow at a task that was really just three sequential steps on one file. Spinning up the orchestration, writing the script, launching agents — all that overhead, for something a single skill would've handled in a quarter of the tokens. Workflows are overkill for small or simple jobs, full stop. The orchestration *costs* something even before the agents run. If the task doesn't genuinely break into many independent pieces, you're paying the setup tax for nothing.

**Mistake two: I was vague, and a workflow took me literally.** I asked one to "review the codebase for issues." No scope, no deliverable, no boundaries. It happily fanned out across far more files than I cared about, each agent a full Claude call, the input-token meter spinning like a slot machine. This is *the* failure mode. Workflows can burn input tokens absurdly fast on broad jobs precisely because they're designed to crawl wide. A workflow does exactly what you said — and at the scale of hundreds of parallel agents, "exactly what you said" includes every loose interpretation of a sloppy prompt.

The fix for both is the same, and it's boring, and it works: **be explicit and specific.** Define the deliverable. Bound the scope. "Audit the 14 files in `app/Http/Controllers` for missing authorization middleware and return a table of file, route, and the missing check" gives the orchestrator a wall to stop at. "Review the code" gives it a continent.

Here's the rule I now actually live by. A workflow is the right tool only when *all* of these are true: the task is large, the pieces are independent, and those pieces are parallelizable. Miss any one of those and you've picked the wrong primitive. Large but sequential? Use `/goal`. Small but repeated? Use a skill. Collaborative and discussion-driven? Use an [agent team](https://www.mejba.me/blog/claude-agent-teams-guide) instead. The orchestration choices follow the same task-shaped logic I worked through in my [agent swarm architecture breakdown](https://www.mejba.me/blog/claude-code-agent-swarm-architecture) — match the structure to the work, not to your enthusiasm.

If you'd rather not learn this calculus by setting tokens on fire on a live client repo, this is exactly the kind of orchestration setup I build and tune for teams — you can [see what I take on at my Fiverr](https://www.fiverr.com/s/EgxYmWD). Getting the primitive selection right the first time is most of the value.

## The Trick That Changes the Economics: Nest Skills Inside Workflows

Here's the move that made workflows feel less like a money pit and more like leverage. **You can nest skills inside a workflow.** Each of the many agents a workflow spawns can call your existing reusable recipes.

Think about what that does. You spend the effort once to write a tight, well-tested skill — say, a precise "score this skill file against these ten criteria" recipe. Then a workflow spins up 41 agents and *each one runs that same skill* against a different target. You get the parallelism of a workflow with the consistency and maintainability of a skill. The expensive, complex layer leans on the cheap, simple layer. That's the architecture I landed on for the audit that opened this post, and it's why the output was so clean — every one of those 41 agents was grading by the identical rubric, because they were all running the identical skill.

This is the part of the cost-complexity ladder people miss. The rungs aren't mutually exclusive. The smart pattern is the cheapest tool doing the actual work, wrapped in the expensive tool only where you genuinely need the scale. Workflows on top, skills underneath. You're not choosing between them — you're stacking them.

You *can* go further and combine a workflow with `/goal` — width and depth together, many parallel agents each iterating to a target. It's the most powerful orchestration I've run. It is also the most expensive thing in this entire post, by a wide margin, and I treat it like a power tool with no guard. Worth it for a genuinely big, genuinely hard job. A great way to vaporize tokens on anything less.

A quick aside that has nothing to do with workflows: if all of this sounds like more orchestration than your actual problem needs — say you just want to ship an AI app or website with some MCP connections, not coordinate 41 agents — [Lovable](https://lovable.dev) is a far simpler on-ramp. It wires up MCP servers and gives you a building experience that doesn't require any of this. It's a different tool for a different altitude. The whole point of this post is matching the tool to the task, so I'd be a hypocrite not to mention it. Now back to the agents.

## What This Actually Costs You — And How to Know It's Working

Let me ground the economics, because "5 million tokens" with no context is either terrifying or meaningless depending on what you assume.

The number that matters isn't total tokens — it's *which* tokens. My 41-agent audit was almost entirely **input** tokens, and I ran the scorers on Haiku. On Anthropic's published pricing, Haiku input runs a fraction of a cent per thousand tokens, so 5 million input tokens of cheap-model reading is a fundamentally different bill than 5 million output tokens of Opus generation. The lesson generalizes: **a workflow's cost is dominated by how much its agents read, times the price of the model they read with.** Pick the model deliberately. Cheap models for wide, shallow crawling. Expensive models only for the pieces that need real reasoning.

How do you know a workflow is the right call before you run it? Run this gut-check. Count the independent pieces. If the task splits into roughly ten or more chunks that genuinely don't depend on each other, parallelism will pay for the orchestration overhead — that's your green light. Fewer than that, or the pieces depend on each other, and a simpler primitive almost certainly wins on cost.

And once it's running, watch two things in `/workflows`: the agent count and the wall-clock time. If the agent count is climbing toward territory you didn't intend — that monorepo-scale fan-out — stop it and tighten your scope. If a workflow is taking far longer than the equivalent sequential job would, the task probably wasn't parallelizable in the first place and you've picked wrong. The whole promise of width is *faster wall-clock through parallelism*. If you're not getting that, the shape was wrong.

The realistic payoff, when the shape *is* right: jobs that would've taken a single agent an hour of sequential grinding finish in minutes, because the work was wide and you let it spread. That's the entire value proposition. Not magic. Just parallelism, correctly applied, with the plan held safely outside the model's head.

## The One Decision That Makes All of This Easy

Go back to that terminal counter climbing past 41. The reason that run felt good instead of reckless wasn't the technology. It was that I'd matched the tool to the shape of the work: a wide, flat pile of independent scoring jobs, each running an identical skill, results merged in code, output tiny. Right primitive, right model, bounded scope. Everything downstream of that one decision was easy.

That's the whole skill here, and it's not really about Claude Code at all. It's about looking at a task and asking one question before you touch a single command: *is this wide or deep, collaborative or independent, large or small?* Answer that honestly and the tool picks itself. Skill for the small and repeated. Sub agent for the simple side errand. Agent team for the genuine debate. Workflow for the wide independent crawl. `/goal` for the deep iterative grind. The expensive tools wrapped around the cheap ones, never the reverse.

So before your next big job — the codebase audit, the broad research, the dataset you've been dreading — stop and name its shape out loud. Wide or deep? That one word will save you more tokens than any setting in the app. What's the widest task on your plate right now that you've been doing one file at a time?

## Frequently Asked Questions

### What are Claude Code dynamic workflows?

Claude Code dynamic workflows are a feature, launched May 28, 2026, that lets Claude write a JavaScript orchestration script and run many independent agents in parallel on different pieces of a task. The plan lives in an external file, not Claude's context window, and agents don't communicate — results are combined in script logic. You trigger one by including the word "workflow" in your prompt (requires Claude Code v2.1.154+).

### How are workflows different from sub agents and agent teams?

Workflows spawn many non-communicating agents in parallel with the plan held in an external script, while sub agents run isolated side tasks that report only to the main session, and agent teams are a small group that *do* talk and share context to collaborate. The clean rule: sub agents offload, teams deliberate, workflows fan out wide. For the deeper distinction on when agents should communicate, see my [agent teams guide](https://www.mejba.me/blog/claude-agent-teams-guide).

### When should I use a workflow versus the /goal command?

Use a workflow for width — many independent, parallelizable pieces like crawling a codebase or scoring a dataset — and use `/goal` for depth, where one effort iterates in a loop until a target is met. Workflows spread out; `/goal` drills down. If the task is wide and shallow, workflow. If it's narrow and deep, `/goal`.

### How much do Claude Code workflows cost in tokens?

A workflow's cost is dominated by how much its agents read multiplied by the price of the model they use, so the same 5-million-token run is cheap on Haiku input and expensive on Opus output. Costs spike when prompts are vague or scope is unbounded, because each of up to 1,000 agents is a full Claude call. Bound the scope and pick cheap models for wide, shallow crawling. For the model side of this, see my [Opus 4.8 effort levels review](https://www.mejba.me/blog/claude-opus-4-8-effort-levels-review).

### What is Ultra Code mode in Claude Code?

Ultra Code (`/effort ultracode`) combines the highest reasoning effort with automatic workflow orchestration, letting Claude decide when each substantial task warrants spinning up a workflow. It's the most capable mode in Claude Code and the most expensive — a single request can fan into several workflows in a row. Use it for genuinely hard, high-value work, not as a default.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
