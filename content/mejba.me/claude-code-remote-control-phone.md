**BRAND:** mejba.me
**TITLE:** Claude Code Remote Control: Code From Your Phone Now
**SLUG:** claude-code-remote-control-phone
**TAGS:** Claude Code, AI Development, Developer Tools, Remote Work, Tutorial

---

I was three hours into a refactoring session last week when my dog started doing the thing where he sits by the door and stares at me like I'm personally responsible for his suffering. He needed a walk. My Claude Code agent was mid-task — halfway through restructuring an authentication module across fourteen files. Normally, I'd have two options: abandon the session and lose context, or ignore the dog and feel guilty for the next hour.

Instead, I typed `/remote-control`, scanned a QR code with my phone, and walked out the front door.

From the sidewalk, I watched Claude finish the auth refactor on my phone screen. I approved a file edit while waiting for my dog to investigate a suspicious fire hydrant. I sent a follow-up instruction — "now update the tests to match the new module structure" — while sitting on a park bench. Twenty minutes later, I walked back inside, sat down at my desk, and the terminal session was exactly where I'd left it. Every file change, every conversation turn, fully synced.

This is Claude Code's new Remote Control feature, and it solves a problem I didn't realize was costing me so much until it disappeared. The problem isn't that coding sessions are long. The problem is that coding sessions are *fragile* — tethered to your desk, your terminal window, your physical presence in front of a screen. One interruption breaks the flow, and rebuilding context after a break costs more time than the break itself.

Remote Control cuts that cord. But the way it works is different from what you might expect, and understanding the architecture matters if you want to use it without hitting frustrating limitations. There's a specific mental model for when Remote Control is the right tool versus when Claude Code on the web is better, and I got it wrong the first two times before it clicked.

## What Remote Control Actually Is (And Isn't)

The name is precise. Remote Control doesn't move your session to the cloud. It doesn't spin up a virtual machine somewhere. It doesn't replicate your environment on Anthropic's servers.

Your Claude Code session keeps running on your local machine. Your filesystem, your MCP servers, your project configuration, your tools — everything stays exactly where it is. Remote Control opens a window into that local session from another device. Your phone, a tablet, a browser on a different computer. The window is real-time and bidirectional — you can read and send messages from any connected device — but the actual computation never leaves your machine.

This distinction matters enormously for two reasons.

First, your local environment is available. If your project depends on local MCP servers, custom tool configurations, or filesystem access that doesn't exist in a cloud sandbox, Remote Control preserves all of it. Cloud-based coding environments strip away your local setup. Remote Control doesn't touch it.

Second, your terminal must stay open. Because the session runs locally, closing your terminal or shutting down your machine ends the session. This isn't a "start something and come back tomorrow" feature. It's a "step away from your desk without breaking flow" feature. The distinction is subtle but important, and I'll explain the practical implications when we get to the workflow section.

The connection architecture is clean. Your local Claude Code instance makes outbound HTTPS requests to the Anthropic API — no inbound ports open on your machine, which is significant from a security perspective. When you connect from your phone or browser, the API routes messages between your device and your local session over a streaming connection. All traffic travels over TLS. Multiple short-lived credentials handle authentication, each scoped to a single purpose and expiring independently.

I spent twenty minutes reading the security documentation before trusting this feature with client project access. The model is sound — it's functionally the same transport security as a normal Claude Code session, just with an additional routing layer.

Now that you know what it is, here's how to actually use it. And there's a right way that saves time and a wrong way that wastes it.

## Setting It Up — Two Minutes, Not Twenty

The setup is deliberately minimal, which I appreciate after years of developer tools that require thirty-minute configuration rituals.

**Prerequisites you probably already have:**

You need a Pro or Max plan — API keys don't work with Remote Control. You need to be logged in via `/login` in Claude Code. And you need to have run `claude` in your project directory at least once to accept the workspace trust dialog.

If you've been using Claude Code for any length of time, you already meet all three requirements. If you're brand new, the setup takes under two minutes.

**Starting a fresh Remote Control session:**

Navigate to your project directory and run:

```bash
claude remote-control
```

That's it. The process stays running in your terminal, waiting for remote connections. It displays a session URL and — this is the key quality-of-life detail — you can press spacebar to show a QR code. Point your phone camera at the QR code, and you're connected.

The command supports two useful flags:

- `--verbose` shows detailed connection and session logs, which I use when debugging connection issues
- `--sandbox` / `--no-sandbox` enables or disables filesystem and network sandboxing during the session. Sandboxing is off by default, which makes sense for most development workflows where you need full filesystem access

**Connecting from an existing session (the more common scenario):**

