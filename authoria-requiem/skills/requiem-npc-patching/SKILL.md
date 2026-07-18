---
name: requiem-npc-patching
description: Patch a new NPC, enemy, follower, or boss for Requiem via houseCARL — derive fixed level, flags, class, combat style, factions, perks, gear-tempering traits, and racial trait inheritance from Requiem's own comparable actor, set only the balance fields, and emit a direct ESP override the Reqtificator can then rebalance. Use when the user wants to patch an NPC or enemy for Requiem, balance a modded boss or dungeon mob, register a follower, set Requiem factions/class/combat style on an actor, fix an enemy that is too strong or too weak, or a custom creature that takes the wrong damage. Load this before touching an NPC record, not after the enemy spawns unbalanced or the boss dies to one arrow.
---

# Requiem NPC Patching

## Overview

This skill patches an `NPC_` record so its **combat balance** is consistent with Requiem — the
right fixed level and flags, the Requiem class + combat style for its role, the combat perks and
gear-tempering traits Requiem hands its own actors, the correct factions, and (for creatures) the
trait inheritance from its race. The output is a **direct ESP override** authored with houseCARL
`set_field` / `bulk_apply` / `create_record`; the Reqtificator's auto-balance pass then runs over it.

NPCs are the **actor-instance layer on top of the race layer** (`requiem-race-patching`). A race sets
what every member of the race inherits; an NPC record sets that individual actor's level, class,
perks, gear, factions, and template. Read the race skill's trait model first — this skill *reuses*
its creature-trait bridge (below).

**The central discipline is the visual-vs-balance split.** An `NPC_` record is contested by two
independent kinds of winner: **appearance** mods (face/body/tint — e.g. `MOSRefined.esp`,
`TSOSRefined.esp`, NPC overhauls) and **balance** sources (`Requiem.esp`, `USMP - Requiem.esp`,
Requiem addons). The Reqtificator's `actorVisualAutoMerge` reconciles the two at build time. **Your
patch sets only the Requiem-balance fields and must leave the appearance winner's fields alone.**
Use `conflict_tree` to see who wins which fields, derive balance from the Requiem comparable, and
let the merge handle the rest — the same "carry the inputs" discipline as the item domains.

What this skill does **not** do: design the MGEF/effect on a spell (link the existing Requiem spell;
route *design* to the `requiem-magic-patching` skill), author the **contents** of a death-item or outfit leveled list
(route to the `requiem-leveled-list-patching` skill), or do **script-based** follower registration
(route to the `requiem-script-patching` skill — see `references/followers.md`). And it **never** touches `Factions`, `AIData`, or race
*traits* as authored by the mod for non-balance reasons — see `## Judgment`.

## First step

Confirm authority is fresh, then identify what you're holding before you change a field.

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; if it is the wrong instance, fix it with
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. Then sanity-check Requiem is present:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   `Requiem.esp` must appear in the override chain. Either winner is valid: `Requiem.esp`
   (authoring-style profile, generated overlay disabled) or `Requiem for the Indifferent.esp` /
   a later patch (live profile — the normal consumer state). The live winner is the authority to
   derive from; **never** re-point houseCARL because the Reqtificator's output wins. Full
   doctrine: the `requiem-patching` skill's `references/scope-and-authority.md`.

2. **Read the modded NPC with its conflict tree** and separate the two winners:

   ```
   housecarl_read_record formid="<npc>" conflict_tree=true \
     fields=["Configuration","Class","CombatStyle","Race","Factions","DefaultOutfit","DeathItem",
             "Perks","ActorEffect","Template","AIData","Keywords"]
   ```

   The **balance winner** owns Configuration/Class/CombatStyle/Perks/factions; an **appearance
   winner** owns face/body fields. Note which is which — you write balance, never appearance.

3. **Decide: patch or skip — and a skip needs positive evidence.** Patch combatants; but the field
   failure mode is combatants misclassified as civilians, so **one civilian-looking signal never
   decides a skip, and any combat signal overrides them all** (a "citizen"-classed actor with a
   weapon, combat style, hostile faction, or dungeon placement is a combatant). Skip only when the
   skip signals agree — civilian class AND unaggressive AI AND no combat faction AND no combat
   gear/perks/spells — verified on *that* record. True non-combatants split two ways: a generic
   civilian/child/beggar is left alone (Requiem ships no override for them), while a **named
   non-combatant with wrong numbers** (PCLevelMult, inflated skills) gets Requiem's own
   civilian treatment — a **stat-only pass** (fixed low level, skills to 5–20, DNAM base
   attributes), no kit. Genuinely ambiguous → patch the combat-consistent minimum and say so,
   don't skip. Full signal table + the two civilian lanes: `references/identification.md`.
   **A conjured/summoned actor is a combatant, not a skip.** Its summon *spell* (tier, cost) routes
   to the `requiem-magic-patching` skill, but the summoned `NPC_` record's own level block is yours:
   give it a fixed level and stats here like any combatant, or it ships on `PCLevelMult` — the
   anti-Requiem pattern, hiding behind "the spell balances it" (the spell prices the summon; it does
   not de-level the actor).

