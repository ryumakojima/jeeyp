# Japan-East Europe Young Professionals — Project Spec & Handoff

> Handoff doc for a future developer or AI. Covers what this is, how it's built,
> the decisions behind it, what's still placeholder, and known issues.
> Companion docs: `PHOTOS.md` (images), `CLAUDE.md` (quick brief).

## 1. What this is

A bilingual (English / 日本語) marketing site for **Japan-East Europe Young
Professionals (JEEYP)** — a community connecting young people in Japan with the
Eastern European community living in Japan, through **networking events,
cultural exchange, and sports (e.g. soccer)**.

It is a **re-theme of the sister project JAYP** (Japan-Africa Young Professionals):
same Astro codebase, components, and design system, with the geography and copy
changed from Africa to Eastern Europe.

- **Audience:** open to *all* Eastern European countries, and to anyone in Japan
  interested in Eastern European culture, language, business, or exchange.
  Explicitly **not students-only** — young professionals, grads, entrepreneurs,
  and Eastern European residents/workers in Japan are all included.
- **Status:** pre-launch. Founded 2026. First event **"Japan–East Europe
  Connection Party"** planned for **around November 2026** (date/venue TBA).
- **Important framing:** this is about the *community*, NOT about the founder.
  The founder appears only as one entry on the Members page.

## 2. Tech stack & commands

- **Astro 7** (static output), TypeScript, no UI framework, no Tailwind.
- Fonts via Google Fonts: **Cinzel** (Latin labels), **Noto Serif JP** (headings),
  **Noto Sans JP** (body) — loaded in `BaseLayout.astro`.
- `npm run dev` (port 4321) · `npm run build` → `dist/` · `npm run preview`.
- Node ≥ 22.

## 3. Architecture

```
astro.config.mjs       ← site = ryumakojima.github.io, base = '/jeeyp'
src/
  config.ts            ← central config + withBase() helper + getSignupUrl()
  i18n/ui.ts           ← nav/footer chrome strings + CTA label (EN/JA only)
  data/updates.ts      ← activity feed (currently empty → section hidden)
  styles/global.css    ← design tokens, utilities, JA line-break rules
  layouts/BaseLayout.astro  ← <head> SEO (canonical/hreflang/OG/Twitter/JSON-LD),
                              Nav + Footer, lang auto-redirect, reveal/scroll JS
  components/
    Nav.astro          ← fixed glass nav, circular logo, primary CTA, lang switch
    Footer.astro       ← dark footer, socials/links from config
    Hero.astro         ← full-bleed/short cover hero (object-fit:cover), gold CTA
    CardGrid.astro     ← accent-border cards w/ inline-SVG icons (icon map inside)
    SectionLabel.astro ← gold rule + Cinzel eyebrow + serif heading
    Stats.astro        ← count-up reach numbers
    Updates.astro      ← activity cards from data/updates.ts
    (PhotoFeature / MiniBars / PhotoWall / PastEvents / Partners are available
     but not currently mounted on any page)
  assets/photos/       ← hero-*.jpg + feature-contact.jpg (PLACEHOLDER gradients)
  pages/               ← EN at root, JA mirrored under /ja
    index, programs (slug = "What We Do"), events, connection-party, members,
    get-involved, contact   (+ ja/ copies of each except members)
public/
  logo.png             ← circular logo placeholder (also favicon)
  og.jpg               ← 1200×630 social card (placeholder)
  members/ryuma.jpg    ← founder photo
  robots.txt · .nojekyll
.github/workflows/deploy.yml  ← GitHub Pages deploy
```

### Base path (important)
The site is a **GitHub Pages project site**, served from **`/jeeyp/`**. `base` is
set in `astro.config.mjs`. `astro:assets` image imports are auto-prefixed by
Astro, but **hand-written `href`/`src` strings are not** — they all go through
**`withBase()`** in `config.ts`, which reads `import.meta.env.BASE_URL`. The
`<head>` slug/hreflang logic and the inline language-redirect script both strip
the base first. To change the subpath (or move to root), edit `base` in ONE place.

### i18n
- Astro built-in i18n: `defaultLocale: 'en'` at root, `ja` under `/ja/...`,
  `prefixDefaultLocale: false`. Slugs are identical across locales.
- **Body copy lives directly in each page file** (not a dictionary) so it's easy
  to hand-edit. Only nav/footer chrome is in `i18n/ui.ts`.

