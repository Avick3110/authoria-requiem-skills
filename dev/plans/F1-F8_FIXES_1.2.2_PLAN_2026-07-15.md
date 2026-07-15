# F1–F8 fixes batch — 1.2.2

*Author: Aaron (+ Claude session) · 2026-07-15*

*Class: ARCHIVE (plan executed and closed in the same session; frozen at close). Chartered from
`dev/s4-graycowl-validation/FINDINGS.md` per the S4 close-out handoff.*

## Charter

Apply all eight skill-body findings from the S4 Gray Cowl empirical close in one worktree
(`claude/f1-f8-fixes`), body-only — no descriptions change, so no §6.5 re-measure is owed.
Decisions settled with Aaron up front:

- **F8b:** quest-start level gating = a **flag-to-the-user router row** (no gap-mechanic reference
  yet; revisit if the case recurs).
- **F8c:** record-level `CLAS`/`CSTY`/`OTFT`/`FACT` disposition is **owned by
  `requiem-npc-patching`** (rides its bulk pass).
- **Version:** **1.2.2** — the batch is framed as fixes to the shipped derivation discipline.

## Fix map (finding → edit)

| Finding | Skill(s) | Edit |
|---|---|---|
| F1 summoned actors | npc (+ `identification.md`) | summon = combatant; spell routes to magic, actor gets fixed level/stats here |
| F2 NonPlayable combat gear | weapon, armor | skip taxonomies rewritten: worn/wielded NonPlayable gear tiers like its playable twin; only recipes/value exempt; NonPlayable bows still need `NPCsUseAmmo` |
| F3 unzoned cells | leveled-list | new cell→zone sweep in the bulk pass; create tiered ECZNs when the mod ships none; Judgment reframed "leave existing, create missing" |
| F4 recipe material | weapon | ingot + `HasPerk` gate derive from the product's material keyword; Steel/Craftsmanship default named as an anti-pattern |
| F5 flags are unions | npc, weapon, armor, ammo, magic, consumable, race, leveled-list | one union sentence per bulk protocol + one checklist line, domain casualties named; the two literal `Flags` examples annotated. `requiem-script-patching` carries no flag writes — deliberately exempt. houseCARL flag-bit verbs filed as HCBR gap `2026-07-15_gap_no-flag-bit-verbs-on-enum` |
| F6 source-scoped perk derivation | perk-assignment | prefix-filtering a winner list demoted to flagged last-resort; source-scoped read mandatory |
| F7 resolve gate | npc, perk-assignment, router | `housecarl_resolve` every carried FormID before write (wrong-master-suffix slip named); HCBR note `2026-07-15_note_perkplacement-depth2-identity` covers the tool side |
| F8a ARMA split | router (+ armor boundary note) | explicit one-lane assignment rule |
| F8b quest gating | router | flag-to-user row (SMQN / `GetLevel` start conditions) |
| F8c CLAS/CSTY/OTFT/FACT | router + npc | npc bulk pass owns record-level disposition |
| F8d standing-stone-like abilities | router gap table + magic | co-route `standing-stones.md` |

## Close-out

Shipped as PR from `claude/f1-f8-fixes`; `plugin.json` → 1.2.2; CHANGELOG entry maps each fix to
its finding id. Follow-up test candidate (standing, from the S4 handoff): regenerate the Gray Cowl
patch with the post-fix pack — validates F1–F8 in the field and retires the old patch's
double-scale risk.
