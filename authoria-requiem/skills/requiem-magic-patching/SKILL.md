---
name: requiem-magic-patching
description: Patch a new or modded spell, magic effect, or enchantment for Requiem (Magic Redone) via houseCARL — derive the school, tier, cast type, magicka cost, effect magnitude and duration, keywords, the tier-classifier perk, and the enchant/staff/scroll effect from Requiem's own comparable magic, then emit a direct ESP override the Reqtificator can rebalance. Use when the user wants to patch a spell for Requiem, balance a modded spell, enchantment, staff, or scroll, set Magic Redone cost or magnitude, fix a spell that costs too little or too much magicka, set a spell tome's value, design an enchanted-weapon, apparel, or elemental-arrow effect, or fit new magic into Requiem's schools and perk tiers. Load this before touching an MGEF, SPEL, or ENCH record, not after the spell breaks Requiem's magic economy.
---

# Requiem Magic Patching

## Overview

This skill patches the magic records — `MagicEffect` (MGEF), `Spell` (SPEL), and `ObjectEffect`
(ENCH) — so a new or modded spell, enchantment, staff, scroll, or elemental hit is consistent with
Requiem as redesigned by **Magic Redone** (MR): the right school and tier, a magicka cost that fits
Requiem's economy, effect magnitude/area/duration that match the tier, the keywords and flags MR and
the Reqtificator expect, and the **tier-classifier perk** that tells Requiem what the spell *is*. The
output is a **direct ESP override** authored with houseCARL `create_record`/`set_field`/`bulk_apply`,
because the Reqtificator's auto-balance pass runs over real records.

The method is **live-analogy, never invented numbers.** Requiem (via MR) already defines a spell for
nearly every school × tier × delivery, an enchantment for every element × tier, and a tome value for
every tier. Find Requiem's own comparable, read its winner, and derive from it. The mined ladders in
`references/` are a cross-check and a starting point; a live read of the comparable is always the
authority.

For the full map of what Magic Redone actually contains — every record class (MGEF 1413, ENCH 1029,
COBJ 2955 craftables, WEAP/SCRL/BOOK/ARMO/PERK/PROJ/EXPL/HAZD/…), the new damage types, the crafting
systems, pre-enchanted gear, and the delivery/FX chain — see `references/mr-content-survey.md`. It is the
"know the whole mod" overview; the other references drill into each area.

Magic splits into **three layers** — know which you are touching (`references/keywords.md`):

1. **Record-side (you author this):** the MGEF/SPEL/ENCH design below.
2. **Reqtificator-assigned (never hand-stamp):** the global `RFTI_All_*` rescaling perks (absorb,
   poison, ward, persistent-spell). Added at build from keywords/conditions, not carried on a record.
3. **Script runtime (`Nox_*`, → `requiem-script-patching`):** only **special mechanics** (bound items,
   teleport, mind-control, weather, scroll-crafting, multi-spell tomes) need a Papyrus script. A plain
   damage/heal/buff spell scales through the engine `PowerAffectsMagnitude` flag — **no script.** Carry
   the record-side marker; route the runtime to the `requiem-script-patching` skill.

Out of scope, cross-referenced: the **weapon/staff/ammo frames** (`requiem-weapon-patching`,
`requiem-ammo-patching` link the `ObjectEffect`; you design it); the **placement** of tomes/staves/
scrolls in vendor and loot lists (`requiem-leveled-list-patching`); the **Nox script runtime**
(`requiem-script-patching`).

## First step

Confirm houseCARL's authority is fresh, then identify what you are patching.

1. **Freshness probe.** Read Iron Sword and confirm the winner is `Requiem.esp`:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   The winner must be `Requiem.esp`. If it is `Requiem for the Indifferent.esp` or any `Authoria - *`
   plugin, the resolver is stale — re-point houseCARL at your own Requiem MO2 instance and re-probe
   (this skill's reference data was mined on the author's Authoria instance):

   ```
   housecarl_set_mo2_instance path="<your MO2 instance>"
   ```

   Then confirm a magic record resolves to MR (e.g. `012FCD:Skyrim.esm` Flames →
   `Requiem - Magic Redone.esp`).

