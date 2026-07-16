**BRAND:** mejba.me
**TITLE:** A CMS for Claude Code Websites: The Missing Layer
**META TITLE:** Claude Code CMS: Make AI Sites Client-Editable
**SLUG:** claude-code-cms-client-editable-websites
**PRIMARY KEYWORD:** Claude Code CMS
**META DESCRIPTION:** Claude Code builds gorgeous sites clients can't edit. Here's the CMS layer — GitHub, Vercel, MongoDB Atlas, OpenRouter — that makes them shippable.
**TAGS:** Claude Code, CMS, Vercel, MongoDB, Web Agency

---

# A CMS for Claude Code Websites: The Missing Layer

I've shipped maybe a dozen sites with Claude Code at this point. Marketing pages, a SaaS landing page, a couple of agency one-pagers. Every single one looked good enough that a client would happily pay four figures for it. And every single one had the same fatal flaw the moment I imagined actually handing it over: the client couldn't change a word of it without calling me.

That's the dirty secret nobody puts in the demo videos. A Claude Code CMS isn't a nice-to-have bolted onto these sites — it's the thing that decides whether a beautiful AI-generated website is a real product or a screenshot you posted to Twitter for likes. The design problem got solved sometime in 2025. The "now what" problem did not.

So when I came across Jack's tutorial — he's a serial founder in the AI startup world — walking through exactly this gap, I paid attention. His framing was blunt: roughly 99% of Claude-generated websites are unusable in production because they have no content management layer. A client who needs to fix a typo in their hero headline has two options. Get the raw repo credentials (terrifying) or text the developer (a bottleneck that kills your margins). Neither scales past a handful of clients.

His fix is to build a CMS _on top of_ the static site — a controlled editing layer that lets non-technical people change text, swap images, add pages, and tweak SEO, without ever touching code or being able to break the layout. I want to walk through how that stack actually fits together, what it costs to run on free tiers, and — because I've used every piece of this stack in production — exactly where the "version 1" reality diverges from the highlight reel. By the end you'll know whether this is worth building for your own client work or whether you're better off reaching for an off-the-shelf tool.

But first, the part most people get wrong about _why_ this matters.

## Why a Beautiful Claude Code Website Is Worthless Without a CMS

Here's a scene I've lived more than once. You ship a gorgeous site. The client is thrilled. Two weeks later: "Hey, can you change the phone number in the footer and update the pricing on the second tier? Sorry to bother you!"

That message costs you. Not in the five minutes the edit takes — in the context switch, the redeploy, the "while you're in there can you also..." that follows. Multiply it across ten clients and you've rebuilt the exact freelance trap I wrote about in [how AI website cloning creates recurring revenue](https://www.mejba.me/ai-website-cloning-recurring-revenue): you're trading time for money on a treadmill that never speeds up.

The conventional answer is "just use WordPress" or "build it on Webflow." Both work. Both also throw away the entire reason you used Claude Code in the first place — the custom, hand-tuned design that doesn't look like every other template on the internet. The instant you pour a Claude-generated layout into a WordPress theme, you've sanded off the thing that made it special.

What Jack's approach preserves is the custom code _and_ the editability. The site stays a static, lightning-fast set of files. The CMS sits beside it as a separate dashboard that knows which parts of the page are editable and writes changes back through a controlled pipeline. The client gets a login that only ever exposes safe edit targets — the headline text, the pricing number, the image slot — never the structural HTML that holds the design together.

Jack calls the goal "immutable to client destruction but still editable." That phrase stuck with me because it names the actual product requirement. Clients should be able to do everything they need and nothing that breaks the site. That's the whole game.

And the mechanism that makes it possible is a stack of four free or near-free services wired together. Let me break down each one, because the architecture is the interesting part — not the marketing.

## The Full Stack: Claude Code, GitHub, Vercel, MongoDB Atlas, OpenRouter

Strip away the pitch and the system is five components, each doing one job:

- **Claude Code** generates the website and, separately, the CMS dashboard itself.
- **GitHub** stores the codebase and gives you version control.
- **Vercel** deploys the live site from GitHub and handles hosting.
- **MongoDB Atlas** stores all editable content as the single source of truth.
- **OpenRouter** routes the AI editing assistant to whatever language model you pick.

The flow runs like this. Claude Code writes the site and pushes it to a private GitHub repo. Vercel watches that repo and deploys whatever's on the main branch to a live URL. So far that's a standard static-site deploy — nothing novel, and something I've done dozens of times. The CMS is where it gets interesting. Instead of content living hard-coded in the HTML, the editable pieces live as documents in MongoDB. The published site reads from that database (or from a build that pulls from it), so when a client edits the pricing in the dashboard and hits publish, the change propagates to the live site.

