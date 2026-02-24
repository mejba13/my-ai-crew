**BRAND:** mejba.me
**TITLE:** How I Turned Obsidian and Claude Code Into My AI Brain
**SLUG:** obsidian-claude-code-second-brain
**TAGS:** AI Automation, Personal Knowledge Management, Claude Code, Obsidian, Tutorial

---

Three months ago, I asked Claude Code a question about a side project I'd been building. It gave me a decent answer. Generic, but decent. Then I pointed it at my Obsidian vault — 847 markdown files spanning two years of daily notes, project specs, meeting transcripts, and half-baked ideas scribbled at 1 AM.

The response it gave me next made me sit back in my chair.

It connected a concept from a filmmaking note I wrote in March 2024 to a startup idea I'd abandoned in November, then mapped both to a pattern in my daily notes I hadn't consciously noticed. I'd been circling the same problem from three different domains for eight months without realizing it.

That moment changed how I think about AI tools. Not as answer machines. As thinking partners that know your context deeply enough to show you what you've been missing about your own ideas.

Here's the thing — most developers use AI the same way every session: explain the project, re-state the constraints, re-describe what you've already tried. It's like having a brilliant colleague with amnesia. Every morning, you start from zero. I got tired of that cycle, and what I built to replace it has become the most valuable tool in my workflow. But the setup requires a mental shift most people aren't ready for, and I'll explain why that matters more than the technical details.

## Why Your AI Conversations Keep Starting From Scratch

The pain point is universal if you've spent any real time with Claude, ChatGPT, or any LLM-powered tool. You open a new session. You type three paragraphs of context. You get a response. Tomorrow, you do it again. And again. And again.

Web-based AI tools have "memory" features now, sure. But here's what nobody talks about — you can't fully control what's in that memory. You can't inspect it, edit it, or structure it. The AI decides what to remember based on its own heuristics. That's a black box sitting between you and your most important projects.

I've been building AI agents and automation systems for over two years now. The single biggest productivity unlock wasn't a better model or a fancier prompt. It was solving the context problem. When your AI partner has persistent, structured, *your-way-organized* context, the quality of everything it does jumps by an order of magnitude.

Obsidian and Claude Code solve this problem together in a way nothing else I've tried comes close to matching. The key insight is deceptively simple: your markdown files become the AI's long-term memory, and you control every byte of it.

The technical integration is straightforward, but the real power shows up when you start building custom commands that leverage the relationship graph between your notes. That's where things stop being "a cool trick" and start being genuinely transformative.

## The Obsidian Foundation — More Than a Note-Taking App

If you're not familiar with Obsidian, here's the short version: it's a markdown-based note-taking tool that manages a vault, which is really just a folder of `.md` files on your local machine. You own the files. No cloud lock-in. No proprietary format. Just text.

I started using Obsidian skeptically. Another note-taking app, right? I'd been through Notion, Roam Research, Bear, Apple Notes — the full graveyard of productivity tools that promise to change how you think and end up as another abandoned subscription.

What made Obsidian stick was one feature: **bi-directional linking with a visual graph**.

When you write `[[project-idea]]` inside a daily note, Obsidian creates a link. When you open `project-idea.md`, you can see every note that references it. The graph view shows these connections as an interactive web — nodes and edges mapping how your ideas relate to each other.

This sounds academic until you've used it for three months. Then you open the graph and see that your notes about "developer burnout" connect to your notes about "automation anxiety" which connect to your notes about a client project that went sideways — and suddenly you're seeing a pattern in your own thinking you'd never have spotted by scrolling through a folder.

The human brain is terrible at tracking these connections across hundreds of notes written over months. Obsidian makes them visible. That's the foundation. But the vault by itself is still passive — it waits for you to explore it manually.

That changes when Claude Code enters the picture.

## Claude Code — Your Computer Listens to English Now

Claude Code is an agent interface that lets you control your computer through natural language commands in a terminal. You type what you want in plain English, and it executes.

"Create a new file called `project-spec.md` with these sections." Done.
"Read my last three daily notes and summarize the action items." Done.
"Find every mention of 'authentication' across my vault and list the files." Done.

This isn't a chatbot. It's an agent with tool access — it can read files, write files, run commands, and chain operations together. The more context you give it, the more sophisticated the tasks it can handle autonomously.

