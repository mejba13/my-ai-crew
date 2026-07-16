**BRAND:** mejba.me
**TITLE:** NotebookLM + Gemini Gems: The PACT Build I Actually Use
**META TITLE:** NotebookLM Gemini Gems Workflow: My PACT Blueprint
**SLUG:** notebooklm-gemini-gems-pact-framework
**PRIMARY KEYWORD:** NotebookLM Gemini Gems workflow
**META DESCRIPTION:** I built four production AI assistants with free Google tools using the PACT framework. Here's the NotebookLM + Gemini Gems blueprint and time saved.
**TAGS:** Google Gemini, NotebookLM, AI Agents, Prompt Engineering, Tutorial

---

The proposal was due in three hours. Standard scope, returning client, the kind of thing I've written maybe forty times. I opened a fresh Gemini chat, dumped the brief, and asked for a first draft. Forty seconds later, I had a proposal that quoted a price 35% below my actual rate card, hallucinated a "case study" with a client I've never worked with, and used the phrase "synergistic value alignment" twice.

I closed the tab. Opened ChatGPT. Same prompt. Different draft, different problem — this one was technically accurate but read like it was written by a corporate intern who had never met me, and it ignored my pricing tier structure entirely.

That was the second time that week. And it was the moment I stopped pretending that "good prompting" was going to fix this. The issue wasn't my prompt. The issue was that I was asking a stateless chat interface to do a job that requires *memory of who I am, what I sell, what I charge, and what I've shipped before*. Of course it was failing. I was using the wrong tool.

Three weeks later I have four production AI assistants — Email Drafter, Content Writer, Proposal Writer, and Support Assistant — running on nothing but free Google tools. Same proposal task that used to take me 50 minutes now takes 15. The output is on-brand, on-rate, and grounded in real case studies because the assistant is reading from a knowledge base I built, not making things up. No subscriptions beyond Google AI Pro (which I already had for other reasons). No API costs. No platform lock-in.

The unlock was a tiny mental shift: stop trying to make one tool do two jobs. Use NotebookLM for the *knowledge layer* and Gemini Gems for the *behavior layer*. Glue them together with a four-part instruction framework called PACT. That's the whole trick. The rest of this post is the exact blueprint, including the full instruction text I'm using inside every gem.

## Why "Just Use ChatGPT" Stopped Working For Real Work

Before the builds, let me name the two failure modes that pushed me into this stack — because if you haven't hit them yet, you will, and naming them helps.

**Failure mode one: fabrication under pressure.** Generic chat models will confidently invent statistics, case studies, client names, and policy details when you ask them to draft business documents. They don't do this maliciously. They do this because the request "write a proposal for my agency" is under-specified, and the model fills the gap with plausible-sounding nonsense. The faster you need the output, the less you proofread, and the more often the fabrication ships. I shipped a proposal in February with a fabricated client testimonial in it. The client (the new one, not the fictional one) caught it. That conversation was not fun.

**Failure mode two: drift across conversations.** Even when you carefully prompt a chat to behave a certain way — your tone, your structure, your rules — that behavior lives only inside that single conversation. Open a new chat tomorrow and you start over. Try to share your "great prompt" with a teammate and they get inconsistent results because they tweaked one word. There is no persistence. The behavior never compounds. Every conversation is a fresh negotiation with the model.

Both failure modes have the same root cause: **chat interfaces are stateless and source-less by default**. They don't know your stuff. They don't remember your rules. And that's fine for "explain quantum entanglement to me" but catastrophic for "write a client-facing document that represents my business."

NotebookLM and Gemini Gems each solve exactly one of those problems. Pair them, and both problems go away.

## What NotebookLM Actually Does (Beyond The Audio Overview Hype)

Most people I know discovered NotebookLM through the Audio Overview feature — the eerily good fake podcast that makes two AI hosts banter about your PDFs for ten minutes. That demo went viral for good reason. It's genuinely impressive. But it's also the least useful thing NotebookLM does.

Here's what NotebookLM is actually for: it's a **source-grounded research environment**. You upload up to 300 sources per notebook (PDFs, Google Docs, websites, YouTube videos, pasted text, and now Loom and a handful of other video formats). NotebookLM indexes those sources, and every answer it gives you is constrained to only what's in those sources. If the answer isn't in your uploads, NotebookLM tells you it can't find it instead of inventing one. Every response includes citations — clickable footnotes that take you to the exact passage in the exact source where the claim came from.

