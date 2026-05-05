**BRAND:** mejba.me
**TITLE:** Claude Code Content Autopilot With Blotato + Arvow
**META TITLE:** Claude Code + Blotato + Arvow: Full Content Autopilot
**SLUG:** claude-code-blotato-arvow-content-autopilot
**PRIMARY KEYWORD:** Claude Code content automation
**META DESCRIPTION:** I rebuilt my entire publishing stack on Claude Code, Blotato, and Arvow. Here is the actual setup, the API wiring, and what it ships in a week.
**TAGS:** Claude Code, Content Automation, Blotato, Arvow, SEO Workflow

---

# Claude Code Content Autopilot With Blotato + Arvow

There is a folder on my Mac called `autopilot/`. It has eleven files in it. Most of them are markdown. One is a `.env`. Two are skill definitions. Nothing else — no Node project, no Python virtualenv, no Make.com canvas with thirty-eight nodes connecting things together with the elegance of a server room cable nightmare.

That folder runs my entire publishing operation.

In the last twenty-eight days, the system shipped twelve SEO blog posts (averaging 3,400 words each), 184 social posts spread across LinkedIn, X, Instagram and Facebook, and somewhere around forty short-form videos that I never opened a single editor to produce. I reviewed roughly a third of it before publish. The rest went out under approval gates I had pre-approved by category.

The stack is Claude Code running inside VS Code, talking to two products via API: **Blotato** for social distribution and **Arvow** for the SEO content engine. That is the whole setup. The interesting part is not what each tool does individually — both have been written about plenty. The interesting part is what happens when you wire them through Claude Code skills so they hand work to each other without you in the middle.

I want to walk through exactly how that wiring works, because the difference between "I use AI tools" and "AI runs my publishing" comes down to about six hundred lines of skill definitions and one architectural decision most people get wrong. We will get to the wrong decision in a minute.

But first, the moment that convinced me this was actually working.

<!-- IMAGE: [Screenshot of VS Code with the autopilot/ folder open, showing skills/, prompts/, .env, and a Claude Code chat panel mid-execution]. Alt text: "Claude Code content automation project structure in VS Code with Blotato and Arvow skills". Caption: "The whole stack lives in eleven files. No frameworks, no orchestrators." -->

## The Tuesday Morning That Changed My Mind

I had stopped checking the system. That sounds like a humble brag. It is actually how I noticed it was real.

Two Tuesdays ago I opened my Facebook page analytics for an unrelated reason — I was looking for an old client conversation. The reach number on a post from the previous week was 218,000. I had never seen that post before. Not because I had not approved it; I had. Bulk-approved a batch of fourteen posts the previous Sunday over coffee, and that one was buried in the middle.

It was a four-line carousel about a Claude Code workflow. Pulled from a blog post Arvow had generated. Repackaged into platform-native copy by Blotato. Scheduled into the next-free-slot on my Facebook calendar by a Claude Code skill that called Blotato's `/v2/posts` endpoint. I had touched none of it after the bulk approval.

That was the moment I stopped thinking of this as "an experiment." It was infrastructure. And like all infrastructure, the only time you notice it is when the analytics surprise you.

Here is what changed in my head: the question was no longer *can AI write my content?* (it can, with caveats) or *can AI schedule my content?* (also yes). The real question was *can a Claude Code project orchestrate two specialist tools end-to-end without me being the glue between them?*

That last word is the trap. Glue.

## The Architectural Decision Most People Get Wrong

When I first sketched this stack, I did what nearly everyone does. I designed Claude Code as the orchestrator that would do everything in the middle. Generate the blog. Format it for SEO. Push it to WordPress. Then read its own output. Convert it to social. Schedule the social. Generate the visuals. Approve the visuals. The whole pipeline running through Claude Code's tool calls, with Blotato and Arvow reduced to dumb publishing endpoints.

That version sort of worked. It was also slow, expensive, and brittle.

The mistake was treating Blotato and Arvow as APIs instead of as agents. Both products have their own internal intelligence. Arvow does SERP analysis, competitor scrape, cluster mapping and on-page optimization in a single batch call to `https://api.arvow.com/api/v1/batch`. Blotato has platform-specific formatters baked in — LinkedIn gets professional structure, X gets the punchy hook, TikTok gets the vertical script — without me writing a single platform-specific prompt.

When I let each product own what it is good at and used Claude Code only as the connective tissue, the whole thing got faster, cheaper and more reliable in the same week.

