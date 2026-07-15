# Formulator

A standalone, single-file web app that turns a LaTeX formula into a downloadable SVG — entirely in the browser, no backend required.

Type something like:

```
\int_{-\infty}^{\infty} e^{-x^2}\,dx = \sqrt{\pi}
```

… and get a clean, scalable vector graphic you can drop into a slide deck, a blog post, or a design tool.

## Features

- **Live preview** — renders as you type, powered by [MathJax](https://www.mathjax.org/) in SVG output mode (true vector paths, not HTML/CSS — so the exported file is portable and font-independent).
- **Display or inline mode** for different formula layouts.
- **Customizable appearance** — font size, text color, optional solid background color.
- **Dark-mode-aware export** — embeds a `prefers-color-scheme` media query directly in the SVG, so the formula automatically switches color to match the viewer's system/browser theme. Includes a light/dark toggle in the preview to check both variants before exporting.
- **Two export options** — download as a `.svg` file, or copy the raw SVG markup to the clipboard.
- **Zero dependencies to install** — a single HTML file that loads MathJax from a CDN. Just open it in a browser.

## Usage

### Locally

Open formulator.html directly in any modern browser. That’s it – no build step, no server.

Alternatively try the always up-to-date [app on GitHub Pages](https://607011.github.io/Formulator/).


## Dark mode notes

Two different mechanisms matter depending on how the SVG is embedded elsewhere:

- **OS/browser-level dark mode** — handled automatically via the embedded `@media (prefers-color-scheme: dark)` rule. Works even when the SVG is referenced via `<img src="...">`, since the media query is evaluated against the real environment, not the embedding page.
- **Manual/toggle-based dark mode** (a `.dark` class on `<html>`, common on many sites) — this can't reach into an `<img>`-embedded SVG. To support it, inline the SVG's markup directly into the host page's HTML; its paths use `fill="currentColor"`, so the surrounding page's CSS can override the color via the `.formulator-formula` class.

## Tech details

- Rendering engine: [MathJax 3](https://www.mathjax.org/) (`tex-svg` combined component), configured with the `ams` and `boldsymbol` packages.
- Exported SVGs use proper namespace-declaration attributes (`xmlns`, `xmlns:xlink`) so they serialize as valid, standalone XML — no duplicate namespaces, no mangled `xlink:href` references.
- All rendering happens client-side; no formula data is sent anywhere.
