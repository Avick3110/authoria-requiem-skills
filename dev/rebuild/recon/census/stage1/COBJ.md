# COBJ census: ConstructibleObject conventions in ARR's handmade Requiem patches

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 5,350 COBJ records · 17,810 diff rows · 95 plugins · held out: `Authoria - Reqtificated - Falkreath Shades.esp`
and `Authoria - Reqtificated - The Rot Below.esp` (15 records, 53 rows). Evidence: stage-1 consensus
(`ConstructibleObject.json`), the ledger, and 12 live houseCARL reads (listed in §7).

COBJ is **all upstream input**. The Reqtificator holds no COBJ records, and no tested build path writes COBJ (facts 32, 33).
Nothing in this type is baked.

## 1. Conventions

| # | Convention | Class | In/baked | Support (rows / plugins) | Held-out result | Conf. |
|---|---|---|---|---|---|---|
| 1 | Temper recipes drop the vanilla gate pair `EPTemperingItemIsEnchanted() != 1 OR` + `HasPerk 05218E` | Requiem-aligned: Requiem temper COBJs carry no EPTempering (verifier, live) | input | 6,379 removed / 73 (EPTempering 2,174 / 63; 05218E 2,077) | **Follows**: all 11 held-out tempers drop EPTempering; 9 also drop 05218E, 2 keep it as their material perk (58E728, 58E730; see #2) | high |
| 1a | Gap: a temper ARR leaves untouched keeps the vanilla gate (Twilight TO_ temper 000803) | outlier (coverage gap) | input | 1 seen | n/a | high |
| 2 | Temper recipes end with **one** `HasPerk` on the Requiem smithing perk for the item's material | Requiem-aligned: 693 / 826 Requiem temper COBJs have exactly one HasPerk (fact-sheet figure, not rechecked; verifier live: 671 REQ_Temper_* with exactly 1 condition and 1 item) | input | 3,602 HasPerk-added rows / 76 (of 6,063 Conditions-added rows / 84) | **Follows**: 3 held-out tempers read live (58E728, 58E730, 58E72F), each with exactly 1 HasPerk | high |
| 3 | Temper recipes take one material item, count 1 (the material's ingot / leather) | Requiem-aligned: REQ_Temper_* = 1 × 05ACE5 (live) | input | 1,927 removed / 62; 1,632 added / 63 (a swap, not a trim) | **Follows**: e.g. Falkreath 05AD93 → 05ACE5, Rot Below 05ADA1 → 05AD9F | high |
| 4 | Craft recipes keep the mod's own unlock conditions (quest / global / GetItemCount / mod perk) and gain a smithing-perk HasPerk | Requiem-aligned for the perk (REQ_Forge_* carry HasPerk 0CB40D); house for keeping the unlocks | input | most of the craft half (3,920 added craft rows) | No held-out craft row adds a perk | med |
| 5 | Unique-tier craft recipes use **Arcane Craftsmanship 309D25** | house convention (no Requiem comparable read) | input | 646 rows (580 in pattern 118: Vigilant, Glenmoril, Dac0da) | n/a | low-med |
| 6 | Retire a recipe by setting `WorkbenchKeyword` = **AD3B01 REQ_DisableRecipe** | Requiem-aligned: Requiem.esp wins 104 COBJs on AD3B01 (live; not rechecked by verifier) | input | 933 rows / 40 (685 craft, 248 temper) | Not used (0 rows) | high (idiom); per-record lead |
| 7 | Second retire idiom: add `GetLevel() == 0` (never true) | house convention: Requiem.esp has 0; USMP - Requiem has 1 (AND-ed with AD3B01) | input | ≈681 real disables of 1,039 GetLevel rows / 18 | **Follows**: Falkreath craft recipes 58E727 and 58E729 disabled this way, with their tempers left live | med |
| 8 | Clothing recipes move to the Threads of the North loom `000800` (Ivy_CraftingLoom); tanning-rack armour moves to the forge `088105` | house convention (needs Threads of the North in the list) | input | 110 / 14 (loom 75 rows / 8 plugins; forge 33) | n/a (0 rows) | med |
| 9 | Ammo and consumable recipe yields are rebalanced | arrows 24 → 30 = Requiem-aligned (REQ_Forge_Arrow_* yield 30, live); bullets / poisons halved = house | input | 18 / 6 | **Follows**: Falkreath poisons 4498C4 / 4498C5 go 5 → 2 | med |
| 10 | Where the source ships no recipe, the patch defines its own `Authoria_Forge_*` / `Authoria_Temper_*` / `ARR_Temper_*` COBJs | house convention | input | 13 / 3 | n/a | med (not re-read) |
| 11 | Item-list reorders, bookkeeping, presentation fields | noise | — | 618 / 12; 76 / 9; 36 / 7 | 2 reorder rows | high |

**Validation.** The held-out rows (53; 11 tempers) fall entirely under conventions 1–3, 7, 9 and 11. None contradicts a convention.
The held-out plugins exercise no recipe retire by AD3B01, no bench routing, and no unique-tier perk.

## 2. Upstream checklist (what an agent writes, per mod)

1. **Inventory**: list every COBJ that creates or tempers the mod's items, by bench (`088105` forge, `0ADB78` armour
   table, `088108` sharpening wheel, `07866A` tanning rack, mod benches).
2. **Decide each item's status**: keep, or retire. Retire only when the item is itself retired (renamed `NULL_` / `NP_` /
   `REQ_NULL_`) or another live recipe makes it. Otherwise the item can no longer be crafted (fact: a disable needs a replacement).
