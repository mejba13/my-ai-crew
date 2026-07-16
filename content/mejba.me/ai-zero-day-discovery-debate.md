**BRAND:** mejba.me
**TITLE:** AI Zero-Day Discovery: Is the Cyber Risk Real or Hype?
**META TITLE:** AI Zero-Day Discovery Debate: Real Risk or Marketing?
**SLUG:** ai-zero-day-discovery-debate
**PRIMARY KEYWORD:** AI zero-day discovery
**META DESCRIPTION:** Anthropic says Claude Mythos found thousands of zero-days. George Hotz says release one a day. Here's what the AI vulnerability research debate actually means.

**TAGS:** AI Security, Cybersecurity, Claude Mythos, Zero-Day Research, Vulnerability Discovery

---

George Hotz threw a grenade into the AI security discourse last week. One tweet. Forty-six words. And a challenge so pointed it made every cybersecurity marketing deck in Silicon Valley suddenly look a little silly.

"What if I release one zero day a day until a big new model is released? Will this finally make OpenAI and Anthropic shut up about 'cybersecurity risk'?"

I read it three times. Then I opened my notes from the week before — when Anthropic had announced Claude Mythos Preview, claimed an 83.1% success rate on the CyberGym vulnerability benchmark, and declined to release the model publicly because it was "too dangerous." I'd been drafting a post about what that meant for defensive security. Hotz's tweet made me tear the whole draft up and start over.

