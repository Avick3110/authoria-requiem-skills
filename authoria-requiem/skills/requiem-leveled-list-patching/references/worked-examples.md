# Worked examples

Three real placement cases plus a round-trip. FormIDs are live winners from the studied Authoria
load order (`:Skyrim.esm` unless noted) — re-read before relying on them.

## Example 1 — place a new steel-tier sword into loot, vendor, and faction lists

**Subject:** a modded craftable one-handed sword, already statted at steel tier by
`requiem-weapon-patching` (call its FormID `<Sword>`).

**Plan from the reverse-reference.** Querying which Requiem lists contain the steel sword
(`013989`) returns the placement footprint:

```
housecarl_cross_plugin_query type="LeveledItem" references="013989:Skyrim.esm" plugins=["Requiem.esp"]
→ REQ_LI_Loot_Weapon_Sword 016578, REQ_LI_Town_Weapon_Sword 0165BC,
  REQ_LI_Blacksmith_Weapon_Sword 09BC43, REQ_LI_Special_Weapon_Sword 1031B0,
  LItemBanditSword 037C19, LItemBanditBossSword 03DF1D, LItemVampireSword 02DF97,
  REQ_LI_RewardWeaponNoEnchant 03B486:Requiem.esp,
  (+ REQ_LI_Weapon_SteelSword_Quality*_N_* — Reqtificator output seeds, ignore)
```

**Tier weighting.** In `REQ_LI_Loot_Weapon_Sword` the steel sword is at `Level = 1`; iron is repeated
more, steel less. For a comparable steel sword, one Loot entry plus one Town/Blacksmith entry matches
its availability.

**Patch (additive, build on the winner):**

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"016578:Skyrim.esm", field_path:"Entries", verb:"Add", compose:{type:"LeveledItemEntry",
     sets:[{path:"Data.Level",value:"1"},{path:"Data.Count",value:"1"},{path:"Data.Reference",value:"<Sword>"}]}},
  {formid:"0165BC:Skyrim.esm", field_path:"Entries", verb:"Add", compose:{type:"LeveledItemEntry",
     sets:[{path:"Data.Level",value:"1"},{path:"Data.Count",value:"1"},{path:"Data.Reference",value:"<Sword>"}]}},
  {formid:"09BC43:Skyrim.esm", field_path:"Entries", verb:"Add", compose:{type:"LeveledItemEntry",
     sets:[{path:"Data.Level",value:"1"},{path:"Data.Count",value:"1"},{path:"Data.Reference",value:"<Sword>"}]}}
]
```

`016578`/`0165BC`/`09BC43` are `REQ_LI_*` (Requiem-renamed) lists, so the patch auto-masters
`Requiem.esp` — the merge will see it. If the sword has a bandit flavor, also add it to `LItemBanditSword
037C19`. Each list is built on its winner, so Requiem's curated entries stay and yours is appended.

## Example 2 — distribute a new creature into wolf spawns

**Subject:** a modded wolf-type creature, already classified/balanced by `requiem-npc-patching` (call it
`<Wolf>`); its race is the recognized Requiem wolf race, so the race + Reqtificator already carry its
traits — this step only makes it spawn.

**Plan.** Reverse-reference the comparable creature (`EncWolf 023ABE`) across `LeveledNpc`, or go straight
to `LCharWolf 0B83C2` (5 entries, all Level 1, fixed `EncWolf` repeated). Add your creature there:

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"0B83C2:Skyrim.esm", field_path:"Entries", verb:"Add", compose:{type:"LeveledNpcEntry",
     sets:[{path:"Data.Level",value:"1"},{path:"Data.Count",value:"1"},{path:"Data.Reference",value:"<Wolf>"}]}}
]
```

Because `LCharWolf` is `Skyrim.esm`-defined and `<Wolf>` may be a non-Requiem form, reference a Requiem
form to master Requiem (e.g. also add to a Requiem-defined spawn pool if one fits) or accept that this
single list won't enter the merge — with one contributor the conflict winner (your base + entry) is used
anyway, which is still correct. Confirm the `masters:` read-back either way.

## Example 3 — fill a new mod's boss container

**Subject:** a modded dungeon boss chest (`<Chest>`) that a content mod left stuffed with raw gear or
vanilla-overstuffed loot.

**Approach.** Mirror Requiem's `TreasDraugrChestBoss` pattern (10 curated references): clear the raw gear
intent and reference themed Requiem leveled lists + generic pools. Gear itself rides the boss's
death-item/outfit (the NPC domain), not the chest.

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"<Chest>", field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
     sets:[{path:"Item.Item",value:"050EF1:Requiem.esp"},{path:"Item.Count",value:"1"}]}},  # REQ_LI_LootMidLevelContent
  {formid:"<Chest>", field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
     sets:[{path:"Item.Item",value:"AD8CA7:Requiem.esp"},{path:"Item.Count",value:"1"}]}}   # REQ_LI_TreasureHunter_Draugr (theme-match)
]
```

CONT isn't merged, so build on the winner and keep its curated items; if the chest was overstuffed,
`ReplaceAll` with the curated set deliberately. Reference Requiem pools → `Requiem.esp` is mastered.

## Round-trip — reproduce a real placement

**Goal:** confirm the model by reproducing how Requiem placed an item, blind, from the comparable.

1. **Observed (live):** the steel sword (`013989`) sits in `REQ_LI_Loot_Weapon_Sword 016578` and
   `REQ_LI_Town_Weapon_Sword 0165BC`, every entry `Level = 1`, weighted by repetition (iron heavier than
   steel; Town carries only iron×2 + steel×1).
2. **Predicted, blind:** to place a *new* steel-tier sword, add one `Level = 1, Count = 1` entry to each
   of the same lists the steel comparable occupies, matching its repetition — no level gate, no new list
   invented.
3. **Result:** the predicted recipe (Example 1) reproduces the steel sword's own footprint exactly —
   same lists, same Level 1, same single-weight placement. The additive `Add` keeps Requiem's curated
   entries (verified by reading the patched list back: base 20 entries + 1 = 21, original entries intact),
   confirming the merge assumption (add, don't clobber) and the Level-1 + repetition tiering model.

**Conclusion:** placement is derived, not guessed — reverse-reference the comparable for the list set,
copy its repetition for the tier, add at Level 1, build on the winner, master Requiem.
