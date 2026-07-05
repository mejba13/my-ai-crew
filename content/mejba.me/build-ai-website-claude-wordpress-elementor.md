**BRAND:** mejba.me
**TITLE:** Build an AI Website With Claude and WordPress
**META TITLE:** Build AI Website With Claude + WordPress (Elementor)
**SLUG:** build-ai-website-claude-wordpress-elementor
**PRIMARY KEYWORD:** build AI website with Claude and WordPress
**META DESCRIPTION:** Generate a 5-page site with Claude, then import it into WordPress + Elementor for hands-on editing. The full workflow, the gotchas, and what to fix.
**TAGS:** Claude AI, WordPress, Elementor, AI Web Design, MCP

---

The mistake almost everyone makes with AI-built websites is the one you can't see until you try to change a button.

You prompt an AI, it spits out a gorgeous landing page, you ship it — and three days later the client emails: "Can we make the hero text a little smaller and swap the second photo?" Now you're back in the chat window, typing "make the hero text smaller, swap the second photo, keep everything else exactly the same," crossing your fingers, and watching the AI rebuild half the page and break the part you liked. I've lived this loop. It's why I wrote a whole post about building a [client-editable CMS on top of Claude Code](https://www.mejba.me/claude-code-cms-client-editable-websites) — because "the AI made it but nobody can touch it" is the real failure mode, not the generation.

So when I came across Daryl's tutorial on how to build an AI website with Claude and WordPress, the part that grabbed me wasn't the speed. It was the architecture. Generate the design with Claude — fast — then import it into WordPress and Elementor, where you edit text, images, colors, and layout *with your hands* instead of guessing through prompts. That split is the whole point. Let me walk you through the workflow, where it actually breaks, and what I'd watch for before you trust it with a real project.

## Why generate with AI but edit in WordPress?

Here's the short version, because it's the load-bearing idea of this entire approach: **use Claude to generate the first 80% of a website in minutes, then move into WordPress + Elementor for the granular 20% — the part where prompting is slower and worse than just dragging a slider.**

Traditional web development is slow in ways that have nothing to do with talent. You hunt for a theme. You wrestle the layout into shape. You fight the mobile breakpoints at 11pm. Daryl's framing — and I agree with it completely — is that the bottleneck was never the *idea* of the site. It was the manual assembly.

AI fixes the assembly. What it doesn't fix is the fiddly, iterative, "nudge this 8 pixels left" work that follows. And that's where most AI website tutorials fall apart: they treat the AI as the editor too. So you end up re-prompting for every tweak, burning tokens and patience, never quite landing the change you wanted.

The smarter division of labor looks like this:

- **Claude** handles structure, copy, sections, and a coherent first design — the heavy lift.
- **WordPress + Elementor** handles the edits a human makes by eye — drag, click, type, replace.

WordPress isn't a nostalgic choice here, by the way. It still runs roughly 43.5% of all websites on the internet, which means the plugin ecosystem, the hosting support, and the sheer volume of "how do I fix X" answers are unmatched. Even Elementor itself now ships an agentic assistant called Angie that talks to AI tools over the Model Context Protocol — the page builders are leaning into this, not fighting it. The AI web development market is projected around $14.27 billion in 2026, and the workflows that win aren't "AI does everything." They're "AI does the part it's great at, humans steer the part they're great at."

That's the thesis. Now here's the three-step process that makes it real — and the third step is where the interesting failures live, so stick with me.

## Step 1: Generate the site with Claude AI

First, a non-obvious requirement that trips people up immediately: **you need the Claude desktop app, not the browser version.** Download it from claude.com/download. The reason is the integration in Step 3 — Claude desktop connects to your WordPress site through a local config file and an MCP server, and the browser tab can't reach into your machine that way. Skip this and you'll hit a wall later wondering why the WordPress connection won't appear.

