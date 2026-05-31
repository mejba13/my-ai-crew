**BRAND:** mejba.me
**TITLE:** AI Roundup May 14 2026: Six Days From Google I/O
**META TITLE:** AI Roundup May 14 2026: Gemini 3.2, GPT-5.6, Claude Code
**SLUG:** ai-roundup-may-14-2026-gemini-gpt-anthropic-figure
**PRIMARY KEYWORD:** AI roundup May 14 2026
**META DESCRIPTION:** Gemini 3.2 leaks, GPT-5.6 Ember-Alpha, Claude Code's billing controversy, Figure's 8-hour autonomous shift. My builder's-eye view six days from I/O.

**TAGS:** AI Weekly, Gemini 3.2, Claude Code, GPT-5.6, Figure AI

---

# AI Roundup May 14 2026: What's Actually Happening Six Days From I/O

It is 7:14 AM on Thursday and I have three terminals open, one of them is yelling at me, and my coffee is already cold.

The terminal that is yelling at me is Claude Code. Specifically, it is the daemon I run for one of my brand sites, the one that quietly reconciles content across four content directories every morning. Two weeks ago it ran on autopilot for the price of a single Max subscription. As of May 13, that exact same job started costing me real API credits on top of the subscription — because Anthropic just split programmatic usage into its own metered bucket. Same agent. Same model. Same prompt. Different bill. I am still working out the new math.

In another tab, a Reddit thread is unfolding in real time about somebody's iOS Gemini app cycling through model versions over twenty-four hours and landing on something called Gemini 3.2 Flash. There are screenshots. There is a redesigned interface with a pill-shaped prompt box and a pulsating gradient background that nobody asked for. Google I/O is six days away. The leaks are not subtle.

And on my second monitor, an X post about Figure's Helix-02 robots is sitting open with 14 million views — a livestream where humanoids ran an entire 8-hour shift in a warehouse without a single human in the loop. Battery swaps. Self-diagnosis. Multi-robot coordination through visual cues. The conveyor belt did not stop once.

That is what this AI roundup May 14 2026 looks like from my desk. Four labs, one robotics company, and an industry on the cusp of either a real breakthrough at Google I/O or a quietly embarrassing letdown. I want to walk you through what I am tracking, what I have already tested, what nobody is saying out loud, and what I would do this week if I were you.

