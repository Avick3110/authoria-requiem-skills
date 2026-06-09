# Worked Examples

Real in-scope Requiem armor, derived end-to-end with the live-analogy loop. All values mined from
the conflict winners (2026-06-08).

## 1 — Heavy set piece: Ebony Gauntlets (from the Ebony Cuirass)

Goal: derive ebony **gauntlets** knowing only the ebony **cuirass**.

1. **Comparable.** Ebony Cuirass `013961` — AR 500 / Val 5000 / Wt 40, keywords
   `[heavy 06BBD2, ebony 06BBD8, cuirass 06C0EC, vendor 08F959]`.
2. **Per-part ratio (gauntlets = ×0.30 on AR, value, weight).** AR 150, Value 1500, Weight 12.
3. **Keywords.** Swap the part keyword cuirass→gauntlets (`06C0EF`). It's a **heavy** gauntlet, so
   add `PerkFists<Material>` — for ebony that's `PerkFistsEbony 02C178` (read off the comparable).
   Final: `[heavy 06BBD2, ebony 06BBD8, gauntlets 06C0EF, vendor 08F959, PerkFistsEbony 02C178]`.
   No ranged-resistance keyword (that's cuirass-only and Reqtificator-assigned); no tempering
   keyword (Reqtificator assigns `ebonySmithing` from the set keyword).
4. **Crafting.** Clone the ebony cuirass's forge + temper COBJ, swap `CreatedObject` to the
   gauntlets and the `Items` to the gauntlet inputs; temper at the **armor workbench** `0ADB78`.

**Verification:** the live Ebony Gauntlets winner (`013962`) is AR 150 / Val 1500 / Wt 12 with
exactly `[PerkFistsEbony 02C178, heavy 06BBD2, ebony 06BBD8, gauntlets 06C0EF, vendor 08F959]` —
an exact match. The loop reproduces Requiem's own numbers, and the heavy-gauntlet fist-perk rule
holds.

## 2 — Light set piece: Dragonscale Gauntlets (the light/heavy contrast)

Goal: derive dragonscale (light) **gauntlets** from the dragonscale **cuirass**, contrasting the
fist-perk rule with example 1.

1. **Comparable.** Dragonscale Cuirass `01393E` — AR 300 / Val 8000 / Wt 15, keywords
   `[light 06BBD3, dragonscale 06BBD6, cuirass 06C0EC, vendor 08F959]`.
2. **Per-part ratio (×0.30).** AR 90, Value 2400, Weight 4.5.
3. **Keywords.** Swap part → gauntlets `06C0EF`. It's a **light** gauntlet, so **no** `PerkFists`
   keyword. Final: `[light 06BBD3, dragonscale 06BBD6, gauntlets 06C0EF, vendor 08F959]` (the live
   record also carries the incidental Survival tag `002ED9` — copy what the comparable has).
4. **Reqtificator-assigned.** The cuirass would get ranged-resistance tier3 (light high); the
   gauntlets get tempering `draconicSmithing` from the set keyword. Neither is stamped.

**Verification:** the live Dragonscale Gauntlets winner (`01393F`) is AR 90 / Val 2400 / Wt 4.5
with `[light 06BBD3, dragonscale 06BBD6, gauntlets 06C0EF, vendor 08F959, Survival_ArmorWarm
002ED9]` and **no PerkFists** — exact match. This is the case that proves only heavy gauntlets get
the fist perk.

## 3 — Shield: Daedric Shield (the shield ratio + block links)

Real record `REQ_Heavy_Daedric_Shield` (`01396E`). Shields are the one part where the AR ratio
(×0.60) differs from value/weight (×0.50), and they carry block/bash links.

1. **Comparable.** Daedric Cuirass `01396B` — AR 600 / Val 25000 / Wt 50.
2. **Shield ratio.** AR ×0.60 = 360; Value ×0.50 = 12500; Weight ×0.50 = 25.
3. **Keywords.** `[heavy 06BBD2, daedric 06BBD4, vendor 08F959, shield part 0965B2]` — the shield
   part keyword replaces the body-slot part keyword.
4. **Block / bash.** `BashImpactDataSet = 0183FE`, `AlternateBlockMaterial = 016979` — copied from
   the comparable. Ranged resistance does **not** apply to shields (cuirass-only).

**Verification:** the live winner is AR 360 / Val 12500 / Wt 25, BashImpactDataSet `0183FE`,
AlternateBlockMaterial `016979` — exact. WAR (`Requiem - Weapons and Armor Redone.esp`) owns most
shields/bucklers; read the winner.

## 4 — Unique / faction armor (the deviation case)

A named faction or unique piece (e.g. a Nightingale or Ancient-Shrouded set, or a modded artifact
cuirass) keeps the *structure* but may deviate on value and resist tier. Method:

1. **Confirm it's a unique, not custom gear** — run the acquisition trace (`## Judgment` in
   `SKILL.md`): reverse-reference the FormID for `ConstructibleObject` and `LeveledItem`. Craftable
   or leveled ⇒ treat as normal gear on the material ladder. Non-craftable + single fixed reward ⇒
   unique; keep the structure, let value be bespoke.
2. **Read the closest `REQ_*` faction comparable** for the keyword skeleton. Faction light sets get
   their resist tier from `feature_damageResistances.facionLeader` (nightingale, ancientShrouded,
   penitusOculatus, thievesGuildMaster → tier4) — but you still don't stamp the tier; the set
   keyword drives it. Nightingale tempering is `legendaryBlacksmithing`.
3. **Value** may be hand-set far off the ladder; take the design intent or the real comparable
   rather than ladder-deriving. AR still follows the part × tier shape (an artifact cuirass is
   still a cuirass on the AR ladder).
4. **A novel material** with no Requiem set keyword borrows the closest set keyword so the
   Reqtificator assigns a resist tier + tempering perk — state the choice explicitly.

Mirror the skeleton from the closest comparable and let value/resist be bespoke — but say so rather
than silently inventing numbers. Per-piece unique effects live in
`…\Reqtificator\documentation\Artifacts.md`.