The AI editing assistant — the chat box that lets a client say "make this section shorter and punchier" — runs through OpenRouter. That's a smart choice on Jack's part, and here's why it matters: OpenRouter is a single API that fronts 315+ models from Anthropic, OpenAI, Google, DeepSeek, and others, all behind one OpenAI-compatible endpoint. You switch the model with one parameter, no code changes. For a CMS you're selling to clients, that flexibility is the difference between being locked to one vendor's pricing and being able to swap to a cheaper model the moment one ships.

I'll be honest about one thing up front, because the demo glosses over it: wiring these five things together so they stay in sync is the hard 20% that takes 80% of the time. The "Claude builds it for you" framing is true for the happy path and optimistic everywhere else. We'll get to that. First, let me show you what it costs to run, because that number surprised me.

## What Does It Actually Cost to Run This Stack?

Running a Claude Code CMS for a single client site costs effectively nothing on free tiers, plus per-edit AI usage that's measured in fractions of a cent. Here's the verified breakdown as of June 2026.

**Claude itself** is the only guaranteed monthly cost. The Pro plan is $20/month (or about $17/month if you pay annually) and now bundles Claude on web, the desktop app, Cowork, and Claude Code in your terminal. One important caveat the videos skip: Pro runs on rolling five-hour usage windows. Hit the limit at 2 PM and your next window opens around 7 PM. For heavy site generation you'll feel that wall, and it's the single biggest reason people jump to the Max tier.

**GitHub** is free for private repos. No asterisk for this use case.

**Vercel's Hobby plan** is free and generous: 100 GB bandwidth, 1 million edge requests, and 1 million serverless function invocations per month, with 4 hours of active CPU. The catch that bites agencies specifically — Hobby is **non-commercial only**. The moment you're hosting a paying client's site, you're supposed to be on Pro at $20/month per developer seat, which bumps you to 1 TB of bandwidth and adds overage billing instead of a hard pause. For personal projects, free is genuinely fine. For client work, budget the $20.

**MongoDB Atlas** has a free M0 cluster (formerly called M0, now just "Free") with 512 MB of storage. For a content database holding text, layout config, and version history, 512 MB is a lot — you'll fit dozens of sites before it matters. The real constraints are the throughput limits: roughly 100 operations per second and 500 concurrent connections. Fine for a CMS where edits are occasional human actions. Not fine if you accidentally point high-traffic page rendering directly at it without caching.

**OpenRouter** is pure pay-as-you-go on top of passthrough model pricing, with a 5.5% platform fee on credits. There's no subscription. If a client's AI editing assistant runs, say, 50 edit requests in a month against a mid-tier model, you're looking at pennies. OpenRouter also exposes free models (rate-limited to roughly 20 requests/minute, 200/day) if you want the assistant to cost literally zero during testing.

Add it up for a real client deployment: $20 Claude + $20 Vercel Pro + $0 GitHub + $0 MongoDB + a few cents of OpenRouter = roughly $40/month to run an unlimited number of client sites through one dashboard. If you're charging clients $100–$200/month for hosting and edits — the model I broke down in [the AI agency retainer post](https://www.mejba.me/ai-agency-retainer-model-2026-claude-code) — the margin is obvious. That's the recurring-revenue unlock hiding inside a CMS tutorial.

Now the part I actually care about: how you build the thing.

## How Do You Build a CMS on Top of a Claude Code Website?

You build a Claude Code CMS by generating the static site first, deploying it to Vercel, then having Claude Code generate a separate dashboard app that connects to MongoDB Atlas for content storage and OpenRouter for the AI editing assistant. Here's the five-step sequence Jack demonstrates, with my notes on each.

### Step 1: Set up Claude with the right plan

Grab the Claude desktop app and get on at least the Pro plan ($20/month) so you have Claude Code access. If you're going to generate multiple sites in a session, seriously consider Max — the five-hour Pro window will interrupt you mid-build, and there's nothing more annoying than waiting three hours to finish a layout.

### Step 2: Steal a design direction (the right way)

This is the step that separates a site that looks like _a_ Claude site from one that looks like _your client's_ site. Don't accept Claude's default aesthetic. Pull real inspiration first.

Jack's move is to browse [Dribbble](https://dribbble.com), find layouts that match the vibe the client wants, and feed screenshots or URLs into Claude so it can analyze the actual visual language. He uses a "design blueprint extractor" skill from his community to translate that inspiration into a structured site plan — color system, type scale, section layout, spacing rhythm — before a single line of code gets written.

