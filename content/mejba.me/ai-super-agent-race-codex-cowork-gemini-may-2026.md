**BRAND:** mejba.me
**TITLE:** AI Super Agent Race: Codex vs Cowork vs Gemini, May 2026
**META TITLE:** AI Super Agent Race May 2026: Codex, Cowork, Gemini Tested
**SLUG:** ai-super-agent-race-codex-cowork-gemini-may-2026
**PRIMARY KEYWORD:** AI super agent race May 2026
**META DESCRIPTION:** I tested Codex, Claude Cowork and Gemini side by side. Plus NotebookLM auto-labels, Nano Banana vs GPT Image 2, GPT-5.5 prompts, and Spotify's AI badge.
**TAGS:** AI Weekly, OpenAI Codex, Claude Cowork, Google Gemini, NotebookLM

---

# The AI Super Agent Race in May 2026: I Tested Codex, Cowork, and Gemini Side by Side

The first thing I did Monday morning was open three windows.

Codex on the left. Claude Cowork in the middle. Gemini on the right. Same task in all three: take this messy folder of 47 unread emails, pull out the four that need a reply this week, draft replies, and produce a one-page status summary I could paste into Notion before standup. No prompt engineering tricks. No "you are an expert assistant." Just the kind of half-formed instruction I would actually type at 8:14 AM with cold coffee.

By 8:39, only one of them had finished cleanly.

That is the version of the AI super agent race May 2026 looks like from inside my workflow. Not a benchmark sheet. Not a demo. Three real tools on three real screens trying to handle the kind of compound knowledge work that used to mean an hour of context-switching. And they are no longer chasing the same goal. Codex wants to live in a single chat that does everything. Claude Cowork wants to be a separate dedicated surface. Gemini wants to be the file factory bolted onto Google Drive.

Three different bets. Three different futures for what an "AI agent" even means. I spent the week running each of them through real work — not prompts I designed to make them look good — and the gap between marketing and reality showed up fast.

