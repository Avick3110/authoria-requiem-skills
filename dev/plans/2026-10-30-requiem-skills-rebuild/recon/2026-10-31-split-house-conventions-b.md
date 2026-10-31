# Split hunt 11: house conventions B (Bruma charge offsets, Glenmoril gun charge, Bruma master-patch habits)

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. Three leftovers from split hunt 4
(`2026-10-30-split-enchant-charge.md`, rule 1 and the Glenmoril specific; origin `census/stage1/WEAP.md` conv 16) and
the census's "Bruma dominance" flags (`census/stage2/GEAR-NPC-S2.md` §7, `LISTS.md`, `CONSUMABLES.md`, `LOCKS.md`).
Scripts and data stay local (`census/splits/hc11_*`; reused spills `weap.jsonl`, `weap_ench_map.json`,
`req_ench_weapons.jsonl`, `npc.jsonl`). Live reads at epoch `e2-58972ca8618e79ea` (the census snapshot).*

## Scope

- **(a)** Bruma's 106 enchanted weapons that carry a generic `REQ_Ench_Weapon_<Effect><N>` enchantment. Comparable:
  Requiem's 3,674 enchanted weapons named "… of <word>".
- **(b)** All 179 staff-animation weapons defined in Glenmoril.esm (164 carry `zzzRevWeapTypePistol`), read live as
  source (Glenmoril.esm) and winner (the ARR patch), plus their 174 enchantments.
- **(c)** Every Bruma habit the census docs name, measured per plugin over all 237 census plugins (both stages),
  with and without `Authoria - Master Patch - Bruma.esp`. Held-out plugins left out of the shares.

## (a) Answer: there is no +1000 offset

Split hunt 4 measured Bruma's charges against the **source** enchantment. Bruma swapped 9 of them.

| # | Rule | Requiem evidence | ARR evidence |
|---|---|---|---|
| a1 | **Charge = 500 × the tier of the enchantment the patch ships** | 3,654 / 3,654 generic enchanted weapons (split hunt 4) | Bruma **106 / 106** (was "93 / 102") |
| a2 | **The name word sets the enchantment tier** (Sparks 1, Arcing 2, Shocks 3, Thunderbolts 4, Lightning 5, Storms 6; same ladder per effect) | 3,672 / 3,674 Requiem weapons named "… of <word>" carry the tier that word names | Bruma 90 / 90 named words; 12 Turn Undead prefixes (Blessed 1, Sanctified 2, Reverent 3) also consistent |

**What happened.** BSHeartland's Ayleid bow, dagger and sword lines are named "of Shocks / of Thunderbolts / of
Lightning". The source gave them Shock1 / 2 / 3. Bruma re-tiered them to Shock3 / 4 / 5 to match the names, as Requiem
names its own weapons. Then it applied 500 × tier: 1500 / 2000 / 2500.
- "3 per tier" was these 9 weapons: 3 weapon types × 3 tiers.
- The Cyrodilic Steel lines already had matching names (Sparks = Shock1 …). They needed no swap.
- No other ARR patch re-tiers a generic enchantment to match a name. The 8 other swaps (Dac0da 7, Remiel 1) are on
  uniques.

**Correction to split hunt 4, rule 1 (that doc is ARCHIVE and stays as written; this supersedes its ARR-evidence
cell and its "+1000, not chased" note).** ARR's support for the ×500 rule is mostly Bruma: 106 of the 133 ARR rows that
sit on it. Outside Bruma, 27 / 60 rows with a generic enchantment sit on it, and most of the 60 are named uniques
(rule 3 there). The rule stands on Requiem's 3,654 / 3,654, not on ARR's share.

## (b) Answer: Glenmoril gun charge is a shot count

Glenmoril gun enchantments cost **100 per shot** (cost 100 on 156 / 164; the 8 others are singletons; MAGIC conv 20).
The cost is ARR's: the source enchantments cost 10, and the patch raised every gun enchantment to 100 (Jillian lines
included). So the shot table below is ARR's own design, not the source's. So charge / 100 =
shots. ARR's patch rewrote the shot table for 5 of the 29 gun families and the NPC copies. It left the rest.

