**BRAND:** mejba.me
**TITLE:** Claude Opus 4.7 vs GPT-5.4 vs Gemini 3 Pro for Coding
**META TITLE:** Claude Opus 4.7 vs GPT-5.4 vs Gemini 3 Pro: Coding Pick
**SLUG:** claude-opus-4-7-vs-gpt-5-4-vs-gemini-3-pro
**PRIMARY KEYWORD:** claude opus 4.7 vs gpt-5.4 vs gemini 3 pro
**META DESCRIPTION:** Hands-on comparison of Claude Opus 4.7, GPT-5.4, and Gemini 3 Pro for coding in May 2026. Real prices, benchmarks, and which to pick.
**TAGS:** Claude Opus 4.7, GPT-5.4, Gemini 3 Pro, AI Coding Models, Model Comparison

---

# Claude Opus 4.7 vs GPT-5.4 vs Gemini 3 Pro for Coding

At 11:47 PM on May 6, I had three terminals open and the same prompt ready to paste into all of them. Refactor a 14,000-line Laravel queue worker to use a new dispatch pattern, keep all the existing job classes interface-compatible, write the migration plan, and stop when something looks unsafe. The codebase was not toy code — it is a payment processing pipeline I have been maintaining for a client for two years. The kind of refactor where one missed call site costs you a Sunday morning of incident response.

Terminal one was Claude Code on Opus 4.7. Terminal two was the Codex CLI on GPT-5.4. Terminal three was Gemini Code Assist on Gemini 3 Pro. I started all three at 11:47 PM. I went to bed at 1:08 AM. I woke up at 7 AM and read the diffs over coffee.

The results were not what I expected. Two of them were genuinely usable. One of them I would not ship. The cheapest one finished first. The most expensive one was the only one that flagged the unsafe call site I had quietly buried as a trap.

This post is the version of that night I wish I had read three weeks ago. I have spent the last 21 days running the three frontier coding models — **Claude Opus 4.7**, **GPT-5.4**, and **Gemini 3 Pro** — through the actual coding tasks I do for clients. Not benchmark replays. Not pelican-on-bicycle drawings. Production work. Refactors, agent loops, debugging sessions, full-stack scaffolds, code reviews on PRs from junior engineers.

By the end of this you will know which model belongs in your daily driver slot, which one belongs on escalation, which one is quietly the best price-to-performance pick most teams are missing, and the specific archetypes where each one wins. You will also know what each of them gets wrong, because all three of them get something wrong, and the gap between "this model is amazing" and "this model is amazing *for me*" is the thing nobody is writing about clearly.

## The 60-Second Verdict

If you only read one paragraph: **Claude Opus 4.7 is the highest-quality coder of the three, GPT-5.4 is the best agentic terminal operator and the smartest dollar-for-dollar pick, and Gemini 3 Pro is the most aggressive value play if you live inside 1M-token contexts and care about UI-heavy front-end work**. Opus 4.7 wins SWE-bench Verified (87.6%) and SWE-bench Pro (64.3%) and is the model I trust on multi-file refactors I would actually ship. GPT-5.4 paired with ForgeCode hits 81.8% on Terminal-Bench 2.0 and ships well-shaped solutions to gnarly system bugs at less than half the price. Gemini 3 Pro at $2 per million input tokens up to 200K context is genuinely cheap, has a real WebDev Arena lead, and handles whole-codebase reasoning better than the headline benchmarks suggest. Pick one of these three. If your stack does not have one of them in it by the end of this month, you are leaving capability on the table.

## The Three Contenders, Plainly

**Claude Opus 4.7** is Anthropic's flagship as of April 16, 2026. It kept Opus 4.6's price ($5 input, $25 output per 1M tokens) but added a 1M-token context window at standard pricing, a new tokenizer that uses 1.0 to 1.35x more tokens for the same text, and a behavioral shift toward literal, verbose, self-verifying execution. Cursor measured a 12-point jump on its internal CursorBench (58% to 70%) between 4.6 and 4.7. The headline benchmarks are 87.6% on SWE-bench Verified, 64.3% on SWE-bench Pro, and 69.4% on Terminal-Bench 2.0. It is the most expensive of the three and the best at the kinds of code reviews you would put your name on.

