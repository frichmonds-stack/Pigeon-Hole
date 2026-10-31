# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Session Start
Always run before doing any work:
```bash
git fetch origin claude/amazing-carson-5zucgf
git pull origin claude/amazing-carson-5zucgf
git status
git log --oneline -5
```

Then verify the Git root is exactly `E:/Projects/Pigeon Hole`.
No startup fetch, pull, or other Git mutation. Load `ai/command-profile.json` and its central
command contract; it owns command matching, modes and authority.
Prototype execution is explicit opt-in for a named experiment/run and automation scope.

Then:
1. Read [`.claude/current-state.md`](.claude/current-state.md) — the present-tense project snapshot — and [`.claude/owner-context.md`](.claude/owner-context.md) — how to work with the owner.
2. Route the task via [`.claude/task-map.md`](.claude/task-map.md) and read only what the task needs (it also holds the source-of-truth order when docs conflict).
3. State mode and edit authorization from the central contract; use `.claude/close-out.md` for project steps.
4. Before substantive work on each meaningful owner-linked turn, follow [`.claude/owner-time.md`](.claude/owner-time.md) to begin local project timing (Windows only; on the Mac, say timing was not recorded). Skip subagents, timer-only queries, and `time out` itself. Timing grants no edit authority.
5. Before writing any planning/continuity doc, name its canonical destination from the routing table in `close-out.md` — one canonical file per fact, short pointers elsewhere, no duplication.

## Branch
Work happens on `claude/amazing-carson-5zucgf`. `main` is production; Cloudflare Pages
also creates preview deployments for work branches. Command authority follows the central
contract; project steps live in `.claude/close-out.md` and `.claude/deploy.md`. No automatic merge.

## Dev Commands
```bash
cd frontend && npm start        # dev server at http://localhost:5173
cd frontend && npm run build    # production build to frontend/build/
cd frontend && npm run preview  # preview the production build
```
No test suite — exploratory/content project.

## Architecture

### Routing — BrowserRouter
All routes live in [`frontend/src/App.jsx`](frontend/src/App.jsx). **`BrowserRouter`** with clean URLs (was `HashRouter`; switched 2026-07-01 for SEO/share cards). Deep links work via an SPA fallback (`public/_redirects`, `vercel.json`); `npm run build` then runs a **pre-render** ([`scripts/prerender.mjs`](frontend/scripts/prerender.mjs)) that writes a real per-route `index.html` with `<title>` + OG/Twitter tags (all static + dynamic routes) so non-JS/social crawlers get proper metadata, while [`usePageMeta`](frontend/src/lib/usePageMeta.js) updates them on client nav. Set `SITE_URL` + add `public/og-default.png` (1200×630) for share images. Host: **Cloudflare Pages** — see [`.claude/deploy.md`](.claude/deploy.md).

| Path | Component |
|---|---|
| `/` | `HomePage` |
| `/guides` | `GuidesPage` (work-first hub) |
| `/guides/:guideId` | `GuidePage` (task walkthrough) |
| `/articles` | `ArticlesPage` |
| `/articles/:articleId` | `ArticlePage` |
| `/tools` | `ToolsPage` |
| `/tools/:toolId` | `ToolDetailPage` |
| `/explainer/:category` | `ExplainerPage` (category: `uses` · `roles` · `pedagogies`) |
| `/learn` | `LearnPage` (hub → About AI · AI Capabilities · AI Safety) |
| `/learn/capabilities` | `CapabilitiesPage` (index, grouped by capability) |
| `/learn/capabilities/:capabilityId` | `CapabilityPage` (detail + tools that do it) |
| `/glossary` | `GlossaryPage` |
| `/saved` | `SavedPage` |
| `/about` | `AboutPage` |
| `/foundations`, `/risks`, `/practice`, `/pedagogies`, `/explore` | `TrackPage` (one explicit `Route` per track, **not** a dynamic `/:track`) |
| `/<track>/:sectionId` | `SectionPage` (same five tracks, one explicit `Route` each) |

Tracks (content files): `foundations` (title "About AI") · `risks` (title "AI Safety") · `pedagogies` (title "Teaching Ideas") · `practice` & `explore` (legacy, **not in nav** — retained as raw material, mostly AI-generated cruft pending rework).

### Strategy — work-first
The site leads with **doing the work** (Guides + Tools), then the craft, then learning about AI, with **child safety first**. Nav is deliberately just four uniform labels: **Explore** (route `/guides` — pending rename to an articles/"what's new" section) → **Tools** → **Teaching** (`pedagogies`) → **Learn** (hub over About AI · AI Capabilities · AI Safety). **Learn is a hub** (`/learn`) over three areas: **About AI** (= `foundations` track), **AI Capabilities** (= `capabilities.json`, the new capability-first catalogue), and **AI Safety** (= `risks` track, which now also holds the "When AI Serves Learning" judgment section moved out of pedagogies). Full rationale in [`.claude/project-context.md`](.claude/project-context.md). Audience is K–12 educators broadly; **CEWA approval badges are retained** as the worked example of "safe/approved to use".

