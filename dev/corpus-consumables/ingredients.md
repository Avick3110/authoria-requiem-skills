# Requiem - Alchemy Redone (AR) — INGREDIENT (INGR) system corpus

Live-load-order mining via houseCARL. Scope: every INGR record `Requiem - Alchemy Redone.esp`
touches. READ-ONLY corpus for the new Requiem-patching **consumables** skill.

**Companion lane:** the potion/MGEF side (per-second PeakValueModifier conversion, BaseCost retune) is
mined separately; this file is the *ingredient object* side only.

---

## 0. Accounting

- **AR touches 183 INGR** (cross_plugin_query `defined_in`-less scope; `total=183, capped=false`).
  Master breakdown (by defining plugin):
  Skyrim.esm 91 · ccBGSSSE037-Curios.esl 51 · Dragonborn.esm 11 · Requiem.esp-**defined** 10 ·
  ccBGSSSE001-Fish.esm 9 · Dawnguard.esm 5 · ccBGSSSE025-AdvDSGS.esm 4 · HearthFires.esm 2. (=183.)
- **Enumeration was read in full** (first pass truncated at 154/183 under the 80k default; re-run at
  `max_chars=300000` rendered all 183). All census numbers below are over the full 183.
- **Live winner is NOT always AR.** Winner census over the 183:
  `Requiem - Alchemy Redone.esp` 161 · `Requiem - ECSS.esp` 14 · `Authoria - Patch - Requiem - Wounds.esp` 6 ·
  `Authoria - Reqtificated - Miahil Hawk and Tern.esp` 1 · `Authoria - CC Curios Reqtificated.esp` 1.
  → **22 of 183 live winners are a downstream patch, not AR.** Derive from the *live winner* (doctrine),
  and expect the winner sometimes to have broken AR's own conventions (see §6).
- Sample depth: 24 records read at full effect-quartet detail (stratified: common vanilla, rare, CC
  Curios, all 10 Requiem-defined); Value/IngredientValue/Flags and Keywords censused over all 183.

---

## 1. What AR changes on an ingredient (the change-pattern)

AR is the **terminal alchemy authority**: it re-authors the whole alchemy payload over *both* vanilla
**and** Requiem.esp's own earlier alchemy pass. Confirmed by conflict tree on Wheat & Daedra Heart —
three plugins touch each (Skyrim.esm → Requiem.esp → AR winner), and AR's effect set differs from
*both* predecessors (e.g. Daedra Heart: vanilla and Requiem.esp each use a different BaseEffect quartet;
AR replaces it wholesale).

Per record AR sets, in one override:

| Field | Vanilla | AR (winner) | Rule |
|---|---|---|---|
| `Flags` | `0` (auto-calc ON) | **`NoAutoCalculation`** | AR hand-authors every number; kills auto-calc. 169/183 winners carry it (§6). |
| `IngredientValue` | `47` (auto-calc placeholder) | a real gold number | Becomes meaningful; drives the alchemy-strength tier. |
| `Value` | auto-derived | usually **== IngredientValue** | Merchant gold. Equal to IngredientValue for 168/183 (§4). |
| `Weight` | vanilla | **normalized** to a small ladder | See §5. |
| `Effects` (×4) | vanilla MGEFs | **4× `REQ_Alch_*` MGEFs**, hand-tuned magnitude/duration | See §2–3. |
| `Keywords` | vanilla | re-set to the vendor/gift scheme | See §7. |

**Derivation consequence for the skill:** a new ingredient must emit a *full* AR-style payload —
`Flags=NoAutoCalculation`, `Value==IngredientValue` (unless deliberately decoupling gold from tier),
normalized weight, a 4-effect `REQ_Alch_*` quartet, and the vendor keyword — copying each number from a
comparable AR ingredient. Never leave auto-calc on and never reuse vanilla/Requiem.esp MGEFs.

---

## 2. The effect quartet — structure & the `REQ_Alch_*` pool

- **Every ingredient has exactly 4 effects** (`Effects` is always a 4-item list; `Conditions` empty on
  all sampled; `Area` always `0`). Slot order is the Bethesda discovery order — `Effects[0]` is the
  first-known ("eat-to-learn") effect.
