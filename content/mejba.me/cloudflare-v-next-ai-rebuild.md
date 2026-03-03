**BRAND:** mejba.me
**TITLE:** Cloudflare Rebuilt Next.js With AI in 7 Days
**SLUG:** cloudflare-v-next-ai-rebuild
**TAGS:** Frontend Development, AI Tools, Cloudflare Workers, Next.js, Case Study

---

One engineer. One week. One thousand dollars.

That's what it cost Cloudflare to rebuild Next.js from scratch.

Not patch it. Not wrap it. Not reverse-engineer its build output like everyone else had been attempting. A complete reimplementation of the Next.js API surface — Vite instead of Webpack, Cloudflare Workers instead of Node.js, and a deployment model that runs at the edge by design rather than as an afterthought.

The result is called V-Next. And when I first read the announcement, my initial reaction was a mix of "that's impressive" and "wait, is this actually stable enough to think about?" Those two feelings held simultaneously for about a day, until I started digging into the technical details and the production data.

Here's what shifted my thinking: the $1,000 figure isn't the interesting part. Cost is just the headline that grabs attention. What's actually worth examining is the methodology — how an AI-assisted feedback loop, anchored by Next.js's own open-source test suite, turned what should have been a multi-month team project into seven days of work by one engineer. That methodology is more significant than the framework itself, because it describes something about how complex software gets built going forward.

There's also a buried technical decision in V-Next that most writeups skip past — the one that explains why the bundle size reduction is 57% and not 15%. I'll get to that, but first you need to understand why Cloudflare was motivated to do this at all, because the backstory makes the architecture choices make sense.

---

**The Vercel Lock-In Problem Nobody Talks About Honestly**

Next.js is genuinely excellent software. Vercel built something that solved real problems — SEO-friendly rendering, static generation, server-side rendering, incremental static regeneration, server actions — and wrapped it in a deployment experience that made all of it feel easy. The framework and the platform grew together, and that co-evolution is part of what made Next.js dominant.

It's also exactly what created the tension.

Vercel runs on full Node.js. That gives it access to the complete Node.js API surface — native modules, file system, crypto, streams, all of it. Next.js features evolved assuming that full compatibility was available. Some of those features — particularly the more advanced ISR configurations and server action patterns — work on Vercel and become maintenance headaches everywhere else.

Cloudflare Workers is a different runtime. It's not Node.js. It's a custom V8-based execution environment with a subset of web standard APIs and a limited Node.js compatibility layer. The tradeoff is significant: Workers run at the edge, in data centers across 300+ cities, with cold start times measured in milliseconds rather than seconds. The performance profile is genuinely different. But that performance comes from a constrained runtime, and Next.js was never designed with that constraint in mind.

The community attempt to bridge this gap was called OpenNext — an open-source project that tried to make Next.js work on Cloudflare Workers by adapting the build output. The problem was architectural. OpenNext had to reverse-engineer what Next.js's build process produced and then transform it to work in a different runtime. Every time Vercel updated Next.js internals (which happens constantly), something in OpenNext broke. Maintainers were always chasing the framework instead of building.

Cloudflare watched this cycle long enough, looked at the effort required to maintain OpenNext, and made a different decision: stop adapting Next.js's output and instead rebuild the thing that produces it.

That's V-Next. A clean implementation of the Next.js API surface — the same `app/` directory, the same routing conventions, the same component patterns developers already know — but written from scratch against a Cloudflare Workers target.

---

**How AI Built a Framework in Seven Days**

The methodology is the part of this story that keeps pulling my attention back.

The Next.js test suite is public. It's comprehensive — covering routing behavior, rendering modes, data fetching patterns, error boundaries, and a few hundred other cases that define exactly what correct Next.js behavior looks like. It's the kind of test coverage you'd want for a framework that millions of applications depend on.

Cloudflare's engineer used those tests as the specification. Not documentation. Not the Next.js source code. The tests — because tests define behavior unambiguously in a way that prose documentation never quite does.

