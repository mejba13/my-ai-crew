**BRAND:** mejba.me
**TITLE:** Anthropic vs OpenAI AI Coding War: What I'm Doing Now
**META TITLE:** Anthropic vs OpenAI AI Coding War 2026: My Playbook
**SLUG:** anthropic-vs-openai-ai-coding-war-2026
**PRIMARY KEYWORD:** Anthropic vs OpenAI AI coding war
**META DESCRIPTION:** Anthropic just passed OpenAI in business adoption. Both are throwing free usage at developers. Here's what's really happening and how I'm playing it.
**TAGS:** AI Industry, Claude Code, Codex, AI Pricing, Developer Strategy

---

I had two browser tabs open last Tuesday morning that told me everything I needed to know about where the AI coding tools market is heading.

One was the Ramp AI Index dashboard. April 2026 was the first month in history that Anthropic had passed OpenAI in U.S. business adoption — 34.4% versus 32.3%, a 3.8-point jump for Anthropic and a 2.9-point drop for OpenAI in a single month. The other tab was the OpenAI Codex pricing page, which was quietly running a promotion doubling Codex usage on the $100 tier through May 31, 2026, plus a brand-new "Import other agent setup" tool that scans your machine for Claude Code configs and ports them over in roughly two minutes.

Between those two tabs is the most consequential pricing fight in developer tools since AWS spot instances. And almost nobody I talk to is reading it correctly.

I've been running both Claude Code and Codex daily since February 2026. Same projects. Same prompts. Same deadlines. So when the adoption numbers flipped and both companies started carpet-bombing my inbox with usage upgrades, I didn't see a story about which AI is winning. I saw a textbook free-sample phase — the exact pattern Facebook Ads ran in 2008, Uber ran in 2014, and AWS ran from 2006 to roughly 2015. And I started doing some math on what happens in 12 to 24 months when this phase ends.

This post is what I'd tell a friend over coffee. The adoption flip, the dueling promos, why "adoption ≠ quality," the data moat nobody's talking about, my own real experience with Claude's stability problems, and the provider-agnostic setup I'm running right now to make sure I don't get caught when the pricing inevitably resets upward. I'll also share the comparison table I keep open on my second monitor, because the cost-per-output math has gotten genuinely strange.

Let's start with the number that broke the story.

## The Adoption Flip Nobody Saw Coming

For roughly two years, the enterprise AI conversation had a default shape: OpenAI was the incumbent, Anthropic was the challenger, and the gap was either closing slowly or holding steady depending on who you asked. Then April 2026 happened.

Per Ramp's AI Index — which tracks AI tool spending across more than 50,000 U.S. businesses — Anthropic's share of business adoption climbed to 34.4% in April, up 3.8 points month-over-month. OpenAI dropped to 32.3%, down 2.9 points. That's a 6.7-point swing in a single month, and the first time Anthropic has been ahead since Ramp started tracking the metric.

The bigger picture is even more striking. Menlo Ventures' 2026 enterprise survey put Anthropic at 40% of total enterprise LLM spend, with OpenAI at 27%. In the coding category specifically — which is what matters for anyone reading this post — Anthropic holds an estimated 54% market share against OpenAI's 21%, up from 42% just six months earlier. That growth is being driven almost entirely by Claude Code, which is now generating around $2.5 billion in annualized revenue purely as a terminal tool.

Here's the thing about that number. $2.5 billion in annualized revenue from a CLI is the kind of figure that gets investment bankers excited and makes me deeply suspicious of the pricing model. Because revenue that grows that fast on usage-based subscriptions almost always means one of two things: either the product is genuinely indispensable to a critical mass of professional buyers, or the pricing is currently set well below the eventual sustainable level. Often both.

But before we get into what that means for your wallet — and mine — there's a critical distinction the headlines are missing.

## Adoption Is Not Quality. It Never Was.

When I read the Ramp data, my first reaction was "that tracks with my workflow." My second reaction was "that doesn't tell me Claude Code is better than Codex, and anyone reading it that way is going to make a bad bet."

Adoption is a lagging indicator of a bunch of things. It's a signal that the marketing landed, the developer evangelism worked, the GitHub stars compounded, the YouTube tutorials normalized one workflow over another. It's also, critically, a signal of which tool got into developers' hands first and built habit before the alternatives were comparable. Anthropic shipped Claude Code in February 2025. OpenAI's Codex agent — the modern one, not the original 2021 model — didn't reach feature parity for serious daily use until late 2025.

