---
title: Requiem skills distillation (rewrite brief)
date: 2026-10-30
audience: Aaron, Heisen
scope: all 11 authoria-requiem skills as tested (scoring/synthesis/synth-work/skills/), merged from the 12 per-skill and cross-cutting files in this folder
inputs: drill/distill/{requiem-*.md, cross-cutting.md}; checked against drill/r3/INTEGRATED.md §2-§3 and drill/r3/CRITIC.md
new computation: none. Line counts and item counts are sums of the per-skill files' own counts
---

# Requiem skills distillation: what helped, what hurt, what was dead weight

Conventions. Rule ids are from `drill/absolutes/requiem-<skill>.json` (rp = router requiem-patching, ll = leveled-list, cons = consumable). Run ids: task (AC animated-carriages, FS falkreath-shades, FQ favor-quests, MC mage-core, RB rot-below, TR triumvirate) + s/c + run, n = nothink. `rNN gN` = judgement group (base as cited in the per-skill file). Deciding points (AC1, FS1, MC1 ...) are the 17 from `drill/r2/U7-ablation` with strict attribution in `drill/r3/R2-attribution`. **E:** = evidence; **I:** = inference. Each item links to the per-skill file that holds its full citation chain.

Layer tags used below: **G** general layer (game-agnostic patching discipline) · **R** router · **P** Requiem profile (generator contract, analogue keys, invariants with probes, traps) · **A** ARR list layer (house conventions) · **D:x** domain skill x · **HC** houseCARL (tool docs / review skills).

---

## 1. Summary

- **The wins are joint, and three things carry them.** (1) The router's plan: enumerate every record, give each a disposition after reading it, route each type, reconcile. (2) One corrective boundary fact the controls lacked: "the Reqtificator does not set NPC template flags, de-level, give custom races traits, write RACE, or touch a mod's own LVLI". (3) A domain skill naming which live comparable to read. Of 17 deciding points: 9 joint, 1 router-only (FS1 coverage), 1 domain-only (RB1 race stat block), the rest domain-led, own probe or mixed (E: R2). The boundary fact sits behind 6 of the 9 joint points (AC1, AC2, FS2, TR1, TR2, MC1).
- **Values came from the load order, not from skill text.** The only skill-supplied number that decided a point is the MR summon Duration 15 s (FS4, RB4). Consumable ladders (restore over time, ingredient durations) are the other place where text supplied values the controls never attempted (E: R2, R10 FS-C1/C2).
- **Heisen's observation holds and has a clear cause.** Skill agents patched SPEL/MGEF/ENCH because the router put them in scope and the magic skill said where the truth lives (live MR winner, archetype exemplar). Controls listed them and skipped them as "the Reqtificator handles it" (E: FS4, RB4; magic file H1-H3).
- **What hurt is a short list of repeated mechanisms**, not scattered mistakes: the REQ_NULL "inert stubs, none may remain" family (false for every type but PERK; ~63 rule lines in all 11 skills); the blanket "never hand-stamp" line beating the trait-bridge exception; a route with no liveness check (the one failed run, FQ s3); unscoped absolutes (fixed level, wielder tier, creature perk superset, absorb signature, player spell ladder on bosses); stop-to-ask gates (0 helped in 19 rows); checklists that turned into false compliance claims; and houseCARL write shapes that were refused or mangled.
- **About 86% of the text (~10,900 of ~12,660 lines) is unnecessary as written**: duplicated across skills (freshness probe in all 11, bulk-pass x10, checklists x11), houseCARL 1.x recipes (~1,900 lines), generic craft Opus 5.5 does unaided, worked examples never used (~620), and untested-domain references. Untested is not useless: six domains were never exercised and should keep short versions.
- **Cost:** skill arm 3.87x the money, 1.7x per right/major (E: r3 INTEGRATED §2 row 4). Avoidable spend is mostly rule churn (REQ_NULL ~$29, PRKZ ~$3) and lanes reloading skill text; scope work itself is real work (E: U15).
- **Verdict: rebuild, not edit.** Target ~1,550 lines in five layers (G, R, P, A, HC) plus 7 slim domain skills. Retire the script skill as a standalone, fold perk-assignment into npc, fold ammo into weapon. Every cross-skill decision gets one owner (cross-cutting §5).

---

## 2. HELPED: master list

Strength: **router-only / domain-only / joint** per R2; **multi** = 2+ judges or runs; **single** = one judge; **mech** = also passes the R10 mechanical checklist. "Needs" = which layer must carry it after the rewrite.

### 2a. Coverage and procedure

| # | Item | Source (rule ids) | Evidence | Strength | Needs |
|---|---|---|---|---|---|
| HP1 | **Enumerate the whole mod; per-record disposition after a read; drop none; triage never defines coverage** | rp#9-#11, #13, #27, #46, #163 | FS1: 106-123 records patched v 10-22, 4/4 skill runs; controls' main failure "listed, then dismissed unread" (12 of 17 controls outside FQ, drill/coverage). AC: 366 NPCs triaged v 8-12. RB, TR scope widened to types no control opened | **router-only** (FS1); scope half of the 9 joint points | G (dissect fully, scripts included) + R |
| HP2 | **Route each type to its domain skill; the load is what delivers the method** | rp#14, routing-table | MC1 LVLI → LL in 4/4 (MC s2 reversed "de-leveled by Requiem" after the load, L310 → L349). TR NPC_/RACE/WEAP/MGEF routes carried TR1-TR3; TR s1n never routed WEAP and is the only TR skill run to miss the staves (r33 G17). 54/54 lanes loaded their skill (U10) | joint (value = what it delivers) | R, with a mandatory load per nonzero type and a liveness gate (HU-S1) |
| HP3 | **Reconciliation: routed + skipped-with-reason + flagged = enumerated** | rp#50, #51, #108, #109 | FS s1/s3 240/240. Harm by omission: TR s3 skipped it and lost the Skaal chests that 3/3 controls got (R8) | process only (no judged call the controls missed traces to it) | G, unskippable, re-run after any resume |
| HP4 | **A final REQ_NULL scan of what was written (as a scan, not "strip all")** | rp#114, #53, #137 | RB s2/s3 knockdown spell (s2 g12, s3 g15 right/major); FS Apocrypha chest (s2 G43 right/major, controls 0/4); RB s2 VMAD ghost perk (g10 right/minor); AC1 s1 trigger (R3588). Every right value came from copying the live analogue; the premise was false for non-PERK types (R1) | multi, mixed | P (rebuilt per type: "what does this record end up with?") |
| HP5 | **Uniform visibility** (same types covered in every run) | router procedure; MC triage matrix | FS 4/4 runs covered the same 15 types; MC 6 changes common to all 4 (drill/consistency) | consistency gain only | G |
| HP6 | **Lanes write dry-run manifests; the main applies** | agents' own design | 55 lanes, 0 ops lost (U10). 2 cross-record collisions missed (FS s1 RACE v SPEL → r48 G21). Relay helps: RB r15 g38, r26 g42 | delivery only; bought no extra right/major in TR | G (optional, for types in volume) |

### 2b. Boundary facts (the controls' wrong prior)

