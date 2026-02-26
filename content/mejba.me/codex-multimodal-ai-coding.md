**BRAND:** mejba.me
**TITLE:** Codex Can See Its Own Code Now — That Changes Everything
**SLUG:** codex-multimodal-ai-coding
**TAGS:** AI Tools, Coding Assistants, OpenAI Codex, Front-End Development, Deep Dive

---

Last week I watched an AI coding assistant look at a whiteboard sketch — a rough, hand-drawn rectangle with some squiggly circles and arrows — and turn it into a working interactive 3D globe with clickable destination pins, smooth animations, and responsive mobile layouts. Then it opened a browser, took a screenshot of what it built, noticed the pin labels were overlapping on smaller screens, and fixed the CSS without anyone asking it to.

That last part is what stopped me cold. Not the code generation — I've seen impressive code generation for two years now. The part where the AI looked at its own output, identified a visual problem, and corrected it autonomously. That's not a coding assistant. That's a coding assistant with eyes.

OpenAI's Codex has had multimodal capabilities for a while, but the latest demonstrations show something qualitatively different from what I've tested before. The system now runs a continuous loop: generate code, render the result, screenshot the output, analyze the screenshot for issues, fix the issues, screenshot again. It mimics — and in some cases surpasses — the workflow a human front-end developer follows naturally: write code, check the browser, adjust, check again.

I've spent the past week pushing this workflow to its limits across three projects. The results forced me to rethink how I approach front-end development entirely. Not because Codex is perfect — it absolutely isn't, and I'll show you exactly where it falls apart. But because the self-checking loop solves the single biggest problem with AI-generated UI code: the gap between what the AI thinks it built and what actually renders on screen.

Let me walk you through what this looks like in practice, where the magic actually is, and why the impressive demos hide some real limitations you need to know about.

## The Problem Every AI Coding Tool Has (Had)

Here's a frustration I've lived with since the first time I used an AI to generate front-end code. You describe a layout. The AI writes HTML and CSS. You render it in the browser. And it looks... wrong. Not broken-wrong. Subtly wrong. Spacing is off. Elements overlap at certain viewport widths. A button that should be centered is twelve pixels left of where it should be. Colors that seemed right in the code look muddy against the actual background.

So you describe the problems. The AI adjusts. You render again. Still not quite right. Three or four rounds of this and you've spent more time describing visual issues in text than you would have spent just writing the CSS yourself.

The core issue: AI coding tools generate code blindly. They produce tokens that represent HTML and CSS, but they have no visual model of what those tokens will render as. It's like asking someone to paint a portrait while blindfolded — they know the theory of where eyes and noses go, but they can't see the result.

Every developer who's used Claude, Cursor, Copilot, or any AI coding assistant for front-end work has hit this wall. The code is syntactically correct. The structure is reasonable. But the rendered output doesn't match what you — or the AI — intended. And the only feedback mechanism is you, the human, describing visual problems in words and hoping the AI interprets those descriptions correctly.

Codex's multimodal self-checking loop breaks this cycle entirely. The AI generates code, renders it in a real browser environment, takes a screenshot, and uses its vision model to analyze the actual visual output. If something looks wrong — overlapping elements, broken layouts, misaligned components — it sees the problem the same way you would and fixes it before you ever have to describe it.

That's not an incremental improvement to code generation. That's closing the feedback loop that's been open since AI coding assistants first appeared. And the practical implications are bigger than most people realize.

## Watching Codex Build a 3D Globe From a Whiteboard Sketch

The demo that convinced me to take this seriously involved a travel app called Wonderlust. The team sketched ideas on a physical whiteboard — rough drawings, handwritten labels, arrows connecting concepts. Someone took a photo of the whiteboard and fed it directly to Codex as a prompt.

What happened next took about eight minutes.

Codex analyzed the sketch. It identified the intended UI elements: a 3D globe for discovering travel destinations, clickable pins on the globe for specific locations, a detail panel that slides in when you tap a pin, and keyboard navigation for rotating the globe. From a photo of a whiteboard drawing.