With desktop installed, the goal is a 5-page modern professional site: Home, About, Services, Listings, and Contact. (Want more pages? You edit the prompt — it's not locked to five.) The example Daryl uses is "Wilson Estates," a fictional Las Vegas real estate agency, which is a good choice because real estate forces the AI to handle listings, filters, and testimonials — not just a brochure.

### What goes in a strong generation prompt?

A strong prompt for an AI website includes five things: the business name and description, the target customers, the website's goals, niche-and-location SEO keywords, and your design preferences for color, font, and style. Miss the keywords and you get generic filler copy.

Let me expand that, because the keyword piece is the single highest-leverage line in the whole prompt:

1. **Business name + description.** "Wilson Estates, a boutique real estate agency."
2. **Target customers.** Who's buying? First-time buyers, luxury, investors?
3. **Website goals.** Lead capture? Listing browsing? Booking calls?
4. **SEO keywords — niche *plus* location.** This is the one. Not "real estate" but "real estate services Las Vegas." Targeted keywords pull the AI away from beige, interchangeable copy and toward text that reads like it belongs to a specific business in a specific city. It improves the quality of the writing *and* the SEO foundation in one move.
5. **Design + functional requirements.** Colors, fonts, overall vibe — plus working links and buttons, SEO-ready markup, and images included.

### A worked example: the Wilson Estates prompt

Abstract advice about prompts is cheap, so here's what a real one looks like assembled from those five pieces. This is the kind of prompt I'd actually paste into Claude desktop for the example agency — notice how the keyword line is woven into the brief, not bolted on at the end:

```
Build a modern, professional 5-page website (Home, About, Services,
Listings, Contact) for "Wilson Estates," a boutique real estate agency.

Business: Wilson Estates helps first-time buyers and investors find
and close on homes in the Las Vegas valley. Friendly, expert, no pressure.

Target customers: first-time homebuyers, relocating professionals, and
small property investors searching for "real estate services Las Vegas,"
"Las Vegas homes for sale," and "Summerlin real estate agent."

Goals: capture buyer leads (prominent contact + call-to-action on every
page), let visitors browse listings with filters, build trust with
testimonials.

Design: clean and warm — deep navy and gold accents, a serif heading
font paired with a readable sans body, generous white space. Modern,
not corporate-cold.

Requirements: working internal links and buttons, SEO-ready headings
and meta structure, real placeholder images included, mobile-responsive,
a listings page with location/price/bedroom filters, and a testimonials
section on the home page.
```

See how the location keywords ("Las Vegas," "Summerlin") sit inside the customer and goals description? That's deliberate. When the keywords are *contextual* rather than a trailing list, Claude writes copy that actually reads like a Las Vegas agency wrote it — neighborhood names, local framing, specific calls to action. Drop those and you get "Welcome to our real estate agency. We are passionate about helping you find your dream home." Generic, forgettable, and indistinguishable from ten thousand other AI drafts. The keyword line is the cheapest quality upgrade in the entire prompt.

In Daryl's walkthrough, Claude produces raw HTML in about three to five minutes: structured pages, a modern header and footer, listings with filters, testimonials, and CTA forms, all in a consistent tone. One quirk worth knowing — the images render correctly in a browser but *don't* preview inside Claude itself, so don't panic when the chat looks image-less. Open the file in a browser to actually see it.

Then you debug with a follow-up prompt. Layout inconsistencies, a broken link, a mobile menu button showing the wrong text — you describe what's off and Claude patches it. This is the *last* place where prompting-to-edit makes sense, because you're still in the raw-HTML phase. Once it's in WordPress, you stop prompting for tweaks. That's the discipline.

<!-- IMAGE: Side-by-side of the Claude desktop chat (left) showing the generation prompt, and a browser tab (right) rendering the generated Wilson Estates homepage with a hero image and listings grid. Alt text: "Claude AI generating a real estate website homepage from a prompt, rendered in a browser". Caption: "Claude builds the raw HTML in minutes — but the images only render in a browser, not in the chat window." -->

That's the easy part. Now you need somewhere to put it.

## Step 2: Set up hosting on Hostinger

You need WordPress hosting before you can import anything, and the tutorial uses Hostinger because the WordPress install is automated and the entry price is low. Here's the current lineup (verified June 2026), so you can pick by *traffic*, not by guessing:

| Plan | Price (2-yr intro) | Sites | Storage | Backups | Best for |
|---|---|---|---|---|---|
| Premium | ~$2.99/mo | up to 3 sites | ~20GB standard SSD | Weekly, ~30 PHP workers | A single first site |
| Business | ~$3.99/mo | up to 100 sites | 200GB NVMe | Daily, free Cloudflare CDN, up to 60 PHP workers | ~100k visits/mo — the sweet spot |
| Cloud Startup | ~$7.99/mo | up to 100 sites | 100GB NVMe, dedicated IP | Daily, priority support | ~200k visits/mo |

A real detail that matters: NVMe storage is roughly 6x faster than standard SSD. For a site that's heavy with images and Elementor (and an AI-generated site *will* be image-heavy), that speed shows up in load times and Core Web Vitals. If you're choosing between Premium and Business, the NVMe + daily backups + free CDN on Business is usually worth the extra ~$1.30/month — especially the daily backups, because you *will* break the site at least once during the Elementor import phase.

Two more things people get burned by:

- **The free domain.** Plans of 12 months or longer include a free domain registration. Register it during setup.
- **The two verification emails.** Hostinger sends two emails you must confirm within 30 days, or the service cancels. Confirm both immediately — don't leave them sitting in your inbox.

Setup itself is linear: create your account, choose the plan and duration, handle billing, register the free domain, select your audience's location (it places your site on a nearby server), and let the automated WordPress install run. After that you reach the WordPress admin two ways — `yourdomain.com/wp-admin` with your credentials, or through Hostinger's hPanel without typing credentials at all.

One honest note since transparency matters here: the tutorial uses a creator coupon, `darrell10`, for roughly 10% extra off. That's Daryl's referral code — I'm naming it plainly so you know what it is rather than dressing it up as a secret deal. Use it or don't; the workflow is identical either way.

Hosting's done. Now comes the part that separates this approach from every other AI-site tutorial.

## Step 3: Connect Claude to WordPress and convert the site

This is the step that makes the whole thing work, and it's also where I'd slow you down and make you read carefully — because one of the tools involved can delete files on your server if you're careless.

### Install the theme and plugins

Start with the **Astra theme**. Daryl picked it through extensive A/B testing as a proven lightweight base, and "lightweight" is doing real work here — a bloated theme fights Elementor and tanks your load times. Then install four plugins:

- **Elementor (free)** — the page builder you'll edit in.
- **WPForms (free)** — for the contact form.
- **XPro Theme Builder for Elementor (free)** — this is the quiet hero. It's a free alternative to Elementor Pro for building custom headers and footers, which it saves to the XPro library. You do not need to pay for Elementor Pro just to get a custom header.
- **Nova Mira (free)** — and this one does NOT come from the WordPress plugin repository. You download it from a separate site. More on what it does — and why it's dangerous — in a second.

Before you leave Elementor's settings, two toggles. **Disable the experimental Atomic editor.** Elementor's Editor V4 introduced "Atomic Elements" — lightweight modular widgets, a Variables Manager, a flexbox-first, cleaner DOM. It's genuinely the future of Elementor. But right now it confuses the AI import, so turn it off for this workflow. And **enable the "Container" (flexbox) feature**, so your layouts use modern flexbox containers instead of the deprecated section/column structure. (Yes, there's irony in disabling one flexbox-forward feature while enabling another — Atomic is too new for clean AI mapping today; the Container feature is mature and well-understood.)

