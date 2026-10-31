# requiem-consumable-patching — live verification vs load order (2026-07-17)

Verified the four reference docs against the live Requiem MO2 instance via houseCARL (read-only).
`Requiem for the Indifferent.esp` INACTIVE (expected). ~21 houseCARL calls.

## Coverage census (group_by=type, all touched records)

**Requiem - Alchemy Redone.esp — 717 records, 15 types:**
| Type | Count | Doc coverage |
|---|---|---|
| Ingestible (ALCH) | 287 | covered (potion/poison/oil + food overlap) |
| Ingredient (INGR) | 183 | covered (ingredients.md) |
| LeveledItem (LVLI) | 106 | characterized (crafting doc: "6 pools + ~100 injects") |
| MagicEffect (MGEF) | 80 | covered (routed to magic-patching for new) |
| ConstructibleObject (COBJ) | 28 | covered (crafting doc says "25"; live 28) |
| Perk | 8 | mentioned (crafting doc: "8 Alchemy-tree perk overrides") |
| ObjectEffect (ENCH) | 7 | **NOT mentioned** (all overrides, 0 defined) |
| ArtObject | 4 | **NOT mentioned** |
| Scroll | 4 | **NOT mentioned** (1 defined: REQ_Powder_Silver 000805 — thrown silver powder) |
| Projectile | 3 | **NOT mentioned** (oil/powder delivery) |
| Book | 2 | **NOT mentioned** |
| Keyword | 2 | covered |
| ActorValueInformation | 1 | mentioned (AVAlchemy 000456) |
| GameSettingFloat | 1 | mentioned (fAlchemySkillFactor 10C4D9) |
| Spell | 1 | **NOT mentioned** |

**Requiem - Food and Beverages Redone.esp — 354 records, 11 types:**
| Type | Count | Doc coverage |
|---|---|---|
| Ingestible (ALCH) | 196 | covered (food-drink-kits.md; doc says FaB won 59/196) |
| Flora (FLOR) | 52 | **NOT mentioned** (all overrides, 0 defined — re-points harvestable food plants) |
| MagicEffect (MGEF) | 37 | covered (REQ_Apo_* library) |
| Keyword (KYWD) | 29 | covered (LoreBox_* taxonomy) |
| LeveledItem (LVLI) | 20 | characterized (crafting doc: "FaB edits 20 vanilla food lists") |
| Race | 8 | mentioned as OUT of scope ("never a race-record edit from this skill") but FaB itself edits 8 |
| GlobalFloat | 4 | not mentioned |
| Perk | 3 | not mentioned |
| Spell | 3 | conceptually referenced (Green Pact watcher + FoodPoisoning); not enumerated |
| FormList | 1 | not mentioned |
| GlobalShort | 1 | not mentioned |

MGEF, GMST, LVLI, COBJ, KYWD — all covered/mentioned. The types the docs miss: **AR → ENCH/Scroll/Projectile/ArtObject/Book/Spell** (mostly overrides + the thrown powder/oil delivery form); **FaB → Flora (52, harvestable plants), Race (8), Perk (3), Globals (5), FormList (1)**.

## Verdict table (claim → status; every claim FormID+EditorID+plugin)

