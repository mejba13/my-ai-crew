**BRAND:** mejba.me
**TITLE:** Google IO 2026: The AI Announcements That Matter
**META TITLE:** Google IO 2026 AI Announcements: Gemini 4 & Beyond
**SLUG:** google-io-2026-ai-announcements
**PRIMARY KEYWORD:** Google IO 2026 AI announcements
**SECONDARY KEYWORDS:** Gemini 4, Ironwood TPU, Android 17 AI
**META DESCRIPTION:** Google IO 2026 brings Gemini 4, Ironwood TPUs at 42.5 exaflops, AI glasses, and Android 17. Here's what every developer needs to know from the event.
**TAGS:** Google IO, AI Models, Gemini AI, Google DeepMind, Event Analysis
**CONTENT CLUSTER:** AI Model Reviews
**TRANSFORMATION GOAL:** After reading, the reader will understand the strategic significance of every major Google IO 2026 announcement and know which developments directly affect their AI workflows.

---

I've been tracking Google's AI moves since the original Bard launch — the one that wiped $100 billion off Alphabet's market cap because it got a James Webb Space Telescope question wrong in the demo. That was February 2023. Three years later, the same company is about to walk onto a stage in Mountain View on May 19, 2026, with what might be the most aggressive AI lineup any company has ever assembled for a single event.

And I'm not being hyperbolic. When you look at what Google is bringing to IO this year — Gemini 4 scoring 84.6% on ARC-AGI2, a TPU that pushes 42.5 exaflops, AI glasses built with Warby Parker, a brand new desktop operating system, and a robotics partnership that puts Gemini inside Boston Dynamics' Atlas — it reads less like a developer conference and more like a company trying to rewrite every technology category simultaneously.

I've spent the last two weeks tracking every leak, every confirmed announcement, and every credible rumor coming out of Google's IO 2026 preparations. Here's my breakdown of what actually matters, what's hype, and what's going to change how you and I build things over the next twelve months.

## Google's $185 Billion Bet Is Starting to Pay Off

Before we get into specific announcements, the market context matters — because it explains *why* Google is swinging this hard.

Twelve months ago, the narrative around Google AI was brutal. ChatGPT was dominating mobile with a 69.1% daily active user share. Google's AI market share sat at a modest 14.7%. Every tech pundit had written the same article: "Google missed the AI wave." I half-believed it myself. My own workflow leaned heavily on Claude and GPT-4 for most tasks, with Gemini relegated to multimodal experiments where its massive context window gave it an edge.

That picture has shifted dramatically. Google's AI market share has climbed to 25.1% — nearly doubling in under a year. ChatGPT's mobile DAU share dropped from 69.1% to 45.3%. Gemini 3.1 Pro scored 80.6% on the S2E benchmark, which put it in genuine contention with the best reasoning models from Anthropic and OpenAI. And behind those numbers sits a staggering $185 billion investment that's now starting to produce compound returns across hardware, software, and services.

The turnaround didn't happen because Google got lucky. It happened because they did what only Google can do — throw massive infrastructure advantages at the problem while simultaneously shipping a model (Gemini 3.1 Pro) that could actually compete on quality. I covered [how Gemini 3.1 Pro replaced entire workflows](/content/mejba.me/gemini-3-1-pro-real-power.md) a few weeks ago, and the response from readers confirmed what the market data shows: people are actually switching.

That's the backdrop. Now here's what's coming on May 19.

## Gemini 4: The Model That Could Change the Leaderboard

This is the headliner. And the numbers — if Google's internal benchmarks hold up to independent testing — are legitimately staggering.

Gemini 4 reportedly scores 84.6% on ARC-AGI2. For context, ARC-AGI2 is the benchmark specifically designed to test reasoning capabilities that current AI models struggle with — tasks requiring genuine abstraction, not pattern matching from training data. When I [tested Gemini 3 Deepthink](/content/mejba.me/gemini-3-deepthink-tested.md) earlier this year, I was impressed by its Codeforces ELO of 3,455, which placed it among the top 0.2% of competitive programmers. Gemini 4 reportedly matches that coding capability while pushing reasoning scores into territory nobody else has reached publicly.

Here's what we know about the technical specs:

