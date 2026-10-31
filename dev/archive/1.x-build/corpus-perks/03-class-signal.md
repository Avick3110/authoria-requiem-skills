# Corpus probe 03 — How reliable is the CLASS record as a perk signal?

*Live corpus mining through houseCARL against the Requiem MO2 instance. Reads return the live
conflict winner (Requiem for the Indifferent / Reqtificator output wins most contested NPCs); where
the SOURCE-carried value matters, records were read with `plugin="Requiem.esp"`. For the
`requiem-perk-assignment` skill. Owner's standing instruction: **"careful when assigning by class."**
This probe makes that caution operational.*

## TL;DR headline

**Class is an unreliable perk signal and must never be the primary one.** In Requiem's own corpus,
perks are placed by **(weapon/combat archetype) × (encounter tier)**, matched to a live comparable —
*not* derived from the CLAS record. The class field's real job is **skill-VALUE derivation via
AutoCalcStats + the H/M/S pool ratio**, a job it does *only while AutoCalc is on* — and Requiem turns
AutoCalc **off** on every actor it hand-perks. On a huge fraction of the load order the class field is
literal placeholder junk (see the 4229-record Dremora finding below). Use class as at most a weak
corroboration of the archetype bucket, and never on a `Lvl*`/template/placeholder record.

---

## 1. Requiem's class space — true totals

`housecarl_cross_plugin_query type=CLAS`:

| Query | Total |
|---|---|
| `plugins=[Requiem.esp] defined_in=true` (Requiem-**defined** classes) | **43** |
| `plugins=[Requiem.esp]` (defined + vanilla overrides Requiem touches) | **66** (43 defined + 23 vanilla overrides) |

The 43 defined classes are the `REQ_Class_*` / `FZR_Class_*` set: bespoke combat archetypes
(`REQ_Class_Guard_Onehanded`, `REQ_Class_Bandit_Bow`, `REQ_Class_Bandit_GreatSword`,
`REQ_Class_Vigilant_Warhammer`, `FZR_Class_Cultist_Mage`, …) plus named-boss and citizen classes.
The 23 overrides are vanilla classes Requiem re-tuned in place (`Citizen 01326B`, `EncClassDraugrMagic
023C0E` → renamed "Draugr Warlock", `DLC2EncClassRiekling 03BD08`, the Trainer classes, etc.).

**Not overridden by Requiem** (still `winner=Skyrim.esm`), and this matters: `EncClassDremoraMelee
017008`, `EncClassDraugrMelee 023C0C`, `EncClassDraugrMissile 023C0D`, `EncClassFalmer 01CE1E`,
`EncClassBanditMelee 01CE17`, `EncClassAnimalPredator 0131E6`, `CombatWarrior1H/2H` (winner = Minor
Arcana). Requiem leaves the vanilla generic classes alone and does its balancing elsewhere.

### CLAS schema — what a class actually carries

Full-read of ~10 classes. The record has exactly two load-bearing fields plus vestigial training
fields:

- **`SkillWeights`** — dict of all 18 skills, small integer weights. Feeds AutoCalcStats skill-value
  derivation.
- **`StatWeights`** — dict `{Health, Magicka, Stamina}`. The H/M/S pool split ratio.
- **`Teaches = 0`, `MaxTrainingLevel = 0`** on **every** combat class read → the trainer fields are
  vestigial here; classes do not "teach" perks or train. **There is no perk field on a CLAS record at
  all.** A class cannot, by schema, carry a perk.

Sample weights (FormID → `SkillWeights` nonzero / `StatWeights`):

