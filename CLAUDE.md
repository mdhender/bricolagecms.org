# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

A `wget` archive of the (now-defunct) **bricolagecms.org** marketing/docs site for
Bricolage CMS — a Perl / mod_perl / Mason content management system. What is
checked out is rendered site output, not the project that produced it: ~470
`.html` files, the site's CSS/JS/images under `ui/`, generated Perl API docs
under `docs/`, and 60 release tarballs under `downloads/`.

The pages were themselves emitted by Bricolage (`<meta name="generator"
content="Bricolage 2.0.0" />`); the templates and database behind them are not
here, and nothing in this repo regenerates the HTML.

Bricolage's own source *is* present, but only compressed inside the release
archives — `bricolage-2.0.1.tar.gz` holds `lib/Bric/**` (the modules `docs/`
documents), a `t/` test suite, and `Makefile.PL`. There is no checked-out,
editable copy of it.

The archive was produced with the flags documented in `spider.sh`
(`--recursive --page-requisites --adjust-extension --convert-links
--restrict-file-names=windows --no-parent`). Note that `spider.sh` only *echoes*
the `wget` command; it does not run it.

## Build / run

There is no build step and no toolchain — the archive is served as-is. (A
`package.json` declaring Vite/React/Tailwind was briefly committed in `337d4e5`
and removed again; it was never part of the capture and none of its scripts
worked. If a rebuild of the site is ever wanted, start that conversation from
scratch rather than restoring that file.)

To view the site, serve the repo root statically, e.g.:

```sh
python3 -m http.server 8000   # then open http://localhost:8000/index.html
```

Serving from the root matters: a few IE-only conditional links use root-absolute
paths (`/ui/css/blueprint/ie.css`, which was never captured). All other links
are relative, so `file://` browsing mostly works too.

## Layout

- `index.html`, `downloads.html`, `news.html` — top-level pages. `tour/`,
  `showcase/`, `support/`, `about/doc_models/`, `downloads/index.html` each hold
  a single `index.html`.
- `news/` — dated permalink tree (`news/YYYY/MM/DD/[slug]/index.html`), release
  announcements under `news/announce/`, per-release changelogs under
  `news/announce/changes/`, paginated archive `news/index.html` +
  `index1.html`…`index4.html`, and `news/rss.xml`.
- `ui/` — shared assets for the marketing pages: Blueprint CSS framework
  (`ui/css/blueprint/`), the site stylesheet
  `ui/css/bricolagecms-org.css@v04112010-2.css` (the `@` comes from
  `--restrict-file-names=windows` mangling a `?v=...` query string — keep the
  filename verbatim), jQuery 1.3.2 + `jquery.corner.js`, and `ui/img/`.
- `docs/` — Pod::Site–generated API docs for the `Bric::*` Perl modules, in three
  near-duplicate trees: `docs/2.0/api/`, `docs/current/api/` (the fuller one, plus
  `podsite.css`/`podsite.js`), and `docs/devel/api/` (stub). `docs/2.0/api` is
  missing its `podsite.css`/`podsite.js`, so it renders unstyled.
- `downloads/` — 103 MB of historical release archives fetched along with the
  pages: 55 `bricolage-*.tar.gz` (1.0.1 → 2.0.1) and 5 `bricolage.cc-*.tar.gz`.
  Each is a full Perl source distribution. Read one with `tar tzf` / `tar xzf -O`
  rather than unpacking into the tree.
- `CREDITS.md`, `LICENSE.md`, `SOURCES.md` — upstream Bricolage project files
  (contributor list, the Bric::License POD, and icon-set licensing).

## Editing conventions

- **The marketing pages have no templates.** Header, `#main-nav`, sidebar, and
  footer are copy-pasted into all 99 `main-nav`-bearing pages. A nav or footer
  change is a repo-wide `sed`/script edit across those files, not a one-file
  change. `docs/` pages are separate — they carry the Pod::Site chrome instead
  and share none of this boilerplate.
- Pages are HTML 4.01 Transitional with tab indentation; `docs/` is XHTML 1.1.
  Match whichever tree you are editing.
- Every page still loads dead or third-party-tracking resources over plain
  `http://`: Google Analytics (`UA-2885748-1`), Chartbeat, Disqus reply counts,
  FeedBurner, and `jquery.cycle.lite.js` from `malsup.github.io`. Expect console
  errors and mixed-content blocks when serving over HTTPS. Removing them is a
  reasonable cleanup, but it is a repo-wide edit — ask before doing it wholesale.
- Regenerating rather than hand-editing: if the goal is a fresher capture, run the
  `wget` line from `spider.sh` against a live host — but the origin site is gone,
  so in practice this snapshot is the source of truth.
