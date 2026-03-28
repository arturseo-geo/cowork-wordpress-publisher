---
description: Manage Nginx 301 redirects. Add, list, verify, or remove redirect rules safely with syntax validation before any reload.
---

Use the redirect-manager skill.

Arguments: $ARGUMENTS

Supported formats:
  /wp:redirect list                        — show all current redirects
  /wp:redirect add /old-slug /new-slug     — add a new 301 redirect
  /wp:redirect verify /old-slug            — verify a redirect is working
  /wp:redirect remove /old-slug            — remove a redirect rule

If no arguments, ask what the user wants to do (list / add / verify / remove).
Always run nginx -t before any reload.
