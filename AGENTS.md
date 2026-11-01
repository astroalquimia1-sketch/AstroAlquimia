# Project notes for AI agents

## What this repo is

A single self-contained static page: `astroalquimia_x5f_maqueta_x5f_portfolio_x5f_mitico.html`
(a portfolio landing page for "AstroAlquimia"). React, ReactDOM and the compiled
Tailwind CSS are inlined in one `<script type="module">` / `<style>`; there is no
`package.json`, no bundler, no backend, no database and no external credentials.

**This HTML file is the source.** Edit it directly — the JSX is already compiled, so
changes are made in the compiled `x("div", { className: ... })` call style, not JSX.

## Running it (Base44 sandbox)

```bash
docker compose -f docker-compose.base44.yml up -d --build
```

- `web` — `nginx:alpine` on host port 3000, serving the repo bind-mounted read-only
  at `/usr/share/nginx/html`. No build step: an edit to the HTML is live on refresh
  (browser refresh, not HMR — call `reload_preview` after editing).
- `.base44/nginx.conf` — sandbox-only main config. The repo root is root-owned mode
  `0700`, so nginx workers run as `root` inside the container so they can read the
  bind mount. `.base44/nginx-site.conf` sets the index file, `no-store` caching, and
  a deny rule for dotfiles so `.git` is not served over the preview host.
- Health: `wget -q -O /dev/null http://127.0.0.1/` inside the container;
  `.base44/environment.json` records `previewPort: 3000`, `healthPath: /`, `secrets: []`.

## Verify a change

```bash
curl -s -o /tmp/page.html -w '%{http_code} %{size_download}\n' http://localhost:3000/
grep -c 'some new copy' /tmp/page.html
docker compose -f docker-compose.base44.yml logs --tail=20 web
```

Then load `http://localhost:3000/` in the preview: the page is a dark, serif
single-page portfolio whose hero reads "Donde el cielo se hace materia."; it must show
the fixed nav, the natal-chart graphic and the two CTA buttons, with no console errors.
Because nothing compiles, a syntax error in the inline script makes `#root` render
empty while the HTTP response still returns 200 — check the rendered DOM, not just the
status code.
