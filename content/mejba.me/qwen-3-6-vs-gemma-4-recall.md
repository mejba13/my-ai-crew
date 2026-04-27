**BRAND:** mejba.me
**TITLE:** Qwen 3.6 vs Gemma 4: The 108K Token Recall Test
**META TITLE:** Qwen 3.6 vs Gemma 4: 108K Token Memory Recall Test
**SLUG:** qwen-3-6-vs-gemma-4-recall
**PRIMARY KEYWORD:** Qwen 3.6 vs Gemma 4
**META DESCRIPTION:** I benchmarked Qwen 3.6, Qwen 3.5, and Gemma 4 on a 336K minified JS file. Here's which local LLM actually remembers what it reads — and why it matters.
**TAGS:** Local AI, Qwen 3.6, Gemma 4, LLM Benchmarks, Reverse Engineering

---

I had a minified JavaScript file sitting on my desk for three weeks. 336 kilobytes. One line of code visually, but roughly 108,000 tokens once a local model swallowed it. Inside that file were 1,300 function definitions — the entire logic for the web interface of an LTE modem I wanted to crawl in real time for radio signal metrics. Signal strength, tower handoffs, SINR, the works.

I needed a local LLM to reverse-engineer it for me. The file was too weird to paste into a cloud model, and honestly, I didn't love the idea of shipping my ISP's firmware guts to someone else's servers. So I did what I always do when I'm stuck — I set up a benchmark.

The first thing I tried was Gemma 4. I'd been running it locally for a month. Loved it for small tasks. Then I asked it to reproduce the opening lines of a specific function from the middle of the file, and the answer came back wrong. Confidently wrong. Made-up function bodies. Phantom return statements. The kind of hallucination that makes you stop trusting the model for anything serious.

That was the moment I realized: context window size and context window *memory* are two completely different things. Gemma 4 technically accepted the 108K tokens. It just couldn't remember what was at token 50,000 by the time it got to token 100,000. The architecture was fighting against me.

So I built a real test. Three models. Sixteen queries. One brutally honest scoring rubric. What came out of it changed which model sits on my local machine for code reverse engineering — probably for the rest of the year.

## Why Minified JavaScript Is the Cruelest Context Test

If you want to find out whether a local LLM actually remembers what you fed it, forget the needle-in-a-haystack tests. Those are too easy. Embedding a single odd sentence in a sea of Wikipedia prose and asking the model to find it is like testing a detective by hiding one banana in a library. Of course they find it. The banana doesn't belong.

Minified JavaScript is worse. It's the detective's nightmare: an entire library where everything is written in the same pale gray font, by the same pseudonymous author, using the same three-letter names. `a`, `b`, `c`. `_0x4f2a`. `function t(e,n,r){}`. Repeat for 1,300 functions. No comments. No whitespace. No structural landmarks.

That's what I loaded into each model. The exact same 336K file, manually beautified so every function started on its own line (a necessary concession — a single-line minified blob makes even the tokenizer unhappy). Then I wrote a script that generated 16 test queries. Each query picked a random function from the file and asked the model a surgical question:

> Reproduce, verbatim, the first 20 lines of code that appear immediately after the opening brace of function `[name]`.

This is a positional memory recall task disguised as a simple lookup. The model can't just "understand" the code and guess — it has to remember, byte for byte, what was at a specific location in a context that's over 100K tokens long. Miss a variable name, hallucinate a return statement, skip a closing bracket, and you lose the point.

