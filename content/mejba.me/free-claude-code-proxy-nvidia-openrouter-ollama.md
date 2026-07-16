**BRAND:** mejba.me
**TITLE:** Free Claude Code Proxy: NVIDIA, OpenRouter, Ollama Tested
**META TITLE:** Free Claude Code Proxy: NVIDIA, OpenRouter, Ollama
**SLUG:** free-claude-code-proxy-nvidia-openrouter-ollama
**PRIMARY KEYWORD:** free Claude Code proxy
**META DESCRIPTION:** I tested a free Claude Code proxy routing to NVIDIA NIM, OpenRouter, and Ollama. Built a habit tracker for cents, not $5-$10. Setup, pitfalls, real costs.
**TAGS:** Claude Code, AI Proxy, OpenRouter, NVIDIA NIM, Ollama

---

# Free Claude Code Proxy: NVIDIA, OpenRouter, Ollama Tested

I built a habit tracker app called Habitual last weekend. Three iterations, two redesigns, a real database, working streaks logic. The whole thing — start to finish — cost me less than the price of a stick of gum.

If I'd built it the way I normally do, on my Claude Max subscription with Opus 4.7 doing the heavy lifting, the same project would have eaten somewhere between five and ten dollars of API credit on top of my $200 monthly subscription. Not a fortune. But for a side project I might delete next week, it's exactly the kind of cost that makes me think twice before opening the terminal at midnight.

Here's what changed. I kept Claude Code — the CLI, the agent, the tool execution, all of it — exactly the same. I didn't switch to a different IDE. I didn't learn a new workflow. I just slipped a small open-source proxy underneath it, and that proxy quietly rerouted every API call to a free or near-free model on a different infrastructure. NVIDIA NIM. OpenRouter. Ollama running on my MacBook. Three backends, one proxy, the same Claude Code interface I already know cold.

The result isn't perfect. I'll get into where it cracks. But for prototyping, learning, side projects, and the long tail of "I want to try something but don't want to think about cost" — this is the closest thing to a cheat code I've found in 2026.

<!-- IMAGE: Architecture diagram showing Claude Code CLI on left, a localhost:8082 proxy server in the middle, and three backends on the right (NVIDIA NIM, OpenRouter, Ollama). Alt text: "free Claude Code proxy architecture routing to NVIDIA NIM OpenRouter Ollama backends". Caption: "The proxy sits between Claude Code and any backend you point it at — Anthropic never sees the request." -->

## Why I Went Looking for This in the First Place

My Claude Max plan is $200/month. I'm not complaining — I burn it down every week on client work and I'd pay double for the reliability. According to Anthropic's [current pricing breakdown](https://support.claude.com/en/articles/11049741-what-is-the-max-plan), Max 20x gets me roughly 20x the Pro tier's usage window, which works out to about 220,000 tokens every five hours on the API equivalent. For full-day Opus 4.7 work, that's the threshold where rate limits stop being a daily annoyance.

But there's a different math that bothers me. The Pro plan at $20/month is the entry point most developers actually start at. The Max 5x at $100 is the upgrade you hit when Pro starts ghosting you mid-task. Both come with weekly usage limits — one across all models, one Sonnet-only — that reset seven days from when your session started. If you're building a side project on Pro and you blow through your weekly quota on a Tuesday afternoon, you wait until next Tuesday. Or you cough up another $80 to upgrade.

For client work? Pay it. The reliability is worth every dollar.

For learning, prototyping, and the personal projects I might never ship? That same money could buy me a year of side-project experimentation if I just routed the cheap stuff through cheaper models. The catch: Claude Code is the best agentic coding tool I've ever used. The file editing, the sub-agents, the skill system, the way it handles long-running tasks — none of the open-source alternatives match it yet. So switching tools to save money was off the table.

The proxy approach is the cleanest workaround I've seen. Keep the tool. Swap the engine. Three lines in an `.env` file.

