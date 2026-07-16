**BRAND:** mejba.me
**TITLE:** OpenAI Codex vs Claude Code: I Tested Both. Here's The Truth
**META TITLE:** OpenAI Codex vs Claude Code: Tested Side-By-Side (2026)
**SLUG:** openai-codex-vs-claude-code-tested
**PRIMARY KEYWORD:** OpenAI Codex vs Claude Code
**META DESCRIPTION:** I ran OpenAI Codex vs Claude Code on a PDF report, a landing page, and a dashboard. Tokens, time, output quality — here's what actually shipped.
**TAGS:** Claude Code, OpenAI Codex, AI Coding Agents, AI Model Reviews, Developer Workflow

---

I had a Tuesday morning hole in my calendar and a question I couldn't keep ignoring. Every time someone asked me which agentic coding tool to actually pay for in 2026, I'd given them a vague "depends on your workflow" answer that I knew was lazy. So I cleared the morning, opened two terminals, logged into both my Anthropic Max account and my OpenAI ChatGPT Pro plan, and ran the same three jobs through both stacks back to back.

Three tasks. One research report formatted as a PDF. One marketing landing page for a fake SaaS product. One analytics dashboard for the same product. Nothing exotic — these are the jobs I actually take on for clients in any given week. The kind of work where a freelancer with a working agentic stack ships before lunch and a freelancer without one ships next Tuesday.

What I expected: Claude Code would win on UI polish, Codex would win on structured docs, and the comparison would end somewhere around "use both depending on the job." What I actually got was a much sharper picture than that — including one moment, around the 14-minute mark of the dashboard build, where I almost stopped the test because I didn't believe what the timer was telling me.

Stick with me through the dashboard section. That's where the framing of this whole comparison snapped into focus.

## What Each Tool Actually Is In May 2026

Before I get to the runs, you need to understand what's actually shipping inside each box, because both products got materially upgraded in the last quarter and a lot of the "I tried it six months ago" takes floating around are now obsolete.

**Claude Code (Anthropic)** is the agentic coding tool I've been living inside for the better part of a year. As of this month, it runs in four surfaces: a terminal CLI, a VS Code extension, a desktop app for Mac and Windows, and a web version that's in research preview. Under the hood it routes between three models — Opus 4.7 for heavy planning and code generation, Sonnet for fast iteration, Haiku for cheap subagent work. The customization layer is where it earns its keep: 30 hook events you can wire into the lifecycle, auto-delegating sub-agents that fork off without you babysitting them, slash commands like `/ultraplan`, `/ultrareview`, and `/loop` for structured workflows, plus the Claude Agent SDK in Python and TypeScript if you want to embed any of this in your own products. On the enterprise side, it's deployable through Amazon Bedrock, Google Vertex AI, and Microsoft Foundry — meaning a Fortune 500 security team can put it inside their existing cloud contract without a procurement war.

**OpenAI Codex** is the rebuilt one. The version I used eighteen months ago is not the version I'm reviewing today. It now ships across four surfaces too — terminal, desktop, VS Code, and a cloud version at chat.openai.com/codex that runs sandboxed sessions you can hand work to and walk away from. It runs the GPT family plus the dedicated GPT-Codex and GPT-Codex-Spark variants (Spark is in research preview). The killer additions in this generation are native Git worktree support so multiple agents can run in parallel branches without trampling each other, an in-app browser with inline comments for design review, robust computer-use capabilities for QA work, and a GitHub integration where you `@Codex` a PR and a cloud sandbox spins up to review it. There's an experimental `/goal` command for long-running multi-tool jobs, and GPT Image 2 is built directly into the desktop so you can generate hero images without leaving the tool. Pricing is bundled with every ChatGPT plan including the free tier.

That last sentence is important because it changes the math for a lot of people. Neither tool requires a separate API key. Claude Code is included with the Anthropic Pro ($20/mo), Max 5X ($100/mo), and Max 20X ($200/mo) plans. Codex is included with ChatGPT free, Plus ($20/mo), and Pro ($200/mo) — where Pro is effectively unlimited usage, and a current promo on the $100 tier doubles Codex usage through May 31. If you already pay for either consumer plan, you already have access. That's a different equation from a year ago when both were API-metered specialist tools.