Then it started coding. Three.js for the globe rendering. CSS animations for smooth rotation. Event handlers for click interactions and keyboard input. A responsive layout that adapts the globe size and detail panel position for mobile screens.

But here's the part the demos usually skip over and the part that matters most: after generating the initial implementation, Codex opened a browser, rendered the app, and took a screenshot. The globe was there. The pins were placed. But the detail panel was partially hidden behind the globe on tablet-width viewports. Codex saw this in the screenshot, identified the z-index and positioning conflict, adjusted the CSS, and re-rendered. The second screenshot showed a clean layout.

It caught a responsive design bug that I — a developer who builds responsive layouts regularly — might have missed on the first pass. Not because I'm careless, but because testing every viewport width manually is tedious and we all cut corners on it. Codex doesn't cut corners because the visual check is part of its automated loop, not a manual step it can skip.

The Three.js implementation itself was solid. Not production-optimized — it loaded the full Three.js library rather than tree-shaking to only the needed modules — but functionally complete with proper lighting, smooth rotation interpolation, and correct pin positioning on the globe surface. The kind of code a mid-level front-end developer would produce on a good day, delivered in minutes from a whiteboard photo.

I want to be precise about what impressed me and what didn't. The code generation speed is impressive but not unique — Claude and GPT-4o can generate Three.js code quickly too. The multimodal input (whiteboard sketch to code) is impressive but has existed in various forms. What's genuinely new is the closed loop: generate, render, see, fix. That loop is what turns "impressive demo" into "actually useful workflow."

## The Travel Log Feature: Where Self-Checking Really Shines

The second part of the demo built a Travel Log screen — a dashboard showing travel statistics with interactive checklists, pie charts, and gamified progress tracking. Continents visited, photos taken, local dishes tried, that sort of thing.

This is exactly the kind of UI that AI coding tools usually mess up. Dashboards have complex layouts with multiple data visualization components, varied card sizes, mixed typography, and dense information hierarchy. Getting one card right is easy. Getting twelve cards to coexist harmoniously on a single screen, with proper spacing and visual rhythm across desktop and mobile? That's where AI-generated layouts typically start looking like a junk drawer.

Codex generated the initial dashboard layout, rendered it, and screenshotted. The first pass had two visible issues: a pie chart legend was truncating on mobile, and the spacing between stat cards was inconsistent (some had 16px gaps, others had 24px). Codex identified both issues from the screenshot, fixed the legend overflow with a flexible wrapping layout, standardized the card spacing, and re-rendered.

The second screenshot was clean. Consistent spacing, readable legends, proper hierarchy. Two visual issues caught and fixed in under a minute, without any human intervention.

I deliberately tested whether the self-checking was genuine or theatrical by giving Codex a more complex dashboard prompt for one of my own projects. I asked it to build a data visualization dashboard from a rough sketch I drew on my iPad — intentionally messy, with overlapping annotations and ambiguous element sizes.

The first generated layout had three issues I could spot: a bar chart was too wide for its container, a header was misaligned with the grid, and a scrollable section wasn't actually scrollable. Codex's self-check caught two of the three. It fixed the chart width and the header alignment. It missed the scroll issue — the screenshot showed the section at its default height, which didn't reveal the overflow problem.

Two out of three isn't perfect. But two out of three caught automatically is two fewer things I had to describe in text and hope the AI understood. That's a real time savings, and it compounds across every front-end task.

## From Napkin Sketches to Figma Mockups: The Input Flexibility

One of Codex's more practical capabilities is the range of visual inputs it accepts. I tested four levels of input fidelity to see how the output quality scaled.

**Whiteboard sketch** — rough, hand-drawn, ambiguous proportions. Codex interpreted the general layout and element types correctly but made its own decisions about spacing, sizing, and styling. The output was functional but required significant refinement to match any specific design vision. Good for rapid prototyping when you don't care about pixel precision.