I used Claude Code for months before connecting it to Obsidian, mostly for coding tasks and file management. Useful, but limited. The real breakthrough came when I realized the agent could consume my entire vault as context — not just the raw text, but the *relationship structure* between notes.

This is where Obsidian CLI enters the story, and where most tutorials on this topic stop being useful because they focus on the setup and miss the workflow design entirely. I'll cover both, but the workflow design is where 90% of the value lives.

## The Integration That Changed Everything

Obsidian released Obsidian CLI — a tool that exposes your vault to command-line interfaces. When Claude Code connects through it, the agent doesn't just see a pile of markdown files. It sees the entire link graph: which notes reference which, what's connected to what, where the clusters form and where the orphan notes sit alone.

Imagine handing someone a box of 800 sticky notes versus handing them a mind map with all 800 notes already organized by relationship. Same information, wildly different utility. That's the difference Obsidian CLI makes.

The first time I ran a command that asked Claude Code to "analyze my vault and identify themes I might be overlooking," it returned a report that included:

- **14 orphan notes** — ideas I'd written once and never connected to anything
- **3 unresolved links** — notes I'd referenced but never actually created
- **A cluster of 23 notes** around "AI-assisted creativity" that I hadn't tagged as a category
- **A contradiction** between what I wrote about work-life balance in June versus what my daily notes showed I was actually doing in July

That last one hit different. The AI wasn't telling me something I didn't know in the abstract — it was showing me evidence from my own writing that my beliefs and behaviors were misaligned. No productivity app had ever done that.

But pulling a one-time report isn't a workflow. The real system emerged when I started building custom commands.

## Building Commands That Think With You

Here's where this moves from "interesting experiment" to "I can't imagine working without it." Custom commands in Claude Code let you create reusable operations that load specific files, apply specific logic, and produce specific outputs.

I built five commands that I now run almost daily. Each one leverages the vault context differently.

**1. Morning Review**

This command reads my calendar integration, recent daily notes, any unresolved action items, and the current state of active projects. It then produces a prioritized task list — not a generic one, but one that accounts for what I've been working on, what's stalled, and what deadlines are approaching.

The crucial difference from any task manager: it notices patterns. "You've had 'refactor auth module' on your list for 9 days. Based on your notes, the blocker seems to be the API migration you mentioned on Tuesday. Should we address that first?"

No task app has ever connected those dots for me.

**2. End-of-Day Processing**

Before I close my laptop, this command scans the day's notes, extracts action items I might have buried in meeting notes or brainstorming sessions, surfaces any vault connections to what I worked on, and rates my confidence level on decisions I made based on how they align with my documented thinking.

That last part sounds weird until you experience it. I wrote a note in January saying "microservices are overkill for projects under 50K users." In March, I started architecting a microservices setup for a project with 12K users. The end-of-day processor flagged it. "Your vault contains a strong opinion against this approach for projects at this scale. Has something changed, or should you reconsider?"

I reconsidered. It was the right call.

**3. Belief Challenger**

This is the command that makes people uncomfortable when I show it to them. It takes a statement I'm currently operating on — "We should build the MVP in React Native" — and searches my vault for any notes, past decisions, or recorded experiences that contradict it.

Sometimes it finds nothing. That's confidence-building. Sometimes it surfaces a note from eight months ago where I wrote about the exact pain points I'm about to walk into. That's invaluable.

I learned this the hard way on a client project where I was advocating for a specific database architecture. My vault had three separate notes, spread over five months, documenting problems with that exact approach in similar contexts. I'd forgotten. The AI didn't.

**4. Idea Evolution Tracker**

This one maps how a concept has changed across my vault over time. I asked it to trace my thinking about "AI agents in production" and got back a timeline:

- **Month 1:** Skeptical. Notes focused on limitations and hallucination risks.
- **Month 4:** Cautiously experimenting. First successful automation documented.
- **Month 7:** Building daily. Notes shift from "can this work?" to "how do I scale this?"
- **Month 10:** Evangelizing. Writing about it, teaching it, building tools around it.

Seeing that evolution mapped out — with timestamps and quotes from my own notes — was genuinely moving. You don't realize how much your thinking shifts until someone (or something) shows you the trajectory.

**5. Cross-Domain Pattern Finder**

My vault spans software engineering, cybersecurity, design thinking, business strategy, and personal development. This command looks for concepts that appear across two or more unrelated domains and generates connections I haven't explicitly made.

