# Suspected bugs in ARR's handmade Requiem patches

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: LIVING. Collected from the census and split hunts; candidates only — each must be re-verified live before it is filed in arr-release BUGS.md (a report is a lead, not a fact).*

## Summary

**95 candidates: 15 CONFIRMED, 80 PLAUSIBLE.** Plus 3 errors in our own docs (last section).

| Domain | CONFIRMED | PLAUSIBLE | Total |
|---|---|---|---|
| NPC | 0 | 16 | 16 |
| Weapons | 3 | 9 | 12 |
| Armour | 3 | 8 | 11 |
| Ammo | 1 | 9 | 10 |
| Magic (spells, perks) | 4 | 8 | 12 |
| Race | 0 | 7 | 7 |
| Containers / locks | 1 | 4 | 5 |
| Consumables | 1 | 2 | 3 |
| Recipes | 2 | 10 | 12 |
| Other (lists, outfits, cells, explosions) | 0 | 7 | 7 |

- **CONFIRMED** means the record was read live and the fault needs no intent to judge: a typo, wrong arithmetic,
  a wrong field, a value the engine ignores. It is still a candidate until re-read at the current load order.
- **PLAUSIBLE** means the record is off-pattern, but intent could explain it. Many of these are also asked in
  [the questions doc](questions-for-list-authors.md); an answer there may clear them.
- "Reqtificated - X" is short for `Authoria - Reqtificated - X.esp`. FormIDs are `XXXXXX:Plugin` where the source
  doc gave the master.
- Not counted: rows the source docs called noise (inert data, duplicate keywords, test races), design calls, and
  findings later cleared (listed at the end).

## NPC

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| N1 | Reqtificated - Grand Solitude | 26 civilians / guards on `039CFD` REQ_LookTemplate_EncBandit01TemplateMelee, e.g. WSEnbjorf A68798, WSTotWPactusAcentis A4EE97 | Civilians templated onto a level-3 sword-and-shield bandit, Stats + SpellList | ledger exactly 26; 8 verifier-confirmed; same patch puts other civilians on Lisette / Beirand / EvetteSan. Asked as Q25 | PLAUSIBLE | [pilot NPC_](census/pilot/NPC_.md), [stage1 NPC_](census/stage1/NPC_.md), [split 7](splits/07-npc-d1-templates.md) |
| N2 | Reqtificated - Grand Solitude | WSTempleLeksii A1B4A8 | Temple priest templated on hedge wizard `039D31` | Priest L9, temple faction; Rorlund `0132A2` fits | PLAUSIBLE | [pilot NPC_](census/pilot/NPC_.md) |
| N3 | Reqtificated - Grand Solitude | WSTotWRinosFirehand A4EE9B | L100 master destruction trainer templated on Wuunferth (L40 Journeyman) | own class TrainerDestructionMaster (verifier) | PLAUSIBLE | [pilot NPC_](census/pilot/NPC_.md) |
| N4 | Reqtificated - Grand Solitude | WSAlfbrand A69294, WSTotWGhost A4EEA1 | Template link added (`039CFD` / `039D31`) with TemplateFlags 0: inherits nothing. Also got the marker and a 45 / 46-perk kit | live read; a half-done template edit. Harmless in game. Asked as Q24 | PLAUSIBLE | [pilot NPC_](census/pilot/NPC_.md), [split 7](splits/07-npc-d1-templates.md) |
| N5 | Reqtificated - Moonpath | anvil_Aleyt `01EE1B:moonpath.esp` | Anvil citizen templated on `09A54D` REQ_LookTemplate_EncBandit03MissileKhajiitM | same patch puts Ri'saad / Zaynabi on civilian templates; same shape as N1 | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |
| N6 | Reqtificated - Tools of Kagrenac | Ayleid skeletons, 20 flag edits (16 judged, tiers 03–06), e.g. 461299 (L51), 46129A (L63) | Stats flag added, so L51–63 skeletons now take an L13 template's stats (`07F393` Ayleid Archer, `07A28B` Ayleid Warrior) | live read 461299; 2 of 16 verifier-sampled | PLAUSIBLE | [stage1 NPC_](census/stage1/NPC_.md) |
| N7 | Reqtificated - Dac0da | zDcdLvBlackworm2H 0043CB–0043CD, zDcdLvBlackwormDual_WS2 0056F1–0056F3 | Melee leveled actors repointed to `01E777` LCharWarlockNecromancer | verifier agreed on 0043CD / 0056F3 | PLAUSIBLE | [stage1 NPC_](census/stage1/NPC_.md) |
| N8 | Reqtificated - Animated Ships | zxShRiftenCourierHonnigbrew 000878, Windhelm Sailer 000891, Solitude Courier 000A13 | SpellList added onto `LCharBanditMissile` / `zxSHLCharBanditM`: couriers get bandit spells | verifier agrees. The Stats misfit is the source's; ARR extended it | PLAUSIBLE | [stage1 NPC_](census/stage1/NPC_.md) |
| N9 | Reqtificated - Animated Carriages (held out) | Farmer F 000B32 / 000B31; Psijic 000A93–95 (Vigilant list); Dawnguard 000B65 (soldier list); TelvanniMage 0105E2 (Redoran guards); civilians on combat pools (zxAC_LCharALLMale 13, Trader 4, Smith, Brewer); troll 000B33 on EncTroll L14 for a frost-armoured troll race | Role / tier misfit templates | verifier agree / disagree → misfit-role. A reference patch: eval scoring must not reward copying it | PLAUSIBLE | [pilot NPC_](census/pilot/NPC_.md), [stage1 NPC_](census/stage1/NPC_.md) |
| N10 | Reqtificated - Animated Carriages | zxACLvlCompanion03 000A4A | DefPackList added (only one in 244 rows), and the template is Requiem-nulled `037C28` REQ_NULL_LCharBanditOnlyNordM | ledger | PLAUSIBLE | [pilot NPC_](census/pilot/NPC_.md) |
| N11 | Reqtificated - Animated Carriages | zxACDummyGoods01 "Goods" | Marker and a 45-perk kit on a non-combat dummy actor (twin zxACMarkerHare_Unused got the marker only) | harmless; low | PLAUSIBLE | [split 7](splits/07-npc-d1-templates.md) |
| N12 | Reqtificated - Glenmoril (+ others) | 21 NPCs, 14 of them Glenmoril Solstheim bosses (e.g. zzzRevBossSEEldenKing); TGV Kynesgrove | Hand kit (47–56 perks) but no `AlreadyReqtified` | harmless: the build adds nothing they carry. Asked as Q2 | PLAUSIBLE | [split 1](splits/01-npc-workflow.md) |
| N13 | Reqtificated - Glenmoril | zzzRevSq03SacrificeEcho01–03 3B1B54–56 | Template race misfit | LLM verdict only, not verifier-sampled | PLAUSIBLE | [stage1 NPC_](census/stage1/NPC_.md) |
| N14 | Authoria - Master Patch - Bruma | 44 named combat-class NPCs, e.g. CYRArilusQuenti `0650F1:BSHeartland.esm` (Legate), CYRMarciusCarvain `0700D5` (source L20), CYRBrumaMS11Olaf `0C9BDB`, CYRBotramtheHammer (L13 → 4) | Belethor's civilian block (fixed L4, civilian skills) on fighters | Requiem's own PcLevelMult → fixed conversions of combat-class NPCs: median 35, 3 / 98 at ≤ 5. Block picked by "named", not class | PLAUSIBLE | [family check](splits/14-npc-stage2-family-verification.md) |
| N15 | Authoria - Master Patch - Bruma | 35 BSK goblin templates, `BSAssets.esm` (BSKEncGoblin00–06 Melee / Missile / Berserker / BossMelee, e.g. 602608, 60265F) | Every tier fixed L6, AutoCalc (source L1–51); BossMagic tiers all L30 | skills cloned from Requiem rieklings 01–04 (L18 / 21 / 24 / 27) but not their level; under AutoCalc every tier fights as L6 | PLAUSIBLE | [family check](splits/14-npc-stage2-family-verification.md) |
| N16 | Authoria - Master Patch - Bruma | CYREncVampire00–05Template `0792C4`… `0792E7:BSHeartland.esm`; CYREncBloodscale01TemplateMelee `06173A` (+ Melee1HArgonianBerserk) | Level copied from a donor's inert, Stats-masked own row: vampires L20–40 from `REQ_LookTemplate_Vampire0X*` (Requiem actually uses L45–49); bloodscale L7 from EncForsworn01TemplateBerserk (masked onto L25) | live reads of the donors' template flags | PLAUSIBLE | [family check](splits/14-npc-stage2-family-verification.md) |

