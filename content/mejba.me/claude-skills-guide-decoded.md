**BRAND:** mejba.me
**TITLE:** Claude Skills: Anthropic's 32-Page Guide Decoded
**SLUG:** claude-skills-guide-decoded
**TAGS:** AI Development, Automation, Claude Skills, MCP Integration, Guide Breakdown

---

I've been writing the same instructions to Claude for eight months. Every Monday morning, same sprint planning prompt. Every client call, same meeting notes template. Every deployment, same checklist pasted into the chat window like some kind of digital Groundhog Day.

Then Anthropic dropped a 32-page guide about something called Claude Skills — and within a single afternoon, I deleted every saved prompt I had.

Not archived. Deleted. Because once you understand what Skills actually are, going back to copy-pasting prompts feels like going back to writing letters after discovering email. The concept is deceptively simple: you teach Claude something once, inside a folder with a markdown file and some optional scripts, and it just *knows* how to do that thing from that point forward. No re-explaining. No "here's my style guide again." No "remember, we use Tailwind, not Bootstrap."

One SKILL.md file. Some YAML at the top. Done.

But — and here's what I want to dig into — the guide itself is both brilliant and frustrating. Brilliant because the framework it lays out is genuinely powerful. Frustrating because the most important implementation details are buried under layers of polished documentation-speak, and some of the hardest-won lessons about making Skills actually work are mentioned in passing or not mentioned at all.

I spent three days reading the guide, building Skills, breaking Skills, and rebuilding them. What follows is everything I wish someone had told me before I started — the parts Anthropic nailed, the parts they glossed over, and the five patterns that actually matter once you strip away the marketing language.

## The Concept That Makes Everything Click

Here's the mental model that finally made Claude Skills make sense to me, and it's not the one Anthropic leads with.

You probably already know about MCP — the Model Context Protocol that lets Claude connect to external tools like Notion, Linear, Figma, GitHub, Slack, and basically anything with an API. MCP is about *access*. It gives Claude the keys to your tools. Open the door, walk inside, look around.

Skills are different. Skills are about *judgment*.

Think about it this way. You hire a new developer and give them admin access to your entire stack — your project management tool, your CI/CD pipeline, your design system, your communication channels. They can technically touch everything. But they don't know your team's conventions. They don't know that your sprint velocity calculation excludes bug fixes under two story points. They don't know that design handoffs always go through a specific Figma-to-Linear workflow with mandatory accessibility annotations. They don't know that deployment announcements follow a particular format in a particular Slack channel.

Access without judgment creates chaos. MCP gives Claude access. Skills give Claude judgment.

That distinction is the entire guide in a single sentence. Everything else — the YAML frontmatter, the folder structure, the five patterns, the debugging advice — is implementation detail hanging off that core idea.

Once this clicked for me, I stopped thinking about Skills as "fancy prompts" and started thinking about them as institutional knowledge captured in code. And that reframe changed how I built every single one.

The practical question, of course, is how you actually capture that knowledge in a format Claude can use. The guide spends a lot of pages on this, and some of it is genuinely useful. But there's a shortcut nobody tells you about, and I'll get to it after we walk through the mechanics.

## What's Actually in the Folder — And What Each Piece Does

A Claude Skill lives in a folder. At minimum, that folder contains one file: `SKILL.md`. That's it. One markdown file, and you have a working Skill.

At the top of that markdown file sits YAML frontmatter — the metadata block between triple dashes that tells Claude what this Skill is, what it does, and critically, *when to activate it*. The rest of the file contains the actual instructions: how to perform the task, what standards to follow, what tools to use, and what pitfalls to avoid.

Optionally, you can add scripts, reference documents, and templates to the folder. A style guide PDF. A bash script that runs linting. A JSON schema for your API responses. Claude will reference these when executing the Skill.

Here's a minimal example — a Skill that generates weekly status reports for my projects:

```yaml
---
name: weekly-status-report
description: >
  Generates a formatted weekly status report by pulling data
  from Linear and GitHub. Trigger when user asks for a status
  report, weekly update, or progress summary.
---
```

