# conformist-mw.github.io

Landing page (`index.html`) + Hugo blog (`blog/`) + docsify notes (`docs/`).
Deployed to GitHub Pages by `.github/workflows/pages.yml`.

## Landing page

Styles are Tailwind CSS v4. The compiled file `static/css/tailwind.css` is
committed, so the deploy stays a plain copy — no node step in CI.

After changing classes in `index.html`, rebuild it:

```bash
npm install     # once
npm run css     # -> static/css/tailwind.css
npm run css:watch
```

Theme tokens and base styles live in `src/input.css`. The light theme is a
`data-theme="light"` attribute on `<html>` (set by the inline script in
`index.html`), exposed to utilities as the custom `light:` variant.
