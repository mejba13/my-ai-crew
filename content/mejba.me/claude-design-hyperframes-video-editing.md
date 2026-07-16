**BRAND:** mejba.me
**TITLE:** Claude Design + Hyperframes: Prompt-Driven Video Editing
**META TITLE:** Claude Design and Hyperframes Video Editing Tested
**SLUG:** claude-design-hyperframes-video-editing
**PRIMARY KEYWORD:** Claude Design video editing
**SECONDARY KEYWORDS:** Hyperframes Claude Code, HTML to MP4 video automation, transcript-synced motion graphics
**META DESCRIPTION:** I tested Claude Design and HeyGen's Hyperframes for prompt-driven video editing. Here's what worked, what broke, and the workflow that actually ships MP4s.
**TAGS:** Claude Code, AI Video Editing, Hyperframes, Motion Graphics, Automation
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, you will know exactly which of the two tools — Claude Design or Hyperframes — to pick for your next video, how to prepare a transcript that actually syncs, and the specific iteration pattern that gets a prompt-driven edit across the finish line without hours of manual cleanup.

---

It was 11:14 PM on a Friday when I finally admitted the thing I had been dancing around for two weeks.

I was on my third cup of cold brew, my third render of the same 90-second explainer video, and my third attempt to make a text callout land on the word "automation" instead of 0.4 seconds after it. The audio track was a recording of me walking through a client dashboard. The visuals were supposed to be clean on-screen text, a couple of charts, and a lower-third with our brand colors. Standard stuff.

What I was finding out the hard way is that prompt-driven video editing — the kind everyone has been breathlessly posting about since HeyGen dropped Hyperframes on April 17 and Anthropic quietly added video-style exports to Claude Design the same week — has a very specific failure mode. The AI doesn't listen to the audio. It can't. It has no idea when you say "automation." It only knows what you tell it.

That's the first thing nobody explains in the launch videos. And it's the thing that determined, in the end, whether this workflow actually saved me time or became another clever toy I abandoned after a week.

This post is the honest version of what happened across those two weeks. I built the same 90-second video in both tools — in Claude Design first, then in Hyperframes through Claude Code — and I'll walk you through what each one actually does, where each one broke, and the specific iteration pattern I landed on that now lets me ship prompt-driven motion graphics in under 30 minutes per video.

If you have been watching these launches wondering whether you can finally stop fighting Premiere or After Effects, you can. But not for the reasons the demos suggest. Let me show you where the real value is hiding.

## The Two Tools, The Same Problem

Before I get into the testing, we need to get the shape of these two tools correct. A lot of the confused takes I have seen online are mixing them up — treating them like competitors when they are actually doing different jobs inside the same broader pipeline.

**Claude Design** is a web workspace at claude.ai/design, running on Opus 4.7, that you use through a browser. I wrote a full breakdown of what it is and why it exists in [my Claude Design review](/claude-design-visual-workflow-claude-code), but the short version is this: it is Anthropic's visual surface for building designs, slides, prototypes, one-pagers, and now — via the updated export pipeline — animated scenes that you can hand off as an HTML bundle or screen-capture as video.

**Hyperframes** is an open-source rendering framework from HeyGen, released April 17, 2026, under Apache 2.0. You install it into Claude Code with `npx skills add heygen-com/hyperframes` and it gives your agent three slash commands: `/hyperframes` for authoring compositions, `/hyperframes-cli` for the command-line operations, and `/gsap` for animation help. Scenes are written in plain HTML, CSS, and JavaScript. The rendering pipeline turns every frame into a real image, then stitches them into an MP4, MOV, or WebM. It runs locally. No cloud. No API key.

Both tools hit the same wall the moment you try to turn them into a real editing workflow: neither of them interprets audio. They do not hear your voiceover. They do not know where the words land in time. They know only what you feed them through text.

That is why the transcript is the single most important asset in this entire workflow — more important than the prompt, more important than the design system, more important than the model you are running. I spent the first three days of my testing not understanding this, and it was three wasted days.

Let me explain what I mean.

## The Transcript Is The Script

Here is the mental model that unlocked this whole workflow for me. Stop thinking of the transcript as "the subtitles." Start thinking of it as the timeline. It is the thing the AI reads to know where your words are. Without word-level timestamps, every on-screen animation you ask for becomes a guess.

I produce transcripts with Whisper — the same way I do for my [Claude Code video editing pipeline](/claude-code-video-editing-workflow). The specific output format matters. You do not want a plain text dump. You want a JSON file that looks like this, with timestamps on individual words:

