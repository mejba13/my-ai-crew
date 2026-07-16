**BRAND:** mejba.me
**TITLE:** Sora 2 + Arcads + Claude Code: Cloning UGC Ads on Autopilot
**META TITLE:** Sora 2 + Arcads + Claude Code UGC Automation Guide
**SLUG:** sora-2-arcads-claude-code-ugc-automation
**PRIMARY KEYWORD:** Sora 2 Arcads Claude Code UGC automation
**META DESCRIPTION:** I cloned a winning UGC ad with Sora 2, Arcads, and Claude Code. Real costs, the API setup, where the output breaks, and what 15 seconds buys you.
**TAGS:** AI Video, Claude Code, Sora 2, UGC Automation, Arcads

---

# Sora 2 + Arcads + Claude Code: Cloning UGC Ads on Autopilot

The first AI-cloned ad I ever shipped to a client cost me $7.42 to generate and took 11 minutes from "drop the reference video" to "watch the rendered MP4." The original — a winning TikTok UGC spot one of their competitors had been running for six weeks — would have cost roughly $280 to remake with a human creator on Fiverr, plus three to five days of waiting, two rounds of revisions, and the inevitable "can you reshoot the closing line?" thread on Slack.

The cloned version wasn't perfect. The avatar's left hand drifted unnaturally for about half a second around the 8-second mark. The product close-up lost some texture you'd see in a phone shot. But the *vibe* — the pacing, the script structure, the energy, the framing — was almost a one-to-one match. And it was sitting in a folder ready to A/B test against three other variants Claude Code had spun off the same source material while I made coffee.

That's the workflow I've been quietly running for the last six weeks: a Claude Code agent wired into the Arcads external API, pulling reference videos apart, rebuilding them around a new product, and pushing the rebuild through Sora 2 (or Seedance 2.0, depending on what the scene needs). It is not magic. It breaks in specific, predictable ways. And it changes the unit economics of UGC ad testing so completely that I think most agencies running paid social are going to look very different by the end of 2026.

This is what I learned testing it.

## Why I Stopped Hiring UGC Creators (Mostly)

For two years I'd been running the same loop with every e-commerce client. Find a winning competitor ad. Brief a UGC creator. Wait. Pay $200 to $500 per video. Get the output. Find one thing wrong. Wait again. Burn another week. Test the ad. Either it worked (rare) or it didn't (more common), and we'd start over.

