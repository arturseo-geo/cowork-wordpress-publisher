---
description: Run the 28-check post compliance audit on any WordPress post or HTML file. Returns a compliance score, deviation map, and prioritised fix list.
---

Use the post-audit skill to run a full compliance check.

Target: $ARGUMENTS

If a URL or post ID is provided, fetch from WordPress and audit.
If an HTML file path is provided, audit the file directly.
If no argument, ask the user for the post URL, post ID, or file to audit.

Return the full compliance report with all 28 checks.
