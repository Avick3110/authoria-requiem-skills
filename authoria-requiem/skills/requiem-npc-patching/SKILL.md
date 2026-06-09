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

1. **Freshness probe.** Read Iron Sword and confirm the winner is `Requiem.esp`:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   If it's `Requiem for the Indifferent.esp` or an `Authoria - *` plugin, the resolver is stale —
   point houseCARL at your Requiem MO2 instance and re-probe:
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. (This skill's reference data was mined on
   the author's Authoria instance.)

2. **Read the modded NPC with its conflict tree** and separate the two winners:

   ```
   housecarl_read_record formid="<npc>" conflict_tree=true \
     fields=["Configuration","Class","CombatStyle","Race","Factions","DefaultOutfit","DeathItem",
             "Perks","ActorEffect","Template","AIData","Keywords"]
   ```

   The **balance winner** owns Configuration/Class/CombatStyle/Perks/factions; an **appearance
   winner** owns face/body fields. Note which is which — you write balance, never appearance.

3. **Decide: patch or skip.** Patch **combatants only.** Skip civilians, townsfolk, children,
   quest-helpers, vendors with no combat role, and **conjured/summoned** actors. Tell them apart by
   `Class` (a `*Citizen`/vendor/`Job*` class → skip), `AIData` (Aggression `Unaggressive` + low
   Assistance → skip), factions (job/crime only, no combat faction), the child race, and the absence
   of a combat class/style. Detail + signals in `references/identification.md`.

4. **Classify the role** (drives everything): a **generic combatant** (bandit, forsworn, mage,
   marauder), a **creature** (animal/undead/daedra/automaton), a **follower**, or a **boss** (unique,
   heavily buffed). Then pick the **Requiem comparable** — the Requiem actor this NPC is meant to be —
   read its balance winner, and replicate. The class is usually the best giveaway of intended role;
   cross-check combat style, gear, and perks (some modders slap a generic/Dremora class on
   everything). Mapping heuristic in `references/identification.md`.

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
`Template` points at a representative base and the NPC carries **no modded spells/perks** of its own,
then leave it. Don't re-derive what the template already delivers.

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
  {formid:"<npc>", field_path:"Configuration.Flags", value:"Respawn, AutoCalcStats"},# remove PCLevelMult; AutoCalcStats for humanoids
  {formid:"<npc>", field_path:"Class",        value:"85BCE3:Requiem.esp"},           # REQ_Class_Bandit_SwordShield
  {formid:"<npc>", field_path:"CombatStyle",  value:"03BE1B:Skyrim.esm"},            # role combat style (often vanilla)
  {formid:"<npc>", field_path:"DefaultOutfit",value:"<REQ outfit>:Requiem.esp"},     # gear drives much of the difficulty
  {formid:"<npc>", field_path:"Perks", verb:"ReplaceAll", values:["<combat perks from the analogue>"]},
  {formid:"<npc>", field_path:"ActorEffect", verb:"Add", value:"93369F:Requiem.esp"} # REQ_Trait_Tempering_* (tempers its gear)
]
```

**Class is the balance spine** — Requiem's `REQ_Class_*` set has `StatWeights` that distribute
AutoCalc stats by role (Bandit Health 4/Stamina 6, Guard 5/5, **Slighted 1/0/0** = the weak tier).
Pick the class whose role + weapon + strength matches; the rest of the difficulty follows from class,
level, perks, and gear. Combat perks are **vanilla `Skyrim.esm` skill perks** the analogue carries
(escalating with tier) plus, on stronger actors, Requiem skill-tree perks (`REQ_OneHanded_*`,
`REQ_Conjuration_Empower_*`); copy the analogue's set. The `ActorEffect` carries Requiem
**`REQ_Trait_Tempering_<role>_<tier>`** marker spells that temper the NPC's worn gear — match the
analogue's. **Do not hand-add the armor-penetration / armor-weight / arrow-recovery perks or the
racial trait perk — those are Reqtificator-assigned** (see `## Common mistakes` and
`references/perks.md`).

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
one combat perk.

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

