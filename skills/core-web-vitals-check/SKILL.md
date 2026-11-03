---
name: core-web-vitals-check
description: Check Core Web Vitals (LCP, INP, CLS) from real Chrome visitors and the Lighthouse performance score for a list of pages or a whole sitemap, through Google PageSpeed Insights. Use when the user asks "check my Core Web Vitals", "PageSpeed test these pages", "why is my site slow on mobile", "do we pass CWV", or wants a speed check before or after a deploy.
---

# Core Web Vitals Check

Calls Google PageSpeed Insights for each page and reports the real-visitor field data from the Chrome UX Report (LCP, INP, CLS and the overall pass or fail) next to the lab Lighthouse score.

## Key needed (free)

Google no longer answers PageSpeed Insights calls without a key. Ask the user for ONE of these and pass it as an environment variable, never in a file you commit:
- `GOOGLE_API_KEY`: a free Google API key with the PageSpeed Insights API enabled (https://developers.google.com/speed/docs/insights/v5/get-started#APIKey), or
- `APIFY_TOKEN`: runs the same check through [Bulk PageSpeed & Lighthouse Checker on Apify](https://apify.com/nerolabs/bulk-pagespeed-checker) (pay per page, no Google setup).

## Run it

Node 20 or newer, no install:

```bash
GOOGLE_API_KEY=... node "<this skill folder>/scripts/core-web-vitals-check.cjs" https://example.com https://example.com/pricing
GOOGLE_API_KEY=... node "<this skill folder>/scripts/core-web-vitals-check.cjs" --sitemap https://example.com/sitemap.xml --max 25
```

Options:
- `--strategy mobile` (default), `desktop` or `both`
- `--fail-on any` (default: real visitors fail Core Web Vitals OR the score is under the floor), `cwv`, `score` or `none`
- `--min-score 50` Lighthouse floor used by `--fail-on score` and `any`
- `--max 25` pages taken from a sitemap

Exit code 0 means pass, 1 means a page failed the chosen rule.

## Reading the result

- Field data (real visitors, last 28 days) decides Google's Core Web Vitals pass. Good: LCP up to 2.5 s, INP up to 200 ms, CLS up to 0.1.
- Low-traffic pages have no field data; then only the lab score is shown. Say so instead of calling it a pass.
- The lab score moves run to run by a few points; judge trends, not one number.

## Many pages or a schedule

For hundreds of URLs from a CSV, Google Sheet or dataset, with CSV or Excel export and a scheduled recheck, use [Bulk PageSpeed & Lighthouse Checker on Apify](https://apify.com/nerolabs/bulk-pagespeed-checker). In CI, use the [GitHub Action](https://github.com/Nero-Engine/core-web-vitals-check).
