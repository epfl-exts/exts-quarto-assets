# Quarto Assets for the EPFL DEXTS <a href="https://www.epfl.ch/education/continuing-education/"><img src="https://raw.githubusercontent.com/epfl-exts/exts-quarto-assets/main/logos/Extension-school-logo-2023-positif.png" align="right" height="138"/></a>

Stylesheets, logos and starter templates for making Quarto documents and slide
decks that look like the Digital Extension School.

You do not need to clone this repo to use it — the stylesheets are served over
the web, so a single line in your YAML header is the whole setup.

## 👀 See the theme first

**[Open the showcase deck →](https://epfl-exts.github.io/exts-quarto-assets/examples/exts-reveal-showcase.html)**

Every class, what it looks like, and the markdown that produces it. Start there,
find the thing you want, copy the source.

## Slide decks (revealjs)

Copy this into your deck's YAML header:

``` yaml
---
title: "Your title"
format:
  revealjs:
    css: https://epfl-exts.github.io/exts-quarto-assets/themes/exts-reveal.css
---
```

A fuller starting point, with two columns, speaker notes and content boxes
already wired up, is in [`templates/slides.qmd`](./templates/slides.qmd).

### The classes

Use these as fenced divs — `::: {.content-box-marine}` … `:::`

| Class | Write it as | Does |
|---|---|---|
| `.content-box-marine` | fenced div | Teal box — the point you want remembered |
| `.content-box-oil` | fenced div | Dark grey box — supporting detail |
| `.content-box-white` | fenced div | White box — nests inside the other two |
| `.box-title` | `[Title]{.box-title}` | Heading inside a box |
| `.box-body` | `[Text]{.box-body}` | Body text inside a box |
| `.subsection` | `# Title {.subsection}` | Full-bleed oil divider slide |
| `.subsubsection` | `## Title {.subsubsection}` | Full-bleed marine divider slide |
| `.small` / `.tiny` | fenced div | 0.8× / 0.6× body text |
| `.footnote` | fenced div | 0.6× body text, grey |

> [!IMPORTANT]
> Inside a content box use `[Title]{.box-title}`, **never a `###` heading**.
> Pandoc wraps every heading in its own `<section>`, which revealjs treats as a
> new slide — so a box built from headings silently loses everything after the
> first one.

### Using it as a Quarto theme instead

`css:` layers plain CSS over the stock theme and is enough for most decks.
For full control — fonts, heading colours, the title slide — copy
[`exts-reveal.scss`](./themes/exts-reveal.scss) next to your deck and use
`theme:` instead. Quarto will not resolve `theme:` over `http`, so it has to be
a local file:

``` yaml
format:
  revealjs:
    theme: [default, exts-reveal.scss]
```

## HTML documents

``` yaml
---
title: "Your title"
format:
  html:
    theme: simplex
    toc: true
    toc-location: right
    title-block-banner: "#413D3A"
    title-block-banner-color: "White"
    embed-resources: true
css: https://epfl-exts.github.io/exts-quarto-assets/themes/styles-html.css
---
```

The stylesheet places the school logo in the title banner. A complete starting
point is in [`templates/document.qmd`](./templates/document.qmd).

![](./images/html-document.png)

## What's in here

| Directory | Contents |
|---|---|
| `themes/` | The stylesheets. These are the files your deck loads by URL. |
| `templates/` | Thin copy-me starters — take the YAML, delete the filler. |
| `examples/` | The showcase deck: exhaustive, rendered, meant to be read. |
| `logos/` | School logos, referenced by URL from `styles-html.css`. |

## Changing the theme

See [CONTRIBUTING.md](./CONTRIBUTING.md). The short version: `themes/exts-reveal.css`
is committed rather than built on demand, so after editing the `.scss` you must
recompile it — and pushing to `main` publishes to every deck immediately.