That citation behavior is the entire point. It's the difference between an AI that helps you write and an AI you can actually trust to draft something that has your name on it.

The Studio panel sits on the right side of every notebook and turns those grounded sources into derivative artifacts in one click: **Briefing Doc** (the one that matters most for our purposes — a structured summary of every key idea, finding, and quote across your sources), **Study Guide**, **Mind Map**, **Audio Overview**, **Video Overview**, and now **Reports** with custom templates you can save. The Briefing Doc is what we'll feed into Gemini Gems in the workflow below. It's the bridge.

NotebookLM is free to start, and it's surprisingly generous on the free tier — you get up to 100 notebooks with 50 sources each. If you have Google AI Pro at $19.99 a month, you jump to 500 notebooks and 300 sources per notebook plus higher daily limits. Workspace Business and Enterprise tiers include NotebookLM access by default as of early 2026.

What NotebookLM cannot do: it cannot consistently behave like a specific person or brand. It can answer questions about your sources beautifully. But it has no persistent persona. No locked instructions. No "always respond in this format." It's a brilliant research assistant. It is not an employee.

That's where gems come in.

## What Gemini Gems Actually Do (The Persistence Layer)

A gem is a saved Gemini configuration. That's it. Under the hood, it's an instruction block (up to 10,000 characters), an optional set of knowledge files (up to 10), and a name. You give the gem instructions once, and every conversation you start with that gem inherits those instructions automatically. No drift. No re-prompting. No "wait, can you respond like before?" — the behavior is locked.

The killer feature isn't any single gem. It's that gems persist across conversations and can be shared with a team. If I build a "Mejba's Email Voice" gem and tell it exactly how I write replies, every email I draft through that gem sounds like me. Every. Single. One. If a teammate uses the same gem, their drafts sound like me too. The gem becomes the single source of truth for that behavior.

As of May 2026, gems are available on the free Gemini tier (with daily prompt limits), Google AI Plus ($7.99/month), Google AI Pro ($19.99/month, which gives you Gemini 3.1 Pro at the full 1M context window), and Workspace Business Standard and above. The free tier gives you enough to test all four of the builds below — you only need to upgrade if you're hitting daily message caps.

You access gems through the gem picker on the left rail of the Gemini app, or — and this is the part most people miss — through Google Docs. When you're in a Doc and pull up Gemini in the side panel, you can pick a specific gem to draft or edit with. That single integration is what made me delete my Notion template library. More on that later.

So: NotebookLM gives the assistant *what to know*. Gems give the assistant *how to behave*. Pair them, and you have something that looks an awful lot like a junior employee who never forgets the brand guide and never invents a case study.

## The PACT Framework: Four Slots, Zero Drift

Every gem instruction I write follows the same structure. It's called **PACT** — Persona, Assignment, Context, Template. Google's docs sometimes call the fourth slot "Format." Same idea. PACT is what most people write below before-they-realize-they-need-a-framework, but writing it explicitly forces clarity and makes gems easy to revise.

Here's what each slot does:

**Persona** — Who is this gem? Not "an assistant." Specifically: a 7-year B2B copywriter who specializes in SaaS proposals. A senior front-desk manager at a boutique hotel. The head of customer success at a Series B fintech. The more specific the persona, the more consistent the output. Vague persona means generic output.

**Assignment** — What is the one job this gem does? Not three jobs. One job. A gem that "writes emails, drafts proposals, and answers support tickets" will be mediocre at all three. A gem that "drafts proposal first drafts using the uploaded pricing sheet and case studies" will be excellent at exactly that. Resist the urge to make a gem do everything. Build four gems instead of one super-gem.

**Context** — What rules, constraints, voice notes, taboos, and reference material does this gem need to do its job well? This is where you put your brand voice rules ("never use the word 'leverage'"), your business rules ("always quote tier-2 pricing unless explicitly asked otherwise"), and your hard limits ("if asked about a topic outside the uploaded sources, respond with 'I don't have that in our knowledge base — please check with [human]'"). Context is the longest section of every gem instruction.

**Template** — What does the output look like? Word count, sections, tone, format. "Reply as a single email under 120 words, with no greeting and no sign-off — I'll add those manually." Or "Output a Markdown table with three columns: Section, Recommendation, Source." If you skip this slot, the gem will improvise the structure every time and you'll spend ten minutes reformatting.

That's the framework. Four slots. Now let me show you what it looks like filled in for four real assistants I'm using right now.

## Build 1 — The Email Drafter Gem (Gems Only, No NotebookLM)

