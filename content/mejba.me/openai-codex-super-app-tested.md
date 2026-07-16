**BRAND:** mejba.me
**TITLE:** OpenAI Codex Super App: I Tested Every Feature
**META TITLE:** OpenAI Codex Super App Tested: Honest 2026 Review
**SLUG:** openai-codex-super-app-tested
**PRIMARY KEYWORD:** OpenAI Codex super app
**META DESCRIPTION:** I tested OpenAI's Codex super app on real workflows: file access, Chronicle, plugins, computer use, automations. Here's what worked and what stumbled.
**TAGS:** OpenAI Codex, AI Agents, Computer Use, Chronicle, AI Automations

---

The first thing I asked the OpenAI Codex super app to do was OCR a stack of 53 receipts I had been avoiding for two months. I dragged the folder into the app, typed one sentence about wanting them in an Excel dashboard, and walked to the kitchen to make coffee. By the time I came back, Codex had read every receipt, extracted vendor, date, amount, tax, and category, dropped them into a sheet, added a pivot table by category, and was asking me whether I wanted a chart. Coffee wasn't even ready yet.

That's when I stopped treating the April 2026 Codex update as another OpenAI press release and started treating it as the agent platform I had been waiting for since GPT-4 first got tools. The OpenAI Codex super app — yes, it's actually called Codex, not "Codeex" the way YouTube tutorials keep pronouncing it — is no longer just a coding assistant. It's a desktop agent with full file access, persistent memory, ninety-plus plugins, computer control, scheduled automations, built-in image generation through GPT-image-1.5, and a screen-watching memory system called Chronicle that's either the future of context or a privacy disaster, depending on which review you read.

I've been running it on my actual workflows for two weeks: receipt processing, brand-deal email triage, Canva presentation builds, web app testing, scheduled Friday reports, and one experiment where I let Chronicle watch me edit a slide deck and asked it to suggest improvements based on what it had seen. Some of it was genuinely impressive. Some of it stumbled in exactly the places you'd expect a v1 super app to stumble. And one piece of it made me turn the whole thing off for forty-eight hours while I thought about whether I wanted that much surveillance on my own machine.

This is the honest verdict. Stay through the Chronicle section — that's where the privacy-versus-autonomy trade-off gets real.

## What OpenAI Actually Shipped In April 2026

Let's get the naming and the timeline straight before anything else, because there's enough confusion around this release to fill a separate post.

The product is called **Codex**. It runs as a desktop app on macOS (Windows is in preview), and it's bundled with paid ChatGPT plans — Plus at twenty dollars a month, Pro at one hundred or two hundred a month, plus Business, Edu, and Enterprise tiers. There's no separate Codex subscription. Your ChatGPT plan defines how much Codex usage you get before you hit credit limits, and the new hundred-dollar Pro tier explicitly markets "5x more Codex usage than Plus" with a celebratory 2x bonus running through May 31, 2026.

The April 2026 update is the one that turned Codex from "coding agent" into "super app." OpenAI shipped it in two waves. The first wave landed on April 16 with the desktop overhaul: full file access, in-app browser, computer use, GPT-image-1.5 generation, and the new plugin system. The second wave landed a few days later with Chronicle in opt-in research preview, ChatGPT Pro only, macOS only, not yet available in the EU, UK, or Switzerland.

The shipping headline numbers from OpenAI's own announcement: ninety-plus plugins at launch (Slack, Gmail, Notion, Google Drive, Microsoft Suite, Atlassian Rovo, GitLab, CircleCI, Figma, Render, Neon, Remotion, and a long tail of others), full memory and personalization rolling out to ChatGPT-signed-in desktop users, and one demo where Codex built a complete racing game from a single prompt using more than 7 million tokens across image generation and web game development skills.

That's the surface. The interesting part is what happens when you try to put real work through it.

## Full File Access: The Receipt Test

The single biggest mental shift compared to ChatGPT Agent or Claude Code is that Codex actually lives on your filesystem now. Not as a sandboxed cloud worker reaching into a synced folder. Not as a CLI tool that operates on whatever directory you cd into. As a desktop application with native file system access that can read, write, and modify whatever you point it at — with the caveat that you grant access folder by folder, not blanket.

I tested this with the receipt workflow because it's the kind of task I had given up on. Fifty-three crumpled-corner receipts, scanned into JPGs by my phone, sitting in a folder called `2026-q1-expenses-final-FINAL`. I pointed Codex at the folder and asked for an Excel dashboard with category totals, monthly breakdowns, and a flag for any receipt where the OCR confidence was low.

