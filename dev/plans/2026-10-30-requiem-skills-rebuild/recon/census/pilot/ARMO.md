# Census: armour family (ARMO, ARMA, COBJ) + container locks

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus (pilot):** Armor 4 records / 2 plugins (fit: Obi Hedge Knight, 3 records; held-out: Animated Carriages,
1 record, `zxACArmorTsunCuirass` 0009F9:AnimatedCarriage.esm). ArmorAddon 2 records and ConstructibleObject 6 records,
all Obi Hedge Knight (no held-out rows). Container 67 records / 2 plugins (fit: Grand Solitude 14; held-out: Animated
Carriages 53). Sources: `pilot/work/consensus/{Armor,ArmorAddon,ConstructibleObject,Container}.json`,
`pilot/work/annot/items-cont.final.jsonl`, live reads (epoch e2-58972ca8618e79ea).

> **Thin support.** Every armour / recipe rule below rests on **one fit plugin** (Obi Hedge Knight, a 3-piece iron-tier
> heavy set) and is tested on **one held-out record**. Read the rules as format-working hypotheses, not conventions.
> Only the container-lock rule has real held-out weight (53 records).

## Candidate rules

| # | Rule | Class | Layer | Support | Held-out |
|---|---|---|---|---|---|
| A1 | Set ArmorRating to the Requiem comparable (same material tier + slot); a multi-slot piece takes the **sum** of the per-slot comparables | Requiem-aligned (012E49/012E46/012E4B/012E4D) | input | 3 rec / 1 plugin | **0 of 1** (Tsun 180 v comparable 161) |
| A2 | Set Value and Weight the same way (comparable, summed over slots) | Requiem-aligned | input | 3 rec / 1 plugin | 1 of 1, vacuous (already 0/0 = comparable, left untouched) |
| A3 | Every hand-set AR carries `RFTI_Exclusions_NoArmorRatingRescale` (AD3B2B) | House convention / ecosystem practice (Requiem.esp: 0 ARMO; WAR + MR patches: 18 each); redundant when AR is above the rescale threshold, as on all 4 pilot records | input | 3 rec / 1 plugin | **1 of 1** |
| A4 | Swap the mod's set keyword for the comparable's `REQ_ArmorSet_<material>` (remove the old one) | Requiem-aligned (012E49 carries 06BBE3) | input | 3 rec / 1 plugin | n/a (neither Tsun nor 0C0165 has a set keyword; consistent) |
| A5 | A piece covering the hands slot carries the material's `PerkFists<Material>` keyword | Requiem-aligned (012E46 carries 0424EF) | input | 1 rec / 1 plugin | n/a (no hands slot) |
| A6 | Build-stamped keywords (`REQ_Tempering_Craftsmanship` AD3AEC, `REQ_Armor_Resistance_Ranged_Tier5` AD3A25 on cuirasses) are written into the patch | House convention (redundant baking) | baked | 3 rec / 1 plugin | n/a (RFI output stamps nothing on 0C0165; Tsun carries nothing; consistent) |
| C1 | Temper recipe condition = one `HasPerk` on the tier perk (`REQ_Smithing_Craftsmanship` 0CB40D for iron); drop vanilla perk + `EPTemperingItemIsEnchanted` | Requiem-aligned (085DC5) | input | 2 rec / 1 plugin | none |
| C2 | Recipe ingredients = per-piece Requiem counts, summed over the slots a piece covers; drop the mod's off-tier material | Requiem-aligned (0DB291/0DB28F/0DB290) | input | 4 rec / 1 plugin (1 exception) | none |
| C3 | A variant with no recipe gets new forge + temper COBJs copied from the comparable's | Requiem-aligned (0DB292, 0ADB93) | input | 2 rec / 1 plugin | none |
| C4 | The mod's forge recipe is moved to `REQ_DisableRecipe` (AD3B01) | **unclear** (suspected bug or house call, see B1) | input | 2 rec / 1 plugin | none |
| K1 | Every container carries the `REQ_LockpickControl` VMAD (dataStorage AD3B22, FollowerControl 82F3AF) | Requiem-aligned (003E38 RFTI_Prototype_LockedContainer) | baked | 14 rec / 1 plugin | **53 of 53** |