| # | Rule | Source (Glenmoril.esm) | ARR patch | Hit rate |
|---|---|---|---|---|
| b1 | **Standard bullet guns: 5 shots at +0, +1 shot per upgrade level** (charge 500 + 100 × N) | Witch Hunter Pistol / Blunderbuss, Elven Pistol / Shotgun had 500 + 50 × N (half-shot steps) | Re-stepped to 600 … 1500 for +1 … +10. +0 stays 500 | **40 / 40** |
| b2 | **Families already on +100 per level are left alone** | Musketeer Pistol / Twin Pistol (500 … 1000), Witch Hunter / Elven Gatling, Flame- / Frostsprayer (1000 … 2000) | Unchanged | 6 / 6 families (56 records) |
| b3 | **Proto-Pistol: 3 shots, +1 shot every two levels** | 500 + 50 × N | 300, 300, 300, 400, 400, 500, 500, 600, 600, 700, 700 (+0 … +10) | 11 / 11 |
| b4 | **NPC copies cap at the player's +10 gun** (15 shots) | `zzzRevCrPistol01–03` (in `zzzGHLitemHunterGuns`, `zzzRevLitemWeapBeastCitizenGUN`): 5000 | 1500 | 3 / 3 |
| b5 | **Singletons are left alone** | Beast Hunter ×3, Yelem ×3, Mythic Warmth ×2, Netch, Ozwald's, M-ARM, Dwemer ballista / gatling, Aetherium emitter, Door Knocker, GK Staff Gun (250 … 2500, costs 0 … 100) | Unchanged | 16 / 16 |

**So the "+50 … +500 step table" in split hunt 4 is b1 seen as a delta.** The source step was 50 per level, the new
step is 100, so the delta grows by 50 per level.

**Two cases the table doesn't settle:**
- **Jillian, Anti-Jillian and Histus Gravity Pistols (33 records)** keep the source's +50 step (500 … 1000). The ARR
  patch does override them (Value 500 + 100 × N, Weight 8, enchantment cost 10 → 100, magnitude), but leaves the
  charge. Ten properties were tested (log below, H11–H20). Cost, base charge, source step, weapon keywords, material,
  rate of fire, shotgun / single shot, area, duration and placement (all are craftable lines) don't separate them.
  What they share is one content batch: late FormIDs (34F5xx–435xxx), "9th" projectiles (`zzzRevPrj9thJillian`,
  `zzzRevPrj9thHist`), and untyped damage effects (no `MagicDamage*` keyword, MGEF BaseCost 1.2 v 2.3). That is
  provenance, not a rule. To the list authors.
- **Flute of Queen Ayrenn** (artifact chest + mini-boss Razboy): 2000 → 800, 8 shots. That is one above the Proto-Pistol
  ceiling, and it shares the proto bullet effect. Read: a unique on the proto line.

## (c) Answer: Bruma's habits, and where they skew the census

**Provenance first, as a fact (not the explanation).** Bruma's master patch came from a skills-era whole-mod run,
manually corrected afterwards (session handoff 2026-07-18, Bruma NPC write hygiene; split hunt 1). Its upstream
habits match the old skills' doctrine. Each habit below was first tested by domain: does it appear in other patches
whose content is in the same state?

Labels: **G** = the general ARR rule, applied to Bruma's content. **R** = Bruma follows Requiem where other ARR patches
don't. **U** = the upstream stance (skills-era). **S** = Bruma-specific, no other patch does it.

