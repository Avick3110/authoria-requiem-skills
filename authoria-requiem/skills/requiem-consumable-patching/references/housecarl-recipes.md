# houseCARL Authoring Recipes

Copy-ready call shapes for emitting a consumable override. houseCARL always writes to a **new
patch plugin** (originals untouched); pass `into="<patch filename>"` to accumulate edits into one
patch across calls. Every write resolves the record's load-order winner and overrides it.
All-or-nothing: a malformed op refuses the whole call and writes nothing — read first, then write.

## Coverage audit (whole-plugin bulk pass)

Before any per-record work on a whole-plugin job, sweep both types so the enumeration becomes the
work queue (full doctrine: the skill body's *Bulk pass protocol*). The calls write nothing.

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="ALCH" \
  fields=["Name","Value","Weight","Flags","Keywords"]
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="INGR" \
  fields=["Name","Value","Weight","Flags"]
```

Read each ALCH row's `Flags`/`Keywords` to classify it (potion/poison/oil, food, alcohol, drug —
or a script-carrier/prop skip); every INGR row goes to the ingredient lane. Give every FormID a
disposition — **patched** (its class checklist passed on that record) or **skipped** (reason
verified on that record). Close with a reconciliation count per type: patched + skipped =
enumerated.

## A — Rebalance a modded potion (keep the effect links)

The common potion case: effects already point at MGEFs Requiem overrides, so only the shell moves.
Zero a magnitude only when the comparable shows the REQ MGEF ignores it (fixed-behaviour effects).

```
housecarl_bulk_apply patch_name="Authoria_Consumables" operations=[
  {formid:"0E46A5:SomeMod.esp", field_path:"Flags", value:"NoAutoCalc, Medicine"},
  {formid:"0E46A5:SomeMod.esp", field_path:"Value", value:"70"},
  {formid:"0E46A5:SomeMod.esp", field_path:"Weight", value:"0.5"},
  {formid:"0E46A5:SomeMod.esp", field_path:"Effects[0].Data.Magnitude", value:"0"},
  {formid:"0E46A5:SomeMod.esp", field_path:"Keywords", verb:"ReplaceAll",
   values:["08CDEC:Skyrim.esm"]}   # VendorItemPotion (poisons/oils: 08CDED)
]
```

## B — Rebuild a modded food's effect list (the food kit)

Replace the whole list, then compose each kit member as an `Effect` (BaseEffect + Data). Order:
themed fortifies → hunger tag → nutrition tag → any preserved animation marker (re-add it — a
`ReplaceAll` with `composes=[]` clears the list first).

```
housecarl_set_field formid="0013DB:SomeMod.esp" field_path="Effects" verb="ReplaceAll" \
  into="Authoria_Consumables" composes=[
    {type:"Effect", sets:[{path:"BaseEffect", value:"000A12:Requiem - Food and Beverages Redone.esp"},
       {path:"Data.Magnitude", value:"25"}, {path:"Data.Duration", value:"900"}, {path:"Data.Area", value:"0"}]},
    {type:"Effect", sets:[{path:"BaseEffect", value:"00080D:Requiem - Food and Beverages Redone.esp"},
       {path:"Data.Magnitude", value:"15"}, {path:"Data.Duration", value:"900"}, {path:"Data.Area", value:"0"}]},
    {type:"Effect", sets:[{path:"BaseEffect", value:"002EE4:Update.esm"},
       {path:"Data.Magnitude", value:"0"}, {path:"Data.Duration", value:"0"}, {path:"Data.Area", value:"0"}]},
    {type:"Effect", sets:[{path:"BaseEffect", value:"000A36:Requiem - Food and Beverages Redone.esp"},
       {path:"Data.Magnitude", value:"10"}, {path:"Data.Duration", value:"0"}, {path:"Data.Area", value:"0"}]}
  ]
housecarl_bulk_apply into="Authoria_Consumables" operations=[
  {formid:"0013DB:SomeMod.esp", field_path:"Flags", value:"NoAutoCalc, FoodItem"},
  {formid:"0013DB:SomeMod.esp", field_path:"Value", value:"30"},
  {formid:"0013DB:SomeMod.esp", field_path:"Keywords", verb:"Add", value:"08CDEA:Skyrim.esm"}
]
```

The alcohol kit is the same shape with the `REQ_Alcohol_*` pair (`18ED7C:Requiem.esp` +
`AE3729:Requiem.esp`), the Small hunger tag, and `REQ_Apo_AlcoholNutrition 000A37:…FaB.esp`.

## C — Ingredient: the full payload

```
housecarl_bulk_apply into="Authoria_Consumables" operations=[
  {formid:"001234:SomeMod.esp", field_path:"Flags", value:"NoAutoCalculation"},
  {formid:"001234:SomeMod.esp", field_path:"Value", value:"25"},
  {formid:"001234:SomeMod.esp", field_path:"IngredientValue", value:"25"},
  {formid:"001234:SomeMod.esp", field_path:"Weight", value:"0.25"},
  {formid:"001234:SomeMod.esp", field_path:"Keywords", verb:"ReplaceAll", values:["08CDEB:Skyrim.esm"]},
  {formid:"001234:SomeMod.esp", field_path:"Effects", verb:"ReplaceAll", composes:[
    {type:"Effect", sets:[{path:"BaseEffect", value:"03EB15:Skyrim.esm"},
       {path:"Data.Magnitude", value:"1"}, {path:"Data.Duration", value:"20"}, {path:"Data.Area", value:"0"}]},
    {type:"Effect", sets:[{path:"BaseEffect", value:"03EAF3:Skyrim.esm"},
       {path:"Data.Magnitude", value:"8"}, {path:"Data.Duration", value:"300"}, {path:"Data.Area", value:"0"}]},
    {type:"Effect", sets:[{path:"BaseEffect", value:"073F2C:Skyrim.esm"},
       {path:"Data.Magnitude", value:"16"}, {path:"Data.Duration", value:"60"}, {path:"Data.Area", value:"0"}]},
    {type:"Effect", sets:[{path:"BaseEffect", value:"10DE5F:Skyrim.esm"},
       {path:"Data.Magnitude", value:"2"}, {path:"Data.Duration", value:"15"}, {path:"Data.Area", value:"0"}]}
  ]}
]
```

Exactly four effects; `Effects[0]` is the eat-to-learn slot; durations are the per-MGEF standards
(`references/ingredients.md`).

## D — Crafting recipe (COBJ) at the cookpot

Clone the gate from the comparable recipe's `Conditions` read. The condition grammar: add the
`ConditionFloat` shell, then Set its polymorphic `Data` arm via compose — a direct leaf set like
`Conditions[0].Data.Perk` is refused by design (the whole `Data` arm must be composed). Read the
comparable's conditions first and mirror the arm types it renders.

```
housecarl_create_record record_type="ConstructibleObject" \
  editorid="Authoria_Cook_Potion_MyTonic" into="Authoria_Consumables" operations=[
    {field_path:"WorkbenchKeyword", value:"0A5CB3:Skyrim.esm"},   # CraftingCookpot — Requiem's consumable station
    {field_path:"CreatedObject", value:"<the consumable's FormID>"},
    {field_path:"CreatedObjectCount", value:"1"},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"<reagent FormID>"},{path:"Item.Count", value:"1"}]}},
    # perk gate (Requiem craft-consumable lane; OR-flag it against the racial keyword arm the
    # comparable shows, and add the Alchemy skill-band conditions the comparable carries):
    {field_path:"Conditions", verb:"Add", compose:{type:"ConditionFloat",
       fields:{CompareOperator:"EqualTo", ComparisonValue:"1"}}},
    {field_path:"Conditions[0].Data", verb:"Set", compose:{type:"HasPerkConditionData",
       sets:[{path:"Perk", value:"0BE127:Skyrim.esm"}]}}   # REQ_Alchemy_AlchemicalLore1
  ]
```

A plain cooked-food recipe (raw meat → cooked dish) conventionally ships **ungated** — omit the
Conditions entirely and note which lane's convention you followed.

## Verify the write

**Before the patch is enabled in MO2**, the write call itself is the verification: the per-op
read-back confirms each edited value, and `full_readback=true` on the write call returns the
ENTIRE written record re-read from the patch file on disk — confirm composed structures (the
effect list, the recipe's Items/Conditions) there. A `housecarl_read_record plugin="<patch>.esp"`
does NOT work on a not-yet-enabled patch; if the write reported success the edits landed — never
re-issue them.

**After enabling + sorting in MO2:**

```
housecarl_read_record formid="<consumable>" conflict_tree=true
```

The new patch should appear last in the chain as the winner, with your derived values — and
remember: for consumables that winner is final; no Reqtificator pass follows.