Held-out total: of the rules the Tsun cuirass can test, A3 reproduced, A1 did not; A2/A4/A6 held only vacuously.
K1 reproduced 53/53. No COBJ or ARMA rule has a held-out test.

## Per field

### Armor.ArmorRating (4 rows, pattern 80)
- Obi rows match Requiem iron exactly: suit 00080A (slots 32+33+37) 125 -> 400 = body 250 + hands 75 + feet 75;
  helmets 00080F / 000D6A 17 -> 100 = REQ_Heavy_Iron_Head 012E4D. Verified live.
- Exception: Tsun cuirass 0009F9 29 -> 180. Requiem's own `REQ_Light_Tsun_Body` 0C0165 (same armature TsunCuirassAA
  0C0167, same keywords) = 161, which is also what the build's rescale gives for base 29 (fact 5). Candidate reasons:
  (a) deliberate buff of a unique item (no sign); (b) different patch author / age than Obi; (c) error. AD3B2B does
  not decide it: the build rescales only AR below the threshold (light body 62; fact 5, U6 s3), so 180 and 161 both
  pass untouched with or without the keyword. The 180 is simply hand-set. Outlier pending Aaron.
- Consensus per-row split: ok 3 / conflicts-comparable 1.

### Armor.Value / Weight (3 rows each, patterns 86/87)
All Obi rows equal Requiem iron sums (200 = 125+37+38, 32 = 20+6+6; helm 50 / 8). Weight row set unsampled by the
verifier. Tsun untouched, already 0/0 like 0C0165.

### Armor.Keywords (13 added, 3 removed)
- AD3B2B on all 4 records (Obi suit carries it **twice**, Keywords[7]/[8]). Live: 3003 winning ARMO reference it across
  97 winner plugins. Requiem.esp itself has **0** ARMO carrying it; only the separate ecosystem patches
  `Requiem - Weapons and Armor Redone.esp` (18) and `Requiem - Magic Redone.esp` (18) do. ARR uses it widely (Vigilant
  600, Glenmoril 483, LOTD 212...). On all 4 pilot records it is redundant: every hand-set AR (400 / 100 / 180) is
  above the rescale threshold (heavy 74 body / 27 head, light body 62). It only matters when a hand-set AR sits below
  the threshold. The full run should test whether it accompanies *every* hand-set AR, and find any below-threshold cases.
- Steel set 06BBE6 -> Iron set 06BBE3 (3/3); PerkFistsIron 0424EF on the suit.
- Baked: AD3AEC on 3, AD3A25 on the suit. Requiem.esp source iron pieces lack them; the RFI output adds them to
  012E49 / 012E4D (verified). The RFI output carries no version of the Obi records, so whether the build would have
  stamped them itself here is shown only by analogy.

### ArmorAddon.FirstPersonModel (6 rows, patterns 105/106/146/147)
Torso addon 000801 (UBE patch record) swaps the mod's FP nif for vanilla iron light-cuirass FP nifs (= IronCuirassAA
012E48); gloves addon 000802 loses its FP model. Mesh choice, no record comparable: correctness unclear. Female path
carries a trailing CR LF (B2). Not a Requiem convention at all; likely a first-person fix specific to this mod.

### ConstructibleObject
- Items (6 added / 6 removed): steel removed, iron + leather added at per-piece sums. Exception: temper suit 00080D
  iron x4 v 1+1+1 = 3 (B3).
- Conditions: +HasPerk 0CB40D, -HasPerk 05218E, -EPTemperingItemIsEnchanted (2 temper recipes); matches 085DC5.
- WorkbenchKeyword (2): forge recipes 00080B / 00080C Forge 088105 -> REQ_DisableRecipe AD3B01. AD3B01 is common
  (1299 winning COBJ, ~50 plugins; Requiem itself uses it on 104). But nothing replaces these two: no forge recipe
  makes 00080A or 000D6A (verifier; Reqtificator source has no recipe generation). The ingredient edits on these two
  disabled recipes are inert.
- Defined (2): 000800 forge / 000801 temper for the Alt helmet 00080F, exact copies of 0DB292 / 0ADB93.

### Container (lock section)
- K1 on 67/67: VMAD created (Version 5, ObjectFormat 2 = header noise) + REQ_LockpickControl. The build stamps the
  identical entry on every winning CONT lacking it (CustomLockpicking.cs L19-48; MainLogicExecutor.PatchContainers
  L197-201), so carrying it is redundant but harmless. Baked, Requiem-aligned.
