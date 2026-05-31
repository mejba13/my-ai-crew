**BRAND:** mejba.me
**TITLE:** Graphify Tested: A Knowledge Graph Index for Claude Code
**META TITLE:** Graphify Knowledge Graph for Claude Code: Honest Review
**SLUG:** graphify-knowledge-graph-codebase-claude-code
**PRIMARY KEYWORD:** Graphify knowledge graph for Claude Code
**META DESCRIPTION:** I tested Graphify on my own repo. Real install, real queries, real token math. Here's where the knowledge graph saves you tokens and where it doesn't.
**TAGS:** Claude Code, Knowledge Graph, Token Optimization, AI Tools, Tutorial

---

I almost dismissed Graphify as another `npx`-flavored toy.

The pitch sounded too clean: "point it at any folder, get a knowledge graph, query the graph instead of re-reading files, watch your token usage collapse." I have a healthy reflex about anything that promises an order-of-magnitude cost reduction in a single command. Most of the time, the math holds for the demo and falls apart on a real codebase.

Then I ran it on a project I'd been losing arguments with for a month — an agency repo with seven app modules, a tangle of shared services, and a `docs/` folder I'd been quietly avoiding. The first build took less than three minutes on my MacBook. The graph it spat out told me something about my own architecture I'd missed for half a year — a circular dependency between billing logic and a notification service that should never have known about each other.

That was the moment I stopped treating Graphify as a token-savings party trick and started treating it as a code review tool that happens to also save tokens.

This post is what I learned from running it on three real codebases — what it actually does, what the install really looks like, where the token math is honest, where it's marketing, and which workflows it changed for me. Honest take, not a press release. If you're a Claude Code user who keeps watching `/cost` climb every time you ask about an unfamiliar repo, the next twenty minutes might be the most useful thing you read this week.

## The Pain Graphify Actually Solves

Here's the workflow most of us are stuck in.

You open Claude Code inside a repo you don't know cold. You ask a question — "where does this user_id get validated?" or "which services touch the billing module?" or just "explain how this app is structured." Claude starts reading files. It reads the file you mentioned. Then the file that file imports. Then the file that imports the file that imports the file. Twenty tool calls later, you have an answer and a context window that's 70% full before you've written a single line of code.

This is the *token tax*. Every conversation pays it. Every time you start a new session, you pay it again. Every time you compact context, you pay it again. The codebase doesn't change between Tuesday and Thursday, but your agent re-reads it from scratch every time.

Semantic search — `grep`, ripgrep, vector embeddings — chips at the problem but doesn't solve it. `grep` finds strings, not concepts. Embeddings find paragraphs that *look* like your query, not the structural relationships your code actually has. Neither one captures the answer to "which functions does `RateLimiter` end up calling, three hops deep?" because that answer isn't in any single file. It lives in the *graph* between files.

Graphify's bet is that you should build that graph once, store it next to your repo, and let your agent query the graph instead of crawling the source every time. The graph is roughly two megabytes of JSON. Your source folder might be hundreds of megabytes. The agent reads the graph and reaches for raw files only when it actually needs to write or modify code.

If you've been doing AI-assisted research on big codebases, you already know why that bet is interesting. The rest of this post is whether it pays off in practice.

## The Core Idea: A Graph as an Index

Before the install walkthrough, let me make sure you have the mental model right. Most posts about Graphify skip this part and it's the part that determines whether the tool will be useful for *your* repo.

A knowledge graph is just two things: **nodes** (the entities — functions, classes, modules, doc sections, concepts) and **edges** (the relationships — `calls`, `imports`, `inherits from`, `depends on`, `references`, `is similar to`). Graphify builds both deterministically for code and semantically for docs.

For code, it uses tree-sitter to parse your source into ASTs and extracts the structural relationships without ever sending a token to an LLM. That part is fast, free, and exact. Tree-sitter knows that `function A` calls `function B` because the syntax says so — no inference required.

