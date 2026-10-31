# Split hunt 9: weapon reach (and speed)

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`census/stage1/WEAP.md` conv 4 and 8, open
call 4) found reach "snapped to the comparable's class reach" on 92 % of rows and "source reach x0.7" on ~8 %, and
left the split open. This round tested hypotheses first. Scripts and data stay local (`census/splits/reach_*`).*

## Scope

- **Corpus:** 1,398 patched melee weapons whose reach changed (stage 1 + stage 2 raw dumps), with the previous
  provider's reach and speed from the delta files.
- **Only 243 rows can tell the two sides apart.** In 1,139 of the 1,257 "on class" rows the source already sat on
  the class reach, so class x0.7 and source x0.7 give the same number. The split is decided on the 243 rows whose
  source reach is *not* the class value.
- **Comparables:** read live (same snapshot as the census): all 4,340 Requiem.esp weapons, in Requiem's own version
  and in the live winner's (mostly *Requiem - Weapons and Armor Redone*, "WAR"). Keyword names read live.
- **Build code:** the shipped Reqtificator's `WeaponMeleeRangeScaling` and `WeaponRangedSpeedScaling` (decompiled).

## What the build does (sets the frame)

| Field | Build action | Skipped when |
|---|---|---|
| Reach (melee anims only) | `Reach *= 0.7`, flat, no snapping | AlreadyReqtified, AD3B2E NoWeaponReachRescaling, **Template set** |
| Speed, bow / crossbow | set to 0.3704 / 0.4445 + heavy keyword | AlreadyReqtified, AD3B2F, NonPlayable, Template |
| Speed, melee | **never touched** | n/a |

So melee speed is always input. Reach on a templated weapon is never rescaled; Requiem's own `REQ_Ench_*` variants
carry the pre-rescale class value (1.0 / 1.3).

## Requiem's live class table (pre-rescale; baked = x0.7)

The census assumed three classes (1.3 / 1.0 / 0.7). Requiem plus WAR have more. Sub-classes are keyed by the record,
not by a keyword: a wakizashi carries plain `WeapTypeSword`.

| Kind | Reach | Speed (Requiem → WAR live) | Examples |
|---|---|---|---|
| Sword, katana | 1.0 | 1.0; katana → 1.1 | REQ_Weapon_Steel_Sword, _Akaviri_Katana |
| Shortsword, wakizashi | **0.9** | 1.15 → 1.1 / 1.2 | REQ_Weapon_Imperial_Shortsword, _Steel_Wakizashi |
| Miraak's sword | 1.2 | 1.0 | REQ_Artifact_MiraaksSword |
| Dagger, tanto | 0.7 | 1.3; tanto → 1.4 | REQ_Weapon_Iron_Dagger, _Steel_Tanto |
| War axe | 1.0 | 0.9 | REQ_Weapon_Iron_WarAxe |
| Woodcutter's axe, pickaxe | **0.7** | 1.3 | REQ_Special_WoodcuttersAxe, _Pickaxe |
| Mace | 1.0 | 0.8 | REQ_Weapon_Iron_Mace |
| Club | **0.9** | 0.95 → 0.9 | REQ_Weapon_Iron_Club |
| Greatsword, daikatana | 1.3 | 0.75; daikatana → 0.85 | REQ_Weapon_Iron_Greatsword, _Steel_DaiKatana |
| Battleaxe / warhammer / quarterstaff | 1.3 | 0.7 / 0.6 / 0.8 | WeapTypeQuarterstaff `ADDF81:Requiem.esp` |
| Battlestaff | 1.5 → **1.6** (WAR) | 0.9 | REQ_Weapon_Iron_Battlestaff |
| Headsman's axe | 1.5 | 0.7 | REQ_Special_HeadsmansAxe |

WAR also sets bows to 0.4 / 0.5 and crossbows to 0.5 / 0.6 and carries the bow-speed exclusion keyword. That is
where ARR's crossbow / rifle 0.4 comes from (census conv 8), not from the build's 0.4445.

## Answer

