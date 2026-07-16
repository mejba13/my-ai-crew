**BRAND:** mejba.me
**TITLE:** AI State of Play May 14 2026: The Pre-I/O Compute Crunch
**META TITLE:** AI State of Play May 14 2026: Pre-I/O Compute Crunch
**SLUG:** ai-state-of-play-may-14-2026-pre-io-compute-crunch
**PRIMARY KEYWORD:** AI state of play May 2026
**META DESCRIPTION:** Gemini 3.2 leaks, GPT-5.6 checkpoints, Claude's SDK billing shift, Figure's 8-hour autonomous shift. The AI state of play May 2026 from a builder's desk.

**TAGS:** AI Industry, Gemini 3.2, Claude Code, GPT-5.6, Figure AI

---

# AI State of Play May 14 2026: The Pre-I/O Compute Crunch From My Desk

It is Thursday morning, six days before Google I/O, and my Claude Code daemon just sent me an email I did not expect to read this year.

The subject line said "Usage credits remaining: $11.42." The body explained, in the kind of language only a billing system writes, that my Agent SDK workload — a nightly content reconciliation job that has run inside my Max subscription for months — had been migrated to a new metered programmatic pool. Same agent. Same prompt. Same model. Different bill. The flat-fee party is over.

I leaned back, set the cup down, and opened a second terminal. A Reddit thread was scrolling past with screenshots of an iOS Gemini app cycling through model versions over twenty-four hours, settling on something labeled Gemini 3.2 Flash with a redesigned UI nobody asked for. On my second monitor, Figure AI's livestream had been running for six hours. Humanoid robots were sorting boxes on a real conveyor belt without a single human in the frame.

That is the AI state of play May 2026 from where I sit. Pre-I/O. Pre-GPT-5.6. Mid-Anthropic-controversy. And mid-something far bigger than any of them: the moment when the labs ran out of free compute to give away, the moment when the productization curve hit the wall of actual scarcity, and the moment when humanoid robotics finally caught up to the demos.

This is not five disconnected stories. It is one story, told from five angles, and the angle that ties them all together is the one nobody puts on the press releases.

Compute is the bottleneck now. Every other headline this week is downstream of that.

Let me walk you through what I have been testing, what I have verified, and what I think actually ships at Google I/O when the keynote opens at 1 PM Pacific on May 19.

## The Frame: Why This Week's News Reads So Strangely

There is a pattern to the week before a major AI keynote. Leaks accelerate. Counter-programming intensifies. The press release language goes deliberately vague. And underneath the noise, the real product cycle keeps grinding — which is where the interesting story usually lives.

This week was that, in concentrated form. Four model labs and one robotics company all moved within seventy-two hours. Google had Gemini 3.2 variants surface inside the iOS app and AI Studio without an announcement, plus an Omni video model spotted in a UI string leak. OpenAI dropped breadcrumbs of GPT-5.6 internal testing under two checkpoint codenames. Anthropic shipped a 50% weekly limit increase for Claude Code on the same day it broke programmatic usage off into a separate credit pool. Figure ran a fully autonomous 8-hour warehouse shift on camera. And somewhere in the background, Nous Research's Hermes Agent crossed 140,000 GitHub stars while Alibaba's Qwen 3.6 series quietly started outperforming models four times its size.

I have been doing this beat for long enough now to recognize when a week is signal versus when it is noise. This week is signal. But the signal is not what the headlines say it is.

The signal is that the frontier labs are out of free-sample budget. The economics that subsidized aggressive distribution from late 2024 through Q1 2026 are tightening hard. Compute partnerships — SpaceX, Stargate, whatever Google reveals next Tuesday — are becoming load-bearing infrastructure rather than press-release garnish. And every "limit increase" or "new tier" announcement from here on is going to come paired with a "but the way we count usage is changing" footnote that does most of the actual work.

If you read this week's news through that lens, none of it is contradictory. All of it makes sense.

Let me start with Google, because the keynote is six days away and the noise is loudest there.

