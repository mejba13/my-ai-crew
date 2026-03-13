**BRAND:** mejba.me
**TITLE:** Pencil AI Turns 6 Agents Into Your Design Team
**SLUG:** pencil-ai-design-tool-claude-code
**PRIMARY KEYWORD:** Pencil AI design tool
**SECONDARY KEYWORDS:** AI agent swarm design, Pencil Claude Code integration, AI UI design workflow
**META DESCRIPTION:** I tested Pencil AI's 6-agent swarm mode to generate polished UI designs and export them as React code via Claude Code. Here's how this workflow actually performs.
**TAGS:** AI Development, UI Design, Pencil AI, Claude Code, Deep Dive

---

I watched six AI agents design a mobile app dashboard simultaneously. Not sequentially. Not taking turns. Six cursors moving across the same canvas at the same time, each building a completely different visual direction for the same product brief.

One agent was laying out a neon-soaked analytics view with electric gradients. Another was constructing a minimal editorial layout with generous white space and restrained typography. A third went full luxury -- gold accents on dark backgrounds, the kind of aesthetic you'd expect from a premium fintech app. And while all six were working, I was dragging elements around on the canvas, editing colors, regrouping components -- and nothing broke.

That was Tuesday night. I'd been testing Pencil for about three hours at that point, and this was the moment where I stopped evaluating the tool and started rethinking how I approach UI design entirely.

If you've spent any time building interfaces with AI coding tools -- Claude Code, Cursor, anything in that space -- you know the dirty secret nobody talks about in demo videos. The code works. The design doesn't. You get functional components wrapped in the visual equivalent of a college assignment. Default spacing. Predictable color choices. Typography that says "I let the AI handle it" from across the room.

Pencil fixes this problem in a way I genuinely didn't expect, and the Claude Code integration creates a pipeline from visual design to production code that's tighter than anything I've used. But there's nuance here that matters. This isn't a magic wand, and the workflow requires understanding where the tool shines and where you still need to bring your own judgment.

Here's what I found after a week of building with it.

## What Pencil Actually Is (And Why It's Not Just Another AI Design App)

Pencil lives at pencil.dev, and the first thing worth knowing is that it's backed by A16Z Speedrun -- which signals serious investment in the underlying technology, not just a weekend hackathon project. That backing matters because the core feature set is genuinely ambitious.

At surface level, Pencil is a desktop app with an infinite canvas. You design UI screens, components, and full layouts. It has frames, a properties panel, asset management on the left sidebar, styling controls on the right. If you've used Figma for more than five minutes, you'll feel oriented immediately. The spatial model is familiar.

But the engine underneath is different in a fundamental way. Everything you create in Pencil is built in real code. Not "code-like representations" or "exportable layers." Actual code. When you place a button on the canvas, there's a real component backing it. When you adjust spacing, you're adjusting real values that map directly to CSS properties. This distinction sounds academic until you try to export a design from a traditional tool and watch the generated code bear almost no resemblance to what you designed.

The second differentiator -- and the one that made me sit up -- is the agent swarm mode. You can run up to six AI agents concurrently on a single canvas. Each agent operates independently, generating its own design direction based on your brief. You can assign different AI models to different agents. You can attach style kits, reference images, and design system constraints to each conversation independently. And you can do all of this while manually editing the canvas yourself.

That last part deserves emphasis. In every other AI design tool I've tested, you're either watching the AI work or doing your own work. Pencil dissolves that boundary. The AI agents have visible cursors on the canvas -- you can literally watch them placing elements, adjusting layouts, building components in real time. And while they're doing that, you can select a different frame, change a background color, resize a component, and none of it interferes with the agents' work.

<!-- IMAGE: [Screenshot of Pencil canvas with multiple AI agent cursors working simultaneously on different frames]. Alt text: "Pencil AI design tool showing six concurrent AI agent cursors building UI designs on an infinite canvas". Caption: "Six agents, one canvas, zero interference. Each cursor represents an independent AI agent." -->