4. **Classify the role** (drives everything): a **generic combatant** (bandit, forsworn, mage,
   marauder), a **creature** (animal/undead/daedra/automaton), a **follower**, or a **boss** (unique,
   heavily buffed). Then pick the **Requiem comparable** — the Requiem actor this NPC is meant to be —
   read its balance winner, and replicate. The class is usually the best giveaway of intended role;
   cross-check combat style, gear, and perks (some modders slap a generic/Dremora class on
   everything). Mapping heuristic in `references/identification.md`.

5. **Choose one stat-authority mode with the user for this plugin, before the first write.** Apply
   that choice to every non-templated NPC that enters the stat pass; never mix modes record by
   record. **AutoCalc mode:** ensure `AutoCalcStats` is on and let class + level own the calculation; do not
   write `PlayerSkills` or ACBS Health/Magicka/Stamina offsets. **Manual mode:** remove
   `AutoCalcStats`, then derive and write the complete DNAM `PlayerSkills` block plus the ACBS stat
   offsets from the analogue. Templated actors remain Workflow A skips in either mode.

## Bulk pass protocol (whole-plugin jobs)

When you're patching a whole plugin — routed here from the `requiem-patching` skill, or any job with
more than a handful of NPCs — the enumeration **is the work queue**, not a sample of it. High-count NPC
plugins hide their hard records inside uniform-looking groups: the PC-scaling vendor, the over-tier
summon, the level-1 quest attacker sitting among a dozen near-identical guards. Those non-uniform
outliers are exactly what a rebalance pass exists to catch — and the fastest way to ship them unpatched
is to read one member of a same-prefix EditorID family and extrapolate to the rest.

**Never extrapolate across a uniform-looking family — a same-prefix EditorID, a shared `Template`, or a
shared `Class`.** `AlikrEncBandit01..06`, a `Thalmor*` pair, a run of `…Guard##`, a block that all
template onto one base, or a group that all carry one `Class` *look* uniform, but a modder hand-tweaks
individuals inside the family — the one carrying its own stats is the record that needs you. Reading the
outlier costs one query; missing it ships a vendor that never got de-levelled, or a boss that dies to
one arrow in someone else's game.

Open with the four coverage queries — one call each, the whole plugin at once, and none writes
(the third is the stat/kit matrix — DNAM base attributes, ACBS offsets, perk/spell counts — the
per-record baseline the field checklist reconciles against; the fourth is the **forwarded-NULL
scan**: a `resolve_names=true` read of `Perks`/`ActorEffect`/`Keywords`, because `REQ_NULL_*`
stubs ride in on the mod's own list fields even when you author none. Treat it as a candidate scan,
not an instruction to touch every hit: an inherited/template NPC remains a no-write skip, including
its inert local NULL list entries. Strip NULLs only from fields the actor actually owns. Shapes in
`references/housecarl-recipes.md`):

1. **The de-levelling work list** — every NPC still on a player-level multiplier. A scalar comparison on
   the union-arm field doubles as an **arm-presence test**: PC-scaling actors have a readable `LevelMult`
   and match; fixed-level actors report no value on that arm and drop out. So this returns exactly the
   actors that still need a fixed level + `PCLevelMult` removed:

   ```
   housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="NPC_" \
     where=["Configuration.Level.LevelMult >= 0"]
   ```

   Every FormID it returns is on the de-levelling queue (Workflow C) — except followers, which keep the
   multiplier (`references/followers.md`).

2. **The triage matrix** — the disposition-driving fields for every record of the type, in one table:

   ```
   housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="NPC_" \
     fields=["Configuration.Level","Configuration.Flags","Configuration.TemplateFlags","AIData.Aggression","Class"]
   ```

   Read `Class` + `AIData.Aggression` + `TemplateFlags` per row to disposition each NPC (which workflow,
   or which skip category) instead of hand-picking batch reads.

