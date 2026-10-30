# Magecore → Requiem: does each skill earn its place?

**Task:** Patch `[Caenarvon] Magecore.esp` for Requiem as a new plugin. It has 169 ARMO (all clothing or jewelry), 8 LVLI, 5 CONT overrides of the College vendor chests, plus ARMA, TXST and KYWD records.
**Runs:** control-1/2/3; skill-1/2/3, plus skill-1-nothink as an extra data point. No run stopped to ask, and none used subagents.

> [!summary] Verdict: **BEAT**
> - **Decided by de-levelling.** The skill arm de-levelled the College vendor lists in 4 of 4 runs; the control arm did it in 0 of 3. Every judge rates this major, and the text that drove it is in `requiem-leveled-list-patching`.
> - **Skill-arm extras.** The skill arm also weighted the vendor lists the way Requiem does and kept Master robes out of level-1 stock. It also fixed the Magicka-regen robe prices.
> - **Shared blind spot.** Both arms mostly missed the extra-slot enchant-stacking exploit: the skill arm in 4 of 4 runs, the control arm in 2 of 3.
> - **Cost.** About +64% money ($2.80 vs $1.71 median) and +36% wall time (5.8 vs 4.25 min).

## Scores at a glance

The judge's quality score is out of 100. "Ref" is the reference patch's score from the same judge.

| Run | Quality | Ref | Δ | De-level | Master robes out of vendors | Regen prices | AR 0 (12) | Boots keyword | Enchant block (40) | Cost | Min |
|---|---|---|---|---|---|---|---|---|---|---|---|
| control-1 | 55 | 58 | −3 | ✗ | ✗ | ✗ | ✗ (offered) | ✓ | **✓** (slot-keyword strip, 42 items) | $2.16 | 6.4 |
| control-2 | 25 | 58 | −33 | ✗ | ✗ | ✗ | ✓ | ✓ | ✗ | $1.71 | 3.9 |
| control-3 | 35 | 68 | −33 | ✗ | ✗ | ✗ (claimed it matched) | ✓ | ✗ | ✗ | $1.65 | 4.3 |
| skill-1 | 58 | 50 | +8 | ✓ | ✓ (4/3/2/1, no Master) | ✓ | ✓ | ✓ | ✗ | $2.14 | 4.7 |
| skill-1-nothink | 62 | 60 | +2 | ✓ (flat) | ✗ | ✓ | ✓ | ✓ | ✗ | $2.56 | 5.2 |
| skill-2 | 66 | 50 | +16 | ✓ | ✓ + Masters → REQ_LI_HighLevelRobes | ✓ | ✓ | ✓ | ✗ | $2.80 | 5.8 |
| skill-3 | 62 | 55 | +7 | ✓ | ✓ + Masters → REQ_LI_HighLevelRobes | ✓ | ✓ | ✓ | ✗ | $3.02 | 6.0 |

A different judge scored each run, and their reference scores range from 50 to 68. Compare the Δ column, not the raw scores. On that measure every skill run beat its reference, and every control run fell short of it.

---

## 1. Right and wrong, per arm (major first)

### Where the arms differ on major points

**A. College vendor lists left level-gated: control wrong ×3, skill right ×4.**
- **What the lists are.** The `aaLMC*` lists stock the five College vendor chests (`REQ_VendorChest_WCollege_*`), through Authoria Container Merge.
- **How Requiem does it.** Requiem - Magic Redone's own `LItemRobesCollegeAlteration` (016E1E) and `…MagickaRegen` (016E22) have every entry at Level 1.
- **What was left unpatched.** Magecore's lists keep gates at 8/16/24/32. Low-level characters see no Magecore stock, and Master robes only appear at level 32.
- **The controls never looked.**
  - control-1 listed the LVLI but never read them (mining L324/L612).
  - control-2 never opened them.
  - control-3 called distribution "not relevant" (L537).
- **Every skill run de-levelled, and cited the leveled-list skill each time.**
  - skill-1 L590: "Mod's OWN list escapes the merge".
  - nothink L231/L337: 96 entries set from Level >1 to Level 1.
  - skill-2 L1406.
  - skill-3 L294/L1342.
- **The skill corrected a wrong prior.** Before loading the leveled-list skill, skill-2's own summary said "the leveled lists are de-leveled by Requiem" (L310).

