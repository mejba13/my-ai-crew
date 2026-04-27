**BRAND:** mejba.me
**TITLE:** Forked Subagents in Claude Code: Why I Stopped Using Normal Ones
**META TITLE:** Forked Subagents in Claude Code: A Practitioner's Guide
**SLUG:** forked-subagents-claude-code-anthropic
**PRIMARY KEYWORD:** forked subagents in Claude Code
**META DESCRIPTION:** How forked subagents in Claude Code inherit full context, share the prompt cache, and fix the compression tax I used to pay on long sessions.
**TAGS:** Claude Code, AI Agents, Anthropic, Developer Tools, Context Engineering

---

It was 1:47 AM when I realized I had been debugging the wrong problem for four hours.

I was deep into a long Claude Code session — maybe 180k tokens in — wiring up a payment flow for a client SaaS. I spun up a normal subagent to go review a specific controller and tell me whether my validation logic was actually enforcing what I thought it was. The subagent came back confident. "Looks correct. The guard at line 47 prevents the race condition you described."

Except it didn't. The guard I was worried about wasn't in the controller at all. It was in a middleware the subagent had never seen, because its compressed context summary had flattened that middleware into a line that read, roughly, "project uses standard Laravel middleware stack." The nuance — the specific custom middleware that mattered — got eaten during compression.

That's when I started paying attention to forked subagents in Claude Code. And after two weeks of rewiring how I delegate work inside long sessions, I'm not going back.

This post is what I wish someone had handed me the first day I enabled `/fork`. It's not the press release. It's what actually happened when I tested it on real client work, where the compression tax hurts, and where forked subagents still do the wrong thing.

## The Compression Tax Nobody Talks About

Here's the thing about normal subagents in Claude Code. They don't inherit your conversation. They inherit a *summary* of your conversation — Anthropic's built-in context compression running over whatever you've said so far, compressed down into something small enough to fit into the subagent's own context window alongside its task.

That compression is lossy. Always. It has to be.

The mental model I'd been carrying around was wrong. I thought a subagent was like a colleague I could slack: "hey, quick thing, here's what we've been working on, go check this." But what I was actually doing was more like handing a colleague a one-page executive summary of a three-hour meeting and asking them to weigh in on a decision that hinged on a specific comment someone made at minute 47.

The summary captures the shape. It loses the specifics. And for code work, the specifics are the entire game.

