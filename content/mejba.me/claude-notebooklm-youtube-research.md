**BRAND:** mejba.me
**TITLE:** Claude Code + Notebook LM: Zero-Token Research Stack
**SLUG:** claude-notebooklm-youtube-research
**TAGS:** AI Tools, Claude Code, Notebook LM, YouTube Research Automation, Tutorial

---

Three weeks ago, I burned $4.20 in Claude API tokens and produced a research report full of hallucinated statistics.

I was building a pipeline to track which AI skills were trending on YouTube — what developers are actually teaching versus what the job market demands. The plan felt solid: use Claude Code to search YouTube, pull video data, analyze the content, and generate a weekly trend report. Six hours in, I had a beautifully formatted document with citations to videos that said things they never actually said.

That's the quiet failure mode nobody warns you about. Claude Code is exceptional at orchestrating tasks and synthesizing structured data — but when you ask it to analyze YouTube video content by reading titles and descriptions alone, it fills the gaps. Confidently. Incorrectly.

I tried three workarounds. The YouTube Data API's free tier quota is brutal for any real research volume. Paid transcription services were expensive enough that I closed the pricing tab immediately. I even tried prompting Claude to "infer" video content from metadata signals like duration, channel authority, and title specificity. That produced creative fiction.

What actually fixed it was something I'd been treating as a toy: Google's Notebook LM. Specifically, the unofficial Python API that lets Claude Code talk to Notebook LM programmatically. That combination completely rewired how I approach research. Token cost dropped to near zero. Research quality went through the roof. And the whole setup took under an hour.

Here's the complete system — every script, every step, and the one gotcha that took me two days to figure out.

---

## Why YouTube Is the Most Underused Research Source in AI

If you're building anything in the AI space right now, YouTube might be more valuable than any paid research service you're using. Tutorial creators publish new content constantly. View counts are a direct signal of market interest. Comment sections are raw, unfiltered feedback about what's confusing people and what they actually want next.

The problem is accessing that data at scale without spending your entire week watching videos.

Manual review is obviously out. Even at 2x speed, fifty relevant videos represents six to eight hours of work — and you'd still be taking notes in some fragmented system, trying to synthesize patterns across dozens of creators. YouTube's official API gives you metadata but not transcripts, and the daily quota caps are specifically calibrated to frustrate any serious research workflow.

I've been using Claude Code for research since early 2025. The tool is genuinely good at web search, file organization, running scripts, and making sense of structured data. But for YouTube video content — the actual spoken words inside those videos — it has a gap that workarounds don't solve cleanly.

That gap is exactly where Notebook LM belongs.

Notebook LM is Google's RAG (Retrieval-Augmented Generation) system. Give it sources — PDFs, URLs, YouTube video links — and it builds a dedicated knowledge base from the actual content. For YouTube videos, it automatically fetches and indexes the captions. Ask it what skills the top 40 AI tutorials recommend, and it answers from what those creators actually said, with specific citations.

The real insight isn't Notebook LM alone. It's what happens when you route Claude Code's orchestration capabilities to Notebook LM's processing infrastructure. Claude handles the decisions — which videos to analyze, what questions to ask, how to format the output. Notebook LM handles the heavy lifting entirely on Google's servers, for free.

Your Claude API tokens stay almost completely untouched. The expensive part — reading fifty video transcripts and finding patterns across them — runs offsite at zero marginal cost to you.

But before I show you the setup, there's a critical limitation that shapes everything about how this system should be built. I planted that thread intentionally — we'll get to it in the real talk section, and it'll save you from the mistake I made on day two.

---

## How the Three-Layer Stack Actually Works

Understanding the architecture before touching any code makes the implementation obvious. Three components. Each handles what it's genuinely best at.

**Claude Code: The Orchestration Layer**

Claude Code's job in this stack is pure orchestration. It receives your research question, decides which videos are worth analyzing, runs scripts, monitors status, and formats final output. It never reads video transcripts directly — that responsibility gets delegated immediately.

This is the shift in thinking that makes the whole system work. Most people's instinct is to feed everything into the LLM context window. For transcript analysis across dozens of videos, that's the expensive wrong answer. Claude Code is best used as the intelligent decision-maker that routes work — not as the processor that does every computation itself.

**YT-DLP: The Data Collection Layer**

YT-DLP is an open-source command-line tool that scrapes YouTube metadata efficiently and reliably. Title, channel, view count, duration, upload date, description, and the video URL — everything you need to filter intelligently before uploading anything to Notebook LM.

That filtering step matters more than you'd think. Notebook LM caps you at 50 sources per project. If you waste those slots on low-quality or outdated videos, your analysis suffers. YT-DLP lets you set hard filters: minimum view count, date range, maximum duration. A custom Claude Code skill built on top of YT-DLP handles this automatically.

