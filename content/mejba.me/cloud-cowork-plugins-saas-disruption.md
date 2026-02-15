**BRAND:** mejba.me
**TITLE:** Cloud Co-work Plugins Just Broke the SaaS Model
**SLUG:** cloud-cowork-plugins-saas-disruption
**TAGS:** AI Automation, SaaS Industry, Cloud Co-work, Workflow Plugins, Analysis

---

I watched $47 billion in SaaS market cap evaporate in a single trading session last month. Not because of an earnings miss. Not because of a recession scare. Because Entropic shipped a feature called plugins for Cloud Co-work — and Wall Street immediately understood what it meant for every subscription tool sitting in your browser tabs right now.

That number landed differently when I was staring at the fifteen SaaS apps I had open on my own machine. Slack. Notion. Asana. Linear. Three different analytics dashboards. A CRM I barely use but pay $49/month for. A design handoff tool. Two different email platforms. I counted them, felt a little sick, and then spent the next seventy-two hours rebuilding my entire workflow around Cloud Co-work plugins.

What happened next changed how I think about software. Not incrementally. Fundamentally.

Here's the thing most people are missing about this moment — it's not that plugins make Cloud Co-work smarter. That's the surface-level take. The real shift is architectural. Plugins turn a single AI agent into a universal interface that absorbs the functionality of dozens of standalone tools. And once you understand the mechanics of how that works, you start seeing why SaaS CEOs are quietly panicking behind their "we welcome AI integration" press releases.

I'm going to walk you through exactly what these plugins are, how I've been using them to replace tools I've paid for since 2021, and why I think we're watching the early innings of the biggest platform shift since mobile ate desktop software. But first, you need to understand the anatomy of a plugin — because what Entropic built is more elegant than most people realize.

## What a Plugin Actually Is (and Why the "App Store" Analogy Falls Short)

Everyone keeps comparing Cloud Co-work plugins to an app store. I get the impulse, but the comparison misses the point entirely.

An app store gives you standalone applications that each do their own thing in isolation. Plugins are different. Think of them more like a USB stick you plug into an already-brilliant colleague's brain. You're not giving them a new app — you're giving them new expertise, new access, and new workflows that integrate with everything they already know.

Each plugin bundles three components, and understanding all three is critical.

**Skills** are the brains of the operation. A skill is defined in a markdown file — yes, a plain text markdown file — that contains instructions, domain knowledge, reference documents, and sometimes even sub-agent configurations. When I built my first custom skill, I literally wrote a document explaining how I write LinkedIn posts, included five examples of my best-performing posts, and described my tone of voice. Cloud Co-work absorbed it and started producing drafts that sounded like me. Not "AI trying to sound like me." Actually me. My wife read one and asked when I'd found time to write it.

**Connections** handle the plumbing. These are secure integrations that let Cloud Co-work talk to your existing tools — your CRM, your project management system, your email, your file storage. The key detail that matters: connections are scoped by department. Your marketing team's plugin can access the content calendar and social accounts without ever touching the finance team's billing data. This isn't an afterthought. It's core architecture, and it solves the security concern that kills most AI-in-the-enterprise conversations before they start.

**Commands** are the triggers. Slash commands that kick off specific skills or chain multiple skills together into what Entropic calls agentic workflows. One command. Multiple skills firing in sequence. Outputs from one feeding into the next.

When you bundle skills, connections, and commands together, you get a plugin. And when you start chaining plugins together — that's when things get genuinely wild. I'll show you exactly how I set this up in the implementation section, but the short version is this: I replaced a six-tool content repurposing workflow with a single slash command that takes about nine seconds to trigger.

That's not an optimization. That's a category shift.

## Why I Stopped Dismissing This as "Just Another AI Feature"

I'll be honest — my first reaction was skepticism. I've been building with AI agents for over a year now. I've watched dozens of "this changes everything" launches that changed approximately nothing. When Entropic announced plugins, I assumed it was marketing theater. Dress up some prompt templates, call them plugins, get a press cycle.

I was wrong. And understanding why I was wrong matters, because the same skepticism is preventing a lot of smart engineers from recognizing what's happening here.

The difference between Cloud Co-work plugins and every "AI automation" tool I've tested is composability. Most AI tools give you a fixed pipeline: input goes in, output comes out, and if the output isn't quite right, you start over with a different prompt. Plugins create a living system where skills build on each other, share context, and adapt to your specific business logic.

Here's a concrete example that made me a believer. I needed to create a case study for a client project. Normally, my workflow looks like this: transcribe the client interview in Otter, clean it up in Google Docs, pull project metrics from our dashboard, draft the case study in Notion using our brand template, create pull quotes for social media, generate a LinkedIn post, and schedule everything in Buffer. Six tools. Roughly ninety minutes if everything goes smoothly. Things rarely go smoothly.

