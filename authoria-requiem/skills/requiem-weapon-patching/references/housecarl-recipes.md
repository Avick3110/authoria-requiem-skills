# houseCARL Authoring Recipes

Copy-ready call shapes for emitting a weapon override. houseCARL always writes to a **new patch
plugin** (originals untouched); pass `into="<patch filename>"` to accumulate edits into one patch
across calls. Every write resolves the record's load-order winner and overrides it. All-or-nothing:
a malformed op refuses the whole call and writes nothing — so read first, then write.

## A — Re-balance an existing modded weapon (the common case)

The modded weapon already exists in some plugin, with the mod's own (wrong-for-Requiem) stats.
Override it with `bulk_apply`, setting each field to the value you derived from the comparable.
Example: bring a modded steel-tier sword `00134A:CoolSwords.esp` onto Requiem's steel ladder.

```
housecarl_bulk_apply patch_name="Authoria_Weapons" operations=[
  {formid:"00134A:CoolSwords.esp", field_path:"BasicStats.Damage", value:"8"},
  {formid:"00134A:CoolSwords.esp", field_path:"BasicStats.Value",  value:"45"},
  {formid:"00134A:CoolSwords.esp", field_path:"BasicStats.Weight", value:"10"},
  {formid:"00134A:CoolSwords.esp", field_path:"Critical.Damage",   value:"4"},
  {formid:"00134A:CoolSwords.esp", field_path:"Critical.PercentMult", value:"1"},
  {formid:"00134A:CoolSwords.esp", field_path:"Data.Speed", value:"1"},
  {formid:"00134A:CoolSwords.esp", field_path:"Data.Reach", value:"1"},
  {formid:"00134A:CoolSwords.esp", field_path:"Name", value:"Steel Longsword"},
  # keywords: replace the whole list with Requiem's melee set (material + type + vendor)
  {formid:"00134A:CoolSwords.esp", field_path:"Keywords", verb:"ReplaceAll",
   values:["01E719:Skyrim.esm","01E711:Skyrim.esm","08F958:Skyrim.esm"]},
  # sound/impact links copied from the Steel Sword comparable
  {formid:"00134A:CoolSwords.esp", field_path:"ImpactDataSet", value:"013CAC:Skyrim.esm"},
  {formid:"00134A:CoolSwords.esp", field_path:"EquipSound",    value:"03C72E:Skyrim.esm"}
]
```

A single keyword tweak can use `set_field` instead:

```
housecarl_set_field formid="00134A:CoolSwords.esp" field_path="Keywords" verb="Add" \
  value="01E719:Skyrim.esm" into="Authoria_Weapons"
```

## B — Author a net-new weapon record

For a weapon that doesn't exist yet, `create_record` allocates a fresh FormID (in the patch's
0x800+ range) and returns it.

```
housecarl_create_record record_type="Weapon" editorid="Authoria_Weapon_Steel_Longsword" \
  into="Authoria_Weapons" operations=[
    {field_path:"Name", value:"Steel Longsword"},
    {field_path:"BasicStats.Damage", value:"8"},
    {field_path:"BasicStats.Value",  value:"45"},
    {field_path:"BasicStats.Weight", value:"10"},
    {field_path:"Critical.Damage", value:"4"},
    {field_path:"Critical.PercentMult", value:"1"},
    {field_path:"Data.Speed", value:"1"},
    {field_path:"Data.Reach", value:"1"},
    {field_path:"Data.AnimationType", value:"OneHandSword"},
    {field_path:"Keywords", verb:"Add", value:"01E719:Skyrim.esm"},
    {field_path:"Keywords", verb:"Add", value:"01E711:Skyrim.esm"},
    {field_path:"Keywords", verb:"Add", value:"08F958:Skyrim.esm"},
    {field_path:"ImpactDataSet", value:"013CAC:Skyrim.esm"},
    {field_path:"EquipSound", value:"03C72E:Skyrim.esm"}
  ]
```

## C — Enchanted variant

Set `Template` → the plain base weapon, `ObjectEffect` → the enchantment, and `EnchantmentAmount`
→ the charge pool (≈ 500 × tier). Keep base stats/keywords/value.

```
housecarl_bulk_apply into="Authoria_Weapons" operations=[
  {formid:"<enchanted weapon>", field_path:"Template",     value:"<plain base FormID>"},
  {formid:"<enchanted weapon>", field_path:"ObjectEffect", value:"045C2A:Skyrim.esm"},
  {formid:"<enchanted weapon>", field_path:"EnchantmentAmount", value:"1000"}
]
```

## D — Bow frame extras

```
housecarl_bulk_apply into="Authoria_Weapons" operations=[
  {formid:"<bow>", field_path:"Data.Flags", value:"NPCsUseAmmo"},
  {formid:"<bow>", field_path:"Keywords", verb:"ReplaceAll",
   values:["01E715:Skyrim.esm","01E719:Skyrim.esm","08F958:Skyrim.esm",
           "2E232E:Requiem.esp","9F9915:Requiem.esp","AD3B2D:Requiem.esp","AD3B2F:Requiem.esp"]}
]
```

## E — Crafting recipe (COBJ)

Create the forge recipe, then clone the comparable's `Conditions` (perk gate) onto it. Because the
perk reference is a form-index that may not render as a readable FormID, the robust path is to read
the comparable recipe and reproduce its condition list; the skeleton is:

```
housecarl_create_record record_type="ConstructibleObject" \
  editorid="Authoria_Forge_Weapon_Steel_Longsword" into="Authoria_Weapons" operations=[
    {field_path:"WorkbenchKeyword", value:"088105:Skyrim.esm"},          # forge
    {field_path:"CreatedObject", value:"<new weapon FormID>"},
    {field_path:"CreatedObjectCount", value:"1"},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"05ACE5:Skyrim.esm"},{path:"Item.Count", value:"2"}]}},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"0800E4:Skyrim.esm"},{path:"Item.Count", value:"1"}]}}
    # then add the HasPerk condition cloned from the comparable recipe's Conditions[0]
  ]
```

The tempering recipe is the same with `WorkbenchKeyword` = `088108:Skyrim.esm` and a single ingot.

## Verify the write

Read the record back (the write tools also return a read-back). To confirm a full override:

```
housecarl_read_record formid="<weapon>" conflict_tree=true
```

The new patch should appear last in the chain as the winner, with your derived values.
