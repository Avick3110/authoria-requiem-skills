---
name: requiem-weapon-patching
description: Patch a new or modded weapon for Requiem via houseCARL — derive damage, keywords, tempering/crafting recipe, gold value, and enchantment charge from Requiem's own comparable weapon (same type + material tier) and emit a direct ESP override the Reqtificator can then rebalance. Use when the user wants to patch a weapon for Requiem, balance a modded sword, bow, mace, dagger, or staff, set Requiem weapon keywords, fix weapon damage that is too high or too low for Requiem, add a craftable or enchanted weapon, or make a new weapon fit Requiem's material tiers. Load this before touching a weapon record, not after the Reqtificator rebalances it wrong.
---

# Requiem Weapon Patching

## Overview

This skill patches a weapon so it is consistent with Requiem — the right damage for its
material tier, the keywords Requiem and its Reqtificator expect, a tempering and crafting
recipe gated behind the correct smithing perk, a sane gold value, and (for enchanted weapons
and staves) the right enchantment charge. The output is a **direct ESP override** authored
with houseCARL `create_record` / `set_field` / `bulk_apply`, because the Reqtificator's
auto-balance pass runs over real records — not SkyPatcher/SPID/KID.

The method is **live-analogy, never hardcoded numbers.** Requiem already defines a weapon for
nearly every type × material combination. Find the closest comparable that Requiem itself
ships, read its winner via houseCARL, and derive the new weapon's stats, keywords, recipe, and
value from it. The mined ladders in `references/` are a cross-check and a starting point, but a
live read of the comparable is always the authority — the load order can shift under you.

Covers melee weapons (sword, greatsword, war axe, battleaxe, dagger, mace, warhammer, plus
Requiem-added shortsword, club, quarterstaff, battlestaff, katana, dai-katana, tanto,
wakizashi), bow/crossbow frames, and **staves** (their balance is enchantment-driven — see
`references/enchanted-and-staves.md`). Ammo (arrows/bolts) is out of scope — route it to the
`requiem-ammo-patching` skill.

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

2. **Classify the weapon.** Three branches diverge from here:
   - **Plain melee or bow/crossbow** → the main `## Workflow` below.
   - **Enchanted weapon** (it has an `ObjectEffect`) → workflow, then the enchantment section of
     `references/enchanted-and-staves.md` (Template links the plain base; `EnchantmentAmount` is
     the charge pool). A **modded** `ObjectEffect` is a new enchantment that itself needs Requiem
     balancing — route its ENCH/MGEF design to the `requiem-magic-patching` skill. This skill sets
     only the weapon-side frame (the `Template` link, the `ObjectEffect` link, `EnchantmentAmount`);
     setting those does **not** make the enchantment balanced, the same way that skill designs the
     effect and this one only links it.
   - **Staff** (`Data.AnimationType = Staff`, `Damage = 1`) → keywords and value come from the
     staff's spell, not a material ladder. Go to the staff section of
     `references/enchanted-and-staves.md`.
   - **Unique artifact** (bespoke enchantment, hand-set value) → workflow for the skeleton, then
     `## Judgment` for what it is allowed to deviate on.

## Bulk pass protocol (whole-plugin jobs)

When you're patching a whole plugin — routed here from the `requiem-patching` skill, or any job with
more than a handful of weapons — the enumeration **is the work queue**, not a sample of it. A weapon
plugin hides its hard records inside uniform-looking groups: the one dagger in a matched set carrying
a hand-set value, the "steel" sword the author quietly gave ebony damage, the enchanted variant whose
`ObjectEffect` is a brand-new enchantment nobody balanced. Those outliers are exactly what a rebalance
pass exists to catch, and the fastest way to ship them wrong is to read one member of a family and
extrapolate to the rest.

Open with one enumeration query — the whole plugin at once, no writes — and use it as your triage
table:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="WEAP" \
  fields=["Name","Data.AnimationType","Data.Flags","Keywords","BasicStats.Damage","ObjectEffect","Template"]
