# Session handoff — 2026-07-15 (S4): empirical close run on Gray Cowl — charter gates PASS, findings ledger opened

*Author: Aaron (+ Claude session) · 2026-07-15*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-15_S3-CLOSED-perk-assignment-1.2.1-live.md` as the latest. The
coverage-audit charter (`dev/plans/COVERAGE_AUDIT_1.0.3_PLAN_2026-07-15.md`) is CLOSED by this run
and flipped to ARCHIVE this session.*

## What happened

Aaron asked for skill-effectiveness tests that write real patches. Ran **S4, the empirical close**,
as a blinded oracle study on `Gray Fox Cowl.esm` (41,187 records / 80 types) under pack **1.2.1**
(live install verified byte-identical to repo first):

- **Design:** the existing curated `Authoria - Patch - Gray Cowl of Nocturnal Requiem.esp`
  (125 records) served as scoring oracle. 7 Opus lane agents (npc, weapons/ammo, armor, magic,
  consumables, lists/zones, perk+race), each loading its domain skill, **blinded** to the curated
  patch (read the mod's own record versions only), derived per-record dispositions + field values.
  Router disposition table over all 80 types built in the main loop. A writer agent applied all 181
  patched records (~843 field writes) to a disposable ESP (`S4 Validation - Gray Cowl.esp`,
  INACTIVE, mod folder `houseCARL - S4 Validation - Gray Cowl`); a scorer agent field-diffed the 84
  oracle-shared records and classified every delta (strategy/judgment/error/output-stamp).

## Verdict

**All three charter gates PASS:** (1) field-gated reconciliation closed per lane and at the router
top level — 0 of 41,187 records undispositioned; the Heisen failure class did not reproduce;
(2) router could not clear with undispositioned records — catch-all fired (SMQN flagged, GLOB
explicitly bucketed); (3) PERK/LVSP/ALCH/INGR landed with their 1.1.0/1.2.0 owners.

**Field accuracy (84 shared records):** 11 clean/strategy-identical + ~18 output-stamp-only
(our omission doctrine-correct) + 51 legitimate judgment divergences (the oracle authors in
final-values style; the pack carries source inputs — verified tier-equivalent on the steel ladder)
+ **22 records with real errors** → distilled into **8 findings (F1–F8)** in
`dev/s4-graycowl-validation/FINDINGS.md`. Headliners: summoned-actor level ownership seam (F1),
NonPlayable NPC-hand gear wrongly cosmetic (F2), unzoned-cell→ECZN gap (F3), COBJ material-recipe
defaulting (F4), flags-are-unions doctrine sentence missing everywhere (F5), perk-superset
double-stamp risk (F6). Also proved real curated UNDER-coverage: consumables + all 5 creature
races got Requiem-grounded derivations the shipped curated patch never had.

## Where the next session picks up

1. **Findings → fixes charter.** F1–F8 are skill-body edits (no description changes expected ⇒ no
   §6.5 re-measure; if any description changes, the fan-out re-triggers). Natural batch: one
   worktree, version bump (1.2.2 or 1.3.0 by scope), CHANGELOG, PR, Aaron's go.
2. **Tell Heisen to update** (pre-1.1.0 → 1.2.1) — still outstanding from S3.
3. Backlog carries the standing items + a pointer to the findings ledger.
4. The test ESP mod folder can be deleted whenever; it was never enabled.

## Artifacts

`dev/s4-graycowl-validation/` — FINDINGS.md + all 13 run artifacts (lane JSONs, coverage join,
adjudication, write log, scoring). Nothing committed to the repo this session (dev/ is gitignored;
no repo file changed).
