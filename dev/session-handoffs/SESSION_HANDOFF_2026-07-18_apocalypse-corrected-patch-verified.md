# Session handoff — 2026-07-18: Apocalypse corrected patch VERIFIED — Heisen's build is the authority

*Author: Aaron (+ Claude session) · 2026-07-18*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-18_spell-coverage-1.7.0.md` as the latest "where are we."*

## Outcome

**Heisen's `Authoria - Reqtificated - Apocalypse.esp` (930 records / 11 types, mod folder
"Apocalypse Magic Redone Corrected Patch") passed a six-lane read-only verification fan-out and is
the ACCEPTED AUTHORITY for Apocalypse integration** (maintainer decision, Aaron, this session; the
local instance is a pure dev build). It loads immediately after our 1.5.0-era
`Authoria - Patch - Apocalypse Requiem.esp` (745 records) and wins every overlap; our patch's only
live contribution is the 91 internal worker sub-spells (verified harmless). The 1.7.0 handoff's
"full magic re-run against 1.7.0" is therefore DELIVERED — by Heisen's build, not a local re-run.

**Do not reorder the two plugins.** The verification's inverse discovery (below) means restoring
our patch above Heisen's would reintroduce broken records.

Delivery note for the record: the first file dropped into the mod folder was a byte-identical copy
of vanilla `Apocalypse - Magic of Skyrim.esp` (MD5-confirmed; wrong file sent). Caught by
re-derivation before any conclusion was built on it; replaced with the real patch the same morning.

## Method

Six parallel read-only agent lanes against the live instance (profile *Authoria - Requiem Reforged -
NSFW Profile*): MGEF (106/225 read in detail + a 126-record unpatched-denominator probe), Spell
(40/175 stratified 8-per-school + rider resolution + overlap diffs), Book/Scroll economy (~30 diffs
+ ~70 value reads + ~120 live `REQ_Tome_*` anchors), Weapon/ObjectEffect (~24 diffs + all non-staff
weapons in full + the 148-staff MR anchor), NPC/Race/COBJ/LVLI (all 42 actors read winner-side with
`resolve_names`), and whole-patch integrity (`check_errors`, master hygiene, REQ_NULL reverse-scan,
origin-scope, ITM sample). Every lane required UNVERIFIED over guessing.

## Verdict — all six lanes PASS (no blockers in Heisen's file)

- **Integrity:** 0 dangling / 0 missing masters; all 930 records are true overrides (zero
  patch-origin records); no tool-output master; **zero REQ_NULL references** (all 21 Requiem
  `REQ_NULL_*` stubs reverse-checked). One NOTE: `Dragonborn.esm` is a probable unused master.
- **MGEF:** the 1.6.0 behavioral-signature doctrine is applied correctly across every sampled
  archetype (Bolide fixed; concentration/nova/cloak/poison/pain signatures right; cosmetic shells
  and `_Desc` carriers correctly left mechanical-free). Masters 100% clean **including the three
  `:Requiem.esp` exceptions** from the 1.7.0 census.
- **Spell:** ManualCostCalc + on-ladder BaseCost/ChargeTime everywhere sampled; **magnitude
  derivation done** (e.g. FrozenOrb: our patch had left Apocalypse's raw 180, his derives 80 + full
  rider set); riders school-correct with doctrine-exact values (`Slow` 5, `Impact_Stagger` 0.25);
  no keywords on SPEL; `HalfCostPerk` tier-true incl. `_NPC` variants on the player ladder; never
  regresses a field we set.
- **Economy:** books are our records with a regularized, MR-aligned tome ladder; scrolls were
  substantially REBUILT and now mirror their patched spells exactly.
- **Weapon/ENCH:** staff frames at MR-canonical Value 100; staff-enchant costs track the live MR
  ladder (T2 45 / T3 120 / T4 165 exact); NonPlayable summon weapons carry final-tier values, bows
  carry `NPCsUseAmmo` + rescale exclusions; the Daedric Crescent enchant tiered per the 1:1 rule.
- **Actors:** all 42 summons machine-stamped Reqtificator-style (fixed levels laddering sensibly,
  role-appropriate perks, `REQ_Trait_*` resist traits — every form resolves real); the 4 races get
  only the correct racial-trait spell, stats untouched; summon worn gear left intact per the 1.5.0
  decision.

## The inverse discovery — our 1.5.0 build carries real defects (all shadowed live)

Comparing lanes exposed systematic faults in our own 745-record patch. None reach the game while
Heisen's superset wins, but they are real if ours ever ships alone:

1. **All sampled staff ObjectEffects ship `Flags = 0`** — missing `NoAutoCalc`, so the engine
   ignores the hand-set EnchantmentCost and recomputes (the ENCH analogue of the ManualCostCalc
   failure). All 148 live MR staff enchants carry `NoAutoCalc`; the rule is in
   `requiem-magic-patching` SKILL.md ("all ENCH are `NoAutoCalc`") and was still missed.
2. **NonPlayable summon bows lack `NPCsUseAmmo`** — summoned archers can't fire; Reqtificator
   skips NonPlayable so it never self-heals. The rule is explicit in `requiem-weapon-patching`.
3. **Conjured weapons off-doctrine:** crit = damage instead of `floor(Damage/2)`; damage far
   off-ladder (daedric sword 90 vs Requiem's 15); staff frame Value left at base 5 vs MR's 100;
   Daedric Crescent kept as a physical weapon rather than bound-weapon treatment.
4. **Scroll lane bugs:** Flamestrike scroll magnitude 160 with no Cremation rider; Fracture scroll
   `Slow` 50 (the documented ~100× class of error); self-buff scrolls missing ManualCostCalc.

The pattern: **every one of these rules already existed in the skill text at 1.5.0 build time** —
these are execution failures, not doctrine gaps. The staff WEAPON-frame rules live in
`requiem-weapon-patching` while a magic-heavy job routes staves through the magic lanes (the magic
skill's own eval notes the split); that cross-skill routing seam is where 1–3 slipped through.

## Would the skills produce Heisen's patch today? (the adequacy question, answered)

- **The magic core (MGEF/SPEL/scroll/ENCH/tome) — yes.** Everything load-bearing in his build is
  now stated doctrine (1.6.0 + 1.7.0), and his records conform to it as written. The verification
  effectively ran his patch against the skill text and they agree.
- **Minor deviations from the skill's own stated rules (his file, take-or-leave economy nits):**
  Apprentice/Adept Destruction damage tomes at 350/600 vs the stated 400/(600–700 live) rungs; one
  T5 tome (MirrorEntity 2200) above the 2000 ceiling; Restoration circle hazards carry
  `REQ_NoDurationScaling` while `keywords.md`'s hazard-tick row says element-keyword-only (his
  bare Destruction hazards match the rule; the circles deviate — one rule should be settled).
- **Not skill-producible, by design:** the NPC 42 / Race 4 lanes are Reqtificator OUTPUT baked into
  the file (the skills forbid hand-stamping these; a skill-produced patch omits them and converges
  at build time), the COBJ staff-blank 2→1 recipe tweaks, and the staff-LVLI de-level are hand
  choices no skill derives.
- **The honest caveat: text coverage ≠ execution.** Our 1.5.0 run missed rules that were already
  written down. 1.6.0/1.7.0 hardened exactly the magic lanes that failed (definition of "patched",
  mandatory magnitudes, bulk-pass protocol); the equivalent hardening for the **staff-frame routing
  seam** (weapon-skill rules applied to staves inside a magic-dominated whole-mod job) does not yet
  exist and is the standing risk for the next whole-mod run. → BACKLOG candidate.

## Open / next

- **Reqtificator run + in-game spot test** (the 1.5.0 remaining steps, now unblocked). Test list:
  a T5 spell's cost, a tome price at a vendor, a summon fight, Willpower's BaseCost-0 cast, the
  LightningStrike staff (his magnitude 150 sits above the MR T4 line — UNVERIFIED as wrong), and
  Kyrgar (his machine-stamped fixed-32 replaces Aaron's hand-tuned fixed-40; accepted under the
  authority decision).
- **For Heisen:** the two settle-a-rule items (damage-hazard keyword rule; `WB_X100_Wizardfire_Effect`
  — a Bolide-class miss if player-castable) + confirm the summon-innate MGEF family skip
  (~20 effects, defensible under the creature-innate carve-out) was deliberate.
- **BACKLOG:** staff-frame routing hardening for whole-mod bulk passes (above); the 1.5.0-era
  defect ledger stays relevant only if our patch is ever unshadowed — consider retiring our 745
  patch entirely if the 91 worker spells are folded into a future re-emit.
- The 7 field-report issue drafts from the 1.5.0 session remain unfiled (maintainer go pending).
- houseCARL: zero tool bugs across ~200 calls / 6 agents this session.
