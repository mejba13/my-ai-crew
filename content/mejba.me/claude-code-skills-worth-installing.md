**BRAND:** mejba.me
**TITLE:** Claude Code Skills Worth Installing Right Now (2026)
**SLUG:** claude-code-skills-worth-installing
**TAGS:** AI Development, Claude Code, Claude Code Skills, Developer Productivity, Curated Guide

---

Seventy-six of them were designed to steal your credentials.

That number comes from a Snyk audit published last month. They scanned 3,984 skills from the skills.sh registry — the cross-agent skill marketplace Vercel launched back in January — and found that 36% had some kind of security flaw. Most were sloppy code, unvalidated inputs, overly broad file access. But 76 were genuinely malicious. Keyloggers disguised as linting helpers. Token exfiltrators wrapped in innocent-looking formatting skills. One posed as a Markdown beautifier while quietly shipping your `.env` contents to an external endpoint every time you invoked it.

I read that report on a Friday night and immediately audited every skill I had installed. Pulled three I couldn't fully verify. Replaced two with alternatives from sources I trust. And then I sat with a question that's been nagging me since: with 60,000+ skills now live on the registry, how does anyone separate signal from noise without accidentally handing their API keys to a stranger?

That's what this post is actually about. Not a listicle of "cool tools." A filtered, tested, opinionated shortlist of 10 Claude Code skills I've installed, used on real projects, and would recommend to another developer without hesitation. I'll share my exact filter criteria, the install commands, and — for each skill — the specific moment where I thought "okay, this actually changed my workflow."

There's a catch with one of these skills that sits in a legal grey area. I'll be honest about it when we get there.

## The Filter That Killed 59,990 Skills

Before I walk through the list, you need to understand how I evaluate skills. The registry is massive. Sorting by install count helps, but popularity isn't safety and popularity isn't quality. I've seen skills with 50,000+ installs that do almost nothing useful.

My filter has four questions. A skill needs to pass all four or it doesn't get installed:

**Does it solve a real pain?** Not a theoretical inconvenience. A thing that actually slows me down or produces worse output on real projects. If I can't point to a specific moment in the last month where this skill would have helped, it's out.

**Does it save real time?** I'm talking measurable minutes, not "it feels faster." If the setup time plus the learning curve exceeds what I'd save in the first two weeks, it's not worth the cognitive overhead.

**Is the source trustworthy?** This is where the Snyk report changed my behavior. I now check three things: who published it (individual vs. established org), whether the repo has visible code I can audit, and whether the skill requests permissions that match its stated purpose. A Markdown formatter that wants network access? Uninstall.

**Can I demo it working?** I have to see it produce a result I couldn't have gotten without it. Not marginally better — noticeably better. If I install a skill and can't show someone a concrete before/after within ten minutes, it fails this test.

Ten skills survived. Here's the first one — and it's the one that makes finding the other nine trivially easy.

## 1. find-skills — The Registry Browser You Install First

**Publisher:** Vercel Labs | **Installs:** 418K+

The skills registry has over 60,000 entries. Browsing it through a web interface is painful. Browsing it from inside Claude Code, where you're actually going to use these skills, is the obvious move — and that's exactly what find-skills does.

```bash
npx skills add https://github.com/vercel-labs/skills --skill find-skills
```

Here's how I actually use it. Last week I started a new project involving PDF invoice parsing for a client's accounting pipeline. Instead of alt-tabbing to a browser, searching the registry, reading descriptions, copying install commands — I asked Claude directly. "Find me skills related to PDF parsing." It searched, returned five options with descriptions and install counts, and I picked one and installed it without leaving my terminal.

The whole interaction took maybe 40 seconds. That's not life-changing on its own. But multiply it across every new project, every time you wonder "has someone already built a skill for this?" — and the compound time savings become significant.

One thing I appreciate: it shows you the source repository before installation. Given the security landscape I described earlier, that matters. You can glance at who built it and make a quick trust decision before anything touches your system.

If you install nothing else from this list, install this one. It's the gateway to everything else. Speaking of everything else — the next skill fundamentally changed how I approach UI work with Claude.

## 2. frontend-design — The Skill That Stopped My UIs Looking AI-Generated

**Publisher:** Anthropic | **Installs:** 124K+

I'll admit something. Before this skill, every UI I built with Claude looked... fine. Functional. Clean, even. But identical to every other AI-generated interface on the internet. The same rounded corners, the same blue-purple gradients, the same suspiciously perfect spacing that somehow still felt lifeless.

