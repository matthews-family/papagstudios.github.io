# papagstudios.com Site Refresh Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild papagstudios.com as a multi-page Jekyll site that positions PapaG as an indie studio making games *and* apps, with per-product pages, a Promise page, and corrected product statuses.

**Architecture:** Convert the single hand-written `index.html` into a Jekyll site (GitHub Pages native build). Shared chrome lives in `_layouts` + `_includes`; the product catalog is single-sourced from `_data/products.yml` so home cards and product pages can never drift. Output is pure static HTML — the only JS is the existing email-obfuscation snippet.

**Tech Stack:** Jekyll (via the `github-pages` gem), Liquid templates, HTML/CSS. Ruby + Bundler for local preview. `html-proofer` for link/image verification.

## Global Constraints

- **Static + privacy-clean:** no runtime JS or external network calls EXCEPT the existing email-obfuscation `<script>` and the Google Fonts `<link>` already in use. No analytics, trackers, or third-party embeds.
- **GitHub Pages native:** only the `github-pages` gem's allowlisted plugins. No custom plugins.
- **Keep the existing dark aesthetic verbatim** as the base: `--bg:#0c1021`, `--surface:#151a2e`, `--border:rgba(255,255,255,0.06)`, `--text:#c8ceda`, `--text-dim:#6b7394`, `--heading:#e8ecf4`, `--accent-green:#34d399`, `--accent-blue:#60a5fa`, `--accent-purple:#a78bfa`, `--accent-amber:#fbbf24`. Fonts: `DM Sans` (body), `JetBrains Mono` (accents).
- **Single source of truth:** every product's name, status, tagline, links, and accent come from `_data/products.yml`. Pages and cards read from it; never hardcode a status in two places.
- **`CNAME` unchanged** (`papagstudios.com`).
- **Honest Promise copy:** never claim "no ads ever" (BattleKeep has removable ads). Use the three-pledge spine from the spec.
- **No PII / no personal names** anywhere (public repo). Studio voice is "PapaG Studios", contact is the obfuscated `support@papagstudios.com`.
- **Preserve existing URLs:** `/numshift/support` and `/numshift/privacy` must keep working.
- Spec: `docs/superpowers/specs/2026-07-18-papagstudios-site-refresh-design.md`.

---

## File Structure

```
_config.yml                 site config, collections off, defaults
Gemfile                     github-pages + html-proofer
_data/products.yml          product catalog (single source of truth)
_includes/head.html         <head> (meta, fonts, stylesheet link)
_includes/nav.html          header nav (logo + Games/Apps)
_includes/footer.html       footer (email obfuscation + Promise link)
_includes/product-card.html reusable card (home sections + product hero)
_layouts/default.html       base chrome (head + nav + {{content}} + footer)
_layouts/product.html       product page (hero/pitch/features/gallery/legal links)
_layouts/legal.html         support/privacy wrapper (narrow prose column)
assets/css/style.css        all styles (extracted from inline, plus new rules)
index.html                  home (hero + Games/Apps sections from data)
numshift.html               product page (front-matter only)
battlekeep.html             product page (front-matter only)
abide.html                  product page (front-matter only)
promise.html                privacy manifesto
numshift/support.html       migrated to layout: legal (text preserved)
numshift/privacy.html       migrated to layout: legal (text preserved)
battlekeep/support.html     new (layout: legal)
battlekeep/privacy.html     new (layout: legal)
abide/support.html          new (layout: legal)
abide/privacy.html          new (layout: legal)
CNAME                       unchanged
images/                     existing logo assets (keep)
assets/img/                 new product key art / screenshots (Higgsfield, added later)
```

---

### Task 1: Jekyll scaffold + chrome migration (build baseline)

Stand up Jekyll and move the existing site into layouts/includes **without changing what the home page looks like yet**. Deliverable: `bundle exec jekyll build` succeeds and the built home page is equivalent to today's.

**Files:**
- Create: `_config.yml`, `Gemfile`, `.gitignore`
- Create: `_includes/head.html`, `_includes/nav.html`, `_includes/footer.html`
- Create: `_layouts/default.html`
- Create: `assets/css/style.css`
- Modify: `index.html` (add front-matter, remove inline `<head>`/`<style>`, keep body content)