```json
{
  "words": [
    { "text": "I",          "start": 0.00, "end": 0.12 },
    { "text": "automated",  "start": 0.14, "end": 0.78 },
    { "text": "this",       "start": 0.80, "end": 0.96 },
    { "text": "entire",     "start": 1.00, "end": 1.42 },
    { "text": "workflow",   "start": 1.46, "end": 2.08 }
  ],
  "segments": [
    { "start": 0.0, "end": 4.2, "text": "I automated this entire workflow..." }
  ]
}
```

With a file like that, you can write prompts that actually make sense to the AI. Instead of "animate the word automation at the right time," you can tell it: "read `transcript.json` and fade in a highlight box over the word *automated* using its `start` and `end` fields." That sentence is computable. The model can execute it deterministically. It lands on the frame every single time.

The other thing the transcript lets you do — and this is where the real leverage shows up — is pattern-match across your own speech. Want to throw up a chart every time you say a number? Scan the transcript for numeric strings. Want to highlight a brand name every time it appears? Scan for the string. Want to color-code your own filler words differently from your emphasis words? Write that rule once, apply it to every future video.

I realized on day four of testing that the transcript was not an input to the video. It was the source of truth for the video. Once I made that mental flip, both tools started behaving the way the launch demos made them look.

Now let me show you what each one did with the same 90-second source material.

## Test 1: Claude Design Built My Explainer in Thirty-Seven Minutes

The source for this test was a client explainer video for a Ramlit dashboard project. 90 seconds of voiceover. Me walking through three screens, narrating what each one does, with three numbers I wanted called out and a final CTA card.

I went into Claude Design first because the barrier to entry is almost nothing. Open the browser. Click the palette icon. Paste a prompt. That is the whole onboarding.

My first prompt was long on purpose. I had learned the hard way from the brand-extraction work that Claude Design rewards context. Here is roughly what I wrote:

> "I have a 90-second explainer video for a project management dashboard. Attached is the transcript JSON with word-level timestamps and the dashboard screenshots. Please build three animated scenes that match my mejba.me brand (dark navy background, purple-to-cyan gradient accent, Inter typography). Scene 1 opens on the dashboard overview screen at 0-18 seconds. Scene 2 highlights the analytics panel at 18-52 seconds. Scene 3 is a closing CTA at 52-90 seconds. For every number I say in the transcript, animate a pill-shaped stat card that appears centered over the relevant screen area and matches the start and end timestamps from the JSON. Export as an HTML bundle."

What I got back in about eighteen minutes was genuinely impressive. Three composed scenes. My brand colors pulled correctly. Animated stat pills sitting on top of my screenshots. A generated SVG intro title that looked like a junior designer on a good day had made it.

But it had drift. The animations were triggering on section boundaries, not word boundaries. When I narrated "we cut editing time by forty percent," the forty-percent pill appeared somewhere between "editing" and "time" — about 0.6 seconds early. Across three scenes, four of the five stat callouts were off by more than half a second. Two felt completely wrong.

I asked Claude Design why. The answer was the thing I had already suspected: the tool was reading my scene boundaries from the prompt, but it was not parsing the per-word timestamps inside the transcript file. It was snapping to the nearest segment. Segment-level synchronization is fine for big mood shifts. It is not fine for a stat callout that is supposed to pop on a specific word.

That is Claude Design's structural limit for video. It is brilliant at composition, at layout, at brand consistency, at building scenes that look like a designer touched them. It is not built as a frame-accurate animation engine. The export is also where this shows up — you can get an HTML bundle, but to get an actual MP4 out, you either screen-record the preview or hand the bundle off to Claude Code and render it through a second tool. Which is exactly what I did next.

Here is where I want to be clear about what Claude Design is genuinely great at in a video context, because I do not want to undersell it. It is the fastest way I have found to generate the static pieces of a video: intro cards, outro cards, lower-thirds, callout templates, stat pill designs, thumbnail variants. You burn fifteen minutes in Claude Design, pull five polished graphic templates into your Hyperframes project, and you have just skipped the worst part of video production — designing the look. The AI is a better junior designer than most juniors I have worked with, specifically because it reads your actual codebase for brand tokens instead of guessing them.

What it is not is the whole pipeline. It is half of it.

## Test 2: Hyperframes Rendered The Same Video In Three Iterations

For the Hyperframes test, I opened Claude Code in a fresh terminal, cloned a new project folder, and ran the install:

```bash
npx skills add heygen-com/hyperframes
```

The skill registered `/hyperframes`, `/hyperframes-cli`, and `/gsap` as slash commands inside my Claude Code session. I dropped the same voiceover MP3 into an `assets/` folder, the same word-level `transcript.json`, and the same three dashboard screenshots. Then I typed:

