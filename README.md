# waycap-public

The public marketing site for [WayCap](https://waycap.app), served via GitHub Pages.

## Contents

| File         | Purpose                                                                  |
| ------------ | ------------------------------------------------------------------------ |
| `index.html` | Landing page — hero, features, invite-code entry                          |
| `tos.html`   | Terms of Service                                                         |
| `privacy.html` | Privacy Policy                                                        |
| `404.html`   | Catch-all that detects `/join/<INVITE_CODE>` and deep-links into the app |
| `CNAME`      | Custom domain — `waycap.app`                                             |

## How the invite-link catchall works

`404.html` is what GitHub Pages serves for any path that doesn't have a matching file. The script:

1. Parses `window.location.pathname` for `/join/<CODE>` (or `/waycap-public/join/<CODE>` for the `github.io` fallback before DNS is live).
2. On mobile, attempts `window.location.href = "waycap://join/<CODE>"` plus a hidden-anchor click (gesture-safe Android Chrome fallback).
3. Uses the Page Visibility API — if the page goes hidden, the app took over and we leave the loading view alone.
4. If still visible after 1200 ms, swaps to a manual fallback: code display, "Open WayCap" button, copy-code button, and an "open on your phone at waycap.app/join/<code>" hint.
5. On desktop, skips the auto-redirect entirely and goes straight to the manual fallback.
6. If the path doesn't match `/join/<code>`, shows a generic 404 with a link back to `/`.

The deep link `waycap://join/<CODE>` matches the Expo Router scheme defined in `waycap/app.json` (`scheme: "waycap"`) and the invite URL builder in `waycap/src/app/event/[id]/invite.tsx`.

## Deploy

- Source: `main` branch.
- Custom domain via the `CNAME` file.
- DNS at the registrar must point `waycap.app` (and `www.waycap.app`) to GitHub Pages A records: `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` (and a CNAME for `www`).

## Local development

```sh
python3 -m http.server 8000
# http://localhost:8000/                              → index.html
# http://localhost:8000/tos                          → tos.html
# http://localhost:8000/privacy                       → privacy.html
# http://localhost:8000/404.html                     → generic 404 view
```

`python3 -m http.server` returns its own 404 and ignores `404.html`, so testing the invite-link path locally requires either:

- Pushing to a branch and letting GitHub Pages preview the deploy, or
- Pushing to `main` and waiting ~60 s before testing on a phone.

## Adding a page

1. Drop a new `.html` at the repo root.
2. Add a link to it from `index.html` and the footer (shared across all four pages).
3. The same `<head>` block (Tailwind config + CDN `<script>`) should be copied verbatim so the visual language stays consistent.