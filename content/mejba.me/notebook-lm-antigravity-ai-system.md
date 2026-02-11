**BRAND:** mejba.me
**TITLE:** I Built an AI Research System With Notebook LM
**SLUG:** notebook-lm-antigravity-ai-system
**TAGS:** AI Tools, Google Notebook LM, AI Automation, Research Workflow, Tutorial

---

Two weeks ago, I hit a wall I didn't see coming. Not a coding wall — a knowledge wall. I had fourteen browser tabs open, three PDF reports half-read, a YouTube playlist of tutorials I'd bookmarked but never watched, and a Google Doc full of notes that made sense when I wrote them at midnight but looked like hieroglyphics by morning.

My projects were growing faster than my ability to keep context in my head. Client research, competitive analysis, technical documentation, content strategy — all living in different tools, none of them talking to each other. Every time I switched contexts, I lost twenty minutes just rebuilding the mental model of where I'd left off.

Then I stumbled into a combination that fixed this problem so completely that I'm almost annoyed I didn't find it sooner. Google's Notebook LM paired with Anti-Gravity. One tool reads and remembers everything. The other builds things from what it remembers. Together, they gave me something I've been trying to cobble together for months: an AI research system that actually scales — and doesn't drain my API budget doing it.

But here's the part that genuinely surprised me. The real power isn't in either tool alone. It's in a specific integration pattern using MCP that turns them into something neither was designed to be on its own. I'll show you the exact setup, but first, you need to understand why the obvious approaches to this problem all fail in the same way.

## The Problem That Every AI Power User Eventually Hits

If you've been using AI tools seriously — not just asking ChatGPT to write emails, but actually building workflows around LLMs — you've probably run into this exact bottleneck.

You want the AI to know things. Specific things. Your client's brand guidelines. The technical specs from that 47-page PDF. The insights from twelve YouTube videos you watched last month about growth strategy. The competitive landscape research you did in Q3.

So you try the obvious solution: paste everything into the context window. And it works — for about five minutes. Until you hit the token limit. Or the model starts hallucinating because you've crammed so much context in that it can't distinguish between your brand guidelines and your competitor's pricing page.

I tried building custom RAG pipelines. Spent a weekend setting up vector databases, chunking documents, tuning retrieval parameters. It worked, technically. But the maintenance overhead was brutal, and every time I wanted to add a new data source, I was back in configuration hell.

I tried creating massive system prompts. I tried uploading files to Claude Projects. I tried building custom GPTs with knowledge bases. Each approach solved one piece of the puzzle while creating new problems elsewhere.

What I actually needed was dead simple in concept: a system where I could throw unlimited research material into organized containers, query those containers intelligently, and then use the answers to build real things — documents, dashboards, applications, presentations. Without token anxiety. Without configuration nightmares. Without spending more on API calls than my actual project budgets.

Notebook LM plus Anti-Gravity turned out to be exactly that system. And the way they connect through MCP is what makes the whole thing work in a way my DIY solutions never did.

## What These Tools Actually Do (And Why They're Better Together)

Let me break this down clearly, because the naming doesn't help. "Notebook LM" sounds like a note-taking app. "Anti-Gravity" sounds like a physics experiment. Neither name tells you what they actually are.

**Google Notebook LM** is a reader and knowledge base. You feed it sources — documents, YouTube videos, articles, PDFs, Google Drive files, websites — and it ingests them deeply. Not surface-level summarization. Genuine comprehension of the content, with the ability to answer nuanced questions, find connections between sources, generate audio overviews, create study guides, and surface insights you'd miss reading the material yourself.

Think of it as having a research assistant who actually reads every document you give them. Cover to cover. And remembers all of it perfectly.

**Anti-Gravity** is a builder and command center. It constructs applications, dashboards, websites, and software systems. But more importantly, it orchestrates. It connects to external services through MCP, runs multiple AI agents simultaneously, and executes multi-step workflows. Think of it as the brain that tells other tools what to do.

Here's why the combination matters: Notebook LM handles the expensive part of AI work — storing and retrieving large amounts of context — without consuming tokens in Anti-Gravity. When Anti-Gravity needs to know something, it queries Notebook LM through MCP, gets a focused answer, and uses that answer to build or create. The research context lives in Notebook LM. The execution happens in Anti-Gravity. Neither tool is overwhelmed.

