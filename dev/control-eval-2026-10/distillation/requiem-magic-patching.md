---
title: Distillation — requiem-magic-patching
date: 2026-10-30
scope: one skill (SKILL.md + 11 references + index.jsonl), as tested in scoring/synthesis/synth-work/skills/requiem-magic-patching/
evidence: drill/absolutes (requiem-magic-patching.json, ledgers FS/RB/TR, SYNTHESIS), drill/facts/SYNTHESIS.md, drill/coverage, drill/consistency, drill/process, drill/r2 (INTEGRATED, U6, U7, U8, U10, U13), drill/r3 (INTEGRATED §3, CRITIC, R1, R2, R4, R5, R7, R9, R10), scoring/synthesis/synthesis.md
rule ids: magic#N = drill/absolutes/requiem-magic-patching.json; rp# = router; race#/npc# = those skills
---

# requiem-magic-patching — what helped, what hurt, what is unnecessary

Run codes: FS falkreath-shades, RB rot-below, TR triumvirate, AC animated-carriages, MC mage-core, FQ favor-quests. s1/s2/s3/s1n = skill runs, c1/c2/c3/c1n = controls. G/g = judge group in that run's judgement, numbered as in the ledgers (0-based index into `judgement.json`, so one less than the row number in `judgement.md`). Evidence is cited; **Inference** marks my own joins.

## 1. What it is

Designs MGEF / SPEL / ENCH (plus tome BOOK, staff effect, scroll) for Requiem + Magic Redone (MR): school, tier, cost, charge, magnitude, riders, keyword/flag signature, HalfCostPerk, tome value — all derived from a live MR comparable. 2,267 lines (SKILL.md 472 + references 1,795), 281 prescriptive rules (129 in SKILL.md).

**Use (U8, U7):** loaded in **11 of 21** skill runs: all 4 FS, all 3 RB, all 4 TR (TR s3 in the main thread; the rest in fan-out lanes). Never loaded in AC, MC, FQ (no spell work there). SKILL.md reached context in 11/11 loads. Reference reads (runs any / ≥50%): resistance-map 9/9, bulk-pass 8/8, keywords 8/5, perks-and-tomes 8/7, cost-and-magnitude 7/6, enchantments 7/6, housecarl-recipes 7/4, spell-archetypes 7/4, **mgef-conditions 0, mr-content-survey 0, worked-examples 0**. Rules: 68 used (ledger row), 192 read with no evidence, 21 never read; 45 rules with a helped row, 8 with a harmed row. In FS s1 the magic lane produced 37 of the run's citations (synthesis.md).

## 2. Helped

Strength: **multi** = 2+ judges / runs right; **single** = one judge; **joint** = needed the router's routing/coverage to reach the record (R2). Controls: "0/n" = no control made the change. Per R2, values came from live MR reads in nearly every win; skill text supplied the number only for the 15 s summon duration.