**Interfaces:**
- Produces: `layout: default` (wraps a page in head+nav+footer); `assets/css/style.css` (all styles); CSS custom properties listed in Global Constraints.

- [ ] **Step 1: Add Gemfile and .gitignore**

`Gemfile` (plain Jekyll — the `github-pages` gem pins old Jekyll incompatible with the installed Ruby 4.0; the site uses no custom plugins so GitHub Pages builds it identically server-side):
```ruby
source "https://rubygems.org"
gem "jekyll", "~> 4.3"
gem "html-proofer", "~> 5.0"
```

`.gitignore`:
```
_site/
.jekyll-cache/
.sass-cache/
Gemfile.lock
vendor/
```

- [ ] **Step 2: Install and verify Jekyll runs**

Run:
```bash
cd /Users/genem/Claude/matthewsOS/Projects/PapaG-Studios/site
bundle install
```
Expected: bundle completes, `github-pages` and `html-proofer` installed. (If Ruby is too old, `brew install ruby` and re-run; note it in the commit.)

- [ ] **Step 3: Create `_config.yml`**

```yaml
title: PapaG Studios
description: A small studio crafting delightful games and apps — built with care, without the tracking.
url: "https://papagstudios.com"
lang: en
# Static, no gimmicks
plugins: []
exclude:
  - Gemfile
  - Gemfile.lock
  - docs/
  - README.md
  - vendor/
# Pretty URLs: /numshift.html -> /numshift/
permalink: pretty
```

- [ ] **Step 4: Extract styles into `assets/css/style.css`**

Copy the **entire contents** of the current `<style>...</style>` block from `index.html` (lines 11–219, the `*{}` reset through the `@media` query) verbatim into `assets/css/style.css`. Do not edit them in this step — this is a pure move so the baseline looks identical. (New rules get appended in later tasks.)

- [ ] **Step 5: Create `_includes/head.html`**

```html
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{% if page.title %}{{ page.title }} — {% endif %}PapaG Studios</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600&family=JetBrains+Mono:wght@500;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">
```

- [ ] **Step 6: Create `_includes/nav.html`**

```html
<header class="site-nav">
  <a class="nav-logo" href="{{ '/' | relative_url }}"><span class="papa">Papa</span><span class="g">G</span></a>
  <nav class="nav-links">
    <a href="{{ '/#games' | relative_url }}">Games</a>
    <a href="{{ '/#apps' | relative_url }}">Apps</a>
  </nav>
</header>
```

- [ ] **Step 7: Create `_includes/footer.html`** (preserve the email-obfuscation script exactly)

```html
<footer class="footer">
  <p>
    Questions? <a href="#" class="eml" data-u="support" data-d="papagstudios.com">support at papagstudios.com</a>
    &nbsp;·&nbsp; <a href="{{ '/promise/' | relative_url }}">Our Promise</a>
  </p>
  <p class="footer-copy">&copy; 2026 PapaG Studios. All rights reserved.</p>
</footer>
<script>
document.querySelectorAll('a.eml').forEach(function(el){
  var a=el.dataset.u+'@'+el.dataset.d;
  el.href='mailto:'+a;
  el.textContent=a;
});
</script>
```

- [ ] **Step 8: Create `_layouts/default.html`**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  {% include head.html %}
</head>
<body>
  {% include nav.html %}
  <main class="container">
    {{ content }}
  </main>
  {% include footer.html %}
</body>
</html>
```

- [ ] **Step 9: Convert `index.html` to use the layout**

Replace the whole file with front-matter + the existing body content (hero + the single NumShift card + contact block as they are today — restructuring happens in Task 2). Top of file:
```html
---
layout: default
---
<div class="hero">
  <img src="{{ '/images/logo-gaming.png' | relative_url }}" alt="PapaG Studios" class="hero-avatar">
  <div class="hero-logo"><span class="papa">Papa</span><span class="g">G</span></div>
  <div class="hero-sub">Studios</div>
  <p class="hero-tagline">Independent games for iOS. Simple to learn, hard to put down, and zero tracking.</p>
