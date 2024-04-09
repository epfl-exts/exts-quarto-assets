

# Quarto Assets for the EPFL Digital Extension School <a href="https://www.epfl.ch/education/continuing-education/"><img src="https://raw.githubusercontent.com/epfl-exts/exts-quarto-assets/main/logos/Extension-school-logo-2023-negatif-red.png" align="right" height="138"/></a>

This repo contains a series of assets to create documents with the
Digital Extension School visual identity.

## HTML Documents

To create the

``` yaml
title: "Your Title Here"
subtitle: "Your Subtitle Here"
format: 
  html:
    theme: simplex
    number-sections: true
    code-fold: show
    highlight-style: zenburn
    toc: true
    df-print: paged
    toc-location: right
    toc-float:
      smooth-scroll: true
    title-block-banner: "#413D3A"
    title-block-banner-color: "White"
    embed-resources: true
css: https://epfl-exts.github.io/exts-quarto-assets/themes/styles-html.css
```

![](/images/image-document.png)
