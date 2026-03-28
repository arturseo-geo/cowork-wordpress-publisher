# WordPress Publisher Plugin for Claude Cowork

**The complete WordPress publishing pipeline — deploy, optimise, audit, and manage your site from natural language.**

Built by [Artur Ferreira](https://thegeolab.net) | The GEO Lab

---

## What This Plugin Does

Every WordPress content team repeats the same 8-step deployment process for every post.
This plugin packages that entire workflow — from raw HTML to verified live URL — into
a single Cowork session. No dashboard switching. No manual steps.

No other plugin in the Claude directory covers WordPress publishing. This is the first.

---

## Skills (auto-activate when relevant)

| Skill | Activates When |
|-------|---------------|
| `post-deploy` | You mention deploying, publishing, or reference an HTML file to go live |
| `image-seo` | You reference an image file and WordPress in the same context |
| `post-audit` | You ask to check, review, or audit a post before or after publishing |
| `redirect-manager` | You mention a redirect, 301, old URL, or slug change |
| `site-health` | You ask about site performance, errors, or want a health check |

---

## Commands

| Command | What It Does |
|---------|-------------|
| `/wp:deploy [file.html]` | Full 8-step deployment pipeline — draft → audit → publish → verify |
| `/wp:image-seo [image.png] [post-id]` | Optimise → WebP → upload → set all 4 metadata fields |
| `/wp:audit [url or post-id]` | 28-check post compliance audit with deviation map |
| `/wp:redirect add /old /new` | Safely add a 301 redirect with Nginx validation |
| `/wp:redirect list` | Show all current redirects |
| `/wp:redirect verify /old-slug` | Verify a redirect is working correctly |
| `/wp:health` | Full site health check — plugins, cache, SSL, broken links, backups |

---

## Agent

`full-publish` — Autonomous end-to-end publishing. Audit → process images → deploy →
verify live → log to Notion. One command, zero dashboard switching.

Invoke: *"Publish this post end to end"* or *"Handle everything for [file.html]"*

---

## The 8-Step Deploy Pipeline

1. Read & validate source file
2. Extract clean content + strip wrapper HTML
3. Inject or validate JSON-LD schema
4. Create WordPress draft via REST API
5. Assign categories, tags + set RankMath SEO fields
6. Upload and set featured image
7. Publish + clear Nginx FastCGI cache
8. Verify live URL + submit to GSC for indexing

---

## Required Connectors

| Connector | Used For |
|-----------|---------|
| WordPress | Posts, pages, media, SEO fields, cache |
| Filesystem | Read local HTML and image files |
| Google Search Console *(optional)* | Submit URL for indexing after publish |
| Puppeteer/Browser *(optional)* | Screenshot verification of live post |
| Notion *(optional)* | Publishing log and content database |

---

## Install

```bash
# Add the marketplace
claude plugin marketplace add arturseo-geo/wordpress-publisher-plugin

# Install the plugin
claude plugin install wordpress-publisher@wordpress-publisher-plugin
```

Or install directly from Cowork → Customize → Browse Plugins.

---

## About

This plugin is extracted from a production WordPress publishing workflow running at
[thegeolab.net](https://thegeolab.net) — 43 posts/pages, 112 WebP-optimised images,
12+ Nginx redirects, and a 28-check compliance audit system.

Everything in this plugin is derived from real daily use, not theory.

---

MIT License · [thegeolab.net](https://thegeolab.net) · [@TheGEO_Lab](https://x.com/TheGEO_Lab)