The architecture I landed on:

- **Arvow** owns SEO research, blog generation, and publish-to-WordPress. Triggered by a Claude Code skill that submits keyword clusters and reads back webhook confirmations.
- **Blotato** owns social formatting, scheduling, and posting across nine platforms. Triggered by a Claude Code skill that reads the published blog's RSS, summarizes the angle, and submits posts to `/v2/posts` with appropriate `accountId` per platform.
- **Claude Code** owns brand voice, approval gates, decision-making about what to publish when, and the small bits of code that move JSON between the two services.

Three jobs. Three owners. The boundaries are clear.

This is the part I want you to internalize before we look at any code. The reason most automation stacks collapse under their own weight is that one tool is trying to do everything. The Make.com workflows that look like a Christmas tree of nodes? That happens because the orchestrator is doing the formatting, the routing, and the logic all at once. Strip the orchestrator down to its actual job — *make decisions, hand off work* — and the complexity drops by an order of magnitude.

Now we can look at how the wiring actually works.

## What Lives Inside the autopilot/ Folder

Eleven files. Let me list them with what each one does:

```
autopilot/
├── .env                          # API keys: BLOTATO_API_KEY, ARVOW_API_KEY
├── claude.md                     # Project context Claude Code reads on every session
├── brand-voice.md                # The voice rules, banned phrases, persona definitions
├── skills/
│   ├── seo-blog-generate.md      # Calls Arvow with keyword clusters
│   ├── social-from-rss.md        # Reads blog RSS, pushes to Blotato
│   ├── approval-gate.md          # Bulk-approval workflow with category rules
│   └── visual-brief.md           # Produces image prompts for Blotato's visual engine
├── prompts/
│   ├── seo-research-input.md     # The "site profile" that Arvow uses for context
│   └── social-distribution.md    # The post-per-platform expansion rules
└── logs/                         # Every API call's request and response, JSONL
```

That is it. No `package.json`. No `requirements.txt`. The "code" is markdown. The execution is Claude Code reading a skill, calling tools (mostly `Bash` to `curl` against an API and `Read` to parse a file), and writing the result back to disk.

This works because of a feature of Claude Code that became generally available in early 2026: **Agent Skills**. A skill is a markdown file with a name, a trigger condition, and a sequence of instructions. When you invoke the skill, Claude reads it, follows the steps, and does not improvise outside the boundary. If you have used the `/loop` or `/simplify` skills, you have already used the same primitive — you are just defining your own.

I wrote about the deeper mechanics of skills in [my Claude Code agent skills guide](/claude-code-agent-skills-guide), so I will not re-explain them here. The thing to know for this article: a skill is the unit of automation. Each one in the `skills/` folder above is a single agent that does one job and nothing else.

Let me show you the most important two.

## Skill 1: SEO Blog Generation Through Arvow

The Arvow API has a single useful endpoint for our purpose: `POST https://api.arvow.com/api/v1/batch`. You hand it a list of keyword entries, a content formula, and a webhook URL. Arvow does the SERP work, generates the article, and posts the result back to your webhook (or directly to WordPress / Wix / Webflow / Ghost / Shopify if you have configured an integration). API access requires their Agency plan, which is $429/month — there is no way around that, I checked twice.

Here is what the skill looks like, abbreviated:

```markdown
# Skill: seo-blog-generate

## Trigger
User says "generate this week's SEO posts" or "run Arvow batch".

## Inputs needed
- Keyword cluster file at /autopilot/keywords/this-week.md
- Brand voice profile at /autopilot/brand-voice.md
- Arvow integration ID (in .env as ARVOW_INTEGRATION_ID)

## Steps
1. Read the keyword cluster file. Extract entries as a JSON array.
2. Read brand-voice.md and condense to a 250-word "voice prompt".
3. Construct the Arvow batch request body:
   - formula: "long_form_seo_v3"
   - entries: keywords from step 1
   - integration_id: from env
   - voice_prompt: from step 2
   - target_word_count: 3200
4. POST to https://api.arvow.com/api/v1/batch with Bearer auth.
5. Log the batch_id and entry_ids to logs/arvow-batches.jsonl.
6. Stop. Arvow will publish to WordPress directly via integration.

## Failure modes I've actually hit
- 401 means the API key is wrong, not the integration ID.
- "credit limit reached" means we've used >10,000 credits this month;
  pause and notify, don't retry.
- An entry can silently fail if the keyword has < 100 monthly searches;
  Arvow's site-profile filter rejects it. Surface this in the log summary.
```