Here's the head-to-head feature snapshot before we get into the actual tests.

| Feature | Claude Code | Codex |
|---|---|---|
| Hook events | 30 | ~6 |
| Sub-agents | Auto-delegating | Explicit invocation |
| Workflow shape | Customizable, workflow-focused | Unified end-to-end shipping |
| Platforms | Terminal, VS Code, Desktop, Web | Terminal, VS Code, Desktop, Cloud |
| Models | Opus, Sonnet, Haiku | GPT family + GPT-Codex, GPT-Codex-Spark |
| In-app browser | No (Claude in Chrome extension) | Yes, built into desktop |
| Computer use QA | Limited first-party | Sophisticated bug detection & triage |
| GitHub integration | PR reviews, no native sandbox | @Codex mention → cloud sandbox |
| Long-running goal | Multi-tool stitching | Experimental /goal |
| Image generation | None (third-party) | Built-in GPT Image 2 |
| Enterprise hosting | Bedrock, Vertex, Foundry | Not specified |

Reading that table, you'd expect them to feel like very different products. They do. But not in the ways you'd predict from the bullet points alone, which is why I had to actually run the work.

## The Test Setup, And Why These Three Jobs

I picked the three tasks deliberately. Each one stresses a different muscle.

The **research report** tests structured document generation — long-form writing with citations, formatted output, and a final PDF render. This is the job most freelancers underestimate. It looks like "just write a doc," but it actually requires the model to plan a structure, hold dozens of sources in working memory, and produce something a paying client would accept without revision. I asked both tools for a 20-page report on the state of agentic coding tools in May 2026, formatted as PDF with a cover page, table of contents, citations, and a section on market consolidation predictions.

The **landing page** tests front-end UI generation with brand-grade polish. This is the job that separated good models from impressive ones eighteen months ago, and now separates impressive models from production-ready ones. I asked for a landing page for a fictional product called "Throughline" — an AI meeting summary tool — with a hero, three feature sections, social proof, pricing, and a footer. No design system specified. The model had to make taste decisions.

The **marketing analytics dashboard** tests the hardest job of the three: a full interactive front-end with charts, filters, state management, and realistic-looking data. I asked for a Throughline analytics dashboard with weekly meeting volume, summary engagement rates, a search panel, a leaderboard, and a settings drawer. Multiple components, real interactivity, the kind of build I'd quote at 4-6 hours of senior front-end time.

Same prompt to both. Same starting state. Same machine. I logged token consumption, wall-clock time, output quality, and the number of times I had to intervene to get the agent unstuck.

## Task One: The Research Report

I started both runs at the same moment by triggering them in parallel terminals. Claude Code on the left, Codex on the right.

Codex pulled ahead in the planning phase immediately. The `/goal` command on Codex picked up the prompt, decomposed it into a research outline with eight sub-topics, kicked off web searches for current sources, and started populating sections within the first ninety seconds. The structure it produced upfront was tight — the kind of outline I'd write myself if I had thirty minutes to think about the report before opening a document.

Claude Code, by contrast, opened with a planning conversation. It asked me to clarify the audience tier (CTO buyers vs developer audiences), the citation style (academic vs blog-style), and whether the predictions section should be conservative or speculative. Useful questions — and exactly what `/ultraplan` is designed to surface — but they cost me about three minutes of input I hadn't budgeted. Once that was settled, Claude went deep on each section with longer paragraphs, more transitions, more rhetorical structure.

The final deliverables looked different in revealing ways. Codex's report ran 19 pages, was citation-heavy with 34 sources, and read like a McKinsey briefing — short paragraphs, clear headers, dense bullet points, an executive summary up front. Claude's report ran 26 pages, had fewer sources (22), and read like a longform essay — flowing paragraphs, narrative arcs, fewer bullets. Both were genuinely good. They were just optimized for different reading contexts.

Time: Codex finished in 7 minutes, 22 seconds. Claude Code finished in 11 minutes, 4 seconds.

Tokens: Codex burned roughly 1.8M tokens. Claude burned roughly 3.1M, the larger budget coming entirely from longer output sections. Same task, very different output volumes.

