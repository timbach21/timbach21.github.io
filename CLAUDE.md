# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

Timothy Bach's personal portfolio site, deployed as a GitHub Pages user site
(`origin` = `timbach21/timbach21.github.io`). It is a hand-written static site:
no build step, no package manager, no test suite, no dependencies to install.
Every file at the repo root is served verbatim.

## Running it

Open `index.html` directly in a browser, or serve the root over HTTP so relative
paths and the Font Awesome CDN behave as in production:

```bash
python -m http.server 8000   # then http://localhost:8000
```

## Branches and deploy

- `Dev` — working branch (current default for edits).
- `main` — integration branch; `origin/HEAD` points here.
- `Live` — receives `main` via pull request (`Merge pull request #N from timbach21/main`) and is the published branch. Nothing is committed to it directly.

So the flow is `Dev` → `main` → PR → `Live`. Do not push straight to `Live`.

## Structure

Three hand-authored pages, all sharing one stylesheet:

- `index.html` — single-page site with `#home` / `#projects` / `#skills` / `#contact` sections.
- `blog.html` — index of posts; each post is its own full HTML file.
- `Adventure-Engine.html` — a post. New posts follow this pattern: copy the page, then add a `.blog-preview` `<article>` to `blog.html`.

`styles.css` is the single stylesheet for all pages, driven by CSS custom
properties on `:root` (`--primary-color`, `--gray-100`, …). Change colors there,
not in the pages. It carries page-specific blocks (`.blog-*`,
`.adventure-container`) alongside the shared ones.

Images live in `images/` (the only subdirectory; everything else is flat).
`images/hero.jpg` is the homepage hero photo, downscaled from a 4032x3024
original to 1600x1200 / ~260 KB. Downscale new photos the same way rather than
committing camera-resolution files.

`script.js` is loaded only by `index.html` (the blog pages have no script tag),
so navbar show/hide-on-scroll and smooth anchor scrolling are homepage-only
behavior.

Every page duplicates the same `<nav class="navbar">` markup. A nav change has to
be applied to all three by hand — there is no include mechanism. Note the anchor
prefixes differ: `index.html` uses bare `#skills`, the sub-pages use
`index.html#skills`.

## Content currently commented out

The Projects section, its nav link, the "View My Work" CTA, and the Blog nav link
are wrapped in HTML comments in all three pages (see commit `ebbccd8` "Hide
projects"). They are intentionally hidden, not dead code — restore by
uncommenting rather than rewriting. The blog pages remain reachable by direct URL
while the nav link is hidden.

## Known breakage in script.js

`script.js:54-55` looks up `#contact-form`, which no longer exists in
`index.html`. `contactForm.addEventListener` throws a TypeError on every page
load, killing the rest of the script — which is currently harmless only because
that block is last. Guard the lookup before appending any code below it.

## Favicon

`favicon.svg` is the only committed icon and the only one referenced by the
pages. `generate_favicon.js` (Node, `svg2img`) and `generate_favicons.py`
(`cairosvg` + Pillow) are two alternative one-off scripts that rasterize it to
PNG/ICO. Their outputs are not committed and neither script is wired into
anything.
