**BRAND:** mejba.me
**TITLE:** The AI Design System Workflow That Stopped Giving Me Slop
**META TITLE:** AI Design System Workflow With Claude + Figma MCP
**SLUG:** ai-design-system-workflow-claude-figma
**PRIMARY KEYWORD:** AI design system workflow
**SECONDARY KEYWORDS:** Claude Figma MCP, design tokens skill, AI-generated UI design
**META DESCRIPTION:** The structured workflow I use to make Claude generate UI that actually respects a design system — tokens, components, Mobbin references, Figma MCP.
**TAGS:** Claude Code, Figma MCP, Design Systems, AI Design, Tutorial
**CONTENT CLUSTER:** AI Tools & Productivity
**TRANSFORMATION GOAL:** After reading, the reader will be able to structure their design system as training data for Claude and generate first-pass UI that actually respects tokens, components, and style — instead of generic AI slop.

---

I spent the better part of a Saturday arguing with Claude about a button.

Not the logic. The *button*. The primary call-to-action on a paywall screen for a finance app I was prototyping. I'd hooked up the Figma MCP, pointed Claude at my design system, typed "build me a paywall using our components" — and the thing it produced looked like every other AI-generated screen I've ever seen. Rounded corners that felt arbitrary. A gradient that didn't exist anywhere in my system. Spacing that was *almost* right, in the way a cover band is *almost* the original. Close enough to recognize. Far enough to hurt.

I closed the file. Made coffee. Sat down and actually read what I'd been doing.

Here's what I realized: I'd been treating Claude like a magic wand. Wave it at a Figma file, expect pixel-perfect output. And it was quietly doing exactly what any model does when you hand it a pile of unlabeled data — averaging. Averaging my tokens against every other design system it had ever seen. Averaging my buttons against every button on the internet. The result was a ghost of my design system wearing a convincing costume.

The fix wasn't a better prompt. The fix was structured training data — tokens with descriptions, components grouped with usage rules, specific example screens — then iterating locally in Claude Code before pushing a single frame to Figma. Once I did that, my first-pass UI went from "yeah I'll rebuild this" to "actually, I just need to tweak two text styles."

That's the workflow I want to walk you through. Nothing revolutionary. Just boring, patient structure that makes Claude behave like a junior designer who's actually read your system — not a drunk tourist who's heard of design.

## Why Vanilla Figma AI And "Just Prompt It" Workflows Fall Apart

Let me describe the failure mode precisely, because most writeups skip this part.

You install the Figma MCP server. You connect Claude to your Figma file. You write "generate a settings screen using our design system." Claude goes off, reads some metadata, produces something. You open it. It's wrong in ways that are hard to name — the hierarchy feels off, the padding is the wrong 16 (the 16 that exists in every system, not the 16 you use), the card shadow is close but not quite your shadow. It technically *uses* your components. It just doesn't understand *when* to use them.

This happens for a reason that's obvious once you name it: **a design system file is not training data.** A design system file is a warehouse. Tokens sit in one collection. Components sit in another. Usage rules live in a Notion doc nobody reads. The glue — the actual knowledge of "when do we use the elevated surface vs the raised one" — only exists in your senior designer's head.

When Claude reads that warehouse, it sees names and values. It does not see intent. And without intent, every ambiguous decision gets resolved by statistical averaging against everything it's ever seen on the internet. Which is how you end up with a paywall that looks vaguely like Stripe, vaguely like Linear, and not at all like your app.

