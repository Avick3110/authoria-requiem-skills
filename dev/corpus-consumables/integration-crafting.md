# Consumables â€” Integration & Crafting Corpus (ALCH / INGR / COBJ)

*Read-only mining of the live ARR 2.0 load order via houseCARL. Instance: `E:\Skyrim Modding\ARR 2.0`,
profile "Authoria - Requiem Reforged". Feeds a new Requiem-patching skill for consumables.
All FormIDs are verbatim wire tokens `XXXXXX:Plugin.esp` â€” reusable in a write.*

---

## 0. Headline doctrine fact â€” the Reqtificator does NOT touch consumables

`Requiem for the Indifferent.esp` (RftI, the Reqtificator output) **defines only two record types**:

| defined_in RftI | count |
|---|---|
| Npc | 48,295 |
| LeveledNpc | 2,649 |

Query evidence:
- `cross_plugin_query type=ALCH plugins=["Requiem for the Indifferent.esp"] group_by=defined_in` â†’ **0 matches**
- `cross_plugin_query type=INGR plugins=["Requiem for the Indifferent.esp"] group_by=defined_in` â†’ **0 matches**
- `plugins=["Requiem for the Indifferent.esp"] defined_in=true group_by=type` â†’ only Npc + LeveledNpc.

RftI touches **zero** ALCH, INGR, and COBJ â€” not as a definer and not as an override (the ALCH/INGR
`plugins=[RftI]` scans return 0 for *touches*, not just for *defines*). **Consumables are entirely a
hand-authored ESP domain.** Unlike weapons/armor/NPCs â€” where you carry inputs and let the Reqtificator
emit the final balanced record â€” for a potion, food, drink, ingredient, oil, or poison **the ESP override
IS the final balanced record.** Nothing about a consumable is deferred to build time. This is the single
most important divergence from the weapon/armor/NPC domains and must anchor the skill.

---

## 1. Contested-consumable landscape (accounting)

**ALCH contested order-wide: 687 records across 49 winning plugins.** Top winners:

| Winner | ALCH won | Role |
|---|---|---|
| Requiem - Alchemy Redone.esp (AR) | 242 | Requiem's own potions/poisons/oils |
| Authoria - ATweaks.esp | 50 | systemic tweak layer |
| Requiem - Food and Beverages Redone.esp (FaB) | 49 | Requiem's own food/drink |
| Authoria - Reqtificated - Sweets and Such.esp | 35 | **per-mod content patch** |
| Authoria - CC Fishing Patches.esp | 34 | Creation-Club content |
| Authoria - Patch - Alcohol Tweaks.esp | 32 | systemic tweak |
| Authoria - Reqtificated - Xelzaz.esp | 30 | **per-mod content patch** |
| Authoria - Reqtificated - Vigilant.esp | 19 | per-mod content patch |
| Authoria - Master Patch - Food and Drink.esp | 16 | cross-mod food master patch |
| Authoria - Reqtificated - Unslaad / Glenmoril / Remiel / LOTD / Morskom / Mrissi â€¦ | 13/11/10/9/6/6 | per-mod content patches |
| Sunhelm - EAS - water patch.esp / Requiem.esp / Requiem - ECSS.esp / USSEP | 10 / 9 / 12 / 5 | third-party + base |

**INGR contested: 225 across 25 winners.** AR dominates with **161**; then ECSS 15, Authoria - Reqtificated -
Vigilant 10, Authoria - Patch - Requiem - Wounds 6, Requiem - Wyrmstooth 6, Requiem.esp 3, plus one-per-mod
`Authoria - CC * Reqtificated.esp` / `Authoria - Reqtificated - *.esp` entries. Ingredients are far less
often modded than potions/food â€” the team mostly leans on AR's own ingredient set.

**The team's naming convention for a modded-consumable patch** (mirrors the weapons domain):
- `Authoria - Reqtificated - <ModName>.esp` â€” per content mod (Xelzaz, Vigilant, Unslaad, Sweets and Such, â€¦)
- `Authoria - CC <Name> Reqtificated.esp` â€” per Creation-Club creation
- `Authoria - Master Patch - Food and Drink.esp` / `Authoria - Patch - Alcohol Tweaks.esp` / `ATweaks.esp` â€”
  systemic / cross-cutting layers