Example output: "Your notes on 'defensive coding patterns' share structural similarities with your notes on 'brand identity consistency.' Both emphasize establishing boundaries early, creating predictable behavior, and building trust through reliability. There may be a framework that unifies these ideas."

I'd never have connected defensive coding to brand identity on my own. But the connection is real, and it sparked a blog post and a new approach to how I structure client projects.

If you've made it this far, you already understand why this isn't just another tool integration. The next section covers the exact setup so you can build this yourself.

## Setting Up the Full Pipeline

Here's the step-by-step. I'm assuming you have Obsidian installed with an active vault and basic familiarity with terminal commands.

**Step 1: Install Claude Code**

If you haven't already, install Claude Code via the official channel. You'll need an Anthropic API key or a Claude subscription that includes CLI access. The installation is straightforward — follow the docs at the official Claude Code page. Make sure it's working by running a simple command like "list the files in this directory."

**Step 2: Install Obsidian CLI**

Obsidian CLI is the bridge. Install it following the official Obsidian CLI documentation. Once installed, verify it can see your vault by running the vault listing command. You should see your vault name and path.

**Step 3: Create Your Context File**

Before building commands, create a single markdown file in your vault called `_context.md` (I prefix with underscore so it sorts to the top). This file should contain:

```markdown
# About Me — Context for AI Agents

## Current Role
Software engineer, AI developer. Building [current projects].

## Active Projects
- Project A: [one-line description and status]
- Project B: [one-line description and status]

## Current Priorities
1. [Priority with deadline]
2. [Priority with deadline]

## How I Work
- I prefer [tools, frameworks, approaches]
- I avoid [things you've learned don't work for you]

## Decision Log
- [Date]: Decided to [X] because [reason]
- [Date]: Changed from [Y] to [Z] after [what happened]
```

This file becomes the grounding context for every command you build. Update it weekly. Five minutes of maintenance saves hours of re-explaining.

**Step 4: Build Your First Command**

Start with the morning review. Create a command file that instructs Claude Code to:

1. Read `_context.md` for your current state
2. Read the last 3 daily notes
3. Check for any notes tagged `#action-item` or `#todo`
4. Cross-reference with your active projects
5. Produce a prioritized list with reasoning

The command structure will look something like this in your Claude Code configuration:

```
Read my context file at [vault-path]/_context.md, then read my last 3 daily
notes. Identify all action items, cross-reference with my active projects,
and produce a prioritized task list for today. Flag any items that have
been pending for more than 5 days.
```

**Pro tip:** Start simple. My morning review command started as three lines and grew to a full page over two months as I discovered what information was most useful. Don't over-engineer the first version.

**Step 5: Iterate Based on What You Actually Use**

Here's what most tutorials won't tell you: your first commands will be wrong. Not broken — wrong. You'll ask for things you think you want and discover the output isn't that useful. That's normal.

After the first week, I completely rewrote my end-of-day processor because the original version was summarizing my notes (boring) instead of challenging them (valuable). The iteration cycle is the system. Don't skip it.

**Common Errors and How to Fix Them:**

- **"Claude Code can't find my vault"** — Check that Obsidian CLI is pointing to the correct vault path. Run the vault listing command to verify.
- **"The output is too generic"** — Your context file needs more specifics. Vague input produces vague output. Add actual project details, not placeholder text.
- **"Commands take too long"** — Large vaults with thousands of notes will slow things down. Use tags or folder-specific queries to narrow scope.
- **"The AI hallucinates connections that aren't there"** — This happens occasionally. Add a line to your commands: "Only cite connections you can trace to specific notes. If uncertain, say so." The honesty instruction makes a measurable difference.

## What Most People Won't Tell You About This Setup

I want to be honest about the parts that aren't magic.

**Privacy is a real concern.** You're giving an AI agent access to your second brain. Daily notes, project specs, meeting transcripts, personal reflections — whatever's in your vault. I keep a separate vault for truly private content that never touches any AI tool. My "AI-accessible vault" contains professional content, project notes, and curated personal reflections. Never the unfiltered stuff.

You need to think about this before you start. What are you comfortable with an AI processing? Where's your line? There's no right answer, but there is a wrong approach: pretending the question doesn't matter.