If you've already gone down the [OpenRouter route with Claude Code](https://www.mejba.me/claude-code-openrouter-free-models) using a custom config, the proxy I'm about to walk through is the more flexible cousin — it handles three backends, not one, and it normalizes provider quirks the manual setup misses.

## How the Proxy Actually Works (and Why It Matters)

The mental model is simpler than it sounds.

Claude Code, by default, talks to Anthropic's API at `api.anthropic.com`. Every prompt, every tool call, every streaming chunk goes there and comes back. The proxy interrupts that conversation. You spin up a tiny FastAPI server on your own machine — usually `localhost:8082` — that exposes the same Anthropic-compatible routes Claude Code expects (`/v1/messages`, `/v1/messages/count_tokens`, `/v1/models`). When Claude Code sends a request to that local address instead of Anthropic's, the proxy translates the payload into whatever format the actual backend wants, fires it off, and translates the response back into the shape Claude Code knows how to parse.

The proxy is doing the work of a translator at a UN summit. Claude Code speaks Anthropic. NVIDIA NIM speaks OpenAI-flavored. OpenRouter speaks its own dialect. Ollama speaks something close to OpenAI but with quirks. The proxy listens to one and speaks all the others, on the fly, in real time, including the streaming tokens, the tool-use blocks, the reasoning/thinking blocks, and the usage metadata Claude Code uses to track cost.