</div>
```
(Keep the rest of the current body — the `section-label`, the NumShift `game-card`, and `contact` block — verbatim below the hero for now. Remove the old `<footer>`/`<script>` since the layout provides them. The `.container` wrapper now comes from the layout, so drop the outer `<div class="container">`.)

- [ ] **Step 10: Add `.site-nav` styles to `assets/css/style.css`**

Append:
```css
.site-nav {
  position: relative; z-index: 1;
  max-width: 680px; margin: 0 auto;
  display: flex; align-items: center; justify-content: space-between;
  padding: 1.25rem 1.5rem 0;
}
.nav-logo { font-family: 'JetBrains Mono', monospace; font-weight: 700; font-size: 1.1rem; text-decoration: none; }
.nav-logo .papa { color: var(--accent-green); }
.nav-logo .g { color: var(--accent-blue); }
.nav-links a { font-family: 'JetBrains Mono', monospace; font-size: 0.8rem; color: var(--text-dim); text-decoration: none; margin-left: 1.25rem; }
.nav-links a:hover { color: var(--heading); }
.footer-copy { margin-top: 0.4rem; }
```

- [ ] **Step 11: Build and verify the baseline**

Run:
```bash
bundle exec jekyll build
grep -q "hero-tagline" _site/index.html && grep -q "NumShift" _site/index.html && echo OK
```
Expected: `OK`. Also run `bundle exec jekyll serve` and eyeball `http://localhost:4000` — it should look like today's site plus a small top nav.

- [ ] **Step 12: Commit**

```bash
git add -A
git commit -m "build: migrate site to Jekyll (chrome in layouts/includes, styles extracted)"
```

---

### Task 2: Product catalog + home Games/Apps sections + NumShift → Live

Introduce `_data/products.yml` as the single source of truth, a reusable card include, and rebuild the home body into Games + Apps sections. Corrects NumShift to "Live". Deliverable: home renders both sections from data.

**Files:**
- Create: `_data/products.yml`, `_includes/product-card.html`
- Modify: `index.html` (replace the hardcoded card/label with data-driven sections + new tagline), `assets/css/style.css` (section + badge variants)

**Interfaces:**
- Consumes: `layout: default` (Task 1).
- Produces: `site.data.products` (list of product objects with keys: `slug, name, kind [game|app], status [live|soon], tagline, accent, cta_label, cta_url, has_page`); `product-card.html` (renders one product object passed as `include.product`).

- [ ] **Step 1: Create `_data/products.yml`**

```yaml
- slug: numshift
  name: NumShift
  kind: game
  status: live
  tagline: Slide rows and columns; merge tiles that sum to the target.
  accent: green
  cta_label: Available on the App Store
  cta_url: "https://apps.apple.com/app/numshift/id0000000000"   # TODO(open-question): real App Store URL
  has_page: true
- slug: battlekeep
  name: BattleKeep
  kind: game
  status: soon
  tagline: Capture towers, stream troops, conquer the map — illustrated tower conquest.
  accent: green
  cta_label: Coming to the App Store
  cta_url: ""
  has_page: true
- slug: abide
  name: Abide
  kind: app
  status: soon
  tagline: Scripture memory and a prayer journal — a calm, on-device faith toolset.
  accent: blue
  cta_label: Coming to the Mac App Store
  cta_url: ""
  has_page: true
```

- [ ] **Step 2: Create `_includes/product-card.html`**

```html
{% assign p = include.product %}
<a class="product-card accent-{{ p.accent }}" href="{% if p.has_page %}{{ p.slug | prepend: '/' | append: '/' | relative_url }}{% else %}#{% endif %}">
  <div class="pc-head">
    <span class="pc-name">{{ p.name }}</span>
    {% if p.status == 'live' %}<span class="pc-badge badge-live">Available Now</span>
    {% else %}<span class="pc-badge badge-soon">Coming Soon</span>{% endif %}
  </div>
  <p class="pc-tagline">{{ p.tagline }}</p>
  <span class="pc-cta">{{ p.cta_label }} &rarr;</span>
</a>
```

- [ ] **Step 3: Rewrite the home body in `index.html`**

