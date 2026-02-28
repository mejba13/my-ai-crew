**BRAND:** mejba.me
**TITLE:** 10 CLI Tools I Use Daily with Claude Code
**SLUG:** cli-tools-claude-code
**TAGS:** Developer Tools, Terminal Productivity, Claude Code, CLI Workflow, Tutorial

---

Three monitors. Seventeen terminal tabs. One very confused brain.

That was my setup six months ago when I went deep into Claude Code for the first time. I thought more windows meant more productivity. iTerm2 sprawled across my entire desk. VS Code on one screen. A browser on another. And my terminal — a chaotic mess of `cat` commands and half-remembered directory paths — on the third.

I was technically "productive." But honestly? It felt like building a spaceship with a butter knife.

The breakthrough didn't come from learning a new programming language or discovering some obscure Claude Code feature. It came from building a proper CLI environment — a set of terminal tools that turned my workflow from frantic window-switching into something that actually felt smooth and intentional.

Here's what I wish someone had told me earlier: Claude Code lives in the terminal. When you treat the terminal as a first-class environment and tool up accordingly, everything changes. The feedback loops get tighter. Context switches disappear. You stay in the zone longer.

I've been testing and collecting these tools for months. Some I use every single day. A few I tried and abandoned — I'll tell you which ones and why, because that's more useful than just a curated "best of" list. And one of them, which I'm saving until we get to the honest section, became so essential that I can't imagine an agentic session without it.

---

## Why Most Claude Code Users Are Flying Blind

Before we get to the tools, let me describe the problem more precisely — because I think a lot of developers are solving the wrong problem entirely.

When you're running Claude Code, you're managing a live collaboration between your thinking and an AI agent that's actively writing, editing, and rearranging files. That means you need to answer three questions in real time, almost simultaneously:

1. What is Claude actually doing to my codebase right now?
2. Where am I in my file system, and where do I need to be?
3. How are my system resources holding up under the load?

Most developers answer these by tabbing between windows, running `git status` every 30 seconds, and hoping for the best. That's fine for quick sessions. But when you're running longer agentic loops — Claude refactoring a service, generating content, scaffolding entire features — you need better instrumentation.

The tools I'm sharing here are the ones that answer those three questions without breaking your flow. I installed most of them with Homebrew on my Mac, and I'll give you exact install commands throughout. Most have Linux equivalents for headless server setups.

One honest admission upfront: I don't use all twelve of these every single day in every project. Some are context-dependent. I'll flag which are daily drivers and which are situational — because I'd rather give you a truthful toolkit than one that sounds impressive but doesn't reflect how real work actually happens.

But before we get to specific tools, you need to understand the mental model that makes this stack work. Without it, you'll install everything and use nothing.

---

## The Three-Layer Terminal Stack

Here's how I think about my CLI environment when working with Claude Code. Every tool fits into one of three layers:

**Layer 1: Awareness** — Knowing what's happening to your code and system in real time.
**Layer 2: Navigation** — Getting where you need to be, fast, without thinking about it.
**Layer 3: Intelligence** — Understanding your AI tools, model options, and resource costs.

Most developers only have Layer 2 covered — they know `cd` and `ls`. The ones running efficient Claude Code sessions have all three wired up. Let me walk through each layer and the tools that serve it.

---

## Layer 1: Awareness — Knowing What's Actually Happening

### Lazygit: The Tool I Can't Open Claude Code Without

Open Lazygit before you start any Claude Code session. Just run `lazygit` in your project directory and you get a full terminal UI showing your repo status, staged changes, commit history, branches, and stashes — all in one view, all updating in real time.

Why this matters specifically for Claude Code: when you're running an agentic session and Claude is making changes, Lazygit becomes your live window into what's actually happening. You can see file modifications as they occur. Review diffs without leaving the terminal. If Claude does something unexpected — and it will, eventually — you see exactly what changed before you decide whether to keep or roll back.

```bash
brew install lazygit
```

