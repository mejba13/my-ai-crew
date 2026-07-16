**BRAND:** mejba.me
**TITLE:** Claudeex Tested: Claude Code and Codex Loop for Plans
**META TITLE:** Claudeex Review: Claude Code + Codex Loop Tested
**SLUG:** claudeex-claude-code-codex-loop
**PRIMARY KEYWORD:** Claudeex Claude Code Codex loop
**META DESCRIPTION:** I tested Claudeex — the Claude Code Codex loop that audits and revises plans automatically. 3 rounds, ~80% bug catch rate. Here is what actually held up.
**TAGS:** Claude Code, Codex CLI, AI Workflow, Hooks, Iterative Planning

---

The plan looked clean. Twelve numbered steps. Architecture diagram in markdown. Database migration order spelled out. I was about to hit "implement" when I remembered I had Claudeex installed, so I figured I would let it run before shipping anything.

Round one came back with seven issues. Three of them were serious. One of them — "the plan calls a webhook before the database row exists" — would have cost me a full afternoon of debugging at 11pm on a Tuesday. The plan I had been about to ship had a race condition baked into step four.

That is the moment I stopped thinking of Claudeex as a curiosity and started thinking of it as the kind of thing I should have been running for the last six months.

If you have not seen it yet — Claudeex is a small bash and YAML harness that turns Claude Code and the OpenAI Codex CLI into a planning loop. Claude Code drafts a plan, Codex audits it, Claude Code revises, Codex audits the revision, and so on until you hit a configurable round cap. Default is three rounds. The whole thing rides on Claude Code's stop hook, which pauses execution between turns so the bash script can shell out to `codex exec` for the audit pass.

I have spent the last two weeks running it on real client work — a visitor analytics page, a Stripe webhook refactor, a small Laravel admin dashboard — and there is enough here that I want to walk through what works, where the seams show, and why two-model planning loops are about to become the default for anything more complex than a CRUD form.

## What Claudeex Actually Is (Beyond the README)

The pitch is straightforward: Claude Code is good at drafting plans, Codex is good at finding holes in them, and most planning bugs are catchable if you just have a second model read the plan with a critical eye. So instead of asking *you* to be the second model, Claudeex automates the back-and-forth.

Mechanically, it is four pieces:

1. **A bash launcher** that takes a prompt, writes a YAML state file, and starts Claude Code with the right hook configuration loaded
2. **A YAML state file** that tracks the prompt, total rounds, current iteration, and any pinned context files (like `scope.md` or `architecture.md`)
3. **A Claude Code stop hook** that fires after Claude finishes a planning turn, reads the state file, and decides whether to invoke Codex or let Claude finish
4. **Three slash commands** — `/plan`, `/review`, and `/cancel` — plus a `/rollback` for wiping plan state when you want a clean slate

The stop hook is the clever part. Claude Code's [stop hook fires once per turn when Claude finishes responding](https://code.claude.com/docs/en/hooks), and a stop hook that exits with code 2 forces Claude to keep working. Claudeex uses that exit-2 trick to inject the Codex audit transcript back into the conversation as if Claude itself had received feedback from a reviewer, then asks Claude to revise. From inside Claude Code's context, it just looks like another turn — but the turn happens to contain a structured critique from a different frontier model.

