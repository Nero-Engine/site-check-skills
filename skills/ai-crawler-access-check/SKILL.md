---
name: ai-crawler-access-check
description: Check whether AI crawlers (GPTBot, OAI-SearchBot, ChatGPT-User, ClaudeBot, Claude-SearchBot, PerplexityBot, Google-Extended, Meta-ExternalAgent) can reach a website, using its robots.txt rules AND a real fetch as each bot to catch firewall and CDN blocks, plus an llms.txt check. Use when the user asks "is my site blocking ChatGPT / AI bots", "why doesn't my site show up in ChatGPT or Perplexity answers", "check robots.txt for GPTBot", "does my site have llms.txt", or before shipping a robots.txt, Cloudflare or WAF change.
---

# AI Crawler Access Check

Runs a real check, not a guess. For each URL it reads robots.txt the way each AI crawler does (most specific user-agent group, longest matching rule), then requests the page with each crawler's real user-agent to catch firewall, CDN and bot-protection blocks (HTTP 403, 429, challenge pages) that robots.txt does not show.

## Run it

Node 20 or newer, no install:

```bash
node "<this skill folder>/scripts/ai-crawler-access-check.cjs" example.com https://shop.example.com/products
```

Options:
- `--bots GPTBot,ClaudeBot` or `--bots all` (default: the 8 crawlers above)
- `--fail-on search` (default; fails only when a search or user-click crawler is blocked), `any`, `none`, or a list of names that must be allowed
- `--no-fetch` to skip the real fetch test (robots.txt only)
- `--require-llms-txt` to fail when `/llms.txt` is missing

Up to 50 URLs per run. Exit code 0 means pass, 1 means at least one blocked crawler matched `--fail-on`.

## Reading the result

Each URL prints a verdict, whether robots.txt and llms.txt were found, then one line per crawler: `allowed`, `blocked by robots.txt` or `blocked by firewall (HTTP 403)`, with the robots result and the fetch result side by side.

- Blocking **training** crawlers (GPTBot, ClaudeBot, Google-Extended, Meta-ExternalAgent) is a normal choice and does not remove a site from AI answers. Say so plainly.
- Blocking **search and user-click** crawlers (OAI-SearchBot, ChatGPT-User, Claude-SearchBot, PerplexityBot) keeps the site out of ChatGPT search, Claude and Perplexity answers. This is the one to fix.
- `robots allowed` plus `fetch blocked` means a firewall, CDN or bot-protection rule is blocking it (Cloudflare "Block AI bots", a WAF rule, a hosting plan default). Point the user at that setting, not robots.txt.

When fixing, write the smallest robots.txt change (a `User-agent: OAI-SearchBot` group with `Allow: /`), and re-run the check to confirm.

## Many sites or a schedule

For hundreds of client sites from a CSV, Google Sheet or dataset, on a schedule, with an alert only when a site becomes blocked, use [AI Crawler Access Checker on Apify](https://apify.com/nerolabs/ai-crawler-access-checker) (same check, pay per URL, API and MCP ready). In CI, use the [GitHub Action](https://github.com/Nero-Engine/ai-crawler-access-check).