**B. Vendor weighting and where Master robes go: skill better than both control and reference.**
- **The pattern copied.** skill-1, skill-2 and skill-3 copied Magic Redone's College pattern: 4 Novice / 3 Apprentice / 2 Adept / 1 Expert, all at Level 1, with no Master tier.
- **Master robes re-homed.** skill-2 and skill-3 moved Magecore's 18 Master robes into `REQ_LI_HighLevelRobes` (32CEE0). This is where Requiem keeps its own Master robes. They did it additively, on top of the Magic Redone winner, and kept all 60 of its entries.
- **Judges.** The judges rated this right and major. They rated the reference's flat de-level, which puts Master robes on sale at level 1, as wrong (judgement-s1 group 1, s2 groups 1–2, s3 groups 15–16).
- **Where it came from.** The method is in the LL skill's Workflow 1–4 (pick the list from the comparable, tier by repetition, add additively, master Requiem). The numbers came from the load-order analogue.
- **The odd one out.** nothink only flattened the lists, so Master robes are sold at level 1. Its judge still rated that right, so the judges disagree on this point.

**C. Magicka-regen robe values: skill right ×4, control missed ×3.**
- **The error.** Magecore priced its Fortify Magicka Regen robes like the school robes: 408/910/1612/2563.
- **The fix.** Vanilla `EnchClothesRobesMageRegen02–05` carry the same enchantments and are worth 308/660/1112/1803. Requiem does not change them. All 4 skill runs wrote exactly those values (judged major in s1, s2 and s3).
- **The controls.** control-3 printed 660/1112/1803 in its own output (L570) and still reported the robes as correctly priced. The miner marked that claim false.

**D. Extra-slot enchant stacking: control-1 right; every other run wrong.**
- **The problem.** 40 accessories sit in slots 44–59: hats, mantles, panties, tome, wraps, collars, armlets, thighlets and so on. They still carry ClothingBody/Head/Hands/Feet/Necklace. Requiem's `REQ_Ench_Armor_*_Base` WornRestrictions lists are keyword-only, so these items can be enchanted and worn on top of a full enchanted kit. That allows 10+ extra enchantments.
- **The reference fix.** The reference adds `MagicDisallowEnchanting` (0C27BD).
- **control-1's fix.**
  - It stripped the slot keyword from 42 items, including the bracers on slot 34 and the stockings on slot 38, which the reference missed.
  - It found the problem through a load-order references sweep that reached `REQ_Perk_Evasion_Agility_Unarmored` (L635/L672). That spell gives +50/+20/+15/+15 armour per worn clothing keyword and never checks for real armour, so control-1 closed that exploit as well.
- **Everyone else.**
  - All 4 skill runs and control-2/3 missed it. In every skill judgement it is the candidate's worst problem.
  - Some noticed it and stopped short: control-2 flagged the armlet (L714), control-3 saw "misapplied" keywords (L380), skill-2 wondered about Mage Armor and ClothingBody (L1131), and skill-3 asked the judge.

### Same in both arms (mostly)
- **AR → 0 on the 12 armlets, thighlets, tome and mantles** (minor, judged right). All 4 skill runs did it. Two of three controls did too: control-2 and control-3, with control-1 only offering it. Both arms covered all 12, where the reference did only 6.
- **aMCBoots: ArmorBoots swapped for ArmorClothing.** All 4 skill runs did it; two of three controls did (control-1 and control-2). The judges disagree on severity: control-1's judge says major (cloth boots complete armour full-set perks), control-2's says minor (those set spells are `REQ_NULL_` and unreferenced here).
- **Leaving values and slots alone.** Both arms left the school-robe values (already equal to vanilla) and the thighbelt slot untouched, so both avoided the reference's ×3 and the thighbelt that no longer renders.

### Skill-only minor rights
- **Unenchanted clothing repriced to Requiem comparables.** skill-2 and skill-3 set unenchanted robes to 5 and mantles to 10. skill-2 also priced silver armlets and thighlets at 65 and gold ones at 250 (judged right, minor).
- **Jewelry vendor keyword.** nothink retagged the collars and lantern as VendorItemJewelry.

### Shared weak spots
- **Dead edit.** Every skill run rewrote `aaLMCMagickaRegen`, which nothing references (no effect in game).
- **Plugin hygiene.** No run in either arm ESL-flagged its plugin, and every run carries an unused Update.esm master.

---

## 2. Verdict: **BEAT**