Keep the front-matter and hero from Task 1, but update the tagline and replace everything below the hero with data-driven sections:
```html
---
layout: default
---
<div class="hero">
  <img src="{{ '/images/logo-gaming.png' | relative_url }}" alt="PapaG Studios" class="hero-avatar">
  <div class="hero-logo"><span class="papa">Papa</span><span class="g">G</span></div>
  <div class="hero-sub">Studios</div>
  <p class="hero-tagline">A small studio crafting delightful games and apps — built with care, without the tracking.</p>
</div>

<section id="games" class="catalog">
  <div class="section-label">Games</div>
  {% assign games = site.data.products | where: "kind", "game" %}
  {% for p in games %}{% include product-card.html product=p %}{% endfor %}
</section>

<section id="apps" class="catalog">
  <div class="section-label">Apps</div>
  {% assign apps = site.data.products | where: "kind", "app" %}
  {% for p in apps %}{% include product-card.html product=p %}{% endfor %}
</section>

<section class="promise-strip">
  <p>🔒 Privacy-first, always. <a href="{{ '/promise/' | relative_url }}">Read our promise &rarr;</a></p>
</section>
```

- [ ] **Step 4: Add card/section/badge styles to `assets/css/style.css`**

Append:
```css
.catalog { margin-bottom: 2.5rem; }
.product-card { display: block; text-decoration: none; color: inherit;
  background: var(--surface); border: 1px solid var(--border); border-radius: 12px;
  padding: 1.75rem; margin-bottom: 1rem; transition: border-color .2s, transform .2s; }
.product-card:hover { border-color: rgba(255,255,255,0.12); transform: translateY(-1px); }
.pc-head { display: flex; align-items: center; justify-content: space-between; margin-bottom: .6rem; }
.pc-name { font-family: 'JetBrains Mono', monospace; font-weight: 700; font-size: 1.2rem; color: var(--heading); }
.pc-tagline { font-size: .92rem; color: var(--text-dim); margin-bottom: 1rem; }
.pc-cta { font-size: .82rem; font-weight: 500; }
.pc-badge { font-family: 'JetBrains Mono', monospace; font-size: .65rem; font-weight: 700;
  letter-spacing: .05em; text-transform: uppercase; padding: .2rem .6rem; border-radius: 20px; }
.badge-live { color: var(--accent-green); background: rgba(52,211,153,.1); border: 1px solid rgba(52,211,153,.25); }
.badge-soon { color: var(--accent-amber); background: rgba(251,191,36,.1); border: 1px solid rgba(251,191,36,.2); }
.accent-green .pc-cta { color: var(--accent-green); }
.accent-blue .pc-cta { color: var(--accent-blue); }
.accent-purple .pc-cta { color: var(--accent-purple); }
.promise-strip { text-align: center; margin: 2.5rem 0; padding: 1.25rem; border: 1px solid var(--border); border-radius: 12px; }
.promise-strip a { color: var(--accent-green); text-decoration: none; }
```

- [ ] **Step 5: Build and verify the sections**

Run:
```bash
bundle exec jekyll build
for s in Games Apps NumShift BattleKeep Abide "Available Now" "Read our promise"; do
  grep -q "$s" _site/index.html || echo "MISSING: $s"
done; echo done
```
Expected: `done` with no `MISSING:` lines.

- [ ] **Step 6: Commit**

```bash
git add -A
git commit -m "feat: data-driven Games/Apps sections on home; NumShift marked live"
```

---

### Task 3: Product page layout + the three product pages

One `product` layout driven by front-matter; three thin product pages. Deliverable: `/numshift/`, `/battlekeep/`, `/abide/` render hero + status + CTA + features + screenshot slots + legal links.

**Files:**
- Create: `_layouts/product.html`, `numshift.html`, `battlekeep.html`, `abide.html`
- Modify: `assets/css/style.css` (product hero/features/gallery styles)

**Interfaces:**
- Consumes: `layout: default`; `site.data.products`.
- Produces: `layout: product` — expects page front-matter keys: `product` (slug matching products.yml), `pitch`, `features` (list of `{title, body}`), `screenshots` (list of image paths, may be empty).

- [ ] **Step 1: Create `_layouts/product.html`**

