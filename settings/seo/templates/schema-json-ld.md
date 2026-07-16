# JSON-LD Schema Templates — Aria Reference

Aria reads this file on demand to emit the correct schema block after each article. Emit the block as an HTML comment wrapping the JSON so it doesn't render in markdown preview but CMS parsers can extract it.

## Emission Format

Wrap the entire `@graph` JSON inside:

```html
<!-- SCHEMA_JSON_LD
{ ... JSON-LD ... }
SCHEMA_JSON_LD -->
```

## Base Template — Article + BreadcrumbList (always required)

```json
{
  "@context": "https://schema.org",
  "@graph": [
    {
      "@type": "Article",
      "headline": "[TITLE]",
      "description": "[META DESCRIPTION]",
      "image": "https://www.[brand]/images/blog/[slug]-featured.jpg",
      "datePublished": "[published_at]",
      "dateModified": "[updated_at]",
      "author": {
        "@type": "Person",
        "name": "Engr Mejba Ahmed",
        "url": "https://www.mejba.me"
      },
      "publisher": {
        "@type": "Organization",
        "name": "[brand org name]",
        "logo": {
          "@type": "ImageObject",
          "url": "https://www.[brand]/images/logo.png"
        }
      },
      "mainEntityOfPage": {
        "@type": "WebPage",
        "@id": "[canonical_url]"
      },
      "keywords": "[comma-separated keywords]",
      "articleSection": "[category]",
      "wordCount": [word_count],
      "inLanguage": "en-US"
    },
    {
      "@type": "BreadcrumbList",
      "itemListElement": [
        { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://www.[brand]/" },
        { "@type": "ListItem", "position": 2, "name": "Blog", "item": "https://www.[brand]/blog" },
        { "@type": "ListItem", "position": 3, "name": "[TITLE]", "item": "[canonical_url]" }
      ]
    }
  ]
}
```

## Optional Node: FAQPage (append to `@graph` when FAQ module exists)

```json
{
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "[FAQ question 1 — verbatim from the FAQ module]",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "[FAQ answer 1 — first sentence or full short answer]"
      }
    }
  ]
}
```

**Rule:** Mirror EVERY FAQ question. Do not truncate.

## Optional Node: HowTo (append for tutorial content — Type 1 Deep Dive with numbered steps)

```json
{
  "@type": "HowTo",
  "name": "[TITLE]",
  "description": "[META DESCRIPTION]",
  "totalTime": "PT[N]M",
  "step": [
    {
      "@type": "HowToStep",
      "position": 1,
      "name": "[Step 1 short name]",
      "text": "[Step 1 description — mirror the Implementation phase step text]"
    }
  ]
}
```

**Rule:** Mirror steps from the Implementation phase. `totalTime` in ISO 8601 duration (`PT30M` = 30 minutes).

## Optional Node: Review (append for practitioner reviews — Content Type 2)

```json
{
  "@type": "Review",
  "itemReviewed": {
    "@type": "[SoftwareApplication | Product | Service]",
    "name": "[tool/product name]"
  },
  "reviewRating": {
    "@type": "Rating",
    "ratingValue": "[1-5]",
    "bestRating": "5",
    "worstRating": "1"
  },
  "author": {
    "@type": "Person",
    "name": "Engr Mejba Ahmed"
  },
  "reviewBody": "[2-3 sentence honest summary — can reuse The Verdict section]"
}
```

## Publisher Names Per Brand

| Brand | Publisher Name |
|---|---|
| mejba.me | Engr Mejba Ahmed |
| ramlit.com | Ramlit Limited |
| colorpark.io | ColorPark |
| xcybersecurity.io | xCyberSecurity |

## Schema Rules

- All URLs absolute HTTPS with correct brand domain
- `datePublished` + `dateModified` must match frontmatter `published_at` + `updated_at`
- `wordCount` = actual article body count (exclude frontmatter, schema, social package, crawl package)
- `keywords` = comma-joined frontmatter `keywords` array
- `articleSection` = frontmatter `category`
- `inLanguage` = `en-US`
- Validate final JSON is parseable — no trailing commas, balanced braces
