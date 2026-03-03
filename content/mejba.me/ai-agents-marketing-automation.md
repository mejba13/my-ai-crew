**BRAND:** mejba.me
**TITLE:** AI Agents for Marketing: My 24/7 Autonomous Stack
**SLUG:** ai-agents-marketing-automation
**TAGS:** AI Automation, Marketing Technology, AI Agents, Claude Code, Tutorial

---

Three months ago, I watched a Facebook ad campaign run itself for 72 hours straight — pausing underperformers, reallocating budget to winners, scraping new audience data — while I was debugging a Laravel authentication issue for a completely different client.

That's not a flex. It's a description of what's actually possible right now when you stop thinking about AI as a writing assistant and start thinking about it as an autonomous operational system.

I've been building software for years. Shipped Laravel applications, deployed Kubernetes clusters, written more CI/CD pipelines than I care to count. But nothing quite prepared me for the strange feeling of watching an agent swarm handle marketing tasks I used to do manually — tasks that once consumed entire workdays. It felt wrong at first. Like I was cheating on my calendar.

The thing that surprised me most wasn't the automation itself. It was how much of marketing is just *pattern-repeated work* dressed up as strategy. Post engagement tracking. Cold email sequencing. Ad variant testing. These aren't creative tasks requiring deep human judgment. They're deterministic workflows waiting to be handed off to something that never sleeps and never gets distracted.

But here's what nobody in the "AI for marketing" space will actually tell you: most tutorials are either too abstract to implement or so vendor-specific they stop working the moment an API changes. What I want to share is the real architecture — the specific tools, the actual failure points, and honest results from months of running this in production.

And there's one part of this setup that most marketers skip entirely, the piece that transforms an automation experiment into something that keeps improving without you. I'll cover it in detail further down — and it's the reason this whole stack actually scales.

---

## Why Marketing Became the Perfect Target for Autonomous AI

The reason marketing tasks are so automatable isn't that they're simple. It's that they're *predictable*.

Think about what a cold outreach sequence actually requires:
- Find the right contacts (scraping, data enrichment)
- Validate their contact information (email verification)
- Craft a message (template plus personalization layer)
- Send it at the right time (scheduling)
- Follow up based on responses (conditional logic)
- Track results and adjust (analytics plus feedback loop)

Every single one of those steps can be expressed as a function call. When you frame it that way, you stop seeing marketing workflows as creative endeavors and start seeing them as *data pipelines with a human-sounding output layer*.

My first attempt at automating this was with shell scripts and cron jobs in 2023. It worked — barely. The problem was brittleness. One API schema change, one rate limit hit, and the whole thing broke silently. I'd come back after a week to find zero emails sent and no error logs that made any sense.

What changed everything was Claude Code and MCP (multi-cloud platform) architecture. Not because the underlying tasks changed, but because the orchestration layer became intelligent. Instead of rigid scripts that failed catastrophically, I had agents that could observe a failure, reason about what went wrong, try an alternative approach, and report back with context.

That's the actual difference between automation and agency. And once you feel it working, you cannot go back to doing it manually.

---

## The Stack: Every Tool and Exactly Why It's There

Before getting into implementation, here's what my current marketing agent stack looks like. Every tool earns its place — nothing is in here because it was trending on a YouTube video.

**Claude Code** is the brain. It coordinates everything else, writes integration scripts, debugs failures, and handles the reasoning layer when something unexpected happens. Running on claude-opus-4-6 via API gives agents enough capability to handle edge cases that would snap a simpler system in half.

**Phantom Buster** handles LinkedIn scraping. Yes, cheaper alternatives exist. But Phantom Buster's reliability and the depth of their phantom library — specifically the LinkedIn Post Engager Phantom — means I'm not rebuilding scrapers every time LinkedIn adjusts their front-end DOM structure.

**Instantly AI** is the cold email platform. I evaluated three competitors before landing here: Smartlead, Reply.io, and Lemlist. Instantly won on deliverability infrastructure and API documentation quality. Those two factors matter more than any UI feature when you're running automation at scale.

**Apollo API** fills the email-finding gaps. When I have a LinkedIn profile but no email address, Apollo's enrichment API returns a verified business email roughly 73% of the time. The other 27% get dropped — clean lists are more valuable than large ones.

