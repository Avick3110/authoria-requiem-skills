# GEAR-NPC-S2: armour, weapons, recipes, ArmorAddon and NPCs in the stage-2 patches

*Author: Aaron (+ Claude session) · 2026-10-30*

**Addendum to stage 1** (`stage1/out/NPC_.md`, `ARMO.md`, `WEAP.md`, `COBJ.md`). It says where the stage-2 patches
confirm, extend or contradict those conventions; it does not restate them.

**Corpus:** ARMO 3,066 rows / 786 records / 8 plugins; WEAP 1,278 / 481 / 3; COBJ 877 / 337 / 5; NPC_ 23,294 /
1,052 / 5; ARMA 137 / 53 / 15. The ARMO / WEAP / COBJ / NPC_ rows come from the 14 stage-2 patches (only 11 of them
touch these types). ARMA is a type that stage 1 didn't extract, so its rows come from **stage-1** plugins (Unslaad,
LOTD, Sigrin…). **Held-out: 0 rows.** None of the 6 eval references is a stage-2 patch, so this doc has no held-out
validation; "held-out" below points at the stage-1 result where there is one. **One plugin dominates:** Authoria -
Master Patch - Bruma.esp holds 81 % ARMO, 99 % WEAP, 88 % COBJ and 99 % NPC_ rows. Every convention gives its
spread outside Bruma. Live reads: 6 (houseCARL, epoch `e2-58972ca8618e79ea`), plus subject-file scans of the 11
plugins.

## 1. Conventions

S1 = the stage-1 convention it maps to. Rel. = relation to stage 1: **C** confirms, **E** extends, **X** contradicts.

