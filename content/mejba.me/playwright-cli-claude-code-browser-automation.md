**BRAND:** mejba.me
**TITLE:** Playwright CLI in Claude Code: Autonomous Browser Bots
**META TITLE:** Playwright CLI Claude Code: Browser Automation Guide
**SLUG:** playwright-cli-claude-code-browser-automation
**PRIMARY KEYWORD:** Playwright CLI Claude Code
**META DESCRIPTION:** How I use Playwright CLI inside Claude Code for autonomous QA loops, scraping behind logins, and agentic bots — with code, install steps, and token math.
**TAGS:** Claude Code, Playwright, Browser Automation, AI Agents, Web Scraping

---

The first time I let Claude Code drive a real browser unsupervised, I was watching a 12-question onboarding form fail in slow motion. Page 1 worked. Page 2 worked. Page 3 — a long-form textarea — froze. The agent pressed Enter to advance. Nothing happened. Pressed it again. Still nothing. Then it did something I genuinely did not expect: it took a screenshot, opened the page source, located the keydown handler, noticed the textarea was swallowing the Enter key instead of bubbling it up to the form's submit listener, patched the handler, redeployed, re-ran the form, and pinged me when all 12 questions submitted clean.

That whole loop — test, detect, fix, re-test — took about eleven minutes. I had been three feet from my keyboard the entire time and contributed zero keystrokes.

The thing that made it work wasn't a clever prompt. It wasn't a skill. It was a switch I'd flipped a week earlier: ditching Playwright MCP for **Playwright CLI** as Claude Code's hands inside the browser. That single change cut my browser-automation token bill by roughly 4x, made debug screenshots sane to review, and unlocked a category of agent I hadn't really been able to build before — autonomous QA loops, scrapers that survive Google blocking them, and login-gated bots that stay logged in across runs.

This is the full breakdown. What Playwright CLI is, why it specifically beats Chrome DevTools MCP and Playwright MCP for Claude Code workloads, the three production patterns I now use it for, and the ugly parts nobody mentions in the launch posts.

## Why Playwright CLI Exists (And Why It's Not Just Another MCP)

Microsoft shipped Playwright CLI in early 2026 as a deliberate companion — not replacement — to Playwright MCP. The MCP server still exists. It still works. But the team noticed something the rest of us were noticing too: when a coding agent like Claude Code talks to a browser through MCP, every single page interaction round-trips a full accessibility tree back into the model's context window. On a complex page, that tree is 50,000 tokens. Per click. Per scroll. Per keystroke.

Multiply that by a 50-step QA run and you understand why my Anthropic dashboard was screaming.

Playwright CLI flips the data flow. Instead of streaming the accessibility tree into the model context, the CLI saves snapshots to disk as compact YAML files. The model reads only the part it asked for, when it asked for it. Same browser. Same Playwright API underneath. Different relationship between the model and the data.

The numbers from the public benchmarks line up with what I saw on my own bills:

- **Playwright MCP:** ~1.5M tokens per browser-automation run (worst case, full pages, multiple turns)
- **Chrome DevTools MCP:** ~330K tokens per run (better — scoped snapshots, single execute call batching)
- **Playwright CLI:** roughly 4x fewer tokens than Playwright MCP for equivalent work

That's not a marginal optimization. That's the difference between "I can run this agent overnight" and "I can run this agent for forty seconds before billing tells me to stop." For people who already track token costs aggressively — and if you don't, my [Claude Code token optimization breakdown](https://www.mejba.me/blog/claude-code-token-optimization) is worth reading before you build any of this — Playwright CLI is the answer to a problem you might not have realized you had.

There's a second reason it matters that nobody talks about, and it took me a few sessions to figure out. **Playwright CLI is a CLI.** Not a daemon. Not a server. Not a protocol. It's a binary you call with arguments. Claude Code is *very* good at calling binaries with arguments. It is less good at managing a long-lived MCP connection, recovering from MCP timeouts, and parsing accessibility trees it didn't ask for. Playwright CLI plays to Claude Code's actual strengths — bash, files, and small focused tool calls.

