**BRAND:** mejba.me
**TITLE:** Deep Modules: The Claude Code Skill Saving My Codebase
**META TITLE:** Deep Modules Claude Code Skill: My Honest Test Results
**SLUG:** improve-codebase-architecture-skill-deep-modules
**PRIMARY KEYWORD:** deep modules Claude Code
**META DESCRIPTION:** I ran the improve-codebase-architecture skill on my own repo. Six deepening opportunities, one that made me wince, and the cadence I now run weekly.
**TAGS:** Claude Code, Software Architecture, AI Skills, Code Quality, Refactoring

---

I ran the `improve-codebase-architecture` skill on a project I've been shipping to since November. Sonnet 4.6 spent eleven minutes reading my code, opened a markdown report in my editor, and listed six things it thought were quietly rotting underneath me.

The first one I dismissed. The second one I argued with. The third one made me wince, walk to the kitchen, pour coffee I didn't want, and come back to read it again.

It had found two parallel implementations of the same concept — one in my frontend, one in my backend — sitting in entirely different folders, owned by entirely different mental models, with no unified seam between them. They were drifting. I had shipped a bug three weeks earlier whose root cause was that exact drift. I had not connected the two events. The skill connected them in a paragraph.

That is the moment I stopped treating this skill as another GitHub repo and started treating it as the only thing standing between me and the codebase I'm headed toward if I keep shipping at AI speed without architectural pressure.

