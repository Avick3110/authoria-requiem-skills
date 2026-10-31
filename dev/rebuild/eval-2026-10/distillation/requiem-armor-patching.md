---
title: Distillation - requiem-armor-patching
date: 2026-10-30
scope: the armor skill as tested (scoring/synthesis/synth-work/skills/requiem-armor-patching/, SKILL.md + 5 references, 1,070 lines, 151 rules)
evidence: drill/absolutes (requiem-armor-patching.json, ledger-*.json, SYNTHESIS.md); drill/facts/SYNTHESIS.md; drill/rescore; drill/consistency; drill/coverage; drill/r2 (INTEGRATED, U3, U6, U7, U8, U10, U11, U12, U13); drill/r3 (INTEGRATED §3, CRITIC, R1, R2, R6, R7, R10, R12)
---

# requiem-armor-patching: what helped, what hurt, what was unnecessary

Rule ids are `armor#N` from `drill/absolutes/requiem-armor-patching.json`. "Evidence" is a ledger row, a judgement group or a deciding point. "Inference" is marked.

## 1. What it is

Patches ARMO (cuirass, helmet, gauntlets, boots, shield, clothing/jewelry frames) to Requiem: armor rating, value and weight from Requiem's same part x weight x material comparable, the input keywords, temper/forge COBJs by cloning, as an ESP override.
**Load/read (U8):** loaded in **14 of 21** skill runs (FS 4, MC 4, RB 3, TR 3; **AC 0, FQ 0**). SKILL.md read in 14; keywords.md 9 (6 at 50%+); housecarl-recipes.md 4 (1); crafting.md 3; ar-ladder.md 2; worked-examples.md 1 (0 at 50%+). 28 of 151 rules used, 119 read with no evidence, 4 never read. 12 rules have a helped row, 3 a harmed row.

Overall evidence (U11, R2, R10): armor work in FS and RB was done by the controls too; the skill added consistency more than discovery. None of U7's 17 deciding points is armor-domain-led (R2: FS1 router-only coverage; MC3 regen robe prices own probe and no live effect). Its real wins are small: FS cuirass weight and not over-setting the Witch Hood (R10 FS-A1/A3), and RB temper recipes (minor). Its real loss is the MC extra-slot enchant gap: on R10's core MC items the best run is control-1 (75 v 70), only because of that gap.

## 2. Helped

