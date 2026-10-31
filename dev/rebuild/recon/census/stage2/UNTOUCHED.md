# Untouched-record audit: partial (source, type) cases

*Author: Aaron (+ Claude session) · 2026-10-30*

Scope: the 315 non-Requiem-ecosystem partial cases in `untouched.json` (some records touched by an ARR patch, others not).
Cases below are the largest by untouched count; all 25 biggest are covered. Evidence is live winner reads (about 40 reads
of `housecarl_records`; counts used `where=` over the source's records). "Winner = source" means no ARR or other patch
overrides the record, so its source values are live.

Verdict key: **likely-miss** (needs Requiem change, none present), **legitimate**, **mixed**, **unclear** (not read, or
reading did not settle it).

## Method notes

- Untouched = no ARR patch override. I checked what the live winner carries: Requiem perks/enchants, template links, scale.
- Benchmark for template-linked variants: Requiem.esp sets its own enchanted armour variants to the **same AR as the
  template** (`10DFBE:Skyrim.esm` REQ_Ench_Heavy_Iron_Shield_ResistMagic3 AR 150 = template `012EB6:Skyrim.esm` AR 150),
  because the build skips TemplateArmor armour (code: `ArmorRatingScaling.cs:40-46` returns early on a non-null TemplateArmor; `ArmorKeywordsFromRules.cs:24` and `ArmorTypeKeyword.cs:17` skip the same way). Enchanted weapon variants stay at base damage
  (`089716:Skyrim.esm` REQ_Ench_Weapon_Iron_Sword_Fear3 dmg 7, Template `012EB7`), because the build skips Template weapons
  (fact 9).

## Cases (largest untouched first)

| Source | Type | T / U | Verdict | Reason | Evidence |
|---|---|---|---|---|---|
| Skyrim Extended Cut - Saints and Seducers.esp | Weapon | 13 / 1261 | legitimate (medium) | 1261 are source-winning; 1173 non-staff are enchanted variants with Template set and REQ_Ench_Weapon_* enchants already; dmg 13 matches Requiem's own convention of leaving ench variants at base damage. Staves have dmg 0 with REQ_Ench_Staff_* enchants | 57AFCC (bow ench, dmg 13, ObjectEffect 02C46F:Dragonborn.esm REQ_Ench_Weapon_Chaos3), 00083B (staff, REQ_Ench_Staff_Conjuration3...); touched = the 13 base Grummite / Amber / Madness items, e.g. 2F68AD |
| BSHeartland.esm | Npc | 918 / 1209 | legitimate (mostly) | 1154 source-winning: 1022 have Stats+SpellList template flags (masked, build stamps nothing needed); 125 untemplated are generic AutoCalc citizens (e.g. Level 4 Citizen class) with no perks/spells, which the build assigns via its rules (not AlreadyReqtified) | 0FC809, 0038FF, 003C89 (templated); 06DAA8-06DAAB (L4 citizens). Named NPCs 003A18, 003A1F, 003A20 have TemplateFlags 0 and 0 perks/spells: unchecked for combat role |
| BSHeartland.esm | LeveledItem | 1199 / 1180 | unclear | Not read. Samples are barrels, ingots, inn food, vendor misc, soldier gear lists. Build does not touch a mod's own LVLI (fact 1). Gear lists only matter if they hold unpatched gear | 05F09D, 0038AD, 06DA7C (CYRSoldierImperialGear) |
| LegacyoftheDragonborn.esm | ConstructibleObject | 209 / 647 | unclear | Not read. Samples are museum replica / display / crafting recipes (DBM_REPLICA_*), which have no Requiem equivalent; touched 209 are probably the real item recipes. Likely fine | 166300, 166305, 0010D3 |
| Vigilant.esm | MagicEffect | 23 / 527 | legitimate | Spell side is already Requiem-native (below). Requiem needs no MGEF change for new effects; fact 32 (all magic is upstream input) | 10D838, 10C892 |
| Skyrim Extended Cut - Saints and Seducers.esp | Armor | 20 / 458 | **likely-miss** | 431 of 457 source-winning are enchanted variants with TemplateArmor set; 404 carry AR < 50 (vanilla scale) while their template was patched to Requiem scale. Build skips TemplateArmor, Requiem sets variant AR = template AR. 26 others are NonPlayable skins / clothing (legit) | 57AED9 (Golden Saint Boots ench, AR 15) vs template 000878:ccBGSSSE025-AdvDSGS.esm AR 120 (winner Trad_SaintsSeducers025Curios037_Requiem_Patch.esp); 57AEDA AR 15; skins 02E8A4, 033BB7 NonPlayable AR 0 |
| Glenmoril.esm | MagicEffect | 2 / 449 | legitimate | As Vigilant MGEF | see Spell rows |
| Glenmoril.esm | Npc | 1566 / 437 | unclear | Not read. Samples are `zzzRevLv*` leveled creature variants (Aggro / Ambush / Missile), which are normally template-masked (Stats+SpellList) | 015F5A, 015F5B, 2AC028 |
| Glenmoril.esm | LeveledItem | 231 / 402 | unclear | Not read; own LVLI untouched by build | none |
| Glenmoril.esm | Armor | 500 / 380 | mixed | 368 are NonPlayable skins / clothing (legit). **12 playable body-slot armours** are enchanted/magic variants at vanilla AR 8-11 with TemplateArmor set: likely-miss | 009B27 (wooden shield magic, AR 8), 012DE7-012DE9 (Ardeo Copper Ench, 9), 012DEC/EF/F0 (Gold, 11), 012DF2/F4/F5 (Silver, 10), 014E3D (10), 366877 (9) |
| Vigilant.esm | Spell | 402 / 310 | legitimate | Source carries Requiem perks itself: `10D83A` HalfCostPerk REQ_Conjuration_Mastery_075 (Expert), `10C893` REQ_..._050 (Adept). Across Vigilant, Glenmoril, Unslaad, DAc0da, Apocalypse, LOTD, BSAssets, Tools of Kagrenac, **0** spells have a non-REQ_ HalfCostPerk | 10D83A (cost 394), 10C893 (cost 215) |
| Glenmoril.esm | Spell | 257 / 295 | legitimate | Same count (0 non-REQ perks). Not checked: cost values vs Requiem tiers | see above |
| Vigilant.esm | Armor | 672 / 290 | mixed | 282 skins / clothing legit. **8 playable** ench variants at vanilla AR with TemplateArmor: likely-miss | 0B2539 (AR 23), 0BD352 (10), 0E1079 (28 shield), 0E7EFB (13), 5584C2/C3/C4 (13), 5584C5 (15) |
| BSHeartland.esm | LeveledNpc | 169 / 258 | unclear | Not read; build does not edit LVLN | none |
| Glenmoril.esm | Book | 9 / 255 | unclear | Not read. Includes spell tomes (`zzzRevSpellTome*`); a tome needs to teach a spell on the Requiem ladder, which the source does not check | 4528DF, 4528E0, 594AC5 |
| DAc0da.esm / Unslaad.esm | MagicEffect | 22 / 234; 23 / 215 | legitimate | As Vigilant MGEF | none |
| BSHeartland.esm | Armor | 532 / 210 | mixed | Only **1** playable light/heavy armour in the source-winning set: ench boots AR 12, TemplateArmor set (likely-miss). Remaining 209 are clothing, skins, NonPlayable | 0D3022 CYREnchArmorSteelBootsCarry01 (AR 12, template 300022); 080F7B etc. (clothing AR 0) |
| Vigilant.esm | Npc | 1026 / 207 | unclear | Not read. Samples `zzzCHLv*` leveled variants and encounter entries | 0BF828, 0C8417, 4FFB9A |
| AnimatedCarriage.esm | Npc | 175 / 191 | unclear | Not read | none |
| Vigilant / BSHeartland | Outfit | 23 / 189; 4 / 160 | legitimate (assumed) | Outfits only reference gear; Requiem outfits unaffected. Not read | none |
| Apocalypse - Magic of Skyrim.esp | Spell | 176 / 197 | legitimate | Covered by the 0-non-REQ-perk check above | none |
| BSHeartland.esm | Weapon | 375 / 129 | mixed | 129 source-winning: 83 template ench variants (legit as S&S); **46 playable non-template weapons** at vanilla damage (build rescales ×6 itself, so damage is covered). Not checked: Requiem material / temper keywords on them | 30005C TemperedSteelBattleaxe (dmg 18, 3 keywords), 30005D dagger (5), 0EE5C5 CYRThorneblade (11), 0D383B CYRHvitbrand (11), 06C14D (11) |
| Glenmoril.esm | Weapon | 545 / 80 | legitimate | 79 playable with dmg > 0, **0** without Template: all enchant variants left at base (Requiem convention) | 5BCE70 (dmg 13, Template 5BCE6D), 5BCE73 (dmg 23) |
| Unslaad.esm | Armor | 297 / 147 | mixed | Combined check over Unslaad, DAc0da, LOTD, BSAssets, Val Serano: playable light/heavy AR < 40 winners: Unslaad.esm 1, DAc0da.esm 6 (the rest are won by other patches). Remainder skins / clothing | not itemised (count only) |
| DAc0da.esm | Armor | 126 / 86 | mixed | Up to 6 playable low-AR source-winning armours (likely enchant variants) | counts only |
| LegacyoftheDragonborn.esm | Armor | 273 / 156 | mixed | 180 low-AR playable light/heavy are won by Authoria - Reqtificated - LOTD.esp (patched) and 2 by Requiem - New Legion - LOTD.esp; none counted as LOTD-source-winning | counts only |
| Other 14 of the top 25 (LVLI LOTD/Vigilant/DAc0da, Book Vigilant/BSAssets, Perk Vigilant/LOTD, Outfit LOTD, MiscItem BSHeartland, Spell Tools of Kagrenac / BSAssets) | various | | unclear | Not read; LVLI / Book / Perk / MISC sampled by name only | none |

## Patterns: what ARR leaves alone

1. **Enchanted variants with a Template link.** Weapons: left at base damage, matching Requiem. Armour: left at vanilla AR,
   which does **not** match Requiem (it sets variant AR = template AR). For Glenmoril, Vigilant and BSHeartland the ARR patch author patches the base and skips the
   variants. For S&S the bases are patched by the third-party Trad_SaintsSeducers025Curios037_Requiem_Patch.esp, not ARR,
   so that gap is not an ARR miss. This is the one systematic gap found.
2. **Skins, NonPlayable armour, clothing, and creature-only records**: legit (AR 0 clothing is vanilla-faithful, fact 7;
   skins have AR 0, fact 27).
3. **Spells and MGEFs from Requiem-aware sources** (Vigilant, Glenmoril, Unslaad, DAc0da, Apocalypse) already carry
   REQ_ mastery perks, so the unpatched ones are the source's own handling. 0 of the spells with a half-cost perk carry a
   non-REQ_ one.
4. **Template-masked leveled-creature NPCs** (`*Lv*` variants, 1022 of 1154 in BSHeartland): Stats+SpellList flags mean the
   edit would be masked; the build assigns what is left.
5. **Mod-internal LVLI / LVLN / Outfit / MISC / replica COBJ**: no Requiem change needed unless the list holds
   unpatched gear (not verified).

## Likely misses for Aaron's review (all low-AR, TemplateArmor-linked enchant variants)

Severity depends on an untested point: whether the engine uses a variant's own AR or its template's AR in game. If it uses the template's, the gap is cosmetic.

- S&S Extended Cut (bases patched by third-party Trad patch, not ARR; ARR attribution does not apply): about 404 playable armours, e.g. 57AED9-57AEDC (Golden Saint Boots, AR 15 vs 120), madness helmets
  57AEDD-57AEE0. Highest impact, one rule (copy the template's patched AR).
- Glenmoril: 12 (009B27, 012DE7-012DEC, 012DEF-012DF0, 012DF2, 012DF4-012DF5, 014E3D, 366877).
- Vigilant: 8 (0B2539, 0BD352, 0E1079, 0E7EFB, 5584C2-5584C5).
- BSHeartland: 1 (0D3022). DAc0da.esm: up to 6; Unslaad.esm: 1 (not itemised).
- Plausible: BSHeartland's 46 non-template weapons (damage is rescaled by the build, but Requiem material / tempering
  keywords unchecked); Glenmoril spell tomes (`zzzRevSpellTome*`) on the Requiem tome ladder.

## Limits

- Read budget (about 40) was spent on the largest armour / weapon / spell / NPC cases. LVLI, LVLN, Book, Perk, Outfit, MISC,
  COBJ and the Glenmoril / Vigilant / AnimatedCarriage NPC cases were judged from sample names and counts only (marked
  unclear).
- Whether the engine uses a template-linked armour's own AR or its template's was not tested in game; the miss verdict
  rests on Requiem's own convention (equal AR) and on the build skipping TemplateArmor.
- Variant AR/damage comparisons use the base's current winner; S&S base armour winner is a third-party patch (Trad), not an
  ARR file, so ARR may have deliberately left variants to it. No check that this third-party patch also covers variants
  (it does not: variants remain at AR 15, source winner).
- Requiem-ecosystem sources and sources starting with "Requiem" were excluded as asked; 315 of 316 cases remained, 25+ covered.
