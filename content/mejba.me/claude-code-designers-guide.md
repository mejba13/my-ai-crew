**BRAND:** mejba.me
**TITLE:** How I Use Claude Code as a Designer's Secret Weapon
**SLUG:** claude-code-designers-guide
**TAGS:** Claude Code, UI Design, Figma Integration, AI Tools, Tutorial

---

I watched a designer friend scroll past a Claude Code demo on Twitter last week. "That's a developer thing," she said, barely glancing at the screen. Thirty seconds of dismissal for something that had completely reshaped how I build interfaces.

She's not alone. Most designers I know treat Claude Code like it's a locked room with a "Developers Only" sign on the door. Terminal commands, file systems, code generation — it all sounds like the opposite of what designers signed up for. And I get it. The gap between dragging elements in Figma and typing commands into a black box feels enormous.

But here's what nobody showed my friend. They didn't show her the moment I typed a single sentence into my terminal and watched a complete SaaS dashboard materialize inside her favorite tool — Figma. They didn't show her the two-way sync that lets you push changes from Claude to Figma, tweak them by hand, then pull those tweaks back into Claude for another round of iteration. They definitely didn't show her Pencil.dev, a tool that wraps all of Claude Code's power in an interface that actually looks like something a designer would use.

I've spent the last few months bridging the gap between Claude Code and design workflows. Some of what I found was genuinely exciting. Some of it was frustrating enough to make me close my laptop. And a few things fell into the category of "why didn't anyone tell me this sooner?"

That last category is what this post is about.

## The Confusion That Keeps Designers Away

Before I touch anything technical, I need to clear up the single biggest misunderstanding I see in design communities. People keep confusing Claude Code with Claude the model. They sound similar. They share a name. But they're fundamentally different things, and mixing them up leads to wildly wrong expectations.

Claude the model — the thing you chat with on claude.ai — is a language model. You type text in, you get text out. It has no idea what's on your computer. It can't open files, create designs, or interact with any tool on your machine. Think of it as a brilliant brain floating in a jar. Smart, but disconnected from the physical world.

Claude Code is the body that brain lives in. When you run Claude Code on your machine — through the terminal, the desktop app, or VS Code — it gets hands. Real hands. It can read your files, write new ones, install packages, talk to APIs, and yes, generate designs that land directly in your Figma workspace. The underlying AI brain is the same (currently Claude Opus 4.6), but the operational shell around it changes everything.

Why does this matter for designers? Because if you've tried chatting with Claude on the web and thought "this can't help me design anything," you were right — about that specific interface. Claude Code is a different animal entirely.

I had a conversation with a UX lead at a mid-size agency last month who told me she'd "tried Claude for design and it didn't work." When I asked what she'd done, she described copying Figma component specs into the chat window and asking for design suggestions. That's like trying to use Photoshop by describing your edits in an email. The tool wasn't wrong — the interface was.

Here's the breakdown that finally made it click for me:

| Feature | Claude Model (Web/API) | Claude Code |
|---|---|---|
| File system access | None | Full read/write |
| Figma integration | None | Two-way sync via MCP |
| Design generation | Text descriptions only | Actual visual output |
| System interaction | Isolated sandbox | Full machine access |
| Runs where | Browser or API | Terminal, Desktop App, VS Code |

Once this distinction sank in, everything about Claude Code for design started making sense. But understanding the concept is one thing — setting it up is where most designers hit their first wall.

## Getting Claude Code Running (Without Losing Your Mind)

I'm going to be honest about something most tutorials skip. The installation process for Claude Code is not hard, but it is unfamiliar. If you've never opened a terminal before, the first two minutes will feel uncomfortable. That discomfort goes away fast — I've watched designers who'd never touched a command line get fully set up in under ten minutes — but I'm not going to pretend it doesn't exist.

Here's the step-by-step for Mac. Windows and Linux follow a similar pattern with different commands.

**Step 1: Open your terminal.**

Not your browser. Not VS Code (yet). The actual Terminal app on your Mac. Hit Cmd+Space, type "Terminal," and open the first result. You'll see a text-based window with a blinking cursor. This is where the magic starts.

**Step 2: Install Claude Code.**

Copy the installation command from Claude's official documentation for your OS. Paste it into the terminal and hit Enter. The installer handles everything — dependencies, paths, configurations. You'll see some text scroll by. Let it finish.

**Pro tip:** If you see red error text during installation, don't panic. Take a screenshot and paste it into any AI chat tool (ironically, even Claude's web interface works here). Nine times out of ten, the fix is a single missing dependency or a permissions issue. I've seen designers solve their own installation errors faster than some junior devs I've worked with.

