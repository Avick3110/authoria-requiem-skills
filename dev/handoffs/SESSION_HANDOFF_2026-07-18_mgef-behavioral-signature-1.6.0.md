# Session handoff — 2026-07-18: MGEF behavioral-signature doctrine (1.6.0)

*Author: Heisen (+ Claude session) · 2026-07-18*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-17_apocalypse-redo-built-1.5.0.md` as the latest "where are we."*

## Outcome

**`requiem-magic-patching` fixed at 1.6.0** — the "MGEF is already Requiem-correct" overclaim that
shipped a bad Apocalypse patch is closed with a re-derived doctrine, live-verified on the ARR
houseCARL instance (profile *Authoria - Requiem Reforged - Main Profile*). Body + references only;
no descriptions changed, so **no §6.5 fan-out re-measure owed**. Live install mirrored.

## The defect, confirmed live

Agents dispositioned modded MGEFs as "already Requiem-correct" on **`MagicSkill` + a vanilla element
keyword + a `MinimumSkillLevel` tier marker** — a trio that is *necessary but not sufficient*, and that
any competent vanilla-style mod ships on its own. Confirmed on the reported record:

| | Apocalypse Bolide `028F67` | MR comparable `01CEA1` |
|---|---|---|
| EditorID | `WB_Des_Fire3_Effect_Bolide` | `REQ_Effect_Destruction3_Fire_AimedExp` |
| `MagicSkill` | Destruction | Destruction |
| `MinimumSkillLevel` | 50 | 50 |
| Keywords | `MagicDamageFire`, `WISpellDangerous`, `WB_Destruction_Fire`, `WB_Destruction` | `MagicDamageFire`, **`REQ_NoDurationScaling 412EDF:Requiem.esp`** |

School, element, and tier all present; flags near-identical to the comparable. **No REQ keyword.**
Unpatched, and it reads as correct to anyone checking school + element + tier.

## What shipped

New `references/keywords.md` section — **the MGEF keyword+flag signature is BEHAVIORAL, not elemental
or tier-keyed.** Classify by what the effect *does*, read MR's exemplar for *that archetype*, diff, add
what's missing. Promoted into SKILL.md step 5 as a three-step procedure; bulk-pass protocol + Checklist
+ Common mistakes all tightened so "already correct" is a disposition requiring a **named comparable
and a shown-equal keyword set**, not an eyeball. Full itemization in `CHANGELOG.md` 1.6.0.

## Findings the maintainer's brief did not have (re-derivation paid, as usual)

The brief supplied a derived shock table and asked for live verification. Four of six rows confirmed
exactly. The corrections:

1. **"DoT/taper" is two archetypes, not one row.** Aimed DoT
   (`REQ_Effect_Destruction2_Shock_AimedDoT 029AFE:Requiem.esp`) carries **only**
   `REQ_NoDurationScaling`; the GM taper rider (`REQ_Effect_DestructionGM_Shock_Taper
   005FAE:Requiem - Magic Redone.esp`) adds `REQ_SpellConcentration` **plus** `Recover` + `HideInUI`.
   Reading one and writing the other under- or over-builds the effect.
2. **Flags do NOT mirror across elements** — the brief said "mirror per element". Keywords did mirror in
   every pair checked; flags did not. The *shock* hazard tick (`045D5A`) omits `NoDeathDispel`; the
   *fire* hazard tick (`REQ_Effect_DestructionGM_Fire_Hazard_Damage 08F3F2:Skyrim.esm`) carries it
   **and** `NoRecast`. The "no NoDeathDispel" rule is shock-specific.
3. **Flags vary within an archetype by tier.** `Destruction2_Shock_Aimed` has no `FXPersist`;
   `Destruction4_Shock_Aimed 10F7EF:Skyrim.esm` does, on identical keywords.

All three are now in the reference as the caveats that make per-record re-derivation mandatory.

## The master-suffix item — the defect was different from the report

The brief expected `REQ_SpellConcentration` recorded as `:Skyrim.esm` somewhere in the skill. **It is
not there** — a full houseCARL resolve-audit of *every* FormID in `references/keywords.md` (element
keywords, REQ markers, RFTI family, vanilla effect-class tags, `Nox_KW_*` sample, staff range, perks)
found **zero wrong master suffixes**. The `:Skyrim.esm` guess came from session scratchpad notes, not
the repo.

The real defect is upstream of that: the behavioral keywords were written as **bare FormIDs** in mixed-
master company — `REQ_SpellConcentration 2FFEAD` sitting in the same sentence as `MagicDamageFire
01CEAD` (which *is* `:Skyrim.esm`). That is how a run guesses the neighbour's master. Fixed by writing
the suffix explicitly everywhere the four appear, plus a note on the `Nox_KW_*` section that all its
bare six-digit IDs take `:Requiem - Magic Redone.esp`.

## Open / next

- **Re-run the Apocalypse magic pass against 1.6.0** — the 745-record patch was built under 1.5.0 and
  its MGEF lane is exactly what this doctrine invalidates. Expect a real MGEF delta this time
  (`REQ_NoDurationScaling` on the burst/DoT effects at minimum). The 1.5.0 handoff's remaining steps
  (enable mod → re-sort → Reqtificator → in-game spot test) should wait for that re-run.
- The 7 field-report issue drafts from the 1.5.0 session are still unfiled (blocked on maintainer go).
- houseCARL: zero tool bugs this session (~15 calls). One ergonomics note — `housecarl_resolve` takes
  `formids=[...]`, not `query=`; the error message named the right parameter, so no report filed.
