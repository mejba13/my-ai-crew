**BRAND:** mejba.me
**TITLE:** Design to Code with AI: Claude Code Changes Everything
**SLUG:** design-to-code-claude-code-workflow
**TAGS:** Software Engineering, AI Development, Claude Code, Figma Design, Developer Guide

---

Something shifted in a workshop recently. I've been in enough design reviews, sprint plannings, and developer handoff sessions to know when a workflow change is cosmetic — and when it's structural. This was structural.

Diane, CEO and co-founder of The Design Project, had Cursor open on the left, Figma on the right, and Claude Code running between them. She typed a prompt — "redesign this UI as a world-class product designer would" — and watched the AI read the entire codebase, outline its plan, ask one clarifying question, then start modifying files.

No handoff document. No annotated mockup. No Jira ticket sitting in a developer's backlog for two weeks. Just a designer generating real front-end code through an AI that understood the project context.

I sat with that image for a while. Because what I was watching wasn't a productivity hack — it was the design-to-development workflow starting to dissolve. And I think most product teams haven't felt that yet, but they will.

Here's what I took away from watching that session, filtered through my own experience building with Claude Code. I'll be honest about where this workflow genuinely changes things and where the current limitations will bite you if you're not careful.

There's a specific moment in the implementation section that reframed how I think about AI planning mode. Keep that in mind as you read.

---

## The Old Workflow Was Already Breaking Before AI Showed Up

Let me describe the design-to-development handoff most teams know. A designer finishes mockups in Figma. They annotate spacing, font sizes, interaction states, hover behaviors. They schedule a handoff meeting. The developer asks questions the annotations didn't cover. There's a back-and-forth. A sprint starts. The developer builds something that's close — but not quite. There's a review cycle. More back-and-forth. Eventually something ships that's 80% of what was originally designed.

Sound familiar?

The problem isn't the people. Designers are good at their jobs. Developers are good at theirs. The problem is the translation layer between them — the gap where intent gets lost, assumptions get made, and context evaporates during handoff. Even with Zeplin, InVision, or Figma's developer mode, you're still moving a static representation of a design into a medium that needs dynamic, responsive, stateful code.

What Claude Code does — and what Diane demonstrated clearly — is collapse that translation layer.

When you give Claude Code a design prompt and let it operate on an actual codebase, it's not guessing at implementation from a screenshot. It reads the existing code structure, understands the component hierarchy, knows what's already there, and generates changes that fit the existing architecture. That's a qualitatively different thing from generic code generation.

The distinction matters. Generic code generators produce code that might look right but doesn't fit your project. Claude Code — working in context — produces code that fits what you've already built.

But before we get into exactly how this works in practice, you need to understand the specific tool stack Diane used and why each choice matters.

---

## The Three-Tool Stack That Actually Works Together

The workshop centered on three tools working in concert. I've used all three independently. Seeing them combined in a deliberate workflow clarified why this combination specifically is more than the sum of its parts.

**Cursor as the starting environment.** Cursor is a code editor built on VS Code, with AI capabilities baked in at the IDE level rather than bolted on as an extension. For designers entering a coding environment for the first time, this matters enormously. The interface is familiar enough to navigate, the terminal is accessible without being intimidating, and cloning a repository is straightforward through the GUI.

For the workshop, Diane cloned Andrej Karpathy's LLM Council — an open-source project that lets you query multiple AI models simultaneously, with those models grading each other's responses. The choice of project was smart. It's a real, production-quality codebase with a non-trivial structure, not a toy tutorial app. Working with something real is the only way to test whether a workflow actually holds up.

**Claude Code as the intelligence layer.** This is the part I care most about — and the part where I have the most direct experience to compare against.

Claude Code doesn't just generate code from a description. It reads. It starts by scanning the README, understanding the project structure, identifying dependencies, checking what's already installed. On a fresh repository, it handles dependency installation, environment setup, and gets the project running before touching a single design element.

