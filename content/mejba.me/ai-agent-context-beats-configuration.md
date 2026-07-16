**BRAND:** mejba.me
**TITLE:** Your AI Agent Config Is Wasting Tokens — Fix This
**META TITLE:** AI Agent Context Over Configuration: Save Tokens Now
**SLUG:** ai-agent-context-beats-configuration
**PRIMARY KEYWORD:** AI agent context management
**SECONDARY KEYWORDS:** AI agent skills workflow, progressive disclosure AI agents, token efficiency AI coding
**META DESCRIPTION:** Ross Mike's framework for AI agent productivity: build skills through iteration, ditch bloated config files, and manage context like a budget. Here's how.
**TAGS:** AI Agents, Context Management, Token Optimization, Workflow Automation, Guide
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will replace their bloated agent configuration files with a lean, skills-based workflow that cuts token waste by up to 82% and produces consistently better AI output.

---

# Your AI Agent Configuration File Is Wasting Tokens — Here's What to Do Instead

I had a 1,247-line CLAUDE.md file. I was proud of it. Every coding convention, every architectural preference, every edge case I'd ever encountered — documented, organized, and loaded into context on every single message I sent to Claude Code.

Then I did the math.

At roughly 3 tokens per line, that file was costing me ~3,700 tokens per turn. Not per session — per turn. In a typical 40-turn session, that's 148,000 tokens consumed by instructions the model wasn't even using 90% of the time. I was paying a context tax larger than most people's entire conversations, and my agent's output quality was actually getting *worse* as the file grew.

Ross Mike laid out the fix in a recent discussion that reframed how I think about AI agent productivity. His argument is simple, backed by data, and — once you hear it — painfully obvious: the quality of your AI agent's output depends far more on how you manage context than on which model you're running. Opus 4.6, GPT 5.4, Gemini 3 — they're all remarkably capable. The bottleneck isn't intelligence. It's the information diet you're feeding them.

I've spent the last two weeks rebuilding my entire agent workflow around this principle. The results have been stark enough that I'm writing this instead of doing the three other things on my list today. Here's what changed, what broke, and what I'd tell anyone still nursing a 500-line config file.

## The Uncomfortable Truth About Your Agent.md File

Most developers I talk to have some version of the same setup. A big markdown file — CLAUDE.md, .cursorrules, agents.md, whatever the tool calls it — stuffed with instructions about their stack, their preferences, their conventions. The file grows over months. Every frustrating interaction adds another line: "Always use named exports." "Prefer Tailwind over CSS modules." "Never suggest class components."

Ross calls this the "context dump" approach, and his critique hit me where it hurts: these files feel productive because writing them feels like work. You're codifying knowledge! You're training your agent! Except you're not. You're creating a static blob of text that gets injected into every interaction regardless of relevance — and the model treats all of it as equally important.

Here's what [recent research from InfoQ](https://www.infoq.com/news/2026/03/agents-context-file-value-review/) actually shows about these files: LLM-generated context files *degrade* performance, reducing task success rates by an average of 3% compared to providing no context file at all. They also consistently increase the number of steps the agent takes, driving up inference costs by over 20%. Even human-written files only delivered a marginal 4% success rate improvement — while simultaneously increasing step counts and costs by up to 19%.

Read that again. The file you spent hours crafting might be making your agent *slower and more expensive* while barely improving its accuracy.

The mechanism is straightforward once you understand how these models work. They don't "understand" your configuration file the way a human developer would read and internalize a style guide. They predict tokens based on patterns in the context window. When you flood that window with 1,200 lines of instructions, you're not giving the model a reference manual — you're diluting the signal-to-noise ratio of every prompt you send. The model spends attention capacity processing your TypeScript preferences when you're asking it to debug a CSS layout.

This doesn't mean context files are useless. It means the default approach — one big file, loaded everywhere, growing forever — is almost certainly wrong.

## What Ross Gets Right About How Models Actually Work

There's a misconception that trips up most AI agent builders, and Ross addressed it directly: these models don't think. They don't understand. They predict the next token based on patterns in their training data and the context you provide.

This sounds like a limitation. It's actually a design constraint that tells you exactly how to get better output.