That alignment is the thing the testcollab post called out as the real reason coding agents prefer it. Token efficiency is the headline. Tool fit is the substance.

## The Install (And Why I Skip Half of Microsoft's Recommended Setup)

You can be running Playwright CLI inside Claude Code in about ninety seconds. The install is cleaner than Playwright MCP, which used to involve copying a JSON snippet into your `mcp.json` and hoping the version string didn't drift.

The version of the install I actually use:

```bash
# Initialize a Playwright project — creates package.json, tsconfig, example tests
npm init playwright@latest

# Install browser binaries (Chromium, Firefox, WebKit + dependencies)
npx playwright install --with-deps

# Verify the CLI works
npx playwright --version
```

If you want it available everywhere instead of per-project:

```bash
npm install -g @playwright/cli@latest
playwright-cli install
playwright-cli install-browser
```

Microsoft also ships a `--skills` flag (`playwright-cli install --skills`) that wires Playwright into Claude Code's skill system. I tried it. It works fine. But I prefer talking to the CLI directly through bash because it gives Claude clearer error surfaces — when something breaks at the skill layer, you have to debug the skill *and* the underlying command. When something breaks at the CLI layer, the stderr tells you exactly what happened.

Once it's installed, the surface area Claude Code actually uses is small:

- `npx playwright codegen <url>` — record a session, output a working test script
- `npx playwright test` — run tests (headless by default, headed with `--headed`)
- `npx playwright test --debug` — open the inspector, step through frame by frame
- `npx playwright show-trace trace.zip` — review a recorded trace after the fact

The Playwright CLI proper (the `@playwright/cli` package) layers on a different vocabulary geared at coding agents — `open`, `goto`, `click`, `type`, `fill`, `select`, `check`, `hover`, `drag`, `upload`, `snapshot`, `screenshot`, `close`. Claude tends to compose those into short scripts rather than calling them one-by-one, which is the right instinct.

Now the part that matters: what you actually build with it.

## Pattern 1: The Autonomous QA Loop

This is the use case that converted me. I had a multi-page onboarding form — twelve questions, six pages, conditional branching on page four, a review screen, an edit-from-review flow. Standard stuff. Unstandardly broken.

The bug list when I started the run:

1. The Enter key wasn't advancing the form on textarea pages — only the explicit Next button did
2. The review page failed to load about 20% of the time, returning a blank component
3. The Edit button on the review page got blocked by a stale modal overlay if you'd dismissed a modal earlier in the flow

I knew about the first one. The other two I discovered *because* I let the agent run.

The script Claude Code wrote for itself, slightly cleaned up:

```typescript
import { test, expect } from '@playwright/test';

test('full onboarding flow — 12 questions, 6 pages', async ({ page }) => {
  await page.goto('http://localhost:3000/onboarding');

  for (let pageNum = 1; pageNum <= 6; pageNum++) {
    await page.screenshot({
      path: `screenshots/onboarding-page-${pageNum}.png`,
      fullPage: true,
    });

    // Fill whatever inputs exist on this page
    const inputs = await page.locator('input, textarea, select').all();
    for (const input of inputs) {
      const type = await input.getAttribute('type');
      if (type === 'email') await input.fill('test@example.com');
      else if (type === 'tel') await input.fill('555-0100');
      else await input.fill('automated test response');
    }

    // Advance — explicit button click, not Enter
    await page.getByRole('button', { name: /next|continue|review/i }).click();
    await page.waitForLoadState('networkidle');
  }

  await expect(page.getByText(/thank you|submitted/i)).toBeVisible({
    timeout: 10_000,
  });
});
```

The Claude Code instruction that drove it was four sentences: *"Run the test. If it fails, screenshot the failure point, read the source for the failing component, propose a fix, apply it, restart the dev server, and re-run. Repeat until the test passes three times in a row. Don't ask me about anything below P0."*

