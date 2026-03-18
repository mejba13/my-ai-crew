**BRAND:** mejba.me
**TITLE:** Claude Code + Blotato: I Automated My Content Pipeline
**SLUG:** claude-code-blotato-content-repurposing
**PRIMARY KEYWORD:** Claude Code content repurposing
**SECONDARY KEYWORDS:** Blotato API social media automation, AI content workflow, Claude Code skills
**META DESCRIPTION:** I built a system turning one YouTube video into 9 social media posts using Claude Code and Blotato. Full workflow with Python scripts and project structure.
**TAGS:** AI Automation, Claude Code, Content Repurposing, Social Media Workflow, Tutorial

---

# Claude Code + Blotato: How I Automated My Entire Content Pipeline

Last Thursday, I uploaded a 22-minute YouTube video about building AI agent teams. Within 40 minutes, I had nine pieces of platform-specific content sitting in a drafts folder on my machine — three LinkedIn posts with professional formatting, three Instagram carousel scripts with slide-by-slide breakdowns, and three X posts with punchy quote visuals. All tailored. All on-brand. All waiting for me to review and hit publish.

I didn't write any of them manually.

The system that produced them is a Claude Code project with four Python scripts, a Blotato API integration, and a concept I keep coming back to: skills. Not the vague "AI can do things" kind. The specific, documented, repeatable kind — where every automation has a name, a set of ingredients, a sequence of steps, and an expected output format.

Building this took me about three weekends of iteration. The first version was embarrassingly broken. Claude Code kept trying to fetch YouTube pages directly and hitting bot-detection walls. The image generation produced oversized PNGs that Blotato's API rejected. My brand assets weren't loading because I'd hard-coded paths that only existed on my work machine.

But each failure taught the system something — and that's the part nobody explains about working with Claude Code for automation. The AI doesn't just execute your instructions. It watches what breaks, proposes fixes, and updates its own skill documents so the same mistake doesn't happen twice.

I want to walk you through exactly how this pipeline works, because the 9x content multiplier it creates has genuinely changed how I think about publishing. But first, you need to understand why I abandoned three other tools before landing on this approach.

<!-- IMAGE: [Screenshot of the project folder structure in VS Code showing scripts/, drafts/, brand-assets/ directories and the claude.md file]. Alt text: "Claude Code content repurposing project structure in VS Code with scripts and drafts folders". Caption: "The project structure that powers the entire pipeline." -->

## Why Repurposing Tools Kept Failing Me

I tried Repurpose.io, manually prompting ChatGPT, and a tool called ContentFries that promised "one video, unlimited content." They all had the same problem: they treated every platform the same.

The LinkedIn post sounded like a tweet with extra paragraphs. The X posts were truncated versions of whatever the tool generated first. No platform intelligence — no understanding that LinkedIn audiences want professional frameworks while X audiences want a sharp take they can quote-tweet.

Brand consistency was the second failure. My profile picture, verified badge, color palette — none of these tools let me inject brand assets. Every output looked generic. I'd spend 20 minutes per post manually adjusting tone and reformatting. Twenty minutes times nine posts times four videos per month. Twelve hours of mechanical work on content I'd already created once.

The third problem pushed me to build my own system: the approval workflow. Or rather, the absence of one. These tools wanted to auto-publish directly to my accounts with no review step. I've been burned before — a scheduling tool once published a draft with "INSERT RELEVANT STAT HERE" to 14,000 LinkedIn followers. The comments were... educational.

I needed three things: platform-specific content intelligence, brand asset integration, and mandatory human review. Claude Code and Blotato gave me all three.

## What Makes a "Skill" Different From a Prompt

If you've been following my writing about [Claude Code agent workflows](/mejba.me/claude-skills-automation-workflows), you know I think about skills as the fundamental unit of AI automation. But this project forced me to get much more precise about what that actually means.

A prompt is a one-shot instruction. "Write me a LinkedIn post about this video." You type it, the AI responds, and the interaction is done. If the output is wrong, you revise the prompt and try again. The knowledge about what works lives in your head.

A skill is a documented recipe. It has four components:

**Name:** A clear identifier. Mine are called things like `youtube-to-linkedin`, `youtube-to-instagram-carousel`, `youtube-to-x-quote`.