### Content — JSON files
All content lives in [`frontend/src/content/`](frontend/src/content/). Never hardcode strings in components.

- **Track files** (`foundations.json`, `risks.json`, etc.): `{ id, title, description, sections[] }`. Each section has `blocks[]` — see [`schema.md`](frontend/src/content/schema.md) for all block types (`text`, `heading`, `list`, `risk`, `pedagogy`, `quote`, `callout`).
- **`tools.json`**: has two top-level keys — `meta` (filter taxonomy: `useCategories`, `pedagogyFrameworks`, `bands`, `subjects`, `roles`, `cewaStatuses`, `accessTiers`) and `tools` (array of tool objects). Both are consumed together throughout ToolsPage and ToolDetailPage. Two **detail-page-only** fields (kept off the card to keep cards calm): `access` (an `accessTiers` id — cost/availability) and `cewaProvided` (boolean — already licensed/provisioned in the CEWA stack, shown as an "In your CEWA toolkit" badge), with an optional `accessNote`. `cewaProvided`/access values are currently **placeholders pending CEWA confirmation** and only seeded on a handful of tools.
- **`guides.json`**: the work-first content type — `{ meta: { difficulties }, guides: [...] }`. Guides **reuse `tools.json`'s taxonomy** (one source of truth) and cross-link to real tool ids (inheriting their CEWA badge). Every guide has a **mandatory `safety` block** (child-safety-first) and a `pedagogyNote`. See [`schema.md`](frontend/src/content/schema.md). **Note: a rework is planned** — repurposing "Guides" into a curated articles/reading section (see BACKLOG).
- **`capabilities.json`**: `{ capabilities: [...] }` — the AI-capability catalogue under Learn. Each entry has `eduUses` (useCategory ids) and `tools` (tool ids), both resolved to labels via `tools.json` meta (one source of truth). Rendered by `CapabilitiesPage` (index, badges to scan — **no filtering by design**) and `CapabilityPage` (detail, lists the tools that perform the capability with their CEWA badge).
- **`index.js`**: re-exports all track files as a keyed object for `TrackPage` / `SectionPage`.

### Design System — Lumen + DaisyUI
Two layers that must work together:

**Lumen tokens** (`frontend/src/lumen/tokens/*.css`) are imported first in [`frontend/src/index.css`](frontend/src/index.css), then bridged into DaisyUI CSS custom properties (e.g. `--color-primary: var(--pine-600)`). This means DaisyUI components like `btn`, `card`, `badge` automatically render in Lumen's palette.

**Styling approach varies by component:**
- Layout, content pages, track pages → DaisyUI utility classes (`btn`, `card`, `alert alert-warning`, `prose`)
- Lumen components (`frontend/src/lumen/`) → inline CSS using `var(--token-name)` directly, with component-scoped CSS injected via `document.createElement('style')` at module load (see `ToolCard.jsx`)

**Never use hardcoded hex values** — always reference Lumen CSS custom properties.

**Key tokens:** `--pine-*` (primary green), `--clay-*` (accent terracotta), `--ochre-*` (highlight gold), `--paper-*` (warm cream backgrounds), `--ink-*` (text). Fonts: `--font-display` (Newsreader), `--font-sans` (Hanken Grotesk), `--font-mono` (IBM Plex Mono).

### Lumen Source System
[`lumen-design-system/project/`](lumen-design-system/project/) is the canonical design spec — component `.jsx` files, token CSDs, guidelines, and an interactive UI kit at `ui_kits/lumen/index.html`. When implementing new UI, check this directory first. Components have been adapted from here into `frontend/src/lumen/`.

### Key Conventions
- `RiskCallout` → `alert alert-warning` (child development risk)
- `PedagogyNote` → `alert alert-info` (links AI use to teaching theory)
- Long-form content: wrap in `prose` class (`@tailwindcss/typography`)
- CEWA status strings in `tools.json` (`approved`, `approved-conditions`, `under-review`, `not-approved`, `not-reviewed`) must be mapped to display variants (`approved`, `conditional`, `review`, `restricted`, `unreviewed`) — use `statusOf(tool)` / `cewaStatusMap` from [`frontend/src/lib/cewa.js`](frontend/src/lib/cewa.js), don't redefine the map locally
- Sentence case for all headings and buttons; uppercase mono only for eyebrow/kicker labels — use [`lumen/Eyebrow.jsx`](frontend/src/lumen/Eyebrow.jsx) (`tone="pine"` / `"muted"` / a CSS colour), not an inline style block
- Australian English throughout (`organise`, `Year 7`, `maths`)
- **Reuse before re-create:** before defining a component or helper inside a page file, check `frontend/src/lumen/` and `frontend/src/lib/` first. If a visual pattern or lookup (label resolution, status mapping) shows up in a second page, promote it to a shared file instead of copying it — this codebase is entirely AI-written across many sessions with no shared memory of each other's work, so duplication is the default failure mode, not an edge case.
- Mapping/lookup logic (status maps, taxonomy label lookups like `useCatLabel`/`pedLabel`/`diffLabel` in [`frontend/src/lib/taxonomy.js`](frontend/src/lib/taxonomy.js)) lives in `frontend/src/lib/` — never redefined inside a page.
- `npm run lint` (ESLint, flat config at `frontend/eslint.config.js`) is available but not yet wired into any CI/build gate — run it manually after non-trivial changes.

