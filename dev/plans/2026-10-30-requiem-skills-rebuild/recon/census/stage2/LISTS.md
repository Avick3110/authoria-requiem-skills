# LISTS census: leveled lists, outfits, form lists

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** LeveledItem, LeveledNpc, LeveledSpell, Outfit, FormList. 2,086 records / 23,026 ledger rows (LVLI 20,003,
LVLN 2,519, LVSP 164, OTFT 268, FLST 72) / 32 plugins. Held-out: 4 plugins, LVLI only (Favor Quests Seperated 286 rows,
Mage Core 192, Falkreath Shades 10, The Rot Below 2). No held-out rows in LVLN / LVSP / OTFT / FLST. Bookkeeping, reorder
and presentation rows (460) are script-labelled noise. **The Bruma master patch is 82 % of the rows** (LVLI 16,643,
LVLN 2,134). Every count below that says "non-Bruma" excludes it, and every convention is built without the held-out rows.

Method: consensus families + verifier verdicts, re-derived per plugin from the ledger (row level), plus 5 live reads
(vanilla loot sublist identities, Requiem's vanilla DeathItem overrides, Requiem's vanilla LChar overrides, outfit piece
identities). Where my ledger counts correct a verifier claim, I say so.

## Conventions

| # | Convention | Class | Layer | Support rows / plugins | Spread | Held-out | Conf. |
|---|---|---|---|---|---|---|---|
| 1 | Level ladders in LVLI / LVLN / LVSP are flattened: every entry an ARR patch writes is **Level 1** | Requiem-aligned (FACTS §D: Requiem flattens 1,086 vanilla LVLI, 158/200 gated LVLN, 8/9 LVSP) | input | LVLI 6,195 same-ref rewrites / 6 plugins; LVLN 919 / 4; LVSP 40 / 4. Added entries with Level > 1: **0 of 11,131, in all 20 plugins that add entries** | Bruma 5,916 LVLI; non-Bruma Inigo 145, LOTD 109, Apex Khajiit 20, Bannermyst 3, Apocalypse 2; LVLN AFDI 37, TOK 20 | Mage Core 96/96, Favor Quests 13 added entries, Falkreath 2: all Level 1. **Pass** | high |
| 2 | Flattening keeps the entry set and counts when refs are distinct (level-only rewrite) | Requiem-aligned (Requiem keeps tiers: LCharDwarvenCenturion 10FCE5 1/26/32 -> 1/1/1) | input | LVLI 5,987 count-same pairs / 6 plugins | Bruma 5,822, LOTD 105, Inigo 35, Apex 20, Bannermyst 3, Apocalypse 2 | Mage Core 96 count-same. **Pass** | high |
| 3 | A ladder of the **same ref** at several levels / counts (gold, arrows, ingredients) collapses to **one** Level-1 entry at a mid-to-high rung value | house convention (no Requiem check) | input | Bruma 40 lists dedupe-only; LOTD arrows 236CB3 10..50 -> 1 x 40; Inigo 1 | 3 plugins non-HO; Bruma-dominated | Favor Quests 13/13 lists (21 gold rungs 100..2500 -> one x 500); Falkreath 2 lists (x2 + x1 -> x1). **Pass** | med |
| 4 | LVLN ladders of distinct actors may be **trimmed** as well as flattened: Bruma keeps the top rungs (usually 3); Dac0da keeps one low-mid rung | Requiem-aligned in shape (Requiem shrinks 96/193 vanilla LChar* lists, e.g. LCharFalmerMelee 5 -> 1 vanilla tier actor 023A9D, bandit lists 12 -> 1 REQ actor); the tier choice is house | input | Bruma 58 lists drop the low rungs (CYRLCharGoblinBossMelee 0882A8 6 -> 3: L38/46/54 kept); Dac0da 33 lists 6..12 -> 1 (zDcdLCharSload 00306A:DAc0da.esm keeps the L7 rung) | 2 plugins, two different rules | none (no HO LVLN) | low |
| 5 | LVSP spell ladders in mod patches collapse to the **top** tier(s) at Level 1 | house convention (Requiem keeps all tiers: LSpellWEBattlemageFireLeftHand 105101 1/5/20 -> 1/1/1, 3 entries) | input | Glenmoril 5 lists, Vigilant 6 dropped-low + 4 mixed, LOTD 3 (2 keep the *lower*) | 3 plugins; Bruma keeps tiers | none | low |
| 6 | Entries pointing at a NULL-ed item (REQ_NULL_ / NULL_ / AUTH_NULL_) are swapped for the canonical Requiem or vanilla item, same count | Requiem-aligned for the Requiem piece (REQ_Outfit_Vigilant 0B3290 carries 39C006/39C007) | input | LVLI LOTD 19 REQ_NULL_Armor*DBM -> REQ_ Imperial/Stormcloak; Bruma AUTH_NULL_ pelts; TOK gold -> C.O.I.N.; OTFT Vigilant 25 outfits -> 39C006-9:Requiem.esp, Bruma 4 (Akatosh amulet 0C8911), Rogue Stalker 1; FLST LOTD 8, Bruma 1 | 6 plugins, 3 types | none | high |
| 7 | An entry is removed when the same patch disabled what it gives (NULL spell -> its tome leaves loot) | house convention (input clean-up with the disable, FACTS §B) | input | 16 tomes: Dac0da NULL_zDcdConjure* 15, DG Arsenal HolyGround 1 | 2 plugins | none | high |
| 8 | Creature death-item lists drop vanilla generic loot sublists and keep the creature's own drop (often count -> 1) | Requiem-aligned for LootSmallTreasure10 (Requiem removes 0F961F from >= 23/40 vanilla DeathItem* it overrides; DeathItemDraugr loses DeathItemDraugrGold); the wider strip is house | input | ~260 lists (EditorID contains DeathItem) with a net vanilla-sublist removal: Glenmoril 121, Vigilant 84, Unslaad 28, Bruma 16, DAc0da 9, Moonpath 2; 527 sublist removals | 6 plugins; Glenmoril + Vigilant + Unslaad carry 495 / 527. DAc0da is minor (its 38 remove-only lists are mostly tome culls, #7) | Falkreath / Rot Below death items: no generic-loot case to test | med |
| 9 | Spell tomes that teach **live** spells are culled from loot (tome kept, reverse refs 0) | house convention **or** suspected systemic bug: Aaron call | input | 29 tomes: Vigilant 10, TOK 10 (race shapeshift, 42E81C 10 -> 2), Unslaad 8, Glenmoril 1 | 4 plugins | Rot Below: both rows remove a Conjure tome (65E3B7, 65E3B0) from boss death items. **Follows** | med |
| 10 | A list is emptied when everything it gave is removed | Requiem-aligned (Requiem.esp empties 14 vanilla LVLI, e.g. DeathItemDraugrGold 04F78C) | input | 7 rows / 3 plugins (Dac0da 4, Bruma 2, Farmgees 1) | thin | Rot Below 2 lists emptied. **Pass** | med |
| 11 | ChanceNone is retuned per list, mostly **up** (rarer drops) | house convention (no rule; no code writes it) | input | 27 non-HO rows / 6 plugins; 23 / 27 up (25 / 29 overall) | Bruma 15, LOTD 6 (soul gems), Glenmoril 3 | Falkreath 0.3 -> 0.5 x 2. **Follows direction** | low |
| 12 | Flags: dropping CalculateFromAllLevels after flattening is a moot side-effect | noise | input | Bruma 1 (0B5508) | — | — | med |
| 13 | Outfits re-pointed to a sibling variant set of the same mod (`_Cl` clothing variants carrying MagicDisallowEnchanting 0C27BD) | house convention (fact 26 is multi-judge, not live) | input | Wine Duchess 3 outfits / 28 items; Rogue Stalker NULL_RoSt_* -> _RoSt_*_Cl; LOTD DBM_ShadowHunterOutfit 4 | 3 plugins | none | low |
| 14 | Leveled lists are never baked: the build does not touch a mod's LVLI / LVLN (merge off; RFI LVLI = Requiem / WAR / VampireCollection only) | baked: none (code, FACTS facts 1 + 21) | — | whole domain | — | — | high |
| 15 | Auri's outfit 018AuriOutfit 08C10A goes from clothing (REQ_Cloth_Prisoner_Feet, REQ_Cloth_Wench_Body) to REQ_Light_Hide_Feet / Body | house convention (Aaron confirms) | input | 4 rows / 1 plugin | Auri only | none | low |

