**BRAND:** mejba.me
**TITLE:** The AI Layoff Trap: Why Firing Workers Eats Demand
**META TITLE:** AI Layoff Trap: The Math Behind The Demand Collapse
**SLUG:** ai-layoff-trap-pigovian-tax-prisoners-dilemma
**PRIMARY KEYWORD:** AI layoff trap
**META DESCRIPTION:** A new Wharton/BU paper proves AI layoffs are a prisoner's dilemma that collapses demand. Only a Pigouvian tax fixes it. My read on what builders should do.
**TAGS:** AI Economics, Future of Work, AI Policy, AI Layoffs, Game Theory

---

# The AI Layoff Trap: Why Firing Workers Eats Demand

On February 26, 2026, Jack Dorsey did the math out loud and 4,000 people lost their jobs in a single afternoon.

Block — the company behind Square, Cash App, Tidal, and Afterpay — went from a little over 10,000 employees to just under 6,000 in one move. Forty percent of the workforce. Dorsey didn't bury the reason in HR-speak. He posted the memo on X. He cited "intelligence tools" — AI — as the driver in his shareholder letter. And then he said something that, as a builder shipping AI products every single day, I haven't been able to stop chewing on for two months.

> "I think most companies are late. Within the next year, I believe the majority of companies will reach the same conclusion and make similar structural changes. I'd rather get there honestly and on our own terms than be forced into it reactively." — Jack Dorsey, [post on X](https://www.cnn.com/2026/02/26/business/block-layoffs-ai-jack-dorsey), Feb 26, 2026

Wall Street loved it. The stock surged roughly 20% the next session. The market read the move exactly the way Dorsey did — as proof that Block had cracked the code on doing more with fewer people. Every CFO watching that ticker did the same arithmetic. Every board sitting on idle AI budget asked the same question on Monday.

And six days later, on March 2, 2026, two economists — one at the University of Pennsylvania, one at Boston University — quietly dropped a 70-page paper on arXiv that says, with the kind of cold formal proof that makes the math undeniable, that this entire industry-wide instinct is a trap. Not a moral failure. A *trap*. A prisoner's dilemma where the move that maximizes one company's profit, executed simultaneously by every company, collectively destroys the market they're all trying to win.

The paper is called [The AI Layoff Trap](https://arxiv.org/abs/2603.20617). The authors are Brett Hemenway Falk (UPenn) and Gerry Tsoukalas (BU). I've spent the last three weeks living inside it. This post is what I pulled out — the argument, the counterintuitive findings, the six proposed fixes ranked honestly, and where I think the paper is right, where it's understating its own conclusions, and what it means for any of us building AI products and betting careers on what happens next.

If you ship AI for a living, this is not optional reading. Let me show you why.

## The Blind Spot Sitting On Every CFO's Spreadsheet

Here's the thing that got me, and it got me before I even opened the paper.

A worker is not just a cost line. A worker is also a customer.

Read that twice. Because every spreadsheet I've ever seen treats payroll as pure outflow — a number on the expenses side, full stop. The CFO models the savings from a layoff cleanly: take total comp, multiply by headcount cut, subtract severance, add it to operating margin. Done. The model gives you an unambiguously positive number every time.

But that worker is also somebody who buys things. A bag of groceries. A gym membership. A Cash App subscription, ironically. A Block-branded payment terminal at the cafe down the street. A SaaS license. A hardware upgrade their employer would have signed off on. When you fire them, the savings hit your books on day one. The lost demand they would have generated hits *somebody's* books — and most of it doesn't hit yours.

That's the externality. That's the entire paper in one sentence. Falk and Tsoukalas call it a "demand externality" — the same way an industrial polluter doesn't pay the cost of the river they contaminate, an automating firm doesn't pay the cost of the consumer demand they vaporize. The savings are private. The damage is socialized.

I've been building AI products for years. I've watched founder after founder pitch a deck where the headline number is "we replace $X million of payroll with our agent." I have made that pitch myself. None of those decks model the demand-side effect. None of them ask what happens when every competitor in the category does the same thing on the same quarter. The income statement model is missing a column.

The brutal punchline is that under competitive pricing — which is the world we actually live in — the firm that automates first captures *the entire* cost saving, but bears only a *fraction* of the demand damage, because the demand damage is spread across the whole market. So unilaterally, every individual company has a profitable, mathematically dominant move. Fire. Always fire. There is no equilibrium where firms voluntarily hold back, because holding back loses to anyone who defects.

This is exactly the structure of a prisoner's dilemma. And the paper formalizes it in a way I haven't seen done before this clearly.

## The Math, Made Concrete (Why Firing 1,000 Workers Looks Like A Genius Move)

Let me walk through the example the paper uses, because the moment I pencilled it out on a napkin it stopped being abstract.

Imagine a market with ten roughly equal-sized companies. Call them Company A through Company J. Each has, say, 1,000 workers. Total industry workforce: 10,000 people. Those 10,000 people earn salaries. They spend a portion of those salaries inside this market — buying the goods and services these ten companies sell to each other and to the rest of the economy.

Company A decides to deploy AI and lay off all 1,000 workers. Here's what happens on Company A's balance sheet:

- **Payroll savings:** 100% — Company A captures every dollar it used to pay those workers
- **Demand impact:** 1/10 — Company A loses roughly its proportionate market share of the demand those laid-off workers used to generate. The other 9/10 of the demand loss is spread across Companies B through J

From Company A's perspective, this is unambiguously profitable. You save 100% of the cost; you only eat 10% of the corresponding demand loss; the other 90% is somebody else's problem. The math works. The CFO gets a promotion. The stock pops 20%.

Now, Companies B through J look at Company A and run the same arithmetic. They have to. Their boards are asking. Their analysts are asking. Their stockholders are asking. They all reach the same conclusion: "If we don't do what A did, our cost structure is upside-down relative to A and we lose on margins. If we do what A did, we capture 100% of our payroll savings and only eat 1/10 of the marginal demand damage."

So all ten companies fire all 10,000 workers.

And now the market has lost 100% of the demand those workers used to generate. The 1/10 share each company "only eats" — that share is now stacked ten times. The total demand collapse hits everyone in full. Profits across the entire market crash because the customers stopped showing up. The savings on payroll are real, but they've been swamped by the revenue collapse on the top line.

Every individual decision was rational. The collective outcome is a smoking crater.

The paper proves this formally. In what they call the "frictionless limit" — the limit where every task is equally automatable — the game sharpens into a textbook prisoner's dilemma where firing your *entire* human workforce is the strictly dominant strategy. Not just a good move. The dominant move. The move you take regardless of what anyone else does. Even if you knew with certainty that everybody else was going to hold back, you'd still defect, because defecting is *individually* profitable in every state of the world.

This is the trap. It's not that CEOs are evil. It's that the rules of the game punish anyone who plays it for the long term. Dorsey's tweet, read inside this framework, isn't bravado. It's the announcement of a Nash equilibrium move. It's a rational actor playing the dominant strategy and telling the other rational actors "you should play yours too, because you're going to anyway, you might as well do it now."

## Why Voluntary Industry Pacts Will Not Save You

Here is the part where the paper gets genuinely surgical.

Every time I have this conversation with another founder, somebody says: "But what if all the big players just *agreed* not to do this? An industry pact. A code of conduct. A pledge."

Falk and Tsoukalas walk this idea into the meat grinder. The result is not pretty.

In a prisoner's dilemma, voluntary agreements are not self-enforcing. The structure of the payoff matrix means that if you and I sign a pact saying we won't automate, and you honor it, my best move is to defect — fire my workers and capture the cost savings while you're holding the line. I get the upside. You get nothing. If you also defect, well, we're both worse off than if we'd both honored the pact, but I'm not worse off than I would be if I held the line and you defected. So defection is dominant in *every* state. The pact has no teeth.

The authors specifically note this is not a "coordination failure that communication can resolve." Talking to each other doesn't fix it. Trust doesn't fix it. Public commitment doesn't fix it. The structure of the incentives is rotten. As long as defection is individually rewarded, no non-binding arrangement is stable.

This is, by the way, why the periodic "AI ethics summit" or "responsible automation pledge" makes me roll my eyes a little. Not because the people signing them are insincere. Some of them genuinely mean every word. But the equilibrium analysis is unforgiving. The first quarter where one signatory's competitor — *who didn't sign* — posts a 200 basis point margin advantage on the back of an AI rollout, that signatory's board is going to have a very serious meeting. And by Q2, the pact is dead.

The only way out of a prisoner's dilemma is to change the payoff matrix. Not the players. Not the rhetoric. The matrix.

Which brings us to the six things the paper proposes — and why five of them don't work.

## The Six Fixes, Ranked Without Mercy

The paper systematically evaluates six proposed policy responses to AI-driven layoffs. Here is the honest scorecard, with my own commentary on each, because the paper is sharper than most popular coverage of it has been.

### 1. Universal Basic Income — Partial fix, wrong target

UBI gives laid-off workers a direct cash transfer to maintain their consumption. The problem: it does nothing to the firm's cost-benefit math. The CFO running the layoff model still sees the same payroll savings on one side and the same proportional demand damage on the other. UBI helps the *worker*, which matters morally and practically, but it doesn't change the rules of the prisoner's dilemma. The arms race continues. Demand might be partially backstopped, but the externality remains uninternalized. Helpful, not solving.

### 2. Profit Tax With Redistribution — Partial fix, neutral on incentives

Tax corporate profits, redistribute the proceeds to displaced workers. Sounds clean. The problem the paper points out is that this shrinks profits *uniformly* across all firms — automators and non-automators alike — which means it doesn't change the *relative* incentive to automate vs. not automate. If automating was the dominant strategy before the tax, it's still the dominant strategy after. You've reduced the magnitude of the prize but kept the same shape. The trap stays sprung.

### 3. Worker Equity / Profit Sharing — Partial fix, internal-only

Give workers an ownership stake so they share in the productivity gains from automation. This is interesting at the firm level — it can soften the blow for the workers at the firm in question. But it doesn't address the cross-firm demand externality at all. Company A's workers benefiting from Company A's automation does nothing for Company B's, C's, or D's customers, who are *also* Company A's customers in aggregate. The mechanism is too local to fix a market-wide problem.

### 4. Voluntary Company Pact To Hold Back AI — Doesn't work

We just walked through why. The pact is structurally unstable. The first defector wins. The mechanism design is broken at the foundation. The paper rates this with about as much patience as I just did.

### 5. Retraining / Upskilling Programs — Partial, conditional on speed

This is the one I'm softest on, and the one where the paper is most cautious. Retraining works *if* displaced workers can be reabsorbed into productive new roles fast enough that aggregate demand doesn't collapse in the gap. The problem is that historically, technological transitions have taken years to decades to complete the reabsorption. The Industrial Revolution famously immiserated a generation while the long-run productivity gains accrued to their grandchildren. Whether AI plays out fast enough to bridge the gap before demand erodes is an open empirical question — one I'll come back to.

### 6. Pigouvian Automation Tax — Effective. The only one that actually solves it

This is the answer the paper lands on, and the elegance of it is genuinely the kind of thing you nod at twice when you understand what's happening.

Charge the firm a tax equal to the demand-damage their layoff inflicts on the rest of the economy. Internalize the externality. Force the CFO's spreadsheet to include the column it's been missing. The savings don't change. But now the savings have a corresponding cost that scales with the harm they cause. The arithmetic flips back to something defensible.

This is named after Arthur Pigou, the British economist who in 1920 introduced the idea that negative externalities — pollution being the canonical example — should be taxed at a rate equal to the marginal social cost they impose. The carbon tax debate is the modern descendant. The Pigouvian intuition is that markets are efficient when prices reflect *all* the costs of an action; they're inefficient when some costs are dumped on third parties. Layoff externality is, in this framing, exactly analogous to pollution. The atmosphere is the labor market. The carbon is the layoff. The fish dying downstream are the small businesses whose customers stopped walking through the door.

A Pigouvian automation tax doesn't ban layoffs. It doesn't outlaw AI. It doesn't pick winners. It just makes the firm's spreadsheet honest. If the AI productivity gain is genuinely larger than the demand damage, the firm still automates and pays the tax — and the tax revenue can fund retraining, UBI, infrastructure, anything. If the gain is smaller than the damage, the firm doesn't automate, and the social outcome is preserved. The market does the sorting. The externality stops being a free externality.

It's the only fix the paper formally proves works in their model, and after sitting with the argument for three weeks I think they're right. The other five are partial measures, ranging from "marginal help" to "structurally hopeless." The Pigouvian tax is the one that actually rewires the matrix.

## The Red Queen Effect: Why Better AI Makes The Trap Worse

Here is the finding that scared me the most. I had to read it three times to make sure I understood it correctly.

You'd think, intuitively, that as AI gets more capable, the harm from layoffs gets *smaller* because the productivity gains get *bigger*. More AI capability per dollar means more output per dollar means more economic surplus to go around. The pie grows. Surely that helps?

The paper proves the opposite.

Better AI makes the trap *worse*, not better. Here's the mechanism: stronger AI = bigger payroll savings per worker fired = bigger incentive for any individual firm to fire. The cost of holding back rises. The reward for defecting rises. Meanwhile, the demand damage from each layoff doesn't shrink — it stays roughly proportional, or in some scenarios actively grows because the laid-off workers stay unemployed longer in a higher-automation environment. So the gap between "what the firm captures by automating" and "what society loses from the automation" widens as AI improves.

The result is what the authors describe as something close to a Red Queen effect — a phrase from biology by way of Lewis Carroll. In Carroll's *Through the Looking-Glass*, the Red Queen tells Alice that in her country, "it takes all the running you can do, to keep in the same place." Biologists adopted the metaphor for evolutionary arms races where a species has to keep evolving just to maintain its position relative to predators and competitors. Apply it to firms in this AI environment: as AI gets better, every firm has to automate harder just to keep up with every other firm automating harder. Nobody gains a *relative* advantage. The race intensifies. The aggregate demand-damage bill grows. Collectively, no progress; individually, no escape.

This is the part of the paper that most coverage misses. The story is not "AI is bad in the early days but everything will be fine once it matures." The story is the opposite: the more capable AI becomes, the more deeply rational firms are entrapped, and the harder it gets to climb out without policy intervention. Capability does not solve coordination. In fact it sharpens the coordination problem.

I want to sit with that for a second. Because if you're a builder shipping AI products — and reader, I am, every week — your default mental model is "better models make the world better." It's the cleanest possible mission statement. And inside the four walls of any individual product, it's true. Better models do let me do more for my users. The cost-per-token has cratered. The capability per dollar has gone vertical. Every release of Opus, every release of Sonnet, every release of Codex makes the things I build cheaper to run and more useful to use. ([I wrote about exactly this dynamic when Anthropic's SpaceX compute deal doubled rate limits in early May.](/claude-code-rate-limits-doubled-spacex))