The agent ran the test. It failed at page 3 — Enter key, the bug I knew about. It opened the textarea component, found the `onKeyDown` handler that called `event.preventDefault()` unconditionally, narrowed it to only prevent Enter when Shift was held (so multi-line input still worked), saved, restarted dev, re-ran. Test passed page 3, failed at page 4 — the blank review page. The agent suspected a race between the route loader and the form-state hook, added a loading state, retried. Passed. Failed at the Edit-from-review modal collision. Wrote a small effect that cleaned up modal overlays on route change. Passed three times running. Stopped. Wrote a summary in `qa-run.md`.

Eleven minutes. Three real bugs found and patched. One human supervising from across the room.

The pattern, distilled:

1. **Test** — Playwright CLI runs the script
2. **Detect** — On failure, screenshot + read source + form a hypothesis
3. **Fix** — Apply the patch, restart anything that needs restarting
4. **Re-test** — Loop until green N times in a row, not just once

The "N times in a row" requirement is doing a lot of work. A flaky test that passes once isn't fixed. Three consecutive passes is the smallest sample size where you can credibly say the fix held.

This is genuinely the closest I've seen Claude Code get to behaving like a junior QA engineer who actually finishes the ticket. If you want to see how this kind of loop composes into a broader engineering workflow, the [Claude Code skills stack](https://www.mejba.me/blog/claude-code-skills-stack-bookz-ai-senior-engineer) post breaks down the layers above this one — Superpowers, Skill Creator, the rest. Playwright CLI is the eyes and hands. Those skills are the brain.

## Pattern 2: Adaptive Web Scraping (When Google Decides It's Bored Of You)

Different job, different lesson. A friend running a small dental marketing service asked if I could pull contact info — name, address, phone — for every dentist in a few specific California zip codes. Manual research time per zip code: four to six hours. Public information, just tedious to collect.

The first script Claude wrote was the obvious one: query Google for `dentist near 94110`, parse the SERP, visit each result, extract the phone number from the contact page. It worked. For about thirty searches. Then Google served a CAPTCHA, then a soft block, then a hard rate limit.

The patch was the interesting part. Without me prompting, Claude added three behaviors:

```typescript
import { chromium } from 'playwright';

async function adaptiveSearch(query: string) {
  const browser = await chromium.launch({ headless: true });
  const context = await browser.newContext({
    userAgent: 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) ' +
               'AppleWebKit/537.36 (KHTML, like Gecko) Chrome/124.0.0.0 Safari/537.36',
  });
  const page = await context.newPage();

  // 1. Try Google first
  await page.goto(`https://www.google.com/search?q=${encodeURIComponent(query)}`);

  // 2. Detect blocking — captcha, "unusual traffic" page, empty results
  const blocked = await page.locator('text=/unusual traffic|captcha|are you a robot/i').count();
  if (blocked > 0) {
    console.log('Google blocked — switching to DuckDuckGo');
    await page.goto(`https://duckduckgo.com/?q=${encodeURIComponent(query)}`);
  }

  // 3. Random jitter between requests so the cadence doesn't look automated
  await page.waitForTimeout(2000 + Math.random() * 3000);

  return page;
}
```

That fallback alone — Google → DuckDuckGo when blocked — took the script from "dies after 30 searches" to "ran for six hours unattended." DuckDuckGo doesn't have Google's anti-bot infrastructure, the SERP layout is simpler to parse, and for a query like "dentist 94110" the result quality is essentially equivalent.

The second adaptation was subtler. The phone number was visible on the SERP for about 70% of dentist listings — Google pulls it out of structured data and shows it inline. The naive scraper would happily grab that visible number and move on. The problem: that number is sometimes a marketing tracking number, not the dentist's actual line.

So Claude updated the logic: even when a phone is visible on the SERP, click through to the dentist's own contact page and grab the number from there. Slower per-record. Higher accuracy. The kind of decision a careful human would make and a sloppy automation would skip.

The full collection script ran for under three hours, hit ~430 California dentists across five zip codes, and produced a CSV with name, address, phone, website, and (where available) office hours. Cost in API tokens, with Playwright CLI managing the browser instead of MCP: about $4.20.

Two practical rules I now apply to every scraping job:

1. **Always have a fallback search engine.** Google is the best source until it's the worst source. Your script should detect the transition without you babysitting it.
2. **Distrust the SERP for any data point that has commercial value.** Phone numbers, prices, hours — click through and verify. The extra latency is cheaper than a contact list full of dead ends.

If you're building anything more complex than this, my [WebMCP for Chrome AI agents](https://www.mejba.me/blog/webmcp-chrome-ai-agents) post covers the alternative when you need Chrome-specific protocol features Playwright doesn't expose.

## Pattern 3: Persistent Login Sessions — The Logged-In Bot

This is the pattern that actually changed what I think Claude Code can do.

Scraping public pages is easy. Anything behind a login is the real frontier — and most of the interesting work happens behind a login. Slack channels. School platforms. Internal dashboards. SaaS products you pay for. The challenge isn't logging in once. It's *staying* logged in, across runs, across days, across browser restarts, without you re-typing credentials every time.

Playwright's persistent context is the answer, and most tutorials get it wrong because they conflate `storageState` with `launchPersistentContext`. They are not the same thing.

**`storageState`** is a snapshot of cookies + localStorage, dumped to a JSON file. Good for headless CI runs where you log in once, save the state, and reuse it across hundreds of test runs.

```typescript
// Save after a successful login
await page.context().storageState({ path: 'auth.json' });