Navigation is dead simple: `j`/`k` to move up and down, `enter` to drill in, `space` to stage, `c` to commit. The learning curve is twenty minutes, not twenty days.

Something I didn't expect: Lazygit made me commit more frequently during Claude sessions. Watching changes accumulate in real time creates a natural rhythm. I checkpoint more often now, which means rolling back is less scary — which means I let Claude be more aggressive with refactors. That's a compounding benefit that took me a while to notice.

### Btop: System Monitoring That Actually Tells You What's Wrong

When I first started running longer Claude Code sessions, my MacBook would start crawling after about 45 minutes. I had no idea why. Was it the LLM API calls? Node.js memory leaks? Too many background processes competing for RAM?

Btop answered that in about 10 seconds.

```bash
brew install btop
```

Run `btop` and you get an interactive terminal dashboard showing CPU usage per core, memory consumption by process, disk I/O, and network traffic — all in one view. Hit `t` to toggle different display panels. Hit `e` to expand process details.

For Claude Code users: keep Btop running in a terminal split during your agent sessions. You'll quickly learn what "normal" resource usage looks like for your setup, and you'll catch runaway processes before they crash your session.

Pro tip: Btop's memory view was how I discovered that a particular npm package I was using was leaking memory aggressively during Claude's test runs. Fixed it in 20 minutes once I could actually see it happening. Without visibility, I would have kept blaming Claude.

---

## Layer 2: Navigation — Getting Everywhere Without Thinking

### Zoxide: Directory Navigation That Learns From You

Here's a workflow that embarrassed me for years: `cd ~/projects/clients/acme/backend/src/controllers`. Every time. In full. Even after visiting that directory 200 times.

Zoxide fixes this permanently.

```bash
brew install zoxide
```

Add this to your `~/.zshrc`:

```bash
eval "$(zoxide init zsh)"
```

After installation, once you've visited a directory, you can jump back to it with just a fragment: `z controllers` and Zoxide figures out where you mean based on your navigation history. The `zi` command gives you an interactive fuzzy search of your entire jump history.

For Claude Code: when you're context-switching between multiple project directories during a session — checking one project's output while Claude generates files for another — Zoxide cuts navigation time from "multiple seconds of typing" to a keystroke and a word.

I underestimated this one when I first installed it. Now it's pure muscle memory. The kind of tool where you notice its absence immediately when working on a fresh machine.

### Ranger: A File Browser That Lives in Your Terminal

Ranger gives you a three-pane file browser inside your terminal. The left pane shows the parent directory, the center shows the current directory, and the right shows a preview of the selected file. Navigate with `h`/`j`/`k`/`l` (Vim-style), open files with `enter`, preview text files without running a separate command.

```bash
brew install ranger
```

I use this in two specific scenarios: when exploring a project structure I don't know well (Ranger gives me a spatial understanding of the codebase that flat `ls` never provides), and when Claude has generated a batch of new files and I want to quickly review the structure and spot anything out of place.

For Linux or headless environments specifically, Ranger is basically essential. It replaces the GUI file manager you'd normally reach for.

### EZA: What `ls` Should Have Been From the Start

`eza` is a modern replacement for `ls` with icons, color-coded permissions, directory grouping, and git status integration baked in.

```bash
brew install eza
```

Then add these aliases to your `~/.zshrc`:

```bash
alias ls='eza --icons --group-directories-first'
alias ll='eza -l --icons --group-directories-first'
alias la='eza -la --icons --group-directories-first'
alias lt='eza --tree --icons --level=2'
```

The difference is immediately visible: file type icons, directories grouped at the top, and — this is the part I love — if you're in a git repo, eza shows which files are modified, staged, or untracked right in the file listing. No separate `git status` needed for a quick visual check.

Small quality-of-life upgrade. But you'll wonder how you worked without it after a week.

---

## Layer 3: Intelligence — Understanding Your AI Tools and Resources

This is the layer most developers skip entirely, and it's where the real leverage lives when you're running AI-heavy workflows.

### LLMFit: Know What Your Hardware Can Actually Handle