- **Context window:** 2 million+ tokens. Not a modest bump — this matches the longest context windows available from any frontier model and makes Gemini 4 viable for full-codebase reasoning, long document analysis, and the kind of multi-turn agent workflows I use daily.
- **Latency:** Sub-300ms response time. This is the number that interests me most. Fast models change how you build with them. When a model responds in under 300 milliseconds, it becomes viable for real-time applications — live coding assistants, interactive agents, voice-first interfaces.
- **Persistent memory:** Gemini 4 reportedly maintains context across sessions without the developer needing to rebuild state each time. If this works as described, it eliminates one of the most frustrating limitations in current AI development.
- **Project Astra integration:** Google's multimodal assistant project gets a direct line into Gemini 4's capabilities. This means a single model that can see, hear, reason, and respond — with sub-second latency.

The planned rollout is a preview at IO on May 19-20, with wider availability coming in late 2026 or early 2027. That gap between preview and general access is worth noting — it suggests Google is being cautious about scaling, which usually means the model is good but computationally expensive to run at scale.

My take? If Gemini 4 delivers on even 80% of these claims, it becomes the model I'd want for complex agentic workflows. The combination of a 2M+ context window, sub-300ms latency, and persistent memory addresses the three biggest pain points I hit daily when building AI-powered systems.

But here's the thing I'll be watching for at the actual keynote: real-time demos with unscripted inputs. Benchmarks tell you one story. Watching a model handle unexpected edge cases on a live stage tells you a completely different one.

## Ironwood TPU: The Hardware Nobody Is Talking About Enough

Every AI company talks about models. Google is the only one that also builds its own silicon — and the 7th generation Ironwood TPU might be the most consequential announcement at IO that nobody outside the ML infrastructure community is paying attention to.

The headline number: a 9,216-chip superpod delivering 42.5 exaflops of compute. That's 10x the previous generation. Google's TPU budget jumped from $6.2 billion in 2024 to $9.8 billion in 2025, and Ironwood is where that money went.

Why should you care if you're a developer building applications and not training foundation models? Three reasons.

**First, inference costs drop.** Ironwood isn't just a training chip. Google specifically designed it for the "age of inference" — running trained models at scale for millions of users. According to Google's own blog, Ironwood offers 4x better performance per chip for both training and inference compared to the previous generation, with 2x the performance per watt relative to Trillium (the 6th gen TPU). Each chip packs 192 GB of High Bandwidth Memory, 6x what Trillium offered. When inference gets cheaper, API pricing follows — which means every application you build on Gemini gets more economical to run.

**Second, it signals Google's infrastructure moat.** Anthropic, OpenAI, and every other AI lab depends on Nvidia GPUs or rented compute. Google builds its own chips, operates its own data centers, and controls the full stack from silicon to API. That's a structural advantage that compounds over time. Anthropic itself has announced plans to use up to 1 million Ironwood TPUs to run Claude — which tells you everything about how seriously the industry takes this hardware.

**Third, 42.5 exaflops enables model architectures that aren't feasible today.** When you have that much compute available, you can train models with different trade-offs — larger context windows, deeper reasoning chains, real-time multimodal processing. Ironwood doesn't just make existing models faster. It makes new model designs possible.

This is the announcement that will look the most important in hindsight, even if it gets the least attention during the keynote.

## Gemini Nano 4: AI That Runs on Your Phone Without Draining Your Battery

While Gemini 4 is the flagship model for cloud and heavy compute, Gemini Nano 4 is what puts AI directly on your device — and the improvements here are practical in a way that matters for everyday use.

The key numbers: 4x faster than the previous Nano generation and 60% less battery consumption. That's not an incremental upgrade. That's the difference between "on-device AI is a gimmick" and "on-device AI is a feature I actually leave turned on."

I've been skeptical of on-device AI for a while. The models were too slow, too limited, or killed your battery. But a 4x speed improvement combined with a 60% battery reduction means Nano 4 can handle real-time translation, smart compose, photo enhancement, and contextual suggestions without making your phone feel like it's running a space heater in your pocket.

There's also Nano Banana 2 — and yes, that's the actual name — which generates 4K watermark-free images directly on-device. No cloud roundtrip. No API call. No waiting. For content creators who need quick visuals while working from a phone or tablet, this is a genuine workflow unlock.

