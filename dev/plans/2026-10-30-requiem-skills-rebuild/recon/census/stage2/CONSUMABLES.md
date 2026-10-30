# Census: CONSUMABLES (stage 2)

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 9 types, 2,551 diff rows across 60 plugin-type pairs. Ingredient 1,433 rows / 15 plugins; Ammunition 415 / 21;
HeadPart 365 / 1; Flora 136 / 4; Ingestible 105 / 6; MiscItem 50 / 7; Static 37 / 3; SoulGem 6 / 1; Tree 4 / 2.
**Held-out** (eval references): Falkreath Shades (Ingredient 7 rows, Ingestible 12, Ammunition 7, Tree 2) and The Rot Below
(Ingestible 6, Ammunition 4); no held-out rows in MiscItem, SoulGem, Flora, Static, HeadPart. Conventions below are
built from non-held-out rows; held-out is the validation column. Counts come from `stage2/work/consensus/<Type>.json`.

Domain character: **small and mostly hand-tuned.** Only two conventions are tight (ammo kit, mushroom lists). Everything
else is per-patch judgement, and four of nine types are noise or one-plugin. Class legend: RA = Requiem-aligned,
HC = house convention (Aaron confirms), BK = baked, noise.

## Conventions

| # | Convention | Class | Layer | Rows / plugins | Per-plugin spread | Held-out | Conf |
|---|---|---|---|---|---|---|---|
| C-1 | Ammo carries the Requiem ammo keyword kit: `AD3B2D` NoDamageRescale + AmmoWeight_* + ArmorPiercingArrow_Tier* + material (+ REQ_NoArrowRecovery on enchanted) | RA | input | 221 keyword rows over 56 records / 20 | Glenmoril 172 and Vigilant 61 of 415 ammo rows; holds in 18 other plugins | Falkreath + Rot Below carry the same kit patterns (Requiem keywords `ADDDD2`, `AD397F`, `9D1F4C`): follows | high |
| C-2 | Ammo Weight := 0 (weight comes from REQ_AmmoWeight_* keywords) | RA | input | 58 / 20 | even across plugins | follows (2 rows) | high |
| C-3 | Ammo Damage hand-set to tier values (after/before 2.6-10x: 5x on 23 rows, 4x on 14, 3x on 7, 6x on 6, 10x on 3; mostly 4-5x vanilla) together with `AD3B2D`; the build would otherwise x4 again | BK | baked | 55 / 19 | spread over 19 plugins | follows (2 rows) | high |
| C-4 | Ammo Flags: drop IgnoresNormalWeaponResistance, keep NonBolt | RA (unsampled) | input | 6 / 2 | 2 plugins | none | low |
| C-5 | Ingredient effects rewritten onto REQ_Alch_* MGEFs (same FormIDs as vanilla, no re-keying) with hand-converted durations and magnitudes; restore dur 20 / mag ~1, weakness 30; other durations vary | mixed (RA for restore, HC otherwise) | input | 525 added / 517 removed, 10 plugins | Bruma MP is 1,260 of 1,433 Ingredient rows; Moonpath keeps dur 0 and scales magnitude ~x3; Glenmoril FortifyDestruction dur 20 vs Requiem 60 | Falkreath rewrote GRVEShadeGoo's effects: follows the pattern, but there is no tier rule to test | high (that it varies), low (any single rule) |
| C-6 | Hand-valued ingredients set `NoAutoCalculation` | RA | input | 155 / 13 | Bruma MP + Glenmoril + Ashe + Falkreath; holds outside Bruma | Falkreath 1 row: follows | high |
| C-7 | IngredientValue == Value on NoAutoCalc ingredients | RA (unsampled) | input | 148 / 10 (+26 Value rows / 7) | not sampled | none | med |
| C-8 | Potion effects: restore potions follow the Requiem shape (dur 20, per-second magnitude, NoAutoCalc); non-restore potions are multi-effect hand designs | mixed | input | 73 effect rows / 6 | Remiel is 74 of 105 rows; Falkreath shows the same restore shape (dur 20, mag 8) | Falkreath restore: follows. Rot Below sets dur 3600 on three effects of one Undead Blacksmith potion (source tuning, no Requiem shape) | med |
| C-9 | Potions set `NoAutoCalc` (Medicine / Poison flags preserved) | RA (unsampled) | input | 14 / 4 | 4 plugins | Falkreath 2 rows: follows | med |
| C-10 | Mushroom flora Ingredient points at the Requiem `REQ_LI_Mushroom01..06` list of the matching vanilla mushroom (01=2500D3, 02=2500DA, 03=2500DD, 04=2500E0, 05=2500E3, 06=2500E6) | RA | input | 24 / 1 | all Blended Shrooms: 23 follow, 1 outlier (000807); spread untestable, single plugin. The other 3 Flora Ingredient rows are not mushroom lists (2 Glenmoril nulls, 1 Bruma butter churn 602B1C -> HearthFires 00353C) | none | high |
| C-11 | Source-mod harvest flora repointed at the vanilla / Requiem ingredient (Falkreath Red Nightshade tree (editorid GRVEFloraRedNightshade01; name unchecked) -> vanilla Nightshade `02F44C`) | RA (single case) | input | 2 / 1 | one held-out plugin | Falkreath is the case itself | low |
| C-12 | Bruma MP strips Playable from all 219 BSAssets head parts (hair, mouths, hairlines); sex and extra-part flags kept | HC | input | 219 / 1 | Bruma only | none | med |
| C-13 | Food and Drink MP replaces flora "Harvest" text with Iconographia glyphs | HC (UI), noise | input | 51 / 1 | Food and Drink only | none | high (as noise) |
| C-14 | MiscItem Value / Weight rebalanced per item (Value 100 x4, 35 x2, 75 x2, 150 x2, up to 3000; Weight 2 x7, 0.05 x4) | unclear, no rule | input | 32 / 7 | Bruma MP is 23 of 50 MiscItem rows | none | low |
| C-15 | Static (36 Praedy staff first-person duplicates) and SoulGem (LOTD museum gems): no gameplay convention | noise / HC | input | 36 / 2 and 6 / 1 | single source each | none | med |