```html
---
layout: default
---
{% assign p = site.data.products | where: "slug", page.product | first %}
<article class="product accent-{{ p.accent }}">
  <div class="product-hero">
    {% if page.key_art %}<img class="product-art" src="{{ page.key_art | relative_url }}" alt="{{ p.name }} key art">{% endif %}
    <h1 class="product-name">{{ p.name }}</h1>
    <p class="product-tagline">{{ p.tagline }}</p>
    {% if p.status == 'live' %}<span class="pc-badge badge-live">Available Now</span>
    {% else %}<span class="pc-badge badge-soon">Coming Soon</span>{% endif %}
    <div class="product-cta">
      {% if p.cta_url != "" %}<a class="cta-btn" href="{{ p.cta_url }}">{{ p.cta_label }}</a>
      {% else %}<span class="cta-btn cta-disabled">{{ p.cta_label }}</span>{% endif %}
    </div>
  </div>

  <p class="product-pitch">{{ page.pitch }}</p>

  <div class="section-label">Features</div>
  <ul class="feature-list">
    {% for f in page.features %}
    <li><span class="feat-title">{{ f.title }}</span><span class="feat-body">{{ f.body }}</span></li>
    {% endfor %}
  </ul>

  {% if page.screenshots and page.screenshots.size > 0 %}
  <div class="section-label">Screenshots</div>
  <div class="shot-gallery">
    {% for s in page.screenshots %}<img src="{{ s | relative_url }}" alt="{{ p.name }} screenshot">{% endfor %}
  </div>
  {% endif %}

  <div class="product-legal">
    <a href="{{ p.slug | prepend: '/' | append: '/support/' | relative_url }}">Support</a>
    <a href="{{ p.slug | prepend: '/' | append: '/privacy/' | relative_url }}">Privacy</a>
  </div>
</article>
```

- [ ] **Step 2: Create `numshift.html`**

```html
---
layout: product
product: numshift
title: NumShift
key_art: /assets/img/numshift-hero.png
pitch: Slide entire rows and columns across a 5×5 board. When adjacent tiles sum to the target, they merge and you score. Easy to learn, fiendishly hard to master — and totally free of ads and tracking.
features:
  - title: One simple rule
    body: Rows and columns slide; sums that hit the target merge.
  - title: Zero ads, zero tracking
    body: No accounts, no analytics, no data collection. Ever.
  - title: Daily challenge
    body: A fresh board every day to chase your best score.
screenshots: []
---
```

- [ ] **Step 3: Create `battlekeep.html`**

```html
---
layout: product
product: battlekeep
title: BattleKeep
key_art: /assets/img/battlekeep-hero.png
pitch: Capture towers, stream troops between them, and conquer an illustrated map one keep at a time. Fast, tactile real-time strategy in a hand-drawn world.
features:
  - title: Living map
    body: Towers, obstacles, mines, and gates shape every push.
  - title: Illustrated world
    body: Hand-crafted castles and factions, not sterile geometry.
  - title: Fair monetization
    body: Light ads you can remove for a single $2.99 purchase — never a nag.
screenshots: []
---
```

- [ ] **Step 4: Create `abide.html`**

```html
---
layout: product
product: abide
title: Abide
key_art: /assets/img/abide-hero.png
pitch: A calm place to hide Scripture in your heart and keep a prayer journal. Abide pairs spaced-repetition memory work with a simple, private prayer practice — all on your own device.
features:
  - title: Memorize with confidence
    body: Cloze-deletion drills and spaced repetition make verses stick.
  - title: A quiet prayer journal
    body: Capture requests and answers in a distraction-free space.
  - title: Yours and private
    body: Your content lives on your device — no tracking, no selling data.
screenshots: []
---
```

- [ ] **Step 5: Add product-page styles to `assets/css/style.css`**