You don't need his specific skill to do this. The principle is what matters: give Claude a concrete visual reference plus a crisp statement of the site's purpose and brand, and you get output that's miles past the generic default. I've found the same thing every time — the quality of a Claude-generated design is almost entirely a function of how specific the reference and the brief are. Vague brief, generic site. Tight brief with a real reference, something a client will actually pay for. I dig into this dynamic more in [the AI app design systems guide](https://www.mejba.me/ai-app-design-systems-guide).

In the demo, the example build is a service site for an AI automation company selling "Hermes" agents that cut businesses' operational costs. Claude produced a cohesive professional theme, real marketing copy, multiple custom illustrations of the AI concepts, and a clean sectioned layout — system explanations, partner logos, the works. The custom imagery came through an API integration with an affordable AI image generator (he uses one called KIE) rather than stock photos, which is a big part of why it doesn't look templated.

### Step 3: Build and refine iteratively

Generate, then critique with screenshots. This is non-negotiable. You give Claude a screenshot of what it produced and detailed feedback — "the hero spacing is too tight, the partner logo row needs more breathing room, the CTA color is competing with the headline" — and it revises. The first pass is never the final pass. Treat Claude like a fast junior designer you're art-directing, not a vending machine.

### Step 4: Host it for free

Claude Code handles this almost entirely through the CLI. It creates a private GitHub repo, pushes the code, connects Vercel, and deploys to a live URL. The only thing you do manually is authenticate. GitHub is your file storage and version control; Vercel reflects whatever's in the repo to the live site. From the Vercel dashboard you can also buy or link a custom domain. I've run this exact deploy flow many times — it genuinely is close to one command once your auth is set up, and it's the least flaky part of the whole system.

### Step 5: Integrate the CMS (the actual hard part)

Here's where Claude generates a _second_ app — the CMS dashboard — and wires it to the site. The dashboard connects to MongoDB Atlas (your content store), uses a Vercel token to trigger deploys, and routes the AI editing assistant through an OpenRouter API key. This is the piece that turns a static site into an editable platform, and it's the piece that takes real work to get right.

If you've made it this far and you're nodding along, you're already thinking about this more seriously than most people who watch the demo and move on. The next section is where I separate what genuinely works from what's still rough.

If you'd rather have someone build and wire this whole stack for you — the site, the CMS, the multi-tenant setup — I take on exactly these kinds of engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Setting Up MongoDB Atlas as Your Content Backend

MongoDB is the right call for this, and not by accident. It's a document database — it stores JSON-like documents instead of rigid rows and columns. That maps almost perfectly onto a CMS, where every page is really just a tree of content blocks: a hero with a headline and image, a pricing section with three tiers, a footer with contact info. You store each page as a document, each version as a snapshot, and querying is fast.

Setting up the free cluster is genuinely simple, and here are the exact steps from the demo, which match what you'll see in the Atlas UI today:

1. **Create a MongoDB Atlas account** at the cloud console.
2. **Choose the free M0 cluster** — 512 MB of storage, no credit card required.
3. **Name the cluster** something obvious like `website-cms`.
4. **Create a database user** with a username and password the CMS will authenticate with.
5. **Whitelist IP addresses** under Network Access so your app can connect. For development you can allow all IPs temporarily, but lock this down before production.
6. **Grab the driver connection string** and drop it into the CMS config.

Once that connection string is in place, the CMS stops being a local toy and becomes something a client can hit over the internet. And here's the detail that genuinely impressed me in the demo: because MongoDB is the single source of truth, you can wipe the local cache and files entirely and the CMS rebuilds its state from the database. The content isn't trapped in the code. That's a real architectural win and exactly how a content system _should_ behave — the database is the canonical store, the code is disposable.

Atlas also gives you automatic backups, global distribution, and an always-on managed cluster, so you're not babysitting a database server. For a solo operator managing client sites, "I never have to think about the database infrastructure" is worth a lot.

One honest caveat, because I've hit this: the M0 free tier's ~100 ops/sec ceiling is fine for human-paced editing but will absolutely choke if you architect it badly — for instance, hitting the database on every public page view instead of building static pages from it. Read content at build or cache it hard. Don't let visitor traffic touch M0 directly. Get that wrong and your "free" tier quietly becomes the reason a client's site feels slow.

## The Two-Link Architecture: How Multi-Tenant Actually Works

This is the part of Jack's build I found most clever, and also the part where "version 1" honesty matters most.

The system uses two distinct entry points:

- **A master control link** for you, the developer or agency. From here you manage every client site, pick which AI model powers editing, and connect the hosting and database backends. This is your command center.
- **A client-facing editing link**, password protected, scoped to one client's site only. The client logs in and can edit _their_ content and nothing else. They can't see other clients, can't touch structure, can't break anything outside their sandbox.

Claude generates both the individual client sites _and_ the master CMS, coordinating the authentication so each user only ever reaches their assigned pages. Vercel tokens control deployment; OpenRouter keys control the editing AI. In theory this scales to dozens or hundreds of client sites under one dashboard, which is the genuine business unlock — it's the same multi-tenant logic that makes the [AI agency retainer model](https://www.mejba.me/ai-agency-retainer-model-2026-claude-code) work at scale.

What the client can do inside their link: edit text and pricing, adjust spacing and layout within safe bounds, add and modify images, add new pages (an article page, a blank page, whatever), preview across desktop/tablet/mobile with AI suggestions for mobile optimization, and run SEO tools that score target phrases, suggest improvements, and let them edit meta descriptions per page. There's also a form inbox that captures leads directly in the CMS, and an integrated AI chat assistant for editing help. Every change is version controlled, and only validated content publishes live — which is what protects the structure and design from "client destruction."

Now the honesty. Jack is upfront that this is version 1 and basic, and you should take that seriously. A multi-tenant CMS with auth, per-tenant data isolation, audit logs, deploy orchestration, and an AI editing layer is a _real software product_ — the kind of thing companies raise money to build. Claude Code can scaffold a working version of it impressively fast, and the demo proves the architecture is sound. But "demo works end to end" and "I'd trust this with fifty paying clients' data and a real auth boundary" are different statements.

The things I'd personally harden before billing anyone: the auth and tenant-isolation boundary (a bug here means one client seeing another's data — unacceptable), input validation on what the AI editor is allowed to write back, and rate limiting so a single client can't exhaust your OpenRouter credits or hit the M0 throughput ceiling for everyone. None of these are reasons not to build it. They're reasons to treat the generated version 1 as a strong starting point, not a finished product you hand to a client on day one.

