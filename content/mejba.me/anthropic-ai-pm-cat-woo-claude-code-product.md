**BRAND:** mejba.me
**TITLE:** AI-Native Product Management: Cat Woo on Claude Code
**META TITLE:** AI-Native Product Management at Anthropic: Cat Woo
**SLUG:** anthropic-ai-pm-cat-woo-claude-code-product
**PRIMARY KEYWORD:** AI-native product management
**META DESCRIPTION:** Cat Woo runs product for Claude Code at Anthropic. Inside her interview: how AI-native product management compresses 6-month cycles to a single day.
**TAGS:** Claude Code, Product Management, Anthropic, AI Workflow, Product Strategy

---

I almost closed the tab when I saw the runtime. Forty-something minutes of a product leader interview, on a week where I had three deadlines stacked on top of each other and an inbox that looked like a crime scene. Then I saw the name: Cat Woo, Head of Product for Claude Code and Co-work at Anthropic. The person whose team has been shipping the tools I open every single morning before coffee. I hit play.

What I expected was the usual product-leader content — frameworks, OKRs, "we built the team in three pillars" energy. What I got was something I have not been able to stop thinking about for a week: a working description of how AI-native product management actually operates inside the company building the models. Not the theory. The day-to-day. The friction that disappears. The friction that does not. The trade-offs no one in a LinkedIn carousel will tell you about because they require you to ship daily for two years to even notice them.

This post is my breakdown of that interview, filtered through what it means for the rest of us — the indie builders, the small-team PMs, the engineers who suddenly find themselves doing product work, the founders trying to figure out why their cycles still feel like 2019. If you have been wondering what changes when product management goes AI-native, Cat Woo just answered it. I am going to translate her answers into something you can actually use on Monday.

## The Six-Month-to-One-Day Compression Is Not a Slogan

Cat described Anthropic's shipping cadence in numbers that should make anyone who has worked in traditional product feel slightly nauseous. The pre-AI norm — a six-month cycle from idea to release — has compressed to one month, one week, or even one day depending on the feature. Not "feature flags toggling on and off" days. Real, customer-facing capability shipping in twenty-four hours.

I have heard variants of this claim from a dozen AI companies and ignored most of them because the math never added up. Either the "feature" was a CSS tweak being framed as a launch, or the company had quietly defined "shipping" as "merged behind a flag nobody can access." But Cat's framing made the mechanism legible. The compression is not a productivity tool stack. It is not "we use Claude internally" (though they do). It is a deliberate stripping of the things that used to make six months the floor.

Three of those things, in order of importance:

The first is **alignment overhead.** In most companies, the reason a feature takes six months is not the building. It is the seven Slack threads, four cross-functional meetings, and three "let me sync with my counterpart" cycles required before anyone touches a keyboard. Anthropic gets around this through what Cat described as a unifying mission — safe AGI for all humanity — that lets teams sacrifice individual product goals for company priorities without it feeling like a loss. That sounds soft. It is not. When the mission is sharp enough, alignment becomes a one-line decision instead of a three-week negotiation.