That eight-to-ten month head start is doing a lot of work in those adoption numbers. It built muscle memory. It built `CLAUDE.md` files across millions of repos. It built the social proof loop where every other tweet about AI coding has a screenshot of Claude Code's purple terminal. None of that means Claude Code is producing better code than Codex on any given task today.

I've run enough side-by-side tests at this point — I wrote up the model-quality split in detail in [my Codex vs Claude Code subscription breakdown](/codex-vs-claude-code-subscription) and the workflow logic in [my two-agent setup](/claude-code-codex-two-agent-workflow) — to tell you the honest answer: the gap depends entirely on the task. Codex's `/goal` command runs autonomous plan-act-test-review loops that genuinely outperform Claude Code on long-horizon backend work. Claude Code's planning, design sensibility, and front-end output still feel sharper to me on most days. Both ship buggy releases. Both have stability issues I'll get to in a minute. Neither is clearly "the right answer" for every developer, every workflow, every codebase.

So when you see "Anthropic passes OpenAI in adoption" and start thinking that means you should consolidate on Claude, remember what that headline is actually measuring. It's measuring who got there first and who got there loudest. Quality and adoption travel together for a while, then diverge — and that's exactly when the smart developers start hedging.

Which brings us to the part that should have your full attention. The promos.

## The Dueling Promos Tell You What's Really Happening

Look at what both companies are doing right now and the strategy reveals itself in about thirty seconds.

OpenAI is offering double Codex usage on the $100/month ChatGPT Pro tier through May 31, 2026 — effectively giving Pro subscribers 10x the Codex usage of the $20 Plus tier during the launch window. They've also shipped a migration tool inside Codex that auto-imports system prompts, custom skills, and chat history from Claude Code with a single click. The intent isn't subtle. Take your habits, your prompts, your `claude.md` files, your muscle memory — and just keep using them, but pointed at our model.

Anthropic, on May 6, 2026, doubled Claude Code's five-hour rate limits across Pro, Max, Team, and seat-based Enterprise plans. Same Pro subscribers, same Max subscribers, same monthly bills — twice the usage starting immediately. No new tier required. No migration friction. Just: here's more, keep coming back.

These aren't goodwill gestures. They're not "we listened to our community." They're the standard playbook for category-defining tech in the user-acquisition phase. Subsidize aggressively, drive adoption to a point where switching costs become real, then unwind the subsidies once the habit is locked in.

I've watched this exact pattern run before. So have you. Let me run the parallels because they matter for what comes next.

- **Facebook Ads, 2008–2012:** CPMs were stunningly cheap. Small business owners I knew were getting customers at $1.50 each. By 2018 those same customer-acquisition slots cost $40–60.
- **Google AdWords, 2002–2007:** Bid floors were minimal, agencies were getting Fortune 500 keywords for pennies. Today those keywords cost $50–$300 per click in competitive verticals.
- **Uber and DoorDash, 2014–2018:** Riders and eaters got $5 fares and free delivery. Drivers got bonuses on top of bonuses. Now: surge pricing, "service fees," "small order fees," and driver pay that's collapsed in real terms.
- **Netflix, 2007–2018:** $7.99/month for unlimited everything, no ads, password sharing tolerated. Today: $24.99 for the premium tier, ads on lower tiers, password sharing cracked down on, content libraries shrinking.
- **AWS, 2006–2015:** Free credits, generous trial tiers, simple pricing. Now: you need a FinOps consultant to read your bill.

Every single one of those companies ran the exact same play. Land the user. Make them dependent. Reset the price upward once they can't easily leave. And the AI coding category is right in the middle of step one and step two of that loop.

So when I see Claude Code doubling my limits and Codex offering me 10x Codex usage at the $100 tier, I don't read "I won the lottery." I read "I have roughly 12 to 24 months to make the most of this before the pricing model becomes something I'll like a lot less than the one I have today."

That's not pessimism. That's pattern recognition. And the deeper you go into the unit economics, the clearer it gets why.

## The $200/Month Engineer Math (And Why It Can't Hold)

Here's the rough math I run in my head whenever I think about AI coding pricing. A senior software engineer in North America costs an employer somewhere between $8,000 and $13,000 per month in salary, before benefits, equipment, and overhead. A mid-level AI developer falls in the $8,100–$12,900 monthly range. A staff-level engineer at a competitive tech company is closer to $16,000 a month base.