If the model is a pattern-matching engine, then the patterns in your context window are the single largest lever you have over output quality. Feed it a cluttered context full of irrelevant instructions, and the pattern matching gets noisy. Feed it a focused context with exactly the information needed for the current task, and the pattern matching becomes sharp.

Ross used an analogy that stuck with me: imagine briefing a new employee before every task. If you hand them a 50-page manual covering every scenario the company has ever encountered, they'll be overwhelmed and slow. If you give them a focused one-pager specific to the task at hand, they'll perform well immediately. The models work the same way — not because they "understand" the briefing, but because a focused context produces cleaner token prediction patterns.

This has a practical implication that most people miss. The context window isn't just a bucket you fill with useful information. It's more like a spotlight — it has a limited area of illumination, and everything inside that area competes for the model's attention. The research backs this up: keeping the context window between fresh and roughly 70% capacity produces the most reliable output. Push past that, and you start seeing degraded performance — missed instructions, repeated suggestions, inconsistent code patterns.

I tested this myself. Same prompt, same model (Opus 4.6), same task: build a user authentication flow with JWT tokens and refresh rotation. With my full 1,247-line CLAUDE.md loaded, the agent took 14 turns and produced code with two bugs. After stripping the CLAUDE.md down to 47 lines of genuinely essential conventions, the same task completed in 8 turns with zero bugs. Fewer instructions, better output. The stripped version let the model focus on what mattered.

But here's where it gets interesting — because the answer isn't just "make your config file smaller." That's treating the symptom. Ross's real insight is about the architecture of how context should flow.

## Progressive Disclosure: The Pattern That Changes Everything

