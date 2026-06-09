# Race Keyword Vocabulary

Keywords on a `RACE` are load-bearing: they classify the actor (`ActorType*`), gate Requiem
mechanics (knockdown immunity, unperked skills, diet), and the Reqtificator reads the race-FormID
(not a keyword) for trait perks. All FormIDs verified live 2026-06-08. Where a keyword isn't listed,
**read it off the comparable** rather than guessing.

## Table of contents
- [The split: carried-on-RACE vs Reqtificator-assigned-to-NPC](#the-split-carried-on-race-vs-reqtificator-assigned-to-npc)
- [ActorType (classification)](#actortype-classification)
- [Requiem race keywords](#requiem-race-keywords)
- [Playable racial-skill (unperked) keywords](#playable-racial-skill-unperked-keywords)
- [Knockdown](#knockdown)
- [Diet (Food & Beverages Redone)](#diet-food--beverages-redone)
- [Attack data (knockdown / poison / spit)](#attack-data-knockdown--poison--spit)

## The split: carried-on-RACE vs Reqtificator-assigned-to-NPC

The race domain's version of the source-vs-assigned rule the item skills
(`requiem-weapon-patching`/`requiem-armor-patching`/`requiem-ammo-patching`) established:

| Carried ON the RACE (set these) | Assigned by the Reqtificator TO the NPC (don't stamp) |
|---|---|
| `ActorType*` classification | the `incomingDamageModifier` trait **perk** (per-NPC, by race-FormID match) |
| trait **spells** in `ActorEffect` (Resist/Armor/Healing/…) | |
| `ActorEffect`-borne abilities (Heritage/Blood/Physique/Cuisine) | |
| race keywords (knockdown, unperked skills, diet, drops-blood) | |

The split is **not** "inputs vs outputs" like weapons/armor — here the RACE carries almost everything,
and the *only* thing the Reqtificator adds is the per-NPC trait perk (Layer B). Trait **spells** are
the race's own data and propagate to its NPCs by the engine; the trait **perk** is the one piece the
auto-pass owns. See `creature-traits.md`.

## ActorType (classification)

The first keywords you check to classify humanoid vs creature (vanilla `Skyrim.esm`):

| Keyword | FormID | Keyword | FormID |
|---|---|---|---|
| ActorTypeNPC (humanoid) | 013794 | ActorTypeCreature | 013795 |
| ActorTypeUndead | 013796 | ActorTypeDaedra | 013797 |
| ActorTypeAnimal | 013798 | ActorTypeDwarven | 01397A |
| ActorTypeTroll | 0F5D16 | ActorTypeDragon | 035D59 |
| ActorTypeGiant | 10E984 | ActorTypeGhost | 0D205E |
| ActorTypeHorse | 026110 | ActorTypeFamiliar (= "spirit") | 10EAD7 |

All `Skyrim.esm`, verified live. The config's state-trait `spirit` keyword is `ActorTypeFamiliar
10EAD7`; the `ghost` state keyword is `ActorTypeGhost 0D205E`. Read the analogue's `Keywords` for the
exact set a given creature carries.

## Requiem race keywords

| Keyword | EditorID | FormID | Meaning |
|---|---|---|---|
| drops blood | REQ_DropsBloodKeyword | 586728:Requiem.esp | actor bleeds (on most living races incl. all playable + trolls) |
| minor knockdown immunity | REQ_MinorKnockdownImmunity | 5F367F:Requiem.esp | partial resist to being knocked down (e.g. trolls) |
| beast race | IsBeastRace | 0D61D1:Skyrim.esm | Argonian/Khajiit (vanilla, but Requiem keys diet/claws off it) |
| do not inherit traits | doNotInheritTraits | AD3A4F:Requiem.esp | opt the record OUT of the Reqtificator's trait pass |

## Playable racial-skill (unperked) keywords

The "Unperked Skills" from `Races.md` are keywords that let a race use a skill without its first
perk. Copy the set the analogue carries (all `Requiem.esp`):

| Keyword | FormID |
|---|---|
| REQ_RacialSkills_SneakUnperked | AD3A3A |
| REQ_RacialSkills_CreatePotionsUnperked | AD3A3B |
| REQ_RacialSkills_RechargeWeaponsUnperked | AD3A3C |
| REQ_RacialSkills_PickpocketUnperked | AD3A3D |
| REQ_RacialSkills_LockpickUnperked | AD3A3E |

## Knockdown

Two distinct mechanisms — don't confuse them:

- **Immunity keywords** (on the *defending* race): `ImmuneStrongUnrelentingForce 0172AC:Skyrim.esm`
  = full immunity (Orc "cannot be knocked down"); `REQ_MinorKnockdownImmunity 5F367F:Requiem.esp` =
  partial (trolls). Copy from the analogue.
- **Attack-data knockdown** (on the *attacking* creature, in `Attacks` — see below): the creature's
  own power attacks that stagger/knock down the target.

The old standalone knockdown tweak ESP (dated, drops trait spells) is **superseded by the Master
Patch**, which carries the attack-data faithfully — read the Master Patch winner, never the tweak.

## Diet (Food & Beverages Redone)

The Cuisine races (Argonian, Khajiit, Orc, Bosmer) carry a diet keyword
`Apo_StrongStomach 000B45:Requiem - Food and Beverages Redone.esp` plus a `REQ_Trait_Cuisine_<Race>`
ability spell. Copy whatever the live comparable lists — Food & Beverages Redone is folded into the
Master Patch winner.

## Attack data (knockdown / poison / spit)

A creature's special attacks live in the RACE `Attacks` list. Each `Attacks[i].AttackData` carries
`DamageMult`, `Chance`, `Spell` (the on-hit effect — a knockdown, poison, or debuff spell),
`Flags` (e.g. `PowerAttack`), `Stagger`, `Knockdown`, `RecoveryTime`, plus an `AttackEvent`
(animation tag, e.g. `attackStartForwardPower`). Example (troll forward power attack):
`DamageMult 1.5, Chance 0.3, Spell 75CD1F:Requiem.esp, Stagger 1.25`.

When patching a creature with special attacks: **preserve its `Attacks`** and, if it already has its
own spit/breath/poison spell, leave it and patch *that* spell to match Requiem later (the
`requiem-magic-patching` skill) — don't add a second Requiem attack alongside.
