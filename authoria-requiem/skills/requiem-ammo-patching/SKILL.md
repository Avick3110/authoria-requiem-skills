---
name: requiem-ammo-patching
description: Patch new arrows or crossbow bolts for Requiem via houseCARL — derive damage, gold value, the material/weight/armor-piercing keywords, the projectile link, and the forge recipe from Requiem's own comparable ammo (same type + material tier) and emit a direct ESP override the Reqtificator then leaves alone. Use when the user wants to patch arrows or bolts for Requiem, balance modded ammo whose damage is too high or too low, set Requiem ammo keywords, add craftable arrows, fix weightless or overpriced ammo, or tier a new quiver to Requiem's materials. Load this before touching an AMMO record, not after the arrows fire wrong in game.
---

# Requiem Ammo Patching

## Overview

This skill patches an arrow or crossbow bolt so it is consistent with Requiem — the right
damage for its material tier, the keywords Requiem expects (material, ammo-weight class,
armor-piercing tier), a sane gold value, a projectile that flies like Requiem's, and a forge
recipe gated behind the correct smithing perk. The output is a **direct ESP override** authored
with houseCARL `create_record` / `set_field` / `bulk_apply`, because Requiem's balance lives in
real records — not SkyPatcher/SPID/KID.

The method is **live-analogy, never hardcoded numbers.** Requiem ships an arrow and a bolt for
nearly every material tier. Find the closest comparable Requiem itself ships, read its winner via
houseCARL, and derive the new ammo's damage, value, keywords, projectile, and recipe from it. The
exhaustive ladder in `references/ammo-ladder.md` is a cross-check and a starting point, but a live
read of the comparable is always the authority. For fully worked end-to-end cases (a tiered bolt,
elemental ammo, and a modded war arrow), see `references/worked-examples.md`.

The defining difference from weapons and armor: **ammo carries all its own keywords.** For
weapons the Reqtificator assigns the damage-type keyword, and for armor it assigns resistance and
tempering — but there is **no ammo keyword-assignment rule in the Reqtificator config at all** (it
only grants a global arrow-recovery perk). So the armor-piercing tier and the ammo-weight class
are **source-carried** — you stamp them yourself, and Requiem's ammo even carries
`RFTI_Exclusions_NoDamageRescale` to keep its hand-tuned damage. Nothing here is "let the patcher
fill it in." See `references/keywords.md` § The split.

Covers all `AMMO` records — **arrows and crossbow bolts**, normal tiered and special/elemental.
Out of scope: the **bow/crossbow frames** that fire them (→ `requiem-weapon-patching`) and the
*design* of a magic arrow's effect (the explosion/MGEF → the `requiem-magic-patching` skill). This skill sets the
AMMO frame, sets or patches its projectile, and routes effect authoring onward.

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

2. **Classify the ammo — two coordinates decide the comparable:**
   - **Type:** arrow or crossbow bolt. Read `Flags` — arrows carry `NonBolt`, bolts read `0`.
     Bolts hit 20% harder than the same-material arrow (see the ladder).
   - **Material / tier:** iron, steel, silver, elven, dwarven, orcish, glass, ebony, stalhrim,
     dragonbone, daedric, … The material drives damage, value, the armor-piercing tier, and the
     ammo-weight class.

   Then branch:
   - **Normal tiered ammo** → the main `## Workflow` below.
   - **Special / elemental / quest ammo** (an exploding bolt, a soul-stealer arrow, a bound
     arrow) → workflow for the frame, then `## Judgment` for what it may deviate on and what to
     route to the `requiem-magic-patching` skill.

## Workflow

### 1 — Find Requiem's comparable

Requiem names ammo `REQ_Arrow_<Material>` and `REQ_Bolt_<Material>`, so a substring scan finds
them fast:

```
housecarl_cross_plugin_query type="AMMO" editorid_contains="REQ_Arrow_" plugins=["Requiem.esp","Requiem - Weapons and Armor Redone.esp"] limit=200
```

Pick the canonical record — **not** a `REQ_NULL_*` stub (a NULLed/disabled duplicate) or a
`REQ_Creature_*` / `REQ_NP_*` (non-player creature ammo). If your ammo's material has no Requiem
equivalent, pick the tier it should sit at and derive from that tier's comparable. If you have an
arrow but need a bolt (or vice-versa), use the same-material arrow as the spine and apply the
arrow→bolt rule in step 3.