LLMFit is a CLI tool that scans your hardware and tells you which local AI models you can realistically run — with performance scores, estimated memory requirements, and parameter counts. No more guessing.

```bash
pip install llmfit
```

Run `llmfit scan` and you get a table: model name, parameter count, memory requirement, estimated performance score on your specific hardware, and compatibility status given your current available RAM.

Why this matters: I wasted two hours once trying to run a 13B parameter model on a machine that technically had enough total RAM but not enough free RAM after system overhead. The model kept crashing during inference. LLMFit would have flagged that immediately and suggested a 7B alternative.

For Claude Code users working with local models via Ollama or LM Studio alongside Claude's API: run LLMFit once when setting up a new machine or experimenting with model sizes. It saves debugging sessions you don't need.

### Models CLI: AI Provider Comparison Without a Browser Tab

The `models` CLI gives you a terminal-based comparison of AI model providers — pricing per token, context window sizes, benchmarks, and changelogs — without opening a browser.

```bash
npm install -g models-cli
```

The use case is specific but valuable: when deciding which model to use for a particular agent task, being able to pull up a quick comparison without context-switching to a browser tab keeps you in flow. I use this mostly for cost analysis. When a high-volume content generation workflow is getting expensive with Claude Opus, I can quickly compare whether a smaller model is viable for a particular subtask — right from the terminal, mid-session.

### Taproom: Your Installed Homebrew Packages, Organized

Taproom shows every Homebrew package and cask installed on your Mac, organized clearly and searchably.

```bash
brew install taproom
```

Run `taproom` for a clean list of your installed formulae and casks. Run `taproom | grep ranger` to check whether something's already installed. I use this more than I expected — primarily when setting up new machines and when I can't remember whether I've already installed something. It's saved me from running duplicate installs and helped me replicate my environment quickly when switching between machines.

---

At this point, you have the full mental model and the nine core tools. But here's where it gets genuinely useful — the last three tools are the ones most people don't think to install, and two of them changed specific parts of my workflow more than anything else.

---

## The Underrated Trio: Markdown, Images, and Data

### Glow: Read Claude's Markdown Output Like It Was Meant to Look

When Claude generates documentation, blog posts, or READMEs, I read them with Glow instead of `cat`. The difference is significant.

```bash
brew install glow
```

Run `glow yourfile.md` and Glow renders proper markdown formatting — headers look like headers, code blocks have syntax context, bold text is actually bold. No more squinting at raw asterisks and backticks.

For content workflows specifically, this is essential. When I'm reviewing 3,000-word posts before publishing, reading them in Glow versus raw markdown is the difference between catching formatting issues and missing them entirely.

### Neovim: Power Navigation for Long Documents

Neovim is the advanced option, and I won't pretend the learning curve is gentle — it's not. But if you're comfortable with Vim keybindings, Neovim gives you navigation superpowers for long markdown files: jump between headers with `]]` and `[[`, fold sections with `za`, search within the file with `/`. For long-form documents that Claude generates, this is faster than any GUI editor for quick structural edits.

```bash
brew install neovim
```

Honest take: if you've never used Vim before, install Glow first, use it daily for two weeks, then consider Neovim. Don't let the cool factor push you into a tool you're not ready for. Glow will serve most people just fine — and a basic `nvim` invocation without plugins handles quick edits cleanly.

### Shua and CSV Lens: Visual and Data Tools for the Terminal

**Shua** renders images directly in your terminal. More niche, but if you're working with Claude on image-related tasks — reviewing screenshots, previewing generated assets, checking visual output — being able to preview images without leaving the CLI is a real convenience. Compatibility depends on your terminal emulator (works cleanly in iTerm2).

**CSV Lens** gives you an interactive TUI for CSV files:

```bash
cargo install csvlens
# If you don't have Rust installed: brew install rust first
```

Navigate with arrow keys, search with `/`, quit with `q`. If you're working with data in your Claude Code sessions — exported logs, structured data, test fixtures — CSV Lens lets you browse and filter without opening Excel or Numbers. Surprisingly fast even on larger CSVs.

