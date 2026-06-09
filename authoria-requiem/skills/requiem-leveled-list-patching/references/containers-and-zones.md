# Containers and encounter zones

Two record types that ride alongside leveled lists in the placement domain: `Container` (CONT) and
`EncounterZone` (ECZN). Both behave differently from LVLI/LVLN — read this before editing either.

## Containers (CONT)

### How Requiem curates a container

A Requiem boss chest references **themed leveled lists**, not raw gear. Worked anchor —
`TreasDraugrChestBoss` (`020671:Skyrim.esm`, winner `Requiem.esp`, 10 items; vanilla had 18, Legacy of
the Dragonborn had 23 — Requiem trims it). Its `Items` are all `ContainerItem` references to leveled
lists, weighted by repetition:

| Item | Reference | Meaning |
|---|---|---|
| 0 | `LootDraugrChestPotions25` | themed potion pool |
| 1 | `LootDraugrSoulGems20` | soul gems |
| 2–4 | `LootDraugrGems15` (×3) | gems, weighted high by repetition |
| 5 | `LootDraugrJewelry15` (Requiem-overridden) | jewelry |
| 6 | `LootDraugrScrolls10` (Requiem-overridden) | scrolls |
| 7 | `TGLootProwlersProfit` | thieves-guild perk loot |
| 8 | `REQ_LI_LootMidLevelContent 050EF1:Requiem.esp` | Requiem generic mid-level pool |
| 9 | `REQ_LI_TreasureHunter_Draugr AD8CA7:Requiem.esp` | Requiem treasure-hunter pool, themed |

Patterns to copy:
- **Reference lists, not items.** Curated loot comes from themed `Loot<Theme><Category>` lists (potions/
  soul gems/gems/jewelry/scrolls) plus Requiem's own generic pools (`REQ_LI_LootMidLevelContent`,
  `REQ_LI_TreasureHunter_<Theme>`). Don't stuff raw weapons/armor — gear comes from the enemy's
  death-item/outfit (the NPC domain), not the chest.
- **Repetition is the weight** here too (gems ×3).
- **Trim, don't overstuff.** Requiem deliberately shrinks vanilla/overhaul chests to ~10 curated refs.

### How to patch a container

**CONT is NOT in the Reqtificator merge** (only LVLI/LVLN are) — a container resolves by normal conflict
winner, and an override **replaces the whole `Items` list**. So there's no merge safety net: build on the
**winner** and keep its curated entries, then `Add` your reference. For a brand-new modded container,
fill it by referencing the theme-appropriate Requiem leveled lists rather than direct items.

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"<container FormID>", field_path:"Items", verb:"Add",
   compose:{type:"ContainerEntry", sets:[
     {path:"Item.Item",  value:"050EF1:Requiem.esp"},   # REQ_LI_LootMidLevelContent
     {path:"Item.Count", value:"1"}]}}
]
```

Repeat to weight, or add several themed-list references for a full fill. Because you're building on the
winner, its existing curated items stay; you're appending. If you must author the whole list from scratch
(new container with vanilla/overhaul contents you want to replace), use `ReplaceAll` deliberately and
include the full curated set.

## Encounter zones (ECZN)

### What the Reqtificator actually does

`openEncounterZones = true` runs `OpenCombatBoundaries`, which sets the **`DisableCombatBoundary`** flag on
every encounter zone at build (skipping only zones in the `ClosedEncounterZones` FormList). **It does not
change `MinLevel`/`MaxLevel`.** The flag just lets combat cross the zone's boundary — it is not a
de-leveling step.

Requiem's de-leveling comes from elsewhere:
- **Fixed-level NPCs** (the NPC domain — `PCLevelMult` removed, flat `Configuration.Level.Level`).
- **Level-1 leveled lists** (this domain).

So the encounter zone is mostly *not* the lever for "make this dungeon Requiem-appropriate."

### Requiem's own ECZN overrides

Requiem ships only ~8 ECZN overrides — e.g. the DLC2 dungeon zones (`DLC2Book0?DungeonZone`,
`0142B1..` etc.) set to `MinLevel = 25`, `MaxLevel = 0`, `Flags = NeverResets`, and
`REQ_Whiterun_HallOfTheDeadCatacombs AD3984:Requiem.esp` (MinLevel/MaxLevel 0, `NeverResets`). That's the
shape to mirror if you ever need a custom zone.

### How to handle a new area's encounter zone

- **Default: do nothing.** The build pass opens the zone automatically; fixed-level NPCs + Level-1 lists
  carry the balance.
- **Only if a modded zone hard-gates content** with a high `MinLevel`/`MaxLevel` range that fights
  Requiem's flat design, set the levels (mirroring Requiem's `MinLevel`-only, `MaxLevel = 0`,
  `NeverResets` pattern) — and explain why. Prefer fixing the zone's NPCs and list placement first.

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"<zone FormID>", field_path:"MinLevel", value:"0"},
  {formid:"<zone FormID>", field_path:"MaxLevel", value:"0"},
  {formid:"<zone FormID>", field_path:"Flags",    value:"NeverResets"}
]
```