- **Patch combatants; skip the rest.** Civilians, children, quest-givers, vendors-with-no-fight, and
  conjured/summoned actors stay as authored. When the role is genuinely ambiguous (a guard who also
  trades, a "boss" who's really a quest NPC), say what you checked and ask rather than guess.

- **Fixed level, remove `PCLevelMult` — except followers.** De-levelling is the core of Requiem
  balance; a non-follower must get a flat level and lose the multiplier. A **follower** keeps
  `PCLevelMult` (with its `CalcMinLevel`/`CalcMaxLevel`) so it scales with the player.

- **Bosses get buffed, not normalized.** Recognize the unique heavily-buffed actor and make it
  strong (high level, strong class/style, rich perks, a `REQ_Trait_*` for signature toughness —
  reuse Requiem's or author one). Don't flatten it to a generic tier.

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
- **Under-buffing a boss** to a generic tier — recognize it and buff it (see Workflow D).
- **Removing or replacing a *modded* spell/perk/item** (augment instead) — and the inverse, **leaving
  a *vanilla* spell/perk** in place instead of swapping in the Requiem analogue.
- **Patching `Factions` / `AIData` / mod-authored traits.** Out of scope; leave them.
- **Adding a redundant generic class** (a `DremoraClass`-on-everything reflex) instead of the
  role-correct `REQ_Class_*`.
- **Replicating traits onto a recognized-race creature's NPC.** The race carries them; the NPC needs
  almost nothing. Over-patching it duplicates or fights the race layer.
- **Leaving a `REQ_NULL_*` reference in the record.** These are inert stubs Requiem retired; houseCARL
  resolves their EditorIDs live (Requiem is a master of the read), so scan `Perks`/`ActorEffect`/
  `Keywords`/inventory/`Template` and **strip every one** — never carry or add a `REQ_NULL_*`. See
  `references/housecarl-recipes.md` and the masters/`REQ_NULL` rule carried by the `requiem-patching`
  skill.
- **Designing a spell here.** Link the Requiem spell; route MGEF/effect design to the `requiem-magic-patching` skill.

## Checklist

Before finishing an NPC override, confirm:

- [ ] **Role identified** and it's a combatant (not a civilian/helper/summon).
- [ ] **Level** fixed + `PCLevelMult` removed (followers: kept).
- [ ] **Flags** correct (Respawn for generic spawns; Unique/Protected/Essential by role; AutoCalcStats
      for humanoids, explicit Health/Magicka/Stamina offsets for creatures/bosses).
- [ ] **Class** = the role-correct `REQ_Class_*`; **CombatStyle** matches the role.
- [ ] **Perks** = the analogue's combat perks (vanilla + Requiem skill perks); **no** Reqtificator-
      assigned perks hand-added.
- [ ] **Trait bridge:** recognized-race creature → nothing (Reqtificator handles it); new-race
      creature → retarget `Race` or hand-add the physique perk.
- [ ] **ActorEffect** = the analogue's `REQ_Trait_Tempering_*` (and a `REQ_Trait_*` for a boss).
- [ ] **Spells/perks:** modded ones augmented (not removed); vanilla ones replaced with the analogue.
- [ ] **DefaultOutfit / DeathItem** links set to the Requiem analogue's (contents → the `requiem-leveled-list-patching` skill).
- [ ] **Template + TemplateFlags** set when templating onto a Requiem base.
- [ ] **Appearance fields untouched**; `Factions`/`AIData`/mod traits untouched.
- [ ] **Masters correct** — houseCARL Add+Sorts them from referenced forms the xEdit way; verify the
      `masters:` read-back. `Requiem.esp` auto-masters when a Requiem class/perk/spell is referenced
      (force it in by referencing a real Requiem form when you specifically need it).
- [ ] **No `REQ_NULL_*` remains** in any field (`Perks`/`ActorEffect`/`Keywords`/inventory/`Template`)
      — houseCARL resolves their EditorIDs live; strip or replace each. See `references/housecarl-recipes.md`.
- [ ] Routed onward: loot/leveled-list contents → the `requiem-leveled-list-patching` skill; spell *design* → the `requiem-magic-patching` skill;
      script-based follower registration → the `requiem-script-patching` skill.

## Notes

- **Authority** = houseCARL's live conflict winner. NPC **balance** resolves to `Requiem.esp`,
  **`USMP - Requiem.esp`** (an in-scope Requiem compatibility patch that carries Requiem's values
  forward — the winner for many named actors), or a Requiem addon (`Requiem - Minor Arcana - *`,
  `Requiem - Magic Redone.esp`, `Requiem_VampireCollection.esp`, WAR). **Appearance** resolves to a
  separate visual winner. There is **no** active Authoria NPC input-merge (`Authoria - NPC Merge` is
  disabled) — unlike the race domain, do not expect an `Authoria - Master Patch` NPC winner. Read the
  live winner per record and the conflict tree tells you which source owns balance vs appearance.
- **houseCARL writes directly into active patches via `into=`** — author straight into
  `Requiem NPC patching`; verify a brand-new patch via the `bulk_apply` read-back until it's refreshed.
  The `where=` filter helps archetype scans (e.g. `where="Configuration.Level.Level >= 30"`).
- This skill sets the NPC frame and links Requiem forms. **Race** traits → `requiem-race-patching`;
  **spell/MGEF design** → the `requiem-magic-patching` skill; **leveled-list contents** → the `requiem-leveled-list-patching` skill;
  **script-based follower registration** → the `requiem-script-patching` skill.
