# Project Context — cowork-wordpress-publisher

## Purpose
WordPress Publisher Cowork Plugin for Claude Cowork. Packages domain-specific skills, commands, and an autonomous agent into a single installable plugin.

## Architecture
- **Skills (5):** post-deploy, image-seo, post-audit, redirect-manager, site-health
- **Commands (5):** /wp:deploy, /wp:image-seo, /wp:audit, /wp:redirect, /wp:health
- **Agent:** full-publish

## Design Decisions
- Skills contain the domain knowledge and step-by-step instructions
- Commands are lightweight entry points that invoke the right skill
- The agent chains multiple skills into an autonomous workflow
- MCP connectors declared in .mcp.json (user configures credentials)

## Installation
Cowork → Customize → Browse Plugins → Upload → select ZIP.

## Author
Artur Ferreira / The GEO Lab (thegeolab.net)