**The mod's own actor-support records ride this pass.** A plugin that defines its own `CLAS`
(class), `CSTY` (combat style), `OTFT` (outfit), or `FACT` (faction) records gets those dispositioned
here too — enumerate each type (`cross_plugin_query plugins=["<NewMod>.esp"] type="CLAS"`, etc.).
The usual fix is on the *actor*: retarget the NPC to the Requiem analogue (`REQ_Class_*`, a role
combat style, a REQ outfit) per Workflow C, which leaves the custom record unreferenced and
skippable. But where a custom record stays referenced — a bespoke boss class, a mod's own faction the
quest needs — rebalance it by live analogy against the closest Requiem record (a class's
`StatWeights` vs the matching `REQ_Class_*`) or skip it with the reason stated. Assignment on the
actor and the record's own balance are both this lane's job; neither may be waved to another skill.

**Every FormID gets a disposition, and "patched" is a field verdict — not a record-touch.** Walk the
full enumeration: each record is **patched** (note which workflow) or **skipped** (note the reason —
civilian / vendor / child / already-templated *with the chain walked to a patched or Requiem base*
(Workflow A); a "quest or scene actor" is a skip only when it never fights — a summoned copy, duel
double, or scene attacker is a combatant; the fuller skip taxonomy
is in `references/identification.md`), and a skip is verified on *that* record, never inherited from a
neighbour. **Flags fields are unions, not scalars:** a `Configuration.Flags` write replaces the whole
bitfield, so read the winner's flags first and write original-bits + your change — a literal Set that
only names the bits you thought about silently strips `Female`, `Essential`, `Unique`, `IsGhost`,
`Invulnerable` from the actors that carried them. A record counts **patched only when the per-record field checklist (`## Checklist`) passes
for it** — level, intended flag delta, class, perks, spells, and the plugin-wide stat mode. In a bulk
job that checklist runs **for every enumerated record, not once for the job**: a manual-mode record
missing its `PlayerSkills`/offset block, or an AutoCalc-mode record carrying newly stamped manual
stats, is **not patched, it's half-done**, and the count must never close green over it.