- Requiem's own addon satellites: `Requiem - Food and Beverages Redone - <Mod>.esp` (thin, upstream of the
  Authoria patch â€” see Â§5).

---

## 2. The modded-consumable patch pattern (worked examples)

The live-analogy precedent is **"point the item's effect list at Requiem's OWN magic effects, rebalance
value/weight/flags, and let Requiem's effect records carry the mechanics."** There are three distinct kits,
split by consumable class. In every case the record ends its conflict chain in the Authoria patch (winner
last); prior layers (base mod â†’ Taberu animation patch â†’ FaB per-mod patch) are all forwarded.

### 2a. POTION kit â€” keep the effect links, rebalance the shell

Content mod: `BPUFXelzazFollower.esp` (Xelzaz follower). Patch: `Authoria - Reqtificated - Xelzaz.esp`.

**`0E46A5:BPUFXelzazFollower.esp` â€” BPUFXelzazInvisPotion "Potion of Imperception"**
- Effects list **unchanged** between base and winner: `[0]` BaseEffect `03EB3D:Skyrim.esm` (â†’ **REQ_Alch_Invisibillity**,
  Invisibility, dur 20), `[1]` `0EE8D2:BPUFXelzazFollower.esp` (BPUFMufflePotion, mag 1 dur 20).
- **Only** delta the patch makes: `Value 250 â†’ 70`. Flags = `NoAutoCalc, Medicine`.
- Why it works: `03EB3D` is the *vanilla* Invisibility MGEF FormID, but AR **overrides that MGEF** and renames
  it `REQ_Alch_Invisibillity`. The potion keeps pointing at the same FormID; Requiem's MGEF override supplies
  the Requiem-correct behaviour for free. The patch only fixes the gold value and sets `NoAutoCalc`.

**`0E97AF:...` BPUFXelzazCureDisease** â€” baseâ†’winner: `Flags Medicine â†’ NoAutoCalc,Medicine`; `Value 79 â†’ 50`;
effect `[0]` BaseEffect `0AE722:Skyrim.esm` kept, **magnitude 100 â†’ 0** (the REQ MGEF is fixed-behaviour, so
the per-item magnitude is zeroed).
**`3C301E:...` BPUFXelzazNightEye** â€” `Flags Medicine â†’ NoAutoCalc,Medicine`; `Value 0 â†’ 60`; effect `[0]`
`06B10C:Skyrim.esm` kept (mag 0).

> **Potion rule:** when the modded potion's effects already point at vanilla MGEF FormIDs that Requiem
> retunes (Invisibility, CureDisease, NightEye, Waterwalking, Ethereal, Frost/Fire shields, â€¦), **do not
> rebuild the effect list.** Set `NoAutoCalc` (+`Medicine`), assign a Requiem-consistent gold `Value`, and
> zero any magnitude the REQ MGEF ignores. Keyword set is minimal â€” Invis potion carries only
> `VendorNoSale = 0FF9FB:Skyrim.esm`.

### 2b. FOOD kit â€” rebuild the effect list to the REQ_Apo_Food template

Content mod: `KvSweets.esp` (Sweets and Such). Patch: `Authoria - Reqtificated - Sweets and Such.esp`.

**`0013DB:KvSweets.esp` â€” KvSweetsCakeChocolate "Chocolate Cake"** (base 2 vanilla effects â†’ winner 7):

| idx | BaseEffect (wire token) | resolves to | Mag / Dur |
|---|---|---|---|
| 0 | `000A12:Requiem - Food and Beverages Redone.esp` | REQ_Apo_FoodFortifyHealth | 25 / 900 |
| 1 | `00080D:...FaB` | REQ_Apo_FoodFortifyHealthRegen | 15 / 900 |
| 2 | `000A13:...FaB` | REQ_Apo_FoodFortifyStamina | 25 |
| 3 | `000001:...FaB` | REQ_Apo_FoodFortifyStaminaRegen | 15 |
| 4 | `002EE4:Update.esm` | Survival_FoodRestoreHungerLarge | 0 |
| 5 | `000A36:...FaB` | REQ_Apo_FoodNutritionSM | 10 |
| 6 | `000812:Taberu Animation - Sweets and Such.esp` | aaTaberu_SweetsCakeChocoME (eat-anim marker) | 0 |

