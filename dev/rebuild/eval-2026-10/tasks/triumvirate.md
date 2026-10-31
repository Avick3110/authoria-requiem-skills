# Triumvirate - Mage Archetypes → Requiem: skill vs control

**Verdict: the skill arm BEAT the control.** It won on correctness of what matters in game, and it did so consistently across runs.

| Run | Judge quality | Cost | Wall | houseCARL calls | Subagents | Stop to ask |
|---|---|---|---|---|---|---|
| control-1 | 38 | $2.15 | 5.0 min | 25 | 0 | – |
| control-2 | 66 | $2.99 | 6.8 min | 35 | 0 | – |
| control-3 | 15 | $1.71 | 3.8 min | 22 | 0 | – |
| skill-1 | 72 | $18.51 | 19.7 min | 155 | 5 | no |
| skill-2 | 72 | $9.67 | 9.6 min | 86 | 5 | no |
| skill-3 | 62 | $8.09 | 12.3 min | 56 | 0 | **yes** (stat-authority mode) |
| skill-1-nothink *(extra data point)* | 72 | $16.41 | 18.3 min | 123 | 4 | no |

Medians: control $2.15 / 5.0 min, skill $9.67 / 12.3 min. That is about 4.5× the cost and 2.5× the time. The reference patch scored 50–75 depending on the judge, and every skill run beat it.

> [!note] Judge consistency
> Different judges scored the same change differently in a few places:
> - The reference's baked RFTI package is rated *debatable* by control-1 and skill-1, *right/minor* by most other judges, and *right/major* by skill-3, which notes that RFTI output is inactive in this profile.
> - skill-1's aura proc cuts (Thorns Release 100→10, Aura of Might 20→4) are rated *wrong/debatable* by the skill-1 judge and *right/major* by the nothink judge, which checked the MR patch's tooltips.
> - The Skaal chests do not appear in skill-3's judgement at all, because neither plugin touched them. The control judges rate the blacksmith chest major.
>
> I note each disagreement where it affects a call below.

---

## 1. Right and wrong, per arm

### Major points where the arms differ

| Point | Control (3 runs) | Skill (3 runs + nothink) | Why it matters in Requiem |
|---|---|---|---|
| **Summons de-levelled** (35 NPCs off PcLevelMult) | 0/3 | **4/4**: right/major (skill-3 debatable on stat details) | Requiem is a static-level world, and all 86 Summonable NPCs in Requiem.esp use NpcLevel. Left alone, a novice Gremlin matches a level-60 player. The Reqtificator does not fix levels. |
| **Custom summon race traits** (REQ_Trait_Armor/Resist/Healing on 6–7 RACEs) | 0/3 | **4/4**: right/major | Requiem puts natural armor and resistances on the RACE. Without them, under Requiem's armor formula, these summons have about zero armor. The Reqtificator doesn't know custom races. |
| **Physique/AP trait perks on new-race NPCs** | 0/3 | skill-1, skill-2 right/major; skill-3 AP perks right/major; nothink withheld | The Reqtificator only assigns `incomingDamageModifier` perks to races it knows, and the reference's baked output has none for these NPCs. |
| **MR staff keyword `Nox_KW_Staff_<School><Tier>`** | 1/3 (control-2) | 3/4 (missing in nothink) | It drives MR's 5–25% staff spell-cost reduction (`REQ_Ability_Staff_*`). The Reqtificator does not add it to modded staves. |
| Staff Value 100 / Damage 1 | 0/3 | Value 3/4; Damage 2/4 | This is MR's staff convention. Minor (economy). |
| Tree Totem tier / Iron Maiden + Life Tap | control-2 only | skill-1, skill-2, skill-3 | The reference got these wrong or skipped them. |
| **Skaal blacksmith chest** (enchanted Stalhrim revert) | **3/3** | 3/4 (missing in skill-3) | Triumvirate's vanilla-based override puts back the enchanted Stalhrim gear and ore that Requiem removed. |

### Control: what it got right and wrong, and why

**Right**
- **Skaal chests, 3/3.** Each control forwarded Requiem.esp's version of the chest and appended Triumvirate's one tome list. Every value is traceable to the load order:
  - control-1 L885
  - control-2 L1473
  - control-3 L718
