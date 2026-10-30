---
skill: requiem-perk-assignment
date: 2026-10-30
inputs: scoring/synthesis/synth-work/skills/requiem-perk-assignment/ (SKILL.md 294 lines + 5 references 573 lines + index 28 = 895); drill/absolutes/requiem-perk-assignment.json (176 rules) + ledger-{falkreath-shades,rot-below}; drill/facts/SYNTHESIS.md; drill/r2/{U2,U6,U7,U8,U9,U10,U13,U15}; drill/r3/{INTEGRATED §3,R2,R5,R9}; scoring/analysis/*/analysis.md; scoring/judgements/{r26,r15,r54,r48,r98}
---

# requiem-perk-assignment: distillation

Rule ids are `perk#N` = `requiem-perk-assignment#N` in `drill/absolutes/requiem-perk-assignment.json`. Run ids: FS = falkreath-shades, RB = rot-below, TR = triumvirate, AC = animated-carriages. Judgement group numbers are 0-based indexes into each run's `scoring/judgements/<rid>/judgement.json` (as U13 and drill/consistency use). Note: `ledger-rot-below` numbers its groups 1-based (its "s3 g3" = r54 g2 here).

> [!summary] Verdict
> Small real value, and none of it needs a separate skill. One method (derive an NPC's kit from a Requiem comparable's source perks: union over the loadout; casters get school nodes by spell kit plus the survivability set; hybrids stack) helped in 2 of 7 skill runs that used it (RB s3 r54 g2 and FS s3 r98 g2, right/major, single judge each, joint with npc#223 and the router; the other 5 were debatable). One rule clearly hurt (the race × tier creature superset put weapon perks on unarmed FS creatures, 2 runs). The rest went unused: 10 of 176 rules used, and 3 of 5 references never opened. **Fold about 50 lines into the NPC skill's perks section / the Requiem profile. Move tool recipes to houseCARL. Delete the rest.**

## 1. What it is

Derives which existing Requiem player-tree perks an NPC should carry (from gear, spell kit and tier, matched to Requiem's own ladders). Writes them as rank-chain PerkPlacements and dispositions a mod's own PERK records. It never hand-places Reqtificator chassis perks.

**Exposure (U8, metrics `skill_file_reads`):** loaded in **10 of 21** skill runs: FS 4/4, RB 3/3, TR 3/4 (no TR s3). **Not loaded on AC**, the task where NPC perk kits mattered most (60 Stats-only fighters, fighter kits), and not on MC or FQ. SKILL.md was read in 10 runs. `mod-perk-disposition.md` was read in 3 runs (FS, RB), `boundary-and-write-shapes.md` in 1 (RB). `caster-perks.md`, `class-and-creatures.md` and `equipment-perk-map.md` were read in **0** runs. Rules: 10 used, 109 read with no evidence, 57 never read. Ledger: **1 helped row** (RB s3, citing 5 rule ids, which U8 counts as "5 helped"), 1 harmed rule in 2 rows (perk#146, FS s1 + s1-nothink), the rest neutral. U8 L219 caveat: perk#146 sits in `class-and-creatures.md`, which no run opened; the text the runs actually read is the SKILL.md copy (perk#34, #72). In U7/R2 it decided **none of the 17 deciding points**.

## 2. Helped

| Item | Source | Evidence | Strength | Rewrite must keep |
|---|---|---|---|---|
| **Derive a kit from a live Requiem comparable's source perks**: union over every carried weapon, the universal floor (WeaponMastery/HandToHand), hybrids stack melee + school, casters get school nodes matching the actual spell kit plus the shared survivability set. Never author a new PERK record | SKILL.md Workflow 1-2; perk#13, #37, #43, #33, #3 | RB s3 (r54 g2) **right/major**: role kits copied from Ancano 01E7D7 / Morokei 0F496C (the judge verified both), melee Focus + REQ_Trait_Damage140/150. RB s1 (r26 g16) and s2 (r15 g3) followed the same rules and were judged **debatable/major** (Mastery split and scattershot creature kits, see Hurt). **Controls:** c1 and c3 gave no skill perks at all, believing the Reqtificator does perks (facts #1, RB c1 L2193). c2 (g5 in U13) debatable put one draugr pack on everyone. FS s3 (r98 g2) **right/major**: per-weapon / per-school packages; but the same move on FS s1 (r48 g3), s1-nothink (r93 g3) and s2 (r11 g9) was **debatable/major**, and all 4 FS controls were not_done. Ledger `ledger-rot-below.json`, analysis RB L131, U13 RB R12 (skill only, 1/6), consistency/rot-below "split" (s3 right, s1/s2 debatable) | single-judge right per task (RB s3, FS s3), 2 of 7 skill runs; **joint** with npc#223 ("copy the comparable's set") and the router's NPC_ row. Values came from live comparables, not skill text (R2 pattern) | The method in 4 lines: read the comparable's **source** perks; union over loadout; caster = school nodes by highest spell + survivability; hybrid stacks. No tables |
| **Lane 1 boundary: the patch writes the hand-picked source perks; the build adds only the generic chassis** ("source 5 → build 50") | SKILL.md Overview L24-35; perk#5, #6, #88-#92 | Facts #1 (critical, class **W**): every control on AC and RB believed the Reqtificator supplies NPC perks/kits (AC c1 L732, c2 L80, c3 L745; RB c1 L2193). FS: 4/4 skill runs made hand NPC perks, 0/4 controls (U13 FS N5; the skill-held column names "perk-assignment lane 1"); judged quality was mixed (1 right, 3 debatable, row above). **But** on AC the win came through the npc skill, since this skill was not loaded | not attributable to this skill alone; same fact sits in router + npc `perks.md`. Code-proven (R12: NPC perks/spells predicted 20/20) | Verbatim, once, in the Requiem profile: "The Reqtificator adds only a generic ~45-perk chassis. It never copies an archetype kit and never sets template flags. A combatant's skill perks are the patch's job." |
| Reference the origin FormID; the live winner delivers Requiem's re-authored perk | perk#48, #69, #94 | RB decision_map cites #69; no judged effect isolated | weak (cited, not decisive) | One line |

**Not a help, though often cited:** the Mastery-gate rule (perk#44/#67/#108) was neutral in the ledger and is contested by the judges (see Hurt).

## 3. Hurt

| Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|
| **"Creature → race × tier superset; copy the whole shape, don't trim it"** | SKILL.md Workflow 1 L127-131, Common mistakes L248-249; perk#34, #72, #143, #146 | FS s1 (r48 G3, D156-157): the 14-perk draugr weapon kit went onto the unarmed Walking Mound and its summon. FS s1-nothink (r93 G3, D48): Marksmanship onto the Putrid Masses. Both **debatable/major**: "perks that never fire on an unarmed creature, and no `REQ_Trait_ArmorPenetration`". RB s1 (r26 g16, debatable/major): "broad all-weapon focus kits (mace, axe, hammer, bow) for draugr and skeletons", while "the reference's melee kits follow each NPC's actual weapon": the judge's contrast is the opposite of perk#34 ("not by the individual carried weapon"); the causal link is **inference** (no trace cite checked). consistency/falkreath-shades names this rule as the split cause. AC controls got creature damage right unaided, via AP traits (facts #25, AC c1 L738/L1381) | **missing scope**. SKILL.md L128-130 does name "trait-only kits (giant, troll)", but gives no test for choosing them (unarmed / no weapon), and Common mistakes L248-249 then says copy the superset. The fuller counter-case sat in `class-and-creatures.md` L87-91, never opened | Scoped rule: "Armed humanoid-skeleton creature (draugr, skeleton, falmer) → copy the same-race same-tier comparable's kit. **Unarmed creature** → a trait-only kit: `REQ_Trait_ArmorPenetration*` (source-carried) + its spells, no weapon-skill perks." |
| **"Never hand-place chassis; no forbidden prefix anywhere in the list"**, stated with no upstream scope | perk#1, #7, #65, #80, #96-#101, #107 (absolutes note: assumes upstream, never says so) | TR-L: the Primal summons' baked RFTI perks (from the Triumvirate MR patch, an input) were stripped: s2 r69 g1 and nothink r33 g3 wrong/minor, s1 r95 g4 no_effect. U10 TR L87: these actors lanes had loaded this skill and the npc skill (perk#65, npc#259) and the router (rp#42). R2 TR-L assigns the strip to **router text relayed through the briefs** (rp#42); the npc and perk skills restate it, so the share **cannot be separated**. This skill's share is therefore secondary. (Framing note, not a harm: FS U13 finds ~80% of the reference's units are the Reqtificator layer this skill forbids (SKILL.md L28-32, L230-234). Under the upstream stance that is a valid omission, not a wrong call.) | **no scope** (upstream only, unstated) + **conflict** with inputs that already carry a bake | State the two lanes, not a ban: "Upstream patch: leave the chassis to the build. Downstream (Reqtificated) patch: bake it. A chassis already on an **input** plugin is input; keep it." |
| **Trait-bridge exception routed away** ("stays with npc/race"; chassis list includes racial `RFTI_Trait_*`) | perk#7, #55, #91, #100, #147 | The briefs restated "never hand-stamp" and dropped the exception that perk#7/#91 and npc#79/#233 state (U10 TR L36). TR nothink withheld trait perks, "per your instruction" (r33: no credit; r95 g1 and r69 g3 got right/major for the same move). RB s2 hand-added RFTI_Trait_* creature perks and got **right/major** (r15 g4); RB s3 refused them (U13 RB R11, "because of the contradiction"). FS s1 withheld them (no verdict effect, U10 FS). R2 TR2b: the withholding in TR s3 and nothink was caused by the **router** line | **conflict between skills** (router vs npc bridge vs this skill's chassis list). This skill states the exception (#7, #55, #91), but its Common mistakes L230-233 lists "racial/state trait perk (draugr 031285)" as chassis with no exception at that spot. Share: minor, contributing | One line, in the profile, with its probe: "Racial trait perks come only from `ActorAssignmentRules_<plugin>.conf` race lists. A custom race with no entry gets nothing, so hand-place the analogue race's trait perk." |
| **"NPCs never carry `REQ_<School>_Mastery_*` gate perks"** | perk#44, #67, #103, #108 (absolutes note: basis is "not one sampled") | RB s1 r26 g16 and s2 r15 g3 marked the casters down for missing Mastery perks; consistency/rot-below names this rule as the s1/s2 split cause. The r26 judge's own live read of EncWarlockNecro07Boss lists **no** Mastery perk, but notes the run set the mod spells' HalfCostPerk to Apprentice/Expert Destruction, which its own casters then lack. The r54 judge read Ancano/Morokei (none) and graded right. ARR's handmade reference gives every caster Mastery perks (U2 RB #4: "genuinely ambiguous"). The skill also contradicts itself: Judgment L226-227 says "a few source-won boss templates show the opposite (mastery perks, no chassis)" | **contested**, not proven wrong: judge vs judge; plus an internal inconsistency. Requiem's own casters back the rule; ARR's patch does not follow it | Keep it as a Requiem fact with its probe ("read the comparable caster's source perks"). Add the ARR line: "ARR's own patches give casters Mastery perks; ask whether to follow that house convention." |
| Caster bosses left without a kit: Richter (FS) and Adabelle (RB) | gap; possibly Judgment perk#62 "don't fix a lean comparable" | FS s3 (r98 g3) **not_done/major**: "Richter's SpellList category is local … perks on the record are live". The lane said "comparable carries no perks" (U10 FS L92). RB s3 (r54 g3) **not_done/major**: Adabelle's four level-200 sorcerer forms (and 2 summons) untouched while the other casters were kitted (consistency/rot-below "s3#3"). `caster-perks.md` was unread in both | **omission**. The cause is **inference** for Richter (no skill line covers Stats-without-SpellList, R9) and **untraced** for Adabelle; not attributable to this skill's text | Add the R9 line (§6). Make "a lean comparable" apply only after checking which template flags the record owns |
| Obsolete 1.x tool shapes (`housecarl_read_record`, `cross_plugin_query`, `bulk_apply` composes, `housecarl_resolve`) | SKILL.md L48-52, L83-84, L172-177, L275; boundary L95-138 | 0 refused calls on 6/6 tasks (U15, drill/process), so $0 | **tool shape** (no measured harm) | Move to houseCARL (see §4) |

**No measured harm** from the stop-to-ask gates here (perk#25, #52, #60, #171, #174, #176). Mod-perk questions ended up in final reports, not stops (FS Death Strider, RB Adabelle's Blessing).

## 4. Unnecessary

| Content | Source | Why | Lines | Keep short / delete |
|---|---|---|---|---|
| Freshness probe (instance + Iron Sword conflict tree) | SKILL.md First step 1, L39-44; perk#9-#11 | **duplicated** ×11 across the skills | 6 | delete (router/profile once) |
| Mod-PERK disposition: bulk queue, 3-way rule, calibration table, reconciliation | SKILL.md L80-92, L183-195, checklist L260-262; `mod-perk-disposition.md` | **noise**: FS (Death Strider), RB (Adabelle's Blessing) flagged with no judged effect, TR not judged (analysis FS L115, RB L133, TR L143). Reconciliation **duplicates** the router's. Fold (case 3) **untested** | ~75 | keep short (2 lines: "list the mod's PERKs; leave hidden plumbing; report playable balance-bearing ones, with the overlap named") |
| NPC-lane bulk protocol + family-extrapolation list | SKILL.md L94-111 | **duplicated** (npc skill's enumeration) + generic | 18 | delete; keep "derive from the matching weapon's ladder" as 1 line |
| Equipment → perk matrix, ladder tables, FormID lists by tree, per-weapon variation | `equipment-perk-map.md`; SKILL.md L135-147 | **untested** (0 reads; only #36/#37 cited from SKILL.md, neutral). Values are read live anyway (R2) | 154 + 13 | keep short: the 3-term formula (weapon line + defence line by shield/armor + floor) and "read the matching `REQ_Bandit_Template_<Weapon>_<tier>` ladder". Drop the FormID tables |
| Caster tree tables (FormIDs per school), tier table, dragon priest recipe, MagickaOffset | `caster-perks.md` | **untested** (0 reads). The one caster win (RB s3) came from live reads of Ancano/Morokei | 132 | keep short: the comparable list (EncWarlock<NN>Template<School>, dragon priests, Ancano/Morokei) as probes; delete the tables |
| Class signal essay (CLAS schema, 4,229 Dremora placeholders, precedence rule, AutoCalc split) | `class-and-creatures.md` L13-67; SKILL.md L63-65, L211-214, L222-225 | **untested** + **generic craft** (equipment over class is the model's default; RB s2/s3 fixed draugr classes without it: r15 g5, r54 g9 judged right) | ~70 | keep 1 line: "class is corroboration; on Lvl*/templated actors it is filler" |
| Rank mechanics (one PerkPlacement per rank FormID, Rank=1) | SKILL.md L164-170, L239-240; boundary L84-91 | **untested**; Skyrim-generic | 15 | move to houseCARL (write-shape doc) |
| houseCARL recipes, `references=` vs `where=`, resolve-verify, masters read-back | boundary L93-143; SKILL.md L275-279; perk#82, #83, #105, #136 | **obsolete tool shape** / **generic craft** / tool property | ~60 | move to houseCARL |
| Routing section, Notes, checklist | SKILL.md L197-207, L256-294 | Checklists are rarely cited across the set (U8: 23/135). Routing **duplicates** the router | ~50 | delete |
| Before/after proof detail (45-perk enumerations), source-won boss caveats | boundary L26-43; caster L129-131 | evidence prose; the conclusion is the only part used | ~25 | keep 1 line (the 5 → 50 fact) |