The simplest build, and the one I recommend you start with. No knowledge base needed. Pure persona and template work. Goal: every email I draft sounds like me, regardless of how rushed I am.

Here's the actual instruction text in this gem:

```
PERSONA
You are Mejba's email voice. Mejba is a software engineer and AI
developer who runs a small agency. He writes the way he talks: direct,
warm, slightly informal, no corporate filler. He uses short sentences.
He occasionally uses dashes — like that — for emphasis. He never opens
with "I hope this finds you well." He never closes with "Best regards."

ASSIGNMENT
Take the rough notes I paste in and turn them into a complete email
ready to send. One draft per request. No options, no alternatives.

CONTEXT
- Audience is usually a paying client or a prospect.
- Default tone is professional-friendly. Lift formality up if the input
  notes suggest the recipient is senior or new. Lift formality down for
  ongoing collaborators.
- Banned phrases: "circle back", "touch base", "synergy", "leverage",
  "moving forward", "at your earliest convenience", "kindly".
- If the notes mention a price, use it exactly as written. Never round.
  Never invent context around a number.
- If the notes are ambiguous on a key fact, ask one clarifying question
  before drafting. Do not guess.

TEMPLATE
- Subject line on its own line, prefixed with "Subject:"
- One blank line
- Greeting: "Hi [name]," — first name only unless notes specify otherwise
- Body: 2-4 short paragraphs, max 150 words total
- Closing: "— Mejba" on its own line. Nothing else.
```

That's 1,400 characters. Took me about 20 minutes to refine across six test drafts before it locked in. Now every email I send goes through it. I paste three lines of notes — "reply to Sarah about the timeline slip, push delivery to May 22, blame the Stripe API change, offer 10% credit" — and I get back a finished email in my voice in about eight seconds. I edit maybe one word per draft. Sometimes none.

The before-and-after is stark. Before: 4-7 minutes per email, written from scratch, varying quality based on how tired I was. After: 15 seconds of paste-edit-send. Across an average week of 30-40 client emails, that's a few hours back. And the consistency means clients never get the version of me that was writing at 11pm with three coffees.

## Build 2 — The Content Writer Gem (NotebookLM + Gems)

This is where the pairing starts to matter. Goal: draft blog posts that actually sound like the rest of my site, grounded in the source material I've already researched, without hallucinating sources or drifting into generic AI prose.

The workflow has two parts. First, build the knowledge.

I created a notebook in NotebookLM called "Mejba.me Voice + Style." Into it I uploaded:

1. **Six of my best-performing posts as PDF exports** — the ones that captured my voice cleanly.
2. **My personal style guide** — a Google Doc I'd written months ago listing banned phrases, sentence rhythm rules, the way I open hooks, the way I close posts.
3. **Three reference posts I admire from other writers** — labeled in the source description as "external — voice influence only, never quote."
4. **A list of internal posts I've already published**, with URLs and one-line summaries — so the gem knows what to link to and what topics I've already covered.

Then in NotebookLM Studio, I clicked **Briefing Doc**. Got back a 4-page structured summary of my voice, my style rules, my recurring themes, and my internal link inventory. Copied that briefing doc into a Google Doc called "Voice Briefing." That doc becomes the gem's knowledge file.

Now the gem itself:

```
PERSONA
You are Mejba's blog co-writer. Mejba writes long-form first-person
posts about AI tools, software engineering, and Claude Code workflows
at mejba.me. The voice is practitioner-first: he tests things, ships
things, breaks things, and reports back. The reader is another
developer who can spot AI filler from a mile away.

ASSIGNMENT
Draft a long-form blog post (3,000+ words) on the topic I provide,
following the voice rules and structural patterns in the attached
Voice Briefing knowledge file. One draft per request.

CONTEXT
- Read the Voice Briefing carefully before drafting. Match sentence
  rhythm, hook style, section bridge style, and banned phrase list.
- Open with a specific moment, scene, or confession. Never open with
  "In today's..." or "In this post..."
- Use first person throughout. "I tested...", "I shipped...",
  "I was wrong about..."
- Vary sentence length aggressively. A 4-word sentence next to a
  35-word sentence is good. Three identical-length sentences in a row
  is a bug.
- Cite specific tool versions, dates, and numbers. Never write
  "recently" or "the latest version" — anchor to a date.
- Internal linking: when a topic in the post overlaps with a published
  post in the link inventory, suggest the link inline as a Markdown
  link. Do not invent posts that aren't in the inventory.
- If asked to write about something you don't know enough about,
  output a list of research questions instead of a guessed draft.

TEMPLATE
- H1 title (under 60 chars)
- Hook (200-400 words, no header)
- 6-9 H2 sections, each 400-700 words
- Each H2 ends with a one-sentence bridge to the next
- Strong close (no "in conclusion") — 200-300 words
```

