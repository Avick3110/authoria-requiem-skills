# houseCARL Authoring Recipes (ammo)

Copy-ready call shapes for emitting an ammo override. houseCARL always writes to a **new patch
plugin** (originals untouched); pass `into="<patch filename>"` to accumulate edits across calls.
All-or-nothing: a malformed op refuses the whole call and writes nothing — so read first, then
write.

> **Write gotchas:** you cannot write into an **active** patch (Mutagen self-locks
> it) — author into a fresh **inactive** patch, then move the `.esp` into place if consolidating. A
> brand-new never-enabled patch isn't in houseCARL's registry, so even `read_record
> plugin="<patch>.esp"` returns "does not touch" — the **`bulk_apply` per-op read-back is your only
> verification** until the patch is enabled + `housecarl_set_mo2_instance` refresh. Don't re-issue a
> Remove the active winner already applied; copy the winner and add only new deltas.

## A — Re-balance an existing modded arrow (the common case)

The modded arrow already exists with the mod's own (wrong-for-Requiem) stats. Override it. Example:
bring a modded steel-tier arrow `00080A:CoolArrows.esp` onto Requiem's steel arrow ladder
(Damage 50 / Value 1 / Weight 0).

```
housecarl_bulk_apply patch_name="Authoria_Ammo" operations=[
  {formid:"00080A:CoolArrows.esp", field_path:"Damage", value:"50"},
  {formid:"00080A:CoolArrows.esp", field_path:"Value",  value:"1"},
  {formid:"00080A:CoolArrows.esp", field_path:"Weight", value:"0"},
  {formid:"00080A:CoolArrows.esp", field_path:"Name",   value:"Steel Arrow"},
  # full steel-arrow keyword set: material + vendor + ammo-weight + AP-arrow-tier + RFTI exclusion
  {formid:"00080A:CoolArrows.esp", field_path:"Keywords", verb:"ReplaceAll",
   values:["01E719:Skyrim.esm","0917E7:Skyrim.esm","9D1F4A:Requiem.esp","0FD2AF:Requiem.esp","AD3B2D:Requiem.esp"]}
]
```

A single field tweak can use `set_field`:

```
housecarl_set_field formid="00080A:CoolArrows.esp" field_path="Damage" value="50" into="Authoria_Ammo"
```

For a **bolt**, use Damage = arrow × 1.2, `Flags` = 0, and the **Bolt** AP series
(`REQ_ArmorPiercingBolt_Tier<N>`). For **silver** ammo add `WeapMaterialSilver 10AA1A` +
`REQ_WeaponType_SilverArrow 0FAB11`. **Iron** ammo carries no AP keyword.

## B — Author a net-new arrow record

```
housecarl_create_record record_type="Ammunition" editorid="Authoria_Arrow_Steel" \
  into="Authoria_Ammo" operations=[
    {field_path:"Name", value:"Steel Arrow"},
    {field_path:"Damage", value:"50"},
    {field_path:"Value",  value:"1"},
    {field_path:"Weight", value:"0"},
    {field_path:"Flags",  value:"NonBolt"},                          # arrow; a bolt uses 0
    {field_path:"Projectile", value:"03BE11:Skyrim.esm"},            # reuse REQ_Projectile_Arrow_Iron
    {field_path:"Keywords", verb:"Add", value:"01E719:Skyrim.esm"},  # WeapMaterialSteel
    {field_path:"Keywords", verb:"Add", value:"0917E7:Skyrim.esm"},  # VendorItemArrow
    {field_path:"Keywords", verb:"Add", value:"9D1F4A:Requiem.esp"}, # REQ_AmmoWeight_Medium
    {field_path:"Keywords", verb:"Add", value:"0FD2AF:Requiem.esp"}, # REQ_ArmorPiercingArrow_Tier1
    {field_path:"Keywords", verb:"Add", value:"AD3B2D:Requiem.esp"}  # RFTI_Exclusions_NoDamageRescale
  ]
```

## C — Patch a modded projectile onto Requiem's profile

When the modded arrow ships its own PROJ (custom mesh) that still flies vanilla:

```
housecarl_bulk_apply into="Authoria_Ammo" operations=[
  {formid:"<modded arrow PROJ>", field_path:"Speed",   value:"3600"},   # bolt: 5600
  {formid:"<modded arrow PROJ>", field_path:"Gravity", value:"0.35"},
  {formid:"<modded arrow PROJ>", field_path:"Flags",   value:"CanBePickedUp"}  # bolt: CanBePickedUp, Supersonic
]
```

## D — Forge recipe (COBJ)

One ingot + one firewood → 30 ammo, gated by a cloned perk condition. Because the perk reference is
a form-index that may not render as a readable FormID, the robust path is to read the comparable
recipe and reproduce its `Conditions`; the skeleton is:

```
housecarl_create_record record_type="ConstructibleObject" \
  editorid="Authoria_Forge_Arrow_Steel" into="Authoria_Ammo" operations=[
    {field_path:"WorkbenchKeyword", value:"088105:Skyrim.esm"},       # forge
    {field_path:"CreatedObject", value:"<new arrow FormID>"},
    {field_path:"CreatedObjectCount", value:"30"},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"05ACE5:Skyrim.esm"},{path:"Item.Count", value:"1"}]}},  # Steel Ingot
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"06F993:Skyrim.esm"},{path:"Item.Count", value:"1"}]}}   # Firewood
    # then add the HasPerk condition cloned from the comparable recipe's Conditions[0]
  ]
```

Bespoke/quest/creature ammo gets **no** forge recipe.

## Masters

Ammo patches reference Requiem-defined keywords (`REQ_AmmoWeight_*`, `REQ_ArmorPiercing*_Tier*`,
`RFTI_Exclusions_NoDamageRescale`), so `Requiem.esp` is pulled in as a master automatically. If a
patch somehow references only vanilla forms (an iron arrow with no Requiem keyword — rare, since
iron still carries the ammo-weight + RFTI keywords), add `Requiem.esp` as a master manually.

## Verify the write

```
housecarl_read_record formid="<ammo>" conflict_tree=true
```

The new patch should appear last in the chain as the winner with your derived values. If the patch
is inactive, read it explicitly with `plugin="<patch>.esp"` instead of trusting the winner read.
