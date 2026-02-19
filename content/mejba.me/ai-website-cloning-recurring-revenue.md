**BRAND:** mejba.me
**TITLE:** How AI Website Cloning Creates Recurring Revenue
**SLUG:** ai-website-cloning-recurring-revenue
**TAGS:** AI Automation, Web Design Business, Lead Generation, Recurring Revenue, Strategy Guide

---

# How AI Website Cloning Creates Recurring Revenue

Last Tuesday at 11 PM, I cloned a website fourteen times in under an hour. Each version had unique logos, testimonials, color schemes, service descriptions, and localized copy — all tailored to different pool cleaning businesses across three Texas cities. Total cost for the scraping, enrichment, and deployment pipeline: $4.23.

I didn't write a single line of HTML by hand. I didn't open Figma. I didn't negotiate with a single client yet. But by Wednesday morning, I had fourteen personalized, production-quality websites sitting on Vercel, ready to send as free samples to business owners who desperately needed them.

This isn't some hypothetical. I've been testing a system that combines AI-powered website generation, automated lead scraping, and email outreach into a pipeline that turns boring local businesses into monthly recurring revenue. And the part that keeps surprising me — the businesses that pay the most aren't the ones you'd expect. They're pool cleaners. Junk haulers. Pest control operators. The most unglamorous industries you can imagine.

But here's what nobody tells you about this model: the money isn't in the websites. The websites are free. The real revenue comes from something else entirely, and it takes a specific mindset shift to see it. I'll break down the exact framework, but first you need to understand why the obvious approach to selling web design services is fundamentally broken.

---

## Why Traditional Web Design Freelancing Is a Trap

I've built websites for clients since 2018. Custom Laravel backends, React frontends, the whole stack. And for years, I made the same mistake every developer-turned-freelancer makes: I charged for the build.

Think about what that means. You spend two to four weeks building a custom site. You charge $2,000, maybe $5,000 if you're good at sales. The client pays once. Then they disappear for eighteen months until something breaks or they want a redesign. Your revenue is pure feast-or-famine. One month you're flush, the next you're hunting for the next project.

I watched agencies around me solve this with retainers, but retainers require relationship-building, project management overhead, and a level of client hand-holding that scales terribly. For a solo developer or small team, the math just doesn't work past five or six clients.

The model I'm about to walk you through flips this entirely. You don't charge for the website. You give it away for free. The website becomes your foot in the door — your proof of competence — and the recurring revenue comes from services the client didn't even know they needed until you showed them what was possible.

Sound counterintuitive? It did to me too. Until I ran the numbers.

A single pool cleaning business paying $150 per month for hosting, basic SEO, and an AI chatbot generates $1,800 per year. Ten clients at that rate — $18,000. Thirty clients — $54,000. And because you've automated the website creation and most of the service delivery, your actual time investment per client drops to maybe two hours per month after the initial setup.

That's the destination. Here's the road map.

---

## Step One: Pick the Most Boring Niche You Can Find

I know your instinct. Mine was the same. When I first heard "niche down for web design," my brain went straight to dentists, real estate agents, fitness coaches. The industries with obvious online presence needs and money to spend.

Here's the problem: every web designer on Upwork, Fiverr, and every freelance marketplace had the same instinct. The dentist market is saturated beyond belief. Real estate agents get cold-pitched by three web designers a week. Gym owners have been offered free websites so many times they've developed antibodies to the pitch.

The profitable niches are the ones that sound boring at a dinner party. Commercial cleaning companies. HVAC repair. Landscaping. Tree removal services. Auto detailing. Pest control. Pool maintenance. Junk removal.

Why do these work? Three reasons that took me embarrassingly long to figure out.

**They have money.** A commercial cleaning company doing $500K in annual revenue absolutely has budget for a $150/month web presence. That's a rounding error in their overhead. An HVAC company making $80K on a single commercial installation doesn't blink at marketing spend.

**Their websites are terrible.** Pull up Google Maps right now for "pool cleaning" in any mid-size city. Click through the first twenty results. I guarantee at least half have websites that look like they were built in 2009 — because they were. Some don't have websites at all, just a Google Business Profile with a phone number.

