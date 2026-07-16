**BRAND:** mejba.me
**TITLE:** Google's Open Knowledge Format: A Builder's First Look
**META TITLE:** Open Knowledge Format (OKF): What It Is & How to Use It
**SLUG:** google-open-knowledge-format-okf-explained
**PRIMARY KEYWORD:** Open Knowledge Format
**META DESCRIPTION:** Google shipped OKF v0.1 — knowledge as markdown bundles for AI agents. I built a test bundle to see what it actually is, and what it isn't yet.
**TAGS:** AI Agents, Knowledge Management, Markdown, GEO, Guide

---

# Google's Open Knowledge Format: A Builder's First Look at OKF

I almost ignored it. Another spec, another acronym, another "vendor-neutral standard" press release landing in my feed on a Friday. Google Cloud published the **Open Knowledge Format** — OKF v0.1 — on June 12, 2026, and my honest first reaction was a shrug. We've watched a dozen "this changes everything" formats arrive and quietly die.

Then I opened the spec. And I realized the entire thing is just markdown files with a few lines of YAML at the top, grouped into a folder. No SDK. No runtime. No compression scheme. No API to call. A "knowledge bundle" in the Open Knowledge Format is, almost insultingly, just files you could write in a text editor.

That's the part that made me stop scrolling and start a test folder. Because I've spent the last year building agent workflows, and the one thing I keep hitting is the same wall: my agents are brilliant at reasoning and terrible at remembering. They re-scrape, re-summarize, re-derive the same context every session. If a format this dumb-simple could give an agent a clean, structured place to *read knowledge from* — instead of re-deriving it from raw HTML every time — that's worth an afternoon.

So I spent that afternoon. I built a small bundle by hand from the published spec, ran my own site through a community generator, and opened the result in Obsidian to see whether the "human-friendly" claim survives contact with reality. This is what I found — what OKF actually is, the parts the announcements get right, and the much larger pile of things that are forward-looking promises rather than shipped reality. I'll be clear about which is which, because the gap between them is where most of the hype lives.

## What Is the Open Knowledge Format, Actually?

The Open Knowledge Format is an open, vendor-neutral specification from Google Cloud for storing knowledge as a directory of markdown files, each carrying YAML front matter, designed to be read by both humans and AI agents. That's the whole thing. One sentence covers it.

Strip away the announcement language and OKF makes exactly three structural decisions:

- **Knowledge is a folder of markdown files.** Google calls a folder a *knowledge bundle*. It's a directory — possibly with subdirectories — full of `.md` files.
- **Each file is one concept.** Not a web page. Not a whole document. One discrete unit of knowledge: a playbook, a metric definition, a runbook, an API description, a single entity. Karpathy's instinct that knowledge should be atomized into concept pages rather than dumped as long documents is baked straight into the format.
- **Every concept declares a `type`.** The spec requires exactly one front-matter field in every file: `type`. Everything else — `title`, `description`, `resource`, `tags`, `timestamp` — is recommended but optional.

Here's a concept file I wrote by hand to test the format, modeling one of my own internal playbooks:

```markdown
---
type: playbook
title: Client Onboarding Sequence
description: The exact steps I run when a new AI automation client signs.
tags: [onboarding, process, clients]
resource: https://mejba.me/onboarding
timestamp: 2026-06-18
---

# Client Onboarding Sequence

When a new client signs, I run these steps in order. Each one has a
trigger and a definition of done — agents reading this should treat the
"done" condition as the success check.

## 1. Access provisioning
Grant repo + cloud read access within 24h of signature...

## 2. Context capture call
A 45-minute recorded call. The recording becomes its own concept file...
```

That is a complete, spec-valid OKF concept. No tooling produced it. I typed it. And here's the quietly important part: it renders cleanly in any markdown app I threw it at — GitHub previewed it, Obsidian indexed the front matter as properties, and it would drop into Notion without a fight. The format isn't asking you to adopt anything. It's describing what you're probably already doing, just with one agreed-upon shape.

