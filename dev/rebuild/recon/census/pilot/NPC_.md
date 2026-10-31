# NPC_ census (pilot)

*Author: Aaron (+ Claude session) · 2026-10-30*

Corpus: 264 NPC_ records from 2 patches. **Fit:** Authoria - Reqtificated - Grand Solitude (GS), 89 NPCs (plus 14
containers, 103 records). **Held out:** Authoria - Reqtificated - Animated Carriages (AC), 175 NPCs. Sources:
`pilot/work/consensus/Npc.json` (counts), `pilot/work/annot/npc-*.final.jsonl` + `npc-fit.jsonl` (verified),
`pilot/work/verify/*`, fresh houseCARL reads (epoch e2-58972ca8618e79ea), Reqtificator C# source in the instance mod
"Authoria - Reqtificator And Requiem Tweak" (`Source/Reqtificator/Reqtificator/...`).

## Candidate rules

| # | Rule | Class | Layer | Fit support | Held-out (AC) |
|---|---|---|---|---|---|
| 1 | Every touched NPC ends in exactly one state: **templated with SpellList** (rule 2) or **TemplateFlags 0 + `REQ_KW_AlreadyReqtified` AE3B83** (rules 7-8) | house convention (built on Requiem/Reqtificator mechanics) | input | 89/89 NPCs (71 + 18) | **175/175** (173 + 2) |
| 2 | A templated NPC gets **Stats + SpellList** added to its TemplateFlags | Requiem-aligned (fact 4 "Workflow A"; skip code below) | input | end state Stats+SpellList 71/71; strictly "nothing else added" 64/71 (5 add Inventory, 2 Traits) | SpellList added **173/173** (Stats already present on 64); Stats+SpellList end state **166/173**: 7 SpellList-only, 1 also adds DefPackList (see bugs) |
| 3 | **Keep an existing Template**; retarget only where Requiem retargets that template itself (GS guards 0853C4 → 9473F0 REQ_CLChar_Spawns_Guard_Solitude, as Requiem.esp does on 0853C4) | Requiem-aligned | input | 7 pre-templated: 4 kept, 3 retargeted | **159/159** kept; near-trivial, since no AC NPC pointed at a Requiem-retargeted template |
| 4 | An untemplated **creature** gets Template → the Enc* actor of its race (Requiem winner where one exists), flags Stats + SpellList only (no Traits: race and looks stay) | Requiem-aligned | input | 2/2 (EncDog, EncAtronachFrost) | form **14/14**; template right 12/14 (troll, rabbit wrong) |
| 5 | An untemplated **civilian** gets Template → a vanilla named NPC of the same job (Lisette, Beirand, Evette San, Rorlund, Sorex, Arcadia, Styrr, Niluva) | house convention (Requiem has no civilian donor pool) | input | 24 GS NPCs, 22 fit (2 design-question) | not testable: AC civilians were already templated |
| 6 | A **guard** inherits Requiem's per-hold guard list `REQ_CLChar_Spawns_Guard_<Hold>` | Requiem-aligned | input | **7/7 effective**: 5 direct on 9473F0, 2 on vanilla Solitude guards whose chain resolves to 9473F0 (02E1F0 → 0853C4 → 9473F0) | **0/28**: AC guards stay on vanilla LCharGuardSons* (design question) |
| 7 | An `AlreadyReqtified` NPC that is **used** carries the Reqtificator's kit baked: **45 perks + 4 spells** (+ state-trait perks, e.g. ghost 031284) | house convention, ARR-wide | **baked** (proved by code) | 8/8 | **1/1** (zxACDummyGoods01) |
| 8 | An **unused** NPC gets the keyword only, no kit | house convention | input | 10/10 | **1/1** (zxACMarkerHare_Unused) |
| 9 | Removing perks / spells masked by a new SpellList flag is **optional clean-up**: no gameplay effect | noise | input | 6 distinct GS records cleaned; 0 left with masked perks, 1 still with masked ActorEffect | applied on 88 + 7 records, but **25 NPCs keep a masked perk**: partial |
| 10 | Never add a Template link without flags: TemplateFlags 0 makes it inert | outlier bug where broken | input | 2 GS breaches (WSAlfbrand, WSTotWGhost) | 0 breaches: all 14 new AC links carry flags |
| 11 | Do not set Level / skills under a Stats flag: masked, dead data | noise where broken | input | no GS rows | 2 of 3 AC Level rows are dead (Tsun clone, Enchanter) |

Not rules: the 039CFD commoner template (suspected systemic bug, below), and the `NULL_` rename (one-off, §4).

## Per field

