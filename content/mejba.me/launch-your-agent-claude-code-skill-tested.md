**BRAND:** mejba.me
**TITLE:** Launch Your Agent: I Tested Anthropic's Free Skill
**META TITLE:** Launch Your Agent: Anthropic's Free Claude Code Skill
**SLUG:** launch-your-agent-claude-code-skill-tested
**PRIMARY KEYWORD:** Launch Your Agent skill
**META DESCRIPTION:** I installed Anthropic's free Launch Your Agent skill for Claude Code, shipped a live cloud agent, and hit a $12 wall. Here's the honest hands-on review.
**TAGS:** Claude Code, AI Agents, Anthropic, Automation, Tool Review

---

The agent had been running for nineteen minutes and I was watching my API spend climb in real time like a taxi meter stuck in traffic.

I'd asked for something simple: a daily digest of five trending Reddit posts, summarized with a hook angle for each. The kind of thing I'd normally bang out with a cron job and forty lines of Python. Instead I'd let Anthropic's new **Launch Your Agent skill** interview me, scaffold the whole thing, and deploy it to the cloud without my writing a single line of code. The promise was magic. The reality, at minute nineteen, was a Console dashboard showing retry after retry — Reddit kept refusing the agent's requests — and a token counter that had quietly crossed eight dollars.

That tension, between "I built a cloud agent in a five-minute conversation" and "this one run cost me twelve bucks and twenty-eight minutes," is the whole story. So let me give you both halves honestly, because most of what's been written about this skill so far reads like it was copied off the GitHub README without anyone actually running the thing.

Here's what the Launch Your Agent skill is, exactly how I installed it, what it shipped, where it broke, and whether it's worth pointing at a real workflow.

## What is the Launch Your Agent skill, exactly?

