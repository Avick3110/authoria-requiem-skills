# houseCARL Authoring Recipes

Copy-ready call shapes for emitting a race override. houseCARL writes to a **new patch plugin**
(originals untouched); pass `into="<patch filename>"` to accumulate across calls. Read first, then
write — a malformed op refuses the whole call (all-or-nothing).

> **Write gotchas:** you can't write into an **active** patch (Mutagen self-locks it)
> — author into a fresh **inactive** patch. A freshly written patch isn't in houseCARL's registry, so
> verify via the `bulk_apply` per-op read-back (or `read_record plugin="<patch>.esp"`), **not** the
> winner read, until it's enabled + refreshed. Don't re-issue a `Remove` the winner already applied.

## A — Re-balance an existing modded race (the common case)

The modded race exists in its own plugin with the mod's stats. Override it. `Starting` and `Regen`
are **dictionaries** — set each key with `verb:"Set" key:"<stat>"`. Example: bring a humanoid follower
race onto the Nord standard.

```
housecarl_bulk_apply patch_name="Authoria_Races" operations=[
  {formid:"<race>:ModRace.esp", field_path:"Starting", verb:"Set", key:"Health",  value:"110"},
  {formid:"<race>:ModRace.esp", field_path:"Starting", verb:"Set", key:"Magicka", value:"80"},
  {formid:"<race>:ModRace.esp", field_path:"Starting", verb:"Set", key:"Stamina", value:"110"},
  {formid:"<race>:ModRace.esp", field_path:"Regen",    verb:"Set", key:"Health",  value:"0.21"},
  {formid:"<race>:ModRace.esp", field_path:"Regen",    verb:"Set", key:"Magicka", value:"0.36"},
  {formid:"<race>:ModRace.esp", field_path:"Regen",    verb:"Set", key:"Stamina", value:"0.84"},
  {formid:"<race>:ModRace.esp", field_path:"BaseCarryWeight", value:"110"},
  {formid:"<race>:ModRace.esp", field_path:"UnarmedDamage",   value:"9"}
]
```

## B — Skill boosts (playable)

Each `SkillBoost<n>` is a substruct with `.Skill` (ActorValue) + `.Boost`. Set the six the analogue
uses; leave unused slots `Skill=255` (None).

```
housecarl_bulk_apply into="Authoria_Races" operations=[
  {formid:"<race>", field_path:"SkillBoost1.Skill", value:"OneHanded"}, {formid:"<race>", field_path:"SkillBoost1.Boost", value:"10"},
  {formid:"<race>", field_path:"SkillBoost2.Skill", value:"TwoHanded"}, {formid:"<race>", field_path:"SkillBoost2.Boost", value:"10"},
  {formid:"<race>", field_path:"SkillBoost3.Skill", value:"HeavyArmor"}, {formid:"<race>", field_path:"SkillBoost3.Boost", value:"5"}
  # … SkillBoost4..6 likewise; SkillBoost0 often Skill=255 / Boost=0
]
```

## C — Ability spells (ActorEffect)

`ActorEffect` is a list of spell FormLinks (engine-inherited by the race's NPCs). For a playable race,
replicate the analogue's whole list; for a creature, `Add` the trait spells you need.

```
# replace the whole bundle with the analogue's (playable):
{formid:"<race>", field_path:"ActorEffect", verb:"ReplaceAll",
 values:["609AF0:Requiem.esp","82CC14:Requiem.esp","AE3B19:Requiem.esp","AE3B25:Requiem.esp","AE3B35:Requiem.esp"]}

# or add individual trait spells (creature):
{formid:"<race>", field_path:"ActorEffect", verb:"Add", value:"AD39E6:Requiem.esp"}   # Armor_Troll
```

## D — Creature: add traits + the RegenHpInCombat flag

`Flags` is a flag set — set the full string (read the analogue's flags, add `RegenHpInCombat` when you
add a Healing trait).

```
housecarl_bulk_apply into="Authoria_Races" operations=[
  {formid:"<modded creature race>", field_path:"ActorEffect", verb:"Add", value:"AD39E6:Requiem.esp"},  # Armor_Troll
  {formid:"<modded creature race>", field_path:"ActorEffect", verb:"Add", value:"AE3AED:Requiem.esp"},  # Healing_Troll
  {formid:"<modded creature race>", field_path:"Flags",
   value:"Walks, NoCombatInWater, RegenHpInCombat, UseAdvancedAvoidance"},
  {formid:"<modded creature race>", field_path:"Keywords", verb:"Add", value:"5F367F:Requiem.esp"}      # MinorKnockdownImmunity
]
```

Do **not** add a `REQ_Trait_FX_*` spell, a Layer-B `incomingDamageModifier` perk (that goes on NPCs),
or a second resistance if the race already ships its own `Ab*` ones.

## E — Playable: keywords + ARMA race-add

Keywords as a `ReplaceAll`, then make the race wear gear. **Cleanest gear path: set `ArmorRace` to the
vanilla analogue** so the race reuses every ArmorAddon that already covers it.

```
{formid:"<race>", field_path:"Keywords", verb:"ReplaceAll",
 values:["013794:Skyrim.esm","586728:Requiem.esp"]}                 # ActorTypeNPC + DropsBlood (+ unperked-skill kws as needed)
{formid:"<race>", field_path:"ArmorRace", value:"013746:Skyrim.esm"}  # delegate to NordRace
```

Exhaustive alternative (per-armor): add the race FormID to each relevant ARMA's `AdditionalRaces`:

```
{formid:"012E48:Skyrim.esm", field_path:"AdditionalRaces", verb:"Add", value:"<new race>"}  # IronCuirassAA
```

## F — Net-new race record

Rarely needed (usually the modded race already exists). If you must:

```
housecarl_create_record record_type="Race" editorid="Authoria_NewRace" into="Authoria_Races" operations=[
  {field_path:"Name", value:"My Race"},
  {field_path:"Starting", verb:"Set", key:"Health", value:"110"},
  # … Magicka/Stamina, Regen, BaseCarryWeight, UnarmedDamage, SkillBoost0..6, ActorEffect, Keywords, Flags
]
```

## G — Per-NPC trait perk (the `requiem-npc-patching` skill bridge)

The Layer-B `incomingDamageModifier` perk goes on the creature's **NPC_** records, not the RACE.
Hand-adding it is the `requiem-npc-patching` skill's job, but the shape is:

```
housecarl_bulk_apply into="Authoria_Races_NPCs" operations=[
  {formid:"<creature NPC>", field_path:"Perks", verb:"Add", compose:{type:"PerkPlacement",
     sets:[{path:"Perk", value:"000805:Requiem - Resist and Regen Tweak.esp"},{path:"Rank", value:"1"}]}}  # physique.fur
]
```

Alternatively set the NPC's `Race` to a vanilla Requiem race so the Reqtificator perks it
automatically (template-onto-known-race).

## Masters

A race patch that references a Requiem trait spell/keyword (Heritage/Blood/Armor/Resist/Healing,
`REQ_*` keywords) auto-adds `Requiem.esp` (and `Requiem - Resist and Regen Tweak.esp` for its trait
spells) as masters. If a patch happens to reference only vanilla forms, add `Requiem.esp` as a master
manually so the Reqtificator resolves it.

## Verify the write

```
housecarl_read_record formid="<race>" conflict_tree=true
```

Your patch should appear last as the winner with your values. If it's inactive, read it explicitly
with `plugin="<patch>.esp"` instead of trusting the winner.
