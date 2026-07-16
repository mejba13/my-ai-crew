**BRAND:** mejba.me
**TITLE:** OKF Second Brain: I Converted My Claude Setup
**META TITLE:** OKF Second Brain: Converting a Claude Knowledge Base
**SLUG:** okf-second-brain-claude-conversion
**PRIMARY KEYWORD:** OKF second brain
**META DESCRIPTION:** I converted my messy Claude second brain into an OKF knowledge base — the structure, the conversion skill, the Claude.md tweak, and what got faster.
**TAGS:** AI Agents, Knowledge Management, Open Knowledge Format, Claude Code, Case Study

---

# OKF Second Brain: I Converted My Claude Setup and It Finally Stopped Forgetting

The moment I gave up on my old second brain came on a Tuesday, watching Claude create a file called `pricing-v2-FINAL.md` six folders deep — right next to the `pricing.md` it had written three weeks earlier and apparently forgotten existed.

Two files. Same knowledge. Contradicting each other. Both confidently "correct."

That's the rot that sets into every personal knowledge base built the way mine was: a sprawl of markdown notes held together by a heroic `Claude.md` file doing the work of an index, a librarian, and a memory all at once. It works beautifully at thirty files. It quietly falls apart at three hundred. My agent wasn't dumb — it just had no reliable way to know what already existed before it wrote something new. So it re-derived, re-summarized, and duplicated, session after session.

This is the exact problem Google's **Open Knowledge Format** was built to kill, and over one weekend in June 2026 I rebuilt my entire setup as an **OKF second brain** to find out whether the format actually fixes it or just renames the mess. I'm going to walk you through the real conversion — the folder surgery, the conversion skill I leaned on, the one line I added to `Claude.md` that changed everything, what broke, and the honest before-and-after. If you've got a markdown knowledge base that Claude treats like a junk drawer, this is the path out.