---

## The Honest Section: What I Got Wrong

Here's what I wish someone had told me before I went tool-crazy.

**Mistake #1: Installing everything at once.** My first attempt at building a CLI toolkit was a disaster. I installed about 20 tools in one afternoon, aliased half of them, and then kept reaching for the same three tools I'd always used. The new tools just sat there, theoretically available, never actually triggered by habit.

What fixed it: installing one new tool per week. Specifically, removing my existing alias for something and forcing myself to use the replacement. `ls` became `eza` for two weeks before I installed Zoxide. Forced immersion. It works.

**Mistake #2: Overengineering Neovim.** Neovim is genuinely powerful. I use it. But I burned about six hours trying to set up the "perfect" Neovim config with LSP servers, plugins, and a fully customized theme — for markdown editing. That was overkill. Glow handles reading. A plain `nvim` handles quick edits. You don't need the full IDE setup unless you're using Neovim as your primary editor for everything.

**Misconception about LLMFit:** A few people I know assumed it gives a definitive guarantee of what a model will do on their hardware. It's more like a well-informed estimate. Memory usage varies based on quantization, context length, and what else is running. Treat it as a starting point, not a contract.

The unpopular opinion: not every Claude Code user needs this full toolkit. If you're doing light sessions — quick questions, small edits — a well-configured VS Code terminal is probably enough. This stack is for people running extended agentic sessions, managing content systems, or doing heavy file manipulation across multiple projects. Know your actual workflow before you optimize for one you don't have.

And the tool I dropped that I mentioned at the start? Mac Top. A macOS-specific system monitor that, after a two-week test, I found less comprehensive and less customizable than Btop for my needs. Not a bad tool — just the wrong fit for what I needed from system monitoring during AI sessions.

---

## What Actually Changed After 90 Days

Ninety days in, here's what I can honestly report:

**Context switches dropped noticeably.** Before: tabbing to GitHub Desktop to check repo state, switching to Finder for file browsing, opening a browser for model pricing comparisons. After: Lazygit, Ranger, and the Models CLI handle all three without leaving the terminal. Fewer context switches means longer unbroken focus windows.

**I catch Claude's mistakes faster.** Lazygit in a terminal split shows me diffs in real time. When Claude makes an unintended change — which happens, especially during longer sessions — I see it within seconds, not after the whole session completes. Early detection means smaller rollbacks and less rework.

**Environment replication went from hours to minutes.** When I spun up a new dev machine last month, I had my entire CLI environment running in about 25 minutes: Homebrew bundle, shell config lines, a few manual installs. Without this deliberate, documented toolkit, that would have taken half a day of "wait, what did I have installed?"

**Content workflow specifically:** for the blog system I run on mejba.me, Claude generates markdown files that I review with Glow, check into git via Lazygit, and organize with Ranger. That cycle — generate, review, commit, organize — used to take me about four minutes per post. Now it's under 90 seconds. Over dozens of posts per month, that compounds into hours saved.

Quick wins appear immediately after install (Zoxide alone saves real minutes per day). The deeper gains — better situational awareness, faster debugging, less cognitive overhead — emerge over weeks as these tools become automatic.

---

## Your Next Move

Pick three tools from this list. Just three — not all twelve. Tonight.

Specifically: Lazygit, Zoxide, and Glow. Install those three and they'll change the texture of your next Claude Code session in ways that are immediately noticeable. Use them for two weeks before you add anything else. Resist the urge to install the full stack. Depth of adoption beats breadth of installation every time.

The developers I see running Claude Code most effectively aren't the ones with the most tools. They're the ones who've made a small set of tools completely automatic — where Lazygit is muscle memory, where Zoxide is just how navigation works, where reviewing Claude's output in Glow is the obvious thing to do.

Six months ago: three-monitor chaos, constant window-switching, flying blind through file changes. Now: one focused terminal split with tools that actually fit how I think.

What part of your current terminal workflow makes you wince a little every time you do it? Start there.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