**GPT-5.4** is OpenAI's unified frontier model from March 5, 2026 — the release where OpenAI collapsed coding, reasoning, and computer-use into a single model with five reasoning levels and a 1M-token context. Pricing is $2.50 input, $15 output per 1M tokens. SWE-bench Pro lands at 57.7%, OSWorld at 75%, GDPval at 83%. The Terminal-Bench 2.0 number that gets repeated — 81.8% — is GPT-5.4 plus the ForgeCode harness, not the bare model. GPT-5.4 is the model I reach for when I want a fast, agentic terminal operator that ships. It is also the price point where the dollar-for-dollar argument starts to get serious.

**Gemini 3 Pro** is Google's flagship from November 18, 2025. Pricing is $2 input and $12 output per 1M tokens up to 200K context, then $4 and $18 beyond that — context-tiered, not flat. The 1M-token context window comes standard. SWE-bench Verified hits 76.2%, Terminal-Bench 2.0 lands at 54.2%, but the real distinguishing scores are 2,439 Elo on LiveCodeBench Pro and 1487 Elo on WebDev Arena, where it dominates. Note that Google has since shipped Gemini 3.1 Pro (February 2026) which raises the SWE-bench number to 80.6% — but the model the search results are still naming when developers say "Gemini 3 Pro" is the November 2025 release, and that is the one this comparison is against.

Three models. Three philosophies. Let me show you what each one actually does when you put it in front of real code.

## Claude Opus 4.7: The Code-Review Gold Standard

If you sit in a senior engineer seat and the code your model writes lands on your name in the PR review queue, Opus 4.7 is the model that makes that bargain feel safe. That sentence is not marketing. I have shipped real client code generated by all three of these models in the last three weeks, and Opus 4.7 is the one I have spent the least time correcting after the fact.

The mechanism is the behavioral shift Anthropic landed in 4.7 that nobody has framed cleanly enough yet. Opus 4.7 follows instructions more *literally* than 4.6. It is also more *verbose* about what it is doing and why. And it self-verifies — meaning it will, on its own initiative, run the test it just wrote, read its own output, and self-correct mid-run instead of charging into a confident wrong answer. The downstream effect is that you get fewer "looks right but isn't" diffs. The upstream cost is that vague prompts get rougher results. Anthropic's own community started calling this the "ambiguity tax" — Opus 4.7 will not silently rescue a hand-wavy spec the way 4.6 would.

**Pricing as of May 8, 2026:** $5 per 1M input tokens, $25 per 1M output tokens. Cached system prompts and tool definitions land at $0.50 per 1M tokens — a 90% discount. Batch processing is 50% off. The 1M-token context window is included at standard pricing with no long-context premium. The tokenizer change matters: Opus 4.7 can use up to 35% more tokens for the same fixed text than 4.6, so your real-world spend on identical workloads is meaningfully higher than the unchanged sticker rate suggests. I am running about 18% over my Opus 4.6 monthly burn for the same workload mix. Plan for that.

**The benchmarks that matter for code:** SWE-bench Verified at 87.6% — currently the highest single-model score I have verified in the field. SWE-bench Pro at 64.3%, which is the harder variant that strips out the easier SWE-bench Verified instances and adds longer, more realistic engineering tasks; Opus 4.7 is the best in the world here. Terminal-Bench 2.0 at 69.4% (Anthropic's reported number; tbench.ai's leaderboard clocks 68.54% for the same release window). MCP-Atlas at 77.3% — the best multi-tool orchestration score on any model I have reviewed. CharXiv visual reasoning jumped from 69.1% on 4.6 to 82.1% on 4.7, which matters more than it sounds: computer-use agents that read UIs are now in materially better shape.

