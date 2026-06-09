# Ammo Keyword Vocabulary + the Split

Every keyword FormID below was read live off the WAR ammo winners. Re-verify against the comparable
before writing — the load order can shift.

## The split: source-carried vs Reqtificator-assigned

This is the headline difference from weapons and armor. For weapons the Reqtificator assigns the
damage-type keyword; for armor it assigns the ranged-resistance tier and tempering perk. **For ammo
it assigns nothing.** The live Reqtificator config has **no ammo keyword-assignment rule** — the
only ammo line anywhere in `Data\*.conf` is `gameMechanics.perks.arrowRecovery = "AD3A35
Requiem.esp"`, a perk granted to all actors (not a keyword on the ammo).

Consequence: **every keyword on a Requiem arrow or bolt is source-carried, and you stamp all of
them.** There is no "let the patcher fill it in" step. Requiem even tags ammo with
`RFTI_Exclusions_NoDamageRescale` so the Reqtificator leaves the hand-tuned damage alone. Get the
keyword set complete on the record itself.

## Standard keyword set (arrow or bolt)

A normal tiered arrow/bolt carries (counts vary 4–7 by material):

| Keyword | FormID | On | Role |
|---|---|---|---|
| `WeapMaterial<X>` | see table below | all | material; drives value + silver/daedric interaction |
| `VendorItemArrow` | 0917E7:Skyrim.esm | all (arrows AND bolts) | merchant category |
| `REQ_AmmoWeight_<class>` | 9D1F49–4C | all | weight/draw class (bow-tier pairing) |
| `REQ_ArmorPiercingArrow_Tier<N>` | arrow series | arrows (not iron) | penetration tier |
| `REQ_ArmorPiercingBolt_Tier<N>` | bolt series | bolts (not iron) | penetration tier |
| `RFTI_Exclusions_NoDamageRescale` | AD3B2D:Requiem.esp | all | opt out of Reqtificator damage rescale |

Iron carries no AP keyword (tier 0). Silver and Daedric add a special weapon-type keyword (below).

## Material keywords (vanilla `WeapMaterial*`, reused on ammo)

| Material | FormID |
|---|---|
| Iron | 01E718:Skyrim.esm |
| Steel | 01E719:Skyrim.esm |
| Elven | 01E71B:Skyrim.esm |
| Dwarven | 01E71A:Skyrim.esm |
| Orcish | 01E71C:Skyrim.esm |
| Glass | 01E71D:Skyrim.esm |
| Ebony | 01E71E:Skyrim.esm |
| Daedric | 01E71F:Skyrim.esm |
| Silver | 10AA1A:Skyrim.esm (silver ammo carries Steel 01E719 *and* this) |
| Dragonbone | 019822:Dawnguard.esm (DLC1WeapMaterialDragonbone) |
| Stalhrim | 02622F:Dragonborn.esm (DLC2WeapMaterialStalhrim) |
| Quicksilver | 026230:Dragonborn.esm (DLC2WeapMaterialQuicksilver) |

## `REQ_AmmoWeight_*` — the bow-tier pairing

| Class | FormID | Materials |
|---|---|---|
| Light | 9D1F49:Requiem.esp | Elven |
| Medium | 9D1F4A:Requiem.esp | Iron, Steel, Silver, Quicksilver, Skyforge Steel, Glass |
| Heavy | 9D1F4B:Requiem.esp | Dwarven, Orcish, Stalhrim |
| Massive | 9D1F4C:Requiem.esp | Ebony, Dragonbone, Daedric |
| None | 9D1F4D:Requiem.esp | (special/creature ammo with no draw class) |

Heavier ammo wants a heavier-draw bow — this is the keyword that ties an arrow to the
`REQ_WeaponType_Bow{Light,Heavy}` classes in `requiem-weapon-patching`. Map by **density**, not
damage (Elven is the lightest material; Ebony/Dragonbone/Daedric are Massive even though
Dragonbone out-damages Ebony).

## `REQ_ArmorPiercingArrow_Tier<N>` (arrows)

| Tier | FormID | Materials |
|---|---|---|
| (none) | — | Iron |
| Tier1 | 0FD2AF:Requiem.esp | Steel, Silver |
| Tier2 | 0FD2B2:Requiem.esp | Elven |
| Tier3 | 0FD2B1:Requiem.esp | Dwarven, Quicksilver, Orcish, Skyforge Steel |
| Tier4 | 0FD2B0:Requiem.esp | Glass |
| Tier5 | 0FD2AE:Requiem.esp | Ebony, Stalhrim |
| Tier6 | AD3A38:Requiem.esp | Dragonbone |
| Tier7 | AD3982:Requiem.esp | Daedric |

## `REQ_ArmorPiercingBolt_Tier<N>` (bolts) — same tier number, different keyword

| Tier | FormID | Materials |
|---|---|---|
| (none) | — | Iron |
| Tier1 | AD3981:Requiem.esp | Steel, Silver |
| Tier2 | AD3980:Requiem.esp | Elven |
| Tier3 | AD397F:Requiem.esp | Dwarven, Quicksilver, Orcish, Skyforge Steel |
| Tier4 | AD397E:Requiem.esp | Glass |
| Tier5 | AD397D:Requiem.esp | Ebony, Stalhrim |
| Tier6 | AD3A37:Requiem.esp | Dragonbone |
| Tier7 | AD397C:Requiem.esp | Daedric |

**Never cross the series:** an arrow takes `…ArmorPiercingArrow…`, a bolt takes
`…ArmorPiercingBolt…`. The tier number is the same per material; the FormID is not.

## Special weapon-type keywords

| Keyword | FormID | On | Role |
|---|---|---|---|
| `REQ_WeaponType_SilverArrow` | 0FAB11:Requiem.esp | silver arrow + silver bolt | silver-vs-undead interaction |
| `REQ_WeaponType_DaedricArrow` | AD3A4E:Requiem.esp | daedric arrow + daedric bolt | daedric arrow special property |

Silver ammo is steel-tier (damage 50/60) with a silver coating — it carries `WeapMaterialSteel` +
`WeapMaterialSilver` + `REQ_WeaponType_SilverArrow` and the steel-tier AP/weight keywords.

## Flags (the `Flags` field, not keywords)

- **Arrow:** `Flags = NonBolt`.
- **Crossbow bolt:** `Flags = 0` (the absence of `NonBolt` is what makes it a bolt).
- **Creature / siege / trap ammo:** add `NonPlayable` (player can't loot or fire it) — e.g.
  `NonPlayable, NonBolt` on the Dwarven Sphere Bolt.
