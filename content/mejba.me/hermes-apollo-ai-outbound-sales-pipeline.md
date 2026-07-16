**BRAND:** mejba.me
**TITLE:** I Built an AI Outbound Sales Pipeline and Cancelled $400/mo
**META TITLE:** AI Outbound Sales Pipeline: Hermes + Apollo + Claude Code
**SLUG:** hermes-apollo-ai-outbound-sales-pipeline
**PRIMARY KEYWORD:** AI outbound sales pipeline
**META DESCRIPTION:** I replaced a $400/mo sales stack with Hermes, Claude Code, and Apollo. Here's the exact AI outbound sales pipeline, the prompts, and honest ROI math.
**TAGS:** AI Agents, Sales Automation, Claude Code, Hermes Agent, B2B Lead Generation

---

The cancel-subscription email I sent Lemlist on a Tuesday afternoon in May was the smallest victory of the quarter and somehow the one I'm most proud of. Twenty seconds of clicking. About $97 a month off the credit card. Combined with the Apollo seat I'd downgraded the week before and the Clay trial I'd let expire, the running tally hit somewhere north of $400 a month I'd stopped paying — and my outbound pipeline was *better*, not worse, than when I was paying for all of it.

The thing that replaced it isn't a tool I bought. It's a workflow I built. The engine underneath is the same Hermes + Claude Code AI OS I [walked through end to end in my full stack setup post](https://www.mejba.me/hermes-claude-code-ai-operating-system) — that post is the architecture, the install, the cron jobs, the Pantheon personas. This post is what I do *with* that stack on the business side. Specifically: how I run an entire outbound B2B sales pipeline — scrape, score, draft, sequence, send — as a single Hermes workflow that costs me about $79 a month in API fees and roughly forty minutes a week of actual attention.

I want to walk you through the pipeline end to end. Not the pitch. The actual ICP file. The real Apollo API call. The exact prompts I feed the Copywriter persona. The lead that closed last month. The honest comparison against Clay, Smartlead, Instantly, and the all-in-one Apollo motion most agencies are running. And the ROI math, including the parts where this stack is *worse* than what it replaced.

By the end, you'll know whether the DIY route is right for your situation or whether you should keep paying SaaS rent. Both are defensible. The one I want to talk you out of is the middle road — buying half the tools and pretending you have a pipeline.

## Why I cancelled a stack that was technically working

For about eight months I ran a textbook agency outbound stack. Apollo Professional for the lead database. ChatGPT Plus for first-line personalization at scale. Lemlist for the sending and sequencing. Notion for the CRM lite. Total monthly cost hovering around $400 once I factored in the dedicated sending domain and warmup tooling. The pipeline was working — five to seven booked calls a month from cold, two to three of which turned into proposals, roughly one a quarter that became a real engagement.

By any objective measure, that's a fine sales pipeline for a solo operator. So why was I cancelling it?

Three reasons, and they're the three reasons most builders eventually end up here. First, the tools didn't talk to each other. Apollo knew the lead's job title; ChatGPT knew nothing about the lead until I pasted it in; Lemlist sent the email but never told the rest of the stack whether the lead opened it. I was the human glue between four products that had no idea any of the others existed. Every booked call cost me twenty to thirty minutes of copy-paste shuttle work I'd stopped noticing because it had become routine.

Second, the personalization ceiling was real and low. ChatGPT writing a first line from a LinkedIn bio is a 2024 trick. In 2026, that level of personalization is what the prospect's spam filter is already triaging. To get past it you need either dramatically deeper signal — what the company shipped last week, what their CTO blogged about three days ago — or you need to give up on personalization entirely and lean on volume and deliverability. The middle position is where most SaaS stacks live. It's also where reply rates have been falling since Q3 2025.

Third — and this is the one that pushed me over the edge — I already had a Hermes agent with a Pantheon of personas reading my Obsidian vault every night and dispatching tasks to Claude Code. The infrastructure for an actual AI sales operator existed inside my own setup. The Apollo API was already integrated for prospecting briefs. I'd just never asked the obvious question: *what if the entire pipeline lived inside the AI OS I've already built?*

Sixteen days of nights-and-weekends later, it did. Here's the shape of it.

## The pipeline, end to end, in one diagram I'd put on a whiteboard

Before the prompts and the API calls, the mental model. The pipeline is six stages, each owned by either Hermes, Apollo, Claude Code, or me. Mixing those owners up is how you build a pipeline that looks impressive in a demo and falls apart in week three.

1. **ICP definition.** Me. Always me. The single most expensive mistake you can make with this stack is letting an AI decide who your customer is.
2. **Apollo scrape.** Hermes Researcher persona, calling the Apollo API on a cron schedule. Raw output: a JSON file of 50–100 contacts matching the ICP.
3. **Enrichment.** Hermes again, layering in extra signal: recent company news, role tenure, technographic data, recent funding events.
4. **Scoring.** Hermes Researcher with a scoring rubric I wrote and version-control in my Obsidian vault. Output: a ranked list with confidence scores.
5. **Draft email.** Claude Code, invoked via the Labyrinth persona, using the Copywriter system prompt and the enriched lead record as context. Output: a Markdown draft per lead, saved into `02-clients/outbound-drafts/`.
6. **Gmail draft + human review.** Hermes pushes drafts into Gmail (draft-only, never send) via the Zapium MCP. Monday morning, coffee, twenty minutes, I approve and hit send myself.

That's the whole pipeline. Six stages, three owners, zero auto-send. Every other piece of complexity I considered adding — auto-reply handling, intent-based reprioritization, a sequencer that fires follow-ups without me — I deliberately left out for now. Not because I couldn't build it. Because the marginal pipeline gain didn't justify the marginal risk of an autonomous agent fat-fingering my sending reputation. We'll come back to that decision.

Now let's go through each stage with the actual config.

## Stage 1 — The ICP file that took two months to get right

I cannot overstate how much hinges on this one file. It's a single Markdown document in my Obsidian vault at `02-clients/icp/agency-saas-founders.md` and every other stage in the pipeline reads from it. If the ICP is wrong, you're using $400/mo of AI infrastructure to spam the wrong people with elegant emails. That is the worst possible outcome.

Here's the actual structure I run, redacted in places where the specifics are mine:

```markdown
# ICP — Bootstrapped B2B SaaS Founders, 2-15 employees

## Firmographics
- Industry: B2B SaaS (vertical SaaS preferred)
- Company size: 2-15 employees
- Founded: 2020-2024
- Headquartered: US, Canada, UK, Australia, Netherlands
- Funding: bootstrapped or seed (<$2M raised)

## Role targets
- Founder / Co-founder
- CTO (only at <8 person companies)
- Head of Engineering (only at >8 person companies)
- Exclude: VPs, anyone with "Sales" or "Marketing" in title

## Signals (positive)
- Posted on LinkedIn about hiring engineers in last 30 days
- Company shipped a public changelog entry in last 14 days
- Founder has personal Twitter/X with >1k followers
- Tech stack includes: Next.js, Postgres, Vercel, Stripe

## Signals (negative — disqualify)
- Recently funded Series A+ (they have budget for big SaaS)
- Currently a customer of: Lemlist, Outreach, Salesloft
- Founder hasn't posted publicly in >90 days
- Company website is a Webflow template with no product screenshots

## Pain point hypothesis
They're at the stage where they're losing 4-6 hours a week to
ops work that should be automated. Their stack is fragmented.
They've heard about AI agents but think they need a full-time
hire to implement them.

## Offer fit
A 4-week sprint to install one specific AI workflow (sales,
support, or content) that saves >5 hours/week, with handoff
documentation so they can extend it themselves.
```

That's not a wall of text for the sake of it. Every line is load-bearing. Hermes uses the firmographics for the Apollo search. The signals shape the scoring rubric. The pain point hypothesis is what the Copywriter persona uses to write the first line that actually lands. The offer fit is what shows up in the CTA paragraph.

The two months part: I rewrote this file at least eight times in eight weeks. Every Friday I sat with the previous week's reply rate, the actual responses, and the rejections, and edited the ICP. The thing nobody tells you is that the ICP is your most important prompt, and prompts get better when you treat them like code — versioned, iterated, evidence-driven. The first version of this file was generic enough that Hermes pulled 50 leads I had no business contacting. The current version pulls 50 leads where I'd happily get on a call with all of them.

The boring lesson: AI pipelines reward focus. The narrower the ICP, the better every downstream stage performs. I'll come back to this when we hit the comparison section.

## Stage 2 — Calling the Apollo API from Hermes (and the pricing reality)

Apollo is the data layer. It's the part of the stack that's genuinely hard to replace because their contact database — 275M+ contacts according to their 2026 marketing, intent signals layered on top — is just better than what you can stitch together from LinkedIn scrapers and free sources. The question isn't *whether* to pay Apollo. It's *which tier* and *how to call the API without melting your credits*.

The Apollo pricing as of May 2026, verified on their site this week: Free at $0 with 10,000 email credits but **no API access at all**, Basic at $49/seat/mo (annual) with very limited API, Professional at $79/seat/mo (annual) with real API access, and Organization at $119/seat/mo (annual, three-seat minimum) with the advanced API features. Monthly billing adds roughly 20–25% across the board.

If you're a solo operator running outbound through your own infrastructure, **Professional at $79/mo is the floor.** Anything below it forces manual UI work, which defeats the entire point of building the pipeline. Anything above it — Organization at $119+ — only pays back if you need the advanced sequencing features, dialer, or three-seat collaboration, which most solo operators don't.

That single decision — Professional, not Basic, not Organization — saves you both the "I bought too little" pain and the "I'm paying for features I'll never use" guilt. Pick it and move on.

Now, calling the API. My Hermes skill is called `apollo-leads` and it exposes one function I care about: `people_search`. The cron job runs every Sunday at 22:00, pulls a fresh batch matching the ICP, and writes the raw response to `02-clients/raw/{{date}}-apollo-search.json`.

Here's the actual cron config in `~/.hermes/cron/apollo-sunday.yml`:

```yaml
schedule: "0 22 * * 0"
persona: Researcher
prompt: |
  Read the ICP file at 02-clients/icp/agency-saas-founders.md.
  Translate the firmographics into an Apollo people_search query.
  Cap the result at 100 contacts. Sort by intent_score descending.
  Save raw JSON to 02-clients/raw/{{date}}-apollo-search.json.
  Write a one-paragraph summary of what was pulled to today's daily note.
tools:
  - vault.read
  - vault.write
  - apollo.people_search
limits:
  max_credits_per_run: 200
```

That `max_credits_per_run: 200` line is the single most important guardrail in the whole config. Apollo's API will happily burn through credits searching the entire B2B universe if you ask it imprecise questions. The first time I ran this without a credit cap, a poorly-scoped query chewed through about 4,000 credits in a single night before I noticed. Cron + API + vague prompt is a wallet hazard. Tight cap, tight ICP, narrow person filters. Always.

Rate limits on the Apollo API are generous for Professional-tier users — you won't hit them with a weekly batch of 100 — but they're worth knowing if you ever consider going daily. I don't, for two reasons: data freshness on B2B contact records changes weekly at best, and the rest of the pipeline downstream is built to process a weekly batch, not a daily firehose.

Data quality reality check: Apollo's contact database is good, not perfect. Expect 70–85% email deliverability across a clean search, dropping into the 50s if you don't filter aggressively for active LinkedIn profiles and recently verified emails. The fix is downstream verification in the enrichment step, which we'll get to. Anyone telling you Apollo's data is 99% accurate is selling you Apollo. Anyone telling you it's garbage hasn't tried filtering it properly.

## Stage 3 — Enrichment, where Haiku 4.5 earns its keep

The raw Apollo dump is useful but not enough. To write an email that doesn't read like every other AI-generated cold email in the prospect's inbox, you need extra signal beyond the contact record. That's the enrichment stage.

Hermes's Researcher persona handles enrichment, but here's the routing trick: the enrichment calls run on **Haiku 4.5**, not Opus or Sonnet. Enrichment is high-volume, low-stakes-per-call, and the difference between Haiku and Opus for "pull the last three LinkedIn posts and extract any business-relevant signal" is negligible in quality and roughly 12x in cost. Routing this stage to the cheap model is the single biggest cost optimization in the whole pipeline. My monthly enrichment bill is around $4. On Opus, it would be closer to $50.

For each lead in the Sunday batch, the enrichment job adds:

- **Recent public signal.** Latest LinkedIn posts, recent blog posts on the company site, recent product releases or changelog entries.
- **Tech stack confirmation.** Cross-checked against BuiltWith or Wappalyzer-style scraping where ToS permits.
- **Recency check.** When was the contact last "active" on LinkedIn — proxy for whether they're still at the role Apollo claims.
- **Disqualifiers.** Anyone the negative signals in the ICP rule out gets a `status: disqualified` flag and skips the rest of the pipeline.

The enriched output lands as a Markdown record per lead in `02-clients/enriched/{{date}}/{{slug}}.md`. The format matters because the next stages — scoring and drafting — read these files directly. Here's an example record, with names changed:

```markdown
# Jordan Reeves — Founder, OrbitMetrics

- Role: Co-founder & CTO
- Company: OrbitMetrics (B2B usage analytics for SaaS)
- Size: 6 employees
- Founded: 2022
- HQ: Austin, TX
- Funding: bootstrapped, no public raise

## Recent signal
- LinkedIn post 5 days ago: hiring "first sales engineer"
- Blog post 12 days ago: "Why we ripped out PostHog after 18 months"
- Personal X account: 3.2k followers, posts weekly about
  founder-led sales and engineering culture

## Tech stack
- Next.js, Postgres on Neon, Vercel, Stripe, ClickHouse for analytics
- No CRM detected — they're using a Notion database

## Score
- intent: 8/10 (recent hire signal + active blog)
- fit: 9/10 (exact ICP match on size, stage, stack)
- channel readiness: 7/10 (active on LinkedIn + X, replies publicly)
- COMPOSITE: 8.0

## Disqualifiers
None.

## Hook angle
Recent PostHog rip-out blog post is a high-confidence opening.
He's clearly opinionated about ops tooling — lead with the
"AI workflow you can extend yourself, not another tool to rip out"
angle from the ICP offer fit.
```

That last section — the hook angle — is the one I underestimated when I first built this. It's a single paragraph the enrichment stage writes about *how* to open the email, not the email itself. It's the bridge between the cold data and the warm draft. When Claude Code writes the actual email in the next stage, it reads this hook angle first and the resulting draft is dramatically better than anything I'd get by handing the raw record to a copywriting prompt.

If you've read my piece on [why context beats configuration for AI agents](https://www.mejba.me/ai-agent-context-beats-configuration), this is the same pattern applied to sales: the LLM doesn't need more parameters, it needs better-shaped context. The hook angle is shaped context.

## Stage 4 — Scoring with a rubric I version like code

Most sales tools score leads with a black box. Apollo's intent score, Clay's enrichment scoring, Lemlist's engagement scores — you don't really know what's in them, and you can't easily change them. That's fine if the scoring matches your ICP. It rarely does.

My scoring rubric is a Markdown file at `02-clients/scoring/rubric-v4.md`. Version 4 because, like the ICP, I've rewritten it. The rubric is what the Researcher persona uses to assign the composite score you saw in the example record above. Three sub-scores, each on a 1–10 scale: **intent** (how likely are they buying something like my offer right now), **fit** (how well does this lead match the ICP), and **channel readiness** (how reachable are they via cold email).

The trick is that the rubric is *visible to the system prompt*. When the Researcher persona scores a lead, it reads the rubric file fresh every time. Which means if I edit the rubric on a Tuesday — say, adding "negative score if the company's latest blog post is older than 60 days" — the very next scoring run uses the new logic. No code deploy. No vendor change request. Just an edit to a Markdown file.

This is what people mean when they talk about [agent skills that compose into an advanced workflow](https://www.mejba.me/agent-skills-advanced-claude-code) — the rubric isn't code, it's a skill the agent reads and applies. The agent stays the same. The skill evolves. That's the right separation.

The output of stage 4 is a single ranked file: `02-clients/briefs/{{date}}-prospect-brief.md`. It contains the top ten enriched, scored leads, sorted by composite score, with the hook angle from the enrichment step preserved. That brief is what Monday morning starts with.

## Stage 5 — Drafting in Claude Code with the Copywriter system prompt

Now we're at the part that actually produces revenue, and the part that took me three full rebuilds to get right.

The instinct everyone has is to give the LLM a one-shot prompt: "Write a cold email for this lead." The output is the cold email you've already seen a thousand times. Generic opener, vague value prop, weak CTA, signed *Best, your name*. It's bad because the prompt is bad. The fix isn't a better one-shot prompt. The fix is to break the email into pieces and let the model write each one against different context.

Here's how I structure the Copywriter call inside Labyrinth (the Hermes persona that talks to Claude Code):

1. **Hook line.** Generated from the *hook angle* in the enriched record. The system prompt explicitly tells Claude Code: "The first sentence must reference something specific and verifiable from the recent signal section. If you cannot, return EMPTY and flag the lead as needs-manual."
2. **Bridge.** One sentence connecting the hook to the prospect's pain. Pulls from the ICP pain point hypothesis, but personalized using the recent signal.
3. **Offer.** Two sentences. Based on the offer fit in the ICP. Always concrete — a specific outcome, a specific timeline.
4. **CTA.** One sentence. Always asking for a 20-minute conversation, never a demo, never a "quick chat." The word "quick" is banned.
5. **Sign-off.** Generated from a sign-off rotation file so my emails don't all look identical.

The whole prompt I send Claude Code is roughly 1,200 tokens of system prompt plus the 600–900 tokens of the enriched record. The output is a draft email plus a confidence score Claude Code assigns to itself. Anything under 7/10 self-confidence gets routed back to me with a "this one needs manual editing" flag instead of going to Gmail draft.

A real example from last month, again with names changed. The Apollo record said *Jordan Reeves, Co-founder & CTO, OrbitMetrics, Austin TX*. The enrichment said *recent post about ripping out PostHog, hiring first sales engineer*. The Copywriter draft, verbatim:

> Subject: the OrbitMetrics post on PostHog
>
> Jordan — the PostHog rip-out post from a couple weeks back resonated, especially the bit about replacing it with something your team could actually extend.
>
> Most B2B SaaS founders I work with hit the same wall around 6–8 employees: the ops stack is a Frankenstein of SaaS tools, and the only obvious fix is "hire someone to manage it" — which is exactly the wrong move at your stage.
>
> I do a 4-week sprint that installs one AI workflow inside your existing setup (sales, support, or content ops, founder's pick), saves >5 hours a week, and ships with handoff docs so you can extend it yourself. No new SaaS subscription, no maintenance dependency on me.
>
> Worth a 20-minute conversation next week to see if there's fit?
>
> — Mejba

Jordan replied in eleven hours. We talked the following Tuesday. He didn't become a client — he'd just hired the sales engineer and was deferring all ops work until Q3 — but he forwarded the email to a friend who *did* become a client. That's one lead, one workflow run, one Tuesday call, one $6k engagement. The math on $79/mo of Apollo and maybe $30/mo of API spend writes itself.

The reason this email works isn't that it's brilliant prose. It's that every element is specific and earned. The hook references a real post. The pain point is calibrated to his exact company stage. The offer matches his stack philosophy (he hates SaaS bloat, the offer leans into "no new subscription"). The CTA is unambiguous and small. None of that comes from the model getting smarter. All of it comes from the *context* being right by the time the model writes.

If you've ever built a [multi-agent marketing team with Claude Code](https://www.mejba.me/build-ai-marketing-team-claude-code), this is the same architecture pointed at outbound: specialized agents working off shared context, with a human approving the final output.

## Stage 6 — Gmail drafts with the brakes on

This is the stage that most demos skip and most production deployments regret skipping.

Hermes can write to Gmail through the Zapium MCP gateway. I gave it exactly three Gmail capabilities: read inbox, **create draft**, search threads. There is no Send action wired up. There won't be. Every draft Claude Code produces lands in my Gmail Drafts folder, neatly subject-lined with the prospect's company name, ready for me to skim, edit, and send manually on Monday morning.

I want to defend the draft-only choice because it's the single most-debated configuration choice with anyone I've shown this stack to. The pushback is always the same: "Why have a pipeline if a human has to push send?" The answer is in three parts.

First, **sender reputation is your most important asset and the cheapest one to destroy.** A single bad week of auto-sent emails to wrong addresses or wrong personas can permanently nuke a sending domain's deliverability. The cost of one hour a week of human review is trivial compared to the cost of warming up a new domain for sixty days.

Second, **the compliance surface is real.** CAN-SPAM in the US requires accurate sender info and a working unsubscribe. GDPR in the EU requires legitimate interest documentation per outreach. Some US states (California, Colorado) have layered their own consumer-data rules on top. The human-in-the-loop checkpoint is the cleanest place to enforce any of these. A Send Email action wired to an autonomous agent is a regulatory incident waiting to happen.

Third — and this is the empirical one — **the draft review catches model failure modes that I wouldn't catch any other way.** About one draft in twelve, Claude Code does something subtly weird: hallucinates a detail about the company, gets the prospect's first name wrong, references a product feature that doesn't exist. Reviewing the drafts before send isn't optional, it's quality control. The day I stop catching one weird draft a week, I'll start trusting auto-send. We are not there yet.

The Monday morning ritual is short: open Gmail, sort by Drafts, skim each one, edit anything that looks off, send. Twenty to thirty minutes for a batch of ten. That ritual is the single best forty-minute investment in the business each week.

I covered the safety architecture in more depth in [why I treat AI agents as office workers, not factory robots](https://www.mejba.me/ai-assistants-agent-operators-organizations) — same principle applied here.

## The honest ROI: what this stack actually replaces

I cancelled four subscriptions to build this. Here's exactly what got replaced and what didn't.

**Replaced — fully:**

- **Lemlist ($97/mo).** Replaced by Claude Code drafting + Gmail draft folder + my own send-on-Monday ritual. Lost: Lemlist's deliverability infrastructure and warmup. Gained: dramatically better personalization and zero send-velocity risk because I'm not blasting.
- **ChatGPT Plus for sales personalization ($20/mo).** Replaced by Claude Code with the Copywriter system prompt. Same model family does both stages now.
- **Clay trial ($149/mo I almost subscribed to).** Replaced by the enrichment stage in Hermes running Haiku 4.5. Lost: Clay's incredible waterfall enrichment with 100+ data sources. Gained: a workflow tight enough that the enrichment I do have is enough.
- **Notion CRM template ($16/mo for the team plan).** Replaced by the Obsidian vault Hermes already writes to. Same data, one fewer login.

**Not replaced — kept:**

- **Apollo Professional ($79/mo).** Cannot be replaced. The contact database is the core. Any DIY scraper is either slower, lower quality, or in ToS-violation territory.
- **A dedicated sending domain + warmup tool (~$25/mo).** This isn't part of Apollo or Hermes; it's deliverability infrastructure. You still need it.

So the real before-and-after: **about $407/mo down to about $104/mo**, with ~$30/mo of API spend layered on top from the Hermes + Claude Code calls. Call it $135/mo total. Annualized savings around $3,260.

The savings aren't the point, though. The point is what the stack gives me that the SaaS version didn't: **owned workflow**. When I want to change how leads get scored, I edit a Markdown file. When I want to add a signal source, I add it to the enrichment skill. When the next model release comes out, I route a persona to it and the pipeline gets better with no vendor request, no roadmap waiting list. That ownership is the asset, not the savings.

**Who shouldn't bother:**

- If you don't write code, the eight-step Hermes + Claude Code install I covered in the [full stack setup post](https://www.mejba.me/hermes-claude-code-ai-operating-system) is too sharp an edge. Pay for the SaaS stack and check back when this gets packaged.
- If you're sending more than 500 cold emails a week, the draft-only constraint becomes operationally infeasible. You probably need Smartlead or Instantly for the sending infrastructure layer, possibly stacked on top of Apollo + Clay. Different scale, different problem.
- If you're at a company with compliance, legal, or InfoSec gatekeepers, an open-source agent running your outbound is going to be a months-long approval process. Not worth fighting that battle.

**Who absolutely should:**

- Solo operators or two-person teams running outbound for their own services agency or consulting practice.
- Anyone whose ICP is narrow enough that they can hand-write the ICP file from real customer interviews.
- Anyone who's already on the Hermes + Claude Code stack for other workflows and is paying for SaaS sales tools alongside it. You're paying twice. Stop.

## Where DIY loses against Clay, Smartlead, Instantly, and Apollo's own all-in-one

I want to be specific about where this stack is *worse* than the alternatives, because pretending otherwise is the kind of thing that costs readers money.

**Clay is better for waterfall enrichment.** If your business depends on pulling data from 20+ sources and reconciling it into one record per contact — what Clay calls waterfall enrichment — Clay's 100+ data integrations and visual workflow builder are genuinely best-in-class as of mid-2026. Replicating that in Hermes is possible but tedious. If enrichment depth is your bottleneck, pay Clay. The 2026 pricing puts the entry tier at roughly $134/mo and the serious tier closer to $349/mo, which is the math you'd weigh against the API costs of doing it yourself.

**Smartlead and Instantly are better for high-volume sending.** Both tools nailed the deliverability infrastructure problem — mailbox rotation, warmup, inbox placement monitoring — in a way that's borderline impossible to DIY. If you're sending more than a few hundred emails a week, you need their infrastructure. Smartlead's entry plan is ~$39/mo and Instantly's Hypergrowth runs ~$97/mo. Building cold-email infrastructure from scratch in 2026 is not where you want to spend your engineering time.

**Apollo's own Outbound Copilot is better for non-technical operators.** Apollo has been investing hard in their AI-powered outbound features through 2025–2026. Their Copilot finds ICP-matching prospects, adds them to sequences, and writes messaging using your "AI Content Center" context. For a non-technical founder who wants a sales pipeline up in a weekend, paying Apollo Organization at $119+/mo and using their built-in AI is the right answer. The DIY stack pays back when you want *control over the prompts and routing*, not when you just want a working pipeline.

So where does the Hermes + Apollo + Claude Code stack actually win?

- **You already have the AI OS.** If Hermes is already running, the marginal cost of adding the sales workflow is small. If it isn't, the install cost is the whole investment.
- **Personalization that survives 2026 spam filters.** The Copywriter persona with the hook-angle enrichment routinely produces emails better than what I got from Lemlist + ChatGPT, because the *context* is better-shaped.
- **No vendor lock-in on prompts or workflow.** Every prompt is a Markdown file you own. Every score is a rubric you wrote. The day a competitor SaaS ships a feature you want, you can copy it into your stack in an afternoon.
- **Cost compounds in your favor as you grow.** SaaS stacks scale linearly with users and contacts. API spend on your own workflow scales sub-linearly because better prompts mean fewer retries, better ICPs mean less enrichment waste.

If you're somewhere in the middle — too technical to want SaaS bloat, not yet building enough volume to need Smartlead — the DIY stack is the sweet spot. That's where I sit. That's where most of the operators I show this to sit.

## What I'm building next, and the thing I'm deliberately not building

Two things on the next-quarter roadmap.

The first is a **reply-handling pipeline.** Right now, when a prospect replies, the reply lands in my normal Gmail and I handle it manually. The natural next stage is to route replies into Hermes, classify them (interested / not interested / referral / wrong person), and produce a suggested follow-up draft. Same draft-only safety pattern, same human review on send. I've been deliberately slow on this because reply handling is where most "AI SDR" tools have failed publicly — auto-responses to misclassified replies are how you torch a brand. So I'll build it, but I'll over-index on the classification confidence threshold before any draft gets written.

The second is a **lead-source diversification layer.** Apollo is great, but it's a single point of failure. If their data quality degrades, or their pricing jumps, or they get acquired by someone whose incentives don't match mine, I want to be one config flag away from a Hermes skill that pulls from a different source. So the next sub-skill on the list is a generic `contact_source` interface where Apollo is one implementation and at least one alternative (likely a LinkedIn-Sales-Navigator-derived source) is the second.

What I'm not building, and don't think I should:

**A fully autonomous sequencer with auto-send.** Several people have asked me for this. The cost-benefit doesn't work. The marginal upside of auto-send is twenty to thirty minutes a week saved. The marginal downside is one bad week of misfires permanently degrading sender reputation. I do not need to win that trade. Neither do you.

**A dashboard.** Several people have asked for this too. I have a Markdown brief on Monday morning. I have a Telegram message on Tuesday morning if anything weird happened over the weekend. That's enough. Building a dashboard would be exactly the kind of tool-building-for-its-own-sake that I'm trying to avoid by leaving Notion and Lemlist behind.

If you're already running the Hermes + Claude Code stack for something else — content, research, engineering work — and you've been paying for sales tools alongside it, here's the one specific thing I'd do this week. Write the ICP file. That's it. Not the install, not the cron, not the API integration. Just sit down with your last ten customers, the ones you'd take ten more of, and write a single Markdown document that captures who they are, what signals they show before they buy, and what offer matches them. Forty-five minutes. Even if you never build the rest of the pipeline, that file is the most valuable document in your business.

The reason the rest of this stack works is that file. Everything downstream — the Apollo scrape, the enrichment, the scoring, the copy — is a function of how sharp the ICP is. Tools don't fix a fuzzy ICP. AI doesn't fix a fuzzy ICP. The hour you spend writing the ICP returns more than any subscription you'll cancel.

The cancellation email to Lemlist was the small victory. The ICP file is the one I'd put on my desk in a frame.

## Frequently Asked Questions

### How much does an AI outbound sales pipeline cost to run per month?
The realistic monthly cost for a solo operator on this DIY stack is around $135/month all-in — Apollo Professional at $79/mo, sending infrastructure at ~$25/mo, and API spend across Hermes and Claude Code at roughly $30/mo. That's down from $400+/month for a Lemlist + ChatGPT + Apollo + Clay stack. The setup cost is the time investment, not the cash.

### Can I run this without Claude Code?
Yes, but the email drafting quality drops noticeably. Hermes alone can write the drafts using its Copywriter persona, but Claude Code's tighter coupling with the enriched lead record on disk produces more grounded, specific output. If you're not using Claude Code, route the Copywriter persona to a strong model like Opus 4.7 and accept that the workflow is slightly less integrated.

### Is cold email still working in 2026?
Cold email works when personalization is genuinely deep and ICPs are genuinely narrow. The bar has risen sharply since 2024. Reply rates of 5–10% on focused lists of 50–300 highly qualified prospects are typical of well-tuned pipelines in 2026. Generic blasts with one variable token in the first line are now spam-filtered at scale.

### How do I stay GDPR and CAN-SPAM compliant with an AI outbound pipeline?
The draft-only Gmail configuration is the foundation — no message goes out without a human approving it, which is where you enforce sender info, unsubscribe links, and legitimate-interest documentation. Apollo's contact records include opt-out status flags; honor them at the enrichment stage. For EU contacts, document the legitimate-interest basis per ICP in your scoring rubric. Manual send is the cleanest compliance posture available.

### Should I use Clay instead of building the enrichment myself in Hermes?
If waterfall enrichment across 20+ data sources is your bottleneck, Clay is best-in-class as of mid-2026 and worth its ~$134–$349/month entry pricing. If you can get enough signal from Apollo plus 3–4 targeted sources (LinkedIn posts, company blog, tech stack detection), the Hermes enrichment stage routed to Haiku 4.5 will get you 80% of Clay's value at roughly 10% of the cost.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