**Ingredients:** The inputs the skill needs before it can run. For `youtube-to-linkedin`, the ingredients are: video transcript (plain text), video title, video URL, target audience descriptor, and brand asset directory path.

**Steps:** The ordered sequence of operations. Not vague instructions — specific, testable steps. "Extract the three most quotable statements from the transcript" is a step. "Make it sound professional" is not.

**Expected Output:** The exact format and structure of what comes out. For the LinkedIn skill, the expected output is a markdown file with a hook line (under 200 characters), a body section (150-300 words), three hashtags, and a CTA linking back to the full video.

The difference matters because skills are *iterable*. When the LinkedIn output is too long, I don't re-prompt from scratch. I adjust step 4 of the skill document — "Compress the body section to under 250 words while preserving all framework references" — and every future execution reflects that change.

This is what I mean when I say the AI self-corrects. The skill document is a living artifact. Claude Code reads it before every execution, follows its steps, and when something breaks — a web fetch gets blocked, an image exceeds Blotato's size limit — I update the skill document with the fix. The next run works correctly. The system gets smarter every time it fails.

My project currently has seven skill documents. Three for content generation (one per platform), two for image creation (LinkedIn infographic and X quote visual), one for transcript extraction, and one for the approval workflow. Each one started rough and improved through real usage.

That iterative loop — run, fail, fix the skill, run again — is the actual secret to making AI automation reliable. Not better prompts. Better documented processes.

Now let me show you the project structure that holds all of this together.

## The Project Structure That Makes Everything Work

I run this entire system from a single VS Code project. Here's what the directory looks like:

```
content-repurposer/
  claude.md
  scripts/
    extract_transcript.py
    generate_content.py
    create_visuals.py
    submit_to_blotato.py
  skills/
    youtube-to-linkedin.md
    youtube-to-instagram-carousel.md
    youtube-to-x-quote.md
    linkedin-infographic.md
    x-quote-visual.md
    transcript-extraction.md
    approval-workflow.md
  drafts/
    linkedin/
    instagram/
    x/
  brand-assets/
    profile-pic-square.png
    profile-pic-round.png
    verified-badge.png
    color-palette.json
    fonts/
  approved/
    linkedin/
    instagram/
    x/
  .env
```

A few things to notice.

The `claude.md` file at the root is the system prompt for Claude Code. This is the brain of the operation — it tells Claude Code what this project does, what skills are available, and what constraints to follow. I keep mine under 150 lines. That's a hard rule I learned through painful experimentation. Go over 150 lines and Claude Code starts losing context on the less-emphasized instructions. The model has plenty of capacity, but the signal-to-noise ratio in your system prompt matters enormously.

Here's a simplified version of my `claude.md`:

```markdown
# Content Repurposer

## Purpose
Transform YouTube video content into platform-specific
social media posts for LinkedIn, Instagram, and X.

## Available Skills
- transcript-extraction: Get plain text from YouTube videos
- youtube-to-linkedin: Professional long-form posts
- youtube-to-instagram-carousel: Educational carousel scripts
- youtube-to-x-quote: Casual quote-style posts

## Constraints
- NEVER auto-publish. All content goes to drafts/ first.
- ALWAYS use brand assets from brand-assets/ directory.
- Image files must be under 4MB for Blotato API.
- LinkedIn posts: 150-300 words, professional tone.
- Instagram carousels: 5-8 slides, educational tone.
- X posts: Under 280 characters, casual tone.

## API Configuration
- Anthropic API via .env (ANTHROPIC_API_KEY)
- Blotato API via .env (BLOTATO_API_KEY)
- OpenRouter as fallback (OPENROUTER_API_KEY)
```

The `scripts/` directory contains four Python files. Each one handles a specific phase of the pipeline. I use Python because Claude Code generates reliable Python faster than any other language in my testing, and the library ecosystem for API calls and image manipulation is unmatched.

The `drafts/` and `approved/` directories are the two stages of the approval workflow. Content starts in `drafts/`. I review it. Approved content moves to `approved/`. Only content in `approved/` gets submitted to Blotato for scheduling.

The `brand-assets/` directory is surprisingly important. Without it, every generated visual looks generic. With it, Claude Code can overlay my profile picture, apply my color palette, and include branded elements that make the social posts look like they came from a human designer — not a script.

That brand asset integration was one of the hardest things to get right. Let me show you why, and how the AI eventually solved its own problem.