```

Read each row's `Data.AnimationType` (which workflow branch), `ObjectEffect` (enchanted vs plain),
`Template` (variant vs base), and `Data.Flags` (playable loot vs not) to disposition every record.

**A `NonPlayable` weapon in an actor's hand is combat gear, not a cosmetic skip.** The player never
loots it, but the player *feels* it — an NPC-hand copy of a sword deals its stamped damage on every
hit. Derive its damage, crit, and keywords at the same tier as its playable twin (or the wielder's
tier when no twin exists), exactly like a playable weapon; a NonPlayable **bow** still needs
`Data.Flags = NPCsUseAmmo` or its wielder won't fire it. What NonPlayable copies legitimately skip
is the player-economy surface only: no forge/temper recipes, value as authored. The true skips are
below — records with no combat surface at all.

**Every FormID gets a disposition.** Walk the full enumeration; each record is either **patched** —
routed to a named branch: plain melee, bow/crossbow, enchanted, staff, or artifact — or **skipped**
with a reason verified on *that* record, never inherited from a neighbour. The skip categories to name:

- **Trap / prop / pseudo-weapons** — a trap trigger, a static prop, or a display piece that happens to
  be a WEAP; there's no tier to place it on.
- **Unarmed placeholders** — a zero-stat unarmed marker record, not a real weapon.
- **Template-only base records** — a hollow record that exists only to be a `Template` target for
  variants and carries no stats of its own; skip it and patch the variants that point at it.

**Patched means field-complete, not merely touched.** A record counts as patched only when the
`## Checklist` below passes for that record — the per-record field checklist is the gate. A weapon you
set the damage on but never gave its material keyword, its recipe, or (for an enchanted one) a
balanced enchantment is not patched, it's in progress. Run the checklist per record before you mark it
done. **Flags fields are unions, not scalars:** a `Data.Flags` write replaces the whole bitfield, so
read the winner's flags first and write original-bits + your change — setting only the bit you thought
about silently strips `NPCsUseAmmo`, `NonPlayable`, and their kin.

Close the pass with a **reconciliation count — patched + skipped = enumerated.** If the two sides
don't add up, a record fell through; find it before you call the type done. (This is the per-record
coverage the `requiem-patching` skill's integration checklist gates on for high-count types.)

**Don't extrapolate across a uniform-looking family.** Four weapon-family shapes *look* uniform and
tempt one read applied to the whole group:

- **same-material tiers** — a run of steel weapons of different types;
- **same-type families** — every sword in the mod across materials;
- **same-prefix EditorID lines** — `MyMod_Sword_01..09`;
- **enchanted variants of one base** — a plain sword and its Fire/Frost/Shock editions.

This skill's own ladder formulas (+1 damage per material tier, `floor(Damage/2)` crit, ≈ 500 × tier
enchantment charge) produce a **candidate** value for a sibling — a starting point to check the read
against, never a value to stamp unread. Each sibling WEAP is still read and dispositioned on its own
record, because the hand-tweaked outlier inside a uniform-looking family — the "steel" sword given
ebony damage, the one variant carrying a bespoke enchantment — is precisely what the pass exists to
catch.

## Workflow

### 1 — Find Requiem's comparable

Query Requiem's own weapon of the **same type and nearest material tier**. Requiem names them
`REQ_Weapon_<Material>_<Type>`, so a substring scan finds them fast:

```
housecarl_cross_plugin_query type="WEAP" editorid_contains="REQ_Weapon_" plugins=["Requiem.esp"] limit=500
```

Narrow with the material or type (`editorid_contains="Steel_Sword"`). Pick the canonical
record — **not** a `REQ_Var_*` or leveled-chain entry: Requiem NULLs leveled duplicates and
keeps only the strongest variant, so a `_Var_` record is a dead stub with junk stats. If your
weapon's material has no Requiem equivalent (a novel alloy), pick the tier it should sit at and
derive from that tier's comparable.

### 2 — Read the comparable's winner

```
housecarl_batch_record_detail formids=["013989:Skyrim.esm"] conflict_tree=true \
  fields=["Name","BasicStats","Data","Critical","Keywords","ImpactDataSet","EquipSound","Template","ObjectEffect","EnchantmentAmount"]
```

Read the **winner**, which already folds in WAR (`Requiem - Weapons and Armor Redone.esp`), MR
(`Requiem - Magic Redone.esp` for staves/bound/artifacts), the ISC sound patch, and the USSEP
fixes Requiem inherits. On a live profile the winner may be `Requiem for the Indifferent.esp` —
still the authority: if the Reqtificator rescaled the comparable, the read folds the rescale in
automatically. That is exactly the value a patch must be consistent with — you never do manual
exclusion math. (Reqtificator-*assigned* keywords/perks on the winner remain build outputs you
never hand-copy — see Common mistakes.)

### 3 — Derive stats

Copy the comparable's shape and adjust along the ladders (full tables in
`references/damage-ladder.md`):