When I ask this gem to draft a post, the output isn't a generic AI blog. It opens with a scene. It links to my real existing posts. It uses dashes the way I use dashes. It refuses to invent statistics. It still needs editing — about 25-30% of the words get rewritten — but I'm starting from a draft that's 70% mine, not 0%. That changes the math on how often I publish.

## Build 3 — The Proposal Writer Gem (The 50-to-15 Build)

This is the build that paid for itself in the first week. Goal: client proposals drafted in 15 minutes instead of 50, on-rate, with real case studies and honest scope.

The notebook took an hour to set up. I uploaded:

1. **My current pricing sheet** as a PDF — every tier, every line item, every "starts at" anchor.
2. **Eight anonymized case studies from past projects** — what the client needed, what I built, the timeline, the outcome, the deliverables.
3. **My ideal client profile (ICP) doc** — what kinds of projects I take, what I refuse, what red flags I screen for.
4. **Three of my strongest past proposals** as exemplars — the ones that closed.
5. **My agency boilerplate** — about me, about the team, terms, payment schedule, IP language.

Generated a Briefing Doc from the Studio panel. Saved that as a Google Doc. Attached it to the gem as a knowledge file.

The gem instructions:

```
PERSONA
You are a senior B2B proposal writer with 7 years of experience writing
SaaS and agency proposals that close. You work for Mejba's agency. You
know the pricing sheet cold. You know which case studies match which
prospect type. You write proposals that respect the prospect's time.

ASSIGNMENT
Take the prospect intake notes I paste in and draft a complete
first-pass proposal using the attached knowledge file. The draft is
for Mejba to edit, not to send directly.

CONTEXT
- Read the pricing sheet carefully. Quote real prices from the sheet.
  Never invent a price. Never round to suggest a lower number.
- Match the prospect to the closest of the eight case studies. Reference
  one (1) case study by name in the proposal. If no case study fits,
  say so and skip the section instead of inventing one.
- Default to the tier-2 engagement unless intake notes clearly indicate
  enterprise (>10 seats) or starter (< $5k budget).
- If intake notes contain ICP red flags (no defined success metric,
  payment-on-completion only, deliverable-vs-outcome confusion), flag
  them at the top of the draft in a "REVIEW BEFORE SENDING" block.
- Honest scope. If a request looks unrealistic in the proposed timeline,
  call it out and propose a phased alternative.
- Use the agency boilerplate verbatim for the "About" and "Terms"
  sections. Do not paraphrase boilerplate.

TEMPLATE
- Cover line: "Proposal for [Company] — [Project Name]"
- Section 1: Understanding (3-4 sentences mirroring back what they need)
- Section 2: Proposed Scope (bulleted, max 8 bullets)
- Section 3: Approach (3 short paragraphs, not bullets)
- Section 4: Timeline (table: phase / weeks / deliverable)
- Section 5: Investment (line items + total, from pricing sheet)
- Section 6: Relevant Work (one case study, 100 words)
- Section 7: About + Terms (boilerplate, verbatim)
- Total length: 800-1,200 words
```

Before this gem, every proposal was a from-scratch effort. Open the template, paste the brief, write the understanding section, dig through Notion for the right case study, copy-paste pricing, format everything. 50 minutes if I was focused. 80 if I was distracted.

After this gem: I paste the intake notes, get a finished first draft in 90 seconds, and spend 12-13 minutes editing — adjusting one or two scope bullets, tightening the case study summary, double-checking the timeline. Total elapsed time: 15 minutes. That's a ~70% time saving on a task I do 4-6 times a month. The math works out to a few full working days a year, returned. Not theoretical days. Real ones.

The bigger win isn't the time. It's that the proposal quality is more consistent. I'm no longer writing my best proposal on Tuesday morning and my mediocre one on Friday afternoon. The gem doesn't have a Friday afternoon.

If you'd rather have someone build this kind of system end-to-end for your business — knowledge base, gems, workflow integration — I take on these engagements. You can see what I've shipped at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Build 4 — The Support Assistant Gem (On-Policy Replies)