The problem wasn't Claude's coding ability. The problem was that Claude would jump straight from "build me a landing page" to writing HTML and CSS without ever stopping to think about visual direction. No mood. No palette rationale. No typography system. Just... default good taste, which is another way of saying no taste at all.

```bash
npx skills add https://github.com/anthropics/skills --skill frontend-design -a claude-code
```

What frontend-design does is deceptively simple: it forces Claude to commit to a real visual direction before writing a single line of code. When I invoke it, Claude asks me 3-5 questions about the aesthetic I'm going for. Not vague questions like "what style do you want?" — pointed ones. What emotion should the page create? Who's the audience and what do they expect visually? Are there brands whose aesthetic resonates with what you're building?

Then it locks in a design system. Color tokens, type scale, spacing rhythm, component patterns. Only after that foundation exists does it start writing code.

I used this on a SaaS landing page for a client project through Ramlit last month. The output looked like a designer had been involved. Not because Claude suddenly became a designer — but because the skill imposed the same constraints a good designer would impose on themselves. Constraints produce coherence. Coherence produces the feeling of quality.

The difference between "AI-generated UI" and "UI that happens to have been built with AI" is almost entirely about whether someone stopped to define the visual system first. This skill automates that pause.

But a good visual direction means nothing if the underlying code has accessibility holes and performance problems. That's where the next skill earns its place.

## 3. web-design-guidelines — 100+ Rules You Don't Have to Remember

**Publisher:** Vercel Labs | **Installs:** 137K+

I shipped a dashboard component two weeks ago. Looked great. Worked perfectly in Chrome on my MacBook. Then I ran it through this skill's audit, and Claude found six issues I'd missed: two accessibility violations (missing ARIA labels on interactive elements, insufficient color contrast on a secondary button), one performance problem (an unoptimized image loading above the fold without lazy-load consideration), and three layout inconsistencies that would have broken on smaller viewports.

```bash
npx skills add vercel-labs/agent-skills --skill web-design-guidelines -a claude-code
```

Vercel packed over 100 rules into this skill — accessibility standards, performance best practices, responsive design patterns, semantic HTML conventions. Claude checks your code against all of them automatically.

Here's what makes this different from running a linter. Linters catch syntax problems. This catches *design* problems. Things a senior frontend developer would notice in code review but a linter would never flag. "This button is technically accessible but the tap target is too small for mobile." "This layout works but the visual hierarchy doesn't guide the eye correctly." That kind of feedback.

I've started running this as a final check before every frontend PR. It adds maybe two minutes to my workflow and consistently catches things I would have shipped otherwise. Two minutes that prevent the "oh, I didn't notice that" conversation in QA.

The three skills I've covered so far handle discovery, design, and code quality. The next one operates at a completely different level — it changes how Claude *thinks* before it builds anything.

## 4. obra/superpowers — The Thinking Phase That Cuts Revision Cycles

**Publisher:** obra (Jesse Vincent) | **Category:** Brainstorming

This one doesn't generate code. It doesn't touch files. It doesn't produce any visible artifact. And it might be the most impactful skill on this list.

```bash
npx skills add https://github.com/obra/superpowers --skill brainstorming -a claude-code
```

What superpowers does is insert a structured thinking phase before Claude starts executing. When you ask Claude to build something — say, an authentication system — the default behavior is to charge ahead and start writing code immediately. That's fast, but it's the kind of fast that leads to three rounds of "actually, I also need..." and "wait, what about..." revisions.

With this skill active, Claude pauses. It surfaces constraints you haven't mentioned. Edge cases you haven't considered. Alternative approaches worth evaluating. Failure modes that would be expensive to discover later.

I tested this on a multi-tenant auth system for a client project. Without the skill, Claude would have built a perfectly functional single-strategy auth flow that I'd then have to iterate on when the client mentioned they need Google SSO, magic links, AND traditional email/password. With the skill, Claude asked me upfront: which auth providers, what session strategy, any multi-tenant requirements, token expiry preferences?

The final implementation was dramatically better on the first pass. I tracked it informally over a month — roughly 30% fewer revision rounds on tasks where I used this skill versus tasks where I didn't. That's not a scientific study. But the pattern was consistent enough that I stopped disabling it.

The skill works because it mimics what experienced developers do naturally: think before coding. Most of us skip that step when we're using AI because the whole point feels like speed. This skill makes the argument — convincingly — that a two-minute thinking phase saves twenty minutes of rework.

Alright, thinking better is powerful. But what about building your *own* skills so Claude thinks the way you specifically need it to? That's skill number five.

## 5. skill-creator — Build Custom Skills Without Writing Code

**Publisher:** Anthropic