| # | Rule | Requiem / build evidence | ARR evidence |
|---|---|---|---|
| 1 | **NPC-only weapons (NonPlayable) keep their own reach and speed**: reach = source x0.7, speed untouched | n/a (the build rescales reach on NPC weapons too; the speed step skips NonPlayable) | Reach 48 / 49 decisive rows; **0** NonPlayable weapons snapped. Speed changed on 6 / 329 NonPlayable rows. Pairs show it: Vigilant `zzzCHPaladinSpear` 1.5 → 0.91, its `...Unplayable` twin 1.5 → 1.05 |
| 2 | **Playable weapons: reach = Requiem class reach x0.7, by animation type**, using the sub-class where one fits | Class table above | 127 / 170 playable decisive rows (75 %). Sub-class cases: Gray Cowl Elven Club 1.0 → 0.63 (club 0.9) |
| 3 | **Reach and speed travel together.** When ARR sets the speed, it sets the class reach too | Melee speed is never build-written, so a changed speed is a deliberate class pick | Speed changed → reach snapped **69 / 72**. Speed set to a live Requiem / WAR class speed on 310 / 325 changed rows |
| 4 | **Speed follows the type keyword, reach follows the animation** | Reach step keys on animation only | Anim / keyword mismatches: speed took the keyword's class 8 / 8 (e.g. Glenmoril `zzzRevChurchHammer2H`, 2H-sword anim + warhammer keyword → 0.6). 2H-axe speed splits by battleaxe 0.7 / warhammer 0.6 keyword 79 / 81 |
| 5 | **Templated variants: class value at input scale** (no x0.7) | Build skips Template; Requiem's `REQ_Ench_*` carry 1.0 / 1.3 | 5 / 5 (Vigilant `0543E3` 1.4 → 1.3; Glenmoril Fishman Warhammer variants 1.3 → 1.0 after their 1H-sword reclass) |
| 6 | **No triple: class value at input scale**, sub-class speeds from WAR | Build applies x0.7 later | Bruma master patch 3 / 3: Wakizushi 0.7 → 0.9 / speed 1.2, Daedric Nodachi 1.0 → 1.3 / 0.85, Iron Club 1.0 → 0.9 / 0.9 (all WAR live values) |

**Dead on the way:** custom-animation and mod type keywords don't exempt a weapon. Every playable weapon with a mod
type keyword snapped by its animation (katana, rapier, halberd, scythe, claw keywords: 17 / 17).

## Domain specifics

- **An already-baked previous provider gets x0.7 again.** Magic Redone's spell-pack patches (Runemaster, Ancient
  Blood, Obscure Magic) bake reach x0.7 and damage x6 and add only AD3B2D. The build then rescales reach a second
  time. ARR took the MR value as source, applied x0.7 and added AD3B2E: Runemaster's runic sword 1.0 → 0.7 (MR) →
  0.49 (ARR). ARR keeps the in-game result MR would get, but the result is a double rescale (bug B3). 11 rows.
- **Requiem's compatibility patches are input-scale.** `Requiem - Campfire.esp` reclasses the Stone Hatchet to the
  tool-axe class (1.0 → 0.7, speed 1.3); ARR's x0.7 → 0.49 is right. `Requiem - ECSS.esp` and
  `Requiem - Wyrmstooth.esp` leave source reach; ARR's x0.7 is right.