**The hands-on test I ran.** Going back to that 14,000-line Laravel refactor from the opening: Opus 4.7 took 47 minutes of wall-clock time and made 73 file-level changes. The diff was clean enough that I could review it in roughly 90 minutes. Two things stood out. First, it caught a deprecated method call in a downstream service I had forgotten about — the trap I had quietly seeded — and stopped to ask before proceeding. Neither of the other two flagged it. Second, the code style matched the existing codebase's conventions almost perfectly, including the unusual way I name protected methods. The subjective gap between "Opus 4.7 wrote this" and "I wrote this" is small enough that I have started using its diffs as code review training material for the junior engineer on my client's team.

**Real strengths.** Multi-file refactors where the call graph is wider than what fits in a single mental load. Code reviews where literal compliance with a spec matters. Long-running agent tasks where mid-run self-correction is worth more than raw speed. Anything where the cost of a wrong answer is meaningfully higher than the cost of a slow answer.

**Real limitations.** It is verbose to a fault. A simple "rename this variable" task will produce a four-paragraph reasoning trace before the diff. The token burn on chatty workloads is real and annoying. The new tokenizer makes that worse. And the literal-instruction shift means you have to write better prompts. Vague specs, abstract goals, and "you know what I mean" briefs will produce technically-correct-but-wrong outputs more often than they did with 4.6. It is also the slowest of the three on simple tasks — first-token latency at high reasoning effort can push past 6 seconds on cold starts.

