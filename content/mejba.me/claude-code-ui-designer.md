**BRAND:** mejba.me
**TITLE:** How Claude Code Turned Me Into a UI Designer
**SLUG:** claude-code-ui-designer
**TAGS:** AI Development, Web Design, Claude Code, UI Design, Tutorial

---

I have a confession that would get me laughed out of any design meetup: I've been a software engineer for years and I still can't make a button look good from scratch. Give me a complex distributed system? I'll architect it in my sleep. Ask me to pick a color palette and lay out a hero section? I'll stare at a blank screen for an hour and produce something that looks like it crawled out of 2009.

That changed three weeks ago when I installed a single markdown file into my Claude Code setup.

The file is called a "front-end design skill," and it transforms Claude Code from a coding assistant into something I didn't expect — a genuinely capable UI designer that takes a short text prompt and produces hero sections, landing pages, and component layouts that look like a real designer built them. Not "pretty good for AI" good. Actually good. The kind of output where I showed it to a designer friend and she asked which Dribbble shot I was referencing.

I discovered this through a 30-day UI design challenge that's been running on designcourse.com, where over 600 participants are all using Claude Code to generate designs from the same business prompt. Same fictional company, same constraints, wildly different results — because the differentiator isn't the AI. It's how you talk to it.

I went in expecting to produce mediocre mockups and learn a few tricks. What actually happened was a fundamental shift in how I think about the boundary between engineering and design. And I'm going to walk you through exactly how to set this up, what the output actually looks like, and why this matters way more than another "AI can make pretty pictures" headline.

But here's the part that surprised me most — and the reason I'm writing this instead of just sharing a screenshot. The skill system behind this isn't magic. It's a pattern that applies to *any* domain you want Claude Code to handle better. Design just happens to be the one that made the concept click for me.

## Why Engineers Build Ugly Things (And Why It's Not Really Our Fault)

Before I show you the setup, I want to address something that's bothered me for years. Engineering culture treats design skill as optional — a nice-to-have that some people are born with and others aren't. You hear it everywhere: "I'm a backend person." "I don't have the design gene." "Just make it functional, we'll hire someone to make it pretty."

This mindset creates a real problem. Most side projects, internal tools, and MVPs die looking terrible — not because the engineering is bad, but because the person building them couldn't bridge the gap between "working" and "someone would actually want to use this." I've killed three side projects myself, not because the code was broken, but because I couldn't get the landing page to look credible enough for anyone to take seriously.

The design skill gap isn't about talent. It's about vocabulary. Designers think in terms of visual hierarchy, whitespace ratios, color contrast, and typographic rhythm. Engineers think in terms of data flow, component architecture, and state management. We're solving different problems with different mental models, and the vocabulary gap means that even when I *know* what good design looks like (I can spot it immediately), I can't *produce* it because I don't have the right intermediate language.

That's exactly what the front-end design skill gives Claude Code. It's a translation layer — a set of design principles, component patterns, and visual decision-making frameworks written as instructions that Claude Code ingests into its context. When you ask it to build a hero section, it doesn't just write HTML and CSS. It makes design decisions: where to place visual weight, how to create contrast, what animation adds polish without being distracting, how to guide the eye from headline to CTA.

I'm getting ahead of myself. Let me show you the actual setup, because it takes about two minutes and the payoff is immediate.

## Setting Up the Front-End Design Skill (2 Minutes, No Design Experience Required)

The skill system in Claude Code is one of its most underappreciated features. Skills are markdown files that inject domain-specific knowledge and instructions into Claude Code's context. Think of them as giving your AI assistant a crash course in a specific discipline before it starts working.

Here's the exact setup process:

**Step 1: Find your Claude Code configuration directory.**

The path depends on your operating system:

```
macOS: ~/.claude/
Linux: ~/.claude/
Windows: %USERPROFILE%\.claude\
```

**Step 2: Create the skills folder structure.**

```bash
mkdir -p ~/.claude/skills/designer
```

**Step 3: Download and place the skill file.**

The front-end design skill is a markdown file (typically called `skill.md`) that contains detailed instructions about design principles, component patterns, color theory, typography, layout strategies, and animation guidelines. Place it in the folder you just created:

```
~/.claude/skills/designer/skill.md
```

**Step 4: Restart Claude Code.**

Close and reopen your terminal. Claude Code will automatically detect the new skill on startup.

**Step 5: Use the skill.**

When you want design-quality output, prefix your prompt with `/frontend design`. This tells Claude Code to activate the design skill context before processing your request.

That's it. Two minutes. No npm packages, no dependencies, no configuration files. Just a markdown file in the right folder.

