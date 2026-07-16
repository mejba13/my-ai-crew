**BRAND:** mejba.me
**TITLE:** Agentic OS for Claude Code: The 3 Layers That Matter
**META TITLE:** Agentic OS Claude Code: 3 Layers Most Builders Skip
**SLUG:** agentic-os-claude-code-three-layers
**PRIMARY KEYWORD:** agentic OS Claude Code
**META DESCRIPTION:** I built an agentic OS on Claude Code in the wrong order — dashboard first. Here are the 3 layers that actually work: skills, memory, then UI.
**TAGS:** Claude Code, AI Agents, Agentic OS, Automation, Productivity

---

I built my dashboard first. That was the mistake.

For about three weeks last month I had a beautiful Streamlit panel sitting on my second monitor. Five tabs. Real-time agent activity. A clickable button labeled "Run Content Cascade" that pulsed when an agent picked up the job. From the outside it looked like the kind of agentic OS Claude Code power users post screenshots of on X for engagement. From the inside it was a Potemkin village. The buttons called bash scripts that called other bash scripts that called Claude Code with prompts I had to maintain in three different places. Every change broke two other things. Every new "skill" was a new spaghetti chain.

I tore the dashboard out on a Sunday morning. Started over. And this time I built in the order that actually works — the one I should have followed from day one. Skills first. Memory second. Dashboard last, if at all.

That sequence is the entire thesis of this post. If you're trying to build an agentic OS Claude Code can actually drive, and you're starting with the UI, you're building fancy nonsense. I learned that the hard way. Here's the map I wish someone had handed me three weeks earlier.

## What An Agentic OS Actually Is (And Isn't)

Let me kill some terminology first, because "agentic OS" has become one of those phrases that means something different in every Substack post.

An agentic operating system, in the way I'm using it, is the layered software environment that lets a coding agent — Claude Code in my case — run repeatable, observable, composable work across your actual machine and accounts. Not "an AI app." Not "a wrapper around the API." A _chassis_ that gives the agent skills it can invoke, memory it can read, and visibility into what it's doing.

The shape of mine, after the rebuild, looks like this. Three layers. Bottom to top, in order of importance:

1. **Skill and automation backbone** — codified, versioned, testable workflows the agent can call by name
2. **Memory layer** — structured context the agent can traverse efficiently without burning tokens
3. **Dashboard and command center** — observability and one-click triggers for the layers below

Most public agentic-OS content reverses that order. Builders show off the dashboard because dashboards are easy to screenshot. Skills aren't. Memory isn't. A folder of well-named markdown files doesn't make a viral post. But here's the thing — the dashboard is the _least_ valuable layer in terms of actual capability. It's pure user interface. Strip it away and your agent still works. Strip away skills, and your agent is a stranger in your terminal every single session.

That's the asymmetry nobody talks about. Build wrong and you spend months on the layer that produces the least leverage.

## Layer 1: The Skill And Automation Backbone

This is the layer that does all the real work, and it's the one almost everyone underbuilds.

Most people use Claude Code like an advanced chatbot. They open a terminal in a project folder, type a paragraph of instructions, watch the agent do the thing, close the tab. That works. It's better than not using Claude Code. But it's also the developer equivalent of writing every email from scratch because you've never heard of templates. You're leaving 90% of the leverage on the floor.