### Configuration.TemplateFlags / Template (rules 1-6, 10)
- **TemplateFlags changed, 244 rows** (fit 71 / held-out 173), all input. Top changes: AC `Traits, AttackData` →
  `+Stats, SpellList` (82); GS `0` → `Stats, SpellList` (59); AC `Traits, Stats, AttackData` → `+SpellList` (30); AC
  `+Keywords` variant (29). Flags are patch-carried in practice: U6 7a found 0/3,770 Reqtificator template-flag writes (the code can copy Traits/AttackData flags via ActorVisualAutoMerge).
- **Why SpellList matters (code):** `ActorPerksFromRules.cs:24` and `ActorSpellsFromRules.cs:25` return early when
  TemplateFlags has SpellList; `ActorGlobalPerks.cs:23-24` returns early when Template is set **and** SpellList is set.
  So a templated NPC gets the template's final perks/spells and none of its own. R12: NPC perks/spells 20/20 exact.
- **Template changed, 83 rows** (fit 69 / held-out 14); 80 are from a null link. GS targets: 039CFD (26), Lisette
  013297 (6), Beirand 013261 (6), Evette San 013273 (5), RCWO_ImperialBattlemageRequiem 000804 (3), 9473F0 (5).
  AC adds links only for its 14 animals; every other AC NPC keeps its Traits template (mod pools `zxAC_LChar*`,
  vanilla LChar*, uniques such as Tsun, Ysgramor).
- **Extra flags:** GS adds Inventory on 5 and Traits on 2 records from 0 (they take looks from the template too); AC
  adds DefPackList on 1 (zxACLvlCompanion03, bug below).
- **Template-fit (consensus `template_fit`)**, per-NPC verdicts, 58 sampled by a verifier (48 agree, 8 changed, 4 of
  them material, 2 cannot-tell):

  | | fits | misfit-role | design-question | misfit-tier | misfit-race |
  |---|---|---|---|---|---|
  | GS (73 templated) | 35 | 28 | 9 | 1 | 0 |
  | AC (173) | 73 | 48 | 49 | 2 | 1 |

  - **Requiem-aligned choices:** guards on 9473F0 (copies Requiem.esp 0853C4); creatures on Requiem Enc* actors
    (EncDog, EncBear, EncSabreCat, EncCow, EncMudcrabMedium, EncDwarvenSpider01, EncGoatDomestic, EncFoxArctic).
    A few are vanilla or other-mod winners (EncChicken, EncHare, EncHorseBlack from Witcher Horses,
    EncSkeleton01Melee1H): low stakes.
  - **House convention:** civilians on vanilla named NPCs (rule 5). Requiem does not edit these records, so the stats
    are vanilla autocalc archetypes, not Requiem-tuned. Class pairing is loose (e.g. WSDagmar, Dremora class → Beirand).
  - **Misfits (bug):** **039CFD `REQ_LookTemplate_EncBandit01TemplateMelee`** is Requiem's level-3 sword-and-shield
    **bandit** template (Skyrim.esm EncBandit01TemplateMelee, renamed and stat-edited by Requiem). Requiem.esp uses
    the REQ_LookTemplate_* set only for bandit variants by race and sex, and with Traits only for DLC1 hunter and
    vampire looks. No Requiem civilian takes Stats from it. GS puts 26 NPCs on it: 23 misfit-role (farmers, merchants,
    a chef, nobles, 2 mage-outfit NPCs), 2 design-question, 1 fit. Siblings: banker / merchants on Bandit02/03
    Melee, innkeeper + Ila Visan on the trickster mage 8A8BE3 (unverified: only 1 fit row sits on 8A8BE3), priest Leksii on the hedge wizard 039D31 (Rorlund 0132A2,
    Priest class, exists). AC misfits are pools: civilians on combat-heavy `zxAC_LCharALLMale` (13 misfit),
    `zxAC_LCharTrader` (4/4), `zxAC_LCharBrewer`, `zxAC_LCharSmith` (3/3 combat); casters on melee pools.
  - **Design questions:** mixed pools (half civilian, half combat) where the mod uses them for appearance only.
    With Stats added, every passenger rolls a random pool member's statline. Also: Imperial-held holds on Sons
    guard lists, and the Olivia Vaylon vs Vitar court-mage split (Bandit02 magic vs RCWO battlemage).
- **Inert links (rule 10):** WSAlfbrand → 039CFD and WSTotWGhost → 039D31 have TemplateFlags 0 on the winner. The
  patch added only the link, so the engine inherits nothing. Both also carry the baked kit, which is right for flags 0.
  If inheritance was meant, the flags are missing **and** the kit would then be dead. `ForwardDataFromTemplate.cs`
  does only visual auto-merge by master chain, so the Reqtificator does not act on the link either. Verifier: the
  fit verdicts on both are now "fits (inert)". NULL_WSStormGateGuard keeps a source-inherited inert link to 0853C4
  (not patch-made).

