**BRAND:** mejba.me
**TITLE:** I Built a Self-Improving LinkedIn System with Claude Code
**META TITLE:** Self-Improving LinkedIn System with Claude Code
**SLUG:** linkedin-auto-research-claude-code
**PRIMARY KEYWORD:** self-improving LinkedIn system Claude Code
**SECONDARY KEYWORDS:** Karpathy auto research technique, LinkedIn automation Claude Code, AI content feedback loop
**META DESCRIPTION:** How I built a LinkedIn content system that writes, publishes, tracks engagement, and rewrites itself using Claude Code and Karpathy's auto research pattern.
**TAGS:** Claude Code, AI Automation, LinkedIn Marketing, Auto Research, Tutorial
**CONTENT CLUSTER:** Claude Code & AI Agents
**TRANSFORMATION GOAL:** After reading, the reader will understand how to build a self-improving content pipeline that uses real engagement data to autonomously optimize future posts.

---

# I Built a Self-Improving LinkedIn System with Claude Code

The post that changed my mind about LinkedIn automation wasn't one I wrote. It was one the system wrote after analyzing forty-seven of my previous posts, identifying that transformation hooks outperformed my beloved "I" statement openers by 2.3x on reach, and rewriting its own content strategy at 4 AM on a Tuesday while I slept.

I woke up to a Notion database with a new lead magnet, a LinkedIn post already scheduled through Blotato, and a strategy document explaining *why* it had changed its approach. The reasoning was better than most content audits I've paid humans to produce.

That moment crystallized something I'd been circling for months: the real unlock in AI-driven content isn't generation. It's the feedback loop. Any prompt can produce a decent LinkedIn post. What separates a system that plateaus from one that compounds is whether it learns from what actually works — and adjusts without waiting for you to notice.

This is the story of how I built that system using Claude Code, Karpathy's auto research pattern, and a handful of tools most people use in isolation but never wire together. The whole thing runs on GitHub Actions. I haven't manually written a LinkedIn post in six weeks. And engagement is up.

## Why Most LinkedIn Automation Fails Within Two Weeks

I've tried the obvious approaches. Buffer. Hootsuite. Even a custom n8n workflow that pulled topics from RSS feeds and generated posts through the OpenAI API. They all share the same fatal flaw: they're open-loop systems.

Open-loop means the system produces output but never observes the result. It generates Post A, publishes it, then generates Post B using the exact same strategy. Whether Post A got 47 impressions or 4,700 is irrelevant — the system doesn't know and doesn't care.

This is like a chef who cooks dinner every night but never tastes the food or asks the guests what they thought. After a month, you'd have someone who's cooked thirty meals and learned nothing.

The LinkedIn algorithm in 2026 makes this problem worse. Views are down roughly 50% year-over-year according to recent platform data, engagement has dropped 25%, and follower growth has cratered by 59%. The algorithm now rewards dwell time and conversation quality over raw reactions — expert interactions carry 7-9x more algorithmic weight than a quick thumbs-up. Playing the old game of "post consistently and hope" stopped working when LinkedIn's recommendation engine got genuinely sophisticated.

What you need isn't more posts. You need a system that observes what actually resonates with *your specific audience* and continuously sharpens its aim.

That's where Karpathy's auto research pattern comes in. And it's where everything clicked for me.

## Karpathy's Auto Research: The Mental Model That Changes Everything

