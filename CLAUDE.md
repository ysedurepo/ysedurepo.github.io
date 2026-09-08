# CLAUDE.md

Hugo static blog (`youssadi`) published to GitHub Pages at `https://ysedurepo.github.io/`.
Theme: **PaperMod**, vendored as a git submodule at `themes/PaperMod`.

## Commands

```bash
hugo server -D
```

```bash
hugo --minify
```

```bash
hugo new content/posts/my-post.md
```

Local Hugo is **0.165.0 extended**; CI pins **0.150.0 extended** (`.github/workflows/hugo.yml`).
Both are past 0.146, which matters because the theme uses the new `layouts/_partials/`
layout tree — an older Hugo will not render it.

## Deployment

Push to `main` → `.github/workflows/hugo.yml` checks out with `submodules: recursive`,
runs `hugo --minify`, and deploys `./public` via `actions/deploy-pages`. There is no
manual publish step.

## Repo gotchas

**`public/` is committed but not used.** 32 build-output files are tracked in git while CI
rebuilds `public/` from scratch on every push. Nothing you change in `public/` affects the
live site. There is no `.gitignore`, so every local `hugo` run dirties the working tree with
regenerated HTML/XML. Ignore that churn; don't hand-edit files under `public/`.

**Never edit `themes/PaperMod/` to customize the site.** It is a submodule pinned to commit
`d376885`; CI checks out that exact commit, so local theme edits are invisible to the
deployed site. There is currently an uncommitted edit in
`themes/PaperMod/layouts/_partials/footer.html` adding MathJax `<script>` tags — it is both
undeployed and non-functional (the `src` values are bare hosts, `https://polyfill.io` and
`https://jsdelivr.net`, with no path). To override theme markup, create
`layouts/_partials/extend_head.html` in the project root; PaperMod calls that partial from
its `head.html`. The project's `layouts/`, `assets/`, `data/`, and `i18n/` dirs are all
currently empty.

**`hugo.yaml` still has leftover PaperMod example values.** Identity, home page, menu, and
social icons are real now, but `params.assets` favicon paths, `editPost.URL`
(`<path_to_repo>`), and the `XYZabc` analytics verification tags are still placeholders.

**The home page bio lives in `params.homeInfoParams.Content` in `hugo.yaml`**, not in a
content file. It is Markdown rendered through `RenderString`, and it contains a raw `<img>`
for the portrait — which is why `markup.goldmark.renderer.unsafe: true` is set. Turning
`unsafe` back off silently drops the photo and the trailing `clear:both` div. Shortcodes do
*not* work in that field (`RenderString` does not expand them); use raw HTML instead.
`homeInfoParams` is used rather than `profileMode` deliberately: `layouts/list.html` swaps
the recent-posts list out entirely when `profileMode.enabled` is true.

## Content

- Posts live in `content/posts/`. `content/docs/template.md` is a Markdown/shortcode
  cheat-sheet (in French, `draft: true`) — a reference, not a published page.
- `content/posts/test.md` is the PaperMod front-matter sample, kept as documentation of
  every available field.
- Front matter is written in English for real posts (`probabilities-calibration.md`) with
  `tags` and `categories` as YAML lists.
- Images go in `static/images/` and are referenced as `/images/<name>` (either Markdown
  `![]()` or the `{{< figure >}}` shortcode).

## Math

`hugo.yaml` configures goldmark passthrough for `$...$` (inline) and `$$...$$` (block), so
LaTeX survives Markdown processing. Posts using math set `math: true` in front matter.
Rendering the passed-through LaTeX still requires a MathJax/KaTeX script — wire it up via
`layouts/_partials/extend_head.html` guarded on `.Param "math"`, not by editing the theme.
