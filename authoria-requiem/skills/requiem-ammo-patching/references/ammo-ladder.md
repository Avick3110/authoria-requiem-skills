# Requiem Ammo Ladder (exhaustive)

The complete Requiem arrow + bolt catalog, mined live from the `Requiem - Weapons and Armor
Redone.esp` (WAR) winners. Ammo is a bounded domain, so this is the full set, not a sample. Numbers
are the authority a patch must match — but re-read the live comparable, the load order can shift.

**Two clean rules tie the whole table together:**
- **Arrow damage is a +10-per-tier ladder**, 40 → 120.
- **A bolt is the same-material arrow × 1.2** (damage *and*, roughly, value). Exact on damage.

All ammo has **`Weight = 0`** — heft is the `REQ_AmmoWeight_*` keyword, not the stat.

## Arrows (plain, tiered)

| Material | FormID | Damage | Value | AmmoWeight | AP tier | Material kw |
|---|---|---|---|---|---|---|
| Iron | 01397D:Skyrim.esm | 40 | 1 | Medium | — (none) | WeapMaterialIron 01E718 |
| Steel | 01397F:Skyrim.esm | 50 | 1 | Medium | Arrow Tier1 | WeapMaterialSteel 01E719 |
| Silver | 0FAB0E:Requiem.esp | 50 | 2 | Medium | Arrow Tier1 | Steel 01E719 + Silver 10AA1A (+SilverArrow) |
| Elven | 0139BD:Skyrim.esm | 60 | 2 | Light | Arrow Tier2 | WeapMaterialElven 01E71B |
| Dwarven | 0139BC:Skyrim.esm | 70 | 3 | Heavy | Arrow Tier3 | WeapMaterialDwarven 01E71A |
| Quicksilver | 02623B:Dragonborn.esm | 70 | 2 | Medium | Arrow Tier3 | DLC2WeapMaterialQuicksilver 026230 |
| Orcish | 0139BB:Skyrim.esm | 80 | 3 | Heavy | Arrow Tier3 | WeapMaterialOrcish 01E71C |
| Skyforge Steel | ADE466:Requiem.esp | 80 | 4 | Medium | Arrow Tier3 | WeapMaterialSteel 01E719 |
| Glass | 0139BE:Skyrim.esm | 90 | 14 | Medium | Arrow Tier4 | WeapMaterialGlass 01E71D |
| Ebony | 0139BF:Skyrim.esm | 100 | 22 | Massive | Arrow Tier5 | WeapMaterialEbony 01E71E |
| Stalhrim | 026239:Dragonborn.esm | 100 | 22 | Heavy | Arrow Tier5 | DLC2WeapMaterialStalhrim 02622F |
| Dragonbone | 0176F4:Dawnguard.esm | 110 | 44 | Massive | Arrow Tier6 | DLC1WeapMaterialDragonbone 019822 |
| Daedric | 0139C0:Skyrim.esm | 120 | 55 | Massive | Arrow Tier7 | WeapMaterialDaedric 01E71F (+DaedricArrow) |

## Bolts (plain, tiered) — arrow × 1.2

