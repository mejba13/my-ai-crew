**BRAND:** mejba.me
**TITLE:** I Built a Full App in 2 Hours Without Figma First
**SLUG:** claude-code-designers-figma-sync
**TAGS:** Claude Code, UI Design, Figma Integration, AI Development, Tutorial

---

Two hours. That's how long it took me to go from a blank terminal to a fully interactive app with micro animations, hover states, and a complete design system — all without opening Figma once.

And then I pushed the whole thing *back into Figma* as editable layers.

I realize that sentence probably broke something in your brain if you're a designer. It broke mine too. The workflow we've all internalized — design in Figma, hand off to dev, wait three weeks, get something back that looks nothing like the mockup — just got flipped completely upside down. Build first, sync to Figma second. Code-first design. And before you tell me that's a developer workflow pretending to be a design tool, let me show you what I actually built, because the output would fool any design lead into thinking it came from a senior product designer's Figma file.

The secret is Claude Code's Figma MCP integration combined with a workflow most designers haven't discovered yet. I spent a week testing three specific use cases — design system translation, rapid prototyping from screenshots, and full landing page conversion — and the results made me rethink what "designer" even means in 2026.

But here's the catch nobody mentions in the tutorials: setting up the environment wrong will burn through your credits in an hour. I learned that the expensive way, and I'll save you from making the same mistake.

## The Setup Most Designers Get Wrong

Here's the thing about Claude Code that confuses designers the first time they encounter it. They hear "Claude," they open claude.ai, they select Opus 4.6 from the model dropdown, and they start prompting for code. Thirty minutes later, their credits are gone and they have a half-finished HTML file they can't do anything with.

That's not how this works. At all.

Claude Code runs inside an *environment* — think of it like a workspace that organizes your files, manages your AI agents, and gives you a proper development context. The tool works best when paired with an editor like Cursor, though you can also run it through Google's AntiGravity or similar platforms. I use Cursor because the file management feels natural if you've ever used VS Code.

The environment matters because Claude Code needs to see your project structure. It needs to know where your CSS lives, where your HTML files are, what assets you've imported. When you just chat with Claude in a browser window, it's generating isolated code snippets with no project awareness. When you run it inside a proper environment, it generates code that fits into your actual file structure, references real paths, and builds on what already exists.

Setting up took me about 15 minutes the first time. Now I can spin up a new project in under 3 minutes. Here's the process:

**Step 1: Get the right subscription.** Claude Code offers tiered plans. The "Pro" plan works for experimentation and learning. If you're doing real project work — especially with multiple agents running simultaneously — the "Max" plan gives you unlimited credits and parallel agent support. For designers testing the waters, Pro is fine. Once you're hooked (and you will be), upgrade.

**Step 2: Install Claude Code as an extension inside Cursor.** This connects the AI directly to your project workspace. Every prompt you write has full context of your files.

**Step 3: Start local.** Create your project folder, initialize it, and work locally first. GitHub integration comes later when you want to host or share. Don't overcomplicate the start.

That last point matters more than you'd think. I've watched three designer friends try to set up Claude Code by creating a GitHub repo first, configuring deployment pipelines, setting up CI/CD — all before writing a single line of code. They were following developer tutorials, not designer workflows. Start local. Get your first build working on your machine. The cloud stuff can wait.

Now, the Figma sync — this is the part that changes everything for design workflows, and setting it up is simpler than you'd expect.

## Connecting Claude Code to Figma (The Part That Changes Everything)

Syncing Claude Code with Figma requires a personal access token. Here's exactly how to generate one:

1. Open Figma, go to your account settings
2. Navigate to the "Personal Access Tokens" section
3. Generate a new token with **full permissions** — read, write, the works
4. Set the expiration to 90 days (the maximum)
5. Copy that token immediately — Figma only shows it once

Back in Claude Code, run the authentication command with your token. Once connected, you have a two-way bridge between your code environment and Figma. Designs flow in both directions.

Why does this matter? Because the traditional workflow is one-directional. Design goes to dev. Dev interprets it. Stuff gets lost. With Claude Code's Figma integration, you can:

- Pull design specs directly from Figma into your code prompts
- Push generated HTML/CSS back into Figma as editable vector layers
- Keep both environments in sync as you iterate

I'll be honest — when I first heard "push code back to Figma," I assumed it would create some janky rasterized screenshot. Nope. The HTML to Design pipeline converts your code output into actual Figma layers. Editable text. Resizable frames. Proper auto-layout. It's not pixel-perfect every time, but it's close enough to continue iterating in either direction.

That bi-directional flow unlocks something I didn't expect: the ability to prototype in code (where interactions actually work) and then refine in Figma (where visual polish is faster). Best of both worlds. But I'm getting ahead of myself — let me show you the three use cases where this setup genuinely shines.

## Use Case 1: Translating a Design System to Production Code

This was my first experiment, and it sold me on the entire workflow.

