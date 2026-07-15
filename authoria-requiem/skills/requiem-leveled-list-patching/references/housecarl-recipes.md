# houseCARL recipes — copy-ready call shapes for placement

Real houseCARL call shapes for adding to leveled lists, spawn lists, and containers, plus masters and
verification. Write into one accumulating patch via `into="Requiem leveled list patching"`. All edits go
to a new patch plugin; originals are never touched. The patch is later run through the Reqtificator.

## Contents

- Coverage audit (whole-plugin bulk pass)
- A — Reverse-reference: find where the comparable is placed
- B — Read a list's tier weighting
- C — Add an item to a leveled list (LVLI)
- D — Add an NPC to a spawn list (LVLN)
- E — Weight a tier by repeating the entry
- F — Fill / extend a container (CONT)
- G — Encounter zone (rare)
- H — Masters & REQ_NULL hygiene
- I — Verify the read-back

## Coverage audit (whole-plugin bulk pass)

Before any per-record work on a whole-plugin job, sweep each type against the **mod's own** records so
the enumeration becomes your work queue (full doctrine: the skill body's *Bulk pass protocol*). None of
these three calls writes.

```
# One sweep per leveled-list type — the mod's own records ARE the work queue.
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="LeveledItem"
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="LeveledNpc"
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="LeveledSpell"
```

For each enumerated list, read its entries and check the `Level` field — any entry `> 1` is a
vanilla-style level gate to flatten (Requiem's model is `Level = 1` everywhere; see `list-structure.md`):

```
housecarl_read_record formid="<list>" fields=["Entries"] depth=2
```

De-level the mod's OWN gated lists by hand — they escape the merge (`baseVersion == null`) and ship
verbatim. Rewrite each `Level > 1` entry to `Level = 1`:

```
housecarl_set_field formid="<mod's own list>" into="Requiem leveled list patching" \
  field_path="Entries[0].Data.Level" value="1"     # flatten each gated entry
```

An **override of a Requiem-defined** list rides the merge instead — patch it add-only (§C/§D below),
never hand-flatten it. Tell them apart by whether the list's winner chain carries a `Requiem.esp`
version (Requiem-defined = add-only; only in `<NewMod>.esp` = the mod's own = hand-de-level).

Give every FormID from the three sweeps a disposition — **de-levelled** (mod's own gated list
flattened), **placed-into** (your record added, §C/§D), or **skipped** (already flat / quest-fixed /
cosmetic, reason stated) — counting a record as done only when its per-record checklist passes,
verified per record and never extrapolated across a sublist tree, a same-prefix family, or a per-tier
variant. Close each type with a reconciliation count: de-levelled + placed + skipped = enumerated.

## A — Reverse-reference: where does Requiem place the comparable?

The placement plan = the set of lists the closest comparable already lives in.

```
housecarl_cross_plugin_query type="LeveledItem" references="<comparable item FormID>" plugins=["Requiem.esp"] limit=40
housecarl_cross_plugin_query type="LeveledNpc"  references="<comparable NPC  FormID>" plugins=["Requiem.esp"] limit=40
```

Ignore `REQ_NULL_*` and `*_Quality<N>_<size>_<dist>` results (retired lists / Reqtificator output seeds).

## B — Read a list's tier weighting

Confirm the comparable's repetition + that entries are Level 1:

```
housecarl_read_record formid="016578:Skyrim.esm" fields=["Entries"] depth=2
housecarl_read_record formid="016578:Skyrim.esm" \
  fields=["Entries[0].Data.Level","Entries[0].Data.Count","Entries[0].Data.Reference",
          "Entries[1].Data.Reference","Entries[2].Data.Reference"]
```

(Struct leaves need explicit scalar paths — `Entries[0].Data.Level`, not just `Entries[0].Data`.)

## C — Add an item to a leveled list (LVLI)

`Add` to the **winner** so the record is base + your entry (dual-correct; see `merge-behavior.md`):

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"016578:Skyrim.esm", field_path:"Entries", verb:"Add",        # REQ_LI_Loot_Weapon_Sword
   compose:{type:"LeveledItemEntry", sets:[
     {path:"Data.Level",     value:"1"},
     {path:"Data.Count",     value:"1"},
     {path:"Data.Reference", value:"<your item FormID>"}]}},
  {formid:"0165BC:Skyrim.esm", field_path:"Entries", verb:"Add",        # REQ_LI_Town_Weapon_Sword
   compose:{type:"LeveledItemEntry", sets:[
     {path:"Data.Level",     value:"1"},
     {path:"Data.Count",     value:"1"},
     {path:"Data.Reference", value:"<your item FormID>"}]}}
]
```

## D — Add an NPC to a spawn list (LVLN)

Identical shape; element type is `LeveledNpcEntry`, reference is the NPC FormID:

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"0B83C2:Skyrim.esm", field_path:"Entries", verb:"Add",       # LCharWolf
   compose:{type:"LeveledNpcEntry", sets:[
     {path:"Data.Level",     value:"1"},
     {path:"Data.Count",     value:"1"},
     {path:"Data.Reference", value:"<your NPC FormID>"}]}}
]
```

## E — Weight a tier by repeating the entry

To make a common item more likely, add the same entry more than once (each as its own op). Keep total
list size under 255 — add only the repetition the tier needs.

```
operations=[
  {formid:"016578:Skyrim.esm", field_path:"Entries", verb:"Add", compose:{type:"LeveledItemEntry", sets:[
     {path:"Data.Level",value:"1"},{path:"Data.Count",value:"1"},{path:"Data.Reference",value:"<item>"}]}},
  {formid:"016578:Skyrim.esm", field_path:"Entries", verb:"Add", compose:{type:"LeveledItemEntry", sets:[
     {path:"Data.Level",value:"1"},{path:"Data.Count",value:"1"},{path:"Data.Reference",value:"<item>"}]}}
]
```

## F — Fill / extend a container (CONT)

Containers reference themed leveled lists, not raw gear. `Add` to the winner (CONT isn't merged — keep
its curated `Items`):

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"<container FormID>", field_path:"Items", verb:"Add",
   compose:{type:"ContainerEntry", sets:[
     {path:"Item.Item",  value:"050EF1:Requiem.esp"},   # REQ_LI_LootMidLevelContent
     {path:"Item.Count", value:"1"}]}}
]
```

## G — Encounter zone (rare — usually skip)

Only when a modded zone's level range fights Requiem's flat design (see `containers-and-zones.md`):

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"<zone FormID>", field_path:"MinLevel", value:"0"},
  {formid:"<zone FormID>", field_path:"MaxLevel", value:"0"},
  {formid:"<zone FormID>", field_path:"Flags",    value:"NeverResets"}
]
```