Append:
```css
.product-hero { text-align: center; margin-bottom: 2.5rem; }
.product-art { width: 100%; max-width: 420px; border-radius: 16px; margin-bottom: 1.25rem; }
.product-name { font-family: 'JetBrains Mono', monospace; font-size: 2rem; color: var(--heading); margin-bottom: .5rem; }
.product-tagline { color: var(--text-dim); max-width: 460px; margin: 0 auto 1rem; }
.product-cta { margin-top: 1.25rem; }
.cta-btn { display: inline-block; font-family: 'JetBrains Mono', monospace; font-size: .85rem; font-weight: 700;
  padding: .7rem 1.4rem; border-radius: 10px; text-decoration: none;
  color: var(--bg); background: var(--accent-green); }
.accent-blue .cta-btn { background: var(--accent-blue); }
.cta-disabled { background: var(--surface); color: var(--text-dim); border: 1px solid var(--border); cursor: default; }
.product-pitch { font-size: 1.05rem; margin-bottom: 2.5rem; }
.feature-list { list-style: none; margin-bottom: 2.5rem; }
.feature-list li { padding: 1rem 0; border-top: 1px solid var(--border); }
.feat-title { display: block; font-family: 'JetBrains Mono', monospace; color: var(--heading); font-weight: 700; font-size: .95rem; }
.feat-body { display: block; color: var(--text-dim); font-size: .9rem; margin-top: .25rem; }
.shot-gallery { display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 2.5rem; }
.shot-gallery img { width: 100%; border-radius: 12px; border: 1px solid var(--border); }
.product-legal { display: flex; gap: 1.5rem; justify-content: center; padding-top: 1.5rem; border-top: 1px solid var(--border); }
.product-legal a { color: var(--accent-green); text-decoration: none; font-size: .85rem; }
@media (max-width: 600px) { .shot-gallery { grid-template-columns: 1fr; } }
```

- [ ] **Step 6: Build and verify all three product pages**

Run:
```bash
bundle exec jekyll build
for p in numshift battlekeep abide; do
  test -f _site/$p/index.html && grep -q "product-hero" _site/$p/index.html || echo "BAD: $p"
done
grep -q "Available on the App Store" _site/numshift/index.html || echo "BAD: numshift cta"
grep -q "\$2.99" _site/battlekeep/index.html || echo "BAD: battlekeep monetization"
echo done
```
Expected: `done` with no `BAD:` lines. `key_art` images don't exist yet, so `<img>` tags will 404 in preview — that's expected; real art is added later (see Follow-ups).

- [ ] **Step 7: Commit**

```bash
git add -A
git commit -m "feat: product layout + NumShift/BattleKeep/Abide pages (data-driven)"
```

---

### Task 4: Promise page (privacy manifesto)

Deliverable: `/promise/` renders the three honest pledges.

**Files:**
- Create: `promise.html`
- Modify: `assets/css/style.css` (prose column styles, reused by legal pages in Task 5)

**Interfaces:**
- Consumes: `layout: default`.
- Produces: `.prose` styles (narrow readable column) used here and by `_layouts/legal.html` in Task 5.

- [ ] **Step 1: Create `promise.html`**

```html
---
layout: default
title: Our Promise
---
<article class="prose">
  <h1>Our Promise</h1>
  <p>PapaG is one person making software he'd want his own family to use. That shapes a few promises that hold across every game and app we ship.</p>

  <h2>No tracking. No selling your data. No dark patterns.</h2>
  <p>We don't run analytics that follow you, we never sell or share your data, and we don't design traps to squeeze you. What you do in our apps is your business.</p>

  <h2>Ads, where they exist, are minimal — and always removable.</h2>
  <p>Some titles show light ads to keep the lights on. When they do, a single honest one-time purchase removes them for good — never a nag, never a subscription. NumShift has no ads at all.</p>

  <h2>You own your stuff.</h2>
  <p>Your content lives on your device or in your own accounts. Abide keeps your Scripture progress and prayers on your device. Your data isn't our product.</p>

  <p class="prose-foot">Questions about any of this? <a href="#" class="eml" data-u="support" data-d="papagstudios.com">support at papagstudios.com</a></p>
</article>
```

- [ ] **Step 2: Add `.prose` styles to `assets/css/style.css`**

Append:
```css
.prose { max-width: 640px; }
.prose h1 { font-family: 'JetBrains Mono', monospace; font-size: 1.8rem; color: var(--heading); margin-bottom: 1.25rem; }
.prose h2 { font-size: 1.1rem; color: var(--heading); margin: 2rem 0 .5rem; }
.prose p { margin-bottom: 1rem; }
.prose a { color: var(--accent-green); text-decoration: none; }
.prose-foot { margin-top: 2rem; padding-top: 1.5rem; border-top: 1px solid var(--border); color: var(--text-dim); font-size: .9rem; }
```

