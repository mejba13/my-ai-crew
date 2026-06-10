**BRAND:** mejba.me
**TITLE:** The Grill Me Skill: How I Extract My Own Brain
**META TITLE:** Grill Me Skill for Claude Code: Extract Your Knowledge
**SLUG:** grill-me-skill-claude-code-knowledge-extraction
**PRIMARY KEYWORD:** Grill Me skill Claude Code
**META DESCRIPTION:** The Grill Me skill for Claude Code interviews you until your knowledge is captured. Here's how it works, why it beats a brain dump, and how I use it.
**TAGS:** Claude Code, AI Skills, Knowledge Extraction, AI Operating System, Workflow

---

The skill that taught me the most about building an AI Operating System is six sentences long.

Not six hundred lines. Not a Python toolchain with a dozen reference docs. Six sentences sitting in a markdown file. I opened it expecting something clever and almost closed the tab when I saw how short it was. Then I ran it, and it interrogated me about a packaging workflow for fifty-one minutes straight — and by the end it knew more about how I actually run that part of my business than any document I'd ever written about it.

That's the **Grill Me skill for Claude Code**, and it solves the single hardest problem in this entire space: getting what's in your head into the machine. Not "what is a skill." Not "ten skills you should install." The unglamorous bottleneck that nobody wants to talk about — your knowledge is locked in your skull, and a base model can't read minds.

Here's the part that took me a while to accept. You and I and every other person reading this are pointing the same model at our problems. Claude Opus 4.8. Same weights, same training, same reasoning. The model is not your edge. Whatever makes your AI outputs different from the generic slop everyone else is generating comes from one place: the context, the voice, and the hard-won decisions *you* feed it. Strip that away and every output regresses to the bland mean. So the real game was never "find a better model." It's "extract your knowledge and shape it for a machine to consume." Grill Me is the best tool I've found for doing exactly that.

## Why your brain dump produces generic AI output

Let me start with the thing I got wrong for months.

When I wanted to build a skill, I'd sit down and write everything I knew about the process into a markdown file. A brain dump. I'd think I was being thorough — three hundred lines, headers, bullet points, the works. Then the skill built on top of it would produce work that was *fine*. Competent. Forgettable. The kind of output that makes you go "yeah, that's roughly right" and then quietly rewrite half of it.

The problem isn't that I'm a bad writer. The problem is that a brain dump only captures what you already know you know. It can't capture the stuff you don't think to mention — the exceptions, the "oh wait, except when the client is in the EU," the reason you stopped doing it the other way three years ago. That tacit knowledge is precisely the differentiation that makes your AIOS *yours* instead of everyone else's. And you will never write it down voluntarily, because by definition you've forgotten it's even a decision.

I learned this watching how I scope client projects. The projects that go well aren't the ones where I let the client talk. They're the ones where I ask uncomfortable, almost-annoying questions. "What happens to the order if the payment succeeds but the inventory check fails?" The client pauses. They hadn't thought about it. *That pause is the entire value.* It's the difference between a system that's 80% reliable and one that's 95% reliable. The questions that feel like overkill are the ones that find the gaps.

