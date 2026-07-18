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

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; if it is the wrong instance, fix it with
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. Then sanity-check Requiem is present:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   `Requiem.esp` must appear in the override chain. Either winner is valid: `Requiem.esp`
   (authoring-style profile, generated overlay disabled) or `Requiem for the Indifferent.esp` /
   a later patch (live profile, Reqtificator output enabled — the normal consumer state). The live
   winner is the authority to derive from; **never** re-point houseCARL because the Reqtificator's
   output wins. Full doctrine: the `requiem-patching` skill's `references/scope-and-authority.md`.

   Then confirm a magic record's chain contains MR (e.g. `012FCD:Skyrim.esm` Flames →
   `Requiem - Magic Redone.esp`).

2. **Classify what you are patching** — the branch decides the comparable:
   - **Spell** (a school spell on the `REQ_<School><Tier>_*` scheme — cast by the player *or* an NPC,
     tome-taught or not) → the `## Workflow`. An
     **ability SPEL granting standing-stone-like passive boons** (a birthsign/doomstone blessing, a
     "Stone" power) additionally co-routes the `requiem-patching` skill's
     `references/standing-stones.md` constraints — its magnitudes follow that model, not a combat
     spell's tier ladder.
   - **Creature-innate magic** (a bite, breath, cloak, or innate elemental attack that *is* the
     creature — not a spell anyone learns) → compare against **`REQ_Creature_*`**, never the tier
     ladder (`references/spell-archetypes.md` → *Creature innates*). They are hand-tuned per
     creature: no cost ladder, **no rider convention**, `ManualCostCalc` inconsistent — so running one
     down the spell lane *over-builds* it. An `_NPC`-suffixed spell is **not** this (see that section).
   - **Weapon enchantment** (charge-based ENCH) → workflow + the weapon section of
     `references/enchantments.md`.
   - **Apparel enchantment** (constant-effect ENCH) → the apparel section.
   - **Staff effect** (the spell the `Nox_KW_Staff_<School><Tier>` frame casts) → the staff section
     (the staff *item* is `requiem-weapon-patching`).
   - **Elemental projectile hit** (an arrow/bolt's explosion effect) → the projectile section.
   - **Alchemy effect** (potion/poison MGEF) → the alchemy section.
   - **Resistance/defensive magic** (grants Resist X, waterbreathing — spell, ability, enchant, or
     potion effect) → check every effect against `references/resistance-map.md` first: the vanilla
     resist MGEFs are all `REQ_NULL`ed, and the live analogue differs by lane.
   - **Scroll / shout** → the `## Notes` sub-cases (scrolls mirror their spell; shouts are niche).

## Bulk pass protocol (whole-plugin jobs)

**Load `references/bulk-pass.md` before starting one** — it carries the per-type sweep calls, the
reconciliation procedure, and the full definition of "patched". The load-bearing points:

- **The enumeration is the work queue, not a sample.** Sweep each record type (Spell, MagicEffect,
  ObjectEffect, Scroll, tome-BOOK) as its own coverage denominator, and **enumerate MGEF first-class** —
  never reach an effect only through the spells that reference it.
- **Never extrapolate across a family.** Rank chains, element triples, enchant-tier runs and tome
  ladders *look* mechanical; members are hand-tuned. Read each member's own record.
- **"Patched" has a definition, and scalars do not meet it.** A SPEL is patched only with **all** of:
  cost + `ManualCostCalc` union, tier `ChargeTime`, correct `HalfCostPerk`, **magnitudes derived from
  the comparable**, **subtype keyword(s)** on the MGEF, **mechanical tier-resolved riders**, the
  primary MGEF's **behavioral keyword/flag signature**, no `REQ_NULL_*`, and its tome priced. Cost +
  charge + flags + perk with the mod's original magnitudes is *cost-normalized*, **not patched** — the
  spell deals the mod's damage at Requiem's price. Anything less goes on the still-open side.
- **"Already Requiem-correct" requires a diff, not an eyeball.** `MagicSkill` + element keyword +
  tier marker is what a vanilla-style mod ships anyway — necessary, not sufficient. Name the archetype
  comparable and show the keyword sets match, or the record is unpatched.
- **The sweep can't answer the questions that matter.** `Keywords`/`Flags`/`Effects` return
  `[list: N item(s)]` — a count that reads like data. Follow up with `housecarl_batch_record_detail`
  at `depth=2` (keywords/flags) or `depth=4` (per-effect magnitudes).
- **Flags writes are unions.** Read the winner's flags and write original-bits + your change, or you
  silently strip `ManualCostCalc` and `PowerAffectsMagnitude`.
- Close every type with **patched + skipped = enumerated**, then the **MGEF cross-check**: every modded
  effect a patched spell casts must itself be dispositioned.

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

Read the **winner** (MR folds over base Requiem; on a live profile the winner may be
`Requiem for the Indifferent.esp` — still the authority to derive from). Read its
`Effects[i].BaseEffect` MGEF and `Effects[i].Data` (Magnitude/Area/Duration) too — that is the
shape to mirror.

### 4 — Derive cost, charge, and magnitude

- **Cost is explicit — and on a modded spell the flag is a WRITE.** 99.3% of MR's castable spells
  carry `ManualCostCalc`; mods ship auto-calc. **Tick `Flags += ManualCostCalc` (as a union) or
  the `BaseCost` you set is ignored** and the engine re-derives cost from magnitudes. There is
  **no cost formula** — take the comparable's `BaseCost`. Ladders + the delivery multipliers
  (Rune ≈2×, AoE ≈2×, ritual 1800 at T5; Conjuration prices by what's summoned) in
  `references/cost-and-magnitude.md`.
- **ChargeTime is rebalanced, not inherited**: Concentration = 0 always; FaF single = 0.25 × tier
  (T2 0.5 … T5 1.25); Touch = half the tier's aimed; master rituals = 3.0. Requiem uses charge as
  the anti-spam lever — a modded master nuke that charges instantly is unpatched.
- **Magnitude is MANDATORY, and it is the balance.** Cost/charge/flags/`HalfCostPerk` are the
  *economy*; `Effects[i].Data.Magnitude` is what the spell actually does. **A spell still carrying the
  mod's original magnitudes is UNPATCHED, not "cost-normalized" — scalars-only patching is the #1
  field failure this skill exists to kill.** Read the comparable's per-effect Data and set from it:

  ```
  housecarl_batch_record_detail formids=["<comparable SPEL>"] fields=["EditorID","Effects"] depth=4
  ```

  `depth=4` is what expands `Effects[i].Data.Magnitude/Area/Duration`; at lower depth you get
  `[EffectData]` and cannot see the numbers. Verified live anchors — Destruction base damage is
  **identical across fire/frost/shock at a tier** (T2 30, T3 32, T5 64); Healing's Respite rider is
  **exactly 50%** of the heal (30→15); Ward's `Ward_Shield` rider is **1:1** with the ward magnitude;
  mage-armor riders are **50% and 20%** of the base at `Duration 60`; summon effects are
  **Magnitude 0, Area 0, Duration 15** (thralls 300). Ladders: `references/cost-and-magnitude.md`.
- **Riders are part of the magnitude work, not decoration.** Requiem spells are **multi-effect**, and
  patching means **ADDING the riders the comparable carries** with **their** magnitudes copied, never
  guessed: Fire→Cremation, Frost→Slow+DeepFreeze, Shock→ElectrostaticDischarge magicka (+stagger on
  projectiles), Venom→DoT, FaF Destruction→Impact Stagger, Healing→Respite, Ward→Ward_Shield,
  perk-gated `_Improved`/`_Potent`, Illusion Break1/Break2. Rider magnitudes are small and easy to
  get wrong by orders of magnitude — `Impact_Stagger` is **0.25**, not 25; `Slow` is **5**, not 50.
  Full per-subtype rider doctrine (incl. the mechanical-vs-cosmetic trap):
  `references/cost-and-magnitude.md`. Each magnitude scales at runtime via the MGEF's
  `PowerAffectsMagnitude` flag — set the *base*, not the scaled number.
- **Any effect that resolves to `REQ_NULL_*` / `REQ_DEPRECATED_*` is a silent no-op to fix now.**
  Modded **resistance** spells are the classic case — all 8 vanilla resist MGEFs are NULLed, and
  the live analogue differs by lane (ability / enchant / potion / castable spell). Re-point each
  dead effect same-lane, same-element via `references/resistance-map.md`; read every effect with
  `resolve_names=true` so the NULLs are visible.

### 5 — Set keywords and flags (load-bearing)

**The keyword+flag signature is BEHAVIORAL — derived from what the effect *does*, not from its element
or its tier.** Same element and same tier, different behavior = different signature. So the derivation
is a three-step diff, not a lookup (full archetype table + FormIDs in `references/keywords.md`):

1. **Classify the effect's behavior:** burst FaF bolt · concentration stream · aimed DoT · lingering
   taper rider · self-buff · magicka-burn / discharge rider · cloak tick · hazard tick.
2. **Read MR's exemplar for *that archetype*** at `depth=2` so `Keywords` actually expands — at
   `depth=1` it prints `[list: N item(s)]`, which is how a missing keyword goes unnoticed.
3. **Diff the modded MGEF against it and add what's absent.** The element keyword swaps per element;
   everything else comes from the archetype.

**`MagicSkill` + element keyword + `MinimumSkillLevel` tier marker is *necessary but not sufficient*.**
That trio is what a competent vanilla-style mod already ships. An MGEF carrying it but lacking the **REQ
behavioral keywords its archetype comparable carries** is **unpatched**, not "already Requiem-correct" —
Requiem's scaling rules key off those keywords, so without them the effect scales as vanilla inside
Requiem's economy. Diff before you conclude anything is already correct.

**Resist the flat rule.** "Damage → `REQ_NoDurationScaling`, concentration → `REQ_SpellConcentration`"
is wrong both ways live: a **taper** rider carries *both*, a **magicka-burn rider** and a **hazard
tick** carry **none**.

- **Element keyword** on a damage MGEF: `MagicDamageFire 01CEAD:Skyrim.esm` / `MagicDamageFrost
  01CEAE:Skyrim.esm` / `MagicDamageShock 01CEAF:Skyrim.esm` — drives resistance and the
  Pyromancy/Cryomancy/Electromancy perks. Set `ResistValue` to match (`ResistFire`/`ResistFrost`/
  `ResistShock`/`Poison`/`MagicResist`).
- **Subtype keyword — on the MGEF, and only where the comparable has one.** MR classifies a spell's
  sub-archetype with a `Nox_KW_<School>_<Subtype>` keyword that its perk routes match on; omit it and
  the perks silently never apply (no error, no log). **These live on the MGEF — MR's SPEL records
  carry no keywords at all** (915 scanned, `Keywords` unset on every one), so never try to write one
  onto a Spell. **Not every subtype has one** (Calm/Fear/Frenzy, Healing, Ward, TurnUndead and mage
  armor use vanilla class keywords instead), so take the comparable's set as-is rather than
  synthesising a name. Per-school table with verified masters: `references/keywords.md`.
- **Requiem behavioral markers — write the master, it is not `Skyrim.esm`:**
  `REQ_SpellConcentration 2FFEAD:Requiem.esp`, `REQ_NoDurationScaling 412EDF:Requiem.esp`,
  `REQ_NoMagnitudeScaling 3FCA4C:Requiem.esp`, `Nox_KW_CloakDamage 007609:Requiem - Magic Redone.esp`.
  These sit next to `Skyrim.esm` element keywords in every effect's list, so a bare FormID gets guessed
  as `:Skyrim.esm` and the write fails. Copy the set from the archetype comparable.
- **MGEF `Flags`** are meaningful and archetype-shaped: damage → `PowerAffectsMagnitude`; timed
  buffs/debuffs → `PowerAffectsDuration`; binary states (invisibility/paralyze/summon) →
  `NoMagnitude`; instant heals → `NoDuration`; plus `Hostile`/`Detrimental`/`FXPersist`/
  `NoDeathDispel`/`NoArea` and the self-buff pair `IgnoreResistance, NoAbsorbOrReflect`. The
  per-archetype table is in `references/keywords.md` — mirror the comparable exactly.
- **`MinimumSkillLevel` on the MGEF is the tier marker** (0/25/50/75/100 = tiers 1–5) — set it to
  the spell's tier; perk-gated GM effects sit at 0 and gate via the perk + `HideInUI`.
- **Conditions**: a plain effect carries none; add them only when the comparable does — creature-
  type gates, perk gates, immunity/resistance checks on riders. Copy the exact rows per
  `references/mgef-conditions.md`; never hand-author a function/parameter combo.
- **Do NOT add the `RFTI_All_*` rescaling perks** (absorb `962799`, poison `962798`, ward `682FB5`,
  NPC-persistent `AD3977`). The Reqtificator assigns those at build — see `## Judgment`.

### 6 — Set the tier-classifier perk (`HalfCostPerk`)

This is the one field that matters most. A spell's `HalfCostPerk` names a `REQ_<School>_Mastery_<NNN>`
perk that simultaneously sets the spell's **school and tier**, gates **learning** (Requiem's
perk-gated learn system), and grants half-cost at mastery. Get it right and Requiem treats the spell
correctly; get it wrong and the spell is mis-classified. The full 5×5 matrix of
`REQ_<School>_Mastery_<NNN>` FormIDs (all `:Skyrim.esm`) is in `references/perks-and-tomes.md` — read
it there rather than recalling one.

**Do not hand a plain damage spell a `Nox_KW_<School>_Perk_*` specialization keyword.** Verified live:
MR's own Fire/Frost/Shock damage effects carry **no** Pyromancy/Cryomancy/Electromancy keyword — those
sit on **cloak / enchant / hazard** delivery forms only, where the perk cannot otherwise find the
effect. Copy whichever the archetype comparable carries; **hook into Requiem's existing perks, never
author new perk trees** (`references/perks-and-tomes.md`).

### 7 — The spell tome (BOOK) and scrolls

**For every spell you patch, reverse-resolve its teaching tome and price it — an un-priced tome leaves
the spell half-patched.** The tome value is part of the spell's balance, not an optional extra: a patched
SPEL whose BOOK still carries the mod's original price is not done. Find the BOOK that `Teaches` the
spell, keep `Type = BookOrTome` and `Teaches` → the spell, set `Weight = 1`, and set **`Value` to
Requiem's tier ladder** — Novice 100 / Apprentice ~300–400 / Adept 600 / Expert 800 / Master 2000,
where each tier is a **band, not a flat number**: summons and utility price above damage at the
same tier (Flames 100 but Bound Sword 200; Flame Atronach 700, Storm Atronach 900) — match a
same-tier same-*role* tome (`references/perks-and-tomes.md`). The tome has no learn-gate field; learning is
gated by the spell's `HalfCostPerk` tier. **Placement of the tome in a vendor/loot list →
`requiem-leveled-list-patching`.** A **scroll** (SCRL) is a one-shot cast of the spell at its tier:
`Weight 0.5`, a modest tier-scaled `Value`, charge mirroring the spell.

### 8 — Enchantments and staves

Full models in `references/enchantments.md`; the delivery/FX and pre-enchanted-gear chains in
`references/mr-content-survey.md` §5–§7. The load-bearing points:

- **Weapon enchant** (`EnchantType=Enchantment`, FaF, Touch): `EnchantmentCost = EnchantmentAmount =
  effect Magnitude`, 1:1 by tier (10/20/25/35/40/50); the charge **pool** is the WEAP's own
  `EnchantmentAmount` (≈500×tier). **Apparel enchant** is `ConstantEffect`/`Self` — no pool.
- **The enchantment adds zero gold value.** Requiem hand-sets every pre-enchanted item's `Value` to
  the base item's value (all ENCH are `NoAutoCalc`); the tier goes into magnitude/cost/pool, never gold.
- **Staff:** the WEAP carries `EnchantmentAmount` + `Nox_KW_Staff_<School><Tier>`; its `ObjectEffect`
  casts the school's effect at that tier. **Elemental projectile hit:** the element lives on the
  **explosion** — `AMMO` → `PROJ` → `EXPL` → `ObjectEffect`/MGEF; you design EXPL→MGEF,
  `requiem-ammo-patching` links AMMO/PROJ. Reuse a tier-matched Requiem PROJ/EXPL/HAZD; don't invent
  physics. **Craftable** items get a `COBJ` gated `HasSpell` (± `HasPerk`), which assembles the
  finished item and never embeds the effect.

### 9 — Emit the override

Author with houseCARL into a patch plugin. Copy-ready `create_record`/`set_field`/`bulk_apply` shapes
(composing the `Effects` list with per-effect `Data` and `Conditions`) are in
`references/housecarl-recipes.md`. Verify the `masters:` read-back includes `Requiem.esp` / MR and
that no `REQ_NULL_*` reference remains.

## Judgment

### `HalfCostPerk` is the classifier — set it deliberately

The MR-patch addons (real "new magic patched for Requiem" cases) all do the same thing: they re-point
`HalfCostPerk` to the Mastery perk for the spell's intended school and tier, even **reclassifying the
school** (sonic magic → Alteration, star magic → Restoration). Decide the modded spell's Requiem school
+ tier and set it accordingly (`references/worked-examples.md`).

### The three-layer split — don't hand-stamp Layer 2, route Layer 3

- **Never add an `RFTI_All_*` rescaling perk** to a spell or MGEF. They are Reqtificator outputs (the
  `RFTI_` prefix marks the whole assigned family, including `RFTI_Trait_*`); adding one fights the
  build pass — the analog of hand-stamping the weapon damage-type keyword.
- **A plain damage/heal/buff spell needs no script.** Only a special mechanic (bound item, teleport,
  mind-control, weather, scroll-craft, multi-spell tome, weakness/rune debuff) carries a `Nox_*`
  script — carry the record-side marker and **route the runtime to `requiem-script-patching`**.

### REQ_NULL — strip on items, replace on creatures

`REQ_NULL_*` effects are retired vanilla abilities/powers/perk-effects. On a **player-side spell or an
item enchantment**, strip a NULL reference. But when a modded **creature or NPC** carries a now-NULLed
vanilla resistance/power, Requiem **replaced** it with a race trait spell or perk — route the
replacement (`requiem-race-patching`), don't delete the protection. Never carry a `REQ_NULL_*` forward.

### Uniques and novel mechanics

A spell with no Requiem analog keeps its custom effect — derive the *structure*
(cost/charge/magnitude/keywords/flags) from the nearest comparable, set the tier perk to the closest
school/tier, and state the assumption. When you cannot find a clean comparable, say so and name what
you checked rather than inventing a number.

## Common mistakes

- **Inventing a magicka cost.** Cost is explicit (`ManualCostCalc`); read it from the same-school,
  same-delivery, same-tier comparable. Don't compute it — there is no formula.
- **Setting `BaseCost` without ticking `ManualCostCalc`.** Mods ship auto-calc spells; without the
  flag the engine ignores your cost entirely. Cost-only patching is the field failure this skill
  exists to kill — cost, flag, charge, magnitudes, riders, and the tome are one pass.
- **Scalars-only patching — the #1 field failure.** Setting `BaseCost` + `ManualCostCalc` +
  `ChargeTime` + `HalfCostPerk` and leaving every `Effects[i].Data.Magnitude` at the mod's original
  value. That is *cost-normalized*, not patched: the spell deals the mod's damage at Requiem's price.
  Magnitudes come from the comparable, read at `depth=4`.
- **Attaching a cosmetic rider instead of the mechanical one.** Illusion's `_Desc_Improved` effects
  are `Archetype.Type = Script`, keyword-less tooltip carriers — attaching one where the `_Improved`
  effect belongs displays an improved effect and applies nothing. The Destruction `_Taper` riders are
  the other shape of this trap: `ActorValue = Fame` at magnitude 0. Verify the rider's `Archetype`
  before counting it (`references/cost-and-magnitude.md`).
- **Guessing a rider's magnitude.** They are small and unforgiving — `Impact_Stagger` is **0.25**
  (not 25) and `Slow` is **5** (not 50). Copy the number from the comparable's effect entry.
- **Giving a plain damage spell a `Nox_KW_<School>_Perk_*` keyword.** MR's own damage effects carry
  none; those keywords tag cloak/enchant/hazard delivery forms. Adding one mis-files the spell.
- **Trying to write a subtype keyword onto the SPEL.** MR spells carry no keywords at all — the
  subtype classification lives on the MGEF.
- **Leaving a patched spell single-effect.** MR's comparable is multi-effect; a modded fireball
  without the Cremation burn and Impact Stagger is under-built, not "already fine."
- **Calling an MGEF "already Requiem-correct" because it has `MagicSkill` + an element keyword + a
  tier marker.** That trio is what a vanilla-style mod ships anyway — necessary, not sufficient. If the
  effect lacks the REQ behavioral keywords its archetype comparable carries, it is unpatched and
  Requiem's scaling never matches it. Diff at `depth=2` before you skip anything (worked negative:
  `references/keywords.md`).
