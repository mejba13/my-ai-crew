**BRAND:** mejba.me
**TITLE:** Agent Skills in Claude Code: The Advanced Playbook
**SLUG:** agent-skills-advanced-claude-code
**PRIMARY KEYWORD:** Claude Code agent skills
**SECONDARY KEYWORDS:** Claude Code skills advanced workflows, dynamic context injection Claude, Claude Code background agents
**META DESCRIPTION:** I tested every advanced Claude Code skill feature — context forking, dynamic injection, background agents, and automated evals. Here's what actually works.
**TAGS:** AI Agents, Claude Code, Developer Tools, Agent Skills, Deep Dive
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand how to build, test, and deploy advanced Claude Code skills using context forking, dynamic injection, and automated evaluation — and know when each pattern is worth the complexity.

---

The skill broke my main session. Not a soft failure — not a "hmm, that didn't look right" situation. I'm talking about a 47,000-token context window consumed in eight seconds because a research skill I'd written tried to pull an entire GitHub repository's issue history into the conversation. Claude stopped responding mid-sentence. The session was toast.

That was three weeks ago. I was trying to build a skill that would gather competitive intelligence before writing a technical article. Simple idea: query GitHub, pull recent issues, summarize trends. The skill worked — technically. But it worked the way a fire hose works when you're trying to fill a coffee cup. All the data arrived, and it destroyed everything around it.

I spent the next week rebuilding that skill using three features I'd been ignoring: context forking, dynamic context injection, and background agents. The same skill now runs in its own isolated session, pre-processes data through shell scripts before Claude ever sees it, and works asynchronously while I keep coding in my main window. Token cost dropped from 47,000 to about 3,200. Same output quality. Different universe of efficiency.

That experience taught me something I hadn't read in any documentation or tutorial: the basic skill architecture — a folder with a SKILL.md file — is the starting line, not the finish line. The advanced features sitting on top of that foundation are where skills go from "clever prompt shortcut" to "genuine autonomous workflow." And most developers building skills right now haven't touched them.

This is everything I've learned about the advanced side of Claude Code skills — the parts that took me from building toys to building systems.

---

## Where Skills Are Now (And Why the Basics Aren't Enough)

If you've read my earlier breakdown of [how agent skills work at a fundamental level](/claude-code-agent-skills-guide), you know the core concept: a skill is a folder containing a SKILL.md file with YAML frontmatter and markdown instructions. Claude loads the metadata, sees what skills are available, and pulls the full content only when a skill is relevant to the current task. Progressive disclosure. Context efficiency. Modular expertise.

That architecture is genuinely good. I still use basic skills every day — my commit message formatter, my code review checklist, my deployment pre-flight script. They're simple, they work, and they save me 10-15 minutes per use.

But I kept hitting walls.

The research skill fiasco was the dramatic example, but the quieter failures were more instructive. A skill that needed current project state — which files changed, which branch I'm on, what dependencies are installed — but could only access static instructions written at skill-creation time. A skill that ran a complex multi-step workflow and polluted my main conversation with 30 messages of intermediate output I didn't care about. A skill that worked brilliantly with Opus 4 but started hallucinating steps after I switched to Sonnet 4.6.

Each of these failures pointed at the same gap: basic skills are static. They encode knowledge, but they can't adapt to live context, isolate their work, or prove they're still functioning correctly as models evolve.

Anthropic clearly saw the same gap. The skill system as of March 2026 includes features that address every one of those problems. The challenge is that these features are documented across different pages, explained in different contexts, and — honestly — not always obvious in their power until you've hit the specific wall they solve.

I'm going to walk through each one in the order I wish I'd learned them.

---

## Skill Types: Pick the Right Architecture Before You Write a Line

Before building any advanced skill, you need to answer one question: what kind of problem is this skill solving? Anthropic categorizes skills into two types, and choosing wrong means building something that's either overcomplicated or underpowered.

### Capability Uplift Skills