Close the pass with a **reconciliation count — patched (field checklist passed) + skipped =
enumerated.** If the two sides don't add up, a record fell through; find it before you call the type
done. Then **re-run query 1 as a drain check**: it must come back empty, or return only intended keepers
— followers (which keep `PCLevelMult`), and `Stats`-templated actors whose verified chain ends in a
patched base (their own `LevelMult` is inert — the template's `Stats` flag overrides it — but only a
walked chain earns the keeper label, never the flags alone). A non-empty result carrying anything else
means a `LevelMult` was never removed on that actor — find it and de-level it before you close. (This is the per-record
coverage the `requiem-patching` skill's integration checklist gates on for high-count types.)

## Workflow

Real call shapes below; the full copy-ready set is in `references/housecarl-recipes.md`, the field
standard by archetype in `references/npc-fields.md`, and the perk model in `references/perks.md`.
End-to-end identification → analogue → apply walk-throughs are in `references/worked-examples.md`.

**Always, for any combatant you patch: set a FIXED level and remove `PCLevelMult`.** Requiem is
de-levelled — actors have a flat `Configuration.Level.Level` (read the comparable's), not a
player-level multiplier. **The one exception is followers** (Workflow C), which *keep* `PCLevelMult`
so they scale with the player.

### A — Already Requiem-templated → skip

If the NPC's `Configuration.TemplateFlags` already include `Stats` + `SpellList` (often a `…lvl…`
editorID, or it templates onto a Requiem base), it already inherits Requiem's balance. Confirm the
`Template` points at a representative base, then leave the NPC **entirely untouched**. Don't
re-derive what the template already delivers, and don't clean inert local `REQ_NULL_*` perks/spells:
creating an override for data the template masks only wastes work and can serialize stray count
subrecords.

**Walk the template chain to its end before crediting it.** Inheritance is only as good as the base
it lands on: a chain ending in another of the *mod's own* actors — a soul/simulacrum copy templating
its living original, itself still on `PCLevelMult` — delivers unpatched balance, not Requiem's
(verified field case: a "soul" duplicate skipped wholesale because its flags looked inherited). Such
a record is dispositioned "inherits via template from `<base>`" only once the base itself is patched
this pass. Reconcile only fields the template flags do **not** inherit; inherited local residue is
inert and does not justify an override.

### B — Has a template, no modded spells/perks → tick the flags

If it has a `Template` onto a representative base but the flags aren't set, just turn on inheritance:

```
housecarl_set_field formid="<npc>" into="Requiem NPC patching" \
  field_path="Configuration.TemplateFlags" value="Stats, SpellList"   # add to existing flags
```

Double-check the template is the right Requiem analogue first.

### C — Standalone combatant → template onto a Requiem base, or replicate fields

The clean path is **templating onto a Requiem base actor** so the NPC inherits Requiem balance for
free (the actor analog of race-templating). Requiem ships base templates per role + weapon + tier,
e.g. `REQ_Bandit_Template_<Weapon>_{Base,01,02,03}` (`SwordShield_02` = `86837F`,
`Warhammer_01` = `8749BD`, …). Point the NPC at the matching tier and inherit stats + spell list:

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<npc>", field_path:"Template", value:"86837F:Requiem.esp"},               # REQ_Bandit_Template_SwordShield_02
  {formid:"<npc>", field_path:"Configuration.TemplateFlags", value:"Traits, Stats, Factions, SpellList, AIData, AIPackages, Script, DefPackList, AttackData, Keywords"}
]
```

If templating doesn't fit (the NPC needs its own appearance/gear), **replicate the comparable's
balance fields** instead — set them from the live analogue, never from guessed numbers:

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<npc>", field_path:"Configuration.Level.Level", value:"12"},              # fixed; read the analogue
  {formid:"<npc>", field_path:"Configuration.Flags", value:"Respawn"},               # manual-mode example; full winner union, AutoCalcStats removed
  {formid:"<npc>", field_path:"Class",        value:"85BCE3:Requiem.esp"},           # REQ_Class_Bandit_SwordShield
  {formid:"<npc>", field_path:"CombatStyle",  value:"03BE1B:Skyrim.esm"},            # role combat style (often vanilla)
  {formid:"<npc>", field_path:"DefaultOutfit",value:"<REQ outfit>:Requiem.esp"},     # gear drives much of the difficulty
  {formid:"<npc>", field_path:"Perks", verb:"ReplaceAll", values:["<combat perks from the analogue>"]},
  {formid:"<npc>", field_path:"ActorEffect", verb:"Add", value:"93369F:Requiem.esp"} # REQ_Trait_Tempering_* (tempers its gear)
]

# Continue this same manual-mode example — AutoCalcStats is OFF for the whole plugin stat pass:
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<npc>", field_path:"PlayerSkills.Health",  value:"112"},   # base attributes (NOT the ACBS offsets)
  {formid:"<npc>", field_path:"PlayerSkills.Magicka", value:"100"},
  {formid:"<npc>", field_path:"PlayerSkills.Stamina", value:"118"},
  {formid:"<npc>", field_path:"PlayerSkills.SkillValues[OneHanded]", value:"21"}      # each elevated skill, from the analogue
  # Named/scaling actor: also the analogue's PlayerSkills.SkillOffsets[<Skill>] (generic mobs: 0).
  # Creature/boss: also Configuration.<Stat>Offset — the offsets stack ON the DNAM base attrs.
  # A caster: also carry the analogue's castable spells (ActorEffect spell entries, or the template SpellList).
]
```

**The modded NPC's own kit — empty *or* populated — is never the derivation source, and never the
adequacy bar.** Requiem's own low-tier records ship with zero perks and zero `ActorEffect` (bandit
`_Base`, `EncWolf` — verified live), so an empty list is no reason to skip; and a *populated* list
is no reason to stop — a handful of source perks reads as "already has perks" only until you compare
it against the analogue's kit (bandit tiers carry 5/9/12, a guard ~17, a vampire tier-2 30 — the
field failure is leaving a 2–3-perk source kit standing because none of its entries were null). The
perk/spell verdict on every combatant is a **comparison against the analogue's kit**: vanilla source
perks are replaced by it, modded ones augmented. See `references/perks.md`.

