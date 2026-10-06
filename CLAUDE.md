CLAUDE.md
=========

This repository root is the FerrariLabs corporate public website.

Corporate scope
---------------

The following files and directories are within corporate scope:
- `index.html` (homepage)
- `financial-crimes/` (former homepage content — AML/fraud/sanctions/digital-asset consulting)
- `small-business/` and `small-business/pt/` (small-business technology)
- `insights.html`
- Root translated corporate pages (`index.pt.html`, `index.es.html`, `index.jp.html`)
- `assets/`
- `styles.css`
- `docs/website/`
- `CNAME`, `sitemap.xml`, `.nojekyll`
- Corporate deployment, SEO, forms (Formspree + Cloudflare Turnstile), and analytics

Corporate production origin remains `https://www.ferrarilabs.com` (set by CNAME).
`ferrarilabs.github.io` and the apex `ferrarilabs.com` respond 301 there.

GitHub Pages deploys from `main`. Avoid unnecessary GitHub Actions.

Non-company personal projects are out of corporate scope. They must not be indexed, referenced,
summarized, modified, monitored, or treated as FerrariLabs assets. Any work under `bolao/` must
use the nested `bolao/CLAUDE.md` instead and is not part of FerrariLabs corporate operations.
Do not import corporate brand/governance into personal subtrees.

Preserve current corporate pages, CNAME, Formspree/Turnstile/analytics behavior, accessibility,
SEO, and translations.

Footer
------

© 2026 GitHub, Inc.