| Class | FormID | Nonzero SkillWeights | StatWeights H/M/S |
|---|---|---|---|
| REQ_Class_Guard_Onehanded | 7F6492:Requiem.esp | 1H 4, Block 4, HeavyArmor 4 | 5/0/5 |
| REQ_Class_Guard_Marksman | 7F6494:Requiem.esp | Archery 5, LightArmor 4, 1H 2, Block 1 | 3/0/7 |
| REQ_Class_Bandit_SwordShield | 85BCE3:Requiem.esp | 1H 4, Block 4, HeavyArmor 4 | 4/0/6 |
| REQ_Class_Bandit_Bow | 879915:Requiem.esp | Archery 5, LightArmor 4, Sneak 2, 1H 1, Block 1 | 4/0/6 |
| REQ_Class_Bandit_GreatSword | 86D2E8:Requiem.esp | 2H 4, Block 4, HeavyArmor 4 | 4/0/6 |
| FZR_Class_Cultist_Mage | AE3980:Requiem.esp | Destruction 5, Alteration 3, Restoration 3, Conj 1, 1H 1 | 5/8/2 |
| EncClassDraugrMagic ("Warlock") | 023C0E:Skyrim.esm | Conjuration 4, Destruction 4, 1H 2, Sneak 2 | 3/3/0 |
| DLC2EncClassRiekling | 03BD08:Dragonborn.esm | 2H 3, Archery 3, LightArmor 2, Block 1, Sneak 1 | 6/0/4 |
| Citizen | 01326B:Skyrim.esm | 1H/Archery/Block/Smith/LightArmor/Sneak/Speech all 1 | 2/0/1 |

The weights **do** encode the archetype (SwordShield → 1H/Block/HeavyArmor; Bow → Archery/LightArmor).
That is exactly why class *looks* like a perk signal — but it is a redundant, lower-resolution echo of
the equipment, and it breaks in all the ways below.

---

## 2. Class ↔ perk correlation — does a shared class mean a shared perk shape?

**No. Perks track archetype × TIER; class encodes only the archetype and is identical across all
tiers.** Structural fact first: on Requiem's bandit actors, `Configuration.TemplateFlags` =
`Stats, Factions, SpellList, AIData, AIPackages, BaseData, Inventory, Script, DefPackList, AttackData,
Keywords` — **`Traits` is absent**, so **Perks, Class, CombatStyle, Race are the NPC's OWN fields, not
inherited.** Perks are placed directly on the record.

One class (`REQ_Class_Bandit_Bow 879915`), two tiers, two different perk sets:

| Actor (FormID) | Tier | # perks | Perks placed |
|---|---|---|---|
| REQ_LookTemplate_EncBandit01TemplateMissile `037C2C` | 01 | 4 | 1H WeaponMastery1, Marksmanship RangedCombatTraining, Block ImprovedBlocking, Evasion Agility |
| REQ_LookTemplate_EncBandit03MissileImperialF `037C2F` | 03 | 10 | tier-01 base **+** Marksmanship EagleEye/Ranger1/PreciseAim, 1H WeaponMastery2, Evasion Finesse/Dodge |

Same class → same perk **trees** (Marksmanship + Evasion + a little 1H/Block), but the **count and
depth scale with encounter tier**, which the class does not encode (the class record is byte-identical
across tiers 01–06). Compare `REQ_Class_Bandit_SwordShield 85BCE3` actor `039CF5`
(EncBandit01Melee1HNordF, tier 01, 5 perks): 1H WeaponMastery1/2, Block ImprovedBlocking, HeavyArmor
Conditioning, 1H HandToHand — a 1H/Block/HeavyArmor shape matching its class weights AND its
heavy-armor shield outfit.

**Equipment resolution beats class resolution.** The Bow bandit's class weights 1H at only 1, yet the
actor carries `REQ_OneHanded_WeaponMastery` (1 rank at tier 01, 2 ranks at tier 03) — because its
inventory includes a **melee sidearm** alongside the bow. The perk set is the **union over every weapon
type the actor actually carries**, at a finer grain than the single headline skill the class implies.
The class would have told you "archer"; the perks tell you "archer who also swings a sword," which is
what the equipment says.

---

## 3. The caution corpus — where class misleads

### 3a. The 4229-record placeholder — "DremoraClass on everything" is literally true here

