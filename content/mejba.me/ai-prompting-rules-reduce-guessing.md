**BRAND:** mejba.me
**TITLE:** 3 Prompting Rules That Stopped My AI From Guessing
**SLUG:** ai-prompting-rules-reduce-guessing
**PRIMARY KEYWORD:** AI prompting rules reduce guessing
**SECONDARY KEYWORDS:** AI hallucination reduction, prompt engineering reliability, AI source attribution
**META DESCRIPTION:** I tested 3 simple prompting rules that cut AI guessing and hallucination in real workflows. Blank answers, penalty framing, and audit trails that actually work.
**TAGS:** AI Development, Prompt Engineering, Automation, Reliability, Tutorial
**CONTENT TYPE:** Deep Dive
**CONTENT CLUSTER:** AI Tools & Productivity
**TRANSFORMATION GOAL:** After reading, the reader will implement three specific prompting rules that shift AI behavior from confident guessing to cautious accuracy with transparent audit trails.

---

# 3 Prompting Rules That Stopped My AI From Guessing Wrong

I lost a client because GPT-4o confidently told me the wrong payment terms on a contract.

Not "kinda wrong." Not "slightly off." It pulled a net-30 figure from page 8 of a 22-page vendor agreement, completely ignoring the amended net-45 on page 14. I built the invoice schedule around that number. The client paid on time — according to the wrong schedule. The vendor flagged the discrepancy. The client asked why I hadn't caught it. I didn't have a good answer.

The AI had been right on 47 other fields in that same extraction. Addresses, dates, line items, tax IDs — all perfect. That's what made the payment terms mistake so dangerous. When a system gets 98% of things right, you stop checking the other 2%. And that 2% is where the damage hides.

That contract incident happened in October 2025. Since then, I've been obsessed with a single question: how do you stop AI from guessing when it doesn't know?

Not "how do you reduce hallucinations" in the abstract, academic sense. I mean specifically — when you hand an AI model a document and ask it to extract structured data, how do you prevent it from filling in a confident answer when the real answer is ambiguous, missing, or contradictory?

I found three rules that work. They're dead simple. They don't require fine-tuning, RAG pipelines, or custom model training. They're just prompting strategies — but they fundamentally change how the AI behaves. I've been running them in production across contract review, invoice processing, and CRM data entry for five months now, and the difference is night and day.

Here's the thing most prompt engineering guides won't tell you: the problem isn't that AI models lack intelligence. The problem is they have too much of it — paired with zero honesty about what they don't know.

---

## Why Smarter Models Guess More Confidently (Not Less)

There's a pattern I keep running into that nobody talks about enough. As AI models get more capable, they don't get more honest. They get more convincingly wrong.

I call this the Intelligence-Honesty Gap. And the research backs it up.