What happened next was the moment that sold me on the local-files-first design. Codex opened each image, ran vision analysis, extracted the structured fields, wrote them to a CSV, then opened the CSV with a Python skill, generated the Excel file with formatting and a pivot table, and saved it next to the originals. No upload. No "please paste your receipts into the chat." The data never left the folder it started in until I told it to.

The catch: Codex's OCR confidence flag worked on 47 of the 53 receipts cleanly. Six receipts — all from the same gas station that prints on thermal paper that fades within a week — came back with low-confidence dollar amounts. Codex flagged them, didn't guess, and put them in a separate "manual review" tab in the spreadsheet. That's the behavior I want from an agent. The failure mode I was afraid of — confidently inventing dollar amounts to fill the cells — didn't happen.

Compare this to Claude Code, which I cover in detail in [my Claude Code 3.2 power-user hacks post](https://www.mejba.me/claude-code-32-power-user-hacks). Claude Code can do the same workflow if you wire it up — it's a CLI agent with file access, after all. But Codex's desktop UI lowers the barrier from "I need to write a CLAUDE.md and a Python skill and figure out the right MCP server" to "I drag the folder onto the app and type a sentence." For the kind of person who hires me to automate this stuff, that lower barrier is the difference between adopting it and not.

The privacy framing matters too. Local files mean the receipts, the bank statements, the contracts — none of it gets uploaded to OpenAI's servers wholesale. The model still talks to OpenAI's API to do the actual reasoning, but the file payload stays local. That's a different threat model than ChatGPT Agent's cloud-sandbox approach, and for a lot of my client work — agencies handling client data, freelancers with NDAs — it's the only acceptable model.

That privacy framing is going to come back when we get to Chronicle. Hold onto it.

## Persistent Memory: agents.md Plus The Auto Layer

Memory in Codex comes in two layers, and the distinction matters more than OpenAI's own docs make clear.

The first layer is `agents.md`. This is the manual memory file — a markdown document you write and edit yourself, where you tell Codex who you are, what your projects are, what your preferences are, and what shortcuts you want it to take. It's the same `agents.md` convention that Codex CLI uses, now surfaced inside the desktop app. I keep one at the root of every project folder and one global one in my home directory.

My global `agents.md` looks roughly like this:

```markdown
# About me
- Engr Mejba Ahmed, software engineer running multi-brand content
- Brands: mejba.me, ramlit.com, colorpark.io, xcybersecurity.io
- Default editor is VS Code, default shell is zsh

# Coding preferences
- TypeScript over JavaScript when given a choice
- Tailwind for styling, never inline styles
- Prefer functional React components, no class components

# Writing preferences
- First-person voice on mejba.me posts
- No "in conclusion", no "furthermore", no "game-changing"
- 3000+ words minimum on long-form posts
```

That file gets read every time Codex starts a session in the directory tree it lives in. It's deterministic, it's transparent, and I can version-control it.

The second layer is the new automatic memory, stored in a separate file Codex manages itself. This is where Codex writes things it learns over time — that I've been complaining about a specific Laravel package, that my last three projects all used Drizzle instead of Prisma, that I prefer code reviews framed adversarially. It's a different file from `agents.md` precisely because OpenAI doesn't want their auto-memory rewriting the file you control.

The behavior I noticed after a week: the auto memory is good at preferences and bad at facts. It correctly learned that I want PR descriptions to lead with the user-facing change, not the code change. It also "remembered" that I prefer pnpm over npm, which I don't — I had once mentioned avoiding pnpm on a specific Tauri project because of a known bug, and the memory generalized that to a global preference. That's the kind of overgeneralization you'd expect from a v1 memory system, and the fix was straightforward: I edited the auto-memory file directly and deleted the wrong claim.

The fact that the auto-memory is a plain markdown file you can edit is, by itself, the design decision that makes me trust this feature. Compare that to Claude's memory feature in the consumer app, which is opaque and only editable through a settings dialog. With Codex you can `cat` the file, you can grep it, you can copy it across machines. That's what memory should look like.

## Plugins: Where The Super App Becomes A Super App

The ninety-plus plugin number is the headline, but the more useful framing is what plugins actually are. In the new Codex architecture, a plugin is a packaging unit that bundles three things: skills (reusable workflow files), app integrations (the OAuth handshake to Slack or Gmail or Notion), and MCP server configuration. One install gives you all three for that tool.

I tested the Slack, Gmail, and Notion plugins because those are the three I actually live in.

The Gmail plugin is where I had my "this is different" moment. I asked Codex to scan the last 30 days of email for brand-sponsorship offers, extract sender, brand name, offered amount, deadline, and any links to a brief. It walked through inbox in batches, ignored newsletters and notifications, found six legit sponsorship inquiries, two scam attempts that pretended to be sponsorship inquiries, and one offer I had completely forgotten about that was about to expire. It dumped the results into a markdown table and offered to draft replies for the legit ones.

The actual quality detail that made me trust it: it correctly classified one email from a brand I had previously declined work with as "previously declined, do not re-engage" — because that classification lived in my agents.md from a note I had written months earlier. The memory layer and the plugin layer composed correctly. That's a small thing on paper. In practice, it's the thing that turns "AI features" into "AI workflows."

The Slack plugin lets Codex read channels, post messages, and respond to threads. I have one set up to summarize my agency's `#client-updates` channel into a daily briefing. Works. The Notion plugin reads pages, creates pages, and updates databases. I use it to push my weekly content plan from a Notion database into actionable tasks. Also works.

What didn't work as cleanly: combining plugins on a multi-step task that crossed permission boundaries. I asked Codex to "read my Gmail for any client invoice questions, look up the invoice status in Notion, and post a summary to Slack." It got partway through, then stopped to re-confirm Notion access because the session token had timed out, then asked again about Slack permissions, then finally completed. The end result was right. The flow was bumpy. Fix is coming, per the changelog, but as of late April 2026 the cross-plugin handoff isn't quite the seamless thing the demo videos suggest.

## Skills: The Reusable Workflow Layer

If plugins are the surface integration, skills are the workflow logic. A skill in Codex is a small markdown file with a frontmatter block that tells Codex when to invoke it, what tools it needs, and what steps to follow. They live in your skills directory and get invoked either by slash command or automatically when the model decides the task matches.

I built one called `/brand-deal-researcher`. It's a markdown file maybe forty lines long. The flow it encodes:

1. Take a brand name as input
2. Search the web for recent news about the brand
3. Look up their last 6 months of marketing spend reporting (where public)
4. Cross-check with my Gmail history for any prior contact
5. Check my Notion database of past deals for similar brands
6. Output a one-page brief with go/no-go recommendation

I built the first version in about twenty minutes. The first run was decent but missed the Gmail history step. I edited the skill file, added an explicit "always check Gmail before recommending" line, ran it again, worked perfectly. The fact that skills are plain text files you can edit is the same design choice that makes agents.md work. There's no GUI, no proprietary format, no "compile your skill" step. You write markdown, Codex reads it.

Skills also chain. I have another skill called `/brand-deal-replier` that takes the output of the researcher and drafts a reply email. The two compose. That's exactly the kind of small-pieces-loosely-joined architecture I want from an agent platform, and it maps directly onto how I think about [agent skills in Claude Code](https://www.mejba.me/claude-code-agent-skills-guide). The two ecosystems are converging on the same idea: workflows as version-controlled markdown files, not buried in a vendor's database.

## Built-In GPT-image-1.5: Image Generation Inside Projects

This one I almost dismissed. Image generation feels like a side feature when you're shipping computer use and Chronicle in the same release. Then I tried using it inside an actual content project and saw why OpenAI bundled it.

The flow that made it click: I was building a landing page mock-up. I asked Codex to generate three hero image variants in the project folder. It used GPT-image-1.5, dropped three PNGs into `assets/hero/`, and then — without me asking — used those exact files in the HTML it scaffolded next. No copy-paste. No "save image, upload, reference." The image and the code lived in the same project context, so the image generation step and the implementation step were one continuous workflow.

For solo operators building marketing sites, course landing pages, or any kind of asset-heavy project, this is the feature that quietly removes the most steps from your day. I'm not going to pretend GPT-image-1.5 is a Midjourney replacement for finished commercial art — it isn't, and the styles still have a slight "AI image" sheen on photorealistic work. But for placeholder hero shots, icons, illustrations, and the in-between assets that drown projects in switching cost, having it inline is the right call.

If you'd rather have someone build out the full content-plus-design workflow for your brand, I take on these engagements through [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD) — and I cover the structure I use in [the AI design system platform post](https://www.mejba.me/claude-code-ai-design-system-platform).

## Computer Use: The Canva Test

This is the feature that gets the most demo airtime, and rightly so. Codex's computer use lets it control your mouse, your keyboard, and your screen. Open apps. Click buttons. Fill forms. Drag files. The whole desktop becomes the agent's tool surface.

I tested it on two workflows. First, building a Canva presentation. Second, manually testing a web app I'd shipped.

The Canva test went like this. I asked Codex to build a six-slide presentation pitching a service offering, using my brand colors, and to use Canva so I could keep editing it after. Codex opened Canva in the in-app browser, signed me in via the saved session, picked a template close to my brand style, then went slide by slide. Cover. Problem. Solution. Process. Case study. Call to action. It typed the copy. It clicked the color picker. It dragged the brand color hex codes from a note I had open in another window. It saved the presentation. The whole thing took about twelve minutes.

The catch: the typography on slide three was wrong. Codex picked a font from Canva's recommended list instead of the one I'd set as my brand default. I told it to fix slide three's typography to match the others. It took the correction, applied it, and got it right on the second pass. Total elapsed time including the correction: maybe fifteen minutes for a presentation that would have taken me forty-five minutes if I'd built it cold.

The web app test went differently. I had Codex open my staging URL, log in as a test user, run through the new onboarding flow I'd just shipped, and tell me where it broke. It found two real bugs — a button that was visible but not clickable on mobile widths, and a form validation message that disappeared too fast to read. Both were the kind of bug you'd catch in QA but wouldn't catch in unit tests. Codex caught them in the same run and wrote up reproduction steps in markdown.

The honest critique: computer use is slower than direct API calls. When Codex can talk to a service through the API (Slack, Gmail, Notion via plugins), it's fast. When it has to drive a browser or a desktop app pixel-by-pixel, it's at human-typing speed. For tools without APIs, this is a transformative capability. For tools with APIs, it's the slower fallback. Knowing when to use which mode is a real skill, and Codex's auto-routing isn't perfect — sometimes it'll pick computer use for something the plugin would have handled in a quarter of the time.

This is also where the most direct comparison with Claude Code emerges. Claude Code with the right harness can also drive computer use, but Codex packages it natively into the desktop app. Claude Code is a CLI tool that you bring computer use to. Codex is a desktop app that has computer use built in. Different mental models. Different ergonomic ceilings. I still use Claude Code for deep coding sessions because the terminal-first UX wins on real engineering work — covered more in [my Claude Code and Codex two-agent workflow post](https://www.mejba.me/claude-code-codex-two-agent-workflow). But for non-engineering computer use — the Canva, the QA, the form-filling — Codex's UI is the better tool.

## Automations: Friday 9 AM Reports

Automations are scheduled, recurring Codex runs. You define the prompt, you define the schedule, you define which plugins and skills it can use, and then it runs without you. OpenAI's docs frame these around "status reports, triage, monitoring, maintenance workflows." That's roughly the shape of what I built.

My first automation: every Friday at 9 AM, run the brand-deal researcher skill against any new sponsorship inquiries from the past week, check the calendar for next week's content schedule, summarize active client projects from Notion, and email me the result. It runs whether I open the laptop or not. If I open Codex on Friday afternoon, the report is already in my inbox.

The second one: every weekday at 5 PM, scan my open editor tabs (via Chronicle context), grep recent commits, and ask whether anything looks unfinished. This one I built more as a forcing function than a real workflow — the goal was an end-of-day "did you remember the thing?" nudge.

The third one is the one I'm most cautious about, and it leads naturally into Chronicle.

## Chronicle: The Privacy Trade-Off That Made Me Pause

Chronicle is the feature that made me turn off Codex for forty-eight hours and think.

What it does: with your opt-in consent, Chronicle takes regular ephemeral screenshots of your Mac in the background, sends them to an ephemeral Codex session on OpenAI's servers for processing, and stores the resulting structured memories as local markdown files. Screenshots get deleted after six hours. The structured memories — what apps you used, what content was on screen, what you typed in titles — stay locally, unencrypted, indefinitely until you clear them.

The pitch is real. Chronicle gives Codex live context. When I'm editing a slide deck and ask Codex "make slide three better," it knows what slide three is because it watched me build it. When I'm midway through a debug session and ask "what was that error from earlier," it can pull the error message from twenty minutes ago because it screenshotted the terminal when the error fired. The friction reduction is genuine. The presentation-improvement use case from the demo isn't fake — I tested it on my own decks and the suggestions were on the money.

The trade-off is also real, and the press around it has not been kind. The Register ran a piece comparing Chronicle to Microsoft Recall's privacy footprint. SC Media flagged that the unencrypted markdown storage is a softer target than Recall's encrypted local database. The EU, UK, and Switzerland are explicitly excluded from the rollout — that's not a soft warning, that's OpenAI knowing the regulatory ground isn't ready.

Here's my honest take after running Chronicle for four days. The screenshot capture is genuinely ephemeral — I checked, the six-hour deletion happens. The screenshots do leave your machine and travel to OpenAI's servers for processing, even though the resulting memories come back local. That round trip is the part that matters. Anyone telling you Chronicle is "fully local like Recall" hasn't read the docs. It isn't.

The structured memories file, in plain markdown on disk, is searchable by any process that has access to your home directory. If your laptop is compromised, those markdown files are an inventory of your last few weeks of life — every app you opened, every doc you edited, every error you saw. There's no biometric lock. There's no encryption layer. There's just a markdown file, exactly the kind that auto-syncs cleanly to Dropbox or iCloud Documents if you've left those features on.

I'm keeping Chronicle off on my main work machine. I'm running it only on a separate Mac mini I use for content workflows where the privacy threat model is different and the productivity gain on presentation editing is worth it. That's a judgment call. Yours might be different. What I won't do is what some of the early adopters are doing — leaving it on by default, on a single primary machine, on a workflow that touches client data and personal banking. The risk-to-reward math doesn't work there.

If you're in the security space, my [xCyberSecurity walkthroughs](https://www.xcybersecurity.io) are where I dig deeper into client-data threat models. The short version: opt-in surveillance tools are fine when the threat model and the data sensitivity are matched. Defaulting Chronicle on for everything is a mismatch.

## Honest Verdict: What's Worth It, What's Overhyped

Two weeks in, here's how I'd rank the eight features by actual impact on my workflow:

**Genuinely transformative:**
- **Full file access** — the receipts test alone makes this a permanent part of my stack
- **Plugins (Gmail and Notion specifically)** — the email triage workflow is the one I won't give up
- **Skills** — the moment you've built three of them, you're in a different productivity tier
- **Computer use for non-API tools** — the Canva and QA flows are real wins

**Useful but situational:**
- **Persistent memory** — works once you treat agents.md as the source of truth and the auto memory as advisory
- **Built-in image generation** — quietly removes friction in asset-heavy projects
- **Automations** — only worth the setup time if you have 2+ recurring tasks worth scheduling

**Wait and see:**
- **Chronicle** — capability is real, the privacy posture is wrong by default, run it on a separate machine if at all

The bigger comparison question — Codex vs Claude Code — is the one I keep getting asked. The honest answer: they're different shapes of the same problem. Claude Code wins on deep engineering work, terminal-native ergonomics, and agent-team coordination patterns. Codex wins on desktop ergonomics, computer use for non-coding tasks, and the all-in-one bundle of plugins, skills, memory, and automations. I run both. So should you, if your work spans engineering and operations.

The thing that's actually changed with this April 2026 release is that the "super app" framing is no longer marketing. An agent that lives on your machine, sees your screen optionally, runs scheduled jobs, controls your apps, and remembers your preferences is what people imagined AI assistants would be when GPT-3 first shipped. We're closer to that reality than we've ever been. Whether you want to live in that reality with Chronicle running, or with Chronicle off and your privacy threat model intact, is the actual decision in front of you.

For me, the answer is "yes to almost everything, no to default Chronicle, and a separate machine for the experimental stuff." For you, the answer depends on what's on your screen at any given moment and how much you trust an unencrypted markdown file in your home directory to stay there.

The receipts, by the way, are still sitting in my Excel dashboard. Categorized. Totaled. Cross-checked. I haven't thought about them since the day I dragged that folder onto Codex. That's the bar.

## Frequently Asked Questions

### Is OpenAI Codex free or do I need a paid plan?
Codex is included with ChatGPT Plus ($20/mo), Pro ($100 or $200/mo), Business, Edu, and Enterprise plans — there's no separate Codex subscription. Free ChatGPT users don't get desktop Codex access. Pro tiers get 5x the Codex usage of Plus, with a 2x bonus running through May 31, 2026.

### What's the difference between Codex and ChatGPT Agent?
Codex is a full desktop application with local file access, plugins, computer use, and scheduled automations. ChatGPT Agent runs in a cloud sandbox without persistent local file access. Codex is the platform; Agent is a cloud-only subset of similar capabilities.

### Is Chronicle safe to enable?
Chronicle takes screenshots that travel to OpenAI's servers for processing and stores resulting memories as unencrypted local markdown files. It's safer than letting an open browser scrape your screen, less safe than encrypted local-only solutions like Microsoft Recall. I'd run it on a secondary machine, not on a primary work machine touching client data.

### Can Codex replace Claude Code for coding work?
Not yet, in my experience. Codex is excellent for desktop computer-use workflows, plugin-driven tasks, and image generation in projects. Claude Code is still ahead for deep terminal-native engineering work, agent-team coordination, and complex codebase reasoning. I run both.

### Where are agents.md and the auto memory files stored?
The manual `agents.md` lives wherever you put it — typically the project root or your home directory. The auto-memory file is in Codex's app data directory and is editable as plain markdown if you want to correct overgeneralizations. Both are version-controllable and human-readable.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