2. **Classify what you are patching** — the branch decides the comparable:
   - **Spell** (SPEL the player/NPC casts, or a spell tome teaches) → the `## Workflow`.
   - **Weapon enchantment** (charge-based ENCH) → workflow + the weapon section of
     `references/enchantments.md`.
   - **Apparel enchantment** (constant-effect ENCH) → the apparel section.
   - **Staff effect** (the spell the `Nox_KW_Staff_<School><Tier>` frame casts) → the staff section
     (the staff *item* is `requiem-weapon-patching`).
   - **Elemental projectile hit** (an arrow/bolt's explosion effect) → the projectile section.
   - **Alchemy effect** (potion/poison MGEF) → the alchemy section.
   - **Scroll / shout** → the `## Notes` sub-cases (scrolls mirror their spell; shouts are niche).

## Workflow

### 1 — Identify school, tier, and delivery

Every Requiem spell encodes this in its EditorID: `REQ_<School><Tier>_<Element/Type>_<Delivery>`
(e.g. `REQ_Destruction3_Fire_AimedExp`, `REQ_Restoration2_Healing_Self`). **Tier 1–5 = Novice /
Apprentice / Adept / Expert / Master.** Delivery is the `CastType` (Concentration / FireAndForget) +
`TargetType` (Self / Aimed / TargetActor / TargetLocation). Decide all three for the new spell from
what it does — a single-target fire bolt is Destruction, tier by power, FireAndForget + Aimed.

**There are only five schools, and Magic Redone adds none.** `MagicSkill` is the engine's fixed
`ActorValue` enum (Alteration, Conjuration, Destruction, Illusion, Restoration — plus Enchanting as a
craft skill), and the Mastery perk tree has exactly five school roots. A mod that calls itself a "new
school" (Sonic, Constellation/star, Holy Templar, nature magic, Apocalypse spells…) is **reclassified
into one of the five** — pick the closest fit and set `HalfCostPerk` to that school's tier perk.

**Within each school, classify the spell's *archetype*, not just its school.** Each school holds many
sub-spell families — Destruction has Fire/Frost/Shock **plus Venom (poison), Arcane, Entropic, Absorb**;
Restoration has Healing/Ward **plus TurnUndead, Sun, an offensive Poison line, Dispel, WeaknessMagic**;
Conjuration has **Bound, Daedra, Undead, Reanimate, Spirit, Banish, SoulTrap, Oblivion, Necrotic,
Teleport**; Illusion has the mind effects **plus Shadow, Sound, Invisibility/Chameleon, Sleep, Command**;
Alteration has **Armor/Flesh, Shields, Telekinesis, Paralyze, Physical force, elemental Weakness,
Transmute, Wind, Ash, Enlarge/Shrink, Etherealize**. An effect-type can live in more than one school —
**poison** is Destruction `Venom` *and* Restoration `Poison` *and* alchemy; **undead** is Conjuration
(raise) *and* Restoration (Turn/Sun). The full catalog with representative comparables and which
archetypes are `Nox_*`-scripted is in `references/spell-archetypes.md` — consult it to pick the right
comparable.

### 2 — Find Requiem's comparable

Query MR's spell of the **same school + same archetype + nearest tier + same delivery** (archetype
catalog in `references/spell-archetypes.md`):

```
housecarl_cross_plugin_query type="Spell" plugins=["Requiem - Magic Redone.esp"] editorid_contains="Destruction3_Venom" limit=80
```

Narrow by archetype + tier (`editorid_contains="Restoration2_Poison"`, `"Conjuration2_Undead"`). Cost
and magnitude vary by *archetype and delivery*, not by tier alone (a tier-3 conjuration summon costs far
more than a tier-3 fire bolt; a poison DoT is shaped unlike a fire bolt), so match the archetype and the
delivery, not just the school and number.

### 3 — Read the comparable's winner

```
housecarl_batch_record_detail formids=["012FD0:Skyrim.esm"] \
  fields=["Name","BaseCost","CastType","TargetType","ChargeTime","Flags","HalfCostPerk","Effects"] depth=3
```

Read the **winner** (MR folds over base Requiem). Read its `Effects[i].BaseEffect` MGEF and
`Effects[i].Data` (Magnitude/Area/Duration) too — that is the shape to mirror.

### 4 — Derive cost, charge, and magnitude

- **Cost is explicit.** SPEL carries `Flags = ManualCostCalc`, so `BaseCost` is read and set directly
  — there is **no cost formula**. Take the comparable's `BaseCost`. Cost ladders live in
  `references/cost-and-magnitude.md`, but they are per-school-per-delivery (Destruction T1 conc 40 /
  T2 FaF 90 / T3 240 / T4 495; Conjuration is far higher; Alteration/Illusion utility far lower).