| # | Habit | Class | All plugins | Without Bruma | Bruma | Distorts a census consensus? |
|---|---|---|---|---|---|---|
| 1 | No `AE3B83` marker on NPCs that keep their own SpellList | **U** | marker on 3,094 / 3,772 (82 %) | 3,094 / 3,290 (**94 %**), 90 / 103 plugins | 0 / 482 | **Yes.** GEAR-NPC-S2 #1 ("0 / 1,052 marked") is 95 % Bruma (995 NPCs). Split hunt 1 already sets the rule without it |
| 2 | Base weapons at source scale, no skip triple (`AD3B2D`) | **U** | triple on 1,625 / 2,473 base weapons (66 %) | 1,616 / 2,325 (70 %), 57 / 63 plugins | 9 / 148 | **Yes.** GEAR-NPC-S2 #11 "weapons upstream" is Bruma only |
| 3 | Enchanted variant damage = base's unscaled damage | **G** | 467 / 540 | 145 / 218; no variant is baked anywhere (skip triple on 3 / 235) | 322 / 322 | No. The general rule is "don't bake variants". Equality only shows where the base is unbaked too. Dac0da leaves 27 / 36 at source; harmless if, as the census infers, the template's stats apply |
| 4 | Enchanted variant AR = base AR | **G** | 377 / 405 | 16 / 22 (+ held-out Mage Core 90 / 90) | 361 / 383 | No, but GEAR #21 rests on Bruma's rows (89 %) |
| 5 | `AD3B2B` on base light / heavy armour, never on variants | **G** | bases 2,604 / 2,869; variants 0 / 433 | bases 2,473 / 2,738 (90 %), 78 / 87 plugins; variants 0 / 25 | 131 / 131; 0 / 408 | No |
| 6 | Level ladders flattened to Level 1 | **G** | 1,416 / 1,418 lists | 100 / 102, 10 / 11 plugins | 1,316 / 1,316 | No (rows 93 % Bruma, the rule holds without it) |
| 7 | Ingredients set `NoAutoCalculation` | **G** | 154 / 159 | 26 / 31, 11 / 13 plugins | 128 / 128 | No |
| 8 | Generic charge = 500 × tier; re-tier by name word | **R** | 133 on-rule rows | 27 / 60 (mostly uniques) | 106 / 106 | **Yes**, for split hunt 4's ARR column (see (a)) |
| 9 | LVSP spell ladders keep every tier | **R** | lists trimmed 20 / 31 (65 %) | 20 / 22 (**91 %**) trim to the top tier(s) | 0 / 9 trimmed | **Yes.** LISTS #5 "collapse to the top tier" is the non-Bruma habit; Bruma does what Requiem does |
| 10 | LVLN ladders trimmed to the top rungs | S (per plugin) | trimmed 93 / 224 (42 %) | 39 / 49, but 38 are Dac0da (one low-mid rung) | 54 / 175 | Two plugins, two opposite tier picks. No consensus either way (LISTS open call 2) |
| 11 | Retired recipes renamed `AUTH_NULL_*` | **S** | 155 / 1,140 retirements renamed | 26 / 1,001 (LOTD 24) | 129 / 139 | Yes if a spec writes "rename retired recipes". Renaming retired **items** is general (Vigilant `NULL_` 106, FurSet `REQ_NULL_` 99, LOTD both); only the prefix varies |
| 12 | Xarrian lock-break script on mod treasure chests | **S** | 28 adds | 0, though other patches override ~300 mod-added `Treas*` containers (Dac0da 109, Unslaad 81, Vigilant 56, Glenmoril 16) | 28 | No (census already calls it Bruma-only). Note: Requiem.esp itself puts the script on 27 vanilla treasure chests, so Bruma is extending Requiem by analogy |
| 13 | Strip Playable from all 219 BSAssets head parts | **S** | 219 | 0 (no other patch touches HDPT) | 219 | No (one-plugin convention already flagged, CONSUMABLES C-12) |
| 14 | AutoCalc kept on fixed-level NPCs | mixed | dropped on 2,966 / 4,641 (64 %) | 2,731 / 4,116 (66 %) | 235 / 525 (45 %) | Small. Dac0da, Saints and Seducers and Wyrmstooth also keep it |