| # | Item | Source | Evidence | Strength | Needs |
|---|---|---|---|---|---|
| HB1 | **The Reqtificator does not change template flags (0/3,770), write NPC Level, write RACE, give racial traits to races outside the conf lists, or touch a mod's own LVLI** | rp#122 (matrix NPC row), rp#153 (vision pillar 1); integration-checklist L50-51; only half stated today | Controls lost AC1, AC2, FS2 (c1, c3), TR1, TR2 on "the Reqtificator handles NPCs, races and summons" (c2 AC L80; TR c2 L713). TR s3 named the 35 PCLevelMult summons from pillar 1 before loading npc (L554/L574). Code-proven on the RFI build (U6, R12) | joint; the highest-leverage line in the set | **P** (one ~10-line block with probes); R and domains link to it |
| HB2 | **A mod's own leveled lists are untouched; de-level them by hand** | ll#33, #8, #64 | MC1: 4/4 v 0/3, right/major in all 7 MC judgements. FS death-item lists 4/4 v 0/4 (minor) | joint with router route; multi | P (fact) + D:ll (action, after liveness) |
| HB3 | **Custom races get no trait perks from the build; hand-add the analogue's (trait-bridge Fix 2 / race Layer B)** | npc#79, #80, #150, #152; race#8, #71, #67, #121; perk#7, #91 | TR s1 r95 G1, s2 r69 G3 right/major; RB s2 r15 g5 right/major (reference lacks them); AC s1 g7, s2 g14 right. **0 control hits for RFTI_Trait_ in any task** (facts #3). FS: debatable where applied. Code: only `ActorAssignmentRules_<plugin>.conf` race lists assign them (R5, R12) | multi; domain | P (fact) + D:race names the perk, D:npc applies it |
| HB4 | **Perks and spells inherit only through SpellList: tick it on a Stats-only combatant** (Workflow B) | npc#47, #48; npc Bulk-pass L117-118 (#26) | AC1: s1 r63 g0, s2 r64 g4 right/major (58 NPCs); controls not_done (believed the build does NPC perks). s3 missed 60 fighters (R9). Triggers: s1 router REQ_NULL scan, s2 the npc bulk-pass ownership line | multi; joint | P (fact) + D:npc (the drafted R9 line) |
| HB5 | **Consumables are never rebalanced by the build; effects use Requiem's REQ_Alch_* MGEFs** | rp#23, #30, #35, #58 (router alchemy.md); cons anchor #2-#5 | RB Saint's Elixir relinked from shrine effects to REQ_Alch_Fortify* 3/3 (s1 g42, s2 g38, s3 g52) v controls 0/3. FS s2 ingredient flags + effects G38 right/major v 0/4 | multi; joint (router-held) | P (boundary) + D:consumable (record facts) |
| HB6 | **Ammo keywords are all source-carried; carry NoDamageRescale with a hand value** | ammo#4, #5, #7, #41, #49, #74, #81, #130, #131; rp#120 | 7/7 skill runs stamped the comparable's set incl. NoDamageRescale, all right (FS s2 G24, s3 G31 major); 0 controls patched AMMO. The fact is incomplete: the build does x4 with no keyword (R6) | single per run, consistent; joint | P (corrected: "x4, no keyword") |
| HB7 | **Authority = the live winner; for spells, staves, summons that is the MR winner, not Requiem.esp** | rp#6, #56, #180; magic#18, #30, #32, #33; weapon#4 | FS4: s1-s3 right. The one control that priced spells (c1n) read MR Fireball 240 and still priced from Requiem.esp 156 (G26 wrong; R2 FS4). TR c3 read Requiem.esp for staves and missed (U12 T1) | joint, domain-led | P (one line) |

### 2c. Domain methods (where to look, what to copy)

