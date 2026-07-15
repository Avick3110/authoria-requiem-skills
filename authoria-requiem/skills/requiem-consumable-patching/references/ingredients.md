# Ingredients (INGR) — the Alchemy Redone payload

Mined live from load-order winners over all 183 INGR the addon touches (2026-07-15). FormIDs are
wire tokens; bare 6-hex are `:Skyrim.esm`.

## The full payload a Requiem-consistent ingredient carries

Alchemy Redone is the terminal alchemy authority — it re-authors the whole payload over both
vanilla AND Requiem.esp. A new/modded ingredient must emit **all** of it; a partial payload
(e.g. effects swapped but auto-calc left on) silently reprices itself:

| Field | Rule |
|---|---|
| `Flags` | `NoAutoCalculation` — universal (169/183 live winners; the exceptions are a convention-broken forwarder set, below) |
| `Effects` | exactly **4**, each an existing `REQ_Alch_*` MGEF; `Area=0`; no conditions; `Effects[0]` = the first-known ("eat-to-learn") effect |
| `IngredientValue` | a real hand-set number — the alchemy-strength tier |
| `Value` | **== IngredientValue** (168/183). Decouple only for a deliberate expensive-but-alchemically-weak novelty (one precedent: Value 500 vs IngredientValue 15 on a trophy fish) |
| `Weight` | normalized to the archetype ladder below |
| `Keywords` | by commercial role (below) |

## Effects: duration is per-MGEF, magnitude is the tier knob

The same `REQ_Alch_*` effect keeps a **standardized duration** on every ingredient bearing it;
the **magnitude** is the ingredient's potency tier — a small discrete ladder, never computed from
value. Measured durations: RestoreHealth **20** · Fortify attribute **300** · Fortify skill
**60** · instant Damage **0** · Damage…Regeneration **60** · LingeringDamage **15** · Fear **15**.
Magnitude examples: RestoreHealth 0.5 (Blisterwort) / 1 (Wheat) / 2 (Daedra Heart).

Find the pool live: `housecarl_cross_plugin_query type="MGEF" editorid_contains="REQ_Alch"
plugins=["Requiem - Alchemy Redone.esp"]`. The pool is mostly renamed overrides of vanilla MGEFs
(so their FormIDs are `:Skyrim.esm` — e.g. RestoreHealth `03EB15`, FortifyHealth `03EAF3`,
DamageHealth `03EB42`, ResistPoison `090041`, Paralysis `073F30`) plus a few Requiem.esp-defined
members (Dispel `AD3A55:Requiem.esp`, SoulTrap `20B30F:Requiem.esp`). Map each of the new
ingredient's intended vanilla effects to its `REQ_Alch_*` analogue; if no analogue exists, the
effect design routes to `requiem-magic-patching`.

Worked quartets (verbatim live winners):

- **Wheat** `04B0BA` — V/IngrV 1, W 0.1: RestoreHealth m1 d20 · FortifyHealth m8 d300 ·
  DamageStaminaRegeneration m16 d60 · LingeringDamageMagicka m2 d15.
- **Nightshade** `02F44C` — V 8, W 0.1: DamageHealth m8 d0 · DamageMagickaRegeneration m32 d60 ·
  LingeringDamageStamina m2 d15 · FortifyDestruction m0.5 d60.
- **Daedra Heart** `03AD5B` — V/IngrV 1500, W 1: RestoreHealth m2 d20 ·
  DamageStaminaRegeneration m64 d60 · DamageMagicka m32 d0 · Fear m4 d15.

## Value & weight ladders (curated, not formulaic)

Values snap to a discrete set (1, 2, 3, 5, 8, 10, 12, 15, 20, 25, 30, 40, 50, 100, 250, 400,
500, 850, 1500 — common tiers 1/5/15/20/25/30; high end: Crimson Nirnroot 250, Void Salts 500,
Strange Remains 850, Daedra Heart 1500). Weights: ~90% are 0.1 / 0.2 / 0.25 / 0.5 —
flowers/herbs 0.1, salts/small parts 0.2, roots/bulbs 0.25, hearts/organs 0.5–1; physically
large items go above (Mammoth Heart 5). **Derive both by matching a role/rarity peer** — never
by arithmetic.

## Keywords (by commercial role)

- Default: `VendorItemIngredient 08CDEB:Skyrim.esm` (175/183).
- **Edibles** get `VendorItemFood 0A0E55` / `VendorItemFoodRaw 0A0E56` **instead of** (not in
  addition to) the ingredient tag (Wheat, Snowberry, Salt).
- Contraband: `REQ_VendorItem_BlackMarket` only (precedent: MoonSugar).
- Gore/forbidden (HumanHeart, HumanFlesh): **no keyword** — not vendored.
- Additive flavor where apt: `REQ_ArgonianTreat AE3727:Requiem.esp` (fish/meat treats),
  `GiftChildSpecial 0A0E52`, `GiftFlower 0A0E54`.

## Requiem's own defined ingredients (the `REQ_Ingredient_*` precedent set)

Ten records defined in Requiem.esp, alchemy-authored by AR — the naming + payload model for a
net-new ingredient: HorkerFat `034E09:Requiem.esp` V25 W0.5 · WolfHeart `04365E` V25 W0.75 ·
BearHeart `04365F` V40 W1.75 · MammothHeart `043661` V250 W5 · SabrecatHeart `043665` V35 W1 ·
StrangeRemains `07524B` V850 W0.75 · RedGemDust `3AB3BC` V50 W0.3 · BlueDust `3AB3C5` V100 W0.3 ·
FrostBiteSpiderSalvia `44C4A8` V50 W0.3 · and the deliberately decoupled trophy fish
`0972E6:Requiem.esp` (V500 / IngrV15 / W30).

## Anomalies (flag, don't smooth — and don't propagate)

- **A convention-broken forwarder set:** 14 CC-Curios ingredients are won by a downstream
  forwarder that turned auto-calc back ON (`Flags=0`) and left `IngredientValue ≠ Value`
  placeholders. The live winner is still what the game plays, but it is a **broken baseline** —
  derive a new ingredient's payload from a convention-conforming peer instead, and flag the set.
- **Downstream systems legitimately inject non-`REQ_Alch_` effects** into a slot (a disease-system
  patch replaced Blue Mountain Flower's `Effects[3]` with its own cure-infection MGEF). Read the
  winner; don't assume all four slots are pool members on a patched load order.
- 22/183 live winners are downstream patches, not the addon — normal; the winner is the authority.