I scored each of the 16 runs on four axes: how many lines matched exactly, how many lines were missing entirely, how many were hallucinated (invented code that didn't exist in the source), and whether the model eventually collapsed into circular reasoning loops.

The results surprised me. Two of them I expected. One of them genuinely didn't.

## The Benchmark Setup — And Why Every Detail Mattered

Before I get to the scorecard, you should know exactly how I ran this, because the details change the outcome more than the model choice in some cases.

All three models ran locally via LM Studio, with 8-bit KV cache quantization enabled on every run. That last detail matters. The KV cache is the running transcript a model keeps in memory as it reads your input — every token it's seen so far, encoded as key and value vectors. At full FP16 precision, a 108K token context can eat 20+ GB of VRAM just for the cache, never mind the model weights themselves. Quantize the cache to 8-bit (Q8_0 in llama.cpp parlance) and you cut that memory roughly in half, which is the only way I could fit these contexts on a single consumer GPU.

The fair question is whether 8-bit KV cache ruins quality. The short answer: for everything I tested, it doesn't. [vLLM's documentation](https://docs.vllm.ai/en/latest/features/quantization/quantized_kvcache/) and multiple benchmark threads in the llama.cpp repo confirm what I observed — 8-bit KV quantization is effectively a free memory win. You roughly halve the cache footprint with no perceptible quality degradation. I ran a few of the queries at FP16 cache on a bigger machine to spot-check, and the answers were identical.

What did vary between runs was the model build itself. I quickly learned that the same "Qwen 3.5" or "Gemma 4" label hides a wide variance in quality depending on who quantized the weights.

For each model, I tried multiple builds:

- **Gemma 4** — Unsloth's Q4_K_XL quantization, and the LM Studio community's K_M build
- **Qwen 3.5** — LM Studio Community build and the Unsloth variant
- **Qwen 3.6** — LM Studio's build running on llama.cpp's latest engine

Same model weights at the foundation. Different quantization recipes, different imatrix calibration data, different packaging. The differences were not subtle.

## The Scoreboard — What Actually Happened Across 16 Queries

Here's the full results table. Read it slowly. The numbers tell a story about architecture that's more interesting than the raw scores.

| Model | Build | Functions Passed (/16) | Lines Correct | Lines Missing | Hallucinations |
|---|---|---|---|---|---|
| Gemma 4 | Unsloth Q4_K_XL | 6 | 163 | 132 | Multiple |
| Gemma 4 | LM Studio K_M | 2 | — | — | Significant |
| Qwen 3.5 | LM Studio Community | 11 | 245 | 50 | Few |
| Qwen 3.5 | Unsloth | Fewer passes | — | — | — |
| Qwen 3.6 | LM Studio + llama.cpp | 15 | 283 | 9 | 2 lines |

Fifteen out of sixteen for Qwen 3.6. Nine missing lines total, across the entire 16-query gauntlet. Two hallucinated lines — I'll get to those later. Compare that to Gemma 4's best run: six out of sixteen passes, 132 lines missing, multiple hallucinations per function.

That gap isn't a "new model is slightly better" gap. That's an architectural generation gap. And once you understand why, the entire local-LLM picture starts looking different.

But before we get to the architecture, there's one result that deserves its own section — because it almost made me throw the whole benchmark out.

## The Gemma 4 Failure Mode That Looked Like Genius

On query six of the Gemma 4 run, something strange happened. The model returned a response that looked perfect at first glance. The first few lines matched the source. The variable names were right. Then, around line eight, it started inventing.

Not sloppy inventing. Smart inventing. The hallucinated lines fit the function's apparent purpose. They used the right variable names from elsewhere in the file. They returned plausible values. If you didn't have the ground truth open next to the response, you'd skim it and move on. You'd ship code based on it. You'd be wrong.

This is the Gemma 4 failure mode that concerns me most, and it has a specific architectural cause. Gemma's architecture — inherited from Gemma 3 and continuing in Gemma 4 — uses an aggressive sliding window attention scheme for most of its layers. According to [Google's Gemma 3 technical report](https://arxiv.org/html/2503.19786v1), five out of every six layers in the model are "local" attention layers with a fixed 1024-token look-back window. Only every sixth layer is a "global" layer that can attend to the full context.

In plain English: when Gemma 4 is generating token 60,000 of its response, the majority of its reasoning is only looking back at the previous 1024 tokens. Every 6th layer can reach further, but the bulk of the model's thinking is stuck in a tiny local window. For most tasks — summarization, chat, short code — this is fine. [The memory savings are enormous](https://learnopencv.com/gemma-3/), trimming KV cache from ~60% of model compute to around 15%. Google designed it that way on purpose.

But when you need the model to recall something specific from 90,000 tokens ago, the architecture works against you. The local layers don't have access. The global layers are doing most of the long-range heavy lifting alone, and they can't hold every detail. So the model fills in the gaps with what *seems* right based on nearby context. That's where the hallucinations come from — they're not random. They're architectural completions of a memory the model doesn't actually have.

Once you see it, you can't unsee it. Gemma 4 wasn't being careless. It was doing exactly what its architecture was optimized for, on a task its architecture was never designed for.

## Why Qwen 3.5 Was Already a Generational Leap

When I ran Qwen 3.5 against the same queries, the scoreboard jumped. Eleven passes out of sixteen, 245 correct lines, only 50 missing. The hallucinations dropped from "multiple per function" to "a handful across the whole run."

The reason sits in one acronym: gated DeltaNet.

DeltaNet is a linear attention variant — meaning its compute cost scales linearly with context length instead of quadratically, the way vanilla transformer attention does. That's not new. Linear attention has been around for years. The "gated" part is what matters. [As the Qwen team's documentation explains](https://github.com/QwenLM/Qwen3.6), gated DeltaNet adds learned gates that let the model tune how much of its past state to keep and how aggressively to write new information into the state. When context shifts, old information gets attenuated. When something's still relevant, it stays active in the state.

That last sentence is what separated Qwen 3.5 from Gemma 4 on my test. Gemma's sliding window throws everything older than 1024 tokens out of the local layers unconditionally. Qwen's gated DeltaNet decides what to keep — and critically, it can decide to keep a function definition from 90,000 tokens ago if later queries depend on it.

On the queries where Qwen 3.5 failed, the pattern was consistent: large return statements with lots of concatenated object properties. The function body would be mostly correct, then somewhere around a `return { ... }` block, lines would start dropping. The model was deciding that deeply nested object properties weren't worth keeping in state as context grew. Sometimes it was right. Sometimes it wasn't.

Eleven out of sixteen is a massive jump from six. But it's not fifteen. And the gap between eleven and fifteen is where Qwen 3.6 lives.

## What Qwen 3.6 Got Right — And The Two Lines It Got Wrong

Running the same 16 queries through Qwen 3.6 felt different from the first query. The first function I tested had a gnarly return statement spanning 14 lines with chained method calls and nested ternaries. Qwen 3.5 had missed seven of those lines. Qwen 3.6 reproduced all 20 lines exactly. Byte for byte. Closing brackets in the right places. Semicolons where they belonged.

I tried a harder one. Function with 1,100 tokens between the opening brace and line 20. Qwen 3.6 nailed it.

I started pulling functions from the deepest part of the file, 95,000 tokens into the context. Perfect recall. Again. Again.

Fifteen out of sixteen. The one it missed wasn't even a total miss — it got 18 of 20 lines right, and the two lines it got wrong weren't hallucinations in the Gemma 4 sense. They were abbreviated. The source had a particularly verbose error-handling block with three nearly identical `throw new Error(...)` lines. Qwen 3.6 returned two of them and appeared to consolidate the third, producing a line that wasn't in the source but also wasn't wrong in spirit.

That's a different kind of error. Not "I don't remember, so I made something up." More like "I remembered, but I compressed." For my reverse engineering use case, that's still a fail — I need the exact bytes to match what the modem's JavaScript does. But it's a fundamentally less scary failure than Gemma's confident invention.

The architecture behind Qwen 3.6 is an evolution of what Qwen 3.5 introduced. The 35B-A3B open-weight variant, [according to the model's technical writeup](https://lilting.ch/en/articles/qwen36-35b-a3b-agentic-coding-moe-hybrid), pairs gated DeltaNet with Mixture-of-Experts routing, using a 4:1 ratio of DeltaNet layers to standard attention layers across 40 total transformer blocks. Three gated DeltaNet layers followed by one gated attention layer, repeated ten times. That 4:1 ratio gives you the memory and compute efficiency of linear attention while still preserving the sharp local precision of standard attention where it matters.

Here's where I'll save you a wrong assumption: "bigger model equals better recall" isn't what's happening. Qwen 3.6's 35B-A3B variant only activates roughly 3 billion parameters per token. Gemma 4's 27B variant is fully dense — all 27 billion parameters fire on every token. By raw compute, Gemma 4 should have the advantage. It doesn't. Because architecture ate compute for breakfast.

## The Build Quality Rabbit Hole Nobody Warns You About

There's a second story buried in the scorecard that nobody talks about enough: the quantization build you grab matters almost as much as the model you choose.

Look at Gemma 4 again. Unsloth's Q4_K_XL scored 6/16. LM Studio's K_M build scored 2/16. Same base model. Same weights at the foundation. Different quantization pipeline. The delta between those two builds was larger than the delta between Gemma 4 and Qwen 3.5 on some individual queries.

Unsloth's builds tend to use larger imatrix calibration datasets and more careful per-tensor quantization strategies. It shows up on recall-heavy tasks, where the precision you preserve in critical layers maps directly to how much of the context the model can actually use. [llama.cpp's ongoing work on extreme KV cache quantization](https://github.com/ggml-org/llama.cpp/discussions/20969) is showing similar findings in the other direction — the keys are more sensitive than the values, and which layers you quantize aggressively versus conservatively changes what your model remembers.

For Qwen 3.5, the direction flipped. The LM Studio Community build scored 11/16. The Unsloth build was meaningfully less effective on the same queries. Different model families respond differently to different calibration data. There's no universal "Unsloth is better" or "LM Studio is better" — it's per-model, and honestly, per-use-case.

The lesson I took from this: when you're benchmarking a local model and it's underperforming expectations, try another build before you blame the model. I've seen people write off Qwen 3.5 based on an underwhelming Unsloth test, then discover the LM Studio build does exactly what the benchmarks promised. The hardware runs the same. The model ID is the same. The quantization isn't.

If you'd rather skip the build-shopping rabbit hole and have someone set up a local inference stack dialed in for your specific workload, that's part of what I take on through [my Fiverr gigs](https://www.fiverr.com/s/EgxYmWD) — quantization selection, KV cache tuning, and reverse engineering pipelines are the exact kind of work these tests feed into.

## The Exact LM Studio Setup I Used

For anyone who wants to replicate this, here's the configuration that mattered.

**1. LM Studio version.** I was on LM Studio's latest build as of mid-April 2026, which ships with a recent llama.cpp engine. KV cache quantization settings are exposed in the advanced model loading panel — earlier versions of LM Studio didn't expose this, which is worth checking before you assume your cache is quantized. [There's an open LM Studio bug tracker issue](https://github.com/lmstudio-ai/lmstudio-bug-tracker/issues/186) documenting when this went in.

**2. KV cache quantization.** Set both K and V caches to `Q8_0`. This is the "safe" quantization level — roughly half the memory of FP16 with quality effectively identical for everything I threw at it. The edge cases where Q8 degrades quality are narrow and don't show up in a recall test like this.

**3. Context length.** I set the context to 131,072 for all three models. The file consumed roughly 108K tokens. The remaining ~23K was headroom for the query and response. Going tighter risked the model truncating the input silently, which is the failure mode that kills these tests before they start.

**4. Temperature.** Zero. Not 0.1. Not "low." Zero. For a recall test, any randomness is noise. You want the model's most confident best guess, every time.

**5. System prompt.** Minimal. "You are a code analyst. When asked to reproduce code verbatim, output only the requested lines with no commentary or formatting." Any longer system prompt adds tokens to the context the model has to track, and for a test this tight, you want every byte of the KV cache going toward the file.

**6. The input structure.** I pasted the entire 336K beautified JS file into the first user message, followed by the first test query. For subsequent queries, I did *not* restart the context — I asked them sequentially in the same conversation. This matters because it tests whether the model's state can survive multiple queries, which is how you'd actually use it for reverse engineering. You're going to ask about function `t`, then `e`, then `rn`, then 40 more. If the model forgets the file by query 15, it's useless.

Qwen 3.6 didn't forget. Gemma 4 forgot by query 3 in some runs.

## What This Means for Local LLM Reverse Engineering

Here's the practical punchline. If you're doing what I'm doing — using a local model to unwrap minified code, trace API endpoints, reconstruct crawlers from web UI JavaScript — you now have a clear answer about which model is actually fit for purpose.

Gemma 4 is a great local model for chat, short-context summarization, and tasks where the relevant information is nearby. It runs fast. It's tiny. It's polite. If I had to recommend a model for a friend who wanted to replace ChatGPT for everyday questions on a laptop, Gemma 4 would be in my top three. But for deep recall over huge contexts? It's not ready, and the architecture is why — not the training, not the size, not the quantization. It's a design choice Google made about memory efficiency that trades off against what I need.

Qwen 3.5 is the sweet spot for most people with serious local AI needs. Eleven out of sixteen on a brutal recall test is *usable*. If you're running quantized on modest hardware and can't fit the 3.6 variants you need, Qwen 3.5 will get your work done with a bit of verification.

Qwen 3.6 is the current local pick for complex codebase understanding. Fifteen out of sixteen, with the one miss being a compression rather than a hallucination, is the kind of result that makes a workflow viable. You still need to verify — you always need to verify — but the verification pass is now "double-check the output" instead of "rewrite everything the model produced."

For my LTE modem crawler, I'm going forward with Qwen 3.6. The next session I run will feed it the same 336K file, ask it to identify the specific function that pulls radio strength metrics from the modem's WebSocket feed, and ask it to write me a Python crawler that speaks the same protocol. I'll write that up once the crawler's working. If it fails, you'll hear about that too.

One more thing worth calling out before the end: this is a snapshot of April 2026. Gemma 4's architecture was a deliberate choice that traded recall for efficiency, and there's no technical reason Google couldn't ship a Gemma 4.x variant with a different layer ratio next month. Qwen's team could tune their gated DeltaNet ratio in the next release. Local LLM performance on long-context tasks is changing faster than most benchmark leaderboards can track. The numbers in this post will age. The *method* for generating these numbers won't — the positional recall test, the real-world minified JS as the input, the build-quality sanity check — those are what you should steal.

## Frequently Asked Questions

### Is Qwen 3.6 better than Gemma 4 for local coding?
For long-context code reverse engineering, Qwen 3.6 significantly outperforms Gemma 4 — 15/16 versus 6/16 on a 108K token recall test. Gemma 4's sliding window attention architecture limits most of its layers to a 1024-token look-back, which hurts deep recall. For short-context chat and summarization, Gemma 4 remains a strong, fast option.

### What is gated DeltaNet and why does it matter?
Gated DeltaNet is a linear attention variant that uses learned gates to selectively retain or attenuate past context. Unlike sliding window attention, it can keep specific older information in state indefinitely if the model deems it relevant. Qwen 3.5 and Qwen 3.6 use gated DeltaNet to achieve strong long-context recall without the quadratic cost of standard attention.

### Does 8-bit KV cache quantization hurt quality?
No, not meaningfully. Q8_0 KV cache quantization roughly halves memory usage versus FP16 with effectively identical output quality on every test I ran. Keys are slightly more sensitive than values, but at 8-bit precision both are well within the noise floor. For 108K+ token contexts on consumer hardware, Q8 KV cache is basically required.

### Why does the quantization build matter so much?
Different quantization pipelines use different imatrix calibration data and per-tensor precision strategies. Unsloth's builds scored higher on Gemma 4 in my tests; LM Studio Community builds scored higher on Qwen 3.5. Same weights, different packaging, meaningfully different recall performance. Always test two builds before writing off a model.

### Can I run these models on a laptop?
Qwen 3.6's 35B-A3B open-weight variant activates only ~3B parameters per token, making it feasible on a 32GB+ unified memory Mac or a 24GB VRAM GPU with Q4 quantization and 8-bit KV cache. Gemma 4's 27B dense variant is heavier per token. For laptops with 16GB RAM, the smaller Qwen 3.5 or Gemma 4 E4B variants are more realistic starting points — see my [Gemma 4 local AI setup guide](https://www.mejba.me/gemma-4-free-local-ai-model-review) for the hardware breakdown.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