## Weapons

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| W1 | Reqtificated - Vigilant | zzzAoMVigilantGreatsword `11CCBF:Vigilant.esm` | Speed 9.75 (should be 0.75, the greatsword class) | live delta v source 0.7; only playable weapon in the order above 5 | CONFIRMED | [stage1 WEAP](census/stage1/WEAP.md) |
| W2 | Reqtificated - LOTD | REQ_Ench_Weapon_AncientNordHoned_Greatsword_Fire3 `0F494C:Skyrim.esm` | Template `05BF13` cleared (LOTD's version forwarded over Requiem's). Without the Template and the No*Rescale triple, the build rescales damage 20 → 120 | live tree | CONFIRMED | [stage1 WEAP](census/stage1/WEAP.md) |
| W3 | Ultra Enemy Good Death patch | cleavers `00080E`, `000823`, `00081B:Ultra Enemy Good Death.esp` | Reach 0.6125 = 1.25 × 0.49: × 0.7 applied twice (expected 0.91, or 0.875 for the NonPlayable `00081B`) | live tree; speed was set to class, so hand-made | CONFIRMED | [split 9](splits/09-weapon-reach.md) |
| W4 | Reqtificated - Glenmoril / Vigilant | 14 warhammers: Glenmoril `2CECC4` Twin Hammer, `32C64F` Fishman Warhammer, `515257`, `5807CE`; Vigilant `048025`, `048026`, `048593`, `048594`, `537802`–`537807` | Reach 0.49 (10) or 0.7 (4) instead of 0.91 | damage / weight / speed / stagger copy REQ_Weapon_Daedric_Warhammer (reach 0.91); 116 / 139 ARR warhammers sit at 0.91. Looks like a dagger / 1H value pasted | PLAUSIBLE | [split 9](splits/09-weapon-reach.md) |
| W5 | Reqtificated - Runemaster / Ancient Blood / Obscure Magic | Runemaster `0008A1`–`0008A8:RunemasterMagic.esl` (7), `0008B0` / `B1` / `B3:AncientBloodII.esl`, `00084A:KittySpellPack01.esl` | Reach rescaled twice (runic sword 1.0 → 0.49, runic battleaxe 1.3 → 0.637) | the MR patches already bake × 0.7; ARR applied it again and added AD3B2E. Upstream origin | PLAUSIBLE | [split 9](splits/09-weapon-reach.md) |
| W6 | Reqtificated - LOTD | REQ_Artifact_GhorbashsAncestralAxe `06A8FD:Skyrim.esm` | Reach 1.2 with no rescale triple, so the build gives 0.84; its LOTD replica `14337E` is baked at 0.91 | live; speed set to battleaxe class but reach left at LOTD's | PLAUSIBLE | [split 9](splits/09-weapon-reach.md) |
| W7 | Reqtificated - Vigilant / LOTD / Unslaad | zzzCHMaraGreatSwordFlamePlayable `0C74EE:Vigilant.esm` + ~22 rows of pattern 455; 55 of 72 charge-absent rows keep the enchantment | Enchantment kept with charge absent (source 5000) | live read 0C74EE; family unsampled; in-game behaviour of a 0-charge enchanted weapon not tested | PLAUSIBLE | [stage1 WEAP](census/stage1/WEAP.md) |
| W8 | Reqtificated - LOTD | Winterhold staves `161D3A:LegacyoftheDragonborn.esm` (+ 4) | VMAD `dbm_addspellorperkonitemadded` removed (the spell-on-add feature) | verifier live read 1 → 0 | PLAUSIBLE | [stage1 WEAP](census/stage1/WEAP.md) |
| W9 | Reqtificated - Vigilant | pattern 489, 25 rows (e.g. `0C74EE`, `0E04BC`, `12F208:Vigilant.esm`) | WeapMaterialImperial removed; no replacement material keyword seen (temper / perks key on it) | unverified by either verifier | PLAUSIBLE | [stage1 WEAP](census/stage1/WEAP.md) |
| W10 | Reqtificated - Lucien | JRmihailinvhungerstrike `2E447A:Lucien.esp` | Damage 20 → 80 (× 4), where a 1H sword gets × 6 (120) | live delta; NonPlayable NPC weapon, possibly deliberate | PLAUSIBLE | [stage1 WEAP](census/stage1/WEAP.md) |
| W11 | Reqtificated - Glenmoril | Jillian / Anti-Jillian / Histus Gravity Pistol lines (`zzzRevJillianPistol00–10`, `zzzRevHistPistolA00–10`, `zzzRevHistPistolB00–10`, 33 records) | Charge left at source 500 + 50 × level (half-shot steps) while every other gun line went to + 100 (40 / 40). The patch overrides these records' Value / Weight / Damage, not the charge | live; asked as Q36 | PLAUSIBLE | [split 11](splits/11-house-conventions-b.md) |
| W12 | Authoria - Master Patch - Bruma | CYRAncientImperialBow `05BC3A`, …Supple `05BC44:BSHeartland.esm`, CYRAyleidBow `300064` | Speed 0.5 / 0.4 and damage 25 / 80 / 30 behind AD3B2F / AD3B2D: not the build values (0.3704 / 0.4445), not × 4 of the source | verifier family read | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |

## Armour

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| A1 | Obi's Hedge Knight Armor UBE patch | ARMA `000801:Obi's Hedge Knight Armor UBE patch.esp` | FirstPersonModel path ends in CR LF: the mesh won't resolve | verifier read the string ending `\r\n` | CONFIRMED | [pilot ARMO](census/pilot/ARMO.md) |
| A2 | Reqtificated - Vigilant | zzzCHArmorDaedricMageCuirass `03D788:Vigilant.esm` | Value 1 (source placeholder) → 0 on an AR 380 heavy daedric cuirass (Requiem daedric body 25,000) | verifier live read | CONFIRMED | [stage1 ARMO](census/stage1/ARMO.md) |
| A3 | Reqtificated - Dint Hair | wig `003BC3` | Keywords[0] is a null link (beside AD3B2B) | verifier live read | CONFIRMED | [stage1 ARMO](census/stage1/ARMO.md) |
| A4 | Animated Carriages patch | ARMO `0009F9:AnimatedCarriage.esm` | AR 180, hand-set, where Requiem's Tsun body (same armature, keywords) is 161 | pilot read | PLAUSIBLE | [pilot ARMO](census/pilot/ARMO.md) |
| A5 | Authoria - CC Fishing Patches | DBM_PrvtIVampiricRing `125B05:LegacyoftheDragonborn.esm` | VMAD dropped (prvtivampirelordscript + prvtivampiricscript, which LOTD and both other patches carry) | live tree; confirm in game | PLAUSIBLE | [stage1 ARMO](census/stage1/ARMO.md) |
| A6 | Reqtificated - Vigilant; Xtudo patch | NP_zzzCHArmorGorieusHelmet; Xtudo Nibenean hood | Value 375 → 0 (AR → 0 too); 75 → 0 | verifier note; may be NPC-only or clothing | PLAUSIBLE | [stage1 ARMO](census/stage1/ARMO.md) |
| A7 | Reqtificated - LOTD | REQ_Var_Light_AncientDragonscale_Head_Winterhold `0940D5:Skyrim.esm` | TemplateArmor `ADDD8E:Requiem.esp` cleared: drops the inherited stats and the rescale skip | family sample, not live-read | PLAUSIBLE | [stage1 ARMO](census/stage1/ARMO.md) |
| A8 | Ysmir Armor, Penetrator patches | `1YsmirBoots`, `penetratorF_Gauntlets`, … | ObjectEffect null → `0BE027` / `0BE024` added | family sample; intent unknown | PLAUSIBLE | [stage1 ARMO](census/stage1/ARMO.md) |
| A9 | Authoria - Master Patch - Bruma | 21 CYREnchArmorIronCuirass* variants v base CYRArmorIronHeavyCuirass `0D51D1:BSHeartland.esm`; CYRArmorFurHelmet `300014` | Variants AR 250, base 275 (fur helmet variant 60 v base 50). Requiem iron body is 250, so the base was pitched 10% above | live Requiem read; sync slip, which value is meant? | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |
| A10 | Reqtificated - Moonpath | Alfiq clothes `000804` (AR 5), hoods `000805` / `00080C` (AR 1), JKJ - Moonpath.esp | Wearable clothing with AR > 0 | subject scan (the creature skins with AR are probably legit) | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |
| A11 | Reqtificated - LOTD | DBM_VisageGlovesBASIC `124E11:LegacyoftheDragonborn.esm` | Value 400 → 2500; every other Visage gauntlet keeps 400 and the other BASIC pieces follow their Heavy twins | stage-1 delta; economy only | PLAUSIBLE | [split 10](splits/10-house-conventions-a.md) |

## Ammo

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| AM1 | Reqtificated - Val Serano | AX_ValCrossbowBolts `162F76:AX ValSerano.esp` | A bolt carrying the **arrow** tier keyword `REQ_ArmorPiercingArrow_Tier3` and arrow damage 70 | Requiem `REQ_Bolt_Dwarven` = 84 + `REQ_ArmorPiercingBolt_Tier3`. Keyword CONFIRMED; damage 84 PLAUSIBLE | CONFIRMED | [split 8](splits/08-ammo-damage.md) |
| AM2 | Reqtificated - Glenmoril | zzzLrhForswornSpearThrown `007DEA:Glenmoril.esm` | An arrow (NonBolt) carrying `REQ_ArmorPiercingBolt_Tier3` | Requiem's only NonBolt + Bolt-tier records are creature bolts | PLAUSIBLE | [split 8](splits/08-ammo-damage.md) |
| AM3 | Kaidan, LOTD, Vigilant, CC Saints and Seducers patches | KaiArrow; DaedricArrowUnp `125ADF:LegacyoftheDragonborn.esm`; zzzCHLogArrowEx; EC_SS_JygSwordBack | Incomplete ammo keyword kit: Kaidan no ArmorPiercing tier; LOTD Daedric no AmmoWeight (Requiem Daedric = Massive); Log and Jyggalag neither | every Requiem arrow / bolt carries both (tier omitted only at ≤ 40 damage) | PLAUSIBLE | [split 8](splits/08-ammo-damage.md) |
| AM4 | Falkreath Shades patch (held out) | GRVEGolemShooterBolt `21F8C0:FalkreathShades.esp` | No material and no AmmoWeight keyword; keeps `IgnoresNormalWeaponResistance`, which other ammo drops | low | PLAUSIBLE | [split 8](splits/08-ammo-damage.md) |
| AM5 | Reqtificated - Glenmoril | zzzRevBoltOblivionBullet, zzzRevBoltApoBullet (Glenmoril.esm) | `WeapMaterialDaedric` on rounds priced 90 / 60 with Bolt_Tier4 / 5 (Requiem Daedric bolt 144, Tier7) | low; may be cosmetic on purpose | PLAUSIBLE | [split 8](splits/08-ammo-damage.md) |
| AM6 | Reqtificated - Vigilant | great / log arrow projectiles `0BAB16` `0BD35A` `0C7E3A` `0C7E3B` `0D1013` | Gravity 1.25; no Requiem arrow exceeds 1.0 | live; asked as Q41 | PLAUSIBLE | [stage2 QUEST-MISC](census/stage2/QUEST-MISC.md), [split 13](splits/13-locks-consumables-quest-calls.md) |
| AM7 | Reqtificated - Remiel | bolt projectile `237ED9` | Gravity 0.2 → 1.0, off the bolt tier (Requiem bolt `000BB4` 0.35) | low; asked as Q41 | PLAUSIBLE | [stage2 QUEST-MISC](census/stage2/QUEST-MISC.md) |
| AM8 | Reqtificated - Unslaad | zzzCrbFrostArrowPROJ `17854E:Unslaad.esm` | Arrow projectile speed 3600 → 5400 (bolt-like) | Requiem arrows 3600, bolts 5600; source was already 3600 | PLAUSIBLE | [stage2 QUEST-MISC](census/stage2/QUEST-MISC.md), [split 13](splits/13-locks-consumables-quest-calls.md) |
| AM9 | Reqtificated - Val Serano (no override) | AX_ValBoltProjectile `162F75:AX ValSerano.esp` | Bolt projectile left at vanilla 5400; every other bolt projectile ARR touches goes to 5600, and this patch patches the ammo | low | PLAUSIBLE | [split 13](splits/13-locks-consumables-quest-calls.md) |
| AM10 | Reqtificated - Vigilant | zzzCHLogArrowEx (Vigilant.esm) | Value left 18 while its twin zzzCHLogArrow went 18 → 1 | the Ebony great arrow and its Ex twin both went to 30 | PLAUSIBLE | [split 13](splits/13-locks-consumables-quest-calls.md) |

## Magic (spells, perks)

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| MG1 | Reqtificated - Vigilant | zzzCHConjureVernaccus `10F6B4:Vigilant.esm` | BaseCost 350 without ManualCostCalc: the engine autocalcs, 350 is ignored | live: Flags = NoAbsorbOrReflect only; Bourlor has MCC | CONFIRMED | [stage2 MAGIC](census/stage2/MAGIC.md), [split 12](splits/12-magic-race-calls.md) |
| MG2 | Reqtificated - Vigilant | Vernaccus `10F6B4`, zzzCHConjureBourlor `13BAAF` | Master BothHands ritual with ChargeTime 0 / 0.5 and CastDuration 3 (inert on FireAndForget) | live; MR rituals ChargeTime 3, CastDuration 0 (571 / 571) | CONFIRMED | [stage2 MAGIC](census/stage2/MAGIC.md), [split 12](splits/12-magic-race-calls.md) |
| MG3 | Reqtificated - Glenmoril | zzzRevPerkArmorPiercing60 / 65 / 70 (`012015`, `012017:Glenmoril.esm`) | AP ladder inverted: 60 = 0.65, 65 = 0.4, 70 = 0.5, so AP65 beats AP70. Likely AP65 typo for ~0.6 | verifier live read. Asked as Q6 | CONFIRMED | [stage2 PERK-AI](census/stage2/PERK-AI.md), [split 2](splits/02-perk-values.md) |
| MG4 | Reqtificated - LOTD | DBM_VisageRemanShieldPerk01 / …Ability01 (LegacyoftheDragonborn.esm) | Description typo "absorprtion", shown in game | stage-2 delta | CONFIRMED | [split 10](splits/10-house-conventions-a.md) |
| MG5 | Apocalypse patch | scrolls MysticWind `03AF3B`, CircleOfTheMoons `03AF41`, Willpower `1A7926`, Transcendence `083FAB:Apocalypse - Magic of Skyrim.esp` | BaseCost → 0 without ManualCostCalc (autocalc), where the other 66 Apocalypse scrolls have BaseCost == Value + MCC | ledger + live flags; low impact | PLAUSIBLE | [stage2 MAGIC](census/stage2/MAGIC.md) |
| MG6 | Reqtificated - Glenmoril | Blazing Cow ability | Effect 064459 (mag 10) removed with no AbHide replacement | verifier note; spell FormID not recorded | PLAUSIBLE | [stage2 MAGIC](census/stage2/MAGIC.md) |
| MG7 | Bruma; Lucien; Glenmoril | CYRIcyFireball `07233F:BSHeartland.esm` (0F3930); JRBlueFireBalltestteleport `297429:Lucien.esp` (0F3930); zzzGHEnchDweEmitter `0125B5:Glenmoril.esm` (0F3F0C) | Vanilla perk-proc effects left on patched winners (the drop the other patches make) | live reverse MGEF walk; no Requiem / MR spell carries these | PLAUSIBLE | [split 12](splits/12-magic-race-calls.md) |
| MG8 | Gray Cowl; Vigilant | manny_GF_Spell_Mark / _Recall / _ClearMark; zzzCHSealedSun | HalfCostPerk school differs from the first effect's school | MR 365 / 365 match; low | PLAUSIBLE | [split 12](splits/12-magic-race-calls.md) |
| MG9 | Reqtificated - Dac0da | zDcdPerkDragonKiller1H | +15 → +80 v dragons; every other dragon perk lands at 45 | asked as Q7 | PLAUSIBLE | [split 2](splits/02-perk-values.md) |
| MG10 | Reqtificated - Vigilant | zzzCHcrExtraDamageNPCs `0D2F45`, zzzCHcrMolagExtraDamageNPCs `09D82F` | × 2.5 / × 3 → × 1 (neutralised in place) | likely deliberate (`VIG_REQ_` perks replace); check those reach the NPCs | PLAUSIBLE | [stage2 PERK-AI](census/stage2/PERK-AI.md), [split 2](splits/02-perk-values.md) |
| MG11 | Reqtificated - Glenmoril | GLEN_REQ_ExtraDamage06 `000801` | ModAttackDamage 4 / 6 / 2.5; Requiem's steps are 1.1–1.5 | exclusivity of the three conditioned entries not checked | PLAUSIBLE | [stage2 PERK-AI](census/stage2/PERK-AI.md) |
| MG12 | Reqtificated - Vigilant / TOK / Unslaad / Glenmoril | spell lists zzzGHLSpellWitchBloodBall `01125D` (+ `01125E`, `01255E`, Glenmoril); zzzCHLSpellFleshLeftHand `0C3A55` (Vigilant) | Tiered spell lists flattened: 7 tiers L1–48 → top spell at L1; 4 tiers → Ebonyflesh at L1 (Requiem keeps tiers, `105101`) | verifier live read 01125D; systemic | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |

## Race

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| R1 | Reqtificated - Vigilant | zzzCHShoggothRace `080D5D:Vigilant.esm` | Starting Health −150 (source 125); Stamina 100 → 1000 | live winner read; the sign looks like a slip | PLAUSIBLE | [stage2 RACE](census/stage2/RACE.md) |
| R2 | Reqtificated - Vigilant | zzzChScorpionGiantRace `534133`, zzzChScorpionLargeRace `534134`, zzzCHShoggothMotherRace `080D5E` | BaseMass 0.25 (source 4 / 3.5 / 5): the small-spider mass copied onto giants (FrostbiteSpiderRaceGiant is 4) | live | PLAUSIBLE | [stage2 RACE](census/stage2/RACE.md) |
| R3 | Glenmoril; Bruma | 11 races: zzzRevGryphonRace, zzzRevManticoreRace (+_Boss), zzzRevKhimeraRace (+_Boss), zzzRevEntTrollRace, zzzRevEntDraugrRace, zzzRevEntGiantRace, zzzRevMushGuyRieklingRace, zzzRevHandplastRace; BSKAtronachColdFlameRace | `REQ_NULL_AbResist*` left unswapped in the source ability, no Resist trait (the swap the other races got) | resists still work mechanically | PLAUSIBLE | [split 12](splits/12-magic-race-calls.md) |
| R4 | Bruma; Val Serano; Tools of Kagrenac | 10 races: CYRAyleidLichRaceCANTALK, CYRMizregRace, BSKAyleidLichRace, BSKSprigganFrozenRace; AX_DaedrothRace, AX_CthulhoidRace, AX_WatcherRace, AX_StormGolemRace; TOK_RACE_ImperfectCenturionRace, TOK_RACE_AyleidLichRace | REQ_NULL resists left in the ability **and** a `Resist_<X>` trait added: they stack (e.g. AX_StormGolem Shock 100 + Resist_AtronachStorm) | live race + ability reads | PLAUSIBLE | [split 12](splits/12-magic-race-calls.md) |
| R5 | Vigilant; AFDI | zzzCHEbonyGiantRace (+ x15, x20), zzzCHXivkynGiantRace, zzzCHMentanaRace; mihaildaedroth_lesser_race (+ forshock) | AbHide resists in the ability plus a Resist trait: double resist (e.g. Fire 35 + Resist_Giant) | low | PLAUSIBLE | [split 12](splits/12-magic-race-calls.md) |
| R6 | AFDI; Glenmoril; Vigilant; Bruma | AFDI_RACE_ApocAbomRace; zzzLrhOwlRace, zzzRevDagiRace; zzzCHMaggotmanRace, zzzCHKanraRace, zzzChScorpionGiantRace, zzzChScorpionLargeRace, zzzCHCowBelharzaRace; CYRMinotaurRace, CYRMinotaurLordRace; + 9 Draugr / Spirit-armoured races (e.g. zzzCHShamblesRace, zzzRevDraugrLittleRace) | Armor trait but no resist anywhere, though every Requiem race with that trait carries one | gap list; low | PLAUSIBLE | [split 12](splits/12-magic-race-calls.md) |
| R7 | Glenmoril; Vigilant; others | zzzRevAUFalmerRace `2E0322:Glenmoril.esm`, zzzCHMagicBroomRace `0E04DF:Vigilant.esm`; 60+ races with no REQ_Trait_* (Glenmoril 23, Vigilant 13, Dac0da 10, Bruma 9, Races Merge 5); zzzGHDweGearRace keeps REQ_LEGACY_AbDwarvenAutomatons | No Requiem trait at all (FalmerRace carries Resist_Falmer + TheBetrayed); one legacy spell left where siblings got Resist_Dwarven* | some legit (DogRace has none); per-race check needed | PLAUSIBLE | [stage2 RACE](census/stage2/RACE.md) |

## Containers / locks

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| C1 | Authoria - Master Patch - Container Merge | REQ_Chest_GlassShield_Dawnstar `AD3AFC:Requiem.esp` | Drops `AA000XarrianLockBreakScriptNormal` (Requiem and WAR both carry it) while adding WAR's item; the build re-adds only REQ_LockpickControl, so Xarrian is lost in the final build | tree read incl. RFTI output | CONFIRMED | [stage2 LOCKS](census/stage2/LOCKS.md) |
| C2 | Reqtificated - Armours of Velothi | REFR `087901:Skyrim.esm` (FFRiften08BrandSheiChest) | Master lock, no key, added to a Riften quest chest by an unrelated armour patch | tree read: only Skyrim.esm and this patch touch it | PLAUSIBLE | [stage2 LOCKS](census/stage2/LOCKS.md) |
| C3 | Authoria - Master Patch - Bruma | CYRTreasBanditChest (BSHeartland.esm) | No Xarrian lock-break script, though the patch adds it to 28 sibling CYR chests, incl. CYRTreasBanditChestBoss | vanilla twin `03AC21` carries it; same Chest01 model | PLAUSIBLE | [split 13](splits/13-locks-consumables-quest-calls.md) |
| C4 | Reqtificated - LOTD | DBM_RainsKnapsack `1A6223:LegacyoftheDragonborn.esm` | Respawns added on a knapsack holding unique museum fragments, keystones and `DBM_ResearchNotesGreater`: they refill on cell reset | vanilla knapsacks respawn, but hold only generic lists | PLAUSIBLE | [split 13](splits/13-locks-consumables-quest-calls.md) |
| C5 | Reqtificated - LOTD; Miahil Hawk and Tern | 22 vanilla containers (e.g. `02064F`, `020662`, `03AC21`, `0774BF`), `14CF41` DBM_JARBarrel; `105821` fcHawkGO | REQ_LockpickControl missing, unlike the plugin's other 312 records | harmless while the build loads after; wrong only without it | PLAUSIBLE | [stage2 LOCKS](census/stage2/LOCKS.md) |

## Consumables

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| K1 | Blended Shrooms.esp | FloraMushroom02Small_c (Bleeding Crown) `000807` | Ingredient → `2500DD` REQ_LI_Mushroom03 (White Cap list) | siblings 000804–6 and Requiem's FloraMushroom02Small → `2500DA`; verifier confirmed | CONFIRMED | [stage2 CONSUMABLES](census/stage2/CONSUMABLES.md), [split 13](splits/13-locks-consumables-quest-calls.md) |
| K2 | Reqtificated - Glenmoril | flora: two WhiteShade trees (zzzLrhTreeFloraWhiteShade01 + sibling), maggot egg nest `00DB58`, kin cluster; ingredient zzzGHMaggotEgg01 `00DB59:Glenmoril.esm` | Ingredient nulled on the only 4 yielding plants; Maggot Egg now has no source at all | reverse references 0; may be deliberate, asked as Q45 | PLAUSIBLE | [stage2 CONSUMABLES](census/stage2/CONSUMABLES.md), [split 13](splits/13-locks-consumables-quest-calls.md) |
| K3 | Ryn's Farms.esp | Ryn's Farms brandy | Loses `00082F:Requiem - Foods and Ingestibles`, gains `10C645` + `03EAEB` | unresolved lead | PLAUSIBLE | [stage2 CONSUMABLES](census/stage2/CONSUMABLES.md) |

## Recipes

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| P1 | Reqtificated - LOTD | DBM_AAWulfharthLTemperREP `217CBB:LegacyoftheDragonborn.esm` | WorkbenchKeyword = AD3B45 REQ_DisableAlcoholBlur, a non-bench keyword | verifier read | CONFIRMED | [stage1 COBJ](census/stage1/COBJ.md) |
| P2 | Reqtificated - LOTD | DBM_TiberArmorLTemperREP `217CB9`, DBM_MiraakMaskLTemperREP `217CBF` | AD3B01 (a keyword) put in CreatedObject, not WorkbenchKeyword. Net effect is a disable, by the wrong field | verifier reads | CONFIRMED | [stage1 COBJ](census/stage1/COBJ.md) |
| P3 | Obi's Hedge Knight Armor patch | COBJ 00080B, 00080C (suit + helmet forge) | Retired with AD3B01; nothing else makes those items (the Alt helmet got a new recipe) | pilot read | PLAUSIBLE | [pilot ARMO](census/pilot/ARMO.md) |
| P4 | Obi's Hedge Knight Armor patch | COBJ 00080D (suit temper) | Iron count 4 where the per-piece sum is 3 | low; may be rounding | PLAUSIBLE | [pilot ARMO](census/pilot/ARMO.md) |
| P5 | COCKS-listed mods (Twilight Princess, Apex Khajiit, FVO Cloth, Wine Duchess, Arcane Mage, Sigrin, Moon Monk, …) | 250 items, list in [`census/recipe-orphans.csv`](census/recipe-orphans.csv); e.g. Twilight Princess cuirass `000802` craft recipes (8, incl. 000841, 000842, 0008AF) on GetLevel()==0 | Retired and obtainable nowhere, though COCKS gives them a forge category | obtainability walk; "strongest candidates for left uncraftable by accident". Asked as Q16 | PLAUSIBLE | [split 5](splits/05-recipe-retirement.md), [stage1 COBJ](census/stage1/COBJ.md) |
| P6 | Reqtificated - Unslaad | SnowSpirit 01–06 (01 = `05267F`) | GetLevel()==0 on 01–06 while SnowSpirit07 `052685` stays live | verifier; same root as P5 | PLAUSIBLE | [stage1 COBJ](census/stage1/COBJ.md) |
| P7 | Reqtificated - Dint Hair | wig recipes, e.g. `000DC6:[dint999] HairPack02.esp` (358 rows) | `[GetItemCount OR, GetLevel()==0]`: the OR makes the GetLevel gate a no-op | verifier: same gate as the source | PLAUSIBLE | [stage1 COBJ](census/stage1/COBJ.md) |
| P8 | Reqtificated - Val Serano | AX_ValCrossbowBoltsRecipe `A038AB:AX ValSerano.esp` | Yield 25 → 10; Requiem bolt recipes yield 30 | ledger; live Requiem | PLAUSIBLE | [stage1 COBJ](census/stage1/COBJ.md), [split 8](splits/08-ammo-damage.md) |
| P9 | Moon Monk; FVO; Pig Iron; Ruby Flower; Lastriem | 0RecipeArmorKhajiitRobes_Clothes `000925`; aaa_CraftFur2 / Craft2 `000866` / `000883`; Pig Iron Boots craft `000802`; Ruby Flower Gloves NoClaws `000835`; Lastriem Pants temper `000816` | Craft retired on a live-named item with no surviving recipe; Lastriem the reverse (craftable, untemperable) | live reads | PLAUSIBLE | [stage1 COBJ](census/stage1/COBJ.md) |
| P10 | Reqtificated - Dwemer Armor | tempers `000805`–`000808:1Dwemer.esl` (+ crafts `000A02`–`000A05`) | All 8 on AD3B01: the armour exists only as placed refs, so found armour can't be tempered | live; the craft disable may be deliberate (find-only) | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |
| P11 | Authoria - Master Patch - Bruma | AUTH_NULL_BSKRecipeJewelryRingSilverSapphire / Emerald `602920` / `602921:BSAssets.esm`; hide → leather recipes (CYRRecipeLeatherDeerHide, …WolfHide, …MountainLionPelt + 2) | Recipes disabled with no replacement for live items (rings `60291F` / `60291E`); leather path not compared with Requiem's tanning | verifier (rings); lead (leather) | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |
| P12 | Reqtificated - Lunar Guard Armor (Standalone) | GCON_Temper_LunarGuard_* (4), Temper_LunarGuard_Battleaxe | Double perk gate: mod perk `00088C:Kad_MoonMonkRobes.esp` AND a Requiem material perk | ledger; is the mod perk obtainable in ARR? | PLAUSIBLE | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) |

## Other (lists, outfits, cells, explosions)

| # | Plugin | Record(s) | What's wrong | Evidence | Label | Source doc |
|---|---|---|---|---|---|---|
| O1 | Reqtificated - Glenmoril | zzzGHDeathItemM06Lurcher `4528C8`, …LurcherAncient `4528CB:Glenmoril.esm` | Give-all death item turned into CalcFromAllLevels + ForEachItem, and its own item removed (4 → 2 entries) | verifier tree read | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |
| O2 | Reqtificated - LOTD | DBM_CC_LItemCuratorReward050+ `0CCC70`… `DBM_CC_HallOfWonders.esp` | LOTD artifact weapons (`125A1B`, `1259F2`, …: Trueflame, Thornblade, Stendarr's Hammer) swapped for gold 500–1,250; nothing nulled the artifacts | ledger; maybe deliberate | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |
| O3 | Vigilant, TOK, Unslaad, Glenmoril patches | 29 live-spell tomes (Vigilant zzzCHConjure* 10, TOK race tomes 10, Unslaad 8, Glenmoril 1) | Culled from loot while their spells stay live: tome unobtainable (reverse refs 0) | verifier read all 45 removed BOOK; systemic | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |
| O4 | Reqtificated - Bannermyst | LItemEnchWeaponSwordDUPLICATE051 `00085E:Bannermist.esp` | 12-rung enchanted-sword ladder (to L47) collapsed to one `04B583` at L1 | ledger; vanilla original not read | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |
| O5 | Reqtificated - Wet and Cold | `05C8E0`, `05E3D5:WetandCold.esp` | Backpack pools 27 → 0 entries | verifier tree; consumer quest script unread | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |
| O6 | Reqtificated - LOTD | DBM_ShadowHunterOutfit `1A6A02:LegacyoftheDragonborn.esm` | 4 Kynreeve pieces swapped across types (NonPlayable light torso `1A18F8` out, heavy boots `1A18EE` in) | live read; low | PLAUSIBLE | [stage2 LISTS](census/stage2/LISTS.md) |
| O7 | Reqtificated - Gray Cowl of Nocturnal; Reqtificated - Troll MCO | cell mannyGFIntShantaFilibb02 `050CEB:Gray Fox Cowl.esm`; slam explosions `00080B`, `00080E` | Player-house cell (3 mannequins) moved onto the desert dungeon zone while its sibling stays home-zoned (harmless: also NeverResets). Troll explosions' SpawnProjectile nulled (unconfirmed) | live CELL / LCTN reads; Troll ledger only | PLAUSIBLE | [split 10](splits/10-house-conventions-a.md), [stage2 QUEST-MISC](census/stage2/QUEST-MISC.md) |

## Errors in our own docs

| # | Doc | Error | Correction |
|---|---|---|---|
| D1 | [split 4](splits/04-enchant-charge.md), rule 1 and the Glenmoril specific | "Bruma 93 / 102, 3 per tier sit +1000" was measured against the source enchantment; and the "+50 … +500 step table" is the +100 re-step seen as a delta | Bruma re-tiered 9 Ayleid weapons; by the shipped enchantment it is 106 / 106 ([split 11](splits/11-house-conventions-b.md)) |
| D2 | [stage2 MAGIC](census/stage2/MAGIC.md) S4 | Glenmoril throwing knives 372295 / 372298 called a wrong-school HalfCostPerk | Withdrawn: the first effect is a Conjuration MGEF, so the Conjuration perk matches ([split 12](splits/12-magic-race-calls.md), M5) |
| D3 | [stage2 GEAR-NPC-S2](census/stage2/GEAR-NPC-S2.md) conv #3 and #5 (and stage 1's versions) | Level read as "PcLevelMult → fixed, AutoCalc kept"; offsets "mostly inert" | The real rule is a whole stat-block clone from a Requiem analogue (467 / 541); offsets are live, only Stats-masked rows are inert ([family check](splits/14-npc-stage2-family-verification.md)) |

Cleared, not counted: census MAGIC S3 (the Resist Damage drop is a rule; the open part is Q38); census RACE 3
(~140 Glenmoril races get resists through their ability); Bruma's 68 ActorEffect edits under a SpellList mask
(inert noise); LOCKS' Vigilant knapsack `026E87` and `09DA64` (deliberate).