Grill Me takes that exact dynamic — the relentless discovery interview — and points it at *you*. The AI becomes the annoying consultant, and you become the client who hadn't thought about the edge case. That role reversal is the whole trick. If you've spent time on [why AI agent context beats configuration](https://www.mejba.me/blog/ai-agent-context-beats-configuration), this is the most concentrated version of that idea I know.

But before I show you the prompt, you need to see how little is actually in it. It's going to surprise you.

## What the Grill Me skill actually contains

Here is the entire skill. This is the real `SKILL.md` from my `.claude/skills/grill-me/` directory, originally written by Matt Pocock and published in his [public skills repo](https://github.com/mattpocock/skills) under `skills/productivity/grill-me/`:

```markdown
---
name: grill-me
description: Interview the user relentlessly about a plan or design until
  reaching shared understanding, resolving each branch of the decision tree.
  Use when user wants to stress-test a plan, get grilled on their design,
  or mentions "grill me".
---

Interview me relentlessly about every aspect of this plan until we reach a
shared understanding. Walk down each branch of the design tree, resolving
dependencies between decisions one-by-one. For each question, provide your
recommended answer.

Ask the questions one at a time.

If a question can be answered by exploring the codebase, explore the codebase
instead.
```

That's it. That's the whole thing. The frontmatter is wiring — a name and a description that tells Claude *when* to fire the skill. The body is four instructions:

1. **Interview relentlessly until shared understanding.** Not "ask a few questions." Relentlessly. The word is doing real work — it gives the model permission to keep going past the point where a polite assistant would stop.
2. **Walk the design tree, resolving dependencies one at a time.** Every decision has child decisions. "Advanced search or simple box?" If advanced — which filters? Which sort orders? Each answer opens new branches. You traverse until every leaf is concrete.
3. **For each question, give your recommended answer.** This is the part most people miss. It's not a blank interrogation. The model proposes a default, so you're *reacting* to a recommendation, not generating answers from a cold start. Reacting is ten times faster than authoring.
4. **If the codebase can answer it, look there instead of asking.** Don't waste my attention on questions a `grep` could resolve.

That fourth line is why I trust it. A worse skill would ask me forty questions including five it could have answered itself by reading my code. This one offloads the cheap questions to itself and saves my brain for the expensive ones.

**How many questions does a Grill Me session actually ask?** In my real sessions, anywhere from roughly 16 to 50, depending on how fuzzy the topic is — which matches what other practitioners report. A tight, well-understood feature lands near the bottom. An entire business operation lands at the top, and then some.

The lesson I keep relearning: effective skills are not complex automations. The most useful thing in my whole setup is a six-sentence prompt. I write about this tension in [how I test Claude skills before they break my workflow](https://www.mejba.me/blog/claude-skill-creator-testing-optimization) — complexity is usually a smell, not a feature. Grill Me is the proof.

So if the original is this good, why did I change it? Because of one specific failure mode that shows up the moment your sessions get long.

## The fix that made long grilling sessions usable: checkpointing

The original Grill Me has a problem it can't see, and you only hit it once your sessions run long.

My packaging session ran fifty-one minutes. By minute forty, the conversation had grown enormous — dozens of questions, dozens of answers, branches inside branches. And the model started to *drift*. A decision I'd locked in at minute eight quietly stopped showing up in its reasoning at minute forty. Not because the model is dumb, but because as the active conversation grows, earlier turns get less attention. The detail you settled an hour ago is competing with everything since for the same finite focus. (If you've felt this on any long session, I broke down the mechanics in [Claude Code context hygiene and token limits](https://www.mejba.me/blog/claude-token-limits-context-hygiene).)

A brilliant hour-long interview is worthless if the model forgets the first half by the end.

So the enhancement I run is dead simple: **checkpoint after every single question.** The moment a question is answered, the skill appends that exchange to a markdown file on disk — before moving to the next question. The conversation in the context window can drift all it wants. The *record* is immutable, sitting in a file, complete from question one.

I keep these in a `brainstorms/` folder at the project root. Every session produces one log, and each log carries three things:

- **Key decisions** — the resolved leaves of the design tree, stated as commitments. "Returns are handled by X, not Y, because Z."
- **The full Q&A log** — every question and my exact answer, in order, so I can reconstruct the reasoning later.
- **A highlight summary** — a short top-of-file digest I can skim in thirty seconds six weeks from now without rereading the whole transcript.

```
your-project/
├── brainstorms/
│   ├── 2026-06-05-packaging-process.md
│   ├── 2026-06-04-ai-safety-rollout.md
│   └── 2026-06-01-client-onboarding-flow.md
├── .claude/
│   └── skills/
│       └── grill-me/
│           └── SKILL.md
└── ...
```

<!-- IMAGE: Terminal showing a Grill Me session in progress — one question on screen with a recommended answer, and a brainstorms/ markdown checkpoint file open in a split pane being appended to. Alt text: "Grill Me skill Claude Code session checkpointing a question to a brainstorms markdown file". Caption: "Every answered question is written to disk before the next one is asked — drift in the chat can't corrupt the record." -->

There's a real, published cousin of this idea worth knowing about. The `grill-with-docs` skill takes the same relentless interview and writes resolved terms into a `CONTEXT.md` glossary and hard, surprising trade-offs into ADR files (`docs/adr/`) *as they crystallize*, inline, instead of batching them at the end. Same instinct as my `brainstorms/` folder — capture the moment a decision is made, not after the session when you're tired and the nuance has evaporated. Two roads to the same destination: the live conversation is fragile, the file on disk is not.

And once the file exists, something useful happens after the session ends.

## What happens after the interview ends

The capture isn't the finish line. It's the raw material.

When a session wraps, I have a structured `brainstorms/` log full of decisions and edge cases the model just pulled out of my head. The next move is to feed that back into the system: ask Claude to suggest updates to the related skill and its documentation based on the nuances it just captured. The packaging session didn't just produce notes — it produced a *better packaging skill*, because every "oh, except when..." I'd forgotten was now written down and could be folded into the instructions the skill runs on.

This is also where Grill Me does something I didn't expect: **it flags what you don't know.** Partway through the AI-safety session, it hit a branch I couldn't answer — something about a data-retention boundary that genuinely wasn't my call. Instead of guessing or letting me bluff, it marked it. "This needs input from whoever owns your compliance posture." That single flag was worth more than ten answers, because it told me exactly where to go ask a real human before I built something on a wrong assumption.

So the loop is: grill → capture to markdown → update the skill → flag the gaps → go fill the gaps with the right stakeholder → grill again next quarter when the process has changed. Each pass leaves you with a more battle-tested skill and an AIOS that knows more of your actual business. This is the supporting workflow under [the AI Operating System I built with Claude Code](https://www.mejba.me/blog/build-ai-operating-system-claude-code) — the pillar covers the architecture; Grill Me is how you fill it with knowledge that's genuinely yours.

If you want this kind of structured knowledge extraction running across an entire business — not just one process — that's the sort of thing [Ramlit builds for teams](https://www.ramlit.com/services): turning the expertise locked in your senior people's heads into systems the rest of the company (and its AI) can actually use.

Now to the number that made me reorganize how I start every skill.

## The iteration math: starting at 90% instead of 70%

Here's the case for spending an hour getting grilled before you write a line of skill instructions.

Build a skill the normal way — brain dump, write it, run it — and on the first try it lands somewhere around 70% effective. Good enough to be useful, wrong enough to need babysitting. Then you iterate. Each real-world use surfaces a gap, you patch it, and over many cycles it crawls toward 95%. The trajectory works. It's just *slow*, because every gap costs you a full build-run-discover loop to find.

Grill Me front-loads that discovery. Because the interview hunts edge cases *before* you build, the same skill starts its life around 90% instead of 70%. You're not finding the EU exception on day forty when a client gets a wrong output. You're finding it in minute twelve of the interview, when the model asks "what changes for EU customers?" and you go "oh — right."

The old carpenter's line fits exactly: spend four hours sharpening the axe before two hours chopping the tree. The upfront grind feels like a delay. It isn't. A sharp axe through a tree in two hours beats a dull one hacking for eight. An hour of grilling that takes you from 70% to 90% on iteration one saves you a dozen patch-and-rerun cycles downstream. I have never once regretted the hour. I have regretted every skill I shipped without it.

To be fair about the trade-off: this is real cognitive work, not a passive tool you fire and forget. A fifty-minute Grill Me session is fifty minutes of *thinking hard* about decisions you'd been handling on autopilot. If you're tired, it's draining. And for a genuinely trivial skill, full grilling is overkill — sometimes a brain dump really is enough. Match the depth of the interview to the stakes of the skill. The point isn't to grill everything. It's to grill the things that will hurt if you get them wrong.

So how do you actually run one?

## How to use the Grill Me skill in Claude Code

Two ways to trigger it, and they behave a little differently.

**Natural language.** Type something like *"Hey, grill me about how we run client onboarding"* or *"grill me on this deployment plan."* The skill's description matches the phrase and Claude fires it. This is how I start most sessions — it's conversational and you can aim it loosely.

**Slash command.** Run `/grill-me` directly. More deliberate, good when you know exactly the topic and want to skip the warm-up.

Either way, here's the flow I follow:

1. **Name the target precisely.** Not "grill me about my business." Too broad — you'll get a shallow pass over everything. Pick one process: "grill me about how I apply AI safely inside the business." Narrow target, deep interview.
2. **Answer honestly, including 'I don't know.'** The "I don't know" answers are the most valuable thing you'll produce, because they're the gaps the flagging will catch. Don't bluff to look competent in front of a model that's trying to help you.
3. **React to its recommended answers.** Remember, it proposes a default for every question. Often the fastest move is "yes, that, but change one thing." You're editing, not authoring.
4. **Let it run long.** Resist the urge to wrap at question ten because you're bored. The gold is usually in the back half, on the branches you didn't know existed. Sessions over an hour are normal and good.
5. **Harvest the output.** When it ends, you have discovery notes, a key-decisions summary, structured Q&A logs, and a list of flags marking where you need another human. Read the highlight summary. Then ask Claude to fold the new nuances into the relevant skill.

One honest caveat on setup: the bare upstream Grill Me doesn't checkpoint to a `brainstorms/` folder on its own — that's the enhancement layer I described, and you have to add it to the skill body (an instruction to append each answered question to a dated markdown file before continuing). The original keeps everything in the live conversation, which is exactly where the drift problem lives. If your sessions are short, the original is fine. If they run long, add the checkpoint instruction or you'll lose the first half.

That's the entire workflow. Six sentences of prompt, one folder, and a habit of letting the machine ask you the questions you'd never ask yourself.

The next time you sit down to build a skill and feel the pull to just dump everything you know into a file — stop. You don't know what you know. Let the machine find out. Open a session, say "grill me," and spend the uncomfortable hour. The version of your AIOS that comes out the other side will sound like *you* instead of like everyone else pointing the same model at the same problems. That voice — the one no base model can give you — is the only edge there is.

## Frequently Asked Questions

### What is the Grill Me skill in Claude Code?
Grill Me is a Claude Code skill that interviews you relentlessly about a plan, process, or design until your knowledge is fully captured. It walks down each branch of the decision tree one question at a time, proposing a recommended answer for each, and resolves codebase questions itself instead of asking. It was created by Matt Pocock. See [what the skill actually contains](#what-the-grill-me-skill-actually-contains) above for the full source.

### How do I trigger the Grill Me skill?
Say "grill me about [topic]" in Claude Code, or run the `/grill-me` slash command directly. The natural-language phrasing matches the skill's description and fires it automatically; the slash command is more deliberate when you already know the exact topic.

### How many questions does a Grill Me session ask?
A typical session runs roughly 16 to 50 questions, depending on how well-defined the topic is. Tight, familiar features land near the low end; an entire business operation lands at the high end and can run over an hour. Match the depth of the session to how costly it is to get the skill wrong.

### Why is Grill Me better than just writing a brain dump?
A brain dump only captures what you already know you know — it misses the tacit edge cases and forgotten decisions that make your AIOS unique. Grill Me's relentless interview surfaces those gaps before you build, which is why a grilled skill starts near 90% effectiveness instead of the ~70% a brain-dump skill typically starts at.

### Does Grill Me save the conversation to a file?
The original skill keeps everything in the live conversation, which can drift on long sessions. The enhanced workflow adds a checkpoint instruction that appends each answered question to a dated markdown file in a `brainstorms/` folder before continuing — preserving every decision even as the chat context grows. See [the checkpointing fix](#the-fix-that-made-long-grilling-sessions-usable-checkpointing) above.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