The process ran as a feedback loop: AI generates code, tests run, failures get fed back to the AI, AI revises, tests run again. Repeat. The open-source test suite provided the ground truth that the AI generation needed to converge toward. Without that anchor, AI-generated code would drift — producing something that looked like Next.js behavior but diverged in edge cases in ways that only surface in production.

The human engineer's role was oversight and course correction. When the AI misunderstood a requirement, when two tests pulled in incompatible directions, when a generated solution technically passed tests but implemented something semantically wrong — those were the moments requiring human judgment. Not the majority of the work. But the critical 10% that determined whether the output was actually correct.

The cost figure — $1,000 — reflects AI compute spend. One week of intensive agent runs, test iterations, and revision cycles. That number will only come down as models get more efficient.

What this approach requires is a comprehensive test suite that actually specifies the behavior you're trying to replicate. Most projects don't have this. Next.js does because it's a mature, heavily maintained open-source project with thousands of contributors who care about behavioral correctness. The methodology that worked here works precisely because the specification was already encoded in tests. That's a real constraint, and it's worth acknowledging.

What I find most interesting about the methodology is where the human engineer spent their time. Not writing implementation code — the AI handled that. Not running tests — CI handles that. The human work was largely semantic: evaluating whether a generated solution that passed tests was actually implementing the right thing. Tests verify behavior within defined inputs. They don't verify that you picked the right architecture for the next three versions of the framework.

That distinction matters. There's a class of decision — "should routing state live in memory or be reconstructed from URL on each request?" — where tests don't give you a clear answer, but architectural consequences compound over time. These are the decisions where human expertise is non-negotiable. The AI can generate both approaches. It takes someone who's maintained production Next.js apps to know which one causes problems six months later.

The implication for teams thinking about applying this methodology to their own projects: you need a dense test suite and a domain expert who can audit the output. The AI compresses implementation time. It doesn't remove the need for engineering judgment. Those two things are different, and conflating them is how you end up with a codebase that passes all its tests and still has fundamental architectural problems.

---

**The Technical Decision That Explains the 57% Bundle Reduction**

Here's the thing most V-Next coverage glosses over.

Switching from Webpack to Vite isn't just a build tool swap. The two tools have fundamentally different philosophies about bundling.

Webpack was designed in an era of HTTP/1 where bundling everything into fewer, larger files was the right call — fewer round trips meant better performance. Vite was designed after ES modules became universal, which changed the optimization math entirely. Vite uses native ES modules in development and Rollup under the hood for production builds. Both are oriented toward producing smaller, more targeted output than Webpack.

On top of that, Cloudflare Workers runs at the edge — which means geographic latency is already solved by the runtime architecture. You're not racing against 200ms round trips to a distant server. The edge location handles that. What you're optimizing for shifts from "fewer HTTP requests" to "smaller total payload." Vite's output philosophy aligns better with that constraint.

The 57% bundle size reduction isn't magic. It's the compound effect of a build tool that produces leaner output combined with a deployment target that doesn't need the Webpack-era optimizations that added weight to bundles in the first place.

Build times at 4x faster follow similar reasoning. Vite's development experience has always been faster than Webpack's — near-instant hot module replacement, faster cold starts, parallel processing that Webpack's plugin architecture made difficult. That speed advantage translates to production builds too.

**What V-Next Does With ISR**

Incremental Static Regeneration is one of Next.js's most powerful features and also the hardest to port to a different runtime. Original Next.js ISR uses the file system — it writes regenerated pages to disk on the server, serves them, and revalidates in the background.

Cloudflare Workers doesn't have a file system. It has Cloudflare KV — a globally distributed key-value store with edge-level read speeds.

V-Next's ISR implementation uses KV as the storage layer. Pages get written to KV stores, served from there, and revalidated on the configured interval. The behavior from the developer's perspective is the same. The implementation underneath is adapted to the runtime constraints of Workers.

This is actually a good fit. KV reads at the edge are fast. The globally distributed nature of KV means ISR'd pages are available close to users across all of Cloudflare's 300+ data center locations without additional configuration. On Vercel, ISR requires specific edge cache behavior that you configure per-route. On V-Next with Cloudflare, the distribution is a property of the storage layer itself.