- **TVR vendor buy lists (control-1).** It noticed that Triumvirate repoints 6 vanilla factions to cloned lists, which silently loses Requiem's list edits. The miner called this a "clever catch" (L725–L728).
- **Staff keywords (control-2).** It followed `Nox_KW_Staff_*` through its references to the `REQ_Ability_Staff` conditions (L1219–L1258). All 26 tiers came out right, including Tree Totem.
- **No baked Reqtificator output.** Every judge rated leaving the generated layer to the Reqtificator as fine or equivalent.

**Wrong / missed**
- **All 3 scoped to "conflicts Triumvirate wins over Requiem" and trusted the existing `Triumvirate - Magic Redone Patch.esp` as the full integration.** None opened the 41 NPC records' Configuration. All three deferred them to the Reqtificator, so summons stay player-scaled and custom races stay trait-less.
- **Staves missed in control-1 and control-3.**
  - control-1 wrongly assumed "the Praedy patch handles staves". That patch doesn't master Requiem.
  - control-3 sampled one staff and concluded "Staves need nothing" (L429–L462).
- **MR-patch gaps assumed covered.** No control caught the 2 mis-tiered HalfCostPerks, the 4 mispriced tomes or the 4 VsUndead effects missing `REQ_SunDamage`.

### Skill arm: what it got right and wrong, and why

