**BRAND:** mejba.me
**TITLE:** Remotion in OpenAI Codex: Prompt-Based Motion Graphics
**META TITLE:** Remotion Codex Motion Graphics: My 30-Min Video Test
**SLUG:** remotion-codex-motion-graphics-ai-video
**PRIMARY KEYWORD:** Remotion Codex motion graphics
**META DESCRIPTION:** I built a launch video using the Remotion Codex plugin and GPT-5.4. Here are the prompts, the broken parts, and the H.264 file I shipped.
**TAGS:** Remotion, OpenAI Codex, Motion Graphics, AI Video, GPT-5.4

---

The render finished at 11:42 PM on a Tuesday. I dragged the H.264 file out of the `out/` folder, dropped it into a Twitter post, and stared at the screen for a second. Thirty-four seconds of motion graphics. Logo intro. Animated beams connecting Claude Code to my agent stack. Three scene transitions with kinetic typography. A closing logo fly-off with a Suno-generated funk track underneath at 75% volume.

Total time from "I want to make this video" to "I'm posting this video": 34 minutes.

I have not opened After Effects in three weeks. Not because I'm a better motion designer than I was a month ago — I'm objectively worse. My muscle memory for the AE timeline is rotting. But I shipped four videos in April that would have eaten an entire weekend each in my old workflow. And I did all of them inside the OpenAI Codex super-app, talking to GPT-5.4, with the Remotion plugin doing the heavy lifting.

This is the post I wish someone had written before I spent an afternoon figuring it out the wrong way. Not the marketing version where everything is smooth. The real version, with the prompts that worked, the prompts that broke, and the moment I almost gave up and went back to After Effects.

## The Workflow That Made Me Stop Editing Videos By Hand

Here's the short version of what's actually happening when you use Remotion Codex motion graphics together: Remotion is a React framework for making videos with code. You write JSX components, define a composition, and Remotion renders it to MP4. It's been around for years. Jonny Burger built it. The piece that changed in early 2026 was the Codex plugin — a packaged set of skills the OpenAI Codex agent loads, so when you type "make a logo intro with a beam animation between two PNGs" into the Codex chat, GPT-5.4 actually knows what a `<Sequence>` is, what `interpolate()` does, and how to wire up a `Composition` without you ever touching the code.

The Remotion Codex plugin shipped on the official `remotion-dev/codex-plugin` repo around the same window OpenAI announced Codex plugins (March 26, 2026). The Codex agent runs on GPT-5.4 by default — the first OpenAI model with native computer-use baked in and a 1M-token context window, which matters more than it sounds when your video project starts piling up component files.

You install the plugin into Codex once. You spin up a project folder. You start a chat. You describe the video. Codex writes the Remotion code. Remotion serves a live preview at `http://localhost:3000`. You watch the video render in real-time as you iterate by prompt — "make the beam pulse twice", "delay the second logo by 12 frames", "the kerning on the title is off, tighten it." When you're done, you run `npx remotion render` and out pops an H.264 file.

That's the whole workflow. No keyframe panel. No graph editor. No nested precomps. No wondering why your text layer is suddenly behind the background again.

The catch — and I'll get to the honest catch later — is that this only works because GPT-5.4 has gotten genuinely good at writing Remotion code. Two years ago this would have been a disaster of broken imports and made-up APIs. Today it's good enough that I trust it with a 30-second video. Not yet good enough to trust it with a 5-minute documentary. We'll come back to where the line is.

## Why I Even Tried This (Or: The Claude Code Marketing Story Nobody Verifies)

I want to be honest about why I started testing this in the first place, because the trigger was a story that's been circulating on X that I cannot fully verify.

The claim, repeated in roughly forty different threads, is that Anthropic's Claude Code marketing team built their daily video output with Remotion-plus-an-AI-agent and accumulated something like 300M cumulative views in a month. I've seen the screenshots. I've seen the engagement numbers. What I cannot do is point you at a published Anthropic case study that confirms the workflow exactly as described. I tried.

What I can verify with my own eyes: the Claude Code marketing channel ships short-form motion graphics videos at a cadence no traditional video team I've worked with could match. Three to five a day, sometimes more. The visual language is consistent — same easing curves, same background gradient family, same logo motion. That kind of consistency comes from one of two places. Either a senior motion designer is templating everything in After Effects (possible), or there's a code-based pipeline where the templates are React components getting reused (much more likely).

