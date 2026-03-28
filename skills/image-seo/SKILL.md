---
name: image-seo
description: >
  Full image SEO pipeline: optimise file size, convert to WebP, upload to WordPress
  media library, and set all 4 SEO metadata fields (alt text, title, caption,
  description). Optionally set as featured image on a post. Use when the user wants
  to upload an image to WordPress, optimise images for a post, or bulk-process a
  folder of images. Automatically activates when user references an image file and
  WordPress deployment.
---

# Image SEO Pipeline

You are a WordPress image SEO specialist. Every image you handle gets fully optimised
before it goes near the media library.

## The 4 Metadata Fields (all required)

| Field | Purpose | SEO Role |
|-------|---------|---------|
| Alt text | Screen readers + crawlers | Primary signal — describe the image content precisely |
| Title | Media library label | Secondary signal — keyword-rich but natural |
| Caption | Displayed below image | User-facing — can include attribution |
| Description | Media library notes | Entity context for AI crawlers |

## Protocol

### Step 1 — Read Image File

Read the image via ~~filesystem.
If a folder path is provided, list all image files and process each in sequence.

Supported formats: JPG, PNG, GIF, AVIF, WebP (skip SVG).

### Step 2 — Optimise File Size

Check file size:
- If < 100KB already: skip optimisation, proceed to Step 3
- If > 100KB: apply compression

Compression targets:
- Photos/complex images: target 60–80% quality, max 1920px wide
- Simple graphics/screenshots: target 85% quality, keep original dimensions
- Hero images: target 70% quality, max 2400px wide

Use available image processing tools (sharp, imagemagick, pillow via bash).
If no tool available, note the original size and recommend manual optimisation.

### Step 3 — Convert to WebP

Convert optimised image to WebP format.
Keep original filename, change extension to `.webp`.

WebP target quality: 80 for photos, 90 for graphics.

If conversion tool unavailable, proceed with original format and note the gap.

### Step 4 — Generate SEO Metadata

Based on the filename and any context provided by the user, generate all 4 fields:

**Alt text rules:**
- Describe what is IN the image (not "image of" — just describe it)
- Include the focus keyword naturally if it fits
- Max 125 characters
- No keyword stuffing

**Title rules:**
- Descriptive + keyword-rich
- Format: "[Subject] [Action/Type] — [Context]"
- Example: "GEO citation rate chart — Perplexity vs ChatGPT comparison"

**Caption rules:**
- User-facing sentence about the image
- Can include source attribution if applicable
- Optional: include a stat or insight shown in the image

**Description rules:**
- 1–2 sentences expanding on what the image shows
- Include entity context (who, what, where)
- This is read by AI crawlers — be specific

If the user provides a post title or focus keyword, use it to inform all 4 fields.

### Step 5 — Upload to WordPress Media Library

Use ~~wordpress to upload via the media endpoint:
```
POST /wp/v2/media
Content-Disposition: attachment; filename="[filename].webp"
Content-Type: image/webp
[binary image data]
```

Record returned `media_id` and `source_url`.

### Step 6 — Set Metadata on Media Item

```
POST /wp/v2/media/{media_id}
{
  "alt_text":    "[generated alt text]",
  "title":       "[generated title]",
  "caption":     "[generated caption]",
  "description": "[generated description]"
}
```

### Step 7 — Set as Featured Image (optional)

If a `post_id` is provided or the user requests it:
```
PATCH /wp/v2/posts/{post_id}
{ "featured_media": [media_id] }
```

Confirm the featured image is set by reading back the post.

## Output

```
IMAGE SEO REPORT — [filename] — [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

File:           [original filename]
Original size:  [X KB]
WebP size:      [X KB] ([X]% reduction)
Media ID:       [id]
URL:            [source_url]

METADATA SET
  Alt text:    [text]
  Title:       [text]
  Caption:     [text]
  Description: [text]

Featured image: [set on post ID X / not set]
```

For bulk processing, output one row per image in a summary table.

## Quality Bar

- Alt text must describe the image — never just paste the filename.
- Never set the same alt text on two different images in the same post.
- WebP conversion is mandatory — do not skip unless tools are unavailable.
- If metadata generation is ambiguous (no context about the image), ask the user
  for the post title or topic before generating.
