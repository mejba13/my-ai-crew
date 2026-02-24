**BRAND:** mejba.me
**TITLE:** How I Build Production Design Systems With Figma Make
**SLUG:** figma-make-design-system-workflow
**TAGS:** UI/UX Design, Figma, Design Systems, AI Tools, Tutorial

---

I pulled up a client's dashboard last month and counted five different shades of blue. Three button styles that served the same purpose. Two completely different card components sitting side by side on the same page. The app worked fine. It just looked like four different designers had each built a quarter of it without ever talking to each other.

That's what happens when you skip the design system.

I've shipped projects without one. Early in my career, I thought design systems were overhead — something enterprise teams with dedicated design departments needed, not solo developers or small agencies building fast. I was wrong, and every project I built without one eventually punished me for it. The inconsistencies creep in slowly. A slightly different border radius here. A font weight that doesn't match there. By month three, the UI looks like a patchwork quilt sewn by committee, and every new feature requires you to make fifty micro-decisions that should have been made once.

The problem was never motivation. The problem was time. Building a proper design system from scratch takes days — cataloging colors, defining typography scales, designing component states, documenting spacing rules. Days I didn't have on tight timelines.

Then I found a workflow that cuts that timeline from days to hours using Figma Make, and it changed how I approach every new project. The catch is that the AI-generated output is a starting point, not a finish line, and the gap between "generated" and "production-ready" is where most people's design systems fall apart. I'll show you exactly where that gap lives and how to close it, but first — the preparation step that 90% of tutorials skip entirely.

## The Work Before the Work

Here's what I see developers do wrong consistently: they open Figma, stare at a blank canvas, and start designing. No references. No component inventory. No clear picture of what pages they actually need.

That's like writing code without requirements. You'll produce *something*, but it probably won't be what the project needs.

Before I touch any design tool, I build three things:

**A page inventory.** Every screen the application needs, listed out. Not wireframed, not designed — just named and described. "Dashboard — shows KPIs, recent activity feed, and quick action buttons." "Settings — user profile editing, notification preferences, account management." "Data table — filterable, sortable, with inline editing and bulk actions."

This sounds basic because it is. But I've watched teams skip it and end up designing pages they don't need while forgetting pages they do.

**A component census.** For each page in the inventory, I list every UI component that page requires. Dashboard needs: stat cards, activity feed items, action buttons, a navigation bar, a sidebar. Data table needs: table headers, sortable columns, pagination, filter chips, edit modals, empty states, loading skeletons.

Writing this list forces you to see patterns before you've designed anything. Stat cards and data table cells both need consistent typography. Filter chips and action buttons both need defined states. The component census is where your design system's scope becomes visible.

**A mood board with purpose.** Not a Pinterest board of pretty interfaces. A structured collection of visual references organized by component type and page layout. I use mobin.com heavily for this — it aggregates real UI examples from hundreds of companies, so you're referencing production designs, not concept art.

My mood boards are organized in sections: "Navigation references," "Table layout references," "Card component references," "Dashboard layout references." Each section has 4-6 screenshots showing different approaches to the same component or layout pattern.

This prep work takes about an hour. It saves ten hours downstream. When I eventually prompt Figma Make to generate pages, the instructions are specific enough to produce useful output on the first pass instead of the third.

That specificity is everything when working with AI design tools. Let me show you why.

## Generating UI Pages That Don't Look AI-Generated

Figma Make is an AI-powered design generation tool that sits inside Figma. You describe what you want, and it produces editable design files — layouts, components, full pages. The output quality ranges from "surprisingly good" to "needs significant rework" depending entirely on how you prompt it.

My workflow generates pages in a specific sequence because each page builds context for the next one.

**Navigation first.** The nav bar or sidebar appears on every page, so it sets the visual tone for the entire application. I prompt Figma Make with references from my mood board: "Generate a sidebar navigation with these sections: Dashboard, Projects, Analytics, Team, Settings. Style reference: [description of the mood board examples I liked]. Include collapsed and expanded states."

The first generation usually gets the structure right and the styling 70% right. I adjust colors, tweak spacing, and refine icon choices manually. This takes about fifteen minutes and produces a navigation component I'll reuse across every page.

**Dashboard second.** With the nav component established, the dashboard page has a visual anchor. My prompt includes the specific components from my census: "Generate a dashboard page with: 4 KPI stat cards across the top, an activity feed in the left column, a quick actions panel on the right, and a chart showing weekly trends. Use the navigation component style already established."

Figma Make produces a layout with real structure — not a generic grid, but a considered arrangement that usually needs only minor repositioning. The stat cards come with sensible information hierarchy. The activity feed has reasonable item spacing.

**Data-heavy pages third.** Tables are where most AI-generated designs stumble, because tables have more states than any other component: default, hover, selected, editing, loading, empty, error, filtered, sorted ascending, sorted descending. I prompt each state explicitly.

