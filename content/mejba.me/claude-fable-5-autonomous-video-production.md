**BRAND:** mejba.me
**TITLE:** Claude Fable 5 Made a Full Video From One Prompt
**META TITLE:** Claude Fable 5: One-Prompt Autonomous AI Video
**SLUG:** claude-fable-5-autonomous-video-production
**PRIMARY KEYWORD:** Claude Fable 5
**META DESCRIPTION:** I gave Claude Fable 5 one prompt and got a finished YouTube video — script, voice, avatar, editing. ~380K tokens, ~1 hour, no filming. Here's the full run.
**TAGS:** Claude Fable 5, AI Video Generation, Mythos Class, Autonomous AI, Claude Code

---

# Claude Fable 5 Made a Full Video From One Prompt

I wrote one prompt. I did not write a script. I did not record a single second of audio. I did not open HeyGen, ElevenLabs, or a video editor. I did not touch ffmpeg. About an hour later, a finished, upload-ready YouTube video was sitting in my output folder — narrated in my own voice, fronted by an avatar that moved like me, with motion graphics that hit on the exact word they were supposed to land on.

The model that did all of it was **Claude Fable 5**, Anthropic's new Mythos-class model that went generally available on June 9, 2026. And the thing I keep coming back to, three days later, is not how good the video looks. It's that the entire production pipeline — the part I used to orchestrate by hand across three or four tools — collapsed into a single instruction handed to one model that just... ran the whole thing.

I have been building AI video pipelines for months. I know exactly how much glue code, chunking logic, and babysitting it normally takes. So when I say this run was different, I'm not reacting to a demo reel. I'm reacting to watching a model do, autonomously, the orchestration work I had previously written by hand and described in detail in my [HeyGen + ElevenLabs + Claude Code video pipeline build](/ai-video-production-pipeline-heygen-elevenlabs). This post is the honest account of that run — what it cost, what it actually did, where it's not deterministic, and where I'd still keep my hand on the wheel.

## What "Mythos Class" Actually Means Now

Let me anchor the model before the video, because the capability tier is the whole story.

For most of the last year, "Mythos" was a rumor and then a leak. I wrote about it back when Anthropic accidentally exposed the model under the code name Capabra — the full background is in my [Claude Mythos leak breakdown](/anthropic-claude-mythos-leak). At the time, Mythos was a tier *above* Opus that almost nobody could touch. It was restricted to a small set of vetted security partners and cyberdefenders.

That changed on June 9. Anthropic shipped **Claude Fable 5** as the publicly available Mythos-class model — the same underlying capability tier, with safety scaffolding in place for general release. (The fully unlocked variant, Claude Mythos 5, stays restricted to a narrow group of infrastructure and security partners.) Per Anthropic's launch, Fable 5 is available today on the API and on consumption-based Enterprise plans, and it was rolled into Pro, Max, and Team plans at no extra cost through June 22.

Here's what the tier buys you, and why it matters for a job as long-horizon as making a video:

- **Coding at a scale I haven't seen before.** Anthropic's announcement describes Fable 5 running a full migration across a roughly **50-million-line Ruby codebase in a single day** — work the company frames as normally taking a team two-plus months. I can't reproduce that claim personally; I don't have a 50M-line Ruby monolith lying around. But it tells you the kind of sustained, multi-step work the model is built to hold together without losing the thread.
- **Vision that reconstructs, not just describes.** It can rebuild web app source code from screenshots, and Anthropic showed it playing Pokémon Fire Red from raw screen pixels with no navigation helpers wired in.
- **Long-horizon memory.** This is the one that actually makes the video possible. Fable 5 uses **file-based memory** — it writes itself notes, reads them back, and keeps its place across a long task. Anthropic reports it reaches the final act of Slay the Spire about three times more often than its predecessor, Opus 4.8. A video production run is exactly that kind of marathon: dozens of dependent steps where forgetting step 4 ruins step 19.

That memory mechanism deserves more than a bullet, because it's the difference between "neat demo" and "this actually finished." A full video run does not fit in one context window — not even a 1M-token one. By the time the model is rendering motion graphics in stage four, the script it wrote in stage one and the chunking decisions it made in stage two are thousands of steps behind it. Stuff that far back gets compressed, summarized, or pushed out of the active window entirely. With a normal model, that's where the wheels come off: it forgets that the stat card was supposed to land on the word "fifty million," or it re-derives a chunk length it already settled, and the timing drifts.