| # | Claim (doc) | Live check | Verdict |
|---|---|---|---|
| 1 | VendorItemPotion = 08CDEC, VendorItemPoison = 08CDED (Skyrim.esm KYWD) | both resolve exactly | CONFIRMED |
| 2 | RestoreHealth MGEFs 03EB15/03EB17/03EB16 = REQ_Alch_RestoreHealth/Magicka/Stamina (won by AR) | resolve exact | CONFIRMED |
| 3 | Restore-Complete MGEFs 0FFA03/04/05 won by Requiem.esp | REQ_Alch_Restore*Complete, winner=Requiem.esp | CONFIRMED |
| 4 | RestoreHealth potion ladder: Value 20/40/60/80/100, mag 3/5/8/10/12, dur 20; Complete V500 mag9999 dur0 | 03EADD/DE/DF/03EAE3/039BE4/039BE5 read live — exact match; Complete BaseEffect 0FFA03 | CONFIRMED |
| 5 | Shape: Weight 0.5, Flags NoAutoCalc (potions) | all 6 tiers W0.5, Flags=NoAutoCalc | CONFIRMED |
| 6 | FortifyCarryWeight MGEF 03EB01; HealthRegen fortify 03EB06 | REQ_Alch_FortifyCarryWeight / FortifyHealthRegeneration | CONFIRMED |
| 7 | Resist F/F/S MGEFs 03EAEA/EB/EC; ResistMagic 039E51 | REQ_Alch_ResistFire/Frost/Shock/Magic | CONFIRMED |
| 8 | Invisibility MGEF 03EB3D EditorID misspelled REQ_Alch_Invisibillity | confirmed misspelling live | CONFIRMED |
| 9 | Waterbreathing MGEF 03AC2D | REQ_Alch_Waterbreathing (winner=BOOBIES_potions patch) | CONFIRMED |
| 10 | Specials: CurePoison 065A64, Dispel AD3A54, ResistPoison AD3A53:Requiem.esp, White Phial 10201D | all resolve to REQ_Potion_* as claimed | CONFIRMED |
| 11 | CureDisease ALCH 0AE723 won by downstream Wounds patch | winner = Authoria - Patch - Requiem - Wounds.esp (doc cited 01CBEC:Wounds.esp specifically) | CONFIRMED (patch identity differs) |
| 12 | Poison/harmful MGEFs won by a downstream compat patch (incl. 2 AR-defined) | DamageHealth/Magicka/Stamina, Lingering*, DrainRegen, Silence 000815:AR, DamageStrength 000816:AR, SoulTrap MGEF, Dispel MGEF — ALL winner=BOOBIES_potions - Requiem Patch.esp | CONFIRMED |
| 13 | Damage MGEFs 03EB42/03A2B6/03A2C6; Lingering 10AA4A/10DE5F/10DE5E | resolve exact EditorIDs | CONFIRMED |
| 14 | Weakness 073F2D/2E/2F + WeaknessMagic 073F51; Fear 073F20/Frenzy 073F29; Paralysis 073F30 | resolve exact | CONFIRMED |
| 15 | DrainRegen naming split: MGEFs 073F2C/073F2B = REQ_Alch_Damage*Regeneration | 073F2C=DamageStaminaRegen, 073F2B=DamageMagickaRegen | CONFIRMED |
| 16 | Oils 000807..00080A:AR = REQ_Oil_Fire/Frost/Shock/Silver | resolve exact | CONFIRMED |
| 17 | Poison of Soul Trap 20B310:Requiem.esp + MGEF 20B30F | REQ_Poison_SoulTrap / REQ_Alch_SoulTrap | CONFIRMED |
| 18 | Ingredient invariants: exactly-4 REQ_Alch_* effects, Value==IngredientValue, Flags NoAutoCalculation | Wheat/Nightshade/Daedra Heart all 4 effects, V==IngrV, NoAutoCalculation | CONFIRMED |
| 19 | Ingredient quartets verbatim (Wheat 04B0BA, Nightshade 02F44C, Daedra Heart 03AD5B) w/ per-MGEF durations (RestoreHealth 20, Fortify-attr 300, DamageRegen 60, Lingering 15, Fear 15, instant-Damage 0) | every magnitude+duration matches the doc exactly | CONFIRMED |
| 20 | REQ_Alch_ pool FormIDs: FortifyHealth 03EAF3, DamageHealth 03EB42, ResistPoison 090041, Dispel AD3A55, SoulTrap 20B30F | all resolve exact | CONFIRMED |
| 21 | Edibles get VendorItemFood **0A0E55** / VendorItemFoodRaw 0A0E56 instead of ingredient tag | 0A0E56=VendorItemFoodRaw ✓; Wheat carries only 0A0E56 ✓. But **0A0E55 = GiftUniversallyValuable, NOT VendorItemFood** (real VendorItemFood = 08CDEA) | **WRONG (FormID)** |
| 22 | Requiem-defined ingredients: HorkerFat 034E09, MammothHeart 043661, trophy fish 0972E6 (V500/IngrV15) | REQ_Ingredient_HorkerFat / MammothHeart / Cleese_CritterPondFish | CONFIRMED |
| 23 | Food shape: NoAutoCalc,FoodItem; one hunger size + one Nutrition + themed fortifies | BeefRaw/CookedBeef/BeefStew all NoAutoCalc,FoodItem; kits match | CONFIRMED |
| 24 | Raw meat carries Paralysis; cooking removes it + upgrades NutritionSM→HS | BeefRaw: Paralysis(000808)+FortHealth+FortHealthRegen+Large+NutrSM(000A36). CookedBeef: FortHealth+FortMelee+Large+NutrHS(00025E), NO paralysis | CONFIRMED |
| 25 | Alcohol skeleton: FortifyHealth 18ED7C:Requiem + DamageRegeneration AE3729:Requiem + HungerSmall + AlcoholNutrition 000A37:FaB + anim marker | Ale 034C5E effects match exactly (5 effects incl. Taberu marker) | CONFIRMED |
| 26 | Alcohol Moderate tier: FortHealth 45/450, Inebriation 15/600, Nutr 10; Ale V10-15/W0.75 | Ale live: 45/450, 15/600, nutr 10, V10 W0.75 | CONFIRMED |
| 27 | Alcohol tiers Strong (BlackBriarMead 02C35A V35-45) / Extreme (Firebrand 01895F V90-180) | live V35 / V180, both NoAutoCalc,FoodItem, Apo_DPF_Alcohol 000C51 + LoreBox race kw | CONFIRMED |
| 28 | COBJ: cookpot station (0A5CB3), tiering raises CreatedObjectCount, oil recipe REQ_Smelting_Oil_Fire2 000830 → REQ_Oil_Fire ×9, Alchemy 50-100 band | live: WorkbenchKeyword=CraftingCookpot, CreatedObject=000807, Count=9, Conditions HasPerk + AV>=50 + AV<100 | CONFIRMED |
| 29 | Systemic: fAlchemySkillFactor 10C4D9 (AR wins), AVAlchemy 000456, AlchemicalLore1/2 perks 0BE127/0C07CA | all resolve; Lore perks are Perk type, GMST winner=AR | CONFIRMED |
| 30 | Cuisine abilities AE3B70-73 on Requiem.esp; BosmerExclusion kw 000A33:FaB | AE3B70=REQ_Trait_Cuisine_Argonian (Spell), 000A33=LoreBox_REQ_BosmerExclusion | CONFIRMED |