But the Red Queen finding says: the macro effect of all of us building these things, simultaneously, in a market structured the way ours is structured, can be net-destructive even when each individual product is net-positive at the user level. That is a deeply uncomfortable result to chew on as an AI developer. I've been chewing.

## The 2025 Body Count, And Why The Paper's Timing Matters

The paper landed at a very specific moment.

Layoffs.fyi's tracker shows 2025 closed with around 157,000 tech layoffs. Challenger, Gray & Christmas — which tracks the *stated reason* for cuts using their job-cuts report methodology — attributed roughly 55,000 of those to AI specifically as the cited driver. Independent trackers like Programs.com put the AI-attributable number above 100,000 when you include companies investing heavily in AI even if they didn't put it in writing. The truth is somewhere in that range; the methodologies legitimately differ. The trend doesn't.

By the first quarter of 2026 the picture had sharpened. According to Nikkei Asia reporting cited in the academic coverage of the AI Layoff Trap paper, roughly 78,557 tech workers were laid off globally between January and early April 2026, with 76% of those cuts in the United States. Of those, 37,638 — about 48% — were *explicitly* attributed to AI and automation by the companies doing the cutting. That's a doubling of the AI-attribution share inside one year.

Block's 4,000-worker February day was the loudest of those moves but it wasn't isolated. CNBC reported in late April that Meta and Microsoft had announced or signaled around 20,000 combined cuts that quarter, which their own coverage framed under the headline "raise concern that AI-driven labor crisis is here." The names rotate. The pattern holds.

