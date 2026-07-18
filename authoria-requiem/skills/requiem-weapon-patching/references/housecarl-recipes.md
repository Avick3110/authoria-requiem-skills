# houseCARL Authoring Recipes

Copy-ready call shapes for emitting a weapon override. houseCARL always writes to a **new patch
plugin** (originals untouched); pass `into="<patch filename>"` to accumulate edits into one patch
across calls. Every write resolves the record's load-order winner and overrides it. All-or-nothing:
a malformed op refuses the whole call and writes nothing — so read first, then write.

## Coverage audit (whole-plugin bulk pass)

Before any per-record work on a whole-plugin job, sweep the type in one read so the enumeration
becomes your work queue (full doctrine: the skill body's *Bulk pass protocol*). The call writes nothing.

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="WEAP" \
  fields=["Name","Data.AnimationType","Data.Flags","Keywords","BasicStats.Damage","ObjectEffect","Template"]
```

Read each row to disposition it: `Data.AnimationType` picks the workflow branch (plain melee,
bow/crossbow, staff), `ObjectEffect` flags an enchanted variant, `Template` marks a variant vs a base
record, and `Data.Flags` separates playable loot from NPC-only/non-playable weapons.

Give every FormID a disposition — **patched** (routed to a workflow branch, and counting as patched
only once the skill's per-record Checklist passes for it) or **skipped** (non-playable/NPC-only,
trap/prop, unarmed placeholder, or template-only base record — the reason verified on *that* record,
never extrapolated across a same-material / same-type / same-prefix / same-base family). Close with a
reconciliation count: patched + skipped = enumerated.

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

Create the forge recipe with its perk gate composed in the same call. Read the comparable recipe's
`Conditions` first (houseCARL 1.2.2+ renders the perk parameter as a readable FormID) and reuse its
perk. The condition grammar: add the `ConditionFloat` shell, then Set its polymorphic `Data` arm via
compose — order matters, and a direct leaf set like `Conditions[0].Data.Perk` is refused by design
(the whole `Data` arm must be composed):

```
housecarl_create_record record_type="ConstructibleObject" \
  editorid="Authoria_Forge_Weapon_Steel_Longsword" into="Authoria_Weapons" operations=[
    {field_path:"WorkbenchKeyword", value:"088105:Skyrim.esm"},          # forge
    {field_path:"CreatedObject", value:"<new weapon FormID>"},
    {field_path:"CreatedObjectCount", value:"1"},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"05ACE5:Skyrim.esm"},{path:"Item.Count", value:"2"}]}},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"0800E4:Skyrim.esm"},{path:"Item.Count", value:"1"}]}},
    # the HasPerk gate, cloned from the comparable recipe's Conditions[0]:
    {field_path:"Conditions", verb:"Add", compose:{type:"ConditionFloat",
       fields:{CompareOperator:"EqualTo", ComparisonValue:"1"}}},
    {field_path:"Conditions[0].Data", verb:"Set", compose:{type:"HasPerkConditionData",
       sets:[{path:"Perk", value:"<the comparable's smithing perk, e.g. 0CB40D:Skyrim.esm>"}]}}
  ]
```

The tempering recipe is the same with `WorkbenchKeyword` = `088108:Skyrim.esm` and a single ingot.

### Disable an existing recipe

```
housecarl_set_field formid="<recipe>" field_path="WorkbenchKeyword" \
  value="AD3B01:Requiem.esp" into="Authoria_Weapons"  # REQ_DisableRecipe
```

Use this instead of `Remove` or a null value. Requiem's disabled-recipe convention is an explicit
workbench keyword; the write read-back must resolve it as `REQ_DisableRecipe`.

## Verify the write

**Before the patch is enabled in MO2**, the write call itself is the verification: the per-op
read-back confirms each edited value, and `full_readback=true` on the write call (houseCARL
1.2.3+) returns the ENTIRE written record re-read from the patch file on disk — confirm composed
structures (Items entries, the perk-gate condition) there. A `read_record plugin="<patch>.esp"`
does NOT work on a not-yet-enabled patch (named "not in the load order" error); if the write
reported success the edits landed — never re-issue them.

**After enabling + sorting in MO2:**

```
housecarl_read_record formid="<weapon>" conflict_tree=true
```

The new patch should appear last in the chain as the winner, with your derived values.