That is the entire skill. About sixty lines including the failure notes. When I run it, Claude Code reads the file, executes the steps in order, and stops at step 6. It does not "decide" to do anything else. It does not call Blotato. It does not write the social posts. That is a different skill's job.

The key detail is step 6. Arvow's job ends with the article published to WordPress. The next agent in the chain — the social distributor — is triggered by the article appearing in my RSS feed, not by Claude Code remembering to call the next thing. This is what people mean by *event-driven* architecture, and it is the secret to systems that do not break.

If Claude Code crashes between step 5 and the social skill firing, nothing breaks. The article is still published. The next time Blotato's RSS poller runs (or the next time I manually invoke the social skill), the new post is found and processed. There is no fragile in-memory state to lose.

## Skill 2: Social Distribution Through Blotato

Blotato's API is more interesting because it expects per-account targeting. Every social account you connect — your X handle, your Facebook page, your LinkedIn personal profile, your LinkedIn company page — gets a unique `accountId`. Some platforms (Facebook pages, LinkedIn company pages) also need a `pageId` from a sub-accounts call. The publish endpoint is `POST /v2/posts` and it takes a `post` object plus optional scheduling fields at the top level (`scheduledTime` as ISO 8601 with timezone, or `useNextFreeSlot: true` to drop into Blotato's calendar gaps).

Here is the skill, condensed:

```markdown
# Skill: social-from-rss

## Trigger
- Cron: every 4 hours
- Or: user says "distribute the latest blog"

## Inputs needed
- RSS feed URL (in .env as BLOG_RSS_URL)
- Blotato API key (in .env)
- Account ID map at /autopilot/blotato-accounts.json
  (one entry per platform, with accountId + optional pageId)
- Brand voice profile

## Steps
1. Fetch RSS feed. Compare against logs/processed-posts.jsonl.
   For each new post:
2. Read the post's title, meta description, hero image URL,
   first 800 words of body.
3. For each platform in the account map:
   a. Generate platform-native copy following social-distribution.md rules.
      LinkedIn = professional hook + framework + CTA.
      X = single sharp claim + thread of 3 if topic warrants.
      Facebook = conversational opener + value bullets + soft CTA.
      Instagram = carousel script (5 slides) + caption.
   b. Choose visual: hero image if Blotato accepts the URL,
      otherwise generate via Blotato's visual engine with prompt from visual-brief.md.
   c. Build the /v2/posts request body. Use useNextFreeSlot: true.
   d. POST to https://api.blotato.com/v2/posts with Bearer auth.
   e. Log batch ID, scheduled time, platform, post URL to processed-posts.jsonl.
4. If any platform fails (4xx response), log to logs/failures.jsonl
   and continue with the others. Don't abort the batch on one failure.

## Approval gate behavior
- If APPROVAL_MODE in .env is "auto", post directly.
- If "review", create a draft in /autopilot/drafts/[date]-[post-slug]/
  and notify (Slack webhook). Wait for me to move the file to /approved/
  before submitting to Blotato.
```

The thing I want to point at in this skill is step 4. *Don't abort the batch on one failure.* Early versions of this skill were one-shot — if the LinkedIn API returned a 401 because my token had expired, the whole batch died and the X post never went out. I lost twelve days of distribution before I noticed. Now every platform is independent. One failure is logged, surfaced, and skipped. The other eight platforms ship.

This is the kind of thing you only learn by running the system in production for long enough to hit the failure modes. No tutorial article will tell you "your social token will expire silently and your automation will go quiet for two weeks before you notice." It will. Build for it.

## How Claude Code Becomes the Brand Voice Layer

Here is where the Claude Code piece earns its keep. Both Arvow and Blotato will produce technically valid content without Claude in the loop. What they will not do, by default, is sound like *me*.

Arvow's default voice is competent and a little flat. Blotato's platform formatters are sharp but generic. If I let either one ship without a voice layer, my Substack reader of three years would stop opening the emails. (I have asked. They notice the difference.)

The voice layer lives in two places.

`brand-voice.md` is a 1,400-word document that defines my voice rules. It includes phrases I will never use ("game-changing", "in conclusion", "let's dive in"), phrases that *are* mine ("here is the part most people miss", "the question is", "what changed for me was"), my reading-level target (smart engineer over coffee, not a textbook), and three example paragraphs of canonical "this sounds like Mejba" prose.

