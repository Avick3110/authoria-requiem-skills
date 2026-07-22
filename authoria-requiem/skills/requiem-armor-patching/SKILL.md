---
name: requiem-armor-patching
description: Patch a new or modded armor piece for Requiem via houseCARL — derive armor rating, set/material and part keywords, gold value, weight, and a tempering/crafting recipe from Requiem's own comparable armor (same part + material + light/heavy class) and emit a direct ESP override the Reqtificator can then rebalance. Use when the user wants to patch armor for Requiem, balance a modded cuirass, shield, helmet, gauntlets, or boots, set Requiem armor keywords, fix an armor rating that is too high or too low for Requiem, add craftable light or heavy armor, or make a new armor set fit Requiem's material tiers. Load this before touching an armor record, not after the Reqtificator caps it wrong.
---

# Requiem Armor Patching

## Overview

This skill patches an armor piece so it is consistent with Requiem — the right armor rating for
its part and material tier, the keywords Requiem and its Reqtificator expect, a tempering and
crafting recipe gated behind the correct smithing perk, a sane gold value and weight, and (for
heavy gauntlets) the fist-damage perk keyword. The output is a **direct ESP override** authored
with houseCARL `create_record` / `set_field` / `bulk_apply`, because the Reqtificator's
auto-balance pass runs over real records — not SkyPatcher/SPID/KID.

The method is **live-analogy, never hardcoded numbers.** Requiem already defines an armor piece
for nearly every part × material × weight class. Find the closest comparable that Requiem itself
ships, read its winner via houseCARL, and derive the new piece's stats, keywords, recipe, and
value from it. The mined ladders in `references/` are a cross-check and a starting point, but a
live read of the comparable is always the authority — the load order can shift under you.

Covers all `ARMO` records: cuirass (body), helmet (head), gauntlets (hands), boots (feet), and
**shields/bucklers**, in both **light and heavy**, plus clothing and jewelry *frames* (AR-0
ARMO — robes, rings, amulets, circlets). What it does **not** do: design the enchantment *effect*
on an enchanted piece (route that to the `requiem-magic-patching` skill — this skill only links an `ObjectEffect`),
and *author* race-add ARMA wearability for a new race (route to the `requiem-race-patching` skill).
An `ARMA` record **linked from an ARMO you patch is still dispositioned in this lane** (usually a
cosmetic-skip — it carries no balance fields — but the disposition is recorded here, never waved to
the race lane). Set the frame here; route the rest onward.

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

2. **Classify the piece — three coordinates decide the comparable:**
   - **Part:** body / head / hands / feet / shield (or AR-0 clothing/jewelry frame).
   - **Weight class:** light or heavy (read `BodyTemplate.ArmorType`). Many materials exist in
     **both** (dwarven, orcish, chitin, stalhrim, falmer) — pick the one the piece is.
   - **Material / set:** iron, steel, elven, glass, … or a named faction set (nightingale,
     ancient-shrouded). The set keyword drives value, resist tier, and tempering.

   Then branch:
   - **Armored piece** (AR > 0) → the main `## Workflow` below.
   - **Clothing / jewelry frame** (AR 0) → `references/keywords.md` § Clothing & jewelry frames;
     value tracks material, no resist/tempering, route any enchant to the `requiem-magic-patching` skill.
   - **Unique / faction / artifact piece** → workflow for the skeleton, then `## Judgment`.

## Bulk pass protocol (whole-plugin jobs)

When you're patching a whole plugin — routed here from the `requiem-patching` skill, or any job with
more than a handful of `ARMO` records — the enumeration **is the work queue**, not a sample of it. A
big armor mod hides its hard records inside uniform-looking families: the one cuirass with a
hand-tweaked AR, the back-slot cloak on biped 46, the enchanted variant sitting among plain siblings.
Those off-ladder outliers are exactly what a rebalance pass exists to catch — and the fastest way to
ship them wrong is to patch one piece of a set and assume the rest match.