## Live conventions the references MISS

1. **FaB touches 52 Flora (FLOR) records** — all overrides, re-pointing harvestable food-plants to
   their FaB produce. `food-drink-kits.md` never mentions FLOR; a mod adding harvestable food plants
   would need FLOR handling the doc doesn't route.
2. **AR defines/overrides a thrown delivery form** the potion/crafting docs don't cover: Scroll (4;
   `REQ_Powder_Silver 000805:AR` is a defined Scroll), Projectile (3), ArtObject (4), ObjectEffect/
   ENCH (7 overrides), Book (2), Spell (1). Oils/powders are treated purely as ALCH in
   potion-poison-ladders.md; the Scroll+Projectile "thrown coating/powder" surface is unmentioned.
3. **FaB edits 8 Race records** (for cuisine/stomach traits). food-drink-kits.md correctly tells the
   *skill* not to edit races, but does not note that FaB itself carries these edits (relevant if a
   race-food interaction needs tracing).
4. The COBJ **OR-with-CreatePotionsUnperked** gate the crafting doc generalizes was **not present**
   on the sampled oil recipe (REQ_Smelting_Oil_Fire2 000830): its perk Condition[0] has Flags=0 (no
   OR to a HasKeyword). The OR pattern may be potion-COBJ-specific, not universal to craft-consumables.

## Notes on the one WRONG

`ingredients.md` line ~59: "Edibles get `VendorItemFood 0A0E55`". Live: **0A0E55 = GiftUniversallyValuable**;
the real VendorItemFood keyword is **08CDEA** (confirmed, and used correctly throughout food-drink-kits.md).
VendorItemFoodRaw 0A0E56 is correct. A patcher following the ingredients doc would stamp the wrong
keyword. The example items (Wheat) actually carry 0A0E56 (VendorItemFoodRaw) only — so the *behavior*
described is right, just the FormID token for the cooked-food variant is wrong.