Fable 5 sidesteps that by treating the filesystem as memory instead of relying on the context window alone. Watching its logs, I could see it literally writing notes between stages — a running file of decisions like which audio chunks mapped to which avatar clips, what the target durations were, which graphic fired on which timestamp. Then, stages later, it read those notes back instead of trying to remember. That's the same trick a human editor uses: you don't hold the entire edit decision list in your head, you keep a project file and consult it. The model externalized its own state so the long horizon stopped being a memory problem and became a lookup problem. For anything multi-stage — not just video, but any agentic job that runs longer than a single context window can hold — that's the capability that matters more than raw token throughput.

The price tag is the part you cannot ignore. Fable 5 runs roughly **$10 per million input tokens and $50 per million output tokens** — less than half what the Mythos Preview cost, but still the most expensive frontier model in general circulation right now. Hold that number. It becomes the entire "should you do this" conversation by the end of this post.

So that's the engine. Now the run.

## The One Prompt That Replaced My Whole Pipeline

The setup I gave it was deliberately lazy, on purpose. I wanted to see how much I could *not* do.

I pointed Fable 5 at Anthropic's own Fable 5 launch announcement and asked it to make a YouTube video explaining the release — in my voice, my format, upload-ready. I gave it a voice playbook (more on that below), my brand context, and one hard rule I'll keep repeating: **do not stop until you are 100% confident the output is high quality.** That sentence did more work than any other line in the prompt.

Then I let it go.

What it did next is the part that reorders how I think about "AI video generation." It didn't generate a video the way a text-to-video model does — there was no single magic render. Instead it ran an actual *production*, the same five-stage pipeline I'd normally drive by hand, except I wasn't driving. Here's the sequence it executed.

### 1. It wrote the script — and fact-checked itself first

Before writing a word of narration, Fable 5 read Anthropic's full announcement and fact-checked the claims against it. Then it wrote the script in my voice — not a generic "AI explainer" voice, but mine, pulled from a **voice playbook I'd assembled from transcripts of my prior videos**. Sentence rhythm, the way I open with a number, my habit of saying "here's the part that matters" — it picked all of that up.

This is the first place the Mythos tier earns its keep. **Video script generation** that holds a consistent voice across a long piece is a long-horizon task in disguise. Weaker models drift — paragraph three sounds like a different writer than paragraph eight. Fable 5 stayed in character the whole way because it was literally taking notes on its own style decisions in file-based memory as it went.

### 2. It synthesized the voice in chunks to dodge drift

The finished script went to **ElevenLabs** for audio through my voice clone. And here's a detail I find quietly impressive: it didn't dump the whole script into one API call. It segmented the narration into chunks **just under a minute each** before sending them.

That's not arbitrary. ElevenLabs' instant voice clones get less consistent the further the request drifts from the reference material and the longer the continuous generation runs — cadence flattens, artifacts creep in. I learned this the hard way months ago and hard-coded ~45–60 second chunking into my manual pipeline. Fable 5 arrived at the same constraint on its own and chunked to roughly the same length. It understood the failure mode of the downstream tool without me spelling it out.

### 3. It animated the avatar with HeyGen Avatar 5

Each audio chunk went to **HeyGen**, rendered with the newest **Avatar 5** motion engine. Avatar 5 builds a photorealistic twin from as little as a 15-second clip and is the version that finally fixed the "plastic" look — more natural head and body movement, tighter lip sync, real micro-expressions.

There was one genuinely interesting bit of autonomy here. Earlier in my own testing, Avatar 5 wasn't fully exposed through HeyGen's public API, so I had to automate a browser with Playwright to reach it. Fable 5 handled the equivalent gap by driving browser automation itself when the API path was limited — and now that HeyGen supports the newer engine more directly, it switched to the direct call. It adapted to the integration surface instead of failing on it.

### 4. It edited and built every motion graphic in code

This is where it stopped being "stitch some clips" and became actual editing.

Fable 5 used **ffmpeg** to stitch the avatar clips together. But the graphics are the part I want you to sit with: it built every motion graphic as **coded HTML animations using GSAP** (GreenSock — it kept calling it "Gap" in its own logs, which made me laugh) inside **Hyperframes**, HeyGen's open-source render framework. If you want the deep version of that specific layer, I documented building it from a single prompt in my [Hyperframes one-prompt motion graphics walkthrough](/ai-video-creation-hyperframes-claude-code) — Fable 5 did the same thing here, but as one stage inside a larger autonomous job.

