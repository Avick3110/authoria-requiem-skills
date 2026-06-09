# Nox runtime map — Magic Redone's special-mechanic scripts

Magic Redone (MR) ships ~45 `Nox_*` scripts that implement the special spell mechanics the engine
can't express. Source: `…\Requiem - Magic Redone - rerun\scripts\source\Nox_*.psc` (compiled `.pex`
alongside). This map tells you, per mechanic: **what it attaches to, the properties it needs, and how
a new spell is picked up.** Use it to decide what to clone when patching a new special spell.

**The base damage/heal/buff layer is NOT here.** Magnitude is never script-driven — it scales via the
MGEF `PowerAffectsMagnitude` flag. Many Requiem effects carry no `VirtualMachineAdapter`; some that
reuse a vanilla MGEF FormID carry an incidental vanilla FX/quest script (e.g.
`REQ_Effect_Destruction2_Fire_Aimed` → `MG01FireEffectScript`) that you neither add nor remove. Only
the special mechanics below need a script of yours.

Almost every Nox script extends `ActiveMagicEffect` and fires `OnEffectStart(akTarget, akCaster)`, so
it attaches to the **MGEF** record (one `ScriptEntry`, `Flags=Local`, plus its object properties).
The exceptions are called out. To reuse one: clone the comparable Requiem effect's VMAD onto your
MGEF and repoint the properties to your forms.

## Conjuration (7)

| Script | Mechanic | Attaches to | Key properties (repoint these) |
|---|---|---|---|
| `Nox_BoundWeapon` | Summon a bound weapon (pick by tier/menu) | bound-weapon summon MGEF | `BoundAbility` (Spell[] of the bound abilities), `EffectIndex` (GlobalVariable), `MessageBox` (Message), `MysticInfusion` (Perk gate). Gated on player + MysticInfusion. |
| `Nox_BoundArmor` | Summon bound armor | MGEF | bound-armor ability spell(s) |
| `Nox_Conjuration_BoundThrowingKnives` | Bound throwing knives | MGEF | `BoundThrowingKnife`/`SourceScroll` (Scroll), `SourceSpell` (Spell), **`SourceStaff` (Weapon[])** — the array of staves that cast it, checked to pick the equip slot. This is the only "staff array" consumer; it is NOT a keyword reader. |
| `Nox_Conjuration_Teleport` | Mark / Recall / swap by look angle | MGEF | `Mark` (ObjectReference), `Park` (ObjectReference), `XarrianCell` (Cell), `SummonFX` (Activator) |
| `Nox_Conjuration_GreaterTeleport` / `Nox_Conjuration_Blink` | Long / short blink | MGEF | teleport markers / FX |
| `Nox_Conjuration_SiphonCorpse` | Disintegrate corpse → magicka + ash pile | MGEF | `AshPileObject` (Activator), `MagicEffectShader`, `ExplosionSpell` (Spell), `ImmunityList` (FormList), `MagickaAmount` (Float), delay/alpha floats+bools |
| `Nox_Conjuration_DarkSacrifice` / `Nox_Conjuration_MysticInfusion` | Sacrifice / infuse | MGEF | spell/perk properties |

## Illusion (17)

`Nox_Illusion_Command`, `_Control`, `_Frenzy`, `_Sleep`, `_Silence`, `_Vanish`, `_Death`, `_Break`,
`_Hallucination`, `_ShadowClone`, `_ShadowDoor`, `_ShadowStep`, `_VeilOfShadows`, `_SetAlpha`,
`_AbsorbCheck`, `_RecastDispel`, `_Command_Examine` — plus `Nox_Charm`.

