**BRAND:** mejba.me
**TITLE:** I Built an LLM Wiki From My YouTube Transcripts
**META TITLE:** Build an LLM Wiki: Claude Code as Second-Brain Router
**SLUG:** llm-wiki-claude-code-router
**PRIMARY KEYWORD:** LLM wiki
**META DESCRIPTION:** I built an LLM wiki from YouTube transcripts and meeting recordings, then made Claude Code the router across every markdown wiki. Here's the full setup.
**TAGS:** Knowledge Management, AI Workflow, Claude Code, Obsidian, Build Log

---

# I Built an LLM Wiki From My YouTube Transcripts

Three words. That's all I typed into Claude Code last week: "summarize Fable pricing." I never said which folder to open, which file to read, or even which of my wikis held the answer. Claude Code went straight to the right page in the right vault, pulled two backlinked notes I'd forgotten I wrote, and handed back a clean summary with the source dates attached.

That's the moment an LLM wiki stops being a note-taking gimmick and starts behaving like a second brain with a filing clerk who never sleeps. An LLM wiki is just a folder of plain-markdown pages that an AI agent writes, cross-links, and reads on your behalf — no database, no embeddings, no lock-in. The trick that makes it actually scale isn't the notes. It's running several purpose-built wikis and letting Claude Code decide which one to open.

Most people building a second brain get the first half right and the second half wrong. They obsess over capture — the perfect template, the tagging scheme, the daily-note ritual — and end up with one enormous vault that their AI has to brute-force search every single time. I did that too. It worked until it didn't.

Here's the part nobody tells you: past a few hundred files, the bottleneck isn't writing notes. It's routing. And routing is exactly the thing an agent like Claude Code is good at, if you build the wiki so it can.

## What an LLM wiki actually is (and where Andrej Karpathy comes in)

The phrase comes from Andrej Karpathy, who [posted in mid-2026](https://x.com/karpathy/status/2039805659525644595) that a growing share of his token throughput was going into building personal knowledge bases instead of writing code. He published a short [`llm-wiki.md` gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) — an "idea file" you paste into a coding agent to bootstrap the whole system.

The mechanics are almost insultingly simple. You drop source documents into a `raw/` directory — PDFs, article URLs, transcripts, whatever. The agent reads them and incrementally *compiles* a `wiki/` directory: a set of Markdown files with summaries, backlinks, and articles categorized into concepts. That's it. No vector store. No RAG pipeline to babysit. The "index" is just more Markdown, and the "retrieval" is an agent opening files the way you would.

I'd already built the no-vector-database version of this — I broke down the mechanics in [my walkthrough of Karpathy's Obsidian RAG setup](/karpathy-obsidian-rag-knowledge-base), and it genuinely killed my embeddings pipeline. So when I say the plain-markdown approach works, I'm not repeating a tweet. I've been running it.

What that earlier build *didn't* solve was scale across domains. One wiki for everything is fine at 50 files. At 500, spread across content research, client meetings, and half-finished product ideas, a single vault turns into a swamp. Every query drags the agent through folders it has no business reading, burning tokens and context window on irrelevance.

The fix reframes the whole system. Stop building *a* second brain. Build several small LLM wikis, and put a router in front of them.

## The mental model: Claude Code as a router, not a search box

Picture a small company. You don't hand every question to one overworked generalist who has to read the entire filing cabinet before answering. You have a receptionist who knows which department owns what, and forwards you there. The department is small, focused, and fast because it only holds its own material.

That receptionist is Claude Code. Each department is a separate markdown wiki with its own shape and rules. When I ask a question, Claude Code doesn't grep 500 files. It reads a lightweight index, decides *this belongs to the content wiki, not the meetings wiki*, opens the two or three pages that matter, and answers. The rest of the system stays cold and cheap.

I call the whole thing an AI OS — an AIOS — because that's what it functions as: a personal operating system where markdown is the file system and Claude Code is the kernel deciding what gets loaded into memory. Right now mine runs two wikis:

- **The content wiki** — built from YouTube transcripts of the AI videos I study. Concepts like agentic workflows, tools like Vercel and GitHub, techniques, and the original sources, all backlinked.
- **The Herc brain** — built from meeting recordings. Raw transcripts of calls, decisions, follow-ups. Flatter, messier, searched constantly.