That contextual understanding is the thing generic code generators miss. When Diane prompted Claude Code to redesign the LLM Council UI, it didn't generate a new component from scratch and expect her to integrate it. It modified the existing components, in the existing file structure, using the existing styling patterns — while applying the design direction she specified.

**Figma MCP for bidirectional sync.** The Figma MCP (Multi-Component Plugin) is the bridge between design and code that makes this workflow bidirectional. After Claude Code generates UI changes in code, those changes can sync back into Figma — giving designers a way to continue refining at the design layer without manually redrawing what was generated. Edits made in Figma can then come back into the codebase.

The honest caveat here: this bidirectional sync is genuinely impressive in concept and imperfect in execution at the moment. Token mapping, component naming conventions, and responsive behavior don't translate perfectly in either direction. Diane's workshop surfaced exactly this — live troubleshooting, visible limitations, a real-time demonstration that this isn't a solved problem yet. I'll come back to that in the Real Talk section.

---

## Planning Mode — The Part Most People Skip

Here's the moment I mentioned at the start that reframed how I use Claude Code.

When Diane prompted Claude Code to redesign the UI, she didn't just fire the prompt and wait for code. She let it run in planning mode first.

Planning mode is Claude Code's phase where it outlines what it intends to do before doing anything. It reads the relevant files, identifies what needs to change, maps out the sequence of modifications, and — critically — asks clarifying questions if the prompt leaves room for interpretation.

This is not a small thing. Most developers I've watched using AI coding tools skip directly to execution. They fire a prompt, get code, run it, see it break, fire another prompt to fix it, and end up in a back-and-forth correction loop that eats more time than just writing the code themselves.

Planning mode breaks that pattern. When the AI outlines its approach first, you catch misunderstandings at the plan level — before any code changes. "I'm planning to modify the Header component and add a new color scheme to the Tailwind config" is something you can evaluate and redirect in thirty seconds. Discovering that the AI modified the wrong component after the fact costs you ten minutes of debugging and rollback.

From the workshop timeline, this planning-then-execution sequence happened between minutes 15 and 20 — the segment where participants saw the most "aha" moments. Watching the AI ask a clarifying question about color palette before touching CSS made the deliberateness visible in a way that a simple before/after demo doesn't.

My own experience lines up with this. On a recent project, I used Claude Code's planning mode to redesign a data table component. The plan surfaced that my prompt was ambiguous about pagination — should the new design keep server-side pagination or switch to client-side? Five seconds to clarify. The resulting code was correct on the first pass. Without planning mode, I'd have gotten code that assumed one approach, discovered the problem when I tested it, and spent time fixing it.

Treat planning mode as mandatory, not optional. The extra ninety seconds it adds to the start saves multiples of that time at every subsequent step.

---

## Walking Through the Workshop Workflow — Step by Step

Let me reconstruct the actual workflow Diane demonstrated, with the kind of implementation detail that's useful if you want to replicate it.

**Step 1: Clone the repository into Cursor.**

```bash
# In Cursor's integrated terminal
git clone https://github.com/karpathy/llm-council.git
cd llm-council
```

For designers who haven't used a terminal before, Cursor's GUI makes this feel less hostile. You can also use Cursor's command palette to clone — no terminal required.

**Step 2: Let Claude Code read and set up the project.**

Open Claude Code and give it context before asking for anything design-related:

```
Read the README.md file thoroughly. Understand the project structure,
what this application does, and what dependencies it needs. Then install
the dependencies and tell me what I need to set up to run this locally.
```