Open with the triage matrix — one call, the whole plugin at once, and it doesn't write:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="ARMO" \
  fields=["Name","ArmorRating","BodyTemplate.ArmorType","BodyTemplate.FirstPersonFlags","Keywords","ObjectEffect"] \
  resolve_names=true format="dense"
```

`format="dense"` returns one positional row per record under a single column header rather than a
labelled envelope per field — on a big armor mod that is the difference between a readable table and
a wall of repeated keys. `resolve_names` annotates the `Keywords` and `ObjectEffect` links with what
they point at, so you triage from names instead of FormIDs. Page a large plugin with `limit=` +
`offset=`; the windows tile exactly while the load order is unchanged.

Scope deliberately: this enumerates every `ARMO` the plugin **touches**, overrides included, which is
what the coverage count below reconciles against. `defined_in=true` narrows to only the records the
plugin *defines* — useful when you want its new content alone, but it drops the vanilla armors the
mod overrides, so don't reach for it on a coverage pass. Under a `plugins=` scope the fields are that
plugin's own values; add `winner_fields=true` when you need what currently wins instead.

Read each row to disposition the piece before any per-record work:

- **`BodyTemplate.ArmorType`** — `HeavyArmor`/`LightArmor` → the armored `## Workflow`; `Clothing` →
  a clothing/jewelry frame (`references/keywords.md`). No armor type at all on a body-slot record is a
  nudge to check whether it's a skin or template `ARMO` (skip — below).
- **`ArmorRating`** — `> 0` armored vs `0` cosmetic/clothing (a body-slot piece with a real AR but a
  cloth archetype is a mis-tagged cosmetic worth a `## Judgment` look).
- **`BodyTemplate.FirstPersonFlags`** + the **part keyword** — which slot the piece occupies, so you
  pick the right per-part comparable and catch an off-slot 46/47 accessory the ratio table doesn't
  model.
- **`ObjectEffect`** — a non-null link means the piece is enchanted: patch the ARMO frame here and
  route the effect *design* to the `requiem-magic-patching` skill.

**Every FormID gets a disposition.** Walk the full enumeration: each record is **patched** (note
which part/material/weight workflow) or **skipped** (note the reason, verified on *that* record). The
skip categories to name explicitly:

- **Template / no-slot `ARMO`** — a base or template record with **no biped slots**, worn by no
  one; there is nothing to balance. This skip does **not** extend to a `NonPlayable` piece an NPC
  actually wears: the player feels its armor rating in every fight, so a worn NonPlayable piece is
  derived at its playable twin's (or wielder's) tier — AR, armor-type/set/part keywords, the lot —
  and skips only the player-economy surface (recipes, value as authored).
- **Skin / naked-body `ARMO`** — a race's body-skin ARMO that renders the body, not a worn piece;
  leave it to the race layer.
- **Creature-skin `ARMO`** — a creature's natural-armor skin; creature protection is an actor/race
  concern (`requiem-npc-patching` / `requiem-race-patching`), not this skill.
- **Already-Requiem-consistent** — a piece whose winner already carries the correct set/part
  keywords and on-ladder AR (often a WAR cross-patch already covers it); confirm on the record, then
  leave it.

**Patched means field-complete, not merely touched.** A record counts as patched only once the
per-record **Checklist** (below) passes for *that* piece — the armor-type + part keyword, the set
keyword, on-ladder AR/value/weight, the fist perk where it applies, the recipes, masters, and the
`REQ_NULL` scan. Setting one field and moving on is not a disposition; the Checklist is the gate that
turns "touched" into "patched." **Flags fields are unions, not scalars:** a flags write (record
`Flags`, `BodyTemplate` flags) replaces the whole bitfield — read the winner's bits first and write
original-bits + your change, or you silently strip `NonPlayable` and its kin.

Close the pass with a **reconciliation count — patched + skipped = enumerated.** If the two sides
don't add up, a record fell through; find it before you call the type done. (This is the per-record
coverage the `requiem-patching` skill's integration checklist gates on for high-count jobs.)

