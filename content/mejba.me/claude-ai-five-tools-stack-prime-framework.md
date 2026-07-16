**BRAND:** mejba.me
**TITLE:** Claude AI Stack: 5 Tools I Run Daily (Plus PRIME)
**META TITLE:** Claude AI Stack 2026: 5 Tools + PRIME Framework Tested
**SLUG:** claude-ai-five-tools-stack-prime-framework
**PRIMARY KEYWORD:** Claude AI stack
**META DESCRIPTION:** I run the full Claude AI stack daily — Chat, Projects, Cowork, Code, Chrome — plus the PRIME prompting framework. Here's exactly how the five tools fit.
**TAGS:** Claude AI, AI Productivity, Claude Cowork, MCP, AI Workflows

---

# Claude AI Stack: The Five Tools I Run Daily (and the PRIME Framework That Holds Them Together)

The Slack message landed at 7:14 AM on a Tuesday. A founder I've been advising for a year, four-person team, doing fine, finally cracking $40K MRR. The message was three sentences: "I pay for Claude. I open the chat box. I ask it questions. Am I missing something? Everyone keeps saying I'm using it wrong."

He wasn't using it wrong. He was using one tool out of five.

That conversation made me realize how much of the Claude AI stack hides in plain sight from people paying $20 a month for it. They open Claude.ai, type into the message box, and treat it as a chatbot with better grammar. Then they read a thread on X about someone shipping a SaaS in a weekend with Claude Code, or running their finances through Claude Cowork, and they assume those people bought something different. They didn't. The $20 Pro plan unlocks the entire stack. Chat, Projects, Cowork, Code, and Chrome. Five surfaces, one subscription, one login.

I've been running all five against my own work for the last several months — content production, agent development, finance reviews, code reviews, browser automation, the whole pile. This post is the map I wish my friend had been handed eighteen months ago. I'll walk through what each tool actually does (not what the marketing page says), where I use it, where it falls down, and the prompting framework — **PRIME** — that I use to make all five behave like a team instead of five disconnected chatbots.

Fair warning: if you're already deep in Claude Code and shipping production apps with it, the Code section will be familiar. But the way these five tools compose is where most of the leverage hides. Stick around for the PRIME section and the cross-tool workflows at the end.

## Why "Claude" Is Five Things, Not One

Here's the framing that finally stuck for me. Claude isn't a product. It's a stack of tools, each tuned for a different mode of work:

- **Think** — Claude Chat
- **Remember** — Claude Projects
- **Execute** — Claude Cowork
- **Build** — Claude Code
- **Browse** — Claude in Chrome

When people say "Claude is great" or "Claude is overhyped," they're almost always evaluating one of those five surfaces against a problem that belongs in a different one. Pasting a 47-page contract into Claude Chat and being annoyed that you have to keep re-uploading it is like complaining your hammer is bad at cutting wood. Projects exists for exactly that. Asking Chat to "actually go run this analysis on my Stripe data" and being disappointed it can't is misdiagnosing the tool. Cowork is the surface that does work. Chat is the surface that thinks.

Get the mapping right and the same $20 subscription suddenly feels like five subscriptions. Get it wrong and you'll spend a year wondering what everyone else is seeing.

There's a current-month-specific reason this matters more than ever. As of May 2026, Claude Cowork ships with browser automation through the Chrome extension, runs scheduled recurring tasks, and connects to over 10,000 active MCP servers across the ecosystem. The five tools have quietly become an operating layer, not five separate apps. If you're still in the "Claude Chat with extra steps" mental model, you're shipping less than you could be by a factor that's embarrassing to put a number on.

Let me show you what each surface actually does, in the order I use them on a normal Tuesday.

## Tool 1 — Think with Claude Chat

Claude Chat is the surface most people already know. The message box at claude.ai. What most people *don't* know is what Chat became in the last twelve months.