```markdown
## Weekly Status Report Generator

### What This Skill Does
Produces a concise weekly status report covering completed work,
in-progress items, blockers, and next-week priorities.

### Data Sources
1. Pull completed issues from Linear for the current sprint
2. Pull merged PRs from GitHub for the past 7 days
3. Pull open blockers tagged with "blocked" label in Linear

### Report Format
- **Summary** (2-3 sentences, biggest wins and risks)
- **Completed** (bullet list with ticket references)
- **In Progress** (bullet list with percentage estimates)
- **Blockers** (bullet list with owner and age in days)
- **Next Week** (top 3 priorities)

### Style Rules
- No jargon — this goes to stakeholders, not engineers
- Lead each section with the most impactful item
- Blockers must include who owns the resolution
- Keep total length under 500 words
```

That's a complete Skill. When I ask Claude "give me this week's status update," it recognizes the trigger from the description field, loads the Skill, connects to Linear and GitHub through MCP, and produces a report following my exact format and style rules.

No prompt engineering. No remembering where I saved the template. The knowledge lives in the Skill, and Claude knows when to reach for it.

But here's the part the guide buries in a sidebar that deserves to be on page one: **the description field in your YAML frontmatter is the single most important line you will write.** Get this wrong and nothing else matters.

## The Two Lines That Make or Break Your Skill

I lost four hours to this. Four hours of building a perfectly detailed Skill that Claude never triggered. The instructions were clear. The format was precise. The reference documents were comprehensive. And every time I asked Claude to do the thing the Skill was designed for, it just... used its general knowledge instead. Ignored the Skill entirely.

The problem was my description field.

My original description said: `"Helps with code review processes."` Six words. Vague. Passive. Claude had no idea when to activate it because "helps with code review processes" could mean literally anything.

I rewrote it: `"Performs structured code review on pull requests. Trigger when user shares a PR link, asks for code review, requests a PR review, or pastes a diff. Checks for security vulnerabilities, performance issues, naming conventions per team standards, and test coverage gaps."`

Immediately started working. Every single time.

The pattern I discovered through trial and error — and the guide hints at this but doesn't state it clearly enough — is that your description needs three things:

**1. What it does** (active verb, specific output): "Generates weekly reports" not "Helps with reporting."

**2. When to trigger** (explicit trigger phrases): "Trigger when user asks for X, Y, or Z." List the actual words and phrases a human would use. Don't be clever. Be literal.

**3. What it covers** (scope boundaries): "Checks for A, B, C, and D." This tells Claude what's inside the Skill's domain and, by implication, what isn't.

The name field matters too — use kebab-case, no spaces, and make it descriptive. `weekly-status-report` beats `report-v2` beats `wsr`. Claude uses the name as an additional signal for matching, so descriptive names improve trigger accuracy.

I went back and rewrote the descriptions for all seven Skills I'd built. Six of them started triggering correctly immediately. The seventh needed one more round of refinement — turns out my trigger phrases overlapped with another Skill, and Claude was getting confused about which one to load.

Which brings me to a problem the guide doesn't address at all: what happens when Skills conflict. More on that in a minute. First, the three use cases Anthropic designed this system around — because understanding the intended use cases changes how you think about building Skills.

## Three Use Cases — And the One Anthropic Underestimates

The guide breaks Claude Skills into three core applications. Each one solves a genuinely different problem, and conflating them is a mistake I made early on.

**Use Case 1: Document Creation**

This is the most straightforward application. You have a specific type of document — a presentation, a technical spec, a client proposal, a design brief — that needs to follow consistent standards every time. Fonts, structure, tone, required sections, forbidden phrases. Instead of pasting a style guide into every conversation, you encode it once in a Skill.

I built one for client proposals that includes our pricing tiers, standard scope language, required legal disclaimers, and formatting rules. What used to take me 20 minutes of prompt-crafting per proposal now takes one sentence: "Write a proposal for [client] covering [scope]." The Skill handles everything else.

**Use Case 2: Workflow Automation**

This is where Skills start earning serious time back. Multi-step processes that need to happen the same way every time — sprint planning, deployment checklists, onboarding sequences, incident response procedures.

My sprint planning Skill is the one I'm proudest of. It connects to Linear to fetch current project status, analyzes our velocity over the last three sprints, identifies carry-over items that keep slipping, suggests priorities based on deadline proximity and dependency chains, and creates the actual sprint tickets with estimated points. A process that used to consume 90 minutes of my Monday morning now happens in about four minutes, with me reviewing and approving the output rather than generating it from scratch.