These give Claude abilities it doesn't have out of the box. My Airtop browser automation skill is a pure capability uplift — Claude can't control a cloud browser natively, but with the skill installed, it can navigate websites, fill forms, extract data, and handle authenticated sessions. The [Airtop integration](https://www.airtop.ai/connect/claude-code) compiles plain-English instructions into deterministic code that runs against a real Chromium instance in the cloud.

Other capability uplift examples I use daily: a skill that runs Lighthouse audits and feeds the scores back into the conversation. A skill that queries my Airtable project database. A skill that generates and renders Remotion video compositions.

The pattern is consistent — capability uplift skills include executable scripts (bash, Python, Node) that Claude invokes as tools. The markdown instructions tell Claude *when* and *how* to use the tool. The script does the actual work.

### Encoded Preference Skills

These don't add new capabilities. They encode specific workflows, conventions, and decision-making patterns. My code review skill doesn't give Claude any new tools — Claude can already read code and suggest improvements. The skill encodes *my team's* review standards: check for N+1 queries first, verify that all public methods have docblocks, flag any raw SQL that isn't parameterized, ensure error responses follow our API contract.

The distinction matters because it determines complexity. Capability uplift skills need scripts, tool definitions, sometimes MCP server configurations. Encoded preference skills are pure markdown — often just 50-200 lines of precise instructions. If you're building an encoded preference skill and find yourself writing shell scripts, you've probably misidentified the type.

I made this mistake twice before it stuck. Built an elaborate skill with a Python script to "analyze" my codebase's testing patterns, when all I actually needed was a markdown file telling Claude "when writing tests for this project, use Pest instead of PHPUnit, mock external services with Http::fake(), and always test the sad path first." The markdown-only version was faster to build, faster to load, and produced better results because Claude wasn't context-switching between instruction-following and tool-calling.

That said — the most powerful skills I've built combine both types. The research skill I eventually fixed? Capability uplift for data gathering (shell scripts that query GitHub API), encoded preferences for analysis (how to weight different signals, what format to output). Hybrid skills are where things get interesting, and where the advanced features start earning their complexity.

---

## Context Forking: Give Skills Their Own Room to Think

This is the feature that saved my research skill. And it's the one I think most developers should learn first.

Here's the problem context forking solves. When a skill runs in your main Claude session, every token it generates — every intermediate step, every piece of data it processes, every reasoning chain it follows — lives in your main context window. A skill that does 15 steps of work before producing a final answer has dumped 15 steps of context you didn't ask for into your conversation. Your next prompt now has to compete with all that noise for Claude's attention.

I measured this on a real project last month. My main session was at roughly 12,000 tokens when I triggered a skill that analyzed my package.json, checked for outdated dependencies, cross-referenced known vulnerabilities, and produced a prioritized update plan. By the time the skill finished, my context window held 38,000 tokens. The skill's output — the part I actually wanted — was about 800 tokens. The other 25,200 tokens were working memory I'd never look at again. And they'd be sitting there, consuming capacity and slightly degrading Claude's focus, for the rest of the session.

Context forking eliminates this entirely. You add one line to your skill's YAML frontmatter:

```yaml
---
name: dependency-audit
description: Analyzes project dependencies for security issues and updates
context: fork
---
```

That `context: fork` directive tells Claude Code to spawn a dedicated subagent for this skill. The subagent gets its own context window, its own conversation history, and the full skill content injected at startup. It does all its work in isolation, then returns only the final result to your main session.

Same dependency audit skill, same project. Main session cost: about 1,200 tokens (the request and the returned summary). The subagent consumed its own 26,000 tokens in its own window, which got cleaned up after the skill completed.

The mental model that helped me: think of context forking like running a function in a separate thread versus inline. The inline version works, but it blocks your main thread and leaves its stack frames lying around. The forked version runs independently and returns a clean result.

### When to Fork (And When Not To)

I fork anything that involves more than 3-4 steps of intermediate processing. Short skills — formatting a commit message, generating a migration file, checking a single configuration — run fine in the main context. The overhead of spawning a subagent isn't worth it for a skill that generates 500 tokens of working memory.

But anything that does research, analysis, multi-file processing, or complex reasoning? Fork it. The context savings compound across a workday. I used to restart Claude sessions every 2-3 hours because the context got too cluttered. With forked skills handling the heavy work, I regularly go full 8-hour sessions without degradation.

One thing the docs don't emphasize enough: forked skills can specify which agent model to use. I run my main session on Opus 4.6 for its deep reasoning, but my simpler forked skills — data gathering, formatting, file organization — run on Sonnet 4.6 at a fraction of the cost. You set this in the frontmatter:

```yaml
---
name: format-changelog
description: Generates a formatted changelog from git history
context: fork
model: sonnet
---
```

A single research skill that used to cost me $0.40 per run on Opus now costs $0.06 because the data-gathering fork runs on Sonnet and only the final analysis step uses Opus in my main session. Over a month of daily use, that's real money.

---

## Dynamic Context Injection: Skills That Know What's Happening Right Now

Static instructions break the moment your project state matters. I learned this building a deployment skill that needed to know the current git branch, the last commit hash, which environment variables were set, and whether any migrations were pending. I could hardcode *instructions* about how to check these things, but the skill would spend tokens having Claude run commands and process output that I could've gathered in advance.

Dynamic context injection flips the model. Instead of Claude discovering project state at runtime, a pre-processing script gathers the data *before the skill loads*, and injects it directly into the skill's content. Claude receives the skill with live data already embedded. No discovery step. No wasted tokens.

The syntax uses `!command` placeholders in your SKILL.md:

```markdown
---
name: deploy-preflight
description: Runs deployment readiness checks for current branch
context: fork
---

## Current Project State

- **Branch:** !git rev-parse --abbrev-ref HEAD
- **Last commit:** !git log -1 --oneline
- **Uncommitted changes:** !git status --porcelain | wc -l
- **Pending migrations:** !php artisan migrate:status --pending 2>/dev/null || echo "N/A"
- **Node version:** !node --version
- **Environment:** !echo $APP_ENV

## Deployment Checklist

Based on the current state above, verify the following...
```

When this skill activates, Claude Code runs each `!command` shell command, captures the output, and replaces the placeholder with the actual result. Claude sees:

```
- **Branch:** feature/payment-gateway
- **Last commit:** a3f2c91 Add Stripe webhook handler
- **Uncommitted changes:** 3
- **Pending migrations:** 2 pending
```

Not the commands. The results. Claude starts reasoning about deployment readiness from actual project state instead of spending tokens discovering it.

The token savings are significant — my deploy-preflight skill dropped from about 4,800 tokens (with Claude running discovery commands) to 1,900 tokens (with pre-injected state). But the bigger win is accuracy. When Claude discovers state itself, it sometimes misparses command output, skips a check if it's "confident enough," or gets sidetracked by an unexpected response. With pre-injected data, every piece of state is guaranteed to be present and correctly formatted.

### Combining Injection with Forking

The real power move is using both together. My competitive research skill — the one that started this whole journey — now looks like this:

```yaml
---
name: competitive-research
description: Analyzes GitHub activity and trends for a given technology
context: fork
model: sonnet
---

## Research Context

Target technology: {{topic}}
Date range: !date -v-30d +%Y-%m-%d to !date +%Y-%m-%d

## Recent Activity
!gh api search/repositories --jq '.[0:5] | .[] | "\(.full_name) - \(.stargazers_count) stars - \(.description)"' -q "{{topic}} pushed:>$(date -v-30d +%Y-%m-%d)"

## Trending Issues
!gh api search/issues --jq '.[0:10] | .[] | "[\(.repository_url | split("/") | last)] \(.title)"' -q "{{topic}} is:issue created:>$(date -v-7d +%Y-%m-%d)"
```

The shell commands pre-process the GitHub API queries. The `context: fork` runs everything in an isolated subagent on Sonnet. My main Opus session receives a clean summary. Total main-session token cost: about 900 tokens. Total actual work done: equivalent to what used to blow up my 47,000-token context window.

That's not an incremental improvement. That's a fundamentally different capability.

---

## Background Agents: Skills That Work While You Work

Some skills take time. A comprehensive security audit of a codebase. A full test suite execution with analysis. Scraping and summarizing 20 pages of documentation. These workflows might run for 3-5 minutes — an eternity when you're waiting for your terminal to become usable again.

Background agents solve this by letting skills run asynchronously. You trigger the skill, it spawns a background agent, and your main session stays responsive. When the background agent finishes, it surfaces the results.

I use this daily for my test analysis skill. After pushing a feature branch, I trigger the skill:

```
/skill test-analysis
```

The skill forks into a background agent, runs the full test suite, captures any failures, analyzes coverage reports, and drafts a summary. Meanwhile, I'm already working on the next feature in my main session. Three minutes later, the background agent pops back with:

```
Test Analysis Complete:
- 247 tests passed, 2 failed
- Failed: OrderCalculation::test_discount_stacking (assertion mismatch on line 89)
- Failed: PaymentGateway::test_timeout_retry (mock not returning expected response)
- Coverage: 78.3% (down 0.4% from main branch)
- Suggestion: The discount stacking failure looks like a rounding issue...
```

The workflow is similar to how you'd use `Ctrl+B` to background a running subagent — the agent keeps working independently even after your main task continues. The `/tasks` command gives you a dashboard of everything running in the background, with IDs you can use to check status, view details, or cancel.

Here's what nobody tells you about background agents: they're most valuable not for the time savings, but for the context isolation. Before I used background agents, I'd run my test suite in Claude's main session and get back 200+ lines of test output that I had to mentally filter. The background agent processes all that noise in its own context and hands me back signal.

If you'd rather have someone build this kind of multi-agent skill system from scratch, I take on Claude Code automation engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

---

## Installing Skills: The Ecosystem Is Bigger Than You Think

I should back up and talk about getting skills into your system, because the installation story has evolved fast.

### The Plugin Command

The primary method is Claude Code's built-in plugin system:

```bash
/plugin install <skill-name>@<marketplace>
```

For Anthropic's official skills repository:

```bash
/plugin install document-skills@anthropic-agent-skills
```

You can also register third-party marketplaces:

```bash
/plugin marketplace add alirezarezvani/claude-skills
```

After adding a marketplace, every skill in that repository becomes installable with a single command.

### The skills.sh Ecosystem

The [skills.sh](https://skillsmp.com/) ecosystem has exploded since I [first wrote about it](/skills-sh-claude-code-agents). As of March 2026, there are over 7,000 evaluated skills across multiple marketplaces, compatible not just with Claude Code but also with Codex CLI, Gemini CLI, Cursor, and Antigravity IDE. The universal SKILL.md format means a skill you build for Claude works across all of these tools.

The best discovery method I've found: browse [SkillHub](https://www.skillhub.club) or the [VoltAgent awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) repository, which curates skills from official dev teams and the community. Filter by category, check ratings, and install with one command.

### Manual Installation

For skills you build yourself — which is where the real power is — you have two paths:

**Project-scoped:** Drop the skill folder into `.claude/skills/` in your repository. Every team member who clones the repo gets the skill automatically. Version controlled, shared, consistent.

**Personal:** Put it in `~/.claude/skills/` for skills you want available across all projects. My formatting skills, my general code review standards, my writing style guide — these live in my personal directory because they're not project-specific.

The folder structure is straightforward:

```
.claude/skills/
  deploy-preflight/
    SKILL.md          # Frontmatter + instructions
    scripts/
      check-deps.sh   # Optional executable scripts
    templates/
      deploy-log.md   # Optional reference files
```

Claude sees the skill name and description from the frontmatter. The full content loads only when triggered. Scripts in the folder become tools the skill can invoke.

---

## Building Skills Manually: The Patterns That Actually Work

I've built about 40 skills over the past four months. Most of the early ones were mediocre. The ones I build now are dramatically better, and the difference comes down to three patterns I had to learn through failure.

### Pattern 1: Instruction Specificity Over Instruction Volume

My first skills were 500+ lines of detailed instructions. "If the user asks about X, do Y. If they mention Z, make sure to check W first. In cases where..." Exhaustive. And terrible.

The problem: long skills burn context tokens even after forking, and Claude gets worse at following instructions as the instruction set grows. There's a sweet spot — and in my testing, it's between 80 and 200 lines of markdown for the instruction section.

The fix was writing instructions the way I'd brief a senior engineer, not a junior one. Instead of spelling out every step, I describe the goal, the constraints, and the quality bar. Claude's reasoning fills in the gaps.

Bad:
```markdown
1. First, open the file
2. Then find all functions
3. For each function, check if it has a docblock
4. If it doesn't have a docblock, generate one
5. The docblock should include @param tags
6. The @param tags should list the type first, then the name
7. After the @param tags, add a @return tag
...
```

Good:
```markdown
## Task
Add PHPDoc blocks to all public methods missing them.

## Standards
- Follow PSR-5 PHPDoc format
- Infer parameter types from type hints; fall back to mixed
- Include @throws if the method contains throw statements
- Keep descriptions to one sentence — if you need two, the method needs refactoring
```

The good version is 6 lines instead of 30+. It produces better output because it gives Claude room to reason about each specific case rather than mechanically following a checklist.

### Pattern 2: Exit Conditions, Not Just Entry Conditions

Every skill's frontmatter has a description that tells Claude *when to activate*. Most skill builders stop there. But telling Claude when the skill is *done* is equally important.

Without explicit exit conditions, skills tend to over-deliver. My SEO audit skill used to keep finding "one more thing to check" until it had consumed 15,000 tokens of analysis for a single page. Adding exit conditions fixed this:

```markdown
## Completion Criteria
- All five audit categories scored (Performance, Accessibility, SEO, Best Practices, Content)
- Critical issues listed (severity: high only)
- Three specific, actionable recommendations provided
- Total output: under 500 words

Stop when these criteria are met. Do not continue analyzing.
```

### Pattern 3: Error Recovery Instructions

Skills that call external tools — APIs, shell commands, database queries — will encounter failures. If you don't tell Claude how to handle failures, it either halts awkwardly or starts improvising, which is often worse.

Every capability uplift skill I build now includes a failure mode section:

```markdown
## When Things Go Wrong

**API returns 401:** The token has expired. Tell the user to run `gh auth refresh` and retry.
**API returns 404:** The repository doesn't exist or is private. Ask the user to verify the repo name.
**Command timeout (>30s):** The network request is hanging. Suggest checking VPN status or retrying with `--timeout 60`.
**Empty results:** This is valid — not every query has results. Report "no data found" rather than guessing.

Never fabricate data if a command fails. Report the failure and suggest a fix.
```

This single pattern eliminated about 80% of the unpredictable behavior I was seeing in my skills.

---

## Skill Testing and Evaluation: The Part Everyone Skips

I'm going to be direct about this: if you're building skills without testing them, you're building skills that will break without warning.

I skipped testing for my first 20 skills. Then Claude got a model update, and 6 of them started producing worse output. Not broken — worse. Subtle degradation. My code review skill stopped catching N+1 queries. My deployment skill started skipping the migration check. I didn't notice for a week because the skills still *ran* — they just ran poorly.

This is why Anthropic built the Skill Creator.

### The Skill Creator: A Skill That Builds and Tests Other Skills

The [Skill Creator](https://www.geeky-gadgets.com/anthropic-skill-creator/) is a meta-skill — a skill that automates the development, testing, and evaluation of other skills. Install it as a plugin, and you get access to two core workflows:

**Eval mode:** Define test prompts, describe expected outputs, and the Skill Creator runs your skill against each prompt and grades the results. It evaluates across five dimensions: frontmatter completeness, description quality, instruction clarity, explanation depth, and eval pass rate.

```
eval: Execute skill on test prompts → Grade outputs → Report scores
```

**Improve mode:** Everything in eval mode, plus blind A/B comparisons, automated analysis, and iterative refinement. The Skill Creator runs two Claude instances — one with your skill, one without — gives them the same prompt, and compares outputs. If the skill version doesn't meaningfully outperform the vanilla version, you know the skill isn't adding value.

```
improve: Execute → Grade → Blind A/B compare → Analyze → Iterate
```

### How I Test My Skills Now

Every skill I build goes through this pipeline before I consider it done:

**Step 1: Define 3-5 test prompts.** These should cover the skill's happy path, an edge case, and at least one adversarial case (a prompt that's close to the skill's domain but shouldn't trigger it).

**Step 2: Run eval mode.** Check that all test prompts produce expected outputs. If any fail, revise the skill instructions.

**Step 3: Run the A/B test.** This is the step that changed my perspective on skill building. I had a skill that I *thought* was brilliant — a code review skill with detailed standards. The A/B test showed that vanilla Claude (no skill) produced equally good reviews for 3 out of 5 test cases. My "brilliant" skill was only adding value for edge cases involving database queries and API error handling. I rewrote it to focus exclusively on those areas, cut the instruction length by 60%, and the A/B scores flipped to 5 out of 5.

**Step 4: Retest after model updates.** I keep my test prompts in a `tests/` subfolder within each skill directory. After any Claude model update, I re-run the evals. This takes about 10 minutes for my full skill library and has caught degradation three times in the past two months.

The evaluation system runs each test in parallel, in isolated contexts, so skills can't interfere with each other during testing. That isolation matters — I once had a skill test pass when run individually but fail when run alongside other skills because of a naming collision in the frontmatter.

This testing discipline feels like overhead until the first time it saves you. Then it feels like the only responsible way to build skills.

---

## Real Integration: Airtop Cloud Browser Automation

Theory is useful. Let me show you what a fully advanced skill looks like in production.

The skill I'm most proud of right now is my Airtop browser automation skill. [Airtop](https://www.airtop.ai/connect/claude-code) provides a real cloud browser — not a headless scraper, not a simulated DOM, an actual Chromium instance running in the cloud that can handle JavaScript-heavy sites, authenticated sessions, and dynamic content.

Here's why this matters: half the automations I want Claude to run involve websites that don't have APIs. Competitor pricing pages. Vendor portals. Government compliance databases. Job boards. CRMs. There's no API to query — the only interface is a browser.

The Airtop skill solves this by combining all the advanced features:

```yaml
---
name: airtop-research
description: Automates web research using a real cloud browser via Airtop
context: fork
model: sonnet
---
```

It forks into its own context (because browser automation generates verbose intermediate output). It uses dynamic injection to load my Airtop API credentials and the target URLs. It runs Sonnet for the browser interaction steps (cost-effective for mechanical navigation) and returns structured results to my Opus main session for analysis.

A workflow I ran yesterday: monitoring competitor pricing across five SaaS tools. I described what I needed in plain English, the skill compiled it into deterministic automation code, launched cloud browser sessions against each site, extracted pricing tables, and returned a structured comparison. Total time: about 90 seconds. Manual equivalent: 20-30 minutes of tab-switching and copy-pasting.

The compiled automation is the part that surprised me most. Airtop doesn't just replay my instructions — it generates actual code from them, which runs deterministically on subsequent executions. First run is agent-driven and exploratory. Every run after that is fast and reliable. It's a pattern I haven't seen in other browser automation tools, and it makes the difference between "cool demo" and "production workflow."

---

## The Merge of Skills and Custom Commands

One architectural shift worth noting: skills and custom slash commands in Claude Code are converging. Earlier versions treated them as separate systems — commands were quick shortcuts, skills were deeper knowledge modules. As of Claude Code 2.1, the line has blurred.

A skill can now register itself as a slash command through the frontmatter:

```yaml
---
name: security-audit
description: Full OWASP-aligned security review of the current project
command: /audit
context: fork
---
```

Typing `/audit` in Claude Code triggers the full skill with all its advanced features — forking, dynamic injection, background execution. No need to maintain separate command configurations.

This convergence matters because it reduces the cognitive load of managing your toolkit. I used to maintain a mental map of "which things are skills and which things are commands." Now everything is a skill, some of which have command shortcuts. One system. One location. One testing framework.

The [claude-skills-guide-decoded](/claude-skills-guide-decoded) post I wrote earlier covers the foundational SKILL.md format in depth. What's changed since then is that the format now supports significantly more frontmatter options — `context`, `model`, `command`, tool restrictions, and lifecycle hooks — turning a simple markdown file into a full agent configuration.

---

## What I Got Wrong (And What I'd Do Differently)

I want to close with the mistakes, because they're more useful than the successes.

**Mistake 1: Over-forking.** After discovering context forking, I applied it to everything. A 3-line formatting skill that generated 200 tokens of output? Forked. The overhead of spawning a subagent exceeded the context cost of just running it inline. Fork skills that generate 2,000+ tokens of working memory. Let simple skills run in your main session.

**Mistake 2: Dynamic injection without caching.** Some of my `!command` calls were hitting APIs on every skill activation. If you trigger a skill 10 times in an hour, that's 10 identical API calls. I now use shell commands that check for a cached result first and only query live if the cache is stale. Simple pattern, obvious in retrospect, but I burned through API rate limits before figuring it out.

**Mistake 3: Building skills for things Claude already does well.** The A/B testing framework taught me this: if vanilla Claude handles the task at 80%+ quality without a skill, the skill isn't worth maintaining. Skills should target the gaps — the project-specific conventions, the external tool integrations, the domain knowledge Claude doesn't have. Building a skill for "write good Python code" is wasted effort. Building a skill for "write Python code that follows our team's decorator pattern for FastAPI endpoints with structured logging" is a real improvement.

**Mistake 4: Ignoring the testing story until it was too late.** Six broken skills after a model update. That's what it took. Build the eval suite alongside the skill, not after.

The honest assessment of where skills are right now: the system works. The advanced features — forking, injection, background agents, automated evals — transform skills from "clever prompt templates" into genuine workflow automation. The tooling is still early enough that you'll hit rough edges (error messages from forked skills could be much more descriptive, and the A/B testing setup requires more manual configuration than it should). But the architecture is sound, and the ecosystem is moving at a pace I haven't seen since the early npm days.

Seven thousand skills on SkillsMP alone, and the format works across Claude Code, Codex CLI, Gemini CLI, and Cursor. This isn't a Claude-specific feature anymore. It's an industry standard forming in real time.

The question isn't whether to invest in learning the advanced skill features. It's whether you can afford to keep building agents without them.

---

## Frequently Asked Questions

### What is context forking in Claude Code skills?

Context forking spawns a dedicated subagent with its own context window to execute a skill in isolation. Add `context: fork` to your SKILL.md frontmatter to prevent heavy skills from polluting your main conversation. For the full walkthrough, see the Context Forking section above.

### How do I install Claude Code skills from the marketplace?

Run `/plugin install <skill-name>@<marketplace>` in Claude Code, or add a third-party marketplace with `/plugin marketplace add <repo>`. Skills install to `.claude/skills/` for project-scoped use or `~/.claude/skills/` for personal use.

### What is dynamic context injection in Claude Code?

Dynamic context injection uses `!command` syntax in SKILL.md to run shell commands before the skill loads. The command output replaces the placeholder, so Claude receives live project data — git branch, environment variables, API responses — without spending tokens on discovery.

### How do I test Claude Code skills with the Skill Creator?

Install the Skill Creator plugin, define test prompts with expected outputs, and run eval mode. For deeper validation, use improve mode, which runs blind A/B tests comparing Claude's output with and without the skill active. See the Testing and Evaluation section above.

### Can Claude Code skills run in the background?

Yes. Skills with `context: fork` can spawn background agents that work asynchronously while your main session stays responsive. Use `/tasks` to monitor running background skills, check their status, or cancel them.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