| # | Item | Source | Evidence | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | Derive from the **same-part, same-material, same-weight** Requiem comparable; use ratios only when none exists | SKILL Workflow 3 (armor#34), ar-ladder "How to derive" (armor#91, #97), Overview (armor#3, #4) | Judged: FS Arkay cuirass 0FE2AD/58E72B s1 G33, s2 G25, s3 G26, s1n G18 right/major; controls c1 G8 / c1n G22 debatable, c2 G27 / c3 G12 not_done (ledger FS). Witch Hood 1F6F44 s3 G29, s1n G19 right/major. Reference AR 200, a bug 8/8 judges (U3 FS1). **Mechanical (R10 FS, effective AR via the code formula):** cuirass AR (A2) skill 4/4 pass, c1 and c1n also pass, c2/c3 not attempted; cuirass weight (A3) skill 4/4, c1 and c1n **fail** (weight 13); Witch Hood (A1) skill 4/4, c2/c3 pass **untouched** (21 -> 87.3 in range), c1n pass, c1 fail (AR 140 + exclusion keyword), reference fail | Weaker than the judges say. On cuirass AR the gap is coverage (router FS1, R2), not method: controls that reached it got AR right. The method's own edge is weight (A3, minor) and not over-setting the hood (A1 v c1). Caveat: 0FE2AD is TNAM-templated; its in-game AR not verified (R10 FS L88) | The method line verbatim in spirit: "read the same-part, same-material, same-weight comparable and copy it; ratio off the cuirass only if none exists" |
| H2 | Three coordinates pick the comparable (part, weight class from `BodyTemplate.ArmorType`, material/set); the **set keyword** carries tier | SKILL First step 2 (armor#9), Workflow 4 (armor#10), ar-ladder (armor#89) | FS s3 kept Arkay as heavy steel plate (G26 right/major) and keyed the Witch Hood (G29 right/major) on armor#10. RB Death Knight set right in all 6 runs (noise there) | Single judge per run; joint with H1 | The analogue key: `REQ_<Heavy|Light>_<Material>_<Body|Head|Hands|Feet|Shield>`; pick by `ArmorType`, not by name |
| H3 | Ladder numbers and per-part ratios as a cross-check (helmet x0.40, hands/feet x0.30, shield AR x0.60 / value and weight x0.50) | SKILL Workflow 3 (armor#32, #33), ar-ladder tables (armor#92, #93) | FS s1 D104/D105 (G33 right/major) cited #32/#33; s1n cited #31 (G18 right/major; #31 is the false "~10x" line, it helped only as a pointer to the ladder). Values matched the live comparables | Single judge; values came from the load order (R2), the table only confirmed | One short ladder table (cuirass per material) + the ratio row. Drop the "~10x vanilla" framing (see X5) |
| H5 | Jewelry frame keyword set `[ArmorJewelry, VendorItemJewelry, slot]` | SKILL Judgment, cosmetic-cloth bullet (armor#60) | MC s1n: collars/lantern VendorItemClothing -> VendorItemJewelry, g10 right/minor; no control group | Single judge, minor | Keep the frame lists (clothing + jewelry), with a slot condition (see X1) |
| H6 | Temper COBJ authored by cloning the comparable's conditions and items; armor tempers at the armor workbench 0ADB78 | Workflow 6 (armor#45, #46), crafting.md (armor#106-108), housecarl-recipes E (armor#141) | RB: new temper recipes for Death Knight / Altar Shield, s1 g8, s2 g30, s3 g40 right/minor; no control and not the reference made them. FS s3 Witch Hood temper items G34 right (#45) | Multi-run, minor value; RB new tempers are not scored by R10. FS side is not a win: R10 FS K1-K3 (temper gate, forge perk, Corundum) pass for controls too, and the #45 condition rows were noise | One line: "clone every condition and item of the comparable's COBJ; swap CreatedObject". Keep the workbench FormIDs |
| H7 | Source-carried v build-assigned keyword split, used for a keyword ReplaceAll | keywords.md split (armor#111), SKILL Workflow 4 (armor#36) | FS s2 D14 ReplaceAll on Arkay cuirass, G25 right/major | Single judge | Keep the input set `[armorType, armorSet, armorPart, VendorItemArmor] (+PerkFists<Mat> on heavy gauntlets; shield part 0965B2)`, but say "plus whatever else the comparable carries" (see X9) |

Not counted as helped (followed and right, but controls did the same; ledger label noise): extra-slot AR -> 0 (armor#62/#71, MC; controls 2/3; and R1 relabels clothing AR 0 no_effect, see X4), clothing boots -ArmorBoots +ArmorClothing (armor#122/#60, MC; controls 2/3; R10 MC I14 disputed), RB Death Knight set (armor#34/#36/#10; controls right; R10 C17 all pass), TR creature skins skipped (armor#16/#17/#132; controls same).

Removed from helped on verification:
- **MC3 regen robe prices (armor#3, was H4).** Ledger labels it helped (MC s1 g5, s1n g4 right/major; controls 0/3). But R2 classes MC3 as **own probe**: no skill text names the regen ladder or matching by ENCH; armor#3 and router economy#88 only prompted a value pass. And R10 MC I9 finds it has **no in-game effect**: the 15 regen robes sit only in orphan list 000BFB, so they are unobtainable. Not a win for the skill; no "match on ENCH" line is needed for it.
- **RB Altar Shield (armor#57/#41), contested.** Ledger: noise (judges c2, c3 right, c1 debatable). R10 RB C16 (mechanical: Heavy, or Light with weight <= 7.5, ward enchant kept): skill 3/3 pass, controls 1/3 (c2 only). Judges and checklist disagree; no rule credit until resolved.

Counterfactual (no run evidence): AC Tsun's Armor. The skill holds the counter-case (armor#15, worn NonPlayable is in scope), but **no AC run loaded the skill** (rp#14 overridden for budget; U7 natural ablation 2). Under the upstream lens the build would rescale it anyway (see §5), so this is a downstream-only point (U13 AC, rescore AC).

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | Clothing frame lists `ClothingBody/Feet/Hands/Head` with **no slot condition**; used as a pass on 40 extra-slot accessories | keywords.md "Clothing & jewelry frames" (armor#122) | MC: enchant block missed **4/4 skill** runs (s1 g3, s1n g3, s2 g7, s3 g0, all not_done/major). Reference right/major 7/7 judges (U13 NM1). Control-1 closed it by stripping the slot keywords (r79 g0 "equivalent", found by its own Agility-exploit probe, R2 MC-L); c2, c3 missed. s1: "verified the keyword sets of the other 168" against the frame. R10 MC I1: 0/40 in every run but c1 (40/40), which makes c1 the best run on core items (75 v skill 70). Facts #12; absolutes SYNTHESIS §7 | No scope + missing fact | Add: "a piece on a non-standard biped slot (44-59, or 34/38 bracers/garters) must not carry a vanilla `Clothing*`/`Armor*` slot keyword; add `MagicDisallowEnchanting 0C27BD` (or strip the slot keyword). Otherwise it is enchantable and stacks under real armor." Probe: list ARMO whose FirstPersonFlags are outside 30-33/37 and that carry a Clothing*/Armor* part keyword |
| X2 | Extra-slot section stops at ArmorRating | SKILL Judgment "Cosmetic cloth and extra-slot accessories" (armor#62) | All 4 MC skill runs used it for AR 0 and none extended it to enchantability (ledger MC "SHOULD HAVE FIRED") | No scope (the gap, not a misfire) | Merge with X1 into one extra-slot rule: AR + enchant block (+ Agility stacking, unverified, see §5) |
| X3 | "Already-Requiem-consistent: confirm on the record, then leave it" | SKILL Bulk pass skip list (armor#18) | MC s1n skipped the same 40 as "already consistent" after a sweep truncated at 22 of 44 rows (U12; coverage SYNTHESIS row 1) -> g3 not_done/major. MC s1 skipped school robes the same way (noise; controls too) | No probe defined; truncated read taken as absence | Make it a probe, not a label: "consistent = the X1 slot probe and the effective-AR probe (§5) both pass on the full result set" |
| X4 | "Clothing must be ArmorRating 0 in Requiem" | Common mistakes (armor#71), keywords.md frame (armor#122), checklist (armor#84) | False as a Requiem fact: 20/385 Requiem clothing ARMO have AR > 0, equal to vanilla; the build never rescales clothing (U6 §2, code). MC s3 saw Ulfric/Jester AR > 0 live (trace L980) and still zeroed "per the skill" (L1097). R1: 6 MC right/major relabelled. **But** ARR's MC reference zeroes the same items (r3 CRITIC: r87 g9, r37 g1, r38 g0, r45 g7, r52 g1, r61 g4) | Wrong fact (a house convention stated as a Requiem rule) | Restate: "Requiem keeps vanilla clothing AR and never rescales clothing. ARR convention: zero stray AR on clothing and extra-slot pieces." Put the convention in the ARR list layer. Do not write "copy the mod's clothing AR" (it contradicts the reference) |
| X5 | "AR stored at ~10x vanilla"; `armorRatingThresholds` are "caps"; "a wildly high AR gets clamped"; "derive and you are automatically under the cap" | SKILL Workflow 3 (armor#31), Judgment cap section (armor#54-56), Common mistakes (armor#67), checklist (armor#75), ar-ladder (armor#88, #95, #96); duplicated in router `combat-resistance.md` L34 | False (code + RFI 70/70, U6 §3, R6, R12): affine rescale `AR x 3.3 light / 6.6 heavy + 66 body / 18 other`, only when AR is **below** the threshold. Thresholds are triggers, not caps; nothing is clamped. Tsun 29 -> 161, not 290 (rescore AC L95). R1: 3 control groups and 1 folded skill decision rest on the cap premise, 0 relabelled, 0 RM lost. No skill run made a wrong edit from it (latent; armor not loaded in AC) | Wrong fact | Replace with the formula and an effective-AR probe (§5). Delete the cap section, armor#67 and the "<= cap" checklist item. Fix the router copy |
| X6 | Worn NonPlayable: "tier it, do not skip" (armor#15) v housecarl-recipes skip list "non-playable/template ARMO" (armor#132); router cosmetic-skip rp#12 | SKILL Bulk pass; housecarl-recipes Coverage audit | AC s3 skipped Tsun's Armor as "NPC-only dressing" (process AC L652; not_done). 6/7 AC runs skipped it as non-playable (coverage AC L87). The armor skill never loaded in AC, so no run harm traces to armor text: R2 AC-L2 puts s3's skip on router triage (rp#12). Under the upstream lens the skip is defensible (R4: build re-run gives 161.7; rescore AC calls it a choice item) | Conflict between files and with the router (latent here) | One rule in one place: "a worn NonPlayable piece is in scope. Upstream: leave it if its effective AR lands on tier (the build rescales NonPlayable; RFI holds 109 such overrides). Downstream: copy the twin's AR and add NoArmorRatingRescale." Delete the housecarl-recipes skip line |
| X7 | `REQ_NULL_*` armor = "NULLed dead records with junk stats"; "no REQ_NULL_* reference remains in any patched field" | Workflow 1 (armor#25), Common mistakes (armor#69), checklist (armor#83) | Partly false: REQ_NULL ARMO 10/98 have AR -> 0 (TG amulets, MG04Reward03, Miraak masks); the rest keep content; REQ_NULL means "unused by Requiem's own content" (R7, r3 §3). No armor-run harm logged; the same strip family caused router harms (rp#137: AC s2 major, FS s1 G21 major) | Wrong fact (inherits rp#136) | Keep "prefer the canonical `REQ_<W>_<M>_<P>` over REQ_NULL_/REQ_Var_ as a comparable". Delete the strip checklist item |
| X8 | "Never hand-stamp ranged-resistance tier or tempering keywords; adding them fights the build pass" | Workflow 4 (armor#39), Common mistakes (armor#64), keywords.md (armor#110, #116), recipes (armor#137), checklist (armor#74), armor#30 | The rule is harmless, the reason is false: RFI re-adds both even on AlreadyReqtified / AD3B2B armor, so baking is **redundant**, not harmful (R6, r3 §3). ARR's Reqtificated references bake them by convention (rescore FS/RB "ARMO: Ranged_Tier5, REQ_Tempering_*, NoArmorRatingRescale"). Unscoped for a downstream patch (absolutes notes on #30, #39) | No scope (upstream/downstream) + wrong reason | "The build adds these from the set keyword; upstream, leave them out. A downstream bake may carry them (ARR convention); it changes nothing." One line, stated once |
| X9 | "Standard armored piece carries exactly four keywords" v "copy Survival tags the comparable carries" v ReplaceAll recipe with four values | SKILL Workflow 4 (armor#36), keywords.md (armor#113), housecarl-recipes A/C/D (armor#136) | Contradiction inside the skill; live records carry Survival_ArmorWarm (worked-examples 2). ReplaceAll drops mod-own keywords. No judged harm; FS s2 ReplaceAll judged right | Conflict | "Required inputs: type, set, part, vendor (+PerkFists). Keep any other keyword the winner or comparable carries." Delete "exactly four" and the ReplaceAll templates |
| X10 | Ranged tier "gates on cuirass keyword + armor-type" v "assigned to anything with the cuirass keyword" | Workflow 4 (armor#40) v Judgment cosmetic cloth (armor#61) | Internal conflict (absolutes note on #40). Code: ranged tier is cuirass-only and the rescale needs Light/Heavy (R6, U6). FS s1 #40 use on Witch Hood: G34 debatable | Conflict | State once: "cuirass part keyword on a Light/Heavy piece" |
| X11 | Checklist demands forge + temper recipes, on-tier value and a "material + part" name for every piece | Checklist (armor#76, #79, #80) | Conflicts with artifacts not craftable (armor#51), bespoke unique value (armor#94, worked-examples 4) and worn NonPlayable skipping recipes (armor#15). armor#79 would rename named uniques. 0 uses (U8: Checklist 13 rules, none used) | Conflict; unscoped gate | Delete the checklist; keep exceptions next to the rule they qualify |
| X12 | "Forge and temper recipes carry a single HasPerk condition" | crafting.md (armor#105); recipe E template omits RunOnType | Requiem's daedric temper carries `GetCurrentTime() > 23` + HasPerk (live 0EB860, a weapon COBJ; 4 RB judges called it wrong and were refuted, U2). Armor COBJs not read (inference: same convention). FS-K1 counts "one HasPerk gate" only for FS's items | Wrong fact (incomplete) | "Clone every condition the comparable carries" (H6) and drop the "single condition" claim |
| X13 | Masters note: patches "reference the resist/tempering keywords the Reqtificator assigns"; add Requiem.esp as a master by hand | housecarl-recipes Masters (armor#142), SKILL Workflow 7 (armor#48) | armor#142 contradicts never-stamp (absolutes note). MC s1 considered armor#48 and declined, correctly (neutral; the master matters only for LVLI merge eligibility) | Conflict; generic | Delete both; masters are houseCARL's job |

## 4. Unnecessary

| Content | Source | Why | Lines | Action |
|---|---|---|---|---|
| Freshness probe + RFI authority doctrine | SKILL First step 1 (armor#6-8), Notes (armor#85), Workflow 2 tail (armor#29) | Duplicated x11 across skills; noise in every task (absolutes SYNTHESIS: rp#4/#5/#6/#7 noise) | ~35 | Delete; router only |
| Bulk pass protocol (enumerate, disposition, reconcile, no extrapolation) | SKILL L64-146 (armor#11-24) | Duplicates the router's enumerate/route/reconcile (rp#163/#11/#108). Its used lines were the skips (#16-18: noise or harm) | ~83 | Keep 4 lines: the ARMO-specific skip kinds (template/no-slot, race skin, creature skin) and X6's NonPlayable rule. Rest to router |
| bulk-record-jobs pointer | SKILL Bulk pass (armor#22) | Never exercised | 4 | Delete |
| houseCARL call shapes: dense/resolve_names, depth=4, conflict_tree, batch reads, references= lists, compose grammar, write verification | SKILL Workflow 1-2, 6-7 (armor#27, #28, #43, #44), crafting.md L1-30 (armor#100, #102), housecarl-recipes.md (all) | Tool mechanics in 1.x names; 0 refused calls, mapped silently (process SYNTHESIS); 17/19 recipe rules unused (U8) | ~60 in SKILL + 30 + 177 | Move to houseCARL review skills / tool docs; delete here |
| worked-examples.md | references | Read by 1 run, 0 used (U8: all worked examples unevidenced) | 82 | Delete; keep the one Ebony-gauntlet check as a line in the ladder table |
| Ladder tables duplicated (SKILL step 3 + ar-ladder.md) | SKILL L195-219, ar-ladder.md | Duplicated; values come from the load order (R2) | ~25 + 107 | Keep one table, ~15 lines |
| Ranged-tier and tempering-perk tables ("reference only") | keywords.md L86-121 (armor#116-119) | The build assigns them (R6); reference-only content the agent must not use | ~36 | Delete; one line in X8 |
| Crafting reference beyond the clone rule | crafting.md (101 lines) | Temper = HasPerk(material) is model knowledge (facts #37 "K"); FS #45 condition rows were noise | ~90 | Keep ~6 lines (workbench FormIDs, clone rule, ingredient FormIDs) |
| Sounds / block-bash section | SKILL L240-247 (armor#41), keywords.md L152-159 (armor#124, #125) | RB Altar Shield followed it; controls right too (noise) | ~16 | Keep 1 line: "shields: copy BashImpactDataSet + AlternateBlockMaterial from the comparable shield" |
| "Route the enchant effect to magic" | Overview, keywords.md (armor#123), checklist (armor#84), Notes | Noise 2/3 rows, overridden correctly (absolutes: cut) | ~10 | Delete; router routing covers it |
| Checklist | SKILL L399-426 | Gate; 13 rules, 0 used (U8); conflicts (X11) | 28 | Delete |
| Common mistakes | SKILL L377-397 | Restates body rules; holds X4, X5 errors | 21 | Delete; fold the 3 real traps into the body |
| Notes | SKILL L427-438 | Restates authority and routing | 12 | Delete |
| "If still ambiguous, say so and ask" | Judgment artifact step 4 (armor#53) | Stop-to-ask gate; 0 uses; gates cut eval-wide (U9, U10) | 3 | Delete; "state the call and go on" |
| Clothing-boot frame and extra-slot AR 0 | keywords.md frame (armor#122), armor#60, #62 | Controls did it unaided 2/3 (noise) | - | Keep, merged into the X1/X4 rule (it is the ARR convention) |
| index.jsonl | references | Topic index for the long references | 29 | Delete when references shrink |

**Untested domains (keep a short version; "untested", not "useless"):**
- Heavy-gauntlet `PerkFists<Material>` (armor#37, #65, #120, #121): no gauntlet use logged in any ledger.
- Artifact/unique classification by acquisition trace (SKILL L307-333, armor#50-52, worked-examples 4): 0 uses. Same method lives in the weapon skill; keep one shared copy (Requiem profile).
- Novel material / borrowed set keyword (armor#58, #98, #150): RB Adabelle dress only, 3 runs, 3 different answers (AR 320 elven / 200 / clothing AR 0), all debatable (ledger RB). The skill gives no rule for "dress with AR: armor or clothing?".
- Light v heavy ambiguity (armor#57): one use (RB Altar Shield, noise).
- Net-new ARMO record and forge-recipe authoring (housecarl-recipes B, E): RB authored temper only.
- Faction sets / ranged tier4, smelter recipes, ARMA disposition (armor#5), creature-skin hand-off (TR only, noise).
- Worn NonPlayable (armor#15): never loaded where it mattered (AC).
- Downstream bake lane for ARMO: 0/42 runs (U8, U13).

## 5. Corrected facts for this skill

| Fact | Status | Source |
|---|---|---|
| AR rescale is affine: `AR x 3.3 (light) / 6.6 (heavy) + 66 (body) / 18 (other parts)`, applied only when AR is **below** the slot threshold (heavy 74/27/27/35/54, light 62/18/18/26/44 for body/feet/hands/head/shield). Thresholds are triggers, not caps. Skips AlreadyReqtified (AE3B83 in ARR), NoArmorRatingRescale, TemplateArmor, clothing, and armor with no part keyword | Verified code + RFI 70/70; build proven (R12) | U6 §3, R6, R12, r3 §3 |
| "~10x vanilla" is false. Requiem's own ladder values are real live values above the thresholds (598 RFI armors keep their input AR) | Verified live + code | U6 §3 |
| Effective-AR probe: an untouched mod piece ends at the formula value. Witch Hood 21 -> 87.3 (in the light-helmet range); Arkay cuirass 36 -> 303.6 (below Steel Plate 400, so it needs a hand value) | Code formula applied (R10); ranges inferred | R10 falkreath-shades L28, FS-A1/A2 |
| A hand-set Requiem-scale AR sits above the threshold and is not re-rescaled, so reading an RFI winner for ARMO cannot double-scale (inference from the code) | Inference (code) | U6 §3 (AC 180 above threshold, left alone) |
| Clothing: Requiem keeps vanilla clothing AR (20/385 AR > 0); the build never rescales clothing | Verified live + code | U6 §2 |
| ARR's MC patch zeroes stray clothing / extra-slot AR | **House convention** (one patch; "deliberate or artefact: Aaron knows") | r3 CRITIC §1 #5, §4 |
| Ranged tier (cuirasses only) and REQ_Tempering_* are added by RFI even on AlreadyReqtified / NoArmorRatingRescale armor; baking them is redundant | Code (R6); RFI match on the ARMO-keyword path untested (R12) | R6, r3 §3, R12 |
| NonPlayable worn armor is rescaled by the build (not in the skip list; RFI holds 109 NonPlayable ARMO rescales). Tsun 29 -> 161 (Requiem's own `REQ_Light_Tsun_Body` 161); the AC reference's 180 is off-formula | Verified live + code | U6 §3, facts AC L60 |
| REQ_NULL ARMO: 10/98 zeroed (TG amulets, MG04Reward03, Miraak masks); the rest keep content | Verified live | R7, r3 §3 |
| Extra-slot accessories need `MagicDisallowEnchanting 0C27BD` (or no vanilla slot keyword); the "no WornRestrictions keyword" equivalent rests on 2 of 43 FLSTs | Multi-judge 7/7, not re-read live | U13 NM1, r3 §3, R10 MC I1/I2 |
| Extra-slot `Clothing*` keywords stack `REQ_Perk_Evasion_Agility_Unarmored` armor under real armor | Unverified (1 judge, r79; MGEF conditions not read). Do not encode as fact | U13 RO1, r3 §3 |
| Requiem temper COBJs carry a `GetCurrentTime() > 23` night gate + HasPerk (seen on a daedric weapon temper) | Verified live for 0EB860 (weapon); armor COBJs not read | U2 RB, r2 §4 |
| Light helmets top out at AR 110-120 | Multi-judge 8/8 (U3 FS1), not re-read live; agrees with the ladder (glass 110, dragonscale 120) | U3, facts #35 |
| Creature/boss skins carry AR 0; natural armor comes from `REQ_Trait_Armor_*` only | Read live by all 3 RB skill runs; multi-judge; held by no skill | U7 RB3, facts #23, R10 RB C3 |

## 6. Rewrite brief

**Purpose.** Given an ARMO in scope, pick Requiem's comparable, decide whether the build will fix it unaided (upstream), and set only what the build will not set. Say which stance the patch takes.

**Must keep (this skill):**
1. Analogue key: `REQ_<Heavy|Light>_<Material>_<Body|Head|Hands|Feet|Shield>`, chosen by `BodyTemplate.ArmorType` + part keyword + set keyword; canonical record over REQ_NULL_/REQ_Var_ (H1, H2, X7).
2. Method: copy the same-part, same-material, same-weight comparable; ratio off the cuirass only if none exists (H1, H3). One short ladder table + the ratio row.
3. **Effective-AR probe** (new): compute `AR x 3.3/6.6 + 66/18` for a below-threshold piece. If it lands on tier, an upstream patch leaves AR alone. If not, set the comparable's value (it is above the threshold and stays). Downstream adds NoArmorRatingRescale by ARR convention (§5, X5).
4. Input keywords: type, set, part, VendorItemArmor; PerkFists<Mat> on heavy gauntlets only; shield part 0965B2; keep whatever else the comparable carries (H7, X9).
5. Build-assigned keywords (ranged tier, tempering): upstream leaves them out; downstream may bake; redundant either way (X8).
6. Extra-slot / cosmetic rule (new, from U13 NM1): non-standard slot -> AR per ARR convention, no vanilla Clothing*/Armor* slot keyword or add MagicDisallowEnchanting 0C27BD; mis-tagged cloth with a cuirass keyword -> clothing frame (X1, X2, X4, H5).
7. Worn NonPlayable is in scope (X6). Skins: AR 0, natural armor via race traits (new; hand to race).
8. Temper/forge: clone every condition and item of the comparable COBJ (night gate included); armor workbench 0ADB78 (H6, X12).
9. (Dropped on verification: the "enchanted clothing: match on the same ENCH" line. MC3 was own probe with no live effect, R2 / R10 MC I9. The general "derive value from the comparable" line, armor#3 / economy#88, covers it once.)

**New items:** effective-AR probe (3); extra-slot enchant block (6); skins AR 0 line (7); a "dress with AR: functional armor or clothing?" decision rule with a default (untested domain, RB Adabelle split 3 ways). The R9 SpellList-on-Stats line belongs to the NPC skill/router, not here.

**Delete:** freshness probe, bulk-pass prose, bulk-record-jobs pointer, cap section and every "~10x"/cap line, checklist, common mistakes, notes, worked-examples.md, housecarl-recipes.md, the tier/tempering tables, ask gate, enchant routing lines, ReplaceAll templates, masters note.

**Target length:** SKILL ~80-100 lines + one ~40-line reference (keyword FormIDs, ladder table, workbench and ingredient FormIDs). Now 1,070 lines.

**Where content lives:**
| Content | Home |
|---|---|
| Analogue key, method, effective-AR probe, input keywords, extra-slot rule, NonPlayable rule, COBJ clone rule | This skill |
| AR rescale formula and triggers, "build adds ranged tier + tempering", clothing never rescaled, REQ_NULL per type, artifact acquisition trace (shared with weapons), upstream/downstream definition | Requiem profile |
| Clothing / extra-slot AR 0, NoArmorRatingRescale + baked keywords in Reqtificated patches, ESL on override-only patches | ARR list layer |
| Enumerate, disposition, reconcile, freshness, routing, fix the duplicate "~10x/caps" in `combat-resistance.md` L34 | Router |
| Call shapes, depth, compose grammar, flags-as-unions, masters, write verification, truncation warning | houseCARL |


## Verification (adversarial check, 2026-10-30)

**Changed:**
- H1 downgraded. Added R10 FS mechanical scores (effective AR from the code formula): controls that reached the cuirass got its AR right (c1, c1n pass A2); c2/c3 pass the Witch Hood untouched (87.3). The cuirass AR gap is router coverage (R2 FS1 router-only). The method's own edge: cuirass weight (A3, c1/c1n fail) and not over-setting the hood (A1, c1 fail). TNAM-template caveat added.
- H3: flagged that armor#31, cited by s1n, is the false "~10x" line.
- H4 (MC3 regen robe prices, armor#3) **removed from helped**: R2 = own probe, no skill text names the ENCH key; R10 MC I9 = no in-game effect (robes only in orphan list 000BFB). Rewrite brief item 9 dropped.
- Added RB Altar Shield as contested (judges: noise; R10 C16: skill 3/3 v control 1/3). No credit given.
- H6: the FS part is not a win (R10 K1-K3 controls pass).
- X1: added R10 MC I1 (0/40 every run but c1) and c1 as best core run; fixed the c1 group id to r79 g0 (R2 MC).
- X5: added the R1 count (3 control groups + 1 folded skill decision on the cap premise, 0 RM moved).
- X6: the armor skill was not loaded in AC; s3's skip is router rp#12 (R2 AC-L2); the skip is defensible upstream (R4). Marked latent.
- §1 overall line rewritten to match.

**Checked, no change:**
- Every armor rule with a helped/harmed row in `drill/absolutes/ledger-*.json` appears: helped #3, #10, #31, #32, #33, #34, #45, #60, #91, #107, #111, #141; harmed #18, #62, #122. (#3 now sits under "removed from helped", with the reason.)
- False facts against r3 INTEGRATED §3: ~10x/caps, REQ_NULL ARMO 10/98, ranged-tier/tempering redundant (R6), night gate (U2/R4), AlreadyReqtified AE3B83, NonPlayable rescaled: all match. Clothing AR is stated as an ARR house convention, not a Requiem rule (r3 CRITIC). INTEGRATED §3 row "copy the mod's clothing AR" is deliberately not adopted, per CRITIC. Bows x4 is weapon scope; not cited here.
- Unnecessary items against U7/R2 deciding points and the ledgers: no deleted item is load-bearing. The creature-skin line (armor#16/#17) is noise in TR; RB3 skin AR came from own probe + the race Armor-trait rule (R2), not armor text, so "skins AR 0" stays a new line for the race skill hand-off, not a kept armor rule.
- U8 load/use counts (14/21 loads, 28/151 used, 12 helped, 3 harmed) match U8 row 49.

**Final counts:** helped 6 (H1-H3, H5-H7; all single-judge or minor, H1 joint with router coverage), hurt 13 (X1-X13; 3 with judged harm: X1, X2, X3; X4 beat a live read), unnecessary 16 rows (+8 untested domains).