The fourth build is the one I think will matter most for teams. Goal: customer or community support replies that are on-policy, on-brand, and never invent a refund window that doesn't exist.

Notebook contents:

1. **Full refund and cancellation policy** as a Google Doc.
2. **Top 50 FAQ entries** scraped from my support inbox over the past year, with the canonical answer for each.
3. **Brand voice guide** — tone, vocabulary, how we apologize, how we say no.
4. **Escalation rules doc** — exactly which scenarios get escalated to a human and what the routing is.

Briefing Doc generated. Gem built. Instructions:

```
PERSONA
You are the front-line support assistant for Mejba's agency. You are
calm, warm, technically precise, and direct. You do not over-apologize.
You do not make promises the policy doesn't support. You treat the
customer like an intelligent adult.

ASSIGNMENT
Draft a reply to the inbound message I paste in, using only the
information in the attached knowledge file (policies, FAQs, brand voice).
The reply is reviewed by a human before sending.

CONTEXT
- Stay strictly within the policy and FAQ knowledge file. If the
  customer asks something the knowledge file does not cover, do not
  guess. Output: "ESCALATE: not covered in knowledge base" and
  suggest two clarifying questions a human could ask.
- If the customer is asking about a refund or cancellation, quote the
  exact policy language. Never paraphrase policy language.
- If the customer's tone is angry, acknowledge the frustration in one
  sentence before answering. Do not over-apologize. One acknowledgment.
- Use the customer's name if provided. If not, no greeting name.
- Match the brand voice guide on tone and vocabulary.

TEMPLATE
- One paragraph reply, 60-150 words.
- No greeting line ("Hi X,") — the human reviewer adds that.
- No closing line — the human reviewer adds that too.
- If escalation is needed, the entire output is the ESCALATE block.
```

I've been running this on my own inbox for two weeks. About 60% of incoming messages get a draft reply that I send with zero edits. About 30% get a draft I lightly edit (usually adding a personal detail the gem couldn't know — "congrats on the launch last week"). About 10% trigger the escalate block, which is exactly the behavior I want — I'd rather the gem refuse than guess.

The compounding effect is what makes this build matter for teams. Every time we add a new FAQ to the knowledge base, the assistant immediately knows it. Every policy update ripples through every future reply. And because the policy is the source of truth, no support agent (human or AI) can drift from it. That's a level of consistency that's almost impossible to enforce with humans alone.

## The Generalized Build Flow (Save This Part)

Once you've built one of these, the pattern repeats. Here's the playbook for any new assistant you want to build.

**Step 1 — Define the one job.** Write a single sentence. "This gem drafts X using Y, for Z audience." If that sentence has the word "and" in it, split into two gems.

**Step 2 — Build the notebook.** Create a new notebook in NotebookLM. Upload everything that an expert version of this assistant would need to know to do the job: policies, pricing, voice guides, exemplars, taboos, ICP docs. Aim for 5-15 sources. More than 30 and you're probably trying to make one notebook do too many jobs.

**Step 3 — Generate the Briefing Doc.** Open the Studio panel. Click Briefing Doc. Wait 30 seconds. Read it. If it captures the soul of what's in your sources, save it as a Google Doc. If it doesn't, your sources are probably too scattered — go back and tighten them.

**Step 4 — Create the gem.** Go to Gemini, open the gem picker, click "New Gem." Name it. Paste in your PACT instructions. Attach the Briefing Doc as a knowledge file (you get up to 10 attached files per gem, 100MB total). Save.

**Step 5 — Test against your hardest real input.** Don't test with a toy example. Take the most complex real piece of work the gem will ever face and run it through. Note where the output falls short. Edit the gem instructions to fix that gap. Re-test. Repeat until the output is 70%+ shippable.

**Step 6 — Wire it into your workflow.** This is the step most people skip. The gem is only useful if you use it. For me that means: pinning the gem at the top of my Gemini sidebar, pulling it up inside Google Docs through the side panel, and — for the proposal writer specifically — adding a Drive shortcut so I can right-click on a client folder and "Open with Gemini." Drive search lets you find any gem by name once you've enabled it in settings, which makes sharing across the team trivial.

**Step 7 — Maintain the knowledge base.** Once a month, open the source notebook and check whether anything is stale. New pricing? Add it. New case study? Add it. Old policy that's been retired? Remove it. Re-generate the Briefing Doc. Re-attach to the gem. The whole maintenance cycle takes about 20 minutes a month per gem and is the entire reason these systems get *better* over time instead of decaying like every other automation you've built.

