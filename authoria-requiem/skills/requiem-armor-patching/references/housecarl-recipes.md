# houseCARL Authoring Recipes

Copy-ready call shapes for emitting an armor override. houseCARL always writes to a **new patch
plugin** (originals untouched); pass `into="<patch filename>"` to accumulate edits into one patch
across calls. Every write resolves the record's load-order winner and overrides it. All-or-nothing:
a malformed op refuses the whole call and writes nothing — so read first, then write.

> **Write gotchas:** a freshly written patch isn't auto-enabled, and houseCARL reads load-order
> truth only — so until you enable + sort it in MO2, the WRITE CALL is the verification: every
> write returns a per-op read-back, and `full_readback=true` (houseCARL 1.2.3+) returns the entire
> written record. A `read_record plugin="<patch>.esp"` against a not-yet-enabled patch fails with
> a named "not in the load order" error — if the write reported success the edits DID land; never
> re-issue them (re-running list Adds duplicates entries). Don't re-issue a Remove the active
> winner already applied (Remove-by-index throws on an empty list); copy the winner and add only
> new deltas. (Writing into a patch that is already ACTIVE in the load order works — the old
> self-lock was fixed in houseCARL 1.2.1.)

## Coverage audit (whole-plugin bulk pass)

Before any per-record work on a whole-plugin job, sweep the type in one read so the enumeration
becomes your work queue (full doctrine: the skill body's *Bulk pass protocol*). The call doesn't write.

```
# The triage matrix: the disposition fields for every ARMO of the plugin in one table.
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="ARMO" \
  fields=["Name","ArmorRating","BodyTemplate.ArmorType","BodyTemplate.FirstPersonFlags","Keywords","ObjectEffect"]
```

Per row: `BodyTemplate.ArmorType` (armored vs `Clothing` vs none → armored workflow / clothing frame /
skin-or-template skip), `ArmorRating` (>0 armored vs 0 cosmetic), `FirstPersonFlags` + part keyword
(which slot → which comparable; flags an off-slot 46/47 accessory), and `ObjectEffect` (non-null →
enchanted: link the frame here, route the effect design to the `requiem-magic-patching` skill).

Give every FormID from the enumeration a disposition — **patched** (which part/material/weight
workflow below) or **skipped** with the reason named (non-playable/template ARMO, skin/naked-body
ARMO, creature-skin ARMO, or already-Requiem-consistent). **Patched counts only when the skill's
per-record Checklist passes for that piece** — field-complete, not merely touched. Verify each
disposition on *that* record; never extrapolate across a set, a light/heavy pair, a material tier, or
an enchanted variant (a sibling can carry its own off-ladder AR, an off-slot biped tag, or a bespoke
`ObjectEffect`). Close with a reconciliation count: patched + skipped = enumerated.

## A — Re-balance an existing modded armor (the common case)

The modded armor already exists in some plugin with the mod's own (wrong-for-Requiem) stats.
Override it with `bulk_apply`. Example: bring a modded steel-tier cuirass `00134A:CoolArmor.esp`
onto Requiem's steel heavy ladder (cuirass AR 300 / Val 250 / Wt 25).

```
housecarl_bulk_apply patch_name="Authoria_Armor" operations=[
  {formid:"00134A:CoolArmor.esp", field_path:"ArmorRating", value:"300"},
  {formid:"00134A:CoolArmor.esp", field_path:"Value",  value:"250"},
  {formid:"00134A:CoolArmor.esp", field_path:"Weight", value:"25"},
  {formid:"00134A:CoolArmor.esp", field_path:"Name", value:"Steel Cuirass"},
  # keywords: replace with Requiem's heavy-cuirass set (type + set + part + vendor)
  {formid:"00134A:CoolArmor.esp", field_path:"Keywords", verb:"ReplaceAll",
   values:["06BBD2:Skyrim.esm","06BBE6:Skyrim.esm","06C0EC:Skyrim.esm","08F959:Skyrim.esm"]}
]
```

A single field tweak can use `set_field` instead:

```
housecarl_set_field formid="00134A:CoolArmor.esp" field_path="ArmorRating" value="300" \
  into="Authoria_Armor"
```

**Do not** add a ranged-resistance or tempering keyword — those are Reqtificator outputs. For a
**heavy gauntlet**, add the fist perk: append `PerkFists<Material>` to the keyword list. For a
**shield**, use the shield part keyword `0965B2` (not the cuirass part) and copy
`BashImpactDataSet` + `AlternateBlockMaterial`.

## B — Author a net-new armor record

```
housecarl_create_record record_type="Armor" editorid="Authoria_Armor_Heavy_Steel_Body" \
  into="Authoria_Armor" operations=[
    {field_path:"Name", value:"Steel Cuirass"},
    {field_path:"ArmorRating", value:"300"},
    {field_path:"Value",  value:"250"},
    {field_path:"Weight", value:"25"},
    {field_path:"BodyTemplate.ArmorType", value:"HeavyArmor"},
    {field_path:"BodyTemplate.FirstPersonFlags", value:"Body"},
    {field_path:"Keywords", verb:"Add", value:"06BBD2:Skyrim.esm"},   # armorType heavy
    {field_path:"Keywords", verb:"Add", value:"06BBE6:Skyrim.esm"},   # armorSet steel
    {field_path:"Keywords", verb:"Add", value:"06C0EC:Skyrim.esm"},   # armorPart cuirass
    {field_path:"Keywords", verb:"Add", value:"08F959:Skyrim.esm"}    # VendorItemArmor
  ]
```

For a **heavy gauntlet** add `PerkFists<Material>`; for a **shield** swap the part keyword to
`0965B2` and set `BashImpactDataSet` + `AlternateBlockMaterial`.

## C — Heavy gauntlet (fist perk)

```
housecarl_bulk_apply into="Authoria_Armor" operations=[
  {formid:"<gauntlets>", field_path:"Keywords", verb:"ReplaceAll",
   values:["06BBD2:Skyrim.esm","06BBD8:Skyrim.esm","06C0EF:Skyrim.esm","08F959:Skyrim.esm",
           "02C178:Skyrim.esm"]}     # last = PerkFistsEbony (read the comparable's PerkFists)
]
```

## D — Shield (block / bash links)

```
housecarl_bulk_apply into="Authoria_Armor" operations=[
  {formid:"<shield>", field_path:"BashImpactDataSet",      value:"0183FE:Skyrim.esm"},
  {formid:"<shield>", field_path:"AlternateBlockMaterial", value:"016979:Skyrim.esm"},
  {formid:"<shield>", field_path:"Keywords", verb:"ReplaceAll",
   values:["06BBD2:Skyrim.esm","06BBD4:Skyrim.esm","08F959:Skyrim.esm","0965B2:Skyrim.esm"]}
]
```

## E — Crafting + tempering recipes (COBJ)

Create the forge recipe with its perk gate composed in the same call. Read the comparable recipe's
`Conditions` first (houseCARL 1.2.2+ renders the perk parameter as a readable FormID) and reuse its
perk. The condition grammar: add the `ConditionFloat` shell, then Set its polymorphic `Data` arm via
compose — order matters, and a direct leaf set like `Conditions[0].Data.Perk` is refused by design
(the whole `Data` arm must be composed):

```
housecarl_create_record record_type="ConstructibleObject" \
  editorid="Authoria_Forge_Heavy_Steel_Body" into="Authoria_Armor" operations=[
    {field_path:"WorkbenchKeyword", value:"088105:Skyrim.esm"},          # forge
    {field_path:"CreatedObject", value:"<new armor FormID>"},
    {field_path:"CreatedObjectCount", value:"1"},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"05ACE4:Skyrim.esm"},{path:"Item.Count", value:"2"}]}},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"05ACE5:Skyrim.esm"},{path:"Item.Count", value:"5"}]}},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"0800E4:Skyrim.esm"},{path:"Item.Count", value:"4"}]}},
    # the HasPerk gate, cloned from the comparable recipe's Conditions[0]:
    {field_path:"Conditions", verb:"Add", compose:{type:"ConditionFloat",
       fields:{CompareOperator:"EqualTo", ComparisonValue:"1"}}},
    {field_path:"Conditions[0].Data", verb:"Set", compose:{type:"HasPerkConditionData",
       sets:[{path:"Perk", value:"<the comparable's smithing perk, e.g. 0CB40D:Skyrim.esm>"}]}}
  ]
```

The tempering recipe is the same with `WorkbenchKeyword = 0ADB78:Skyrim.esm` (**armor workbench**,
not the grindstone) and a single ingot.

## Masters

Armor patches reference Requiem-defined keywords (fur/dwarven-light/chainmail set keywords, plus
the resist/tempering keywords the Reqtificator assigns), so `Requiem.esp` is pulled in as a master
automatically when any such keyword is referenced. If a given patch happens to reference only
vanilla keywords (e.g. a plain iron piece), add `Requiem.esp` as a master manually so the
Reqtificator and any Requiem-keyword edits resolve.

## Verify the write

**Before the patch is enabled in MO2**, the write call itself is the verification: the per-op
read-back confirms each edited value, and `full_readback=true` on the write call (houseCARL
1.2.3+) returns the ENTIRE written record — every field, deep, re-read from the patch file on
disk — so you can confirm composed structures (Items entries, the perk-gate condition) and that
nothing else in the record was disturbed. A `read_record plugin="<patch>.esp"` does NOT work on a
not-yet-enabled patch (it fails with a named "not in the load order" error); if the write reported
success the edits landed — never re-issue them.

**After enabling + sorting in MO2:**

```
housecarl_read_record formid="<armor>" conflict_tree=true
```

The new patch should appear last in the chain as the winner, with your derived values.
