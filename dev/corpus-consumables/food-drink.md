# FaB Food & Drink System — Live-Winner Corpus

*Source: `Requiem - Food and Beverages Redone.esp` (FaB) as refined by downstream Authoria patches,
read live through houseCARL against the Requiem MO2 instance. All values are the **live load-order
winner** unless stated. FormIDs are verbatim wire tokens `XXXXXX:Plugin.esp`. READ-ONLY mining pass —
nothing was written. Mined 2026-07-15 for the consumables skill.*

> **Doctrine reminder for the skill:** derive a new food/drink from the LIVE winner of its closest
> comparable (same family + prep state + meal size), *whatever plugin wins* — for most cooked/soup/
> alcohol records that winner is an **Authoria patch downstream of FaB** (ATweaks, Alcohol Tweaks,
> Kanjs Soups, CC Fishing Patches), and that IS the corpus. Never copy Reqtificator output; carry the
> inputs (effects, keywords, value, weight, flags) an ALCH override needs.

---

## 0. Accounting (what FaB defines / touches)

| Type | Defined in FaB | Touched by FaB | Notes |
|---|---|---|---|
| KYWD | **29** | 29 | `LoreBox_*` racial taxonomy + `Apo_*` stomach/stack keywords |
| MGEF | **34** | 37 | 34 defined; 3 of the 34 win from Authoria overrides (see §3) |
| SPEL | **2** | 3 | FaB defines the 2 Bosmer "Green Pact Oath" spells; a 3rd is touched-not-defined |
| ALCH | **7** | **196** | FaB defines 7 new food records; touches 196 total (the food/drink universe) |
| RACE | 0 defined | **8** | ArgonianRace, KhajiitRace, OrcRace, WoodElfRace + 4 vampire variants (winners = Authoria) |
| FLOR/LVLI/GlobalFloat | — | 52 / 20 / 4 | Flora harvest sources, leveled lists, tuning globals (not deep-mined here) |

**196 ALCH winner distribution** (sums to 196, full file read):

| Winner plugin | Count |
|---|---|
| Requiem - Food and Beverages Redone.esp | 56 |
| Authoria - ATweaks.esp | 50 |
| Authoria - CC Fishing Patches.esp | 34 |
| Authoria - Patch - Alcohol Tweaks.esp | 32 |
| Authoria - Master Patch - Food and Drink.esp | 16 |
| Authoria - Reqtificated - LOTD.esp | 3 |
| Requiem - Food and Beverages Redone - Biggie Traits.esp | 3 |
| Authoria - Patch - Kanjs Soups.esp | 2 |

FaB (base + Biggie Traits) wins only **59/196**; **137/196 win from Authoria layers**. The live
authority for most cooked meat, all soups/stews, all alcohol, and all CC/DLC fish is an Authoria patch.

---

## 1. Keyword taxonomy (29, all defined in FaB)