## Setting Up the API Layer: Anthropic and Blotato

Before any content gets generated, two APIs need to be connected. The Anthropic Claude API handles all the intelligence — analyzing transcripts, generating platform-specific text, making creative decisions about hooks and formatting. Blotato handles the distribution — scheduling posts, managing platform connections, and handling the OAuth complexity of posting to LinkedIn, Instagram, and X.

Here's my `.env` configuration:

```bash
ANTHROPIC_API_KEY=sk-ant-api03-xxxxx
BLOTATO_API_KEY=bl_xxxxx
OPENROUTER_API_KEY=sk-or-xxxxx
BLOTATO_WORKSPACE_ID=ws_xxxxx
DEFAULT_SCHEDULE_OFFSET_HOURS=24
```

The OpenRouter key is a fallback. When I'm iterating quickly and burning through Anthropic API credits during development, I route some of the less critical calls — like formatting adjustments or hashtag generation — through OpenRouter's access to Claude 3.5 Sonnet, which is cheaper per token. The heavy lifting — transcript analysis, content generation, tone matching — always goes through the Anthropic API directly for quality.

Setting up Blotato was straightforward but required a specific sequence. You create a workspace, connect your social accounts through Blotato's OAuth flow (this happens in their web dashboard, not through the API), and then grab your workspace ID and API key. The API key scopes to that workspace, so all your connected accounts are accessible through a single key.

Here's the Python function I use to verify the connection:

```python
import requests
import os
from dotenv import load_dotenv

load_dotenv()

def verify_blotato_connection():
    """Check that Blotato API is reachable and workspace is valid."""
    headers = {
        "Authorization": f"Bearer {os.getenv('BLOTATO_API_KEY')}",
        "Content-Type": "application/json"
    }
    response = requests.get(
        f"https://api.blotato.com/v1/workspaces/{os.getenv('BLOTATO_WORKSPACE_ID')}",
        headers=headers
    )
    if response.status_code == 200:
        data = response.json()
        print(f"Connected: {data['name']}")
        print(f"Accounts: {len(data['accounts'])} linked")
        return True
    else:
        print(f"Connection failed: {response.status_code}")
        return False
```

One thing I learned: Blotato's image upload endpoint has a 4MB limit per file. My first visual generation attempts produced 6-8MB PNGs because I was rendering at high resolution without compression. Claude Code caught this during testing — the API returned a 413 error — and automatically updated the `linkedin-infographic.md` skill to include an image compression step: "After generating the visual, compress to JPEG at 85% quality. Verify file size is under 4MB before submission."

That's the iterative improvement loop in action. The skill document now carries the fix permanently.

If you'd rather have someone build this kind of automation setup from scratch, I take on custom AI workflow engagements. You can see what I've built at [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD).

But the API layer is just plumbing. The real magic — the part that makes the content actually good — happens in the platform-specific generation skills.

## Platform-Specific Content Generation: Three Audiences, Three Strategies

This is where most content repurposing falls apart. People take one piece of content and slightly reformat it for each platform. That's not repurposing. That's copy-pasting with different character limits.

Real repurposing means reimagining the same core ideas for fundamentally different audiences and consumption contexts. Here's how each platform skill works.

### How Does the LinkedIn Skill Generate Professional Posts?

LinkedIn audiences scroll during work hours. They're looking for professional insights, frameworks they can apply to their jobs, and content that makes them look thoughtful if they share it. The consumption mode is deliberate — people read LinkedIn posts more carefully than tweets.

My `youtube-to-linkedin.md` skill follows this structure:

**Step 1:** Extract the three most industry-relevant insights from the transcript. Ignore personal anecdotes, jokes, and filler. Focus on frameworks, data points, and counterintuitive findings.

**Step 2:** Write a hook line under 200 characters that either challenges a common assumption or promises a specific outcome. Never start with "I just published a new video" — nobody cares about your publishing schedule.

**Step 3:** Structure the body as a mini-framework. LinkedIn's algorithm rewards posts that feel structured — numbered lists, bold headers, and clear takeaways get more engagement than narrative paragraphs.

**Step 4:** End with a question that invites comments. Not "What do you think?" — that's lazy. Something specific: "What's the most counterintuitive thing you've learned about [topic] in production?"