**Pro tip:** You can peek inside the skill file to understand what it's actually teaching Claude Code. It's readable English — design principles, component patterns, CSS best practices, animation guidelines. Reading it taught me more about design fundamentals than most "design for developers" blog posts I've encountered. The skill file itself is educational, even before the AI uses it.

Now let me show you what happens when you actually use this thing.

## My First Prompt — And Why the Output Made Me Rethink Everything

The 30-day challenge assigned a specific business concept for the first challenge: an **AI home inspection service** that uses computer vision to analyze images of crawl spaces, roofs, and foundations, detecting defects and automatically generating standardized inspection reports.

Everyone gets the same business idea. Same constraints. The test is how well you can communicate your design vision through a prompt.

My first attempt was deliberately minimal. I wanted to see what the skill could do with almost no guidance:

```
/frontend design Create a hero section for an AI home inspection
business that uses computer vision to detect structural defects
in crawl spaces, roofs, and foundations, then generates automated
inspection reports.
```

Thirty seconds later, I had a complete HTML file with embedded CSS. I opened it in a browser and — I'm not exaggerating — my first reaction was "wait, that's actually good."

A two-column layout. Clean sans-serif typography with proper visual hierarchy. A bold headline that communicated the value proposition without being wordy. Body copy that explained the service concisely. A prominent CTA button with good contrast. A subtle gradient background that felt modern without being trendy. On the right side, a placeholder UI element suggesting the inspection interface, with a gentle CSS animation that drew the eye without being distracting.

Was it perfect? No. The color palette was a bit generic — safe blues and whites that screamed "tech startup template." The animation was clever but not quite right for a home inspection brand. The copy was functional but lacked personality.

Here's what matters: it was a **credible starting point.** Not a wireframe. Not a sketch. A fully rendered, responsive hero section that I could show to someone and have a productive conversation about design direction. As a developer, I've never been able to produce that starting point on my own. I'd either spend hours getting something mediocre, or I'd skip design entirely and jump straight to functionality.

The second prompt is where things got interesting.

## Iteration Is the Real Skill — How I Went From Good to Genuinely Impressive

The power of this workflow isn't in the first output. It's in the iteration speed. Here's the follow-up prompt I sent:

```
Adjust the design: use a warm, earthy color palette (think terracotta,
sage green, warm grays) to feel more grounded and trustworthy for
homeowners. Replace the animation with a subtle parallax effect on the
hero image. Make the headline bolder and more direct — something that
addresses the homeowner's anxiety about hidden structural problems. Add
a secondary CTA for a free demo.
```

Twelve seconds later, a completely redesigned hero section. Same structure, but the vibe was entirely different. The earthy tones made it feel trustworthy — like a company you'd actually let into your house. The headline now read something about finding problems before they find your wallet. The parallax effect added depth without being gimmicky. Two CTAs: "Get Your Free Inspection" (primary) and "See How It Works" (secondary, outlined style).

This version was dramatically better. Not because the AI was dramatically smarter, but because *I* was able to articulate what I wanted through specific design language. And that's the hidden curriculum of this whole exercise — using AI design tools teaches you to think about design in concrete, communicable terms.

**Third prompt — the polish pass:**

```
The layout is great. Refine these details: add a trust bar below the
fold with logos for "As Featured In" placeholders. Increase whitespace
between the headline and body copy by about 20%. The secondary CTA
should have a hover animation that fills with the primary color. Add a
subtle noise texture to the background for warmth.
```

Six seconds. Each of those four requests was implemented precisely. The noise texture added a tactile quality that took the design from "clean" to "crafted." The trust bar created instant credibility. The hover animation on the secondary CTA was smooth and satisfying.

Three prompts. Maybe five minutes total, including the time I spent thinking about what to ask for. And the output was something I'd genuinely put on a live website.

Here's a breakdown of what I learned about effective design prompting through the challenge:

| Prompting Approach | Output Quality | Why |
|---|---|---|
| Vague ("make it look nice") | Generic, safe | AI defaults to common patterns |
| Color-specific only ("use blue and white") | Slightly better | One dimension of design isn't enough |
| Mood-based ("warm, trustworthy, grounded") | Much better | AI translates emotion into design decisions |
| Specific + mood + references ("earthy palette, parallax, bold headline addressing homeowner anxiety") | Excellent | Multiple design dimensions addressed simultaneously |
| Iterative refinement (3-4 prompts building on each other) | Best results | Each prompt refines from a strong base |

The sweet spot is prompt three or four. Your first prompt establishes structure. Your second prompt nails the mood and personality. Your third prompt handles the details that separate amateur from professional. Most people stop at prompt one and wonder why AI design "doesn't look that good."

