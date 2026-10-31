---
skill: requiem-ammo-patching
scope: distillation for rewrite (helped / hurt / unnecessary)
evidence: scoring/, drill/ (r1), drill/r2/, drill/r3/
date: 2026-10-30
---

# requiem-ammo-patching: distillation

Rule ids are from `drill/absolutes/requiem-ammo-patching.json` (`ammo#N`). Run labels: FS = falkreath-shades, RB = rot-below; s = skill run, c = control run, s1n/c1n = nothink rerun. G/g = judgement group. "Evidence" means something a file shows. "Inference" is marked as inference.

## 1. What it is

- **What it is.** It patches AMMO records (arrows and bolts) and their PROJ to Requiem: damage, keywords, Weight 0, projectile physics and the forge recipe. The core method is "derive from a live Requiem comparable". It has 933 lines in total: SKILL.md 387, plus 5 references (546) and index.jsonl.
- **How often it was used.**
  - Loaded in 7 of 21 skill runs: FS s1, s2, s3, s1n and RB s1, s2, s3 (U8 L56). SKILL.md was read in all 7.
  - **None of the 5 references was ever opened** (U8 L84-88; synthesis.md L259).
  - Each task had exactly 1 AMMO record: the FS golem "bolt" 21F8C0 and the RB gremlin throwing spear (U8 L26).
  - 12 of 171 rules have a recorded use, 10 have a helped row and 0 have a harmed row (U8 L51).
  - Caveat: 2 of the "used" rules, ammo#108 and ammo#131, sit in files no run opened. The ledger cited the reference copy of a line that also appears elsewhere (U8 L219).
  - None of the 17 deciding points in U7 or R2 involves ammo. The ammo wins are minor-to-major single records, not points that decided a task.

## 2. Helped

Shared control comparison for every row: **no control patched either AMMO record.**
- FS: c1 G38, c2 G30, c3 G15 and c1n G20 are all not_done. c2 left the bolt at 12 damage (analysis.md FS L30).
- RB: c1 g18, c2 g24 and c3 g45 are all not_done.
- The FS controls did examine AMMO and wrote nothing (coverage FS L20-23). RB c2 dismissed AMMO in a one-line summary (coverage RB L27).

