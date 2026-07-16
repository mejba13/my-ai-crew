**BRAND:** mejba.me
**TITLE:** GPT-5.6 Leak, Deepseek Merger, Google's Pentagon Deal
**META TITLE:** GPT-5.6 Leak + Deepseek Merger + Google Pentagon AI
**SLUG:** gpt-5-6-leak-deepseek-google-pentagon
**PRIMARY KEYWORD:** GPT-5.6 leak
**META DESCRIPTION:** Inside the GPT-5.6 leak in Codex logs, the Deepseek-Moonshot merger rumor, and Google's classified Pentagon AI deal. Here's what each one actually means.
**TAGS:** AI News, OpenAI, Deepseek, AI Ethics, Industry Analysis

---

I almost wrote this post three different times.

The first draft was just about the GPT-5.6 entry that surfaced in Codex session logs the same week GPT-5.5 went live — the kind of leak that gets engineering Twitter spinning for 48 hours and then disappears. The second draft was about the Deepseek and Moonshot merger rumor, because if it's real, it reshapes the open-source AI map for the rest of 2026. The third was about Google's classified Pentagon deal — the one that 600 of its own employees are publicly asking the company to walk away from.

Then I noticed something. All three stories broke inside the same ten-day window in late April. They feel unrelated. They aren't. They're three angles on the same shift: AI capability is now compounding faster than the institutions around it can absorb. The release cadence is monthly. The capital is consolidating across borders. The defense pipeline is fully open. And we're all pretending this is normal because the headlines move too fast to sit with.

I've spent the last week reading every primary source I could find on these three stories — the leaked Codex logs, the Bloomberg IPO filings, the Google open letter, the Pentagon CDAO contract documents. What I want to do here is walk through what's actually verified, what's still rumor, and what the three of them mean when you put them on the same table. Because if you're building anything on top of these models — and most of us reading this are — you need a clear-eyed view of where the rails are heading.

Let's start with the leak that's been driving the loudest noise.

## What Actually Happened With the GPT-5.6 Leak