For docs, PDFs, markdown, and images, Graphify uses an LLM (your choice — Claude, GPT, Gemini, Kimi, DeepSeek, local Ollama) to extract entities and infer relationships. That part is slower and costs tokens upfront — once, during the build. After that, the graph persists. You pay the extraction cost the first time and amortize it across every query for weeks.

Then it runs **Leiden community detection** on the combined graph. Leiden is a clustering algorithm that groups densely-connected nodes into communities — basically asking "which parts of this graph hang out together?" The output is your repo's natural module structure, derived from how the code actually connects, not from how the folders happen to be organized. Sometimes those two structures agree. Sometimes they violently disagree, and that disagreement is the most interesting signal in the entire build.

When you query the graph, your agent walks edges instead of reading source files. "Show me the auth flow" becomes a graph traversal that returns maybe three thousand tokens of structured nodes and edges, instead of fifty thousand tokens of raw source code. That's the compression. That's where the savings live.

The catch — and we'll come back to this — is that graph queries are great for **reading** your codebase and terrible for **writing** to it. When you need to modify code, you still need the raw file. Graphify doesn't replace your source folder; it replaces your *exploration* of your source folder.

## Install: What It Really Looks Like in 2026

The repo is at [github.com/safishamsi/graphify](https://github.com/safishamsi/graphify). There's a quirk worth knowing about up front: the PyPI package name is `graphifyy` with a double-y while the original name gets reclaimed, but the CLI command is still `graphify`. The README is up-front about this — don't let it confuse you.

Graphify needs Python 3.10 or newer. If you're already on a modern stack you're fine. If you're still on 3.9, this is your nudge to upgrade.

I install Python tools with `uv` these days because it's the only Python tooling that doesn't make me want to write everything in Rust instead. The single command that gets you a working install is:

```bash
uv tool install graphifyy
```

That puts `graphify` on your `PATH` automatically — no venv juggling, no `pipx` ceremony. If you prefer `pipx`, that works too:

```bash
pipx install graphifyy
```

And if you're a glutton for punishment and want plain pip:

```bash
pip install graphifyy
```

…you'll need to make sure `~/.local/bin` (Linux) or `~/Library/Python/3.x/bin` (Mac) is on your `PATH`, or invoke it as `python -m graphify`. I'd recommend `uv` or `pipx` — there's no upside to fighting `PATH` for a CLI tool.

Verify it's there:

```bash
graphify --version
```

If you get a version string back, you're done with the install. Total time on my machine: under thirty seconds.

## Registering Graphify as a Claude Code Skill

This is the part that actually makes Graphify *useful* inside your agent. The CLI is fine. The skill registration is the moment Claude Code stops asking you to run `graphify` manually and starts using it itself when it needs to explore a repo.

Run:

```bash
graphify install
```

That single command does three things. It copies the platform-specific skill manifest into `~/.claude/skills/graphify/` so Claude Code can discover it. It updates (or creates) your project `CLAUDE.md` with instructions for the assistant to reach for `graphify query` before falling back to raw file reads. And it registers the slash commands — `/graphify query`, `/graphify path`, `/graphify explain` — that you'll invoke directly when you want to drive the queries yourself.

If you're using Codex, OpenCode, Cursor, Gemini CLI, or one of the other ten-plus assistants Graphify supports, the same `graphify install` command auto-detects most of them and writes the correct manifest into the right place. The README has a full matrix. If you're on Windows or you want to be explicit, you can pass `--platform claude` or `--platform codex` and force the target.

After running `graphify install`, the right sanity check is to open Claude Code, type `/` and look for the `graphify` commands in the picker. If they're there, the skill registered correctly. If they're not, check that `~/.claude/skills/graphify/` exists and has a `SKILL.md` inside it — that's the file Claude Code reads on startup.

This is the same skill-loading pattern I covered in [my guide to advanced Claude Code skills](https://www.mejba.me/blog/agent-skills-advanced-claude-code), and it's becoming the dominant integration pattern in the ecosystem. Skills, not MCP servers, are what most of the high-value tooling is shipping as in 2026.

## Building Your First Graph

Now the interesting part. From inside the repo you want to index, run:

```bash
graphify build
```

That's it. Default settings will get you a working graph. Graphify scans the folder, hands code files to tree-sitter for AST extraction, hands docs and other files to your configured LLM for semantic extraction, runs Leiden community detection, and writes the output to a `graphify-out/` directory.

But you'll almost always want to be more deliberate than the defaults. The flags I reach for most:

- **`--mode deep`** — runs a more aggressive semantic extraction pass over docs and comments. Costs more tokens up front, finds more conceptual edges (the ones that don't show up in the AST).
- **`--update`** — incremental rebuild. Only reprocesses files whose SHA256 changed since the last run. This is the flag you'll use 95% of the time after the initial build.
- **`--cluster-only`** — reruns Leiden community detection on the existing graph without re-extracting anything. Useful when you've added new manual edges or tuned clustering parameters and want to see the effect.
- **`--no-viz`** — skips the interactive HTML output. Faster builds, leaner output. Use when you only need `graph.json` for an agent and don't want to look at the visualization.
- **`--watch`** — keeps Graphify running and re-extracts changed files on save. I don't use this in normal development because I want a stable graph, but it's useful when you're actively reshaping the architecture and want to see clustering shift in real time.

For the agency repo I mentioned, the first build was a `graphify build --mode deep` and it took about two minutes forty seconds on a ten-thousand-file codebase. Token cost for the LLM-driven semantic extraction was modest — under a dollar of API spend at Sonnet pricing — because tree-sitter handled the bulk of the structural work without LLM involvement. Subsequent `graphify build --update` runs after small commits finished in under fifteen seconds.

A note on extraction scope. Graphify supports code-only, code+docs, and full-multimodal modes (the multimodal mode reaches into PDFs, images, and even video transcripts if you've installed the optional dependencies). For a typical app repo I keep it on code+docs. The full-multimodal mode is genuinely useful when your `docs/` folder has architecture diagrams as PNGs and you want the visual content extracted — but it costs tokens and time, so only flip it on when you actually have that kind of material.

## What the Output Really Is

When `graphify build` finishes, you'll have a `graphify-out/` directory with three primary files that matter:

**`graph.html`** — an interactive visualization you open in your browser. Nodes are sized by community membership and connectivity. Edges are colored by relationship type. You can filter by community, by file type, by relationship type, and you can click any node to see its neighbors and read the original source. This is the file you show your team in a meeting. It's also the file that surfaced my circular dependency — I could literally see the loop drawn on the screen.

**`graph.json`** — the machine-readable graph. This is what your agent reads when it answers questions. About two megabytes for a medium-sized repo. Structured nodes-and-edges JSON that any tool can parse. This is the file that does the actual token-saving work.

**`GRAPH_REPORT.md`** — a plain-English audit report. It lists your "god nodes" (the highly-connected files that touch everything — usually a sign you've got an architectural smell), unexpected links the LLM noticed that don't appear in any single file, and a set of suggested questions you might want to ask the graph. The first time I read one of these, it was like getting a senior engineer's onboarding notes on my own codebase.

There's also a `cache/` directory with SHA256 hashes per file. That's how `--update` knows which files actually changed — it's a content hash, not a timestamp, so renamed-but-unchanged files don't trigger re-extraction.

## A Real Query, with Real Output Shape

The cleanest way to show what queries feel like is to run one. From the CLI:

```bash
graphify query "what connects the auth layer to the database?"
```

Or, if you've registered the skill, the same thing inside Claude Code:

```
/graphify query "what connects the auth layer to the database?"
```

What comes back is a subgraph — a structured response listing the relevant nodes (the auth middleware, the user session service, the connection pool, the user repository) and the edges between them (which functions call which, which modules import which, which docs reference which concept). On a five-hundred-thousand-word corpus, a query like that typically returns a few thousand tokens, where reading the same files raw would have cost hundreds of thousands.

The two other commands worth memorizing:

```bash
graphify path "UserService" "DatabasePool"
```

That returns the **shortest path** between two named entities — the actual chain of function calls or module imports that connects them. This is the query that gives you impact analysis for free. "If I change `UserService`, which paths does that change propagate down?" Three commands, one answer.

And:

```bash
graphify explain "RateLimiter"
```

That returns a plain-language summary of a node — what it does, what calls it, what it calls, what concepts it's clustered with in the Leiden output. It's the "what is this thing" query. The first thing I run when I'm dropped into an unfamiliar repo.

There are richer query patterns — neighborhood expansion, community-scoped queries, semantic search across the docs subgraph — and the README walks through them. But these three (`query`, `path`, `explain`) are the ones I use daily.

## Incremental Updates: Keep the Graph Fresh

The freshness problem is the obvious objection: "my code changes every day, am I going to rebuild this thing every morning?"

No. You're going to run:

```bash
graphify build --update
```

…and it'll re-extract only the files whose content hash changed since the last build, merge them into the existing graph, and rerun clustering. On a ten-thousand-file repo with a handful of changed files, this takes seconds, not minutes.

Better than that, Graphify can install git hooks that trigger an incremental update automatically on commit or checkout. I have this enabled on my main work repo. Every time I push, the graph updates. Every time I switch branches, the graph reflects the new branch's structure. I never think about freshness anymore.

If you've been following my [token optimization series](https://www.mejba.me/blog/claude-code-token-optimization), you'll recognize this pattern: front-load the expensive work, cache it, and reach for the cache during interactive sessions. Graphify is just an unusually clean implementation of that idea.

## Extensions Worth Knowing About

A few extension flags push Graphify past "code intelligence tool" and into something more interesting.

**`--obsidian`** generates a full Obsidian vault from your graph. Every node becomes a markdown note. Every edge becomes a backlink. You open Obsidian, point it at the generated vault, and you have a navigable wiki of your codebase that updates every time you re-extract. This is the workflow that maps directly onto Karpathy's LLM Wiki idea — I went deep on that pattern in my [Karpathy Obsidian RAG breakdown](https://www.mejba.me/blog/karpathy-obsidian-rag-knowledge-base) and Graphify is the cleanest implementation of it I've used for codebases specifically.

**`--neo4j`** exports a `cypher.txt` file you can replay into a Neo4j instance. If you're building a RAG pipeline that needs proper graph traversal — multi-hop reasoning, weighted paths, real Cypher queries — this is your bridge. Build the graph with Graphify, persist it in Neo4j, query it from your application. I've used this exactly once, for a client who needed an enterprise-grade graph store, and it worked first try.

**`--mcp`** starts Graphify as an MCP stdio server. If you're running a setup where multiple agents need to share the same graph index, MCP is the right boundary — your graph becomes a service, and any MCP-aware client can query it. I covered the broader MCP-vs-skills tension in [my piece on whether MCP is dying out](https://www.mejba.me/blog/mcp-is-dead-corsair-rag-tools); Graphify's MCP mode is one of the cases where MCP still makes sense, because the graph genuinely is a shared resource.

The Obsidian export is the one that surprised me most. The graph becomes a *navigable artifact* — something you read, something you link to, something you commit to a wiki repo. It stops being a token-optimization tactic and starts being documentation that maintains itself.

## The Honest Token Math

This is the part where most write-ups go quiet. Let me be specific.

The big number floating around the marketing material is "71.5x fewer tokens per query." That number is real — it comes from a benchmark run on a mixed corpus that includes PDFs, images, and video transcripts. Multimodal corpora inflate the raw-baseline token count dramatically (you're not reading PDFs cheaply; you're paying to convert and reconvert them). The compression Graphify provides on that kind of corpus is genuinely enormous.

On a pure-code repo — which is what most of you actually have — the realistic compression is closer to **5x to 10x** for typical "explore this codebase" queries. That's still substantial. A query that would have eaten fifty thousand tokens of raw file reads might eat five to ten thousand against the graph. Over the course of a working day, that's real money. Over a working month on a paid API plan, it's the difference between staying on Sonnet for everything and constantly worrying about Opus costs.

But the savings aren't uniform. Here's where Graphify actually wins and where it doesn't.

**Where it wins big:** Onboarding into a repo you've never seen. Impact analysis on a refactor. Cross-cutting questions like "every place we hit Stripe." Architecture review. Anything where you're trying to understand structural relationships rather than read specific code.

**Where it doesn't help:** Writing new code. Modifying existing functions. Debugging a specific error message. Anything where you need the actual source content, not the structural summary. For those workflows, your agent still needs to read raw files — the graph tells it *which* files to read, but doesn't replace the read itself.

This is the part I want to be loud about because I see people overselling it. Graphify is not a replacement for your codebase. It's an *index* over your codebase. Indexes are amazing for lookup. They are not the thing you edit. Treat it accordingly and you'll get a lot of value. Treat it as a silver bullet and you'll be confused when your refactor session still burns tokens.

## What Changed in My Workflow

After three weeks of running it on my own repos and two client codebases, here's where Graphify ended up in my actual toolbelt.

**Onboarding into a new repo.** First command on a fresh clone is now `graphify build --mode deep`. Within three minutes I have a graph and a `GRAPH_REPORT.md`. I read the report. I open the HTML. I ask my agent `/graphify explain` for the three most-connected nodes. By the end of fifteen minutes I have a better mental model than I'd get from an hour of grep-driven exploration. This single workflow has paid for the time I spent learning Graphify already.

**Cross-repo audits.** When a client asks "is this thing actually using the library we're paying for?" — I run Graphify, query the graph for the library's main entry points, and read the answer. Previously this was a forty-minute manual code review. Now it's five minutes.

**Pre-refactor impact analysis.** Before I touch a function I expect to be load-bearing, I run `graphify path "<function-name>" "<distant-thing>"` to see what depends on what. The shortest-path output catches the dependencies I would have missed.

**Documentation freshness.** I generate the Obsidian vault from the graph and commit it. The docs are now a derivative of the code, not a separate artifact that drifts. When the code changes, the vault changes. When I open Obsidian to write a new article, I can backlink straight into the codebase nodes I'm describing.

**Where I still reach for grep.** When I know exactly what I'm looking for — a specific string, an error message, a config key — grep is still faster than any graph query. Graphify earns its keep on questions where I don't yet know the right grep. It's the *what should I be searching for* tool, not the *I already know what I'm searching for* tool.

**Where I still reach for a sub-agent.** When the question is open-ended and creative — "is there a better architecture for this?" — a planning sub-agent with a long context window still wins. Graphify gives the sub-agent better starting context (smaller, denser, structurally aware), but the reasoning still has to happen at the model level.

This combination — graph for structure, grep for strings, sub-agents for reasoning — has compounded in a way I didn't expect. They're not competing tools. They're a stack. The graph tells the sub-agent which files matter. Grep tells the agent where in those files to look. The agent does the thinking. Each layer feeds the next.

If you want the broader pattern around stacking tools like this, my [Claude Code skills stack walkthrough](https://www.mejba.me/blog/claude-code-skills-worth-installing) covers the meta-architecture I'm now defaulting to.

## Where Graphify Breaks Down

I owe you the honest list of failure modes, because the ones I've hit are not theoretical.

**Very small repos don't need it.** If your codebase fits in Claude's context window comfortably, building a graph is overkill. The break-even point in my testing was somewhere around twenty-thousand lines of code or fifty-plus markdown documents. Smaller than that and the build cost outweighs the query savings.

**Languages with weak tree-sitter coverage.** The structural extraction is only as good as the tree-sitter grammar for your language. Mainstream stuff — Python, TypeScript, JavaScript, Go, Rust, Java, PHP — is excellent. More exotic languages can produce thinner graphs, with fewer call edges captured. Test with your stack before relying on it.

**Doc-heavy repos with unstructured prose.** The LLM extraction for docs is sensitive to how the prose is structured. Clean headings, clear entity names, and consistent terminology produce great graphs. Stream-of-consciousness wiki dumps produce noisier ones. This is fixable — restructure the docs, run again — but it's not magic.

**Token costs on the initial build for very large multimodal corpora.** If you point Graphify at a fifty-gigabyte folder of PDFs and videos, the first build will not be free. It'll be one-time, but it won't be free. Plan for that. For pure-code repos, this isn't an issue.

**Semantic precision is model-dependent.** The quality of the LLM-driven edges depends on which model you've configured. Claude Sonnet, GPT, and Gemini all produce solid results in my testing. Smaller local models produce thinner, noisier graphs. There are no published precision-recall benchmarks for the semantic extraction — treat the inferred edges as suggestions, not ground truth.

None of these are deal-breakers. All of them are worth knowing before you bet a workflow on the tool.

## Should You Install It?

Here's my decision tree, distilled from three weeks of daily use.

**Install Graphify today if:** You're a Claude Code, Codex, or Cursor user. You work on codebases bigger than ten-thousand lines. You frequently get dropped into unfamiliar repos. You watch your token usage climb during exploration sessions. You maintain a `docs/` folder that you'd like an LLM to actually read.

**Wait a few releases if:** You only work on tiny projects. Your stack uses a niche language with poor tree-sitter coverage. You don't have a local Python 3.10+ environment and don't want to set one up.

**Don't install it if:** You're looking for a magic refactor tool. Graphify doesn't write code. It doesn't fix your architecture. It tells you what your architecture *is* — what you do with that information is still on you.

For me, this is the cleanest implementation of Karpathy's "the LLM is the programmer, the wiki is the codebase" idea I've used on actual code (not just notes). It pairs naturally with the way I already use Claude Code, it survives daily use without breaking, and the maintainer is shipping releases fast enough that the warts I'd flag this week might be gone next week.

The graph for the repo I started this post with is now committed alongside the source. Every PR rebuilds it. Every onboarding doc references it. The "what does this codebase even look like" question that used to take an hour now takes ninety seconds. That's the workflow shift, and it's the reason I'm writing about Graphify instead of moving on to the next shiny thing in my feed.

If you only do one thing after reading this: open a terminal, run `uv tool install graphifyy`, then `graphify install`, then `graphify build` inside whichever repo you've been quietly avoiding. The graph it builds will probably surprise you — and possibly embarrass you, in the most useful way. That's the point.

## Frequently Asked Questions

### What does Graphify actually do?
Graphify converts a folder of code, docs, and other files into a queryable knowledge graph so your AI assistant can answer questions about the structure without re-reading raw source files. It uses tree-sitter for deterministic code parsing and an LLM for semantic extraction over docs, then clusters the result with Leiden community detection. The output is a `graph.json` your agent queries instead of crawling your codebase.

### How do you install Graphify on Claude Code?
Run `uv tool install graphifyy` (or `pipx install graphifyy`) to install the CLI, then `graphify install` to register it as a Claude Code skill. The install command writes the skill manifest into `~/.claude/skills/graphify/` and updates `CLAUDE.md` so Claude Code reaches for the graph before crawling raw files. Total install time is under a minute on a modern machine.

### Is the 70x token reduction real?
The 71.5x figure is real but corpus-dependent — it comes from a mixed multimodal benchmark with PDFs and images inflating the raw baseline. On pure-code repos, expect roughly 5x to 10x token compression on structural queries. That's still substantial, but it's not the headline number. The savings concentrate in exploration and onboarding workflows, not in code writing.

### Does Graphify replace grep or vector search?
No — it complements them. Graphify wins on structural questions ("what calls what", "shortest path between two modules"). Grep still wins when you know the exact string you're looking for. Vector search still wins for fuzzy semantic similarity over long-form text. The strongest setup stacks all three, with the graph as the structural index layer.

### Can the graph stay fresh as code changes?
Yes. Run `graphify build --update` to re-extract only changed files (detected via SHA256 hashes) and merge into the existing graph. You can also install git hooks that trigger incremental updates on commit or checkout, so the graph stays in sync with your repo without manual intervention.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