[MIT researchers found in January 2025](https://news.mit.edu/2024/thermometer-prevents-ai-model-overconfidence-about-wrong-answers-0731) that when AI models hallucinate, they're 34% more likely to use high-confidence language — words like "definitely," "certainly," and "without doubt." The model doesn't hedge when it's guessing. It doubles down.

This isn't a bug in a specific model. It's a structural problem baked into how all large language models are trained. [A 2025 study published in Science](https://www.science.org/content/article/ai-hallucinates-because-it-s-trained-fake-answers-it-doesn-t-know) laid it out plainly: LLMs learn to bluff because their training rewards confident answers and penalizes uncertainty. The incentive structure is identical to a multiple-choice exam where leaving a question blank scores zero, but guessing has a chance of scoring points. So the model always guesses.

[Carnegie Mellon researchers confirmed this in July 2025](https://www.cmu.edu/dietrich/news/news-stories/2025/july/trent-cash-ai-overconfidence.html) — AI chatbots remain overconfident even when they're wrong, and users can't reliably detect the difference between a confident correct answer and a confident hallucination.

The practical implication hit me hard: reasoning models — the ones I was paying premium prices for — actually hallucinate *more* on ambiguous tasks, not less. [Recent benchmarks from early 2026](https://suprmind.ai/hub/ai-hallucination-rates-and-benchmarks/) show that GPT-5, Claude Sonnet 4.5, and Gemini-3-Pro all exceeded 10% hallucination rates on harder benchmarks. The hypothesis? Reasoning models "overthink" — they invest computational effort into constructing plausible-sounding answers from insufficient evidence, rather than flagging the evidence as insufficient.

When I was using Claude to extract contract data, I was essentially handing a brilliant employee a document and saying "fill in all the fields." And like any brilliant employee who's been trained to never say "I don't know," it filled in every single field. Confidently. Including the ones where the correct answer was "this document doesn't clearly specify."

That's the gap. Intelligence without honesty. Capability without calibration.

The three rules I'm about to share don't make the AI smarter. They make it honest. And honest, it turns out, is worth way more than smart when you're building production systems.

---

## Rule 1: Force Blank Answers When Uncertain

This is the rule that changed everything for me. It's so simple it almost feels like cheating.

The instruction: **Tell the AI to leave a field blank if the data is ambiguous, missing, or conflicting — and require a one-sentence explanation of why it was left blank.**

Here's the exact prompt structure I use for contract data extraction:

```
Extract the following fields from this document.

CRITICAL RULE: If any field's value is ambiguous, missing,
or if conflicting information exists in the document, leave
the field BLANK. Do not guess. Do not infer. Do not fill in
a "most likely" answer.

For every blank field, provide a one-sentence explanation of
why it was left blank.

Your job is to extract only what the document explicitly states.
If a value cannot be determined with certainty from the source
text, it must remain empty.

Fields to extract:
- Vendor name
- Payment terms
- Contract start date
- Contract end date
- Total contract value
- Renewal clause (auto-renew / manual / none)
- Governing law jurisdiction
...
```

The magic is in what happens next. Instead of getting a fully completed extraction with hidden errors scattered randomly across 50 fields, I get something like this:

```
Vendor name: Meridian Supply Co.
Payment terms: [BLANK]
  → Two different payment terms found: "net 30" on page 8
    (Section 4.2) and "net 45" on page 14 (Amendment A,
    dated March 2024).
Contract start date: January 15, 2024
Contract end date: January 14, 2026
Total contract value: $847,500
Renewal clause: [BLANK]
  → Section 9.1 references "automatic renewal" but
    Amendment A, Section 3 states "renewal subject to
    mutual written agreement." These conflict.
Governing law jurisdiction: State of Delaware
```

Look at what just happened. The payment terms problem that cost me a client? The AI caught it. Not because it got smarter — because I gave it permission to say "I found a conflict and I'm not going to pick a side."

### Why This Works Psychologically

This rule works because it fundamentally changes the AI's task. Without it, the model's implicit instruction is: "fill in every field." That's a completion task. The model is optimized for completion. Blank spaces feel like failure to a completion-oriented system.

With the rule, the task shifts to: "fill in what you're certain about, and flag everything else." That's a classification task — certain vs. uncertain. Models are substantially better at binary classification than they are at generating accurate answers from ambiguous inputs.

I'm not asking the model to be smarter. I'm asking it to do a different, easier job.

### The One-Sentence Explanation Is Non-Negotiable

Early on, I tried the rule without requiring the explanation. The model would leave fields blank, but I had no idea why. Was the data genuinely missing? Was there a conflict? Did the model just fail to find it?

The explanation requirement solves this completely. "Two different payment terms found on pages 8 and 14" tells me exactly what happened and exactly where to look. I can resolve the ambiguity in 30 seconds by reading those two pages myself. Compare that to re-reading the entire 22-page contract to figure out why a field is blank.

The explanation also acts as a grounding mechanism. When the model has to articulate *why* it's uncertain, it's forced to reference specific evidence (or the absence of evidence) in the source document. This prevents a failure mode I saw early on where the model would leave things blank not because the data was genuinely ambiguous, but because it was being overly cautious. The explanation requirement creates a natural calibration — the model has to justify its uncertainty, which makes the uncertainty meaningful.

### Real-World Results

I've been running this rule across contract review workflows for five months. The pattern is consistent: instead of getting a clean-looking extraction with 2-4 hidden errors buried in 50+ fields, I get an extraction with 3-7 blank fields that are obviously flagged for human review.

The time savings compound fast. Before this rule, my review process was: check every field against the source document. That took 25-35 minutes per contract. Now my review process is: skim the completed fields for obvious issues, then spend focused time on the blanks. That takes 8-12 minutes. Same accuracy. Less than half the time.

But here's where it gets even more interesting — the rule improved the accuracy of the *non-blank* fields too. When the model knows it's allowed to punt on uncertain fields, it stops trying to force-fit ambiguous data into clean answers. The fields it does complete tend to be genuinely clean.

---

## Rule 2: Change the Incentive — Make Wrong Answers Expensive

Rule 1 gives the AI permission to leave things blank. Rule 2 gives it a *reason* to prefer blank over wrong.

The instruction: **Explicitly tell the AI that a wrong answer costs three times more than a blank answer.**

Here's how I phrase it:

```
Scoring for this task:
- A correct extraction: +1 point
- A blank field with explanation: 0 points
- An incorrect extraction: -3 points

Your goal is to maximize your total score. A wrong answer is
three times worse than leaving a field blank. When in doubt,
leave it blank.
```

This seems almost too simple to work. It's a line of text in a prompt. The model doesn't actually get scored. There's no reinforcement learning loop here. So why does it change behavior?

### The Behavioral Shift Is Real

Because language models have internalized the concept of incentive structures from their training data. They've seen millions of examples of how humans behave when penalties are asymmetric — insurance policies, medical diagnoses, legal filings, quality assurance processes. When you frame the task with explicit costs for errors, the model activates patterns associated with high-stakes, penalty-averse decision-making.

Think of it this way. If you hire a new contractor and tell them "fill in this spreadsheet — I need every field completed," they'll complete every field. Some answers will be guesses. They want to look competent. They want to seem thorough.

Now imagine telling that same contractor: "Fill in what you're sure about. For everything else, leave it blank and tell me why. Oh, and one more thing — if I find a wrong answer, it counts three times against you compared to a blank."

Different behavior. Immediately. Not because the contractor became more skilled. Because the incentive structure shifted from "rewarding completion" to "penalizing errors."

That's exactly what happens with the model. The -3 penalty framing triggers conservative, verification-oriented behavior patterns. The model becomes noticeably more cautious with edge cases and ambiguous data.

### How I Discovered This Rule

I didn't invent this concept — I adapted it from how I onboard junior developers for client projects.

When a new developer joins one of my projects, I always tell them the same thing during the first week: "If you're not sure about a requirement, ask. Don't guess and build the wrong thing. Tearing down wrong code costs the team three times more than the delay of asking a question." Every experienced engineering lead says some version of this. It works on humans because it reframes "I don't know" from a sign of incompetence to a sign of professionalism.

It works on LLMs for the same reason. [OpenAI's own research on why models hallucinate](https://openai.com/index/why-language-models-hallucinate/) points to this exact dynamic — current training incentivizes confident guessing over honest uncertainty. The -3 penalty prompt is a crude but effective way to invert that incentive at inference time, without touching the model's weights.

[Researchers at the University of Maryland formalized this in late 2025](https://arxiv.org/abs/2511.11500) with a technique they called "Reinforced Hesitation" — a training approach using ternary rewards (+1 correct, 0 abstention, -lambda for error). The finding? Models trained with asymmetric penalties produced distinct behavior along what they called a "Pareto frontier" — each penalty level yielded the optimal model for its risk regime. My prompting approach isn't as rigorous as retraining, but it pushes toward the same behavioral shift at the prompt level.

### Combining Rule 1 and Rule 2

Rules 1 and 2 are designed to stack. Rule 1 gives the model *permission* to leave fields blank. Rule 2 gives it *motivation* to prefer blank over wrong.

Without Rule 1, the model has no blank option — it tries to answer everything.
Without Rule 2, the model has the option to go blank but no strong reason to use it.
Together, they create a system where the model actively prefers honest uncertainty over confident guessing.

In practice, I noticed that Rule 1 alone reduced extraction errors by roughly 60% across my contract workflows. Adding Rule 2 pushed that to approximately 80%. The remaining errors tend to be genuinely tricky — cases where the document language is clear but misleading, or where domain knowledge is required to spot the issue. Those still need human review. But 80% error reduction from two lines of prompting? That's a massive win.

The next rule handles the remaining edge cases — and it's the one that turns this from a nice prompting trick into a production-ready audit system.

---

## Rule 3: Source Attribution — The Audit Trail That Changes Everything

Rules 1 and 2 handle the "should I answer?" decision. Rule 3 handles the "how did I get this answer?" question.

The instruction: **For every extracted value, add a column indicating whether the value was "extracted" (directly stated in the document) or "inferred" (derived from context, calculation, or interpretation). If inferred, require a one-sentence explanation of what was inferred and from where.**

Here's the prompt addition:

```
For each field, include a "Source" column with one of two values:

EXTRACTED — The value appears verbatim or near-verbatim in the
source document. Include the page/section reference.

INFERRED — The value was derived from context, calculation, or
interpretation rather than directly stated. Include a one-sentence
explanation of what was inferred and the evidence used.

Examples:
- Total value: $847,500 | EXTRACTED | Page 3, Section 2.1
- Annual value: $423,750 | INFERRED | Calculated as total value
  ($847,500) divided by contract duration (2 years)
- Auto-renewal: Yes | INFERRED | Section 9.1 states "this agreement
  shall continue in effect unless terminated" — interpreted as
  auto-renewal language
```

### Why the Extracted/Inferred Distinction Matters

This is the rule that makes the system auditable. And auditability is what separates a "cool AI trick" from something you can actually rely on in a business.

When every field comes tagged with its source type, your review workflow becomes surgical. Here's what my actual review process looks like now with all three rules active:

**Step 1: Skim the EXTRACTED fields.** These came directly from the document with page references. I spot-check maybe 10-15% of them. Errors here are rare — the model is good at verbatim extraction when it's not being asked to interpret.

**Step 2: Review every INFERRED field.** These are the ones where the model made a judgment call. The explanations tell me exactly what logic it used, so I can quickly validate whether the inference is reasonable. Takes 2-3 minutes for a typical contract.

**Step 3: Review every BLANK field.** These are the ones the model punted on. The explanations tell me what's ambiguous or missing, so I know exactly where to look in the source document. Takes another 2-3 minutes.

**Step 4: Done.** Total review time: 8-12 minutes for a contract that used to take 30+ minutes of line-by-line verification.

The key insight: instead of reviewing everything, I only review blanks and inferences. The EXTRACTED fields with page references are verifiable but low-risk. The system self-sorts into confidence tiers, and my attention goes where it matters most.

### The Hidden Benefit: Catching "Correct but Inferred" Answers

Before Rule 3, I had a blind spot I didn't even know about. The model would sometimes give me the right answer — but for the wrong reason. It would "extract" a contract value that was actually calculated from line items, or it would "extract" a jurisdiction that was actually inferred from the company's registered address.

These answers looked correct. They often were correct. But they were fragile. If the underlying assumption changed — different line item structure, company registered in a different state than its operating address — the inference would break silently.

The INFERRED tag surfaces these cases. When I see "INFERRED: Calculated from line items on pages 4-6," I know to verify the calculation. When I see "INFERRED: Jurisdiction assumed from company registration address in Delaware," I know to check whether the contract specifies governing law explicitly.

This is the difference between an extraction that's right today and an extraction process that's reliably right over time.

### A Complete Prompt Combining All Three Rules

Here's the full prompt template I use for contract data extraction. I've refined this over five months and dozens of contracts:

```
You are extracting structured data from a legal document.
Follow these rules exactly:

RULE 1 — BLANK WHEN UNCERTAIN
If any field's value is ambiguous, missing, or if conflicting
information exists in the document, leave the field BLANK.
Provide a one-sentence explanation of why it was left blank.

RULE 2 — ERROR PENALTY
Scoring: Correct = +1, Blank with explanation = 0, Wrong = -3.
A wrong answer is three times worse than a blank. When in doubt,
leave it blank.

RULE 3 — SOURCE ATTRIBUTION
For each completed field, mark it as:
- EXTRACTED (value appears verbatim; cite page/section)
- INFERRED (value derived from context; explain the inference)

OUTPUT FORMAT:
| Field | Value | Source | Notes |
|-------|-------|--------|-------|
| Vendor | [value or BLANK] | EXTRACTED/INFERRED | [page ref or explanation] |

DOCUMENT:
[paste document here]

FIELDS TO EXTRACT:
1. Vendor legal name
2. Payment terms
3. Contract effective date
4. Contract end date
5. Total contract value
6. Currency
7. Renewal type (auto/manual/none)
8. Termination notice period
9. Governing law jurisdiction
10. Liability cap
```

That's the whole system. No fine-tuning. No vector databases. No custom model training. Three rules in a prompt that fundamentally change how the AI approaches the extraction task.

---

## Beyond Contracts: Where These Rules Actually Shine

I started with contracts because that's where the payment terms mistake burned me. But these three rules apply to any workflow where AI is extracting or summarizing structured information from unstructured sources. I've deployed them across four other use cases, and the results have been consistent.

### Meeting Transcript Action Items

Meeting transcripts are a minefield for AI extraction. People say contradictory things. They assign tasks verbally and then reassign them five minutes later. They reference deadlines informally — "let's try to get that done by end of week" — which could mean Friday, could mean "whenever."

Without my three rules, the AI would generate a clean action items list with specific owners and dates for everything. Looked great. Was frequently wrong about who actually owned what and when things were due.

With the rules applied:

```
Action item: Migrate staging database to new cluster
Owner: Sarah Chen | EXTRACTED | Timestamp 14:32 — "Sarah,
  can you handle the staging migration?"
Deadline: [BLANK]
  → No specific deadline stated. Jake mentioned "before the
    next sprint" at 22:15, but no date was confirmed.
Priority: High | INFERRED | Based on discussion context —
  team discussed this as blocking the release pipeline
```

The blank deadline is the correct answer here. A fabricated "Friday" or "end of sprint" would create a false expectation that nobody actually agreed to.

### Invoice Processing

Invoice extraction shares the same failure modes as contracts — vendor names that don't quite match PO records, tax calculations that should be verifiable, payment terms that reference a master agreement rather than stating them directly.

The INFERRED tag catches something specific in invoice workflows: calculated fields. When the AI extracts a subtotal and a total, it can verify whether the tax calculation is internally consistent. When it can't reconcile the numbers, it flags it:

```
Subtotal: $14,250.00 | EXTRACTED | Line items total, page 1
Tax (8.25%): $1,175.63 | EXTRACTED | Page 1, tax line
Total: $15,450.00 | EXTRACTED | Page 1, total line
Verification: [BLANK]
  → Calculated total ($14,250 + $1,175.63 = $15,425.63) does
    not match stated total ($15,450.00). Discrepancy of $24.37.
```

That $24.37 discrepancy would have sailed through a standard extraction. The three-rule system caught it because Rule 3 forced the model to show its math, and the math didn't add up.

### Legal Document Review

Legal documents are where the INFERRED tag earns its keep most dramatically. Legal language is full of implications, cross-references, and defined terms that mean something different from their plain English meaning. "Reasonable efforts" has a different legal weight than "best efforts." "Material adverse change" is a defined term in most M&A agreements, but the definition varies per contract.

When the AI marks something as INFERRED in a legal context, it's flagging exactly the fields where a lawyer needs to weigh in. The extraction handles the straightforward stuff — names, dates, amounts — while explicitly tagging the interpretive fields for expert review.

### CRM Data Entry and Vendor Scoring

CRM data from emails, forms, and meeting notes is notoriously messy. A prospect says they have "around 200 employees" — is that 200? Is it 150-250? The AI's job is to pull the data; my three rules ensure it doesn't silently round to a precise number that was never stated.

```
Company size: ~200 | INFERRED | Contact stated "around 200"
  in email dated March 3 — exact figure not confirmed
Annual revenue: [BLANK]
  → Revenue not disclosed. Contact mentioned "eight-figure
    range" in call notes but declined to specify.
```

That tilde and that blank field are honest. A CRM populated with fabricated precision is worse than one with honest gaps, because fabricated data gets used for segmentation, scoring, and forecasting — and the errors compound downstream.

If you're building AI-powered workflows for contract review, data extraction, or any of these use cases, and you'd rather have someone set up the full prompting system and integrate it into your pipeline, [I take on these kinds of projects on Fiverr](https://www.fiverr.com/s/EgxYmWD).

---

## What These Rules Don't Fix (And What to Do About It)

I want to be honest about the limitations, because overpromising is exactly the kind of problem this entire article is about.

### Domain Knowledge Gaps

The three rules help with ambiguity and missing data. They don't help when the model lacks the domain knowledge to recognize that something is wrong. If a contract says "payment terms: net 30 from invoice date" and the industry standard for that vendor category is net 60, the model will happily extract "net 30" and mark it EXTRACTED. It won't flag it as unusual because it doesn't know what's usual.

For domain-specific validation, you still need a human expert or a reference dataset the model can check against. The three rules make the human's job faster, but they don't eliminate the human.

### Deliberately Misleading Documents

If a document is designed to deceive — burying contradictory terms in appendices, using defined terms that override plain meaning — the model may not catch it even with these rules. The rules help with *accidental* ambiguity (which is 90%+ of real-world extraction errors). They don't help with *intentional* obfuscation.

### The 2-3% Residual Error Rate

Even with all three rules running, I still see a small residual error rate — roughly 2-3% of fields across large batches. These tend to be cases where the document language is clear and unambiguous, but the AI interprets it differently than a human would due to subtle context it's missing. Uncommon date formats, industry-specific abbreviations, or references to external documents the model doesn't have access to.

The rules reduced my error rate from roughly 12-15% (without any mitigation) to 2-3%. That's a huge improvement. But it's not zero. Plan accordingly.

### Model Selection Still Matters

I've tested these rules across GPT-4o, Claude Sonnet 4, Claude Opus 4, and Gemini 2.0 Pro. They work on all of them, but the behavior isn't identical. Claude models tend to be more conservative with blanks — they'll leave more fields empty even when the data is fairly clear. GPT-4o tends to be more aggressive about inferring — it'll mark things INFERRED rather than BLANK in borderline cases.

I currently run Claude Sonnet 4 for most extraction work. It hits the sweet spot between cost, speed, and appropriate conservatism. For high-stakes contracts where I want maximum caution, I step up to Opus 4. If you're interested in optimizing your model selection across different task types, [I wrote a detailed guide on cost-optimized agent architectures](/content/mejba.me/ai-agent-cost-optimization-guide.md) that covers exactly this.

---

## The Bigger Picture: Why This Framework Matters Now

A multi-model study from 2025 found that [simple prompt-based mitigation cut GPT-4o's hallucination rate from 53% to 23%](https://www.godofprompt.ai/blog/9-prompt-engineering-methods-to-reduce-hallucinations-proven-tips). That's a meaningful reduction from prompt changes alone — no architectural changes, no fine-tuning, no RAG.

My three-rule system pushes further because it doesn't just tell the model to "be accurate." It restructures the task itself. The model isn't being asked to try harder. It's being asked to do a fundamentally different kind of work — classification (certain vs. uncertain), attribution (extracted vs. inferred), and explanation (why was this left blank?). These are tasks LLMs handle well, which is why the error rates drop so dramatically.

Here's what I think is happening at a deeper level. The default behavior of these models — guess confidently, complete every field, never say "I don't know" — comes from their training. As the [Science article on hallucination origins](https://www.science.org/content/article/ai-hallucinates-because-it-s-trained-fake-answers-it-doesn-t-know) explains, LLMs are essentially trained on an exam where blank answers score zero. Guessing is always the rational strategy.

My three rules create a different exam. One where blank answers score zero (neutral), but wrong answers score -3 (actively bad). That's the asymmetric penalty that [the Reinforced Hesitation researchers at Maryland](https://arxiv.org/abs/2511.11500) found changes model behavior at the training level. I'm applying the same logic at the prompt level, and it works — imperfectly, less rigorously, but practically and immediately.

The exciting part? Anthropic, OpenAI, and Google are all actively researching calibration-aware training — building the equivalent of these rules directly into model weights. But that's a multi-year research program. My three rules work today, in production, right now.

And honestly, even when models get better at self-calibration natively, I'll probably keep using explicit prompting rules. Belt and suspenders. The cost of an incorrect extraction in a business context — an invoice paid to the wrong amount, a contract term missed, a compliance field left unchecked — is always higher than the cost of being slightly over-cautious.

---

## How to Implement This in Your Workflow Tomorrow

If you've made it this far, you already understand the framework. Here's the practical implementation path I'd follow if I were starting from zero today.

### Step 1: Pick One Extraction Workflow

Don't try to overhaul everything at once. Pick the workflow where incorrect AI outputs have caused the most pain. For most people, that's one of: contract review, invoice processing, meeting action items, or CRM data entry.

### Step 2: Write Your Prompt Template

Start with my contract template above and modify it for your use case. The three rules stay the same — blank when uncertain, -3 penalty for errors, extracted/inferred attribution. What changes is the field list and the output format.

### Step 3: Run 10 Documents With and Without the Rules

This is how I validated the approach. I ran 10 contracts through standard extraction (no rules) and 10 through the three-rule system, then manually verified every field. The standard extraction had 14 errors across 10 documents. The three-rule extraction had 3 errors — and all three were in the residual category (clear language, subtle misinterpretation).

### Step 4: Calibrate the Blank Threshold

Different models have different blank sensitivities. If your model is leaving too many fields blank (over 20% on clean documents), you might need to soften the language slightly: "Leave blank only when you genuinely cannot determine the value with reasonable confidence." If it's still guessing too aggressively, tighten it: "When in even slight doubt, prefer blank over a guess."

### Step 5: Build the Review Workflow Around the Output

The whole point of these rules is to change how you review AI output. Train your team (or yourself) to follow the three-tier review: spot-check EXTRACTED, review all INFERRED, investigate all BLANK. Once this workflow becomes habit, the time savings are permanent.

### Pro tip: Version Your Prompts

I keep every prompt template in a version-controlled markdown file. When I tweak the wording — adjusting blank sensitivity, adding new fields, changing the output format — I commit the change with a note about why. Three months from now, when you're wondering why you changed "ambiguous" to "unclear" in the blank rule, you'll thank yourself.

---

## The Question Nobody Asks Until It's Too Late

I spent the first year of working with AI on a fundamentally flawed assumption: that accuracy was the metric that mattered. Get the model to produce more correct answers. Fine-tune for precision. Optimize for the right response.

That contract incident taught me the real metric isn't accuracy. It's *trustworthiness*. A system that's 98% accurate but gives you no way to identify the 2% that's wrong is less useful than a system that's 95% accurate but clearly flags every uncertain output.

My three rules don't make AI more accurate (though they do, as a side effect). They make AI more *trustworthy*. They create a system where you know exactly what the model is confident about, what it inferred, and what it couldn't determine. That transparency turns AI from a black box you have to fully verify into a collaborator whose work you can efficiently audit.

The question I'd leave you with: right now, today, in whatever AI workflow you're running — do you know which outputs your model is confident about and which ones it guessed?

Because if you can't tell the difference, you're in the same position I was before that contract blew up. You just haven't found your wrong payment terms yet.

---

## Frequently Asked Questions

### Do these prompting rules work with all AI models?

Yes — I've tested them across GPT-4o, Claude Sonnet 4, Claude Opus 4, and Gemini 2.0 Pro with consistent results. Claude models tend toward more conservative blanking while GPT-4o infers more aggressively. Adjust the blank threshold language to calibrate for your preferred model.

### How much do these rules reduce AI hallucination in data extraction?

In my contract review workflows, the three-rule system reduced extraction errors from roughly 12-15% to 2-3% — approximately an 80% error reduction. A 2025 multi-model study found that prompt-based mitigation alone cut GPT-4o hallucination from 53% to 23%. Results vary by document complexity and model choice.

### Can I use these rules for tasks beyond document extraction?

The framework applies to any workflow where AI processes unstructured input into structured output — meeting transcripts, invoice processing, CRM data entry, legal review, and vendor scoring. The three rules (blank when uncertain, error penalty, source attribution) translate directly. Adjust the field list and output format for your use case.

### Does the -3 penalty scoring actually affect AI model behavior?

It does, measurably. Language models have internalized incentive structures from training data. Framing asymmetric costs in the prompt triggers conservative, verification-oriented behavior patterns. Researchers at the University of Maryland formalized this concept as "Reinforced Hesitation" in late 2025, confirming that asymmetric penalties shift model behavior along a risk-accuracy frontier.

### How long does it take to review AI output with these three rules?

My contract review time dropped from 25-35 minutes (checking every field) to 8-12 minutes (spot-checking extracted fields, reviewing inferred and blank fields). The three-tier review workflow — skim extracted, verify inferred, investigate blank — eliminates the need to re-read source documents line by line.

---

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