**Right** (provenance from the miners' decision tables)
- **De-levelling, 4/4.** Cited from `requiem-npc-patching/SKILL.md` First step 3: *"A conjured/summoned actor is a combatant, not a skip."* skill-3 also cites `requiem-vision.md` pillar 1. The level values themselves came from MR summon analogues (load_order_analogue).
- **Race traits, 4/4.** Driven by `requiem-race-patching` `creature-traits.md` Layer A ("read the analogue's ActorEffect live and copy"). The skill-3 judge: values *"match Requiem's winning race records almost field for field."*
- **Trait perks (skill-1, skill-2).** Driven by `requiem-npc-patching/references/trait-bridge.md` "Fix 2 – hand-add the physique perk". The skill-1 judge: *"nobody adds them unless the patch does."*
- **Staves (skill-1, skill-2, skill-3).** Driven by `requiem-weapon-patching/references/enchanted-and-staves.md` "Staves" and "Deriving a staff's keywords". The Obedience charge fix (skill-1, skill-3) comes from the same section's "copy its keyword + value + charge".
- **MR-patch tier fixes.** These come from `requiem-magic-patching`:
  - HalfCostPerk fixes (skill-1, skill-2), from §6
  - tome prices (skill-1, skill-2), from §7
  - ChargeTime fixes (skill-1), from `cost-and-magnitude.md`
  - `REQ_SunDamage` (skill-2), from `keywords.md`
  - Rattlesnake REQ_NULL replacement, from `resistance-map.md`
- **Skaal chests and FLSTs (3/4).** Mostly derived from Requiem.esp's own records, the same way the control did it. skill-2 cites `economy.md`.

**Wrong**
- **Primal summons stripped (skill-1, nothink, skill-2).** These three runs removed the 41 RFTI/Nox perks that the MR patch had already baked onto Wolf, Raven, Snakes, Snow Leopard and Hound.
  - Judged *wrong/minor* in nothink and skill-2, *no_effect* in skill-1. A Reqtificator rerun restores the perks; without one, these animals lose every Requiem mechanics perk.
  - Provenance is skill text:
    - `requiem-patching/SKILL.md` "Common mistakes – Hand-stamping a Reqtificator output" (skill-1, nothink)
    - `masters-and-null-stripping.md` "REQ_NULL – … leave none" (skill-2, which stripped all 41 to remove 3 REQ_NULL stubs)
  - skill-1 and nothink also wrote zero-count SPCT/PRKZ lists. `requiem-npc-patching` "Common mistakes" explicitly forbids this.
- **skill-1 magnitude cuts.**
  - Draining Shroud/Mist halved, then locked with `REQ_NoMagnitudeScaling`. The expert spell now drains less than MR's Adept cloak. Judged wrong/major.
  - Thorns Release 100→10: wrong/major.
  - Holy Burst and the Fylgja subspells: debatable/major.
  - Provenance: the MR patch author's tooltips and parent spells (load_order_analogue). No skill line drove these, but the bulk pass put them in scope.
- **skill-1-nothink skipped every staff.** The routing table sends WEAP to `requiem-weapon-patching`, but the lane design never loaded that skill.
- **skill-3 missed the Skaal chests.** It wrongly credited Authoria's Container Merge, which does not touch those 2 chests. That is a major in-game regression the controls never made.
- **No skill run added `NoWeaponReachRescale`** to the 2 summon weapons that the MR patch pre-rescaled (minor), even though `scope-and-authority.md` mentions the exclusion kit.
- **Debatable tuning.** Examples:
  - Potent Leviathan is identical to Leviathan (skill-3).
  - Spirit Guardians are level 4 (skill-1) or an arbitrary 20 (skill-2) for an Adept spell.
  - The Oathbreaker race gets `RegenHpInCombat`, because the race skill's rule overrides the live DragonPriestRace.

---

## 2. Verdict: **BEAT**

**Deciding points**
1. **Summon de-levelling.** All 4 skill runs did it, judged right/major. No control did it.
2. **Custom-race traits and trait perks.** All 4 skill runs, judged right/major. No control did it.
3. **Staff MR conventions.** Consistent in 3 of 4 skill runs, against 1 of 3 controls.
4. **Skaal chests.** Roughly a wash: controls 3/3, skills 3/4.
5. **Skill-arm regressions** (Primal perk strip, skill-1 nerfs) are minor or limited to one run. They do not outweigh 1–3.

This is consistent across runs. The worst skill run (skill-3, 62) still outscores the best control (control-2, 66) on the major Requiem conversion. Its loss to control-2 comes only from the Skaal chest miss. Cost and time are reported in §4.

---

## 3. Per skill section

| Skill / file | Section | Label | Evidence | Runs |
|---|---|---|---|---|
| requiem-patching SKILL.md + integration-checklist.md §1 | First step: enumerate / triage / per-record coverage | **helped** | Exhaustive enumeration moved scope from "conflicts" to summons, races and tiers. skill-3: *"The real gap was the summoned creatures"* (R2839). The controls also found the MR patch, but stopped at 3–5 conflict records. | all 4 |
| requiem-patching SKILL.md + routing-table.md | Routing table | **helped** | It sent NPC_, RACE, WEAP and MGEF to the domain skills that produced the wins. nothink broke the table by never routing WEAP, and lost the staves. | all 4 |
| requiem-patching requiem-vision.md | Pillar 1 – de-leveled world | **helped** | skill-3 identified the 35 PCLevelMult summons as the core gap (L37, L780). | skill-3 |
| requiem-patching SKILL.md | Common mistakes – Hand-stamping a Reqtificator output | **harmed** | It lists "trait/RFTI_* perks" as outputs to leave alone. Agents read that as "strip a compat patch's baked RFTI perks" (Primal summons: wrong/minor in nothink, no_effect in skill-1). It also made nothink and skill-3 withhold the trait-bridge Fix 2 perks that the NPC skill sanctions. The benefit, not baking the RFTI package, is noise: all controls already did that. | skill-1, nothink, skill-3 |
| requiem-patching masters-and-null-stripping.md | REQ_NULL – strip, leave none | **harmed** | skill-2 stripped all 41 baked perks to remove 3 REQ_NULL stubs (wrong/minor). | skill-2 (scans in skill-1, skill-3) |
| requiem-patching integration-checklist.md | Checklist 4–7 + Reqtificator matrix | noise | The controls also ran `housecarl_check` and deferred to the Reqtificator unprompted. | all 4 |
| requiem-patching scope-and-authority.md | Authority rule / instance | noise | The controls also built on the live winner (control-2 forwarded Praedy). | skill-2 |
| requiem-patching scope-and-authority.md | "Protect hand-tuned stats with the exclusion kit" | unused | It was read, but no run added `NoWeaponReachRescale` to the pre-rescaled summon weapons (every judge: right/minor for the reference). | skill-2 |
| requiem-patching economy.md / food.md | Vendor stock; alchemists don't buy food | noise | control-1 made the identical FLST fix unaided, and all controls fixed the Skaal chests. | skill-1, nothink, skill-2 |
| requiem-patching perks-skills, standing-stones, alchemy | – | unused | Read; no action resulted. | skill-1, skill-3 |
| requiem-npc-patching SKILL.md | First step 3 – summoned actor is a combatant (+ fixed-level rule) | **helped** | Drove the 35-summon de-level in all skill runs (right/major). 0/3 controls. | all 4 |
| requiem-npc-patching npc-authority.md | Summon analogue lives in Magic Redone | **helped** | Supplied the analogue set for levels and stats (the skill-3 Lurker copy is exact; nothink's Spirit Guardians = SpectralWarrior, right/major). | skill-3, nothink |
| requiem-npc-patching SKILL.md | First step 5 – choose stat-authority mode **with the user** | **harmed** | It forced skill-3's only stop-to-ask (L627, L780). skill-1 and nothink chose without asking, and nothink's choice was judged right/major. The stop bought nothing. | skill-3 (nothink, skill-1) |
| requiem-npc-patching trait-bridge.md / SKILL "Creatures and the trait bridge" | Fix 2 – hand-add the physique perk | **helped** | skill-1 group 2 and skill-2 group 4 are right/major. 0/3 controls, and the reference lacks these perks too. | skill-1, skill-2 |
| requiem-npc-patching SKILL.md | Workflow D – ResistMagic30 on dragon priests | noise | Applied to the Magisters; debatable/minor. | skill-3 |
| requiem-npc-patching SKILL.md | Common mistakes – augment don't remove; no PRKZ/SPCT = 0 | unused | Violated by the Primal strip (skill-1, nothink wrote zero-count lists). Followed by skill-3. | skill-1, nothink, skill-3 |
| requiem-npc-patching identification.md | summon grep | noise | Supported a call already made from SKILL.md. | skill-3, nothink |
| requiem-race-patching creature-traits.md + SKILL | Layer A trait spells / Creature races step 2 | **helped** | All 4 skill runs right/major. 0/3 controls touched RACE. | all 4 |
| requiem-race-patching creature-traits.md | Healing trait ⇒ set RegenHpInCombat | noise | It overrode the live DragonPriestRace (which lacks the flag). One judge rated it debatable; the others did not object. | skill-1, nothink, skill-3 |
| requiem-weapon-patching enchanted-and-staves.md | Staves + Deriving a staff's keywords | **helped** | Keyword + Value 100 on 26 staves; Tree Totem and Iron Maiden/Life Tap right; Obedience charge fixed. Controls: keyword 1/3, Value 0/3. skill-3 ignored "Damage = 1". | skill-1, skill-2, skill-3 |
| requiem-weapon-patching enchanted-and-staves.md | The charge-pool rule | unused | Cited, but the Obedience outlier was missed and the claim that charges already matched was false. | skill-2 |
| requiem-magic-patching SKILL.md + perks-and-tomes.md | §6 HalfCostPerk + Mastery matrix | **helped** | Suggestion→Novice and Visions of Healing→Adept (right). 0/3 controls. | skill-1, skill-2 |
| requiem-magic-patching SKILL.md | §7 Spell tome value ladder | **helped** | 4 tomes repriced (right/minor). The tome names still carry wrong tier labels. | skill-1, skill-2 |
| requiem-magic-patching cost-and-magnitude.md | ChargeTime ladder | **helped** | Wildshape 0.75 and Shield of Awe 3.0 (right/minor). | skill-1 |
| requiem-magic-patching SKILL + resistance-map.md | REQ_NULL replace on creatures / replacement map | **helped** | Rattlesnake ability re-pointed (right, minor/none). | skill-1, nothink |
| requiem-magic-patching keywords.md | Damage-type table (Sun → REQ_SunDamage) | **helped** | skill-2 group 20, right/major. 0/3 controls. | skill-2 (skill-1 via analogue) |
| requiem-magic-patching bulk-pass.md | Bulk pass / no family extrapolation | **helped** | Found the Aid_01–17 family gap, the absorb keywords and the aura proc mismatch. The same pass also put skill-1's unsupported nerfs in scope. | skill-1, nothink, skill-2 |
| requiem-magic-patching spell-archetypes.md | – | unused | Read; no decision cites it. | skill-1 |
| requiem-magic-patching SKILL.md | Three-layer split (don't stamp RFTI_All_*) | noise | No control stamped RFTI_All_* either. | skill-1, skill-2 |
| requiem-leveled-list-patching SKILL.md | Tiering / disposition | noise | skill-1's 4 re-gates were dropped as wasted ops. nothink's tome gating (right/major) was derived from MR vendor chests, not this skill. | skill-1, nothink, skill-2 |
| requiem-perk-assignment SKILL.md | Mod PERK disposition | noise | Dispositions only; not judged. | skill-1, nothink, skill-2 |
| requiem-armor-patching SKILL.md | AR-0 skin skip | noise | control-2 and control-3 skipped the same skins. | skill-1, nothink, skill-2 |
| requiem-consumable-patching SKILL.md | food/ALCH | unused | No write. | skill-1, skill-2 |
| requiem-script-patching, requiem-ammo-patching | all | unused | Never loaded. | – |

### Missing knowledge (a skill could have supplied it)

1. **Leave a compat patch's baked Reqtificator output alone.** At most, strip the REQ_NULL stubs inside it. Never write a list back with count 0. Three runs got this wrong, while skill-3 and all controls left it alone. The router's hand-stamping line needs an exception for already-present baked output.
2. **Partly excluded pre-scaled weapons.** If a compat patch has `NoDamageRescale` but not `NoWeaponReachRescale`, add the missing one. No run did this, and every judge rated it right/minor for the reference.
3. **A recipe for brushed vanilla overrides that revert Requiem.** A mod that adds one entry to a Requiem-edited vendor chest wins with vanilla data. The fix is to forward Requiem's version plus the mod's delta, and to check each record's tree rather than assume a Container Merge covers it. skill-3 missed this while the controls got it 3/3.
4. **Detect the list's Reqtificator convention:** a live RFTI output, or baked `Authoria - Reqtificated - *` + `REQ_KW_AlreadyReqtified`. The skill-3 judge rated the missing generated layer major because RFTI is inactive in this profile. The freshness probe should surface this and tell the user.
5. **Proc and subspell magnitude rule.** Change a magnitude only against a same-tier Requiem/MR comparable. A tooltip or parent value is not an anchor, and adding `REQ_NoMagnitudeScaling` while lowering a value is wrong. Judges disagree on the tooltip case, so the skill should settle it.
6. **Ancestral ghost summons → `ActorTypeGhost`/`RFTI_Trait_Ghost`** (SummonFelldir), not Spirit. Noted by the skill-2 judge.
7. *(Cosmetic)* The MR "Staff of &lt;spell&gt;" renaming convention.

---

## 4. Costs of the skills

- **Money and time:** $9.67 vs $2.15 median (~4.5×), and 12.3 vs 5.0 min (~2.5×). The worst case is skill-1 at $18.51 / 19.7 min.
- **Tokens:** 61–210k output tokens vs 11–22k, and 56–155 houseCARL calls vs 22–35.
- **Subagent fan-out:** 5 general-purpose lanes in skill-1 and skill-2, 4 in nothink, none in skill-3. The lanes created coordination costs:
  - skill-1 dropped 8 LVLI re-gate ops after two lanes disagreed.
  - In nothink, lanes clobbered each other's scratch scripts (4 calls).
- **Stops to ask:** 1. skill-3 stopped for npc-patching First step 5 and resumed on "your call" by choosing Manual. The stop bought nothing.
- **Obsolete tool names:** the skills name houseCARL 1.x calls (`cross_plugin_query`, `housecarl_resolve`, `bulk_apply`). Every run translated them silently, with no refused calls, so this cost nothing here.
- **What the extra spend bought:**
  - summon de-levelling
  - custom-race traits and trait perks
  - consistent staff work
  - the MR-patch tier fixes

  None of these appear in any control run. The cheapest skill run (skill-3, $8.09, no subagents) already captured most of it. The lane fan-out in skill-1 and skill-2 roughly doubled the cost for extra magic and vendor fixes, plus skill-1's nerfs.

---

## 5. Reference bugs (and whether runs confirmed them)

*Reference-bug findings removed from the public copy: they concern ARR's handmade patches and are reviewed and filed in the ARR release tracker, not here.*

## Summary

On this task the skills clearly earn their place on correctness. The decisive Requiem conversion is de-levelling the summons, giving the custom races their traits and the trait-bridge perks, and making the staff conventions consistent. That work came straight from specific skill lines, and no control run attempted it. The router's exhaustive-coverage mandate is what carried agents past the existing Triumvirate MR patch, which all three controls treated as the finished integration. Against that, the skills caused real harm:

- **Primal perk strip.** The router's "hand-stamping" line and the REQ_NULL rule pushed 3 runs to strip the MR patch's baked RFTI perks from the Primal summons.
- **Stop to ask.** npc-patching's must-ask stat-authority instruction caused the only stop.
- **Magnitude nerfs.** The bulk pass gave skill-1 room for unsupported magnitude cuts.

The skill arm was also no better than the control on the Skaal vendor chests, and skill-3 regressed there. The gain costs about 4.5× the money and 2.5× the time. Priorities for the skill author:

1. Add an exception to the router's hand-stamping line for baked output already present in a compat patch.
2. Add a brushed-override recipe for vanilla records that revert Requiem.
3. Make the stat-authority choice the agent's to make, rather than a forced stop.
4. Add the partial-exclusion check for pre-rescaled weapons.