- **Deriving the keyword set from element or tier instead of behavior.** The signature tracks what the
  effect *does*. A flat "damage → `REQ_NoDurationScaling`, concentration → `REQ_SpellConcentration`"
  rule is wrong both ways: a DoT **taper** rider carries both keywords, while a magicka-burn rider and
  a hazard tick carry none.
- **Writing a `REQ_*` behavioral keyword with the `:Skyrim.esm` master.** They are `:Requiem.esp`
  (`Nox_KW_CloakDamage` is `:Requiem - Magic Redone.esp`). They sit beside `Skyrim.esm` element
  keywords in every effect list, so the wrong master is the easy guess — and the write fails.
- **Leaving a resistance spell pointing at a NULLed vanilla MGEF.** All 8 vanilla resist MGEFs are
  `REQ_NULL_*` — the spell casts and protects against nothing. Re-point per
  `references/resistance-map.md`, same lane, same element.
- **Repricing enchanted gear by enchant tier.** Requiem's pre-enchanted items keep the base item's
  gold value; the tier lives in magnitude/cost/pool.
- **Wrong or missing `HalfCostPerk`.** This mis-classifies the spell's school/tier and breaks learning
  and cost. It is the single most important field.
- **Hand-stamping an `RFTI_All_*` rescaling perk.** It is a Reqtificator output, not source data.
- **Trying to script a plain spell.** Damage/heal/buff scaling is the engine `PowerAffectsMagnitude`
  flag — no Papyrus. Only special mechanics route to the `requiem-script-patching` skill.