This is what I found, what changed across the rest of the AI stack at the same time (NotebookLM auto-labels, the Nano Banana vs GPT Image 2 fight, the GPT-5.5 prompting reset, and Spotify's AI music badge), and the one mental model I use to decide which tool gets which job. If you have been trying to figure out which super agent to actually commit to, this is the post I wish someone had handed me on Monday.

If you want the wider context for how I sort these weekly launches, my [last AI weekly roundup covering Jupiter, Flash, and Codex Pets](https://www.mejba.me/ai-weekly-claude-jupiter-gemini-flash-codex-pets) sets the frame.

## Why This Week Matters More Than Most

Most weeks in AI feel busy. This one feels architectural.

OpenAI, Anthropic, and Google all shipped updates that share one quiet theme: the AI assistant is no longer a chat box. It is a working surface — something that runs in the background, holds state across days, creates files, and triages your inbox while you are doing something else. The chat is just the front door.

That shift is not new. But this week is the first time I have looked at all three flagship products and felt that they are no longer optional toys for early adopters. Cowork [went generally available on macOS and Windows on April 9, 2026](https://claude.com/product/cowork) with enterprise analytics, OpenTelemetry, and role-based access controls. Gemini [shipped direct file creation inside chat on April 29, 2026](https://blog.google/innovation-and-ai/products/gemini-app/generate-files-in-gemini/), saving Docs and Sheets straight to Drive. Codex pushed the persisted `/goal` task system that lets a single chat track work across sessions — alongside the much-memed Pets feature.

Three companies. Three weeks. Three big swings at the same prize: become the surface where knowledge work happens.

Here is the part most coverage missed. They are not converging on the same shape. They are deliberately diverging — and that means for the first time, choosing one is going to feel less like picking a tool and more like picking a workflow.

That is the real story. Let me show you what each one does well, where it falls apart, and how I am splitting my week between them now.

## Codex: One Chat, Everything In It

The bet OpenAI is making with the [May 2026 Codex update](https://developers.openai.com/codex/changelog) is the simplest of the three to summarize. You should not have to leave the chat.

If Cowork's pitch is "knowledge work deserves its own dedicated tab," Codex's pitch is the opposite. One adaptive chat interface. Code in it. Plan in it. Triage email in it. Build files in it. The chat reshapes around what you are doing. Tool permissions, environments, working directories — all available through quick controls right inside the conversation. Alt-comma drops reasoning. Alt-period raises it. The model upgrade resets reasoning to the new default instead of carrying stale settings forward. Tiny fixes, but they tell you what OpenAI thinks the future looks like: a single window that gets denser, not a ribbon of tabs.

The headline feature for me was the persisted `/goal` workflow. I had been waiting for this one for months.

Here is how it actually works. You give Codex a long-running goal — "triage my inbox every weekday morning, draft replies for the four most urgent threads, summarize the rest." That goal persists. You can pause it, resume it, clear it. The app-server APIs let it survive across sessions. Each morning when I open Codex, the goal is still there, still tracking, still working through the queue. It is not a one-shot prompt. It is a persistent intent that the agent keeps coming back to.

This is the thing knowledge work actually needs. Most "AI agent" demos are single-task. Real work is a stack of partial goals you carry across days. A goal system is the missing primitive.

Then there are the Pets.

I almost did not bother installing them. The first time someone showed me a Codex Pet bouncing in the corner of their screen — a small pixel creature shadowing the agent's status — I rolled my eyes. Then I tried it for a day. Then a week. I have not turned it off since.

The reason is dumb and obvious in hindsight. The hidden cost of running a long-horizon background agent is not compute. It is your attention. You start a Codex job, switch to a Slack thread, fall down a research rabbit hole, then either obsessively refresh the tab or miss the moment the agent finishes. The pet collapses that loop into peripheral vision. The little creature is idle, you are calm. The creature scurries, work is happening. The creature lies down, the agent is blocked on a permission prompt and needs you. You glance, you know, you move on. Type `/pet` to summon or hide. It works.

I called mine Mango. I am not proud of how attached I am to Mango.

But here is where Codex falls short, and it falls short in exactly the place its single-chat philosophy is supposed to make easy. **It does not directly create files**. You can ask Codex to draft a PowerPoint presentation and it will produce something — the structure, the bullet points, even Mermaid diagrams. But it does not hand you a `.pptx` you can drop into a meeting. You leave the chat to assemble the artifact. For a "single chat does everything" pitch, that is a real gap.

The other gap is mobile. Codex's mobile experience is improving but is still a long way behind Gemini's. If you do half your message-triage on a phone between meetings, that matters.

What Codex is great at: persistent, long-running agentic work where the output is *action* (PRs, refactors, scheduled follow-ups, triage). What it is not great at yet: producing a deliverable file that someone else needs to open in Word or Excel.

## Claude Cowork: A Dedicated Tab for Knowledge Work

Anthropic's bet is the opposite. Knowledge work is different enough from chat and from coding that it deserves its own dedicated surface.

Cowork is that surface. It runs on your desktop, connects to your local files, integrates with external services through connectors, and uses plugins to extend what the agent can do. You open the Claude Desktop app, switch to the Cowork tab, click Customize in the left sidebar, and your plugins, skills, and connectors all live in one place. It is the cleanest separation of concerns of the three: Chat for conversation, Code for engineering work, Cowork for everything between.

The connector system is the part most coverage gets wrong. Cowork shows two tabs in the connector panel — Web connectors (browser-based APIs) and Desktop extensions (run locally). The interesting part is filesystem integration. Pull data from an external service and save it locally. Or use local files as input for an external action. That bridge — between cloud data and your machine — is the thing Codex's single-chat model cannot easily replicate, because it is fundamentally cloud-first.

The Microsoft 365 connector is the killer use case in my week. As of February 2026, Cowork can read Outlook emails, access SharePoint documents, and work with OneDrive files — and it handles cross-app workflows in a single task. I gave it a real one: "analyze the Q1 sales numbers in this Excel file from OneDrive, then build a board-ready PowerPoint summarizing the findings, and save both the source file and the deck back to SharePoint." That is the kind of task that used to mean opening four apps and copying state between them. Cowork did it in a single uninterrupted run.

That is the moment I stopped treating Cowork as a curiosity. It does compound work. Multi-step, multi-app, multi-format. The agent does not break the chain to ask you to copy something from one window to another.

The plugin ecosystem is the other thing worth watching. The [open source knowledge-work-plugins repo](https://github.com/anthropics/knowledge-work-plugins) on Anthropic's GitHub gives you a sense of where this is heading — first-party plugins for the kinds of tasks knowledge workers actually do, plus a path for enterprises to build their own. The February 2026 announcement of private marketplaces and department-specific AI agents made it clear Anthropic is targeting the org chart, not just the individual user.

Where Cowork falls short: the separation that makes it feel clean also creates friction. You have to know in advance what kind of work you are about to do. Switching from Cowork to Chat to Code has a cost — visual, mental, contextual. Codex's single-chat model just feels lighter on small tasks. For a quick "summarize this article" or "draft a tweet," opening Cowork is overkill. You feel it.

The other thing to flag: Cowork is desktop-first by design. There is no mobile parity. If you live on your phone, Cowork is not a serious daily driver yet.

For deeper context on how Cowork fits into a real automation stack, my walkthrough on the [Cowork workflow automation patterns I am using](https://www.mejba.me/claude-co-work-workflow-automation) covers the connector setup in detail.

What Cowork is great at: compound knowledge tasks that span multiple apps and formats — read an email, pull data from a doc, build a deck, save the result. What it is not great at: lightweight, fast-feedback tasks where opening a separate tab feels like overhead.

## Gemini: The File Factory Bolted Onto Drive

Google is making the third bet, and it is the one most people are underestimating.

Gemini is not trying to be the smartest agent. It is trying to be the easiest. The April 29, 2026 [direct file generation rollout](https://9to5google.com/2026/04/29/gemini-app-generate-files/) is the clearest expression of that philosophy. You type "make me a project status report in Google Docs, with a table of milestones in the first section and a quick summary in the second." Gemini generates the file. It saves it to Drive. You get a download link inside the chat or you open it in Docs directly. No copy-paste step. No format conversion. The supported formats now cover Docs, Sheets, Slides, PDF, DOCX, XLSX, CSV, LaTeX, TXT, RTF, and Markdown.

There is one sharp limitation worth flagging up front. **There is no direct PowerPoint export yet**. You can generate Slides and download them as a `.pptx`, but the direct path Codex and Cowork users keep asking for is still missing. If your shop runs on Microsoft, that is a workflow tax.

What Gemini does win on, hands-down, is mobile. The continuous dictation feature is the thing that surprised me most this week. I tested it on a long voice memo — two minutes of stream-of-consciousness about a client project, the kind of thing where I would normally pause and lose my train of thought. Gemini's mobile dictation handled the whole thing without prematurely cutting me off. That is a small detail that translates into a big behavioral shift. I stopped opening Notes to dictate first and then pasting into the AI. The friction is gone.

For the audience Gemini is targeting — non-technical knowledge workers who already live inside Google Docs and Sheets — this is the right shape. They do not want to learn a new tool. They want the tool they already use to suddenly produce files when they ask. That is what Gemini delivers.

Where Gemini falls short: agency. There is no persistent goal system equivalent to Codex's `/goal`. There is no plugin ecosystem equivalent to Cowork's connectors (yet). It is excellent at one-shot file creation, weaker at multi-step compound tasks. If you give Gemini "triage my inbox every morning," it does not have a real mechanism to keep doing that across sessions.

The "Help me create" tool inside Docs that landed in March 2026 hints at where Google is heading — pulling context from Drive, Gmail, and Chat to draft a starting document. But that is composition, not agency. The gap between Gemini and Cowork on multi-app workflows is the biggest of the three comparisons.

What Gemini is great at: fast, accessible file creation for users who live in Workspace already. What it is not great at: persistent agentic workflows or anything that needs long-running goal state.

## The Side-by-Side I Actually Run Now

Three tools. Three philosophies. Here is the comparison table I use when deciding what gets which job.

| Capability | Codex | Claude Cowork | Gemini |
|---|---|---|---|
| Interface | Single adaptive chat | Dedicated Cowork tab | Chat with file actions |
| Primary strength | Persistent agency | Compound multi-app work | Fast file creation |
| Task tracking | Built-in `/goal` system | Via connectors and plugins | None native |
| Plugins / connectors | Yes (Skills, automations) | Yes (Web + Desktop) | Limited |
| Direct file creation | No native | No native (uses connectors) | Yes (Docs, Sheets, PDF, DOCX, XLSX) |
| PowerPoint output | Indirect | Yes (via M365 connector) | No (via Slides export only) |
| Mobile experience | Improving | Desktop-first | Full support with dictation |
| Persistent state | Strong | Strong via Cowork tab | Weak |
| Best for | Long-running agent tasks | Multi-app compound workflows | Fast Workspace-native creation |

The honest answer to "which one should I use" is "more than one of them." Here is the rule I land on after a week of real testing.

Use **Codex** when the work is long-running, agentic, and produces actions or code as the deliverable. Triage. Refactors. PRs. Anything where the output is something the agent *did* rather than a file the agent *made*.

Use **Cowork** when the work spans multiple apps and formats and the deliverable is a real artifact that has to flow between cloud and disk. Excel-to-PowerPoint workflows. Email-to-doc-to-deck pipelines. Anything where the connector graph is the value.

Use **Gemini** when the work is "I need a file, and I need it now, and it should land in my Drive." Status reports. Quick spreadsheets. PDFs. The minute you find yourself opening a blank Doc to start typing, ask Gemini first.

The question is not which tool wins the AI super agent race in May 2026. The question is which combination wins your week.

## NotebookLM Quietly Solved Source Chaos

While the three super agents fought for the headline, NotebookLM shipped the update I think will matter most six months from now.

Auto-labels.

If you have ever used NotebookLM with more than ten sources in a notebook, you know the problem. The sidebar becomes a wall. PDFs, web pages, transcripts, documents — all stacked in upload order, none of them sorted, all of them looking the same. You scroll. You search. You curse. Then you open a different notebook to start fresh.

The April 2026 update changes that. [Once a notebook crosses five sources](https://pasqualepillitteri.it/en/news/1391/notebooklm-april-2026-update-auto-label-flashcards), Gemini analyzes each new source — title, content, document type — and proposes a category. The category becomes a visual tag in the sidebar. Sources with similar tags group together. If a source spans multiple topics, it gets multiple labels. If you disagree with the category, you rename it, reassign it, or stamp it with an emoji prefix to spot the group at a glance.

What I love about this update is how restrained it is. It does not try to do too much. It just admits the obvious: at scale, manual organization breaks. The fix is a model that proposes, you approve.

I tested it on a research notebook with 38 sources spanning three loosely related projects. NotebookLM labeled them into eight categories within a minute of upload. I overrode three. I added emojis to two. The notebook went from "I cannot find anything" to "this is actually usable" in one session.

For students, researchers, and anyone running a long-lived knowledge base inside NotebookLM, this is the difference between using the tool and abandoning it. If you want a deeper walk-through of the broader update set, my [recent NotebookLM major update breakdown](https://www.mejba.me/google-notebooklm-major-update) covers the surrounding feature shifts.

The other thing this update tells me, between the lines: Gemini-as-classifier is being productized. The same model layer that auto-labels NotebookLM sources is the one Google will eventually push into Drive. The day my Drive folders start auto-categorizing themselves is coming. I am ready for it.

## Image Generation: Pick Your Job, Then Pick Your Tool

The other fight worth noting this week is the one between Nano Banana 2 and GPT Image 2. And the honest answer here is the same as the super agent race: there is no overall winner. There are two different jobs.

I tested both this week with the same brief. Generate a hero illustration for a fintech landing page. Then take that illustration and edit it — change the lighting, swap the foreground character, alter the expression on a face.

[GPT Image 2 won the generation half](https://chatgptimages.co/gpt-image-2-vs-nano-banana-2). The text rendering on the headline copy was clean on the first try. The composition felt deliberate, not random. The output landed in about 3 seconds. For anything where a prompt has to be respected with precision — typography, layout, structured product shots — GPT Image 2 is the right call.

Nano Banana won the editing half. When I asked for "the same illustration, but the character in the foreground is now smiling, and the lighting is golden hour instead of overcast," Nano Banana preserved the rest of the frame almost perfectly. Background untouched. Composition untouched. Only the asked-for changes happened. GPT Image 2's edit drifted further from the original than it should have. Nano Banana stayed locked in.

The translation for your workflow: if you are building from scratch, GPT Image 2. If you are iterating on an existing image, Nano Banana. The era of "use one image model for everything" is over. You pick a model the way you pick a brush.

For the deeper hands-on of GPT Image 2 specifically, my [GPT Image 2 review from late April](https://www.mejba.me/gpt-image-2-review-openai-april-2026) covers the typography and prompt-adherence wins in detail.

## GPT-5.5 Wants Shorter Prompts. I Was Wrong About This.

I have to admit something. I was the wrong kind of confident about prompt engineering for years.

I built libraries of multi-thousand-word system prompts. Step-by-step instructions. Explicit constraints. "Always do this. Never do that. Must follow this format. Only respond if X." Long preambles. Persona definitions. Chains of reasoning steps. The works.

The [GPT-5.5 prompting guide that landed in late April](https://simonwillison.net/2026/apr/25/gpt-5-5-prompting-guide/) is OpenAI's quiet way of telling me — and a lot of other people — that we have been doing it wrong for the current generation of models.

The headline rule: shorter, outcome-first prompts beat process-heavy prompt stacks. Describe the destination, not the route. Define what good looks like, what the constraints are, what evidence is available, what the final answer should contain. Then let the model find the path.

The guide explicitly calls out the words to remove. "Always." "Never." "Must." "Only." Not because absolutes are wrong as a concept, but because they narrow the model's search space and lead to mechanical, low-imagination outputs. The model is smart enough now to handle nuance. Heavy-handed instructions actively hurt.

I tested this on three of my workhorse prompts. The first was a research-summary prompt I had refined over six months — about 1,800 words of instruction. I rewrote it as a 140-word outcome-first version: "Produce a research summary that does X, Y, Z. Output format is markdown. Cite sources inline." I ran them head-to-head on the same five articles.

The shorter prompt won four out of five. Not by a small margin. By a lot. The summaries were more focused, less robotic, and surfaced insights the long prompt's rigid structure had been blocking.

This is hard to swallow if you have built a personal library of long prompts. But the lesson is simple. **Models change. Prompts should change with them.** Migration is not optional. If you are running 2024-era prompts on 2026-era models, you are leaving the model's actual capability on the floor.

My new rule of thumb: start every new GPT-5.5 prompt at the smallest version that gets the job done. Add only what failure forces you to add. Treat instructions like dependencies — every line is a cost, and you justify it.

For practitioners still building agent stacks, my deeper post on the [prompting rules I now use to reduce model guessing](https://www.mejba.me/ai-prompting-rules-reduce-guessing) covers how this connects to multi-agent systems specifically.

## Spotify's AI Music Badge: The Trust Layer Goes Live

The last story this week is not a model or a tool. It is a label.

Spotify [announced "Verified by Spotify"](https://newsroom.spotify.com/2026-04-30/verified-by-spotify-badge-artist-details/) on April 30, 2026. A green checkmark on artist profiles that signals the artist has been reviewed and meets Spotify's criteria for authenticity. At launch, more than 99% of the artists Spotify users actively search for will be verified. Hundreds of thousands of artists, mostly independent, across genres and career stages.

The thing the headlines kept missing: this badge is not for AI artists. At launch, profiles that primarily represent AI-generated or AI-persona artists are not eligible. Spotify said "at launch" — leaving the door open — but the signal is intentional. Verification, right now, means a human is behind the music.

I want to sit with that for a second.

This is the first major consumer platform to draw a hard line on AI provenance using a positive signal. Not "this content is AI-generated" warning labels. The opposite. **A trust badge that means a human did this.** That is a different framing, and the difference matters. It says the burden of trust is no longer on the audience to detect AI. The platform is doing the verification work and giving you a visible signal you can rely on.

If you are wondering why this matters beyond music, here is the prediction. Inside twelve months, you will see equivalents on YouTube, Substack, and major news platforms. Possibly LinkedIn for professional content. The AI flood is forcing every platform to answer the same question: how do you know what came from a person? Spotify's answer is the cleanest one I have seen. A green checkmark. A short list of criteria. A clear public claim.

The other thing worth flagging — this is part of a series. [Artist Profile Protection](https://newsroom.spotify.com/2026-04-30/verified-by-spotify-badge-artist-details/) shipped in March. AI Credits in Song Credits earlier this month. The badge is the third move. Spotify is not making one announcement. They are building a layer.

Watch this space. The AI provenance question is about to become a UI question across every platform you use, and Spotify just shipped the reference design.

## What I Am Doing With All Of This By Monday

I told you at the top this was a week that felt architectural. Here is the architecture I am committing to for the next thirty days.

**For long-running agentic work** — code, refactors, scheduled triage, anything where the agent has to keep state across sessions — I am running Codex with persistent `/goal` workflows and Pets enabled. Mango stays on the desktop.

**For multi-app compound workflows** — Excel-to-PowerPoint, email-to-deck pipelines, anything that requires the bridge between cloud apps and local files — I am running Claude Cowork with Microsoft 365 connectors active. Cowork tab open in the morning. Closed by lunch if the day's work does not need it.

**For fast file creation** — quick reports, status docs, spreadsheets I need to share inside an hour — I am opening Gemini first. Especially on mobile. The dictation alone is worth it.

**For research organization** — every notebook I build going forward gets dropped into NotebookLM with auto-labels turned on. I stopped manually tagging six days ago and have not looked back.

**For image work** — GPT Image 2 for generation, Nano Banana for editing. I do not pretend either one wins overall anymore.

**For prompts** — I am rewriting my five most-used system prompts this weekend in the GPT-5.5 outcome-first style. Smallest version. Outcome stated. Trust the model. The 1,800-word monsters in my prompt library are getting retired.

**For trust signals** — when I write about an artist, a creator, or a platform from now on, I am checking provenance. Verified or not. AI or not. The badge is a useful primitive. I am going to use it.

That is the week. Eight stories. Three super agents tested side by side. One contrarian admission about prompt length. One trust-layer prediction I will revisit in October.

The AI super agent race in May 2026 is not going to crown a single winner. It is going to fragment into specialties — agency, compound work, file creation — and the people who thrive will be the ones who pick the right tool for the right job instead of forcing one tool to do everything.

If you take one thing from this post, take this: **stop trying to find the one super agent.** Build a stack. Pick deliberately. Re-evaluate weekly. The companies are diverging on purpose — your workflow should match the divergence, not fight it.

I will see you next week with the next round.

## Frequently Asked Questions

### What is the difference between Codex and Claude Cowork in 2026?
Codex uses a single adaptive chat for coding, knowledge work, and task tracking, while Claude Cowork uses a dedicated tab focused on multi-app knowledge workflows with connectors and plugins. Codex wins on persistent agency through `/goal` workflows. Cowork wins on compound work that bridges cloud apps and local files, especially through the Microsoft 365 connector. See "The Side-by-Side I Actually Run Now" above for the full breakdown.

### Can Gemini directly create PowerPoint files in 2026?
Not directly. Gemini can generate Google Slides as of April 29, 2026, and you can export those Slides as a `.pptx` file, but there is no native PowerPoint export inside the chat. For native `.pptx` output without an export step, Claude Cowork's Microsoft 365 connector is currently the cleaner path.

### Should I use Nano Banana 2 or GPT Image 2?
Use GPT Image 2 for generation from scratch — it has the cleanest text rendering, strongest prompt adherence, and faster output (around 3 seconds). Use Nano Banana 2 for editing existing images, character consistency across a series, and localized inpainting. There is no overall winner; the tools serve different jobs.

### How should I prompt GPT-5.5 differently from older models?
Use shorter, outcome-first prompts. Describe what good looks like, what constraints matter, and what the final answer should contain. Avoid absolutes like "always," "never," "must," and "only" unless they are load-bearing. Older multi-thousand-word system prompts often hurt GPT-5.5 by narrowing its search space. Start with the smallest prompt that works and add only what failure forces.

### What does the "Verified by Spotify" badge mean for AI music?
The badge, launched April 30, 2026, signals that an artist profile has been reviewed and represents a human artist with consistent listener engagement. AI-generated or AI-persona artist profiles are not eligible at launch. It is a positive trust signal — a green checkmark that means a human is behind the music — rather than an AI warning label.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