**Class is the balance spine** — Requiem's `REQ_Class_*` set has `StatWeights` that distribute
AutoCalc stats by role (Bandit Health 4/Stamina 6, Guard 5/5, **Slighted 1/0/0** = the weak tier).
Pick the class whose role + weapon + strength matches; the rest of the difficulty follows from class,
level, perks, and gear. Then obey the plugin's stat-authority choice: AutoCalc mode leaves all
`PlayerSkills` and ACBS stat offsets alone; manual mode removes `AutoCalcStats` and copies the
analogue's complete explicit block. Never stamp manual values while leaving AutoCalc enabled.
Combat perks are **vanilla `Skyrim.esm` skill perks** the analogue carries
(escalating with tier) plus, on stronger actors, Requiem skill-tree perks (`REQ_OneHanded_*`,
`REQ_Conjuration_Empower_*`); copy the analogue's set — and when no clean analogue exists (a custom
gear mix, a hybrid), derive the set via the `requiem-perk-assignment` skill. The `ActorEffect` carries Requiem
**`REQ_Trait_Tempering_<role>_<tier>`** marker spells that temper the NPC's worn gear — match the
analogue's. A **caster** needs more than that trait: carry the analogue's actual **castable spell
list** — its `ActorEffect` spell entries, or the `SpellList` it inherits through its template —
because the tempering/resist trait alone is not the magic kit, and a caster with no spells carried just
stands there. **Do not hand-add the armor-penetration / armor-weight / arrow-recovery perks or the
racial trait perk — those are Reqtificator-assigned** (see `## Common mistakes` and
`references/perks.md`).

**Apply the sparse-write gate before emitting any standalone override.** Never `Set` an optional
FormLink to null: omit the operation when the field should stay absent, or use `verb="Remove"` when
clearing a real link (`CombatStyle`, `AttackRace`, and other nullable links). Never author an empty
`ReplaceAll`: if the target `Perks`/`ActorEffect` list is already empty, don't write it; if an existing
list must become empty, remove its entries and verify xEdit shows both the list and its count subrecord
absent — no `PRKZ = 0` and no `SPCT = 0`. Finally diff `Configuration.Flags` before/after: only the
intended role bits and plugin-wide AutoCalc choice may change; `LoopedScript`/`LoopedAudio` and every
other unrelated bit must remain exactly as authored.

### D — Boss (unique, heavily buffed) → set fields directly, buff hard

A boss is a unique, named actor Requiem buffs well beyond the generic ladder (dungeon bosses, named
chiefs, dragon priests, hagravens, warlords). **Never template a boss onto a generic base** — set its
fields directly: a high **fixed level** (dragon priests sit at 50), the `Unique` flag (and
`Protected`/`Essential` only if its quest needs it), a large stat offset where the role demands it (a
caster boss carries a big `MagickaOffset`, e.g. 1300), the role's strong class + combat style, a rich
perk set (vanilla combat perks + Requiem skill perks), and — for its signature toughness — a
`REQ_Trait_*` ability on its `ActorEffect`. **Reuse an existing Requiem trait where one fits**
(e.g. `REQ_Trait_ResistMagic30 ADDDC7`, which Requiem already puts on dragon priests), or author a
bespoke `REQ_Trait_<Boss>` for this NPC (link it here; design its MGEF in the `requiem-magic-patching` skill). A named
unique may also **template onto the generic boss of its type** for stats/spell list while keeping its
own perks + signature trait (this is how `Rahgot` rides `EncDragonPriest`). The failure to avoid is
under-buffing a boss down to a generic mob.

### Creatures and the trait bridge

For a creature, first check its **race**. If it's a **recognized Requiem race** (the race appears in
the Reqtificator's `ActorAssignmentRules` lists — troll, draugr, wolf, skeleton, atronach, …), the
NPC needs **almost nothing**: the race carries the trait spells (engine-inherited) and the
Reqtificator adds the `incomingDamageModifier` perk by race-FormID match at build. Don't hand-stamp
that perk, and don't replicate traits onto the NPC. (Requiem's own `EncWolf` is *identical to
vanilla* for exactly this reason.) Set only what the comparable creature sets — usually a fixed level,
explicit `HealthOffset`/`StaminaOffset` (creatures use offsets, **not** AutoCalcStats), and at most
one combat perk. **"Almost nothing" is still a patch, not a skip:** the de-level pass (fixed level,
`PCLevelMult` removed) runs on every creature, and a hatchling or ambient critter left untouched
because "the race handles it" is exactly what the drain check exists to catch (verified field case:
a chaurus hatchling shipped on `PCLevelMult` under that reasoning).

If it's a creature of a **new/unrecognized race**, the Reqtificator can't perk it. Resolve it the
two ways the race skill names (`requiem-race-patching` references §G + `references/trait-bridge.md`):

1. **Retarget the NPC's `Race`** to the nearest recognized Requiem race so the Reqtificator perks it
   automatically; or