| Material | FormID | Damage | Value | AmmoWeight | AP tier | Material kw |
|---|---|---|---|---|---|---|
| Iron | 881004:Requiem.esp | 48 | 1 | Medium | — (none) | WeapMaterialIron 01E718 |
| Steel | 000BB3:Dawnguard.esm | 60 | 1 | Medium | Bolt Tier1 | WeapMaterialSteel 01E719 |
| Silver | 0FAB12:Requiem.esp | 60 | 2 | Medium | Bolt Tier1 | Steel 01E719 + Silver 10AA1A (+SilverArrow) |
| Elven | 899DBA:Requiem.esp | 72 | 2 | Light | Bolt Tier2 | WeapMaterialElven 01E71B |
| Dwarven | 00D099:Dawnguard.esm | 84 | 3 | Heavy | Bolt Tier3 | WeapMaterialDwarven 01E71A |
| Quicksilver | 000B5E:WAR.esp | 84 | 2 | Medium | Bolt Tier3 | DLC2WeapMaterialQuicksilver 026230 |
| Orcish | 899DB9:Requiem.esp | 96 | 4 | Heavy | Bolt Tier3 | WeapMaterialOrcish 01E71C |
| Skyforge Steel | 000B8A:WAR.esp | 96 | 5 | Medium | Bolt Tier3 | WeapMaterialSteel 01E719 |
| Glass | 8FCECB:Requiem.esp | 108 | 17 | Medium | Bolt Tier4 | WeapMaterialGlass 01E71D |
| Ebony | 8FCECC:Requiem.esp | 120 | 26 | Massive | Bolt Tier5 | WeapMaterialEbony 01E71E |
| Stalhrim | 000B5F:WAR.esp | 120 | 26 | Heavy | Bolt Tier5 | DLC2WeapMaterialStalhrim 02622F |
| Dragonbone | 8FCECE:Requiem.esp | 132 | 52 | Massive | Bolt Tier6 | DLC1WeapMaterialDragonbone 019822 |
| Daedric | 8FCECD:Requiem.esp | 144 | 65 | Massive | Bolt Tier7 | WeapMaterialDaedric 01E71F (+DaedricArrow) |

*(`:WAR.esp` = `Requiem - Weapons and Armor Redone.esp` — abbreviated for width.)*

## The three independent ladders

Damage, the armor-piercing tier, and the ammo-weight class are **three separate axes** — don't
infer one from another:

- **Damage** = the +10 ladder (arrows) / ×1.2 (bolts). Tracks tier rank.
- **Armor-piercing tier** ≈ tracks penetration/hardness, but **70- and 80-damage materials both sit
  at Tier 3**, so it is *not* a function of damage. Read it off the same-material comparable. Iron
  = no AP keyword (tier 0 baseline).
- **Ammo-weight class** = tracks the material's *density*, not its tier: Elven is Light (the
  lightest), but Stalhrim is only Heavy while the lighter-tier Ebony is Massive. Map by material:
  - **Light:** Elven
  - **Medium:** Iron, Steel, Silver, Quicksilver, Skyforge Steel, Glass
  - **Heavy:** Dwarven, Orcish, Stalhrim
  - **Massive:** Ebony, Dragonbone, Daedric

## Special / non-tiered ammo (bespoke — see SKILL.md § Judgment)

| Ammo | FormID | Damage | Value | Note |
|---|---|---|---|---|
| Steel Arrow of Fire | 0E958F:Requiem.esp | 50 | 11 | Elemental: base dmg kept, value bumped, effect on PROJ |
| Bloodcursed Elven Arrow | 0098A0:Dawnguard.esm | 60 | 100 | Quest: base dmg, bespoke value, special PROJ, not craftable |
| Sunhallowed Elven Arrow | 0098A1:Dawnguard.esm | 60 | 100 | Quest: 8 keywords (the sun effect) |
| Arrow from the Soulcairn | 2E233B:Requiem.esp | 80 | 1 | Fixed reward, soul-cairn |
| Bound Arrow | 10B0A7:Skyrim.esm | 80 | 0 | Conjured by Bound Bow → `requiem-magic-patching` skill |
| Dwarven Sphere Bolt | 07B935:Skyrim.esm | 84 | 0 | Creature: `NonPlayable` flag |
| Falmer Arrow | 038341:Skyrim.esm | 40 | 1 | Enemy ammo (iron-tier) |
| Forsworn Arrow | 0CEE9E:Skyrim.esm | 30 | 1 | Crude enemy ammo (below iron; minimal keywords) |

## Deriving a stat the table doesn't list

1. **Same type + same material exists** → copy it.
2. **Same material, other type** → bolt = arrow × 1.2 (or arrow = bolt ÷ 1.2). Keep the AmmoWeight
   class; swap the AP series Arrow↔Bolt at the same tier number.
3. **Novel material** → pick the tier rung it should sit at (by intended power), copy that material's
   row, and choose the AmmoWeight class by the material's density archetype (wood/feather → Light,
   dense metal/ebony → Massive). State the choice.