The on-device angle matters for another reason too: privacy. When your AI processing happens on the device itself, your data never leaves your phone. For anyone working with sensitive information — client data, financial documents, medical records — local processing isn't just convenient, it's a compliance requirement.

## The Video and Music AI That Makes Creators Nervous

Google's creative AI tools have been quietly impressive for a while, and IO 2026 pushes them further into territory that's going to make some creative professionals uncomfortable.

**V4 Video Model:** Extends AI-generated video from short clips to 10-30 second segments at full 4K resolution, complete with storyboarding capabilities. The storyboarding piece is the real story here — it means you can plan multi-scene narratives before generation, giving you creative control that previous video models lacked. You're not just prompting "make a video of X" anymore. You're directing a sequence of shots.

**Liia 3 Pro:** Generates 3-minute full musical tracks with vocals. That's a 6x increase in duration over previous versions. Three minutes with vocals means you can produce complete songs, podcast intros, video soundtracks, or background music for entire presentations without touching a DAW or hiring a session musician.

I have mixed feelings about these tools. On one hand, they democratize creative production in ways that genuinely help solo creators and small teams. On the other, I've watched entire categories of freelance work evaporate in the last two years as AI creative tools improved. The video and music capabilities Google is shipping at IO 2026 will accelerate that shift.

The practical question for developers and builders: are you going to integrate these APIs into your products? Because your competitors will.

## AI Glasses: Google's Third Attempt Gets Interesting

Google Glass (2013) was too early and too weird. Google Glass Enterprise (2017) was too niche. Now Google is making its third run at face-worn AI — and this time, the approach is dramatically smarter.

Instead of building the hardware alone, Google partnered with Warby Parker (committing $75 million to the collaboration, with an additional $75 million conditional on milestones) and acquired 4% of Gentle Monster, a luxury eyewear brand with actual fashion credibility. Samsung is manufacturing models that weigh just 50 grams and include iOS compatibility — a detail that signals Google learned from the smartwatch wars that platform exclusivity kills adoption.

The glasses come in two variants: AI glasses for screen-free assistance (speakers, microphones, cameras for natural Gemini interaction) and display AI glasses with an in-lens display for private access to navigation, translation captions, and contextual information.

What changed between Google Glass and now? Three things. The AI is good enough to be useful in real-time. The hardware is light enough to wear all day. And the distribution is through brands people already trust for eyewear, not through a Google-branded gadget that screams "tech bro."

I'm cautiously optimistic. The 50-gram weight is critical — anything heavier fails the all-day wearability test. The iOS compatibility is smart. And the Warby Parker partnership means these will look like glasses, not like a science experiment attached to your face.

Whether this becomes a mainstream product or another Google hardware graveyard resident depends entirely on what Gemini can actually do through the glasses in real-world conditions. That demo at IO will be the one to watch.

## Personal Intelligence: Google Searches Your Life

This might be the most ambitious — and most controversial — announcement at IO 2026.

Personal Intelligence connects Gemini to your Gmail, Google Photos, Drive, Calendar, YouTube history, and Search data, creating an AI assistant that doesn't just know general information but knows *your* information. Google is rolling this out to 2 billion users across 200+ countries, with camera input capabilities that let you point your phone at something and get personalized, context-aware responses.

The use case Google demonstrated: Gemini suggests tire options based on family road trips it identified in your Google Photos, then pulls your car's license plate number from a picture you took months ago. That's not a parlor trick. That's an AI that has genuine understanding of your personal context.

Here's why this matters for developers. Personal Intelligence creates an expectation among users that AI should know them — their preferences, their history, their context. Every AI application you and I build will be compared against that expectation. If Google's built-in AI remembers what you drove last summer and your app's AI can't remember what you asked five minutes ago, users will notice.

The privacy implications are obvious and Google has addressed them head-on: you choose which apps to connect, you can disconnect at any time, and Google claims they don't train on your Gmail inbox or Photos library (though they do train on specific prompts and responses). Whether you trust that distinction is a personal call. But the feature itself represents a fundamental shift in what "AI assistant" means.

If you'd rather have someone build personalized AI systems like this for your own business, I take on custom AI integration projects. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Android 17 and Aluminium OS: Google's Biggest Platform Bet in a Decade

Two operating system announcements are coming at IO, and together they represent Google's most ambitious platform strategy since Android itself.

