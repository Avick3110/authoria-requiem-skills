# houseCARL Authoring Recipes (ammo)

Copy-ready call shapes for emitting an ammo override. houseCARL always writes to a **new patch
plugin** (originals untouched); pass `into="<patch filename>"` to accumulate edits across calls.
All-or-nothing: a malformed op refuses the whole call and writes nothing — so read first, then
write.

> **Write gotchas:** a freshly written patch isn't auto-enabled, and houseCARL reads load-order
> truth only — until you enable + sort it in MO2, the WRITE CALL is the verification: the per-op
> read-back confirms each edit, and `full_readback=true` (houseCARL 1.2.3+) returns the entire
> written record. A `read_record plugin="<patch>.esp"` against a not-yet-enabled patch fails with
> a named "not in the load order" error — if the write reported success the edits DID land; never
> re-issue them (re-running list Adds duplicates entries). Don't re-issue a Remove the active
> winner already applied; copy the winner and add only new deltas. (Writing into a patch that is
> already ACTIVE in the load order works — the old self-lock was fixed in houseCARL 1.2.1.)

## Coverage audit (whole-plugin bulk pass)

Before any per-record work on a whole-plugin job, sweep the type in one read so the enumeration
becomes your work queue (full doctrine: the skill body's *Bulk pass protocol*). The call writes
nothing.

```
# the triage matrix: every AMMO's disposition fields in one table.
#   Flags → arrow (NonBolt) vs bolt (0) vs creature/trap (NonPlayable);
#   Damage/Value/Weight → the ladder read; Keywords → what's already stamped;
#   Projectile → the linked PROJ, which rides this sweep (no separate PROJ enumeration).
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="AMMO" \
  fields=["Flags","Damage","Value","Weight","Keywords","Projectile"]
```

Give every FormID a disposition — **patched** (normal-tiered → the skill's Workflow; elemental /
quest-unique → tiered frame with the effect on the PROJ; creature/trap `NonPlayable` → no recipe;
bound → the `requiem-magic-patching` skill) or **skipped** (a named reason). A record counts as
patched only when the skill body's `## Checklist` passes for it — field-complete, not merely touched.
Patch or verify each patched AMMO's linked PROJ in the same disposition; a skipped AMMO's PROJ is a
conscious skip, not an accident. Never extrapolate across a same-material arrow/bolt pair or along the
material-tier line — read each AMMO's own stats. Close with a reconciliation count: patched + skipped
= enumerated.

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

One ingot + one firewood → 30 ammo, gated by a perk condition cloned from the comparable. Read the
comparable recipe's `Conditions` first (houseCARL 1.2.2+ renders the perk parameter as a readable
FormID) and reuse its perk. The condition grammar: add the `ConditionFloat` shell, then Set its
polymorphic `Data` arm via compose — order matters, and a direct leaf set like
`Conditions[0].Data.Perk` is refused by design (the whole `Data` arm must be composed):

```
housecarl_create_record record_type="ConstructibleObject" \
  editorid="Authoria_Forge_Arrow_Steel" into="Authoria_Ammo" operations=[
    {field_path:"WorkbenchKeyword", value:"088105:Skyrim.esm"},       # forge
    {field_path:"CreatedObject", value:"<new arrow FormID>"},
    {field_path:"CreatedObjectCount", value:"30"},
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"05ACE5:Skyrim.esm"},{path:"Item.Count", value:"1"}]}},  # Steel Ingot
    {field_path:"Items", verb:"Add", compose:{type:"ContainerEntry",
       sets:[{path:"Item.Item", value:"06F993:Skyrim.esm"},{path:"Item.Count", value:"1"}]}},  # Firewood
    # the HasPerk gate, cloned from the comparable recipe's Conditions[0]:
    {field_path:"Conditions", verb:"Add", compose:{type:"ConditionFloat",
       fields:{CompareOperator:"EqualTo", ComparisonValue:"1"}}},
    {field_path:"Conditions[0].Data", verb:"Set", compose:{type:"HasPerkConditionData",
       sets:[{path:"Perk", value:"<the comparable's smithing perk, e.g. 0CB40D:Skyrim.esm>"}]}}
  ]
```

Bespoke/quest/creature ammo gets **no** forge recipe.

## Masters

Ammo patches reference Requiem-defined keywords (`REQ_AmmoWeight_*`, `REQ_ArmorPiercing*_Tier*`,
`RFTI_Exclusions_NoDamageRescale`), so `Requiem.esp` is pulled in as a master automatically. If a
patch somehow references only vanilla forms (an iron arrow with no Requiem keyword — rare, since
iron still carries the ammo-weight + RFTI keywords), add `Requiem.esp` as a master manually.

## Verify the write

**Before the patch is enabled in MO2**, the write call itself is the verification: the per-op
read-back confirms each edited value, and `full_readback=true` on the write call (houseCARL
1.2.3+) returns the ENTIRE written record re-read from the patch file on disk — confirm the Items
entries and the perk-gate condition there. A `read_record plugin="<patch>.esp"` does NOT work on a
not-yet-enabled patch (named "not in the load order" error); if the write reported success the
edits landed — never re-issue them.

**After enabling + sorting in MO2:**

```
housecarl_read_record formid="<ammo>" conflict_tree=true
```

The new patch should appear last in the chain as the winner with your derived values.
