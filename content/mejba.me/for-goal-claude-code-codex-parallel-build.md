**BRAND:** mejba.me
**TITLE:** For/Goal Tested: Claude Code vs Codex Built My App in 32 Min
**META TITLE:** For/Goal in Claude Code and Codex: Parallel Build Test
**SLUG:** for-goal-claude-code-codex-parallel-build
**PRIMARY KEYWORD:** for/goal Claude Code Codex
**META DESCRIPTION:** I gave Claude Code and Codex the same 62-task roadmap and hit /goal. Both finished a full Next.js app in 32 minutes. Here is what each got right and wrong.
**TAGS:** Claude Code, Codex CLI, Autonomous Agents, AI Coding Workflows, Next.js

---

# For/Goal Tested: Claude Code vs Codex Built My App in 32 Min

I started the timer at 2:47 PM on a Wednesday. By 3:19 PM, two terminal windows on my left monitor had each finished scaffolding a working Next.js application — onboarding flow, dashboard, landing page, auth screens, the whole shape of a real product. Sixty-two roadmap tasks. Both apps. Thirty-two minutes each. I had been refilling my coffee.

This is the part where the demo people on Twitter usually clip a thirty-second time-lapse and call it a day. I want to show you the actual seams. Because the for/goal feature — the long-running autonomous loop that landed in Claude Code 2.1.139 and ships in Codex CLI 0.128.0 — only looks like magic. Underneath it is a very specific recipe, and that recipe is the difference between an agent that ships a real app in half an hour and an agent that loops forever, rewriting the same broken function until you kill the process out of pity.

I have been running for/goal almost every day since the feature dropped. Two parallel projects. Same product spec. Same six-phase roadmap. One run in Claude Code, one in Codex. Both finished. Both produced something I could `git clone` and demo to a client tomorrow. They also produced *very different* apps from the same prompt, and the differences tell you exactly which agent to reach for and when.

This is the breakdown I wish I had before I started.

---

## What For/Goal Actually Is — And Why It's Not Just Another Slash Command

Let me get the marketing language out of the way first.

For/goal is a persistent objective mode. You set a goal once, the agent works through it across many turns — sometimes hours, sometimes a full day — and a second, smaller model checks after every turn whether the goal is actually done. If the validator says no, the main model gets that "no" plus a one-sentence reason and keeps working. If the validator says yes, the loop ends and your terminal hands control back. That's the whole shape of it.

In Claude Code, the command is literally `/goal`. It shipped in version 2.1.139, released in early May 2026. The validator runs on whichever model you have configured as your small fast model — Haiku by default — and the validator's token cost is billed separately from the main turn budget, which matters when you're running a goal for six hours.

In Codex CLI, the command surface is the same idea. Codex calls the loop primitive `for/goal`, and the slash commands in version 0.128.0 are `/goal`, `/goal pause`, `/goal resume`, `/goal clear`, plus a `/side` thread for asking a quick question without disturbing the main run. Same architecture: main model does the work, small model judges completion.

This is, mechanically, an evolution of the Ralph loop pattern — the bash one-liner that engineers like Adam Tuttle and the snarktank repo turned into a methodology over the last twelve months. Ralph was a `while true` wrapper around your coding agent, with a verification command on the other end of the pipe. For/goal takes that idea and folds it into the agent itself, with a real supervisor architecture and a state-aware stop hook.

Here's the part that took me three runs to internalize: this is not a chat anymore. It's a worker process with a checklist. You don't talk to it. You set the destination and verify the destination is reachable. The agent does the rest.

Which sounds simple. It isn't, and I'll show you why.

---

## The Setup: One PRD, Two Agents, Sixty-Two Tasks

The test app was a content review tool — something between a Notion-style workspace and a video annotation queue. Nothing world-shaking. I picked it because the scope was big enough to be interesting (it needed auth, a workspace concept, a review queue, an onboarding flow, and a settings page) but small enough that I could read every file the agent produced and tell you what was actually there.

The spec was five files in the project root before I touched anything else:

- **`prd.md`** — the product requirements document. About 1,400 words. Audience, problem, the three core jobs the app needed to do, the data model in plain English, and a list of out-of-scope items so the agent wouldn't drift into features I didn't want.
- **`productroadmap.mmd`** — a Mermaid roadmap with six phases and sixty-two leaf tasks. Phase 1 was scaffolding and auth, Phase 2 was the workspace concept, Phase 3 was the review queue, and so on through to Phase 6, which was polish and onboarding.
- **`design.md`** — front-end direction. Color palette, typography stack, layout density preferences ("dense table views over card grids for queue pages"), and a list of components I expected to see (data tables, slide-over panels, command palette).
- **`claude.md`** — Claude Code's project-level rules file. Stack pinned to Next.js 15 App Router, TypeScript strict, Tailwind v4, shadcn/ui, no Redux, no styled-components, server actions for mutations.
- **`agents.md`** — Codex's equivalent rules file with the same constraints, written in Codex's preferred format.

The goal I gave each agent was identical, word for word:

> Build the complete app outlined in `prd.md` following the tasks in `productroadmap.mmd` until all tasks are complete and verified. Use `design.md` for front-end design direction. Fresh Next.js app build. Run with auto-approve enabled. Continue until the validator confirms every roadmap task is checked and the app builds and lints clean.

That goal sentence is doing a lot of work. Let me unpack what makes it survive a thirty-minute autonomous run, because I burned three earlier attempts on weaker phrasings before I got here.

The phrase "until all tasks are complete and verified" is the stop condition. It points the validator at a concrete thing to check — the roadmap file — instead of asking it to evaluate quality, which is a job small models do terribly. "Fresh Next.js app build" is the scope ceiling. It tells the agent "don't try to integrate with anything that already exists in this directory, you own the whole tree." And "Run with auto-approve enabled" is the permission grant — without it, Claude Code in particular will stop every forty seconds to ask if it can install a package.

You'll see what happens when one of those clauses is missing in a few sections. Stick around for the second-half mistakes section.

---

## How To Write a For/Goal Goal That Actually Finishes

This is the part I want to slow down on, because the difference between a goal run that completes and a goal run that loops forever is almost always the goal sentence itself.

A working for/goal target has four parts, in this order:

**1. What to achieve.** A measurable end state. Not "make it nice." Not "improve the UX." Something the validator can read and answer yes-or-no on without making a judgment call. "All sixty-two roadmap tasks marked complete in the file." "Production build passes." "All Playwright tests green."

**2. What to change.** Scope. Which files, which directories, which surfaces the agent is allowed to touch. If you skip this, autonomous agents will refactor adjacent code on their way to the goal, and you'll get a thirty-page diff that includes nine files you never wanted touched.

**3. What to validate.** The actual command or signal the validator should look for. "`pnpm build` exits zero." "The roadmap file has zero unchecked items." This is what gets fed to the small fast model after each turn.

**4. When to stop.** The exit condition. Usually the same as the validation signal, but sometimes you want belt and suspenders — "stop when the validator confirms AND the integration tests have run at least once."

A goal larger than a single prompt but smaller than an open-ended backlog. That's the size sweet spot. "Refactor this function" is too small — just prompt it. "Build a SaaS" is too big — the agent has no idea what done means and will spiral. "Build the app described in `prd.md` until every task in `productroadmap.mmd` is checked and the production build passes" is exactly the size for/goal was designed for.

Side note — I tested this on a weekend morning fueled almost entirely by espresso and stubbornness. The results might have been different on a Monday with better sleep. But I doubt it. The goal-writing discipline matters more than the model you point it at, which is the unexpected lesson of the last two months.

---

## Run One: Claude Code on /goal

I hit `/goal` in Claude Code first. Pasted the goal sentence. Watched.

The first turn was a planning turn. Claude Code read `prd.md`, read `productroadmap.mmd`, read `design.md`, read `claude.md`, then printed a one-paragraph summary of what it was about to do and asked one question: "Several roadmap tasks reference external API integrations — Stripe for billing, Resend for transactional email, Supabase for auth and persistence. Do you have credentials for these, or should I build with mock data and stubbed clients so the app runs offline?"