The graphics weren't decorative. They were **synchronized to the spoken words** — a stat card appearing the instant the narration said the number, a label landing on the term as I "said" it. To get that right, the model rendered frames, looked at them, caught its own timing and rendering errors, and corrected until it cleared the quality bar. It was doing visual QA on its own output.

### 5. It verified the whole thing with multi-agent QA

The last stage was a **dynamic, multi-agent workflow**: spin up agents to take screenshots of the rendered output, visually verify the content matched the script, check the graphics fired on time, and confirm nothing broke. Only after that pass did it call the video done.

The output was a fully vetted, upload-ready YouTube video. I watched it end to end before touching anything. It was genuinely good.

## What It Actually Cost: The Token and Dollar Receipt

I promised you the price would come back. Here it is, with real numbers from the run.

The whole production — script through finished video — consumed roughly **380,000 tokens**. The full workflow ran in about **one hour**. And in terms of plan budget, it ate about **40% of a $200/month plan** in that single run.

Let me translate that, because token counts are abstract and dollars aren't. At Fable 5's rate of $10 per million input and $50 per million output, a one-hour autonomous video run is not a rounding error. If you're producing one flagship video a week, the model cost is real but defensible. If you're trying to push out daily volume this way, you will feel it fast — and you'll want to think hard about which stages truly need Mythos-class reasoning and which could run on a cheaper model.

Here's the honest framing I landed on:

- **Quick win:** The labor savings are immediate and enormous. The human time after "go" was effectively zero. No filming, no editing session, no revision round-trips. That alone changes the economics of any content operation that ships video regularly.
- **The real cost:** It's compute, not labor. You've traded an editor's invoice and four hours of your own filming time for a token bill and a model that's the priciest on the market. For high-value or high-volume professional work, that trade is a steal. For a hobby channel posting occasionally, it's overkill — you'd burn plan budget faster than you'd recoup it.

Let me make the per-video number concrete, because "40% of a plan" only means something if you do the multiplication. One run was ~380K tokens and ~40% of a $200/month plan, which puts the *effective* cost of that single video somewhere near $80 of plan budget. Run it on raw API pricing instead and the shape is similar: 380K tokens skewed toward output at $10/M in and $50/M out lands in the same ballpark of roughly $15–$25 in pure inference if most of those tokens are output, before you count the retries and the QA passes that don't always show in a clean estimate. Either way, call it tens of dollars per finished video, not cents.

Now scale it. One flagship video a week is about 1.6M tokens a month — comfortably inside a $200 plan with room to spare, and a no-brainer against what an editor charges. Push to one video a day and you're at ~11.4M tokens a month from this stage alone; you've blown through a single $200 plan roughly two runs in and you're paying API rates for the rest. At three videos a day for a content team, the model bill stops being a footnote and becomes a line item someone has to defend in a budget meeting. That's the real decision boundary: Fable 5 end-to-end is gorgeous for low-volume, high-stakes output, and it gets expensive fast the moment "autonomous" turns into "always on."

