# Animated Carriage → Requiem: do the authoria-requiem skills earn their place?

**Verdict: the skill arm BEAT the control**, on in-game correctness. Two of the three skill runs fixed the most important problem and no numbered control run did. Cost and time are reported separately below.

| Run | Judge quality | Cost | Wall time | Stopped to ask |
|---|---|---|---|---|
| control-1 (r96) | 22 | $3.25 | 7.6 min | no |
| control-2 (r83) | 28 | $3.18 | 5.3 min | no |
| control-3 (r84) | 28 | $2.58 | 5.4 min | no |
| control-1-nothink (r23, extra) | 55 | $3.45 | 8.0 min | no |
| **skill-1** (r63) | **68** | $11.38 | 22.5 min | yes |
| **skill-2** (r64) | **66** | $9.89 | 21.1 min | yes |
| **skill-3** (r50) | **42** | $9.73 | 23.0 min | yes |

> [!warning] Judge calibration
> Each run had its own judge, and the judges disagree about the reference's **blanket Stats+SpellList on civilians and merchants**. The control-1 and control-2 judges call it right. The skill-1, skill-2, skill-3 and nothink judges call it wrong or debatable. Reference scores range from 52 to 82 as a result. The verdict below rests on points every judge agrees on: the guard/soldier SpellList fix, de-levelling, creatures, and Tsun's Armor. It does not rest on the civilian question.

---

## 1. What each arm got right and wrong (major first)

### The deciding issue: about 60 templated combat NPCs had no Requiem perks

Animated Carriage templates its guards, soldiers, mercenaries, Silver-Hand, Companions and Nord/Orc warriors off vanilla leveled lists. They inherit **Stats** but not **SpellList**. In Requiem the combat perks live on the template actors; `EncGuardSonsTemplate`, for example, has 34 perks including `REQ_Trait_Guard`. Without the SpellList flag these NPCs fight with only an empty `REQ_NULL_ExtraDamage*` perk.

AnimatedCarriage.esm is not a master of the RFTI output, and the Reqtificator never sets template flags. So the Reqtificator does not fix this, on this load order or after a re-run.

| Run | SpellList on Stats-templated combatants | Why |
|---|---|---|
| control-1/2/3 | **not_done (major)** | Assumed the Reqtificator would handle humanoid NPCs (control-1 L732, control-2 L764, control-3 L745), even though all three agents knew perks travel through SpellList |
| control-1-nothink | right (77 records) | Copied the convention from the sister patch `Authoria - Reqtificated - Animated Ships.esp` (L71–L96) |
| skill-1 | **right (58)** | npc SKILL.md **Workflow B** "tick the flags" (mining R3588, provenance skill_text) |
| skill-2 | **right (57)** | Workflow B (R1240, R3089) |
| skill-3 | **not_done (major, 60 records)** | Treated these Stats-only NPCs as already templated (Workflow A; chain walks at R793–R823). It then stripped their NULL perk as an "owned" field (R582), so they now have **zero** perks |

### Control arm

**Right**
- **Creatures** were set to Requiem's exact statlines in every run (bear, sabre cat, dog, cow, goat, spider and more). Judges rate this equivalent to the reference's templating, major. It came from load-order analogues.
- **The Armored Troll** got the armored frost troll statline in control-2, control-3 and nothink: L22, HealthOffset 300, StaminaOffset 250. Judges rate this *better* than the reference's plain EncTroll.
- **Tsun's Armor at AR 161** in **control-1**, copied from `REQ_Light_Tsun_Body`. It is the only run in either arm to fix the armor, and it beats the reference's 180.
- control-2 forwarded Requiem's vendor-chest rework onto AC's cloned chests (right, minor).
- nothink, through the Ships analogue, also added `REQ_LockpickControl` on 21 chests and swapped the abilities to REQ_AbHide effects.

**Wrong**
- **About 150–160 templated humanoids got no SpellList or Stats inheritance** in control-1/2/3 (major; see the table above).
- **Merchants, Tsun, the Psijics, the Dark Brotherhood and the Argonians** were left on PCLevelMult or inflated autocalc in every control run. Judges rate this major (control-3 judge; nothink groups 17, 18 and 20).
- Tsun's Armor stayed at 29 in control-2, control-3 and nothink. No lockpick script on the 53 containers (minor). The skeleton was skipped in control-1/2/3. control-1's troll has StaminaOffset 575 (debatable).

### Skill arm

