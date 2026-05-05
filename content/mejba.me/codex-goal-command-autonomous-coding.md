**BRAND:** mejba.me
**TITLE:** Codex /goal Command: My Honest Take on Autonomous Coding
**META TITLE:** Codex /goal Command: Autonomous Coding I Tested
**SLUG:** codex-goal-command-autonomous-coding
**PRIMARY KEYWORD:** Codex /goal command
**META DESCRIPTION:** I tested OpenAI's Codex /goal command on real exploratory work. Here's how it actually behaves, when to use it, and the trap most people fall into.
**TAGS:** OpenAI Codex, AI Agents, AI Coding Workflows, Autonomous Agents, Codex CLI

---

# Codex /goal Command: My Honest Take on Autonomous Coding

The first time I gave Codex a `/goal` and walked away from my desk, I came back forty minutes later to find it had rewritten the same function eleven times. Eleven. Each version a little different. None of them objectively better than the one before. The agent was looping — not in the productive Ralph-loop sense, but in the "I have no idea when to stop" sense. My goal had been "make the rendering faster." That was the entire prompt. That was the whole problem.

I had given a long-running autonomous agent a vague target and then acted surprised when it wandered.

The Codex `/goal` command landed in version 0.128.0 at the end of April, and it changes the shape of what a coding agent is supposed to do. Most slash commands are reactive — you ask, it answers, you ask again. `/goal` is the opposite. You set an objective once, and Codex keeps looping through plan, act, test, evaluate, and repeat, until either the goal is verifiably done or you tell it to stop. It's the closest thing to "fire and forget" autonomous coding I've used that doesn't feel completely unhinged.

But "doesn't feel completely unhinged" is doing a lot of work in that sentence. Because the difference between a `/goal` run that ships a 25% performance win and one that produces eleven versions of the same broken function isn't the model. It isn't the prompt either. It's something more subtle — and once you see it, you can't unsee it.

I've spent the last two weeks living inside this command. Here's what I learned, what I got wrong, and the framework I now use to decide whether a piece of work belongs in a `/goal` run or a normal pull request.

---

## What the Codex /goal Command Actually Is

Let me strip the marketing language off this thing first, because the OpenAI changelog is — as always — extremely terse about what shipped.

The Codex `/goal` command is a persistent objective mode for the Codex CLI. You give it a target. It doesn't return control after one response. It maps your repo, plans, edits files, runs tests, evaluates the result against your stopping criteria, and either declares the goal complete or starts another iteration. The agent stays attached to the thread across many tool calls. It survives context limits because of how Codex compacts and reuses state. It logs progress. It respects approval rules.

This is not a chat. It's a worker process with a checklist.

It shipped as an experimental feature, which is OpenAI-speak for "this is real but we're not putting it on the homepage yet." You enable it manually in your config file, run Codex in your terminal, and a small set of new slash commands appear in the TUI.

Here's the actual command surface as of Codex CLI 0.128.0:

- `/goal <objective>` — set a long-running goal and start the loop
- `/goal pause` — finish the current step, then pause
- `/goal resume` — resume a paused goal
- `/goal clear` — clear the active goal entirely
- `/goal` (no arguments) — show progress, token usage, and elapsed time
- `/side <prompt>` — open a side thread to ask a question without disrupting the main goal; toggle back with the escape key

The `/side` command is the part nobody talks about, and it's secretly the best feature in the bundle. More on that later.

Now, before I get into how to use any of this — there's one thing in the source video I watched that's wrong, and you'll save yourself a frustrating afternoon if you know it now.

---

## The One Config Detail Everyone Gets Wrong

The walkthrough I originally followed told me to enable goals in `config.yml`. I spent a confused twenty minutes wondering why nothing was happening before I checked the actual Codex documentation.

Codex CLI doesn't use YAML. It uses TOML.

The file is `~/.codex/config.toml`, and the flag lives under a `[features]` table. The minimal enable block looks like this:

```toml
[features]
goals = true
```

You can also do it from the command line — `codex features enable goals` writes the same value to the same file. Either way, save the file, restart Codex, and the `/goal` and `/side` commands appear in the slash-command palette. If they don't, you've either edited the wrong file or you're on a Codex version older than 0.128.0. Run `codex --version` to confirm.

Once `features.goals = true` is set globally, the feature works in both the Codex CLI and the Codex app. You only need to enable it once, in the CLI, and it propagates.

Small detail. Big difference between "this works" and "I am wasting an hour."