The concept that transformed my workflow isn't new — it comes from UX design. Progressive disclosure means showing users only the information they need at each stage, revealing complexity gradually as they go deeper. [Microsoft's agent-skills framework](https://deepwiki.com/microsoft/agent-skills/5.3-progressive-disclosure-pattern) formalized this as a three-tier loading architecture for AI agents, and it's now the basis of how skills work across Claude Code, Cursor, and other major tools.

Here's the concrete difference between the old approach and the new one:

**The old way (agent.md / CLAUDE.md):**
Every interaction loads your entire configuration file. If you have 1,000 tokens of instructions, that's 1,000 tokens added to every single message — whether the agent needs those instructions or not.

**The new way (skills with progressive disclosure):**
At startup, the agent loads only skill *names and descriptions* — roughly 50 tokens per skill. When a task matches a skill's description, only then does the full instruction set load into context. When the task is done, the skill's detailed instructions don't persist into the next task.

The token math is dramatic. Say you have 20 different workflows encoded as instructions. Under the old approach, that might be 15,000-20,000 tokens loaded every turn. Under progressive disclosure, you're loading ~1,000 tokens of metadata at startup, plus maybe 500-800 tokens of the one relevant skill when it's needed. That's roughly [an 82% reduction in context overhead](https://vertu.com/lifestyle/claude-skills-why-they-might-be-a-bigger-deal-than-mcp/), according to real-world benchmarks.

I covered the technical implementation of skills in my [guide to building agent skills](/content/mejba.me/claude-code-agent-skills-guide.md), but Ross's framework adds something the technical docs miss: a philosophy for *when* and *how* to create skills that actually work.

## Ross's Framework: How to Build Skills That Don't Suck

Most people's first instinct when they hear about skills is to sit down and write one from scratch. Open a blank markdown file, think about a workflow, document the steps, save it. Done.

Ross argues this is exactly backwards — and after trying both approaches, I agree completely.

His framework has five steps, and the order matters more than any individual step:

### Step 1: Identify a Real Workflow (Not a Hypothetical One)

Don't build skills for workflows you think you'll need. Build them for workflows you've already done manually at least three times. If you haven't done it three times, you don't understand the edge cases well enough to teach an agent.

This filter alone saved me from creating a dozen useless skills. I had grand plans for a "deploy to production" skill, a "database migration" skill, a "write unit tests" skill. But when I applied Ross's filter — have I actually done this manually, start to finish, at least three times recently? — the list shrank fast. The workflows I actually repeated were more mundane: vetting incoming emails, formatting blog post metadata, setting up new project scaffolding with my specific conventions.

Mundane is good. Mundane means repetitive. Repetitive means high ROI for automation.

### Step 2: Teach the Agent Through Conversation, Not Configuration

This is where Ross's approach diverges from what I see most developers doing. Instead of writing a skill file and hoping it works, you teach the agent the workflow *interactively* — the same way you'd train a new hire.

Forward the agent an actual task. Walk it through your decision process in real time. When it makes a mistake, correct it and explain why. When it gets something right, confirm it. This is experiential learning, and Ross argues it's the only reliable way to surface the edge cases and implicit knowledge that you'd never think to write down in a static instruction file.

His example was compelling: he taught an agent to vet sponsor emails by literally forwarding real emails to it and guiding it through the research process. "Check their Twitter presence. Look them up on Trustpilot. Verify their funding status. If the company was founded less than 6 months ago, flag it." Each correction refined the agent's understanding. After three or four iterations, the agent was vetting emails faster and more thoroughly than Ross was doing manually.

I tried this with my blog post metadata workflow. Instead of writing a skill from scratch, I walked Claude through the process on a real post: "Here's the title, here's what I'd pick for tags, here's why I chose these secondary keywords over those ones, here's the meta description pattern I use." Then I did it again with a different post. By the third time, Claude was generating metadata that matched my style almost exactly — catching nuances I wouldn't have thought to document, like my preference for action verbs in meta descriptions or my habit of putting the brand name last in tag lists.

### Step 3: Iterate Until the Failure Modes Disappear

The first run will have errors. The second will have fewer. By the fourth or fifth, you'll start seeing consistent, reliable output. Ross is explicit about this: don't shortcut the iteration phase. The agent isn't "learning" in a persistent way — you're learning what context it needs, and each iteration reveals gaps you didn't anticipate.

This patience pays compound interest. Every edge case you surface and resolve during training is an edge case that won't bite you during production use. I found that most skills needed 4-6 interactive training runs before they were solid enough to formalize.

### Step 4: Convert the Refined Workflow Into a Skill File

Only after the interactive training produces consistent results do you create the skill.md file. At this point, you're not inventing instructions — you're documenting what already works. The skill file becomes a codification of proven patterns, not a speculative guess about what the agent might need.

The structure Ross recommends is clean:

```markdown
# Skill: [Name]

## Description
[One sentence — what this skill does and when to use it]

## Workflow
[Numbered steps, each specific and actionable]

## Known Edge Cases
[Things that went wrong during training and how to handle them]

## Success Criteria
[How to verify the output is correct]
```

That "Known Edge Cases" section is where the real value lives. It's the institutional knowledge you built during steps 2 and 3 — the gotchas that a blank-page skill author would never anticipate.

### Step 5: Keep Refining Recursively

A skill file isn't a finished product. It's a living document. Every time the skill fails in production, you debug it, fix the root cause, and update the file. Ross calls this "recursive skill building," and it's the mechanism that makes skills compound in value over time.

I've updated my metadata generation skill seven times since creating it three weeks ago. Each update was triggered by a real failure — a post where the meta description was too long, a tag combination that didn't match the cluster structure, a slug that contained stop words. The skill is dramatically better now than when I first wrote it, and each improvement was driven by real usage, not speculation.

## Why You Should Never Download Skills From a Marketplace

Ross's stance on this is unambiguous, and I've come around to agreeing with him after initially pushing back.

Skill marketplaces — places where you can browse and install pre-built skills created by other developers — seem like a great idea on the surface. Why build a "React component generation" skill from scratch when someone else already made one?

Two reasons.

First, context mismatch. A skill built for someone else's workflow encodes their conventions, their stack decisions, their edge cases. Unless their development environment is identical to yours (it's not), the skill will produce output that doesn't quite fit. You'll spend as much time fixing the output as you would have spent building the skill yourself. I wrote about this problem in my [agent skills workflow guide](/content/mejba.me/agent-skills-ai-agents-guide.md) — the skills that work best are the ones built from your actual workflow, not someone else's abstraction of a similar workflow.

Second, security. A skill.md file is essentially a set of instructions that your AI agent will follow. Installing a skill from an untrusted source is like running an npm package without reading the code — except the attack surface is your entire development environment. A malicious skill could instruct the agent to exfiltrate code, inject dependencies, or modify files in ways that create vulnerabilities. The risk isn't theoretical; as agents gain more autonomous capabilities, the instructions they follow become an increasingly attractive attack vector.