// Reuse in later runs
const context = await browser.newContext({ storageState: 'auth.json' });
```

**`launchPersistentContext`** is a real browser profile on disk. Cookies, cache, localStorage, IndexedDB, service worker registrations, the works. This is what you want when you need to behave like an actual logged-in user across sessions — not just a logged-in test runner.

```typescript
import { chromium } from 'playwright';

const userDataDir = '/Users/mejba/.playwright-profiles/school-platform';
const context = await chromium.launchPersistentContext(userDataDir, {
  headless: false, // first run only — log in by hand
  viewport: { width: 1440, height: 900 },
});

const page = context.pages()[0] ?? await context.newPage();
await page.goto('https://school.example.com');
// Log in manually, complete 2FA, dismiss any onboarding modals
// Then close the browser. The profile is now persisted.
```

The hand-off pattern is the part that took me a couple of evenings to get right. **First run: headed, manual login, manual 2FA, manual everything-the-bot-can't-do.** Subsequent runs: same `userDataDir`, but headless, and you're already authenticated. The cookies, the session tokens, the device-fingerprint stuff — all of it lives on disk in that profile directory.

For a school-platform automation I built — a daily script that pulls posts marked as "wins" by classmates, ranks them by recency, and likes the top five — the run looks like this:

```typescript
import { chromium } from 'playwright';

(async () => {
  const context = await chromium.launchPersistentContext(
    '/Users/mejba/.playwright-profiles/school-platform',
    { headless: true }
  );
  const page = await context.newPage();

  await page.goto('https://school.example.com/channels/wins');

  // Filter by newest — the platform's UI tab
  await page.getByRole('tab', { name: 'Newest' }).click();
  await page.waitForLoadState('networkidle');

  // Scroll until we have 30 posts loaded
  for (let i = 0; i < 5; i++) {
    await page.mouse.wheel(0, 2000);
    await page.waitForTimeout(800);
  }

  // Like the top 5 — but throttled, because the platform crashed
  // when I tried to like 5 in 2 seconds during my first run
  const likeButtons = await page
    .locator('[data-testid="like-button"]')
    .filter({ hasNotText: 'Liked' })
    .all();

  for (const button of likeButtons.slice(0, 5)) {
    await button.click();
    await page.waitForTimeout(1500); // throttle
  }

  await context.close();
})();
```

The throttle in the loop is there because of a real bug I caused. My first version of the script clicked all five likes inside a `Promise.all`. The platform's frontend isn't built to handle five concurrent like-mutations from the same session and it crashed the React tree mid-render. Claude figured that out by reading the screenshot of the broken state, finding the React error overlay, reading the stack trace, and deciding the fix was a delay rather than retry logic.

That iterative loop — break, screenshot, read the error, hypothesize, fix, re-run — is the exact same loop as Pattern 1. Different domain, identical shape.

## Connecting To A Browser Already Running (CDP)

There's a third option for the "logged-in" problem that's worth knowing about even if you don't use it often: connect Playwright to a Chrome instance you started yourself.

Launch Chrome with the debugger port open:

```bash
/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome \
  --remote-debugging-port=9222 \
  --user-data-dir=/tmp/chrome-debug-profile
