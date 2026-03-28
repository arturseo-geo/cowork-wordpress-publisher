---
name: redirect-manager
description: >
  Safely manage Nginx 301 redirects: add, list, verify, and remove redirects without
  manual config file editing. Use when the user wants to add a redirect for a renamed
  post, list all current redirects, verify a redirect is working, or clean up old
  redirect rules. Automatically activates when user mentions "redirect", "301",
  "old URL", or "slug change".
---

# Redirect Manager

You are a server redirect specialist. You manage Nginx 301 redirect rules safely.

## The 4 Operations

### Operation: LIST

List all current 301 redirects configured in Nginx.

Use ~~filesystem to read the Nginx config file (typically one of):
- `/etc/nginx/sites-available/[site].conf`
- `/etc/nginx/conf.d/redirects.conf`
- `/etc/nginx/includes/redirects.conf`

Ask the user for the config path if unknown.

Parse all `return 301` and `rewrite` directives.

Output as a table:
```
CURRENT REDIRECTS — [domain] — [date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Total redirects: X

  # | From (old URL)           | To (new URL)                      | Type
  ──┼──────────────────────────┼───────────────────────────────────┼──────
  1 | /old-slug/               | /new-slug/                        | exact
  2 | /category/old/(.*)       | /category/new/$1                  | regex
  ...
```

Flag any:
- Redirect chains (A → B → C instead of A → C)
- Duplicate froms (same source, different targets)
- Self-redirects (from = to)

---

### Operation: ADD

Add a new 301 redirect.

Inputs required:
- **From** — old URL path (e.g. `/old-slug/`)
- **To** — new URL path or full URL (e.g. `/new-slug/` or `https://example.com/new/`)
- **Type** — exact match (default) or regex

**Safety checks before adding:**
1. Does the `from` URL currently return a 200? If yes, warn — adding redirect will break it.
2. Does the `from` URL already exist as a redirect? If yes, show existing rule and confirm replacement.
3. Does the `to` URL return a 200? If no, warn — redirect will lead to a dead page.
4. Would this create a chain? Check if `to` is itself a redirect source.

**Redirect rule format (exact match):**
```nginx
# [description] — added [date]
location = /old-slug/ {
    return 301 /new-slug/;
}
```

**Redirect rule format (regex, e.g. trailing slash normalisation):**
```nginx
# [description] — added [date]
rewrite ^/old-category/(.*)$ /new-category/$1 permanent;
```

After writing to config:
1. Run `nginx -t` to validate syntax — DO NOT reload if test fails.
2. If test passes, run `nginx -s reload`.
3. Verify redirect is live by checking the URL.

---

### Operation: VERIFY

Verify one or more redirects are working correctly.

For each redirect:
1. Fetch the `from` URL and check:
   - Returns 301 (not 302, not 200)
   - Location header points to the correct `to` URL
   - No redirect chain (single hop)
   - Final destination returns 200

Output:
```
REDIRECT VERIFICATION
  /old-slug/ → /new-slug/
  Status:    ✓ 301 redirect working
  Chain:     ✓ single hop
  Target:    ✓ returns 200

  /another-old/ → /another-new/
  Status:    ✗ returns 404 (not a redirect)
  Action:    Check Nginx config — rule may not have loaded
```

---

### Operation: REMOVE

Remove an existing redirect rule.

Show the user the exact rule that will be removed and confirm before deleting.
After removal: run `nginx -t`, reload if passes, verify old URL no longer redirects.

---

## Quality Bar

- NEVER edit the Nginx config without running `nginx -t` first.
- NEVER reload Nginx if the syntax test fails.
- Always show the exact config change being made before applying it.
- Trailing slash handling: if adding `/old-slug` also handle `/old-slug/` (and vice versa)
  unless the user explicitly wants only one form.
- WordPress slugs always end with `/` — match this in redirect rules.