Because here's the thing. I've spent years on both sides of this equation. I build AI automation systems daily — Claude Code agents running across client projects, Opus 4.6 as my primary coding model, the whole stack. I also do security work. I've audited WordPress deployments, Laravel applications, and more AWS IAM policies than I care to remember. I've written [penetration testing-adjacent tooling](https://www.mejba.me/claude-code-security-scanner-agent). I know what finding a real zero-day actually looks like versus what finding one looks like in a demo.

And after two weeks of watching this debate unfold — Anthropic claiming existential cybersec risk, Hotz claiming it's theater — I've landed somewhere neither side will love.

Both of them are partially right. And the part that nobody is talking about is the one that actually matters.

Let me walk you through it.

## What Anthropic Actually Claimed (And Why It Got Everyone's Attention)

Before we get to the critique, let's get the facts straight. Because a lot of the conversation online is based on vibes, not the actual announcement.

On April 8, 2026, Anthropic published two things simultaneously. The first was a research preview of **Claude Mythos Preview** — an unreleased frontier model they described as a "step-change" in autonomous vulnerability discovery. The second was **Project Glasswing** — a program to direct Mythos Preview's capabilities toward defensive security, working with a small group of selected partners and open-source maintainers.

The headline number: **83.1% success rate on CyberGym**, up from 66.6% for their previous best model. CyberGym is a benchmark that measures autonomous discovery of real software vulnerabilities. Not toy bugs. Not synthetic CTF challenges. Real CVEs in real codebases.

The example that made every security researcher's coffee go cold: Mythos Preview found a **16-year-old vulnerability in FFmpeg** — the library that every piece of video software on earth depends on — in a line of code that automated fuzzing tools had hit **five million times** without catching it. It also autonomously chained together multiple Linux kernel vulnerabilities to escalate from ordinary user access to complete root control of a machine.

Anthropic's framing was unambiguous: "AI models have reached a level of coding capability where they can surpass all but the most skilled humans at finding and exploiting software vulnerabilities."

Then they didn't release the model. Limited rollout only. Approved partners under Project Glasswing. General developers and startups need not apply.

That decision — the restriction, not the capability — is what Hotz responded to. And his response is where this gets interesting.

## The Hotz Critique: Scarcity Is a Policy Choice, Not a Technical Limit

Hotz's argument, stripped of the Twitter bravado, comes down to one claim: **software vulnerabilities are not actually rare.** They're everywhere. They're in your router firmware, your smart TV, your Bluetooth stack, the JavaScript engine rendering this blog post. What's rare is people with the legal permission, the time, and the economic incentive to disclose them publicly.

"Want more zero days to be found?" he said. "Make hacking legal. Until then, don't try to claim it's hard — it's just not incentivized."

I want to push back on this before I agree with the part that's right. Because Hotz is George Hotz. He jailbroke the iPhone when he was 17. He reverse-engineered the PlayStation 3 before Sony sued him. He trains self-driving models at Comma.ai. The median security researcher is not George Hotz. Saying "zero-days are easy" when you are one of maybe two hundred people on earth with your specific skill profile is a bit like Tiger Woods saying golf isn't hard.

But.

The structural point holds. I've watched this play out in my own work. When I'm doing a security review for a client, I'm not limited by my ability to find bugs — I'm limited by **time**, **scope**, and **legal coverage**. A typical penetration testing engagement runs two weeks. In those two weeks, I'll find more issues than the client can patch in two months. If I had three months and no scope limits, I'd find an order of magnitude more. That's not a special insight. That's what every working pentester I've ever talked to will tell you.

The bottleneck isn't discovery. It's the permission structure around discovery.

Which means when Anthropic says Mythos Preview found thousands of zero-days in two weeks of internal testing, my reaction is not "wow, AI has unlocked some new capability humans couldn't touch." My reaction is "yes — because it had unlimited scope, unlimited time, and complete legal cover from its parent company." Those are the exact constraints that bind human researchers, and removing them from a sufficiently capable agent produces exactly this result.

So is the 83.1% number real? Probably yes. Is the capability dangerous? Arguably yes. Is it a categorical shift that nothing else in the security industry prepared us for? That part I'm not buying. And if you keep reading, I'll show you why the economics matter more than the capability.

## The Bug Bounty Reality Check

Here's a test for any "AI will destroy cybersecurity" narrative. Ask this question: **if zero-days are so scarce and so valuable, why hasn't the market already cleared?**

Because the market has, actually, been clearing for years. And the prices tell a story that contradicts the existential framing.

Apple's security bounty program, as of October 2025, pays **$2 million for a zero-click remote code execution exploit on iOS** — the crown jewel of offensive security. With the Lockdown Mode bonus and beta-software bonuses stacked on top, the maximum payout can exceed $5 million for a single chain. Microsoft's Zero Day Quest 2026 event — held at their Redmond campus in March — paid out $2.3 million across roughly 80 vulnerabilities, with total program budgets running into eight figures annually. Google's Vulnerability Reward Program, Meta's bounty pool, Samsung's mobile bounty — the collective legal bounty economy is well into nine figures per year.

What does this tell us? Two things, and they cut in opposite directions.

First: **the bounty market is efficient.** If zero-days were as trivially findable as Hotz implies, these prices would be lower. A researcher with a reliable pipeline of iOS zero-click RCEs wouldn't be getting $2M per finding — they'd be getting $50K, because supply would overwhelm demand. The price reflects genuine difficulty.

Second: **the money is already sitting there for anyone with the skill.** A pentester who finds one iOS zero-click chain a year makes more than most senior engineers at FAANG. If AI-assisted research genuinely multiplies a competent researcher's output by even 3x, that researcher's earning potential doesn't go down — it goes *up*, because they capture more of the existing bounty pool. The "AI will devalue security researchers" narrative only works if you assume fixed demand. Demand is not fixed. Every new IoT device, every new cloud service, every new AI model is a fresh attack surface that didn't exist five years ago.

This is the part the doom narrative ignores. The threat model isn't "AI makes zero-days infinite." It's "AI shifts *who* can find them and *how fast*." That shift has consequences — but they're economic and structural, not existential.

And before we get to what those consequences actually look like for working developers, I want to do the uncomfortable thing: steelman Anthropic's side.

## Why Anthropic's Restriction Is Not Just Marketing

The cynical read on Project Glasswing goes like this. Anthropic has a capability they want to brag about. They also have safety-conscious investors and regulators watching their every move. Solution: frame the capability as so dangerous it can't be released, generate a PR cycle about responsible AI development, and quietly use the model with approved enterprise customers who can pay for it. Cake eaten. Cake had.

I've seen that take all over Hacker News and X. And I'll admit — on a bad day, it's where my own mind goes. AI companies have been "crying wolf" about model capabilities for three years now. Every major release gets framed as a potential civilizational risk until twelve weeks later when a competitor ships something similar and the conversation moves on.

But.

I've used Claude Code enough to have calibrated intuitions about what the current Claude models can actually do. Opus 4.6 can hold a 200,000-token codebase in context and reason about cross-file dependencies in ways that would have seemed impossible eighteen months ago. I wrote about this in my [Opus 4.6 hands-on review](https://www.mejba.me/opus-4-6-hands-on-review). When I watch Opus 4.6 refactor a Laravel app, it isn't pattern-matching its way to a solution — it's reasoning about architectural intent and downstream implications.

If Mythos Preview represents a meaningful step above that — and Anthropic's own leaked internal documents, which I covered in [my Claude Mythos leak post](https://www.mejba.me/anthropic-claude-mythos-leak), describe it as "dramatically higher" — then the vulnerability research application is not a marketing stunt. It's a predictable emergent capability of a sufficiently good code reasoner, pointed at adversarial inputs.

Here's the specific thing that convinces me it's not all theater: the nature of the vulnerabilities found.

A 16-year-old FFmpeg bug that five million fuzzer runs missed is not the kind of thing you find by getting lucky with a prompt. It's the kind of thing you find by reasoning about *why* a certain code path might be unsafe in an edge case that fuzzers — which work by random mutation of inputs — would never generate. That's a different kind of search than brute force. That's hypothesis-driven vulnerability research. That's what senior humans do.

If a model can do that reliably — and if the cost of running it keeps falling the way inference costs have been falling for two years — then yes, the economics of security research genuinely do change. Not in the "AI is going to end society" way. In the more mundane, more important way: **the floor rises.**

## The Real Shift: Who Benefits From a Rising Floor

Let me try to be specific about what I think actually changes.

Before AI-assisted vulnerability research, finding a non-trivial bug in a codebase like FFmpeg or the Linux kernel required a specific combination of rare skills: deep understanding of the language, familiarity with the codebase, intuition about attacker models, and the patience to chase ten dead ends for every live one. That combination lived in maybe ten thousand people worldwide, concentrated in a few companies, a few governments, and a handful of independent researchers.

After AI-assisted research — even with public models significantly weaker than Mythos Preview — that combination lives in a much larger pool. A competent developer with security interest and a Claude API key can now do preliminary vulnerability analysis on a codebase in an afternoon that would have taken a specialist a week. The barrier to *entry* drops, even if the barrier to *elite* skill stays roughly where it was.

This is where I think the doomers are wrong and the dismissers are also wrong.

The doomers are wrong because AI doesn't suddenly hand a random 15-year-old the ability to pop the iPhone signal chain. The skills required to weaponize an LLM-found bug — writing a reliable exploit, bypassing mitigations, avoiding detection — are still deeply human skills. Finding a vulnerability is step one of maybe fifteen steps in a real offensive campaign.

The dismissers are wrong because "the bar to finding common bugs" is a real thing that matters. Most security breaches don't come from exotic nation-state zero-days. They come from garden-variety vulnerabilities — SQL injection, auth bypasses, misconfigured S3 buckets, outdated dependencies. A tool that makes discovering those vulnerabilities 10x faster changes the economic calculus for every mid-sized company that hasn't done a real security review in three years.

Which cuts both ways. Defenders can now afford continuous security review in a way they couldn't before. Attackers can now profile targets faster than ever. The race is on. And the question nobody has a clean answer to is whether the defensive side can scale attention and patching as fast as the offensive side can scale discovery.

Given how slowly most organizations patch — the Vidoc Security Lab reproduction of Mythos findings noted that 99% of the bugs Anthropic reported are still unpatched weeks later — I'm not optimistic about that race in the short term.

## Where the Benchmark Numbers Actually Point

Let's ground this in specifics. Because I've seen a lot of articles this month citing benchmark numbers without explaining what they mean for someone making real decisions.

Here are the current frontier model benchmark scores worth tracking, as of April 2026, specifically on ARC-AGI-2 — a reasoning benchmark that correlates reasonably well with general capability:

| Model | ARC-AGI-2 Score | Input / Output Price (per 1M tokens) |
|-------|-----------------|--------------------------------------|
| Claude Sonnet 5 | 84.7% | $3 / $15 |
| Gemini 3.1 Pro | 77.1% | $2 / $12 |
| GPT-5.4 (Pro tier) | 83.3% | Higher-tier pricing |
| GPT-5.4 (standard) | 73.3% | ~$2.50 / output varies |
| Claude Opus 4.6 | ~38% | $5 / $25 |

A few things jump out. First, **Claude Sonnet 5 has surpassed Opus 4.6 on reasoning benchmarks at a fifth of the price.** That's an inversion of the traditional tier hierarchy, and I've written about it in my [Sonnet 5 agentic coding post](https://www.mejba.me/claude-sonnet-5-agentic-coding). Second, every frontier model in 2026 scores dramatically higher on reasoning than the 2024 generation did — the gap between what's available to the public and what sits in labs like Anthropic's restricted Mythos tier is narrowing, not widening.

What does this mean for the zero-day debate? It means Hotz's implicit point — that public models are already capable enough to do meaningful vulnerability research, if you're willing to do the work — is increasingly defensible. Vidoc Security Lab reproduced a chunk of the Mythos findings using *public* models, with more scaffolding and more compute but without any restricted access. The gap between Mythos Preview and what a determined researcher with Claude Sonnet 5 or GPT-5.4 can do is smaller than Anthropic's framing implies.

That gap will close further. Whether Anthropic wants it to or not.

## What I'd Actually Do If I Were You

Enough debate. Let me give you the practical read for three specific situations.

**If you're a developer shipping production software:** Assume your code is going to be audited by an AI with Mythos-tier capability within twelve months — because some version of that capability will be publicly available by then. The hardening that makes sense for a human red team (input validation, minimizing attack surface, principle of least privilege, dependency hygiene) is the same hardening that makes sense for an AI-powered audit. The tools change; the fundamentals don't. If you're not already running a tool like Snyk, Semgrep, or equivalent on every PR, start this week. If you want a deeper walkthrough of how to build a continuous AI-driven security layer into your own workflow, I covered the exact setup I use in my [Claude Code security scanner agent post](https://www.mejba.me/claude-code-security-scanner-agent).

**If you're a security researcher or pentester:** This is the most interesting moment in the field in a decade. Start integrating AI-assisted discovery into your workflow now, not because it replaces your skills, but because the clients who hire you in 2027 will expect it the way they expect you to use Burp Suite today. The researchers who treat this as threatening will get flattened. The ones who treat it as a force multiplier will make more money than ever.

**If you're running a company that depends on open-source infrastructure:** Assume every widely-used open-source library is going to have multiple new high-severity findings disclosed in the next eighteen months. Budget accordingly. Fund the maintainers of libraries you depend on — through GitHub Sponsors, through direct contracts, through anything. The asymmetry Anthropic's research surfaced — powerful AI pointed at underfunded OSS — is not going away. The responsible thing is to help the humans on the other end of that asymmetry.

One more thing. If you're someone building AI agents for any purpose — and a lot of my readers are — the vulnerability research capability that Anthropic demonstrated is a preview of what agent capability looks like across every domain that involves hypothesis-driven search over large structured inputs. Bug hunting. Legal document review. Scientific literature analysis. Financial forensics. If you want a grounded look at how to think about building production agent systems right now, the [Anthropic Agent SDK guide I wrote](https://www.mejba.me/anthropic-agent-sdk-guide) covers the architectural patterns.

## Where This Leaves the Hotz vs. Anthropic Debate

Back to the grenade that started this post.

Did Hotz actually release a zero-day a day? As of this writing, he hasn't. The threat was rhetorical. Which is either a) because doing so is harder than he implied, or b) because the legal and ethical cost of actually dropping zero-days in public is enormous regardless of technical difficulty. I believe it's mostly (b), with some (a) for the specific classes of zero-clicks Apple pays seven figures for.

Did Anthropic overstate the existential risk of Mythos Preview? Probably yes on framing, probably no on capability. The model can do what they said it can do. The question of whether that's "too dangerous to release" is a policy question, not a technical one — and reasonable people can disagree on where the line sits.

Here's where I land. The interesting part of this debate is not which side is right. Both sides have sharp points and both sides are partially arguing from self-interest. The interesting part is what the debate reveals about where the field is.

We are at the point where a frontier AI model can do, autonomously, the kind of vulnerability research that previously required a senior human researcher working for a week. That's real. That's verified by independent reproductions. That's going to be publicly available — at varying quality levels — within the year.

The question isn't whether to panic or dismiss. The question is: given that this capability is now part of the landscape, how do we want to structure the legal, economic, and defensive systems around it?

That's the conversation Anthropic's framing is trying to have. It's also the conversation Hotz's critique is trying to force. They're arguing about tactics. On the underlying question — that something meaningful has shifted in offensive security — they actually agree. They just disagree about who should be allowed to say it out loud.

Me? I think the best thing a working engineer can do right now is take the threat model seriously, use the tools that are available, and harden your own systems as if a tireless AI researcher with a grudge were going to audit your code next Tuesday.

Because sooner than you think, one will.

The FFmpeg maintainers didn't know their code had a 16-year-old bug in it. They do now. The question worth sitting with tonight is what's sitting in your codebase — the one you wrote, the one you depend on, the one you shipped to production last quarter — that an AI researcher with eight hours and a prompt would find by Thursday afternoon.

That's not a marketing question. That's not a hype question. That's just the new reality of shipping software in 2026.

And if you're not hardening against it, you're already behind.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
