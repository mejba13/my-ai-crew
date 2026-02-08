**BRAND:** mejba.me
**TITLE:** Opus 4.6 Hands-On: I Tested It Three Real Ways
**SLUG:** opus-4-6-hands-on-review
**TAGS:** AI Development, Productivity, Opus 4.6, Claude Code, Review

---

I was halfway through debugging a game — a beat 'em up I'd been building with Claude Code — when something happened that made me stop and just stare at my terminal. The AI had hit a wall. The player health bar wasn't rendering. Instead of asking me what to do (like every previous model would have), Opus 4.6 quietly tried three different fixes on its own, figured out the root cause, patched it, and moved on. No hand-holding. No "I encountered an error, could you provide more context?" nonsense. It just... solved it.

That moment told me more about Opus 4.6 than any benchmark chart ever could. And it wasn't even the most impressive thing I saw that day.

Anthropic dropped Opus 4.6 right alongside OpenAI's CodeX 5.3 — the timing was deliberate, almost theatrical. Two companies, both claiming the coding AI crown, releasing their best work within hours of each other. I spent the past week putting Opus 4.6 through real workflows — not toy demos, not "write me a haiku" tests. I'm talking podcast production, game development, and presentation creation. The kind of work that actually eats my week.

Here's what I found — and why I think this model changes the relationship between developers and AI in a way that's hard to unsee once you've experienced it.

## The Five Upgrades That Actually Matter

Before I walk through the use cases, you need to understand what's different under the hood. Anthropic made five specific improvements, and honestly, three of them solve problems that have been driving me crazy for months.

### It Actually Remembers What You Asked For

Here's a scenario every Claude user knows. You start a conversation with a detailed system prompt. Forty messages in, the model has completely forgotten your original instructions. You're basically re-prompting every few turns, wasting time and tokens.

Opus 4.6 fixes this. Not partially — genuinely fixes it. I ran a conversation that went over sixty exchanges, and the model was still following my initial formatting rules and constraints from message one. That might sound minor on paper. In practice, it eliminates an entire category of frustration.

I tested this specifically during the podcast workflow (more on that in a minute). My prompt was long — probably 800 words of detailed instructions about tone, formatting, and output structure. By the twentieth back-and-forth, Sonnet would have drifted. Opus 4.6 was still nailing the output format exactly as specified.

### It Reads Before It Acts

Previous models had an annoying habit — you'd give them a complex task, and they'd immediately start generating code or text before fully processing everything you said. The result? Half-baked outputs that missed key requirements buried in paragraph three of your prompt.

Opus 4.6 takes its time. The initial response is slower — noticeably slower, sometimes by ten or fifteen seconds. But when it arrives, it's dramatically more complete. The model is reading everything, cross-referencing details, and building a mental map of what you actually need before it writes a single character.

I know what you're thinking — slower sounds worse. Trust me, it's not. A ten-second wait that gives you a correct answer beats an instant response you have to regenerate three times.

### It Doesn't Give Up on Hard Problems

This one blew me away during the game development test. When Opus 4.6 hits a complex bug, it doesn't immediately punt back to you with "I'm having trouble with this, could you try..." Instead, it tries multiple independent approaches. Different debugging strategies, different fix patterns, different angles of attack — all before asking for your input.

During my testing, I watched it attempt four separate solutions to a sprite rendering issue before finding the one that worked. The old model would have thrown its hands up after attempt one. This persistence changes the dynamic entirely. You're no longer the AI's pair programmer — it's becoming yours.

### Adaptive Thinking That Doesn't Overthink

Extended thinking in previous models was binary — either it was on and the model burned through tokens on simple questions, or it was off and complex problems got shallow treatment. Opus 4.6 makes this adaptive. Ask it what 2+2 is, and it responds instantly. Ask it to architect a microservices system, and it takes the time it needs.

This sounds like a small optimization. Across a full workday, it saves real money on API costs and real time on simple interactions. The model is matching its effort to the complexity of the task — exactly what a senior engineer would do.

### Your Prompts Can Actually Be Simpler Now

Here's something counterintuitive I discovered. Because Opus 4.6 naturally gathers context and processes deeply, overly detailed prompts can actually hurt performance. I had a prompt that explicitly demanded "consider all edge cases, provide exhaustive detail, think step by step about every component" — and the output was worse than when I gave it a clean, simple prompt and let the model decide how deep to go.

The model's own judgment about how much effort to apply is, in many cases, better than your explicit instructions. That's a weird place to be as a prompt engineer, but it's where we are now.

The real power of these upgrades becomes clear when you stack them together in actual work. Which brings me to the three use cases I tested — and why the third one surprised me most.

## Use Case One: Podcast Post-Production That Used to Eat My Afternoons

