**BRAND:** mejba.me
**TITLE:** Gemini Omni Hands-On: I Tested Google's Video Model
**META TITLE:** Gemini Omni Hands-On: Avatars, Templates, Editing
**SLUG:** gemini-omni-video-generation-hands-on
**PRIMARY KEYWORD:** Gemini Omni video generation
**META DESCRIPTION:** I tested Gemini Omni hands-on — avatar scan, templates, video continuation, in-chat editing. Here's what works, what's capped, and where it beats Sora.
**TAGS:** Gemini Omni, AI Video, Google Gemini, AI Avatars, Creator Tools

---

# Gemini Omni Hands-On: I Tested Google's Video Model All Weekend

The first clip I generated with Gemini Omni was a ten-second shot of myself — or rather, an avatar trained on my face — sitting at a candlelit restaurant table eating spaghetti in a charcoal suit. I had typed two sentences. Maybe twenty words. I clicked generate, set my phone face-down, refilled my coffee, and by the time I came back the video was already done.

I watched it three times. Then I watched it five more.

The spaghetti was wrong — the strands moved like they were attached to invisible strings, not the kind of fluid noodle motion a real camera would capture. The candle flicker was too uniform. But my face? My actual face, doing micro-expressions while chewing, lit by a warm key light from the right, in a setting I had described in one sentence? It looked like me. Not "kind of looks like me" the way most AI avatars do. *Me.* Same jawline, same eye crinkles when I half-smiled, same way I tilt my head slightly to the left when I'm focused on something on my plate.

That was the moment I realized Gemini Omni isn't trying to be Sora. It's trying to be the thing that quietly replaces a third of the short-form video work people currently outsource.

If you read [my Google IO 2026 recap](/content/mejba.me/google-io-2026-gemini-omni-spark-recap.md), you got the strategic frame — how Google moved their generative-video work out of the standalone Veo branding into the core Gemini system, why SynthID provenance is the most important developer announcement of the keynote, and where Omni fits in the bigger agent-pivot story. This post is different. This post is the creator-side breakdown after a weekend of actually using the thing, generating clips until I hit my quota limit on Sunday night.

I'll walk through how the avatar setup actually works, the four templates I tested, the custom prompts that surprised me, where the 10-second clip cap and 720p watermarked export start to bite, and the honest verdict on whether this displaces Sora, Veo, Runway, or Kling for your workflow. Specific numbers, specific failures, no hedging. Let's get into it.

## The Avatar Setup That Made Me Take This Seriously

Most AI video tools treat your face as an afterthought. You upload a photo, the model generates something *adjacent* to you, and the avatar feels uncanny by frame three. Omni doesn't work that way, and the difference shows up in the onboarding.

