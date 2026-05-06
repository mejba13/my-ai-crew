**BRAND:** mejba.me
**TITLE:** Higgsfield CLI in Claude Code: I Built a Whole Landing Page
**META TITLE:** Higgsfield CLI in Claude Code: Landing Page Build Log
**SLUG:** higgsfield-cli-claude-code-landing-pages
**PRIMARY KEYWORD:** Higgsfield CLI Claude Code
**META DESCRIPTION:** I tested the Higgsfield CLI inside Claude Code to ship a full landing page — hero images, lifestyle shots, a loop video, and the HTML. Here is the actual build log.
**TAGS:** Claude Code, Higgsfield, AI Skills, Landing Pages, Creative Automation

---

The terminal asked me a single question at 11:47 PM on a Tuesday: *"What product are we shooting tonight?"* I typed `Brew Line — minimalist Scandinavian espresso machine, matte white, brass accents, $649 price point.* The CLI paused for two seconds, pinged Higgsfield to check my credit balance, told me I had 826 credits available, then asked if I wanted to start with a 16:9 hero before batching the rest of the assets. I hit enter. Forty-six minutes later, I had eleven generated images, a ten-second hero loop video, and an `outputs/` folder that looked like it had come out of a small creative agency — except the only thing in the room was my laptop and a half-cold cup of pour-over.

I want to be honest about what triggered this experiment. I had been watching the Higgsfield CLI quietly roll into IDEs for a few weeks and shrugged it off as another wrapper. I already had a full Higgsfield + Claude Code workflow running for [an automated creative agency build](https://www.mejba.me/automated-creative-agency-claude-higgsfield) that pumps thirty to one hundred ad variations a week. Why would I need a CLI sitting on top of it? Then I watched a friend ship a full product landing page — copy, images, embedded loop video, responsive layout — in a single Claude Code session using the new `Higgsfield CLI Claude Code` skill bundle. The page was rough but it existed. And it existed in under an hour, with zero design tools open.

That is the entire pitch of this setup, by the way. Not "AI replaces designers." That argument is boring and mostly wrong. The actual pitch is: a marketer or product manager who needs to validate a landing page concept this week — not next month — can now go from "I have a product idea" to "I have a working page with on-brand visuals and a hero video" in one terminal session. No Figma. No Photoshop. No four-day asset turnaround from a freelancer. That is a meaningfully different thing.

So I built one. I picked a fake product, ran the full pipeline end to end, watched my credit counter burn down faster and slower than I expected at different stages, fixed the inconsistencies in the second pass, and saved the entire workflow as a reusable Claude Code skill called `landing page gen`. This post is the build log. Every command. Every credit. Every place the model drifted on me and how I corrected it. By the end you will know exactly what this stack is, what it costs, and whether it is worth installing on your machine tonight.

## Why a CLI Beats Clicking Around in the App

Before the install, a quick reality check on why this matters. Higgsfield itself is excellent. The platform aggregates more than thirty image and video models including Soul 2.0, Sora 2, Veo 3.1, Kling 3.0, Seedance 2.0, Nano Banana 2, and Flux 2 under one subscription, and the Marketing Studio layer above those models is genuinely useful. I have spent enough hours in the web app to know it is one of the better creative interfaces on the market right now.

But clicking through a web app to generate eleven assets is not the same as scripting eleven assets. Every time you switch from a browser to a terminal to a code editor, you lose context. You forget which prompt produced which output. You re-upload the same brand reference three times. You burn ten minutes hunting through your downloads folder for the file you actually liked.

A CLI fixes all of that. Once Higgsfield is exposed as commands inside Claude Code, the agent holds the brand in memory, decides which model to call for each shot, saves outputs to a structured folder with consistent filenames, and chains the whole thing into "now build the landing page" without ever leaving the editor. That is the unlock. Not a new model. Not a new feature. Just the wiring.

Here is the part that surprised me the most: by the third asset in the batch, the CLI had stopped feeling like a tool and started feeling like a junior creative I was directing. I would say "make the next shot warmer, more morning light, lower angle" and the next image would come back warmer, more morning light, lower angle. I have used Higgsfield in the web app for months. That feeling does not happen there.

## What You Actually Need Before You Install Anything

Skip this section if you already have a Claude Code workflow and a Higgsfield account. For everyone else, here is the minimum stack:

- **A Higgsfield account with credits.** I am on a paid plan but the CLI works equally well with credit packs. Higgsfield's 2026 plans run from a Starter tier at fifteen dollars to an Ultra tier at eighty-four dollars per month, with credit packs available for burst generation. If you are testing, the cheapest plan plus a small credit top-up is enough to ship one or two landing pages.
- **A paid Claude subscription.** Claude Code requires it. The CLI calls into Claude Code as the agent runtime, so this is non-negotiable.
- **A code editor with a terminal.** I run VS Code because the install flow is smoothest there. Cursor, Anti-Gravity, and the Claude desktop app all work. The desktop app technically runs the CLI fine but the editor experience is rougher — I would steer new users to VS Code on day one.
- **Node.js 18 or higher.** The Higgsfield CLI ships as an npm package, so this is a hard requirement. If `node --version` returns anything below 18, upgrade before you do anything else.

That is it. No API keys to copy-paste manually. No environment variables to set. The interactive installer handles authentication for you when you point it at Claude Code as the agent.

## The Install: One Command, Three Decisions

In the VS Code terminal, with no project open yet, I ran:

```bash
npx skills@higgsfield/ai-skills
```

That `npx skills` invocation is a Vercel Labs package manager for AI agent behaviors. It fetches a SKILL.md from the specified GitHub repository and installs it into your agent's config directory. In plain language: it pulls Higgsfield's official skill bundle and wires it into whichever agent runtime you tell it to.

The installer then asked me three things:

**One — install scope.** Local to this project, or global across every project on the machine? I picked global. The whole point of a CLI like this is to have it available everywhere, not buried in one folder. If you are paranoid about scope creep, local is fine, but you will be re-installing it the first time you open a new project.

**Two — which skills to enable.** The bundle includes several. I picked three: *Higgsfield generate* (the general-purpose image/video skill), *Product photo shoot* (specifically for product visuals — the one I would lean on hardest tonight), and *Soul ID* (Higgsfield's identity-locked character model — useful if you ever need consistent humans in your images, less critical for a product page but I wanted access to it). You can install all of them. Each one adds a few new commands to the CLI surface area.

**Three — which agent to bind to.** Claude Code, Cursor, Codex, or generic. I picked Claude Code. The installer copied the skill files into `~/.claude/skills/` and confirmed the bind.

Total install time: about ninety seconds, most of which was npm pulling dependencies. No errors. No config files I had to edit by hand. This is the smoothest skill install I have done since [the agent skills system landed](https://www.mejba.me/claude-code-agent-skills-guide).

## Launching the CLI: The One Flag That Changes Everything

To actually use the installed skills, I launched Claude Code with one specific flag:

```bash
claude --dangerously-skip-permissions
```

A note before you copy that command. The `--dangerously-skip-permissions` flag does exactly what it sounds like — it lets Claude Code run end-to-end without approval prompts for file writes, command execution, and tool calls. For a creative pipeline like this, where the agent needs to write fifteen image files and assemble HTML without me clicking "approve" forty times, it is the right choice. For anything touching production code, customer data, or a system you cannot rebuild from scratch, it is the wrong choice. Use it in a scratch directory. Do not use it in your monorepo.

Anthropic now offers a safer middle ground called auto mode that I would normally recommend, but the Higgsfield skills are designed assuming full skip-permissions, so I went with the original. If you are nervous, run this whole pipeline inside a fresh folder you can delete afterward.

Once Claude was running, I typed `/skills` to confirm the new commands had registered. Three new entries showed up: image generation, video generation, and marketing studio. Plus the utility commands — credit balance, job status, asset list. The CLI was live.

<!-- IMAGE: Terminal screenshot showing Claude Code launched with --dangerously-skip-permissions and the /skills output listing the Higgsfield commands. Alt text: "Higgsfield CLI Claude Code skill registration in VS Code terminal". Caption: "The skills surface registers as native CLI commands once the install binds to Claude Code." -->

## Meet Brew Line: The Product I Made Up to Stress-Test the System

Every build log needs a product. I was not about to leak a real client's brief, so I invented one.

**Brew Line** is a minimalist Scandinavian-design espresso machine, matte white casing, brass accents on the portafilter and steam wand, exposed brass piping along the side, single dial control. Price point: $649. Audience: home baristas who have outgrown a pod machine but are not ready to spend twenty-five hundred dollars on a prosumer rig. Vibe: the Muji of espresso machines.

I gave the CLI three things to anchor on: the brand name, a one-paragraph description like the one above, and a target aesthetic I described as "warm morning light, soft shadows, breakfast bar context, never sterile." That third piece is the one most people skip and it is the one that decides whether your output looks like a real product page or like an AI demo.

The CLI confirmed the brief, asked if I wanted to upload any reference photos (I did not — I wanted to see how clean the cold-start was), and asked me to confirm the page sections I needed visuals for. We landed on: hero, three product detail shots, two lifestyle scenes, a feature visual showing the brass piping detail, a catalog row of three angles, and one short hero loop video.

That is eleven assets to generate. The CLI estimated 185 credits.

## The Hero Shot Approval Loop: Don't Skip This

Here is where the workflow gets genuinely smart. Before generating the full batch, the CLI generates the hero image first and stops. It asks: *"Approve this hero before continuing?"*

This is not a UX flourish. It is the single most important step in the pipeline. The hero image becomes the visual anchor for every subsequent asset — the product silhouette, the color treatment, the lighting style, the staging — all of it gets locked from this one frame. If the hero is off, every downstream asset will be off in the same direction. If the hero is right, the rest of the batch tracks reasonably well.

My first hero came back at 16:9, warm light, brass piping visible, but the dial was on the wrong side and the body shape read a little too "kettle" and not enough "espresso machine." I rejected it and asked for a tighter, more architectural framing with the portafilter front and center. Second hero: dial correct, portafilter prominent, brass detail crisp, breakfast bar staging in the background just out of focus. I approved.

Credits burned on the hero loop: fourteen. Slightly more than I expected for a single image, but the CLI was running two passes under the hood and picking the stronger one — a behavior I only noticed when I checked the job log afterward.

After approval, the CLI batched the remaining ten assets. I went to make another coffee. The terminal pinged seven minutes later with "batch complete."

## What the `outputs/` Folder Actually Looked Like

This is one of those small details that matters more than it should. The CLI does not dump everything into a single flat directory. It builds a structured tree:

```
outputs/
  brew-line/
    hero/
      brew-line-hero-01.png
      brew-line-hero-loop-10s.mp4
    product-shots/
      brew-line-front.png
      brew-line-three-quarter.png
      brew-line-detail-portafilter.png
    lifestyle/
      brew-line-morning-counter.png
      brew-line-pulling-shot.png
    features/
      brew-line-brass-piping-macro.png
    catalog/
      brew-line-catalog-row.png
    _meta/
      jobs.json
      prompts.json
