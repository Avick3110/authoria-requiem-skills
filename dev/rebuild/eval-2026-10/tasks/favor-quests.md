# Favor Quests Separated → Requiem: skill evaluation

**Verdict for the skill arm: LOST.** The control was right in 4 of 4 runs and the skill arm in 2 of 3. The skill arm's one failure was caused by the skills themselves. Its two good runs tied the control, but none of their right calls came from skill text.

| Run | Plugin | What it patched | Judge quality | Works in game | Cost | Wall |
|---|---|---|---|---|---|---|
| control-1 (r80) | 27 QUST | RewardAmount → Missives globals | 90 | yes | $1.79 | 326 s |
| control-1-nothink (r22) | 27 QUST | same | 88 | yes | $3.67 | 403 s |
| control-2 (r75) | 27 QUST | same | 90 | yes | $2.76 | 357 s |
| control-3 (r29) | 36 QUST | same + objective text on all 36 quests | 86 | yes | $2.67 | 428 s |
| skill-1 (r27) | 27 QUST | same | 90 | yes | $2.34 | 337 s |
| skill-2 (r70) | 27 QUST | same | 90 | yes | $2.41 | 350 s |
| **skill-3 (r57)** | **13 LVLI** | **orphan gold lists, values off Requiem's tiers** | **45** | **partly: bounties still pay 0** | $1.68 | 222 s |
| *reference* | 13 LVLI | orphan gold lists, Requiem-correct values | 10–15 (60 in the skill-3 judgement) | no effect | | |

## The Requiem facts that decide this task

- FQS copies vanilla BQ01–BQ04 into 36 per-hold quests. Each one pays through `BQScript.RewardPlayer()`.
- The `BQScript.pex` that wins in this load order comes from **Headhunter – Bounties Redone.bsa**. It pays `LeveledReward` if that is bound, and otherwise `Gold001 × RewardAmount.GetValue()`, where `RewardAmount` is a **GlobalVariable**.
- Requiem.esp sets vanilla BQ01–04 to flat 500/500/1000/3000 using an *Int* property. The live stack (Requiem – Headhunter + Missives → Authoria SRC patch) binds the GlobalVariables instead: `_M_GlobalRewardKillBandit` (500), `KillGiant` (1000) and `KillDragon` (3000).
- The 27 FQS BQ01–03 clones bind neither property, so **they pay 0 gold**. An existing Authoria Headhunter patch already fixes the 9 BQ04 clones.
- The 13 `LItemRewardBQ*` lists are **referenced by nothing**, so editing them has no effect.

## 1. Right and wrong per arm

### Control (4 runs: 4 of 4 right)

**Right, major (all 4):**
- Bound `RewardAmount` as an *Object* property on the 27 clones: BQ01 and BQ02 → KillBandit (500), BQ03 → KillGiant (1000).
- The judge scored this right/major in every run.
- Mining provenance is **load_order_analogue** in every run: copied from the live vanilla BQ01–03 winners (control-1 L545–549, nothink L164, control-2 L709/L796, control-3 L578).
- Every run picked the GlobalVariable type over Requiem.esp's Int because it checked the live script:
  - nothink, control-2 and control-3 decompiled it from the BSA;
  - control-1 read the declared type from `housecarl_check findings=unbound` (L642).

**Right (all 4):**
- Left BQ04 to the existing Headhunter patch (control-3 carried that binding forward).
- Left the orphan lists alone after reverse-reference checks.
- control-2 had first assumed the lists needed work (L246), then dropped the idea.

**Right, minor (control-3 only):** added Requiem.esp's ` in <Alias=Hold>` objective wording to all 36 quests. This is cosmetic.

**Wrong:** nothing material.
- Debatable: per-template tiers rather than per-creature rates. The judge calls this defensible.
- Hygiene: none is ESL-flagged.
- control-1 overstated "no Requiem.esp overlap" (it missed 4 CELLs).

### Skill (3 runs: 2 of 3 right)

**Right, major (skill-1, skill-2):** the identical 27-quest fix, with the same values and the same Object type.
- Mining provenance is **load_order_analogue**, not skill_text (skill-1 L524/L603/L646, skill-2 L479/L567/L680).
- Both found the dead lists through a generic reverse-reference query (skill-1 L252, skill-2 L705).
- Both found the bug through VMAD reads and a BSA decompile (skill-1 L452/L646).
- A grep of `skills/` for bounty / BQScript / RewardAmount / quest reward finds nothing.

**Wrong, major (skill-3):** it never wired the reward.
- It spent its whole patch on the 13 orphan lists (judgement groups 1–8, all no_effect).
- The 27 bounties still pay 0 gold (group 9, not_done, **major**).
- It never examined how the lists are consumed, the quest VMAD, the Headhunter stack, or the existing Authoria Headhunter patch (mining scope, not_considered).
- The cause is in the skills:
  - the router's LVLI row sent the lists to `requiem-leveled-list-patching` (L225);
  - that skill's *"mod's OWN new lists … ship verbatim, level-gates and all, and you must de-level them by hand"* rule framed dead lists as live player-facing content (L271).