Two wikis, one router. When I'm drafting a post, Claude Code pulls from the content wiki. When I'm writing a follow-up email after a call, it pulls from the Herc brain. When I ask a question that spans both — "did we ever talk about the thing that video covered?" — it opens both and stitches them. That cross-domain stitch is where a routed system does things a single vault can't.

<!-- IMAGE: Diagram of Claude Code sitting between a user prompt and two markdown wikis (content wiki with nested folders, flat meeting wiki), showing an arrow routing the query to the correct vault. Alt text: "LLM wiki router diagram showing Claude Code directing a query to the correct markdown wiki". Caption: "The router pattern: Claude Code reads a lightweight index and opens only the wiki that owns the answer." Dimensions: "1600×900px". -->

The reason this matters more in 2026 than it would have a year ago is the models. Ingestion — turning a raw transcript into a set of cross-referenced pages — is genuinely hard reasoning work. It got good enough to trust right around the time Anthropic shipped [Claude Fable 5 on June 9, 2026](https://techcrunch.com/2026/06/09/anthropic-released-claude-fable-5-its-most-powerful-model-publicly-days-after-warning-ai-is-getting-too-dangerous/). More on which model to use for what in a minute, because the answer is not "always use the expensive one."

## How to build an LLM wiki: the actual setup

Here's the setup I run, start to finish. Building your first LLM wiki takes about fifteen minutes, and most of that is the agent working while you drink coffee.

**Step 1 — Install Obsidian and create a vault.** [Obsidian](https://obsidian.md/) stores everything as plain Markdown in a folder on your disk — no proprietary format, no cloud dependency, [no lock-in whatsoever](https://obsidian.md/help/data-storage). A "vault" is just that folder. Create one and name it something honest like `content-wiki`. Out of the box it contains almost nothing: an `.obsidian/` folder for app settings (hotkeys, themes, workspace layout) and a `welcome.md` file. That emptiness is the point — you're going to let the agent build the structure.

**Step 2 — Open the vault in VS Code.** Obsidian and VS Code both operate on the same folder of files, so they coexist happily. Obsidian gives you the graph view and backlink navigation; VS Code gives you the terminal. Point VS Code at the vault folder.

**Step 3 — Run Claude Code in the terminal.** [Claude Code lives in your terminal](https://github.com/anthropics/claude-code) and understands the folder it's launched in. There's now a [native VS Code extension](https://code.claude.com/docs/en/vs-code) with a side-by-side diff viewer and tabbed sessions, which is nicer than the raw CLI for this, but either works. Launch it inside the vault.

**Step 4 — Paste Karpathy's gist and ask for the scaffold.** Copy the contents of `llm-wiki.md` into Claude Code and prompt it to build a complete second brain from this idea file. Ask it explicitly to generate the schema: a `CLAUDE.md` with the rules, folder conventions, an index, and a log — plus one worked ingestion example so you can see the pattern. This is the step that separates a folder of notes from a *system*. The `CLAUDE.md` is the constitution; it tells every future session how this wiki is organized and how to add to it without making a mess.

What the agent produces for me, reliably, is a layout like this:

```
content-wiki/
├── .obsidian/          # Obsidian app metadata — leave it alone
├── CLAUDE.md           # rules, folder conventions, how to ingest
├── index.md            # structured map of everything in the wiki
├── log.md              # ingestion history: what, when, from which source
├── raw/                # original inputs — PDFs, transcripts, saved articles
└── wiki/               # processed, cross-referenced markdown
    ├── concepts/
    ├── tools/
    ├── techniques/
    └── sources/
```

The split between `raw/` and `wiki/` is the load-bearing decision. `raw/` is the archive — never edited, always the ground truth. `wiki/` is the compiled, human-and-agent-readable layer with all the backlinks. `log.md` is quietly the most useful file in the whole system, and I'll come back to why.

If you want the ingestion itself to be cheaper, or you're pulling transcripts from video, the sourcing step is its own small project. Getting clean, accurate transcripts instead of hallucinated ones is exactly the problem I solved in [my zero-token YouTube research stack](/claude-notebooklm-youtube-research) — pair that with this and the `raw/` folder fills itself with material you can actually trust.

## Why my two wikis have completely different shapes

This is the part most tutorials skip, and it's the part that decides whether your wiki is findable six months in. **Structure should follow purpose, not a template.** My two wikis look nothing alike, on purpose.

The **content wiki is deeply nested**. Concepts get their own pages. Tools get their own pages. Techniques, sources — all separated, all backlinked. When I study a video about agentic workflows, the ingestion doesn't dump one long note. It creates a `concepts/agentic-workflows.md`, links it to `tools/claude-code.md` and `sources/[video-title].md`, and threads the backlinks so that opening any one page surfaces its neighbors. That nesting pays off when I'm writing, because a single concept page becomes a launchpad into everything related I've ever captured.

The **Herc brain is almost flat**. Meeting recordings don't want nesting. They want raw files and fast retrieval. A call becomes a single dated markdown file, lightly cleaned, dropped in with minimal folder ceremony. Why flat? Because meeting content is queried by *recency and participant and decision*, not by concept hierarchy. When I ask "what did we agree on last Tuesday," a flat folder of dated files is faster for the agent to scan than a maze of subfolders. Over-structuring meeting notes actively hurts findability — you spend tokens navigating folders that add nothing.

The lesson took me two rebuilds to internalize: the *right* structure is whatever makes the agent's retrieval cheap for how you actually query that domain. Content is browsed by idea, so nest by idea. Meetings are searched by event, so keep them flat. You tune the folder shape and the ingestion rules the way you'd tune an index on a database — for the queries you actually run.

If you want to go deeper on the token economics of structure, I rebuilt an entire vault around exactly this and measured it in [the Infinite Brain knowledge-graph conversion](/infinite-brain-knowledge-graph-ai). Same principle: structure is a token-efficiency decision, not an aesthetic one.

## The ingestion run that showed me what this is really for

Theory is cheap. Here's an actual run.

I fed the content wiki two sources at once: the PDF of Claude Fable 5's system card, and the URL of an article previewing [OpenAI's GPT-5.6 Sol](https://openai.com/index/previewing-gpt-5-6-sol/) — the new flagship OpenAI put behind a limited preview in late June 2026. Two documents, two different formats, overlapping subject matter. I told Claude Code to ingest both into the wiki following the `CLAUDE.md` rules, and used Fable 5 as the ingestion model because this was dense, cross-referential reasoning where I wanted the strongest synthesis I could get.

In my run it took somewhere in the ten-to-twelve-minute range and produced around twenty fully cross-referenced wiki pages. (I'm giving you a range because I ran it once, live, and I'm not going to pretend I stopwatched it to the second — the honest number is "long enough to make a coffee, short enough that I didn't wander off.") The pages sorted themselves into the categories the schema defined: concepts, entities (Fable, Mythos, GPT-5.6, Opus 4.8), sources, and topics.

The payoff wasn't the page count. It was a connection I would have missed reading the two documents separately. The ingestion flagged a benchmarking nuance between the sources: on TerminalBench 2.1, [GPT-5.6 Sol scores 88.8% and Sol Ultra 91.9%, against Claude Mythos 5's 88.0%](https://www.edenai.co/post/gpt-5-6-sol-benchmarks-pricing-api-access-guide) — so the base Sol edges Mythos by less than a point, while the real separation only shows up at the Ultra tier. Read the OpenAI preview alone and you'd walk away thinking "Sol beats Mythos." Read the system card alone and you'd miss the comparison entirely. The wiki, holding both and cross-linking the entities, surfaced the *actual* gap: it's a rounding error at the base tier and a real jump at the top.

That's the thing a routed, cross-linked LLM wiki does that a chat window can't. It holds multiple sources in a persistent structure and notices where they disagree. You get the subtle nuance, not the headline.

And every one of those ingestions wrote a line to `log.md`: what was ingested, from which source, when. That log is what makes the wiki *incremental*. Next month, when a new Fable pricing article comes out, Claude Code reads the log, sees what it already knows, and only processes the delta — instead of rebuilding the whole entity from scratch. It's also an audit trail. When a wiki page makes a claim, I can trace it back to the exact source and date it came from. For anything I'm going to publish, that traceability is not optional.

## Which model should do the ingesting?

Short answer: not always the expensive one.

Fable 5 is the strongest ingestion model I've used for dense, multi-source synthesis — but it costs [$10 per million input tokens and $50 per million output](https://www.anthropic.com/claude/fable), double Opus 4.8, the most expensive generally available Anthropic model before it. Ingestion is output-heavy by nature; you're generating twenty pages of prose. That adds up fast if you point it at everything.

My rule after a few weeks: use Opus 4.8 for routine ingestion — daily meeting notes, straightforward single-source articles — and save Fable 5 for the runs where cross-source reasoning is the whole point, like the Fable-vs-Sol comparison above. For a lot of use cases Opus is not just "good enough," it's the correct call, and reaching for Fable is just lighting money on fire for a paragraph you'll skim.

I did the full brutal cost math on running an entire AIOS against Fable in [my breakdown of the second brain on the pricey model](/claude-fable-aios-second-brain-four-cs). If you're going to run this daily, read that before you set Fable as your default. The delegation math changes what you build.

If you'd rather have someone architect a routed knowledge system like this for your own stack — the wikis, the ingestion rules, the `CLAUDE.md` conventions — that's the kind of build I take on directly. You can see my work at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Where this breaks, and the mistakes I made first

I'm not going to sell you a system I haven't watched fall over.

**My first HTML interface was worse than no interface.** Early on I had Opus 4.8 generate a browsable HTML front end over the same backend markdown, and it was confusing — over-built, hard to follow, more friction than just reading the files. The AI-generated interface only became genuinely approachable once I rebuilt it to be simpler and got out of the model's way. If you're doing this for a non-technical person, the interface matters as much as the ingestion, and the instinct to make it fancy is exactly wrong. Plain and legible wins.

**Bad ingestion rules produce a beautiful, useless wiki.** My first `CLAUDE.md` let the agent create a new concept page for every minor mention. Within a week the content wiki had a `concepts/` folder full of near-duplicate stubs — `agentic-workflow.md` next to `agentic-workflows.md` next to `agent-workflows.md`. The router got slower because the index got noisy. The fix was a stricter rule: before creating a concept page, check the index for an existing one and extend it instead. You tune ingestion rules the way you'd refactor code — the first version is always too permissive.

**A single mega-vault is the trap you'll fall into first.** Everyone starts with one wiki because it's simpler. It *is* simpler, right up until the router has to reason about which of your 500 files is relevant and starts making expensive, wrong guesses. Splitting into purpose-built wikis felt like overkill until the day it obviously wasn't. If you're past a couple hundred files in one vault and retrieval feels sluggish, that's your signal.

**Flat isn't always right, and nested isn't always right.** I over-nested the meeting wiki at first and it got slower to search. I under-structured the content wiki at first and lost the backlink payoff. Both were wrong for the same reason: I imposed a shape instead of matching the shape to how I query. Watch your own retrieval patterns for a week before you commit to a structure.

## The payoff nobody expects: aggregated context makes better calls

Here's where a second brain stops being a memory aid and starts being a decision tool.

Because the wikis hold time-stamped material — meeting decisions, content I studied, ideas as they evolved — I can ask Claude Code to synthesize *across time*. I asked it to build a six-month story from everything in both wikis: how my focus shifted, what themes kept recurring, where the business actually moved.

What came back was a narrative I hadn't consciously assembled. The through-line was a pivot toward cloud-and-code-agent content — visible in the wikis as the topics I kept ingesting and the decisions I kept revisiting. It could sketch the trend lines: where audience attention grew, where a content bet paid off, where something I'd tried quietly churned. I want to be straight with you here — I'm not going to quote you a subscriber count or a revenue figure, because the honest version is a *direction*, not a dashboard, and inventing precise numbers would be exactly the kind of fake-data nonsense I refuse to publish. But the directional read was real, and it was sharper than my own memory, because it was built from what I actually recorded rather than what I happened to remember.

That's the mechanism worth internalizing: **aggregated context sharpens the decision.** An agent reasoning over six months of your real inputs makes better suggestions than one reasoning over the last three prompts. The wiki isn't storing information for later. It's compounding into judgment.

## Plain markdown is the whole point: portability and scale

Everything above is just Markdown files in folders. That's not a limitation — it's the feature that makes the system outlast any single tool.

Because there's no proprietary database, no agent owns your brain. Claude Code is my router today, but the wikis are readable by anything that can open a text file. Nous Research's Hermes agent ships a [bundled `research-llm-wiki` skill](https://hermes-agent.nousresearch.com/docs/user-guide/skills/bundled/research/research-llm-wiki) that builds and queries this exact interlinked-markdown pattern. Codex reads it. OpenCode reads it. If a better agent ships next quarter, I point it at the same folders and lose nothing. Karpathy's gist even calls this out — the idea file is written to be pasted into *any* capable agent, not one vendor's.

That portability changes the risk calculus of building a second brain at all. You're not betting your knowledge on a startup surviving. You're writing text files you'll be able to read in twenty years.

It also lets you go modular. Personal wikis and business wikis can sit side by side under one AIOS, each with its own shape and rules, all routed by the same agent. Which raises the real strategic question: what *scale* of second brain do you actually need? A solo builder wants a couple of tight, personal wikis. A team wants something else entirely — shared, governed, multi-user, with the structure and access rules that implies. Those are genuinely different systems, and building the org-scale version is its own discipline. When a knowledge system has to serve a whole company instead of one person, that's the kind of build [Ramlit takes on for teams](https://www.ramlit.com/services). Don't build a company brain the way you'd build a personal one; the scale changes the architecture.

## Start with one wiki and one source

Go back to those three words — "summarize Fable pricing" — and the reason Claude Code knew where to look. It wasn't magic, and it wasn't a bigger model. It was structure: two small wikis, a clear index, a router that reads the map before it reads the territory. That's the entire secret. The intelligence was in the filing, not the search.

So here's your next twenty-four hours. Install Obsidian, make one vault, open it in VS Code, run Claude Code, and paste Karpathy's `llm-wiki.md` gist with a prompt to scaffold a `CLAUDE.md`, an index, and a log. Then feed it exactly one source — one transcript, one PDF, one saved article — and watch it compile the first few cross-linked pages. Don't build the AIOS on day one. Build one wiki, ingest one thing, and read what comes out.

You'll know your LLM wiki works the first time you ask a question and the answer comes back from a page you forgot you owned. That's your second brain waking up. Everything after that is just adding rooms.

## Frequently Asked Questions

### What is an LLM wiki?
An LLM wiki is a knowledge base made of plain-Markdown files that an AI agent writes, cross-links, and reads for you — no vector database or embeddings required. You drop sources into a `raw/` folder, and the agent compiles a backlinked `wiki/` folder of summaries and concept pages. It's Andrej Karpathy's pattern; see the setup section above for the full build.

### Do I need a vector database to build a second brain?
No. The entire point of the LLM wiki approach is that plain Markdown replaces the vector store. The agent retrieves by opening files the way a person would, guided by an index, instead of querying embeddings. I broke down the no-vector-database version in [my Karpathy Obsidian RAG walkthrough](/karpathy-obsidian-rag-knowledge-base).

### Should I use Claude Fable 5 or Opus 4.8 for ingestion?
Use Opus 4.8 for routine, single-source ingestion and reserve Fable 5 for dense multi-source synthesis where cross-referencing is the goal. Fable 5 costs $10/$50 per million input/output tokens — double Opus — so defaulting to it on every note wastes money. Match the model to the reasoning difficulty of the source.

### How does Claude Code decide which wiki to search?
It reads a lightweight `index.md` before touching content, uses that map to identify which purpose-built wiki owns the answer, then opens only the two or three relevant pages. That routing avoids brute-force searching every file, which keeps token usage low and answers fast — the core reason a multi-wiki system beats one giant vault.

### Can other AI agents read the same wiki?
Yes. Because everything is plain Markdown with no proprietary format, any agent that reads files can use it — Claude Code, Codex, OpenCode, and Nous Research's Hermes all support the interlinked-markdown wiki pattern. Your knowledge isn't locked to one vendor, which is the main reason to build it this way.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