- **Bumping a spell tome's value freely.** Tome value tracks the spell's tier (100→2000), not the
  mod's original price.
- **Deleting a creature's NULLed resistance.** On actors, replace with the Requiem trait, don't strip.
- **Re-pointing because `Requiem for the Indifferent.esp` wins.** On a live profile the
  Reqtificator's output winning is the healthy state and its values are the authority — never
  `set_mo2_instance` to escape it; re-pointing away from the instance you're patching breaks
  every subsequent read.

## Checklist

Before finishing a magic override, confirm:

- [ ] **Whole-plugin job:** every enumerated SPEL/MGEF/ENCH/SCRL/tome-BOOK dispositioned (patched = its
      per-record field Checklist passed; or skipped with a named reason); counts reconcile per type
      (patched + skipped = enumerated); **MGEF cross-check** done (every effect a patched spell casts is
      dispositioned); no rank-chain / element-variant / enchant-tier extrapolation.
- [ ] **School** set on the MGEF `MagicSkill` (the school ActorValue); `ResistValue` matches the element.
- [ ] **CastType + TargetType** match the delivery; SPEL and MGEF agree.
- [ ] **`ManualCostCalc` ticked on the modded spell** (as a flag union) and **BaseCost** taken from
      the comparable; **ChargeTime** on the tier ladder (conc 0 / FaF 0.25×tier / touch half /
      ritual 3.0) — rebalanced, not inherited.