That distinction — strong starting point versus shippable product — is the whole point of being honest about AI-built software. So let me put a clear stake in the ground on when this is the right move.

## When This Stack Is Right — And When It Isn't

**Build the Claude Code CMS when** you want fully custom design that no template can give you, you're managing your own projects or a small set of client sites, and you have enough engineering judgment to harden the version-1 auth and data boundaries before going live. The ceiling on design quality and the near-zero hosting cost are unbeatable for this profile. This is the right call for a developer-operator running a lean agency.

**Reach for an off-the-shelf tool instead when** you need battle-tested multi-tenant auth on day one, you're not comfortable reviewing and hardening generated backend code, or your clients need features (e-commerce, complex permissions, compliance audit trails) that a v1 won't have. A mature headless CMS or a Webflow plan exists for a reason. There's no shame in not reinventing auth.

The mistake I'd warn against — and I've watched people make it — is treating the slick demo as production-ready and skipping the hardening. AI-generated software _feels_ finished because it runs. Running and being safe to put in front of paying customers are not the same thing. I've made versions of this mistake myself, shipping something that worked in the demo and broke the moment real-world input hit it. The fix is boring: review the generated code, test the auth boundary like an attacker would, and cache aggressively so your free tiers don't betray you under load.

Here's the prediction I'm willing to make. Within a year, "AI builds your site _and_ gives your client a safe way to edit it" stops being a clever tutorial and becomes table stakes for anyone selling AI-generated web work. The design problem is solved. The editability problem is the new moat. Whoever nails the controlled-editing layer — immutable to client destruction, still fully editable — owns the recurring revenue. The tooling shown here is an early, honest sketch of where that's headed.

## Frequently Asked Questions

### Can Claude Code build a CMS?

Yes — Claude Code can generate both a static website and a separate CMS dashboard that connects to a database, deploys through Vercel, and uses an AI editing assistant. The generated CMS is a strong working version-1 that needs auth and data-isolation hardening before you trust it with paying clients. See the build steps above for the full sequence.

### What's the cheapest way to host a Claude Code website?

The cheapest production-ready path is GitHub for the repo (free), Vercel Pro for commercial hosting ($20/month), and MongoDB Atlas's free M0 cluster (512 MB) for content. Vercel's free Hobby plan works for personal, non-commercial sites only.

### Is MongoDB Atlas free tier enough for a website CMS?

For a content CMS, yes — the free M0 cluster's 512 MB of storage easily holds text, layout config, and version history for many sites. The real limit is throughput (~100 operations per second), so cache aggressively and never point public page rendering directly at M0 under traffic.

### What does OpenRouter do in this setup?

OpenRouter is the single API that powers the CMS's AI editing assistant, fronting 300+ models from Anthropic, OpenAI, Google, and others behind one OpenAI-compatible endpoint. You swap models with one parameter, and pricing is passthrough plus a 5.5% platform fee — no subscription. That flexibility keeps you from being locked to one vendor's costs.

### Why can't clients just edit a Claude Code website directly?

Because the design lives in custom code, and giving a non-technical client raw repo access means one wrong edit breaks the layout. A CMS layer exposes only safe edit targets — text, pricing, images, meta tags — while keeping the structural code locked, so clients can change everything they need and nothing they shouldn't.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

- **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
- **Portfolio**: [mejba.me](https://www.mejba.me)
- **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
- **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
- **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