**Android 17** gets deeper Gemini integration, with AI agent capabilities available to Gemini Ultra subscribers at $249.99/month. That price point is aggressive — this isn't a casual consumer feature. It's a professional-tier AI assistant embedded directly in your phone's OS, capable of multi-step task execution across apps.

**Aluminium OS** is the bigger surprise. Google is launching an entirely new Android-based desktop operating system designed to compete directly with Windows and macOS. Built on top of Android 17, Aluminium OS runs all 3+ million Play Store apps natively with proper keyboard, mouse, and window management. Gemini AI is embedded in the OS core and processes locally via NPU.

Hardware partners including HP, Lenovo, Acer, ASUS, and Samsung will ship devices with Aluminium OS pre-installed. Chrome OS isn't going away — Google is pursuing a dual-OS strategy — but Aluminium OS is clearly positioned as the future.

Why does this matter for AI developers? Because an OS with Gemini embedded at the system level means on-device AI becomes a platform capability, not an app feature. Every application running on Aluminium OS can tap into Gemini's reasoning, vision, and language capabilities natively. That changes what's possible for desktop software in the same way that smartphones having GPS and cameras changed what was possible for mobile apps.

The Gemini agent expansion is worth noting too: currently US-only, Google is expanding to the UK, Canada, Australia, and 15 languages by year-end. If you're building AI-powered tools for international markets, that timeline matters for your roadmap.

## The Robotics Announcements That Signal Something Bigger

Google's robotics play at IO 2026 connects three separate threads into something that, honestly, made me sit up straight when I connected the dots.

