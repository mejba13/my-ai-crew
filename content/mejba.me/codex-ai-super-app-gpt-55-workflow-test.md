**BRAND:** mejba.me
**TITLE:** Codex AI Super App: GPT-5.5 Workflow Test
**META TITLE:** Codex AI Super App on GPT-5.5: Tested Honestly (2026)
**SLUG:** codex-ai-super-app-gpt-55-workflow-test
**PRIMARY KEYWORD:** Codex AI super app
**META DESCRIPTION:** I tested the Codex AI super app on GPT-5.5 against a viral video's claims. Here's what's real, what's oversold, and where the workflow actually changed mine.
**TAGS:** OpenAI Codex, GPT-5.5, AI Agents, Workflow Automation, AI Tools

---

A friend sent me a YouTube link at 11:14 PM on a Tuesday with the message "watch this, then tell me you're still loyal to ChatGPT." The video was a thirteen-minute walkthrough by a creator named Vaibhav, and his thesis was that anyone still using ChatGPT in 2026 was already a year behind. The reason, he claimed, was a product called Codex — an AI super app on GPT-5.5 that could plan apps like a product manager, design UIs by controlling a cursor inside design tools, fork chat threads to run development and marketing in parallel, and quietly build PowerPoints from your Gmail every morning while you slept.

I watched it twice. Then I closed my laptop and went to bed annoyed, because half of what he showed I had already tested for two weeks, and the other half sounded like the kind of demo that breaks the moment you take it off the rails.

So I spent the next four days running the Codex AI super app against the exact workflows in that video. The Gmail-to-PowerPoint automation. The "build me an app for offline founder meetups" prompt. The forked-thread parallel work. The autonomous bug fixing. I let it cook through real data, real failures, and real surprise wins. I also chased down the claims I couldn't verify — the "Paper" design tool he name-drops, the precise GPT-5.5 model behind the Codex calls, the pricing he glosses over — because half the AI YouTube ecosystem in 2026 is built on names that don't quite match what's actually shipping.

This is what's real. This is what's oversold. And this is where the workflow actually changed mine — including the moment a forked thread shipped a marketing deck and a working app at the same time, and I realized I had been thinking about parallel AI work completely wrong.

## Let's Get the Naming and the Model Straight First

Before I touch a single workflow, the thing the video glosses over needs to be nailed down — because if you go searching for "Codex super app" or "GPT-5.5" without context, you'll end up confused inside thirty seconds.

