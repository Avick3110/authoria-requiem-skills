# Damage & Value Ladders

All numbers mined live from the conflict winners in the Authoria-dev load order (2026-06-07).
They are a cross-check and a starting point — **a live read of the comparable is the authority.**
Requiem touches ~4,340 WEAP; the canonical records are named `REQ_Weapon_<Material>_<Type>`.

Two rules hold everywhere:
- **Critical damage = `floor(Damage / 2)`**, with `Critical.PercentMult = 1`.
- **Speed / Reach / AnimationType are type-defined and material-independent** — copy them from
  any same-type comparable.

## Table of contents
- [One-handed sword material ladder](#one-handed-sword-material-ladder)
- [Per-type shape at the Steel tier](#per-type-shape-at-the-steel-tier)
- [Material value ladder](#material-value-ladder)
- [How to derive a stat the table doesn't list](#how-to-derive-a-stat-the-table-doesnt-list)

## One-handed sword material ladder

Reference type (Speed 1.0, Reach 1.0, AnimationType OneHandSword). Damage steps ~+1 per tier;
value jumps steeply at Glass and above. FormIDs are the canonical records.

| Material | FormID | Damage | Crit | Value | Weight |
|---|---|---|---|---|---|
| Iron | 012EB7:Skyrim.esm | 7 | 3 | 25 | 9 |
| Steel | 013989:Skyrim.esm | 8 | 4 | 45 | 10 |
| Silver | 10AA19:Skyrim.esm | 8 | 4 | 90 | 10 |
| Elven | 0139A1:Skyrim.esm | 9 | 4 | 135 | 8 |
| Dwarven | 013999:Skyrim.esm | 10 | 5 | 180 | 12 |
| Skyforge Steel | 09F25C:Skyrim.esm | 11 | 5 | 315 | 10 |
| Orcish | 013991:Skyrim.esm | 11 | 5 | 225 | 13 |
| Glass | 0139A9:Skyrim.esm | 12 | 6 | 1125 | 9 |
| Ebony | 0139B1:Skyrim.esm | 13 | 6 | 1800 | 15 |
| Stalhrim | 01CDB8:Dragonborn.esm | 13 | 6 | 1800 | 14 |
| Dragonbone | 014FCE:Dawnguard.esm | 14 | 7 | 3600 | 16 |
| Daedric | 0139B9:Skyrim.esm | 15 | 7 | 4500 | 17 |

The damage ladder for a 1H sword is the spine: Iron 7 → Steel/Silver 8 → Elven 9 → Dwarven 10 →
Orcish/Skyforge 11 → Glass 12 → Ebony/Stalhrim 13 → Dragonbone 14 → Daedric 15.

## Per-type shape at the Steel tier

Fix the material (Steel) and vary the type to see each type's damage/value/speed/reach shape and
its assigned damage type. To get a different material, hold the type's speed/reach/anim and shift
damage/value/weight along the material ladder above.

| Type | FormID | Damage | Crit | Value | Weight | Speed | Reach | Anim | Dmg type |
|---|---|---|---|---|---|---|---|---|---|
| Dagger | 013986:Skyrim.esm | 5 | 2 | 20 | 2.5 | 1.3 | 0.7 | OneHandDagger | pierce |
| Shortsword | ADDEFB:Requiem.esp | 7 | 3 | 45 | 8 | 1.1 | 0.9 | OneHandSword | slash |
| Katana | ADDE06:Requiem.esp | 7 | 3 | 45 | 10 | 1.1 | 1.0 | OneHandSword | slash |
| Sword | 013989:Skyrim.esm | 8 | 4 | 45 | 10 | 1.0 | 1.0 | OneHandSword | slash |
| War Axe | 013983:Skyrim.esm | 9 | 4 | 55 | 12 | 0.9 | 1.0 | OneHandAxe | slash |
| Club | ADDF1E:Requiem.esp | 9 | 4 | 65 | 12 | 0.9 | 0.9 | OneHandMace | blunt |
| Mace | 013988:Skyrim.esm | 10 | 5 | 65 | 14 | 0.8 | 1.0 | OneHandMace | blunt |
| Battlestaff | 06B46A:Requiem.esp | 11 | 5 | 80 | 11 | 0.9 | 1.6 | TwoHandAxe | blunt |
| Quarterstaff | ADDF23:Requiem.esp | 15 | 7 | 80 | 15 | 0.8 | 1.3 | TwoHandAxe | blunt |
| Dai-Katana | ADDE09:Requiem.esp | 16 | 8 | 90 | 17 | 0.85 | 1.3 | TwoHandSword | slash |
| Greatsword | 013987:Skyrim.esm | 17 | 8 | 90 | 17 | 0.75 | 1.3 | TwoHandSword | slash |
| Battleaxe | 013984:Skyrim.esm | 18 | 9 | 100 | 21 | 0.7 | 1.3 | TwoHandAxe | slash |
| Warhammer | 01398A:Skyrim.esm | 20 | 10 | 110 | 25 | 0.6 | 1.3 | TwoHandAxe | blunt |
| Bow (Light) | 013985:Skyrim.esm | 25 | 12 | 45 | 5 | 0.5 | 1.0 | Bow | ranged |
| Crossbow (Heavy) | 000801:Dawnguard.esm | 60 | 30 | 65 | 7 | 0.5 | 1.0 | Crossbow | ranged |

Notes:
- The **damage type** column is what the Reqtificator assigns from the weapon-type keyword — it
  is not stored on the record. See `keywords.md`.
- **Club** uses the OneHandMace animation and the mace weapon-type keyword → blunt. **Quarterstaff
  / battlestaff** are blunt (weapon-type keyword `WeapTypeQuarterstaff`). **Dai-katana** is a
  greatsword-class slash weapon.
- **Bow damage (25) and crossbow damage (60)** are hand-tuned by WAR and protected from rescale
  by the `RFTI_Exclusions_*` keywords; bows come in Light/Heavy frame variants.

## Material value ladder

Value is material-tier driven and then scaled by weapon size-class. The sword column above is the
per-material baseline; multiply up for larger weapons (a steel greatsword is 90 = 2× the steel
sword's 45) and down for daggers (steel dagger 20). Because the size-class scaling is not a clean
closed-form, **prefer the same-type same-material comparable's value directly**; only interpolate
when none exists.

| Material | Sword-baseline value |
|---|---|
| Iron | 25 |
| Steel | 45 |
| Silver | 90 |
| Elven | 135 |
| Dwarven | 180 |
| Orcish | 225 |
| Skyforge Steel | 315 |
| Glass | 1125 |
| Ebony | 1800 |
| Stalhrim | 1800 |
| Dragonbone | 3600 |
| Daedric | 4500 |

Artifacts ignore this ladder — their value is bespoke (see `worked-examples.md`).

## How to derive a stat the table doesn't list

1. Read the **same-type, same-material** comparable → copy its stats outright. Done.
2. No same-material record? Read the **same-type, nearest-material** comparable, keep its
   speed/reach/anim/weapon-type keyword, and shift damage along the material ladder, value along
   the material value ladder. Recompute crit = `floor(Damage/2)`.
3. No same-type record? Read the **same-material, nearest-type** comparable for the value tier,
   and a same-type-different-material comparable for the speed/reach/anim shape; combine.
4. Always re-read live — these FormIDs are stable but the winning *values* can change if the load
   order changes.