The math was getting hard to defend. According to the [2026 UGC pricing guides](https://designrevision.com/blog/ugc-creator-pricing) I checked while writing this, beginner UGC creators charge $75 to $300 per video, mid-tier sits at $300 to $1,000, and top-tier creators in tech or beauty verticals push $600 to $3,000 per deliverable. Influee's [2026 rate breakdown](https://influee.co/blog/ugc-price) puts the average single video at $212. And those are *base* rates — usage rights add 25 to 150 percent on top, whitelisting tacks on roughly 30 percent per month, and rush delivery is another 25 to 50 percent.

For a client testing four ad variants per week, that's $4,000 to $12,000 a month in creator fees alone. Not counting briefing time. Not counting revision cycles. Not counting the variants we never made because the budget ran out.

The trigger that pushed me to seriously test Arcads + Claude Code wasn't the cost, though. It was the *speed gap*. A winning ad's half-life on TikTok in 2026 is brutal — most creative starts to fatigue inside 10 to 14 days. By the time a human-made variant comes back, the original ad is already cooling off and we're chasing the wrong angle. AI-generated UGC isn't replacing human creators because it's better. It's replacing human creators because it's *fast enough to keep up with the algorithm*. That's a different kind of advantage, and most agencies haven't priced it in yet.

## Wait — Sora 2, Seedance 2.0, or "C Dance"? A Quick Name Untangle

Before going further, I have to clear up a name knot that tripped me up for the first week and that I see repeated all over YouTube tutorials: people use "C Dance 2.0," "Seedance 2.0," and "Sora 2" as if they're interchangeable. They aren't.

- **Sora 2** is OpenAI's video model. It's the one most creators reach for when they need a person on camera holding a product, talking, gesturing — the textbook UGC look. Per the [official OpenAI Sora 2 API pricing](https://www.eesel.ai/blog/sora-2-in-the-api-pricing), it's billed at roughly $0.10 to $0.50 per second depending on resolution, which works out to about $1.50 to $7.50 for a 15-second clip.
- **Seedance 2.0** is ByteDance's video model. The [C Dance AI explainer page](https://cdance.ai/what-is-cdance-2-0) lays it out: Seedance 2.0 is the underlying *model*, and "C Dance 2.0" is a *workflow product* built on top of it. Different layer. Different pricing.
- **Arcads** is the orchestration platform. It's not a model — it's a service that gives you 1,000+ pre-cloned avatars, prompt templates, and API endpoints that route requests to whichever underlying model fits the job. Per the [Arcads API documentation](https://intercom.help/arcads/en/articles/10538922-arcads-ai-api-documentation), the platform currently supports Sora 2, Veo 3.1, Kling 3.0, Seedance 2.0, and Nano Banana, all behind a single API.

Why does this matter? Because the model you pick changes the output. Sora 2 produces the most natural human motion — blinks, weight shifts, gesture timing. Seedance 2.0 is better at cinematic camera moves and product hero shots. The whole point of running this through Claude Code is that you don't manually pick the model per scene. The agent reads the reference video, decides which model handles which beat, and routes the requests automatically.

Now, the workflow.

## The Stack: Three Tools, One Pipeline

The pipeline runs on three pieces. Each one has a specific job, and the seams between them are where most public tutorials get vague. I'll be specific.

**Arcads** is the avatar-and-rendering layer. According to their [public pricing](https://www.eesel.ai/blog/arcads-ai-pricing) as of April 2026, the Starter plan is $110/month for 10 videos ($11/video), Creator is $220/month for 20 videos, and Pro is custom-priced with API access, unlimited videos, and actor cloning. The API is the one I care about — the chat UI is fine for one-off ads, but it doesn't scale. For an agency running variant tests at volume, Pro is the only tier that makes sense.

**Claude Code** is the orchestrator. It's what reads the reference video, transcribes the audio, breaks the video into beats, rewrites the script for the new product, decides which Arcads model to call per scene, fires the API requests, polls for completion, and stitches the returned clips into the final cut. The official integration is open-source — the [krusemediallc/arcads-claude-code GitHub repo](https://github.com/krusemediallc/claude-code) ships agent skills, prompting templates, and a Cursor/Claude workspace that handles authentication via `.env`. You install it once and never paste an API key into chat again.

**Sora 2 + Seedance 2.0 (via Arcads)** are the actual generative models. Both are 15-second-per-clip at the time I'm writing this. Sora 2 is for the human-on-camera scenes. Seedance 2.0 is for the cinematic product shots and B-roll. Both are accessed through `POST /v2/videos/generate` on the Arcads external API with a `model` parameter in the JSON body, and both return an asset ID you poll on `GET /v1/assets/{id}` until status flips from `pending` to `generated`.

That's it. Three layers. The trick — and the part nobody tells you in the YouTube walkthroughs — is that the *quality of the prompt* matters more than the model you pick. Arcads' own internal prompt library notes that prompts shorter than 100 words produce vague output and prompts longer than 260 words overwhelm the model. The sweet spot is 150 to 200 words, and Claude Code is what gets you there reliably.

<!-- IMAGE: [Diagram showing the three-layer pipeline: Reference Video → Claude Code (transcribe/segment/rewrite) → Arcads API (Sora 2 / Seedance 2.0) → Stitched Output]. Alt text: "Sora 2 Arcads Claude Code UGC automation pipeline diagram with three layers". Caption: "The three-layer pipeline. Claude Code does the orchestration; Arcads handles models and rendering." -->

## What Happens When You Drop a Reference Video Into Claude Code

This is where the workflow earns its keep. I'll walk through the exact flow Claude Code runs end-to-end, because every step has a failure mode I tripped over at least once.

**Step 1: Source the winning video.** I pull from the [TikTok Creative Center](https://ads.tiktok.com/business/creativecenter) (free, public, sortable by engagement) or the [Meta Ad Library](https://www.facebook.com/ads/library/). The trick is to filter by ads that have been running for 14+ days — those are the ones the algorithm has validated. A 3-day-old ad with high views might just be a budget spike. A 14-day-old ad with steady spend is a winner. I also collect the brand's product images from their site, because Claude Code uses them as visual reference when generating the swap.

**Step 2: Drag everything into Claude Code.** The reference MP4 plus the product images go into the project folder. Claude Code's video skill kicks in — it extracts frames at 1-second intervals, runs the audio through Whisper for transcription, and tags each frame with what's visible (avatar, product, background, on-screen text). The first time you run this, plan for 30-60 seconds of processing on a 15-30 second clip.

**Step 3: Beat extraction.** Claude breaks the video into beats. For a typical UGC unboxing, you'll see something like:

- 0–2s: hook (avatar holds product, looks at camera)
- 3–5s: reveal (product close-up or unbox)
- 6–10s: feature talk (avatar speaks, B-roll cuts)
- 11–15s: CTA (avatar gestures + on-screen text)

This breakdown is the spine of everything that follows. If the beat structure is wrong here, the cloned ad feels off no matter how good the individual clips look.

**Step 4: Script rewrite.** Claude takes the original transcript, my product details (passed in as a `product.md` brief in the project folder), and rewrites the script to match the original's *energy* but pitch the new product. This is where small models fail and Claude Opus shines — getting the rhythm right, keeping the hook punchy, not sliding into generic ad copy. I tested this with three smaller models first; only the full Opus model produced scripts I'd actually run.

**Step 5: Per-beat model routing.** For each beat, Claude decides: human-on-camera (Sora 2), cinematic product shot (Seedance 2.0), screen recording (a third path I'll cover below). It then composes a 150-200 word prompt for each beat, pulling visual descriptors from the original frames, and posts each one to `POST /v2/videos/generate`.

**Step 6: Polling and retry logic.** Each Arcads job returns an asset ID. Claude Code polls `GET /v1/assets/{id}` every 15 seconds. If status is `pending` after 4 minutes, the job is logged as slow but not killed. If status flips to `failed`, Claude regenerates the prompt with a slight variation (different camera angle phrasing, simplified motion description) and resubmits. About 1 in 8 jobs failed on my first weekend of testing — that retry loop is what makes the pipeline reliable instead of theoretical.

**Step 7: Stitching.** When all beats land, Claude pulls them into FFmpeg with crossfades at the beat boundaries — typically 200ms transitions, sometimes hard cuts depending on the source. Background music is reattached from the original (or a royalty-free swap if the original is copyrighted), and a final audio normalization pass evens out the levels.

The output drops in `/output/[product-name]-[timestamp].mp4`. For a 15-second ad, the whole pipeline runs in 8-12 minutes. For a 30-second ad (two stitched 15-second clips), figure 18-25 minutes. Most of that is waiting on Arcads' render queue, not Claude Code's compute.

## The 15-Second Wall and the Stitching Workaround

Here's the constraint that defines the entire workflow: every model on Arcads' platform — Sora 2 included — caps at 15 seconds per clip. That's not a UI limitation. That's how the underlying models work. Coherence over longer durations is still an unsolved problem in 2026, and any platform claiming "60-second AI UGC" is either stitching behind the scenes or producing visibly degraded output.

Stitching is fine *if you do it right*. Wrong-stitched videos look like jump cuts. Right-stitched videos feel continuous.

The trick I landed on after a dozen attempts: the avatar in clip 2 must be in the same physical position as the avatar at the *end* of clip 1. Same room. Same lighting. Same outfit. Same hand position if possible. Claude Code handles this by passing the last frame of clip 1 into Arcads as the *starting reference* for clip 2 — the platform supports this through the `reference_image` parameter in the generate endpoint. The avatar in clip 2 then "starts" from where clip 1 left off, and the splice at the 15-second mark becomes invisible.

For a 30-second cloned ad, this means Claude Code generates clip 1 first, waits for it to render, extracts the final frame, and only *then* fires off the request for clip 2. Sequential, not parallel. It's slower, but it's the only way I've found to make stitched UGC look like a single take.

This is also where I'd stop most readers and say: if you ever need a 60-second ad, don't do it in AI yet. Two stitched clips work. Four don't. The compounding drift is too visible by clip 3, and even with reference framing the avatar starts to feel like a different person. Use AI for the 15-30 second formats. Use humans for anything longer.

## Cost: Real Numbers From Six Weeks of Use

Let me show you what the math actually looked like across six weeks of running this for one e-commerce client (kitchen products) and one SaaS client (a productivity app).

For the e-commerce client, I cloned 24 winning competitor ads in six weeks. Twelve at 15 seconds, twelve at 30 seconds (stitched). Total Arcads cost on the Pro plan: roughly $580 in credits — call it $24 per finished ad on average, including failed retries. Claude Code time, mostly idle polling, ran the API around $40 in tokens across the same period. Total: $620 for 24 ads.

The same volume from a UGC creator on the [JoinBrands 2026 rate guide](https://joinbrands.com/blog/ugc-creator-rates/) at the median $200 per video would have run $4,800. Plus usage rights — call it another 30 percent — bringing the human comparison to roughly $6,240. The AI variant came in at about 10 percent of that.

For the SaaS client, the math was tighter. SaaS UGC is harder — screen recordings and split-screen interaction don't render as naturally as a person holding a physical product, and I'll be honest, two of the eight ads we shipped underperformed visibly compared to a human-made baseline. Pulling out those two failures, the per-ad cost was around $31, which still beats human creators by roughly 7x — but the *quality gap* was wider than for physical-product ads. The rule of thumb I've landed on: AI UGC is excellent for products you can hold, decent for products you can wear, mediocre for products that exist on a screen.

If you're looking at end-to-end automation with this kind of multi-tool pipeline for your own brand and want to see how the cost math plays out for an agency running ad tests at volume, I dug deeper into the unit economics in my breakdown of [the AI agency retainer model for 2026](https://www.mejba.me/ai-agency-retainer-model-2026-claude-code).

## What Still Breaks (And Why I Don't Care)

Six weeks in, I have a clear picture of where this falls short. None of it has changed my mind about using it — but pretending these issues don't exist is how YouTube tutorials lose people.

**Hands and props.** Avatars holding products is Arcads' marquee feature, and most of the time it works. But maybe 1 in 10 generations, the hand will phase through the product, the fingers will deform mid-motion, or the avatar's grip will look anatomically wrong. Claude Code's retry logic catches the worst cases — I have it flag any clip where Whisper transcription fails on the audio (often a sign the model produced something glitchy) — but you still need a human review pass before anything ships. Plan 5-10 minutes of QA per finished ad.

**Eye contact drift.** On 15-second clips this is rare. On 30-second stitched clips, the avatar's eye line will sometimes shift slightly between the two halves. Most people won't consciously notice. About 5 percent will subconsciously, and they'll describe the ad as "feeling off." There's no fix yet. You either accept it or you stay at 15 seconds.

**Green screen / chroma key.** Not native. If you need the avatar against a transparent background for compositing, you have to run it through Runway's background removal as a post-step. Claude Code can automate this, but it's another API call and another point of failure. For most UGC, you don't need it — but if you're cutting the avatar into a more complex composition, factor in the extra pipeline complexity.

**Brand voice and accents.** Out of the box, the voice options on Arcads skew American-English neutral. For clients with strong brand voices (specific cadence, regional accent, signature phrases), I had to either use ElevenLabs voice cloning as an external step and dub it in (which Claude Code handles, but it's another tool) or pick from Arcads' "actor cloning" tier on the Pro plan — which lets you train a custom avatar on your own footage but adds significant setup time. Default avatars are great for generic brands. Strong-voice brands need the custom path.

**Edge cases that just don't render well.** Anything involving complex hand-prop interaction (twisting open a jar, peeling a label, anything with two-handed coordination) is currently rough. Anything involving small text on the product (legible serial numbers, fine print) blurs. Anything involving liquids being poured looks fluid-dynamic-broken about a third of the time.

The reason none of this kills the workflow: I'm not generating *finished* ads. I'm generating *testable variants*. If 8 out of 10 variants look broadcast-quality and 2 need a reshoot or a salvage edit, that's still a 10x improvement over the human-creator baseline where every variant takes a week to produce and revise. Volume kills perfectionism. The economics tilt toward "ship more, kill more, learn faster."

## Setting Up the Pipeline: The Concrete Steps

If you want to actually run this, here's the unvarnished setup. I'm going to assume you already have Claude Code installed and an Arcads Pro account (or you're willing to test on Starter for the first 10 videos before deciding).

**1. Clone the integration repo.**

```bash
git clone https://github.com/krusemediallc/arcads-claude-code.git
cd arcads-claude-code
```

The repo includes the agent skills, prompt library, and master context template that Claude Code reads to understand the Arcads API contract.

**2. Set up authentication.** Create a `.env` file in the project root:

```env
ARCADS_API_KEY=your_key_here
ARCADS_BASE_URL=https://external-api.arcads.ai
```

The key comes from your Arcads dashboard under API settings. Per the [Arcads API documentation](https://intercom.help/arcads/en/articles/10538922-arcads-ai-api-documentation), authentication is HTTP Basic — the API key is used as the Basic password. The Claude Code integration handles this automatically once `.env` is in place. You never paste the key into chat.

**3. Configure your master context.** Copy `MASTER_CONTEXT.template.md` to `MASTER_CONTEXT.md` and fill in your brand details — product name, key features, target audience, brand voice notes, prohibited claims, preferred avatar profiles. This is the file Claude Code reads on every generation to keep output on-brand. Skip this and your output will drift toward generic UGC voice.

**4. Test the round-trip.** Drop a reference video into the project folder and run:

```
@aria clone this UGC video for the product described in product.md, output 15s
```

(Substitute your agent name if you've renamed the workspace.)

The first run takes 8-15 minutes. Watch the terminal — Claude Code will print each step as it goes (transcribe → segment → rewrite → generate → poll → stitch). If anything fails, the most common cause is the reference video being too long (keep source clips under 60 seconds) or the product brief being too vague (specific product details and one differentiating feature is the minimum).

**5. Build your variant matrix.** Once one clone works, you don't generate one ad — you generate *six*. Same source video, six different product angles. Claude Code can fan this out in parallel:

```
@aria generate 6 variants of this clone with different hooks and CTAs
```

Each variant uses the same beat structure but different opening lines, different feature emphasis, different CTAs. This is what unlocks rapid creative testing. You're not making *an* ad — you're populating a test matrix in an afternoon.

If you'd rather have someone build this pipeline end-to-end for your brand instead of wiring it up yourself, I take on agency retainers for exactly this kind of automation work — you can see the kind of builds I've shipped at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## What This Means for the Next Two Years

Here's the part I sit with. The reason this workflow matters isn't that AI UGC is *replacing* human creators. The data still says it isn't. The [PPC.io 2026 pricing analysis](https://ppc.io/blog/ugc-pricing) cites that 80% of brands still prefer human creators for authentic content, and that's consistent with what I see — for hero ads, for influencer-led campaigns, for anything where the creator's personal credibility is part of the pitch, humans win.

But for *variant testing* — the unsexy work of running 20 versions of a hook to find the one the algorithm rewards — AI UGC has already passed the threshold where it's the rational choice. The cost is roughly 10 percent of human creators. The speed is roughly 100x. The quality, for testable variants, is around 90 percent of the human baseline. That math doesn't reverse.

What I think changes in the next 12-18 months: agencies stop billing per-asset for UGC. The model shifts to either (a) a creative-strategy retainer where the agency owns the entire variant testing process and bills monthly, or (b) a hybrid where humans handle the hero creative and AI handles the testing pipeline. The "$200 per video" UGC creator gig is going to compress into either cheaper (race-to-the-bottom Fiverr work) or more expensive (premium hero creators with audience reach the AI can't replicate). The middle disappears.

I don't think this is bad news for human creators. The good ones — the ones with genuine on-camera presence, brand alignment, and audience pull — are about to get *more* valuable, not less, because the demand for hero creative scales with the volume of variant testing. AI doesn't kill the creator economy. It bifurcates it.

## What to Try This Week

If you take one thing from this, take this: pick a single competitor ad you've been wanting to clone, install the Arcads + Claude Code integration tonight, and run it once before bed. Don't try to build the variant matrix. Don't try to wire it into your agency stack. Just generate one ad. Watch the pipeline run. See where it stumbles. Decide whether the output is good enough for your specific use case before deciding whether the workflow is worth scaling.

Most of what I've written here came out of that first weekend test. The nine-tenths I cut from this article was speculation about what *should* work. The tenth I kept is what *did*. Run the test. Make your own version of this article. The math is moving fast enough that what's true in May 2026 might be obsolete by August.

The reference video I started with — that competitor's six-week-old TikTok winner — is now a folder of 14 cloned variants on my client's hard drive. Three are running paid. Two are outperforming the original. The other nine are dead and buried, killed in the first 24 hours of testing because the algorithm said no.

That feedback loop is the entire point. It used to take a month. Now it takes a Tuesday.

## Frequently Asked Questions

### How much does it cost to clone a UGC ad with Sora 2 and Arcads?

Cloning a single 15-second UGC ad with the Sora 2 + Arcads + Claude Code pipeline costs roughly $11 to $24 per finished video, depending on retries and whether you're on the Starter or Pro Arcads plan. Compare that to the [2026 average UGC creator rate of $212 per video](https://influee.co/blog/ugc-price), and the AI route runs about 10 percent of human creator cost. For the deeper cost breakdown, see the Cost section above.

### What's the difference between Sora 2, Seedance 2.0, and C Dance 2.0?

Sora 2 is OpenAI's video model, optimized for natural human motion. Seedance 2.0 is ByteDance's video model, stronger on cinematic camera moves and product shots. "C Dance 2.0" is a workflow product built *on top of* Seedance 2.0 — same underlying model, different UI layer. Arcads can call all of them via a single API. See the name-untangle section above for the full breakdown.

### Can Arcads generate videos longer than 15 seconds?

Not natively — every model on the Arcads platform caps at 15 seconds per clip. To produce longer ads, Claude Code stitches multiple 15-second clips together using the last frame of clip N as the reference image for clip N+1. This works reliably up to 30 seconds. Beyond that, avatar drift becomes visible and quality degrades. The Stitching section above walks through the exact mechanics.

### Is the Arcads API the same as the Sora 2 API directly?

No. The [official OpenAI Sora 2 API](https://www.eesel.ai/blog/sora-2-in-the-api-pricing) is a model-only endpoint billed per-second of generated video. Arcads is an orchestration platform that adds 1,000+ pre-cloned avatars, prompt templates, multi-model routing, and a unified `/v2/videos/generate` endpoint. For UGC specifically, Arcads is faster to ship from because the avatar library is the long pole.

### Where does AI-generated UGC fall short of human creators?

Two-handed prop interactions (twisting jars, peeling labels), small product text legibility, liquid pour physics, and any clip longer than 30 seconds. Eye contact drift on stitched clips affects roughly 5 percent of viewers consciously. For hero creative or influencer-led campaigns where personal credibility matters, human creators still outperform. AI UGC's strength is variant testing volume, not single-asset hero work.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