Every developer has workflows they repeat. Code review with specific criteria. PR descriptions in a particular format. Data validation checks against a known schema. You explain these workflows to Claude, it does them well, and then next session you explain them again. And again.

```bash
npx skills add https://github.com/anthropics/skills --skill skill-creator
```

skill-creator lets you turn any repeated workflow into a permanent Claude Code skill by describing it in plain language. No code required. You tell it what you want Claude to do — the steps, the format, the constraints — and it generates a complete SKILL.md file that Claude loads automatically in future sessions.

I built a `/review` skill for my own code review process. I have specific preferences: I want security implications called out first, I want performance concerns separate from style concerns, I want a severity rating on each finding, and I want the summary in a specific format my team at Ramlit recognizes. Before this skill, I'd re-explain all of that every time. Now I type `/review` and Claude already knows.

The whole process took one conversation. Describe the workflow, answer a few clarifying questions, and the skill-creator generates the SKILL.md. Done.

Here's the real power though — and this is something I didn't appreciate until I'd built three or four custom skills. These compound. Each custom skill removes one more "setup" conversation from your day. After a month, Claude starts a session already knowing your review format, your commit message style, your documentation structure, your testing preferences. It goes from being a powerful generic assistant to being *your* assistant. That shift matters more than any single skill on this list.

There's one specific tool in my stack that benefited enormously from this kind of customization: Obsidian. And the next skill is purpose-built for it.

## 6. obsidian-skills — Because Obsidian Markdown Isn't Regular Markdown

**Publisher:** kepano (Steph Ango, CEO of Obsidian)

If you don't use Obsidian, skip to skill number seven. If you do use Obsidian, this skill will save you from a specific frustration you've probably already encountered.

Standard Markdown and Obsidian Markdown are not the same thing. Obsidian has its own syntax for internal links (`[[note name]]`), its own query system (Bases, formerly Dataview), Canvas layouts, plugin-specific blocks, and a dozen other extensions that standard Markdown generators — including Claude — don't know about.

Without this skill, asking Claude to generate Obsidian-compatible content is an exercise in manual fixing. Internal links come out as standard Markdown links. Base queries get formatted as code blocks instead of live queries. Canvas references break entirely.

```
/plugin marketplace add kepano/obsidian-skills
```

Or manually clone it into the `.claude/` directory inside your vault.

I use Obsidian as my second brain for project notes, research, and writing drafts. When I asked Claude to generate a research note with internal links to three existing notes, a Bases query for related pages, and a Canvas layout — without this skill, all three broke. With it, they worked on the first try.

The fact that this comes from Steph Ango himself — the CEO of Obsidian — gives me confidence it'll stay current as Obsidian's syntax evolves. That matters for a tool I use daily.

This skill is narrow in scope. It does one thing. But for Obsidian users, it does that one thing perfectly, and no other skill in the registry comes close. Now — from personal knowledge management to professional document handling. The next skill (or rather, skill *family*) solves a problem that's been annoying me since Claude Code launched.

## 7. PDF / DOCX / PPTX / XLSX — Document Parsing That Actually Works

**Publisher:** Anthropic

Claude Code can't natively read most document formats. You can paste text, sure. But hand it a PDF and ask about page 34? A DOCX with embedded tables? An Excel sheet with multiple tabs? Without dedicated parsing, you're copying and pasting content manually, losing formatting, missing tables, and wasting time you could spend on actual work.

```bash
npx skills add https://github.com/anthropics/skills --skill pdf
npx skills add https://github.com/anthropics/skills --skill docx
```

There are matching skills for PPTX and XLSX following the same pattern from the same Anthropic repository.

Here's the moment this became indispensable for me. A client sent over a 40-page SaaS contract as a PDF. They wanted me to review the technical obligations — uptime SLAs, data residency requirements, API rate limits buried in the terms. Normally that's 90 minutes of careful reading, cross-referencing clauses, building a summary.

I dropped the PDF into my project directory, invoked the skill, and asked Claude to extract all termination clauses, liability caps, and data ownership terms. Clean, structured summary in 45 seconds. Not a rough overview — a clause-by-clause breakdown with page references.

I still read the contract myself afterward. I'm not outsourcing legal review to an AI. But having Claude's summary as a map meant I knew exactly where to focus my attention. The 90 minutes became 25.

This skill family is boring. There's nothing exciting about document parsing. But boring tools that save an hour per use are worth more than flashy tools you invoke once and forget. And speaking of tools that save research time — the next two skills handle a kind of research that's traditionally been painful to do from a terminal.

## 8. x-research-skill — Twitter Research Without the $100/Month API Bill

**Publisher:** rohunvora

