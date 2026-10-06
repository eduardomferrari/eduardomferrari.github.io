CLAUDE.md
=========

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Deployment
----------

No build step. Push to `main` and GitHub Pages auto-deploys.

**A origem de produção é `https://www.ferrarilabs.com`** (definida pelo `CNAME` a raiz do repo). `ferrarilabs.github.io` e o apex `ferrarilabs.com` respondem **301** para lá — nenhuma página de
produção executa neles. Qualquer código que compare `location.origin` com `ferrarilabs.github.io` está errado para 100% do tráfego real (já causou um incidente: ver `docs/bolao/TEST_ISOLATION.md`).
As URLs abaixo usam o caminho canônico `www.ferrarilabs.com`.

O bump de cache-bust (`?v=`) é feito pelo bot `sync_version.yml`, que **dispara o deploy do Pages
explicitamente** — um push com `GITHUB_TOKEN` ão acorda workflow nenhum. Verificar deploy sempre
comparando o `?v=` ao vivo com o do repositório.

* Main site: `www.ferrarilabs.com` — plus `/financial-crimes/`, `/small-business/` and `/small-business/pt/`
* Bolão root: `www.ferrarilabs.com/bolao/` — redirects to Brasileirão (see below)
* Copa do Mundo 2026: `www.ferrarilabs.com/bolao/copa2026/` (moved here 2026-07-19, v4.159 — see "Copa do Mundo 2026 archive" below)
* Brasileirão 2026: `www.ferrarilabs.com/bolao/br2026/` (not published yet)
* Copa do Brasil 2026: `www.ferrarilabs.com/bolao/cdb2026/` (published 2026-07-19, in production)

To preview locally:

```
python3 -m http.server 8080 # Open: http://localhost:8080/bolao/br2026/
```

Repository structure
--------------------

Three independent sub-projects:

**Main site** (`index.html`, `financial-crimes/index.html`, `small-business/index.html`, `small-business/pt/index.html`, `insights.html`, `index.pt.html`, `index.es.html`, `index.jp.html`, `styles.css`) — static FerrariLabs site. Since 2026-09-30 the homepage (`index.html`) is a short umbrella page linking to two service areas: `/financial-crimes/` (the former homepage content, moved there intact — AML/fraud/sanctions/digital-asset consulting) and `/small-business/` (practical technology for local small businesses). The root PT/ES/JP pages are translations of the financial-crimes content, so their `hreflang="en"` / EN switcher point at `/financial-crimes/`. The small-business line has its own Portuguese localization at `/small-business/pt/`; EN/PT-BR alternate links must remain reciprocal. New pages use root-absolute paths (`/styles.css`). Contact form uses Formspree + Cloudflare Turnstile (keys must be set manually in the HTML); the same form is on `/`, `/financial-crimes/` and `/small-business/` (the last with `_subject` "Ferrari Labs — small business inquiry"). `/small-business/` carries schema.org `ProfessionalService` JSON-LD with locality and service area only — never a street address.

### Corporate website brand system

For the **Main site only**, the canonical visual system is documented in `docs/website/BRAND_SYSTEM.md`.

* Typeface: Arial, Helvetica, sans-serif. Do not add Google Fonts/webfont dependencies.
* Palette: Forest `#0F3D2E`, Terracotta `#B85C3E`, Ivory `#F5EDE2`, Charcoal `#1F2937`, Warm Gray `#E7E1DA`, Sage `#6F8579`.
* Logo mark: `/assets/ferrarilabs-mark.svg`; wordmark is rendered in HTML/CSS with Arial.
* FerrariLabs is the master brand; service families are Business AI & Automation and Financial Crime Technology.
* Small-business customer-facing messaging is organized as Get Found / Don't Lose the Lead / Run Smarter / Fix What's Broken. Lead with outcomes, not technology names.
* Portuguese SMB content is a localization/acquisition channel, not a separate brand; English remains the default corporate language.
* Light mode is default; dark mode uses Forest, not generic black/blue.
* Do not propagate this corporate brand into any Bolão app unless Eduardo explicitly requests a separate Bolão change.
* Preserve the existing information architecture, SEO metadata, Formspree/Turnstile behavior, analytics, accessibility, and responsive behavior during brand changes.
* Avoid unnecessary GitHub Actions usage: iterate and run the required checks locally before opening/merging a PR.

