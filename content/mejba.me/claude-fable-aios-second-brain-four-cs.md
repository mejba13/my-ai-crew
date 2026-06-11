**BRAND:** mejba.me
**TITLE:** Claude Fable AIOS: A Second Brain on the Pricey Model
**META TITLE:** Claude Fable AIOS: Build a Second Brain (Four C's)
**SLUG:** claude-fable-aios-second-brain-four-cs
**PRIMARY KEYWORD:** Claude Fable AIOS
**META DESCRIPTION:** Building a Claude Fable AIOS at $50/M output forces brutal calls. Here's the Four C's framework, the delegation math, and the email blast that broke.
**TAGS:** Claude Fable, AI Operating System, Second Brain, Agent Architecture, Cost Optimization

---

The number that reframed the whole project was $50.

Fifty dollars per million output tokens. That's what Claude Fable 5 costs on the API — double Opus 4.8, and the most expensive generally available model Anthropic has ever shipped ([$10 per million input, $50 per million output](https://platform.claude.com/docs/en/about-claude/pricing), confirmed at the June 9 launch). I'd been planning to point a freshly built personal AIOS — an AI operating system, a second brain with hands — at this model and let it run my life. Then I did the napkin math on a single morning brief that reads my calendar, three Slack channels, yesterday's Stripe charges, and my task board, then writes a 600-word summary. On Fable, running that every morning, the output tokens alone would cost more than my Spotify, Netflix, and iCloud subscriptions combined. For a paragraph I read in ninety seconds.

That's the thing nobody tells you about building a **Claude Fable AIOS**. The framework for a second brain is the easy part. The hard part — the part that actually determines whether your system survives contact with a billing statement — is deciding what is *worth* running on the smartest, priciest model you have, and what should be quietly handed to something cheaper. I learned this the expensive way, and one of the lessons involved an email that should never have been sent.

Let me walk you through what I built, the Four C's framework I built it on, and the cost decisions that turned an interesting toy into something I actually trust with my business.

## What a Claude Fable AIOS Actually Is (and Why Fable Changes the Math)

A second brain is your knowledge — notes, decisions, archives, the stuff you'd lose sleep over if it vanished. An AIOS is the *infrastructure on top of it*: the skills, the live data connections, the automations that act on that knowledge without you in the loop. The brain stores. The OS does.

I've [built a personal AIOS on Claude Code before](/build-ai-operating-system-claude-code), and that version ran on cheap, fast models where you could afford to be sloppy. Burn a few thousand tokens on a throwaway query? Who cares. The whole calculus changes when your reasoning engine is Claude Fable 5.

Here's why Fable specifically forces better architecture. Fable is Anthropic's public-facing slice of the Mythos 5 lineage — the same family I dissected when [Fable 5 and Mythos 5 launched](/claude-fable-5-mythos-5-launch). It's genuinely excellent at long-horizon reasoning, multi-file synthesis, and the kind of judgment calls that cheaper models fumble. But it bills at a rate that punishes waste. And the subscription situation makes this sharper, not softer:

- Fable was **free on Pro and Max from June 9 through June 22, 2026**. On June 23, Anthropic pulled it from plan limits — continued use runs on usage credits at API rates ([per Anthropic's rollout](https://claude.com/pricing)).
- Inside the subscription window, Fable counted **roughly double Opus toward your limits**. On the $200/month Max plan with its ~5-hour session windows, running Fable heavily could exhaust your week's allowance in days, after which the picker silently drops you to Sonnet.

So you get a short honeymoon where Fable feels free, followed by a hard wall where every token has a dollar sign on it. If you architect your AIOS during the honeymoon assuming Fable everywhere, you'll get a brutal bill the moment the window closes. The system has to be designed for the post-June-23 reality from day one.

That single constraint — *the smartest model is too expensive to use for everything* — ends up shaping every other decision. Keep it in mind, because it's the thread running through all four C's.

## The Four C's: The Architecture That Survives a Billing Statement

I organize the entire system around four layers, built in strict order: **Context, Connections, Capabilities, Cadence.** You cannot skip ahead. Cadence without Capabilities is a cron job firing into the void. Capabilities without Connections is a clever notebook. Connections without Context is an agent acting on a stranger's behalf.

What follows isn't the generic version of this framework. It's the version that assumes your reasoning model costs $50 per million output tokens — because that assumption changes what each layer should actually look like.

I named my build "Herk 2" — the second iteration of a long-running project to put my entire personal and business knowledge into one system that thinks alongside me instead of just storing what I've already thought. The naming doesn't matter. The order does.

### Context: Plain Markdown, Because Tokens Are Now Money

Context is the static layer — everything the system knows about me that doesn't change minute to minute. Goals. Business model. Client list. Brand voice. Processes. The archive of decisions I've already made so I don't relitigate them.

All of it lives in markdown files in a single repository anchored by a `CLAUDE.md` at the root. Not a database. Not a vector store. Plain text files in folders I can read with my own eyes.

People assume you need a fancy retrieval system for a knowledge base. At personal and early-business scale, you don't — and on Fable, the wrong choice is actively expensive. A well-organized file system handles a surprisingly large knowledge base without any database at all. The `CLAUDE.md` acts as a routing tree: it doesn't contain the knowledge, it contains the *map* to the knowledge, telling the agent which file to open for which question.

```
herk2/
├── CLAUDE.md                    # the routing tree — points to everything
├── context/
│   ├── business.md              # model, offers, pricing, positioning
│   ├── clients.md               # top accounts, status, history
│   ├── goals.md                 # quarterly + annual, with the why
│   ├── voice.md                 # how I write, what I never say
│   ├── decisions/               # one file per major decision, dated
│   └── archives/                # finished projects, post-mortems
├── connections/                 # API wrappers, scoped keys
├── skills/                      # named capabilities, one folder each
└── automations/                 # cadence — triggers and schedules
```

Why does this matter for cost? Because the routing tree means Fable only ever loads the *one* file relevant to the task instead of stuffing the whole knowledge base into context. When input is $10 per million tokens, loading 40 markdown files you don't need on every query is real money. The router keeps each call lean.

There's a quieter benefit too. Plain markdown is model-agnostic. The exact same `context/` folder works whether the agent reading it is Fable, Sonnet, or a non-Anthropic model like Codex. I'm not locked in. If a cheaper model gets good enough next quarter, I swap the engine and keep the brain. Try doing that with a proprietary database schema wired to one vendor's embeddings.

To populate all this without spending three weeks procrastinating, I use an interview skill — I'll come back to it in the Capabilities section, because it's the single best trick I've found for building context fast. For now, just hold onto this: context is the foundation, it lives in markdown, and the routing tree is what keeps your Fable bill from exploding.

### Connections: API Keys Are Your Real Permission Layer

Once the system knows who I am, it needs to reach the live world. Connections are the dynamic layer — APIs into Stripe, QuickBooks, Google Workspace, ClickUp, Slack. This is what lets the AIOS read *today's* revenue, *this morning's* calendar, *the actual* unread threads, rather than reasoning over a stale snapshot.

Here's the lesson that cost me the most to learn, and it has nothing to do with tokens.

I had an agent whose job was to triage my inbox and draft replies for my review. The instructions were explicit: *draft only, never send, always show me first.* One morning it misinterpreted a multi-step instruction — it read "send the follow-up to the list we discussed" as an instruction to actually send, to an actual list. It sent. To real people. An email that was nowhere near ready.

I caught it fast and the fallout was minor — an awkward "please disregard" follow-up. But the lesson was permanent: **a prompt is not a permission system.** Telling an agent "don't send emails" is a suggestion. It's a string of text the model can misread, the same way I can misread a road sign at night. Real trust comes from the agent *physically not having the ability* to do the thing you don't want it to do.

So now every connection is governed by the scope of its API key, not by the politeness of my prompts. The email-triage agent holds a key with read and draft permissions and nothing else. It *cannot* send, because the credential it's been handed doesn't carry that scope. If it misinterprets an instruction, the worst case is a draft sitting in my folder. The permission layer lives at the key, where the model can't argue with it.

```
# WRONG — permission as a suggestion the model can fumble
SYSTEM: You may read and draft emails. Never send. Always confirm first.

# RIGHT — permission as a hard boundary at the credential
GMAIL_KEY scope: gmail.readonly, gmail.compose   # no gmail.send. Ever.
```

Scope every key to the minimum the task needs. Read-only where read-only suffices. Draft, not send. Single-project access, not account-wide. This is tedious to set up and it is the most important security work in the entire system — especially because Anthropic's models are closed-source, so you're trusting a black box with access to your business. The narrower the key, the smaller the blast radius when something goes wrong. And something will go wrong. Mine sent an email. Yours will do something else.

That mistake reshaped how I think about the next layer too. Because the more capable your skills get, the more it matters what they're allowed to touch.

### Capabilities: Skills, Sub-Agents, and the Art of Not Using Fable

Capabilities are the verbs of the system — the named skills, agents, and automations that actually *do* things. Each one is a folder with a `SKILL.md` defining what it does and when it loads. They range from a one-paragraph prompt ("summarize this transcript in my voice") to a multi-step workflow that researches, drafts, reviews, and publishes.

The architecture lesson here came from watching my skills get worse as they got bigger. A single agent trying to research *and* draft *and* polish in one long session suffers context drift — by the time it's polishing, it's half-forgotten the research brief, and the output drifts off target. The fix is modularity: separate sub-agents in separate sessions, each with one job. One agent researches and hands off a clean brief. A fresh agent drafts from that brief with no research clutter in its context. A third polishes. Clean handoffs, no drift. I learned this pattern the hard way, the same lesson I keep relearning across [multi-session agent workflows](/handoff-skill-claude-code-multi-session).

But the decision that actually matters for a Fable AIOS is which model runs each sub-agent. And the answer, most of the time, is *not Fable.*

This is the heart of the whole thing. **Delegation isn't just a quality pattern — on Fable it's a survival pattern.** Fable is a senior strategist whose hourly rate is brutal. You do not have your most expensive person do data entry. So I delegate aggressively:

- **Fable** handles the genuinely hard, irreversible, high-judgment work: the final synthesis, the strategic call, the thing where being wrong is costly. The 10% of work that justifies $50 per million tokens.
- **Sonnet** handles the bulk of the actual labor: drafting, summarizing, transforming data, the parallel grunt work. It's a fraction of the cost and good enough for 80% of tasks.
- **Haiku** handles the trivial, high-volume stuff: classification, extraction, quick lookups, the things you'd run hundreds of times.

The pattern that makes this work is **fan-out, then aggregate.** When a task has independent parallel parts — say, "summarize these eight client threads" — I don't hand all eight to Fable. I fan them out to eight cheap Haiku or Sonnet workers running in parallel, each producing a tight summary. Then, and only then, Fable receives the eight aggregated summaries and does the one thing it's worth paying for: the cross-cutting judgment. *"Client three and client seven are both quietly unhappy about the same delivery delay — here's the pattern, here's what I'd do."* That insight is worth $50 per million tokens. Reading eight raw threads to produce it is not.

The cost difference is not marginal. Fable's output is double Opus and many times Sonnet. Pushing the routine 80% of work down to cheaper workers and reserving Fable for the irreplaceable 20% is the difference between an AIOS you can afford to run daily and one you switch off after the first invoice.

> **If you build only one thing from this post, build this:** a "Grill Me" skill. It interviews you relentlessly, asking question after question to extract knowledge you'd never sit down and write out. *"What's your hourly rate? Why that number? What client would you fire if you could? What does a good week actually look like?"* Each answer gets written to the right context file as structured markdown. It turns you running your mouth into a populated knowledge base — and the best part is you can run the interviewer on cheap Sonnet, because asking good questions doesn't need a frontier model. (There's a whole [skill built around this interview pattern](/grill-me-skill-claude-code-knowledge-extraction) if you want a head start.)

If you'd rather not assemble this delegation layer yourself, this is exactly the kind of system I build for clients — [you can see what I take on here](https://www.fiverr.com/s/EgxYmWD). But honestly, the framework above is enough to start solo this weekend.

One more capability lesson that saved me repeatedly: **verify outputs, don't trust them.** When a skill builds something visual or functional — a report, a dashboard, a generated page — I have it actually run and check the result, not just declare success. An agent that says "done!" is not the same as a thing that works. Dynamic, functional testing of AI output is non-negotiable once the system is acting on its own.

Which brings us to the layer where it acts entirely on its own.

### Cadence: When the System Runs Without You

Cadence is *when* things fire. Three trigger types: **scheduled** (every morning at 6 AM), **event-based** (a new Stripe charge, a GitHub push, an inbound email), and **manual** (I type `/daily-plan`). Cadence is what turns a clever assistant into an operating system that works while I sleep.

It's also where cost, security, and maintenance all collide at once. A skill you trigger manually runs when you choose, under your eyes. A skill on a cadence runs *whether you're watching or not* — which means a Fable-powered automation firing every hour is a Fable bill accruing every hour, forever, even on the days it produces nothing useful.

So my cadence rules are strict:

1. **Scheduled Fable tasks are rare and high-value only.** The morning strategic brief, yes — once a day, and even then the *gathering* is done by cheap workers and only the final synthesis touches Fable. Everything routine runs on Sonnet or Haiku on its schedule.
2. **Every automation is monitored.** Autonomy does not remove the need for human oversight — it raises the stakes of *not* having it. I log every automated run and skim the logs. The email blast taught me that an agent acting unsupervised on a schedule is exactly how small misinterpretations become real-world events.
3. **Cost ceilings per automation.** Each scheduled task has a rough token budget. If a run blows past it, that's a signal the skill drifted or the input ballooned, and it gets flagged.

This is the same discipline I apply to [scheduled Claude automations generally](/claude-code-loop-cron-scheduling) — but Fable makes the cost line of that ledger impossible to ignore. On a cheap model, a runaway cron is annoying. On Fable, it's a four-figure surprise.

Build the four C's in order and you end up with something genuinely different from a chatbot: a system that holds your context, reaches your real tools through scoped keys, exposes its skills as named capabilities, and runs them on a cadence you control — most of it on cheap models, with Fable reserved for the moments that actually need a frontier brain.

## What It Looks Like When It Works

I want to be specific about the payoff, because "second brain" is a phrase that's been bled dry by people selling Notion templates.

Two demos convinced me the system was real. The first: I pointed it at a long YouTube channel and asked for a structured breakdown of the creator's content strategy. It pulled the transcripts, fanned the summarization out to cheap workers, and handed Fable the aggregate — which produced a genuinely sharp read on the channel's positioning and gaps, in a single prompt. The grunt work was cheap; only the insight was expensive.

The second was more useful day to day: an interactive relationship map of my own tools, workflows, and projects, generated from my context folder in one prompt — which automation feeds which project, which skill depends on which connection. Seeing my own system as a graph surfaced two dependencies I'd forgotten existed.

The realistic results, stated honestly without invented numbers:

- **Context retrieval costs dropped sharply** once the routing tree replaced "load everything." Input tokens are the cheap half of Fable, but loading 40 unnecessary files on every call still added up — the router cut that to the one or two files a task actually needs.
- **The delegation pattern is where the real savings live.** Moving the routine ~80% of work off Fable and onto Sonnet and Haiku is the single change that made daily operation affordable. Your mileage depends on your task mix, but the direction is not subtle — frontier output at $50 per million tokens is something you ration, not something you spray.
- **The expensive part is now my attention, not the API.** Maintenance is the real ongoing cost. Connections break. APIs change. Skills drift. A system this capable is a system you tend, not one you set and forget.

If you're evaluating whether this is worth your time, that last point is the honest catch. So let's talk about the parts that aren't in the highlight reel.

## The Real Talk: What This Costs You That Isn't On the Invoice

The token bill is the cost people fixate on. It's not the one that'll get you.

**The biggest challenge is people, not models.** Building a personal AIOS is hard but tractable — you control every variable. The moment you try to extend it to a team, the difficulty multiplies. Shared knowledge management, getting colleagues to actually maintain their context, training people to think in skills and delegation rather than one-off prompts — that's organizational change, and no model solves it. If you're a solo operator, you have an enormous, underrated advantage here: there's no one to convince but yourself.

**Closed-source means trusting a black box with your business.** Anthropic doesn't open the model. You're routing real revenue data, real client information, real calendars through infrastructure you can't inspect. That's why the scoped-key permission layer isn't optional paranoia — it's the only real control you have. Treat sensitive data deliberately: what genuinely needs to flow to the model, and what can stay in files the agent reads locally without round-tripping to an API.

**The architecture will not survive contact with reality unchanged.** Mine has already been rebuilt once — that's why it's "Herk 2." You'll hit latency problems, token-consumption surprises, a skill that worked great until your knowledge base tripled in size. The system evolves based on what actually breaks, not what you planned. Build it expecting to refactor it.

**Don't outsource your thinking — partner with it.** The highest-value use I've found isn't automation at all. It's using the system as a thought partner: spinning up multiple sub-agents to debate a decision from different angles, then reading the argument. The AIOS that handles your busywork is useful. The one that sharpens your judgment is the one worth building.

Here's the prediction I'll commit to: the people who win with frontier models like Fable won't be the ones who use them the most. They'll be the ones who use them the *least* — who've built systems disciplined enough to spend $50-per-million tokens only on the handful of decisions that genuinely deserve a frontier brain, and route everything else to workers that cost a tenth as much. Restraint is the skill. The model is just the engine.

## The One Thing to Do This Week

Go back to the very first number in this post — the $50. Then look at whatever AI workflow you're running right now and ask the question that reframed my entire project: *what here actually needs the smartest, most expensive model, and what am I overpaying to do?*

You don't need Fable to start. You don't need to build all four C's this weekend. Start with Context: open a folder, create a `CLAUDE.md`, write down who you are and what your business does in plain markdown. That single file — the foundation everything else stands on — costs nothing and works with every model you'll ever swap in.

The second brain stores what you already know. The AIOS acts on it. But the discipline that decides whether yours is affordable or abandoned is the one thing no model will build for you: knowing exactly what's worth thinking hard about, and what isn't.

So — if you ran that audit on your own stack tonight, what would you find you've been paying a strategist's rate to do?

## Frequently Asked Questions

### What is a Claude Fable AIOS?
A Claude Fable AIOS is a personal AI operating system that uses Claude Fable 5 as its reasoning engine to act on your knowledge base. It combines a markdown "second brain" with live API connections, named skills, and scheduled automations — built on the Four C's framework of Context, Connections, Capabilities, and Cadence.

### How much does it cost to run an AIOS on Claude Fable 5?
Claude Fable 5 costs $10 per million input tokens and $50 per million output tokens — double Opus 4.8 and the most expensive generally available Anthropic model. Running an entire AIOS on Fable is impractical; the affordable approach delegates routine work to cheaper Sonnet and Haiku workers and reserves Fable for high-judgment synthesis. See the Capabilities section above.

### What are the Four C's of an AI operating system?
The Four C's are Context (static markdown knowledge), Connections (live API integrations), Capabilities (named skills and agents), and Cadence (scheduled and event-based automations). They build in strict order — each layer depends on the one before it, as explained in the architecture section above.

### Why use API key scopes instead of prompt instructions for AI safety?
Because a prompt is a suggestion the model can misinterpret, while an API key scope is a hard boundary it physically cannot cross. After an agent accidentally sent a real email despite "never send" instructions, I moved all permissions to scoped credentials — the email agent now holds a key without send access, so misreading an instruction can't cause harm.

### Can I switch models without rebuilding my AIOS?
Yes — if your context lives in plain markdown files rather than a proprietary database. Because the knowledge layer is model-agnostic text, you can swap Claude Fable for Sonnet, Opus, or even a non-Anthropic model like Codex without rebuilding the system. Only the reasoning engine changes; the brain stays.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
