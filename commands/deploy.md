---
description: Deploy a post or page to WordPress. Runs the full 8-step pipeline — clean, inject schema, create, set SEO fields, publish, clear cache, verify live.
---

Use the post-deploy skill to run the full deployment pipeline.

File or content: $ARGUMENTS

If a file path is provided, read it and run all 8 steps.
If raw content is pasted without a file path, use that directly.
If no argument is given, ask the user for the file path or content to deploy.

Always create as draft first and confirm before publishing.