If you have read my [breakdown of the Claude Code and Codex two-agent workflow](https://www.mejba.me/claude-code-codex-two-agent-workflow), the mental model is similar. The difference is that Claudeex bakes the loop into the Claude Code session itself — you do not have to manually pipe transcripts between terminals or copy-paste critiques. The hook does it.

## Why Two-Model Planning Beats One-Model Planning

I want to be careful here because the obvious framing — "two models are better than one" — is the kind of vague claim I would normally cut from a draft. So let me get specific about what actually changes.

When Claude Code drafts a plan alone, it is operating from a single distribution of "what good plans look like." It has its own training biases. It has its own blind spots. When I ask it to "double-check the plan," it is essentially being asked to disagree with itself, which is a thing language models are notoriously bad at. There is research on sycophancy and self-consistency that gets at why — models trained with RLHF tend to commit to their first answer and then defend it, even when prompted to critique.

Codex is a different model with a different training pipeline. The current production model in the Codex CLI is GPT-5.5 (with GPT-5.4 as the fallback if your account has not been bumped yet). When Codex audits a Claude-authored plan, it is reading prose written in a slightly different cognitive style, and it is much more willing to flag things Claude glossed over. The asymmetry is the value. Codex catches things Claude wrote past; Claude catches things Codex would have missed if it had drafted the plan instead.

In practice, on the projects I have run through Claudeex over the last two weeks, the catch rate on planning-stage bugs is somewhere around 80% in the first two to three rounds. After round three, the marginal returns get thin — Codex starts flagging stylistic preferences rather than actual problems. That is why the default of three rounds is correct. I tried setting it to five on a complex job and round four was Codex arguing about whether I should use an enum or a string for a status field. Round five was Codex changing its mind about round four. Three rounds is the sweet spot.

## The Slash Command Surface

There are four slash commands and you only really need two of them day to day.

**`/plan <prompt>`** is the workhorse. You hand it a description of what you want to build, optionally pin some context files like `scope.md` or `architecture.md`, and the loop runs. Claude drafts, Codex audits, Claude revises. At the end you get a final plan with the deletions and additions from each round shown in red and green so you can see how the plan evolved. If you have ever done a serious code review and watched a junior engineer's PR transform between v1 and v3, the diff view feels exactly like that.

**`/review`** is the read-only version. It takes an *existing* plan — one you wrote, one a teammate wrote, one Claude wrote in a previous session — and runs Codex over it without any revision step. You get the audit comments and that is it. I use this on plans I am about to hand off to another developer; it is a cheap second opinion before the work starts.

**`/cancel`** is an emergency stop. If the loop goes off the rails — Codex starts hallucinating dependencies, Claude starts rewriting the same section in circles — you can interrupt the run cleanly without leaving the YAML state file in a broken half-state.

**`/rollback`** wipes the plan state entirely. Useful when you want to start over from a blank slate without restarting Claude Code.

The syntax matters more than you would expect. The first time I ran `/plan`, I gave it a one-sentence prompt — "build a visitor analytics page that does not use third parties." Claude wrote a reasonable but generic plan. Codex audited it and the first comment back was "the prompt is too vague to evaluate; what is the success criterion?" That is a feature, not a bug. The optional **ask-user-input tool** that ships with Claudeex is specifically there for these moments — when a prompt is too vague to plan against, it pauses the loop and asks you clarifying questions before continuing. I learned to lead with `/plan` plus a paragraph of context and a pinned `scope.md`, and the quality of every round after that improved noticeably.

## What the Bash and YAML Actually Look Like

The launcher is small enough to read end to end. Here is the shape of it, with the noise cut:

```bash
#!/usr/bin/env bash
set -euo pipefail

PROMPT="$1"
ROUNDS="${2:-3}"
STATE_FILE=".claudeex/state.yaml"

mkdir -p .claudeex

cat > "$STATE_FILE" <<EOF
prompt: |
  $PROMPT
rounds_total: $ROUNDS
rounds_completed: 0
phase: drafting
context_files:
  - scope.md
  - architecture.md
EOF

claude --hook-config .claudeex/hooks.json \
       --slash-command "/plan $PROMPT"
```

That is the entire entry point. Everything else lives in the hook configuration and the state file. The hook config registers a stop hook pointed at a small shell script:

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "command",
            "command": ".claudeex/audit.sh"
          }
        ]
      }
    ]
  }
}
```

And `audit.sh` is the bridge to Codex. The script reads the YAML state, checks the current phase and round count, and decides whether to invoke Codex or let Claude finish:

```bash
#!/usr/bin/env bash
set -euo pipefail

STATE_FILE=".claudeex/state.yaml"
ROUNDS_TOTAL=$(yq '.rounds_total' "$STATE_FILE")
ROUNDS_DONE=$(yq '.rounds_completed' "$STATE_FILE")
PHASE=$(yq '.phase' "$STATE_FILE")

if [ "$PHASE" = "drafting" ] && [ "$ROUNDS_DONE" -lt "$ROUNDS_TOTAL" ]; then
  PLAN=$(cat .claudeex/plan-current.md)
  CRITIQUE=$(codex exec "Audit this plan for correctness, missing edge cases, race conditions, and broken dependencies. Be specific. Cite line numbers." --input "$PLAN")

  echo "$CRITIQUE" > .claudeex/critique-round-$((ROUNDS_DONE + 1)).md
  yq -i ".rounds_completed = $((ROUNDS_DONE + 1))" "$STATE_FILE"

  # Exit 2 forces Claude Code to keep working with the critique injected
  cat <<EOF >&2