The Arvow skill condenses this to a 250-word voice prompt and passes it as part of the batch request. Arvow honors it well — not perfectly, but well enough that maybe one paragraph per article needs my touch.

The Blotato skill takes a different approach. It generates platform copy, then runs it through a Claude Code voice-check pass before submission. The check is literally a sub-prompt: "Read this post. Compare it against brand-voice.md. If it would make Mejba wince, rewrite it. If it is fine, return it unchanged." That sub-pass costs maybe two cents per post in API calls. It catches roughly 30% of generations that need fixing.

This is where Claude Code's role clicks into focus. It is not generating from scratch. It is *quality-gating* output from specialist tools. The specialists are faster and cheaper at the bulk work. Claude is the editor. That division of labor is what makes the whole thing economically viable at the volume I am pushing.

## The Approval Gate That Saves You From Yourself

Three months ago I would have told you full automation meant zero human review. I was wrong, and the wrongness cost me a near-miss with a post that was technically accurate but politically tone-deaf about an industry layoff that had happened the day before generation.

The skill that almost shipped that post had no awareness of the news cycle. Why would it? Arvow had researched the topic seven days earlier when the keyword cluster was created. Blotato had formatted it cleanly. Claude Code's voice check passed it because the sentence structure was good. None of those agents knew that, twenty-four hours before the scheduled publish time, the same company had laid off 800 people.

I caught it manually because I happened to scroll my drafts folder while waiting for a build. That was luck, not design. So I built the approval gate into the skill rather than relying on my reflexes.

The gate works in three modes:

**Strict review.** Every post requires me to drag a draft file from `/drafts/` to `/approved/` before Blotato gets the request. Slow but safe. I use this for thought-leadership content where my reputation is on the line.

**Bulk review.** Posts go into a weekly digest. Sunday morning I open the digest, skim fourteen posts in about ten minutes, and approve in batches. Posts not actively rejected get auto-promoted to `/approved/` after 48 hours. This is what I use most of the time.

**Auto-publish with kill switch.** Strict categories (recipes, how-to bullet posts, evergreen tutorials) ship immediately. Topical categories (anything tagged `news`, `commentary`, `industry`) get held for review automatically. The skill knows which is which from a tag in the keyword cluster.

The kill switch is a single command — `pause-autopilot` — that creates a file at `/autopilot/PAUSED`. Every skill checks for that file before making any API call. If it exists, the skill logs and exits. I can pause the entire publishing operation in three seconds from my phone via a Shortcut that SSHs in and `touch`-es the file. I have used it twice. Once during a personal emergency, once when a major outage hit a vendor I had recommended in three queued posts.

Build the kill switch first. Trust me on this one.

## What This Costs to Run Per Month

I want to give you real numbers because the AI-content-stack space is full of "save thousands per week" claims that turn out to mean "compared to hiring a five-person agency at $40K/month."

Here is what I actually pay:

- **Claude Pro Max** subscription for the Claude Code usage: included in my existing plan, no incremental cost. The skill executions are mostly tool calls, not heavy generation, so token usage stays modest. If you were starting fresh, the Claude API direct usage for this kind of orchestration runs $30–80/month depending on volume.
- **Blotato:** $29/month entry tier covers the API + scheduling for nine platforms. I am on a higher tier ($79/month) because I wanted unlimited posts and the visual generation credits.
- **Arvow Agency plan:** $429/month. This is the painful one. API access is gated to the Agency tier — you cannot get it on the $59 Solo or $129 Business plans. I know because I tried both before reading the fine print. The 10,000 credits/month covers roughly 50 long-form articles. If I were doing only 8-12 articles per month, I would skip the API and use Arvow's UI manually. The API only pays for itself if you are at volume.

Total: roughly **$540/month** for the stack, with Claude Code usage included.

For comparison: the equivalent output through human contractors would be approximately one mid-level SEO writer (~$3,500/month for 12 articles), plus a part-time social media manager (~$1,800/month for the cadence I run). That is $5,300/month of replaced work, and the human team would not be ten times faster. They would be the same speed, with weekends off.

The math is favorable. But the math is not the reason to do this.

## The Reason That Actually Matters

The reason to build this is not cost savings. The reason is that publishing infrastructure should not be the bottleneck on what you say.