With a single Cloud Co-work plugin, I fed in the raw transcript and the brand guidelines document. One command. The agent processed the transcript, extracted key metrics and quotes, generated a full case study matching our template structure, created three LinkedIn post variants in my voice, and compiled everything into a single output document with clearly labeled sections.

Fourteen minutes. I spent most of that time reviewing the output, not creating it.

Now multiply that across every repeatable workflow in a business. Sales outreach sequences. Customer support escalation procedures. Product requirement documents. Financial reporting summaries. Legal contract reviews. Each of these can be captured as a skill, bundled into a plugin, and triggered with a command.

That multiplication is what spooked the market. And honestly? The market isn't wrong to be spooked.

## The SaaS Extinction Event Nobody's Prepared For

Here's where I need to be careful, because the hot take writes itself and the hot take is wrong. The hot take is "SaaS is dead." SaaS isn't dead. But the SaaS business model — charging $15-150/month per user for a single-purpose tool with a pretty interface — is facing an existential challenge it hasn't seen since cloud computing killed on-premise software.

The numbers tell the story. Salesforce dropped 8% in a single day after the plugins announcement. ServiceNow fell 6%. Adobe lost 5%. HubSpot, Asana, Monday.com — all took significant hits. These aren't fly-by-night companies. These are the pillars of the modern software stack. And the market is pricing in a future where their primary value proposition — being the interface between a human and a business process — gets absorbed by an AI agent.

I've been watching this pattern closely because I build software for a living. When I audit a client's tech stack, I typically find 15-25 SaaS subscriptions. Most employees actively use maybe five of them. The rest are zombie subscriptions that persist because switching costs are high and nobody wants to be the person who cancels a tool someone might need.

Cloud Co-work plugins change that calculus in a brutal way. The AI agent becomes the universal interface. You talk to one system. That system connects to everything. The individual tools still exist in the background — you still need a database, you still need file storage, you still need email delivery infrastructure — but the human-facing layer shifts from fifteen different UIs to one conversational interface.

I'm not making a prediction here. I'm describing something I'm already experiencing. Last month I canceled three SaaS subscriptions because Cloud Co-work plugins made them redundant. Not because the plugins replicated every feature — they didn't. But because they replicated the features I actually use, which turns out to be about 20% of what I was paying for.

That 80/20 dynamic is the real knife. Most SaaS revenue comes from features users never touch. When the AI agent handles the 20% people actually need, the justification for the subscription collapses. And it collapses fast.

But — and this is the nuance the doomsayers are missing — smart SaaS companies have a survival path. I'll get to that. It's actually a significant opportunity for the ones who move quickly enough.

## Building Your First Plugin: The Practical Guide Nobody Else Is Writing

Alright, enough analysis. Let me show you how this actually works, because the gap between "understanding plugins conceptually" and "having a working plugin that saves you hours" is smaller than you think. I built my first plugin in about forty minutes, and I'm going to walk you through the process I wish someone had documented for me.

**Step 1: Identify Your Highest-Friction Workflow**

Don't start with something ambitious. Start with the workflow that annoys you the most — the one where you think "I can't believe I'm doing this manually again" at least once a week. For me, that was content repurposing. I write a long-form blog post, and then I need to create a LinkedIn post, a Twitter thread, an email newsletter intro, and an infographic outline from the same source material. Five outputs from one input. Five different tools. Five different formatting requirements.

**Step 2: Document the Workflow in Plain English**

Open a markdown file and describe your process like you're explaining it to a sharp intern on their first day. Include the inputs (what raw materials do you start with?), the transformation logic (what decisions do you make at each step?), the quality standards (what makes a good output vs. a mediocre one?), and example outputs.

This step is where most people cut corners, and it shows in their results. The more specific your skill description, the better Cloud Co-work performs. Don't write "create a LinkedIn post." Write "create a LinkedIn post that opens with a counterintuitive hook, runs 150-200 words, uses short paragraphs of 1-2 sentences, includes one specific data point or personal anecdote from the source material, and ends with a question that drives comments."

**Step 3: Create Your Skill File**

Here's the structure I use for every skill:

```markdown
# Skill: [Name]

## Purpose
[One sentence describing what this skill does]

## Input Requirements
- [What the skill needs to work with]
- [Format specifications]

## Process
1. [Step-by-step instructions]
2. [Include decision points: "If X, then Y. If Z, then W."]
3. [Reference any attached documents or templates]

## Output Format
[Exact format the output should follow]

## Quality Criteria
- [Specific, measurable standards]
- [Examples of good vs. bad output]

## Reference Materials
[Links to or embeds of example outputs, style guides, templates]
```

**Pro tip:** Include 3-5 examples of your best work as reference material. Cloud Co-work's pattern matching improves dramatically when it has concrete examples instead of abstract descriptions. I attached my top-performing LinkedIn posts sorted by engagement rate, and the quality jump between "no examples" and "five examples" was night and day.

