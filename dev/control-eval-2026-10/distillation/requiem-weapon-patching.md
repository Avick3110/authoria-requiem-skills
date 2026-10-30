---
title: Distillation - requiem-weapon-patching
date: 2026-10-30
skill: requiem-weapon-patching (as tested: scoring/synthesis/synth-work/skills/requiem-weapon-patching/)
inputs: drill/absolutes/requiem-weapon-patching.json (139 rules, ids weapon#N), drill/absolutes/ledger-{falkreath-shades,rot-below,triumvirate}.json + SYNTHESIS.md, drill/facts/SYNTHESIS.md, drill/rescore/SYNTHESIS.md, drill/consistency/SYNTHESIS.md, drill/process/*, drill/r2/{INTEGRATED, U7, U8, U12, U13}, drill/r3/{INTEGRATED, CRITIC, R2, R6, R7, R10}, scoring/synthesis/synthesis.md, scoring/analysis/{rot-below,triumvirate}/analysis.md, runs/*/skill*/mined/metrics.json
---

# requiem-weapon-patching: what helped, what hurt, what was unnecessary

Run codes: FS falkreath-shades, RB rot-below, TR triumvirate. Run ids (scoring-map.csv): FS s1 r48, s2 r11, s3 r98, s1n r93; RB s1 r26, s2 r15, s3 r54; TR s1 r95, s2 r69, s3 r20, s1n r33; TR control-2 r94. "E:" = evidence, "I:" = inference. Judgement groups G<n> are 0-based indexes into `scoring/judgements/<rid>/judgement.json` groups[] (the ledger convention); synthesis.md numbers some RB groups one higher.

## 1. What it is

A WEAP patching skill: find Requiem's same-type, same-material comparable, copy its stats, keywords, recipes and value, plus branches for enchanted weapons, staves and artifacts. 1,162 lines: SKILL.md 412 + 7 references (750) + index.jsonl.

Exposure (E: U8 per-skill table; `mined/metrics.json`): loaded in **10 of 21 skill runs**, i.e. every skill run on a mod that has WEAP except TR s1n (FS 4/4, RB 3/3, TR 3/4). AC, FQ and MC have no WEAP (coverage/mod-census.md). SKILL.md read by 10; enchanted-and-staves.md 10 (8 at 50%+); housecarl-recipes.md 5; crafting.md 4; keywords.md 3; damage-ladder.md 2; worked-examples.md 2; sounds-and-misc.md 1 (8% of lines). Of 139 rules, 33 have a ledger row, 11 a helped row, 3 a harmed row; 101 were read with no evidence, 5 never read (U8). Ledger totals for this skill: 38 noise rows vs 17 helped (absolutes/SYNTHESIS.md). Note: synthesis.md says damage-ladder, keywords, sounds and worked-examples were "never opened"; U8's per-run reads supersede that.

## 2. Helped

Strength key: multi-judge = 2+ independent judgements agree; single-judge = one judgement; joint = needed the router's routing/brief too (R2).

| # | Item | Source | Evidence (task, runs, outcome, control) | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | MR staff keyword: `Nox_KW_Staff_<School><Tier>` derived from the staff's bound spell, not a material ladder | enchanted-and-staves.md "Staves" + "Deriving a staff's keywords"; weapon#36, #106, #114 | TR3 deciding point (U7): skill s1, s2, s3 right (r95 G7-G9, r69 G9-G11, r20 G12-G14, major); controls 1/3 (c2 by own probe, r94 G0). Tree Totem Conj1→Conj2 and Iron Maiden/Life Tap fixed: reference bugs flagged by 7/7 and 5/7 judges (R10 TR U4). Also r95 G10 right/minor: the NonPlayable Leech Bolts staff keyed from its spell (#13 + #106; reference value itself contested, U13 N9). FS: Witch staff keyword right in s2 (r11 G23), s3 (r98 G25), s1n (r93 G17), all minor; FS controls 0/4 (r41 G7, r67 G26 not_done; r90, r49 none). RB: noise (c1 r72 G28, c3 r66 G13 did the same). | multi-judge. R2: domain-only in s1, joint in s2 (brief named staff keywords before the text was read), s3 got it from magic §8 first. Only weapon-skill deciding point (1 of 17) | The fact verbatim, the tier rule (tier = spell's MinimumSkillLevel / EditorID tier code 000/025/050/075/100 → 1-5; R10 TR note, facts F4), and the probe "query WEAP carrying Nox_KW_Staff_* in the MR winner" |
| H2 | Staff frame Value 100 | enchanted-and-staves.md worked example; weapon#105 | TR: 26 staves, s1/s2/s3 right inside r95 G7, r69 G9, r20 G12; controls 0/3 (R10 TR U5, minor) | single-judge per run, folded into major groups | "MR staves: Value 100" (one line; Damage 1 see note below) |
| H3 | Per-record comparable for each staff: copy keyword + value + **charge** from the same school+tier MR staff | enchanted-and-staves.md L74; weapon#106 (+ #67 no family extrapolation) | TR Obedience staff EnchantmentAmount 1000→3000: s1, s3 right (r95 G11, r20 G15); controls 0/3. TR s2 cited the 500×tier formula instead, generalised "charges already tiered" and missed it (ledger TR). RB: Amethyst Scythe staff 3000 by s3 (and c1) | 2 judges | The per-record rule, and the staff charge ladder 500/1000/2000/3000 by tier (R10 TR U7 note; U12: 62/70 Destruction-3 staves = 2000) |
| H4 | Staves take their values from the MR winner, not Requiem.esp | SKILL.md step 2 "Read the winner, which already folds in ... MR"; weapon#4 (authority) | E: TR control-3 read Requiem.esp, concluded "Requiem only tweaks flags on staves", missed r51 G3 major + G4 (U12 T1). FS c1-nothink made the same layer error on spells (facts §4). Skill runs read the MR layer | inference only: no own deciding point or helped ledger row (#4 is "cut (noise)" in absolutes SYNTHESIS); same wins as H1-H3 seen as authority. Joint with the router's authority line | One line: "spells, staves, summons: MR winner; upstream melee/bows: pre-build Requiem.esp/WAR values" (facts #13, #18) |
| H5 | NonPlayable weapons in an actor's hand are combat gear; a NonPlayable bow needs `NPCsUseAmmo` | SKILL.md L92-96; weapon#13 (first half), #14, with the bow kit #35 | RB bow 7F2B73 (NonPlayable): s3 set WAR Steel Light Bow 25 / 0.5 + bow keywords + exclusions + flag, r54 G25 right/major (reference judged wrong); s1 same, buried in debatable r26 G2/G4; s2 added the bow keywords (r15 G19, a not_done group); c2 keyword only (r46 G25), c1/c3 not done. Ledger: helped s3, neutral s1/s2. **Contested:** R6 shows the reference's 68 @ 0.9375 is exactly what the Reqtificator does to that NonPlayable bow, so "reference wrong" is a balance call, not an error. Code confirms the fact itself: the build's NPC-ammo and bow-speed steps skip NonPlayable; damage and reach steps do not (R6) | single-judge; fact code-verified | "NonPlayable is not exempt from the build's damage and reach rescale; it IS skipped for NPCsUseAmmo and bow speed, so set the flag on a NonPlayable bow yourself" |
| H6 | An enchanted variant keeps its base's value; Template → plain base | enchanted-and-staves.md L7-9; weapon#63, #102 (+ #103 FS s3 D21: left the 2500 charge alone) | FS Scarlet (Infused) Battleaxe value 4000 = Requiem ebony battleaxe: s3 right (r98 G24; reference's 6500 + charge 3000 judged debatable); c1 right too (r41 G6), c2/c3/c1n not done. Template/value writes elsewhere = noise (c1 did them; #102 "cut (noise)") | single-judge; controls 1/4: weak | One line. I: the Template link also matters because the build skips every Template-set weapon (R6) |

Not counted as help (ledger label "helped" but controls equal): COBJ perk gate from the same-material comparable (weapon#42/#74; FS s1 r48 G43, s3 r98 G33-G35 right; FS c1 r41 G15-G18, c2 r67 G44-G49, c3 r90 G0-G7 also right; RB temper fixes 6/6 in both arms). See N5.

Not counted as help (ledger "helped", no effect of its own): staff **Damage 1** (weapon#78, also in #105). Judges: r48 G31 no_effect, r11 G23 "damage 1 on a staff does nothing", r98 G25 "harmless"; TR s3 left Damage 0 (overrode #78) and still got r20 G12 right/major; R10 U6 "weak, no judge rated it alone"; in-game effect open (R10 TR). The TR/FS wins in those groups are the keyword and value. Keep as a one-word convention, not a reason.

**Share (R2, U7):** weapon text drove 1 of 17 deciding points (TR3), domain-led. Every value in the wins came from the load order (MR staves), not from skill numbers, except the staff keyword family name itself (R2 SYNTHESIS L119 lists TR3 among the few "what to write" cases). I: the skill's real lever is "staves are an MR-convention record, look there"; the delivery depended on the router routing WEAP (TR s1n never routed it and lost the staves; R2 calls that an own scope assumption about the Praedy patch).

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | "Derive damage ... at the wielder's tier when no twin exists" | SKILL.md L94-95, L357; weapon#13, #62, with #32 | RB s3: Malkifer's scythe 30→80 from `REQ_Creature_Giant_Club` (wielder is a giant), r54 G24 wrong/major (synthesis.md and absolutes SYNTHESIS call it g25), ~480 after the ×6 build. s1/s2 30→24/27 (ledger neutral; synthesis: s1 gave a steel scythe Daedric-tier damage); s3's other NPC-weapon normalisations debatable (r54 G23). Controls kept 30. Ledger RB; analysis RB L53/L113 | no scope (a creature or boss wielder is not a weapon tier) | Scoped rule: "An NPC-hand weapon takes its own material/type tier. Never take damage from a creature's natural weapon or a boss record. Counter-case: no material keyword → nearest same-type Requiem weapon by mesh/name; say so" |
| X2 | "Requiem retires records as inert `REQ_NULL_*` stubs; strip or replace, never keep" + checklist "no REQ_NULL remains" | SKILL.md L365-367, L397-399; weapon#65, #80 (echoes router rp#137) | RB s1 + s3 stripped `REQ_NULL_DLC2EnchPoisonRuneExplosion` from 2 NPC/test axes (s3 r54 G57 debatable/minor; ledger neutral). I: removes a live enchantment, i.e. lost value, not judged as such. U6: all 61 REQ_NULL ENCH still have effects; R7: REQ_NULL WEAP 0 zeroed; MGEFs kept working | wrong fact | Delete from this skill. The router owns the per-type rule: "PERK is the only type Requiem routinely empties; read the fields, never swap by name" (r3 §3) |
| X3 | "On a live profile the winner may be Requiem for the Indifferent — still the authority ... never do manual exclusion math" | SKILL.md L42-46, L180-186, L346-349, L403-408; weapon#8, #27, #58, #81 | Not exercised (RFI disabled in every run). Code: an upstream patch is re-processed, so values copied from an RFI winner (Iron Sword 42) get ×6 again (R6: Requiem.esp 7 → RFI 42). Contradicts the skill's own L411 ("the patch is later run through the Reqtificator") and the router's "inputs, not outputs". FS c1 knew to write base tier after diffing RFI (facts #18, K) | wrong fact + conflict (latent) | State the stance once: upstream → derive from pre-build values (Requiem.esp/WAR; MR for staves); downstream → bake the contract (section 5) and protect it. Remove "no exclusion math" |
| X4 | "Critical = floor(Damage/2), PercentMult 1. Holds across every Requiem weapon / never break" | SKILL.md L196-197, L380-381; damage-ladder.md L7-8; worked-examples.md L61; weapon#30, #93, #132 (+ #70 gate) | True only of pre-build source records. Code: the build sets crit := damage on every non-templated, not fully excluded weapon, ignoring AlreadyReqtified and partial exclusions (R6, 1610/1610). FS s1 (D94, D97) and s3 (D2, D9) wrote crit on 3 weapons: ledger noise; I: dead edits once Reqtified (except a Template-set record, which the build skips) | wrong fact (scope) | "Crit is a build output (:= damage). Upstream: leave it. Downstream: crit = damage" |
| X5 | Bow kit stated as required: both RFTI exclusions + `NPCsUseAmmo` on every bow, "or the Reqtificator rescales the bow and NPCs won't fire it" | SKILL.md L217-219, L350-351; keywords.md L74-79; housecarl-recipes.md D; weapon#35, #59, #113 | Code (R6): exclusions are a choice (WAR's own bows carry NoDamage + NoBowSpeed); without them a bow gets damage ×4 and speed **set** to 0.3704 (crossbow 0.4445) + WeapBowHeavy/CrossbowHeavy; the build adds `NPCsUseAmmo` itself to playable bows. Only NonPlayable bows need the hand flag. RB s3 r54 G25 judged right on one judge, but R6 shows the reference is the build output (balance call). The same run set a bow-type spear thrower 2→10 with no exclusion (r54 G26 debatable: the build would ×4 it), so the "every bow" kit was not even applied consistently. No wrong verdict | over-absolute + partly wrong wording ("speed rescale") | "Hand-set bow values (WAR style) → add NoDamageRescale + NoBowSpeedRescale. Otherwise the build ×4s damage and sets speed. NPCsUseAmmo by hand only on NonPlayable bows" |
| X6 | Enchanted charge pool "≈ 500 × tier" used as the staff/enchant default | enchanted-and-staves.md L34; SKILL.md L390-391; weapon#76, #103 | TR s2 cited it, then assumed the TVR charges were "already tiered" and missed Obedience (ledger TR, neutral). RB s1 kept a 10000 staff charge against Requiem's 3000 (synthesis). Staff ladder is 500/1000/2000/3000 (R10 U7; U12 Destruction-3 = 2000), not 500 × tier at tiers 3-4. Rule mined from one Fire ladder on one sword (inventory note) | wrong number outside its scope; invites family extrapolation | Drop the formula. "Copy the same-tier comparable's charge per record" + the staff ladder |
| X7 | Internal contradictions | weapon#13 vs #123 (NonPlayable: tier it vs skip it); #18 vs #126/#35 (flags are unions vs a scalar `Data.Flags = NPCsUseAmmo` example); #53 vs #70, #136, #137 (don't change a sensible author value vs "Value on-tier" gate and Moonblade resetting value/speed) | absolutes inventory `internal_contradictions`. No judged outcome traced to them | conflict | Keep one statement each: NonPlayable = tier it (H5); flags-union rule moves to houseCARL; "correct only off-tier values; ±1 damage is tolerance" |
| X8 | Cross-skill duplication of the staff convention | enchanted-and-staves.md vs requiem-magic-patching §8 L303 | TR s3 got the keyword from magic §8 before loading the weapon skill (R2 triumvirate L80). Two homes, two wordings (magic §8 names EnchantmentAmount + keyword, not Value/Damage) | conflict risk | One home (this skill), magic §8 points to it |

Latent risks, no judged harm (I, from the inventory): weapon#33/#124 "melee carries exactly 3 keywords" + a `ReplaceAll` example would strip mod-added keywords (FS s3 used ReplaceAll on the Gravekeeper axe and Arkayn maces: judged right/debatable, nothing lost); #37/#73 copy sound links verbatim over a mod's own sounds; #72 checklist "Name encodes material/class" would rename named uniques; #74 checklist demands forge + temper recipes with no NonPlayable/artifact/loot-only exception.

Not caused by this skill: FS s1n "weapons left at base tier" (r93 G15 wrong/major) is router brief + judge variance; identical bytes were judged right elsewhere (consistency #4, rescore #1). TR s1n's staff miss is a lead's scope assumption (R2).

## 4. Unnecessary

| # | Content | Source | Why | Lines | Keep short / delete |
|---|---|---|---|---|---|
| N1 | Freshness probe (load_order_status + Iron Sword conflict tree) | SKILL.md First step 1, L31-46 | duplicated ×11 across skills; noise (every control ran load_order_status; synthesis) | 16 | delete (router owns it) |
| N2 | Bulk pass protocol: enumeration query, disposition, skip categories, reconciliation count, family extrapolation, bulk-record-jobs pointer | SKILL.md L63-140; housecarl-recipes.md L8-26 | duplicates router enumerate/route/reconcile (the router's procedure is what helped, absolutes pattern 1). Section rules used: #13 (harmed), #67 (cited, not done) | 78 + 19 | keep 2 lines: NonPlayable rule (H5) and "read each sibling, formulas only nominate"; rest to router |
| N3 | Workflow steps 1-4: find comparable, read winner, derive damage/crit/speed/reach/weight/value, set material + type keywords | SKILL.md L142-221 | generic craft, noise: FS c1 matched base tiers and keyword fixes unaided (G0-G6); RB material/type keyword fixes 6/6 both arms (armour-set keyword → WeapMaterial; c1 r72 G21, c2 r46 G25, c3 r66 G10). U11: every control made the same WEAP comparable lookup unaided (prompted ≠ needed). Rules #4, #29, #30, #33, #102 = "cut (noise)" in absolutes SYNTHESIS. Exception: step 4's staff keyword line (#36, helped FS s3, TR) is load-bearing; it is kept via H1 | 80 | keep 3 lines + the H1 staff line: damage-type keyword is a build output (verified, R6); Requiem-added types map onto base WeapType keywords (shortsword/katana → Sword, club → Mace, battlestaff → Quarterstaff ADDF81); `_Var_` caution only if verified (§5) |
| N4 | Sounds and impact links; ISC/USSEP inheritance; bow Data fields | SKILL.md step 5 L223-229; sounds-and-misc.md | **untested** (1 run, 8% of lines, 0 used; U8). Generic craft | 7 + 54 | delete; 1 line "copy the same-type comparable's links" if anything |
| N5 | Crafting and tempering: query shapes, depth=4, three recipe kinds, clone gate, worked steel recipe, ingredient table | SKILL.md step 6 L231-277; crafting.md | noise: controls fixed COBJs (FS all 4 controls right or debatable; RB 6/6). Query block duplicated verbatim in SKILL.md and crafting.md | 47 + 109 | keep 2 lines: `REQ_DisableRecipe` AD3B01 to disable (untested, Requiem-specific); Requiem's own Daedric temper night gate (GetCurrentTime) is intended (U2, R4). Delete the Bruma verify step (#44) |
| N6 | houseCARL call shapes (cross_plugin_query, batch_record_detail, bulk_apply, set_field, create_record, compose grammar, verify the write) | step 7 L279-282; housecarl-recipes.md | **obsolete tool shape** (1.x names). Cost 0 refused calls (process SYNTHESIS: mapped silently) but dead weight; 1 used rule in 164 lines | 4 + 163 | delete; flags-union and compose grammar belong to houseCARL docs |
| N7 | Artifact identification ladder + "say so and ask" gate; uniques deviation rules | SKILL.md Judgment L284-336; worked-examples.md §3-4 | **untested**: read by 10 runs, 0 used rules (U8). #50 is a stop-to-ask gate (gates: 0 helped across the eval). No artifact in the 6 tasks | 53 + ~70 | keep 3 lines: "not craftable and in no LVLI → artifact candidate; craftable or looted → normal gear; uniques keep bespoke value and enchantment, state the assumption" |
| N8 | Common mistakes | SKILL.md L338-367 | 9 of 14 bullets restate body rules (#55, #57-#62, #64 dup_of) | 30 | delete |
| N9 | Checklist | SKILL.md L369-399 | restates body; gates least-evidenced kind (17/161 used, U8); #70/#72/#74 unscoped | 31 | delete; fold real facts into the contract table |
| N10 | Damage and value ladders, per-type steel table, derivation order | damage-ladder.md | read by 2 runs (RB), 0 helped (U8). Values are correct pre-build inputs (R6: Iron Sword 7/3/1, Steel Light Bow 25/12/0.5, crossbow 60/30/0.5) but live reads superseded them (synthesis) | 108 | keep ≤5 lines as a cross-check (sword spine + "same type same material first") or delete |
| N11 | Keyword vocabulary: material table, weapon-type table, shared keywords | keywords.md | read by 3 runs, 1 helped row (#114, the staff keyword set, TR: load-bearing, kept via H1); generic except the damage-type block and bow FormIDs | 100 | keep the staff line, the bow/exclusion FormIDs (AD3B2D/2E/2F, 2E232E, 9F9915) and damage-type note; move `MagicDisallowEnchanting` 0C27BD to the armor skill (MC enchant block, 7/7 judges; the keyword appears only here, U13) |
| N12 | Enchanted-weapon section (charge ladder, worked Fire2 example) | enchanted-and-staves.md L5-43; housecarl-recipes.md C | noise (#102 cut; #103: 3 noise, 1 neutral, 1 helped where the right call was to leave the charge alone, see H6/X6) | 39 + 12 | keep 2 lines (H6) |
| N13 | Worked examples (steel greatsword, Fire2, Dawnbreaker, Moonblade) | worked-examples.md | read by 2 runs, 0 used (U8: 0 used across all 9 worked-examples files) | 107 | delete |
| N14 | Overview prose, "live-analogy, never hardcoded numbers" sermon, routing notes | SKILL.md L8-27, L401-412 | generic craft; self-contradicted by the shipped tables (#3 note) | 32 | keep 2 lines: scope + "ammo → ammo skill (or merged), ENCH/MGEF → magic" |
| N15 | Retrieval index | index.jsonl | no reader evidence | 29 | delete |

**Untested domains (keep a short version, do not call useless):** artifacts and uniques (N7); playable bows and crossbows (only one NonPlayable bow, RB); new weapon records via create (housecarl-recipes B); recipe disabling (N5); sounds (N4); Requiem-added weapon types (katana, quarterstaff ...); enchanted-weapon charge on melee (N12); whole-plugin weapon catalogue deliverables; the **downstream weapon bake** (0/42 runs went downstream; U8, U13).

## 5. Corrected facts for this skill

| Fact | Status | Source |
|---|---|---|
| Build pipeline per winning, non-templated WEAP: full-skip check → damage → reach → crit → NPC ammo → bow speed → keywords. Only modified records reach RFI | code + RFI 1610/1610 | R6 §1, R12 |
| Melee damage ×6 (ushort truncation); reach ×0.7 (melee only). Skill never states this | code + RFI | R6, r3 §3 |
| Crit := damage on every non-templated, not fully skipped weapon, ignoring AlreadyReqtified and partial exclusions. The skill's floor(Damage/2) is the pre-build source convention only | code + RFI | R6 |
| Bows/crossbows: damage ×4; speed **set** to 0.3704 / 0.4445 + WeapBowHeavy 9F9914 / CrossbowHeavy 899DBE; NPCsUseAmmo added (playable only). Corrects r2's "no ×4 bow rescale" (not the skill's error) and the skill's "speed rescale" wording | code + RFI 20/20 bows | R4, R6, r3 CRITIC #4 |
| NonPlayable skips speed and NPC-ammo, not damage or reach | code | R6 |
| Exclusions AD3B2D NoDamage / AD3B2E NoWeaponReach / AD3B2F NoBowSpeed: all three = full skip (no RFI record); one or two = step-wise skips. WAR bows carry two, so RFI still sets their crit and DamageType (111 bows) | code + RFI | R6 |
| DamageType keyword (Slash AD3954, Ranged AD3955, Pierce AD3956, Blunt AD3957) assigned by WeapType; crossbows get Ranged via WeapTypeBow; staves none; skipped if any already present; not stopped by AlreadyReqtified. Skill's claim holds | code | R6 |
| Template-set weapons are skipped by every build step | code | R6 (I: variants follow their base in game; not verified) |
| AlreadyReqtified in ARR is AE3B83 (upstream 8F08B5 absent); skips damage/reach/ammo/speed, not crit or DamageType. No winner weapon outside a full skip carries it | code | R6, R12, r3 §3 |
| A weapon a third-party patch pre-rescaled with NoDamageRescale only is re-rescaled on **reach** (0.91→0.637, 0.7→0.49). Add NoWeaponReachRescale (TR 247845, 46FD59) | code; in-game effect untested | R6 §3, R10 TR U14, U13 N6 (5/7 judges right, minor) |
| Staves: RFI changes only crit on 705 staves; exclusions on staves are freeze markers with no in-game effect | code | R6 |
| MR staff frame: `Nox_KW_Staff_<School><Tier>`, Value 100, Damage 1; tier from MinimumSkillLevel / EditorID tier code | multi-judge (TR 7/7 Tree Totem) + control-2 own probe; Damage 1 in-game effect unknown | R10 TR U4-U6, facts #16 |
| Staff charge ladder 500 / 1000 / 2000 / 3000 by tier (Destruction-3 = 2000, 62/70 Requiem/MR staves) | live (partial) + R10 observation | U12 FS #4, R10 TR U7 |
| REQ_NULL: WEAP 0 zeroed; ENCH 61/61 still have effects; MGEFs work. weapon#65 "inert stubs" is false | live | R7, U6 |
| Requiem's Daedric temper recipes carry a night gate (GetCurrentTime); cloning it is right | live | U2, R4; RB ledger |
| Pre-build input values in damage-ladder.md (Iron Sword 7/3/reach 1; Steel Light Bow 25/12/0.5; Steel Crossbow 60/30/0.5) | verified as inputs | R6 samples |
| An armour-set keyword on a WEAP is a mod error; replace with the matching WeapMaterial | multi-judge (RB 5/6 runs; FS shovel 3/4 controls) | U13 RB R1, FS R11; facts #36 |
| "`REQ_Var_*` records are dead stubs with junk stats" | **unverified** (sits on the same false REQ_NULL premise) | weapon#24; no live read |
| Clothing AR 0 house convention | n/a to this skill (armor) | r3 CRITIC |
| Ammo ×4, no keyword; a hand value needs NoDamageRescale | code + RFI 11/11 (only if ammo merges here) | R6 |

## 6. Rewrite brief

**Purpose.** A short Requiem WEAP profile: what the Reqtificator does to a weapon (so the agent picks upstream or downstream knowingly), where Requiem's conventions live per weapon class, and the few traps. Not a weapon-patching tutorial: Opus 5.5 finds comparables, ladders, keywords and recipes unaided (N3, N5).

**Must keep (from §2):**
- Staff frame and its probe: Nox_KW_Staff_<School><Tier> from the spell's tier (NonPlayable NPC staves too), Value 100, charge from the same-tier MR staff, per record (H1-H3). Damage 1 as a stated convention only (no judged effect). Probe: list MR staves with that keyword family.
- Authority per class: staves (and spells/summons) from the MR winner; upstream melee/bows from pre-build Requiem.esp/WAR values (H4, facts #13/#18).
- NonPlayable: tier it like a real weapon; set NPCsUseAmmo by hand on a NonPlayable bow (H5, code-backed).
- Enchanted variant: Template → base, same value, ENCH design → magic (H6).
- Damage-type keyword is a build output; set the WeapType keyword (verified).

**New items:**
- The weapon build contract as one table (§5 rows 1-9): melee ×6 / reach ×0.7 / crit := damage / bows ×4 + speed set + Heavy keyword + NPCsUseAmmo / DamageType / exclusion semantics / Template and NonPlayable skips / AlreadyReqtified. Then two lines: upstream = write pre-build inputs and leave crit/DamageType; downstream = bake the contract and protect hand values (all three exclusions = full skip). Untested lane: say so.
- Hand-set values (uniques, WAR-style bows, NPC bows): which exclusion protects what (NoDamage for damage, NoWeaponReach for melee reach, NoBowSpeed for bow speed).
- Pre-rescaled compat-patch weapons: if a patch carries NoDamageRescale without NoWeaponReachRescale, add the missing one (TR U14; gap 0/7 runs).
- Wrong-type keyword probe: armour-set/armour-material keyword on a WEAP → swap to WeapMaterial by name/mesh (FS shovel controls-only 3/4).
- Creature/boss-hand weapon scope (X1 fix).
- Staff tier map + charge ladder (X6 fix).
- R9 SpellList-on-Stats line: not a weapon item (npc skill). No weapon line needed.

**Delete:** freshness probe, bulk-pass protocol body, workflow steps 1-7 prose, Common mistakes, Checklist, crafting.md, housecarl-recipes.md, sounds-and-misc.md, worked-examples.md, index.jsonl, enchanted-weapon charge formula, REQ_NULL lines, "derive from the RFI winner / no exclusion math", crit floor(Damage/2) as a rule, the stop-to-ask artifact gate. Short keeps for untested domains: artifact acquisition test (3 lines), REQ_DisableRecipe (1 line), Requiem-added WeapType map (1 line), ≤5-line ladder cross-check.

**Target length:** about 90-120 lines in one SKILL.md, no references (from 1,162). If the ammo skill merges in (synthesis suggestion), add ~15 lines (×4, no keyword, NoDamageRescale for hand values, creature ammo carries everything).

**Where content lives:**

| Content | Home |
|---|---|
| Enumerate → disposition → reconcile; NonPlayable-never-cosmetic triage class; REQ_NULL per type; upstream/downstream stance; one freshness/convention probe; routing WEAP and loading this skill when WEAP > 0 | router (general layer) |
| Weapon build contract (§5), analogue keys (MR staves, Requiem.esp/WAR weapons), staff frame, exclusion semantics, traps (wielder tier, pre-rescaled compat weapons, wrong-type keyword, RFI-winner double scale) | this skill = Requiem profile, WEAP section |
| ESL flag on override-only patches (6/6 references, 0/42 runs); per-patch stance choice; reference conventions (none weapon-specific found) | ARR list layer |
| Call shapes, flags-as-union writes, COBJ condition compose, ReplaceAll caveats, read-back | houseCARL skill/docs |
| Enchantment and staff-spell design; `MagicDisallowEnchanting` on extra-slot gear | magic skill; armor skill |

## Verification (adversarial pass, 2026-10-30)

**Changed**
- Group indexes: re-checked against the judgement.json files (0-based). RB fixes: Malkifer scythe is r54 G24 (was g25), the WAR bow r54 G25 (was g26), REQ_NULL axe strip r54 G57 (was g58), s1's bow sits in r26 G2/G4 (was "g3"). Added a header note on index base.
- H1: added FS s1n (r93 G17) and the control groups behind "FS 0/4"; added the NonPlayable Leech Bolts staff (r95 G10 right/minor, reference value contested); named the RB controls that make RB noise.
- H2: dropped Damage 1 from the help claim. Judges call it no_effect / harmless (r48 G31, r11 G23, r98 G25), and TR s3 won without it. weapon#78 moved to a "not counted" note; the rewrite brief keeps it as a convention only.
- H4: downgraded to inference (no own deciding point; #4 is "cut (noise)").
- H5: added #35 as source, s2's partial use (r15 G19), c2's group (r46 G25), ledger labels.
- H6: added #103's single helped row (FS s3 D21) and the judge detail; marked weak.
- X5: added the s3 spear-thrower inconsistency (r54 G26 debatable).
- X2: added the ledger label and the lost-value inference.
- N3 / N11: flagged the load-bearing lines inside them (#36 staff keyword in step 4; #114 staff set in keywords.md) and added U11 (controls made the WEAP comparable lookups unaided).

**Checked, no change needed**
- Every weapon rule id with a helped or harmed ledger row is present: helped #14, #35, #36, #42, #63, #74, #78, #103, #105, #106, #114 (11); harmed #13, #32, #62 (3, all X1). Router rp#14/#175 harm (TR s1n, WEAP never routed) is correctly kept out of this skill's HURT list.
- No HELPED item rests on a REQ_NULL re-point or clothing AR 0 (R1's relabels touch magic/armor groups only). Clothing AR is not claimed false here.
- False-fact claims match r3 INTEGRATED §3 and R6: bows ARE ×4 (X5, §5 say so), bow speed is set, crit := damage, REQ_NULL WEAP 0 zeroed / ENCH live, NonPlayable skips speed and ammo only.
- COBJ "not counted": FS controls c1/c2/c3 all right on the same recipe groups; holds.
- UNNECESSARY items vs U7/R2: the only weapon deciding point is TR3 (staves); no other deciding point or helped row depends on N1, N2, N4-N10, N12-N15. Untested domains list unchanged.
- Rule ids and file/line refs spot-checked against drill/absolutes/requiem-weapon-patching.json.

**Final counts:** HELPED 6 (H1-H3 strong-to-moderate, H5 single-judge, H6 weak, H4 inference), plus 2 "not counted" notes; HURT 8 (X1 judged wrong/major; X2 wrong fact with a debatable edit; X3, X7, X8 latent; X4 dead edits; X5 over-absolute; X6 wrong number); UNNECESSARY 15.
