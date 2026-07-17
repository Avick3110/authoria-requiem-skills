---
name: requiem-consumable-patching
description: Patch a new or modded consumable for Requiem via houseCARL — potions, poisons, oils, food, drink, alcohol, and ingredients (ALCH + INGR). Derive effects, magnitude/duration, gold value, weight, flags, keywords, and the cookpot crafting recipe from Requiem's own comparable in Alchemy Redone and Food and Beverages Redone, and emit a direct ESP override that IS the final balance — the Reqtificator never rebalances consumables. Use when the user wants to patch a potion, poison, food, drink, mead, ale, wine, skooma, or ingredient for Requiem, balance a modded consumable, set Requiem food effects or nutrition, fix a potion whose healing or price is off, map an ingredient's four alchemy effects, make a consumable craftable at the cookpot, or integrate a mod's food and drink. Load this before touching an ALCH or INGR record — consumables have no build-time safety net, so a wrong override ships wrong.
---

# Requiem Consumable Patching

## Overview

This skill patches a consumable so it is consistent with Requiem — the right effect kit for its
class, magnitudes and durations on Requiem's ladders, a sane gold value and weight, the flags and
keywords Requiem's survival/economy layers key on, and (when appropriate) a cookpot crafting
recipe. The output is a **direct ESP override** authored with houseCARL `housecarl_create_record` /
`housecarl_set_field` / `housecarl_bulk_apply`.

**The one fact that anchors everything: the Reqtificator does not touch consumables.**
`Requiem for the Indifferent.esp` defines only NPCs and leveled NPCs; it neither defines nor
overrides a single ALCH, INGR, or COBJ. Unlike weapons, armor, and NPCs — where you carry inputs
and the build pass emits the final balance — **your consumable override IS the final balanced
record.** Every number you set ships as-is. That raises the bar on the read side: derive
everything from a live comparable, because nothing downstream will correct you.

The method is **live-analogy, never hardcoded numbers.** Requiem's consumable balance lives in two
addons — `Requiem - Alchemy Redone.esp` (potions, poisons, oils, ingredients) and
`Requiem - Food and Beverages Redone.esp` (food, drink, alcohol) — plus the hand-authored patch
layers a curated list stacks on top of them. Find the closest comparable of the same class and
tier, read its **live winner** via houseCARL, and derive from that. The mined ladders and kits in
`references/` are a cross-check and a map; a live read of the comparable is always the authority.

Covers ALCH (all ingestibles) and INGR, plus their COBJ recipes. Effect *design* (a genuinely new
MGEF) routes to `requiem-magic-patching`; world placement (leveled lists, vendor chests) routes to
`requiem-leveled-list-patching`.

## First step

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; if it is the wrong instance, fix it with
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. Then sanity-check Requiem is present:
   read Iron Sword `012EB7:Skyrim.esm` `conflict_tree=true` → `Requiem.esp` must appear in the
   override chain. Either winner shape is valid; the live winner is the authority to derive from.
   Full doctrine: the `requiem-patching` skill's `references/scope-and-authority.md`.

2. **Classify the consumable.** The class picks the derivation kit, and the classes use two
   *opposite* techniques (see Workflow step 3):
   - **Potion / poison / oil** — `Flags` carry `NoAutoCalc` (+ `Poison` for poisons/oils); no
     `FoodItem`; keyword `VendorItemPotion` or `VendorItemPoison`.
   - **Food** — `Flags = NoAutoCalc, FoodItem`; carries a hunger-size effect + a Nutrition effect.
   - **Drink / alcohol** — food-flagged, but the effect kit is the `REQ_Alcohol_*` pair.
   - **Drug** (skooma-class) — `NoAutoCalc` only, **no** `FoodItem`: Requiem's survival layer
     treats it as a potion; strong short buffs + longer drains, no hunger/nutrition.
   - **Ingredient (INGR)** — its own record type and payload (the 4-effect quartet).

   Classify a *modded* record by what it is meant to be, not by its current flags — mods routinely
   ship food as auto-calc potions. A mod's own classification is a claim to verify, not a fact.

## Bulk pass protocol (whole-plugin jobs)