### Keywords + baked Perks / ActorEffect (rules 7-8)
- **Keywords added: 20 rows**, all AE3B83 (fit 18 / held-out 2), input. `ActorAlreadyReqtifiedSkip.cs:11-14` returns
  `Skipped` for an NPC with `Keywords.AlreadyReqtified` = AE3B83:Requiem.esp (`StaticReferences.cs:36-37`). It runs
  first in the NPC chain (`MainLogicExecutor.cs:291`, before global perks, rule perks and rule spells at 295-297).
- **Perks added: 406 rows / 9 records**, all baked and ok. **ActorEffect added: 36 rows / 9 records** (4 each). Kit, from
  code + config: **10 global perks** = formlist 8F57EA, unioned over imports (Magic Redone 8 incl. Sanctuary 410741,
  WAR 000853/0008E9; `ActorGlobalPerks.cs`, `MainLogicExecutor.cs:289`). **35 rule perks** from
  `ActorAssignmentRules_Requiem.esp.conf` `feature_gameMechanics`. **4 spells:** 03E556, 062275, AE3597 (gameMechanics) +
  npcExclusive AD3977 PersistentSpellRescaling (all :Requiem.esp, 9 records each). Live read: 7 GS NPCs + zxACDummyGoods01 have
  45/4; WSTotWGhost (the 8th GS NPC with the kit) has 46/5 (ghost trait perk + source-carried 05030B). All have TemplateFlags 0, so no skip applies.
- **ARR-wide, not Requiem's own:** 3,239 winning NPCs carry AE3B83 across 97 Authoria patches; Requiem.esp's own NPC
  records carry 0. A house convention: the patch does the build's NPC job by hand and locks the build out.
- **Unused NPCs** (10 GS + 1 AC): keyword only, no perks or spells. GS "unused" rests on 0 live placed references
  (used siblings WSAlfbrand and WSNebia have refs).

### Perks / ActorEffect removed (rule 9)
- **Perks removed: 110 rows** (fit 3 / held-out 88); **ActorEffect removed: 64 rows** (fit 6 / held-out 7). Input +
  noise: the verifier corrected 61 patterns from "baked" to input/noise, because the Reqtificator writes no template
  flags and the SpellList mask hides local lists. Top: REQ_NULL_ExtraDamage25 103A90 (33), 0FA2C5 (20).
- Applied unevenly on AC: 25 SpellList-templated AC NPCs still carry 1 masked REQ_NULL_ExtraDamage perk (e.g.
  zxACLvlSilverhand01 103A90; zxACLvlDunmerLeader also keeps DLC2LSpellBanditBoss05FireFrostShock). No effect, but it
  shows the clean-up is not a requirement. Teach it as optional.