**Notebook LM: The Analysis Layer**

Notebook LM processes the actual content. Feed it YouTube URLs as sources, and it fetches the captions, indexes them, and builds a knowledge base grounded in what those videos actually contain. No hallucination about content it hasn't read. No invented trends.

The deliverables system is where Notebook LM earns its place in this stack. Beyond simple Q&A, it can generate structured briefing documents, FAQ compilations, study guides, flashcard sets, and audio overviews — a podcast-style AI-generated discussion of all your sources. For trend research, the briefing document is the most useful. For learning workflows, the audio overview is genuinely interesting.

Tang Ling built the unofficial Python library `notebooklm-pi` that makes programmatic access possible. No web browser required. Create notebooks, add sources, trigger deliverable generation, download outputs — all from Python code that Claude Code can run automatically.

That's the full picture. Now let's build it.

---

## Step 1: Install YT-DLP and Validate the Setup

YT-DLP installs via pip and works immediately with no authentication required for public videos:

```bash
pip install yt-dlp
```

Validate the installation by running a metadata extraction on any public video:

```bash
yt-dlp --dump-json --no-download "https://www.youtube.com/watch?v=dQw4w9WgXcQ" | python3 -m json.tool | head -40
```

You should see structured JSON containing title, channel name, view count, duration, upload date, and other metadata. If that runs cleanly, the data collection layer is ready.

---

## Step 2: Install and Authenticate the Notebook LM API

```bash
pip install notebooklm-pi
```

After installation, authenticate via the CLI:

```bash
notebooklm-pi login
```

This opens a browser window for Google OAuth. Log in with the same Google account you use with Notebook LM. Credentials are stored locally, and the library handles token refresh automatically.

Test the connection before writing any workflow code:

```python
from notebooklm_pi import NotebookLM

client = NotebookLM()
notebooks = client.list_notebooks()
print(f"Connected. Found {len(notebooks)} existing notebooks.")
```

A successful connection — even showing zero notebooks — confirms authentication worked. If you get an auth error, log out and re-authenticate:

```bash
notebooklm-pi logout
notebooklm-pi login
```

---

## Step 3: Build the YouTube Scraping Script

Create `youtube_research.py` in your project directory. This script takes a search query and returns a filtered, ranked list of video URLs ready for analysis:

```python
import subprocess
import json
import sys
from typing import List, Dict


def search_youtube_videos(
    query: str,
    max_results: int = 60,
    min_views: int = 5000,
    max_duration_seconds: int = 7200,
    published_after: str = "20250101"
) -> List[Dict]:
    """
    Search YouTube and return filtered video metadata.

    Args:
        query: Search query string
        max_results: Number of results to fetch before filtering
        min_views: Minimum view count to include (filters low-quality content)
        max_duration_seconds: Max video length (7200 = 2 hours)
        published_after: YYYYMMDD format — filters to recent content

    Returns:
        List of dicts with url, title, views, duration, channel, uploaded
    """

    cmd = [
        "yt-dlp",
        f"ytsearch{max_results}:{query}",
        "--dump-json",
        "--no-download",
        "--match-filter", f"view_count >= {min_views}",
        "--date-after", published_after,
    ]

    result = subprocess.run(cmd, capture_output=True, text=True, timeout=60)

    if result.returncode != 0:
        print(f"YT-DLP error: {result.stderr}", file=sys.stderr)
        return []

    videos = []
    for line in result.stdout.strip().split('\n'):
        if not line:
            continue
        try:
            data = json.loads(line)
            duration = data.get('duration', 0) or 0

            if duration > max_duration_seconds:
                continue

            videos.append({
                'url': data['webpage_url'],
                'title': data.get('title', 'Unknown'),
                'channel': data.get('uploader', 'Unknown'),
                'views': data.get('view_count', 0),
                'duration_mins': round(duration / 60, 1),
                'uploaded': data.get('upload_date', 'Unknown'),
            })
        except (json.JSONDecodeError, KeyError):
            continue

    # Sort by view count descending, cap at 50 (Notebook LM source limit)
    videos.sort(key=lambda x: x['views'], reverse=True)
    return videos[:50]


if __name__ == "__main__":
    query = sys.argv[1] if len(sys.argv) > 1 else "Claude Code AI development 2025"
    results = search_youtube_videos(query)

    print(f"\nFound {len(results)} qualifying videos:\n")
    for i, v in enumerate(results, 1):
        print(f"{i}. {v['title']}")
        print(f"   Channel: {v['channel']} | Views: {v['views']:,} | {v['duration_mins']} mins")
        print(f"   URL: {v['url']}\n")
```

