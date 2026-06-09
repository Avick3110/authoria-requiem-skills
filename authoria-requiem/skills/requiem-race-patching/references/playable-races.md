# Playable Race Standard

The Requiem standard for the 10 playable races — starting stats, regeneration, skill bonuses, and
the racial ability spells. A modded **humanoid/playable** race is patched to become a near-copy of
the analogue it most represents, so this is the table you derive from. All values are the **live
Master-Patch winner** (`Authoria - Master Patch - Races Merge.esp`, which consolidates Races Redone +
Food & Beverages Redone) read 2026-06-08. **Read the live comparable before writing — this is a
cross-check, not gospel.**

## Table of contents
- [Live > Races.md — the doc drifts](#live--racesmd--the-doc-drifts)
- [Starting stats, regen, carry, unarmed (live)](#starting-stats-regen-carry-unarmed-live)
- [Skill bonuses](#skill-bonuses)
- [The ability-spell bundle (ActorEffect)](#the-ability-spell-bundle-actoreffect)
- [Race keywords (playable)](#race-keywords-playable)
- [Flags](#flags)
- [The playable-race perk caveat](#the-playable-race-perk-caveat)
- [Vampire variants](#vampire-variants)

## Live > Races.md — the doc drifts

`…\Requiem 6.0.2 …\documentation\Races.md` is the **base-Requiem** design intent. Races Redone (now
folded into the Master Patch) **rebalanced the attribute spreads**, so the doc and the live records
disagree — sometimes a lot:

| Race | Races.md (H/M/S) | Live winner (H/M/S) |
|---|---|---|
| Altmer | 90 / 140 / 80 | **90 / 120 / 90** |
| Khajiit | 110 / 70 / 140 | **90 / 90 / 120** |
| Orc | 140 / 70 / 110 | **120 / 80 / 100** |
| Redguard | 130 / 60 / 120 | **100 / 80 / 120** |
| Nord | 120 / 80 / 100 | **110 / 80 / 110** |

**Both the attributes *and* the skill bonuses drift** — Races Redone flattened the doc's +15/+5
spreads (e.g. doc Redguard `+15 OneHanded`; live `+10 OneHanded, +10 TwoHanded`). Take **everything
numeric from the live record**; use the doc only to understand intent (which resistances, which
heritage flavour).

## Starting stats, regen, carry, unarmed (live)

| Race | Health | Magicka | Stamina | Carry | Unarmed | Regen H / M / S |
|---|---|---|---|---|---|---|
| Nord | 110 | 80 | 110 | 110 | 9 | 0.21 / 0.36 / 0.84 |
| Imperial | 100 | 100 | 100 | 100 | 8 | 0.20 / 0.40 / 0.80 |
| Breton | 90 | 110 | 100 | 90 | 7 | 0.19 / 0.42 / 0.80 |
| Redguard | 100 | 80 | 120 | 100 | 8 | 0.20 / 0.36 / 0.88 |
| Altmer (High Elf) | 90 | 120 | 90 | 90 | 7 | 0.19 / 0.44 / 0.76 |
| Bosmer (Wood Elf) | 90 | 100 | 110 | 90 | 7 | 0.19 / 0.40 / 0.84 |
| Dunmer (Dark Elf) | 100 | 100 | 100 | 100 | 8 | 0.20 / 0.40 / 0.80 |
| Orc | 120 | 80 | 100 | 120 | 10 | 0.22 / 0.36 / 0.80 |
| Argonian | 100 | 100 | 100 | 100 | 10 | 0.22 / 0.40 / 0.80 |
| Khajiit | 90 | 90 | 120 | 90 | 10 | 0.19 / 0.38 / 0.88 |

`Starting` and `Regen` are `Dictionary<BasicStat,float>` (keys Health/Magicka/Stamina). Health regen
on playable races is small (~0.2) because they also carry `REQ_Trait_NoHealthRegeneration` — out-of-
combat regen is gated behind food/rest, not the passive value. `UnarmedReach` is humanoid-relevant
(copy the analogue, ~/0); `UnarmedDamage` tracks the race's brawl strength (beast races highest).

## Skill bonuses

Six boosts per race (the 7th `SkillBoost` slot is `Skill=255`/None). Each `SkillBoost<n>` is a
`.Skill` (ActorValue name) + `.Boost` (int). **Live Master-Patch winner values** (these are *not* the
`Races.md` numbers — Races Redone retuned them; read the comparable to confirm):

| Race | Skill bonuses (live) |
|---|---|
| Nord | OneHanded +10, TwoHanded +10, Smithing +10, Block +5, HeavyArmor +5, LightArmor +5 |
| Imperial | HeavyArmor +10, Speech +10, Restoration +10, OneHanded +5, Block +5, Destruction +5 |
| Breton | Conjuration +10, Illusion +10, Restoration +10, Alchemy +5, Speech +5, Enchanting +5 |
| Redguard | OneHanded +10, TwoHanded +10, LightArmor +10, Archery +5, Block +5, Smithing +5 |
| Altmer | Alteration +10, Illusion +10, Enchanting +10, Conjuration +5, Destruction +5, Restoration +5 |
| Bosmer | Archery +10, LightArmor +10, Alchemy +10, Pickpocket +5, Sneak +5, Alteration +5 |
| Dunmer | OneHanded +10, Conjuration +10, Destruction +10, LightArmor +5, Sneak +5, Illusion +5 |
| Orc | Block +10, Smithing +10, HeavyArmor +10, OneHanded +5, TwoHanded +5, Destruction +5 |
| Argonian | Sneak +10, Alchemy +10, Restoration +10, OneHanded +5, LightArmor +5, Lockpicking +5 |
| Khajiit | Pickpocket +10, Lockpicking +10, Sneak +10, OneHanded +5, LightArmor +5, Alchemy +5 |

Pattern (not a rule): three +10 skills + three +5 skills (~+45 total). Always read the analogue live.

## The ability-spell bundle (ActorEffect)

Racial abilities ride on `ActorEffect` spells — the engine grants them to every NPC of the race, so a
new race that carries the analogue's `ActorEffect` inherits its abilities for free. Families:

- **Universal (every race):** `REQ_Trait_NoHealthRegeneration 609AF0` (no passive HP regen — the
  Requiem hardcore staple) + `REQ_Trait_MassEffect 82CC14` (armor-weight encumbrance).
- **Heritage** — the named racial perks ("Nord Heritage", etc.), one per race:
  Altmer `AE3B1E`, Argonian `AE3B1F`, Bosmer `AE3B20`, Breton `AE3B21`, Dunmer `AE3B22`,
  Imperial `AE3B23`, Khajiit `AE3B24`, Nord `AE3B25`, Orc `AE3B26`, Redguard `AE3B27` (all `Requiem.esp`,
  won by Races Redone).
- **Blood** — innate resistances (some races carry two):
  Altmer `AE3B14` (ResistDisease) + `AE3B32` (WeaknessMagic); Argonian `AE3B15` (ResistDisease) +
  `AE3B33` (ResistPoison); Bosmer `AE3B16` (ResistDisease); Breton `AE3B17` (ResistMagic);
  Dunmer `AE3B18` (ResistFire); Nord `AE3B19` (ResistFrost) + `AE3B35` (ResistShock);
  Orc `AE3B1A` (ResistMagic); Redguard `AE3B1B` (ResistDisease) + `AE3B34` (ResistPoison).
  Imperial has **no** Blood spell.
- **Physique** — beast-race natural armor/claws: Argonian `AE3B1C`, Khajiit `AE3B1D`.
- **Cuisine** — diet bonus (from Food & Beverages Redone): Argonian `AE3B70`, Bosmer `AE3B71`,
  Khajiit `AE3B72`, Orc `AE3B73`.
- **Race-specific powers:** Khajiit `REQ_Power_NightEye 0AA01D`; Bosmer `REQ_Power_WorshipYffre AE3B67`
  (Green Pact) + a Races-Redone Bosmer trait `000805:Requiem - Races Redone.esp`. Read the comparable
  for any extras like these.

**The four Elf+beast races (Argonian, Khajiit, Orc, Bosmer) also carry a Food & Beverages diet trait
`000944:…Food and Beverages Redone.esp` / Cuisine spell — copy whatever the live comparable lists.**

## Race keywords (playable)

| Keyword | EditorID | FormID | On |
|---|---|---|---|
| ActorTypeNPC | ActorTypeNPC | 013794:Skyrim.esm | all |
| drops blood | REQ_DropsBloodKeyword | 586728:Requiem.esp | all (living) |
| beast race | IsBeastRace | 0D61D1:Skyrim.esm | Argonian, Khajiit |
| full knockdown immunity | ImmuneStrongUnrelentingForce | 0172AC:Skyrim.esm | Orc ("cannot be knocked down") |
| unperked Sneak | REQ_RacialSkills_SneakUnperked | AD3A3A:Requiem.esp | races whose "Unperked Skills" include it |
| unperked Create Potions | REQ_RacialSkills_CreatePotionsUnperked | AD3A3B:Requiem.esp | " |
| unperked Recharge | REQ_RacialSkills_RechargeWeaponsUnperked | AD3A3C:Requiem.esp | " |
| unperked Pickpocket | REQ_RacialSkills_PickpocketUnperked | AD3A3D:Requiem.esp | " |
| unperked Lockpick | REQ_RacialSkills_LockpickUnperked | AD3A3E:Requiem.esp | " |
| strong stomach (diet) | Apo_StrongStomach | 000B45:…Food and Beverages Redone.esp | Cuisine races |

The `REQ_RacialSkills_*Unperked` keywords are the **"Unperked Skills"** from Races.md (e.g. Argonian
"can sneak / create potions without perks" = `SneakUnperked` + `CreatePotionsUnperked`). Copy the set
the analogue carries.

## Flags

Playable races carry (live Nord): `Playable, FaceGenHead, Swims, Walks, NoCombatInWater,
UsesHeadTrackAnims, AllowPcDialog, AllowPickpocket, CanPickupItems, CanDualWield, UseAdvancedAvoidance`.
**Playable races do not set `RegenHpInCombat`** (they carry `NoHealthRegeneration` instead). Copy the
analogue's flag set.

## The playable-race perk caveat

Requiem also defines a hidden perk `RFTI_All_PlayableRace_<Race>` (e.g. Nord `3E43DC`) with
entry-point bonuses (skill-use rates, etc.). Its effects are **per-effect `GetIsRace`-gated**, so a
new race's FormID won't match — those bonuses won't transfer to a modded race. The race's *abilities*
come from its `ActorEffect` spells, which do transfer. So mirroring the analogue's `ActorEffect` is
enough for a faithful copy; only if the modded race must keep its own FormID *and* needs the perk
bonuses do you edit the perk's effect conditions. State which you chose.

## Vampire variants

Each playable race has a `<Race>RaceVampire` record (NordRaceVampire `088794`, …) carrying a
`REQ_Trait_Vampire_<Race>` spell (`AD3A0B`–`AD3A14`). If the modded race has a vampire form, mirror
the analogue's vampire record too. Vampires also pick up the **state-trait** `vampire`
incomingDamageModifier perk (`AD3A44`) via the `vampire` keyword `0A82BB` — that part is
Reqtificator-assigned, not on the RACE.