It is no longer a chatbot. It is a research workstation with web search built in, file uploads up to 30 MB per file, image and PDF understanding, artifacts that render code and SVGs and tables in a side panel, and parallel conversations you can fork from any message. On Pro, you're working with Sonnet 4.6 by default and can switch to Opus 4.7 when reasoning depth matters. On Max, you can leave Opus on permanently.

The shift that changed how I use Chat: I stopped treating it as a question-answer machine and started treating it as a thinking partner I run multiple parallel threads with.

Here's what that looks like in practice. When I'm researching a topic for a post — say, the current state of AI agent harness design — I'll open three Chat tabs and start three different angles in parallel. One thread is the optimistic case: "make the strongest argument for X." A second thread is the skeptical case: "now make the strongest argument against X, citing the same sources." A third is the synthesis: "given these two threads, where does the truth probably sit?" That third thread is where I find what I actually believe. It's also where most of my best post angles come from. You won't get to that synthesis by typing one prompt into one thread.

Chat is also where I do all of my first-draft research. I drop a question in, ask Claude to search the web and cite sources, then I follow up with "now what are you uncertain about?" That second prompt — asking the model to flag its own confidence — has saved me from publishing wrong things more times than I can count. Most people never run it.

**Where Chat is the right surface:**

- First-draft thinking and brainstorming
- Research with web search and source citation
- Quick code questions where you don't need a connected codebase
- Reviewing screenshots or PDFs without setting up persistent context
- Anything where the session is one-and-done and you won't need the context tomorrow

**Where Chat falls down:**