Also `Value 25 â†’ 30`. Keywords `[VendorItemFood 08CDEA:Skyrim.esm, GiftChildSpecial 0A248C:Skyrim.esm]`
(the sweet stays child-giftable). Flags `NoAutoCalc, FoodItem`; ConsumeSound `0CAF94:Skyrim.esm` (ITMFoodEat).

**`00140B:KvSweets.esp` â€” KvSweetsCookieChocolate** â€” base 2 â†’ winner 5 effects (Fortify Health `000002:FaB` 7,
`000B4A:FaB` 10, + nutrition + Survival hunger + Taberu); `Value 5 â†’ 7`, `Weight 0.1 â†’ 0.25`.

> **Food rule:** replace the mod's vanilla RestoreHealth-style effects with the REQ food kit â€”
> FortifyHealth + FortifyHealthRegen + FortifyStamina + FortifyStaminaRegen (all `REQ_Apo_Food*` in FaB),
> plus a `Survival_FoodRestoreHunger{Small|Large}` hunger hook from `Update.esm`, plus `REQ_Apo_FoodNutrition*`.
> **Preserve** the trailing Taberu animation marker effect (mag 0) if the mod ships a Taberu patch.
> Keyword = `VendorItemFood`; the mechanics live in the effect list, **not** in a tier keyword (contrast
> weapons/armor, where the keyword carries the tier).

### 2c. ALCOHOL / DRINK kit â€” the REQ_Alcohol template

**`FA82FA:BPUFXelzazFollower.esp` â€” BPUFXelzazMeadSnowberry "Xelzaz's Southfringe Mead"** (base 1 â†’ winner 5):

| idx | BaseEffect | resolves to | Mag |
|---|---|---|---|
| 0 | `18ED7C:Requiem.esp` | REQ_Alcohol_FortifyHealth | 45 |
| 1 | `AE3729:Requiem.esp` | REQ_Alcohol_DamageRegeneration ("Inebriation") | 15 |
| 2 | `002EE2:Update.esm` | Survival_FoodRestoreHungerSmall | 0 |
| 3 | `000A37:...FaB` | REQ_Apo_AlcoholNutrition | 10 |
| 4 | `01B75C:TaberuAnimation.esp` | aaaBlackBriarMead_Animation_ME | 0 |

Keywords `[VendorItemFood 08CDEA, VendorNoSale 0FF9FB]`, `Value 100`, Flags `NoAutoCalc, FoodItem`.

> **Alcohol rule:** FortifyHealth + Inebriation (both `REQ_Alcohol_*` in **Requiem.esp**, not FaB) +
> Survival hunger hook + `REQ_Apo_AlcoholNutrition` (FaB) + Taberu marker.

### 2d. TEA / warm-drink variant

**`776C69:...` BPUFXelzazWarmTea** â€” 4-effect winner including `002EE5:Update.esm` (a Survival warm/food hook)
+ `000A36:FaB` REQ_Apo_FoodNutritionSM (5) + a warm effect + Taberu marker; `Value 15 â†’ 10`. Conflict chain is
4-deep: base â†’ `Taberu Animation - Xelzaz.esp` â†’ `Requiem - Food and Beverages Redone - Xelzaz.esp` â†’
`Authoria - Reqtificated - Xelzaz.esp` (winner). Confirms the **layer order**: base mod, then Taberu anim,
then the FaB per-mod satellite, then the Authoria Reqtificated patch on top.

---

## 3. The COBJ crafting layer

### 3a. Requiem - Alchemy Redone's own recipes (25 defined COBJ; ~28 touched)