**Major issues per run**
- **Skill arm:** one major miss per run (the enchant block), the same in all four.
- **Control arm:**
  - control-2 and control-3 each have two major misses: de-levelling and the enchant block.
  - control-1 has one (de-levelling), but it does score the enchant and Agility wins.

**What decided it**
- **De-levelling:** 4/4 vs 0/3.
- **Requiem-shaped Master handling:** 3/4 and 2/4 vs 0/3.
- **Regen prices:** 4/4 vs 0/3.
- **Consistency:** every skill run beat its reference; every control run fell short of its reference.

**Against the skill arm:** control-1 alone found the most important balance exploit on this mod, and no skill run did.

---

## 3. Per skill section

| Skill | File § section | Label | Evidence (runs) |
|---|---|---|---|
| leveled-list | SKILL.md § Overview pt 1 / Bulk pass protocol / **The de-levelling triage** ("mod's OWN list escapes the merge") | **helped** | Drove the de-level in all 4 runs; 0/3 controls did it. Major in every judgement. It also corrected skill-2's wrong prior (L310). s1, nothink, s2, s3 |
| leveled-list | SKILL.md § **Workflow 2 — Tier by repetition** | **helped** | 4/3/2/1 weighting with no Master tier, from MR's LItemRobesCollege*. Judged right; the reference's version judged wrong. s1, s2, s3 |
| leveled-list | SKILL.md § **Workflow 1 Pick the list from the comparable / 3 Add additively / 4 Master Requiem** | **helped** | Master robes → REQ_LI_HighLevelRobes, additive, all 60 MR entries kept. Major, candidate-only. s2, s3 |
| leveled-list | SKILL.md § Judgment — Containers (CONT not merged, build on winner) | noise | All 3 controls also read the chest trees and left the merged chests alone. s1, nothink, s2 |
| leveled-list | SKILL.md § Flags are unions / Checklist read-back | noise | No change to the outcome. s1, nothink |
| leveled-list | references/containers-and-zones.md | noise | Read; same chest decision as the controls. s2 |
| armor | SKILL.md § Common mistakes — "Clothing must be ArmorRating 0" | noise | Drove 12 AR→0 edits in 4/4 runs, but control-2 and control-3 made the same edits. The rule is also overstated: Requiem.esp has 20 clothing ARMO with AR>0, including the Novice Hood at 5. s1, nothink, s2, s3 |
| armor | SKILL.md § Judgment — Cosmetic cloth and extra-slot accessories | noise | Cited for AR 0 on the mantles and tome, which the controls also did. This is the only extra-slot text in the skills, and it covers AR only, not enchantability. nothink, s2, s3 |
| armor | references/keywords.md § Clothing & jewelry frames | **harmed** (contributory) | The boots fix was noise: control-1 and control-2 made it too. But the file lists `ClothingBody/Feet/Hands/Head` as the standard clothing set with no condition on slot. skill-1 "verified the keyword sets of the other 168" against it, which passed the 40 extra-slot items as fine. Small credit: the jewelry retag (nothink) and "value tracks material" (s2). s1, nothink, s2, s3 |
| armor | SKILL.md § Bulk pass protocol triage matrix / reconciliation | noise | The controls did the same full 169-row dump. All 4 |
| armor | SKILL.md § Workflow 1–2 Find/read the comparable (live analogy) | **helped** (weak) | Matching robes by identical ENCH exposed the regen price error. All controls compared robes too and missed it. nothink, s3 |
| armor | SKILL.md § ObjectEffect → route to requiem-magic-patching | noise | Correctly skipped; the controls reached "ENCH already Requiem" too. nothink, s2 |
| armor | SKILL.md § Checklist — Requiem.esp master | noise | Followed or ignored with no in-game effect. All 4 |
| router | SKILL.md § **Routing table** | **helped** (enabling) | It is the only path to the leveled-list skill: LVLI → LL in 4/4 runs. The controls never opened the LVLI. All 4 |
| router | SKILL.md § First step (freshness probe, enumerate by type) | noise | The controls also ran load_order_status and a type census. All 4 |
| router | SKILL.md § Checklist + integration-checklist.md (reconciliation, masters/REQ_NULL, run the Reqtificator) | noise | Report polish only; the REQ_NULL scans errored (s2 L1564, s3 L1448). All 4 |
| router | references/economy.md § Derive value from the comparable | **helped** | Credited for the regen-robe fix and the unenchanted-value fixes in s2 and s3. The miners' "round to 5 not applied" is a misreading: that rule is for weapons only. nothink, s2, s3 |
| router | references/economy.md § Vendor stock | noise | Chest checks matched what the controls did. nothink |
| router | references/masters-and-null-stripping.md | noise | No effect. nothink, s3 |
| router | references/requiem-vision.md | noise | Only s3 opened it (head -80). The router says to read it, but 3 of 4 runs never did. s3 |
| router | references/routing-table.md | noise | Duplicates SKILL.md. s3 |
| router | Checklist `housecarl_resolve` + 1.x tool names | unused | Never called. The 1.x names were mapped silently to the 2.x tools with no refusals. All 4 |