Claude Max Pro is $200/month. Codex Pro is $200/month. ChatGPT Pro is $200/month. So for somewhere between 1.3% and 2.5% of a single senior engineer's monthly cost, I'm getting output that — on the right task with the right setup — approaches what that engineer would produce in a similar time window. Not all the time. Not on every task. But often enough that the ratio is genuinely insane on a per-deliverable basis.

That ratio is not stable. It can't be. The model providers are spending billions on compute and research, they have investors with eight-figure quarterly burn to justify, and the only way the unit economics resolve long-term is one of three ways: usage prices go up significantly, tier limits get tighter, or both. Probably both.

What's keeping the price low right now is the second piece of this story, the one almost nobody outside the strategy teams is paying attention to.

## The Data Moat Is the Actual Game

Every prompt you send to Claude Code or Codex isn't just a transaction. It's training signal. Real-world developer workflows, real codebases, real edge cases, real corrections when the model gets it wrong. The conversations Anthropic and OpenAI are subsidizing right now generate exactly the data that makes their next model better than their competitors' next model.

This is the part that flips the whole pricing question on its head. If you're a model provider, the *worst* outcome isn't that you lose money on a $20/month subscriber for a year. The worst outcome is that the $20/month subscriber leaves for a competitor and you lose the data feedback loop. So you keep prices low — even artificially low — for as long as it takes to build a data moat that's expensive enough to recreate that no new entrant can catch up.

Once the moat is locked in, prices can climb. Not because compute got more expensive — compute is actually getting cheaper — but because the *quality* the moat enables justifies the price even at the higher level. By the time a competitor's model is as good as yours, you have two years of refinement they don't have, and the buyers who tried to switch have already discovered that "almost as good" is not "good enough" when production deadlines are on the line.

This is exactly why I think the current pricing window is going to close. The moats are forming right now. Anthropic's 54% coding market share isn't just a vanity number — it's a data flywheel that's going to be very hard to attack in 18 months. OpenAI's Codex migration tool isn't just a convenience feature — it's a data acquisition play that ports your workflow patterns into their training pipeline.

And here's where my own experience starts mattering for the strategy.

## What Actually Happened to Claude Code in March and April

I want to be careful here because Claude Code is a tool I love and have written about more than almost any other in 2026. But I'd be lying if I told you the last 90 days have been smooth.

The Fortune piece from April 24, 2026 laid it out plainly: Anthropic published a technical post-mortem confirming three separate product-layer changes had quietly degraded Claude Code's quality. A March 4 change altered the default reasoning effort from high to medium to address UI latency complaints. A March 26 caching optimization shipped with a critical bug that cleared thinking history on every turn instead of after inactivity. An April 16 verbosity-limit tweak caused a 3% drop in coding quality evaluations.

I felt all three of those changes before I understood why. There was about a six-week stretch starting in early March where I'd run a prompt I'd run a hundred times before and watch Claude Code produce something noticeably weaker than what I was used to. Truncated reasoning. Looser pattern matching. The kind of output where you could tell the model wasn't fully engaged with the problem. Some of my Opus 4.7 runs felt closer to Sonnet outputs from a month earlier.

Independent benchmarks during that window measured Opus 4.6 accuracy dropping from 83.3% to 68.3%, with its ranking falling from #2 to #10 among production coding models. The drop wasn't subtle. The community noticed. Anthropic eventually noticed too, rolled back the changes, and shipped fixes — but the trust hit was real, and a lot of the developer migrations to Codex during April and early May were direct responses to that quality regression.

I share all of that not to dunk on Anthropic — they handled the post-mortem with more transparency than most companies would — but to ground the rest of the strategy conversation in something honest. Claude Code is excellent today. It was meaningfully worse for about six weeks. That kind of drift is going to keep happening, on both providers, because we are in the steepest part of the iteration curve and nobody ships perfect releases at this velocity.

If your entire workflow depends on a single provider not having a bad month, your workflow is fragile. Which is the actual problem I'm trying to solve.

## The Comparison Table I Run My Workflow Against

Here's the side-by-side I keep open. The numbers are as of mid-May 2026. Both providers move fast, so check the official pricing pages before you make a commitment — Codex pricing lives on `developers.openai.com/codex/pricing` and Claude Code limits are documented in Anthropic's help center.