I need to be upfront about this one. It works. It's genuinely useful. And it operates in a grey area that you should understand before installing it.

Twitter's official API costs a minimum of $100 per month for any meaningful access. That's the Basic tier. For serious research — pulling user timelines, searching tweets by keyword, analyzing threads — you're looking at the Pro tier or higher. For an independent developer doing occasional research, that's a hard cost to justify.

This skill sidesteps the API entirely. It uses TLS client emulation to pull tweets, threads, and user timelines directly. No API key, no monthly fee. You get full research capability from your terminal.

```bash
mkdir -p .claude/skills
cd .claude/skills
git clone https://github.com/rohunvora/x-research-skill.git x-research
```

You'll need Bun installed and an `X_BEARER_TOKEN` environment variable set.

I've used this for content research — understanding how developers talk about a framework before I write about it, surfacing the actual pain points people mention (which are often different from what blog posts focus on), tracking sentiment around new releases. Last month I pulled the last 50 tweets mentioning a specific Claude Code feature, summarized the sentiment, and identified the three most repeated complaints. The whole process took under two minutes.

Now, the honest part. TLS emulation to bypass API restrictions is a grey area. It's not illegal in most jurisdictions — there's no law against making HTTP requests that look like a browser. But it does circumvent Twitter's intended access model, and platform terms of service are a separate consideration. I use this for research, not automation or scraping at scale. Your risk tolerance may differ from mine.

I debated whether to include this skill. I included it because it solves a genuine problem — Twitter is a critical source for developer sentiment and I shouldn't need to pay $1,200 a year to search it — but I wanted you to have the full picture.

The next skill has a similar "access something that's normally blocked" premise, but with a completely different technical approach.

## 9. reddit-fetch — Reddit Research Through a Gemini Backdoor

**Publisher:** ykdojo

Reddit blocks AI agent requests. If you've tried to fetch Reddit content from Claude Code, you've hit this wall. The requests get rejected, and you're back to manually browsing threads and copy-pasting quotes.

This skill takes a creative approach: it routes through Gemini CLI — which has live web access — via tmux, then passes the results back to Claude. It's a relay, essentially. Claude asks Gemini to fetch the Reddit content, Gemini does (because Google's crawler access to Reddit is established), and the content comes back to your Claude session.

```bash
npx skills add https://github.com/ykdojo/claude-code-tips --skill reddit-fetch
```

You'll need Gemini CLI installed and authenticated for this to work.

I use this for the same kind of research as the Twitter skill but for a different audience. Reddit threads about developer tools contain a rawness you don't find in blog posts or tweets. People complain more honestly on Reddit. They describe their actual frustrations in detail. When I'm building a tool or writing about a technology, knowing the real complaints — not the polished "constructive feedback" version — is invaluable.

Last week I pulled top threads from r/programming about a deployment tool I was evaluating. The recurring frustration wasn't about the tool's features (which every review covers) but about its upgrade path breaking existing configs. That's the kind of signal that changes a technical decision. I wouldn't have found it by reading the tool's changelog or blog posts.

Fair warning: this skill depends on Gemini CLI's continued web access and tmux being available in your environment. If either of those breaks, the skill stops working. It's a dependency chain, and dependency chains are fragile. I've been using it for about six weeks without issues, but I wouldn't build a critical workflow on top of it.

We've covered research, design, thinking, document parsing, and workflow automation. The last skill goes somewhere different — somewhere I care about deeply given my work in cybersecurity. It finds vulnerabilities in your code before they find you.

## 10. trailofbits/skills — Security Scanning From the Best in the Business

**Publisher:** Trail of Bits

Trail of Bits is not a random GitHub account. They're one of the most respected security research firms in the world. Their client list includes major protocol audits, Fortune 500 engagements, and foundational work on tools like Slither, Echidna, and Manticore. When Trail of Bits publishes security tooling, I pay attention.

```
/plugin marketplace add trailofbits/skills
/plugin menu
```

This skill suite brings CodeQL and Semgrep static analysis directly into your Claude Code workflow. Instead of running security scanners separately, reviewing reports in another window, and then switching back to fix things — Claude runs the analysis, interprets the results, and can fix the issues in the same session.

I tested this on an authentication module I'd just written. Claude flagged two potential injection vulnerabilities — one in the input validation layer that I'd considered "good enough" and one in a database query I'd parameterized incorrectly — plus a timing attack surface in the token comparison function. Subtle stuff. The kind of findings that would survive a standard code review but not a dedicated security audit.

All three were fixable in under ten minutes. Without the scan, they would have shipped to production. With my background at xCyberSecurity, the irony of shipping vulnerable auth code would have been especially painful.