- **ChargeTime ladder** (clean across schools): T1 conc 0 / T1 FaF 0.25 / T2 0.5 / T3 0.75 / T4 1.0;
  rituals 3+. Requiem gives master nukes a long charge as the anti-spam lever — set it.
- **Magnitude/area/duration** — copy the comparable's `Effects` shape. Requiem spells are usually
  **multi-effect**: a primary damage/heal effect + a secondary (slow, lingering taper) + sometimes a
  perk-gated grandmaster effect. Each effect's magnitude scales with skill at runtime via the MGEF's
  `PowerAffectsMagnitude` flag — set the *base* magnitude from the comparable's tier.

### 5 — Set keywords and flags (load-bearing)

Read them off the comparable's MGEF and replicate (full vocab + FormIDs in `references/keywords.md`):

- **Element keyword** on a damage MGEF: `MagicDamageFire 01CEAD` / `MagicDamageFrost 01CEAE` /
  `MagicDamageShock 01CEAF` (Skyrim.esm) — drives resistance and the Pyromancy/Cryomancy/Electromancy
  perks. Set `ResistValue` to match (`ResistFire`/`ResistFrost`/`ResistShock`/`Poison`/`MagicResist`).
- **Requiem markers:** `REQ_SpellConcentration 2FFEAD` (concentration effects), `REQ_NoDurationScaling
  412EDF` (opt out of duration scaling) — copy from the comparable.
- **MGEF `Flags`** are meaningful: `Hostile`, `Detrimental`, `FXPersist`, `NoDeathDispel`,
  `PowerAffectsMagnitude` (the skill-scaling switch), `NoArea`; self-buffs/heals/summons add
  `IgnoreResistance`, `NoAbsorbOrReflect`. Mirror the comparable exactly.
- **Do NOT add the `RFTI_All_*` rescaling perks** (absorb `962799`, poison `962798`, ward `682FB5`,
  NPC-persistent `AD3977`). The Reqtificator assigns those at build — see `## Judgment`.

### 6 — Set the tier-classifier perk (`HalfCostPerk`)

This is the one field that matters most. A spell's `HalfCostPerk` names a `REQ_<School>_Mastery_<NNN>`
perk that simultaneously sets the spell's **school and tier**, gates **learning** (Requiem's
perk-gated learn system), and grants half-cost at mastery. Get it right and Requiem treats the spell
correctly; get it wrong and the spell is mis-classified. The matrix (all `:Skyrim.esm`):

| School | Novice | Apprentice | Adept | Expert | Master |
|---|---|---|---|---|---|
| Destruction | 0F2CA8 | 0C44BF | 0C44C0 | 0C44C1 | 0C44C2 |
| Restoration | 0F2CAA | 0C44C7 | 0C44C8 | 0C44C9 | 0C44CA |
| Conjuration | 0F2CA7 | 0C44BB | 0C44BC | 0C44BD | 0C44BE |
| Alteration  | 0F2CA6 | 0C44B7 | 0C44B8 | 0C44B9 | 0C44BA |
| Illusion    | 0F2CA9 | 0C44C3 | 0C44C4 | 0C44C5 | 0C44C6 |

For a spell that wants a school's specialization scaling (Pyromancy, Necromancy, …), also carry that
school's specialization keyword so the perk applies — but **hook into Requiem's existing perks; do not
author new perk trees** (`references/perks-and-tomes.md`).

### 7 — The spell tome (BOOK) and scrolls

If the mod ships a tome, patch the BOOK: keep `Type = BookOrTome` and `Teaches` → the spell, set
`Weight = 1`, and set **`Value` to Requiem's tier ladder** — Novice 100 / Apprentice ~300–400 / Adept
600 / Expert 800 (≤1000) / Master 2000 (verified identical across every MR-patch addon). The tome has
no learn-gate field; learning is gated by the spell's `HalfCostPerk` tier. **Placement of the tome in
a vendor/loot list → `requiem-leveled-list-patching`.** A **scroll** (SCRL) is a one-shot cast of the
spell at its tier: `Weight 0.5`, a modest tier-scaled `Value`, charge mirroring the spell.

### 8 — Enchantments and staves

See `references/enchantments.md` for the full models:

- **Weapon enchant** (`EnchantType=Enchantment`, `CastType=FireAndForget`, `TargetType=Touch`):
  per-cast `EnchantmentCost` by tier (≈10/20/25/35/40/50); the charge **pool** is the WEAP's
  `EnchantmentAmount` (≈500×tier) set by the weapon skill. Magnitude on the effect.
- **Apparel enchant** (`CastType=ConstantEffect`, `TargetType=Self`): no charge pool; magnitude on the
  MGEF; passive while worn.
- **Staff:** the WEAP carries `EnchantmentAmount` + `Nox_KW_Staff_<School><Tier>`; the staff's
  `ObjectEffect` casts the school's spell effect at that tier's power.
- **Elemental projectile hit:** the element lives on the **explosion**, not on weapon/ammo damage —
  arrow `AMMO` → `PROJ` (`Explosion`) → `EXPL` (Force/Radius) → `ObjectEffect`/MGEF. You design the
  EXPL→MGEF; `requiem-ammo-patching` links the AMMO/PROJ.
- **Delivery/FX records** (`references/mr-content-survey.md` §7): a damage spell's MGEF wires
  `Projectile`/`Explosion`/`Hazard`. An **AoE** spell links an `EXPL` on its MGEF (the EXPL is a
  force+visual shell — `Damage = 0`; the area damage comes from the MGEF). A **wall/rune/circle** links a
  `HAZD` whose `Spell` re-applies the damage MGEF on a tick. Reuse a tier-matched Requiem PROJ/EXPL/HAZD
  or clone its profile; don't invent physics.
- **Pre-enchanted gear & crafting** (`mr-content-survey.md` §5–§6): MR factors enchanted items as
  {base item} × {ENCH} × {magnitude tier} — to add one, point the item's `ObjectEffect` at an ENCH
  (armor: no charge; staff: ~1000). If it should be **craftable**, add a `COBJ` (forge / DLC2 staff
  enchanter / scroll tool) gated `HasSpell` (± `HasPerk`); the COBJ assembles the finished item, it
  never embeds the effect. Placement of the item into vendor/loot lists → `requiem-leveled-list-patching`.

### 9 — Emit the override

Author with houseCARL into a patch plugin. Copy-ready `create_record`/`set_field`/`bulk_apply` shapes
(composing the `Effects` list with per-effect `Data` and `Conditions`) are in
`references/housecarl-recipes.md`. Verify the `masters:` read-back includes `Requiem.esp` / MR and
that no `REQ_NULL_*` reference remains.

## Judgment

### `HalfCostPerk` is the classifier — set it deliberately

The MR-patch addons (real "new magic patched for Requiem" cases) all do the same thing: they re-point
`HalfCostPerk` to the Requiem Mastery perk for the spell's intended school and tier, even **reclassifying
the school** (sonic magic → Alteration, star magic → Restoration). When you patch a modded spell, decide
its Requiem school + tier and set `HalfCostPerk` accordingly — that, the cost, and the charge time are
the core of the patch. See `references/worked-examples.md`.

### The three-layer split — don't hand-stamp Layer 2, route Layer 3

- **Never add an `RFTI_All_*` rescaling perk** to a spell or MGEF. They are Reqtificator outputs (the
  `RFTI_` prefix marks the whole assigned family, including `RFTI_Trait_*` creature traits). Adding one
  fights the build pass — the analog of hand-stamping the weapon damage-type keyword.
- **A plain damage/heal/buff spell needs no script.** Only a special mechanic (bound item, teleport,
  mind-control, weather, scroll-craft, multi-spell tome, weakness/rune debuff) carries a `Nox_*` script.
  If the modded spell does one of those, carry the record-side marker and **route the runtime to
  `requiem-script-patching`** — do not try to author the Papyrus here.

### REQ_NULL — strip on items, replace on creatures

`REQ_NULL_*` effects are retired vanilla abilities/powers/perk-effects (e.g. `REQ_NULL_AbResistFire`,
`REQ_NULL_RaceOrcBerserkEffect`). On a **player-side spell or an item enchantment**, strip a NULL
reference. But when a modded **creature or NPC** carries a now-NULLed vanilla resistance/power, Requiem
**replaced** it with a race trait spell or perk — route the replacement (`requiem-race-patching`),
don't just delete the protection. Never carry a `REQ_NULL_*` forward; never add one.

### Uniques and novel mechanics

A bespoke artifact enchantment or a spell with no Requiem analog keeps its custom effect — derive the
*structure* (cost/charge/keywords/flags) from the nearest comparable, set the tier perk to the closest
school/tier, and state the assumption. When you cannot find a clean comparable, say so — name what you
checked — rather than inventing a number.

