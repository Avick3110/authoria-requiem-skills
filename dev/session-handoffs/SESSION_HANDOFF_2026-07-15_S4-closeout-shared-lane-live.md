# Session handoff — 2026-07-15 (S4 close-out): charter closed, oracle provenance corrected, HCBR reports filed, shared dev lane LIVE — next: F1–F8 fixes

*Author: Aaron (+ Claude session) · 2026-07-15*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-15_S4-empirical-close-graycowl-PASS.md` as the latest — that doc still
carries the S4 run's detail; this one adds what happened after it and where to pick up.*

## State at close

- **main `f153075`**, plugin **1.2.1**, live install synced. Working tree clean; no worktrees open.
- **Coverage-audit charter CLOSED** (plan doc flipped to ARCHIVE). All S4 gates passed — see the
  superseded handoff + `dev/s4-graycowl-validation/FINDINGS.md` (F1–F8 ledger, fix candidates
  inline).
- **`dev/` is the SHARED, TRACKED lane as of this session** (PR #8, Heisen is a collaborator).
  Authorship stamps are mandatory per CLAUDE.md §Working discipline. Aaron approved the open
  questions as posed: doc-only dev changes ride small `docs:` PRs; HCBR store question still open
  (see Standing).

## What happened after the S4 run (post-run events this doc adds)

1. **Oracle provenance corrected (Aaron):** the Gray Cowl patch used as scoring oracle was itself
   authored by houseCARL, pre-1.0.2-era — so S4 = old pipeline vs new, blinded. Consequences folded
   into FINDINGS.md (provenance-correction block): the oracle's under-coverage IS the pre-fix
   failure class; its output-stamping style is pre-doctrine; (B)-class divergences have no
   privileged side; latent double-scale risk if the Reqtificator ever reprocesses Gray Cowl —
   regenerating that patch with the fixed pack is a natural follow-up test.
2. **houseCARL gap probe + reports (after Aaron's mid-session houseCARL update):** the new `has`
   operator is scalar-only — struct presence still unfilterable (its own diagnostics count the
   VMAD carriers but won't match them). Filed to the HCBR store (all 2026-07-15):
   `gap_struct-presence-filter-has-scalar-only`, `gap_batch-record-detail-no-plugin-scope`,
   `gap_no-flag-bit-verbs-on-enum` (evidence: Add on a Flags enum refused; root of the S4
   flag-clobber errors), `note_perkplacement-depth2-identity` (depth=2 renders PerkPlacement
   opaque; depth=3 opens it — also root-caused F7's "dangling" perk: right FormID, wrong master
   suffix). BACKLOG's VMAD-sweep entry updated: still blocked, re-probe on next houseCARL update.
3. **Shared dev lane shipped (PR #8, rebase-merged `053a8f4`+`f153075`):** `dev/` un-gitignored and
   tracked (44 docs), scrubbed for publication (email, `C:\Users\<name>` paths, profile tag — all
   verified zero-hit), CLAUDE.md Working discipline updated in the same commit (stamps, read both
   authors' handoffs by date, publish hygiene). Local unscrubbed originals were session-scratch
   backup only; the tracked versions are canonical.

## Next session (Aaron): the F1–F8 fixes batch

Charter it from `dev/s4-graycowl-validation/FINDINGS.md` — each finding carries its fix candidate.
Shape: one worktree; body-only skill edits expected (**no §6.5 re-measure unless a description
changes**); version bump (1.2.2 if framed as fixes, 1.3.0 if F1–F3 count as behavior changes —
Aaron's call); CHANGELOG entries mapping fix → finding id; sync live install after merge.
Decisions Aaron owns inside the batch: F8b (quest-start level gating — new gap-mechanic reference
vs explicit flag-to-user row), F8c (Class/CombatStyle/Outfit/Faction record-level ownership), and
the version framing. F5's houseCARL side is filed (flag-bit verbs); the skill-side sentence
("flags are unions — carry the bits you aren't changing") goes in regardless.

## Standing

- **Heisen:** now a collaborator — `git pull` gives him the whole lane. His live skill install was
  pre-1.1.0 at last word; current is 1.2.1 — he should re-sync.
- **HCBR store question (open):** the store lives on `E:\Skyrim Modding\ARR Workspace\` — confirm
  Heisen can reach it, else move houseCARL gap/bug reports into a tracked lane too.
- **Test artifact:** MO2 mod folder `houseCARL - S4 Validation - Gray Cowl` (INACTIVE) — keep for
  inspection or delete freely.
- **Follow-up test candidate:** regenerate the Gray Cowl patch with the post-fix pack (retires the
  old patch's double-scale risk; doubles as the F1–F8 fix validation).
- Backlog: perk-assignment Q8 description tweak (triggers §6.5 if taken); root README authority
  section (needs Aaron); original-nine empirical re-validation (standing).