This isn't a gimmick. It's a workflow architecture decision that changes the tempo of design exploration in a way I'll break down in the implementation section. But first, you need to understand why this multi-agent approach solves a problem that single-agent tools fundamentally can't.

## Why Single-Agent Design Feels Like Settling for the First Idea

Here's something I learned the hard way across dozens of projects: the biggest risk in AI-assisted design isn't bad output. It's premature convergence.

When you use a single AI agent to generate a design, you get one direction. Maybe it's good. Maybe it's great. But it's one perspective, one interpretation of your brief, one set of aesthetic choices. And because it took five minutes instead of five hours, you're tempted to run with it. Why iterate when the first result looks polished enough?

That temptation is a trap.

Every designer I respect -- the ones whose work makes you stop scrolling -- will tell you the same thing. The first idea is almost never the best idea. It's the most obvious idea. The one that sits closest to the surface of the AI's training data, the averaged-out version of every similar design that exists on the internet. Professional design work comes from exploring multiple directions, comparing them against each other, and making deliberate choices about which elements work best for the specific product, audience, and context.

The problem is that exploration takes time. In Figma, creating six distinct design directions for a single screen means six rounds of manual work. Realistically, most solo developers and small teams skip this step entirely. They take the first decent output and move on.

Pencil's six-agent swarm mode eliminates the time cost of exploration without eliminating the exploration itself.

When I set up my first multi-agent session, I gave all six agents the same brief: "Design a creator analytics dashboard for a mobile app." Same product description. Same target audience. Same core features list. I attached a warm editorial style kit and told Pencil to use Opus 4.6 for all six agents -- Chris from the demo I studied recommended Opus specifically for creative tasks, and I found that recommendation held up.

What came back was six genuinely distinct design directions. Not minor color variations of the same layout. Six different philosophies about how to present the same data.

The neon dashboard used vivid, saturated colors and dense data visualization -- the kind of design that appeals to power users who want everything visible at once. The minimal editorial version stripped away decoration and used typography hierarchy alone to guide the eye, with generous negative space that made each metric feel considered rather than crammed. The bold bow house direction went heavy and blocky, with thick dividers and strong visual weight. The warm humanist version felt approachable and friendly, using rounded shapes and soft warm tones. The luxury gold direction was all premium aesthetics -- dark backgrounds, gold accents, refined spacing. And the terminal dev version went full hacker aesthetic, monospace fonts and minimal chrome, the kind of interface a developer would actually enjoy using.

Six minutes of generation time. Six distinct directions that would have taken a human designer most of a day to produce manually.

That's not a marginal improvement. That's a structural change in how design exploration works. And the best part? I could edit elements on any frame while the agents were still working on others.

## How to Get Started With Pencil (The Setup Nobody Explains Clearly)

Getting Pencil running is straightforward, but there are a few things I wish someone had told me before I started.

**Step 1: Download the desktop app from pencil.dev.** Pencil runs as a native desktop application, not a browser tool. This matters for performance -- rendering six concurrent AI agents on a canvas would choke a browser tab. The desktop app handles it smoothly.

**Step 2: Work through the walkthrough file.** When you first open Pencil, there's a built-in walkthrough file that teaches you the basic commands and workflow patterns. Don't skip this. I almost did, and I would have missed key interaction patterns like how to convert frames to dark mode or how to trigger AI-generated content for specific rows within a design.

The walkthrough introduces you to the named AI agents -- they have names like Cosmo and Orchid -- and shows you how the agent cursor system works. Each agent's cursor is visible and trackable on the canvas, which isn't just a visual flourish. It builds genuine trust in the AI's process because you can see exactly what it's doing, where it's working, and when it finishes.

**Step 3: Set up your design system before generating screens.** This is the step most people rush past, and it's the one that matters most for production-quality output. Pencil supports full design systems -- consistent color tokens, typography scales, spacing values, and reusable UI components. When you ask agents to generate screens using a design system, the output is cohesive across all six directions because they share the same visual language.

