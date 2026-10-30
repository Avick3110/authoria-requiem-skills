# Census: WEAP (Weapon) conventions in ARR's handmade Requiem patches

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 2,611 distinct WEAP FormIDs, 18,431 changed-field rows across 65 plugins (stage-1 consensus `Weapon.json`
+ ledger). Held out (eval references touching WEAP): `Authoria - Reqtificated - Falkreath Shades.esp`,
`... - The Rot Below.esp`, `... - Triumvate.esp`. Heaviest: Glenmoril 4,284, LOTD 3,340, Vigilant 3,269,
Unslaad 1,683, Dac0da 1,241, MR Praedy's Staves 1,160. Script-decided rows: 4,637 (presentation 1,924, build-assigned
keywords 1,436, Weight = comparable 699, Value = comparable 201, reorders / bookkeeping / re-saves 305, Damage =
comparable 50, build-recognised keywords 17, empty-v-absent 5). 7 live reads this session (cited inline as *live*).

## 1. Conventions

Held-out column: rows the 3 held-out plugins have in that family / rule. A single-pattern family with held-out rows
means the held-out patches make the same change (same pattern); multi-pattern families can't be split per pattern
from the consensus, so they say "in family".

| # | Convention | Class | Input / baked | Support | Held-out | Conf. |
|---|---|---|---|---|---|---|
| 1 | Hand-rescale every touched weapon and add the **No\*Rescale triple** AD3B2D / AD3B2E / AD3B2F (Requiem.esp) | baked (code: `StaticReferences.cs`, `WeaponFullExclusionSkip` needs all 3) | baked | AD3B2D 1,693 / AD3B2F 1,685 / AD3B2E 1,653 rows; ~60 plugins | 202 held-out rows in family | high |
| 2 | Damage = **Requiem comparable's base damage x6** (melee), x4 bows / crossbows; staves → 1 | baked (code: `WeaponDamageScaling.cs`); the comparable pick is input | baked (value) on input (comparable) | 1,751 rows / 51 plugins, 1 pattern; ~6.0 in 19/25 off-comparable samples; 453 rows end at 1 | 52 → follow | high |
| 3 | Critical.Damage := Damage | baked, **redundant** (build sets it regardless, ignores AlreadyReqtified / AD3B2D) | baked | 1,605 rows / 54 plugins, 1 pattern | 52 → follow | high |
| 4 | Reach = **comparable's class reach** (1.3 / 1.0 / 0.7) x0.7 → 0.91 / 0.7 / 0.49 on 1,278 / 1,387 rows (92 %); ~109 rows are source reach x0.7 | x0.7 baked (code: flat `Reach *= 0.7` in `WeaponMeleeRangeScaling.cs`, no snapping); class value = input (comparable) | baked (factor) on input (class reach) | 1,387 rows / 49 plugins, 1 pattern | 23 → follow | high |
| 5 | Write the build-assigned keywords too (DamageType AD395x etc.) | baked (`keywords_assign` configs) | baked | 1,436 rows / 48 plugins (script rule) | 22 → follow | high |
| 6 | Weight = Requiem comparable | Requiem-aligned | input | 699 rows / 25 plugins (script rule) | 6 → follow | high |
| 7 | Value = Requiem comparable *where one matched*; otherwise rounded tier values (100 / 250 / 500 / 1000 / 1500 / 2500) | Requiem-aligned (201) / **house?** (553, Aaron call) | input | 201 rule rows; 553 family rows / 22 plugins, 370 off comparable | 4 rule + 31 family → follow | med / low |
| 8 | Speed set to Requiem class speeds (1.0 1H sword, 0.75 greatsword, 0.7, 0.6 ...) | values Requiem-aligned (*live*: REQ_Weapon_Silver_Sword 1, _Greatsword 0.75); verifier class "possibly" | input (melee); bows partly baked (0.3704 x17 = build value; 0.4 x20 and 0.4445 x4: ARR's crossbow / rifle value is mostly 0.4, not the build's 0.4445) | 406 rows / 25 plugins, 1 pattern | 4 → follow | med |
| 9 | Swap material / type keywords to the right vanilla / Requiem ones (Requiem bow / crossbow tier keywords 9F9914 BowHeavy, 9F9915 BowLight, 899DBE CrossbowHeavy; 2E232E REQ_BowBreakable) | Requiem-aligned | input | long tail of 77 add + 74 remove patterns; 253 removal rows / 27 plugins | in family (202 add, 1 remove) | med |
| 10 | Add **MagicDisallowEnchanting 0C27BD** to uniques / artifacts | Requiem-aligned (fact 26 shape) | input | 166 rows | in family | med |
| 11 | Staves get MR frame keywords `Nox_KW_Staff_<School><Tier>` (e.g. 0076ED Destruction3) | Requiem-aligned (MR, fact 30) | input | 17+ rows per keyword (MR Praedy's, Apocalypse, LOTD, Falkreath Shades) | held-out plugin listed on 0076ED | med |
| 12 | Strip **SilverSwordScript** from silver weapons (keep WeapMaterialSilver 10AA1A) | Requiem-aligned (*live*: Requiem's REQ_Weapon_Silver_Sword / _Greatsword have no VMAD) | input | 45 + 7 + 4 + ... rows (Vigilant, Unslaad, LOTD, JS Radiance) | 0 | high |
| 13 | Strip other source-mod weapon scripts (CHGiantWeaponScript, WuuthradScript, DLC1DawnguardItemScript, dbm_addspellorperkonitemadded); drop the VMAD when empty | **unclear** (Aaron call) | input | 352 removal rows / 13 plugins; 175 whole-VMAD removals / 9 plugins | 0 | low |
| 14 | NPCsUseAmmo on bows / crossbows (incl. NonPlayable / Bound) | baked for playable (fact 9: build adds it, playable only); **input** for NonPlayable | mixed | 77 additions: patterns 315 (56), 837 (10, Glenmoril, replaces anim flags), 921 (8), 1879 (2), ... | 1 in family | med |
| 15 | Clear DontUseFirstPerson/ThirdPersonIsAnim flags | unclear (unsampled lead) | input | 110 rows (Glenmoril, Apocalypse, Gray Cowl) | in family | low |
| 16 | Enchant charge **normalised to 3000** on uniques (57 / 237 rows: Vigilant 10000 / 7500 / 5000 → 3000, LOTD 5000 → 3000, Unslaad 100 → 3000; *live* LOTD 161D3A = 3000, Vigilant 00A962 8000 → 3000); other targets 1500 (15), 2000 (14), Glenmoril +50..+500 steps, Dawnguard Arsenal 2000 x5 / 4000 x7; 72 rows → absent; mod enchants swapped to Requiem / MR / vanilla ones | house? (Aaron call) | input | 237 rows / 20 plugins; ObjectEffect 93 rows / 15 plugins | 6 + 1 | low |
| 17 | Stagger normalised to Requiem-like values | Requiem-aligned? (unsampled) | input | 112 rows / 7 plugins | 2 → follow | low |
| 18 | Enchanted-variant weapons keep `Template` → base REQ_Weapon_* (Requiem's own shape) | Requiem-aligned (CC Fishing sets it) | input | 6 rows / 3 plugins | 0 | med |

Noise (don't teach): presentation 1,924; reorders / re-saves / bookkeeping; VMAD ObjectFormat / Version echoes;
Critical.Unused*, Data.Unknown2, rumble fields (whole-struct copies); dbm_ancienttonguescript "removal" (pattern 1110,
property order only).

## 2. Upstream checklist (what a patch must carry as input), in order

1. **Pick the Requiem comparable** per weapon (same type + material tier). Everything numeric keys off it.
2. **Keywords:** WeapType\* + WeapMaterial\* correct for the weapon (swap a wrong vanilla material keyword rather than
   add a second); Requiem tier keywords on bows / crossbows (BowHeavy / BowLight / CrossbowHeavy, REQ_BowBreakable
   where the comparable has it); MR `Nox_KW_Staff_*` on staves; MagicDisallowEnchanting on uniques that must not be
   disenchanted.
3. **Weight** = comparable; **Value** = comparable (or the tier table, pending Aaron, convention 7).
4. **Speed / Stagger** = Requiem class values (melee). Base Damage and class Reach come from the comparable —
   *pre-rescale* (what the build expects as input); ARR itself writes them post-rescale (§3).
5. **Scripts:** drop SilverSwordScript (Requiem handles silver by keyword); keep other source scripts unless Aaron
   rules otherwise (convention 13).
6. **Enchantment:** a Requiem / MR / vanilla enchant where the mod's is out of band; set a charge consistent with
   Requiem uniques (live: REQ_Ench_Weapon_AncientNordHoned_Greatsword_Fire3 carries 1500). Don't strip a charge while
   keeping the enchant (see bug B3).
7. **Template:** an enchanted variant keeps / gets `Template` → its base weapon; never clear Requiem's (bug B2).
8. **NonPlayable bows / crossbows:** add NPCsUseAmmo by hand (the build adds it on playable ones only).

## 3. Downstream bake package (what ARR writes in that the build would otherwise produce)

ARR does not leave weapons to the Reqtificator; it writes the build's output and opts out. For a rebuilt skill to
reproduce ARR's patches it must write, as a unit:

1. Damage = comparable x6 (melee / H2H) or x4 (bow / crossbow); staves 1.
2. Critical.Damage = that Damage (redundant: the build re-sets crit anyway).
3. Reach = reach x0.7 (melee; the code factor is flat). ARR applies it to the comparable's class reach (1.3 → 0.91,
   1.0 → 0.7, 0.7 → 0.49) on 92 % of rows, so the class reach is an input choice, the x0.7 the bake.
4. Bows / crossbows: Speed 0.3704 / 0.4445 + WeapBowHeavy / CrossbowHeavy, NPCsUseAmmo (fact 9). The build skips
   NonPlayable bows for speed and ammo. ARR: 0.3704 x17 matches; crossbows / rifles mostly 0.4 (x20), not 0.4445 (x4).

The build also skips damage and crit when `Template` is set, and damage / reach / speed / ammo when the weapon carries
AlreadyReqtified.
5. DamageType keyword (AD395x) and other `keywords_assign` keywords.
6. **All three** of AD3B2D NoDamageRescaling, AD3B2E NoWeaponReachRescaling, AD3B2F NoWeaponSpeedRescaling. With only
   one or two the build re-applies the missing steps (fact 10: AD3B2D alone → reach shrinks again).

The bake is all-or-nothing per weapon: hand values without the triple are rescaled a second time; the triple without
hand values ships vanilla-scale weapons.

## 4. Per field / family

**Keywords added** (5,686 rows / 63 plugins / 77 patterns; verifier *fixed*, material error: the reader called the
triple input). Triple counts are near-equal (1,693 / 1,685 / 1,653); the AD3B2E shortfall vs AD3B2D is consistent with
weapons whose reach isn't touched, and AD3B2E on bows is harmless. Long tail = material / type swaps: 01E719 Steel 44,
01E71E Ebony 44, 01E71D Glass 26, 01E71F Daedric 23, 10AA1A Silver 30, plus Requiem bow / crossbow tiers. *Live*:
Requiem's own silver sword carries both WeapMaterialSilver and WeapMaterialSteel, so a silver weapon with two material
keywords is Requiem's shape, not a duplicate.

**Keywords removed** (253 / 27 / 74; *cannot-tell*). Mostly vanilla material swaps (01E719 Steel 45, 01E71B Elven 25,
01E718 Iron 15, 01E71E Ebony 12) and type fixes (01E711 WeapTypeSword, 01E714 WeapTypeMace, 06D930 WeapTypeWarhammer
on the Glenmoril reclass). Pattern 489 (25 rows, Vigilant only) removes **0C5C00 WeapMaterialImperial** (*live* id):
plausible swap (Mara / Zenithar silver-themed weapons), unverified whether a material is re-added. 936 removes
0C5C01 WeapMaterialDraugr from Apocalypse / Unslaad conjured weapons (summon weapons; no temper / material role).

**Damage** (verifier *fixed*: reader said Requiem-aligned input; it is baked). The 395 "off comparable" rows are x6 of
a *different* comparable, not off-rule: Stendarr dagger 03D213 10 → 54 = comparable 9 x6. So the input is the
comparable's damage, not the source's. Exceptions: Lucien 2E447A (NonPlayable NPC sword) 20 → 80 (x4 on a 1H sword;
NPC-only weapon, possibly hand-tuned, a lead).

**Critical.Damage**: one pattern, values mirror Damage. Redundant bake; harmless.

**Reach**: one pattern. The code is a flat `Reach *= 0.7` with no snapping; the class values come from the author using
the comparable's reach (Requiem pre-rescale: greatsword 1.3, sword 1.0, dagger 0.7). 0.91 x585, 0.7 x574, 0.49 x119
= 1,278 / 1,387 (92 %): 1.4 / 1.5 / 1.6 → 0.91 (Vigilant greatsword 11CCBF, *live*), 0.8 → 0.49. The other ~109 rows
are source x0.7 (1.05 x17, 0.77 x14, 0.98 x12, 0.84 x11, 1.4 x6 ...). Candidate reasons for the split: patch age /
author habit (comparable first v source first). Effect of the class value: oversized mod weapons lose their extra
reach. Aaron call.

**Speed** (verifier *fixed* class to "possibly"; melee values are not build-written, so input). Top values match
Requiem class speeds (*live*). One outlier: B1.

**Value / Weight** (*agree*, not re-derived). Rule rows where the comparable matched are Requiem-aligned; the family
residue (Value 553 with 370 off, Weight 330 with 163 off) diverges strongly (Value 1440 → 5000 vs 9000 expected;
Weight 8 → 19 vs 24–40 for Molag great-spikes). Candidate reasons: (a) a deliberate house tier table for uniques,
(b) comparable mis-pick by the script (artifact vs base comparable), (c) author habit. The rounded values point to (a).

**Scripts** (verifier *fixed* on removed; *unsampled* on VMAD echoes). Real removals: SilverSwordScript (Requiem-aligned,
*live*), CHGiantWeaponScript, WuuthradScript, DLC1DawnguardItemScript, dbm_addspellorperkonitemadded (LOTD Winterhold
staves 161D3A–: loses spell-on-add behaviour; they still carry the triple although the build never scales staves). CHAddWeaponSkillScript is kept where checked (03D783), so this is not a
blanket strip. Scripts **added** (63 rows / 10 plugins, *cannot-tell*): source scripts carried forward onto overrides
(Dawnfang / Duskfang drain, LOTD) — consistent with "forward the source's script unless Requiem replaces it".
Candidate reasons for the giant / Wuuthrad removals: Requiem-mechanic overlap (Wuuthrad's elf-damage is a vanilla
artifact script; CHGiantWeaponScript probably a stagger / size effect) or rebuild side-effect. Aaron call.

**Data.Flags** (*unsampled*). NPCsUseAmmo additions fit fact 9 (redundant on playable, needed on NonPlayable / Bound —
patterns 921, 1879; 837 swaps the anim flags for NPCsUseAmmo on 10 Glenmoril bows / rifles). Clearing DontUse\*IsAnim (243, 110 rows) has no Requiem reference read. 851 sets PlayerOnly on
LOTD DUMMY display weapons (deliberate). 5218 clears IgnoresNormalWeaponResistance on a LOTD replica (lead).

**EnchantmentAmount / ObjectEffect** (family *unsampled*; doc verifier counted the ledger). 237 rows: **57 → 3000**
(Vigilant 10000 / 7500 / 5000, LOTD 5000, Unslaad 100; *live*: LOTD 161D3A = 3000, Vigilant 00A962 8000 → 3000),
15 → 1500, 14 → 2000, Glenmoril +50..+500 steps, Dawnguard Arsenal Super 2000 x5 / 4000 x7 (+ enchant 05B46F,
pattern 790), 72 → absent. The 3000 normalisation is the main convention (house?, Aaron call). Of the 72 absent:
(a) un-enchanting, enchant removed too (Unslaad Frozen katana 4B1F40, *live*) — coherent; (b) 55 keep the enchant with
no charge — e.g. Vigilant 0C74EE Mara Flame greatsword, playable, keeps zzzCHEnchFlameSword (*live*) — B3.
Note: the doc verifier's ledger shows no 8000 before-value; the live tree on 00A962 shows Vigilant.esm 8000 →
patch 3000, so it is kept here on the live read.

**Template** (verifier *fixed*). CC Fishing sets Template on REQ_Ench_ records to the base REQ_Weapon_ (Requiem's
shape); LOTD clears Requiem's Template on 0F494C — B2. Glenmoril re-templates the Benthic warhammer enchanted variants
to the sword base (one consistent act with AnimationType / Skill / EquipmentType / BlockBash).

**Reclassification** (AnimationType / Skill / EquipmentType, 4–5 rows): Glenmoril Fishman Warhammers 5294B6 / B7 now
1H swords (name still says warhammer); LOTD Ghorbash axe 1H → 2H; Val Serano scythe 1H sword → 2H axe. Mod-specific
class fixes, not a convention.

## 5. Suspected bugs (for Aaron's review; not filed)

| # | Record (FormID, plugin) | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| B1 | zzzAoMVigilantGreatsword `11CCBF:Vigilant.esm`, Authoria - Reqtificated - Vigilant.esp | Data.Speed | 9.75 | 0.75 (Requiem greatsword class; source 0.7) | *live* delta vs Vigilant.esm 0.7; Requiem REQ_Weapon_Silver_Greatsword 0.75; only playable weapon in the order > 5 | outlier bug (typo) |
| B2 | REQ_Ench_Weapon_AncientNordHoned_Greatsword_Fire3 `0F494C:Skyrim.esm`, Authoria - Reqtificated - LOTD.esp | Template | absent | 05BF13 (Requiem.esp and Skyrim.esm both carry it) | *live* tree; verifier family Template 4987. Winner has Damage 20, no No*Rescale triple, charge 1500: with Template cleared the build no longer skips it and x6 → 120. Patch also forwards LOTD's model / bounds (whole LOTD version) | outlier bug (LOTD version forwarded over Requiem's) |
| B3 | zzzCHMaraGreatSwordFlamePlayable `0C74EE:Vigilant.esm`, Authoria - Reqtificated - Vigilant.esp (+ ~22 of pattern 455's 28 rows, LOTD / Unslaad / Vigilant: only 5 drop the ObjectEffect, 1 swaps it; 55 of all 72 charge-absent rows keep the enchant) | EnchantmentAmount | absent (source 5000), enchant kept | a charge (Requiem unique: 1500 on 0F494C) or the enchant removed too | *live*: ObjectEffect zzzCHEnchFlameSword present, charge absent; in-game behaviour of a 0-charge enchanted weapon not tested | suspected systemic bug (unsampled family) |
| B4 | LOTD Winterhold staves `161D3A:LegacyoftheDragonborn.esm` (+ 4, pattern 1265), Authoria - Reqtificated - LOTD.esp | VMAD | dbm_addspellorperkonitemadded removed | kept (spell-on-add feature) | verifier live read 1 → 0 | suspected bug, intent unclear |
| B5 | Vigilant pattern 489 (25 rows, e.g. `0C74EE`, `0E04BC`, `12F208:Vigilant.esm`) | Keywords | WeapMaterialImperial 0C5C00 removed | a replacement material keyword | unverified (not re-read by either verifier) whether one is added; temper / perk keyed by material | lead, unverified |
| B6 | JRmihailinvhungerstrike `2E447A:Lucien.esp`, Authoria - Reqtificated - Lucien.esp | Damage | 80 (source 20, x4) | x6 = 120 for a 1H sword | *live* delta; NonPlayable NPC weapon (Speed 5 is the source's) | lead, unverified / possibly deliberate |

## 6. Open calls for Aaron

1. **Bake v input for weapons.** ARR bakes the whole weapon rescale (damage, crit, reach, keywords) and opts out with
   the triple. Should the rebuilt skill teach the bake (reproduce ARR) or input-only (comparable values, let the
   build rescale)? This choice decides §2 v §3 for every weapon.
2. **Value tier table** (553 rows): a deliberate house table for uniques, or comparable-derived?
3. **Script strips** beyond SilverSwordScript (CHGiantWeaponScript, WuuthradScript, DLC1DawnguardItemScript, dbm_*):
   deliberate or rebuild side-effect?
4. **Reach from the comparable's class reach** x0.7 (92 %; oversized mod blades lose reach) v source x0.7 (8 %): which
   is the convention?
5. **Enchant charges**: is 3000 the house value for uniques (57 rows), and what sets 1500 / 2000 / Dawnguard 4000; is "absent with enchant
   kept" (B3) ever intended?
6. **DontUse\*IsAnim clearing** (110 rows): deliberate?
7. **Glenmoril Fishman Warhammer → 1H sword**: intended (name mismatch)?
8. B1–B6 confirmations.

## 7. Limits

- Only records ARR's patches touch; untouched mod weapons (left to the build) are invisible here, so "ARR always bakes"
  means "always, where it patched".
- Unsampled families (Flags, EnchantmentAmount, Stagger, VMAD echoes, rumble / unused) rest on reader labels; B3 and
  convention 15–17 are leads.
- Multi-pattern families can't be split per pattern for held-out rows; held-out validation is strong only for the
  single-pattern families (Damage, Crit, Reach, Speed, Stagger, Value, Weight).
- Bow speed share is counted (0.3704 x17, 0.4 x20, 0.4445 x4); the WeapBowHeavy / CrossbowHeavy share was not.
- `ksws03_quest.esp` failed to load (live epoch excludes it); its one defined staff row is unread.
- No in-game test: 0-charge enchant behaviour, reach feel, NPCsUseAmmo on NonPlayable.
- Record count isn't in the consensus; figures are rows.
