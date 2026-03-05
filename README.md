# SEO & SEM Audit Tool

AI-powered SEO auditor that crawls a URL, analyzes it using Claude or Gemini, and generates a professional `.docx` report. Uses a **fixed scoring rubric** so you can rerun audits after site improvements and accurately track your progress over time.

---

## Features

- Crawls a live page and extracts 20+ technical and content signals
- AI analyzes findings against a **fixed checklist** (16 Technical + 14 Content + 11 SEM checks)
- Scores are **computed from fixed weights** — not assigned by AI — so they're consistent every run
- **Progress tracking**: saves a history file per domain and shows score deltas vs your last run
- Generates a formatted `.docx` report with tables, color-coded PASS/WARN/FAIL status, and actionable recommendations
- Supports both **Claude** (Anthropic) and **Gemini** (Google) as the AI provider

---

## Requirements

- Python 3.9+
- An API key for Claude or Gemini (see [Getting API Keys](#getting-api-keys))

---

## Installation

**1. Clone or download the script**

```bash
git clone <your-repo-url>
cd py
```

**2. Create a virtual environment (recommended)**

```bash
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows
```

**3. Install dependencies**

```bash
pip install requests beautifulsoup4 python-docx anthropic google-genai lxml python-dotenv
```

**4. Configure your environment**

Copy `.env.example` to `.env` and fill in your API key(s):

```bash
cp .env.example .env
```

Edit `.env`:

```ini
ANTHROPIC_API_KEY=sk-ant-api03-...   # your Claude key
GEMINI_API_KEY=AIzaSy...             # your Gemini key

CLAUDE_MODEL=claude-sonnet-4-20250514
GEMINI_MODEL=gemini-2.5-pro-preview-03-25
```

You only need one key — whichever provider you prefer.

---

## Getting API Keys

| Provider | URL | Notes |
|---|---|---|
| Claude (Anthropic) | https://console.anthropic.com/ | Key starts with `sk-ant-` |
| Gemini (Google) | https://aistudio.google.com/apikey | Key starts with `AIzaSy` |

---

## Usage

### Basic — auto-detect provider from `.env`

```bash
python seo_audit.py https://example.com
```

### Specify provider explicitly

```bash
# Use Claude
python seo_audit.py https://example.com --provider claude

# Use Gemini
python seo_audit.py https://example.com --provider gemini
```

### Pass API key inline (overrides .env)

```bash
python seo_audit.py https://example.com --api-key sk-ant-api03-...
```

### Custom output filename

```bash
python seo_audit.py https://example.com --output my-report.docx
```

### Crawl only — no AI, outputs raw JSON (free, no API key needed)

```bash
python seo_audit.py https://example.com --crawl-only
```

---

## All Options

```
usage: seo_audit.py [-h] [--output OUTPUT] [--api-key KEY] [--provider {claude,gemini,auto}] [--crawl-only] url

positional arguments:
  url                         URL to audit (http/https, or bare domain)

optional arguments:
  -o, --output OUTPUT         Output .docx file path (default: auto-generated from domain + date)
  -k, --api-key KEY           API key — Claude (sk-ant-...) or Gemini (AIzaSy...)
  -p, --provider              AI provider: claude, gemini, or auto (default: auto)
      --crawl-only            Only crawl the page and save raw data as JSON, skip AI analysis
```

---

## Output Files

| File | Description |
|---|---|
| `SEO-Report-<domain>-<date>.docx` | Full formatted report |
| `seo-history-<domain>.json` | History of all past runs for this domain (used for score tracking) |
| `SEO-Report-<domain>-<date>.json` | Raw crawl data (only created with `--crawl-only`) |

---

## Report Structure

The `.docx` report contains:

1. **Executive Summary** — key strengths and opportunities specific to the site
2. **Overall Scores Table** — Technical / Content / SEM scores with grade, and delta vs previous run
3. **Technical SEO Review** — 16-check table with PASS/WARN/FAIL status and recommendations
4. **Content SEO Review** — 14-check table with findings and recommendations
5. **Google Ads / SEM Review** — 11-check table + recommended ad groups and campaign strategy
6. **Quick Wins** — top actions ranked by impact vs effort

---

## Scoring System

Scores are deterministic — the same site in the same state will always get the same score.

| Status | Score value |
|---|---|
| PASS | 100% of check weight |
| WARN | 50% of check weight |
| FAIL | 0% of check weight |

Section weights (overall score):
- Technical SEO: 40%
- Content SEO: 35%
- SEM Readiness: 25%

| Score | Grade |
|---|---|
| 90–100 | A |
| 85–89 | A- |
| 80–84 | B+ |
| 70–79 | B |
| 60–69 | C |
| 50–59 | D |
| 0–49 | F |

---

## Fixed Checklist

### Technical SEO (16 checks)

| Check | Weight | What is evaluated |
|---|---|---|
| HTTPS / SSL | 8 | Site uses HTTPS, no SSL errors |
| Indexability | 8 | No noindex in meta robots |
| Page Speed | 7 | Server response time (ms) |
| Mobile-Friendly | 7 | Viewport meta tag, responsive signals |
| Canonical Tag | 6 | Canonical URL set correctly |
| Structured Data | 6 | JSON-LD schema present |
| Mixed Content | 6 | No HTTP resources on HTTPS page |
| Image Optimization | 6 | WebP format, dimensions set (CLS prevention) |
| HTTP Security Headers | 5 | X-Frame-Options, CSP, HSTS |
| Internal Linking | 5 | Internal link count and structure |
| Duplicate Content | 5 | Repeated paragraphs detected |
| Lazy Loading | 4 | Images use `loading="lazy"` |
| Redirect Handling | 4 | Correct HTTP→HTTPS, www→non-www |
| Robots.txt | 4 | File exists, sitemap referenced |
| XML Sitemap | 4 | Sitemap exists, target URL included |
| URL Structure | 3 | Clean slugs, no session IDs or excessive params |

### Content SEO (14 checks)

| Check | Weight | What is evaluated |
|---|---|---|
| Title Tag | 10 | 50–60 chars, target keyword present |
| H1 Tag | 10 | Single H1, keyword-rich |
| Meta Description | 8 | 120–160 chars, compelling copy |
| Keyword Targeting | 8 | Keyword in key on-page positions |
| Content Depth | 7 | Word count and paragraph quality |
| Image Alt Text | 7 | All images have descriptive alt text |
| Heading Hierarchy | 6 | Logical H1→H2→H3 structure |
| Keyword in URL | 5 | Target keyword present in URL slug |
| OG / Social Tags | 5 | og:title, og:description, og:image set |
| Local SEO / NAP | 5 | Name, Address, Phone consistent on page |
| External Links | 4 | Quality outbound links to authoritative sources |
| FAQ / Rich Content | 4 | Structured FAQ, lists, tables |
| CTA Placement | 4 | Clear calls-to-action present |
| HTML Lang Attribute | 3 | `<html lang="en-au">` set correctly |

### SEM Readiness (11 checks)

| Check | Weight | What is evaluated |
|---|---|---|
| Landing Page Relevance | 12 | Page matches paid ad intent |
| Clear Value Proposition | 10 | USP immediately visible |
| Call to Action | 10 | Prominent, specific CTA |
| Above the Fold CTA | 8 | CTA visible without scrolling |
| Phone / Contact Visibility | 8 | Phone number prominent, click-to-call |
| Lead Capture / Form | 7 | Contact form, booking, or subscription |
| Trust Signals | 7 | Accreditations, logos, certifications |
| Social Proof / Reviews | 7 | Testimonials, ratings, case studies |
| Ad Keyword Alignment | 7 | Page copy matches search terms |
| Page Load Speed | 6 | Fast load = lower CPC, higher Quality Score |
| Mobile UX | 6 | Tap targets, readable text, mobile layout |

---

## Tracking Progress

Every time you run an audit on the same domain, the results are saved to `seo-history-<domain>.json`. On subsequent runs, the report will show a comparison column:

```
Category          Score     Grade    vs 2026-03-04
Technical SEO     72/100    B        +8
Content SEO       61/100    C        +5
SEM Readiness     55/100    D        +3
Overall           65/100    C        +6
```

This lets you verify that site changes are actually improving your SEO scores.

---

## Changing the AI Model

To upgrade or switch models, edit `.env` — no code changes needed:

```ini
# Upgrade to Claude Opus
CLAUDE_MODEL=claude-opus-4-6

# Use a different Gemini model
GEMINI_MODEL=gemini-2.0-flash
```

---

## Troubleshooting

**`ModuleNotFoundError: No module named 'dotenv'`**
```bash
pip install python-dotenv
```

**`❌ Error: API key required`**
- Check that `.env` exists and contains your key
- Or pass it directly: `python seo_audit.py <url> --api-key YOUR_KEY`

**`lxml` install fails on Linux**
```bash
sudo apt-get install libxml2-dev libxslt-dev python3-dev
pip install lxml
```

**Report score seems low even after fixes**
- The score is based on a fresh crawl of the live page — make sure your changes are deployed
- Use `--crawl-only` first to verify the crawl is picking up your changes in the raw JSON
