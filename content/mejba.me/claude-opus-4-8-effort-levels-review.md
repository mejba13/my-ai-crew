**BRAND:** mejba.me
**TITLE:** Claude Opus 4.8: The One Setting That Decides It
**META TITLE:** Claude Opus 4.8 Review: Effort Levels Tested in Code
**SLUG:** claude-opus-4-8-effort-levels-review
**PRIMARY KEYWORD:** Claude Opus 4.8
**META DESCRIPTION:** I've run Claude Opus 4.8 since launch day. The real verdict beyond the benchmark chart, plus the one effort level setting that decides if you love it.
**TAGS:** Claude Opus 4.8, Claude Code, AI Models, Effort Levels, Model Review

---

The thing that sold me on Claude Opus 4.8 wasn't the benchmark chart. It was a refactor I'd been dreading.

I had a Laravel service class that had grown into a 600-line monster over four months of feature creep — the kind of file where you change one method and three unrelated tests turn red. Back on Opus 4.7, I'd tried to get the model to untangle it twice. Both times it gave up halfway, declared the job "substantially complete," and left me with a half-extracted trait and a broken test suite. Classic 4.7. Confident, then quietly lazy.

On the morning of May 28, the day Claude Opus 4.8 dropped, I pointed it at the same file. Same prompt. Same repo. I bumped the effort level to **max**, hit enter, and went to make coffee.

When I came back, it had extracted three cohesive classes, rewritten the bindings in the service provider, updated every test, run the suite, found two genuine edge cases it had introduced, and fixed them — without asking. Then it told me, plainly, "I'm reasonably confident in the extraction, but I didn't touch the caching layer because the original behavior there was ambiguous and I didn't want to guess." That last sentence is the whole story of this release. Not just that it finished the job. That it told me exactly where it *didn't*.

I've now been running Opus 4.8 as my daily driver for over a week — client work, this blog's content pipeline, a half-finished SaaS side project. This is the real-world verdict beyond Anthropic's chart, and the one setting that decides whether you walk away loving this model or cursing it.

## What Anthropic Actually Shipped on May 28

Claude Opus 4.8 went live on May 28, 2026, building directly on Opus 4.7. Anthropic's own framing in the [official announcement](https://www.anthropic.com/news/claude-opus-4-8) is unusually restrained: it builds on 4.7 with "sharper judgment, more honesty about its own progress, and the ability to work independently for longer than its predecessors."

Two practical things matter before we get into the model itself.

First, **the price didn't move.** Opus 4.8 shipped the same day at the same per-token cost as 4.7 — $5 per million input tokens and $25 per million output tokens at standard speed. That sounds boring until you've lived through enough model launches to know the usual pattern is "smarter model, fatter bill." Not this time. Anthropic also made fast mode cheaper. And there's a quieter efficiency win buried in the docs: high effort on 4.8 burns roughly the same tokens on a coding task that the old xhigh setting burned on 4.7 — while scoring higher. You're getting more thinking per token, not just more thinking per dollar.

Second, **Claude Code rate limits went up.** Anthropic raised the limits specifically to accommodate higher token usage across the new effort levels — which is a strong tell about how this model is meant to be driven. They expect you to spend more tokens on hard tasks. They built the headroom in. If you've followed how [Anthropic doubled Claude Code rate limits earlier this year](https://www.mejba.me/blog/claude-code-rate-limits-doubled-spacex), this is the same trajectory: more compute pushed toward the people actually shipping with it.

So the headline isn't "Opus 4.8 is a bit smarter." It's "Opus 4.8 is smarter, costs the same, and gives you a new dial to control how hard it thinks." That dial is the entire game. We'll get to it. First, let's deal with the chart, because you've already seen it and you've got questions.

## The Benchmark Numbers — Including the One It Loses

Here's the comparison Anthropic published, straight from the announcement. I'm reproducing the exact figures because the gaps tell you more than the headline.