**Step 3: Launch Claude Code.**

Once installed, type `claude` in your terminal and press Enter. That's it. You're now inside a Claude Code session. The terminal becomes your conversation window — you type prompts, Claude responds and takes actions.

**Step 4: Install the Figma MCP plugin.**

This is the part that transforms Claude Code from a developer tool into a designer tool. MCP — which stands for Model Context Protocol — is the bridge between Claude Code and external tools like Figma. Copy the MCP plugin installation command from the documentation and run it inside your active Claude Code session.

**Step 5: Authenticate your Figma account.**

After installing the MCP plugin, Claude Code will prompt you to connect your Figma account. Your browser will open with a permissions dialog. Click "Allow." Make sure you're logged into the correct Figma account — I learned this the hard way when I accidentally connected my personal account instead of my agency one and spent twenty minutes wondering why the team file wasn't showing up.

That five-step process is the entire setup. No Docker containers. No environment variables. No configuration files to hand-edit. And once it's done, you never have to do it again.

But the setup is just the prerequisite. What happens next is where things get genuinely interesting for designers — and where I discovered some capabilities that changed how I approach early-stage design work.

## The Two-Way Figma Sync That Changed My Workflow

The first time I tested the Figma MCP integration, I sent Claude Code this prompt:

> "Create a black and purple dashboard for a modern SaaS analytics platform in this Figma file."

I pasted the URL to a blank Figma file. Then I waited.

Within about ninety seconds, Claude Code generated a full dashboard layout — sidebar navigation, metric cards, a chart area, data tables, user avatars — and pushed it directly into my Figma file. I switched to my Figma tab and there it was. Not a screenshot. Not a static image. Actual Figma layers and components that I could select, move, resize, and edit.

My first reaction was excitement. My second reaction — about ten minutes later — was more nuanced.

The generated design was impressive as a starting point. The layout logic was sound, the color palette matched my prompt, and the component hierarchy made sense. But it wasn't production-ready. Some elements were oddly sized. A few text labels had emoji characters I didn't ask for. The responsive behavior was nonexistent — everything was positioned for one specific viewport.

This is where the two-way sync becomes powerful. Instead of throwing the whole thing away and re-prompting from scratch, I did what any designer would do: I opened the Figma file and started fixing things by hand. I removed the unwanted emojis. I adjusted spacing. I regrouped some components that were awkwardly nested.

Then — and this is the part that made me sit up straight — I copied the URL of my updated Figma file, went back to the Claude Code terminal, and said: "I've made some changes. Pull the updated design from this Figma file and refine the data visualization section."

Claude Code pulled my changes, recognized what I'd modified, and iterated on the design while preserving my manual edits. Two-way sync. Real collaboration between a human designer and an AI agent, with Figma as the shared canvas.

Here's the workflow I've settled into after a month of experimentation:

1. **Generate** — Send Claude Code a detailed prompt with a blank or partially designed Figma file URL.
2. **Review** — Open Figma, inspect what was generated. Identify what works and what doesn't.
3. **Refine by hand** — Fix the obvious issues yourself. You're a designer — this is your superpower.
4. **Push back** — Send the updated Figma URL back to Claude Code with specific instructions for the next iteration.
5. **Repeat** — Keep this loop going until the design meets your standard.

What makes this powerful isn't any single step. A lot of tools can generate designs from prompts. The power is in the loop — the fact that Claude Code treats Figma as a living document rather than a one-shot export target.

I should mention something most promotional content about this workflow conveniently omits. The generated designs need real work. Responsiveness is a known weakness — Claude Code tends to design for a single viewport and doesn't automatically create responsive variants. Layout logic sometimes breaks in unexpected ways, especially on smaller screens. If you're working on a laptop with limited screen real estate, the results will be worse than on a larger display. I've gotten noticeably better outputs when working on my 27-inch monitor versus my MacBook screen.

These aren't dealbreakers. They're the same kinds of issues you'd deal with when iterating on any early-stage design. The difference is that your starting point — the thing you're iterating on — appeared in sixty seconds instead of six hours.

## You Don't Actually Need the Terminal (But It Helps to Know It)

I know what some designers are thinking right now. "This all sounds great, but I really don't want to type commands into a terminal every day."

Fair point. And honestly? You don't have to.

Claude Code ships with a desktop application that does everything the terminal does, wrapped in a proper GUI. Same underlying engine. Same Figma integration. Same capabilities. Just a different interface — one with buttons and a text input field instead of a command prompt.