I produce a podcast where I interview engineers and builders. After every episode, there's a miserable pile of post-production work — writing show notes, crafting YouTube titles and thumbnail concepts, pulling key quotes for social media, identifying moments to cut from the raw footage. On a good day, this takes me ninety minutes. On a bad day, two hours.

I built a long prompt — roughly 800 words of specific instructions — that covers every output I need from a single transcript. The prompt includes my best practices for YouTube titles (curiosity-driven, under 60 characters, front-loaded keywords), thumbnail strategies (high contrast, readable at small sizes, emotion-driven), show note formatting, and social post styles for different platforms.

Here's what happened when I fed Opus 4.6 a forty-five-minute interview transcript.

First, the YouTube titles. It generated five options, and three of them were genuinely better than what I would have written myself. Not generic AI clickbait — actual compelling titles that matched the specific content of the interview. One referenced a specific technique the guest described, another used a contrarian framing I hadn't considered.

The thumbnail concepts were practical and specific. Not just "show the guest's face with bold text" — it described color schemes, text placement, and emotional tone for each concept, matched to the corresponding title.

Show notes came out structured exactly as I specified — timestamps, key takeaways, notable quotes, and a narrative summary that didn't read like a Wikipedia article. The social posts were platform-appropriate: punchy for X, story-driven for LinkedIn, visual-hook-first for Instagram captions.

Here's the honest part — it wasn't perfect on the first pass. The model identified one moment to cut that was actually one of the best parts of the interview. And two of the social posts needed tweaking for tone. But the iteration cycle was fast. I'd say what needed fixing, and because of that instruction retention I mentioned earlier, the corrections were precise. No drift, no forgetting the overall format.

**Time saved: roughly ninety minutes per episode.** Across four episodes a month, that's six hours I'm getting back. And the quality of the outputs is close enough to my own work that I'm only editing, not rewriting.

The podcast workflow proved Opus 4.6's context retention and deep processing. But I wanted to push it into something more creative and technically demanding. That's where the game came in — and things got genuinely fun.

## Use Case Two: Building a Beat 'Em Up Game With Almost No Prompts

I had a folder of pixel art assets sitting on my machine — over 4,000 sprites, tilesets, character sheets, and UI elements I'd collected over the years. Most developers would spend a week just cataloging that library. I pointed Opus 4.6 at the folder and said, effectively: "Build me a game with these."

What happened next showed every single improvement working in concert.

### The Model Asked Smart Questions First

Instead of immediately generating code (which is what Sonnet or GPT would do), Opus 4.6 scanned the asset library, cataloged what was available, and then asked me three specific questions: What genre? What framework? What resolution? Not generic "tell me more about your project" questions — targeted questions based on what it had already analyzed from the assets.

I told it beat 'em up, Phaser 3, and let it choose the resolution based on the sprite sizes it found. It picked 800x600, which was the right call given the asset dimensions.

### The First Build Took Fifteen Minutes

The model generated a complete Phaser 3 project — HTML, JavaScript, asset loading, character controllers, basic enemy AI, collision detection, and a scoring system. Fifteen minutes from prompt to playable game. Not a toy game, either. A functional side-scrolling beat 'em up with punch, kick, and jump mechanics.

Did it work perfectly? No. The game didn't start on the first try — there was an asset path issue and a timing bug in the animation system. But here's where Opus 4.6 earned its reputation. I told it "the game isn't starting" — that's it, no error logs, no stack trace — and it diagnosed the problem, fixed both issues, and had the game running in under two minutes.

### The Health Bar Moment

This is the story I opened with. After getting the basic game running, I noticed the player health bar wasn't displaying. With previous models, I'd need to describe the bug in detail, maybe share a screenshot, probably copy-paste the relevant code section. With Opus 4.6, I just said "player HP bar isn't showing."