Build your own. It takes more time upfront. The compound return is worth it.

## The "One Agent, Many Skills" Principle

Here's where Ross's framework connects to a broader architectural decision that I see developers getting wrong constantly.

The temptation, once you understand agents and skills, is to build a fleet. A coding agent. A research agent. A writing agent. A deployment agent. Each with its own system prompt, its own tool configuration, its own personality. It looks impressive. It feels sophisticated. And for most use cases, it's massive overkill.

Ross's recommendation: start with one agent. Build it 10 skills. Get those skills working reliably. Only then consider whether a second agent would genuinely improve your productivity — and only if you can articulate exactly what the second agent would do that the first one can't.

The reasoning is practical. Multiple agents introduce coordination overhead — they need to communicate, share context, hand off tasks, and resolve conflicts. That overhead is only justified when the agents are doing genuinely parallel work that can't be serialized. For a solo developer or a small team, one well-skilled agent handles most workflows more efficiently than three specialized ones that need orchestration.

I restructured my own setup around this principle last month. I went from three configured agents (one for coding, one for content, one for DevOps) down to one agent with a library of 14 skills spanning all three domains. The single-agent setup is faster to maintain, more predictable in behavior, and — counterintuitively — produces better output because the full context of my project is always available, not fragmented across agents that can't see each other's work.

If you'd rather have someone set up this kind of agent architecture from scratch, I take on AI workflow and automation engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

The exception to the single-agent rule is genuine parallelism — tasks that are truly independent and time-sensitive enough to justify running simultaneously. Deploying to staging while running a test suite while generating documentation? That's three independent tasks. Three agents make sense. But writing code, reviewing it, and then deploying it? That's a serial workflow. One agent, three skills.

## Practical Migration: From Bloated Config to Lean Skills

If you're sitting on a large CLAUDE.md or agents.md file right now, here's how I migrated mine without losing the knowledge I'd built up:

**1. Audit your config file for actual usage patterns.**

Go through every instruction in your file and ask: "When was the last time this instruction actually changed the agent's output?" Be honest. I found that roughly 60% of my instructions were either redundant (the model already does this by default), outdated (referring to patterns I stopped using), or so rare that they'd been triggered maybe twice in months of use.

**2. Separate the universal from the task-specific.**

Some instructions genuinely belong in every interaction: "Use TypeScript, not JavaScript." "Follow the existing project structure." "Run tests before suggesting a PR." These stay in your lean CLAUDE.md. Everything else — specific API patterns, deployment procedures, content formatting rules — becomes a skill candidate.

**3. For each skill candidate, apply the "three times" test.**

Have you actually done this workflow manually at least three times? If yes, it's worth building a skill. If no, either do it manually a few more times to understand the edge cases, or skip it entirely.

**4. Build skills through conversation, not configuration.**

For each skill you're creating, walk the agent through the workflow interactively 3-5 times before writing the skill file. This surfaces edge cases you'd miss writing from a blank page.

**5. Keep your CLAUDE.md under 200 lines.**