This is the skill I recommend most urgently to anyone writing code that handles authentication, payment processing, or user data. Static analysis isn't a replacement for a full penetration test — but it catches the low-hanging vulnerabilities that account for the majority of real-world breaches. Running it costs you five minutes. Not running it costs you a lot more when something goes wrong.

## The Skill That Almost Made the List

I want to mention one that didn't quite pass my filter: a shell history analytics skill that tracks your most-used commands and suggests aliases. Clever idea. But when I tested it, the time savings were marginal — maybe 30 seconds a day — and it required persistent access to shell history, which felt like more surface area than I wanted to expose. It solved a real annoyance but not a real pain. Close, but my filter exists for a reason.

There was also a database schema visualization skill with decent install numbers. Nice output, but I couldn't demo it doing something I couldn't already do with a quick `\d+` in psql or a DataGrip diagram. The "can I demo it working?" test killed it.

I mention these because the filter matters more than the list. Skills will come and go. New ones will launch tomorrow that might be better than half of what I've recommended here. The filter is what protects you from installing 40 skills you'll never use while missing the three that would transform your workflow.

## A Note on Security — Because 76 Malicious Skills Isn't a Small Number

I opened this post with the Snyk findings, and I want to close the loop on that before wrapping up.

36% of scanned skills had security flaws. That's more than one in three. And 76 were actively malicious — designed to steal credentials, exfiltrate data, or establish persistence on your machine. This isn't a theoretical risk. This is the current state of the ecosystem as of March 2026.

Here's what I do, practically:

**Source verification.** I strongly prefer skills from Anthropic, Vercel Labs, and established organizations (Trail of Bits, kepano/Obsidian). For individual publishers, I read the source code before installing. Yes, actually read it. SKILL.md files are usually short enough to review in five minutes.

**Permission awareness.** Does the skill request capabilities that match its purpose? A PDF parser needs file read access. A Twitter research tool needs network access. A code formatter that wants both? Suspicious.

**Install count isn't trust.** High install counts mean popularity, not safety. The Snyk report found malicious skills with thousands of installs. People install things without auditing them. Don't be those people.

**Regular audits.** Once a month, I run through my installed skills and ask: am I still using this? Is the repo still maintained? Has anything changed in the permissions? If I can't answer confidently, I remove it.

The skills ecosystem is powerful. It's also young, largely unmoderated, and growing faster than anyone can audit. Treat it like you'd treat any package ecosystem — with enthusiasm tempered by caution.

## What Changes When You Actually Install These

I want to be specific about the cumulative impact because individual skill descriptions undersell it.

Before I adopted this stack, a typical Claude Code session involved a lot of setup conversation. Explaining my review preferences. Defining the visual direction for a UI. Manually feeding document contents. Running security scans in a separate terminal. Browsing Reddit on my phone to check developer sentiment about a library before committing to it.

After — and this took about two weeks of gradual adoption, not an overnight transformation — most of that friction disappeared. Claude starts sessions with my workflows already loaded. Design work begins with a real aesthetic conversation instead of default choices. Document analysis happens in-terminal. Security scanning is part of the build process, not an afterthought.

The time savings are real but hard to quantify precisely. My rough estimate: 40-60 minutes per day across all the small efficiencies. Some of that is direct time savings (document parsing, research). Some is indirect (fewer revision cycles from better upfront thinking, fewer bugs from automated security scanning). Some is just the compounding effect of removing small frictions — the kind that individually seem trivial but collectively fragment your focus.

The more important shift is qualitative. Claude Code went from being a tool I use to being a tool that knows how I work. That's a different relationship with your tooling, and it's one worth building intentionally.

## Your Move

You don't need all ten of these. You might not need half of them. But here's what I'd bet: at least two or three solve a problem you're currently working around manually. And the first one — find-skills — takes 30 seconds to install and makes finding the others trivially easy.

So here's the challenge. Before you close this tab, open your terminal and run this:

```bash
npx skills add https://github.com/vercel-labs/skills --skill find-skills
```

That's it. One command. From there, you can explore the registry from inside Claude Code, evaluate skills against your own filter criteria, and build a stack that fits how *you* work — not how I work or how anyone else works.

The skills ecosystem is messy, fast-moving, and occasionally dangerous. It's also the single biggest force multiplier available to Claude Code users right now. The developers who figure out their personal skill stack early are going to build faster, ship cleaner, and catch problems sooner than those who keep using Claude Code vanilla.

Sixty thousand skills exist. You need maybe ten. Choose them carefully, audit them honestly, and let them compound.

Which one are you installing first?

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