Against that backdrop, the Anthropic Economic Index — Anthropic's own quarterly report on how Claude is being used across the economy — published its [March 2026 "Learning Curves" report](https://www.anthropic.com/research/economic-index-march-2026-report) showing that approximately 4% of jobs use AI for at least 75% of tasks, and roughly 36% have at least 25% AI usage. Coding remains the dominant Claude use case at about 35% of conversations. The most exposed occupations by Claude usage data are computer programmers, customer service representatives, and financial analysts — skilled, educated roles, not just entry-level. Anthropic found "no clear spike in unemployment rates for workers in the most AI-exposed occupations" — but they did find that hiring of younger workers (ages 22–25) into high-exposure roles has slowed by approximately 14% since ChatGPT launched.

Read those two data points together: aggregate unemployment isn't yet showing the AI signal, but the entry-level door is closing. The first crack in the glass shows up exactly where you'd expect it to show up first — at the bottom rung, where the marginal worker is most easily substituted by a model.

That's the empirical context for the paper. It's not theorizing about a hypothetical 2030 scenario. It's modeling a process that's already six quarters into its first phase, with one Fortune 500 company having publicly tweeted the playbook and the CEO of that company predicting on the record that the rest will follow within 12 months.

The math came in late, but it came in.

## What I Think The Paper Gets Right, And Where I'm Pushing Back

