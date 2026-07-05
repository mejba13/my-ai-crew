**BRAND:** mejba.me
**TITLE:** 17 Claude Code Plugins and Skills I Actually Use
**META TITLE:** 17 Claude Code Plugins and Skills That Earn Their Spot
**SLUG:** claude-code-plugins-skills-clis
**PRIMARY KEYWORD:** Claude Code plugins and skills
**META DESCRIPTION:** 17 Claude Code plugins and skills across design, productivity, and research — with the install commands, real benchmarks, and the ones I kept.
**TAGS:** Claude Code, AI Development, Claude Code Skills, Developer Tools, Roundup

---

My `.claude` folder got fat before I noticed. One Sunday I ran a count and there were 31 skills, plugins, and CLIs installed — and I genuinely could not remember what nine of them did. So I did the unglamorous thing: I uninstalled everything and rebuilt the stack from zero, only re-adding a tool after it earned its slot on a real project. The Claude Code plugins and skills that survived that purge are the ones below.

This is not a "top repos to star and forget" list. Starring a GitHub repo costs nothing and changes nothing. What changed my actual throughput was wiring specific tools into specific moments — the design skill that fires when I'm building a landing page, the research skill that runs before I write a single line. Seventeen of them stuck. I've split them into the three buckets they live in for me: Design, Productivity, and Data & Research.

A quick honesty note before we start. I run these on real client and side-project work, so I can tell you which ones I reach for. But the hard numbers — Ponytail's 54% code reduction, Firecrawl's free-tier limits, Impeccable's command count — come from the maintainers' own benchmarks and docs, which I've linked and verified, not from a lab I run. Where a metric is theirs, I say so. Where it's my experience, I say that too. That line matters, because most "best skills" lists blur it until you can't trust either half.

## Why bolt-on skills beat a bigger model

Here's the thing most people get backwards. They chase the next model release expecting it to fix their design output or their token bill. But a frontier model with no constraints will still produce generic gradients and 400 lines of code where 180 would do. The model is raw capability. Skills are the judgment you bolt onto that capability.

Think of Claude Code as a brilliant contractor who's never seen your house. The model knows how to build anything. A skill is the project brief that says "match the existing trim, don't touch the load-bearing wall, here's the style we're going for." Same contractor, wildly different result.

That's why this list matters more than a model comparison. Three of the tools below — Taste, Impeccable, Ponytail — got adopted by major platforms within weeks of release. When GitHub Copilot integrates an open-source skill, that's a signal the open ecosystem is solving problems the model vendors haven't. You don't have to wait for the next release to fix your workflow. You can patch it today. Let's start where the pain is most visible: design.

## The design skills that kill AI slop

You know AI-generated design when you see it. The slightly-too-rounded corners, the purple-to-blue gradient on everything, the spacing that's almost right but never quite. Three skills attack that problem from different angles, and I use all three at different stages.

### Taste — the front-end judgment layer

Taste is an open-source skill built to give AI front-end work the thing it most obviously lacks: taste. It's not a component library or a template pack. It's a set of design principles plus sub-skills — image-to-code, redesign, and output optimization — that nudge the agent away from the default AI aesthetic and toward something a human designer would sign off on.

What I like about it: it's agent-agnostic. It works across Claude Code, Cursor, Codex CLI, and the rest, because it's just structured guidance, not a Claude-specific binary. I reach for the image-to-code sub-skill most — feed it a screenshot of a layout I admire, and it reconstructs the structure as clean front-end code without me hand-describing every element.

Use Taste when the *direction* is the problem — when the output is technically fine but soulless. It's a first-pass corrector, not a polisher. For the polish, you want the next one.

### Impeccable — 23 commands and a live browser editor

Impeccable is the one that blew up. Built by Paul Bakaus — former Google Developer Advocate and the original author of jQuery UI — it crossed 15,000 GitHub stars within days of release and became the most-starred design skill in the Claude Code ecosystem, according to the project's own write-up. GitHub Copilot integrated it. That kind of pickup doesn't happen for a toy.

