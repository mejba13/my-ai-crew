**BRAND:** mejba.me
**TITLE:** Claude Programmatic SEO: How I Built One Skill That Ships Pages
**META TITLE:** Claude Programmatic SEO: 1 Skill, Hundreds of Pages
**SLUG:** claude-programmatic-seo-skill
**PRIMARY KEYWORD:** Claude programmatic SEO
**META DESCRIPTION:** How I use Claude programmatic SEO with one Skill, one template, and parallel agents to publish hundreds of ranking pages without writing each by hand.
**TAGS:** Claude Code, Programmatic SEO, Claude Skills, AI Automation, Content Strategy

---

The first time I tried programmatic SEO, I built 400 pages in a weekend and watched them all die in Google's index within ninety days.

Not deindexed because of spam flags. Worse. They just sat there in the "Crawled — currently not indexed" bucket, the purgatory where Google tells you your pages exist but aren't worth showing anyone. I had followed every 2022-era pSEO tutorial I could find: scrape a dataset, pipe it through a template, hit publish, wait for the traffic tsunami. The traffic tsunami never arrived. What arrived instead was a Search Console report that read like a coroner's log.

That was in 2023. I rebuilt the whole system from scratch four times since then. The version I'm running now — the one that's actually working on two production sites, pulling real clicks from real search queries — looks almost nothing like those early attempts. It runs on exactly four moving pieces: one topic pattern, one page template, one Claude Skill, and one custom command. Everything else is Claude Opus 4.7 or Sonnet 4.6 doing the work in parallel sessions while I do something else.

The reason it works now and didn't work then isn't mysterious. It's that Claude programmatic SEO, done properly, is no longer "scrape data and fill a template." It's template-plus-judgment-at-scale, and the judgment is what makes or breaks it. Let me show you exactly what I mean, because there's a specific failure mode most tutorials quietly skip past — and once you see it, the whole playbook I'm about to walk you through makes sense.

## Why Old-School Programmatic SEO Stopped Working in 2026

Here's the part nobody selling "build 10,000 pages in a weekend" courses wants to tell you: Google published an explicit policy on what they call "scaled content abuse" in March 2024, and they've been quietly tightening enforcement ever since. The current informal floor for programmatic pages that actually survive indexing is **≥30–40% unique content per page**, with anything under 30% treated as a hard stop risk. That's not a Google employee quote. That's the consensus benchmark I've been seeing across programmatic SEO practitioners who've tracked which of their pages survive core updates and which evaporate.

The old pSEO playbook violated this on purpose. Grab a CSV of 5,000 cities, shove each one into a `{city}` slot in a template, and call it a day. The result was 5,000 pages that were 95% identical. Google's spam algorithms caught up to that in 2019. By 2023 they were actively suppressing it. By 2026 they just don't index it at all.

The reason this matters for **Claude programmatic SEO** specifically is that a naive LLM approach makes the same mistake in a more sophisticated disguise. If you prompt Claude once per page with "write an SEO article about `{keyword}`," you get back 500 pages of plausible-looking content that share the same structural skeleton, the same opening cadence, and the same stock transitions. Google's quality classifiers fingerprint that pattern faster than a human reader does.

What changed in 2026 — and what made me rebuild my system for the fourth time — is that Claude Skills finally gave me a way to enforce *genuine per-page variance* while still running the whole thing from a single template. That's the piece I want you to understand before we touch a single line of code.

A Skill, in the current Claude Code implementation, is a loadable bundle of instructions, reference files, and sub-instructions that activates on demand. It's not a prompt. It's not a project. It's closer to an agent profession. You can pack it with a tone-of-voice document, a data validation checklist, a list of banned phrases, and a required unique-angle generator that fires *before* the template renders. Every page the Skill produces gets forced through that unique-angle step, which means no two pages share the same hook, the same anecdote, or the same structural flow — even though they all come from the same template and the same pattern.

That one architectural shift is the difference between pages that index and pages that die. Everything else in this guide is downstream of it.

## The Four Pieces of a Working Claude Programmatic SEO System

Before I walk through the step-by-step, here's the whole thing in one paragraph so you can hold the shape of it in your head: I run one **topic pattern** that describes a repeatable keyword structure, one **page template** that specifies how each page is built, one **Claude Skill** that packages the template plus all the judgment rules, and one **custom command** that invokes the Skill with the right inputs. Then I dispatch the same command across 6–10 parallel Claude Code sessions using git worktrees, and each session chews through a slice of the keyword list independently.