If you've been vibe-coding in Claude Code through Q1 2026 and your codebase is starting to feel heavier than it should — sluggish to navigate, scary to refactor, full of small modules that all seem to depend on each other in ways you can't draw on a whiteboard — this post is for you. The next 4,000 words are about why that's happening, what John Ousterhout figured out twenty years ago that makes it fixable, and how Matt Pocock's [improve-codebase-architecture skill](https://github.com/mattpocock/skills/tree/main/skills/engineering/improve-codebase-architecture) operationalizes the fix inside Claude Code.

## The Real Cost of Shipping Fast With AI

Here's the thing nobody puts on the AI coding marketing pages.

The reason Claude Code feels magical at week two and feels heavy at month four is not that the model gets worse. It's that your codebase gets worse, faster than your brain can model it. Software has always had this property — Ousterhout built an entire book around it, *A Philosophy of Software Design*, which I'll come back to in a second. But the rate has changed. AI doesn't write better or worse code on average than I do. It writes code at five to ten times the rate I do. Every architectural shortcut I take ships in fifteen minutes instead of two days, which means the entropy compounds on a calendar that my judgment was never trained on.

The technical name for what's happening is software entropy. The code-shaped name is "ball of mud." The feeling, if you've lived it, is the moment you open a file and realize you no longer know who calls this function, what it returns when the input is malformed, or whether changing it will break the test suite or production or both.

I hit that feeling on the project I mentioned above. Not because the code was bad — most of it had been reviewed, most of it had tests, most of it ran in production for paying users. The problem was finer-grained than "bad code." The problem was that I had thirty modules where I needed twelve. Concepts that belonged together had been split across files because at the moment I wrote each piece, splitting felt cleaner. The total cognitive cost of those splits was now higher than the cognitive cost of the duplication they were avoiding.

That's the thing AI accelerates. The decision to split, extract, and abstract is cheap when an agent can do it in twenty seconds. The decision is so cheap that I make it without thinking. The result is a codebase shaped like a fractal of small, polite, individually-correct modules with no center.

Ousterhout has a word for that shape. He calls it shallow. The fix has a name too: deep modules. The skill I'm about to walk you through is the first tool I've used that operationalizes the fix without requiring me to re-read three hundred pages of a software design book every Friday afternoon.

## Deep Modules vs Shallow Modules — In Plain English

Before I walk through the skill, you need the vocabulary. Once you have it, the skill's output reads like English. Without it, the report looks like a refactoring shopping list.

A **module** is a unit of your application with a clear boundary. In a React app, a module might be a component. In a Node service, it might be a function, a class, or a folder. The thing that makes it a module is not its size — it's that there's an *outside* and an *inside*, and the inside is hidden.

The **interface** is what someone has to learn to use the module from outside. Function signatures. Component props. Public methods. Documentation. Type definitions. Anything a caller has to load into their head to make the module do its job.

The **implementation** is everything inside the boundary that the caller doesn't have to know about. Loops, helpers, state machines, database queries, retries — the actual work.

Now the part that matters.

A **deep module** has a small interface and a complex implementation. You learn ten things about it, and it does a thousand things for you. The leverage ratio — behavior accessible per unit of interface learned — is high. Ousterhout's classic example is the Unix file system call `read(fd, buf, n)`. Three arguments. Decades of operating-system complexity hiding behind it. You don't think about disk geometry, page caches, or block allocation. You ask for n bytes. You get n bytes.

A **shallow module** has an interface that is roughly as complex as the implementation it hides. You learn ten things about it, and it does eleven things for you. Or, in the worst case, you learn ten things about it and it does eight things for you, because the interface leaks more than the implementation contains. The leverage ratio is near one. The module is barely paying its rent.

Here's a concrete pair. Stay with me — this is the moment the vocabulary lands.

```typescript
// SHALLOW: this module's interface is bigger than what it hides
export class UserAuthHelper {
  hashPassword(password: string, salt: string): Promise<string>;
  generateSalt(): string;
  verifyPasswordAgainstHash(password: string, hash: string, salt: string): Promise<boolean>;
  isPasswordStrongEnough(password: string): boolean;
  getMinimumPasswordLength(): number;
  getMaximumPasswordLength(): number;
  generateSessionToken(userId: string): string;
  validateSessionToken(token: string): { userId: string } | null;
  revokeSessionToken(token: string): Promise<void>;
}
```

You read that and you can already feel it. To use this thing I have to know about salts, hashes, session tokens, password rules, and revocation, all of which are implementation details of *being authenticated*. The interface is leaking the inside of the module out into every caller's brain.

Now the deep version of the same thing.

```typescript
// DEEP: small interface, the complexity is locked inside
export class Auth {
  signIn(email: string, password: string): Promise<Session>;
  signOut(session: Session): Promise<void>;
  currentUser(session: Session): Promise<User | null>;
}
```

Three methods. Salts, hashes, session generation, password rules, revocation, expiry, refresh tokens — all of that lives inside `signIn`, `signOut`, and `currentUser`. The caller doesn't need to know any of it. If I want to migrate from bcrypt to argon2 next month, no caller changes. If I want to add multi-factor auth, the interface stays the same — `signIn` just gets richer behind the seam.

That's the move the skill is looking for. Every deepening opportunity is, at its core, an opportunity to take the second shape and cover up the first one.

There's one more piece of vocabulary you need before we go further.

**Locality** is how related functionality is grouped in the codebase. High locality means the things that change together live next to each other. Low locality means changing one feature requires editing files in three folders that don't even know about each other. The shallow `UserAuthHelper` above has medium locality — at least it's one class. The bug I found in my project had *low* locality — auth-shaped logic was duplicated in `apps/web/src/lib/session.ts` and `services/api/src/auth/session.go`, with no shared types and no enforced contract between them.

Deepening usually improves locality automatically. When you collapse three shallow modules into one deep module, the related code moves into the same file, which means the next person who edits it (probably future me, at 11 PM, two months from now) can see all of it at once.

## What the Skill Actually Does

The `improve-codebase-architecture` skill, written by Matt Pocock and shipped as part of his open-source [skills repo](https://github.com/mattpocock/skills), is a small markdown bundle that reshapes how Claude Code reads your repository. You install it like any other skill — drop it in `~/.claude/skills/` or use the install command from the README — and from then on, when you ask Claude Code to look for refactoring opportunities, it does it through Ousterhout's lens instead of generic "code smell" heuristics.

Mechanically, the skill does three things.

First, it scans the repo with a specific question in mind: where are the shallow modules clustered? It's not looking for individual bad files. It's looking for clusters of small modules that are tightly coupled, where each one has an interface nearly as complex as its implementation, and where you can feel the friction of moving between them when you read the code.

Second, it produces a list of **deepening opportunities** — usually between three and ten, in my experience — each one written as a short proposal: which modules to merge, what the new interface should look like, where the test seam would live, and what risks the refactor carries. The proposal is meant to be read by a human, argued with, and either accepted, modified, or rejected.

Third, when you accept a proposal, the skill enters an interactive design pass. Claude proposes the new interface, you push back on names and shapes, the model revises, and once the interface is settled it proposes an implementation strategy. The strategy includes how to migrate callers and where to put the test boundary. When you give the green light, the skill files a GitHub issue (or whatever issue tracker you've wired in) so the work is tracked even if you don't do it that day.

The two things I want to underline. The skill does not refactor your code by itself. It surfaces opportunities and helps you think. Humans make every architectural call. And the skill is opinionated — it specifically prefers fewer, deeper modules over more, smaller modules. If your team has a strong cultural preference in the other direction, you'll fight it.

## The Day I Ran It On My Own Repo

The repo I tested on was a SaaS dashboard I've been shipping to weekly since November. Around 1,500 commits, mostly from me, occasionally pair-programmed with Claude Code. TypeScript across the whole stack, React Router on the frontend, a Node service in the back, a Postgres database underneath. Real users. Real bugs. Real cognitive cost every time I open it.

I cleared a Sunday morning, made coffee, and ran one prompt:

> Use the improve-codebase-architecture skill. Scan this repo and produce a list of deepening opportunities, ordered by impact.

Eleven minutes. Six opportunities.

I'm going to walk through three of them, because the other three were variations on the same patterns and you'll get the shape from these.

**Opportunity 1: The duplicated session concept.** This was the one that made me put the coffee down. The skill flagged that `apps/web/src/lib/session.ts` and `services/api/src/middleware/session.go` were both implementing what looked, semantically, like the same module — read the session, validate it, refresh if expired, return null if invalid. They had different types. Different naming. Different error semantics. The frontend silently treated expired sessions as "log the user out." The backend returned a 401. There was no shared contract between them, which meant any drift between the two would manifest as a UX bug. The skill's proposal: define a single `Session` module with one interface (typed in shared schema), one canonical state machine (expressed in OpenAPI plus a generated client), and an adapter on each side that implements the interface against the local environment. Two adapters, one interface. A real seam. I'd shipped a session-related bug three weeks earlier. The skill didn't know that. The skill saw the architecture and predicted the bug shape from the architecture alone.

**Opportunity 2: The fragmented logger.** I had four logging utilities. A `console.log` wrapper in the frontend. A `pino` config in the backend. A "structured event" helper for analytics. A "telemetry span" helper for tracing. Each one had been added, separately, in response to a real need. Each one looked clean in isolation. Together, they meant that when I wanted to debug a specific user's flow across the stack, I had to read four sets of logs in four different formats. The skill's proposal: a single `Observability` module with one interface — `event(name, payload)`, `error(err, context)`, `span(name, fn)` — and four adapters that fan out to the existing transports. Same call sites. Same outputs. One interface for me to learn, four implementations behind it. This was a pure deepening — no functionality lost, callers' lives meaningfully simplified.

**Opportunity 3: The one I argued with.** The skill flagged my form-validation helpers as a deepening opportunity. I had `validateEmail`, `validatePhone`, `validateRequired`, and a half-dozen others, each one a small pure function. The proposal: collapse them into a single `Validator` module with a fluent API. I pushed back. Pure functions are usually deeper than they look — `validateEmail(email)` has a tiny interface and a non-trivial implementation (RFC 5322 is not friendly), and the leverage ratio is fine. The skill's counter-argument was about *locality*: the validators were used together, in clusters, on every form, and the surrounding code had to import six different functions instead of one. After ten minutes of back-and-forth in the chat, I conceded that the locality argument was real, but proposed a compromise — keep the pure functions, add a `Form` module with a fluent API that composes them. The skill agreed, drafted the new shape, and filed the issue. That's the moment I trusted this thing.

The other three opportunities involved a router-state module that had grown a state-machine inside it, a payment integration that was leaking webhook details into UI code, and a feature-flag system that had quietly turned into three independent feature-flag systems. All three got proposals. Two I accepted; one I deferred to the next quarter.

## Seams and Adapters — Why Deepening Makes Tests Possible

Here's the part of the skill that connects directly to whether your codebase is testable, and the reason this all matters more than just "code looks nicer."

A **seam** is a boundary in your code where you can substitute a fake for the real thing without changing the surrounding code. Michael Feathers called it that in *Working Effectively With Legacy Code* — twenty years ago, but it's never been more relevant than in 2026. The interface of a module is its natural seam. If the module has a small, clean interface, you can put a fake on the other side of that interface for tests. If the module has a sprawling, leaky interface, every test has to pretend to be the real thing in twelve different ways, and you stop writing tests because they hurt.

An **adapter** is the concrete implementation that lives on the other side of the seam. The real one talks to the real thing — Postgres, the network, the system clock. The fake one returns whatever you want for the test.

The cleanest example, and the one I now use to teach this on my team, is the system clock.

```typescript
// The interface — a one-method module
interface Clock {
  now(): Date;
}

// The real adapter
class SystemClock implements Clock {
  now() { return new Date(); }
}

// The fake adapter, for tests
class FakeClock implements Clock {
  constructor(private current: Date) {}
  now() { return this.current; }
  advance(ms: number) {
    this.current = new Date(this.current.getTime() + ms);
  }
}
```

Now any code that depends on time depends on `Clock`, not on `Date.now()`. In production it gets the real clock. In tests it gets a fake clock that I can advance by an hour, a day, a year. Every test I've written that depended on time used to be flaky. Every test I've written since I extracted the clock into a deep module with two adapters is deterministic.

The skill loves this kind of refactor. When it scans a repo, the question it asks at every module is: *where would the test seam go?* If the answer is "nowhere obvious — the module is talking directly to the database and the network and the clock and the file system simultaneously," that's a deepening opportunity. The fix is to extract the dependencies into modules with their own interfaces, then put adapters on either side. Suddenly every test that was painful gets easy.

This is the part that pays for itself fastest. The first deepening I did from the skill's report — the session module — saved me a full afternoon the next week when I needed to test a session-expiry edge case. Before the refactor, I would have stood up a test database, mocked an HTTP call, and prayed. After the refactor, I instantiated a fake session adapter, set its expiry to 30 seconds ago, and ran one assertion.

## The Legacy-Codebase Trap (And How to Avoid It)

Now the warning, because I almost made this mistake.

If you run this skill on a legacy codebase — one with patchy test coverage, low locality, and shallow modules everywhere — the first instinct is to go top-down on the report. Take the biggest deepening opportunity, refactor aggressively, ship.

Don't.

The reason every senior engineer with scars on their hands has the same advice — *don't refactor untested code* — is that shallow modules with no tests are exactly the modules where deepening will break things you didn't know existed. Callers depend on undocumented behavior. Edge cases hide in the cracks between modules. The shape of the bugs is exactly what made the modules shallow in the first place.

The right move is the unglamorous one. Before you deepen, write **characterization tests** around the existing shallow module. Not unit tests. Not perfect tests. Just tests that pin down the current behavior — including the buggy behavior — so that when you deepen, you have a way to know what changed. Feathers' book is the canonical reference here. The skill itself, in its README, recommends roughly the same workflow for legacy code: write tests that document the current behavior of the cluster you're about to deepen, run the skill's deepening proposal against those tests, and use the test deltas as a forcing function for design decisions.

I now follow this rule even on greenfield code. If a deepening proposal touches a module that doesn't have tests, the first commit in the refactor is the test commit. The deepening commit comes second. It slows me down by maybe twenty minutes per refactor and saves me from "wait, why is the dashboard blank now" debugging sessions an hour later. Trade I'll take every time.

## How Often I Run It Now (And Where It Stumbles)

I run the skill every Monday morning on my main project, and roughly every five working days on side projects with high commit velocity. That cadence emerged from a simple observation: on a project where I'm shipping daily with Claude Code, the entropy accumulates fast enough that a weekly architectural review actually catches it before it congeals. On a project that's mostly stable, monthly is fine.

The cadence advice from the skill's documentation is "every few days in fast-moving codebases" and that lines up with my experience. If I let it slide for two or three weeks, the report goes from six opportunities to fifteen, and at fifteen I get decision-fatigue and start ignoring the report entirely. Six is the right number to actually act on.

Now the honest part — where the skill stumbles.

It's bad at language-specific idioms. When I ran it on a Go service, it kept proposing class-shaped designs that don't fit Go's grain. The vocabulary of modules, interfaces, and adapters translates, but the *shape* of the proposals is biased toward TypeScript and Python. If you're in a language with a strong opinion of its own — Go, Rust, Elixir — you'll spend the first five minutes of every proposal translating the idiom.

It's also blind to runtime cost. Every proposal I've gotten has been about cognitive cost — how easy is the code to understand, how testable is the seam — and zero of them have considered things like memory layout, allocation patterns, or hot-path performance. For most app code, that's fine. For anything performance-sensitive, you have to layer your own judgment on top.

And the third stumble: it sometimes proposes deepenings that would require me to rewrite the test suite to validate. The proposal looks beautiful in isolation, but the cost of the migration — including the tests that depend on the current shape — is higher than the value of the new shape. The skill doesn't model migration cost very well. I now read every proposal with one question on top: *what does the migration look like, and is the migration cost less than the leverage I'd gain?* Half the time, the answer is yes. The other half, I close the issue.

If you've been running the older skills like the [Karpathy CLAUDE.md install](https://www.mejba.me/karpathy-claude-md-skills-install-guide) or any of the [32 daily Claude Code hacks](https://www.mejba.me/claude-code-32-power-user-hacks) I wrote up last month, this skill slots in cleanly above them. The hacks make Claude Code shipping faster. The architecture skill is the architectural pressure that keeps the speed from rotting your codebase.

## The Question I Carry With Me Now

Six weeks after I started running this skill weekly, my codebase is meaningfully different from where it was. Not dramatically smaller — I haven't deleted that much code — but meaningfully more navigable. The session module is one place. The observability module is one place. The form composition has a single front door. When I open a file at 11 PM to debug something, I can usually see the whole concept from the file I'm in, instead of bouncing between four files and reconstructing the architecture from memory.

The change that matters more is the one upstream of the codebase. When I'm prompting Claude Code to ship a new feature now, I'm thinking in modules first. Where will the seam be? What's the interface? What does the deep version of this look like? The skill trained me to ask those questions before I write the prompt, which means the prompts themselves produce code that's already closer to what the skill's next scan would propose.

Software entropy is a one-way arrow without architectural pressure. AI just made the arrow move faster. The fix isn't slower AI. The fix is more pressure, applied earlier, by something that scales with the shipping rate. The `improve-codebase-architecture` skill is the closest thing I've found to that pressure, and it's the only architectural review tool in my workflow that has survived past the first month.

If you take one thing from this post, take the question I now carry into every Claude Code session: *is this module deeper or shallower than what it's replacing?* Ask it before you write the prompt. Ask it again when you read the diff. Ask it on Monday mornings, with coffee, while a small markdown file scans your repo and tells you the things you already half-knew but were too tired to face.

That question is doing more for my code than any skill, framework, or refactoring tool I've installed in the last two years. The codebase you're shipping to in 2027 will be the codebase that question built — or the one it didn't.

Open the report. Read the six things. Pick one.

## Frequently Asked Questions

### What is the improve-codebase-architecture skill in Claude Code?
It's an open-source skill from Matt Pocock that scans a repository for shallow modules and proposes deepening refactors. It runs inside Claude Code, produces a list of architecture opportunities, and helps you interactively design the new interfaces. For the full walkthrough of what it found on my repo, see "The Day I Ran It On My Own Repo" above.

### What is a deep module in software design?
A deep module is one with a simple interface and a complex implementation, so a caller learns very little about the module but gets a lot of behavior in return. The term comes from John Ousterhout's *A Philosophy of Software Design*. Shallow modules, by contrast, have interfaces nearly as complex as their implementations and provide low leverage.

### How often should I run the codebase architecture skill?
Every few days on fast-moving codebases with daily AI-assisted commits, and weekly to monthly on more stable repos. Past three weeks of silence, the report grows to ten-plus opportunities and decision-fatigue kicks in. Six opportunities a week is the sweet spot for actually acting on the proposals.

### Does the skill refactor code automatically?
No, and that's deliberate. It surfaces deepening opportunities and helps you design the new interface and seam, but humans make every architectural call and approve every change. Once you accept a proposal, it can file a GitHub issue to track the refactor.

### Should I deepen modules in a legacy codebase without tests?
Not directly. Write characterization tests around the shallow cluster first to pin the existing behavior, then deepen with the tests as your safety net. Deepening untested shallow modules is one of the fastest ways to ship a regression.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