**They're not getting pitched.** Nobody is cold-emailing junk removal companies with free website offers. The competition for their attention is almost zero. When you show up with a professional, mobile-responsive site customized with their business name and local details, the reaction is consistently "wait, you did this for us? For free?"

My validation process is dead simple. Search "[service] near [city]" on Google Maps. Count the businesses. If there are at least twenty, check their websites. If more than half look outdated or non-existent, and the businesses have 50+ Google reviews (which means they're active and making money), that's a viable niche.

I settled on pool cleaning in Texas as my test market. Seventy-one unique businesses in the Austin metro area. Ninety-six percent had phone numbers listed. Only forty-six percent had email addresses I could find — before enrichment. The competition for this niche was so thin that my outreach response rate hit numbers I'd never seen in my previous freelancing life.

But finding the niche is the easy part. What comes next is where the AI automation turns this from a nice idea into something that actually scales.

---

## Step Two: Scraping Leads Without Losing Your Mind

Manual lead research is the silent killer of every web design business model. You find a niche, get excited, start Googling businesses one by one, copying phone numbers into spreadsheets, hunting for email addresses on websites that don't have contact pages. Two hours later, you have twelve leads and zero energy left to actually build anything.

I wasted an entire weekend doing this before I discovered how to automate the pipeline. The toolchain I use now costs less than a dollar per batch and produces cleaner data than anything I ever assembled manually.

**The scraping layer** uses a Chrome extension called Instant Data Scraper combined with Appify for more advanced extraction. Instant Data Scraper is surprisingly powerful for a free tool — point it at Google Maps search results and it pulls business names, addresses, phone numbers, websites, ratings, and review counts into a structured table. For pool cleaners in Austin, this took about three minutes to generate a base list of seventy-one businesses.

But raw scraped data is only half useful. You need emails, and most Google Maps listings don't include them. That's where the enrichment step matters.

**The enrichment layer** uses Any Mail Finder's API to cross-reference business names and domains against email databases. It validates addresses in real-time, so you're not wasting outreach on dead inboxes. My enrichment run on the Austin pool cleaner data bumped email availability from forty-six percent to over seventy-five percent. Cost: $0.77 for the entire batch.

Here's a snapshot of what a single enrichment run produced:

| Metric | Before Enrichment | After Enrichment |
|---|---|---|
| Total businesses | 71 | 71 |
| Phone numbers | 96% | 96% |
| Websites found | 89% | 89% |
| Valid emails | 46% | 77% |
| Social profiles | ~50% | ~50% |
| Total cost | $0.00 | $0.77 |

The data gets exported as a CSV that feeds directly into the outreach platform. No manual copying. No spreadsheet gymnastics. The whole pipeline — from "I want to target pool cleaners in Austin" to "I have a validated lead list ready for email campaigns" — takes under fifteen minutes.

I built reusable automation scripts for this using Anti-Gravity's skill system, which means switching to a new niche or city is literally a matter of changing two variables and running the scrape again. The first time took me an afternoon to set up. Every subsequent run takes minutes.

Now you have leads. But sending cold emails to businesses without something concrete to show them is a waste of everyone's time. You need the website first. And this is where the cloning system changes everything.

---

## Step Three: Build Once, Clone Infinitely

This is the part that made me rethink my entire approach to web development. Not just for this business model — for everything.

The traditional web dev mindset says: every client gets a custom build. You start from scratch, or maybe from a template, and spend hours customizing everything to match the client's brand. It's artisanal. It's time-consuming. And for a business model that depends on volume, it's a death sentence.

The AI-powered cloning approach works differently. You build one phenomenal website for your target niche. One. You pour your best design skills into it. You research the top five to ten competitors in the niche — the ones with the highest Google reviews and the most professional online presence — and you study what they do well. Clear service descriptions. Trust indicators. Real testimonials with actual names. Phone numbers prominently displayed instead of generic contact forms. FAQ sections that answer the questions prospects actually ask.

Then you build a template that incorporates all of these best practices. Here's my actual process:

**Research phase.** I search for the top-rated businesses in my target niche on Google. Not the ones with the prettiest websites — the ones with the most five-star reviews. These are the businesses that are winning in the real world. I study their sites and note every element that contributes to conversions: hero copy structure, service page layout, testimonial placement, pricing tier presentation, and how they handle calls to action.

**Design phase.** I use a combination of Google AI Studio for initial design mockups and Dribbble for visual inspiration. The trick isn't copying any single design — it's extracting the patterns that work across the top performers and combining them into something better than any individual competitor has. I run every design through a UI/UX audit checking contrast ratios, mobile responsiveness, accessibility scores, and load performance.

**Build phase.** The actual code gets generated through AI tools — Anti-Gravity and Claude Code handle different parts of this. The site is built with a config-driven architecture, which is the crucial technical decision that makes cloning possible. Every business-specific element — logo, business name, tagline, testimonials, service descriptions, pricing, phone number, address, color scheme — lives in a configuration object. The website reads from this config at build time.

**Clone phase.** When I want to create a version for a new business, I duplicate the config file, swap in the new business details, and deploy. Anti-Gravity's cloning system automates even this step — feed it a business name and it can pull publicly available information to populate the config automatically. Logos get generated or extracted. Color schemes get adjusted. Copy gets rewritten to include local keywords and business-specific details.

One build. Fourteen clones in under an hour. Each one looks custom. Each one is optimized for the specific business. None of them required me to touch HTML.

**Pro tip:** Store everything in GitHub. Every clone gets its own branch or deployment configuration. Hosting on Vercel means each site gets a unique URL automatically. When a prospect sees their business name on a live, professional website — not a mockup, not a PDF, but an actual functioning site — the conversion psychology is entirely different from showing a portfolio of other people's websites.

Multi-page sites add another dimension here. Tools like Stitch integrate with the cloning pipeline to produce full multi-page sites with about pages, service pages, contact forms, and galleries — all programmatically generated and customizable per clone. This isn't a single landing page anymore. It's a complete web presence that would cost $3,000-$5,000 if built traditionally.

At this point, you've got leads and you've got personalized websites for those leads. The next step is getting those websites in front of the right people — without spending your entire week sending emails manually.

---

## Step Four: Outreach That Doesn't Feel Like Spam

Cold email has a terrible reputation, and honestly, most of it deserves that reputation. Generic "Hey, I noticed your website could use an upgrade" messages get deleted instantly. Business owners can smell a mass blast from three words in.

The system I use is different for one specific reason: you're not pitching. You're giving.

Imagine you own a pool cleaning business. You're busy. Your website was made by your nephew in 2015 and you know it's embarrassing but you don't have time to deal with it. Then you get an email with a link to a professional, modern website that already has your business name on it, your real phone number, your actual service area, and testimonials that read like they could be from your real customers.

That email doesn't feel like spam. It feels like someone did you a favor.

**The outreach platform** I use is Instantly. It handles campaign automation, follow-up sequences, A/B testing, and reply detection. The setup looks like this:

**Email 1 (Day 1):** Short, friendly, zero pressure. "Hey [First Name], I'm [Your Name] — I build websites for [niche] businesses in [City]. I put together a free sample site for [Business Name] that I thought you might find useful. No strings attached — take a look: [personalized URL]. If you'd like to use it or want any changes, just reply."

**Email 2 (Day 5):** Quick follow-up. "Just checking if you had a chance to look at the site I put together for [Business Name]. Happy to walk you through it on a quick call if you'd like. Either way, it's yours to keep."

**Email 3 (Day 14):** Value-add angle. "One thing I noticed while researching [Business Name] — you're ranking on page 2 for '[niche] in [city]' on Google. With a few SEO tweaks to the new site, you'd likely move to page 1. Want me to show you what that looks like?"

**Email 4 (Day 28):** Last touch. Light, professional, leaves the door open. "Last note from me — the site I built for [Business Name] is still live if you'd like to use it. If now's not the right time, totally understand. I'll be here if things change."

Three critical principles make this sequence work:

**Personalization is non-negotiable.** Every email includes the business name, owner's first name, city, and a direct link to their custom website. Instantly's dynamic field system handles this automatically from the CSV lead list.

**A/B test everything.** I run split tests on subject lines for every campaign. "Quick question about [Business Name]" vs "Free website for [Business Name]" vs "I built something for [Business Name]." Response rates vary by 3-8x between subject lines, and the winner is never the one I predict.

**Stop sequences save your reputation.** The moment someone replies — even if it's "not interested" — the automated sequence stops. No one should ever receive a follow-up after they've already responded. Instantly handles this automatically, but you need to configure it correctly.

My response rate on the pool cleaning campaign was twenty-three percent. Not open rate — response rate. For context, industry average cold email response rates hover around one to two percent. The difference is entirely attributable to leading with a free, personalized deliverable instead of a pitch.

Of those responses, about forty percent wanted to use the site, thirty percent were interested but had questions, and thirty percent said no thanks. The nos were completely fine — the website cost me nothing to produce, and the relationship might convert later.

But responding to "yes, I'd love to use this" is just the beginning. What happens next is where the actual revenue model lives.

---

## Step Five: The Razor and Blade Model for Web Services

Gillette figured this out a century ago. Give away the razor. Sell the blades.

Your free website is the razor. The monthly services are the blades. And unlike physical razors, your blades get more valuable over time as clients see results.

Here's the conversation flow that converts a free website recipient into a paying monthly client. I'm sharing this because it took me multiple failed attempts to get right, and the failures were all caused by the same mistake: pitching too early.

**Phase 1: Deliver and listen.** When someone responds positively, your only job is to get them on a five-minute call to walk through the site. During that call, ask two questions: "What do you think?" and "What's the biggest challenge with your online presence right now?" Then shut up and listen.

They'll tell you everything. "I don't get any leads from the internet." "I can't figure out Google." "People can't find us when they search." "I'm paying some company $500 a month for SEO and I don't think it's working." "I wish I could respond to inquiries faster."

Every answer maps to a service you can provide.

**Phase 2: Propose one thing.** Not five things. One. Based on what they told you they struggle with, offer a single service at a price point that feels like a no-brainer. If they're worried about online visibility, offer basic local SEO for $100/month. If they want faster lead response, offer an AI chatbot integration for $75/month. If they need hosting and maintenance for the new website, offer that for $50/month.

Start low. I know this feels wrong. You're thinking "$50 a month isn't worth my time." You're wrong, and here's why.

**Phase 3: Expand over time.** That $50/month hosting client? After two months, they trust you. They've seen their site running smoothly. Now you suggest adding SEO for another $100/month. Three months later, you add an AI chatbot for $75/month. Six months in, they ask you about social media management.

My average client started at $75/month and grew to $225/month within four months. Not because I pressured them — because I delivered results at each tier and they organically wanted more.

Here's the service menu I offer, with typical price ranges:

| Service | Monthly Price | Your Time Investment |
|---|---|---|
| Website hosting + maintenance | $50-$100 | 30 min/month |
| Custom domain management | $25-$50 | 15 min/month |
| Local SEO (Google Business, citations) | $100-$200 | 2 hrs/month |
| AI chatbot (lead qualification) | $75-$150 | 1 hr setup, 15 min/month |
| Review generation system | $50-$100 | 30 min/month |
| Social media content | $200-$400 | 3-4 hrs/month |
| Email/SMS follow-up automation | $100-$200 | 1 hr setup, 30 min/month |

A client who buys hosting, SEO, and a chatbot pays $225-$450 per month. Twenty clients at the midpoint of $300/month generates $6,000 in monthly recurring revenue. That's $72,000 per year from a system where the website — the traditional deliverable that agencies charge thousands for — was free.

And the compounding effect is the real story. Every month, some percentage of your pipeline converts. Every month, existing clients add services. Revenue grows in both directions simultaneously. After six months, I stopped needing to run cold outreach campaigns because referrals from happy clients started filling the pipeline on their own.

---

## The Honest Parts Nobody Mentions

I'd be lying if I said this was frictionless. Let me share what actually went wrong, because the mistakes were more educational than the wins.

**Mistake one: I picked the wrong niche first.** My first attempt targeted restaurants. Made sense in theory — restaurants need websites, they have money, they're everywhere. In practice, restaurant owners are the hardest people to get on the phone. They work insane hours, they get pitched constantly by marketing companies, and their margin sensitivity means even $50/month feels like a lot. I burned three weeks and got two responses from forty emails. Switching to pool cleaning tripled my response rate overnight.

**Mistake two: I over-engineered the first website.** My developer brain couldn't resist adding animations, parallax scrolling, custom form validation with real-time error messages. It looked incredible. It also took me four days to build and was nearly impossible to clone because the customizations were too deeply embedded. The version that actually worked — and that I now clone — is simpler, faster, and focuses entirely on conversion elements rather than technical impressiveness.

**Mistake three: I tried to sell on the first call.** The first three times someone accepted my free website, I immediately launched into pricing for additional services. All three went cold. When I switched to pure listening — no selling until the second or third conversation — my conversion rate jumped from zero to over sixty percent. People buy from people who understand their problems, not from people who have solutions looking for problems.

**The limitation nobody talks about:** This model requires patience. The first month feels like a lot of work with no return. You're building the template, setting up scraping, creating the outreach sequence, and waiting for responses. Month two starts generating conversations but probably only one or two paying clients. The hockey stick growth doesn't hit until month three or four when referrals kick in and your service delivery is smooth enough to handle volume.

If you need money next week, this isn't the right model. If you can invest eight to ten weeks of consistent effort, the recurring revenue foundation you build will outlast any one-off freelance project.

**A prediction that might be unpopular:** I think traditional web design agencies that charge $5,000-$15,000 for custom builds have about eighteen months before this model erodes their market significantly. When a solo operator with AI tools can produce 80% of the same output at zero upfront cost to the client, the value proposition of a big agency build becomes very hard to justify. The agencies that survive will be the ones that shift to recurring service models — which is exactly what this framework is designed around.

---

## What the Numbers Actually Look Like After 90 Days

I'm sharing real metrics because vague success claims are worthless. This is from my pool cleaning niche test in Texas, running the full pipeline for 90 days.

**Lead generation:** 213 unique businesses scraped across Austin, San Antonio, and Houston. 164 valid email addresses after enrichment. Total scraping and enrichment cost: $3.41.

**Website production:** 1 master template built (took 6 hours including research and design). 47 cloned versions produced for outreach. Average clone time: 4 minutes per business.

**Outreach results:** 164 emails sent across 4 campaigns. 38 responses (23.2% response rate). 22 positive conversations. 14 accepted the free website. 9 converted to paying clients within 60 days.

**Revenue at day 90:** $1,875/month recurring ($225 average per client across 9 clients). Projected annual: $22,500. Total investment: approximately 40 hours of work across 90 days, plus $3.41 in scraping costs and $30/month for the Instantly subscription.

Those aren't life-changing numbers yet. But the trajectory is what matters. At month three, I was adding two to three clients per month. If that rate holds — and referrals suggest it will accelerate — the twelve-month projection sits around $5,000-$7,000 in monthly recurring revenue from this single niche.

The real test will be replicating this across a second and third niche. HVAC is next. The scraping is already done.

**Quick wins to measure early:** Your first leading indicator is email response rate. If it's below ten percent, your subject lines need work or your niche is too saturated. Above fifteen percent means you've found a responsive market. Above twenty percent and you've hit something special.

The second indicator is the free-to-paid conversion rate. If fewer than thirty percent of businesses that accept the free website convert to at least one paid service within sixty days, your listening-and-proposing process needs adjustment. You're probably pitching too early or proposing the wrong first service.

---

## Your Move

I started this by cloning fourteen websites at 11 PM on a Tuesday. Three months later, nine of those businesses pay me every month for services they genuinely value — and most of them found me first through a free website that cost me four minutes and zero dollars to produce.

The tools exist. Anti-Gravity handles the cloning. Appify and Instant Data Scraper handle the leads. Any Mail Finder handles the emails. Instantly handles the outreach. Vercel handles the hosting. Claude Code handles the parts I need custom logic for. The entire pipeline runs on less than $35/month in tooling costs.

The question isn't whether this works. The question is which boring, profitable, underserved niche are you going to pick — and whether you're willing to give away your best work for free to build something that pays you every single month.

Open your browser. Search Google Maps for a service industry in your city. Count the businesses. Check their websites. If the opportunity is as obvious as it was for me, you'll know within five minutes.

The razor is free. The blades are forever.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
