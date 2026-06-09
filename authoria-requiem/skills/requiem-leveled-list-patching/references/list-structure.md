# List structure — Requiem's leveled-list taxonomy and de-leveling model

How Requiem organizes LVLI/LVLN and how it tiers items without level gates. All FormIDs are live winners
(verify per record). Counts as of the studied Authoria load order: Requiem touches ~3433 LeveledItem,
~571 LeveledNpc, ~279 Container, 8 EncounterZone records.

## Contents

- The de-leveling model (Level 1 + repetition + list choice)
- Standard flags
- The `REQ_LI_*` distribution backbone (item lists)
- Faction / themed item pools (vanilla editorids, Requiem contents)
- LeveledNpc (LVLN) tiers and spawn lists
- REQ_NULL retired lists

## The de-leveling model — Level 1 + repetition + list choice

Requiem does **not** gate loot by player level. Across its lists, **every entry is `Level = 1`** — even
on overridden vanilla lists. The full material spectrum is reachable from level 1; what controls how
often you see a tier is:

1. **Which list the item is in** — Town (vendor) carries only low-tier basics; Loot (dungeon) spans
   everything; Best carries the high end.
2. **How many times the item is repeated** within a list — common materials appear several times, rare
   materials once. With `CalculateForEachItemInCount` + `ChanceNone = 0`, this repetition is a direct
   probability weight.

Example — `REQ_LI_Loot_Weapon_Sword` (`016578:Skyrim.esm`, 20 entries, all Level 1): iron sword
(`012EB7`) appears ~3×, steel (`013989`) less, a Dragonborn-tier sword once. `REQ_LI_Town_Weapon_Sword`
(`0165BC`, 3 entries): iron ×2, steel ×1 — vendors stock basics only. `REQ_LI_Best_Weapon_Sword`
(`0571AA`, 6 entries): the high-tier pool.

**To place a new item at tier X:** add it to the same lists its tier-X comparable lives in, at
`Level = 1`, `Count = 1`, repeated the same number of times the comparable is repeated.

## Standard flags

Requiem's leveled lists carry `Flags = CalculateFromAllLevelsLessThanOrEqualPlayer,
CalculateForEachItemInCount` and `ChanceNone = 0`. When you `Add` to the winner you inherit these — don't
change them. (`CalculateFromAllLevels…` + Level 1 everywhere = "any entry can roll regardless of player
level"; `CalculateForEachItemInCount` rolls each count separately; `ChanceNone = 0` = always yields.)

## The `REQ_LI_*` distribution backbone (items)

Requiem renames the vanilla weapon/armor lists to a structured scheme and curates their contents:

```
REQ_LI_{Loot|Town|Best|Blacksmith|Special}_{Weapon|Heavy|Light}_{Subtype}
```

- **Loot** — dungeon/world drops. **Town** — general-vendor stock. **Best** — high-end pool.
  **Blacksmith** — smith vendor stock. **Special** — special-context pool.
- **Weapon subtypes:** `Sword, GreatSword, WarAxe, BattleAxe, Mace, Warhammer, Dagger, Bow`, plus the
  rollups `1H`, `2H`.
- **Armor:** `{Heavy|Light}_{Body|Head|Hands|Feet|Shield}`.

Verified anchors (`:Skyrim.esm` unless noted):
- `REQ_LI_Loot_Weapon_Sword 016578`, `REQ_LI_Town_Weapon_Sword 0165BC`, `REQ_LI_Best_Weapon_Sword 0571AA`,
  `REQ_LI_Blacksmith_Weapon_Sword 09BC43`, `REQ_LI_Special_Weapon_Sword 1031B0`.
- `REQ_LI_Loot_Weapon_Bow 017024` (winner = WAR), `REQ_LI_Loot_Heavy_Shield 0165C2` (WAR),
  `REQ_LI_Loot_Heavy_Body 0166F8`, `REQ_LI_Loot_Light_Body 016705`.
- Generic loot pools used by containers: `REQ_LI_LootMidLevelContent 050EF1:Requiem.esp`,
  `REQ_LI_TreasureHunter_<Theme>` (e.g. `_Draugr AD8CA7:Requiem.esp`).
- Reward pool: `REQ_LI_RewardWeaponNoEnchant 03B486:Requiem.esp`.

A weapon's full placement footprint (from reverse-referencing the steel sword) spans Loot + Town + Best/
Blacksmith + Special + the faction pools + the reward pool — plus the auto-generated tempered-quality
seeds (`REQ_LI_Weapon_<Mat><Type>_Quality<N>_<H|N|D>_<dist>`, which are Reqtificator **outputs** — see
`merge-behavior.md`, don't author them).

## Faction / themed item pools

Themed enemy-drop and vendor lists **keep their vanilla EditorIDs** (Requiem overrode the contents, not
the names): `LItemBanditSword`, `LItemBanditBossSword`, `LItemVampireSword`, `LItemBanditHeavyCuirass`,
`LItemEnchWeaponSwordBlacksmith`, `dunMarkarthWizard…`, etc. Some are won by Requiem addons (e.g.
`LItemSoldierSonsSword` → `Requiem - Minor Arcana - Civil War.esp`). When you place a faction-flavored
item, add it to the matching themed pool in addition to the generic Loot/Town lists.

## LeveledNpc (LVLN) — spawn tiers

Same model: entries are NPC references, all `Level = 1`, same flags, weighted by repetition. Example —
`LCharWolf 0B83C2:Skyrim.esm` (5 entries) points at the fixed `EncWolf` actor (`023ABE`) repeated;
Requiem replaced the vanilla leveled-wolf reference with a **fixed** de-levelled NPC.

Requiem's bandit-style tiering uses sub-lists named `SubCharBandit<NN>_<Role>` where `NN` = tier 01–06
and Role = `Melee1H / Melee2H / Magic / Missile / Tank / Berserk / Boss` — these align with the
fixed-level `EncBandit01..06` tiers. Many are now `REQ_NULL_*` (retired) because Requiem replaced the
vanilla random-leveling structure with fixed tiers (`mergeLeveledCharacters` then merges contributors).

To add a new creature to spawns: reverse-reference the comparable creature's NPC across `LeveledNpc`,
then `Add` your NPC to the same active (non-`REQ_NULL`) spawn lists at Level 1, weighted like the
comparable.

## REQ_NULL retired lists

`REQ_NULL_*`-prefixed LVLI/LVLN are lists Requiem retired but kept so old references resolve (e.g.
`REQ_NULL_LootDraugrEnchWeapons100`, `REQ_NULL_SubCharBandit01Melee1H`). **Never add an item/NPC to a
`REQ_NULL_*` list** (it's a dead end) and never introduce a `REQ_NULL_*` reference. Leave Requiem's own
`REQ_NULL` entries inside live lists untouched (see `merge-behavior.md`).
