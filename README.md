# AEO Audit — public demo

Search is shifting from blue links to AI answers. This audits 6 real, live
company websites for AI-engine discoverability (AEO/GEO) — would ChatGPT,
Claude, or Perplexity actually cite this page? — and scores them on the same
two-layer methodology a real AEO audit uses.

**Write-up:** https://aditya-chouhan.github.io/aeo-audit-demo/

## Methodology (two layers, 50/50)

1. **Foundational (deterministic)** — `scripts/aeo-audit.mjs` crawls each site
   (sitemap + robots.txt + internal links, respecting robots.txt) and runs 16
   pass/fail checks: title/meta quality, canonical tags, schema.org/JSON-LD,
   heading hierarchy, `llms.txt`, AI-bot access in `robots.txt`, RSS feeds,
   and more. Fully reproducible — run it yourself, get the same score.
2. **Intelligence (judged)** — the deterministic script surfaces up to 5
   representative pages per site (`pagesForReview`, with title/headings/schema/
   text excerpt). I read those actual excerpts and scored 6 content-quality
   dimensions (0-5 each: Answer Readiness, Quotability, Evidence Density,
   Content Depth, Freshness, Structural Clarity) — the way an AI agent
   deciding whether to cite a source would. Every score in `data/scores.json`
   carries a one-line rationale grounded in what was actually observed.

`final = round(0.5 x foundational + 0.5 x intelligenceScore)`, mapped to an A-F grade.

## Run it yourself

```
node scripts/aeo-audit.mjs https://example.com --max-pages=8 --out=report.json
```

No npm install, no API keys — Node 18+ only (uses global `fetch`). Requires
nothing beyond internet access.

## What's in it

- `scripts/aeo-audit.mjs` — the deterministic crawler/scorer. **Not written by
  me** — this is [OnVoyage AI's `audit-website-aeo` skill](https://github.com/onvoyage-ai/gtm-engineer-skills/tree/main/audit-website-aeo)
  (MIT license, included below), used as-is. My contribution is running it
  against 6 real sites, judging the intelligence layer myself, and building
  the comparison.
- `data/*.json` — the 6 raw audit reports, one real API-free crawl per site.
- `data/scores.json` — my intelligence-layer scoring with rationale, plus
  each site's final grade and key finding.
- `index.html` — the write-up.

## Sites audited

Anthropic, Linear, Vercel, Notion, Ramp, Stripe — a mix of AI-native dev
tools, an AI safety/research company, and a fintech, chosen for name
recognition and to see whether "AI-native" companies actually practice AEO
on their own marketing sites. (HubSpot was attempted and excluded: its
homepage crawl returned 0 pages under the script's own bot-detection — see
`data/scores.json` methodology note. Reporting the failure honestly rather
than swapping it out silently.)

**Scale note:** 6 sites, 8 pages each, is a demo-scale sample — enough to
show the methodology and produce real, defensible findings, not a market
survey.

## Real finding

Ramp — a fintech, not an AI-native dev tool — scores highest (81, B+) of the
6, beating Anthropic itself (68, C+). Ramp's homepage serves crawlers a
literal hand-built "Machine Version" markdown summary, and its blog runs
dated, authored, data-backed vendor-comparison articles. Notion — an
AI-forward product company — scores lowest (38, F): its marketing site leans
on embedded video, so an AI agent reading the page gets ad-blocker warnings
and video-player button labels instead of product substance. Full
site-by-site rationale in `data/scores.json`.

---

### Third-party script license (MIT)

```
MIT License

Copyright (c) 2025 OnVoyage AI

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```

Aditya Chouhan · ai.adityachouhan@gmail.com