| # | Item | Source (file §, rule) | Evidence (task, runs, outcome, control) | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | MR summon frame: Magnitude 0, Area 0, **Duration 15 at every tier**; cost by what is summoned; ManualCostCalc; NoAbsorbOrReflect | SKILL §4 L195-196 (magic#42); cost-and-magnitude Summons row (#156), #150, #148, #33, #22, #60, #28 | FS4: s1 G26, s2 G35, s3 G47 right/major; c1-c3 untouched, c1n debatable G30. RB4: s1 g38, s2 g35, s3 g44 right/major; 0/3 controls. RB s2 L1210-1215 and s3 L1466 doubted 15 s and confirmed it live; FS s1 L1223 "I'll follow despite it seeming unusual" | multi, joint (router coverage + domain value). The **only** win where skill text supplied the number (R2 SYNTHESIS L14) | The fact verbatim + its probe: "read an MR summon spell's Effects at full depth before trusting 15 s" (r3 §3 lists it multi-judge, not re-read live) |
| H2 | Authority for spells is the **live MR winner**, not Requiem.esp; take the comparable's BaseCost; ManualCostCalc is a write (union) or BaseCost is ignored | SKILL §3 L166 (#30), §4 L173-178 (#32, #33), Bulk-pass definition of done L100 (#18, cited by FS s2 for G31), cost-and-magnitude L7-19 (#146, #147, #149), Judgment Uniques (#86) | FS4: Scarlet Bolt s1 G23, Heavy Flame s2 G31, s3 G47 right/major; cloaks s1 G25 (minor) / s3 G47 (major). The one control that priced spells (c1n) read MR Fireball 240 (L296) but priced from Requiem.esp 156: G26 wrong, cloak 271/30 s G28 wrong (R2 FS L82). Facts #13/#15 class P | multi, joint domain-led | One authority line: "spells, staves, summons: derive from the live MR winner" + "set ManualCostCalc with the cost". Drop the 99.3% / 827-of-833 trivia |
| H3 | MGEF signature by **behaviour**: classify → read the archetype exemplar's Keywords/Flags → diff and add; copy Conditions rows from the comparable. Absorb: REQ_Absorb **replaces** the element keyword, ResistMagic, Dwarven/NoLifeDrain conditions | SKILL §5 L214-229 (#49-#52), #61; keywords.md Absorb row L94 (#209), #81 | FS absorb group: s1 G22 right/major (CopyFrom 005B7E / 005D86 conditions), s2 G28 right/major (the reference is the one judged wrong there), s3 G51 right/major; controls not_done 4/4 (c1 G28, c2 G33, c3 G26, c1n G31). Facts #5 class **L**: no control derived the full signature (RB c2 L3334 found the keyword only) | multi, joint | The 3-step diff (5 lines) and the absorb row, **with the beneficial exception** (Hurt X1) |
| H4 | Damage-type table: Sun → `REQ_SunDamage` (and poison/necrotic/entropic → right ResistValue) | keywords.md L310-316 (#227) | TR: +REQ_SunDamage on 4 VsUndead MGEFs: r69 G19 right/major (s2, cited), r95 G23, r33 G10 right/minor (via analogue). 0/3 controls. TR s3 planned the MGEF sweep, never ran it, missed it (ledger TR #131/#132) | multi | The damage-type → keyword → ResistValue table (~8 lines) |
| H5 | Cloak tick signature: `Nox_KW_CloakDamage` (MR master) + NoMagnitude/NoDuration scaling | keywords.md L307 (#226), #205, #206 | FS s3 G51/G52 right; TR s1 D11/D12 r95 G25, s1n r33 G11 right/minor; 0/3 TR controls. FS c3 G8 got cloak keywords unaided (1/4) | multi, minor | Row in the signature table. Add the anchor caveat from Hurt X7 |
| H6 | HalfCostPerk = school + tier classifier; mastery matrix FormIDs | SKILL §6 L266-271 (#64, #65), Judgment (#81), Checklist (#120); perks-and-tomes L7-31 (#247, #248, #256) | TR: Suggestion Apprentice→Novice, Visions of Healing Novice→Adept: r95 G19 right/minor, r69 G20 right/major; 0/3 controls. FS: HalfCostPerk on Shadow Blast, cloaks, wisps (G25, G26, G47 right). Facts #17 class K/P (FS c3 L962 found it) | multi | The classifier sentence + the 5×5 matrix (as a table, ~8 lines) + "HalfCostPerk and MGEF MinimumSkillLevel must agree" |
| H7 | ChargeTime ladder: FaF single 0.25 × tier; Touch half; ritual 3.0 | SKILL §4 L179-181 (#35, FaF part), cost-and-magnitude L63-75 (#151, #152) | TR s1 D26/D27 (0.75, 3.0) r95 G20 right/minor, 0/3 controls; FS G23, G25, G26 (charge part of right/major groups). Facts #11 class L | multi, minor | FaF/Touch/ritual rungs as "typical; copy the comparable". **Drop "Concentration = 0 always"** (false, Hurt X6) |
| H8 | Tome value is a tier band; price off a same-tier, same-role tome; Weight 1 | SKILL §7 L281-289 (#69, #71), perks-and-tomes L60-92 (#253, #254) | FS G46/G45 right/minor (s1, s3; controls not_done); TR r95 G18, r69 G21 right/minor, 0/3 controls. RB s1 g40 / s2 g43 debatable minor (neutral). Facts #10 class L | multi, minor | Ladder table (5 rows) + "band, not a point" |
| H9 | Add the riders the comparable carries, with its magnitudes (Cremation, Electrostatic, Impact Stagger 0.25, Respite 50%) | SKILL §4 L197-205 (#43, #44), cost-and-magnitude L76-97, L112-136 (#41, #154, #157) | FS Shadow Blast rider s1 G26 right/major; Spark of Life Respite s1 G27 right/minor; Heavy Flame + Cremation s3 G47 right/major; Scarlet Bolt + staff Electrostatic riders U13 FS N20 right 10 / debatable 5. s1 Heavy Flame rider G24 debatable. Controls: not_done | multi, mixed | "Copy the comparable's full effect list, BaseEffect + Data" (3 lines). Keep the 0.25 / 5 magnitude warning only as an example of why to copy |
| H10 | Staff ENCH = clone of the tier-matched MR staff enchantment; staff WEAP keyed by `Nox_KW_Staff_<School><Tier>` | SKILL §8 L303-305 (#77), enchantments.md Staves L72-91 (#180) | RB s3 g33 right/major (exact clone of REQ_Ench_Staff_Destruction4_Frost_Aimed 10FCF6), c1-c3 not_done; RB s1 g14 / s2 g33 debatable. FS s1 staff ENCH G23 right/major (c1-c3 not_done). TR3: s3 reached the staff convention via magic §8 first (R2 L51) | multi, domain-led (weapon skill shares TR3) | The clone rule (2 lines). WEAP side stays in weapon skill; say so once |
| H11 | Never extrapolate across a family; enumerate MGEF first-class | SKILL Bulk pass L95-99 (#15-#17), bulk-pass.md (#130-#132) | TR s1 tagged all 17 Aid child effects (D13) r95 G24 right/minor, 0/3 controls. TR s3 skipped the MGEF sweep and lost SunDamage (neutral row) | single-multi, minor | 2 lines. Reconciliation itself belongs to the router |

**Joint-win note (R2, r3 §2):** H1, H2, H3, H9 sit inside FS4 / RB4, both classed **joint**: the router put the SPEL/MGEF records in scope (controls listed them and skipped as "Reqtificator handles it" or "low priority"), and this skill supplied *where to look* (the MR winner, the archetype exemplar) and, for summons only, the value. FS3 (REQ_NULL abilities → REQ_AbHide, 4/4 vs 0/4) was counted as a magic win in U7 and synthesis.md; R1/R4 removed it as **no in-game effect**. It is listed under Hurt X2, not here.

**Ledger "helped" rows demoted (not skill-caused or no effect):**
- **#46, RB Featherfall** (Malkifer's Elixir PerkToApply REQ_NULL_DLC2BlackBookNoFallingDamagePerk → REQ_AlterationGM_Featherfall; ledger: s1 g43, s3 g51 right). R2 strict attribution (R2 rot-below L112-113, L119): s1 = own probe via a cross-lane relay, s3 = own probe; s2 had #46 loaded and stripped the effect as "intentional design" (own wrong inference). So the rule did not cause the fix. The underlying fact is right and worth keeping, scoped: a REQ_NULL **PERK** is empty (147/154, U6), so a perk link to one is dead (see §5). #46 as written ("any REQ_NULL effect is a silent no-op") is the false fact in X2.
- **#105 / #260 / #267, TR** REQ_NULL_AbResistPoison → REQ_AbHide_FortifyPoisonResistance (ledger: r95 G22 right/minor, r33 G12 right/none). R1 relabels both **no_effect** ("same in-game result"). Counted in X2.

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | Absorb signature applied to a **beneficial** absorb (heal-other) | keywords.md Absorb row L94 (#209); SKILL §5 "copy the set from the archetype comparable" (#58) | FS s2 D71 (thinking L1530) copied Hostile + Detrimental + stagger from MR 005B7E onto Spark of Life: it now drains its target and aggroes followers, **G29 wrong/major**. s1n overrode the archetype reading, debatable G25 | no scope (facts #6: class L, in no skill) | Scoped row: "Hostile absorb: REQ_Absorb + ResistMagic + conditions. A beneficial / heal-other absorb keeps the mod's non-hostile flags; check what the effect does to its target first" |
| X2 | "All 8 vanilla resist MGEFs are REQ_NULLed, gutted, a silent no-op in game; re-point them" | resistance-map.md L3-7, L10-27 (#259, #261), L63-75 (#266, #267); SKILL §1 classify L85-87 (#13), §4 L206-210 (#46-#48), Judgment L335-340 (#84, #85), Common mistakes L385-387 (#99), Checklist L431-432, L449 (#116, #124) | **False fact.** U6/R7: REQ_NULL MGEFs keep their content; 0CAB90 is used by 41 live spells; 024315 vs 0008CF differ only in HideInUI and PowerAffectsMagnitude (R1). Effects: FS3 re-points in 4/4 runs are functional no-ops; **3 right/major groups lost** (r48 g19, r11 g33, r98 g54; R1). r11 g33's only functional change dropped Putrid Mass's working 500 DamageResist; r48 g20 removed a working 10000 Weakness-to-Magic (relabelled wrong/minor). RB s1/s2 magic lanes re-pointed, then reverted on a race-lane relay, leaving an empty ability (r26 g33 no_effect, r15 g31 debatable; process RB L58). TR r95 G22 / r33 G12: ledger rows labelled "helped" (#105, #260, #267), R1 relabels both no_effect ("same in-game result") | wrong fact | Replace with the per-type fact: "REQ_NULL on an MGEF/SPEL/ENCH means *unused by Requiem's own content*, not dead. Do not swap by name. Only PERK is routinely emptied (147/154). Probe: `housecarl_records` delta of the NULL form vs its Skyrim.esm original." Delete the re-point map |
| X3 | Split REQ_NULL rule: #84 "strip on items" vs #105 "replace on creatures", beaten by the router's "no REQ_NULL may remain / inert stubs" | SKILL Judgment L335-340 (#84, #105), Common mistakes L397 (#105); router rp#136/#137/#114/#53, rp#75 | FS s1 D76 cited the item half on a creature ability and deleted Putrid Mass's +500 armour: **G21 wrong/major** (controls left it, keeping the +500). The magic lane also cut magic resist 50→20 via router combat-resistance ×3 (D75). U10: the lead merged the lanes silently; s2/s3, where the lead relayed the race lane's decision, were judged right | conflict between skills + wrong fact | With X2 fixed the rule is one line: "never remove or replace a working REQ_NULL MGEF on any carrier". One owner (this skill or the router), linked from race/npc/LL |
| X4 | Player-ladder anchors applied to **NPC/boss signature spells** | SKILL §4 L192-193 (#40), #32; enchantments.md L101 "EXPL Damage 0" (#182); #44/#58 arcane comparable | RB s2 cut Adabelle's explosive spell 300 + 200 → 80 + 0: **g31 debatable/major**; s1 Malkifer strike 100 → 30, knockdown removed: g36 debatable/minor. Controls left them (c1 g42, c2 g29, c3 g41 not_done); the reference **buffs** them | no scope | Scope the ladder to player-learnable / tome-taught spells. "An NPC or boss signature spell keeps its authored magnitude; add the MR riders, ManualCostCalc, NoAbsorbOrReflect." State that judges split on boss magnitudes |
| X5 | No rule for **NPC-only spells** at all | SKILL §1 classify L66-88 (no NPC-spell branch); #18/#43/#157 did not fire | RB N5: ManualCostCalc / NoAbsorbOrReflect / IgnoreResistance on 10 NPC attack spells, never made by any run (4/6 judges right); RB s1 g35, s3 g46 not_done/major (controls also not_done). 11 enemy spells untouched by every run (U13 RB) | no scope (gap) | Add an NPC-spell branch (see §6) |
| X6 | "Concentration ChargeTime = 0, always" | SKILL §4 L179 (#35), Checklist L414 (#110), cost-and-magnitude L67 (#151) | **False** (R5 live): 17 Requiem/MR Concentration spells have ChargeTime > 0, e.g. 03DA6B 0.625. FS s1 broke it on Grasp Soul, GRVEShadowGrab (0 → 0.625 from its comparable): r48 G26 right/major, identical write in s2/s3/s1n (right/major ×2, debatable). No damage, but the absolute contradicts the skill's own "live read is the authority" | wrong fact | "Copy the analogue's ChargeTime; FaF 0.25 × tier is the common case" |
| X7 | Bulk pass widened scope to magnitudes with no anchor; the cloak-tick lock made a cut stick | bulk-pass.md (#130, #17) scope + keywords.md cloak row (#226, REQ_NoMagnitudeScaling) | TR s1 halved Draining Shroud and locked it with REQ_NoMagnitudeScaling: **r95 G27 wrong/major**. Ledger TR: the cut came from no skill line; the lock came from #226. Thorns 100 → 10 r95 G28 wrong, but R4 re-read relabels it right (matches the MR patch's own tooltip effect) | no scope (missing anchor rule); single-judge, PLAUSIBLE | Add: "change a magnitude only against a same-tier MR comparable; a tooltip or parent value is not an anchor; never add REQ_NoMagnitudeScaling while lowering a value" |
| X8 | resistance-map re-point vs race creature-traits "add REQ_Trait_Resist only if the race ships none" | resistance-map.md (#259-#267) vs race#58/#124 | RB s1/s2: race lane counted NULL resistance abilities as "own" and emptied them; magic lane had re-pointed them, then reverted (r26 g34 no_effect, r15 g32 debatable). s3 decided per record: g49 right minor. Lane relay "remove, don't re-add" confirmed (U10 L38) | conflict between skills | Moot once X2 is fixed: the NULL ability works, leave it. State that in race and here |
| X9 | Scarlet Bolt damage halved to 20 on the same 240 frame | SKILL Judgment "Uniques" (#86, #87) | FS s1n D69 G23 wrong/major; the same frame judged right (s1 G23) and debatable (s2 G30). R2: domain method + own comparable choice; judge variance | weak scope (no floor) | Add: "do not reduce damage below the same-tier MR comparable" |

**No stop-to-ask gate exists in this skill** (rule inventory note), so none of the 4 stops came from it. **1.x tool names** (housecarl_read_record, cross_plugin_query, batch_record_detail, create_record/set_field/bulk_apply): 0 refused calls in all tasks (process SYNTHESIS L59); no magic-specific write-shape refusal found. Listed under Unnecessary as obsolete, not as harm. *Unverified link:* RB's 7 lanes that sent a refused `project.depth` may echo this skill's "depth=2 / depth=4" instructions (process RB L36); not traced.

## 4. Unnecessary

Lines are approximate. "Untested" = never exercised by the six tasks, not useless.

| # | Content | Source | Why | Lines | Action |
|---|---|---|---|---|---|
| N1 | Freshness probe + live-winner doctrine + "never re-point when RftI wins" | SKILL First step 1 L46-64 (#6-#10), Notes L453-461 (#125, #126), §3 L166-169 (#30), Common mistakes L398-401 (#106) | duplicated ×11 skills + router; noise in every task (absolutes SYNTHESIS "generic craft") | ~35 | delete; router owns. Keep only H2's one line "for spells, the MR winner" |
| N2 | Three-layer split, RFTI_All_* never stamp, Nox scripts only for special mechanics | Overview L29-42, §5 L261-262, Judgment L326-333, Common mistakes L392-394, Checklist L442 (#4, #5, #63, #82, #83, #102, #103, #121); keywords.md L6-38 (#200-#202) | noise: TR no control stamped RFTI_All_* either (synthesis.md); repeated 6+ times | ~60 | 2 lines in the upstream/downstream statement (§6) |
| N3 | Common mistakes section | SKILL L349-401 | every item restates the body (scalars-only ×8: #19, #37, #38, #89, #90, #111, #139, #192; flags-union ×4: #22, #119, #141, #191; "already-correct needs a diff" ×4: #20, #52, #96, #143) | 53 | delete |
| N4 | Checklist | SKILL L403-449 (#107-#124) | restates the workflow; gates are the least-evidenced kind overall (17/161 used, U8) | 47 | ≤8 lines, only items that are not in the body |
| N5 | Bulk-pass protocol + bulk-pass.md | SKILL L90-115; bulk-pass.md (114) | reconciliation duplicates router rp#108; 1 helped row (H11) | ~140 | keep H11's 2 lines + "patched = magnitudes and riders too, not just cost" (1 line) |
| N6 | housecarl-recipes.md | 233 lines | read by 7 runs, **0 rules used**; 1.x call shapes | 233 | delete; any tested 2.x shape goes to houseCARL docs |
| N7 | mr-content-survey.md | 168 lines | **never read** (0 runs). Untested | 168 | delete; its damage-type table already lives in keywords.md |
| N8 | worked-examples.md | 92 lines | **never read**. Untested | 92 | delete; keep "a self-styled new school is reclassified into one of the five" as 1 line |
| N9 | mgef-conditions.md | 71 lines | **never opened**; FS s1 copied conditions by CopyFrom per SKILL #61 instead. Untested | 71 | keep ~5 lines: "copy condition rows from the comparable; never hand-author"; undead triad note |
| N10 | spell-archetypes.md | 173 lines | read 7/4 runs, 1 neutral row (#272 creature innates, RB s1 g37 debatable, s2 g34 right; c2 right unaided) | 173 | compress to an archetype → comparable-EditorID table (~30 lines) |
| N11 | keywords.md subtype tables + Nox_KW vocabulary | keywords.md L68-166, L264-309 | 1 rule used (#208, FS s3 G53 right/minor); controls found element keywords unaided (facts #32 K/P) | ~145 | keep the behaviour signature table (absorb, cloak, damage types, taper) + master suffixes; drop the vocabulary list |
| N12 | Survey trivia | SKILL L24-27, L173 (99.3%), L242 (915 scanned); keywords.md L267 (106 Nox_KW); enchantments.md L7 (1029 ENCH) | no decision depends on a count | ~12 | delete |
| N13 | Generic output craft | SKILL §9 L310-316 (#80), Notes L471-472 (#129); housecarl-recipes (#186, #195-#198) | masters read-back, new plugin, "don't re-issue a successful write": Opus does these unaided | ~15 | delete |
| N14 | Resistance replacement map | resistance-map.md L28-75 | built on the false "gutted" premise (X2); helped nothing real | ~50 | delete; keep 1 note: "AbShow_Resist* are display-only" (untested) |
| N15 | Rider deep-dive: tier resolution, cosmetic vs mechanical (`_Desc_`, Fame taper) | cost-and-magnitude L137-203 (#158-#167); SKILL L360-364 (#91) | read by 7 runs, 1 ledger row (#162, FS, neutral), none helped or harmed. Untested trap | ~70 | keep 3 lines: "verify a rider's Archetype/ActorValue; `_Desc_` effects are tooltip carriers" |
| N16 | Per-school cost ladders and delivery multipliers | cost-and-magnitude L27-62 (#150 used once in FS G26) | values came from live reads in every win (R2) | ~35 | keep as a cross-check table, ≤10 lines |

**Untested domains** (keep a short version; the six tasks cannot judge them):

| Domain | Skill text | Exposure in the eval |
|---|---|---|
| Scrolls (SCRL) | SKILL §7 L291, Notes L462-464, perks-and-tomes L94-99 | none scored |
| Shouts (SHOU) | Notes L464-465 | SHOU in FS (2) and RB (5), never scored (U8) |
| Alchemy MGEF design | enchantments.md L113-127, Notes L466-470 | ALCH thin (6 records), owned by consumable skill |
| Creature innates (`REQ_Creature_*`) | SKILL L73-77, spell-archetypes L33-69 | RB only, neutral |
| Weapon / apparel enchant ladders | enchantments.md L11-70 | RB neutral/debatable; FS ENCH debatable (G26/G30/G56) |
| Elemental projectile hit (AMMO→PROJ→EXPL) | enchantments.md L92-112 | 1 AMMO each in FS and RB |
| Crafting COBJ (staff/scroll craft), pre-enchanted gear value | SKILL §8 L301-308, mr-content-survey §5-6 | none |
| New MGEF creation, multi-spell tomes, standing-stone abilities, mage armor/ward riders, Illusion Break riders | housecarl-recipes F, perks-and-tomes L91, SKILL L69-72, cost-and-magnitude anchors | none (Respite once, FS G27 minor) |
| Resistance potion lane | resistance-map L58 | none |
| Downstream lane (bake RFTI_All_* magic perks) | forbidden by #63/#82 | 0/42 runs went downstream |

## 5. Corrected facts that belong to this skill

| Fact (as it should be written) | Status | Source |
|---|---|---|
| REQ_NULL MGEF/SPEL/ENCH = "unused by Requiem's own content", not dead. MGEF: 274 kept, 0 archetype changes, 0 scripts removed; 0CAB90 used by 41 live spells; 0F3F0A was edited **up** | verified live | U6 §1, R7, r3 §3 |
| A REQ_NULL → REQ_AbHide resist swap changes only HideInUI and PowerAffectsMagnitude (FS3 = no-effect) | verified live | R1, R4 |
| PERK is the only type Requiem routinely empties (147/154): a REQ_NULL perk link (e.g. BlackBook no-fall) is dead and worth re-pointing | verified live | U6, r3 §3 |
| Concentration spells are not always ChargeTime 0: 17 have > 0 (03DA6B 0.625) | verified live | R5 |
| The Reqtificator output (RFI) holds **no** Spell, MagicEffect, Book or COBJ records: all magic work is upstream input; nothing here is "left to the build" except RFTI_All_* actor perks | verified live (FS r41 L1418) | coverage SYNTHESIS #4 |
| RFTI_All_* rescaling perks are build-assigned actor perks | code-consistent (R12 NPC perk path 20/20); the magic RFTI_All_ IDs (962799, 962798, 682FB5, AD3977) not individually checked | R12, R5 |
| MR summons: Duration 15 at every tier | multi-judge + 2 runs' own live probes (RB s2 L1215, s3 L1466); not re-read in round 3 | r3 §3 "read-backed", R10 fix 6 |
| Beneficial absorb stays non-hostile | single judge (FS s2 G29); no run derived it | facts #6 |
| Destruction base damage identical across elements at a tier (T2 30, T3 32, T5 64) | skill-only (dated 2026-07-18); not re-checked; scope to player spells | SKILL L192 |
| EXPL Damage 0 on Requiem explosions | skill-only; applies to Requiem's arrow/spell explosions, not a mod boss's authored explosion | enchantments.md L101 |
| Clothing AR 0 | **not this skill** (armor / its keywords.md); house convention per r3 CRITIC | — |

Router-owned facts this skill must not restate: the Reqtificator boundary line, SpellList inheritance (R9 line, NPC skill), lockpicking, weapon/armour rescale.

## 6. Rewrite brief

**Purpose.** One page that tells Opus *where Requiem's magic truth lives* and *which few Requiem conventions it would not guess*, for player spells, NPC spells, enchantments and staves. Values come from live reads; the skill names the comparable and the traps.

**Must keep (verbatim facts, conditions, probes):**
1. Authority: spells, staves, summons derive from the **live MR winner**; Requiem.esp alone is the wrong layer (H2).
2. The method: classify by behaviour → read the archetype exemplar (keywords, flags, effect list, conditions) at full depth → diff and add. Copy BaseEffect + Data of every rider (H3, H9).
3. ManualCostCalc with the cost (flag union) (H2).
4. Summons: Magnitude 0 / Area 0 / Duration 15 at every tier + "probe one MR summon first" (H1).
5. HalfCostPerk = school + tier; matrix table; must agree with MinimumSkillLevel (H6).
6. Signature table: absorb (with the beneficial exception), cloak tick, damage types incl. Sun, taper; master suffixes `:Requiem.esp` / `:Requiem - Magic Redone.esp` (H3-H5).
7. Typical ChargeTime and tome bands as "copy the comparable; typical values" (H7, H8).
8. Staff ENCH = clone the tier-matched MR staff ENCH (H10).
9. REQ_NULL: per type, PERK dead, everything else live (§5, X2; the Featherfall fix shows the PERK case matters, though no rule caused it).
10. Never extrapolate across a family; patched = magnitudes and riders too (H11).

**New items:**
- **NPC-spell branch** (X4, X5): an NPC/boss signature spell keeps its authored magnitude; add MR riders, ManualCostCalc, NoAbsorbOrReflect, IgnoreResistance as the comparable shows; include it in the enumeration (RB N5).
- **Magnitude anchor rule** (X7, X9): change a magnitude only against a same-tier MR comparable; never below it for damage; never add REQ_NoMagnitudeScaling while lowering.
- **Beneficial-absorb exception** (X1).
- **Upstream/downstream statement** (maintainer framing), 2 lines: RFI never writes Spell/MGEF/BOOK/COBJ, so all of it is upstream input; RFTI_All_* actor perks are the build's in an upstream patch and may be baked in a downstream one.
- Cross-refs only (no ownership): R9's SpellList-on-Stats line lives in the NPC skill (NPC spells ride on SpellList); the extra-slot `MagicDisallowEnchanting` gap (MC, U13) lives in the armor skill.
- Probes as queries, not prose: "NULL form vs original delta", "MR summon Effects", "archetype exemplar keywords/flags".

**Delete:** N1-N8, N12-N14; the re-point map; Common mistakes; most of the Checklist; all 1.x call shapes; trivia counts; magic#35 as written; the "gutted / silent no-op" lines (#13, #46-#48, #84, #85, #99, #116, #124, #259-#267).

**Target length:** SKILL.md ~140 lines + one reference (~150 lines: signature table, summon/charge/tome/cost cross-check tables, archetype → comparable EditorIDs, untested-domain stubs of 2-5 lines each) ≈ **290 lines vs 2,267 today (~13%)**.

**Where content lives:**

| Content | Home |
|---|---|
| MR authority, method, signature table, summon/tome/charge/HalfCostPerk, NPC-spell branch, anchor rule, staff ENCH clone | this skill |
| Freshness probe, live-winner doctrine, enumerate → route → reconcile, Reqtificator boundary fact | router (requiem-patching) |
| REQ_NULL per-type fact | Requiem profile (one owner), linked from router, race, npc, LL, magic |
| RFTI_All_* / upstream vs downstream, invariants with probe queries | Requiem profile |
| Any ARR house convention (e.g. MR patch addons in the list, ARR's own magic tweaks) | ARR list layer |
| Call shapes, `depth`/projection grammar, write-shape recipes | houseCARL docs / review-type skill |
| WEAP staff frame, ALCH/INGR records, AMMO/PROJ links, tome placement | weapon / consumable / ammo / LL skills (cross-ref only) |

## Verification (adversarial check, 2026-10-30)

**Changed:**
- **H11 (REQ_NULL PERK / Featherfall) moved out of Helped** into a "demoted" note. R2 rot-below L112-119 attributes the Featherfall fix to own probe in s1 and s3; s2 had #46 loaded and still stripped it. The PERK fact stays in §5 and the rewrite brief. Old H12 renumbered H11; brief and N5 references updated.
- **H2 source:** added #18 (definition of done), the rule the ledger ties to FS s2 G31 right/major. It was the one helped rule id missing from the file.
- **H3:** "s2 G28 (reference wrong)" now reads right/major for the candidate, checked in r11 `judgement.md` row 29 (candidate right, reference wrong).
- **X2:** named the TR ledger "helped" rows (#105, #260, #267) that R1 relabels no_effect.
- **N15:** "no ledger row" corrected to 1 neutral row (#162, FS).
- **Header:** G numbers are 0-based `judgement.json` indices, as in the ledgers (checked on r11: G29 = Spark of Life = md row 30).

**Checked, held:**
- Every magic rule id with a helped or harmed row in `drill/absolutes/ledger-*.json` (37 ids by first-id parse; grouped ids such as RB #148/#42/#33/#35/#22/#60/#28 included) appears in the file. Harmed: #84, #105, #209 (FS), #40/#182/#32, #44/#58 (RB). All are in X1, X3 and X4.
- No Helped item rests on a no-effect edit: FS3 (REQ_AbHide re-points) is already excluded, per R1/R4. Clothing AR 0 is not in this skill (armor skill; r3 CRITIC house-convention caveat noted in §5). Bows ×4 is not in this skill.
- The false facts in Hurt match r3 INTEGRATED §3: REQ_NULL MGEF live (L63-64, L89, L102) and magic#35 false (L75, L97). R1 lost groups r48 g19, r11 g33, r98 g54, plus r48 g20 relabelled wrong/minor, all confirmed in R1 L37, L59-61.
- R2 confirms: c1n priced from Requiem.esp after reading MR 240 (FS L82). Skill text supplied numbers only in FS4/RB4 (the 15 s summon) and FS3 (no effect). RB-S boss-spell harm is domain, out of scope (RB L42, L136). Putrid Mass is magic #84 plus router (U7 L93).
- None of the Unnecessary items is load-bearing in U7/R2 deciding points. Magic text shows up in FS3 (no effect), FS4, RB4 and TR3 (§8, kept in H10). N9, N7 and N8 have no ledger rows. N6 (housecarl-recipes) has none either, and its #184 is noise. The `project.depth` refusals are attributed to model/houseCARL query shape (process rot-below.json), not to this skill.
- Not re-checked: line numbers inside the skill files, and the per-reference read counts (taken from U8 as given).

**Final counts:** Helped 11 (was 12), Hurt 9, Unnecessary 16 + 10 untested domains.