The Launch Your Agent skill is a free, open-source set of Claude Code skills that interviews you about a task and turns your answers into a live, cloud-hosted Claude Managed Agent — no code required. It lives at [github.com/anthropics/launch-your-agent](https://github.com/anthropics/launch-your-agent) under an Apache 2.0 license.

That one-sentence answer is the part Google and the AI engines will quote, so let me earn the rest of your scroll by unpacking what it actually means — because there are three separate things tangled together here, and the marketing blurs them.

**Thing one: the skill itself.** It's not a product with a billing page. It's a folder of Markdown and scripts you drop into Claude Code. When you run `/launch-your-agent`, Claude reads those instructions and turns itself into an interviewer. The repo ships two skills: `launch-your-agent` (the main four-phase flow) and `wrap-up` (a companion that recaps your agent and suggests upgrades). Anthropic ships it as a *reference implementation* — the README says plainly it's "not maintained, not accepting contributions." That matters, and I'll come back to it.

**Thing two: Claude Managed Agents (CMA).** This is the actual paid infrastructure the skill deploys to. CMA is Anthropic's hosted runtime that launched on April 8, 2026, and got its agent-hosting story at Code with Claude in May. You write agent logic; Anthropic runs it in an isolated container, handles state and tool execution, and bills you standard Claude API token rates plus **$0.08 per session-hour**. If you want the deep platform teardown, I wrote a full hands-on of [Anthropic Managed Agents and what the beta still gets wrong](https://www.mejba.me/anthropic-managed-agents-office-ai-workers) — this post is about the *skill that sits on top of it*, not the platform underneath.

**Thing three: the "loop" idea.** This is the conceptual frame Anthropic wraps around the whole thing, and it's the part worth slowing down for.

You're probably picturing the skill as a fancy form-filler. It's more interesting than that. Stick with me.

## The mental model: you're not writing prompts, you're writing loops

Here's the shift that took me a second run to actually feel.

When you write a prompt, you hand Claude instructions: *do this, then this, format it like that.* You own the quality of the output. If it's wrong, it's because your prompt was wrong.

A loop inverts that. You hand Claude a **goal**, some **context**, and a set of **success criteria** — and then Claude owns the quality. It plans, picks tools, runs them, grades its own output against the criteria you gave it, and if it falls short, it tries again. The loop keeps spinning until the output clears the bar or you run out of budget.

Three inputs feed every loop:

1. **Context** — background the agent needs. For my digest: "the audience is indie AI builders, prioritize posts under 24 hours old, ignore meme threads."
2. **Goal** — what you actually want produced. "Five trending posts, each with a two-line summary and a hook angle."
3. **Success criteria** — how the agent knows it's done well. "Each entry has a working source link, a distinct angle, and reads in under fifteen seconds."

That third input is the one everybody underweights. Without explicit success criteria, the agent has nothing to grade itself against, so the loop either stops too early or spins forever. The Launch Your Agent skill spends a real chunk of its interview dragging those criteria out of you, and that interrogation is, honestly, the most valuable thing it does — more on that below.

Think of it like the difference between giving a junior employee a checklist versus giving them a definition of "done" and the authority to figure out the steps. The checklist scales worse. The definition of done scales, but only if you write it precisely. The loop is the second thing, productized.

Now, theory's cheap. Let me show you what happened when I actually installed it.

## How I installed the Launch Your Agent skill in Claude Code

I did this on a clean machine — Claude Code v2.1.101, macOS, signed in — specifically so I could write down every step instead of glossing over the "and then it just works" part. Total time from clone to first interview question: under three minutes.

**Step 1 — Clone the repo and open Claude Code.** The repo is small. There's no npm install, no build step, nothing to compile.

```bash
# Clone Anthropic's reference repo
git clone https://github.com/anthropics/launch-your-agent
cd launch-your-agent

# Launch Claude Code from inside the repo
claude
```

The reason you launch Claude Code *from inside the repo* is that Claude Code auto-discovers skills sitting in a `.claude/skills/` directory. There's no separate "install this skill" command — drop the files in the right place, start Claude Code, and it picks them up. That's the whole "installation."

**Step 2 — Invoke the skill.** Inside the Claude Code session, type:

```
/launch-your-agent
```

This fires the main four-phase skill. The companion `/wrap-up` becomes available too — you run that *after* your agent is live to get a status recap.

**Step 3 — Wire up an API key.** This is the step the breezy tutorials skip, and it's the one that actually costs you money. The skill deploys to Claude Managed Agents, and CMA runs on your own Anthropic account. So you need an API key from [platform.claude.com](https://platform.claude.com) → API keys. Create one, and the skill stores it locally in a `.env` file — never pasted into the chat transcript, which is the right call for a credential.

Pro tip: before you generate that key, set a hard spending limit on the API key itself in the Console. I didn't, the first time. You can guess where this is going.

**Step 4 — Answer the interview.** Once the key's in place, the skill starts asking questions. This is where the real work happens, and it deserves its own section.

If you've used Claude Code skills before, none of this will surprise you — it's the same `.claude/skills/` discovery pattern I covered in my breakdown of [advanced Agent Skills in Claude Code](https://www.mejba.me/agent-skills-advanced-claude-code). What's new is what the skill *does* with that pattern: it doesn't help you write code, it interviews you into a deployment.

## The interview is the actual product

I went in expecting a glorified config wizard. What I got felt more like a sharp PM cornering me in a hallway until I admitted what I actually wanted.

The skill — running as Phase 1 of four (Interview → Stage & Launch → Grade & Iterate → Run Without You) — worked through roughly these areas:

- **What does the agent do?** Not "summarize Reddit" but the specific shape of the output. It pushed back when my first answer was vague.
- **What's the output format?** Markdown digest? Email? A row in a sheet? It wanted the artifact, concretely.
- **Who's the audience and what are the data sources?** This is where I said "indie AI builders" and "Reddit, specifically r/LocalLLaMA and r/ChatGPT."
- **What are the success criteria and the grading rubric?** The part I mentioned earlier. It made me define what a *good* digest looks like versus a mediocre one, in terms it could actually score against.
- **How often should it run?** Daily, weekly, on-demand. I picked daily at 7 a.m.

Then it did something I didn't expect: it scoped a **v0** — a deliberately minimal first version — rather than trying to build my full dream agent on the first pass. It told me, essentially, "let's get the smallest useful version live, grade it, then climb." That's good engineering discipline baked into a skill, and it's the single biggest reason a non-coder can use this without producing a tangled mess.

When the interview wrapped, the skill generated a `my-agent/` folder. I want to be specific about what's in it, because this is the part that makes the thing legible instead of magic:

- **A build sheet** — the human-readable spec of what's being deployed.
- **The exact API payloads** — the literal JSON the skill sends to the CMA API. You can read it, audit it, and reuse it.
- **A resumable launch script** — so if a deploy dies halfway, you re-run it instead of starting over.
- **An eval scaffold** — the grading harness that scores each run against your criteria.
- **An overview page** — a generated dashboard-ish summary.
- **`NEXT-DIRECTIONS.md`** — a v1/v2 roadmap of upgrades for later.

That folder is the difference between "an AI did something opaque in the cloud" and "here is the exact, inspectable, version-controllable definition of my agent." I cannot overstate how much that artifact transparency matters once you're spending real money on runs.

You've made it past the setup. Now the honest part — what happened when I actually let it run.

## What happened when I launched my first agent

Phase 2 (Stage & Launch) pushed my v0 to Claude Managed Agents. In the Console, a new agent appeared with its environment, and the skill triggered the first graded run.

Then I sat back to watch the dashboard, which shows session history, API calls, and outcomes per run. And this is where the gap between demo and reality opened up.

The agent's job needed Reddit data. The agent could not reliably get Reddit data. Reddit's endpoints kept refusing the requests — rate limits, access blocks, the usual hostility public APIs show to anything that smells automated. So the loop did exactly what loops do: it failed a step, evaluated, and *tried again.* And again. Each retry burned tokens.

Three hard numbers from that first run, and I'm giving you the real ones, not flattering ones:

- **~28 minutes** of wall-clock time, almost all of it spent on retries and failure handling, not productive work.
- **~$12** in API cost for a single run, driven by heavy token usage as the loop chewed through Opus-class reasoning on each retry. (Opus 4.8 runs $5 input / $25 output per million tokens, and a thrashing loop generates a *lot* of output tokens.)
- **5** trending stories in the final digest — because despite the thrash, it did eventually produce a usable output, with links and commentary.

So: it worked. It also cost more than a month of some SaaS subscriptions, for one daily digest. If that ran every morning unsupervised, I'd be looking at roughly $360 a month for a Reddit summary. That's not a typo, and it's the kind of math nobody mentions in the launch threads.

Here's the part that redeemed it, though. The skill's Phase 3 (Grade & Iterate) didn't just hand me the expensive mess and shrug. It graded the run, noticed the Reddit failures were the cost driver, and **recommended switching the data source to web search only** — drop the flaky Reddit dependency, pull trending discussion through search instead, cut both the error rate and the token burn. The agent diagnosed its own most expensive failure mode and proposed the fix. That's the loop earning its keep.

## The honest take: where this skill is brilliant and where it bites

I've now run this on three different task ideas — the Reddit digest, a competitor-pricing watcher, and a daily changelog summarizer for a repo I follow. Patterns emerged. Let me give you the trade-offs nobody's putting in their headlines.

**What it genuinely nails:**

The interview-to-deployment pipeline is the real innovation, not the cloud hosting. I've watched a lot of "no-code AI agent" tools, and they all fail the same way: they make it easy to start and impossible to know if the result is any good. This skill inverts that by forcing success criteria out of you up front and then *grading against them.* The `my-agent/` artifacts mean you're never trapped — you can read the exact payloads, version them in git, and walk away from the skill entirely while keeping the agent. That's an unusually honest design for something pitched at beginners.

**Where it bites — and these are real:**

*Third-party tool reliability is your problem, not the skill's.* The Reddit wall wasn't a bug in Launch Your Agent. It's the reality that the open web fights automated access, and a loop that retries failures will happily turn that friction into a five-figure-token bonfire. Before you point an agent at a data source, ask: *will this source let a bot in?* If the answer is shaky, the loop will find out the expensive way.

*The cost model rewards precision and punishes vagueness.* A tight goal with crisp success criteria converges in a few cheap iterations. A loose goal spins. Because the agent owns quality, a fuzzy definition of "done" means it keeps trying to satisfy a target you never clearly drew. Your spend is directly proportional to how sloppy your interview answers were. If you're serious about keeping these bills sane, my [AI agent cost-optimization guide](https://www.mejba.me/ai-agent-cost-optimization-guide) covers the token-discipline tactics that matter most here.

*"Reference implementation, not maintained" is a real caveat.* The repo says it outright. This is Anthropic showing you a pattern, not shipping you a supported product. When CMA's API shifts — and a public-beta API will shift — the skill won't get a patch. You're adopting a snapshot. Fine for learning and prototyping. Think harder before you build a business-critical workflow on top of an explicitly unmaintained scaffold.

If you'd rather have someone design these agents so they converge cheaply and don't thrash on flaky data sources, that's a chunk of what I build for clients — you can see the kind of automation work I take on at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

That's the experience. Now let me zoom out to what it means for how you should actually use the thing.

## When this skill is worth it (and when a cron job wins)

The mistake I almost made was treating Launch Your Agent as a replacement for every automation. It isn't. Here's the decision framework I landed on after three runs.

**Reach for the skill when:**

- The task is **genuinely judgment-heavy** — summarizing, prioritizing, triaging, writing — where a deterministic script can't capture "good." The grading loop earns its cost when quality is subjective.
- The task **recurs** and you'd otherwise babysit it. CMA's scheduled deployments (cron schedules, now in public beta as of June 9, 2026) let an agent fire on its own; you get pause, resume, archive, and on-demand re-runs, with an org cap of 1,000 scheduled deployments.
- The data sources are **automation-friendly** — your own APIs, web search, services with vault-injected credentials (CMA can inject secrets into the sandbox at runtime without the model ever seeing them).

**Skip it and write the cron job when:**

- The task is **deterministic.** If a forty-line script produces the exact right answer every time, an LLM loop is a strictly more expensive way to get a worse-defined result. Don't pay Opus rates to do `if/else`.
- The data source is **hostile to bots** (looking at you, Reddit) and you don't have legitimate API access. The loop will retry into your wallet.
- You need **tight, predictable cost.** A script costs pennies in compute. A reasoning loop costs whatever it takes to satisfy your criteria, which you cannot perfectly predict in advance.

This connects to a bigger shift I keep writing about: the unit of automation is moving from *scripts you maintain* to *goals you delegate.* I dug into the always-on, scheduled side of that in my piece on running [Claude Code loops on a cron schedule](https://www.mejba.me/claude-code-loop-cron-scheduling). The Launch Your Agent skill is the friendliest on-ramp to that world I've found — as long as you go in knowing it's a metered taxi, not a flat-rate subscription.

## What I'd tell you to actually do

Don't deploy a daily agent on your first run. That was my mistake, and it's an avoidable one.

Here's the sequence that would have saved me eleven dollars and a lot of dashboard-staring:

1. **Install the skill and run the interview on a real task** — but for the first agent, pick something with a *friendly* data source. Your own files, your own API, or plain web search. Not Reddit. Not anything that fights scrapers.
2. **Set an API key spending limit in the Console first.** A `$5` cap on a fresh key turns "I lost track of cost" into "the run stopped itself." Cheap insurance.
3. **Run it once, on-demand. Read the graded output and the `my-agent/` folder.** Treat the first run as a paid lesson in how the loop behaves with your task, not as production.
4. **Only then schedule it.** Once you've seen one clean, cheap run, turn on the cron deployment. Now you're automating a known quantity instead of a question mark.
5. **Run `/wrap-up`** to get the recap and the suggested next upgrades, then decide whether v1 is worth the spend.

The bigger lesson sitting underneath all of this: the skill didn't make automation easy. It made *defining the goal* easy — and then it ruthlessly exposed every place my definition was sloppy, by charging me for the sloppiness in real tokens. That's not a flaw. That's the most honest feedback loop I've gotten from an AI tool in a while.

So here's the question I'll leave you with, the same one the agent basically asked me at minute nineteen: if you had to write down the exact success criteria for a task you do every day — the precise definition of "done well" — could you? Because the moment you can, you can hand it to a loop. And the moment you can't, you've found the part of your work that was never going to be automated anyway.

## Frequently Asked Questions

### What is the Launch Your Agent skill for Claude Code?
The Launch Your Agent skill is a free, open-source set of Claude Code skills from Anthropic that interviews you about a task and deploys it as a live, cloud-hosted Claude Managed Agent without any coding. It ships two skills, `launch-your-agent` and `wrap-up`, under an Apache 2.0 license at github.com/anthropics/launch-your-agent. For the full install walkthrough, see the installation section above.

### Is the Launch Your Agent skill free to use?
The skill itself is free and open-source, but it deploys to Claude Managed Agents, which bills you standard Claude API token rates plus $0.08 per session-hour on your own Anthropic account. My first real run cost about $12 because the loop kept retrying a flaky data source — so "free skill" does not mean "free to run."

### How do I install the Launch Your Agent skill?
Clone the repo with `git clone https://github.com/anthropics/launch-your-agent`, `cd` into it, run `claude` to open Claude Code from inside the folder, then type `/launch-your-agent`. Claude Code auto-discovers skills in `.claude/skills/`, so there is no separate install step. You will also need an Anthropic API key from platform.claude.com.

### What is a Claude Managed Agent (CMA)?
A Claude Managed Agent is a cloud-hosted agent that Anthropic runs in an isolated container on its own infrastructure, handling sandboxing, state, and tool execution while billing you per token plus $0.08 per session-hour. As of June 2026, CMA supports cron-style scheduled deployments and vault-injected credentials in public beta. The Launch Your Agent skill is one way to create one.

### Why did my Launch Your Agent run cost so much?
Runs get expensive when the loop repeatedly retries a failing step — in my case, Reddit refused the agent's requests, and each retry burned Opus-class tokens at $5 input / $25 output per million. Vague success criteria make it worse, because the loop keeps trying to hit an undefined target. Tight criteria and automation-friendly data sources keep cost down.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