## Upstream checklist (what a patch must carry)

- **Ammo:** the full keyword kit from C-1 (always `AD3B2D` when damage is hand-set), Weight 0, tier keywords by judgement.
  Pick the tier by comparing a live Requiem arrow of similar material (live Requiem WAR winners: damage Iron 40, Steel 50, Elven 60, Dwarven 70,
  Orcish 80, Glass 90, Ebony 100, Daedric 120; value 1/1/2/3/3/14/22/55; ARR final damage ranges 10-120).
- **Ingredients and potions** from a source mod: NoAutoCalc, `Value == IngredientValue`, effects on the Requiem-overridden
  MGEFs (same FormIDs). Restore effects follow dur 20. For fortify / resist / weakness read a live Requiem ingredient with
  the same effect (fortify 60 / 120 / 180, resist 30-300, weakness 30-60). There is no single scale.
- **Mushroom flora** from a variant mod: Ingredient -> the matching `REQ_LI_Mushroom0N`.
- **Harvest flora / trees** whose ingredient was removed or replaced: keep a live ingredient (a null link yields nothing).
- Value, Weight and presentation fields of MISC, SoulGem, Static, HeadPart: no Requiem rule; leave to the source mod
  unless the item feeds a Requiem-scale economy (C-14).

## Downstream bake package (what ARR writes that the build would produce)

