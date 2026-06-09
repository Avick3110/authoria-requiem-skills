# Armor-Rating, Value & Weight Ladders

All numbers mined live from the conflict winners in the Authoria load order (2026-06-08).
They are a cross-check and a starting point — **a live read of the comparable is the authority.**
Requiem renames vanilla armor to `REQ_<Weight>_<Material>_<Part>` (cuirass = `Body`).

Two facts frame everything:
- **Requiem stores armor rating at ~10× the vanilla scale.** An iron cuirass reads
  `ArmorRating = 250` (vanilla 25). These are Requiem's own clean ladders, not vanilla numbers.
- **Three coordinates fix a piece: part × material/set × weight class (light/heavy).** Value and
  weight track the **set/material keyword**; armor rating tracks material × part × weight.

## Table of contents
- [Per-part ratio (off the cuirass)](#per-part-ratio-off-the-cuirass)
- [Heavy material spine (cuirass)](#heavy-material-spine-cuirass)
- [Light material spine (cuirass)](#light-material-spine-cuirass)
- [The armor-rating cap (Reqtificator)](#the-armor-rating-cap-reqtificator)
- [How to derive a stat the table doesn't list](#how-to-derive-a-stat-the-table-doesnt-list)

## Per-part ratio (off the cuirass)

Within a material + weight class, every part is a fixed fraction of that material's **cuirass**.
The same factor scales armor rating, value, and weight — **except the shield**, whose AR (×0.60)
differs from its value/weight (×0.50). Verified across iron, steel, daedric, ebony, elven, glass.

| Part | EditorID suffix | Armor rating | Value | Weight |
|---|---|---|---|---|
| Cuirass | `_Body` | ×1.00 | ×1.00 | ×1.00 |
| Helmet | `_Head` | ×0.40 | ×0.40 | ×0.40 |
| Gauntlets | `_Hands` | ×0.30 | ×0.30 | ×0.30 |
| Boots | `_Feet` | ×0.30 | ×0.30 | ×0.30 |
| Shield | `_Shield` | ×0.60 | ×0.50 | ×0.50 |

Worked: ebony cuirass = AR 500 / Val 5000 / Wt 40 ⇒ ebony gauntlets AR 150 / Val 1500 / Wt 12
(×0.30) and ebony shield AR 300 / Val 2500 / Wt 20 (AR ×0.60, val/wt ×0.50). **Prefer the
same-part same-material comparable's values directly;** use the ratio only when none exists.

## Heavy material spine (cuirass)

`REQ_Heavy_<Material>_Body`. Armor rating climbs in clean steps; value and weight jump steeply at
ebony and above. (Heavy weight class = `BodyTemplate.ArmorType HeavyArmor`, keyword `06BBD2`.)

| Material | Body FormID | ArmorRating | Value | Weight |
|---|---|---|---|---|
| Iron | 012E49:Skyrim.esm | 250 | 125 | 20 |
| Steel | 013952:Skyrim.esm | 300 | 250 | 25 |
| Chitin (heavy) | 01CD8A:Dragonborn.esm | 250 | 500 | 20 |
| Steel Plate | 01395C:Skyrim.esm | 400 | 500 | 30 |
| Dwarven (heavy) | 01394D:Skyrim.esm | 400 | 750 | 30 |
| Orcish (heavy) | 013957:Skyrim.esm | 450 | 1000 | 30 |
| Ebony | 013961:Skyrim.esm | 500 | 5000 | 40 |
| Stalhrim (heavy) | 01CD9F:Dragonborn.esm | 500 | 5000 | 40 |
| Dragonplate | 013966:Skyrim.esm | 500 | 10000 | 35 |
| Daedric | 01396B:Skyrim.esm | 600 | 25000 | 50 |

Sampled parts confirm the ratio exactly — e.g. iron: Body 250 / Head 100 / Hands 75 / Feet 75 /
Shield 150; steel: 300 / 120 / 90 / 90 / 180; daedric: 600 / 240 / 180 / 180 / 360.

## Light material spine (cuirass)

`REQ_Light_<Material>_Body`. (Light weight class = `BodyTemplate.ArmorType LightArmor`, keyword
`06BBD3`.)

| Material | Body FormID | ArmorRating | Value | Weight |
|---|---|---|---|---|
| Fur | 10594F:Skyrim.esm | 125 | 50 | 4 |
| Hide | 013911:Skyrim.esm | 150 | 100 | 5 |
| Leather | 03619E:Skyrim.esm | 150 | 100 | 5 |
| Scale | 01B3A3:Skyrim.esm | 175 | 200 | 7.5 |
| Elven | 0896A3:Skyrim.esm | 200 | 400 | 10 |
| Chitin (light) | 01CD87:Dragonborn.esm | 200 | 400 | 10 |
| Dwarven (light) | ADDD73:Requiem.esp | 200 | 500 | 15 |
| Glass | 013939:Skyrim.esm | 275 | 3000 | 12.5 |
| Stalhrim (light) | 01CDA2:Dragonborn.esm | 275 | 3000 | 12.5 |
| Dragonscale | 01393E:Skyrim.esm | 300 | 8000 | 15 |

Faction light sets (nightingale, ancient-shrouded, thieves-guild, forsworn, …) follow the same
shape — read the named comparable directly; their value is often bespoke and their resist tier is
set by the faction (see `keywords.md`).

## The armor-rating cap (Reqtificator)

`Reqtificator.conf` → `armors.armorRatingThresholds` are per-slot caps in the **÷10 (vanilla)
scale**. Multiply by 10 to compare against the record's `ArmorRating`.

| Slot | Heavy cap (÷10) | Heavy cap (record ×10) | Light cap (÷10) | Light cap (record ×10) |
|---|---|---|---|---|
| body | 74 | 740 | 62 | 620 |
| feet | 27 | 270 | 18 | 180 |
| hands | 27 | 270 | 18 | 180 |
| head | 35 | 350 | 26 | 260 |
| shield | 54 | 540 | 44 | 440 |

Requiem's own top tier sits under the cap with headroom — a daedric cuirass (600) is 60 of a 74
cap. So deriving AR from the comparable keeps you under the cap automatically; the cap exists to
clamp over-inflated modded pieces (and to leave room for enchanted/unique ones). Don't mistake the
cap number for the record value.

## How to derive a stat the table doesn't list

1. Read the **same-part, same-material, same-weight** comparable → copy its stats outright. Done.
2. No same-part record? Read the material's **cuirass** and apply the per-part ratio above.
   Recompute value/weight with the same factor (shield value/weight ×0.50, AR ×0.60).
3. No same-material record? Pick the nearest tier on the spine, take its cuirass, and apply the
   per-part ratio; borrow the nearest set keyword for resist/tempering (state the choice).
4. Always re-read live — these FormIDs are stable but the winning *values* can change if the load
   order changes.
