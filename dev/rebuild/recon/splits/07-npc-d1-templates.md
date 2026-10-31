# Split hunt 7: NPC D1, why ARR adds a template to untemplated mod NPCs

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. Split hunt 1 (`2026-10-30-split-npc-workflow.md`, open
item 1) left "D1: ARR adds a template (345 NPCs)" unexplained; it is census conv. 10 in `../census/stage1/NPC_.md`
(Workflow A for template-less mod NPCs). This round pulled the data the census lacked (placement, leveled-list
membership, factions, class, voice, the template's own type, the source record's stats) and tested 50+ hypotheses.
Scripts and data stay local (`census/splits/npcd1_*`).*

## Scope

- **Population:** every census NPC whose source had no inheriting template (no Stats / SpellList inheritance) and
  that ARR patched: **3,336 NPCs**. ARR either adds inheritance (**D1, 360**) or stat-sets the NPC with the marker
  and kit (**B, 2,976**). Split 1 counted 345 D1 inside the baking plugins; 360 includes the skills-era plugins.
- **D1 is two different edits:**
  - **link added (209):** the source had no Template at all; ARR adds Template + Stats/SpellList.
  - **flags ticked (151):** the source already had a look-only Template (Traits / AttackData etc.); ARR adds
    Stats + SpellList on the same link. Animated Carriages is 95 of these.
- Live reads on epoch `e2-58972ca8618e79ea` (the census snapshot). The Animated Carriages patch is active on this
  profile, so it was read live, not from the dumps.

## Answer

**The choice is made per source mod, not per NPC.** Within one source mod ARR templates nearly all its
untemplated NPCs or nearly none. Inside that, a few domain rules move individual NPCs. No property of the mod's
records explains which mods are "templater" mods.

