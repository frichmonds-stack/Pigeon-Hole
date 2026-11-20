# Modes & Close-Out Procedure

## Mode Gate

Load `../ai/command-profile.json` and its central machine-readable contract.

Command authority, modes and matching are defined only by that central contract.
Project steps: `execute-now.md`, this closeout procedure, `deploy-now.md` and `owner-time.md`.

## Close-out checklist (both modes)

1. Run the best available checks for what changed — `npm run build` is the default gate (it also exercises the pre-render across all content routes); browser-preview verification for visible UI changes.
2. Update the docs the work actually touched — classify each update and write it to its **canonical destination only**, short pointers elsewhere:

| Update type | Canonical location |
|---|---|
| Present-tense project snapshot | `current-state.md` (only when verified state changed) |
| Priorities / concrete next work | `BACKLOG.md` |
| New or resolved open decision | `THREADS.md` |
| Durable decision + rationale | `DECISIONS.md` |
| Dated session summary | `session-log.md` |
| Owner-collaboration facts | `owner-context.md` |
| Architecture / conventions / routing | `CLAUDE.md` or `task-map.md` |
| Content model rules | `frontend/src/content/schema.md` |
| Deploy steps | `deploy.md` |
| Work Queue identity (Sync Key) | `portfolio-identities.md` |

   It's fine to report "continuity: no update required" when nothing material changed.

   **Before moving on, re-read the session's discussion turns, not just the diff.** The routing table above is easy to satisfy from `git diff` alone — which silently misses decisions reached in conversation that produced little or no code, and misses them worst in a long session that later gets consumed by an unrelated problem (a failing push, a broken tool), because by close-out time the diff is the only thing still in view. Ask explicitly: *what did the owner rule on today that isn't visible in the diff?* Anything with a stated rationale belongs in `DECISIONS.md`, marked **OWNER CALL** where the owner actually ruled and flagged as proposal where it was AI elaboration they didn't examine (see `owner-context.md`). A shipped artefact whose reasoning is unrecorded is a half-finished close.
   *Why this is here: the 2026-07-20 premise design (four questions and a stance, the three-ring scope model, the deferral ruling) passed through a Publish Close unrecorded — only the homepage card it produced was written down. It survived by chance, recovered from a transcript a day later.*
3. Add a dated entry to `session-log.md` for meaningful implementation, decision, or handoff milestones (not for trivial sessions). If the log exceeds ~150 lines, archive older entries to `session-log-archive.md` and leave a pointer.

4. Report Notion disabled unless the matching committed opt-in and permitted closeout exist. No direct writes by default.

## Notion Work Queue (portfolio)

Disabled: `.ai-efficiency.toml` has identity only, without committed `notion_sync = true`.
Retain project_id `ai-explore`, project_name `Pigeon Hole`, and `portfolio-identities.md` Sync Keys.
Do not invoke connector writes, import/sync or configure Notion under ordinary closeout.
Future delivery needs owner-approved committed opt-in matching the working copy and the AI Project
Manager `docs/design/work-management-model.md` closeout property whitelist, exact Project + Sync Key,
and only named curated rows. No database/view changes, deletion, credentials or source/technical data.
Report pending/disabled truthfully. No arbitrary global Active-work limit applies.

## Publish Close only

Inspect staged and unstaged changes; scope only completed-batch files and preserve unrelated work.
Complete confidentiality review: no CEWA internal approval data or wording in staged files/artifacts.
Commit/tag only within the reviewed publication scope; an agreed remote/ref is required for push.
`main` deploys production and work-branch pushes create Cloudflare Pages previews.
Follow the central command contract for publication authority and report deployment status
separately from Git publication. Never merge to production automatically.
If a permitted push fails, report the failure and bounded retry evidence, never claim success.

## Owner time

After a successful `execute now` batch (checks and docs included), or a successful `publish close` (authorized publication only), run the matching completion hook in [`owner-time.md`](owner-time.md). Incomplete work preserves timer state.

## Final report — every close

State explicitly:

- files changed
- docs updated (or "no update required")
- checks run, their results, and **what they prove in product terms** — how the owner can verify without reading code
- commit + push status, and deploy/live verification status
- assumptions, uncertainties, and manual review still needed

**Never claim GitHub is updated or the site is live unless it was actually pushed and verified.** If not done, say exactly what remains.

Publication/deployment authority and handling of unknown automatic triggers follow the central command contract.