When the sweep's product is a **deliverable** — a catalogue, audit table, or conflict survey rather
than a set of edits — load houseCARL's `bulk-record-jobs` skill before the first query. It maps
many-records-to-one-deliverable jobs onto the bulk primitives and pins a canonical output schema,
which is what stops a fan-out inventing a different shape per worker.

**Never extrapolate across a set, a light/heavy pair, a material tier, or an enchanted variant.** A
full set (cuirass + boots + gauntlets + helmet + shield), a material's light-and-heavy pair, a run of
same-material tiers, and the enchanted variants of one base piece all *look* uniform — but modders
hand-tweak individuals: a sibling may carry its own off-ladder AR, an off-slot biped tag (46/47), or
a bespoke `ObjectEffect`. **Read and disposition each member.** The per-part ratio table (Workflow
step 3) derives a *target* from Requiem's comparable — it never substitutes for reading the new mod's
actual sibling record, which is the only thing that tells you what that piece really carries.

## Workflow

### 1 — Find Requiem's comparable

Requiem names armor `REQ_<Weight>_<Material>_<Part>` (`REQ_Heavy_Steel_Body`,
`REQ_Light_Glass_Shield`), so a substring scan finds them fast:

```
housecarl_cross_plugin_query type="ARMO" editorid_contains="REQ_Heavy_Steel" \
  plugins=["Requiem.esp"] limit=200 format="dense"
```

Parts are `Body` (cuirass), `Head` (helmet), `Hands` (gauntlets), `Feet` (boots), `Shield`,
`Buckler`. Pick the canonical record — **not** a `REQ_NULL_*` (a NULLed duplicate/leveled stub
with junk stats) or a `REQ_Var_*` variant. If the new piece's material has no Requiem equivalent
(a novel alloy), pick the tier it should sit at and derive from that tier's comparable. If you
only have a different *part* in that material, use the **cuirass (body) as the spine** and apply
the per-part ratio in step 3.

### 2 — Read the comparable's winner

Read every comparable the job needs in **one** call — the whole set's parts, not one piece at a
time. `batch_record_detail` resolves each FormID to its winner, so a five-piece set is one read
(below: Requiem's heavy steel feet, body, hands, head, shield):

```
housecarl_batch_record_detail \
  formids=["013951:Skyrim.esm","013952:Skyrim.esm","013953:Skyrim.esm","013954:Skyrim.esm","013955:Skyrim.esm"] \
  fields=["Name","ArmorRating","Value","Weight","Keywords","BodyTemplate","BashImpactDataSet","AlternateBlockMaterial"] \
  resolve_names=true
```

`resolve_names` renders each keyword as its EditorID, which is how you tell an `armorSet` keyword
from a Reqtificator-assigned `REQ_Tempering_*` at a glance — the distinction step 4 turns on.

No `conflict_tree` here: the fields returned are the winner's either way, and the chain was already
established by the freshness probe. Reach for `conflict_tree=true` when you specifically need to see
*which* layer contributed a value — not to read the winner, which is the default.

Read the **winner**, which already folds in WAR (`Requiem - Weapons and Armor Redone.esp`, which
owns shields/bucklers + cross-patches) over base `Requiem.esp`, plus USSEP fixes Requiem inherits.
On a live profile the winner may be `Requiem for the Indifferent.esp` — still the authority: if
the Reqtificator rescaled the comparable, the read folds the rescale in automatically. That is
exactly the value a patch must be consistent with — you never do manual exclusion math.
(Reqtificator-*assigned* keywords on the winner — resist tier, tempering — remain build outputs
you never hand-copy; see Common mistakes.)

### 3 — Derive armor rating, value, weight

Requiem's armor rating is stored at **~10× the vanilla scale** (an iron cuirass reads
`ArmorRating = 250`, vanilla 25) — these are Requiem's own clean ladders, not vanilla numbers.
Copy the comparable and adjust along two axes (full tables in `references/ar-ladder.md`):

