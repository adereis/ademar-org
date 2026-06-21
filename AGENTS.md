# AGENTS.md — architecture & conventions

Developer-facing notes for this repo. Usage/build instructions live in
[`README.md`](README.md); this file covers *how it's put together and why*.

## What this is

The publishing layer for ademar.org: a Hugo static site, deployed to a CDN,
controlled entirely via git. It is intentionally a *personal blog as
container* — the home page is a minimal profile; long-form essays are posts.
The framework essay and its provenance corpus live in a **separate** repo and
are not part of this one.

## Stack rationale

- **Hugo** — a single Go binary packaged in Fedora (`dnf install hugo`) with no
  transitive dependency tree. Chosen to minimize supply-chain surface; do not
  introduce npm/gem-based tooling.
- **PaperMod** — typography-first theme, good for long-form reading, vendored as
  a **git submodule** at `themes/PaperMod`. Treat as swappable.

## Layout

```
hugo.toml                 site config; baseURL is the apex https://ademar.org/
content/                  Markdown (posts/, about.md); new content is draft by default
layouts/                  LOCAL OVERRIDES of theme templates (see below)
static/                   copied verbatim to site root (headshot, misc/, CNAME)
themes/PaperMod/          theme submodule (do not edit in place)
.github/workflows/hugo.yml    deploy (GitHub Pages)
```

## Key conventions

### Theme overrides instead of editing the submodule
Files under `layouts/` shadow the theme's templates at the same path. We use
this to patch, *without* dirtying the submodule:
`layouts/baseof.html`, `layouts/rss.xml`,
`layouts/_partials/templates/opengraph.html` replace Hugo v0.158 deprecated
`.Language.{LanguageDirection,LanguageCode}` calls. Each carries a `{{/* … */}}`
comment marking it a temporary shim — **delete these overrides once PaperMod
fixes the deprecations upstream**, then `git submodule update --remote`.

### Never change `baseURL` to fix a preview
`baseURL = "https://ademar.org/"` emits root-absolute asset paths. The
`adereis.github.io/ademar-org/` preview URL therefore looks unstyled (assets
404 under the subpath) — this is expected and resolves at the custom domain.
Changing `baseURL` to fix the preview would break the real site. Preview with
`hugo server` (root baseURL) instead.

### Drafts
`draft = true` content is version-controlled and deployable but excluded from
production builds (no `-D`). Publishing is a one-character change, so unfinished
prose can live in the repo without going live.

### Static dotfiles
Hugo copies `static/` verbatim, including dotfiles. `static/CNAME` (GitHub Pages
custom domain) rides along this way. (A `static/.htaccess` from the DreamHost
phase used to live here too; it was removed as inert on GitHub Pages, which
serves via a CDN and never reads Apache config.)

## Deploy architecture

Source of truth (this repo) is decoupled from the deploy target:

- **GitHub Pages** via `.github/workflows/hugo.yml`. Builds on push to
  `main`; checks out with `submodules: recursive` (required, or the theme is
  missing); custom domain `ademar.org` set in Pages settings and persisted by
  `static/CNAME`.

## DNS / email separation

`ademar.org` DNS stays at DreamHost. Only the website records (apex `A`/`AAAA`,
`www` CNAME) point at GitHub Pages; **email** (`MX` → MailChannels, SPF/DKIM
`TXT`) is untouched. This is why GitHub Pages was chosen over moving the zone to
Cloudflare.

## History note

The previous ademar.org was a ~2.4 GB git repo of photo albums; it is **not**
in this repo. It was archived cold (rsync → tarball → Google Drive) and is not
to be pushed to GitHub.