That's it. That's the whole machine. Every part has a specific job, and none of them is optional.

- **Topic Pattern** — the keyword skeleton. Example: `best [use case] tools for [audience]` or `[framework] vs [framework] for [task]`. One pattern spawns 50–500 keywords.
- **Page Template** — the content blueprint. Defines sections, required elements, unique-angle hook placement, data validation rules, and the conversion funnel.
- **Claude Skill** — the template made executable. Bundles the template plus the tone-of-voice file, the banned-phrase list, the per-page uniqueness enforcer, and any reference data (sitemap, product info, case studies).
- **Custom Command** — the trigger. A slash command like `/pseo-generate "keyword X"` that loads the Skill, runs the research pass, and writes the page. I covered the fundamentals of this kind of setup in my [Claude Code plugins daily workflow breakdown](https://www.mejba.me/claude-code-plugins-daily-workflow) if you want the lower-level mechanics.

The insight that took me three rebuilds to internalize: the Skill isn't the output factory. The Skill is the *quality gate*. The template produces pages. The Skill decides whether those pages are allowed to ship.

Alright. Let's build the thing.

## Step 1: Generate Topic Patterns That Scale Without Turning Into Spam

This is where most people blow it in the first ten minutes. They pick a pattern that *looks* scalable but has no real search demand behind it, or they pick one that has demand but is already saturated by Wise, Zapier, or TripAdvisor — companies running pSEO at a scale you and I physically cannot match. (Wise currently has 8.5 million currency-converter pages indexed, according to their technical SEO team. You are not out-producing Wise.)

What I do instead: I open Claude Opus 4.7 in the chat interface — not Claude Code yet, just the web app — and paste in a prompt that forces it to think like a keyword strategist rather than a writer. The prompt looks roughly like this:

```
My niche: [specific niche — e.g., "Laravel hosting and DevOps"]
My product: [what I'm funneling traffic toward]
My existing traffic cluster: [1-2 sentence summary of what already ranks]

Generate 10 topic patterns that meet ALL these criteria:
1. The pattern contains at least one variable slot [like this]
2. Each filled instance targets a real search query, not invented language
3. The search intent is informational or commercial investigation, not pure transactional
4. The pattern is not already dominated by a site with >1M indexed pages
5. Each instance of the pattern can have a genuinely different answer (this is the uniqueness filter)

For each pattern, give me:
- The raw pattern
- 3 example filled keywords
- Estimated search intent
- Why this pattern isn't saturated
- A one-sentence unique angle that separates my pages from existing content
```

Criterion 5 is the one people skip. If the pattern is `population of [city]`, every instance has an objectively identical-structure answer, and you're competing with Wikipedia — which is both already-saturated *and* one of the few sites Google trusts enough to let rank with thin content. You will lose.

But a pattern like `best Claude Code plugins for [specific workflow]` — that has genuinely different answers for "React development" versus "Laravel testing" versus "data pipelines," because the actual *tools* are different for each. That's what "genuinely different" looks like in practice, and it's the filter that decides whether your 200-page rollout survives.

I usually get back a set of 10 patterns. I throw out six of them. The ones I keep are the ones where I can *feel*, just reading the filled examples, that each page would genuinely have different content — not just a different noun swapped in.

## Step 2: Validate 20 Real Keywords Before You Trust the Pattern

Once I've picked a pattern, I don't go straight to generating pages. I ask Claude to generate 20 filled instances of the pattern, and then I take every one of those 20 keywords to a real keyword tool. I'm currently using a mix of Ahrefs and the free volume data from Google Search Console's own query reports for my existing properties.

What I'm looking for isn't high-volume keywords. That's a rookie mistake. A pattern where every filled instance has 10K+ monthly searches is a pattern that's already being carpet-bombed by Wise, Zapier, or a dozen other programmatic giants. I'm looking for the opposite: patterns where each keyword pulls 50–500 monthly searches, where the aggregate across 100–300 pages is significant, and where the per-page competition is low enough that a well-built page can actually rank.

This is the "modest traffic per page, massive aggregate" model that makes pSEO economically defensible in 2026. One Zapier-style integration page pulls maybe 200 searches a month. Six thousand of them — which is roughly what Zapier's running — is 1.2 million monthly searches. You're not trying to hit a home run. You're playing for singles, at volume.

If 15 of my 20 validated keywords have real search volume and a reasonable competition score, the pattern is viable. If only 8 of them do, I go back to Step 1 and pick a different pattern. I'd rather burn a day on pattern validation than three weeks on a doomed rollout.

<!-- IMAGE: Screenshot showing a pattern validation spreadsheet with 20 keywords, monthly search volume, and competition scores. Alt text: "Claude programmatic SEO keyword validation spreadsheet with volume and competition data". Caption: "The pattern validation step — the one most tutorials skip entirely." -->

## Step 3: Build a Page Template That Forces Real Differentiation

This is where the craft lives. The template isn't just "H1 → intro → three H2 sections → CTA." That's a layout, not a template. A real pSEO template specifies five things per page:

**A unique angle slot.** Every page must open with a hook that is specific to *this exact keyword* and would make no sense on any sibling page. For a "best Claude Code plugins for Laravel testing" page, the unique angle might be a specific Pest PHP failure scenario I walked into last month. For "best Claude Code plugins for React development," it's a completely different moment — a broken Storybook build, say. Neither hook is swappable. That's the point.

**A fresh-data requirement.** Every page pulls at least one data point from a web search run at generation time. Tool version numbers, recent pricing changes, launch dates, current rankings. This forces Claude to use WebSearch inside the Skill and bakes in freshness that a static template can't fake. I've been enforcing "data must be dated within the last 90 days" as a hard rule, and it noticeably lifts how quickly these pages start ranking.

**An SEO + AIO block.** Every page targets both classical SEO (keyword in H1, natural variants in H2s, semantic entities throughout) and what I'm now calling AIO — AI-answer-engine optimization. This means at least one passage in each page is written as a standalone, citable answer that Perplexity or Google's AI Overviews can pull without needing the surrounding context. Claude Sonnet 4.6 is particularly good at producing these passages because its 1M context window lets the Skill see your full citation style before writing.

**A goal-blended conversion funnel.** Every template includes a mid-page and end-page CTA that's *contextually relevant to the specific page's topic*. Not a hardcoded "buy my course." A "buy my course" that references the exact problem the page is solving. This is where most templated content dies: the reader realizes the CTA is boilerplate and bounces. If the CTA reads like it was hand-written for that exact page, conversion holds up.

**A banned-pattern list.** My template literally contains a list of phrases that are forbidden to appear. "In today's fast-paced world." "Let's dive in." "Furthermore." "In conclusion." If Claude writes any of them, the page gets rejected by the Skill's internal check and regenerated. The banned list isn't decorative. It's the single biggest factor in whether pages read as human-written or AI-slop.

I build the template as a markdown document with commented sections explaining *why* each rule exists. Then I test it by hand on three keywords from my validated list, writing the pages myself using the template as a guide. If I can produce three genuinely different pages from the same template using my own brain, the template is sound. If my own three pages feel repetitive, the template is broken and needs tightening before it ever goes into a Skill.

This manual dry-run is non-negotiable. Skipping it is how you end up with 200 published pages that all sound the same and collectively tank your site's topical authority. I learned this the slow way — twice.

## Step 4: Convert the Template Into a Claude Skill

Now we turn the template into software.

Claude Code ships with a built-in skill-creator, and it's the cleanest way to do this. I run `/skill-creator` from my project root, point it at the template markdown file, add the tone-of-voice document, the banned-phrase list, and a compressed ZIP of reference content (existing top-performing pages, brand assets, a sitemap CSV for internal linking). The skill-creator wraps all of it into a single invocable Skill with its own `SKILL.md` at the root.

If you're coming to this cold, I wrote a [step-by-step breakdown of building your first Claude Skill](https://www.mejba.me/claude-skills-seo-content-writer) that covers the mechanics in detail. The short version: a Skill is a folder, the folder has a `SKILL.md` file with a YAML header describing when to activate, and the rest is reference material Claude loads when the Skill fires.

Three details matter more than most guides flag:

**One — the activation description must be specific.** If your `SKILL.md` says "helps with SEO content," Claude's skill-selector will invoke it randomly. If it says "generates programmatic SEO pages for the pattern `[topic]`, one keyword at a time, using the attached template and data validation rules" — Claude will only fire it when you actually need it. Precision here is what makes Skills composable with the rest of your workflow.

**Two — the uniqueness enforcer has to live inside the Skill, not in the template.** The template is the blueprint. The Skill is the contractor that checks the work. I include explicit instructions in `SKILL.md` like: *"Before writing any page, generate a unique-angle hook that could not plausibly appear on any other page targeting any sibling keyword in this pattern. If you cannot generate a genuinely distinct hook, stop and ask the user for guidance."* That "stop and ask" escape hatch is what prevents Claude from hallucinating fake specificity when the real material is thin.

**Three — iterate on the Skill with real output.** First run, the Skill produces pages that are 80% of what I want. I read 10 outputs, note exactly what's off (too formal, hook pattern repeating across pages, internal links going to wrong URLs), and edit the Skill's instructions. Second pass, it's 90%. Third pass, it's 95% and shipping-ready. Three iteration cycles usually takes me a single afternoon.

Do not, under any circumstances, automate publishing to production before the Skill is at 95%+ quality. Human-in-the-loop review stays on. The cheapest way to destroy a site's crawl budget is to auto-publish 100 mid-quality pages and force Google to burn its trust allocation evaluating your slop.

## Step 5: Scale With a Custom Command and Parallel Agents

This is where it gets fun.

I wrap the Skill in a custom slash command — `/pseo` — that takes a keyword as an argument, loads the Skill, runs web research for that specific keyword, writes the page, saves it to the content directory, and logs the output. The command is a short markdown file in `.claude/commands/pseo.md` with a prompt template that says, roughly: *"Activate the programmatic-seo Skill. Generate one page for the keyword passed as argument. Before writing, run WebSearch for fresh data. Save output to `content/pseo/[slug].md`. Self-review against the template's quality checklist before returning."*

Once the command works in a single Claude Code session, the scale-out is mechanical. I use git worktrees — the same pattern I walked through in my [Claude Code git worktrees parallel agents guide](https://www.mejba.me/claude-code-git-worktrees-parallel-agents) — to spin up 6–10 independent working directories, one Claude Code session per worktree, and feed each session a different slice of the keyword list.

Each session runs `/pseo "keyword-A"`, then `/pseo "keyword-B"`, then `/pseo "keyword-C"` sequentially, while the other sessions do the same with their own slices. I'm not parallelizing within a session. I'm parallelizing *across* sessions, because the real bottleneck is wall-clock time on web searches and page generation, not token throughput on any single Claude instance.

Practical numbers from my last rollout: ten parallel sessions, running Sonnet 4.6 because the cost-per-page math is more forgiving than Opus 4.7 for high-volume generation, produced 120 pages in about four hours. Opus 4.7 produces noticeably better reasoning for complex patterns, but for standard pSEO pages I'm finding Sonnet 4.6 hits a 95%+ quality bar at roughly a third of the token cost. The 1M context window on both models means each session can hold the full template, the full tone-of-voice doc, the full banned-phrase list, and the full sitemap CSV without hitting context limits.

Merge back to main, push, verify in Search Console, submit the sitemap, move on.

## Step 6: Wire Up Analytics Before You Publish a Single Page

This is the step everyone skips that quietly decides whether the whole system was worth the effort.

Before I publish any Claude programmatic SEO rollout, I make sure three monitoring surfaces are already wired and watching:

1. **Google Search Console** — I submit the new sitemap *before* the batch goes live, so GSC starts tracking impressions and positions from day one. I create a custom filter in GSC for URLs matching the pattern's slug prefix, so I can see at a glance how the rollout is performing as a cohort.

2. **Google Analytics 4** — I tag every pSEO page with a custom dimension marking it as `content_type: programmatic` and which pattern it belongs to. This lets me pull conversion rate, bounce rate, and time-on-page *just* for the pSEO pages, separated from my hand-written content.

3. **A simple dashboard** — I run a weekly query that pulls impression count, click count, average position, and indexed-page count for the pattern. If indexed-page count is lagging behind published-page count by more than 20% after four weeks, something is wrong with the pages and I need to read a random sample to find the pattern. If average position plateaus above rank 30 after eight weeks, the pattern's intent match is wrong and I either kill the rollout or rebuild the template.

That last bit — the kill criterion — is the one I had to learn the hard way. Not every pattern is going to work. Some will be wrong about intent. Some will target a SERP that favors forum threads or video over articles. The discipline is to notice a dead pattern within 8 weeks rather than 8 months, deindex it, and move on. Programmatic SEO isn't a one-shot rollout. It's a portfolio, and you kill the dogs quickly.

## What I Got Wrong (And Might Still Be Getting Wrong)

Three honest limitations I've run into, because nobody selling pSEO courses will tell you these:

**The uniqueness problem doesn't fully go away.** Even with a Skill enforcing per-page unique angles, I can still see family resemblance across the 120 pages in a single pattern. Google doesn't seem to penalize this at the level I'm hitting — the indexation rate on my current rollout is sitting around 88% at twelve weeks — but I suspect there's a ceiling I haven't found yet. If I tried to run 5,000 pages off one pattern, I'd expect the indexation rate to drop. I haven't had the nerve to test it.

**Freshness decays fast.** A Claude programmatic SEO page I publish today will have real, fresh data in it. Six months from now, some of that data will be stale — tool versions have updated, pricing has moved, features have shipped. I don't yet have a clean automated refresh system. My current workaround is to re-run the same Skill against the existing slug every 90 days and let Claude rewrite stale sections in place, but it's manual and tedious. This is the next system I'm going to build.

**Sonnet 4.6 occasionally hallucinates internal links.** Even with a sitemap CSV loaded into the Skill, Sonnet 4.6 will sometimes invent a URL that doesn't exist in the CSV. I've got a validation step in the command that grep-checks every internal link against the sitemap before saving the page, and if a link fails the check, the whole page regenerates. Opus 4.7 has this problem noticeably less, which is one reason I still use it for high-stakes pages despite the cost.

This isn't a "set it and forget it" system. It's a system that takes pressure off the part of content production that shouldn't require a human — the template-filling, the research aggregation, the first-draft prose — and concentrates my attention on the parts that do: pattern selection, quality gates, kill decisions.

## What the Numbers Actually Look Like

I want to be careful here because fabricated metrics destroy the trust I've built across 3,000 words. So I'll give you ranges rather than fake precision.

Across the two production sites where I'm currently running this system, the pSEO pages that survived the first twelve weeks of indexing are pulling somewhere in the range of **3–8 clicks per page per month from organic search**. That sounds small until you multiply. A cohort of 120 indexed pages pulling 5 clicks each is 600 organic clicks a month from a single rollout — content that cost me maybe one week of calendar time and roughly the API budget of a medium-sized Cursor subscription to produce.

The industry benchmarks I've seen from Backlinko's 2026 programmatic SEO analysis and Ahrefs' pSEO case studies suggest traffic typically lags publication by 2–4 months before the pattern starts compounding. My observed pattern matches that — almost nothing for the first 30 days, meaningful indexation starting around day 45, noticeable ranking improvement between months two and three.

The conversion rate on pSEO pages is, in my experience, lower than on hand-written cornerstone content. That's expected. These pages capture intent at a wider, shallower layer of the funnel. The job of a pSEO page isn't to convert at 4%. The job is to bring a qualified visitor into the ecosystem at 0.5–1% conversion, at a volume hand-written content can't hit.

## Frequently Asked Questions

### What is Claude programmatic SEO and how is it different from traditional pSEO?
Claude programmatic SEO uses Claude Skills, templates, and parallel agents to generate SEO pages at scale with genuine per-page variance enforced by AI judgment rules. Traditional pSEO fills templates from structured datasets, which Google now flags as scaled content abuse when uniqueness drops below 30%. The Claude approach bakes uniqueness enforcement into the generation step itself. For the full workflow, see the step-by-step walkthrough above.

### Which Claude model is best for programmatic SEO — Opus 4.7 or Sonnet 4.6?
Sonnet 4.6 is the better default for high-volume Claude programmatic SEO generation because it hits 95%+ of Opus 4.7's quality at roughly a third of the token cost, and both models share the same 1M context window. Use Opus 4.7 for pillar pages and complex patterns where reasoning depth matters more than unit cost.

### How many pages can I generate with one Claude Skill?
One Claude Skill paired with a validated topic pattern can realistically produce 50–500 pages before pattern fatigue sets in. Running ten parallel Claude Code sessions through git worktrees, I generate roughly 120 pages in four hours. Scaling past 500 pages on one pattern increases the risk of Google's scaled content abuse detection catching shared structural DNA across the cohort.

### Is programmatic SEO still safe after Google's scaled content abuse policy?
Yes, when pages maintain ≥30–40% genuinely unique content, target real search demand, and serve clear user intent — Wise, Zapier, and TripAdvisor all run massive pSEO operations that rank fine. The policy targets doorway pages and templated thin content, not legitimate programmatic pages with real per-page value. The Claude Skill uniqueness enforcer I described above is specifically designed to stay on the right side of this line.

### How long before programmatic SEO pages start ranking?
Most Claude programmatic SEO rollouts show meaningful indexation at 4–6 weeks and measurable ranking improvements between months two and three, matching broader pSEO benchmarks. If indexation lags published-page count by more than 20% at the four-week mark, the pattern or template likely has a quality problem and needs review before publishing more pages.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