**iPad drawing with annotations** — cleaner than whiteboard, with color-coded sections and text labels specifying things like "this section scrolls" or "cards in a 3-column grid." Codex followed the annotations accurately about 80% of the time. The color coding helped it distinguish between different UI sections. Noticeably better output than the raw whiteboard sketch.

**Screenshot of a competitor's UI** — I fed Codex a screenshot of a dashboard from a SaaS product and asked it to "build something with this layout but our brand's color scheme." The structural reproduction was excellent — same grid layout, same component hierarchy, same responsive breakpoints. The styling was appropriately different (it didn't copy the exact colors or typography). This use case — "like this, but ours" — is probably the most practically useful multimodal input for real projects.

**Figma mockup export** — a proper design file exported as a PNG. This produced the highest-fidelity output. Codex matched the layout, spacing, and component structure closely. The self-checking loop caught minor deviations — a card that was 2px wider than the mockup, a font weight that didn't match — and auto-corrected them.

The pattern is clear: better inputs produce better outputs. That sounds obvious, but the practical implication is important. You don't need a polished Figma file to get useful output from Codex. A quick sketch on a whiteboard or tablet gets you to a working prototype faster than writing a text description ever could. The visual input eliminates the translation layer between what you see in your head and what you can articulate in words.

For my workflow, the sweet spot is the annotated iPad drawing. Detailed enough that Codex understands my intent. Rough enough that I spend thirty seconds drawing it instead of thirty minutes in Figma. The self-checking loop catches the gaps between my rough sketch and a clean implementation.

## Data Visualization: Where Codex Quietly Excels

The demo I found most surprising wasn't the flashy 3D globe — it was the data visualization section. Codex ingested a raw dataset of New York City taxi cab rides (millions of rows) and generated an interactive dashboard with charts, filters, and map visualizations.

Feeding raw data and getting back a working analytical dashboard isn't new. What's new is that Codex verified its own visualizations visually. When a bar chart rendered with axis labels cut off, the self-check caught it. When a map overlay was partially transparent and hard to read against a light background, the self-check adjusted the opacity.

I replicated this with a client dataset — anonymized sales data across multiple regions and product categories. I gave Codex the CSV and asked for an exploratory dashboard. The first render had a chart where the Y-axis label collided with the chart title. The self-check caught it, added padding, and re-rendered cleanly.

The data visualization use case is where the self-checking loop provides the most consistent value, because chart rendering has so many subtle visual failure modes: overlapping labels, truncated legends, colors that are too similar to distinguish, tooltips that overflow their containers. These are problems that are hard to describe in text but instantly obvious visually. A self-checking AI catches them the same way a human does — by looking.

For anyone who regularly builds dashboards or data-heavy UIs, this capability alone makes Codex worth evaluating.

## The Playwright Integration That Ties It All Together

Under the hood, Codex uses Playwright — the browser automation framework — to render generated UIs and capture screenshots. This isn't just an implementation detail. It means Codex can interact with the generated app the way a user would: clicking buttons, filling forms, scrolling, resizing the viewport.

Front-end developers using Codex alongside Playwright get a continuous visual validation pipeline. Generate code → render in a real browser → capture screenshots at multiple viewport widths → analyze each screenshot → fix issues → repeat. The entire cycle is automated.

I set up a version of this pipeline for one of my projects. Codex generates a component, Playwright renders it at three viewport widths (375px mobile, 768px tablet, 1440px desktop), Codex analyzes all three screenshots, and fixes responsive issues across all breakpoints. The responsive testing that usually happens as a manual afterthought becomes part of the generation process.

The practical impact: I haven't manually resized a browser window to test responsiveness on any component Codex generated this week. Every responsive issue was caught by the automated screenshot analysis. Not every responsive issue — some edge cases involving dynamic content and container queries slipped through — but the obvious ones that waste the most manual testing time.

## Where Codex Falls Short (And It Does)

Time for the honest section. Because the demos are designed to impress, and reality is more textured than demos.

