**BRAND:** mejba.me
**TITLE:** AI Video Pipeline: HeyGen + 11 Labs + Claude Code
**META TITLE:** AI Video Pipeline: HeyGen + 11 Labs + Claude Code
**SLUG:** ai-video-production-pipeline-heygen-elevenlabs
**PRIMARY KEYWORD:** AI video production pipeline
**SECONDARY KEYWORDS:** HeyGen Avatar 5, ElevenLabs voice cloning, Claude Code video automation
**META DESCRIPTION:** I built an AI video production pipeline with Claude Code, HeyGen Avatar 5, 11 Labs, and Remotion. Here's what $50 and one overnight render gets you.
**TAGS:** AI Video, Claude Code, HeyGen, ElevenLabs, Automation
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly how to stitch Claude Code, HeyGen Avatar 5, 11 Labs, and Remotion into an overnight video production pipeline — and know the specific chunking, cost, and quality trade-offs that make it actually work.

---

# AI Video Pipeline: HeyGen + 11 Labs + Claude Code

The render finished at 3:47 AM. I know because my laptop fan spun down hard enough to wake me up. I stumbled over to the desk, hit play on the output file, and watched a version of myself I hadn't recorded deliver a 9-minute lesson I hadn't spoken. The lip sync was clean. The gestures felt natural. The voice was mine — except it wasn't.

I had gone to bed at 11:30 PM after dropping a script into Google Drive. While I slept, Claude Code chunked the script, pushed every chunk through 11 Labs for voice synthesis, handed the audio off to HeyGen to drive an avatar trained on 15 seconds of my webcam footage, automated around a HeyGen API restriction with Playwright, and stitched the whole thing together in Remotion with on-screen text. Total cost for the finished 10-minute video: about $50. Total human labor after hitting "go": zero.

This is the AI video production pipeline I've been quietly testing for the last two months. It is not a toy. It crosses the uncanny valley cleanly enough that three people I showed the output to asked when I recorded it. And the interesting part isn't the avatar — it's that the bottleneck in video production just moved. Permanently.

## Why I Stopped Filming and Started Orchestrating

For the last two years, every course lesson, explainer, and tutorial I shipped required the same ritual. Set up the camera. Fix the lighting. Record a take. Flub a line. Record again. Hand the footage to an editor. Wait three to five days. Review. Request revisions. Wait two more days. Publish.

The output cost me roughly $300 per finished 10-minute video in editor fees, plus about four hours of my own time for filming and review cycles. For a 40-lesson course, that's $12,000 and a month of calendar time before anyone clicks "enroll."

That math is what pushed me to test this pipeline seriously. I wasn't looking for novelty. I was looking for a way to ship a course's worth of video content in a week instead of a quarter, without the quality dropping to the floor. What I found was stranger and more useful than I expected.

Before I walk you through the setup, there's one thing worth saying upfront: this pipeline is built for scalable content. Course lessons. Internal training. Repurposed blog-to-video. It is not replacing the videos I film for my personal YouTube channel, and I'll explain exactly why in the real-talk section. The tool matters less than knowing when to reach for it.

## The Four Tools and What Each One Actually Does

The pipeline has four components. Every one of them is doing a specific job, and understanding the division of labor is the difference between a workflow that ships and one that collapses the first time a chunk fails silently.

**HeyGen** handles the visual. Their Avatar 5 model — launched in late 2025 and continuously upgraded through the November 2025 release — is what finally dragged AI avatars across the uncanny valley. The model is trained on roughly 10 million facial expression data points and builds a digital twin from as little as 15 seconds of webcam footage. For my setup, I uploaded about 10 GB of existing video of myself talking at different energy levels, because I wanted the avatar to carry my gesture vocabulary, not just my face. According to HeyGen's [Avatar V research page](https://www.heygen.com/research/avatar-v-model), the model now reproduces characteristic head movements, gestural rhythm, and micro-expressions — which matches what I saw in output. One catch: Avatar 5 is capped at 3-minute segments per generation. That constraint drives almost every architectural decision downstream.