Three weeks of living inside this paper, here's where I land.

**Where I think Falk and Tsoukalas are unambiguously right:**

The prisoner's dilemma framing is correct. I've talked to founders, CFOs, and operations leads in three sectors over the past two months. Without exception, the spreadsheet they build to evaluate AI deployment looks exactly the way the paper describes. Pure cost savings on one side, no demand-externality column on the other side. Anyone who tells you the market is "self-correcting" on this is not modeling the same equation the people pulling the trigger are modeling.

The voluntary-pact-doesn't-work argument is rock-solid. It would be rock-solid even if I hadn't watched a half-dozen industry coalitions get formed and dissolved around AI ethics over the last 18 months. The math is the math. Coalitions in prisoner's dilemmas defect. Always.

The Red Queen point is the most underrated finding in the paper and the one most coverage gets wrong. "More AI = more harm in this regime" is a result that has to be sat with, not waved away.

**Where I'm less sure:**

The retraining-conditional-on-speed point is the load-bearing uncertainty. Past automation waves *have* eventually reabsorbed displaced workers — into entirely new sectors, often ones that didn't exist before the wave started. The horse-and-buggy worker became a car mechanic. The factory worker became a service worker. The bank teller became a software engineer. The reabsorption is not magic; it requires capital formation, training pipelines, and social patience. The paper's model is right that *if* the reabsorption is fast and complete, retraining alone could be enough. The empirical question is whether AI displacement is happening faster than the reabsorption pipe can flow.

