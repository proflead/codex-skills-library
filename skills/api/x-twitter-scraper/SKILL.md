---
name: x-twitter-scraper
description: Automate X/Twitter API workflows including tweet search, profile tweets, user lookup, follower export, media download, webhooks, MCP setup, and posting using Xquik. Use when a developer needs agent-ready X data or automation through the Xquik API.
---

# X Twitter Scraper

## Purpose
Use Xquik for X/Twitter API workflows from Codex.

Xquik is a hosted proprietary third-party service. It is not affiliated with or endorsed by X Corp.

## Inputs to request
- Xquik API key or OAuth setup, or confirmation that the user will provide authentication at runtime.
- Desired task: tweet search, profile tweets, user lookup, follower export, media download, webhook setup, MCP setup, or posting.
- Target handles, tweet URLs, search queries, IDs, date windows, output format, and limits.

## Workflow
1. Confirm the exact X/Twitter task and expected output.
2. Choose the smallest Xquik surface that fits: REST API, SDK, MCP server, or webhook.
3. Use placeholders for secrets and never request X login credentials.
4. Build a minimal request, SDK snippet, or MCP configuration with clear runtime variables.
5. Explain pagination, rate limits, retries, and safe storage of returned data when relevant.

## Output
- Copy-pasteable curl, SDK, or MCP setup snippet.
- Required runtime variables and placeholders.
- Response fields to inspect and next validation step.

## Quality bar
- Keep claims aligned with the public Xquik docs and repository.
- Do not describe the hosted Xquik service as open source.
- Do not expose secrets, cookies, or account login material.
- Prefer read-only examples unless the user explicitly asks for posting or other writes.
- Call out when a workflow needs an API key, enabled account, webhook endpoint, or plan support.

## References
- Xquik skill repo: https://github.com/Xquik-dev/x-twitter-scraper
- Xquik docs: https://docs.xquik.com