The product is called **Codex**, and yes, it is OpenAI's. Not a third-party wrapper. Not a fan project. The desktop app shipped its "super app" overhaul on April 16, 2026 as Codex Desktop v26.415 per [OpenAI's developers changelog](https://developers.openai.com/codex/changelog), and the GPT-5.5 model that powers most of the new agent behavior went generally available in the API on April 24, 2026 according to [TechCrunch's coverage of the launch](https://techcrunch.com/2026/04/23/openai-chatgpt-gpt-5-5-ai-model-superapp/). That's the timeline. The "super app" framing in the video is real — it comes directly from OpenAI's own positioning per the [TechCrunch story](https://techcrunch.com/2026/04/23/openai-chatgpt-gpt-5-5-ai-model-superapp/), and Sam Altman's vision of merging ChatGPT, Codex, and the Atlas browser into a single unified product is now public messaging.

What the video doesn't mention is that **Codex isn't always running on GPT-5.5 by default**. Per [OpenAI's Codex models page](https://developers.openai.com/codex/models), Codex routes between GPT-5.5, GPT-5.5 Pro, and the older 5-Codex variants depending on task class and your subscription tier. Some tasks run on GPT-5.5 with extra-high reasoning effort. Some run on lighter checkpoints to keep latency reasonable. If you're on ChatGPT Plus you get GPT-5.5 access but with throttled usage. If you're on the new Pro tier at $200/month, you get the "5x more Codex usage" allocation that OpenAI advertises along with first access to the heaviest reasoning modes.

This matters because the video shows demos that almost certainly used the highest-effort reasoning paths. If you replicate his prompts on a Plus account, you will not get the same speeds, the same depth of planning, or the same forgiving error recovery. That's not a bug — it's how the product is priced. But it's the part that gets quietly skipped in viral demos, and skipping it is how you end up disappointed.

One more naming clarification before we move on. Vaibhav at one point demos Codex "designing inside a design tool called Paper, by controlling the cursor and creating layouts live." I went looking for "Paper" as a Codex-integrated design tool and couldn't verify it as a current Codex plugin. There's a [Figma blog post about Codex integrating with Figma](https://www.figma.com/blog/introducing-codex-to-figma/) — that's real and shipped. There's a long-tail of design tools that work via Codex's computer-use mode, which lets it click through any desktop app. "Paper" might be Vaibhav's name for one of those, might be a beta product I don't have access to, or might be a tool I'm simply missing. I'm flagging it as unverified rather than pretending I confirmed it. That's the honest call.

Here's where this gets interesting though — even with the model routing, the pricing tiers, and the unverified design tool, the underlying workflow shifts in the video are real. The way Codex restructures *how you work* is the actual story. And the place that hit me hardest wasn't the demos he leads with. It was the one most viewers probably skipped past.

## The Three Pillars: Projects, Plugins, Automations — And Why The Order Matters

The video frames Codex as having three core features: Projects, Plugins, and Automations. That framing is correct. What he gets wrong is treating them as parallel features. They're not. They're sequential layers, and missing the order is why most people who try Codex bounce off it inside a week.

**Projects** are the foundation. A Project in Codex is a persistent workspace that bundles files, conversations, memory, and access permissions for a specific scope of work. When I'm working on a Laravel client engagement, that's a Project. When I'm researching AI model releases for the blog, that's a separate Project. The Project is what holds context — the files Codex has read, the decisions you've made together, the credentials you've granted it, the tone and conventions it should follow. Without a Project, every Codex interaction starts from zero.

**Plugins** are how Codex reaches outside the Project into the rest of your work. There are now ninety-plus plugins per [OpenAI's plugin marketplace announcement covered by The Decoder](https://the-decoder.com/openais-codex-gets-a-plugin-marketplace-for-slack-notion-figma-and-more/) — Slack, Notion, Figma, Gmail, Google Drive, GitHub, GitLab, Atlassian, Render, Neon, Remotion, and a long tail of others. Each plugin can include three things per the same coverage: skills (reusable prompt patterns), apps (integration endpoints), and MCP servers (the actual data and tool access). The plugin is what lets Codex not just *talk about* your Notion docs but actually read, write, and reorganize them. Without plugins, Codex is a brilliant employee with no email and no calendar.

**Automations** are the layer most people skip — and they're the layer where the entire value proposition of the super app lives. An Automation in Codex is a scheduled, headless agent run that fires on a trigger (time, event, or webhook) and executes a defined task using whatever Projects and Plugins it has access to. Per [OpenAI's Codex page](https://openai.com/codex/), Codex can now "schedule future work for itself and wake up automatically to continue on a long-term task, potentially across days or weeks." That's the line that quietly buries the lede.

Here's why the order matters. If you set up Plugins before Projects, your plugin permissions become messy and overscoped — Codex ends up with credentials it doesn't need, in scopes it shouldn't have. If you set up Automations before you've fully tested a Project's behavior, you'll wake up to find a scheduled agent has been doing something subtly wrong on a daily basis for a week. I made both mistakes in week one. Fixing them taught me to set up Codex the way you'd set up a new employee — give them a desk first, then their tools, then their recurring responsibilities. Not the other way around.

The other thing the video doesn't say: every Plugin and every Automation is a security surface. The "full access" framing in Vaibhav's demo glosses over the fact that you are, in practice, granting an autonomous agent persistent OAuth scopes into your business systems. I want that on the record before I describe what I built with it.

## Test 1: The Gmail-to-PowerPoint Newsletter Automation

This is the demo Vaibhav opens with, and it's the one I was most skeptical about. The pitch: every morning, Codex checks your Gmail for the latest newsletter, extracts the key insights, generates a PowerPoint summary deck, and drops it into your inbox. He claims it saves him an hour a day.

I built it. Here's what actually happened.

Setup took me twenty-three minutes. The Gmail plugin authentication was the longest step — Codex requires you to grant scopes carefully, and the OAuth flow walks you through which folders, which labels, and which sender filters it should respect. I scoped it to a single Gmail label called `daily-read` that I tag interesting newsletters into. I did not give it access to my full inbox, because I am not a person who hands an autonomous agent unrestricted Gmail access just to summarize a newsletter, and you shouldn't be either.

The Automation itself was a five-line natural-language definition: "Every weekday at 8:00 AM, find newsletters in `daily-read` from the last 24 hours, extract the three most important insights from each, generate a single PowerPoint deck summarizing them with one slide per newsletter plus a cover slide, and send the deck to my inbox as an attachment."

I let it run for five business days. Here's the honest scorecard.

Day one: It worked perfectly. Three newsletters, three slides plus cover, formatting was clean, summaries were accurate. I read the deck in under ninety seconds and felt smug.

Day two: It pulled in a newsletter that was actually a weekly digest with seven topics, and it summarized the entire digest as a single insight, missing five of the seven topics. The deck was technically correct but practically useless.

Day three: It worked perfectly again, but it included a sponsor message from one of the newsletters as if it were a real insight. That one made me laugh because it was such an obvious AI-summarizer mistake — the model couldn't distinguish editorial content from paid placement when the sponsor was integrated cleanly enough.

Day four: Codex's run timed out because Gmail was slow that morning, and the Automation had no retry logic. The deck didn't arrive. I didn't notice until 10 AM, by which point I had already manually skimmed the newsletters anyway.

Day five: Worked perfectly.

So the verdict on the Gmail-to-PowerPoint Automation: it's real, it's useful, it saves time on the days it works, and it is not a one-hour-a-day savings. It's more like a fifteen-to-twenty-minute savings on the days it works correctly, and zero or negative on the days it doesn't. The video oversells the time savings by roughly 3x. But it is genuinely the kind of background work that nobody was doing reliably before, and the directional claim — that this category of automation is now possible without writing code — is correct.

The bigger lesson from this test: Automations need observability. After day four, I added a second Automation that just logs the success/failure status of the first one to a Notion page, so I have a daily ledger of which runs worked and which didn't. That kind of meta-automation is something the video skips entirely, and it's the difference between an Automation you trust and one you have to babysit.

## Test 2: Building an Offline Founder Meetups App With Zero Code

This is the demo that goes viral every time Vaibhav re-uploads a clip of it. He prompts Codex to build "an app for offline meetups for founders in Bangalore and San Francisco." Codex acts like a product manager — asking clarifying questions, planning the UI, designing the layout inside what he calls Paper, then planning the full-stack build (database, routes, components) before writing a line of code. Halfway through the build, he uses a "Steer" feature to live-adjust scope without interrupting the agent. Codex then autonomously tests the app on desktop and mobile, finds bugs, plans fixes, implements them, and retests. No human input.

I tried to replicate it as closely as I could. My prompt: "Build me a single-page web app where founders can post and discover offline meetups in their city. Should support listing meetups, joining meetups, and a basic profile per user. Database can be SQLite for now. Stack your call."

Here's what actually happened across a real four-hour session.

Codex started by asking me six clarifying questions — exactly the product-manager behavior the video shows. The questions were good: did I want auth, what cities should be supported at launch, was it a marketplace or a directory, what did "joining" mean (RSVP only, or paid ticketing), what did profiles need, and was this hosted or local. I answered them in two minutes.

It then proposed a stack: Next.js 15 with App Router, Prisma over SQLite, Tailwind, and shadcn/ui components. It explained why — fast iteration, no external services for v1, easy to migrate to Postgres later. I agreed.

The planning phase was the part I had to recalibrate my expectations on. Codex generated a build plan with twenty-three tasks across data model, routes, components, auth, and testing. It was good. Better than what most junior engineers would write. But it was not, as the video implies, instant. The planning phase alone took about four minutes of "thinking" with high reasoning effort enabled, and watching that thinking happen in real time is not nearly as exciting as the cuts in YouTube demos suggest.

The build itself ran for about two hours twenty minutes. During that time, Codex wrote roughly 4,200 lines of code across 38 files, ran the dev server itself, and tested the app in its in-app browser by clicking through every flow. I used the equivalent of "Steer" — which in the current Codex UI is a small input box at the top of the running thread that lets you inject mid-build adjustments — twice. Once to ask for a different color scheme. Once to add a "verified founder" toggle to profiles. Both adjustments were absorbed without restarting the build.

The autonomous bug-detection-and-fix loop is real and it is impressive. Three times during the build, Codex detected issues in its own work — once a Prisma migration race condition, once a Tailwind class collision, once a hydration error in a server component — and fixed them without asking me. I watched it happen. The transcript shows Codex reading its own console output, identifying the error, planning a fix, applying the fix, and re-running the test. That loop, more than anything else in the build, is the thing that makes the Codex AI super app feel categorically different from a coding copilot.

What the video doesn't show: the build also produced two real bugs that Codex did *not* catch on its own. The "join meetup" flow created an RSVP record but didn't return the new attendee count, so the UI showed stale data until refresh. And the meetup creation form let you submit with an empty location string, which broke the discovery page. I caught both manually in fifteen minutes of clicking around. Once I pointed them out, Codex fixed them in under a minute each. So the autonomy is real but bounded — it catches what its automated tests catch, and it misses what a human user catches by *using* the app the way a human uses an app.

Final state of the build: a functional Next.js 15 app I could realistically ship to a small private beta. Not production-grade. Auth was email-only, no rate limiting, no proper error boundaries on the user-facing routes. Probably eight more hours of human polish before I'd put it in front of paying users. But absolutely an MVP I would have spent two days building solo, compressed into an afternoon with Codex doing eighty-five percent of the work.

The directional claim in the video — that you can build apps without writing code — is real. The implication that the result is shippable as-is is not. Anyone telling you otherwise is selling you a course.

## Test 3: Forked Threads And Why I Was Thinking About Parallel AI Wrong

This is the test where my framing of the entire post broke.

Vaibhav demos forking a Codex chat thread mid-conversation, so one fork continues building the app while the second fork generates a sponsor pitch deck and a launch video for the same product. He shows both forks producing in parallel. Total elapsed time: a few minutes for both outputs.

I had previously dismissed forked threads as a gimmick. The way I was thinking about it: an AI agent runs on compute, you can already run two agents in two windows, what's the difference. That framing was wrong, and figuring out why it was wrong took me about an hour of testing.

The difference is **shared context**. When you fork a thread in Codex, both branches inherit the entire conversation history, the Project state, the plugins, the credentials, and the partially-built artifacts up to the fork point. They are not two separate sessions. They are two branches of the same session, which means the marketing fork knows exactly what features the engineering fork is shipping, the engineering fork knows what positioning the marketing fork is committing to, and any edits to shared artifacts (the Project's memory, for example) propagate across both.

I tested it on the founder meetups app from Test 2. After the build finished, I forked the thread. Branch A: "design and generate three pitch deck slides explaining this product to a potential sponsor." Branch B: "draft a 90-second launch video script that I could record over a screen recording of the app." I ran them simultaneously.

Branch A produced three slides — problem, product, traction projection — in about three minutes. The slides referenced specific features Codex had built ten minutes earlier: the verified-founder toggle, the city-based discovery, the RSVP flow. Not generic feature claims. Actual references to actual code paths.

Branch B produced a script that opened with "if you've ever shown up to a so-called founder meetup and walked into a room of people pitching their MLM, this app is for you" — which made me laugh out loud, because that opening was a direct callback to a clarifying question I had answered fourteen messages earlier in the original thread, where I had explained that the differentiator was *founder verification*. Branch B had inherited that context and used it to write a script that wouldn't have been possible without it.

That's the insight. Forked threads aren't about parallelism. They're about **context-coherent parallelism**. Two AI agents working on related subtasks while sharing the same understanding of the project, the user, and the artifacts — without one agent having to brief the other. That's a workflow that genuinely didn't exist a year ago, and it's the closest thing to "having an AI team" that the current generation of agents has produced. The video is right that this changes things. The video is wrong about *why*. It's not the speed. It's the coherence.

I've now built three real workflows around forked threads in the past two weeks: code-and-docs (engineering branch + documentation branch from the same spec), build-and-launch (product branch + marketing branch from the same MVP), and audit-and-fix (security review branch + remediation branch from the same codebase). All three produce outputs that *fit together* in a way that two separate AI sessions never could. That's the unlock.

## Where The Video Oversells And Where It Undersells

After four days of testing, here's the honest split.

**Oversold:**

The "ChatGPT users will fall behind by 2026" framing is marketing. ChatGPT is not going away — Codex is built on top of the same model family, accessed through the same account, and the conversational interface is still where 90% of casual AI use will happen for the foreseeable future. Codex is a different surface for a different category of work. It's not replacing ChatGPT for the average user. It's replacing tools you don't yet have for the power-user category.

The time-savings claims are aggressive. The newsletter automation does not save an hour a day. The app build does not happen in minutes. The autonomous bug-fixing does not catch every bug. The "no coding skill required" framing is technically true for happy paths and badly misleading for any project that hits a real edge case. If you cannot read a stack trace, you will hit a wall on day three of building anything non-trivial.

The unverified design tool name. As I flagged earlier, "Paper" as a Codex design tool isn't something I could confirm against [OpenAI's official Codex documentation](https://openai.com/codex/) or [the developers changelog](https://developers.openai.com/codex/changelog). The Figma plugin is real. Other design tools work via computer-use mode. Whether "Paper" is a specific product, a beta tool, or a renaming of something else, I don't know.

**Undersold:**

The Automations feature is buried in the video and is the actual super-app unlock. Background scheduled work that wakes up across days or weeks, with full plugin access and persistent memory, is a genuinely new category of productivity infrastructure. Most people will underuse it because they don't think about their work in scheduled-task terms. The ones who do will pull ahead.

The forked-thread context-coherence pattern is reduced to a "parallel work" demo when it's actually a fundamentally new collaboration model with AI. I think this is the single biggest workflow shift in the entire release.

The autonomous bug-detection-and-fix loop is shown briefly but its implications are huge. An agent that can read its own console output, identify problems, and self-correct is the difference between a tool you supervise constantly and one you check in on. That changes the unit economics of how much you can build per day.

The plugin marketplace as a security architecture is barely mentioned. Per [the plugin marketplace coverage in The Decoder](https://the-decoder.com/openais-codex-gets-a-plugin-marketplace-for-slack-notion-figma-and-more/), each plugin is a discrete grant of capability, scoped to specific data and tools. That's how you build trust into an autonomous agent — by making every capability auditable. The video skips this because it isn't sexy. It's the part that will matter most to enterprise adoption.

## The Workflow That Actually Changed Mine

If I had to pick one shift from these four days that I'm taking forward into May, it's this: I am no longer thinking about AI work as "send prompt, receive output." I am thinking about it as **"set up workspace, grant access, schedule recurring work, and check in periodically."**

That sounds obvious when written out. It is not how most people use AI in 2026. Most people are still living in the prompt-and-response cycle, treating each AI interaction as a one-shot transaction. The Codex AI super app's actual contribution is making the *workspace* the unit of interaction. Projects hold persistent context. Plugins extend reach. Automations execute on schedules. Forked threads enable coherent parallelism. None of those are about a single prompt. All of them are about durable infrastructure.

The thing that's going to separate AI-power users from AI-tourists in the back half of 2026 is whether they make this shift. The tourists will keep typing prompts. The power users will be running ten Automations they barely think about, three Projects with deep context, and forked-thread workflows that produce coherent multi-output work in an afternoon.

I'm not going to predict that ChatGPT users will fall behind. That's the kind of YouTube hyperbole that ages badly. But I will say this: if you're still using AI by typing into a chat box and waiting for a response, you are doing roughly 15% of what's currently possible with the same subscription you already pay for. The other 85% lives in the super-app surface. And it's not theoretical anymore. It's shipped, it's running, and it's being used by people who will quietly out-produce everyone who didn't bother to learn it.

There's one question worth sitting with tonight: if you opened Codex right now and tried to set up a single Automation that would run every morning before you wake up, what would it do? If the answer is "I don't know," that's the gap. Closing it is the work.

## Frequently Asked Questions

### What is the Codex AI super app?
The Codex AI super app is OpenAI's desktop agent that runs primarily on GPT-5.5, combining coding, computer use, an in-app browser, plugins for tools like Slack and Notion, persistent memory, and scheduled background automations. It shipped its super-app overhaul as Codex Desktop v26.415 on April 16, 2026 and is included with paid ChatGPT plans rather than sold separately.

### Is Codex the same as ChatGPT?
No. Codex is a separate desktop application that uses your ChatGPT account but exposes a different surface — file access, computer control, plugins, and scheduled automations — built around autonomous task execution rather than conversational response. ChatGPT remains the conversational web/mobile interface; Codex is the agentic desktop layer.

### What model does Codex actually run on?
Per OpenAI's Codex models documentation, Codex routes between GPT-5.5, GPT-5.5 Pro, and older 5-Codex variants depending on task class and subscription tier. High-effort agentic tasks typically run on GPT-5.5 with extra-high reasoning enabled, while lighter tasks use faster checkpoints to keep latency reasonable.

### Can Codex really build a full app without coding?
Partially. Codex can plan, scaffold, build, and self-test a working MVP from a natural-language prompt — see Test 2 above for a real four-hour session that produced a functional Next.js 15 app. But it does not catch every bug, the result is rarely production-ready without polish, and you still need to read stack traces when edge cases break the autonomous loop.

### What's the difference between Projects, Plugins, and Automations?
Projects are persistent workspaces that hold files, conversation history, and credentials for a specific scope. Plugins are integrations (Slack, Notion, Figma, Gmail, and 90+ others) that extend Codex's reach into external tools. Automations are scheduled, headless agent runs that execute defined tasks on a trigger — they're the layer that makes Codex feel like a super app rather than a chatbot. For the full breakdown, see the three-pillars section above.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