### Design system (see `global.css`)
- Palette: gold `#c5a059`, dark green-charcoal `#1e2b26`, sand `#f4edd8`,
  terracotta accent `#c25e2e`, green `#4e6b43`. (Inherited from JAYP — re-colour
  here if JEEYP wants its own identity.)
- Sections alternate `.section-white / -sand / -dark`. Eyebrows in Cinzel.
- Images **always** use `object-fit: cover` inside a fixed-aspect `.ratio` box.
- Scroll-reveal via `.reveal` + IntersectionObserver (visible without JS).

## 4. SEO

Implemented in `BaseLayout.astro` + config:
- Per-page `<title>` + meta description (keyword-aware), canonical URL.
- hreflang alternates (en / ja / x-default) — base-aware.
- Open Graph + Twitter card (image = `/jeeyp/og.jpg`).
- JSON-LD `Organization` + `WebSite` + `WebPage` graph.
- `@astrojs/sitemap` (i18n-aware) → `/jeeyp/sitemap-index.xml`; `robots.txt` points to it.

After deploy: add the property to **Google Search Console** (set the token in
`config.ts` `googleSiteVerification`), submit the sitemap.

## 5. Deployment (GitHub Pages)

- Repo: `ryumakojima/jeeyp`. `.github/workflows/deploy.yml` builds with
  `withastro/action` and deploys via `actions/deploy-pages` on push to
  `main`/`master`.
- Enable Pages → Source: **GitHub Actions** in repo settings.
- Served at `ryumakojima.github.io/jeeyp/` (project subpath). Base handling is
  already wired (§3). See `DEPLOY.md`.

## 6. Placeholders to fill (search for these)

| What | Where | Note |
|---|---|---|
| Sign-up form | `src/config.ts` `signupUrl` | empty → CTAs fall back to `/contact`. Point to a Google Form / Luma / Peatix. **mailto intentionally avoided** |
| Socials / email | `src/config.ts` `instagram`, `x`, `email` | empty → hidden on Contact/Footer |
| GSC token | `src/config.ts` `googleSiteVerification` | for Search Console |
| Reach numbers | `pages/index.astro` + `ja` (`reachStats`) | edit as the community grows |
| Photos | `src/assets/photos/*.jpg` | placeholder gradients — see `PHOTOS.md` |
| Logo / OG | `public/logo.png`, `public/og.jpg` | placeholder |
| One-page brief | `config.ts` `onePageBriefUrl` | empty → download button hidden; drop a PDF in `public/downloads/` and set the path |
| Member entries | `pages/members.astro` | founder + 2 open slots currently |

## 7. Known issues / risks / TODO

- **All imagery is placeholder.** `src/assets/photos/*` are generated gradients,
  and the logo/OG card are simple placeholders. Replace with real assets.
- **No fabricated data.** Unlike JAYP, this site intentionally ships with NO
  sourced statistics chart, NO activity history (Updates empty), and only
  placeholder reach numbers — because JEEYP is a fresh org. Add real, sourced
  data as it exists; don't invent citations.
- **Design colours are inherited from JAYP** (warm gold/sand). Consider a distinct
  JEEYP palette in `global.css` once branding is decided.
- **Sign-up is a fallback to /contact** until a real form URL is set.
- **No analytics** yet (consider Plausible/GA4 for the SEO goal).
- Japanese line-breaking relies on `word-break: auto-phrase` (Chrome 119+) with
  graceful fallback + `text-wrap: balance/pretty`.

## 8. History

1. Cloned from the JAYP (Japan-Africa) Astro site.
2. Re-themed Africa → Eastern Europe across all EN/JA pages, nav/footer, SEO
   metadata, and JSON-LD; renamed to **Japan-East Europe Young Professionals**.
3. Added a project **subpath deploy** (`base: '/jeeyp'`) with a `withBase()`
   helper and base-aware `<head>`/redirect logic.
4. Replaced all Africa-specific photos with neutral placeholder images; removed
   the sourced stats chart and the (JAYP-specific) activity history and one-page
   brief; pointed sign-up CTAs at the Contact page until real forms exist.

### Likely next asks
- Real logo + photos, sign-up form URL, socials, reach numbers, event date/venue.
- A distinct JEEYP colour palette, if desired.
- Google Search Console verification + sitemap submission.