- [ ] **Step 3: Build and verify**

Run:
```bash
bundle exec jekyll build
grep -q "No tracking" _site/promise/index.html && grep -q "always removable" _site/promise/index.html && echo OK
```
Expected: `OK`.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "feat: Promise page (honest privacy manifesto)"
```

---

### Task 5: Legal pages via a `legal` layout (all products)

Migrate NumShift's support/privacy into the shared layout (text preserved) and add BattleKeep + Abide legal pages. Deliverable: every product's Support and Privacy pages build and link from its product page.

**Files:**
- Create: `_layouts/legal.html`
- Modify: `numshift/support.html`, `numshift/privacy.html` (wrap in layout, preserve text)
- Create: `battlekeep/support.html`, `battlekeep/privacy.html`, `abide/support.html`, `abide/privacy.html`

**Interfaces:**
- Consumes: `layout: default`, `.prose` styles (Task 4).
- Produces: `layout: legal` (renders `{{ content }}` inside a `.prose` column).

- [ ] **Step 1: Create `_layouts/legal.html`**

```html
---
layout: default
---
<article class="prose">
  {{ content }}
</article>
```

- [ ] **Step 2: Migrate `numshift/support.html`**

Open the existing file, take the human-readable support text from inside its `<body>` (everything that isn't the `<head>`/`<style>`/`<script>` boilerplate), and replace the whole file with:
```html
---
layout: legal
title: NumShift Support
---
<h1>NumShift — Support</h1>
<!-- paste the existing support body text here as <p>/<h2> markdown-ish HTML, verbatim -->
```
Preserve the existing contact address and wording exactly. Do not invent new policy text.

- [ ] **Step 3: Migrate `numshift/privacy.html`** the same way

```html
---
layout: legal
title: NumShift Privacy Policy
---
<h1>NumShift — Privacy Policy</h1>
<!-- paste the existing privacy body text here, verbatim -->
```

- [ ] **Step 4: Create `battlekeep/support.html` and `battlekeep/privacy.html`**

`battlekeep/support.html`:
```html
---
layout: legal
title: BattleKeep Support
---
<h1>BattleKeep — Support</h1>
<p>Need help with BattleKeep? Email <a href="#" class="eml" data-u="support" data-d="papagstudios.com">support at papagstudios.com</a> and we'll get back to you.</p>
<p>BattleKeep is coming soon to the App Store. This page will grow with FAQs at launch.</p>
```

`battlekeep/privacy.html` (honest: names the ad/IAP reality):
```html
---
layout: legal
title: BattleKeep Privacy Policy
---
<h1>BattleKeep — Privacy Policy</h1>
<p>BattleKeep does not collect personal information for its own use, run its own analytics, or sell your data.</p>
<p>BattleKeep shows light ads. Ads are served by a third-party ad network, which may use device identifiers per its own policy; a one-time in-app purchase removes ads entirely. Purchases are handled by Apple — we never see your payment details.</p>
<p>Questions? <a href="#" class="eml" data-u="support" data-d="papagstudios.com">support at papagstudios.com</a>.</p>
<p class="prose-foot">This policy will be finalized with the specific ad-network disclosure before App Store submission.</p>
```

- [ ] **Step 5: Create `abide/support.html` and `abide/privacy.html`**

`abide/support.html`:
```html
---
layout: legal
title: Abide Support
---
<h1>Abide — Support</h1>
<p>Questions about Abide? Email <a href="#" class="eml" data-u="support" data-d="papagstudios.com">support at papagstudios.com</a>.</p>
<p>Abide is coming soon to the Mac App Store. This page will grow with FAQs at launch.</p>
```

`abide/privacy.html`:
```html
---
layout: legal
title: Abide Privacy Policy
---
<h1>Abide — Privacy Policy</h1>
<p>Abide keeps your content — memorized Scripture progress and prayer-journal entries — on your device. It does not run analytics, require an account, track you, or sell data.</p>
<p>If any optional cloud sync or backup is added later, this policy will be updated to describe exactly what is stored and where, before that feature ships.</p>
<p>Questions? <a href="#" class="eml" data-u="support" data-d="papagstudios.com">support at papagstudios.com</a>.</p>
```

- [ ] **Step 6: Build and verify all legal pages resolve**

Run:
```bash
bundle exec jekyll build
for p in numshift battlekeep abide; do
  for kind in support privacy; do
    test -f _site/$p/$kind/index.html || echo "MISSING: $p/$kind"
  done