**Census-only counts (not re-measured here), Bruma's share of the rows:** ingredient effect rewrites (CONSUMABLES
C-5) 1,260 / 1,433; vendor-chest quantity rebalance (LOCKS #10) 147 / ~222; ChanceNone retunes (LISTS #11) 15 / 27;
KillMove race-gate merges (PERK-AI-7) all; AttackRace mapping (RACE #14) Bruma + Moonpath only. These read as
master-patch conflict work and Bruma's own retunes.

**What the spec should take from this.**
- Bruma confirms the general rules (3–7) by volume. It doesn't create them.
- It **reverses** two (1, 2: the upstream stance). Those shares must be read without it.
- It is the **Requiem-aligned** side of two splits (8, 9). There Requiem, not ARR's majority, should decide.
- Its own habits (11–13) are one plugin. Teach them only if the list authors say so.
- When the 13 `conventions.json` files are merged, give each convention a Bruma-share field.

## Open residual

- (b) Why the Jillian / Histus lines kept +50 per level. To the list authors.
- (c) The `NULL` rename prefix (`NULL_`, `REQ_NULL_`, `AUTH_NULL_`) is per plugin. Which one a new patch should use is
  a choice the records can't make. To the list authors.
- Already asked by the census, not repeated: Xarrian on mod chests (LOCKS call 2), BSAssets head parts (CONSUMABLES
  call 3), LVSP collapse (LISTS call 3), LVLN trimming (LISTS call 2). This doc adds the shares above to those calls.

## Hypothesis log

**(a) Bruma +1000**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Weapon type (bow / dagger / sword) | Dead as a cause: confounded with material (Ayleid lines are exactly these types), and the charge matches the shipped enchantment |
| H2 | Material Ayleid | Co-extensive (9 / 9) but not causal: the Ayleid Dagger reward (AbsorbHealth2 → 1000) and Ayleid staves follow their own tiers |
| H3 | Effect Shock | Dead: 12 Cyrodilic Steel Shock variants sit on the rule without a swap |
| H4 | Value | Dead: Ayleid sword Value 135 at every tier |
| H5 | Patch adds +1000 to the source charge | Dead: fits Ayleid (500 → 1500 …), not Steel (200 → 500, 300 → 1000, 400 → 1500) |
| H6 | Requiem comparable: Elven / Glass "of Shocks" = Shock3 | **Kept** as the anchor for the re-tier |
| H7 | Hunt 4 compared against the source enchantment | **Kept**: the 9 misses are exactly the 9 swapped enchantments |
| H8 | The name word sets the enchantment tier | **Kept**: Requiem 3,672 / 3,674; Bruma 90 / 90 + 12 / 12 |
| H9 | Leveled list / vendor placement | Not needed (mechanism found); untested |

**(b) Glenmoril gun charge**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Charge = shots × enchantment cost | **Kept**: cost 100 on 156 / 164 (all 8 others are untouched singletons) |
| H2 | One table per gun family | **Kept** as the shape (b1–b5) |
| H3 | Source step decides: +50 lines re-stepped, +100 lines left | Partly: 6 / 6 +100 families left; 5 / 8 +50 families re-stepped (Jillian ×3 not) |
| H4 | Enchantment cost decides | Dead: all re-stepped and kept families cost 100 |
| H5 | Ammo / effect type: bullet re-stepped, aimed "arrow" kept | Fits all 8 +50 families, but only as part of the H20 batch (the "arrow" effects are the late untyped ones) |
| H6 | Per-shot magnitude (Jillian 80 … 190 v 50 … 150) | Dead (H16) |
| H7 | Value ladder | Dead: ARR set the same Value 500 + 100 × N on Jillian and on the Witch Hunter Pistol |
| H8 | Prototype = fewer shots | **Kept**: 3 shots, +1 per two levels (11 / 11) |
| H9 | NPC copies capped at the player's +10 | **Kept** (3 / 3) |
| H10 | FormID era (Jillian / Histus are late 43xxxx records) | See H20 |

*Why the Jillian / Anti-Jillian / Histus lines kept +50 (33 records v the 40 re-stepped):*

| # | Hypothesis | Result |
|---|---|---|
| H11 | Their enchantment costs 50, so +50 charge = +1 shot (same rule) | **Dead**: ARR set cost 100 on them too (`435E2E`, `43025F`, `435DFA`: 10 → 100). They gain half a shot per level |
| H12 | Energy / beam / automatic, not bullets | Dead: FireAndForget aimed projectiles, like the pistols. The beam (Aetherium) and automatics (gatlings, sprayers) are other families |
| H13 | Different weapon type keywords | Dead: identical set (WeapTypeStaff, VendorItemStaff, MagicDisallowEnchanting, `zzzRevWeapTypePistol`) |
| H14 | Material | Dead: WeapMaterialElven, same as the re-stepped Elven Pistol |
| H15 | Shotgun or burst | Dead: re-stepped lines include both single-shot pistols and shotguns; Jillian lines are single-shot |
| H16 | Damage per shot (90 at +1 v 60) | Dead: Musketeer fires 110 at +1 and is on +1 shot per level |
| H17 | Area or duration on the effect | Dead: 0 / 0 on all |
| H18 | Base charge or source step | Dead: same 500 at +0 and same +50 source step as the re-stepped lines |
| H19 | Placement (crafted v loot) | Dead: all are recipe lines the patch also overrides; Elven Pistol is also in NPC lists |
| H20 | Added in a later Glenmoril update | Fits 3 / 3 (late FormIDs, "9th" projectiles, untyped damage MGEFs). Provenance: residual |

**(c) Bruma habits** (each tested as "does another patch with the same content state do it?")

| # | Hypothesis | Result |
|---|---|---|
| H1 | No marker = general rule for own-SpellList NPCs | Dead: 94 % of non-Bruma own-SpellList NPCs carry it |
| H2 | Source-scale weapons = general | Dead: 70 % of non-Bruma base weapons carry the triple |
| H3 | Variants unbaked = general | **Kept**: no plugin bakes a variant |
| H4 | Variant AR = base = general | **Kept** (16 / 22 outside, 90 / 90 held-out) |
| H5 | `AD3B2B` on bases, not variants = general | **Kept** (90 %; 0 / 25 variants) |
| H6 | Flattening = general | **Kept** (100 / 102 outside) |
| H7 | LVSP tier keep = Requiem-aligned | **Kept**: Bruma keeps tiers as Requiem does; others collapse |
| H8 | Recipe rename = general | Dead: 26 / 1,001 outside |
| H9 | Item rename to a NULL prefix = general | **Kept** (9 plugins), prefix per plugin |
| H10 | Xarrian = general | Dead as ARR practice (0 of ~300 mod chests elsewhere); Requiem-aligned by analogy (27 vanilla chests) |
| H11 | AutoCalc kept = Bruma-specific | Dead: 45 % v 66 % dropped, and three other plugins keep it more |
| H12 | Provenance (skills-era run) | Explains 1 and 2 (the upstream stance), already found in split hunt 1. Not needed for the others |