I tested both extensively. The desktop app is a perfectly valid way to work, and for designers who want to minimize their exposure to terminal-based workflows, it's the right choice. I've used it to generate everything from About Us page layouts to full e-commerce product pages, and the results were indistinguishable from what I got through the terminal.

That said, I'd still recommend spending an hour learning the terminal basics. Not because the desktop app is inferior — it isn't — but because terminal literacy unlocks a whole universe of adjacent tools. MCP plugins, custom scripts, automated workflows, CI/CD pipelines that run Claude Code as part of a deployment process — all of these are terminal-first. You don't need them now. But six months from now, when AI-assisted design tools are even more deeply embedded in professional workflows, you'll be glad you know your way around a command line.

Think of it like learning basic HTML as a designer. You don't need it for most of your daily work. But the designers who understand it have a permanent advantage over those who don't.

## Pencil.dev: When You Want the Friendliest Path

Here's where things get interesting for designers who want AI design generation but really, truly, absolutely do not want to deal with terminals or desktop apps that feel like developer tools.

Pencil.dev is a third-party platform that puts a designer-friendly interface on top of Claude Code's engine. You connect it to your Claude subscription, and instead of a terminal prompt, you get a visual workspace where you can type design requests and watch components materialize in real time on a live canvas.

I spent a week with Pencil and came away with mixed but mostly positive feelings.

What it does well: rapid prototyping. I could type "Create a pricing page with three tiers, dark theme, rounded cards" and watch the design build itself piece by piece on the left side of the screen. Pencil also includes pre-built UI kits and design systems, so you can apply consistent styling without describing every detail in your prompt. For brainstorming sessions, mood boarding, or generating quick concepts to present to a client, it's genuinely fast and surprisingly capable.

What it doesn't do: export to Figma. As of right now, Pencil exists in its own ecosystem. You can design, iterate, and refine within the platform, but there's no direct sync to push your work into a Figma file. For designers whose entire workflow lives in Figma, this is a significant limitation. You'd need to manually recreate anything you want to keep, which defeats part of the purpose.

My recommendation? Use Pencil for exploration and early ideation — the "what if we tried this" phase where speed matters more than fidelity. Then switch to the Claude Code + Figma MCP workflow when you're ready to build something you'll actually hand off to developers or present to clients.

Here's a quick comparison of all three approaches:

| Approach | Best For | Figma Sync | Learning Curve | Speed |
|---|---|---|---|---|
| Claude Code (Terminal) | Full control, advanced workflows | Yes (two-way) | Moderate | Fast |
| Claude Code (Desktop App) | Terminal-averse designers | Yes (two-way) | Low | Fast |
| Pencil.dev | Rapid prototyping, ideation | No | Very low | Very fast |

There's no single "right" choice here. I use all three depending on the task, and I suspect most designers will eventually settle into a similar pattern. The important thing is that the on-ramp exists at every comfort level.

## What Most "Claude Code for Design" Posts Won't Tell You

I want to spend some time on the uncomfortable stuff because I think honesty here matters more than hype.

**The polished demos are misleading.** Every Claude Code showcase you've seen on social media represents someone's best result after multiple iterations. The first output is almost never what gets posted. I've had prompts produce layouts with overlapping elements, invisible text on same-colored backgrounds, and component hierarchies so tangled that ungrouping them in Figma created more problems than it solved. These aren't rare edge cases — they're part of the normal workflow.