---

**What V-Next Means If You're Building Something Today**

Before you start migrating anything: V-Next is experimental. Cloudflare has been clear about this, and it's the right framing. Several real applications are running on it in production — including CIO.gov, which is a meaningful endorsement — but the edge cases are real.

Here's what's not fully working yet:

Open Graph edge runtime analytics don't port cleanly. If you're relying on these for social sharing previews in specific configurations, test before committing.

KV blob bindings and Postgres bindings have limitations. V-Next uses Cloudflare's storage primitives, and complex database integration patterns that work in a Node.js environment hit the Workers runtime constraints. Prisma with direct Postgres connections, for instance, requires Hyperdrive (Cloudflare's database proxy service) to work correctly.

Turbopack isn't integrated. If you've adopted Turbopack for local development speed, you're running Vite in V-Next — which is fast, but it's a different tool and some Turbopack-specific configurations won't carry over.

Create Next App scaffolding doesn't have a V-Next equivalent yet. You're setting up projects manually or adapting existing setups.

Some Vercel-specific APIs that Next.js quietly depends on don't exist in V-Next. If you're using `@vercel/og`, `@vercel/analytics`, or similar Vercel-provided packages that integrate with Next.js internals, these need alternatives.

**Projects where V-Next makes sense right now:**

If you're starting a new project and plan to deploy on Cloudflare Workers anyway, V-Next is worth serious consideration. You get the Next.js API surface you already know, faster builds, smaller bundles, and you don't inherit the Webpack constraints.

If you're on Vercel and costs are a pressure point, this is worth benchmarking. Cloudflare's pricing model (bandwidth is cheaper, compute at the edge is cheaper for certain usage patterns) can make the economics meaningfully different at scale.

If you're running a high-traffic content site with heavy ISR usage, the KV-backed approach has real advantages for global read performance.

**Projects to hold off with for now:**

Complex Next.js applications that use many Vercel-specific features or rely on advanced Node.js API patterns are going to hit compatibility walls. The migration surface is too large for experimental software.

Applications with strict stability requirements. Production-critical systems don't belong on experimental frameworks unless you have serious engineering capacity to handle the unexpected.

---

**The Parts of This That Should Make You Uncomfortable**

Honest reaction: V-Next impressed me. The technical approach is sound, the performance numbers are real, and the AI-assisted development methodology represents something genuinely new about how frameworks can be built.

And: I'm not sure Cloudflare is the hero in this story.

Vercel built Next.js. They open-sourced it, maintained it, funded its development for years. Cloudflare used that open-source surface — specifically the test suite that Vercel's team spent enormous effort building — to create a competing product. That's legally fine. It's what open source allows. But it's worth naming what it is: Cloudflare benefited directly from Vercel's investment to compete with Vercel's platform.

The vendor lock-in problem also doesn't disappear. It relocates. V-Next is designed for Cloudflare Workers. ISR runs on Cloudflare KV. Assets serve from Cloudflare R2. If you adopt V-Next, you're not locked into Vercel — you're locked into Cloudflare. The dependency just changed hands.

My unpopular opinion: that might be the right trade for many teams. Cloudflare owns its infrastructure end-to-end — data centers, compute, bandwidth, storage. That vertical integration is a real advantage for pricing predictability and performance optimization. Vercel is building on top of AWS, which adds a layer to the cost structure. But developers should go in clear-eyed about what "escaping lock-in" actually means here.

There's also a broader strategic picture worth keeping in mind. Cloudflare acquired Astro — another popular frontend framework — earlier in this timeline. V-Next is part of a pattern: Cloudflare isn't just selling infrastructure anymore. They're building an opinionated full-stack developer platform that competes with Vercel across multiple frameworks simultaneously. The end state they're working toward is one where the entire frontend deployment ecosystem — framework, build tooling, CDN, storage, compute — lives inside Cloudflare's product surface.