## Sharing Gems With A Team (The Multiplier)

Single-user gems are useful. Team-shared gems are transformative. As of early 2026, Workspace Business Standard and above let you share gems with anyone in your domain. Sharing is identical to sharing a Google Doc — pick people or groups, set view or edit permissions, send.

The implication: your best operator's brain becomes a tool the rest of the team can run. Your best proposal writer leaves the company? Their PACT-defined gem stays. Your best support agent goes on vacation? Their reply pattern keeps shipping. The institutional knowledge that used to walk out the door at 6pm now lives in a shared knowledge base and a shared gem.

I'm not pretending this replaces hiring senior people. It doesn't. But it does make junior people three times more productive on the high-volume, pattern-based work — and it frees the seniors to do the genuinely novel work that gems can't touch.

## Where This Beats Claude Projects And Custom GPTs (And Where It Doesn't)

Honest comparison time. I use Claude Projects, Custom GPTs, and Gemini Gems all weekly. They are not the same tool, and the right answer is "use whichever fits the job."

**Where NotebookLM + Gems beats the alternatives:** source citations are first-class. NotebookLM cites every claim with a clickable footnote. Custom GPTs and Claude Projects can attach documents but don't surface citations the same way. If your job is drafting documents that need to be defensible against "where did this number come from?" — gems win.

**Where NotebookLM + Gems beats on integration:** the Google Docs side panel lets you call any gem inside any Doc. That's enormous for content work. Custom GPTs require copy-paste between ChatGPT and Docs. Claude Projects don't natively wire into Docs at all.

**Where Claude Projects beats gems:** raw model quality on long, nuanced reasoning tasks still favors Claude in my testing. If the job is "read 80 pages and synthesize a position paper," Claude's output is more thoughtful. Gems are better for high-volume pattern work; Projects are better for low-volume hard thinking.

**Where Custom GPTs beat gems:** the GPT Store ecosystem and the broader ChatGPT toolchain (Code Interpreter, advanced data analysis) is more mature. If your assistant needs to run code or analyze a CSV, GPTs are still the better fit.

**When to graduate beyond all of them:** the moment you need real automation — triggered runs, file-system access, multi-step agentic workflows that touch your actual codebase or production systems — gems hit a ceiling. That's when I move the workflow to Claude Code skills, which I've written about in [my breakdown of Agent Skills](https://www.mejba.me/agent-skills-ai-agents-guide) and [the Claude Code agent teams playbook](https://www.mejba.me/claude-code-agent-teams-playbook). Gems are excellent for "draft this for a human to ship." Claude Code is excellent for "ship this without a human in the loop." Different tools, different jobs.

The thing I want you to take away is that you don't have to pick the most expensive tool to get a real productivity win. Free Google tools, used carefully, beat $200/month subscriptions used carelessly. Every time.

## Frequently Asked Questions

### Do I need a paid plan to use NotebookLM and Gemini Gems together?
No, both work on free tiers. The free Gemini app includes Gems with daily prompt caps, and NotebookLM is free for up to 100 notebooks with 50 sources each. Google AI Pro at $19.99/month removes the caps and gives you Gemini 3.1 Pro with the 1M context window. For testing all four builds in this post, the free tier is enough.

### How is this different from just using NotebookLM directly?
NotebookLM is a research environment — it answers questions about your sources but has no persistent persona or output template. Gems add the behavior layer: locked instructions, consistent voice, defined output format. Pairing them gives you grounded knowledge plus consistent behavior, which neither tool provides alone.

### Can I share a gem with my team?
Yes, on Workspace Business Standard and above. Sharing works exactly like a Google Doc — pick people, set permissions, send. The shared gem inherits your instructions and knowledge file, so every teammate gets identical behavior. See the team sharing section above for the full workflow.

### What's the PACT framework and why use it?
PACT stands for Persona, Assignment, Context, Template — the four slots that make up a clear gem instruction. Persona defines who the gem is, Assignment defines its one job, Context provides rules and reference material, Template dictates output structure. Following PACT prevents the most common gem failure mode: vague instructions that produce inconsistent output.

### When should I use Claude Projects or Custom GPTs instead?
Use Claude Projects for low-volume, high-nuance reasoning tasks where output quality matters more than speed. Use Custom GPTs when you need code execution or data analysis. Use Gemini Gems for high-volume, pattern-based drafting work where you want Google Docs integration and source citations. See the comparison section above for the full breakdown.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