"Generate a data table page with: sortable column headers, row hover states, inline edit mode for the 'status' column, a filter bar with chip-style active filters, pagination with page size selector, an empty state for zero results, and a loading skeleton state."

This produces multiple artboards in Figma — one for each state. Not all of them are perfect, but having the states generated as a starting point saves enormous time compared to building each one manually.

**Cards, modals, and secondary components last.** By this point, the visual language is established through the nav, dashboard, and table pages. Secondary components inherit that language naturally.

One thing I want to be honest about: Figma Make doesn't produce pixel-perfect production designs. It produces strong first drafts. The layouts are solid. The component relationships make sense. The visual hierarchy is usually correct. But the details — exact padding values, specific font weight choices, subtle color variations between interactive states — those need human refinement.

I spend roughly 30% of my design time generating with Figma Make and 70% refining. That ratio might sound like the AI isn't saving much, but consider: the 30% replaces what used to be 60-70% of the timeline (creating initial layouts from scratch). The refinement phase is faster and more focused because you're polishing, not building.

The pages are done. Now comes the part most people do backwards.

## Building the Design System After the Pages (Not Before)

This is going to sound counterintuitive. Conventional wisdom says: build your design system first, then design pages using those system components. That's the textbook approach, and for large teams with dedicated design system engineers, it works.

For solo developers and small teams, I've found the opposite order works better. Design the pages first (with AI assistance), then extract the design system from the patterns that emerge.