**Right**
- **SpellList on 57–58 guards and soldiers** in skill-1 and skill-2 (major).
- **Faction elites and named fighters got Requiem-analogue kits** in skill-1 and skill-2 (Workflow C): commanders = `CWFieldCOSons`/`CWFieldCOImperial`, Dawnguard = `EncDawnguard00Template`, Psijics on caster kits, Tsun = Requiem Tsun in skill-1. Judges rate this right and major, *better* than the reference's appearance-template inheritance.
- **skill-3 added Stats+SpellList on 20 Traits-only fighters** (major, identical to the reference). It also gave the Telvanni mage a working Requiem-MR kit, where the reference turns it into a spell-less Redoran guard (right, major).
- **Merchants moved from PCLevelMult to fixed L4–L6** in all three runs, matching Requiem's own Ri'saad (L5) and Belethor (L4).
- Creatures were right in all three runs. The Boneman got skeleton perks and a race trait in skill-1 and skill-2. skill-1 added `ActorTypeGhost` to the ghosts.

**Wrong**
- **skill-3: the 60 guards and soldiers have no SpellList** (major; see the table above).
- **skill-2: the passenger ability regressed** (major). `REQ_NULL_AbResistDamage` 300 still works, but skill-2 swapped it for `REQ_Ability_HelgenPotion_ResistDamage`, which is conditioned to Helgen during MQ101. Every driver, passenger and goods actor loses the protection. The swap was triggered by the "no REQ_NULL anywhere" rule.
- **Tsun's Armor stayed at AR 29 in all three runs** (major for skill-2, minor for the others). skill-3 triaged all ARMO as NonPlayable cosmetic (L204), and the armor skill was never loaded.
- skill-1 left the hostile Thieves 01–03 and the Grave Robber at L6 with no perks (major).
- skill-3's 17 hand-written kits ignore the NPCs' actual gear, and its levels are invented: Ebonarm 200→50, An-Xileel →10 (debatable, major). skill-3's troll copied the masked stamina 575 (wrong).
- skill-2's Tsun got a generic L55 kit instead of Requiem's L100 Tsun (debatable). No run added the lockpick script or templated the mudcrab and horse (minor).
- Removing the only perk serialised `PRKZ=0`. skill-1 shipped 84 of them; skill-2 and skill-3 rewrote the ESP binary outside houseCARL to strip them.

## 2. Verdict: BEAT

The deciding points:
1. **The guard/soldier SpellList fix** (the largest group of combat NPCs): skill 2/3, control 0/3. nothink had it, but only by copying a sister patch.
2. **De-levelling and kitting AC's own fighters, elites and merchants:** skill 3/3, at varying quality; control 0/3.
3. **Judge scores:** skill median 66 against control median 28. The weakest skill run (42) still beats every numbered control run.

What counts against the skill arm: skill-3's guard miss, skill-2's passenger-ability regression, and the Tsun's Armor fix that only a control run made. Consistency is 2 strong runs and 1 middling one. The control arm is consistently partial.

## 3. Per skill section