Figma's own stance on this is actually pretty clear — they've published [a guide to building custom skills](https://developers.figma.com/docs/figma-mcp-server/create-skills/) for exactly this reason. The MCP server ships with foundational skills like `figma-use` and `figma-generate-library`, but those are scaffolding. They expect you to layer your own domain knowledge on top. Most people don't. Then they blame the model.

I blamed the model too. Then I stopped.

There's a third problem most tutorials dodge, and we'll get to it later in the implementation section — but it has to do with what happens when a model sees three variants of a "card" component and has to pick one. I'll show you what that looks like and how to fix it. But first we need tokens that talk.

## Tokens That Talk: The Template That Changed Everything

Here's the single biggest unlock in this whole workflow, and it's embarrassingly simple.

Claude is a language model. Your tokens are mostly numbers and hex codes. When you give a language model non-linguistic input, it has to infer meaning. When you give it linguistic input, it follows instructions. So the fix is to make your tokens *speak English*.

I use a four-column template for every single token. Name. Light value. Dark value. A one-sentence description of when to use it.

Here's a chunk of my actual surface tokens:

```
| Token                  | Light    | Dark     | When to use                                      |
|------------------------|----------|----------|--------------------------------------------------|
| surface-base           | #FFFFFF  | #0B0B0F  | The page background. Nothing sits behind this.   |
| surface-raised         | #F7F7F9  | #15151B  | Cards, list rows, anything one layer above base. |
| surface-elevated       | #FFFFFF  | #1D1D25  | Modals, popovers, menus — floating UI only.      |
| surface-sunken         | #F0F0F3  | #08080C  | Input fields, inset wells, read-only regions.    |
| surface-brand-subtle   | #EEF2FF  | #1A1D3A  | Brand-tinted backgrounds for promoted content.   |
```

Read that "When to use" column. That's the part Claude cares about. That's the part that turns a token from a value into an instruction.

Do this for every token category — surface, content (text), border, action, status, elevation, radius, spacing, motion. Yes, spacing. `space-2: 8px — tight rhythm inside dense components like form field groups` is a thousand times more useful than `space-2: 8px`.

This aligns with what the design systems community has been converging on in 2026 — [semantic tokens with intent baked into the name](https://medium.com/design-bootcamp/color-tokens-guide-to-light-and-dark-modes-in-design-systems-146ab33023ac), paired with documentation that describes *purpose*, not appearance. The twist I'm adding is that the documentation has to live *with* the token in the format Claude ingests, not in a separate wiki.

Save this table as a markdown file. `design-tokens.md`. Keep it in your project repo, right next to your code. This file is now your real design system — the Figma variables are just the rendered output.

Quick aside — I resisted this for weeks because I assumed Claude could just read the Figma variable descriptions directly. It can, technically. But the descriptions most teams write in Figma are either empty or written for designers ("primary brand color") instead of for a model that has to decide between five blues at 2 AM on a Saturday. Rewrite them for the model. Your designers can still read them. Nobody loses.

That's half the battle. The other half is components — and components are where the workflow usually dies if you don't group them right.

## Grouping Components So Claude Doesn't Panic

Here's a thing I learned the hard way. If you hand Claude a design system with 140 components in a flat list and ask it to build a screen, it behaves like a contractor handed a hardware store and told to build a kitchen. Technically it has everything it needs. Practically it's going to use the wrong hammer.

The fix: group your components into semantic categories, with a dedicated skill for each group (or, if you want to keep things simple, one skill that knows all the groups). The three groupings that cover 90% of product UI:

**1. Form elements.** Inputs, textareas, select menus, checkboxes, radios, toggles, date pickers, file upload zones, form row, form error, form help text. Every variant. Every state — default, focus, error, disabled, loading.

**2. Navigation.** Top bars, side nav, tab bars, breadcrumbs, pagination, back links, segmented controls, stepper. States matter here too — active, hover, disabled, collapsed.

**3. Data display.** Tables, cards, list items, stat tiles, badges, tags, avatars, empty states, loading skeletons, charts, pagination footers. This is where most AI-generated screens fall apart because the model defaults to tables when a card grid would be right, or stat tiles when a list is right.

For each component inside each group, document three things for Claude:
- **Variants** — all of them, with the variant key names exactly as they appear in your Figma properties panel
- **Props** — every boolean and enum the component exposes
- **Usage rule** — one sentence: "Use the compact variant for dense tables with more than 8 columns. Default variant for everything else."

That third line is what stops Claude from picking a random variant because the name sounded cool.

This is the pattern I worked out in [my earlier Figma MCP writeup](https://mejba.me/claude-code-figma-mcp-workflow), and it's the pattern that made the current workflow actually ship usable first-pass designs. If you've built design systems before, you already know the hardest part isn't defining components — it's documenting when to reach for which one. That documentation has always been valuable for humans. It turns out it's even more valuable for models.

Pro tip: if you have more than one designer on your team, get them in a room and argue about the usage rules out loud before you write them down. The argument *is* the spec. The disagreements that surface are the exact edge cases Claude will otherwise get wrong. Write the agreed answer as the rule.

You'd think we're ready to generate now. We're not. There's one more piece, and it's the piece most people skip entirely.

## Stop Saying "Build Me A Modal" — Show Claude Exactly What You Mean

The single biggest prompt-engineering mistake I see in AI design work is vagueness masquerading as brevity. "Build me a modal." "Design a dashboard." "Make a settings screen." These feel like sharp prompts. They are not. They are the prompt equivalent of telling a contractor "build me a kitchen" with no floor plan.

Here's what actually works: **pair your component skill with specific example screens from real production apps.**

This is where Mobbin earns its subscription. [Mobbin's library](https://mobbin.com/) is sitting on more than 600,000 screens from 1,200+ production apps as of 2026 — which means for almost any UI pattern you're trying to build, a shipped version exists somewhere in there, built by a team that's probably thought about it harder than you have time to.

My actual workflow, using the paywall example from Saturday:

1. Open Mobbin. Search "paywall" inside the Finance category.
2. Pull up Rocket Money's paywall. Pull up Copilot's. Pull up YNAB's.
3. Screenshot two or three that match the tone I'm going for.
4. Drop the screenshots into Claude Code as image references alongside my tokens skill and components skill.
5. Prompt: *"Build a paywall screen for a finance app. Style and layout reference attached. Use our design tokens and components. Output as HTML. Hero: annual plan at $79.99 with 'Save 40%' pill. Three feature rows. Monthly toggle. Trust logos at bottom."*

That prompt has everything a designer would need. References for style and composition. Specificity on content. Constraints on the building blocks. No ambiguity for the model to resolve by averaging.

Compare that to "build me a paywall." The first version gives you a real screen. The second version gives you a hallucination of a screen.

(If you don't want Mobbin, the 2026 alternatives have gotten genuinely good — [InspoAI](https://www.inspoai.io/blogs/mobbin-alternatives) adds natural-language search, Appshots shows flows rather than single screens, Webframe is web-first. I still keep Mobbin because the Finance and B2B SaaS categories are deeper there. Your mileage will vary.)

One more thing about references — use two or three, not one. One reference and Claude copies it. Three references and Claude interpolates, which is closer to what you actually want. The art of the prompt is in the distance between the examples you pick.

Alright. You have tokens. You have grouped components with usage rules. You have example screens. Now we get to the part where you actually install the machinery and generate.

## Installing The Figma Skills In Claude

Two skills do the real work in this pipeline, and both are a five-minute install.

**1. `figma-use`** — this is the foundational skill from Figma's own MCP server. It lets Claude write to your Figma canvas: create frames, instantiate components, apply variables, set styles. Everything that goes into Figma goes through this skill. [Figma's official documentation](https://help.figma.com/hc/en-us/articles/39166810751895-Figma-skills-for-MCP) covers installation; the short version is: clone the skill repo, zip it, drop it into Claude's skills directory, restart the session. Done.

**2. Your own "Apply Design System" skill** — this is the custom piece. A single markdown file that you author. It contains:
- A link or reference to your `design-tokens.md` file
- A link or reference to your `components.md` file (grouped by category, with usage rules)
- A preamble that tells Claude *how* to apply these: "Always use semantic tokens. Never use raw hex values. Always pick the component variant whose usage rule matches the context. When in doubt, ask before guessing."
- A list of forbidden behaviors: "Do not invent new tokens. Do not create new components. Do not use gradients that don't exist in the token list. Do not round corners with arbitrary radius values."

That preamble matters. The forbidden-behaviors list in particular is what stops the "averaging toward the internet" drift. You're not teaching the model to be creative. You're teaching it to be disciplined. Disciplined is what you want on a first pass.

Ship both skills. Restart Claude Code. Verify they loaded.

At this point you have a Claude instance that understands your tokens in English, knows your components in context, has reference screens for the pattern you want, and has skills that let it write to Figma. This is the setup. Now we generate — and here's the non-obvious part nobody tells you.

If you'd rather have someone set up this whole pipeline end-to-end for a production design system — tokens skill, component skills, MCP wiring, team rollout — that's a specific engagement I take on. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## Generate Locally In Claude Code First. Then Push To Figma.

Here's the step most tutorials get wrong. They prompt Claude once, let it push directly to Figma, see the result, and start iterating inside Figma. That workflow is slow, token-expensive, and produces worse output because every iteration round-trips through the MCP server.

The right pattern: **iterate in Claude Code as HTML first. Push to Figma only when the HTML is acceptable.**

My actual sequence for the finance paywall:

1. **Prompt with everything loaded** — tokens skill, components skill, three Mobbin references, the specific content brief. Ask for HTML output with Tailwind classes mapped to my design tokens.

2. **Claude generates the HTML** inline in Claude Code. I render it in a browser preview. It takes about 8 seconds. No Figma round-trip.

3. **I critique it.** Not "make it better." Specific: "The hero CTA is using `action-primary-subtle` but this is a conversion surface — use `action-primary-bold`. The feature row spacing is using `space-3`; this pattern calls for `space-4` because the icons are 24px. The trust logos row is missing a divider."

4. **Claude updates the HTML.** Another 6 seconds. I re-render.

5. **I do that loop two or three times.** At this stage, each iteration is cheap — no Figma, no MCP payload, just text.

6. **When the HTML is 90% there**, I ask Claude to push it to Figma using the `figma-use` skill. This is the expensive operation, and I only do it once per design.

7. **Claude writes the frames into Figma.** Real components. Real variables. Real variants. Responsive. Named layers. Auto-layout applied. Minor text style misses, which I'll cover in a second.

This local-first pattern cuts my iteration time roughly in half and burns significantly fewer tokens. It also produces better output, because I'm iterating on the design while it's still cheap to change. By the time I push to Figma, the design is essentially done — Figma is the destination, not the workshop.

One specific thing worth calling out: when you ask for HTML output, specify the token names in the prompt. "Use classes like `bg-surface-raised`, `text-content-primary`, `rounded-radius-md`." This forces Claude to treat the tokens as first-class citizens in the generated markup. You can wire these up to your actual Tailwind config later, or just read them as documentation of which tokens got used where. Either way, you're getting auditable output.

## The Honest Part: What This Workflow Still Gets Wrong

I've shipped dozens of screens through this pipeline at this point. It's a massive improvement over vanilla prompting. It is not magic. Here are the specific things it still misses, with no sugar-coating.

**Text styles get missed more than anything else.** Claude will nail the spacing, the colors, the components, the layout — and then apply `text-body-md` to a heading that should be `text-heading-sm`. I don't fully understand why typography specifically is the weak point. My working theory is that text style tokens tend to encode more subtle intent ("use this for mid-density list headings") than color or spacing tokens, and the model has less signal to latch onto. Either way: always audit typography manually on the Figma side. Budget three to five minutes per screen for this cleanup.

**Auto-layout direction sometimes flips.** A row becomes a column, or vice versa, especially inside nested components. Usually a one-click fix in Figma, but it's an annoying tax.

**Brand personality doesn't land.** The workflow produces screens that are *correct*. It does not produce screens that are *soulful*. That extra 10% — the micro-interaction, the one unusual composition choice, the type treatment that makes your app feel like yours — still has to come from a human. This workflow gets you to a polished, on-system first draft. It does not get you to art.

**Tokens sometimes get applied literally instead of semantically.** If Claude sees `surface-raised: #F7F7F9` in my tokens file, it occasionally writes `#F7F7F9` into the HTML instead of `bg-surface-raised`. The preamble forbidding this helps, but doesn't eliminate it. Audit the generated code.

**Light/dark parity requires manual review.** Even with both values defined in your tokens, occasionally Claude picks a combination that works in light but fails WCAG contrast in dark. I verified this on the paywall — the trust logo row passed 4.5:1 contrast in light mode but sat at 3.2:1 in dark. Run a contrast check before shipping; [the 2026 baseline is still 4.5:1 for body text](https://muz.li/blog/dark-mode-design-systems-a-complete-guide-to-patterns-tokens-and-hierarchy/).

If I listed these failures without the context of how much better the workflow is *with* the structure, I'd make it sound worse than it is. So let me flip it.

## What Actually Changes: First-Pass Quality And Cleanup Time

Here's the real delta, observed across my own projects over the last eight weeks. I'm not giving you invented percentages because I don't track this in a database. What I do track is how many screens get shipped without significant rework, and the pattern is consistent enough that I trust it.

**Before this workflow:** first-pass output from vanilla prompting needed structural rebuilds most of the time. Components were wrong, hierarchy was off, half the tokens weren't even applied. Realistic path to a shippable screen was 45–90 minutes of cleanup per screen, and I usually rebuilt significant portions by hand.

**After this workflow:** first-pass output is structurally correct. Tokens are applied. Components are right. Layout hierarchy is close to final. Cleanup is mostly text styles, one or two spacing tweaks, and a contrast audit. Realistic path to a shippable screen is 10–15 minutes of cleanup per screen, and I'm editing rather than rebuilding.

The compounding win is consistency. A team member who follows this same setup produces screens that look like they came out of the same system, because they *did*. Before structured skills, AI-generated screens had a kind of entropy — every generation drifted slightly. After structured skills, the drift is bounded by the system description itself.

For teams working with design systems, this pattern mirrors what Figma documented in their [AI-to-Figma workflow for design systems](https://www.figma.com/blog/introducing-claude-code-to-figma/) earlier this year — structure the system, then let the agents operate inside the structure. The improvement isn't from smarter models. It's from tighter rails.

There's one more thing worth naming. The hour you spend writing tokens descriptions and component usage rules is an hour that pays back every time you generate a screen. After the fifth or sixth screen, the workflow is net positive by a huge margin. Before the fifth, you're investing. Don't quit at screen two. Quit at screen ten if it's still not working, and by then you'll know exactly which part of the setup needs tightening.

## The Paywall, Finished

The paywall that ate my Saturday in the opening of this post got rebuilt the next morning in about 40 minutes, including the token and component skill work I should have done the first time. The final version used my actual `action-primary-bold` for the CTA, the correct `surface-elevated` for the plan card, the specific `space-4` rhythm for the feature rows. The trust logo divider was there. Light and dark both passed contrast. Text styles needed one pass of cleanup on the Figma side. Shipped.

The lesson isn't that AI design works now. The lesson is that AI design works when you treat your design system as training data and iterate locally before you commit to the canvas. It works when you stop hoping the model will read your mind and start describing what's in your mind so specifically that the model doesn't have to.

Here's what I want you to do today — not next week, not after you've read three more articles. Open your design system. Pick one token category. Surface, or content, or spacing. Write a one-sentence "when to use" description for every token in that category. That's your first step. That's the whole unlock. You do that for one category today, and you'll be further along than most teams. Do it for every category this week, and you'll have a design system that a model can actually apply.

The next paywall you build won't eat your Saturday. It'll eat forty minutes. And the forty minutes will mostly be typography cleanup, which — honestly — you were going to do anyway.

## Frequently Asked Questions

### Do I need the paid Figma MCP server to do this workflow?
No. The core Figma MCP server and the foundational `figma-use` skill are free to install. A paid Figma plan is required for some write operations to shared team libraries, but solo work on draft files runs on the free tier. See the implementation section above for the full setup.

### Will this work with Codex or other non-Claude models?
Partially. The Figma MCP server supports multiple MCP clients including Codex, so the Figma side works. The skill loading pattern is Claude-specific — Codex uses its own extension format. The core idea (structured tokens plus component skills plus reference screens) is model-agnostic and transfers to any capable coding agent.

### How long does it take to set up design token and component skills the first time?
Plan for three to six hours of focused work for a mid-size system, assuming your tokens and components are already documented somewhere. The time cost is almost entirely in writing the "when to use" descriptions — the mechanical skill-file authoring is fast.

### Can Claude create new components if I ask it to?
Yes, but you should forbid it in your skill preamble for production work. Allowing Claude to invent components on the fly breaks system consistency, which is the whole reason you're doing this. If a new component is genuinely needed, design it deliberately in Figma first, then add it to your component skill, then generate with it.

### What's the most common mistake when starting this workflow?
Skipping the "when to use" descriptions on tokens. Teams copy token names and values, think that's enough, and wonder why the output still looks generic. The descriptions are the part that turns a token from data into an instruction. Without them, you're back to vanilla prompting with extra steps.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