If you're already deep in a Claude Code conversation and want to go mobile, type:

```
/remote-control
```

Or the shorter alias:

```
/rc
```

This is the command I use 90% of the time. It carries over your entire conversation history — every message, every file edit, every tool call — and generates the session URL and QR code on the spot. You don't lose a single line of context.

**Pro tip:** Before running `/remote-control`, use `/rename` to give your session a descriptive name. "Auth refactor - Feb 2026" is much easier to find in the session list across devices than "Remote Control session," which is the default name when there's no conversation history.

**Connecting from your phone or another device:**

Three options, pick whichever fits your situation:

1. **Scan the QR code** — fastest path from terminal to phone. Opens directly in the Claude app if you have it installed.
2. **Open the session URL** — displayed in your terminal alongside the QR code. Works in any browser.
3. **Find it in the session list** — open claude.ai/code or the Claude app and look for your session by name. Remote Control sessions show a computer icon with a green status dot when they're online and connected.

If you don't have the Claude mobile app yet, the `/mobile` command inside Claude Code displays a download QR code for iOS or Android. Nice touch.

**Always-on mode (for the committed):**

If you want every Claude Code session to be remotely accessible without explicitly running `/remote-control` each time, run `/config` inside Claude Code and set "Enable Remote Control for all sessions" to `true`. I've been running with this enabled for a week and haven't looked back. The overhead is negligible, and the optionality is worth it.

The setup is simple. The workflow design is where it gets interesting — and where most people will either love this feature or underuse it.

## The Workflows That Actually Matter

After two weeks of daily use, I've found three patterns where Remote Control genuinely changes how I work, and two situations where it's the wrong tool.

**Pattern 1: The Walk-Away Workflow**

This is the obvious one, and it's the one from my opening story. You're in the middle of a complex task. Life interrupts — a dog, a delivery, a meeting, lunch, a human being who wants to talk to you about something that isn't code.

Before Remote Control, you had two bad options: pause the AI (losing momentum and potentially context) or ignore the interruption (losing your humanity, slowly).

Now you type `/rc`, scan the QR code, and walk away. The session continues. You can passively monitor progress, approve file changes, or send follow-up instructions from your phone. When you sit back down, the terminal is fully synced.

I've started structuring my days around this. Kick off a task, go make coffee, review progress from the kitchen. Start a test suite run, walk to a meeting, check results from my phone during a lull. The working sessions are the same length, but they're woven into the rest of my day instead of demanding uninterrupted blocks.

**Pattern 2: The Couch Review**

End of the day. I've been at my desk for hours. I don't want to sit in front of a monitor anymore, but I have a Claude Code session with generated code I need to review before tomorrow.

Remote Control turns this into a couch activity. I connect from my phone, scroll through the conversation and file changes, leave review comments and follow-up instructions, and close my laptop guilt-free. The session waits for me until morning (as long as the terminal stays open — more on that in the limitations section).

This pattern is less about productivity and more about physical comfort and mental sustainability. Reviewing AI-generated code on a phone screen isn't ideal for deep analysis, but it's perfectly adequate for high-level review and directional feedback. The detailed analysis can happen at the desk tomorrow, with a fresh perspective and all the context preserved.

**Pattern 3: The Multi-Device Pair Programming**

This one surprised me. I've started keeping Claude Code running on my desktop while working on a different task on my laptop. When I need to reference something from the Claude session — a code snippet, an architectural decision, a file path — I check it on my phone instead of context-switching between machines.

It sounds minor. It's not. Context switching between windows or screens disrupts attention in a way that glancing at your phone doesn't. The phone becomes a read-only dashboard for the AI session while my primary screen stays focused on whatever I'm actively working on.

**When Remote Control is the wrong tool:**

If you want to kick off a task without any local setup — no project cloned, no environment configured — use Claude Code on the web instead. Remote Control requires a running local session. No local session, no remote control.

If you need to run multiple parallel tasks on a project, Remote Control supports one session at a time per Claude Code instance. For parallel work, Claude Code on the web with cloud infrastructure is the better choice.

I made this mistake on day one. I tried to start a second Remote Control session on the same project while the first was still active. The system handled it gracefully — it asked whether to continue the existing session or start a new one — but I realized the parallel workflow I wanted needed cloud infrastructure, not remote access to a single local session.

The mental model is simple: **Remote Control extends your desk. Claude Code on the web replaces it.** Different tools for different situations.

## What Happens When Things Go Wrong

I've tested the failure modes so you don't have to discover them during a client demo.