If you want the bigger frame for how I have been reading May 2026 — the SubQuadratic story, the finance agent war, the Mariner sunset — my [field report from earlier this month](https://www.mejba.me/ai-inflection-point-may-2026-google-io) sets the table. This roundup picks up where that one left off.

## The Setup: Six Days From the Most Loaded Keynote in Two Years

There is a thing that happens in the week before a major AI keynote. The leaks accelerate. The labs counter-program. The benchmarks get fishy. The press releases get vague. And somewhere underneath all of it, the actual product cycle keeps grinding, which is where the interesting story usually lives.

This week was that, in compressed form.

Google has Gemini 3.2 variants being A/B tested inside the iOS app, [surfacing on LMArena and AI Studio without an announcement](https://www.aixploria.com/en/ai-radar/google-gemini-3-2-flash-leaked-ios-lm-arena/) — exactly the [stealth-upgrade pattern I tracked on Flash three weeks ago](https://www.mejba.me/gemini-3-flash-stealth-upgrade-lmarena). OpenAI has GPT-5.6 in internal testing under two codenames, with a Polymarket prediction sitting at [89% odds for a release before June 30, 2026](https://finance.biggo.com/news/QzC-JJ4BtCxy99G5fXuF). Anthropic just shipped a 50% weekly limit increase for Claude Code subscribers — and almost simultaneously broke off SDK and GitHub Actions usage into a separate paid credit pool that has the community furious. Figure ran a fully autonomous 8-hour shift on camera. And somewhere in the background, [Hermes Agent](https://www.mejba.me/openclaw-hermes-multi-agent-workflow) is still quietly compounding into the most interesting open-source agent project of the cycle.

This is not five unrelated stories. It is one story told from five different angles. Compute is constrained. The labs are out of free-sample budget. Robotics is catching the curve. And the question every builder reading this should be sitting with is the same one I am sitting with: which of these moves do I bet on this week, and which do I wait out?

Let me show you what I am betting on. I will start with Google, because the keynote is six days away and the noise is loudest there.

## Google: Gemini 3.2 Is Less Than the Leak Implies

Here is the part of the cycle where I usually have to fight the urge to overhype.

The Gemini 3.2 Flash leak is real. A small number of iOS users on app version 1.2026.1710205 saw the model appear in their picker. [LMArena was running silent benchmarks on it](https://www.aixploria.com/en/ai-radar/google-gemini-3-2-flash-leaked-ios-lm-arena/). The reported pricing — $0.25 per million input tokens — undercuts Gemini 3.1 Pro while reportedly matching much of its capability in coding and creative tasks. The "Liquid Glass" UI redesign is a real screenshot, not a fan mock. None of that is in dispute.

What I want to push back on is the framing.

I have spent time with the leaked variants this week through every backchannel I can stand up — model picker rotations, the AI Studio preview, a few of the LMArena battles where the upgraded model surfaced. Flash is genuinely impressive on SVG generation. I ran my standard PS5 controller prompt and an Xbox Series X controller prompt and got accurate proportions on both, with correct button placement and proper triggers. That is a meaningful step up from the original [Gemini 3 Flash](https://www.mejba.me/gemini-3-flash-stealth-upgrade-lmarena) baseline I tested in April. The single-prompt Mac OS clone demo making the rounds on X — a desktop interface with functional window chrome, menu bar, and three working apps in one shot — is real. I reproduced a close version of it.

But here is what is not making it into the leak coverage. The main Gemini 3.2 variant — the one that will likely be branded "Pro" at the keynote — is not a leap. In side-by-side front-end generation tests against Gemini 3.1 Pro, the upgraded model actually produced more repetitive UI patterns. Cards with the same rounded-corner-pill-button-icon structure. Hero sections that all rhyme. A faint regression to the kind of design output you would expect from a model two generations older. I tested the same prompts on Claude Opus 4.7 and the gap was not subtle.

The internal codenames are even more interesting. There are at least two other variants showing up in side-channel testing — call them Sprite and Cola, because that is what they appear under in the routing logs. The Cola variant runs with noticeably higher reasoning effort and produces better outputs across long-context tasks. That one might be what gets the "Deep Think" or "Ultra" badge at I/O. Sprite looks like a speed-tuned mid-tier that probably becomes the Flash replacement in the lineup.

So my honest read on what Google ships on May 19 or 20: a real, useful Flash upgrade with strong SVG and single-prompt UI generation. A Pro model that is incremental, not transformational. A Deep Think or Ultra variant that does the heavy lifting on the benchmark slides. Public expectations for a Sonnet-4.6-style leap are too high. I would calibrate down.

There is one other thing leaking out of Google that nobody is properly framing yet.

### The Omni Video Model Is the Real Story

[Gemini Omni leaked online this week](https://wavespeed.ai/blog/posts/google-omni-video-model-leak-i-o-2026/) — possibly Veo 4, possibly a separate product line, the naming is still murky. The demos that have surfaced show video editing and scene modification with the kind of motion preservation and structural consistency that previous Veo generations could not hold across cuts. Faces stay correct across angle changes. Background geometry survives camera moves. Object permanence is sharper than anything I have seen out of Sora 2 or Kling 3.0 on the same prompts.

It is still very early. The demos are short. There is no public access. The hands and the fine motion details still drift in places where you would expect a frontier model to hold steady. But the trajectory is clear, and if Google ships any version of this at I/O with a reasonable usage tier, it changes the [video pipeline I have been running](https://www.mejba.me/ai-video-creation-hyperframes-claude-code) for one of my brands.

My bet: Omni gets a tease at I/O, not a full launch. Limited preview access. Real ship by Q3.

That covers Google for now. Let me move to the lab that is making the loudest noise inside developer Slack channels this week.

## Anthropic: A 50% Limit Increase, A 10x Effective Cut, And A Trust Problem

I am going to try very hard to write this section without venting.

I will probably fail.

Anthropic shipped two things almost simultaneously on May 13 that are pulling in opposite directions, and you cannot understand one without the other. Let me lay them both out, then tell you what it actually means at my desk.

**The good news: weekly limits are up 50% through July 13.** [Anthropic announced](https://www.anthropic.com/news/higher-limits-spacex) that Claude Code weekly limits are getting a 50% bump for Pro, Max, Team, and seat-based Enterprise users, running through July 13, 2026. The free plan is excluded. This builds on a [doubling of limits in early May](https://9to5google.com/2026/05/06/claude-code-is-getting-higher-usage-limits-doubled-for-most-users/), funded in part by a fresh SpaceX compute partnership. On paper, a Max user now has roughly 3x the weekly Claude Code budget they had in mid-April. That is genuinely meaningful for daily interactive coding work — the kind of work where you are sitting at a terminal, typing prompts, watching diffs, shipping.

**The bad news: programmatic usage just left the building.** In the same window, [Anthropic spun out Agent SDK, GitHub Actions, `claude -p`, and any third-party agent](https://www.dataworldbank.net/2026/05/13/anthropic-reinstates-openclaw-and-third-party-agent-usage-on-claude-subscriptions-with-a-catch/) into a separate, metered credit pool. Programmatic workloads now draw from a fixed monthly bucket worth $20 to $200 depending on your plan, billed at API rates, no rollover, expires at end of month. If you blow through it, you are paying API rates on top of your subscription.

If you only use Claude Code interactively at a terminal, this is a net win. You get 50% more headroom and your bill does not change.

If you run automation — and many of the people reading this run automation — your effective usage just got cut anywhere from 10x to 40x.

Let me be specific. I have a few autonomous setups across my brands. One is a content reconciliation agent that runs nightly across all four sites. One is a hourly SEO monitor for one of my client projects. One is a [forked subagent pattern](https://www.mejba.me/forked-subagents-claude-code-anthropic) I built earlier this year for parallel codebase analysis. Two weeks ago, those workloads ran inside my Max subscription's daily and weekly limits — meaning the marginal cost of each run was effectively zero past my flat fee. Today, those workloads draw from a $200 monthly SDK credit bucket at API token rates. The brand reconciliation agent alone is on pace to burn through that bucket in eleven days.

I am not the only one feeling this. The community thread on this change is sitting at multiple thousand replies on Reddit and X. The framing inside Anthropic seems to be that programmatic users were [arbitraging the subscription](https://www.xda-developers.com/anthropics-claude-subscriptions-no-longer-include-agent-sdk-and-claude-p-usage/) — which is technically true, particularly the [OpenClaw-style setups](https://www.mejba.me/claude-bot-open-source-ai-agent) that let users route headless agent workloads through a $20 Pro plan. From a pure unit-economics view, Anthropic is correct that those flows were unsustainable. The split makes business sense.

The problem is not the split. The problem is the way it shipped.

It shipped on the same day as the 50% increase announcement, which made the headline read "Claude Code limits go up!" while the actual experience for half the userbase was "your existing automation just got 10x more expensive." The transparency on what would and would not count against the new credit pool was thin for the first 24 hours. The migration path for existing programmatic workloads is still being figured out. And the underlying message — "we are compute-constrained, so the people running agents are the ones who pay" — does not square with the narrative around the SpaceX compute deal.

Here is my honest read. Anthropic is dealing with a real, structural compute shortage. [Reasoning effort levels on Opus 4.7 have been quietly reduced](https://www.mindstudio.ai/blog/anthropic-compute-shortage-claude-limits) on subscription tiers since late April, which is why some of you have noticed model behavior degrading on long-running tasks. The split-billing move is a way to keep the interactive product margin-positive while pricing programmatic usage at its true cost. That is rational. What is not rational is how the rollout treated the developers who built actual products on top of Claude Code's prior pricing model.

I am still using Claude Code daily. I am not switching. But I have moved three workloads to a hybrid setup where the heavy programmatic work runs through Gemini 3.1 Pro on AI Studio (still effectively free for the volume I need) and the interactive coding work stays on Claude Opus 4.7. Anthropic's [agent SDK](https://www.mejba.me/anthropic-agent-sdk-guide) is still the cleanest API surface to build against — I am just being more careful about which jobs are worth its premium pricing.

The one bright spot from Anthropic this week is genuinely useful.

### Fast Mode Just Became the Default on Opus 4.7

[Fast Mode for Claude Code](https://code.claude.com/docs/en/fast-mode) — the 2.5x speed configuration that runs Opus at higher token cost with no quality change — became the default Fast Mode model on Opus 4.7 as of today, May 14. You toggle it with `/fast` in the CLI. It requires Claude Code v2.1.139 or later.

I have been running Fast Mode on Opus 4.6 for weeks. Turning it on for Opus 4.7 is, frankly, ridiculous. Response times on a multi-file refactor I would normally babysit dropped from roughly 90 seconds to about 36. The model output is identical to non-fast Opus 4.7 in everything I have compared. The trade-off is real — Fast Mode draws from extra usage credits, not your subscription pool — so you do not want it on for everything. For interactive coding where you are actually waiting on responses, it is worth the extra cost. I leave it off for long autonomous runs.

Pro tip: combine Fast Mode with the [skill-based workflow setup](https://www.mejba.me/nine-claude-skills-advanced-workflow) I have been running and the speed becomes legitimately uncomfortable in a good way. The model is generating faster than I can read.

That is Anthropic. Let me move to the other lab that quietly had a real week.

## OpenAI: GPT-5.6 Is In Testing, And There Is A Super-App Hiding

OpenAI did not ship a model this week. They are too busy testing the next one.

GPT-5.6 is in full internal testing under two codenames that surfaced in [developer logs](https://x.com/MikelEcheve/status/2054670845453164906) and on LMArena's anonymous-model rotation: **Ember Alpha** and **Beacon Alpha**. The "-alpha" suffix is meaningful in OpenAI's release pattern. It tends to show up roughly four to six weeks before a public launch. Pair that with the [Polymarket prediction sitting at 89%](https://finance.biggo.com/news/QzC-JJ4BtCxy99G5fXuF) for a GPT-5.6 release before June 30, and the math points to a mid-June drop.

The thing I want to highlight here is what is changing in the testing process itself.

OpenAI is running noticeably longer red-teaming and safety evaluation cycles on GPT-5.6 than they did on GPT-5.5. The internal checkpoints have been visible in Codex logs for weeks, but the testing windows are extended. Multiple reasoning regimes are being benchmarked against each other under different safety tunings before the model gets near a public release decision. This is, in my read, a direct response to the [post-GPT-5.5 hallucination metric disclosures](https://www.mejba.me/gpt-5-6-leak-deepseek-google-pentagon) — where GPT-5.5 Instant dropped hallucinations 52.5% on high-stakes domains, and the company quietly committed to making that the baseline going forward.

I think GPT-5.6 ships with a notably better hallucination floor than GPT-5.5. I do not think it ships with a dramatic intelligence jump. The Spud cycle was the intelligence jump. This cycle is reliability.

There is also a teaser making the rounds about a possible new OpenAI super-app called **CodeX** — capitalized like a product name, not the existing Codex CLI. The details are thin. Some screenshots, some hand-wavy descriptions of "a unified workspace for coding, research, and ops." It could be a rebrand of the existing Codex umbrella with a polished consumer surface. It could be the [browser-first surface I covered last week](https://www.mejba.me/ai-roundup-may-9-2026-os-race) getting a real product wrapper. It could be nothing.

My instinct: this is the productization of the [Codex Chrome extension + remote devbox stack](https://www.mejba.me/codex-spark-gemini-deep-think-coding-models) into something a non-developer can use. If OpenAI is gunning for the OS layer — and the May 9 evidence strongly suggested they are — the next step is wrapping the agent stack in a consumer-friendly app surface. Mid-June would be a logical window. We will see.

What I am doing about it this week: nothing. I am not migrating workloads to OpenAI ahead of a model I have not tested. I am keeping Codex installed and pinned to my dock, and I will run my standard test battery the day GPT-5.6 lands. If it clears a specific bar on reliability — measured against my own internal eval set, not benchmark slides — I will rebalance some workloads then.

That is the big-three labs. Now I want to spend a moment on the story that almost nobody in my feed is screaming about, because I think it should be the actual headline of the week.

## Figure AI: The 8-Hour Shift Just Happened, And You Should Sit With It

I want you to imagine the warehouse for a second.

Standard layout. Conveyor belt running through the middle. Stacks of boxes coming in on one side, packages going out the other. A normal team to run this would be six to eight humans on the floor, plus a manager, plus a maintenance person on call. A shift is eight hours. You take breaks. You swap personnel. You deal with the inevitable jam in the line every forty minutes or so.

Now imagine the same warehouse with no humans on the floor for eight hours straight.

That is what [Figure AI livestreamed last week](https://interestingengineering.com/ai-robotics/figure-helix02-humanoid-robots-8-hour-shifts). A fleet of Helix-02 humanoid robots ran a full 8-hour shift moving packages onto a conveyor belt — detecting barcodes on incoming boxes, picking up the packages, reorienting them so the barcodes face down, placing them in the line. Continuous operation. No teleoperation. No human in the loop.

The Helix-02 neural network is doing all of it on onboard inference. No cloud round-trip. The robots see through their cameras, reason about what they are seeing, plan their motions, execute. When one robot detected an issue with its own performance, it self-diagnosed and walked autonomously to the maintenance area to request a fleet replacement. The other robots adjusted their workflow to cover the gap. The conveyor never stopped.

They coordinate visually. There is no verbal communication, no internal messaging protocol you can read on a network sniffer. They look at each other, observe the state of the line, and adjust. The way a human warehouse crew that has worked together for two years coordinates without speaking.

Three things about this matter to me as a builder, not as a robotics enthusiast.

**One: the inference is happening on-device.** That is the part that should be making cloud AI vendors nervous. If a 1.5kW-class compute envelope can run a vision-language-action model good enough for 8-hour package handling, the long tail of physical world AI does not need a $1B inference cluster. It needs a chip and a power supply. The economics of physical AI just diverged from the economics of cloud AI in a meaningful way.

**Two: the multi-agent coordination is emergent.** The robots were not pre-programmed to nod at each other. The visual coordination came out of training. That is the same pattern I have been watching in [multi-agent coding setups](https://www.mejba.me/anthropic-agent-teams-ai-coding) over the last six months — once you let agents observe each other's state, they start coordinating in ways the original training did not explicitly specify. We are watching the same emergent behavior show up in physical space.

**Three: the labor implication is not 18 months away anymore.** I have been writing about [the AI-and-jobs question](https://www.mejba.me/ai-layoff-trap-pigovian-tax-prisoners-dilemma) for a year. The conventional pushback has always been "yeah but physical labor is safe for another decade." That argument got harder this week. A package handling shift is not a thought experiment. It is a real warehouse job category. There are an estimated 1.7 million package handlers in the US alone. The unit economics of a Figure 03 robot at scale is somewhere between $30,000 and $50,000 per unit amortized over its lifetime — well under the loaded cost of a human worker doing the same work over the same window.

I do not say any of this to be doom-y. I say it because the cycle is happening faster than the policy conversation is. If you have not started thinking about what your business does that is physical-world-defensible, this week is a reminder to start.

That covers Figure. Let me sweep up the rest of what is moving this week.

## Quick Hits: Jules, Hermes, And The Open-Source Layer

Two things worth flagging that did not get their own section.

**Google Jules V2 early access is open.** [The form went live](https://x.com/testingcatalog/status/2047042707722281346) for what Google is positioning as "an end-to-end agentic product development platform." The bigger upgrade everyone is watching for: continuous operation, including when the user's device is offline. If Jules V2 ships with truly server-side persistent agent runs — where you can close your laptop, walk away for four hours, and come back to find the work done — that is a competitive answer to where Codex and Claude Code are heading. The waitlist is the right move for now. I am on it. I am not betting any production work on Jules until I can run my standard test battery against V2.

**Hermes Agent continues to be the most interesting open-source project of the cycle.** The [self-improving loop](https://www.mejba.me/hermes-claude-code-vps-discord-deployment) — where Hermes observes its own successful task completions, abstracts them into reusable "trajectories," and gets compounding-better at your specific workflows — keeps shipping updates. The provider integrations have widened. And reports are circulating that the Qwen 3.6 Plus model is being offered free inside Hermes through a news portal partnership for a limited window. (Note: the source material on this hit my desk as "Coin 3.6 Plus" — I am almost certain that is Qwen 3.6 Plus, given the model line and the timing. If you see references to either, they are pointing at the same thing.) For builders running [open-source agent setups](https://www.mejba.me/openclaw-hermes-multi-agent-workflow), Hermes is now firmly in the same conversation as the proprietary players. That was not true six months ago.

That is the field. Let me close with the part you came for — what I would actually do this week.

## What I'd Do This Week As A Builder

Six days from Google I/O. Anthropic billing model shifting under your feet. GPT-5.6 looming. Here is the play.

**Do not pre-migrate.** The single most expensive mistake I see builders make in weeks like this is rushing to switch stacks ahead of a keynote that has not happened yet. The Gemini 3.2 leaks are real, but they are not the final product. The GPT-5.6 codenames are real, but the model is not in production. Wait. Let the dust settle. Run your existing stack one more week.

**Audit your Claude Code automation today.** Specifically: open whatever programmatic workloads you have running on Claude — SDK scripts, GitHub Actions, headless `claude -p` jobs, third-party agents — and price them out at the new credit pool rates. If you find a workload that is going to blow through your $20-to-$200 monthly bucket inside two weeks, you have a decision to make: pay the API premium, port the workload to a cheaper provider for the heavy lifting, or restructure it to do less. Do this before May 31.

**Test Fast Mode on Opus 4.7 if you do interactive coding.** The 2.5x speedup is real. The quality is unchanged. The extra usage cost is contained if you toggle it off for long autonomous runs. This is the single biggest workflow speed win available to Claude Code users this week. Run `/fast` in your CLI. Make it a habit. (Requires v2.1.139 or later — check with `claude --version`.)

**If you build any front-end work with AI, run your standard prompts on Gemini 3.2 Flash this week.** Through AI Studio, through whatever backchannel you have. The SVG generation is strong. The single-prompt UI scaffolding is strong on Flash specifically. For roughed-out hero sections, controller diagrams, icon sets, dashboard skeletons — Flash is genuinely competitive on cost-per-output right now. Save the Pro and Opus tokens for the real work.

**Watch the I/O keynote with a notebook open.** On May 19 or 20, what I will be watching for is not the headline model. It is the depth of the agent story Google tells. Specifically: does Gemini Agent get a real platform reveal? Does Omni get a usage tier? Does Jules V2 get a launch date? Those three signals will tell me more about Google's actual position in the OS race than any benchmark slide.

**And whatever you do this week, do not let the Figure demo slip past you without sitting with it for an hour.** Watch the [livestream replay](https://www.humanoidsdaily.com/news/live-now-figure-03-hits-blistering-2-6-second-throughput-in-8-hour-unedited-shift). Pay attention to the moments where one robot self-diagnoses and walks to maintenance. Pay attention to how the others adjust without missing a beat on the line. That is what an emergent multi-agent system looks like in the physical world, and it just became a real thing this week. Six months ago, that was a research demo. Today it is a product trajectory.

## The Honest Take

Here is what I think is actually happening, if I zoom all the way out.

The labs are out of free-sample budget. SpaceX compute deals, Pentagon contracts, programmatic usage being repriced, longer red-teaming cycles, internal codenames hiding pricing experiments — all of these point at the same underlying reality. We are at the end of the phase where every major model lab eats compute losses to acquire developer mindshare. The next twelve to eighteen months are going to look much more like normal SaaS economics, with all the trade-offs that implies. Free tiers will narrow. Programmatic usage will get priced at cost. Interactive subscriptions will hold. The arbitrage windows that built early 2026's open-source agent boom are closing.

The keynotes you watch in the next six weeks — Google I/O on May 19-20, OpenAI's GPT-5.6 reveal in mid-June, Anthropic's response to whatever Google ships — are going to be the moment the industry decides what the priced-in product layer actually looks like. The free-for-all is ending. The pricing is settling. The differentiation is going to be measured in workflow quality, reliability, and the parts of the stack each lab actually owns.

And underneath all of that, Figure ran a full warehouse shift without humans in the loop. Which is the kind of thing that, in a different week, would have been the only story anyone talked about.

That is what May 14, 2026 looks like from my desk. Six days from a keynote. One week into Anthropic's pricing reset. One leak rotation into Google's new model picker. One livestream into a physical AI future that arrived faster than I expected.

I will write again after I/O. If you are running anything in production on top of these models, batten down the hatches.

It is going to be a loud six days.

## Frequently Asked Questions

### When does Gemini 3.2 launch?
Gemini 3.2 will most likely launch at Google I/O 2026 on May 19-20. The Flash variant has been leaking through the iOS Gemini app and LMArena for over a week, and Google's pattern is to formally announce models that are already running in production A/B tests. Expect a Pro tier alongside Flash, plus a possible Deep Think or Ultra variant.

### What changed with Claude Code limits in May 2026?
Anthropic raised Claude Code interactive weekly limits 50% from May 13 through July 13, 2026. At the same time, Agent SDK usage, GitHub Actions, `claude -p`, and third-party agent calls moved to a separate metered credit pool worth $20-$200 per month depending on plan, billed at API rates. Interactive coding got cheaper. Programmatic usage got dramatically more expensive.

### Is GPT-5.6 coming out soon?
GPT-5.6 is in internal testing under the codenames Ember Alpha and Beacon Alpha, with Polymarket predicting an 89% chance of release before June 30, 2026. Mid-June is the most likely launch window. Expect a meaningful hallucination reduction over GPT-5.5 rather than a dramatic intelligence leap.

### What did Figure AI demonstrate with Helix-02?
Figure AI livestreamed a fleet of Helix-02 humanoid robots running a fully autonomous 8-hour warehouse shift handling package sorting onto a conveyor belt. Coordination was multi-robot and visual-only, with no teleoperation. Robots self-diagnosed faults, requested replacements, and swapped batteries autonomously. All inference ran on-device with no cloud round-trip.

### How does Claude Code Fast Mode work on Opus 4.7?
Fast Mode runs Claude Opus 4.7 with an API configuration optimized for speed, producing identical quality output at 2.5x the speed for a higher per-token cost. Toggle it with `/fast` in Claude Code v2.1.139 or later. On subscription plans, Fast Mode draws from extra usage credits rather than your subscription rate limit pool.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