**Who should NOT pick Opus 4.7.** Cost-sensitive batch workloads where you are processing thousands of jobs that do not need code-review-grade correctness. Anyone running a high-volume code-completion product where median latency is the binding constraint. Junior engineers learning how to prompt — the ambiguity tax will burn you while you are still developing your prompt instincts. For those workloads, run Sonnet 4.6 as a default and route only the hard cases up to Opus 4.7. The two-model routing pattern is the power-user move, and I documented an early version of it in [my Claude Code agent swarm architecture post](https://www.mejba.me/claude-code-agent-swarm-architecture).

## GPT-5.4: The Best Agentic Operator and the Smartest Dollar

Here is the part where I stop being a Claude partisan. GPT-5.4 is the model I reach for first when I am building a coding agent that lives in a terminal and needs to ship answers fast. It is also the model that makes me write the smallest pricing spreadsheets, because at $2.50 input and $15 output, it just costs less to run than Opus while clearing the bar on most coding tasks I throw at it.

GPT-5.4 was OpenAI's first frontier model to land all the specialist capabilities — coding, computer-use, deep reasoning — under one umbrella with user-controllable reasoning levels (`minimal`, `low`, `medium`, `high`, `xhigh`). The reasoning dial matters more than it sounds. On a one-line code formatter task at `low`, you get sub-second responses. On a multi-step refactor at `xhigh`, you get something that looks a lot like Opus 4.7's behavior — slower, more verbose, more careful — at half the price of running Opus.

**Pricing as of May 8, 2026:** $2.50 per 1M input tokens, $15 per 1M output tokens. 1M context window. The OpenAI Batch API gives 50% off for jobs that can wait. Cached input pricing exists but is structured slightly differently than Anthropic's — most of my workloads land somewhere around a 4-6x effective discount on cached system prompts in practice.

**The benchmarks that matter for code:** SWE-bench Pro at 57.7% — meaningfully behind Opus 4.7's 64.3% on that specific test, but the gap shrinks on the easier SWE-bench Verified variant. Terminal-Bench 2.0 has the most interesting story. The bare model number lands lower than Opus, but GPT-5.4 paired with the ForgeCode harness scored 81.8% — the highest verified score I have seen, and the practical signal that GPT-5.4 was *built* for agentic terminal work. OSWorld at 75% (computer use) is genuinely strong. GDPval at 83% (knowledge work) tells you the underlying reasoning is dense.

**The hands-on test I ran.** Same 14,000-line Laravel refactor, same 11:47 PM start. GPT-5.4 finished in 38 minutes — the fastest of the three. The diff size was the smallest, around 60 file-level changes versus Opus 4.7's 73. The code style matched my codebase reasonably well but missed two of my naming conventions. More importantly, it did *not* catch the deprecated method call I had buried as a trap. It also generated a slightly wrong migration plan — the migration was technically valid but skipped a database constraint update that would have caused a deploy failure. The output was 90% of the way there. The 10% gap was the gap between "ship this" and "test this thoroughly first."

That gap matters less in agent loops than it does in single-shot generation. GPT-5.4 in a tight feedback loop — write code, run tests, read output, fix, repeat — closes the gap fast. The Terminal-Bench 2.0 number is real. I built a tiny multi-step debugging agent two weeks ago using GPT-5.4 with a tool for running pytest and a tool for reading log files. The agent solved 14 out of 16 real bugs in a Django project in roughly 22 minutes total. Same agent built on Opus 4.7 solved 15 out of 16 in 41 minutes. Same agent built on Gemini 3 Pro solved 11 out of 16 in 31 minutes. GPT-5.4 is the price-performance sweet spot for that loop.

**Real strengths.** Agentic terminal work — anything that requires tool calling, parallel tool execution, and tight feedback loops. Cost-per-task economics where you are running thousands of similar jobs and Opus pricing would eat your margin. Speed-critical workflows where median latency under 5 seconds matters. Code generation tasks where you have a tight test suite catching the 10% gap. The kind of work I described in my [voice agents post](https://www.mejba.me/gpt-realtime-2-translate-voice-agents) — where you need a model that can plan, call tools, and converse fluidly — sits naturally on GPT-5.4 as well.

**Real limitations.** Single-shot code-review correctness is meaningfully behind Opus 4.7. If you do not have automated tests catching the 10% gap, you will ship bugs. The Terminal-Bench 2.0 81.8% number is *with* the ForgeCode harness — the bare model is lower, and you should not assume bare-model performance hits that mark without scaffolding. And while the model is fast, the `xhigh` reasoning mode pushes first-token latency over 4 seconds on cold starts, similar to Opus.

**Who should NOT pick GPT-5.4.** Workloads where the cost of a missed bug is much higher than the cost of compute — security-critical code, payment systems, anything regulated. For that kind of work, you want Opus 4.7's self-verification and SWE-bench Pro lead. Also: anyone who has built their dev workflow around the Anthropic ecosystem (Claude Code, MCP, Anthropic SDK) — the migration friction usually outweighs the price savings unless you are operating at scale.

## Gemini 3 Pro: The Cheap, Underestimated Long-Context Pick

I went into this comparison expecting to write Gemini 3 Pro off as a distant third. I was wrong, and I want to be honest about why I was wrong, because I think a lot of developers have been making the same mistake.

Gemini 3 Pro is not the best coder of the three. It is meaningfully behind Opus 4.7 on SWE-bench Verified (76.2% vs 87.6%), behind GPT-5.4 on agentic terminal work, and behind both on multi-step debugging in my hands-on tests. None of that is in dispute.

But Gemini 3 Pro has three things going for it that the headlines undersell. First, the price is genuinely the lowest of the three frontier models, especially under 200K context. Second, the WebDev Arena leadership at 1487 Elo is real — for front-end UI work, Gemini 3 Pro generates more visually polished, more interactive components than the other two by a measurable margin. Third, the LiveCodeBench Pro score of 2,439 Elo (nearly 200 points ahead of GPT-5.1) tells you something important: on algorithmic, from-scratch, contest-style problems, Gemini 3 Pro is doing something that the other two are not.

**Pricing as of May 8, 2026:** Context-tiered. Up to 200K input context: $2 per 1M input tokens, $12 per 1M output tokens. Above 200K: $4 input, $18 output. 1M token input context window standard, 64K maximum output tokens. The tiered structure rewards keeping prompts compact — most of my coding workloads run under 200K, which means I am paying $2 input the whole time. That is the cheapest of the three for the bulk of real-world coding tasks.

**The benchmarks that matter for code:** SWE-bench Verified at 76.2%. Terminal-Bench 2.0 at 54.2%. ARC-AGI-2 at 31.1% (3.1 Pro doubled this to 77.1%, but the November 2025 model is the one we are pricing). LiveCodeBench Pro at 2,439 Elo — this is the algorithmic-coding leadership signal. WebDev Arena at 1487 Elo, the highest of any model on front-end UI work. The gap between "Gemini 3 Pro looks weak on SWE-bench" and "Gemini 3 Pro is the best at certain coding tasks" is exactly the gap most comparison articles flatten by averaging benchmark scores.

**The hands-on test I ran.** Two of them, actually, because the Laravel refactor was not Gemini's strong suit and I wanted to be fair.

The Laravel refactor: Gemini 3 Pro completed in 51 minutes with 81 file-level changes. The diff was the noisiest of the three — there were stylistic deviations from the codebase conventions throughout. It did not catch the deprecated method call. The migration plan was incomplete. I would not ship its diff without significant manual cleanup.

The fairer test: a from-scratch React + Tailwind dashboard with five chart types, real-time data binding, dark mode, and responsive breakpoints. Single prompt, single shot, no iterations. Opus 4.7 produced functional code with conservative styling and well-structured components — about 85 minutes to a working dashboard. GPT-5.4 produced functional code that was visually flatter than I wanted — about 60 minutes. Gemini 3 Pro produced a dashboard that looked like a designer had touched it. Color hierarchy that made sense. Animations on chart transitions. A dark mode that actually nailed the contrast on the chart axes. About 70 minutes to working. The visual quality gap was not subtle. WebDev Arena's leaderboard is not lying.

**Real strengths.** Front-end and UI-heavy work where visual polish matters. Algorithmic, contest-style, from-scratch coding problems. Cost-sensitive workloads where the $2/$12 price below 200K context is a big enough advantage to outweigh the SWE-bench gap. Whole-codebase reasoning where 1M context fits and tiered pricing keeps the bill sane. Multi-modal work where the same model needs to understand screenshots, diagrams, and code in one pass.

**Real limitations.** Multi-file refactors with subtle correctness requirements. Anything where literal compliance with conventions matters — Gemini 3 Pro tends to "improve" your code rather than match it. Production code where the SWE-bench Pro gap (Gemini at 54.2%, Opus at 64.3%) translates into ten extra minutes of cleanup per shipped diff. Long-running agentic terminal work where the Terminal-Bench 2.0 gap is real and felt.

**Who should NOT pick Gemini 3 Pro.** Anyone whose primary workload is multi-file refactors on existing codebases. Anyone who needs the strongest single-shot code review. Teams already deeply integrated with Anthropic or OpenAI tooling where the switching cost outweighs the price advantage. The model is genuinely good — but it is good at different things than the other two, and confusing "cheapest" with "best for me" will cost you on the wrong workload.

## Head-to-Head: The Numbers That Matter

| Dimension | Claude Opus 4.7 | GPT-5.4 | Gemini 3 Pro |
|---|---|---|---|
| Release date | Apr 16, 2026 | Mar 5, 2026 | Nov 18, 2025 |
| Input price (per 1M) | $5.00 | $2.50 | $2.00 (≤200K) / $4.00 (>200K) |
| Cached input (per 1M) | $0.50 | discounted | discounted |
| Output price (per 1M) | $25.00 | $15.00 | $12.00 (≤200K) / $18.00 (>200K) |
| Context window | 1M | 1M | 1M |
| Max output tokens | 128K | 100K+ | 64K |
| SWE-bench Verified | 87.6% | ~80%+ (varies by harness) | 76.2% |
| SWE-bench Pro | 64.3% | 57.7% | ~54% |
| Terminal-Bench 2.0 | 69.4% | 81.8% (with ForgeCode harness) | 54.2% |
| Aider Polyglot | strong (Opus family ~89%) | ~88% (GPT-5 high) | not prominent |
| WebDev Arena Elo | competitive | competitive | 1487 (leader) |
| LiveCodeBench Pro Elo | competitive | competitive | 2,439 (leader) |
| Tokenizer change | Yes — 1.0-1.35x more tokens vs 4.6 | No | No |
| Best-fit use case | Code-review-grade refactors | Agentic terminal & cost-balanced workloads | Front-end UI, algorithmic problems, low-cost long context |

A note on the numbers: the SWE-bench Verified score for GPT-5.4 varies meaningfully by harness configuration. OpenAI's reported numbers for GPT-5.4 in March 2026 cluster around the high 70s to low 80s range. The ForgeCode-harnessed Terminal-Bench 2.0 number is the most-cited GPT-5.4 score and the one that drives my recommendation. If you are evaluating models on bare-prompt benchmarks, GPT-5.4 is meaningfully behind Opus 4.7 on the headline SWE-bench Pro variant.

## The Decision Framework: Six Real Archetypes

The right model for *you* depends on the work you actually do. Here are six developer archetypes I have either lived through or coached someone else through, and the model I would pick for each.

**1. The indie hacker shipping a SaaS solo.** Pick GPT-5.4. You need speed, agentic capability, and a cost structure that does not punish fast iteration. The 10% correctness gap behind Opus is closable if you have a reasonable test suite. At $2.50/$15, you can run 50-100 agent loops a day without thinking about the bill. Escalate to Opus 4.7 only when the model is repeatedly failing on a hard task and you need the SWE-bench Pro lead.

**2. The lead engineer running a large-codebase refactor.** Pick Opus 4.7. The 64.3% SWE-bench Pro lead is exactly what you are buying — the ability to ship multi-file refactors with literal compliance to your existing conventions. The price is real but the alternative is two extra days of manual cleanup, and your loaded hourly rate makes that math obvious. Pair Opus 4.7 with cached system prompts and you can keep the cost roughly comparable to GPT-5.4 on tasks where caching applies.

**3. The agent builder shipping a coding agent for end users.** Pick GPT-5.4 as your default. The Terminal-Bench 2.0 81.8% (with harness) is the agentic operator score you actually need. Reasoning effort dial gives you a UX lever — `low` for fast tool calls, `xhigh` for hard reasoning steps, set per-call. Layer in Opus 4.7 as an escalation path when the agent fails twice on the same task. This is the two-model routing pattern that has become the dominant production architecture in 2026.

**4. The cost-sensitive batch processor.** Pick Gemini 3 Pro for jobs that fit under 200K context. At $2 input and $12 output, you are paying ~40% of Opus 4.7's rate on equivalent token volumes. For workloads like code commenting, automated documentation generation, mass code translation, or test generation across thousands of files, the cost delta dwarfs the quality gap. Stay disciplined about the 200K cutoff — the tiered pricing punishes you above that threshold.

**5. The latency-critical product builder.** All three models can hit sub-2-second median response times at low reasoning effort. GPT-5.4 has the edge on cold-start latency in my testing. Gemini 3 Pro is competitive. Opus 4.7 at high reasoning effort is the slowest of the three. If your product is user-facing and median latency under 1 second matters, none of these three are the right answer — drop down to Sonnet 4.6 or GPT-5.4-mini and route hard cases up.

**6. The multi-modal app developer.** Pick Gemini 3 Pro. The native multi-modal handling — text, image, video, audio, code in a single context — is genuinely better than the other two, and the price-to-context ratio is uniquely favorable. The CharXiv jump in Opus 4.7 (69.1% to 82.1%) closes some of the gap on visual reasoning specifically, but the broader multi-modal story is Gemini's home court. If your product is a visual debugging assistant, a UI-aware coding tool, or a screenshot-driven coding agent, Gemini 3 Pro is the natural pick.

## Three Concrete Scenarios Where Each Model Wins

I want to give you scenes, not just rules. Here are three coding tasks I ran in the last three weeks where one model clearly beat the other two and the reason was specific.

**Scenario 1: Migrate a Vue 2 component library to Vue 3 across 47 components — Opus 4.7 wins.** I gave the same task to all three models with the same context (the Vue 2 source, the Vue 3 migration guide, my custom style conventions). Opus 4.7 produced a clean migration with consistent style across all 47 components in roughly 4 hours of agent runtime. Cost: about $34 in tokens. GPT-5.4 produced a migration in 2.5 hours but introduced subtle reactivity bugs in 6 components — the kind that tests would catch but humans might miss. Cost: about $13. Gemini 3 Pro completed in 3 hours but rewrote my custom hooks in a way that broke component contracts in 11 places. Cost: about $11. The Opus 4.7 premium was the correct buy. The dollar gap was small enough to not matter; the quality gap was big enough to dictate the choice.

**Scenario 2: Build a CLI tool from scratch that parses logs, identifies error patterns, and generates a fix-it report — GPT-5.4 wins.** Pure agentic terminal work, no existing codebase, no style conventions to match. GPT-5.4 in a tight loop with shell access produced a working CLI in 22 minutes. The code was idiomatic, the error handling was complete, the output formatting was clean. Opus 4.7 produced a more verbose, more thoroughly-commented version in 41 minutes that did the same thing. Gemini 3 Pro built it in 31 minutes but missed a corner case in the log parsing that needed cleanup. For the kind of work where you are starting fresh and the model needs to make a lot of small judgment calls fast, GPT-5.4's combination of reasoning, speed, and cost was the clearest winner. This pattern is what I was reaching for in the [Claude Code agent build playbook](https://www.mejba.me/build-ai-agent-context-beats-configuration) — same agent loop philosophy, different default model.

**Scenario 3: Generate a marketing landing page with hero, three feature blocks, testimonials, pricing, and footer in React + Tailwind — Gemini 3 Pro wins.** Single-shot, single-prompt, no iterations. Opus 4.7 produced clean, semantically structured HTML with conservative styling — exactly what you would want if a designer was going to touch it next. GPT-5.4 produced functional code that was visually flatter and required Tailwind class cleanup. Gemini 3 Pro produced a page that looked like a real product. Color hierarchy that worked. Subtle animations on hover. Responsive breakpoints that made sense at every viewport. Visual taste that was a category above the other two. For UI-first work where the model's visual output *is* the product, Gemini 3 Pro is the model. The WebDev Arena number is not abstract — it is exactly this experience scaled.

## What I Actually Run Now

For full transparency, here is the model layout I have settled into as of May 8, 2026 after three weeks of testing.

Opus 4.7 is my default for client work involving production code, multi-file refactors, and any code review where my name lands on the PR. I run it with cached system prompts to keep the cost reasonable. I write deliberately specific prompts to dodge the ambiguity tax.

GPT-5.4 is my default for personal projects, prototype builds, and the agent loops I run for content pipeline automation. The cost economics are too favorable to ignore for high-volume work, and the speed-to-correctness tradeoff fits the stage. I escalate to Opus 4.7 the moment a task fails twice in a row.

Gemini 3 Pro is my default for two specific things: front-end work where the visual output is the deliverable, and any task where I am running cheaply against a 1M-token codebase context. I have not made it my daily driver because the SWE-bench Pro gap is real and felt. But I would not be without it for the workloads where it wins.

Sonnet 4.6 still has a seat on the team for fast, low-stakes coding work where Opus 4.7 is overkill and the cost matters. The two-model routing pattern (Sonnet default, Opus on escalation) is still alive — the addition is that GPT-5.4 has joined the rotation as a viable third default for agentic work specifically, and Gemini 3 Pro has joined for UI work specifically.

If you have only had time to integrate one frontier model into your workflow, you are not wrong to default to Opus 4.7. The single-best-coder argument is real. But you are leaving 30-50% cost savings on the table by ignoring the others on the workloads where they shine. That is the gap most developers will close in 2026, one workload at a time.

## Frequently Asked Questions

### Which AI model is best overall for coding in May 2026?

Claude Opus 4.7 is the highest-quality coder of the three frontier models on the headline benchmarks — 87.6% on SWE-bench Verified and 64.3% on SWE-bench Pro, both leading. It is the model I use for production code where correctness matters more than speed. But "best overall" is a misleading question. GPT-5.4 wins on agentic terminal work and price-to-performance, and Gemini 3 Pro wins on UI-heavy front-end work and long-context cost economics. Pick by workload, not by leaderboard average.

### Why does Opus 4.7 cost more in practice than its sticker price suggests?

Opus 4.7 uses a new tokenizer that consumes 1.0 to 1.35x more tokens for the same fixed text compared to Opus 4.6. The per-token rates ($5 input, $25 output per 1M) are unchanged from 4.6, but identical workloads can spend 18-35% more on tokens. The model is also more verbose by design — its self-verification behavior generates more reasoning tokens per task. The fix is cached system prompts (90% discount), batch API (50% off), and explicit prompt design that limits chatty exploration on simple tasks.

### When should I pick a smaller, cheaper model instead of Opus 4.7, GPT-5.4, or Gemini 3 Pro?

Pick smaller models for high-volume, low-stakes workloads where median quality is acceptable and the cost gap matters. Sonnet 4.6, GPT-5.4-mini ($0.40/$1.60 per 1M tokens), and Gemini 3 Flash all serve that role well in 2026. The pattern is two-tier routing: smaller model as default, frontier model on escalation when the smaller one fails twice. For repetitive tasks like code commenting, simple bug fixes, format conversions, and routine documentation, you should not be paying frontier prices.

### Is the 1M-token context window in all three models actually usable for real coding work?

Yes for Opus 4.7 and Gemini 3 Pro, with caveats for GPT-5.4. Opus 4.7 includes the full 1M context at standard pricing — no long-context premium — and the rate-limit ceiling has risen meaningfully on Anthropic's end after the [SpaceX compute deal](https://www.mejba.me/claude-code-rate-limits-doubled-spacex), making 1M-token sessions practical day-to-day. Gemini 3 Pro tiers pricing above 200K (jumping to $4 input and $18 output), which keeps cost controlled but penalizes whole-codebase reasoning. GPT-5.4 supports 1M context at standard pricing too, but in my testing, performance degrades faster past ~600K tokens than the other two.

### Which model is most reliable for long-running agentic coding tasks?

Opus 4.7 is the most reliable on raw correctness over multi-hour agent runs — its self-verification behavior catches its own logical errors mid-run more often than the other two. GPT-5.4 paired with the right harness (ForgeCode being the well-tested option) has the highest Terminal-Bench 2.0 score at 81.8% and is the most efficient on agentic terminal-shaped tasks. The honest answer is that you should run the same task on both, measure correctness vs cost on your specific workload, and pick from data. Generic recommendations break down past a certain agent complexity.

### How does code review quality compare between the three models?

Opus 4.7 is meaningfully ahead on code review quality. The 87.6% SWE-bench Verified and 64.3% SWE-bench Pro scores are exactly what you are paying for here — the ability to read a diff, understand what changed, identify subtle bugs, and explain the trade-offs in language a senior engineer would write. GPT-5.4 is competent on code review but produces shorter, less thorough reasoning traces. Gemini 3 Pro is the weakest of the three for code review specifically — it tends to praise generic style improvements while missing subtle correctness issues. For PR reviews on shipped code, Opus 4.7 is the clear pick.

## Key Takeaways

- **Claude Opus 4.7 wins on code-review-grade correctness** — 87.6% SWE-bench Verified, 64.3% SWE-bench Pro, the highest in the field. Use for production refactors, multi-file changes, and PRs that land on your name.
- **GPT-5.4 wins on agentic terminal work and dollar-for-dollar value** — 81.8% Terminal-Bench 2.0 with ForgeCode, $2.50/$15 pricing, fastest cold-start latency. Use for agent loops, prototypes, and high-volume coding workloads.
- **Gemini 3 Pro wins on UI-heavy front-end work and cheap long-context** — 1487 Elo on WebDev Arena, $2/$12 below 200K context. Use for landing pages, dashboards, and visually-driven coding work.
- **The two-model routing pattern is the production default in 2026** — smaller model (Sonnet 4.6 or GPT-5.4-mini) as daily driver, frontier model on escalation. Single-model workflows are leaving money on the table.
- **Opus 4.7's tokenizer change can increase real-world spend by 18-35%** even though the sticker rate is unchanged from 4.6 — plan for this when budgeting.
- **Benchmarks are a starting point, not a verdict** — the SWE-bench Pro gap, Terminal-Bench 2.0 gap, and WebDev Arena gap each mean something specific. Average them at your peril.
- **Pick by workload, not by leaderboard average** — the right model is the one that best fits the work you actually do, and most teams now run two or three of these in rotation.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