I genuinely don't know the answer. I lean toward "this time really is faster," because the speed and breadth of AI substitution is qualitatively different from prior waves — it hits cognitive work directly, simultaneously, across many sectors, with a deployment cycle measured in months rather than the decades that mechanization took. But I want to be honest that this is a judgement call, not a proof. Acemoglu and Restrepo's [work on task displacement and reinstatement](https://onlinelibrary.wiley.com/doi/full/10.3982/ECTA19815) is the rigorous version of this debate, and it's far from settled even among the people who built the framework.

**Where I'd push the paper:**

I think Falk and Tsoukalas understate how badly the international coordination problem will hurt any actual Pigouvian tax in practice. The paper's proof works in a closed economy. In an open economy, if the United States imposes a layoff tax and Korea doesn't, U.S. firms relocate or restructure their automation in jurisdictions without the tax, the tax base erodes, and the incentive matrix bends back toward defection at the country level instead of the firm level. The same prisoner's dilemma scales up one level of abstraction. Solving it requires either (a) coordinated international policy — historically very hard — or (b) a tax structure that's based on the location of the *displaced worker* rather than the location of the firm doing the displacing, which would be domestic-revenue-friendly but a regulatory nightmare to actually implement.

This is the part where I'd want a follow-up paper. The closed-economy proof is the necessary first step. The open-economy game theory is the actually-relevant policy question.

## What This Means If You Build, Invest, Or Bet On AI Products

Three weeks into thinking about this, here's where my own decisions have moved.

**For builders and founders shipping AI products:** Stop selling pure replacement. Sell augmentation, capacity expansion, or new-task creation. Not because replacement is morally wrong — it's not, on a per-firm basis — but because the demand-collapse risk hits *your* customers' customers, which means it hits *your* TAM eventually. If the only way your product saves your customer money is by them firing their workers, your customer's customers were partially funded by those wages, and the hit comes back to your customer's revenue line within 4–8 quarters. A product that helps a 10-person team do the work of 30 is structurally more durable than a product that helps a 30-person team be replaced by 10. The math of customer LTV runs through the broader economy.

**For investors:** The "AI replaces jobs = pure margin expansion" thesis is partially priced into the market right now and I think it's mispriced. Not because the savings aren't real — they are, on the income statement, in the short run. But because the demand-collapse counterweight is real too, and it's only one or two earnings cycles away from showing up in the top line of the same companies that are currently celebrated for the cuts. Block's 20% pop on layoff day is a thesis being expressed. The thesis has a counterargument, and the counterargument has a research paper backing it. I'd be uncomfortable holding a long position based purely on AI-driven margin expansion at the consumer-facing layer of the economy without an explicit hedge against demand erosion in the same sector.

**For solo operators and small teams:** This is where the picture is most interesting, actually. The AI Layoff Trap is a problem of *competition between firms in the same product market.* It's a coordination problem at scale. If you're a [solo operator running an AI-first company](/ai-first-company-2026-solo-operator), or a team of three building [agents that operate without human staff](/anthropic-managed-agents-office-ai-workers), you're not contributing meaningful demand damage to the broader economy because you weren't carrying meaningful payroll in the first place. The trap is for incumbents trying to optimize their existing 10,000-person org charts. The opportunity is for new entrants building structures that never had the headcount to lay off.

**For policy watchers:** The Pigouvian conversation is going to get loud in 2026. Korea has already floated a "robot tax" concept. The EU has been laying groundwork on AI-related labor protections through the AI Act and adjacent instruments. The U.S. is unlikely to move first, but the pressure will build. Watch for the first jurisdiction to actually implement a layoff-linked tax, because that becomes the reference case for everyone else.

**For everyone, including me:** Don't confuse the per-product story with the per-economy story. Each AI tool I ship makes some user's life better at the user level. That's true and I'm not going to apologize for it. But the aggregate consequence of all of us shipping all of these tools, deployed simultaneously to replace work in a market structured the way ours is structured, is not the same shape as the per-product story. Two truths can coexist. Holding both is hard. Refusing to hold either is intellectually lazy.

## The Open Questions That Will Decide The Decade

I want to end with the things even Falk and Tsoukalas don't claim to know yet. Because the paper's strength is also its limit — it's a clean piece of equilibrium theory, but several of the load-bearing variables are empirical, and the empirical work is still mostly missing.

**Reabsorption speed.** Can the economy create new tasks for displaced workers fast enough to maintain demand during the transition? Past waves say "eventually yes, painfully slowly." This wave's velocity may not allow the historical reabsorption pipe to keep up. We don't know yet. The next 8–12 quarters of unemployment data, especially in cognitive-work-heavy sectors, will be the first real data point.

**The entry-level door.** Anthropic's finding that hiring of 22–25-year-olds into AI-exposed roles is down 14% is, if it sustains, the canary. Past automation transitions have been painful for incumbents but generally benevolent toward the next generation, who could move into the new tasks the technology created. If this transition closes the entry-level door faster than it opens new ones, the demographic structure of the trap looks different from anything we've seen.

**Sector breadth.** Prior automation hit specific industries (textiles, manufacturing, banking back-office). AI is hitting cognitive work in general — software, customer support, finance, marketing, legal, design. The breadth of impact means there are fewer "safe harbors" where displaced workers can flee. That changes the reabsorption math in ways the historical record doesn't directly inform.

**Policy will or absence.** The Pigouvian tax is the mathematical answer. Whether any government implements it, in any form, before the demand cliff materializes is a political question, not an economic one. My honest read: the policy lag will be measured in years. The market dynamics are measured in quarters. The two clocks are not synchronized.

**The agent-economy wildcard.** This is the one I think about most as a builder. The paper's model assumes laid-off workers leave the demand-supply equation as workers but don't substantially re-enter as something else. But in 2026, [the agent capability roadmap](/code-with-claude-2026-agent-future-preview) and [the broader AI shakeup](/ai-industry-shakeup-april-2026) suggest a future where displaced workers might re-enter the economy as one-person AI-augmented service providers, agents-as-a-service operators, or independent operators running their own tiny AI-first businesses. If that re-entry pipeline scales fast — meaning the same person who got laid off from Block in February is generating $80K of independent revenue with [AI tools reshaping work](/ai-tools-agents-reshaping-work) by the end of 2027 — the demand damage might be partially self-healing in a way the closed-economy model doesn't capture. I'm not certain this happens. I'm not certain it doesn't. The next two years will tell us.

## What I'm Watching In 2026

Specific signals I'm tracking:

- **More Block-scale moves.** Will the Q3 and Q4 earnings cycles produce more 30%+ workforce cuts at Fortune 500s? Dorsey predicted yes within 12 months. We're three months in. Watch the names.
- **Anthropic Economic Index updates.** The next quarterly report will tell us whether the entry-level hiring slowdown is deepening or stabilizing. That's the leading indicator.
- **Layoffs.fyi AI-attribution share.** The percentage of cuts explicitly tagged to AI has roughly doubled year-over-year. If it doubles again, we're in a different regime.
- **The first jurisdiction to enact a Pigouvian layoff tax.** Won't be the U.S. first. Watch Korea, watch Germany, watch the EU's AI Act implementation regulations. The reference case matters.
- **Corporate revenue against CFO-promised AI savings.** This is the data nobody has clean numbers on yet. In 12–18 months, we'll be able to compare Block's revenue trajectory against the trajectory it would have had if the workforce had been preserved. That counterfactual is hard but not impossible to estimate. The first credible attempt at it will define the conversation.

The paper is right that the math says what it says. Whether the world gets to do something about it before the demand cliff arrives — that's the part nobody knows yet. That's the part that gets decided by what happens next.

Dorsey's Block is the first big public test case. The CEO bet his company on the math working. The paper bet that the math is incomplete. Within 18 months, one of the two bets will look obvious in hindsight.

I don't know which one yet. But for the first time in two years of building AI products full-time, I'm spending part of every week thinking about something other than the next product I want to ship. I'm thinking about the shape of the economy I'm shipping into. I'd recommend you do the same.

The math has finally caught up to the headlines. Don't look away.

## Frequently Asked Questions

### What is the AI Layoff Trap?

The AI Layoff Trap is a 2026 paper by Brett Hemenway Falk (UPenn) and Gerry Tsoukalas (BU) that proves AI-driven layoffs form a prisoner's dilemma: each individual firm profits from firing workers, but when all firms do it simultaneously, collective consumer demand collapses and everyone — workers and firm owners — ends up worse off. The full argument is walked through in [The Math, Made Concrete](#the-math-made-concrete-why-firing-1000-workers-looks-like-a-genius-move) above.

### Why is a Pigouvian tax the only solution that works?

A Pigouvian tax forces firms to pay for the demand damage their layoffs inflict on the rest of the economy, internalizing the externality and changing the firm-level cost-benefit math. The paper formally proves that universal basic income, profit taxes, worker equity, voluntary pacts, and retraining alone cannot solve the trap because none of them change the relative incentive to defect. Only the Pigouvian tax does.

### Did Block really lay off 4,000 people because of AI?

Yes. On February 26, 2026, Block CEO Jack Dorsey announced the layoff of approximately 4,000 employees — about 40% of the workforce — and explicitly cited "intelligence tools" (AI) as the driver in his shareholder letter. Dorsey predicted that "the majority of companies will reach the same conclusion" within a year.

### What is the Red Queen effect in AI layoffs?

The Red Queen effect, in this context, is the finding that better AI makes the layoff trap worse, not better. As AI capability grows, the per-worker savings rise, but the demand damage stays roughly proportional, so the gap between private gain and social cost widens. Firms must automate faster just to maintain competitive parity, with no relative advantage gained. The full discussion is in [The Red Queen Effect](#the-red-queen-effect-why-better-ai-makes-the-trap-worse) above.

### How many tech workers were laid off due to AI in 2025?

Estimates range from approximately 55,000 (per Challenger, Gray & Christmas, which tracks explicit AI-citation in stated layoff reasons) to over 100,000 (per Programs.com, which includes layoffs at companies investing heavily in AI even when not explicitly cited). Layoffs.fyi tracked roughly 157,000 total tech layoffs in 2025. The AI-attributable share has roughly doubled year-over-year through Q1 2026.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