When you're patching a whole plugin — routed here from the `requiem-patching` skill, or any job
with more than a handful of consumables — the enumeration **is the work queue**, not a sample of
it. A consumable plugin hides its hard records inside uniform-looking groups: the one "potion"
that is actually a scripted quest item, the mead the author priced like a vanilla ale, the raw
meat with no food-poisoning risk. Open with two sweeps and use them as the triage table:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="ALCH" \
  fields=["Name","Value","Weight","Flags","Keywords"]
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="INGR" \
  fields=["Name","Value","Weight","Flags"]
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="FLOR" \
  fields=["Name","Ingredient"]
```

Read each ALCH row's `Flags` (FoodItem? Poison? auto-calc still on?) and `Keywords` to give every
record a class from First step 2; every INGR row goes to the ingredient lane. The FLOR sweep covers
**harvestable food-plants**: a Flora record carries no balance of its own, but its `Ingredient`
link (the harvest yield) must point at a consumable this pass dispositioned — FaB itself overrides
52 Flora records to re-point food plants, so a plant whose yield you rebalanced rides this lane
(verify the link resolves; re-point it if the mod's plant yields an unpatched duplicate).

**Every FormID gets a disposition** — **patched** (routed to a class lane, and that class's field
checklist passed on *that* record) or **skipped** with a reason verified on that record, never
inherited from a neighbour. The skip categories to name:

- **Script-carrier pseudo-consumables** — an ALCH that exists to fire a script/quest event
  (`VirtualMachineAdapter` present, no real balance payload); route the script side to
  `requiem-script-patching`, note the shell here.
- **Non-playable / prop records** — never obtainable; no economy to balance.
- **Duplicate/deprecated stubs** — the mod's own dead variants; and anything resolving to a
  `REQ_NULL_*` form is never a comparable *or* a patch target.

**Patched means field-complete, not merely touched.** A potion you re-valued but left auto-calc
on, or a food you gave effects but no hunger/nutrition tag, is in progress, not patched. Run the
class checklist per record before you mark it done. **Flags fields are unions, not scalars:** an
ALCH/INGR `Flags` write replaces the whole set, so read the winner's flags first and write
original-bits + your change — a literal Set silently strips `FoodItem`, `Poison`, `Medicine`, and
their kin from records that carried them.

Close with a **reconciliation count — patched + skipped = enumerated, per type (ALCH and INGR
separately).** If the sides don't add up, a record fell through; find it before the type is done.

**Don't extrapolate across a uniform-looking family.** The consumable family shapes that tempt
one read applied to the whole group:

- **tier ladders** — a mod's Minor/Regular/Greater potion line: each tier is still read; Requiem's
  own ladders have per-tier hand-set values and effect-specific magnitudes;
- **same-effect lines** — every "healing" item in the mod; Requiem's fortify-skill magnitudes are
  effect-specific (Lockpicking 1 vs ArmorRating 120 at the same tier) — never borrow a sibling
  family's numbers;
- **raw/cooked pairs** — cooking changes the whole kit (paralysis dropped, nutrition upgraded,
  buffs lengthened), not one field;
- **alcohol strength tiers** — buff, debuff, duration, and value all move together per tier;
- **same-prefix EditorID lines** and **ingredient rarity peers** — the hand-tweaked outlier
  inside a uniform family is exactly what the pass exists to catch.

## Workflow

### 1 — Find the live comparable

Pick the comparable by **class + family + tier** (potion: same effect family and tier; food: same
food family, prep state, and meal size; alcohol: same strength tier; ingredient: same role/rarity
peer). Requiem names consumables `REQ_Potion_*`, `REQ_Poison_*`, `REQ_Oil_*`, `REQ_Food_*`,
`REQ_Alcohol_*` / legacy `REQ_Drink*`, `REQ_Ingredient_*` — substring scans find them fast:

```
housecarl_cross_plugin_query type="ALCH" editorid_contains="REQ_Potion_RestoreHealth" \
  plugins=["Requiem - Alchemy Redone.esp"]
```

Two naming caveats: the schemes are **not uniform** (`Meat_<X>Raw` and `Meat_Raw_<X>` coexist, as
do `REQ_Alcohol_<Strength>_*` and `REQ_Drink[N]_*`), so search by the effect/family token, not a
memorized full pattern; and a handful of fully-tuned records were never renamed at all (bare
`Skooma`). The ladders and family maps in `references/potion-poison-ladders.md` and
`references/food-drink-kits.md` tell you which family to search for.

### 2 — Read the comparable's winner

```
housecarl_batch_record_detail formids=["03EADF:Skyrim.esm"] depth=2 resolve_names=true \
  fields=["Name","Value","Weight","Flags","Effects","Keywords"]
