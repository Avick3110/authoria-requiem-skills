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

1. **Freshness probe.** Read Iron Sword and confirm `Requiem.esp` is in the override chain:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   The invariant is chain presence, not winner identity: `Requiem.esp` must appear in the chain
   (`Skyrim.esm → unofficial skyrim special edition patch.esp → Requiem.esp → …`). On a live
   instance the winner is normally `Requiem for the Indifferent.esp` — the Reqtificator's
   generated output, enabled on every playable Requiem setup — or another patch loading after
   Requiem; that is healthy, not stale. Derive reference values from the last hand-authored
   override in the chain, never from the generated output. Only if `Requiem.esp` appears nowhere
   in the chain is houseCARL reading the wrong load order — point it at your Requiem MO2 instance
   and re-probe:

   ```
   housecarl_set_mo2_instance path="<your MO2 instance>"
   ```

2. **Classify the weapon.** Three branches diverge from here:
   - **Plain melee or bow/crossbow** → the main `## Workflow` below.
   - **Enchanted weapon** (it has an `ObjectEffect`) → workflow, then the enchantment section of
     `references/enchanted-and-staves.md` (Template links the plain base; `EnchantmentAmount` is
     the charge pool).
   - **Staff** (`Data.AnimationType = Staff`, `Damage = 1`) → keywords and value come from the
     staff's spell, not a material ladder. Go to the staff section of
     `references/enchanted-and-staves.md`.
   - **Unique artifact** (bespoke enchantment, hand-set value) → workflow for the skeleton, then
     `## Judgment` for what it is allowed to deviate on.

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

Read the **last hand-authored override** in the chain — when `Requiem for the Indifferent.esp`
(the Reqtificator's generated output) is the winner, step one down. The hand-authored winner
already folds in WAR (`Requiem - Weapons and Armor Redone.esp`), MR (`Requiem - Magic
Redone.esp` for staves/bound/artifacts), the ISC sound patch, and the USSEP fixes Requiem
inherits. That is exactly the value a patch must be consistent with — no other exclusion math
is needed.

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
`CreatedObject` to your weapon. Copy the `Conditions` list **verbatim** — the perk reference is
stored as a form-index that does not always render as a readable FormID, so reproduce it from
the comparable rather than retyping it. Recipe shapes in `references/crafting.md`.

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
- **Deriving from `Requiem for the Indifferent.esp`.** The Reqtificator's generated output
  normally wins on a live instance; it is rebuilt every run and carries build-time assignments.
  Read the last hand-authored override beneath it in the chain.
- **Forgetting the bow rescale-exclusion keywords or `NPCsUseAmmo`** — without them the
  Reqtificator rescales the bow's hand-tuned damage and NPCs won't fire it.
- **Giving an enchanted weapon a value bump.** In Requiem the enchanted variant keeps the base
  weapon's value; the enchantment adds a charge pool, not gold.
- **Carrying a `REQ_NULL_*` reference forward.** Requiem retires records as inert `REQ_NULL_*`
  stubs; if a modded weapon's keyword / `ObjectEffect` / recipe points at one (houseCARL resolves
  the live EditorID), strip it or replace it with the real Requiem form — never keep or add one.

## Checklist

Before finishing a weapon override, confirm:

- [ ] **Weapon-type keyword** present (so the Reqtificator assigns the correct damage type) — and
      the damage-type keyword is **not** hand-added.
- [ ] **Material keyword** present (drives value tier, tempering, and damage interactions).
- [ ] **BasicStats**: Damage on-ladder, Value on-tier, Weight sane; **Critical** = `floor(Dmg/2)`,
      PercentMult 1.
- [ ] **Data**: Speed/Reach/AnimationType match the type; bows have `NPCsUseAmmo`.
- [ ] **Name** encodes the material/class ("Steel Greatsword", "Ebony Dagger").
- [ ] **Sound + ImpactDataSet** links copied from the comparable.
- [ ] **Crafting recipe** (forge) + **tempering recipe** with the correct perk gate and inputs.
- [ ] **Enchanted weapons**: `Template` → plain base variant; `ObjectEffect` set;
      `EnchantmentAmount` = charge pool (≈ 500 × tier).
- [ ] **Staves**: keywords derived from the staff's spell; `Damage = 1`.
- [ ] **Leveled-list placement** noted (which LVLI it should join) — the merge happens in the
      `requiem-leveled-list-patching` skill, but record the intent.
- [ ] **Masters correct + load-order-sorted** (verify the `masters:` read-back; `Requiem.esp`
      present when a Requiem form is referenced) and **no `REQ_NULL_*` reference remains** in any
      patched field (strip or replace with the live form).

## Notes

- **Authority** = houseCARL's live conflict winner among the hand-authored plugins. Weapon winners
  come from `Requiem - Weapons and Armor Redone.esp` (WAR), `Requiem - Magic Redone.esp` (MR;
  staves, bound weapons, magic artifacts), `Requiem - Stealth Redone.esp` (trap weapons),
  `Requiem - Immersive Sounds Compendium.esp` (sound-bearing battleaxe/warhammer overrides), and
  core `Requiem.esp`, plus their cross-patches. `Requiem for the Indifferent.esp` (the
  Reqtificator's generated output) is never authority — step past it in the chain when it wins.
- **Ammo** (arrows/bolts) → `requiem-ammo-patching`. **Spell/effect design** (not the staff
  frame) → the `requiem-magic-patching` skill.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is
  later run through the Reqtificator, which applies the auto-balance pass over it.