- Its report says so: *"Without the skills I would have left them as just gold."* In this case, leaving them alone would have been right.

**Wrong, minor if the lists were live (skill-3, 11 of 13 lists):** the values ignore Requiem's tiers.

| List | skill-3 | Requiem tier |
|---|---|---|
| Bandit | 250 | 500 |
| BQ02 Beast | 150 | 500 |
| BQ02 Forsworn | 300 | 500 |
| BQ02 Draugr | 1250 | 500 |
| Giant / Vampire | 1500 | 1000 |
| BQ03 Beast | 150 | 1000 |
| Falmer / Draugr / Hagraven | 1250 | 1000 |

- The result is an inverted curve: a BQ03 beast pays less than a BQ01 bandit.
- Provenance is **source_mod_data**: each list's level-20 rung, with the rung choice inferred from Requiem's `TGRewardGold` collapse (L329–330, L376, L774).

**Honesty slips:**
- skill-2 reported a REQ_NULL pass, but its query had errored (L1064).
- skill-1 claimed no Requiem.esp overlap after sampling 10 formids (it missed 4 CELLs).
- skill-3 miscounted the BQ04 gates.

### Where the arms differ on a major point

Only one point differs: **skill-3's failure to wire RewardAmount.** No control made this mistake. Its root cause is the skill route (router LVLI row → leveled-list de-levelling rule) running before any check that the lists are live.

skill-2 was pulled toward the same route: it planned to load the leveled-list skill at L154. It escaped only because a generic `references=` query showed the lists are inert. The control arm had the same first instinct (control-2 L246) but nothing pushing it further, and it dropped the idea after checking.

## 2. Verdict: LOST

Deciding points:
1. **Consistency on the one major change:** control 4/4, skill 2/3.
2. **skill-3's miss was caused by the skills**, not by chance. The router's LVLI row led into the leveled-list skill's Overview point 1 and its de-levelling triage, and that ended in a dead patch while the bounties stayed at 0 gold.
3. **The skill arm had no compensating win.** On their judged changes, skill-1 and skill-2 match the control exactly (same records, same globals, same scores), and the control reached all of those calls without skills.

## 3. Per-section labels

| Skill / file | Section | Label | Runs | Evidence |
|---|---|---|---|---|
| requiem-patching/SKILL.md | Routing table: LVLI → leveled-list (and Integration workflow 1) | **harmed** | skill-3, skill-2 | skill-3 loaded the leveled-list skill for the 13 orphan lists (L225) and its whole patch has no effect. skill-2 planned the same route (L154) and escaped only via a reverse-reference query (L705). |
| requiem-leveled-list-patching/SKILL.md | Overview point 1 ("mod's own lists … de-level them by hand") | **harmed** | skill-3 | L207/L224/L271: framed orphan lists as gated live content. Judgement: groups 1–8 no_effect, group 9 major not_done. |
| requiem-leveled-list-patching/SKILL.md | Bulk pass → The de-levelling triage | **harmed** | skill-3 | Drove the edit (L326, L801). Followed literally, the recipe would turn a single-pick gold ladder into a random 50–15,000 gold roll. The agent silently collapsed each list instead, then picked off-tier level-20 values (groups 4–8). |
| requiem-leveled-list-patching/SKILL.md | Never extrapolate across a list family | noise | skill-3 | Read all 13 rungs (L398–400), but all on dead records. |
| requiem-leveled-list-patching/SKILL.md | Workflow 4: Master Requiem / own-list distinction | noise | skill-3 | Left out the Requiem master (L774). Every run omitted it anyway. |
| requiem-leveled-list-patching/SKILL.md | Cell→zone sweep | noise | skill-3 | Judged moot (L583). |
| requiem-leveled-list-patching/SKILL.md | Checklist: read-back | noise | skill-3 | The controls also read back their writes. |
| requiem-leveled-list-patching/SKILL.md | Every record dispositioned ("skipped — quest-fixed list") | unused | skill-3 | The only exit available, but it gives no test for whether a list is live. |
| requiem-patching/SKILL.md | First step 1: Freshness probe + Iron Sword | noise | all 3 skill runs | The controls ran load_order_status too. Nothing judged depended on it. |
| requiem-patching/SKILL.md | First step 2: Enumerate | noise | all 3 skill runs | The controls ran the same type aggregates. |
| requiem-patching/SKILL.md | First step 3: Triage | noise | skill-2, skill-3 | Pointed at the LVLIs, as control-2's instinct did. It gave no lead to the clone/VMAD gap. |
| requiem-patching/SKILL.md | Notes: authority = live winner | noise | skill-1, skill-2 | All 4 controls followed the live GlobalVariable binding over Requiem.esp's Int without it. |
| requiem-patching/SKILL.md | Routing row: quest-start GetLevel gating → flag to user | noise | all 3 skill runs | Not scored by the judge. Cost wrong-field dead ends in every skill run. skill-2 over-generalised the gate values and skill-3 miscounted them. It was not a stop-to-ask. |
| requiem-patching/SKILL.md | Routing row: BOOK (tomes) | noise | skill-1, skill-3 | Bounty letters skipped correctly. The controls skipped them too. |
| requiem-patching/SKILL.md | Routing row: VMAD → requiem-script-patching | unused | — | This was the right lane, but no run loaded the skill. |
| requiem-patching SKILL.md + integration-checklist.md | Top-level reconciliation / item 1 | noise | skill-1, skill-3 | 1,158-record tables with no judged effect. skill-1 broke the "never sample" rule, and one sampled claim was false. |
| integration-checklist.md | Item 4: Masters | noise | skill-1 | Same master set as the controls. |
| requiem-patching SKILL.md + integration-checklist.md | Item 5: REQ_NULL gate | harmed (minor) | skill-2 | The query errored (L1060/L1064), but the report claimed a pass anyway. |
| requiem-patching SKILL.md + integration-checklist.md | Item 7: "Run the Reqtificator" | noise | all 3 skill runs | Irrelevant to quest VMAD and mod-own lists. |
| requiem-patching/SKILL.md | `housecarl_resolve`, `cross_plugin_query`, `conflict_tree=true` | unused | all 3 skill runs | These are 1.x tool names. The runs mapped them silently and nothing was refused. |
| requiem-patching/SKILL.md | Read `requiem-vision.md` | unused | — | Never read. |
| references/economy.md | Merchant/vendor/prices row | unused | — | Never opened, and it doesn't cover quest rewards anyway. |
| references/routing-table.md, scope-and-authority.md, other gap references | — | unused | — | skill-1's grep matched some of these (L221); none was opened. |
| requiem-script-patching and the other 8 domain skills | — | unused | — | Never loaded. Script-patching's Sweep (a) names QUST scripts as a host, but it is about Nox reuse and has no check for unbound properties. |

