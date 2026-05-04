**BRAND:** mejba.me
**TITLE:** Gemma Chat: Offline Vibe Coding on Mac, Tested
**META TITLE:** Gemma Chat Tested: Offline Vibe Coding on Apple Silicon
**SLUG:** gemma-chat-offline-vibe-coding-mac
**PRIMARY KEYWORD:** Gemma Chat offline vibe coding Mac
**META DESCRIPTION:** I installed Gemma Chat on my M-series Mac and ran the Gemma 4 E4B model fully offline. Here's what offline vibe coding actually feels like in 2026.
**TAGS:** Gemma 4, Local AI, Apple Silicon, Vibe Coding, MLX

---

It was raining. My MacBook was on a kitchen counter, Wi-Fi off because I'd been on a flight earlier and forgot to flip it back on, and I was halfway through generating a small landing page when I realized I hadn't typed a single API key all morning. No Anthropic. No OpenAI. No Cloudflare tunnel. No Ollama process I'd configured a month ago and forgotten about. The only thing running was an Electron app called Gemma Chat, a little Python virtual environment churning quietly in the background, and a 3 GB model file that lived entirely on my SSD.

The page was finished in about ninety seconds. Hero section, three-column feature grid, a footer with social icons. Tailwind classes, semantic HTML, no broken markup. I closed the lid and opened it on the train. Still worked. No reconnection prompt. No "session expired." No quota meter ticking down in the corner of my screen.

That's the part of Gemma Chat offline vibe coding Mac workflows that nobody really tells you about until you experience it: the silence. You stop hearing the network. You stop refreshing your billing page. You stop refactoring prompts to save tokens because there are no tokens to save — there's just your laptop fan, occasionally, when the model has to think harder.

I've been testing Gemma Chat for about three weeks now. I want to walk you through what it actually is, what works, what's still rough, and the specific moment where I figured out whether this is a serious tool or a curious science project. There are real answers in here — but a few of them surprised me.

## What Gemma Chat Actually Is (And Why It Showed Up Now)

