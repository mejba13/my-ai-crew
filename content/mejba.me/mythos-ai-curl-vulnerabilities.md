**BRAND:** mejba.me
**TITLE:** Anthropic's Mythos Hit curl. The Hype Didn't Survive.
**META TITLE:** Mythos AI curl Vulnerabilities: The Hype vs Reality
**SLUG:** mythos-ai-curl-vulnerabilities
**PRIMARY KEYWORD:** Mythos AI curl vulnerabilities
**META DESCRIPTION:** Anthropic's Mythos reported 5 curl vulnerabilities. Only 1 was real. Here's what Daniel Stenberg's verdict means for AI-assisted security in 2026.
**TAGS:** AI Security, Anthropic Mythos, Vulnerability Detection, Open Source Security, News Analysis

---

I was halfway through my second coffee on May 11 when Daniel Stenberg's blog post landed in my feed. The title was "Mythos finds a curl vulnerability." That sounded straightforward enough. I clicked, started reading, and within two paragraphs realised the post was doing something a lot more interesting than its title suggested.

It was a polite, methodical, ice-cold takedown of one of the most aggressively marketed AI announcements of 2026.

For context, Anthropic spent April building one of the loudest security narratives of the year. Project Glasswing. Claude Mythos Preview. "The zero days are numbered." "Defenders finally have a chance to win decisively." A $100M commitment in model credits. Partner lists that read like a who's-who of critical software. I covered the launch and the underlying debate in my [AI zero-day discovery breakdown](https://www.mejba.me/ai-zero-day-discovery-debate) and the [Claude Mythos cybersecurity impact piece](https://www.mejba.me/claude-mythos-cybersecurity-impact). The framing was unambiguous: Anthropic had built something so capable at finding software flaws that it couldn't be released to the public.

Then they pointed it at curl. The most-audited C codebase on the open web. Maintained by a guy who's spent the last eighteen months publicly destroying AI security reports for being slop.

The result? Mythos delivered five "confirmed security vulnerabilities." Stenberg's team confirmed one. One low-severity bug. Patched in curl 8.21.0, due late June.

That's the data point the press release didn't include. And it's the one worth sitting with — because if you build software for a living, run agents in production, or care about where AI-assisted security actually is right now (not where the slide deck says it is), the curl episode just gave you a clean, well-instrumented reading on the truth.

Let me walk you through what actually happened, what it means, and why I think this single low-severity CVE matters more than the press release that surrounded it.

## What Anthropic Actually Promised With Mythos

Before we get to the curl data, the marketing claims matter — because the gap between them and the result is the entire story.

Anthropic announced Project Glasswing on April 7, 2026. The headline was Claude Mythos Preview, described as "a general-purpose, unreleased frontier model" that had reached "a level of coding capability where they can surpass all but the most skilled humans at finding and exploiting software vulnerabilities." That's Anthropic's wording, not mine.

The supporting evidence was striking. Mythos had reportedly identified thousands of high-severity vulnerabilities across major operating systems and web browsers. The single demo case that got the most coverage: Mythos autonomously found and exploited a 17-year-old remote code execution flaw in FreeBSD that gave root access to any machine running NFS — triaged as CVE-2026-4747. Anthropic also claimed an 83.1% success rate on CyberGym, a benchmark that measures autonomous discovery of real CVEs in real codebases. The previous best model sat at 66.6%.

So far, so impressive. Then came the marketing claims that made every working security researcher I follow squint at their screen.

"The zero days are numbered."

"Defenders finally have a chance to win decisively."

That's the framing Anthropic chose. Not "this is a useful new tool." Not "AI-assisted code analysis just got meaningfully better." A categorical shift in cyber offense and defense. A new era. A weapon so powerful Anthropic refused to release it to the general public — instead distributing access via Project Glasswing to a small group of partners, with the Linux Foundation as a conduit for some open-source maintainers.

The strategic positioning was clean. Capability so dangerous it can't be open. Capability so necessary it must be deployed. Trust us with the controls.

