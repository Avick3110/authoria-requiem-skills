# houseCARL Authoring Recipes — NPCs

Copy-ready call shapes for emitting an NPC balance override. houseCARL writes **directly into
an active patch** via `into=` (e.g. `into="Requiem NPC patching"`). A brand-new patch isn't in the
load order until you enable + sort it in MO2, so verify a fresh patch via the write call itself:
the `bulk_apply` per-op read-back, or `full_readback=true` (houseCARL 1.2.3+) for the entire
written record. Read first, then write — a malformed op refuses the whole call (all-or-nothing).
Verify every FormID against the live comparable.

## A — Re-balance a standalone combatant (replicate the analogue's fields)

`Configuration` sub-fields are addressed by path; `Configuration.Level.Level` is the fixed level.
Bring a modded bandit onto a Requiem tier:

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<npc>:ModBandits.esp", field_path:"Configuration.Level.Level", value:"12"},     # fixed; read the analogue
  {formid:"<npc>:ModBandits.esp", field_path:"Configuration.Flags", value:"Respawn, AutoCalcStats"}, # remove PCLevelMult
  {formid:"<npc>:ModBandits.esp", field_path:"Class",        value:"85BCE3:Requiem.esp"},  # REQ_Class_Bandit_SwordShield
  {formid:"<npc>:ModBandits.esp", field_path:"CombatStyle",  value:"03BE1B:Skyrim.esm"},
  {formid:"<npc>:ModBandits.esp", field_path:"DefaultOutfit",value:"9336AF:Requiem.esp"},  # tier outfit (gear = difficulty)
  {formid:"<npc>:ModBandits.esp", field_path:"Perks", verb:"ReplaceAll", values:[/* analogue's combat perks */]},
  {formid:"<npc>:ModBandits.esp", field_path:"ActorEffect", verb:"Add", value:"93369F:Requiem.esp"}  # REQ_Trait_Tempering_Bandit_Heavy_Rank4
]
```

Removing `PCLevelMult`: set `Configuration.Flags` to the full flag string **without** `PCLevelMult`
and set a flat `Configuration.Level.Level` (read the analogue's flags first so you don't drop a flag
the actor needs, e.g. `Female`).

## B — Template onto a Requiem base (the clean integration)

Inherit Requiem balance wholesale by pointing at a base template + ticking the inherit flags:

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<npc>", field_path:"Template", value:"86837F:Requiem.esp"},                     # REQ_Bandit_Template_SwordShield_02
  {formid:"<npc>", field_path:"Configuration.TemplateFlags",
   value:"Traits, Stats, Factions, SpellList, AIData, AIPackages, Script, DefPackList, AttackData, Keywords"}
]
```

Template family: `REQ_Bandit_Template_<Weapon>_{Base,01,02,03}` for `{MaceShield, SwordShield,
AxeShield, Greatsword, Warhammer, Battleaxe, Bow, Crossbow}` — tier ↑ = stronger. Omit `Traits` from
the flags if the NPC must keep its own appearance (the looktemplate pattern).

### B2 — Already-templated → just tick Stats + SpellList

```
housecarl_set_field formid="<npc>" into="Requiem NPC patching" \
  field_path="Configuration.TemplateFlags" value="Stats, SpellList"   # added to existing flags
```

## C — Boss (set fields directly, buff hard)

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<boss>", field_path:"Configuration.Level.Level", value:"50"},                   # high fixed level
  {formid:"<boss>", field_path:"Configuration.Flags", value:"Unique, BleedoutOverride"},   # named; not Respawn
  {formid:"<boss>", field_path:"Configuration.MagickaOffset", value:"1300"},               # caster boss
  {formid:"<boss>", field_path:"Class",       value:"<strong role class>"},
  {formid:"<boss>", field_path:"Perks", verb:"ReplaceAll", values:[/* vanilla + REQ skill perks */]},
  {formid:"<boss>", field_path:"ActorEffect", verb:"Add", value:"ADDDC7:Requiem.esp"}      # REQ_Trait_ResistMagic30 (reuse), or a bespoke REQ_Trait_<Boss>
]
```

## D — Creature trait bridge (new race)

Recognized race → touch nothing (Reqtificator perks it). New race → retarget the race **or** hand-add
the physique perk:

```
# Fix 1 — retarget to a recognized race:
{formid:"<creature npc>", field_path:"Race", value:"013205:Skyrim.esm"}                    # TrollRace

