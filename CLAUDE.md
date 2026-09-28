# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Personal consulting site and blog for Michal Mottl (https://mottl.io), built with **Quarto** (`project: type: website`, Bootstrap `default` theme customised in `theme.scss`) and deployed to Netlify.

## Commands

```bash
quarto preview            # live-reload dev server
quarto render             # full build into _site/
quarto render about.qmd   # render a single page
quarto publish netlify    # manual publish (target in _publish.yml)
```

There are no tests or linters. Netlify also builds on push via `@quarto/netlify-plugin-quarto` (`netlify.toml`, `package.json`); changes land through PRs to `main`.

## Architecture

- **Pages**: `index.qmd` (homepage: `page-layout: custom`, written as one raw HTML block so the full-bleed sections and the SVG loop diagram render exactly; styles are the `.home` rules in `theme.scss`), `services.qmd` ("Work with me"), `about.qmd`, `posts.qmd` (listing page that auto-indexes everything under `posts/`, with RSS feed). Navbar is defined in `_quarto.yml`.
- **Posts**: one directory per post under `posts/`, containing a `.qmd` or `.ipynb` plus its images. Front matter needs `title`, `date`, `categories`, `description` for the listing.
- **Freeze**: `execute: freeze: auto` — computational output is cached in `_freeze/` and that directory is **committed**. Netlify builds do not re-execute code (R/knitr is not available there), so if you change code in a post with executable chunks, render it locally and commit the updated `_freeze/` output. `renv` is present but not activated (`.Rprofile` has the activate line commented out).
- **`_site/`** is build output and gitignored (a few stale files are still tracked; don't edit them).

## Bilingual content (EN/PL)

The site is bilingual via a client-side toggle, not Quarto's multi-language support:

- Page content is duplicated in Pandoc fenced divs `::: {.lang-en}` and `::: {.lang-pl}`. **Any copy change to `index.qmd`, `services.qmd`, or `about.qmd` must be made in both languages** (on the homepage these are `class="lang-en"`/`class="lang-pl"` HTML elements) and the two blocks should stay structurally parallel.
- `_includes/lang-toggle.html` (injected via `include-after-body`) shows/hides those divs, remembers the choice in `localStorage` (`mottl_lang`), and defaults to Polish for Polish IPs (ipapi.co lookup) or a `pl*` browser language.
- Navbar labels are translated in the `applyNav` map inside `lang-toggle.html`, keyed by path — if you add or rename a navbar page in `_quarto.yml`, update that map too.
- Blog posts are English-only (the PL nav label is "Blog (EN)").
- The switch is moved into the navbar by that script; its styling (`.lang-toggle`) and all colour/font tokens live in `theme.scss`. SVG text on the homepage is translated the same way (`<text class="lang-en">`).