**Million Verifier** validates emails before anything gets sent. Cold email deliverability collapses fast if you're sending to invalid addresses at scale. This step is non-negotiable.

**Railway** is where everything runs. Not Heroku, not a VPS I have to babysit. Railway lets me spin up a Node.js or Python environment, deploy with a git push, and tear it down when the task finishes. For batch jobs that need to run nightly on a schedule, this is exactly the right deployment model. On-demand infrastructure that actually costs what it should.

**Facebook Ads API** is the muscle for paid acquisition. Everything people do manually in Ads Manager — creating ad sets, uploading creatives, pulling performance data, pausing campaigns — has an API endpoint. Most marketers don't know this. The ones who do usually don't have the engineering time to use it. Agents have nothing but time.

**Perplexity API** keeps the agents current. When I need to understand a specific audience's pain points, I can have an agent query Perplexity for recent Reddit and Twitter discussions about the topic, synthesize them into specific pain-point language, and use that language directly in ad copy. Real words from real people — this is what makes copy actually resonate.

**React plus HTML Canvas** is the creative layer for ad generation. More on this shortly, because it's probably the weirdest-sounding part of the stack and also one of the most effective.

Now here's where most articles stop. They list the tools and move on. What they skip is how these pieces actually connect — and the connections are where 80% of the real work lives.

---

## Building the LinkedIn Pipeline: From Post Engagement to Email Campaign

The most immediately valuable thing I built was the LinkedIn engagement scraper plus cold email pipeline. Here's the specific workflow, failure points included — because the failure points are what you'll actually encounter.

**Step 1: Trigger on Post Engagement**

Post a piece of content on LinkedIn — ideally something with a free resource attached. A template, a checklist, a short guide. When people comment or react, Phantom Buster's LinkedIn Post Engager Phantom scrapes those profiles automatically via webhook.

```javascript
// Phantom Buster webhook output lands here
app.post('/phantom-webhook', async (req, res) => {
  const { profiles } = req.body;

  // Filter for profiles we haven't already processed
  const newProfiles = await filterExistingContacts(profiles);

  // Queue for enrichment
  await queueForEnrichment(newProfiles);

  res.json({ queued: newProfiles.length });
});
```

The webhook means there's no polling loop. Phantom Buster runs on its schedule and pushes data when it's ready. My server wakes up, processes, and goes back to idle.

**Step 2: Enrich and Verify**

Each profile goes through Apollo for email enrichment, then Million Verifier for validation. About 27% of contacts drop off here — Apollo can't find an email, or Million Verifier flags the address as risky. That's fine. A smaller, cleaner list dramatically outperforms a large, dirty one.

```python
async def enrich_profile(profile):
    # Apollo enrichment call
    apollo_response = await apollo.enrich(
        first_name=profile.first_name,
        last_name=profile.last_name,
        linkedin_url=profile.linkedin_url,
        domain=profile.company_domain
    )

    if not apollo_response.email:
        return None

    # Verify email quality before it touches any campaign
    verification = await million_verifier.verify(apollo_response.email)

    # Only 'ok' and 'catch_all' are acceptable
    if verification.result not in ['ok', 'catch_all']:
        return None

    return {**profile, 'email': apollo_response.email, 'verified': True}
```

**Step 3: Add to Instantly Campaign**

Verified contacts get added to a specific Instantly campaign automatically. The campaign is pre-built with a three-step sequence: initial outreach, a value-add follow-up on day 4, and a direct ask on day 9.

```javascript
await instantly.addContact({
  campaign_id: process.env.INSTANTLY_CAMPAIGN_ID,
  email: contact.email,
  first_name: contact.firstName,
  company: contact.company,
  custom_variables: {
    linkedin_post_title: triggerPost.title,
    // Personalize based on how they engaged
    engagement_type: contact.engagementType  // 'comment' or 'reaction'
  }
});
```

**Pro tip:** Personalize the first email based on engagement type. Someone who commented gets a specific reference to their comment content. Someone who only reacted gets a warmer but less specific opener. This small personalization lift measurably improves reply rates and it costs almost nothing to implement.

The whole pipeline from LinkedIn engagement to contact-in-campaign runs in about 4 minutes per batch. Manually, this used to be a 2-hour process I ran once a week at best. Now it runs nightly without my involvement.

If you've gotten through the LinkedIn pipeline and it makes sense, you already understand 70% of how agent-based marketing works. The Facebook ad system is where things get significantly more interesting — and more powerful.