| # | Rule | Requiem / vanilla evidence | ARR evidence |
|---|---|---|---|
| 1 | **The source mod decides.** Use one approach for a whole mod's untemplated NPCs | n/a (a workflow choice) | Source-mod majority predicts **3,269 / 3,326 (98.3%)**; **109 / 129** source mods fully uniform. Nimwraith (a bundle of 11 quest mods) splits exactly along its sub-mods: 7 template, 4 stat-set |
| 2 | **Templater mods are the minority:** 13 of 69 source mods with 3+ NPCs | n/a | Templaters: Animated Carriages, Animated Ships, CFTO, Grand Solitude, Moonpath, Gray Fox Cowl, Val Serano, Ashe, IMG Hagravens, and Nimwraith's Dungeon Delver, Hunt for the Spectre, It Came from Akavir, Once We Were Here |
| 3 | **Never template a child** | Not checked against Requiem's own children | **0 / 23** child-race NPCs templated (Grand Solitude's 4 RS Children kids sit among 65 templated adults) |
| 4 | **Unused records get the marker only** | n/a | Grand Solitude's 10 `NULL_*` records (no placement, no leveled list, no quest) are B with 0 perks; Animated Carriages' "Goods" dummies are B |
| 5 | **A mod-record template must itself be patched.** When the template is another mod NPC, that NPC is patched in the same ARR plugin (or the patch for its own mod) | Old `requiem-npc-patching` doctrine: "walk the chain to a patched base" | **49 / 49** D1 rows on a mod-NPC template point at an ARR-patched record (Ashe's Canas duplicate → Canas; AFDI's Knights of Order → Vigilant's originals; Moonpath variants → `Anvil_dervar` etc.) |
| 6 | **Flags-ticked variants follow their siblings** (seen in Dac0da) | n/a | Dac0da's `…05/06` and `…07/08` stragglers get SpellList when `…02–04` already inherit it from `…01` (10 / 10). Not a general rule: Vigilant's same pattern is stat-set (sibling test 19 / 205 overall) |
| 7 | **In a stat-setter mod, templating is a rare exception** for a ready comparable | n/a | 45 templated NPCs in stat-setter mods: 20 vanilla-species creatures (Sirenroot skeletons → `EncSkeleton01*`, Rot Below giants → `EncGiant01`, LOTD dragons → `EncDragon01Fire`, Kaidan elk, ULP lurker), 19 copies / alternate states of a patched primary, 6 humanoid combatants on a role template. But a comparable is **not sufficient**: only 15 / 462 vanilla-race creatures and 11 / 548 same-mod variants in those mods are templated |

**What the template is, inside templater mods** (all 360 D1):

| Target | n | Examples |
|---|---|---|
| The source's own template, flags ticked | 151 | Animated Carriages `zxACLvl*` → its `zxAC_LChar*` / vanilla `LChar*` lists (their chains end in Skyrim 692 + Requiem 221 records); CFTO drivers → `KmodCarriageDriverTemplate` |
| Requiem template by combat role | 89 | `REQ_LookTemplate_EncBandit01TemplateMelee` (Grand Solitude civilians), `REQ_Bandit_Template_*_03` (Val Serano mutineers), `REQ_LookTemplate_EncGuardImperialM` (Gray Fox Cowl Ben Erai guards) |
| Vanilla encounter template, same species / role | 71 | 58 creatures: `EncSabreCat`, `EncMudcrabMedium`, `EncHorseBlack`, `EncWolfIce`; Moonpath Thalmor → `EncThalmor02Melee1HM` |
| A named vanilla NPC of the same job | 36 | Grand Solitude: priest → Rorlund, wizard → Wuunferth, alchemist → Arcadia, townsfolk → Lisette / Beirand / EvetteSan; Moonpath merchant → Risaad |
| The primary of a duplicate | 13 | Ashe, Val Serano, Xelzaz ghost, Falkreath Shades post-game copy, AFDI knights |

263 targets are NPCs, 97 are leveled lists.

## What this means for the rebuilt skills

- Treat "template or stat-set" as a **per-mod workflow decision**, made once, not per NPC. Split 1's B / A rule then
  follows mechanically (marker exactly when SpellList is not inherited).
- Whichever is chosen, rules 3–5 hold: no child templates, unused records get the marker only, and a mod-record
  template must be patched itself.
- The source mod's own look template is the cheapest correct D1: tick Stats + SpellList on it, as the Animated
  Carriages and CFTO patches do. The Reqtificator never sets template flags, so a Stats-only template gets no
  Requiem perks unless the patch ticks SpellList (control eval, `dev/rebuild/eval-2026-10/tasks/animated-carriages.md`).
- Fit still matters when templating. The census already flags Grand Solitude's civilians on bandit `039CFD` and
  Animated Carriages' mixed pools.

## Open residual

- **Which mods get templated (the mod-level choice) is not explained by any record property** (all 11 mod-level
  hypotheses below are dead). Provenance, the last resort: 3 of the 13 templater mods (Moonpath, Gray Fox Cowl,
  Val Serano) are the old skills' own live runs, whose doctrine said "template onto a Requiem base is the clean
  path". The other 10 are unexplained. Question 1 asks Heisen.
- **Grand Solitude, 3 placed adults stat-set** among 65 templated (`WSAlfbrand` A69294, `WSEiruki` A69366,
  `WSOngmar` A69368). Same source shape as their templated neighbours. `WSAlfbrand` carries a Template link with
  no flags plus the 45-perk kit (bugs file).
- **Grand Solitude target choice** (vanilla civilian v bandit look template) leans on sex: women 16 / 26 on a
  vanilla NPC, men 24 / 34 on a bandit template. Nothing cleaner was found.
- **Markarth Entrance (Lux Via) is the reverse of Grand Solitude:** its 3 combatants are templated and its 14
  farmers / vendors stat-set.

## Hypothesis log

Population and within-plugin test as in split 1: D1 rate when the property holds v when it doesn't, then how many
mixed plugins agree in direction. "Dead" = inconsistent within plugins or explained by the per-mod rule.

**Record level, round 1 (live reads, 1,795 NPCs in the 36 D1 plugins):**

| # | Hypothesis | D1 yes v no | Within | Result |
|---|---|---|---|---|
| H1 | Source already has a look-only template | 35% v 15% | 8 / 12 | Real but mod-driven (Animated Carriages 95 / 95, Glenmoril 1 / 151) |
| H2 | Unique | 26% v 17% | 6 / 14 | Dead |
| H3 | Respawn flag | 28% v 16% | 5 / 10 | Dead |
| H4 | Placed (ACHR) | 23% v 17% | 7 / 10 | Dead |
| H5 | In a leveled list (LVLN) | 4% v 22% | 1 / 5 | Dead (reverse of "generic spawns get templates") |
| H6 | Generic spawn (not unique, LVLN or unplaced) | 17% v 22% | 3 / 10 | Dead |
| H7 | Guard faction | 14% v 20% | 1 / 3 | Dead |
| H8 | Any crime faction | 47% v 17% | 4 / 10 | Dead (Grand Solitude carries it) |
| H9 | Town faction | 57% v 17% | 3 / 10 | Dead (same) |
| H10 | Job faction (merchant etc.) | 53% v 18% | 3 / 11 | Dead |
| H11 | Bandit / hostile faction | 26% v 20% | 3 / 6 | Dead |
| H12 | Civilian class | 16% v 20% | 1 / 10 | Dead |
| H13 | Essential or protected | 35% v 13% | 4 / 14 | Dead |
| H14 | Source AutoCalc | 19% v 35% | 9 / 13 | Dead (reversed overall) |
| H15 | Source level ≤ 1 (stub stats) | 34% v 18% | 3 / 15 | Dead |
| H16 | Has dialogue | 18% v 20% | 6 / 13 | Dead |
| H17 | Quest alias reference | 27% v 17% | 5 / 11 | Dead |
| H18 | Creature voice | 10% v 28% | 11 / 14 | Reversed within plugins; explained by rule 7 |
| H19 | Placed in a city / town location | 25% v 17% (n = 12) | 0 / 3 | Dead |
| H20 | Placed in a dungeon | 19% v 17% | 2 / 4 | Dead |
| H21 | No voice type | 53% v 15% | 2 / 11 | Dead (a look template supplies the voice) |
| H22 | PC level mult (final) | 82% v 16% | 5 / 10 | Dead (consequence: templated NPCs keep it, inert) |
| H23 | Unused record | 4% v 24% | 1 / 10 | Kept as rule 4 |

**Record level, round 2 (source-record stats from the census dumps):**

| # | Hypothesis | D1 yes v no | Within | Result |
|---|---|---|---|---|
| S1 / S4 | Source PC level mult / vanilla-autocalc shape (AutoCalc + mult) | 16% v 23% | 8 / 14 | Dead |
| S2 | Source has 0 perks | 28% v 13% | 5 / 14 | Dead |
| S3 | Source has 0 spells | 25% v 13% | 8 / 15 | Dead |
| S5 | Source skills max ≤ 50 | 30% v 12% | 4 / 14 | Dead |
| S6 | Source skills max ≥ 80 (boss-grade) | 10% v 25% | 7 / 11 | Dead |
| S7 | Source fixed level ≥ 30 | 17% v 21% | 5 / 10 | Dead |
| S8 | Source scripts | 12% v 21% | 6 / 16 | Dead |
| S9 | CK-default class (Dremora / Bandit melee) | 20% v 20% | 6 / 13 | Dead |
| S10 | Creature of a vanilla race | 21% v 20% | 8 / 13 | Dead overall; rule 7 inside stat-setter mods |
| S11 | Humanoid with a same-name sibling | 28% v 18% | 2 / 10 | Dead |
| S12 | ARR changed the level | 4% v 45% | — | Consequence, not cause (templated NPCs need no level) |
| — | Child race | 0 / 23 | — | **Kept** as rule 3 |

**Flags-ticked subcase (428 source-templated NPCs, D1 151):** template is a leveled list, vanilla record, mod
record, already ARR-patched, look-only flag family, 0 perks, 0 spells, creature, unique, placed, quest-driven
flags, chained template, same level as the template. All inconsistent within plugins. Sibling consistency holds
only in Dac0da (rule 6).

**Mod level (69 source mods with 3+ NPCs, 13 templaters; best single threshold v the 56 / 69 baseline):** creature
share, civilian share, vanilla-race share, unique share, source AutoCalc share, PC-level-mult share (58 / 69, the
best), median source perks, max level, NPC count, packages share, scripts share. **All dead.** Plugin-level medians
(split 1's 11 stance properties plus these) overlap between templater and stat-setter plugins. Patch file dates
are all the install date, so era can't be read from them.

**Provenance (last resort):** 3 / 13 templater mods are skills-era live runs. That leaves 10 unexplained.