It ships 23 specialized design commands plus seven deep reference guides covering typography, color and contrast, spatial design, motion, interaction, responsive behavior, and UX writing. So instead of vaguely asking Claude to "make it better," you direct it like a creative lead: critique this hierarchy, distill this layout, tighten this spacing. You speak the designer's vocabulary and so does the agent.

The differentiator nobody else has is the live browser editor (in beta) — visual edits made directly on the rendered website, not in the code, then synced back. That's the closest thing to a Figma-meets-terminal workflow I've used inside Claude Code. I went deep on it in [my full breakdown of the Impeccable design skill](https://www.mejba.me/blog/impeccable-claude-code-design-skill), so I won't repeat the whole teardown here. Short version: this is the polisher. Taste sets direction; Impeccable enforces craft.

### Awesome Design.md — reverse-engineer a site you admire

This one's a clever hack. Awesome Design.md leans on the design.md principles from Google Stitch to reverse-engineer an existing website's design language into a reusable template. Point it at a site you respect — Airtable, say — and it extracts the structure, typography, spacing, and component patterns into a spec you can apply to your own project.

To be crystal clear, because this matters: you're borrowing the *design framework*, not the content or the brand. It's the difference between studying how a great restaurant plates its food and stealing their menu. I use it when a client says "make it feel like [well-known product]" — instead of guessing what they mean, I extract the actual system and we talk about it concretely.

Three design skills, three jobs: direction, craft, and framework extraction. Now the bucket where the real time savings live.

## The productivity tools that pay for themselves

Design quality is visible. Productivity gains are quieter but they compound. These seven tools each remove a recurring tax from my week — verbose code, tab-switching, manual browser testing, context loss when I switch models.

### Ponytail — write less code on purpose

Ponytail puts what its creator calls a "lazy senior developer" inside your agent. Before Claude writes anything, Ponytail forces a check: does this actually need new code, or does an existing library, dependency, or pattern already cover it? The rule is strict — write only what the task needs — but with a hard guardrail: never cut validation, error handling, security, or accessibility to hit a smaller line count.

The maintainers' benchmark on a real FastAPI + React repo reported 54% less code, 22% fewer tokens, 20% lower cost, and 27% faster completion versus baseline. Those numbers come from specific Haiku and Opus runs and will swing with your codebase — a greenfield project with no existing libraries to reuse won't see the same reduction as a mature repo. There's also been fair public criticism of the benchmark methodology, which the maintainers responded to by updating their tests. So treat the figures as directional, not a guarantee.

What I can tell you from running it: the output is calmer. Fewer speculative abstractions, fewer "just in case" helper functions I'd have to read and delete later. If you've been fighting Claude's tendency to over-engineer, Ponytail is the constraint that helps. Pair it with the broader habits in my guide to [cutting Claude Code token costs](https://www.mejba.me/blog/caveman-claude-code-token-optimization) and the savings stack.

### NotebookLM CLI — your research docs in the terminal

Google's NotebookLM is excellent and trapped in a browser tab. The NotebookLM CLI (the `nlm` project maintained by Jacob Ben-David) breaks it out — full programmatic access to your notebooks, sources, and Studio outputs from the command line, so Claude Code can query your uploaded PDFs, docs, and YouTube transcripts without you ever leaving the terminal.

It's matured fast. The unified package merges the old `nlm` CLI with the `notebooklm-mcp` server into one installable Python module (via `uv`, `pipx`, or `pip`), and as of version 0.6.1 — released April 28, 2026 — it exposes 35 MCP tools: notebook creation, source addition from URLs and local files, single-source and cross-notebook queries, and Studio artifact generation including audio, slides, and quizzes. The setup command writes the Claude Code config entry for you instead of making you hand-edit JSON.

The reason this beats the web app for me: momentum. When I'm deep in a build and need to check what three source PDFs say about an API, I ask in the same session I'm coding in. No tab switch, no copy-paste, no context loss. I broke down the full research loop in [my NotebookLM plus Claude Code dev workflow post](https://www.mejba.me/blog/notebooklm-claude-code-dev-workflow).

### Playwright CLI — browser automation that scales

Playwright lets an agent drive a real browser — click buttons, fill forms, navigate flows like a human. The CLI flavor is more token-efficient and more flexible than the Playwright MCP server for the work I throw at it: testing a checkout flow across edge cases, automating a repetitive UI task, validating a form actually submits.

The mental shift here is treating your front-end like something to be *exercised*, not just looked at. Instead of manually clicking through fifteen states after a change, you describe the flow once and let Claude run it at scale, catching the edge case that breaks at step twelve. I covered the setup and the token-efficiency angle in detail in [my Playwright CLI browser automation guide](https://www.mejba.me/blog/playwright-cli-claude-code-browser-automation).

### Codex Plugin — a second model in the room

The official OpenAI Codex plugin for Claude Code lets you run GPT models alongside Claude in the same workflow. It's not about replacing Claude — it's about having a second opinion on tap. The "Codex rescue" command offloads a gnarly task to GPT when Claude's stuck in a loop, and you can run adversarial code review where one model critiques the other's output.

I treat it like pair programming with two seniors who think differently. When Claude and Codex agree a change is safe, I trust it more than either alone. When they disagree, the disagreement itself is the signal — that's exactly the spot to slow down. I wrote up how I run them together in [the Claude Code + Codex dynamic-duo workflow](https://www.mejba.me/blog/claude-code-codex-plugin-dynamic-duo-workflow).

### Google Workspace CLI — the deep ecosystem play

The GWS CLI is an unofficial command-line tool built by a Google developer that extends Google Workspace far past the official connector — sending email, building automated workflow skills like weekly digests and meeting prep, with 40+ pre-loaded operations ready to go.

Straight talk: the install is fiddly. This is the most complex setup on the list, and if you only touch Workspace occasionally it's not worth the friction. But if your whole operation lives in Gmail, Calendar, Docs, and Sheets, the payoff is real — Claude becomes an operator inside your actual workspace, not a chatbot you copy results out of. Match the tool to how much of your day Google owns.

### GitHub CLI — the one nobody should skip

`gh` is unglamorous and non-negotiable. It's how projects get from your machine to GitHub without leaving Claude Code — create repos, open pull requests, push branches, manage issues, all from the same session you're building in.

There's no clever angle here. If you're shipping code with Claude Code and you don't have the GitHub CLI wired in, you're context-switching to a browser for work that should be one command. Install it first, thank yourself later.

That covers the tools that save time on work you're already doing. The Skill Creator is different — it improves the tools themselves.

### Skill Creator — measure whether a skill actually helps

Skill Creator is Anthropic's official skill for building, modifying, and A/B testing your own skills inside Claude Code. The killer feature is objective measurement: it runs your task with and without a given skill and compares performance, so you find out whether that skill you installed is genuinely helping or just adding overhead.

This is the antidote to my fat-`.claude`-folder problem. Instead of hoarding skills on vibes, you get a number. I now run anything I'm unsure about through Skill Creator before it earns a permanent slot — it's how I'd have caught those nine forgotten skills months earlier. Install is the easy kind: search for it in the plugin marketplace and add it. I dug into its A/B testing flow in [my Skill Creator testing and optimization post](https://www.mejba.me/blog/claude-skill-creator-testing-optimization).

If you've made it this far, you've already got a sharper stack than most Claude Code users running today. The last bucket is where the real leverage hides — because the bottleneck on most projects isn't writing code, it's knowing what to build.

## The data and research tools that find the truth

Code is the easy part now. The hard part is grounding what you build in reality — real user sentiment, real scraped data, real persistent memory. These seven tools handle the parts of the job that used to require manual grinding.

### Last 30 Days — research beyond the knowledge cutoff

Every model has a knowledge cutoff. The Last 30 Days skill blows past it by researching live across Reddit, X, YouTube, Hacker News, Polymarket, TikTok, Bluesky, and the open web — then synthesizing one ranked brief with real citations from genuinely recent discussion.

It was, briefly, among the highest-starred repos on GitHub, and the maintainer reported it hit roughly 11,900 stars with 2,824 of those landing in a single day. The reason it pops: it scores results by what real people actually engage with, not just keyword match. When I'm validating whether a product idea has genuine demand or just my own enthusiasm, this finds the unfiltered conversations a standard web search buries. Install is marketplace-simple: `/plugin marketplace add mvanhorn/last30days-skill`.

### Firecrawl CLI — scraping that gets past bot walls

Standard web fetching dies the moment a site has bot protection. Firecrawl is the scraper built for exactly that — it discovers, crawls, and interacts with every URL on a site, including pages that block naive requests. It became an official Claude plugin, which says something about reliability.

On pricing, because people always ask: the free tier gives 1,000 pages per month (1,000 credits, one credit per page for scrape, crawl, map, and monitor), with rate limits of 10 scrapes per minute and 1 crawl per minute, no credit card required to start. There's an open-source self-host route too. For the kind of competitive research and data-gathering I do, the free monthly allotment covers most weeks before I touch a paid plan.

### Auto Research — Karpathy's optimization loop

Auto Research, from Andrej Karpathy, automates ML experiments by iterating tests against a single success metric — runtime, accuracy, whatever you define — and logging every step of the optimization loop in detail. You set the objective; it runs the experiments and reports what moved the number.

The constraint to respect: this shines only when your success criterion is *objective and numerical*. "Make the model faster" works. "Make the output feel better" doesn't — there's nothing for the loop to optimize against. Used in its lane, it automates the tedious experiment-tuning grind that eats research time. I unpacked the strategy and where it fits in [my Auto Research with Claude Code breakdown](https://www.mejba.me/blog/auto-research-claude-code-strategy).

### Supabase CLI — a backend by talking to it

The moment a project needs to store something — form submissions, user logins, anything stateful — you need a database. The Supabase CLI lets you create databases, set up auth, and manage all of it through natural language in Claude Code, across both cloud and local setups.

This is the unlock that turns a static prototype into a real app without you context-switching into a database console. "Add a users table with email auth and a submissions table linked to it" becomes a sentence, not an afternoon. For anyone building SaaS or tools that persist data, it removes the single most common blocker between prototype and product.

### Obsidian Integration — give Claude a real memory

Claude Code's context window is powerful but it forgets between sessions. The Obsidian integration fixes that by linking Claude to your Obsidian vault — your organized notes become a connected knowledge graph the agent can query, so context-rich answers come from *your* accumulated knowledge, not just the current chat.

This is the difference between an assistant that starts cold every morning and one that remembers your project's history, decisions, and quirks. I run a vault as Claude's long-term memory and the quality jump is real — fewer re-explanations, more continuity. I covered the setup in [my Obsidian + Claude Code persistent memory post](https://www.mejba.me/blog/obsidian-claude-code-second-brain).

### LightRAG — retrieval built on real knowledge graphs

LightRAG is retrieval-augmented generation done with actual embeddings and genuine knowledge graphs rather than the synthetic, hand-waved maps a lot of RAG setups fake. It's lightweight and fast, supports multi-modal data — text, images, charts — and works as a stepping stone toward heavier RAG systems without the upfront complexity.

If the Obsidian integration is memory for your notes, LightRAG is structured memory for a real corpus — documentation, research libraries, mixed-media knowledge bases. When standard retrieval keeps returning shallow or disconnected answers, the graph structure is what restores the relationships between facts. It pairs naturally with the knowledge-base thinking in [Karpathy's Obsidian RAG approach](https://www.mejba.me/blog/karpathy-obsidian-rag-knowledge-base).

### Stripe CLI — payments without the dashboard maze

If your app takes money, you'll eventually fight the Stripe dashboard. The Stripe CLI simplifies payment management through terminal commands and natural language — create products, test webhooks, manage the commerce layer — so Claude handles the integration instead of you clicking through a maze of settings.

For monetized projects this is the last-mile tool. Building the app is one thing; wiring payments cleanly is the part that separates a demo from a business. Doing it from the same session you built the app in keeps the whole flow coherent.

Seventeen tools. But a stack is only as good as how you assemble it — and that's where most people go wrong.

## What I got wrong building this stack

My first mistake was the one I opened with: collecting skills like trading cards. More installed skills is not more capability — past a point it's noise, conflicting guidance, and overhead. The fat `.claude` folder made Claude *worse*, not better, because skills can pull in opposite directions and I had no way to see which were helping.

The fix was boring discipline: install one tool, run it on real work for a week, then keep it only if I can name the specific moment it earned its place. Skill Creator made that measurable instead of vibes-based. If you take one thing from this list, take that loop — not the list itself.

My second mistake was trusting benchmark numbers as promises. When I first saw Ponytail's 54% figure I expected that on every project. It's real, but it's *their* number on *their* test repo, and it swings hard with context. The public critique of those benchmarks taught me something useful: a metric without its conditions is marketing. Now I read every "X% improvement" claim — including the ones in this article — as "under their specific conditions," and I verify on my own work before I believe it. You should do the same with anything I've reported here that isn't explicitly my own experience.

The honest limitation of this whole category: skills add maintenance. They update, they occasionally break, they need pruning. A stack of seventeen tools is seventeen things that can drift. I re-audit mine roughly monthly. If you won't maintain them, install fewer.

## So how do you start?

Don't install all seventeen. That's the exact mistake I made, repackaged.

Pick the one bucket where your current pain is loudest. If your output looks like AI made it, start with Impeccable and Taste. If your token bill or your over-engineered code is the problem, start with Ponytail and the GitHub CLI. If you keep building things nobody wants, start with Last 30 Days and Firecrawl. Run that one bucket for a week on real work. Then, only if it earned the slot, add the next.

For the full landscape of what's worth installing this year, I keep a running list in [my roundup of the top GitHub repos for Claude Code in 2026](https://www.mejba.me/blog/top-github-repos-claude-code-2026) — this article is the curated, battle-tested cut of that wider field.

Here's the reframe I'll leave you with. The model is not the moat anymore — everyone has access to roughly the same frontier capability. The moat is the judgment you wrap around it: the design taste, the code restraint, the research depth, the memory. That judgment is exactly what these skills encode. The developer who wins in 2026 isn't the one with the biggest model. It's the one whose stack makes a good model behave like a great team.

Go open your `.claude` folder. If you can't remember what half of what's in there does, you've got the same problem I had. Start the purge tonight.

## Frequently Asked Questions

### What is the difference between a Claude Code plugin and a skill?
A skill is structured guidance — principles, references, and commands that shape how the agent reasons about a task, like Impeccable's design rules. A plugin typically packages a skill plus tooling or an external integration installed through the marketplace. In practice the terms overlap, and both extend Claude Code's default behavior toward a specific job.

### How do I install Claude Code skills and plugins?
Most install through the marketplace — run `/plugin` inside Claude Code, search for the tool, and add it, or use `/plugin marketplace add <repo>` for a specific GitHub project. CLIs like NotebookLM's `nlm` install via `uv`, `pipx`, or `pip`, then register themselves in your Claude Code config. See each tool's section above for its exact route.

### Do too many Claude Code skills slow it down?
Yes — past a point, more installed skills add noise, conflicting guidance, and overhead rather than capability. Install one tool, run it on real work for a week, and keep it only if you can name where it helped. Anthropic's Skill Creator can A/B test a skill objectively so you remove the ones that aren't earning their slot.

### Are these Claude Code plugins and skills free?
Most on this list are open-source and free, including Taste, Impeccable, Ponytail, and the NotebookLM CLI. A few wrap paid services: Firecrawl gives 1,000 free pages per month before paid tiers, and Supabase and Stripe have their own free tiers and usage-based pricing. Always check the underlying service's limits before scaling.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
