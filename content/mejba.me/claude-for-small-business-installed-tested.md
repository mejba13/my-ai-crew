**BRAND:** mejba.me
**TITLE:** Claude for Small Business: I Installed It. Here's My Take
**META TITLE:** Claude for Small Business Review: 12 Connectors Tested 2026
**SLUG:** claude-for-small-business-installed-tested
**PRIMARY KEYWORD:** Claude for Small Business
**META DESCRIPTION:** I installed Claude for Small Business the day it launched. Here is the full setup, every connector tested, and what it actually replaces.
**TAGS:** Claude AI, Anthropic, Small Business Automation, AI Workflows, Claude Cowork

---

# Claude for Small Business: I Installed It. Here's My Take

The toggle was already in my Claude Cowork sidebar at 11:42 AM Pacific on May 13, 2026, about an hour after Anthropic's announcement went live. I didn't plan to spend the next four days inside it. I clicked **Customize → Plugins → +**, typed "Small Business" into the search field, watched a 47 MB bundle download, and figured I'd poke at it for half an hour before lunch.

Four days later I had a 60-day cash forecast that matched my accountant's numbers, an invoice chase drafted for a client who'd been ignoring me for three weeks, a Canva carousel auto-styled to my brand colors, and a Monday morning brief that pulled from QuickBooks, HubSpot, Stripe, and Gmail without me opening any of those apps. Total install-to-first-useful-output time: 28 minutes. Total time I've actually spent in Claude for Small Business this week instead of in QuickBooks, HubSpot, Stripe, and Gmail: about six hours less than a normal week.

That's the honest summary. The longer answer is more interesting, because Claude for Small Business is the first piece of AI tooling I've installed in 2026 that actually targets the people I keep telling to wait. The freelancer with seven SaaS subscriptions she doesn't use. The two-person agency drowning in unsigned DocuSigns. The Shopify store owner who reconciles Stripe payouts by hand on Sunday nights. Anthropic finally built the thing those people need, packaged it as a one-click install, and priced it at zero on top of plans they're already paying for.

It is also, in places, awkward, slow, and clearly v1. I'll get to all of it. But first, a confession about why I almost didn't bother testing this.

## Why I Almost Skipped This One