## Common mistakes

- **Inventing a magicka cost.** Cost is explicit (`ManualCostCalc`); read it from the same-school,
  same-delivery, same-tier comparable. Don't compute it — there is no formula.
- **Wrong or missing `HalfCostPerk`.** This mis-classifies the spell's school/tier and breaks learning
  and cost. It is the single most important field.
- **Hand-stamping an `RFTI_All_*` rescaling perk.** It is a Reqtificator output, not source data.
- **Trying to script a plain spell.** Damage/heal/buff scaling is the engine `PowerAffectsMagnitude`
  flag — no Papyrus. Only special mechanics route to the `requiem-script-patching` skill.
- **Bumping a spell tome's value freely.** Tome value tracks the spell's tier (100→2000), not the
  mod's original price.
- **Deleting a creature's NULLed resistance.** On actors, replace with the Requiem trait, don't strip.
- **Reading the wrong winner.** A stale resolver reads the disabled Authoria overlay — run the
  freshness probe first.

## Checklist

Before finishing a magic override, confirm:

- [ ] **School** set on the MGEF `MagicSkill` (the school ActorValue); `ResistValue` matches the element.
- [ ] **CastType + TargetType** match the delivery; SPEL and MGEF agree.
- [ ] **BaseCost** taken from the comparable (explicit, `ManualCostCalc`); **ChargeTime** on the tier ladder.
- [ ] **Effect list** mirrors the comparable's shape (primary + secondary/taper), each with `Data`
      magnitude/area/duration and any `Conditions`.
- [ ] **Element + Requiem-marker keywords** present; **MGEF `Flags`** (incl. `PowerAffectsMagnitude`) mirrored.
- [ ] **`HalfCostPerk`** = the correct `REQ_<School>_Mastery_<tier>` perk; specialization keyword if needed.
- [ ] **No `RFTI_All_*` rescaling perk** hand-added; **special-mechanic `Nox_*` script routed to the `requiem-script-patching` skill.**
- [ ] **Enchantment:** weapon = per-cast `EnchantmentCost` by tier (pool on the WEAP); apparel = constant, no pool.
- [ ] **Spell tome:** `Teaches` → spell, `Value` on the tier ladder, Weight 1. **Placement → `requiem-leveled-list-patching` skill.**
- [ ] **Masters** include `Requiem.esp` / MR (reference a real Requiem form); **no `REQ_NULL_*` remains.**

## Notes

- **Authority** = houseCARL's live conflict winner. Magic winners come from `Requiem - Magic Redone.esp`
  (MR; spells, effects, enchantments, staves, perks, scrolls), base `Requiem.esp` (shouts + base), and
  `Requiem - Alchemy Redone.esp` (potions/poisons), plus MR cross-patches and the **MR-patch addons**
  (`Requiem - Constellation/Obscure/Dark Hierophant/Holy Templar/Sonic Mage Magic - Magic Redone
  Patch.esp`, `Wildwaker Magic - Requiem Magic Redone Patch.esp`, Apocalypse compat) — the best worked
  examples of new magic patched for Requiem. Some spell perks resolve to `Requiem - MR Special Feats
  Patch.esp` / `Requiem - MR WAR Resist and Regen Tweak Patch.esp`. The disabled Authoria overlay is
  excluded automatically.
- **Scrolls** (`REQ_Scroll_<School><Tier>_<Type>_<Delivery>`, Weight 0.5, tier-scaled value, charge =
  the spell's): a single-use cast of the spell at its tier; design the same MGEF, route value/placement
  to the `requiem-leveled-list-patching` skill.
- **Shouts** (SHOU, owned by `Requiem.esp`) are a niche sub-case: a SHOU links three Words of Power to
  three SPEL (whose effects you design here) plus a recovery time; word-unlock and placement are
  out of scope.
- **Alchemy** (potion/poison MGEF, `Requiem - Alchemy Redone.esp`): `PeakValueModifier` archetype,
  `FireAndForget`/`Self`, `Flags = PowerAffectsMagnitude, Recover`, `MagicAlch*` keywords
  (`MagicAlchHarmful` = poison); potency scales with Alchemy skill and `RFTI_All_PoisonRescaling`
  (Layer 2). See `references/enchantments.md`.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is later
  run through the Reqtificator.
