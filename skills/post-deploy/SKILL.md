---
name: post-deploy
description: >
  Full 8-step WordPress post deployment pipeline. Use when the user wants to publish
  a new post or page from an HTML file or raw content. Handles: HTML stripping,
  JSON-LD injection, category/tag assignment, RankMath SEO fields, featured image,
  cache clear, and live URL verification. Automatically activates when user says
  "deploy", "publish", or references an HTML file to go live.
---

# Post Deploy Pipeline

You are a WordPress deployment specialist. You take content from draft to live in 8 steps.

## The 8-Step Pipeline

### Step 1 — Read & Validate Source File

Read the source file via ~~filesystem (or from paste if no file path given).

Validate:
- [ ] File is valid HTML (not broken/truncated)
- [ ] H1 present (exactly one)
- [ ] At least one image present
- [ ] Word count > 300
- [ ] No placeholder text (TODO, LOREM IPSUM, [KEYWORD], etc.)

If any check fails, STOP and report the issue. Do not proceed until resolved.

### Step 2 — Extract Clean Content

Strip wrapper HTML — remove `<html>`, `<head>`, `<body>` tags.
Keep inner content: headings, paragraphs, images, lists, tables, blockquotes.
Preserve any existing JSON-LD `<script>` blocks if present.

Extract metadata from content:
- **Title** — from H1
- **Slug** — lowercase H1, hyphens, no stop words, max 6 words
- **Excerpt** — first paragraph, trimmed to 155 characters
- **Focus keyword** — first prominent keyword phrase from H1/intro

### Step 3 — JSON-LD Injection

Check if JSON-LD is already present. If yes, validate it (see schema-optimization skill).
If no JSON-LD present, generate Article schema minimum:

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "[H1 text]",
  "author": {
    "@type": "Person",
    "name": "[site author from WP settings]",
    "url": "[author URL]"
  },
  "publisher": {
    "@type": "Organization",
    "name": "[site name from WP settings]",
    "url": "[site URL]"
  },
  "datePublished": "[today ISO 8601]",
  "dateModified": "[today ISO 8601]",
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "[expected URL]"
  }
}
```

Append to content before closing body (or inject via wp_head if WordPress MCP supports it).

### Step 4 — Create WordPress Post

Use ~~wordpress to create the post:

```
POST /wp/v2/posts
{
  title: [extracted title],
  content: [clean HTML content],
  slug: [extracted slug],
  status: "draft",     ← always draft first
  excerpt: [extracted excerpt],
  format: "standard"
}
```

Record the returned `post_id` and `draft_url`.

### Step 5 — Assign Categories, Tags & RankMath SEO

**Categories & Tags:**
- Use ~~wordpress to list existing categories/tags
- Match content topics to existing taxonomy terms
- Create new terms only if no match exists (confirm with user first)
- Assign via PATCH to the post

**RankMath SEO fields** (via WordPress custom fields or RankMath REST endpoint):
```
rank_math_focus_keyword: [focus keyword]
rank_math_description:   [155-char meta description from excerpt]
rank_math_title:         [SEO title = H1 + " | " + site name, max 60 chars]
rank_math_canonical_url: [full expected URL]
```

### Step 6 — Set Featured Image

Check if content has a hero/first image:
- If the image is already uploaded to WordPress media library → set as featured image
- If image is a local file path → upload first via ~~filesystem + ~~wordpress media endpoint,
  then set as featured image
- If no image found → warn user, do not block deployment

Set `featured_media` on the post.

### Step 7 — Publish & Cache Clear

Change post status from `draft` to `publish`:
```
PATCH /wp/v2/posts/{post_id}
{ "status": "publish" }
```

Record the live `link` URL from the response.

Clear cache via ~~wordpress (if cache-clear endpoint available) or note that manual cache
clear may be needed (Nginx FastCGI cache, WP Rocket, etc.).

### Step 8 — Verify Live Post

Use ~~browser to take a screenshot of the live URL (confirm visual rendering).
Use ~~search_console to submit the URL for indexing (URL Inspection → Request Indexing).

Final verification checklist:
- [ ] Post is live and returns 200
- [ ] Title matches H1
- [ ] Featured image visible
- [ ] JSON-LD present in page source
- [ ] No redirect loop

## Output

```
DEPLOYMENT REPORT — [post title] — [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

STATUS: ✓ LIVE

Live URL:       [url]
Post ID:        [id]
Word count:     [count]
Categories:     [list]
Tags:           [list]
Featured image: [filename]
Schema types:   [list]
Cache cleared:  [yes/no]
GSC submitted:  [yes/no]

STEPS COMPLETED
  ✓ Step 1 — Source validated
  ✓ Step 2 — Content extracted
  ✓ Step 3 — JSON-LD injected ([types])
  ✓ Step 4 — Post created (draft ID: [id])
  ✓ Step 5 — SEO fields set
  ✓ Step 6 — Featured image set
  ✓ Step 7 — Published + cache cleared
  ✓ Step 8 — Live verification passed

[screenshot attached if browser tool available]
```

If any step fails, report clearly which step and why. Do not silently skip steps.

## Quality Bar

- Always create as draft first — never publish directly without draft review option.
- Never overwrite an existing post without confirming post_id with the user.
- Slug must not duplicate an existing slug — check via ~~wordpress before creating.
- Meta description must be 140–160 chars. Trim or pad if needed.