The key insight for workflow Skills: you need to be explicit about the *order* of operations and the *decision logic* at each step. "Analyze velocity" is too vague. "Calculate average story points completed per sprint over the last 3 sprints, excluding sprints shorter than 8 days. If velocity is declining for 2+ consecutive sprints, flag this in the planning output with the percentage drop" — that's what Claude needs.

**Use Case 3: MCP Enhancement**

This is the use case Anthropic is most excited about, and honestly, it's the one I think they underestimate the potential of. MCP Enhancement means layering domain expertise on top of tool access. Your Skill doesn't just use Figma — it knows your design system, your component naming conventions, your accessibility requirements, and the specific workflow for handing off designs to engineering.

I built a design-to-development handoff Skill that pulls the latest designs from Figma, extracts component specifications, maps them to our existing React component library, identifies gaps where new components are needed, creates Linear tickets for the engineering work with acceptance criteria pulled from the design specs, and posts a summary to our Slack development channel.

Six different tools. One Skill. One trigger phrase: "Process the new designs."

Here's what Anthropic underestimates about this use case: the compounding value. Every time a domain expert on your team reviews the Skill's output and says "actually, we also need to check X before the handoff," you add that check to the SKILL.md file. Over weeks and months, the Skill accumulates institutional knowledge that would otherwise live only in people's heads. It becomes a living document of how your team actually works — not how the tools were designed to work, but how *your people* use those tools in *your specific context*.

That's not automation. That's organizational memory with execution capability. And I don't think most teams have grasped how powerful that is.

Now — the five patterns that actually structure how you build these things.

## Five Patterns That Cover 90% of Real-World Skills

The guide presents five "proven patterns" for structuring Skills. After building eleven Skills across three projects, I'd say these patterns are genuine — they're not marketing categories. Each one solves a structurally different problem, and trying to force a Skill into the wrong pattern creates subtle bugs that are hard to diagnose.

**Pattern 1: Sequential Workflow**

Steps happen in a fixed order. Step 2 depends on step 1's output. Step 3 depends on step 2. No branching, no conditionals — just a reliable pipeline.

Best for: deployment procedures, compliance checklists, onboarding sequences, data migration scripts.

The key detail the guide gets right: number your steps explicitly and include validation criteria between steps. "Before proceeding to step 3, verify that step 2 produced [specific output]. If not, retry step 2 with [fallback approach]."

**Pattern 2: Multi-MCP Coordination**

The workflow spans multiple external services. Figma to Linear to Slack. GitHub to Jira to Confluence. The Skill orchestrates data flow between tools that don't natively talk to each other.

Best for: cross-tool workflows, design handoffs, release management, cross-team notifications.

My biggest learning here: include explicit data transformation instructions between tool calls. The format Figma exports component specs in isn't the format Linear accepts for ticket descriptions. Your Skill needs to specify exactly how to reshape data as it moves between tools.

**Pattern 3: Iterative Refinement**

Generate output, validate it against criteria, improve it, validate again. The Skill includes its own quality loop rather than producing a single-pass result.

Best for: report generation, content creation, code review where you want comprehensive coverage, any output that benefits from self-critique.

I use this pattern for my client proposal Skill. First draft, then a review pass checking for jargon and ambiguity, then a final formatting pass. The output quality difference between single-pass and iterative is substantial — easily worth the extra 30 seconds of processing time.

**Pattern 4: Context-Aware Selection**

Same goal, different execution paths based on context. Upload a PNG and it uses one workflow. Upload an SVG and it uses another. Small file gets processed inline; large file gets chunked.

Best for: file processing, content generation across different formats, deployment scripts that vary by environment.

This pattern is trickier than it sounds. Your SKILL.md needs clear branching logic: "If the input is [type A], follow steps 1-4. If the input is [type B], follow steps 5-8." Ambiguous branch conditions lead to Claude picking the wrong path and producing correct output for the wrong scenario — which is harder to catch than an obvious error.

**Pattern 5: Domain Intelligence**

The Skill embodies deep expertise in a specific domain — financial compliance rules, security protocols, medical coding standards, legal review criteria. The value isn't in the workflow; it's in the knowledge itself.

Best for: compliance checking, security audits, specialized review processes, any task where "knowing the rules" is the hard part.