### EditorID (§4 thread): `NULL_` rename
- **10 rows / 10 GS NPCs**, e.g. WSPal → NULL_WSPal. Noise. No code reads `NULL_`. The prefix differs from Requiem's
  `REQ_NULL_` (159 NPC_ in Requiem.esp's version). Across the whole live order only these 10 NPCs start with `NULL_`, and AC's
  unused marker was not renamed (it is already `_Unused`). **One author's one-off, not a house convention**; do not
  teach it. The keyword-only treatment (rule 8) is the part that holds.

### Level (rule 11) and noise
- Configuration.Level → NpcLevel on 3 AC records: zxACDummyGoods01 L1 (autocalc, live: ok), zxACLvlTsun L100 and
  zxACLvlMerchantEnchanterFine L1 (both Stats-templated, so masked: dead). The readers rated all 3 ok; the Stats mask
  makes 2 of them noise (matches reference bug AC12).
- Noise: Factions reordered (176 rows), Items (24), HeadParts (3), TintLayers (1) reordered, LevelMult drops,
  defaultGhostScript removed + re-added with its 11 properties reordered (2+2, AC Ysgramor), Compressed-flag bookkeeping.
- Unclear: WSIrlav Ebony Dagger 0139AE → Steel Dagger 013986 (with a Stats,SpellList template, no Inventory flag, so
  the item is live). Plausibly a tier-down to the L10 template; no comparable proves it.

## Suspected bugs

| Record | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|
| 26 GS commoners, e.g. A4EE97 WSTotWPactusAcentis | Template | 039CFD bandit L3 | civilian donor (rule 5) | 23 misfit-role; Requiem uses LookTemplates for bandits only | suspected systemic |
| A1B4A8 WSTempleLeksii | Template | 039D31 hedge wizard | 0132A2 Rorlund (Priest) | Priest L9, temple faction | outlier |
| A4EE9B WSTotWRinosFirehand | Template | Wuunferth L40 Journeyman | a master-tier caster | own L100 TrainerDestructionMaster (verifier) | outlier |
| A69294 WSAlfbrand, A4EEA1 WSTotWGhost | Template | link + flags 0 | no link, or flags set | inert in engine | outlier (half-done edit) |
| AC 000B33 zxACEncAnimalTroll01 | Template | EncTroll 023ABA (L14) | 00D0B9:Dawnguard.esm DLC1EncTrollFrostArmored (L22) | race DLC1TrollFrostRaceArmored | outlier (= AC1) |
| AC 000A92 zxACEncAnimalRabbit | Template | EncHare L1 | design call for an L100 "Murder Rabbit" | misfit-tier | design (= AC3) |
| AC 000A4A zxACLvlCompanion03 | TemplateFlags | +DefPackList; template REQ_NULL_LCharBanditOnlyNordM 037C28 | Stats+SpellList only | the only DefPackList add in 244 rows; the template is Requiem-nulled | outlier (= AC6) |
| AC 0105E2 TelvanniMage; 000A93-95 Psijic; Dawnguard 01-03 | Template | Redoran guards / Vigilant / soldier pools | caster / Dawnguard pools | misfit-role, verifier agrees | systemic in AC (= AC8) |
| AC civilians on combat pools (zxAC_LCharALLMale 13, Trader 4, Smith, Brewer, Khajiit bandit lists) | TemplateFlags +Stats | bandit/warlock statlines | civilian or fixed low level | misfit-role | design / systemic (= AC7) |
| AC zxACLvlTsun, zxACLvlMerchantEnchanterFine | Level | set under Stats | leave | masked | noise (= AC12) |
| AC 7 SpellList-only (000A4B, 000A57, 000A5B, 000A63, 000A80, 000A81, 000B45) | TemplateFlags | no Stats | Stats+SpellList (rule 2) | keep own AutoCalc and fixed Level | unclear: deliberate? (AC14 Pugilist) |

## Held-out check against the AC reference bugs (U3)

The census caught **AC1** (troll, misfit-race), **AC3** (rabbit, misfit-tier), **AC7** (civilians, misfit-role and
design-question), **AC8** (Telvanni, Psijic, Dawnguard misfit-role; Morag Tong only design-question) and **AC11**
(ghost script, noise). **AC6:** the readers saw the REQ_NULL_ template as a design question but rated its DefPackList
flag ok; caught only at synthesis. **AC12:** the masked spell removals are rated noise, but the masked Level edits
were rated ok, so this is a partial catch. **AC10:** the dummy's kit was read as the correct bake (rule 7). It is
ok by code but no-effect clutter on an invulnerable dummy, so the census did not call it a bug. **AC14:** surfaced
(SpellList-only exception, design-question). **Missed:** AC9 (Justiciar, Antiquarians), because those records are
not in the patch and a census of changes cannot see an omission. AC4: the census rated it "fits", like the 6
contra judges. AC2, AC5, AC13 are not NPC_ (out of scope).

## Open calls for Aaron

1. **Rule 7, the baked kit + AlreadyReqtified on untemplated NPCs:** confirm this is the ARR convention (3,239 NPCs).
   Why bake instead of letting the build stamp the same 45 + 4? Lock-out against later rule changes?
2. **The 039CFD commoner template:** a deliberate "weak generic NPC" statline, or a bug? 26 GS NPCs ride it.
3. **Rule 5:** are vanilla named NPCs (vanilla autocalc) the intended civilian donors, given Requiem tunes none of them?
4. **Mod pools used for appearance (AC):** add Stats to a mixed pool (as done), or keep civilians on fixed own stats?
5. **Guards:** should mod guards move to `REQ_CLChar_Spawns_Guard_<Hold>` (GS did; AC did not)?
6. **The 7 SpellList-only AC NPCs** (commanders, merchants, monk, pugilist, bard): a deliberate keep-own-stats call?
7. **WSAlfbrand / WSTotWGhost:** were they meant to inherit (then flags + no kit), or should the links go?
8. **`NULL_` rename:** confirm it is a one-off and not to be taught.

## Pilot caveats

- Two patches, both the "Workflow A" style; no patch here leaves NPCs untemplated without the keyword, so rule 1's
  alternative paths (a templated NPC without SpellList, Reqtificator-stamped NPCs) are untested.
- Rules 5 and 6 have no held-out test (AC civilians were pre-templated; AC guards follow the opposite choice).
- Fit verdicts are judged per template group from name, outfit, factions and class; 188 of 246 are unsampled by the
  verifier. 4 of 58 sampled verdicts changed materially, so expect a few percent error.
- Omissions (records the patch should have touched but did not) are invisible to this census.
- **Template fit must be judged on the resolved template chain, not the first link:** a vanilla link can resolve
  to a Requiem record (the first draft wrongly flagged WSStormGateGuard01, whose chain ends at 9473F0).
- Live reads exclude `ksws03_quest.esp` (load failure): unused-NPC calls (0 references) cannot rule out a script spawn.
- The baked kit is proven by code + config + live counts; RFI is unticked, so there was no direct diff against a build output.