With that out of the way, let's talk about what actually matters — which is when you should reach for this command in the first place, because the answer is "way less often than you'd think."

---

## The Two Kinds of Coding Work — And Why /goal Is Wrong for One of Them

Here's the mental model that took me a week of misuse to land on.

Almost every piece of coding work I do falls into one of two categories. The categories look similar from the outside. A senior engineer can usually tell them apart in about thirty seconds. A coding agent — and frankly, a lot of junior engineers — cannot tell them apart at all. And `/goal` is only the right tool for one of them.

**Category one: well-defined work.** You know the input. You know the output. You know roughly what the diff looks like before you start. "Integrate the Notion API so we can sync client briefs into a project's database." "Add a Stripe webhook handler that logs to our existing event table." "Migrate the user model from email-based to UUID-based primary keys." These tasks have a shape. There are a finite number of files that need to change, a clear definition of done, and the path is mostly mechanical once you've thought about it for ten minutes.

For this kind of work, `/goal` is overkill bordering on harmful. You don't want a self-evaluating loop. You want a clean PR. You want the agent to do exactly what you asked, return control, and let you review. The standard Codex workflow — single prompt, single response, normal review — handles this beautifully. I write about how I run that loop in my [breakdown of the Codex and Claude Code two-agent workflow](https://www.mejba.me/blog/claude-code-codex-two-agent-workflow), and 90% of my real work still lives there.

**Category two: exploratory work.** This is where the goal is known but the path isn't. "Cut the P95 latency on this API by 20%." "Reduce our memory footprint on the worker pool." "Find and fix the layout shift causing our Lighthouse score to crater on mobile." You can describe the desired outcome with a metric. You cannot, however, describe the diff in advance. The solution might be a config change, a query optimization, a code refactor, an algorithm swap — or some combination of all four discovered in sequence after running profilers.

This is what `/goal` was built for. The reasoning loop — plan, act, test, evaluate, iterate — is exactly the right shape for exploration. You set a verifiable target and let the agent grind. It tries something. The tests tell it whether the change moved the metric. If yes, it tries to push further. If no, it backs out and tries a different angle.

The 25% FPS improvement story you'll see passed around in Codex demos? That's category two. Someone gave Codex a render performance problem with a clear measurable target and let it run for hours. They didn't know which optimization would land before they started. The agent figured out what to try and what to keep.

The deeper insight here, which I had to learn the hard way: most engineers default to category-one thinking even when they're working on category-two problems. They try to specify the solution before they've understood the search space. `/goal` forces you to flip that — you have to commit to the destination without committing to the route. That's uncomfortable. It's also where the leverage is.

But it only works if your destination is real. Which brings us to the discipline that makes or breaks every single goal run.

---

## Goal Verifiability Is the Whole Game

Watch what happens when you give Codex a vague goal.

I tried `/goal make the search faster` on a small Laravel side project. Codex started by running the existing search, getting a baseline (good). Then it added an index on the most-queried column (good). Then it benchmarked again and found a 14% improvement (good). Then it kept going. It added query result caching. Then it refactored the search controller. Then it added a Redis layer. Then it suggested a full-text search migration to Meilisearch. At no point did it stop, because at no point had I told it what "fast enough" meant.

Codex injects a system instruction at the start of every iteration that says, in essence, "treat uncertainty as not achieved." It's a guardrail against false claims of completion. But it cuts both ways. If your goal is genuinely unverifiable — if there's no metric, no checklist, no test that can return a clean pass — then the agent will treat the goal as perpetually not achieved. It will keep iterating until you run out of tokens, patience, or money.

The fix is simple to describe and surprisingly hard to execute. Every `/goal` you set must contain two things:

1. **A concrete target.** Either a metric ("P95 under 250ms"), a passing test ("all e2e tests in `tests/checkout/` green"), or a verifiable checklist ("dashboard sidebar shows the Filament admin link, automated tests pass, no new console errors in the build log").
2. **A definition of done that the agent can check itself.** No "until it looks good." No "until performance is acceptable." The agent has to be able to run a command, observe a result, and decide based on that observation alone.

Compare these two prompts. Same intent. Wildly different behavior:

- *Bad:* `/goal speed up the dashboard`
- *Good:* `/goal Reduce dashboard initial paint from current 2.4s baseline to under 1.0s on the staging environment. Success criteria: Lighthouse performance score above 90 on /dashboard, all existing e2e tests in tests/dashboard/ continue to pass, no new TypeScript errors in the build.`