This is the pattern where reference documents in your Skill folder earn their keep. A SKILL.md file pointing to a 50-page security compliance checklist can make Claude a genuinely useful first-pass auditor. Not a replacement for human expertise — but a tireless first reviewer who never forgets to check item 47 on page 12.

Alright, that's the framework. Now let me save you the hours I wasted on mistakes the guide doesn't warn you about.

## Four Mistakes I Made So You Don't Have To

**Mistake 1: Vague descriptions that never trigger.**

Already covered this, but it bears repeating because it's the number-one failure mode. If your Skill never activates, the description is almost always the problem. Write trigger phrases that match the exact words a human would use. "Run my deployment checklist" not "assist with deployment processes."

**Mistake 2: Instructions buried in walls of text.**

My first few Skills were novels. Pages of context, background information, edge cases, philosophy about why we do things a certain way. Claude would parse them, but the signal-to-noise ratio was terrible. Important instructions got diluted by explanatory text.

What works better: frontload the critical instructions. Put the non-negotiable rules in the first 20 lines. Use headers aggressively. Reserve background context for a separate reference document in the folder that Claude can consult when needed but doesn't have to process on every invocation.

**Mistake 3: Missing error handling for MCP calls.**

This one bit me hard. My sprint planning Skill worked beautifully — until the Linear API returned a rate limit error one Monday morning, and the Skill just... stopped. No fallback. No retry. No graceful degradation. Just a confused Claude saying it couldn't complete the request.

Your Skill needs to anticipate tool failures. Add explicit instructions: "If the Linear API call fails, wait 10 seconds and retry once. If it fails again, proceed with cached data from the last successful run and note that data may be stale." This is basic resilience engineering, but it's easy to forget when you're focused on the happy path.

**Mistake 4: Trying to do too much in one Skill.**

I built a Skill called "project-manager" that tried to handle sprint planning, status reports, retrospective generation, backlog grooming, and stakeholder updates. It was 400 lines of instructions covering five distinct workflows.

It was terrible. Claude would activate the right Skill but follow the wrong workflow within it. Trigger phrases for different sub-tasks overlapped. The instructions for sprint planning contradicted some of the conventions used in retrospective generation.

I split it into five separate Skills. Each one became simpler, more reliable, and easier to maintain. The overhead of having five folders instead of one is negligible. The improvement in accuracy and consistency was dramatic.

The underlying principle: one Skill, one job. If you're writing "also" or "additionally" in your SKILL.md, you might need a second Skill.

## The Insight That Changes How You Think About AI at Work

The guide ends with a thought that I almost skimmed past, but it's actually the most important idea in all 32 pages.

Most people use AI as a general-purpose assistant. Every conversation starts from zero. You explain your context, your preferences, your standards, your tools, your workflows — and then you get a response shaped by all of that explanation. Tomorrow, you do it again. And again. And again.

Claude Skills flip this model. Instead of bringing the context to the AI in every conversation, you embed the context *inside the AI's environment* permanently. The AI starts every relevant conversation already knowing your standards, already connected to your tools, already understanding your workflows.

This isn't a productivity hack. It's an architectural shift in how humans and AI collaborate.

When I look at the eleven Skills I've built over the past three days, I'm looking at a compressed version of how my team works. Our code review standards. Our deployment process. Our client communication style. Our sprint planning methodology. Our design handoff workflow. All of it encoded, executable, and improving every time someone on the team adds a line to a SKILL.md file.

The organizations that figure this out early — that start building Skills libraries capturing their best practices and institutional knowledge — are going to have a compounding advantage that grows every month. Not because the AI is smarter. Because the AI *knows them better*.

I started this post talking about deleting my saved prompts. That was the symptom. The real change is deeper. I stopped thinking of Claude as a tool I instruct and started thinking of it as a team member I onboard. The Skills folder is the onboarding manual. And just like onboarding a human, the upfront investment of writing clear instructions pays back every single day that person — or that Skill — shows up and does the work without being told twice.

Anthropic built the framework. The 32 pages lay out the mechanics. But the value isn't in the framework — it's in what you put inside it.

So here's my question: what's the one workflow you repeat every week that you could teach Claude once and never explain again? Start there. One folder. One SKILL.md. One description that actually triggers.

Everything else builds from that first Skill that works.

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