But a single file isn't the interesting unit. The bundle is. So let me show you how a bundle is actually wired together — because this is where OKF stops being "a markdown file" and starts being a system.

## How a Knowledge Bundle Is Structured

A knowledge bundle is a directory of concepts, and the spec gives it two optional-but-powerful organizing files that turn a flat folder into something an agent can navigate intelligently.

The structure looks like this:

```
my-knowledge-bundle/
├── index.md          # overview + directory listing (optional)
├── log.md            # chronological change history (optional)
├── onboarding.md     # a concept at bundle root
├── pricing.md        # another concept
└── playbooks/        # a subdirectory groups related concepts
    ├── index.md      # subdirectories can have their own index
    ├── refunds.md
    └── escalation.md
```

The two special files are where the design gets clever.

**`index.md` is the bundle's map.** It enumerates what's in the directory so an agent can survey the bundle before reading anything. This is *progressive disclosure* — the exact same pattern that makes well-designed agent skills efficient. An agent reads the index, decides which three of forty concept files it actually needs, and loads only those. It doesn't pour the whole folder into context. If you've fought the token bloat that comes from dumping everything into the context window, you'll recognize why this matters — it's the same principle I wrote about in my breakdown of [why context management beats configuration for AI agents](https://www.mejba.me). The index is the spotlight; the concepts are what it points at.

**`log.md` is the bundle's memory of its own changes.** A chronological history of updates. Why does a knowledge format need a changelog? Because OKF assumes the knowledge is *alive* — that it gets revised, contradicted, and corrected over time. The log is how an agent (or a human) understands not just what the knowledge says today, but how it got here.

When I built my test bundle, the index file is what sold me. I wrote five concept files, then wrote an `index.md` that listed each with a one-line description. Then I pointed Claude at the folder and asked a question that only one concept could answer. It read the index first, opened exactly one file, and answered. It never touched the other four. That's the difference between handing an agent a library card catalog versus dumping every book on its desk.

Now — before this sounds too polished — let me be honest about where the simplicity becomes a limitation. There's no enforcement. Nothing stops you from writing forty concept files with no index, inconsistent `type` values, and a `log.md` that's a lie. The format is a convention, not a guarantee. Which brings me to the comparison everyone keeps making, and getting slightly wrong.

## OKF vs llms.txt vs schema.org: Where Does It Fit?

This is the question I had within ten minutes of reading the spec, and it's the one the announcements answer least clearly. We already have `llms.txt`. We already have schema.org. Do we need a third thing? The short answer: they sit at different layers, and OKF is the deepest one.

Here's how I'd map the stack for AI visibility in 2026:

| Layer | Format | What it does | Granularity |
|---|---|---|---|
| Discovery | `llms.txt` | Tells an agent what's important and where to find it | Site-level index |
| Understanding | schema.org (JSON-LD) | Disambiguates entities — who you are, what you sell | Page-level annotation |
| Content | **OKF** | Hands over the curated knowledge itself, as clean concepts | Concept-level documents |

Think of it like a restaurant. `llms.txt` is the menu telling the agent what's available. Schema.org is the allergen and ingredient labeling that removes ambiguity about each dish. OKF is the actual food, plated and ready to eat — the knowledge itself, handed over as a clean markdown concept instead of forcing the agent to reverse-engineer it from scraped HTML.

That framing matters because of a comparison the spec makes implicitly and I'll make explicitly: **OKF is what you reach for when schema.org runs out of room.** Schema.org is brilliant for the things it has types for — products, recipes, events, organizations. But the moment your knowledge is a nuanced playbook, a proprietary process, a hard-won metric definition, or a strategy that doesn't map to any `@type` in the vocabulary, schema.org has nothing for you. OKF doesn't constrain you to a predefined vocabulary. Your `type` can be `playbook`, `runbook`, `case-study`, `pricing-logic` — whatever your domain needs. That's the trade: schema.org gives you machine-validated rigor inside a fixed vocabulary; OKF gives you open-ended flexibility with almost no validation.

And the likely connective tissue between these layers is `llms.txt` again. The forward-looking expectation — and I want to flag this clearly as *expected, not shipped* — is that sites will signal the existence of an OKF bundle through an `llms.txt`-style pointer, the same way XML sitemaps and structured data arrived after `robots.txt`. As of v0.1, there's no formalized discovery protocol baked into OKF itself. That's a gap, and it's the kind of gap a v0.1 is supposed to have.

If you're building content systems and want this layer automated rather than hand-maintained, this is squarely the kind of pipeline I build — turning a site's knowledge into agent-readable structure is becoming its own discipline, and you can see how I approach it in my work on [automating SEO content workflows with Claude Code](https://www.mejba.me). But you don't need me to start. You can build your first bundle today, and you should, because reading a spec teaches you almost nothing compared to writing one bad bundle.

## How I Built My First OKF Bundle (And What Broke)

I took two paths on purpose: build one bundle by hand to understand the format, and run an existing site through a generator to see what automation produces. The contrast taught me more than either alone.

**Path one: by hand.** I created a folder, wrote five concept files pulling from real internal docs — my onboarding sequence, my pricing logic, two playbooks, and a glossary of terms I use with clients. The front matter was the only friction. Deciding the `type` for each concept took longer than writing the content, because the spec deliberately doesn't tell you what types to use. Is my pricing doc a `pricing`? A `policy`? A `reference`? The freedom is real, and so is the decision fatigue. My takeaway: pick your type vocabulary *first*, write it down as its own concept, and stick to it. Inconsistent types are the single fastest way to make a bundle that an agent navigates badly.

Then I wrote the `index.md` by hand and immediately understood why it's optional in the spec but mandatory in practice. Without it, a bundle is a pile. With it, it's a graph.

**Path two: a generator.** The community moved fast here. Suganthan Mohanadasan — an SEO who, notably, had his own site speaking a markdown-concept format *before* Google shipped OKF — built a free [OKF Bundle Generator](https://suganthan.com/okf-generator/) that takes a URL or sitemap and produces a downloadable bundle plus a map of your content. I ran a section of my own site through it.

The result was genuinely useful and genuinely limited at the same time, and the limitation is the whole lesson. The generator did the obvious thing well: each page became a clean markdown file with sensible front matter, cross-linked, no HTML cruft. But it produced **one concept per page** — and that's not actually what OKF is for. OKF's unit is the *concept*, not the *page*. A single long blog post of mine might contain four distinct concepts that, in a proper bundle, should be four separate files an agent can reference independently. The generator faithfully translated my page structure; it could not perform the harder act of *concept extraction* — reading a page and deciding it actually contains three ideas that deserve their own files.

That gap is the real opportunity, and I'll say it plainly: the valuable OKF tooling hasn't been built yet. Page-to-markdown converters are a solved, commoditized problem. The tools that will matter are the ones that read your messy, overlapping content and *decompose* it into clean, deduplicated concepts that mirror your actual business structure. Suganthan's term for what OKF enables — "semantic unbaking," breaking baked-together knowledge back into structured, interoperable elements — is exactly the part automation hasn't cracked. A language model is well suited to that extraction, but pointing one at "turn this site into concepts" naively still produces concept files that overlap and contradict. Doing it well is unsolved.

If you'd rather have someone build this concept-extraction pipeline for your business rather than wrestle it yourself, that's a project I take on — you can see the kind of systems I build at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD). But genuinely, build a five-file bundle by hand first. It'll take twenty minutes and it'll teach you what no tool can.

## The Karpathy Connection: Knowledge That Maintains Itself

OKF doesn't come from nowhere. It's the formalization of an idea Andrej Karpathy floated — what he called the **LLM Wiki** pattern — and understanding that origin tells you where this is actually heading.

Karpathy's argument cut against the grain of how we do retrieval today. The dominant pattern, RAG, works by chunking unstructured documents, embedding them, and pulling the nearest-matching chunks at query time. It's powerful, but it's fundamentally a *search over a static pile*. The pile doesn't learn. It doesn't reconcile. If two documents contradict each other, RAG happily retrieves both and lets the model sort it out.

Karpathy's LLM Wiki flips the model: instead of a static pile you search, you maintain a *living wiki* that the model itself builds and revises. New information doesn't just get appended — it gets *integrated*. The model updates the relevant entity page, revises a summary, and when fresh data contradicts an old claim, it reconciles the contradiction rather than storing both. The knowledge base is a dynamic, evolving thing, and the model does most of the upkeep. That last part is the unlock: the reason wikis don't usually scale is that humans have to maintain them. An agent that maintains its own wiki removes the bottleneck.

You can see OKF's `log.md` and concept-per-file design as the on-disk format for exactly this vision. A concept file is an entity page. The log is the revision history. The structure is deliberately simple enough that an agent can not only *read* it but *write* it — open a concept, revise a claim, append to the log, save. That's a living knowledge graph that a machine can actually keep current.

I've been chasing a homegrown version of this for a while using Obsidian as the store and Claude as the maintainer — I wrote up that whole experiment in my [Obsidian + Claude Code persistent memory setup](https://www.mejba.me) and a related deep-dive on [building a Karpathy-style RAG knowledge base in Obsidian](https://www.mejba.me). OKF's contribution isn't a new idea on top of that. It's a *shared shape*. The reason a standard matters here is interoperability: if my agent and your agent both speak OKF, my onboarding bundle could be read by your agent with zero translation. Which leads to the part that's either the most exciting or the most over-promised, depending on your skepticism.

## Agentic Search Optimization and Sellable Knowledge

Here's where the announcements get loud, so here's where I'll get careful. Two big claims travel with OKF, and they're both *plausible directions* rather than *current realities*. I'll separate the mechanism from the marketing.

**Claim one: OKF reframes SEO into "agentic search optimization."** The logic: as AI agents increasingly mediate between people and information, the goal shifts from being *findable* by a search crawler to being *usable* by an agent. Instead of optimizing a page so a human clicks it, you publish knowledge so an agent can read, cite, and act on it directly. When Google itself starts describing your content as "context to be served to agents," serving it in the shape Google is describing is a rational hedge.

I think the *direction* is real. The execution is unproven. There is, as of mid-2026, no confirmed mechanism by which publishing an OKF bundle improves your visibility in Google's agent-mediated surfaces. Google shipped a format; it has not shipped — or promised — a ranking benefit for using it. Treat anyone selling "OKF for rankings" with the same suspicion you'd treat anyone who sold you on a meta keyword tag. The honest play is: OKF makes your knowledge *cleaner and more usable for any agent that reads it*, and that's a defensible bet on its own merits regardless of whether Google ever rewards it.

**Claim two: people will package and sell OKF knowledge bundles.** A lawyer sells a bundle of curated legal playbooks. An accountant sells tax-strategy concepts. An SEO sells a bundle of ranking heuristics. A business buys these and mounts them into its own agents' context. Because a bundle is just a tarball of markdown, it's trivially shippable, hostable on any git repo, and license-able like any digital product.

This one I find more convincing, because the mechanism is sound — a bundle genuinely is a portable, self-contained unit of expertise, and there's real demand for curated context that agents can consume. But it's a *market that doesn't exist yet*, not a thing happening at scale today. The same friction that makes good bundles hard to generate (concept extraction, consistency, keeping the log honest) makes good *sellable* bundles even harder, because now quality is a product feature. I'd bet this market emerges. I would not bet on the timeline, and I'd be skeptical of the first wave of "expertise bundles" the way I'm skeptical of any first wave.

So where does that leave a builder right now? With one clear, low-risk move and a lot of patience for the rest.

## What I'd Actually Do With OKF Today

Let me make this concrete, because "experiment with it" is the kind of advice that sounds good and changes nothing. Here's the specific sequence I'd run, and what to expect from each step.

1. **Read the actual spec, not the recaps.** The [OKF SPEC.md on GitHub](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md) is short and readable in fifteen minutes. Every secondhand summary (including this one) loses fidelity. The source is the source.

2. **Build a five-file bundle by hand.** Pick a real chunk of your own knowledge — your processes, your product docs, your hard-won heuristics. Write five concept files, one `index.md`, and one `log.md`. Do not use a tool. The friction *is* the education. You'll learn more about your own knowledge structure in twenty minutes than a generator will ever tell you.

3. **Open it in the tools you already use.** Drop the folder into Obsidian, push it to a GitHub repo, paste a file into Notion. Confirm for yourself that "human-friendly" holds. This is the property that makes OKF safe to adopt: worst case, you've written some well-organized markdown, which has value with or without any agent.

4. **Point an agent at it and ask a question.** Give Claude or Gemini the folder and ask something only one concept can answer. Watch whether it uses the index to navigate. This is the moment OKF stops being abstract — when you see an agent survey the map and open exactly the right file, the design clicks.

5. **Write down your `type` vocabulary as its own concept.** This is the single highest-leverage habit. The spec's freedom on types is a double-edged sword; the bundles that age well are the ones with a deliberate, documented type system. Make that decision once, on purpose, and reference it.

What I would *not* do today: re-architect my entire content operation around OKF, pay for "OKF SEO" services, or treat v0.1 as a finished standard. Google itself called v0.1 a starting point, not a finished spec. Building on it is smart; betting your business on its current shape is not.

## Frequently Asked Questions

### What is the Open Knowledge Format (OKF)?

The Open Knowledge Format is an open, vendor-neutral specification from Google Cloud, published as v0.1 on June 12, 2026, for storing knowledge as a directory of markdown files with YAML front matter — readable by both humans and AI agents. Each file represents one concept and must declare a `type` field. For the full structure breakdown, see the bundle section above.

### How is OKF different from llms.txt?

OKF and llms.txt operate at different layers: llms.txt is a discovery file that points agents to your important resources, while OKF hands over the curated knowledge itself as concept-level markdown documents. llms.txt is the menu; OKF is the food. They're complementary, and OKF bundles will likely be signaled via an llms.txt-style pointer in the future.

### Is OKF an SEO ranking factor?

No — as of mid-2026, Google has not announced any ranking benefit for publishing OKF bundles. OKF makes your knowledge cleaner and more usable for AI agents that read it, which is a defensible reason to adopt it, but treat any claim that OKF directly improves rankings with skepticism. See the agentic search section above for the full caveat.

### What tools can I use to create OKF bundles?

You can write OKF bundles by hand in any text editor, since they're just markdown plus YAML. Community generators like Suganthan's OKF Bundle Generator can convert a site to a basic bundle, but they currently produce one concept per page rather than true concept extraction — the more valuable tooling for decomposing content into clean concepts hasn't been built yet.

### What is Karpathy's LLM Wiki and how does it relate to OKF?

The LLM Wiki is Andrej Karpathy's concept of a living knowledge base that an AI model builds and maintains itself — integrating new information, revising claims, and reconciling contradictions, rather than searching a static pile like traditional RAG. OKF is essentially the on-disk file format for that vision, with concept files as entity pages and a log file as revision history.

## The Real Reason I'm Glad I Didn't Ignore It

I went into this expecting another spec to file under "interesting, never used." I came out having changed how I store my own knowledge — not because OKF is finished, but because it pointed at something true.

The thing it gets right is humble: knowledge for agents should be *curated, atomized, and handed over*, not scraped, chunked, and reverse-engineered. That's correct whether or not Google's specific format wins. The bundle I built by hand will outlive any guess about OKF's adoption, because it's just well-structured markdown I now understand better than I did this morning.

So here's the one thing I'd actually ask you to do before this week ends: open a folder, write five concept files about something you know cold, add an index, and point an agent at it. Twenty minutes. Then make your own call about whether the future is a folder. Mine is that the format is almost certainly going to evolve past v0.1 — but the *shape* it's describing, knowledge as concepts an agent can read and maintain, is the direction everything is already moving. Better to learn the shape now than to scrape your way into it later.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
