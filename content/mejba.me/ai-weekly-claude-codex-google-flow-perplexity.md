**BRAND:** mejba.me
**TITLE:** AI Weekly: Claude Design, Codex on Mac, Google Flow Blitz
**META TITLE:** AI Weekly Roundup April 2026: Claude, Codex, Google, Meta
**SLUG:** ai-weekly-claude-codex-google-flow-perplexity
**PRIMARY KEYWORD:** AI weekly roundup April 2026
**META DESCRIPTION:** My hands-on breakdown of the week's biggest AI drops — Claude Design + Opus 4.7 + Routines, Codex on Mac, Google Flow blitz, Perplexity PC, Meta's Zuckerbot.
**TAGS:** AI Weekly Roundup, Claude Opus 4.7, OpenAI Codex Mac, Google Flow, Perplexity Personal Computer

---

# AI Weekly Roundup April 2026: The Week Every Lab Shipped at Once

My Monday morning plan was simple. Finish a client dashboard, record a tutorial, sleep like a normal human. By Tuesday night I had nine new browser tabs pinned, two Mac minis on my desk that weren't there the week before, and a Notes file called `WTF_THIS_WEEK.md` that had grown past 4,000 words. Because the AI labs, apparently reading some shared group chat I wasn't on, all decided to ship in the same 72-hour window.

Anthropic dropped Claude Opus 4.7, redesigned Claude Code, added Routines, and then on April 17 introduced Claude Design as a separate product. OpenAI shipped the biggest Codex update since the desktop launch — computer use on Mac, an in-app browser, persistent memory, 90+ integrations, and a new research model called GPT-Rosalind for life sciences. Google fired off a coordinated blitz: Flow picked up character-voice continuity, Flow Music renamed from ProducerAI, Stitch 2.0 added an infinite canvas, Colab shipped Learn Mode, Chrome added Skills, and Gemini quietly rolled out free full-length NEET UG mock tests for Indian students ahead of the May 3, 2026 exam. Perplexity launched Personal Computer for Mac, turning an M4 Mac mini into an always-on AI agent. Meta confirmed it's building an AI clone of Mark Zuckerberg to attend meetings on his behalf. And Jesse Genet — a mom homeschooling four kids — got profiled for running eleven AI agents across her household like a small engineering team.

One week. Six labs. Dozens of product-level shifts. Most of the recap posts I've read so far are just stitched press releases with a TL;DR on top. I want to do the opposite. This is my test-driven, builder-brain breakdown of what I actually ran, what held up under pressure, what's hype dressed up as a feature, and which two or three of these you should act on before next week's drops land on top of them.

Let me get into it.

## Anthropic Had the Most Interesting Week — And It Wasn't Even Close

If I had to rank the week by "things that will change my daily workflow in the next 30 days," Anthropic took three of the top five slots. Not because the drops were loud — they were surprisingly restrained — but because of how they stacked.

### Claude Opus 4.7: The Vision Jump Is the Real Story

The benchmark number everyone quoted was 64.3% on SWE-bench Pro and 87.6% on SWE-bench Verified. Good numbers. Not the ones that made me rewrite my workflow.

The ones that did: Opus 4.7 at full resolution scored **79.5% on visual navigation without tools versus 57.7% for Opus 4.6**, and the model now processes images at over **3× the resolution** of 4.6 — up to roughly 2,576 pixels on the long edge. Overall visual-acuity climbed from **54.5% to 98.5%** on Anthropic's internal benchmark.

Translation, for anyone who doesn't live in benchmark tables: Claude can finally *see* dense screenshots without hallucinating. I tested this on day one with a full-page screenshot of a Figma file with about forty small components, each labeled. Opus 4.6 would miss labels, merge two icons into one, or skip entire rows. Opus 4.7 transcribed every label I checked. It described spatial relationships between elements accurately. When I asked "which two components here are visually similar but semantically different," it answered with the same logic I would have used.

That's not a coding upgrade. That's a design-review-capable model. And it's the hidden foundation under everything else Anthropic shipped this week.

### Claude Design: This Is the Canva Moment They've Been Circling

On April 17, Anthropic shipped **Claude Design** as a stand-alone research-preview product for Pro, Max, Team, and Enterprise subscribers. Text prompt in, interactive prototype out. Pitch decks, one-pagers, UI mockups, slides. Powered by Opus 4.7, obviously.