**Responsiveness is a real weakness.** I mentioned this earlier, but it bears repeating. Claude Code designs for a single viewport by default. Creating responsive variants requires explicit, detailed prompts — and even then, the results often need significant manual adjustment. If responsive design is core to your project (and when isn't it?), plan to do substantial manual work after the AI generation step.

**Your prompting skill matters enormously.** The gap between a vague prompt and a detailed one isn't linear — it's exponential. "Make me a dashboard" will give you something generic and bland. "Create a dark-themed analytics dashboard with a collapsible sidebar, four KPI cards at the top showing MRR, churn rate, active users, and NPS score, a line chart with 6-month trend data below, and a sortable table of recent transactions at the bottom" will give you something remarkably close to what you imagined. The investment in learning to write detailed, specific prompts pays dividends on literally every design task.

**This doesn't replace design thinking.** Claude Code can execute visual layouts, but it can't do user research. It can't interview stakeholders. It can't understand why a particular flow feels confusing or why users keep dropping off at step three of your onboarding. The design judgment, the strategic thinking, the empathy for users — that's still entirely your domain. Claude Code is a production tool, not a strategy tool.

I used to think this last point was obvious. Then I saw a LinkedIn post from a startup founder claiming he'd "replaced his design team with AI." His app's UX was, predictably, terrible. Beautiful components arranged in patterns that made no sense to actual humans. Decoration without design. Don't be that person.

The honest assessment is this: Claude Code makes the production part of design dramatically faster while leaving the thinking part entirely in your hands. If you're strong on design thinking and weak on production speed, Claude Code is genuinely transformative. If you're hoping AI will do the thinking for you, you'll just produce bad designs faster.

## What I've Actually Built With This Workflow

Numbers and theory are fine, but I know what convinced me — seeing real outputs. So here's what my Claude Code + Figma workflow has actually produced over the past month.

**SaaS dashboards:** I've generated initial dashboard layouts for three client projects. Average time from prompt to first Figma draft: about two minutes. Average time to refine that draft into something I'd present to a client: about forty-five minutes. Compare that to my previous workflow where a first draft took two to three hours. The math works out to roughly a 60% time reduction on initial design phases.

**Landing pages:** I've used Claude Code to generate above-the-fold hero sections, feature grids, and pricing tables. The hero sections tend to be the strongest outputs — clean layouts with good visual hierarchy. Feature grids need more iteration. Pricing tables are surprisingly good out of the box.

**Component exploration:** This is my favorite use case, and it's one I didn't expect. When I'm trying to decide how a particular UI element should look, I'll rapid-fire five or six prompts at Claude Code with different stylistic directions. "Show me this card with glassmorphism." "Now try it with a brutalist approach." "What about a soft neumorphic style?" Getting five different stylistic explorations in three minutes is wildly more efficient than mocking each one manually.

**Design system bootstrapping:** I used Claude Code to generate an initial component library for a new project — buttons, inputs, cards, modals, navigation patterns — all following a specified color palette and type scale. It gave me about 70% of what I needed. The remaining 30% required manual refinement for consistency and edge cases. But starting from 70% instead of zero? That's a significant workflow improvement.

The realistic timeframe for getting comfortable with this workflow: about two weeks of regular use. The first few days feel clunky as you learn what makes a good prompt versus a mediocre one. By the end of the first week, you'll have a library of prompt patterns that consistently produce useful results. By week two, the tool disappears into your workflow — you stop thinking about "using Claude Code" and start thinking about "designing."

Quick wins come fast. A landing page concept in minutes rather than hours. A component variation explored in seconds rather than built from scratch. These early wins keep you motivated through the learning curve.

The long-term gains are bigger. Once you've internalized the prompting patterns and the two-way sync workflow, your throughput on production design work increases substantially. I'm consistently shipping initial design concepts 50-60% faster than I was six months ago. The quality bar hasn't dropped — I'm just spending less time on the mechanical parts and more time on the decisions that actually matter.

## Where This Is All Heading

Something I keep thinking about — and I don't have a clean answer for yet — is what design workflows look like in twelve months. The Figma MCP integration is relatively new. Pencil.dev is early-stage. Claude Code itself gets major capability updates roughly every quarter. The tools I'm describing today are the worst they'll ever be.

I suspect the terminal step will disappear within a year. Not because terminals are bad, but because the Figma plugin ecosystem will catch up. I can easily imagine a world where Claude Code is just another panel inside Figma — you select a frame, type a prompt, and the AI generates or modifies the contents. No terminal. No MCP setup. No context switching.

For designers who start learning these tools now, that transition will feel natural. For designers who wait until the "easy" version arrives, they'll be starting from zero while their peers are already on iteration six.

That's the real reason I'm writing this. Not because Claude Code is perfect today — it isn't. Not because every designer needs to become a terminal expert — they don't. But because the designers who understand AI-assisted workflows right now are building a skill gap that compounds every month.

My friend who scrolled past that Claude Code demo? I showed her the Figma integration the next day. She spent three hours that evening learning the basics. Last week, she used it to pitch a client concept that she'd prototyped in fifteen minutes. The client approved it on the spot.

She doesn't call it a "developer thing" anymore.

So here's my challenge. Pick one of the three approaches I described — terminal, desktop app, or Pencil.dev — and spend thirty minutes with it this week. Just thirty minutes. Generate one design. See what happens. Because the worst outcome of trying is that you lost half an hour. And the best outcome is that you fundamentally change how you work.

What's your thirty minutes worth?

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