- **Tools and wakizashis look like sub-class snaps but can't be told apart from source x0.7.** All their sources
  already sat on the sub-class (Twilight Princess wakizashis 0.9 → 0.63, Luca's Pickaxe 0.7 → 0.49). Pickaxes with a
  1.0 source went to 0.7, not 0.49 (3 / 3: ECSS Amber / Madness Pickaxe, Glenmoril Beast Hunter Pickaxe). So the tool
  sub-class is not applied.
- **Speed values ARR writes are live winner values,** WAR's included (wakizashi 1.2, daikatana 0.85, club 0.9). A
  skill must read the comparable from the winner, not from Requiem.esp.

## Open residual

**43 playable weapons kept their own reach** (source x0.7) where the class rule says snap. Speed was left alone on
40 / 43. No property tested separates them from the 127 snapped ones:
- **Oversized uniques:** Gleamstrike 1.8 → 1.26 (4), Smithscript Greathammer 1.5 → 1.05 (6), Unslaad Festering
  Greatsword, Dragonbone Smashers, Vigilant Mercy of the Vigil, Giant Ebony Axe. But Vigilant's Sword of Jyggalag
  1.7 and Great Sootsword 1.7 snapped to 0.91.
- **Odd-shaped weapons:** Vigilant's Molag Bal Spikes 1.1 → 0.77 and Greatspikes 1.4 → 0.98, while the matching
  Molag Bal Sword and Greatsword snapped. Chef's Ladle, Giant Door, Witch Hunter Iron Fist, Broken Steel Sword.
- **LOTD replicas:** 6 replicas kept their reach while their real weapon snapped (Honorblade 0.91, replica 0.98;
  Chrysamere 0.91, replica 0.77). 120+ other replicas sat on class.

**Misses (not on either rule):**
- 14 warhammers at 0.49 / 0.7 instead of 0.91 (bug B2).
- 3 Ultra Enemy Good Death cleavers at 1.25 x 0.49 (bug B1).
- 7 Glenmoril trick weapons: the whip forms go 3.0 → 1.4, the Threaded Cane 3.0 → 1.05 and the Elegant Cane 1.2 → 0.77.
  These are hand-picked values. The NPC Parasite 2.0 → 1.4 suggests the whips were matched to their NPC twin.
- LOTD's override of REQ_Artifact_GhorbashsAncestralAxe: 1.2 and no triple (bug B4).

These go to the list authors.

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H1 | NonPlayable (NPC-only) weapons keep their source reach | **Kept** (48 / 49; 0 NonPlayable snapped) |
| H2 | Playable weapons snap to the class reach by animation | **Kept**, 127 / 170 (residual above) |
| H3 | Reach and speed are normalised together | **Kept** (speed changed → snap 69 / 72) |
| H4 | Snap follows the keyword rather than the animation | Dead for reach (dagger anim + sword keyword → 0.49, 1 / 1; the build keys on animation); **kept for speed** (8 / 8) |
| H5 | Weapon types outside Requiem's classes (spear, katana, rapier, halberd, scythe, claw) keep source reach | Dead: 17 / 17 mod-keyword weapons snapped; spears and halberds snap 32 / 38 playable |
| H6 | Requiem sub-classes (shortsword / wakizashi 0.9, club 0.9, tool axe 0.7, battlestaff 1.6) explain off-class values | Partly: club 2 / 3; wakizashi and tools fit but are indistinguishable from H1-style source x0.7; tools with a 1.0 source go to 0.7 (3 / 3, so not applied) |
| H7 | Templated variants keep input-scale class reach (build skips them) | **Kept** (5 / 5) |
| H8 | No triple → input-scale values | **Kept** (Bruma 3 / 3; LOTD Ghorbash is the miss) |
| H9 | Previous provider already baked → x0.7 lands twice | **Kept** for MR spell-pack patches (11); dead for Requiem's own compatibility patches (input-scale) |
| H10 | Oversized source (> class) keeps its reach | Dead: oversized sources snapped 112 / 143 |
| H11 | Damage taken from the source (x6) rather than the comparable marks a "source-first" weapon | Dead: 11 / 43 kept v 30 / 127 snapped |
| H12 | Anim / keyword mismatch keeps the reach | Dead: 6 kept v 4 snapped |
| H13 | Replicas keep source reach | Dead: 6 kept, 120+ on class |
| H14 | Model length (ObjectBounds) marks kept weapons | Dead: kept weapons are shorter on median (33 v 121), the opposite of "oversized blades keep reach" |
| H15 | Patch / mod provenance | Last resort: kept rows cluster in single-weapon mods (Gleamstrike, Smithscript) and Vigilant / Unslaad / LOTD, but those plugins also snap most of their weapons |
