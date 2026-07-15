# The class signal (why "careful by class") and creature perk shapes

Mined live. This file makes the class caution operational — the evidence for "equipment first,
class as corroboration only" — and carries the creature-side perk model.

## Table of contents
- [What a CLAS record actually is](#what-a-clas-record-actually-is)
- [Why class cannot be the perk source](#why-class-cannot-be-the-perk-source)
- [The placeholder trap](#the-placeholder-trap)
- [The precedence rule](#the-precedence-rule)
- [Creatures: race × tier supersets and trait-only kits](#creatures-race--tier-supersets-and-trait-only-kits)

## What a CLAS record actually is

Requiem defines 43 classes and overrides 23 vanilla ones. The record carries exactly two
load-bearing fields — **`SkillWeights`** (18 skill weights) and **`StatWeights`** (the H/M/S pool
ratio) — feeding AutoCalcStats skill-value derivation. `Teaches`/`MaxTrainingLevel` are 0 on every
combat class. **There is no perk field on a CLAS record; a class cannot, by schema, carry a perk.**

The weights do echo the archetype (`REQ_Class_Bandit_SwordShield 85BCE3` weights 1H/Block/
HeavyArmor; `REQ_Class_Bandit_Bow 879915` weights Archery/LightArmor) — which is exactly why class
*looks* like a perk signal. It's a lower-resolution echo of the equipment, and it breaks in the
ways below.

## Why class cannot be the perk source

- **Perks scale with tier; class doesn't encode tier.** The same `REQ_Class_Bandit_Bow` sits on the
  tier-01 archer (4 perks) and the tier-03 archer (10 perks) — same class record, byte-identical,
  different perk sets. Structural fact: on Requiem's bandits `Configuration.TemplateFlags` lacks
  `Traits`, so Perks/Class/CombatStyle are the NPC's **own** fields — perks are placed per record,
  per tier.
- **Equipment out-resolves class.** The Bow class weights One-Handed at 1, yet the archer carries
  `REQ_OneHanded_WeaponMastery` — for its melee **sidearm**. The perk set is the union over the
  loadout at a finer grain than the class's single headline skill. In no observed case did Requiem
  place perks following the class *against* the equipment.
- **Class ↔ actor mismatches are routine even on bespoke records:** Requiem's giant carries
  `EncClassBanditMelee`; its skeletons borrow `EncClassDraugrMelee`. Requiem leaves the vanilla
  generic classes un-overridden and does the balancing elsewhere.
- **AutoCalc division of labor.** While `AutoCalcStats` is on, class weights derive the
  `PlayerSkills.SkillValues` and the H/M/S split (verified: tier-03 archer, Archery 32 from
  weight 5, offsets all 0) — and the Perks list is an entirely independent placement. **Requiem
  turns AutoCalc OFF on every actor it hand-perks** (stats arrive via a
  `REQ_Trait_*DerivedAttributes`/natural-armor effect instead) — at which point the class drives
  *nothing at all*. On a fully-authored Requiem enemy the class field is decorative.

## The placeholder trap

`EncClassDremoraMelee 017008:Skyrim.esm` sits on **4,229 NPC records** across 178 plugins as pure
filler: `LvlDwarvenCenturion 10FCE6`, `DLC2LvlRiekling* 01B655/6`, `DLC1LvlGargoyle 017705` all
"are" Dremora of the **Fox race** on their base records. On any `Lvl*` / `UseTemplate` /
placeholder actor, Class and Race are junk defaults — the real identity arrives through the
template or leveled-spawn chain. **Resolve the chain before reading anything into class or race.**

Tooling note: `cross_plugin_query where=["Class = <FormID>"]` does not filter FormLink equality —
use `references=[<class FormID>]` for "who carries this class" (see
`boundary-and-write-shapes.md`).

## The precedence rule

1. **Equipment + CombatStyle first** — which trees (union over every carried weapon; shield; armor
   class).
2. **Tier second** — how many and how deep (match the comparable by tier index).
3. **Class = corroboration only.** If class and equipment agree, fine; if they disagree, **the
   equipment is right**.
4. **Distrust class entirely on `Lvl*`/template/placeholder records.**
5. **Respect the AutoCalc split** — class drives skill values only while AutoCalc is on; perks are
   always a separate placement.

## Creatures: race × tier supersets and trait-only kits

Creature perks track **race × tier as a fixed superset, not the individual actor's equipment.**
The proof reads: the melee-**1H** skeleton `0524E5:Skyrim.esm` and the melee-**2H** skeleton
`052543` carry an **identical 13-perk set** spanning both 1H and 2H Focus lines plus
`REQ_Trait_Damage140_Bow` — bow damage on a melee record. The 14-perk missile draugr
`023BEC:Skyrim.esm` likewise carries every melee Focus family plus full Marksmanship. The perk
profile deliberately covers weapons the record doesn't equip; the leveled-spawn weapon and combat
style decide which perks fire in play. **Copy the whole race×tier shape; don't trim it to the
equipped weapon.**

Three creature archetypes to recognize:

1. **Placeholder base** (`Lvl*`, `UseTemplate`, Dremora/Fox junk fields) — no perks, no signal;
   identity lives in the template chain. Not a comparable.
2. **Vanilla-left** (frost troll `0F4C90`, melee draugr `038A11`) — AutoCalcStats **on**, 0–1
   perks (the draugr's one perk is the retired stub `REQ_NULL_ExtraDamage25` — never carry it);
   class weights + race base carry the actor.
3. **Requiem-authored** — AutoCalcStats **off**, one of two kits:
   - **race×tier weapon superset** (draugr 14, skeleton 13, falmer archer 21 perks), or
   - **trait-only** — a single racial `REQ_Trait_*` perk + spells: giant `030437` =
     `REQ_Trait_ArmorPenetration75`; armored troll `011BA9:Dawnguard.esm` = one racial trait;
     falmer spellsword boss `06322D` = `REQ_Trait_FalmerPoison2` + 3 spells.

For a **new creature**, match the same-race same-tier comparable and copy its kit shape. The
racial `incomingDamageModifier`/physique trait perks stay in the Reqtificator's lane (or the trait
bridge for new races — `requiem-npc-patching` / `requiem-race-patching` own that hand-placement).