- **Damage** — take the comparable's damage. If your weapon is a different tier, step along the
  per-type ladder (e.g. 1H sword: Iron 7 → Steel 8 → Elven 9 → Dwarven 10 → Orcish 11 → Glass
  12 → Ebony 13 → Dragonbone 14 → Daedric 15).
- **Critical damage** — `floor(Damage / 2)`, with `Critical.PercentMult = 1`. Holds across every
  Requiem weapon.
- **Speed, Reach, AnimationType** — type-defined, material-independent. Copy from the same-type
  comparable (sword 1.0/1.0, dagger 1.3/0.7, greatsword 0.75/1.3, mace 0.8/1.0, warhammer
  0.6/1.3, etc.).
- **Weight** — tracks material; copy/interpolate from the comparable.
- **Value** — material-tier driven (Iron 25 → Steel 45 → Dwarven 180 → Glass 1125 → Ebony 1800
  → Daedric 4500 for a sword), scaled by weapon size-class. Take the same-type same-material
  value when one exists; otherwise scale the type from a different-material comparable by the
  material value ladder. See `references/damage-ladder.md`.

### 4 — Set keywords

Keywords are what the Reqtificator's rules match on, so they are load-bearing. Read them off the
comparable and replicate (FormIDs and the full vocabulary in `references/keywords.md`):

- **Melee** carries exactly `[WeapMaterial<X>, WeapType<Y>, VendorItemWeapon]`.
- **Do not hand-stamp the damage-type keyword** (slash/pierce/blunt/ranged, `AD3954`–`AD3957`).
  It is **not** stored in source records — the Reqtificator assigns it at build time from the
  weapon-type keyword (`feature_DamageType` in `WeaponKeywordAssignments_Requiem.esp.conf`). Get
  the **weapon-type** keyword right and the damage type follows automatically.
- **Bows/crossbows** add `REQ_BowBreakable`, `REQ_WeaponType_Bow{Light,Heavy}`, and the two
  `RFTI_Exclusions_No{Damage,BowSpeed}Rescale` keywords that opt the bow out of the
  Reqtificator's rescale (WAR hand-tuned bow damage). Also set `Data.Flags = NPCsUseAmmo`.
- **Staves** carry `WeapTypeStaff`, `VendorItemStaff`, and an MR `Nox_KW_Staff_<School><Tier>`
  keyword chosen from the staff's spell — see `references/enchanted-and-staves.md`.

### 5 — Sounds and impact

Copy the comparable's `ImpactDataSet`, `EquipSound`, `BlockBashImpact`, and
`AlternateBlockMaterial` FormID links verbatim. These are links; the actual audio is overridden
at the sound-descriptor level by `Requiem - Immersive Sounds Compendium.esp`, so a weapon that
reuses the right links sounds correct without any further work. Details in
`references/sounds-and-misc.md`.

### 6 — Crafting + tempering recipes

Each Requiem weapon has up to three `ConstructibleObject` recipes. Find the comparable's via:

```
housecarl_cross_plugin_query type="COBJ" references="013989:Skyrim.esm" conflict_tree=true
```

- **Forge** (`WorkbenchKeyword` = `CraftingSmithingForge 088105`): a `HasPerk` condition (the
  smithing-perk gate) + material `Items`.
- **Temper** (`WorkbenchKeyword` = grindstone `088108`): a `HasPerk` condition + one ingot.
- **Smelter** (`WorkbenchKeyword` = `0A5CCE`): melts the weapon back to ingots (optional).

Author the new recipes by cloning the comparable's `Conditions` and `Items` and swapping the
`CreatedObject` to your weapon. **The comparable is the one matching the product weapon's own
material keyword** — a moonstone blade tempers on Refined Moonstone behind the elven smithing gate,
an ebony one on an Ebony Ingot behind Ebony Smithing. Never default the inputs or the perk gate to
the Steel/Craftsmanship shape because it's the first recipe you saw: the ingot and the `HasPerk`
gate both track the material, and a wrong gate makes the recipe available at the wrong smithing
tier. Read the comparable's condition (houseCARL 1.2.2+ renders the perk parameter as a readable
FormID) and compose the same gate onto the new recipe — the condition grammar is in
`references/housecarl-recipes.md` § E. Recipe shapes in `references/crafting.md`.

When an existing recipe must be disabled, keep it structurally valid and set
`WorkbenchKeyword = REQ_DisableRecipe AD3B01:Requiem.esp`. Never clear the workbench link or set it
to null: Requiem disables recipes through this explicit station keyword, and a null link is not the
same record shape. Verify the Bruma-style case (`CYRTemperWeaponAyleidBattleAxe`) reads back with
`REQ_DisableRecipe`, not `(null link)`.

