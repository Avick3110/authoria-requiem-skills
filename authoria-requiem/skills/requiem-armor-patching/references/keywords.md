# Armor Keyword Vocabulary

Keywords are how Requiem's Reqtificator rules find a record — a wrong or missing keyword silently
changes how the armor is balanced. All FormIDs verified live (2026-06-08). Where a set keyword
isn't listed below, **read it off the comparable** rather than guessing.

## Table of contents
- [The split: source-carried vs Reqtificator-assigned](#the-split-source-carried-vs-reqtificator-assigned)
- [Standard keyword sets by part](#standard-keyword-sets-by-part)
- [Armor-type and armor-part keywords](#armor-type-and-armor-part-keywords)
- [Armor-set / material keywords](#armor-set--material-keywords)
- [Ranged-resistance tiers (assigned, do not stamp)](#ranged-resistance-tiers-assigned-do-not-stamp)
- [Tempering perks (assigned, do not stamp)](#tempering-perks-assigned-do-not-stamp)
- [Fist-damage perks (heavy gauntlets)](#fist-damage-perks-heavy-gauntlets)
- [Clothing & jewelry frames](#clothing--jewelry-frames)
- [Sounds](#sounds)

## The split: source-carried vs Reqtificator-assigned

This is the armor analog of the weapon damage-type rule. A Requiem source armor carries only
**input** keywords; the Reqtificator computes the **derived** ones at build time.

| Source-carried (set these) | Reqtificator-assigned (never stamp) |
|---|---|
| armor-type (heavy/light) | ranged-resistance tier (`rangedResistance.tier1..5`) |
| armor-set / material | tempering perk (`tempering.*`) |
| armor-part (cuirass/boots/…) | |
| VendorItemArmor | |
| `PerkFists<Material>` (heavy gauntlets only) | |

The Reqtificator's `feature_damageResistances` assigns a ranged-resistance tier **only to the
cuirass** (it gates on the cuirass part keyword + armor-type), and `feature_tempering` assigns the
smithing perk to **every** piece carrying the set keyword. Both live in
`…\Reqtificator\Data\ArmorKeywordAssignments_Requiem.esp.conf`. Get the **set keyword** right and
both follow automatically — no Requiem source armor carries a resist-tier or tempering keyword.

## Standard keyword sets by part

- **Cuirass / helmet / gauntlets / boots:**
  `[armorType<heavy|light>, armorSet<Material>, armorPart<Part>, VendorItemArmor 08F959]`.
- **Heavy gauntlets:** the above **+ `PerkFists<Material>`**. Light gauntlets do *not* add it.
- **Shield / buckler:** `[armorType, armorSet, VendorItemArmor 08F959, armorPart.shield 0965B2]`
  — the shield part keyword replaces the body-slot part keyword.

Some sets also carry incidental Survival-mode climate tags — `Survival_ArmorWarm 002ED9:Update.esm`
or `Survival_ArmorCold 002ED8:Update.esm` (and occasionally other Update.esm tags). They are not
Requiem balance keywords; copy what the comparable carries, don't invent them.

## Armor-type and armor-part keywords

| Keyword | EditorID | FormID |
|---|---|---|
| heavy | armorTypes.heavy | 06BBD2:Skyrim.esm |
| light | armorTypes.light | 06BBD3:Skyrim.esm |
| cuirass | armorParts.cuirass | 06C0EC:Skyrim.esm |
| helmet | armorParts.helmet | 06C0EE:Skyrim.esm |
| gauntlets | armorParts.gauntlets | 06C0EF:Skyrim.esm |
| boots | armorParts.boots | 06C0ED:Skyrim.esm |
| shield | armorParts.shield | 0965B2:Skyrim.esm |
| VendorItemArmor | VendorItemArmor | 08F959:Skyrim.esm |

## Armor-set / material keywords

From the config's `armorSets` block (verify against the comparable). Common smithing materials:

| Set | FormID | Set | FormID |
|---|---|---|---|
| iron | 06BBE3:Skyrim.esm | steel | 06BBE6:Skyrim.esm |
| steelPlate | 06BBE7:Skyrim.esm | dwarvenHeavy | 06BBD7:Skyrim.esm |
| dwarvenLight | ADDD6F:Requiem.esp | orcishHeavy | 06BBE5:Skyrim.esm |
| orcishLight | 003277:Update.esm | ebony | 06BBD8:Skyrim.esm |
| dragonplate | 06BBD5:Skyrim.esm | daedric | 06BBD4:Skyrim.esm |
| hide | 06BBDD:Skyrim.esm | leather | 06BBDB:Skyrim.esm |
| fur | AD3ADE:Requiem.esp | scale | 06BBDE:Skyrim.esm |
| elven | 06BBD9:Skyrim.esm | glass | 06BBDC:Skyrim.esm |
| dragonscale | 06BBD6:Skyrim.esm | chitinHeavy | 024103:Dragonborn.esm |
| chitinLight | 024102:Dragonborn.esm | stalhrimHeavy | 024106:Dragonborn.esm |
| stalhrimLight | 024107:Dragonborn.esm | chainmail | ADDD5E:Requiem.esp |

Named faction / unique sets (light unless noted) — read the comparable for the exact FormID:
`nightingale 10FD61`, `ancientShrouded AD39C4`, `shrouded 10FD62`, `thievesGuild 0009BC`,
`thievesGuildMaster 0009BF`, `forsworn 0009B9`, `penitusOculatus 0009BB`, `bonemold 024101`,
`ancientNord AD39BE`, `ancientFalmer AD39BF`, `wolf AD3A3F`, `guard AD39C3`. Full list in
`ArmorKeywordAssignments_Requiem.esp.conf` → `armorSets`.

## Ranged-resistance tiers (assigned, do not stamp)

`rangedResistance.*`, all `Requiem.esp`. The Reqtificator picks one for the **cuirass** by
armor-type + set. Listed for reference only — **never put one on a record.**

| Tier | FormID | Who gets it |
|---|---|---|
| none | AD3953 | (cleared first) |
| tier1 | AD3952 | light low: hide, leather, fur, forsworn, shrouded, thieves-guild, vampire… |
| tier2 | AD3951 | light mid: elven, scale, chitinLight, dwarvenLight, orcishLight, chainmail, alikr… |
| tier3 | AD3950 | light high: glass, dragonscale, ancientFalmer, stalhrimLight |
| tier4 | AD394F | faction leaders: nightingale, ancientShrouded, penitusOculatus, thievesGuildMaster |
| tier5 | AD3A25 | **all heavy** armor (flat) |

## Tempering perks (assigned, do not stamp)

`tempering.*`, all `Requiem.esp`. The Reqtificator picks one per piece by set keyword. Reference
only.

| Perk | FormID | Sets (examples) |
|---|---|---|
| craftsmanship | AD3AEC | iron, steel, hide, leather, fur, scale*, most basic + faction sets |
| advancedBlacksmithing | AD3AED | steelPlate, quicksilver |
| advancedLightArmors | AD3AEE | chainmail, scale |
| dwarvenSmithing | AD3AF1 | dwarvenHeavy, dwarvenLight |
| elvenSmithing | AD3AEF | elven, ancientFalmer |
| orcishSmithing | AD3AF2 | orcishHeavy, orcishLight |
| glassSmithing | AD3AF0 | glass |
| ebonySmithing | AD3AF3 | ebony, stalhrimHeavy, stalhrimLight |
| daedricSmithing | AD3AF4 | daedric |
| draconicSmithing | AD3AF5 | dragonplate, dragonscale |
| legendaryBlacksmithing | AD3AF7 | nightingale |

(*scale appears under both craftsmanship and advancedLightArmors in the config; the Reqtificator's
ordering resolves it — don't try to assign it yourself.)

## Fist-damage perks (heavy gauntlets)

Heavy gauntlets carry a material-specific unarmed-damage keyword `PerkFists<Material>`. Light
gauntlets do not. Verified:

| Material | EditorID | FormID |
|---|---|---|
| Iron | PerkFistsIron | 0424EF:Skyrim.esm |
| Steel | PerkFistsSteel | 09379E:Skyrim.esm |
| Ebony | PerkFistsEbony | 02C178:Skyrim.esm |
| Daedric | PerkFistsDaedric | 02C17B:Skyrim.esm |

Others (Dwarven, Orcish, Dragonplate, …) are vanilla `PerkFists*` keywords — **read it off the
same-material heavy-gauntlet comparable** and reuse the FormID it carries.

## Clothing & jewelry frames

AR-0 `ARMO` (`BodyTemplate.ArmorType = Clothing`, `ArmorRating = 0`). No armor-type/set/part
keyword, no ranged-resistance, no tempering. Value tracks the material/quality; weight is small.

- **Clothing** (robes, hoods, boots, gloves): `[ArmorClothing 06BBE8, VendorItemClothing 08F95B,
  ClothingBody 0A8657 / ClothingFeet / ClothingHands / ClothingHead]`. Example: Blue Robes
  (`REQ_Cloth_Robes_Body_Blue`, 0A199B) — Value 5, Weight 1.
- **Jewelry** (rings, amulets, circlets): `[ArmorJewelry 06BBE9, VendorItemJewelry 08F95A,
  ClothingRing 10CD09 / ClothingNecklace]`. Example: Gold Ring (`REQ_Ring_Gold`, 01CF2B) — Value
  250, Weight 0.2.

If the frame is enchanted, link the `ObjectEffect` but route the effect *design* (MGEF/ENCH) to
the `requiem-magic-patching` skill — this skill only sets the frame.

## Sounds

Body/helmet/gauntlets/boots carry null pickup/putdown links (generic armor foley). **Shields**
carry `BashImpactDataSet` and `AlternateBlockMaterial` (block/bash material) — copy both from the
same-material shield comparable (e.g. Daedric Shield: BashImpactDataSet `0183FE`,
AlternateBlockMaterial `016979`). Jewelry/clothing pickup/putdown sounds are overridden by
`Requiem - Immersive Sounds Compendium.esp` (the record-level winner for rings) — reading the
comparable's winner already folds the ISC audio in; you don't author against ISC separately.