| Skill / file | Section | Label | Evidence | Runs |
|---|---|---|---|---|
| requiem-npc-patching SKILL.md | **B — tick the flags** (+ recipes B2) | **helped** | SpellList on 58/57 NPCs (skill-1 R3588, skill-2 R1240) and Stats+SpellList on 27 (skill-3 R75), all judged right/major. Controls: not_done | s1 s2 s3 |
| requiem-npc-patching SKILL.md | **C — replicate analogue fields** (caster spell list; vanilla→Requiem spells) | **helped** | Elite kits judged right/major (skill-1 g4, skill-2 g9). Telvanni mage right/major (skill-3 g9). skill-3's 17 kits debatable. Controls never touched these NPCs | s1 s2 s3 |
| requiem-npc-patching SKILL.md | **Bulk pass protocol** (LevelMult query, drain check) + "fixed level, remove PCLevelMult" | **helped** | Merchants de-levelled in all 3 runs (skill-1 g5, skill-2 g11, skill-3 g6). Controls left them; the nothink judge rates that major | s1 s2 s3 |
| npc references/identification.md | Step 1: stat-only civilian lane / generic civilian skip | **helped** | skill-1 vendors at L5 cite "Belethor … stat-only pass". Keeping civilians non-combatant is right per the skill-run judges (judge-dependent). skill-1 ignored the section's "any combat signal overrides" rule for the thieves | s1 s2 |
| npc references/identification.md | Alternate-state copies & spectral actors | **helped** (minor) | ActorTypeGhost on 3 ghosts (skill-1 g11, right). The wounded Dawnguard raised to L50 is debatable | s1 |
| npc references/trait-bridge.md | Mechanism / Fix 2 | **helped** (minor) | Boneman perks plus the race skeleton trait judged right (skill-1 g8/g19, skill-2 g2/g15). control-2 judge: skeleton not_done. skill-3's version debatable | s1 s2 s3 |
| requiem-npc-patching SKILL.md | **A — already templated → skip** (+ First step 5 "Templated actors remain Workflow A skips", drain-check "Stats-templated … keepers") | **harmed** | skill-3 credited 60 **Stats-only** NPCs as templated and never ticked SpellList (judge g1, major). Workflow A's heading requires Stats+SpellList, but the two other lines leave out the SpellList qualifier | s3 |
| requiem-npc-patching SKILL.md | **First step 5 — choose stat mode with the user** | **harmed** | 3/3 stops-to-ask (L430, L693, L536). Every run then chose its own recommendation, AutoCalc. That choice also kept skill-3 from writing a caster-boss MagickaOffset (R2532) | s1 s2 s3 |
| requiem-npc-patching SKILL.md | Sparse-write gate / "never PRKZ=0" | **harmed** | The runs created PRKZ=0 themselves. skill-2 and skill-3 wrote custom ESP binary rewriters (74 and 90 subrecords); skill-1's attempts were denied twice and it shipped 84. No in-game effect | s1 s2 s3 |
| npc references/housecarl-recipes.md | REQ_NULL recipe (Perks Remove by value), 1.x call shapes | **harmed** | Remove by value was refused (skill-1 R6257; skill-3 had 105 ops rejected at R3587), which led to remove-by-index and PRKZ=0. CopyFrom of a CLAS was refused | s1 s3 |
| requiem-npc-patching SKILL.md | D — Boss ("dragon priests sit at 50", Unique, ResistMagic30) | noise | skill-2's Tsun at L55 is debatable/major; skill-3's Black Knight 200→50 is debatable. No clear win or loss | s1 s2 s3 |
| requiem-npc-patching SKILL.md | Creatures & trait bridge (offsets); mounts/livestock | noise | Creatures are right in both arms. skill-3 repeated control-1's troll 575 error | s1 s2 s3 |
| requiem-npc-patching SKILL.md | Judgment: numbered NPCs collapse / Factions / flag unions | noise | An-Xileel collapse judged debatable. control-2 also preserved flags without the skill | s1 s2 s3 |
| npc references/npc-fields.md | Configuration (NpcLevel) | noise | Only use was the NpcLevel compose, which the runs worked out after the tool threw anyway | s1 s2 |
| npc references/perks.md | — | noise | Read, never cited (in skill-2 the read was denied) | s1 s2 |
| requiem-patching SKILL.md | **First step 2–3 enumerate/triage + reconciliation** | **helped** | Kept the skill runs from scoping down (skill-1 L140 "ambient scenery", skill-2 L693 "my first idea was a few obvious records") and surfaced the 117 own-stats NPCs. Controls scoped down to 8–12 records. The same triage let skill-3 cosmetic-skip Tsun's Armor | s1 s2 s3 |
| requiem-patching SKILL.md | Routing table: NPC_ → npc skill | **helped** | The only route any run followed. It is how Workflow B/C reached the agents | s1 s2 s3 |
| requiem-patching SKILL.md | Routing table: ARMO / LVLI / CONT / SPEL / RACE rows | **unused** | Never followed (skill-2 planned 5 loads and made 1; skill-1 called the extra loads "not worth the budget"). The armor skill's worn-NonPlayable rule never reached any run | — |
| requiem-patching masters-and-null-stripping.md (+ Checklist "no REQ_NULL anywhere", integration-checklist #5, npc Common mistakes) | **REQ_NULL — strip them, leave none** ("inert stubs") | **harmed** | The premise is false here: judges verified REQ_NULL MGEFs and lists still work. The rule drove skill-2's Helgen swap (major), its hood swap (wrong), skill-3's masked-perk strips against Workflow A (R4147) and all of the PRKZ=0 work. Small upside: the AbHide swaps were right/minor, and nothink made them without the skill | s1 s2 s3 |
| requiem-patching integration-checklist.md | #4 masters / housecarl_check | noise | Every control ran housecarl_check too | s1 |
| requiem-patching SKILL.md | Common mistakes: input to the Reqtificator / don't hand-stamp | noise | Controls also told the user to re-run the Reqtificator. The RFTI-perk filtering was not judged to matter | s1 s2 s3 |
| requiem-patching SKILL.md | Freshness probe; quest GetLevel gating | noise | Controls also checked load-order status. The GetLevel scan found nothing | s1 s2 s3 |
| requiem-patching economy.md, vampirism-lycanthropy.md | gap-mechanic routing | noise | Headings grepped; no action | s2 |

**Unused (never loaded or read):** requiem-armor-patching (it held the Tsun's Armor fix), requiem-leveled-list-patching (CONT), requiem-magic-patching, requiem-race-patching, requiem-script-patching, requiem-perk-assignment, and the weapon, ammo and consumable skills. Also unread: the router references `requiem-vision.md`, `scope-and-authority.md`, `routing-table.md`, `stealth.md` and `combat-resistance.md`, and the npc references `followers.md`, `worked-examples.md` and `npc-authority.md`.

### Missing knowledge (what a skill could have supplied)

1. **An NPC templated for Stats but without SpellList gets no Requiem perks**, and neither the Reqtificator nor a re-run sets the flag. That case should be stated as Workflow B, never A. Every numbered control got this wrong, and skill-3 did too with the skill loaded.
2. **A worn NonPlayable ARMO is not cosmetic.** The router's triage should say so, not just the unloaded armor skill. The fix is to match Requiem's own copy of the armor (`REQ_Light_Tsun_Body` = 161) and add `RFTI_Exclusions_NoArmorRatingRescale` when the RFTI output won't cover it. No skill mentions that keyword, and control-1 found 161 without help.
3. **REQ_NULL MGEFs and lists still work** in this load order. Replacing them is optional. If you do replace one, use an *unconditioned* ability-lane effect. The current "inert stubs" claim (router) and "protects against nothing" claim (magic skill) caused the skill-2 regression.
4. **Copy an analogue's template-resolved values**, not the masked fields on its own record. The troll stamina 575 error happened in both control-1 and skill-3.
5. **Look for existing Requiem patches of sibling mods.** The Animated Ships patch made nothink the best control run.
6. **`REQ_LockpickControl` on uncovered containers**, using the `RFTI_Prototype_LockedContainer` bindings. No skill says this, and `stealth.md` says lockpicking is "not record-patchable". Impact is minor.
7. **Check whether a mod's templates are appearance pools mixing combat archetypes** before ticking flags on civilians, merchants or mages. This is where the judges split, and the skill runs reached the right answer only through the civilian lane.

## 4. Costs

| | Control (median of 3) | Skill (median of 3) |
|---|---|---|
| Cost | $3.18 | **$9.89** (3.1×) |
| Wall time | 5.4 min | **22.5 min** (4.2×) |
| Output tokens | 21–30k | 85–102k |
| Thinking tokens | 7–15k | 34–53k |
| Cache reads | 3.9–5.3M | 20.7–23.0M |
| Stops to ask | 0 | **3/3** |
| Subagents | 0 | 0 |

- The sessions before the stop were cheap: $1.08–1.30 and 2.4–3.6 min. Most of the cost is the long analysis after the resume (about 6,000 transcript lines in skill-1).
- Overhead specific to the skills: two custom ESP binary rewriters, denied strip attempts, refused recipe shapes, and three stops that produced no new information.
- **What the cost bought:** median judge quality 66 vs 28, and coverage of the templated combatants, elites and merchants.

## 5. Reference bugs

*Reference-bug findings removed from the public copy: they concern ARR's handmade patches and are reviewed and filed in the ARR release tracker, not here.*

## Summary

On Animated Carriage the skill arm **beat** the control. The main in-game issue is about 60 templated guards, soldiers and mercenaries that inherit Requiem levels but not Requiem perks. skill-1 and skill-2 fixed it with requiem-npc-patching Workflow B. No numbered control run did, because all three assumed the Reqtificator would handle humanoids.

All three skill runs also de-levelled the merchants and gave the mod's own fighters fixed levels, and two of them built Requiem-analogue kits for the elites. No control attempted either. Judge scores are 68/66/42 against 22/28/28 (nothink 55). The extra nothink control did well only because it copied a sister-mod Requiem patch.

Creatures came out identical in both arms, so the creature guidance is noise. Skill consistency is imperfect. skill-3 misapplied Workflow A and left the 60 guards with zero perks. skill-2 followed the "no REQ_NULL anywhere" rule into a major regression, swapping a working damage-resist effect for a Helgen-only one.

The REQ_NULL rule's "inert stubs" premise is false in this load order. It drove most of the skill arm's churn, the PRKZ=0 rewrites and both NULL-related wrong calls. The stat-mode gate stopped every run to ask a question each run then answered with its own default. Because the router's domain routing was never followed, no skill run fixed Tsun's Armor, while one control matched Requiem's AR 161 exactly. The skills cost about 3.1× the money and 4.2× the time.

**Highest-value edits for the author:**
- Make "Stats-only template → Workflow B" explicit.
- Soften the REQ_NULL rule: these records are functional, replacing them is optional, and a replacement must use the right lane with no conditions.
- Make the stat-mode question non-blocking, with a default.
- Have the router surface worn NonPlayable armor instead of letting it be cosmetic-skipped.
