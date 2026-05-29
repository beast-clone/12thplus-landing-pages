# Developer Handover — 12thPlus Landing Pages

## Overview

5 static landing pages for MBBS abroad — **Georgia, Russia, Uzbekistan, Vietnam, JSS Mauritius**.

Each is one self-contained HTML file with its own brochure PDF and campus images. No build step, no framework. Pure HTML/CSS/JS.

---

## Deploy Targets

Each page's canonical URL is set in its `<head>`. Host them at:

| Country | Path | File |
|---|---|---|
| JSS Mauritius | `/jss-mauritius` | `JSS Mauritius/JSS Mauritius 12thPlus Landing Page v3.html` |
| Georgia | `/georgia` | `Georgia/Georgia 12thPlus Landing Page v3.html` |
| Russia | `/russia` | `Russia/Russia 12thPlus Landing Page v3.html` |
| Uzbekistan | `/uzbekistan` | `Uzbekistan/Uzbekistan 12thPlus Landing Page v3.html` |
| Vietnam | `/vietnam` | `Vietnam/Vietnam 12thPlus Landing Page v3.html` |

**Domain:** `goocampus-hr-portal.onrender.com` (or whatever final domain is chosen).

Each folder's `brochures/` and `images/` subfolders must remain relative to the HTML — paths inside the HTML use `brochures/<file>.pdf` and `images/<file>.jpg` style references.

---

## Already Wired

- ✅ **Microsoft Clarity** — ID `wqr9yqu3d9`
- ✅ **Meta Pixel** — ID `1387753849107574` (GooCampus Edu Event Data)
- ✅ **Google Apps Script lead form** — writes to a shared Google Sheet, routed by `source` column to per-country tabs
- ✅ **Brochure auto-download** via blob-fetch (no inline-PDF tab, no Drive redirect)
- ✅ **JSON-LD structured data:** Course + Organization + FAQPage + VideoObject + BreadcrumbList
- ✅ **Open Graph + Twitter Card** meta tags
- ✅ `llms.txt` for AI crawlers (Perplexity, ChatGPT search, etc.)
- ✅ `robots.txt` + `sitemap.xml` at repo root

---

## Still Placeholder — Please Fill

| Token | Where to find it | How to obtain |
|---|---|---|
| `G-XXXXXXXXXX` | All 5 HTML files (GA4 Measurement ID) | analytics.google.com → Admin → Data Streams → Web |
| `REPLACE_WITH_GOOGLE_SEARCH_CONSOLE_TOKEN` | All 5 HTML files, `<meta name="google-site-verification">` | search.google.com/search-console after deploy |
| `REPLACE_WITH_BING_WEBMASTER_TOKEN` | All 5 HTML files, `<meta name="msvalidate.01">` | bing.com/webmasters after deploy |

Search Console + Bing tokens are generated **after** the site is live at the final URL.

---

## Form Backend

- **Endpoint:** `https://script.google.com/macros/s/AKfycby.../exec`
  (hardcoded as `SHEET_URL` in each HTML — single shared script across all 5 pages)
- **POST payload:**
  ```json
  {
    "source": "Russia",
    "firstName": "...",
    "lastName": "...",
    "phone": "(+91) 9876543210",
    "email": "...",
    "city": "...",
    "neetStatus": "...",
    "class12": "..."
  }
  ```
- **Validation:** client-side regex (Indian phone 10-digit starting 6-9, email, required fields)
- **After submit:**
  1. Lead saves to Google Sheet (Source-routed tab)
  2. Success card swaps in: "✅ Thank you! Your `<Country>` brochure is downloading now. Our counsellors will get back to you within 24 to 48 hours."
  3. PDF brochure auto-downloads from `brochures/` (blob-fetch — works on HTTPS, NOT on file://)

---

## Tracking Events Fired

Every page fires these events via `gtag()` and `fbq()`:

| Event | Fires when |
|---|---|
| `webinar_play` | User clicks the hero video card |
| `brochure_cta_click` | User clicks any "Download Brochure" button (nav, hero, bottom CTA) |
| `lead_submit` | Form successfully submitted |
| `whatsapp_click` | User clicks the floating WhatsApp button |

All events include `page: '<Country> v3'` for attribution.

---

## Known Cosmetic Items

- Gallery captions are **hidden** across all 5 pages (intentional — images were drag-dropped, captions didn't always match content)
- A few `<!-- TODO -->` comments scattered in HTMLs where source data was missing (bed counts, overseas eligibility criteria for Georgia, etc.)
- Render's free tier cold-starts after ~15 min idle; consider a paid tier or CDN for the brochure PDFs (each is ~2 MB)

---

## File Structure

```
.
├── Georgia/
│   ├── Georgia 12thPlus Landing Page v3.html
│   ├── brochures/MBBS-in-Georgia-Brochure-2026.pdf
│   └── images/  (14 photos)
├── Russia/
│   ├── Russia 12thPlus Landing Page v3.html
│   ├── brochures/MBBS-in-Russia-Brochure-2026.pdf
│   └── images/  (8 photos)
├── Uzbekistan/
│   ├── Uzbekistan 12thPlus Landing Page v3.html
│   ├── brochures/MBBS-in-Uzbekistan.pdf
│   └── images/  (13 photos)
├── Vietnam/
│   ├── Vietnam 12thPlus Landing Page v3.html
│   ├── brochures/MBBS-in-Vietnam.pdf
│   └── images/  (9 photos)
├── JSS Mauritius/
│   ├── JSS Mauritius 12thPlus Landing Page v3.html
│   ├── brochures/JSS-Academy-of-Higher-Education-and-Research-Mauritius.pdf
│   └── images/  (17 photos)
├── llms.txt
├── robots.txt
├── sitemap.xml
└── HANDOVER.md  ← you are here
```

---

## Questions / Issues

Reach out via WhatsApp or email when:
- Tokens are filled and ready to go live
- Tests for form submission / brochure download have been validated post-deploy
- Render deployment URL is finalized so the canonical tags / sitemap can be updated if the path changes
