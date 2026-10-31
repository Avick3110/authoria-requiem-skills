---
title: Distillation - requiem-race-patching
date: 2026-10-30
scope: what helped, hurt, and was unnecessary in requiem-race-patching (as tested), plus the rewrite brief
inputs: scoring/synthesis/synth-work/skills/requiem-race-patching/ (SKILL.md + 5 references); drill/absolutes/requiem-race-patching.json (rule ids race#N) + ledgers + SYNTHESIS.md; drill/facts/SYNTHESIS.md; drill/process/; drill/consistency/; drill/coverage/; drill/rescore/; drill/r2/{INTEGRATED, U6, U7, U8, U10, U11, U12, U13}; drill/r3/{INTEGRATED, CRITIC, R1, R2, R5, R7, R8, R9}; scoring/synthesis/synthesis.md
---

# requiem-race-patching: distillation

Run ids: FS = falkreath-shades, RB = rot-below, TR = triumvirate. s1/s2/s3/s1n = skill runs, c1/c2/c3/c1n = controls. `gN`/`GN` = judgement group, numbered **1-based as in `scoring/judgements/<run>/judgement.md`** (the ledgers' numbering). R1 and R5 use 0-based numbers (their r26 g27 = md g28); where a 0-based id is quoted it is marked "(0-based)". Rule ids are from `drill/absolutes/requiem-race-patching.json`. **E:** = evidence, **I:** = my inference.

## 1. What it is

Patches RACE records to a Requiem analogue: humanoid races become a copy of a vanilla race (stats, skill boosts, ability bundle), creature races get the analogue's stat block and `REQ_Trait_*` spells, and the per-NPC trait perk (Layer B) is named for the npc skill. 400-line SKILL.md + 5 references (642 lines) = ~1,040 lines, 180 rules.

**Use (U8, `drill/r2/U8-unexercised/U8-unexercised.json`):** loaded in **11 of 21** skill runs: FS 4/4, RB 3/3, TR 4/4. It was never loaded in AC, FQ or MC (those tasks have no custom RACE work). In the fanned runs only the race lane loaded it.

| File | Lines | Runs read | Rules | Used | Helped / harmed rules |
|---|---|---|---|---|---|
| SKILL.md | 400 | 11 | 106 | 24 | 16 / 5 |
| creature-traits.md | 130 | 10 | 23 | 8 | 3 / 3 |
| keywords.md | 104 | 6 (FS, RB) | 9 | 2 | 1 / 0 |
| housecarl-recipes.md | 176 | 5 (FS, RB) | 19 | 0 | 0 / 0 |
| worked-examples.md | 88 | 4 (FS, RB) | 5 | 0 | 0 / 0 |
| playable-races.md | 149 | **0** | 17 | 0 | never read |

By SKILL.md section: Workflow B (creature) 11 of 21 rules used; Workflow A (humanoid) 4/12; Common mistakes 3/13; Checklist 3/14; Normalize-fully 2/5; First step 1/12; **Overview 0/8, Bulk pass 0/14, Judgment new-race 0/2, Notes 0/2**.
Note: synthesis.md says keywords.md and worked-examples.md were "never opened". U8 corrects this: they were read, but not used.

## 2. Helped

| # | Item | Source | Evidence (task, runs, judged, control) | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | **Copy the analogue's full stat block** (Starting, Regen, UnarmedDamage, SkillBoosts), even when it makes the creature far tougher | SKILL Workflow B step 2; Judgment "Normalize fully"; race#54, #36, #48, #73, #81, #39 (SkillBoost slot, FS D142 helped) | **RB1:** s1 g28, s2 g12, s3 g15 right/major. All 3 controls not_done/major (c1 g51, c2 g12, c3 g30). Example: the final-boss dragon at 500 HP vs Requiem's 7000. Controls read the analogue and declined to apply it (c3 L902 "wary of changing Starting health"; facts #20 "known, declined"). **FS2:** 4/4 right vs 2/4 (c2, c1n unaided) | **The only domain-only win of the 17 deciding points** (R2 SYNTHESIS). Multi-run, multi-judge. FS is joint with router scope | The instruction as a **permission**: "copy every stat-bearing field from the analogue's live record, even if the creature gets much tougher". Add BaseMass (see N1) |
| H2 | **Layer A: trait spells live on the RACE `ActorEffect`; copy the analogue's live list** | creature-traits.md Layer A; Workflow B step 2 bullets; race#109, #57, #58, #59, #64 | **TR2:** 4/4 right/major (r95 G5, r33 G5, r69 G5, r20 G11). 0/3 controls touched RACE. The r20 judge: values "match Requiem's winning race records almost field for field". FS s1 rows helped (G12, G13, G16, G17). RB: noise (controls did the same, c1 g45, c2 g13, c3 g24) | **Joint with the router** (R2 TR2): the router's RACE row/matrix fact "race traits are carried by the patch" started it, and this skill said which fields. Values come from the agent's own live reads in every run. **FS is consistency, not a gap:** c2 and c1n made the same calls unaided on every cited FS group (ledger FS: c2 right G0-G8, c1n right G7-G14); the FS lift is only over c1/c3, who skipped RACE on the Reqtificator belief (R2 FS2, router-side) | "Natural armour, resists and combat healing are `REQ_Trait_Armor/Resist/Healing_*` spells on the RACE; read the analogue's `ActorEffect` live and copy it." No FormID tables |
| H3 | **Classify by reading `SkeletalModel`** (skeleton/skin as a creature signal) | SKILL First step 3; race#17 | **RB2:** Gatekeeper (Name "Giant", Benthic Lurker skeleton) → Lurker, 3/3 right/major (R5, 0-based: r26 g27, r15 g11, r54 g14 = md g28/g12/g15, **the same race-package groups as H1**, so H1 and H3 share their judged credit). The reference is also Lurker. Controls chose Giant (r46 g15 wrong/major, r66 g25 wrong/major, r72 g47 debatable/major). SkeletalModel reads: **0 in every control, 2-3 in every skill run**, all after the race skill loaded | "Domain procedure + own judgment" (R2). R5 rates it among the strongest (6 judgements). **The tie-break text points the wrong way** (see X1). The agents overrode it (R2 rot-below RB2). R2 SYNTHESIS spot-check 7 softens this: the tie-break's example is a playable race, so reading it as "Giant" for a creature is a stretch, and RB2 may lean more to domain | "Read SkeletalModel, skin and clone source **before** deciding." Keep the read, reverse the tie-break |
| H4 | **Humanoid: `ReplaceAll` the `ActorEffect` with the analogue's bundle** (NoHealthRegeneration, MassEffect, Heritage, Blood) | SKILL Workflow A step 3; race#42, #36 | RB s1: Nord-clone races got the full Nord kit, g28 right/major, g32 right/minor. Controls: c1 g52 not_done/major, c2 g14 not_done/minor, c3 partial (g20 right, g21 not_done). s2 debatable (g15) | Single task, 1-2 runs. Facts #27: controls know the kit (c2 L2539, c3 L553), so this is consistency, not knowledge | One line: "a humanoid race takes its analogue's whole ability bundle, not a superset" |
| H5 | **A new race gets no Layer-B perk from the build; name it for the npc pass** | SKILL "new-race-not-recognized"; Workflow B step 5; creature-traits Layer B; race#8, #71, #67, #121 | RB s2 g5 right/major (race-keyed `RFTI_Trait_*` hand-added on 38 custom-race NPCs; the reference lacks them). TR2b s1/s2 right (r95 G1, r69 G3). There, npc trait-bridge.md was the main driver. FS hand-placed `RFTI_Trait_*`: debatable (s2 G9, s1n G3, s3 G1). **0 control hits for `RFTI_Trait_` on any task** (facts #3) | Joint with the npc skill. **Code-verified** (R5, R12): trait perks come only from `ActorAssignmentRules_<plugin>.conf` race lists, and the eval mods have none | The fact, plus one probe: "is this race in any `ActorAssignmentRules_*.conf` list? If not, the build gives its NPCs nothing." |
| H6 | **Fix a race's NULL attack spell from the analogue's Attacks** | SKILL Common mistakes REQ_NULL; race#79, #103 | RB s1: `REQ_NULL_werewolfKnockbackSpell` → `REQ_Attack_Race_Knockdown 75CD1F` (live WerewolfBeastRace), s1 g28 right. s2/s3 race lanes **missed it**; the router's final REQ_NULL scan caught it (RB5: domain 1 of 8 cells) | Single run. **The premise is false:** 0F3F0A is a working spell that Requiem edited (magnitude 0 → 100, R1). R1 keeps the result as "analogue-backed" | Reword as "copy the analogue race's attack spell", not "strip anything named REQ_NULL" |
| H7 | **Keywords as the analogue carries them** (`ActorType*`, `REQ_DropsBloodKeyword`, `REQ_MinorKnockdownImmunity`) | Workflow B step 3; keywords.md Knockdown; race#64, #154, #99 | FS s1 helped rows (G12, G13, G17). TR s2 r69 G6 right; 0/3 controls touched RACE. FS c2/c1n made the same calls unaided | Consistency. Partly ledger-only. The TR miner notes DremoraRace carries no DropsBlood; the judge did not check | One line: "copy the analogue's keywords; DropsBlood only if it bleeds" (RB N9: DropsBlood on skeletons is a reference bug) |
| H8 | **Draugr-skeleton races → DraugrRace + draugr traits** | Workflow B step 1 fallback text names DraugrRace; race#51 | FS s1 D145 (G9 right; controls not_done 4/4 on that NPC group), FS s3 D56 (G17 right). U13 FS R1: draugr-skeleton races to DraugrRace stats are a **reference omission** found by both arms | Helped as an analogue **name**, not as the fallback rule (see X6) | Folds into the skeleton-first classification; no separate rule |
| H9 | Side effect: **"add `REQ_Trait_Armor_*`" led the lanes to zero the mod's creature-skin AR** to avoid a double count | Workflow B step 2 (race#57) | **RB3:** 3/3 right/major vs 0/3 (r54 g17 right/major). Each lane read Requiem creature skins live (s3 L1459/L1495: 13 skins, all AR 0; s1 L1815/L1821; s2 L1476/L1479). c2 read the same fact and kept the skins (L1424). R2: "own probe + domain side effect" | Indirect; the skin rule is in no skill. **Bounded by X13:** the same "trait + own armour = double count" reasoning, applied to a working mod ability rather than a skin's AR, contributed to a wrong/major in FS s2 | Add it as a stated fact, scoped to skin ARMO: "Requiem creature skins are AR 0; natural armour comes only from the trait" (live in runs, 3 judges). Do not generalise it to the mod's own Ab* abilities |

**Summary.** What helped was one permission (H1), one placement fact (H2), one probe (H3) and one boundary fact (H5). In every win the values came from live reads, not skill text (R2 TR: "no level, trait or charge was copied from skill text").

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | **"Weigh the name + lore over the mesh"; "classify by Name + Keywords"** | SKILL First step 3 L81-82, Workflow B step 1, Common mistakes; race#19, #82, #50 | **FS, followed 4/4:** shades/Putrid Mass mapped by name to Corrupted Shade/draugr, debatable/major (s1 G15, s2 G13/G16, s3 G18, s1n G6/G8). c2 mapped by skeleton and behaviour graph (Spriggan): right (G2-G4), judged better than every skill run. **RB, broken 3/3:** right/major (H3). Of the 13 safe breaks, R5 rates this among the strongest. The FS rows are the firm harm evidence (ledger FS race#19 harmed x4). For RB, R2 SYNTHESIS spot-check 7 says reading the line as "Giant" is a stretch, so RB shows the rule did not help rather than that it misled | **Wrong priority, no scope.** The example (a Khajiit furstock on a Bosmer mesh) is about a playable race; applied to creatures it points away from the deciding evidence (I, from FS) | Reverse the order: skeleton, skin, clone source and behaviour graph first; then `ActorType*` keywords; name and lore last. Keep the furstock caveat **only** for playable races. Counter-case: a name-only creature with a vanilla skeleton |
| X2 | **"Healing trait needs `RegenHpInCombat`; without it the spell is inert"** | SKILL L242-243, L351, L375 (Checklist); creature-traits L33, L114; recipes D; race#59, #85, #97, #112, #125 | **False (live, U6):** 9 of the 16 Requiem races with a `REQ_Trait_Healing_*` lack the flag, DragonPriestRace among them. TR: the rule beat the live DragonPriestRace (s1 D43, s1n D26, s3 D22/D26). FS s3 L1495 also followed the rule over the live DragonPriestRace (facts #30). r95 G6 debatable, relabelled no_effect (R1). FS N13 (U13): the Putridman RegenHpInCombat flag is judged debatable 6, right 4 (r67#9 "flag does little"). Cost: 0 right/major lost (R1: in 7 groups the flag is one element of a right race package). **Ledger labels not to reuse:** FS race#125 "helped" (D58, G19 right) is package credit, not credit for the flag; RB race#125 "harmed / did_not_fire" (Healing_Lurker without the flag) is void, because the flag is not needed | Wrong fact; an absolute beat the live analogue | Delete. Replace with "copy the analogue's Flags". The Healing trait is a constant Restore Health ability (U6: Healing_DragonPriest = 2x REQ_AbHide_RestoreHealth, magnitude 40) |
| X3 | **"Add `Resist_` only if the race ships none; keep its own `Ab*` (the author's rule)"** | SKILL L239-241, Judgment, Common mistakes; creature-traits L111-113; worked-examples #1; race#58, #124, #74, #86, #176 | RB s1/s2: the race lane counted REQ_NULL-named resist abilities as "none" and relayed "don't re-add". The magic lane reverted its `REQ_AbHide` re-point and left the abilities empty (U10). s1 g34 (R1 0-based g33): judged no_effect/minor, relabelled **debatable** by R1 ("removed a working frost/poison resist and waterbreathing"). s2 g32 (R1 0-based "twin r15 g31") debatable/minor. Those are two groups, one per run. **Correct outcome = leave the working abilities as they are.** s3 (g49) and c1 (r72 g40) re-pointed to AbHide/AbShow; synthesis.md called both "right", but R1 relabels both **no_effect** (r54 g48 and r72 g39, 0-based: "same-AV re-point", "the baseline already worked"). So no run, in either arm, beat the do-nothing baseline here; s1/s2 fell below it | **Conflict** with magic resistance-map.md + the false REQ_NULL premise. Self-labelled author preference | One owner (magic). Rule: "an `Ab*` ability is the race's own if its effects work. Read the MGEFs; REQ_NULL-named MGEFs work (only PERK is emptied). Never empty a creature's resist ability." |
| X4 | **"Near-copy of the analogue"**, silent on size vs clone source | SKILL L206, L19-22; race#48, #49 | RB s1 g30 wrong/major: a small Namira spider cloned from the giant spider got the giant-spider kit (unarmed 120). s2 picked the Frostbite spider (g13, right). c1 g49 also wrong (different error) | Missing scope | "Pick the analogue that matches size and role, not just the clone source." BaseMass makes the split visible (FrostbiteSpider 0.25 vs Giant 4, R7) |
| X5 | **Race `UnarmedDamage` = the analogue's**, with no mention of the NPC-level claw trait | Workflow B step 2; creature-traits `UnarmedDamage_` row; race#54, #114 | RB: Red Skull race unarmed 40 → 20 (werewolf) without `REQ_Trait_UnarmedDamage_Werewolf6` on the NPC. s3 g13 wrong/major, s1 g31 debatable, s2 g13 right/minor. Also noted in r26 g23, r15 g7. No control cut unarmed. U10: a seam no lane owned | Missing scope. creature-traits lists `UnarmedDamage_` as a RACE (Layer A) family. For the werewolf the judges place it on the NPC (judged; not read live) | "When you copy the analogue's UnarmedDamage, check where its NPCs carry `REQ_Trait_UnarmedDamage_*` and hand that to the npc pass." Fix the Layer A table after a live read |
| X6 | **"Every creature maps; fall back to Elite Draugr; never leave one unpatched"** | Workflow B step 1, Common mistakes; race#49, #51, #82 | RB s2/s3: human-skeleton undead mapped to DraugrRace (s3: Health 300, Stamina 10000, carry 9999). s2 g15, s3 g16 debatable/major. It also contradicts #69/#70 ("ask", "say there is no clean analogue") | Hard default with no scope; internal contradiction | Fallback only after stating that no skeleton or behaviour match exists. Prefer the analogue by skeleton. Delete "never leave unpatched" |
| X7 | **"Set `AttackRace` = analogue"** vs **"preserve its Attacks"** | Workflow B step 2 vs step 4 + keywords.md; race#56 vs #66, #157 | RB s1 skipped it (rule conflict). RB s2 g14, s3 g18 debatable/minor. FS s2 G14 "minor oddity". TR s3 refused it for custom skeletons (R2500) and also skipped Starting/SkillBoosts (R2694, race#81); both unjudged (ledger TR) | Conflict + a false assumption ("these mod creatures reuse the vanilla skeleton") | Scope: only when the race uses the analogue's skeleton and has no Attacks of its own. Otherwise keep its Attacks |
| X8 | **Trait-bridge ownership**: race Layer B says hand-add or template; the router (rp#42, rp#203) says never hand-stamp | SKILL Workflow B step 5, Judgment; creature-traits Layer B; race#67, #68, #72 | RB s3's main refused the race lane's `RFTI_Trait_*` under rp#42 (ledger RB: harmed, conflicting rule; rescore: wrong under either stance). s2 did it, right/major (r15 g5). TR s3 and s1n withheld the physique perks per the router (s1n "Withheld per your instruction", r33 L864, U10); s1/s2 added them, right/major (r95 G1, r69 G3). RB s2 is r15 g5 (1-based; U10 cites it as g4). consistency SYNTHESIS: "the trait bridge needs one owner" | Contradiction between skills | One rule, stated once (code-verified, R5/R12): recognised race → never; custom race not in any `ActorAssignmentRules_*.conf` → hand-add the analogue's perk per NPC. The race skill names it; the npc skill applies it; the router links to it |
| X9 | **"REQ_NULL = inert stubs; never keep or add anything named REQ_NULL"** | Common mistakes, Judgment, Checklist; race#44, #76, #78, #79, #103 | **False for every type except PERK** (live: U6, R7, R1). In the race lane: the RB knockdown swap and the NordRace NULL strips are right only because they match the analogue (R1). FS s1n stripped a REQ_NULL perk: no_effect (perks really are empty). The race-lane harm comes through X3 | Wrong fact (shared with router rp#136/137) | Delete here. Link to one per-type REQ_NULL rule in the Requiem profile: "PERK empty; everything else works; read the fields, copy the analogue" |
| X10 | **"Add `REQ_Trait_Armor_<analogue>`: almost every creature needs it"** | Workflow B step 2; creature-traits L30, L108; race#57, #110, #123 | FS s1: not added where the chosen analogue lacks one (thinking L1029). Neutral (G15 debatable / G14 right) | Absolute vs live data; the agent sided with live data | "Add it if the analogue carries one" |
| X11 | **Skip only by field comparison; "not used by any NPC" is not a reason** | Bulk pass protocol; race#25, #24 | RB N8: the reference edited 9 races no NPC uses (dead edits, no_effect, on 2 bug lists). The runs skipped them anyway (U13). TR: 4 unused races (U12 T14) | Missing scope: it forbids a valid liveness skip. The runs ignored it (no judged harm) | Allow "no NPC or leveled actor uses this race (references query)" as a skip reason |
| X12 | **Internal contradictions** (no observed harm) | #140 (union of the **analogue's** flags) vs #32 (union of the **winner's** flags). #48/#81 "copy the whole analogue" vs worked-examples #177 (Wendigo leaves stats untouched) and #129 ("mirror exactly" but add only Armor + Healing). #6 "never hardcoded numbers" vs the tables #62, #137, #161, #164 | Consistency: the models picked one reading each time | Contradiction | Keep one statement each. Flags: "the modded race's own bits + the analogue's Requiem-relevant bits"; houseCARL owns the union mechanics |
| X13 | **"Trait + the mod's own armour = double count" carried from the race lane onto a working ability** (PLAUSIBLE) | Workflow B step 2 (race#57) + "keep own Ab*" (race#58/#124) as relayed by the lane; same reasoning as H9 | **FS s2:** the race lane added `Armor_DraugrElite` and relayed "Putridman's Resist Damage 500 would stack" (U10 FS, s2 L1899); the magic lane removed the 500. Judged right/major (r11 G33), **relabelled wrong/major by R1**: the 500 DamageResist worked; r48 G21 judged the same loss wrong/major. FS s3 relayed "size Putrid armour to draugr tier" → 400 + MR 50, relabelled debatable/minor (r98 G54, R1) | Missing scope + the false REQ_NULL premise (X9). R1 attributes the relabel to the REQ_NULL premise; the race-lane relay is the trigger (U10). Joint cause (I) | Scope the double-count rule to skin ARMO AR (H9). For a mod's working DamageResist/Resist ability: keep it, or replace it by a trait of the same size, never remove it to "avoid stacking" without a comparable |

**Not harm, though flagged elsewhere:**
- 1.x tool names: 0 refused calls in 6/6 tasks (drill/process); hygiene only.
- race#52 (ghost state on NPC, not RACE): FS s3 put ActorTypeGhost on the RACE anyway, debatable/minor "thematic, harmless".
- RB truncation (U12): each race lane capped its trait-reference query at 15-40 of 333-380 rows and concluded "armor traits are per-NPC". No clear judged harm: s3 still put the trait on the race (r54 g10 right, per U12), s2 went per-NPC (r15 g7, debatable on both sides), s1 not singled out. This is tool behaviour (houseCARL notice), but the rewrite's probe should say `types=[RACE]`.

## 4. Unnecessary

| # | Content | Source | Why | Lines | Keep-short / delete |
|---|---|---|---|---|---|
| N0 | (gap, not excess) **BaseMass missing from the copy list** | Workflow B step 2 / Checklist | RB N4: 0/14 made by any run in either arm; 5/6 judges right (4 major). FS: 1/8 runs (control-2 only, U13 SYNTHESIS #5). R7: every reference value is a Requiem race mass; the C# never sets it | +1 | **Add** (not EquipmentFlags: U13 calls those bits a no-effect artefact, despite synthesis.md) |
| N1 | Freshness probe + `set_mo2_instance` + "either winner valid" | SKILL First step 1 | **Duplicated** across all 11 skills; 0 rules used | ~16 | Delete; move to router/houseCARL |
| N2 | Race-domain authority: Races Merge, Races Redone, knockdown tweak, Resist and Regen Tweak; "never re-point to escape RfI" | First step 2; Notes; keywords Knockdown L83; Common mistakes #88 | ARR **list-layer** naming, presented as universal (#16, #21, #105). 0 used. #88 had no live case (RfI disabled) | ~35 | Move 3 lines to the ARR list layer |
| N3 | Overview two-layer model | SKILL L8-44; repeated in Judgment L298-310, creature-traits L1-14, keywords L17-33 | 0/8 rules used; stated **4 times** | ~37 (+~45 repeats) | Keep 6 lines once |
| N4 | Bulk pass protocol + coverage audit + whole-plugin checklist item | SKILL L90-151; recipes L15-43; Checklist L366 | **0/18 rules used.** Generic craft (Opus enumerates unaided; the router repeats it). TR s3 restated "patched + skipped = enumerated" and never ran it (R8) | ~95 | Keep 3 lines: vampire variants are their own rows; read each divergent sibling (frost troll H750 vs troll H300); no label-based skips. Move the generic part to the general layer |
| N5 | Judgment "Normalize fully / asymmetric abilities", Common mistakes, Checklist, Notes | SKILL L285-401 | Restates the Workflow. Common mistakes 3/13 used, Checklist 3/14, Notes 0/2 | ~115 | Delete; fold the 3-4 unique lines into the Workflow |
| N6 | housecarl-recipes.md | whole file | Read by 5 runs, **0 used**. 1.x tool shapes (0 refused calls). Flags-union, masters and verify-the-write are tool behaviour | 176 | Delete; mechanics go to houseCARL |
| N7 | worked-examples.md | whole file | Read by 4, 0 used. The Wendigo example contradicts the full-copy rule. The frost-troll finding repeats SKILL L143-148 | 88 | Delete; keep the frost-troll line |
| N8 | playable-races.md | whole file | **Never read: untested domain.** Hardcoded stat, skill-boost and FormID tables contradict "live analogy" (#6) | 149 | **Keep short (~15 lines, untested):** bundle structure (universal + Heritage + Blood + Physique/Cuisine), "the live winner beats Races.md", vampire variant, `RFTI_All_PlayableRace` GetIsRace caveat, `ArmorRace` gear path. Delete the number tables |
| N9 | Workflow A step detail | SKILL L158-203 | 4 rules used (RB Nord kit). Controls know the kit (facts #27). Real player-selectable races untested | ~46 | Keep ~10 lines |
| N10 | keywords.md: ActorType FormID table, diet section, TOC | keywords.md | Vanilla keywords the model knows; diet is ARR/F&B list content. 2 of 9 rules used (#154 helped, #157 neutral) | ~70 | Keep ~15: REQ_ race keywords, the two knockdown mechanisms, unperked-skill keywords (untested) |
| N11 | Layer-B perk tables (base + Resist and Regen Tweak taxonomy) | creature-traits L43-99 | 1 of 5 rules used, and that one is load-bearing: #121 ("assign whatever perk(s) the analogue's NPCs receive") is cited in the RB s2 g5 right/major (ledger RB). Keep its sense in the probe. ARR-instance content (#105). Code shows where the truth lives (`ActorAssignmentRules_<plugin>.conf`, R5) | ~57 | Replace with a probe ("read the analogue's race list in the confs") + 3 lines. Taxonomy → ARR list layer |
| N12 | `doNotInheritTraits` | SKILL step 3, creature-traits L100-105, keywords | 0 used; stated 3 times | ~10 | 1 line |
| N13 | "Never add `REQ_Trait_FX_*`" | SKILL L245, L347; creature-traits L36, L115; recipes L106; race#60, #83, #115, #126, #141 | Stated 5 times, no mechanism given (unverified). Cited once (FS s2 D40), neutral | ~8 | 1 line, marked "unverified" (or delete) |
| N14 | Hardcoded example values: troll bulk_apply (H300/S500/U60), creature skill boosts (Illusion 95 etc.), Nord standard | SKILL L229-263; recipes A | Values came from live reads in every win (R2). Contradicts #6 | ~25 | Delete |
| N15 | Classify-by-NPC-users, Playable-flag lens | First step 3; race#17 (users), #20 | Generic; 0 used | ~8 | 1 line |
| N16 | State-variant (ghost) section | Workflow B step 1 L218-227 | Exercised once (FS s3), overridden, harmless | ~10 | 2 lines |
| N17 | Upstream-only framing: "patch is later run through the Reqtificator", "Requiem.esp must be a master", "your patch should appear last" | Overview L13; Notes; Checklist; recipes Masters; race#2, #102, #106, #146, #148 | RACE work does not depend on the stance: RFI writes **no RACE records** (code, U6/R12). The masters/winner lines are tool behaviour | ~15 | Delete; one line: "RACE edits are the same upstream or downstream" |
| N18 | Net-new race record recipe; `AdditionalRaces` per-ARMA alternative | recipes F, E | Untested | ~20 | Delete (`ArmorRace` line kept in N8) |

**Domains the six tasks never tested** (keep a short version; "untested", not "useless"):
- player-selectable races (playable-races.md never read; RB's humanoid races were NPC-only Nord clones);
- `<Race>RaceVampire` variants;
- the ARMA race-add / `ArmorRace`;
- `RFTI_All_PlayableRace_*`;
- whole race packs (furstock families, the sibling-extrapolation trap);
- Cloak and `UnarmedDamage_` families on RACE;
- `doNotInheritTraits`;
- FX traits;
- a net-new RACE record;
- the downstream lane (0/42 runs).

**Size:** of ~1,040 lines, roughly 900 are duplicate, generic, ARR-specific, unused or untested.

## 5. Corrected facts for this skill

| Fact | Status | Source |
|---|---|---|
| The Reqtificator writes **no RACE records**. Racial-trait perks go to NPCs only via `race_any` lists in `ActorAssignmentRules_<plugin>.conf` (77 fixed FormIDs, none from mods). A custom race gets nothing | **Verified: code + RFI** | U6, R12, R5; r3 INTEGRATED §3 "Boundary". *Why* RFI has 0 RACE (`CustomRacePatching`) is unverified |
| AD3A43 (werewolf trait) is build-assigned only to WerewolfBeastRace. Hand-stamping a custom race fills a real gap (rp#203 corrected) | Verified: code/config. In-game effect not tested | R5 |
| `RegenHpInCombat` is **not** needed for a Healing trait: 9/16 Requiem Healing races lack it, DragonPriestRace among them | Verified live (data). Whether it changes in-game regen is **in-game only** | U6; r3 §3 Corrected |
| REQ_NULL is not "inert": PERK is the only type routinely emptied (147/154). `0F3F0A REQ_NULL_werewolfKnockbackSpell` was edited **up** to magnitude 100 | Verified live | U6, R7, R1 |
| RACE BaseMass: Requiem changes it (Giant 4→10, Dragon 4→20, DragonPriest 3.5→2.2, Draugr 1→1.5, Wisp 0.1→2.2, FrostbiteSpider 0.25). A clone keeps the vanilla mass; the C# never sets it. Copy the analogue's | Verified: live + code | R7, U13 RB N4 |
| Choose a creature analogue by skeleton, skin, clone source and behaviour graph before name | Multi-judge (RB 6 judgements, reference concurs; FS c2 G2-G4) | R5, ledger FS/RB |
| Requiem creature skins are AR 0; natural armour comes from `REQ_Trait_Armor_*` | Live reads inside runs (RB s3 L1459/L1495, 13 skins) + 3 judges. Not re-read in the drills. **Not** the clothing-AR convention (CRITIC caveat): this concerns creature skin ARMO only | R2 RB3, synthesis.md |
| The werewolf claw bonus is NPC-level (`REQ_Trait_UnarmedDamage_Werewolf6`), not race UnarmedDamage | Multi-judge (r54 g13, r26 g23, r15 g7); **not read live** | ledger RB, U10 |
| DropsBlood on skeleton/undead races is a reference bug | Multi-judge (c1, c3, s2 bug lists) | U13 RB N9 |
| Perks and spells reach a templated NPC only through the `SpellList` flag. So "template the creature's NPCs onto a known race, they then inherit everything" is wrong as written. Setting the NPC's **Race** to a recognised race is what makes the build's race lists apply (I) | Engine fact; outcome multi-judge (R9). The race-field part is inference from R12 | R9, R12 |
| Requiem race stat blocks; armored-troll statline | Multi-judge, not re-read live | r3 §3 "Read-backed" |
| "Resist and Regen Tweak owns Resist_/Armor_/Healing_ and the physique taxonomy" | ARR house/list fact (#105). Not re-verified in the drills | absolutes note |
| "Races.md drifts from the live winner" (Nord 120/80/100 vs 110/80/110) | Unverified in the eval (playable domain untested) | skill's own 2026-06-08 read |
| `REQ_Trait_FX_*` "misbehave on modded races" | **Unverified**; no mechanism given | none |
| `RFTI_All_PlayableRace_*` is GetIsRace-gated | Unverified (untested) | none |
| ESL: all 6 references are ESL-flagged override-only patches; 0/42 runs did it | Verified live (general; router/houseCARL, not this skill) | R7 |

## 6. Rewrite brief

**Purpose (2 lines).** Bring a mod's RACE records to a Requiem analogue: classify, copy the analogue's stat-bearing fields and trait spells from the live record, and name the Layer-B perk for the npc pass when the build cannot. RACE work is the same upstream or downstream, because the Reqtificator writes no RACE records.

**Must keep (verbatim or near):**
1. The boundary fact, at the top: "The Reqtificator writes no RACE records and gives racial-trait perks only to races listed in `ActorAssignmentRules_*.conf`. A mod's custom race gets nothing unless the patch carries it." (Text owned by the Requiem profile; quoted here.)
2. Classify: read `SkeletalModel`, skin and clone source (whose fields it copies) and the behaviour graph **first**; then `ActorType*`; name last. Furstock caveat for playable races only. Match size as well as kind (small vs giant spider).
3. The copy permission: "copy the analogue's Starting, Regen, UnarmedDamage, SkillBoosts, **BaseMass** and Flags from its live record, even if the creature gets much tougher."
4. Layer A: "`REQ_Trait_Armor/Resist/Healing_*` live on the RACE `ActorEffect`; copy the analogue's live list. Add armour if the analogue has it."
5. Skins: "Requiem creature skins are AR 0; once you add the armour trait, zero the mod's skin AR."
6. Layer B handoff: one rule, linked from the npc skill and the router (X8).
7. Humanoid: whole ability bundle, not a superset; playable races strip the mod's free powers.
8. Variants: read each sibling's own record (frost troll H750 vs troll H300); vampire variants are their own rows.

**New items (gaps):**
- BaseMass in the copy list (U13 N4, R7).
- The skin AR-0 fact (RB3), scoped to skin ARMO; a mod's working resist/DamageResist ability is kept (X3, X13).
- The `UnarmedDamage_` NPC-trait seam (X5).
- A liveness skip ("no NPC uses this race", X11).
- The `ActorAssignmentRules` probe (H5). Inference, untested: a third Layer-B option is a conf entry for the new race.
- The R9 line, adapted: "templating inherits perks and spells only with `SpellList`; to get the build's race perks, set the NPC's Race, or hand-add the perk".

**Invariants with probe queries:**
- Analogue: query the RACE with `SkeletalModel`, `Skin`, `BaseMass`, `Starting`, `ActorEffect`, `Keywords`, `Flags` for both the mod race and the candidate. The clone source is the race whose fields match.
- Trait placement: `references=[trait FormIDs]` with `types=[RACE]`, not a capped mixed query (U12).
- Liveness: `references=[race]` with `types=[NPC_, LVLN]`.
- Layer B: grep the confs for the analogue's race FormID.

**Delete:**
- the freshness probe;
- the Bulk pass protocol body;
- Judgment, Common mistakes, Checklist and Notes as separate restatements;
- housecarl-recipes.md, worked-examples.md;
- the playable number tables;
- the hardcoded troll and Nord examples;
- RegenHpInCombat, the REQ_NULL "inert/strip" lines, "Resist only if none", the Elite-Draugr hard fallback, unscoped AttackRace;
- the upstream-only master/winner lines;
- FX x5 → 1;
- doNotInheritTraits x3 → 1.

**Target length:** about 120-150 lines of SKILL.md plus one ~50-line reference. That reference holds: the creature trait families (corrected); short Layer-B notes with the conf probe; the REQ_ race keywords and the knockdown split; the short playable section, marked untested. Down from ~1,040.

**Where content lives:**

| Content | Home |
|---|---|
| Classification, analogue copy list, Layer A, skins, variants, humanoid bundle, playable short | **this skill** |
| Reqtificator boundary (no RACE; conf race lists), REQ_NULL per-type rule, RegenHp correction, analogue-key and probe pattern | **Requiem profile** (single owner; this skill links) |
| RACE row in the routing table; skin ARMA → race lane; coverage | **router** |
| Layer-B per-NPC application; `UnarmedDamage_` NPC traits; SpellList/template rule | **npc skill** (one trait-bridge section, owned there; race names the perk) |
| Races Merge as the winner; Resist and Regen Tweak physique taxonomy; F&B diet keyword; knockdown-tweak warning | **ARR list layer** |
| Write recipes, the Flags-union write mechanics, masters auto-add, verify-the-write, capped-query notice, ESL flag | **houseCARL** (tool docs and review skills) |
| "Dissect the mod fully first; enumerate and reconcile" | **general layer** |

## 7. Verification (adversarial pass, 2026-10-30)

**Checked**
- Every race-scoped row in `drill/absolutes/ledger-*.json` (rows whose rule id names race: FS 36, RB 17, TR 6, some of them joint router/npc rows; AC/FQ/MC have none). Every helped/harmed rule id appears: helped #39, #51, #54, #57, #58, #59, #64, #73, #99, #109, #121, #125, #154, #42/#36, #79/#103, #19/#82 (overrode); harmed #19, #48/#49, #58/#124, #54/#114, #125 (RB), plus the router/npc trait-bridge conflict.
- Group numbers against `scoring/judgements/{r15,r26,r54,r72}/judgement.md`: the ledgers are 1-based, R1 and R5 0-based.
- HELPED items against R2 (17 deciding points) and R1 relabels. None rests on a REQ_NULL→AbHide re-point or clothing AR 0. H6 (knockdown) is kept: R1 marks it analogue-backed.
- HURT facts against r3 INTEGRATED §3: REQ_NULL per type, RegenHpInCombat not needed, BaseMass, the Reqtificator boundary (no RACE records). The clothing-AR caveat (r3 CRITIC) and bows x4 do not apply to this skill. The skin AR-0 in H9 is creature skin ARMO only.
- UNNECESSARY items against the ledgers and U7/R2: none is the sole cause of a judged right call. #121 inside N11 is load-bearing (now noted). The bulk-pass sibling line in N4 is kept.

**Changed**
- Added a note on group numbering. H1 and H3 cite the **same** judged race-package groups (r26 g28, r15 g12, r54 g15), so they share credit.
- H2: FS marked as consistency (c2 and c1n matched unaided). The real FS gap is router-side.
- H3/X1: added R2 SYNTHESIS spot-check 7 (the RB reading of the tie-break is a stretch). FS is now the firm harm evidence for X1.
- H9: the skin-read citations were wrong ("each lane read 13"; only s3 read 13). Scoped to skin ARMO and linked to X13.
- X2: N13 corrected from "debatable 6/6" to "debatable 6, right 4". Added FS s3 L1495. Flagged two ledger labels as void: FS race#125 "helped" (package credit) and RB race#125 "harmed/did_not_fire" (the flag is not needed).
- X3: removed a double count (s1 g33 and g34 are one group, in 0-based and 1-based numbering; same for s2 g31/g32). Reversed "c1 right without skills" and "s3 right": R1 relabels both re-points no_effect, so the baseline was correct.
- X7: added TR s3 skipping Starting/SkillBoosts (unjudged). X8: r15 g4 changed to g5 (1-based).
- **New X13** (PLAUSIBLE): in FS s2, the race lane relayed "the mod's 500 DamageResist stacks with the armour trait", and the magic lane removed it. R1 relabels that group wrong/major.
- Not-harm U12 bullet: added s2 debatable. N0: added FS BaseMass 1/8 (c2 only). N11: flagged #121 as load-bearing. Rewrite brief: the skin fact is scoped.

**Final counts:** 9 helped, 13 hurt, 18 unnecessary, plus 1 gap (N0).