| Dimension | Claude Code (Max, $200/mo) | OpenAI Codex (Pro, $200/mo) |
|---|---|---|
| Promo running now | 2x five-hour rate limits, May 6, 2026 onward | 2x Codex usage on Pro tier, through May 31, 2026 |
| Migration tool | Manual | Auto-import from Claude Code configs |
| Strongest tasks (my experience) | Planning, design sensibility, frontend, complex refactors | Autonomous backend loops, `/goal` long-horizon execution |
| Stability history | Quality regression March–April 2026, since recovered | Steadier in 2026, occasional rate-limit weirdness |
| Coding market share | ~54% per Menlo Ventures | ~21% per Menlo Ventures |
| Tool that ships fastest | Claude Code 2.1 generation | Codex 0.40+ generation |
| Pricing 12 months out (my prediction) | Up 30–60% or tier restructure | Up 30–60% once growth promo ends |
| Best paired with | Codex as adversarial reviewer | Claude Code as planning partner |

The bottom row is the part most people miss. The smart move right now isn't picking a winner. It's running both, treating them as complementary specialists rather than competing generalists, and building your workflow so you can rebalance the load on either side without panicking if one of them has a bad month. I broke down the supervisor-builder pattern in detail in [the two-agent workflow post](/claude-code-codex-two-agent-workflow) and the adversarial review variant in [the Codex plugin pattern](/codex-plugin-claude-code-adversarial-review) — both are worth re-reading in this context.

## The Provider-Agnostic Setup I'm Running Right Now

This is the part where the strategy goes from "interesting analysis" to "what I'm actually doing on Monday morning."

I'm building everything provider-agnostic. That doesn't mean ignoring the differences between Claude Code and Codex — it means structuring my workflows so the human-readable layer (specs, agent definitions, project memory, prompt libraries) lives outside the provider-specific tooling. The Claude Code `CLAUDE.md` and the Codex `AGENTS.md` reference the same source-of-truth project notes. My slash commands have parallel versions on both sides. My evaluation tests run against both providers so I can spot quality drift in 48 hours, not three weeks.

The principle is simple: the model is a commodity, the tool around the model is a temporary advantage, and the workflow is the only thing that's actually mine. I covered the deeper logic of this in [my piece on AI subscription commoditization](/ai-subscription-commoditization-application-layer) — the short version is that you should expect the relative quality of frontier models to converge in 18 months, while application-layer differentiation stays valuable for longer.

Concretely, here's the shape of my current setup:

1. **Project memory in plain Markdown.** Specs, decisions, architectural notes, agent role definitions — all in a single `/docs` folder per project, version-controlled with the code. Both Claude Code and Codex can read it. So can any future provider I bring in.
2. **Parallel agent definitions.** Each specialist agent (planner, reviewer, security auditor, documentation writer) has a Claude Code version and a Codex version, both pointing at the same project memory.
3. **Cross-provider evaluation suite.** Once a week I run the same five reference tasks through both providers and log accuracy, time-to-completion, and token cost. Takes about 40 minutes. It's the single best early-warning system for quality drift on either side.
4. **A third provider in the bench.** I keep DeepSeek or Kimi access live and warm — not because I use them daily, but because if Anthropic and OpenAI both raise prices simultaneously, I want the muscle memory of having shipped real work on an alternative ready to go.
5. **Workflow tools the providers don't control.** Obsidian for memory. Git for state. tmux for orchestration. Anything that depends on a specific provider's CLI becomes a single point of failure I won't accept.

You don't need to do all five at once. You do need to commit, this month, to making your workflow at least one notch less provider-dependent than it is right now. If the answer to "what would I do if Anthropic raised prices 60% next quarter" is "I don't know, I guess I'd just pay it" — that's the gap you're trying to close.

## What I'd Actually Do With This Pricing Window

Here's the playbook I'm running, in the order I'd run it if I were starting from scratch this week.

**Week 1: Maximize the current promos.** If you're already paying for Claude Max, your limits just doubled — use them. Run the workflows you've been throttling. Get the experiments out of your backlog. If you're paying for Codex Plus, look hard at the Pro tier through May 31 — the 10x usage advantage is a real arbitrage window for getting reps in on a tool you might end up depending on.

**Week 2: Run one project on the other side.** Take a project you'd normally run on Claude Code and run it on Codex instead. Or vice versa. Not to migrate — to learn the friction points. You're not optimizing for output here, you're building optionality.

**Week 3: Audit your provider lock-in.** Look at your `CLAUDE.md` files, your custom slash commands, your skills directories. How much of that is portable? How much would you lose if you needed to migrate cold? Anything irreplaceable, get a copy outside the provider's tooling — a plain Markdown export, a Notion mirror, a GitHub repo.