- Recurring work where you keep re-uploading the same files (use Projects)
- Multi-step tasks that need to touch your actual file system (use Cowork)
- Anything that should run unattended (use Cowork's scheduled tasks)

The trap I see most people fall into with Chat: pasting the same context block at the top of every new conversation. If you find yourself doing that more than twice, the next conversation belongs in a Project. Which brings me to the second tool, where almost nobody on a free or Pro plan looks.

## Tool 2 — Remember with Claude Projects

Projects is the most underused tool in the entire Claude stack. Free users get five of them. Pro users get unlimited. And almost nobody I talk to is using more than two.

A Project is a Chat session with persistent memory. You attach files (PDFs, docs, codebases up to a certain size), write custom instructions that survive every conversation, and start new chats inside that container whenever you want. Claude reads the attached files and instructions on every message, so your context never resets. It's the difference between hiring a contractor and explaining your business from scratch every Monday morning, versus hiring an assistant who already knows everything.

I run roughly fifteen Projects. Some examples of how they're scoped:

- **Mejba Brand Voice** — my last 40 posts, the editorial style guide I wrote for myself, the list of phrases I never use. Every new post I draft goes through a Chat session inside this Project so the voice stays locked.
- **Active Client A** — proposal, scope of work, last six weeks of Slack threads exported as Markdown, project goals. Any question I have about that client gets asked in this Project, not in fresh chats.
- **Reading Notes 2026** — every PDF and article I've highlighted this year, plus a meta-instruction telling Claude to act as my book club partner.
- **Aria** — the agent definition for my content agent. When I want to evolve the system prompt, I work inside this Project so all the history is in scope.

The leverage isn't the file upload. It's the custom instructions. The instructions field at the top of every Project is where you encode tone, style, format preferences, things to always do, things to never do, and the role you want Claude to play in this Project specifically. Once you write it right, every conversation inside the Project starts at the level where you'd usually arrive twenty minutes in.

A small thing that took me embarrassingly long to learn: you can edit the custom instructions while a conversation is mid-flight. Claude picks up the change on the next message. So if you notice Claude doing something wrong in a Project — say, formatting tables instead of bulleted lists — don't keep correcting it inline. Open the instructions, add "always use bulleted lists, never markdown tables," save, and continue. The correction sticks for every future conversation in that Project, forever.

If you take one thing from this section: open Claude.ai right now and create your first Project. Title it something like "About Me." Upload your resume, your bio, your portfolio, a list of your goals for the year. Write three lines of custom instructions about how you want Claude to address you. From now on, every personal question — career, writing, planning — starts inside that Project. You will feel the difference in a week.

That covers thinking and memory. Where things get genuinely different is when Claude moves out of the chat window and onto your actual machine.

## Tool 3 — Execute with Claude Cowork

Claude Cowork is the desktop app where Claude stops being a chat partner and starts being an operator. You install it on Mac or Windows, grant it access to specific folders, and from that point on it can read your files, write new files, run shell commands inside a sandbox, and execute multi-step workflows that touch the real world.

This is the surface that most differentiates Claude from the chatbot mental model. If you've never used it, the closest analogy is: imagine if your operating system had a competent intern wired into it, and the intern could see your files but only the ones in folders you explicitly authorize.

Cowork is where I do the work that used to take three apps and an hour. Some real examples from the last week:

- Reconciled a Stripe payout against my QuickBooks export by dropping both CSVs in a folder and asking Cowork to flag mismatches. Took 90 seconds. Caught one $312 discrepancy I'd have missed.
- Drafted six pieces of social copy for the same article by pointing Cowork at a folder of past social posts and the new article, asking for variations matched to past performance patterns.
- Cleaned up the metadata on 47 markdown files in a content folder. One prompt, one approval, done.
- Ran a Tuesday morning brief that pulls from my calendar, my last week's writing, and my open tasks, then writes a one-page summary I read with coffee.

The piece nobody emphasizes enough: Cowork connects to the same MCP ecosystem as Claude Code. As of March 2026, [Anthropic reported over 10,000 active public MCP servers](https://www.anthropic.com/news/model-context-protocol) and 97 million monthly SDK downloads across the protocol. What that means in practice is that Cowork can talk to Notion, GitHub, Slack, Google Drive, Figma, Stripe, Salesforce, Linear, Hugging Face, Higgsfield, and roughly any other tool that ships an MCP server — and most major SaaS platforms now do. The marketing creative pipeline I run end-to-end inside Cowork pulls from Notion (the content calendar), generates images via Higgsfield's MCP, and posts back to my CMS, all from a single conversation thread. No glue code. No Zapier. No webhooks.

The friction point nobody warns you about: Cowork's first run feels slower than Chat. Because it's actually doing work — running tools, checking files, asking you to approve actions — a task that would take Chat 8 seconds takes Cowork 45. The trade is that what comes out is a finished artifact, not text you have to copy somewhere. Once you internalize that trade, you stop wanting to use Chat for things that belong in Cowork.

Two security notes, because this matters and most tutorials skip it:

1. **Folder scope is your firewall.** Cowork only sees folders you authorize. Authorize narrowly. Don't give it your entire home directory. Make a `~/cowork-workspace` folder, drop in the project files for what you're working on, point Cowork at that folder, and that's it. If you want a different project, copy those files into the workspace folder, work, and clean up after.
2. **Approve actions in detail the first time.** Cowork asks for confirmation before it runs a shell command or writes to disk. The first ten times, read the request carefully. Don't auto-approve. Build the muscle of seeing what it's about to do before you let it.

If you're new to Cowork and want a deeper walkthrough, I wrote a [full five-phase business operating system on Cowork](https://www.mejba.me/claude-cowork-five-phases-business-os) and a separate piece on [how the Cowork plugin system maps to virtual employees](https://www.mejba.me/claude-cowork-plugins-business). For Sunday-night admin work specifically, the [Claude for Small Business install I tested last week](https://www.mejba.me/claude-for-small-business-installed-tested) is now the fastest way to get to a useful Cowork workflow.

That covers think, remember, and execute. The next tool is where Claude stops behaving like an assistant and starts behaving like a junior engineer who works for you.

## Tool 4 — Build with Claude Code

Claude Code is the terminal-and-desktop surface where Claude becomes a software engineer that reads, writes, and modifies your codebase. It runs in the terminal as a CLI, in the desktop app as a tab inside Cowork, and as a Chrome extension that hooks into your browser DevTools. Same model under the hood, three different ways to call it.

If you write code professionally, this is the surface that has changed the most about how I work. I went from "AI helps me write functions faster" to "I'm directing four parallel agents working different branches of the same repo at the same time." That's not hype. That's the actual workflow I run on most days.

If you *don't* write code professionally, here's the part everyone gets wrong: Claude Code is still useful to you, because the line between "can write code" and "can use Claude Code" moved when Cowork shipped. Claude Code can build you a dashboard, a scraper, a data cleanup script, a one-off automation, a Tailwind landing page, or a Chrome extension from a plain English description, while you watch and approve each step. You don't need to know how to read the code it writes. You need to know what you want and to test whether the output works. That's the entire skill ceiling for non-engineers using Claude Code in 2026.

Some real examples of things I've built in Claude Code in the last month, none of which I would have bothered with two years ago because the activation energy was too high:

- A 200-line Python script that pulls my last 30 days of activity from three different APIs, normalizes the columns, and writes one CSV. Built in 14 minutes. Replaces a thing I used to do by hand on Sunday afternoons.
- A small Next.js dashboard that visualizes the above CSV. Built in 35 minutes. I host it on a $5 Vercel project. I check it once a week.
- A Chrome extension that highlights certain phrases on every web page I visit. Built in 22 minutes. Solves an actual annoyance I'd been complaining about for a year.

The pricing tier that matters: **Claude Pro at $20/month** includes Claude Code in the terminal, on the web, and on the desktop. You get access to Sonnet 4.6 and Opus 4.7 with a token budget that handles a few focused coding sessions per day. If you start running into limits, [Max 5x at $100/month](https://claude.com/pricing) gives you roughly 6.25x more usage per session and is what most working engineers I know are on. The math gets ridiculous fast — an eight-month case study running on Max 20x at $200/month racked up the equivalent of $15,000 in API spend, all under the flat subscription. That's the point where Claude Code stops being an expense and starts being a tax break.

**Where Code is the right surface:**

- Any work inside an existing codebase
- Building new tools from scratch with iterative approval
- Bug hunts where you need the model to read across multiple files
- Refactors that span the codebase
- Anything where the artifact is code you'll commit

**Where Code falls down:**

- One-off questions that don't need codebase context (use Chat)
- Tasks that mostly touch non-code files like CSVs and PDFs (use Cowork)
- Work that needs to run on a schedule unattended (use Cowork's scheduled tasks)

If you're a non-developer and you've never opened Claude Code, the lowest-friction entry point is the desktop tab. Open Cowork, click the Code tab, give it a small project folder, and ask it to build you one thing. A Markdown-to-PDF converter for your blog drafts. A script that renames a folder of photos by date taken. Whatever. The activation energy is lower than installing the CLI, and the experience is identical. From there, the rest of the stack opens up.

That's four tools. Think, remember, execute, build. The last one didn't exist as a real product two years ago, and it's the one that ties the other four to the web.

## Tool 5 — Browse with Claude in Chrome

Claude in Chrome is a browser extension that runs Claude in your sidebar with full visibility into the page you're on, the tabs you have open, and the actions you take. It can summarize pages, fill forms, navigate links, run multi-tab research tasks, and — the feature most people miss — record a workflow you do once and then repeat it for you on a schedule.

[As of mid-2026, Claude in Chrome is in beta for all paid plans](https://claude.com/claude-for-chrome) — Pro, Max, Team, Enterprise. On Pro, you're working with Haiku 4.5 in the extension, which is fast and cheap and totally fine for most browser tasks. On Max and above, you can pick the model, which means you can run Opus inside your browser when reasoning depth matters.

The feature combination I use most: **multi-tab management plus workflow recording.** You drag a set of tabs into a designated Claude tab group, ask Claude a question, and the model sees all those tabs at once. So when I'm comparing three job descriptions, three pricing pages, or three competitor product pages, I don't summarize them one at a time. I drag, group, and ask one question. Claude reads all three and answers across them.

Workflow recording is the one I underestimated until I tried it. The setup: you click record, do the thing you do every week — say, log into a vendor portal, download an invoice, save it to a specific folder, then archive the original email — and Claude watches. When you click stop, the model produces a structured workflow you can replay manually or schedule to run automatically on a cadence. Daily, weekly, monthly. I have a workflow that runs every Monday at 9 AM, pulls my analytics from three different dashboards into a folder, and pings me when it's done. I haven't logged into two of those dashboards manually in months.

**Where Chrome is the right surface:**

- Research across multiple open tabs
- Summarizing or extracting from a page you're already looking at
- Repetitive browser tasks you do weekly
- Form filling, sign-in flows, scheduled scraping where there's no API
- Reading a long article and asking questions while you scroll

**Where Chrome falls down:**

- Heavy reasoning tasks (use Chat or Cowork)
- Anything touching files outside the browser (use Cowork)
- Coding inside a codebase (use Code)

The one tip I'd give every new Chrome user: turn off auto-actions until you trust it. The extension can navigate and click on its own. The first dozen times, watch every action. Build the same muscle you built with Cowork — see what it's about to do, then approve. You'll catch it doing something dumb at least once, and you want to catch it before it submits a form on your behalf.

That's all five surfaces. Now the question that actually matters: how do you talk to them so they produce work you'd be willing to ship?

## The PRIME Framework: How to Prompt the Stack

Tools are inert without the right input. The single biggest reason people get bad output from any AI surface, Claude included, is that they ask vague questions and accept the first answer. The PRIME framework is the structure I use to make sure my prompts have the five pieces every good request needs.

PRIME stands for **P**urpose, **R**esearch, **I**nterview, **M**echanics, **E**xamples. I'll walk through each piece with a concrete example. Imagine I'm asking Claude to help me write a recruiting email to a senior engineer I want to convince to join a project.

### P — Purpose

State the specific goal. Not "help me write an email." That's a category, not a purpose. The purpose is the outcome: "I want this engineer to agree to a 30-minute call next week to discuss joining a paid four-week project, and the email should make her feel like the offer is serious and unusual rather than another cold ping."

The purpose is the thing the prompt is trying to make happen. Write it as one specific sentence with a measurable outcome. Most prompts fail here. They're directional, not specific.

### R — Research

Tell Claude what it should ground its answer in. This is where you ask for citations, real examples, current data, or a web search. For the recruiting email: "Search for the engineer's recent blog posts and reference one specific technical position she's taken publicly. Don't make anything up." The R is your defense against hallucination. Every claim should be tied to a source the model can produce.

For research-heavy work, this is where I usually demand: "Cite every source. If you can't find a source, say so explicitly." Run that phrase in your prompts and the hallucination rate drops noticeably.

### I — Interview

Ask Claude to ask *you* questions before it produces the output. For the recruiting email: "Before you write, ask me five multiple-choice questions about the engineer's likely objections, the offer details I haven't shared yet, and the tone I want. Then write the email."

This is the move most people skip. The model knows what it doesn't know. If you let it ask, it will surface gaps in your input you didn't know existed. The output gets dramatically better because the input gets dramatically better. I covered the deeper version of this in [my piece on AI prompting rules that reduce guessing](https://www.mejba.me/ai-prompting-rules-reduce-guessing).

### M — Mechanics

Specify the format. Length. Tone. Structure. For the email: "Three paragraphs. Maximum 180 words. Conversational, not corporate. End with a single open-ended question, not a 'looking forward to hearing from you' sign-off."

Mechanics is what separates output you'd send from output you'd rewrite. The clearer your specs, the less rework on the back end. If you find yourself rewriting Claude's outputs structurally — moving paragraphs around, changing the headline, adjusting the length — your mechanics layer was too thin.

### E — Examples

Give Claude one or two reference examples. For the email: "Here's a cold email I sent last year that worked: [paste]. The tone of that one. Not the structure — the tone." One concrete example beats five sentences of description, every time.

Examples are also where you can encode the things you can't articulate. If a piece of writing has a "feel" you want to match but you can't quite explain what the feel is, paste it as an example and let Claude reverse-engineer it. Models are very good at pattern-matching style from a small number of samples. Use that.

**Put it all together** and your prompt for a recruiting email looks something like this:

> P: I want this engineer to agree to a 30-minute call next week to discuss joining a paid four-week project. The email needs to feel serious and unusual, not like another cold ping.
> R: She's written three recent blog posts at [link]. Pull one specific technical position she's taken publicly and reference it.
> I: Before you write, ask me five multiple-choice questions about her likely objections, the offer details I haven't shared, and the tone I want.
> M: Three paragraphs. Max 180 words. Conversational. One open-ended question at the end. No "looking forward."
> E: Here's a cold email I sent last year that worked: [paste]. Match the tone, not the structure.

That prompt produces an email I'd send. The five-line version of the same request — "write me a recruiting email to a senior engineer" — produces an email that gets archived without a reply. The difference isn't the model. It's the prompt.

PRIME works in every surface I described in this post. Use it in Chat to think. Use it in Projects with the R, M, and E baked into the custom instructions so you only have to write P and I. Use it in Cowork with extra emphasis on the M (specify file outputs, folders, naming conventions). Use it in Code with the E being a similar function in your codebase. Use it in Chrome with the R being "read the four tabs I've grouped." Same framework, five different applications.

## How the Five Tools Compose: My Actual Tuesday

The thing nobody shows you in the marketing pages is how the five surfaces interlock when you've internalized all of them. Here's a real Tuesday from earlier this month.

**7:45 AM — Chat.** I open Claude Chat on my phone. New conversation. I ask: "Summarize my last seven emails labeled 'urgent' and tell me what I'm forgetting." It pulls them via the Gmail connector, gives me three things, and I see that I owe a client a Loom video by EOD I'd forgotten about.

**8:30 AM — Projects.** I open my "Aria" Project on the laptop. I tell Aria the topic I want to write today. The Project knows my voice, my last 40 posts, and my style. Within 90 seconds I have a research outline I trust.

**9:15 AM — Cowork.** I switch to Cowork. I paste the outline into a workflow that pulls the latest WebSearch results, drops them into a folder, and writes the first draft into a Markdown file in my content folder. While it runs, I make coffee.

**10:00 AM — Code.** Coffee in hand, I open Claude Code in the terminal in a different repo. There's a bug in a small Laravel app I shipped last week. I describe the bug, point Code at the relevant files, and watch it patch a controller. I commit, I push, I move on.

**11:30 AM — Chrome.** I open three competitor pricing pages and group them in a Claude tab group. Single prompt: "Which of these three is closest to my current pricing? Cite specific numbers from each tab." Two minutes later I have what I'd have spent twenty minutes assembling.

**1:00 PM — back to Cowork.** The first draft is done. I open it in Cowork, ask for three specific revisions, and approve each one. The post is ready for the social distribution package by 1:45.

That entire day, I used one tool — Claude — but I touched five different surfaces. Each was the right tool for what I was doing in that moment. None of them were doing each other's jobs. That's the leverage. That's what the founder who Slack'd me at 7:14 AM didn't yet know existed.

## The Honest Part: Where the Stack Still Stumbles

I would be lying if I told you the five tools are all equally polished. Some honest notes:

- **Cowork is slower than you want it to be.** First-run tasks frequently take 2 to 3 minutes when you expect 20 seconds. You learn to live with it because the output is finished work, but the first few sessions feel sluggish compared to Chat.
- **Chrome on Pro is Haiku-only.** Haiku 4.5 is fast and capable, but if you want browser tasks driven by Opus-level reasoning, you need Max or above. For some workflows this matters. For most browser tasks it doesn't, and I'd argue the Haiku-on-Pro decision is actually right — it keeps the extension snappy.
- **The five-tool framing is still in transition.** Anthropic itself hasn't fully unified the marketing, and you'll find some confusing edges. Cowork plugins, skills, MCP servers, connectors — the vocabulary overlaps and the docs sometimes contradict each other. You'll figure it out. It takes a week.
- **Projects has a file size cap that can bite you.** Very large PDFs (50 MB+) and large codebases hit limits that you wouldn't expect at the $20/month tier. For most users this never comes up. For a few use cases it's a wall.
- **The stack is moving faster than any one person can fully keep up with.** Three new connectors shipped while I was writing this post. By the time you read it, there are probably two more. The trade is real but small — the framework holds even when individual features change.

None of those things stop the stack from being a defensible $20/month purchase. They're notes you'd hear from someone who actually uses it, not someone who watched a demo and tweeted about it.

## What to Do This Week

Three concrete moves, in order, that will get you 80% of the way to running the full Claude AI stack:

1. **Create your first Project today.** Title it "About Me." Upload your bio, your goals for the year, and a list of things you want help with. Write three lines of custom instructions about how Claude should address you. Use it for every personal-context question for one week. Notice the difference.
2. **Install Cowork this weekend.** Mac or Windows, free with your Pro plan. Pick one Sunday-night admin task you hate. Reconciling Stripe payouts, drafting your weekly email, cleaning up a folder. Run it inside Cowork. Time it. Compare to last Sunday.
3. **Pick one PRIME element to add to your default prompt.** If you only do one, do **I — Interview.** Add "before you answer, ask me five multiple-choice questions to refine your output" to your next ten prompts. Watch how much better the answers get.

Three moves. One week. After that, the rest of the stack — Code, Chrome, the heavier workflows — opens up naturally because you've internalized the model: think, remember, execute, build, browse. Five surfaces. One subscription. One login. One framework.

The founder who Slack'd me at 7:14 AM? He's three weeks into the stack now. He texted me last Friday: "I can't believe I was paying $20 a month to use 8% of this." That's the part nobody tells you about Claude — the price tag is the same whether you use one tool or five. The choice is yours.

## Frequently Asked Questions

### What are the five Claude AI tools?
The five tools are Claude Chat (think), Claude Projects (remember), Claude Cowork (execute), Claude Code (build), and Claude in Chrome (browse). All five are included in the Pro plan at $20/month, with usage limits and model access varying by tier.

### Do I need separate subscriptions for Claude Code and Claude Cowork?
No. The Pro plan at $20/month includes Claude Code, Cowork, Chat, Projects, and Chrome in beta. Max ($100 or $200/month) raises usage limits and model access. Team and Enterprise add seat management. There are no per-tool subscriptions.

### What is the PRIME framework for Claude AI?
PRIME stands for Purpose, Research, Interview, Mechanics, Examples. It's a five-part prompt structure that forces you to specify the goal, ground the answer in sources, have Claude ask clarifying questions, set output format, and provide reference examples. See the full breakdown above.

### Is Claude in Chrome safe to use?
Claude in Chrome is in beta on paid plans and requires explicit permission for any action it takes. Turn off auto-actions until you trust it, watch the first dozen tasks closely, and never grant it access to financial or healthcare portals without supervision. Safety is a function of how you scope its permissions.

### What is Model Context Protocol (MCP) and why does it matter?
MCP is the open protocol Anthropic launched in late 2024 that lets AI models connect to external tools, data sources, and services through a standard interface. As of March 2026, there are 10,000+ active public MCP servers across the ecosystem, which is what lets Claude Cowork and Code connect to Notion, GitHub, Slack, Stripe, and almost any major SaaS without custom integration code.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
