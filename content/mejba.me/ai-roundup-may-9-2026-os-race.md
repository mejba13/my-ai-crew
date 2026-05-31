**BRAND:** mejba.me
**TITLE:** AI Roundup May 9 2026: The Chatbot-to-OS Race Is On
**META TITLE:** AI Weekly May 9 2026: Codex, Claude, ERNIE, Grok Moves
**SLUG:** ai-roundup-may-9-2026-os-race
**PRIMARY KEYWORD:** AI roundup May 9 2026
**META DESCRIPTION:** Codex Chrome, GPT-Realtime-2, Claude finance agents, ERNIE 5.1 at 6% training cost, Grok connectors. What I'm tracking and why builders should care.
**TAGS:** AI Weekly, OpenAI Codex, Anthropic Claude, Baidu ERNIE, xAI Grok

---

# AI Roundup May 9 2026: The Chatbot-to-OS Race Just Got Real

Friday morning, 6:41 AM. I had four browser windows open and a half-formed argument in my head.

Codex was running an autonomous task in Chrome on my left monitor — opening Salesforce, scraping a contact list, drafting outreach. I had not touched the keyboard in eleven minutes. On the right, Claude Cowork was building a comp model for one of my brands' Q2 reporting, pulling FactSet data through a connector that did not exist last week. In the corner, Grok was crawling my Notion workspace looking for a draft I half-remembered writing in February. And in the background, somewhere in a tab I had forgotten about, a leaked court text exchange between Sam Altman and Mira Murati was burning across X.

That is what this AI roundup May 9 2026 looks like from inside my workflow: four labs, four totally different bets, all converging on the same product shape. The chat box is dying. The operating surface is being born. And almost nobody is asking the question that actually matters — which is whether the thing replacing it has any legitimacy with the people whose work it is replacing.

This is what I am tracking, what I think the press is missing, and what I am doing about it before Monday.