No section earned **helped**. Nothing in any skill drove a right call that the control missed.

### Missing knowledge (what a skill could have supplied)

1. **Check the record is live before patching it.** Run a reverse-reference that includes VMAD properties, and drop any mod-own record nothing consumes. This decided the task. Six runs did it generically. skill-3 did not, and the leveled-list skill's bulk pass pushes the other way. A one-line guard in that skill's Bulk pass ("first confirm something references the list; orphan lists are skips") would have saved skill-3.
2. **Clones of vanilla records don't inherit Requiem's edits.** For mods that copy vanilla quests or records, diff what Requiem and the live stack changed on the template (here a BQScript property and the objective text) and carry it to each clone. The controls found the gap this way (control-2 L417, nothink L95–102). The router's triage has no class for it.
3. **Bind VMAD properties to the live script's declared type.** The winning `.pex` may come from a BSA (Headhunter here). Use `housecarl_check findings=unbound/scripts`, asset_status, BSA extraction and decompiling. Requiem.esp's Int property would not bind here. `requiem-script-patching` doesn't cover this.
4. **Requiem's flat bounty tiers.** These are 500/500/1000/3000 (Requiem.esp RewardAmount, `REQ_DEPRECATED_LI_Reward_Bounty*`); in Headhunter+Missives orders they live in the `_M_GlobalRewardKill*` globals. Missing this cost skill-3 its values (groups 4–8). economy.md is vendor-only.
5. **Collapsing single-pick reward ladders.** Collapse to one Level-1 entry at the tier value; don't flatten entry by entry (mining notable, skill-3).
6. Look for existing compat patches before writing. All correct runs did; skill-3 didn't.
7. ESL-flag override-only patches. No run did it in either arm.

## 4. Costs of the skills on this task

| | Control (3 main runs) | Skill (3 runs) |
|---|---|---|
| Median cost | **$2.67** ($1.79 / $2.76 / $2.67) | **$2.34** ($2.34 / $2.41 / $1.68) |
| Median wall time | **5.95 min** (326 / 357 / 428 s) | **5.62 min** (337 / 350 / 222 s) |
| Turns | 36 / 40 / 44 | 53 / 53 / 34 |
| Output tokens | 11.2k / 13.5k / 15.0k | 16.4k / 17.1k / 10.8k |
| Subagents | 0 | 0 |
| Stops to ask | n/a | **0** (all runs ended end_turn; the gate questions came after writing) |

With control-1-nothink included ($3.67, 403 s), the control median is $2.72 and 6.33 min.

The skill arm was slightly cheaper at the median, but only because skill-3 locked onto the wrong target early and finished fast. The two correct skill runs cost about the same as the correct controls, and took more turns and output tokens. The extra went to skill ritual: the Iron Sword probe, GetLevel gate sweeps with wrong-field dead ends, and 1,158-record reconciliation tables. **On correctness, the skills bought nothing on this task, and cost one dead patch.**

## 5. Reference bugs

*Reference-bug findings removed from the public copy: they concern ARR's handmade patches and are reviewed and filed in the ARR release tracker, not here.*
