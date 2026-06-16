# ademar.org

Source for [ademar.org](https://ademar.org/) — the personal site of Ademar
Reis. A static site built with [Hugo](https://gohugo.io/) and the
[PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme.

## Prerequisites

- **Hugo (extended)**. On Fedora: `sudo dnf install hugo`.
- **git** (the theme is a submodule).

## Clone

```bash
git clone --recurse-submodules https://github.com/adereis/ademar-org.git
```

If you already cloned without submodules:

```bash
git submodule update --init --recursive
```

## Local preview

```bash
hugo server          # add -D to include drafts
```

Then open **http://127.0.0.1:1313/**.

> Use `127.0.0.1`, not `localhost`: on some systems `localhost` resolves to
> IPv6 `::1` first while Hugo binds IPv4, giving a spurious "connection
> refused".

## Build

```bash
hugo --gc --minify    # output goes to ./public (git-ignored)
```

## Writing content

```bash
hugo new content posts/my-post.md   # new post (created as a draft)
hugo new content about.md           # a top-level page
```

New content is a **draft** by default (`draft = true`) and will not appear in
the production build until you set `draft = false`. Preview drafts locally with
`hugo server -D`.

## Deploy

Every push to `main` triggers `.github/workflows/hugo.yml`, which builds and
deploys to GitHub Pages. No manual step.

## Project notes

Architecture, conventions, and the rationale behind them are in
[`AGENTS.md`](AGENTS.md).