- **Material spine (the cuirass/body value per material).** Heavy: Iron 250 → Steel 300 →
  SteelPlate/Dwarven 400 → Orcish 450 → Ebony/Stalhrim 500 / Dragonplate 500 → Daedric 600.
  Light: Fur 125 → Hide/Leather 150 → Scale 175 → Elven/Chitin/Dwarven 200 → Glass/Stalhrim 275 →
  Dragonscale 300. Value and weight track the **set/material keyword**, jumping steeply at glass
  and above.
- **Per-part ratio (off that material's cuirass).** The same factor scales armor rating, value,
  and weight — except the shield, where AR and value/weight differ:

  | Part | Armor rating | Value | Weight |
  |---|---|---|---|
  | Body (cuirass) | ×1.00 | ×1.00 | ×1.00 |
  | Head (helmet) | ×0.40 | ×0.40 | ×0.40 |
  | Hands (gauntlets) | ×0.30 | ×0.30 | ×0.30 |
  | Feet (boots) | ×0.30 | ×0.30 | ×0.30 |
  | Shield | ×0.60 | ×0.50 | ×0.50 |

  So an ebony cuirass (AR 500 / Val 5000 / Wt 40) implies ebony gauntlets AR 150 / Val 1500 /
  Wt 12, and an ebony shield AR 300 / Val 2500 / Wt 20. **Prefer the same-part same-material
  comparable's values directly;** use the ratio only when none exists.

### 4 — Set keywords

Keywords are what the Reqtificator's rules match on, so they are load-bearing. Read them off the
comparable and replicate (FormIDs and the full vocabulary in `references/keywords.md`):

- **Standard armored piece** carries exactly four:
  `[armorType (heavy 06BBD2 | light 06BBD3), armorSet<Material>, armorPart<Part>, VendorItemArmor 08F959]`.
- **Heavy gauntlets add `PerkFists<Material>`** (iron `0424EF`, steel `09379E`, ebony `02C178`,
  daedric `02C17B`, …) — the unarmed-damage keyword. **Light gauntlets do not** carry it; only
  the heavy line gets the fist perk. Copy it from the same-material heavy-gauntlet comparable.
- **Shields** drop the body-slot part keyword and carry the shield part keyword `0965B2` instead.
- **Do not hand-stamp the ranged-resistance tier or the tempering perk keyword.** Exactly like the
  weapon damage-type keyword, these are **Reqtificator OUTPUTS** — absent from every Requiem
  source armor. The Reqtificator assigns them at build time from the armor-set + armor-type
  keywords (`feature_damageResistances` and `feature_tempering` in
  `ArmorKeywordAssignments_Requiem.esp.conf`). Get the **set keyword** right and resist + tempering
  follow automatically. Ranged resistance is assigned **only to the cuirass** (it gates on the
  cuirass part keyword); tempering is assigned to every piece with the set keyword.

### 5 — Sounds, block & impact

Armor body/head/hands/feet pieces carry null pickup/putdown links (generic armor foley). **Shields**
carry `BashImpactDataSet` and `AlternateBlockMaterial` (the block/bash material) — copy these
verbatim from the same-material shield comparable. Jewelry and clothing pickup/putdown sounds are
overridden at the descriptor level by `Requiem - Immersive Sounds Compendium.esp` (for rings it is
the record-level winner) — reading the comparable's winner already folds that in. Details in
`references/keywords.md` § Sounds.

### 6 — Crafting + tempering recipes

Each Requiem armor has up to three `ConstructibleObject` recipes. Find the comparable's via
reverse-lookup on the armor FormID:

`references=` takes a **list** — look up the whole set's recipes in one call, not one per piece:

```
housecarl_cross_plugin_query type="COBJ" \
  references=["013951:Skyrim.esm","013952:Skyrim.esm","013953:Skyrim.esm","013954:Skyrim.esm","013955:Skyrim.esm"] \
  fields=["CreatedObject","WorkbenchKeyword"] resolve_names=true format="dense"
```

The `matches` column names which armor each recipe belongs to, and `resolve_names` renders the
workbench as `→ CraftingSmithingForge` / `CraftingSmithingArmorTable` / `CraftingSmelter`, so the
recipe kind reads straight off the row. Then expand the ingredient and condition lists over the
recipe FormIDs you just found — `cross_plugin_query` has no `depth=`, so they arrive as
`[list: N item(s)]` until you do:

```
housecarl_batch_record_detail formids=["<the recipe FormIDs>"] \
  fields=["Items","Conditions"] depth=4 resolve_names=true
```

**`depth=4`** is the working depth: it yields `Items[i].Item.Item` (→ `IngotSteel "Steel Ingot"`),
`Items[i].Item.Count`, and `Conditions[0].Data.Perk` resolved to its perk name. `depth=2` shows only
`[ContainerEntry]` / `[ConditionFloat]` element types and `depth=3` stops short of the values — both
leave you guessing at exactly the quantities and perk gate you are supposed to clone.

- **Forge** (`WorkbenchKeyword` = `CraftingSmithingForge 088105`): a `HasPerk` condition (the
  smithing-perk gate) + material `Items` (ingots + leather strips).
- **Temper** (`WorkbenchKeyword` = `CraftingSmithingArmorTable 0ADB78` — the **armor workbench**,
  *not* the grindstone weapons use): a `HasPerk` condition + one ingot.
- **Smelter** (`WorkbenchKeyword` = `CraftingSmelter 0A5CCE`): melts the armor back to ingots
  (optional).

Author the new recipes by cloning the comparable's `Conditions` and `Items` and swapping
`CreatedObject` to your armor. Read the comparable's condition at `depth=4 resolve_names=true` (the
depth at which the perk renders as a name) and compose the same gate onto the new recipe — the condition
grammar is in `references/housecarl-recipes.md` § E. Recipe shapes in `references/crafting.md`.

