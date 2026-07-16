**BRAND:** mejba.me
**TITLE:** Claude Fable 5 and Mythos 5: I Tested the Public One
**META TITLE:** Claude Fable 5 Launch: What I Found Testing It
**SLUG:** claude-fable-5-mythos-5-launch
**PRIMARY KEYWORD:** Claude Fable 5
**META DESCRIPTION:** Claude Fable 5 just launched as Anthropic's first public Mythos-class model. Here's what I found testing it, the real pricing math, and the Mythos 5 catch.
**TAGS:** Claude Fable 5, Anthropic Models, AI Benchmarks, Agentic Coding, News Analysis

---

The model I'd been writing about as a rumor for months showed up in my API console on a Tuesday with a boring little dropdown entry: `claude-fable-5`.

No fanfare in the terminal. No confetti. Just a new model id sitting under Opus 4.8 in the picker, like it had always been there. I'd spent a whole post back in the spring dissecting a leaked internal draft about a model called Mythos — code-named Capabra — that Anthropic's own documents described as "the most capable we've ever built." Now the public-facing half of that exact lineage was live, billable, and waiting for a prompt. I stopped what I was doing and pointed it at a repo.

That's the strange thing about **Claude Fable 5**. The frontier didn't arrive with a keynote. It arrived as a config change. And the way Anthropic split this release in two — one model the whole world can call today, one model almost nobody can touch — is the most interesting product decision they've made in a year.

Here's what I learned testing the public one, what the announcement actually claims versus what I could verify myself, and the one detail about Mythos 5 that changes how you should think about both.

## What Anthropic Actually Launched on June 9

Claude Fable 5 and Claude Mythos 5 went live on June 9, 2026. They are, according to Anthropic, *the same underlying model* — the difference is entirely in the safety layer wrapped around it. That single design choice is the whole story, so hold onto it.

**Claude Fable 5** is the public one. It's generally available right now on the [Claude API](https://www.anthropic.com/news/claude-fable-5-mythos-5) (and consumption-based Enterprise), inside Claude Code, and across the three big clouds — Amazon Bedrock, Google's Vertex AI, and Microsoft Foundry. If you have API access, you can call `claude-fable-5` this afternoon. I did. There's also a staged subscription rollout with a real deadline attached, which I'll get to — and it's the most actionable thing in this whole release.

**Claude Mythos 5** is the one you almost certainly can't use. It's restricted to **Project Glasswing** — Anthropic's program for vetted cyberdefenders and critical-infrastructure providers. Same brain as Fable 5, but with specific safeguards lifted so security teams can probe genuinely dangerous territory. Anthropic describes it as having the strongest cybersecurity capabilities of any model in the world. You don't get to verify that claim. Neither do I. That gate is the point.

Glasswing isn't standing still, either. Existing Mythos Preview partners are being upgraded to Mythos 5, and Anthropic says it's adding roughly **150 new organizations across 15+ countries**, including a collaboration with the US government. There's also a separate, narrow **biology trusted-access program** launching for a small number of life-science researchers — and here's the nuance that matters: those bio researchers get Mythos *with the cybersecurity safeguards still active.* Even inside the vetted program, the cyber door stays the most tightly held one.

So the lineup as of June 2026 looks like this:

| Model | Price vs Opus 4.8 | Safeguards | Who can use it | Built for |
|---|---|---|---|---|
| Opus 4.8 | Baseline ($5 / $25) | Standard | Everyone | General-purpose work |
| Mythos Preview (April) | ~5× Opus | High | Glasswing only | Early cyber testing, eye-watering cost |
| **Mythos 5** | ~2× Opus | Highest, some lifted for defenders | Project Glasswing only | Advanced cyber defense, multi-domain |
| **Fable 5** | ~2× Opus | Cyber/bio queries routed to Opus 4.8 | Public (API + clouds) | Long-running agentic work, knowledge tasks |

One number on that table matters more than the rest. The Mythos Preview that shipped in April cost roughly *five times* Opus. Fable 5 and Mythos 5 land at *twice* Opus — $10 per million input tokens and $50 per million output. Anthropic more than halved the price of frontier access in two months. That's not a footnote. That's the difference between "interesting for a demo" and "I can actually run agents on this."

But before you go change your default model, you need to understand what that "safeguards" column is really doing — because it behaves differently than any model gate I've used before.

## The Safety Net Is a Different Model Entirely

Most "safe" models refuse. You ask something spicy, you get a polite decline, you move on annoyed. Fable 5 does something I hadn't seen before, and once I understood it, a lot of the design clicked.

