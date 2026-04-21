# Indexed Pages — mejba.me

Verified-indexed URLs from Google Search Console. Aria uses this file to validate internal-link targets in the Crawl Acceleration Package.

Last refreshed: 2026-04-21 (placeholder — needs GSC export)

## Source

Export from: GSC → Performance → Pages → filter by "Valid" indexing status → download CSV → paste URLs below.

## Indexed URLs

```
(paste verified-indexed URLs here, one per line)
https://www.mejba.me/[slug-1]
https://www.mejba.me/[slug-2]
...
```

## Refresh Protocol

Re-export monthly. When Aria links to a URL in a new post:
1. If the URL appears in this file → mark `verified indexed: ✓`
2. If the URL does NOT appear → mark `verified indexed: pending` and suggest the user either (a) pick a different link target or (b) manually request indexing for this target in GSC before publishing the new post

When this file is empty or missing, Aria flags: `⚠ indexed-pages-mejba.me.md not populated — all internal-link verifications pending manual GSC export`.