Why? Because designing pages first gives you real-world usage context. You see which components actually appear across multiple pages (those go in the system) versus which are one-offs (those don't). You see which color values you actually used repeatedly versus which were one-time accents. The design system becomes a reflection of real needs, not a speculative library of components you might use someday.

After my pages are refined, I prompt Figma Make to generate what I call a **Minimum Viable Design System (MVDS):**

"Based on the UI pages I've created, generate a design system that includes: color palette with primary, secondary, neutral, success, warning, and error values; typography scale with heading levels H1-H6, body text, captions, and labels; spacing scale; border radius values; and core components — buttons (primary, secondary, ghost, destructive) with all states (default, hover, active, disabled, loading), input fields (default, focused, error, disabled), cards, badges, avatar sizes, and table cell styles."

The MVDS output from Figma Make is genuinely useful. Not comprehensive, but useful. The colors match what I actually used in the page designs. The typography scale reflects real heading relationships. The components have the right visual DNA.

What it misses — and this is predictable — are the edge cases. Alert boxes, toast notifications, empty states, loading patterns, filter components, pagination variants, modal structures with different content types. These need a second generation pass.

"Extend the design system with: alert components (info, success, warning, error with dismissible and persistent variants), toast notifications, loading spinners and skeleton screens, empty state illustrations, pagination with page numbers and load-more variants, modal shells for confirmation dialogs and form modals, and dropdown menus with search."

This second pass produces a more detailed system, but here's a practical warning: **very detailed design systems can be too large for direct import into some development tools.** If you're planning to push this into a codebase through Figma's code export, split the system into chunks — foundations (colors, typography, spacing), core components (buttons, inputs, cards), and complex components (tables, modals, navigation) — and import them separately.

I learned this after a failed import that crashed my development environment because the design token file was 3,000 lines long. Chunking fixed it immediately.

## From Design to Code Without Losing Your Mind

This is where the workflow forks, and which path you take depends on your team setup.

**Path A: Direct code export for solo developers.**

Figma Make can generate code output from your designs. For React, Vue, or vanilla HTML/CSS projects, this is a legitimate time-saver — you get component code that matches your visual design without manually translating pixels to CSS.

But — and this is critical — the generated code will have hardcoded values everywhere. `color: #3B82F6` instead of `var(--color-primary)`. `font-size: 16px` instead of `var(--text-body)`. `padding: 24px` instead of `var(--space-6)`.

Shipping hardcoded values is technical debt disguised as velocity. The moment a client says "can we make the primary color slightly darker?" you're doing a find-and-replace across fifty files instead of changing one variable.

Before pushing any Figma Make code to your repository, run a refactoring pass to convert hardcoded values into **design tokens**. I prompt Figma Make for this explicitly: "Refactor this component code to use CSS custom properties for all color, typography, spacing, and border radius values. Generate a corresponding design tokens file that defines these variables."

The output needs review — the AI sometimes creates redundant tokens or misnames variables — but it handles 80% of the conversion correctly. The remaining 20% is quick manual cleanup.

**Pro tip:** Establish your token naming convention before the refactoring pass and include it in the prompt. "Use the format `--color-[semantic-name]` for colors, `--text-[size-name]` for typography, `--space-[scale-number]` for spacing." Consistent naming from the start prevents token naming chaos later.

**Path B: Figma handoff for teams.**

If you're working with other developers or a design team, the code export path creates merge conflicts and ownership confusion. Instead, keep everything in Figma as the source of truth.

Organize your Figma file with clear structure:
- Page 1: Design System — foundations and components
- Page 2: UI Screens — all application pages
- Page 3: Component States — interactive state documentation
- Page 4: Handoff Notes — annotations for developers

Share the Figma file with your team. Iterate collaboratively. When the designs are finalized, developers can use Figma's inspection tools or Figma MCP to rebuild components in their framework of choice with the design system as reference.

This path is slower but produces fewer integration headaches on multi-person teams. The design system lives in one place, everyone references the same source, and changes propagate through the Figma file rather than through code PRs.

I use Path A for my solo projects and client work where I'm the only developer. I use Path B for agency projects at Ramlit where multiple developers need to implement the same designs. Both work. Neither is universally better.

## The Mistakes I Keep Seeing (Including My Own)

Let me save you some pain by cataloging the failures I've experienced and observed.

**Mistake 1: Treating the AI output as final.** I've seen developers export Figma Make's generated code directly into production without a refinement pass. The UI works but feels off — inconsistent spacing, slightly wrong color relationships, components that don't quite align with each other. AI-generated design is a draft. Always a draft. The refinement phase isn't optional polish; it's where the design becomes professional.

**Mistake 2: Building too much design system too early.** My first attempt at an MVDS included 47 component variants. I used 12 of them in the actual project. The other 35 sat unused, cluttering the system and confusing the design token file. Start minimal. Add components when a page actually needs them, not when you imagine a future page might.

**Mistake 3: Skipping the mood board.** When I'm in a hurry, I'm tempted to skip inspiration gathering and go straight to prompting. The output quality drops noticeably. Figma Make performs significantly better when your prompts reference specific visual patterns: "table with sticky headers like Notion" versus "generate a table." The mood board isn't decoration — it's prompt engineering for visual AI.

**Mistake 4: Ignoring responsive states.** Figma Make generates desktop layouts by default. If your application needs mobile or tablet layouts — and in 2026, it does — you need to explicitly prompt for those breakpoints. I generate desktop first, then prompt: "Create responsive variants of this page for tablet (768px) and mobile (375px) viewports, adjusting the layout while maintaining the same component styles." The mobile variants usually need more manual adjustment than desktop, but starting from a generated layout beats starting from nothing.

**Mistake 5: Forgetting dark mode.** If your application supports dark mode — and increasingly, users expect it — generate your design tokens with both light and dark values from the start. Retrofitting dark mode onto an existing token system is painful. I know because I've done it three times, and each time I swore I'd remember to include it upfront. I finally did on the fourth project.

## What Changes When You Work This Way

After using this workflow on eight projects over six months, the measurable differences are clear.

**Design-to-development handoff time dropped by about 60%.** The biggest savings came from eliminating the blank-canvas phase. Starting from AI-generated layouts and component states rather than empty artboards compresses the creative phase without sacrificing quality — as long as you invest in the refinement phase.

**UI consistency improved across every project.** Even the MVDS — the minimal version — enforces enough consistency to eliminate the "built by committee" look. Clients notice. I've had two separate clients comment that the interface feels "more polished than expected" on projects where I used this workflow. They can't articulate what's different, but they feel the coherence.

**Component reuse across projects became practical.** When your design systems follow the same token structure and naming convention, components from Project A transfer to Project B with minimal adjustment. My button component, my card component, my table styles — they've been refined across eight projects now. Each iteration is better than the last, and spinning up the visual foundation for a new project takes hours instead of days.

**The design conversation with clients shifted.** Before this workflow, early design reviews were often about fixing inconsistencies: "These two pages don't feel like the same app." Now the conversations focus on strategic questions: "Should the dashboard prioritize the activity feed or the KPIs?" That's a much more productive use of everyone's time.

Quick wins appear in the first project — faster page generation, more consistent output. The compound benefit shows up around project three or four, when your refined components and established token conventions start paying recurring dividends.

## Design Systems Are Infrastructure, Not Decoration

There's a mindset shift buried in this entire workflow that I want to make explicit, because it's the thing that took me longest to internalize.

A design system isn't a nice-to-have deliverable you create for documentation purposes. It's infrastructure. It's the foundation that every visual decision in your application stands on. When the foundation is solid, every page you build on top of it is faster, more consistent, and easier to maintain. When the foundation is missing, every page is an isolated construction project with its own rules.

AI tools like Figma Make have made building that infrastructure dramatically faster. What used to be a multi-day investment — and therefore easy to skip under deadline pressure — is now a few hours of focused work. The cost-benefit calculation has shifted. Skipping the design system no longer saves meaningful time, but the debt it creates is exactly the same as it always was.

My challenge to you is specific: pick your next project — the one sitting in your pipeline right now. Before you write a single line of front-end code, spend one morning on this workflow. Collect your inspiration. Generate your pages. Extract your MVDS. Set up your design tokens.

When you start building the UI with that foundation under you, pay attention to how different it feels. Pay attention to how many decisions are already made. Pay attention to how fast you move when you're not reinventing spacing and color choices on every component.

That feeling? That's what shipping with a system feels like. And once you've felt it, you won't go back to the patchwork quilt.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