- **Every BaseEffect is a `REQ_Alch_*` magic effect.** The pool lives mostly in Skyrim.esm's FormID
  space (Requiem/AR *override* vanilla MGEF records and rename them `REQ_Alch_*`), plus a few defined in
  Requiem.esp itself. Verbatim examples of the pool (wire-token FormID → editorid):
  - `03EB15:Skyrim.esm` → REQ_Alch_RestoreHealth
  - `03EAF3:Skyrim.esm` → REQ_Alch_FortifyHealth
  - `03EB17:Skyrim.esm` → REQ_Alch_RestoreMagicka
  - `03EAF8:Skyrim.esm` → REQ_Alch_FortifyMagicka
  - `03A2C6:Skyrim.esm` → REQ_Alch_DamageStamina
  - `073F2C:Skyrim.esm` → REQ_Alch_DamageStaminaRegeneration
  - `10AA4A:Skyrim.esm` → REQ_Alch_LingeringDamageHealth
  - `10DE5E:Skyrim.esm` → REQ_Alch_LingeringDamageStamina
  - `03EB42:Skyrim.esm` → REQ_Alch_DamageHealth
  - `090041:Skyrim.esm` → REQ_Alch_ResistPoison · `090042` → REQ_Alch_WeaknessPoison
  - `039E51:Skyrim.esm` → REQ_Alch_ResistMagic · `073F51` → REQ_Alch_WeaknessMagic
  - `073F25:Skyrim.esm` → REQ_Alch_Slow · `073F30` → REQ_Alch_Paralysis · `073F20` → REQ_Alch_Fear
  - **Requiem.esp-defined pool members:** `AD3A55:Requiem.esp` → REQ_Alch_Dispel;
    `20B30F:Requiem.esp` → REQ_Alch_SoulTrap.
- A given `REQ_Alch_*` effect recurs across many ingredients (e.g. RestoreHealth appears on Wheat,
  Daedra Heart, Blisterwort, Void Essence). The skill maps a new ingredient's *intended* vanilla effect
  to its `REQ_Alch_*` equivalent, then copies the numbers from a comparable ingredient carrying it.

---

## 3. Magnitude/Duration convention — **duration is per-MGEF, magnitude is the ingredient's tier knob**

Same effect across ingredients keeps a **fixed duration** but a **tiered magnitude**:

| REQ_Alch effect | Duration (fixed) | Magnitudes observed | Notes |
|---|---|---|---|
| RestoreHealth | **20** | 0.5 (Blisterwort), 1 (Wheat), 2 (Daedra Heart) | magnitude ladder = tier |
| FortifyHealth | 300 | 8 | |
| Fortify skill (Destruction/Smithing) | 60 | 0.5 / 1 | |
| Damage(instant) Health/Magicka/Stamina | **0** | 8 / 16 / 32 | instant, duration 0 |
| DamageStaminaRegeneration | 60 | 16 (Wheat), 64 (Daedra Heart) | |
| DamageMagickaRegeneration | 60 | 32 | |
| LingeringDamage* | **15** | 2 | DoT |
| Fear | 15 | 4 | |

Worked quartets (all values verbatim from the live winner):

- **Wheat** `04B0BA:Skyrim.esm` — Val/IngrVal 1, W 0.1:
  `[0]` RestoreHealth m1 d20 · `[1]` FortifyHealth m8 d300 · `[2]` DamageStaminaRegeneration m16 d60 ·
  `[3]` LingeringDamageMagicka m2 d15.
- **Garlic** `034D22:Skyrim.esm` — Val 10, W 0.25:
  `[0]` ResistPoison m6 d60 · `[1]` FortifyStamina m8 d300 · `[2]` FortifyMagickaRegeneration m16 d300 ·
  `[3]` FortifyHealthRegeneration m16 d300.
- **Nightshade** `02F44C:Skyrim.esm` — Val 8, W 0.1:
  `[0]` DamageHealth m8 d0 · `[1]` DamageMagickaRegeneration m32 d60 · `[2]` LingeringDamageStamina m2 d15 ·
  `[3]` FortifyDestruction m0.5 d60.
- **Daedra Heart** `03AD5B:Skyrim.esm` — Val/IngrVal 1500, W 1:
  `[0]` RestoreHealth m2 d20 · `[1]` DamageStaminaRegeneration m64 d60 · `[2]` DamageMagicka m32 d0 ·
  `[3]` Fear m4 d15.
- **Blisterwort** `04DA25:Skyrim.esm` — Val 12, W 0.2:
  `[0]` DamageStamina m16 d0 · `[1]` Frenzy m2 d15 · `[2]` RestoreHealth m0.5 d20 · `[3]` FortifySmithing m1 d60.

**Rule for the skill:** for each of the new ingredient's 4 `REQ_Alch_*` effects, take **duration from any
existing ingredient bearing that effect** (it is standardized), and pick **magnitude from the tier ladder**
of a comparable ingredient (rarity/value peer). Do not compute magnitude from ingredient value directly —
it's a small discrete ladder, not a formula.

---

## 4. Value system: `Value == IngredientValue`, with one deliberate exception