- [ ] **Magnitudes DERIVED, not inherited** — every `Effects[i].Data.Magnitude/Area/Duration` read off
      the comparable at `depth=4` and set from it. **A spell still carrying the mod's original
      magnitudes is UNPATCHED**, however correct its cost and `HalfCostPerk` are.
- [ ] **Effect list** mirrors the comparable's **full shape — the comparable's riders ADDED**
      (element rider, Impact Stagger, Respite/Ward_Shield, perk-gated `_Improved`/`_Potent`,
      Break1/Break2), each with `Data` magnitude/area/duration and any `Conditions` copied per
      `references/mgef-conditions.md`. Rider magnitudes **copied, never guessed**
      (`Impact_Stagger` 0.25, `Slow` 5).
- [ ] **Riders are the MECHANICAL ones, tier-resolved as the comparable does it.** No cosmetic
      stand-in (Illusion `_Desc_Improved`, a `Fame`-ActorValue magnitude-0 entry) counted as the
      mechanical rider; rider records taken from the comparable rather than tier-guessed
      (`references/cost-and-magnitude.md`).
- [ ] **Subtype keyword(s)** carried on the **MGEF** exactly as the comparable does — and none
      invented where the comparable has none (SPEL records take no keywords).
- [ ] **`MinimumSkillLevel`** on each patched MGEF = the tier marker (0/25/50/75/100).
- [ ] **No effect resolves to `REQ_NULL_*`/`REQ_DEPRECATED_*`** — every dead BaseEffect (resistance
      magic is the classic) re-pointed same-lane, same-element per `references/resistance-map.md`.