Here's the short version. On April 24, 2026, OpenAI officially released GPT-5.5 to Plus, Pro, Business, and Enterprise users — the model the community had been calling "Spud" after months of leaked benchmarks ([source: OpenAI](https://openai.com/index/introducing-gpt-5-5/)). Within hours of the launch, a developer noticed something odd in their Codex session logs. Most calls were routed to GPT-5.5, as expected. But one log entry showed the model name as `gpt-5.6`. Not 5.5. Not a fine-tune. A version number that didn't exist publicly ([source: TechCity Authority](https://www.techcityauthority.com/2026/04/gpt-5-6-leak-heres-what-you-should-know.html)).

The screenshot got posted. The thread blew up. By the time anyone went back to verify, the entry was gone from the session files entirely.

I want to be careful here, because this is exactly the kind of story where the noise outruns the signal in about ninety minutes flat.

What we know for sure: a Codex log entry referencing GPT-5.6 was real. Multiple developers saw it. The screenshot is consistent with the actual Codex session file format. OpenAI has not commented on it.

What we don't know: whether GPT-5.6 is a real model, an internal evaluation tag, a canary deployment, a misnamed routing test, or a string in a config file that got accidentally exposed. There's a meaningful difference between "OpenAI is testing GPT-5.6 against real traffic" and "someone at OpenAI typed `gpt-5.6` into a Python dictionary and a request hit it once." Both produce the same screenshot.

**The most honest read:** OpenAI is almost certainly running canary or eval traffic on something newer than GPT-5.5. That's how every model lab does it. A controlled slice of requests gets routed to an experiment, results get collected, the entry gets cleaned up. The fact that the log surfaced at all was a server-side slip — the same kind of slip that exposed a 90-minute window of GPT-5.5 traffic before the official launch ([source: Startup Fortune](https://startupfortune.com/a-server-side-slip-at-openai-gave-developers-a-90-minute-glimpse-of-gpt-55-and-the-internet-has-not-stopped-talking-about-it/)).

So is GPT-5.6 coming this month? Probably not as a public release. Is something coming? Almost certainly. The evidence isn't the leak itself — it's the cadence around it.

### The cadence is the real story

Look at this timeline. GPT-5.0 shipped in early 2026. GPT-5.2 followed roughly three months later. GPT-5.3 hit shortly after. GPT-5.4 dropped while teams were still benchmarking 5.3. GPT-5.5 went live April 24. And now GPT-5.6 references are showing up in production logs five days after the 5.5 launch.

I covered the GPT-5.4 release window in [my GPT-5.4 industry shakeup breakdown](https://www.mejba.me/gpt-5-4-ai-industry-shakeup), and at the time I wrote that the major-version-to-point-release gap was compressing. I didn't expect it to compress this fast. We've gone from quarterly to almost monthly point releases inside one calendar year.

Why this matters if you ship code on top of OpenAI: model lock-in just got cheaper. A workflow tuned to GPT-5.5 today might be measurably outperformed by a 5.6 model in 30 days. The "test once and deploy" pattern is dead. The pattern that works now is more like a CI rig that re-runs your eval suite every time a new model variant ships.

I built one for myself last month — a small Python script that pings my prompt suite against the latest API model on a weekly cron and posts a diff to Slack. It's not glamorous. But the first time GPT-5.5 quietly improved on a structured-output task that GPT-5.4 was failing 30% of the time, I shipped the upgrade in an hour instead of finding out three weeks later. If you're building anything serious on these models, you need this pipeline. If you're not building it, you're going to keep getting blindsided.

### The Spud-vs-Mythos angle

Here's the part that connects the leak to a bigger story. The internal codename "Spud" — which the community first thought was a wholly new frontier model — turned out to be GPT-5.5. The leaked OpenAI memo from late March explicitly described Spud as positioned to counter Anthropic's Mythos model ([source: AIBase](https://news.aibase.com/news/27108)).

Mythos is the one to actually pay attention to. It's the Anthropic frontier model that leaked through a misconfigured S3 bucket in late March, with internal docs describing it as a "step change" in capability and scoring "dramatically higher" than Opus 4.6 on coding, reasoning, and cybersecurity benchmarks ([source: Fortune](https://fortune.com/2026/03/26/anthropic-says-testing-mythos-powerful-new-ai-model-after-data-leak-reveals-its-existence-step-change-in-capabilities/)). I broke down what we knew at the time in [the Claude Mythos leak analysis](https://www.mejba.me/anthropic-claude-mythos-leak), and I wrote then that the GPT-5.5 timing felt deliberate. Now we have the receipts: OpenAI shipped GPT-5.5 specifically as a counter-positioned response to Mythos, and the GPT-5.6 reference in Codex logs suggests they have something else queued up behind it.

This isn't two companies competing on a roadmap. This is two companies running a gun-to-gun release cycle, where each public ship is a tactical response to the other's leak. That's a different industry than the one we had even six months ago.

But here's where things get more interesting — because while OpenAI and Anthropic are trading leaks in California, the real pattern shift in 2026 is happening in Beijing.

## The Deepseek and Moonshot Merger Rumor: What's Real

The story going around in late April was that Deepseek and Moonshot AI — the two most aggressive open-source labs in China — were preparing to merge. The angle floating in the Substacks and the Twitter threads was that the merger would consolidate them into a single national champion ahead of Moonshot's rumored Hong Kong IPO, with a combined valuation north of $20B.

I dug into this one for almost two days because it's the story I most wanted to be true. A Deepseek-Moonshot merger would create the most capable open-weights lab on the planet, with both Kimi's massive context window heritage and Deepseek's Multi-head Latent Attention architecture under one roof. It would be the open-source AI moment of the decade.

It's not happening. At least not the way the rumor frames it.

Here's what's actually verifiable. Moonshot AI is in real, documented IPO talks. Bloomberg confirmed in late March that Moonshot has been in discussions with China International Capital Corp. and Goldman Sachs about a Hong Kong listing ([source: Bloomberg](https://www.bloomberg.com/news/articles/2026-03-26/moonshot-is-said-to-consider-hong-kong-ipo-as-ai-stocks-flourish)). The Wall Street Journal reported the company was raising several hundred million dollars at roughly a $4B valuation, targeting an H2 2026 IPO ([source: Techmeme summary](https://www.techmeme.com/251121/p33)). Deepseek's own valuation has been pegged around $20B in recent rounds ([source: BigGo Finance](https://finance.biggo.com/news/UdKivp0B5edQG9E4tJNW)).

But the actual relationship between the two labs isn't a merger. It's something more interesting and more strategically significant: deep technical synergy without legal consolidation.

Deepseek V4, which finally shipped on April 23 after months of delays, adopted Moonshot's Muon optimizer wholesale. Earlier, Kimi K2 had integrated Deepseek's pioneering Multi-head Latent Attention mechanism. The two labs are sharing core research and citing each other's papers in production model cards ([source: BigGo Finance](https://finance.biggo.com/news/snsaxJ0B5edQG9E4fpKS)).

This is what Chinese industrial AI strategy actually looks like in 2026. Not a top-down merger ordered by Beijing. It's coordinated technical interoperability between supposedly independent companies, with shared optimizers, shared attention architectures, and increasingly shared compute pipelines — all heading toward the same domestic chip stack.

### The Huawei chip pivot is the real bombshell

Here's the part most of the merger coverage buried. The reason Deepseek V4 was delayed past Chinese New Year wasn't the model itself. It was hardware. Bloomberg confirmed in late April, citing a CCTV-affiliated account, that the V4 delay was driven by Deepseek's transition from Nvidia to Huawei's Ascend chips ([source: Bloomberg](https://www.bloomberg.com/news/articles/2026-04-26/deepseek-v4-delay-shows-shift-to-china-chips-cctv-account-says)). Reuters separately reported that V4 launched running on the latest Ascend hardware.

Read that again. The most cost-efficient open-weights lab in the world just successfully migrated training and inference off Nvidia. That fact will outlast every merger rumor. I covered the model itself in [my Deepseek V4 Pro review](https://www.mejba.me/deepseek-v4-pro-open-source-ai-review), but the chip-stack story is the one that should be in every AI infrastructure team's planning deck for the next twelve months.

The multimodal version, by the way, isn't shipping with V4. The official line, confirmed by ChinaTalk's primary-source reporting, is that compute and capital constraints pushed multimodal generation training out of the V4 release scope ([source: ChinaTalk](https://www.chinatalk.media/p/deepseek-v4)). V4 stays a language model. Multimodal Deepseek is queued, not delayed indefinitely — but it's not what shipped on April 23.

So if you've been waiting for one Chinese mega-lab to emerge and challenge OpenAI, that's not the move. The move is two labs running coordinated R&D on a domestic chip stack, with one of them about to go public to capitalize the next round of compute. And every Western AI infrastructure decision should account for that.

The third story is where it stops being about AI capability and starts being about who the capability is for.

## Google's Pentagon Deal and the 600 Engineers Who Said No

On April 28, 2026, more than 580 Google employees — including senior DeepMind researchers, more than 20 directors and VPs, and roughly two-thirds named publicly — signed an open letter urging CEO Sundar Pichai to refuse a classified Pentagon AI deal ([source: TheNextWeb](https://thenextweb.com/news/google-classified-ai-pentagon-drone-swarm-exit)). The letter was direct: "We believe that Google should not be in the business of war."

Google moved forward with the deal anyway ([source: Gizmodo](https://gizmodo.com/google-signs-pentagon-ai-deal-despite-employee-backlash-2000751724)).

I want to walk through this one carefully, because the headline version misses what's actually new.

### What the deal actually does

The Google-Pentagon agreement is part of a broader $800M frontier-AI procurement run by the Pentagon's Chief Digital and Artificial Intelligence Office (CDAO). In June 2025, OpenAI received the first $200M contract. In July 2025, Anthropic, Google, and xAI each received their own $200M two-year ceiling contracts ([source: Breaking Defense](https://breakingdefense.com/2025/07/anthropic-google-and-xai-win-200m-each-from-pentagon-ai-chief-for-agentic-ai/)). The contracts cover prototype agentic AI workflows for "national security missions" — explicit language from the CDAO procurement documents ([source: DefenseScoop](https://defensescoop.com/2025/07/14/pentagon-ai-contracts-musk-xai-google-openai-anthropic-cdao/)).

In March 2026, Google rolled Gemini AI agents out to the Pentagon's three-million-strong workforce on unclassified systems. In April 2026, that access extended to classified networks. The terms allow Pentagon use of Gemini for "any lawful government purpose," which — and this is the language the open letter calls out — explicitly includes mission planning and weapons targeting support, with restrictions only on domestic mass surveillance and fully autonomous weapons without human oversight ([source: NBC News](https://www.nbcnews.com/tech/tech-news/pentagon-inks-deal-google-ai-services-rcna342661)).

### The Project Maven echo nobody is talking about correctly

Every news outlet covered the Project Maven parallel — in 2018, around 4,000 Google employees signed a petition over AI analysis of drone video feeds, and Google let the contract lapse. The 2026 letter is being framed as "Maven 2.0."

It's not. It's something different and more significant.

In February 2025, Google quietly removed the passage from its public AI Principles that excluded weapons and surveillance technology ([source: Computing.co.uk](https://www.computing.co.uk/news/2026/government/google-signs-ai-deal-pentagon)). The 2018 commitment that "Google will not pursue AI for weapons" is gone from the policy. The 2026 letter isn't asking Google to honor its principles. The principles already changed. The letter is asking Google to refuse a contract its own policy now allows.

That's the real shift. The 2018 protest worked because the policy and the contract were misaligned, and Google chose the policy. The 2026 protest is happening after Google preemptively re-aligned the policy with the kind of contract it now wants to sign. The institutional answer was already locked in before the engineers were asked.

I'm not going to pretend I have a clean take on the ethics here. I do have a clean take on the engineering reality: every major US frontier model is now defense-procured. OpenAI, Anthropic, Google, xAI — all four labs have $200M ceiling contracts with the Pentagon's CDAO running through 2027. The "I won't build for defense" exit doesn't exist anymore for engineers working at the foundation-model layer. The decision moved up the org chart and out of the principles document.

### What this means for what you build on top of these models

If you're shipping a product on top of Gemini, Claude, GPT, or Grok, you're now downstream of a defense-grade procurement relationship. That's a legal and reputational reality, not a slogan. A few specific implications worth thinking about:

**Data residency and tenant isolation matter more.** Google's classified deployment runs on dedicated infrastructure with separate filtering rules. Your commercial Gemini API does not. Make sure you understand which tier you're on and what the actual data flow looks like.

**Content policy is going to get noisier.** When Pentagon use cases require relaxed safety filters for specific deployments, expect commercial-tier filter behavior to drift — sometimes loosening, sometimes tightening — as the labs balance multiple customer profiles. Your prompt engineering will need version-pinning more than it did a year ago.

**Pricing pressure is real.** $800M in two-year contracts gives the four major US labs guaranteed revenue floors, which changes their commercial pricing strategy. I expect API price compression on lower-tier models to accelerate through 2026 as defense revenue cushions the consumer-facing business.

The deeper question — whether building any consumer product on a defense-procured foundation model is a thing you want to do — that's a personal call. I have my own line. I'm not going to tell you where to draw yours. But pretending the line doesn't exist is the thing I won't do here.

## What I'm Actually Doing About All Three of These

Okay, three stories, three different shapes of risk. Here's what I'm changing in my own workflow this week, concretely:

**1. I'm version-pinning every model call in production.** Every API call now specifies an exact model version, not a `gpt-latest` or `claude-default` alias. The cadence is too fast to let routing drift silently. This was already best practice. It's now non-negotiable.

**2. I'm running a weekly eval suite against new model variants.** Same script I mentioned earlier. If GPT-5.6 ships next week or in three months, I want to know within 7 days whether my workloads improve, regress, or hold steady. I shared the basic pattern in [my AI agent cost optimization guide](https://www.mejba.me/ai-agent-cost-optimization-guide), and I've extended it to include capability deltas, not just cost.

**3. I'm building infrastructure that can route to non-US foundation models.** Not because I expect to flip the switch tomorrow. Because the ability to route Deepseek V4 or Kimi K2.6 for specific workloads — especially open-weights workloads I can self-host — is the only real hedge against any single lab's pricing or policy change. If Deepseek and Moonshot do consolidate operationally, even without a formal merger, the open-source side of the stack will get more capable, not less.

**4. I'm reading the procurement docs.** The CDAO contract language is publicly searchable. The Anthropic DOD agreement page is published ([source: Anthropic](https://www.anthropic.com/news/anthropic-and-the-department-of-defense-to-advance-responsible-ai-in-defense-operations)). If you're going to build on top of these models commercially, knowing what the labs have actually committed to deliver to government customers is part of the due diligence now. Read them.

**5. I'm taking the GPT-5.6 leak seriously enough to plan, not seriously enough to predict.** The cadence tells me OpenAI will ship something between now and mid-summer. The leak doesn't tell me what. Building plans around a screenshot is how you waste two weeks. Building plans around an accelerating cadence is how you stay ahead of the next ship cycle.

The pattern I keep coming back to: capability is compounding, capital is consolidating, and the policy rails are bending toward defense-grade procurement on every major US lab. The job of anyone building real products on this stack in 2026 isn't to predict the next model release. It's to build a workflow that absorbs whatever ships, from whoever ships it, with the eyes-open knowledge of where the rails are now.

I'll be back when GPT-5.6 actually drops — or whatever they end up calling it. Until then: pin your versions, run your evals, and read the contracts.

## Frequently Asked Questions

### Is GPT-5.6 actually being released soon?
Probably not as a public release in May 2026, based on the available evidence. The Codex log entry referencing GPT-5.6 is most likely canary or evaluation traffic, not a production model. OpenAI has not confirmed the model exists. The cadence suggests something is coming within months — but the leaked log entry alone is not a release signal.

### Did Deepseek and Moonshot actually merge?
No formal merger has been confirmed as of late April 2026. What's verified is deep technical collaboration — Deepseek V4 adopted Moonshot's Muon optimizer, and Kimi K2 previously integrated Deepseek's MLA architecture. Moonshot is in real Hong Kong IPO talks per Bloomberg, but the two labs remain legally independent.

### What does Google's Pentagon deal allow specifically?
The agreement permits Pentagon use of Gemini AI for "any lawful government purpose," which explicitly includes mission planning and weapons targeting support. Restrictions cover domestic mass surveillance and fully autonomous weapons without human oversight. The deal extends to classified networks as of April 2026.

### Why did Deepseek V4 ship without multimodal capability?
Per ChinaTalk's primary-source reporting, multimodal generation training was deprioritized due to compute and capital constraints. V4 remains a language model. Multimodal Deepseek is on the roadmap but was not part of the April 23 release.

### How is the 2026 Google Pentagon deal different from Project Maven in 2018?
In 2018, Google's AI Principles explicitly excluded weapons and surveillance, and the Maven contract was allowed to lapse after 4,000 employees protested. In February 2025, Google removed that exclusion language from its principles. The 2026 deal is policy-aligned from Google's side before the engineer protest happened — a structurally different situation than 2018.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