---

## The Bulk Ad Generator: 50 Variants in 8 Minutes

I used to genuinely hate Facebook ad creation. Not because ads don't work — they do — but because the creative process was a constant bottleneck. Ideating hooks, writing copy, resizing images for different placements, setting up A/B tests... it could consume an entire Friday afternoon for a single campaign launch.

The agent-based approach eliminates this almost entirely.

**Step 1: Research Real Pain Points**

Before writing a single word of ad copy, the agent queries Perplexity for recent social media discussions about the target audience's actual frustrations. Reddit threads, YouTube comments, Twitter discussions — it's all searchable via API.

```python
pain_points = await perplexity.search(
    query=f"people frustrated by {target_topic} site:reddit.com OR site:twitter.com",
    date_range="last_30_days",
    max_results=20
)

# Extract the specific language people use
# "I can't figure out why..." / "spent three hours on..." / "nobody tells you that..."
language_patterns = extract_pain_language(pain_points)
```

The output is real vocabulary — the exact phrases people use to describe their frustrations. Ad copy written in the audience's own language converts dramatically better than copy written by someone guessing at what they care about. This step alone improved my ad relevance scores noticeably.

**Step 2: Generate Ad Creatives via React Components**

This is the part that sounds strange until you see it working: ad creatives built as React components, rendered to canvas, exported as images. No Midjourney. No Stable Diffusion. No per-image API costs.

```jsx
// AdCreative.jsx — renders at exactly Facebook's required dimensions
const AdCreative = ({ headline, subtext, cta, colorScheme, format }) => {
  const dimensions = {
    square: { width: 1080, height: 1080 },
    story: { width: 1080, height: 1920 },
    landscape: { width: 1200, height: 628 }
  };

  const { width, height } = dimensions[format];

  return (
    <div style={{
      width,
      height,
      background: colorScheme.background,
      display: 'flex',
      flexDirection: 'column',
      justifyContent: 'center',
      padding: '80px',
      fontFamily: 'Inter, sans-serif'
    }}>
      <h1 style={{
        fontSize: format === 'landscape' ? 52 : 72,
        color: colorScheme.primary,
        lineHeight: 1.1,
        fontWeight: 700,
        margin: 0
      }}>
        {headline}
      </h1>
      <p style={{
        fontSize: format === 'landscape' ? 28 : 36,
        color: colorScheme.secondary,
        marginTop: 24,
        lineHeight: 1.4
      }}>
        {subtext}
      </p>
      <div style={{
        marginTop: 48,
        background: colorScheme.cta,
        color: '#fff',
        padding: '20px 40px',
        borderRadius: 8,
        display: 'inline-block',
        fontSize: 24,
        fontWeight: 600,
        width: 'fit-content'
      }}>
        {cta}
      </div>
    </div>
  );
};
```

Run this through Puppeteer for headless rendering, screenshot each variant, and you have production-ready ad creatives. I can generate 50 variants across three formats (square, story, landscape) in roughly 8 minutes. Each one tests a different headline angle, a different color scheme, a different CTA.

The cost per creative variant: essentially zero. Compare this to premium AI image generators at $0.04–$0.08 per image for 50+ variants per campaign. The savings add up fast when you're running multiple campaigns simultaneously.

**Step 3: Bulk Upload via Facebook Ads API**

This is where having well-documented APIs matters more than anything else. The Facebook Ads API exposes everything in Ads Manager programmatically.

```javascript
const uploadBatch = async (creatives, adSetId) => {
  const ads = await Promise.all(
    creatives.map(async (creative) => {
      // Upload image to Facebook's CDN first
      const imageHash = await facebookAds.uploadImage({
        account_id: process.env.FB_ACCOUNT_ID,
        filename: creative.imagePath
      });

      // Create the ad creative object
      const adCreative = await facebookAds.createAdCreative({
        account_id: process.env.FB_ACCOUNT_ID,
        image_hash: imageHash.hash,
        title: creative.headline,
        body: creative.bodyText,
        call_to_action: {
          type: 'LEARN_MORE',
          value: { link: creative.destinationUrl }
        }
      });

      // Create the ad — paused for review before activation
      return facebookAds.createAd({
        name: `Variant_${creative.id}`,
        adset_id: adSetId,
        creative: { creative_id: adCreative.id },
        status: 'PAUSED'  // Always start paused. Always.
      });
    })
  );

  return ads;
};
```