**The "write to think" habit is non-negotiable.** This entire system falls apart if you don't write notes regularly. The AI can only work with what exists in your vault. If you take sparse, bullet-point notes once a week, the connections will be thin and the insights shallow. Daily writing — even just 200 words — is the fuel that makes this engine run.

I used to think daily notes were a productivity cult thing. Now I understand: they're not about productivity. They're about creating a persistent, queryable record of your thinking that compounds over time. The AI consumes what you produce. Write more, get more.

**You will resist letting AI challenge your ideas.** The belief challenger command made me uncomfortable the first dozen times I used it. Nobody likes being shown that their current plan contradicts their own documented experience. The temptation is to dismiss the AI's findings or stop using the command.

Don't. The discomfort is the point. A thinking partner that only agrees with you is useless.

**AI-generated content should stay separate from your original notes.** I maintain a strict rule: AI output goes in a dedicated folder, never mixed with my personal notes. If an AI generates a suggestion or draft, I edit it manually before it touches my main vault. The vault is my thinking, not the AI's. Mixing them corrupts the signal over time.

This might seem paranoid, but after watching the vault grow for months, I can tell you: knowing that every note in your main vault is genuinely yours creates a trust layer that's impossible to replicate if AI-generated text is sprinkled throughout.

## What Actually Changes When You Work This Way

After three months of daily use, here's what's measurably different.

**Decision speed increased.** Not because the AI makes decisions for me, but because it surfaces relevant context I'd otherwise spend 20 minutes hunting for. When a client asks "have we dealt with this before?" I can answer in 30 seconds instead of digging through old notes.

**Idea quality improved.** The cross-domain pattern finder consistently produces connections I wouldn't have made on my own. Not all of them are useful — maybe 30% lead somewhere interesting. But 30% of connections across 800+ notes is a lot of interesting places.

**Fewer repeated mistakes.** The belief challenger has caught me walking into the same trap three separate times. Each time, my vault had evidence that the approach was risky. Each time, I'd forgotten. The AI doesn't forget.

**Writing became more intentional.** Knowing that my notes feed an AI system that will analyze them later changed how I write. I'm more specific about decisions, more honest about uncertainties, and more consistent about linking related ideas. The feedback loop between writing quality and AI output quality is real and motivating.

**Time saved is significant but not where you'd expect.** I don't save much time on the actual AI interaction — the commands take a few minutes to run. The savings come from not re-explaining context, not re-researching my own decisions, and not losing ideas that would have disappeared into a forgotten note.

Quick wins show up in the first week. The real value compounds over months as the vault grows and the connection graph gets denser. Month one is useful. Month six is transformative.

## The Bigger Picture — Markdown Files as Your Operating System

Here's a thought that keeps me up at night in the best way possible.

Your markdown files are a more accurate representation of your thinking than your own memory. Human memory is reconstructive — we don't recall events, we rebuild them, often inaccurately, colored by current emotions and beliefs. Your notes from six months ago capture what you *actually* thought at the time, not what you remember thinking.

When an AI agent has access to that accurate record and can traverse the connections between thousands of notes, it achieves something that was previously only possible with a therapist or long-term collaborator who's been paying attention for years. It can show you patterns in your own thinking. It can identify when your current beliefs contradict your past experiences. It can find the thread connecting ideas you've been circling without recognizing they're the same idea.

My markdown files are quite literally the memories that power my AI thinking partner. Not training data. Not a prompt. Memories. Persistent, structured, interconnected context that turns a stateless language model into something that understands my work, my patterns, and my blind spots.

The people who invest in building and maintaining this kind of personal knowledge system are going to have a compounding advantage over the next five years. Not because the technology is exclusive — it's available to anyone with Obsidian and a Claude subscription. But because the habit of reflective writing, consistent note-taking, and honest self-documentation is hard. Most people won't do it.

The ones who do will have an AI partner that gets smarter — not because the model improves, but because the context deepens. Every note you write makes the system more valuable. Every connection you create gives the AI more to work with. The vault grows. The insights multiply. And somewhere around month three, you realize you're not just taking notes anymore.

You're building a second mind that actually thinks with you.

So here's my challenge: open Obsidian tonight. Write one daily note. Not a perfect one — just an honest record of what you worked on, what you're stuck on, and one idea that crossed your mind today. Do it again tomorrow. And the day after.

In thirty days, point Claude Code at that vault and ask it what patterns it sees.

I promise — what it finds will surprise you.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
