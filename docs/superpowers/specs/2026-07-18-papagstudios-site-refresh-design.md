# papagstudios.com — Site Refresh & Restructure (Design)

**Date:** 2026-07-18
**Tracking issue:** papagstudios/papagstudios.github.io#1
**Status:** Approved design → ready for implementation plan

## Summary

papagstudios.com is stale (single page, one "Coming Soon" NumShift card, games-only
tagline) and PapaG has outgrown its games-only framing. This refresh repositions PapaG as a
**small indie software studio making privacy-respecting games *and* apps**, rebuilds the site as a
**multi-page Jekyll site** (GitHub Pages native) with per-product pages, and corrects stale product
statuses.

Goal: a site that (a) accurately reflects the current catalog, (b) accommodates non-game products
(Abide), and (c) is structurally easy to keep current (shared layouts, not copy-pasted chrome).

## Positioning & tagline

PapaG Studios = **a small indie studio crafting privacy-respecting games and apps** (no longer
"independent games for iOS").

- **Primary tagline:** *"A small studio crafting delightful games and apps — built with care, without the tracking."*
- **Alt:** *"Independent games and apps for people, not data."*

## Information architecture

Multi-page site. Routes:

```
/                        Home — hero + Games section + Apps section + Promise strip
/numshift                Game — Live (App Store)
/battlekeep              Game — Coming Soon
/abide                   App  — Coming to the Mac App Store
/promise                 Privacy-first manifesto ("Our Promise")
/numshift/support, /numshift/privacy      (existing per-product legal pattern)
/battlekeep/support, /battlekeep/privacy
/abide/support, /abide/privacy
```

- **Nav (header):** `PapaG` logo + `Games` · `Apps`.
- **Footer:** obfuscated support email + link to `/promise` + copyright.
- **TowerForge:** omitted for now (paused). Optional tiny "more in the works" line — not required.
- **/about (the PapaG story):** intentionally **parked** for now (Gene waffling). Design must make it
  trivial to add later (a nav slot + one content page), but it is NOT in this build.

## Build approach

**Jekyll on GitHub Pages** (native build — no CI, no external services). Rationale: shared
layouts/includes fix the maintainability problem (update nav/footer once); output is pure static
HTML with zero runtime JS or external calls, keeping the site fully privacy-clean.

Restructure the current single `index.html` into:

```
_config.yml
_layouts/
  default.html        (nav + footer chrome, <head>, styles link)
  product.html        (extends default; product hero/features/screens/links from front-matter)
_includes/
  nav.html
  footer.html
  product-card.html   (used by home sections AND product hero)
_data/
  products.yml        (catalog: name, slug, kind [game|app], status, tagline, accent, CTA, links)
assets/
  css/style.css       (extracted from the current inline <style>)
  img/                (product key art, screenshots, PapaG avatar — includes existing /images)
index.html            (home; Games/Apps sections rendered from _data/products.yml)
numshift.html, battlekeep.html, abide.html   (front-matter → product layout)
promise.html
numshift/, battlekeep/, abide/  (support.html + privacy.html each)
CNAME                 (unchanged)
```

- **Local preview** needs Ruby + Jekyll (`bundle exec jekyll serve`); GitHub Pages builds it
  server-side regardless.
- A `product` is defined once in `_data/products.yml`; the home cards and the product page both read
  from it, so status/links can never drift between them.

## Page templates

### Home
Nav → Hero (PapaG avatar + name + tagline) → **Games** section (cards: NumShift [Live],
BattleKeep [Coming Soon]) → **Apps** section (card: Abide [Coming to Mac App Store]) → Privacy-first
strip (one line + link to /promise) → Footer.

### Product page (`_layouts/product.html`, front-matter driven)
Nav → Hero (key art · name · tagline · status badge · primary CTA) → one-paragraph pitch →
3–4 feature highlights → screenshot gallery (2–5) → Support · Privacy links → Footer.

### Product card (`_includes/product-card.html`)
Icon/key art · name · one-liner · status badge · link to product page. Shared by home sections and
product hero so every surface is identical.

## Product catalog & statuses

| Product | Kind | Status | CTA | Notes |
|---|---|---|---|---|
| **NumShift** | Game (iOS) | **Live** | "Available on the App Store" + link | Fix the stale "Coming Soon". Zero ads, zero tracking. |
| **BattleKeep** | Game (iOS) | **Coming Soon** | "Coming to the App Store" badge | Illustrated tower-conquest. Light ads + $2.99 remove-ads. |
| **Abide** | App (macOS) | **Coming Soon** | "Coming to the Mac App Store" badge | Distributed **Mac App Store only** (no direct download from the site). iOS later. Faith toolset (scripture + prayer). |
| ~~TowerForge~~ | Game | Paused | — | Omitted this build. |

## Visual direction

- **Keep the current clean dark aesthetic** (deep navy `--bg:#0c1021`, surface cards, DM Sans body +
  JetBrains Mono accents) — reads indie/crafted/privacy-first. Extend, don't replace.
- **Per-product accent color** for identity: NumShift green/blue (existing), BattleKeep warm
  illustrated green, Abide calm blue/gold (reflective/faith). Exact hex values TBD during
  implementation.
- **Higgsfield** for graphics: hero/key art per product (NumShift tiles, BattleKeep castles, Abide
  reflective imagery), the PapaG hero avatar (existing gaming icon), and section flourishes. Games
  also use real App Store screenshots; Abide uses macOS app screenshots. Higgsfield fills gaps.
- **Fast & light:** static HTML, optimized images, minimal/no JS (email-obfuscation script is the
  only JS, carried over).

## The Promise (privacy manifesto)

Honest framing — products are **not** uniformly ad-free (NumShift is; BattleKeep has light,
removable ads). The manifesto spine:

1. **No tracking. No selling your data. No dark patterns.** (true across everything)
2. **Ads, where they exist, are minimal and always removable** — one honest one-time purchase, never
   a nag. (covers BattleKeep; NumShift proudly has none)
3. **You own your stuff** — data stays on-device / in your own accounts. (fits Abide's on-device
   faith content)

Must stay factually accurate as products/monetization evolve.

## Immediate correctness fixes (independent, can land first)

- NumShift card/page: "Coming Soon" → "Available on the App Store" + store link.
- Add BattleKeep (Coming Soon) and Abide (Coming to Mac App Store).

## Out of scope (this build)

- `/about` PapaG-story page (parked; easy future add).
- TowerForge product page.
- Blog / news, dedicated contact page, newsletter/email capture, any e-commerce or accounts.
- Real App Store submission of BattleKeep/Abide (site can badge "Coming Soon" ahead of live links).

## Open questions (resolve during implementation)

- Final accent hex palettes per product.
- Which real screenshots exist now vs. need Higgsfield mockups (esp. Abide macOS, BattleKeep).
- Exact NumShift App Store URL to link.
- Whether to include the optional "more in the works" (TowerForge) teaser line on home.