**Untested domains (keep a short version; untested is not useless):** warrior ladders by weapon (bow / crossbow / dual-wield / 2H Focus depth), tier-depth counts, dragon priest / vampire hybrid shapes, Illusion casters, follower perk scaling, folding a mod perk onto a Requiem node, playable-perk rebalance questions. None of the six tasks had a humanoid warrior roster, except the RB guards and draugr.

## 5. Corrected facts that belong here

| Fact | Status | Source |
|---|---|---|
| The Reqtificator stamps only a generic chassis (RFTI_All_*, Nox mechanics, RFTI_Ench_*, playable-race perks). It never copies archetype kits and never sets template flags (0/3,770) | **verified code + RFI** | U6, R12 (NPC perks/spells 20/20) |
| REQ_NULL **perks** are empty (147/154). The 7 with entries include REQ_NULL_DLC2DragonAspectHeadPerk (13). Other REQ_NULL types work, so the skill's pointer to the all-type "strip on sight" doctrine (perk#74, #102) is wrong outside PERK | **verified live** | U6, r2 CRITIC #3, r3 INTEGRATED §3 |
| After stripping REQ_NULL perks, check what kit remains: perks and spells inherit **only** via the SpellList flag, so a Stats-only templated combatant owns its own (often empty) list | engine fact; outcome **multi-judge** (AC r50 g1 major; FS r98 G3) | R9 |
| `REQ_Trait_ArmorPenetration25/50` (0FAB0A/0B) and the giant's `…75` are **source-carried** (a patch may write them). The build's AP perks are `RFTI_All_*` AD394A-E | **verified code/config** | R5 (corrects npc#60) |
| Racial trait perks come only from `ActorAssignmentRules_<plugin>.conf` race lists (77 fixed race FormIDs). A custom race gets none | **verified code + RFI** | U6 7c, R5 (rp#203), R12 |
| Unarmed creature damage comes from AP traits, not weapon-skill kits | **multi-judge** (FS G3 ×2) + control knowledge (AC) | facts #25 |
| Requiem's own NPC casters carry no `_Mastery_` gate perks (Ancano, Morokei, EncWarlockNecro07Boss) | judge live reads (r54, r26), not re-read by the drill. The skill's own L226-227 admits "a few source-won boss templates" carry mastery perks, so "never" is not an absolute. **ARR's reference gives them: house convention**, effect unverified (HalfCostPerk on NPCs) | U2 RB #4, r15/r26/r54 judgements |
| Mage vendors carry small kits (Farengar 013BBB: 5 perks, 13 spells) | **verified live** | R5 (npc#172) |
| "Requiem turns AutoCalc OFF on every actor it hand-perks" (perk#63, #134) | **unverified as an absolute**: 22/86 Requiem summons keep AutoCalc | R5, U4 |
| REQ_LEGACY_MageArmor50/70 as caster proxies | **contested** (r15: compatibility leftovers; r26 read MageArmor70 on EncWarlockNecro07Boss) | judgements r15, r26 |

## 6. Rewrite brief

**Purpose.** No standalone skill. One **NPC perks section** (in the npc skill or the Requiem profile) that answers three questions: does this combatant need a kit from the patch (ownership); where do I read it (the comparable); and which shape traps to avoid.

**Must keep (verbatim facts, with their probes):**
1. Boundary: "The build adds only a generic chassis. It never copies an archetype kit or sets template flags. Skill perks are the patch's job." Probe: read a comparable's `plugin="Requiem.esp"` perks against its RFI winner.
2. Method: read the comparable's **source** perks (never a winner list). Union over the loadout. Caster = school nodes for the spells it actually casts + survivability set. Hybrid = both.
3. Scoped creature rule: armed creature → same-race same-tier kit; unarmed → AP trait kit.
4. Origin FormID; never a new PERK record.

**New items:**
- **R9 ownership line** (perk side of the drafted npc fix): "Perks and spells travel only with `SpellList`. A Stats-only templated combatant has only its own list. Give it a kit or tick `SpellList`. Stripping its REQ_NULL perks alone leaves it with none."
- **Upstream vs downstream** in one line (from the second Hurt row), plus "a chassis already on an input plugin is input".
- **Custom-race trait perks**: build gives none; hand-place the analogue's (the R5 correction, replacing the "never" lines).
- **AP traits are source perks**, distinct from RFTI_All AP.
- **Mastery**: Requiem casters carry none; ARR's patches add them, so ask. (ARR list layer.)

**Delete:** freshness probe, bulk/reconciliation protocol, routing, checklist, notes, the class essay, FormID tables, before/after enumeration, calibration table, all 1.x call shapes.

**Target length:** ~40-50 lines in the body, plus an optional ~40-line on-demand reference of comparable probes by archetype (ladder EditorIDs, caster templates, creature exemplars), not FormID tables. Down from 895.

**Where it lives:**
| Content | Home |
|---|---|
| Boundary fact, SpellList ownership, AP-trait and custom-race trait facts | Requiem profile (generator contract + invariants) |
| Kit method, creature scope, comparable probes | Requiem profile (analogue keys) or npc skill perks section |
| Mastery on casters, clothing-style house choices | ARR list layer |
| Rank write shape, `references=` reverse lookup, resolve/masters verify | houseCARL |
| Mod-PERK listing (2 lines) | router's triage row for PERK |

## Verification (adversarial pass, 2026-10-30)

**Changed:**
- Judgement group numbers normalised to 0-based `judgement.json` indexes. The RB ones were off by one (from `ledger-rot-below`, which is 1-based): the kit group is r54 **g2** (r54 g3 is the Adabelle *miss*), r26 **g16**, r15 **g3**. FS ones were already right.
- Ledger count corrected: 1 helped row (citing 5 ids), not "5 helped". Added the U8 L219 caveat that perk#146 lives in an unopened file; the runs read the SKILL.md copy (#34/#72).
- Helped row 1: added FS s3 (r98 g2, right/major) as a second single-judge win, and the 5 debatable runs, so the strength reads 2 of 7.
- Hurt, creature row: the counter-case is not wholly absent from SKILL.md (L128-130 names "trait-only kits (giant, troll)"); the defect is that no test (armed/unarmed) picks between them. Added the r26 g16 judge's contrast (actual-weapon kits vs broad kits) and the consistency attribution.
- Hurt, chassis row: R2 attributes the TR strip to router text via briefs; this skill is secondary. FS U13's ~80% downstream gap moved to a framing note (upstream omission is valid, not a harm).
- Hurt, trait-bridge row: added RB s2 r15 g4 right/major, R2 TR2b (router caused the withholding), and the SKILL.md L230-233 chassis line that lists racial trait perks with no exception.
- Hurt, Mastery row: added the consistency attribution, the r26 HalfCostPerk point, and the skill's own L226-227 contradiction. Status stays **contested** (not false).
- Hurt, omission row: widened to Adabelle (RB s3 r54 g3, not_done/major); cause untraced.

**Checked, no change needed:**
- Every perk rule id with a helped/harmed label in `drill/absolutes/ledger-*.json`: only RB `npc#223 / perk#13/#37/#43/#33/#3` (helped) and FS `perk#146` ×2 (harmed). Both appear. Neutral rows (#33, #36, #44/#67/#108) are covered.
- U7 (17 deciding points) and R2 strict attribution: no point is driven by this skill; TR-L and TR2b sit on the router. So no Unnecessary item is load-bearing in a deciding point.
- No HELPED item rests on a REQ_NULL→REQ_AbHide re-point or clothing AR 0 (R1 relabels). The REQ_NULL perk fact matches r3 INTEGRATED §3 (PERK is the one type routinely emptied, 147/154). AP-trait (npc#60 false), rp#203 scope, AutoCalc 22/86 and Farengar facts match §3 / R5. No bows or clothing-AR claim in scope.
- Unnecessary: mod-PERK disposition has no judged ledger row (noise holds); the three reference maps had 0 reads (U8 L147-149); class claims hold (r15 g5, r54 g9 right without the essay).
- File sizes match `wc -l` (SKILL.md 294, references 573, index 28 = 895).

**Final counts:** Helped 3 (1 joint method, 1 shared boundary fact, 1 weak). Hurt 6 (1 clear: creature superset; 3 contributing/contested; 1 omission; 1 tool shape with no measured cost). Unnecessary 10 rows (~620 lines), plus the untested-domain list.