**Unused:**
- **Router references never opened:** scope-and-authority, alchemy, combat-resistance, diseases, exhaustion-stress, food, perks-skills, shouts, standing-stones, stealth, vampirism-lycanthropy.
- **Armor references never opened:** ar-ladder, crafting, housecarl-recipes, worked-examples.
- **LL references never opened:** merge-behavior.md (the ≥3-contributor gotcha was never checked for the HighLevelRobes edit), list-structure, worked-examples, housecarl-recipes. The cell→zone sweep did not apply.
- **Skills never loaded:** magic (correctly, since the ENCH records are already Magic Redone), weapon, ammo, consumable, npc, perk-assignment, race, script.

### Missing knowledge (a skill could have supplied it)
1. **Extra-slot enchant block.** This is the single highest-value addition.
   - **The rule:** clothing or jewelry in a non-vanilla slot (44–59, plus 34/38 when the mod's own gloves and boots hold 33/37) that keeps a vanilla `Clothing*` slot keyword can be enchanted and stacked. Add `MagicDisallowEnchanting 0C27BD:Skyrim.esm`, as the reference, Requiem.esp (222 ARMO) and WAR's slot-56 padding do, or strip the keyword.
   - **Where it belongs:** the armor skill's "Cosmetic cloth and extra-slot accessories" section, with a slot condition added to the keywords.md clothing frame.
2. **Agility clothing bonus.** `REQ_Perk_Evasion_Agility_Unarmored` (0008A1, WAR) adds +50/+20/+15/+15 armour per worn ClothingBody/Head/Hands/Feet and never checks for real armour. Extra-slot clothing with those keywords therefore stacks the bonus under armour. control-1 found this alone (judgement-c1 group 2, major).
3. **Soften "Clothing must be AR 0".** Say instead: AR 0 for extra-slot pieces and jewelry; copy the comparable for standard-slot clothing.
4. **Check reachability before rebuilding a mod's list.** Run reverse references first, and decide whether to place an orphaned list such as aaLMCMagickaRegen.
5. **A College-robe worked example.** Cover the MR 4/3/2/1 pattern with no Master tier, where Requiem keeps Master robes (REQ_LI_HighLevelRobes → REQ_LI_LootHighLevelContent), and the regen-robe price ladder (5/308/660/1112/1803) next to the school-robe ladder (5/408/910/1612/2563).
6. **Hygiene.** ESL-flag a patch that adds no new records, and drop the tool-added Update.esm master. Every run in both arms missed both.

---

## 4. Costs

| | Control (median of 3) | Skill (median of 3) | Δ |
|---|---|---|---|
| Cost | $1.71 | $2.80 | +$1.09 (+64%) |
| Wall time | 4.25 min | 5.8 min | +1.5 min (+36%) |
| Output tokens | 13.8k | 23.0k | +67% |
| Thinking tokens | 6.0k | 9.6k | +60% |
| Cache read | 2.18M | 3.96M | +82% |
| houseCARL calls | 27 (22–29) | 21 (19–24) | slightly fewer |
| Stops to ask / subagents / skill-caused refusals | 0 / 0 / 0 | 0 / 0 / 0 | none |

- **The nothink run:** skill-1-nothink cost $2.56 and took 5.2 min.
- **Same errors in both arms:** the denied spill-file reads and the denied folder `mv` hit both arms equally.
- **What the extra cost bought:** correct de-levelling, Requiem-shaped vendor weighting, Master placement and regen prices. Every skill plugin landed above its reference; every control plugin landed below its reference.
- **Where the extra tokens go:** mostly into re-reading the three skill bodies each turn (cache read), not into more tool work.

---

## 5. Reference bugs (from the judges, checked against run behaviour)

*Reference-bug findings removed from the public copy: they concern ARR's handmade patches and are reviewed and filed in the ARR release tracker, not here.*