This architecture solved my token anxiety overnight. Instead of cramming fifty pages of research into every prompt, Anti-Gravity asks Notebook LM specific questions and gets targeted responses. My token usage in Anti-Gravity dropped by roughly 60% for research-heavy projects, and the quality of outputs went up because the model was working with focused, relevant context instead of a firehose of everything.

That's the theory. Here's what actually happened when I set it up for a real project.

## Setting Up the Connection: MCP Is the Bridge

The integration between these two tools runs through MCP — Model Context Protocol. If you've worked with MCP servers in Claude Code or other AI tools, the concept is familiar. If you haven't, think of MCP as a universal remote control that lets one AI tool operate another.

Here's the actual setup process I followed:

**Step 1: Install Anti-Gravity.**

Download the desktop application. Standard install, nothing unusual. The first launch walks you through connecting your accounts and setting up your workspace.

**Step 2: Configure the Notebook LM MCP server.**

This is the critical step. Anti-Gravity needs an MCP server that knows how to communicate with Notebook LM's API. There's a GitHub repository with the server code and configuration files.

The setup involves cloning the repo, installing dependencies, and configuring authentication. The auth flow uses browser-based Google login — Anti-Gravity opens a browser window, you log into your Google account, and the connection is established.

The configuration in Anti-Gravity's MCP settings looks something like this:

```json
{
  "mcpServers": {
    "notebookLM": {
      "command": "node",
      "args": ["path/to/notebook-lm-mcp/index.js"],
      "env": {
        "GOOGLE_AUTH_TOKEN": "your-oauth-token"
      }
    }
  }
}
```

**Step 3: Verify the connection.**

Once the MCP server is running, you can test it directly in Anti-Gravity's chat interface. Ask it to list your existing notebooks or create a new one. If it responds with your actual Notebook LM data, you're connected.

Pro tip: The first time I set this up, the auth token expired after an hour and I spent thirty minutes debugging what I thought was a configuration error. If your connection suddenly stops working, refresh the auth token first before diving into config files. Saved me a lot of frustration on the second setup.

**Step 4: Create your first notebook programmatically.**

This is where things start getting interesting. Instead of going to Notebook LM's web interface and manually creating notebooks, you tell Anti-Gravity to do it:

"Create a new notebook called 'YouTube Growth Strategies' and add these five sources: [URLs]."

Anti-Gravity sends the commands through MCP, and the notebook appears in your Notebook LM account, fully populated and ready to query. I created seven topic-specific notebooks in about twelve minutes this way. Manually, that would have been an hour of clicking and pasting.

If that was all this integration did — save time on notebook creation — it would barely be worth writing about. But notebook creation is just level one. There are four more levels that get progressively more powerful, and level three is where I started building things I genuinely couldn't have built before.

## Five Levels of Power (And the One That Changed My Projects)

I think of this system in five levels of capability. Each level builds on the previous one, and each unlocks something the level below can't do.

### Level 1: Programmatic Notebook Management

Create, populate, and organize notebooks through conversational commands in Anti-Gravity. No manual clicking through Notebook LM's interface.

This sounds mundane, but it matters for scale. When I'm starting a new research project, I might need eight or ten topic-specific notebooks. Doing this manually is death by a thousand clicks. Doing it programmatically takes minutes.

I created a set of expert notebooks for a client project on AI-driven marketing: one for audience psychology, one for content distribution channels, one for competitor analysis, one for engagement metrics, one for case studies. Each notebook got populated with fifteen to twenty sources — articles, videos, research papers, company blogs.

Total setup time: about twenty-five minutes. Total research coverage: probably three hundred pages of source material, organized and queryable.

### Level 2: Multi-Agent Notebook Querying

Run multiple AI agents in Anti-Gravity, each connected to a different notebook, and synthesize their responses.

This is where the "command center" metaphor starts to click. Imagine having five research assistants, each an expert in a different domain, and being able to ask them all a question simultaneously.

"Agent 1: What does the audience psychology research say about decision fatigue in subscription models? Agent 2: What distribution channels are competitors using for similar products? Agent 3: What engagement metrics should we prioritize?"

Each agent queries its respective notebook and returns focused, source-grounded answers. Anti-Gravity synthesizes them into a coherent response. What would take me a full afternoon of reading across multiple sources takes about ninety seconds.

I want to be specific about the quality here, because "AI synthesis" can mean anything from genuinely insightful to incoherent word salad. The answers I get from this setup are grounded in specific sources I've vetted myself. Notebook LM isn't searching the internet and returning whatever it finds — it's querying the exact documents I've given it. The accuracy is dramatically higher than general-purpose RAG because the knowledge base is curated, not scraped.

