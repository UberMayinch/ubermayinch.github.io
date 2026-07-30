# rhizoMorphic

A minimal technical blog powered by [Hugo](https://gohugo.io/), with LaTeX math
and syntax highlighting rendered at build time.

## Writing a new post

Create a Markdown file in `content/posts/`:

````markdown
---
title: "Your Post Title"
date: 2026-07-31
tags: ["Math", "Physics"]
---

Inline math works: $E = mc^2$.

Display math:

$$
\int_0^\infty e^{-x^2}\,dx = \frac{\sqrt{\pi}}{2}
$$

```python
def f(x):
    return x ** 2
```
````

Push to `main`. The GitHub Actions workflow builds and deploys — nothing else to run.

Filenames become URLs, so keep the `YYYY-M-D-Slug.md` pattern
(`content/posts/2024-3-30-NatIsoVectSpaces.md` → `/blog/posts/2024-3-30-NatIsoVectSpaces/`).

## Local preview

Install Hugo (extended not required, but harmless):

```bash
hugo server
```

Opens at <http://localhost:1313/blog/> with live reload.

## How it works

- **Math**: KaTeX is embedded in Hugo. The passthrough extension (`hugo.toml`)
  captures `$…$` and `$$…$$`, and `layouts/_markup/render-passthrough.html`
  renders them at build time — static HTML, no client-side JS. KaTeX's
  stylesheet and woff2 fonts are vendored into `static/css/katex/` (the woff
  and ttf `@font-face` sources were stripped, since every current browser takes
  woff2). MathML-only output would drop that 330 KB, but browsers lay out
  `CD` commutative diagrams badly without KaTeX's CSS.
  Commutative diagrams use the AMScd `CD` environment, which KaTeX supports;
  `tikzcd` does not work.
- **Code**: Chroma highlights fenced blocks at build time with inline styles
  (`markup.highlight.noClasses`), so there is no syntax stylesheet to maintain.
- **Layouts**: five small templates in `layouts/`. No theme, no submodules.
- **Deployment**: `.github/workflows/deploy.yml` installs Hugo, runs
  `hugo --minify`, and publishes to GitHub Pages. Repository
  **Settings → Pages → Source** must be **GitHub Actions**.

## Project structure

```
├── hugo.toml                  # All site config
├── content/
│   ├── about.md
│   └── posts/                 # Blog posts (.md)
├── layouts/
│   ├── baseof.html            # Shared page shell
│   ├── home.html              # Post index
│   ├── single.html            # Blog post
│   ├── list.html              # Section listing
│   ├── about.html             # About page
│   └── _markup/
│       ├── render-passthrough.html   # LaTeX → KaTeX HTML
│       └── render-link.html          # Root-relative link rewriting
├── static/                    # Copied verbatim (css, images, PDFs)
└── .github/workflows/deploy.yml
```
