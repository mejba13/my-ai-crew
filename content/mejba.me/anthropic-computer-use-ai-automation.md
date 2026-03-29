**BRAND:** mejba.me
**TITLE:** I Let Claude Control My Mac for a Week. Here's What Happened.
**SLUG:** anthropic-computer-use-ai-automation
**PRIMARY KEYWORD:** Anthropic Computer Use
**SECONDARY KEYWORDS:** Claude AI automation, AI desktop agent, Claude Mac control
**META DESCRIPTION:** I gave Anthropic's Computer Use full access to my Mac for a week. Job applications, code reviews, Zoom calls — here's what it nailed and where it broke down.
**TAGS:** AI Automation, Claude AI, Anthropic Computer Use, Agentic Workflows, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand exactly what Anthropic's Computer Use can and cannot do, how it compares to OpenClaw, and have a clear framework for deciding when to trust an AI agent with desktop-level control.

---

The email went out at 3:47 PM on a Thursday. A cover letter, personalized to a specific job posting, referencing the company's recent product launch, matching their tone, formatted correctly, and sent from my actual Gmail account. I didn't write a single word of it. I didn't even open Gmail.

Claude did. On my Mac. While I was making a sandwich in the kitchen.

I'd told it one thing: "Find the senior developer role at [company name] on their careers page, write a cover letter based on my resume, and email it to their hiring contact." Then I walked away. When I came back, the sent folder had a new message, and the cover letter was better than what I would have written if I'd spent forty-five minutes on it — because Claude had actually read the job listing, pulled specific requirements, and matched them against my experience. Not in a generic "I am a motivated professional" way. In a "your listing mentions Kubernetes orchestration at scale, and here's a specific project where I did exactly that" way.

That was day one. By day seven, Claude had joined a Zoom meeting on my behalf, written and scheduled a pull request at 2 AM while I slept, and — in one moment that genuinely startled me — accessed my bank account to check a transaction I'd asked about. It completed every task correctly. And each one raised a question I'm still sitting with: how much control should you hand an AI that can operate your entire computer?

This is Anthropic's Computer Use, and it shipped on March 23, 2026. I've been testing it obsessively since. What I found was both more capable and more unsettling than I expected — and the comparison with OpenClaw, which I've been [using for months](https://www.mejba.me/blog/openclaw-ai-use-cases-productivity), reveals a philosophical split in how we're building the future of AI automation.

Here's the honest breakdown, including the parts Anthropic probably wishes I wouldn't talk about.

## What Anthropic Actually Built Here

Computer Use isn't a chatbot upgrade. It's not a better autocomplete. It's a system that gives Claude the ability to see your screen, move your mouse cursor, click buttons, type into text fields, and navigate between applications — exactly like a human sitting at your desk would.

The technical architecture matters, so let me be precise. Claude receives a stream of screenshots from your Mac's display. It processes what's on screen using its vision capabilities, decides what action to take next, and sends back mouse/keyboard commands that execute on your machine. The loop repeats: screenshot, analyze, act, screenshot, analyze, act. It's not accessing application APIs or reading your filesystem through code. It's literally looking at your screen and clicking things.

This distinction is critical. When Claude fills out a form on a website, it's doing it the same way you would — finding the text field visually, clicking into it, typing characters. When it opens an application, it's clicking the icon in your dock or using Spotlight search. The approach means it can work with any application that has a visual interface, including apps that don't have APIs or automation hooks.

Available now for Claude Pro subscribers at $17/month and Max subscribers at $100 or $200/month, Computer Use runs as a research preview on macOS only. Windows support is planned but doesn't have a public timeline. You enable it inside Claude Cowork or Claude Code, and the first thing it does is ask which applications you want to grant it access to — a permission model I'll dig into shortly, because it's one of the most important design decisions Anthropic made.