- `Value` (merchant gold) **equals** `IngredientValue` for **168/183**.
- The **15 divergences** are:
  - **14 = the ECSS-won Curios set** (§6) — an artifact of ECSS reverting to auto-calc, IngredientValue
    is a placeholder there, not authored.
  - **1 genuine AR authorship divergence:** `REQ_Ingredient_Cleese_CritterPondFish` "Jon's Giant Abecean
    Longfin" `0972E6:Requiem.esp` — **Value 500 (gold) ≫ IngredientValue 15 (alchemy tier)**, W 30. An
    intentionally-expensive-but-alchemically-weak trophy fish. → **gold value and alchemy-tier value are
    independent knobs**; AR keeps them equal by convention but *can* decouple (a costly novelty item).
- Value ladder over the 183 is a curated discrete set (1,2,3,4,5,6,7,8,10,12,15,18,20,23,25,30,35,40,50,
  55,70,100,250,400,500,850,1500) — not a formula. Common tiers: 1 (25×), 25 (17×), 15/20 (18/16×),
  5 (16×), 30 (15×). High end: Daedra Heart 1500, Strange Remains 850, Void/Frost Salts 500/400,
  Crimson Nirnroot 250. → derive value by matching a rarity/role peer, not by arithmetic.

---

## 5. Weight normalization