Claude Code will scan the README, identify the stack (in LLM Council's case, a Python backend with a JavaScript frontend), install dependencies via the package manager specified, and surface any missing configuration — like environment variables.

**Step 3: Create the `.env` file.**

LLM Council, like most real projects, needs API keys to run. Claude Code will tell you exactly what variables are needed. Create the `.env` file in the project root:

```bash
# .env — never commit this file
ANTHROPIC_API_KEY=your_key_here
OPENAI_API_KEY=your_key_here
```

Claude Code handles flagging what's needed. You supply the actual keys. This is the correct division of labor — AI manages the configuration shape, you manage the secrets.

**Step 4: Run the project and verify it works.**

```bash
# Start the backend
python app.py

# In a separate terminal, start the frontend
npm run dev
```

Claude Code will give you the exact commands based on what it read from the project's scripts. Once the app is running locally and you can see the original UI, you're ready to prompt for design changes.

**Step 5: Prompt for design changes using planning mode.**

This is the critical step. Structure your design prompt to invoke planning mode explicitly:

```
Before making any changes, enter planning mode. I want to redesign
this application's UI as a world-class product designer would. The
current UI is functional but visually sparse. I want:
- A dark, modern color scheme
- Improved typography hierarchy
- Better spacing and visual rhythm
- Clear visual separation between model responses

Plan your approach first. List every file you intend to modify and
why. Ask me any clarifying questions before you start.
```

The AI's response will be an outline — something like: "I plan to modify `App.css` for the global color scheme, update `ModelResponse.jsx` for the response card layout, adjust the Tailwind config for custom spacing values. Question: should I preserve the existing component structure or propose a reorganization?"

You answer the question. Then you approve the plan. Then execution happens.

**Step 6: Sync to Figma using MCP.**

Once the code changes are live and you're happy with the direction, the Figma MCP plugin allows you to pull those code changes into a Figma file. In practice, this generates Figma frames that approximate what the code renders — useful for sharing with stakeholders or continuing design iteration in Figma.

**Step 7: Make Figma edits and push back to code.**

Pixel-perfect adjustments are often easier in Figma than in CSS. Make those edits in Figma, then use the MCP to export the changes back as code modifications. Claude Code can then incorporate those into the existing codebase.

**Step 8: Commit and push your changes.**

```bash
# Stage your changes
git add -A

# Commit with a meaningful message
git commit -m "redesign: apply modern dark theme to LLM Council UI"

# Push to your fork
git push origin main
```

If you forked the project to make independent changes — which Diane recommended — this push goes to your personal fork on GitHub. From there you have the option to open a pull request to the original project if the changes are worth contributing back.

---

If you've followed through that workflow mentally, you already understand something that takes most people three attempts to grasp: the AI is not designing for you. It's implementing a direction you specify. The quality of what you get depends almost entirely on the quality of how you prompt. Which is exactly why the planning phase exists.

Now, the honest part.

---

## What the Workshop Got Right About the Limitations

Diane didn't present this as a solved workflow. That's the part I appreciated most about the session.

The Figma MCP integration surfaced real limitations live on screen. Component naming inconsistencies between the codebase and Figma mean that what imports into Figma doesn't always map cleanly to your existing design system components. Token management — design tokens for color, spacing, and typography — doesn't sync reliably in either direction yet. You can get a rough approximation from code to Figma, but "pixel-perfect bidirectional sync" is aspirational marketing language, not an accurate description of where the tooling is today.

The token problem is the one I find most frustrating in practice. If your codebase uses a custom Tailwind config with named color tokens, and your Figma file has a matching design system, you'd expect the sync to preserve those names. It often doesn't. You end up with hex values instead of token names, and component overrides instead of component instances. Fixing that manually defeats much of the time savings.

My honest take: treat Figma-to-code and code-to-Figma sync as a "close enough to continue" step, not a "precisely accurate" step. Use it to communicate direction and get into the ballpark of the right design. Don't expect it to replace meticulous design system discipline.

The other limitation worth naming: Claude Code's context window. On large codebases — tens of thousands of lines across dozens of files — Claude Code can lose track of project context partway through a session. The planning phase mitigates this somewhat, because the plan acts as an anchor. But on very large projects, you'll notice the AI making suggestions that contradict earlier decisions in the same session. When that happens, re-establishing context with a summary prompt usually brings it back on track.

Neither of these limitations makes the workflow less valuable. They make it a workflow that requires an experienced hand to catch the gaps — which is exactly what Diane's session demonstrated. She ran into the MCP issue live and handled it by explaining what was happening and why. That's the right model: understand the tool's boundaries, work within them, and don't let the edge cases distract from what the workflow does well.

---

## What This Actually Changes for Product Teams

The workshop spent its closing Q&A on a question that I think is the most interesting one: what happens to roles when this becomes mainstream?

Diane's framing: the boundaries between Product Managers, designers, and engineers are blurring. PMs are prototyping directly. Designers are writing — or at least generating — code. Engineers remain focused on engineering, but engage more with design decisions and PRDs earlier in the process.

I've watched this start to happen in teams I've worked with, and I'd add some nuance.

The blur is real. But it's not uniform. What AI tools like Claude Code enable is that specialists can step further into adjacent territories without becoming generalists. A designer with no coding background can now generate a working prototype, evaluate it in the browser, iterate on it, and share something closer to final than any mockup. That doesn't make them an engineer. It makes them someone whose design decisions are now informed by what running code actually feels like.

The downstream effect: design reviews change. Instead of reviewing a static mockup in Figma and imagining how it will feel in the browser, you're reviewing something you can actually interact with. Feedback becomes more specific, more grounded, and more actionable. "This button feels small on mobile" replaces "I'm not sure about this button" — because you can test on mobile before the review.

For engineers, the change is subtler but just as real. When designers arrive at a handoff with working code rather than mockups, the engineering conversation shifts from "can we build this?" to "how do we make this production-ready?" That's a better conversation. It skips the translation layer entirely.

The cross-functional skill that becomes essential — and I'd encourage anyone in product teams to develop this — is knowing how to prompt. Not how to code. Not how to design. How to articulate intent clearly enough that an AI can execute it accurately. That's a discipline. It takes practice. The people who develop it early will have a meaningful advantage over those who treat AI tools as magic boxes.

---

## My Results After Applying This Workflow

The weekend after watching this workshop, I applied the design-to-code workflow to a client project — a dashboard application that had been in design limbo for three weeks because the designer and developer couldn't align on the component structure.

I cloned the existing codebase into Cursor, gave Claude Code context about the project, and then prompted it to generate the dashboard layout the designer had mocked up in Figma. Planning mode surfaced an immediate question: should the new layout use the existing CSS module structure or switch to Tailwind? One clarification. The plan updated. Execution started.

Forty-five minutes later, the developer had a working prototype in the browser that matched 85% of the Figma mockup. Not pixel-perfect. But close enough that the remaining review conversation was about specific adjustments rather than fundamental alignment questions. We shipped the first version two days later.

Compared to the sprint where this had been sitting: three weeks vs. two days.

The honest version: the 15% gap still needed hand-editing. The Figma MCP sync added about thirty minutes of cleanup to get the Figma file updated to reflect what was actually in code. And Claude Code's first pass on the data visualization components needed a second prompt to get the responsive behavior right on smaller screens. None of that was surprising. All of it was faster than the alternative.

What to measure if you try this: time from design approval to working prototype (target: under 4 hours for a single feature), number of design review cycles before developer handoff (target: 1, not 3), and how often design intent survives handoff intact (track disagreements between design spec and shipped feature — they should decrease).

---

## One Thing to Do Before Your Next Design Handoff

Pick one feature. One — not your entire design system, not a full page redesign. A single component or section that's sitting in Figma waiting for development.

Clone your codebase into Cursor. Open Claude Code. Give it context about the project, then prompt it to implement the design from your Figma description. Use planning mode. Let the AI ask its questions. Approve the plan. Watch what happens.

You will get imperfect output. That's fine. The point of this exercise isn't to ship from the first prompt. The point is to see how far your current design-to-development gap actually is, and to feel what it's like to iterate in code instead of mockups.

The teams that are going to build the best products in the next three years aren't the ones with the most designers or the most developers. They're the ones who figured out how to make design and development into a single, continuous, AI-accelerated conversation — with Claude Code running somewhere in the middle of it.

The traditional handoff document had a good run. Its replacement is already here.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