The second is **PM bottleneck removal.** A lot of organizations route every decision through a product manager because the PM is the only person with full context. Anthropic explicitly does not. Many of their PMs come from engineering backgrounds and ship code themselves. When the person making the call can also implement the call, you remove the entire handoff layer. I have built features end-to-end this way myself — and I wrote about that mode in my [breakdown of Boris Cherny's Claude Code workflow](https://www.mejba.me/boris-cherny-claude-code-workflow). The compression is real and it is brutal once you get used to it.

The third is **model leverage applied to PM work itself.** This is the part most coverage misses. PMs at Anthropic use the models to draft PRDs, summarize user feedback, run repeatable launch workflows, generate variants, and probe failure modes. The same automation that compresses engineering compresses everything around engineering. If your PMs are still writing every doc by hand and your engineers are using Claude Code, you have a speed-of-light differential between the two ends of the pipe.

There is a line I want to come back to later, but I will plant it here so you have it in your head: **product taste is now more valuable than product execution.** Hold onto that. It is the single most important thing Cat said, and I want to earn the right to explain why before I do.

## What an AI-Native PM Actually Does All Day

Strip away the framework language and Cat described a job that looks fundamentally different from the PM role I learned a decade ago. Here is the shape of it, as I read between the lines of what she described.

**Lightweight PRDs, only when they earn their keep.** Documents still get written, but mostly for genuinely ambiguous projects or infrastructure work where the trade-offs need explicit reasoning. For a feature shipping in a week, the PRD overhead would consume a third of the cycle. So it does not exist. The PM aligns through conversation, prototype, and rapid iteration, not through a thirty-page doc that gets read by four people and forgotten.

**Quantifiable goals to fight ambiguity.** This one surprised me. You would think working with frontier general-purpose LLMs would let you be more loose with requirements, not less. The opposite is true. Because the model can interpret a vague goal seventeen different ways, the PM has to be more precise about what success means. "Make the response feel faster" gets you nothing. "P95 latency under 800ms with no quality regression on the eval set" gets you a measurable target the team can actually hit.

**Repeatable cross-functional workflows.** Marketing, docs, launch coordination — Cat described these as pre-built workflows that get triggered when a feature is ready, not bespoke planning each time. Build the workflow once, run it ten times. This is the part I keep coming back to in my own setup, and it overlaps with what I documented in [the Claude Co-work workflow automation post](https://www.mejba.me/claude-co-work-workflow-automation) — once a workflow is automated end-to-end, the marginal cost of shipping drops toward zero.

**Direct engagement with the model.** PMs at Anthropic actively probe the models' failure modes. They look at introspection outputs. They suggest improvements to the harness. This is not a "PM uses AI tool" relationship. It is a "PM is a power user who shapes what the tool can do" relationship. If your PMs treat the AI as a black box, your roadmap is going to be shaped by surface-level intuition instead of mechanical understanding.

The team itself, by Cat's count, is around 30 to 40 PMs split across research, the cloud developer platform, Claude Code and Co-work, enterprise features, and growth. That is a small number for a company shipping at this pace. The way I read it, the PM density is intentionally low because the model takes care of a lot of work that used to require headcount. Each PM is a leverage point, not a coordination node.

Now for the line I planted earlier.

## Product Taste Is the New Moat

Here is the claim, said plainly: when code becomes cheap and fast to produce, the constraint shifts from "can we build it" to "should we build it, and is what we are building actually good." That second question is what Cat means by product taste. It is the ability to look at three possible features and know which one matters. The ability to look at a working prototype and know it is missing the thing that makes it lovable. The ability to cut a feature that engineering spent two weeks on because it does not deserve to ship.

This used to be a soft skill — important, but secondary to execution discipline. In the AI era it is the primary skill, because execution is increasingly something a model and an engineer with a CLI can do in an afternoon. If you cannot tell the difference between a feature that will move retention and a feature that will look impressive in a demo, the speed of your team becomes a liability. You will ship faster, in the wrong direction, more often.

I have been thinking about how to build this taste deliberately, and three things keep showing up. The first is using the products you build, daily, as a real user — not as the person who built them. The second is reading user feedback obsessively, not in summary form but in the raw, because the texture of how people complain tells you what they actually want. The third is forming opinions about other people's products and writing them down. Taste is a muscle. It atrophies if you do not use it.

If you are a PM reading this and wondering whether your job is going to evaporate, I do not think it is. But the version of the job that is safe is not the version that writes specs and runs sprint planning. It is the version that has strong opinions about what should exist and the technical fluency to evaluate whether the model can actually deliver it.

## The Product Family: What Each Tool Is For

Cat walked through how the Claude Code and Co-work product family is actually segmented internally. This was useful for me because I have been mixing them up in my own usage. Here is the clean version.

**Claude Code (CLI)** is the most powerful surface and the place where features land first. If you want the bleeding edge — new capabilities, new agentic patterns, new sub-agent behaviors — this is where they show up before anywhere else. It is the product I use most days, and it is also the one with the steepest comfort curve if you have not lived in a terminal for years. I covered the muscle memory side of this in [my Claude Code advanced workflow guide](https://www.mejba.me/claude-code-advanced-workflow-guide).

**Claude Code Desktop** is the GUI version, optimized for front-end work and for users who do not want to be in a terminal all day. Preview panes, visual diffs, the kind of feedback loop where you can see what changed without parsing a unified diff in your head. This is the on-ramp for designers, product folks, and engineers who think visually.

**Claude Code Mobile** is the kick-off-tasks-from-anywhere surface. The use case Cat described is the one I have started living: you have an idea on a walk, you fire off the task, and by the time you are back at your laptop the agent has done the first 80% of the work. It is not where you do deep work. It is where you start work without a laptop blocking you.

**Co-work** is the non-code surface. Slides, docs, emails, comms management. Connect Slack, Gmail, Drive, Calendar, and you get a context-aware assistant that can actually do things in your work life instead of just talking about them. I have written more about how I use this surface in [my Claude Co-work daily workflow system breakdown](https://www.mejba.me/claude-cowork-daily-workflow-system) and [the deeper plugins guide](https://www.mejba.me/claude-cowork-plugins-guide), both of which line up almost exactly with the product intent Cat described.

The thing I want to call out, because it changed how I think about my own setup: these are not four versions of the same product. They are four surfaces tuned to different cognitive modes. CLI for deep work. Desktop for visual work. Mobile for initiation. Co-work for the non-code half of your job. Picking the right one for the moment is itself a skill, and it is one I am still building.

## The Model Improvement Loop That Matters

Cat described something almost in passing that I want to spend a section on, because it explains a pattern I have been seeing without being able to name. Newer models have been removing crutches that older versions required. The example she gave: explicit to-do lists for code refactoring used to be necessary because the model would lose track of multi-step work. Newer models do not need them. The crutch becomes a vestigial feature.

This matters for two reasons. First, it means the right way to evaluate a new model is not "does it score higher on the eval" but "what manual scaffolding can I now remove." Every prompt you have lovingly engineered to compensate for a model weakness is a candidate for deletion when the next model ships. Most people do not delete them. They keep stacking complexity on top of capability that has caught up.

Second, it means features that were impossible six months ago suddenly become trivial. Cat used automated code review as the example — a capability that was not viable until recent model improvements made the reasoning reliable enough. The PM job here is not just "build features." It is "watch the capability frontier and notice when the previously-impossible becomes possible." That is a different job than roadmap management. It is closer to scouting.

There is a discipline implied here that I want to name. PMs at Anthropic do not just ship features and move on. They probe the model. They look at where it fails. They reason about what the next model might enable and pre-design for it. That last part is wild — designing features for capability that does not exist yet — but it is the only way to ship on day one of a model release instead of three months later. If your roadmap planning starts from "what can the current model do," you are going to be perpetually behind the curve. The teams winning are starting from "what is the model going to do in the next release, and what do we need ready when it does."

## What Humans Still Bring to AI-Native Product Work

This is the section I expected to be vague. It was not. Cat was specific about the human contribution in three areas, and I think she is right about all three.

**Common sense.** Models still do strange things in unusual contexts. They miss the obvious. They optimize for the literal request instead of the underlying intent. The PM job here is to notice when the output is technically correct and contextually wrong, and to feed that pattern back into the system. This is unsexy work. It is also the work that prevents your product from shipping something embarrassing.

**Emotional intelligence and stakeholder management.** When a launch is delayed, when a customer is unhappy, when an engineer pushes back on a spec — none of that gets resolved by a model. It gets resolved by a human who can read the room, hold space for frustration, and find the version of the path forward that everyone can live with. AI-native product work increases the relative value of EQ because so much of the rest of the job has been automated.

**Product goals and prioritization.** Models can generate features. They cannot decide which features matter. They can summarize user feedback. They cannot decide which feedback to act on. The strategic layer — the part where you decide what the product should become — remains stubbornly human. Cat described this as the PM's defining contribution, and I think that is right.

The trait list she gave for navigating this kind of work was three words: calmness, optimism, bias toward action. I want to expand each one because they are easy to nod at and hard to actually live.

Calmness is the discipline of not panicking when the ground shifts. New model drops, competitor ships something cool, internal priority changes — the AI space is a constant low-grade turbulence. The PM who can stay grounded becomes the stabilizing force a team needs to ship anything.

Optimism is the belief that the next constraint will get unlocked. Most pessimism in tech is wrong because it under-estimates the rate of progress. If you assume the model will get better and the workflow will get smoother, you make different decisions today than if you assume the world freezes.

Bias toward action is what separates the people who learn from the people who theorize. You cannot learn what an AI-native product feels like by reading about it. You have to ship one, watch it fail, fix it, and ship again. The interview made it clear this is the trait Anthropic selects for above almost everything else.

## The Trade-Offs Nobody Wants to Talk About

This is the part of the interview I respect Cat the most for. She did not pretend the speed comes for free.

**Product overlap.** When you are shipping daily across multiple surfaces, capabilities start to overlap in ways that confuse users. "Wait, can I do this in Co-work or do I need to be in Claude Code?" is a question the team is actively navigating. The fix is education and onboarding flows like PowerUp, but it is a real cost — speed of shipping creates a cognitive tax on the user.

**Update fatigue.** Users feel pressure to keep up with daily releases. I have felt this myself. Every time I sit down with Claude Code there is a chance something has changed. That is exciting if you are a power user. It is exhausting if you are a normal human trying to get work done. Anthropic is investing in onboarding to soften this, but it is the structural cost of shipping at this pace.

**Token usage.** Cat was honest about this — token usage is increasing as models get more capable, and the team enforces responsible usage internally even though they have plenty of capacity. The framing she used is the one I want to remember: token usage is still well below the cost of an average engineer salary. If a model can do what an engineer would do for 10% of the cost, you do not optimize the tokens. You let the model run. But you do not waste them either, because waste at scale becomes real money.

**Inconsistency.** When you ship daily, things get inconsistent. UI patterns drift. Feature placement varies. The team has to do periodic consistency passes that slower-shipping companies do not. This is the cost of speed. You get more features faster, you get a slightly less coherent product surface, you accept the trade.

I appreciated that she did not gloss over any of this. The narrative around AI-native product is usually "it just works, ship faster." The reality is closer to "you can ship faster, and you have to be honest about the costs, and you need to invest in fixing them." That is a more useful framing than the hype version.

## What I Am Changing in My Own Setup

Listening to this interview, I made a list of things I want to change in how I work. I am sharing the list partly because writing it down forces me to actually do it, and partly because some of it might be useful to you.

**Stop writing PRDs for things that do not need them.** I have been over-documenting work that ships in two days. The doc takes longer than the build. From now on: if the work fits in a week, no PRD. Conversation, prototype, ship. If it is genuinely ambiguous or infrastructural, then yes, write the doc.

**Set quantifiable goals for everything.** This is the one I am worst at. "Make the agent feel snappier" is not a goal. "Cut median response time on the eval set by 30% without quality regression" is. The model needs the precision. So does my future self when I look back and try to figure out whether the change worked.

**Build repeatable workflows for everything I do twice.** If I publish a post and have to manually do the social distribution, the SEO checklist, the cross-link audit — that is a workflow that should exist once and run forever. I have started building these and the leverage is wild. Cat described this as the PM's job. I think it is everyone's job now.

**Probe the model deliberately.** I have been using Claude Code mostly as an assistant, not as an artifact I am studying. From now on I am going to spend an hour a week intentionally probing failure modes — not to fix them in my prompts, but to understand the shape of the model. This is what the Anthropic PMs do. It is what separates power users from passengers.

**Aim for 100%, not 95%.** Cat's line on automation was that if it works 90 to 95% of the time, you are still doing the work because you cannot trust it. Aim for 100% reliable on the things you automate. I have a half-dozen automations I have been tolerating at 92%. They need to either get to 99% or get deleted. Living with broken automation is worse than not having it.

## The Thing I Cannot Stop Thinking About

I want to come back to where I started. I almost closed the tab. The version of me that did not close it ended up with a working description of how product gets built at the company shipping the tools that have rewired my actual workflow over the last eighteen months.

The line from the interview that has been on a loop in my head is the one about product taste becoming the constraint. Not because it is provocative — it is barely provocative — but because it implies a re-pricing of every skill in the industry. Coding speed: less valuable than it was. PM coordination: less valuable than it was. Roadmap management: less valuable than it was. Taste, opinion, willingness to cut things, ability to feel when a product is good versus impressive: more valuable, possibly by a multiple.

If that re-pricing is right — and I think it is — then the question for every person reading this is whether you have been investing in the things that just got more valuable, or in the things that just got less valuable. If you have spent the last five years getting really good at writing tickets and running standups, the next five years are going to be uncomfortable. If you have spent them developing opinions about products and the willingness to defend them, you are about to be in demand in a way you have not been before.

The thing I am going to do this week is sit down with a blank page and write out, in plain language, what I think makes a product good. Not a framework. Not a checklist. My actual opinions, with examples. I have been outsourcing this thinking to other people's frameworks for too long. If taste is the moat, I want to know what mine actually is.

You should probably do the same.

## Frequently Asked Questions

### What does AI-native product management mean in practice?
AI-native product management means PMs use frontier models throughout their workflow — drafting PRDs, summarizing feedback, probing failure modes, building repeatable launch workflows — and design products that depend on model capabilities being elastic over time. The role shifts from coordination toward taste, precision, and direct model fluency. See the section on what AI-native PMs actually do all day for the working description.

### How does Anthropic ship features in one day instead of six months?
The compression comes from three things: alignment overhead removed by a sharp company mission, PM bottleneck removed by hiring engineer-PMs who can ship code themselves, and model leverage applied to PM work like docs, launches, and feedback synthesis. It is not one tool. It is a stack of friction removals.

### What is the difference between Claude Code and Co-work?
Claude Code is for coding tasks (CLI, Desktop, Mobile surfaces), with the CLI being the most powerful and earliest to get new features. Co-work is for non-code outputs — slides, docs, emails, communications — and integrates with Slack, Gmail, Drive, and Calendar for context-aware productivity work.

### Why is product taste more valuable in AI-native product work?
When code becomes cheap and fast to produce via models, the constraint shifts from execution to selection. The ability to decide what to build, what to cut, and what makes a product genuinely good is no longer a soft skill — it is the primary differentiator. Speed without taste makes you ship in the wrong direction faster.

### How many product managers does Anthropic have?
Around 30 to 40 PMs split across research, cloud developer platform, Claude Code and Co-work, enterprise features, and growth, according to Cat Woo's interview. Many come from engineering backgrounds and ship code themselves, which keeps the team small relative to shipping pace.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