### Level 3: Building Applications From Notebook Knowledge

This is the level that changed my workflow. Anti-Gravity can take the insights from notebook queries and build interactive applications — dashboards, one-page websites, data visualizations — right in the conversation.

Here's a real example. For a YouTube strategy project, I asked Anti-Gravity to query three notebooks (growth tactics, thumbnail psychology, and audience analytics) and build an interactive HTML dashboard showing:
- Key growth levers with priority ratings
- Thumbnail design principles with visual examples
- Audience engagement patterns by content type
- A strategy recommendation engine based on the research

It produced a complete, functional HTML page in under four minutes. The page pulled its content from actual research in my notebooks — not hallucinated statistics or generic advice. I could open it in a browser, interact with the charts, and share it with my team as a strategy reference.

Try building that with a regular chatbot. You'd spend two hours copying and pasting between tools, and the result would be a static document, not an interactive application.

I've since built research dashboards for three client projects using this pattern. The process is always the same: curate sources into notebooks, query notebooks for key insights, and tell Anti-Gravity to build something visual from those insights. Start to finish, each dashboard took about thirty minutes. Previously, this kind of deliverable was a full-day project involving separate research, design, and development phases.

### Level 4: Automated Resource Generation

Notebook LM can generate audio overviews (podcast-style summaries of your research), and Anti-Gravity can trigger this programmatically. Same with slide decks, study guides, mind maps, and formatted reports.

I set up a workflow where every new notebook automatically gets an audio overview generated. When I'm commuting or doing dishes — times when I can listen but not read — I play these audio summaries to stay current on research I've collected but haven't had time to sit down with.

Is the audio quality going to win any podcast awards? No. But it's surprisingly listenable, and it turns dead time into research time without any manual effort on my part.

Anti-Gravity can also generate presentation decks from notebook content. I tested this with a client strategy presentation: gave it the notebook, told it the audience and key messages, and got back a structured slide deck that needed maybe fifteen minutes of refinement before it was client-ready. Not perfect out of the box, but a massive head start.

### Level 5: Local File Ingestion

This level unlocked something I didn't expect to need as badly as I did. You can upload local files — PDFs, text documents, internal reports — into Notebook LM through Anti-Gravity.

Why does this matter? Because some of the most valuable research material isn't on the internet. Client SOPs. Internal compliance documents. Proprietary research reports. Historical project documentation. Contract terms you need to reference during negotiations.

The process involves converting files (especially PDFs) into text format and importing them into Notebook LM. Anti-Gravity handles the conversion and upload. Once the files are in a notebook, you can query them like any other source.

I imported a 200-page technical specification document for a client's existing system. Within minutes, I could ask questions like "What authentication method does the legacy system use?" or "What are the documented rate limits for the internal API?" and get accurate, cited answers. Previously, answering those questions meant CTRL-F through a PDF and hoping I found the right section.

## The Skills Layer: Teaching the System Your Preferences

One concept that ties everything together is Skills — and if you read my post on Claude Co-Work, you know I'm becoming a believer in this pattern across AI tools.

Skills in Anti-Gravity are reusable instruction sets that codify how the system should behave in specific contexts. For the Notebook LM integration, I built three Skills that dramatically improved output quality:

**The Research Analyst Skill** tells Anti-Gravity how to query notebooks for my projects. It includes instructions about my preferred level of detail, how to cite sources, when to flag contradictory information across sources, and how to structure synthesis responses. Without this Skill, every query needed manual instructions about format and depth. With it, every query returns consistently useful output.

**The Client Context Skill** embeds information about my active clients — their industries, their goals, their constraints, their terminology preferences. When I query notebooks for client projects, this Skill automatically tailors the responses to be relevant to the specific client. A query about "engagement optimization" returns different insights depending on whether I'm working on an e-commerce project or a SaaS platform.

**The Dashboard Builder Skill** provides Anti-Gravity with my design preferences, color schemes, layout patterns, and data visualization standards for when it builds interactive applications from notebook research. This means every dashboard it generates looks consistent and professional without me specifying design details each time.

Building these Skills took about two hours total. They've saved me probably ten hours over the past two weeks just in reduced instruction-giving and output reformatting.

The key insight — and I keep coming back to this across different AI tools — is that Skills turn one-time configurations into compound advantages. Every time the Skill runs, it applies lessons I only had to specify once. The ROI gets better every single week.

