# falkreath-shades (Gravewind): skill arm vs control

> [!summary] Verdict: **BEAT**
> The skill runs scored **60 / 70 / 68** (skill-1-nothink: 48) against the controls' **30 / 33 / 14** (control-1-nothink: 38). The skill runs were consistently better on what matters in game: creature races, REQ_NULL creature abilities, player spells on Magic Redone frames, absorb effects, chests and consumables. The price was about 7.4x the cost and 2.5x the time ($20.22 vs $2.74 median; 18.0 vs 7.2 min). 3 of 4 skill runs also introduced 1-2 new major wrong calls.

Sources: `runs/*/judgement.*` (arm-blind judge, G# = judgement group index), `runs/*/mining.*` (D# = mining decision index, L# = trace line), `runs/*/metrics.json`. The -nothink runs are treated as extra data points.

---

## 1. Right and wrong, per arm

### What the task needed (Requiem terms)
Gravewind is a creature-and-magic dungeon mod: 10 custom races, 26 NPCs, about 25 player spells, abilities still pointing at Requiem's **REQ_NULL** MGEFs, custom absorbs, cloaks and summons, a few unique items, ingredients, self-poisons, chests and vendors. Requiem needs five things here:
- creature races on the stats and traits of Requiem's analogue race;
- abilities re-pointed off nulled effects;
- spells on Magic Redone (MR) cost, charge and duration frames;
- items on the Requiem ladders;
- the Reqtificator layer: either regenerated, or baked the way this list's `Authoria - Reqtificated` patches do it.

### Major differences between the arms

| Point | Control | Skill | Why (Requiem) |
|---|---|---|---|
| **Coverage** | 10-22 records. control-3 did only COBJ + 1 MGEF + 1 keyword (judge: "does_the_job: no", 14) | 106-123 records, all 240 gameplay records dispositioned | Gravewind's balance lives in races, abilities and spells, not in its 7 weapons |
| **Creature races** | Right in control-1-nothink and control-2 (by skeleton). **Not done** in control-1 (G26) and control-3 (G23), major | Right on the draugr, DP, lurker, golem and ballista races in all 4 (skill-1 G12-G17, skill-2 G12-G18, skill-3 G15-G20) | Vanilla-scale 50 HP / 0 stamina bodies with no natural armor die in one hit and get crippled by exhaustion under Requiem |
| **REQ_NULL abilities** | Never fixed. Judges: the Shade keeps "-90000 magic resistance", and the golem's resists do nothing (control-2 / control-3 overall) | Re-pointed to `REQ_AbHide_*` in all 4 (skill-1 G19, skill-2 G33, skill-3 G54, right/major) | Requiem nulls the vanilla Ab* resist MGEFs |
| **Player spells** | Only control-1-nothink patched them, priced from Requiem.esp instead of the MR winner: **wrong** (G26 Heavy Flame 156; G28 expert cloak 271 / 30 s). Conjures 8-10 s | MR frames: summons 15 s (skill-1 G26, skill-2 G35, skill-3 G47), Flame Cloak 320/20 s + Dispel Cloak (skill-2 G32), Fireball 240/0.75 frame (skill-2 G31) | Unpatched 60 s autocalc summons are an exploit next to Requiem's 15 s atronachs |
| **Absorb MGEFs** | Not touched | ResistMagic + REQ_Absorb + Dwarven/NoLifeDrain conditions (skill-1 G22, skill-2 G28, skill-3 G51) | Without them the absorbs drain undrainable creatures and are resisted by the wrong element |
| **Steel-plate torso** | control-1 360 / 13 (G8 debatable), control-3 untouched (G12 not_done/major) | 400 / 30 / 500 = REQ_Heavy_SteelPlate_Body in all 4 | Requiem steel plate |
| **Ballista bolt** | Left at 12 damage (control-2 G30) | 84 / AP tier 7 / NoDamageRescale (skill-2 G24, skill-3 G31) | WAR's ballista bolt |
| **Chests and consumables** | Not touched; 100-point instant restores stay | Requiem's chests copied item for item; Alchemy Redone ingredient values; Restore tier-2 potions (skill-1 G40, skill-2 G38/G43) | REQ_NULL loot list yields nothing; Alchemy Redone potions restore over 20 s |
| **Reqtificator layer** (NPC RFTI package, x6 weapons, lock scripts, AlreadyReqtified) | Not done (all) | Not done (all) | Tied. Major in 3 of 4 skill judgements because RFTI output is unticked and was built without Gravewind. The skill-2 judge calls leaving it out correct |

### Control: right
- **Smithing recipes, all 4 runs.** The enchanted-item OR gate was removed and recipes were re-gated on Requiem perks. control-3 found on its own that Requiem reuses vanilla perk FormIDs (`0CB414` = REQ_Smithing_AdvancedLightArmors) and kept Corundum on the Arkayn temper, which the reference gets wrong (control-3 G4).
- **Races by skeleton.** control-2 mapped races by skeleton/behaviour graph. Its shades and Putrid Mass on SprigganRace were judged right (control-2 G2/G3) where every skill run is only debatable. Its golem on AtronachFrostRace keeps ActorTypeDaedra (G0, reference wrong).
- **Weapons.** control-1 left weapons at base tier for RFTI's x6 rescale, which it learned by diffing RFTI output (G0-G6 right).
- **Helmet AR.** control-1-nothink set the helmet to 120 AR (G23 right, reference wrong).
- **Static level.** control-2 gave Richter's Flame a static level (G17).

### Control: wrong
- **Coverage** (major in every run, as above).
- **control-1-nothink spells.** It used Requiem.esp values instead of the live `Requiem - Magic Redone.esp`, giving two wrong/major verdicts (G26, G28).
- **Torso.** Armor rating and weight were under-scaled or untouched.
- **Other leftovers.** The bolt stays at 12 damage and the potions stay vanilla.

### Skill: right
- **Coverage and reconciliation.** Every main thread did enumerate-first worklists with per-record reconciliation (router_effect in all 4 minings).
- **Conversions.** Races, abilities, spells, absorbs, torso, bolt, chests and ingredients were all converted, as in the table above.
- **NPC stat mode.** Manual mode with AutoCalcStats removed, so stat edits actually apply (skill-2 G2 right/major where the reference is wrong; skill-3 G7).
- **Mod data bugs fixed.** The shovel's armor keyword, the REQ_NULL dragon-priest template, wrong smithing perks, and the Scarlet script property kept (skill-2 G36, reference wrong).

### Skill: wrong (major first)
1. **skill-2 G29: Spark of Life** (wrong/major). A beneficial heal-other absorb got the hostile absorb signature (Hostile + Detrimental + stagger), so it now drains its target and aggroes followers. Mining D71: "following the Requiem absorb convention", copied from MR 005B7E.
2. **skill-1 G21: Putrid Mass Durability** (wrong/major). The armor half was deleted (D76) and magic resist cut 50 -> 20 (D75, `skill_text`: `requiem-patching/references/combat-resistance.md` §Magical resistance, "x3 toward the cap").
3. **skill-1-nothink G23** (wrong/major). Scarlet Bolt / Expel Shade were priced at Adept 240 with damage halved to 20, below MR's 90-cost apprentice bolts.
4. **skill-1-nothink G15** (wrong/major). Weapons were left at base tier. The same method was judged *right* in control-1 G0 and skill-2 G20, so this is judge variance, not an arm difference.
5. **Shades and Putrid Mass mapped by name/lore** (Corrupted Shade, draugr) instead of by skeleton (spriggan). Debatable/major in every skill run; control-2 got it right.
6. **Weapon-skill perk kits on unarmed creatures**, with no ArmorPenetration trait (skill-1 G3, skill-1-nothink G3, debatable/major).
7. **The living Arkay Pyromancer was missed**: no static level in skill-1 (G4 not_done/major), no MR perks in skill-3 (G3 not_done/major).
8. **AutoCalc kept on creatures** in skill-1 and skill-1-nothink, after the lane had to guess the stat mode.

skill-3 has **no** wrong verdicts.

---

## 2. Verdict: BEAT

The skill arm wins because it does the parts of the job that matter in game. The controls mostly did not. Consistency across the three numbered runs:
- **Skill runs.** All three converted races, abilities, spells, absorbs, gear and loot, at quality 60-70.
- **Controls.** They ranged from "races + COBJ" (control-2) to "COBJ only" (control-3), at quality 14-33.

The controls' zero wrong verdicts reflect how little they touched, not better judgement. The skill arm's new errors are real: Spark of Life and the Putrid Mass ability are each exactly the kind of in-game breakage a narrow control never risks. Still, they amount to 0-2 records per run against dozens of right conversions the controls lack.

**Deciding points:**
1. Coverage driven by the router's enumerate/route/reconcile procedure.
2. Race conversions in 4 of 4 skill runs vs 2 of 4 controls.
3. REQ_NULL abilities fixed only by the skill runs.
4. MR spell frames, where the one control that tried got them wrong.
5. Partly offsetting: the new major wrongs in skill-1, skill-2 and skill-1-nothink.
6. Neutral: the Reqtificator layer is missing in both arms.

Cost and time are reported beside the verdict (section 4), not folded in.

---

## 3. Per skill section

Labels: **helped** = drove a right call the control got wrong or missed · **noise** = followed, but the control got there anyway (or the result was judged debatable) · **harmed** = led to a wrong call, a forced guess, or a tool workaround · **unused**.

| Skill / file | Section | Label | Evidence | Runs |
|---|---|---|---|---|
| requiem-patching SKILL.md + routing-table.md + integration-checklist §1 | First step 2-3, Routing table, coverage reconciliation | **helped** (decisive) | 240-272-record worklists routed to lanes, with per-record dispositions (skill-1 L1582-L1610, skill-3 L2203). Controls: 10-22 records; every control judge's worst problem is coverage | all 4 |
| requiem-patching SKILL.md | First step 1: Freshness probe | noise | Controls also ran load_order_status; no judged effect | all 4 |
| requiem-patching SKILL.md | Common mistakes: hand-stamping RFTI output | noise | Kept the skill runs off the RFTI layer, but control-1 (L801), control-2 (L1394) and control-3 (L731) decided the same unaided | 2, 3, nothink |
| requiem-patching SKILL.md | same rule, as a blanket lane rule | **harmed** | skill-1 briefs "never hand-stamp RFTI_*" (L441) suppressed the sanctioned trait bridge, leaving 8/10 races with no Layer-B perk. nothink had to re-read the skill to resolve the conflict (L894-L929) | 1, nothink |
| requiem-patching references/masters-and-null-stripping.md | REQ_NULL: leave none | **helped** | skill-2 D32: Apocrypha chest REQ_NULL loot replaced (G43 right/major); D57 template retarget (G6 right) | 2, 3, nothink |
| requiem-patching references/integration-checklist.md | §4-5 masters / REQ_NULL scan | noise | Controls also ran housecarl_check; no judged effect | all 4 |
| requiem-patching SKILL.md | Gap-mechanic routing; quest-gate flag | noise | Followers, quest gates and traps were checked; none found, nothing written | 1, 2, nothink |
| requiem-patching references/economy.md | Integration recipes | **helped** (minor) | Vendor trims: skill-1 G42 right/minor; skill-2 G44 debatable. No control touched vendors | 1, 2, 3 |
| requiem-patching references/combat-resistance.md | Magical resistance (x3 cap) | **harmed** | skill-1 D75 50->20 on GRVEAbPutridman, judged **G21 wrong/major** | 1 |
| requiem-patching references/alchemy, shouts, standing-stones, stealth | whole | noise | Used to confirm REQ_Alch MGEFs and to skip creature shouts; no judged effect | all |
| requiem-race-patching SKILL.md | Workflow B steps 2-3, "Normalize fully to the analogue" | **helped** | 20+ citations in skill-1 (D123-D143); races right/major in every skill run. control-1 and control-3 missed races entirely; control-1-nothink and control-2 matched unaided (so the gain is consistency) | all 4 |
| requiem-race-patching SKILL.md | First step 3 classify: "weigh the name + lore over the mesh" | **harmed** (minor) | Shades/Putrid mapped by name, debatable/major in all skill runs (skill-1 G15, skill-2 G13/G16, skill-3 G18, nothink G6/G8). control-2's skeleton mapping was right (G2/G3) | all 4 |
| requiem-race-patching references/creature-traits.md | Layer-B tables / new-race problem | noise | RFTI_Trait_* hand-placed; judged debatable (skill-2 G9, nothink G3, skill-3 G1) | 2, 3, nothink |
| requiem-race-patching SKILL.md | No FX traits; keep own resists; Knockdown; AttackRace | noise | Followed; skill-2's AttackRace is a "minor oddity" (G14) | all |
| requiem-npc-patching SKILL.md | First step 5: choose stat mode **with the user** | **harmed** | Subagents can't ask, so every lane guessed (skill-3 workaround "cause: skill instruction"). skill-1 and nothink kept AutoCalc, leaving creature skills/offsets dead (G1) | 1, nothink |
| requiem-npc-patching SKILL.md + npc-fields.md | Manual mode fields; creatures use offsets; Workflow C | **helped** | skill-2 G2 AutoCalcStats removed, right/major (reference wrong); skill-2 G5 Statue = EncDragonPriest, right/major; skill-3 G7 right/major | 2, 3 |
| requiem-npc-patching SKILL.md | Workflow D: Boss (ADDDC7, MO 1300) | noise | skill_text values (skill-1 D148, nothink D55); judged debatable (skill-1 G6, skill-2 G4, skill-3 G8). skill-2 D65 over-extended ADDDC7 | all |
| requiem-npc-patching SKILL.md | Fixed level / remove PCLevelMult | noise | control-2 did the same unaided (G17); skill-1 still missed the living Pyromancer (G4) | 1, 3, nothink |
| requiem-npc-patching references/trait-bridge.md | Fix 2 | noise | Debatable where applied; it contradicts the same skill's Common mistakes on 031285 (skill-2 notable) | 2, 3, nothink |
| requiem-npc-patching SKILL.md + perks.md | Spectral actors -> ActorTypeGhost | noise | "Design change, not a fix" (skill-1 G8, skill-3 G14 debatable) | 1, 3, nothink |
| requiem-npc-patching SKILL.md + housecarl-recipes.md | REQ_NULL perks / PRKZ=0 / "Remove Perks = <FormID>" | **harmed** (minor) | The REQ_NULL strip has no effect in game (skill-1 G2). The recipe was refused ("remove one BY INDEX"; skill-1 L1365/L1831, skill-3 L1819), so workarounds were needed and empty PRKZ lists remained | 1, 3, nothink |
| requiem-perk-assignment SKILL.md | Workflow 1-2: copy the race x tier kit | **harmed** | The 14-perk draugr kit went onto the unarmed Walking Mound (skill-1 D156/D157; nothink D48 adds Marksmanship to Putrid Masses). G3 debatable/major in both: inert perks, no AP trait | 1, nothink |
| requiem-perk-assignment SKILL.md (+ mod-perk-disposition.md) | 4: disposition the mod's PERKs | noise | Death Strider left and flagged | 2, 3, nothink |
| requiem-magic-patching references/resistance-map.md | The replacement map | **helped** | REQ_NULL abilities -> REQ_AbHide (skill-1 D79/D80 skill_text). G19 / skill-2 G33 / skill-3 G54 right/major; no control did it | all 4 |
| requiem-magic-patching SKILL.md + cost-and-magnitude.md | 4: Derive cost/charge/magnitude, summon anchor 15 s, ChargeTime ladder | **helped** | 37 citations in skill-1. Summons (skill-1 G26, skill-2 G35, skill-3 G47), Fireball frame (skill-2 G31) and cloak frame (skill-2 G32) all right/major. The unaided control was wrong twice (control-1-nothink G26/G28) | all 4 |
| requiem-magic-patching SKILL.md + keywords.md | 5: keywords and flags; Absorb archetype | **helped** | Absorb signature: skill-1 G22, skill-2 G28 (reference wrong), skill-3 G51, all right/major | all 4 |
| requiem-magic-patching references/keywords.md | Absorb signature, no beneficial-absorb exception | **harmed** (PLAUSIBLE) | skill-2 D71 applied it to Spark of Life, judged **G29 wrong/major** | 2 |
| requiem-magic-patching references/perks-and-tomes.md | HalfCostPerk classifier; tome value tracks tier | **helped** (minor) | Tome prices right/minor (skill-1 G46, skill-2 G37, skill-3 G45); tier perks re-pointed | all 4 |
| requiem-magic-patching SKILL.md | Uniques and novel mechanics | noise | The same 240 frame was judged right (skill-1 G23), debatable (skill-2 G30) and wrong (nothink G23) | 1, nothink |
| requiem-magic-patching SKILL.md | REQ_NULL: strip on items, **replace on creatures** | noise | Correct text, but skill-1 D76 did the opposite (G21) | 1 |
| requiem-magic-patching enchantments.md, spell-archetypes.md, bulk-pass.md | §8, innates, bulk pass | noise | ENCH rebuilds judged debatable (nothink G26, skill-2 G30, skill-3 G56) | all |
| requiem-armor-patching SKILL.md | 3: Derive AR/value/weight (SteelPlate spine) | **helped** | 400 / 30 / 500 right/major in all 4 (skill-1 G33 etc.); control-1 G8 360/13 debatable, control-3 G12 not_done | all 4 |
| requiem-armor-patching SKILL.md + keywords.md | 4: keywords; jewelry frame | noise | ClothingNecklace right/minor. skill-1 dropped the hood set keyword (G34 debatable). control-1-nothink matched the helmet AR unaided | all |
| requiem-ammo-patching SKILL.md (+ keywords.md "The split") | 4: Keywords, creature ammo | **helped** | Bolt 84 / T7 / NoDamageRescale (skill-2 D11, G24 right/major; skill-3 G31). control-2 G30 left it at 12 | all 4 |
| requiem-ammo-patching SKILL.md | 5: Projectile bolt profile | noise | Skill and live PROJ disagree on Supersonic; result no_effect / debatable | 1, 3 |
| requiem-weapon-patching SKILL.md | Workflow 1-4, 3 stats, 4 keywords, 6 recipes, enchanted / charge-pool | noise | control-1 got the same base tiers, keyword fixes and COBJ fixes unaided (G0-G6, G15-G19); all controls fixed COBJs | all 4 |
| requiem-weapon-patching enchanted-and-staves.md | Staves: Nox_KW_Staff | **helped** (minor) | skill-2 G23 right ("the substantive part"); no control did it | all 4 |
| requiem-consumable-patching ingredients.md + SKILL §3 | Ingredient payload; duration per-MGEF | **helped** | skill-2 G38 right/major (AR modal values); skill-1 G37/G38; skill-3 G38/G39 | all 4 |
| requiem-consumable-patching potion-poison-ladders.md | Restore tier 2 | **helped** (minor, mixed) | skill-1 G36 / skill-2 G39 right; skill-3 G37 / nothink G31 debatable | all 4 |
| requiem-consumable-patching crafting-and-integration.md | COBJ gate (AlchemicalLore1) | noise | "Invented restriction on a custom bench", debatable (skill-1 G45, skill-2 G40) | all |
| requiem-leveled-list-patching containers-and-zones.md + Notes | How to patch a container | **helped** | Requiem chests copied item for item (skill-1 D171-D175, G40 right/major; skill-3 G43) | all 4 |
| requiem-leveled-list-patching SKILL.md | De-levelling triage | **helped** (minor) | LVLI 3->1 right/minor in all; controls not_done | all 4 |
| requiem-leveled-list-patching SKILL.md | Encounter zones: leave existing | noise | Skipped; nothing judged | all |
| requiem-script-patching SKILL.md + follower-registration.md | Follower registration, script sweep | noise | Not a follower; no ops | 1, 2, nothink |
| all domain skills + housecarl-recipes.md | houseCARL 1.x tool names and write shapes | **harmed** (minor) | Silently remapped to housecarl_records / housecarl_apply, with no refusals. Remove-by-value was refused (skill-1, skill-3) and the Archetype Set was mis-shaped (nothink), costing retries | all 4 |

**Unused** (never opened, or read with no action):
- **requiem-patching:** requiem-vision.md (the router says to read it once; 3 of 4 main threads skipped it), food, diseases, exhaustion-stress, perks-skills, vampirism-lycanthropy.
- **requiem-perk-assignment:** class-and-creatures.md, caster-perks.md, equipment-perk-map.md, boundary-and-write-shapes.md.
- **requiem-magic-patching:** mgef-conditions, mr-content-survey, worked-examples.
- **requiem-weapon-patching:** damage-ladder, keywords, sounds-and-misc.
- **requiem-ammo-patching:** all references.
- **requiem-leveled-list-patching:** merge-behavior.
- **requiem-script-patching:** nox-runtime-map, req-core-scripts, vmad-and-compile.
- **requiem-consumable-patching:** food-drink-kits.
- **requiem-race-patching:** playable-races, worked-examples.
- **requiem-npc-patching:** npc-authority, identification.

> [!warning] Two unread files held what the runs missed
> `class-and-creatures.md` (L84-L90) documents trait-only creature kits such as the giant's `REQ_Trait_ArmorPenetration75`. That bears on skill-1 G3. `caster-perks.md` covers NPC destruction-mage perks, which bears on skill-3 G3 (Richter got none).

### Missing knowledge
1. **This list's Reqtificator convention.** The `Authoria - Reqtificated` patches bake RFTI output and stamp `REQ_KW_AlreadyReqtified`, and here the RFTI output is unticked and was built without Gravewind. The router's blanket "inputs, never outputs" rule never asks the agent to detect this, or to bake the output or loudly require a regeneration. This is G0 not_done in all 8 runs, major for 3 of 4 skill judges (the skill-2 judge dissents).
2. **Map creature analogues by skeleton first.** The race skill says the opposite ("weigh the name + lore over the mesh"). control-2 was right, and every skill run was debatable.
3. **Unarmed creatures need a trait-only / ArmorPenetration kit**, not the weapon superset. The perk SKILL.md says "copy the whole kit", and the counter-example sits in an unread reference (skill-1 G3).
4. **Beneficial absorbs stay non-hostile.** The absorb signature needs an exception (skill-2 G29).
5. **Scope the x3 magic-resist rule** to racial abilities. Re-point a creature's own resist ability at its authored magnitude, and replace its physical half rather than deleting it (skill-1 G21).
6. **Stats-template NPCs and human casters.** Fix their levels on the template route and give casters MR caster perks (skill-1 G4, skill-3 G3).
7. **RFTI_Exclusions_NoArmorRatingRescale.** The skills are silent (skill-1 gear lane L1018). The controls learned it from the list's own Gryphonknight patch.
8. **A subagent-safe stat-mode default:** manual for creatures, as Requiem's own creatures and bosses use (skill-2 G2 / skill-3 G7 right, skill-1 / nothink kept AutoCalc).
9. **houseCARL 2.x write shapes**, for example deleting a whole perk list (empty PRKZ left on 3-4 NPCs).

---

## 4. Costs of the skills on this task

| | control (median) | skill (median) | ratio |
|---|---|---|---|
| Cost | $2.74 (2.74 / 5.00 / 2.61) | $20.22 (18.68 / 20.82 / 20.22) | ~7.4x |
| Wall time | 7.2 min (6.4 / 9.9 / 7.2) | 18.0 min (18.0 / 14.4 / 19.4) | ~2.5x |
| Output tokens | 25-38k | 237-274k (thinking 105-116k) | ~8x |
| Cache reads | 4-10M | 35-39M (~90% in subagents) | ~5x |
| Subagents | 0 | 5 / 8 / 5 (nothink 4) | |
| houseCARL calls | 37-54 | 150-174 | ~3.5x |
| Dead ends / wasted calls | 5-10 / 6-11 | 11-24 / 13-30 | |
| Stops to ask | 0 | **0** | |

The -nothink runs (control $3.87 / 6.9 min; skill $19.52 / 18.5 min) do not change the picture.

**What it bought:** quality 60-70 against 14-33, and about 5-10x more correctly converted records: races, abilities, spells, absorbs, gear, ammo, chests and ingredients.

**What it also bought:**
- 1-2 new major wrong calls in 3 of 4 runs.
- Lanes forced to guess where the NPC skill demands asking the user. There were no actual stops, but every NPC lane logged "can't ask".
- Retries from houseCARL 1.x recipe shapes.

The subagent fan-out was the agents' own design. The router does not ask for it, and it accounts for most of the token bill.

---

## 5. Reference bugs (judge-found) and what the runs did

*Reference-bug findings removed from the public copy: they concern ARR's handmade patches and are reviewed and filed in the ARR release tracker, not here.*

## Summary
- **Verdict.** The skill arm **BEAT** the control on this whole-mod creature-and-magic patch, consistently across all three runs.
- **The router is the decisive piece.** Its enumerate -> route -> reconcile procedure turned a 10-22-record gear/recipe patch into a 106-123-record conversion.
- **Sections that helped.** These domain sections supplied right calls the controls missed or got wrong:
  - resistance-map.md (REQ_NULL abilities);
  - the magic skill's cost frames and absorb signature;
  - the armor SteelPlate spine;
  - the ammo keyword set;
  - container copying;
  - the Alchemy Redone ingredient payload.
- **The race skill** made race conversion consistent, but two controls did as well unaided. control-2's skeleton-first mapping beat the skill's name-and-lore rule.
- **Noise.** The weapon, COBJ, freshness-probe, checklist and RFTI-rule sections matched what the controls did from load-order reads alone.
- **Harm.** Five things hurt:
  - the x3 magic-resist line (skill-1 Putrid Mass);
  - the absorb signature with no beneficial exception (skill-2 Spark of Life, plausible);
  - "copy the whole race x tier perk kit" on unarmed creatures;
  - "choose the stat mode with the user", which subagents can't do;
  - the blanket RFTI rule suppressing the trait bridge (skill-1).
- **The largest gap is shared by both arms:** neither baked or flagged this profile's missing Reqtificator layer.
- **Cost.** About 7.4x the cost and 2.5x the time, driven by 5-8 subagents. There were no stops to ask.