| # | Convention | Class | Layer | Rows / plugins | Spread outside Bruma | Rel. to S1 | Conf. |
|---|---|---|---|---|---|---|---|
| 1 | **No NPC carries AlreadyReqtified `AE3B83`**: 0 / 1,052 (Bruma 995, Moonpath 50, IDE 3, Dragon Tweaks 3, A New Debt 1). Every stage-2 NPC is left to the build | Requiem-aligned (FACTS §D upstream stance; build dedupes, RFTI 001D77 9 → 54 perks) | input | 23,294 / 5 | holds in all 5 | **X** NPC_ #1 (59 % marked in stage 1); confirms FACTS §D at scale | high |
| 2 | On non-SpellList NPCs, hand-carry only the **Requiem tier skill kit** (REQ_OneHanded / Block / HeavyArmor…, scaled to level). Rule perks are left to the build | Requiem-aligned (verified *fixed*; sets disjoint from the build's) | input | Perks added 3,051 (936 SpellList-masked); removed 497 / 2 | Moonpath 43 perk rows on 11 NPCs; rest 0 | **X** NPC_ #2 (no rule-kit bake here); E #3–4 (swap source perks for the tier kit, e.g. 06DA75 → exactly 039CFD's kit) | med |
| 3 | PcLevelMult → fixed NpcLevel; **AutoCalc kept** on most (AutoCalc removed in 236 Flags rows; 755 / 995 Bruma NPCs keep it; 30 Bruma NPCs still carry LevelMult) | Requiem-aligned (live: of 2,702 Requiem-touched NPCs, 2,636 fixed and 1,256 AutoCalc + fixed; strict Requiem.esp winners 1,968 / 1,953 / 935) | input | Level 181 + 419; Flags 434 / 2 | Moonpath only | C NPC_ #5; **X** #6 ("drop AutoCalc" isn't required) | high |
| 4 | Skill block: 5 floor, tier values on combat skills (EncOgre01 6026C0 15 / 78 / 79 → 5 / 72 / 73) | Requiem-aligned (verified: 016EF0 shape) | input | 8,712 / 2; **only 2,439 effective**, the rest are under AutoCalc / Stats mask | Moonpath | C NPC_ #8; E: ~72 % inert | med |
| 5 | Health / Magicka / Stamina and offsets hand-set | Requiem-aligned (unsampled, except MagickaOffset agree) | input | 441 / 433 / 351; offsets 333 / 243 / 160; effective 94 / 93 / 49 | Moonpath | C NPC_ #7; E: mostly inert | low |
| 6 | Class → `REQ_Class_*` (85BCE3) or a role-matched vanilla combat class; CombatStyle → Requiem / vanilla human styles by role (03BE1B, 91AA67) | Requiem-aligned (verified *agree*; mixed by role) | input | Class 184; CombatStyle 163 / 1 | none | **E** (stage 1 had Class 26 / 4, not re-derived) | med |
| 7 | ActorEffect: one `REQ_Trait_Tempering_*` per armoured fighter, REQ spell kits for mages, `REQ_Trait_Vampire_NPC`. The build adds its 4 controllers on top (RFTI 1 → 5) | Requiem-aligned (unsampled) | input | added 294, removed 106, changed 88 / 1 | none | C NPC_ #3 (trait part); X: no hand `RFTI_All_*` | low |
| 8 | **Workflow A** (null Template → Stats + SpellList): creature → a Requiem-winning vanilla creature by species (sabre cat → 023AB5, guar → 02403B Bristleback, Therium → 023AB4 mammoth, Imga → 023ABA troll); townsfolk → a named vanilla civilian (Ri'saad 01B1DB, Zaynabi 01B1D0); Thalmor → EncThalmor | Requiem-aligned in shape (live read: all targets Requiem / NPCs-Merge winners) | input | Template 30 (Moonpath 29, IDE 1); TemplateFlags 51 (Moonpath 41, Bruma 6, IDE 3, A New Debt 1) / 4 | null → Template is **Moonpath only**; IDE / A New Debt add SpellList to existing templates | C NPC_ #10 (incl. the named-civilian variant, call 6); one misfit (B1) | med |
| 9 | Drop SpellList (or Stats + SpellList) from a source template so the NPC's own kit applies (werewolf 08B5EF, Bloodscale 061731); the build then stamps its rule kit on them | Requiem-aligned / deliberate (verified) | input | 5 rows / 1 | none | C NPC_ #11 | med |
| 10 | CalcMin / CalcMax reset to 0 with the fixed level | noise (inert; Requiem 039CFD 0/0) | input | 135 / 132 | none | C NPC_ #9 | med |
| 11 | **Weapons upstream:** base weapons written at **source scale**: Damage = the Requiem comparable's raw value (122 rule rows), class reach left at 1.3 / 1.0 / 0.7, no skip keywords. The build applies ×6 and ×0.7 | Requiem-aligned input (FACTS §D) | input | 164 damage rows total (122 rule + 42 family; 28 on base weapons without skip keywords) / 1 | **Bruma only** | **X** WEAP #1–4 (stage 1 baked everywhere it patched) | high |
| 12 | Enchanted variants (Template set) carry the **base's raw damage**: all 322 checkable variants equal it (322 of 337; the other 15 have templates outside the patch, unchecked). The build skips Template weapons | Requiem-aligned (UNTOUCHED benchmark: 089716 dmg 7 = base) | input | 126 damage rows on variants | Bruma only | **E** (WEAP had no variant rule) | high |
| 13 | **Bake kept for special cases:** 12 bows (AD3B2D + AD3B2F, hand damage 8 → 25, 14 → 80, 15 → 30; speed 0.5 / 0.4), 6 DBM `*_REPLICA` (full triple, damage 1, reach 0.7 = display dummies), LunarGuard_Battleaxe (20 → 120, 1.3 → 0.91, triple: textbook) | baked (code: `StaticReferences.cs`) | baked | 19 records / 2 | Lunar Guard 1 | C WEAP #1 as a minority case | high |
| 14 | Hand Critical.Damage (inconsistent: 05BC3A dmg 25 / crit 12) | noise (build sets crit := damage) | baked | 155 / 2 | — | C WEAP #3 (redundant) | high |
| 15 | Melee Speed to Requiem class values (0.75 ×58) | Requiem-aligned | input | 70 of 82 / 1 | none | C WEAP #8 | med |
| 16 | Value / Weight re-tiered to the comparable of the item's new tier (BSKHatchet → Elven: Value 165, Weight 10 = 01399B) | Requiem-aligned (verified; the reader's "outlier" overturned) | input | Value rule 70 + 63; Weight rule 35 + 44 | none | C WEAP #6–7 | med |
| 17 | Staff charge ladder 500 / 1,000 / 1,500 / 2,000 / 3,000 | house? (1,500 is off the MR ladder; fact 30) | input | 119 / 1 | none | **X** WEAP #16 (3000 normalisation) | low |
| 18 | Requiem bow tier keywords (9F9914 / 9F9915, REQ_BowBreakable 2E232E); MR `Nox_KW_Staff_*` on staves; material keyword swaps (Iron → Steel) | Requiem-aligned (live) | input | ~40 + 7 / 2 | none | C WEAP #9, #11 | med |
| 19 | NPCsUseAmmo set by hand on 12 bows | redundant (the build adds it to playable bows) | baked | 12 / 1 | — | C WEAP #14 | high |
| 20 | Armour AR = Requiem comparable; real pieces tiered (Siligonder 200 / 250; Bruma 150 / 180 / 275) | Requiem-aligned | input | 485 rule + 122 family / 4 | Moonpath, Lunar Guard, Dwemer | C ARMO C1 | high |
| 21 | **Enchanted variants AR = base AR** (361 / 383 TemplateArmor pairs) | Requiem-aligned (10DFBE AR = template 012EB6) | input | 383 / 1 | Bruma only | **E** (new). Exceptions: B4 | high |
| 22 | **AD3B2B on every base light / heavy piece, never on a TemplateArmor variant** (Bruma 131 / 131 base, 0 / 406 variants; also Moonpath, Dwemer, Lions Mane wigs) | house (Requiem.esp 0 ARMO); load-bearing only on AR-0 pieces | input | 246 / 5 | Lunar Guard omits it on 4 base heavy pieces (AR 150–500, harmless); Wet and Cold puts it on 75 AR-0 **clothing** (inert) | **E** ARMO C3: gated by variant, not blanket | high |
| 23 | Formula AR (×3.3 / ×6.6 + 18 / 66) written by hand (Moonpath 26 → 151.8) | baked, redundant | baked | ~26 / 2 | Moonpath | C FACTS §D | med |
| 24 | AR → 0 on wigs, earrings, scarves, glasses (Lions Mane 10 → 0 ×30; Blanket Scarf 1 → 0 ×21) | house | input | 60 + rule / 4 | most of it is outside Bruma | C ARMO C2. Gaps: B6 | high |
| 25 | Wigs: two shapes in one patch. 10 → Clothing + ArmorClothing / VendorItemClothing; 20 stay LightArmor AR 0 + AD3B2B + `AE35A8` | house | input | 30 / 1 | Lions Mane only | C ARMO C6, C9 | med |
| 26 | Source vanilla-set keyword → the **specific Requiem set keyword** (REQ_ArmorSet_Hide → Fur AD3ADE ×105; Steel → Chainmail ADDD5E ×84; Bonemold 024101) | Requiem-aligned (verified *fixed*, material: every record gains a replacement; ArmorKeywordAssignments config) | input | 284 removed / 3 | Bruma dominant | **E** ARMO C7 / C12 | med |
| 27 | `0C27BD` MagicDisallowEnchanting on extra-slot accessories | Requiem-aligned (fact 26) | input | 84 added; 135 carry it / 4 | Glasses 24, Scarf 21, Wet and Cold 82 | C ARMO C8 | high |
| 28 | Survival warmth keywords (Survival_ArmorWarm 002ED9, CC0E1B, CC0E28 `Update.esm`; Frostfall) | different-convention (survival-mod input) | input | 169 / 3 | Blanket Scarf 21 | E ARMO call 5 (now 3 plugins) | med |
| 29 | Build-stamped armour keywords and `keywords_assign` keywords written in | baked (RFTI / config) | baked | 184 + 68 / 5 | yes | C ARMO C10–11 | high |
| 30 | Temper: drop the EPTempering OR + 05218E pair, one material HasPerk, one ingot (Ebony 05AD9D with 0CB412) | Requiem-aligned (verified) | input | removed 212, added 158 / 3 | Lunar Guard, Moonpath | C COBJ #1–3. Exception: B5 | high |
| 31 | The Daedric temper **copies Requiem's night gate** (`GetCurrentTime() > 23` AND 0CB413, BSKTemper74wDaedricNodachi 601A61) | Requiem-aligned (fact 31) | input | 2 / 1 | none | **X** COBJ lead L3 (Rot Below omits it) | med |
| 32 | **Retire = AD3B01 only.** 207 rule rows / 3 (Bruma 139, Lions Mane 60, Dwemer 8). No `GetLevel()==0` in stage 2. Bruma also renames most retired recipes `AUTH_NULL_*` (129 / 139), and 115 / 139 target an item that is itself NULL | Requiem-aligned (idiom) | input | 207 / 3 | Lions Mane, Dwemer | C COBJ #6; leans to call A (AD3B01) | high |
| 33 | Disabled recipes keep dead cruft (HasPerk 05218E, ×2 counts on AUTH_NULL jewellery) | noise | input | ~30 / 1 | — | — | high |
| 34 | ARMA (stage-1 plugins): cloak / cape addons moved to slot 46 (65536); hats gain Hair; the Rogue Stalker ring / band swap | unclear (cannot-tell) | input | 44 / 13 | Unslaad, Glenmoril, Sigrin | C ARMO's FirstPersonFlags family on the ARMA side | low |
| 35 | ARMA WeightSlider / WeaponAdjust / list order / presentation | noise | — | 93 / 12 | — | — | high |