### 2 — Read the comparable's winner

```
housecarl_batch_record_detail formids=["01397F:Skyrim.esm"] conflict_tree=true \
  fields=["Name","Damage","Value","Weight","Keywords","Projectile","Flags"]
```

The hand-authored owner is `Requiem - Weapons and Armor Redone.esp` (WAR) for essentially all
ammo — it owns the whole ammo set over base `Requiem.esp`. Read the **winner**: on a live profile
that may be `Requiem for the Indifferent.esp`, still the authority — if the Reqtificator touched
the comparable, the read folds it in automatically. That is exactly the value a patch must be
consistent with; you never do manual exclusion math.

### 3 — Derive stats

Copy the comparable's shape and step along the ladders (full tables in
`references/ammo-ladder.md`):

- **Damage** — take the same-type same-material comparable. The arrow ladder is a clean +10 per
  tier: Iron 40 → Steel/Silver 50 → Elven 60 → Dwarven/Quicksilver 70 → Orcish/Skyforge 80 →
  Glass 90 → Ebony/Stalhrim 100 → Dragonbone 110 → Daedric 120. **A bolt is the same-material
  arrow × 1.2** (Steel arrow 50 → Steel bolt 60; Daedric arrow 120 → Daedric bolt 144). Exact
  across every material.
- **Value** — material-tier driven, small at the low end and jumping hard at glass: arrows Iron 1
  → Steel 1 → Elven/Silver/Quicksilver 2 → Dwarven/Orcish 3 → Skyforge 4 → Glass 14 → Ebony/
  Stalhrim 22 → Dragonbone 44 → Daedric 55. Bolt value ≈ arrow × 1.2. Prefer the comparable's
  exact value.
- **Weight = 0.** Every Requiem arrow and bolt weighs 0 — heft is carried by the *keyword*
  (`REQ_AmmoWeight_*`), not the Weight stat, so a full quiver never burdens the inventory. Do not
  give ammo a non-zero weight.

### 4 — Set keywords (all source-carried — you stamp them)

Keywords are the whole game here, and unlike weapons/armor **none of them are assigned for you.**
Read them off the comparable and replicate (FormIDs and the split in `references/keywords.md`). A
standard arrow or bolt carries:

- **`WeapMaterial<X>`** (vanilla, e.g. `WeapMaterialSteel 01E719`) — the material keyword; drives
  value and the silver/daedric interactions. Silver ammo also carries `WeapMaterialSteel` +
  `WeapMaterialSilver 10AA1A` + `REQ_WeaponType_SilverArrow 0FAB11`.
- **`VendorItemArrow 0917E7`** (vanilla) — on **all** ammo, arrows *and* bolts.
- **`REQ_AmmoWeight_<class>`** — Light / Medium / Heavy / Massive, by material density (elven
  Light; iron/steel/silver/glass Medium; dwarven/orcish/stalhrim Heavy; ebony/dragonbone/daedric
  Massive). This is the **bow-tier pairing** — heavier ammo wants a heavier-draw bow (the
  `REQ_WeaponType_Bow{Light,Heavy}` classes from `requiem-weapon-patching`).
- **`REQ_ArmorPiercingArrow_Tier<N>`** (arrows) or **`REQ_ArmorPiercingBolt_Tier<N>`** (bolts) —
  Requiem's core ammo mechanic. The tier tracks the material's penetration rank (Steel 1 → Elven
  2 → Dwarven/Orcish/Skyforge 3 → Glass 4 → Ebony/Stalhrim 5 → Dragonbone 6 → Daedric 7). **Iron
  carries no AP keyword** (tier 0 baseline). The tier is **not** derivable from damage alone (70-
  and 80-damage materials both sit at tier 3) — read it off the same-material comparable. Use the
  **Arrow** series on arrows and the **Bolt** series on bolts; never cross them.
- **`RFTI_Exclusions_NoDamageRescale AD3B2D`** — on all Requiem ammo. It opts the ammo out of the
  Reqtificator's damage rescale so the hand-set damage survives. Carry it.

### 5 — Projectile