### 7 — Emit the override

Author with houseCARL into a patch plugin (originals are never touched). Copy-ready
`create_record` / `set_field` / `bulk_apply` call shapes are in `references/housecarl-recipes.md`.
Because armor patches reference Requiem-defined keywords, `Requiem.esp` is pulled in as a master
automatically; if a patch happens to reference only vanilla keywords, add `Requiem.esp` as a
master manually.

For end-to-end derivations of each shape (heavy set piece, light set piece, shield, and a unique
faction armor), see `references/worked-examples.md`.

## Judgment

The ladder is the default, not a straitjacket. The calls that need real judgment are **whether a
piece is a unique/artifact**, **how the AR cap interacts**, and **which set keyword a novel
material borrows**.

### Is it a unique/artifact? Identify before you deviate

There is no record field that means "artifact." Infer authorial intent from a priority ladder,
strongest signal first (same method as weapons):

1. **Is it a known Requiem artifact (or a copy)?** Check the `REQ_Artifact_*` set and
   `…\Reqtificator\documentation\Artifacts.md`. If so, mirror that treatment and stop.
2. **Trace how it is obtained — the decisive signal.** Reverse-reference the armor's FormID:

   ```
   housecarl_cross_plugin_query type="ConstructibleObject" references=["<armor FormIDs>"] format="dense"
   housecarl_cross_plugin_query type="LeveledItem"         references=["<armor FormIDs>"] format="dense"
   ```

   Pass every candidate at once — `references=` is a list, and the `matches` column tells you which
   piece each hit belongs to, so one pair of calls dispositions a whole set's craftability and loot
   presence rather than two calls per piece.

   A genuine artifact is **not craftable** (no forge COBJ), **not in leveled lists**, and reaches
   the player as a **single fixed reward**. If it has a forge recipe or sits in loot lists, it is
   **custom gear, not an artifact** — give it normal part/material treatment.
