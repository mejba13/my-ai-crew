# SEO Settings — Reference Files for Aria

This directory is the source of truth for per-brand SEO configuration that Aria reads before writing any post.

## Files

| File | Purpose |
|---|---|
| `top-keywords-[brand].md` | Prioritized keyword list (seeded from GSC). Aria biases primary keyword selection toward these. |
| `audience-personas.md` | Per-brand reader personas (goals, pain points, technical depth). |
| `competitor-gaps.md` | Keyword gaps vs competitors — angles that competitors miss. |
| `content-calendar.md` | Planned topics, pillar/cluster mapping, freshness schedule. |
| `indexed-pages-[brand].md` | Verified-indexed URL list per brand. Aria uses this to validate internal-link targets in the Crawl Acceleration Package. |

## Maintenance

- **`indexed-pages-[brand].md`:** repopulate monthly from GSC Pages report (Performance → Pages → export indexed URLs). Aria reads this to flag internal links pointing to not-yet-indexed pages.
- **`top-keywords-[brand].md`:** refresh quarterly from GSC Queries report. Prioritize queries with high impressions but position > 10 (rankable if content is sharper).
- **`competitor-gaps.md`:** refresh when running a new competitive analysis (Ahrefs, SEMrush, or manual SERP audit).
- **`content-calendar.md`:** update weekly with planned pieces.

## How Aria uses these files

1. **Step 3 Keyword Planning:** Aria reads `top-keywords-[brand].md` and biases primary keyword selection toward listed phrases.
2. **Step 3 Reader Profile:** Aria reads `audience-personas.md` for the target brand.
3. **Step 3 Competitive Differentiation:** Aria reads `competitor-gaps.md` for angle inspiration.
4. **Crawl Acceleration Package:** Aria reads `indexed-pages-[brand].md` to verify internal-link targets. Flags unverified links as `⚠ pending`.

If a file is missing, Aria proceeds without it and flags the gap in the delivered package. No file = soft warning, not a hard stop.