**Boston Dynamics Atlas on Gemini:** Google DeepMind and Boston Dynamics announced a partnership at CES 2026 to integrate Gemini Robotics models into the Atlas humanoid robot. Atlas can now perceive natural language commands, decompose high-level tasks into subtasks, analyze its environment through sensors, and execute autonomously. Hyundai (Boston Dynamics' parent company) plans to manufacture up to 30,000 humanoid robots annually by 2028, with the first units deploying to Hyundai's Robotics Metaplant Application Center and Google's DeepMind offices.

**AlphaEvolve:** Google's evolution-based AI system reportedly saves approximately $1.3 billion per year through optimized algorithm design. That's not a research curiosity — that's a system generating measurable ROI at a scale that justifies Google's entire AI investment in hardware.

**AlphaGenome:** Building on the Nobel Prize-winning AlphaFold legacy, AlphaGenome aids 3,000 scientists across 160 countries in genomic research. This is Google's clearest example of AI driving genuine scientific advancement, not just commercial products.

The through-line: Google is positioning Gemini not just as a chatbot or a coding assistant, but as a general-purpose reasoning engine that works across digital interfaces, physical robots, scientific research, and everything in between. That ambition is either visionary or overextended — and IO 2026 is where we'll start finding out which.

## The Competition: Why Google Is Moving This Fast

Google isn't the only company shipping big AI products in 2026, and their IO lineup makes a lot more sense when you see who's pushing them.

**Anthropic's Claude Mythos** is the rumored next-generation model that could leapfrog current benchmarks. I've covered [Opus 4.6's capabilities](/content/mejba.me/opus-4-6-hands-on-review.md) extensively, and Anthropic's trajectory suggests Mythos could be transformative. Google needs Gemini 4 to be ready before Mythos ships.

**Grok** has quietly captured 17.8% AI market share, and XAI's partnership with SpaceX has pushed the combined company's valuation to $1.25 trillion. Elon Musk's AI play has real distribution through X (formerly Twitter) and real compute through a massive GPU cluster. Google can't ignore that.

**Deep Seek V4** running on Huawei chips represents a fundamentally different competitive vector — a Chinese AI lab building frontier models on non-Nvidia hardware. If Deep Seek proves you can build competitive models without depending on American chip companies, the entire semiconductor-AI supply chain gets disrupted.

This competitive pressure explains why Google is shipping everything at once. They're not just announcing a new model. They're announcing a new model, new hardware, new devices, new operating systems, and new scientific breakthroughs — all in a two-day window. It's a show of force designed to make the market understand that Google's AI capabilities extend far beyond any single product.

## The Apple-Google Collaboration: The Deal Nobody Expected

Perhaps the most surprising development heading into IO 2026 is the deepening Apple-Google AI partnership.

Apple and Google have entered a multi-year collaboration where the next generation of Apple Foundation Models will be based on Google's Gemini models and cloud technology. Apple is using a process called model distillation to create smaller, efficient AI systems based on Gemini that run locally on iPhones, iPads, and Macs — eliminating cloud dependency and enhancing privacy.

The first Gemini-powered Siri features are expected with iOS 26.4 in spring 2026. A radically redesigned Siri — functioning as a full chatbot with web search, image generation, content summarization, coding assistance, and multi-step command execution — is expected at WWDC on June 8, roughly three weeks after IO.

The timing is deliberate. Google previews Gemini 4 at IO on May 19. Apple shows what Gemini can do inside Siri at WWDC on June 8. Two keynotes, three weeks apart, telling the same story from two angles: Gemini is the reasoning engine powering both Android and iOS.

For developers building cross-platform AI features, this convergence simplifies things enormously. If both major mobile platforms run Gemini-based AI, you can build against one set of capabilities and reach essentially every smartphone user on the planet.

## The IO 2026 Timeline: What Happens When

| Date | Event | Significance |
|------|-------|-------------|
| May 19, 2026 | IO Day 1 Keynote | Gemini 4 preview, Ironwood TPU, AI glasses reveal |
| May 20, 2026 | IO Day 2 Sessions | Developer deep dives, Android 17 details, Aluminium OS hands-on |
| June 8, 2026 | Apple WWDC | Gemini-powered Siri debut, iOS 27 preview |
| Late Q3 2026 | Aluminium OS GA | Consumer device availability with partner OEMs |
| Q4 2026 | Gemini Agent Expansion | UK, Canada, Australia, 15 languages |
| Late 2026/Early 2027 | Gemini 4 Wide Rollout | General API access for developers |
| 2028 | Atlas Production Scale | 30,000 humanoid robots per year |

## By the Numbers: Google IO 2026 At a Glance

| Metric | Value | Context |
|--------|-------|---------|
| Google AI Market Share | 25.1% (up from 14.7%) | Fastest market share gain in AI history |
| ChatGPT Mobile DAU Share | 45.3% (down from 69.1%) | Google's Gemini eating into OpenAI's lead |
| Total AI Investment | $185 billion | Largest AI infrastructure commitment globally |
| Gemini 4 ARC-AGI2 Score | 84.6% | Highest public score on reasoning benchmark |
| Gemini 4 Codeforces | 3,455 ELO (top 0.2%) | Elite competitive programming level |
| Gemini 4 Context Window | 2M+ tokens | Viable for full-codebase reasoning |
| Gemini 4 Latency | Sub-300ms | Real-time application capable |
| Ironwood TPU Compute | 42.5 exaflops | 10x previous generation |
| Ironwood Chip Count | 9,216 per superpod | Largest single-cluster TPU deployment |
| TPU Budget (2025) | $9.8 billion | Up from $6.2B in 2024 (58% increase) |
| Ironwood HBM per Chip | 192 GB | 6x the previous generation |
| Gemini Nano 4 Speed | 4x faster | Meaningful on-device AI performance |
| Nano 4 Battery Improvement | 60% less consumption | All-day viability for on-device AI |
| V4 Video Duration | 10-30 seconds at 4K | Storyboarding enables directed narratives |
| Liia 3 Pro Track Length | 3 minutes with vocals | 6x duration increase |
| Warby Parker Investment | $75M (+ $75M conditional) | Serious hardware commitment |
| AI Glasses Weight | 50 grams | Passes all-day wearability threshold |
| Gemini Ultra Subscription | $249.99/month | Professional-tier AI agent in Android |
| Personal Intelligence Reach | 2 billion users, 200+ countries | Largest AI assistant deployment planned |
| AlphaEvolve Annual Savings | ~$1.3 billion/year | ROI that justifies the infrastructure spend |
| AlphaGenome Reach | 3,000 scientists, 160 countries | Real scientific acceleration |
| Atlas Production Target | 30,000 units/year by 2028 | Industrial-scale humanoid manufacturing |
| Grok Market Share | 17.8% | Third player emerging fast |
| XAI + SpaceX Valuation | $1.25 trillion | Competitive pressure driving Google's pace |
| AI Master Platform Learners | 5,000+ | Ecosystem building through education |

## What I'm Actually Watching For

Strip away the marketing and the benchmark numbers, and three questions will determine whether Google IO 2026 lives up to the hype.

**Can Gemini 4 handle unscripted, complex tasks in real-time?** Benchmarks are controlled environments. I want to see what happens when someone throws an unexpected multi-step problem at Gemini 4 on stage. That's where the truth lives.

**Does Aluminium OS actually feel like a real desktop OS?** Running Play Store apps with keyboard and mouse support is table stakes. The question is whether it has the refinement, the developer tools, and the app ecosystem to make someone choose it over macOS or Windows for daily work. That's a much higher bar.

**How does Personal Intelligence handle edge cases?** Searching across Gmail, Photos, and Drive sounds transformative. But what happens when the AI surfaces the wrong context? What happens when it surfaces something private in a shared screen? The design decisions around failure modes will tell you more about this feature's viability than any demo of it working perfectly.

The AI Master Platform — Google's hands-on learning environment with Gemini models and an AI tutor serving 5,000+ learners — is a quieter announcement that signals something important. Google is investing in building the next generation of developers who think Gemini-first. That's a long game play, and it's the kind of strategic move that's easy to overlook in a keynote packed with flashier reveals.

## Where This Leaves Us

Three years ago, Google was the AI company that missed its own moment. The one that had the research, the talent, and the infrastructure but couldn't ship a product that matched what a startup in San Francisco launched over a long weekend.

That narrative is dead. What Google is bringing to IO 2026 isn't just a collection of impressive products — it's a demonstration of vertical integration that no other company can match. From custom silicon (Ironwood) to foundation models (Gemini 4) to on-device AI (Nano 4) to operating systems (Android 17 and Aluminium OS) to physical hardware (AI glasses and Atlas robots) to the largest consumer AI deployment in history (Personal Intelligence for 2 billion users) — no other organization has this breadth.

Whether that breadth translates to products people actually choose over the alternatives is the question IO 2026 needs to answer. My bet? At least two of these announcements will meaningfully change how I work within six months. I just don't know which two yet.

I'll be covering the keynote live on May 19. If you've been building with Gemini models — or if you've been holding off because the ecosystem wasn't mature enough — this is the event that forces a decision.

The clock hits 10:00 AM Pacific on May 19. I'll be watching. And based on everything I've seen so far, I think you should be too.

## Frequently Asked Questions

### When is Google IO 2026 and how can I watch?

Google IO 2026 runs May 19-20, 2026 at the Shoreline Amphitheatre in Mountain View, California. The keynote starts at 10:00 AM PT on May 19 and will be livestreamed on Google's IO website and YouTube channel. Registration for in-person and virtual attendance is available at io.google/2026.

### What is Gemini 4 and when will developers get access?

Gemini 4 is Google DeepMind's next flagship AI model, scoring 84.6% on ARC-AGI2 with a 2M+ token context window and sub-300ms latency. Google will preview Gemini 4 at IO on May 19, with wider developer access expected in late 2026 or early 2027. For more on Google's current model capabilities, see my [Gemini 3 Deepthink hands-on review](/content/mejba.me/gemini-3-deepthink-tested.md).

### What is Google Aluminium OS?

Aluminium OS is Google's new Android-based desktop operating system designed to compete with Windows and macOS. It runs all Play Store apps natively with full keyboard, mouse, and window management support, and has Gemini AI embedded at the OS level. Hardware from HP, Lenovo, Acer, ASUS, and Samsung will ship with Aluminium OS pre-installed starting late Q3 2026.

### How does Google's Apple partnership affect Siri?

Apple and Google have entered a multi-year deal where Apple Foundation Models will be built on Gemini technology. Apple is distilling Gemini into smaller on-device models for iPhone, iPad, and Mac. A redesigned Siri with chatbot capabilities, web search, and image generation is expected at WWDC on June 8, 2026, with rollout alongside iOS 27.

### Is Google Ironwood TPU available for developers?

Ironwood is Google's 7th-generation TPU delivering 42.5 exaflops across a 9,216-chip superpod. It offers 4x better performance per chip and 192 GB HBM per chip. Ironwood will be available through Google Cloud later in 2026, with pricing and access details expected at IO.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