The first one is a wish. The second one is a contract. Codex can fulfill contracts. It cannot fulfill wishes — and worse, it will pretend to try.

When I'm not sure how to write the contract, I do something the OpenAI Codex docs explicitly recommend and that I now refuse to skip: I brainstorm the goal *with* Codex first, in normal chat mode, before I run `/goal`. I describe the problem. I ask Codex what verifiable success criteria it would propose. I argue with it. I tighten the criteria. Then, and only then, do I open a clean thread and run `/goal` with the refined contract. That brainstorming step is maybe ten minutes of work and it's saved me hours of wasted token spend.

---

## What Codex Actually Does Once the Goal Starts

Let me walk through the loop, because the mechanics matter.

When you type `/goal <objective>`, Codex does something that looks unremarkable but is actually the foundation of everything that follows: it maps the repository. It reads file structures, key configs, package manifests. It sketches a model of what your project is and how it's wired. This isn't free — it costs tokens — but it's the difference between an agent that writes plausible-looking code and one that writes code that actually fits your codebase. I dig into why this kind of upfront context loading matters in [my piece on context engineering](https://www.mejba.me/blog/ai-agent-context-beats-configuration), and `/goal` is one of the clearest expressions of that principle in any current AI tool.

Then it plans. Codex sketches a sequence of steps it thinks will move toward the goal. It picks the first step. It runs it. The "running" can be anything — editing files, executing shell commands, running tests, hitting an API, reading logs. Then it evaluates. Did the step move the metric? Did it pass the tests? Did it satisfy a checklist item?

If yes, it picks the next step. If no, it tries a variant or backs out and tries a different angle. The loop continues.

You can watch this happen in real time, and it's genuinely hypnotic. There's a rhythm to it. Read, write, test, observe, decide. The agent isn't waiting for you. It's just working.

While it works, you can issue commands. `/goal pause` finishes the current step and stops the loop cleanly — no half-applied edits, no orphaned tool calls. `/goal resume` picks up where it left off. `/goal` with no arguments shows you a progress summary, token spend, and elapsed time without interrupting anything.

And then there's `/side`.

`/side <prompt>` opens an ephemeral side thread that doesn't disrupt the main goal. The main loop keeps running. You can ask Codex a question — "wait, what's the difference between a debounced and throttled scroll handler again?" — and get an answer in a separate context, then escape back to the main thread to keep watching the goal run. This sounds minor. It's not. Before `/side`, every interruption broke the agent's flow. With `/side`, you can sanity-check decisions, look up unrelated info, or even kick off a small clarifying experiment, all without poisoning the main goal's context.

This single feature is what made me start trusting `/goal` for longer runs. The ability to ask without interrupting changes the relationship from "I have to commit fully and hope" to "I can supervise without sabotaging."

---

## The Compaction Problem Nobody Sees Coming

Here's where things get technical, and where the difference between a productive `/goal` run and a doomed one lives.

Long-running agents accumulate context. Every tool call, every file read, every test result — it all piles up in the conversation history. Eventually you hit the model's context window, and something has to give. Codex handles this with prompt compaction. It summarizes earlier turns into a tighter brief, then continues from there.

Compaction sounds simple. It is not.

Good compaction preserves what matters: the goal, the current state of the work, the things that worked, the things that failed and why, the constraints, the user's preferences. After a good compaction, the agent picks up where it left off and the work feels continuous. Bad compaction strips out hard-won context — the specific reason an earlier approach failed, the configuration choice that made a benchmark valid, the tradeoff the user explicitly chose. After a bad compaction, the agent re-tries failed approaches, re-asks settled questions, and slowly drifts away from the original intent.

I watched this happen on a real project. Codex was running a `/goal` to optimize a database query pipeline. Around the third compaction, it lost the fact that I had explicitly opted out of denormalization. It re-suggested denormalization. I caught it because I was watching, but if I'd been AFK, the agent would have spent an hour going down a path I'd already closed off.