I've been around long enough to recognise when a company is doing a real safety move versus when they're doing a positioning move. Both can be true at the same time. But the test of which dominates is always the same: what happens when the capability meets a serious benchmark in the wild, in front of someone who can't be flattered?

That test arrived on May 6, 2026. The benchmark was curl. The someone was Daniel Stenberg.

## Why Curl Is the Perfect Test (And the Worst One for the Marketing)

If you wanted to set Mythos up to fail, you'd point it at curl. If you wanted to set it up to succeed honestly, you'd also point it at curl. Same answer either way — because curl is the cleanest possible test environment for an AI vulnerability scanner, and that cleanliness cuts both ways.

Here's why.

Curl is roughly **178,000 lines of C** maintained by a community of **573 contributors** over more than two decades. It runs on over 110 operating systems and 28 CPU architectures. It's installed on more than 20 billion devices — phones, tablets, cars, TVs, game consoles, servers, embedded systems you don't even know exist. If your software talks to the internet, curl is probably in your stack somewhere.

That alone makes it a high-value target. But the part that matters for this conversation is the security posture. Curl has published **188 CVEs** over its lifetime, with an expected ~50 new vulnerabilities to be disclosed in 2026 alone. That's not a sign the codebase is sloppy. It's a sign the codebase is _examined_. Every CVE represents a vulnerability that was found and fixed before it was exploited, which is exactly the cycle you want from a security-critical project.

The defensive infrastructure inside curl is, by any reasonable standard, world-class. Capped dynamic buffers. Explicit max-value enforcement on numeric parsing. Overflow guards. Format-string enforcement that systematically kills entire bug classes. Continuous fuzzing. Static analysis. Automated regression coverage. And — critically for this story — an extensive history of AI-assisted security analysis from earlier tools.

Stenberg himself has been remarkably transparent about this. In his April 22, 2026 blog post "High-Quality Chaos," he noted that AI-assisted reports had finally crossed from being mostly slop to being meaningfully useful. He named the tools that had been contributing real signal: AISLE, Zeropath, and OpenAI Codex Security. Between them, those earlier-generation AI tools had triggered **two to three hundred bugfixes** merged into curl across the previous 8-10 months.

Read that sentence again. Before Mythos ever scanned curl, prior-generation AI tools had already pushed hundreds of fixes into the codebase. The easy bugs — the kind that show up with basic pattern matching, the kind that fuzzers find with a few thousand iterations, the kind that any "AI security scanner" can catch in a demo — were already gone. What remained was the hard layer: real bugs hidden in subtle code paths, deep edge cases, multi-step preconditions.

That's exactly the surface where a categorically-better model should outperform. If Mythos really represents a step change in vulnerability research — the kind that justifies "the zero days are numbered" framing — curl is precisely where you'd expect to see it prove it.

So what did the test produce?

## The Actual Mythos Report: Five Reports, One Real Bug

Stenberg's blog post walks through the report he received. Worth noting: Stenberg never got direct access to Mythos. Anthropic had promised him access via Project Glasswing through the Linux Foundation. That access never materialised. Instead, someone else with Mythos credentials ran it against the curl repository and emailed Stenberg the output.

The report contained five findings, each labelled by Mythos as a "confirmed security vulnerability."

Stenberg's seven-person security team reviewed them all. Here's the breakdown of what survived contact with people who actually know the codebase:

| Finding | Mythos verdict          | Curl team verdict                          |
| ------- | ----------------------- | ------------------------------------------ |
| Issue 1 | Confirmed vulnerability | Low-severity vulnerability — CVE in 8.21.0 |
| Issue 2 | Confirmed vulnerability | False positive — documented API behaviour  |
| Issue 3 | Confirmed vulnerability | False positive — documented API behaviour  |
| Issue 4 | Confirmed vulnerability | False positive — documented API behaviour  |
| Issue 5 | Confirmed vulnerability | Bug, but not a security issue              |