### 7 — Emit the override

Author with houseCARL into a patch plugin (originals are never touched). Copy-ready
`create_record` / `set_field` / `bulk_apply` call shapes are in `references/housecarl-recipes.md`.

## Judgment

The ladder is the default, not a straitjacket. The two calls that need real judgment are
**whether a weapon is an artifact** and **what a unique is allowed to deviate on.**

### Is it an artifact? Identify before you deviate

There is no single record field that means "artifact," and Requiem doesn't detect them
algorithmically — it keeps a curated list (the `REQ_Artifact_` editorid prefix +
`…\Reqtificator\documentation\Artifacts.md`). For new modded content there is no flag in the data;
you infer authorial intent from a priority ladder, strongest signal first:

1. **Is it a known Requiem artifact (or a copy of one)?** Check the `REQ_Artifact_*` set /
   `Artifacts.md`, or whether its `ObjectEffect` matches a Requiem artifact's. If so, mirror that
   treatment and stop here.
2. **Trace how it is obtained — the decisive signal.** Reverse-reference the weapon's FormID:

   ```
   housecarl_cross_plugin_query type="ConstructibleObject" references="<weapon FormID>"
   housecarl_cross_plugin_query type="LeveledItem"          references="<weapon FormID>"
   ```

   A genuine artifact is **not craftable** (no COBJ recipe), **not in leveled lists**, and reaches
   the player as a **single fixed reward** (quest/boss/placed reference). If it has a forge recipe
   or sits in loot lists, it is **custom gear, not an artifact** — give it normal material-tier
   treatment.
3. **Record heuristics only nominate candidates — they never decide.** `MagicDisallowEnchanting`
   hints at a non-learnable signature effect but also sits on plenty of craftable non-artifacts; a
   "bespoke `ObjectEffect`" is near-useless (every modded enchanted weapon has its own); an
   off-tier value or a unique lore-name are weak nudges. Use them to decide *what to check*, not to
   conclude.
4. **If it is still ambiguous, say so and ask** — don't silently classify. Authorial intent (the
   mod's description, how a quest hands the weapon over) is often the real arbiter and isn't fully
   in the data. A wrong "it's an artifact" call inflates value and strips craftability; a wrong
   "it's not" call flattens a real unique — both are worse than asking.

### What a unique may deviate on

- **Confirmed artifacts** (Dawnbreaker, Mehrunes' Razor, …) keep a bespoke `ObjectEffect` and a
  hand-set value far off the ladder (Dawnbreaker is 80,000), may carry `DaedricArtifact` /
  `MagicDisallowEnchanting` and a `REQ_Tempering_<Perk>` keyword, and often repurpose the material
  keyword for a damage interaction (Dawnbreaker is `WeapMaterialSilver` for bonus undead damage).
  Keep the *structure* Requiem uses (read a real `REQ_Artifact_*` comparable); let value and
  enchantment be bespoke. Damage still follows a tier; crit is still `floor(Damage/2)`. See
  `references/worked-examples.md`.
- **Novel damage types or mechanics** with no Requiem precedent — pick the closest analog, state
  the assumption, and flag it rather than inventing a number silently.
- **Value tracks the material keyword, not raw damage.** Requiem's economy is tight, but don't
  change a sensible author value unprovoked — correct only a value that's clearly off the weapon's
  *material* tier (or when the user asks).

When you cannot find a clean comparable, say so explicitly — name what you checked and what you
assumed — rather than emitting a confident guess.

## Common mistakes

- **Hand-stamping the damage-type keyword.** It is a Reqtificator output, not source data;
  adding it yourself double-assigns or fights the build pass. Set the weapon-type keyword instead.
- **Copying vanilla or another mod's damage.** Vanilla numbers are not Requiem numbers — always
  derive from a Requiem comparable.
- **Deriving from a `REQ_Var_*` or leveled stub.** Those are NULLed dead records; use the
  canonical strongest variant.
- **Re-pointing because `Requiem for the Indifferent.esp` wins.** On a live profile the
  Reqtificator's output winning is the healthy state and its values are the authority — never
  `set_mo2_instance` to escape it; re-pointing away from the instance you're patching breaks
  every subsequent read.
- **Forgetting the bow rescale-exclusion keywords or `NPCsUseAmmo`** — without them the
  Reqtificator rescales the bow's hand-tuned damage and NPCs won't fire it.
- **Defaulting a recipe to the Steel ingot + Craftsmanship gate.** The temper/forge inputs and the
  `HasPerk` gate track the weapon's own material keyword — read the same-material comparable's
  recipe, don't reuse the first (steel) shape for the whole plugin.
- **Disabling a recipe with a null workbench.** Set `WorkbenchKeyword` to
  `REQ_DisableRecipe AD3B01:Requiem.esp`; never clear/null the station link.
- **Skipping a `NonPlayable` NPC-hand weapon as cosmetic.** The player takes its damage; tier it
  like its playable twin (it only skips recipes/value).
- **Giving an enchanted weapon a value bump.** In Requiem the enchanted variant keeps the base
  weapon's value; the enchantment adds a charge pool, not gold.
- **Treating an enchanted weapon as balanced once the frame is set.** Wiring
  `Template` / `ObjectEffect` / `EnchantmentAmount` balances the *weapon* side only; a modded
  `ObjectEffect` is a new enchantment whose ENCH/MGEF still needs Requiem balancing — route its design
  to the `requiem-magic-patching` skill. A charge pool on an unbalanced effect is still unbalanced.
- **Carrying a `REQ_NULL_*` reference forward.** Requiem retires records as inert `REQ_NULL_*`
  stubs; if a modded weapon's keyword / `ObjectEffect` / recipe points at one (houseCARL resolves
  the live EditorID), strip it or replace it with the real Requiem form — never keep or add one.

