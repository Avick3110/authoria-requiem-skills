# houseCARL recipes — copy-ready call shapes for magic

Real houseCARL call shapes for finding, reading, and authoring MGEF / SPEL / ENCH / BOOK records. Write
into one accumulating patch via `into="Requiem magic patching"`. All edits go to a new patch plugin;
originals are never touched. The patch is later run through the Reqtificator.

## Contents

- A — Find the comparable (by school/tier/delivery)
- B — Read the comparable's cost, charge, effects, keywords, flags
- C — Read a comparable's MGEF (design + keyword vocab)
- D — Patch an existing modded spell (the common case)
- E — Compose a multi-effect spell's `Effects` list
- F — Create a new MGEF
- G — Patch an enchantment (weapon / apparel)
- H — Patch a spell tome (BOOK)
- I — Masters & REQ_NULL hygiene
- J — Verify the read-back

## A — Find the comparable

```
housecarl_cross_plugin_query type="Spell" plugins=["Requiem - Magic Redone.esp"] editorid_contains="Destruction2_Fire" limit=40
housecarl_cross_plugin_query type="ObjectEffect" plugins=["Requiem - Magic Redone.esp"] editorid_contains="REQ_Ench_Weapon_Fire" limit=40
```

Match school + tier + delivery (and element). Ignore `REQ_NULL_*` and leveled-stub results.

## B — Read the comparable's spell

```
housecarl_batch_record_detail formids=["012FD0:Skyrim.esm"] \
  fields=["Name","BaseCost","CastType","TargetType","ChargeTime","Flags","HalfCostPerk","Effects"] depth=3
```

Then read each effect's data (struct leaves need explicit scalar paths):

```
housecarl_read_record formid="012FD0:Skyrim.esm" \
  fields=["Effects[0].BaseEffect","Effects[0].Data.Magnitude","Effects[0].Data.Duration",
          "Effects[1].BaseEffect","Effects[1].Data.Magnitude","Effects[1].Data.Duration"] depth=3
```

## C — Read a comparable's MGEF

```
housecarl_batch_record_detail formids=["013CA9:Skyrim.esm"] \
  fields=["Archetype","MagicSkill","ResistValue","CastType","TargetType","Flags","Keywords",
          "Projectile","Explosion","HitShader","HitEffectArt","Sounds","PerkToApply","VirtualMachineAdapter"] depth=3
```

`Archetype.Type` + `Archetype.ActorValue` give the effect kind (ValueModifier/DualValueModifier/Cloak/…).
A non-null `VirtualMachineAdapter` means a `Nox_*` script → route to `requiem-script-patching`.

## D — Patch an existing modded spell (the common case)

Most magic work is rebalancing a mod's spell, not creating one. Set the four core fields from the
comparable — `BaseCost`, `ChargeTime`, `HalfCostPerk` (the school+tier classifier), and the effect
magnitudes:

```
housecarl_bulk_apply into="Requiem magic patching" operations=[
  {formid:"<spell FormID>", field_path:"BaseCost",      value:"90"},
  {formid:"<spell FormID>", field_path:"ChargeTime",    value:"0.5"},
  {formid:"<spell FormID>", field_path:"HalfCostPerk",  value:"0C44BF:Skyrim.esm"},   # Apprentice Destruction
  {formid:"<spell FormID>", field_path:"Flags",         value:"ManualCostCalc"},
  {formid:"<spell FormID>", field_path:"Effects[0].Data.Magnitude", value:"<tier magnitude>"}
]
```

(Re-pointing `HalfCostPerk` is how the MR-patch addons reclassify a spell's school+tier — see
`worked-examples.md`.)

## E — Compose a multi-effect spell's `Effects` list

When creating a spell or rebuilding its effect list, `Add` each `Effect` (element type `Effect`):

```
housecarl_bulk_apply into="Requiem magic patching" operations=[
  {formid:"<spell FormID>", field_path:"Effects", verb:"Add",
   compose:{type:"Effect", sets:[
     {path:"BaseEffect",     value:"013CAA:Skyrim.esm"},     # primary frost damage MGEF
     {path:"Data.Magnitude", value:"16"},
     {path:"Data.Area",      value:"0"},
     {path:"Data.Duration",  value:"0"}]}},
  {formid:"<spell FormID>", field_path:"Effects", verb:"Add",
   compose:{type:"Effect", sets:[
     {path:"BaseEffect",     value:"00607E:Requiem - Magic Redone.esp"},   # frost taper (MR-defined)
     {path:"Data.Magnitude", value:"0"},
     {path:"Data.Duration",  value:"3"}]}}
]
```