`EncClassDremoraMelee 017008:Skyrim.esm` (vanilla, un-overridden) is carried by **4229 NPC records**
across 178 plugins — and it is pure placeholder noise on `Lvl*`/base/template actors. Spot-confirmed by
direct read:

| Actor | FormID | Class | Race |
|---|---|---|---|
| LvlDwarvenCenturion | 10FCE6:Skyrim.esm | **EncClassDremoraMelee** | **FoxRace** |
| DLC2LvlRieklingMelee | 01B656:Dragonborn.esm | **EncClassDremoraMelee** | **FoxRace** |
| DLC2LvlRieklingMissile | 01B655:Dragonborn.esm | **EncClassDremoraMelee** | **FoxRace** |
| DLC1LvlGargoyle | 017705:Dawnguard.esm | **EncClassDremoraMelee** | **FoxRace** (flag `UseTemplate`) |

A Dwarven centurion, a riekling, and a gargoyle all "are" Dremora of the Fox race — because on a
leveled/template **base record** the Class and Race fields are junk defaults; the real identity arrives
through template inheritance (`UseTemplate`) or the leveled-spawn wrapper. **On any `Lvl*` / template /
placeholder actor, class is 100% noise.** (Method note: `where=["Class = <FormID>"]` did **not** filter
by equality in this build — it returned the full NPC set; `references=[<class FormID>]` is the reliable
reverse-lookup, but the 4229 total for 017008 was independently spot-confirmed by direct reads, so it is
a real count, not a filter artifact.)

### 3b. Bespoke actors with a borrowed / mismatched class

Even on hand-authored creatures Requiem happily assigns an unrelated class and lets equipment +
combat style + race carry the meaning:

| Actor | FormID | Class (→ meaning) | Reality |
|---|---|---|---|
| EncGiant02 | 030437:Skyrim.esm | **EncClassBanditMelee** | A giant. CombatStyle csGiant, Race Giant. |
| EncSkeleton01AmbushMelee1H | 0524E5:Skyrim.esm | **EncClassDraugrMelee** | A skeleton borrowing the draugr class. |
| EncSkeleton01AmbushMelee2H | 052543:Skyrim.esm | **EncClassDraugrMelee** | Same class as the 1H skeleton. |

### 3c. Class vs equipment disagreement — Requiem follows the equipment

The Bow bandit (§2) is the clean humanoid case: class barely weights 1H, but the carried sidearm earns
the 1H mastery perk. The skeletons (§4) are the clean creature case: the **1H** and **2H** skeleton
carry an **identical** perk set that includes both 1H *and* 2H focus perks — the perks follow the
**race+tier weapon pool**, and the individual record's equipment selects which of those perks actually
fire in play. In no observed case did Requiem place perks that follow the *class* against the
*equipment*.

### 3d. Division of labor — class drives skill VALUES (AutoCalc), perks are placed separately

Confirmed on the tier-03 Bow bandit `037C2F` (`Configuration.Flags` includes `AutoCalcStats`):
`PlayerSkills.SkillValues` are auto-derived straight from the class weights — Archery **32** (weight 5,
the peak), LightArmor **27** (weight 4), 1H **16** (weight 1, still competent), Block/Sneak **16**,
everything else floored at **5**; **all `SkillOffsets` = 0**. So while AutoCalc is on, the class sets
the skill numbers and the H/M/S pool ratio, and the **Perks list is an entirely independent placement**.
Two separate systems — do not conflate them.

**Crucial corollary:** every actor Requiem **hand-perks has AutoCalcStats OFF** (missile draugr, falmer
missile, skeletons, giant all show `Configuration.Flags = Respawn` with no `AutoCalcStats`, and instead
carry a `REQ_Trait_*DerivedAttributes` / natural-armor effect). Once AutoCalc is off, the **class does
not even drive stats anymore** — its skill weights are inert and it is doing *nothing*. On a
fully-authored Requiem enemy the class field is decorative.

