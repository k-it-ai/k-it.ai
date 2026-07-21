# k-it.ai — Sovereign AI website

Static marketing site for **k-it.ai**. Plain HTML/CSS/JS — no build step, no
dependencies, no server-side code. Bilingual (German default / English),
deployed on Cloudflare Workers (Static Assets).

## Structure

```
index.html             Homepage (Hero · Why · Services · Data flow · Deployment ·
                       Compliance · SLA · FAQ · Contact)
legal-notice.html      Impressum + Datenschutzerklärung (Legal Notice + Privacy)
assets/
  k-logo.svg           Brand logo (the "K" mark)
  favicon.svg          Favicon (the K mark)
  apple-touch-icon.png iOS / home-screen icon (180×180)
  og-image.png         Social share card (1200×630)
  fonts.css            @font-face for the self-hosted fonts
  fonts/               Inter + JetBrains Mono (woff2, self-hosted, SIL OFL)
  flags/de.svg         Language switch — German
  flags/en.svg         Language switch — English
_headers               Security + cache headers
_redirects             /impressum, /datenschutz, /legal → legal-notice.html
robots.txt             Crawl rules + sitemap reference
sitemap.xml            Sitemap (homepage)
wrangler.jsonc         Cloudflare Workers config (serves the repo as static assets)
.assetsignore          Repo files that must NOT be served to visitors
```

## Languages (DE / EN)

The site is bilingual. **German is the default** and lives directly in the HTML,
so visitors with JavaScript disabled and search-engine crawlers always get
German. English strings live in a small `I18N_EN` dictionary inside each page and
are applied on top when needed.

Selection order:

1. A previously chosen language saved in `localStorage` (`lang` = `de` | `en`).
2. Otherwise the browser language — switches to **English** only if the browser
   explicitly prefers English (`navigator.language` starts with `en`).
3. Otherwise **German** (fallback).

Clicking a flag switches immediately and remembers the choice; `<html lang>`,
`<title>` and the meta description follow the language. To edit copy: change the
German text in the HTML and the matching key in `I18N_EN`.

## Local preview

```bash
npx wrangler dev        # serves exactly as Cloudflare does (reads wrangler.jsonc)
# …or any static server, e.g.
python3 -m http.server 8080
```

## Deploy — Cloudflare Workers

Deployed as a Cloudflare Worker using **Static Assets** (`wrangler.jsonc` →
`assets.directory: "."`), connected to this repo via Workers Builds, so
**pushing to `main` auto-deploys**. `_headers` and `_redirects` are applied by
the runtime; `.assetsignore` keeps `README.md`, `wrangler.jsonc` and `.gitignore`
out of the public site. The custom domain (k-it.ai) is set in the Cloudflare
dashboard. Manual deploy: `npx wrangler deploy`.

## Notes

- **Contact form → FormSubmit** (no account). The **first** submission emails
  `hi@k-it.ai` a one-time activation link; click it once and later submissions
  arrive in that inbox. The address is assembled at runtime (never in the page
  source); if the request fails the form falls back to a pre-filled email. For a
  fully EU-resident setup, swap FormSubmit for a Workers handler + an EU mailer
  (Brevo/Mailjet) and update the privacy policy.
- **Email obfuscation:** the address never appears as text in the HTML/JS source
  (assembled by JS from `data-` attributes; `hi(at)k-it.ai` fallback if JS is
  off).
- **Fonts** (Inter, JetBrains Mono) are self-hosted under `assets/fonts/` — no
  request ever goes to Google.
- **Privacy / legal:** `legal-notice.html` contains the Impressum and the
  Datenschutzerklärung (anchor `#datenschutz`). Have both reviewed by a lawyer,
  and conclude data-processing agreements (AVV) with Cloudflare and FormSubmit.
- **Social preview:** replace `assets/og-image.png` (1200×630) to change the
  link-preview card.
- **Marketing claims** (model versions, SLA targets, compliance wording) are
  copy — keep them accurate and verifiable.
