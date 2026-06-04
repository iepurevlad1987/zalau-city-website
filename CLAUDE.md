# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page static website about Zalău (Sălaj County, Romania). Plain HTML, CSS, and vanilla JavaScript — **no build step, no package manager, no dependencies**. The only external resource is Google Fonts (Playfair Display + Inter), loaded via `<link>` in `index.html`.

## Running

Open `index.html` directly, or serve it (recommended so behavior matches production):

```bash
python -m http.server 8000   # then open http://localhost:8000
```

There are no tests, no linter, and no build/lint commands. "Deploying" is copying the three files to any static host.

## Architecture

Three files do everything:

- `index.html` — all content and structure. It is the single source of truth for what appears on the page; sections use `id`s (`#about`, `#history`, `#attractions`, `#facts`, `#visit`) that the nav anchors link to.
- `css/style.css` — all styling. Built on a CSS custom-property design system declared in `:root` (colors `--brand`/`--accent`/`--ink`, `--radius`, `--shadow`, `--max` width). **Change the palette or spacing there, not inline.** Layout is CSS Grid with `repeat(auto-fit, minmax(...))` so card/stat/visit grids reflow responsively without media queries; the one `@media (max-width: 760px)` block only handles the mobile nav drawer.
- `js/main.js` — all behavior, plain DOM APIs, no framework. Four independent features run on load.

### Conventions that span files (the non-obvious parts)

- **Reveal-on-scroll**: `main.js` adds the `.reveal` class to every `.section`, `.card`, and `.timeline li` at runtime, then an `IntersectionObserver` adds `.in` when each scrolls into view. The CSS for both classes lives under `/* Reveal animation */`. New sections/cards animate automatically — no markup change needed — but anything that must be visible without JS should not rely on this (elements start at `opacity: 1` in CSS and are only hidden once JS adds `.reveal`).
- **Animated stat counters**: stat numbers are driven by `data-target` (and optional `data-suffix`) attributes on `.stat-num` spans in `index.html`. `main.js` reads those attributes and counts up when the stats section enters view. To change a number or its suffix (e.g. `"+"`, `" m"`), edit the data attributes — the visible `0` is just a placeholder.
- **Mobile nav**: the `.nav-toggle` button toggles `.open` on `.nav-links`; clicking any link closes it. `aria-expanded` is kept in sync in JS.

All JS feature blocks are guarded (`if (el)` / no-op if absent), so adding or removing a section won't throw.
