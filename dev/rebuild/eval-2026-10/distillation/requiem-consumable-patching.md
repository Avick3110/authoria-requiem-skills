---
skill: requiem-consumable-patching
round: distill
date: 2026-10-30
evidence: scoring/judgements, scoring/synthesis, drill/absolutes, drill/facts, drill/process, drill/r2 (U7, U8, U10, U11, U12, U13), drill/r3 (INTEGRATED §3, R1, R2, R5, R10, R12)
verified: adversarial pass 2026-10-30 (see Verification)
---

# requiem-consumable-patching: distillation

Rule ids are `consumable#N` from `drill/absolutes/requiem-consumable-patching.json`. Run codes: FS falkreath-shades, RB rot-below, TR triumvirate; s1/s2/s3 skill runs, n = skill-1-nothink, c = control. **Judge groups are the row numbers in each run's `scoring/judgements/<rid>/judgement.md` (1-based).** Note: the FS ledger and R5 use the 0-based index, one lower (FS ledger G36 = judgement.md G37; R5's RB g41/g37 = g42/g38). **Evidence** is what a file records. **Inference** is marked as such.

## 1. What it is

It patches ALCH and INGR records (potions, poisons, oils, food, drink, drugs, ingredients) and their cookpot COBJ for Requiem. It works by live analogy to Alchemy Redone (AR) and Food and Beverages Redone (FaB) comparables. Its anchor is that the Reqtificator never touches consumables, so the override ships as written.

**Exposure (U8):** the skill was loaded in **9 of 21** skill runs: FS 4 (r48, r93, r11, r98), RB 3 (r26, r15, r54), TR 2 (r95, r69). It was never loaded in AC, FQ or MC.
- SKILL.md was fully read in all 9 runs.
- References:
  - potion-poison-ladders.md: 5 runs (FS ×4, RB r26 at 63%).
  - ingredients.md: 4 runs (FS).
  - crafting-and-integration.md: 4 runs (FS).
  - housecarl-recipes.md: 4 runs (FS, mostly partial, 21–31%).
  - food-drink-kits.md: **0 runs**.