- [ ] **Hand pairs** (`_LeftHand`/`_RightHand`) dispositioned together.
- [ ] **MGEF keyword+flag signature diffed against the ARCHETYPE comparable** at `depth=2` — not assumed
      from element or tier: element keyword present, **every REQ behavioral keyword the comparable
      carries added** with correct masters (`2FFEAD`/`412EDF`/`3FCA4C` = `:Requiem.esp`; `007609` =
      `:Requiem - Magic Redone.esp`), **`Flags`** mirrored, and any "already correct" skip names its
      comparable and shows the sets match.
- [ ] **Flags written as unions** — every SPEL/MGEF `Flags` write carries the winner's original bits
      plus your change; `ManualCostCalc` never silently dropped.
- [ ] **`HalfCostPerk`** = the correct `REQ_<School>_Mastery_<tier>` perk; specialization keyword if needed.
- [ ] **No `RFTI_All_*` rescaling perk** hand-added; **special-mechanic `Nox_*` script routed to the `requiem-script-patching` skill.**
- [ ] **Enchantment:** weapon = `Cost = Amount = Magnitude` 1:1 by tier (pool on the WEAP); apparel
      = constant, no pool, cost = tier×100; **pre-enchanted item `Value` = the base item's value**
      (the enchant adds no gold).