## What 600 People Designing the Same Thing Taught Me About Prompts

The most fascinating aspect of this challenge isn't individual designs — it's the variation. Same AI, same skill file, same business concept, nearly 200 submitted designs for the first challenge alone. Every single one looks different.

Some went dark and dramatic — almost cinematic hero sections with high-contrast photography and bold typography. Others went minimal and clean — lots of whitespace, understated colors, letting the copy do the work. A few went full illustration mode — custom-feeling graphics and hand-drawn accents that made the AI inspection service feel approachable rather than cold.

The variation proves something important: **the front-end design skill doesn't impose a style.** It gives Claude Code design literacy — the ability to make informed visual decisions — but the direction comes entirely from the human. Your prompt is the creative director. The AI is the production designer executing your vision.

I browsed through dozens of submissions and noticed patterns in what separated the strongest designs from the weakest:

**Strong designs** described a *feeling*, not just features. "Make it feel like a trusted family contractor's website, but modern" produces fundamentally different output than "add a header with company name and CTA button."

**Strong designs** specified what they *didn't* want. "No stock photo vibes. No generic tech startup aesthetic. No gradients that look like every SaaS landing page." Constraints sharpen the AI's decisions.

**Strong designs** iterated. The best submissions openly shared that they used 4-7 prompts to reach their final design. Nobody produced their best work on the first try. That's not a limitation of AI — that's how design works, period.

**Weak designs** treated the AI like a vending machine. One prompt, accept the output, done. The results were competent but unremarkable — technically sound but missing the human touch that makes design feel intentional.

This maps perfectly to how I've seen engineers interact with AI coding tools, by the way. The ones who get mediocre results are the ones who send a single prompt and accept whatever comes back. The ones who get exceptional results treat AI as a collaborator — they prompt, review, redirect, refine. The tool amplifies your taste and judgment. It doesn't replace them.

If you've been following my posts about building a second brain with Claude Code, this should sound familiar. The same principle applies — quality output requires quality context and iterative refinement, not a single perfect prompt.

## Beyond Hero Sections — Why the Skill Pattern Matters More Than the Design

Here's where I want to zoom out, because the design challenge is cool but the underlying pattern is what actually excites me as an engineer.

The front-end design skill is a *markdown file with instructions*. That's all it is. A set of principles, patterns, and guidelines that Claude Code reads and applies. The same mechanism works for literally any domain.

Want Claude Code to write better database migrations? Create a skill file with your team's migration conventions, naming patterns, rollback strategies, and common pitfalls. Want it to generate better test cases? Write a skill that describes your testing philosophy, coverage requirements, and assertion patterns.

I've started creating skills for everything I do repeatedly:

- **API design skill**: REST conventions, pagination patterns, error response formats, versioning strategy
- **Code review skill**: What I look for during reviews, common anti-patterns in our codebase, security checklist items
- **Documentation skill**: Our team's doc structure, tone guidelines, example templates

Each one is a markdown file. Each one takes maybe 30 minutes to write initially. And each one makes Claude Code dramatically better at that specific type of work.

The design skill just happens to be the most visually striking example. When Claude Code produces a beautiful hero section, the improvement is immediately obvious. When it produces a better-structured database migration, the improvement is just as real but less photogenic.

**Pro tip:** Skills compose beautifully. I've started combining my front-end design skill with a project-specific context file. When I say "build a hero section for our product," Claude Code now draws on both general design principles (from the skill) and specific brand knowledge (from the context file) — our color palette, our typography choices, our design language. The output feels like it came from our design team, not a generic AI.

This composability is where the real power lives. Skills + persistent context + iterative prompting = output quality that's genuinely hard to distinguish from human-produced work.

But I need to be honest about the limitations, because there are real ones.

## What This Can't Do (Yet) — Honest Limitations of AI-Driven Design

I'd be doing you a disservice if I presented this as "AI replaces designers." It doesn't. Not close. Here's what I've found it genuinely struggles with:

**Complex multi-page design systems.** The skill works beautifully for individual components and pages. Building a consistent, cohesive design system across an entire application? That still requires human design thinking. The AI can produce components that are individually excellent but don't always feel like they belong together without careful prompting.

**Responsive edge cases.** The generated HTML/CSS is responsive in the basic sense — it handles desktop and mobile. But the tricky in-between breakpoints, the tablet landscape layouts, the "what happens when this headline is 3 words vs 12 words" edge cases — those still need manual attention.

**Brand-specific nuance.** The AI understands design principles. It doesn't inherently understand *your* brand's soul. It can generate something that *looks* professional, but making it feel authentically like a specific brand requires either very detailed prompting or (better) a project-specific context file that describes the brand personality.