**Step 4: Set Up Connections**

This is the part that felt intimidating but turned out to be straightforward. Cloud Co-work's connection interface lets you authorize access to external tools through OAuth flows — similar to how you'd connect any third-party app. The difference is the departmental scoping I mentioned earlier.

For my content plugin, I connected Google Docs (for source materials), Buffer (for social scheduling), and my email platform. Each connection asked me to specify what level of access the plugin needed — read-only, read-write, or full access. Start with the minimum permissions you need. You can always expand later.

**Step 5: Define Your Commands**

Commands are where individual skills become automated workflows. The syntax is simple — you're essentially creating slash commands that chain skills together.

```
/repurpose-content
  → Skill: Extract key insights from source document
  → Skill: Generate LinkedIn post
  → Skill: Generate Twitter thread
  → Skill: Generate newsletter intro
  → Skill: Generate infographic outline
  → Output: Compiled document with all formats
```

Each skill in the chain receives the output of the previous skill plus the original source material. This context accumulation is what makes chained workflows feel coherent rather than fragmented.

**Step 6: Test, Iterate, and Refine**

Run your plugin against three different inputs. Not one. Three. The first run shows you whether the basic flow works. The second run reveals edge cases. The third run exposes consistency issues.

After my first test, I realized my LinkedIn skill was writing posts that were too long — 300 words instead of my target 200. I added a hard constraint to the skill file: "Output must be 150-200 words. If the first draft exceeds 200 words, automatically trim by removing the weakest sentence and tightening transitions." That fixed it.

Iteration isn't failure. It's calibration. Budget thirty minutes for refinement and you'll end up with a plugin that genuinely works.

## The Three Plugin Types That Are About to Create a New Economy

Here's what I think most commentary is missing about the business implications of Cloud Co-work plugins. It's not just about individual productivity. It's about the emergence of three distinct plugin categories, each of which creates different economic dynamics.

**Entropic-Built Plugins** are the baseline. Open-source, covering common business functions — sales, support, product management, finance, legal. These are deliberately generic, designed to handle 70% of a standard workflow out of the box. Think of them as the default apps that come pre-installed on your phone. Good enough for most people. Not customized to anyone.

**Third-Party Provider Plugins** are where things get interesting from a business perspective. Imagine Salesforce — instead of fighting Cloud Co-work, they build a Salesforce plugin that lets Cloud Co-work interact with Salesforce's data layer and proprietary features through the agent. The AI handles the interface. Salesforce handles the data infrastructure and business logic that took twenty years to build. Their subscription model shifts from "paying for the UI" to "paying for the engine." That's actually a more defensible position.

I've already seen early-stage startups pivoting their entire business model toward plugin development. A company I advise was building a standalone competitive intelligence tool. After the plugins announcement, they scrapped the UI entirely and rebuilt their core analysis engine as a Cloud Co-work plugin. Their reasoning: why spend two years and $2M building an interface when you can plug into an interface that already has millions of users?

**Custom-Built Plugins** are the long tail, and potentially the most transformative. Any business can create plugins that encode their specific workflows, institutional knowledge, and competitive advantages. A law firm can build a plugin that drafts contracts in their specific style, referencing their clause library. An e-commerce company can build a plugin that generates product descriptions matching their brand voice and SEO requirements. A marketing agency can package their campaign planning methodology as a plugin and license it to clients.

The custom plugin economy is going to look a lot like the early days of WordPress themes and Shopify apps. Some will be free. Some will command premium prices. The best will generate passive income for their creators.

I'm already building three custom plugins for my own workflow, and I'm considering packaging two of them as products. The one that automates my content repurposing process alone saves me roughly six hours per week. If I charged even $29/month for that as a standalone plugin — which is cheap compared to the tools it replaces — the economics work beautifully.

## The Honest Problems Nobody Wants to Talk About

Look, I've spent the last two thousand words painting an optimistic picture, and I believe most of it. But I'd be doing you a disservice if I didn't talk about the genuine problems I've hit, because they're real and they'll affect you too.

**The "good enough" trap is dangerous.** Plugin-generated output is impressive — maybe 80-85% as good as what a skilled human produces. For many use cases, that's sufficient. For content repurposing, quick drafts, and internal documentation, I'll take 85% quality at 10% of the time investment all day long. But I've caught myself letting that "good enough" standard creep into work that demands excellence. A client-facing case study. A technical blog post where accuracy matters. A sales proposal where a single wrong number could cost a deal.

The discipline required is knowing when 85% is fine and when it's not. I've started color-coding my tasks: green for "plugin can handle this end-to-end," yellow for "plugin drafts, I review and edit," and red for "I need to do this myself." That framework has saved me from publishing work I'd regret.