I'll assume you already know roughly what OKF is. If you don't, read my [builder's first look at the Open Knowledge Format](https://www.mejba.me/google-open-knowledge-format-okf-explained) first — it covers the spec from scratch. This piece is the part that comes after: *I already have a second brain. Now what?*

## Why My Claude Second Brain Hit a Wall

Let me describe the setup, because yours probably rhymes with it.

I'd been running a personal knowledge base for about a year — the kind I documented in my [Claude Code second brain build](https://www.mejba.me/claude-code-second-brain). Markdown files, nested folders by project and topic, and a fat `Claude.md` at the root telling Claude where things lived and how to behave. Client playbooks. Pricing logic. Code snippets. Meeting notes. Hard-won heuristics I never wanted to re-learn.

The system had four failure modes, and they got worse the bigger it grew.

**Claude couldn't tell if something already existed.** Without a machine-readable map, the only way for the agent to check "do I already have a note on refund policy?" was to grep filenames and skim folders. That's slow, token-hungry, and unreliable. So it frequently *didn't* check — and wrote a duplicate. That `pricing-v2-FINAL.md` moment was not a one-off.

**Search was a tax.** Every retrieval meant the agent fanning out across nested directories, opening files to see if they were relevant, and burning context on dead ends. The deeper the folder tree, the heavier the tax.

**Nothing was portable.** My second brain was bespoke to *my* workflow and *my* `Claude.md`. I couldn't hand it to a teammate, mount it into a different agent, or share a slice of it without the whole thing losing meaning. It was a private dialect, not a language.

**The `Claude.md` was load-bearing in a dangerous way.** All the structure lived in one file's prose. If that file drifted out of sync with the actual folders — and it always did — the agent navigated on a stale map.

None of these are exotic problems. They're what *every* unstandardized second brain hits past a certain size. The structure that felt like freedom at the start became the thing strangling it. I'd tried to fix it with better folder discipline and a longer `Claude.md`. That's treating a structural problem with willpower. It doesn't last.

What I actually needed was a *standard shape* — one the agent could rely on without me hand-holding every navigation. That's the bet OKF makes. Time to test it.

## What an OKF Second Brain Actually Looks Like

Here's the reframe that made the format click for me: OKF doesn't ask you to add tooling. It asks you to agree on a shape. And the shape is almost insultingly simple — markdown concept files with YAML front matter, grouped into a bundle, fronted by an `index.md` that acts as the map.

My old second brain organized by *project*. My OKF second brain organizes by *concept*, with a hard separation Karpathy's [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) hammered into me: keep raw source material apart from synthesized knowledge. Raw transcripts, scraped pages, and dumped notes live in `/raw`. The clean, agent-readable concepts live in the wiki itself. The agent reads `/raw`, extracts concepts, and writes structured pages — it never serves you the raw pile directly.

The new structure looks like this:

```
second-brain/
├── index.md              # the map: every concept, one line each
├── log.md                # append-only history of changes
├── raw/                  # source material, untouched
│   ├── call-2026-06-18.md
│   └── scraped-pricing-research.md
├── clients/
│   ├── index.md          # sub-map for this folder
│   ├── onboarding-sequence.md
│   └── escalation-playbook.md
└── business/
    ├── index.md
    ├── pricing-logic.md
    └── positioning.md
```

Every concept file carries front matter the agent reads *before* it opens the body:

```markdown
---
type: playbook
title: Client Onboarding Sequence
description: The exact steps I run when a new AI automation client signs.
tags: [onboarding, process, clients]
timestamp: 2026-06-18
---

# Client Onboarding Sequence

When a new client signs, I run these steps in order. Each step has a
trigger and a definition of done...
```

The `type` field is the only one OKF v0.1 strictly requires. Everything else — `title`, `description`, `tags`, `timestamp` — is recommended. But the `description` is the quiet hero here, and I'll show you why in the results section. It's what lets an agent decide whether to open a file without opening it.

The `index.md` is where a flat folder becomes a navigable graph:

```markdown
---
type: index
title: Second Brain — Root Index
---

# Second Brain Index

## clients/
- **onboarding-sequence** — steps run when a new client signs
- **escalation-playbook** — how to handle an unhappy client mid-project

## business/
- **pricing-logic** — how I price AI automation engagements
- **positioning** — who I serve and who I turn away
```

That one-line description per concept is the whole trick. The agent reads the index, sees thirty descriptions, and decides which two files it actually needs — then opens only those. It's progressive disclosure, the same principle that makes well-built Claude skills efficient. The index is the spotlight. The concepts are what it points at.

This is the structural difference between an OKF second brain and the pile I had before: the map is a *file*, not prose buried in `Claude.md`, and it lives next to the thing it describes. When I add a concept, I update its local index. The map and the territory stay in sync because they're neighbors.

That's the target. Now the migration.

## How I Converted My Second Brain to OKF

I took two passes on purpose, because I wanted to feel the format by hand before I let a tool touch it. This is the same lesson I keep relearning: reading a spec teaches you almost nothing compared to converting one folder badly.

**Pass one: the spine, by hand.** I picked my single messiest folder — client work — and rebuilt it manually. I created the `index.md`, then went file by file deciding the `type` and writing a one-line `description` for each. This was slow, and the slowness was the point. Deciding whether my pricing doc was a `pricing`, a `policy`, or a `reference` forced me to actually understand what each note *was*. Half my "duplicate" files turned out to be the same concept written twice from different angles. The conversion surfaced the duplication I'd been blind to.

My single biggest takeaway from pass one: **write your `type` vocabulary down as its own concept file before you convert anything.** OKF deliberately doesn't dictate your types, and that freedom turns into decision fatigue and inconsistency fast. I created `business/type-vocabulary.md` listing the eight types I'd allow — `playbook`, `runbook`, `reference`, `pricing`, `case-study`, `glossary`, `decision`, `index` — and refused to invent a ninth on the fly. Consistent types are the difference between a bundle an agent navigates well and one it stumbles through.

**Pass two: automation for the bulk.** Hand-converting three hundred files was never the plan. For the rest, I leaned on the [`okf-skills` Claude Code plugin](https://github.com/scaccogatto/okf-skills) — an open-source set of skills that teaches Claude Code to author, maintain, and validate OKF bundles, driven by the verbatim spec and backed by a conformance checker. That last part matters more than it sounds: it means the agent's output gets *checked* against the spec instead of being trusted blindly. I also looked at Suganthan Mohanadasan's [OKF Bundle Generator](https://suganthan.com/okf-generator/) for the URL-to-bundle path, but for a local markdown folder the Claude Code skill route fit better.

Here's the honest part: automation nailed the easy 80% and fumbled the valuable 20%. Page-to-markdown-with-front-matter is a solved problem — the skill added `type` and `description` fields to my existing notes cleanly and flagged spec violations. What it could *not* do reliably was the hard act: reading a 2,000-word note that secretly contained three distinct concepts and *splitting* it into three files. That decomposition — Suganthan calls it "semantic unbaking" — is the part still mostly on you. An LLM can attempt it, but pointed at "break this into concepts" naively it produces overlapping, contradicting files. I reviewed every split by hand.

**The one line that changed everything.** None of the structure matters if the agent doesn't *use* it. The unlock was a small, explicit instruction at the top of `Claude.md`:

```markdown
## Knowledge Base Protocol

This is an OKF bundle. Before answering any question or creating any file:

1. Read `index.md` at the relevant level FIRST.
2. Use file `description` fields to decide what to open — do NOT open
   files to check relevance.
3. Before creating a concept, check the index for an existing one.
   Update it instead of duplicating.
4. After any change, append a line to `log.md`.
```

That's it. Four rules. The format gives the agent a reliable map; this tells it to actually read the map first. Without these instructions, Claude treated the OKF bundle like any other folder and went back to grepping. With them, its behavior changed visibly — which is the whole results story.

If you'd rather have this conversion done *for* you — the type vocabulary designed, the decomposition done right, the `Claude.md` protocol tuned — that's exactly the kind of pipeline I build. You can see the work at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD). But genuinely: convert one folder by hand first. It'll take an afternoon and teach you what no tool will.

## What Broke During the Conversion

A build log without the failures is marketing, not teaching. Three things bit me.

**The `description` fields were lazy, and the agent inherited my laziness.** My first batch of descriptions were things like "notes on pricing" — useless. When the whole navigation strategy depends on the agent reading descriptions instead of files, a vague description silently breaks the system. The agent can't tell `pricing-logic` from `pricing-history` if both say "notes on pricing," so it opens both, and you're back to the token tax you were trying to escape. I had to rewrite every description to be *discriminating* — what makes this file different from its neighbors — not just *descriptive*. That rewrite took longer than the structural conversion.

**Stale links everywhere.** When you split and rename files, internal references rot. My old notes cross-referenced each other by filename, and half those names changed. OKF tolerates broken links by design — the spec treats links as untyped edges and shrugs at dead ones — which is forgiving but also means nothing warns you. I had to grep for orphaned references manually. A link checker is going on my someday-list.

**I over-atomized at first.** Drunk on the "one concept per file" rule, I shattered a coherent onboarding playbook into eleven micro-files: one per step. That's not minimalism, that's confetti. The agent now had to reassemble a single process from eleven fragments, which is *worse* than one well-structured file. Minimalism in OKF means one *coherent idea* per file, not one *sentence* per file. I recombined them back into a single `onboarding-sequence.md` with headed sections. The line between "atomic" and "shredded" is judgment, and I got it wrong before I got it right.

None of these are dealbreakers. They're the normal friction of imposing structure on a year of organic mess. But if you go in expecting a tool to handle them, you'll ship a bundle that looks conformant and navigates badly. The spec validates *shape*, not *quality*. Quality is still your job.

That covered, here's what I actually got for the weekend.

## The OKF Second Brain Results: What Actually Changed

I want to be careful here, because this is exactly where knowledge-base content gets dishonest with invented benchmarks. I did not run a controlled token-count study with clean error bars, so I'm not going to hand you a fake "73% fewer tokens" number. What I *can* give you is what changed in observable agent behavior, and the mechanism that explains it.

**The duplication stopped.** This was the whole reason I started. Because rule 3 of the protocol forces an index check before creation, and because the index is a real map the agent can scan cheaply, Claude now finds the existing concept and *updates* it instead of writing `thing-v2-FINAL.md`. In the weeks since converting, I haven't caught a single duplicate concept. That alone justified the weekend.

**The agent opens fewer files to answer.** Before, answering a question meant the agent skimming several files to find the relevant one. Now it reads `index.md`, picks the file whose `description` matches, and opens that one. I watched it answer a question that only one of forty concepts could address — it read the index, opened exactly that file, and never touched the other thirty-nine. The mechanism is simple and real: parsing a short YAML description is far cheaper than reading a full file body, so filtering on descriptions first means fewer wasted full-file reads. That's not a benchmark; it's how progressive disclosure works.

**It stopped re-deriving context.** The most satisfying change is the hardest to screenshot. With a stable, navigable knowledge base, the agent stops re-summarizing the same background every session because it can reliably *find* the synthesized version it wrote last time. The `/raw` vs wiki split reinforces this — finished thinking lives in the wiki and gets reused, instead of getting re-extracted from raw notes each time.

**It's finally portable.** My second brain is now a folder of standard markdown I could push to a Git repo, hand to another agent, or share a slice of, and it would still mean something to a stranger's setup. That wasn't true before. The format is the shared language that makes a private system legible to anyone — human or agent.

For the deeper "why does standardized structure beat a clever `Claude.md`" argument, I broke down the levels of agent memory in my [six levels of Claude Code memory systems](https://www.mejba.me/claude-code-memory-systems-six-levels) — OKF is essentially a clean, shareable implementation of the higher levels.

The honest summary: OKF didn't make my agent smarter. It made my agent's *environment* legible, and a legible environment is what stops a capable agent from acting dumb.

## Should You Convert Your Second Brain to OKF?

Direct answer: convert if your knowledge base is big enough that the agent loses track of what's in it — and skip it if you're under fifty files and everything still fits comfortably in the agent's head.

The conversion has real cost. You'll spend hours writing discriminating descriptions, designing a type vocabulary, and reviewing automated splits. That cost only pays off when your base is large enough that navigation has become the bottleneck. At thirty well-named files, a good `Claude.md` is genuinely fine and OKF is overkill. The duplication-and-search tax I hit is a *scale* problem.

Convert if any of these are true: your agent duplicates knowledge it already has, retrieval feels slow and token-heavy, you want to share or mount the base across tools, or you're already running a Karpathy-style living wiki and want it to speak a standard other tools can consume. If you're building that living-wiki pattern, my [Obsidian + Claude Code second brain](https://www.mejba.me/obsidian-claude-code-second-brain) walkthrough pairs naturally with OKF — Obsidian for the human graph view, OKF as the on-disk standard underneath.

Hold off if you're under fifty files, if your base is purely personal and will never be shared, or if you're tempted to convert because OKF is new and shiny rather than because you've hit a real wall. New is not a reason. A wall is.

One thing I would *not* do: treat OKF v0.1 as finished. Google called it a starting point, and it is — there's no formal discovery protocol baked in yet, no link validation, no enforcement of quality. Building on it is smart. Betting your entire operation on its current shape is not. Convert the part that hurts, learn the shape, and let the spec mature before you go all-in.

## Frequently Asked Questions

### What is an OKF second brain?

An OKF second brain is a personal knowledge base structured according to Google's Open Knowledge Format — a directory of markdown concept files with YAML front matter, fronted by an `index.md` map, that both you and an AI agent like Claude can read, navigate, and update. It replaces ad-hoc folders and a long `Claude.md` with a standardized, portable shape.

### How do I convert an existing Claude knowledge base to OKF?

Convert one folder by hand first to learn the format, define a fixed `type` vocabulary, then use a tool like the `okf-skills` Claude Code plugin to add front matter to the rest. The hardest step — splitting baked-together notes into clean single concepts — still needs human review. Finish by adding an index-first protocol to your `Claude.md`. See the conversion walkthrough above for the exact steps.

### Does OKF make Claude faster at searching my notes?

OKF reduces wasted file reads rather than making the model itself faster. Because the agent reads short YAML `description` fields in the index before opening any file, it filters to the relevant concept without skimming irrelevant ones — which lowers token use on retrieval. The gain scales with how large and messy your base was to begin with.

### Do I still need a Claude.md file with an OKF second brain?

Yes, but its job shrinks. Instead of holding all your structure in prose, `Claude.md` becomes a short protocol telling the agent to read `index.md` first, navigate by descriptions, check for duplicates before creating, and log changes. The structure lives in the bundle; `Claude.md` just tells the agent how to use it.

### Is OKF better than RAG for a second brain?

They solve different problems. RAG searches a static pile of embedded chunks and rebuilds context every query without accumulating knowledge. An OKF second brain is a curated, living set of concepts the agent reads and *updates* over time, so it compounds. For evolving personal knowledge that you revise, OKF's maintainable structure tends to fit better than pure vector RAG.

## The Folder That Stopped Forgetting

I started this whole experiment because of two contradicting pricing files. I'll end it there too, because the resolution is the point.

A few days after the conversion, I asked Claude to update my pricing logic for a new service tier. It read the root index, went straight to `business/pricing-logic.md`, opened that one file, revised it, and appended a line to `log.md`. No new file. No `v2-FINAL`. No contradiction. It found the thing it already knew and changed it, the way a person with a real memory would.

That's the entire promise of an OKF second brain, and it's smaller and more honest than the hype around the format suggests. OKF didn't give my agent intelligence. It gave it a map it could trust — and a capable agent with a trustworthy map stops behaving like an amnesiac. The format will evolve past v0.1, the tooling will get better, and the sellable-bundle marketplace people keep predicting may or may not arrive. None of that changes the move available to you today.

So here's the one thing worth doing before this week ends: take your single messiest knowledge folder — the one your agent keeps fumbling — and convert just that one to OKF by hand. Write the index. Write discriminating descriptions. Add the four-line protocol to `Claude.md`. Then ask your agent a question only one file can answer, and watch what it does. If it reads the map and opens exactly the right file, you'll understand in thirty seconds what took me a weekend to feel.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