That single question is the most senior-engineer moment I have ever seen an agent produce in the wild. It identified that the app couldn't actually be completed without external creds, recognized that I had not provided any, and proposed a fallback that would let it ship something runnable instead of stalling. I told it to build offline with mocks and a clearly-marked TODO list of integration points, and it kept going.

From there it ran for twenty-nine minutes without another question.

The pattern was the same every turn: pick the next unchecked roadmap task, read the relevant files, write or modify the code, run a focused validation (`pnpm tsc --noEmit` for type changes, `pnpm lint` for component changes, `pnpm build` after major milestones), tick the task in the roadmap file, and return to the validator. The validator answered "no" after every turn except the last one. The "no" came back with a one-line reason — "Phase 3 task 4 still unchecked" or "Build last passed twenty minutes ago, verify it still passes after the new components" — and the next turn started with that reason as the first input.

At minute twenty-nine, the validator said "yes." Claude Code stopped, summarized what it built, and listed the TODO integration points it had stubbed. I checked the repo. Sixty-two tasks ticked. Build passed. Lint clean.

Then I read the actual app.

The landing page was dense with copy. Real heading hierarchy. A testimonials block with three quoted personas that matched the audience described in the PRD. A pricing table with three tiers, each anchored to a job-to-be-done from the spec. The dashboard was text-heavy but clearly structured — sidebar with the workspace switcher, a top bar with the command palette trigger, a main panel that defaulted to the review queue. The review queue itself was a dense table with sortable columns, exactly what `design.md` had asked for. There was an onboarding flow with three steps and clean micro-copy.

The auth was mocked. Login redirected to dashboard after a fake delay. Session was stored in a cookie with a TODO comment pointing at the Supabase integration point. The Stripe checkout button opened a modal that explained what would happen when the integration was wired. Every external integration was a clearly-marked seam, not a broken call.

This was the version I would send to a client to show them what their idea looks like. It would not survive contact with a paying user — the auth alone is a security incident waiting to happen — but it would absolutely survive contact with a Tuesday demo meeting.

Total elapsed: 32 minutes, 14 seconds. Two Haiku validator runs cost me forty-seven cents. Main agent token spend was, by the cost calculator, $11.20 on Sonnet 4.6.

---

## Run Two: Codex CLI on /goal

I cleared the directory, restored the spec files, hit `/goal` in Codex.

Codex did not ask the env-vars question. It just started.

This is the first place the two agents diverged in a way that matters. Codex assumed it had license to build with mocks anywhere external creds were missing. Which is — in the end — what I would have told it to do anyway. But Claude Code's behavior of pausing to confirm is, for production work, the better default. Codex's behavior of just deciding is the faster path when the spec is unambiguous, and that's most of the time.

The Codex run pattern was almost identical to Claude Code's at the loop level — plan, act, validate, repeat — but the way it sequenced work was different. Claude Code went phase by phase, finishing Phase 1 entirely before touching Phase 2. Codex worked more lattice-shaped: it scaffolded the entire app skeleton first (all six phases' worth of empty routes and component stubs), then went back and filled them in. This is a thing I've noticed across multiple Codex runs and it's not a bug — it's how the planning model in Codex prefers to decompose work. It builds the silhouette of the finished product first, then sculpts.

Minute thirty-one, the validator said yes. I read the app.