That was enough to get me curious. If you've followed any of my [Claude Code video editing workflow](https://www.mejba.me/blog/claude-code-video-editing-workflow) experiments, you know I've been chasing a "ship the video the same day I have the idea" pipeline for almost a year. I tried HeyGen plus ElevenLabs in the [AI video production pipeline](https://www.mejba.me/blog/ai-video-production-pipeline-heygen-elevenlabs) post and got close, but it was avatar-driven, not motion graphics. I tried [Claude Code with Remotion](https://www.mejba.me/blog/claude-code-remotion-video-creation) directly and that worked — Claude Code is genuinely good at this — but I wanted to test the Codex side honestly because I keep two subscriptions and I'd rather not.

So: a real test, on a real launch video, with prompts I'd use again.

## The Project I Used As My Test Bed

I needed a real video, not a tutorial demo. The launch video I picked was for a small internal tool I built — a personal AI assistant project I've been running called OpenClaw. I needed:

- Roughly 30 seconds, ready for a Twitter post
- An animated logo intro
- Two text scenes with the value prop
- A "stack reveal" — small icon cards for the tools the project uses, with animated connecting beams
- A closing CTA scene
- Background music
- H.264 export, vertical-friendly but landscape priority

In After Effects this would have been four to six hours of focused work, more if I was being precious about easing. I budgeted 90 minutes for the Codex test and figured if I blew through that I'd write a different post. I finished the first usable cut in 41 minutes. The next 30 minutes were polish, music, and the second render after I caught a typo. We'll get to all of it.

But before any of that, you need a project folder that the Remotion plugin can actually work with. That setup matters more than it should.

## Setting Up: The Codex Plugin and the Remotion Project

Here's the sequence I ran, exactly. Skip nothing.

**Step 1: Install the Remotion plugin into Codex.** Inside the Codex super-app, I opened the plugin marketplace, searched for Remotion, and installed the official `remotion-dev/codex-plugin`. You can also do this from the Codex CLI if you live there. The plugin registers a set of skills the GPT-5.4 agent uses — composition scaffolding, the official Remotion docs MCP, render configuration. After installing, restart the Codex chat surface so it picks up the new skills.

**Step 2: Create the project folder.** I made a folder at `~/projects/openclaw-launch-video/` and opened a new Codex chat scoped to that folder. Codex's project organization matters here — every video gets its own folder, every folder gets its own chat. Mixing two videos in one chat is how you end up with the agent renaming components from your last project into your current one. Ask me how I know.

**Step 3: Bootstrap the Remotion project.** My first prompt to Codex:

> "Initialize a new Remotion project in this folder using the latest version. Set the composition to 1920x1080, 30fps, and include a single 'HelloWorld' composition that loops a colored circle scaling up and down so I can confirm the live preview works. Then start the dev server."

Codex ran `npm create video@latest`, picked the empty TypeScript template, scaffolded the folder, and ran `npm run dev`. Within about 90 seconds I had `http://localhost:3000` open in my browser with a pulsing circle. Hello World confirmed. Live preview confirmed. Dev loop confirmed.

The localhost preview is the unsung hero of this entire workflow. Every change Codex makes to the code, the browser hot-reloads. You watch the video update in real-time as you prompt. This is the loop that makes Remotion-in-Codex feel less like coding and more like directing.

**Step 4: Drop in your assets.** I created an `assets/` folder and dropped in the OpenClaw logo as a PNG (1024x1024, transparent background), the icons for the stack tools as SVGs, and a `music/` subfolder with a Suno-generated funk loop I exported earlier in the day. Then I told Codex:

> "I've added assets. Logo is at `assets/openclaw-logo.png`. Stack icons are SVGs in `assets/stack/`. Background music is at `music/slow-funk-lift.mp3`. Use these in the upcoming compositions, never re-import other versions."

That last line — "never re-import other versions" — is a habit I've built. GPT-5.4 will sometimes invent paths that don't exist if you're vague. Be specific about your asset paths and tell it not to wander.

If your hands haven't shipped a `npm install` yet, do it now. The next phase only works if dev mode is hot.

## The First Real Composition: Logo Intro With An Animated Beam

Here's where the workflow starts to feel like magic the first time, and where I want to slow down because the prompts I used aren't obvious until you've used them once.

My prompt for the logo intro:

> "Create a new composition called `LogoIntro` that runs from frame 0 to frame 90 (3 seconds at 30fps). The OpenClaw logo enters from the left at frame 10, scaling from 0.6 to 1.0 with a spring easing. At frame 30, a horizontal beam of light starts drawing from the center of the logo to the right edge of the screen, reaching full width by frame 60. The beam is a 4px line with a soft white-to-cyan gradient and a subtle glow. From frame 60 to 90, the beam pulses once, then fades. Background is a dark navy gradient (#0F172A to #1E293B). Use Remotion's `spring` and `interpolate` helpers. Keep the code in `src/compositions/LogoIntro.tsx` and register it in the root."

What Codex did: created `LogoIntro.tsx`, imported `spring`, `interpolate`, `useCurrentFrame`, and `useVideoConfig` from `remotion`, wrote a properly framed `AbsoluteFill` with the gradient, animated the logo entry with a spring config, and built the beam as an SVG line with a `linearGradient` definition and a CSS box-shadow for the glow. Registered it in `Root.tsx`. Hot-reloaded the localhost preview.

Looked at the preview. The beam was there but it shot off the right edge of the screen instead of stopping at the screen boundary. The logo entry was crisper than I expected.

Second prompt:

> "The beam is overshooting the right edge. Constrain the beam endpoint to the screen width. Also slow the spring entry on the logo by 15% — it feels too snappy."

Codex adjusted the `damping` value in the spring config, clamped the beam endpoint to `width - 80`, and reloaded. Better. Shipped.

This is the rhythm. You prompt. You watch. You correct in plain English. You don't think about frame numbers or keyframe interpolation curves — you think about what the video should feel like, and the agent translates that into the spring `mass` and `stiffness` values you'd otherwise be tweaking by hand.

There is one moment from this exact session that I keep thinking about. I was halfway through the second prompt and I caught myself writing "make the spring less bouncy" instead of "reduce the spring damping coefficient." Six months ago that would have felt like sloppy communication. Now it's the entire interface. The agent knew what I meant. It picked the right parameter. That tiny shift — from technical instruction to creative direction — is the actual product here. The Remotion plugin and GPT-5.4 are just the wiring.

## Building the Stack Reveal Scene (The Part That Almost Broke Me)

The stack reveal was the hardest scene and the one I want you to learn from, because this is where the workflow's limits show up.

The idea: four small icon cards (Claude logo, Codex logo, n8n logo, Supabase logo) appear in sequence, with animated beams of light drawing between them in a graph pattern. Card one connects to card two with a beam, card two splits beams to cards three and four. The whole sequence runs from frame 0 to frame 120 (4 seconds at 30fps).

My first prompt was too vague. I said:

> "Make a stack reveal scene with four icon cards that animate in with beams connecting them in a graph pattern."

What I got back was four icon cards stacked vertically with straight horizontal beams. Not what I wanted. Not what I'd described, but also not unreasonable given how I'd described it.

So I rewrote the prompt with actual coordinates:

> "Create a `StackReveal` composition. Layout four icon cards in a 2x2 grid centered on screen. Card 1 (Claude) at position (640, 360). Card 2 (Codex) at (1280, 360). Card 3 (n8n) at (640, 720). Card 4 (Supabase) at (1280, 720). Each card is 200x200, dark gray background with rounded corners, the SVG icon centered. Cards fade in with a 6-frame stagger starting at frame 10. After all cards are visible (around frame 40), draw animated beams: from Card 1 to Card 2 (horizontal), then from Card 2 splitting down to Card 3 and Card 4 simultaneously. Beams use the same gradient as the LogoIntro. Each beam takes 15 frames to draw. End with a brief pulse of all beams at frame 110."

That worked on the first hot-reload. Lesson: when the agent guesses wrong, give it coordinates. Stop being abstract.

Then I tried to push it further:

> "Add a subtle Lissajous curve background animation behind the cards. Slow, low contrast, just to give the scene some life."

This is where I learned the boundary. Codex generated a `LissajousBackground` component that math-wise was correct — actual parametric equations, `useCurrentFrame` driving the phase — but visually it looked like spaghetti. Too fast, too high contrast, too distracting from the cards.

I went back and forth four times before getting something usable:

1. "Make the Lissajous slower — reduce the frequency."
2. "Lower the opacity to 0.15."
3. "Use only a single curve, not three overlapping ones."
4. "Color it with the same cyan from the beams, not white."

After the fourth iteration it was finally subtle enough to work as background. Total time on the Lissajous experiment: 11 minutes. In After Effects this would have been 30 seconds with a preset. In Codex it was four prompts and one moment of doubt.

This is the honest part of this workflow. Some things are dramatically faster. Some things are slower. The math-driven backgrounds that take 30 seconds in AE take a few prompts in Codex. The character animations that take 90 minutes in AE take 15 minutes in Codex. The trade is real and you should expect it. If you've followed my [vibe coding](https://www.mejba.me/blog/vibe-coding-build-apps-without-code) experiments, this will feel familiar — same trade, different medium.

## The Frame-Level Editing Trick That Changed Everything

This is the technique I want you to take away from this post even if you forget everything else.

You can take a screenshot of the localhost preview at a specific frame, drag it into the Codex chat, annotate it (or just point at things in the prompt), and Codex will read the screenshot and understand exactly which element you mean.

I did this for the title text in scene three. The text "AI-powered video motion graphics" was animating in fine, but the kerning between the "f" and "i" in "video" looked off. I screenshot the preview at frame 247, dropped it into the chat, and wrote:

> "Look at this frame. The kerning between the 'f' and 'i' in 'video' is too tight. Loosen it slightly. Also the word 'graphics' is sitting about 4px too low on the baseline."

Codex parsed the screenshot, identified the `Text` component rendering that scene, added a `letterSpacing` adjustment for the affected glyph pair, and bumped the baseline of the second word. One prompt, two surgical fixes, both correct on first hot-reload.

Frame-level references work the same way. Instead of saying "the part where the beam pulses", I'd say "frame 4:17" or "around frame 127" and Codex would target exactly that frame in the timeline. Combined with screenshots, it's the closest thing I've used to an actual conversation about video edits with someone who can't see what I'm seeing.

This is what you give up when you go back to After Effects: not the timeline (the timeline is fine), but the ability to describe a problem in human language and have the tool understand which pixel on which frame you're talking about.

## Adding Music: Suno + 75% Volume + One Render Quirk

Music was the last 5 minutes of the build.

I generated a 35-second instrumental on Suno earlier in the day — a track called "Slow Funk Lift" that had the right tempo and didn't fight the visuals. Saved as MP3. Dropped into `music/` in the project folder. Then prompted Codex:

> "Add the audio file at `music/slow-funk-lift.mp3` as the soundtrack for the entire video. Volume at 75%. Fade in over the first 30 frames, fade out over the last 30 frames. Make sure the audio length matches or exceeds the total video length."

Codex added an `<Audio>` component in the root composition with the volume prop set to `0.75`, used `interpolate` to drive the volume up and down over the fade frames, and confirmed the audio file was longer than the 35-second total video runtime.

One quirk I'll save you: when I rendered the first time, the audio was missing entirely from the output file. Cause: the dev server preview plays audio fine, but the render command needs `--codec h264` explicitly when you're also bundling audio, or the default codec selection sometimes silently drops the audio track. Adding `--codec h264` to the render command fixed it.

This is the kind of bug that costs you nothing once you know about it and costs you 20 minutes of "why is there no sound" the first time. Now you know.

## The Final Render: H.264, Twitter, Done

Final render command:

```bash
npx remotion render src/index.ts MainComposition out/openclaw-launch.mp4 --codec h264
```

Render time on my MacBook M-series: about 3 minutes for 35 seconds of finished video. Output file: 14.8 MB H.264 MP4. Twitter accepted it without re-encoding. Posted it. Went to bed.

Three days later it had pulled engagement numbers I would not have predicted. Not Anthropic-marketing-team numbers — I'm one guy with an idea, not a brand machine — but enough to make me schedule the next four videos for the rest of the week.

## What This Workflow Is Actually Good For (And What It Isn't)

I want to be honest about where this lives.

**This workflow is excellent for:**

- Social media drop videos (Twitter, LinkedIn, IG Reels at 30 seconds or less)
- Product launch videos where the visual language is logo-first and text-heavy
- Daily or near-daily content where speed beats polish
- Templated motion graphics where you're going to make the same kind of video twenty times with different copy
- Indie creators, solo founders, and small teams without a motion designer
- Anyone who's already comfortable with code and wants to skip the AE learning curve entirely

**This workflow is not yet good enough for:**

- Hand-animated character work or anything that needs frame-by-frame illustration
- Heavy compositing with green screen, rotoscoping, or complex tracking
- VFX work for film, broadcast, or anything that needs to survive a colorist's grade
- Long-form documentary cuts where the human edit decisions are the entire point
- Anything where a senior motion designer is going to look at the keyframe graph and need to tweak a single bezier handle

That last point is the one After Effects loyalists keep raising and they're right. AE gives you surgical control over every keyframe, every easing curve, every layer blending mode. Remotion-in-Codex gives you speed and consistency. They are not competing for the same job.

A working motion designer is going to keep AE open. A solo founder shipping a launch video on a Tuesday night is going to be in Codex.

## Honest Comparison: Codex vs Claude Code for This Workflow

Since I run both subscriptions and I've now done this workflow on both sides, here's the honest comparison.

**Claude Code with Remotion** (the workflow I documented in [the earlier post](https://www.mejba.me/blog/claude-code-remotion-video-creation)) is excellent. The Anthropic agent is fluent in React, the Remotion skills work cleanly, and the iteration loop is smooth. If you're already in Claude Code for everything else, there's no urgent reason to switch.

**Codex with the Remotion plugin and GPT-5.4** is also excellent. The plugin is well-built. GPT-5.4 understands Remotion deeply, including some of the trickier parts around `<Sequence>` composition and audio bundling. The 1M-token context window means a large project with many compositions stays coherent across a long chat session.

The differences I noticed in actual use:

- **GPT-5.4 was slightly more aggressive about generating math-heavy components** (Lissajous, parametric curves, particle systems) on a first prompt. Sometimes that was great. Sometimes it generated more complexity than I asked for.
- **Claude was slightly more conservative about file structure**, which meant cleaner folders by default but also meant I had to ask for more files explicitly.
- **Both handled screenshot-based feedback well.** This is now table stakes for any agent doing visual work.
- **Codex's plugin marketplace** makes the Remotion install a one-click affair. Claude Code's skills require slightly more setup.

If you're building a daily video pipeline, I'd pick whichever agent is already deeper in your workflow. Don't switch your whole subscription stack just for this. The agent matters less than the Remotion plugin and the React-first mental model.

If you're brand new to both and choosing fresh, Codex's plugin marketplace gives you a slightly faster zero-to-first-render path. That's the only edge I'd call decisive.

## Frequently Asked Questions

### Do I need to know React to use the Remotion Codex plugin?
No, but it helps. The Codex agent writes the React code for you, so you can describe videos in English and never touch a component file. Knowing React makes debugging faster when an animation looks off — you can read what the agent wrote and ask for a specific fix instead of describing the visual problem from scratch.

### How long does a typical 30-second motion graphics video take with this workflow?
For a templated video with logo, text scenes, and basic transitions: 30 to 45 minutes from blank folder to rendered H.264 file, including music. For a more custom video with hand-tuned timing and multiple iterations: 60 to 90 minutes. Both are dramatically faster than the same video in After Effects from a non-templated start.

### Is Remotion free to use commercially?
Remotion is free for individuals and companies with three or fewer employees. Companies with four or more employees need a commercial license. Pricing is on the official Remotion site. The Codex plugin itself is free; the cost is your Codex subscription and the Remotion license tier you fall into.

### Can I export formats other than H.264 MP4?
Yes. Remotion supports H.265, ProRes, VP8, VP9, and frame sequences (PNG, JPEG). For Twitter, LinkedIn, and most social platforms, H.264 MP4 is the safe default. For higher-quality archival or further editing in another tool, ProRes is the better choice.

### What happens if Codex generates broken Remotion code?
It will sometimes — usually around obscure API features or when you give vague prompts about complex animations. The fix is to either give more specific coordinates and frame numbers, or paste the error message back into the chat and ask Codex to debug. The official Remotion docs MCP shipped with the plugin gives the agent up-to-date API references, which has dramatically reduced the rate of made-up function calls in my experience.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