Andrej Karpathy released his [autoresearch project](https://github.com/karpathy/autoresearch) in early 2026, and the AI community hasn't stopped riffing on it since. The original concept was elegant: give an AI agent a small LLM training setup, let it modify the code, run a short experiment, evaluate the results, and decide whether the change was an improvement. Keep the wins, discard the losses, repeat.

Karpathy let this loop run for two days. The agent conducted 700 experiments. It discovered 20 optimizations that improved training time. When those same 20 tweaks were applied to a larger model, they produced an 11% speedup.

Here's what most people miss about this story: the pattern underneath has nothing to do with machine learning training. It works on anything you can measure.

Your LinkedIn posts? Measurable. Hook performance? Measurable. Post format engagement rates? Measurable. Which topics your audience actually cares about versus which ones you *think* they care about? Very measurable.

The auto research pattern is a closed-loop optimization system: **act, measure, analyze, adjust, repeat**. The "research" part isn't web browsing or reading papers — it's the system researching *its own performance* and drawing conclusions that inform the next cycle.

When I saw Duncan Rogoff demo this concept applied to LinkedIn content — he's an ex-art director turned AI agency founder who runs a community called [The Build Room](https://expert.buildroom.ai/) — something shifted in my thinking. Duncan had built a system where Claude Code generated lead magnets, published LinkedIn posts through Blotato, scraped engagement metrics through Apify, and then ran a weekly auto research analysis to rewrite its content strategy. The whole pipeline ran on GitHub Actions without human intervention.

I didn't just want to replicate it. I wanted to understand every layer deeply enough to customize it for my own workflow. So I built my own version from scratch.

Here's the architecture.

## The Five-Layer Architecture: How the System Actually Works

Most tutorials describe automation as a linear pipeline: input goes in, output comes out. This system is better understood as a loop with five distinct layers, each handling a specific job. Miss any layer and the feedback loop breaks.

### Layer 1: The Lead Magnet Engine

Every post in this system starts with a lead magnet. Not the post itself — the *asset* the post promotes. This is a strategic decision, not a technical one. LinkedIn's algorithm in 2026 crushes external links (roughly 60% reach reduction), so you need to give people a reason to engage *on the platform* before sending them anywhere.

The lead magnet engine is a Claude Code skill that takes three inputs: a target audience description, a trending topic from the research layer, and a content format (framework, playbook, prompt pack, or storytelling piece). It outputs a complete lead magnet stored in Notion — title, structure, the actual content, and storytelling elements designed to make the asset feel worth requesting in the comments.

The skill definition matters enormously here. I spent two hours refining the prompt that drives this skill, and I'd estimate that investment has saved me 60+ hours since. The prompt includes my audience's specific pain points (experts with deep knowledge but low online visibility), my brand's positioning, and explicit instructions about depth. I don't want thin, generic "5 tips" PDFs. I want assets that make someone think "this person actually knows what they're talking about."

Here's the skeleton of the skill configuration:

```yaml
name: lead_magnet_generator
description: Generate high-value lead magnets for LinkedIn distribution
inputs:
  - audience_profile: "Professionals with expertise but low online visibility"
  - topic: "{{trending_topic}}"  
  - format: "{{selected_format}}"  # framework | playbook | prompt_pack | story
context:
  - brand_voice_guide.md
  - top_performing_magnets.md  # auto-updated by research layer
  - audience_pain_points.md
output:
  destination: notion
  database: "Lead Magnets"
  fields:
    - title
    - hook
    - body_content
    - storytelling_elements
    - cta_text
    - target_format
```

The `top_performing_magnets.md` file is the key differentiator. This isn't a static document — the auto research layer rewrites it weekly based on which lead magnets actually drove the most comments and engagement. The system literally teaches itself what kind of assets your audience wants.

### Layer 2: The Post Writer and Publisher

Once the lead magnet exists in Notion, a second Claude Code workflow generates the LinkedIn post. This workflow reads the lead magnet, the current content strategy document (also auto-updated), and produces a post optimized for the patterns that are currently working.

The post writer follows specific structural rules that emerged from the auto research analysis:

- **Hook**: Under 200 characters. LinkedIn data shows hooks under 200 characters that introduce tension or promise specific value dramatically outperform explanatory openings. The system rotates between hook types — transformation hooks, contrarian hooks, and specific-number hooks — and tracks which type performs best.

- **Body**: Formatted for dwell time. Short lines. Strategic line breaks. The algorithm now weighs whether someone spends 45 seconds reading your post more heavily than whether they hit the like button.

- **CTA**: Comment-based, not link-based. "Comment FRAMEWORK and I'll send it to you" outperforms "Download here: [link]" by a wide margin because it drives engagement signals the algorithm rewards.

Publishing happens through [Blotato](https://www.blotato.com/), which offers API access starting at $29/month on the starter tier. Blotato supports nine platforms — LinkedIn, X, Instagram, Facebook, TikTok, Pinterest, YouTube, Threads, and Bluesky — and has native n8n and Make.com nodes. For this system, I use the REST API directly from a Node.js script that Claude Code wrote.

```javascript
// publish-to-linkedin.js (simplified)
const axios = require('axios');

async function publishPost(postContent, mediaUrl = null) {
  const payload = {
    content: postContent,
    platforms: ['linkedin'],
    publish_at: calculateOptimalTime(), // based on engagement data
    media: mediaUrl ? [{ url: mediaUrl, type: 'video' }] : []
  };

  const response = await axios.post(
    'https://api.blotato.com/v1/posts',
    payload,
    {
      headers: {
        'Authorization': `Bearer ${process.env.BLOTATO_API_KEY}`,
        'Content-Type': 'application/json'
      }
    }
  );
  
  return response.data;
}
```

The `calculateOptimalTime()` function isn't hardcoded to "Tuesday at 9 AM" like every generic LinkedIn guide suggests. It pulls from the engagement database and calculates when *my specific audience* is most active based on historical performance. Another thing the auto research layer figured out on its own.

### Layer 3: The Video Component

This layer surprised me the most. Duncan's system includes a step where Claude Code records a 6-7 second screen recording scrolling through the lead magnet in Notion, with branded text overlays — specific font (Gotham), neon green highlight box with black stroke. The video gets attached to the LinkedIn post.

Why does this matter? Because document posts and video posts on LinkedIn are hitting 6.60% engagement rates in 2026, the highest of any format. Standard text posts struggle to break 2%. A short scroll-through video of your lead magnet serves two purposes: it previews the value (reducing friction to comment), and it signals to the algorithm that this is rich media content worth distributing.

I adapted this for my own brand colors and style. The recording script uses Puppeteer to open the Notion page, scroll through it at a controlled pace, and capture the screen. A separate FFmpeg command adds the text overlay and outputs a compressed MP4.

The whole video generation takes about 30 seconds. No human involvement. No screen recording software. No editing.

### Layer 4: The Data Scraper

This is where the loop starts closing. Every day, an [Apify](https://apify.com/) actor scrapes engagement metrics from my LinkedIn posts. Apify's LinkedIn scrapers can extract likes, comments, reposts, impressions, and reaction breakdowns — all without requiring cookies or account access for public-facing data.

The pricing is reasonable: roughly $1-2 per 1,000 posts scraped, which for a daily check on your last 30 posts means negligible cost.

The scraped data gets written to a Notion database with this schema:

```
Post ID | Date | Hook Type | Post Length | Line Length | Content Format | 
Content Angle | Topic | Likes | Comments | Shares | Impressions | 
Engagement Rate | Lead Magnet Type
```

Every post gets tagged with metadata at creation time (hook type, format, angle, topic), so when the data comes back, the system can cross-reference performance against these variables. This is the instrumentation that makes the research layer powerful. Without structured metadata, you're just collecting numbers. With it, you're collecting *actionable intelligence*.

### Layer 5: The Auto Research Engine

This is the brain. Once a week (I'm experimenting with increasing the frequency), a Claude Code workflow pulls the entire engagement dataset, runs a structured analysis, and produces two outputs:

1. **A performance report** identifying what's working and what isn't
2. **An updated content strategy** that the lead magnet engine and post writer use for the next cycle

The analysis covers:

- **Hook performance**: Which hook types (transformation, contrarian, specific-number, question, story) generate the most impressions, comments, and engagement rate?
- **Format performance**: Do frameworks outperform playbooks? Do prompt packs beat storytelling posts?
- **Length analysis**: Is there a sweet spot for total post length and average line length?
- **Topic analysis**: Which subject areas does the audience respond to most?
- **Angle performance**: Do results-oriented posts beat social-proof posts? Does vulnerability outperform authority?

The system doesn't just identify correlations — it generates hypotheses and plans experiments. "Transformation hooks are outperforming by 2.3x. Next week, test 3 transformation hooks with different topic areas to determine whether the hook type or topic was the primary driver."

This is Karpathy's auto research pattern in pure form. Act, measure, analyze, form hypothesis, design next experiment, repeat.

Here's a simplified version of the analysis prompt:

```markdown
## Auto Research Analysis

You have access to the following LinkedIn post performance data:
{{engagement_data}}

Analyze this data to answer:
1. Which hook types generate the highest engagement rate?
2. Which content formats drive the most comments (our primary KPI)?
3. What's the optimal post length range?
4. Which topics resonate most with the audience?
5. What content angles perform best?

Then produce:
- A performance summary with specific numbers
- Updated recommendations for the content strategy
- 3 specific hypotheses to test next week
- An updated top_performing_magnets.md file
```

The output overwrites `content_strategy.md` and `top_performing_magnets.md` — the same files the lead magnet engine and post writer read as context. The system has just rewritten its own instructions based on empirical evidence.

If you've read my piece on [building self-improving AI systems with Claude Code](/blog/self-improving-claude-code-systems), you'll recognize this architecture. The reflection agent pattern I described there — where one AI evaluates another's output and adjusts the system prompt — is exactly what's happening here. The difference is that instead of evaluating chatbot conversations, this system evaluates LinkedIn engagement metrics. Same pattern, wildly different application.

## Setting Up the GitHub Actions Pipeline

The entire system runs headless on GitHub Actions. No server. No cron jobs on my machine. No Zapier. Just a repository with workflow files that trigger on schedule.

Here's the daily workflow:

```yaml
# .github/workflows/daily-content.yml
name: Daily LinkedIn Content Pipeline

on:
  schedule:
    - cron: '0 7 * * *'  # 7 AM UTC daily
  workflow_dispatch:  # manual trigger for testing

jobs:
  generate-and-publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Generate lead magnet
        run: node scripts/generate-lead-magnet.js
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
      
      - name: Generate LinkedIn post
        run: node scripts/generate-post.js
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
      
      - name: Record lead magnet video
        run: node scripts/record-video.js
        env:
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
      
      - name: Publish to LinkedIn
        run: node scripts/publish.js
        env:
          BLOTATO_API_KEY: ${{ secrets.BLOTATO_API_KEY }}
      
      - name: Scrape engagement data
        run: node scripts/scrape-engagement.js
        env:
          APIFY_API_KEY: ${{ secrets.APIFY_API_KEY }}
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
```

And the weekly research workflow:

```yaml
# .github/workflows/weekly-research.yml
name: Weekly Auto Research

on:
  schedule:
    - cron: '0 9 * * 0'  # Sunday 9 AM UTC
  workflow_dispatch:

jobs:
  auto-research:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Pull engagement data
        run: node scripts/pull-engagement-data.js
        env:
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
      
      - name: Run auto research analysis
        run: node scripts/auto-research.js
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
      
      - name: Update content strategy
        run: node scripts/update-strategy.js
        env:
          NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
      
      - name: Commit updated strategy files
        run: |
          git config user.name "Auto Research Bot"
          git config user.email "bot@mejba.me"
          git add strategy/
          git commit -m "auto-research: update strategy $(date +%Y-%m-%d)" || true
          git push
```

Six secrets in the GitHub repository settings. Two workflow files. That's the entire operations layer.

One thing I want to flag: you'll hit errors. Duncan mentioned in his walkthrough that Claude Code threw errors during the initial setup — scrapers timing out, API rate limits, Notion field type mismatches. I hit all of those plus a few more. The Apify scraper occasionally returns stale data if a post is less than 2 hours old. The Blotato API has a quirk where video attachments over 10MB silently fail without an error response. And Notion's API returns different field formats for rollup properties versus formula properties, which will break your data ingestion script if you're not handling both.

My advice: build each layer independently and test it in isolation before wiring them together. Run the lead magnet generator ten times manually. Publish five posts through Blotato's API directly. Scrape your last twenty posts with Apify and inspect the raw JSON. Debug layer by layer, then connect.

If you'd rather have someone build this kind of automation pipeline from scratch, I take on AI automation and agent development engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

## The Surprising Finding: Transformation Hooks Win (And It's Not Close)

I seeded the system with data from my last 47 LinkedIn posts before turning on the auto research loop. This gave the analysis engine enough data to draw real conclusions from day one instead of waiting weeks to accumulate signals.

The first auto research report contained a finding that genuinely surprised me.

I'd assumed — based on everything I'd read about LinkedIn copywriting — that "I" statement hooks with specific numbers would be the top performers. Posts opening with "I spent 6 hours testing..." or "I built a $3K/month system with..." felt like they *should* work. They're personal, specific, and create curiosity.

The data said otherwise.

Transformation hooks — posts that show a before-and-after progression — outperformed personal "I" hooks by 2.3x on reach and 1.8x on comment rate. Hooks like "From zero followers to 400 leads in 90 days" or "What happens when you stop chasing engagement and start building systems" consistently pulled more impressions and deeper engagement.

Why? My best theory: transformation hooks activate the reader's self-interest more directly. An "I" statement is about *me*. A transformation hook is about a *result the reader wants*. The reader projects themselves into the transformation. They don't just think "interesting" — they think "could that be me?"

This isn't a universal law. It's what works for *my audience* at *this point in time*. And that's exactly the point. A static content strategy would have kept me writing "I" hooks indefinitely because they seemed right. The auto research loop identified the actual pattern within six days of analysis.

The system has since evolved its strategy three more times. Post length sweet spot shifted from 1,200-1,500 characters to 800-1,100 (shorter, punchier). Frameworks started outperforming playbooks as the lead magnet format. Tuesday and Thursday posting times beat Monday and Wednesday. None of these were insights I would have caught manually — at least not this quickly.

## Running Multiple Agents in Parallel

One technical detail worth expanding on: Claude Code's ability to run multiple agent threads in parallel dramatically speeds up both the build phase and the daily execution.

When I was building this system, I had three Claude Code instances running simultaneously. One was writing the lead magnet generation script. Another was building the Apify scraping integration. A third was working on the video recording pipeline. They operated on separate files in the same repository, occasionally referencing shared utility modules.

This isn't a theoretical pattern — it's the [agent teams architecture](/blog/claude-code-agent-swarm-architecture) that Anthropic has been pushing hard since Opus 4. Each agent has its own context window, its own task, and its own ability to read and write files. When I needed the scraping agent to format data in a way the research agent could consume, I defined the data schema in a shared spec file and pointed both agents to it.

During daily execution, the pipeline runs sequentially (generate, then publish, then scrape) because each step depends on the previous one. But during the *weekly research* phase, I've experimented with parallelizing the analysis. One agent analyzes hook performance while another analyzes format performance while a third examines topic trends. The results merge into a single strategy update.

Total time from blank repository to working system: about 20 minutes of actual Claude Code interaction time for the initial MVP, plus another few hours of testing and refinement over the following week. Duncan reported a similar timeline — the bones of the system come together fast because Claude Code handles the boilerplate. The refinement is where the human judgment matters.

## What I Got Wrong (And What I'd Change)

Six weeks in, here's my honest assessment of what didn't work as planned.

**The video layer is fragile.** Puppeteer running in a GitHub Actions container occasionally times out when Notion takes too long to load. I've added retry logic, but I'm considering switching to a pre-rendered image carousel approach instead — more reliable, and the engagement data doesn't show a meaningful difference between a scroll video and a well-designed static carousel.

**Weekly research isn't frequent enough.** By the time Sunday's analysis identifies a failing pattern, I've already published 5-6 posts using the old strategy. I'm moving to twice-weekly analysis — Wednesdays and Sundays — to tighten the feedback loop. Karpathy's original autoresearch ran continuously. Content systems can't quite match that cadence because you need posts to accumulate enough engagement data before analysis is meaningful. But once per week feels too slow.

**The system doesn't handle comments.** This is the biggest gap. When someone comments "FRAMEWORK" on a post to request the lead magnet, a human (me) still needs to send the DM. I've explored LinkedIn API-based solutions for this, but LinkedIn's messaging API is locked behind partnership programs that aren't accessible to individual developers. For now, I batch-respond to comments twice a day manually. It takes 10 minutes but breaks the fully-autonomous dream.

**Audience targeting needs constant refinement.** My initial audience description was too broad: "professionals with expertise but low online visibility." The auto research loop identified that my highest-engagement posts targeted a narrower slice — specifically, solo consultants and agency founders in the AI/automation space. I've since sharpened the audience profile, and the next wave of content is performing noticeably better.

**Not every post should be a lead magnet.** Pure value posts, contrarian takes, and personal stories outperform lead magnet posts on reach (because they don't have a transactional CTA). The system now alternates: 3 lead magnet posts per week, 2 pure engagement posts. The engagement posts feed the algorithm's perception of my account quality, and the lead magnet posts convert that reach into actual leads.

## The Numbers After Six Weeks

I'm cautious about reporting metrics because context matters enormously. My account size, niche, posting frequency, and audience composition all affect these numbers. They're directional, not prescriptive.

What I can share: the auto research loop identified five distinct strategy adjustments over six weeks. Each one was based on a statistically meaningful pattern in the engagement data — not a hunch, not a best practice from some LinkedIn guru's course. The system spotted things I wouldn't have noticed for months, if ever.

The pattern I find most compelling isn't any single metric improvement. It's the trajectory. Each week's content strategy is measurably better-informed than the previous week's. The gap between the system's first-week posts and its sixth-week posts is visible in the engagement data. That compounding effect — where each iteration builds on validated insights from the previous one — is exactly what Karpathy's auto research pattern promises. And it delivers.

For comparison: my manual LinkedIn posting over the previous three months was sporadic (2-3 posts per week when motivated, zero during busy project weeks) with no systematic tracking. The automated system posts 5 times per week without exception, tracks everything, and evolves its approach based on evidence. Consistency alone accounts for a significant portion of any engagement improvement. The auto research optimization on top of that consistency is what makes the trajectory accelerating rather than flat.

## How to Build Your Own Version

If you want to replicate this, here's the sequence I'd recommend. Don't try to build everything at once — that's how automation projects die.

**Week 1: Manual foundation.** Write 10 LinkedIn posts manually. Track hook type, format, and topic for each one. Scrape their engagement after 48 hours using Apify (free tier works fine). Store everything in a Notion database. This gives you seed data and forces you to define your content categories.

**Week 2: Automate generation.** Build the Claude Code lead magnet skill and post writer. Test them repeatedly. Compare AI-generated posts against your manual ones. Refine the skill prompts until the output quality matches or exceeds your manual writing on a first draft.

**Week 3: Automate publishing.** Connect Blotato. Set up the GitHub Actions daily workflow. Run it manually (using `workflow_dispatch`) five times before trusting the schedule. Verify that every step completes and the post actually appears on LinkedIn.

**Week 4: Close the loop.** Build the auto research layer. Run the first analysis on your accumulated data (you should have 20+ posts by now). Review the strategy document the system produces. Does the analysis match your intuition? Where does it surprise you? The surprises are where the value lives.

**Week 5+: Iterate and expand.** Increase research frequency. Add the video component if it fits your brand. Experiment with the content mix. Let the system run and resist the urge to override its recommendations unless you have a specific reason.

One non-obvious tip: keep every version of your content strategy document in version control. I use git for this (the weekly research workflow commits strategy changes automatically). Six months from now, you'll want to trace the evolution of your strategy and understand *why* the system made each change. That history is incredibly valuable for debugging and for understanding your audience at a deeper level.

## The Bigger Picture: AI Systems That Learn From Their Own Output

What I've described here is a LinkedIn content system. But the architecture is general-purpose.

The auto research pattern — act, measure, analyze, adjust — applies to email subject lines, pricing page copy, ad creative, product descriptions, customer support responses, or any content where you can measure performance. The specific tools change (you might swap Apify for Google Analytics, Blotato for Mailchimp), but the five-layer architecture remains identical.

Karpathy's original auto research ran 700 experiments in two days on LLM training configurations. Duncan Rogoff applied the same pattern to LinkedIn content. I built my own version and discovered that transformation hooks beat "I" statements for my specific audience. Someone else will apply this to YouTube thumbnails, or landing page headlines, or outbound email sequences.

The pattern is the thing worth internalizing. The tools are just implementation details.

And the trajectory of these systems matters more than any single output. A content system that's 10% better each week doesn't sound dramatic until you realize that after 12 weeks, it's operating at a fundamentally different level than where it started. That's not linear improvement — it's compounding intelligence applied to a specific, measurable domain.

My [AI marketing team built with Claude skills](/blog/claude-skills-ai-marketing-team) was the precursor to this system. Skills gave me repeatable execution. The auto research layer gave me repeatable *learning*. The combination of both — consistent execution plus continuous optimization — is what turns a content workflow into a content engine.

Whether you build this exact system or adapt the pattern for your own use case, the principle stays the same: stop treating AI as a one-shot generator. Start treating it as a feedback loop. The difference in outcomes over time is staggering.

The system just generated tomorrow's post while I wrote this article. According to the latest strategy update, it's testing a new hook format — a question-based opener targeting a specific pain point the data surfaced last week. I'll check the engagement numbers in 48 hours. I won't be surprised if the system knows something I don't.

That's the whole point.

## Frequently Asked Questions

### How much does the full LinkedIn automation stack cost per month?

The core costs are Claude Code API usage (variable, roughly $30-60/month depending on volume), Blotato at $29/month for publishing, and Apify at approximately $1-2 per 1,000 scrapes. GitHub Actions is free for public repos or included in GitHub Pro. Total: roughly $70-100/month for a daily posting system.

### Can Karpathy's auto research pattern work for platforms besides LinkedIn?

The pattern works anywhere you can measure content performance with structured data. Email open rates, YouTube retention curves, ad click-through rates, and landing page conversion metrics all feed the same act-measure-analyze-adjust loop. Swap the scraping tool and publishing API; the architecture stays identical.

### How many posts do you need before the auto research analysis is useful?

I seeded with 47 posts and got meaningful insights immediately. For statistical significance on hook-type comparisons, aim for at least 20-30 posts with varied hook types. Fewer than 15 posts and you're reading noise, not signal. For a deeper walkthrough of the self-improving architecture, see my post on [building self-improving AI systems with Claude Code](/blog/self-improving-claude-code-systems).

### Does LinkedIn penalize automated posting through third-party tools?

LinkedIn's API terms allow publishing through authorized third-party applications. Blotato uses official platform APIs, not browser automation or scraping hacks. The risk comes from low-quality content patterns, not the publishing method. LinkedIn's 2026 algorithm detects engagement pods with 97% accuracy but doesn't penalize legitimate API-based publishing.

### How do you handle LinkedIn DM follow-ups for lead magnets?

This is currently the manual bottleneck. LinkedIn's messaging API requires partnership-level access unavailable to individual developers. I batch-respond to lead magnet requests twice daily, taking about 10 minutes total. Some builders use third-party tools with browser automation for this step, but that approach carries account-risk I'm not willing to accept.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