| Benchmark | Opus 4.8 | Opus 4.7 | GPT-5.5 | Gemini 3.1 Pro |
|---|---|---|---|---|
| Agentic coding (SWE-Bench Pro) | **69.2%** | 64.3% | 58.6% | 54.2% |
| Agentic terminal coding (Terminal-Bench 2.1) | 74.6% | 66.1% | **78.2%** | 70.3% |
| Multidisciplinary reasoning (Humanity's Last Exam, no tools) | **49.8%** | 46.9% | 41.4% | 44.4% |
| Multidisciplinary reasoning (with tools) | **57.9%** | 54.7% | 52.2% | 51.4% |
| Agentic computer use (OSWorld-Verified) | **83.4%** | 82.8% | 78.7% | 76.2% |
| Knowledge work (GDPval-AA) | **1890** | 1753 | 1769 | 1314 |
| Agentic financial analysis (Finance Agent v2) | **53.9%** | 51.5% | 51.8% | 43.0% |

Look at the SWE-Bench Pro jump: 64.3% to 69.2%. Nearly five points of agentic coding gain in a point release, while GPT-5.5 sits at 58.6% and Gemini 3.1 Pro trails at 54.2%. That's not a rounding-error upgrade. That's the difference between a model that finishes a multi-file change and one that stalls.

The reasoning numbers move in the same direction. Humanity's Last Exam without tools climbs from 46.9% to 49.8%, and with tools to 57.9% — both clear leads. Knowledge work on GDPval-AA leaps from 1753 to 1890, which on that scale is a meaningful margin over GPT-5.5's 1769 and miles ahead of Gemini's 1314.

Now the honest part. **Opus 4.8 does not win everywhere.**

On agentic terminal coding — Terminal-Bench 2.1 — GPT-5.5 still beats it, 78.2% to 74.6%. That's a real loss, not a margin of error, and I'd be lying if I pretended otherwise. If your workflow is heavily terminal-driven — long chains of shell commands, CI orchestration, raw bash agentic loops — GPT-5.5 and Codex still have an edge there. I ran both side by side on the same repo for a few days, and the gap is visible: Codex is just a little more surefooted when the entire task lives in the terminal. I've written before about [running Claude Code and Codex side by side in the same repo](https://www.mejba.me/blog/claude-code-codex-side-by-side-same-repo), and 4.8 narrows that terminal gap from where 4.7 sat (66.1%) — but it doesn't close it.

So if you came here for "Opus 4.8 destroys everything," that's not the truth. The truth is: it leads in six of seven categories, often by a lot, and loses one — terminal coding — to GPT-5.5. Keep that asterisk in your head. It'll matter when we talk about which model to reach for.

But here's the thing the chart can't show you. None of these numbers mean anything until you understand the lever that controls them.

## Effort Levels: The Setting That Decides Everything

Opus 4.8's headline feature isn't a benchmark. It's a slider.

Inside Claude Code, you can now set the model's **effort level** across five steps: **low → medium → high (default) → max → ultra.** This is the single most important thing to understand about this release, because it's the difference between the model that aced my refactor and the model that would have flubbed it.

Here's how the levels actually behave:

| Effort | What it does | Token cost | Speed |
|---|---|---|---|
| **Low** | Fast, lightweight responses | Low | Fast |
| **Medium** | Balanced, moderate complexity | Moderate | Moderate |
| **High** (default) | Quality/resource balance | High | Moderate–slow |
| **Max** | Built for genuinely complex tasks | Very high | Slower |
| **Ultra** | Max effort plus **dynamic workflows** for large-scale work | Highest | Slowest |

The mental model that clicked for me: effort level is a *thinking budget*. Crank it up and the model reasons harder, holds more context in working memory, and pushes through tasks it would otherwise abandon. Dial it down and you get fast, cheap answers that are perfectly fine for a lookup but will collapse under a real refactor.

One naming note, because it tripped me up and it'll trip you up too. Anthropic's own documentation describes the underlying reasoning tiers as **low, high (default), and a top "extra"/xhigh** setting — and in Claude Code, the top rung is exposed as **ultracode**, which combines xhigh reasoning with automatic workflow orchestration. The five-rung slider framing (low / medium / high / max / ultra) is the cleaner mental model for day-to-day driving, and that's how I'll talk about it here, but if you go digging in the [official announcement](https://www.anthropic.com/news/claude-opus-4-8) and see "xhigh" and "ultracode," that's the same top-end gear under a different label. Don't let the vocabulary confuse you — it's all the same dial.

That top rung deserves its own paragraph. Ultra (a.k.a. ultracode in Claude Code) is max effort *plus* **dynamic workflows**, where the model plans the work and then spins up parallel sub-agents to chew through large-scale problems on its own. This is the part that genuinely surprised me: dynamic workflows can orchestrate up to **1,000 parallel sub-agents in a single session** (that's the hard cap Anthropic set), and on 4.8 those agents run longer before they tap out. Think "rewrite this module, migrate the tests, update the docs, and verify the build" as a single instruction, with the model writing its own orchestration plan and sequencing the sub-tasks rather than waiting for you to spoon-feed each one. It then verifies its own outputs before reporting back. It's the spiritual successor to the goal-oriented work I covered when [the /for and /goal commands changed my Claude Code flow](https://www.mejba.me/blog/for-goal-claude-code-codex-parallel-build) — except now the orchestration is the model's job, not a command you bolt on. Worth knowing: dynamic workflows shipped in research preview, so expect the occasional rough edge at this tier.

Here's the trap, and I fell into it on day one. **The default is high, and the default is wrong for half your tasks.** Too low, and the model terminates early or reasons weakly — exactly the 4.7 laziness everyone complained about, except now it's a setting you chose, not a flaw you inherited. Too high, and the model overthinks a one-line config lookup, burns 8,000 tokens, and takes 40 seconds to tell you something a grep would've answered.

The skill isn't picking the highest level. The skill is *matching effort to task complexity.* That's the whole game. We'll get tactical about it in a minute.

## How Opus 4.8 Behaves Differently — Beyond the Slider

The effort levels get the headlines, but the model's underlying behavior changed in ways that matter just as much in daily use. After a week, four shifts stand out.

**It reasons before it reaches for tools.** This is the big one. Opus 4.7 had an itchy trigger finger — it would fire off a tool call or spawn a sub-agent before it had actually thought about whether it needed to. 4.8 tries to resolve the problem internally first, and only invokes tools or sub-agents when reasoning alone won't cut it. In practice this means fewer wasteful tool calls, fewer half-baked sub-agent spawns, and a model that feels like it's *thinking* rather than *flailing.*

**It calibrates response length to the task.** Ask 4.8 a quick factual question and you get a short answer. Ask it to analyze an architecture decision and you get the depth the question deserves. 4.7 had one volume knob, stuck on "verbose." 4.8 reads the room.

**It's more honest about its own progress.** Anthropic explicitly tuned for this, and the numbers back it up — they documented roughly a four-fold reduction in unreported code flaws, meaning 4.8 is far less likely to quietly ship a bug and call the job done. Fewer false "done!" claims. Fewer phantom completions where the model swears the tests pass and they don't. The refactor story from the top of this post is the canonical example — it told me what it *hadn't* touched and why. That's the single biggest trust upgrade in this release, and it's the kind of thing no benchmark headline captures.

**The tone warmed up.** Opus 4.7 had a streak of what the community charitably called "sass" — a slightly rigid, occasionally contrarian edge, plus safety overreach that made it refuse or hedge on perfectly reasonable requests. 4.8 is more collaborative. Warmer. It pushes back when it should but doesn't lecture. If you bounced off 4.7's attitude, this alone might bring you back.

There's a quieter shift underneath all four, and it's the one Anthropic leaned on hardest: **goal-orientation is now a core trait, not a patch.** With 4.7, getting the model to work toward an outcome — rather than just satisfying the literal text of your last message — took deliberate prompting and the right commands. 4.8 holds the goal across a long task and steers toward it. When it hits an ambiguous fork, it asks a sharper question instead of guessing or stalling. In a 40-minute autonomous run, that's the difference between coming back to finished work and coming back to a polite excuse. It also makes 4.8 ask *fewer* questions than 4.7 — but the ones it does ask are the ones that actually unblock the work.

Stack those four together with the effort slider and you get a model that doesn't just score higher — it feels fundamentally more like a teammate and less like a tool you have to wrestle. Which brings me to the part you actually came for: how to drive it.

## How I'm Actually Configuring Opus 4.8 (Step by Step)

Benchmarks are theory. Here's the practical setup I've landed on after a week of trial and error. Steal it, then tune it to your own work.

### Step 1: Stop accepting the default effort level

The first thing I did wrong was leave everything on high and wonder why simple tasks felt sluggish and expensive. Don't do that. Before you start a task, ask one question: *how hard is this, really?*

- **Looking something up, renaming a variable, a quick "where is X defined?"** → **low.** It'll answer in seconds for a fraction of the tokens.
- **Writing a focused function, a single-file change, a normal bug fix** → **medium.**
- **Most real feature work, multi-file changes, anything where you'd want a colleague to actually think** → **high** (the default earns its keep here).
- **Gnarly refactors, architecture decisions, debugging something genuinely subtle** → **max.**
- **"Migrate this whole module and verify it" scale work where you want the model to plan and sequence sub-tasks** → **ultra** with dynamic workflows.

**Pro tip:** I keep a sticky note on my monitor that just says "match the dial to the difficulty." It's stupid, and it's saved me more tokens than any clever prompt.

### Step 2: Tell the model what TO do, not what NOT to do

This isn't new advice, but it matters more with 4.8 because the model is so much better at following positive instructions. Instead of "don't break the existing tests," write "keep every existing test green and add new ones for any behavior you change." Positive framing gives the model a target to hit instead of a minefield to avoid. The difference in output quality is real and consistent.

### Step 3: Give it the *why* behind your instructions

The single highest-leverage prompting change I made for 4.8: explain the rationale. Don't just say "use the repository pattern here." Say "use the repository pattern here because we're going to swap the data source from MySQL to an external API next sprint, and I want the calling code untouched when we do."

When 4.8 understands the *why*, its compliance and its judgment both jump. It makes better decisions in the gaps your instructions didn't cover, because it's reasoning toward your actual goal instead of pattern-matching your literal words. This pairs perfectly with the "reasons before it acts" behavior change — give it good reasoning material and it reasons well.

### Step 4: Watch your tokens, especially on max and ultra

Higher effort means more tokens. That's the deal. The raised rate limits give you room, but room isn't infinite. Keep a token tracker running so you can *see* what max and ultra actually cost you on real tasks. The first time I ran a full ultra dynamic-workflow migration, I watched the counter and recalibrated immediately — some of that work didn't need ultra, it needed max with a tighter prompt. If you're serious about cost, my full [Claude Code token management hacks](https://www.mejba.me/blog/claude-code-token-management-hacks) still apply, and they apply *harder* now that you have a dial that can quietly burn through your budget.

### Step 5: Test, don't assume the upgrade helps

Here's the uncomfortable truth nobody puts in launch-day posts: a newer model does not guarantee better results for *your* use case. Opus 4.8 is a clear step up in aggregate. But I've got one specific content-formatting task where 4.7's output was actually cleaner for my pipeline, and I kept that one prompt tuned the old way until I'd properly re-tested it.

Run your real workflows. Compare. Tailor. The model is a starting point, not a finished answer.

If you'd rather have someone set up and tune this whole effort-level workflow for your team's stack rather than learn it the hard way, that's the kind of build I take on — you can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Honest Take: Most "Model Failures" Are Your Fault

Let me say the thing that'll annoy some people. After a week with Opus 4.8 and years of running these models daily, I'm convinced that the majority of "the model is dumb / lazy / broke my code" complaints are not model failures. They're prompting and configuration failures on the user's side.

I watched it happen in real time during the 4.7 era. People would leave the model on aggressive defaults, give it vague one-line instructions with no rationale, no context, no clear target, and then post screenshots complaining the model "gave up." The model didn't give up. It did exactly what an under-specified instruction at the wrong effort level produces.

Opus 4.8 makes this even clearer, because now the effort level is *in your hands.* If you run a hard refactor on low effort, the model will terminate early — and that's not laziness, that's you telling it to think shallowly. If you run a trivial lookup on ultra, it'll overthink and burn tokens — and that's not bloat, that's you cranking the dial past what the task needs.

I'm not letting Anthropic fully off the hook. The early rollout had bugs — a few people hit flaky behavior in the first 48 hours, and I caught one weird sub-agent loop myself before it settled. The community sentiment is mixed-but-positive, which is honest: people love the coding and the warmer collaboration style, some hit rough edges on the rollout. Anthropic iterates from user feedback and logs, so the rough patches tend to smooth out within days. That's been the pattern through 4.6 and 4.7 both.

But the durable lesson stands: **the model is more capable than your defaults are letting it be.** Fix the defaults before you blame the model. That single mindset shift will do more for your output than waiting for 4.9.

## What I'm Actually Seeing in Daily Use

I won't invent precise numbers I can't back up — that's a great way to lose your trust. But I can tell you the consistent patterns from a week of real work across client repos, my content pipeline, and a side project.

On agentic coding tasks, the difference between 4.7 and 4.8 is most obvious on *long* jobs. The kind of multi-file refactor that 4.7 would abandon two-thirds through, 4.8 carries to completion — and that tracks exactly with the SWE-Bench Pro jump from 64.3% to 69.2%. The sustained autonomy is the headline feature in practice. It just keeps going where 4.7 quit.

Token efficiency is the one I'm watching most carefully. Anthropic claims improvement, and the "reasons before reaching for tools" behavior *should* mean fewer wasteful tool calls. In my use it broadly holds — fewer junk tool invocations on medium and high effort. But max and ultra are genuinely expensive, and that's not a regression, that's the design. Efficiency gains at the low-to-mid end, deliberate spend at the high end. Verify it on your own workloads before you trust any blanket "it's cheaper" claim, including mine.

The honesty improvement is the one that's quietly changed how I work. Because 4.8 is more reliable about flagging what it *didn't* finish or *wasn't* sure about, I spend less time double-checking phantom completions. That's a real time saving that won't show up on any chart — and across a week of daily use, it adds up to the model feeling trustworthy in a way 4.7 never quite did. For the bigger picture on how the defaults shifted across these releases, my earlier [Claude Opus 4.7 analysis](https://www.mejba.me/blog/opus-4-7-analysis) still sets the baseline 4.8 is building on.

The expectation to set: this is a genuine step up, but the upgrade you *feel* is proportional to how well you drive it. Leave it on autopilot and you'll get a slightly-better-4.7. Tune the effort levels to your tasks and you'll get a model that finishes work the old one couldn't.

## Should You Switch? My Straight Answer

If you're already on Opus 4.7 in Claude Code: yes, switch now. Same price, real gains, and the effort slider alone is worth the move. There's no reason to stay on 4.7 except inertia.

If you live in the terminal — heavy bash chains, CI orchestration, raw shell agentic loops: stay aware that GPT-5.5 still wins terminal coding 78.2% to 74.6%. For that specific work, keep Codex in your toolkit. For everything else, Opus 4.8 is the stronger pick by a wide margin. Running both isn't hedging — it's just using the right tool for the right job, which is the same conclusion I reached when I [compared GPT-5.5 and Opus 4.7 on identical code](https://www.mejba.me/blog/gpt-5-5-vs-opus-4-7-comparison).

If you're new to all of this: start on Opus 4.8, leave it on high, and only start touching the effort slider once you've felt where high overshoots and undershoots. The dial is powerful, but you have to develop a feel for it.

## Frequently Asked Questions

### What are effort levels in Claude Opus 4.8?
Effort levels are a controllable thinking budget in Claude Code with five settings: low, medium, high (the default), max, and ultra. Higher effort means deeper reasoning, more tokens, and slower responses; lower effort means faster, cheaper, shallower output. Match the level to your task's complexity. See "Effort Levels: The Setting That Decides Everything" above for the full breakdown.

### Is Claude Opus 4.8 better than GPT-5.5?
Opus 4.8 leads GPT-5.5 in six of seven published benchmarks, including agentic coding (69.2% vs 58.6% on SWE-Bench Pro) and reasoning. GPT-5.5 still wins agentic terminal coding, 78.2% to 74.6%. For most coding and reasoning work Opus 4.8 is stronger; for terminal-heavy workflows GPT-5.5 keeps an edge.

### Does Claude Opus 4.8 cost more than Opus 4.7?
No. Opus 4.8 launched on May 28, 2026 at the same per-token price as Opus 4.7. Anthropic also raised Claude Code rate limits to accommodate higher token usage across the new effort levels. Note that max and ultra effort levels consume significantly more tokens per task.

### What are dynamic workflows in Claude Code?
Dynamic workflows are a Claude Code feature, activated at the ultra effort level, where Opus 4.8 plans and orchestrates multiple steps and sub-tasks to solve large-scale problems autonomously. Instead of you sequencing each step, the model breaks the job down and works through it on its own.

### Should I always use the highest effort level?
No — that's the most common mistake. Max and ultra overthink simple tasks and burn tokens unnecessarily, while low effort causes premature termination on hard work. The skill is matching effort to task difficulty: low for lookups, high for real feature work, max for gnarly refactors, ultra for large-scale autonomous jobs.

## The Refactor That Convinced Me

Remember that 600-line Laravel monster from the top of this post? It's been in production for six days now. Three clean classes, full test coverage, and the caching layer Opus 4.8 deliberately refused to touch — because it told me it wasn't sure — turned out to have a subtlety I'd forgotten about myself. If the model had "confidently" rewritten it the way 4.7 would have, it would have shipped a bug.

That's the real upgrade. Not the five points on SWE-Bench Pro. Not the warmer tone. It's a model that knows the edge of its own competence and tells you where it is. Pair that honesty with an effort slider you actually know how to drive, and you've got the first Claude that feels less like a tool you supervise and more like a colleague you trust.

So here's your one thing to do in the next 24 hours: open Claude Code, pick the hardest task on your plate today, set the effort level to max, and give it the *why* behind what you're asking. Then watch what happens when you stop fighting the defaults and start driving the model on purpose.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
