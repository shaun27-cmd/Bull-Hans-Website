# Bull-Hans site — split into files

Original file: `Bull-Hans-Premium-3.html` (one 3.3 MB HTML file with everything inlined).

## What's here

| File | What it is |
|---|---|
| `index.html` | The page shell — just the `<head>`/`<body>` markup, now linking out to the files below |
| `styles.css` | All CSS (Tailwind-generated utility classes + custom rules), ~36 KB |
| `app.js` | The React application code (compiled/bundled), ~212 KB |
| `external-links.js` | Tiny helper script that makes outside links open in a new tab |
| `assets/` | 23 images (`image-01.webp` … `image-23.webp`/`.png`), extracted from base64 |

## Why it's not split further (e.g. into components/JSON)

`app.js` is a **pre-built, minified bundle** — React, ReactDOM, and the site's
own component code were already compiled together into one file (by esbuild
or similar) before you got it, with variable names shortened to single
letters (`o`, `s`, `us`, etc.) and no source map included. That's the nature
of the original file, not something introduced by this split.

Because of that:
- There's no reliable way to pull the original JSX component files back out —
  that information (component boundaries, prop names, original variable
  names) doesn't exist anymore in the compiled output.
- Any text content (headings, service descriptions, contact info, etc.) lives
  inline in `app.js` as string literals passed to React's `createElement`
  calls (shown as `o(...)`/`s(...)` in the code) — not in a separate JSON
  data file, so I couldn't cleanly extract a `data.json` without risking
  breaking the render logic that consumes it.

What I *could* safely and reliably separate out:
- **CSS** → its own file (it was already self-contained).
- **Images** → the 23 base64-encoded images were decoded back into real
  `.webp`/`.png` files in `assets/`, and `app.js` now references them as
  `./assets/image-XX.ext` instead of carrying ~3 MB of inline base64 text.
  This is what took the JS file from 3.26 MB down to ~212 KB.
- **The small vanilla-JS snippet** for external links → its own file.

## Running it

This is a static site — open `index.html` directly in a browser, or serve
the `project/` folder with any static file server (needed because
`app.js` is loaded as an ES module, which some browsers restrict on
`file://` URLs):

```bash
cd project
python3 -m http.server 8000
# then visit http://localhost:8000
```