If you want a setup that's optimized to keep that cost down — choosing where to spend frontier-model reasoning and where to drop to something cheaper — that's exactly the kind of pipeline architecture work I take on through [my Fiverr](https://www.fiverr.com/s/EgxYmWD). The orchestration logic is where the money is saved or wasted.

That's the math. Now the part the launch demos won't tell you.

## The Real Talk: Where This Breaks and What I Don't Trust Yet

I'd be doing you a disservice if I stopped at "it made a video and it was great." Three things from this run are worth your skepticism.

**It is not deterministic, and that's a bigger deal than it sounds.** I re-ran the same prompt to see if I'd get the same video. I didn't. Not even close in places. Anthropic's own framing acknowledges this: re-running an identical prompt may not reproduce the output, partly because the system leans on pre-existing skills and workflows that aren't fully exposed or pinned. For a creative one-off, fine. For a *repeatable production process* you want to run a hundred times with consistent results, non-determinism is a real operational risk. You cannot yet treat this like a deterministic build script. You treat it like a very fast, very capable contractor who does excellent work slightly differently each time.

**"Autonomous" still needs a human quality gate.** The output cleared the model's own QA. It still got *my* review before I'd have published it. The multi-agent verification is genuinely good — it catches broken renders and timing misses — but "the model is 100% confident" and "this represents my brand the way I want" are not the same standard. That confidence threshold in the prompt is what produced professional results; it's not a substitute for the last human look.

**The chunking and tooling knowledge is baked in, which cuts both ways.** Part of why this worked so well is that the model already "knew" things like the ElevenLabs drift problem and Hyperframes' render flow. That's great until your tools change. When ElevenLabs or HeyGen ship a breaking update, a baked-in workflow can confidently do the old thing. The flip side: the methodology isn't locked to Fable 5. The same pipeline approach adapts down to a model like Sonnet for the stages that don't need frontier reasoning — which is exactly how you'd tame the cost.

**What I actually had to babysit.** "Autonomous" reads cleaner in a headline than it felt in practice. The non-determinism wasn't an abstract caveat — it bit me. My second run wandered off: it restructured the script into a different order and re-timed graphics that had landed perfectly the first time, so the polished version from run one wasn't something I could simply regenerate on demand. I had to keep the good output, not trust the process to recreate it. The HeyGen gap was the other rough edge. Because Avatar 5 wasn't fully reachable through the public API in my earlier testing, the model fell back to driving a browser to get the render done — and browser automation against a live web app is exactly the brittle, slow, "did the button move?" layer you don't want in an unattended pipeline. It worked, but it's the kind of thing that breaks silently when the site's markup shifts, and it's why I watched that stage instead of walking away. Native support landing for the newer engine is what let it switch to the cleaner direct call, and that's a reminder of how much of this "magic" is resting on integration surfaces the model doesn't control.

One more honest note: the single biggest lever in the whole run wasn't the model's raw power. It was **purpose-driven prompt engineering** — giving it clear context, a real voice playbook, and an explicit, non-negotiable quality standard. The "stop only when 100% confident of high quality" instruction is doing heavy lifting. Hand Fable 5 a vague prompt and you'll get a vague, expensive video. The model rewards precision in proportion to its price.

## So Should You Actually Do This?

Here's my straight read after living with the output for a few days.

If you ship video at professional volume — course lessons, product explainers, a content channel with a real cadence — **Claude Fable 5 changes your production math today.** Not next year. The bottleneck I'd been chipping at for months, where a human still had to orchestrate the tools even after the AI did the hard parts, just moved. One model now holds the whole pipeline.

If you make the occasional video for fun, this is a magnificent piece of overkill. Use the cheaper, hand-built version instead — and frankly, keep filming the stuff that's genuinely *you*. I still record my personal YouTube videos myself. This pipeline is for scalable content, not for the things where my actual unrehearsed presence is the point.

The frontier this run actually marks isn't "AI can make videos." We've had mediocre text-to-video for two years. The frontier is **one prompt, end to end, fully autonomous, professional output** — script, voice, avatar, editing, and self-QA, with a human writing nothing but the brief. That's new. And it points somewhere larger: increasingly autonomous AI multimedia creation where the human role compresses to *direction and judgment*, and everything between intent and finished file gets handled by a model that takes its own notes and checks its own work.

## Frequently Asked Questions

### What is Claude Fable 5?

Claude Fable 5 is Anthropic's first publicly available Mythos-class model, released June 9, 2026 — a capability tier above Claude Opus 4.8. It features a 1M-token context window, file-based long-horizon memory, and strong vision and coding performance, priced at $10 per million input tokens and $50 per million output tokens.

### Can Claude Fable 5 really make a full video from one prompt?

Yes — in my test it produced a complete, upload-ready YouTube video from a single prompt by orchestrating its own pipeline: script generation, ElevenLabs voice synthesis, HeyGen Avatar 5 animation, ffmpeg editing, GSAP motion graphics in Hyperframes, and multi-agent visual QA. The output is not deterministic, so re-running the same prompt won't reproduce the same video.

### How much does a Claude Fable 5 video cost to produce?

My one-hour run used roughly 380,000 tokens and consumed about 40% of a $200/month plan. At $10/$50 per million input/output tokens, it's cost-effective for high-value or high-volume professional video, but expensive for occasional or hobby use. The savings come from eliminating filming and editing labor, not from cheap compute.

### Does this video pipeline only work with Claude Fable 5?

No. The methodology adapts to other models, including Claude Sonnet, for stages that don't require frontier-level reasoning. For the full manual build, see my [HeyGen + ElevenLabs + Claude Code pipeline guide](/ai-video-production-pipeline-heygen-elevenlabs) above — Fable 5 automates the orchestration you'd otherwise wire together yourself.

### Why isn't the Claude Fable 5 video output deterministic?

Re-running the same prompt produces different videos because the model relies on pre-existing skills and workflows that aren't fully pinned or exposed, plus inherent generation variance. For repeatable production at scale, that non-determinism is an operational risk you have to plan around rather than ignore.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