### Filtering — shared `FacetFilters` + `useFacetState`
Both `ToolsPage` and `GuidesPage` filter through one shared component, [`frontend/src/lumen/FacetFilters.jsx`](frontend/src/lumen/FacetFilters.jsx): a single compact row of dropdowns (options hidden until opened, the chosen value shown on the button, single-select per facet, `'all'` sentinel = no filter). This deliberately replaced stacked rows of pills — keep filters in this one-row dropdown form, don't reintroduce always-visible pill rows. A page supplies a `facets` config (`{ key, label, options[], allLabel? }`), a `values` map, an `onChange(key, val)`, an `onClear`, and `resultCount`. Facet vocabulary is user-facing: **Work type** (`useCategory`), **Work area** (`role`), **Year level** (`band`), **Subject**, **Pedagogy**, plus **Approval** (tools) / **Difficulty** (guides).

The state behind those facets — one value per facet key, the Domain→Work type narrowing rule (choosing a domain scopes the work type options to it and clears a now-invalid work type), and `clearAll` — is centralised in [`frontend/src/lumen/useFacetState.js`](frontend/src/lumen/useFacetState.js) and shared by both pages. Each page still owns its own `filtered` predicate (the actual per-item filter logic differs between tools and guides) — only the facet *state* is shared.

### Tools Page UX Pattern
`ToolsPage` implements a two-panel pattern: a filterable grid of `ToolCard`s, and a `ToolSpotlight` that appears above the grid when a card is clicked. The spotlight auto-collapses on scroll (48px threshold with a 420ms suppress after expand). Filters are driven entirely from `tools.json`'s `meta` object via `FacetFilters` (above) — adding a new filter category requires updating both the JSON `meta` and the `facets`/`values`/`setters` in the page.

## Planning Docs
- Current state (session-handoff snapshot): [`.claude/current-state.md`](.claude/current-state.md)
- Owner context (how to work with the owner): [`.claude/owner-context.md`](.claude/owner-context.md)
- Task map (work routing + source-of-truth order): [`.claude/task-map.md`](.claude/task-map.md)
- Session log (dated milestones): [`.claude/session-log.md`](.claude/session-log.md)
- Backlog: [`.claude/BACKLOG.md`](.claude/BACKLOG.md)
- Decisions log: [`.claude/DECISIONS.md`](.claude/DECISIONS.md)
- Open threads: [`.claude/THREADS.md`](.claude/THREADS.md)
- Tool review architecture (DRAFT, fluid): [`.claude/tool-review-architecture.md`](.claude/tool-review-architecture.md)
- **Delivery rubric — the scoring instrument** (`Function`/`Form` ruled; `Substance`'s list ruled and its collisions settled, **no scales written yet**): [`.claude/delivery-rubric.md`](.claude/delivery-rubric.md)
- Project background (purpose, audience): [`.claude/project-context.md`](.claude/project-context.md)
- Close-out procedure: [`.claude/close-out.md`](.claude/close-out.md)
- Work Queue identities (Notion Sync Keys): [`.claude/portfolio-identities.md`](.claude/portfolio-identities.md)

## Session closeout (Notion Work Queue)

Pigeon Hole is a managed project in the **Work Queue** database in Notion, under the *AI Project Manager* page. At every `Normal Close` and `Publish Close`, update it through the Notion connector. The procedure, the identity rules, and the "what must never go up" list are in [`.claude/close-out.md`](.claude/close-out.md) → "Notion Work Queue"; the permanent Sync Keys are in [`.claude/portfolio-identities.md`](.claude/portfolio-identities.md).

The repository stays authoritative for code, docs, decisions and detailed next actions. Notion carries a curated portfolio summary only.

**Not the CLI.** The `ai-project-manager` / `ai-efficiency` CLI (`frichmonds-stack/AI-Efficiency`) is Windows-only Python requiring 3.12+, and is not installed on this Mac — the previous instruction to run `ai-efficiency closeout` here could never have succeeded. There is no local outbox, so report failed delivery honestly rather than assuming a retry.
