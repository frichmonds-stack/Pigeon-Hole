# Owner Context

How to work with this project's owner. Shared canon for any agent working in this repo — if this file and an agent's private memory disagree, this file wins. Ported and adapted 2026-07-13 from the more mature Budgie repo (then `Budget-Tool`) (`ai/owner-context.md`), where the owner established these rules.

Scope note: this file holds owner-as-collaborator facts. Project purpose and audience belong in `project-context.md`; priorities in `BACKLOG.md`; workflow rules in `CLAUDE.md` / `close-out.md`.

## Who the owner is

- **Non-coder, builds entirely through AI assistance.** A K–12 maths educator (WA, Catholic-school context — hence the CEWA thread). Cannot read or verify code, so "readable code for the owner" is not a decision variable — favour machine-checkable correctness (builds passing, visible behaviour verified in the browser) and explain technical matters in plain English with analogies.
- **Learning product-building live, not arriving with it.** Keep product discussions plain-language, explain why a question matters before arguing its answer, and treat "I can't foresee the implications yet" as a valid outcome of a discussion, not an unfinished one.
- **The pedagogy source of truth.** For teaching judgment, safety framing, and lesson/guide content, the owner's wording and rulings are authoritative — don't invent pedagogy claims; propose drafts and ask.

## How the owner works with agents

- **Approval before building.** Propose, then wait for an explicit go-ahead. Vague approval ("sounds good", "ok", "what next?") is continued discussion, not authorisation. Full command vocabulary: `close-out.md` Mode Gate.
- **Challenge over validate.** Default to calibrated honesty over agreeableness; surface the strongest counter-case, especially right after the owner states a preference. Don't reorder options to flatter the stated choice, and don't manufacture disagreement to seem independent. Also: don't over-extend an owner ruling past where the owner put it — the owner rules exactly as far as stated.
- **Lean communication.** Keep replies and plans lean; rely on standing repo rules instead of restating them; flag uncertainty instead of over-confident plans. Don't dump code into chat — the owner can't read it; describe changes in plain English and let the diff live in the files.
- **Usage-budget aware.** The owner tracks model usage and will interrupt expensive operations. When budget is tight, sequence cheap durable work (docs, decisions, state capture) first; proactively offer the cheap path.
- **Larger batches, driven end-to-end.** Execute coherent multi-layer batches (content + component + docs) rather than one small change per pass; document at completion, not mid-stream. Don't stop between chunks of an approved plan to re-ask permission.
- **Capability scout.** The owner relies on agents to surface what is newly possible — reveal unlocks they don't know to ask for, rather than only answering the literal question.

## Project-specific confidentiality gate

The analogue of Budget-Tool's financial-data rule: **CEWA internal-sourced approval data must not resurface publicly.** The approval layer is off (`frontend/src/config.js`), user-facing CEWA/internal wording was scrubbed, and the internal values still exist in git history — scrub history before the repo ever goes public. Don't re-add any of it without explicit owner authorisation and a public source (see THREADS + DECISIONS 2026-07-01).
