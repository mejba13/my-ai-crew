**BRAND:** mejba.me
**TITLE:** One Person Ran Anthropic's Marketing for 10 Months
**SLUG:** one-person-marketing-team-claude-code
**PRIMARY KEYWORD:** Claude Code marketing automation
**SECONDARY KEYWORDS:** one person marketing team AI, Claude Code skills workflow, AI marketing sub-agents
**META DESCRIPTION:** How Austin Lau ran Anthropic's entire growth marketing solo for 10 months using Claude Code. I rebuilt his system — here's the full architecture.
**TAGS:** AI Automation, Claude Code, Marketing Automation, AI Agents, Case Study
**CONTENT TYPE:** Case Study / Build Log
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will be able to build a multi-agent Claude Code marketing system with sub-agents, an orchestrator, and a single slash command that generates multi-channel content in their own brand voice.

---

Thirty seconds. That's how long it takes Austin Lau to generate a complete set of Google Ads responsive search ads — fifteen headlines, four descriptions, formatted into a CSV ready for upload. The same task used to eat thirty minutes of his day. Every single time.

Here's the part that made me stop scrolling and actually pay attention: Austin isn't an engineer. He'd never opened a terminal before Claude Code launched. He had to Google how to open one. And yet, for ten months, this one person ran the entire growth marketing operation at Anthropic — a company valued at [$380 billion after its Series G in February 2026](https://www.anthropic.com/news/anthropic-raises-30-billion-series-g-funding-380-billion-post-money-valuation). Paid search. Paid social. App stores. Email. SEO. All of it.

Not a team of twelve. Not a team of five. One growth marketer with Claude Code and a collection of custom skills he built himself.

When I first heard this story, my immediate reaction wasn't "that's impressive" — it was "I need to understand exactly how he did it, and then I need to build my own version." So that's what I did. I spent a weekend reverse-engineering Austin's approach, adapting his architecture for personal brand content, and stress-testing it against my own marketing workflow. What I found changed how I think about the relationship between AI agents, marketing operations, and the future of one-person teams.

But before I walk you through the rebuild, you need to understand what Austin actually built — because it's more sophisticated than "I told AI to write my ads."

## What Austin Lau Actually Built Inside Claude Code