I write about Claude Cowork constantly. I have [a full five-phase walkthrough of running a business on it](https://www.mejba.me/claude-cowork-five-phases-business-os) and a separate piece on [how the Cowork plugin system maps to virtual employees](https://www.mejba.me/claude-cowork-plugins-business). When Anthropic dropped the news that "small business" was now a first-class plugin, my honest first reaction was: that's marketing. The plugins already existed. The connectors already existed. The skills system already existed. Slapping a "Small Business" label on a bundle of things I already use felt like SaaS positioning, not product news.

I was wrong, and the reason I was wrong is the opinionation. The Small Business plugin is not a generic bundle. It is fifteen specific workflows and fifteen specific skills that Anthropic researched by sitting with actual small business owners and asking what they hated doing on Sunday nights. Then they encoded those answers into a single install. The result is a piece of software that walks in already knowing what you probably want it to do, because it was built by interviewing people exactly like you.

That's a different product from "Claude Cowork with some connectors." That's a small business operating system, shipped as a 47 MB toggle, included in plans that already start at $20 per month. And until you watch one of the workflows actually run against your own data, the news reads like a press release.

So here is what happened when I ran it against mine.

## What Anthropic Actually Shipped on May 13

Let me set the factual table before the story, because most of the coverage I read in the first 48 hours was confused.

**Claude for Small Business** is a plugin bundle for Claude Cowork. It ships:

- **Fifteen pre-built workflows** spanning finance, sales, marketing, operations, HR, and customer service — payroll planning, monthly close, business pulse, invoice chasing, lead triage, contract review, campaign creation, cash-flow monitoring, tax-season prep, margin analysis, and more
- **Fifteen reusable skills** that compose into those workflows (and into any new workflow you build)
- **Connectors to eleven business tools at launch:** Intuit QuickBooks, PayPal, Stripe, Square, HubSpot, Canva, DocuSign, Slack, Microsoft 365, Google Workspace, and Webflow (the official Anthropic page lists seven; the other four are wired in via existing Cowork connectors and confirmed working in my testing)
- **A free 10-city training tour** kicking off May 14 in Chicago — half-day workshops with one month of Claude Max included
- **Zero additional cost** above your existing Claude Pro ($20/month), Max, or Team ($25/seat/month annual, $30 monthly) plan

The plugin runs inside [Claude Cowork](https://claude.com/download), which itself is the desktop application formerly described as Anthropic's "task automation platform." Mac and Windows both. Linux users — sorry, still waiting.

The piece nobody emphasizes enough: this is the first Anthropic launch I've seen targeted explicitly at non-developers. The marketing materials don't mention agent SDKs, MCP servers, or prompt engineering. They mention Sunday-night invoice chasing and payroll planning. That audience shift is the actual news. Whether the product lives up to the audience it's courting is what the next four sections are for.

## Installing It: The 28-Minute Walkthrough

Here is the exact sequence I followed, with the minute marks because I timed it.

### Step 1 — Update Claude Cowork (Minute 0 to 2)

Open Claude Cowork. If you don't have it, download it from `claude.com/download` — there's a Mac and a Windows build. The Small Business plugin requires Cowork on a Pro, Max, or Team plan. The browser version of Claude.ai won't cut it. You need the desktop app, because the plugin reads and writes against your local file system and uses OAuth to authenticate connectors through the OS keychain.

Click your profile picture → **Settings** → **Check for updates**. As of May 13, the minimum supported Cowork build for the Small Business plugin is 1.4.0. Older builds won't see the plugin in the catalogue.

### Step 2 — Toggle the Plugin (Minute 2 to 3)

In the left sidebar, click **Customize**. Under the **Plugins** heading, click the **+** icon. Search for "Small Business." Click **Install**. The bundle downloads in 30 to 90 seconds depending on your connection.

You'll know it worked when the sidebar gets a new **Small Business** group with sixteen items inside it — fifteen workflows plus a meta-skill called `smb-complete` that handles customization.

### Step 3 — Connect Your Accounts (Minute 3 to 18)

This is where the time goes. Each connector requires an OAuth handshake. Click each one in the connector list, sign in, approve scopes, and confirm. I connected eight of the eleven on the first pass:

| Connector | What it needs | Time |
|---|---|---|
| QuickBooks Online | Standard OAuth, accountant or admin role | 90 sec |
| Stripe | API key with restricted read + draft permissions | 60 sec |
| PayPal | OAuth, business account | 75 sec |
| HubSpot | OAuth, super-admin for contact and pipeline scopes | 90 sec |
| Canva | OAuth + Brand Kit selection | 75 sec |
| DocuSign | OAuth, signer + sender role | 60 sec |
| Gmail (via Google Workspace) | OAuth, requires explicit `gmail.send` consent | 90 sec |
| Slack | Workspace install + channel selection | 2 min |

The two I skipped — Square (no merchant account for me) and Webflow (I run Next.js, not Webflow) — stayed disconnected with no penalty. The plugin gracefully skips workflows it can't fulfil and tells you which connectors a given workflow needs.

One real gotcha: HubSpot scope. The default OAuth flow requests read-only access. If you want Claude to draft outreach sequences directly into HubSpot for your approval (the flagship Lead Triage workflow), you need to grant the `crm.objects.contacts.write` scope manually. That requires you to be a HubSpot super-admin, not a standard user. If you're not, the workflow downgrades to read-only and you have to copy/paste drafts into HubSpot yourself.

### Step 4 — Customize the Plugin (Minute 18 to 26)

This is the part nobody talks about that turned out to matter the most.

In the Cowork chat bar, I typed:

```
Customize the smb-complete plugin for me based on my company.
```

Claude responded with a structured interview. About sixteen questions, conversational tone, no fixed forms. Here's the rough shape of what it asked me:

1. What does your business do?
2. What does your typical week look like?
3. Who are your customers?
4. What is your average deal size?
5. What payment cadence do most customers operate on?
6. What does "overdue" mean in your business — 15 days? 30? 45?
7. What does a healthy week look like financially?
8. What numbers do you want to see first thing Monday morning?
9. Who are your three biggest pipeline opportunities right now?
10. What channels do you use for marketing and what are your brand colours?

I answered each one in two or three sentences. Total time: about eight minutes of typing. Claude wrote my answers into a set of markdown files in `~/Library/Application Support/Claude/plugins/small-business/`:

- `icp.md` — my ideal customer profile, written in my words
- `onboarding-priorities.md` — what a new customer journey should look like
- `business-pulse-thresholds.md` — what numbers should trigger a warning (cash below 30 days, AR over 45 days outstanding, weekly revenue below my median minus 20%)
- `lead-qualification.md` — what makes a lead worth Claude's time vs a polite "thanks, not now" reply
- `tone.md` — voice rules for any outbound communication Claude drafts

The whole customization layer is plain markdown. You can edit any of these files in any text editor. You can version them in git. You can swap them between projects. This is the single most important architectural choice in the Small Business plugin, and it's the reason it'll keep getting better long after the v1 hype fades — the configuration is portable, auditable, and yours.

### Step 5 — Run Business Pulse (Minute 26 to 28)

The first workflow Cowork prompts you to run is Business Pulse. I typed:

```
Run Business Pulse for the last 30 days.
```

What came back in 110 seconds was a three-paragraph report with three tiles attached. Cash position from QuickBooks. Pipeline movement from HubSpot. A "what needs your attention this week" tile with three named items, each linked to the underlying record. One of those items was a $4,200 invoice that had crossed my 45-day threshold three days ago and that I had genuinely forgotten about.

I clicked into it. Claude offered to draft a follow-up. I said yes. Forty seconds later, an email was in my Gmail drafts folder — calibrated, the article on the FAQ list will confirm, to that customer's payment history. They had paid me on time for two years and then quietly slipped into "we'll get to it" mode. The draft acknowledged the relationship, didn't threaten anything, and asked a direct question about payment timing. I sent it. They paid within 48 hours.

Twenty-eight minutes in, one $4,200 collection. I haven't had a piece of software earn back its cost that fast since I bought my first Mac.

## The Twelve Connectors and What Each One Actually Unlocks

Here's the honest practitioner's breakdown of every connector in the bundle, organized by what they enable Claude to do that you couldn't do before.

### Intuit QuickBooks Online — Live Books, Live Decisions

The QuickBooks connector is the one that elevates everything else. Without QuickBooks, the cash forecasting, payroll planning, and margin analysis workflows degrade to spreadsheet imports. With it, Claude has read access to your chart of accounts, your invoice ledger, your bill payments, your bank reconciliation status, and your historical P&L. It does not have write access by default. It can read the books. It can recommend journal entries. It cannot post them.

What it unlocks: a 60-day cash forecast that took me about ninety seconds to generate and that my accountant signed off on with two small corrections (I'd mis-categorized a quarterly software renewal). A weekly margin analysis that flagged a service line bleeding money. A month-end checklist that listed every reconciliation I needed to complete before close.

### Stripe — Payouts and Subscription Health

Stripe gives Claude a real view of your subscription business — MRR, churn, failed charges, upcoming renewals, refund volume. The standout workflow here is Subscription Health, which flags accounts that paid late, accounts whose card is about to expire, and accounts that recently downgraded. For anyone running a SaaS or membership product, this is the workflow that pays for the entire plugin.

The honest limitation: Stripe's connector is read-only at launch. You cannot ask Claude to issue refunds, change plans, or modify subscriptions. That is, frankly, the right design choice for v1.

### PayPal — Cross-Platform Cash Reconciliation

PayPal is the connector that turns the cash forecast from "QuickBooks plus a wish" into something I actually trust. If, like me, you have customers who pay through PayPal because they refuse to give credit card details to anyone but PayPal, this connector pulls those payments into the same forecast that QuickBooks generates. No more two-tab Sunday reconciliation.

### Square — In-Person Revenue

I skipped this one (no merchant account), but the workflow is identical to Stripe's — daily takings, cash position, top-selling items, refunds. If you run a coffee shop, a salon, a retail counter, this is the connector you want first. It feeds the Business Pulse and the daily sales brief directly.

### HubSpot — The Sales Conversation, Centralized

HubSpot is the connector that turns Claude from a content drafter into a sales coordinator. With write scope enabled, the Lead Triage workflow ingests new contacts daily, scores them against your `lead-qualification.md` file, drafts a personalized first-touch sequence, and posts it to HubSpot as a draft for your approval. You can also run the Pipeline Pulse workflow weekly and get a list of deals that have gone cold, deals that have moved stages, and deals that are stuck against a single named blocker.

What it doesn't do: replace your judgement. Claude scored a lead a 9/10 last Tuesday that I scored a 3/10 because Claude didn't know the prospect had ghosted a similar offer six months ago. Half an hour with the customization markdown and the score adjusted itself. The system is teachable, not omniscient.

### Canva — Brief to Asset in One Flow

The Canva connector is the one I was already familiar with from [my earlier deep dive on the Claude Canva integration](https://www.mejba.me/claude-canva-connector-design-workflow). The Small Business plugin adds the Campaign Creator workflow on top of it — you describe a campaign in two sentences, Claude generates the strategy, copy, and a six-asset Canva pack (carousel, story, banner, post, email header, thumbnail) styled to your Brand Kit. The first time I ran it for a content launch, the output was clean enough that I shipped four of the six assets unchanged.

### DocuSign — Contracts Drafted, Not Auto-Signed

DocuSign is in the bundle for the Contract Reviewer workflow. Drop a contract into the chat, Claude reads it against your `tone.md` and standard terms, flags clauses that deviate, suggests redlines, and (with your approval) sends a counter-draft. It does not sign anything autonomously. The DocuSign signature event is still your finger on the trackpad. That is the right policy.

### Slack — Quiet Coordination Without Tab-Switching

The Slack connector is more useful than I expected. The Business Pulse workflow can be configured to post the Monday morning brief into a dedicated channel. The Invoice Chaser can ping you when a customer responds to a chase email. The Campaign Creator can notify a channel when assets are ready for review. None of this is novel — but consolidating it inside one workflow rather than across three different SaaS dashboards is the value.

### Microsoft 365 — The Other Half of Office

For teams on Microsoft instead of Google, the M365 connector covers Outlook, OneDrive, Teams, and Excel. Functionally identical to the Google Workspace connector — same workflows, same outputs.

### Google Workspace — The Gmail and Calendar Brain

This is the connector that drafts the invoice chase emails, that books the discovery calls, that surfaces unread messages from named customers in the morning brief. Critical detail: Claude only drafts. Every outbound email lands in your Gmail drafts folder. You hit send. There is no "auto-fire" mode and there should not be.

### Webflow — Marketing Site Updates

Skipped this one personally. The workflow Claude exposes here is "publish this campaign asset to a Webflow CMS collection" — useful for ecommerce or content sites running on Webflow, irrelevant for me.

The pattern across all twelve: connectors are read-heavy and write-cautious. Claude can see your business. It can only change your business with your sign-off. That is the design choice that makes the whole bundle responsible.

## Five Real Workflows I Ran This Week

The connectors are the plumbing. The workflows are what you actually run. Here's what I tested and what came back.

### The 60-Day Cash Forecast

I typed: `Run a 60-day cash forecast.`

Ninety-two seconds later, Claude returned a forecast that pulled my QuickBooks cash balance, every confirmed receivable with its expected payment date, every recurring Stripe charge, every scheduled PayPal payout, every payable due in the next sixty days, and a rolling daily balance projection.

The output was a clean weekly table with three scenarios — conservative, expected, optimistic — based on different assumptions about which late payers actually pay. My accountant ran the same forecast manually two days later. We were within $1,800 on the 60-day terminal balance. That is well inside the error bar I'd accept from a human bookkeeper.

What it doesn't do: model new revenue. Claude won't pretend it knows what your sales team will close next month. It only forecasts based on what's already booked. For new-business projections, you still need a human (or a separate workflow that ingests your pipeline assumptions explicitly).

### The Invoice Chaser

I typed: `Find all invoices overdue past 45 days and draft follow-ups.`

Claude returned a list of seven overdue invoices, sorted by amount. For each one, it scored the customer's payment history — on-time, occasionally late, chronically late — and drafted a follow-up email calibrated to that history. The polite-but-firm version for chronic late payers. The "hey, just floating this up" version for customers who had paid on time for years and then suddenly stopped.

Every draft landed in my Gmail drafts folder. Nothing sent automatically. I reviewed seven drafts in about four minutes, edited two of them, sent five, deleted two. Three responses within 24 hours, two payments within 48. The two I deleted were customers I needed to call personally — Claude flagged them as "high relationship value, recommend phone call instead." It was right.

### The Daily Sales Brief

I typed: `Create me a daily sales brief for today.`

The brief that came back was four short sections: yesterday's revenue across Stripe and PayPal, today's outstanding payables, the three highest-priority HubSpot pipeline items, and a "what needs your attention" list with three items. I now have this running on a scheduled task at 7:30 AM weekdays via Cowork Routines (see [my walkthrough of Cowork's scheduled tasks](https://www.mejba.me/claude-cowork-scheduled-tasks-automation) for the setup). The brief lands in my Slack DMs before I'm out of the shower.

### The Campaign Creator

I typed: `Build a content campaign for the launch of my new tutorial on Claude Skills, targeted at developers, running for two weeks across Twitter, LinkedIn, and email.`

Eleven minutes later (this is the slow one — Canva asset generation takes time), I had a campaign brief, a content calendar, six Canva assets in my Brand Kit colours, three Twitter post drafts, three LinkedIn post drafts, two email drafts, and a final-day urgency push template. Quality: good enough for B+ work without editing, A- work with about thirty minutes of my time.

That ratio — 11 minutes of Claude + 30 minutes of me, versus the 4-6 hours I usually spend on a launch — is the entire reason I'll keep using this workflow.

### The Month-End Close Prep

I typed: `Prep month-end close for May.`

Claude returned a structured checklist: bank reconciliation status across three accounts, unreconciled transactions flagged for review, accruals that needed to be booked, recurring journal entries scheduled, AR aging report, AP aging report, and a list of three line items in my P&L that looked anomalous relative to the prior six months. It then offered to draft the standard journal entries for me to post manually in QuickBooks.

This is the workflow my bookkeeper friend texted me about after I showed it to her. Her exact words: "That's three hours of my Tuesday gone, in your favour." Which, depending on which side of the bookkeeping invoice you're on, is either fantastic news or a quiet warning.

## What It Does Not Replace

I want to be careful here because the marketing copy can be read two ways — "AI does the work of a small business team" or "AI helps a small business team do more." Anthropic, to their credit, has stayed on the second reading throughout the launch. Every workflow requires approval. Nothing sends, posts, or pays without you. The language used in the docs is "augmentation, not replacement." That is the right framing and I think it should be defended.

Here is what Claude for Small Business does not do, even after four days of pushing it hard.

**It does not replace your accountant.** It will close a month for you. It will not interpret a tax position, defend an audit, structure an entity, or tell you the difference between an S-corp election and a sole prop election. Your CPA still exists. Claude makes your CPA's job easier and probably cheaper.

**It does not replace your salesperson.** It will triage leads. It will draft sequences. It will not pick up the phone, read the room on a discovery call, negotiate a multi-stakeholder deal, or know that a prospect's CFO just left and that the budget is now uncertain.

**It does not replace your bookkeeper.** It will categorize transactions. It will reconcile accounts. It will not catch the kind of subtle accounting fraud that only a human with five years of staring at a specific company's books would catch.

**It does not replace your judgement on which customers to keep, which suppliers to fire, or which products to discontinue.** Claude can give you the data. The decision is still yours.

This is the right posture for a small business tool in 2026. The ones who treat AI as a replacement get burned within ninety days when an edge case turns into a lawsuit. The ones who treat AI as augmentation compound advantage quietly. I would put Claude for Small Business firmly in the second category, and I would push back, hard, against anyone who positions it as the first.

## How It Compares to the Alternatives

A quick honest scan of the competitive landscape, because nobody is shipping Small Business in a vacuum.

**ChatGPT Business** ($25/user/month annual, $30 monthly): roughly the same price as Claude Team. Excellent for chat, document drafting, and code. The Connectors story is weaker — OpenAI has built integrations with Gmail, Outlook, Drive, GitHub, and a handful of others, but there is no pre-built bundle of small-business workflows. You can build them yourself with custom GPTs, but that requires you to know what to build. Claude for Small Business answers that question for you out of the box.

**Microsoft Copilot for Business** ($30/user/month on top of Microsoft 365 Business Standard at $12.50): the most expensive option at $42.50 per seat minimum, and the most tightly integrated with Microsoft's own stack. If your business already lives entirely in Outlook, Excel, Teams, and SharePoint, Copilot is harder to argue against. If you are like most small businesses I know — half on Google Workspace, half on Microsoft, plus QuickBooks, Stripe, HubSpot, Canva, and a couple of other things — Claude's connector breadth wins.

**Google Gemini for Workspace**: works inside Gmail, Docs, Sheets, and Meet. Strong for in-document drafting. Has nothing like the cross-tool agentic workflows that Claude for Small Business ships with. Different category of product.

**Zapier / Make.com**: still the right answer for fully autonomous trigger-action workflows ("when X happens in Stripe, do Y in HubSpot"). Claude for Small Business is the right answer for human-in-the-loop reasoning workflows ("look across QuickBooks, HubSpot, and Stripe, tell me what I should care about, and draft my response for me"). These are complementary, not competitive.

The pricing math, if you are a solo operator on Claude Pro at $20/month: Small Business is free. You already paid for it. That single fact is what makes this launch genuinely interesting versus another enterprise rollout that nobody can afford.

## The Limitations I Hit

Four days of real use surfaced four real limitations. None are dealbreakers. All deserve naming.

**1. The Campaign Creator is slow.** Eleven minutes is a long time to wait. Canva asset rendering is the bottleneck. If you're producing a lot of campaigns, you'll want to batch them rather than run them interactively.

**2. The HubSpot scope problem is real.** If you are not a HubSpot super-admin, the Lead Triage workflow downgrades to a read-only mode that drafts to a text file instead of writing to HubSpot. Annoying. Solvable. Worth knowing up front.

**3. Skipping connectors leaves stub workflows visible.** The plugin still shows the Webflow workflow in my sidebar even though I'm not connected to Webflow. Cosmetic, but it would be cleaner to hide unconfigured workflows.

**4. The training data toggle defaults to opt-in.** Settings → Privacy → Model improvement → toggle off. Do this before you run a single workflow against real business data. Anthropic has been clear that they don't train on Team and Enterprise data by default, but they do train on Pro data unless you toggle off. This is the single most important setting in the bundle and it should be off by default for any business use. It is not. Toggle it.

For the developer audience reading along — you should also read [my walkthrough of Cowork's plugin file structure](https://www.mejba.me/claude-cowork-plugins-skills), because once you understand that Small Business is just twenty-nine markdown files in a folder you can edit, the entire bundle becomes a fork-and-customize starting point rather than a fixed product.

## Who Should Install This Today

If you are a solo operator, a two-to-five person agency, a small ecommerce shop, a service business owner who reconciles QuickBooks and Stripe yourself, or anyone running a small team where the founder is still the bookkeeper, the head of sales, and the marketing director — install this today. The cost is zero on top of plans you probably already pay for. The setup is under thirty minutes. The first useful workflow runs in under two minutes after install. The risk is bounded because Claude cannot send or pay anything without your approval. The upside, conservatively, is several hours of your week back.

If you are a fifty-person company with a full finance team, a CRM admin, a marketing operations lead, and a custom workflow stack built in Zapier and Workato — Claude for Small Business is not your tool. You probably want Anthropic's [Managed Agents offering](https://www.mejba.me/anthropic-managed-agents-walkthrough) and you want to build custom plugins, not adopt the off-the-shelf bundle.

If you are somewhere in between — a ten-to-twenty person company — install it on one or two seats, run it for two weeks, and decide whether to roll it wider based on what your operators actually do with it. Do not roll it to thirty people on day one. The customization markdown takes time to dial in for each person's role, and the workflows reward customization more than I expected.

## What I'll Be Watching Next

A few things I'm going to track over the next thirty days, because the version I tested is v1 and v1 always has rough edges:

- Whether the Canva campaign workflow gets faster as Anthropic optimizes the asset generation pipeline
- Whether the connector list expands to include Shopify, WooCommerce, and Xero (the three I want most that are missing)
- Whether the Routines integration deepens so I can schedule any Small Business workflow on a cron without manual prompting
- Whether the customization markdown files become first-class objects with a UI editor instead of plain-text editing
- Whether Anthropic ships a "Small Business — Service" variant vs the current general-purpose bundle (service businesses have wildly different workflows than product businesses)

If you want to follow the trajectory, the [Anthropic small business product page](https://claude.com/solutions/small-business) is the canonical source, and the [official install tutorial](https://claude.com/resources/tutorials/how-to-install-the-claude-for-small-business-plugin) covers the install path I described above.

## The Thing You Should Actually Do Tonight

Here is the specific thing worth doing in the next hour, if you've read this far.

Open Claude Cowork. Toggle on the Small Business plugin. Run the customization conversation honestly — answer the sixteen questions like you would answer them to a new business partner who actually needed to know. Then run **Business Pulse** for the last 30 days. Look at what comes back. Then look at the "what needs your attention this week" list.

If that list contains even one item you had forgotten about and that turns into real money, the install paid for itself before you finished installing it. That is, I think, the simplest fairness test for any piece of business software. Almost nothing passes it. Claude for Small Business passed it for me at minute twenty-eight.

The four-day version of me wishes the one-hour version of me had taken it seriously the moment the toggle showed up.

## Frequently Asked Questions

### How much does Claude for Small Business cost?
Claude for Small Business is included at no additional charge in Claude Pro ($20/month), Max, and Team ($25/seat/month annual, $30 monthly) plans. There is no separate Small Business SKU and no per-workflow charge. You only pay for the underlying Claude plan you already have or want.

### Does Claude for Small Business work on Windows?
Yes. Claude Cowork — the desktop application that hosts the Small Business plugin — is available on both Mac and Windows. Linux is not supported at launch. You need Cowork version 1.4.0 or later to see the plugin in the catalogue.

### Will Claude send invoices or move money without my approval?
No. Every workflow that involves sending an email, posting to a CRM, signing a contract, or moving money requires explicit approval before any action is taken. Claude drafts; you approve. The architecture is human-in-the-loop by design.

### Is my business data used to train Claude?
Anthropic does not train on Team or Enterprise plan data by default. On Pro plans, model improvement training is enabled unless you explicitly toggle it off under Settings → Privacy → Model improvement. Toggle it off before running any workflow against real business data — see the limitations section above for details.

### Can I customize the workflows for my industry?
Yes. The plugin stores customization in plain markdown files (ICP, business pulse thresholds, lead qualification, onboarding priorities, tone). You can edit them in any text editor, version them in git, or run the built-in `smb-complete` interview to update them conversationally. For the full configuration walkthrough, see the install walkthrough section above.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