Every AMMO points at a `Projectile` (PROJ) that carries its flight physics, in-air mesh, and (for
elemental ammo) the impact effect. Requiem **standardizes the physics** and overrides even the
vanilla arrow projectiles:

- **Arrows:** `Speed 3600`, `Gravity 0.35`, `Flags = CanBePickedUp` (Requiem **strips the vanilla
  `Supersonic` flag** from arrows). Type `Arrow`.
- **Bolts:** `Speed 5600`, `Gravity 0.35`, `Flags = CanBePickedUp, Supersonic` (bolts fly faster
  and flatter). Type is still `Arrow`.

A new arrow can **reuse a tier-matched Requiem arrow projectile** (e.g. `REQ_Projectile_Arrow_Iron
03BE11`) if it doesn't need a unique mesh/trail. If the mod ships its **own** projectile, patch it
to the profile above — a modded arrow PROJ usually keeps vanilla's `Supersonic` and a wrong speed,
which reads wrong against Requiem's ammo. See `references/crafting.md` § Projectiles.

### 6 — Crafting recipe

Each craftable ammo has one forge `ConstructibleObject`. Find the comparable's via reverse-lookup:

```
housecarl_cross_plugin_query type="ConstructibleObject" references="01397F:Skyrim.esm"
```

The pattern is uniform: **`WorkbenchKeyword = CraftingSmithingForge 088105`, one metal ingot + one
firewood (`06F993`), `CreatedObjectCount = 30`**, gated by a `HasPerk` condition. Read the
comparable's condition (houseCARL 1.2.2+ renders the perk parameter as a readable FormID) and
compose the same gate — the grammar is in `references/housecarl-recipes.md` § D. Recipe shapes in
`references/crafting.md`.

### 7 — Emit the override

Author with houseCARL into a patch plugin (originals are never touched). Copy-ready
`create_record` / `set_field` / `bulk_apply` call shapes are in `references/housecarl-recipes.md`.
Because ammo patches reference Requiem-defined keywords (ammo-weight, armor-piercing tier, RFTI
exclusion), `Requiem.esp` is pulled in as a master automatically.

## Judgment

The ladder is the default, not a straitjacket. The calls that need judgment are **special/
elemental ammo**, the **bow-tier pairing**, and a **weight/value sanity check**.

### Special, elemental, and quest ammo — identify before you deviate

Trace how the ammo is obtained, exactly as for weapons and armor — reverse-reference for a forge
recipe and for leveled lists:

```
housecarl_cross_plugin_query type="ConstructibleObject" references="<ammo FormID>"
housecarl_cross_plugin_query type="LeveledItem"          references="<ammo FormID>"
```

Craftable or in loot ⇒ treat as normal tiered ammo. Non-craftable + fixed reward + a special
effect ⇒ bespoke; keep the tiered *frame* but preserve the author's effect and value. The cases
Requiem itself ships:

- **Elemental ammo** (`REQ_Ench_Arrow_<Mat>_Fire/Ice/Shock`) keeps the **base material's damage**
  (a Steel Arrow of Fire is still 50 damage) and bumps value modestly (steel 1 → 11). The
  elemental hit lives on the **projectile** (a custom PROJ carrying an `Explosion`), not on the
  AMMO `Damage`. To make one: clone the base arrow, keep its damage, point `Projectile` at the
  elemental PROJ, nudge value up. **Designing** that explosion/MGEF is the `requiem-magic-patching`
  skill — this skill links it.
- **Quest / unique ammo** (Bloodcursed, Sunhallowed): base-material damage, a **bespoke high
  value** (100), a special projectile, and **no forge recipe**. Keep the structure; preserve the
  author's value and effect.
- **Bound ammo** (conjured by a spell): `Value 0`, summoned — route the conjuration to the
  `requiem-magic-patching` skill.
- **Creature / siege ammo** (`REQ_Creature_Bolt_DwarvenSphere`, ballista/catapult ammo) carries
  the **`NonPlayable`** flag so the player can't loot or fire it. If you are patching enemy/trap
  ammo, keep `NonPlayable`; don't give it a player forge recipe.

### Bow-tier pairing and weight sanity

- The `REQ_AmmoWeight_*` class is the lever that ties ammo to bows. Match it to the material's
  density (a light wood-and-feather elven arrow is Light; a massive daedric arrow is Massive), not
  to its damage. When a mod's "heavy" arrow is meant for a war bow, Heavy/Massive is right.