`cross_plugin_query type=COBJ plugins=["Requiem - Alchemy Redone.esp"] defined_in=true` â†’ **25** records.
(The task's "28 touched" figure includes ~3 vanilla/Requiem COBJ AR merely overrides.) All 25 are
**alchemy-adjacent crafting, NOT cooking food**, in three families:

1. **Weapon-coating oils** `REQ_Smelting_Oil_{Fire|Frost|Shock|Silver}{1,2,3}` â†’ create `REQ_Oil_*`
2. **Weapon-coating powders** `REQ_Smelting_Powder_{Fire|Frost|Shock|Silver}{1,2,3}` â†’ create `REQ_Powder_*`
3. **Fortify-Enchanting potions** `REQ_Cooking_Potion_FortifyEnchanting{1,2,3,4}` â†’ create the `REQ_Potion_FortifyEnchanting*`
   (Diluted/Faint/Fair/Good) at `039CFB/039D02/039D0A/039D12:Skyrim.esm`

**Every one uses the same station:** `WorkbenchKeyword = 0A5CB3:Skyrim.esm` (**CraftingCookpot**) â€” NOT the
alchemy lab. Requiem routes these alchemical crafts through the cooking pot.

**Recipe shape (verbatim):**
- `REQ_Smelting_Oil_Fire2` (`000830`): Items = `03AD5E:Skyrim.esm` FireSalts Ã—1 + `0F11C0:Skyrim.esm`
  DwarvenOil Ã—3 â†’ `CreatedObject 000807:AR` (REQ_Oil_Fire) Ã—**9**. (Tier 1 makes Ã—6; count scales with tier.)
  Conditions: `HasPerk 0C07CA:Skyrim.esm` (**REQ_Alchemy_AlchemicalLore2**) `== 1`; `GetActorValue Alchemy >= 50`;
  `GetActorValue Alchemy < 100` â€” i.e. a **skill-band-gated tier**.
- `REQ_Cooking_Potion_FortifyEnchanting2` (`000821`): Items = `063B5F:Skyrim.esm` SprigganSap Ã—1 +
  `06B689:Skyrim.esm` HagravenClaw Ã—1 â†’ REQ_Potion_FortifyEnchanting2 Ã—1. Conditions:
  `HasKeyword AD3A3B:Requiem.esp` (**REQ_RacialSkills_CreatePotionsUnperked**, flagged **OR**) â€” OR â€”
  `HasPerk 0BE127:Skyrim.esm` (**REQ_Alchemy_AlchemicalLore1**); then `GetBaseActorValue Alchemy >= 25` and
  `< 50` (a base-skill band).

> **COBJ rule for a craftable consumable:** station = `CraftingCookpot 0A5CB3:Skyrim.esm`; gate on the
> Requiem Alchemy perk tree (`REQ_Alchemy_AlchemicalLore1 = 0BE127`, `Lore2 = 0C07CA`) **OR** the racial
> `REQ_RacialSkills_CreatePotionsUnperked = AD3A3B:Requiem.esp` keyword, plus an Alchemy-skill band
> (`GetActorValue`/`GetBaseActorValue Alchemy`, tiered thresholds). Ingredients are vanilla reagents; higher
> tiers raise `CreatedObjectCount`, not potency.

### 3b. Cooked-food recipes â€” a separate, thin, ungated Authoria layer

Reverse-lookup `type=COBJ references=[FaB food FormIDs]`:
- `000D58:FaB` REQ_Food_Baked_GarlicBreadSlice â†’ **no COBJ** (loot/vendor only; not craftable).
- `000C53:FaB` REQ_Food_Meat_BearRaw â†’ **one** COBJ: `000807:Authoria - Patch - Food Recipes.esp`
  `Authoria_Cook_Food_Cooked_BearMeat`: Items = `000C53:FaB` BearRaw Ã—1 â†’ `000801:Authoria - Patch - Food
  Recipes.esp` (Authoria_Food_Cooked_BearMeat "Bear Haunch") Ã—1, station CraftingCookpot, **Conditions = []
  (ungated)**.

`Authoria - Patch - Food Recipes.esp` = **6 COBJ + 6 Ingestible** â€” a small custom layer turning FaB raw
meats into cooked foods, with **no perk/skill gate**. This is distinct from the modded-consumable *patch*
pattern (Â§2) and from AR's perk-gated alchemy COBJ (Â§3a). **FaB itself defines no cooking recipes** â€” most FaB
food is not craftable; only a handful of raw meats gain an Authoria recipe.

---

## 4. Satellite compatibility patches (model)

The Requiem addon satellites are **thin, upstream** overrides â€” the model of "how a proper compat patch for
FaB/AR looks" â€” but note the Authoria "Reqtificated" patch usually wins *on top* of them.

- **`Requiem - Food and Beverages Redone - LoTD.esp`** = **1 Ingestible**, `2E0517:LegacyoftheDragonborn.esm`
  DBM_cinnaspicecookie. Baseâ†’winner: `Value 4 â†’ 10`, `Weight 0.5 â†’ 0.75`, vanilla effects swapped to the REQ
  kit (`18ED7C:Requiem.esp` etc.), **+1 FaB food keyword `000A21:FaB`**. Clean 2-plugin chain (base â†’ FaB-LoTD
  winner). **This is the canonical satellite shape: retarget effects â†’ REQ kit, add FaB nutrition/food keyword,
  rebalance value/weight.**
- **`Requiem - Food and Beverages Redone - Auri.esp`** = **1 Ingestible**, Jagga (`30E7E8:018Auri.esp`). The
  satellite renames the EditorID to `REQ_Alcohol_Moderate_Jagga`. 5-deep chain: `018Auri.esp` â†’ `Snazzy_Auri_Items.esp`
  â†’ `...FaB-Auri.esp` â†’ `DBM_Auri_Patch.esp` â†’ **`Authoria - Reqtificated - Auri.esp` (winner)**. Winner uses the
  REQ_Alcohol kit (`18ED7C` FortifyHealth 45, `AE3729` Inebriation 15, + nutrition + Survival), `Value 5 â†’ 10`,
  `Weight 0.5 â†’ 0.75`, adds FaB keyword `000C51:FaB`.
- **`Requiem - WAR Alchemy Redone Patch.esp`** = **1 Quest**, `000A9A:Requiem - Weapons and Armor Redone.esp`
  `REQ_Quest_WAR_ThrowingKnife_PoisonApply` (StartGameEnabled, RunOnce, VM script + 1 alias; the patch tweaks a
  script property on the alias). **Not a consumable patch** â€” it bridges WAR throwing knives into Requiem's
  poison-application system. Belongs to the alchemy *systems* boundary, not the item-balancing story.

---

## 5. LVLI boundary (characterize only â€” placement doctrine is another skill)

- **AR touches 106 LeveledItem**: defined_in Requiem.esp 55, Skyrim.esm 37, Dragonborn.esm 6,
  **Requiem - Alchemy Redone.esp 6**, Dawnguard.esm 2. AR **defines 6** of its own: `LItemPotionFortifyUnarmed(Best)`,
  `LItemPoisonDamageStrength(Best)`, `LItemPoisonSilence(Best)`. Example `000828:AR LItemPotionFortifyUnarmed` =
  16 entries, flags `CalculateFromAllLevelsLessThanOrEqualPlayer, CalculateForEachItemInCount` â€” a tiered pool of
  fortify-unarmed potions. The other 100 touches are AR **injecting its potions/poisons/oils into existing
  vanilla + Requiem loot lists.**
- **FaB touches 20 LeveledItem**: Skyrim.esm 19 + Requiem.esp 1 â€” FaB **edits vanilla food/loot lists** to
  distribute its foods; it defines **none** of its own here.

Boundary note for the consumables skill: distributing a modded consumable into the world = editing/adding to a
leveled list, which is the **leveled-list skill's** job. The consumables skill owns the *record balance*
(effects/value/weight/flags/keywords) and the *recipe* (COBJ), not the placement.

---

## 6. Systemic knobs AR turns

- **GMST `fAlchemySkillFactor` (`10C4D9:Skyrim.esm`)**: vanilla 1.5 â†’ Requiem.esp 1.1 â†’ **AR 2** (winner). AR
  *raises* the alchemy skillâ†’potency scaling (potions get stronger per skill point). Single knob, whole-system.
- **AVIF `AVAlchemy` (`000456:Skyrim.esm`)**: AR touches it (override depth 5) but the live winner is
  `Requiem - AR Special Feats Patch.esp` â€” the Alchemy skill's actor-value definition (skill-use description /
  perks) is tuned by AR then finalized downstream.
