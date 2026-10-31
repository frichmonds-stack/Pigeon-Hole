# Task Map

Route work to the smallest relevant set of files and docs. Don't read everything by default — start with `CLAUDE.md`, `current-state.md`, and `git status --short`, then pick one route below.

## Routes

### Content (tracks, guides, articles, capabilities, glossary)
- Files: `frontend/src/content/*.json`, `frontend/src/content/schema.md`
- Rules: never hardcode strings in components; guides/capabilities reuse `tools.json` taxonomy (one source of truth); every guide keeps its mandatory `safety` block; Australian English.
- Risks: breaking block-type rendering in `SectionPage`; inventing pedagogy/safety claims — the owner is the source of truth for judgment content.
- Checks: `npm run build` (also exercises the pre-render over all content routes); spot-check affected pages in dev.

### Tools page / filtering / taxonomy
- Files: `frontend/src/content/tools.json` (`meta` + `tools`), `ToolsPage.jsx`, `ToolDetailPage.jsx`, `frontend/src/lumen/FacetFilters.jsx`, `ToolCard.jsx`, `ToolSpotlight.jsx`
- Rules: adding a filter category means updating both JSON `meta` and the page's `facets`/`values`/`setters`; keep filters in the one-row dropdown form (no pill rows); approval layer stays off (`config.js`).
- Docs: `tool-review-architecture.md` (DRAFT) for profile-depth work.
- Checks: dev-server click-through of filters + spotlight; build.

### UI / design system
- Files: `frontend/src/lumen/` (components + `tokens/*.css`), `frontend/src/index.css`
- Canonical spec: `lumen-design-system/project/` — check there before building new UI.
- Rules: no hardcoded hex — Lumen CSS custom properties only; DaisyUI utilities for layout/content pages, token-inline CSS for Lumen components; sentence case headings.
- Checks: visual review in dev, light/dark and narrow widths where relevant.

### Routing / SEO / meta
- Files: `frontend/src/App.jsx`, `frontend/scripts/prerender.mjs`, `frontend/src/lib/usePageMeta.js`, `public/_redirects`, `vercel.json`
- Rules: a new route needs all three: the `<Route>`, a `usePageMeta` call in the page, and coverage in `prerender.mjs`.
- Checks: `npm run build` then `npm run preview`; verify the pre-rendered `index.html` for the new route.

### Benchmark rubric (the scoring instrument)
- Docs: **`delivery-rubric.md` is the instrument** — the buckets, criteria, scales and the rules that govern them. Read it first; read DECISIONS → the 07-23 → 07-30 Strategy entries only for *why*.
- Status: `Function` (`Brief`·`Purpose`) and `Form` (`Polish`·`Format`) are owner-ruled and stress-tested; **aggregation is ruled** (overall = mean of the three buckets, each bucket = mean of its criteria, five owner-authored bands); **`Substance` is not red-penned** and its sub-criteria are agent-drafted.
- **The rubric is the *outputs* score only.** The tool card also carries a separate, editorial **tool recommendation** (binary, positive-only) — that lives in `tool-review-architecture.md` → "What the tool card carries", not here. Don't merge the two.
- Rules: **owner-authored in substance** — the agent structures and challenges, the owner rules each criterion. Never present an agent draft as settled (this failed within 24h on 07-24). Criteria must generalise across artefact type or they fold. Scales measure excellence of their own descriptor; above-and-beyond is a separate, still-parked metric. Keep it **simple and intuitive** (owner, 07-29) — that constraint governs the instrument, not just the arithmetic.
- Risks: over-extending a local point into a re-plan; treating the pilot artefacts as a data source rather than a thinking prop (logged four sessions running).
- Checks: none automated — the test of a criterion is whether it has a real face in every artefact type, and whether a reader could see from the artefact why it scored what it did.

### Deploy / publishing
- Docs: `deploy.md` (Cloudflare Pages). Commit/push only per `close-out.md`; command authority follows the central contract; manual deployment steps are in `deploy-now.md`.

### Process / continuity docs
- Files: `CLAUDE.md`, `.claude/*.md`
- Rules: before writing, name the canonical destination from the routing table in `close-out.md` — one canonical file per fact, short pointers elsewhere. Update `current-state.md` only when verified state changes. Keep entries terse; link by path.
- Owner-collaboration facts (non-coder, challenge-over-validate, approval gates, confidentiality) → `owner-context.md`.

## Source-of-truth order

When information conflicts, prefer in this order:

1. Current local code and working-tree evidence
2. The task the owner has explicitly authorised in this session
3. `DECISIONS.md` (accepted decisions)
4. `current-state.md`
5. `CLAUDE.md` architecture notes and route-specific docs (`schema.md`, `deploy.md`, `tool-review-architecture.md`)
6. `THREADS.md` and `BACKLOG.md`
7. Git history and old session summaries as historical record only

Historical records never silently override current code or accepted decisions.