The specific repo I tested is [Ali Sharer's free-claude-code](https://github.com/Alishahryar1/free-claude-code) on GitHub. The source video I worked from called it "free-cloud-code" — that's a transcription artifact. The actual project name is free-claude-code, by Alishahryar1. I want to flag that up front because the wrong name will send you down a rabbit hole of forks and unrelated projects when you search.

There's a second thing the proxy does that's easy to miss: it normalizes provider errors. When DeepSeek throws a 429 rate-limit response, or NVIDIA NIM returns a malformed tool-call block, or Ollama silently truncates a long response, the raw error would crash Claude Code or send it into a confused retry loop. The proxy catches these, reshapes them into Anthropic-style errors, and Claude Code handles them gracefully. That single layer of error normalization is why the proxy approach works in practice and a naive "just point at OpenRouter" config falls over within ten minutes.

Now — the part that matters when you actually try this. The three backends each behave differently, and the trade-offs aren't symmetrical.

## The Three Backends, Side by Side

Before I walk through the setup, here's what you're actually choosing between. I tested all three with the same project — Habitual, the habit tracker — and here's how they shook out.

### NVIDIA NIM: Free, but You Sign Up

NVIDIA NIM is the surprise winner of the free-tier game. As of April 2026, NVIDIA's [build.nvidia.com platform](https://build.nvidia.com/) hosts more than 100 models with free API access for anyone in the NVIDIA Developer Program. No credit card. No paid plan. You sign up, grab a key, and you get 1,000 free inference credits with a rate limit of 40 requests per minute on the free tier.

The headline model on NIM right now is GLM-5 (744 billion parameters), Z.ai's frontier MoE model that was open-sourced in February 2026. The benchmark numbers are real — GLM-4.7 (the previous generation) hits 73.8% on SWE-bench and 41% on Terminal Bench 2.0, per the [Cerebras GLM-4.7 announcement](https://www.cerebras.ai/blog/glm-4-7). GLM-5 and the more recent GLM-5.1 are noticeable steps above that. For coding tasks routed through Claude Code, GLM models punch well above their cost class.

The downside: NIM is slower than OpenRouter. Streaming is fine. Cold starts on the bigger models can take 8-12 seconds before the first token. For a Claude Code agentic loop with dozens of tool calls per minute, those seconds add up.

### OpenRouter: Cheap, Fast, Sprawling

OpenRouter is the gateway I default to. It's an aggregator — you get one API key, one endpoint, and access to almost every commercial and open-source model through a single billing account. The pricing is the closest the LLM world gets to a commodity market.

Real numbers from [OpenRouter's pricing page](https://openrouter.ai/deepseek/deepseek-v4-flash) as of May 2026:

- **DeepSeek V4 Flash:** $0.14 per million input tokens, $0.28 per million output tokens
- **DeepSeek V3.2:** $0.252 input / $0.378 output per million tokens
- **DeepSeek V4 Pro:** $0.435 input / $0.87 output per million tokens (promotional rate, rising to $1.74 / $3.48 after May 5, 2026)
- **DeepSeek R1:** $0.70 input / $2.50 output per million tokens

Compare those numbers to Anthropic's published [API pricing](https://platform.claude.com/docs/en/about-claude/pricing): Opus 4.7 is $5 input and $25 output per million tokens. Sonnet 4.6 is $3 input and $15 output. Even Haiku 4.5 at $1/$5 is more expensive than DeepSeek V4 Flash by a wide margin.

For Habitual, I burned through roughly 850,000 tokens across the full build — planning, scaffolding, three iterations, debugging, polish. On Opus 4.7 that would have run me about $7-9 in API cost. On DeepSeek V4 Flash through OpenRouter, it cost me 23 cents. Twenty-three cents. I have to keep saying it because every time I do the math I'm still slightly stunned.

A correction worth making: the source video I built this post from referred to "Anthropic's DeepSeek Flash V4." DeepSeek V4 Flash is a DeepSeek model, not an Anthropic one. DeepSeek and Anthropic are completely separate companies — DeepSeek is a Chinese AI lab, Anthropic is the company that makes Claude. The naming gets blurry in fast-moving content, but the distinction matters because it changes how you think about data flow and which terms of service apply.

### Ollama: Free, Private, Hardware-Bound

Ollama runs models entirely on your local machine. No API call leaves your laptop. No third party sees your code. For sensitive or NDA work, this is the only option on the list that's actually private.

The catch is hardware. The current generation includes Llama 4 (Meta's flagship) and Gemma 4 (Google's open-weight series). On Apple Silicon, Ollama v0.19+ automatically uses Apple's MLX framework for faster inference — that's the unlock that makes local models genuinely usable on a MacBook. Per [Unsloth's Gemma 4 documentation](https://unsloth.ai/docs/models/gemma-4), the smaller variants — E2B and E4B — hit roughly 95 and 57 tokens per second respectively on a current-gen Mac. The 26B variant is recommended for 24GB+ of unified memory.

I ran Gemma 4 12B locally on a M-series MacBook with 32GB of RAM. Single-file edits and small refactors worked fine. The moment Claude Code tried to load four files into context for a multi-file change, the model started slowing to a crawl — 8 to 12 seconds between tool calls, and the output quality dropped on anything that required holding more than two files in mind at once.

If you have a workstation with a discrete NVIDIA GPU, this story changes completely. On a desktop with a 4090 or better, Llama 4 70B is genuinely fast and the quality holds up across complex tasks. On a MacBook without that GPU? Use Ollama for short, focused tasks where privacy matters. Use OpenRouter or NIM for anything else. There's also a known [streaming bug in Ollama v0.20.3](https://github.com/ollama/ollama/issues) that mishandles Gemma 4's tool-call responses — pin to v0.19.x or wait for the fix if you hit it.

Now — the actual setup.

## Step-by-Step: Getting the Proxy Running

This is the section I'd want if I were starting from scratch. I'm assuming you already have Claude Code installed. If you don't, install it first via the [official Anthropic instructions](https://docs.claude.com/en/docs/claude-code) — the proxy doesn't replace Claude Code, it sits underneath it.

### Step 1: Install Prerequisites

You need Python 3.14 and `uv` (the fast Python package manager). On macOS:

```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install Python 3.14 via uv
uv python install 3.14
```

On Windows PowerShell:

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
uv python install 3.14
```

The reason for `uv` instead of `pip`: it's roughly 10-100x faster for resolving dependencies, and the proxy repo's `pyproject.toml` is set up for it. You can use `pip` if you insist, but `uv` makes the install instant.

### Step 2: Clone the Repo

```bash
git clone https://github.com/Alishahryar1/free-claude-code.git
cd free-claude-code
cp .env.example .env
```

That `.env.example` → `.env` step is where most people get tripped up. The `.env` file is hidden by default on macOS. If you can't see it in Finder, hit `Cmd+Shift+.` (period) to toggle hidden files. In the terminal, `ls -la` shows it. In VS Code, it shows by default. This sounds trivial, but I lost ten minutes the first time wondering why my edits weren't sticking — I was editing a file in a different directory.

### Step 3: Get Your API Keys

Pick a backend. You can configure all three later, but start with one to confirm the proxy works.

**OpenRouter:** Sign up at [openrouter.ai](https://openrouter.ai), add $5 of credit (it'll last you weeks), and grab an API key from the dashboard. Five dollars on OpenRouter is enough to build several full applications through DeepSeek V4 Flash.

**NVIDIA NIM:** Sign up at [build.nvidia.com](https://build.nvidia.com/), join the NVIDIA Developer Program (free), and generate an API key. You get 1,000 free credits immediately. No credit card required.

**Ollama:** Install from [ollama.com](https://ollama.com), then pull a model. For coding on a MacBook with 16-32GB RAM, start with `ollama pull gemma4:e4b`. For a workstation with serious GPU, `ollama pull llama4:70b`. No API key needed — Ollama runs locally.

### Step 4: Edit `.env`

Open `.env` in your editor of choice. The file has commented-out blocks for each backend. You uncomment the one you want and fill in the key and model name. Here's a working OpenRouter config:

```bash
# Backend selection
PROVIDER=openrouter

# OpenRouter
OPENROUTER_API_KEY=sk-or-v1-xxxxxxxxxxxxxxxxx
OPENROUTER_MODEL=deepseek/deepseek-v4-flash

# Proxy port
PORT=8082
```

For NVIDIA NIM, swap to:

```bash
PROVIDER=nvidia_nim
NVIDIA_API_KEY=nvapi-xxxxxxxxxxxxxxxxx
NVIDIA_MODEL=zai-org/glm-5
PORT=8082
```

For Ollama (no key needed):

```bash
PROVIDER=ollama
OLLAMA_MODEL=gemma4:e4b
OLLAMA_HOST=http://localhost:11434
PORT=8082
```

### Step 5: Start the Proxy

From the repo directory:

```bash
uv run main.py
```

You should see something like `Uvicorn running on http://0.0.0.0:8082`. Leave this terminal open — the proxy needs to keep running while you use Claude Code.

### Step 6: Point Claude Code at the Proxy

In a new terminal, set the environment variable that tells Claude Code where to send requests, then launch:

```bash
export ANTHROPIC_BASE_URL=http://localhost:8082
export ANTHROPIC_API_KEY=anything
claude
```

The `ANTHROPIC_API_KEY` value doesn't matter — the proxy ignores it and uses the real key from `.env` to authenticate with the actual backend. But Claude Code refuses to start without *some* value in that variable, so set it to anything non-empty.

If you want this to be the default for a project, drop those exports into a `.envrc` file (with [direnv](https://direnv.net/)) or into a project-local startup script. I keep a `claude-cheap` shell alias that exports both vars and launches Claude Code in one go.

That's the whole setup. Six steps, maybe ten minutes if you're moving carefully. The first time I ran through it took me about twenty — most of that was the hidden `.env` file confusion and one wrong model name in the OpenRouter config that returned a confusing 404.

If you've followed any of my other [Claude Code workflow guides](https://www.mejba.me/claude-code-32-power-user-hacks), this is the same Claude Code you already use — every command, every slash command, every skill. The only difference is which model is doing the inference under the hood.

## What It Was Like to Actually Build Habitual

I want to talk through the real build, not because the app matters but because the rough edges only show up when you push through a real project. Habitual is a habit tracker — daily check-ins, streak counting, a calendar heatmap, the standard stuff. Here's how it went on each backend.

### DeepSeek V4 Flash via OpenRouter

This was the workhorse. I started with a one-paragraph prompt: "Build me a minimal habit tracker, single user, web app, Tailwind, vanilla JS, daily check-in with streak counter and a 30-day calendar grid." Claude Code (running through the proxy on DeepSeek V4 Flash) planned the structure, scaffolded the files, wrote the HTML and JavaScript, and ran it.

First iteration was rough — the streak logic was off-by-one and the calendar grid had a UTC vs local-time bug. I asked it to fix both. It fixed them. I asked it to add a "skip day" option that doesn't break the streak (rest days for sustainable habits). It refactored the streak calculation cleanly.

Three iterations later, working app. Total token spend: 850,000 tokens, roughly evenly split between input and output. Total cost: about $0.21. The output quality was noticeably below what Opus 4.7 would produce — variable names were a bit generic, comments were sparser, error handling was minimal — but for a side project where I'm going to refactor anything I keep, that's fine.

The one place DeepSeek V4 Flash visibly struggled was when I asked it to add IndexedDB persistence with proper migration logic. Opus 4.7 would have written the schema, the migration runner, and the version handling in one pass. DeepSeek wrote the schema fine, missed the migration entirely, and I had to ask twice before it implemented something that would survive a schema change. For most CRUD work, the gap is invisible. For anything requiring careful systems thinking, you'll feel the difference.

### GLM-5 via NVIDIA NIM

Once the app worked, I rebuilt the same project from scratch on NIM with GLM-5 to compare. GLM-5 is, on paper, a much larger model than DeepSeek V4 Flash — 744B parameters MoE versus DeepSeek's smaller flash variant. And the output quality showed it. The variable names were better. Comments were more thoughtful. The IndexedDB implementation was correct on the first pass.

The trade-off was speed. Each tool call took noticeably longer — call it 4-8 seconds versus 2-3 on OpenRouter. For an agentic workflow with 40+ tool calls in a session, that compounds. The full build took roughly twice as long, even though the model needed fewer back-and-forths because the output was higher quality the first time.

NIM is also free at the developer-credit tier, which makes the speed trade-off easier to accept. If I were going to do production-grade open-weight inference, GLM-5 on NIM would be my default. For rapid iteration on disposable side projects, OpenRouter wins on responsiveness alone.

### Gemma 4 12B via Ollama

This is where the trade-offs got loud. On my MacBook with 32GB of unified memory, Gemma 4 12B loaded fine and ran the small stuff — single-file edits, simple refactors, naming things — at roughly 30-40 tokens per second. Genuinely usable.

The moment Claude Code tried to load four or five files into context for a multi-file refactor, things broke down. The streaming slowed dramatically. Tool calls started timing out. One of them silently truncated mid-response, which is the failure mode the proxy can't fully fix — Ollama just returns less than it should and Claude Code thinks the task is done.

For Habitual specifically, Gemma 4 12B got through the initial scaffold but couldn't handle the streak logic refactor without me hand-feeding it one file at a time. On a workstation with a 4090, this would be a non-issue. On a MacBook without a discrete GPU, treat local Ollama as a tool for short, focused tasks rather than a Claude Code daily driver.

There's a deeper conversation about local-first AI development worth having — I went into it more in [my Gemma 4 local setup post](https://www.mejba.me/gemma-chat-offline-vibe-coding-mac) — but the short version is: Apple Silicon is genuinely impressive for local inference, and it's still not a substitute for cloud-tier models on agentic coding workflows.

## The Real Power Move: Multi-Model Orchestration

Here's where this gets interesting beyond saving money. Once you have a proxy that can route to different models, you're one step away from the orchestration pattern that's quietly becoming the default for serious AI development.

The idea: use Opus 4.6 (or 4.7) on your paid Anthropic subscription as the planner and orchestrator. Have it break a complex task into discrete subtasks. Then delegate each subtask to a cheap model — DeepSeek V4 Flash, GLM-5, whatever — through the proxy. Opus reviews the results, integrates them, and only burns its expensive context on the high-leverage thinking.

In practice, this looks like running two Claude Code instances side by side. One is your main session on Anthropic-direct, doing the orchestration and the architectural decisions. The other is the proxy session running on a cheap backend, executing the routine implementation work — generate the test file, write the boilerplate, update the docs, refactor this one function. You hand subtasks from the first to the second, then come back to the first to integrate.

I've been doing variations of this for a few months. The token math gets compelling fast. A typical day where I might burn 3-4 million tokens of Opus on subscription becomes maybe 800K of Opus (the planning, the integration, the hard parts) and 3M of DeepSeek through the proxy (the execution, the boilerplate, the routine refactors). The output quality is indistinguishable on the finished product. The time spent is similar. The cost difference is dramatic.

This pattern echoes what I covered in [Anthropic's agent SDK guide](https://www.mejba.me/anthropic-agent-sdk-guide) — the future of agentic coding isn't "one giant model does everything," it's "one good model orchestrates many cheap models." The proxy is the missing piece that makes this practical without writing custom orchestration code.

## What This Approach Gets Wrong (the Honest Part)

I'd be lying if I said this was a drop-in replacement for paid Claude Code. There are real limitations and you should know them before you commit a real project to this stack.

**Cheap models miss fine detail at the edges of complex tasks.** I mentioned this with the IndexedDB migration. It shows up most in: complex async/concurrency reasoning, subtle security implications, deep refactors that touch many files at once, and anything requiring a strong grasp of architectural trade-offs. For 70-80% of typical coding work, you won't notice. For the hard 20%, you'll feel the gap.

**Some Claude Code features assume Anthropic-direct.** Fast mode (the new low-latency tier in Claude Code 2.x) errors out on non-Anthropic backends because it depends on Anthropic-specific streaming optimizations. Some skills and plugins that use Claude-specific tool-use formats may behave oddly. Most of the core features — file editing, sub-agents, slash commands, the skill system — work fine. Edge cases will trip you up.

**Long sessions degrade on cheap models faster than on Opus.** I've found that around 50,000 tokens into a session, output quality on DeepSeek V4 Flash starts noticeably dropping. The model loses track of earlier decisions, contradicts itself, and starts hallucinating function signatures from earlier in the conversation. The fix is mechanical: when you hit that wall, exit Claude Code, restart with a fresh context, and reload the relevant files. On Opus 4.7's 1M context window, this is rarely necessary until much deeper into a session.

**Local models on a MacBook without a GPU are genuinely slow.** I covered this in the Ollama section but it's worth repeating. Treat Ollama-on-MacBook as a privacy tool, not a productivity tool. For real local inference performance, you need NVIDIA GPU hardware.

**Privacy and data flow change.** When you route through OpenRouter or NIM, your prompts and code pass through their infrastructure. OpenRouter has reasonable privacy policies and doesn't train on your data by default, but it's an additional hop and an additional company seeing your code. For client work, NDA work, or anything sensitive: stay on Anthropic-direct or go full Ollama-local. No middle ground.

**Subscription savings have a ceiling.** If you're already paying for Max ($100-200/month), you're not going to save the whole subscription by routing personal projects through the proxy — you'll still want Max for client and production work. The savings show up as "I'm not buying API credits on top of my subscription for personal experiments anymore," which is real but bounded.

The model lineup is also moving fast. The names in this post — DeepSeek V4 Flash, GLM-5, GLM-5.1, Gemma 4 — were current as of May 2026. By the time you read this, there may be newer flagships at lower price points. Check OpenRouter's pricing page and the NVIDIA NIM model catalog before you commit to a specific model in your `.env`.

## What I'd Tell Someone Starting Today

If you're thinking about trying this, here's the path I'd take if I were starting over.

Spend ten minutes on the OpenRouter setup with DeepSeek V4 Flash. Drop $5 in credit. Get the proxy running. Build something small — a script, a tiny app, anything you'd otherwise burn a few minutes of subscription quota on. Notice how it feels. The friction is almost zero, and the cost is almost zero.

If that experience hooks you (it hooked me), set up NVIDIA NIM as your second backend. The free tier with GLM-5 is genuinely good and gives you a higher-quality fallback when DeepSeek V4 Flash isn't enough. Switching backends is one line in `.env` and a proxy restart.

Don't bother with Ollama unless you have a workstation with a real GPU or you specifically need local-only privacy. On a MacBook, the experience is going to disappoint you and that's the wrong first impression for what's otherwise a great toolchain.

Once you're comfortable, try the orchestration pattern. Run Opus on Anthropic-direct for the planning and architecture. Run DeepSeek through the proxy for the execution. The cost savings compound quickly and the workflow feels surprisingly natural after a few days.

And keep paying for Max for the work that matters. This proxy approach isn't a replacement for what Anthropic charges you for — reliability, support, the absolute best-in-class model on the hard problems. It's a way to extend your experimentation budget by an order of magnitude without giving up the Claude Code interface you already love.

The Habitual app is still on my phone. I check it every morning. It cost me 23 cents to build, runs entirely in my browser, and I genuinely use it. If next week I want to add habit categories or weekly reports or a notification system, I'll fire up the proxy, point Claude Code at OpenRouter, and iterate for another fistful of pennies. That's the unlock.

The rest of 2026 is going to be a wild ride for AI tooling. Models are getting cheaper. Open-weight options are catching up to frontier closed models faster than anyone predicted. The proxy approach is the right shape for this moment — keep the best UX, route to whatever model makes sense for the task, and let the market for inference do its thing.

Build the side project. Build it through the proxy. Spend the dollar you saved on coffee.

## Frequently Asked Questions

### How much does it cost to run Claude Code through the free Claude Code proxy?
For most personal projects, costs run from absolutely free (NVIDIA NIM credits or Ollama local) to under $1 per app build (DeepSeek V4 Flash on OpenRouter at $0.14/M input tokens). The same project on direct Anthropic API would cost $5–$10. Setup is free; your only spend is the inference at whichever backend you pick.

### Does the proxy work with all Claude Code features?
Most features work — file editing, sub-agents, slash commands, skills, MCP servers, multi-file workflows. The notable exception is fast mode, which depends on Anthropic-specific streaming optimizations and errors on non-Anthropic backends. Some plugins that use Anthropic-specific tool-use formats may also behave oddly. See the implementation walkthrough above for the full setup.

### Is it safe to send my code through OpenRouter or NVIDIA NIM?
For personal projects and open-source work, both providers have reasonable privacy policies and don't train on your data by default. For client work, NDA-bound code, or anything with credentials, stay on Anthropic-direct or use Ollama locally — your code never leaves your machine with Ollama. There's no safe middle ground for sensitive work.

### What's the best free model to use with the proxy?
For coding, GLM-5 on NVIDIA NIM is the strongest free option — 744B parameters, frontier-class quality, free with developer signup. DeepSeek V4 Flash on OpenRouter is the cheapest at $0.14/M input tokens (not free, but close), and it's faster than NIM. For local-only, Gemma 4 E4B on Ollama works on Apple Silicon but has hardware limits.

### Can I switch between backends without reinstalling?
Yes. Change the `PROVIDER` value in your `.env` file, restart the proxy with `uv run main.py`, and Claude Code will use the new backend on its next request. You don't need to restart Claude Code itself. Many users keep multiple `.env` files (`.env.openrouter`, `.env.nim`, `.env.ollama`) and symlink the active one.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
