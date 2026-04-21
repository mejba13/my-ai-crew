# Crawl Acceleration Package — Aria Reference

Aria reads this file on demand to emit the Crawl Acceleration Package after every new post. Purpose: reduce the not-indexed backlog via GSC submit list, sitemap entry, IndexNow ping, internal-link verification, and inbound-link opportunities.

## Emission Format

Append the block below to the delivered content package AFTER the social distribution package and BEFORE any final wrap-up.

```markdown
## 🚀 Crawl Acceleration Package

### Google Search Console — URLs to Submit

Submit these URLs via GSC URL Inspection → "Request Indexing":

1. `[canonical_url]` — the new post
2. `[canonical_url of parent cluster/pillar if updated]` — re-crawl trigger
3. `[canonical_url of internal-link target 1]` — refresh crawl signal
4. `[canonical_url of internal-link target 2]` — refresh crawl signal

### Sitemap Entry

Append to `sitemap.xml`:

```xml
<url>
  <loc>[canonical_url]</loc>
  <lastmod>[updated_at]</lastmod>
  <changefreq>[sitemap_changefreq]</changefreq>
  <priority>[sitemap_priority]</priority>
</url>
```

### IndexNow Ping (optional — for Bing / Yandex)

```
POST https://api.indexnow.org/indexnow
Content-Type: application/json

{
  "host": "www.[brand]",
  "key": "[indexnow-key]",
  "urlList": ["[canonical_url]"]
}
```

### Internal Link Verification

Every post MUST reference at least 2 already-indexed pages from the same brand. Verify against `settings/seo/indexed-pages-[brand].md`.

Links used in this post:
- [Anchor text 1] → [URL 1] — verified indexed: ✓ / pending
- [Anchor text 2] → [URL 2] — verified indexed: ✓ / pending
- [Anchor text 3] → [URL 3] — verified indexed: ✓ / pending

If a link target is not in `indexed-pages-[brand].md`, flag it: `⚠ not yet indexed — consider swapping for an indexed target`.

### Cross-Brand Link Suggestions

(Only include if genuinely relevant — skip if forced.)
- [Brand]: [specific URL or section] — [why this reader would care]

### Inbound Link Opportunities

Posts that should link TO this new post (scan via Glob, suggest 2-3 candidates):
- `content/[brand]/[existing-post].md` — add link in section "[section name]" with anchor "[suggested anchor text]"

### Social Ping URLs

Platforms to post to within 24 hours of publish (helps Google discover via social crawl):
- Twitter/X: [handle]
- LinkedIn: [handle]
- Newsletter: next send
- Reddit (if relevant subreddit exists): r/[subreddit]
```

## Rules

- GSC submit list MUST include at least 2 already-indexed URLs (targets of the new post's internal links) — re-submitting these re-triggers crawl on their outbound link graph, accelerating discovery of the new post
- Sitemap entry `lastmod` MUST equal frontmatter `updated_at`
- `changefreq`: `weekly` for tool reviews / news / security, `monthly` for evergreen tutorials
- `priority`: 0.9 pillar, 0.7 standard, 0.6 cluster, 0.5 light refresh
- Inbound link opportunities: Glob `content/[brand]/*.md`, identify 2-3 existing posts where adding a link to the new post is natural, specify WHERE in each
- If `indexed-pages-[brand].md` is empty or missing: mark all verifications as `pending` and prepend the block with `⚠ indexed-pages-[brand].md not populated — verification pending manual GSC export`
