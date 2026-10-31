# Session handoff — 2026-07-18: spell coverage — magnitudes, subtypes, riders (1.7.0)

*Author: Heisen (+ Claude session) · 2026-07-18*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-18_mgef-behavioral-signature-1.6.0.md` as the latest "where are we."*

## Outcome

**`requiem-magic-patching` at 1.7.0.** Second field report on the same Apocalypse patch: spells got
cost/charge/flags/`HalfCostPerk` and nothing else, and the skill's checklist let that count as patched.
Closed by **defining "patched"**, making **magnitude derivation mandatory**, and deriving the real
subtype/rider vocabulary live across all five schools. Body + references only — no descriptions
changed, **no §6.5 re-measure owed**. Live install mirrored.

## Method

Five parallel read-only agent lanes (one per school) against the ARR houseCARL instance, each required
to return `UNVERIFIED` rather than guess. **Every load-bearing claim was then re-read by the maintainer
session directly** before it entered skill text — which mattered: see the corrections below. Roughly
100 live queries total.

## The report was right about the defect and wrong about three mechanisms

Consistent with the CLAUDE.md rule that a report is a lead, not a fact.

1. **Subtype keywords are not on the SPEL.** The brief asked for a SPEL-side table and a checklist line
   "every patched SPEL carries its subtype keyword(s)". **MR Spell records carry no keywords at all** —
   915 scanned, `Keywords` unset on every one. The classification lives on the **MGEF**; the checklist
   line is written against the MGEF, and "don't try to write a keyword onto a SPEL" is now a Common
   mistake.
2. **`Nox_KW_<School>_Perk_*` does not belong on a damage spell.** MR's own Fire/Frost/Shock damage
   MGEFs carry only the element keyword + `REQ_NoDurationScaling`. Pyromancy/Cryomancy/Electromancy
   (`006015`/`006016`/`006017`) appear on **cloak/enchant/hazard** forms only — confirmed directly on
   `REQ_Effect_Destruction3_Shock_CloakSelf 03AEA1:Skyrim.esm` and `…Destruction5_Shock_CloakSelf
   00751A:MR` (both carry `006017`) vs `…Destruction2/4_Shock_Aimed` (neither does). The skill's prior
   advice to "carry that school's specialization keyword so the perk applies" was **wrong for damage
   spells** and is now listed as a mistake.
3. **"Riders must be tier-matched" is school-dependent, not a flat rule.** Destruction riders are
   tier-indexed *records* (`Cremation2/3/5`, `ElectrostaticDischarge1–5`). Illusion `_Improved` riders
   are **shared tier-2 stubs**: verified myself that `REQ_IllusionGM_Pain4 00596E` and
   `REQ_IllusionGM_Pain5 0061A6` **both** carry `GM_Pain2_Improved 00594C` ×2 and `GM_Pain2_Effect2
   005ED4` ×2. The tier lives in the spell's per-effect `Data.Magnitude`; the rider's own
   `MinimumSkillLevel` stays 25. So a rider's tier marker must never be used to infer the spell's tier.

## Two further findings the brief didn't anticipate

- **`Archetype.Type = Script` is not the mechanical/cosmetic discriminator.** The brief's Pain2 example
  is correct (`00594C` ValueModifier vs `005EA5` Script), but the rule doesn't generalise: Destruction's
  `Slow_Aimed 0B729F:Skyrim.esm` is Script **and** mechanical (Papyrus `Nox_Destruction_Weakness`), as
  are Restoration's Dispel/Purify effects; Conjuration's *cosmetic* `Bound_Weapon_<Element>` are Script
  with no VMAD. The `_Desc_` convention is **Illusion-only** (all 10 in MR).
- **A second cosmetic shape exists that the brief listed as a required rider.** The Destruction
  **Tapers** (`Fire_Taper 005AF5`, `Frost_Taper 005FAD`, `Shock_Taper 005FAE`) are `ActorValue = Fame`
  at **magnitude 0** — they carry a *full and correct* behavioral keyword set and still do nothing. The
  brief listed "fire with no Cremation/Taper" as a defect; **Cremation is the mechanical half, the Taper
  is an FX carrier.** Documented so nobody "fixes" a Taper by giving it a magnitude.

## Magnitudes verified directly (the cross-check now in the reference)

Firebolt T2 `012FD0`: base **30**, Cremation2 **m3/d3**, Impact_Stagger **0.25**. Healing T2 `02F3B8`:
heal **30**, Respite **15** (exactly 50%). Mage armor T1 `05AD5C`: base **100**, riders **50** and
**20**, Duration 60. Confirms the shipped bug's numbers were off by ~100× (`Slow` 50 vs **5**,
`Impact_Stagger` 25 vs **0.25**).

## Regression fixed from my own 1.6.0

1.6.0 asserted **every** FormID in the `Nox_KW_*` section takes `:Requiem - Magic Redone.esp`. A full
106-keyword census disproves it: **three are `:Requiem.esp`** (`Illusion_Pain 482636`, `Illusion_Sleep
484DDB`, `Illusion_Weakness2 3C130A`). Replaced with an exception table + the shape tell (MR's own are
`00xxxx`). A blanket master assertion over a namespace was the wrong move; per-item census is the right
one, and that is what shipped.

## Open / next

- **The Apocalypse patch needs a full magic re-run against 1.7.0**, not a touch-up. Under the new
  definition its spells are *cost-normalized*, not patched: magnitudes, subtype keywords and mechanical
  riders were never derived. This supersedes the 1.6.0 handoff's "expect an MGEF delta" — the delta is
  the whole balance layer.
- Structural note for whoever picks this up: SKILL.md was at the 500-line cap, so the bulk-pass detail
  moved to `references/bulk-pass.md` (new). Further additions to this skill must land in `references/`.
- The 7 field-report issue drafts from the 1.5.0 session remain unfiled (maintainer go pending).
- houseCARL: zero tool bugs across ~100 calls + 5 agents. One note — `cross_plugin_query` `where`
  predicates on an all-null field return a clear "field IS VALID but UNSET on every record" message,
  which is what proved the SPEL-keywords finding rather than leaving it ambiguous. Good behavior.
