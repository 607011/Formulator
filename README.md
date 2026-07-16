# Formulator

A standalone, single-file web app that turns a LaTeX formula into a downloadable SVG — entirely in the browser, no backend required.

Type something like:

```
\int_{-\infty}^{\infty} e^{-x^2}\,dx = \sqrt{\pi}
```

… and get a clean, scalable vector graphic you can drop into a slide deck, a blog post, or a design tool.

Not sure what LaTeX syntax is supported? See MathJax's [supported TeX/LaTeX commands](https://docs.mathjax.org/en/latest/input/tex/macros/index.html) reference.

## Features

- **Live preview** — renders as you type, powered by [MathJax](https://www.mathjax.org/) in SVG output mode (true vector paths, not HTML/CSS — so the exported file is portable and font-independent).
- **Display or inline mode** for different formula layouts.
- **Font size with selectable unit** — set the formula's font size in `px`, `pt`, `em`, or `rem`. The chosen unit drives both the preview and the exported `width`/`height`. Relative units (`em`/`rem`) are measured against a configurable **base font size**, so `2em` at an 11 px base renders at 22 px — and, when embedded inline, scales with the surrounding text.
- **Live size readout** — shows the resolved font size and the resulting overall graphic dimensions (which differ, since tall constructs like an integral with limits span well beyond the font size).
- **Screen-reader accessibility** — bakes a spoken description of the formula (via MathJax's Speech Rule Engine) into the exported SVG as `role="img"`, `aria-label`, and `<title>`, with the LaTeX source kept in `<desc>`. Choose the spoken language (German or English), or turn the feature off.
- **Customizable appearance** — text color and an optional solid background color.
- **Dark-mode-aware export** — embeds a `prefers-color-scheme` media query directly in the SVG, so the formula automatically switches color to match the viewer's system/browser theme. Includes a light/dark toggle in the preview to check both variants before exporting.
- **Two export options** — download as a `.svg` file, or copy the raw SVG markup to the clipboard. Optionally omit the `<?xml … ?>` declaration for cleaner inline embedding.
- **Zero dependencies to install** — a single HTML file that loads MathJax from a CDN. Just open it in a browser.

## Usage

### Locally

Open formulator.html directly in any modern browser. That’s it – no build step, no server.

Alternatively try the always up-to-date [app on GitHub Pages](https://607011.github.io/Formulator/).


## Dark mode notes

Two different mechanisms matter depending on how the SVG is embedded elsewhere:

- **OS/browser-level dark mode** — handled automatically via the embedded `@media (prefers-color-scheme: dark)` rule. Works even when the SVG is referenced via `<img src="...">`, since the media query is evaluated against the real environment, not the embedding page.
- **Manual/toggle-based dark mode** (a `.dark` class on `<html>`, common on many sites) — this can't reach into an `<img>`-embedded SVG. To support it, inline the SVG's markup directly into the host page's HTML; its paths use `fill="currentColor"`, so the surrounding page's CSS can override the color via the `.formulator-formula` class.

## Accessibility (screen readers)

With the speech option enabled, the exported SVG carries a spoken-math description on the root element, so a screen reader reads the formula aloud instead of skipping it or spelling out raw LaTeX. This works when the SVG markup is embedded **inline** in the page. When embedding via `<img src="formula.svg">`, the browser isolates the SVG, so set an `alt="…"` on the `<img>` instead — you can copy the spoken text shown beneath the preview for that.

The speech language (German/English) is selectable; the corresponding Speech Rule Engine locale data is fetched from the MathJax CDN on demand, so the first switch to a language may take a moment.

## Sizing notes

The **font size** field sets the actual font size — not the overall graphic size. A formula's bounding box (especially with tall constructs like integrals with limits) is typically larger than the font size; the live readout shows both. Relative units (`em`/`rem`) are resolved against the **base font size** field, which represents the target page's root/standard font size.

Note that Firefox does not reliably support `rem` as an SVG `width`/`height` unit (Chrome, Safari, and Edge do); use `em` or `px` for guaranteed cross-browser sizing.

## Tech details

- Rendering engine: [MathJax 3](https://www.mathjax.org/) (`tex-svg` combined component), configured with the `ams` and `boldsymbol` packages, plus the `a11y/semantic-enrich` extension for spoken-math generation.
- Exported SVGs use proper namespace-declaration attributes (`xmlns`, `xmlns:xlink`) so they serialize as valid, standalone XML — no duplicate namespaces, no mangled `xlink:href` references.
- All rendering happens client-side; no formula data is sent anywhere (the only network requests are for the MathJax library and its on-demand speech-locale data from the CDN).
