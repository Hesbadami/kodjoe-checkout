# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of standalone, hand-authored **static HTML** documents for KodjoEnglish (kodjoe.com) — checkout pages, pricing tables, and transactional emails. There is **no build system, package manager, framework, or test suite**: each `.html` file is self-contained (inline `<style>`, no shared CSS/JS files) and is pasted into / served by external platforms. To preview a file, open it directly in a browser. The PDF folders (`Checkout PDF designs/`, `Email PDF designs/`) are the source design mockups the HTML implements — consult them when a layout question arises.

The content is in **French** (the audience is francophone). Keep all user-facing copy in French and preserve euro (`€`) pricing.

## The product / commerce model

Pages drive sales of KodjoEnglish "Passe" plans through **SamCart** (subdomain `kodjoenglish`), then hand off to the **Kopilot** app for student registration.

- **Plans (tiers), cheapest → most expensive:** `solo`/`etudiant` → `gold` → `platinum` → `diamond` (a.k.a. `diamant`).
- **Checkout embed:** every checkout page mounts SamCart via `<script src="https://static.samcart.com/checkouts/sc-checkout.js">` plus a `<sc-checkout product="..." subdomain="kodjoenglish" coupon="">` web component. The `product` attribute is the SamCart product slug and is the single source of truth for what is sold (e.g. `gold`, `gold-6x`, `passe-gld-6m-3x`, `after-6-months-gold`). When adding/duplicating a checkout, the product slug is the thing that must be correct.
- **Post-purchase handoff:** email CTAs link to `https://kopilot.kodjoe.com/register?orderid=##order_id##&email=##email##&plan=<tier>`. `##order_id##` and `##email##` are **SamCart merge tags** substituted at send time — do not hardcode them.

## Directory layout & file-naming conventions

- `Checkout HTMLs/` — current single-payment checkout pages, one per tier (`gold.html`, `platinum.html`, `diamond.html`, `etudiant.html`, `solo.html`).
- `PASSE 2-1 Checkout HTMLS/` — a separate "2-for-1" checkout campaign; same tiers, different offer/copy.
- `Email HTMLs/` — transactional receipt/next-step emails, one per tier (`gold_email.html`, etc.). Built as nested `<table role="presentation">` layouts with inline styles (email-client compatibility) — keep that structure; do not convert to flexbox/grid.
- `Pricing Table/` + top-level `pricing_final.html`, `pricing.html`, `main_page_table.html` — marketing pricing/comparison tables embedded on the main site.
- **Filename suffixes carry meaning:**
  - `_6x` = installment-plan variant of a checkout (adds discount/savings UI rows on top of the base file).
  - `_raw` = the same checkout **without** the testimonials block (Wistia video embeds + quotes).
  - `_only` = a single-tier slice of the full pricing table.

These suffixed files are near-duplicates of their base file. When changing shared markup/styles, apply the edit to **every variant** (base, `_6x`, `_raw`) so they don't drift.

## Conventions to follow when editing

- **BEM-style class names, namespaced per page**, e.g. `passe-checkout__card`, `passe-testimonials__title`, `kodjoe-simple-pricing-wrapper`. Match the existing prefix of the file you're in.
- Pricing-table styles use a wrapper class and `!important` on nearly every rule (`kodjoe-simple-pricing-*`) to survive injection into the host CMS. Preserve `!important` in those files.
- Fonts/icons load from CDNs (Google Fonts `Poppins`, `bootstrap-icons`); brand assets and testimonial videos come from `kopilot.kodjoe.com/static/...` and `fast.wistia.net`. Reuse the existing URLs rather than inventing new asset paths.
- Checkout pages include an inline `setInterval` script that reaches into the SamCart `iframe.contentDocument` to restyle SamCart's modal/backdrop. It is intentionally fragile (depends on SamCart's hashed class names like `ui-library-styles-*`); leave it unless specifically fixing checkout-modal styling.