done; echo done
```
Expected: `done` with no `MISSING:` lines. (Preserved URLs: `/numshift/support/` and `/numshift/privacy/` now exist via `permalink: pretty`.)

- [ ] **Step 7: Commit**

```bash
git add -A
git commit -m "feat: shared legal layout; BattleKeep + Abide support/privacy; migrate NumShift legal"
```

---

### Task 6: Link integrity, responsive nav, final verification

Verify the whole site holds together and looks right on mobile. Deliverable: `html-proofer` passes (ignoring not-yet-existing art) and nav/layout work at phone width.

**Files:**
- Modify: `assets/css/style.css` (mobile nav tweak)
- Modify: `_config.yml` (only if html-proofer needs ignore rules)

- [ ] **Step 1: Add a mobile nav rule to `assets/css/style.css`**

Append inside/near the existing `@media (max-width: 600px)` block (add the block if the extracted CSS put it elsewhere):
```css
@media (max-width: 600px) {
  .site-nav { padding: 1rem 1.25rem 0; }
  .nav-links a { margin-left: 1rem; font-size: .75rem; }
  .product-name { font-size: 1.6rem; }
}
```

- [ ] **Step 2: Build and run html-proofer (ignore missing art + external store URLs)**

Run:
```bash
bundle exec jekyll build
bundle exec htmlproofer _site \
  --ignore-urls "/apps.apple.com/" \
  --ignore-missing-alt false \
  --swap-urls '^/assets/img/.*:' \
  --disable-external
```
Expected: passes, or reports ONLY missing `/assets/img/*` product art (expected until art is added). Any broken internal page link is a real failure — fix it. (If the `--swap-urls` form errors on your html-proofer version, instead run without it and confirm the only failures are the known `assets/img/*hero.png` files.)

- [ ] **Step 3: Manual mobile check**

Run `bundle exec jekyll serve`, open `http://localhost:4000` at ~390px width (browser devtools). Confirm: nav fits, cards stack, product pages readable, footer email resolves on click, `/promise/` and each product's Support/Privacy link works.

- [ ] **Step 4: Commit**

```bash
git add -A
git commit -m "polish: responsive nav + link-integrity pass"
```

---

## Follow-ups (not blocking this build)

- **Product art via Higgsfield:** generate `assets/img/{numshift,battlekeep,abide}-hero.png` + screenshots, drop them in; `<img>` slots already wired. Use the established gaming-icon / illustrated styles.
- **Real NumShift App Store URL:** replace the `TODO` placeholder in `_data/products.yml`.
- **Finalize BattleKeep privacy** with the specific ad-network name before submission.
- **`/about` page:** parked; add a nav slot + one `layout: default` page when ready.
- **Go-live:** open a PR from `feature/site-refresh`, review, merge to `main`; GitHub Pages rebuilds automatically.

## Self-Review Notes

- **Spec coverage:** positioning/tagline (T2), IA/routes+nav (T1–T5), Jekyll build approach (T1), home + product + card templates (T2–T3), catalog+statuses incl. NumShift fix (T2), visual direction/dark aesthetic (T1 extract + per-task styles), Promise honest framing (T4), per-product legal incl. preserved NumShift URLs (T5). Higgsfield art = deliberately deferred to Follow-ups (art generation isn't code). `/about` = deferred per spec.
- **Placeholder scan:** the only intentional `TODO` is the real App Store URL (an explicit spec open-question) and the deferred art files; both are called out, not silent.
- **Type/name consistency:** `_data/products.yml` keys (`slug,name,kind,status,tagline,accent,cta_label,cta_url,has_page`) are used identically in `product-card.html`, `index.html`, and `_layouts/product.html`. Layouts referenced (`default`, `product`, `legal`) are each created before use.