PDF render: This is where I noticed the first divergence I hadn't predicted. Codex piped the output directly through its built-in PDF generation flow and handed me a finished file. Claude Code wrote the markdown, then generated a Pandoc command, then needed me to confirm a system prompt about installing missing dependencies. Faster for Codex on the last mile, by maybe 90 seconds.

If your week involves a lot of client-facing reports — quarterly reviews, market analyses, audit summaries — that PDF pipeline matters more than the underlying writing quality. The Codex round-trip from "I need a report on X" to "here's the PDF in your downloads folder" is materially shorter today. I noted this for myself and moved on.

## Task Two: The Landing Page For Throughline

Claude Code got its first clear win here, and it wasn't subtle.

I gave both tools the same prompt: build a marketing landing page for Throughline, an AI meeting summary tool, with a hero section, three feature blocks, a testimonials/social proof row, a pricing section, and a footer. Use Tailwind. Make it look like the kind of page you'd see from a Series A SaaS company.

Codex shipped a working page in 4 minutes, 11 seconds. The structure was correct, the sections were all present, the copy was passable. The visual language was — and I'm being fair here — competent. It looked like a 2023 SaaS template. Centered hero with a gradient background, three-column feature row with icons, a generic pricing table. Nothing wrong with it. Nothing memorable about it either.

Claude Code took 6 minutes, 38 seconds. Then it kept going for another 90 seconds polishing. The result was a different category of output. The hero section had asymmetric typography with a lowercase wordmark, the gradient was a noise-textured radial that I'd actually keep, the feature sections used alternating image-left/image-right layouts with subtle parallax hints, the social proof row used a marquee of logos that scrolled on hover, and the pricing section had a "most popular" tier with a soft shadow lift that came from the actual brand palette rather than a generic accent color.

I'm not exaggerating when I say I'd ship the Claude Code output to a client without revision. The Codex output I'd revise for half an hour first.