Two families: **`Apo_*`** = mechanical (stacking / stomach / DPF) keywords; **`LoreBox_*`** = the
racial-cuisine gate carried **on the food ALCH record** (the eater's race ability reads them). The
LoreBox keywords are NOT on race records — they tag the *food*, and the racial cuisine ability decides
how that race digests it.

| FormID (`:…FaB.esp`) | EditorID | Meaning / role |
|---|---|---|
| 000802 | `Apo_NosStack` | Anti-stack association key — every PeakValueMod fortify/regen MGEF hangs its `Archetype.AssociationKey` on this so food buffs don't stack |
| 000809 | `Apo_IngestibleBonus` | Marks the milk/holy-water "ingestible bonus" line |
| 000B45 | `Apo_StrongStomach` | Carried by all 4 special-diet races (Argonian/Khajiit/Orc/Bosmer) — the "strong stomach" gate |
| 000C51 | `Apo_DPF_Alcohol` | Alcohol tag for Requiem's Diverse Priests/DPF-style distribution; on every alcohol record |
| 000A20–000A29 | `LoreBox_REQ_Alcohol_<Race>` | Per-race alcohol affinity: Imperials, Imperials&Osimer, Nords, Nords&Osimer, Dunmer, Dunmer&Osimer, Argonians, Argonians&Osimer, Khajiit, Osimer — an ale carries the token(s) for the race(s) that brew/tolerate it |
| 000A2F | `LoreBox_REQ_BeastStomach` | Argonian digestion gate |
| 000A35 | `LoreBox_REQ_FelineStomach` | Khajiit digestion gate |
| 000A34 | `LoreBox_REQ_OrcPoisonResistance` | Orc poison-resist food gate |
| 000A33 | `LoreBox_REQ_BosmerExclusion` | **Green Pact** gate — tags an *earthborn/plant* food that a Bosmer may NOT eat (present e.g. on Cabbage Soup) |
| 000A30 / 000B3D | `LoreBox_REQ_ArcheryBonus` / `…ArcheryBonusBosmer` | Food that fortifies Marksman (Bosmer-specific variant) |
| 000A31 / 000B3E | `LoreBox_REQ_MeleeBonus` / `…MeleeBonusNord` | Food that fortifies melee (Nord-specific variant) |
| 000A3B | `LoreBox_REQ_MageBonus` | Food that fortifies magicka/mage skills |
| 000A34…000B46 | `LoreBox_REQ_StrangeMeat` (000B40), `…BretonCheese` (000B3F), `…CheeseWheel` (000B44), `…Khajiit&ArgonianTreat` (000B46), `…WrothgarTartare` (000C50), `…Brew` (000C7C) | Named dish/treat gates tying a specific food to a specific racial bonus |

**Reverse-lookup convention:** to see who carries a keyword, `cross_plugin_query references=["<kw formid>"] type="ALCH"`. Confirmed live: `LoreBox_REQ_BosmerExclusion` (000A33) sits on
Cabbage Soup `0EBA02` alongside `VendorItemFood`; `LoreBox_REQ_CheeseWheel` (000B44) on Goat Cheese
Wheel `064B33`; `LoreBox_REQ_Alcohol_Osimer` (000A29) on Nord Ale `034C5E`.

---

## 2. Value / Weight norms (live winners, per family)

| Family | Modal Value | Value range | Modal Weight | Notable outliers |
|---|---|---|---|---|
| Fruit | 2 | 2 | 0.4 | Gourd W1 |
| Veggie (raw) | 2 | 2–3 | 0.5 | LeekGrilled V3/W0.1; Cabbage W1 |
| Treat / sweets | 6–7 | 6–7 | 0.25–0.5 | — |
| Cheese wedge/slice | 12 | 6–18 | 0.4–0.5 | Wheels V24–36 **W3**; MammothBowl V25/W1.5 |
| Baked (bread/pie) | 10 | 4–15 | 0.5 | ApplePie V15/W1.5; Potatoes W0.1; BreadSlice V4/W0.2 |
| Meat (raw) | 15 | 2–30 | 1.5 | MammothSnout **V30/W5**; AshHopper/Mudcrab V2; Clam W0.35 |
| Meat (cooked) | 12–15 | 4–35 | 1 | MammothSteak V35/W3.5; SkeeverMeat V4; Mudcrab V2/W0.1 |
| Vegan Soup | 27 | 27–35 | **1** | +apple/+potato = 35 |
| MeatVegan stew | 20/30 | 20–30 | 1 (stews 1.5) | — |
| FlourVegan (crostata) | **14** | 14 | 0.5 | uniform |
| Fish (cooked) | — | 2–40 | 0.2 (CC) / 1 (ATweaks) | ScorpionFish/Angelfish V40 |
| Fish (raw) | — | 1–30 | 0.5–1 | BrookBass raw V1 |
| Survival "hot" soup/stew | 35 | 26–50 | 1 | ElsweyrFondue Hot V50 |
| Alcohol Moderate | 10 | 10–15 | **0.75** | — |
| Alcohol Strong | 45 | 15–45 | 0.75 | NordMead V15 (low) |
| Alcohol Extreme | 90 | 90–180 | 1 | Firebrand/Surilie V180 |
| Drink[N] (legacy naming) | by tier | 10–180 | 0.75/1 | same economics as Alcohol_<Strength> |
| Beverage (milk/holy) | 4–5 | 4–5 | 0.75–2 | Milk W2 |
| Drug (skooma etc.) | — | 175–300 | 1 | **BalmoraBlue V1500** |

**Flags convention (of 196):** `NoAutoCalc, FoodItem` = **187** (the default for anything edible).
`NoAutoCalc` only (no FoodItem, so Requiem's survival layer treats it as a *potion*, not food) = **6**:
the 4 `REQ_Drug_*` + bare `Skooma 057A7A` + `REQ_Beverage_HolyWater 0A9661`. `NoAutoCalc, FoodItem,
Poison` = **2** (the CC Curios poison apples `000849`/`000D98`). `Flags = 0` = **1** anomaly
(`SoulHuskExtract 015A1E`, see §8). **No Medicine flag anywhere.**

---

## 3. MGEF reference — FaB's food/drink effect library (34)

All EditorIDs prefixed `REQ_Apo_`; all FormIDs `:Requiem - Food and Beverages Redone.esp`. MagicSkill
and ResistValue are `None` on all 34. Every PeakValueMod fortify/regen effect keys its
`Archetype.AssociationKey` to `Apo_NosStack` (000802) for anti-stacking.

| FormID | EditorID (sans `REQ_Apo_`) | Name | Archetype | Target AV | BaseCost | CastType | Flags |
|---|---|---|---|---|---|---|---|
| 000001 | FoodFortifyStaminaRegen | Fortify Stamina Regen | PeakValueMod | StaminaRateMult | 0.0001 | FireAndForget | Recover, NoArea, PowerAffectsMagnitude |
| 000002 | FoodFortifyMagickaRegen | Fortify Magicka Regen | PeakValueMod | MagickaRateMult | 0.0001 | FAF | Recover, NoArea, PowerAffectsMagnitude |
| 00080D | FoodFortifyHealthRegen | Fortify Health Regen | PeakValueMod | HealRateMult | 0.0001 | FAF | Recover, NoArea, PowerAffectsMagnitude |
| 000A12 | FoodFortifyHealth | Fortify Health | PeakValueMod | Health | 0.0001 | FAF | Recover, NoArea |
| 000A13 | FoodFortifyStamina | Fortify Stamina | PeakValueMod | Stamina | 0.0001 | FAF | Recover, NoArea |
| 000A14 | FoodFortifyHMagicka | Fortify Magicka | PeakValueMod | Magicka | 0.0001 | FAF | Recover, NoArea |
| 00080C | FoodFortifyCarryWeight | Fortify Carry Weight | PeakValueMod | CarryWeight | 0.0001 | FAF | Recover, NoArea |
| 000910 | FoodFortifyMovementSpeed | Fortify Move Speed | PeakValueMod | SpeedMult | 0.0001 | FAF | Recover, NoArea |
| 000A2D | FoodFortifyMarksman | Fortify Marksman | PeakValueMod | MarksmanPowerModifier | 0.0001 | FAF | Recover, NoArea |
| 000A39 | FoodFortifyMelee | Fortify Melee | PeakValueMod | OneHandedModifier | 0.0001 | FAF | Recover, NoArea |
| 000A3A | FoodFortifyResistMagic | Fortify Resist Magic | PeakValueMod | ResistMagic | 0.0001 | FAF | Recover, NoArea |
| 000B48 | FoodFortifyUnarmed | Fortify Unarmed | ValueMod | UnarmedDamage | 0.0001 | FAF | Recover, NoArea |
| 000B4A | FoodFortifySpeech | Fortify Speech | PeakValueMod | Speech | 0.0001 | FAF | Recover, NoArea |
| 000C4B | FoodFortifyJump | Fortify Jump | PeakValueMod | JumpingBonus | 0.0001 | FAF | Recover, NoArea |
| 000C7D | FoodFortifySneak | Fortify Sneak | DualValueMod | SneakingPowerModifier | 0.025 | FAF | Recover, NoArea, PowerAffectsMagnitude (+VMA script) |
| 000C7E | FoodResistPoison | Resist Poison | PeakValueMod | PoisonResist | 0.025 | FAF | Recover, FXPersist, PowerAffectsMagnitude |
| 000A38 | FoodRestoreStaminaPerSecond | Restore Stamina | PeakValueMod | Stamina | 0 | FAF | NoArea |
| 000B3C | FoodRestoreMagickaPerSecond | Restore Magicka | PeakValueMod | Magicka | 0 | FAF | NoArea |
| 000B47 | FoodRestoreHealthPerSecond | Restore Health | PeakValueMod | Health | 0 | FAF | NoArea |
| 00025E | FoodNutritionHS | Nutrition | DualValueMod | Health | 0 | FAF | NoDuration, NoArea |
| 000A36 | FoodNutritionSM | Nutrition | DualValueMod | Magicka | 0 | FAF | NoDuration, NoArea |
| 000A37 | AlcoholNutrition | Nutrition | DualValueMod | UnarmedDamage | 0 | FAF | Recover, NoArea |
| 000B43 | GreenPact_FoodNutrition | Nutrition | DualValueMod | Magicka | 0 | FAF | NoDuration, NoArea |
| 000808 | FoodParalysisEffect | Paralysis | Paralysis | Paralysis | 500 | FAF | **Hostile**, Recover, NoMagnitude, NoArea |
| 000C4D | FoodParalysisExemptKhajiitEffect | Paralysis | Paralysis | Paralysis | 500 | FAF | Hostile, Recover, NoMagnitude, NoArea |
| 000C4F | FoodWrothgarTartareParalysis | Paralysis | Paralysis | Paralysis | 0 | FAF | Hostile, Recover, NoMagnitude, NoArea |
| 000C4E | FoodWrothgarTartareFortifyHealth | Fortify Health | PeakValueMod | Health | 0 | FAF | Recover, NoArea |
| 000911 | FoodDamageAttributes | Damage Attributes | PeakValueMod | StaminaRateMult | 0 | FAF | Recover, NoArea |
| 00025C | AlcoholTolerance | Alcohol Tolerance | **Script** | — | 0 | ConstantEffect | Recover, NoHitEvent, HideInUI, Painless, NoHitEffect |
| 00080A | FoodIngestibleBonusEffect | Ingestible Bonus | Script (no VMA) | — | 0 | FAF | 0 |
| 000942 | BosmerExclusionNegEffect | Green Pact Oath Neg | Script | — | 0 | FAF | Recover, NoMagnitude, NoArea, HideInUI |
| 000943 | BosmerExclusionEffect | Green Pact Oath | Script | — | 0 | FAF | Recover, NoDuration, NoMagnitude, NoArea |
| 000D57 | Food_AddLeftOver_GarlicBread | Leftover Food | Script (+VMA) | — | 0 | FAF | NoDuration, NoMagnitude, NoArea, HideInUI |
| 000D5A | Food_AddLeftOver_BraidedBread | Leftover Food | Script (+VMA) | — | 0 | FAF | NoDuration, NoMagnitude, NoArea, HideInUI |

**Effect classes:**
- **Fortify (skill/attribute):** `PeakValueModifier` (or `ValueModifier` for Unarmed 000B48), BaseCost
  0.0001, `Recover, NoArea`. The magnitude is set per-ALCH, not on the MGEF.
- **Regen (rate):** the four `…RateMult`/Sneak effects add `PowerAffectsMagnitude` + the `Apo_NosStack` key.
- **Restore-per-second:** 000A38/000B3C/000B47 — PeakValueMod on the pool, BaseCost 0, `NoArea` only.
- **Nutrition (the "this counts as eating" tag):** all `DualValueModifier`, BaseCost 0, `NoDuration,
  NoArea`. `…SM` (000A36) for light/raw food, `…HS` (00025E) for cooked/rich food, `AlcoholNutrition`
  (000A37) for drinks. Magnitude scales 5(raw) → 10(normal) → 15(soup/milk) → 25(stew).
- **Illness / negative:** the three `Paralysis`-archetype effects (raw-food poisoning; see §5) — these
  are the ONLY negatives (no negative-magnitude Fortify anywhere). `FoodDamageAttributes` 000911 is a
  nominal "damage" stub.
- **Racial:** `AlcoholTolerance` (script, constant), `FoodParalysisExemptKhajiit`, `GreenPact_*`, the
  `WrothgarTartare` pair (Orsimer dish).
- **Script leftover-spawners:** `Food_AddLeftOver_GarlicBread/BraidedBread` (real VMA scripts) turn a
  large item into a smaller "leftover" on eat.

**3 of the 34 win from Authoria overrides** (derive from the winner, not FaB's base):
`FoodDamageAttributes 000911` → `Authoria - Patch - Torn Flesh Tweaks.esp`; `GreenPact_FoodNutrition
000B43` → same; `BosmerExclusionNegEffect 000942` → `Authoria - Patch - Green Pact.esp` (script swapped
to `AuthoriaGreenPactWipe`).

---

## 4. Effect-kit per family (live winners, magnitude / duration seconds)

**Hunger-size MGEFs** (all `:Update.esm`, all scalars 0 — pure size tags; the size lives in the MGEF
identity): `002EE1` VerySmall · `002EE2` Small · `002EE3` Medium · `002EE4` Large.

**Standard food kit shape:** `[optional raw-only Paralysis] + 1–2 themed Fortify/regen + optional
restore-per-second + exactly one hunger-size tag + one Nutrition tag`.

| Family (record) | V/W | Effect kit (mag/dur) | Hunger |
|---|---|---|---|
| **Raw meat** `065C99` BeefRaw | 12/1.5 | Paralysis(1/15) · FortHealth(15/900) · FortHealthRegen(5/900) · NutritionSM(5) | Large |
| **Raw meat** `0669A2` VenisonRaw | 4/1.5 | Paralysis(1/0) · FortHealthRegen(10/300) · FortCarryWeight(15/900) · NutritionSM(5) | Large |
| **Raw fish** `065C9F` SalmonRaw | 9/1 | **ParalysisExemptKhajiit**(1/0) · RestoreHealthPerSec(1/300) · FortStamRegen(10/900) · NutritionSM(5) | Medium |
| **Cooked meat** `0721E8` CookedBeef | 15/1 | FortHealth(**30**/1800) · FortMelee(7.5/1800) · **NutritionHS**(10) | Large |
| **Cooked meat** `0722BB` MammothSteak | 35/3.5 | FortHealth(25/300) · FortHealthRegen(5/600) · FortCarryWeight(25/600) · NutritionHS(10) | Large |
| **Cooked fish** `00087A` Cod | 18/1 | FortStamRegen(10/600) · NutritionHS(10) · +Taberu anim ME | Medium |
| **Bread/baked** `065C97` Bread | 7/0.5 | FortStamina(15/600) · FortStamRegen(10/600) · AddLeftOver_Bread · NutritionSM(10) | Medium |
| **Bread** `0009DC` GarlicBread | 10/0.5 | FortStamina(15/600) · FortStamRegen(10/600) · AddLeftOver_GarlicBread · NutritionSM(10) · **CureDisease(1)** | Medium |
| **Cheese** `064B32` EidarWedge | 12/0.4 | RestStamPerSec(1/1200) · FortStamRegen(5/600) · RestMagPerSec(1/1200) · FortMagRegen(4/600) · NutritionSM(10) | VerySmall |
| **Cheese wheel** `064B33` GoatWheel | 24/3 | **only** AddLeftOver_GoatWheel (a cut-into-wedges container, NOT eaten directly — no kit) | — |
| **Fruit** `064B2E` RedApple | 2/0.4 | **ResistDisease(30/900)** · FortHealthRegen(5/300) · NutritionSM(10) | Small |
| **Veggie** `064B3F` Cabbage | 2/1 | ResistDisease(30/900) · FortHealthRegen(5/300) · NutritionHS(10) | VerySmall |
| **Veggie** `0669A5` LeekRaw | 2/0.5 | ResistDisease(**40**/900) · FortHealthRegen(5/300) · NutritionHS(10) | VerySmall |
| **Vegan soup** `0EBA01` CabbageApple | 35/1 | ResistDisease(80/1800) · FortHealth(50/1800) · FortStamina(50/1800) · NutritionHS(15) | Large |
| **Meat stew** `0F4314` BeefStew | 30/1.5 | FortHealth(50/3600) · RestHealthPerSec(1/3600) · FortMelee(25/3600) · FortMelee(10/3600) · NutritionHS(25) | Large |
| **Treat** `064B3D` SweetRoll | 7/0.5 | FortStamRegen(5/600) · FortSpeech(10/600) · NutritionHS(10) | Small |
| **Milk** `003534` | 4/2 | IngestibleBonus(15/3600) · RestMagPerSec(1/3600) · NutritionSM(15) | VerySmall |
| **Special** `014DC4` SoulHusk | 185/0.25 | SoulHuskEffect(1/30) · FortResistMagic(10/300) — **no hunger, no nutrition** | — |

Themes are consistent: **meat → Health / HealthRegen / CarryWeight / Melee** (strength); **bread/grain
→ Stamina + StamRegen** (both mag 15/10 dur 600, identical across breads); **cheese → mixed Stamina +
Magicka** (mage food); **fruit/veg → ResistDisease(30, leek 40) + HealthRegen**; **soup/stew → big
FortHealth/Stamina + long durations (1800–3600s) + high Nutrition (15–25)**.

---

## 5. Raw vs cooked (the food-poisoning rule)

- **Raw meat/fish carry a `Paralysis`-archetype effect (mag 1) as the food-poisoning risk; cooking
  removes it entirely.** Raw beef/venison lead with `REQ_Apo_FoodParalysisEffect 000808`; raw salmon
  uses the Khajiit-exempt variant `REQ_Apo_FoodParalysisExemptKhajiitEffect 000C4D` (mag 1). Cooked
  Beef, Mammoth Steak, and Cooked Cod carry **no** paralysis effect.
- Paralysis is the **only** negative — there are no negative-magnitude fortify effects on any food.
- Cooking also **upgrades the Nutrition tier**: raw = `FoodNutritionSM` mag 5 → cooked/stew/soup =
  `FoodNutritionHS` mag 10–25, and cooked buffs get bigger/longer (e.g. FortHealth 15/900 raw beef →
  30/1800 cooked beef).
- **Khajiit exemption:** raw fish/meat that Khajiit can stomach uses the `…ExemptKhajiit` paralysis
  variant so the Feline Stomach race is spared the proc.

---

## 6. Alcohol & drug rules

**Uniform 4–5 effect alcohol skeleton** (buffs point at *Requiem.esp*'s alcohol MGEFs, not FaB's food
MGEFs; the *magnitude* is set per-ALCH by **FaB**, then only display-labeled by Alcohol Tweaks):

- Buff: `REQ_Alcohol_FortifyHealth 18ED7C:Requiem.esp` ("liquid courage" HP).
- Debuff: `REQ_Alcohol_DamageRegeneration AE3729:Requiem.esp`, display name **"Inebriation"** (the
  drunk penalty — damages regen).
- Hunger tag: always `Survival_FoodRestoreHungerSmall 002EE2`.
- Nutrition: `REQ_Apo_AlcoholNutrition 000A37` (dur 180).

**Magnitude scales cleanly by strength tier** (buff + debuff + duration all rise together):

| Tier | Example | FortHealth | Inebriation | AlcNutrition | Bonus | V/W |
|---|---|---|---|---|---|---|
| Moderate | Ale `034C5E` | 45/450 | 15/600 | 10/180 | — | 10/0.75 |
| Strong | Black-Briar Mead `02C35A` | 90/900 | 30/1350 | 15/180 | — | 35/0.75 |
| Extreme | Firebrand Wine `01895F` | 120/1350 | 45/1800 | 30/180 | **ResistFrost 20/600** (`03EAEB`) | 180/1 |
| Extreme | Dragon's Breath Mead `0555E8` | 120/1350 | 45/1800 | 30/180 | **ResistFire 20/600** (`03EAEA`) | 180/1 |

Top-tier drinks add a flavor elemental resist. The legacy `REQ_Drink[N]_*` naming has identical
economics to `REQ_Alcohol_<Strength>_*` — same tiering, older label era.

**Addiction:** there is **no ALCH-level addiction/AddictionChance field** — Skyrim's ALCH record does
not model addiction, and FaB doesn't fake one. The alcohol economy is entirely MGEF + script:
`REQ_Apo_AlcoholTolerance 00025C` (Script archetype, ConstantEffect, HideInUI) is the tolerance/
withdrawal driver, and `Inebriation` is the acute penalty. The `Apo_DPF_Alcohol 000C51` keyword tags
every drink for distribution/tolerance bookkeeping.

**Drugs** (`NoAutoCalc` only — not FoodItem, no hunger, no nutrition — treated as potions):
- **Skooma** `057A7A` (winner Biggie Traits): huge short combat stims (FortOneHanded 50, FortTwoHanded
  50, FortUnarmed 15, FortStamina 500, all dur 120) **+ longer withdrawal drains** (DrainMagicka 10/500,
  DrainStamina 1/500) — buffs 120s, crash 500s.
- **Sleeping Tree Sap** `0AED90`: Slow(30/300) + FortHealth 200/300 + zero-mag Hist-communion/ISM tags.
- **BalmoraBlue** `0DC172` V**1500** (extreme value outlier, 5–10× the next drug).

---

## 7. Racial digestion & the Green Pact system

**The LoreBox stomach/alcohol keywords live on the FOOD, not the race.** Each race carries a
`REQ_Trait_Cuisine_<Race>` ability (defined in *Requiem.esp*, granted per race) + the FaB keyword
`Apo_StrongStomach 000B45`; the cuisine ability reads the food's LoreBox tags to decide digestion.

| Race (winner) | Cuisine ability | Food keyword the race carries |
|---|---|---|
| WoodElfRace `013749` (Authoria - Patch - Green Pact) | `REQ_Trait_Cuisine_Bosmer AE3B71` ("Carnivore: Meat twice as nourishing") **+ Green Pact Oath `000944`** | Apo_StrongStomach |
| KhajiitRace `013745` (Authoria - Master Patch - Beast Races) | `REQ_Trait_Cuisine_Khajiit AE3B72` ("Crystallized Moonlight: Elsweyr Fondue twice as nourishing") | Apo_StrongStomach + IsBeastRace |
| ArgonianRace `013740` (Authoria - Master Patch - Races Merge) | `REQ_Trait_Cuisine_Argonian AE3B70` | Apo_StrongStomach + IsBeastRace |
| OrcRace `013747` (Authoria - Master Patch - Races Merge) | `REQ_Trait_Cuisine_Orc AE3B73` | Apo_StrongStomach |

The `REQ_Trait_Cuisine_*` abilities point at description-only Script/ConstantEffect MGEFs (flavor
markers, e.g. `AE3B75` "Carnivore") — they *advertise* the bonus; the actual scaling resolves against
the food's LoreBox keyword.

**Green Pact (Bosmer) — the two "Green Pact Oath" spells FaB defines:**
- `REQ_Apo_Trait_BosmerExclusionAb 000944` (Ability, ConstantEffect, always on WoodElfRace) → MGEF
  `REQ_Apo_BosmerExclusionEffect 000943` (Script, player-only via `GetIsID Player`). The persistent
  watcher. Description: *"Green Pact: You are forbidden from harming nature and consuming earthborn
  foods. Violating the Green Pact disables food benefits for 90 minutes."*
- `REQ_Apo_BosmerExclusionSpell 000941` (the penalty, cast on violation) → MGEF
  `REQ_Apo_BosmerExclusionNegEffect 000942` (Duration **5400s = 90 min**, `PerkToApply →
  REQ_Apo_BosmerExclusionPerk 00093E`, which suppresses all food benefits). The live winner of 000942
  is **Authoria - Patch - Green Pact.esp**, which swapped FaB's stock script for `AuthoriaGreenPactWipe`.

Mechanically: a Bosmer eating a food tagged `LoreBox_REQ_BosmerExclusion 000A33` (earthborn/plant food,
e.g. Cabbage Soup) trips the watcher → penalty spell → 90-min food-benefit blackout perk. All
script/perk driven; the ALCH keyword is the gate.

---

## 8. Authoria refinement pattern (FaB → live winner)

**FaB does the heavy Requiem rebalancing** (REQ_ effects, tiered magnitudes, keywords, values, initial
weight/EditorID). Authoria layers sit on top and forward FaB's numbers verbatim except for targeted
refinements:

- **`Authoria - ATweaks.esp` — general food normalizer.** Three consistent operations, nothing else:
  (1) **Weight normalization** to clean values (Cabbage Soup 3.5→1, Cooked Beef 1.75→1, Beef Stew
  3.75→1.5, Goat Cheese 6→3) — it is the *final* weight authority even over an intermediate Kanjs
  layer; (2) **EditorID re-taxonomy** into a dietary scheme (`Vegan_`, `MeatVegan_`, `Cheese_` from
  `Dairy_`, `Fish_`) — a hook for a downstream dietary/KID system; (3) **model/alt-texture cleanup**
  (generic `Stew.nif` → unique BYOH mesh, strip stray `Model.AlternateTextures`). It does **not** retune
  effect magnitudes/durations or add effects.
- **`Authoria - Patch - Alcohol Tweaks.esp` — labeler / animation re-merger.** Only **adds the tier
  label to the Name** (`Ale` → `Ale (Moderate)`, `Black-Briar Mead` → `…(Strong)`) and re-merges the
  Taberu eating-animation ME where one exists. The magnitude tiering is **FaB's**, forwarded intact.
- **`Authoria - Patch - Kanjs Soups.esp` — soup/stew completer.** For records FaB never processed (Clam
  Chowder `00353E`: vanilla V5/W0.5/2-effect → REQ V20/W1/6-effect/keyworded) it does the *entire*
  Requiem conversion; for FaB-processed soups it's an intermediate weight layer ATweaks then normalizes.
- **`Authoria - CC Fishing Patches.esp` — CC/DLC fish completer.** Finishes CC fish FaB left
  half-processed (Cooked Cod `00087A`: FaB kept vanilla editorid + 2 near-vanilla effects → CC patch
  applies `REQ_Food_Fish_Cooked_Cod` + full 4-effect fish kit). Wins 34 records.
- **`Authoria - Master Patch - Food and Drink.esp`** — higher-tier conflict-resolution master (16
  wins); not observed altering any of the 8 sampled records — likely pure forwarding/merge.

---

## 9. FaB's 7 own-defined ALCH

The only genuinely *new* food records FaB introduces (all `NoAutoCalc, FoodItem`; values shown are
FaB's own, override_depth 1):

| FormID (`:…FaB.esp`) | EditorID | Name | V | W |
|---|---|---|---|---|
| 00025D | REQ_Food_Meat_FoxRaw | Fox Meat | 15 | 3 |
| 000C53 | REQ_Food_Meat_BearRaw | Bear Meat | 15 | 3 |
| 000C54 | REQ_Food_Meat_MammothRaw | Mammoth Meat | 15 | 3 |
| 000C55 | REQ_Food_Meat_SabrecatRaw | Sabrecat Meat | 15 | 1.5 |
| 000C56 | REQ_Food_Meat_TrollRaw | Troll Meat | 15 | 1.5 |
| 000D58 | REQ_Food_Baked_GarlicBreadSlice | Garlic Bread Slice | 4 | 0.35 |
| 000D59 | REQ_Food_Baked_BraidedBreadSlice | Braided Bread Slice | 4 | 0.35 |

Five new raw-meat types (creatures vanilla had no meat item for — all normed to V15) + two "slice"
leftovers for the HearthFires custom breads. Confirms the raw-meat norm **V15 / W3 (large game),
W1.5 (medium)** and the bread-slice norm **V4 / W0.35**.

---

## 10. Inconsistencies (NOT smoothed over)

1. **Two coexisting naming conventions for the same concept** — raw meat uses BOTH
   `REQ_Food_Meat_<X>Raw` and `REQ_Food_Meat_Raw_<X>`; cooked meat uses BOTH `REQ_Food_Cooked_<X>` and
   `REQ_Food_Meat_Cooked_<X>`; drinks use BOTH `REQ_Alcohol_<Strength>_<X>` and legacy `REQ_Drink[N]_<X>`.
   A comparable-finder keying on the prefix must handle both spellings.
2. **EditorID dietary taxonomy is not uniform.** ATweaks tags Beef Stew `MeatVegan_` and Cabbage Soup
   `Vegan_`, but Cooked Beef `0721E8` had its `Meat` infix *dropped* (`REQ_Food_Meat_Cooked_Beef` →
   `REQ_Food_Cooked_Beef`) rather than dietary-tagged — so it's untagged where sibling meals are tagged.
3. **`REQ_NULL_SalmonCooked02 003541`** — a deliberately nulled/deprecated duplicate (Name "Salmon
   Steak", V4/W0.2, winner Master Patch). Do NOT derive from it (masters/`REQ_NULL` doctrine).
4. **Bare `Skooma 057A7A`** and vanilla `Survival_FoodHotBeefStew 0009E7` / `Survival_BYOHFoodHotClamChowder
   0009EA` — never renamed to the `REQ_` scheme though fully Requiem-tuned.
5. **`REQ_Food_GreenPact_TornFlesh 284894` — Value 0** (the only zero-value food; unique `GreenPact` prefix).
6. **`SoulHuskExtract 015A1E` — `Flags = 0`** (no NoAutoCalc, no FoodItem): carries neither a hunger
   tag nor a Nutrition effect, so Requiem's survival layer treats it as a **potion, not food** — unlike
   SoulHusk itself. Anomalous kit shape (2 effects only).
7. **`HolyWater 0A9661`** uses the legacy `REQ_LEGACY_FoodRestoreHealth 0F33CB` for its nutrition slot
   and is FoodItem-less yet still carries a hunger MGEF — an older-kit record surfaced via the LOTD patch.
8. **`AlcoholNutrition 000A37`** is a "Nutrition" DualValueMod targeting **UnarmedDamage** (not a
   resource pool) — odd vs the other Nutrition effects on Health/Magicka.
9. **`FoodFortifySneak 000C7D`** is the only fortify effect that is DualValueMod at BaseCost 0.025 AND
   has a VMA script attached; `FoodDamageAttributes 000911` at the winner is a PeakValueMod stub on
   StaminaRateMult, not the summon/damage it implies.
10. **Weight normalization has no single multiplier** — 3.5→1, 1.75→1, 6→3 are hand-tuned per record,
    not formulaic. Extreme raw-weight outlier: `MammothSnoutRaw 0669A4` **W5**.

---

## 11. Derivation rules for the skill (summary)

1. **Find the comparable by (family + prep-state + meal-size), then read its LIVE winner** — usually an
   Authoria patch, not FaB. Copy that record's effect kit, keywords, value, weight, flags.
2. **Every edible food = `NoAutoCalc, FoodItem`, exactly one `Survival_FoodRestoreHunger{VerySmall/
   Small/Medium/Large}` tag, and exactly one Nutrition tag** (`…SM` raw/light, `…HS` cooked/rich,
   `AlcoholNutrition` for drinks). Hunger size tracks meal bulk, not stat power.
3. **Raw meat/fish add a `Paralysis` effect** (`000808`, or `…ExemptKhajiit 000C4D` for fish/Khajiit-
   safe); **cooking removes it and upgrades Nutrition SM→HS + bigger/longer buffs.**
4. **Theme the fortify buffs to the food:** meat→Health/CarryWeight/Melee; bread→Stamina; cheese→
   Stamina+Magicka; fruit/veg→ResistDisease(30–40)+HealthRegen; soup/stew→big FortHealth/Stamina,
   long duration, high Nutrition.
5. **Alcohol** = Requiem MGEFs `18ED7C` (FortHealth) + `AE3729` (Inebriation) + Small hunger +
   `AlcoholNutrition`; **set magnitude by tier** (Mod 45/15/10 → Strong 90/30/15 → Extreme 120/45/30 +
   elemental resist), W0.75 (extreme W1), keywords `Apo_DPF_Alcohol` + `LoreBox_REQ_Alcohol_<Race>`.
   No ALCH addiction field — tolerance is the script MGEF `00025C`.
6. **Drug** = `NoAutoCalc` only (no FoodItem), short strong buffs + longer drains, no hunger/nutrition.
7. **Racial gate = keyword on the FOOD**, read by the race's `REQ_Trait_Cuisine_*` ability; tag
   earthborn/plant food with `LoreBox_REQ_BosmerExclusion 000A33` for the Green Pact system.
8. **PeakValueMod fortify/regen effects must key `Archetype.AssociationKey = Apo_NosStack 000802`** (anti-stack).
9. **Never derive from `REQ_NULL_*` (003541) or Flags-0 potion-like records (015A1E).**