## What I Wish Someone Had Told Me Before Starting

Alright, here's the honest section. The part where I share what went wrong and what I'd do differently.

**Mistake #1: Too many sources per notebook.**

My first instinct was to dump everything remotely related into a single notebook. Fifty sources in one container. The result was unfocused responses that drew from irrelevant material. Notebook LM is better with fifteen to twenty curated sources per notebook than fifty loosely related ones. Quality of sources beats quantity every time.

**Mistake #2: Skipping the Skills step.**

For the first three days, I ran every query with manual instructions. "Query notebook X, format the response as bullet points, include source citations, focus on actionable insights." Typing that out every time was tedious and inconsistent. I should have built Skills on day one. If you're setting this up, create at least one basic Skill before you start querying.

**Mistake #3: Expecting browser-level reliability from MCP.**

The MCP connection is solid, but it's not bulletproof. Auth tokens expire. Occasional timeouts happen during heavy use. I've had two sessions where the connection dropped mid-workflow and I lost the thread of a multi-agent query. Save your work frequently and don't build workflows that assume the connection never drops.

**The trade-off nobody mentions:** This system works best when you invest time curating your notebooks. Garbage in, garbage out applies here more than anywhere. If you throw random, low-quality sources into a notebook and expect brilliant synthesis, you'll be disappointed. The magic happens when you're deliberate about what goes into each notebook — when every source is something you'd actually want your research assistant to read.

**A prediction I'll stand behind:** Within eighteen months, every major AI platform will have some version of this pattern — external knowledge bases connected to action-capable agents through standardized protocols. The tools might change. The names will definitely change. But the architecture — separate your knowledge storage from your execution engine, connect them through MCP, and use Skills to maintain consistency — that pattern is here to stay.

## Real Numbers From Real Projects

Here's what this system actually produced across three client projects over two weeks:

**Project 1: Market Research for SaaS Client**
- Sources ingested: 87 documents across 6 notebooks
- Research phase time: 4 hours (estimated 15+ hours manually)
- Deliverables: Interactive strategy dashboard, 3 competitive analysis reports, audio briefing
- Client feedback: "This is the most thorough research package we've received."

**Project 2: Technical Architecture Review**
- Sources ingested: 1 large PDF (200 pages) + 12 technical articles
- Query sessions: 15 focused queries over 3 days
- Time saved on document review: approximately 6 hours
- Found 2 specification conflicts the client's team had missed

**Project 3: Content Strategy Development**
- Sources ingested: 40 articles, 8 YouTube videos, 5 competitor analyses
- Generated: Content calendar with research-backed topic recommendations
- Built: Interactive content performance prediction dashboard
- Setup to deliverable: 3 hours total

The token cost savings deserve a mention too. By storing research context in Notebook LM instead of passing it through Anti-Gravity's context window with every query, I estimate I'm saving 40-60% on token costs for research-heavy workflows. That's not pocket change when you're running multi-agent queries across multiple projects daily.

Are these results reproducible on day one? Honestly, no. The first project took longer because I was still building Skills and learning the query patterns that produce good results. By project three, I had the muscle memory and the Skills library to move fast. Budget a learning curve of three to five days before you're operating at full speed.

## The Bigger Picture Most People Are Missing

Here's what sticks with me after two weeks of building on this stack. The combination of Notebook LM and Anti-Gravity isn't just a productivity hack. It's a preview of how AI-augmented knowledge work is going to function.

The pattern — curate knowledge into specialized containers, connect those containers to action-capable agents, use Skills to maintain consistency, and let the system build things from what it knows — that pattern scales in a way that "chat with a smart AI" fundamentally doesn't.

One notebook with twenty sources becomes a domain expert you can query forever. Ten notebooks become a research department. Connect them to a builder that can create applications, presentations, and reports from that research, and you've got something that feels less like a tool and more like a team.

I started this experiment trying to solve a simple problem: too many browser tabs, too little context in my head. What I ended up with was a system that changed how I approach every new project. The first thing I do now isn't open a Google Doc and start writing notes. The first thing I do is create notebooks, curate sources, build a Skill, and let the system give me a foundation I can build on.

If you take one thing from this post, make it this: stop treating AI tools as isolated assistants and start treating them as components in a system. Notebook LM is the memory. Anti-Gravity is the hands. MCP is the nervous system connecting them. Skills are the habits that make the whole thing efficient.

Build the system once. Use it on every project. Watch what happens to your output quality when your AI actually knows what you know.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