Last local prerequisite: **install Node.js on your machine.** Claude may export React components during the build, and Node ensures those exports actually succeed. It's a five-minute install you'll forget you needed until something silently fails without it.

### Connect Claude desktop to WordPress with MCP

Here's how the connection works, plainly: WordPress generates an application password, you paste a config block into Claude desktop's config file, you restart Claude, and an MCP server running on your WordPress site lets Claude read your database, files, active plugins, theme structure, and — critically — your Elementor layout data structure.

That last bit is why Nova Mira matters so much. Without it, an AI building for WordPress tends to dump raw HTML blocks into the page. With Nova Mira feeding Claude the real Elementor element schema, Claude can build with *native* Elementor widgets — headings, buttons, containers, image widgets — the things you can actually click and edit later. It's the difference between "an HTML file living inside WordPress" and "a real Elementor page." I've configured plenty of MCP servers, and this is one of the more genuinely useful applications I've seen — it gives the model the structured context it was always missing.

The config steps that trip people up:

1. In WordPress, generate a **WordPress application password** (Users → Profile → Application Passwords).
2. Copy the config code WordPress provides into Claude desktop's config file. **Use "Save," not "Save As"** — Save As creates a duplicate config and the connection silently won't appear.
3. **Restart Claude desktop fully.** Not just the window — quit and relaunch.
4. Confirm the MCP connection shows as running inside Claude before you do anything else.

### The honest warning about Nova Mira

I'm putting this in its own block because it's the most important safety note in the post.

**Nova Mira can execute PHP and read, write, and delete files on your WordPress server. It is built for development and staging environments only — not live production.** Once your build is done, disable or remove it. Giving an AI model a connection that can run arbitrary code and delete files on a public site is exactly how a fun automation becomes a 2am incident. The free version covers the core MCP capability; Nova Mira Pro (~€49/yr) adds AI memory and deeper WordPress expertise, but the safety rule is the same regardless of tier.

This is also my single biggest "test it yourself first" flag. I have not run this exact end-to-end pipeline on a production project, and I would not tell you to either. Spin up a staging site, point the integration at *that*, and only promote to live once you've seen the result with your own eyes.

### Convert the AI site into Elementor