**Copa do Mundo 2026** (`bolao/copa2026/`) — bracket pool, tournament concluded (Spain champion, 2026-07-19) and archived. Vanilla JS, no framework, no build system. URL: `www.ferrarilabs.com/bolao/copa2026/`. See "Copa do Mundo 2026 archive" below.

**Brasileirão 2026** (`bolao/br2026/`) — G4/Z4 classification picks with live ESPN standings. Not published yet (no link from main site). URL: `www.ferrarilabs.com/bolao/br2026/`.

**Copa do Brasil 2026** (`bolao/cdb2026/`) — knockout-round picks with real teams. Published 2026-07-19 (in production, invited by email). URL: `www.ferrarilabs.com/bolao/cdb2026/`.

Copa do Mundo 2026 archive (v4.157–v4.159, 2026-07-19)
------------------------------------------------------

Eduardo, after the Final concluded: "Copa do mundo finalizada! ... Desabilitar os botões todos,
deixar só o vencedor, auditoria e os palpites" (v4.157 — `CONFIG.archived` in `js/config.js` hides every nav button except Ranking, which already has the podium banner, audit report link,
and "Ver palpites" per-entry detail), then "Deixe o default do site como o Brasileiro agora" —
confirmed he wanted a real redirect, not just the switcher's default option changing (v4.158 had
only done the latter). A real redirect at `bolao/index.html` required moving the whole app so the
archived Ranking would still have a URL of its own — so the entire Copa app (was directly under `bolao/`) moved to `bolao/copa2026/` (v4.159), matching the `br2026/`/`cdb2026/` folder pattern.

* `bolao/index.html` is now a redirect (meta refresh + JS `location.replace`) to `/bolao/br2026/`.
* `bolao/audit-report.html`, `audit-detail-picks.html`, `audit-detail-governance.html`, and `classificacao-geral.html` are redirect stubs pointing into `bolao/copa2026/` — these paths
  were already emailed to real participants before the move and must keep resolving.
* `bolao/sw.js` is left in place unchanged (harmless, generic — no app-specific paths) as a
  safety net for any browser that still has the old `/bolao/`-scoped service worker registered;
  the live app now registers its own copy at `bolao/copa2026/sw.js`.
* The "Copa do Mundo" option in all three apps' "Alternar bolão" switcher now points at `/bolao/copa2026/`, not `/bolao/` (pointing at `/bolao/` would loop back to the redirect).
* Reversible: to make Copa the default again, edit `bolao/index.html`'s redirect target back to `/bolao/copa2026/` and flip `CONFIG.archived` to `false` in `bolao/copa2026/js/config.js`.

Bolão app — quick reference
---------------------------

### Script load order

1. `@emailjs/browser@4` (CDN, sync)
2. `@supabase/supabase-js@2` (CDN, sync)
3. `js/config.js` → `window.BOLAO_CONFIG`
4. `js/data.js` → `window.BOLAO_DATA`
5. `js/i18n.js` → `window.BOLAO_I18N`
6. `js/app.js` (defer — all logic in a single IIFE)

### Key files

| File | Purpose |
| --- | --- |
| `js/config.js` | Runtime config: scoring, payments, Supabase, EmailJS, cutoff date, admin hash |
| `js/data.js` | Fixture data: 72 group + 32 knockout matches, team flags, strength ratings |
| `js/i18n.js` | All UI strings in **3 languages**: `pt-BR`, `es`, `en-US` |
| `js/app.js` | Single IIFE (~1430 lines): all state, rendering, validation, scoring, admin |
| `css/styles.css` | All styles — mobile-first, responsive |
| `index.html` | Single page; sections shown/hidden by JS |

### State