> "/hyperframes build a 90-second composition. Read transcript.json for word-level timestamps. For each numeric value spoken in the audio, render a pill-shaped stat card centered over the corresponding screen image using the exact start and end timestamps from the JSON. Use GSAP for the animations. My brand colors are #0F172A background, gradient accent from #8B5CF6 to #3B82F6 to #06B6D4, Inter typography. Output a single scene composition in HTML with GSAP timelines."

The first output ran in about nine minutes. Claude Code wrote a single composition file, set up the GSAP timelines, bound the animations to the word timestamps from the transcript, and rendered a preview in the browser. I watched it play. Every stat pill landed on its word. Every word. Not close, not "within a frame" — on the frame.

The reason it worked is mechanical, not magical. Hyperframes compositions are just HTML. GSAP timelines accept a `delay` and a `duration` in seconds. When Claude Code reads a transcript entry `{ "text": "forty", "start": 34.21, "end": 34.68 }` and writes `gsap.to(statPill, { opacity: 1, delay: 34.21, duration: 0.47 })`, there is no ambiguity. The tool is doing exactly what I told it to do, using exactly the timestamps I gave it. Claude Design was guessing. Hyperframes was executing.

The first draft was not perfect, obviously. The stat pills animated in with a bounce that felt wrong for a serious dashboard video. My client's brand is calm and precise. I gave Claude Code timestamped feedback:

> "At 34.21 seconds, the 40% pill bounces in. Replace the elastic easing with `power2.out`. Same change for the pills at 48.9 and 71.4. Also, at 12.5 seconds, the intro title is still fading out when the first screenshot appears — delay the screenshot entrance by 0.3 seconds."

Eleven minutes later, I had iteration two. Easing was clean. Title handoff was smooth. But the outro CTA at the end of the video had its subtitle overlapping the button for about a second. One more timestamped comment, one more render, and iteration three was the final cut. Total wall-clock time from empty folder to rendered MP4: about thirty-four minutes, of which maybe nine were hands-on keyboard time. The rest was render wait.

I did this same video in Premiere the month before. It had taken me 2 hours and 40 minutes, including re-cuts. This was not a small delta. This was a structural difference in how the work feels.

## What The AI Cannot Do

If you read the last two sections and thought "this sounds suspiciously good," you are paying attention. Here are the failure modes I hit over two weeks, ranked by how often they happened.

**Raw audio cleanup still needs a human.** The entire prompt-driven pipeline assumes your voiceover is already clean. No um filler. No long pauses. No weird breath cuts. If your raw recording is rough, the tools will happily render motion graphics on top of garbage audio. Whisper transcription, a quick pass in Descript to bleep out filler words, and re-export the audio before you even touch a transcript. I covered that upstream side in [the video editing workflow post](/claude-code-video-editing-workflow).

**Preview glitches in both tools.** Claude Design's in-browser preview stuttered on my MacBook Pro M2 about every fifteenth frame. Hyperframes preview was better but still occasionally lost sync between audio and the GSAP timeline when scrubbing. Final rendered output was always correct. The preview bug is real, and it will trick you into fixing things that are not actually broken. If something looks off in preview, render a 10-second test clip before you start rewriting prompts.

**Token burn is real.** A full 90-second composition with timestamped animations, iterative feedback, and three rendered previews took me about 340,000 tokens of Opus 4.7 usage in Claude Code, end to end. That is not nothing. For a Pro subscriber doing one video a week, it is comfortable. For somebody trying to run a ten-video-per-week content factory, you need to switch to Sonnet for the iteration loops and reserve Opus for the initial build. I usually do the first draft with Opus, switch to Sonnet for feedback iterations, and only go back to Opus if something needs a structural rebuild.

**Complex 3D effects need a human in the loop.** Hyperframes supports Three.js, and yes, Claude Code will happily write a Three.js scene. But the outputs for anything genuinely 3D — reactive audio visualizers, dimensional reveals, camera moves in 3D space — need an engineer who knows Three.js to debug them. The AI writes the scaffold. A human often has to fix the physics and the timing. This is not a critique, exactly. It is where the tool stops replacing expertise and starts amplifying it.

**Neither tool edits raw footage.** Worth saying explicitly because I had a client ask me this last week. You cannot drop a 40-minute raw recording into Claude Design or Hyperframes and get a cut video out. These tools build the motion graphics layer that sits on top of an already-cut video. The cutting still happens in Descript, Premiere, or a Whisper-driven automation pipeline. What changed is the step after the cut — the part where you used to spend three hours in After Effects.

## The Pattern That Now Ships My Videos

After two weeks of this, I have landed on a specific workflow I use for every explainer-style video I ship. It takes about thirty minutes of hands-on time for a 90-second video, and it looks like this.