One out of five. A 20% true-positive rate on the most important label Mythos applied. And the one that survived is being patched as a low-severity CVE in curl 8.21.0, scheduled for late June.

Let me be precise about what "low severity" means in curl's CVSS framework, because the word can land softer than it should. Low severity at curl scale still means a real bug, a real disclosure, a real patch cycle, and a real coordinated update across billions of devices. It is not nothing. It is also not the kind of finding that justifies "defenders finally have a chance to win decisively" rhetoric.

The auxiliary results are slightly more interesting. Beyond the five "security vulnerabilities," Mythos also flagged roughly **20 minor bugs** in the codebase. Most of these held up under review. They weren't security issues, but they were real bugs — quality-of-code findings that the curl team has since started working through. That's genuinely useful output. It's also exactly what a competent code-review LLM has been able to produce for at least a year, and what tools like AISLE and Zeropath have already been delivering at scale.

Stenberg's conclusion, in his words: "My personal conclusion can however not end up with anything else than that the big hype around this model so far was primarily marketing." And: "I see no evidence that this setup finds issues to any particular higher or more advanced degree than the other tools have done before Mythos."

That's not a casual swipe. That's a multi-decade open-source maintainer who runs one of the most stressed security pipelines on the internet, calmly stating that the highest-marketed AI security model of 2026 did not outperform the tools that were already available.

If you're building with AI right now — agents, automations, security tooling, anything — that data point is worth more than the entire press release that surrounded it. Let me explain why.

## What This Tells Us About Where AI Security Actually Is

I want to walk through what I think the curl episode actually proves, because it's not the simple "Mythos is a flop" reading that some commentators are running with.

Three things are true at the same time. None of them are comfortable for either the maximalist or minimalist position.

**First: Mythos is real, working, and meaningfully capable.** A model that scans a 178,000-line C codebase maintained by 573 contributors and surfaces one real CVE plus 20 minor bugs in a single pass is not nothing. That's a non-trivial result against a codebase that has been hammered by every fuzzer, static analyser, and AI security tool currently in production. The signal is real. The output is useful.

**Second: Mythos is not the categorical leap the marketing claimed.** The 20% true-positive rate on its highest-confidence label, combined with the fact that prior-generation AI tools were already pushing hundreds of bugfixes through curl, makes "the zero days are numbered" framing land as marketing copy rather than technical reality. Mythos appears to be a moderate improvement over already-deployed tools, not a paradigm shift.

**Third: the gap between (1) and (2) is the most important fact in the entire 2026 AI security narrative.** It's the gap where every overclaim, every restricted-access policy, every fear-marketing cycle lives. And the gap is closing — but not in the direction the marketing suggests. The reality is moderately useful tools, used by experienced humans, producing incremental gains in security. The marketing keeps insisting on revolution.

I'll be honest. I expected to land somewhere different when I started writing this post. The Anthropic narrative is internally consistent. The FreeBSD demo was striking. The CyberGym numbers, taken at face value, are impressive. Going into the research, I half-expected to find that Stenberg was being too harsh, or that the curl test was an unfair venue, or that the false-positive rate would soften under closer reading.

It didn't. The numbers are what they are. One out of five on the security label. Twenty minor bugs at acceptable accuracy. Zero advanced findings that earlier tools missed. Against the most extensively pre-analysed open-source C codebase on the planet, Mythos performed like a moderately better version of what was already in production.