The model went through the code, identified that the health bar was being created but positioned behind the game layer, tried repositioning it (didn't fully fix it), then tried a different rendering approach (partially worked), then discovered the actual issue was a z-index conflict with the background tilemap. Fixed it. All autonomously. Three attempts, one correct solution, zero hand-holding from me.

That persistence on hard problems isn't just a bullet point on a feature list. When you experience it in real time, it fundamentally changes how you interact with the model. You start trusting it with harder problems because you've seen it push through difficulty instead of giving up.

### Adding Complexity After the Basics Worked

Once the core game was running, I asked for more enemy types. The model went back to the asset library, found additional character sprites it hadn't used in the initial build, and created three new enemy types with different behaviors — a fast melee attacker, a ranged enemy, and a slow tank with high health. Each one had distinct AI patterns.

The whole process — from empty folder to a playable beat 'em up with multiple enemy types, health systems, and scoring — took about forty-five minutes of my actual time. Most of that was me playing the game between prompts.

I'm not claiming Opus 4.6 can build a production game. But for prototyping, game jams, or just having fun on a Saturday afternoon? This is genuinely transformative. And the debugging autonomy means you spend your time designing, not fixing.

But game development is a relatively structured task — the model knows what games look like, it understands Phaser 3 well. I wanted to test something messier, where the output quality is subjective and harder to evaluate. That's what led me to the presentation test — and why the result caught me off guard.

## Use Case Three: Presentation Creation With Co-work

Co-work is Anthropic's feature that runs inside the Claude desktop app and operates through a virtual machine — it can create files, organize content, and interact with applications like a human would. I'd tried it when it first launched about a month ago and found it... okay. Functional but rough. Lots of alignment issues, weird spacing, presentations that looked like a first draft.

So when I decided to test Opus 4.6 through Co-work, my expectations were modest. I asked it to create a PowerPoint presentation on Claude Code best practices — a topic I know deeply, which means I could evaluate the content quality, not just the visual output.

### It Started by Asking the Right Questions

Before touching a single slide, the model asked about my audience (developers new to Claude Code), presentation length (roughly twenty slides), and which topics to prioritize. These weren't generic questions — they shaped the entire output. When I said "beginners," the model adjusted its content depth accordingly, avoiding jargon without being condescending.

### Research, Planning, Then Execution

Here's what impressed me. The model didn't jump straight into building slides. It conducted research, created an internal to-do list (which I could see), and organized the content into a logical flow before writing any code. Getting started, core commands, prompting patterns, testing workflows, security considerations, common mistakes to avoid. The outline alone was solid.

Then it coded the PowerPoint — and this is where I noticed the Opus 4.6 difference. The slides came out clean. Proper alignment. Consistent spacing. Readable font sizes. A month ago, Co-work's presentations needed manual cleanup on almost every slide. This time, maybe three slides needed minor tweaks.

### Visual Quality Assurance — By the AI

The model actually performed its own visual QA pass after generating the slides. It checked alignment, spacing, font consistency, and fixed issues it found before presenting the final output to me. That's a behavior I hadn't seen before. Self-checking work without being asked to self-check work.

The presentation used icons and emoji for visual elements — Co-work doesn't have access to an image generation model, so this was the best it could do for visual interest. Honestly, for a technical presentation aimed at developers, icons and clean typography worked fine. I wouldn't use this for a client pitch deck, but for internal training material or a conference talk draft? Absolutely usable.

### The Co-work Limitation Nobody Mentions

I should be honest about something — I'm still not sure if Co-work supports Google Slides or just PowerPoint. I work in PowerPoint, so it wasn't an issue for me, but if you're a Google Workspace shop, verify this before building a workflow around it. It's a gap in the documentation that Anthropic should address.

Still, the improvement from Co-work's launch to now is significant. If it keeps improving at this rate, it'll be a legitimate presentation tool within a couple of months.

If you've followed me through all three use cases, you've seen the pattern — Opus 4.6 isn't just faster or smarter in isolation. The improvements compound. Context retention makes long workflows viable. Deep processing means fewer iterations. Persistence on hard problems means less hand-holding. The result is a tool that fits into real work, not just demos.

## The Competitive Landscape Nobody's Being Honest About

Anthropic and OpenAI are in an arms race, and both companies know it. Opus 4.6 launched almost simultaneously with OpenAI's CodeX 5.3 — a deliberate move by both sides. The benchmarks tell one story. The developer experience tells another.

Here's my honest read after using both extensively.

**Opus 4.6 wins at daily coding work.** The personality is better — it feels like collaborating with a thoughtful colleague rather than querying a database. For zero-to-one projects (building something from scratch), Opus is consistently stronger. The planning, the context awareness, the way it asks questions before diving in — these make it the better pair programmer for greenfield work.

**CodeX 5.3 wins at gnarly bugs.** When you've got a deeply nested async race condition or a memory leak that only manifests under specific load patterns, CodeX seems to have an edge. I don't have enough data to say this definitively — it's a feeling based on maybe a dozen debugging sessions across both models. But the feeling is consistent.

**Both are improving so fast that today's comparison will be outdated in weeks.** I'm serious about this. The pace of iteration from both Anthropic and OpenAI means that any detailed feature comparison I write today becomes historical artifact by next month. What matters more than the snapshot is the trajectory — and both trajectories point steeply upward.

What I find more interesting than the horse race is what these models reveal about where AI-assisted development is heading. The shift from "AI as autocomplete" to "AI as autonomous problem-solver" happened somewhere between Sonnet 4.5 and Opus 4.6. We're not prompting tools anymore. We're directing agents.

That shift has implications most developers haven't fully processed yet, and honestly, I'm still working through them myself.

## What I Got Wrong About Prompting Opus 4.6

I need to share a mistake I made early on, because I think a lot of experienced Claude users will make the same one.

When I first got access to Opus 4.6, I used my best prompts — the ones I'd refined over months with Sonnet. Long, detailed, explicit instructions covering every edge case. "Think step by step." "Consider all possible failure modes." "Be thorough and exhaustive."

The outputs were... worse. Not terrible, but noticeably over-complicated. The model was trying so hard to follow my explicit thoroughness instructions that it lost the thread of what actually mattered. It was generating exhaustive edge case analysis for simple tasks that didn't need it.

When I stripped my prompts back to clean, focused descriptions of what I wanted — without all the meta-instructions about how to think — the quality jumped dramatically. Opus 4.6 already knows how to be thorough. Telling it to be thorough is like telling a senior engineer to "make sure you test your code." They were going to do that anyway, and now they're annoyed and second-guessing themselves.

**The lesson: trust the model's judgment more.** Give it clear goals and context, then get out of the way. The less you micromanage its reasoning process, the better it performs. This is genuinely new — with previous models, detailed prompting almost always improved output. With Opus 4.6, there's a sweet spot, and over-prompting pushes you past it.

I realize this advice will age poorly. The prompting meta-game shifts with every model release. But for right now, with Opus 4.6 specifically, simpler prompts win.

## The Numbers That Actually Matter

I tracked my productivity across the week I spent testing Opus 4.6 intensively. Here's what the data shows.

**Podcast post-production:** Down from 90 minutes to roughly 20 minutes per episode. That's a 78% reduction. The output needs light editing but no major rewrites. Across four episodes per month, I'm saving roughly five hours.

**Game prototyping:** Built a functional beat 'em up in 45 minutes of human time (plus roughly 15 minutes of model compute time). Without AI assistance, a similar prototype would take a skilled developer 8-12 hours minimum. That's roughly a 90% time reduction for prototyping.

**Presentation creation:** A twenty-slide deck took about 30 minutes through Co-work, including the research and QA phases. Manually, this would take me 2-3 hours. Around 80% time savings, though the visual quality is still below what a human designer would produce.

**Debugging cycles:** Across all three use cases, I spent approximately 70% less time explaining bugs to the model compared to Sonnet 4.5. The model's improved context gathering and autonomous debugging meant most issues got solved with a one-line description instead of a detailed bug report.

These aren't synthetic benchmarks. These are real hours from a real work week. Your mileage will vary — probably significantly — based on how your tasks map to the model's strengths. But the directional signal is clear: Opus 4.6 is a meaningful productivity upgrade for technical creative work.

**Quick wins you'll notice immediately:** Better instruction following in long threads, smarter question-asking before task execution, and autonomous debugging that doesn't need hand-holding.

**Longer-term gains that take a week to appreciate:** Simpler prompts producing better results, fewer regeneration cycles, and the compounding effect of all five improvements working together across complex workflows.

## What This Actually Means for How We Work

I keep coming back to that moment with the health bar bug. Not because it was technically impressive — it wasn't, really. A z-index fix is junior developer territory. What struck me was the behavior pattern. The model encountered a problem, tried multiple solutions independently, and solved it without involving me.

That's not a chatbot. That's an agent.

And the gap between "chatbot that writes code when asked" and "agent that solves problems autonomously" — that gap is where everything interesting happens next. We're early in this shift, but Opus 4.6 makes it tangible in a way previous models didn't.

My workflow has already changed. I find myself writing fewer, simpler prompts. I'm describing desired outcomes instead of prescribing specific approaches. I'm trusting the model to figure out implementation details that I used to spell out explicitly. And the results are better for it.

Is Opus 4.6 perfect? Obviously not. It still hallucinates occasionally. It still makes architectural decisions I disagree with. The Co-work presentation output, while improved, won't replace a skilled designer anytime soon. And the price-to-performance ratio for casual use cases probably doesn't justify choosing Opus over Sonnet.

But for sustained, complex technical work — the kind where context retention, deep processing, and autonomous problem-solving actually matter — Opus 4.6 is the best model I've used. Not by a little. By enough that I've restructured my default workflows around it.

So here's the question that keeps bouncing around my head. If an AI model can already debug its own code, research a topic before building a presentation, and iterate on creative output without losing context — what exactly is the ceiling? Because three months ago, I would have told you we were years away from this level of autonomy. I was wrong. And I suspect I'll be wrong again about whatever I think the next ceiling is.

The models aren't just getting smarter. They're getting more like the kind of collaborator you'd actually want on your team. Opus 4.6 is the first model that made me feel that shift in my gut, not just my head. And once you feel it, you can't unfeel it.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