**Step 1: Clean the audio first.** Record, transcribe with Whisper, clean in Descript, re-export an MP3. Do not skip this. A bad recording is a bad video no matter how good the animation layer is.

**Step 2: Generate the word-level transcript JSON.** Whisper's `--output_format json` flag gives you what you need. Keep the file in your project root. Call it `transcript.json`. Every downstream prompt depends on this file existing at that path.

**Step 3: Build static assets in Claude Design.** Intro cards, outro cards, stat pill templates, lower-third designs. Dump them into your project's `assets/` folder as SVGs or HTML snippets. Do this in one Claude Design session of about fifteen minutes. This is the part Claude Design is genuinely the best at.

**Step 4: Scaffold the composition in Hyperframes through Claude Code.** Write the first prompt with the full scene plan, the transcript reference, the brand tokens, and the asset paths. Let Opus 4.7 build the first draft. Expect about ten to fifteen minutes per render.

**Step 5: Iterate with timestamped feedback.** Watch the preview. When something is wrong, describe it in the format "at 34.2 seconds, [thing] does [wrong thing], change to [right thing]." Switch Claude Code to Sonnet for these iteration loops to save tokens. I rarely need more than three iterations.

**Step 6: Render final MP4 locally.** `npx hyperframes render --format mp4 --output final.mp4`. Takes about two to four minutes for 90 seconds of content on my M2. Verify audio sync, verify all timestamps, ship.

That is it. Two tools, one transcript, a specific order.

If I had to strip it down further, the one-line version is this: use Claude Design for static graphics, use Hyperframes through Claude Code for the composed animation, and always — always — drive timing from a word-level transcript JSON. Everything else is workflow dressing.

## What This Means If You Edit Video For A Living

I want to finish with a thought for anyone reading this who currently makes a living editing video, because I have been getting DMs from freelance editors asking the honest version of this question: am I out of a job in six months?

No. Not even close. But the job is changing shape, and the shape of the change matters.

The mechanical parts of editing — positioning text, keyframing motion, ensuring brand consistency across a hundred scenes, generating lower-thirds for a series, rendering CTA cards to spec — all of that is now compressible into minutes. An AI agent with Hyperframes installed will do that work faster than you can, for cheaper than you charge, with fewer errors.

What is not compressible, and what I do not see becoming compressible in the next eighteen months, is the thing your best clients actually pay you for: taste. The decision about which three-second moment in a twelve-minute recording carries the whole piece. The instinct that tells you a silence needs to breathe for half a beat longer. The judgment about when a stat card helps the story and when it pulls attention away from a facial expression that was about to land.

Those decisions are why editors get hired. The tools I tested this month do not make those decisions. They execute the ones you have already made, at a speed that was simply not possible eight weeks ago.

The editors who will thrive in the next two years are the ones who stop thinking of themselves as people who move clips around a timeline, and start thinking of themselves as directors who conduct an AI animation team. The work becomes more strategic, less mechanical. The billing rate, if you play it right, goes up, not down.

## Frequently Asked Questions

### What is the difference between Claude Design and Hyperframes?
Claude Design is a web-based visual workspace at claude.ai/design used for building layouts, slides, and static graphics with brand consistency. Hyperframes is an open-source HTML-to-MP4 rendering framework from HeyGen that runs through Claude Code for composing and rendering actual video. Use Claude Design for static assets; use Hyperframes for the animated, timestamp-synced video output.

### Can Claude Design or Hyperframes read my audio file?
No. Neither tool interprets audio natively. Both require a pre-generated transcript with word-level timestamps — typically produced by Whisper — to synchronize on-screen animations with spoken content. The transcript is the timeline. Without it, timing is a guess.

### How do I install Hyperframes with Claude Code?
Run `npx skills add heygen-com/hyperframes` inside a Claude Code session. The skill registers three slash commands: `/hyperframes` for authoring compositions, `/hyperframes-cli` for command-line operations, and `/gsap` for animation help. Full install in under a minute on most machines.

### How long does a prompt-driven video actually take to produce?
For a 90-second explainer with motion graphics, my current workflow takes about thirty minutes of hands-on time: roughly fifteen minutes in Claude Design for static assets, fifteen minutes in Claude Code plus Hyperframes for the composition and two or three iteration cycles. Compare to two-plus hours in a traditional editor like Premiere.

### What kind of videos is this workflow not good for?
Raw-footage editing (cutting hours of interview video down to highlights), anything that needs nuanced 3D physics without a human Three.js developer, and videos where the motion graphics layer carries emotional weight that depends on human timing intuition. The tools amplify editorial taste; they do not replace it.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