**Validation.** No held-out rows. Against stage 1, 25 of the 35 conventions confirm or extend. The contradictions
come down to one theme, the **upstream stance**: NPCs without AE3B83 (#1–2, #7) and weapons at source scale (#11).
Stage 1 read both as ARR-wide; in fact they are per-patch choices, and Bruma (a master patch) takes the upstream path.
Smaller contradictions: AutoCalc kept (#3), the charge ladder (#17), the night gate copied (#31).

## 2. Upstream checklist (additions to stage 1)

1. **Pick the stance per patch, not per type.** Upstream: no AE3B83 on NPCs, weapons at source scale, no skip triple.
   Downstream: AE3B83 + full kit, baked weapons + triple. Both are ARR practice (§6 call 1). Upstream is the smaller
   write and matches how Requiem's own NPCs and weapons are processed.
2. **Upstream NPC:** fixed NpcLevel (AutoCalc may stay); Requiem tier skill perks only (no `RFTI_All_*`, no rule
   perks); `REQ_Trait_*` ActorEffects; `REQ_Class_*` / role CombatStyle; skills / stats only where not masked by
   AutoCalc / Stats (otherwise they're inert).
3. **Templating (Workflow A):** a creature takes the Requiem-winning vanilla encounter NPC of its species; a
   townsperson takes a named vanilla civilian; a fighter takes the same faction's Enc template. **Never a
   `REQ_LookTemplate_*` on a civilian** (B1).
4. **Upstream weapon:** Damage = the comparable's raw damage, class reach unscaled, Speed = class, Value / Weight =
   comparable (re-tier both together). Enchanted variants mirror the base's raw damage. No triple.
5. **Armour:** base pieces get the comparable AR + `AD3B2B`; enchanted variants copy the base AR exactly and carry no
   `AD3B2B` (TemplateArmor already skips the build). When you change a base AR, re-sync its variants (B4). Swap a
   vanilla-set keyword to the specific `REQ_ArmorSet_*`.
6. **Recipes:** retire with AD3B01 (optionally rename `AUTH_NULL_*`); for Daedric, copy the night gate; never retire
   the temper of an obtainable armour (B2).

## 3. Downstream bake package (stage 2)

- Weapons: only 19 records. Bows: hand damage + AD3B2D + AD3B2F (no AD3B2E needed, since reach scaling is melee-only).
  Replicas: triple + damage 1 (keeps display dummies at 1). LunarGuard_Battleaxe: the full stage-1 bake.
- Armour: build-stamped / `keywords_assign` keywords (#29), redundant; formula AR on ~26 rows (#23), redundant.
- Crit and NPCsUseAmmo (#14, #19): redundant, since the build overwrites / adds them anyway.
- NPCs: **none** (no AE3B83 anywhere).
- COBJ: none (fact 32–33).

## 4. Per-type detail

**NPC_.** Template status of the 1,052 records: SpellList-templated 560 (the build stamps no perks / spells there,
the template supplies them), templated without SpellList 115, untemplated 377. On the last two groups the build adds
its rule kit to the hand tier kit. FACTS §D measured this complementarity on RFTI 001D77 (9 → 54) and 001973 (30 → 75,
no duplicates). Masked writes are heavy: 4,159 rule rows under a template mask, 936 perk rows on SpellList NPCs, and
~6,300 skill rows on AutoCalc / Stats NPCs. They're harmless but wasted. **Correction to the consensus:** variant 334
calls Moonpath.esp "a source mod". The 30 Template / 51 TemplateFlags rows are `Authoria - Reqtificated -
Moonpath.esp`'s own delta against moonpath.esp / JKJ - Moonpath.esp, so they are ARR's Workflow A. Haunted-mine
ghosts gain ActorTypeGhost / Undead / ImmuneParalysis (64 rows, unsampled; call 5). IDE Steinolf 000035 is a Vigilant
moved to `REQ_Vigilant_Loc_Ruunvald_Charmed` 0135BF: same faction, but a quest-specific "Charmed" variant (lead).

**WEAP.** Bruma base weapons: 148 (37 with a damage row); variants: 337. Damage rows: 164 = 117 on variants without
skip keywords + 19 skip-keyworded (9 of them variant bows) + 28 base upstream. The base retunes are small (goblin sword
8 → 7, iron greatsword 15 → 16). These are source-scale analogues, not ×6. Two base outliers: 0C8CC0 Nord Hero
Antique 25 → 11, and the staff 6028DB 0 → 1. Critical.PercentMult 0 → 1 on 25 weapons is unclear (it plausibly
restores crit chance). Lunar Guard is the only stage-2 plugin with stage-1's full bake.

**ARMO.** By plugin: Bruma = full Requiem pass (AR / Weight / Value comparables, set keywords, AD3B2B on bases).
Moonpath = Siligonder chitin tiered + AD3B2B, Value retuned off comparable (call 4). Lions Mane / Blanket Scarf /
Glasses = cosmetics zeroed + 0C27BD. Wet and Cold = keywords only (AD3B2B + 0C27BD + Requiem keyword AD3AEC on AR-0
clothing; the AR itself is untouched). Dwemer = 4 heavy pieces, AR + AD3B2B. Lunar Guard = AR / Value / Weight from
KAR's values to Requiem tier, no AD3B2B. Weight / Value families (43 / 38 rows) are Bruma / Moonpath retunes, not on the
comparable: cannot-tell.

**COBJ.** Bruma 262 records: 139 retired (AD3B01), 94 live (forge 29, sharpening wheel 45, armour table 48, 1 at
0A5CB3). Of the 24 retirements that target a non-NULL item: 4 Bruma metal-ingot smelts (Bronze 601C51, Copper 601C5A,
Brass 601C89, Meteoric Iron 601C91), 5 food recipes, 5 hide → leather 0DB5D2, 2 silver rings (verified uncraftable),
5 Ayleid tempers (null CreatedObject), the Goblin Shield temper 60263E, and 1 Daedric Nodachi with a live replacement.
`GetGlobalValue(3BA1F3:Requiem.esp) == 0` was added on 4 leather recipes; the global was read but its meaning was not
settled. Lunar Guard tempers: Ebony ingot + 0CB412, but the KAR mod perk is kept (B5).

**ARMA.** 137 rows on stage-1 plugins (ARMA was not extracted in stage 1). Gameplay-neutral except slot bits: 16
cloak addons moved to slot 46 so they stop blocking amulets. That matches ARMO-side slot moves.

## 5. Suspected bugs (for Aaron's review; not filed)

| # | Record (FormID, plugin) | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| B1 | anvil_Aleyt `01EE1B:moonpath.esp` (Anvil_citizens, class EncClassDremoraMelee), Authoria - Reqtificated - Moonpath.esp | Template + TemplateFlags | `09A54D` REQ_LookTemplate_EncBandit03MissileKhajiitM, Stats + SpellList | a civilian template (as Ri'saad / Zaynabi in the same patch) | live read this session; same shape as Grand Solitude 039CFD (stage-1 NPC_ B) | outlier bug |
| B2 | 1Dwemer tempers `000805`–`000808:1Dwemer.esl` (+ crafts `000A02`–`000A05`), Authoria - Reqtificated - Dwemer Armor.esp | WorkbenchKeyword | AD3B01 on all 8 | keep the temper live (0ADB78 + Dwarven perk 0CB40E) | live: no other COBJ / LVLI / container / outfit makes 000800–803. The armour exists only as placed refs (22 PlacedObjects in 1Dwemer.esl), so found armour can't be tempered | suspected bug (craft disable may be deliberate: find-only) |
| B3 | AUTH_NULL_BSKRecipeJewelryRingSilverSapphire / Emerald `602920` / `602921:BSAssets.esm`, Bruma | WorkbenchKeyword | AD3B01 | a live recipe, or the rings retired | verifier: live items 60291F / 60291E, no other COBJ | outlier bug (disable without replacement) |
| B4 | 21 CYREnchArmorIronCuirass* variants vs base CYRArmorIronHeavyCuirass `0D51D1:BSHeartland.esm` (+ CYRArmorFurHelmet `300014` variant 60 v base 50), Bruma | ArmorRating | variants 250, base 275 | variant = base (#21) | subject scan; Requiem REQ_Heavy_Iron_Body 012E49 = 250 (live), so the base was pitched 10 % above | outlier (sync slip). Which value is meant? |
| B5 | GCON_Temper_LunarGuard_* (4) and Temper_LunarGuard_Battleaxe, Authoria - Reqtificated - Lunar Guard Armor (Standalone).esp | Conditions | HasPerk `00088C:Kad_MoonMonkRobes.esp` **AND** HasPerk 0CB412 / 0CB40D | one Requiem material perk (#30) | ledger: the source gate was re-added. Double gate; is the mod perk obtainable in ARR? | lead |
| B6 | Moonpath clothing-typed with AR > 0: AnvilGuar `047C94` 77, SloadSkin `055276` 127, SkinTherium `06369A` 82; Alfiq clothes `000804` 5, hoods `000805` / `00080C` 1 (JKJ - Moonpath.esp) | ArmorRating | as listed | 0 on wearable clothing (ARMO C2) | subject scan. Skins are probably NonPlayable creature skins (legit). The Alfiq pieces are a gap | lead |
| B7 | Bows CYRAncientImperialBow `05BC3A`, …Supple `05BC44:BSHeartland.esm`, CYRAyleidBow `300064`, Bruma | Data.Speed / Damage | 0.5 / 0.4; dmg 25 / 80 / 30 behind AD3B2F / AD3B2D | build values 0.3704 / 0.4445; dmg ×4 of a comparable | verifier family (call 3); not a ×4 of the source | lead |
| B8 | Bruma hide → leather recipes (CYRRecipeLeatherDeerHide, …WolfHide, …MountainLionPelt + 2 AUTH_NULL) | WorkbenchKeyword | AD3B01 | Requiem's leather path | not compared with Requiem's own tanning recipes | lead |

## 6. Open calls for Aaron

1. **Stance per patch.** Bruma is upstream (no AE3B83, weapons at source scale). Most stage-1 patches are downstream.
   Which should the rebuilt skill teach as the default? Is "master patch → upstream, Reqtificated → downstream" the
   rule, or patch age?
2. Keeping AutoCalc on fixed-level NPCs (Requiem-native) v dropping it (stage-1 habit): either acceptable?
3. Bow speed 0.5 / 0.4 and hand damage on Bruma bows (B7): deliberate, or should they match the build's values?
4. Moonpath Siligonder Value 300 → 1,000 (comparable 2,500 / 400) and Bruma Weight retunes: tier choices or drift?
5. Haunted-mine ghost keywords and the bandit class on ghosts: intended?
6. Staff charge ladder with 1,500 / 1,750 / 2,500 steps (#17) v stage 1's 3,000 normalisation.
7. Retiring whole cosmetic lines (Lions Mane 60 recipes; the items survive only in the Alternate Perspective start
   wardrobe): deliberate? Retiring Bruma's metal-ingot smelts: are these metals meant to come only from loot?
8. B1–B8 confirmations.

## 7. Limits

- **No held-out validation:** the 6 references contain no stage-2 patch.
- **Bruma dominance:** conventions #6, #7, #12, #17, #21, #26 and #31 are effectively one plugin.
- **Unsampled families (leads):** NPC Health / Magicka / Stamina, Level.Level, Health / Stamina offsets,
  ActorEffect added / changed, Keywords added / changed, SkillOffsets, Template, Items, Attacks, SpeedMultiplier; WEAP
  ObjectEffect, Stagger; COBJ CreatedObjectCount / CreatedObject; ARMA AdditionalRaces. Cannot-tell: ARMO Weight /
  Value / ObjectEffect / TemplateArmor / FirstPersonFlags, COBJ Items removed, all of ARMA FirstPersonFlags, WEAP
  Weight / PercentMult.
- **Untouched records:** see `stage2/out/UNTOUCHED.md`. BSHeartland NPCs untouched 1,154 (1,022 template-masked,
  125 AutoCalc citizens left to the build: consistent with #1); BSHeartland Weapon 46 base weapons untouched at vanilla
  damage (the build rescales them, consistent with #11); BSHeartland Armor 1 playable enchanted boots `0D3022` at AR 12
  (likely miss, same class as B4).
- **Inference:** the claim that the engine reads a template-linked variant's own AR / damage was not tested in game.
  #12 / #21 rest on Requiem's equal-value convention and on the build's Template skips.
- `ksws03_quest.esp` failed to load in every live read; the 1 ARMA row from the ksws03 patch and any reachability
  check (B2, B3) can't rule out a recipe there.
- Per-NPC kit contents (which perks per role) aren't resolved, so a role → kit table is not derivable here.