## H — Masters & REQ_NULL hygiene

- The merge ignores any patch that doesn't master `Requiem.esp`. When all your edits touch only
  vanilla-defined lists with vanilla items, **reference a real Requiem form** to pull Requiem in — e.g.
  also add to a `REQ_LI_*` (Requiem-renamed) list, or include a Requiem-defined pool entry. houseCARL
  Add+Sorts masters from your references (the xEdit way).
- Never add an item/NPC to a `REQ_NULL_*` list and never introduce a `REQ_NULL_*` reference. Leave
  Requiem's own `REQ_NULL` entries inside live lists intact (additive patching emits only your entry, so
  there's nothing to strip). See the masters/`REQ_NULL` rule carried by the `requiem-patching` skill +
  `merge-behavior.md`.

## I — Verify the read-back

Every `bulk_apply` returns the patch path, `masters:`, and a per-op read-back. Confirm `Requiem.esp` is
in `masters:` and your entry is present. For the full list content — every entry, not just the ones you
added — pass `full_readback=true` on the write call (houseCARL 1.2.3+): it returns the ENTIRE written
record re-read from the patch file on disk, before the patch is even enabled.

Once the patch is enabled + sorted in MO2, you can also re-read it directly:

```
housecarl_read_record formid="016578:Skyrim.esm" plugin="Requiem leveled list patching.esp" fields=["Entries"] depth=2
```

(This `plugin=` read works only for a patch IN the load order; against a not-yet-enabled patch it
fails with a named "not in the load order" error. If the write reported success the entries landed —
never re-issue them: re-running list Adds duplicates entries.)