- **Mind control** (`Command`/`Control`/`Charm`): attach to MGEF; key property is `CharmFaction`
  (Faction the target temporarily joins). `OnEffectStart` adds the faction + sets teammate + pauses
  combat; `OnEffectFinish` removes it + restores morality. A new charm/command spell clones this and
  points `CharmFaction` at the right faction (reuse Requiem's).
- **Crowd control** (`Frenzy`/`Sleep`/`Silence`/`Vanish`): mostly property-light. `Vanish` simply
  calls `akTarget.StopCombat()` (no properties). `Sleep`/`Silence` apply states.
- **Shadow magic** (`ShadowClone`/`ShadowDoor`/`ShadowStep`/`VeilOfShadows`/`SetAlpha`): teleport
  + alpha/invisibility helpers; SetAlpha controls render alpha.

## Alteration (4)

| Script | Mechanic | Properties |
|---|---|---|
| `Nox_Alteration_ControlWeather` | Pick weather from a menu | `WeatherList` (Weather[]), `ExceptionList` (Weather[]), `MessageBox` (Message), `StormSpell` (Spell). Exterior-only. |
| `Nox_Alteration_TelekineticDisarm` | Disarm via telekinesis | target/weapon handling |
| `Nox_Alteration_Open` | Open locks | lock handling |
| `Nox_Alteration_Jump` | Jump/levitate assist | movement |
| `Nox_EnlargeShrink` | Enlarge/shrink target | scale |

## Restoration / Dispel (5)

`Nox_Restoration_Purify`, `Nox_Restoration_DivineIntervention`, `Nox_Dispel`, `Nox_Illusion_RecastDispel`,
`Nox_Enthrall_Dispel`. Purify/Dispel strip effects; DivineIntervention teleports to a temple.

## Destruction (2)

| Script | Mechanic | Properties |
|---|---|---|
| `Nox_Destruction_RuneMastery` | Raise max simultaneous runes | `RuneMastery` (Perk), `Trapper` (Perk). Listens to StatsMenu/load/race-switch; sets the `iMaxPlayerRunes` game setting from base 1 + perks. |
| `Nox_Destruction_Weakness` | Stacking elemental weakness | weakness tracking |

## Enchanting / tomes / scrolls (4 + 1)

| Script | Mechanic | Attaches to | Properties |
|---|---|---|---|
| `Nox_TomeMultipleSpells` | One tome teaches several spells | **BOOK** (extends `ObjectReference`, `OnEquipped`) | `SpellsToLearn` (Spell[]). Adds each spell the actor doesn't have. A multi-spell tome clones this and fills the array. |
| `Nox_Enchanting_MultipleEnchantments` | Learn double/triple enchant | MGEF | `DoubleEnchantment`/`TripleEnchantment` (Spell), `EnchType` (Int) |
| `Nox_Enchanting_ArtifactEnchanter` | Artifact enchanting furniture | activator/furniture | enchant spell list |
| `Nox_Enchanting_ScrollCraftingFurniture` / `Nox_Enchanting_ScrollCraftingTool` | Scroll crafting | furniture/tool | scroll recipe handling |

## Enthrall / Charm

`Nox_Enthrall` (+ `Nox_Enthrall_Dispel`) — permanent thrall; faction-based like Command.

## Live worked example (clone target)

`REQ_Effect_Conjuration4_Teleport_RitualSelf 02CBCE:Requiem.esp` (winner = Magic Redone):

```
VirtualMachineAdapter
  Scripts[0] = Nox_Conjuration_Teleport   (Flags=Local; Version 5; ObjectFormat 2)
  Scripts[0].Properties (4):
    Mark        = 02CBC8:Requiem.esp   (ObjectReference)
    Park        = 02CBCF:Requiem.esp   (ObjectReference)
    XarrianCell = 02900B:Requiem.esp   (Cell)
    SummonFX    = 07CD55:Skyrim.esm    (Activator)
```

To make a new teleport spell, reproduce this VMAD on your MGEF and repoint `Mark`/`Park`/`XarrianCell`
to your own markers (or reuse Requiem's if the destination is the same). The `bulk_apply` shape is in
`housecarl-recipes.md`.

## What is NOT scripted (so don't go looking)

- **Staff power/charge scaling** — record-side only (`EnchantmentAmount` + the `ObjectEffect`'s
  magnitude + the `Nox_KW_Staff_<School><Tier>` keyword as a *classifier*). No script reads the staff
  keyword. A plain staff needs no Papyrus.
- **Base damage / heal / ward / buff magnitude** — engine `PowerAffectsMagnitude` flag.
- **Resistance / armor-penetration / poison / absorb rescaling** — Reqtificator-assigned `RFTI_All_*`
  perks (see `requiem-magic-patching`), not scripts.
