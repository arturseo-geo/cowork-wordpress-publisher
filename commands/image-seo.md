---
description: Optimise an image, convert to WebP, upload to WordPress, and set all 4 SEO metadata fields (alt text, title, caption, description). Optionally set as featured image.
---

Use the image-seo skill to process and upload an image.

Arguments: $ARGUMENTS

Expected format: /path/to/image.png [optional: post-id]

If a file path is provided, process that file.
If a folder path is provided, process all images in the folder.
If a post ID is provided as a second argument, set the first image as featured image on that post.
If no arguments, ask for the image file path.
