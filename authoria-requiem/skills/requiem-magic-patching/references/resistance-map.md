# Resistance Map — REQ_NULL'd vanilla resist MGEFs → the live Requiem analogues

Requiem **retires all 8 named vanilla resist MGEFs** (overridden in `Requiem.esp`, renamed
`REQ_NULL_*`, `MagicSkill=None`, gutted). A modded resistance spell, enchantment, or potion whose
effects point at them is a **silent no-op in game** — it casts, shows a magnitude, and protects
against nothing. This is the single most common way modded defensive magic ships broken, and the
fix is a re-point, not a redesign: swap each dead MGEF for the live analogue **in the same lane**.
All rows verified live 2026-07-17.

## The dead set (never a target, never a comparable)

| Element | NULLed vanilla MGEF |
|---|---|
| Fire | `024314:Skyrim.esm` REQ_NULL_AbResistFire |
| Frost | `024315:Skyrim.esm` REQ_NULL_AbResistFrost |
| Shock | `024318:Skyrim.esm` REQ_NULL_AbResistShock |
| Poison | `0AA024:Skyrim.esm` REQ_NULL_AbResistPoison |
| Disease | `0E40D3:Skyrim.esm` REQ_NULL_AbResistDisease |
| Magic | `053124:Skyrim.esm` REQ_NULL_AbResistMagic |
| Physical | `0CAB90:Skyrim.esm` REQ_NULL_AbResistDamage |
| Waterbreathing | `0AA01C:Skyrim.esm` REQ_NULL_AbWaterbreathing |

Also dead and easy to miss: the robe-enchant set `REQ_NULL_EnchRobesResist{Fire,Frost,Magic,Shock}
ConstantSelf` (`109635–109638:Skyrim.esm`), `REQ_NULL_EnchFortifyDmgResistVampire 0F81E2`,
`REQ_NULL_DLC2ResistMagicFFSelf 03BD01:Dragonborn.esm`, and the **no-NULL-prefix**
`REQ_DEPRECATED_Food_Resist*` batch (`AD38F9–AD3901:Requiem.esp`).

## The replacement map — pick the LANE first

The lane is what the carrier record is: an **ability/trait SPEL** (racial passive, boon, creature
trait), an **ENCH**, an **ALCH potion**, or a **castable spell**. The same element has a different
live MGEF per lane — re-pointing a potion at an ability MGEF is still wrong.

| Element | ABILITY lane (`REQ_AbHide_Fortify*`, all `:Requiem.esp`) | ENCHANT lane (`REQ_Ench_Resist*`, `:Skyrim.esm`) | POTION lane (`REQ_Alch_Resist*`, `:Skyrim.esm`, won by AR) |
|---|---|---|---|
| Fire | `0008CD` (+ Drain `000A15`) | `048C8B` | `03EAEA` |
| Frost | `0008CF` (+ Drain `000A17`) | `048F45` | `03EAEB` |
| Shock | `0008CE` (+ Drain `000A16`) | `049295` | `03EAEC` |
| Poison | `0008CC` (+ Drain `000A14`) | `0FF15E` | `090041` |
| Disease | `0008D1` (+ Drain `000A19`) | `100E60` | *(none — food only: `REQ_Food_ResistDisease AE371D:Requiem.esp`)* |
| Magic | `0008D0` (+ Drain `000A18`) | `0B7A35` | `039E51` |
| Physical | `REQ_Trait_NaturalArmor_AmorResistance_{Blunt,Slash,Pierce,Ranged}{1–5}` (`AD39C6–AD3A4D:Requiem.esp`, 20 records) | *(none — physical mitigation is armor rating)* | `AD3A6D:Requiem.esp` (Helgen potion) |
| Waterbreathing | — | — | use the live Alteration effect via `REQ_Alteration2_Waterbreathing_Self 05D175:Skyrim.esm` |

Lane notes:

- **Ability lane:** the functional effect is the `REQ_AbHide_Fortify<X>Resistance` (hidden) —
  the `REQ_AbShow_Resist*` MGEFs (`000828–00082D:Requiem.esp`) are **display-only UI shells**;
  re-pointing to an `AbShow` gives a tooltip and no protection. Racial/boon abilities pair
  AbHide + AbShow the way the comparable does.
- **Castable resist SPELLS exist and are the comparable for a modded resist spell:**
  Restoration `REQ_Restoration2/3_ResistPoison_Self/Target` (effects `032820/032823/005CF6/005CF7`)
  and `REQ_Restoration2/3_ResistMagic_Self/Target` (effects `006200–006203`); the elemental lane is
  Alteration's `FireShield`/`FrostShield`/`ShockShield` spells and the Destruction cloak resist
  riders (`REQ_Effect_DestructionGM_<Element>_Cloak_Resistance`). There is **no castable
  disease-resist spell** — a modded one has no comparable; classify to Restoration, derive
  structure from ResistPoison, and flag the novelty.
- **Potion lane magnitudes:** `REQ_Potion_Resist<Element>1–4` = m20/30/40/50, dur 60 — the ladder
  a modded resist potion joins (`requiem-consumable-patching` owns the ALCH shell).
- Weakness (resistance-*lowering*) spells are **Alteration `Weakness<Element>`** MGEFs
  (`REQ_Alteration2/3_Weakness*_Target`) — the vanilla `AbWeakness*Constant` set is NULLed too.

## The scan that catches all of this

Resolve every effect of every modded SPEL/ENCH/ALCH you touch with `resolve_names=true` and treat
any `REQ_NULL_*` / `REQ_DEPRECATED_*` BaseEffect as a defect to fix by this map — same-lane,
same-element. One sweep shape per plugin:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="Spell" fields=["Effects"] resolve_names=true
```

(and the same for `ObjectEffect` and `Ingestible`). Every hit is a re-point; do not carry, and do
not delete the protection outright — on actors the race/trait replacement rule applies
(`requiem-race-patching`).