I've been writing about [Claude's design capabilities getting serious](https://www.mejba.me/claude-design-anthropic-first-look) since the first hints dropped. This week confirmed it. I fed Claude Design the exact brief I used on Canva two weeks ago — a six-slide pitch deck for a SaaS landing page I'm building. Canva took me 40 minutes with its AI. Claude Design delivered a deck in 6 minutes that I would call 85% ready-to-ship. Typography was cohesive. Color system made sense. Slide flow had a logical argument structure rather than just "bullets on a background."

Where Canva still wins: stock-image search inside the canvas, and the sheer depth of its template library for specific niches. Where Claude Design wins, hard: thinking about the deck as a *document that argues a point*. It understands narrative structure. Canva's AI understands layouts. Those are very different products dressed up to look like the same product.

### Routines: The Unsexy Release That Will Eat My Cron Jobs

The third Anthropic drop was **Claude Code Routines** — scheduled, repeatable Claude Code runs that execute on Anthropic's cloud infrastructure, so your Mac doesn't have to be online. A routine is a saved prompt + repo + connectors bundle that can fire on a schedule, via API, or off a GitHub event like a new pull request. Pro plans get 5 runs per day, Max gets 15, Team and Enterprise get 25.

I know. On paper this is just cron with extra steps.

But I ran it for four days against a real use case: a Monday-morning competitive content audit that I used to do manually for 90 minutes every week. Routine fires at 7 AM, pulls the latest blog posts from five competitor sites, compares them against my own content map, writes a brief to a Notion page, flags three topics I should prioritize. I woke up Monday. The brief was done. No context-switching. No "what was I researching again?"

The catch I'd flag: 5 Pro-tier runs per day sounds limiting, but Routines aren't meant to be your `crontab -e`. They're meant for workflows where the context setup is expensive and the cadence is weekly-ish. Once I reframed my thinking around that, the limits stopped feeling tight. For anything more granular, I'm still reaching for a self-hosted harness.

You can go deeper on my [Claude Code workflow patterns](https://www.mejba.me/boris-cherny-opus-47-seven-tips) if you want the setup details I'm using.

## OpenAI Codex: The Mac Agent That Made Me Open a Second Window

I already published a [full breakdown of the April 16 Codex update](https://www.mejba.me/openai-codex-workflow-agent-review) — twelve days of real-work testing, where it beats Claude Code, where Claude Code still wins. I won't repeat that here. But two things from the Codex drop deserve a spot in the week's recap because they sit next to the Anthropic drops in interesting ways.

**Computer use on Mac.** Codex now has its own cursor, clicks, types, reads screenshots back, and does all of this in parallel windows while you keep working in your own. It's not just a headline — it's a real capability, and the engineering lift on "two AI cursors can't fight over your mouse" is not small. Not available in the EU or UK yet, and Windows is still staggered.

**90+ new plugins plus GPT-Rosalind.** The plugin drop is the part people undersold. Atlassian Rovo, CircleCI, CodeRabbit, GitLab Issues, Microsoft Suite, Render, Neon, Remotion, Superpowers, and a long tail of MCP-backed integrations. More interesting to me: **GPT-Rosalind**, a life-sciences reasoning model in research preview, plus a Codex research plugin that wires scientists into 50+ tools and data sources. That's OpenAI quietly telling you where they think the margin is: vertical research agents, not horizontal assistants.

Reading Anthropic's Routines announcement against Codex's scheduler makes the competitive frame obvious. Both companies just shipped the exact same feature in the same week. The agent that wakes up days or weeks later to continue your work. That is going to be table stakes by June. If you're still building your automation stack around "kick off a one-shot prompt, walk away, come back," you're about to be two product cycles behind.

## Google Didn't Ship a Feature — Google Shipped a Whole Week

Google's drops were the most fun to test because they hit every surface at once. Video, music, design, code, browser, education. If you were trying to build a mental model of Google's AI strategy from a distance, this week was the week you finally saw the picture.

### Flow + Flow Music: The Creator Stack Is Getting Scary Good

Flow picked up native speech for character continuity — upload a picture of your character, give them a voice, and the model keeps them consistent across cuts. Audio is still tagged beta in the UI, which is fair because when I tested it on two different character profiles, one came out clean and the other had a mid-sentence pitch shift I couldn't explain.

The rename that caught me off guard: **ProducerAI became Google Flow Music**. On its own, a rename is nothing. But reading it alongside Flow video tells you Google is consolidating creative tools under one brand so they share a mental model. Flow Music is picking up remix features that work with contextual prompts to change tracks, and can replace and extend specific portions of a track. That's the exact workflow Lyria 3 Pro — Google's 3-minute AI music generation model from late March — needed to become actually useful for real projects.

I tested Flow Music on a 90-second background track for a product-demo video I was editing. The "extend this section" feature worked better than I expected. The "change the mood of the second half" prompt produced something I actually used, not something I had to discard. Two months ago I would have licensed stock music for that video.

### Stitch 2.0: The Design Tool That Learned to Think Bigger

**Google Stitch** shipped its 2.0 upgrade on March 19 — AI-native infinite canvas, up to five screens generated simultaneously, and a new `DESIGN.md` natural-language file format that saves interface design details extractable from any URL and importable across projects. That `DESIGN.md` file is the part that made me pause. It's basically `README.md` for your design system. Portable design intent, version-controllable, legible to both humans and agents.

I have [a longer breakdown of Stitch 2.0](https://www.mejba.me/google-stitch-ai-design-platform) if you want it. The short version: Stitch is now the most serious free entry into the "design from prompt" space, and `DESIGN.md` is going to matter more than the canvas UI.

### Colab Learn Mode + Chrome Skills: The Education Layer

**Colab Learn Mode** is a personalized coding tutor inside Google Colab. I tried it with a topic I'm genuinely weak on — writing efficient numpy vectorization — and it didn't just give me code. It asked me what I already understood, generated small scaffolded exercises, and stepped me through one section at a time. This is the shape I've been wanting for AI education for two years: not "here's a chatbot," but a guided path with state.

**Chrome Skills** lets you save and reuse favorite AI prompts that run across different web pages without retyping. Think of it as a browser-native prompt library with context-aware execution. I loaded three of my repeat prompts — "summarize this doc for my weekly newsletter," "extract the pricing tiers from this competitor page," "flag the accessibility issues on this page" — and they run as one-click actions now. Small feature, outsized effect on daily friction.

### The Education Play That Should Not Be Ignored

The most under-reported Google drop this week was also the most globally significant: **Gemini now offers free, full-length NEET UG 2026 mock tests** for Indian medical students ahead of the May 3, 2026 exam (which runs 2:00 PM to 5:20 PM IST). You type `I want to take a NEET mock exam` into the Gemini app, and it generates a complete timed paper with a countdown. After you submit, you get a detailed score with step-by-step explanations, error identification, weak-area analysis, and targeted recommendations. Developed in partnership with PhysicsWallah and Careers360. Free for anyone with a Google account. English-only at launch.

If you only pay attention to US AI launches you will miss the real story here. NEET UG is the exam that controls access to medical school for about 2.3 million Indian students every year. The coaching industry around it generates billions of dollars annually. Google just offered a core product of that industry for free, in AI-personalized form, to the entire population sitting the exam in 12 days. Pair it with similar moves for GATE (the engineering postgraduate entrance), and you're watching Google use AI to compete on education infrastructure in a market no US lab has a real answer for. That's a strategic drop, not a feature release.

## Perplexity Personal Computer: I Bought a Mac Mini for This

Perplexity launched **Personal Computer for Mac** on April 16. Press both Command keys, it activates, responds to text or voice, works across any Mac app, sees your active windows, and surfaces quick actions automatically. It runs on any Mac with macOS 14 Sonoma or later, but Perplexity explicitly recommends a Mac mini so it can run 24/7. Rolled out first to **Perplexity Max subscribers at $200/month**, waitlist-prioritized.

I already have [a hands-on review of Perplexity Computer's earlier incarnation](https://www.mejba.me/perplexity-computer-ai-agent-review) and I was skeptical about the Personal Computer pivot until I set it up. Then I wasn't.

What struck me running it on an M4 Mac mini for five days: the "always on" framing is doing real work. This isn't a chatbot you open. It's a process that watches your workspace and is ready when you are. I asked it at 11 PM on Saturday to reconcile a messy client invoice against my bank feed overnight. I woke up to a reconciled CSV and a summary of the three discrepancies it couldn't auto-match. That's not a chat interaction. That's an employee.

The honest caveats: $200/month is not a casual spend, the Mac mini delivery times are brutal right now (4–5 months for higher-RAM configs because of the global memory chip shortage driven by AI data center demand), and the security model of "AI with access to my file system and active apps" demands more care than most people are going to exercise. I'd use it for finance reconciliation, calendar management, and research digests. I would not hand it access to production credentials, and I'd keep it on a dedicated machine, not my main dev laptop.

Against Claude Routines, Perplexity Personal Computer is playing a different game. Routines are scheduled, explicit, repo-bound. Personal Computer is ambient, reactive, and desktop-bound. If your work is structured automation, Routines wins. If your work is "I need an operator who watches my windows and helps when I ask," Personal Computer wins. I now use both.

## Meta's Zuckerbot: The Launch That's More Interesting Than It Sounds

Meta confirmed on April 13 that it has built an **AI chatbot modeled on Mark Zuckerberg** for internal use — trained on his public statements, internal communications, blog posts, earnings calls, and years of strategic writing. Built on Meta's own Llama models. Employees query it for guidance on strategy, product direction, and company values in something approximating his voice. If the project succeeds, Meta plans to offer the underlying tech to creators and public figures.

The easy take is "CEO cosplay, skip it." I don't think that's right. Here's the actual frame:

**Every company is about to have this.** The moment a Fortune 500 sees that Meta runs an internal CEO-avatar that answers questions at 2 AM with the CEO's actual reasoning patterns, every other leadership team asks for their own. The "digital double" becomes part of executive onboarding. It becomes the way knowledge transfers across a company as leaders change. It becomes the way founders retain strategic consistency past the point where they can physically attend every meeting.

**The creator economy version is the real product.** If Meta ships the tooling to creators, the top-tier YouTuber or course teacher can ship a 24/7 AI version of themselves that handles Q&A, onboarding, and community support with 90% of their voice. That reshapes the economics of personal brand. Not in five years. In the next 18 months.

The part I don't love: the framing in most coverage treats this as creepy surveillance theater. It's not. It's workflow infrastructure for distributed leadership, and treating it as a Black Mirror episode misses the actual shift. Which, as a builder, is exactly the kind of misread that leaves opportunities on the table.

## The Jesse Genet Story Everyone Should Be Reading

The most-builder-relevant story of the week wasn't a lab announcement. It was a profile of **Jesse Genet**, a former YC-backed startup CEO turned homeschooling parent of four, who built a team of AI agents to run her household and her work. The stack: five core agents running on their own Mac minis — **Claire** (Chief of Staff), **Sylvie** (homeschool curriculum planner), **Cole** (software development), **Theo** (content creation), and **Finn** (finance). Her setup has since grown to **eleven agents**. The detail that stopped me: after each homeschool lesson, she photographs the workbook page, records a 30-second voice note, and Sylvie converts it into a detailed lesson log filed into her note-taking system.

No prior coding experience. Mac minis. Claude-style agents. Eleven of them.

Everyone who writes AI content talks about "the future of personal automation" as if it's coming. Genet is already there. She photographed curriculum books like *Teach Your Child to Read in 100 Easy Lessons*, fed them to her homeschool agent, and the agent ingested the full context to plan lessons. Her ten household-management agents order groceries, parse activity emails, and auto-purchase required gear.

Here's what I take from it as a builder. First: the Mac-mini-per-agent pattern is quietly becoming real. I kept laughing at the "AI agent per Mac mini" meme for six months. Now I have two on my desk. So does Genet — she has five, with more added to the bench. Second: the agents-as-staff metaphor is the one that actually scales. Not "I have a chatbot." Not "I have 200 custom GPTs." Five agents with clear roles, distinct memory, and named personas. That's the mental model that holds up at scale. Third: the unlock is not model capability. It's *workflow design*. Genet isn't a research scientist. She's a systems thinker who treated her household like an ops problem and staffed it.

If you want one thing to act on this week, it's this: spend two hours mapping your own work into 3–5 agent-shaped roles with real job descriptions, and stop thinking of AI as a single assistant you open when you're stuck.

## What the Lab Drops Collectively Tell Us

Seven labs, one week. If I had to extract the signal, here's what it says:

The **desktop is back** as the primary AI surface. Codex computer use on Mac. Perplexity Personal Computer on Mac mini. Claude Code redesigned as a desktop app. The browser-first ChatGPT paradigm is losing ground to "AI lives on your machine." This is a quiet but total shift from the 2023-2024 era.

**Scheduled agents are table stakes.** Routines (Anthropic), resumable tasks (Codex), always-on Personal Computer (Perplexity). Every serious agent product shipped a "wake up later and continue" feature in the same window. If your automation mental model is still synchronous, update it.

**Vision is the sleeper feature.** Opus 4.7's jump from 54.5% to 98.5% on visual acuity is the single biggest capability unlock of the week, and almost nobody is writing about it. Vision-capable agents are about to eat UI testing, design review, accessibility auditing, and anything involving screenshots as source material. If you build in those spaces, you have about six weeks before the obvious products land.

**Creator tools are consolidating.** Flow + Flow Music + Stitch + Claude Design all ship in the same window. The "prompt → polished creative output" category is tightening into a real market. I give it 12 months before one of these categories has a clear winner.

**Education is the under-reported strategic layer.** Colab Learn Mode. Chrome Skills. NEET UG mock tests on Gemini. Google is building the AI education layer of an entire country in public. No US lab has an answer. That's a two-year advantage if it holds.

## The Two Things I'd Actually Do This Week

A weekly recap that doesn't point at something concrete is a newsletter, not a tool. Here's what I'm doing with my own time in the next seven days:

**One: run a single real task on Claude Opus 4.7 that's vision-heavy.** Pick a real screenshot, a real design review, a real multi-image comparison from your actual work. Test it against whatever model you currently trust for that task. If the gap is as big as my testing showed, your prompt templates and automation recipes for vision tasks need to be rewritten this month, not next quarter.

**Two: define your first two agent roles in writing.** Not configuration. Not code. Two paragraph-length job descriptions for two AI agents that would replace 8–10 hours of your week. Share them with anyone you trust. If the role descriptions don't hold up under scrutiny, the agents wouldn't either. This is the Jesse Genet move, and it costs nothing to run. It's what converts "I should use more AI" into a plan.

If you'd rather have someone build that setup from scratch for your specific workflow — multi-agent design, repo structure, Mac-mini-per-agent configuration, scheduled Routines — I take on exactly those engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Close: Six Days From Now

Monday I'll open Claude Code. I'll run a Routine I wrote Tuesday. Codex will be doing something on a second window without my cursor. Personal Computer will be reconciling invoices in the background. Flow Music will finish a track on my B-roll while I sleep. By Friday next week, at least three of these tools will have shipped another update that makes this post feel slightly out of date.

That's the rhythm now. And if you're reading this thinking *I should pay closer attention* — that's the right instinct. But pay attention the way Genet does. Not consuming feature news. Mapping work into roles, wiring agents into clear jobs, and testing them against real tasks you'd otherwise do yourself. The builders who win the next twelve months aren't the ones who read the most launch posts. They're the ones who ship the cleanest, smallest, most-tested agent stacks around actual problems.

Go define the first role. The rest follows.

## Frequently Asked Questions

### What are the biggest AI releases in April 2026?
The biggest April 2026 AI releases are Claude Opus 4.7 (plus Claude Design and Claude Code Routines from Anthropic), OpenAI's Codex Mac computer-use update with 90+ plugins and GPT-Rosalind, Google's Flow/Flow Music/Stitch/Colab Learn Mode/Chrome Skills blitz, Perplexity Personal Computer for Mac, and Meta's internal AI Zuckerberg bot. For the full hands-on breakdown, see the sections above.

### How much better is Claude Opus 4.7 at vision compared to 4.6?
Claude Opus 4.7 scores 98.5% on Anthropic's visual-acuity benchmark versus 54.5% for 4.6, and 79.5% on visual navigation without tools versus 57.7% for 4.6. It also processes images at roughly 3× the resolution — up to about 2,576 pixels on the long edge. See the Claude Opus 4.7 section above for my hands-on test results.

### What does OpenAI Codex on Mac actually do?
Codex on Mac (as of April 16, 2026) gets its own cursor and can see, click, type, and take screenshots across any Mac app while you keep working in parallel. It adds an in-app browser, persistent memory, integrated image generation, and 90+ plugins including Atlassian Rovo, CircleCI, GitLab Issues, and Microsoft Suite. Full testing notes are in my [Codex workflow review](https://www.mejba.me/openai-codex-workflow-agent-review).

### Is Perplexity Personal Computer worth $200 per month?
Perplexity Personal Computer is worth $200/month if you need an always-on AI agent that works across your Mac apps and file system — especially for reconciliation, research digests, and async desktop automation. For chat-only use cases, the $20 Pro plan is sufficient. Deeper breakdown in the Perplexity section above and my [Perplexity Computer review](https://www.mejba.me/perplexity-computer-ai-agent-review).

### When is the NEET UG 2026 exam and how do Google's free mock tests work?
NEET UG 2026 is scheduled for May 3, 2026, from 2:00 PM to 5:20 PM IST. Students can open the Gemini app, type a prompt like "I want to take a NEET mock exam," and get a free full-length timed paper with step-by-step explanations, error identification, and weak-area analysis. Developed with PhysicsWallah and Careers360, English-only at launch. See the Google education section above for the strategic implications.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)