When a Fable 5 session trips a safety classifier — Anthropic flags three zones specifically: offensive cybersecurity, *most* biology and chemistry requests, and model-distillation attempts — your request doesn't get refused. It gets **silently answered by Claude Opus 4.8 instead.** You're still talking to a model. Just not the frontier one, for that particular turn. The cyber zone is the strictest: Anthropic says the classifiers there prevent Fable from making *any* progress on offensive cyber and exploitation tasks at all.

Think about what that means in practice. The dangerous capability and the everyday capability live in the same API endpoint, and a classifier decides, per session, which brain you reach. Anthropic's own number is the reassuring one: **"more than 95% of Fable sessions involve no fallback at all."** I couldn't manufacture a clean trip during normal coding work, which tracks — nothing about refactoring a Laravel service or building a Next.js dashboard looks like bioweapon research.

That thin-but-firm safety layer held up under genuine adversarial pressure, which is worth saying because it's the part most launch coverage skips. An external bug bounty found **no universal jailbreaks in over 1,000 hours of testing.** One external partner threw 30 different jailbreak techniques at it and reported Fable 5 "complied with zero harmful single-turn requests" — they called its safeguards the most robust they'd tested. The UK's AI Safety Institute did make *progress* toward a jailbreak in a brief window, so this isn't unbreakable — but the bar is high. Anthropic also retains all Mythos-class traffic for 30 days for safety monitoring and explicitly doesn't use it for training.

Here's why this is clever, and why it's also a little unsettling. It's clever because the vast majority of real developer work never touches the gated zones, so 95%+ of the time you get the full frontier model with zero friction. It's unsettling because the *capability is still in there.* Fable 5 isn't a weaker model that can't do the dangerous thing. It's the dangerous model with a bouncer at one specific door. Mythos 5 is the same building with the bouncer told to step aside for trusted guests.

I keep coming back to a line from the announcement materials: the models are designed to be neutral, and outcomes depend on human intent and judgment. That's true, and it's also the most loaded sentence Anthropic has shipped this year. A model this capable, gated this thinly, makes the question of *who's holding it* the entire safety story. I dug into exactly that tension when [Anthropic accidentally leaked the original Mythos documents](https://www.mejba.me/blog/anthropic-claude-mythos-leak) — and the public launch resolves almost none of the questions that leak raised. It just makes them billable.

Alright — that's the philosophy. Let's get concrete about whether the thing is actually good.

## Is Claude Fable 5 Actually Better Than Opus 4.8?

Yes — and Anthropic's own framing is unusually direct about it: "Fable 5's capabilities exceed those of any model we've ever made generally available." The pattern in the claims is what made me sit up. This isn't a single headline benchmark. It's a spread of them, across domains that don't usually move together.

Start with coding, because that's what most of us are buying it for. Anthropic reports Fable 5 scores **highest among frontier models on Cognition's FrontierCode software-engineering eval — and it does it at *medium* effort,** not maxed out. That last detail matters more than the ranking: a frontier coding result without cranking the effort dial is exactly the kind of headroom that shows up in your actual day, not just in a press chart.

The result I keep circling back to is the analytics one. Fable 5 is, in Anthropic's words, the **first model to break 90% on their core analytics benchmark** — a roughly ten-point jump over Opus 4.8. (Worth correcting a thing floating around the early coverage: that 90% is Anthropic's *own* internal analytics benchmark, not a third party's eval. I had the attribution wrong in an earlier draft and want to be straight about it.) Crossing 90 there suggests the gains aren't just coding-shaped; they extend into the multi-step analytical reasoning that agentic workflows lean on hardest.

The rest of the capability sheet is broad in a way that's hard to fake:

- **Top score on Hebbia's Finance Benchmark** — the highest of any model, per Anthropic.
- **"Aced" IMC's trading-analysis evaluations**, in Anthropic's phrasing.
- **State-of-the-art for vision tasks**, which is the engine behind the agentic demos I'll get to in a second.
- **Vision, tool use, memory, compaction, and adaptive thinking** all built in, same as the rest of the current Claude line. (Anthropic's materials don't pin a specific context-window or max-output number to Fable 5, so I'm not going to hand you one as if it were confirmed — treat the long-context capability as the general Claude strength it is, not a verified Fable-5 spec.)

The efficiency claim is the one I'd actually budget around. On a physics research task, Anthropic reports Fable 5 **"used a third of the reasoning tokens"** of GPT-5.5 and **"got nearly to where GPT-5.5 landed after four days" in just 36 hours.** That's the real version of the "more capable per token" story — not a tidy "low-effort Fable equals high-effort Opus" slogan (which isn't actually in the announcement), but a measured claim that on hard, long reasoning it reaches comparable answers while spending dramatically fewer tokens. If that pattern holds for your workload, it reframes the 2× rate card, because you're paying more per token but burning far fewer of them. I dug into why [the effort level is the single setting that defines Opus 4.8](https://www.mejba.me/blog/claude-opus-4-8-effort-levels-review) — and Fable 5's whole pitch leans on getting more out of each token at a given effort.

One more line from the announcement that I think is the actual thesis: **"The longer and more complex the task, the larger Fable 5's lead over our other models."** Hold that — it's the whole reason the long-horizon section below matters.

I'll be honest about the limit of what I'm telling you here: every capability number above is Anthropic's own published claim from the [official announcement](https://www.anthropic.com/news/claude-fable-5-mythos-5), not a benchmark I ran in a controlled harness myself. What I *can* tell you from the console is that the model is live, the id is `claude-fable-5`, the pricing is exactly $10/$50, and the responses on ordinary coding work came back noticeably more decisive than Opus 4.8 on the same prompts. Treat the leaderboard claims as the vendor's until your own eval confirms them — which is how you should treat every launch chart, mine included.

That decisiveness is worth unpacking, because it's where the model earns or loses your trust.

## What "Works Longer Without Losing the Thread" Actually Looks Like

The capability Anthropic leans on hardest isn't a benchmark. It's *duration.* Fable 5 is pitched as the most capable Claude for ambitious, long-running agentic work — large migrations, multi-day autonomous sessions, tasks that span millions of tokens without the model drifting off course. Anthropic says it "stays focused across millions of tokens" and "can work autonomously for longer than any previous Claude models."

The demos they chose to show this off are weirder and more telling than the usual benchmark chart. Fable 5 plays Pokémon FireRed with a vision-only harness. It rebuilds a web app's *source code from screenshots alone.* It extracts precise numbers out of detailed scientific figures, autonomously plays Factorio, and designs a 3D-printable model in a browser-based CAD editor. On Slay the Spire — a notoriously punishing roguelike — persistent memory let it reach the final act *three times more often* than without. None of those are "write me a function" tasks. They're long, stateful, vision-driven loops where losing the thread means losing the run. That's the capability being advertised, dressed up as games.

If you've run long agent sessions, you know exactly why that matters, because you've watched the failure mode. The model starts strong, makes real progress, then somewhere around the third or fourth hour it begins to lose the plot — re-litigating decisions it already made, "forgetting" a constraint you set at the top, declaring a half-finished job complete. Opus 4.7 was notorious for that quiet laziness. Opus 4.8 fixed a lot of it. Fable 5's whole pitch is that it fixed more.

Here's what that looks like in a real workflow. Imagine handing an agent a database migration that touches forty tables, with the instruction to preserve every foreign-key relationship and run the test suite after each batch. On a model that loses the thread, you babysit it — checking in every twenty minutes, re-stating constraints, catching the moment it decides table thirty is "probably fine to skip." On a model that holds context across a million tokens, you give the instruction once and review the result. The difference isn't speed. It's how many times you have to interrupt your own day to keep the thing honest.

There's a second-order effect here that the launch materials only gesture at: **agentic loops.** Agents prompting other agents, autonomously, for extended runs. A model that stays coherent across millions of tokens is exactly what makes those loops viable instead of chaotic — and I've written before about how [context, not configuration, is what makes agents actually work](https://www.mejba.me/blog/ai-agent-context-beats-configuration). Fable 5's long-horizon coherence is the missing ingredient that lets a loop run for hours without spiraling.

A word of caution that the hype tends to skip: longer autonomous runs at 2× Opus pricing means **token consumption is now your main risk surface, not capability.** An agent that runs for six hours unsupervised on a $10/$50 model can quietly spend real money. The skill isn't getting it to run long. It's bounding how long, with hard stops and budget ceilings, so a runaway loop doesn't become a runaway invoice.

Which brings me to the one date you should actually write down.

## When Is Claude Fable 5 Free — and When Does the Window Close?

Here's the most actionable fact in this entire launch, and it's buried in the availability section where most people will scroll right past it: **from June 9 through June 22, 2026, Fable 5 is included at no extra cost on Pro, Max, Team, and seat-based Enterprise plans.** If you already pay for one of those, you can use the frontier model right now without touching your API budget or adding usage credits. That's a free two-week test drive on the most capable model Anthropic has ever shipped publicly.

**On June 23, that changes.** After the free window, continued Fable 5 access on those subscription plans shifts to **usage credits — token-based billing on top of your plan.** Anthropic has said it aims to bring Fable 5 back as a standard, included plan feature later on (and that it'll extend the free window if capacity allows), but there's no firm date for that. So the honest read is: free now, paid after the 22nd, included-again at some unscheduled point in the future.

The practical takeaway is simple. If you're on a paid Claude plan, the cheapest possible way to form a real opinion about Fable 5 is to run your hardest task *before June 22.* After that, the same experiment costs credits. This is the rare AI-launch deadline that's genuinely worth acting on rather than ignoring — not artificial scarcity, just a billing change with a date on it.

If you're trying to actually put Fable 5 to work during that window instead of just reading about it, here's the order I'd do it in.

## How to Test Claude Fable 5 Without Wasting Money

You don't need a grand plan. You need a controlled first contact that tells you whether the upgrade is worth it for *your* work. Here's the sequence I'd run.

**Step 1 — Pin the model id explicitly.** In the API, set the model to `claude-fable-5`. Don't rely on a "latest" alias; you want to know exactly what you're billing. In Claude Code, select it from the model picker. Confirm the rate you're being charged is $10 input / $50 output before you run anything expensive — a quick sanity check against a surprise.

**Step 2 — Run a head-to-head on a task you already know.** Take a coding task you've recently done with Opus 4.8 — a real one, not a toy — and run it again on Fable 5 with the *same prompt* and the same effort setting. The point isn't to learn the task. It's to feel the delta on something you can judge instantly. Watch two things at once: the quality of the answer *and* the token count it burned to get there. The efficiency story — comparable answers for fewer reasoning tokens — only matters if it shows up on your own work, so measure both, not just one.

**Step 3 — Test the long-horizon claim on purpose.** Give it something genuinely big — a migration, a multi-file refactor, a feature that spans the codebase. Set a token budget. Then watch *where* it would normally have drifted on an older model and see if it holds. This is the capability you're actually paying the premium for, so test it directly instead of assuming it.

**Pro tip:** Turn on prompt caching before you do anything at volume. Anthropic offers up to a 90% discount on cached input tokens, and when you're re-sending the same large codebase every turn across a long agentic run, that discount is the difference between Fable 5 being affordable and being a luxury. People who skip caching and then complain about the bill are measuring the wrong model.

**Step 4 — Try to trip the safety net, knowingly.** Ask it something in the gated zones — a pointed offensive-security question, say — and watch the response quality shift as Opus 4.8 quietly takes over. Knowing what that handoff *feels like* matters, because if your real work lives anywhere near those zones, you need to recognize when you've silently dropped off the frontier model mid-session.

If you'd rather have someone architect and run these agentic workflows for you — wiring up the budget guards, the eval harness, the long-running orchestration — that's the kind of build I take on. You can see my work at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

Once you've run those four steps, you'll have a real opinion. But there are two things I got wrong in my own first day with it that are worth flagging before you do.

## What I Got Wrong About Fable 5 in the First Hour

I assumed "twice the price of Opus" meant "twice the bill." That was lazy thinking, and it's the most common mistake I'm watching people make.

Price-per-token and total-cost-of-task are different numbers. Anthropic's own physics example makes the point: a third of the reasoning tokens to reach a near-equivalent result. If that token-efficiency pattern holds for your workload, you burn dramatically fewer tokens reaching the same answer — which can make Fable 5 *cheaper per finished task* than Opus 4.8 even at double the rate card. I almost wrote it off on the sticker price alone. Run the per-task math before you decide, not the per-token math.

The second thing I got wrong: I expected the safety routing to be annoying, the way refusals usually are. It isn't, for normal work — because normal work doesn't go near the gated zones. The friction I braced for never showed up across a full day of ordinary coding. That's either reassuring or quietly concerning depending on your temperament, and honestly, I think it should be a little of both. A frontier model whose safety layer is invisible 95% of the time is a frontier model most people will forget is gated at all.

Here's the limitation nobody selling you on Fable 5 will lead with: **the genuinely novel cybersecurity capability — the thing that made Mythos famous — is the one thing the public model specifically won't give you.** Fable 5 is Mythos with the most dangerous door locked. If your interest in this family was the cyber frontier, the public release is the one model that routes you *away* from exactly that. That's not a bug. It's the entire design. But it means a lot of the breathless "Mythos is public now" coverage is technically true and practically misleading.

So who is this actually for? Let me be specific.

## Who Should Switch to Fable 5 — and Who Shouldn't

Switch if you run **long, autonomous, high-stakes coding work** — large migrations, multi-day agent sessions, anything where a model losing the thread costs you real hours. The duration improvement is the headline feature and it's the one that justifies the premium. This is the model for the person who's tired of babysitting agents.

Switch if you do **heavy analytical or knowledge work** — the kind of multi-step reasoning behind that 90% analytics result. If Fable 5 really is the first model past 90% on Anthropic's core analytics benchmark, that capability shows up in research synthesis, data analysis, finance work (it tops Hebbia's Finance Benchmark too), and complex multi-domain reasoning — not just code. Anthropic also reports something stranger from its own labs: internal protein-design experts accelerated parts of the drug-design process roughly 10×, and scientists preferred the model's novel molecular-biology hypotheses over Opus-class output about 80% of the time — with one hypothesis independently corroborated by another lab. Treat those as vendor claims, but they hint at where the real frontier of this model lives.

**Don't** switch your default for everyday tasks where Opus 4.8 already nails it. Quick edits, short conversations, routine generation — you're paying double for headroom you won't use, and the per-token math stops being kind when the tasks are small and you're not leaning on caching or long context.

**Don't** assume you're getting Mythos. You're getting the public, gated sibling. If you genuinely need the lifted-safeguard cyber capabilities, the only door is Project Glasswing, and that door is closed to almost everyone reading this.

For teams trying to make this call across a whole engineering org rather than one workflow, the model-selection question gets genuinely complicated — and getting it wrong at scale is expensive. That's the kind of architecture decision [Ramlit handles for teams evaluating AI-assisted development](https://www.ramlit.com/services): which model for which workload, with the cost guardrails built in from the start.

Step back and the strategy comes into focus. Anthropic took one frontier model and shipped it as two products: a public one with a thin, model-swap safety net for the 95% of work that's harmless, and a locked one for the defenders who need the sharp edge. They more than halved the price on the way out the door. And they timed the public launch days after warning that AI is getting dangerously capable — releasing the proof of their own warning, with the most dangerous capability held back behind a vetting gate.

That's not a contradiction. That's the bet: that the way to handle a model this powerful isn't to keep it locked away, but to give the world the 95% that's useful and reserve the 5% that's dangerous for people you've checked. Whether that bet holds is the most important open question in AI right now — and as of June 9, it's no longer hypothetical. It's a dropdown in your console.

So here's the thing to actually do before June 22, while it's still free on your paid plan: open Claude Code or your API console, pin `claude-fable-5`, and run one real task you've already done on Opus 4.8. Not a benchmark. Not a demo. A task you can judge. After the 22nd that same experiment costs usage credits, so the cheapest window to form a real opinion is open right now and closing. The frontier showed up quietly this time. The only way to know what it's worth is to call it yourself — and the clock on doing it for free is already running.

## Frequently Asked Questions

### What is Claude Fable 5?
Claude Fable 5 is Anthropic's first publicly available Mythos-class model, launched June 9, 2026, with state-of-the-art benchmark performance. It's the same underlying model as Mythos 5 but with safety classifiers that route cybersecurity, biology, and model-distillation queries to Claude Opus 4.8 instead. For the full breakdown, see the launch section above.

### How much does Claude Fable 5 cost?
Claude Fable 5 costs $10 per million input tokens and $50 per million output tokens — double Opus 4.8's $5/$25 rate, but less than half the price of the April Mythos Preview. Prompt caching can discount cached input by up to 90%, which materially changes the math on long-context work.

### What's the difference between Claude Fable 5 and Mythos 5?
Fable 5 and Mythos 5 are the same core model; the only difference is the safety layer. Fable 5 (public) routes dangerous queries to Opus 4.8, while Mythos 5 has those safeguards lifted and is restricted to vetted Project Glasswing cyberdefenders. See "The Safety Net Is a Different Model Entirely" above.

### Is Claude Fable 5 better than Opus 4.8 for coding?
Yes — Anthropic reports Fable 5 scores highest among frontier models on Cognition's FrontierCode software-engineering eval, even at medium effort. Its biggest practical advantage is staying coherent across long, autonomous, million-token sessions where older models lose the thread.

### Is Claude Fable 5 free right now?
Fable 5 is included at no extra cost on Pro, Max, Team, and seat-based Enterprise plans from June 9 through June 22, 2026. After June 23 it requires usage credits (token-based billing). Anthropic aims to restore it as a standard plan feature later, but hasn't set a date. See "When Is Claude Fable 5 Free" above.

### Can I use Claude Mythos 5?
Almost certainly not. Mythos 5 is restricted to Project Glasswing — Anthropic's vetted program for cyberdefenders and critical-infrastructure providers. The public model is Claude Fable 5, which deliberately routes you away from the lifted-safeguard cyber capabilities Mythos is known for.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