- **Weight stays 0 and value stays small.** A modded arrow shipped with Weight 0.1 and Value 25 is
  off Requiem's model on both axes — zero the weight and bring value onto the material tier. Value
  tracks the **material**, not the damage.

When you cannot find a clean comparable, say so explicitly — name what you checked and what you
assumed — rather than emitting a confident guess.

## Common mistakes

- **Waiting for the Reqtificator to assign the armor-piercing or weight keyword.** It never does —
  there is no ammo assignment rule. If you don't stamp `REQ_ArmorPiercing*_Tier<N>` and
  `REQ_AmmoWeight_*`, the ammo has no penetration tier and no draw class. Stamp them from the
  comparable.
- **Crossing the Arrow and Bolt armor-piercing series.** Arrows take `REQ_ArmorPiercingArrow_*`,
  bolts take `REQ_ArmorPiercingBolt_*`. The tier number matches per material; the keyword does not.
- **Giving ammo a non-zero weight.** Requiem ammo weighs 0; heft is the `REQ_AmmoWeight_*` keyword.
- **Copying vanilla damage.** Vanilla arrows are ~8–24 damage; Requiem ammo is 40–144. Always
  derive from a Requiem comparable.
- **Leaving a modded projectile supersonic / at the wrong speed.** Patch the PROJ to arrow
  3600 / no-Supersonic or bolt 5600 / Supersonic, or point at a Requiem arrow/bolt projectile.
- **Re-pointing because `Requiem for the Indifferent.esp` wins.** On a live profile the
  Reqtificator's output winning is the healthy state and its values are the authority — never
  `set_mo2_instance` to escape it; re-pointing away from the instance you're patching breaks
  every subsequent read.
- **Forgetting `RFTI_Exclusions_NoDamageRescale`** — without it the Reqtificator may rescale your
  hand-set damage.

## Checklist

Before finishing an ammo override, confirm:

- [ ] **Type** correct: arrow `Flags = NonBolt`, bolt `Flags = 0`; creature/trap ammo keeps
      `NonPlayable`.
- [ ] **Damage** on the ladder for the material (bolt = arrow × 1.2).
- [ ] **Value** on the material tier; **Weight = 0**.
- [ ] **Material keyword** (`WeapMaterial<X>`) + **`VendorItemArrow`** present.
- [ ] **`REQ_AmmoWeight_<class>`** present (the bow-tier pairing).
- [ ] **Armor-piercing tier** present and from the right series (`Arrow` vs `Bolt`); iron carries
      none.
- [ ] **`RFTI_Exclusions_NoDamageRescale`** present.
- [ ] **Projectile** set and on Requiem's profile (arrow 3600 / bolt 5600, gravity 0.35).
- [ ] **Forge recipe** (ingot + firewood → 30) with the cloned perk gate — unless it's
      bespoke/creature ammo that shouldn't be craftable.
- [ ] **Elemental/quest ammo**: effect carried on the projectile, value preserved, effect *design*
      routed to the `requiem-magic-patching` skill.
- [ ] **`Requiem.esp` is a master** of the patch (auto when a Requiem keyword is referenced) —
      masters list correct + load-order-sorted (verify the `masters:` read-back).
- [ ] **No `REQ_NULL_*` reference remains** in any patched field — strip it or replace it with the
      live Requiem form (houseCARL resolves the EditorID live against the load order).

## Notes

- **Authority** = houseCARL's live conflict winner. Hand-authored ammo overrides come almost
  entirely from `Requiem - Weapons and Armor Redone.esp` (WAR) over base `Requiem.esp`; bound ammo
  from `Requiem - MR Weapons and Armor Redone Patch.esp`; on a live profile `Requiem for the
  Indifferent.esp` folds the build pass over them and is the winner to derive from.
- **Bows/crossbows** (the frames) → `requiem-weapon-patching`. **Magic-arrow effect design** (the
  explosion/MGEF/conjuration) → the `requiem-magic-patching` skill. This skill sets the AMMO frame and its projectile.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is
  later run through the Reqtificator, which leaves the hand-tuned ammo alone (the RFTI exclusion).