I had an existing design system in Figma — typography scale, color palette, shadow definitions, button variants, spacing tokens. The kind of foundation every product team builds and then struggles to translate into code that actually matches. I've been on projects where the design system Figma file and the CSS implementation drifted apart within two weeks. It happens because maintaining both manually is tedious and error-prone.

Here's what I did: I opened the design system file in Figma's Dev Mode, selected the pages containing my core tokens, and copied the component references. Then I dropped into Claude Code with a specific prompt describing what I wanted — a complete CSS implementation of the design system with proper custom properties, responsive typography, and component classes.

Claude Code generated the CSS and HTML files in about 4 minutes.

The typography matched. The colors were exact hex values pulled from the Figma specs. The button variants had the correct padding, border-radius, and state styles. I opened the generated HTML in Live Server (a simple browser preview extension) and compared it side-by-side with the Figma file.

It wasn't perfect — the shadow values needed slight adjustment, and one of the font weights defaulted to 500 instead of 600. But these were 30-second fixes. The structural accuracy was something that would have taken a developer an entire day to build from scratch, assuming they interpreted the Figma specs correctly.

**Pro tip:** When translating design systems, feed Claude Code one category at a time. Don't dump your entire system in a single prompt. Do typography first, then colors, then shadows, then components. The output is more accurate when each prompt has a focused scope.

The real payoff came two weeks later when a client requested changes to the primary button style. I updated it in Claude Code, the CSS propagated across every page using that component, and I pushed the updated design back to Figma for stakeholder review. One change, both environments updated. No "which file is the source of truth?" confusion.

This alone would justify the setup time. But the second use case is where I had the most fun.

## Use Case 2: Rapid Prototyping From Screenshots

Imagine this. You're scrolling Dribbble, or browsing a competitor's site, or looking at a screenshot a client sent you with the note "something like this." Normally, you'd open Figma, recreate the layout from scratch, approximate the spacing, guess at the type scale. An hour later, you have a static mockup.

With Claude Code, I took a screenshot of a design I liked — a SaaS dashboard with a sidebar nav, metric cards, and a data table — dragged it into my Claude Code prompt, and described what I wanted: "Create a single HTML file with embedded CSS that recreates this layout. Include hover animations on the cards and a smooth sidebar toggle."

Ninety seconds later, I had a working prototype in my browser.

Not a mockup. Not a wireframe. A working, interactive page with hover states that actually responded to my cursor, a sidebar that collapsed with a smooth CSS transition, and metric cards that scaled properly when I resized the window.

Was it production-ready? No. The data was placeholder, the responsive breakpoints needed work, and some of the spacing was slightly off. But as a *starting point for exploration* — for testing whether a layout direction feels right before investing hours in high-fidelity design — it's unmatched.

I've started using this workflow for client discovery calls. Instead of presenting static mood boards, I show interactive prototypes I built in the 20 minutes before the meeting. Clients can click through them, resize the browser, see how things move. The feedback is dramatically better because they're reacting to something that *feels* real.

The speed advantage is hard to overstate. A layout exploration that took me 2-3 hours in Figma now takes 5-10 minutes in Claude Code. I can generate four different approaches in the time it used to take me to build one. And the ones that don't work? I delete the file and move on. No emotional attachment to a carefully crafted Figma frame.

**Pro tip:** For screenshot-based prototyping, stick to single HTML files with embedded CSS. Don't ask Claude Code to set up a framework or component architecture for quick explorations. The simpler the file structure, the faster the iteration.

Here's where it gets interesting, though — because the third use case is where I hit the biggest challenges.

## Use Case 3: Converting a Full Landing Page From Figma to Code

This was the ambitious test. I had a complete landing page designed in Figma — hero section, feature grid, testimonials, pricing table, footer. Custom illustrations, gradient backgrounds, the whole production.

I grabbed the Figma link and prompted Claude Code to convert the full design to HTML and CSS.

The first output was... mixed. The structure was right — every section existed, the layout flow matched the design, the typography was on point. But the custom illustrations didn't translate (expected — AI can't recreate vector art from a link reference), and some of the gradient angles were off.

Here's what I learned and what saved the project: **don't send the entire page at once.** Break it into sections.

I restarted the process, sending Claude Code the hero section link first. The output was significantly more accurate — the gradient matched, the headline typography was correct, the CTA button had the right padding and border-radius. I approved it, then sent the features section, then testimonials, building the page incrementally.

Section by section, the landing page came together in about 45 minutes. Each prompt was focused, and Claude Code could devote its full attention to getting one section right instead of approximating an entire page.

The custom illustrations were the one genuine gap. I ended up exporting those from Figma as SVGs and dropping them into the project folder manually. Claude Code then referenced them correctly in subsequent prompts. Not fully automated, but a minor manual step in an otherwise streamlined process.