This tracks with everything I've written about Opus 4.7's design instincts in [the Opus 4.7 vs GPT 5.4 vs Gemini 3 Pro breakdown](https://www.mejba.me/blog/claude-opus-4-7-vs-gpt-5-4-vs-gemini-3-pro). When the task involves taste decisions about visual hierarchy, color, motion, and rhythm — Claude Code is still the front-runner in this generation. It's not even a close race on raw aesthetic output.

But here's the catch I want to flag: Claude Code burned almost twice the tokens to get there. Roughly 1.4M tokens versus Codex's 780k. If you're cost-sensitive and the output quality difference doesn't translate to client revenue, Codex is the rational pick. If you're charging clients real money for landing pages and the visual difference shows up in conversion rates, Claude Code's premium is justified.

This is the first place where the cost-efficiency story for Codex starts to crystallize. It's not that Codex is sloppy — it's that Codex stops at "competent and shipped" while Claude Code keeps polishing until "memorable and shipped." Different output philosophies. Both legitimate. Pick the one that matches what your buyers actually pay for.

## Task Three: The Dashboard, And The Moment I Almost Stopped The Test

I queued the analytics dashboard build expecting it to be the longest task of the three. I was wrong about which tool would surprise me.

Claude Code finished the dashboard in 2 minutes, 4 seconds.

I rewatched the timer to make sure I hadn't misread it. Two minutes. From prompt to working interactive dashboard with weekly meeting volume chart, summary engagement panel, search box, leaderboard, and settings drawer. The data was synthetic but realistic. The charts rendered cleanly on first load. The filter logic worked. State persisted across the drawer toggle.

The token count was the part that broke my mental model. Claude Code burned roughly 283,000 tokens for that entire dashboard. Two minutes, 283k tokens, working dashboard.

Codex took 8 minutes, 11 seconds and burned roughly 1.64M tokens for an arguably equivalent dashboard. The output was good — fully functional, clean component structure, sensible state management. But the runtime gap and the token gap on this specific task were both larger than anything I've seen between these tools on previous builds.

I want to be careful here because one task is not a trend. But the pattern I saw repeated across the three jobs is worth naming: **Claude Code is dramatically faster on heavy front-end builds, and dramatically slower on long structured documents. Codex flips that.** They're not converging into a single "best agentic coder" — they're specializing in opposite directions.

This is also where the [/ultraplan and /ultrareview commands](https://www.mejba.me/blog/claude-code-32-power-user-hacks) earned their seat at the table. Before the dashboard build, I ran `/ultraplan` on the prompt. The plan that came back broke the build into a layout shell, a data layer with mocked time-series, four chart components, a filter store, and a settings panel — and explicitly noted which pieces should be auto-delegated to Sonnet sub-agents to keep Opus focused on the orchestration. That auto-delegation is most of why the runtime collapsed. Five sub-agents working in parallel on isolated component scopes, with Opus stitching the result together. Codex's `/goal` command does something similar in spirit, but the sub-agent dispatch isn't as automatic — you tend to nudge it more.

If your week involves a lot of dashboards, internal tools, admin panels, or any kind of interactive front-end build, this is where Claude Code's auto-delegating sub-agent architecture pays off in literal minutes of your life. The hook event count (30 vs ~6) maps directly to this — more lifecycle injection points means more places to intervene, observe, and customize without breaking the agent's flow.

## The Aggregate Numbers Across All Three Tasks

After the three runs were done I let the dust settle and pulled the totals.

| Metric | Claude Code (Opus 4.7) | Codex (GPT-5.5) |
|---|---|---|
| Context window | Up to 1,000,000 tokens | ~256,000 tokens |
| Total runtime (3 tasks) | ~15 min | ~26 min |
| Token consumption (3 tasks) | ~6M | ~6M |
| Dashboard build | 2 min, ~283k tokens | 8 min, ~1.64M tokens |
| Research report + landing | Slower | Faster |
| Output token volume | 2–5x higher | More concise |
| Cost efficiency | Higher cost (more output) | More cost-efficient |

Total token consumption across the three tasks landed almost identical at around 6M tokens each. But the distribution across tasks was inverted. Claude Code spent more on documents, less on UI. Codex spent more on UI, less on documents. The aggregate was equal. The lived experience was completely different depending on which task you were running.

On wall-clock time, Claude Code finished the suite in about 15 minutes total. Codex took about 26 minutes. That's an 11-minute gap, which is roughly the difference between "I'll wait at my desk" and "I'll grab coffee and come back." On any given task it might flip — Codex won the report by four minutes, Claude won the dashboard by six minutes — but in aggregate, Claude Code shipped the suite faster.

On cost-efficiency, Codex is the more disciplined operator. It produces more concise output, hits fewer dead ends on simpler tasks, and lands the ball in the goal with fewer tokens per unit of value. Claude Code's output is 2-5x longer on average — sometimes that translates to material quality (the landing page), sometimes it's just verbose (the research report). If your bill is metered by token usage and your buyers don't pay a premium for verbose output, Codex is the cheaper engine per finished job.

The context window difference is real but less impactful than I expected. Claude Code's 1M token window matters when you're throwing an entire monorepo at the agent and asking it to refactor across files — I've used it for exactly that, and it's transformative. For the three tasks in this test, neither tool hit a context wall. 256k was plenty for everything I threw at it. If you're not doing whole-codebase reasoning, the 1M number is a spec sheet bullet point, not a workflow advantage.

## Where Each One Earned My Trust (And Where It Didn't)

I'm going to write this in plain terms because the bullet-point version reads like every other AI tool comparison and you've already read those.

**Claude Code earned my trust on heavy front-end work, deep planning, and any job where output quality scales with token spend.** The landing page wasn't just better-looking — it was better in a way that would translate to client revenue. The dashboard wasn't just faster — the auto-delegation pattern is the kind of architectural advantage that compounds over a workweek. If you write any kind of custom workflow with [Claude Code hooks](https://www.mejba.me/blog/claude-code-32-power-user-hacks), if you're embedding agents in your own products through the Agent SDK, if you're brainstorming at the strategy level and need a thinking partner before a coding partner, Claude Code is where I'd start.

It didn't earn my trust on the last mile of structured documents. The PDF pipeline still requires me to confirm Pandoc paths and dependency installs more often than I'd like. For client-facing reports where the final file matters more than the prose inside it, Codex's integrated render is the smoother experience.

**Codex earned my trust on research-heavy structured docs, end-to-end shipping, and any workflow that touches GitHub.** The `@Codex` GitHub integration deserves its own paragraph: I tagged Codex on a PR review in my own repo during the test window, walked away, and came back to a thoughtful review with line-by-line comments and three suggested edits. Cloud sandbox spun up automatically. No setup. That workflow alone is worth the Plus subscription for anyone who runs more than two repos. The native Git worktree support means I can have multiple Codex sessions working on parallel branches without trampling each other — which is a workflow I'd previously built manually with [Claude Code git worktrees](https://www.mejba.me/blog/claude-code-git-worktrees-parallel-agents) and which Codex now ships as a first-class primitive.

The in-app browser with inline comments is the feature I didn't think I'd care about and now refuse to give up. When I'm reviewing a design or a deployed page, being able to highlight a section in the browser and add a comment that the agent picks up as context is the kind of workflow detail that saves twenty context switches a day.

It didn't earn my trust on visual polish. The landing page output was fine. Fine is not what I sell. For UI work that gets judged on aesthetics, I'd run the same prompt through Claude Code and use the Codex output as a reference.

The computer-use QA capability is genuinely strong. I asked Codex to find bugs in the landing page it had just built and it spotted a broken anchor link and a CTA hover state that didn't trigger on mobile. Claude Code can do similar work through external tooling but it isn't as polished or as fast as Codex's first-party computer-use flow.

The built-in GPT Image 2 generator is the kind of thing that sounds minor until you need it. Generating a hero image for the Throughline landing page took one prompt and stayed inside the Codex session. With Claude Code, that's a separate trip to a third-party image tool and a copy-paste back. Small workflow tax, but it adds up.

## The Subscription Math And A Note On Anthropic's Restrictions

Pricing is where I want to put a flag down for anyone making a purchasing decision.

Claude Code Pro is $20/month. Max 5X is $100/month. Max 20X is $200/month. The Max tiers buy you more usage allowance and priority access to Opus during high-demand windows. If you're using Claude Code as your primary coding tool five days a week, Max 5X is the floor — you'll hit the Pro tier limits inside of two days of heavy work.

Codex is included with the ChatGPT free tier (limited usage), Plus at $20/month, and Pro at $200/month where usage is effectively unlimited. The current promo on a $100 tier doubles Codex usage through May 31 — if you're already on Plus and considering an upgrade, that's the math to run before the promo expires.

Two things to know about Anthropic that don't show up in the pricing table. First, Anthropic restricts third-party use of your Claude subscription — you can't, for example, embed your personal Pro plan inside a product you're shipping to your own customers. The Agent SDK and Bedrock/Vertex/Foundry deployments are the official path for that, and they're billed separately. Second, OpenAI is more permissive on subscription-bundled use, which is part of why you see more indie hackers shipping Codex-powered side projects on consumer plans. Neither posture is wrong. They're different business models, and they affect what you can legally do with the tools you're paying for. Read the terms before you build a product on top of either one.

## How I'm Actually Using Both Now

Here's the workflow I landed on after this test, which I've been running for the last three weeks and which has materially shortened my client work.

When a job starts with strategy — figuring out what to build, planning architecture, brainstorming UX flows, deciding on tech stack — I open Claude Code. The `/ultraplan` command is the closest thing I have to a senior engineering partner who's actually paying attention. The planning conversation that opens a Claude Code session is consistently better than what I get out of any other tool, including Codex.

When that plan turns into UI work — landing pages, dashboards, internal tools, anything where taste decisions matter — I stay in Claude Code. Auto-delegating sub-agents make the build feel fast even on dashboards with five interactive components. The visual output is consistently the kind of thing I can ship without revision.

When the job pivots into structured documentation — research reports, audit summaries, client briefs, anything that needs a clean PDF at the end — I switch to Codex. The `/goal` command on structured docs is faster than anything I've seen, and the integrated PDF pipeline saves the last-mile friction that Claude Code still has.

When the job touches GitHub — PR reviews, multi-branch parallel work, anything where the cloud sandbox earns its keep — Codex is the default. The `@Codex` mention flow on PRs is too good to give up.

When I need a hero image, a marketing asset, or any kind of generated visual that's going into the build — Codex stays open because GPT Image 2 is in the box. I still use [Higgsfield for the higher-end product photoshoots](https://www.mejba.me/blog/ai-video-creation-hyperframes-claude-code), but for fast inline image work, Codex is enough.

This mixed-stack approach is the part I want to underline. The two tools are not competing for the same seat at my desk. They're occupying different seats. The question "Claude Code or Codex?" is the wrong question. The right question is "which one for this specific kind of work?" And once you know the answer for your own workload, you stop choosing and start switching.

If you're running a lean stack and can only afford one, here's my honest call: if your week is mostly UI work and you charge clients for visual quality, Claude Code Max 5X is the better $100. If your week is mostly research, documentation, and GitHub-mediated team work, Codex Plus at $20 is the better deal and gets you 90% of the value.

If your week is both — and most professional developer weeks are — pay for both. Plus and Max 5X together is $120/month for what amounts to two senior engineers on retainer. There's no other line item in my business that returns that much value per dollar.

## The One Thing I'd Tell Past-Me About This Comparison

Six months ago I would have written this same post and called Claude Code the winner. The visual output was meaningfully better, the planning was deeper, the workflow customization was unmatched.

Today I can't write that post honestly. Codex closed the gap on most of the workflow features I used to call decisive, and opened a gap of its own on GitHub integration, cloud sandbox, computer-use QA, and integrated image generation. The thing I would tell past-me is that the right question stopped being "which tool is better" sometime around Q1 2026, and the people still asking it are about to be outshipped by the people who learned to switch.

There are still distinctive strengths. Claude Code is the better thinking partner. Codex is the better executor. Claude Code wins on UI polish and customization depth. Codex wins on end-to-end shipping and integrated workflow primitives.

If you've been waiting for one of them to obviously win so you can stop tracking the other — that's not the timeline we're on. The next twelve months are going to be a sustained back-and-forth where each release closes one gap and opens another. The developers who win this stretch are the ones who keep both tools open, keep their muscle memory current on both, and stop treating tool choice as an identity question.

The Tuesday morning experiment I started to settle this comparison didn't settle anything. It just gave me a sharper map of when to use which engine, which has been worth roughly six hours of saved work in the three weeks since. If you want the same map for your own workflow, the only way to draw it is to run your own three tasks through both stacks back to back. Pick the work you actually do for money. Run it twice. Watch what each tool does well and where each one breaks.

The honest answer to "Claude Code or Codex" in May 2026 is: yes. Both. And if your budget forces you to pick one, pick the one that matches the work you ship most weeks — not the one with the louder release notes.

## Frequently Asked Questions

### Which is better, Claude Code or Codex, for solo developers in 2026?
For solo developers, the right pick depends on the work mix — Claude Code is stronger for UI-heavy weeks and deep planning, while Codex is stronger for research docs, GitHub-mediated review work, and end-to-end shipping. If you can only afford one and your work skews visual, take Claude Code Max 5X at $100/month. If your work skews structured documentation and team workflows, take Codex Plus at $20/month.

### Is Claude Code faster than Codex?
Claude Code finished the three-task suite in about 15 minutes versus Codex at 26 minutes in my test, with the gap concentrated on the dashboard build where Claude's auto-delegating sub-agents collapsed the runtime to 2 minutes. Per-task, the answer flips — Codex was faster on the research report by about 4 minutes. Faster depends on what you're building. See the dashboard section above for the breakdown.

### Does Claude Code or Codex have a bigger context window?
Claude Code supports up to a 1,000,000 token context window with Opus 4.7. Codex with GPT-5.5 runs at approximately 256,000 tokens. For whole-codebase reasoning, Claude Code's window is materially larger. For typical task-scoped work like landing pages or single dashboards, both windows are sufficient.

### Can I use OpenAI Codex without a separate API key?
Yes — Codex is bundled with every ChatGPT subscription tier, including the free plan. Plus ($20/month) and Pro ($200/month) raise the usage limits. No separate API key or billing setup is required. The same is true for Claude Code, which is bundled with Anthropic Pro, Max 5X, and Max 20X plans.

### Does Codex support Git worktrees and parallel agents?
Yes — Codex now has native Git worktree support, letting you run multiple agent sessions on parallel branches without conflict. Claude Code supports the same workflow but historically required manual worktree setup, which I covered in the [Claude Code git worktrees guide](https://www.mejba.me/blog/claude-code-git-worktrees-parallel-agents). Codex ships it as a first-class primitive in the May 2026 release.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
