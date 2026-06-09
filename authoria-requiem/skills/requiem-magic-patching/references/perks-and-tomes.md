# Perks & Spell Tomes

The perk system is the **school+tier spine** of Requiem magic, and the spell tome is how the player
acquires a spell. A patch hooks a new spell into Requiem's existing perk trees and prices its tome to
tier — it does **not** author new perks. All FormIDs verified live (2026-06-09).

## `HalfCostPerk` = the school+tier classifier (the most important field)

A spell's `HalfCostPerk` names a `REQ_<School>_Mastery_<NNN>` perk. That single field:

1. **Classifies the school** — which of the five magic skills the spell belongs to.
2. **Classifies the tier** — Novice/Apprentice/Adept/Expert/Master.
3. **Gates learning** — Requiem's perk-gated learn system checks the Mastery perk before the player can
   learn the spell from its tome.
4. **Grants half-cost at mastery** — the vanilla half-cost-perk role.

Set it correctly and Requiem treats the spell right; set it wrong and the spell is mis-classified
(wrong school tree, wrong learn gate, wrong cost reduction). The full matrix (`<NNN>` =
`000`/`025`/`050`/`075`/`100`; all FormIDs `:Skyrim.esm`, winners MR / `Requiem - MR Special Feats Patch.esp`):

| School | Novice `000` | Apprentice `025` | Adept `050` | Expert `075` | Master `100` |
|---|---|---|---|---|---|
| Destruction | `0F2CA8` | `0C44BF` | `0C44C0` | `0C44C1` | `0C44C2` |
| Restoration | `0F2CAA` | `0C44C7` | `0C44C8` | `0C44C9` | `0C44CA` |
| Conjuration | `0F2CA7` | `0C44BB` | `0C44BC` | `0C44BD` | `0C44BE` |
| Alteration  | `0F2CA6` | `0C44B7` | `0C44B8` | `0C44B9` | `0C44BA` |
| Illusion    | `0F2CA9` | `0C44C3` | `0C44C4` | `0C44C5` | `0C44C6` |

EditorIDs follow `REQ_<School>_Mastery_<NNN>_<Tier><School>` (e.g.
`REQ_Destruction_Mastery_050_AdeptDestruction 0C44C0`).

## Specialization, Empower & Grandmaster perks (hook in, don't author)

Beyond Mastery, Requiem has a rich per-school tree (186 magic perks live). You generally **do not touch
these** — a new spell rides Requiem's existing perks by carrying the right keywords. The families:

- **Destruction:** Pyromancy / Cryomancy / Electromancy (e.g. `REQ_Destruction_Pyromancy_025_Pyromancy1
  0581E7`, `..._050_Cremation 0F392E`, `..._100_FireMastery 179121`); Battlemage (RuneMastery `105F32`);
  Empower (`REQ_Destruction_Empower_025_EmpoweredDestruction 0153CF`, `..._050_Impact 0153D2`).
- **Conjuration:** Necromancy / Daedric / Bound / Spirit (e.g. `REQ_Conjuration_Bound_025_MysticBinding
  0640B3`, `REQ_Conjuration_Daedric_025_DaedricBinding 105F30`).
- **Alteration:** MagicResistance / MageArmor / Metamagic / MagicalAbsorption (e.g.
  `REQ_Alteration_MageArmor_025_ImprovedMageArmor 0D7999`).
- **Illusion:** Perceptual / Emotional / Delusive / ShadowMagic / Overmind (e.g.
  `REQ_Illusion_Emotional_025_EmotionalIllusions 059B77`).
- **Restoration:** Healing / Recovery / Turning / Protection / PainfulRegrets (e.g.
  `REQ_Restoration_Healing_025_ImprovedHealing 0581F8`).
- **Enchanting:** Artificer / Enchantment (e.g. `REQ_Enchanting_Enchantment_100_EnchantmentMastery 058F7F`).

**Specialization perks gate secondary MGEF.** The Flames spell's Cremation effect (`005AA8:MR`) fires
only when the caster has `REQ_Destruction_Pyromancy_050_Cremation 0F392E` — the gate is the effect's
`PerkToApply` / a `HasPerk` condition on the effect, not a flag on the spell. So when you copy a
multi-effect spell, you copy that perk-gated effect verbatim; you don't recreate the perk.

**Rule: hook into existing perks; don't author new perk trees.** A new spell becomes part of a school
by its `HalfCostPerk` (school+tier) + its element/school keyword. If the mod ships *its own* perk tree,
rebalance those perks to Requiem's perk-magnitude conventions (read a comparable Requiem perk); don't
leave a vanilla-magnitude perk in place. If it ships no perks, leave perks to Requiem.

## Spell tomes (BOOK) — value tracks tier

A spell tome is a `Book` with `Type = BookOrTome` and `Teaches` = a `BookSpell` arm pointing at the
SPEL. Patch shape:

- `Teaches` → the (patched) spell.
- `Weight = 1`.
- `InventoryArt` → a standard tome static (copy the comparable's; e.g. `02FBB7:Skyrim.esm`).
- `Keywords` → `VendorItemSpellTome` (+ any school/tier tag the comparable carries).
- **`Value` = the Requiem tier ladder** (this is the field the MR-patch addons normalize):

| Tier (editorid `…00/25/50/75/100`) | Tome Value |
|---|---|
| Novice (`00`) | **100** |
| Apprentice (`25`) | ~**300–400** |
| Adept (`50`) | **600** |
| Expert (`75`) | **800** (occasionally up to 1000) |
| Master (`100`) | **2000** |

Verified identical across **every** MR-patch addon (Constellation, Sonic Mage, Obscure, Dark Hierophant,
Holy Templar, Wildwaker): e.g. Sonic Mage Amplifier/Muffler 100, Resona 400, Reverbra 600, Novasphere
800, Mega Reverbra 2000; the Supernova tome `00081F` was set from the mod's 4975 → **2000** (Master).
**Don't carry the mod's original price** — set value to the tier.

A tome has **no learn-gate field** — learning is gated globally by the spell's `HalfCostPerk` tier, so a
correctly-classified spell automatically gates its tome. **Placement** of the tome into a vendor or loot
leveled list is `requiem-leveled-list-patching` (and the "leveled-list-tied-to-perks" concern resolves
to exactly this: the LVLI placement is the `requiem-leveled-list-patching` skill; the perk tie is the learn-gate that
follows from the spell's tier perk — nothing is stamped on the list). A tome that teaches **multiple**
spells uses the `Nox_TomeMultipleSpells` script → `requiem-script-patching`.

## Scrolls (SCRL)

A scroll is a one-shot cast of a spell at its tier: `REQ_Scroll_<School><Tier>_<Type>_<Delivery>`,
`Weight 0.5`, a modest tier-scaled `Value` (Candlelight 25, Firebolt 45, Flame Atronach 250, ritual
500–750), `ChargeTime` mirroring the spell. It carries the same MGEF as the spell. Crafting scrolls uses
`Nox_Enchanting_ScrollCrafting*` (→ `requiem-script-patching` skill); placement → `requiem-leveled-list-patching` skill.