**User experience flows.** A hero section is one screen. An onboarding flow, a checkout funnel, a multi-step form — these require understanding user psychology, not just visual design. The skill helps with visual execution, but the strategic thinking about *what* to design and *why* is still entirely human territory.

**Pixel-perfect refinement.** The output gets you 85-90% of the way there. That last 10-15% — the kerning adjustment, the 2px padding tweak, the micro-animation timing — that's still faster to do by hand in CSS than to describe in a prompt.

I want to be clear about my positioning here: **this tool makes engineers dangerous at design, not expert.** I can now produce a credible landing page in 15 minutes. I could not produce what a senior designer creates after a week of research, iteration, and user testing. Different tools for different levels of need.

For side projects, internal tools, MVPs, hackathons, and quick prototypes? This is more than enough. For production applications with real brand requirements and UX complexity? You still need a designer — but now you can have much more productive conversations with them because you understand the vocabulary.

## From Figma Export to Live Code — The Full Workflow

One detail from the challenge that deserves its own section: the **Claude Code to Figma pipeline.** Several participants mentioned using a plugin that exports Claude Code's generated HTML/CSS directly into Figma as editable components.

I tested this and the workflow is genuinely smooth:

1. Generate the design in Claude Code
2. Export to Figma using the plugin
3. Refine in Figma (adjust spacing, swap fonts, add real images)
4. Share with stakeholders or your design team for feedback
5. Iterate in either Claude Code (for structural changes) or Figma (for visual refinement)

This workflow bridges the gap between "engineer playing with AI" and "professional design review process." The Figma export means your AI-generated designs enter the same workflow your team already uses. No special tools, no awkward screenshot sharing, no "trust me, it looks good on my machine."

For teams where engineers and designers collaborate closely, this creates an interesting dynamic. Engineers can now produce design proposals — rough but credible — that designers can refine rather than build from scratch. The design conversation shifts from "can you design this from nothing?" to "here's my starting point, how would you improve it?"

I showed my Figma exports to the designer on my current project. Her reaction: "The structure is solid and the color choices are smart. Let me fix the typography scale and tighten the spacing." She spent 20 minutes refining what would have taken her 2 hours to build from scratch. We both won.

## What Changed in How I Build Things

Here's the honest impact after three weeks of using the design skill regularly:

**Side project completion rate:** Up dramatically. I have two projects that were 90% functional but 0% designed. Both now have landing pages that I'm not embarrassed to share. One has already gotten its first real users — something that wouldn't have happened with my previous "I'll design it later" approach, because "later" never came.

**Internal tool quality:** I built an admin dashboard for a client project last week. Normally, I'd use a generic admin template and call it done. Instead, I spent 20 minutes with Claude Code designing custom components that matched the client's brand. The client noticed. They specifically called out how "polished" the internal tools looked. That matters for retention.

**Design conversations:** I'm a better collaborator with designers now. Not because AI taught me design, but because the iterative prompting process forced me to articulate design preferences in precise terms. "Make it warmer" became "use a terracotta accent against warm grays with increased whitespace." That precision translates directly to better design briefs, better feedback, and less frustration on both sides.

**Speed to prototype:** From idea to shareable visual prototype dropped from "I'll do it this weekend" (which meant never) to about 30 minutes. That speed changes the calculus on whether to prototype an idea at all.

The biggest shift is psychological. I no longer see design as a blocker. It used to be the thing standing between my finished code and a shippable product. Now it's a 30-minute step in the process, not a weeks-long dependency on someone else's availability.

## Your Move — The 10-Minute Challenge

Here's what I want you to do today. Not this week. Today.

Install the front-end design skill. Two minutes, steps are above. Then open Claude Code and type this:

```
/frontend design Create a hero section for [your side project or
current work project]. Modern, clean, [one adjective that describes
the vibe you want]. Include a headline, supporting copy, and a
primary CTA.
```

Fill in the brackets. Send it. Open the generated HTML file in your browser.

I'm betting you'll do what I did — sit there for a few seconds thinking "wait, I could actually ship this." And then you'll send a second prompt. And a third. And before you know it, you'll have a landing page for that project you've been meaning to launch for six months.

The 30-day challenge is still running on designcourse.com if you want community feedback and the motivation of seeing other people's approaches. But honestly, the challenge is just the catalyst. The real value is having a design-capable AI in your terminal, available every time you need to make something look good instead of just making it work.

For years I told myself I wasn't a designer. Turns out, I just didn't have the right tools. Now I do — and the gap between "functional" and "beautiful" has never been smaller.

What are you going to build first?

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