## Checklist

Before finishing a weapon override, confirm:

- [ ] **Whole-plugin job:** every enumerated WEAP dispositioned — patched (this field checklist
      passed for that record) or skipped with a reason; counts reconcile (patched + skipped =
      enumerated); no family extrapolation (material tier, type, EditorID prefix, or enchant variant);
      `NonPlayable` NPC-hand copies tiered like their playable twins, not skipped as cosmetic.
- [ ] **Weapon-type keyword** present (so the Reqtificator assigns the correct damage type) — and
      the damage-type keyword is **not** hand-added.
- [ ] **Material keyword** present (drives value tier, tempering, and damage interactions).
- [ ] **BasicStats**: Damage on-ladder, Value on-tier, Weight sane; **Critical** = `floor(Dmg/2)`,
      PercentMult 1.
- [ ] **Data**: Speed/Reach/AnimationType match the type; bows have `NPCsUseAmmo`.
- [ ] **Name** encodes the material/class ("Steel Greatsword", "Ebony Dagger").
- [ ] **Sound + ImpactDataSet** links copied from the comparable.
- [ ] **Crafting recipe** (forge) + **tempering recipe** with the ingot and perk gate matching the
      weapon's own material keyword (never a defaulted Steel/Craftsmanship gate); any intentionally
      disabled existing recipe uses `REQ_DisableRecipe AD3B01:Requiem.esp`, never a null workbench.
- [ ] **Flags written as unions** — any `Data.Flags` write carries the winner's original bits plus
      your change; no bit silently dropped.
- [ ] **Enchanted weapons**: `Template` → plain base variant; `ObjectEffect` set;
      `EnchantmentAmount` = charge pool (≈ 500 × tier).
  - [ ] A **modded** `ObjectEffect` is routed to the `requiem-magic-patching` skill for its ENCH/MGEF
        balancing — the weapon-side frame alone doesn't make the enchantment balanced.
- [ ] **Staves**: keywords derived from the staff's spell; `Damage = 1`.
- [ ] **Leveled-list placement** noted (which LVLI it should join) — the merge happens in the
      `requiem-leveled-list-patching` skill, but record the intent.
- [ ] **Masters correct + load-order-sorted** (verify the `masters:` read-back; `Requiem.esp`
      present when a Requiem form is referenced) and **no `REQ_NULL_*` reference remains** in any
      patched field (strip or replace with the live form).

## Notes

- **Authority** = houseCARL's live conflict winner. Hand-authored weapon overrides come from
  `Requiem - Weapons and Armor Redone.esp` (WAR), `Requiem - Magic Redone.esp` (MR; staves,
  bound weapons, magic artifacts), `Requiem - Stealth Redone.esp` (trap weapons),
  `Requiem - Immersive Sounds Compendium.esp` (sound-bearing battleaxe/warhammer overrides), and
  core `Requiem.esp`, plus their cross-patches; on a live profile `Requiem for the
  Indifferent.esp` folds the build pass over them and is the winner to derive from.
- **Ammo** (arrows/bolts) → `requiem-ammo-patching`. **Spell/effect design** (not the staff
  frame) → the `requiem-magic-patching` skill.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is
  later run through the Reqtificator, which applies the auto-balance pass over it.