A skill, in the Anthropic sense, is a small folder containing a `SKILL.md` file and optionally some supporting scripts or reference documents. According to the [Claude Code skills documentation](https://code.claude.com/docs/en/skills), the agent automatically discovers skills you've installed, reads their metadata, and pulls them into the conversation only when your request actually needs them. The skill ecosystem has grown to over 1,000 community-built skills as of early 2026, with categories ranging from code review to git commit writing to SEO optimization, according to coverage from [Agensi's 2026 skills roundup](https://www.agensi.io/learn/best-claude-code-skills-2026).

But "install a skill from the marketplace" is the boring story. The interesting one is writing your own.

### Why Codifying Your Tasks Changes Everything

When you type a prompt into Claude Code, three things happen that should worry you. First, the prompt is _ephemeral_ — next time you do the same task, you'll type something slightly different and get a slightly different result. Second, it's _untestable_ — there's no version to compare against, no benchmark, no A/B. Third, it's _non-deterministic squared_ — the LLM is already non-deterministic, and now your input to it is also drifting.

Codifying a task into a skill collapses two of those problems. The prompt is no longer ephemeral; it lives in a file at a stable path. The prompt is no longer drifting; you commit changes to it like any other code. And because the skill has a fixed surface ("call skill X with input Y"), you can actually test it — feed in five different inputs, eyeball the outputs, decide if the skill needs tightening.

I learned this when I built my content cascade skill. The job: take one finished blog post, generate the Twitter version, the LinkedIn version, the newsletter snippet, the Reddit-friendly summary, the YouTube description, and a Mastodon variant — all aligned to the source post's voice and primary keyword. Before the skill existed, I'd done this manually about forty times, and every batch came out slightly different. Tone drift. Length drift. CTA drift. Once I codified the workflow into a skill folder with explicit format templates, anchor examples, and a hardcoded brand voice reference, the variance collapsed. Now I type `@aria run content cascade for [slug]` and six artifacts drop into a folder in roughly two minutes. The skill is 180 lines of markdown. The leverage is permanent.

That's the move. Not "wow, this LLM can write a tweet." More like — wow, this workflow that used to take me forty minutes and produce inconsistent output now takes two minutes and produces consistent output, and I can keep tightening the skill until it's surgical.

### Higher-Order Skills: The Compounding Move

The real unlock isn't single-task skills. It's _workflow skills_ — skills that orchestrate other skills.

Example from my own setup. The content cascade I just described is itself called by a higher-level skill named `weekly-publish`. That parent skill does roughly this: open the next post in the publishing queue, run the cascade, schedule the social variants across the right platforms, log the metadata into my Obsidian vault, ping a Slack webhook with the publish report. Six discrete actions, one trigger, zero manual coordination.

This is the thing that turns a skills folder into an actual operating system. Once your skills are stable enough to call each other, you've moved from "the agent does individual tasks" to "the agent runs procedures." That's the line between automation and orchestration. And it only becomes possible after the underlying skills are reliable enough that the higher-order skill can trust them. Which means — you guessed it — you have to build the bottom layer first.

There's a quiet point worth pulling out here. The Anthropic team's own [Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) is a fifty-page document covering skill design patterns in depth. Almost nobody reads it. The most useful Claude Code feature in 2026 is, by my read, the most underused one. Skill creation is sitting right there, the docs are excellent, and the average user is still pasting paragraph-long prompts into a terminal like it's 2024.

### Local Versus Cloud Automations

A quick aside, because this comes up every time. Claude Code can run in two modes — local on your machine, and cloud (the headless setup Anthropic offers through their managed agent infrastructure).

Local is what I run. It has access to my filesystem. It can call CLI tools. It can read and write files. It can invoke skills that live in my project directory. When I tell Claude Code to "regenerate the social cascade for the last three posts," it can actually open those posts, read them, and write the output back. That's the entire game.

Cloud is what runs on Anthropic's servers. It does not have access to your local files. It's bound by whatever APIs and tools Anthropic exposes. It's useful for certain long-running headless workflows — but if you're a solo operator building an agentic OS, local is almost always the right answer. You get more capability, more control, and a wider surface for skills to plug into.

I'll come back to cost concerns at the end, because there's a real but smaller-than-you-think pricing question that lives at the cloud edge of this discussion.

<!-- IMAGE: Diagram showing three stacked layers of an agentic OS — Skill Backbone at the foundation, Memory Layer in the middle, Dashboard at the top. Arrows show the agent reading from all three layers. Alt text: "agentic OS Claude Code three layers diagram showing skills, memory, and dashboard". Caption: "The three layers, in build order. Skip the bottom and the top collapses." -->

## Layer 2: The Memory Layer (Context Engineering, Done Quietly)

If skills are the verbs of your agentic OS, memory is the nouns. Without it, every session starts from zero. The agent has no idea who you are, what you've decided, or where anything lives.

Most discussions of memory go straight to vector databases, embeddings, and RAG pipelines. Those are real tools and they have real uses. But they're also the most over-recommended architecture in the agentic-OS conversation right now, and I want to push back on that hard. For most solo operators and small teams running Claude Code, you don't need a vector DB. You need an organizational layer.

This is where Obsidian earns its keep. And it's also where most people misunderstand what Obsidian is.

### Obsidian Is Not A Vector Database. That's The Point.

Let me say this plainly because the confusion costs people weeks. Obsidian is not a RAG system. It does not generate embeddings. It does not do semantic search across your vault by default. It is, structurally, a very polished markdown file viewer that runs on top of a folder on your hard drive.

That's the _whole_ feature. And it's exactly what you want.

Because here's the thing — Claude Code already reads markdown. It already understands folder structure. It already follows links. You don't need to bolt a vector database onto your knowledge base to make an AI agent useful inside it. You need to organize your markdown well enough that the agent can navigate it the same way a human would. The model is the search engine. Your folder structure is the index.

This insight isn't mine. The pattern is showing up across the community — Andrej Karpathy published an [LLM Wiki architecture](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-obsidian-codeex-second-brain) earlier this year where an AI agent continuously builds an Obsidian-style markdown wiki out of saved content, and the entire design relies on plain files and link graphs rather than embeddings. A March 2026 [practical writeup by Eric Ma](https://ericmjl.github.io/blog/2026/3/6/mastering-personal-knowledge-management-with-obsidian-and-ai/) describes reducing personal knowledge management overhead from 30-40% of his time to under 10% by combining Obsidian with agent skills — explicitly because plain text is the lowest-friction substrate for an LLM to operate on.

The vector database community will object. They have a point in narrow cases — if you're indexing tens of thousands of long documents and need sub-second semantic retrieval, fine, build a real RAG pipeline. But if your "memory" is 200 markdown files of project notes, brand voices, code conventions, and decision logs, Obsidian-as-an-organizational-layer beats Obsidian-plus-vector-db every time. Less to maintain. Less to break. Faster to query.

### The Index File Pattern (This Is The Real Trick)

The single highest-leverage move in your memory layer is this: drop an index file at every folder level in your vault.

Not a fancy plugin. Not a generated graph. A plain `index.md` (or `_index.md`, or `README.md` — pick one and stick with it) that lives in each folder and contains a small table of contents listing what's inside, with one-line descriptions. That's it. Three minutes to write. Pays for itself permanently.

Why this matters comes down to tokens. When Claude Code is trying to find the brand voice for `colorpark.io`, two paths are possible. Path A: the agent reads every file in `content/colorpark.io/` looking for the voice spec. That's potentially thousands of tokens of irrelevant blog posts loaded into context before the agent finds the one document it wanted. Path B: the agent reads `content/colorpark.io/index.md`, sees a one-line entry pointing to `_brand-voice.md`, opens that file directly. Maybe 400 tokens total. Same outcome, ten times cheaper, twenty times faster, and dramatically less context rot inside the session.

Multiply this pattern across a vault with seven top-level directories and forty subdirectories and the savings compound. My own `ai-agents-team` repo has index files at every directory level above ten files. Every time I add a new structured folder — a new brand, a new skill, a new workflow — the first file I create is the index. Then everything else slots in below it.

This is the part of context engineering that doesn't get written about, because it's boring. Nobody wants to read a thread titled "I wrote a table of contents." But this single habit has done more for my agent's ability to navigate my memory layer than any plugin, any vector database, any prompt engineering trick. For a much deeper treatment of the broader memory-architecture question, I wrote a [full breakdown of the six levels of Claude Code memory systems](https://www.mejba.me/claude-code-memory-systems-six-levels) — this index-file pattern is the load-bearing trick at Level 2.

### What Memory Layer Actually Contains

For the avoidance of doubt, here's what lives in mine after the rebuild:

- **Project-level instructions** — the `CLAUDE.md` at the repo root, defining hard constraints and output formats
- **Brand voice specs** — one markdown file per brand, with voice rules, banned phrases, example phrasing
- **Decision logs** — short dated entries when I make a non-obvious choice, so the agent can reconstruct why
- **Skill inventory** — an index of every skill I've built, with one-line descriptions and trigger phrases
- **Personal preferences** — coding style, tool defaults, my standing rules about when to ask vs. when to act
- **Reference snippets** — code patterns I reuse, prompts I've benchmarked, config files I've validated

That's it. Six folders, maybe a hundred files. No embeddings. No vector DB. No background indexer eating CPU. The agent walks the graph the same way I would.

You can extend this with MCP integration if you want the agent to read directly from an actual Obsidian vault rather than from your repo. The [Obsidian-Skills toolkit](https://addozhang.medium.com/obsidian-skills-empowering-ai-agents-to-master-obsidian-knowledge-management-8b4f6d844b34) released in March 2026 gives Claude Code an explicit set of skills for handling Obsidian's Markdown, Bases, and JSON Canvas formats. I haven't migrated my own setup to that yet — the plain markdown approach is still doing the job — but if you're already a heavy Obsidian user, that's a clean on-ramp.

## Layer 3: The Dashboard And Command Center (Last, Smallest, Optional)

Now we get to the dashboard. The layer everyone wants to build first. The layer I built first. The layer that, after rebuilding everything from the bottom up, I'm now genuinely glad to come back to — because _now_ there's something worth observing.

A dashboard for an agentic OS does two jobs, and only two. It surfaces information that the terminal can't show cleanly. And it gives non-technical users (or future-you, three months from now and tired) a clickable surface for triggering the skills underneath.

That first job matters more than the second. Your terminal is great at showing you the agent's current activity — what file it's reading, what tool it's calling, what command it's about to run. The community has built solid observability tooling around this. The [Claude Code multi-agent observability project](https://github.com/disler/claude-code-hooks-multi-agent-observability) is a real-time dashboard that captures hook events and streams them to a live UI, monitoring multiple concurrent agents with session tracking and event filtering. Cole Murray's [claude-code-otel project](https://github.com/ColeMurray/claude-code-otel) wires Claude Code into OpenTelemetry for cost and performance monitoring. These exist. They're good. Install one if you're running multiple agents in parallel and losing track. I've also written more about the [Claude Code agents view dashboard](https://www.mejba.me/claude-code-agents-view-dashboard) if you want the visual walkthrough.

But there's a second class of information a dashboard can show that the terminal genuinely can't: _downstream metrics_. Social analytics from the posts your agent published. Search rankings from the SEO audits your agent ran. Cost breakdowns by skill. Research summaries from a long-running deep-research run. Anything that lives outside the agent's immediate execution context but inside the agent's overall mission. That's what a dashboard is for.

The second job — the clickable-button-for-non-technical-users layer — is where Streamlit comes in. According to [Streamlit's own coverage](https://blog.streamlit.io/vibe-code-streamlit-apps-with-ai-using-agents-md-04b7480f754e), the framework has quietly become the default UI substrate for Claude-powered agents in 2025 and 2026. The pitch: a few dozen lines of Python become a working web app, and Claude Code can edit and extend those lines as easily as it edits anything else.

### Obsidian Dashboard Versus Streamlit Web App

I've run both. Here's the honest comparison.

| Dimension              | Obsidian Dashboard                     | Streamlit Web App                     |
| ---------------------- | -------------------------------------- | ------------------------------------- |
| Setup time             | 30 minutes (it's already your vault)   | Half a day (Python env, deploy, auth) |
| Customization ceiling  | Extremely high (any plugin, any embed) | Medium (Streamlit's component model)  |
| Integrated terminal    | Yes (via plugin)                       | No — separate window                  |
| Calendar view          | Native                                 | Build it yourself                     |
| Distribution to others | Painful (everyone needs Obsidian)      | Easy (it's a URL)                     |
| Mobile access          | Possible but awkward                   | Trivial                               |
| Maintenance burden     | Low (markdown + a few plugins)         | Medium (Python deps, hosting)         |
| Best for               | Solo operator                          | Team or client-facing                 |

For my own day-to-day, Obsidian wins. Everything lives in markdown anyway. The integrated terminal pane means I can watch Claude Code's output and the dashboard side by side in one window. Custom views via Bases or Dataview surface exactly what I want. I'm a population of one, so distribution doesn't matter.

The moment I need to hand a panel to a client or a teammate, Streamlit wins. "Click this button to regenerate the weekly report" lands very differently from "install Obsidian, clone this vault, configure these five plugins." Streamlit has nine official dashboard templates with synthetic data and layout patterns, designed exactly for this kind of agent-driven app, per the [Snowflake/Streamlit guide on building dashboards with agent skills](https://www.snowflake.com/en/developers/guides/build-streamlit-apps-with-agent-skills/). For a client-facing layer, that's an enormous head start.

The actual answer for most operators is _both_. Obsidian for your private control center. A small Streamlit app for anything that needs to leave your machine. Each one wraps the same skills underneath. That's the point of having a real Layer 1 — the UI layer is interchangeable.

## How Do I Know If My Skills Are Working Well Enough To Build A Dashboard?

This is the question I should have asked myself a month ago. Here's the test I use now.

A skill is dashboard-ready when three things are true. It produces the same shape of output every time you call it on equivalent inputs. It fails loudly when its preconditions aren't met (the file doesn't exist, the API key is missing) rather than silently producing garbage. And it can be invoked by name in plain language without ambiguity — `run content cascade for slug X` should not be confused with `run content review for slug X`.

If a skill fails any of those tests, putting a button on top of it just means a beautiful button that produces unreliable output. Fix the skill. Then the button is honest.

## The Cost Concern Everyone Brings Up

Whenever I describe this setup to someone new, the same objection arrives. "Doesn't this get expensive? Aren't you burning tokens every time the agent loads your memory layer?"

Honest answer: not really, and the math has gotten more forgiving over the last year.

Claude Code's [Max 20x plan](https://www.cloudzero.com/blog/claude-pricing/) sits at $200 per month and gives you a usage envelope that, per Anthropic's own framing, would cost $600 to $1,500 in pay-as-you-go API tokens. For a heavy Claude Code user — and "heavy" means running the agent multiple hours a day, not casually — the Max 20x cap is the practical ceiling on how much you'll spend on the engine. If you're not at Max 20x, the math is even more forgiving.

For headless cloud runs, where you're paying per token rather than a flat subscription, the cost pattern is different but still manageable for individual operators. Anthropic's current API pricing has Claude Sonnet 4.6 at $3 input and $15 output per million tokens, with Claude Opus 4.7 at $5 and $25 per million. Most of your agentic OS work runs on Sonnet or Haiku, not Opus. A well-structured memory layer with index files means each agent invocation loads maybe a few thousand tokens of context, not a few hundred thousand. The cost-per-task ends up far lower than people expect.

The deeper point is that the engine is replaceable. Claude Code is the most capable agent in this category right now, but it isn't the only one. If you ever do bump against Anthropic's pricing ceiling — or you want a backup, or you want to A/B-test models — you can swap the engine. Codex CLI runs on the same kind of skill-and-folder substrate. Other agents will too. The OS chassis is what you're building. The engine plugs in and out.

This is why getting the layers right matters more than picking the perfect model. The model is the variable. The OS is the constant.

## What I'd Do Differently Starting From Scratch Today

If I were rebuilding from zero tomorrow, here's the order. No dashboards. No vector databases. No premature optimization.

Week one: pick three tasks I do at least weekly, and codify each one as a skill. Just three. Write the SKILL.md, write the prompt, test the inputs, commit. Use the skills for a week. Refine them. Get the variance down. If you want a head start on what good skills look like, I've covered the [advanced skill patterns inside Claude Code](https://www.mejba.me/agent-skills-advanced-claude-code) — start there, then write your own.

Week two: drop in the memory layer. A `CLAUDE.md` at the repo root. A folder for brand voices or project specs or whatever your equivalent is. An index file at every directory level. That's the whole memory layer. Don't add embeddings. Don't add a vector database. Just write index files.

Week three: build one more skill — a higher-order skill that orchestrates the first three. Now you've crossed the threshold from "automation" to "orchestration." This is where the agent feels like a teammate instead of a tool.

Week four, and only if you want one: a small dashboard. Obsidian if it's just for you. A 100-line Streamlit app if you need to share it. Wire the buttons to the skills you already built. That's the entire dashboard build.

The whole thing fits in a month. Most of the work isn't agent work — it's the human work of deciding which tasks are worth codifying and writing them down clearly enough that a future version of you, or an LLM, can execute them without ambiguity. That's the actual job. The agent is the leverage. The skills are the IP.

The dashboard people will keep posting screenshots. Let them. You'll be the one whose agent actually does work.

## Frequently Asked Questions

### What is an agentic OS for Claude Code?

An agentic OS for Claude Code is a layered software environment — skills, memory, and observability — that lets the agent run repeatable, composable workflows on your machine. It's not an app; it's a chassis. For a deeper breakdown, see the three-layers section above.

### Do I need a vector database for Claude Code memory?

For most solo operators, no. A well-organized folder of markdown files with index files at every directory level outperforms a vector database for typical agent workflows — faster, cheaper, simpler. Vector DBs only earn their complexity at very large scale.

### Should I use Obsidian or Streamlit for my agent dashboard?

Obsidian if the dashboard is just for you — it's already your knowledge base, fully customizable, with an integrated terminal pane. Streamlit if you need to share the dashboard with clients or non-technical teammates — a URL beats "install this app."

### How much does running Claude Code as an agentic OS cost?

The Max 20x plan at $200/month covers most heavy Claude Code workflows, with token allowances Anthropic frames as equivalent to $600-$1,500 in API usage. Lighter users sit comfortably on the $20/month Pro tier or pay-as-you-go API rates.

### Can I swap Claude Code for another engine in my agentic OS?

Yes — that's the design point. If your skills and memory layer are well-structured, the engine becomes the variable. Codex CLI and other agents can run on the same folder substrate. Build the OS once, swap engines as the model landscape shifts.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

- **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
- **Portfolio**: [mejba.me](https://www.mejba.me)
- **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
- **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
- **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