# Fix 2 — hand-add the physique perk (preserves the modded race):
{formid:"<creature npc>", field_path:"Perks", verb:"Add", compose:{type:"PerkPlacement",
   sets:[{path:"Perk", value:"000805:Requiem - Resist and Regen Tweak.esp"},{path:"Rank", value:"1"}]}}  # physique.fur
```

## E — Follower (record-side)

Keep `PcLevelMult`; add the two follower factions:

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<follower>", field_path:"Configuration.Flags", value:"AutoCalcStats, Unique, Protected"},   # do NOT remove PcLevelMult
  {formid:"<follower>", field_path:"Factions", verb:"Add", compose:{type:"RankPlacement",
     sets:[{path:"Faction", value:"05C84E:Skyrim.esm"},{path:"Rank", value:"-1"}]}},                   # PotentialFollower
  {formid:"<follower>", field_path:"Factions", verb:"Add", compose:{type:"RankPlacement",
     sets:[{path:"Faction", value:"05C84D:Skyrim.esm"},{path:"Rank", value:"0"}]}},                    # CurrentFollower
  {formid:"<follower>", field_path:"DefaultOutfit", value:"<REQ follower outfit>:Requiem.esp"}
]
```

Runtime registration (affinity/dialogue) is script-side → route to the `requiem-script-patching` skill.

## F — Numbered leveling-copies → collapse

A mod's `BanditA01..05` that differ only by number: set them all to the **same** fixed level + class +
perks + outfit, removing `PCLevelMult`, so they become one de-levelled Requiem tier. (Requiem's own
`EncBandit01..06` are *real* tiers — don't flatten those.)

## Masters & REQ_NULL stripping

houseCARL manages the master list the **xEdit way**: a FormID's first byte indexes the patch's ordered
`MAST` list (own records last), so a patch must declare as a master every file whose forms it
references. houseCARL does **Add + Sort Masters automatically** from the forms you reference — verify
the `masters:` line in the write read-back (it's already load-order-sorted). Full mechanism +
project-wide rule = the masters/`REQ_NULL` rule carried by the `requiem-patching` skill.

- A patch referencing a Requiem form (`REQ_Class_*`, `REQ_Trait_*`, a `Requiem.esp` perk/keyword)
  auto-masters `Requiem.esp` (and `Requiem - Resist and Regen Tweak.esp` for a physique perk).
- A patch that sets **only vanilla forms** (flat level + vanilla class, or the vanilla `ghost`
  keyword) carries no `Requiem.esp` master — usually fine (the Reqtificator keys off the vanilla form
  at build). When you specifically need `Requiem.esp` present, **reference a real Requiem form** to
  bring it in (there is no empty-master verb).

**REQ_NULL — strip every one; none may remain.** `REQ_NULL_*` are inert stubs Requiem retired
(NULLed perks/spells/keywords/look-templates). houseCARL reads live (Requiem active) so it **resolves
and shows their EditorIDs** — scan the NPC's `Perks`, `ActorEffect`, `Keywords`, inventory, and
`Template` for the `REQ_NULL` prefix and remove or replace each:

```
{formid:"<npc>", field_path:"ActorEffect", verb:"Remove", value:"<REQ_NULL_* FormID>"}   # strip a retired stub
{formid:"<npc>", field_path:"Perks",       verb:"Remove", value:"<REQ_NULL_* perk>"}      # or replace with the live Requiem form
```

Re-read the record after writing and confirm no `REQ_NULL_*` is left in any field.

## Verify the write

```
housecarl_read_record formid="<npc>" conflict_tree=true \
  fields=["Configuration","Class","CombatStyle","Perks","ActorEffect","Template"]
```

Your patch should appear last as the winner with your balance values — and the **appearance** winner
should still own the face/body fields (confirm you didn't displace it). This winner read only sees
the patch once it's enabled + sorted in MO2; before that, the write call is the verification (per-op
read-back, or `full_readback=true` on houseCARL 1.2.3+ for the entire written record). A `read_record
plugin="<patch>.esp"` on a not-yet-enabled patch fails with a named "not in the load order" error —
if the write reported success the edits landed; never re-issue them.