Weights snap to a small ladder. Census over 183:
`0.1` (82×) · `0.25` (33×) · `0.2` (25×) · `0.5` (19×) · `0.3` (6×) · `1` (9×) · `0.75` (2×) · `5` (2×) ·
and singletons `1.5, 1.75, 2, 3, 30`. → **~90% of ingredients are 0.1 / 0.2 / 0.25 / 0.5.** Flowers/herbs
= 0.1, roots/bulbs = 0.25, salts/small = 0.2, hearts/organs = 0.5–1. Outliers are physically-large items
(Large Antlers 2, Mammoth Heart 5, Minotaur Horn 5, Ogre's Teeth 3, Longfin **30**). → derive weight by
matching the new ingredient's physical archetype to a peer on this ladder.

---

## 6. Anomalies & inconsistencies (NOT smoothed over)

1. **The ECSS-won Curios set (14 records) breaks AR's conventions.** `Requiem - ECSS.esp` is the live
   winner on 14 Curios ingredients and re-forwards them with **`Flags=0` (auto-calc back ON)** and
   **`IngredientValue ≠ Value`** (IngredientValue is a placeholder again). These are *exactly* the 14
   non-`NoAutoCalculation` records and 14 of the 15 Value≠IngredientValue records. Examples:
   `ccBGSSSE037_VoidEssence` `000820:ccBGSSSE037-Curios.esl` (Val 100, IngrVal 111, Flags 0),
   `ccBGSSSE037_FungusStalk` (Val 30, IngrVal 120), `ccBGSSSE037_HungerTongue` (Val 70, IngrVal 137).
   Their effect quartets are still the `REQ_Alch_*` pool, but the object-level payload is non-canonical.
   → **The skill must derive from the live winner but flag ECSS-won Curios ingredients as a
   convention-broken baseline** — don't propagate their auto-calc flag / placeholder IngredientValue to
   new content; use an AR-authored peer instead.
2. **Live winner ≠ AR for 22/183.** Blue Mountain Flower `077E1C:Skyrim.esm` is won by
   `Authoria - Patch - Requiem - Wounds.esp`, which keeps AR's first 3 effects but replaces `Effects[3]`
   with `_W_EffectAlchCureInfection` (`01CBEC:Wounds.esp`) — a **non-`REQ_Alch_` effect from the Wounds
   disease system**. So the effect pool is *mostly* `REQ_Alch_*` but downstream systems legitimately
   inject their own MGEF into a slot. Derive from the winner, and don't assume all four slots are pool
   members.
3. **The Longfin gold/tier decoupling** (§4) — the one intentional AR Value≠IngredientValue.
4. **3 ingredients carry NO keywords:** HumanHeart `0B18CD`, HumanFlesh `1016B3`, Blissbug
   `059155:ccBGSSSE025-AdvDSGS.esm` — forbidden/gore items not sold by vendors (§7).

---

## 7. Keyword system

Census over 183 (per-ingredient count: 0 kw ×3, 1 kw ×133, 2 kw ×43, 3 kw ×4):

| Keyword | Count | Meaning / rule |
|---|---|---|
| `VendorItemIngredient` `08CDEB:Skyrim.esm` | **175** | Default apothecary-vendor tag; on nearly everything. |
| `REQ_ArgonianTreat` `AE3727:Requiem.esp` | 17 | Requiem Argonian-food buff tag (fish/meat treats). |
| `GiftChildSpecial` `0A0E52:Skyrim.esm` | 12 | Giftable-to-child (eggs, berries). |
| `VendorItemFood` `0A0E55` | 8 | Edible-in-food-vendor (cooked/berry). |
| `VendorItemFoodRaw` `0A0E56` | 8 | Raw-food vendor (wheat, garlic, salt). |
| `GiftFlower` `0A0E54` | 7 | Giftable flower. |
| `isNirnroot` `1010B1` | 2 | Nirnroot / Crimson Nirnroot. |
| `REQ_VendorItem_BlackMarket` | 1 | MoonSugar (contraband — not normal apothecary). |
| `EnchantMorpholith_EC_SS_StaffEnchanter` | 1 | one ECSS-forwarded record. |

**Rule:** default is `VendorItemIngredient`. **Edibles** get `VendorItemFood`/`VendorItemFoodRaw`
*instead of* (not in addition to) VendorItemIngredient (Wheat, Snowberry, Juniper Berries, Salt carry
only the food tag). **Contraband** (MoonSugar) gets `REQ_VendorItem_BlackMarket` only. **Forbidden/gore**
items (HumanHeart, HumanFlesh, Blissbug) get **no keyword** (not vendored). Gift/nirnroot tags are
additive flavor. → the skill picks the vendor keyword by the ingredient's commercial role, mirroring a
role-peer.

---

## 8. The 10 Requiem.esp-DEFINED ingredients AR touches

These are Requiem's *own* new ingredients (defined in Requiem.esp, alchemy re-authored by AR). Verbatim:

| FormID | EditorID | Name | Val | IngrVal | W |
|---|---|---|---|---|---|
| `034E09:Requiem.esp` | REQ_Ingredient_HorkerFat | Horker Fat | 25 | 25 | 0.5 |
| `04365E:Requiem.esp` | REQ_Ingredient_WolfHeart | Wolf Heart | 25 | 25 | 0.75 |
| `04365F:Requiem.esp` | REQ_Ingredient_BearHeart | Bear Heart | 40 | 40 | 1.75 |
| `043661:Requiem.esp` | REQ_Ingredient_MammothHeart | Mammoth Heart | 250 | 250 | 5 |
| `043665:Requiem.esp` | REQ_Ingredient_SabrecatHeart | Sabre Cat Heart | 35 | 35 | 1 |
| `07524B:Requiem.esp` | REQ_Ingredient_StrangeRemains | Strange Remains | 850 | 850 | 0.75 |
| `0972E6:Requiem.esp` | REQ_Ingredient_Cleese_CritterPondFish | Jon's Giant Abecean Longfin | 500 | **15** | 30 |
| `3AB3BC:Requiem.esp` | REQ_Ingredient_RedGemDust | Red Glitterdust | 50 | 50 | 0.3 |
| `3AB3C5:Requiem.esp` | REQ_Ingredient_BlueDust | Blue Glitterdust | 100 | 100 | 0.3 |
| `44C4A8:Requiem.esp` | REQ_Ingredient_FrostBiteSpiderSalvia | Venomous Spittle | 50 | 50 | 0.3 |

They mostly = animal organs (heart/fat), two "glitterdust" gem powders, a frost-spider salvia, and the
Argonian trophy fish. Their quartets use the same `REQ_Alch_*` pool incl. the Requiem.esp-defined
members (RedGemDust `[3]` = REQ_Alch_Dispel `AD3A55:Requiem.esp`; StrangeRemains `[1]` = REQ_Alch_SoulTrap
`20B30F:Requiem.esp`). All carry `VendorItemIngredient` (+ Longfin also `REQ_ArgonianTreat`), all
`NoAutoCalculation`.

---

## 9. Derivation rules for the consumables skill (summary)

1. **Read the live winner** of the comparable AR ingredient — never assume it's AR; it may be ECSS/Wounds/
   Authoria (22/183). If the winner is an ECSS-won Curios record, treat it as convention-broken and pick
   a different AR-authored peer.
2. Emit a **full AR payload**: `Flags=NoAutoCalculation`; a 4-effect `REQ_Alch_*` quartet; `Value` and
   `IngredientValue` set (equal unless deliberately a gold≠tier novelty); normalized weight; vendor keyword.
3. **Effects:** map each intended vanilla effect → its `REQ_Alch_*` equivalent; **duration = the
   standardized value** for that effect (copy from any peer bearing it); **magnitude = the tier step** of
   a rarity/value peer. `Area=0`, no conditions. Slot `[0]` = the intended first-known effect.
4. **Value & weight:** copy from a role/rarity peer on the discrete ladders (§4, §5) — no arithmetic.
5. **Keyword:** `VendorItemIngredient` by default; `VendorItemFood(Raw)` for edibles (replaces it);
   black-market tag for contraband; none for gore/forbidden.
