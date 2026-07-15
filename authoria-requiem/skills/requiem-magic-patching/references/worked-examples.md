# Worked Examples — real new-magic-patched-for-Requiem cases

These are live `conflict_tree` deltas from the **MR-patch addons** — the load order's own examples of a
new spell pack rebalanced for Requiem/Magic Redone. They are the patching recipe in practice. Read each
as: *mod-original → Requiem-patched*, and note that the same three moves recur every time —
**reclassify school+tier via `HalfCostPerk`, normalize `ChargeTime` to the tier, normalize `BaseCost`,
and set the tome value to the tier ladder.**

## Example 1 — Supernova (Constellation Magic), a master nuke

Plugin `Requiem - Constellation Magic - Magic Redone Patch.esp` patches the Supernova star-magic pack
(13 MGEF, 5 SPEL, 3 SCRL, 4 spell-tome BOOK, explosion effects).

`_SS_Supernova 000812:Supernova.esl` (a self-centered AoE nuke):

| Field | Mod original | Requiem-patched | Why |
|---|---|---|---|
| `BaseCost` | 4900 | **4000** | normalized down to the master-tier band |
| `ChargeTime` | 0 | **6** | a master AoE nuke gets a long anti-spam charge |
| `HalfCostPerk` | `0C44C2` (Master **Destruction**) | **`0C44CA`** (Master **Restoration**) | reclassified the school (star/light → Restoration) |
| `Flags` | — | `ManualCostCalc, AreaEffectIgnoresLOS, IgnoreResistance, NoAbsorbOrReflect, NoDualCastModification` | explicit cost + AoE flags |

Tome `_SS_SpellTomeSupernova 00081F`: Value 4975 → **2000** (Master tier), Weight 1, standard
`InventoryArt 02FBB7`. **Lesson:** the patch decides the spell's Requiem school + tier and re-points
`HalfCostPerk`; cost and charge follow the tier; the tome is priced to tier, not to the mod's price.

## Example 2 — Resona (Sonic Mage), an apprentice concentration buff

Plugin `Requiem - Sonic Mage - Magic Redone Patch.esp` (7 spells + tomes). `_Sn_Spell_Resona
000820:Shockwave.esl`:

| Field | Mod original | Requiem-patched |
|---|---|---|
| `BaseCost` | 42 | **40** |
| `ChargeTime` | 0 | **0.5** (apprentice standard) |
| `HalfCostPerk` | `0C44BF` (Apprentice **Destruction**) | **`0C44B7`** (Apprentice **Alteration**) |
| `CastType`/`TargetType` | Concentration / Self | unchanged |

Tome `_Sn_SpellTomeResona 000833`: Value **400** (Apprentice). **Lesson:** sonic magic was reclassified
**Destruction → Alteration**; the charge time was set to the apprentice standard (0.5); the cost barely
moved. The classifier (`HalfCostPerk`) is doing the real work.

## Example 3 — Reverbra (Sonic Mage), an adept aimed spell

`_Sn_Spell_Reverbra 000800:Shockwave.esl`:

| Field | Mod original | Requiem-patched |
|---|---|---|
| `BaseCost` | 120 | **200** | (raised to the adept band) |
| `ChargeTime` | 0.5 | **0.75** (adept standard) |
| `HalfCostPerk` | `0C44C0` (Adept **Destruction**) | **`0C44B8`** (Adept **Alteration**) |
| `Flags` | — | `ManualCostCalc, NoAbsorbOrReflect` |

Tome `_Sn_SpellTomeReverbra 000835`: Value **600** (Adept). **Lesson:** cost can move **up** to hit the
tier band (120 → 200); charge normalized to the adept 0.75; same Destruction → Alteration reclassify.
Cost is not "always lower" — it is normalized to the school/tier/delivery comparable, either direction.

## The cross-addon tome-value ladder (every MR-patch addon agrees)

Every addon normalizes its tomes to the same tier ladder, regardless of the mod's original prices:

| Tier | Tome Value | Examples (live) |
|---|---|---|
| Novice | 100 | Amplifier/Muffler (Sonic), Runemend (Abyssal), Consecrate Dead (Holy Templar) |
| Apprentice | ~300–400 | Resona (Sonic), Sun Shards (Holy Templar), Floral Blockade (Wildwaker) |
| Adept | 600 | Reverbra (Sonic), Sunlight Targe (Holy Templar), Nocturne Pollen (Wildwaker) |
| Expert | 800 (≤1000) | Novasphere (Sonic), Celestial Rays/Stardust (Constellation), Arcane Cross (Dark Hierophant) |
| Master | 2000 | Mega Reverbra (Sonic), Supernova (Constellation), Arcane Light (Dark Hierophant), Reaper (Wildwaker) |

## Round-trip — reproduce Ice Spike (T2 frost) from the Firebolt comparable

A same-tier cross-element reproduction, built from scratch and diffed against the live winner. **This is a
*validation* exercise — proof the recipe reproduces a known Requiem spell — not license to patch a real
element triple by reading one member and swapping the element on the rest. When you patch a family for
real, read each member's own record (the skill body's *Bulk pass protocol*).** Take the tier-2 fire bolt `REQ_Destruction2_Fire_Aimed` (Firebolt)
as the comparable and reproduce the tier-2 frost bolt `REQ_Destruction2_Frost_Aimed` (Ice Spike),
changing only the element-specific pieces:

- **Comparable (Firebolt `012FD0`):** BaseCost 90, FaF/Aimed, ChargeTime 0.5, `HalfCostPerk` Apprentice
  Destruction `0C44BF`, `Flags = ManualCostCalc`; effects = primary fire MGEF `012F03` mag 30 +
  `005AA9:MR` secondary mag 3 + `0153D3` impact mag 0.25.
- **Predicted Ice Spike:** identical cost 90 / charge 0.5 / tier-perk `0C44BF` / 3-effect shape; swap the
  primary to the frost MGEF, swap the element keyword `MagicDamageFire 01CEAD` → `MagicDamageFrost
  01CEAE` and `ResistValue` ResistFire → ResistFrost.
- **Live winner (Ice Spike `02B96C`):** BaseCost 90, FaF/Aimed, ChargeTime 0.5, `HalfCostPerk 0C44BF`,
  `Flags ManualCostCalc` — **exact**. Effects: primary frost MGEF `01CEA2` mag 30 + `0B729F` (slow) mag 5
  dur 2 + `005AA4:MR` frost taper dur 1 — same structure as Firebolt, element-specific MGEFs swapped.
- **Live write check:** the reproduction was authored from scratch via `create_record` into
  `Requiem magic patching.esp` (Spell `000800`), with the three effects composed and `HalfCostPerk
  0C44BF`. The write masters resolved to `Skyrim.esm, Update.esm, Requiem - Magic Redone.esp` (MR pulled
  in automatically by the `005AA4:MR` effect reference) and no `REQ_NULL_*` appeared — confirming the
  recipe, the `Effects` compose shape, and the master derivation all work end to end.