If you want context on how I usually sort weeks like this, my [signal-versus-noise breakdown of April's launch flood](https://www.mejba.me/ai-news-week-april-2026-signal-vs-noise) sets the frame.

## The Thesis: Three Companies Just Stopped Pretending This Is About Chatbots

There is a thing that happens in a fast-moving market where every player keeps releasing the same kind of feature on the same kind of cadence, and it looks chaotic from the outside, but if you squint, the actual move is one big synchronized swerve.

That is what this week was.

OpenAI shipped a Chrome extension that lets Codex run Salesforce, Gmail, and LinkedIn workflows in a separate browser instance the agent owns. Google, watching from the sidelines, started field-testing Gemini 3.2 Flash inside iOS without an announcement. xAI rolled Grok connectors live across Web, iOS, and Android, hooking into Gmail, Drive, Docs, Sheets, Calendar, Notion, GitHub, and Linear in one announcement. And Anthropic — playing what I am increasingly convinced is the smartest long game of the four — shipped ten ready-to-run financial-services agent templates that turn Claude into something closer to an analyst's workstation than a chatbot.

Four companies. One direction. Stop trying to win the chat box. Start trying to be the operating layer for knowledge work.

The reason this matters is downstream. If you run brands, ship code, write content, manage a small team — any of the things I do across my four sites — your assumptions about how AI shows up in your week are already obsolete. Three months ago, "AI agent" meant "I open a tab and type a prompt." This week, it means "an agent ran for forty minutes in a browser instance I cannot see, accessed three SaaS tools using my login session, and posted the result to Slack while I was at the gym." That is a different product. It deserves a different mental model.

The rest of this post is me sorting through which of those bets is real, which is teased, which is overhyped, and which one quiet announcement out of Beijing might matter more than all four of them combined.

Let me show you.

## Codex Just Became a Browser

I have been waiting for [Codex to control browser tabs in the background](https://www.mejba.me/codex-goal-command-autonomous-coding) since the `/goal` command shipped in version 0.128.0. It finally happened.

[On May 7, 2026, OpenAI launched the Codex Chrome extension](https://developers.openai.com/codex/app/chrome-extension) for macOS and Windows. It is not a tab takeover. It is a separate Chrome instance the agent owns, with its own tab groups, its own DevTools access, and its own ability to use your signed-in sessions on sites like Salesforce, Gmail, LinkedIn, and any internal tool with a browser interface. You keep working. The agent works in parallel.

Pair that with the [in-app browser, multiple terminal tabs, SSH connections to remote devboxes (in alpha), and Chrome DevTools integration](https://decrypt.co/364670/codex-computer-use-browser-image-gen-openai-super-app) that landed in the same update, and the picture sharpens fast. Codex is no longer a coding agent. It is a coding agent plus a browser plus a remote shell plus a long-running goal system.

According to OpenAI's own numbers, Codex now has [more than 4 million weekly active users — an 8x increase since the start of 2026](https://www.marktechpost.com/2026/05/08/openai-adds-chrome-extension-to-codex-letting-its-ai-agent-access-linkedin-salesforce-gmail-and-internal-tools-via-signed-in-sessions/). That growth is not because the model got better. It is because the surface got bigger. People are using Codex to do work that has nothing to do with code — outreach campaigns, research scrapes, dashboard updates, expense reports — because the agent can finally reach the tools where that work lives.

What I tested this week. I gave Codex three real jobs.

Job one: pull every paid invoice from my Stripe dashboard for the last 90 days, cross-reference against the expected MRR projection in a Google Sheet, flag the gaps. Time elapsed: 22 minutes. It got eighty-something percent of the way before hitting a Stripe permission prompt I had to clear, then finished. Output was correct.

Job two: read the last fourteen days of my Substack analytics, identify which posts are outperforming the trailing 90-day median, draft a Twitter thread teasing the top three with quotes from the bodies. Time elapsed: 11 minutes. Quality of the draft was better than the version I would have written, which is faintly humiliating.

Job three: open Salesforce, find every contact tagged "warm lead Q1 2026" who has not had a touch in 30+ days, draft personalized re-engagement emails that reference the last conversation thread. This one I babysat. It worked. I would not have shipped the emails without reading them, but the draft layer was real.

The honest assessment. The Chrome extension is the most useful thing OpenAI has shipped in 2026. It is also the most dangerous, because the failure mode of "agent with your Salesforce session" is much worse than "agent that gets a code snippet wrong." I am running it. I am also reading the audit log of every action it takes before I let it touch anything that costs money or has a customer's name on it.

Then there is voice, which OpenAI quietly turned into its own agent surface the same week.

## GPT-Realtime-2 Is the Voice Layer Most Builders Will Ignore (And Shouldn't)

[On May 7, 2026, OpenAI shipped GPT-Realtime-2](https://openai.com/index/advancing-voice-intelligence-with-new-models-in-the-api/), its first voice model with what the company is calling "GPT-5-class reasoning" — meaning the model can think through a multi-step request mid-conversation while keeping the audio stream live.

The headline numbers. The context window jumped from 32K to 128K, which means longer sessions and more complex agentic flows without external state stitching. The model can call multiple tools in parallel and narrate what it is doing — "checking your calendar, looking that up now" — while the work happens in the background. Pricing comes in at $32 per million audio-input tokens and $64 per million audio-output tokens, with cached input dropping to $0.40 per million.

OpenAI shipped two companions alongside it. GPT-Realtime-Translate handles 70+ input languages into 13 output languages at $0.034 per minute. GPT-Realtime-Whisper streams speech-to-text live at $0.017 per minute. I covered the [translate model and what it does for cross-border voice agents](https://www.mejba.me/gpt-realtime-2-translate-voice-agents) earlier this week, but the realtime-2 base model is the one most app builders will dismiss too fast.

Here is the thing nobody is saying out loud. Voice is the next chat-box-dies inflection. Most of the AI products I run for my brands today are typed conversations. That is going to look as quaint in eighteen months as IRC does today. Realtime-2 is the first voice model where the latency is low enough, the reasoning is deep enough, and the tool-calling is reliable enough that a non-coder small business owner could actually run a voice support agent on their site without it sounding like a robot reading a script.

I am building exactly that for one of my brands this month. The bet is not that voice replaces text — it is that voice plus text plus background browser agents collapse into one assistant surface, and whoever owns the latency floor on the voice side wins the surface.

OpenAI just made a real bid for that floor.

## Anthropic's Counter-Move: Depth Over Breadth

While OpenAI was building the everything-app, Anthropic shipped something almost exactly the opposite. And I think it might be the smarter bet.

[On May 5, 2026, Anthropic launched ten ready-to-run AI agent templates for financial services](https://www.anthropic.com/news/finance-agents), available as plugins in Claude Cowork and Claude Code, and as cookbooks for Claude Managed Agents. The list is specific in a way that matters: a pitch builder, a meeting prep tool, an earnings reviewer, a financial model builder, a comparable-companies engine, a general ledger reconciler, a month-end closer, a financial statement auditor, a KYC screener, and an escalations handler.

That is not a horizontal product play. That is one vertical, fully covered.

The data side is where the bet sharpens. The [Anthropic finance agents announcement](https://www.anthropic.com/news/finance-agents) lists connector partners across [FactSet, S&P Capital IQ, MSCI, PitchBook, Morningstar, Chronograph, LSEG, Daloopa](https://www.fundssociety.com/en/news/business/anthropic-launches-ten-ai-agents-for-banking-and-asset-management-professionals/), plus newer additions like Dun & Bradstreet, Fiscal AI, Financial Modeling Prep, Guidepoint, IBISWorld, SS&C IntraLinks, Third Bridge, and Verisk. Moody's launched a separate MCP app that surfaces proprietary credit ratings and data on more than 600 million public and private companies. And the same week, Era became [the first personal finance connector in the Claude directory](https://ca.finance.yahoo.com/news/era-becomes-first-personal-finance-140000473.html), built on the open MCP protocol.

I am not running a hedge fund. None of this directly applies to my work. So why do I keep coming back to it?

Because the strategy is the part that scales. Anthropic is not trying to be everything. They are picking a vertical, owning the data partnerships, building the templates, and letting the agent be the smartest analyst in that one specific domain. If they ship a similar pack for legal next quarter, healthcare after that, manufacturing after that — each pack with its own connector ecosystem, its own templates, its own verticalized prompts — they end up with depth that horizontal players cannot match.

The AI press is treating these as enterprise wins for revenue. I think they are something else. I think Anthropic just published the playbook for how a frontier-model company beats a generalist by going narrow and deep, one industry at a time. Watch which vertical they hit next. Whatever it is will tell you where the second compounding moat starts.

If you build for any specific industry — and most of us do, even if we do not realize it — this is the structural template worth copying. Pick one vertical. Build the connectors. Ship the templates. Let the model be smart but let the data and the workflows be specific.

Speaking of moats: the one quiet announcement out of Beijing might unmake all of them.

## The Real Story Nobody Is Headlining: ERNIE 5.1 at 6% the Training Cost

If I had to pick the single most consequential announcement of the week — the one most likely to reshape the cost curve for the next eighteen months — it would not be Codex Chrome and it would not be Claude finance agents. It would be a model release out of Baidu that the English-language press half-covered and then forgot about by Tuesday.

[ERNIE 5.1 Preview launched on April 30, 2026](https://ernie.baidu.com/blog/posts/ernie-5.1-0508-release/). Within five days, it had climbed to #13 on LMArena's Text Arena leaderboard with an Elo of 1,476, ranking #1 among all Chinese AI models, [#1 globally in Legal and Government categories, #4 in Business Management and Financial Operations, and #7 in Software and IT Services](https://www.remio.ai/post/baidu-ernie-5-1-hits-no-1-chinese-model-on-lmarena-built-at-6-of-the-normal-training-cost).

Those numbers are good. They are not the story.

The story is in the parameter math. ERNIE 5.1 compressed total parameters to roughly one-third and active parameters to roughly one-half of ERNIE 5.0. And it achieved the leading foundational performance at its model scale using approximately **6% of the pre-training cost of comparable models**. Six percent. Not sixty. Six.

If you are a builder, that number should make you sit up.

Here is why. The dominant assumption baked into every frontier-lab valuation, every GPU contract, every datacenter buildout — Stargate, the $500B Microsoft commitment, the new Coreweave facilities — is that frontier capability requires frontier compute, and frontier compute requires frontier capital. That is the moat. That is the gating function. That is what gives Anthropic and OpenAI and Google their pricing power.

A 6% pre-training cost claim, if it generalizes — and that is a real if — knocks the bottom out of that assumption. It means a competently-funded lab in any country can ship frontier-tier text capability for less than the marketing budget of a single Super Bowl ad. It means the cost floor on text intelligence is collapsing, fast. It means the moat at the model layer is leaking.

What this means for builders downstream. I do not run model training. Neither do most of you. But the cost curve at the bottom of the stack determines the API prices at the middle of the stack, which determines the unit economics at the top of the stack — which is where I and most of you live. If ERNIE-style efficiency techniques propagate to the open-source community in the next two quarters (and based on what happened after [DeepSeek-V4 Pro shipped under MIT license last quarter](https://www.mejba.me/deepseek-v4-pro-open-source-ai-review), I expect they will), the price-per-million-tokens curve drops another order of magnitude.

That is the story I am tracking. Not "who shipped the best demo this week." Who is bending the cost curve fastest.

If you build at the application layer — apps, agents, content systems, automations — your strategic question stops being "which model do I bet on" and starts being "which architecture do I build that survives a 10x price drop in the underlying model every nine months." That is a different question with a different answer.

Now to the model that might be quietly losing ground.

## Gemini's Strange Week and What to Do When a Model You Depend on Drifts

Reports surfaced this week that Gemini 3 Pro and the unreleased 3.5 Pro have been "nerfed hard" — fewer follow-throughs on long context, weaker first-pass code generation, regressions on reasoning chains that worked a month ago. Whether these reports are real measurement or user-noise is genuinely unclear. The community thread on the [Gemini Apps support forum](https://support.google.com/gemini/thread/395392671/it-seems-that-gemini-3-0-has-reduced-performance-compared-to-previous-versions) is full of complaints, and at least one credible voice on X is calling for Google to ship something significant in the next two weeks or risk losing momentum.

Add to this Google's [March 9, 2026 sunset of Gemini 3 Pro Preview](https://www.threads.com/@bassey__j/post/DVpyVntjDJn) — only four months after the model launched — and you have a pattern. Model lifecycles are now measured in weeks. The upgrade treadmill is real. Builders who picked Gemini for production workflows in late 2025 have already had to migrate twice.

Meanwhile, [Gemini 3.2 Flash quietly appeared inside the iOS app and AI Studio on May 5, 2026](https://www.buildfastwithai.com/blogs/gemini-3-2-flash-release-2026) with no press release, showing strength on SVG generation, coding, and animation. I covered the [Gemini 3 Flash stealth upgrade pattern](https://www.mejba.me/gemini-3-flash-stealth-upgrade-lmarena) earlier this quarter, and the playbook is identical. Google's strategy is clearly the cheap-fast-tier squeeze rather than premium-flagship dominance.

The lesson for builders is the one I learned the hard way in 2025. Never depend on a single model for a workflow that has to ship reliably across a quarter. Build your agent stack so the model is a swappable variable. Pin your prompts to behavior, not to a specific model name. Run the same eval suite against every new release that lands in your stack so you catch regressions before your customers do.

When Gemini 3.1 Pro feels off this week, swap to Opus 4.7 or Sonnet 4.8 and keep shipping. When Sonnet drifts, swap to GPT-5.5. The model is a commodity input now. Treat it that way.

## Grok Becomes a Productivity App

xAI shipped its connectors play this week, and on the surface it looks like a cleaner version of what Codex and Cowork already do. [Connectors went live on May 6, 2026](https://x.ai/news/grok-connectors) for Web, iOS, and Android, hooking into Google Workspace (Gmail, Drive, Docs, Sheets, Calendar), Notion, GitHub, Linear, and any custom Model Context Protocol server through "Bring Your Own MCP."

I tested it for two days. The UX is smoother than I expected. The latency is good. The ability to drop a custom MCP server into Grok and have it just work is genuinely impressive — I plugged in an internal MCP I built for one of my agencies and Grok handled it without configuration friction.

But here is my honest take. Grok is following, not leading. Every connector on the list ships in Cowork or Codex or both. The one differentiator — Grok being inside X's main feed, with whatever weird viral lift that brings — is also the thing most builders are not optimizing for. Most of us are not trying to win on X virality. We are trying to ship.

If you live inside X already, Grok connectors are a quality-of-life upgrade. If you do not, this is not the week to migrate. Watch what xAI does next quarter — if they ship something Codex and Cowork have not, the calculation changes.

For now, my Grok usage is unchanged. I keep it open for one specific job (low-cost research with web access), and the rest of my work runs on Claude Code and Codex. Your stack should reflect what each tool is best at, not what is newest.

I covered [where Grok actually fits in a multi-agent stack](https://www.mejba.me/ai-model-tool-roundup-april-2026-kimi-spud-grok) in last month's roundup, and the answer this month is the same. It is a useful side-tool, not a primary surface.

## The Two Stories the AI Press Is Underweighting

I want to spend the rest of this post on the two stories that did not make the front page but might shape the next year more than anything else this week.

### Story One: The Mira Murati Court Texts and What They Actually Mean

This week, in the ongoing Musk v. Altman trial, [a text exchange between Sam Altman and Mira Murati from the night of November 19, 2023 was entered into the record](https://www.aol.com/articles/still-dont-want-trial-reveals-152214752.html). Altman, freshly fired by the OpenAI board two days earlier, was pinging Murati — who was in the board meeting that would decide whether to install Emmett Shear as the replacement CEO — for inside intelligence.

His message: "Can you indicate directionally good or bad?"

Her reply: "directionally very bad."

Within hours, Altman had organized the petition that 600 OpenAI employees signed, threatening to defect en masse to Microsoft. Within days, he was reinstalled. Within weeks, the board members who had voted for his removal were the ones gone.

The new revelation, the one that makes this week's leak meaningful and not just historical, is the [reporting that Murati had funneled significant information — screenshots, documentation of text messages, allegations of mismanagement — to cofounder Ilya Sutskever, who built it into the 52-page memo](https://gizmodo.com/ex-openai-cto-mira-murati-testifies-about-sam-altman-allegedly-lying-to-her-2000755394) that triggered the original board action.

She was not just the CTO. She was a primary witness for the case against him.

Why this matters now. Murati left OpenAI in September 2024 to found [Thinking Machines Lab](https://techcrunch.com/2026/01/14/mira-muratis-startup-thinking-machines-lab-is-losing-two-of-its-co-founders-to-openai/), which raised a $2B seed round but lost three co-founders back to OpenAI in January 2026. The reading I keep coming back to is that the entire AI executive class is locked in a war over the same shrinking talent pool, and the legal-evidentiary trail of who said what to whom in November 2023 is going to keep showing up in court rooms and press cycles for the next eighteen months.

For builders, the lesson is not gossip. It is governance. The companies you depend on for foundational infrastructure are run by people whose private text messages from three years ago are now being entered into evidence. That is a reminder to never bet your business on a single API. Your stack should survive any one of these labs imploding. Build accordingly.

### Story Two: The Anti-Clanker Backlash Is Going Mainstream

The other story that did not get enough coverage. The "clanker" slur — originally a Star Wars term, [now used as a derogatory label for AI and robots](https://en.wikipedia.org/wiki/Clanker) across TikTok, X, and increasingly in real-world rallies — has crossed over from internet slang into actual movement.

The numbers from [NBC's reporting](https://www.nbcnews.com/tech/internet/ai-backlash-brewing-clanker-says-growing-frustrations-emerging-tech-rcna222231) and [Substack-tracked incident logs](https://adinonline.substack.com/p/the-rise-of-clanker-attacks-documenting): documented anti-robot incidents have escalated from 16 major events in 2023 to over 40 in 2026. Real-life rallies are happening in San Francisco and London. Starship Technologies' delivery robots have been [systematically vandalized in Sheffield, UK](https://www.nbcnews.com/tech/internet/ai-backlash-brewing-clanker-says-growing-frustrations-emerging-tech-rcna222231) since March, with attackers spray-painting machines and bending identification poles.

The polling numbers underneath the movement are the part that should worry every founder in this space. An [Ernst & Young report from July 2025](https://www.nbcnews.com/tech/internet/ai-backlash-brewing-clanker-says-growing-frustrations-emerging-tech-rcna222231) found 42% of European employees worry that workplace AI threatens their jobs. A Gartner survey found 64% of customers prefer companies not use AI for customer service, and 53% would switch to a competitor that did not.

This is the consent gap. Capability is racing forward. Cultural and political consent is lagging — and the gap is now wide enough that the resentment has its own slang, its own street rallies, and its own attack patterns.

The takeaway for builders. If your product is "AI-powered" and you brag about it on the front page, you are on the wrong side of the cultural curve as of right now. The companies that will win the next 24 months are the ones that ship products which are obviously useful and quietly AI-driven, not the ones that lead with "now powered by GPT-5.5." Watch how Anthropic positioned its finance agents this week — the messaging is "your team can now do X faster." Not "AI replaces your analyst." That framing is not an accident. It is the only framing that survives.

I am rebranding two product pages this weekend with this lesson in mind. I would suggest you audit yours.

## What I'm Actually Doing This Week As a Builder

This is the section that should justify this post existing. Five concrete moves I am making before Monday based on what shipped this week.

**One: I am moving my email triage and outreach drafting workflow to Codex Chrome extension.** The 22-minute Stripe-and-MRR job was the proof point. I will run this on a sandboxed Chrome profile with no payment credentials saved, and I will read every audit log before I trust the agent with anything that touches a customer. I expect to save four to six hours a week within a month.

**Two: I am building a voice agent on GPT-Realtime-2 for one of my brands.** The 128K context plus parallel tool-calling is the threshold I have been waiting for. I will pair it with an MCP that hits the brand's CRM, calendar, and Stripe. Goal: voice-first booking and support for clients who hate forms. Budget: $200 in API spend for the test, kill it if call quality is anything less than acceptable.

**Three: I am NOT migrating any mejba.me automations to Grok connectors.** The connectors are nice. They are not better than what I already have running on Claude Code with custom MCP servers I built last quarter. The migration cost is not worth a 5% UX improvement.

**Four: I am running the Anthropic finance-agent template stack against my own brands' bookkeeping for the rest of May.** Not because I run a hedge fund. Because I want to see whether a vertical agent pack outperforms a generalist agent on a structured workflow that actually maps to my brand operations. If it does, I copy the template-pack pattern for content creation, my actual core competency.

**Five: I am rewriting the front page of two product pages to remove every "AI-powered" claim.** Lead with the outcome. Bury the technology. Let the work speak.

If you take one thing from this entire post, take that fifth one. The market is shifting. The labs are racing toward an OS-shaped product. The cost floor is collapsing. The cultural consent is fraying. In that environment, the founders who win are the ones who ship value people can feel and stay quiet about how it gets made.

That is what I am tracking. That is what is changing Monday. See you next week.

## Frequently Asked Questions

### What is the most important AI announcement from the week of May 9 2026?
The single most consequential release was Baidu's ERNIE 5.1, which achieved leading foundational performance at its model scale using approximately 6% of the pre-training cost of comparable models. It launched on April 30 and climbed to #13 on LMArena's Text Arena within a week. The cost compression matters more than any individual model demo because it signals where the price-per-token floor is heading across the industry.

### Is the Codex Chrome extension safe to use?
The Codex Chrome extension is technically safe but operationally risky. It runs in a separate Chrome instance the agent owns, with audit logs for every action, but it can use your signed-in sessions on Salesforce, Gmail, LinkedIn, and similar tools. Run it on a dedicated browser profile, never store payment credentials in that profile, and review the audit log before trusting it with anything customer-facing.

### What is GPT-Realtime-2 and should I build with it?
GPT-Realtime-2 is OpenAI's voice model with GPT-5-class reasoning, a 128K context window, and parallel tool-calling at $32 per million audio-input tokens and $64 per million audio-output tokens. It is the first voice model where latency, reasoning depth, and tool reliability hit production thresholds simultaneously. Build with it now if voice is core to your product. If not, monitor the price curve through Q3 before committing.

### Did Gemini 3 Pro really get nerfed in May 2026?
Reports of degraded Gemini 3 Pro performance circulated widely this week, with multiple users on X and the Gemini Apps support forum reporting weaker reasoning chains and code generation. Whether this reflects an actual RLHF tuning round or user-perception drift is unclear. Either way, the lesson is the same: never depend on a single model for production workflows. Build your stack so the model is a swappable variable.

### What does Anthropic's financial services agent launch mean for non-finance builders?
Anthropic shipped ten ready-to-run financial-services agent templates with deep data-partner connectors (FactSet, S&P Capital IQ, MSCI, Morningstar, Moody's, and more). The strategic template — pick one vertical, own the data partnerships, ship vertical-specific templates — is more important than the announcement itself. Expect Anthropic to repeat this pattern in legal, healthcare, and manufacturing in the next two quarters.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