| # | Item | Source | Evidence | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | **No ammo keyword is assigned by the Reqtificator. Carry every keyword yourself, NoDamageRescale included.** | SKILL.md Overview L24-30 (ammo#4, #5, #7); step 4 L206 (#41), L224 (#49); Common mistakes L328 (#74), L347 (#81); keywords.md "The split" (#130, #131). Restated by the router: integration-checklist.md L49 ("even RFTI exclusions are carried") | All 7 runs stamped the comparable's keyword set including NoDamageRescale AD3B2D, and all were judged right. FS: s1 G32 minor, s2 G24 **major**, s3 G31 **major**, s1n G21 minor (ledger FS ammo#49/#74/#131; U11 FS rows d11, d37, d113, d15). RB: s1 g49, s2 g22, s3 g28, all minor (ledger RB). Runs cited it directly: FS s2 d11 ("every ammo keyword, including RFTI_Exclusions_NoDamageRescale, is source-carried"); RB s3 d14 cited the router matrix line. ledger ammo#49: 3 helped rows, 0 harmed (absolutes SYNTHESIS L222). Rescore: NoDamageRescale on a hand-set bolt is "needed either way" in FS and borderline in RB (rescore FS L73, RB L77) | 7/7 runs, but 1 judge per run (single-judge each, consistent across runs). **Joint with the router:** the router put AMMO in scope; this line made the agent patch the record instead of leaving it to the Reqtificator. The values came from the comparable. Inference on share: about half router scope, about half this fact | The keyword half verbatim. The Reqtificator half must be **corrected** (see Hurt X3): "The Reqtificator multiplies AMMO damage ×4 and adds no keyword. Every ammo keyword is source-carried. A hand-set Requiem-scale damage needs RFTI_Exclusions_NoDamageRescale AD3B2D, or it gets ×4 again." |
| H2 | **Derive everything from a live Requiem comparable ammo** | SKILL.md L17-21 (ammo#2, #3); step 1-2 L144-184 (#30, #33); Common mistakes L335 (#77, "always derive from a Requiem comparable"); ammo-ladder L82 (#108, "same type + material exists → copy it") | Ledger helped rows: #77 FS s1 Damage D109 (G32 right, minor); #108 RB s1-s3 (g49/g22/g28 right, minor), but ammo-ladder.md was never opened, so the RB credit really belongs to the SKILL.md copy of the method (U8 L219). #77's "Requiem ammo is 40-144" half is a latent wrong number (Forsworn 30, #107), see X8. U11 labels 17 of 20 FS AMMO/PROJ rows and 12 of 14 RB rows "analogue-prompted-by-skill". Values came from reads: ballista bolt 0339A1 gave 84 (FS s1 d109, s2 d10); sphere bolt 07B935 gave 84 (s1n d13); Ancient Nord arrow 034182 gave 50 (s3 d35); riekling arrow 017720 gave 50 (RB s1 d191, s2 d34, s3 d14). The reference hand-set 80 (FS) and 50 (RB) (R6 L123). All judged right | 7/7 runs, joint with the router. **The values are from the load order, not the skill text** (facts RB L44, L93: "keywords came from comparables") | One line: "Read the closest Requiem ammo and copy its Damage, Value, Weight and full keyword list. Keep the mod's own NonPlayable/NonBolt flags (see X7). Say what differs." |
| H3 | **Creature / trap ammo is its own branch** | Bulk pass L104 (ammo#20); Judgment L310 (#70) | FS s3 cited it for the comparable choice (ledger FS ammo#20, helped, G31 right/major). But s3's final comparable was a **player** arrow, REQ_Arrow_AncientNord 034182 (damage 50), not a creature record (U11 FS s3 d34); what #20 drove there was "no forge recipe, keep NonPlayable". FS s1, s2 and s1n chose the ballista and sphere creature bolts, and RB s1-s3 the riekling creature arrow, without a ledger citation of #20 | Single ledger citation. **Inference:** the creature context, not #20's text, steered the other 6 runs to REQ_Creature_* records (they broke ammo#31, see X1) | "Ammo an NPC or creature fires: the comparable is a REQ_Creature_* ammo. No forge recipe." |
| H4 | **Weight = 0** | step 3 L200 (ammo#40); Common mistakes L334 (#76) | Cited as skill text in RB s1 d192 and s2 d35, and in FS s1 (ledger #76 helped, minor). Every comparable also had Weight 0 (same rows), so a live read alone would have given the same answer | Several runs, minor, **redundant with the read** | One line, as an invariant with a probe: "Requiem AMMO weighs 0; check with an AMMO Weight>0 query." |

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | **"Pick the canonical record, not a REQ_Creature_* / REQ_NP_*"** | SKILL.md step 1 L154 (ammo#31) | RB s1 and s2 broke it and used REQ_Creature_Arrow_Riekling, judged right. The rule as written would have misled (ledger RB L54). R5 rates it a **multi-judge safe break (6)**: RB s3 is identical, and the reference values are identical (r46 g23, r66 g44, r72 g17). FS s1, s2 and s1n also used REQ_Creature_* bolts (U11 FS). Caveat: the verdicts judge the end values, not the choice of comparable (R5 L85). The ledger labels #31 "helped" only in the sense "helped when broken" (absolutes SYNTHESIS L257). Counter-evidence: FS s3 **followed** #31 (player REQ_Arrow_AncientNord, 50 damage, reference 80) and was also judged right/major (G31), so the break is backed by RB only | No scope. The rule is right only for player ammo | Scoped rule with its counter-case: "Player ammo: use REQ_Arrow_/REQ_Bolt_<Material>. Ammo an NPC or creature fires: use the matching REQ_Creature_* record. Find it by the PROJ the mod ammo uses, or by the weapon or NPC that fires it." RB s1 found its comparable with a reverse AMMO query on the shared PROJ 017721 (U11 RB d191) |
| X2 | **Fixed PROJ profile: bolt Speed 5600, Gravity 0.35, Flags CanBePickedUp + Supersonic. Arrow 3600, Supersonic stripped** | step 5 L233-236 (ammo#51, #52); Common mistakes L341 (#79); Checklist L366 (#90); crafting.md Projectiles (#122) | Live creature PROJs disagree. DLC2ArrowDwarvenBallistaBoltProjectile 0365FD: Speed 5600, Flags **0**. Sphere-bolt PROJ 07B936: Speed **3600**, Flags 0 (U11 FS s1 d114, s1n d16). Outcomes on PROJ 43A46C: FS s1 kept Supersonic "because the skill's bolt profile includes it", judged **no_effect** (G48). s2 kept it, debatable. s3 and s1n removed it per the live read, debatable (ledger FS L79; U11). absolutes SYNTHESIS L274: "fix fact". synthesis.md L258: noise | Wrong fact for creature PROJs. Unscoped number | Delete the profile. Replace with: "Copy Speed, Gravity and Flags from the comparable ammo's PROJ." The player-ammo profile is **unverified** in this eval. If kept, mark it player-only and re-read it live |
| X3 | **"No ammo rule in the Reqtificator config... it only grants a global arrow-recovery perk"; "may rescale your hand-set damage"** | Overview L26 (ammo#5); keywords.md L9-12 (#130); Common mistakes L347 (#81) | The code does **×4 on AMMO damage and adds no keyword**. It skips AlreadyReqtified, NoDamageRescale and Damage ≤ 0 (R6 L43, `AmmunitionTransformer.cs` L14-18). It matches 11/11 RFI ammo (R6 L58; r3 CRITIC row 4). The skill never says ×4. FS c1 knew it (r41 L1437) and left ammo to the Reqtificator (rescore FS L59). Under upstream the FS bolt would end at 48, against the reference's hand-set 80 (R6 L123). No judged wrong call came from this; it hides the upstream option | Incomplete fact. Unstated stance (ammo#36/#96 derive from the RFI winner, which is downstream; #97/#171 assume upstream) | Corrected fact (H1 column). Plus a stance line: "Upstream: leave vanilla-scale damage and RFI applies ×4. Or hand-set Requiem-scale damage with NoDamageRescale, which is what Requiem's own ammo does (234/258 AMMO winners) and what ARR's handmade patches do." |
| X4 | **Material → AP tier and material → AmmoWeight maps as rules** | step 4 L214-223 (ammo#44, #45, #47); Judgment L316 (#71); ammo-ladder L53-65 (#104-#106); keywords.md L62 (#137) | Creature ammo breaks both maps. The ballista bolt is WeapMaterialDwarven but carries **AP tier 7**. FS s1 noted "Draugr material with AP tier 7 is unusual but matches the ballista comparable" (U11 FS d113). The riekling spear is **Massive with no AP keyword**, "taken from the comparable, not from the skill's material-density mapping" (U11 RB s1 d193). Agents copied the comparable, so there was no wrong call | Latent wrong fact (no scope) | Delete the maps from the rule text. "Copy the comparable's full keyword list (ReplaceAll). AP tier and weight class are per-record facts." If a map is kept, mark it as player-ammo only and as a cross-check |
| X5 | **Cross-skill conflict on RFTI exclusions** | ammo#49 and router integration-checklist L49 versus the armor skill's refusal of exclusions | FS s2 (r11): "armor refused RFTI_Exclusions while ammo stamped them" (process FS L97). Not judged as a wrong call | Conflict | One stance statement owned by the router / Requiem profile, applied per type (ammo: hand value + NoDamageRescale; armor: per its own stance) |
| X6 | **Arrow vs bolt classified by Flags; "never cross the AP series"** | First step L57 (ammo#13); step 4 L222 (#48); keywords.md L92 (#140), L107 (#143) | The FS "bolt" 21F8C0 is flagged **NonBolt** (an "Ancient Nord Arrow" fired by a ballista). Its comparable 0339A1 has the same flags (U11 FS s3 d38). The handmade reference gives it **REQ_ArmorPiercingBolt_Tier3** AD397F (FS reference body, keywords). s1n did the same (d15). Judged right. No harm | Latent wrong fact for creature ammo | Classify by the firing weapon and the comparable, not by the flag alone. Copy the comparable's AP keyword |
| X7 | **"Creature / siege ammo carries NonPlayable"** | Judgment L310 (ammo#70); keywords.md L109 (#144); Checklist L357 (#83) | RB s2 read Requiem's REQ_Creature_Arrow_Riekling as **NonBolt only** and kept the mod's NonPlayable (U11 RB s2 d36, L1356). Single run, live read, no harm. Both handmade references keep the mod's NonPlayable: FS 21F8C0 "IgnoresNormalWeaponResistance, NonPlayable, NonBolt"; RB spear "NonPlayable, NonBolt" (scoring/bodies falkreath-shades_H, rot-below_H) | Latent over-generalisation as a Requiem fact (the riekling arrow lacks it); right as a "keep" rule | "Keep the mod's own NonPlayable on NPC/creature ammo; do not add it by rule." Not "copy the comparable's flags", which would strip it in RB |
| X8 | **Internal contradictions (latent: no ledger row hit them; #2, #3, #23, #24, #66, #68, #77, #85 sit in SKILL.md, which all 7 runs read; the rest sit in unopened references)** | Flags-as-union rule (ammo#24, #91) vs literal `Flags = CanBePickedUp` sets (#126, #154, #165). "Never hardcoded numbers" (#2, #3) vs "numbers are the authority" (#98). Checklist "value on tier" (#23, #85) vs quest value 100 and elemental value 11 (#66, #68). AmmoWeight None 9D1F4D (#136) vs checklist #87. "40-144" (#77) vs Forsworn 30 (#107). ReplaceAll example drops enchant keywords (#163). Worked example 2 drops the enchant marker it names (#149). Post-enable read-back expects the patch to win, but RFI wins after a rerun (#171) | drill/absolutes/requiem-ammo-patching.json notes | Conflict (untested) | Delete with the references. Flags-as-union belongs in houseCARL |

There is no stop-to-ask gate in this skill (absolutes json field_notes), and it caused no refused calls. The 1.x tool names caused 0 failed calls in all 6 tasks (process SYNTHESIS L59).

Adjacent harm, owned by the magic skill: enchantments.md "EXPL Damage 0", a line written for elemental arrow explosions, was applied to a spell explosion in RB s2 (d161; R2 RB L136). The rewrite should scope the arrow-explosion rule where it lives.

## 4. Unnecessary

| Content | Source | Why | Lines | Keep-short / delete |
|---|---|---|---|---|
| Freshness probe + "never re-point houseCARL" | SKILL.md First step 1, L41-54 (ammo#9-#12); Common mistakes L343 (#80); Notes L380 (#96) | **Duplicated** in all 11 skills. Noise in every task (rp#4/#5, absolutes SYNTHESIS L89) | ~20 | Delete (router / houseCARL) |
| Bulk pass protocol | SKILL.md L69-140 (#16-#29); housecarl-recipes Coverage audit L17-45 (#160-#162); Checklist L354 (#82) | **Generic craft and duplicated** (the router owns enumerate → reconcile). Untested: 1 AMMO per task. Bulk-pass sections are rarely cited across skills (U8: 14/114 used) | ~105 | Delete |
| Query recipes and depth hints (dense, resolve_names, depth=4, no conflict_tree) | step 1-2 L146-184; step 6 L247-265; crafting.md L24-40 (#33, #34, #56, #57, #119) | **Generic tool craft, 1.x names.** 0 failed calls, mapped silently (process SYNTHESIS L59) | ~50 | Delete. Move to houseCARL query docs |
| Ladder tables (13 arrows, 13 bolts, special ammo) | ammo-ladder.md (#98-#110); step 3 L191-199 (#37-#39) | **Untested** (no player tiered ammo in any task; never opened). The values are readable live. Facts RB L106: "cut value tables the model reads live" | ~100 | Keep short: 2 lines as a cross-check (arrows +10 per tier from Iron 40; bolt = arrow ×1.2), marked "unverified in eval, read live" |
| Keyword FormID tables | keywords.md L19-103 (#132-#142); step 4 L208-223 | Untested as tables; every run copied the comparable's list. Partly a wrong scope (X4) | ~95 | Delete the tables. Keep the "split" fact (H1) and 1 line on silver/daedric special keywords (untested) |
| PROJ section | step 5 L227-241; crafting.md L45-89 (#121-#128) | Wrong for creature PROJs (X2). Player PROJ untested | ~60 | Replace with 1 line: copy the comparable PROJ |
| Forge recipe | step 6 L243-271; crafting.md L3-43 (#111-#118); housecarl-recipes D L106-132 (#166, #167); Checklist L369 | **Untested** (no craftable ammo in any task) | ~85 | Keep short: "One forge COBJ per craftable ammo, copied from the comparable's recipe (ingot + firewood → 30, HasPerk gate). No temper recipe." Composition grammar goes to houseCARL |
| Emit the override / masters / verify write | step 7 L273-278; housecarl-recipes L1-16, L134-156 (#155-#159, #169-#171) | Generic houseCARL craft, 1.x shapes | ~40 | Delete. Move to houseCARL |
| housecarl-recipes A/B/C | L47-105 (#163-#165) | Never opened. Contains 2 contradictions (X8) | ~60 | Delete |
| Special / elemental / quest / bound ammo | Judgment L285-312 (#63-#70); worked-examples ex. 2 (#148, #150) | Only the creature branch was used (H3). Elemental, quest and bound are **untested**. Elemental (#66, "the hit lives on the PROJ/EXPL") was listed as Helped in the first draft; no elemental ammo appeared and no call was judged on it (only an indirect note, U11 FS s3 d34), so it moved here | ~28 | Keep short: about 4 lines (creature; elemental effect on the PROJ, routed to magic; quest keeps the author's value, no recipe; bound routed to magic) |
| Bow-tier pairing | Judgment L314-321 (#71, #72) | **Untested** (no player ammo, no ammo packs) | ~8 | Keep 1 line: AmmoWeight pairs with the bow draw class |
| Common mistakes | L326-348 (#74-#81) | **Duplicated**: restates the workflow | ~23 | Delete (fold the 2 surviving facts into H1 and X2) |
| Checklist | L350-376 (#82-#95) | Duplicated. Gates are the least-evidenced rule kind (U8: 17/161 used) | ~27 | Delete. At most a 3-line "done" line |
| Worked examples | worked-examples.md (#145-#154) | Never opened (U8 L33). Example 2 contradicts itself (#149) | 76 | Delete |
| index.jsonl | references/index.jsonl | Routing metadata for deleted references | 28 | Delete |
| Overview scope paragraph / Notes | L32-35, L378-387 | Duplicated routing (the router routing-table already routes AMMO/PROJ) | ~15 | Keep 1 scope line |

**Untested domains** (the six tasks never exercised them; keep short versions, do not call them useless):
- player tiered arrows and bolts;
- modded ammo packs (synthesis.md L467);
- forge recipes for ammo;
- elemental ammo and its PROJ/EXPL link;
- quest and unique ammo;
- bound ammo;
- the bow-tier (AmmoWeight) pairing;
- player-ammo PROJ physics;
- whole-plugin bulk passes over many AMMO records;
- silver and daedric special keywords.

## 5. Corrected facts for this skill

| Fact | Status | Source |
|---|---|---|
| The Reqtificator multiplies AMMO Damage ×4 and **adds no keyword**. It skips AlreadyReqtified, NoDamageRescale and Damage ≤ 0 | **Verified in code + live** (11/11 RFI ammo) | R6 L15, L43, L58; r3 INTEGRATED L69, L96; r3 CRITIC row 4 |
| Requiem AMMO population: 258 winners = 234 with NoDamageRescale + 13 with Damage ≤ 0 + 11 rescaled by RFI. 0 carry AlreadyReqtified | Verified live | R6 L60 |
| A hand-set Requiem-scale value needs NoDamageRescale to survive. Under upstream with vanilla-scale damage, RFI gives ×4 (FS bolt 12 → 48; RB spear 10 → 40) | Verified code + live | R6 L123; rescore SYNTHESIS L95 |
| ARR's handmade patches hand-set creature ammo (FS bolt 80, RB spear 50) and add NoDamageRescale | **House convention** (both references) | R6 L123; FS reference body 21F8C0 |
| Creature ammo comparables: REQ_Creature_Arrow_Riekling 017720 (50, Massive, no AP, NoDamageRescale). REQ_Creature_Bolt_DwarvenBallista 0339A1 (84, Dwarven, AP 7). REQ_Creature_Bolt_DwarvenSphere 07B935 (84, Heavy, AP Bolt T3) | Live reads in several runs (U11 rows) | U11 FS d109, d113, d13, d15; RB d191, d193 |
| Creature PROJs are not on the skill's bolt profile: 0365FD is Speed 5600 / Gravity 0.35 / Flags 0; 07B936 is Speed 3600 / Flags 0 | Live reads in runs (2 records) | U11 FS d114, d16; ledger FS L79 |
| Player arrow/bolt PROJ profile (3600 / 5600, Supersonic on bolts) | **Unverified** in this eval | not read by any run |
| Requiem's riekling creature arrow is NonBolt only (no NonPlayable) | Unverified (single-run live read) | U11 RB s2 d36 (L1356) |
| Both handmade references keep the mod's NonPlayable on creature ammo | Live (reference records) | scoring/bodies falkreath-shades_H, rot-below_H |
| Following "player comparable" for the FS bolt (Ancient Nord arrow, 50) and breaking it (creature bolts, 84) were both judged right | Judged (single judge per run) | U11 FS s3 d34-d38 (G31); s1 G32, s2 G24 |
| A NonBolt-flagged creature "bolt" can carry the Bolt AP series (reference 21F8C0: NonBolt + REQ_ArmorPiercingBolt_Tier3) | Live (reference record) | FS reference body |
| "Not a REQ_Creature_* comparable" is wrong for NPC/creature ammo | **Multi-judge** (R5: 6) | R5 L51, L72 |
| 1.x houseCARL names in skill text cause 0 refused calls (hygiene, not harm) | Verified across 6 tasks | process SYNTHESIS L59 |

None of the r3 false facts (REQ_NULL inert, clothing AR 0, AR caps, RegenHpInCombat, lockpicking DLL, bows not ×4, magic#35, npc#60, rp#203) appears in this skill. The R9 SpellList-on-Stats line does not apply to ammo.

## 6. Rewrite brief

**Purpose.** Make AMMO and its PROJ consistent with Requiem by copying a live Requiem comparable, and make the agent aware of the Reqtificator's AMMO behaviour so that it picks a stance. The skill is small enough to stop being a standalone skill.

**Must keep (verbatim intent):**
1. The boundary fact, corrected: "RFI: AMMO damage ×4, no keyword. Every ammo keyword is source-carried. A hand-set Requiem-scale damage needs RFTI_Exclusions_NoDamageRescale AD3B2D."
2. The stance line: upstream (vanilla-scale damage, RFI ×4) vs hand-set + NoDamageRescale. Requiem's own ammo (234/258) and ARR's references use the second.
3. Analogue keys:
   - player ammo → REQ_Arrow_/REQ_Bolt_<Material>;
   - NPC or creature ammo → REQ_Creature_*;
   - find the comparable by the shared PROJ or by the firing weapon/NPC.
   - Probe: an AMMO query with `references=[<PROJ>]`, or an EditorID-contains `REQ_Creature_` query.
4. "Copy Damage, Value, Weight and the full keyword list (ReplaceAll) from the comparable; keep the mod's own NonPlayable/NonBolt flags. Copy its PROJ's Speed, Gravity and Flags. Say what differs."
5. The Weight 0 invariant, with a probe query.
6. Traps (one line each):
   - an elemental effect lives on PROJ/EXPL, not AMMO Damage (route to magic);
   - quest ammo keeps the author's value and gets no recipe;
   - bound ammo goes to magic;
   - crafted ammo gets one forge COBJ copied from the comparable.

**New items.** No new item comes from U13. Its only ammo gap is the NPC ammo count (FS N10), which judges call taste (U13 FS L57), so no rule is needed. The AC N9 exclusion gap is armor, not ammo. FS N27 (reference golem-bolt keywords 01CEAD and ADDDD2, never made by any run, judged right 6 / debatable 2) is marked "skill holds (ammo AP tiers)" in U13 FS L51, but neither FormID appears anywhere in the ammo skill, so it is not evidence that the keyword tables are load-bearing; copying the comparable's list also misses them. **Inference:** a house-specific keyword choice; at most an ARR list-layer note. Also add the cross-type stance statement that removes the armor/ammo exclusion conflict (X5), which lives in the router/profile.

**Delete:**
- freshness probe, bulk pass, coverage audit;
- query and depth hints, all housecarl-recipes;
- the PROJ profile;
- material → AP and weight maps as rules;
- keyword FormID tables;
- Common mistakes, Checklist, Notes;
- worked examples, index.jsonl.

**Target length.** About 30-45 lines, plus an optional 10-line player-ammo cross-check table marked "unverified, read live". Today it is 933 lines.

**Where it lives:**
- **Requiem profile** (item section, shared with weapons; synthesis.md L253 already suggests merging with weapons): items 1, 3, 4, 5, 6.
- **Router:** item 2 (one stance statement for all types), and the AMMO/PROJ routing. The router should also give PROJ 43A46C-type cases (an exploding bolt) a single owner, since FS s2/s3 double-counted it across the item and magic lanes (process FS L98).
- **ARR list layer:** "ARR's handmade patches hand-set creature ammo at Requiem scale + NoDamageRescale".
- **houseCARL:** Flags-as-union writes, COBJ condition composition, query depth, pre-enable verification.

## Verification (adversarial pass, 2026-10-30)

**Changed:**
- H2: added ammo#77 (FS s1 D109, G32 right minor) and ammo#108 (RB s1-s3) ledger helped rows, which the draft left out of Helped. Flagged #108's file as never opened (U8 L219).
- H3: corrected. FS s3 cited ammo#20 but picked a player arrow (REQ_Arrow_AncientNord 034182), not a creature record. The "branch used in 6 runs" claim is now marked inference.
- H5 (elemental on PROJ/EXPL) moved from Helped to Unnecessary/untested: no elemental ammo, no judged call.
- X1: added FS s3 counter-evidence (followed ammo#31, also judged right/major) and the ledger's "helped = when broken" label (absolutes SYNTHESIS L257).
- X7 and the H2/brief copy rule: "copy the comparable's flags" would strip NonPlayable, which both handmade references keep. Now "keep the mod's own NonPlayable/NonBolt".
- X8: heading fixed. Several contradicting rules sit in SKILL.md (read in 7 runs), not only in unopened references.
- New items: added U13 FS N27 (reference keywords 01CEAD, ADDDD2) and why it does not make the keyword tables load-bearing.
- Section 5: 2 rows added (references keep NonPlayable; both comparable choices judged right in FS).

**Checked, no change:**
- Every ammo rule id in drill/absolutes/ledger-*.json (FS and RB only; the other 4 ledgers have none): #20, #31, #40, #41, #49, #51, #52, #74, #76, #77, #108, #131. All now appear. 10 helped ids + 2 neutral (#51, #52) = U8 L51's 12 used / 10 helped / 0 harmed.
- H1 run/group/severity values match the ledger. R6 L43/L58/L60/L123 and r3 INTEGRATED L69/L96 confirm ×4 with no keyword. The "fact" X3 corrects matches the r3 fact sheet.
- X2: ledger FS L79 and absolutes SYNTHESIS L274 ("fix fact") confirm. U11 shows 0365FD Flags 0 and 07B936 Speed 3600.
- X5: process FS L97 confirms the armor/ammo conflict.
- X6: the FS reference body has NonBolt + AD397F (Bolt Tier3).
- None of the r3 false facts (REQ_NULL, clothing AR, AR caps, RegenHpInCombat, lockpick DLL, bows not ×4, magic#35, npc#60, rp#203) is in this skill. The clothing-AR caveat does not apply.
- Unnecessary rows: no ammo item is among the U7/R2 deciding points (U7 mentions ammo only as "no ammo" in TR). Grep of R1, R2 and R10 finds no ammo use. No Unnecessary row is load-bearing.
- Line counts (933 total, SKILL.md 387) match `wc -l`.

**Open discrepancy:** R5 L51 gives the RB ammo judgement groups as g48/g21/g27. The ledger, used here, gives g49/g22/g28. This looks like an off-by-one in group indexing between the two sources. The verdicts agree (right/minor).