**Laptop sleeps:** The session reconnects automatically when your machine wakes up. I tested this by closing my MacBook lid, waiting five minutes, opening it again. The phone session reconnected within about eight seconds. No messages lost, no context dropped. This is the feature's best quality-of-life detail.

**Network drops temporarily:** Same graceful recovery. The session tolerates brief disconnections without any intervention. I tested this by toggling airplane mode on my phone for thirty seconds. Reconnection was seamless.

**Extended network outage:** If your machine is awake but can't reach the network for roughly ten minutes, the session times out and the process exits. You'll need to run `claude remote-control` again to start a new session. The conversation history isn't lost — you can resume where you left off — but the active remote connection needs to be re-established.

**Terminal closes:** Session ends. Period. This is the one non-recoverable scenario. If your terminal process gets killed — accidentally closing the window, a system restart, a crash — the Remote Control session terminates. You can start a new one, but the seamless continuity breaks.

My mitigation: I run Claude Code in a `tmux` session on my development machine. Even if I accidentally close the terminal application, the `tmux` session persists and the Claude Code process stays alive. If you're planning to use Remote Control regularly, I strongly recommend this approach. Five minutes of `tmux` setup saves you from the most common failure mode.

```bash
# Start a tmux session for Claude Code
tmux new -s claude

# Inside tmux, navigate and start remote control
cd /path/to/your/project
claude remote-control

# Detach with Ctrl+B then D — session keeps running
# Reattach later with: tmux attach -t claude
```

**Pro tip:** Set up a simple alias in your `.zshrc` or `.bashrc`:

```bash
alias ccr='tmux new -s claude -d "cd /path/to/project && claude remote-control"'
```

One command, and you have a persistent Remote Control session that survives terminal closures.

## The Security Question You Should Be Asking

Anytime a tool says "access your local filesystem from your phone over the internet," your security instincts should activate. Mine did.

Here's what I verified before using this on client projects:

**No inbound ports.** Your machine only makes outbound HTTPS connections. Nothing listens for incoming traffic. This eliminates the entire category of "someone scans my open ports and finds my coding session" attacks.

**TLS everywhere.** All traffic between your machine and the Anthropic API, and between your phone and the API, travels over TLS. Same encryption standard as your normal Claude Code usage.

**Scoped credentials.** The authentication uses multiple short-lived tokens, each limited to a specific purpose and expiring independently. If one token is compromised, the blast radius is contained.

**Session isolation.** Each Claude Code instance gets its own remote session. No cross-contamination between projects or instances.

Is it as secure as running Claude Code purely locally with no network involvement? No — any networked feature introduces attack surface. But the security model is well-designed for what it does. The risk profile is comparable to using any authenticated cloud service over TLS, which is a bar most of us already clear daily with dozens of other tools.

For client projects with sensitive code, I use the `--sandbox` flag to enable filesystem and network isolation during remote sessions. Belt and suspenders.

## My Actual Daily Setup After Two Weeks

Here's what my workflow looks like now that Remote Control is part of my daily routine:

**Morning:** Open terminal, start `tmux` session, run `claude remote-control` in my primary project directory. The session stays alive all day.

**Work blocks:** Code at my desk using the terminal directly. Claude Code runs as normal — I'm not using it through the remote interface when I'm sitting at my machine.

**Breaks:** Scan the QR code, walk away. Monitor from phone. Approve changes, send quick instructions, keep momentum without staying chained to the screen.

**Context switches:** When I need to pivot to a different task but want the Claude session to continue working, I let it run and check progress from my phone periodically.

**End of day:** Couch review from phone. Leave instructions for tomorrow's first task. Close laptop.

**Next morning:** Reattach `tmux` session. Everything's waiting exactly where I left it.

The cumulative effect after two weeks: I spend roughly 30% less time physically at my desk with zero loss in Claude Code output quality or volume. The breaks aren't productivity losses anymore — they're productivity *features* because I'm not losing session context every time I stand up.

That's the thing about Remote Control that the feature announcement doesn't quite capture. The technical capability — "continue sessions from your phone" — sounds like a convenience. And it is. But the second-order effect is larger. It changes the *shape* of your workday. Coding stops being something that requires continuous presence at a desk and becomes something that flows around the rest of your life.

My dog gets walked on time now. I eat lunch away from my screen. I take calls without anxiety about losing session state. And the code still gets written.

Every developer I know complains about being chained to their desk during long AI coding sessions. The chain just got a lot longer. `/rc`, scan, walk.

Try it tonight. Start a refactoring task you've been putting off. Type `/rc`. Go somewhere that isn't your desk.

You might be surprised how much better the code looks when you review it from a park bench.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
