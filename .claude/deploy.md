# Deploy

Static SPA (Vite build) + per-route pre-rendered HTML. Target host: **Cloudflare Pages** (free). Netlify/Vercel also supported.

## Build
```bash
cd frontend
SITE_URL=https://<your-domain> npm run build   # -> frontend/dist/
```
`npm run build` runs `vite build` then `scripts/prerender.mjs`, which writes a real `index.html` per route (correct `<title>` + Open Graph/Twitter tags) so social cards + non-JS crawlers work.

- **`SITE_URL`** — set it (no trailing slash) so `og:url` / `og:image` are absolute. Without it, social **images won't render** (relative URLs). Set it as a build env var on the host.
- **`public/og-default.png`** — add a 1200×630 share image (logo + tagline on the Lumen palette). Referenced by every page's `og:image`; until it exists the card shows title/description with no image.

## Cloudflare Pages — LIVE (since 2026-07-15)
Project **`pigeon-hole`** → **https://pigeon-hole-87j.pages.dev** (plain `pigeon-hole.pages.dev` was taken). Connected to `frichmonds-stack/Pigeon-Hole` (renamed from `AI-Explore` 2026-10-16).

Actual settings:
- **Production branch:** `main` (work branches get preview deploys; publish = merge to `main` — see `close-out.md`)
- **Root directory:** `frontend` · **Build command:** `npm run build` · **Output directory:** `dist`
- **Env var:** `SITE_URL = https://pigeon-hole-87j.pages.dev` (set by owner 2026-07-15; swap to custom domain at launch)
- SPA fallback via `frontend/public/_redirects` (copied to `dist/`).

### Pre-launch noindex
`frontend/public/_headers` sends `X-Robots-Tag: noindex` on every page so search engines don't index the pages.dev URL before launch. **Delete that block at launch** or the site stays invisible to Google. (Also on BACKLOG launch gate.)

## Netlify
Base `frontend`, build `npm run build`, publish `frontend/dist`. `_redirects` handles fallback. Set `SITE_URL`.

## Vercel
Root `frontend`. `vercel.json` provides the SPA rewrite. Set `SITE_URL`.

## After deploy — verify social cards
- Facebook/LinkedIn: paste a deep URL (e.g. `/tools/chatgpt`) into their post composer or the [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/) — confirm title/description/image.
- Or `curl -A "facebookexternalhit" https://<domain>/tools/chatgpt` and check the `<meta property="og:...">` tags are present in the returned HTML.