Visually, the Codex app was the prettier one. Cleaner typography (it picked Geist over the Inter that Claude Code defaulted to, which matches the front-end design conversation I'm starting to see across most senior product designers in 2026). The landing page had real product imagery — Codex pulled placeholder images from a clean Unsplash collection rather than dropping `Image` components with broken src attributes the way Claude Code did once during an earlier test. Tabs were cleaner, icons were rounded, the color accent system pulled red accents from the design file consistently across every page.

Functionally, Codex did some things Claude Code did not. The review queue had inline editing — you could click a status pill and change it without leaving the page. The filter system on the queue had three working filters plus a saved-view dropdown. The settings page had a tooltip layer that explained every field. There was a demo workspace pre-populated with fake content so the empty state of every page showed something instead of an "add your first item" placeholder.

The losses, because there are always losses: two icons were broken — Codex referenced `lucide-react` icon names that don't exist in the current version, and I had to swap them by hand. The auth fallback was clever but fragile — the mock client had a 50% chance of returning the demo user on session check, which I'm pretty sure was an artifact of how Codex wrote the stub and not a deliberate design. The landing page copy was sparse compared to Claude Code's — Codex put visual polish first and let the words coast.

Total elapsed: 32 minutes, 42 seconds. Roughly the same token spend. Roughly the same task completion rate.

Two agents. Same spec. Same loop architecture. Different products.

---

## The Side-By-Side: What Each Agent Optimized For

Here's the comparison I would have wanted handed to me before I started this experiment.

| Aspect | Claude Code (`/goal`) | Codex (`/goal`) |
|---|---|---|
| Total time | 32 min 14 sec | 32 min 42 sec |
| Tasks completed | 62 of 62 | 62 of 62 |
| Asked clarifying questions | Yes — one (env vars) | No |
| Build order | Phase by phase | Whole skeleton first |
| Landing page | Dense, copy-led, conversion-shaped | Image-led, visually clean |
| Typography default | Inter | Geist |
| Design polish | Less | More |
| Functional depth (per page) | Less inline interaction | More — inline editing, filters, tooltips |
| Demo content | Empty states | Pre-populated demo workspace |
| Broken pieces | None I caught | Two missing Lucide icons |
| Auth mock quality | Cleanly stubbed with seams | Stubbed but probabilistic |
| Onboarding | Three-step flow with copy | Two-step flow, lighter |
| Token cost | ~$11.20 main + $0.47 validator | Roughly equivalent |

If you're looking for the headline: **Claude Code wrote a better product story, Codex wrote a better product shell.** Claude Code's app would convert better on a landing page; Codex's app would feel better in a product demo. Which one matters for you depends entirely on what you're shipping.

For my own workflow, I have started reaching for Claude Code on for/goal runs where the output has to *communicate* — landing pages, marketing pages, anything that needs to read like a person wrote it. I reach for Codex on for/goal runs where the output has to *behave* — dashboards, tools, internal apps with a lot of interaction surface. That heuristic has held across about a dozen runs since.

---

## What This Means For The Way I Build

Let me zoom out, because the implication here is bigger than which agent to pick.

For about eighteen months, the dominant pattern in AI-assisted development has been the call-and-response loop. You prompt, the agent does one thing, you review, you prompt again. Even the best workflows I've built — and I've written about most of them, including the [Claude Code and Codex two-agent workflow](https://www.mejba.me/blog/claude-code-codex-two-agent-workflow) and the [Claudeex planning loop](https://www.mejba.me/blog/claudeex-claude-code-codex-loop) — were variations on call-and-response with a second pair of eyes added in.

For/goal breaks that pattern. The agent is not asking permission. The agent has a destination and a budget and a validator, and your job upstream of the run is to write the destination clearly enough that the validator can recognize when it's been reached. Your job during the run is to be elsewhere. Your job after the run is to read the output and decide what's good.

That's a different muscle. It's closer to writing a brief for a contractor than it is to pair programming. The skill is in the spec, not in the prompting. If your PRD is sharp, your roadmap is granular, and your design doc is concrete, for/goal makes you fast in a way that the previous generation of coding agents could not. If any of those three are vague, for/goal will produce a beautifully-completed run that ships the wrong product.

The reason this matters: for the first time, the bottleneck in AI-assisted building is not the model. It's the prep work. And prep work is something most engineers — myself very much included — under-invest in because it doesn't feel like building. The shift for/goal forces is that prep work *becomes* the building. The code is downstream of the spec, and the spec is where the engineering judgment now lives.

I would have told you six months ago that the future of coding was multi-agent orchestration — Claude Code talking to Codex talking to Gemini, with hand-offs and review loops and consensus decisions. I still think that's part of it. But for/goal made me realize there's a simpler future running in parallel: one well-specified destination, one capable agent, one verifiable stop condition. No orchestration. No hand-offs. Just a goal and a loop.

That future is less interesting to write think-pieces about. It's more interesting to ship product in.

---

## The Mistakes I Made (And You Will Make)

Three mistakes from my first ten for/goal runs, in case you'd like to skip the tuition.

**Mistake one: I gave for/goal a vague goal.** "Build a project management tool" instead of "build the app outlined in prd.md until all roadmap tasks are checked and the build passes." The agent ran for two hours, produced six different versions of a settings page, and never stopped because there was no concrete signal for the validator to check. The fix: always point the validator at a file or a command, never at a feeling.

**Mistake two: I forgot to enable auto-approve.** Without it, Claude Code in particular will pause to ask permission for every package install, every file delete, every shell command outside a small whitelist. For a sixty-two-task run, this means the agent stops about forty times to ask, which makes the whole point of long-running autonomy moot. Enable it explicitly in your goal sentence — "run with auto-approve enabled" — or set it in your config before you start.

**Mistake three: I let the agent invent the spec inside the run.** I gave it a one-page brief and said "figure out the rest." It did figure it out. The rest was not what I wanted. For/goal is not a substitute for thinking through what you're building. It is a substitute for *typing what you've already thought through*. Those are different skills. The first one still lives with you.

There's a deeper version of mistake three, which is the one nobody writes about: long-running agents are very good at making any spec look right by the time they finish. The final product will look polished, will pass its own validator, will appear to complete every task — and will be exactly as good as the spec you started with, no better. If the spec was thin, the polished product is a thin product wearing a clean shirt. The agent is not going to push back on weak product thinking. That's still on you.

This is the part of for/goal that takes the most getting used to, especially for engineers who learned to build by iterating with a teammate. The teammate is now the validator, and the validator is reading a roadmap file, not your roadmap thinking. If the roadmap is wrong, the loop will faithfully build the wrong thing. The work that used to happen during implementation now has to happen before you press enter on the goal. That mental shift is the one skill that separates the engineers getting two-week features done in an afternoon from the engineers getting two-week features done in eight hours of frustrating loops.

I'm still adjusting. The discipline of writing a roadmap that an agent can faithfully execute is genuinely different from the discipline of writing a roadmap that *you* can execute, because you and the agent fail in different ways. You drift. The agent doesn't. You compensate for ambiguity by exercising judgment in the moment. The agent compensates for ambiguity by inventing detail, often wrong. So the roadmap has to be tighter than the one you'd write for yourself. That's the new shape of the work.

---

## When To Reach For For/Goal — And When Not To

Three categories of work where for/goal earns its keep:

**First-pass app scaffolding from a clear spec.** Exactly the experiment in this post. You have a PRD, a roadmap, a design doc. You want a runnable shell with most of the surfaces in place. For/goal is unbeatable here. Thirty-two minutes for what would take a junior engineer most of a week.

**Long-horizon refactors with measurable end states.** "Migrate every component in this directory from class to function syntax until `pnpm tsc --noEmit` passes." "Convert all `useEffect` data fetching to server actions until no `useEffect` references `fetch` anywhere in the codebase." Anything with a measurable stop condition and a mechanical path. The Ralph-loop folks have been doing this for a year; for/goal just makes it a first-class feature.

**Bug-fix campaigns.** "Resolve every TypeScript error in the repo without changing runtime behavior." The validator checks the error count. The agent works it down to zero. Goes home.

Three categories where for/goal is the wrong tool:

**Anything requiring product judgment mid-flight.** "Improve the onboarding flow." That's not a destination, that's an opinion. The agent will produce something. It will not produce what you wanted unless you wrote what you wanted into the spec first. Just prompt it.

**Anything touching production systems.** For/goal is for environments where the worst outcome is "the agent wasted my afternoon." Not "the agent wiped a database." Auto-approve plus production access is a category of mistake I would like none of us to make.

**Anything where the validator has to evaluate quality.** Small models cannot answer "is this code good?" reliably. They can answer "does this command exit zero?" reliably. Design your validator question around what small models are good at — yes/no decisions with concrete signals — or your loop will either stop early or never stop at all.

If your work doesn't fit those three "good" categories, you probably want the regular Claude Code or Codex chat loop, not for/goal. Most of my real work still lives in normal chat. For/goal is the specialist tool I reach for two or three times a week, not the daily driver.

---

## What I'd Run Differently Next Time

Three small things I'll change in my next for/goal experiment.

I'd add a `tests.md` file alongside `prd.md` and ask the agent to write integration tests for the critical paths as Phase 0, before any UI. The tests then become the validator's stop signal, which is much stronger than "all roadmap tasks ticked." Right now the loop trusts the agent's own check that the roadmap is complete; if the tests were the truth, the agent couldn't lie to itself.

I'd tighten the design doc. My `design.md` was about three hundred words of preferences. The next one will be six hundred words with specific component patterns called out — exact spacing tokens, exact font scale, exact button hierarchy. I want to give the agent less room to invent design judgment, because the moments where Claude Code and Codex diverged most sharply were the moments where the design doc was silent and they each picked a different default.

I'd run the same goal in both agents *and* a third — Gemini 3 Pro through its new CLI, which I haven't yet stress-tested at this length of run. If two agents diverge this much on the same spec, I want to know what a third gives me. That's a post for next month.

---

## The Mindset Shift, Stated Plainly

Here's what I keep coming back to.

Six months ago, building an app meant me writing code with help from an agent. The agent was an accelerator on the work. I was the engine.

Today, with for/goal, building an app means me writing a spec with help from an agent, then handing the spec to a different agent and reading what comes back. The agent is the engine. I'm the architect.

That sentence is easy to type and hard to live. Most days I still default to opening the editor and writing the first component myself, because that's the move I've made ten thousand times. The discipline of *not* doing that — of staying in the spec file for an extra hour, of writing the validator question instead of the implementation — is the new skill. The engineers who develop it first are the ones who'll ship five products in the time it used to take to ship one.

The two terminals on my left monitor at 3:19 PM that Wednesday afternoon were not, in retrospect, the impressive part. The impressive part was the four hours I spent the day before writing the PRD, the roadmap, and the design doc. The agents executed in half an hour what would have taken me a week. The thinking that made the half-hour possible still took the day.

That's the trade. I'm pretty sure I want it.

---

## Frequently Asked Questions

### What is for/goal in Claude Code and Codex?
For/goal is a persistent objective mode where the agent works autonomously across many turns until a small validator model confirms a defined completion condition is met. In Claude Code, it ships as `/goal` in version 2.1.139; in Codex CLI, the same primitive lives behind the `/goal` and related slash commands in version 0.128.0. See "What For/Goal Actually Is" above for the full mechanics.

### How long can a for/goal run go?
For/goal runs are bounded by the budget you set — turns, tokens, or wall-clock time — rather than by a hard ceiling. I've seen 32-minute scaffolding runs and overnight refactor runs both succeed. The practical ceiling is your token budget and your willingness to let the loop continue without supervision.

### Is for/goal the same as the Ralph loop?
For/goal is an evolution of the Ralph loop pattern. Ralph was a bash `while true` wrapper around a coding agent with an external verification step; for/goal folds the same idea into the agent itself with a built-in supervisor architecture and a state-aware stop hook. Same shape, cleaner mechanics, better validator integration.

### Do I need a PRD to use for/goal?
You need a clear, file-based destination. A PRD plus a roadmap is the most reliable combination, but you can also point for/goal at a test suite, a build command, or a measurable metric. The rule is: the validator has to be able to answer "is the goal reached?" with a yes-or-no decision based on a concrete signal, not a quality judgment.

### Which is better for for/goal — Claude Code or Codex?
Neither is strictly better. In my testing, Claude Code produces more communicative output — landing pages, marketing surfaces, copy-heavy pages read like a person wrote them. Codex produces more interaction-rich output — dashboards, internal tools, surfaces with editable cells and filters feel more polished out of the box. Pick by what you're shipping.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
