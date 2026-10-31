# Rot Below (CJ03Elroy.esp): skill vs control analysis

**Verdict: BEAT.** The skill arm beat the control on what matters in game, consistently across all three runs. The biggest in-game failure is shared by both arms and does not decide the verdict.

| | control-1 | control-2 | control-3 | skill-1 | skill-2 | skill-3 |
|---|---|---|---|---|---|---|
| Judged quality (ref) | 18 (80) | 38 (82) | 30 (80) | 42 (78) | 42 (72) | **64** (70) |
| Records written | 76 | 85 | 49 | 182 | 191 | 167 |
| Cost | $3.85 | $7.03 | $3.52 | $21.68 | $24.65 | $24.23 |
| Wall time | 10.2 min | 13.7 min | 8.3 min | 15.8 min | 19.5 min | 18.4 min |
| Subagents | 0 | 0 | 0 | 7 | 6 | 6 |
| Stops to ask | 0 | 0 | 0 | 0 | 0 | 0 |

The judges disagreed on one thing. The control-3 and skill-3 judges graded the missing Reqtificator layer as minor ("equivalent if RFTI is rerun"); the other four graded it major. Comparing within the same judge style, the skill runs still win: strict judges 42/42 vs 18/38, lenient judges 64 vs 30.

---

## 1. Right and wrong, per arm (major first)

### The shared failure: the Reqtificator layer
In this list the prebuilt `Requiem for the Indifferent.esp` does not master CJ03Elroy.esp, and it is unticked in the eval profile. The list's convention is to bake the Reqtificator pass into an `Authoria - Reqtificated - <mod>` plugin. The judges found 3,239 NPCs carrying `REQ_KW_AlreadyReqtified` across 97 such plugins. The reference does this. **No run did:**

