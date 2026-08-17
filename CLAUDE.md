# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of Quarto assets (stylesheets, logos, starter templates) giving EPFL
Digital Extension School documents and slide decks the school's visual identity.
There is no application and no test suite — the deliverable is the CSS itself plus
the YAML headers that pull it in.

**[CONTRIBUTING.md](./CONTRIBUTING.md) is the maintainer guide** and covers the
build step, the `css:`/`theme:` split, and the colour rules in full. Read it
before editing the theme; don't duplicate its content here.

## The constraint that shapes everything

GitHub Pages serves this repo from the **root of `main`** (no `docs/`, no
workflow, no `gh-pages` branch), and the templates reference the stylesheets by
their public URL rather than a relative path. So `themes/` and `logos/` are
frozen public API — never move, rename, or reorganise anything inside them, and
treat any edit to a committed `.css` as a change to published output that reaches
every downstream deck on push.

## Build and verify

```bash
sass themes/exts-reveal.scss themes/exts-reveal.css   # required after any .scss edit
quarto render examples/exts-reveal-showcase.qmd       # the theme's only regression test
```

`themes/exts-reveal.css` is committed, not built on demand — it is what decks load
over http. `themes/exts-reveal.css.map` is committed too and is referenced by a
`sourceMappingURL` comment at the end of the `.css`; don't delete it.

`themes/styles-html.css` has no Sass source; edit it directly.

To review the showcase deck, open the rendered HTML in a browser. Reviewing via
`?print-pdf` is misleading: print mode builds no `.backgrounds` container, so
`.subsection`/`.subsubsection` divider slides come out blank white in the PDF
while being correct in normal view. (Headless Chrome can screenshot a slide
reliably by rendering it as the first slide of a scratch deck; navigating by
`#/n` hash does not work headlessly.)

## Layout

- `themes/exts-reveal.scss` → `themes/exts-reveal.css` — the revealjs theme. Source of truth.
- `themes/styles-html.css` — HTML documents; paints the logo into the Quarto title banner.
- `templates/` — thin copy-me starters. Keep minimal; the exhaustive material belongs in `examples/`.
- `examples/exts-reveal-showcase.qmd` + committed `.html` — demonstrates every class next to its source, and doubles as the visual regression check.
- `logos/` — referenced by raw.githubusercontent.com URLs from `styles-html.css`.

## Gotchas worth knowing before you edit

- **Only `scss:rules` survives compilation to CSS.** Variables in `scss:defaults`
  vanish and matter only to `theme:` users, which is why the rules section repeats
  literal hex codes rather than interpolating the brand variables. Not an oversight.
- **The white-box overrides are specificity-critical.** `.reveal section.subsection strong`
  is (0,2,2) while `.reveal .content-box-white strong` is (0,2,1), so the divider
  selectors are spelled out explicitly. Adding a new dark surface without its
  matching white-box override reintroduces invisible white-on-white inline code.
- **`$exts-marine` (#00a79f) is deliberately not `$exts-discover-turquoise` (#35d48d)** —
  pre-2023 deck teal vs current identity green.
- **Oil and marine are not both "dark".** Luminance 0.083 vs 0.301. Oil takes a
  light accent (bold `#e2b3e3`, links `#7fe3b6`); marine is a mid-tone where no
  hue clears 3.0, so bold is white and links are white-underlined. Compute the
  contrast ratio against the actual surface before adding any accent — this is
  documented with the measurements in CONTRIBUTING.md.
- **Fenced divs need increasing colon counts as they nest.** Equal counts work
  until a div has two children, then the column renders empty with no error.
- **`code-overflow: wrap` does nothing for revealjs.** To stop long lines
  clipping, put `{.smaller}` on the slide.
