# houseCARL Authoring Recipes

Copy-ready call shapes for emitting a race override. houseCARL writes to a **new patch plugin**
(originals untouched); pass `into="<patch filename>"` to accumulate across calls. Read first, then
write — a malformed op refuses the whole call (all-or-nothing).

> **Write gotchas:** a freshly written patch isn't auto-enabled, and houseCARL reads load-order
> truth only — until you enable + sort it in MO2, the WRITE CALL is the verification: the per-op
> read-back confirms each edit, and `full_readback=true` (houseCARL 1.2.3+) returns the entire
> written record. A `read_record plugin="<patch>.esp"` against a not-yet-enabled patch fails with
> a named "not in the load order" error — if the write reported success the edits DID land; never
> re-issue them. Don't re-issue a `Remove` the winner already applied. (Writing into a patch that
> is already ACTIVE in the load order works — the old self-lock was fixed in houseCARL 1.2.1.)

## Coverage audit (whole-plugin bulk pass)

Before any per-record work on a whole-plugin job, enumerate the type in one read so the enumeration
becomes your work queue (full doctrine: the skill body's *Bulk pass protocol*). This call doesn't
write. Its fields are the triage matrix that drives the humanoid-vs-creature classification per row.

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="RACE" \
  fields=["Keywords","Flags","Starting","ActorEffect"] resolve_names=true format="dense"
```

`resolve_names` renders the link fields as identities (so the `ActorType*` classification reads off
the row), and `format="dense"` returns one positional row per race under a single column header.

`Keywords` classifies each row (`ActorTypeNPC 013794` → humanoid → Workflow A;
`ActorType{Creature,Animal,Troll,Undead,Daedra}` → creature → Workflow B); `Starting`/`ActorEffect`/
`Flags` show what balance-bearing content the record carries. `<Race>RaceVampire` counterparts
enumerate as their **own** rows — disposition each against the analogue's vampire record, never as a
rider on the base race.

Give every FormID from the enumeration a disposition: **patched** (which workflow — and only once the
per-record field checklist passes for it, so patched means field-complete, not merely touched) or
**skipped** (a per-record reason, and the only valid one is "no balance-bearing field — `Starting`
stats / regen / `UnarmedDamage` / `ActorEffect` / `SkillBoost`s / resistances / keywords — differs
from this record's vanilla or Requiem base, verified on that record"). A cosmetic / gimmick / reskin /
chargen-only purpose is **not** a skip reason — the skip is a field comparison, never a category label.
Close with a reconciliation count: patched + skipped = enumerated, and never extrapolate a disposition
across a same-prefix or playable/vampire family (read the divergent sibling's own record).

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

**Before the patch is enabled in MO2**, the write call itself is the verification: the per-op
read-back confirms each edited value, and `full_readback=true` on the write call (houseCARL
1.2.3+) returns the ENTIRE written record re-read from the patch file on disk (confirm the
`Starting`/`Regen` dictionary values there). A `read_record plugin="<patch>.esp"` does NOT work on
a not-yet-enabled patch (named "not in the load order" error); if the write reported success the
edits landed — never re-issue them.

**After enabling + sorting in MO2:**

```
housecarl_read_record formid="<race>" conflict_tree=true
```

Your patch should appear last as the winner with your values.