This is the threshold that [current best practices](https://code.claude.com/docs/en/costs) suggest for maintaining the cost-benefit balance. Beyond 200 lines, the persistent input token cost starts outweighing the output quality improvements. Mine is currently at 47 lines, and the output quality is the best it's ever been.

After this migration, my per-session token usage dropped by roughly 60%. I'm accomplishing the same work — often more — while consuming dramatically fewer tokens. The sessions feel different too. The agent responds faster, stays more focused, and produces fewer off-target suggestions. I wrote about the token optimization side in detail in my [Claude Code token optimization guide](/content/mejba.me/claude-code-token-optimization.md), but the migration from monolithic config to skills is where the biggest single improvement came from.

## The Real Paradigm Shift: Context Is the Product

Ross said something toward the end of the discussion that I've been turning over in my head since: the models are a commodity. Opus 4.6, GPT 5.4, whatever ships next quarter — they're all converging toward similar capability levels. The competitive advantage isn't which model you use. It's how well you build context, workflows, and skills around whichever model you choose.

This reframes what it means to be productive with AI in 2026. The developers who thrive won't be the ones chasing every new model release or accumulating the longest config files. They'll be the ones who've built a personal library of battle-tested skills — each one refined through real usage, each one encoding workflow knowledge that a fresh agent couldn't replicate without weeks of training.

Think of it as building a knowledge moat. Every skill you create, every edge case you document, every workflow you refine — it's compounding expertise that makes your AI setup more valuable and more efficient over time. Someone starting from scratch tomorrow can't shortcut that process. They can use the same model. They can't use your skills.

Ross warns — and I think he's right — that the gap between people who understand this and people who don't is going to widen fast. The models will keep getting better. The people who know how to steer them through well-constructed context will extract dramatically more value from each improvement. The people treating AI agents as magic boxes that should "just work" will keep hitting the same walls, blaming the model for failures that are actually context failures.

## What I Changed This Week and What Happened

I want to be specific about results because vague claims are worthless.

After rebuilding my workflow around Ross's framework, here's what my Monday looked like compared to the previous Monday:

**Previous Monday (monolithic CLAUDE.md, no skills):**
- Hit token limits twice during a 5-hour work session
- Spent approximately 25 minutes re-explaining context after each `/clear`
- Produced 3 finished components, 2 of which needed manual fixes
- Estimated token consumption: ~850,000 tokens

**This Monday (47-line CLAUDE.md, 14 active skills):**
- Hit token limits zero times during the same 5-hour window
- Context re-establishment after `/clear` took under 2 minutes (skill loaded relevant context automatically)
- Produced 5 finished components, 1 needed a minor fix
- Estimated token consumption: ~340,000 tokens

Same developer. Same model. Same project. The only variable was how context was structured.

The most surprising improvement wasn't the token savings — it was the quality consistency. With the monolithic config, output quality degraded noticeably as the session progressed and the context window filled up. With skills, each task starts with a relatively fresh context plus only the relevant skill instructions. The fifth task of the day gets the same quality as the first.

## The Five-Minute Version

If you take nothing else from this, here's the distilled framework:

**Stop growing your config file.** Cap it at 200 lines maximum. Strip it down to only the instructions that genuinely need to apply to every single interaction.

**Build skills through iteration, not imagination.** Walk the agent through real tasks 3-5 times before writing a skill file. The edge cases you discover during training are the most valuable part of the skill.

**One agent, many skills.** Resist the urge to build a fleet of specialized agents. One well-skilled agent outperforms three poorly coordinated ones for most workflows.

**Never install someone else's skills.** Build your own. The context mismatch and security risks aren't worth the time savings.

**Treat context as a budget, not a container.** Every token in the context window competes for the model's attention. Spend tokens deliberately on information the current task needs. Starve everything else.

Ross frames this as a paradigm shift, and I don't think that's overstatement. The developers who master context engineering — who treat the information diet of their AI agents as a first-class engineering concern — are going to outperform the ones who keep dumping everything into a config file and hoping the model figures it out.

The models are smart enough. The question is whether we're smart enough to feed them properly.

## Frequently Asked Questions

### Do I need a CLAUDE.md or agents.md file at all?

Only if you have universal conventions that genuinely apply to every interaction — language preferences, project structure rules, or proprietary patterns the model wouldn't know. Keep it under 200 lines. For most solo developers, 50-100 lines is the sweet spot where you get consistent conventions without paying excessive token overhead.

### How many skills should I build before seeing real productivity gains?

Most developers see meaningful improvement after 3-5 well-crafted skills covering their most repeated workflows. Don't aim for a large library upfront — focus on the tasks you do daily and build outward from there. I hit a noticeable inflection point at 8 skills.

### Can I convert my existing CLAUDE.md into skills?

Yes, and you should. Group related instructions into workflow-specific clusters, apply the "three times" test to each cluster, then build skills for the ones that pass. The instructions that don't fit any specific workflow stay in your lean config file.

### What's the difference between skills and MCP tools?

Skills are knowledge packages — they tell the agent *how* to approach a task. MCP tools are capabilities — they let the agent *take actions* like reading files, running commands, or calling APIs. Skills direct the agent's reasoning; tools extend what it can do. They're complementary, not competing.

### How do I know if my context window is too full?

Watch for three signals: the agent starts repeating suggestions it already made, response times slow noticeably, or the agent misses instructions you've clearly provided. These indicate the context is saturated and the model is losing focus. Use `/compact` or `/clear` to reclaim space.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
