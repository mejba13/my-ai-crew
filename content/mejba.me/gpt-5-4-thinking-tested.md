**BRAND:** mejba.me
**TITLE:** I Tested GPT 5.4 Thinking — Here's What Actually Changed
**SLUG:** gpt-5-4-thinking-tested
**TAGS:** AI Tools, GPT 5.4, AI Development, Model Comparison, Hands-On Review

---

I had GPT 5.4 Thinking build me a 15-slide presentation, a fully functional Excel spreadsheet with live formulas, and a research report with citations — all within the same session, all in under ten minutes. Then I asked it to write a YouTube hook in my voice, and it sounded like a corporate press release wearing a casual hat.

That contradiction — stunningly capable in some areas, frustratingly tone-deaf in others — is the most honest summary I can give you of [OpenAI's latest flagship model](https://openai.com/index/introducing-gpt-4-5/). GPT 5.4 Thinking isn't a small incremental update. It's a genuine leap in certain dimensions and a sideways shuffle in others. After two days of intensive testing across coding, research, document creation, and content generation, I have a clear picture of where this model shines, where it stumbles, and — most importantly — where it fits in a landscape where Anthropic's Opus 4.6 and Google's Gemini 3.1 Pro are both competing for the same crown.

Before I get into benchmarks, you need to understand the model lineup because OpenAI didn't just release one thing — they released a family.

## Three Models, Three Jobs, One Confusing Naming Scheme

OpenAI's GPT 5.x generation now has three distinct variants, and the differences between them matter more than you'd think from the names alone.

**GPT 5.4 Thinking** is the headline model — the one designed for deep reasoning and complex tasks. When you ask it something hard, it doesn't just generate an answer. It enters a visible "thinking" phase where the model processes, deliberates, and works through the problem before responding. Think of it as the model that pauses to consider rather than blurting out the first plausible answer. This thinking process is why it excels at research synthesis, multi-step analysis, and tasks where getting the reasoning chain right matters more than raw speed. The thinking tokens aren't billed the same way as output tokens, which keeps costs reasonable despite the additional processing.

**GPT 5.4 Pro** is the research-grade tier — same underlying architecture but with extended thinking time, higher context limits, and access to more compute per query. OpenAI positions this for professional and enterprise users who need the absolute ceiling of capability and are willing to pay significantly more per query to get it. I haven't had enough time with Pro to give a definitive assessment, but early testing suggests the quality difference over standard 5.4 Thinking is most noticeable on very long, very complex tasks — multi-document analysis, comprehensive code reviews across large repositories, that sort of thing. For everyday knowledge work, standard 5.4 Thinking is more than sufficient.

**GPT 5.3 Instant** takes the opposite approach. Speed is the entire point. It sacrifices depth for responsiveness, delivering answers in fractions of a second rather than the 5-15 seconds that Thinking mode sometimes requires. The quality is noticeably lower on complex tasks — you can feel the model cutting corners on reasoning — but for quick lookups, brainstorming, chat-style interactions, and tasks where "good enough in 0.3 seconds" beats "excellent in 12 seconds," Instant is genuinely useful. I've started using it as my default for rapid-fire questions during development sessions where I need a fast sanity check, not a deep analysis.

The three-tier approach makes strategic sense. OpenAI is acknowledging what power users already know: different tasks need different speed-quality tradeoffs. But the naming is a mess. "GPT 5.4 Thinking" versus "GPT 5.3 Instant" implies Instant is a generation behind, when it's actually a contemporaneous model optimized for a different use case. I suspect OpenAI will clean up the naming eventually, but for now, just remember: Thinking = deep and thorough, Pro = maximum capability, Instant = fast and lightweight.

With the model family mapped out, here's the thing about GPT 5.4's architecture that changes the game in a way most reviewers aren't talking about yet.

## Native Computer Use Changes the Entire Value Proposition

Every previous GPT model was essentially a text-in, text-out machine. Sure, you could connect it to plugins, wire it up to browsing tools, build agent workflows around it. But the model itself lived inside a chat window. It could *tell* you what to do on your computer. It couldn't *do* it.

GPT 5.4 crosses that line.

Native computer use means the model can perform web actions directly — data entry, managing emails, interacting with calendar apps, filling out forms. Not through a hacky browser automation layer that breaks every time a website changes its CSS. Natively. As a built-in capability that OpenAI has integrated into the model's core functionality.

I've been watching this capability evolve across the AI landscape. Anthropic introduced computer use with Claude back in 2024, and Google has been experimenting with similar features through Project Mariner and Gemini's agent capabilities. But GPT 5.4's implementation feels different because of how seamlessly it integrates with the existing ChatGPT ecosystem. You don't need to set up a separate agent or configure a browser sandbox. You just... ask it to do something on the web, and it does it.

The implications for knowledge workers are massive, and I'll walk through some specific examples later. But first — the part everyone actually wants to know about.

## The Benchmarks Tell Half the Story

OpenAI's marketing materials position GPT 5.4 Thinking as state-of-the-art, claiming a slight edge over both Opus 4.6 and Gemini 3.1 Pro in certain benchmarks. After running my own tests, here's my honest assessment: they're right about "slight," and they're being generous with "edge."

On knowledge work tasks — creating structured documents, synthesizing research, building formatted outputs — GPT 5.4 is genuinely impressive. It handles complex spreadsheet logic that would have tripped up GPT 5.2 entirely. The formulas are correct, the formatting is clean, and the charts actually make sense visually. This isn't the "almost right" output we've been tolerating from AI document generation for the past year. It's production-ready.

On coding tasks, the picture gets more interesting. OpenAI claims GPT 5.4's coding abilities now match their specialized GPT 5.3 Codex model — the variant specifically fine-tuned for code generation that developers have been using through the API. My testing partially confirms this — simple to moderately complex coding tasks are handled well, with improved accuracy over GPT 5.2, and the fact that a general-purpose model now matches a code-specialized one is genuinely impressive. But "matching Codex" and "being the best coding model available" aren't the same claim. I built a small web app with rounded cards and a light/dark mode toggle. GPT 5.4 delivered a working implementation, but some links were non-functional and the filtering features I requested didn't actually filter anything. Workable? Yes. Impressive? Somewhat. Better than what I get from Opus 4.6 in Claude Code? Honestly, no — and I say that as someone who uses both ecosystems daily.

Here's the comparison table from my testing:

| Capability | GPT 5.4 Thinking | Opus 4.6 | Gemini 3.1 Pro |
|-----------|------------------|----------|----------------|
| Research synthesis | Excellent — fast, well-structured, cited | Very good | Very good |
| Spreadsheet/document creation | Best in class | Good (via artifacts) | Good |
| Coding (simple-medium) | Strong improvement over 5.2 | Strongest overall | Competitive |
| Coding (complex/interactive) | Still has gaps | Most reliable | Hit or miss |
| Writing (natural tone) | Weakest of the three | Strong | Strong |
| Native computer use | Built-in, seamless | Available but sandboxed | Limited availability |
| Token efficiency | Improved over 5.2 | Efficient | Very efficient |
| Hallucination rate | 33% reduction claimed | Low | Low |

The 33% hallucination reduction over GPT 5.2 is worth calling out specifically because it addresses one of the most persistent criticisms of the GPT line. I ran several factual recall tests — technical specifications, historical dates, API documentation details — and GPT 5.4 was noticeably more careful about qualifying uncertain answers. It said "I'm not confident about this specific version number" in situations where GPT 5.2 would have confidently hallucinated a plausible-sounding answer.

That said, "33% fewer hallucinations" still means hallucinations happen. Trust but verify remains the only sane approach. But the trend line is encouraging.

What the benchmarks completely miss, though, is the *feel* of working with these different models — and that's where my strongest opinions live.

## The Research Workflow That Actually Impressed Me

Most AI model reviews test research capabilities by asking a single question and evaluating the answer. That's a terrible test. Nobody uses AI research that way in practice. Real research is iterative — you start with a broad question, get results, narrow your focus, adjust your angle, dig deeper into one specific thread.

GPT 5.4 Thinking handles this iterative flow better than any model I've tested.

I started with a broad query: "Analyze the current state of AI-powered marketing automation tools, focusing on market leaders, pricing models, and integration capabilities." The model started its thinking process, searched the web, and returned a structured analysis in about 45 seconds. Clean sections, specific product names with current pricing, integration ecosystem comparisons. Good but not remarkable — Gemini and Claude can do similar work.

Here's where it got interesting. I said: "Actually, narrow this to tools that specifically integrate with Shopify for e-commerce email marketing, and add a comparison of their AI personalization capabilities."

With GPT 5.2, this kind of mid-stream pivot would have required essentially starting over. The model would treat it as a new question, losing the context from the first research pass. GPT 5.4 adjusted its search parameters, kept the relevant findings from the first query, and built on them. The refined output cross-referenced the original market overview with Shopify-specific integration data, producing a comparison that felt like it came from someone who'd actually done a deep dive rather than two separate shallow searches.

The output was structured into findings with citations, a competitive comparison matrix, and — this was a nice touch — a checklist of evaluation criteria for making a final selection. The kind of deliverable that would have taken me 2-3 hours of manual research to produce.

I pushed it one step further and asked it to convert the research into a presentation. Fifteen slides, properly structured, with a logical narrative flow from market overview to specific recommendations. The default design was corporate-bland (as expected), but when I asked for a minimalistic, modern redesign, the second version was genuinely usable. Not award-winning — but absolutely good enough for an internal strategy meeting.

Then I asked it to build an Excel spreadsheet summarizing the key data points with comparison formulas and a chart. It delivered a downloadable .xlsx file with working VLOOKUP formulas, conditional formatting, and a bar chart comparing pricing across providers. I opened it in Excel and everything worked. No broken references, no formula errors, no phantom data.

This is the workflow where GPT 5.4 absolutely earns its place. Research → Presentation → Spreadsheet, all in one conversation, each building on the previous output. For knowledge workers who spend their days assembling information into documents, this is a genuine productivity multiplier.

But there's a significant caveat I need to address before anyone gets too excited about those spreadsheet capabilities.

## The Excel Add-On Is Impressive But Not What You Think

OpenAI released the ChatGPT for Excel add-on alongside GPT 5.4, available for paid subscribers. On paper, it sounds like the killer feature for business users — seamless AI integration directly inside your spreadsheets.

In practice, it's useful but narrower than the marketing suggests. The add-on lets you use GPT functions within Excel cells, which is great for tasks like categorizing data, extracting information from text columns, or generating formulas based on natural language descriptions. What it doesn't do is turn Excel into a fully AI-powered analysis platform. You're still working within Excel's paradigm; the AI just helps with specific cell-level tasks.

Where I found genuine value was in formula generation. Describing what I wanted in plain English — "calculate the year-over-year growth rate comparing column C to column D, but only for rows where column A contains 'Enterprise'" — and getting a working formula back instantly. That saves real time, especially for complex nested formulas that would otherwise require twenty minutes of documentation browsing.

Where I didn't find much value was in the broader "AI in Excel" use cases. For serious data analysis, I'd still rather export the data and work with it in Claude Code or a Python notebook. The cell-by-cell AI approach feels like using a race car to drive to the mailbox — technically works, architecturally wrong for the task.

The real story of GPT 5.4 isn't any single feature. It's the pattern of what OpenAI is optimizing for — and what they're not.

## Where GPT 5.4 Falls Short (And Why It Matters)

I asked GPT 5.4 Thinking to write five YouTube video hooks in a conversational, direct tone. "No corporate language. No m-dashes. Write like you're talking to a friend who asked you a question."

The first output used four m-dashes in five hooks.

I clarified: "Zero m-dashes. None. Not one."

The second output used two m-dashes and added "furthermore" to one of the hooks.

This is not a small complaint. Writing style adherence is one of the most fundamental capabilities content creators need from an AI model, and GPT 5.4 is measurably worse at it than both Claude and Gemini. I've spent enough time with all three to say this confidently: if your primary use case is generating content that matches a specific voice or style, GPT 5.4 should not be your first choice.

The issue isn't that the model can't generate good text. Individual sentences are well-constructed. The vocabulary is sophisticated. The ideas are relevant. But GPT 5.4 has a persistent tendency to default to a formal, slightly academic register that it struggles to override even with explicit instructions. It's like working with a brilliant consultant who went to business school and can't stop saying "synergize" no matter how many times you ask them to talk normally.

Claude — particularly in the current Opus 4.6 iteration — handles style adherence dramatically better. When I tell Claude "write in a conversational first-person tone," the output actually sounds conversational. When I tell it "no transition words like furthermore or however," those words disappear. The instruction-following gap between GPT 5.4 and Claude on stylistic constraints is wide enough that I wouldn't consider switching my content generation workflows.

Gemini 3.1 Pro sits in the middle. Better than GPT 5.4 at matching conversational tones, not quite as flexible as Claude for nuanced style instructions, but generally reliable for straightforward content tasks.

This matters because it reveals what OpenAI is optimizing for with the GPT 5.4 line — and what they're deprioritizing. The model is clearly engineered for knowledge work: research, analysis, document creation, structured outputs. These are enterprise use cases with enterprise revenue potential. Content generation in a specific brand voice is a creator economy use case with less obvious enterprise value. The optimization choices make business sense even if they frustrate people like me who want one model that does everything.

Which brings me to the question I keep asking myself after every new model launch.

## The Multi-Model Reality Nobody Wants to Accept

Here's a take that might sound obvious but that almost nobody actually acts on: there is no single best AI model. Not GPT 5.4. Not Opus 4.6. Not Gemini 3.1 Pro. The right model depends entirely on what you're doing with it.

I know that's unsatisfying. We want a winner. We want to say "use this one" and be done with it. But after testing all three extensively — and I mean actual project work, not benchmark puzzles — the honest answer is that I use different models for different tasks, and you probably should too.

**My current model allocation looks like this:**

- **Coding and software development:** Opus 4.6 in Claude Code. Not close. The agentic workflow, the file system access, the ability to iterate on a codebase rather than generating isolated snippets — nothing else matches this experience right now.

- **Research and document creation:** GPT 5.4 Thinking. The research-to-presentation-to-spreadsheet pipeline is unmatched. If I need to produce a strategy document, a market analysis, or a formatted report, this is where I start.

- **Content generation and writing:** Claude (Opus or Sonnet, depending on complexity). Best style adherence, best instruction following for creative and brand-voice work, most natural conversational output.

- **Quick questions and brainstorming:** Gemini 3.1 Pro or GPT 5.3 Instant. Speed matters more than depth for rapid ideation, and both are fast enough to feel like a real-time conversation. Instant's sub-second responses make it feel like autocomplete on steroids — perfect for "what's the syntax for X" or "give me five names for Y" type queries.

- **Computer use and web automation:** GPT 5.4 for now, though this landscape is changing fast as Claude and Gemini expand their agent capabilities.

This multi-model approach adds complexity. You need accounts with multiple providers. You need to develop intuition for which model fits which task. You need to context-switch between different interfaces and interaction paradigms. It's messier than having one tool for everything.

But it's also dramatically more effective. Using GPT 5.4 for a task where Claude excels (or vice versa) means you're getting 70% of possible quality when you could be getting 95%. Over dozens of tasks per week, that quality gap compounds into a significant productivity difference.

The people who will get the most value from GPT 5.4 aren't the ones who switch to it exclusively. They're the ones who add it to their toolkit for the specific use cases where it outperforms everything else — and keep using other models where those models are stronger.

## Token Economics: The Hidden Story in GPT 5.4's Pricing

OpenAI made an interesting pricing decision with GPT 5.4. The per-token cost is slightly higher than GPT 5.2, but the model uses fewer tokens to accomplish the same tasks. This means the actual cost per task is lower in most cases, even though the sticker price went up.

I tracked token usage across ten comparable tasks between GPT 5.2 and GPT 5.4. On average, GPT 5.4 used 22% fewer tokens for equivalent outputs. Factor in the pricing change, and the net cost was about 15% lower per task. Not a dramatic savings, but meaningful at scale — especially for teams running hundreds of API calls daily.

The token efficiency improvement also means faster responses. Fewer tokens generated means less time waiting, which compounds when you're running iterative workflows where each step depends on the previous output. My research-to-presentation pipeline completed about 30% faster with GPT 5.4 compared to 5.2, which translates to real time savings across a workday.

For API users building products on top of GPT, this efficiency gain is probably the most practically significant improvement in the entire release. It's not the kind of thing that makes headlines, but it's the kind of thing that shows up in your monthly OpenAI bill.

## What This Means for the Next Six Months

I've been testing new AI models every few weeks for over a year now, and a clear pattern has emerged. Each new release from any major provider narrows the gap with competitors in their weak areas while pushing further ahead in their strong areas. GPT 5.4 follows this pattern exactly — it closed ground on Claude's coding capabilities (though didn't surpass them), pushed further ahead on knowledge work and document creation, and made incremental improvements on hallucination rates.

The competitive dynamic this creates is genuinely good for users. OpenAI improving coding pushes Anthropic to improve their research capabilities. Google improving both pushes everyone to optimize token efficiency. Nobody can rest on a single advantage because the other providers will close that gap within one or two release cycles.

What I'm watching for in the next six months:

**From OpenAI:** A GPT 5.5 or GPT 6 that finally cracks writing style adherence. This is the most obvious gap in their lineup, and they know it. The enterprise customers they're courting need brand-voice consistency as much as they need research capabilities.

**From Anthropic:** Expanded computer use and a more robust document creation pipeline. Claude's coding dominance is secure for now, but the knowledge work gap with GPT 5.4 is real.

**From Google:** Gemini's deep think capabilities applied to longer, more complex tasks. Google has the data advantage (Search, YouTube, Scholar) that neither competitor can match; the question is whether they can translate data access into model capability.

The model I'm most excited about isn't any specific release — it's the workflow where I can route tasks to the best available model automatically, without manual switching. We're not there yet, but we're getting closer with every release.

## Stop Waiting for the Perfect Model

I opened this piece by describing GPT 5.4 building me a presentation, a spreadsheet, and a research report in under ten minutes — then failing to write a simple hook without m-dashes. That contradiction hasn't resolved. It won't resolve in this model generation, and probably not in the next one either.

The perfect all-in-one AI model is a fantasy that's preventing people from getting real value from the imperfect models that exist right now. GPT 5.4 Thinking is the best knowledge work and research model available today. It's not the best coding model. It's not the best writing model. It's not the best anything-else model. And that's fine.

If you're a knowledge worker drowning in research, reports, and presentations, GPT 5.4 just saved you ten hours a week. If you're a developer looking for a better coding assistant, Opus 4.6 is still your answer. If you're a content creator who needs AI that actually sounds like you, Claude wins that race by a comfortable margin.

The people gaining real competitive advantage from AI right now aren't the ones debating which model is "best." They're the ones who figured out which model is best *for each specific thing they do* — and built workflows that route accordingly.

GPT 5.4 Thinking earned a permanent spot in my toolkit today. Not as a replacement for anything. As an addition. And honestly? That's the highest compliment I can give any AI model in 2026.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)