Test it with your actual research topic:

```bash
python3 youtube_research.py "AI agent frameworks tutorial 2025"
```

Review the output carefully. This is your checkpoint — the moment where you decide which videos actually belong in the analysis before committing them to Notebook LM. If you see irrelevant results, tighten your query or increase the view count minimum.

---

## Step 4: Build the Notebook LM Analysis Script

Create `analyze_with_notebooklm.py`. This script creates a new Notebook LM project, adds your filtered videos as sources, waits for indexing, then generates analysis:

```python
from notebooklm_pi import NotebookLM
import time
import sys


def analyze_videos(
    video_urls: list,
    notebook_title: str,
    research_question: str,
    output_file: str = "research_output.md"
) -> str:
    """
    Creates a Notebook LM project, adds YouTube sources, generates analysis.

    Args:
        video_urls: List of YouTube video URLs
        notebook_title: Name for the Notebook LM project
        research_question: The specific analytical question to answer
        output_file: Where to save the output

    Returns:
        Path to the saved output file
    """

    client = NotebookLM()

    print(f"Creating notebook: {notebook_title}")
    notebook = client.create_notebook(title=notebook_title)

    print(f"\nAdding {len(video_urls)} video sources...")
    failed = []
    for i, url in enumerate(video_urls, 1):
        try:
            client.add_source(notebook.id, url=url)
            print(f"  [{i}/{len(video_urls)}] Added: {url}")
            time.sleep(1.5)  # Rate limiting — be gentle with the API
        except Exception as e:
            print(f"  [{i}/{len(video_urls)}] Failed: {url} — {e}")
            failed.append(url)

    if failed:
        print(f"\nWarning: {len(failed)} sources failed to add.")

    # Notebook LM needs time to fetch and index video captions
    # 3 minutes handles most batches of 30-50 videos reliably
    print(f"\nIndexing sources (3 minutes)...")
    for i in range(18):
        time.sleep(10)
        print(f"  {(i + 1) * 10}s elapsed...")

    print("\nGenerating briefing document...")
    briefing = client.generate_briefing(notebook.id)

    print("Running targeted analysis...")
    targeted = client.query(notebook.id, research_question)

    output = f"""# Research Report: {notebook_title}

Generated: {time.strftime('%Y-%m-%d %H:%M')}
Sources analyzed: {len(video_urls) - len(failed)} videos

---

## Briefing Document

{briefing}

---

## Targeted Analysis

**Question:** {research_question}

{targeted}
"""

    with open(output_file, "w") as f:
        f.write(output)

    print(f"\nSaved to: {output_file}")
    return output_file


if __name__ == "__main__":
    # Replace with URLs from youtube_research.py
    urls = [
        "https://www.youtube.com/watch?v=EXAMPLE_1",
        "https://www.youtube.com/watch?v=EXAMPLE_2",
        # Add all your filtered URLs here
    ]

    analyze_videos(
        video_urls=urls,
        notebook_title="AI Skills Research — March 2026",
        research_question=(
            "What are the top 10 most-mentioned skills, tools, and frameworks "
            "across all video sources? Include specific tool names, programming languages, "
            "and any emerging trends multiple creators agree on."
        ),
        output_file="research/ai-skills-march-2026.md"
    )
```

The 3-minute indexing wait is intentional and non-negotiable. Notebook LM needs that time to fetch captions from YouTube and process them into its knowledge base. Skip the wait and your queries return empty or inaccurate results — ask me how I know.

---

## Step 5: Wire Everything Into Claude Code

The final step is giving Claude Code a skill file that connects these scripts into a single, conversational workflow. Create `.claude/skills/youtube-research.md` with instructions telling Claude to:

1. Accept a research topic and optional filters from the user
2. Run `youtube_research.py` and display the ranked video list
3. Pause for optional user review (you can approve, exclude specific videos, or adjust filters)
4. Run the analysis with the approved URLs
5. Save the output to a structured `research/` folder

Once the skill is in place, the entire workflow runs conversationally:

```
You: Research trending AI agent frameworks on YouTube, past 6 months

Claude Code: [runs youtube_research.py]
Found 43 qualifying videos. Top results:
1. "Building Production AI Agents" — 287K views — 45 mins
2. "LangGraph vs CrewAI vs AutoGen" — 194K views — 38 mins
...

Approve this list or adjust filters?

You: Looks good, run the analysis

Claude Code: [runs analyze_with_notebooklm.py]
Creating notebook... Adding 43 sources... Indexing (3 min)...
Generating analysis... Saved to research/ai-agents-march-2026.md
```

From trigger to saved output: about 25-35 minutes total, with 3 of those minutes being idle indexing time.

