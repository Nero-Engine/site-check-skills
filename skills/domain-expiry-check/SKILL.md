---
name: domain-expiry-check
description: Check when domains and their TLS/SSL certificates expire, using the registry's own RDAP record (no WHOIS scraping) and a live TLS handshake. Flags domains in hold, redemption or lapsed status and broken certificates (self-signed, wrong host, missing intermediate, expired). Use when the user asks "when does my domain expire", "check SSL certificate expiry", "is my cert about to expire", "audit our domains", or wants a list of client or brand domains checked.
---

# Domain Expiry Check

Reads the domain's registration expiry from the official registry RDAP service for its ending (the record ICANN requires registries to publish), and opens a TLS connection to read the live certificate's expiry and trust.

## Run it

Node 20 or newer, no install:

```bash
node "<this skill folder>/scripts/domain-expiry-check.cjs" example.com example.org shop.example.net
node "<this skill folder>/scripts/domain-expiry-check.cjs" --file domains.txt
```

Options:
- `--check both` (default), `domain` or `ssl`
- `--fail-within-days 30` domain window (default 30), `--ssl-fail-within-days 14` certificate window (default 14; Let's Encrypt renews at 30 days left, so a short window avoids false alarms)
- `--warn-within-days 60`
- `--no-fail-on-problems` to report status problems (hold, redemption, bad certificate) without failing
- `--port 443`

Up to 200 hosts. Exit code 0 means pass, 1 means something expires inside its window or has a problem.

## Reading the result

One line per host: days left on the domain, days left on the certificate, then the host. Problems are named in plain words (for example `redemptionPeriod`, `self-signed certificate`, `certificate for a different host`).

Notes for the user:
- `.uk` domains are skipped for the registration date (Nominet's terms); the certificate is still checked.
- Shared platform hosts (github.io, workers.dev, vercel.app and similar) get a certificate check only, since the user does not own the parent domain.
- A domain with auto-renew on can still lapse if the card on file fails: a short window is the cheap insurance.

## Many domains or a schedule

For a portfolio of client or brand domains in a CSV, Google Sheet or spreadsheet, filtered and exported as CSV or Excel, on a schedule, use [Domain WHOIS & Age Checker on Apify](https://apify.com/nerolabs/domain-whois-checker). In CI, a weekly [GitHub Action](https://github.com/Nero-Engine/domain-expiry-check) run emails the repo owner when something is close.