3. **Record heuristics only nominate** — a bespoke `ObjectEffect` or an off-tier AR is a weak
   nudge, never a decision. Authorial intent (the mod page, how a quest grants it) is the real
   arbiter and isn't fully in the data.
4. **If it is still ambiguous, say so and ask.** A wrong "artifact" call inflates value and strips
   craftability; a wrong "not" call flattens a real unique — both worse than asking.

### Armor-rating cap interaction

The Reqtificator's `armorRatingThresholds` (`Reqtificator.conf`) are per-slot caps in the **÷10
(vanilla) scale** — heavy body 74 / feet 27 / hands 27 / head 35 / shield 54; light body 62 /
feet 18 / hands 18 / head 26 / shield 44. Multiply by 10 to compare to the record's `ArmorRating`
(heavy body ≤ 740, etc.). Requiem's own top tier sits comfortably under: a daedric cuirass is 600
(60 of a 74 cap), leaving headroom for enchanted/unique pieces. **Derive AR from the comparable
and you are automatically under the cap.** A modded piece whose AR is wildly high gets clamped by
the Reqtificator — set it on-ladder so it isn't silently flattened.

### Light vs heavy, and novel materials

- **Light vs heavy** is the `BodyTemplate.ArmorType` + the `armorType` keyword; it picks the
  ladder, the resist behaviour (heavy → tier5 flat; light → tier by set), and the fist-perk rule.
  If the mod is ambiguous, choose by the set's archetype and say which you chose.
- **A novel material with no Requiem set keyword** still needs *a* set keyword so the Reqtificator
  assigns a resist tier and tempering perk. Pick the closest Requiem set (by archetype and tier),
  state the choice, and use its tempering/resist behaviour. Value tracks that borrowed tier.

### Cosmetic cloth and extra-slot accessories

Requiem's protection is **slot-based** — it balances around the four body slots (cuirass, helmet,
gauntlets, boots) plus a shield. Two common modded shapes break that model and need a deliberate
call:

- **Cosmetic cloth mis-tagged as armor.** Outfit/lingerie/"cloth" mods often ship pieces as
  `LightArmor` carrying the **cuirass part keyword** (`06C0EC`) and a real armor rating. Because
  the Reqtificator assigns ranged resistance to *anything* with the cuirass keyword, several such
  "cuirass" pieces worn at once stack resistance and protection outside the slot model. The
  Requiem-consistent fix is to make them **clothing frames** — `ArmorRating 0`,
  `BodyTemplate.ArmorType = Clothing`, drop the armor-type/set/cuirass keywords, and use
  `[ArmorClothing 06BBE8, VendorItemClothing 08F95B]` (jewelry → `[ArmorJewelry, VendorItemJewelry,
  slot]`). If a piece is genuinely meant to be functional light armor, put it on the cloth/leather
  ladder instead — say which reading you chose.
- **Extra-slot armored accessories** (a standalone pauldron, scarf, cloak, back-piece on a
  non-standard biped slot like 46/47) add protection on top of the four slots. Requiem itself models
  pauldrons as *cuirass mesh variants* (body slot), never as separate armored slots. Set such
  accessories to **`ArmorRating 0`** (cosmetic) so they don't exceed the intended per-slot cap, and
  say so — the author may have intended them as armor.

When you cannot find a clean comparable, say so explicitly — name what you checked and what you
assumed — rather than emitting a confident guess.

## Common mistakes

- **Hand-stamping the ranged-resistance or tempering keyword.** They are Reqtificator outputs, not
  source data; adding them yourself fights the build pass. Set the **armor-set** keyword instead.
- **Forgetting `PerkFists<Material>` on heavy gauntlets** (or wrongly adding it to light ones).
  Heavy gauntlets carry it; light gauntlets don't.
- **Tempering at the grindstone.** Armor tempers at the **armor workbench** (`0ADB78`); the
  grindstone (`088108`) is for weapons.
