# Site Check Skills for Claude Code and other AI agents

**Three website checks your AI coding agent can actually run, instead of guessing from memory.** Each skill ships a tested, zero-dependency Node script (Node 20+), so the agent gets real answers from the live site, the domain registry and Google.

| Skill | What it checks | Ask your agent |
|---|---|---|
| [`ai-crawler-access-check`](skills/ai-crawler-access-check/SKILL.md) | Whether GPTBot, OAI-SearchBot, ChatGPT-User, ClaudeBot, Claude-SearchBot, PerplexityBot, Google-Extended and Meta-ExternalAgent can reach the site: robots.txt rules **plus a real fetch as each bot** (catches Cloudflare, CDN and firewall blocks), and llms.txt | "Is my site blocking ChatGPT search?" |
| [`domain-expiry-check`](skills/domain-expiry-check/SKILL.md) | Domain registration expiry from the registry's own **RDAP** record, TLS certificate expiry and trust, hold and redemption status, up to 200 hosts | "When do our domains and SSL certificates expire?" |
| [`core-web-vitals-check`](skills/core-web-vitals-check/SKILL.md) | **Real-visitor** Core Web Vitals (LCP, INP, CLS) and the Lighthouse score for a page list or a sitemap, through Google PageSpeed Insights | "Do our top pages pass Core Web Vitals on mobile?" |

## Install

**Claude Code** (plugin marketplace):

```
/plugin marketplace add Nero-Engine/site-check-skills
/plugin install site-check-skills@nero-labs
```

**Any agent that reads Agent Skills** (`SKILL.md` folders), with the open `skills` installer:

```bash
npx skills add Nero-Engine/site-check-skills
```

**By hand:** copy a folder from `skills/` into `~/.claude/skills/` (or your agent's skills folder).

**Without an agent:** every script is a normal CLI.

```bash
node skills/ai-crawler-access-check/scripts/ai-crawler-access-check.cjs example.com
node skills/domain-expiry-check/scripts/domain-expiry-check.cjs example.com example.org
GOOGLE_API_KEY=... node skills/core-web-vitals-check/scripts/core-web-vitals-check.cjs https://example.com
```

Exit code 0 is a pass and 1 a fail, so the scripts also work in cron jobs and CI.

## Why scripts, not just instructions

Most SEO skills tell the agent what to look for and leave it to improvise with curl. These run the checks the same way every time:

- **Robots.txt the way each crawler reads it** (the most specific user-agent group, longest matching rule), and a real request with each bot's user-agent, because a site can allow GPTBot in robots.txt and still return 403 to it from the firewall.
- **RDAP, not WHOIS scraping.** The registry's official record for each domain ending, plus a live TLS handshake for the certificate. Certificates get their own 14 day window, since Let's Encrypt renews at 30 days left.
- **Field data first.** Google's Core Web Vitals pass is decided by real Chrome visitors, not the lab score, so both are reported and pages without field data are labelled as such.

Nothing is sent anywhere except the site being checked, the registry, and Google (for the PageSpeed check, with the user's own key).

## Many sites, schedules and alerts

The skills are free and cover one site or a short list. For hundreds of sites from a CSV, Google Sheet or dataset, on a schedule, with exports and change alerts, the same checks run on Apify, pay per result:

- [AI Crawler Access Checker](https://apify.com/nerolabs/ai-crawler-access-checker)
- [Domain WHOIS & Age Checker](https://apify.com/nerolabs/domain-whois-checker)
- [Bulk PageSpeed & Lighthouse Checker](https://apify.com/nerolabs/bulk-pagespeed-checker)

In CI, use the matching GitHub Actions: [ai-crawler-access-check](https://github.com/Nero-Engine/ai-crawler-access-check), [domain-expiry-check](https://github.com/Nero-Engine/domain-expiry-check), [core-web-vitals-check](https://github.com/Nero-Engine/core-web-vitals-check).

## Licence

MIT. Made by [Nero Labs](https://apify.com/nerolabs).