| # | Item | Source | Evidence | Strength | Needs |
|---|---|---|---|---|---|
| HD1 | **NPC: copy the live analogue's whole kit** (class, perks, ActorEffect incl. tempering trait and castable spells); sparse source kit is not the bar | npc#51, #57, #59, #92, #223; perk#13, #33, #37, #43 | AC s1 g3, s2 g8 right/major, s3 g8 right; FS s2 G5 right/major; RB s3 r54 g2/g3 right/major; FS s3 r98 g2 right/major. Controls not_done. Counter-weight: #51 also sits behind 24/25 RB wrong/major level rows (U11) | multi; joint | D:npc (perk-assignment folded in) |
| HD2 | **NPC: de-level PCLevelMult actors; summons are combatants** | npc#16, #17, #40, #41, #78 | TR1: 35 summons in 4/4 skill runs v 0/3. AC2 merchants + own fighters 3/3 v 0/4. FS creatures s1 G5, s3 G7, s1n G13 | multi; joint | D:npc (scoped to PCLevelMult, see HU-S3) |
| HD3 | **NPC: named non-combatant with wrong numbers → fixed low level** | npc#14, #172 | AC merchants s1 g4, s2 g10, s3 g5 right | multi; joint | D:npc |
| HD4 | **NPC: manual stats for creatures/bosses/summons** (AutoCalc off + analogue DNAM + offsets) | npc#21, #201, #76 | FS s2 G2, s3 G7 right/major (5 FS judges list AutoCalc-dead edits as a reference bug); TR s1n G0 right | multi; reached by breaking the gate | D:npc as a stated default |
| HD5 | **NPC boss: read Requiem's own record of the actor or role** | npc#66, #68, #69, #71 | AC s1 g9 (Tsun from Requiem Tsun L100); FS s3 G9, G2, s1 G9 right/major | multi (same section also harmed, HU-S4) | D:npc, no numbers |
| HD6 | NPC small lines: combat signal beats civilian class (npc#11); ActorTypeGhost on spectral actors (npc#89, contested); alternate-state template onto primary (npc#88); role class (npc#110); analogue lane table (npc#140: MR summons, VampireCollection) | npc file H8, H10-H13 | single/minor each (AC s3 g3, AC s1 g10, RB s1 g21, RB s2 g6, TR s1n G1) | single, minor | D:npc, one line each |
| HD7 | **Race: copy the analogue's full stat block, even if the creature gets much tougher** (a permission) | race#54, #36, #48, #73, #81, #39 | **RB1, the only domain-only win**: s1 g28, s2 g12, s3 g15 right/major v 3/3 controls not_done (controls read the analogue and declined, c3 L902). FS2 4/4 v 2/4 | multi; domain-only | D:race (+ BaseMass, gap) |
| HD8 | **Race: classify by SkeletalModel, skin, clone source, behaviour graph** | race#17 | RB2 Gatekeeper → Lurker 3/3 v controls Giant (2 wrong/major). SkeletalModel reads: 0 in every control, 2-3 in every skill run. The skill's tie-break text pointed the wrong way (HU-S7) | multi; domain + own judgment | D:race (tie-break reversed) |
| HD9 | **Race Layer A: trait spells live on the RACE ActorEffect; copy the analogue's live list** | race#109, #57-#59, #64 | TR2 4/4 right/major v 0/3 controls touched RACE. FS = consistency (c2, c1n did it unaided) | multi; joint | D:race |
| HD10 | Race humanoid: replace ActorEffect with the analogue's whole bundle | race#42, #36 | RB s1 g28, g32 right v controls not_done/partial | single | D:race, 1 line |
| HD11 | **Magic: MGEF signature by behaviour** (classify → read the archetype exemplar's keywords/flags/conditions → diff and add); hostile absorb = REQ_Absorb + ResistMagic + conditions | magic#49-#52, #61, #81, #209 | FS absorb s1 G22, s2 G28, s3 G51 right/major v 0/4 controls (facts #5: no control derived the signature) | multi; joint | D:magic (with the beneficial-absorb exception) |
| HD12 | Magic smaller methods: Sun → REQ_SunDamage (magic#227; TR r69 G19 right/major, r95 G23, r33 G10; 0/3); cloak tick keyword (#226; FS s3, TR s1/s1n); HalfCostPerk = school + tier (#64, #65, #247, #248, #256; TR r95 G19, r69 G20; FS); add the comparable's riders (#43, #44, #154; FS s1 G26, s3 G47); staff ENCH = clone the tier-matched MR staff ENCH (#77, #180; RB s3 g33, FS s1 G23); never extrapolate across a family (#15-#17; TR s1 G24) | magic file H4-H11 | multi but mostly minor; 0/3 or 0/4 controls on each | multi/minor | D:magic (tables, ~30 lines) |
| HD13 | **LL: read the comparable list and copy its weighting (tier by repetition)** | ll#48 | MC2: s1-s3 copied MR LItemRobesCollege* 4/3/2/1, no Master tier; 0/3 controls weighted anything. Contested 4/3 on the reference | single per run; own-probe values | D:ll |
| HD14 | **LL: reverse-reference where Requiem keeps the comparable; Add to that list's winner** | ll#16, #71, #51 | MC s2/s3 re-homed 18 Master robes into REQ_LI_HighLevelRobes 32CEE0 (r87 g1, r52 g14 right/major); 0/3 controls; s1 orphaned them | single, 2 runs | D:ll |
| HD15 | **CONT: a mod's copy of a vanilla container drops Requiem's edits; forward Requiem's version** | ll#58, #60, #99 (partly; the clean rule is a gap, U13) | FS strongbox/boss chest/Apocrypha pod s1 r48 g40, s2 r11 g43 right/major, s3 right/minor; 0/4 controls | multi; domain-led | G (clone-forward rule, all types) + D:ll |
| HD16 | **Weapon: MR staff keyword `Nox_KW_Staff_<School><Tier>` from the bound spell's tier; Value 100; charge from the same-tier MR staff, per record** | weapon#36, #105, #106, #114 | TR3 (the only weapon deciding point): s1-s3 right/major v 1/3 controls; Tree Totem tier fixed (reference bug 7/7). Obedience charge 1000 → 3000 s1, s3 v 0/3. FS staff keyword 3 runs v 0/4 (minor) | multi; domain-led (magic §8 also holds it) | D:weapon (one home; magic links) |
| HD17 | Weapon: NonPlayable NPC weapons are combat gear; a NonPlayable bow needs NPCsUseAmmo by hand (weapon#13 first half, #14, #35); enchanted variant keeps base value, Template → base (#63, #102) | weapon file H5, H6 | RB s3 r54 G25 right/major (contested: R6 shows the reference = build output); FS s3 r98 G24 right (c1 also) | single/weak; fact code-verified | D:weapon |
| HD18 | Armor: same-part, same-material, same-weight comparable; ratios only without one; set keyword carries tier (armor#3, #4, #9, #10, #34, #91, #97) | armor file H1-H3 | FS cuirass s1-s3, s1n right/major, but R10 shows the AR gap was router coverage; the method's own edge is cuirass weight (A3: skill 4/4, c1/c1n fail) and not over-setting the Witch Hood (A1) | weaker than judged | D:armor |
| HD19 | Armor/weapon temper COBJ: clone every condition and item of the comparable's recipe | armor#45, #46, #106-#108; weapon#42, #74 | RB new tempers s1 g8, s2 g30, s3 g40 right/minor (no control, not the reference). FS COBJ = noise (controls equal) | multi, minor | D:armor/weapon, 1 line |
| HD20 | Ammo: copy a live Requiem comparable (Damage, Value, Weight, full keyword list); NPC/creature ammo → REQ_Creature_* | ammo#2, #3, #20, #77, #108 | 7/7 runs right (minor-major); 0 controls patched AMMO. Values from reads (ballista 84, riekling 50) | joint, single per run | D:weapon (ammo section) |
| HD21 | **Consumable: ingredient payload** (NoAutoCalculation, exactly 4 REQ_Alch_* effects, per-MGEF durations, magnitude/value from a rarity peer, Value == IngredientValue) | cons#55-#59, #158, #161, #163, #166 | FS s1 G38/G39, s2 G39, s3 G39/G40 right; **R10 FS-C2: 4/4 skill pass, 0/4 controls attempted**; facts F29: the model lacks it | multi + mech; joint | D:consumable |
| HD22 | Consumable: unique consumable → trace how it is obtained; a restocking "unique" gets the ladder value | cons#75 | RB Saint's Elixir 5000 → 600 in 3/3 (judges argued effects, not value) | weak | D:consumable, with the counter-case up front |

### 2d. Value frames (numbers the skill text supplied)

| # | Frame | Source | Evidence | Needs |
|---|---|---|---|---|
| HV1 | **MR summon: Magnitude 0, Area 0, Duration 15 at every tier; ManualCostCalc; NoAbsorbOrReflect** | magic#42, #156, #150, #148, #33, #22, #60, #28 | FS4 s1 G26, s2 G35, s3 G47; RB4 s1 g38, s2 g35, s3 g44 right/major v 0/3 controls. The only deciding-point value from skill text (R2). Agents doubted it and confirmed live (RB s2 L1215) | D:magic + probe "read one MR summon's Effects first" |
| HV2 | **Restore potions heal over time**: dur 20, Value 20/40/60/80/100, mag 3/5/8/10/12 per second | cons#180 | FS s1 G37, s2 G40 right; s3, n debatable; U13 N17 8/8; **R10 FS-C1 4/4 v 0/4** | D:consumable |
| HV3 | Fortify attribute ladder (dur 300, mag 20..100) | cons#182 | RB Saint's Elixir 100/300 s 3/3 (part of "right" is the reference being wrong) | D:consumable |
| HV4 | Staff charge ladder 500/1000/2000/3000 by tier; staff Value 100 | weapon#105, #106 (not the 500 x tier formula, HU-F12) | TR Obedience, TR staves (R10 TR U5, U7) | D:weapon |
| HV5 | ChargeTime FaF 0.25 x tier, Touch half, ritual 3.0 (as "typical; copy the comparable") | magic#35 (FaF part), #151, #152 | TR s1 r95 G20; FS G23-G26 (charge part) | D:magic |
| HV6 | Tome value bands by tier | magic#69, #71, #253, #254 | FS G45/G46, TR r95 G18, r69 G21 right/minor v 0/3 | D:magic |
| HV7 | Value from the comparable, not the mod author's number | rp#88 | MC s2/s3 robe reprices g5-g13 right; but R2 classes MC3 as own probe, and R10 MC I9 finds the regen robes unobtainable (no live effect) | P economy reference, 1 line (weak) |

**Not help (evidence says no):** stat-mode stop (0 of 3 AC decisive wins, U9); forced written plan (U9); verification depth (process §2); prior-art step (1 of 6 tasks, U14); freshness probe (noise everywhere); REQ_NULL → REQ_AbHide re-points (FS3: no in-game effect, R1/R4); clothing AR 0 edits (house convention, controls did it too; R1 relabels 6 MC RM); staff Damage 1 (judged no_effect); potion shell invariants on Malkifer's Elixir (the "right" rested on an own-probe Featherfall fix, R2 RB5); script skill (0 used rules, 0 judged help).

---

## 3. HURT: master list

Fix column names the layer that owns the fix.

### 3a. False facts

| # | Fact as written | Source | Evidence of harm | Fix |
|---|---|---|---|---|
| HU-F1 | **"REQ_NULL_* are inert stubs; no REQ_NULL may remain; strip or replace"** | rp#136-#141, #114, #53, #43; magic resistance-map #259-#267 + #13, #46-#48, #84, #85, #99; npc#106, #117, #259; race#44, #76, #78, #79, #103; weapon#65, #80; armor#25, #69, #83; cons#23, #89, #99; script#14, #60, #68, #112; ll#67, #119, #141, #164 (~63 rule lines) | **Non-PERK (false):** AC s2 swapped a working DR 300 for a Helgen-only effect (r64 g0 wrong/major); FS s1 deleted Putrid Mass's live +500 armour (r48 G21 wrong/major); FS s2 dropped the same 500 DR (r11 G33 → wrong/major, R1); FS s1 removed a live 10000 Weakness to Magic (r48 g20 → wrong/minor); RB s3 stripped a live ENCH from 2 axes (r54 g57 → wrong/minor); FS3 re-points: 3 right/major lost (R1). **PERK (true, but churn):** AC 3/3 shipped PRKZ=0 / binary rewriters; AC s3's strip closed 60 fighters without asking what they keep (R9); TR s2 replaced 41 baked perks to drop 3 stubs (r69 G1 wrong). Churn ~$29 (U15). Beat npc#43 and magic#105 every time | **P**: one per-type fact (PERK emptied 147/154; NPC_, WEAP, MGEF, ENCH, LVLI, LVLN live). Rule: read the fields; never swap by name; never empty a list to drop a stub; never strip a layer another patch baked. Delete every per-skill copy |
| HU-F2 | **"Clothing must be AR 0 in Requiem"** | armor#71, #122, #84; router routing-table L12 | MC s3 saw Ulfric/Jester AR > 0 live (L980) and zeroed "per the skill" (L1097). False as a Requiem fact (20/385 clothing > 0; build never rescales clothing, U6). **But ARR's MC reference zeroes the same items** (r3 CRITIC §1.5) | **A** (house convention) with the Requiem fact beside it. Do not rewrite to "copy the mod's AR" |
| HU-F3 | **"AR ~10x vanilla"; thresholds are caps; high AR gets clamped** | armor#31, #54-#56, #67, #75, #88, #95, #96; router combat-resistance L34 (rp#71) | Latent: 0 RM lost (R1). Affine rescale below thresholds is the truth (code + RFI 70/70) | **P** formula + an effective-AR probe |
| HU-F4 | **"Healing trait needs RegenHpInCombat"** | race#59, #85, #97, #112, #125 | Beat the live DragonPriestRace in TR s1/s1n/s3 and FS s3. 9/16 Requiem healing races lack it (U6). 0 RM lost (R1) | Delete; "copy the analogue's Flags" |
| HU-F5 | **"Lockpicking is an SKSE DLL, not record-patchable"** | rp#34, #44, #128, #199; script#132 | RB s1 skipped doors citing it (neutral upstream). It is a Papyrus script the build stamps on 1,162 CONT/DOOR (U6, R12) | **P** generator contract (upstream: leave; downstream: carry) |
| HU-F6 | **"The quest record carries no Requiem balance"; "most patches need no script"** | rp#169, rp#26; script#1, #8 | FQ s3 never read the quest VMAD; the task's one major fix stayed not_done (r57 g9). Requiem.esp sets BQScript.RewardAmount on BQ01-04 | Delete rp#169; **G** clone-forward rule + script binding check |
| HU-F7 | **"Never hand-add AP perks / the racial trait perk / the werewolf trait"** (conflates build perks with source-carried ones) | npc#60, #74, #221, #229; rp#42 (AP half), rp#203, #210 | TR s1n/s3 withheld physique perks that s1/s2 got right/major; RB s3 refused race Layer B; TR s3 broke it with live evidence and got r20 G6 right/major (REQ_Trait_ArmorPenetration25/50 are source-carried, R5) | **P**: name the build perks exactly (RFTI_All_* AD394A-E, recognized-race traits); everything else is source-carried |
| HU-F8 | **magic#35 "Concentration ChargeTime = 0 always"** | magic#35, #110, #151 | 17 live spells > 0; FS runs broke it on Grasp Soul and were judged right/major (r48 G26). Safe break, no damage | D:magic: "copy the analogue's ChargeTime" |
| HU-F9 | **Fixed numbers read as targets: "dragon priests sit at 50", "MagickaOffset 1300"** | npc#67, #69 | RB s2 Adabelle 200 → 50 (g3 wrong/major), Hierophant 60 (g2 wrong/major); AC s3 Black Knight 200 → 50 (g6 debatable) | D:npc: no numbers; Requiem bosses reach L100+ |
| HU-F10 | **"Creatures use offsets, not AutoCalc"; "non-combatant gets no kit"** | npc#76; npc#172 | 22/86 Requiem summons keep AutoCalc (R5); AC s2 broke #172 for mage vendors, right/major (Farengar has 5 perks, 13 spells) | D:npc: tendencies, with the counter-cases |
| HU-F11 | **"The Reqtificator merges leveled lists (both toggles on)"** and everything built on it (master Requiem, >= 3 contributors, "RFI wins most lists") | ll#4, #121, #6, #52-#55, #70, #72, #84, #91, #122-#136, #163 | Latent; ARR runs with mergeLeveledLists and mergeLeveledCharacters **off** (U6 #7d); RFI LVLI = 872 temper + 230 unroll (R12). ~40% of LL text describes a mechanism ARR never runs | **P** 3-line conditional; **A** "merge off" |
| HU-F12 | Weapon facts: crit floor(Damage/2) as a rule (weapon#30, #93, #132: dead edits in FS); "derive from the RFI winner, no exclusion math" (#8, #27, #58, #81: would double-scale upstream); bow kit as required + "speed rescale" (#35, #59, #113); charge 500 x tier (#76, #103: TR s2 missed Obedience) | weapon file X3-X6 | Mostly latent; one miss (TR s2) | **P** weapon build contract table; D:weapon per-record charge |
| HU-F13 | Ammo: "no ammo rule in the Reqtificator" (ammo#5, #130, #81: omits x4); fixed bolt PROJ profile (#51, #52: FS s1 kept Supersonic → G48 no_effect); material → AP/weight maps (#44-#47) | ammo file X2-X4 | One no-effect edit; the rest latent | **P** "x4, no keyword"; copy the comparable PROJ |
| HU-F14 | "Authoria - Reqtificated - * are tool outputs"; freshness probe "authoring-style profile, generated overlay disabled" | npc-authority L28-32; scope-and-authority | They are handmade downstream patches that win records (TR s1 L1297); TR s1 lanes acted on the label; the best AC control won by copying one (c1n) | **A** "handmade precedents: read and audit first" |
| HU-F15 | **Magic resist capped at 60 per race, counts x3** (unverified) | rp#75, #157 | FS s1 cut Putrid Mass MR 50 → 20 on one ability (G21 wrong/major, joint with HU-F1) | Scope to racial budgets or drop |
| HU-F16 | Smaller false/stale facts: "RFI defines exactly two record types" (cons#4, #101, #105); "forge/temper carry a single HasPerk" (armor#105; Requiem's daedric temper has a night gate); "REQ_Var_* are dead stubs" (weapon#24, unverified) | per-skill files | Latent | P / delete |

### 3b. Unscoped absolutes (right somewhere, applied out of scope)

| # | Rule | Source | Evidence | Fix |
|---|---|---|---|---|
| HU-S1 | **Route a type with no liveness check; "a gated entry in a Requiem game is a bug"** | rp#14 → ll#8, #30, #33, #64, #28, #29, #150; skip exits ll#35, #36 | **FQ s3, the only lost run**: 13 orphan reward lists rewritten (r57 g0-g7 no_effect), bounty fix missed (g8 not_done/major); 4/4 controls ran a reverse-reference. MC dead aaLMCMagickaRegen edit 4/4 skill runs. FQ s1/s2 tied the controls by ignoring the route | **G** liveness gate (reverse refs incl. VMAD; 0 → skip "unreferenced"); R calls it before any route |
| HU-S2 | Cosmetic-skip; "trust a compat patch as coverage" | rp#12; rp#14/#41 | AC s3 skipped Tsun's worn NonPlayable armour (g22 not_done); TR s1n counted the Praedy patch as staff coverage (lost TR3) | R: "a worn NonPlayable ARMO/WEAP is never cosmetic" |
| HU-S3 | **"Always set a FIXED level"** applied to author-fixed levels; levels "from the live analogue" | npc#40, #15, #51 | RB s1 g16 wrong/major (Adabelle 200 → 120, atronach 60 → 20); 24/25 RB wrong/major skill-prompted rows are NPC levels (U11) | D:npc: de-level PCLevelMult only; never lower an author-fixed level unless a same-role comparable is lower |
| HU-S4 | "Never template a boss onto a generic base" read as "never inherit"; "set only what the comparable sets" (masked fields) | npc#66; npc#77 | AC s2 avoided inheriting Requiem Tsun (g11 debatable); AC s3 copied masked troll values (g14 wrong/major; c2, c3, c1n right) | D:npc: may inherit a Requiem-tuned boss template; copy template-resolved values |
| HU-S5 | Templated actors = Workflow A skips (no Stats-without-SpellList case) | npc#22, #42 + HU-F1 | AC s3: 60 Stats-only fighters left with no perks (g0 not_done/major) | D:npc: the R9 line |
| HU-S6 | Combat signal lost to the civilian skip; modded spell "never removed" (no counter-case); class → weapon kit family; cross-family stat transplant | npc#11 v #13/#171; npc#90; npc#57; npc#21, #56 | AC s1 g2 not_done/major; AC s1 g18 / s2 g1 removed stacking resists correctly by breaking #90; AC s3 g6 debatable/major; TR s3 G4/G8 debatable/major | D:npc, one ordered test + counter-cases |
| HU-S7 | **Race: "weigh name + lore over the mesh"** | race#19, #82, #50 | FS 4/4 followed it: shades mapped by name, debatable/major; c2 mapped by skeleton and was judged better than every skill run | D:race: skeleton/skin/clone/behaviour first, name last |
| HU-S8 | Race: near-copy with no size check; UnarmedDamage without the NPC claw trait; Elite-Draugr hard fallback; AttackRace always | race#48, #49; #54, #114; #49, #51, #82; #56 | RB s1 g30 wrong/major (small spider got giant kit); RB s3 g13 wrong/major (unarmed 40 → 20); RB s2 g15, s3 g16 debatable/major | D:race scoped rules |
| HU-S9 | "Trait + the mod's own armour = double count" carried onto a working ability | race#57, #58, #124 relayed | FS s2: the race lane relayed it, the magic lane removed the live 500 DR (r11 G33 → wrong/major, R1) | D:race: scope to skin ARMO AR only |
| HU-S10 | **Absorb signature on a beneficial absorb** | magic#209, #58 | FS s2 Spark of Life now drains its target and aggroes followers (G29 wrong/major) | D:magic: beneficial-absorb exception |
| HU-S11 | Player spell ladder on boss/NPC spells; magnitude change with no anchor + a NoMagnitudeScaling lock; no damage floor | magic#40, #32, #182; #130, #17, #226; #86, #87 | RB s2 g31 debatable/major (300+200 → 80+0), RB s1 g36; TR s1 Draining Shroud halved and locked (r95 G27 wrong/major); FS s1n Scarlet Bolt 20 (G23 wrong/major). No NPC-spell branch at all (RB N5: 0 runs made the 10 NPC-spell fixes) | D:magic: NPC-spell branch + anchor rule |
| HU-S12 | **Weapon damage "at the wielder's tier"** | weapon#13, #62, #32 | RB s3 Malkifer's scythe 30 → 80 from a giant's club (r54 G24 wrong/major); controls kept 30 | D:weapon: an NPC-hand weapon takes its own material tier |
| HU-S13 | **Creature perk "race x tier superset, don't trim"** | perk#34, #72, #143, #146 | FS s1 r48 G3, s1n r93 G3 debatable/major: weapon perks on unarmed creatures, no AP trait | D:npc: armed → same-race kit; unarmed → AP-trait kit |
| HU-S14 | **Clothing frames with no slot condition; "already consistent, leave it"** | armor#122, #62, #18 | MC enchant block missed 4/4 skill runs (not_done/major, 7/7 judges on the reference); only c1 closed it, making c1 the best core MC run (R10 MC I1). MC s1n skipped the 40 after a truncated read | D:armor: extra-slot rule (AR per A + MagicDisallowEnchanting 0C27BD); "consistent" = a probe on the full result set |
| HU-S15 | LL: "rewrite each Level > 1 entry to 1" on single-pick reward ladders; remove a tier without re-homing | ll#151, #150; ll#48 + #57 | FQ s3 near-miss; MC s1 orphaned 15 Master robes (live: no referencer) | D:ll counter-case + reverse-reference after removal |
| HU-S16 | Smaller: ammo "canonical, not REQ_Creature_*" (ammo#31; safe break RB, multi-judge); "creature ammo carries NonPlayable" (#70); cons cookpot / AlchemicalLore gate on a mod's bench (cons#68, #69; judges split); ECZN example values (ll#101, #104) | per-skill files | Overridden or debatable, no wrong/major | Scope or delete |

### 3c. Contradictions between or inside skills

| # | Pair | Who won | Fix (one owner) |
|---|---|---|---|
| HU-C1 | **Hand-stamp ban** (rp#42, #203, npc#60, #74, perk chassis list) **v trait-bridge exception** (npc#79, #152, race Layer B) | Ban won in FS s1, TR s1n, TR s3, RB s3; exception won in TR s1/s2, RB s2 (all right/major where it won). 9/10 lane briefs flattened the ban (U10) | D:race owns custom-race traits; P states only the boundary; no ban anywhere |
| HU-C2 | Router REQ_NULL absolutes v npc#43 "don't clean masked NULLs" / magic#105 "replace on actors" | Router won every time; the premise is false | P per-type fact (HU-F1) |
| HU-C3 | Stat mode: npc#20 (AutoCalc forbids offsets) v #76 (creatures use offsets) v #69 (MagickaOffset) | Split 2-2 in TR and FS; FS s1 offsets under AutoCalc (dead), FS s1n left AutoCalc (not_done/major) | D:npc one default, no gate |
| HU-C4 | race#58 "keep own Ab*, add Resist only if none" v magic resistance-map re-point | RB s1/s2 emptied working abilities after a relay (r26 g34, r15 g32) | Moot once HU-F1 is fixed: leave working abilities |
| HU-C5 | cons#81 "keep the links" v router alchemy.md "reuse REQ_Alch_*" | Agents swapped (right) | D:consumable scoped re-link rule; router holds no numbers |
| HU-C6 | rp#88 "value from comparable" v rp#162 "preserve author intent"; rp#40 "round to 5" v economy.md | rp#88 won (MC) | P economy: one priority line |
| HU-C7 | Ammo stamps RFTI exclusions v armor refuses them | FS s2 noted the conflict | P one stance statement, per type |
| HU-C8 | Internal: LL mastering (ll#38/#54 v #131, #170); weapon NonPlayable (#13 v #123); armor "exactly four keywords" (#36 v #113) and ranged tier (#40 v #61); race flags (#140 v #32); script 6 conflicts (follower bridge, repoint every property); weapon/magic staff convention in two homes | Latent | Delete with the rewrite; one statement each |

### 3d. Gates

| # | Gate | Source | Evidence | Fix |
|---|---|---|---|---|
| HU-G1 | **Stat-mode stop-to-ask** | npc#18, #103, #193, #247 | 4 stops (AC x3, TR s3); each took its own recommendation after "your call"; 0 helped / 14 harmed in 19 gate rows; lanes cannot ask (coin flip) | Default + report line |
| HU-G2 | Flag-to-user (quest GetLevel) | rp#21, #170 | FQ s1 searched the wrong fields, told the user "no quest depends on level" (false: 22 EventConditions gates) | 1 line naming QUST EventConditions, or drop |
| HU-G3 | Forced plan freezes scope | router First step + stop | TR s3's resume never reopened skips, never reconciled (R8) | Reconciliation re-run after any resume |
| HU-G4 | **Checklists as compliance claims** | rp#51, #53; 11 checklists | Errored or unrun checks reported as passes, skill arm only: FQ s2 L1064, MC s2 L1561-L1607 (+2 borderline); "each of 25,987 records dispositioned" over-claims (U12, process §1) | G: evidence-conditioned report lines ("errored = not run") |
| HU-G5 | LL report obligations | ll#21, #35-#40 | FQ: 6 of 7 skill-arm false claims came from skill-mandated steps | Delete; G owns reporting |
| HU-G6 | Latent asks: armor#53, weapon#50, script#73-#76, perk#25/#52/#60, race#69/#70, npc "When to ask" | per-skill files | Never fired | Delete; state the call and go on |

### 3e. Tool shapes (houseCARL)

| # | Shape | Source | Evidence | Fix |
|---|---|---|---|---|
| HU-T1 | "Never PRKZ=0 / SPCT=0", "never an empty ReplaceAll" with no list-clear op | npc#63, #64, #104, #266 | AC s1-s3 binary rewriters / fix.py x3, 84 PRKZ=0 edits; FS shipped empty lists; ~$3.1 | HC list-clear op; skill keeps "don't empty a list you don't own" |
| HU-T2 | Remove-by-value at batch scale; CopyFrom CLAS | npc recipes; rp#139 | AC s3 lost 105/526 ops (R3587); refused calls AC s1 R6257, R6102 | HC |
| HU-T3 | ConditionFloat compose recipe | magic/npc recipes | FS r48 L1040 failed | HC |
| HU-T4 | REQ_NULL scan with `plugins=` on an unregistered patch | rp#112-#114 recipe | Errored in FQ s2, MC s2/s3; reported clean | HC error surfaces; scan the written plugin's read-back |
| HU-T5 | Truncated / capped reads taken as absence | (no skill guard) | 7 false absence claims in 7 runs (5 skill); MC s1n r37 g3 major; FQ s3 limit=60; RB race lanes (U12) | HC notice + 1-2 lines in G |
| HU-T6 | 1.x tool names (~250 mentions) | all recipe files | **0 refused calls in 6/6 tasks**; hygiene and tokens only | Delete (counted under unnecessary) |

### 3f. Process rituals

| # | Ritual | Evidence | Fix |
|---|---|---|---|
| HU-P1 | **Lane briefs paraphrase rules** and carry unchecked lead premises | 9/10 flattened the hand-stamp ban; 2 lanes obeyed the brief over the skill; TR s1 lead premises drove 2 wrong/major (r95 g27/g28); final reports dropped lane warnings in every run (hid RB s3 Malkifer 30 → 80) (U10) | G brief template: facts, scope, ownership, "load skill X"; never paraphrase a rule; final report carries lane flags verbatim |
| HU-P2 | **Fan-out by default** | Bought no extra right/major in TR (s3 unfanned $6.29 / 7 RM v s1 fanned $18.51 / 6 RM); ~half the skill-text bill is lanes reloading skills (U15) | G: single agent by default; lanes only for types in volume |
| HU-P3 | Bulk-pass reading of dead records | FQ s3 read all 13 ladder rungs of dead lists (ll#43); FS s2 script lane read 28 .psc for nothing | Liveness first (HU-S1) |

---

## 4. UNNECESSARY: master list

Sizes are line counts from the per-skill files (approximate; some overlap between rows). "Untested" = the six tasks never exercised it, which is not "useless".

### 4a. Noise (controls did it unaided, or no decision turned on it)

| Item | Where | Evidence | Lines |
|---|---|---|---|
| Freshness probe (load_order_status + Iron Sword conflict tree, wrong-instance procedure) | First step 1 of all 11 SKILL.md; scope-and-authority | rp#4, #5 noise in every task (8 rows, 0 helped); controls ran it too | ~170 |
| Authority prose beyond one line (winner shapes, never re-point) | router, every skill | rp#6/#7/#56/#180 noise; keep one line (HB7) | ~60 |
| "Run it through the Reqtificator" closers | rp#18, #43, #55, #116, #159 + domain copies | 0 helped | ~20 |
| Masters mechanics, FormID layout, resolve/verify read-back | router masters-and-null L8-46; per-skill emit sections | every control got the same masters | ~150 |
| Generic workflow prose: find/read comparable, damage/value/weight derivation, keyword vocab, class essays, COBJ craft | weapon steps 1-7 + crafting.md + damage-ladder; armor crafting + ladder; perk class essay; cons steps 1-2; magic keyword vocab; npc-fields archetype tables | weapon 38 noise v 17 helped rows; controls made the same lookups (U11) | ~1,500 |
| Verification beyond liveness (read-back sweeps, invariant passes) | spread | depth does not predict quality (FS r90 10/10 read-back; FQ s3 13/13 of a dead patch) | spread |

### 4b. Generic tool craft and obsolete shapes

| Item | Where | Evidence | Lines |
|---|---|---|---|
| housecarl-recipes.md x9 | ammo, armor, cons, ll, magic, npc, race, script, weapon | rules used: npc 3 (all harmed), armor 2, ll 2 (harmed), weapon 1, others 0; ammo/script never opened | 1,589 |
| Call blocks inside SKILL.md | npc ~80, ammo ~50, armor ~60, LL ~35, others | 1.x names, 0 refused, mapped silently | ~300 |
| LL merge-behavior.md and its SKILL copies | LL | never read; moot in ARR (merge off) | ~190 |
| Flags-as-unions, rank mechanics, compose grammar | npc, perk, cons, weapon, ammo | tool behaviour | ~60 |

### 4c. Duplicated

| Item | Where | Evidence | Lines |
|---|---|---|---|
| Bulk-pass protocols | 10 SKILL.md + magic bulk-pass.md | 14/114 rules used; router covers it. **Keep** the npc ownership line (npc#26, AC1 s2 trigger) as the R9 line | ~780 |
| Checklists | 11 SKILL.md | 23/135 used; form drove false compliance claims. **Keep** rp#53's scan as a per-type query | ~330 |
| Common mistakes | most skills | restate the body | ~300 |
| Overview + Notes | 11 SKILL.md | 7/57 and 1/34 rules used | ~430 |
| REQ_NULL text per skill | ~63 rule lines + masters-and-null-stripping.md | one per-type fact replaces it | ~150 |
| Hand-stamp / RFTI-assigned lines | ~58 rule lines | one boundary block replaces it | ~90 |
| Router internal duplicates: routing-table.md, integration-checklist items, requiem-vision.md, gap-table rows | router | repeats SKILL table; vision skipped by most runs | ~200 |
| NPC internal duplicates: stat mode x12, fixed level x7, trait bridge x5, boss x4 | npc | every copy lacks scope | ~220 |
| Race restatements (two-layer model x4, Judgment/Common mistakes/Checklist) | race | | ~160 |
| Staff convention in weapon and magic §8 | weapon, magic | two wordings | ~15 |

### 4d. Untested domains (keep a short version each)

| Domain | Where now | Lines now | Keep |
|---|---|---|---|
| Worked examples (all skills) | ~9 files | ~620 | 1 College-robe example (LL); the rest delete |
| Scripts beyond binding: Nox clone, compile, followers, REQ_* controllers | script skill, npc followers.md | ~520 | ~20 (P analogue keys + A follower line) |
| Playable races | race playable-races.md (never read) | 149 | ~15 |
| Router gap mechanics: vampirism, shouts, diseases, exhaustion, stones, food, perks/skills, stealth, combat model | router references | ~340 | ~50 in P |
| Food, drink, alcohol, drugs, poisons, oils | cons food-drink-kits (never read), ladders | ~180 | ~45 |
| Magic: scrolls, MR content survey (never read), MGEF conditions (never read), enchant ladders, spell archetypes | magic | ~500 | ~40 |
| Perk maps by weapon/caster/class | perk (3 refs, 0 reads) | ~356 | ~10 probe list |
| Weapon artifacts/uniques, sounds, Requiem-added weapon types | weapon | ~180 | ~8 |
| Player ammo ladders, PROJ physics, forge, keyword tables | ammo (refs never opened) | ~380 | ~10 |
| LL backbone placement (REQ_LI_*), LVLN/LVSP, zones | LL | ~200 | ~8 |
| Armor: PerkFists, artifacts, novel material, net-new ARMO | armor | ~100 | ~8 |
| **The downstream lane** (bake the Reqtificator layer) | no text; forbidden | 0 | new: one package from R6/R12 code in P |

### 4e. Total

| | Lines |
|---|---|
| Current text (11 skills, SKILL.md + references, excluding evals/index) | **~12,660** |
| Unnecessary as written (sum of the per-skill estimates) | **~10,900 (~86%)** |
| of which untested-domain text | ~3,600 → keep ~260 |
| of which tool recipes / call shapes / merge mechanics | ~2,100 |
| of which duplicated | ~2,700 |
| of which noise / generic craft | ~2,500 |
| Proposed rewrite (all layers) | **~1,550 (~12%)** |
| Not counted: evals/ and index.jsonl (author metadata) | ~550 |

*I:* the category split overlaps by a few hundred lines; the per-skill totals are the firmer figure.

---

## 5. Per-skill verdicts

Counts are each per-skill file's own final counts (raw; the same harm can appear in several skills, which is why §2-§3 dedupe). H = helped, X = hurt, N = unnecessary rows.

| Skill | Now (lines) | H / X / N | Verdict | Target | Rewrite notes | File |
|---|---|---|---|---|---|---|
| requiem-patching (router) | ~1,030 | 11 / 14 / 17 (+10 untested) | **Rewrite** | ~90 SKILL + ~120 into P | Keep the 4-step plan verbatim with unskippable reconciliation and mandatory loads; add liveness gate, clone-forward, NonPlayable-never-cosmetic; move contract, REQ_NULL, stance, economy, gap mechanics to P; delete rp#169, lockpick DLL, hand-stamp lines, routing-table.md, vision.md | [requiem-patching.md](requiem-patching.md) |
| requiem-npc-patching | 1,539 | 13 / 17 / 21 (+7) | **Rewrite** (absorbs perk-assignment) | ~200-230 | New per-NPC order (combatant → template state → level → stats → kit → custom race); R9 SpellList line; stat-mode default, no gate; scoped de-level; no boss numbers; template-resolved values | [requiem-npc-patching.md](requiem-npc-patching.md) |
| requiem-race-patching | ~1,040 | 9 / 13 / 18 (+1 gap) | **Rewrite** | ~170-200 | Skeleton-first classification; copy permission incl. BaseMass and Flags; Layer A; skins AR 0 (skin ARMO only); owns custom-race traits; delete RegenHpInCombat, name-over-mesh, Elite-Draugr fallback | [requiem-race-patching.md](requiem-race-patching.md) |
| requiem-magic-patching | 2,267 | 11 / 9 / 16 (+10) | **Rewrite** (largest cut) | ~290 | MR authority + behaviour-signature method + summon frame + tables; add NPC-spell branch, anchor rule, beneficial-absorb exception; delete resistance-map, survey, recipes | [requiem-magic-patching.md](requiem-magic-patching.md) |
| requiem-leveled-list-patching | 1,047 | 4 / 9 / 14 (+6) | **Rewrite** | ~70-90 | Boundary pointer + flatten action after liveness; comparable weighting; reverse-ref + Add to winner; CONT forward; reward-ladder counter-case; delete merge mechanics | [requiem-leveled-list-patching.md](requiem-leveled-list-patching.md) |
| requiem-armor-patching | 1,070 | 6 / 13 / 16 (+8) | **Rewrite** | ~120-140 | Analogue key + method; effective-AR probe; extra-slot enchant block (the MC loss); clothing AR as ARR convention; delete caps/~10x, ReplaceAll templates, checklist | [requiem-armor-patching.md](requiem-armor-patching.md) |
| requiem-weapon-patching | 1,162 | 6 / 8 / 15 | **Rewrite** (absorbs ammo) | ~90-120 (+~15 ammo) | Weapon build contract table; staff frame (one home); wielder-tier scope; exclusion semantics; pre-rescaled reach gap; delete workflow prose and recipes | [requiem-weapon-patching.md](requiem-weapon-patching.md) |
| requiem-ammo-patching | 933 | 4 / 8 / 16 | **Fold into weapon / P** | ~30-45 | "x4, no keyword; hand value needs NoDamageRescale"; creature ammo → REQ_Creature_*; copy comparable PROJ; keep the mod's NonPlayable | [requiem-ammo-patching.md](requiem-ammo-patching.md) |
| requiem-consumable-patching | 886 (+325 meta) | 4 / 7 / 17 | **Rewrite, keep** (best value per line) | ~185 | Ingredient payload and ladders verbatim; scoped re-link rule; COBJ gate scope; food/alcohol short (untested) | [requiem-consumable-patching.md](requiem-consumable-patching.md) |
| requiem-perk-assignment | 895 | 3 / 6 / 10 | **Fold into npc / P** | ~40-50 | Source-perk kit method (4 lines); armed v unarmed creature scope; Mastery as an ARR decision | [requiem-perk-assignment.md](requiem-perk-assignment.md) |
| requiem-script-patching | 793 | 0 / 8 / 12 | **Retire as standalone** | ~60-80 spread over G, P, A | Rebuild around existing scripts: VMAD probe, preserve mod properties, clone-forward + bind to the winning .pex type (FQ's fix), lockpick as contract line | [requiem-script-patching.md](requiem-script-patching.md) |
| cross-cutting | n/a | 9 / 12 / 12 (+8 gaps) | Build the G and P layers once; ownership map | G ~60, P ~120, A ~40 | One owner per cross-skill decision (its §5) | [cross-cutting.md](cross-cutting.md) |
| **Total (11 skills)** | **~12,660** | **71 / 112 / 172** raw | | **~1,550** | Master lists above: 42 helped, 55 hurt, 32 unnecessary groups after dedupe | |

---

## 6. Corrected fact sheet

Status: **code** = Reqtificator C# + ARR configs, proven on the RFI build on tested paths (R12); **live** = houseCARL read; **multi-judge** = 2+ judges read-backed, not re-read live; **house** = ARR convention; **unverified** = do not encode as fact.

| # | Fact (as the rewrite should state it) | Replaces | Status | Source |
|---|---|---|---|---|
| 1 | Under ARR settings the Reqtificator does not change template flags on overrides (0/3,770), write NPC Level, write RACE records, give racial-trait perks outside `ActorAssignmentRules_<plugin>.conf` race lists (77 fixed FormIDs), or touch a mod's own LVLI | controls' prior; matrix rows rp#121-#123 | code + RFI | U6, R12, R5 |
| 2 | REQ_NULL = "unused by Requiem's own content", not "does nothing". PERK is the only type routinely emptied (147/154). NPC_ 155/159 substantive; WEAP 0 zeroed; ARMO 10/98 AR → 0; MGEF 274 kept, 0 archetype changes, 0 scripts removed; ENCH 61/61 keep effects; LVLI 197 / LVLN 63 keep entries; 0F3F0A edited up to magnitude 100 | ~63 "inert stub" lines | live | U6, R7, R1 |
| 3 | A REQ_NULL → REQ_AbHide resist swap changes only HideInUI and PowerAffectsMagnitude | resistance-map | live | R1, R4 |
| 4 | Perks and spells inherit only through SpellList. Workflow A = Stats **and** SpellList; a Stats-only combatant has only its own list | npc#22, #42 | engine fact; outcome multi-judge | R9 |
| 5 | AR rescale: AR x 3.3 light / 6.6 heavy + 66 body / 18 other, only **below** thresholds (heavy 74/27/27/35/54, light 62/18/18/26/44). Thresholds trigger, never cap. Skips AlreadyReqtified, NoArmorRatingRescale, TemplateArmor, clothing, no part keyword. NonPlayable worn armour **is** rescaled (Tsun 29 → 161) | "~10x, capped" | code + RFI 70/70 | U6, R6 |
| 6 | Ranged-tier (cuirasses only) and REQ_Tempering_* keywords are re-added by RFI even on AlreadyReqtified armour; baking them is redundant, not harmful | "fights the build" | code | R6 |
| 7 | Clothing: Requiem keeps vanilla clothing AR (20/385 > 0); the build never rescales clothing | armor#71 as a Requiem rule | live + code | U6 |
| 8 | ARR's MC patch zeroes stray clothing / extra-slot AR | — | **house** (one patch) | r3 CRITIC §1.5, §4 |
| 9 | Weapons: melee damage x6, reach x0.7 (melee), crit := damage (ignores AlreadyReqtified); bows/crossbows x4, speed **set** to 0.3704 / 0.4445 + WeapBowHeavy / CrossbowHeavy, NPCsUseAmmo added (playable only); DamageType keyword by WeapType unless present; all 3 exclusions = full skip, 1-2 = step-wise; Template-set weapons skipped; NonPlayable not exempt from damage or reach | crit floor(D/2); "bows not x4"; "speed rescale" | code + RFI 1,610/1,610 | R6, R12, R4 |
| 10 | A pre-rescaled weapon with NoDamageRescale only is re-rescaled on reach; add NoWeaponReachRescale | — | code (in-game untested) | R6 |
| 11 | Ammo: x4 damage, no keyword added; a hand value needs NoDamageRescale. Requiem: 234/258 AMMO winners carry it | "no ammo rule" | code + RFI 11/11 | R6 |
| 12 | Lockpicking: REQ_LockpickControl is a Papyrus script the build stamps on every winning CONT/DOOR lacking it (1,162), AlreadyReqtified included. Upstream: leave it; downstream: carry it | "SKSE DLL, not record-patchable" | code + RFI exact set | U6, R12 |
| 13 | AlreadyReqtified is AE3B83 in ARR (upstream 8F08B5 absent). Skips NPC processing and AR/damage/reach/ammo/speed; not lockpick, crit, DamageType or armour keywords | — | code | U6, R6, R12 |
| 14 | REQ_Trait_ArmorPenetration25/50 (0FAB0A/0B) are source-carried; the build's AP perks are RFTI_All_* AD394A-E. AD3A43 (werewolf) is build-assigned only to WerewolfBeastRace; a custom race gets nothing | npc#60, rp#42 AP half, rp#203 | code/config | R5 |
| 15 | RegenHpInCombat is not needed for a healing trait (9/16 Requiem healing races lack it) | race#59 etc. | live (in-game effect untested) | U6 |
| 16 | Concentration spells can have ChargeTime > 0 (17 live, e.g. 03DA6B 0.625) | magic#35 | live | R5 |
| 17 | Requiem summons: 85/86 fixed level; 22/86 keep AutoCalcStats | npc#76 as a rule | live | R5, U4 |
| 18 | Mage vendors carry small kits (Farengar L49, 5 perks, 13 spells); non-mage vendors none | npc#172 | live | R5 |
| 19 | RACE BaseMass is changed by Requiem (Giant 4 → 10, Dragon 4 → 20, DragonPriest 3.5 → 2.2, Draugr 1 → 1.5, Wisp 0.1 → 2.2); clones keep vanilla mass; the C# never sets it | (missing) | live + code | R7 |
| 20 | Re-template targets are Requiem-tuned encounter templates or leveled actors (EncGiant01, 02E1BF), not necessarily REQ_Template_* | "onto REQ_Template_*" | live | R7 |
| 21 | ARR: mergeLeveledLists and mergeLeveledCharacters are **off**; RFI LVLI = 872 tempered + 230 unrolled lists, all Requiem/WAR/VampireCollection | ll#4, #121, #91 | code | U6, R12 |
| 22 | CONT is not merged; a mod's copy of a vanilla container loses Requiem's edits. Authoria Container Merge does not cover the TR Skaal chests | — | multi-judge | U13 §3B, TR ledger |
| 23 | Requiem.esp sets BQScript.RewardAmount on BQ01-04; under Headhunter + Missives the winning .pex declares a GlobalVariable (009F96/009F97), so an Int would not bind | rp#169 | multi-judge 7/7 + decompiled .pex | U13 FQ, R10 FQ C1 |
| 24 | `Authoria - Reqtificated - *` patches are handmade downstream patches that win records | npc-authority L28 "tool outputs" | **house** (maintainer) | rescore #7 |
| 25 | All 6 references are ESL-flagged override-only patches; 0/42 runs flagged theirs | (missing) | live; **house** | R7 |
| 26 | Extra-slot accessories need MagicDisallowEnchanting 0C27BD (or no vanilla slot keyword) | (missing) | multi-judge 7/7 | U13 NM1, R10 MC I1 |
| 27 | Requiem creature skins are AR 0; natural armour comes from REQ_Trait_Armor_* | (missing) | live reads in runs + 3 judges | R2 RB3 |
| 28 | MR summons: Duration 15 at every tier | — | multi-judge + 2 runs' live probes | R10 fix 6 |
| 29 | Restore potions heal over time (dur 20, 3/5/8/10/12 per s); ingredient payload (NoAutoCalc, 4 REQ_Alch_*, per-MGEF durations, Value == IngredientValue) | — | multi-judge + R10 mech 4/4 | R10 FS-C1/C2 |
| 30 | MR staff frame: Nox_KW_Staff_<School><Tier> from the spell's tier, Value 100; charge ladder 500/1000/2000/3000 | 500 x tier | multi-judge (TR 7/7) + live partial | R10 TR U4-U7, U12 |
| 31 | Requiem's daedric temper COBJ carries a GetCurrentTime night gate + HasPerk | "single HasPerk" | live (weapon COBJ; armour not read) | U2, R4 |
| 32 | The Reqtificator holds no Spell, MGEF, Book or COBJ records; magic work is all upstream input | "left to the build" | live | coverage #4 |
| 33 | No tested build path writes ALCH/INGR/COBJ; a consumable override ships as written | "RFI defines exactly two types" | *I:* from R12 code-path table | R12 |
| 34 | Magic-resist cap 60 per race, x3 weighting | rp#75, #157 | **unverified**; harmful on a creature ability | FS s1 G21 |
| 35 | Agility armour stacking from extra-slot Clothing* keywords | — | **unverified** (1 judge) | U13 RO1 |
| 36 | Cosmetic auto-merge; LVLN unrolling; encounter zones (openEncounterZones); why RFI has 0 RACE | rp#12, #22, #166, #177; ll zones | **unverified** (R12 untested paths) | r3 INTEGRATED §3 |
| 37 | REQ_Trait_FX_* "misbehave on modded races"; RFTI_All_PlayableRace GetIsRace gate; REQ_Var_* dead stubs | race, weapon | **unverified** | — |
| 38 | Requiem casters carry no `_Mastery_` gate perks (Ancano, Morokei, EncWarlockNecro07Boss); ARR's RB reference gives them | perk#44 "never" | judge live reads; **contested** / **house** | U2 RB #4 |
| 39 | MR College robe lists 4/3/2/1, no Master tier; Requiem keeps Master robes in REQ_LI_HighLevelRobes 32CEE0 | — | multi-judge; Master-at-L1 contested 4/3 | R10 MC I6 |
| 40 | In-game only: healing trait without the flag; AutoCalc v offsets; Int v GlobalVariable binding; reach on TR summon weapons; REQ_NULL MGEF firing | — | **in-game only** | r3 INTEGRATED §3 |

---

## 7. Open decisions for the maintainers

House conventions (Aaron's call; the evidence cannot settle them):
1. **Clothing / extra-slot AR 0.** ARR policy or one MC patch's habit? If policy, it goes in the A layer with the Requiem fact (#7) beside it.
2. **ESL flag on override-only patches.** Uniform in all 6 references; confirm as an A-layer final step.
3. **Mastery perks on NPC casters.** Requiem's own casters have none; ARR's RB reference gives them. Follow the house or Requiem?
4. **Stat-mode list default** (AutoCalc v manual) if a list-wide default is wanted beyond the npc skill's per-actor default.
5. **NoArmorRatingRescale + baked ranged/tempering keywords** in Reqtificated patches: keep as convention (redundant per code)?
6. **Follower registration via the Authoria bridge**: still current? (untested, dated 2026-07-16).
7. **Contested reference calls** to settle before encoding: Master robes at L1 (4/3), Altar Shield heavy v light (judges v R10), RB bow 68 @ 0.9375 (= build output), Saint's Elixir 3600 s (PLAUSIBLE bug).

Stance policy:
8. **Upstream v downstream** is a per-patch choice. Decide: do the skills only *state* both (proposed), or also ship the downstream package as a tested lane? 0/42 runs went downstream, and 81% of never-made reference units (~6,500) are that layer (U13). A downstream lane needs its own checklist (r3 INTEGRATED §4 item 10).
9. **Fan-out**: single agent by default with lanes only for types in volume (proposed), or keep fan-out available but unprompted?
10. **Price rule priority**: rp#88 (Requiem comparable) over rp#162 (author intent) for Requiem-governed fields (proposed).

Structure:
11. Confirm the merges: perk-assignment → npc; ammo → weapon; script retired into G/P/A. Consumable stays (best value per line).
12. Keep or drop the unverified magic-resist x3 cap and the quest GetLevel flag line.

Untested domains: which to keep short (proposed: all, at the sizes in §4d):
13. Followers; playable races; vampirism/lycanthropy; shouts; diseases; standing stones; food/alcohol/drugs; poisons/oils; scrolls; artifacts/uniques; player ammo and forge; LL backbone placement; Nox special-mechanic reuse; script authoring. Drop any of these to zero only if ARR never patches that domain.

Before the rewrite ships:
14. **Run the P+B arm** (plan + ~10-line boundary block, no domain skills) against K (compact rebuilt skills) on MC, TR, AC, RB, FQ, n = 3 (r3 INTEGRATED §5). It is the one experiment that tells whether the domain methods earn their lines or whether coverage + the boundary fact carry most joint wins. Score with the frozen R10 checklist.
15. File the reference bugs to `arr-release/BUGS.md` (still awaiting Aaron): by judge count 48 CONFIRMED / 39 PLAUSIBLE, of which 14 near-unanimous real defects are ready (drill/r2/U3-reference-bugs).