```

Then have Claude Code connect to it from a Playwright script:

```typescript
import { chromium } from 'playwright';

const browser = await chromium.connectOverCDP('http://localhost:9222');
const context = browser.contexts()[0]; // attach to the existing default context
const page = context.pages()[0] ?? await context.newPage();
```

When this is useful: you've manually navigated through a complex multi-step auth flow (SSO redirects, hardware key prompts, captchas) and you want the agent to take over from where you stopped. Headed mode, real browser, real cookies, no profile copying. The limitation: CDP only works for Chromium-based browsers — not Firefox or WebKit.

I use this maybe one in twenty automations. But the times I do, nothing else solves the same problem.

## Headed vs Headless — A Rule, Not A Preference

The default in CI is headless. The default during development should be **headed for the first run of any new automation, then headless once it's stable.**

The reason is debugging asymmetry. When a headless run fails, you have a screenshot, a trace, and your imagination. When a headed run fails, you can see *the actual browser making the actual mistake* in real time. The difference between those two debugging experiences is the difference between fixing a bug in twenty minutes and fixing it in three hours.

The flag is just `--headed`:

```bash
npx playwright test --headed --debug
```

`--debug` adds the inspector — pause on every action, step through, modify selectors live. Use it once. You'll never go back to print-debugging Playwright again.

The exception: any time the agent is running unattended, it must be headless. Headed runs need a display server, get killed when your session ends, and add real overhead. The one-line flip is what you want — headed during build, headless during run.

## What I Won't Use Playwright CLI For

This is the part most posts skip. Three things I tried and walked back on.

**Heavy network/performance debugging.** Playwright CLI can capture network logs and traces, but for serious debugging — comparing waterfall timings, profiling JavaScript hot paths, inspecting CDP-level events — Chrome DevTools MCP is genuinely better. The execute tool batches actions into a single call and the CDP-native data is richer. I keep both installed and reach for DevTools MCP when the question is "why is this page slow" rather than "did this page work."

**Anything inside iframes from a different origin.** Playwright handles cross-origin iframes, but the API gets ugly fast — `frameLocator` chains, careful `waitFor` placement, and a permanent suspicion that the selectors won't survive the next deploy. For ad widgets, embedded Stripe/Plaid flows, or social login popups, I either intercept at the network layer or just don't automate that step. The cost-benefit isn't there.

**Email-confirmation flows.** Playwright will happily click the link in the email if you give it the link. The hard part is *getting* the link. Mailbox APIs (Mailtrap, Mailosaur) solve this; Playwright doesn't. Trying to scrape Gmail for the verification email is a path to pain.

The honest summary: Playwright CLI is the right default for browser automation inside Claude Code in 2026. It is not the right tool for performance work, exotic embed flows, or email plumbing. Knowing where the edges are saves you from learning them at 2 AM.

## Scheduling The Bots — Modal, Trigger, And The Desktop Cron Trap

A browser automation that only runs when you remember to run it is a demo. Production browser automations run on a schedule.

Three options, each suited to a different reality:

**Modal** — serverless Python with first-class Playwright support. You define a function with `@modal.function(schedule=modal.Cron("0 9 * * *"))` and Modal handles the container, the browser binaries, the run isolation, the logs. My daily news roundup bot runs here. About $0.40 per day in compute.

**Trigger.dev** — TypeScript-native, JS ecosystem feels closer if your Playwright scripts are already TS. Their browser-task primitive is built specifically for Playwright workloads.

**Desktop cron + headless** — for personal automations on your own laptop. Works. Falls over the moment your laptop sleeps, the wifi flips, or the browser updates. Don't use it for anything that matters.

I started everything on desktop cron because it was free. I migrated to Modal after the third missed run during a flight. Total monthly bill across four scheduled bots: under $25. Worth it.

## The Agent Layer On Top

Once Playwright CLI is wired up and your first three patterns work, the temptation is to keep building bigger scripts. Don't. The next move is to build *agents* that call the scripts.

A daily-news-roundup agent that:
1. Wakes up at 8 AM
2. Pulls headlines from three RSS feeds via plain HTTP (no browser needed)
3. Asks Claude to summarize and rank them
4. Calls a Playwright CLI script to post the summary into a Slack channel
5. Watches the channel for replies for the next thirty minutes via persistent session
6. Asks Claude to draft responses to anything that needs one
7. Calls another Playwright script to post the responses

Each piece is small. The browser parts are tiny — a click, a type, a screenshot, an exit. The agent reasoning lives outside the browser. The browser is just hands.

This separation is the whole point of preferring CLI over MCP for these workflows. The browser layer should be cheap, fast, and dumb. The reasoning layer should be where the tokens go. When the browser layer is also chewing through tokens — which is what Playwright MCP does, on every page — the math stops working at any non-trivial scale.

The closest framing I can give you: Playwright CLI is to browser automation what bash is to filesystem automation. Small, sharp, scriptable. Easy to compose into something larger. Forgettable in the way good infrastructure is supposed to be.

## Frequently Asked Questions

### Is Playwright CLI a replacement for Playwright MCP?
No — Playwright CLI is a companion tool, not a replacement. Use CLI when a coding agent like Claude Code is driving the browser; use MCP when an autonomous agent workflow needs the standard MCP protocol. Microsoft maintains both deliberately.

### How much does Playwright CLI actually save in tokens vs Playwright MCP?
Public benchmarks and my own runs show roughly 4x fewer tokens per session with Playwright CLI vs Playwright MCP. The savings come from CLI saving snapshots to disk as compact YAML instead of streaming full accessibility trees into the model context on every interaction.

### Can Playwright CLI keep me logged in across runs?
Yes — use `chromium.launchPersistentContext(userDataDir, { headless: false })` for the first manual login, then run subsequent automations with the same `userDataDir` in headless mode. Cookies, localStorage, and session tokens all persist on disk.

### When should I use Chrome DevTools MCP instead?
Reach for Chrome DevTools MCP when the task is performance profiling, network waterfall analysis, or any deep CDP-level debugging. It's also more token-efficient than Playwright MCP for those specific workloads. For straight automation and QA loops, Playwright CLI wins.

### Can Claude Code write Playwright scripts from scratch?
Yes — and it's reliable. Use `npx playwright codegen <url>` to record an initial session if you want to seed the script, then let Claude refine it. For most automations I describe the goal in 2-3 sentences and the working script is written before I finish my coffee.

## The Eleven Minutes That Changed How I Build

Back to the onboarding form. Eleven minutes, three real bugs found and fixed, zero keystrokes from me. The thing I keep thinking about isn't the speed — it's that the loop *closed itself*. The agent didn't write code and stop. It wrote code, ran the test, saw the failure, traced the cause, applied the fix, and re-ran the test until the result matched the goal.

That closed loop is what makes browser automation finally feel like infrastructure instead of theater. Playwright CLI didn't invent the loop. It made it cheap enough to run.

Pick one of the three patterns from this post tonight. The QA loop is the easiest to start with — point Claude at any form-heavy app you maintain, give it the four-sentence instruction from earlier, walk away for ten minutes. Come back. See what it found. The first time the loop catches a real bug you didn't know about, you'll understand why I changed how I build.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
