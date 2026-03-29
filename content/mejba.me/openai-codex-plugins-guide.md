**BRAND:** mejba.me
**TITLE:** I Tested OpenAI's New Codex Plugins — Here's the Truth
**SLUG:** openai-codex-plugins-guide
**PRIMARY KEYWORD:** OpenAI Codex plugins
**SECONDARY KEYWORDS:** Codex plugin system, Codex MCP servers, Codex skills and apps
**META DESCRIPTION:** I tested OpenAI's new Codex plugin system — skills, apps, MCP servers, and the Build iOS Apps plugin. Here's what works, what doesn't, and what matters.
**TAGS:** AI Tools, OpenAI Codex, Plugin Systems, Developer Productivity, Deep Dive
**CONTENT TYPE:** Practitioner Review
**CONTENT CLUSTER:** AI Tools & Productivity
**TRANSFORMATION GOAL:** After reading, the reader will understand how Codex plugins work architecturally, know which plugins are worth installing today, and be able to build their own custom plugin wrapping existing skills.

---

# I Tested OpenAI's New Codex Plugins — Here's the Truth

The Slack notification from a developer friend hit me at 11 PM on a Wednesday: "Codex just shipped plugins. There are like 20+ of them. You need to see the Build iOS Apps one."

I was mid-session on a Claude Code project, three agents deep in a parallel workflow, and my first instinct was to ignore it. Another feature drop. Another ecosystem play. I'd get to it over the weekend.

Then I opened the Codex app, typed `/plugins`, and saw what OpenAI had actually built. This wasn't a marketplace bolted onto the side of a coding tool. It was an architecture — skills, third-party app connectors, and MCP servers bundled into installable packages that fundamentally change what Codex can do out of the box. I closed my Claude Code session. This needed my full attention.

I've spent the last two days installing every plugin I could get my hands on, stress-testing the Build iOS Apps plugin against a real project, and pulling apart the manifest files to understand how the system actually works under the hood. What I found is a plugin system that's simultaneously more powerful and more frustrating than I expected.

Here's everything — the architecture that makes it interesting, the practical experience that reveals the gaps, and whether this changes the competitive picture for AI coding tools in 2026.

## Why This Plugin Drop Matters Right Now

OpenAI launched Codex plugins on March 26, 2026, with more than 20 integrations available across the Codex app, CLI, and VS Code extension. That timing isn't accidental. The AI coding tool space has been fracturing — Claude Code has its agent teams and skills system, Cursor has its composer workflows, and every major IDE is shipping AI features. OpenAI needed a way to make Codex sticky.

Plugins are their answer. And it's a smart one, because the real problem with every AI coding assistant isn't the AI itself — it's the integration gap. You can have the most capable model on the planet, but if it can't talk to your project management tool, read your design files, or trigger your build system, you're still copying and pasting between windows like it's 2023.

I've been living in this exact pain for months. My workflow involves Slack for team communication, GitHub for version control, Figma for design references, and Xcode for iOS builds. Before Codex plugins, connecting these required either custom MCP server configurations or a lot of manual context-feeding. The promise of plugins is that someone else has already done that wiring — you just install and go.

The question is whether that promise holds up in practice. Spoiler: it does in some places and absolutely doesn't in others. But the architecture underneath is solid enough that the rough edges feel fixable, not fundamental.

Before I walk you through the specific plugins I tested, you need to understand how the three-layer system actually works — because that architecture determines everything about what plugins can and can't do.

## The Three-Layer Architecture: Skills, Apps, and MCP Servers

Every Codex plugin is a package containing up to three types of components, and understanding the distinction between them saved me hours of confusion during testing. Most coverage of this launch treats "plugins" as a monolithic concept. They're not. The three layers serve completely different purposes.

### Skills: The Brain Layer

Skills are reusable instructions that tell Codex *how* to approach specific kinds of work. They can be as simple as a text prompt — "When scaffolding a React component, always use TypeScript interfaces instead of type aliases" — or as complex as a multi-step build script that manages compilation, testing, and deployment.