When you tap into the avatar section under "more uploads" in the Gemini app, the flow opens a camera view and walks you through what is effectively a multi-modal facial scan. You speak a series of numbers aloud while slowly rotating your head — chin up, chin down, profile left, profile right, level. The whole capture takes about ninety seconds. The voice prompt isn't there to clone your voice for speech synthesis (Omni Flash deliberately doesn't include a personal voice clone — that's the "riskiest feature" Google held back at launch, according to [TechTimes' rundown of the rollout decisions](https://www.techtimes.com/articles/316859/20260519/google-launches-gemini-omni-video-model-holds-back-its-riskiest-feature.htm)). The voice prompt is there to verify liveness. You can't train an avatar from a still photo of a stranger. Your face has to be in front of the camera, your voice has to come out of your mouth, in real time.

This is the same anti-deepfake guardrail OpenAI used in the original Sora app's Cameos feature, and Google has clearly studied it carefully. The fact that Omni shipped with this guardrail on day one — and that the personal voice clone *did not* ship — tells you something about Google's read of the regulatory environment. They are trying very hard not to be the company that gets blamed for the first scaled-deepfake political incident.

Once the scan completes, the avatar is stored under your account's "more uploads" panel alongside any images or video clips you've imported. You don't see a 3D mesh, you don't see a parameter file. You just see your avatar listed as a reusable asset. From that point forward, any prompt you write can reference "my avatar" and Omni will use the trained likeness as the subject of the generated clip.

The quality of that trained likeness is where Omni earns its reputation. I ran four prompts back-to-back using the same avatar — a restaurant scene, a chalkboard lecture scene, an outdoor jogging scene, and a kitchen cooking scene — and the same face came back across all four. Same skin tone in different lighting conditions. Same proportions. Same expressive defaults. The consistency within each generated clip is strong; the consistency *between* clips, when you regenerate the same prompt, drifts a little on the opening frame but locks in correctly by the second or third second of the clip. Google's [official overview page for Gemini Omni](https://gemini.google/overview/video-generation/) acknowledges this — first-frame stability is a known soft spot, and the model is tuned to recover quickly into character once motion begins.

That's the avatar foundation. Now let's talk about what you can actually do with it.

## The Templates That Tell You Where Omni Wants to Live

Omni ships with a small library of prompt templates baked into the generation interface. These aren't full-fat features Google has documented publicly with marketing names — they're inline shortcuts that appear in the prompt composer once your avatar is set up. I tested four of them across the weekend, and the choices Google made about which templates to surface tell you exactly which audience this product is for.

**Metallic** wraps your avatar in a chrome-green metallic skin effect. Imagine the T-1000 from Terminator 2 — that's the visual reference. I ran it once on my avatar and the surface treatment was genuinely impressive: light bent and reflected off the contours of my face the way actual liquid metal would, the eye sockets stayed properly defined, the lip movements still registered through the chrome plating. It's a TikTok effect. It is unambiguously designed to be a TikTok effect. And it works on the first try, no prompt engineering required.

**Meme Me** generates a clip of your avatar doing one of a handful of meme-coded reactions — surprised face, slow nod, head-shake, the whole "this is fine" universe of internet visual grammar. Again, the audience is obvious. You're not generating this for a corporate video. You're generating this to send to a group chat.

**Indie Pastel** is the one that surprised me. It applies a Wes Anderson-style aesthetic — centered symmetric composition, soft pastel palette, slightly arch framing, the deadpan stare straight into the lens. I dropped my avatar into it expecting a parody and got back a clip that looked like a tonal homage to a Wes Anderson short. The color grading was deliberate, the framing was correct, the camera held a perfectly level horizon. If I had been told this was a frame from an actual indie short, I would not have flagged it. That template alone tells you Google has dialed in some specific film-grammar references into the model's conditioning, and the results are sharper than I expected from a stylistic preset.

**Montage** is the structural template. It accepts five images plus one video as inputs (that's the hard upload cap per project, which I'll come back to) and compiles them into a single ten-second cut with transitions, pacing, and ambient soundtrack. This is the one I think creators will actually use the most, and it's the one that hints most strongly at where Google is taking Omni next. The Montage template isn't a stylistic filter — it's a structured editing operation. The model is making decisions about cut length, transition type, and audio bed based on the visual content of your inputs. That's the same compositional logic a video editor charges $50-150 an hour to apply manually. Omni applies it in about ninety seconds.

The pattern across all four templates is the same: they're optimized for short-form social content, not for narrative or commercial production. You're not going to generate a thirty-second product launch video with Omni Flash. You're going to generate a ten-second reel.

That's not a limitation, by the way. That's a strategic choice. According to [the rollout coverage at Gagadget](https://gagadget.com/en/711206-googles-gemini-omni-turns-anything-into-video-and-its-free-on-youtube-shorts/), Omni Flash is being made available for free inside YouTube Shorts and the YouTube Create app — exactly the surface where ten-second clips with templated effects are the dominant content format. Google didn't build a Hollywood tool. They built the engine for the next billion vertical-video clips.

## Custom Prompts: Where the Model Actually Stretches

The templates are fun. The custom prompt field is where the model actually shows you what it can do.

I tested two prompts that I think most clearly demonstrate the range. The first was the restaurant scene I described in the opener: *"My avatar sitting at a candlelit restaurant table, charcoal suit, eating spaghetti, warm key light from the right, slight smile when chewing."* Ten seconds, mid-shot, no dialogue. Generation time was under two minutes — I clocked it at one minute forty-three seconds the first time, one minute fifty-eight seconds the second. The output had the avatar quality I described earlier, the lighting setup was correctly inferred from the "warm key light" instruction, and the spaghetti — yes, the spaghetti was still wrong, but it was the kind of wrong that I think pasta physics will keep being wrong for another couple of model generations.

The second prompt was more ambitious: *"My avatar as a college math professor teaching a quantum mechanics lecture. Schrödinger equation written on a chalkboard behind him. Tweed jacket. Mid-gesture, explaining a concept. No dialogue audio."* This one is the test I run on every video model that touches educational content, because it forces three things simultaneously — recognizable subject-matter accuracy (the equation has to look like the actual Schrödinger equation, not generic squiggles), period-correct costume (tweed jacket has a specific cut and texture), and naturalistic gesture (a professor mid-lecture has body language that's distinct from a person standing still).

Omni nailed the equation. The chalk-written formula on the board behind my avatar was the actual time-dependent Schrödinger equation — iℏ ∂ψ/∂t = Ĥψ — with proper notation and proper proportions. I checked the freeze frames. The chalk grain was right, the spacing was right, the i and ℏ were positioned correctly. That's not a stylistic flourish. That's the model integrating semantic knowledge from Google Search and rendering it as a visual artifact in the generated clip, which Google's engineering blog discussed in the launch context as "historical and contextual accuracy via Search integration."

The tweed jacket was correct. The gesture was correct. The clip held together for the full ten seconds.

This is the demo that made me stop thinking of Omni as a TikTok effects engine and start thinking of it as something genuinely useful for explainer content. I work on tutorials and product reviews. The amount of time I currently spend either filming myself on camera or hiring a presenter to film themselves is non-trivial. If I can generate a ten-second cutaway of "my avatar explaining the concept I just outlined" for educational filler in a longer YouTube video, that's a real workflow change.

The customization I really want to call out, though, is the editing. After I generated the professor clip, I changed the tweed jacket to a navy blazer with one follow-up prompt: *"Same scene, change the jacket to a navy blazer, leave everything else identical."* The follow-up rendered in about ninety seconds and came back with — the jacket changed, the equation preserved, the chalkboard preserved, the gesture preserved, the lighting preserved. That kind of selective edit was, until about a year ago, an effects-house workflow. You'd have a junior compositor isolate the garment, track it across the clip, and apply the color change frame by frame. Omni did it in chat.

This is the in-chat editing capability that, per [TechCrunch's launch coverage](https://techcrunch.com/2026/05/19/googles-gemini-omni-turns-images-audio-and-text-into-video-and-thats-just-the-start/), is the actual flagship feature Google is pushing. You don't render a video and then edit it in a timeline tool. You edit it in the same chat where you generated it. Swap a prop. Change the lighting. Switch the wardrobe. Adjust the gesture. All from text.

## Video Continuation: The Feature Nobody's Talking About Enough

The clip length cap is ten seconds. That's the headline limit, and the limit that most reviews fixate on.

What most reviews are missing is that Omni supports video continuation from the last frame of an existing clip. You generate ten seconds. You like it. You ask the model to continue from the final frame, with a new prompt describing what happens next. Omni renders another ten-second segment that starts exactly where the first one ended.

In practice, this means the ten-second cap isn't really a ten-second cap. It's a ten-second-per-shot cap. You can chain shots together by continuing from the previous frame, and the avatar consistency holds across the continuation boundary far better than it does across two independent generations of the same prompt. The avatar's lighting carries over. The setting carries over. The framing carries over. The transition is invisible in the final cut as long as you don't change the camera position dramatically in the continuation prompt.

I tested this with the restaurant scene. Original clip: my avatar eating spaghetti. Continuation prompt: *"Continue from the last frame. My avatar looks up, sees someone enter the restaurant off-screen, sets down the fork, dabs his mouth with the napkin."* The second clip rendered, again under two minutes, and the cut between the two was clean. You could see the same plate of spaghetti, the same candle, the same lighting, the same posture. The hand-off was — I won't say invisible, because if you slowed it down to 60fps and looked at the exact frame boundary you'd see the subtle hand-off — but at normal playback speed it looked like a single continuous take.

This is the feature that turns Omni Flash from a clip generator into something closer to a sequence generator. Ten seconds doesn't sound like a lot until you realize you can chain three of them into a thirty-second narrative arc with consistent character continuity. That's enough for a YouTube Short. That's enough for a TikTok. That's enough for an Instagram Reel. That's enough for an explainer cutaway.

The compute model behind this is the part that makes me suspect the ten-second cap is temporary rather than architectural. According to the launch reporting, the cap is "a deployment decision rather than a model constraint — a way to widen access while compute demand is high." Read between the lines: when Google's TPU buildout catches up to demand, expect that cap to relax. We've already seen the same pattern with image-generation tools where launch caps quietly doubled or tripled within the first quarter of availability.

## The 5+1 Upload Limit and What It Means For Your Workflow

The hard input cap for any Omni project is five images plus one video. That's the ceiling. You cannot upload a sixth image. You cannot upload a second video. You cannot work around it by attaching a Google Drive folder.

That cap matters because it forces a discipline I think most creators will benefit from. You can't dump your whole content library into Omni and hope the model figures out a montage. You have to pick five images. You have to pick one video. The selection itself becomes the creative decision. The constraint is the feature.

In my testing, the most productive use of this cap was reference-anchored generation. Upload a single video that represents the visual style I want (a dolly shot through a kitchen, for example), upload five images that represent the subjects and props I want featured, and then write a prompt that references them. Omni uses the uploads as visual anchors — not just as raw inputs to be composited, but as conditioning signals that bias the output toward the aesthetic of the uploaded reference.

The volcano demo is the one I want to walk through here, because it's the demo that surprised me the most. I uploaded a short clip I'd shot from the dashboard of my car driving through the New England countryside — fall foliage, two-lane road, no traffic. The prompt: *"Take the original driving footage. Keep the road, the trees, the lighting, the foliage colors. Add an active volcano on the horizon in the distance, with visible smoke and lava glow, like it's been there the whole time."*

The output was a ten-second clip of my original driving footage with an active volcano composited into the distant skyline. The original scenery — every tree, the road surface, the painted lines, the lighting — was preserved. The volcano was integrated with appropriate atmospheric haze, depth-of-field consistency, and a subtle orange cast on the trees nearest to it. The smoke rose at a realistic angle for the implied wind direction in the original footage. It looked like I had driven past a volcano.

That's not a stylistic edit. That's structural compositing inside a moving video, with preserved scene context, executed from one prompt. The closest analog I have for this in traditional VFX work is a junior compositing pass that would take a working day on a single shot. Omni did it in under two minutes.

If you produce travel content, real-estate walkthroughs, product demos in physical settings, or any kind of B-roll that needs a hypothetical element added or removed, this capability changes your production math entirely. You're not shooting in the location with the volcano. You're shooting in the location you have, and Omni is adding the volcano in post.

## The 720p Watermark and Why I'm OK With It

Now the limits.

Every Omni Flash export comes out at 720p. That's the resolution cap at launch. There's also a visible watermark in the corner of every clip — the Gemini sparkle mark — in addition to the imperceptible SynthID watermark that's embedded in every frame of every Google generative-video output. The visible watermark is removable in the YouTube Shorts integration (where the clip is being uploaded directly into YouTube's surface), but on direct exports through the Gemini app, the visible mark stays.

I'm OK with this. I want to explain why.

720p is below the bar for premium video production. If you're shooting a hero brand campaign, a commercial spot, or a high-production-value YouTube long-form piece, 720p won't cut it. You want 4K-mastered sources for cropping, grading, and platform-specific re-encoding. Omni Flash is not the tool for that work, and Google is explicit about this — Omni Flash is positioned as the access tier, with higher-resolution variants implied for future releases.

But 720p is *above* the bar for short-form social. Instagram Reels are served at 720p on most playback devices regardless of source resolution. TikTok playback caps at 1080p in most regions and 720p in many bandwidth-constrained markets. YouTube Shorts is served at variable resolutions optimized for the playback device, and for the majority of mobile viewers, the difference between 720p and 1080p is imperceptible at the playback viewport size.

The watermark is a clearer trade-off. If you're a brand and your final asset needs to ship without third-party branding, Omni Flash isn't yet a finished-good production tool. If you're a creator generating B-roll, cutaways, social-feed content, or remix material that lives inside a YouTube Shorts or TikTok layer, the watermark either lives natively (in the YouTube integration) or gets covered by overlay graphics in your editor (in most short-form workflows where text and stickers already cover the corners).

The deeper read on the watermark, though, isn't about resolution or aesthetics. It's about the SynthID provenance layer that's embedded in every frame. I covered this in [the IO 2026 recap](/content/mejba.me/google-io-2026-gemini-omni-spark-recap.md) and I want to underline it here because I think most creator coverage is missing the point. The watermark isn't a limitation. The watermark is a feature for the next ten years of AI-generated media governance. As detection-API integration spreads (and SynthID is now backed by NVIDIA, OpenAI, ElevenLabs, and Kakao, per Google's announcement), the absence of a watermark on AI-generated content is going to start looking like a red flag, not a clean export. Omni's visible mark plus invisible mark is Google staking out the position they want to occupy when the regulatory wave hits.

## Comparing Omni Flash to Sora 2, Veo, Runway, and Kling

You can't talk about Omni without talking about the field it just stepped into. Let me give you the honest comparison from the weekend's testing.

**Sora 2.** The big context piece here is that OpenAI [shut down the consumer Sora 2 app on April 29, 2026](https://nerdbot.com/2026/05/15/game-over-for-sora-how-seedance-2-0-and-gemini-omni-are-winning-the-ai-video-wars/), retaining the model only as a paid API offering. That left a Sora-shaped hole in the consumer creator market, and Omni Flash is the product that filled it. Capability-wise, Sora 2 still produces excellent output on the API side and remains technically competitive, particularly for longer-form narrative shots. But Sora is no longer a consumer product. If you're a creator who wants to open an app and generate a clip with your face in it, Omni Flash is the closest analog now available.

**Veo 3 / Veo on Vertex.** Google's own previous video model, still alive on the enterprise side via Vertex AI. Veo is positioned for higher-resolution, longer-duration, professional production workflows. Omni Flash is positioned for short-form, consumer creator workflows. They share lineage — and in the IO keynote Google described Omni as the next chapter of work that includes Veo, Nano Banana, and Genie — but they're not competing for the same user. Veo is the studio engine. Omni Flash is the app.

**Runway Gen-3 and Gen-4.** Runway remains the strongest professional creator tool for filmmakers, music video producers, and ad-agency teams who want timeline-style editing on top of their generative output. Runway's Director Mode and motion brush gives you finer control over individual elements in a frame than Omni's chat-based editing currently does. If you're producing a polished commercial or a music video, Runway is still the more mature tool. If you're producing a ten-second social clip with your avatar in a stylized template, Omni is faster and the avatar consistency is better.

**Kling 2.x.** The Chinese-developed Kling model is the dark horse most Western coverage underrates. Its physical motion quality — fabric simulation, water, fire, human gait — is genuinely class-leading. If you're generating clips that hinge on physical realism (a person walking through tall grass, a cup of water spilling, a flag in wind), Kling is still my first call. Where Kling lags is the integrated avatar workflow and the in-chat conversational editing. You generate in Kling, you edit elsewhere. Omni keeps everything in one surface.

The pattern across all of this: Omni Flash isn't the best at any single dimension. It's not the highest-resolution. It's not the longest-clip. It's not the most physically realistic. What it is, is the most *integrated* — your face, your prompts, your edits, your exports, all inside one chat. That integration is the moat. And it's the same moat that made Sora 1's original consumer app feel magical when it launched, before OpenAI pulled it back.

If you're a serious filmmaker, you're not going to abandon Runway or Kling. If you're a creator who wants to ship a clip before lunch, Omni Flash is the fastest path I've found.

## What I'd Use Omni Flash For Tomorrow

Let me skip the abstractions. Here is the actual list of workflows I'm planning to migrate to Omni Flash this week.

**Educational cutaways for long-form video.** I produce explainer content. Right now, when I need a ten-second cutaway of "me at a whiteboard sketching the concept I just outlined," I either film it (forty-five minutes of setup, lighting, recording, retakes) or skip it. With Omni Flash, that cutaway is two minutes of prompting plus a thirty-second touch-up edit. The cost-per-cutaway dropped by a factor of about twenty.

**Thumbnail-style B-roll.** Hero shots for YouTube thumbnails that need to be motion-frames, not static images, because YouTube's thumbnail A/B testing favors short motion previews on the hover state.

**Social-first product demonstrations.** A ten-second clip of my avatar holding a product in different settings — at a desk, in a cafe, on a beach — for use as platform-native ad creative inside Instagram and TikTok ad tools. Variant generation that previously required a photoshoot now requires four prompts.

**Tutorial intros.** Ten seconds of branded animation at the start of a tutorial video, with my avatar greeting the viewer. Previously a Hyperframes or After Effects task ([I wrote about the Hyperframes + Claude Code pipeline here](/content/mejba.me/ai-video-creation-hyperframes-claude-code.md) — that still wins for fully deterministic, programmatic motion graphics). Now an Omni task for the avatar-presence variants where I want my actual face in the intro.

**Reference visualization for client work.** When I'm pitching a video concept to a client, instead of describing it or mocking up a static frame, I generate a ten-second Omni clip that represents the concept. Two minutes of work. The client sees the actual visual feel before signing off.

That's five workflows. Each one of them used to consume thirty minutes to a day of production time. Each one of them now takes under five minutes inside Omni Flash. Aggregate that across a week of content production and the math is — bluntly — embarrassing for any tool that came before it for these specific use cases.

## Where Omni Falls Down (And Why It Doesn't Matter Yet)

Honest list of limits.

Avatar consistency across regenerations is still imperfect. The first frame of a regenerated clip with the same prompt sometimes shows slight drift in face proportions before the model locks back in. If you need pixel-perfect frame-by-frame consistency between two separately-generated clips, Omni isn't there yet. The continuation flow handles this internally; back-to-back regenerations of the same prompt do not.

Audio is limited. Omni Flash deliberately does not include personal voice cloning at launch, and the generated audio is ambient and atmospheric rather than dialogue-driven. If you want a clip with your avatar speaking lines, you generate the visual with Omni and dub the audio elsewhere — through ElevenLabs, your own voice recording, or a separate TTS pipeline.

Hand and object physics are still pasta-grade. Anything involving fine-grained physical interaction — eating, drinking, handwriting, tool use — has visible artifacting. The macro-level scene is convincing. The micro-level physical detail is the next frontier.

10-second clips chained via continuation are not the same as native 30-second clips. The continuation feature is excellent and the seams are clean at normal playback speed, but if you're producing single-take cinematography for narrative work, you want a model with native long-clip generation. Omni Flash isn't that model. Future Omni variants probably will be.

The 720p ceiling is real. Not for short-form, but for anything heading to a delivery spec above 1080p, you need a different tool in your stack.

None of these limits matter for the use cases I outlined in the previous section. They matter a lot if you're trying to produce a feature film, a high-end commercial, or premium long-form video work. Don't use Omni Flash for the wrong job and don't expect the wrong job to get done. Use it for the jobs it's designed for, which happen to be the jobs about ninety percent of working creators actually have to ship every week.

## The Verdict, And The Number That Stuck With Me

After two days of testing, I generated forty-seven clips. Twenty-eight of them I would consider usable in some production context — either as final assets or as B-roll inside a larger edit. That's about a sixty percent useful-output rate, which is dramatically higher than any consumer-facing AI video tool I've tested previously. The next-closest comparison in my testing has been around thirty to thirty-five percent.

The combination of consistent avatar identity, fast generation time (under two minutes for ten-second clips), conversational in-chat editing, and structured-but-flexible templates makes Omni Flash the first AI video tool I've used where I caught myself thinking *I want to keep using this* rather than *I want to write up my experience using this and move on*. Those are different reactions and they tell you something about whether the tool actually slots into your workflow.

The number that stuck with me, though, isn't the sixty percent. It's the time-per-edit. I made fourteen edits to clips this weekend — outfit changes, scene modifications, prop swaps, lighting adjustments. The average time from edit prompt to rendered output was ninety-two seconds. The average time from intent to outcome in a traditional editing workflow for an equivalent change is somewhere between fifteen minutes (for a simple color tweak) and a working day (for a full prop replacement). When the unit cost of a creative iteration drops from minutes-to-days into a sub-two-minute window, the entire creative workflow changes. You don't agonize over the first take, because there's no cost to generating the second one.

Remember the avatar I trained on Friday night? I just generated a new clip of him for this post — same face, same likeness — sitting at a desk typing on a laptop with the words "Gemini Omni Hands-On" on the screen behind him. It took ninety-six seconds. I wrote the prompt while drinking the last sip of my coffee. The coffee was cold by the time I started this paragraph and the clip was done before the next paragraph. That's the workflow.

If you're a creator and you haven't generated your avatar yet, do that this week. The setup takes ninety seconds, the first clip takes another two minutes, and the moment you see your own face do something you didn't film is the moment your sense of what's possible in short-form video production resets. Don't take my word for it. Generate the spaghetti scene. See what comes back.

## Frequently Asked Questions

### How long does it take to generate a clip in Gemini Omni?
Generation time averages under two minutes for a 10-second clip in my testing — I logged times between 1:43 and 1:58 across the weekend. Edits to existing clips ran slightly faster, around 90 seconds on average. For the full timing breakdown across different prompt types, see the Custom Prompts section above.

### What is the maximum clip length in Gemini Omni Flash?
Individual clips are capped at 10 seconds in Omni Flash, but you can chain clips together using the video continuation feature, which extends from the last frame of the previous clip. The 10-second cap is a deployment decision tied to compute availability, not an architectural model limit, so expect it to relax in future releases.

### Does Gemini Omni support video editing without regenerating?
Yes, Omni supports conversational in-chat editing as a first-class feature. You can change wardrobe, swap props, adjust lighting, or modify scene elements with a follow-up prompt and the model preserves the rest of the clip. This is the flagship capability differentiating Omni from prior Google video models.

### Can I use my own face in Gemini Omni videos?
Yes, through the avatar setup under "more uploads" in the Gemini app. You record a 90-second facial scan with voice-prompted head movements (liveness check), and the trained avatar becomes a reusable asset you can reference in any subsequent prompt. Personal voice cloning was deliberately not shipped at launch.

### How does Gemini Omni compare to Sora 2?
OpenAI shut down the consumer Sora 2 app on April 29, 2026, retaining the model only as a paid API. Omni Flash now occupies the consumer-creator video generation slot Sora 2 used to fill. Sora 2 remains competitive at the API level, but for a self-contained app-based creator workflow with avatar consistency, Omni Flash is the current best option.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
