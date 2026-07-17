# Food & Drink Kits (Requiem - Food and Beverages Redone + the live patch layers)

Mined live from load-order winners (2026-07-15). On a curated Requiem load order most food/drink
winners are **hand-authored layers stacked above FaB** (FaB won only 59 of its 196 ALCH touches
here) — that is normal; derive from the winner. FormIDs are wire tokens; bare `:FaB` means
`:Requiem - Food and Beverages Redone.esp`.

## Universal food shape

- Flags `NoAutoCalc, FoodItem` (187/196 of the corpus). Exceptions: drugs + holy water =
  `NoAutoCalc` only (survival layer treats them as potions); the two CC poison apples add `Poison`.
- Exactly **one hunger-size effect** — `Survival_FoodRestoreHunger{VerySmall|Small|Medium|Large}`
  (`002EE1/002EE2/002EE3/002EE4:Update.esm`, all scalars 0 — the size IS the MGEF identity).
  Hunger size tracks **meal bulk**, not stat power.
- Exactly **one Nutrition effect** — `REQ_Apo_FoodNutritionSM 000A36:FaB` (raw/light),
  `REQ_Apo_FoodNutritionHS 00025E:FaB` (cooked/rich), or `REQ_Apo_AlcoholNutrition 000A37:FaB`
  (drinks). Nutrition magnitude scales 5 (raw) → 10 (normal) → 15 (soup/milk) → 25 (stew).