There's good research from the community on how Codex compaction actually works under the hood — [Simon Zhou's investigation](https://simzhou.com/en/posts/2026/how-codex-compacts-context/) and [the broader compaction research roundup](https://gist.github.com/badlogic/cd2ef65b0697c4dbe2d13fbecb0a0a5f) are both worth your time if you want the engine-room details. The short version: compaction quality is a function of how the agent harness summarizes, what it preserves, and what it discards. Codex is reasonably good at this but not perfect, and longer goals stress it more.

The practical implication for `/goal` users is small but important: write your goal definition in the kind of language that survives compaction. Use specific numbers. Use named files and functions. State constraints explicitly with "do not" rather than "prefer not to." Anything you say once and assume the agent will remember is at risk. Anything you bake into the goal definition itself gets re-injected at every iteration boundary, which means it survives every compaction.

I now write my goal definitions like I'm writing a contract that has to be read aloud at the start of every meeting, because that's effectively what's happening.

---

## Environment Matters More Than Model Choice

This is the part of `/goal` that surprised me most.

I assumed the difference between a great run and a mediocre one would come down to which model I picked. It didn't. The single biggest variable in `/goal` quality, by a wide margin, is the *environment* the agent gets to operate in.

A `/goal` run is only as good as the signals available to the agent. If the goal is "reduce P95 latency 20%" and the agent has no way to measure P95 latency, the goal is unverifiable in practice no matter how cleanly you wrote it. The agent will guess. It will optimize what it can see, hope that correlates with what it can't, and produce changes that may or may not move the actual metric.

Rich environments produce great `/goal` runs. Rich means:

- **Real logs.** Application logs, structured and queryable, ideally from a staging environment that mirrors production behavior.
- **A staging or test cluster.** Distributed if the goal involves anything network-related. The agent needs to be able to make a change, deploy it, and observe.
- **Cost and performance metrics.** Live, queryable, with clear baselines. If the agent can't pull current numbers, it can't decide whether it's done.
- **Flame graphs and profilers.** For performance work, this is non-negotiable. The agent isn't going to find a hot path by reading source code alone.
- **Full codebase access with permission to modify, run tests, and inspect git state.** A `/goal` run that has to ask permission for every command will burn its time on approval prompts rather than progress.

For high-risk or resource-intensive goals — anything that's going to chew CPU for hours, hammer a database, or run a long benchmark suite — I do not run them on my local machine. I spin up a cloud VPS, clone the repo there, run Codex from inside an SSH session, and let it work. This isn't just about resource cost. It's about not having my laptop fan screaming for six hours while a benchmark loop runs. It's about isolating the environment so that "the agent broke something" stays contained.

If you've been holding off on cloud-based agent execution, this is the use case that finally justifies setting it up properly. I cover the broader pattern in [my long-running agent harness writeup](https://www.mejba.me/blog/anthropic-long-running-agent-harness), and `/goal` slots into that pattern as cleanly as anything I've seen.

---

## The Scrappy Branch Trap — And the PRD Loop That Fixes It

Here's the most counterintuitive thing I've learned about `/goal` runs, and it's the lesson I wish someone had handed me before I started.

The output of a successful `/goal` run is almost never code you should ship.

That sentence will sound wrong if you haven't lived it. Let me explain.

A `/goal` run is, by design, exploratory. The agent is hunting for a solution it doesn't know in advance. The path it takes will include dead ends, debug print statements, hardcoded values used for testing, comments like `// TODO: revisit this hack`, and shortcuts that worked locally but won't survive code review. The agent isn't being lazy. It's doing exactly what exploratory work looks like when a human does it — get something working, validate the approach, then clean up. Except `/goal` usually runs out of time, tokens, or your patience before the cleanup phase.

What you end up with is what I now call a "scrappy branch." It works. It moves the metric. It satisfies the goal definition. It is also, frequently, an absolute mess.

The first few times I ran `/goal`, I tried to merge these branches directly. Always a mistake. I'd find debug prints in production code two weeks later. I'd find a hack that depended on a specific file existing only on the agent's working directory. I'd find approaches that solved the immediate goal but introduced subtle technical debt.

The fix is a workflow, not a config flag. Once a `/goal` run completes successfully, I treat the result as a proof of concept, not a deliverable. I read through the diff carefully. I extract the *insight* — the actual technique that solved the problem. Then I write a short PRD describing what was learned: the approach, the tradeoffs, the constraints, the parts that worked and the parts that need cleanup.

Then I throw the scrappy branch away.

I open a fresh thread, give Codex the PRD as a clean specification, and run a normal task — category one work, by my earlier definition — to implement the same insight against a clean codebase. The result is dramatically better. No debug prints. No hacks. No accumulated scar tissue from the exploration phase. Just a clean implementation of an approach that's now proven to work.

This two-phase pattern — exploration via `/goal`, then implementation via normal workflow — is the highest-leverage AI coding pattern I've found this year. It mirrors how good human engineers actually work on hard problems. You explore. You learn. You throw the spike away. You build the real thing.

A few engineers in the Ralph-loop community have been writing about [PRD-driven autonomous coding](https://gist.github.com/prateek/14fae59c71921710a3e055d74f30c8af) along similar lines, and the convergence is not a coincidence. The pattern works because it separates two kinds of cognition the agent shouldn't be doing simultaneously: figuring out what to build, and building it well.

---

## My Decision Framework for /goal vs Normal Workflow

After two weeks of testing, here's the decision tree I now use before reaching for `/goal`:

**Question 1: Can you describe the diff before you start?**
- If yes → standard workflow. Write a focused prompt, get a focused response, review the PR. `/goal` adds nothing here.
- If no → continue to question 2.

**Question 2: Can you describe the goal as a verifiable, measurable outcome?**
- If yes → `/goal` is on the table. Continue.
- If no → you're not ready. Brainstorm the goal in normal mode until you can write a clean contract.

**Question 3: Does the agent have access to the signals it needs to verify?**
- If yes → `/goal` is the right tool. Run it.
- If no → fix the environment first. Add metrics. Add a staging cluster. Add real logs. Then re-evaluate.

**Question 4: After the run finishes — are you treating the output as a deliverable or a spike?**
- Treat it as a spike. Always. Distill the insight into a PRD. Run a clean implementation pass against the refined spec. Ship that.

That's it. That's the framework. It's saved me real money in tokens and real hours in cleanup work, and it's the lens through which I now read every `/goal` demo on Twitter.

If somebody tells you their `/goal` run shipped code straight to main, I'd ask very politely how their bug rate looks in two weeks. Because I've been there. The first run is intoxicating. The cleanup is where the lesson lives.

---

## Where I Think This Goes Next

`/goal` is experimental. It's going to evolve fast. A few things I'm watching:

The boundary between `/goal` exploration and structured task execution is going to blur. The PRD-loop pattern I described above will probably get baked into the tool itself — distill, refactor, implement — rather than living as a manual workflow on top. Once that happens, the gap between "I had an idea" and "I have a clean PR" collapses dramatically.

The environment problem is going to get solved with better defaults. Right now, getting a `/goal` run rich enough signals to verify its own work is a setup-heavy exercise. Most projects don't have it. The next wave of agent tooling will ship with managed staging environments, observability built-in, and goal templates for common optimization targets. We're not there yet. We will be soon.

The compaction quality differences between AI coding tools are going to become a major selection factor. Right now, most engineers pick a coding agent based on the model. In a year, they're going to pick based on how the harness manages context across hours-long autonomous runs. Codex, Claude Code, Anthropic's managed agents — the model isn't the moat. The harness is.

If you want to keep tracking this space, I write about new AI coding tools every week, and the [Codex / Claude Code coverage is where most of the practical lessons land](https://www.mejba.me/blog/codex-vs-claude-code-subscription).

---

## Frequently Asked Questions

### How do I enable the Codex /goal command?
Add `[features]` followed by `goals = true` to your `~/.codex/config.toml` file, save, and restart the Codex CLI. Alternatively, run `codex features enable goals`, which writes the same value automatically. Verify by running `codex --version` and confirming you're on 0.128.0 or later — `/goal` and `/side` will appear in the slash-command palette once enabled.

### Is the Codex /goal command safe to run unattended?
It's safer than running an arbitrary Ralph loop, but I would not let it run completely unattended on production-adjacent code. Use a cloud VPS or isolated environment, set a token budget, and review the resulting branch carefully. Treat the output as a spike, not a deliverable — see the scrappy branch section above.

### When should I use /goal vs a normal Codex prompt?
Use `/goal` only for exploratory work where you know the destination but not the route — performance optimizations, latency reductions, finding-the-bug investigations. Use normal prompts for any task where you can describe the diff before you start. Most real coding work falls into the second category.

### What's the difference between /goal and /side in Codex CLI?
`/goal` sets a persistent objective and starts a long-running autonomous loop. `/side` opens a temporary side conversation that does not disrupt an active goal — you can ask questions, look up info, or run small experiments while the main goal keeps running. Toggle back to the main thread with the escape key.

### Why does my /goal run never finish?
Almost always because the goal isn't verifiable. Codex injects "treat uncertainty as not achieved" into every iteration, so a vague goal like "make it faster" loops indefinitely. Rewrite the goal as a concrete contract — specific metric, named tests, explicit checklist — and the loop terminates correctly when the criteria are met.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