2. **Hand-add the named physique perk** to the NPC (`Perks` Add, compose `PerkPlacement`) using the
   Resist-and-Regen-Tweak taxonomy (fur `000805`, metal `000807`, chitin `000802`, stone `00080A`,
   zombie `00080D`, supernatural-undead `00080B`/dragon `000804` — verify live).

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<creature npc>", field_path:"Perks", verb:"Add", compose:{type:"PerkPlacement",
     sets:[{path:"Perk", value:"000805:Requiem - Resist and Regen Tweak.esp"},{path:"Rank", value:"1"}]}}  # physique.fur
]
```

## Judgment

The mechanics are easy; the judgment is **identification** — what the NPC is, and whether to touch it.

- **Patch combatants; a skip needs positive evidence.** Any combat signal (weapon, style, hostile
  faction, combat perks/spells, dungeon placement) overrides a civilian-looking class — mislabeled
  combatants are the recurring field failure, not over-patched shopkeepers. Generic
  civilians/children/beggars stay as authored; a **named** non-combatant with wrong numbers gets
  the stat-only pass (`references/identification.md`). A **conjured/summoned** actor is a
  combatant: its spell routes to the `requiem-magic-patching` skill, its actor record gets the
  fixed-level/stats pass here. Genuinely ambiguous after reading all the signals → patch the
  combat-consistent minimum and say so; in an interactive session, ask.

- **Fixed level, remove `PCLevelMult` — except followers.** De-levelling is the core of Requiem
  balance; a non-follower must get a flat level and lose the multiplier. A **follower** keeps
  `PCLevelMult` (with its `CalcMinLevel`/`CalcMaxLevel`) so it scales with the player.

- **Bosses get buffed, not normalized — but the buff path needs a combat archetype first.**
  Recognize the unique heavily-buffed actor and make it strong (high level, strong class/style, rich
  perks, a `REQ_Trait_*` for signature toughness — reuse Requiem's or author one). Don't flatten it
  to a generic tier. `Unique`/`Summonable`/a grand name on a **mount, pet, or ambient critter** does
  *not* qualify — those route to the mounts lane below.

- **Mounts, pets, and livestock are their own lane — never the combat ladder.** A horse, dog, or
  pack animal derives from the live winner of its own kind: an ordinary saddled horse is fixed
  level 4 (H 289, AutoCalc on — verified live), and only the named supernatural steed tier
  (Shadowmere: 50, H 1637, a `REQ_Trait_Healing_*`) sits higher. De-level it like everything else;
  a level-50 riding horse is the field failure this lane exists to kill. Details + mined numbers:
  `references/identification.md`.

- **Alternate-state copies of a named actor inherit from their primary.** A "soul", "simulacrum",
  ghost, marooned, or flashback duplicate is a combatant, not a quest-actor skip: patch the primary
  once, then template the copy onto it (`Template` → the primary; `TemplateFlags` `Stats, SpellList`
  as the state allows — the pattern vanilla itself uses for Soul Cairn copies) instead of
  re-deriving each copy. A **spectral** actor also carries `ActorTypeGhost 0D205E:Skyrim.esm` on its
  NPC record — the Reqtificator's state-trait pass keys on that keyword to add the ghost trait at
  build; carry the keyword, never hand-stamp the trait perk (`references/perks.md`).

- **Modded vs vanilla spells & perks — the precise rule.** A **modded** spell/perk/item (new, not in
  the base game, carrying the mod's own mechanic) is **never removed — augment or patch it**, and
  **don't add a redundant usable spell if the NPC already wields a modded one** (route its design to
  the `requiem-magic-patching` skill). A **vanilla** spell/perk is **replaced** with the Requiem analogue — vanilla
  content isn't preserved. (Adding a generic resistance like magic-resist is always fine.) Decide per
  spell/perk: modded → augment; vanilla → replace.

- **Numbered NPCs.** Requiem's own `EncBandit01..06` are *intentional power tiers* (escalating perks +
  gear). A **mod's** arbitrary numbered copies (`BanditA01..05` that differ only by a number and feed
  a leveled list for PC-scaling) should **collapse to one Requiem tier** — make them identical and
  fixed-level, the way Requiem de-levels. Tell the two cases apart before you act.

- **Uniques set fields; generic spawns template.** A named/unique actor gets its fields set directly;
  a generic spawn is cleanest templated onto a Requiem base.

- **Never touch `Factions`, `AIData`, or mod-authored race traits** for balance. Patch everything
  else in scope. (Follower *registration* factions are the record-side exception — see
  `references/followers.md`.)

## Common mistakes

- **Clobbering the appearance winner.** Writing face/body fields (or a whole-record overwrite) buries
  the visual mod the Reqtificator was going to merge. Set balance fields only.
- **Hand-stamping a Reqtificator-assigned perk.** The `incomingDamageModifier` racial trait perk
  (e.g. draugr `031285`), and the all-actor `gameMechanics` perks (armor penetration `AD394*`, armor
  weight `AD3A34`, arrow recovery `AD3A35`) are added by the Reqtificator at build — they are
  **absent from source records** and must not be placed by hand. (Confirmed: Requiem's draugr carries
  14 *vanilla* combat perks, none of them the trait perk.)
- **Leaving `PCLevelMult` on a non-follower**, or **stripping it from a follower.** Fixed level for
  combatants; keep the multiplier for followers.
- **Mixing AutoCalc and explicit stats in one plugin.** Ask once: AutoCalc mode leaves manual stats
  alone; manual mode removes `AutoCalcStats` before writing the full DNAM/ACBS block.
- **Writing null links or empty lists.** Remove an optional link instead of setting it null; never
  leave `PRKZ = 0` / `SPCT = 0` after clearing perks or actor effects.
- **Under-buffing a boss** to a generic tier — recognize it and buff it (see Workflow D).
- **Removing or replacing a *modded* spell/perk/item** (augment instead) — and the inverse, **leaving
  a *vanilla* spell/perk** in place instead of swapping in the Requiem analogue.
- **Patching `Factions` / `AIData` / mod-authored traits.** Out of scope; leave them.
- **Adding a redundant generic class** (a `DremoraClass`-on-everything reflex) instead of the
  role-correct `REQ_Class_*`.
- **Replicating traits onto a recognized-race creature's NPC.** The race carries them; the NPC needs
  almost nothing. Over-patching it duplicates or fights the race layer.
- **Leaving a live `REQ_NULL_*` reference in the record.** These are inert stubs Requiem retired; houseCARL
  resolves their EditorIDs live (Requiem is a master of the read), so scan `Perks`/`ActorEffect`/
  `Keywords`/inventory/`Template` and strip every one from fields the NPC owns. An already-templated
  Workflow A skip remains untouched even if its masked local lists contain NULLs. See
  `references/housecarl-recipes.md` and the masters/`REQ_NULL` rule carried by the `requiem-patching`
  skill.
- **Designing a spell here.** Link the Requiem spell; route MGEF/effect design to the `requiem-magic-patching` skill.

## Checklist

Before finishing an NPC override, confirm:

- [ ] **Whole-plugin job:** every enumerated NPC dispositioned — **patched = this checklist passed on
      that record**, or skipped with a reason; counts reconcile (patched + skipped = enumerated); query 1
      re-run drains to empty/followers-only; no same-prefix / shared-`Template` / shared-`Class`
      extrapolation; the mod's own `CLAS`/`CSTY`/`OTFT`/`FACT` records dispositioned too.
- [ ] **One stat mode recorded for the plugin:** AutoCalc (`AutoCalcStats` on, no manual stat writes)
      or manual (remove it, write the full derived block); no record-by-record mixing.
- [ ] **Role identified** and it's a combatant (not a civilian/helper) — a summoned actor counts as a
      combatant (fixed level + stats here; the summon spell routed to the `requiem-magic-patching` skill).
- [ ] **Level** fixed + `PCLevelMult` removed (followers: kept).
- [ ] **Flags** correct (Respawn for generic spawns; Unique/Protected/Essential by role;
      `AutoCalcStats` matches the plugin-wide choice, not a per-record impulse).
- [ ] **Stat authority:** AutoCalc mode made no `PlayerSkills` or ACBS stat-offset writes. Manual mode
      removed `AutoCalcStats` and carried the analogue's complete DNAM base attributes, `SkillValues`,
      `SkillOffsets`, and ACBS Health/Magicka/Stamina offsets.
- [ ] **Class** = the role-correct `REQ_Class_*`; **CombatStyle** matches the role, or is absent via
      `Remove` — never an explicit null link. The same rule holds for `AttackRace` and optional links.
- [ ] **Perks** = the analogue's combat perks (Requiem's player perk tree on vanilla FormIDs);
      **the source kit — empty or populated — never caps the derivation**: the verdict is the
      comparison against the analogue's kit, per record; **no** Reqtificator-assigned perks hand-added.
- [ ] **Named non-combatant with wrong numbers:** got the stat-only civilian pass (fixed low level,
      skills 5–20, DNAM set, no kit) — not skipped outright, not given a combat kit.
- [ ] **Trait bridge:** recognized-race creature → nothing (Reqtificator handles it); new-race
      creature → retarget `Race` or hand-add the physique perk.
- [ ] **ActorEffect** = the analogue's `REQ_Trait_Tempering_*` (and a `REQ_Trait_*` for a boss).
- [ ] **Caster spell kit:** a caster carries the analogue's actual castable spells (its `ActorEffect`
      spell entries, or the `SpellList` inherited through its template) — not just the tempering/resist
      trait.
- [ ] **Spells/perks:** modded ones augmented (not removed); vanilla ones replaced with the analogue.
- [ ] **DefaultOutfit / DeathItem** links set to the Requiem analogue's (contents → the `requiem-leveled-list-patching` skill).
- [ ] **Template + TemplateFlags** set when templating onto a Requiem base — and every
      inherited-template skip has its **chain walked to a patched or Requiem-balanced end** and
      received no cleanup override for masked perks/spells/NULLs.
- [ ] **Mount/pet/livestock:** patched in its own lane (ordinary mount fixed ~L4; the supernatural
      steed tier only with live precedent) — never the combat ladder, never Workflow D.
- [ ] **Alternate-state copies** templated onto their patched primary; spectral actors carry
      `ActorTypeGhost` (the Reqtificator adds the ghost trait — not hand-stamped).
- [ ] **Appearance fields untouched**; `Factions`/`AIData`/mod traits untouched.
- [ ] **Flags written as unions** — every `Configuration.Flags` write carries the winner's original
      bits plus your change; no `Female`/`Essential`/`Unique`/`IsGhost`/`Invulnerable` bit silently
      dropped, and no unrelated `LoopedScript`/`LoopedAudio` bit added.
- [ ] **Sparse serialization:** an empty `Perks`/`ActorEffect` result has no list and no count
      subrecord (`PRKZ`/`SPCT` absent); no nullable FormLink was written as null.
- [ ] **Every carried FormID resolve-verified** — `housecarl_resolve` each derived FormID (perk,
      class, outfit, spell) before the write; the classic slip is the right FormID under the wrong
      master suffix, which reads fine until the record dangles.
- [ ] **Masters correct** — houseCARL Add+Sorts them from referenced forms the xEdit way; verify the
      `masters:` read-back. `Requiem.esp` auto-masters when a Requiem class/perk/spell is referenced
      (force it in by referencing a real Requiem form when you specifically need it).
- [ ] **No live `REQ_NULL_*` remains** in fields a patched NPC owns; strip or replace each. Masked
      local lists on a Workflow A template skip are inert and remain untouched.
- [ ] Routed onward: loot/leveled-list contents → the `requiem-leveled-list-patching` skill; spell *design* → the `requiem-magic-patching` skill;
      script-based follower registration → the `requiem-script-patching` skill.

## Notes

- **Authority** = houseCARL's live conflict winner **across the whole Requiem lane** — all seven
  hand-authored Requiem plugins that touch actors, enumerated with per-lane guidance (vampires →
  `Requiem_VampireCollection.esp`, forsworn → Minor Arcana, summons → Magic Redone, named-actor
  forwards → `USMP - Requiem.esp`) in `references/npc-authority.md`. Pick the lane before the
  actor: the analogue for a modded vampire lives in the vampire lane, not among Requiem.esp
  bandits. On a live profile `Requiem for the Indifferent.esp` (the Reqtificator's output) wins
  most NPCs and is the winner to derive from — it folds the merge in. **Appearance** resolves to
  a separate visual winner. Read the live chain per record — it tells you which source owns
  balance vs appearance.
- **houseCARL writes directly into active patches via `into=`** — author straight into
  `Requiem NPC patching`; verify a brand-new patch via the `bulk_apply` read-back until it's refreshed.
  The `where=` filter helps archetype scans (e.g. `where="Configuration.Level.Level >= 30"`).
- This skill sets the NPC frame and links Requiem forms. **Race** traits → `requiem-race-patching`;
  **spell/MGEF design** → the `requiem-magic-patching` skill; **leveled-list contents** → the `requiem-leveled-list-patching` skill;
  **script-based follower registration** → the `requiem-script-patching` skill; **perk-set derivation
  with no clean analogue + mod `PERK` records** → the `requiem-perk-assignment` skill.