- [ ] **Spell tome (per patched spell):** every SPEL you patched has its teaching BOOK reverse-resolved
      and `Value` priced to the tier ladder (Weight 1); an un-priced tome leaves the spell half-patched.
      **Placement → `requiem-leveled-list-patching` skill.**
- [ ] **Masters** include `Requiem.esp` / MR (reference a real Requiem form); **no `REQ_NULL_*` remains.**

## Notes

- **Authority** = houseCARL's live conflict winner. Magic winners come from `Requiem - Magic Redone.esp`
  (MR; spells, effects, enchantments, staves, perks, scrolls), base `Requiem.esp` (shouts + base), and
  `Requiem - Alchemy Redone.esp` (potions/poisons), plus MR cross-patches and the **MR-patch addons**
  (Constellation / Obscure / Dark Hierophant / Holy Templar / Sonic Mage / Wildwaker / Apocalypse
  compat) — the best worked examples of new magic patched for Requiem. Some spell perks resolve to
  `Requiem - MR Special Feats Patch.esp` / `…WAR Resist and Regen Tweak Patch.esp`. On a live profile
  `Requiem for the Indifferent.esp` folds the build pass over them and is the winner to derive from.
  **A third-party plugin often wins an MR record** (an MGEF-merge or visual-tweak patch); derive from
  the winner, and note it when its values are the ones you copied.
- **Scrolls** (`REQ_Scroll_<School><Tier>_<Type>_<Delivery>`, Weight 0.5, tier-scaled value, charge =
  the spell's): a single-use cast at its tier; design the same MGEF, route value/placement to
  `requiem-leveled-list-patching`. **Shouts** (SHOU, `Requiem.esp`) are niche: three Words → three
  SPEL (effects designed here) + a recovery time; word-unlock and placement are out of scope.
- **Alchemy** (potion/poison MGEF, `Requiem - Alchemy Redone.esp`): `PeakValueModifier`,
  `FireAndForget`/`Self`, `Flags = PowerAffectsMagnitude, Recover`, `MagicAlch*` keywords
  (`MagicAlchHarmful` = poison); potency scales with Alchemy skill and `RFTI_All_PoisonRescaling`
  (Layer 2). This skill owns designing a genuinely **new** alchemy/food MGEF; the ALCH/INGR **records**
  carrying effects belong to `requiem-consumable-patching`, which routes new-effect design back here.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is later
  run through the Reqtificator.