All ads go in as drafts initially. I do a 10-minute review pass — checking for anything obviously wrong, any copy that missed the mark — then activate the batch. From that point, the optimization agent takes over.

---

## The Open Loop I Planted Earlier: Campaign Optimization

Here's the piece I flagged at the start — the part most marketers skip, the thing that actually makes this system scale.

Continuous campaign optimization.

Creating ads is straightforward. Most people build automation that creates campaigns and then manually checks results a week later, making gut-feel adjustments based on incomplete data. That's like building a racing engine and then never tuning it. You get some performance improvement but nowhere near what's possible.

The optimization agent runs every 6 hours. It pulls campaign performance data from the Facebook Ads API, applies a scoring algorithm tuned to the campaign type, and takes direct action:

```python
async def optimize_campaigns():
    campaigns = await fb_ads.get_active_campaigns(
        account_id=os.environ['FB_ACCOUNT_ID']
    )

    for campaign in campaigns:
        ads = await fb_ads.get_ads(
            campaign_id=campaign['id'],
            fields=['id', 'name', 'spend', 'impressions',
                   'clicks', 'ctr', 'cpp', 'reach', 'status']
        )

        for ad in ads:
            spend = float(ad.get('spend', 0))
            impressions = int(ad.get('impressions', 0))
            clicks = int(ad.get('clicks', 0))
            ctr = clicks / impressions if impressions > 0 else 0

            # Minimum spend threshold before making decisions
            # ($15 gives statistically meaningful data without wasting budget)
            if spend < 15:
                continue

            # Pause underperformers
            if ctr < 0.008:  # Below 0.8% CTR
                await fb_ads.update_ad(ad['id'], {'status': 'PAUSED'})
                await log_decision(
                    action='paused',
                    ad_id=ad['id'],
                    reason=f"CTR {ctr:.3%} below threshold after ${spend:.2f} spend"
                )

            # Scale winners: duplicate into new ad set with higher budget
            elif ctr > 0.025 and spend > 30:  # Above 2.5% CTR after $30 spend
                await duplicate_winning_ad(
                    ad=ad,
                    campaign_id=campaign['id'],
                    budget_multiplier=2.0
                )
                await log_decision(
                    action='scaled',
                    ad_id=ad['id'],
                    reason=f"CTR {ctr:.3%} — creating scaled ad set"
                )
```

The thresholds are tunable and I adjust them based on campaign type and industry CTR benchmarks. What matters is that this loop runs constantly — not when I remember to log in and check.

Over time, the campaign mix shifts. Winning ads accumulate budget. Losers get cut early, before they drain significant spend. The overall ROI improves without active management. This is what "autonomous" actually means — not "I set it up once" but "it keeps getting better without me being involved in the decisions."

Running this system for 4 weeks on a campaign produces results that would take 8-10 weeks of manual optimization to achieve. The compounding effect is real.

---

## Honest Mistakes I Made Building This

Alright, the part where I stop being polished about it.

**Giving agents full spend authorization too early** was my most expensive mistake. I had an agent authorized to activate Facebook ads — not just create them as drafts, but actually enable them for spend. One night, a rate-limit error caused a retry loop in my upload script. I woke up to 200+ duplicate ads with active budgets and a credit card alert from Facebook. Total loss: about $340 before I caught it.

Lesson: human review gates on anything that touches real money. Non-negotiable. Agents draft, humans activate. This one rule would have saved me $340 and a stressful morning.

**LinkedIn automation has hard limits that the Phantom Buster docs understate.** LinkedIn actively detects and throttles scraping. Push past roughly 80-100 profile scrapes per day and you start seeing slowdowns and occasional temporary access restrictions. The agents need built-in rate limiting, exponential backoff, and they need to respect signals that they're moving too fast. I learned this the slow way.

**Email warm-up exists for a reason.** Instantly AI has built-in warm-up functionality, but I initially skipped their recommended 3-week warm-up period because I was impatient to start the campaigns. My deliverability took a significant hit for two weeks before I diagnosed what happened and restarted the warm-up process properly. Three weeks feels long. It's not. It's the difference between landing in the inbox and training spam filters to hate your domain.