You can see this in the GitHub issues Anthropic's team maintains. There's a live request — [issue #6825](https://github.com/anthropics/claude-code/issues/6825) — asking for configurable inheritance of system prompt and memory for subagents, precisely because the default inheritance behavior is either too much or too little depending on what you're doing. And there's a parallel concern in [issue #4908](https://github.com/anthropics/claude-code/issues/4908) about scoped context passing, which is really just the same problem from a different angle: developers want to control what the subagent actually sees.

The tension sits here: a full context window degrades Claude Code's decision-making quality over time — accuracy drops past roughly 200k tokens in my own testing, and Anthropic's own [context management docs for the 1M window](https://www.mejba.me/blog/claude-code-1m-context-management) acknowledge this cliff. So the main session wants to stay lean. But the subagent starved of context makes bad calls. Compression is the compromise, and compromises always cost you something.

Forked subagents break the trade-off. They inherit the *entire* conversation history from the main session, byte for byte, and they share the prompt cache with that session so you don't pay full retail on the tokens. That's the pitch. The question I spent two weeks answering: does it actually hold up?

## What /fork Actually Does (And Why It's Different)

The `/fork` command spawns a subagent that starts with your complete conversation history loaded into its context window. Not a summary. Not a compressed outline. The actual messages, the actual tool calls, the actual file reads — everything you've accumulated up to the point you forked.

Crucially, the fork shares the prompt cache with the main session. This matters more than it sounds. Without shared caching, a forked subagent would be prohibitively expensive — you'd be paying for the same inherited tokens twice, once in the main session and once in the fork. Anthropic's [context engineering writeup on LMCache](https://blog.lmcache.ai/en/2025/12/23/context-engineering-reuse-pattern-under-the-hood-of-claude-code/) put the prompt reuse rate across Claude Code phases at 92%, and for ReAct-style subagent loops it's even higher. Forking leans hard on that reuse.

The practical cost profile looks like this. If your main session is sitting at 180k tokens and you fork, the fork starts at 180k tokens too. But because those tokens are already cached, the effective pricing is closer to cache-read rates — about 10% of the normal input price on Sonnet tiers. So a fork that would nominally cost you the price of a 180k-token prompt actually costs something much closer to a 18k-token prompt on the first turn, and behaves like a normal conversation from there.

That's the part that changes the math on when to delegate.

There's a subtle but important detail in how this got built. A recent optimization to the `/fork` internals writes a pointer instead of the full parent conversation to disk per fork, with hydration on read. That's plumbing, not user-facing, but it's why you can spawn multiple forks without your disk filling up. The feature is production-shaped now, not experimental-shaped.

To enable forked subagents on external builds, you set the environment variable `CLAUDE_CODE_FORK_SUBAGENT=1` or flip the equivalent flag in your `settings.json`. Once enabled, `/fork` is available as a slash command in any session, and you can address the spawned fork directly for follow-up prompts without going back to the main thread.

## The First Fork I Actually Trusted

I'll be honest — I didn't trust `/fork` on day one. I'd been burned enough times by features that looked clean on a landing page and fell apart on real client code that my default stance is skeptical.

Here's what converted me.

I was working on a design system project — a dashboard with a Kanban board, a calendar view, and a settings panel, all needing to share a consistent visual language. I was about 140k tokens deep into a session where I'd already made a dozen component decisions, established a color system, picked a typography scale, and written three components. I needed to generate two design variations of the Kanban card — one denser, one more spacious — to see which fit better with the rest of the system.

With a normal subagent, this would have been a disaster. The subagent would have gotten a compressed summary that said something like "project uses Tailwind, design system is dark with purple accents, typography is Inter." That's not enough to generate variations that feel like they belong to the same system. Half the nuance — the exact spacing scale I'd been using, the specific shadow treatments, the way I'd been handling icon sizes — would have been gone.

I forked instead. Two forks, actually, in parallel. One with the prompt "generate a denser Kanban card variation — keep everything about the existing system, just compress vertical spacing by ~25% and tighten the type scale one step." The second with "generate a more spacious Kanban card variation — keep the system, expand padding and give the card more breathing room."

Both forks came back with variations that actually looked like they belonged to the same design system, because they had the same design system loaded. Not a summary of it. The whole thing. The color tokens I'd committed, the component patterns I'd established, the spacing logic I'd been refining for two hours. All there.

That was the moment I stopped arguing with the feature and started using it.

## Where Forking Earns Its Keep

Two weeks of heavy use has given me a rough taxonomy of where forked subagents actually matter and where normal subagents are still the right tool. Let me walk through the patterns that have paid off.

### Design Work Parallelization

This is the use case that sold me. When you're making visual or structural decisions that depend on a system you've already built up in the session, losing that system through compression kills the output quality. Forking preserves it.

I now routinely spawn two or three forks in parallel when I need to evaluate variations — component designs, API structures, database schemas, copy alternatives. Each fork gets the full context plus a specific variation prompt. I compare the outputs side by side. The comparison is only meaningful because all three forks were working from the same foundation.

### Memory Consolidation and Recap

If you've used Claude Code's `/bytheway` command or the memory consolidation features, you've already been using forked subagents — they run behind the scenes. These tasks need the full conversation to be useful. A recap that's generated from a compressed summary is just a summary of a summary, which is the content equivalent of a photocopy of a photocopy.

Now that I know what's happening under the hood, I use these features more aggressively. When I'm about to hit a context wall on a session I want to preserve, I'll fire off a `/bytheway` to consolidate the important decisions into memory before I compact or restart.

### Multi-Tool Parallel Verification

Here's a pattern I've built up that I find genuinely useful. When I've just generated a non-trivial chunk of code, I'll spawn two forks at once:

1. **Fork A**: "Generate a Mermaid diagram showing how the code changes you just made flow through the request lifecycle. Mark anything that changed in a different color."

2. **Fork B**: "Do a web search to verify the specific API patterns used in the code changes are current best practice for [framework] as of 2026. Cite anything that looks outdated."

Both forks have the complete session context, so they both know exactly what code I'm talking about. The diagram fork gives me a visual sanity check. The verification fork catches the cases where my muscle memory wrote a pattern that was idiomatic three years ago but has since been superseded. I get two independent angles on the same change, generated in parallel, without cluttering the main session with either task.

### Tangent Containment

This one's quieter but matters for sanity. Sometimes I have a side question that's interesting but off-topic. "Wait — if we'd used a different ORM pattern here, would this whole flow be simpler?" Normally that kind of tangent either derails my main thread for twenty minutes or gets lost because I don't want to derail.

Forked subagents give you a clean answer. Fork, ask the tangent question, explore the counterfactual. If the answer is valuable, bring the insight back to main. If it's a dead end, `/rewind` and the fork is gone. The main conversation never knew it happened.

This is a behavior change in how I use Claude Code, not just a feature. I explore more side questions now because the cost of exploration went from "potential derailment" to "free tangent, throw it away if useless."

## When Forking Is the Wrong Move

Forking is not always the right call. There's a real trap in assuming more context is always better, and I hit it in the second week of using the feature.

The trap is bias. A forked subagent has seen everything you've done in the session — including the code you just wrote, the architecture choices you made, the libraries you chose. When you ask a fork to review that code, you're not getting a fresh set of eyes. You're getting your own eyes, just running as a separate process. The fork remembers *why* you made every decision, and confirmation bias creeps in.

For code reviews, this matters. A good code review catches the thing you didn't think of, which means it has to come from a mind that hasn't been thinking the way you've been thinking.

I tested this directly. I wrote a batch of authentication-related code and asked a forked subagent to review it. The fork came back with cosmetic suggestions — variable naming, a docstring tweak, a minor refactor. Then I asked a normal subagent (fresh context, no history) to review the same code. The normal subagent flagged that I wasn't properly constant-time comparing a token, which was a real security bug that the fork had glossed over because, in the fork's context, the code "looked reasonable given the established patterns."

That's the rule of thumb I've settled on:

- **Fork when context is an asset.** Design consistency, memory consolidation, multi-step workflows that need history, tangent exploration.
- **Don't fork when context is a liability.** Code reviews, security audits, adversarial testing, anything where you need a fresh set of assumptions.

Normal subagents still have a seat at the table. They just have a different job.

## The Forked vs Normal Breakdown

Here's the comparison I wish I'd had on day one, compressed into something you can actually use when you're deciding in the moment.

| Aspect | Normal Subagent | Forked Subagent |
|---|---|---|
| **Context** | Summary / compressed | Full conversation history |
| **Token cost** | Lower absolute tokens | Higher tokens, shared cache brings effective cost down |
| **Bias** | Lower — fresh perspective | Higher — remembers and defers to prior decisions |
| **Best for** | Code reviews, unbiased audits, adversarial checks | Design continuity, memory consolidation, complex multi-step workflows, tangents |
| **Interaction model** | Single-step, one-shot | Multi-step, interactive, handles follow-ups natively |
| **Spawning cost** | Cheap | Cheap after cache warm-up, first fork warms the cache |

The row that matters most is the "Best for" row. Don't reach for `/fork` because it's the newer feature. Reach for it because the task genuinely benefits from context continuity. Otherwise you're paying a bias tax for no gain.

## Enabling It and Actually Using It

If you haven't turned forked subagents on yet, here's the shortest path.

Open your Claude Code `settings.json` — usually at `~/.claude/settings.json` for user-scope or `.claude/settings.json` at your project root. Either set the environment variable `CLAUDE_CODE_FORK_SUBAGENT=1` in your shell profile, or add the equivalent flag to your settings JSON. The exact key name has shifted between versions, so check the current Claude Code release notes if the environment variable alone doesn't light it up.

Once enabled, `/fork` is available as a slash command inside any session. Type it, add your prompt, and you've got a fork. You can address the fork directly for follow-up turns by prefixing messages, and you can use `/rewind` to discard a fork that didn't pan out.

A few operational tips from two weeks of living with this:

**Fork intentionally, not reflexively.** The first week, I forked for everything. That was wrong. Forking carries an implicit bias cost on review-style tasks, and it adds mental overhead because you now have two or three threads to track. I've settled into forking maybe three or four times per serious session — at moments where the task genuinely benefits from full context.

**Use parallel forks for convergence.** When you're unsure about a decision, spawn two forks with opposing assumptions and compare. I did this recently when debating whether to use server components or client components for a specific Next.js page. Two forks, two arguments, side-by-side output. The answer was obvious after five minutes of reading both.

**Watch the `/rewind` habit.** `/rewind` is the escape hatch that makes exploration cheap. If you're not using it regularly, you're probably not forking often enough for exploration. Forks that become dead ends should be rewound, not mourned.

**Pair forks with normal subagents for adversarial checks.** After a fork generates a solution, send the same problem to a normal subagent without context. If they agree, your confidence should be higher. If they disagree, that disagreement is information — dig into why the fresh perspective sees something the context-loaded fork missed.

## What I Got Wrong the First Week

I want to be honest about the misses, because the feature is powerful enough that it's tempting to oversell it.

The first mistake: I forked too much. I forked for code reviews, which was dumb. The fork, remembering every piece of code I'd just written, was basically rubber-stamping my work back at me. [Boris Cherny's talk on Claude Code workflow](https://www.mejba.me/blog/boris-cherny-claude-code-workflow) makes the point that fresh context is sometimes the whole point of a subagent, and I ignored that advice on the way in.

The second mistake: I didn't realize how much cache warmup matters. The first fork in a session pays a real cost to cache the inherited context. Subsequent forks are cheap. If you're doing a single fork for a small task, you're not really amortizing the cache the way the feature is designed to. Batch your forks when you can — if you know you're going to need three parallel explorations, spawn them close together.

The third mistake: I got complacent about session length. Forked subagents don't solve the fundamental problem that a 400k-token main session is a fuzzy-thinking session. They just move some of the work out of the main session into forks. If your main session is bloated, forks from that bloated session will themselves be bloated. At some point you still need to compact, checkpoint, or start fresh. [My earlier writeup on 1M context management in Claude Code](https://www.mejba.me/blog/claude-code-1m-context-management) covers the pruning side of this in more depth.

The feature doesn't excuse bad session hygiene. It rewards good session hygiene by making delegation actually useful within well-managed sessions.

## The Pattern That Stuck

Of all the workflows I tried, one has stuck and is now reflexive.

When I'm deep into a build and about to make a decision that feels load-bearing — "should I use this library or roll my own?" "should this API be REST or something else?" "does this component architecture generalize or am I painting into a corner?" — I fork twice. Two parallel forks, one taking each side of the decision, each asked to argue their case with full session context.

Five to ten minutes later I have two short memos. I read both. The right answer is usually obvious by then, and more importantly, the *reasoning* is usually obvious. I'm not just getting a recommendation — I'm getting the case for each option, evaluated against the specific context of what I've already built.

This has replaced a habit I used to have of pinging a developer friend to talk through decisions. The friend was sometimes unavailable, didn't know the project context, and had their own biases. The two forks are always available, know the project context exactly because they *are* the project context, and their biases are transparent because I wrote the prompts.

That's not a small change. That's a real shift in how I make technical decisions on solo projects.

## The Test I'd Run Tonight

If you've read this far and you haven't enabled `/fork` yet, here's what I'd do before bed.

Open your Claude Code settings, flip `CLAUDE_CODE_FORK_SUBAGENT=1`, and restart the session. Pick a project you've been working on for a few hours today — somewhere past 80k tokens of context. Think of a question that's been nagging you about that project, something you haven't answered because it felt like too much of a tangent.

Type `/fork`, ask the question, and see what comes back.

Then compare what you got to what you would have gotten from a normal subagent — you can actually run the same prompt via a normal subagent afterward and compare side by side. Notice what the fork caught that the normal subagent missed. Notice where the normal subagent's fresh perspective was actually more useful than the fork's full recall.

By the second or third fork you'll have a feel for when the feature earns its keep and when it doesn't. That instinct is what you're actually building. The command is easy. The judgment about when to use it is the skill.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