---

## 4. Creatures — who carries combat perks, and does the set track equipment or race/tier?

Requiem re-authors creatures into three shapes. **Perk sets track RACE × TIER as a fixed superset, not
the individual actor's equipment.**

| Creature | FormID | Class | AutoCalc | Perks | Nature of perks |
|---|---|---|---|---|---|
| Draugr melee 2H (vanilla-left) | 038A11:Skyrim.esm | DraugrMelee | **ON** | 1 | `REQ_NULL_ExtraDamage25` (nulled placeholder) |
| Draugr missile (Requiem) | 023BEC:Skyrim.esm | DraugrMissile | OFF | **14** | cross-weapon superset (see below) + Marksmanship |
| Skeleton melee 1H (Requiem) | 0524E5:Skyrim.esm | DraugrMelee | OFF | **13** | full 1H+2H focus superset + Damage traits (incl. **Bow**) |
| Skeleton melee 2H (Requiem) | 052543:Skyrim.esm | DraugrMelee | OFF | **13** | **identical set to the 1H skeleton** |
| Falmer missile (Requiem) | 025D28:Skyrim.esm | Falmer | OFF | **21** | full melee+archery+evasion superset; trait `Falmer_DerivedAttributes` |
| Falmer spellsword boss (Requiem) | 06322D:Skyrim.esm | Falmer | OFF | **1** | `REQ_Trait_FalmerPoison2` racial + 3 spells (Healing/Ice Spike/Sparks) |
| Giant (Requiem) | 030437:Skyrim.esm | BanditMelee | OFF | 1 | `REQ_Trait_ArmorPenetration75` racial |
| Troll armored tamed (Requiem) | 011BA9:Dawnguard.esm | AnimalPredator | OFF | 1 | racial trait perk; CombatStyle csTroll |
| Frost troll (vanilla-left) | 0F4C90:Skyrim.esm | AnimalPredator | **ON** | 0 | stats from class + race base |
| Gargoyle (placeholder base) | 017705:Dawnguard.esm | Dremora+Fox | — | 0 | pure template placeholder |

**The superset proof.** The **melee-1H** skeleton `0524E5` carries: OneHanded (Sword/WarAxe/Mace Focus,
PowerfulStrike, SwiftStrikes), TwoHanded (Greatsword/Warhammer/Battleaxe Focus, BarbaricMight,
DevastatingStrike), **and** `REQ_Trait_Damage140_OneHanded`, `Damage150_TwoHanded`,
**`Damage140_Bow`** — bow damage on a melee record. The 14-perk missile draugr `023BEC` is the same
idea: warhammer/waraxe/mace/battleaxe focus perks *plus* full Marksmanship, on one ranged actor. So the
perk profile is **race × tier wide**, deliberately covering weapons the specific record does not equip;
the leveled-spawn weapon + CombatStyle decide which perks matter in play.

**Three creature archetypes for the skill to recognise:**
1. **Placeholder base** (`Lvl*`, `UseTemplate`, Dremora+Fox) — no perks, no signal; identity via template.
2. **Vanilla-left** (frost troll, melee draugr) — AutoCalcStats ON, ~0–1 perks; class weights + race base carry it.
3. **Requiem-authored** — AutoCalc OFF, either (a) a large hand-placed **race×tier weapon superset**
   (draugr, skeleton, falmer archer), or (b) a **trait-only** kit: 1 `REQ_Trait_*` racial (armor-pen,
   poison, natural armor, derived-attributes) + spells (giant, armored troll, falmer boss/caster).

---

## 5. Derived operational doctrine — the precedence rule for `requiem-perk-assignment`

Derive perks for a new NPC by **matching it to a live comparable Requiem actor**, in this precedence:

1. **Equipment + CombatStyle first (primary).** Weapon type(s) in the actor's inventory/outfit and
   armor weight/material decide *which perk trees*. Include **every** carried weapon — the perk set is
   the **union** over the loadout, not just the primary (evidence: Bow bandit gets 1H WeaponMastery for
   its sidearm; skeletons carry both 1H+2H focus perks).