**11 Labs** handles the voice. I fed their voice cloning system about two hours of clean audio — podcast recordings, tutorial voiceovers, a few narrated screencasts — far past the 30-minute minimum their docs recommend but comfortably inside the 2+ hour range ElevenLabs calls out for Professional Voice Cloning. The four sliders that matter are speed, stability, similarity, and style exaggeration. I landed on stability around 0.7 and similarity around 0.8 after testing, which lines up almost exactly with what their community considers the sweet spot for presenter voice work. Here's the non-obvious bit: voice quality visibly degrades past about 1 minute of continuous generation. Artifacts creep in. The cadence flattens. So every script gets chunked to 45-60 seconds before it ever hits the API.

**Claude Code** is the orchestration layer. This is where the whole thing lives or dies. Claude Code pulls scripts from Google Drive, splits them at sentence boundaries into 45-60 second chunks, sends each chunk to 11 Labs with my voice and parameter settings baked in, collects the returned audio, hands each audio file to HeyGen with the matching avatar ID, monitors render jobs, downloads outputs, and shoves everything into the right folder for the next stage. It also handles something weirder that I'll get to in a minute — using Playwright to automate a browser workaround because HeyGen hasn't exposed Avatar 5 through their public API yet.

**Remotion** handles the editing. Audio gets transcribed, words sync to on-screen text, clips stitch together at the natural sentence boundaries where they were originally split, and motion graphics and captions get layered in. If you want the deeper mechanics of why videos-as-React-components change everything about programmatic video, I walked through that in my breakdown of [how I build promotional videos with code, not editors](https://www.mejba.me/remotion-video-workflow-claude-code) — that piece pairs well with this one.

That's the stack. Four tools, each doing one thing well, with Claude Code as the connective tissue that makes it operate as a single pipeline instead of four disconnected SaaS products.

## Inside the Pipeline: What Actually Happens Between 11:30 PM and 3:47 AM

Here is the end-to-end flow for a single script. I'll walk it from "Mejba drops a .md file in Drive" to "a rendered MP4 lands in my output folder."

**Step 1: Script ingestion.** I write or edit a lesson script in a Google Doc, format it in markdown, and drop it in a specific Drive folder. That folder has a Claude Code watcher pointed at it. The moment a new file shows up, Claude reads it, normalizes the formatting, strips presenter notes, and saves a clean version locally.

**Step 2: Semantic chunking.** Claude Code splits the script into 45-60 second chunks. The splits happen at sentence boundaries, and Claude specifically avoids breaking mid-thought or mid-example. A chunk ending on "…here's why" with the payoff in the next chunk produces an audible glitch, so the splitter is told to prefer natural pause points — end of a paragraph, end of a numbered step, before a transition word like "but" or "so." This single rule is the difference between a video that feels continuous and one that sounds like it was assembled from cue cards.

**Step 3: Voice synthesis per chunk.** Each chunk goes to 11 Labs with my cloned voice, stability 0.7, similarity 0.8, speed 1.0, style exaggeration low. The audio returns as an MP3. Claude Code times each file — if any chunk comes back over 60 seconds of audio, it flags the chunk for re-splitting. This catch-and-retry loop saved at least one full render from silently degrading halfway through.

**Step 4: Avatar rendering per chunk.** Each audio file goes to HeyGen paired with my avatar ID. HeyGen generates a video clip of the avatar speaking that exact audio. Because each chunk is under 60 seconds, every clip stays comfortably under Avatar 5's 3-minute ceiling. Render time varies, but plan for 2-4x the audio length.

**Step 5: The Playwright workaround.** This is the part that felt slightly criminal the first time I ran it. At time of writing, HeyGen's public API defaults new renders to Avatar 4, not Avatar 5. Avatar 4 is fine. Avatar 5 is the one that crosses the uncanny valley. So Claude Code drives a Playwright browser script that logs into HeyGen, opens each pending render, and clicks through to upgrade it to Avatar 5 before the generation finalizes. It is ugly. It works. HeyGen will eventually expose this through their API — the [November 2025 release notes](https://www.heygen.com/blog/heygen-november-2025-release) already signal heavy Avatar V investment — and this whole step will disappear. Until then, Playwright is the bridge.

**Step 6: Remotion stitching.** All the avatar clips land in a folder. Remotion pulls them in order, runs transcription across the audio track, positions captions and section titles on-screen at the right timestamps, adds transitions between chunks (tiny 200ms crossfades at the sentence boundaries where the splits happened — you literally cannot see them), and renders the final composite MP4.

**Step 7: Delivery.** Final video drops into the output folder. Claude Code tags it with the script name, writes a summary of the render job (chunk count, total runtime, any retries), and — if I've set it up — posts a Slack message saying the render is ready.

Seven steps. Zero human intervention between steps 1 and 7. I start the pipeline before bed, and breakfast comes with a finished video.

## The Single Rule That Saves the Whole Pipeline

If I could go back and tell myself one thing before the first failed overnight run, it would be this: the entire quality ceiling of the pipeline is set by how well you chunk the script.

Not by the avatar quality. Not by the voice model. Not by the orchestration code. By the chunking.

Chunks that break mid-thought produce audible discontinuities. Chunks that run over 60 seconds blow up 11 Labs' quality. Chunks that start with a conjunction ("But here's the thing…") lose their contextual pacing and land flat. I spent a full afternoon tuning the chunker prompt before I got consistent overnight output. The final version treats the splitter as a mini-editor: it has to produce chunks that can stand alone as deliverable sentences while still flowing together when played back-to-back.

If you're building this pipeline, budget more time for the chunker than you think. It is the thing that separates "huh, that's impressive" from "wait, you didn't film this?"

## What It Actually Costs to Run This

Here's the monthly math for the stack I described, based on the current pricing tiers I'm on:

| Service | Cost | What it covers |
|---|---|---|
| HeyGen Creator | $30/mo | Limited Avatar 5 generations |
| HeyGen API credits | ~$4/min of clip | Additional avatar renders beyond the tier |
| 11 Labs Creator | $22/mo | About 100 minutes of generated audio |
| Claude Code | $20-$200/mo | Orchestration, depending on usage tier |
| Remotion | Free (self-hosted) | Rendering runs on my machine |

For a 10-minute finished video, the marginal cost lands right around $50 — mostly HeyGen API time. Compared to the ~$300 I was paying a freelance editor per video, that's a 6x cost reduction. Across a 40-lesson course, it's the difference between a $12,000 production bill and a $2,000 one.

The subtler savings is time. I used to burn about 4 hours of my own time per video on filming, review, and revision cycles. Now I burn about 20 minutes writing the script and starting the run. If you value your own time at $50/hour, that's another $190 of buyback per video. Call the total savings north of $400 per finished 10-minute lesson, and the math for a course gets genuinely silly.

One honest caveat on these numbers: I'm not counting the setup time. I spent probably 15 hours building and tuning the orchestrator across two weekends. If you want this working end-to-end, expect to invest that upfront regardless of how fast the models get. The pipeline is cheap to run and expensive to build, which is exactly the shape you want.

## Real Talk: Where This Pipeline Breaks and Where It Shouldn't Be Used

I want to be direct about the limits here, because there's too much AI video content online pretending this stuff is finished. It isn't.

**Avatar 5 still has occlusion artifacts.** When I gesture with my hand crossing my face, the avatar sometimes produces a subtle ripple at the occlusion edge. It's not obvious unless you're looking for it, but a trained eye catches it. For broadcast-quality work, this is a dealbreaker. For course content, it's invisible to learners.

**The Playwright workaround is fragile.** Any HeyGen UI change breaks the automation, and I've had to re-record the Playwright flow twice in two months. This is the biggest operational risk in the stack right now, and it will stay that way until HeyGen ships an Avatar 5 API. If you're building this today, plan for the Playwright piece to occasionally need 30 minutes of maintenance.

**I will not use this for my personal YouTube channel.** This is the thing most creators miss. My personal YouTube is a relationship channel — people show up because they know me, not because they need information. An AI avatar would feel like a betrayal of that contract, even if it looked perfect. So the real mental model isn't "AI video replaces filming." It's "AI video lets you scale the content where presence doesn't matter, so you can invest the saved time in the content where presence is everything." Course lessons, internal training, explainer videos — pipeline. Personal channel, client calls, keynotes — still me, on camera, for real.

**The "AI content flood" objection is overrated.** Yes, more people can produce more video now. So what? More people could produce more blog posts when WordPress shipped, and the good ones still stood out. Quality still wins. The bottleneck moved from production to ideation, and the creators with the best ideas are about to have a very good year.

**Editors aren't going away — their role is transforming.** The editor I was paying $300 per video can now charge me $100 to QA and polish the AI output, and do five times as many videos per week. The ones who understand the new pipeline become domain AI specialists. The ones who refuse to touch it will struggle. This is the same pattern that hit every creative field that automation touched before this one.

## What Changes When the Bottleneck Moves

Here is the real takeaway, and it's bigger than the specific tools.

For the last twenty years, video production economics have been set by the cost of filming and editing. Ideas were cheap. Execution was expensive. That ratio is why video content has been dominated by professionals and well-funded channels — the execution moat kept amateurs out.

This pipeline inverts the ratio. Execution is now cheap and overnight. Ideas are the bottleneck. The creators who win the next cycle are the ones who can generate, test, and ship ten times more video concepts per week than they used to, because the cost of being wrong about a concept just collapsed. Film a 10-minute video the old way, hate it, and you've burned $300 and a week. Generate it through the pipeline, hate it, and you've burned $50 and six hours of machine time. Revision becomes real. Iteration becomes possible. Volume becomes strategy.

If you build courses, train internal teams, ship developer education, or produce repeatable explainer content, this pipeline is worth the two weekends of setup. If you're a creator whose audience is paying for presence — your face, your voice, your live reactions — keep filming and use this pipeline for the supporting content you weren't producing anyway.

## Frequently Asked Questions

### Do I need coding skills to build this pipeline?
You need enough comfort with Claude Code and basic scripting to wire the services together, but you don't need to be a senior engineer. Most of the orchestration is prompt-driven, with Claude writing the glue code. For a deeper walkthrough of how Claude Code handles multi-tool orchestration, see the pipeline breakdown above.

### How much voice data does ElevenLabs really need for a good clone?
ElevenLabs recommends at least 30 minutes of clean audio, and 2+ hours for Professional Voice Cloning, according to their [official documentation](https://elevenlabs.io/docs/creative-platform/voices/voice-cloning/professional-voice-cloning). I used 2 hours and the quality was significantly better than the 45-minute test clone I made first.

### Is HeyGen Avatar 5 available through the public API?
Not yet as of April 2026. HeyGen's public API defaults new renders to Avatar 4. Avatar 5 generations currently require the web dashboard, which is why my pipeline uses Playwright to automate the upgrade click. Expect this workaround to become unnecessary when HeyGen ships Avatar 5 API access.

### Why chunk scripts to 45-60 seconds instead of sending the full script at once?
Two reasons. ElevenLabs voice quality degrades past roughly 60 seconds of continuous generation, introducing flattening and artifacts. HeyGen Avatar 5 also caps segments at 3 minutes. Chunking at natural sentence boundaries stays inside both limits and produces cleaner stitching in Remotion.

### What does it cost to produce a 10-minute AI video with this stack?
Roughly $50 per finished 10-minute video, primarily HeyGen API time, compared to about $300 for a freelance editor. See the cost breakdown section above for the full math including subscription tiers.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)

---

**FEATURED IMAGE PROMPT:**
Create a premium tech-forward blog header image for an AI video production pipeline:
- Background: Dark gradient (#0F172A → #1E293B) with subtle neural network mesh overlay
- Central element: A holographic avatar silhouette emerging from a stream of code, glowing cyan (#06B6D4)
- Left side: Claude Code terminal window with purple (#8B5CF6) glow showing an orchestration prompt
- Connected nodes around the avatar labeled subtly: voice waveform, camera aperture, film strip, React logo
- Floating audio waveforms transforming into video frames
- Color palette: Purple (#8B5CF6) → Blue (#3B82F6) → Cyan (#06B6D4) gradient flowing diagonally
- Motion lines suggesting an overnight pipeline — clock, moon silhouette, processing indicators
- Style: Futuristic production studio aesthetic, cinematic lighting, clean 3D elements with depth
- Mood: Automated, powerful, effortless scale
- Aspect ratio: 16:9