That's not inherently bad for developers. Competition between Vercel and Cloudflare over developer experience is going to push both platforms to improve. But it's worth recognizing the incentive structure behind V-Next. This is a customer acquisition play, not a philanthropic framework contribution. Cloudflare wants the traffic, the retention, and the billable compute that comes with running your production applications. V-Next is the technical means to that commercial end.

Understanding that doesn't make V-Next worse. It just clarifies the lens through which Cloudflare will make decisions about its future development. Features that keep you on Cloudflare infrastructure will get prioritized. Portability features that make it easy to leave will not.

The AI-assisted development story also needs careful framing. One engineer + AI + one week is remarkable. It's also one engineer who understood Next.js deeply, understood Cloudflare Workers deeply, and could evaluate the AI-generated output with precision. The AI compressed the implementation timeline. The human expertise determined whether the compressed output was correct. Both parts of that equation matter.

---

**What the Numbers Actually Show**

Let me be concrete about what's proven and what's still being established.

4x faster build times: measured against real projects, not toy apps. If your CI pipeline is spending 8-12 minutes on Next.js builds, you're looking at sub-3-minute builds with V-Next. That compounds across a team — fewer context switches waiting for builds, faster CI feedback, more iterations per day.

57% smaller bundles: this is significant for Time to Interactive, especially on mobile. A 57% reduction in bundle size translates directly to faster page loads on constrained connections. For content sites, this affects both user experience and Core Web Vitals scores.

CIO.gov running V-Next in production: a US government site with real traffic and real compliance requirements running experimental software is meaningful signal. Government IT teams don't take risks lightly. The fact that they moved is data.

What's not yet established: long-term stability under complex workload patterns, the full compatibility surface across the real diversity of Next.js usage in production codebases, and the maintenance trajectory once V-Next catches up to current Next.js versions.

Track these metrics if you're piloting V-Next: build time (CI logs give you this immediately), bundle size (Cloudflare's build output reports this), and Time to Interactive (Lighthouse or WebPageTest against production). Those three numbers tell you whether the theoretical advantages are showing up in your specific application.

One practical note: set up a shadow deployment first. Run V-Next in parallel with your existing Vercel deployment on non-critical routes before cutting over. This gives you real traffic benchmarks without production risk. The compatibility gaps that matter for your specific project will surface quickly under real conditions — far more reliably than any internal testing can reveal. Two weeks of shadow traffic on your actual user patterns is worth more than any benchmark comparison from someone else's project.

---

**The Part That's Worth Sitting With**

One engineer and an AI model rebuilt a major frontend framework in seven days, and the output runs in production on real sites including a government domain.

That's not a benchmark. That's a proof of concept for how complex software can get built.

The Next.js test suite acted as the specification. The AI acted as the implementation engine. Human expertise acted as the quality gate. That division of labor is going to show up in more software projects over the next 18 months — not just framework rebuilds, but complex migrations, library ports, and API reimplementations where the correct behavior is already defined but the implementation work is prohibitive.

The interesting question isn't whether Cloudflare can finish V-Next. They have the engineering capacity and the infrastructure motive. The interesting question is what gets rebuilt next, by whom, and what that does to the assumption that complex software takes teams of people over long timelines.

If a framework can be rebuilt in a week, what else can?

That's the question V-Next is actually asking. The answer is going to reshape how we think about the cost and pace of ambitious engineering work — and as someone building with AI agents professionally, that question is significantly more exciting to me than any benchmark number.

---

## 🤝 Let's Work Together

Looking to build AI systems, automate workflows, or scale your tech infrastructure? I'd love to help.

* 🔗 **Fiverr** (custom builds & integrations): [fiverr.com/s/EgxYmWD](https://www.fiverr.com/s/EgxYmWD)
* 🌐 **Portfolio**: [mejba.me](https://www.mejba.me)
* 🏢 **Ramlit Limited** (enterprise solutions): [ramlit.com](https://www.ramlit.com)
* 🎨 **ColorPark** (design & branding): [colorpark.io](https://www.colorpark.io)
* 🛡 **xCyberSecurity** (security services): [xcybersecurity.io](https://www.xcybersecurity.io)