```

Read the **live winner** and expect it to *not* be the addon itself — that is normal and is the
authority. Measured on this class of load order: magic-school fortify potions resolve to
`Requiem - Magic Redone.esp`; poison MGEFs to a downstream compatibility patch; most cooked food,
soups, and alcohol to hand-authored layers stacked above FaB (weight normalization, dietary
EditorID re-taxonomy, name labels). Whatever plugin wins is the balance the game actually plays —
derive from it. One exception class: a winner that has *broken* the addon's own conventions (e.g.
a forwarder that turned auto-calc back on and left a placeholder `IngredientValue`) is a
convention-broken baseline — flag it and derive from a convention-conforming peer instead
(`references/ingredients.md` § Anomalies).

### 3 — Derive by class

The potion and food lanes use **opposite techniques** — the deciding factor is whether the item's
effects point at MGEFs Requiem already overrides:

- **Potion / poison / oil — keep the links, rebalance the shell.** A modded potion whose effects
  point at vanilla MGEF FormIDs (Invisibility `03EB3D:Skyrim.esm`, CureDisease, NightEye,
  Waterwalking…) needs **no effect rebuild**: Requiem overrides those MGEFs in place, so the
  FormID already resolves to the `REQ_Alch_*` behaviour. Set `Flags = NoAutoCalc` (+ `Medicine`
  where the comparable carries it), set `Value` from the matching ladder tier, zero any
  per-item magnitude the REQ MGEF ignores (fixed-behaviour effects), keep Weight 0.5, and set the
  single vendor keyword. Magnitude/duration for tiered effects come from the same-family
  same-tier comparable — the scaling axis varies by family (most scale magnitude at fixed
  duration; invisibility/waterbreathing/paralysis/silence scale **duration** at magnitude 0).
  Ladders: `references/potion-poison-ladders.md`.
- **Food — rebuild the effect list to the kit.** Vanilla-style restore-on-eat effects are wrong
  in Requiem (food is buff-over-time; sweets' instant restore is the lone exception). Compose:
  1–2 themed fortify/regen effects (meat→Health/CarryWeight/Melee; bread→Stamina;
  cheese→Stamina+Magicka; fruit/veg→ResistDisease+HealthRegen; soup/stew→large, long fortifies) +
  exactly one `Survival_FoodRestoreHunger{VerySmall|Small|Medium|Large}` tag (size = meal bulk,
  not stat power) + exactly one Nutrition tag (`…SM` raw/light, `…HS` cooked/rich). **Raw meat
  and fish additionally carry the food-poisoning paralysis effect; cooking removes it** and
  upgrades nutrition + buffs. Preserve a trailing eating-animation marker effect (magnitude 0) if
  the mod's stack ships one. Racial-cuisine gates (Green Pact, beast stomachs, per-race alcohol
  affinity) are **keywords on the food**, not on the race. Kits, magnitudes, keyword taxonomy:
  `references/food-drink-kits.md`.
- **Alcohol** — the fixed pair `REQ_Alcohol_FortifyHealth 18ED7C:Requiem.esp` +
  `REQ_Alcohol_DamageRegeneration AE3729:Requiem.esp` ("Inebriation") + Small hunger +
  `REQ_Apo_AlcoholNutrition`, magnitudes by strength tier (Moderate 45/15/10 → Strong 90/30/15 →
  Extreme 120/45/30 + a flavor elemental resist). There is **no ALCH addiction field** —
  tolerance/withdrawal is a script MGEF; don't invent one.
- **Drug** — `NoAutoCalc` only (no `FoodItem`), strong short buffs + longer drains, no
  hunger/nutrition tags.
- **Ingredient — emit the full payload.** `Flags = NoAutoCalculation`; exactly **4 effects**, each
  an existing `REQ_Alch_*` MGEF (map each intended vanilla effect to its REQ analogue — find them
  with `editorid_contains="REQ_Alch"`); **duration is standardized per-MGEF** (copy it from any
  peer bearing that effect); **magnitude is the tier knob** (copy from a rarity/value peer — a
  discrete ladder, not a formula); `Value == IngredientValue` (decouple only for a deliberate
  expensive-but-weak novelty); weight from the physical-archetype ladder; vendor keyword by
  commercial role (`VendorItemIngredient` default; food tags *replace* it for edibles; black-market
  for contraband; none for gore). Full rules: `references/ingredients.md`.

### 4 — Route what isn't yours

- **A genuinely new effect** (no `REQ_Alch_*` / `REQ_Apo_*` / `REQ_Alcohol_*` analogue exists) →
  the MGEF design belongs to `requiem-magic-patching`; this skill wires the finished effect into
  the ALCH/INGR record. Setting the record's shell does not make an unbalanced effect balanced —
  the same handshake weapons use for modded enchantments, in both directions.
- **Placement** (loot lists, vendor chests) → `requiem-leveled-list-patching`. Record the intent
  ("this potion joins the fortify pools"); don't edit LVLI here. Food stays **out of alchemist
  vendor lists** — alchemists don't buy food in Requiem's economy.
- **A script-bearing consumable** (quest tokens, scripted drinks) → `requiem-script-patching` for
  the VMAD side.

### 5 — Crafting recipe (COBJ), when one is due

Craftability is the **exception** for consumables, not the default: FaB defines zero cooking
recipes, and most food is loot/vendor-only. Add a recipe only when the comparable class has one
or the user asks. The Requiem shape (worked examples in `references/crafting-and-integration.md`):

- Station: `WorkbenchKeyword = CraftingCookpot 0A5CB3:Skyrim.esm` — Requiem routes even oils,
  powders, and craft-potions through the **cookpot**, not the alchemy lab.
- Gate (Requiem's own craft-consumables): `HasPerk` on the Requiem Alchemy tree
  (`REQ_Alchemy_AlchemicalLore1 0BE127:Skyrim.esm` / `Lore2 0C07CA:Skyrim.esm`) **OR** the racial
  `HasKeyword REQ_RacialSkills_CreatePotionsUnperked AD3A3B:Requiem.esp`, plus an Alchemy
  skill band; higher tiers raise `CreatedObjectCount` (yield), not potency.
- Plain cooked-food recipes (raw meat → cooked) are conventionally **ungated** — match the lane
  you're extending, and say which convention you followed.

### 6 — Emit the override

Author with houseCARL into a patch plugin (originals are never touched). Copy-ready
`housecarl_create_record` / `housecarl_set_field` / `housecarl_bulk_apply` call shapes — including
the effect-list rebuild via `composes=` — are in `references/housecarl-recipes.md`. Verify masters
on the read-back, and remember there is no Reqtificator pass behind you: what you wrote is what
plays.

## Judgment

- **Unique / quest consumables** may sit far off the ladder (Requiem's White Phial is Value
  50000). Trace how the item is obtained before re-valuing: a single fixed quest reward may keep a
  bespoke value — flag it rather than flattening it; a mass-produced "unique" priced absurdly is
  just wrong and gets the tier value.
- **No clean comparable** (a novel mechanic, a food with no family) → pick the closest analog,
  state the assumption explicitly, and flag it. An explicit "derived from X because Y, verify"
  beats a silently invented number — nothing downstream will catch it.
- **Value is hand-set everywhere.** Every Requiem consumable is `NoAutoCalc`; MGEF `BaseCost` does
  **not** set gold and is not a derivation input. Value comes off the family ladder or the
  comparable, nothing else.
- **The mod's classification is a claim.** A "potion" that is really food (or vice versa) gets
  reclassified to the kit that matches its fiction, and the change is stated in the summary.

## Common mistakes

- **Deriving from the addon's own version instead of the live winner.** AR/FaB are mid-chain on a
  curated load order; the hand-authored layers above them (weight norms, added second effects,
  re-tiered names) are the played balance. Read the winner, whoever it is.
- **Rebuilding a potion's effect list unnecessarily.** If its effects already point at MGEFs
  Requiem overrides, the behaviour is already Requiem's — swap nothing; fix flags/value/magnitudes.
- **Giving food an instant-heal effect** (or any vanilla restore-on-eat kit). Requiem food is
  buff-over-time + hunger + nutrition; instant restore belongs to sweets alone.
- **Forgetting the hunger + nutrition pair on food** — without exactly one of each, the survival
  layer misreads the item. And a drug/potion must *not* carry them.
- **Leaving auto-calc on.** `NoAutoCalc` (ALCH) / `NoAutoCalculation` (INGR) is universal in
  Requiem's consumable corpus; auto-calc silently reprices your item from MGEF BaseCosts.
- **Copying a sibling family's magnitudes.** Fortify-skill magnitudes are effect-specific;
  lingering-damage families don't share a base; Damage Magicka runs 2× Damage Health. Read the
  exact family.
- **Inventing an addiction field for alcohol/skooma.** The ALCH record has none; tolerance is a
  script MGEF. Carry the alcohol keywords and the Inebriation effect instead.
- **Using the alchemy lab as the crafting station** — Requiem's consumable crafts run through the
  cookpot; a lab-keyed recipe puts the item on the wrong bench.
- **Treating ingredient value as computable.** `Value`/`IngredientValue` come off a curated
  discrete ladder by rarity peer; there is no formula.
- **Carrying a `REQ_NULL_*` reference or deriving from one.** Requiem retires records as inert
  `REQ_NULL_*` stubs (one sits in the food corpus); never a comparable, never a target.

## Checklist

Before finishing a consumable override, confirm:

- [ ] **Whole-plugin job:** every enumerated ALCH + INGR dispositioned — patched (class checklist
      passed on that record) or skipped with a per-record reason; counts reconcile per type; no
      family extrapolation (tier ladder, same-effect line, raw/cooked pair, alcohol tier,
      EditorID prefix, rarity peer).
- [ ] **Class assigned** (potion/poison/oil, food, alcohol, drug, ingredient) and its kit used —
      flags match the class (`NoAutoCalc` everywhere; `FoodItem` on food/drink only; `Poison` on
      poisons/oils; `Medicine` where the comparable carries it).
- [ ] **Effects correct for the class:** potion links kept (magnitudes zeroed where the REQ MGEF
      is fixed-behaviour); food kit rebuilt (themed fortifies + exactly one hunger tag + exactly
      one nutrition tag; paralysis on raw meat/fish only; animation marker preserved); alcohol
      pair + tier magnitudes; ingredient quartet of exactly 4 `REQ_Alch_*` effects with per-MGEF
      durations.
- [ ] **Value on-ladder** for the family and tier (hand-set; never from BaseCost); **Weight**
      normalized (potions 0.5; food/ingredient from the family/archetype ladder).
- [ ] **Keywords**: the class's vendor keyword; food's racial-cuisine (`LoreBox_*`) tags where the
      fiction calls for them; alcohol's tags; none on gore/forbidden items.
- [ ] **New-effect handshake:** any genuinely new MGEF routed to `requiem-magic-patching`; the
      record ships only once the effect side is balanced.
- [ ] **Recipe** (only when due): cookpot station, the lane's gate convention, yield-not-potency
      tiering.
- [ ] **Placement intent noted** for `requiem-leveled-list-patching`; food kept out of alchemist
      vendor lanes.
- [ ] **Flags written as unions** — every `Flags` write carries the winner's original bits plus
      your change; no `FoodItem`/`Poison`/`Medicine` bit silently dropped.
- [ ] **Masters correct + load-order-sorted** on the read-back; **no `REQ_NULL_*` reference
      remains** in any patched field.

## Notes

- **Authority** = houseCARL's live conflict winner. The consumable stack, bottom to top:
  vanilla/CC → `Requiem.esp` → `Requiem - Alchemy Redone.esp` / `Requiem - Food and Beverages
  Redone.esp` (+ their thin per-mod satellites) → the load order's hand-authored patch layers.
  The Reqtificator output plugin is **absent** from every consumable chain — that absence is
  structural (it emits only NPC/LeveledNpc), not a broken build.
- **Boundaries:** effect design → `requiem-magic-patching`; placement →
  `requiem-leveled-list-patching`; scripts → `requiem-script-patching`; whole-mod jobs arrive via
  `requiem-patching`, which also owns the alchemy/food gap doctrine at the system level.
- **Systemic knobs are read-only context.** Requiem tunes the alchemy economy through a GMST
  (`fAlchemySkillFactor`), the Alchemy AVIF, and its perk tree. A patch never re-tunes those to
  balance one item.
- **Thrown consumables are a delivery form AR models with SCRL, not ALCH.** AR defines thrown
  powders as Scroll records (`REQ_Powder_Silver 000805:Requiem - Alchemy Redone.esp`) with their
  own PROJ/ArtObject chain. A mod's thrown vial/powder maps to that shape — the SCRL/PROJ design
  routes to `requiem-magic-patching`; this skill owns only an ALCH/INGR sibling if one exists.
- houseCARL writes go to a new patch plugin; the live load order is never modified.