Corrections to the consensus: (a) Favor Quests and Mage Core are **held-out**; the verifier used them as non-Bruma
support for #1. Without them, non-Bruma flatten support is 279 LVLI rows / 5 plugins (still holds). (b) Vigilant LVSP is
**not** a plain flatten: 6 of its 11 lists drop low tiers (zzzCHLSpellFleshLeftHand 0C3A55 4 -> 1, Ebonyflesh at L1;
zzzCHLSpellFireLeftHand 0BE075 5 -> 2). (c) LVLN is not only Bruma + AFDI: Dac0da has 33 collapse-to-one lists (264 rows),
and Bruma trims 58 lists, so "the list keeps the same entry count" is wrong for ~90 lists. (d) The death-item strip (#8)
is the main pure-removal driver for Glenmoril / Vigilant / Unslaad (DAc0da's removals are mostly tome culls, #7) and was
not named by the reader.

## Upstream checklist (what a patch must carry)

- [ ] Every list entry the patch writes is Level 1 (#1); a mod ladder of distinct items keeps all its refs (#2).
- [ ] Same-ref rungs (gold, arrows) become one entry; pick a mid rung (#3; an Aaron call fixes the rule).
- [ ] Every NULL-ed item the patch creates is swapped out of every list / outfit / form list that names it, for the
      Requiem piece where Requiem has one (#6), or removed with its tome / recipe (#7). Reverse-ref the NULL target.
- [ ] Creature death items: drop LootSmallTreasure10 at least (Requiem does); the wider vanilla-sublist strip is house (#8).
- [ ] Compare against the **live winner** of a vanilla list, not Requiem.esp-defined lists only (FACTS §D).
- [ ] An emptied list is fine (#10); an emptied **form list** needs its consumer read first (Wet and Cold, below).
- [ ] Do not assume LVSP ladders collapse: Requiem keeps tiers (#5 is unsettled).

## Downstream bake package

**Empty.** The Reqtificator never writes a mod's LVLI / LVLN / LVSP / OTFT / FLST under ARR settings (FACTS fact 1, fact 21:
mergeLeveledLists and mergeLeveledCharacters off). Everything in this domain is input. Nothing ARR writes here
duplicates build output.

## Per type

**LeveledItem (1,780 records, 24 plugins).** Bruma: 1,209 lists, 5,916 flatten rewrites, 2,364 pure adds (own-mod items
into its CYR lists), 1,338 pure removals, 82 lists shrink (40 same-ref dedupe, the rest item swaps). Glenmoril / Vigilant /
Unslaad / DAc0da: almost all pure removals (153 / 101 / 29 / 38 remove-only lists): death-item strip (#8; mainly the first
three), tome cull (#9), disable fallout (#7; most of DAc0da's). LOTD: 400 pure adds, mostly its books / gems into vanilla LItemBook* / LItemGems* at Level 1 (lead:
probably carrying LOTD's injections over Requiem's winners; not read). Inigo: gold ladders flattened, counts raised
(97 up). Count changes on kept refs: up 290, down 345 (Favor Quests 190 of the downs). Not systematic outside Bruma:
Glenmoril.esm 21 and Vigilant.esm 3 ladders the patches leave laddered (verifier).

**LeveledNpc (226 records, 7 plugins).** Bruma 175 lists: 829 flatten rewrites, 58 trimmed to the top rungs. Dac0da 38:
33 collapse to one actor. AFDI 6 and TOK 4: plain flatten. Glenmoril defines EchoLevelled (unread).

**LeveledSpell (35 records, 4 plugins).** Bruma keeps every tier (CYRLSpellWEBattlemageFireLeftHand 300CAA, 3 entries at L1:
matches Requiem). Glenmoril / Vigilant / LOTD collapse (#5). LOTD DBM_MAASE_LeveledSpell_ICE gets a plain add (02B96C).

**Outfit (36 records, 7 plugins).** Vigilant 25 outfits: NULL Vigilant armour -> Requiem's own REQ_Heavy_Vigilant_*
(Requiem-aligned). Wine Duchess / Rogue Stalker: own `_Cl` variants (#13). Auri 018AuriOutfit 08C10A: REQ_Cloth_Prisoner_Feet
+ REQ_Cloth_Wench_Body -> REQ_Light_Hide_Feet / Body (one follower upgraded from clothing to armour; one plugin: Aaron call).
Glenmoril defines NightingaleOutfitNoHood (unread).

**FormList (9 records, 7 plugins).** Wet and Cold empties _WetBPLeatherList 05C8E0 / _WetBPLeatherFarList 05E3D5 (27 -> 0;
the backpack ARMO stay and gain AD3AEC + AD3B2B). LOTD drops REQ_NULL_ shields from display lists (#6). Small swaps:
EEKs EEKsWarmaidensSSEForms 12F2B1 (2 for 2), Vigilant DBM_SectionHOOVigilantItems (1 for 1), Frozen Heart
ksws07ArmorWeapons 00246E (-1). CC Survival adds two vanilla fire statics to Survival_WarmUpObjectsList. All unclear, 1-2
rows each.

## Suspected bugs (for Aaron's review, not filed)

| Record + plugin | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|
| 4528C8:Glenmoril.esm zzzGHDeathItemM06Lurcher, 4528CB ...LurcherAncient (Reqtificated - Glenmoril) | Flags, Entries | UseAll -> CalcFromAllLevels + ForEachItem; 060038:Skyrim + own item removed (4 -> 2) | give-all death item kept, or the same entries | verifier tree read 4528C8; ledger | outlier-bug |
| 0CCC70..0CCC7x:DBM_CC_HallOfWonders.esp DBM_CC_LItemCuratorReward050+ (Reqtificated - LOTD) | Entries | LOTD artifact weapons (125A1B, 1259F2, ...) -> gold 500 / 750 / 1,000 / 1,250 ... | artifact kept (nothing NULL-ed it) | ledger; verifier: Trueflame, Thornblade, Stendarr's Hammer | suspected (deliberate?) |
| (= convention #9, not counted twice) 29 live-spell tomes (Vigilant zzzCHConjure* 10, TOK race tomes 10, Unslaad 8, Glenmoril 1; held-out CJ03 2) | loot entries | removed; reverse-ref count 0 | tome stays obtainable, or its spell is disabled too | verifier read all 45 removed BOOK; 16 others follow a NULL | suspected-bug (systemic) |
| (= convention #5, not counted twice) 01125D:Glenmoril.esm zzzGHLSpellWitchBloodBall (+01125E, 01255E); 0C3A55:Vigilant.esm zzzCHLSpellFleshLeftHand | Entries | 7 tiers L1..L48 -> top spell 01254D at L1; 4 tiers -> Ebonyflesh 072318 at L1 | tiers flattened and kept (Requiem 105101) | ledger; verifier live read 01125D | suspected-bug (systemic) |
| 00085E:Bannermist.esp (the master's own filename; patch is Reqtificated - Bannermyst) LItemEnchWeaponSwordDUPLICATE051 | Entries | 12-rung enchanted-sword ladder (to 000F23 at L47) -> one 04B583 at L1 | all refs at L1 (#2), or Requiem's winner of the vanilla original | ledger; vanilla original not read | outlier-bug |
| 05C8E0, 05E3D5:WetandCold.esp (Reqtificated - Wet and Cold) | Items | 27 -> 0 | backpack pool kept, unless backpacks are meant off | verifier tree; consumer quest _WetQuest 000D63 script unread | suspected-bug |
| 1A6A02:LegacyoftheDragonborn.esm DBM_ShadowHunterOutfit (Reqtificated - LOTD) | Items | 4 Kynreeve pieces swapped (1A18F8 NonPlayable light torso AR 201.3 out; 1A18EE heavy boots AR 180 in) | same slots, same armour type | live read 1A18F8 / 1A18EE | outlier-bug (low) |

## Open calls for Aaron

1. Same-ref ladders (#3): which value is the rule? Favor Quests take the L30 rung (500 of 100..2,500), LOTD arrows the
   L40 rung (40 of 10..50).
2. LVLN trimming (#4): Bruma keeps the top 3 rungs, Dac0da one low-mid actor. Is either the rule, or should a patch keep
   every tier, as Requiem does for the Dwarven lists?
3. LVSP collapse to the top spell (#5, Glenmoril / Vigilant): intended, so low-level casters get the top spell?
4. Tome cull of live spells (#9): deliberate content cut, or a leftover? The tomes become console-only.
5. Death-item strip beyond LootSmallTreasure10 (#8): house rule for all creature mods, or only Glenmoril / Vigilant / Unslaad?
6. LOTD curator rewards -> gold; Glenmoril Lurcher give-all -> roll; Wet and Cold backpack lists emptied.
7. Auri's outfit upgraded to hide armour: one-off follower decision?

## Limits

- Unsampled / unread: LVLN EchoLevelled (Glenmoril), OTFT NightingaleOutfitNoHood (Glenmoril), LOTD's 400 vanilla-list adds,
  the vanilla original of Bannermyst DUPLICATE051, the Wet and Cold consumer script, which tier rule Dac0da follows.
- #3, #11, #13 rest on ledger counts with no Requiem comparison. #4's Requiem count covers LChar*-prefixed lists only
  (200 read, 193 compared). #8's Requiem count is a lower bound (40 of 49 DeathItem rows read, deltas truncated).
- Untouched records (see `UNTOUCHED.md`): BSHeartland 1,180 LVLI and 258 LVLN, Glenmoril 402 LVLI, Vigilant / BSHeartland
  outfits are not overridden by any ARR patch and were judged from names only. Glenmoril.esm 21 / Vigilant.esm 3 ladders
  stay laddered.
- Held-out validation covers LVLI only (4 plugins, 490 rows); LVLN / LVSP / OTFT / FLST conventions have no held-out test.
- ksws03_quest.esp fails to load (order_degraded on every call, this session's reads included). The ksws03 patch has no
  list records; any winner read touching records that plugin overrides is degraded.
