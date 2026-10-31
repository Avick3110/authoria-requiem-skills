# Session handoff — 2026-07-14: npc-patching bulk-pass coverage fix, 1.0.2 shipped

*Class: ARCHIVE once superseded (per `standards/HOUSECARL_DOC_HYGIENE.md`). First entry in the
`session-handoffs/` lane — **backfilled 2026-07-15** from the merged PR + commit, not written live by
the 07-14 session (the lane didn't exist yet). Facts below are reconstructed from
[PR #2](https://github.com/Avick3110/authoria-requiem-skills/pull/2) / commit `01ab413`; anything not
in that record is marked as such.*

## What the session did

Fixed **HCBR-2026-07-14-03** and shipped **1.0.2** (merged to `main` 2026-07-14 ~22:35 UTC, branch
`claude/npc-bulk-pass-coverage` → PR #2 → merged; commit `01ab413`).

**The bug:** a whole-plugin NPC pass gated coverage at record-**type** granularity, so a Gray Cowl of
Nocturnal rebalance shipped **7 of 144 NPCs unpatched** — same-prefix EditorID families
(`AlikrEncBandit01..06`, a `Thalmor*` pair, `…Guard##` runs) were sampled and extrapolated, dropping
the hand-tweaked outliers a rebalance exists to catch (PC-scaling vendors, an over-tier summon, a
level-10 Thalmor pair, a level-1 quest attacker).

**The fix:**
- `requiem-npc-patching` — new **"Bulk pass protocol"** section: two one-call coverage sweeps (the
  PC-mult **de-levelling work list** via the union-arm presence test
  `where=["Configuration.Level.LevelMult >= 0"]`, + a full-type **triage matrix**);
  enumeration-as-work-queue; per-record disposition; a **reconciliation count**
  (patched + skipped = enumerated); a **no-extrapolation-across-same-prefix-EditorIDs** rule.
  Copy-ready sweep mirrored in `references/housecarl-recipes.md`; checklist line added.
- `requiem-patching` — integration-checklist item 1 + the skill's checklist bullet tightened from
  per-type to per-record for high-count types (`NPC_`, `ARMO`, `BOOK`, `CONT`, `LVLI`, …).
- `plugin.json` 1.0.1 → **1.0.2**; CHANGELOG entry.

**Explicitly established:** no houseCARL defect — the coverage primitive was confirmed working live on
`Gray Fox Cowl.esm`. Skill descriptions unchanged → no trigger re-validation needed.
`claude plugin validate --strict` passed (plugin + marketplace).

## Follow-ups out of this session

- **The optional houseCARL ergonomic** (document the union-arm `where=` presence-test idiom in
  houseCARL itself) was flagged "tracked separately in that repo" — **captured 2026-07-15** in
  houseCARL's `dev/BACKLOG.md`. Not this repo's item.
- **Live install re-sync:** unknown whether `~/.claude/skills/authoria-requiem/` was re-synced to
  1.0.2 after the merge (not recorded in the PR). → on `dev/archive/BACKLOG-1.x.md` to verify.
- **CLAUDE.md repo map still says "current: 1.0.1"** — the LIVING doc wasn't updated in the same
  commit (doc-hygiene slip). → folded into the 2026-07-15 dev-env work (CLAUDE.md rewrite,
  `dev/archive/1.x-plans/DEV_ENV_PLAN_2026-07-15.md`).

## Where the next session picks up

The 2026-07-15 session is executing `dev/archive/1.x-plans/DEV_ENV_PLAN_2026-07-15.md` (this lane is one of its
products). Check that plan's status + its own handoff before assuming anything here is still current.