A per-effect perk gate is a `Conditions` entry on the effect (a `HasPerk` condition) — clone it from the
comparable rather than retyping the perk form-index.

## F — Create a new MGEF

Only when no Requiem MGEF fits (usually you reuse the comparable's). Catalog type `MagicEffect`:

```
housecarl_create_record record_type="MagicEffect" editorid="MyMod_Effect_FrostLance" into="Requiem magic patching" operations=[
  {field_path:"Name",      value:"Frost Lance"},
  {field_path:"MagicSkill",value:"Destruction"},
  {field_path:"ResistValue",value:"ResistFrost"},
  {field_path:"CastType",  value:"FireAndForget"},
  {field_path:"TargetType",value:"Aimed"},
  {field_path:"Flags",     value:"Hostile, Detrimental, FXPersist, NoDeathDispel, PowerAffectsMagnitude"},
  {field_path:"Archetype", compose:{type:"MagicEffectArchetype", sets:[
     {path:"Type",value:"ValueModifier"},{path:"ActorValue",value:"Health"}]}},
  {field_path:"Keywords",  verb:"Add", value:"01CEAE:Skyrim.esm"}     # MagicDamageFrost
]
```

The new FormID is returned; reference it from the spell's `Effects` via E. Prefer **reusing** the
comparable's MGEF (overridden if needed) over a net-new effect.

## G — Patch an enchantment (ENCH)

Weapon enchant (per-cast cost on the ENCH; charge pool is the WEAP's `EnchantmentAmount`, set by the
weapon skill):

```
housecarl_bulk_apply into="Requiem magic patching" operations=[
  {formid:"<ench FormID>", field_path:"EnchantType",     value:"Enchantment"},
  {formid:"<ench FormID>", field_path:"CastType",        value:"FireAndForget"},
  {formid:"<ench FormID>", field_path:"TargetType",      value:"Touch"},
  {formid:"<ench FormID>", field_path:"EnchantmentCost", value:"20"},                 # tier 2
  {formid:"<ench FormID>", field_path:"Effects[0].Data.Magnitude", value:"<tier magnitude>"}
]
```

Apparel enchant: `CastType=ConstantEffect`, `TargetType=Self`, no charge pool — magnitude on the effect.

## H — Patch a spell tome (BOOK)

```
housecarl_bulk_apply into="Requiem magic patching" operations=[
  {formid:"<tome FormID>", field_path:"Value",  value:"600"},          # Adept tier
  {formid:"<tome FormID>", field_path:"Weight", value:"1"},
  {formid:"<tome FormID>", field_path:"Teaches", compose:{type:"BookSpell", sets:[
     {path:"Spell", value:"<spell FormID>"}]}}
]
```

(Placement of the tome into a vendor/loot list → `requiem-leveled-list-patching`.)

## I — Masters & REQ_NULL hygiene

- A spell/effect that references a Requiem/MR form (a `HalfCostPerk`, a Requiem keyword, an MR MGEF)
  auto-masters `Requiem.esp` / MR. If your edits touch only vanilla forms, **reference a real Requiem
  form** (e.g. set the `HalfCostPerk` to the `REQ_<School>_Mastery_*` perk) to pull the master in.
  houseCARL Add+Sorts masters from your references.
- Scan the record for `REQ_NULL_*` references (effects, perks, keywords). **Strip them on player-side
  spells/items.** On a **creature/NPC**, a NULLed vanilla resistance/power was replaced by a Requiem
  trait — route the replacement to `requiem-race-patching`, don't just delete. Never carry or add a
  `REQ_NULL_*`. See the masters/`REQ_NULL` rule carried by the `requiem-patching` skill.

## J — Verify the read-back

Every `bulk_apply`/`create_record` returns the patch path, `masters:`, and a per-op read-back. Confirm
`Requiem.esp` / MR is in `masters:`, the values are set, and no `REQ_NULL_*` remains. Then re-read:

```
housecarl_read_record formid="<spell FormID>" plugin="Requiem magic patching.esp" \
  fields=["BaseCost","ChargeTime","HalfCostPerk","Effects"] depth=2
```

(For a brand-new patch not yet in the registry, the per-op read-back is the verification until a
`housecarl_set_mo2_instance` refresh.)