- no `RFTI_All_*` perk set, no 4 RFTI abilities, no AlreadyReqtified keyword on 61 NPCs;
- weapons written at pre-Reqtify scale (e.g. Prowler Iron Sword 7 against RFTI's vanilla Iron Sword at 42), with no `REQ_DamageType_*` and no exclusion keywords;
- no `REQ_LockpickControl` on 30 containers and doors.

The arms reached this the same way in effect but for different reasons:
- **Controls** chose it on their own. control-1 said "the Reqtificator should add these when you rebuild", control-3 said "re-run the Reqtificator", control-2 "left alone what the Reqtificator adds".
- **Skill runs** were told to. The router says "carry inputs / never hand-stamp Reqtificator outputs", ends every job with "run it through the Reqtificator", and says "Lockpicking is an SKSE DLL". skill-1 skipped the doors citing stealth.md.

So the skills did not *cause* a difference here, but they wrote the wrong call into policy (see §3).

### Where the arms differ (skill arm right, control wrong or missing)

| Point | Skill | Control | Why it matters in Requiem |
|---|---|---|---|
| **Race stat blocks copied from the Requiem analogue** (Starting, Regen, Unarmed, SkillBoost) | 3/3 right (s1 g28, s2 g12, s3 g15) | 0/3; major not_done (c1 g51, c2 g12, c3 g30) | Custom races cloned from vanilla bypass Requiem's race rework. The final boss dragon stays at 500 HP / 0.7 regen against Requiem DragonRace's 7000 / 0.02; Fungal Draugr stay at 50 HP / 1 unarmed. |
| **Gatekeeper race → Lurker** (named "Giant", BenthicLurker skeleton, used by The Librarian) | 3/3 right | 0/3 (c2 g16, c3 g26 wrong major; c1 g48 debatable) | The boss on screen is a Lurker; Giant traits give the wrong resist/armor/healing profile. |
| **Boss skins zeroed** in favour of `REQ_Trait_Armor_*` | 3/3 right major (s1 g11, s2 g25, s3 g17) | 0/3 (c2 deliberately kept them as "author tuning") | Requiem skins carry AR 0. Keeping 400 skin AR plus 800 trait AR double-stacks boss armor. |
| **Player summons → Magic Redone tiers** (15 s, 600–1200 cost, charge, NoAbsorbOrReflect) | 3/3 right major (s1 g38, s2 g35, s3 g44) | 0/3 | 60–120 s summons of boss-tier creatures are 4–8× any Requiem conjuration. |
| **REQ_NULL-dead content fixed**: Saint's Elixir rebuilt on `REQ_Alch_*` (3/3), Malkifer's Elixir → Featherfall (s1, s3), ghoul knockdown spells (3/3) | yes | none found | Requiem nulled these effects, so the items and attacks did nothing. |
| **NPC perk kits / classes** from Requiem comparables | s3 right major (g3); s1, s2 debatable; class fixes right (s2 g6, s3 g10) | c1/c3 none; c2 one draugr pack on everyone (debatable) | Requiem NPCs get their damage from skill perks. |
| **Race-keyed RFTI_Trait_\*** (draugr/skeleton/chitin/lurker) on custom-race NPCs | s2 right major (g5; the reference misses these); s1 also | none | The Reqtificator can't recognise new races, so these creatures lack WAR damage-type resistances. |
| **Hunting bow = WAR Steel Light Bow** (25 / 0.5 + bow keywords) | s1, s3 (s3 g26 right; reference wrong at 68 dmg) | c2 keyword only | Requiem bows are hand-set and excluded from rescale. |
| **Nord-clone races given the full NordRace kit** (incl. Heritage, REQ_NULL_RaceNord stripped, carry 110) | s1 | c1 none (g52 major); c2/c3 partial | Requiem humanoids have no natural health regen. |

### Where the skill arm was wrong (and the control wasn't)

| Point | Runs | Judgement | Source |
|---|---|---|---|
| **Author-fixed bosses re-levelled down** (Adabelle 200 → 120 / 50 / 100; Knights of Rot 200 → 100 / 45 / 80; skill-2's Hierophant at 60, below the mod's floor of 100) | 3/3 | s1 g16 wrong major; s2 g2/g3 wrong major; s3 g6 debatable | npc SKILL "Always … set a FIXED level", D-Boss "dragon priests sit at 50" (cited by s2) |
| **Boss spells nerfed to player-spell tiers** (Adabelle explosion 300 + 200 → 80 + 0) | s2 (and s1's Malkifer strike 100 → 30) | s2 g31 debatable major | magic SKILL step 4 "Destruction base damage identical across tier"; enchantments.md "EXPL Damage 0" |
| **Malkifer's scythe at 80 pre-scale** (about 480 after RFTI) | s3 | g25 wrong major | weapon SKILL "the wielder's tier when no twin exists" → giant club |
| BaseMass not copied with the race stats | 3/3 | s1 g29 not_done major | race Workflow B omits BaseMass |
| Small Namira spider given the giant-spider kit | s1 | g30 wrong major | analogue classification |
| REQ_NULL resistance abilities emptied instead of re-pointed to `REQ_AbHide_*` | s1, s2 | s1 g34 no_effect, s2 g32 debatable (control-1 got it right) | the race lane's creature-traits rule overrode resistance-map.md |
| Caster kits without MR Mastery perks | s1, s2 | debatable | perk-assignment "NPCs never carry Mastery gate perks" (contested) |

### Where the controls were wrong (and the skill arm wasn't)
- control-3 left all 38 PcLevelMult NPCs scaling with the player (g35, major not_done).
- control-2's PcLevelMult conversions were consistently a tier low (g3, wrong major).
- control-1 gave ordinary spiders sneak-attack immunity (g49, wrong major).
- control-3 gave Daughter of Namira medium-spider traits (g27) and gated Great Flame tempering on Ebony Smithing instead of Daedric (g18).
- No control did any spell, enchant, consumable, container or summon work. control-3 never read SPEL, MGEF or ENCH.

### What both arms got right
- Death Knight set at Requiem Ebony 500/200 with the NoArmorRatingRescale exclusion.
- Two-handed speeds.
- Weapon keyword repairs: armor-set keyword → WeapMaterialEbony, missing WeapType keywords.
- Temper recipes gated on the material perk.
- Staff tier keywords.
- Shared creature trait spells and keywords.
- Clean plugins: 0 dangling references, 0 missing masters.

### Judge artefact to discount
The control-2, skill-1 and skill-2 judges called the Great Flame recipe's `GetCurrentTime() > 23` a bug. It is Requiem's own `REQ_Temper_Weapon_Daedric_Greatsword` night gate: the skill-3 judge verified it, and control-1's mining notes it. Those runs copied it faithfully.

---

## 2. Verdict: **BEAT**

**Deciding points:**
1. **Race stat blocks:** 3/3 vs 0/3, major. This is the biggest player-facing difference outside the shared RFTI gap, and it includes the final boss.
2. **Gatekeeper → Lurker:** 3/3 vs 0/3, major.
3. **Skin AR zeroed:** 3/3 vs 0/3, major.
4. **Summons to Magic Redone tiers:** 3/3 vs 0/3, major.
5. **REQ_NULL content restored** (elixirs, knockdown): 3/3 vs 0/3.
6. **Against the skill arm:** fixed-level bosses cut in 3/3 (2 judged wrong) and skill-2's boss spell nerf. These are real, but they are fewer and smaller than points 1–5. Control-side level errors of similar size exist too (c2 wrong, c3 not done).
7. **Shared, not deciding:** the Reqtificator layer, missing in all 6 runs.

**Cost and time, reported beside the verdict:** the skill arm's median was $24.23 and 18.4 min against $3.85 and 10.2 min for the control, about 6.3× the cost and 1.8× the wall time.

---

## 3. Per skill section

| Skill / file / section | Label | Evidence | Runs |
|---|---|---|---|
| **requiem-patching** SKILL.md First step 2–3 (enumerate, triage, drop none) + reconciliation; integration-checklist item 1 | **helped** | Drove the whole-mod sweep that found SPEL/MGEF/ALCH/CONT work no control touched (summons, elixirs, ring NULLs, ghoul knockdown). 550/487/517 records reconciled. | s1 s2 s3 |
| requiem-patching Routing table (ARMA / skin split) | **helped** | Skins routed to the race lane, which zeroed them (judged right major ×3). The AR-0 rule itself was agent-inferred. | s1 s2 s3 |
| requiem-patching First step 1 freshness probe + scope-and-authority "authority rule" (authoring-style ⇒ Requiem.esp winner) | **harmed** | The BRIEF set Requiem.esp pre-Reqtify values as authority, so weapons came out at about 1/6 of list scale (s1 g3, s2 g19). The control made the same call without the skill. | s1 s2 s3 |
| requiem-patching Common mistakes "Hand-stamping a Reqtificator output" / "patch is an input"; checklist item 7; Reqtificator-vs-manual matrix | **harmed** | Told every run not to bake RFTI perks, damage-type keywords or exclusions, and to end with "run the Reqtificator". That is wrong for this list's prebuilt, non-mastering RFTI (4/6 judges: the major failure). The outcome matches the control. | s1 s2 s3 |
| requiem-patching integration-checklist item 5 "no REQ_NULL anywhere" | **helped** | The final dump grep caught the REQ_NULL knockdown spells on the ghoul race (s2, s3); no control found it. control-1 also swept the NULL rings and abilities. | s1 s2 s3 |
| requiem-patching stealth.md / checklist "Lockpicking is an SKSE DLL … new locks behave automatically" | **harmed** | s1 skipped the doors citing it. The judges show REQ_LockpickControl is attached by the build to 1,162 locks; all 30 Rot Below locks lack it. | s1 s2 |
| requiem-patching stealth.md trap recipe | noise | Tentacle trap de-levelled to flat 200, judged debatable. | s1 s2 |
| requiem-patching alchemy.md / gap table "REQ_Alch_* MGEFs" | **helped** | Saint's Elixir relinked off the crime/guild-conditioned shrine effects. Judged right; reference judged wrong. | s1 s2 s3 |
| requiem-patching economy.md vendor recipe | noise | Vendor chest re-curated; judged debatable taste. | s1 s2 s3 |
| requiem-patching vampirism-lycanthropy.md | noise | No judged difference; s2 hand-stamped the werewolf perk anyway. | s1 s2 |
| requiem-patching masters-and-null-stripping.md; routing-table quest-gating row | noise | Masters were correct in all runs, controls included; no quest gate found. | s1 s2 s3 |
| requiem-patching requiem-vision.md | unused | Read; nothing traces to it. | s1 s2 |
| **requiem-weapon-patching** SKILL 4 keywords / material checklist; 6 crafting clone | noise | Same fixes as all three controls. | s1 s2 s3 |
| requiem-weapon-patching SKILL 4 bow keywords + housecarl-recipes D + "NPCsUseAmmo" | **helped** | Bow = WAR Steel Light Bow (s3 g26 right, reference wrong). | s1 s3 |
| requiem-weapon-patching Bulk pass "wielder's tier when no twin exists" | **harmed** | Malkifer scythe 80 (s3 g25 wrong major); Daedric-tier damage on a steel scythe (s1). | s1 s3 |
| requiem-weapon-patching enchanted-and-staves.md staves / charge pool | noise | Same staff keywords as the controls. s1 kept a 10000 charge against the reference's 3000. | s1 s2 s3 |
| **requiem-armor-patching** SKILL 3 derive AR; light vs heavy; cosmetic cloth | noise | Death Knight values identical to the controls; shield readings judged right in both arms; dress debatable. | s1 s2 s3 |
| requiem-armor-patching SKILL 6 / crafting.md temper COBJs | noise | Equivalent to the controls' REQ_Tempering keywords. | s1 s2 s3 |
| **requiem-race-patching** SKILL Workflow B step 2 "copy the analogue's full record" | **helped** | Race stat blocks right ×3 vs controls major not_done ×3. | s1 s2 s3 |
| requiem-race-patching First step 3 Classify / Workflow B step 1 | **helped** | Gatekeeper → Lurker ×3 vs Giant ×3 in the controls. Counter-case: s1's small spider (g30 wrong). | s1 s2 s3 |
| requiem-race-patching Workflow A humanoid (ReplaceAll with analogue list) | **helped** | Full Nord kit on the clones; controls none or partial. | s1 s2 |
| requiem-race-patching Elite-Draugr fallback | noise | s3 Nord-body undead → draugr, judged debatable. | s3 |
| requiem-race-patching Workflow B "AttackRace = analogue" | noise | Debatable minor. | s2 s3 |
| requiem-race-patching creature-traits.md "Resist only if none" (NULL abilities counted as none) | **harmed** | Led to emptied abilities instead of AbHide re-points (s1 no_effect, s2 debatable; control-1 got it right). | s1 s2 |
| requiem-race-patching Layer B + npc trait-bridge.md Fix 2 (hand-add RFTI_Trait_\* for new races) | **helped** | s2 g5 right major (reference bug). s3 refused it under the integration matrix, a skill conflict. | s1 s2 |
| requiem-race-patching "never keep REQ_NULL" / attack data | **helped** | s1's race lane fixed the ghoul knockdown itself. | s1 |
| **requiem-npc-patching** Workflow "Always set a FIXED level" | **harmed** | Right for the 38 PcLevelMult NPCs (only c3 missed that), but applied to author-fixed 200s too: s1 g16 and s2 g3 wrong major. | s1 s2 s3 |
| requiem-npc-patching D-Boss "dragon priests sit at 50" | **harmed** | s2 Adabelle 200 → 50 citing it (wrong major). | s2 |
| requiem-npc-patching D-Boss ResistMagic30 example | noise | Debatable ×2, equivalent ×1. | s1 s2 s3 |
| requiem-npc-patching perks.md source-carried perks; Common mistakes class | **helped** | s3 kits right major; draugr class right (s2, s3); controls none or debatable. | s1 s2 s3 |
| requiem-npc-patching alternate-state templating / tick-flags / ghost keyword | noise | Minor, mixed verdicts. | s1 s2 s3 |
| requiem-npc-patching First step 5 "choose stat mode with the user" | unused | Never asked; no stop-to-ask. | s1 s2 s3 |
| **requiem-perk-assignment** SKILL 2 Derive the set | **helped** | s3 right major; s1 debatable. | s1 s3 |
| requiem-perk-assignment "NPCs never carry Mastery gate perks" | **harmed** (contested) | Judges mark the casters down for missing Mastery perks (s1 g17, s2 g4). Neither cites a vanilla Requiem NPC carrying them, so verify before changing the rule. | s1 s2 |
| requiem-perk-assignment SKILL 4 mod-PERK disposition | noise | Flagged Adabelle's Blessing; no judged effect. | s1 s2 s3 |
| **requiem-magic-patching** cost-and-magnitude summons row + SKILL 2–4 | **helped** | Summons right major ×3; controls 0. | s1 s2 s3 |
| requiem-magic-patching SKILL 4 "REQ_NULL is a silent no-op to fix now" | **helped** | Featherfall fix (s1 g43, s3 g51 major; reference bug). | s1 s2 s3 |
| requiem-magic-patching resistance-map.md ability lane | **helped** | s3 g49 right (reverted by relay in s1/s2). control-1 got there without a skill. | s3 |
| requiem-magic-patching SKILL 4 "base damage identical across tier" + enchantments.md "EXPL Damage 0" | **harmed** | s2 boss nukes cut to player tiers (g31 debatable major). | s1 s2 |
| requiem-magic-patching enchantments.md ladders | **helped** | s3 Adabelle staff = exact Requiem Icy Spear staff enchantment (g33 right major). | s1 s2 s3 |
| requiem-magic-patching perks-and-tomes / spell-archetypes / bulk-pass | noise | Debatable values; supported coverage only. | s1 s2 s3 |
| **requiem-leveled-list-patching** cell→zone sweep, ECZN shape, container curation | noise | New ECZN judged debatable (NeverResets may block vendor restock). | s1 s2 s3 |
| **requiem-ammo-patching** SKILL 3/4 | **helped** (minor) | Throwing spear 10 → 50 with Massive + NoDamageRescale ×3; controls not done. | s1 s2 s3 |
| **requiem-consumable-patching** SKILL + potion-poison-ladders.md | **helped** (minor) | Elixirs at Requiem potion scale and shape ×3; controls 0. | s1 s2 s3 |

**Unused** (never loaded, or read with no action):
- requiem-script-patching (never loaded in any run).
- weapon: sounds-and-misc.md; damage-ladder.md was superseded by live reads.
- armor: ar-ladder.md, worked-examples.md.
- ammo: all references.
- consumable: food-drink-kits.md, ingredients.md, crafting-and-integration.md.
- leveled-list: list-structure.md, merge-behavior.md, housecarl-recipes.md, worked-examples.md.
- perk-assignment: equipment-perk-map.md, caster-perks.md, class-and-creatures.md.
- npc: followers.md. race: playable-races.md.
- magic: mgef-conditions.md, mr-content-survey.md.
- router: diseases.md, food.md, standing-stones.md, exhaustion-stress.md; perks-skills.md and combat-resistance.md were read with no action.

### Missing knowledge (a skill could have supplied it)
1. **This list's Reqtificator convention.** The prebuilt RFTI output doesn't master new mods, so the patch must be an `Authoria - Reqtificated` plugin that bakes:
   - `RFTI_All_*` + 4 abilities + `REQ_KW_AlreadyReqtified` on NPCs;
   - weapons ×6 (bows ×4), crit = damage, reach ×0.7, `REQ_DamageType_*`, `RFTI_Exclusions_No*Rescale`;
   - armor tempering and ranged-tier keywords;
   - `REQ_LockpickControl` on locks.

   The router currently says the opposite. This is the single largest item. Because these are the *authoria* skills, they should know their own list. The controls even learned the exclusion-keyword half of the convention by diffing the list's Reqtificated patches.
2. **`REQ_LockpickControl` is a build-attached VMAD script.** "Lockpicking is a DLL, new locks behave automatically" is false here.
3. **BaseMass** belongs in race Workflow B, along with EquipmentFlags.
4. **Don't re-level author-fixed NPCs**, keep PcLevelMult conversions inside the author's CalcMin/Max band, and widen the boss band beyond "dragon priests at 50".
5. **Requiem templates for custom giants and vampire bosses** (EncGiant01; REQ_Template_BossSteelPlateVampire01). Neither arm did this.
6. **State the skin rule explicitly:** Requiem skins are AR 0 and natural armor comes from race traits only.
7. **REQ_NULL resistance abilities:** re-point to `REQ_AbHide_*` and don't empty them. Resolve the conflict between creature-traits.md and resistance-map.md.
8. **Cap the "wielder's tier" rule** for NPC weapons; creature weapons are not a humanoid tier.
9. **Boss signature spells ≠ player tiers.** Use MR riders plus NoAbsorbOrReflect/ManualCostCalc rather than player magnitudes (judges are split on the magnitudes themselves).
10. **Small vs giant spider clones**, and ActorTypeAnimal on frostbite-type spiders.
11. **Resolve the RFTI_Trait_\* conflict for new races** (trait-bridge says add; the matrix says never).
12. **Update the houseCARL 1.x tool names.** They caused silent translation and one non-existent tool search, but no refusals.

---

## 4. What the skills cost on this task

| | Control (median) | Skill (median) | Ratio |
|---|---|---|---|
| Cost | $3.85 | $24.23 | ~6.3× |
| Wall time | 10.2 min | 18.4 min | ~1.8× |

Per-run detail (control vs skill):
- **Output tokens:** 31k–61k vs 282k–342k. **Thinking tokens:** 17k–28k vs 125k–134k.
- **houseCARL calls:** 36–71 vs 147–177.
- **Tool errors:** 1–4 vs 8–17, mostly bash heredoc EOF failures and refused subagent report writes.
- **Subagents:** none vs 6–7 general-purpose lanes, one per domain skill. That design is the agent's own, but the router's per-record, per-domain routing invites it.
- **Stops to ask:** 0 in every run.
- **Incident:** in skill-2 a lane made an accidental real `housecarl_create`, which became the base plugin.

**What the extra cost bought:**
- 2–4× the records patched.
- Every major point the skill arm won in §2.
- Judged quality above every control run.

**What it did not buy:**
- The Reqtificator layer, which the skills told the agents to skip.
- It also added the boss re-levelling error.

---

## 5. Reference bugs (and whether run behaviour confirms them)

*Reference-bug findings removed from the public copy: they concern ARR's handmade patches and are reviewed and filed in the ARR release tracker, not here.*