For the final polish, I used iterative prompts: "Add a dark mode toggle that transitions smoothly" (took 2 minutes), "Make the pricing cards animate in on scroll" (90 seconds), "Add a mobile navigation drawer" (3 minutes). Each addition landed cleanly because Claude Code understood the existing code structure.

The finished product — a fully responsive, animated landing page with dark mode — would have taken me 2-3 days to hand-code or 4-5 hours of back-and-forth with a developer. Total time from Figma design to working code: about 2 hours including the false start.

## The Real Talk: What This Tool Can't Do Yet

I've been painting a pretty rosy picture, and that's because the results genuinely impressed me. But I'd be lying if I said the workflow was flawless, and you deserve to know the limitations before you invest time setting this up.

**Claude Code generates front-end code.** HTML, CSS, JavaScript for interactions and animations. If your project needs a backend — user authentication, database connections, server-side logic — you're stepping into developer territory. The tool won't generate your API endpoints or database schemas from a Figma file. For marketing sites and interactive prototypes, this doesn't matter. For product apps, you'll still need backend support.

**Custom illustrations are a manual step.** As I mentioned, complex vector art needs to be exported from Figma separately. Claude Code can reference image files in your project, but it can't recreate hand-drawn illustrations from a design spec. Expect to handle assets manually.

**The Figma-to-code accuracy depends on design organization.** If your Figma file uses proper auto-layout, named layers, and component structures, Claude Code's output is remarkably faithful. If your Figma file is a mess of absolute-positioned frames with "Frame 247" names... the output reflects that chaos. Good design hygiene in Figma leads to better code output. Same as with human developers, honestly.

**Iteration is required.** I've never gotten a perfect output on the first prompt for anything beyond simple components. The workflow is: generate, preview, identify gaps, prompt for fixes, repeat. Usually 2-3 rounds. That's still dramatically faster than traditional hand-coding, but don't expect magic on the first try.

**Credit consumption varies wildly.** Simple component generation barely dents your budget. Full landing page conversion with iterative refinement can consume 20-30% of a Pro plan's daily allocation. If you're doing this professionally, budget for the Max plan and save yourself the anxiety of watching credits tick down mid-project.

One prediction I'll make: within the next six months, the Figma-to-code accuracy will improve significantly as these models get better at understanding spatial relationships and design tokens. The gap between "generated" and "hand-crafted" is already smaller than most people assume. It's closing fast.

## What Designers Should Actually Measure

After a week of using this workflow across client projects and personal experiments, here's what the numbers look like.

**Design system translation:** 15-20 minutes for a complete token system (typography, colors, shadows, buttons). Manual equivalent: 4-6 hours.

**Rapid prototyping from screenshots:** 5-10 minutes per layout exploration. Manual equivalent: 2-3 hours per mockup.

**Full landing page conversion:** 1.5-2.5 hours including iteration. Manual equivalent: 2-3 days of designer-developer collaboration.

**Figma sync round-trips:** I pushed code back to Figma 8 times across the week. 6 of those produced clean, editable layers that my team could work with directly. 2 needed minor manual cleanup in Figma. That's a 75% clean-sync rate, which honestly exceeded my expectations.

**Client feedback turnaround:** This is the metric I care about most. Interactive prototypes generated same-day meant clients could give feedback in the first meeting instead of waiting for a second review round. Two projects moved from discovery to approved direction in a single session. That's never happened to me before with static mockups.

The quick wins are obvious — speed, cost, iteration velocity. The long-term gain is subtler but more important: designers who can build interactive prototypes and production-ready front-ends have a fundamentally different position in the industry. You're not waiting for someone else to bring your vision to life. You're building it yourself and proving it works before anyone can argue about feasibility.

## The Workflow That Actually Works

After testing every combination I could think of, here's the workflow I've settled on for design projects:

**For exploration and concept work:** Start in Claude Code. Generate rapid prototypes from screenshots or verbal descriptions. Don't touch Figma until you've found a direction that feels right. This is counterintuitive for designers trained on Figma-first workflows, but the speed of code-based exploration changes the math.

**For production design systems:** Build in Claude Code, sync to Figma for team review. Update in either environment as needed. The bi-directional sync means neither becomes stale.

**For landing pages and marketing sites:** Design key sections in Figma for stakeholder approval, then convert to code section-by-section in Claude Code. Add interactions and animations in code (where they actually work), then push final builds back to Figma for documentation.

**For client presentations:** Always show the interactive version. Static mockups undersell your work. A 5-minute Claude Code session before a meeting can turn a flat design into a clickable prototype that closes deals.

The common thread across all of these: Claude Code isn't replacing Figma. It's giving Figma a production-capable partner. Design and code stop being separate phases of a project and start being two views of the same thing.

If you'd told me a year ago that I'd be recommending designers learn terminal commands, I'd have laughed. But the terminal I'm using isn't asking me to write JavaScript from scratch. It's asking me what I want to build, and then building it while I make creative decisions.

That's not a developer workflow wearing a designer mask. That's what design tools should have been all along.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