3. **Retire**: set `WorkbenchKeyword` = `AD3B01:Requiem.esp` (Requiem's idiom). `GetLevel()==0` is ARR's alternative
   (open call A). Never put AD3B01 in `CreatedObject` (bug B2).
4. **Temper recipes**: delete the EPTempering + 05218E pair. Write exactly one `HasPerk(<material perk>) == 1` on Subject.
   Set Items to one material entry, count 1. For Daedric, consider Requiem's night gate `GetCurrentTime() > 23` AND 0CB413 (fact 31).
5. **Craft recipes**: keep the mod's unlock conditions. Add the material's smithing HasPerk (Craftsmanship 0CB40D for basic
   tiers; 309D25 for unique gear per #5). Rewrite ingredients onto vanilla / Requiem materials. Craft counts are the author's call.
6. **Benches**: clothing goes to the ToN loom `000800:Threads of the North.esp`. Armour still on the tanning rack goes to the forge.
7. **Yields**: arrows 30, as Requiem does. Check bolts against Requiem's 30 (bug B6).
8. **Gaps**: if an item has no recipe, define `Authoria_Forge_*` / `Authoria_Temper_*` in the patch.

**Material → perk map** (live EditorIDs `REQ_Smithing_*`; the live winner of each perk is `Requiem - Reading teaches smithing perks.esp`, not Requiem.esp; counts = HasPerk-added rows, verifier ledger recount):

| Perk | FormID | Rows added |
|---|---|---|
| Craftsmanship | 0CB40D | 1,179 |
| Arcane Craftsmanship | 309D25:Requiem | 648 |
| Ebony | 0CB412 | 360 |
| Elven | 0CB40F | 324 |
| Advanced Light Armors | 0CB414 | 239 |
| Advanced Blacksmithing | 05218E | 213 |
| Daedric | 0CB413 | 183 |
| Glass | 0CB411 | 108 |
| Draconic | 052190 | 70 |
| Dwarven | 0CB40E | 45 |
| Legendary Blacksmithing | 17B8BF:Requiem | 32 |
| Orcish | 0CB410 | 21 |

> **Correction.** The consensus "was" text maps Glass to 0CB413. Live: **Glass = 0CB411,
> Ebony = 0CB412, Daedric = 0CB413**. 05218E is Requiem's *Advanced Blacksmithing*, a real material perk, not just
> vanilla Arcane Blacksmith. Removing it is part of the gate-pair strip only when the item's tier isn't Advanced.

## 3. Downstream bake package

**Empty for COBJ.** The build writes no COBJ (facts 32, 33), so nothing needs hand-baking. Temper keywords on the
*items* (REQ_Tempering_*) are ARMO / WEAP work and are re-added by the build (fact 6). They are not a COBJ concern.

## 4. Per family

**Conditions | removed** (6,379 / 73; held-out 20; verified: fixed).
- Consensus: the strip in #1.
- Exceptions:
  - The CC Fishing and Saints & Seducers replica tempers keep the vanilla 4-condition chain (verifier).
  - Falkreath 58E728 and 58E730 keep 05218E alone as their single perk (live). That is legitimate: steel item, Advanced Blacksmithing.
- Not real removals: the Dint Hair GetItemCount (patterns 219 / 220, 130 each) and Vigilant quest-unlock (362 / 363) pairs
  are index shifts from a list rewrite. Same text before and after (verified on Dint 000DC6 and Vigilant 0B1CD7).
- IsPS3 removal (pattern 508) is dead-platform cleanup.
- Disagreement: ≈84 % of tempers follow the strip, not 100 %. Candidate reasons:
  - patch age / author habit: the CC patches are separate `Authoria - CC …` plugins, not `Reqtificated`;
  - deliberate 05218E retention by tier (Falkreath).

**Conditions | added** (6,063 / 84; held-out 11; verified: fixed, **material error corrected**).
- Consensus: one material HasPerk (#2), plus GetLevel()==0 disables (#7).
- Requiem itself is not "one gate" everywhere: 133 / 826 tempers have two conditions. Dwarven = `HasPerk 0009D4:Update.esm OR
  0CB40E`; Daedric = `GetCurrentTime() > 23 AND 0CB413`.
- ARR does not copy these two-condition shapes. Held-out Rot Below CJ03TemperWeaponGreatFlame (672865) has 0CB413 alone, no
  night gate (lead L3).
- GetLevel()==0 splits:
  - **real disables**: GetLevel sole, or in an AND chain. Twilight Princess 316, Unslaad 232, CC replica 4-cond chain, ~12 Dint.
  - **no-op OR terminator**: Dint Hair 358 / 370 rows are `[GetItemCount >= 1 OR, GetLevel()==0]`. The source already ended
    in a dangling OR, so behaviour is unchanged (B5).
- ARR adds GetLevel itself: the source plugins carry none (Twilight 000802, Unslaad 05267F: 0 → 1).
- Moon Monk keeps its mod perk 00088C (186 removed, 180 re-added). There are 69 net swaps 0CB411 → 0CB414 (Glass →
  Advanced Light Armors): a tier judgement, plausible for robes.

**Items | removed / added** (1,927 / 62 and 1,632 / 63; held-out 9 + 9; verified: fixed → Requiem-aligned).
- Tempers swap to one count-1 material (verified on Vigilant / Velothi trees: replaced, not trimmed).
- Removed materials are mostly Requiem-reworked ones: Corundum 05AD93 189, Steel 05ACE5 123, Leather 0DB5D2, BoneMeal 034CDD 90.
- Craft recipes keep multi-item lists, counts 1–8 (Twilight cuirass 3 items).
- Unsampled leads:
  - BoneMeal ×2 removals (pattern 262): swap vs trim not proven;
  - Velothi Dragonborn materials removed (346): replacement not checked.

**WorkbenchKeyword | changed** (110 / 14 in the family; plus 933 rule-decided AD3B01 rows; verified: fixed, **material error**).
- Routing (#8): tanning → loom 50; tanning → forge 33; forge → loom 19; Skyforge 0F46CE → loom 6.
- One outlier: AD3B45 (B1).
- The AD3B01 rows were labelled by script as "no other live recipe creates the item".
- Live sampling: most retire an item that is itself retired:
  - Vigilant 1B4554 → `NULL_zzzCHArmorArkayKnightCuirassRust`;
  - FurSet 015EA1 → `REQ_NULL_AM_…`;
  - Vigilant 0E04DD → `NP_…`.
- Across all live winners on AD3B01, 754 / 1,299 target an item without a NULL / NP prefix (one aggregate, not rechecked). Mostly clothing / fur variants:
  FurSet 149, Moon Monk 51, FVO 49, Hal's 37.
- Five were checked (two by me, three by the verifier): Moon Monk 000900, FVO 000840, Pig Iron Boots, Ruby Flower Gloves NoClaws have
  **no** live craft recipe left; Lastriem Pants shows a second shape (temper retired, craft live).
  Intent unknown: duplicate variants retired on purpose, or lost? (open call B).
- Candidate reason, author habit by mod tier: armour mods retire whole items; clothing mods retire duplicate `_Clothes`
  copies of armour pieces.

**CreatedObjectCount | changed** (18 / 6; held-out 2; verified: agree, **but the family file is wrong**).
- The family shows null → null and calls it noise. The ledger has the real values:
  - Glenmoril bullets 10 → 5 and 20 → 10 (11 bullet / shell records); Glenmoril iron-dagger material 10 → 3;
  - Falkreath poisons 5 → 2;
  - Apex Khajiit and Auri arrows 24 → 30;
  - Val Serano bolts A038AB 25 → 10;
  - Vigilant 0FD09B 1 → absent.
- Reclassify from noise to convention #9. Arrows match Requiem's 30. The rest have no Requiem comparable.

**`*` | defined** (13 / 3; agree, not re-read): Obi Hedge Knight, Cimmerian, Scout Armor. 8 temper, 5 craft.

**CreatedObject | changed** (4 / 3; fixed, material error):
- Velothi 000881 retarget 00081C → 00081D (Rhino Beetle Full Helmet), plausibly a source fix (lead);
- two LOTD wrong-field edits (B2);
- the Vigilant 0FD09B blank-out.

**Items | changed** (1): part of the 0FD09B blank.

## 5. Suspected bugs (for Aaron's review; none filed)

| # | Record (FormID, plugin) | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| B1 | DBM_AAWulfharthLTemperREP `217CBB:LegacyoftheDragonborn.esm`, LOTD patch | WorkbenchKeyword | AD3B45 (REQ_DisableAlcoholBlur, a non-bench keyword) | AD3B01, or 0ADB78 if tempering is meant to stay | verifier read 217CBB, AD3B45 | outlier bug |
| B2 | DBM_TiberArmorLTemperREP `217CB9`, DBM_MiraakMaskLTemperREP `217CBF` (LOTD), LOTD patch | CreatedObject | AD3B01 (a keyword in the item slot) | WorkbenchKeyword = AD3B01, CreatedObject unchanged | verifier reads; bench still 0ADB78. Net effect is a disable, but a wrong-field edit | outlier bug |
| B3 | Twilight Princess cuirass `000802` craft recipes (base, City, Home, City_to_Normal, Home_to_Normal, City_to_Home 000841, Home_to_City 000842, Armored 0008AF), Twilight Princess patch | Conditions | GetLevel()==0 on all | a live craft recipe, unless no-craft is intended | verifier: only the TO_ temper 000803 is live, and ARR left it untouched, so it keeps the vanilla EPTempering + HasPerk gate (#1a); 316 GetLevel rows in the plugin | suspected systemic (if unintended) |
| B4 | Unslaad SnowSpirit 01–06 (01 = `05267F`), Unslaad patch | Conditions | GetLevel()==0 on 01–06; SnowSpirit07 `052685` stays live with HasPerk | same | verifier | suspected (same root as B3) |
| B5 | Dint Hair wig recipes, e.g. `000DC6:[dint999] HairPack02.esp` (358 rows), Dint Hair patch | Conditions | `[GetItemCount OR, GetLevel()==0]` | if a disable was meant: GetLevel must be AND-ed (fix the OR flag) or use AD3B01 | verifier: same gate as the source, a no-op | suspected systemic bug, or noise if intent was only to close the dangling OR |
| B6 | AX_ValCrossbowBoltsRecipe `A038AB:AX ValSerano.esp`, Val Serano patch | CreatedObjectCount | 10 (from 25) | 30, like REQ_Forge_Bolt_* | ledger; live Requiem bolts 30 | outlier (lead) |
| B7 | Moon Monk 0RecipeArmorKhajiitRobes_Clothes `000925` → `000900`; FVO aaa_CraftFur2 / Craft2 `000866` / `000883` → `000840`; Pig Iron Boots craft `000802` (temper 000808 left); Ruby Flower Gloves NoClaws craft `000835` (tuning 000851 left); reverse shape: Lastriem Pants temper `000816` retired, craft `000815` live | WorkbenchKeyword | AD3B01 on the craft recipe(s) of a live-named item (Lastriem: on the temper) | a surviving recipe, or the item retired | live reads (mine + verifier): no live craft recipe for Moon Monk, FVO, Pig Iron, Ruby Flower; Lastriem craftable but untemperable | lead (open call B) |
| B8 | Vigilant `0FD09B:Vigilant.esm`, Vigilant patch | all | CreatedObject, bench, items and count cleared | AD3B01 on the bench (the Requiem idiom) | ledger patterns 2768–2772 | noise / outlier (harmless) |

## 6. Open calls for Aaron

- **A. One retire idiom.** AD3B01 (Requiem's; 40 plugins) or GetLevel()==0 (ARR's; 18 plugins)? Five plugins use both
  (Glenmoril, Lastriem, Pig Iron, Ruby Flower, Unslaad), and the held-out Falkreath uses GetLevel. Should the skill teach AD3B01 only?
- **B. Item reachability rule.** Is "retire a recipe only if the item is retired (NULL_ / NP_) or has another live
  recipe" the rule? 754 AD3B01 disables target live-named items (B7).
- **C. Mod unlock gates kept** (Vigilant quest / global, Dint GetItemCount): the data says yes. Confirm that's intended.
- **D. 309D25 Arcane Craftsmanship for unique craft gear**: house tier rule, or per-mod judgement?
- **E. Bench routing** to the ToN loom for all clothing: standing rule? It makes Threads of the North a hard dependency.
- **F. Yields**: is halving modded ammo / poison output a rule, and is the bolt 10 (B6) deliberate?
- **G. Two-condition Requiem tempers** (Daedric night gate, Dwarven OR): should the skill copy them? ARR currently writes one perk (L3).
- **H. Dint Hair GetLevel**: intended as a disable (B5), or tidy-up?

## 7. Limits

- **Coverage by name.** The corpus selects plugins named `Authoria - Reqtificated - *` (plus some CC / Rectificated spellings). It missed
  `Authoria - Reqticated - Lions Mane.esp` (60 live AD3B01 disables) and `Authoria - Master Patch - Bruma.esp` (139). Neither is in the ledger.
- **Untouched records.** The diff shows only what ARR changed. A mod recipe ARR left alone is invisible, so "every temper has one
  perk" is a claim about touched recipes only.
- **Unsampled / not re-read:** the `*|defined` family; the BoneMeal and Velothi Dragonborn item leads; 309D25's comparable; whether
  each of the 933 AD3B01 disables leaves its item reachable (5 checked); the CC replica 4-cond exceptions (count not taken).
- **Hidden flag bytes**: the Vigilant 0B1CD7 condition rows match in text, but the tree flags them as differing. Flag bytes not ruled out.
- **Requiem side note**: REQ_Forge_Arrow_Daedric `003987:Dawnguard.esm` and REQ_Forge_Bolt_Daedric `964F35` both yield 300 (others 30): a
  Daedric pattern, likely deliberate. Not an ARR concern; compare against the non-Daedric 30.
- **ksws03_quest.esp** failed to load in every live read (load failure, not reorder). Its recipes, if any, are absent. A
  "no other recipe" check cannot rule out one there.
- **Live reads used (12):** perks 0CB40D–0CB414, 05218E, 052190, 309D25, 17B8BF, ToN 000800, Vigilant 0B1CCD; held-out
  58E728 / 58E730 / 58E72F; Requiem COBJ yields; 5 AD3B01 samples; reverse references for 3 items; 2 AD3B01 aggregates.