```

Every file is named consistently. Every prompt that produced a file is logged in `_meta/prompts.json` along with the model used and the credit cost. If I want to regenerate the front shot with a tweaked prompt, I do not have to remember what I said the first time — I open the JSON, copy the prompt, edit two words, and re-run.

This is the kind of housekeeping that the web app does not do for you. You can build it manually but you will not. Having it for free is half the reason the CLI workflow scales.

## The Credit Math: 185 Estimated, 50.5 Actually Spent

Here is the moment that genuinely surprised me. The pre-batch estimate was 185 credits. Most of the eleven assets came in at three to six credits each, the hero loop video came in at eighteen credits, and the total bill at the end of the run was 50.5 credits. I went from 826 down to 776 (give or take some rounding the platform does on partial-credit jobs). The estimate had been roughly 3.7x too high.

I have a theory about why. The estimator assumes every asset will take the most expensive viable model and burn a full generation. In practice, the Marketing Studio routing layer sends simpler shots to cheaper, faster models — a flat product front view does not need Sora-grade compute, it needs a fast image model running for two seconds. The expensive credits get spent on the hero, the loop video, and any asset where the prompt explicitly asks for cinematic motion or complex staging.

If you are budgeting for a full landing page run, my working math after this build:

- Static product shots: 3 to 6 credits each
- Lifestyle scenes with environmental complexity: 5 to 9 credits each
- Hero loop video (10 seconds, no audio): 15 to 25 credits
- Soul ID character generations (if you use them): 8 to 15 credits each, plus a one-time training cost

For a typical eight-to-twelve-asset landing page with one short loop video, expect to spend somewhere between 45 and 90 credits. Not 185. The estimator is conservative on purpose — better to scare you up front than surprise you on the bill — but the actual spend is much friendlier.

## The Inconsistency Problem: When the Product Drifts

I want to be straight about a real limitation. Across the eleven generated assets, the Brew Line product was *mostly* consistent but not fully consistent. The brass piping appeared on the left side of the body in eight images and on the right side in three. The dial was identical in every shot but the depth of the brass accents varied — some shots had a thicker, more brutalist brass band; others had a thinner trim. The matte white came back as a slightly warmer cream in two of the lifestyle scenes, probably because the warm morning light prompt influenced the body color rendering.

This is the well-known consistency problem with text-to-image models and it does not disappear because there is a CLI in front of them. Anyone who tells you AI product visuals are fully consistent across a batch is selling you something.

The fix is iteration plus reference locking. After the first batch, I picked the strongest hero and one strong three-quarter shot, fed those back into the CLI as locked references, and re-ran the three off-brand images. Second pass came back significantly tighter — brass on the correct side, body color closer to true matte white, brass accent thickness consistent. That cost me another 14 credits.

If I had been doing this for a real client, I would also use Higgsfield's Soul ID to lock the product as a "character" — the same technique people use for human faces. Train once on twenty good shots of the product, and downstream generations stay locked across style, pose, and lighting. I did not bother for this test because I wanted to see how the cold start performed, but for a production run it is the move.

The honest summary: 80 percent of the assets were usable on first generation. 20 percent needed a second pass. Total time including the second pass was still under an hour. That is the trade I am willing to make.

## Handing It Off: Claude Code Builds the Landing Page

Once the assets were locked in the `outputs/` folder, I switched modes. I asked Claude Code to build a single-page landing page for Brew Line that pulled in every asset from the folder, structured the page in this order — hero with embedded loop video, three-up product gallery, lifestyle band with two scenes, feature highlight section with the macro shot, catalog row, pricing, footer — and shipped responsive HTML and CSS that worked on desktop and mobile.

That is one prompt. Maybe forty words. No code attached. No design references uploaded.

What came back was an `index.html` and `styles.css` pair with the hero loop autoplaying muted on load, asset paths wired correctly to the `outputs/` folder, a sticky nav that shrunk on scroll, the pricing block with a $649 callout and a "Reserve Yours" CTA, brand colors pulled from the warm cream + brass palette in the imagery, and a mobile breakpoint at 768 pixels that stacked the gallery vertically. Was it Apple-quality? No. Was it a credible v1 of a product landing page that I could send to a client for feedback? Absolutely yes.

The whole hand-off — assets to working landing page — took about four minutes. I opened the file in Chrome, watched the hero loop play, and just sat there for a second. I have built a hundred landing pages in my career. The first time you watch one assemble itself end-to-end from a single prompt is a strange feeling.

This is the part that connects most directly to [the AI landing page pipeline I built using MCPs](https://www.mejba.me/ai-landing-page-pipeline-mcps) earlier this year. Same end goal, different wiring. The MCP version is more flexible and supports a wider asset palette. The CLI version is faster, simpler, and ships in one terminal session with no MCP servers to spin up. I would now use the CLI for first drafts and the MCP version when I need something custom.

<!-- IMAGE: Browser screenshot of the generated Brew Line landing page showing the hero loop video, product gallery, and pricing section. Alt text: "Higgsfield CLI Claude Code generated landing page for Brew Line espresso machine". Caption: "The full landing page assembled by Claude Code from the Higgsfield CLI outputs folder, rendered in Chrome." -->

## Saving the Whole Thing as a Reusable Skill

Here is where the workflow stops being a one-off demo and starts being infrastructure.

Inside Claude Code, I asked the agent to save the entire workflow as a custom skill named `landing page gen`. Saved globally — same scope as the original Higgsfield install — so it would be available the next time I opened any project on this machine.

The skill creation flow asked me to define what the skill needed as input. I told it: a product name, a product description, an aesthetic direction, brand colors if they exist, a target audience, the page sections needed, and the image ratio (default 16:9). I added an optional input for uploaded brand assets — logos, reference photos, existing product shots — that the skill would use as visual anchors instead of cold-starting from prompts.

The skill saved as a SKILL.md file in `~/.claude/skills/landing-page-gen/`. The next time I want to ship a landing page, I run one command, answer six prompts, and the entire pipeline executes — Higgsfield asset generation, hero approval loop, batch run, folder structuring, HTML build, responsive CSS, the whole sequence.

I have written about [the agent skills system in detail](https://www.mejba.me/claude-code-agent-skills-guide) before, but this is the first time I have built a skill that wraps another set of skills. The Higgsfield CLI commands become primitive operations; my custom skill becomes a higher-order workflow that orchestrates them. The skill creator pattern of [composable workflows on top of primitive skills](https://www.mejba.me/agent-skills-advanced-claude-code) is finally clicking for me, and this build is what made it click.

## Who This Is Actually For

I keep seeing people pitch this stack as "for everyone." That is lazy. Here is the honest list of who should care, in order of how much value they get.

**Marketers and growth leads validating concepts.** This is the strongest use case. You have a campaign idea, you need a landing page to test it against an audience this week, you do not have a designer on retainer or the budget to commission one. Ship a v1 in an hour, run paid traffic to it, validate or kill the concept, repeat. This is the workflow that justifies the entire stack on its own.

**Product managers building internal demos.** The pages you ship to your CEO for the Monday review meeting do not need to be production-quality. They need to communicate the concept fast. Same workflow as marketers, lower stakes, faster turnaround.

**Solo founders pre-revenue.** Validate three product ideas in a weekend instead of one. The credit cost for three landing pages is roughly one-fifth of what you would pay a freelance designer for a single page. The math is hard to argue with.

**Content creators and course builders.** Course launch pages are a perfect fit. Hero, curriculum band, instructor bio (Soul ID earns its keep here), testimonial section, pricing. The skill turns a half-day project into a one-coffee project.

**Real estate agents.** Property listing pages are surprisingly close to product landing pages structurally. Hero shot, gallery, feature highlights, pricing, CTA. I have not tested it but the pattern transfers.

**Where this stack does not belong.** Production e-commerce that needs to scale to thousands of SKUs. Brand-critical campaigns where every asset needs hand-art-direction. Anything where the consistency drift across images would be unacceptable. For those, you still need humans, real photography, or a much more carefully managed asset pipeline.

## What I Would Tell You to Do Tonight

If you have read this far, you are probably already running the math in your head on whether to install this. Three concrete next steps if you want to actually try it:

**1. Spin up a scratch folder and install the CLI.** `mkdir higgsfield-test && cd higgsfield-test`, then `npx skills@higgsfield/ai-skills`. Pick global scope. Enable Higgsfield generate, product photo shoot, and Soul ID. Bind to Claude Code. Total time: under two minutes.

**2. Pick a fake product and run the full pipeline once.** Do not use a real client product on your first run. The point is to learn where the workflow lives, where it breaks, and where it surprises you. My fake product was Brew Line. Yours can be anything — a hot sauce brand, a desk lamp, a SaaS dashboard, a yoga mat. Run it end to end. Watch the credits burn. Look at the `outputs/` folder structure when it is done.

**3. Save it as a skill.** This is the step most people skip and it is where the leverage compounds. The first run takes you an hour. The skill turns every subsequent run into a six-prompt, fifteen-minute job.

The Brew Line page is still sitting in my scratch folder. I might never use it. The point was never the page. The point was the skill. The skill is now part of every project I open going forward, and the next time someone DMs me with "I need a landing page by Friday," I am going to ask them six questions and ship a v1 before they finish their coffee.

That is what changed for me this week. Not the tools — the tools have been there. The wiring. The wiring finally got good enough that "I built a landing page tonight" stopped being a flex and started being a Tuesday.

## Frequently Asked Questions

### Do I need to know how to code to use the Higgsfield CLI in Claude Code?
No. The CLI runs entirely on natural-language prompts and the skill handles the code generation for the landing page itself. You need a terminal, a Claude Code subscription, a Higgsfield account, and the ability to follow a three-step install. If you can install Slack, you can install this stack.

### How much does one landing page cost in Higgsfield credits?
Based on this build, about 50 to 90 credits for a typical eight-to-twelve-asset landing page including a short hero loop video. The CLI's pre-batch estimate runs roughly 3 to 4 times higher than the actual spend because it assumes every asset will use the most expensive model. Budget for the higher number, expect to spend the lower one.

### What is the difference between the Higgsfield CLI and the Higgsfield MCP server?
The CLI installs as a Claude Code skill bundle and runs as native terminal commands inside Claude Code. The MCP server exposes Higgsfield as a remote tool any MCP-compatible agent can call. The CLI is faster to set up and friendlier for one-shot creative pipelines. The MCP is better when you want Higgsfield available across multiple agents or inside a more complex orchestration. For a single-machine creative workflow, start with the CLI.

### Can I run this without `--dangerously-skip-permissions`?
Technically yes, practically no. Without the flag you will hit dozens of approval prompts for file writes and tool calls, and the workflow stops feeling like a pipeline and starts feeling like a click farm. Run it inside a scratch folder you can delete, never inside your production codebase, and you get the speed without the risk.

### Does the Higgsfield CLI work in Cursor or only in Claude Code?
It works in Cursor, Anti-Gravity, the Claude desktop app, and any agent runtime the `npx skills` installer supports. VS Code with Claude Code is the smoothest experience because the skill bundle is designed and tested against that combination. I would start there and migrate later if you have a strong Cursor preference.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
