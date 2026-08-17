# Contributing

Notes for anyone editing the theme itself. If you just want to *use* it, the
[README](./README.md) is the right file.

## The one thing that catches everyone

`themes/exts-reveal.css` is **committed to the repo, not built on demand.** It
is the file decks load over `http`. The `.scss` is only the source.

``` bash
sass themes/exts-reveal.scss themes/exts-reveal.css
```

Edit the `.scss` without running that and your change reaches nobody — the
`.scss` sits there looking correct while every deck keeps loading the old CSS.

`sass` also writes `themes/exts-reveal.css.map` and the compiled CSS ends with a
`sourceMappingURL` comment pointing at it. Both are committed on purpose: the
`.scss` is published too, so browser devtools maps the live stylesheet back to
real `.scss` line numbers. Commit all three files together.

## There is no staging

GitHub Pages serves this repo from the root of `main`. Decks across the school
hardcode:

- `https://epfl-exts.github.io/exts-quarto-assets/themes/exts-reveal.css`
- `https://raw.githubusercontent.com/.../logos/Extension-school-logo-2023-negatif-red.png`

So **`themes/` and `logos/` are a public API.** Do not move, rename, or
reorganise anything inside them. A push to `main` is a release: it reaches every
existing deck at once, including ones being presented that afternoon.

Everything else in the repo can be reorganised freely.

## `css:` and `theme:` are not equivalent

|  | `css:` (what the templates use) | `theme:` |
|---|---|---|
| Written as | `css: https://…/exts-reveal.css` | `theme: [default, exts-reveal.scss]` |
| Remote URL? | yes | **no** — Quarto needs a local path, so the `.scss` must be copied next to the deck |
| What takes effect | only the `scss:rules` section | the whole file, including `scss:defaults` |

This is why the `scss:rules` section repeats literal hex codes instead of
interpolating `$exts-marine` and friends. Sass variables are a compile-time
concept — they vanish when the file becomes CSS, so they only exist for the
minority of decks using `theme:`. **Anything that must work for everyone has to
live in `scss:rules` as a literal value.** It looks redundant; it isn't.

## Two colour decisions that look like bugs

**Marine is not the brand green.** `$exts-marine` (`#00a79f`) is the teal the
older xaringan workshop decks were built against. `$exts-discover-turquoise`
(`#35d48d`) is the 2023 identity green. They are deliberately separate variables
so that ported decks look like the originals. `.content-box-marine` still uses
marine. Switch it when the slides are re-branded, not before.

**Bold text changes colour by surface.** It is EXTS red (`#cf0000`) normally and
brand purple (`#b741ba`) on dark surfaces — the dark content boxes and the
divider slides — because red-on-dark fails contrast. A `.content-box-white`
nested inside a dark box is a light surface again, so it flips back to red.

### Specificity, not source order

The white-box-inside-dark-box override is decided by **specificity**, and the
selectors are not as symmetrical as they look:

| Selector | Specificity |
|---|---|
| `.reveal .content-box-marine strong` | (0,2,1) |
| `.reveal section.subsection strong` | (0,2,**2**) — `section.subsection` adds an element |
| `.reveal .content-box-white strong` | (0,2,1) |

The bare white-box selector beats the *box* rule but loses to the *divider*
rule. That is why `.reveal section.subsection .content-box-white strong` and its
`subsubsection` twin are written out explicitly. Without them, bold in a white
box on a divider slide comes out purple, and inline code in that box renders
white-on-white — invisible.

If you add a new dark surface, add the matching explicit white-box override at
the same time.

## Adding or changing a class

1. Edit `themes/exts-reveal.scss`.
2. Recompile: `sass themes/exts-reveal.scss themes/exts-reveal.css`
3. Add or update a slide in `examples/exts-reveal-showcase.qmd` demonstrating it.
4. Re-render: `quarto render examples/exts-reveal-showcase.qmd`
5. Look at the result. The showcase deck is the theme's only regression test.
6. Update the class table in the README.
7. Commit the `.scss`, `.css`, `.css.map`, `.qmd` and `.html` together.

### Reviewing the showcase deck

Open `examples/exts-reveal-showcase.html` in a browser and step through it.

Do **not** review it via `?print-pdf` alone. Print mode does not build reveal's
`.backgrounds` container, so the `.subsection` and `.subsubsection` divider
slides come out blank white in the PDF even when they are perfectly fine in the
browser. Check dividers in normal view.

## Writing slides that survive

Inside a content box use `[Title]{.box-title}`, never `###`. Pandoc wraps every
heading in its own `<section>` and revealjs treats each `<section>` as a slide,
so a box built from headings silently drops everything after the first heading.

Fenced divs need **increasing colon counts** as they nest:

``` markdown
::::: {.columns}
:::: {.column width="50%"}
::: {.content-box-marine}
Text.
:::
::::
:::::
```

Equal counts appear to work until a div has two children, at which point the
first `:::` closes the wrong thing and the column renders empty with no error.

## Repo layout

| Path | |
|---|---|
| `themes/` | Stylesheets. **Frozen paths** — served by URL. |
| `templates/` | Thin copy-me starters. Keep them minimal. |
| `examples/` | The showcase deck. Keep it exhaustive. |
| `logos/` | **Frozen paths** — referenced from `styles-html.css`. |
| `.nojekyll` | Stops GitHub Pages running the committed HTML through Jekyll. |

`README.md` used to be generated from a `README.qmd`. It no longer is — it is
plain Markdown now, edit it directly.