* **localStorage key:** `bolao_copa_2026_state`
* **Supabase table:** `bolao_state`, single row `id = "main"`, column `state jsonb`
* **Draft key:** `sessionStorage["bolao_draft_v4"]` (2-hour expiry)
* **Language key:** `localStorage["bolao_lang"]`
* App is local-first: Supabase failure degrades gracefully.

### Scoring (configured in `js/config.js`)

* Exact score: **10 pts**
* Correct advancement: **5 pts**
* One team's goals correct: **1 pt**
* Bonus: champion **+25**, runner-up **+15**, 3rd **+10**, 4th **+5**
* Prize pool: 70% → 1st, 20% → 2nd, 10% → 3rd

**This is the part of the site that can never be broken — real money is paid out based on it.** Standing rule from Eduardo (July 2026, after an audit found `send_result_email.py` had
 silently drifted from the site's own scoring logic — see CHANGELOG v4.57):

* `send_result_email.py --auto` runs `audit_scoring.py`'s static self-test suite before
  touching anything, and refuses to send any email if it fails. It also re-validates each
  individual match at runtime (event date not in the future, teams fully resolved, result
  shape sane) right before trusting it enough to save + email — see `check_match_is_real()` and `check_result_shape()` in `bolao/copa2026/scripts/audit_scoring.py`.
* **After every change you make to this repo — whether or not it looks scoring-related —
  run `python3 bolao/copa2026/scripts/audit_scoring.py`, `python3 bolao/br2026/scripts/audit_scoring.py`,
  and `python3 bolao/cdb2026/scripts/audit_scoring.py`, and say so in your summary to Eduardo, even
  if the answer is just "scoring untouched, audit still passes."** Don't assume a change is
  unrelated; the two bugs found in the July 2026 audit were both in code that looked
  unrelated to whatever was being worked on at the time.
* If you change the bracket (`bolao/copa2026/js/data.js`'s `knockoutMatches`), the scoring
 formula, the tiebreak cascade, or anything in `bolao/copa2026/scripts/send_result_email.py`,
 treat `audit_scoring.py` failing as a hard blocker — fix it before opening a PR, not after.

### Admin

* Password stored as SHA-256 hash in `config.adminPasswordHash`. Plaintext never in source.
* Lockout: 5 failed attempts → 15-min block.
* Session: 30 min, `sessionStorage`, cleared on tab close.
* `guardAdmin()` called on every admin action.

To generate a new hash:

```
crypto.subtle.digest("SHA-256", new TextEncoder().encode("YourPassword"))
  .then(b => console.log([...new Uint8Array(b)].map(x=>x.toString(16).padStart(2,"0")).join("")))
```

### Cutoff

* `cutoffIso: "2026-06-28T14:00:00-04:00"` — Sunday June 28 2026 at 2 PM ET.
* Enforcement is client-side only (clock manipulation bypasses it).

### EmailJS

* Template body must contain **only** `{{{html_message}}}` — no other fields.
* Rate limit: 30-second throttle per browser.
* Two templates: participant receipt (`participantTemplateId`) + admin notification (`adminTemplateId`).

### i18n

All UI strings are in `js/i18n.js`. **Three language objects:** `pt-BR`, `es`, `en-US`.
When adding a new key, add it to all three objects. Default fallback is `pt-BR`.

### Supabase

* `database.enabled: true` in config to activate.
* Only anon key used — never the service_role key.
* RLS restricts all operations to `id = "main"`.
* Merge strategy: union entries, local wins for paid/results.
* See `bolao/copa2026/docs/DATABASE_SETUP_SUPABASE.md` for SQL setup.

### Release process

1. Edit files under `bolao/copa2026/` (or `bolao/br2026/`, `bolao/cdb2026/` for those apps).
2. Bump `siteVersion` in that app's `js/config.js`.
3. Add a CHANGELOG entry in that app's `CHANGELOG.md` (e.g. `bolao/copa2026/CHANGELOG.md`).
4. Commit and push to `main`.
5. Run QA checklist from `docs/bolao/QA_CHECKLIST.md`.

### Rollback

```
git revert HEAD && git push # or git checkout
