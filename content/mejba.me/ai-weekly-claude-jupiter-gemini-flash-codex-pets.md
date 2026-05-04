**BRAND:** mejba.me
**TITLE:** AI Weekly: Claude Jupiter, Gemini Flash, Codex Pets
**META TITLE:** AI Weekly Roundup May 2026: Jupiter, Flash, Codex, Grok
**SLUG:** ai-weekly-claude-jupiter-gemini-flash-codex-pets
**PRIMARY KEYWORD:** AI weekly roundup May 2026
**META DESCRIPTION:** Claude Jupiter, Gemini 3 Flash, Codex Pets, ARC-AGI-3 scores, Copilot Max, Grok 4.3 Imagine — what I tested, what to ignore, and what changes Monday.
**TAGS:** AI Weekly, Claude Jupiter, Gemini 3 Flash, OpenAI Codex, Grok 4.3

---

# AI Weekly Roundup May 2026: Jupiter, Flash, Pets, and the ARC-AGI Reality Check

The first ping landed at 4:47 AM Bangladesh time. A friend in San Francisco — three hours into a debugging spiral, two hours from his red-eye to JFK — sent a screenshot of a Code with Claude conference banner. May 6, 2026. San Francisco. Then a second screenshot, this one from the [TestingCatalog scoop](https://www.testingcatalog.com/anthropic-tests-jupiter-v1-p-before-potential-launch-on-may-6/): "Anthropic tests Jupiter-v1-p before potential launch on May 6."

I was not even out of bed yet. By the time I had coffee, six other things had hit. xAI quietly pushed Grok 4.3 to the API at $1.25 input per million tokens — a 40% price cut against the previous tier. OpenAI shipped Codex Pets, which sounds like a joke until you actually use one. Google had a new Gemini 3 Flash variant battle-testing in Arena. ARC Prize had published a brutal post-mortem on GPT-5.5 and Opus 4.7's performance on ARC-AGI-3. And someone in a private Discord I lurk in had screenshots of what looked like a leaked GitHub Copilot tier called "Max" with a $99/month placeholder.

That was Tuesday. By Saturday, I had hands-on time with four of these, deep research on two more, and one strong opinion about the ARC-AGI-3 numbers that I think most coverage is missing.

This is my AI weekly roundup May 2026 edition. Six stories. Three I tested. Two I tracked closely. One I think is going to age badly inside thirty days. Let me walk you through what is actually shifting under all the press releases — and what to do with that information by Monday morning.

If you want context on how I usually sort the noise from the signal, my earlier [signal-versus-noise breakdown of April's launch flood](https://www.mejba.me/ai-news-week-april-2026-signal-vs-noise) sets the frame for how I rank these.

## The Story That Frames the Whole Week: Claude Jupiter

Anthropic does not leak. That is the first thing worth saying. When something escapes the perimeter, it is almost always because a piece of internal infrastructure was bundled into a public artifact — like the [March 31 source-code spill](https://fortune.com/2026/03/31/anthropic-source-code-claude-code-data-leak-second-security-lapse-days-after-accidentally-revealing-mythos/) that exposed roughly 512,000 lines of TypeScript and a roadmap nobody was supposed to see for another six weeks.

Claude Jupiter is the next chapter of that story. The codename surfaced in red-teaming logs first, and TestingCatalog independently confirmed Anthropic was running a fresh round of safety evaluations on a build labeled `jupiter-v1-p` ahead of the May 6 Code with Claude developer event in San Francisco. The pattern is identical to last year. In May 2025, Anthropic ran the same kind of pre-release jailbreak sweep under a planetary codename — Neptune — and Claude 4 launched weeks later.

So what is Jupiter, actually? Here is where the leak archaeology gets fun.

The March source-code dump contained references to two unreleased models: **Opus 4.7** (which has since shipped — I covered the [vision benchmark jump that made it real](https://www.mejba.me/ai-news-week-april-2026-signal-vs-noise) in my April roundup) and **Sonnet 4.8**, which has not. The naming convention matters: Anthropic typically ships Sonnet one to four weeks after the corresponding Opus, and Sonnet 4.7 was skipped entirely. Multiple analysts looking at the leak — [NxCode's breakdown](https://www.nxcode.io/resources/news/claude-sonnet-4-8-release-date-features-what-to-expect-2026) and the Discord-confirmed cross-references in the [Fordel Pulse analysis](https://fordelstudios.com/pulse/anthropic-leaks-claude-code-source-opus-47-sonnet-48-mythos-exposed) — landed on the same conclusion. Jupiter is almost certainly Sonnet 4.8.

Why does that matter more than another point release? Because Sonnet is the workhorse. Opus 4.7 is the model I test for vision and complex reasoning. Sonnet is the one that runs every Claude Code agent I have shipped in the last six months. Every routine, every sub-agent in my swarm, every cron-driven research pipeline — they all run Sonnet because the cost-to-capability ratio works. A Sonnet upgrade is a forced upgrade for hundreds of thousands of working agent stacks the moment it ships.

Three things I am watching at the May 6 event:

First, the **vision parity question**. Opus 4.7's vision jumped from 54.5% to 98.5% on Anthropic's internal visual acuity benchmark. If Sonnet 4.8 closes even half of that gap, my Claude Code screenshot-driven workflows get a step-function upgrade overnight.

Second, the **Cardinal feature**. This was the other thing the source leak exposed — and the part most coverage missed. According to [Wes Roth's read](https://x.com/WesRoth/status/2050168360420151591) of the leaked code, Cardinal is a visual retrospective dashboard inside Claude. You pick a month. The feature surfaces what topics you focused on, which conversations clustered around the same problem, which work styles dominated. It is essentially a year-in-review for your Claude usage, but always-on, always-fresh. If you have been wondering when an LLM would expose its own observability layer to the user — this is that moment.

Third, the **UI/UX redesign rumors**. The same March leak referenced design tokens and component libraries inconsistent with the current Claude.ai interface. A redesign across web, mobile, and desktop tracks with what you would expect from a company about to ship a new flagship Sonnet plus an analytics product on top of it.

The Jupiter event is six days from when I am writing this. By the time you read this post, you may already know what shipped. But the framing that matters is this: Anthropic is not just releasing a model on May 6. They are releasing **a model plus a dashboard plus a visual language**, and that combination tells you they are no longer competing for "best benchmark." They are competing for **most-used surface in your week**.

That is a different fight. Watch the keynote with that lens.

## Gemini 3 Flash: Google's Quiet Top-of-Funnel Move

While everyone was watching San Francisco, Google was running a much subtler play.

A new Gemini 3 Flash variant has been showing up in Arena — the rebranded LMArena, which [officially changed its name on January 28, 2026](https://en.wikipedia.org/wiki/LMArena). The model's exact build number is not public yet. It could be a 3.1 Flash refresh, a 3.2 Flash slot, or even an early 3.5 Flash candidate. Google is using Arena's blind battle mode the way the rest of the industry uses internal eval sets — letting the public vote, then watching the win-rate curves move.

Here is what I tested. I fed Arena's battle mode the exact same brief I used last week against Gemini 3.1 Pro: "build me an infinite-terrain Minecraft clone in a single HTML file with mouse-driven block placement and destruction, plus a procedurally generated landscape that loads new chunks as the player moves." It is the same test that flagged [Opus 4.7's Minecraft clone moment](https://www.mejba.me/gemini-opus-antigravity-minecraft-clone) as worth paying attention to.

The unknown Flash variant produced a working voxel world in one shot. Block placement worked. Block destruction worked. The chunk loading was rough at the edges — pop-in artifacts when you walk fast — but the core engine loop was sound. Quality felt closer to 3.1 Pro than to last year's Flash baseline. That is the headline. Google's cheap, fast tier is now producing output that would have been mid-tier-Pro last quarter.

Layer in the [Gemini 3.1 Flash-Lite rollout on Vertex AI](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-1-flash-lite/), priced at $0.25 per million input tokens and $1.50 per million output tokens, and the strategy clarifies fast. Google is collapsing the price-to-quality curve at the bottom of its lineup. Flash-Lite is 2.5× faster on time-to-first-token than 2.5 Flash with a 45% bump in output speed. Paired with whatever this new Arena variant turns out to be, Google is running the most aggressive cost-down strategy in the industry right now.

What does that mean for your stack?

If you are running large-volume background agents — content moderation, classification, translation, automated tagging — Gemini Flash-Lite at $0.25 per million input is a real hard look. I have a Claude Code routine that classifies and tags incoming research notes. Sonnet costs about $3 per million input. Same job on Flash-Lite would be 12× cheaper on the input side and roughly 7× cheaper on output. For one workflow, the math is irrelevant. For an agent fleet running at scale, that is the difference between a hobby project and a viable margin.

The Arena variant is also worth watching for one specific reason. If you can submit to Arena's battle mode, vote on a few prompts, and then access the model under test through the post-vote interface, you have a free preview of an unreleased model. Google has done this before — it is how Gemini 2.5 Pro was first rumored — and the workflow is the same now under the new Arena branding.

I would not architect production around it. But for prompt-engineering experiments? Free preview access to whatever ships next. Take the gift.

## OpenAI Codex Pets: The Joke That Is Not a Joke

The first time someone showed me a Codex Pet in a screenshare, I laughed.

It was a small pixel-art creature — a sort of Tamagotchi-meets-Slime — bouncing on the corner of his screen while Codex worked through a refactor in the background. The pet's animation state changed depending on what Codex was doing: idle when waiting, scuttling when actively running tools, lying down when blocked on a permission prompt. He had named his Mango.

I dismissed it as whimsy. Then I installed it.

[OpenAI shipped Codex Pets](https://x.com/OpenAIDevs/status/2050277882715611419) as overlay avatars that live on your desktop. Eight predefined characters out of the box. Custom pet generation via Codex Skills using your own character art or screenshots. Type `/pet` to summon or hide them. The pet shadows the agent's status — running, waiting, ready for review — visible in your peripheral vision while you work in another window.

Why this matters more than it looks: **it solves the agent observability problem at the human-attention layer**.

The hidden cost of running long-horizon background agents is not compute. It is context-switching. You start a Codex job, switch to Slack, fall into a thread, forget the job is running, then either refresh the tab obsessively or miss the moment it finishes. The pet collapses that loop into a glance. Your eye catches motion, you look, you know the state, you move on.

I gave it three days on my normal Codex workflow. By day two, I was checking the pet the way I check whether my coffee is still hot — passive, ambient, no friction. By day three, I had stopped opening the Codex window to check status entirely. The pet had absorbed the job.

Compare that to Claude Code's terminal. I love the terminal. I have written about [Claude Code's daily-driver workflow](https://www.mejba.me/claude-code-1m-context-management) more times than I can count. But the terminal demands attention. You either watch it or you do not. There is no middle state. Pets are the middle state — and OpenAI is the first lab to ship that affordance to the desktop.

The migration system OpenAI shipped alongside Pets is the unlovely sibling that will quietly matter more. It imports settings, plugins, agents, and project configs from competitors — Claude Code chief among them. That is OpenAI saying out loud what their product strategy has been doing quietly for months: **lower the switching cost from Claude Code to Codex to zero**. If you have ever wanted to run a side-by-side, this is your moment. The migration tool drops the activation energy to a button click.

I would not migrate fully. My core agents stay on Claude Code because Sonnet's reliability under tool use still wins for the workflows I care about. But I now run Codex as a dedicated parallel runtime for the kind of work I described in my [two-agent Claude Code and Codex workflow post](https://www.mejba.me/claude-code-codex-two-agent-workflow). Pets make Codex livable as a background runtime in a way it was not before.

## ARC-AGI-3: Why the Numbers Are Brutal — and Misleading

Here is the table that has been making the rounds this week:

| Model          | ARC-AGI-3 Score (High Mode) |
| -------------- | --------------------------- |
| GPT-5.4        | ~0.02%                      |
| GPT-5.5        | ~0.4%                       |
| Opus 4.6 (Max) | ~0.5%                       |
| Opus 4.7       | ~0.2%                       |

**Question one: are these numbers right?**

Roughly yes, with caveats. The ARC Prize team published a [detailed analysis of GPT-5.5 and Opus 4.7](https://arcprize.org/blog/arc-agi-3-gpt-5-5-opus-4-7-analysis) earlier this month, and the broader picture from the [March developer preview](https://medium.com/@AdithyaGiridharan/arc-agi-3-dropped-and-frontier-ai-scored-less-than-1-90cd70e65a61) confirms that frontier models are scoring well below 1% on the official no-harness leaderboard. Gemini 3.1 Pro hit roughly 0.37%. Opus 4.6 came in around 0.2%. The "high mode" numbers in the table above are leaked figures floating in private channels — directionally correct, exact decimals contested.

The honest read of the official ARC Prize leaderboard is this: **every frontier model from every major lab is failing the test by roughly the same margin**.

**Question two: why?**

ARC-AGI-3 is not the static pattern-matching test of ARC-AGI-1 or 2. It is interactive. You are given a novel environment with novel rules, and you have to figure out the rules by acting in the environment. The benchmark explicitly tests something none of these models were trained for: **learning a new task by interaction in real-time, in an environment whose physics nobody on the training crew has seen**.

The failure mode is consistent across labs. Models try a few actions, build an internal hypothesis about what is happening, then commit hard to that hypothesis even when subsequent observations contradict it. They cannot update. They cannot back out of a wrong frame. They keep pushing on a door that is clearly locked because their first-pass model said the door opens that way.

That is not a "we need more data" problem. That is an architecture problem.

**Question three: what does it mean for your work?**

This is where I think the discourse is getting it wrong. The narrative is "AI cannot really reason, the labs are bluffing, AGI is far." The narrative is half right. ARC-AGI-3 is a real test of generalized fluid intelligence, and the fact that GPT-5.5 and Opus 4.7 cannot crack 1% says something true about the gap between current frontier models and human-flexible reasoning.

But here is what it does not say. It does not say these models are useless for the tasks they were trained for. They are not failing your codebase. They are not failing your design system. They are not failing your research pipeline. They are failing a brand-new interactive environment with rules they have never seen.

The thing to take away is this: **the more your daily workflow looks like ARC-AGI-3 — novel, interactive, requiring on-the-fly belief revision under contradicting evidence — the less these models will help.** The more your workflow looks like SWE-bench, Figma reasoning, or pattern-matching across a known domain, the more they will continue to lift you. Calibrate accordingly.

I would tape that sentence above your monitor.

## GitHub Copilot Max: The Tier I Cannot Verify Yet

Now the rumor I am least sure about.

The brief I started this post from listed a "Copilot Max" tier at a $99/month placeholder. I went looking. I could not find it on the [official GitHub Copilot plans page](https://github.com/features/copilot/plans), the [GitHub Docs plans index](https://docs.github.com/en/copilot/get-started/plans), or any verified Microsoft announcement. The current confirmed tiers are Copilot Free, Copilot Pro at $10 a month, Copilot Pro+ at $39 a month, Copilot Business at $19 a user, and Copilot Enterprise at $39 a user.

What is real is the pricing model shift. Effective June 1, 2026, [GitHub Copilot is moving to usage-based billing](https://github.blog/news-insights/company-news/github-copilot-is-moving-to-usage-based-billing/) — every plan gets a monthly allotment of GitHub AI Credits, with the option to buy additional usage. That is the structural change. Existing Pro+ users keep premium request behavior through the transition. New sign-ups have been paused while the plan reshuffle stabilizes.

So where is "Max" coming from? My best read after spending an afternoon with this:

It is either an internal placeholder name for a future high-cap power-user tier — the one that absorbs the heaviest token consumers when usage-based billing goes live — or it is an enterprise-tier-plus product I have not seen public surface area for yet, possibly shipping alongside the June 1 billing switch. The $99 placeholder is consistent with where a bring-your-own-budget tier would land relative to Cursor, Codex Pro, and Claude Max.

If you are a Copilot power user right now, here is what to actually do: do not pay for an unverified tier. Wait until June 1. Use your existing Pro+ allotment until the billing switch lands, then re-evaluate based on the published credit pricing. Anyone telling you to pre-commit to a $99/month placeholder before GitHub publishes it has a longer leash on rumor than I do.

I will update this post the moment the tier ships officially or the rumor evaporates.

## xAI Grok 4.3 and the Imagine Agent: Aggressive Pricing, Real Canvas

The week's loudest pricing move did not come from OpenAI or Anthropic. It came from xAI.

Grok 4.3 [shipped to the xAI API on April 30, 2026](https://the-decoder.com/xai-drops-grok-4-3-with-steep-price-cuts-and-an-imagine-agent-mode-for-creative-projects/) at $1.25 per million input tokens and $2.50 per million output tokens, with a one-million-token context window. That is roughly 40% under the previous Grok tier and competitive with Sonnet's input pricing, while staying under GPT-5.5 and Opus 4.7 on raw cost. The model still does not beat Opus 4.7 on the benchmarks I care about — coding accuracy, vision, long-horizon reasoning — but at this price, it does not need to. It needs to be 70% as good for 50% of the cost. It clears that bar.

The more interesting drop is the Imagine agent.

[xAI's Imagine agent](https://www.testingcatalog.com/xai-debuts-imagine-agent-in-grok-with-open-canvas-ai-workspace/) replaces Grok's chat interface with an infinite canvas. A single workspace where you brainstorm with text, generate images, edit them in place, convert them to video, lay them out beside more text, and keep iterating. No tool-switching. No copy-paste between Midjourney and a video tool and a doc. The whole creative output lives on one continuous surface, generated and refined by the same agent.

I have not had hands-on access yet — Imagine is rolling out in waves and I am not in the early cohort. But I have watched two creators I trust use it for short film generation and product UGC. The pattern they both landed on is identical: the canvas removes the cognitive cost of remembering which tool holds which artifact. Everything is here. Everything updates. The agent moves inside your work instead of you ferrying outputs between agents.

If you have ever tried to maintain creative coherence across a brand campaign — same characters, same color palette, same vibe across stills, video, copy, and motion — you know the pain. You spend more time making sure Tool A's output looks like Tool B's output than you spend making either one. The infinite canvas attacks exactly that problem.

Compare to Claude Design, which I covered when [Anthropic launched it in mid-April](https://www.mejba.me/ai-news-week-april-2026-signal-vs-noise). Claude Design is document-shaped. Slides, decks, one-pagers. Imagine is canvas-shaped — more like Figma than Keynote. Different surfaces, different jobs. If your work is bounded — pitch decks, prototypes, structured exports — Claude Design wins. If your work is generative and visual-first — campaigns, films, brand worlds — Imagine is the closer fit.

I expect to spend two weeks with Imagine the moment my access opens, and I will publish a hands-on then. For now: it is real, the canvas is the right metaphor, and the price-to-capability curve on Grok 4.3 underneath it gives xAI a credible run at the cost-conscious creative tier.

## What I Am Doing Monday Morning

If you skim every newsletter on Sunday night, this is the part you should read. Here is what shifts in my actual workflow this week:

**1. I am pre-watching the May 6 Code with Claude keynote.** Sonnet 4.8 — if Jupiter is what we think it is — forces a review of every Claude Code agent I have running. The vision delta from Opus 4.7 was real enough that even a partial Sonnet upgrade changes which screenshot-driven workflows are viable. I have a post-keynote re-test scheduled for May 7.

**2. I am benchmarking a Gemini Flash-Lite migration for one specific routine.** My research-classification cron runs Sonnet at ~1.2M input tokens per week. Same job on Flash-Lite is roughly $0.30 a week instead of ~$3.60. I am running a two-week parallel A/B starting Monday. Output quality is the gate. If Flash-Lite holds 95%+ accuracy on classification, the routine moves.

**3. I am installing Codex Pets on my main machine and giving it two weeks.** Not a migration — a peripheral runtime. The pet sits in the corner. Codex runs longer-horizon, less-trusted refactors. Claude Code stays primary for in-repo precision work. I will evaluate whether the ambient observability changes how often I delegate to Codex versus driving Claude Code directly.

**4. I am not paying for an unverified Copilot Max tier.** I am going to wait for June 1's usage-based billing rollout, watch what the published credit pricing looks like, and decide then.

**5. I am keeping ARC-AGI-3 taped above my monitor.** Not as a benchmark to optimize for. As a reminder. Every time I catch myself expecting an agent to do something it has never been trained on, I look up. The score is 0.4%. Calibrate.

The week was loud. Most of it will not survive the next thirty days. But Jupiter and Pets will. Bookmark this post and re-read it the day after May 6. We will know which calls landed and which ones fell apart, and that is the only way to get better at predicting the next round.

## Frequently Asked Questions

### What is Claude Jupiter?

Claude Jupiter is Anthropic's internal codename for a new model build undergoing pre-release safety evaluations ahead of the May 6, 2026 Code with Claude conference in San Francisco. Based on the March 2026 source-code leak, multiple analysts believe Jupiter is Claude Sonnet 4.8 — the next workhorse model in Anthropic's lineup. The codename follows the same planetary pattern Anthropic used last year with Neptune, which preceded Claude 4.

### When does Sonnet 4.8 launch?

The most likely launch window is on or shortly after May 6, 2026, at the Code with Claude developer conference in San Francisco. Anthropic typically ships Sonnet versions one to four weeks after the corresponding Opus, and Opus 4.7 has already shipped. Note that Sonnet 4.7 was skipped — the next Sonnet release is expected to be 4.8.

### What are Codex Pets in OpenAI Codex?

Codex Pets are animated overlay avatars that live on your desktop and reflect Codex's background activity in real time. The pet's animation state shows whether the agent is running, waiting, ready for review, or blocked on a prompt. Type `/pet` to summon or hide them. Eight predefined pets are available, plus custom pet generation via Codex Skills.

### Are the ARC-AGI-3 scores really under 1% for frontier models?

Yes. According to the ARC Prize team's public analysis, GPT-5.5, Opus 4.7, and Gemini 3.1 Pro are all scoring under 1% on the official no-harness ARC-AGI-3 leaderboard as of May 2026. The benchmark tests novel interactive reasoning rather than static pattern matching, and current frontier architectures struggle to update internal hypotheses mid-task — the core failure mode the benchmark is designed to expose.

### Is GitHub Copilot Max a real tier?

As of early May 2026, GitHub Copilot Max is not a publicly listed tier. The official Copilot plans are Free, Pro ($10), Pro+ ($39), Business ($19/user), and Enterprise ($39/user). The "Max" name and $99 price point appear to be either a leaked internal placeholder or pre-launch enterprise tier connected to GitHub's June 1, 2026 transition to usage-based billing. Wait for the official rollout before subscribing.

### What is Grok 4.3's Imagine agent?

Imagine is xAI's agent mode for Grok, launched alongside Grok 4.3 in late April 2026. It replaces the chat interface with an infinite canvas where users can brainstorm with text, generate and edit images, convert outputs to video, and iterate across a single continuous workspace — without switching between separate creative tools. Grok 4.3 is priced at $1.25 input and $2.50 output per million tokens with a one-million-token context window.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

- **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
- **Portfolio**: [mejba.me](https://www.mejba.me)
- **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
- **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
- **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
