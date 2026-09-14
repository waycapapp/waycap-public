# WayCap Public Site — Claude Code guide

## What this repo is

The static GitHub Pages site for WayCap — marketing pages plus the
`404.html` deep-link catchall that converts a web visit to `waycap.app/join/<CODE>`
into a `waycap://join/<CODE>` deep link into the mobile app. No build
step, no framework, no package.json. Sibling repos:

- [`../waycap-app`](../waycap-app) — React Native mobile client
  (target of the deep links).
- [`../waycap-api`](../waycap-api) — Rust backend.
- [`../waycap-web`](../waycap-web) — Vite + React SPA.

## Stack

- Plain HTML + inline CSS + a small inline-JS shim in `404.html`.
- GitHub Pages for hosting (CNAME → `waycap.app`).
- No build step. No `node_modules`. No bundler.

## Local dev

There is no build. To preview:

```bash
# Open index.html directly in a browser, OR
python3 -m http.server 8000
# then visit http://localhost:8000
```

GitHub Pages serves the site on push to `main` (per the repo's Pages
settings). No CI to wire up.

## Repo layout

```
index.html       — landing page (hero, features, CTAs)
tos.html         — Terms of Service
privacy.html     — Privacy Policy
404.html         — deep-link catchall — see "Conventions" below
CNAME            — waycap.app (required by GitHub Pages custom domain)
README.md        — this file
```

## Conventions

- **`404.html` is load-bearing.** When a user lands on
  `waycap.app/join/<CODE>` (or any path GitHub Pages can't resolve), the
  Pages server returns `404.html`. The inline JS in that file parses the
  path, extracts the invite code, and `location.replace`s to
  `waycap://join/<CODE>` to launch the mobile app. Don't refactor it
  without testing the full path — iOS Safari, Android Chrome, and desktop
  browsers each handle the `404` flow slightly differently.
- **No build step.** Resist the urge to add a framework, a bundler, or
  a `package.json`. The site is intentionally simple: three static HTML
  files, one shim. A future iteration might add a real link-redirect
  service (e.g. Firebase Hosting) — that's a different repo, not a
  refactor of this one.
- **CNAME is committed.** `waycap.app` is the canonical domain. If the
  domain changes, update `CNAME` in the same commit as any DNS work.
- **Inline assets.** Any image, font, or stylesheet is inlined or lives
  next to the HTML file. No CDN, no `<script src="https://…">`. The
  site is offline-readable.

## Commit hygiene

- **No `Co-Authored-By:` trailer in commit messages.** User-set
  preference for every repo in this workspace.
- Conventional-ish subject lines (`feat: …`, `fix: …`, `chore: …`,
  `docs: …`).
- One logical change per commit. For copy edits to `tos.html` or
  `privacy.html`, group the legal-text changes in a single commit so a
  reviewer can see the diff cleanly.

## Pointer to global guidance

`~/.claude/CLAUDE.md` covers CodeGraph and Context7 — those are global,
not per-repo. No need to duplicate them here.