With the connection live, you send Claude a refined prompt that tells it to build an Elementor-based site — not raw HTML blocks. The prompt asks Claude to:

- Build with native Elementor elements, using Nova Mira for element mapping.
- Create a new navigation menu and set the homepage.
- Use XPro for the header and footer, saving them to the XPro library.
- Use WPForms for the contact page.
- Install to the correct domain directory — **provide the exact site URL with the `https://` prefix.** Omit the protocol and Claude can target the wrong directory.

Claude will likely ask clarifying questions here, and Nova Mira's context helps it ask *good* ones. In Daryl's walkthrough, after about 12 minutes Claude delivered an Elementor-compatible site with most pages and elements intact.

Most. Not all. And "most" is exactly why this approach beats the prompt-everything alternative — because the gaps are now fixable by hand in a visual editor instead of through another round of hopeful prompting.

If you'd rather not stand up staging, configure MCP, and babysit a build like this yourself, this is the kind of AI + WordPress system I build for clients through [my Fiverr](https://www.fiverr.com/s/EgxYmWD) — pipeline set up safely, on staging, with the production cutover handled. But if you're hands-on, keep going, because the next section is the part nobody puts in the highlight reel.

## What breaks during the import — and how to fix it

This is the gold, so I'm not going to soften it. When you build an AI website with Claude and WordPress, the import is never 100% clean. Here are the failures you should *expect*, and the fix for each.

**The header and footer don't show up.** Most common issue. Usually it's a draft-status problem or a placement/template error — the header exists but isn't published or isn't assigned to display sitewide. Fix: prompt Claude to debug the header and footer specifically, addressing metadata and template library issues. This is a legitimate use of a follow-up prompt because it's a structural fix, not a cosmetic tweak.

**Unnecessary flexbox wrapping.** Claude sometimes wraps sections in extra flexbox containers that throw off your layout. Fix: in Elementor, select the section and **deselect "wrap."** One checkbox. The layout usually snaps back into place.

**Some elements fall back to raw HTML widgets.** When Claude can't translate a component into a native Elementor element, it drops in a raw HTML widget instead — testimonials are the usual culprit. Fix: manually replace those with the native Elementor testimonial widget. This is the moment the entire architecture pays off. You're not re-prompting and praying — you're dragging in the correct widget and moving on in 90 seconds.

**Placeholder and AI images.** AI-generated images load from URLs, not from your WordPress media library, so they can be flaky or generic. Fix: replace placeholders with real images from a source like Unsplash, and while you're in there, adjust padding, alignment, and background images by eye. This is the polish pass — the satisfying part where the site stops looking auto-generated and starts looking like *yours*.

<!-- IMAGE: Elementor editor open on the Wilson Estates homepage, with the navigator panel visible showing a raw HTML widget highlighted in red and a native Elementor testimonial widget being dragged in to replace it. Alt text: "Replacing a raw HTML widget with a native Elementor testimonial widget in the WordPress editor". Caption: "When Claude can't translate an element, it falls back to raw HTML — you swap in the native Elementor widget by hand." -->

Notice the pattern across all four fixes: structural problems go back to Claude as a debug prompt; cosmetic and content problems get fixed by hand in Elementor. That line — debug via prompt, polish via mouse — is the mental model to walk away with.

## How long does it really take to build an AI website this way?

Realistically, plan for under an hour of active work for a 5-page site once your hosting and tools are set up — roughly 3-5 minutes for Claude's first generation, ~12 minutes for the Elementor conversion in Daryl's run, and the rest in manual polish.

That's the honest range, and I want to be precise about whose numbers those are: the 3-5 minute and ~12 minute figures come from Daryl's walkthrough, not from a run I personally timed. Your mileage shifts with site complexity, how clean your prompts are, and how many raw-HTML fallbacks you have to swap. The first time you do this, the tool *setup* — Hostinger, theme, four plugins, Node.js, MCP config — will take longer than the build itself. Budget for that.

What you're really buying with this workflow isn't raw speed. It's the elimination of the worst part of web development: the blank canvas. You start from a coherent, populated, on-brand draft and spend your time *refining* instead of *assembling*. For me that's the actual win.

## The caveat I won't skip: AI design sameness

Here's where I push back on the hype a little. The risk with any AI-first build is that it looks like every other AI-first build — the same centered hero, the same three-card services row, the same gradient. I wrote about this exact trap in my piece on [why AI web design needs human oversight](https://www.mejba.me/ai-web-design-human-oversight), and it applies in full force here.