But before we get into the security model, you need to see what this thing can actually do when you let it loose. Because the demos on Anthropic's marketing page don't come close to capturing what happens when you give it real tasks in a messy, real-world environment.

## Seven Days, Seven Experiments — What I Actually Tested

I designed a week of increasingly ambitious tests, starting with simple automation and ending with tasks that genuinely made me nervous. Here's what happened.

### Day 1: Job Application Automation

The cover letter experiment I described in the opening. But the interesting part wasn't the output — it was the *process*. I watched Claude's screen interactions in real time (you can observe everything it does). It opened Safari, navigated to the company's careers page, scrolled through listings until it found the right role, and read the full job description. Then it switched to a text editor, drafted the cover letter, revised it twice (I watched it delete and rewrite the opening paragraph), opened Gmail, composed a new message, pasted the letter, added the correct recipient from the job listing, and hit send.

The whole sequence took about four minutes. A human doing the same thing — reading the listing carefully, writing a tailored letter, sending it — would spend thirty to forty-five minutes. And Claude's letter was genuinely good. Not template-good. Context-aware-good.

### Day 2: Calendar and Meeting Management

I told Claude: "Check my calendar for tomorrow, find any conflicts, and if there are overlapping meetings, email the less important one's organizer to reschedule." It opened Calendar, identified a conflict between a client call and an internal standup, determined (correctly) that the standup was lower priority, opened Gmail, composed a polite reschedule request, and sent it.

The judgment call about which meeting was "less important" is what made me pause. Claude inferred priority based on context — client-facing vs. internal, the number of attendees, and the meeting description. It got it right. But the fact that it's making prioritization decisions about my schedule without explicit rules feels like a threshold we've crossed that most people haven't fully processed.

### Day 3: Code Generation and PR Scheduling

This is where my developer brain got excited. I asked Claude to write a utility function for parsing nested JSON configurations, create a new branch in my Git repository, commit the code, and schedule the pull request to open at 2 AM — when my teammate in a different timezone would be starting their day.

