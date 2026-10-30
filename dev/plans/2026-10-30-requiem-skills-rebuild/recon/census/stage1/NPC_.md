# NPC_ census: how ARR's handmade Requiem patches treat NPCs

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 5,499 NPC records (3,091 templated / 1,564 stat-set / 844 AutoCalc), 296,037 ledger rows, 114 patch
plugins. 4 of the 6 held-out eval references carry NPCs: Animated Carriages, Falkreath Shades, The Rot Below,
Triumvate (309 records, 8,268 rows). Template fit: 571 verdicts (script + LLM, verifier-sampled 48).

Counts are quoted from `consensus/Npc.json`. "Held-out" = rows in the 4 held-out plugins. Conventions were built
from the other 110.

## 1. Conventions

| # | Convention | Class | Input / baked | Support (rows / plugins) | Held-out | Conf. |
|---|---|---|---|---|---|---|
| 1 | **AlreadyReqtified `AE3B83`** (build skips the NPC) on 3,221 / 5,499 NPCs (59%): nearly all stat-set (1,529 / 1,564) and AutoCalc (716 / 844), but only 976 / 3,091 templated (32%), and only 27 of the 222 NPCs ARR gave a Template | house convention (Requiem's own NPCs: 0); code `ActorAlreadyReqtifiedSkip.cs:11` | input | 3,221 / 93 (+1,647 marker rows in the Keywords family / 88) | partly: Rot Below 61, Triumvate 41, Shades 21 rows; **Animated Carriages 2 rows on 175 NPCs** (173 templated, no marker) | high |
| 2 | On those NPCs, hand-carry every perk / spell the Reqtificator's rules would assign (`*_assign` + RFTI kit) | baked: the config entry is the proof | **baked** | 155,967 / 85 | follows: 5,879 | high |
| 3 | Plus a hand **kit beyond the rules**: ~47–67 perks (Requiem + Magic Redone), and ActorEffect lists (4 items for common NPCs, 4–12 for casters: `RFTI_All_*` controllers, `RFTI_NPC_PersistentSpellRescaling`, `REQ_Trait_*`) | mixed (Requiem-shaped, values are author choices) | input (verifier: build skips these NPCs, so it can't be output) | Perks added 19,597 / 33; changed 1,508 / 79; ActorEffect changed 1,804 / 80, added 770 / 23 | follows: 579 + 87 + 60 + 22 | med |
| 4 | **Rebuild the kit, don't top it up.** The source's placed perks / mod abilities are removed: REQ_NULL / LEGACY stubs, but also live Requiem perks and Apocalypse `WB_*` | house convention; dropping NULL stubs matches Requiem (7 / 2,702 of its NPCs carry any) | input (build only ever Adds) | Perks removed 1,225 / 24; ActorEffect removed 85 / 9 | follows: 22 / 0 | med |
| 5 | **Fixed level:** PcLevelMult → NpcLevel, 1–100, 100 for bosses | Requiem-aligned (2,633 / 2,702 Requiem NPCs fixed) | input (build never writes Level, FACTS C) | Level 3,314 / 30; LevelMult 1,067 / 25 | follows: 38 / 36 | med (value rule unknown) |
| 6 | **Drop AutoCalcStats**, keep the other flags | Requiem-aligned (Elenwen fixed, no AutoCalc) | input | 2,966 / 31 | follows: 4 | med |
| 7 | **Hand-set the stat block:** Health / Magicka / Stamina offsets and base values | house convention, Requiem-shaped (Elenwen HealthOffset 460) | input (no build writer) | HealthOffset 3,063 / 25; Stamina off. 2,914; Magicka off. 2,379; bases 2,547 / 2,810 / 1,992 | **barely:** 1 offset row each, 0 base rows | med |
| 8 | **Skills flattened to 5 where touched** (15 → 5 on 27,988 rows); 55 / 75 / 100 for specialists, but mid values (15–50) also appear (Vigilant Traitor 000D62 mixes 5 / 15 / 20 / 50 / 75; TOK 461299 keeps source 15s) | Requiem-aligned (680 / 2,702 Requiem NPCs have Block = 5) | input | 49,504 / 22 | weak: 71 rows | med (verifier-fixed; class was "unclear") |
| 9 | CalcMin / CalcMax zeroed, sometimes. It's a separate partial step, not part of the level change | Requiem-aligned norm (77% of Requiem's fixed-level NPCs have 0/0), applied inconsistently | input | 447 / 459 and 310 / 344 rows → 0; 15 plugins | not seen (0 rows) | med |
| 10 | **Workflow A for template-less mod NPCs:** set Template + `Stats, SpellList`, pointed at a Requiem encounter / look template chosen by role, or at a named vanilla civilian (Lisette 013297, Beirand 013261) for town NPCs | Requiem-aligned in shape (fact 20) | input | Template 222 / 34; TemplateFlags 578 / 48 (~83% end Stats+SpellList) | follows in one plugin: of 185 held-out TemplateFlags rows, 173 are Animated Carriages (also 14 / 22 held-out Template rows); Rot Below has 6 | med |
| 11 | Stats-only and SpellList-only templating are deliberate variants (the NPC keeps its own 46–54-perk kit). Requiem has 199 Stats-only and 103 SpellList-only | Requiem-aligned | input | TemplateFlags variants 1114, 1749, 1523, 854, 1021, 1722, 2084 | n/a | med |
| 12 | `REQ_MinorKnockdownImmunity 5F367F` goes on mod bosses / large creatures | Requiem-aligned (Requiem puts it on 12 NPCs: Miraak, Ebony Warrior, giant mudcrabs) | input | 14 / 5 | none | low |
| 13 | Source-mod scripts, packages, factions, combat style and VMAD re-orders are carried unchanged | noise | input | VMAD removed 228 / 32 (10 checked: reorder only) | follows | med |
| 14 | Edits written under a masking template flag (no effect) | noise (engine semantics) | n/a | 16,528 / 37 | 278 | high |

**Validation summary.** The held-out patches follow 2–6 at volume and 13–14 as noise. They follow 1 except Animated
Carriages, which skips the marker. They barely hand-set stat blocks (7) or skills (8). They split by plugin:
- **The Rot Below** is stat-set / AutoCalc + AE3B83 + kit + level conversion (38 Level rows, only 6 TemplateFlags
  rows).
- **Animated Carriages** alone templates (173 / 185 held-out TemplateFlags rows), with no marker.

So "held-out leans on templating" is one plugin. Template fit on held-out NPCs matches the rest: 111 / 185 fit (60%),
against 350 / 571 overall (61%).

**Three groups, not two:**
- (A) ARR-templated NPCs, mostly without the marker;
- (B) stat-set / AutoCalc + AE3B83 + kit;
- (C) **source-templated + AE3B83 + kit:** 976 templated NPCs carry the marker and kit, 937 of them with no ARR
  Template / TemplateFlags edit.

A rebuilt skill must pick per NPC, and check fit when it templates (§4.3).

## 2. Upstream checklist (what an agent writes, in order)

1. **Decide the group per NPC:** A (template), B (stat-set + AlreadyReqtified + kit), or C (keep the source's
   template + AlreadyReqtified + kit). "Generic → A, uniques / bosses → B" is a **lead only**; the evidence doesn't
   show what rule ARR used (§6 call 1). Read the record's existing Template / TemplateFlags first, because a source
   may already template (937 group-C NPCs; Animated Ships had Stats on a bandit list before ARR touched it).
2. **Workflow A:**
   a. Pick a template by **role, race and tier on the resolved chain**, not by name. Expand LVLN and follow template
      of template. `REQ_LookTemplate_*` are bandit-only. A civilian takes a civilian (Lisette / Beirand), a mage
      takes a mage template, and a high-level NPC must not take an L13 template.
   b. Set flags. `Stats, SpellList` is the default, because perks and spells inherit only through SpellList
      (fact 4). Use Stats-only if the NPC keeps its own kit, SpellList-only if it keeps its own stats.
   c. Leave the masked fields alone, since edits under a mask do nothing (conv. 14).
3. **Workflow B** (C: steps a, e, f only, on top of the source template):
   a. Add `AE3B83` AlreadyReqtified.
   b. Change PcLevelMult to a fixed NpcLevel, and remove AutoCalcStats.
   c. Write the stat block: offsets + base Health / Magicka / Stamina. Skills: mostly 5, specialist skills 55–100,
      and mid values (15–50) occur.
   d. Zero CalcMin / CalcMax, which is the Requiem norm. Aaron confirms (§6).
   e. Rebuild the perk list. Drop REQ_NULL / LEGACY stubs and the source's ad-hoc perks, then write the role kit.
   f. Write ActorEffect: the role's Requiem traits (`REQ_Trait_Armor_*`, `REQ_Trait_ResistMagic*`) and the mod's
      Magic Redone spells. Drop the mod's own scaling abilities (e.g. Boss Difficulty).
4. **Boss / large creature:** add `5F367F` where Requiem would (conv. 12).
5. **Race clone / new armour in the patch:** repoint Race / WornArmor / DefaultOutfit to the patch record
   (11 + 42 + 17 rows; not verified, so these are leads).

## 3. Downstream bake package (only on AlreadyReqtified NPCs)

The build skips AE3B83 NPCs entirely, so whatever it would stamp has to be carried by hand:

- **Rule-assigned perks / spells:** everything `ActorAssignmentRules_*.conf` `*_assign` and the RFTI kit give that
  NPC (race lists, global perks, RFTI_All_* controllers, e.g. `AE3597` CrossbowStagger, `03E556` BaseAttackSpeed,
  `062275` ExhaustionController, `AD3977` PersistentSpellRescaling). This is the 155,967-row family. It's baked by
  config proof.
- **Not baked (it looks baked, but it's input):** Level, AutoCalc, skills, stat block, and the non-config kit
  perks. The C# writes none of these on overrides (FACTS C). Don't teach them as "what the build would do".
- On a Workflow-A (Stats+SpellList) NPC **without** AE3B83, carry none of this either. The reason:
  `ActorPerksFromRules`, `ActorSpellsFromRules` and `ActorGlobalPerks` return early when TemplateFlags has
  SpellList, so the build stamps nothing there, and the perks / spells come from the template. The build stamps only
  NPCs with neither SpellList nor AE3B83 (order: `MainLogicExecutor.cs:291`, skip first).

## 4. Per field / family detail

### 4.1 Perks and ActorEffect (conv. 2–4)
- **Consensus:** the kit sits on AlreadyReqtified NPCs. Among residual Perks-added rows, 17,387 / 19,597 are on
  AlreadyReqtified NPCs. The verifier moved all four kit families from "baked" to **input** (material errors): the
  skip transformer runs first, so no perk on those NPCs can be build output. Masking was overstated (SpellList-masked
  9,374 / 166,248, not the reader's 6,647-of-19.6k).
- **Exceptions:** pattern 183 (a single-perk list on a creature) is a different convention, not a kit.
- **Removal (4):** of 18 resolved removed IDs, ~9 are NULL / LEGACY, ~6 live Requiem perks (SwordFocus1,
  PowerfulCharge, Electromancy1, Agility) and 3 Apocalypse `WB_*`. Candidate reasons: a deliberate role re-kit (an
  author habit, consistent over 24 plugins), or collateral from the rebuild. Example: zzzAoMBossMedium 7 → 59 perks.
  It's a swap, not a stub clean-up.
- **ActorEffect removed (85 / 9):** `cannot-tell`, not re-derived, so this is a lead.

### 4.2 Level, flags, stats, skills (conv. 5–9)
- **Level:** PcLevelMult → NpcLevel (Vigilant Traitor 000D62 1.1 → L50; zzzAoMBossMedium → 100; Whore → 36; Thief
  27). There's no visible value rule. Candidate reasons: hand judgment per role, or matching a comparable Requiem NPC.
  Pattern 79 (2,252 rows) has null before/after, so it couldn't be judged.
- **Stat block:** 1,723 HealthOffset rows are on AlreadyReqtified NPCs. The reader said ~half were Stats-masked
  (1,717 templated). The verifier measured only 219 / 3,281 Stats-masked. There's no formula in the data. Vigilant Traitor:
  HealthOffset 50 → 250.
- **Skills:** 35,469 of 37,409 "5" rows are live, not masked. The reader's "57% templated" was wrong (verifier: ~5%).
  So 5 is a deliberate low placeholder, and the fixed Health / offsets carry strength.
- **CalcMin / Max:** BossMedium keeps 40/45, Whore 20/50 and FleshGuardian 10/15 after conversion. Only Thief went
  20/25 → 0/0. So zeroing is independent and partial. Candidate reasons: patch age or author habit. It's inert on a
  fixed-level NPC either way (the engine reads CalcMin/Max only for PcLevelMult), so it's low stakes.

### 4.3 Template and TemplateFlags (conv. 10–11), and template fit
- **Template (222 / 34):** mostly null → template. The verifier read one chain live: WSTotWPactusAcentis (class
  Citizen, Solitude factions) → `039CFD` REQ_LookTemplate_EncBandit01TemplateMelee → `86837E`
  REQ_Bandit_Template_SwordShield_01. Stats and SpellList are inherited, so a citizen gets bandit level, perks and
  spells. Pattern 473 is reclassed **outlier bug** (§5).
- **TemplateFlags (578 / 48):** 577 / 578 rows are templated. Variants 1114 / 1749 / 1523 (Stats-only) carry their
  own 46–54 perks; 1722 / 2084 remove SpellList (own 48 / 67-perk kit); 854 / 1021 are SpellList-only. All are
  judged deliberate. **1403** (Glenmoril shadow) cut down to Traits + AttackData on a patch-made template
  (EchoLevelled), keeping PcLevelMult with Calc 80/200. That's unusual, `unclear`. HSRen4NPC (pattern 1936)
  Stats+SpellList onto `REQ_LookTemplate_EncVigilantOfStendarr05NordM01` is a fit lead, unverified.
- **Fit totals (verifier-corrected):** fits 350, misfit-role 94, misfit-tier 26, misfit-race 13, design-question 40,
  unclear 37, inert 11.
- **Fit reliability:**
  - LLM non-fit verdicts held 17 / 20 and LLM `fits` 8 / 8. Some LLM design-questions were upgraded to misfit-role
    (Animated Ships Sailor / Courier on `zxSHLCharBanditM`) and two `unclear` to misfit-role (Carriages Farmer F
    000B32 / 000B31 on a mixed Thalmor / Vigilant pool).
  - **Script misfits held only 2 / 6.** It misread magic-bandit templates (`REQ_LookTemplate_EncBandit05Magic*`,
    `...06MagicDarkElfM`) and vanilla civilians (EvetteSan, Lisette) as combat pools, because it failed to resolve
    them. Script `fits` held 9 / 9. **The 55 `source: script` misfits are leads, not findings.** That includes all of
    Once We Were Here, Sithis, Penitus Oculatus, Lunar Guard, Traveling Mage and Ashe, and most of the 17 Animated
    Carriages "civilian on a combat pool" rows.
- **Who introduced the misfit:**
  - **ARR did** in Grand Solitude (0 → Stats, SpellList, null → `039CFD`, 26 NPCs), Tools of Kagrenac (Stats flag
    added on 20 records, no Template change) and Dac0da (6 records repointed from the mod's 00D0A0 to `01E777`
    LCharWarlockNecromancer, pattern 1115).
  - **The source did, and ARR extended it,** in Animated Ships 000878 / 000891 / 000A13. The source already had
    Stats on a bandit list; ARR added SpellList.
  - Not every LLM Grand Solitude misfit is a bug. A68DFC / A68DFA (on Lisette) were flipped to fits. Of the 34 LLM
    misfit-role verdicts there, only the 26 on 039CFD are firm.
  - Animated Carriages (held out): ARR added Stats+SpellList onto `zxAC_LCharALL*` mixed pools.

### 4.4 Smaller families
- **Keywords added 14 / 5:** `5F367F`, confirmed Requiem-aligned (above).
- **WornArmor 42 / 5, DefaultOutfit 17 / 6, Race 11 / 5:** these repoint to records the patch or mod defines
  (Karura's Wyrmstooth Refined, Vigilant NPCs Refined, HLIORemi, Kaidan race clones). There are also some
  vanilla → vanilla swaps (patterns 3212 / 3198 / 3193, 3219 / 3304 / 3742), and Hagraven skin 097243 → null on 3
  NPCs (pattern 1776). All `cannot-tell` / unsampled, so they're leads.
- **Items removed 78 / added 33 / changed 23:** creature loot stripped, Requiem-tier gear added. Not re-derived.
  Inigo 3134 / 3135 is an ExtraData rewrite, not a removal.
- **Class 26 / 4:** Apocalypse conjured actors → Enc* classes (017008 / 01CE1C / 0131E6). Not re-derived.
- **AIData (S&S CC):** Aggression VeryAggressive → Aggressive, Assistance → HelpsFriendsAndAllies, 4 rows each,
  one patch. Intent unknown.
- **Noise:** PlayerSkills.Unused / Unused2 (padding), Weight, Sound, Mood, Factions rank-0 re-expression, carried
  packages (but Authoria CC Fishing adds AI Overhaul.esp packages, pattern 3860, which is a cross-mod dependency).

## 5. Suspected bugs (for Aaron's review; not filed)

| Record (FormID, plugin) | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|
| 26 Grand Solitude civilians / guards, null → `039CFD` (pattern 473), e.g. WSEnbjorf A68798, A691F0, A6912D, A6917F, A68EDD, A68899, A686C2, A684EC (patch Authoria - Reqtificated - Grand Solitude) | Template + TemplateFlags | `039CFD` REQ_LookTemplate_EncBandit01TemplateMelee, Stats+SpellList | a civilian template (as Lisette / Beirand / EvetteSan elsewhere in the same patch) | Ledger: exactly 26. Fit verifier confirmed 8 individually. Live chain → `86837E` L3 sword-shield bandit | **outlier bug** (one patch, one pattern) |
| Tools of Kagrenac Ayleid skeletons: 20 flag edits, 16 judged (tiers 03–06), e.g. 461299 (L51) → `07F393` Ayleid Archer L13; 46129A (L63) → `07A28B` Ayleid Warrior L13 (patch Authoria - Reqtificated - Tools of Kagrenac) | TemplateFlags | Stats **added** by ARR (source flagged SpellList, not Stats; no Template change) | keep own Stats, or pick a tier-matched template | Live: 461299 L51 is now Stats-masked; 07F393 is L13 with flags 0. 16 LLM misfit-tier verdicts, only 2 verifier-sampled | **suspected systemic bug** in this patch. "Every tier L13" rests on LLM live-read lines, 2 verified |
| Dac0da, 6 records: 0043CB, 0043CC, 0043CD zDcdLvBlackworm2H, 0056F1, 0056F2, 0056F3 zDcdLvBlackwormDual_WS2 (pattern 1115) | Template | 00D0A0 → `01E777` LCharWarlockNecromancer (ARR repoint) | melee / assassin pool | Fit verifier agreed on 0043CD / 0056F3 (material) | outlier bug |
| Animated Ships 000878 zxShRiftenCourierHonnigbrew, 000891 Windhelm Sailer, 000A13 Solitude Courier | TemplateFlags | SpellList added onto `LCharBanditMissile` / `zxSHLCharBanditM` | civilian stats / spells | Verifier agree, material. The Stats misfit is the source's; ARR extended it | outlier bug (ARR compounded a source issue) |
| Animated Carriages (held out) 000B32 / 000B31 Farmer F, 000A95 Psijic (on Vigilant list), 000B65 Dawnguard (on Imperial soldier list) | TemplateFlags | Stats+SpellList added onto mixed / combat pools | role-matched pool | Verifier agree / disagree → misfit-role | outlier bug in a reference patch: eval scoring must not reward copying it |
| Glenmoril 3B1B54-56 zzzRevSq03SacrificeEcho01-03 | template race | misfit-race (LLM) | — | not verifier-sampled | lead |

## 6. Open calls for Aaron

1. Three groups (ARR template / stat-set + AE3B83 + kit / source template + AE3B83 + kit): what decides which one an
   NPC gets? Is it uniques v generics, patch age, or author habit? Animated Carriages (held out) templates 173 NPCs
   without the marker; Rot Below doesn't template.
2. Fixed level: is there a value rule (comparable Requiem NPC? role table?), or hand judgment?
3. Skills flat 5 for non-specialists: is that an ARR rule?
4. CalcMin / Max zeroing: should a rebuild always zero (Requiem norm), or leave it (inert on a fixed level)?
5. Kit rebuild removes **live** Requiem perks (SwordFocus1, Agility…): deliberate re-kit, or collateral?
6. Named vanilla civilians (Lisette, Beirand) as stat templates for town NPCs: confirm as house convention.
7. Stats-only / SpellList-only / SpellList-removed variants (1114, 1749, 1722, 2084): confirm deliberate. Pattern
   1403 (Glenmoril shadow, flags cut to Traits + AttackData): intended?
8. S&S CC AIData softening (Aggression / Assistance): deliberate?
9. The §5 template misfits: confirm as bugs, and whether the eval should score them against the agent.

## 7. Limits

- **Untouched records are invisible.** This is a diff of what patches changed. NPCs a patch should have fixed and
  didn't don't show up.
- The non-config kit's **contents** (which perks per role) aren't resolved per ID. Patterns show list sizes, not
  members, so a role → kit table can't be derived from this evidence.
- **Unsampled / cannot-tell families** (leads only): ActorEffect removed, Items *, WornArmor, DefaultOutfit, Race,
  Class, DeathItem, AttackRace, SpeedMultiplier, VMAD added (216 on AlreadyReqtified, unverified).
- Template fit was verifier-sampled at 48 / 571. The 55 script misfits are mostly unverified and the script is
  unreliable on non-fit (2 / 6). The fit verdicts don't say how big the in-game impact is (a low-tier soldier pool on
  a driver is minor).
- Level / skill value patterns with null before/after (Level pattern 79, SkillValues per index, SkillOffsets) can't
  be judged per value.
- `ksws03_quest.esp` failed to load during the diff (order_degraded on the Vigilant batch). So Vigilant's 70k rows
  were diffed against a degraded provider order, and the ksws03 patch's 3 NPC_ records may be missing or misread.
- Only 4 of the 6 held-out patches have NPCs, and Animated Carriages has 664 rows. The held-out validation of the
  stat-set conventions (7–9) rests on very few rows.