- Rules: 202. Used 22, read with no evidence 156, never read 24 (all in food-drink-kits). The ledgers give 18 rules a helped row and **0 a harmed row** (U8). After this check, 10 of those 18 (#25, #110 and the 8 potion-shell ids) are judge variance or rode on another fix, not a measured help (see "Not a measured help").
- Records in the six tasks: 6 ALCH (FS 3, RB 2, TR 1) and 2 INGR (FS). This domain was only **thinly tested** (U8). TR's consumable lane made no write (synthesis).
- Cost: RB s1's ammo+consumable lane cost $0.72 of $21.68 (`drill/process/rot-below.json`).

## 2. Helped

| # | Item | Source | Evidence | Strength | Rewrite must keep |
|---|---|---|---|---|---|
| H1 | **Ingredient payload**: `NoAutoCalculation`, exactly 4 `REQ_Alch_*` effects, duration fixed per MGEF, magnitude and value taken from a rarity peer, Value == IngredientValue | SKILL step 3 Ingredient (#55–#59); ingredients.md payload table, durations, ladders (#158, #161, #163, #166) | FS Scarlet Nightshade + Shade Goo: s1 G38/G39 right, s2 G39 right/major, s3 G39/G40 right, n G33 debatable. Controls all not_done (c1 G32, c2 G39, c3 G30, c1n G33). R10 FS-C2: **pass 4/4 skill runs, not attempted 4/4 controls**. Facts F29: **the model lacks this**. s3 confirmed it with a reverse MGEF walk (L1348) "only after the skill named it". s2 has no cited rule in the ledger, but read ingredients.md (U8) | **Multi-judge** (3 runs) + mechanical check. Joint with the router: no control opened INGR (U11 FS: a type no control did) | Verbatim: "Flags NoAutoCalculation; exactly 4 effects, each an existing `REQ_Alch_*` MGEF; durations RestoreHealth 20 · Fortify attribute 300 · Fortify skill 60 · instant Damage 0 · Damage…Regeneration 60 · LingeringDamage 15 · Fear 15; magnitude = tier, copied from a rarity/value peer; Value == IngredientValue". Probe: read 2–3 AR ingredients bearing the same effects |
| H2 | **Restore potions heal over time** (dur 20, magnitude per second) on the Restore ladder | ladders.md Potion ladders (#180) | FS Health↔Magicka pair (042751, 1127F2): s1 G37 right, s2 G40 right; s3 G38 and n G32 debatable. Controls not_done/major (c1 G31, c2 G38, c3 G29, c1n G33). U13 N17: the reference's over-time 8×20 is **right 8/8**. R10 FS-C1 (major): **pass 4/4 skill runs, not attempted 4/4 controls** | **Multi-judge** (2 right, 2 debatable) + mechanical check. Joint with router coverage | Verbatim: "Restore Health/Magicka/Stamina: dur 20, Value 20/40/60/80/100, mag 3/5/8/10/12 per second; the Complete tier is a distinct instant MGEF (V500)" |
| H3 | **Fortify-attribute ladder** gives the elixir scale | ladders.md Fortify attribute (#182; the ledger's auto-join mislabels it #195); SKILL step 3 Potion | RB Saint's Elixir (315E4D) set to 100 for 300 s in every skill run: s1 g42 right/minor, s2 g38 right/minor, s3 g52 right/major. Controls 0 (U13 R6, "skill only"). The reference's 300→3600 s is judged wrong ×2 and debatable ×4 (PLAUSIBLE bug, U3 #39). R10 left it judgement-only (J8). *Inference:* 100/300 s is tier 5 of the ladder; s1's lane read ladders.md (U12 scan), s2 cited #32 "same-family same-tier comparable" (decision_map d193) | **Multi-judge**, but part of each "right" is the reference being wrong. Joint: the **re-link** to `REQ_Alch_*` came from the router's alchemy.md (s2; s3 probable) or the agent's own probe (s1, against #81) (R2 RB5). This skill supplied the scale, not the swap | Fortify attribute: dur 300, Value 40..200, mag 20..100; CarryWeight dur 600 |
| H4 | **Trace how a unique consumable is obtained before re-valuing it**; a mass-produced "unique" gets the tier value | SKILL Judgment (#75) | RB Saint's Elixir 5000→600, because it restocks in a vendor chest: s1 g42, s2 g38 right/minor; s3 the same (g52). The ledger logs this as "overrode #75", but s1 quotes the rule's own second clause ("a mass-produced 'unique' … gets the tier value", decision_map d196). R5 #10: the judges argued the effects, not the value | **Weak** multi-judge (value not examined) | Add the counter-case up front: "restocks in a vendor or chest = mass-produced → ladder value" |

**Not a measured help** (ledger says helped; this check says otherwise):
- **Potion shell invariants** (NoAutoCalc, Weight 0.5, one vendor keyword, duration-scaled at magnitude 0; #11, #41, #42, #76, #84, #175, #176, #188) on RB Malkifer's Elixir (CAFBBF). The s2 judge (g43, debatable) calls these "cosmetic economy changes" because the effect still points at the emptied REQ_NULL perk. The s1 g43 and s3 g51 "right" verdicts rest on the **Featherfall re-point** (`682FB4:Requiem.esp`), which R2 RB5 attributes to the agents' own probe (s1 via a consumables-lane flag relayed to magic; s3 the magic lane's MGEF sweep), not to this skill. Keep the shell as profile invariants (they are true: #84 is a probe-checkable Requiem invariant), but no deciding point turns on them.
- **Flags are unions** (#25). FS s1 G37 is right for the over-time restore, not the flag write; s3 G38 debatable. It is houseCARL write semantics. Move it there (1 line).
- **"Read the comparable recipe's own conditions"** (#110). s3 G37 was judged right, but its edit is the **same** gate as s1 G46, s2 G41 and n G35 (HasPerk Alchemical Lore OR HasKeyword REQ_RacialSkills_CreatePotionsUnperked), judged debatable. This is judge variance on one edit, not a better call.
- **The anchor fact** (#2–#5, #74, #101, #105, "the Reqtificator never touches consumables"): "right and not something the controls acted on" (synthesis). No deciding point turns on it. Keep it as one corrected line (§5).

## 3. Hurt

| # | Item | Source | Evidence | Cause | Fix |
|---|---|---|---|---|---|
| X1 | **Requiem's AlchemicalLore perk gate stamped on a mod's own bench recipes** | SKILL step 5 (#69); crafting-and-integration.md (#109); housecarl-recipes D | FS self-poison recipes (4498C4/C5), same gate in all 4 runs: s1 G46 debatable ("a plausible but invented restriction on a custom bench"), s2 G41 debatable ("applies correctly"; objects to the yield), n G35 debatable ("neither is a Requiem rule"), s3 G37 right. Ledger: neutral ×3. The synthesis calls it the skill's only weak spot. **No wrong verdict.** Gap beside it: every skill run kept the mod's ×5 yield, which two judges call generous; the reference cut it to 2. No consumable rule asks for a yield sanity check (#70/#111 only say tiers raise yield) | No scope (weak harm: judges split) | Scoped rule: "Gate a recipe only when its comparable recipe is gated. Recipes at a mod's own bench keep the mod's conditions; check the yield against a comparable." Counter-case: plain cooked-food recipes are ungated |
| X2 | **CraftingCookpot station as an absolute** | SKILL step 5 (#68), Common mistakes (#87); crafting (#109) | Cited and **overridden in all 4 FS runs**: the agents kept the mod's station and asked in the report (ledger FS "cited but overridden"). Neutral outcome; cost only | No scope | Scope it: "For a new recipe of an item Requiem itself crafts, use the cookpot. Leave existing mod stations alone." |
| X3 | **"Keep the links, swap nothing"** on potion effects | SKILL step 3 Potion (#36, #37), Common mistakes (#81) | RB s1's consumable lane re-linked Saint's Elixir off `REQ_BlessingEffect_*` **against** this line (U11 RB manual override; R2 RB5 L1391). The re-link was right (s1 g42). Live: the shrine effects carry 15 crime/stage conditions (R10 C6). Router alchemy.md says the opposite ("must reuse `REQ_Alch_*`"; "a modded ingredient using vanilla effects → replace each vanilla effect"). **No run followed #81 into a wrong call**; all 3 swapped. The harm is the contradiction, which the agent had to resolve itself | Missing counter-case; **conflict** with the router | Scoped rule: "Keep the effect only when it points at a vanilla MGEF that AR overrides in place (it already *is* `REQ_Alch_*`). Re-link to the `REQ_Alch_*` analogue when it points at a non-alchemy MGEF (shrine blessing, quest or custom effect) or one whose PerkToApply is an emptied REQ_NULL perk" |
| X4 | **"RFI defines exactly two record types (Npc, LeveledNpc)"** | Overview (#4), Notes (#101), crafting (#105) | False on ARR: RFI holds NPC, WEAP, ARMO, CONT/DOOR and LVLI (R12 exact predicted sets; U6), and the code has an LVLN path (R12 marks LVLN untested). No consumable lane acted on it | Wrong fact (stale count, latent) | Replace with the corrected boundary line (§5). Drop the counts |
| X5 | **REQ_NULL = inert stub; "no REQ_NULL_* reference remains in any patched field"** | Bulk pass (#23), Common mistakes (#89), Checklist (#99), food-drink-kits (#140) | No harm seen in a consumable lane. The same wording elsewhere cost 3 right/major (FS REQ_NULL re-points, R1). In RB the no-fall potion's REQ_NULL **perk** really is empty (R10 C5, live 0 entries), so the rule was right there by luck of type | Wrong fact (over-general) | Per type: "PERK REQ_NULLs are emptied. Every other type is live, so read the field before swapping. Don't use a REQ_NULL record as a comparable." |
| X6 | **Contradictions with the router's alchemy.md and food.md** | ladders.md (#176 "No Requiem keyword sits on the ALCH"); ingredients.md keywords (#167); food-drink-kits family kits (#127); SKILL step 5 (#67 "FaB defines zero cooking recipes") | Router alchemy.md says ALCH = "single `REQ_Alch_*` effect + a keyword" and INGR "plus a Requiem alchemy keyword". food.md gives nutrition-category effects (Meat +15 CW, Sweets +50 instant) that differ from the kit tables, and says "mirror Requiem's food recipes". RB s2/s3 lanes were briefed with alchemy.md (R2 RB5). No wrong call traced to it; the food rows are **untested** | Conflict (latent) | One owner. This skill keeps the record facts. The router keeps one routing row and no numbers |
| X7 | **houseCARL 1.x call shapes** (`housecarl_create_record`, `set_field`, `bulk_apply`, `cross_plugin_query`, `batch_record_detail`, `read_record`) | SKILL steps 1, 2, 6; Bulk pass; housecarl-recipes.md (all); also router alchemy.md L26 | 0 refused calls across all tasks (`drill/process/SYNTHESIS.md` L59). RB: "1.x create shapes sent lanes to load the tool schema (minor)". housecarl-recipes: read in 4 runs, 0 rules used | Obsolete tool shape (hygiene; cost minor) | Delete from this skill. Write recipes, Flags-union semantics and the "read-back before the patch is enabled" note move to houseCARL |

No stop-to-ask gate in this skill fired. #95 ("ships only once the effect side is balanced") and #66 ("only when … the user asks") were never exercised. No item here was a wrong verdict; X1 is the only one with a judged cost.

## 4. Unnecessary

| # | Content | Source | Why | Lines | Action |
|---|---|---|---|---|---|
| N1 | Freshness probe (Iron Sword chain) | SKILL First step 1 (#9, #10) | Duplicated ×11 across skills; the router owns it | 8 | Delete |
| N2 | Bulk pass protocol: two sweeps + FLOR sweep, dispositions, reconcile count, family list | SKILL L57–115 (#16–#28); housecarl-recipes "Coverage audit" | Duplicates the router's enumerate → route → reconcile, which U7 credits as the router's own contribution. 1 of ~14 rules used (#25, itself noise). FLOR link (#18): FS had Red Nightshade TREE harvest links, which every skill run correctly left on Scarlet Nightshade, with no rule use logged. **Thin, not exercised** | 59 + 18 | Delete. Keep a 1-line "a flora/tree harvest link must point at the patched ingredient" and a 1-line "every tier and family is read, not extrapolated" |
| N3 | Checklist | SKILL L270–300 | 0 rules used (U8). Restates the body | 31 | Delete |
| N4 | Common mistakes | SKILL L245–269 | 1 used (#84, noise per §2); #81 overridden (X3). Restates step 3 | 25 | Delete. The 3 live traps move into the body (see N7) |
| N5 | Notes: stack order, boundaries, systemic knobs, thrown powders | SKILL L301–319 | 0 used. Stack and boundaries duplicate the Overview and the router routing table | 19 | Keep 2 lines: systemic knobs are read-only; thrown vials are SCRL → magic (**untested**) |
| N6 | Overview prose | SKILL L8–33 | 0 used beyond the anchor; mostly framing | 26 | Cut to 3 lines |
| N7 | Steps 1–2: find and read the comparable | SKILL L118–152 | Generic craft: controls made analogue lookups unaided on every type they opened (U11). Only #32 cited (RB s2 d193, U11 "prompted") | 35 | Keep the EditorID tokens (`REQ_Potion_*`, `REQ_Poison_*`, `REQ_Oil_*`, `REQ_Alch_*`, `REQ_Apo_*`, `REQ_Alcohol_*`/`REQ_Drink*`) and the live-winner traps (school fortifies won by Magic Redone; CureDisease by a Wounds patch). About 6 lines |
| N8 | Step 4 routing | SKILL L195–206 | Duplicates the router routing table. 0 used | 12 | 2 lines |
| N9 | Step 6 emit + "new patch plugin" lines | SKILL L222–229 (#72, #73, #104); recipes (#142) | Generic or houseCARL craft | 10 | Delete |
| N10 | Judgment: "the mod's classification is a claim", classify by fiction | SKILL First step 2 tail, Judgment (#15, #79) | Generic craft, never cited | 6 | 1 line at most |
| N11 | **food-drink-kits.md** | reference, all | Never read (0 runs). **Untested**: no food records in the six tasks | 120 | Keep short (~35 lines): universal food shape with the hunger and nutrition FormIDs, raw/cooked paralysis, alcohol pair + tier table, drug shape, LoreBox gate keyword. Drop pattern-breaks and dual-naming trivia |
| N12 | crafting-and-integration.md: potion, food and alcohol kit worked examples; RFI evidence; satellite model; LVLI boundary; systemic knobs | reference L1–55, L82–97 | Read in 4 FS runs; 2 COBJ lines cited (#109, #110), both neutral or noise. The rest duplicates SKILL, food-drink-kits and router economy.md | 96 | Fold the ~6 COBJ lines (scoped per X1/X2) into SKILL. Delete the rest |
| N13 | housecarl-recipes.md | reference, all | Obsolete tool shape (X7). 0 used | 138 | Delete; move to houseCARL |
| N14 | ladders.md: poison ladders, oils, MGEF conversion pattern, pattern-breaks | reference L62–124 | 0 used (#195 Paralysis appears only as a mis-join of the #182 quote). Poisons and oils **untested**. MGEF conversion is the magic skill's domain | 63 | Keep the poison and oil ladders as one compact table (untested, short). Move the MGEF archetype map to magic or delete. Drop pattern-breaks |
| N15 | ingredients.md: keywords, REQ_Ingredient precedent set, anomalies | reference L55–86 | Read in 4 runs, 0 used | 32 | Keywords 3 lines. Anomalies (CC Curios auto-calc forwarder, Wounds/disease effect injection) → ARR list layer |
| N16 | evals/ + index.jsonl | skill folder | Trigger tests and a topic index, not instructions (absolutes notes) | 325 | Move out of the shipped skill (dev repo) |
| N17 | "Live analogy, never hardcoded; live read is the authority" restated | Overview (#6–#8), ladders (#174, #200), food (#118) | Repeated in every skill; generic | ~8 | One line, in the general layer |

**Untested domains** (keep short versions, do not delete as "useless"):
- food, drink and alcohol kits, and drugs
- poison, lingering, weakness and paralysis ladders
- oils
- thrown powders (SCRL)
- Requiem's own cookpot craft lane
- racial cuisine, Green Pact and `LoreBox_*`
- the FaB anti-stack key `Apo_NosStack`

**Thin:** potions (6 ALCH), ingredients (2 INGR), flora harvest links (FS Red Nightshade only). The fortify-skill classes A/B and the resist ladders were never exercised.

Total removable: about **640 of 886 .md lines**, plus 325 non-instruction lines.

## 5. Corrected facts for this skill

| Fact | Status | Source |
|---|---|---|
| The Reqtificator emits NPC, WEAP, ARMO, CONT/DOOR and LVLI (and has an LVLN path), and **not** "only NPC + LeveledNpc". No code path in the tested build writes ALCH, INGR or COBJ, so a consumable override ships as written | NPC/WEAP/CONT-DOOR/LVLI sets: **code + RFI exact** (R12); ARMO AR rescale code + RFI 70/70; LVLN code only (untested, R12). "No consumable path": **inference** from R12's code-path table (consumables not directly counted in the eval) | R12, r3 INTEGRATED §3, U6 |
| REQ_NULL depends on type. PERK is emptied (147/154); NPC_, WEAP, ARMO and MGEF are kept and mostly live. Read the field; don't swap by name | **Verified live** | r3 INTEGRATED §3, R1, R7, U6 |
| The REQ_NULL fall-damage perk on the no-fall potion has 0 entries. Featherfall `682FB4:Requiem.esp` is the working analogue | **Live** | R10 RB C5; R2 RB5; r54 g51 |
| Shrine `REQ_BlessingEffect_*` MGEFs (0FB98E/0FB98F) carry 15 crime/stage conditions, so they are not alchemy effects. Potions use `REQ_Alch_*` | **Live** (in-run read RB s1 L1513) + multi-judge | R10 RB C6; facts #39 |
| Restore potions heal over time: dur 20, ladder 3/5/8/10/12 per second | **Multi-judge** (U13 N17 8/8) + R10 FS-C1 4/4 skill pass. Ladder mined 2026-07-15, not re-read in the eval | ladders.md; R10 |
| Ingredient payload (NoAutoCalculation, 4 `REQ_Alch_*`, per-MGEF durations, Value == IngredientValue) | **Multi-judge** + R10 FS-C2 4/4 + live in-run walk (FS s3 L1348) | facts F29; R10 FS-C2 |
| A unique quest elixir that restocks is mass-produced, so it gets the ladder value | **Multi-judge** on the edit (g42, g38, g52); judges argued effects, not value | R5 #10 |
| The reference's Saint's Elixir at 3600 s is a reference bug | **PLAUSIBLE** only; don't encode | U3 #39 |
| houseCARL 1.x tool names cost 0 refused calls | Process evidence | `drill/process/SYNTHESIS.md` |
| All other ladder and kit numbers (fortify skill classes, poisons, oils, food, alcohol) | **Unverified in this eval** (skill-mined 2026-07-15) | U8 |

The clothing-AR caveat (CRITIC) and the bows-x4 correction do not apply to this skill.

## 6. Rewrite brief

**Purpose.** Requiem's consumable record shapes and the ladders a strong model cannot derive without a lot of reading: the ingredient payload, the potion ladders, the food and alcohol kits. Everything else is method the model already has, or belongs to another layer.

**Must keep** (H1–H4, verbatim facts in §2):
1. Boundary line: no Reqtificator consumable path; the override is final (corrected wording, §5).
2. Class shapes (true invariants, not measured wins):
   - potion/poison/oil shell: NoAutoCalc, 0.5, one vendor keyword;
   - food: FoodItem + 1 hunger + 1 nutrition;
   - alcohol pair;
   - drug: NoAutoCalc only;
   - ingredient payload (measured win, H1).
3. Ladders as one compact table: Restore (heal-over-time, H2), Fortify attribute/regen/skill (H3), Resist, duration-scaled, poisons and oils (the last two marked untested).
4. Ingredient durations per MGEF and the rarity-peer rule.
5. Unique consumables: trace how they are obtained. Restocking = ladder value (H4).
6. COBJ: copy the comparable recipe's conditions; don't assume the OR; yield, not potency; sanity-check the yield.

**New items:**
- Effect re-link counter-case (X3): keep vanilla-FormID effects that AR overrides in place; re-link non-alchemy, quest or blessing MGEFs and emptied-REQ_NULL-perk effects to the `REQ_Alch_*` analogue. This also covers the Malkifer's Elixir case (the no-fall effect's REQ_NULL perk → Featherfall), which no skill line drove.
- REQ_NULL by type (X5) and the corrected boundary (X4).
- Scoped COBJ station and gate (X1, X2), with the mod's-own-bench counter-case.
- Probe queries for invariants (houseCARL 2.x `housecarl_records`, the shape used in RB s1's lane per U12):
  - "AR ingredients bearing effect E" (durations);
  - "`REQ_Potion_<Family>*` at tier N" (value and magnitude);
  - "every ALCH in `<mod>` with NoAutoCalc unset" (invariant check).
- Liveness check: does anything use this consumable (container, list, quest, vendor)? No skill holds it (U13 FQ, facts #45). Put it in the general layer.
- The R9 SpellList-on-Stats line is NPC-only. It does not belong here.

**Delete:**
- freshness probe
- bulk pass (keep the harvest-link line)
- Checklist
- Common mistakes
- step 6
- housecarl-recipes.md
- crafting-and-integration.md, except 6 COBJ lines
- MGEF conversion map (to magic)
- evals/ and index.jsonl
- the RFI type counts
- the "inert REQ_NULL" wording
- the router alchemy.md/food.md numbers
- Flags-union line (to houseCARL)

**Target:** SKILL.md about 90 lines, plus one reference of about 60 lines (ladders + ingredient tables), plus a food/alcohol reference of about 35 lines (untested, kept short). About 185 lines against 886 today.

**Where content lives:**

| Content | Home |
|---|---|
| Class shapes, ladders, ingredient payload, re-link counter-case, COBJ scope, unique-value rule | **this skill** |
| Routing row for ALCH/INGR/consumable COBJ; enumeration and reconcile; no consumable numbers (fixes X6) | **router** |
| Generator contract (no consumable path), REQ_NULL per type, analogue keys (`REQ_Alch_*`, `REQ_Apo_*`, `REQ_Alcohol_*`, `REQ_Potion_*`), invariants with probes (NoAutoCalc universal; Value == IngredientValue; 4 effects) | **Requiem profile** |
| Live winners above the addons (Magic Redone school fortifies, Wounds CureDisease, Taberu animation marker, hand-authored FaB layers, CC Curios forwarder anomaly) | **ARR list layer** |
| Write recipes, Flags-union write semantics, read-back before enabling; a review check "consumable has auto-calc on / effect count ≠ 4" | **houseCARL** |
| "Read the whole mod first"; live analogy over hardcoding; state the assumption when there is no comparable | **general layer** |

## Verification

Adversarial pass, 2026-10-30. Checked against `scoring/judgements/<rid>/judgement.json|md`, all six `drill/absolutes/ledger-*.json` (uses + RB decision_map), U7, U8, U11, U13, r3 INTEGRATED §3, R2 RB5, R5, R10 FS/RB.

**Changed:**
- **Judge-group numbers fixed.** The draft mixed 0-based (FS ledger, R5) and 1-based (judgement.md) numbering. All now use judgement.md rows. FS: s1 G36/37/38/45 → G37/38/39/46; s2 G38/39/40 → G39/40/41; s3 G36/37/38/39 → G37/38/39/40; n G31/32/34 → G32/33/35; c1n G32 → G33; INGR controls c1 G32, c2 G39, c3 G30. RB Saint's Elixir s1 g41 → g42, s2 g37 → g38, s3 g51 → g52 (s3 g51 is Malkifer's).
- **H4 (potion shell) moved out of Helped.** The judges' "right" on Malkifer's Elixir rests on the Featherfall re-point (own probe, R2 RB5); s2's judge calls the shell edits cosmetic.
- **H5 (Flags union) moved out of Helped.** The group verdict turned on the restore effect, not the flag write.
- **H7 (#110) moved out of Helped.** s3's "right" recipe edit is the same gate the other three runs made, judged debatable: judge variance.
- **X1 weakened.** No wrong verdict; judges split (3 debatable, 1 right) and only one calls it invented. Added the yield-sanity gap.
- **X3 clarified.** No run followed #81 into a wrong call; the harm is the router conflict. Added alchemy.md's "replace each vanilla effect" line.
- **H1/H2 strengthened** with R10 FS-C2/FS-C1: pass 4/4 skill runs, not attempted by any control.
- **H3:** fixed rule id (#182; decision_map's #195 is a mis-join), noted R10 J8 (judgement-only) and that part of "right" is the reference being wrong.
- **FLOR harvest link** moved from "untested" to "thin": FS had Red Nightshade harvest links.
- **N7:** #32 was cited once (RB s2), not 0.
- **§5 boundary row:** LVLN marked code-only (R12 untested); ARMO sourced to the AR-rescale code + RFI 70/70.

**Checked, unchanged:**
- Every consumable rule with a helped row in the ledgers (18: #11, #25, #41, #42, #57, #58, #75, #76, #84, #110, #158, #161, #163, #166, #175, #176, #180, #188) appears here. **0 harmed rows** exist for this skill in any ledger. Neutral rows (#68, #69, #109, #163 n, #164 n, #25 s3, #180 s3) are covered by X1, X2 or H1/H2; #164 (route effect design to magic) never decided anything.
- No consumable rule appears in the AC, FQ, MC or TR ledgers. TR's lane made no write.
- False facts: X4 and X5 match r3 INTEGRATED §3 (PERK is the only type routinely emptied; RFI record sets). The clothing-AR and bows-x4 corrections do not touch this skill.
- Unnecessary items: none is load-bearing in U7 or R2 deciding points. RB5's consumable side is router alchemy.md (s2), own probe (s1, Featherfall), not this skill's bulk pass, checklist or recipes.

**Final counts:** Helped 4 (2 strong with a mechanical check, 1 multi-judge, 1 weak); Hurt 7 (0 wrong verdicts; 1 weak judged cost, 2 override-only, 4 latent or hygiene); Unnecessary 17 items (~640 of 886 lines, plus 325 non-instruction lines).