- Noise: Items reordered on 19 AC chests (same entries); 2 GS chest entries re-typed Faction -> Untyped owner, same
  faction FormID (A687DE / A687E1), tool re-serialisation; VersionControl zeroed.

## Suspected bugs

| Record | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|
| B1: COBJ 00080B, 00080C (Obi suit + helmet forge) | WorkbenchKeyword | AD3B01 REQ_DisableRecipe | a live forge recipe for 00080A / 000D6A, or an explicit "unique, uncraftable" decision | no other COBJ creates those items; Alt helmet got a new recipe, these did not | Suspected bug (or house call) |
| B2: ARMA 000801:Obi's Hedge Knight Armor UBE patch.esp | FirstPersonModel[1].File | `Armor\Iron\F\1stPersonCuirassLight_1.nif` + CR LF | same path, no CR LF (012E48) | verifier read the string ending `\r\n`; clean path resolves, CRLF one will not | Outlier bug |
| B3: COBJ 00080D (temper suit) | Items iron count | 4 | 3 (085DC5 + 0ADB92 + 0ADB89, 1 each) | per-piece sum used everywhere else | Outlier bug (low; may be rounding) |
| B4: ARMO 0009F9:AnimatedCarriage.esm | ArmorRating | 180 | 161 (0C0165) | same armature + keywords as Requiem's Tsun body; hand-set (AD3B2B irrelevant, both values above threshold 62) | Outlier bug, pending Aaron |
| B5: ARMO 00080A | Keywords | AD3B2B x2 | once | Keywords[7], [8] | Noise |

## Open calls for Aaron

1. **B1:** were the Obi suit and main helmet meant to be forge-uncraftable (temper only)? If yes, C4 becomes a house
   convention "disable the mod recipe for unique / non-tier gear" and the inert ingredient edits should go.
2. **A6:** keep writing build-stamped keywords (AD3AEC, ranged tier) into patches, or leave them to the build? Pilot
   sees it on Obi only; it decides whether the skill teaches "carry" or "omit".
3. **B4:** is 180 on the Tsun cuirass a deliberate buff? If not, the AR should be 161. AD3B2B is redundant at both values (both above the light-body threshold 62).
4. **ARMA FP swap:** is replacing a mod's first-person mesh with a vanilla one a list convention (clipping fix) or a
   one-off for this mod?
5. **Multi-slot sum (A1/A2/C2):** confirm that a combined suit gets the summed stats and recipe of its pieces; the
   pilot has one such record.

## Pilot caveats

- One fit armour plugin, one iron-tier heavy set: no light/clothing, no higher tier, no enchanted or unique gear, no
  shield, no jewellery. Rules A1-A6 and C1-C4 cannot be told apart from "what one author did on one day".
- Held-out armour = 1 record; only A1 and A3 got a real test (1 hit, 1 miss). No held-out COBJ or ARMA at all.
- No patch age / Requiem-version signal available: two armour plugins cannot separate author or era effects.
- Annotations partly unsampled by the verifier (patterns 87, 88, 99-102, 136, 138-140, 142, 144-145).
- RFI output carries no version of the Obi or Tsun records, so "baked" for A6 rests on the iron analogues.

**What the full 244-plugin run needs, per rule:**
- A1/A2: per-row comparables across tiers and slots; per-row aggregation; count how often patch AR
  equals the comparable v the build-rescaled base v neither, split by plugin to find authors who hand-buff.
- A3: AD3B2B presence v "AR changed by patch" cross-tab (3003 winners already carry it; most are outside this pilot).
- A4/A5: set-keyword and PerkFists swaps across materials; does any patch keep a mod's own set keyword?
- A6: per plugin, share of records carrying build-stamped keywords, against the RFI output's version.
- C1-C4: every COBJ the patches touch; for each AD3B01 recipe, whether another recipe creates the same item.
- ARMA: count FP-model edits and any other mesh-path edits; scan all patch path strings for trailing CR/LF.
- K1: expected near-universal; the full run should list containers in patches that *lack* it (would be baked by the
  build anyway, so absence is harmless; presence is the convention).