Before this stack, I had ideas every week that I never wrote up because the friction of "draft → edit → format for blog → write LinkedIn version → write X version → schedule everything → make a header image" was forty-five minutes per idea. Forty-five minutes when I am tired on a Friday is enough to kill the post. Multiply that by the four months of ideas I let die last year and you can see the actual cost.

What changed when the stack was running was not that I produced more — though I did. What changed was that the *floor* of "I will publish this thought" dropped to almost zero. If I have a research insight on a Tuesday and I want it indexed by Friday, I add the keyword to the cluster file, run the Arvow skill, and the system handles the rest. The decision to publish becomes the only decision.

That is the leverage. Removing every step between *deciding to ship* and *the thing being shipped*. The cost savings are a happy byproduct.

## What I Would Do Differently if Starting Today

Three things, if I were rebuilding from scratch:

**Start with the Blotato side first, not Arvow.** Social distribution gives you immediate feedback. You see engagement data within hours. The SEO side has a six-to-twelve-week feedback loop because Google indexes slowly. Build the fast-feedback loop first so you learn how the system behaves before you commit to the slow one.

**Write the brand-voice document before any skill.** I rewrote mine three times after I had already built the skills, which meant retesting every output path each time. If you nail the voice doc first, your skills can reference it from day one and stay stable.

**Skip the "auto-publish everything" phase.** I went all-in on auto-publish for two weeks before adding the approval gate. Of those two weeks, three posts were embarrassing enough that I pulled them within 24 hours. The approval gate is not slowing your system down; it is the thing that lets you trust the system enough to keep using it.

I would also have spent less time on visual generation. Blotato's built-in visual engine is fine for 80% of posts. The custom image prompts I wrote in the early days produced "better" visuals at the cost of unpredictable failure rates. Predictable mediocre beats unpredictable excellent when the system has to run unattended.

## What Comes Next on This Stack

The next layer I am building is feedback. Right now the system publishes blindly — it does not know which posts performed and which sank. The metrics live in Google Search Console, Blotato's analytics dashboard, and the per-platform native dashboards. Nothing reads them.

The plan is a fourth skill, `harvest-metrics`, that pulls weekly performance data from each surface, joins it back to the keyword cluster file, and tells the next batch which patterns are working. If LinkedIn carousels with five-slide structure outperform three-slide by 4x, the next batch leans toward five. If Arvow articles in the 2,800–3,200 word range outrank the 4,000+ ones for my niche, next week's keyword cluster targets the shorter range.

When that loop closes, the system will not just publish. It will learn what to publish more of. That is the moment "automation" becomes "agent" in the meaningful sense.

I will write that one up when it is running. Probably eight weeks from now, given how iteration cycles tend to slip when the underlying system is working well enough to ignore.

## Frequently Asked Questions

### What is the difference between Blotato and Arvow?
Blotato handles social media — generating, formatting, and scheduling posts across nine platforms with a single API. Arvow handles SEO blog content — keyword research, article generation, and direct publishing to WordPress, Wix or Webflow. They solve different ends of the publishing pipeline and pair well together. For the full architectural split, see *The Architectural Decision Most People Get Wrong* above.

### Do I need the Arvow Agency plan to use this stack?
Yes, if you want API automation. Arvow gates API access to the Agency plan ($429/month as of May 2026). The Solo ($59) and Business ($129) plans do not include API access — you would need to use Arvow's UI manually. Worth it only if you are publishing 15+ articles per month.

### Can I do this without Claude Code, just using Make.com or n8n?
Technically yes. Both Blotato and Arvow have official n8n nodes and Make.com integrations. The reason I use Claude Code is the brand voice layer — running every output through a voice-check pass with my voice document loaded as context is much cleaner in Claude Code than chaining LLM nodes in Make. If brand voice is not critical for your niche, the n8n route is faster to set up.

### How long did this take to build?
The first working version was five evenings — about 14 hours total. Reaching the current state, with approval gates and per-platform handling, took another four weekends spread over six weeks. Most of that time was not coding; it was discovering failure modes by running the system and fixing them as they appeared.

### What happens if Blotato or Arvow go down?
Each skill logs its failure, surfaces it via Slack webhook, and exits cleanly. No retry storms. The next scheduled run picks up where things left off. I have lost maybe three posts to outages in the last three months — far less than a human team would have lost to a sick day.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