What makes skills different from just pasting instructions into your system prompt is persistence and shareability. A skill lives inside the plugin package. Anyone who installs that plugin gets the same skill. And Codex loads relevant skills automatically based on what you're doing, rather than requiring you to remember which instructions apply to which task.

I tested this with the Build iOS Apps plugin's skills, which include an iOS debugger skill and a project scaffolding skill. The debugger skill doesn't just say "help me debug iOS apps" — it includes specific knowledge about Xcode Build MCP integration, common SwiftUI layout issues, and how to interpret Xcode's notoriously cryptic error messages. When I asked Codex to debug a layout rendering issue in a SwiftUI preview, it automatically invoked the debugger skill and walked through the diagnostic process in an order that made sense.

The limitation: skills are only as good as the instructions they contain. A poorly written skill is worse than no skill at all, because Codex will follow bad instructions confidently. There's no validation layer checking whether a skill's advice is actually correct.

### Apps: The Hands Layer

Apps are third-party service connectors — the pieces that let Codex reach out and interact with tools like Slack, GitHub, Notion, Gmail, Google Drive, Box, Figma, Linear, Canva, and others. When you install a plugin that includes an app connector, you're giving Codex the ability to read information from that service and take actions in it.

This is where authentication enters the picture. Each app connector requires its own auth flow, and during my testing, this ranged from seamless (GitHub's OAuth took about ten seconds) to annoying (one connector required me to generate an API token manually, paste it in, and restart the plugin). The auth experience is inconsistent, and I suspect it'll vary significantly depending on which third-party service you're connecting to.

Once authenticated, the app connectors work well. I connected the Slack plugin and asked Codex to summarize a channel where my team had been discussing a deployment issue. It pulled the last 50 messages, identified the relevant technical discussion, and gave me a summary that actually captured the nuance — not just "people talked about deployments" but "the team identified a race condition in the queue worker that surfaces under load above 200 concurrent connections." That level of contextual understanding is genuinely useful.

The Figma connector is another standout. Being able to reference a design file directly from within a coding session — "match the spacing from this Figma frame" — eliminates one of the most annoying context switches in front-end development.

### MCP Servers: The Nervous System

Model Context Protocol servers are the middleware layer that connects Codex to external build tools, databases, monitoring systems, and anything else that needs a persistent, bidirectional connection. If skills are the brain and apps are the hands, MCP servers are the nervous system running underneath everything.

The most important MCP server in the current plugin ecosystem is **Xcode Build MCP**, which ships with the Build iOS Apps plugin. This server manages build-related tasks — listing targets, triggering compilation, capturing build output, and running the app in a simulator. It turns Codex from a code suggestion tool into something closer to a build system operator.

I've been following the MCP standard closely since Anthropic published the specification, and seeing OpenAI adopt it for Codex plugins is significant. It means plugins can leverage the same MCP server infrastructure that Claude Code, Cursor, and other tools are building around. A well-built MCP server works across the ecosystem, not just within one vendor's tool.

The practical implication: if you've already set up MCP servers for another coding tool, some of that configuration might carry over to Codex plugins. I tested this with a custom MCP server I'd built for a database monitoring workflow, and with some manifest adjustments, it worked inside a Codex plugin structure. That portability matters.

## Installing and Managing Plugins: What the Process Actually Looks Like

The installation flow has two paths, and they serve different audiences. Understanding which path to use saved me from a frustrating false start.

### Path 1: The Plugin Directory

Type `/plugins` in the Codex interface and you get a browsable directory of available plugins. Each listing shows the plugin name, a description, what components it includes (skills, apps, MCP servers), and an install button. Click install, complete any authentication requirements, and the plugin is active immediately.

The directory currently includes integrations with:

- **Communication:** Slack (summarize channels, draft replies, post updates)
- **Design:** Figma (reference designs, pull component specs, check spacing)
- **Project Management:** Linear (track issues, update status, reference tickets in code)
- **Documentation:** Notion (pull reference docs, update wikis, search knowledge bases)
- **Storage:** Google Drive (access docs, sheets, and slides), Box (file management)
- **Email:** Gmail (read and manage email within coding context)
- **Code:** GitHub (PR management, issue tracking, code search), Sentry (error monitoring), Hugging Face (model references)
- **Design Tools:** Canva (design asset management)
- **Development Workflows:** Build iOS Apps, Build macOS Apps

Once installed, you invoke a plugin using the `@` symbol — `@slack summarize #deployments` or `@figma show me the header component from the main design file`. This convention is familiar from other tools and works intuitively.

One thing I noticed immediately: there's no versioning information displayed for any plugin. You can't tell when a plugin was last updated, what changed between versions, or whether you're running the latest release. For a system that's supposed to be production-ready, this is a real gap. I've been burned enough times by outdated tool integrations to consider this a meaningful risk.

### Path 2: Local Plugin Builds

For custom plugins — or if you want to modify an existing one — you work with a manifest file and a local skills directory. The manifest is a JSON file that declares what the plugin contains:

```json
{
  "name": "my-custom-plugin",
  "description": "Custom workflow for my iOS development process",
  "skills": [
    {
      "name": "swift-conventions",
      "type": "prompt",
      "content": "When writing Swift code for this project, always use async/await instead of completion handlers. Prefer struct over class unless reference semantics are required. Use SwiftUI previews for every view component."
    }
  ],
  "mcp_servers": [
    {
      "name": "xcode-build",
      "command": "npx",
      "args": ["xcode-build-mcp"]
    }
  ]
}
```

The manifest-based approach is powerful because it lets you combine multiple skills into a single installable package. I already have a set of coding conventions, build scripts, and project-specific instructions scattered across various configuration files. Wrapping them into a plugin means I can install my entire workflow setup with a single command on a new machine — or share it with a team member who's joining a project.

The installation for local plugins involves pointing Codex at your manifest file. It reads the declaration, sets up any MCP servers, loads the skills, and makes everything available through the same `@` invocation pattern.

What's missing from both paths is a proper dependency system. If Plugin A requires a specific MCP server version that conflicts with Plugin B's requirements, you're on your own figuring out the conflict. This hasn't bitten me yet with the current 20+ plugins, but as the ecosystem grows, it will become a problem.

## Testing the Build iOS Apps Plugin: Where Theory Meets Reality

The Build iOS Apps plugin was the one that made me close everything else and pay attention, so I gave it the most thorough test. This plugin bundles several skills — including an iOS debugger and a project scaffolder — with the Xcode Build MCP server to create a workflow for building native iOS applications from within Codex.

### The Setup

I had an existing macOS app — a Markdown presentation tool I'd been working on — and I wanted to test whether the plugin could port it to iOS. This is a real task I'd been procrastinating on for weeks because managing multi-target Xcode projects is one of those chores that's tedious enough to keep pushing to "next sprint."

I created a new Git branch (I've learned the hard way to never let AI tooling operate on my main branch), opened the project in Codex, installed the Build iOS Apps plugin, and asked it to add an iPhone target to my existing Markdown presenter app.

### What Worked

The scaffolding was genuinely impressive. The plugin created an iOS app target, set up the build configuration, established shared code between the macOS and iOS targets, and generated the initial SwiftUI views — all through the Xcode Build MCP integration. I didn't touch Xcode once during the initial setup.

The build-test loop is where the plugin earned its keep. Codex would write code, trigger a build through the MCP server, capture the build output, identify errors, and fix them — all in a continuous cycle. Apple's `xcodebuild` tool can list schemes and run build, test, and archive actions from the terminal, and the MCP server leverages this to keep Codex in an agentic loop instead of requiring me to bounce into the Xcode GUI.

I watched it resolve four consecutive build errors — a missing import, a type mismatch, a deprecated API usage, and a missing entitlement — without my intervention. Each time, it read the error output, identified the cause, applied a fix, and rebuilt. That cycle would have taken me fifteen minutes of Xcode wrangling. Codex handled it in about ninety seconds.

### What Didn't Work

Here's where I have to be honest, because the demos make this look smoother than the reality.

The iPhone app that the plugin produced was functional in the strictest sense — it compiled, launched in the simulator, and displayed slides from my Markdown files. But the UI was rough. Font colors weren't optimized for the iPhone's display characteristics. Slide playback was buggy — transitions would stutter, and occasionally a slide would render with the wrong dimensions. The control elements (next/previous buttons, a progress indicator) were positioned for a macOS window and looked awkward on a phone screen.

Some of these issues stem from the fundamental challenge of porting a desktop app concept to mobile. The plugin's scaffolding skill assumes a certain app architecture, and when your existing app doesn't match those assumptions, the generated code adapts poorly. My Markdown presenter uses custom rendering logic that the scaffolder didn't fully account for.

The debugger skill identified some of these UI issues when I pointed them out, but it couldn't fix all of them autonomously. The font color problem required understanding the relationship between the app's appearance settings and iOS's light/dark mode handling — a nuance the skill's instructions didn't cover deeply enough.

And the rendering bugs in slide playback? Those turned out to be a Core Animation timing issue specific to the way I'd implemented transitions. The debugger skill suggested generic fixes (adjusting animation durations, checking for main thread violations), but the actual fix required understanding my specific implementation. Fair enough — no skill can anticipate every codebase.

### The Verdict on Build iOS Apps

The plugin is a solid starting point but not a finished solution. It handles the tedious setup work — target creation, build configuration, shared code structure — better than any tool I've used. The build loop through Xcode Build MCP is genuinely powerful and saves real time. But the generated iOS code needs significant refinement before it's production-quality.

I'd rate it at about 60-70% of the way to a shippable app. That last 30-40% — the polish, the platform-specific optimizations, the edge cases — still requires human expertise. Calling it "not yet production-ready" is accurate, but calling it useless would be deeply unfair. It compressed what would have been a two-day porting project into a four-hour refinement session.

## Building Your Own Plugin: The Part Nobody's Talking About

Most of the coverage around this launch focuses on the pre-built plugins. That misses the more interesting story: the ability to create custom plugins that wrap your existing workflows into shareable, installable packages.

I decided to build a plugin that combines three things I use constantly: my Swift coding conventions, a build-and-run script for my standard project structure, and an MCP server connection for database state inspection. Before plugins, these lived in separate configuration files and required manual setup on every new project.

### Step 1: Define Your Skills

Start with the skills — the reusable instructions your plugin will provide. I created three:

A **coding conventions** skill that encodes my team's Swift style guide: naming conventions, architecture patterns (MVVM with coordinators), testing requirements (every public function gets a unit test), and SwiftUI-specific guidelines (preview providers for every view, environment objects over singletons).

A **build script** skill that contains the actual shell commands for compiling, testing, and running the app. This isn't a prompt — it's a script that Codex executes through the MCP server. It handles the full build lifecycle: clean, build, run tests, generate coverage reports, and launch the app in the simulator with specific device configurations.

A **debug workflow** skill that defines a systematic approach to common issues: check build logs first, then console output, then memory graph, then instruments traces. This ordering reflects how I actually debug problems after years of iOS development, and encoding it as a skill means Codex follows the same process.

### Step 2: Wire Up the MCP Servers

The manifest file declares which MCP servers the plugin needs. For my plugin, that's the Xcode Build MCP (for build operations) and a custom SQLite MCP server I built for inspecting local database state during development.

```json
{
  "name": "ios-dev-workflow",
  "description": "Complete iOS development workflow with Swift conventions, build automation, and database inspection",
  "skills": [
    {"name": "swift-conventions", "file": "skills/swift-conventions.md"},
    {"name": "build-script", "file": "skills/build-script.sh", "type": "script"},
    {"name": "debug-workflow", "file": "skills/debug-workflow.md"}
  ],
  "mcp_servers": [
    {
      "name": "xcode-build",
      "command": "npx",
      "args": ["xcode-build-mcp"]
    },
    {
      "name": "sqlite-inspector",
      "command": "node",
      "args": ["./mcp-servers/sqlite-inspector/index.js"]
    }
  ]
}
```

### Step 3: Test and Iterate

Install the plugin locally, run it against a real project, and watch where it breaks. My first version had a skill conflict — the coding conventions skill said "always use async/await" while the build script was written with completion handler patterns. Codex followed both instructions and generated confused, hybrid code. Fixing this meant ensuring all skills within a plugin are internally consistent.

The iteration cycle for custom plugins is fast. Edit a skill file, reinstall the plugin, test it. No compilation step, no build process. It's just configuration files and scripts.

### The Power of Composition

What gets me genuinely excited about custom plugins is the composition potential. I can wrap my entire iOS development workflow into one plugin. A colleague working on Android can wrap theirs. We install each other's plugins and instantly inherit the other person's best practices and automation.

This is the same idea behind skills systems in other tools — I've written about how [skills.sh works with Claude Code agents](/content/mejba.me/skills-sh-claude-code-agents) — but Codex's implementation adds the app connector and MCP server layers, making plugins heavier-weight but more capable.

For teams that need to standardize their development process, this is the real value proposition. Not the pre-built Slack integration — your team can set that up in ten minutes regardless. The value is encoding your team's accumulated knowledge into a package that every new team member can install on day one.

If you'd rather have someone build this kind of custom development workflow from scratch, I take on AI tooling and automation engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## How Codex Plugins Stack Up Against the Competition

I can't review Codex plugins in isolation because I'm actively using three other plugin/skill ecosystems. The comparison matters because your choice of AI coding tool increasingly depends on which ecosystem has the integrations you need.

### Codex Plugins vs. Claude Code Skills

Claude Code's skill system is more mature for pure coding workflows. Skills in Claude Code are deeply integrated with the agent model — they can spawn sub-agents, manage context across long sessions, and leverage Claude's strong reasoning for complex architectural decisions. I've been using [Claude Code agent teams](/content/mejba.me/claude-code-agent-swarm-architecture) for multi-file refactoring tasks, and the skill-based coordination is something Codex plugins can't match yet.

Where Codex plugins win is the app connector layer. Claude Code doesn't have native Slack, Figma, or Linear integration in the same bundled format. You can set up MCP servers for those services manually, but the one-click install experience of Codex plugins is meaningfully smoother for teams that want integration without configuration.

### Codex Plugins vs. Cursor Composer

Cursor's approach is different — it focuses on IDE-native integration rather than plugin extensibility. Cursor is excellent at understanding your codebase context and generating code that fits, but it doesn't have a plugin marketplace or a skill-sharing mechanism. If your needs go beyond code generation — project management integration, design file references, build automation — Cursor requires external tooling.

### The Ecosystem Bet

The honest assessment: no single tool's plugin ecosystem is complete yet. I'm using Codex for its Slack and Figma connectors, Claude Code for its agent architecture and reasoning depth, and Cursor for its IDE integration. Consolidating to one tool would mean losing capabilities I use daily.

OpenAI's bet is that the plugin ecosystem will grow fast enough to make Codex the one tool you don't need to leave. That bet depends entirely on whether third-party developers build high-quality plugins — and right now, self-serve plugin publishing isn't available yet. OpenAI has curated the initial 20+ plugins, but the ecosystem needs to open up before it can compete with the breadth of MCP servers available for Claude Code.

## The Gaps You Need to Know About

I've been positive about the architecture, and I want to balance that with the real limitations I encountered. These aren't minor complaints — they're issues that will determine whether plugins become central to your workflow or remain a nice-to-have.

**No plugin versioning.** This is the biggest gap. When a plugin updates, you have no way to know what changed, whether it might break your workflow, or how to roll back to a previous version. For individual developers experimenting, this is annoying. For teams relying on plugins in production workflows, it's a genuine risk.

**Inconsistent authentication.** Some plugins authenticate seamlessly through OAuth. Others require manual token generation and pasting. At least one plugin I tested required restarting Codex after authentication to pick up the new credentials. This needs standardization.

**No dependency management.** Plugins can't declare dependencies on other plugins or specific MCP server versions. As the ecosystem grows and plugins start sharing infrastructure, this will create conflicts.

**Limited error reporting.** When a plugin fails, the error messages are often generic — "plugin execution failed" without details about which component (skill, app, or MCP server) actually broke. Debugging plugin issues requires more trial-and-error than it should.

**macOS-only for the Codex app.** The full plugin experience requires the Codex desktop app, which currently runs only on macOS with Apple Silicon. The CLI and VS Code extension support plugins, but the experience is reduced — you lose the visual plugin directory and some of the authentication flows. Windows alpha testing has started, but Linux support isn't on the horizon yet.

## What This Means for Your Workflow in 2026

The Codex plugin system isn't going to replace your existing tool setup overnight. That's not how developer tooling adoption works. But it does change the decision matrix for teams evaluating AI coding assistants.

If you're already using Codex and your workflow involves Slack, GitHub, Figma, or any of the other integrated services, install the relevant plugins immediately. The time saved on context switching alone justifies the ten minutes of setup.

If you're comparing Codex against Claude Code or Cursor, the plugin system becomes a meaningful differentiator — but only if the specific integrations you need are available. Check the plugin directory before making a decision, not after.

If you're a team lead thinking about standardizing on an AI coding tool, the custom plugin capability is the feature to evaluate most carefully. The ability to encode your team's conventions, build processes, and integration patterns into an installable package is a genuine productivity multiplier. It turns onboarding from "read the wiki and figure out our setup" into "install this plugin and start coding."

And if you're a tool builder or MCP server developer, this is a market signal. OpenAI is betting on the same MCP standard that Anthropic championed. Building for MCP means your integration works across the ecosystem, not just within one vendor's walled garden.

The plugin system launched three days ago. Some of my complaints will be addressed in updates. Others represent architectural decisions that won't change quickly. But the foundation — skills for knowledge, apps for integration, MCP servers for infrastructure — is sound. What gets built on top of it in the next six months will determine whether Codex plugins become essential or merely interesting.

I'm planning to build a custom plugin that wraps my complete AI development workflow — skills from Claude Code, MCP servers I've built for database and monitoring tools, and app connectors for the services my team relies on. When that's ready, I'll share the manifest and walk through the entire build process.

For now, the twenty-plus plugins available at launch are worth installing and testing. Start with the ones that connect to services you already use daily. The Slack plugin alone has already saved me more time than the entire testing process cost.

The AI coding tool war isn't about which model generates the best code anymore. It's about which tool fits most seamlessly into the way you already work. Plugins are OpenAI's strongest move toward winning that war — imperfect today, but architecturally positioned to get much better very fast.

## Frequently Asked Questions

### What are OpenAI Codex plugins?

Codex plugins are installable packages that bundle three types of components — skills (reusable instructions), apps (third-party service connectors), and MCP servers (middleware for build tools and external systems) — into shareable workflows. They launched on March 26, 2026, with 20+ integrations available across the Codex app, CLI, and VS Code extension.

### How do I install Codex plugins?

Type `/plugins` in the Codex app to browse the plugin directory. Select any plugin, complete the authentication flow if required, and it becomes available immediately via the `@` symbol. For custom plugins, create a manifest JSON file declaring your skills and MCP servers, then point Codex at the local file.

### Can I build my own Codex plugins?

Yes. Custom plugins use a manifest file that declares skills (text prompts or scripts), app connectors, and MCP server configurations. No compilation is needed — edit the manifest and skill files, install locally, and test. Self-serve publishing to the official plugin directory is not yet available as of March 2026.

### Does the Build iOS Apps plugin produce production-ready code?

Not yet. The plugin handles project scaffolding, build configuration, and multi-target setup well, and its Xcode Build MCP integration enables powerful build-test loops. But the generated UI code needs significant refinement — expect to spend time polishing layouts, fixing platform-specific rendering issues, and optimizing for iOS display characteristics.

### How do Codex plugins compare to Claude Code skills?

Claude Code skills offer deeper agent coordination and stronger reasoning for complex coding tasks. Codex plugins add third-party app connectors (Slack, Figma, Linear) and a one-click install experience that Claude Code's manual MCP setup can't match. The two systems serve different strengths — neither fully replaces the other as of March 2026.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