This connects directly to a frame I keep coming back to: the [hype-vs-reality calibration problem in AI](https://www.mejba.me/ai-prompting-rules-reduce-guessing) that I've written about before. Marketing claims travel at internet speed. Verification travels at human speed. The window between launch and verification is exactly where the narrative gets shaped — and by the time the verification arrives, the original narrative has often already been priced in by the markets, the press, and the policy conversation.

This isn't an anti-AI position. I run AI agents in production daily. I bet my own time and money on these tools. But betting well requires calibration, and calibration requires watching what happens when capability claims meet the real world.

The curl test is the real world. The score is one low-severity bug.

## The Maturity Curve: From AI Slop to Useful, In Two Years

There's a longer arc here that's worth zooming out on, because the curl episode isn't a one-frame snapshot — it's a frame in a sequence that started two years ago and is still evolving.

Pull up the timeline:

**January 2, 2024.** Daniel Stenberg publishes "The I in LLM stands for Intelligence." In it, he describes the flood of low-quality AI-generated bug reports hitting curl's HackerOne program. By mid-2025, he estimated that roughly 20% of submissions to the curl bug bounty were what he called "AI slop" — reports that sounded technical but contained nothing useful. The accurate report rate fell to roughly **one in 20 or one in 30**, and triage was draining the seven-person security team's bandwidth.

**January 26, 2026.** Curl announced the termination of its paid bug bounty program. The cited reason: AI-generated slop had made the cost-benefit math collapse. A bounty designed to reward useful disclosures had become a magnet for low-effort, high-volume AI-assisted submissions. Curl wasn't the only project affected — Nextcloud and several others took similar steps around the same time. The open-source security ecosystem was being DDoSed by AI-generated reports.

**April 22, 2026.** Stenberg publishes "High-Quality Chaos." The tone shift is real. He notes that AI-assisted reports — when run by experienced engineers, not anonymous bounty submitters — are now delivering genuine signal. Tools like AISLE, Zeropath, and OpenAI Codex Security have collectively pushed hundreds of fixes into curl. AI has crossed the threshold from net-negative to net-positive in the curl ecosystem.

**May 6, 2026.** Curl receives the Mythos report. Five findings. One survives review.

**Late June 2026 (planned).** Curl 8.21.0 ships with the patch for the one confirmed Mythos finding.

That two-year arc is the actual story. AI security tooling started as a nuisance, became modestly useful, and is now incrementally improving — quarter by quarter, model release by model release, with each generation slightly tighter than the last. Mythos is the latest data point on that curve, not a discontinuity from it.

I think that arc is the most important framing for any developer trying to figure out where to place their bets right now. The maturity curve is real. It's pointing in a useful direction. But it's not vertical. It's not even particularly steep. It's a normal, somewhat-faster-than-usual capability curve in a field that's been over-promised for at least three years.

Side note — I tested this hypothesis on my own infrastructure last weekend. Ran an AI-assisted security review across a mid-size Laravel codebase I maintain for a client. The findings were useful. Some were already in our backlog. A couple were genuinely new. None of them justified rewriting the security strategy. That experience tracks exactly with what the curl team is reporting. Useful tool. Not a revolution. Pair it with experienced humans and it earns its keep. Hand it the wheel and it'll waste your time.

## The Project Glasswing Equity Problem Nobody Wants to Talk About

There's a piece of this story that the technical write-ups keep skipping past, and I want to spend some time on it because I think it's the most consequential long-term issue.

Mythos is restricted. The model isn't generally available. Access is gated through Project Glasswing, with a curated partner list and the Linux Foundation serving as a conduit for a small set of open-source projects. Anthropic's framing is that the model is too dangerous to release broadly, so they're directing it toward defensive use with trusted partners and committing $100M in model credits to make it economically viable for those partners.

Take that framing at face value for a moment. The structural consequence is the same regardless of intent: **a small number of organisations get early access to the best vulnerability-detection model available, and the rest of the world doesn't.**

Now layer in two facts.

Fact one: Stenberg, the maintainer of one of the most security-critical pieces of open-source infrastructure on the internet, was promised Mythos access via Glasswing and never got it. He had to wait for someone else to run the model and email him the report. If curl is too small to clear the access bar, what does that say about the long tail of less-famous open-source projects? The 90% of dependencies sitting under your application that don't have a maintainer with a recognisable name?

Fact two: Anthropic's own internal assessment, leaked in the [Claude Mythos document leak](https://www.mejba.me/anthropic-claude-mythos-leak) earlier this year, described the model as tipping the offense-defense balance in favor of _offense_. Their words, not mine. The model is a force multiplier for whoever holds it. Restricting access by trust and curation means defenders with access get the multiplier; defenders without access don't.

Where this lands in practice: well-resourced organisations with the right relationships get protected. Everyone else gets to hope that the eventual public-tier model arrives before an attacker with comparable capability does. That's not a hypothetical concern — it's the same access-asymmetry problem that's been a feature of the cybersecurity industry for decades, except now the asymmetry sits at the model layer rather than the tooling layer.

I'm not arguing Anthropic made the wrong call. The dual-use problem is real. A broadly-released Mythos would absolutely end up in the hands of attackers, and the safety case for staged rollout has merit. But there's a real cost to that approach, and the cost is borne disproportionately by the smaller players in the security ecosystem — the maintainers, the indie security researchers, the open-source projects that don't have the institutional pull to make a Glasswing partnership list.

If the marketing framing were honest, it would acknowledge this cost. "The zero days are numbered" would become "the zero days are numbered for our partners; the rest of you still need to figure it out." That's a less impressive headline. It's also closer to what's actually happening.

## What This Means For How You Use AI In Your Own Security Work

Let me bring this back to the practical question, because if you're reading this, you probably have AI tooling somewhere in your security stack — or you're considering it. The curl episode has some specific implications for how to use those tools well.

Here's the framework I'm running with now, based on what the curl data is telling us.

**Use AI as a force multiplier on the experienced engineer, not a replacement for one.** The curl team got useful output from Mythos because they had a seven-person security team that could triage five findings down to one truth. Without that triage layer, all five findings would have either been treated as real (wasting downstream effort) or all five would have been dismissed (missing the one real bug). The triage layer _is the value_. AI without expert review is slop. Expert review without AI is slower than it needs to be. Together, they're the current state of the art.

**Expect a 15-25% true-positive rate on flagged security issues from any current AI tool.** That's roughly where Mythos landed against curl, and it's consistent with what I've seen from Codex-style security scanners in client work. Plan your review pipeline around that ratio. If your team can't afford to triage four false positives for every real finding, AI security tooling will cost you more time than it saves.

**Treat severity labels from AI tools as suggestions, not classifications.** Mythos labelled all five curl findings as confirmed security vulnerabilities. The curl team's actual severity assignment for the one true finding was _low_. That's a multi-step downgrade — from "security vulnerability" to "low severity bug." Severity is a judgment call that depends on threat model, attack surface, and exploit conditions. AI tools currently can't do that judgment well. They flag patterns. Humans assess risk.

**Don't pay for the version-locked enterprise tier unless you can verify the gain.** The Mythos result against curl, compared to results from AISLE and Zeropath in the months before, suggests that the gap between frontier security models and the previous generation is narrower than the marketing implies. Before signing a six-figure contract for "frontier-tier" AI security tooling, run a parallel evaluation against the cheaper alternatives on a representative slice of your own code. The curl numbers suggest the delta may not justify the price.

**Pay attention to bug discovery, not just vulnerability discovery.** Mythos's strongest result on curl was the ~20 minor non-security bugs it surfaced. Those have real value — code quality improves, future bug surface area shrinks, maintenance gets easier. If you frame AI security tooling purely as a CVE-finder, you'll undervalue it. If you frame it as a "code quality and risk reduction" tool, the ROI math looks better.

This framework isn't novel. It's what experienced security engineers have been saying about AI tooling for the last 18 months. The curl episode just made it harder to dismiss those engineers as out of touch.

## The One Prediction I'm Confident About

I want to close with a prediction, because I think the trajectory matters more than the snapshot.

The Mythos+curl episode will be looked back on as the moment the 2026 AI security narrative recalibrated. Not because Mythos failed — it didn't — but because the gap between marketing claims and verified output became impossible to ignore when the verification came from a maintainer with a public platform and zero incentive to flatter the vendor.

What happens next, I'd bet, is a quieter, more honest second wave of AI security claims. Vendors will dial back the "zero days are numbered" rhetoric. The framing will shift toward "force multiplier" language, "human-in-the-loop" architectures, and "incremental risk reduction" — the actual value proposition. The truly novel research direction — autonomous agentic security tools that can find, validate, and patch vulnerabilities end-to-end — will continue to advance, but at a pace that looks like normal capability growth, not the discontinuous leap that Project Glasswing was packaged as.

The vulnerabilities will keep coming. Curl will keep publishing CVEs at roughly its current rate. The pipeline of human researchers will remain the dominant source of high-impact findings for at least the next several quarters. AI tools will continue earning their keep at the margins, getting better year over year, and occasionally turning up something genuinely surprising. Mostly, they'll do what they've started doing: catch the routine stuff faster so humans can focus on the hard stuff.

That's the boring version of the story. It also happens to be the true one.

If you'd like the unsexy version of the future of AI security, here it is in one sentence: better tools used by experienced engineers will keep beating worse tools used by inexperienced engineers, and the gap between the two will widen, not narrow. The Mythos+curl episode is a data point in service of that thesis. The marketing will catch up to the reality eventually. It always does. But in the meantime, the calibrated bet is to assume your AI security tooling is moderately better than what you had last year — and to keep the experienced humans firmly in the loop.

Daniel Stenberg already figured this out. The curl 8.21.0 release will ship in late June with one low-severity CVE patched, courtesy of an AI scan that promised five vulnerabilities and delivered one. The bug will be fixed. The codebase will be slightly stronger. The marketing will move on to the next claim.

And somewhere in the next sprint, a competent engineer is going to use an AI tool to find a real bug in their own codebase, fix it before it ships, and get back to work. That's the future. It's already here. It just doesn't sound as good in a press release.

## Frequently Asked Questions

### What did Anthropic's Mythos actually find in curl?

Mythos reported five "confirmed security vulnerabilities" in curl, but only one survived review by the curl security team. The one confirmed finding is a low-severity bug being patched in curl 8.21.0, scheduled for late June 2026. Three of the rejected findings were false positives flagging documented API behaviour, and one was a non-security bug. Mythos also surfaced roughly 20 minor non-security bugs with good accuracy.

### Why did Daniel Stenberg call Mythos a marketing stunt?

Stenberg, curl's lead maintainer, concluded that "the big hype around this model so far was primarily marketing" because Mythos didn't outperform earlier AI tools like AISLE, Zeropath, or OpenAI Codex Security — all of which had already pushed hundreds of bugfixes through curl over the prior 8-10 months. The 20% true-positive rate on Mythos's highest-confidence label was the deciding signal.

### What is Anthropic's Project Glasswing?

Project Glasswing is Anthropic's restricted-access program for distributing Claude Mythos Preview to selected security partners, with the Linux Foundation acting as a conduit for some open-source projects. Anthropic committed $100M in model credits to the program. Stenberg was promised access but never received direct access — someone else with Glasswing credentials ran Mythos against curl and emailed the report.

### Should I use AI for security analysis in my own projects?

Yes, with appropriate framing. Current AI security tools — including Mythos — work as force multipliers for experienced engineers, not replacements. Expect a 15-25% true-positive rate on flagged security issues, plan a triage layer accordingly, and don't outsource severity classification to the model. See the full implementation framework in the "What This Means" section above.

### When will Mythos be available to the public?

Anthropic has not announced a public release. The model is currently restricted to Project Glasswing partners under a managed access program, citing concerns about dual-use offensive capability. There's no published timeline for broader availability, and based on Anthropic's framing of the model as a critical-software defensive asset, broad release seems unlikely in the near term.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

- **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
- **Portfolio**: [mejba.me](https://www.mejba.me)
- **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
- **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
- **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
