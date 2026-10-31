---
title: Distillation, requiem-leveled-list-patching (helped, hurt, unnecessary, rewrite brief)
date: 2026-10-30
scope: one skill, requiem-leveled-list-patching (abbrev. LL; rule ids ll#N from drill/absolutes/requiem-leveled-list-patching.json)
inputs: scoring/synthesis/synth-work/skills/requiem-leveled-list-patching/ (SKILL.md + 5 references + index.jsonl); scoring/synthesis/synthesis.md; drill/absolutes/{SYNTHESIS.md, ledger-*.json/.md, requiem-leveled-list-patching.json}; drill/{facts,coverage,consistency,process,rescore}/; drill/r2/{INTEGRATED.md, U6, U7, U8, U11, U12, U13}; drill/r3/{INTEGRATED.md, CRITIC.md, R1, R2, R5, R7, R10, R12}; scoring/judgements/{r48,r11,r98,r93}/judgement.json (FS list and chest groups)
new computation: none beyond line counts of the skill files and a per-run load count from U8-unexercised.json. No runs, no live reads.
---

# requiem-leveled-list-patching: distillation

> [!summary] Headline
> - **One fact carried this skill: "a mod's own leveled lists are never touched by the Reqtificator, so de-level them by hand."** It won MC1 (4/4 skill vs 0/3 control, right/major in every judgement) jointly with the router's LVLI route, and the FS death-item lists (4/4 vs 0/4, minor). Two short methods added MC2 and the Master-robe re-homing: read the comparable list's entries and copy its repetition; reverse-reference where Requiem keeps the comparable and `Add` to that list's winner. The values (4/3/2/1, no Master tier, `REQ_LI_HighLevelRobes`) came from the load order, not from skill text.
> - **The same fact, with no liveness test, caused the only lost run** (FQ s3: 13 orphan reward lists patched, the real QUST fix missed). It also produced a dead edit in 4/4 MC runs (`aaLMCMagickaRegen`). The skill's merge premise is false for ARR (`mergeLeveledLists` and `mergeLeveledCharacters` are both **off**), so about 40% of the text (mastering Requiem, the 3-contributor rule, "RFI wins most lists") describes a mechanism ARR never runs.
> - **Most of the 1,047 lines were never exercised.** merge-behavior.md and worked-examples.md: 0 runs read them. housecarl-recipes.md and list-structure.md: 1 run each. Placing new gear into the `REQ_LI_*` backbone, the skill's headline workflow, has no ledger row at all.
> - **Verdict: rewrite to about 70-90 lines.** Keep the boundary fact (live and code wording), the liveness gate, the read-the-comparable and Add-to-winner methods, and the container forward rule. Move the merge mechanics to the Requiem profile as a 3-line conditional, ARR's merge-off setting and Container Merge scope to the ARR list layer, and every call shape to houseCARL.

Conventions. Evidence = a cited ledger row, judgement group or drill item. *Inference* is marked. Run ids: task + s/c + number, n = nothink (FS s1 = falkreath-shades skill-1). Groups are `rNN gN`, 0-based as in judgement.json. "Ctrl" = the control arm on the same task.

---

## 1. What it is

The placement and distribution skill. It covers LVLI, LVLN, LVSP, CONT and ECZN: putting already-statted records into Requiem's lists, containers and zones, and de-levelling a mod's own lists. It is 1,047 lines in 7 files and has 174 inventoried rules (ll#1-#174).

**Use** (U8-unexercised.json per_run; U8 table):

| | |
|---|---|
| Skill runs that loaded it | **15 of 21**: FS 4/4, MC 4/4, RB 3/3, TR 3/4 (not s3), FQ 1/3 (s3 only), AC 0/3 |
| Rules used / read with no evidence / never read | 37 / 104 / 33 (174 total). Rules with a helped row: 9 (ll#8, #16, #33, #48, #58, #60, #64, #71, #99). With a harmed row: 9 (ll#8, #28, #29, #30, #33, #35, #64, #150, #151) (U8 rules[]; ledgers). Plus one unnumbered TR "tiering section" harmed row (X8) and ll#36 "missed" (FQ) |
| SKILL.md | 15/15 loads. 93 rules, 26 used. Labels: 18 helped / 5 noise / 10 harmed / 14 neutral / 2 missed |
| references/containers-and-zones.md | read in 5 runs (FS, MC, RB). 11 rules, 5 used (1 helped / 4 neutral) |
| references/housecarl-recipes.md | read in 1 run (FS). 20 rules, 2 used (attributed: 2 harmed, 1 neutral, both FQ #150/#151) |
| references/list-structure.md | read in 1 run (FS). 15 rules, 2 used (neutral) |
| references/merge-behavior.md | **read in 0 runs.** 27 rules. 2 "used" (ll#124, #125) are ledger attributions to text no run opened (U8 caveats) |
| references/worked-examples.md | **read in 0 runs.** 8 rules, 0 used |

Where it mattered: MC (decisive), FS (minor lists + chests), FQ (the loss), RB (zones and chests, debatable), TR (one wasted lane). AC never loaded it (U12 scan_ac: "I'll skip loading the dedicated leveled-list skill").

---

## 2. Helped

| # | Item | Source | Evidence | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | **A mod's own leveled lists are not touched by any Reqtificator pass; de-level them by hand (Level > 1 → 1)** | ll#33 (SKILL Bulk pass, "The de-levelling triage" L146-154); ll#8 (Overview L28-32); ll#64 (Judgment L291-295) | **MC1:** College vendor lists de-levelled in 4/4 skill runs (s1 r45 g0, s1n r37 g0, s2 r87 g0, s3 r52 g13; right/major in every judgement), 0/3 controls (c1 g8, c2 g0, c3 g7 not_done/major). The controls never read the entries; c1 L612 "Leveled lists ... also seem unaffected", c3 L820 "the container merge already handles" (drill/facts §1). **Within-run proof:** MC s2 L310 "the leveled lists are de-leveled by Requiem" before the LL load (L349); flattened after it (L1406) (U7 §3; r3 CRITIC spot-check). **FS:** the mod's death-item lists 1ECD34/35/37, Level 3 → 1, in 4/4 skill runs (r48 g39, r11 g41, r98 g41, r93 g37, right/minor), 0/4 controls (ledger-falkreath-shades). The reference does the same | **Joint with router** in all 4 MC runs (R2 mage-core: the router's rp#14 LVLI row delivered the skill; the fact exists only in LL text). Multi-judge (7 MC + 4 FS judgements). One of the ~4 facts the model truly lacks (drill/facts headline) | The fact in its code-proven form: "No Reqtificator pass changes a list entry's Level. Under ARR's settings it does not touch a mod's own LVLI at all (`mergeLeveledLists=false`; only REQ:TEMPER/UNROLL-tagged mods' lists are rebuilt)" (U6 #7d, R12). Drop the `baseVersion == null` merge-guard reasoning, which is moot in ARR |
| H2 | **Tier by repetition and list choice, not by Level: read the comparable list's entries and copy its weighting** | ll#48 (Workflow 2, L218-229); ll#8 for the cosmetic list | **MC2:** s1, s2 and s3 copied Magic Redone's `LItemRobesCollege*` pattern (Novice ×4 / Apprentice ×3 / Adept ×2 / Expert ×1, no Master) at Level 1. s1 and s2: judged inside the same group as the de-level (r45 g0, r87 g0, right/major). s3: weighting judged separately, r52 g15 right/**minor** (novice ×3, not ×4, "a trivial gap"); its Master removal + re-home is r52 g14 (H3). 0/3 controls weighted anything (ledger-mage-core). s1n loaded LL and did not apply it: it read the vendor containers instead of `LItemRobesCollege*` and flattened like the reference (consistency/mage-core §4). s1 applied the same shape to the cosmetic list (r45 g2 right/minor) | **Domain method + own-probe values** (R2 mage-core). U11 tier A: the skill names the lookup, the numbers come from the live read. Share caveat: the router's rp#14 row (L87) also says "tier by repetition", so a router-only cause cannot be fully excluded; no run quotes it, and the miners cite ll#48 (R2 mage-core L61, L73). **Contested**: 4/7 judges call the reference's flat list with Master robes at L1 wrong, 3/7 call it right (R10 MC I6; U3 MC8) | The method: "Find the closest live Requiem/MR list for the same goods; read its entries; copy its repetition and its tier cut." No numbers in the skill. Add the College-robe case as the one worked example (synthesis.md L324) |
| H3 | **Reverse-reference where Requiem already places the comparable; `Add` to that list's winner (base + your entry, never a lone entry)** | ll#16 (First step 3, L68-82); ll#71 (Common mistakes L312-314); ll#51 (Workflow 3) | **MC:** s2 and s3 queried where Requiem keeps vanilla Master robes, found `REQ_LI_HighLevelRobes 32CEE0:Requiem.esp`, and added the 18 Magecore Master robes (15 school + the Magicka-regen Masters, per r52 g14) there with `Add` on the MR winner, keeping all 60 base entries (s2 r87 g1 via a ×2 sublist; s3 r52 g14; right/major, candidate-only). 0/3 controls. s1 dropped the Masters with no new home, so they are unobtainable (R10 MC: no other referencer, live). Its judge rated that right (r45 g0) | **Domain method + own design** (R2: re-homing = LL#16/#71 shape; the ×2 sublist was s2's own). Single-judge per run, 2 runs | "Reverse-reference the comparable to find its lists; add yours to the same ones. `Add` to the current winner." This matters more in ARR, where the merge is off: the plain winner ships, so building on it is what keeps Requiem's and other patches' entries (*inference* from U6 #7d) |
| H4 | **Containers are not merged: build on the winner, fill with Requiem's curated list references** | ll#58/#60 (Judgment L275-281); ll#99 + containers-and-zones.md §Containers | **FS:** 3 skill runs copied Requiem's own versions of the vanilla chests that the mod cloned as NoRespawn copies (strongbox, Warlock boss chest, Apocrypha pod): s1 r48 g40 right/major ("item-for-item identical to Requiem.esp's TreasStrongBox 10AAD2, TreasWarlockChestBoss 02065D, DLC2TreasApocryphaChest 02C460"), s2 r11 g43 right/major, s3 r98 g43 right/minor. 0/4 controls; the reference changes no items. Caveat: r11's stated reason ("REQ_NULL special-loot list gives nothing") is false. 02BC2F still drops loot (live read, ledger-falkreath-shades §REQ_NULL), but the edit still mirrors Requiem's chest. **MC:** noise (ll#60; 3/3 controls also read the College chest trees and left the Container Merge winners). **RB:** chest re-curation into Requiem loot lists debatable/minor (r26 g40; ledger-rot-below ll#95/#96/#98 neutral) | **Domain-led; the agent chose the CopyFrom.** No LL line says "a mod's copy of a vanilla container loses Requiem's edits; forward them". That rule is U13's top gap (see N4) | "CONT is not merged. A mod's copy or override of a vanilla container drops Requiem's edits: forward Requiem's version plus the mod's delta, per record." Drop the boss-chest anatomy table |

**Neutral / noise rows that are not wins but show the text being read:** ll#36 used inside the ll#33 helped rows (skip an already-flat list with a reason: MC s1n, s3), but its own FQ row is labelled **missed** (no test for "unused"; see X1); ll#124 (FQ s3: own lists are outside the merge, so no Requiem master, which is correct); ll#113 (FQ s3 kept Flags=0); ll#125 (MC s2: the ≥3-contributor rule was never checked; harmless because Add-to-winner is right either way); ll#43 and ll#23 (FQ s3, neutral); ll#90 read-back (FQ s3, noise); ll#25/#26/#101/#82/#62 zone sweep and ll#95/#96/#98 container curation (RB, neutral) (ledgers).

**Attribution summary (evidence).** LL text is involved in 2 of the 17 U7 deciding points (MC1, MC2), both through the router's route. MC3 (regen robe prices) is own probe, not LL (R2). Values come from the load order in every LL win (R2 §3.2; U11).

---

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | **"Own lists ship verbatim, level-gates and all ... a gated entry in a Requiem game is a bug"** with no test that anything rolls the list | ll#8, #30, #33, #64, #28/#29, #150 (all labelled harmed on FQ s3); skip exit ll#35 (harmed, MC s1) / ll#36 (missed, FQ s3) (closed set: flat / quest-fixed / cosmetic) | **FQ s3 (r57), the only lost run:** the router's LVLI row loaded LL (L225); s3 took the 13 `LItemRewardBQ*` lists as live gated content (L271) and rewrote them. r57 g0-g7 no_effect; g8 (BQScript RewardAmount / LeveledReward wiring) not_done/major. 4/4 controls and s1/s2 left the lists alone and fixed the QUST. Counter-proof: FQ s2 ran `references=` on all 13 (→ 0) and skipped them as "unreferenced, inert" (R2 favor-quests L49, own probe, LL not loaded). A `limit=60` query window also hid Requiem's `REQ_DEPRECATED_LI_Reward_Bounty*` lists from s3 (U12; R2) (ledger-favor-quests; consistency/favor-quests: chain rp#14 → ll#8 → ll#30, plus a truncated query). Live: `references=` over the 13 lists returns 0 (R10 FQ C5). s3's own report: "Without the skills I would have left them as just gold." **MC:** `aaLMCMagickaRegen` 000BFB rebuilt in 4/4 runs; s1 knew it was unreferenced and did it "for consistency" (r45 g1, r87 g2, r52 g16 no_effect; in s1n it is lumped into the right/major group r37 g0; R10 MC: the 15 regen robes' only referencer is this orphan list). r3 CRITIC: the FQ reference makes the same dead edit, so r57 is the run closest to the reference | **No scope** (no liveness test) + absolute "is a bug" wording | Liveness gate before any list or container edit: reverse-reference the record, including VMAD properties. 0 referencers → skip as "unreferenced". Add "unreferenced" to the skip exits. Probe: `references=[<list>]` over all types plus a VMAD property search |
| X2 | **Recipe "rewrite each Level > 1 entry to Level = 1"** | ll#151 (housecarl-recipes L44-50; ledger: neutral as applied, harmed as an "also" rule on the FQ s3 row), ll#150, ll#33 body | FQ s3: applied literally, it would turn a single-pick reward ladder into a random 50-15,000 gold roll. s3 instead collapsed each list to one Level-1 entry, copying Requiem's `TGRewardGold` (L325-330). The reference made the same collapse, 275/286 units (R5 #5) | **Over-general** (no counter-case for ladders that pick one rung by level) | Counter-case: "A list that pays one value by level (reward gold, bounty tiers) collapses to one Level-1 entry at the tier value, as Requiem's TGRewardGold does." Its values (500/500/1000/3000) belong to router economy (R10 FQ C6) |
| X3 | **Remove a tier from a list without re-homing it** | ll#48 + ll#57 ("artifact tier wants Best/reward pool, not Town") with no "does it still have a referencer" step | MC s1 (r45) dropped the 15 Master school robes from the vendor lists and did not re-home them, so they are unobtainable (R10 MC, live: no other referencer; consistency/mage-core §4). Its judge rated it right (r45 g0) | **No scope** | "After removing an item from a list, reverse-reference it. If nothing else holds it, add it to the comparable's list (H3)." This is the same liveness probe, run on the item |
| X4 | **REQ_NULL in lists: LL vs the router, and a wrong "dead end" premise** | LL: ll#65/#66/#76/#85 "leave Requiem's REQ_NULL entries", ll#67/#119/#141/#164 "never add to a REQ_NULL_* list (dead end)". Router: rp#114/#137 "no REQ_NULL may remain in any record you patch" | **Latent in LL; no LL rule has a ledger row.** LL's "leave Requiem's REQ_NULL entries" is the correct side; the false part is the "dead end" premise of ll#67/#119/#141/#164. The harm sits on the router side: AC s1 (LL not loaded) removed 6 `REQ_NULL_EncBandit*` entries from the mod's own LVLN under rp#114 (r63 g14 right/none, "Requiem retired these actors"). R7: REQ_NULL NPCs are 155/159 substantive, so those entries were live actors (*inference*, r3 INTEGRATED L40; R1 left it "unverified"). Live: all 197 REQ_NULL LVLI and 63 LVLN keep their entries (U6 §1). FS 02BC2F `REQ_NULL_DLC2LItemSpecialLoot10` still drops loot, and FS s2's judge gave a false reason for a chest fix (r11 g43; ledger-falkreath-shades L113) | **Conflict** between skills (LL vs router) + **wrong fact** (latent) | One REQ_NULL-by-type rule in the Requiem profile: "REQ_NULL = no longer used by Requiem's own content. LVLI/LVLN/CONT keep content and still roll wherever something references them. Don't strip or swap them by name; decide by reachability and curation." Delete the 6 LL copies |
| X5 | **"The Reqtificator MERGES leveled lists (both toggles on)", and everything built on it** | ll#4, #121 (from `DefaultUserConfig.json`); built on it: ll#6, #52-55, #70, #72, #84, #91, #122-#136, #163 (master Requiem.esp, ≥3 contributors, "RFI wins most lists and shows the list as played") | ARR `UserSettings.json`: `mergeLeveledLists: false`, `mergeLeveledCharacters: false` (U6 #7d, code). RFI's 1,102 LVLI are exactly 872 tempered + 230 unrolled lists, all defined by Requiem.esp, WAR or VampireCollection (R12, exact set). No run went wrong on this: MC s2/s3's Add-to-winner is right either way (ll#125 neutral) | **Wrong fact** (default config, not the house config) | Requiem profile, 3 lines: "When `mergeLeveledLists` is on, additions from Requiem-mastering patches are unioned (≥3 candidates; removals need consensus). ARR runs with it off: a list resolves by plain conflict winner." Delete the master-Requiem workaround |
| X6 | **Contradictions about mastering Requiem, inside the skill** | ll#38/#54 ("a placement whose patch never mastered Requiem is not placed"; "its addition can be lost") vs ll#131 (merge-behavior L74: "the plain conflict winner is used") and ll#170 (worked-examples L61: "accept that this single list won't enter the merge ... still correct"). worked-examples L41 ("016578/0165BC/09BC43 are REQ_LI_* lists, so the patch auto-masters Requiem.esp") and housecarl-recipes L167-170 ("also add to a REQ_LI_* list to pull Requiem in") vs SKILL L254-257 ("REQ_LI_* are Skyrim.esm-defined; adding a vanilla item does **not** auto-master Requiem, verified live") | Latent: no run hit it. The absolutes inventory flags the counter-cases | **Conflict** (internal) | Delete. Moot under ARR (X5) |
| X7 | **Cell → zone sweep and the ECZN recipe** | ll#23-#26, #78, #87 (create tiered ECZNs for unzoned cells); ll#101 (mirror the DLC2 shape `MinLevel 25, MaxLevel 0, Flags = NeverResets`), ll#104/#162 (recipe `MinLevel 0, MaxLevel 0, Flags = NeverResets`, "literal Set") vs ll#41/#88 ("Flags fields are unions"; write original bits + change) | RB s1 and s2 each created one ECZN and assigned 15 CJ03 cells: debatable/minor (r26 g46: MinLevel 25 + NeverResets copied from DLC2; r15 g40: MinLevel 20). Ledger-rot-below labels the sweep rules neutral. FQ: moot (no cells). FS: skipped. No control did it. synthesis.md: cut to one line | Example read as a target + **internal conflict** (literal Flags write) | One line: "An unzoned dungeon cell gets no zone from any build pass. If one is needed, copy a comparable Requiem dungeon's zone and read its flags first." The zone behaviour is unverified in code (R12 untested path) |
| X8 | **Tiering applied inside a lane against the coordinator's call** | LL tiering section (miner gave no exact line) | TR s1: the lists lane re-gated 4 single-tome LVLI to the MR patch's wrong book values; the coordinator overruled it and dropped 8 ops (ledger-triumvirate L64; L1379, L1904-L2053) | Lane conflict, not a wrong rule (ledger) | None in LL. Router lane-brief rules own this (cross-cutting) |
| X9 | **Report obligations: "every record dispositioned", reconciliation counts, skip "with reason"** | ll#21, #35-#40, #80 | FQ: 6 of the skill arm's 7 false claims came from skill-mandated steps, the LL route among them (process/favor-quests). FQ s3 read all 13 ladder rungs (ll#43) on dead records (no_effect) | **Gate / report obligation** | Delete from LL. The router owns enumeration and reconciliation |

---

## 4. Unnecessary

Lines are from the files as tested. "Untested" means no run exercised it, not that it is useless.

| # | Content | Source | Why | Lines | Keep-short / delete |
|---|---|---|---|---|---|
| U1 | Freshness probe + Requiem-in-chain check + authority doctrine | SKILL First step 1 (L42-56); Notes L359-366 | **Duplicated** (in all 11 SKILL.md files, router included; grep "fresh"), cross-cutting; the router owns it. Notes L359-361 claims RFI "carries the leveled-list merges" and "wins most lists", which is false for ARR (X5) | ~23 | Delete |
| U2 | Merge algorithm, dual correctness, eligibility, compact lists, 255 cap, tempered output lists, zones | references/merge-behavior.md | **Untested** (0 runs read it) and **moot** in ARR (merge off) | 156 | Keep 3 lines in the Requiem profile (X5), plus 2 lines: "Don't author `*_QualityN_*` or `*_CLI_` lists. RFI regenerates them for REQ:TEMPER/UNROLL mods and loads last" (code, R12). Delete the rest |
| U3 | Worked examples (steel sword, wolf spawn, boss chest, round-trip) | references/worked-examples.md | **Untested** (0 runs), and it contradicts SKILL on auto-mastering (X6) | 103 | Delete. Replace with one ~10-line College-robe example (H2/H3) |
| U4 | Call shapes: coverage audit, reverse-reference, read weighting, Add LVLI/LVLN/CONT, repeat, ECZN, masters, read-back | references/housecarl-recipes.md | **Obsolete tool shape** (houseCARL 1.x names: `housecarl_bulk_apply`, `cross_plugin_query`, `read_record`, `set_field`; the runs used 2.1.1). Read in 1 run. **Generic craft.** 1.x names caused 0 failed calls in all 6 tasks; Opus maps them silently (process/SYNTHESIS L59), so the cost is tokens, not errors. One live harm from a recipe shape is router-side: the `plugins=` REQ_NULL scan errored on an off-order patch and was reported clean (MC s2; process/SYNTHESIS L21, L49) | 191 | Move to houseCARL (its own list-write and reverse-reference docs). Delete here |
| U5 | Recipe blocks inside SKILL.md | SKILL L47-49, 71-74, 95-99, 117-119, 132-134, 151-154, 222-224, 237-245 | **Obsolete tool shape**, duplicated with U4 | ~35 | Delete. Name the probe in words ("reverse-reference the list") |
| U6 | Bulk-pass sweeps, per-record disposition, reconciliation count, "touched is not patched", "never extrapolate across a list family" | SKILL L84-107, L160-198; recipes L20-60 | **Noise**: FQ s3 read every rung of dead lists (ll#43 neutral); no outcome change in MC or TR (synthesis.md). The router owns enumeration and reconciliation. Report obligations hurt (X9) | ~100 | Keep 2 lines: "Enumerate the mod's own LVLI/LVLN/LVSP; for each live one, read the entries; Level > 1 → flatten" + "read every sibling of a same-prefix family" |
| U7 | Cell → zone sweep, ECZN judgement, ECZN half of containers-and-zones, ECZN common mistakes and checklist lines | SKILL L109-126, L283-295, L328-332, L349-351; containers-and-zones L55-91; merge-behavior L144-156; recipes G | **Noise / debatable** (RB only; X7). Zone behaviour unverified in code (R12) | ~95 | Keep 1 line (X7) |
| U8 | Checklist | SKILL L334-355 | **Duplicated** (restates the body). Checklists are rarely cited set-wide (U8: 23/135 rules used) and turn into compliance claims (X9) | 22 | Delete |
| U9 | Common mistakes | SKILL L310-332 | **Duplicated** (every line restates the body) | 23 | Delete; fold the 2 live ones (lone entry, raw gear in chests) into the body |
| U10 | "Level = 1 everywhere; tier is repetition + list" restated about 8 times | Overview pt 2, triage L136-139, Workflow 2, Common mistakes, Checklist, list-structure §1, recipes, worked examples | **Duplicated** | ~40 | Keep once (H2) |
| U11 | Overview scope prose ("links, does not re-stat"; five record types; LVSP design routed to magic) | SKILL L8-36, L369-371 | **Generic craft**; routing belongs to the router | ~30 | 2 lines |
| U12 | Standard flags section; boss-chest anatomy table; "~3433 LVLI / ~571 LVLN / ~279 CONT" counts | list-structure L4-5, L36-41; containers-and-zones L10-32 | **Generic craft** / **noise** (FQ s3 ll#113 neutral; MC ll#60 noise). Counts unused | ~35 | Delete. Add-to-winner inherits flags |
| U13 | `bulk-record-jobs` pointer | SKILL L180-183 (ll#42) | Unexercised. *Unverified*: not in this machine's houseCARL skill list | 4 | Delete |
| U14 | index.jsonl | references/index.jsonl | Routing index for references that the rewrite removes | 34 | Delete |

### Untested domains (keep a short version; the six tasks never exercised them)

| Domain | Where now | Status | Keep |
|---|---|---|---|
| Placing new gear into the `REQ_LI_{Loot,Town,Best,Blacksmith,Special}_*` backbone and the faction pools (`LItemBandit*` etc.) | SKILL Workflow 1; list-structure L43-77 | **No ledger row.** No run placed a new weapon or armour into these lists | Taxonomy in ~4 lines (names + "Town = low tier only; Best = high end"), no FormIDs. Method = H3 |
| LVLN spawn placement (new creature into spawn lists; `SubCharBandit<NN>` tiers) | list-structure L79-92; worked-ex 2; recipes D | No placement row. AC touched an own LVLN only for REQ_NULL (X4) | 2 lines: same model as LVLI; NPC refs at Level 1 |
| LVSP distribution | SKILL L105-107, step 2 | No row | 1 line: "LVSP has no merge; hand de-level a gated own LVSP" |
| Merge mechanics, `_CLI_` unrolling, 255 cap, tempered Quality lists | merge-behavior.md | Code-verified for TEMPER/UNROLL (R12), merge off in ARR | U2's 5 lines |
| Container built from scratch / `ReplaceAll` a curated set | containers-and-zones L34-53 | Exercised only by FS CopyFrom (H4) and RB (debatable) | Covered by the N4 forward rule |
| Encounter zones | see U7 | RB only, debatable | 1 line |

---

## 5. Corrected facts for this skill

| Fact (as the rewrite should state it) | Status | Source |
|---|---|---|
| No Reqtificator pass changes a list entry's Level. Under ARR's settings it does not touch a mod's own LVLI at all; it rebuilds LVLI only for REQ:TEMPER (Quality lists) and REQ:UNROLL (`*_CLI_`) mods, and merges only if `mergeLeveledLists` is on | **code + RFI exact set** (1,102 = 872 temper + 230 unroll) | U6 #7d, R12 |
| ARR: `mergeLeveledLists: false`, `mergeLeveledCharacters: false`. Was: "both on" (ll#4, #121) | **code / config** | U6 (ARR UserSettings.json) |
| RFI's LVLI are Requiem/WAR/VampireCollection Quality and `_CLI_` lists only. Was: "RFI wins most lists and shows the merged list" (ll#91) | **code + RFI** | U6, R12 |
| CONT is not merged. The only Reqtificator CONT pass stamps `REQ_LockpickControl` on every winning CONT/DOOR that lacks it (1,162) | **code + RFI exact set** | U6, R12 |
| REQ_NULL_* LVLI (197) and LVLN (63) all keep their entries; 02BC2F still drops loot. REQ_NULL = "unused by Requiem's own content", not "dead end" (ll#67, #119) | **verified live** | U6, r3 INTEGRATED §3, ledger-falkreath-shades |
| `aaLMCMagickaRegen` 000BFB and the 13 FQ `LItemRewardBQ*` lists have 0 referencers | **verified live** | R10 MC, R10 FQ C5 |
| MC s1's Master-robe removal leaves 15 robes with no referencer | **verified live** | R10 MC |
| MR College robe lists: 4/3/2/1, no Master tier; Requiem keeps Master robes in `REQ_LI_HighLevelRobes 32CEE0` | **multi-judge**, read-backed, not re-read live. Whether Master robes at L1 are wrong is contested 4/3 | r3 INTEGRATED §3 (read-backed list), R10 MC I6 |
| Single-pick reward ladders collapse to one `Gold001 @L1` at the tier value (FQ: 500/500/1000/3000) | **multi-judge** + live Requiem RewardAmount values | R10 FQ C6, R5 #5, U13 |
| Authoria Container Merge does not cover the mod's override of a vanilla container (TR Skaal chests 01F88D/01F897) | **multi-judge** (3/3 controls right; 6 judges list it) | ledger-triumvirate, U13 §3B. ARR list-layer fact |
| `openEncounterZones` sets only DisableCombatBoundary and never changes Min/MaxLevel | **unverified** in the eval (skill's own source citation; R12 did not test this path) | r3 INTEGRATED "still unverified" |
| LVLN unrolling / actor variations | **unverified** (R12 untested) | R12 L96 |
| Override-only patches are ESL-flagged in all 6 references (0/42 runs) | **verified live**; router-level, not LL | R7 |

Not this skill: the clothing-AR house convention (armor skill; r3 CRITIC) and the R9 SpellList-on-Stats line (npc triage). The only LL link to R9 is LVLN leveled-actor templates, and no LL change is needed for it.

---

## 6. Rewrite brief

**Purpose (2 lines).** Put records where the game rolls them, and remove the level gates a mod ships. Lists and containers only; the item and NPC skills own stats, and the router owns enumeration.

**Must keep (from §2):**
1. Boundary fact (H1), code wording from §5 row 1. Its home is the Requiem profile's generator contract; LL keeps a one-line pointer plus the action ("flatten Level > 1 on the mod's own live lists").
2. Read-the-comparable weighting (H2): method only, no numbers.
3. Reverse-reference the comparable, then `Add` to the winner, never a lone entry (H3), with the ARR reason: merge off, so the plain winner ships.
4. Containers: not merged; build on the winner; reference curated lists, not raw gear (H4).

**New items:**
- **N1 Liveness gate** (X1, X3; synthesis.md L300; consistency SYNTHESIS #3): before editing a list or container, and after removing an item from one, reverse-reference it, including VMAD properties. 0 referencers → skip "unreferenced", or re-home the item. Owner: the general layer ("dissect fully before reasoning"), with a one-line call here.
- **N2 Reward-ladder counter-case** (X2): single-pick ladders collapse to one Level-1 entry at the tier value. Tier values live in router economy.
- **N3 College-robe worked example** (~10 lines; synthesis.md L324): MR 4/3/2/1 with no Master tier; Masters re-homed to `REQ_LI_HighLevelRobes`; the regen-robe ENCH-match pricing (own probe, MC3) belongs in armor/economy, not here.
- **N4 Clone/override forward rule for CONT** (U13 §3B, the strongest new rule; FS H4; TR Skaal): "A mod's copy or override of a vanilla container does not inherit Requiem's or the live stack's edits. Diff it against Requiem's version and forward Requiem's content plus the mod's delta, per record. Don't assume Container Merge covers it." Skill-side miss it would fix: TR s3 skipped the Skaal chests as "merged by Authoria's container merge" (false on screen at L127; R8; ledger-triumvirate L50, "the one place the skill arm lost to every control"). Owner: router triage (all types); LL gets the container case. ARR list layer: Container Merge's scope.
- **N5 Probe queries** named in words: "references to <list> (all types + VMAD)"; "references to <comparable item> in LVLI"; "read the comparable list's entries". The call shapes live in houseCARL.

**Delete:** merge-behavior.md, worked-examples.md, housecarl-recipes.md, index.jsonl, the Checklist, Common mistakes, the freshness probe, the bulk-pass ritual (keep 2 lines), the cell → zone sweep (keep 1 line), the boss-chest table, the record counts, the REQ_NULL copies (point to the profile rule), the master-Requiem workaround and its contradictions (X5, X6).

**Keep short** (untested domains, §4): backbone taxonomy (4 lines), LVLN (2), LVSP (1), Quality/`_CLI_` "don't author" (2), zones (1).

**Target length:** SKILL.md ~70-90 lines, no references (or one ≤30-line reference holding the taxonomy and N3). Down from 1,047.

**Where content lives:**

| Content | Home |
|---|---|
| Boundary fact (no Level writes; own LVLI untouched), merge conditional, REQ_NULL by type, Quality/`_CLI_` regeneration | **Requiem profile** (generator contract + invariants, with the probe "is this list in RFI's LVLI set?") |
| `mergeLeveledLists/Characters = false`; Authoria Container Merge scope; ESL on override-only patches | **ARR list layer** |
| Liveness / reachability probe; "dissect the mod fully, VMAD included, before reasoning" | **General layer** |
| Enumeration, reconciliation, LVLI/CONT routing, the clone-forward rule for all types | **Router** |
| Comparable weighting, Add-to-winner, container curation, reward-ladder counter-case, taxonomy, College-robe example | **This skill** |
| List/container write shapes, reverse-reference, read-back, the "re-running Adds duplicates entries" warning | **houseCARL** (review-type skill or tool docs) |

**Open questions (not facts; check before encoding).** Whether any `REQ_LI_*` backbone list is in RFI's 1,102 (if so, a patch adding to it would lose to RFI, which loads last). Whether `openEncounterZones` is on in ARR and behaves as the skill says.

---

## Verification (adversarial check, 2026-10-30)

**Checked, held:**
- Every ll# rule id with a helped or harmed row in `drill/absolutes/ledger-*.json` and U8 rules[] appears: helped {8, 16, 33, 48, 58, 60, 64, 71, 99}; harmed {8, 28, 29, 30, 33, 35, 64, 150, 151} + TR tiering (X8). Cited rule texts re-read in `requiem-leveled-list-patching.json`.
- H1-H4 judgement groups re-read in `scoring/judgements/*/judgement.json` (FS r48 g39/g40, r11 g41/g43, r98 g41/g43, r93 g37; MC r45 g0-g2, r37 g0, r87 g0-g2, r52 g13-g16). All right as stated except the fixes below. None is a REQ_NULL→AbHide re-point or a clothing-AR-0 edit: R1 kept the FS chest groups as "analogue-backed" (they copy Requiem's own chest), so H4 survives the R1 relabel. Clothing AR and bows ×4 do not touch this skill.
- MC1/MC2 deciding points and joint attribution: U7 L62-63, R2 mage-core. FQ loss chain: R2 favor-quests, absolutes SYNTHESIS L87.
- False-fact claims checked against r3 INTEGRATED §3 and U6: merge off (U6 #7d, R12 L53/L75), RFI LVLI exact set 1,102, REQ_NULL LVLI 197 / LVLN 63 keep entries (U6 §1). The two orphan lists: R10 MC, R10 FQ C5.
- Unnecessary items: no ll# rule in U1-U14 has a helped row in any ledger, U7 or R2 deciding point. Line counts re-measured (1,047 total).

**Changed:**
- X1: r57 groups were off by one (now g0-g7 no_effect, g8 not_done/major). Added ll#64 (harmed) and ll#36 (missed), the FQ s2 own-probe counter-proof, the query-window contributor, and s1n's lumped 000BFB group.
- H2: s3's weighting is r52 g15 right/**minor** (novice ×3), not major; s1/s2 weighting shares the de-level group. Added the rp#14 "tier by repetition" share caveat (R2).
- H3/X3: Master-robe count clarified (18 re-homed incl. Magicka Masters; 15 school robes orphaned by s1).
- X4: reframed. LL's "leave REQ_NULL entries" was the right side; only the "dead end" premise is false, and it is latent (no LL ledger row). The AC harm is router rp#114.
- X6: ll#131 is merge-behavior L74, not worked-examples.
- X7 / H4: RB group ids fixed (r26 g40 chests, r26 g46 and r15 g40 zones, 0-based); s2's zone is MinLevel 20; split ll#101 (MinLevel 25) from ll#104/#162 (MinLevel 0).
- U1: the freshness probe is in 11 SKILL.md files, not 10. U4: 1.x names caused 0 failed calls (process/SYNTHESIS L59), now cited as evidence instead of inference.
- Neutral list: added ll#43, #23, #90 and the RB neutral rows. N4: added TR s3's Skaal miss as the skill-side case.

**Final counts:** helped 4, hurt 9 (X4 latent in LL), unnecessary 14.