**Week 4: Start the evaluation habit.** Pick five tasks that represent your daily work. Run them on both providers monthly. Keep a simple log. This is the early warning system that lets you redistribute load before you panic.

**Months 2 to 6: Watch the pricing announcements.** When one of them ends their current promo without replacement, that's the first inning of the price reset. When tier limits start getting structured around "intelligent" routing that decides what model you get, that's the second inning. When the $200/month tier stops including the flagship model and gets pushed to a $400 or $500 enterprise plan, that's the third inning. You don't need to predict the timing — you need to be ready to pivot when it lands.

**Months 6 to 24: Treat every promo with suspicion.** If a provider offers you another generous upgrade in late 2026 or 2027, that's not goodwill — that's a sign their internal data is showing churn risk or competitive pressure they need to defuse. Take the upgrade. Don't take it as a signal that the pricing trajectory has changed.

And here's the one I almost forgot. **Read your bills.** Every month. Yes, even when they're small. The price increases in this category are going to come the way they always come: incrementally, buried in plan changes, with new "fair use" definitions that quietly redefine what your tier includes. The developers who survive the next pricing cycle with their margins intact are going to be the ones who notice the changes when they happen, not three months later when the bill spikes.

## The Wider Frame

There's a temptation to read the Anthropic-versus-OpenAI story as a horse race. Who's winning. Who's losing. Whose model is smarter. Whose adoption curve is steeper. The horse race is the least interesting part of the story.

The interesting part is the structural pattern: a foundational technology category, two well-funded duopolists, an active price war disguised as a feature war, a hot data-acquisition phase, and a developer community that's currently getting an extraordinary deal on tools that genuinely make us more productive. That deal is going to end. The shape of the ending is what determines whether you come out of the cycle stronger or weaker.

Anthropic passing OpenAI in adoption isn't a verdict. It's a snapshot of one month in a multi-year transition where the winners and losers haven't been sorted yet, where second and third entrants haven't even joined the conversation yet, and where the actual economic outcome — who pays how much for what — is going to look very different in 2028 than it does today.

The flip everyone is celebrating tells me one thing: we're entering the part of the cycle where strategy beats consumption. The developers who win in 2028 aren't the ones who picked the right model in 2026. They're the ones who built workflows flexible enough that the question stopped mattering.

That window is open right now. Both providers are subsidizing your learning. Both are handing you free reps with their tools. Both are showing you their hand a year before they start playing it for real. Use the gift.

That Tuesday morning when I had those two tabs open, I didn't close them and pick a side. I made a coffee, opened my workflow audit doc, and started writing down every place I was one provider deep when I should have been two. By the time the next pricing announcement hits — and there will be one, probably within ninety days — I want to be the developer who doesn't flinch.

You should want the same thing.

## Frequently Asked Questions

### Did Anthropic really pass OpenAI in business AI adoption in April 2026?
Yes, per the Ramp AI Index released in May 2026. Anthropic reached 34.4% of U.S. business AI adoption (+3.8%) while OpenAI fell to 32.3% (-2.9%), the first month Anthropic has held the lead. The data tracks 50,000+ Ramp business customers, which skews tech-forward, so the broader market lead is likely narrower than the headline number suggests.

### Is Codex better than Claude Code in 2026?
It depends on the task. Codex's autonomous `/goal` loops generally outperform Claude Code on long-horizon backend work, while Claude Code still feels sharper on planning, design, and frontend tasks. Most serious developers I know are running both rather than picking one. See the comparison table above and my full [Codex vs Claude Code subscription breakdown](/codex-vs-claude-code-subscription) for the detail.

### What is the OpenAI Codex promo right now?
OpenAI is doubling Codex usage on the $100/month ChatGPT Pro tier through May 31, 2026, giving Pro subscribers effectively 10x the Codex usage of the $20 Plus tier during the launch window. OpenAI has also shipped a one-click migration tool inside Codex that imports system prompts, custom skills, and chat history from Claude Code.

### How much did Claude Code increase its limits in May 2026?
On May 6, 2026, Anthropic doubled Claude Code's five-hour rate limits across Pro, Max, Team, and seat-based Enterprise plans. The change took effect immediately for existing subscribers with no plan upgrade required.

### Will AI coding tool prices go up in 2026 and 2027?
Almost certainly. The current pricing reflects an aggressive user-acquisition phase that parallels Facebook Ads 2008, Uber 2014, and AWS 2006–2015. Once data moats and switching costs are locked in — likely within 12–24 months — expect tier restructuring, tighter rate limits, and effective price increases of 30–60% at the same usage level.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