- **Ammo Damage (C-3):** `AmmunitionTransformer.cs` does Damage x4 unless AlreadyReqtified / NoDamageRescaling /
  Damage <= 0. ARR hand-sets tier values (mostly 4-5x vanilla) and adds `AD3B2D` (Requiem's own arrows carry `AD3B2D` too). A patch that
  writes the final value without the keyword gets rescaled a second time. This is the only baked item in the domain.
- Nothing else is baked: no code touches INGR, ALCH, MISC, SLGM, FLOR, TREE, STAT or HDPT.

## Per-type detail

**Ingredient.** Bruma MP carries 1,260 of 1,433 rows, so every family is dominated by one plugin. Flags and effect
conversion appear in 10-13 plugins, so they hold outside Bruma. The verifier corrected the reader on durations: not a
uniform "weakness / fortify 30"; Requiem fortify is 60 / 120 / 180 and resist 30-300. Removed / added effect pairs mostly
share the same BaseEffect (a magnitude / duration edit, not a re-key). Keyword adds (23 rows: VendorItemIngredient,
VendorItemFoodRaw, REQ_ArgonianTreat on Kwama Egg) and weight (12 rows) are unsampled leads.

**Ingestible.** Requiem `REQ_Potion_RestoreHealth1-5` are NoAutoCalc, dur 20, mag 3 / 5 / 8 / 10 / 12, value 20-100; ARR
restore potions match (Falkreath mag 8 dur 20). Non-restore potions do not follow Requiem: Remiel Fortify Sneak is dur 20
against Requiem 60, and ARR adds side effects Requiem lacks (Slow, Drain Stamina, Damage Health). Requiem Invisibility and
Fortify Sneak potions are autocalc (Flags 0); ARR's are NoAutoCalc. No single value scale (Requiem Invisibility 214-587).

**Ammunition.** The most uniform type; see C-1..C-4. Value (38 rows / 14 plugins) is unclear: Requiem arrows are 1-55 gold by tier (Iron 1 .. Daedric 55),
ARR modded ammo 15-30; no rule can be read. Keyword removals (2 rows): Auri drops Dawnguard `019822` from Bone arrow, Snow
Elven Arsenal drops `01E71B`; both unresolved.

**MiscItem, SoulGem.** 50 and 6 rows, no Requiem comparable, no code. One keyword swap on Tools of Kagrenac Ayleid Gold
(VendorItemClutter `0914E9` -> VendorNoSale `0FF9FB`; live Value 0). LOTD sets Azura's Star / Black Star / Riften quest
gem Value to 115000 / 115000 / 200 and Weight from 0 to 1 / 1 / 0.5: museum tuning.

**Flora, Tree.** Flora is 136 rows, 109 from Food and Drink MP (Harvest glyphs, bookkeeping). The real content is the
Blended Shrooms lists (C-10). Tree has 4 rows: Glenmoril nulls two WhiteShade tree ingredients (intent unreadable);
Falkreath (held out) repoints Red Nightshade (editorid) at vanilla Nightshade.

**Ingestible Value (14 rows):** hand-set 50-100, no single 20-100 scale (verifier: Falkreath value 100 at mag 8 vs Requiem tier 3 = 60).

**Static, HeadPart.** Static is 36 new mesh records. HeadPart is Bruma MP only: Requiem.esp touches 0 HDPT, so C-12 is
house-only; BSAssets.esm defines 276 HDPT, 219 Playable, all stripped, including mouths.

## Suspected bugs (for Aaron's review, not filed)

| Record + plugin | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|
| 000807 FloraMushroom02Small_c (Bleeding Crown), Blended Shrooms.esp | Ingredient | 2500DD REQ_LI_Mushroom03 (White Cap list) | 2500DA REQ_LI_Mushroom02 | siblings 000804/5/6 -> 2500DA; Requiem FloraMushroom02Small 04DA05 -> 2500DA; verifier confirmed | outlier-bug |
| zzzLrhTreeFloraWhiteShade01 + one sibling, Glenmoril.esp | Ingredient | null | zzzLrhWhiteFlower 007289 (exists, Flags 0) | tree yields nothing; deliberate vs leftover cannot be told | lead (unclear) |
| maggot egg nest and kin cluster flora, Glenmoril.esp | Ingredient | null | unknown | same pattern | lead (unclear) |
| Ryn's Farms brandy, Ryn's Farms.esp | Effects | loses `00082F:Requiem - Foods and Ingestibles`, gains `10C645` + `03EAEB` | unknown | unresolved | lead (unclear) |

## Open calls for Aaron

1. Fix Blended Shrooms 000807 (Bleeding Crown Small_c -> White Cap list)?
2. Are the nulled Glenmoril tree / flora ingredients deliberate (harvest disabled) or leftovers?
3. Confirm C-12: hide BSAssets hairs and mouths from character creation while NPCs keep them (inferred intent).
4. Ammo gold value: keep 15-30 for 60-110 damage ammo, or scale toward Requiem 1-5?
5. Potion side effects (Slow, Drain Stamina, Damage Health) and NoAutoCalc on non-restore potions: intended design, or
   follow Requiem's autocalc potions?

## Limits

- **Unsampled families (leads, not verified):** Ingredient IngredientValue (148), Value (26), Keywords (23 + 8), Weight
  (12); Ingestible Flags (14); Ammunition Flags (6) and Keywords removed (2). C-4, C-7, C-9 rest on these.
- MiscItem, SoulGem, Static, HeadPart classes rest on family numerics and a few live reads; none re-derived against a
  Requiem comparable (none exists).
- Ingredient is Bruma-dominated (88 %); C-5 / C-6 hold elsewhere by plugin counts, not per-row reads.
- Held-out coverage is thin: 0 rows for six of nine types; Ingredient 7, Ingestible 18, Ammunition 7, Tree 2. Held-out
  results come from variant listings, not row-by-row diffs.
- Untouched records: see `stage2/out/UNTOUCHED.md`. Its MISC sample (BSHeartland vendor misc, barrels, ingots) was not read
  and needs no Requiem change unless it holds unpatched gear. The ksws03 load failure does not touch these types.