**Context window limitations are real and frustrating.** Cloud Co-work is powerful, but it's still working within token constraints. When I tried to feed it a 40-page product specification document and ask it to generate comprehensive technical documentation, it started losing details from the early pages by the time it reached the implementation section. I had to chunk the document into sections and process them individually, which broke the seamless "one command, full output" experience.

Entropic is clearly working on this — the context windows keep expanding — but right now, complex workflows with large inputs require chunking strategies that add friction.

**Plugin portability is still a pain.** As of today, sharing plugins means exporting a zip file or pushing to a GitHub repository and having the other person import it. There's no marketplace. No one-click install. No version management. Entropic has said a marketplace is coming, along with internal sharing tools for teams. But we're not there yet, and the gap between "I built an amazing plugin" and "my team can use it" is wider than it should be.

**The security implications keep me up at night.** I say this as someone who runs a cybersecurity practice. When you give an AI agent connection access to your CRM, your email, your project management tool, and your file storage — you're creating a single point of compromise. The departmental scoping helps. The OAuth permission model helps. But the attack surface is still concerning, especially for businesses handling sensitive data. I'd love to see Entropic publish a detailed security architecture document. Until then, I'm being cautious about which connections I authorize.

My prediction? These problems will get solved within 12-18 months. They're engineering problems, not fundamental design flaws. But if you're adopting plugins today, you need to know about them.

## What My Numbers Look Like After Sixty Days of Heavy Plugin Usage

I've been tracking my own metrics obsessively because I wanted to know whether plugins deliver real productivity gains or whether the novelty effect was inflating my perception. Here's the unvarnished data.

**Time spent on content creation:** Down 62%. I was averaging 4.5 hours per long-form piece including repurposing. Now I'm averaging 1.7 hours, most of which is editing and quality review.

**SaaS spend:** Down $387/month. I canceled subscriptions for three content tools, one social scheduling platform, and one analytics dashboard that Cloud Co-work plugins replaced functionally.

**Output volume:** Up 3x. I'm publishing more content across more channels without working more hours. Same 50-hour weeks, different distribution of effort.

**Quality consistency:** This one surprised me. Before plugins, my content quality varied depending on my energy level, what day of the week it was, and how many times I'd been interrupted. Plugins produce a consistent baseline that I then elevate through editing. My worst outputs got better. My best outputs stayed about the same.

**Time to measure ROI:** About two weeks of active usage before the gains became obvious. The first week is setup and calibration. The second week is when muscle memory kicks in and you stop thinking about the tool and start thinking about the work.

The metric I care about most — billable hours freed up for higher-value work — improved by roughly eight hours per week. That translates directly to revenue when you're running a consultancy. For an employee, it translates to either getting more done or getting the same amount done with less stress. Both are valuable.

Quick wins come from replacing your most repetitive workflows first. Long-term gains come from building plugins that encode your institutional knowledge so well that new team members can produce expert-level work in their first week. That second category is where the compounding returns live, and I'm only beginning to explore it.

## What Happens When Every Employee Has Their Own AI Agent

Sixty days of plugin usage has shifted my thinking about something much bigger than personal productivity.

We're heading toward a world where the average business employee — not the developer, not the power user, the average person who currently struggles with Excel formulas — has a configured AI agent that understands their role, their tools, their workflows, and their preferences. An agent that gets better the longer they use it because their skills and plugins accumulate institutional knowledge over time.

That's not a productivity improvement. That's a labor market restructuring. And the companies that figure out how to integrate plugins into their operations first will have a structural advantage that compounds monthly.

I keep coming back to a conversation I had with a non-technical friend who runs a twenty-person marketing agency. She'd been drowning in tool sprawl — her team was using nineteen different SaaS products. She told me she couldn't hire fast enough to keep up with client demand. I helped her set up three Cloud Co-work plugins: one for client onboarding, one for campaign planning, and one for reporting. She called me a week later. "My existing team just got 40% faster. I might not need to hire those two positions."

That's one small agency. Multiply it by millions.

I don't have a clean answer for what this means for employment, for SaaS economics, for the way we think about software itself. What I do know is that the plugin model — modular, composable, accessible to non-developers — is the mechanism that takes AI from "interesting demo" to "structural change." And structural change doesn't wait for you to be ready for it.

So here's what I'd tell you to do before the weekend. Pick one workflow. The most annoying, repetitive, soul-draining workflow in your week. Spend forty minutes building a plugin for it. Not because it'll be perfect on the first try — it won't. But because the act of building it will rewire how you think about every other workflow in your life. You'll start seeing plugin opportunities everywhere. And that shift in perception is worth more than any individual plugin you'll ever build.

The SaaS model isn't dead. But the assumption that humans need fifteen different interfaces to do their job? That assumption just got a expiration date. And the clock is already ticking.

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