Pro tip: Notebook LM's API exposes functionality the web UI buries. Ask Claude Code to also trigger audio overview generation after the analysis — you end up with an AI-generated podcast synthesizing all 40+ videos. Not perfect, but genuinely useful for processing research during commutes.

---

## The Honest Version: What I Got Wrong and What to Watch For

Here's the part most tutorials skip.

My first research session with this system had zero filtering. I told YT-DLP to grab the top results for my search query with no view count minimum and no date filter. Notebook LM hit its 50-source cap with a mix of viral clickbait, beginner tutorials from 2022, and some videos that were tangentially related at best. The briefing it generated was technically accurate to those sources — and almost completely useless for my actual research question.

The analysis kept surfacing references to tools that the AI community had moved past eighteen months ago. Lots of enthusiasm for things that are now considered antipatterns. The date filter and view count minimum aren't optional optimizations. They're the difference between getting signal and getting noise.

Second issue: the unofficial API. This is the one I promised to come back to.

`notebooklm-pi` is not an official Google product. Tang Ling built it by reverse-engineering Notebook LM's internal API calls. Google hasn't sanctioned it, hasn't committed to keeping those endpoints stable, and could break the library with any Notebook LM update. I've been running this workflow for three weeks without a failure — but three weeks isn't a track record. If you're building this into something critical (a client-facing automated report, a scheduled newsletter, anything where failure has real business consequences), build a fallback and monitor the library's GitHub repo regularly.

For personal research and internal projects? The risk profile is acceptable. For production systems? Add a human checkpoint.

One more honest thing: the 50-source cap is a genuine constraint that shapes your research design. You can't feed Notebook LM "all AI YouTube content from 2025." You have to be specific about your question before you start. I've found this constraint is actually healthy — it forces research precision. A tight, well-filtered question produces dramatically better output than a vague broad one.

**Where this is all heading**

This pattern — a lightweight AI orchestrator routing expensive processing to free external infrastructure — is going to define how smart developers build AI workflows over the next couple of years. The instinct to do everything inside one LLM's context window is expensive and often unnecessary.

The better instinct is asking: which parts of this workflow actually require expensive AI intelligence, and which parts can be delegated to purpose-built infrastructure? In this stack, Claude Code handles the strategic decisions. Notebook LM handles the heavy computation. The split is clean, and the cost savings are real.

That's not a workaround. That's architecture.

---

## What the Numbers Actually Look Like

Before this workflow:
- **Token cost per research session:** $4-6 in Claude API usage
- **Time to complete:** 5-6 hours including manual video review and note synthesis
- **Videos analyzed:** 8-12 (the ones I could reasonably review manually)
- **Output quality:** Directional insights I'd hedge with "based on what I observed"

After implementing this stack:
- **Token cost per research session:** $0.10-0.30 (Claude Code orchestration only)
- **Time to complete:** 25-40 minutes including the indexing wait
- **Videos analyzed:** 40-50 per session
- **Output quality:** Grounded claims with specific citations to which video said what

The quality shift is the part I didn't fully anticipate. When a research report says "three of the five highest-viewed tutorials on this topic explicitly recommended X framework over Y, citing Z reason" — that's a claim I can stand behind. The old reports were impressions. These are findings.

The unexpected benefit: Notebook LM's audio overview feature turned fifty tutorial videos into a twenty-minute AI-generated podcast. I listened to the first one on a walk. It oversimplifies in places, but for a high-level orientation to a new research topic? It works better than I expected.

For my specific use case — weekly AI skill trend tracking — the workflow now runs on a Monday schedule. By the time I'm done with my first coffee, a new trend report is waiting in my `/research` folder. The consistency alone changed how I approach content strategy.

---

## One Research Session This Week

Here's what I want you to do with this: pick one research question you've been putting off because it felt too time-consuming. Market research on a tech topic, competitive analysis on what tutorial creators in your niche are teaching, trend tracking for a skill category you want to write about.

Build one iteration of this workflow manually this week. Not the full automated system. One session, triggered by hand, using the scripts from this post. See how the output compares to what you'd produce yourself in the same time window.

I ran my first session on a Tuesday afternoon with forty minutes between meetings. The output surprised me enough that I cancelled the next two hours and spent them building the skill file to make it repeatable. The evidence was that clear.

What started as a frustrating token bill and a hallucinated report ended up as something closer to a real research assistant — one that runs on its own schedule, costs almost nothing, and grounds every claim in actual video content.

Claude Code's intelligence plus Notebook LM's grounding changes the quality ceiling on what's possible with this kind of research. Neither tool alone gets you there. Together, they're a genuinely different kind of system.

Build the first version. Run it against one real question. Then tell me what you find — I'm genuinely curious which topics people explore first.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