Round $((ROUNDS_DONE + 1)) audit from Codex:

$CRITIQUE

Please revise the plan addressing each point.
EOF
  exit 2
fi

# Loop complete — let Claude finish normally
exit 0
```

The exit-2 pattern is doing all the heavy lifting. As [the Claude Code hooks reference](https://code.claude.com/docs/en/hooks) describes, a stop hook exiting with status 2 forces Claude to continue working, with the hook's stderr output injected back into the conversation. Claudeex uses that to feed Codex's critique back to Claude as if it were a new turn — and Claude responds by revising the plan in place.

It is worth saying: this is duct tape, but it is *good* duct tape. The hook system was not designed for cross-model adversarial loops. It was designed for things like "auto-format on edit" or "block dangerous commands." The fact that it can be repurposed for something this elaborate is a small testament to how well the primitives were chosen.

## The Demo I Built To Stress-Test It

I needed a real test case, so I picked something I have actually been meaning to ship: a single-page visitor analytics tool that tracks interactions on my site without sending data to any third-party service. No Google Analytics, no Plausible, no Fathom. Just my own backend collecting clicks, scrolls, and time-on-page from my own visitors.

The reason this is a good Claudeex test case is that it sits at an awkward intersection of frontend instrumentation, backend storage, and privacy law. There are at least four ways to get it wrong, and most of those ways are *planning* mistakes, not coding mistakes. You can write the JavaScript correctly and still ship something illegal under GDPR. You can design the database schema correctly and still create a system that crashes the page on slow networks. The bugs hide upstream of the code.

I pinned two context files: `scope.md` (functional requirements, including the "no third parties" rule and a list of events to track) and `architecture.md` (a sketch of the stack — Laravel backend, vanilla JS frontend, MySQL for storage, Redis for buffering high-frequency events). Then I ran:

```
/plan Build a visitor interaction tracking page following scope.md and architecture.md. Plan should cover schema, frontend instrumentation, backend ingestion, and the consent flow.
```

About 15 to 20 minutes of iteration later, I had a plan I would actually ship. Round one produced a reasonable first draft that completely ignored the consent flow — Claude treated GDPR as a footnote rather than a gating requirement. Codex's round-one critique opened with "the consent flow is missing entirely; without it, no events should fire," which is exactly the kind of thing that would have bitten me in production. Round two added the consent gate but introduced a different problem: it had the frontend buffer events in localStorage before consent was granted, which is itself a GDPR violation in some interpretations. Codex caught that in round two. Round three had a clean version where the frontend stores nothing until consent is given, then flushes a queue of *intent-recorded* events to the backend.

The diff view at the end was the part that surprised me most. Claudeex shows the plan with deletions struck through in red and additions highlighted in green, round by round. You can see exactly what the audit caught and where the plan strengthened. It is the closest thing I have seen to a code review experience for plans rather than code.

## Claudeex vs Claude Code Alone

Here is the comparison I keep coming back to. After two weeks of running both — sometimes the same prompt through both, sometimes alternating based on project complexity — the differences sort into a clear pattern.

| Dimension | Claude Code Alone | Claudeex Loop |
|---|---|---|
| Planning detail | Good first draft, often complete on the surface | Same first draft, then 2–3 rounds of forced refinement |
| Bug detection | Catches obvious issues; misses cross-cutting concerns | Catches roughly 80% of planning bugs in 2–3 rounds |
| Iterative refinement | Requires you to manually prompt "what did you miss?" | Automatic; the loop runs without your intervention |
| Complex multi-step builds | Plans are coherent but often optimistic | Plans are coherent, pessimistic, and edge-case aware |
| Clarification handling | Will guess if the prompt is vague | Will ask via the optional ask-user-input tool |
| Time cost | 1–2 minutes for a plan | 15–20 minutes for a plan |
| Token cost | Lower | Roughly 2.5–3x for the same plan |
| Best for | Small focused tasks, prototypes, throwaway scripts | Production features, security-sensitive work, anything that touches data |

The token cost is real and worth being honest about. Three rounds of Claude planning plus three rounds of Codex auditing is not free. For a quick script or a prototype, the overhead is not worth it — you can just run a plan, scan it yourself, and ship. For anything where a planning bug would cost you more than 30 minutes of debugging, Claudeex pays for itself on the first save.

The clarification behavior is the underrated benefit. The optional ask-user-input tool turns vague prompts into productive conversations instead of confidently-wrong plans. In my experience, about a third of the prompts I write are too vague to plan against — and Claudeex is the first tool I have used that catches that consistently before generating a plan that confidently misinterprets what I wanted.

If you want a related comparison, the [Claude Code and Codex plugin dynamic duo workflow](https://www.mejba.me/claude-code-codex-plugin-dynamic-duo-workflow) is the manual version of what Claudeex automates. The plugin route is more flexible; Claudeex is more opinionated. I use both, depending on the job.

## Where Claudeex Falls Short

I want to be honest about this because the article would be useless otherwise.

**It is brittle on long contexts.** When the plan and the audit transcript get long enough — somewhere past 30,000 tokens combined — Claude starts losing track of which round it is in and sometimes regenerates whole sections that Codex already approved. This is a context-management problem, not a Claudeex bug per se. But it is the seam where the duct tape shows.

**The YAML state file is not great for parallel work.** If you try to run two Claudeex sessions in the same repo at the same time, they will trample each other's state files. There is no per-session isolation by default. I worked around it by creating per-feature subdirectories, but it is a real wart.

**Codex sometimes hallucinates dependencies.** This happens roughly one round in ten. Codex will critique a plan for "missing the Redis Streams setup" when the plan does not actually need Redis Streams. Claude, given that critique, will helpfully add Redis Streams setup. You end up with a plan that is more complex than it needs to be, with infrastructure introduced by an audit hallucination. The fix is to read every round's diff yourself and reject changes that do not match reality. Which means Claudeex does not actually remove the human from the loop — it just makes the human's job easier.

**It is not magic for greenfield work.** When you do not have a `scope.md` or an `architecture.md` to pin, the early rounds of the loop spend a lot of cycles arguing about scope. Claudeex works best when you have done the strategic thinking already and want help with tactical planning. If you are still figuring out *what* to build, the loop will not help you decide. It will just keep refining a plan for the wrong thing.

**The model asymmetry can flip.** Codex is good at audits *most of the time*. But when the topic drifts toward Claude's stronger areas — anything to do with Anthropic-specific tooling, agentic workflows, or [Claude Code's own hooks system](https://www.mejba.me/claude-code-advisor-slash-command), for example — Claude's draft is sometimes better than Codex's audit. In those cases, the loop adds noise rather than signal. You learn to recognize when you are in one of those zones and just run `/plan` once without the audit step, or use `/review` with a longer round-three threshold.

## Beyond Code: Where Else This Pattern Works

The thing that genuinely surprised me is how well the pattern extends past code. I have been testing Claudeex on non-code planning tasks for the last week and the results are interesting enough to mention.

I ran it on a slide deck outline for a workshop I am giving in May. The first draft from Claude was solid — a logical flow, decent section breakdowns, reasonable time estimates. Codex's audit flagged that the workshop had no exercises in the middle third, which would mean 40 minutes of straight talking after the energy peak around minute 25. I had not noticed. I would have caught it in rehearsal, but rehearsal would have happened the night before. Claudeex caught it before I had wasted any prep time.

I ran it on a feature spec for a freelance client — not the code, just the feature description doc that was going into the proposal. Round one passed. Round two, Codex flagged that the doc described the happy path but never mentioned what happens when the third-party API is down. The client signed off on a v3 that included a graceful degradation paragraph. That paragraph is now contractually part of what I owe them.

I have not tried it on Excel models or financial documents yet, but I see no reason it would not work. The pattern is generic: anywhere you can write a plan in markdown and ask another model to audit it, the loop applies. This is also where I see it converging with the broader pattern I covered in [Claude Code's ultra plan mode](https://www.mejba.me/claude-code-ultra-plan-tested) — both are about formalizing the planning step instead of treating it as a vibes-driven preamble to coding.

## Setup, Real Cost, and What to Run It On

Getting it running takes about ten minutes if your environment is already set up.

**Prerequisites:**
- Claude Code installed and signed in
- Codex CLI installed (`npm i -g @openai/codex` or `brew install --cask codex`) and signed in
- `yq` installed (`brew install yq` on macOS)
- A repo where you want to run it

**Install:**
```bash
git clone <claudeex-repo-url> .claudeex
chmod +x .claudeex/*.sh
```

**First run:**
```bash
.claudeex/run.sh "build a visitor analytics page following scope.md"
```

The first time, expect 15 to 20 minutes for a moderately complex plan. The Codex CLI will use whichever model your account has access to — GPT-5.5 if you are on the latest, GPT-5.4 if not. Both work fine for audit-style critique; GPT-5.5 is meaningfully sharper at catching subtle issues.

**Cost-wise**, a three-round loop on a moderately complex plan runs me about three to four times the cost of a single Claude Code planning turn. For freelance and client work, that is rounding error. For personal experiments, you might want to run it only on the harder problems and let `/plan` (without the loop) handle the small stuff.

**What to run it on first:** any feature where a planning mistake would cost you more than a half-day of rework. Authentication flows. Payment integrations. Data migrations. Anything with concurrency. Anything touching GDPR or HIPAA. These are the areas where 15 minutes of automated audit pays for itself ten times over. If you are doing security-adjacent work, the same logic applies — and the [security scanner agent pattern in Claude Code](https://www.mejba.me/claude-code-security-scanner-agent) pairs naturally with Claudeex on the planning side.

Skip it for: bug fixes where the bug is already isolated, small UI tweaks, anything you have done before and could write the plan for in your sleep. The overhead is not worth it on those.

## Frequently Asked Questions

### What is Claudeex and how does it work?
Claudeex is an iterative planning loop where Claude Code drafts a plan, the OpenAI Codex CLI audits it, and Claude Code revises — repeating for a configurable number of rounds (three by default). It uses a Claude Code stop hook with exit code 2 to inject Codex's critique back into the conversation as if it were a new turn. For a full walkthrough of the bash and YAML implementation, see the section above on the launcher and audit script.

### How many rounds does Claudeex need to catch most planning bugs?
Two to three rounds catches roughly 80% of planning bugs in my testing. Round four and beyond produce diminishing returns and often surface stylistic preferences rather than actual issues. The default of three rounds is well-tuned. See the comparison table above for full context on time and token cost.

### Does Claudeex work for non-code planning tasks?
Yes. The pattern works for any plan written in markdown that another model can audit — slide outlines, feature specs, project proposals. I have tested it on workshop decks and freelance client specs with strong results. The "Beyond Code" section above walks through specific examples.

### What is the difference between /plan and /review in Claudeex?
`/plan` runs the full iterative loop: draft, audit, revise, repeat. `/review` is read-only — it runs Codex over an existing plan once and returns the audit comments without any revision step. Use `/review` when you want a second opinion on a plan you or a teammate has already written.

### Is Claudeex worth the extra token cost?
For production features, security-sensitive work, or anything where a planning bug would cost more than 30 minutes of debugging — yes. The 2.5–3x token cost pays for itself the first time it catches a race condition. For prototypes, throwaway scripts, or work you have done many times before, the overhead is not justified.

## What I Took Away

The morning after my first real Claudeex run, I went back and looked at three plans I had shipped in the previous month — plans I had been happy with at the time. I ran each of them through `/review` to see what Codex would have caught.

All three had at least one issue. Two had race conditions I had missed. One had a missing rollback path on a destructive migration that, in fairness, would have been caught in code review — but only if the reviewer was paying close attention. None of them were catastrophic. All three would have been less embarrassing if the audit had run before I shipped instead of two weeks later in retrospect.

That is the part that got me. Not the demo. Not the diff view. The realization that I had been shipping medium-quality plans and calling them done because nothing in my workflow ever pushed back on them. Claude Code does not push back. I do not push back, because I wrote the prompt and I am rooting for the plan to be good. The only thing in this loop that has no skin in the game is Codex.

The model that does not care if your plan is good is the model you want auditing it.

The plan I almost shipped before installing Claudeex — the one with the race condition baked into step four — would have made it past me. It would have made it past Claude. It would not have made it past Codex, because Codex did not write it and had no reason to defend it. That is the asymmetry the loop is exploiting, and that is the asymmetry that makes it worth running.

Tonight, before you ship the next plan you are about to ship, run it through a second model. If you have Codex installed, do it manually. If you want it automated, install Claudeex. Either way — let something that does not care about your plan tell you what is wrong with it. You will not regret the ten minutes.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