- **8 Perk overrides** (AR retunes the Alchemy perk tree): `REQ_Alchemy_ImprovedElixirs 058216`,
  `PurificationProcess 05821D`, `AlchemicalLore1 0BE127`, `AlchemicalLore2 0C07CA`, `ConcentratedPoisons 105F2F`
  (all Skyrim.esm FormIDs), `AlchemicalIntellect 1D9AAB:Requiem.esp`, `RFTI_Player_Alchemy AD3A2F:Requiem.esp`,
  `RFTI_Player_Enchanting AD3A30:Requiem.esp` (this last one's winner is Requiem - Magic Redone). These govern
  potion strength multipliers, poison behaviour, and the crafting-condition perks referenced by the COBJ in Â§3a.
- **No GLOB** touched by AR (`type=GLOB plugins=[AR]` â†’ 0).

> The skill should treat these as **read-only context, not edit targets**: a new consumable inherits the
> alchemy economy through these existing knobs. Never re-tune `fAlchemySkillFactor` or the perk tree to balance
> one item.

---

## 7. Inconsistencies (surfaced, not smoothed)

1. **EditorID scheme divergence on the same item.** FaB-Auri renames Jagga â†’ `REQ_Alcohol_Moderate_Jagga`,
   but the final Authoria winner (`Authoria - Reqtificated - Auri.esp`) calls it **`REQ_Drink_Jagga`**. Two
   different Requiem naming conventions (`REQ_Alcohol_Moderate_*` vs `REQ_Drink_*`) coexist across the patch
   stack for one record. The skill must not assume a single canonical EditorID scheme.
2. **Potion vs food effect-handling asymmetry.** Potions **keep** their (vanilla-FormID) effect links and only
   rebalance Value/Flags/magnitude â€” Requiem's MGEF override carries the behaviour. Food/drink get their entire
   effect **list rebuilt** to the `REQ_Apo_Food*` / `REQ_Alcohol_*` kit. Same domain, opposite technique â€” the
   deciding factor is whether the item's effects point at MGEFs Requiem already overrides.
3. **"Alchemy" crafts happen at the cooking pot.** AR's oils, powders, and fortify-enchanting potions all use
   `CraftingCookpot 0A5CB3:Skyrim.esm`, gated by Alchemy perks/skill â€” not the alchemy lab. Counter-intuitive;
   easy to get wrong when authoring a new craftable consumable.
4. **Two unrelated crafting layers with different gating philosophies.** Requiem's own consumable COBJ are
   perk+skill-band gated (Â§3a); the team's `Authoria - Patch - Food Recipes.esp` cooked-food COBJ are **ungated**
   (empty Conditions). A modded craftable food does not automatically inherit AR's perk gates.
5. **Cooked food is mostly NOT craftable.** FaB defines zero cooking COBJ; garlic bread etc. have no recipe.
   Only a few raw meats get an Authoria recipe. "Add a cooking recipe" is the exception, not the default, for
   food.
6. **Reqtificator blindness (the big one).** RftI touches no ALCH/INGR/COBJ. Every consumable balance decision
   is final in the hand-authored ESP; there is no build-time safety net for consumables the way there is for
   weapons/armor/NPCs.

---

## 8. Accounting summary

| Metric | Value | Source |
|---|---|---|
| RftI-defined consumables (ALCH/INGR/COBJ) | **0** | group_by=defined_in, all zero |
| RftI-defined records total | Npc 48,295 + LeveledNpc 2,649 | defined_in group_by=type |
| ALCH contested order-wide | 687 / 49 winners | conflicts_only group_by=winner |
| INGR contested order-wide | 225 / 25 winners | conflicts_only group_by=winner |
| AR COBJ defined | 25 (oils 15, powders overlap, fortify-ench 4; ~28 touched) | defined_in |
| AR COBJ station | CraftingCookpot (0A5CB3) â€” 100% of sampled | batch read |
| Authoria - Patch - Food Recipes.esp | 6 COBJ + 6 Ingestible (ungated) | group_by=type |
| AR LeveledItem touched / defined | 106 / 6 | group_by=defined_in |
| FaB LeveledItem touched / defined | 20 / 0 (19 Skyrim + 1 Requiem, all overrides) | group_by=defined_in |
| AR systemic: GMST / AVIF / Perk / GLOB | 1 / 1 / 8 / 0 | type-scoped touches |

*Truncation note: several conflict-tree effect diffs reported "+N more field(s)" and were re-read at depth for
the records quoted verbatim above; unquoted counts are exact match totals from group_by (uncapped).*