Without a design system, each agent generates its own visual vocabulary. The results are still interesting for exploration, but they don't share the consistency you need for a real product.

**Step 4: Learn the chat tab system.** Each AI conversation in Pencil gets its own chat tab, similar to having multiple Claude conversations open. You can run different tasks in different tabs, assign different models, and attach different reference materials. I typically keep one tab for exploration (multi-agent swarm), one for refinement (single agent iterating on a chosen direction), and one for design system work.

**Step 5: Connect Claude Code via MCP.** This is where the magic happens for developers, and I'll cover it in detail in the next section. The short version: Pencil exposes its designs through MCP (Model Context Protocol), which means Claude Code can read your Pencil frames directly and generate code from them.

Here's a pro tip that saved me hours: before you start your first real project, spend thirty minutes creating a baseline design system with your brand colors, fonts, and core components. Attach this as a style kit to every agent conversation. The quality difference between "agent with style kit" and "agent without style kit" is the difference between "this looks professional" and "this looks like an AI demo."

<!-- IMAGE: [Screenshot of Pencil's chat tab interface showing multiple concurrent AI conversations with different style kits attached]. Alt text: "Pencil AI design tool chat tabs with style kit attachments for multi-agent UI design workflow". Caption: "Each tab runs an independent AI conversation. Attach different style kits for different design directions." -->

## The Claude Code Integration That Changes the Design-to-Code Pipeline

This is the section I was most skeptical about before testing, and the one that impressed me most after.

The standard design-to-code workflow has always been painful. You design in Figma, export assets, manually inspect spacing values, approximate typography in CSS, build the component, compare it side-by-side with the design, fix fifteen discrepancies, repeat. Even with good Figma-to-code tools, the translation introduces entropy. Spacing drifts. Font rendering differs. The border-radius that looked perfect in the design tool looks slightly wrong in the browser.

Pencil's approach sidesteps this problem entirely because the designs are already code. The canvas is a visual representation of real components with real values. When Claude Code reads a Pencil frame, it's not interpreting a visual -- it's reading a data structure that already maps to CSS properties.

Here's the actual workflow:

**Step 1:** Design your screen in Pencil (or let agents design it for you).

**Step 2:** Open your project in Claude Code. Run `/mcp` to access Pencil-specific tools. This requires the MCP connection to be configured -- same protocol you'd use for Figma MCP or any other tool integration.

**Step 3:** Tell Claude Code to generate code from a specific frame in Pencil. Something like: "Generate a static HTML page from the 'Dashboard Main' frame in Pencil."

**Step 4:** Claude Code reads the design structure -- colors, fonts, spacing, layout hierarchy, border radii, everything -- and generates the corresponding code.

I tested this with the landing page variant from my spa mindfulness site exploration. One of the six agents had produced a warm editorial layout that I liked, so I told Claude Code to generate it as an HTML file. The result opened in my browser and looked... identical. Same colors. Same spacing. Same font weights. Same border-radius values. Not "close enough." Matching.

```html
<!-- Example of what Claude Code generated from a Pencil frame -->
<!-- The fidelity is remarkable - colors, spacing, and typography -->
<!-- match the Pencil design exactly -->
<section class="hero" style="
  background: #FAF7F2;
  padding: 96px 64px;
  font-family: 'Playfair Display', serif;
">
  <h1 style="
    font-size: 56px;
    line-height: 1.15;
    color: #2C2420;
    letter-spacing: -0.02em;
    max-width: 680px;
  ">Find stillness in the chaos</h1>
  <p style="
    font-size: 18px;
    line-height: 1.7;
    color: #6B5E54;
    max-width: 520px;
    margin-top: 24px;
  ">A mindfulness retreat designed for people who think
     they don't have time for one.</p>
</section>
```

There's an important caveat: the generated output isn't fully responsive by default. It produces static layouts that match the frame dimensions in Pencil. For production use, you'll want to either generate for multiple breakpoints or use the code as a foundation and add responsive behavior in your framework -- Next.js, Vite, whatever you're building with.

But here's why that caveat matters less than it sounds. The generated code is a near-perfect starting template. The hardest part of building a UI from a design -- getting the visual fidelity right, matching exact colors and spacing, reproducing the typographic feel -- is done. Adding responsive breakpoints to a pixel-perfect static page takes a fraction of the time compared to building responsive from scratch while also trying to match a design.

If you'd rather have someone build this kind of design-to-code pipeline for your team, I take on integration projects like this regularly. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Building a Design System That Your Codebase Can Actually Use

Here's where Pencil's architecture pays the biggest dividends, and where I think it genuinely pulls ahead of the Figma workflow for developer teams.

When you build a design system in Pencil -- tokens, components, patterns, the whole stack -- you can export that entire system as code through Claude Code. Not as a PDF spec document. Not as a Figma file that developers have to manually interpret. As an actual interactive reference page built in React and Tailwind CSS.

I ran this workflow for the mobile dashboard project. After choosing the warm humanist direction from my six-agent exploration, I spun up six agents again -- but this time, instead of exploring visual directions, I tasked them with building out a complete design system based on that chosen direction.

The agents divided the work naturally. Some focused on design tokens -- color values, typography scale, spacing units. Others built UI components: status bars, navigation tabs, hero metrics cards, list items, toggle switches, form inputs. Each agent worked on different component categories simultaneously. The whole design system populated across the canvas in about eight minutes.

Then I pointed Claude Code at it.

The result was a standalone React page with every component rendered and documented. Color swatches with hex values and usage labels. Typography samples at every scale step. Spacing demonstrations. And every UI component rendered in its default state, hover state, active state, and disabled state.

This reference page becomes the single source of truth for your project. When Claude Code (or any AI coding tool) builds new features for your app, you can point it at this reference page and say "follow this design system." The AI reads real component code with real values, not a design spec that requires interpretation.

```jsx
// Example: Design system token export from Pencil via Claude Code
const tokens = {
  colors: {
    primary: '#E8A87C',
    primaryDark: '#D4956A',
    surface: '#FDF6F0',
    surfaceElevated: '#FFFFFF',
    text: '#2C2420',
    textSecondary: '#6B5E54',
    border: '#E8E0D8',
    success: '#7CB87A',
    warning: '#E8C97C',
    error: '#D4726A',
  },
  typography: {
    heading1: { size: '28px', weight: 700, lineHeight: 1.2 },
    heading2: { size: '22px', weight: 600, lineHeight: 1.3 },
    body: { size: '16px', weight: 400, lineHeight: 1.6 },
    caption: { size: '13px', weight: 500, lineHeight: 1.4 },
  },
  spacing: {
    xs: '4px', sm: '8px', md: '16px',
    lg: '24px', xl: '32px', xxl: '48px',
  },
  borderRadius: {
    sm: '8px', md: '12px', lg: '16px', full: '9999px',
  },
};
```

One critical best practice I picked up: make sure your design system export matches your target framework. If you're building in Next.js with Tailwind, tell Claude Code to generate Tailwind utility classes and component code in JSX. If you're building in plain HTML/CSS, ask for vanilla CSS custom properties. The mismatch between design system format and development framework creates friction that defeats the purpose of the integration.

When the system is set up correctly, the loop looks like this: design in Pencil, export system via Claude Code, build features referencing the system, iterate on designs in Pencil, re-export. The design and code stay synchronized because they share the same underlying data.

## What Nobody Tells You About Multi-Agent Design (The Honest Take)

I've been painting a rosy picture, and most of what I've said is genuinely how the tool performs. But I'd be doing you a disservice if I didn't share the friction points I hit.

**The output quality varies between agents.** When you run six agents, you typically get two that are genuinely excellent, two that are solid but not inspired, one that's decent, and one that misses the mark. That's actually fine -- the point is exploration, and having two excellent options is better than having zero. But don't expect six home runs every time. The agents are drawing from the same underlying model, and the variation comes from different random seeds, not different expertise levels.

**Style kit quality determines output quality.** Agents without style kits produce generic work. Agents with detailed, well-structured style kits produce professional work. The tool amplifies your design direction -- it doesn't replace having one. I spent my first afternoon frustrated because my outputs looked like Bootstrap templates. The problem wasn't Pencil. It was my lazy, one-line style instructions.

**The responsive gap is real.** I mentioned this earlier, but it's worth repeating because it's the most common complaint I anticipate. Pencil generates fixed-dimension designs. The Claude Code export produces fixed-dimension code. You need to add responsive behavior yourself. For prototype work and MVP landing pages, this doesn't matter. For production applications, plan to spend time on responsive adaptation after the initial code generation.

**Canvas performance with six agents has occasional hitches.** On my M2 MacBook Pro, running six concurrent agents while manually editing the canvas produced occasional frame drops and brief interface lag. Nothing crashed, nothing was lost, but the experience wasn't silky smooth during peak activity. This is likely to improve with updates -- the tool is still relatively early.

**The Figma comparison is nuanced.** Pencil positions itself as a potential Figma replacement, and for AI-native workflows it genuinely might be. But Figma's collaborative features, plugin ecosystem, developer handoff tools, and prototype linking are mature capabilities that Pencil doesn't match yet. If your workflow is deeply embedded in Figma with team collaboration, Pencil is better positioned as a complementary tool than a replacement -- at least right now.

I want to be honest about my own learning curve too. It took me about three sessions before I stopped thinking in Figma patterns and started thinking in Pencil patterns. The mental model shift from "I design, then AI helps" to "agents design, I direct and curate" takes adjustment. It's a different skill. You become less of a pixel-pusher and more of a creative director managing a team of AI agents. Some people will love that shift. Others will find it uncomfortable.

That creative director analogy isn't mine -- Chris from the demo used it, and it's exactly right. The most effective way to use Pencil isn't to treat the agents as assistants who execute your precise vision. It's to treat them as a creative team that generates options while you make the editorial decisions about which direction serves the product best.

## The Workflow That Produces Professional Results (My Actual Process)

After a week of testing, here's the workflow I've settled on. It's five phases, and each one builds on the previous.

**Phase 1: Reference Collection (10 minutes).** Before opening Pencil, I gather 3-5 reference images or designs that capture the aesthetic direction I want. Pencil supports importing images as design references, and I've found that visual references produce dramatically better agent output than text descriptions alone. A screenshot of a design I admire communicates more about spacing rhythm and color relationships than three paragraphs of description.

**Phase 2: Style Kit Creation (15 minutes).** Build a style kit with your brand colors, preferred typography, spacing preferences, and any constraints ("no gradients," "rounded corners only," "dark mode primary"). Attach this to your agent conversations. This step is the single biggest lever for output quality.

**Phase 3: Multi-Agent Exploration (10 minutes).** Run six agents with the same brief and style kit. Let them work. Watch the cursors. Resist the urge to edit while they're still generating your exploration frames -- save your edits for frames you've already decided to keep or discard. When they finish, compare all six directions side by side.

**Phase 4: Selection and Refinement (20 minutes).** Pick the direction you like best. Open a new single-agent chat tab and start refining: "Adjust the spacing between the metrics cards," "Make the navigation tabs more compact," "Swap the heading font to something with more personality." This is where your design taste matters most. The agent generated the foundation. You're sculpting it into something that serves your specific product.

**Phase 5: Design System and Export (15 minutes).** Generate a full design system from your refined direction using multiple agents. Export via Claude Code into your project's framework. Set up the reference page. Start building features.

Total time from blank canvas to production-ready design system with exported code: about 70 minutes. I timed it across three projects. The fastest was 55 minutes for a simpler dashboard. The longest was 90 minutes for a multi-screen app with complex data visualization components.

Compare that to my previous workflow: 4-6 hours in Figma for a single direction, then 2-3 hours translating to code manually. The time savings aren't incremental. They're transformative.

## What This Means for the Design-Development Gap

There's a bigger shift happening here that goes beyond any single tool.

For most of the history of digital product design, we've had two separate worlds: the world of design (Figma, Sketch, Adobe) and the world of code (VS Code, terminal, frameworks). A massive amount of creative and engineering energy gets wasted translating between them. Designers spec something. Developers interpret it. Discrepancies appear. Meetings happen. Corrections are made. More meetings. The translation tax is real, and it accumulates across every feature, every sprint, every product.

Pencil's approach -- design in code, on a visual canvas, with AI agents that understand both worlds -- compresses that gap. Not eliminates it. Compresses it. The designs are code from the start. The export is code. The design system is code. The AI agents generating the designs are producing code-backed visuals that map directly to what a developer would build.

I've been watching this space closely, and what excites me about Pencil isn't that it's perfect right now. It's that it points toward a future where the design-development handoff problem simply doesn't exist. Where visual design and code generation are the same activity, not two activities bridged by documentation and good intentions.

We're not there yet. The responsive limitations, the framework-specific tweaking, the need for developer judgment on interaction patterns -- these are real gaps. But the direction is clear, and the foundation Pencil has built is solid enough to be genuinely useful today, not just promising for tomorrow.

## Is Pencil Worth Adding to Your Workflow Right Now?

Here's my honest assessment after a week of real use.

If you're a solo developer or small team building products where UI quality matters and you don't have a dedicated designer, Pencil is worth your time immediately. The multi-agent exploration alone -- getting six professional-quality design directions in under ten minutes -- solves a problem that no other tool in this category handles this well.

If you're already deep in a Figma-based team workflow with design systems, shared libraries, and prototype testing, Pencil is worth watching and experimenting with on side projects. The Claude Code integration is compelling, but the collaborative features aren't yet mature enough to replace a full Figma team workflow.

If you're a designer who's been feeling the pressure of AI tools and wondering where design as a profession is heading -- pay attention to Pencil. Not because it replaces your skills. Because it redefines how you apply them. The designers who thrive with these tools won't be the ones who can push pixels fastest. They'll be the ones who can direct AI agents most effectively, who have the taste and judgment to curate machine-generated options into cohesive, intentional products.

The cursor is moving on its own now. The question is whether you're directing it.

## Frequently Asked Questions

### What is Pencil AI and how is it different from Figma?

Pencil is an AI-powered desktop design app at pencil.dev that creates designs in real code, not visual layers. Its standout feature is agent swarm mode -- up to six AI agents working simultaneously on one canvas. Unlike Figma, designs export directly as functional code through Claude Code's MCP integration. For the full comparison, see the honest take section above.

### Can Pencil AI generate production-ready code?

Pencil generates pixel-accurate static code through its Claude Code integration -- matching exact colors, spacing, fonts, and border radii from your designs. The output works as HTML, React, or Tailwind CSS components. Responsive behavior requires manual addition. For the export workflow, see the Claude Code integration section above.

### How many AI agents can work at once in Pencil?

Pencil supports up to six concurrent AI agents on a single canvas. Each agent produces an independent design direction from the same brief, and users can edit the canvas manually while agents work. For best results, use Opus 4.6 and attach a style kit to each conversation. See the multi-agent exploration phase in my workflow section.

### Does Pencil work with Claude Code?

Pencil integrates with Claude Code through MCP (Model Context Protocol). After connecting via the `/mcp` command in Claude Code, you can instruct Claude to read any Pencil frame and generate corresponding code in your target framework. The integration produces faithfully accurate visual reproductions. Setup details are in the implementation section above.

### Is Pencil AI free to use?

Pencil is backed by A16Z Speedrun and available at pencil.dev. Pricing and access details are available on their site -- check pencil.dev directly for the most current information on plans and availability as of March 2026.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