**Step 5:** Add three hashtags. Two broad (#AIEngineering, #SoftwareDevelopment), one specific (#ClaudeCode). Never more than three — LinkedIn penalizes hashtag stuffing.

**Step 6:** Generate an infographic-style visual using the `linkedin-infographic.md` visual skill. This pulls my profile picture from `brand-assets/`, applies my color palette, and creates a clean graphic that highlights the post's core framework.

The visual generation was a challenge I didn't expect. My first approach used Claude Code to generate HTML, render it to an image with a headless browser, and save the PNG. It worked, but the images looked sterile. The breakthrough came when I added my `color-palette.json` to the brand assets:

```json
{
  "primary": "#1a1a2e",
  "secondary": "#16213e",
  "accent": "#0f3460",
  "highlight": "#e94560",
  "text_light": "#ffffff",
  "text_dark": "#1a1a2e",
  "background": "#f5f5f5"
}
```

With those colors loaded, the generated infographics went from "generic AI art" to "this looks like it came from my design system." Small detail, massive difference in perceived professionalism.

### Instagram Carousel: Educational Slides With Brand Identity

Instagram is a different beast entirely. People are scrolling fast, usually on mobile, and they stop for visuals — not text. The carousel format is the workaround: each slide is visual enough to catch attention, but the swipe-through mechanic rewards educational content that builds slide by slide.

My `youtube-to-instagram-carousel.md` skill generates 5-8 slides with this structure:

**Slide 1 (Hook):** A bold statement or question in large text over a branded background. Uses my accent color (#e94560) and profile picture in the corner with the verified badge overlay.

**Slides 2-6 (Content):** Each slide covers one key point from the video. Maximum 40 words per slide. The skill specifies: "Write at the reading level of a smart 16-year-old. No jargon without immediate explanation. One concept per slide."

**Slide 7 (Summary):** A quick recap of all points in a condensed format.

**Slide 8 (CTA):** "Follow for more AI engineering breakdowns" with my profile picture and username.

The verified badge integration uses Pillow to composite my profile picture with a `verified-badge.png` positioned in the standard bottom-right corner. The `create_visuals.py` script loads brand colors from `color-palette.json`, creates 1080x1080 slides, overlays the profile picture and badge, renders the text with Inter Bold at 48pt, and adds a slide counter in the corner.

```python
def create_instagram_slide(text, slide_number, total_slides):
    """Generate a single Instagram carousel slide with brand assets."""
    with open("brand-assets/color-palette.json") as f:
        colors = json.load(f)

    slide = Image.new("RGB", (1080, 1080), colors["primary"])

    # Composite profile pic + verified badge
    profile = Image.open("brand-assets/profile-pic-round.png").resize((80, 80))
    slide.paste(profile, (40, 40), profile)
    badge = Image.open("brand-assets/verified-badge.png").resize((24, 24))
    slide.paste(badge, (96, 96), badge)

    # Render slide text and counter
    draw = ImageDraw.Draw(slide)
    font = ImageFont.truetype("brand-assets/fonts/Inter-Bold.ttf", 48)
    draw.text((80, 200), text, fill=colors["text_light"], font=font)

    return slide
```

Each slide saves to `drafts/instagram/` as a separate PNG with the naming convention `{date}_{video_slug}_slide_{number}.png`.

### X Posts: Casual Quote Visuals That Get Retweeted

X (formerly Twitter) is the platform where brevity wins and personality matters most. Nobody shares a corporate-sounding tweet. They share sharp observations, surprising takes, and quotable one-liners.

My `youtube-to-x-quote.md` skill takes a completely different approach from LinkedIn and Instagram. Instead of extracting frameworks or educational breakdowns, it hunts for three things in the transcript:

1. **The most quotable single sentence.** The line someone would screenshot and share.
2. **The most contrarian take.** The moment where I said something most people would disagree with.
3. **The most practical one-liner.** A tip so specific and immediately actionable that people bookmark it.

For each one, the skill generates two outputs: the tweet text (under 280 characters, casual tone, no hashtags — they look desperate on X) and a quote visual. The quote visual is a simple branded image with the quote text in a large, clean font over my color palette background, with my profile picture and handle in the corner.

The casual tone requirement is the hardest part to get right. LinkedIn and Instagram both tolerate polished, somewhat formal writing. X punishes it. My skill document includes a tone calibration section:

```markdown
## Tone Rules for X
- Write like texting a smart friend, not presenting at a conference.
- Contractions always. "I've" not "I have." "Don't" not "Do not."
- OK to start sentences with "So" or "Look" or "Honestly."
- One emoji maximum per post. Zero is fine.
- Never use corporate phrases: "leverage," "synergy," "ecosystem."
- If the tweet sounds like it could appear in a press release, rewrite it.
```

That tone section has been revised four times. Version one sounded like shortened LinkedIn. Version two overcorrected into teenager territory. Version three used too many exclamation marks. Version four consistently produces tweets that sound like something I'd actually write at 11 PM after having a strong opinion.

Here's what surprised me about generating for three platforms simultaneously: the LinkedIn insight, the Instagram breakdown, and the X hot take are three expressions of the same underlying idea. That coherence is the real multiplier. Not just 9x volume — 9x volume with consistent messaging.

But volume means nothing without quality. That's where the approval workflow earns its place.

## The Manual Approval Workflow: Why I Refuse to Auto-Post

I know what you're thinking. "You built all this automation just to... still review everything manually?"

Yes. Absolutely. And I'd do it again.

Here's what I've learned after a year of running various content automation systems: the cost of publishing one bad post is higher than the cost of reviewing twenty good ones. That LinkedIn placeholder incident I mentioned earlier? It took me three weeks to recover the engagement rate on my profile. Three weeks of consistently good posts to undo thirty seconds of automated carelessness.

My approval workflow has three stages:

**Stage 1: Generation.** All content lands in `drafts/`. Each piece gets a metadata header:

```markdown
---
platform: linkedin
video_source: "Building AI Agent Teams - March 2026"
generated_at: 2026-03-15T14:23:00Z
status: draft
skill_version: youtube-to-linkedin-v4
---
```

**Stage 2: Review.** I open the `drafts/` folder — usually once in the morning, once in the evening. I read each piece. I check for: factual accuracy, tone appropriateness, brand consistency, and the "would I actually post this?" gut check. If it passes, I move it to `approved/`. If it needs changes, I edit directly or re-run the skill with adjusted parameters.

**Stage 3: Scheduling.** Only files in `approved/` get submitted to Blotato. The `submit_to_blotato.py` script scans the `approved/` directory, uploads any content that hasn't been submitted yet, and schedules it based on the `DEFAULT_SCHEDULE_OFFSET_HOURS` from the environment config.

```python
def submit_approved_content():
    """Submit all approved, unsubmitted content to Blotato."""
    approved_dir = Path("approved")
    headers = {
        "Authorization": f"Bearer {os.getenv('BLOTATO_API_KEY')}",
        "Content-Type": "application/json"
    }

    for platform in ["linkedin", "instagram", "x"]:
        for content_file in (approved_dir / platform).glob("*.md"):
            # Skip already-submitted files (.submitted marker)
            if content_file.with_suffix(".submitted").exists():
                continue

            metadata, body = parse_front_matter(content_file.read_text())
            offset = int(os.getenv("DEFAULT_SCHEDULE_OFFSET_HOURS", 24))
            schedule_time = datetime.utcnow() + timedelta(hours=offset)

            payload = {
                "workspace_id": os.getenv("BLOTATO_WORKSPACE_ID"),
                "platform": platform,
                "content": body.strip(),
                "scheduled_at": schedule_time.isoformat() + "Z",
            }

            response = requests.post(
                "https://api.blotato.com/v1/posts",
                headers=headers, json=payload
            )

            if response.status_code == 201:
                # Write marker to prevent double-posting
                content_file.with_suffix(".submitted").write_text(
                    json.dumps({"blotato_post_id": response.json()["id"]})
                )
```

The `.submitted` marker files prevent double-posting — I learned that one the hard way when an early version submitted the same LinkedIn post three times on a cron job without idempotency checks.

My typical review session takes 10-15 minutes for a batch of nine posts. I approve about 80% on the first pass. The other 20% need minor edits — usually a tone adjustment or a factual correction where the AI paraphrased something from the video slightly wrong.

Fifteen minutes to review nine posts. Compared to the two-plus hours it used to take me to create them from scratch. That's the efficiency gain that matters — not eliminating the human entirely, but eliminating the mechanical work and keeping the judgment.

## How Claude Code Self-Corrects: The Iterative Loop That Makes This Reliable

I mentioned earlier that Claude Code learns from failures. Let me show you exactly what that looks like, because this is the part of the workflow that surprised me most.

During my second week of testing, the transcript extraction skill hit a wall. Claude Code was trying to fetch YouTube pages directly using Python's `requests` library and parse the transcript from the HTML. YouTube's bot detection blocked it immediately.

Here's what happened in the Claude Code terminal:

```
> Running transcript-extraction skill...
> Fetching https://youtube.com/watch?v=xxxxx
> ERROR: 403 Forbidden - Bot detection triggered
> Analyzing failure...
> Updating skill: transcript-extraction.md
>   Added step: "Use youtube-transcript-api library instead
>   of direct page fetching. Install via pip if not present."
> Retrying with updated skill...
> Success: Transcript extracted (4,847 words)
```

Claude Code didn't just report the error. It diagnosed the cause, identified an alternative approach, updated the skill document with the fix, and retried. The `transcript-extraction.md` skill now has a line that reads: "NEVER attempt direct YouTube page fetching. Always use the `youtube-transcript-api` Python package for transcript extraction."

That fix is permanent. Every future run uses the corrected approach.

Another example: the image generation for LinkedIn infographics initially produced images at 2400x2400 pixels — way too large for Blotato's 4MB upload limit. The first failure triggered this sequence:

```
> Generating LinkedIn infographic...
> Uploading to Blotato...
> ERROR: 413 Payload Too Large (6.2MB)
> Analyzing failure...
> Updating skill: linkedin-infographic.md
>   Added step: "After rendering, resize to 1200x1200
>   and compress to JPEG at 85% quality.
>   Verify file size < 4MB before upload."
> Regenerating with compression...
> Upload successful (1.8MB)
```

I've counted fourteen skill document updates that originated from runtime failures. Fourteen problems that solved themselves. Each one would have been a manual debugging session — finding the error, figuring out the fix, remembering to apply it next time. Instead, the skill document carries the institutional memory.

This is why I think of skills as living documents. They're not static instructions. They're a knowledge base that grows every time something goes wrong. After three weeks of running this pipeline, my skill documents are significantly more battle-tested than the versions I originally wrote. The AI's corrections often handle edge cases I never would have anticipated.

The catch: Claude Code doesn't always get the fix right on the first try. About 30% of the time, the initial correction creates a new problem. The image compression fix first compressed to JPEG at 50% quality, making text unreadable. I manually adjusted to 85%. The AI's corrections are good starting points, not infallible solutions.

Trust the loop. Verify the output.

## Running the Full Pipeline: From Video to Scheduled Posts

Alright, here's the moment where everything connects. You've seen the project structure, the API setup, the platform skills, the approval workflow, and the self-correcting loop. Let me walk you through a complete run.

**Step 1:** I open the project in VS Code, launch Claude Code, and paste the YouTube URL. Claude Code reads the `transcript-extraction.md` skill and pulls the full transcript using `youtube-transcript-api`.

**Step 2:** Three content generation skills run in sequence. Each reads the transcript, applies platform-specific rules, and saves output to `drafts/linkedin/`, `drafts/instagram/`, or `drafts/x/`.

**Step 3:** Visual generation skills fire next — LinkedIn infographic, Instagram carousel slides, X quote visuals. All pull from `brand-assets/`.

**Step 4:** I open `drafts/`, review everything, make edits, and move approved content to `approved/`.

**Step 5:** `python scripts/submit_to_blotato.py` picks up everything in `approved/`, schedules through Blotato's API, and marks each file as submitted.

Total time from video URL to scheduled content: about 40 minutes, of which 15 minutes is my review. For a single 20-minute video, I get three LinkedIn posts (different angles), three Instagram carousel sets (different sections), and three X posts (quote, contrarian take, practical tip). Nine pieces of content from one video.

That's the 9x multiplier. Not theoretical. Actual output I'm running weekly.

## What I Got Wrong and What I'd Do Differently

Three weeks into production use, some honest reflection.

**The claude.md file went through five rewrites.** My first version was 300 lines — Claude Code ignored half the instructions. At 150 lines, execution became reliable. With AI system prompts, less is more. Cut anything that isn't a hard constraint or critical context.

**I overengineered image generation.** Headless Chromium rendering HTML to PNG was slow and brittle. Switching to Pillow was faster, more predictable, and gave pixel-level control. Skip browser rendering entirely.

**Quality isn't uniform across platforms.** LinkedIn posts hit about 90% of what I'd write manually. Instagram carousels land around 80%. X posts range from great to mediocre — short-form casual writing is genuinely harder for AI. I'm still tuning that skill document.

**Start with one platform, not three.** Building simultaneously meant every debugging session was tripled. If I started over, I'd perfect LinkedIn first, then clone and adapt for Instagram and X.

**The approval workflow is non-negotiable.** I tried auto-posting X posts for one week. Two of fourteen had tone issues I would have caught manually. One referenced a "recent update" that was three months old. Manual review stays.

## What This Looks Like at Scale: The Numbers After 30 Days

After running this pipeline for a full month with weekly uploads: four videos produced 36 content pieces. Of those, 29 passed first-pass review (81% approval rate). Five needed minor edits. Two I regenerated with adjusted skill parameters. Average weekly time: 40 minutes for nine posts.

Before this system, I spent 3-4 hours weekly on repurposing — when I did it at all. The consistency improvement matters more than the time savings. I went from posting sporadically to nine times per week, every week, with on-brand messaging across platforms.

The biggest surprise? Cross-platform coherence drove more profile visits than raw volume. When someone saw my LinkedIn framework post, then the same concept as a punchy X quote an hour later, pattern recognition kicked in. Several people DM'd me saying "I keep seeing your content everywhere." That wasn't an accident — nine posts from one video, released across platforms over 48 hours.

You can't get that coherence from manual repurposing, because manual repurposing happens on different days in different moods with different effort levels. Automated repurposing happens in one batch, from one source, with consistent quality parameters.

## Where Does This Go From Here?

Three extensions on my roadmap. Parallelized generation using Python's `asyncio` to cut generation time from 25 minutes to about 10. An analytics feedback loop that feeds post performance data back into skill documents — if question-ending CTAs consistently outperform statements on LinkedIn, the skill should learn that automatically. And newsletter integration as a fourth output type, which the skill architecture supports naturally with one new markdown file.

The underlying architecture scales because each new platform is just a new skill document. The approval workflow, brand assets, and Blotato integration stay the same. That's the power of building on skills instead of one-off scripts — the system is modular by nature.

I started this project because I was tired of mechanical content work. I ended up rethinking the relationship between creating and distributing content entirely. Creation is the hard part — the thinking, recording, explaining. Distribution should be automatic. Your ideas deserve to reach every platform where your audience lives, in the format each platform rewards.

One video. Nine posts. Forty minutes. The system gets smarter every week.

What's the one piece of content you keep meaning to repurpose but never find time for? That's your first skill document.

## Frequently Asked Questions

### Can I use Claude Code and Blotato without writing Python scripts?

You can use Claude Code's built-in terminal to run individual skill commands without custom scripts. But the Python layer handles image generation, file management, and Blotato API calls more reliably than shell commands alone. For the full pipeline, Python is worth the setup.

### How much does the Blotato and Anthropic API combination cost per month?

My monthly API spend averages $25-40 for four videos worth of content generation. Anthropic API handles the heavy text generation (roughly $15-25), and Blotato's API usage falls within their standard plan pricing. OpenRouter as a fallback adds $5-10 for lighter tasks. For a deeper look at managing AI API costs, see my [AI agent cost optimization guide](/mejba.me/ai-agent-cost-optimization-guide).

### What happens when Blotato's API rejects a post?

The `submit_to_blotato.py` script logs the rejection with the full error response. Common causes: oversized images (compress below 4MB), invalid scheduling times (must be future timestamps), or expired OAuth tokens (re-authenticate through Blotato's dashboard). The rejection doesn't affect other posts in the batch.

### How do I keep the claude.md system prompt under 150 lines?

Focus on constraints and available skills only. Remove explanations, examples, and context that Claude Code can infer. Every line should be either a hard rule ("NEVER auto-publish") or a reference to a skill document that contains the detailed instructions. Move all implementation details into the skill files themselves.

### Does this workflow support video content in languages other than English?

The `youtube-transcript-api` library supports transcripts in multiple languages. The content generation skills would need language-specific tone rules, but the pipeline architecture stays identical. I haven't tested this myself with non-English content yet — it's on my list for Q2 2026.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
