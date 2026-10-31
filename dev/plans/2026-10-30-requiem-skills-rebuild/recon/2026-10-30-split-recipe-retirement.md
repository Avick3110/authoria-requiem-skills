# Split hunt 5: recipe retirement (craftable or not)

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`census/stage1/COBJ.md`) found two ways to
retire a recipe, Requiem's `AD3B01` workbench and ARR's own `GetLevel() == 0` condition, and "some items with no
live craft recipe". It left the choice open. Scripts and data stay local (`census/splits/recipe_*`); the orphan
list is published as [`census/recipe-orphans.csv`](census/recipe-orphans.csv).*

## Aaron's account (2026-10-30)

- **Distribution instead of crafting.** For armour-only mods, Heisen sometimes made the pieces uncraftable and
  distributed them instead: on NPCs, as world loot, or in shops.
- **The no-crafting era.** In an earlier ARR version every mod-added armour was uncraftable, to keep the forge menu
  uncluttered.
- **The COCKS re-enable pass.** After the COCKS KID solution tidied the forge menu, many armours were made
  craftable again. Some may have been left uncraftable by accident.

The COCKS pass is in the instance as `Authoria - Crafting Catergories`. Its KID ini routes a named list of about 70
armour and weapon plugins into an "Exotic" forge category; the file was edited as late as 2026-05-29.

## Method

- **Items:** 6,882 mod-added ARMO / WEAP touched by ARR patches.
- **Recipes:** every recipe that makes each item, read from the live winners.
- **Status per item:**
  - *craftable*: at least one live craft recipe;
  - *retired*: has craft recipes, all disabled by `AD3B01` or `GetLevel() == 0`;
  - *never craftable*.
- **Obtainability:** every record referencing the item (NPC, outfit, leveled list, container, placed reference,
  quest, script).
- **Population tested:** 2,569 playable, non-template, non-held-out items that are either craftable or retired.

## Answer

1. **There is no single rule; retirement is a curation call made piece by piece.** It's not per mod (27 of 94 mods
   are mixed, holding half the items) and not per item type. What the records do show:
   - **Degraded copies are retired.** "Rusty", "broken", "worn" and similar copies: 75% retired v 33%. Vigilant
     keeps the base piece craftable and retires the "(Rusty)" copy for every Paladin and Knight set.
   - **Variants are thinned to declutter the forge.** Pieces with colour or style variants are retired at twice the
     rate of singletons (51% v 26%). 85 variant groups keep exactly one craftable piece.
   - **Armour-only mods are retired more** (47% v 26% of items), as Aaron described.
   - **Some items are retired for crafting but can still be tempered**: 326 of 1,146 retired items keep a live
     temper recipe.
   - **Not retired:** named uniques (retired *less*, 19%), LOTD replicas (1%), and pieces distributed elsewhere
     (retired less, 28% v 42%).
2. **Distribution instead of crafting is real but covers under half.** 457 of 1,146 retired items can be obtained
   elsewhere: containers 211, outfits 110, leveled lists 105, NPCs 36, placed in the world 23.
   - ARR's own distribution layer is `Authoria - Modded Outfit Distribution`: Haberdashery outfits, SPID onto
     bandits / Forsworn / witches, and SkyPatcher `outfitDefault`. It works through outfit records, which these
     counts include.
3. **689 retired items can't be obtained at all** ([`census/recipe-orphans.csv`](census/recipe-orphans.csv)):
   no live craft recipe, and no NPC, outfit, list, container, placement or quest reference.
   - **250 of them are in mods on the COCKS "Exotic" forge list**: Twilight Princess (123), Apex Khajiit (24),
     FVO Cloth (21), Wine Duchess (11), Arcane Mage (10), Sigrin (9), Moon Monk (7) and others. COCKS gave these
     items a forge category while their recipes stayed retired. **They are the strongest candidates for "left
     uncraftable by accident" after the re-enable pass.**
   - **439 are in mods not on the list:** FurSet by Keung (225), Vigilant (92), Bruma's BSAssets (69), BlanketScarf
     (18), Pig Iron (8) and others. These look like whole-mod or whole-set retirements from the no-crafting era, or
     deliberate.
4. **The two methods differ in use.** `GetLevel() == 0` mods are almost all fully retired (6 / 7). `AD3B01` is the
   selective tool (7 / 16 fully retired; most mixed).

## Limits

- The obtainability check can't see SPID `Item =` lines that name items by EditorID, or loot that a script grants.
  A plugin-name search of every SPID, KID and SkyPatcher ini found no distribution for the main orphan mods.
- Distribution was counted one step back. An item in a leveled list that nothing uses still counts as obtainable.

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H1 | Distributed elsewhere → retired (Aaron) | Real for 457 retired items, but distributed items are retired *less* overall (28% v 42%), so it's not the deciding rule |
| H2 | COCKS-listed mods → craftable (era, Aaron) | Flat at item level (37% v 33%). Shows up instead as the 250 COCKS-listed orphans (answer 3) |
| H3 | Armour-only mods → retired (Aaron) | **Kept** as a tendency (47% v 26%) |
| H4 | Enchanted items → retired | Weak (44% v 33%) |
| H5 | Armour weight class / weapon | Weak (26–40%) |
| H6 | Craft retired, temper kept | 326 / 1,146: a deliberate "not craftable, still improvable" shape |
| H7 | Retire method marks the policy | **Kept**: `GetLevel` = whole-mod; `AD3B01` = selective |
| H8 | Mostly-weapon mods → craftable | 1 / 20 fully retired v 12 / 47 |
| H9 | Variants retired, one recipe per piece kept | **Kept** as a tendency (51% v 26%; 85 groups keep exactly one) |
| H10 | Degraded copies retired | **Kept** (75% v 33%, n = 71) |
| H11 | Named uniques retired | Dead (retired less, 19%) |
| H12 | Replicas retired | Dead (1%; LOTD replicas are craftable by design) |
| H13 | Per-mod decision | Dead as a rule (27 / 94 mods mixed) |