The [official Anthropic blog post](https://claude.com/blog/how-anthropic-uses-claude-marketing) tells the clean version. Austin built a custom slash command called `/rsa` that generates responsive search ads. He types the command, provides campaign data and keywords, and Claude cross-references everything against brand voice guidelines, product accuracy rules, and Google Ads best practices he'd encoded as Agent Skills.

That's the headline. The architecture underneath is what matters.

Austin's system isn't a single prompt doing everything. It's a network of specialized components working together — and this is the insight most people miss when they hear "AI replaced my marketing team." The system has three layers:

**Layer 1: The Data Foundation.** Austin fed Claude comprehensive context about Anthropic's brand — tone of voice guidelines, product messaging, existing high-performing ad copy, campaign performance data. This isn't a paragraph of instructions. It's a structured knowledge base that every other component references.

**Layer 2: Specialized Agent Skills.** Each marketing function gets its own skill definition. One skill handles brand voice compliance. Another handles Google Ads RSA best practices (character limits, headline diversity rules, description formatting). Another handles product accuracy — making sure Claude never hallucinates features or misrepresents capabilities. These skills act as guardrails and knowledge modules simultaneously.

**Layer 3: The Orchestrator.** The `/rsa` slash command isn't doing the work itself. It's routing inputs to the right skills, assembling outputs from multiple specialized processes, and formatting everything into upload-ready deliverables. It's a conductor, not a soloist.

The result? A workflow that exports fifteen headlines and four descriptions per ad group into a CSV file ready for direct upload to Google Ads — after human review, which Austin emphasizes is non-negotiable. He also built a Figma plugin that generates up to 100 ad creative variations with a single click by identifying frames within templates and swapping headlines and descriptions programmatically.

What struck me wasn't the efficiency gain — though cutting thirty minutes to thirty seconds is a 98% reduction that compounds fast across hundreds of ad groups. What struck me was the architecture pattern. Specialized sub-agents coordinated by an orchestrator, triggered by a single command, producing multi-format output. That pattern isn't specific to Google Ads. It's a general-purpose content production system.

And I wanted one for myself.

## Why I Decided to Rebuild This for Personal Brand Content

I've been [building AI marketing systems in Claude Code](/build-ai-marketing-team-claude-code) for a while now. My five-agent setup handles research, social content, email, design direction, and data analysis. It works. But watching Austin's approach exposed a gap in my own workflow.

My system was agent-centric — each agent operated independently, and I manually routed work between them. Austin's system is skill-centric with orchestration. The difference is significant. In my setup, I'm the orchestrator. I decide which agent handles what, I copy context between them, I assemble the final deliverables. In Austin's setup, the orchestrator agent handles all of that. The human provides the input and reviews the output. Everything in between is automated.

That's the gap I wanted to close. Not because I'm lazy — because routing and assembly work is exactly the kind of cognitive overhead that makes you feel busy without being productive. Every minute I spend copying a headline from one agent's output and pasting it as context for another agent is a minute I'm not spending on strategy, testing, or creative direction.

So I set out to build a personal brand content system modeled on Austin's architecture — but instead of Google Ads, optimized for the content channels I actually use: LinkedIn posts, email subject lines and bodies, video hooks, and social media copy.

The build took me about six hours spread across a weekend. Here's the exact process.

## Step 1: Clone Your User Profile Into a Data Packet

Austin's system works because it starts with comprehensive context. Not "I'm a tech blogger" comprehensive — I mean granular, structured data about who you are, what you offer, who you serve, and how you communicate.

I created a user profile document that Claude references as the foundation for every piece of content it generates. Here's the structure I used:

```markdown
# User Profile — Content System Foundation

## Identity
- Name: [Your name]
- Role: [Your primary professional identity]
- Brand: [Your brand/site URL]
- Niche: [Specific area of expertise]

## Audience
- Primary: [Who reads your content — be specific]
- Pain points: [Top 3-5 problems they're trying to solve]
- Aspiration: [What they want to become/achieve]
- Technical level: [How much do they already know]

## Content Pillars
- Pillar 1: [e.g., AI Development & Automation]
- Pillar 2: [e.g., Cloud & DevOps]
- Pillar 3: [e.g., Software Engineering]
- Pillar 4: [e.g., Building in Public]

## Offers
- Primary: [What you sell or provide]
- Secondary: [Other services/products]
- CTA preference: [How you like to close — soft, direct, etc.]

## Differentiators
- What makes your perspective unique
- Real experiences/projects you reference
- Opinions you hold that others in your space don't
```

This document lives in my project's context directory so every agent and skill can reference it. The critical thing here — and this is where most people's AI content falls flat — is specificity. "I help developers" is useless context. "I help mid-level developers who are transitioning from writing code manually to building with AI agents, and who are skeptical of AI hype but open to pragmatic tools that save real time" gives Claude enough to actually hit the right tone and angle.

I spent about forty-five minutes filling this out honestly, including examples of topics I'd covered before and my actual opinions on contentious subjects in my space. That forty-five minutes pays dividends on literally every piece of content the system produces.

## Step 2: Extract Your Tone of Voice Into a Living Document

This is the step that separates content that sounds like you from content that sounds like "professional AI output." Austin's system at Anthropic includes brand voice guidelines as a core skill. For a personal brand, you need something more intimate — a tone of voice kit that captures the way you actually write and speak.

Here's the process I followed. I gathered five pieces of content I'd written that I felt genuinely represented my voice — two blog posts, a LinkedIn thread, an email newsletter, and a video script. I fed them to Claude with this prompt:

```
Analyze these five pieces of content from my brand. Extract a detailed
tone of voice guide that captures:

1. Sentence structure patterns (length variation, use of fragments,
   paragraph rhythm)
2. Vocabulary preferences (words I overuse, words I avoid, technical
   vs conversational balance)
3. How I open pieces (patterns in my hooks and introductions)
4. How I handle technical explanations (analogies, code examples,
   step-by-step vs narrative)
5. My relationship with the reader (peer, mentor, friend, authority)
6. Opinions and editorial stance (where I'm opinionated, where I
   hedge, where I'm direct)
7. What I NEVER do (cliches I avoid, tones I never strike)
```

The output was a two-page tone of voice document that I reviewed, refined, and saved as a reference file. Claude nailed about 80% of my patterns on the first pass. The 20% I corrected were mostly around edge cases — like the fact that I'm deliberately more casual in LinkedIn posts than in blog tutorials, or that I use profanity sparingly but intentionally when something genuinely surprised me.

This tone of voice kit becomes a skill that both sub-agents reference. Every piece of content runs through the voice filter, which is why the output sounds consistent across formats — a LinkedIn post and an email subject line feel like they came from the same person, even though they serve completely different purposes.

## Step 3: Build Your Hook Library From Proven Patterns

Austin's system benefits from Anthropic's existing high-performing ad copy as training data. For personal brand content, your equivalent is a library of proven hooks — opening lines, subject lines, and attention-grabbers that have actually worked.

I compiled a library of roughly 150 hooks organized by type:

- **Curiosity hooks:** "I almost didn't test [X]. Then I saw what it could do."
- **Contrarian hooks:** "Everyone says you need [common advice]. They're wrong."
- **Story hooks:** "At 2 AM on a Tuesday, my deployment was failing and..."
- **Stat hooks:** "73% of [audience] have this exact problem."
- **Result hooks:** "I cut my [task] from [time] to [time]. Here's how."
- **Question hooks:** "What if the biggest problem with your [X] isn't [obvious thing]?"

Each hook in the library includes the template pattern, two to three real examples, and a note about which content format it works best for (LinkedIn favors curiosity and contrarian hooks; email subject lines favor result and question hooks; video hooks favor story openers).

This library gets loaded as context for the hook-writing sub-agent. Instead of generating hooks from scratch every time — which produces generic, predictable openers — the agent draws from patterns with proven engagement and adapts them to the specific topic.

You don't need 150 to start. Thirty solid hooks across five categories is enough to avoid repetition and give the agent meaningful variety to work with.

## Step 4: Build the Sub-Agent Architecture

This is where Austin's pattern gets really interesting, and where I spent most of my build time. The system uses two specialized sub-agents coordinated by an orchestrator — not one mega-agent trying to do everything.

I've [written about why single agents fail at scale](/build-ai-marketing-team-claude-code) before. The short version: when you ask one agent to handle hooks AND body copy AND formatting AND voice compliance, the model's attention gets diluted. Instructions for writing punchy headlines compete with instructions for writing detailed email bodies. Everything gets a little worse.

Austin's architecture solves this with specialization.

### Sub-Agent 1: The Hook Writer

This agent has one job — generate attention-grabbing opening lines across multiple formats. Its skill definition includes:

- The hook library as reference context
- The user profile for topic relevance
- The tone of voice kit for personality
- Format-specific constraints (LinkedIn character patterns, email subject line length limits, video hook timing)

When triggered, it receives a topic and produces a set of hooks — typically three to five per format. It doesn't write body copy. It doesn't format deliverables. It writes hooks. That focus means every hook gets the model's full attention.

Here's a simplified version of what the skill definition looks like:

```markdown
# Hook Writer Sub-Agent

## Role
Generate attention-grabbing hooks for marketing content across
multiple formats.

## Context Files
- @user-profile.md
- @tone-of-voice.md
- @hook-library.md

## Output Formats
For each topic, generate:
- 3 LinkedIn post hooks (first 2 lines that appear before "see more")
- 3 email subject lines (under 50 characters, curiosity-driven)
- 2 video hooks (first 10 seconds of script, conversational)

## Rules
- Every hook must create an open loop or promise specific value
- Match the tone of voice guide exactly
- Never use generic openers ("In today's world...", "Have you ever...")
- Reference specific tools, numbers, or scenarios from the topic
- Vary structure across the set — no two hooks should follow the
  same pattern
```

### Sub-Agent 2: The Body Copy Writer

This agent takes a hook and expands it into complete content for each format. Its skill definition includes:

- The user profile for positioning and CTA preferences
- The tone of voice kit for consistent personality
- Format-specific templates (LinkedIn post structure, email body structure, video script structure)
- Length constraints per format

The body copy writer receives a topic AND the selected hooks from Sub-Agent 1, then produces complete content pieces. The hooks provide direction — the body writer doesn't have to figure out the angle, it just needs to deliver on the promise the hook made.

```markdown
# Body Copy Writer Sub-Agent

## Role
Expand hooks into complete, publish-ready content pieces.

## Context Files
- @user-profile.md
- @tone-of-voice.md

## Input
- Topic summary or article URL
- Selected hooks from Hook Writer

## Output Formats
For each hook provided, generate:
- LinkedIn post (150-300 words, end with engagement question or CTA)
- Email body (200-400 words, conversational, single CTA)
- Video script expansion (30-60 second script from the hook)

## Rules
- The body must deliver on whatever the hook promised
- Include at least one specific example, number, or reference per piece
- Match tone of voice guide — especially paragraph rhythm and
  vocabulary preferences
- End each piece with a clear next step for the reader
- Never repeat the hook verbatim in the body
```

### The Orchestrator: Tying It All Together

The orchestrator is the slash command that makes the whole system feel like magic. It's modeled directly on Austin's `/rsa` command — a single entry point that routes work through the sub-agents and assembles the final output.

Here's the flow:

1. You type `/content` followed by a topic (or paste an article URL)
2. The orchestrator reads the input and generates a topic brief
3. It sends the brief to the Hook Writer sub-agent
4. The Hook Writer returns hooks across all formats
5. The orchestrator sends the hooks plus original brief to the Body Copy Writer
6. The Body Copy Writer returns complete content pieces
7. The orchestrator assembles everything into a structured deliverable

The skill definition for the orchestrator looks something like this:

```markdown
# Content Orchestrator

## Trigger
/content [topic or URL]

## Process
1. If input is a URL, fetch and summarize the article
2. Generate a topic brief: key angle, target audience segment,
   primary insight
3. Route brief to Hook Writer — collect hook variations
4. Route hooks + brief to Body Copy Writer — collect complete pieces
5. Assemble final output as structured content bank

## Output Format
### LinkedIn Posts
[Hook 1]
[Body 1]
---
[Hook 2]
[Body 2]

### Email Campaign
Subject lines: [List]
Body: [Full email]

### Video Hooks
[Hook + script expansion 1]
[Hook + script expansion 2]

## Quality Gate
Before outputting, verify:
- Every piece matches the tone of voice guide
- No two pieces use the same angle or hook structure
- All format-specific constraints are met
- CTAs are present and brand-appropriate
```

If you'd rather have someone build this entire setup for you — the sub-agents, the orchestrator, the voice kit, the hook library — I take on exactly this kind of Claude Code automation project. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Step 5: Deploy, Test, and Break It

I'm not going to pretend my first run was perfect. It wasn't.

The first test I ran — pasting one of my own article URLs into `/content` — produced LinkedIn posts that were technically correct but felt sterile. The hooks were strong (the hook library earned its keep immediately), but the body copy read like a summary rather than a personal take. It had my vocabulary but not my opinion.

The fix was adding a specific instruction to the Body Copy Writer: "Always include at least one first-person opinion, reaction, or experience related to the topic. The reader should feel the writer has skin in the game, not that they're reporting from the sidelines."

That single line transformed the output.

The second issue was repetition across formats. The LinkedIn post and the email body were making the same point with slightly different words. Useless. I needed each format to approach the topic from a different angle. So I added a constraint to the orchestrator: "Assign a different angle to each format. LinkedIn = counterintuitive insight. Email = practical takeaway. Video = personal story."

After three rounds of testing and refinement, the system was producing content I'd actually publish with light editing. Not zero editing — I still review everything and add personal touches. But the difference between "write everything from scratch" and "review and refine AI-generated content that already sounds like me" is the difference between a thirty-minute task and a five-minute task.

## The Honest Assessment: What This Gets Right and What It Doesn't

Here's where I break from the hype. This system is genuinely powerful, but it's not a replacement for marketing strategy. It's an execution accelerator.

**What it gets right:**

The multi-channel output is real. One topic, one command, five to eight publishable content pieces in under two minutes. The voice consistency across formats is noticeably better than what you get from a single mega-prompt. And the hook quality — because the hook writer has a curated library to draw from — is consistently above what I'd produce under time pressure.

The parallel processing of specialized sub-agents is the key architectural insight. It's the same principle behind Austin's success at Anthropic: don't build one system that does everything; build specialized components that each do one thing well.

**What it doesn't solve:**

Strategy. The system can't tell you what to write about. It doesn't know which topics will resonate with your audience this week, which conversations are trending in your niche, or which angle will differentiate you from the twelve other people writing about the same subject. You still need to bring the strategic thinking.

Genuine novelty. The system produces excellent riffs on existing ideas, but it doesn't generate original research, run experiments, or have experiences worth writing about. The best content comes from combining this system's execution speed with your own original insights, data, and stories.

Distribution and engagement. Producing content is only half the game. The system doesn't post for you, respond to comments, engage with your community, or build the relationships that turn followers into customers. Austin still reviews every piece of output. So do I.

**The honest productivity math:**

Before this system, generating a week's worth of multi-channel content (three LinkedIn posts, two emails, four video hook scripts) took me roughly four to five hours. With the system, the generation phase takes about fifteen minutes. But review, editing, and adding personal touches takes another forty-five minutes to an hour. So the real reduction is from five hours to about one hour. An 80% time savings — not 98% like Austin's ad generation, but significant enough to change how I allocate my week.

## What This Means for Solo Marketers and Small Teams

Austin Lau's story isn't an anomaly. It's a preview.

When Anthropic closed its [$30 billion Series G at a $380 billion valuation](https://techcrunch.com/2026/02/12/anthropic-raises-another-30-billion-in-series-g-with-a-new-value-of-380-billion/) in February 2026, one of the data points that caught investors' attention was Claude Code's run-rate revenue exceeding $2.5 billion — more than doubling since the start of the year. That growth isn't coming from novelty. It's coming from people like Austin building systems that produce measurable operational leverage.

The pattern is clear. According to [Anthropic's own case study](https://claude.com/blog/how-anthropic-uses-claude-marketing), their Customer Marketing team now drafts case studies in thirty minutes instead of two and a half hours — saving ten hours per week. Austin's ad generation went from thirty minutes to thirty seconds. These aren't theoretical projections. They're measured results from a company using its own tool in production.

For solo marketers and small teams, the implication is stark: the execution barrier to multi-channel marketing is collapsing. The competitive advantage shifts from "who can produce more content" to "who has better strategy, better data, and better taste." If you're still spending four hours a week on content production that a Claude Code skill system could handle in fifteen minutes, you're not just inefficient — you're allocating your most valuable resource (focused thinking time) to your lowest-leverage activity.

I've been building [Claude Code skills systems](/claude-skills-ai-marketing-team) for months now, and the trajectory is unmistakable. Each new Claude Code update makes the sub-agent architecture more capable. [Skills have evolved](https://code.claude.com/docs/en/skills) from simple prompt templates to full programmable agents with invocation control, tool restrictions, model overrides, and lifecycle hooks. The gap between what a solo operator can produce and what a traditional marketing team produces is shrinking fast.

## The Six-Step Playbook: Your Rebuild Checklist

If you want to build this system yourself, here's the sequence I'd follow based on everything I learned:

**1. User Data Cloning (45 minutes).** Fill out the user profile document with painful specificity. The more detailed your context, the better every downstream output gets. Don't rush this.

**2. Tone of Voice Extraction (30 minutes).** Feed five of your best content pieces to Claude and extract a tone of voice guide. Review it. Correct the 20% it gets wrong. Save it as a reference file.

**3. Hook Library Integration (1-2 hours).** Compile thirty to fifty proven hooks organized by type. Include your own best-performing openers and supplement with proven frameworks. This library is what prevents your content from sounding generic.

**4. Skill Architecture Build (2-3 hours).** Build two sub-agents (Hook Writer and Body Copy Writer) and one orchestrator skill. Start simple — you can add complexity later. Test each sub-agent independently before connecting them.

**5. Deploy and Test (1 hour).** Run the `/content` command against five different topics. Identify where the output falls flat and refine your skill definitions. Common fixes: adding opinion requirements, enforcing angle diversity across formats, tightening voice constraints.

**6. Extend (ongoing).** Add new sub-agents for platforms you want to cover — YouTube descriptions, Twitter threads, podcast show notes. Each new sub-agent slots into the existing orchestrator without rebuilding anything.

Total initial build time: approximately six hours. Total ongoing benefit: four-plus hours saved per week on content production, with higher consistency and quality than rushing through everything manually.

## Frequently Asked Questions

### Do you need coding experience to build this Claude Code marketing system?

No. Austin Lau had never opened a terminal before building his system at Anthropic. Claude Code skills are written in markdown, not traditional programming languages. If you can write structured instructions, you can build skills.

### How is this different from just using ChatGPT or Claude with a long prompt?

The sub-agent architecture is the key difference. Instead of one model trying to handle hooks, body copy, voice compliance, and formatting simultaneously, specialized agents each handle one task with full attention. The orchestrator coordinates them — producing higher quality output than any single prompt can match.

### Can this system handle content in multiple languages?

Yes, with additional configuration. You'd create language-specific tone of voice kits and add language routing to the orchestrator. The sub-agent architecture actually makes multilingual content easier because you can swap voice kits without rebuilding the entire workflow.

### What does this cost to run monthly?

Claude Code runs on Anthropic's API pricing. For a typical solo marketer generating fifteen to twenty content batches per month, expect roughly $50-80 in API usage on the Max plan. The time savings — four-plus hours per week — makes the ROI straightforward for anyone whose time is worth more than $5/hour.

### How long before the system produces content you'd actually publish?

Plan for three to five rounds of refinement after the initial build. My first output was about 70% publishable. After tweaking voice constraints, adding opinion requirements, and enforcing angle diversity, the system hit 90%+ publishable quality — still requiring light human editing, which I consider non-negotiable.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