The good news is that this workflow has the cure built in. Because you finish in Elementor by hand, you have a real opportunity to break the sameness — bespoke spacing, a distinctive type choice, images that aren't stock, an asymmetric section the AI would never have proposed. Use it. The generation gets you to "competent fast." The manual pass is what gets you to "memorable." Skip the manual pass and you've just produced another forgettable AI site at speed.

If you've enjoyed building things this way, you'll probably like my broader take on [vibe coding and building apps without writing code](https://www.mejba.me/vibe-coding-build-apps-without-code) — same philosophy, different tools: let AI carry the structural weight, keep a human hand on the wheel.

## Going from staging to a live site without breaking things

Everything above assumes you built on staging — and you should have, because Nova Mira can delete files and you don't point that at a public site. But that leaves an obvious question the tutorial glosses over: how do you actually move the finished site to production safely? This is the part where AI-built projects quietly go wrong, so here's the sequence I'd follow.

First, **turn off the connection before you migrate.** Disable or remove Nova Mira on the staging site once the build is done. There's no reason for an MCP server that can run PHP and delete files to survive into the thing real visitors will hit. This is step one, not an afterthought.

Second, **migrate with a real tool, not by copy-paste.** On Hostinger you can clone or migrate a WordPress site through hPanel, or use a plugin like All-in-One WP Migration to export the staging site and import it onto the production domain. The reason this matters: an Elementor site stores layout data and absolute URLs in the database. If you move files without rewriting those URLs, your images and links point back at the staging domain and half the site appears broken. A proper migration tool handles the search-and-replace of `staging-url` to `live-url` for you.

Third, **re-check the four failure modes on production.** The header/footer, the flexbox wrapping, the raw-HTML fallbacks, and the image URLs — verify all of them survived the move. Migrations occasionally re-trigger the exact issues you fixed on staging, especially the image URLs, since AI-generated images loaded from external links don't live in your media library and can break if the source changes. This is also the moment to finally pull those external images into the WordPress media library so the live site doesn't depend on a URL you don't control.

Fourth, **lock it down.** Confirm SSL is active on the live domain (Hostinger provisions it, but verify the padlock), set up the daily backups you're paying for on the Business plan, and remove any leftover development plugins you only needed for the build. A staging tool left running on production is a liability, not a convenience.

I'm spelling this out because "it worked on my staging site" is where a lot of AI-assisted builds stop — and then the live launch surfaces broken images and a missing header in front of an actual client. The migration is not the glamorous part. It's the part that decides whether the project was real.

## The one thing to do today

Don't build a client site first. Spin up a throwaway staging site — Hostinger's cheapest plan, a fake business — and run the entire pipeline end to end once. Generate with Claude desktop, host it, install Astra plus the four plugins, connect MCP, convert to Elementor, and then deliberately break and fix the header. You'll learn more from that one disposable run than from re-reading any tutorial, mine included.

Because the real skill here was never the prompt. It's knowing which problems to hand back to Claude and which ones to fix with your own two hands in Elementor. Get that line right, and you've turned the slowest, most frustrating part of web development into the fastest. Get it wrong, and you're just back in the chat window at 11pm, typing "keep everything else exactly the same" and watching it break anyway.

## Frequently Asked Questions

### Do I need the Claude desktop app to build a website with WordPress?
Yes — the Claude desktop app is required, not the browser version. The WordPress integration relies on a local config file and an MCP server connection that the browser tab cannot access. Download it from claude.com/download before you start.

### Is Nova Mira safe to use on a live WordPress site?
No. Nova Mira can execute PHP and read, write, and delete files on your server, so it is designed for development and staging environments only. Use it on a staging site, then disable or remove it before going to production.

### Do I need to pay for Elementor Pro for this workflow?
No. The XPro Theme Builder for Elementor is a free alternative that handles custom headers and footers, saving them to its own library. Combined with free Elementor, WPForms, and Nova Mira, the entire plugin stack can be free.

### Why edit the AI-generated site in WordPress instead of just re-prompting Claude?
Because prompting is slow and imprecise for small visual changes. Editing text, images, colors, and layout directly in Elementor is faster and more reliable than re-prompting and hoping the AI doesn't break the parts you already liked. For the full reasoning, see the "Why generate with AI but edit in WordPress" section above.

### What hosting plan should I choose for an AI-built WordPress site?
For most sites, Hostinger's Business plan (~$3.99/mo on a 2-year term) is the sweet spot — NVMe storage roughly 6x faster than standard SSD, daily backups, a free CDN, and headroom for around 100k visits a month. Use the cheaper Premium plan only for a single low-traffic first site.

## Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* **Portfolio**: [mejba.me](https://www.mejba.me)
* **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