2. **Tier / encounter rank second (depth).** How many perks and how deep scales with the intended power
   tier, *not* with the class (evidence: Bandit_Bow tier 01 = 4 perks vs tier 03 = 10 perks, same
   class). Pick the comparable at the matching tier and copy its perk shape.
3. **Class = weak corroboration only.** Use the class's `SkillWeights` at most to sanity-check that
   your archetype read (from equipment) is in the right bucket. If class and equipment agree, fine. **If
   they disagree, trust the equipment** — Requiem always does (Bow bandit; and see the placeholder /
   borrowed-class cases). Class carries no perk field and cannot be a source of perks.
4. **Distrust class entirely on `Lvl*` / template / placeholder records.** Class=EncClassDremoraMelee +
   Race=FoxRace (or any `UseTemplate` base) is junk — 4229 such records. Resolve the real identity
   through the template/leveled chain before reading anything into class or race.
5. **Respect the AutoCalc division of labor.** Class → skill VALUES + H/M/S ratio via AutoCalcStats;
   perks are placed independently. If the comparable (and your new actor) is meant to be hand-perked
   like a Requiem enemy, expect **AutoCalcStats OFF** with a `REQ_Trait_*DerivedAttributes`/natural-armor
   effect supplying stats — at which point class is inert and must not be leaned on for anything.
6. **Creatures: match by race × tier, not by the individual weapon.** Copy the whole race/tier perk
   superset from a live comparable of the same race and tier (draugr/skeleton/falmer), or the trait-only
   kit for the trait archetype (giant/troll/falmer-caster). Do not tailor a creature's perks to its one
   equipped weapon — Requiem's own actors don't.

**One-line rule:** *derive perks from equipment + tier against a live comparable; use class only to
confirm the archetype bucket, never as the source — and treat class as noise on any templated or
placeholder actor.*

---

## 6. Accounting

- **Enumerations (true totals reported by the tool):** CLAS defined_in Requiem = **43**; CLAS touched
  by Requiem = **66**; CLAS `editorid_contains` — Dremora **2**, Warrior **4**, Draugr **4**. NPC
  reverse-link / editorid scans: DraugrMelee referrers **697**, DraugrMissile referrers **93**,
  EncClassDremoraMelee-carrying NPCs **4229**, EncDraugr05Melee **16**, EncFalmer **65**, Riekling
  **64**, EncGiant **17**, EncTroll **19**, EncSkeleton **64**, Gargoyle **52**.
- **Records full-read:** ~10 CLAS (schema + weights) + ~20 NPC actors (bandit look-templates ×4, draugr
  ×3, falmer ×2, riekling ×2, giant, troll ×2, skeleton ×2, gargoyle, dwarven centurion) + targeted
  perk-list expansions on 6 actors. ~36 records total.
- **Tooling caveat (verified):** `cross_plugin_query where=["Class = <FormID>"]` did **not** constrain
  by FormLink equality in this houseCARL build — it returned the unfiltered NPC set (identical 4229 rows
  for the Dremora and for a Draugr class query). `references=[<FormID>]` is the working reverse-lookup;
  the 4229 Dremora-placeholder total was corroborated by direct reads (LvlDwarvenCenturion,
  DLC2LvlRiekling×2, DLC1LvlGargoyle all confirmed Class=017008, Race=Fox), so it is treated as real.
- **Inferences (not directly read), flagged as such:** (i) that the ~2300 look-template counts per
  bandit class span all races/genders/tiers is inferred from EditorID naming, not a per-record read;
  (ii) that AutoCalc-ON vanilla creatures get their combat power from class weights + race base is
  inferred from the flag + absent perks, not from an in-game stat capture; (iii) exact per-tier perk
  counts were sampled at tiers 01 and 03 only — the monotonic scaling across 02/04/05/06 is inferred.
