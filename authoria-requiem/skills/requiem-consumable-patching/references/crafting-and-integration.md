# Crafting (COBJ) & Integration — how consumables actually get patched

Mined live (2026-07-15). Worked examples are verbatim live winners; FormIDs are wire tokens.

## The Reqtificator does not touch consumables (evidence)

`Requiem for the Indifferent.esp` defines exactly two record types — Npc (48,295) and LeveledNpc
(2,649) — and touches **zero** ALCH, INGR, or COBJ, neither as definer nor as override
(`cross_plugin_query type=ALCH plugins=["Requiem for the Indifferent.esp"]` → 0 matches; same
for INGR). Its absence from every consumable conflict chain is structural, not a broken build.
**Consequence:** a consumable override is the final balanced record. There is no
carry-inputs-and-let-the-build-pass-finish discipline here, and no safety net behind a wrong
number.

## The modded-consumable patch pattern (the live-analogy precedent)

Measured order-wide: 687 contested ALCH across 49 winning plugins; the winners split into the
Requiem addons, systemic layers, and **per-mod content patches** (the consumable equivalent of
the weapons-domain per-mod patch layer, same naming family). Layer order in a typical chain,
bottom to top: base mod → eating-animation patch → the addon's thin per-mod satellite → the
per-mod content patch (winner). Three kits, split by class:

### Potion kit — keep the links, rebalance the shell

`0E46A5:BPUFXelzazFollower.esp` "Potion of Imperception": the patch changed **only**
`Value 250 → 70` (Flags `NoAutoCalc, Medicine`). The effect list was left alone because its
`[0]` BaseEffect is `03EB3D:Skyrim.esm` — the vanilla Invisibility MGEF FormID, which Requiem
overrides in place as `REQ_Alch_Invisibillity`; the link already resolves to Requiem behaviour.
Siblings: CureDisease potion — Flags `Medicine → NoAutoCalc,Medicine`, `Value 79 → 50`, effect
kept with **magnitude 100 → 0** (the REQ MGEF is fixed-behaviour, so the per-item magnitude is
zeroed); NightEye — `Value 0 → 60`, effect kept at mag 0.

### Food kit — rebuild the list

`0013DB:KvSweets.esp` "Chocolate Cake", base 2 vanilla effects → winner **7**:
`REQ_Apo_FoodFortifyHealth 000A12:FaB` (25/900) · `…FortifyHealthRegen 00080D` (15/900) ·
`…FortifyStamina 000A13` (25) · `…FortifyStaminaRegen 000001` (15) ·
`Survival_FoodRestoreHungerLarge 002EE4:Update.esm` · `REQ_Apo_FoodNutritionSM 000A36` (10) ·
the Taberu eat-animation marker (mag 0, **preserved**). `Value 25 → 30`; keywords
`VendorItemFood` + `GiftChildSpecial` kept.

### Alcohol kit

`FA82FA:BPUFXelzazFollower.esp` "Xelzaz's Southfringe Mead", base 1 → winner 5:
`REQ_Alcohol_FortifyHealth 18ED7C:Requiem.esp` (45) · `REQ_Alcohol_DamageRegeneration
AE3729:Requiem.esp` (15) · `Survival_FoodRestoreHungerSmall 002EE2:Update.esm` ·
`REQ_Apo_AlcoholNutrition 000A37:FaB` (10) · animation marker. Magnitudes = the Moderate tier.

### The satellite model (a thin compat patch for the addons themselves)

The addon's own per-mod satellites are single-record-thin: retarget effects → the REQ kit, add
the addon's food/alcohol keyword, rebalance value/weight. One EditorID-scheme collision was
measured (a satellite renamed a drink `REQ_Alcohol_Moderate_*` while the final winner calls it
`REQ_Drink_*`) — two conventions coexist; don't assume one canonical scheme.

## The COBJ crafting layer

**Requiem's own craft-consumables** (25 COBJ defined by Alchemy Redone — weapon-coating oils,
powders, fortify-enchanting potions):

- Station: **`WorkbenchKeyword = 0A5CB3:Skyrim.esm` (CraftingCookpot) on every one** — not the
  alchemy lab.
- Gate: `HasPerk REQ_Alchemy_AlchemicalLore1 0BE127:Skyrim.esm` / `Lore2 0C07CA:Skyrim.esm`
  (the tier picks the perk) — on potion-class recipes **OR**-flagged with `HasKeyword
  REQ_RacialSkills_CreatePotionsUnperked AD3A3B:Requiem.esp` — plus an Alchemy skill band
  (`GetActorValue`/`GetBaseActorValue Alchemy >= a`, `< b`). The OR-with-unperked pattern is
  **not universal**: the sampled oil recipe (`REQ_Smelting_Oil_Fire2 000830:AR`, re-verified
  2026-07-17) gates on the perk + skill band alone, no racial OR. Read the comparable recipe's
  own conditions; don't assume the OR.
- Tiering raises **`CreatedObjectCount`** (yield), not potency: e.g. `REQ_Smelting_Oil_Fire2`
  `000830:Requiem - Alchemy Redone.esp` = FireSalts `03AD5E` ×1 + DwarvenOil `0F11C0` ×3 →
  `REQ_Oil_Fire 000807:…AR.esp` ×9 (tier 1 yields ×6), gated Lore2 + Alchemy 50–100.

**Plain cooked-food recipes** are a separate, conventionally **ungated** lane (empty Conditions;
raw meat ×1 → cooked dish ×1 at the cookpot). The two lanes have different gating philosophies —
match the lane you're extending and say which convention you followed.

**Craftability is the exception.** FaB defines zero cooking recipes; most food is loot/vendor
only, and even garlic bread has no recipe. Add a COBJ only when the comparable class has one or
the user asks.

## LVLI boundary (characterize; don't edit here)

Alchemy Redone defines 6 potion/poison pools of its own and injects into ~100 vanilla/Requiem
loot lists; FaB edits 20 vanilla food lists and defines none. Distributing a new consumable =
`requiem-leveled-list-patching`'s job; this skill records the placement intent (which pool/tier)
in its summary. Food stays out of alchemist vendor lanes (Requiem's closed economy: alchemists
don't buy food; innkeepers sell meat as stews).

## Systemic knobs (read-only context — never edit targets)

Requiem tunes the alchemy economy through: GMST `fAlchemySkillFactor 10C4D9:Skyrim.esm`
(vanilla 1.5 → Requiem 1.1 → Alchemy Redone **2** — skill→potency scaling), the `AVAlchemy
000456:Skyrim.esm` actor-value definition, and 8 Alchemy-tree perk overrides (incl. the
`AlchemicalLore` perks the COBJ gates reference). A new consumable inherits the economy through
these; never re-tune them to balance one item.
