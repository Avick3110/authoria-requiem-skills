# Session handoff — 2026-07-17: field-report round 2 → 1.3.0 (NPC stats/classification, magic overhaul, consumables verify) — PR open

*Author: Heisen (+ Claude session) · 2026-07-17*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-16_valserano-follower-registration-removed.md` (Aaron's lane) as the
latest.*

## What this session was

Heisen's second field report, delivered directly to the session: (1) NPCs — perks/spells skipped
when the source record had none; the DNAM base stats + skill offsets never patched; too many NPCs
skipped by classification; authority = *all* Requiem addons touching NPCs. (2) Spells — cost-only
patching (flag, charge, magnitudes, added effects, subclasses all missed). (2b) Resistance spells
silently broken on `REQ_NULL` MGEFs. (3) MGEF keywords/values/conditions. (4) ENCH magnitude/cost.
(5) Consumables "completely skipped — study again". Standing instruction: **every rule gets a
live-mined reference.**

## What shipped (branch `claude/field-report-round2`, version 1.3.0)

Charter: `dev/plans/2026-07-17-field-report-round2-npc-magic-consumables.md` (ARCHIVE, all steps
done). Evidence: `dev/corpus-2026-07-17-field-report-round2/` (4 mining reports, scrubbed). Full
per-item detail: the 1.3.0 CHANGELOG entry. Headlines:

- **NPC:** DNAM block (`PlayerSkills.Health/Magicka/Stamina` + SkillValues + SkillOffsets)
  first-class across field standard/recipes/checklist/sweeps; wrong AutoCalc claims corrected;
  empty-kit rule (analogue supplies perks+spells); positive-evidence skip taxonomy + the
  named-civilian stat-only lane; new `references/npc-authority.md` (7-plugin live census).
- **Magic:** ManualCostCalc-as-a-write (99.3% measured), charge/cost ladders + delivery
  multipliers, the rider doctrine (add the comparable's secondaries — full FormID table), 833-spell
  census + hand-pair rule, new `references/resistance-map.md` (NULL→analogue per element per lane),
  new `references/mgef-conditions.md` (patterns A–D), MinimumSkillLevel tier marker, per-archetype
  flags table, ENCH gear-value correction (enchant adds zero gold — the tier lives in
  magnitude/cost/pool), staff/scroll ladders.
- **Consumables:** references verified live 29/30 — one FormID bug fixed (`VendorItemFood 08CDEA`),
  FLOR sweep + thrown-SCRL routing + FaB race-side note + COBJ gate nuance added.

Body + references only — **no description changed, so no §6.5 re-measure owed.** All three
SKILL.md bodies remain under the 500-line cap (npc 427 / magic ~445 / consumable ~330).

## Judgment calls the maintainers should know

1. **Report item 4's premise corrected, not encoded.** "ENCH cost is tied to the value of the
   enchantment on the gear" is vanilla-autocalc thinking; Requiem sets `NoAutoCalc` everywhere and
   hand-keeps enchanted items at the *base* item's gold value (verified on Iron Sword/Gauntlets
   tier runs). The references document the mined truth and name the correction.
2. **"Consumables completely skipped" looks like a stale live install** — the skill shipped in
   1.1.0 and the references verified 29/30 correct; Heisen's install was pre-1.1.0 at last record.
   After this lands: `robocopy authoria-requiem "$env:USERPROFILE\.claude\skills\authoria-requiem" /MIR`
   + restart, on BOTH machines.
3. **Version framed 1.3.0** (behavior-changing doctrine: classification default, DNAM writes,
   rider adds), not a 1.2.x fix batch.

## Next / standing

- **PR open, awaiting maintainer go** (review-by-request: this one warrants Aaron's eyes —
  doctrine changes in classification + the ENCH value correction).
- **Empirical close candidate:** re-run one of Heisen's failing NPC/magic cases (or regenerate the
  Gray Cowl patch) under 1.3.0 and check the new checklist items reconcile — the standing
  "a plan reviewed is not a thing proven" principle. Not done this session.
- houseCARL: **zero tool bugs/gaps hit across ~180 read calls** (incl. bracket paths in `where=` —
  `PlayerSkills.SkillOffsets[OneHanded] > 0` works). Nothing filed.
- BACKLOG items untouched (perk-assignment Q8 description tweak; root README authority section;
  original-nine empirical re-validation).