## Google: The Gemini 3.2 Leak Picture Is Mixed

Here is the part of the cycle where I have to fight the urge to overhype.

The Gemini 3.2 Flash leak is real. A small population of iOS users on Gemini app build 1.2026.1710205 saw the new model appear in their picker over the last two weeks. LMArena was running silent benchmark battles on it before Google could announce. The reported pricing — $0.25 per million input tokens — undercuts Gemini 3.1 Pro while reportedly matching much of its capability on coding and creative tasks. The "Liquid Glass" UI redesign that surfaced alongside it — pill-shaped prompt box, pulsating gradient background, model picker moved to a top-left dropdown — is real screenshots, not fan mocks. Polymarket is currently pricing Gemini 3.2 launching before May 31 at around 83% probability.

I spent the week probing the leaked variants through every channel I could stand up. Model picker rotations. The AI Studio preview window. A few LMArena battles where the upgraded model surfaced under blind labels. Here is what I actually found, separated from what the leak coverage is saying.

**Flash is genuinely impressive on SVG generation.** I ran my usual PS5 controller prompt and an Xbox Series X controller prompt and got accurate proportions on both, with correct button placement and proper triggers — a meaningful step up from the [Gemini 3 Flash stealth upgrade I tested in April](https://www.mejba.me/gemini-3-flash-stealth-upgrade-lmarena). The single-prompt Mac OS clone demo making the rounds — desktop interface with functional window chrome, a menu bar, three working apps in one shot — is reproducible. I got close to it on the third attempt with a slightly tightened prompt.

**The main "Pro" variant is not a leap.** In side-by-side front-end generation tests against Gemini 3.1 Pro, the upgraded mid-tier produced more repetitive UI patterns — cards with identical rounded-corner-pill-button-icon structure, hero sections that all rhyme, a faint regression to the kind of design output you would expect from a model two generations older. When I ran the same prompts on Claude Opus 4.7 with [Fast Mode toggled on](https://www.mejba.me/boris-cherny-opus-47-seven-tips), the gap was not subtle.

**There are at least two other variants in side-channel testing.** Internal routing logs and a couple of LMArena blind labels have shown what look like Sprite and Cola codenames — one speed-tuned mid-tier that probably becomes the new Flash replacement, and one higher-reasoning variant that I suspect gets the "Deep Think" or "Ultra" badge at the keynote. The Cola variant noticeably outperforms on long-context reasoning tasks. That is the model to watch.

My honest read on what Google ships on May 19 or 20: a real, useful Flash upgrade with strong SVG and single-prompt UI generation. A Pro model that is incremental, not transformational. A Deep Think variant that does the heavy lifting on the benchmark slides. Public expectations for a Sonnet-style discontinuous leap are too high. I would calibrate down before the livestream starts.

But there is one other thing leaking out of Google that nobody is properly framing yet.

### The Omni Video Model Is the Quieter Story That Matters More

On May 2, an X user spotted a UI string inside the Gemini video generation tab reading "Start with an idea or try a template. Powered by Omni." That string sat next to Toucan, the internal name for Gemini's current video tool. TestingCatalog, the long-running Google leak tracker, picked it up. By the second week of May, a handful of users had reportedly gained early access through pop-ups offering to "create with Gemini Omni," described in the in-app copy as Google's new video model.

Three interpretations are live. Omni could be a public-facing rename for the existing Veo pathway. It could be a new Gemini-trained video model running alongside Veo. Or — and this is the version that would matter — it could be a unified omni-model handling image, video, and text under one architecture, the way GPT-4o was supposed to do but on a much harder modality.

The demo clips that have surfaced show video editing and scene modification with the kind of motion preservation and structural consistency that previous Veo generations could not hold across cuts. Faces stay correct across angle changes. Background geometry survives camera moves. Object permanence is sharper than what I have seen out of Sora 2 or Kling 3.0 on comparable prompts. The hands and fine motion details still drift in places, but the trajectory is the part that should make video tool builders sit up.

If Google ships any version of Omni at I/O with a reasonable usage tier, it changes the [video pipeline I have been running across my brands](https://www.mejba.me/ai-video-creation-hyperframes-claude-code). My bet: Omni gets a tease at I/O with limited preview access, not a full launch. Real shipping is Q3 or later.

That is Google. Now let me turn to the lab making the loudest noise inside developer Slack channels this week.

## Anthropic: A 50% Limit Bump That Quietly Becomes A Cut For Half The Userbase

I am going to try very hard to write this section without venting.

I will probably fail.

Anthropic shipped two things almost simultaneously on May 13 that are pulling in opposite directions, and you cannot read one without the other. Let me lay them both out, then tell you what they actually mean at my desk.

**The headline: weekly limits are up 50% through July 13.** Anthropic raised Claude Code's weekly limits by 50% across Pro, Max, Team, and seat-based Enterprise plans, running through July 13, 2026. The free plan is excluded. This builds on a doubling of five-hour limits announced on May 6, both of them funded in part by the SpaceX compute capacity deal for the Colossus 1 data center in Memphis. On paper, a Max subscriber now has roughly 3x the weekly Claude Code budget they had in mid-April. For daily interactive coding work — terminal open, prompting, watching diffs, shipping — that is meaningful.

**The footnote: programmatic usage just got its own meter.** In the same forty-eight-hour window, Anthropic spun the Agent SDK, GitHub Actions, `claude -p`, and any third-party agent or harness off the general subscription pool and into a separate metered credit bucket. That bucket is fixed monthly at somewhere between $20 and $200 depending on plan, billed at full API rates, with no rollover and no overflow back into the general subscription. If you exhaust it, you pay API rates on top of your subscription.

Read those two together and the trick is visible. If you only use Claude Code interactively at a terminal, this is a net win — more headroom, same bill. If you run any automation — and a large chunk of the people reading this run automation — your effective programmatic usage just dropped anywhere from 10x to 40x in real terms.

Let me be specific. I have a handful of autonomous setups across my brands. A nightly content reconciliation agent across all four sites. An hourly SEO checker for a client project. A [forked-subagent pattern](https://www.mejba.me/forked-subagents-claude-code-anthropic) I built earlier this year for parallel codebase analysis. Two weeks ago, those workloads ran inside my Max subscription's daily and weekly limits — the marginal cost of each invocation past my flat fee was effectively zero. As of May 13, those same workloads draw from a $200 monthly SDK credit pool at API token rates. The brand reconciliation job alone is on pace to drain that pool inside eleven days.

The community thread on this change is sitting at multiple thousand replies between Reddit and X by the end of the week. The framing inside Anthropic appears to be that programmatic users were arbitraging the subscription — which is technically true, particularly the OpenClaw-style routing setups that let users push headless workloads through a $20 Pro plan. From pure unit economics, Anthropic is correct that those flows were unsustainable.

The problem is not that the split exists. The problem is how it shipped.

It shipped on the same day as the 50% increase, which let the headline read "Claude Code limits go up" while the actual experience for half the userbase was "your existing automation got 10x more expensive." Transparency on what counts against the new bucket was thin for the first 24 hours. The migration path for existing programmatic workloads is still being figured out in real time. And the underlying message — we are compute-constrained, so the agents are the ones who pay — does not square cleanly with the [SpaceX compute partnership narrative](https://www.mejba.me/claude-code-rate-limits-doubled-spacex) Anthropic spent the previous week leaning into.

Here is what the change actually looks like at the line-item level.

### What Changed In The Anthropic Limit Structure

| Surface | Before May 13 | After May 13 |
|---|---|---|
| Interactive Claude Code (terminal) | Pooled with subscription, free under flat fee | Pooled with subscription, free under flat fee, +50% weekly headroom through July 13 |
| Five-hour limits | Pre-May 6 baseline | 2x pre-May 6 baseline, no peak-hour throttling |
| Agent SDK / `claude -p` | Pooled with subscription | Separate metered credit pool, API rates, no rollover |
| GitHub Actions integration | Pooled with subscription | Same separate metered pool |
| Third-party agents (OpenClaw, harnesses) | Restricted in April, then reinstated | Restricted to the metered pool only |
| Effective cost of a nightly automation job | Inside flat fee | API rates against fixed monthly bucket |

The "Fast Mode" piece sits inside that same picture, and it is worth treating separately, because it is genuinely good — and worth understanding before you turn it on.

### My Take On Fast Mode

Fast Mode for Opus 4.7 went generally available this week, and on May 14 it becomes the default fast-mode model. The mechanics: Opus 4.7 with a different API configuration that runs 2.5x faster at a higher per-token cost, with identical model quality and capabilities. You toggle it on with `/fast` inside Claude Code, and it requires v2.1.139 or later. Until today, you had to opt in via the `CLAUDE_CODE_ENABLE_OPUS_4_7_FAST_MODE=1` flag.

I have been running Fast Mode for about ten days on a side branch of one of my brand sites. For interactive work — code reviews, rapid iteration, debugging a failing CI run live — it is the right default. The 2.5x latency drop changes the shape of how you prompt. You stop batching questions into mega-prompts because you can ask three smaller ones and still come out ahead. You start using it in places you would have used a smaller model before.

For long autonomous runs where cost matters more than latency, you turn it off. The token premium is real and it adds up.

The thing nobody is saying about Fast Mode in the context of the SDK change is this: Fast Mode burns through your new programmatic pool faster than baseline Opus 4.7 by a margin that matters. If you run automation that defaults to Fast Mode, your $200 bucket lasts even less than the eleven days I estimated above. Toggle deliberately.

That is Anthropic. Let me move to the quieter lab this week, which is OpenAI.

## OpenAI: GPT-5.6 Is Cooking, And The Codex Super App Is The Story That Will Matter Most

OpenAI was almost suspiciously quiet on the public-facing front this week. No major announcement. No keynote-style drop. But the internal signal is strong, and the developer logs leaked enough that the picture is reasonably clear.

GPT-5.6 has entered internal testing under two checkpoint codenames: **ember-alpha** and **beacon-alpha**. Both surfaced in OpenAI's internal Codex logs, alongside a routing map entry that pointed explicitly at gpt-5.6. The Codex environment appears to already be running test traffic against the new model. Polymarket is pricing a GPT-5.6 release before June 30, 2026 at 89% — and that number has barely moved all week.

Two checkpoints in active testing this early in the cycle is the signal worth tracking. OpenAI has historically used parallel checkpoint runs to evaluate divergent training recipes against each other. Whichever one wins the head-to-head becomes the production model. The previous release pattern from GPT-5.4 through 5.5 followed roughly this same pre-release cadence — internal checkpoints surfacing in developer logs three to five weeks before the public announcement.

If the pattern holds, GPT-5.6 hits public release somewhere in the back half of June. I am penciling in June 17 to June 24 as the most likely window.

There is another thing happening at OpenAI this month that I think is going to age into a bigger story than GPT-5.6 itself: the Codex super app teaser.

### Why The Codex Super App Matters More Than The Next Model

OpenAI shipped major Codex updates in early May with one clear strategic direction — Codex is no longer "the coding product." Codex is becoming OpenAI's everything-app shell. The mechanics that landed this month tell the story.

Codex now has background computer use — the agent can access any app on your machine the way a human operator does, by seeing the screen, clicking with its own cursor, and typing into windows. Multiple Codex agents can run in parallel without interrupting your workflow. The Codex app added a native in-app browser that lets you comment directly on webpages to give the agent precise instructions. Codex can now generate and iterate images using gpt-image-1.5. OpenAI shipped over 90 new plugins this month including GitLab Issues, Microsoft Suite, and Neon by Databricks. Codex automations got expanded so threads preserve context across reuse, and the agent can schedule work for itself and wake up to do it.

That is not the description of a coding tool. That is the description of a generalist agent platform that happens to be very good at coding.

I have been [running Codex against Claude Code on adversarial workflows for months](https://www.mejba.me/codex-plugin-claude-code-adversarial-review). The picture three months ago was that Claude Code held a clear lead on autonomous coding and Codex held an edge on consumer-grade chat. The picture today is that Codex is closing fast on coding while pulling ahead on the broader productivity surface that Anthropic does not even compete in. Sam Altman has been telegraphing the super app vision in public talks since Q1, and the May updates are the first time the product actually moved in that direction at speed.

If GPT-5.6 ships in mid-June with the Codex super app layer wrapped around it, that combination is the most direct shot at the long-running consumer thesis OpenAI has been chasing since GPT-4. Anthropic, by contrast, just made it harder to build agents on top of Claude. The strategic divergence between the two labs is now wider than at any point in the last eighteen months.

Now to the story that I think matters more than any model release this month.

## Figure AI: The Helix-02 8-Hour Shift Is The Robotics Inflection Point

On May 13, Figure AI broadcast an eight-hour livestream of its humanoid robots completing a full warehouse shift with zero human intervention. The livestream is still up. It crossed fourteen million views by Wednesday morning.

The robots, running Figure's Helix-02 unified neural network, were placed on a real package-sorting conveyor belt and tasked with detecting barcodes, picking up packages, and reorienting them so the barcode faces down onto the belt. A task that takes a human worker about three seconds on average. The robots matched that throughput across the eight hours. They self-diagnosed when sensors went out of calibration. They swapped batteries autonomously. They coordinated with each other through visual cues alone — no networked task orchestration layer, just robots looking at each other and routing around bottlenecks.

The conveyor did not stop once.

I have been [tracking the humanoid robotics curve for a year](https://www.mejba.me/ai-tools-agents-reshaping-work), and the gap between Figure's demos at the start of 2025 and this week's livestream is the kind of gap that does not show up in linear projections. The 2025 demos were teleoperated. The early 2026 demos were autonomous but in heavily structured environments with short task horizons. Helix-02 is a single end-to-end neural network handling walking, manipulation, balance, and whole-body coordination from a unified learning system. The eight-hour horizon is not just impressive engineering. It is the threshold where the labor argument starts to bite for real.

The labor argument has lived in slide decks for three years. "Humanoid robots will eventually be cheaper than warehouse workers." Fine. The argument moves from slide deck to spreadsheet when one specific number crosses one specific threshold: cost per hour of autonomous operation, normalized against a human shift, including amortized capex, energy, and maintenance. Until this week, every public estimate of that number sat above $30 per hour for the leading humanoid platforms — well over a fully-loaded warehouse worker in most markets. Figure has not published its number. But the company's CEO has been signaling that the 2026 cost target is sub-$15 per hour and that internal pilots are already approaching it.

If that holds, and if the Helix-02 reliability curve continues at the current trajectory through Q3, the conversation about humanoid labor exits theory in late 2026. Warehouse and logistics customers are already in pilot. The math starts working for them inside this calendar year for a non-trivial fraction of shift types.

I am not going to write the easy headline here. The "robots are taking the jobs" framing is too coarse to be useful. The actual short-term story is that humanoid platforms are going to absorb the night shifts, the dangerous shifts, and the high-turnover shifts first — the work humans do not want, in environments where the cost of a humanoid hour is competitive against the cost of a human hour plus the cost of replacing high-attrition staff every quarter. That is a different conversation than the dystopian one, and it is the conversation that is actually happening inside operations teams right now.

I have my own running take on what this means for [how solo operators and agencies will compete in 2026](https://www.mejba.me/ai-first-company-2026-solo-operator). It is not pretty. It is also not the apocalypse the X timeline wants it to be. The honest middle is that the labor cost curve is bending in a real way for the first time in eighteen months, and the bend is going to compound.

That is the Figure story. Let me close out with the two threads that did not get top billing this week but that should be on your radar.

## The Quieter Threads: Jules, Hermes, And The Open-Source Curve

Two stories underneath the headlines that are worth tracking.

**Google's Jules coding agent got an asynchronous self-healing update.** Jules now closes the loop on CI failures — if the build breaks on the pull request Jules just opened, the agent receives the error, reasons about the cause, applies a fix, and re-pushes the commit without developer intervention. Google also rolled out a "critic" feature that subjects every proposed change to adversarial review at generation time. When the critic catches code that needs to be redone, the change goes back into Jules for rework before the PR ever lands in front of you. I have been testing Jules against my own [auto-research Claude Code workflow](https://www.mejba.me/auto-research-claude-code-strategy) on smaller repos and the asynchronous model — fire and forget, come back to a finished PR — is a different rhythm than the interactive Claude Code pattern. Both are useful. Neither replaces the other yet.

**Hermes Agent crossed 140,000 GitHub stars in under three months.** OpenRouter is reporting Hermes as the most-used agent on its routing layer. Nous Research built Hermes as a provider- and model-agnostic agent optimized for always-on local use — NVIDIA RTX PCs, RTX PRO workstations, and DGX Spark are the recommended hardware. Pair Hermes with Alibaba's new Qwen 3.6 series and the open-source side of the agent stack is suddenly very interesting. Qwen 3.6 35B runs on roughly 20GB of memory while surpassing 120B-parameter models that require 70GB+. Qwen 3.6 27B is dense and matches the accuracy of 400B-parameter models like the previous Qwen 3.5 397B at roughly one-sixteenth the size. The efficiency gain is the part that should make centralized labs nervous. If you can run an agent at near-frontier capability on a workstation under your desk, the unit economics of the cloud subscription model start to look different.

Both threads are tied to the same underlying current as the rest of this week. Compute is constrained. The closed labs are tightening their belts. The open-source side is finding ways to do more with less. That gap is going to be one of the load-bearing dynamics through the second half of 2026.

## My Pre-I/O Calibration: What To Bet On This Week And What To Wait Out

Here is my honest read on what is worth doing in the next seven days, based on what I have tested and what the signal-to-noise ratio looks like from my desk.

**Bet on the Flash upgrade, not the Pro hype.** When Gemini 3.2 Flash launches publicly — almost certainly at the keynote on May 19 or 20 — it is genuinely worth integrating into pipelines where you currently use Gemini 3.1 Pro for cost reasons. The undercut on input pricing combined with the SVG and UI generation strength is real. Don't wait for the "Pro" or "Ultra" variant to deliver discontinuous gains. It probably won't.

**Audit your Anthropic programmatic usage by Sunday.** If you run any automation through Claude Code, the Agent SDK, GitHub Actions, or third-party harnesses, your effective cost just changed. Pull your usage from the last thirty days. Estimate what the same workload will cost against the new metered pool. Either accept the new bill, refactor to push fewer tokens per run, or migrate the non-critical jobs to a different model. I am personally moving two of my three automation jobs to a Qwen 3.6 + Hermes setup on a local box and keeping the most accuracy-sensitive one on Claude. Your mix will be different.

**Hold off on Codex-as-super-app commitments until GPT-5.6 lands.** The Codex updates are interesting, the background computer use is impressive in demos, but the underlying model is still GPT-5.5. Waiting three to five weeks for the GPT-5.6 release before reorganizing a workflow around Codex is the better trade. The super app vision is real and worth taking seriously — but the moment to commit is the moment the new model lands, not the moment the wrapping ships.

**Take Figure seriously even if you don't work in warehousing.** The eight-hour autonomous shift threshold is the kind of milestone that changes the planning assumptions for adjacent industries — logistics, manufacturing, food service, construction. If any of your business model depends on labor cost stability in those sectors past 2027, the eight-hour livestream is the data point you should be sitting with. The robotics curve is not going to wait.

**Watch the open-source side seriously.** Hermes plus Qwen 3.6 on local hardware is the first time in eighteen months that the open stack has felt genuinely competitive for production agent workloads. The closed labs tightening their subscriptions is exactly the kind of forcing function that pushes serious builders to evaluate the alternatives. I expect the Hermes star count to clear 250K by end of Q3 and a meaningful chunk of independent dev shops to migrate at least their non-critical agent workloads off Claude and OpenAI by end of year.

The Google I/O keynote opens at 1 PM Pacific on May 19. I will be writing live notes as it runs and will post a follow-up calibration once the dust settles.

Here is the timeline I am holding in my head for the next six weeks. This is the version I would have written down for myself if I had read this article on Monday.

### The Pre-I/O To Mid-June Timeline From My Desk

| Date | Event | What I'm Doing |
|---|---|---|
| May 14 | Opus 4.7 becomes default Fast Mode model | Reviewing which workloads benefit from speed vs cost |
| May 14-18 | Final leak window before I/O — expect more Gemini 3.2 / Omni surfacing | Logging anything that surfaces in AI Studio and LMArena |
| May 19-20 | Google I/O keynote, Gemini 3.2 family launch (high probability), Omni tease (moderate probability) | Live notes during keynote, follow-up calibration post within 48h |
| May 19-26 | Public testing window for whatever Google ships | Same prompt suite against new variants, comparison against Opus 4.7 and GPT-5.5 |
| Mid-late May | Continued Anthropic SDK billing fallout and possible policy adjustments | Watching for credit pool changes or migration tooling |
| Early June | Possible GPT-5.6 leaks and early-access invites | Standing up a test harness ready for the moment a checkpoint goes broadly available |
| June 17-24 | GPT-5.6 public release window (most likely band) | Full cross-model comparison, Codex super app reassessment |
| Through July 13 | Anthropic 50% weekly limit increase active | Maximizing interactive use of the temporary headroom |

That is the picture six days before Google I/O. The compute crunch is the real story underneath all of it. The labs that figure out how to build infrastructure that scales faster than their distribution will lead the next cycle. The labs that price-discriminate against their existing customers to manage scarcity will hold the line in the short term and lose ground in the medium term. And the open-source side will keep grinding the cost-per-capability curve down until the centralized model has to respond.

I will see you on the other side of the keynote. The cup, this time, will be hot.

## Frequently Asked Questions

### When is Google I/O 2026 and what is expected to launch?
Google I/O 2026 runs May 19-20 with the keynote at 1 PM Pacific on May 19. Expect a Gemini 3.2 family launch — Flash, Pro, and a Deep Think or Ultra variant — based on leak evidence from iOS app builds and AI Studio. Polymarket prices Gemini 3.2 launching before May 31 at 83%. A Gemini Omni video model tease with limited preview access is also likely.

### What changed with Anthropic's Claude Code limits on May 13, 2026?
Anthropic raised weekly interactive limits by 50% through July 13 for Pro, Max, Team, and seat-based Enterprise plans, but moved programmatic usage — Agent SDK, GitHub Actions, `claude -p`, and third-party agents — into a separate metered credit pool billed at API rates. Interactive Claude Code users gain headroom. Automation users see effective costs rise meaningfully.

### When will GPT-5.6 be released?
GPT-5.6 is in internal testing with two checkpoint codenames, ember-alpha and beacon-alpha, surfacing in OpenAI Codex logs. Polymarket prices a release before June 30, 2026 at 89%. The most likely public release window based on prior pre-release cadence is June 17 through June 24.

### What is Figure AI's Helix-02 8-hour shift demonstration?
On May 13, Figure AI livestreamed humanoid robots completing a full eight-hour warehouse shift with zero human intervention, sorting barcoded packages on a real conveyor belt using the Helix-02 unified neural network for walking, manipulation, balance, and coordination. The livestream crossed fourteen million views and marks a credibility threshold for fully autonomous humanoid labor.

### How does Claude Code Fast Mode for Opus 4.7 work?
Fast Mode runs Opus 4.7 at 2.5x baseline speed with identical capability at a higher per-token cost. As of May 14, 2026, Opus 4.7 is the default Fast Mode model. Toggle with `/fast` in Claude Code v2.1.139 or later. Best for interactive iteration and debugging; costs add up faster on long autonomous runs.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