- 1–2 **themed** fortify/regen effects (table below) — buff-over-time, never instant restore
  (sweets' instant restore is the lone vanilla-style exception in doctrine).
- Keyword `VendorItemFood 08CDEA:Skyrim.esm` (+ racial `LoreBox_*` tags where the fiction calls);
  the mechanics live in the **effect list**, not in a tier keyword — contrast weapons/armor.
- Preserve a trailing **eating-animation marker** effect (magnitude 0, from a Taberu-style
  animation patch) when the mod's stack ships one.

## The FaB effect library (`REQ_Apo_*`, all `:FaB`)

Fortify: Health `000A12` · Stamina `000A13` · Magicka `000A14` · CarryWeight `00080C` ·
MoveSpeed `000910` · Marksman `000A2D` · Melee `000A39` · ResistMagic `000A3A` · Unarmed
`000B48` · Speech `000B4A` · Jump `000C4B` · Sneak `000C7D`. Regen: StaminaRegen `000001` ·
MagickaRegen `000002` · HealthRegen `00080D`. Restore-per-second: Stamina `000A38` · Magicka
`000B3C` · Health `000B47`. ResistPoison `000C7E`. Food poisoning: Paralysis `000808` ·
ParalysisExemptKhajiit `000C4D`. ResistDisease rides `REQ_Food_ResistDisease AE371D:Requiem.esp`.

All fortify/regen effects are PeakValueModifier (Unarmed: ValueModifier), BaseCost ~0.0001,
`Recover, NoArea`; every PeakValueMod fortify/regen keys `Archetype.AssociationKey =
Apo_NosStack 000802:FaB` — the **anti-stack** association. A new food effect missing that key
stacks with everything (route new-effect design to `requiem-magic-patching` with this constraint).

## Family kits (live-winner worked examples, mag/dur)

| Family | Kit | Example |
|---|---|---|
| Raw meat | Paralysis(1) + themed fortify/regen + NutritionSM(5) + Large hunger | BeefRaw `065C99`: Paralysis 1/15 · FortHealth 15/900 · FortHealthRegen 5/900 · NutrSM 5 |
| Raw fish | ParalysisExemptKhajiit(1) + restore/regen + NutritionSM(5) + Medium | SalmonRaw `065C9F`: RestoreHealth/s 1/300 · FortStamRegen 10/900 |
| Cooked meat | themed fortifies, bigger/longer, **no paralysis**, NutritionHS(10) + Large | CookedBeef `0721E8`: FortHealth 30/1800 · FortMelee 7.5/1800 |
| Bread/baked | FortStamina 15/600 + FortStamRegen 10/600 + NutritionSM(10) + Medium | Bread `065C97` (identical across breads) |
| Cheese | Stamina + Magicka mix + NutritionSM(10) + VerySmall | EidarWedge `064B32`: RestStam/s 1/1200 · FortStamRegen 5/600 · RestMag/s 1/1200 · FortMagRegen 4/600 |
| Fruit/veg | ResistDisease 30–40/900 + FortHealthRegen 5/300 + Small/VerySmall | RedApple `064B2E` V2/W0.4 |
| Vegan soup | ResistDisease 80/1800 + FortHealth 50/1800 + FortStamina 50/1800 + NutritionHS(15) + Large | CabbageApple `0EBA01` V35/W1 |
| Meat stew | FortHealth 50/3600 + RestHealth/s 1/3600 + FortMelee + NutritionHS(25) + Large | BeefStew `0F4314` V30/W1.5 |
| Treat/sweet | small fortify/regen + Speech + NutritionHS(10) + Small | SweetRoll `064B3D` V7/W0.5 |
| Milk/beverage | IngestibleBonus + RestMag/s + NutritionSM(15) + VerySmall | Milk `003534` V4/W2 |

**Raw vs cooked — the paralysis rule.** Raw meat/fish carry a Paralysis-archetype effect (mag 1)
as the food-poisoning risk (`000C4D` variant for what Khajiit can stomach); **cooking removes it
entirely**, upgrades Nutrition SM→HS, and lengthens/strengthens the buffs. Paralysis is the ONLY
negative on food — no negative-magnitude fortifies exist.

## Value / weight norms (live winners)

Fruit/veg V2 W0.4–1 · treats V6–7 · cheese wedge V12, wheel V24–36 W3 (a wheel is a
cut-into-wedges container, not directly eaten) · bread V7–10 W0.5, slice V4 W0.35 · raw meat V15
(large game W3, medium W1.5 — FaB's own five new raw meats confirm the norm) · cooked meat
V12–15 W1 · vegan soup V27–35 W1 · meat stew V20–30 W1–1.5 · survival "hot" soups V26–50 ·
cooked fish V2–40. Weight normalization is hand-tuned per record (3.5→1, 6→3 — no single
multiplier); match a family peer.

## Alcohol & drugs

**Alcohol skeleton** (buffs point at *Requiem.esp*, not FaB): `REQ_Alcohol_FortifyHealth
18ED7C:Requiem.esp` + `REQ_Alcohol_DamageRegeneration AE3729:Requiem.esp` ("Inebriation") +
`Survival_FoodRestoreHungerSmall 002EE2:Update.esm` + `REQ_Apo_AlcoholNutrition 000A37:FaB`
(dur 180) + optional animation marker. Tiers (buff mag/dur · debuff mag/dur · nutrition · V/W):

| Tier | FortHealth | Inebriation | Nutr | V/W | Example |
|---|---|---|---|---|---|
| Moderate | 45/450 | 15/600 | 10 | 10–15 / 0.75 | Ale `034C5E` |
| Strong | 90/900 | 30/1350 | 15 | 35–45 / 0.75 | Black-Briar Mead `02C35A` |
| Extreme | 120/1350 | 45/1800 | 30 | 90–180 / 1 | Firebrand Wine `01895F` (+ResistFrost 20/600) |

Extreme-tier drinks add a flavor elemental resist (`03EAEA`/`03EAEB`). Keywords:
`Apo_DPF_Alcohol 000C51:FaB` + the brewing/tolerating race's `LoreBox_REQ_Alcohol_<Race>`
(`000A20–000A29:FaB`). **No ALCH addiction field exists** — tolerance/withdrawal is the script
MGEF `REQ_Apo_AlcoholTolerance 00025C:FaB`; the acute penalty is Inebriation. Legacy
`REQ_Drink[N]_*` names carry identical economics to `REQ_Alcohol_<Strength>_*`.

**Drugs** (`NoAutoCalc` only, no FoodItem, no hunger/nutrition): strong short combat buffs +
longer withdrawal drains — Skooma `057A7A`: Fortify One/TwoHanded 50 + Stamina 500 (dur 120) then
Drain Magicka/Stamina (dur 500). Values 175–300 (BalmoraBlue V1500 is a flagged outlier).

## Racial cuisine & the Green Pact

The gate is a **keyword on the food**, read by the race's `REQ_Trait_Cuisine_<Race>` ability
(Requiem.esp `AE3B70–AE3B73`) — never a race-record edit from this skill. (FaB itself carries 8
RACE overrides wiring those cuisine/stomach traits race-side — that side is already built; the
keyword on the food is your only lever, and a mod's new playable race needing the trait side
routes to `requiem-race-patching`.) FaB's `LoreBox_*`
taxonomy (all `:FaB`): stomach gates `REQ_BeastStomach 000A2F` / `REQ_FelineStomach 000A35` /
`REQ_OrcPoisonResistance 000A34`; bonus tags `REQ_ArcheryBonus 000A30` (+Bosmer variant
`000B3D`), `REQ_MeleeBonus 000A31` (+Nord `000B3E`), `REQ_MageBonus 000A3B`; named-dish gates
(StrangeMeat `000B40`, BretonCheese `000B3F`, CheeseWheel `000B44`, Khajiit&ArgonianTreat
`000B46`, WrothgarTartare `000C50`, Brew `000C7C`); and **`LoreBox_REQ_BosmerExclusion 000A33`**
— tag every earthborn/plant food with it so the Green Pact system can see it (a Bosmer eating a
tagged food trips a watcher spell → 90-minute food-benefit suppression). Mechanical keywords:
`Apo_NosStack 000802` (anti-stack key), `Apo_StrongStomach 000B45` (on the special-diet races),
`Apo_DPF_Alcohol 000C51`, `Apo_IngestibleBonus 000809`.

## Known pattern-breaks (flag, don't smooth)

- **Dual naming schemes coexist**: `Meat_<X>Raw` vs `Meat_Raw_<X>`; `Cooked_<X>` vs
  `Meat_Cooked_<X>`; `REQ_Alcohol_<Strength>_*` vs legacy `REQ_Drink[N]_*`; some fully-tuned
  records never renamed at all (bare `Skooma`, two Survival hot foods). Search by family token.
- The dietary EditorID re-taxonomy (`Vegan_`/`MeatVegan_`) is not uniformly applied.
- `REQ_NULL_SalmonCooked02 003541:Skyrim.esm` — a nulled duplicate in the food corpus. Never
  derive from it.
- `SoulHuskExtract 015A1E` ships `Flags = 0` (neither NoAutoCalc nor FoodItem) with no
  hunger/nutrition — an anomalous potion-like record, not a food template.
- `HolyWater 0A9661` uses a legacy nutrition MGEF; an older-kit record.
- `REQ_Apo_AlcoholNutrition` targets UnarmedDamage (odd vs the other Nutrition effects) — copy
  the link, don't "fix" it.