**AI-generated copy needs a human creative layer.** The agents are excellent at scale and iteration. They're genuinely weak at original creative hooks. The best-performing ads I've run had headlines written by me — not generated, not suggested, but actually crafted based on understanding the audience emotionally. I use AI to generate 20 variations of a human-written hook, test them all, and let data pick the winner. That hybrid beats pure AI generation by a consistent margin.

Here's my prediction: the marketing teams that thrive as this technology matures won't be the ones who handed everything over to agents. They'll be the ones who identified which 20% of their work genuinely requires human judgment — positioning, narrative, emotional resonance — and protected that space while automating everything else around it.

---

## What the Actual Numbers Look Like After Three Months

After three months running this in production, here's what I can honestly report.

**LinkedIn-to-Email Pipeline:**
- Average profiles scraped per week: ~200
- After enrichment and verification: ~140 contacts (70% yield)
- Cold email open rate: 38% (industry benchmark: 21%)
- Reply rate: 7.2% (industry benchmark: 2-3%)
- Time investment: 30 minutes per week for review versus 8+ hours previously

**Facebook Ad Performance:**
- Time to launch a 50-variant test: 8 minutes versus 2+ days manual
- Underperformer identification: 48-72 hours with continuous optimization versus 1-2 weeks of weekly manual review
- Cost per click reduction over 3 weeks of optimization: approximately 31%
- Ad spend wasted on confirmed losers: down sharply because they get paused before burning through budget

**Podcast Outreach (added most recently):**
- First batch: outreach to 45 podcast hosts, automated via Refonic email scraping and Instantly sequencing
- Booked interview calls in 3 weeks: 8
- Prior manual rate: 2-3 per month at significant effort

These aren't theoretical improvements. They're real, measurable, with honest context attached. The LinkedIn pipeline alone saves approximately 6 hours per week. Over a month, that's 24 hours I'm redirecting to actual strategic work — and to building more automation.

The ad optimization is harder to express as a single number because it compounds. But cutting underperformers 72 hours earlier than I would manually saves 15-25% of ad spend that would otherwise have gone to proven losers. Across an active Facebook campaign budget, that's meaningful money.

---

## Where to Start If You're Building This for the First Time

Pick one workflow, not all of them. The LinkedIn pipeline is the right starting point because the feedback loop is fast and the cost of mistakes is zero — no real money involved in getting the scraping and enrichment right.

Get that end-to-end before touching Facebook ads. Seriously. The LinkedIn pipeline will teach you everything you need to know about webhook handling, data enrichment APIs, and rate limiting before you're working with a system that can actually spend your money when something goes wrong.

**What you need before starting:**
- Claude Code with API access (claude-opus-4-6 for the orchestration layer)
- Phantom Buster account (their basic plan is sufficient to start)
- Apollo API key (starter plan covers the volume you need initially)
- Million Verifier credits (buy in bulk, they're cheap)
- Instantly AI account (allow 3 full weeks for warm-up before sending)
- Railway account for deployment

The total monthly cost for a solo operator running this at moderate volume: approximately $180-220 across all platforms. The time savings justifies this by week two.

Domain knowledge matters more than coding ability in this setup. My advantage isn't being a better programmer than most marketers — it's understanding both the marketing workflow and the technical implementation deeply enough to spot failure modes before they hit production. If you're a strong marketer with limited coding experience, describe what you want to build to Claude Code in plain language and let it write the integration scripts. That's a viable approach, with the caveat that you'll need to understand enough to review what it produces.

---

The thing nobody says plainly about this kind of automation is that it doesn't replace judgment — it amplifies it. Bad marketing strategy, automated at scale, fails faster and more expensively than bad marketing done manually. Agents are multipliers. They multiply whatever you put in.

Get the strategy right first. Understand your audience, your offer, and what actually makes your outreach different from everything else landing in someone's inbox. Then build the system around that understanding.

Once the understanding is solid and the system is running, you'll experience something genuinely strange: watching marketing work happen without being involved in it. Your calendar opens up. Your focus shifts from execution to direction. And you start wondering what else can be handed off.

That question — what else can I automate — is the most productive question in a solo founder's toolkit right now.

What's one marketing task you did manually this week that didn't actually require your judgment? Map it out. Look at each step. Then ask yourself which ones you're doing by hand that could run while you sleep.

The infrastructure to hand those steps off already exists. The only question is whether you're going to build the bridge.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