Gemma Chat is an open-source Electron app built by [Ammaar Reshi](https://github.com/ammaarreshi/gemma-chat), the Product and Design Lead on Google AI Studio at DeepMind. It's MIT-licensed on his personal GitHub, not an official Google product, but it ships under his name and the official Google Gemma account has been amplifying it. The app wraps Apple's [MLX framework](https://github.com/ml-explore/mlx) and the [MLX-LM](https://github.com/ml-explore/mlx-lm) Python package around Google's Gemma 4 model family, then puts a chat-and-build interface on top.

The why-now part matters. Google released Gemma 4 on April 2, 2026, and the new architecture is what makes this whole experience viable on consumer hardware. The "E" in E2B and E4B stands for *effective* parameters — Per-Layer Embeddings (PLE) let the model run with a 2 GB or 3 GB memory footprint while behaving like something much larger. Apache 2.0 license, multimodal input across all four sizes, native audio on the smaller models, 128K context on E2B/E4B and 256K on the 26B A4B and 31B Dense variants. That's the toolkit. Gemma Chat is one of the first really polished consumer apps wired up to use it.

If you've followed [my earlier deep dive on Gemma 4](https://www.mejba.me/google-gemma-4-series-tested) you know the model family is strong. The question with Gemma Chat is different: not "is the model good?" but "is the *agent loop* good enough to feel like Cursor or Claude Code, when there's no Anthropic or OpenAI server in the picture?"

Short answer: closer than I expected. With caveats.

## The Setup, And The First Thing That Surprised Me

I installed it on an M3 Pro MacBook with 18 GB of unified memory. The README lists Python 3.10 to 3.13, Node 20 or higher, and npm. Nothing exotic.

```bash
git clone https://github.com/ammaarreshi/gemma-chat
cd gemma-chat
npm install
npm run dev
```

The first launch is where the magic gets a little misleading. Gemma Chat auto-provisions its own Python virtualenv, installs `mlx-lm`, downloads the model weights from Hugging Face, and only then opens the chat window. On my connection that took about seven minutes for the E4B variant. The download is roughly 3 GB once quantized.

Here's the surprise: from that moment on, I never needed an internet connection again unless I deliberately invoked a tool that required one. The first time I confirmed this was Sunday afternoon. I airplane-moded my Mac, restarted the app expecting a "no network" error, and instead got a clean cursor blink and a welcome message. Total cold start time on my hardware: about four seconds.

That's the bar local AI has to clear to feel real. Open it offline. Have it just work. Gemma Chat clears it.

## The Two Modes, And Why The Distinction Actually Matters

Gemma Chat has two modes that look superficially similar but operate on completely different mental models.

**Build mode** is the headline feature. You give it a prompt — "build me a landing page for a fictional dog-walking startup" — and it streams a multi-file project into a workspace folder. HTML, CSS, JavaScript, sometimes a `package.json`, sometimes a small Python script if the task warrants it. There's a live preview pane that reloads as files are written. You can iterate on it: "make the hero darker," "swap the testimonials section for a pricing grid," "add a contact form that posts to a fake API." Each instruction triggers another agent loop.

**Chat mode** is the everyday assistant. Web search and command execution are available here, but they require internet. Without Wi-Fi, chat mode becomes a code-aware conversation partner without retrieval — useful, but limited. The split is honest. Build mode is the part that's truly offline; chat mode tells you when it needs the network.

The reason this distinction matters: most people compare Gemma Chat to Cursor or Claude Code and form expectations based on what those do. Those tools assume a frontier model on the other end of an API call. Gemma Chat assumes a 3 GB model on your local SSD and a sandbox folder. The shape of the work is different. You don't ask it to refactor 50,000 lines of legacy Java. You ask it to build the kind of thing you'd build over coffee.

That's a feature, not a limitation. I'll come back to why.

<!-- IMAGE: Screenshot of Gemma Chat's dual-pane interface with the build mode prompt on the left and live preview on the right, showing a generated landing page being assembled in real time. Alt text: "Gemma Chat offline vibe coding Mac interface showing build mode generating a landing page". Caption: "Build mode running entirely offline on an M3 Pro — the live preview reloads as files are written to disk." -->

## The Model Variants, And The One I Settled On

Gemma Chat lets you hot-swap between the four Gemma 4 sizes from a dropdown in the top-right. I tested all of them. Here's how each one actually felt in real coding work, not benchmark theater.

**E2B (effective 2B parameters).** This is the model designed for 8 GB Macs and the smaller half of the iPad Pro lineup. It generates fast — I clocked it at around 28 tokens per second on my hardware — but it cuts corners. Asking it to scaffold a React component with three pieces of state? It'll forget the third piece by the time it writes the JSX. Asking it to generate a landing page with a specific color palette? It'll get the palette right but invent class names that don't exist in Tailwind. Fine for prototyping ideas. Frustrating for anything you want to ship.

**E4B (effective 4B parameters).** This is where it gets genuinely useful. ~3 GB on disk, and on my M3 Pro it runs at roughly 50 tokens per second sustained. The recommended sweet spot for a reason: it follows multi-step instructions, it remembers what's in the workspace folder, and it makes fewer hallucinations about library APIs. This is the variant I've left selected as my default. If you have 16 GB of unified memory or more, start here.

**26B A4B (Mixture of Experts).** Quality jump is real. This is the variant where I started getting the kind of output where I'd squint at the code and think "okay, that was a sensible architectural choice." The MoE design means only about 4 billion parameters are active per inference step, so it's roughly 2 to 2.5x faster than the dense 31B for similar quality. But the catch: it wants 16 GB of free memory minimum, and on a 16 GB Mac you're flirting with swap. I'd recommend it for 32 GB Macs primarily.

**31B Dense.** The strongest reasoning, currently sitting at #3 on the open-model Arena leaderboard. On my 18 GB MacBook, I could load it but barely — the rest of my apps got squeezed and I started seeing memory pressure. On a 32 GB or 64 GB MacBook Pro this is the one to beat. For Gemma Chat specifically, though? E4B and 26B A4B handle 95% of what the app is designed for. The 31B is overkill for landing pages and underkill for replacing your senior engineer.

The honest take after three weeks: I almost never switch off E4B. The cases where I want more reasoning are also the cases where I'd rather just open Claude Code and hit a frontier model anyway.

## What's Actually Happening Under The Hood

This is the part most reviews skip, and it's where Gemma Chat is most clever.

When you submit a build-mode prompt, the Electron app sends it to a local MLX server running in the auto-provisioned Python virtualenv. The MLX-LM package handles inference using Apple's Metal-accelerated MLX framework — it's not just running on the CPU, it's hitting the unified memory architecture and the Neural Engine where it can. That's why the tokens-per-second numbers are competitive with cloud inference for models this size.

The agent loop uses an XML-style tool protocol instead of JSON function calling. That detail matters more than it sounds. JSON function calling is what most cloud APIs use — you give the model a tool schema, it returns structured arguments. But smaller models are notoriously bad at producing valid JSON under pressure; they break the schema, they truncate, they hallucinate field names. XML tags are far more forgiving for a 4B-parameter model. The tool calls look something like this:

```xml
<write file="index.html">
<!DOCTYPE html>
<html>
  <head><title>Dog Walking Co.</title></head>
  ...
</html>
</write>

<bash>
npm install tailwindcss
</bash>
```

The Electron app parses these tags as a stream, writes content to disk in real time, and triggers a workspace reload. That's why you see the preview pane updating *as the model types* — files are flushing partial content continuously, not waiting for the full response. It's a small architectural choice that makes the whole experience feel responsive.

The available tools are narrow on purpose: write a file, edit a file, read a file, list files, run a bash command in the sandbox. No git. No package publishing. No "deploy to Vercel." That's the right call for an MIT-licensed local agent that runs unattended on your laptop.

If you'd rather have a custom local agent setup tuned for your specific workflow rather than the defaults Gemma Chat ships with, that's exactly the kind of thing I take on as a Fiverr project — you can [see what I've built at fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## What I Built, And What Broke

I gave Gemma Chat seven real tasks across three weeks. Not toy benchmarks — actual things I wanted to exist. Here's how each one actually went.

**Task 1: A landing page for a fictional SaaS product.** E4B, build mode. Took about ninety seconds. Output was clean Tailwind, semantic HTML, three CTAs that all linked to `#`. Asked it to swap the testimonial section for a pricing table. It did. Asked it to add a dark mode toggle. It added one — but the toggle didn't actually persist across page loads because it forgot to write to localStorage. Fixed it on the second prompt. Total time: under five minutes. Result was usable as a real prototype.

**Task 2: A small browser-based pomodoro timer.** E4B, build mode. Single HTML file with embedded CSS and JS. Worked first try. Notification permissions handled correctly. Audio cue for end of session. The kind of thing I'd normally bang out in 20 minutes was done in ninety seconds.

**Task 3: A Tetris clone.** This is where things got interesting. E4B couldn't quite hold all the game logic in its head — collision detection got muddled, line-clearing logic was off by one. Switched to 26B A4B and tried again. Cleaner output, playable game, but the rotation logic for the S-piece was still subtly wrong. Switched to 31B Dense. Got a working Tetris on the second attempt with a fix prompt. Total time: about 25 minutes including model swaps. A frontier cloud model would have done this in one shot, but I never opened a cloud API.

**Task 4: Refactor an existing 800-line Python script.** Build mode handles workspaces, but the model has to fit the relevant code into context. With E4B's 128K context that's not a problem in raw tokens — it's a problem in *attention*. The model lost track of which functions called which around the middle of the file. The output compiled but introduced a subtle bug where a regex was rewritten to be more permissive than the original. I caught it because I'm picky. A junior dev on autopilot wouldn't have. **This is where Gemma Chat falls down.** Don't use it for nuanced refactoring of code you didn't write.

**Task 5: A small dashboard pulling from a JSON file.** E4B, build mode. Worked beautifully. Created the dashboard, parsed the JSON, built filter controls, added a chart. Twenty seconds of generation, no fixes needed. This is the sweet spot.

**Task 6: A Chrome extension manifest.** E4B couldn't get manifest v3 right. It kept producing v2 syntax with v3 keys mixed in. 26B A4B got it right on the first try. Specific knowledge that's both recent and detailed is where the bigger model earns its keep.

**Task 7: An animated SVG hero section using Lottie.** E4B got the structure right but invented Lottie API methods that don't exist. 26B A4B used real ones. This is a recurring pattern: when the task involves a specific library's API surface, the smaller model hallucinates more. The bigger model has actually seen the library.

The pattern: anything self-contained, common, and shorter than 500 lines? E4B is great. Anything that requires precise knowledge of a specific library, a longer file, or careful reasoning across many parts? Step up to 26B A4B or 31B.

## The Voice Input Surprise

Buried in the feature list is local voice input via [transformers.js](https://github.com/huggingface/transformers.js) running an in-browser Whisper model. I almost ignored it. I'm a keyboard person. I assumed it'd be the kind of feature where you tap the mic, speak, and watch the transcription crawl in five seconds behind your voice.

It's not. On the M3 Pro, it's near-realtime. Not Siri-fast, but conversational. And because it's running entirely in the Electron renderer process via WebAssembly and ONNX, it works offline too. The whole stack — Whisper for STT, Gemma 4 for code generation, MLX for inference, transformers.js for the audio frontend — never touches a network after initial setup.

I've started using it for build-mode prompts when I'm pacing the room. "Build a landing page for a coffee subscription service, dark theme, three pricing tiers." The voice gets transcribed locally, the prompt goes to the local MLX server, the model writes files locally, the preview reloads locally. There is something genuinely strange about watching a coding agent obey a voice command with no network indicator lighting up anywhere on the machine.

## Where The Whole Thing Falls Apart

I want to be specific here, because every Gemma Chat post I've read so far has been weirdly cheerleading. The honest list of where this tool stops working:

**Production code is out.** Gemma Chat is not a replacement for Cursor or Claude Code on real work. The model isn't strong enough for nuanced refactoring, multi-file architectural changes, or anything where you need to trust the output without reading every line. It's a prototype factory.

**Mac-only is a real limitation.** MLX is Apple's framework. There's no Windows or Linux build, and there isn't going to be one. If you work on a non-Mac team, this isn't a tool you can recommend universally.

**The agent loop is shorter than what you're used to.** Cloud agents like Claude Code happily run for ten minutes on a single prompt, calling dozens of tools, reading entire codebases. Gemma Chat's loop tops out earlier — partly because the model is smaller, partly because the tool surface is intentionally narrow. You'll notice this most when you ask for something complex and it stops three steps in.

**Sandboxing is a polite fiction.** The bash tool runs in your workspace folder, but if you `cd ..` and start running things, it's running them with your user permissions. Gemma Chat is for trusted local workflows, not for executing untrusted prompts from external sources. This is actually the same constraint as every other agent tool, but it bears repeating because the offline framing makes it feel more sandboxed than it actually is.

**Initial setup is developer-coded.** The README assumes Python, Node, and npm are concepts you understand. There's no double-click installer. If you're a non-developer who heard about local AI and wanted to play, this isn't your entry point yet. The friction is real.

**No fine-tuning hooks.** You're stuck with the four Gemma 4 variants the app offers. You can't point it at a custom model you trained, and there's no plugin system to extend the tool surface. For a v1 of a side project this is fine. For your daily driver, it's a ceiling.

## The Mental Model That Made This Click For Me

After about a week of use I had a small realization that reframed how I thought about the whole tool.

Gemma Chat isn't trying to be Claude Code. It's trying to be the local equivalent of [Google AI Studio's Vibe Coding experience](https://aistudio.google.com/), shrunk down and made portable. Ammar Reshi works on AI Studio. The interface choices reveal that lineage: the build mode, the live preview, the prompt-to-app speed, even the way iteration feels conversational rather than transactional. It's vibe coding pulled out of the cloud and stuffed into an Electron window.

That's a different design goal than what cloud coding agents are optimizing for. Cloud agents want to reach into your repo, understand your team's conventions, and replace tasks you'd assign to a junior engineer. Vibe coding wants to compress idea-to-prototype to under sixty seconds.

Once I stopped expecting Gemma Chat to be a Claude Code competitor and started using it as a local prototype factory, the experience clicked. I stopped reaching for it for refactors. I started reaching for it when I wanted to materialize a half-formed idea before I lost the thread. "What would this onboarding flow look like?" — three minutes later, here's a working version. "What if the dashboard had a sparkline?" — done, evaluated, kept or discarded.

The faster you can move from idea to artifact, the more ideas you actually evaluate. That's the unlock. It's not faster than a cloud agent for the same task — it's faster than nothing, available everywhere, with no marginal cost. Different bar.

## The Privacy Argument, And Why It's Stronger Than It Sounds

The privacy story for local AI usually gets pitched as a defensive argument: "you don't have to send your code to the cloud." That's true, and for some use cases (regulated industries, NDA work, security-sensitive prototyping) it's the only argument that matters.

But there's an offensive version that I've started caring more about: you don't have to *think* about whether to send your code to the cloud. Every cloud-coding workflow has a small persistent friction where you're evaluating, even unconsciously, whether the prompt you're about to send is something you're comfortable sending. Client code? Probably fine. That weird half-formed business idea you don't want logged anywhere? Hesitate. A snippet of someone else's codebase you're debugging? Awkward.

Gemma Chat removes that question entirely. Whatever you type goes nowhere. Whatever it generates goes nowhere. There's no logging endpoint, no evaluation pipeline, no "we may use your data to improve our models" footnote. The model file on your disk is the entire product surface.

For the kind of half-formed exploratory work that's the most valuable use of an AI coding agent, this is genuinely freeing. You stop self-censoring. You explore more weird ideas. The output isn't necessarily better, but the *process* is. That feeds back into the [token-management discipline I wrote about earlier](https://www.mejba.me/claude-token-limits-context-hygiene) — when there's no token meter, you stop optimizing for one and start optimizing for ideas.

## Who Should Actually Install This

After three weeks I'd put real users into four buckets.

**Install today, no hesitation:** Apple Silicon Mac owners with 16 GB or more, who already do prototype-style work, who travel or work in spotty-Wi-Fi environments, who care about cost predictability, or who have any kind of NDA or sensitive code constraint that makes cloud APIs awkward.

**Install if you're curious:** Mac owners with 8 GB who want to experiment with E2B specifically. Performance is real but limited. Treat it as a science project, not a daily driver.

**Wait for now:** Anyone on Windows or Linux. Anyone whose primary workflow is large-codebase refactoring. Anyone who needs cross-platform team adoption. Anyone who already has a stable Cursor or Claude Code workflow they're happy with — Gemma Chat doesn't replace it, it sits beside it for a different category of work.

**Skip it:** Non-developers looking for "AI on my laptop" out of the box. The setup friction is too high right now. The path of least resistance for that audience is still LM Studio or Ollama with a friendlier wrapper. If you want a more turnkey local experience, [my earlier writeup on running Gemma 4 in LM Studio](https://www.mejba.me/gemma-4-lm-studio-local-ai-setup) is the easier on-ramp.

## What This Foreshadows

Gemma Chat is a v1 from a single person on a side project. It's not the polished product that's going to redefine local AI coding. But it's a proof of concept that the *primitives* are now in place: model architectures small enough to run on consumer hardware (Gemma 4's effective-parameter design), inference frameworks fast enough to feel like cloud (MLX), agent loops reliable enough at small parameter counts (the XML tool protocol), and supporting tech for the rest of the stack (transformers.js, Whisper, ONNX runtime).

Six months ago this would have felt impossible. Local models were too slow, agent loops broke too often, and the whole thing required a stack of separate tools you had to manually compose. Gemma Chat shows what happens when one designer-engineer with taste assembles those primitives into a coherent product.

The next move is bigger players noticing. The same way Ollama got copied into a dozen friendlier interfaces, the Gemma Chat pattern — local-first agentic coding with a polished UI — is going to get rebuilt by teams with more resources. When that happens, the offline vibe coding paradigm stops being a curiosity and starts being a serious second pillar of how developers work, alongside cloud agents.

I'd bet on that pillar growing fast through the rest of 2026.

## The Test That Actually Mattered

I almost forgot the most important test until I sat down to write this. Three days ago I was on a three-hour flight with no Wi-Fi. I'd been thinking about a project idea on the way to the airport — a small interactive storytelling demo, the kind of thing I'd usually pitch to myself, decide was too much work, and forget about by Tuesday.

I opened Gemma Chat at cruising altitude. E4B selected. Built the entire prototype across the flight. Six iterations. Three style refinements. By the time we descended I had a working demo of an idea that, three weeks earlier, would have lived in my notes app forever.

That's the real measurement. Not benchmarks. Not tokens per second. Whether a tool moves an idea from your head to a thing that exists in the world. Gemma Chat moved one off my list and onto my disk while my MacBook was in airplane mode at 35,000 feet.

The next time someone asks me whether local AI coding agents are "ready" — that's the answer I'm going to point at.

## Frequently Asked Questions

### What hardware do I need to run Gemma Chat?
Gemma Chat requires an Apple Silicon Mac (M1, M2, M3, M4, or M5 series) running macOS, with at least 8 GB of unified memory for the E2B variant and 16 GB or more for the recommended E4B model. The 26B A4B and 31B Dense variants need 16 GB and 32 GB respectively. There is no Windows or Linux build because the app depends on Apple's MLX framework.

### Is Gemma Chat completely offline?
After the initial model download, build mode runs entirely offline including code generation, file writes, and live preview. Chat mode includes optional web search and URL fetching tools that require internet, but those are clearly marked. Voice input via local Whisper also works fully offline.

### Is Gemma Chat a replacement for Claude Code or Cursor?
No. Gemma Chat is a local prototype factory designed for self-contained projects under a few hundred lines — landing pages, demos, small games, dashboards. For nuanced refactoring or large-codebase work, frontier cloud models still win. See the use case section above for where each variant performs best.

### Which Gemma 4 variant should I start with?
Start with E4B if you have 16 GB or more of unified memory — it is the recommended sweet spot. Use E2B only on 8 GB Macs, and step up to 26B A4B for stronger reasoning on 32 GB+ hardware. The 31B Dense is overkill for most Gemma Chat workflows.

### Is Gemma Chat free?
Yes. The app is MIT licensed, the Gemma 4 model weights are Apache 2.0 licensed, and there is no subscription or API cost. Your only cost is hardware and the initial download bandwidth (~3 GB for E4B).

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