Claude opened VS Code, created the file, wrote the function (with tests — I didn't even ask for tests), opened the terminal, ran the git commands, pushed to the remote repository, and used GitHub's scheduled PR feature to set the open time. At 2:07 AM, the PR appeared in my teammate's notification queue. The code was clean. The tests passed.

I've been [building AI-assisted coding workflows](https://www.mejba.me/blog/claude-code-agent-swarm-architecture) for a while now, and this felt like a meaningful step forward. Not because the code was better than what Claude generates through its API — it wasn't — but because the *end-to-end delivery* happened without me touching anything after the initial prompt.

### Day 4: Zoom Meeting Autopilot

This one made me uncomfortable before, during, and after. I told Claude to join my 11 AM Zoom meeting, take notes, and if anyone asked me a question, respond with "I'm reviewing that and will follow up by end of day."

Claude opened Zoom, clicked the meeting link, joined with my camera off and microphone muted. It watched the screen-shared presentation slides and generated notes in real time. When someone called my name and asked about a deployment timeline, Claude unmuted, and — using the Mac's text-to-speech — delivered the canned response I'd given it.

Did it work? Technically, yes. Did it feel deeply weird? Absolutely. The other participants didn't know they were talking to an AI. That's a line I'm not sure I want to cross again, even though the immediate utility was obvious. I was in a dentist's chair during that meeting and would have missed it entirely otherwise.

### Day 5: Research and Report Compilation

I asked Claude to research the current state of AI regulation in the EU, compile findings into a structured report with citations, and save it as a PDF on my desktop.

This was the task where Computer Use felt most natural. Claude opened Safari, searched for recent EU AI Act updates, visited official government pages, navigated to specific articles and amendments, switched between tabs to cross-reference information, opened Pages (Apple's word processor), and built a formatted report with headers, bullet points, and inline citations. The PDF appeared on my desktop twenty-two minutes later.

The report was solid — not publication-ready, but a strong first draft that would have taken me two to three hours of research and writing. The citations were accurate. The structure was logical. It missed some nuance around the March 2026 enforcement timeline updates, which I caught and corrected in about ten minutes.

### Day 6: Financial Operations

Here's where the trust question gets real. I asked Claude to log into my bank account and check whether a specific freelance payment had cleared.

It opened Safari, navigated to my bank's login page, entered my credentials (which I'd provided), handled the two-factor authentication prompt by telling me to approve it on my phone, and once inside, navigated to recent transactions and found the payment. It reported back: "The payment of $4,200 from [client name] cleared on March 25."

Accurate. Helpful. And I was sweating the entire time. Not because Claude did anything wrong — it didn't. But watching an AI navigate my bank's interface, with my actual credentials, accessing real financial data, triggered every security instinct I have. I'll explain why this matters in the security section. This test was important precisely because it was uncomfortable.

### Day 7: The Multi-Step Stress Test

For the final day, I gave Claude a complex, multi-application task: "Find the top three trending AI papers on arXiv from this week, summarize each one, create a presentation comparing their approaches, email the presentation to my research group, and add a reminder to my calendar to discuss it next Tuesday at 3 PM."

This required Claude to coordinate across Safari, a text editor, Keynote, Gmail, and Calendar. It took thirty-one minutes. The presentation had six slides — an overview, one slide per paper, a comparison table, and a discussion questions slide. The email was formatted correctly. The calendar event appeared with the right time and a link to the attached presentation.

One paper summary had an error — Claude misattributed a finding from the methodology section to the results. Everything else was accurate. For a thirty-one-minute autonomous run touching five applications, that's an impressive error rate.

But impressive isn't the same as trustworthy. And that distinction is the entire conversation we need to have about this technology.

## The Permission Model — Why Anthropic's Approach Is Smarter Than It Looks

When you first enable Computer Use, Claude asks you to approve each application individually. Want it to use Safari? Approve Safari. Gmail? Approve Gmail. Terminal? Approve Terminal. Each app gets its own toggle, and Claude cannot interact with any application you haven't explicitly granted access to.

This seems like a small design choice. It's not. It's the most important architectural decision in the entire product.

OpenClaw — the open-source alternative I've [tested extensively](https://www.mejba.me/blog/openclaw-ai-use-cases-productivity) — takes the opposite approach. It requests broad system access. Once you grant it, OpenClaw can touch anything on your machine. Files, applications, network connections, system settings. The flexibility is powerful, and for structured automation workflows, [OpenClaw's action primitives are genuinely better for repeatable tasks](https://www.mejba.me/blog/openclaw-agents-scale-business). But the security surface is enormous.

Anthropic's per-app permission model means that even if something goes wrong — a prompt injection attack, a misunderstood instruction, a hallucinated action — the blast radius is contained. If Claude only has access to Safari and a text editor, it physically cannot touch your financial applications, your terminal, or your system settings. The damage ceiling is capped by the permissions you've granted.

This matters more than most people realize, because prompt injection is a real and demonstrated attack vector for computer-use agents. Security researchers have shown that [malicious instructions embedded in webpage content can hijack Claude's actions](https://prompt.security/blog/claude-computer-use-a-ticking-time-bomb) — telling it to download files, click links, or navigate to hostile pages. Anthropic says Sonnet 4.6 shows "improved resistance" to prompt injection compared to earlier models, and their [published research on prompt injection defenses](https://www.anthropic.com/research/prompt-injection-defenses) outlines the mitigation strategies they're using. But "improved resistance" isn't "immune." A 1% attack success rate across millions of users still means thousands of potential compromises.

My recommendation: start with the minimum permissions you need. If you're using Computer Use for research, grant Safari access only. If you're doing code-related tasks, add VS Code and Terminal. Expand the permission set only when a specific task requires it, and revoke access when you're done. Treat it like you'd treat SSH keys — principle of least privilege, always.

## Computer Use vs. OpenClaw — The Real Comparison

I've been running both systems for weeks now, and the comparison isn't "which one is better." It's "which one is right for what you're trying to do."

**Setup and accessibility:** Claude's Computer Use wins decisively. Enable it in Claude Cowork, approve your apps, and you're running. Total time: under five minutes. OpenClaw requires Docker containers, action schema definitions, environment configuration, and CLI familiarity. Budget an afternoon for your first setup, longer if you don't have existing Docker infrastructure.

**Model flexibility:** OpenClaw is model-agnostic — it works with Claude, GPT-4o, Gemini, local models via Ollama, and anything with a compatible API. You can route simple tasks to cheaper models and reserve expensive ones for complex reasoning. Computer Use is locked to Claude. If you want model diversity or need to avoid vendor lock-in, OpenClaw is the only option.

**Automation reliability:** For workflows you'll run hundreds of times, OpenClaw is more reliable. Its action primitives are testable and debuggable. You can inspect exactly what happened at each step, replay failures, and build error-handling logic. Computer Use's vision-based approach is more flexible but less predictable — screenshot interpretation can fail if a UI element loads slowly or renders differently than expected.

**Security model:** Anthropic's per-app permissions versus OpenClaw's broad system access. For individual users running sensitive tasks, Claude's model is safer by design. For teams running structured automation in sandboxed environments, OpenClaw's approach is acceptable if you've done the security work.

**Remote operation:** Computer Use pairs with Anthropic's Dispatch feature — you can [assign tasks from your iPhone](https://www.mejba.me/blog/claude-code-remote-control-phone) and return to finished work on your desktop. OpenClaw doesn't have a native mobile trigger mechanism, though you can rig one through Telegram or WhatsApp integrations.

**Cost:** Computer Use requires a Claude Pro ($17/month) or Max ($100-$200/month) subscription. OpenClaw is free and open-source, though you'll pay for the underlying model API calls — which can add up quickly if you're running frequent, complex tasks.

The bottom line: if you want a general-purpose desktop agent that handles diverse, one-off tasks with minimal setup, Computer Use is the better choice right now. If you need high-reliability, repeatable automation across specific workflows — and you have the technical skills to configure it — OpenClaw gives you more control.

If you'd rather have someone build a custom automation setup tailored to your workflow, I take on AI integration projects. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Job Displacement Conversation Nobody Wants to Have Honestly

Anthropic's CEO Dario Amodei [told Axios last year](https://fortune.com/2025/05/28/anthropic-ceo-warning-ai-job-loss/) that AI could eliminate half of all entry-level white-collar jobs within five years — potentially pushing unemployment to 10-20%. That prediction made headlines, drew criticism, and got filed away in most people's "scary AI predictions" folder.

After a week with Computer Use, I think Amodei's timeline might be aggressive, but his direction is right. And the mechanism is more specific than most people imagine.

Computer Use doesn't replace a software engineer. It doesn't replace a lawyer or a financial analyst. What it replaces is the *routine execution layer* of those jobs — the hours spent on tasks that require computer literacy but not deep expertise. Writing cover letters. Scheduling meetings. Compiling research into reports. Filling out forms. Sending standardized emails. Updating spreadsheets.

According to [Anthropic's own research published in March 2026](https://fortune.com/2026/03/06/ai-job-losses-report-anthropic-research-great-recession-for-white-collar-workers/), while AI hasn't meaningfully eliminated jobs yet, there's "suggestive evidence that hiring of younger workers" in exposed occupations has already slowed — particularly for ages 22-25. The jobs aren't vanishing overnight. The pipeline is narrowing.

Here's the honest tension I'm sitting with: as a developer and AI builder, Computer Use makes my work more efficient. I save real time. I get things done while away from my desk. My output per hour has measurably increased.

But I also know that "entry-level developer who handles routine tasks" is exactly the kind of role that Computer Use makes redundant. The junior team member who writes boilerplate code, manages the CI pipeline, and sends status update emails? That entire job description is now a prompt.

I don't have a clean answer for this. What I do have is an observation: the people who will thrive alongside these tools are the ones who understand what the AI is doing well enough to direct it, catch its mistakes, and handle the tasks it can't. The meta-skill isn't coding or writing or researching — it's knowing when to trust the AI and when to intervene. That judgment layer is, for now, irreplaceable.

## What Broke — The Honest Failure Report

Not everything worked. And the failures are as instructive as the successes.

**UI timing issues.** Computer Use takes screenshots at intervals, and if a page loads slowly or a modal appears between screenshots, Claude can miss it entirely. I watched it click a button that had already been replaced by a loading spinner, which triggered an unintended action. This happened three times during the week, always on JavaScript-heavy web applications with dynamic content.

**Context window pressure.** Extended multi-step tasks push against Claude's context limits. By the time Claude reached step 8 of a 12-step task, it had consumed so many tokens processing screenshots that its responses became noticeably less precise. Breaking long tasks into smaller chunks works, but it undermines the "just tell it once and walk away" promise.

**Two-factor authentication friction.** Any task that requires 2FA interrupts the automation flow. Claude can't tap "Approve" on your phone for you. Every 2FA prompt becomes a manual intervention point, which means highly secured services (banking, email, cloud platforms) don't automate as cleanly as less secured ones. The irony isn't lost on me — the more security-conscious the service, the harder it is for AI to automate.

**Misidentifying similar UI elements.** Claude occasionally clicked the wrong button when two buttons looked visually similar. In one case, it clicked "Delete" instead of "Download" because both buttons were in the same location on consecutive dialog boxes. No data was lost — the action had a confirmation step — but it was a stark reminder that vision-based interaction is fundamentally less reliable than API-based automation.

**Mac-only limitation.** This is a dealbreaker for many developers. If you're running Windows or Linux as your primary development environment, Computer Use simply doesn't exist for you yet. My main development machine runs macOS, so this wasn't a problem for me, but it eliminates a huge portion of the potential user base.

These aren't theoretical edge cases. They happened during normal use, doing normal tasks. Computer Use is impressive, genuinely useful, and clearly not ready to be trusted unsupervised with anything mission-critical. Anthropic labels it a "research preview" for a reason, and that label is honest.

## The Bigger Picture — What This Changes About AI Agents

Step back from the specific features and something larger comes into focus.

For the past three years, AI assistants have been confined to text boxes. You describe what you want, the AI generates text or code, and you copy-paste the output into whatever application actually needs it. The human serves as the bridge between the AI's capabilities and the computer's interface. You're the middleware.

Computer Use, OpenClaw, Google's Autobrowse, and the wave of browser agents and desktop agents arriving in 2026 — they all eliminate the middleware. The AI talks directly to your computer. It sees what you see, clicks what you'd click, and navigates where you'd navigate.

This shift from "AI generates output" to "AI executes actions" is the most significant capability change since large language models started writing coherent text. Generation is useful. Execution is transformative. And we're at the very beginning of figuring out what that transformation looks like.

The tools companies building in this space — [SerpAPI providing structured search data to AI agents](https://serpapi.com/use-cases/ai-search-engine-api), Nvidia accelerating structured data pipelines, Shopify building agentic storefronts — are all betting on the same thesis: AI agents that can act on the world need reliable data infrastructure to act *well*. The plumbing is being built right now, and Computer Use is the visible tip of a much larger iceberg.

What I'm watching most closely is the convergence of computer use, persistent memory, and multi-agent coordination. Right now, Computer Use handles single tasks on one machine. But Anthropic already has [agent teams](https://www.mejba.me/blog/anthropic-agent-teams-ai-coding) running multiple Claude instances in parallel. Connect those agents to Computer Use, give them persistent memory across sessions, and you're looking at a system that can manage an entire workflow — not a task, a *workflow* — across days and applications without human input.

That's not science fiction. Every individual piece exists today. The integration is what's coming.

## Who Should Use This Right Now — And Who Should Wait

**Use Computer Use now if:**
- You're on macOS and already paying for Claude Pro or Max
- You regularly spend time on repetitive, multi-application tasks (research compilation, email drafting, scheduling)
- You want to offload tasks while away from your desk (pairs beautifully with Dispatch)
- You're comfortable supervising AI actions and catching occasional errors
- You're building AI-assisted workflows and want to understand the state of the art

**Wait if:**
- You need Windows or Linux support
- Your critical workflows depend on high-security services with mandatory 2FA
- You need 100% reliability on automated tasks (use OpenClaw with tested action schemas instead)
- You're not comfortable with an AI having screen-level access to your applications
- You work with sensitive client data and aren't sure about the compliance implications

The honest truth? I'm keeping it turned on. The time savings are real — I estimate 4-6 hours per week on the tasks I've automated — and the ability to trigger work from my phone while I'm away from my desk has already changed how I structure my days. But I'm keeping a close eye on every task it runs, and I'm not giving it access to financial applications again without a much more robust safety net.

## The Question Worth Sitting With

A week ago, "AI assistant" meant something that lived inside a chat window, answered questions, and generated text I'd copy somewhere else. Today, sitting at my desk after seven days of watching Claude physically operate my computer — opening apps, clicking buttons, sending emails, joining meetings — that definition feels like it belongs to a different era.

The gap between "AI that talks" and "AI that acts" is the gap between a coworker who gives advice and a coworker who does the work. We just crossed it. Not perfectly. Not safely enough. Not for everyone. But the crossing happened, and there's no un-crossing it.

The question isn't whether AI agents will operate our computers. That's settled — they already do. The question is this: when you hand over your mouse and keyboard to an intelligence that can click any button, open any app, and send any message, what exactly are you trusting — and what happens when that trust is wrong?

I don't have a complete answer yet. But I know this: the people who start building that answer now, while the technology is early and the stakes are still manageable, are going to be far better prepared than the people who wait until it's too late to ask.

## Frequently Asked Questions

### What is Anthropic Computer Use and how does it work?

Anthropic Computer Use is a feature that lets Claude AI control your Mac by taking screenshots, interpreting what's on screen, and executing mouse clicks and keyboard inputs — just like a human would. It's available as a research preview for Claude Pro ($17/month) and Max ($100-$200/month) subscribers on macOS only.

### Is Claude Computer Use safe to use with sensitive applications?

Claude requires per-app permission approval before accessing any application, which limits the blast radius of errors or attacks. However, prompt injection remains a demonstrated vulnerability, and Anthropic acknowledges the feature is early. Avoid granting access to banking or sensitive applications until the security model matures. For a deeper look at AI agent security, see [xCyberSecurity's assessment resources](https://www.xcybersecurity.io).

### How does Computer Use compare to OpenClaw?

Computer Use is easier to set up (minutes vs. hours), Mac-only, locked to Claude, and uses a safer per-app permission model. OpenClaw is open-source, model-agnostic, more reliable for repeatable automation, but requires Docker and CLI knowledge. See the full comparison in the section above.

### Will Anthropic Computer Use replace human jobs?

Anthropic's CEO predicts AI could eliminate 50% of entry-level white-collar jobs within five years. Computer Use specifically targets the routine execution layer — scheduling, email, research compilation, form-filling — rather than roles requiring deep expertise. Early data from Anthropic's March 2026 research shows hiring of 22-25 year-olds has already slowed in AI-exposed occupations.

### Does Computer Use work on Windows or Linux?

No. As of March 2026, Computer Use is macOS-only. Windows support is planned but has no public release date. Linux support hasn't been announced.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