**The self-check isn't comprehensive.** Codex takes a screenshot at one moment in time, at one viewport width (unless you configure the Playwright pipeline for multiple). It doesn't test hover states, animations mid-transition, form validation feedback, or interactive states that require specific user actions to trigger. A button that looks perfect in its default state might have a broken hover state that the screenshot never captures.

**Visual analysis has a resolution limit.** Codex can catch a chart label that's obviously cut off. It struggles with subtler issues: a font weight that's 400 when it should be 500, a color that's #333 when the design spec says #2D2D2D, a line-height that's slightly too tight but not obviously wrong. The vision model's ability to detect "not quite right" is meaningfully weaker than a human designer's eye.

**Code quality takes a back seat to visual correctness.** Codex optimizes for "does it look right in the screenshot?" This sometimes means it generates CSS hacks — absolute positioning where flexbox would be cleaner, hard-coded pixel values where relative units would be more maintainable — because those approaches produce the correct visual output faster. The code works and looks right, but a senior developer would refactor half of it for maintainability.

**Complex state management is still weak.** The demos show mostly static or lightly interactive UIs. When I asked Codex to build a multi-step form with validation, conditional fields, and error states, the self-check only verified the initial state. It didn't test the error state visually, didn't verify that conditional fields appeared correctly, and didn't catch a layout shift that occurred when validation messages appeared. The closed loop works for static visual verification. It doesn't yet handle the full spectrum of interactive state testing.

**Performance isn't considered.** Codex generated a perfectly functional Three.js globe. It also loaded 500KB of unminified Three.js for a component that used maybe 10% of the library. The visual self-check confirms the output looks right. It says nothing about bundle size, render performance, or memory usage. You still need a human (or a different tool) for performance optimization.

These aren't minor quibbles. They're the difference between a demo and a production workflow. Codex's multimodal loop is genuinely impressive for prototyping, initial implementation, and catching obvious visual bugs. It's not yet a replacement for thorough front-end development practices.

## What This Actually Means for Front-End Development

Here's the shift I've made in my own workflow after a week with Codex's multimodal capabilities.

I no longer write first-pass front-end code by hand for new features. Sketch the UI (iPad, thirty seconds), feed it to Codex with a text description of the functionality, let it generate and self-check the initial implementation. Then I spend my time where it actually matters: refactoring the generated CSS for maintainability, adding proper state management, testing interactive flows, and optimizing performance.

My role shifted from "write the code" to "architect the code and refine the output." The first draft — which is the most tedious part of front-end work, wrestling with layout CSS and basic component structure — is now automated and visually verified.

The time savings are hard to quantify precisely because every component is different. But directionally: tasks that took ninety minutes of initial implementation now take about twenty minutes of generation plus thirty minutes of refinement. Roughly half the total time, with a better starting point.

That math won't hold for every project or every developer. If your front-end work is heavily interactive and state-driven, the generation covers a smaller percentage of the total effort. If your work is layout-heavy with lots of cards, grids, dashboards, and data display components — the kind of UI work that many real applications actually need — the coverage is substantial.

The bigger question isn't whether Codex is useful today. It clearly is, with caveats. The question is what happens when the self-checking loop gets better: higher resolution visual analysis, interactive state testing, performance awareness, accessibility checks. Each improvement to the verification side makes the generation side more trustworthy.

We're watching the feedback loop between AI code generation and AI code verification tighten in real time. And the logical endpoint of that tightening — an AI that generates, verifies, and iterates to production quality without human intervention — isn't here yet. But it's visible from here. You can see the trajectory.

The developers who learn to work with this loop now — directing it, refining its output, filling the gaps it can't close yet — will have a significant advantage when it gets good enough to close most of those gaps on its own.

That shift isn't coming next year. Parts of it are already here. I'm staring at a Three.js globe on my screen that was a whiteboard sketch twenty minutes ago, built by an AI that checked its own work and fixed its own mistakes.

What are you going to build when your coding assistant can see?

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