- **Treating the AR cap (74) as the record value.** The record stores ~10× that; don't clamp a
  cuirass to 74 — derive its AR (e.g. 500) from the comparable.
- **Copying vanilla armor ratings.** Vanilla numbers are not Requiem numbers — always derive from
  a Requiem comparable.
- **Deriving from a `REQ_NULL_*` or `REQ_Var_*` stub.** Those are NULLed dead records; use the
  canonical `REQ_<Weight>_<Material>_<Part>`.
- **Re-pointing because `Requiem for the Indifferent.esp` wins.** On a live profile the
  Reqtificator's output winning is the healthy state and its values are the authority — never
  `set_mo2_instance` to escape it; re-pointing away from the instance you're patching breaks
  every subsequent read.
- **Leaving armor rating on a clothing/cosmetic piece.** Clothing must be `ArmorRating 0` in
  Requiem; a robe or towel with AR (or a cosmetic-cloth piece tagged with the cuirass keyword) gets
  treated as armor and assigned ranged resistance. See `## Judgment` → cosmetic cloth.

## Checklist

Before finishing an armor override, confirm:

- [ ] **Whole-plugin job:** every enumerated `ARMO` dispositioned — patched (the field checklist
      below passed for that piece) or skipped with a reason (template/no-slot, skin/naked-body,
      or creature-skin `ARMO` named — a *worn* `NonPlayable` piece is tiered, not skipped); counts
      reconcile (patched + skipped = enumerated); no set/pair/tier/variant extrapolation.
- [ ] **Armor-type keyword** (heavy/light) and **armor-part keyword** present, matching
      `BodyTemplate.ArmorType` and the biped slot.
- [ ] **Armor-set / material keyword** present (drives value tier, ranged resistance, tempering) —
      and the resist-tier and tempering keywords are **not** hand-added.
- [ ] **ArmorRating** on-ladder for the part + material + weight class (≤ the ×10 cap).
- [ ] **Value** and **Weight** on-tier (per-part ratio off the cuirass; shield value/weight ×0.50).
- [ ] **Heavy gauntlets**: `PerkFists<Material>` present. **Light gauntlets**: absent.
- [ ] **Shields**: `BashImpactDataSet` + `AlternateBlockMaterial` copied from the comparable.
- [ ] **Name** encodes material + part ("Ebony Gauntlets", "Glass Shield").
- [ ] **Crafting recipe** (forge) + **tempering recipe** (armor workbench) with the cloned perk
      gate and correct ingot inputs.
- [ ] **Flags written as unions** — any flags write carries the winner's original bits plus your
      change; no bit silently dropped.
- [ ] **`Requiem.esp` is a master** of the patch (auto when a Requiem keyword is referenced) —
      masters list correct + load-order-sorted (verify the `masters:` read-back).
- [ ] **No `REQ_NULL_*` reference remains** in any patched field — strip it or replace it with the
      live Requiem form (houseCARL resolves the EditorID live against the load order).
- [ ] **Enchanted pieces**: `ObjectEffect` linked; the effect *design* routed to the `requiem-magic-patching` skill.
      **AR-0 jewelry/clothing**: value tracks material; no resist/tempering.

## Notes

- **Authority** = houseCARL's live conflict winner. Hand-authored armor overrides come from
  `Requiem - Weapons and Armor Redone.esp` (WAR; shields, bucklers, cross-patches) over core
  `Requiem.esp`, plus their patches (`Requiem - New Legion.esp`, `Requiem - Creation Club.esp`,
  `Sons of Skyrim - Requiem Patch.esp`, …); on a live profile `Requiem for the Indifferent.esp`
  folds the build pass over them and is the winner to derive from.
- **Enchantment-effect design** (the MGEF/ENCH on an enchanted piece) → the `requiem-magic-patching` skill.
  **Race/ARMA models** (which races can wear it) → the `requiem-race-patching` skill. This skill sets the ARMO frame
  and links an existing `ObjectEffect`.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is
  later run through the Reqtificator, which applies the auto-balance pass over it.
