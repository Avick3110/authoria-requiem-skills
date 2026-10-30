# ARMO census: ARR handmade Requiem patches

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 18,605 Armor rows across 107 plugins (from `stage1/work/consensus/Armor.json`). Script rules decided 9,500
rows (11 rules). The residual sits in 18 families. **Held out:** 4 of the 6 eval references touch ARMO: Animated
Carriages, Falkreath Shades, Mage Core and The Rot Below (`Authoria - Reqtificated - *`). Mage Core has the most rows
of the four (216). The biggest plugins are Vigilant (3,619 rows), Glenmoril (2,332), FurSet by Keung (1,607), Unslaad
(1,481) and LOTD (1,478).
Five live reads were made this session (houseCARL, epoch `e2-58972ca8618e79ea`), and each one is cited where it is used.

## 1. Conventions

The support figures come from non-held-out rows. "Held-out" gives the held-out rows that follow the convention (from
`held_out_rows`).

| # | Convention | Class | Input / baked | Support (rows / plugins; rule counts incl. held-out) | Held-out result | Conf. |
|---|---|---|---|---|---|---|
| C1 | Hand-set AR at Requiem scale, tiered against a Requiem comparable (daedric body 600, boots 180). Lesser sets are deliberately pitched below their comparable | Requiem-aligned | input | 1,157 rule rows (65 plugins) = comparable; 1,702-row family (77 plugins); 136 off-comparable (8 %) | 5 rule + 10 family rows follow | high |
| C2 | Zero the AR on accessories, extra-slot pieces, wigs, tails, backpacks and reclassified clothing | **house** (44 plugins). Goes beyond Requiem (only 20/385 Requiem clothing pieces have AR > 0, fact 7). Fact 8 ("one patch") understates it | input | 611 rows → 0 (44 plugins; 605 / 43 non-held-out; Dint Hair 152, LOTD 125, FurSet 79) | follows (6 Mage Core pieces zeroed, e.g. aMCArmlet / Thighlet / Tome / Hood, live) | high |
| C3 | Put NoArmorRatingRescale `AD3B2B` on every record with a hand-set AR and on AR-0 pieces, with no threshold gate | **house** (Requiem.esp: 0 ARMO) | input (a skip switch the build reads) | 2,991 rows, 83 plugins; 2,687 records | 9 held-out rows: 3 of 4 held-out plugins follow (Rot Below 5, Falkreath 3, Animated 1). **Mage Core adds none**, even on its 6 zeroed pieces, but those are clothing, where the flag is not load-bearing | high |
| C4 | Hand-set Weight to the Requiem comparable per slot / type | Requiem-aligned | input | 1,074 rule rows (60 plugins); 639-row family (54 plugins), 15 % off-comparable (lower tiers, NPC_ copies) | 5 rule + 3 family rows follow | med |
| C5 | Hand-set Value to the Requiem comparable for real armour | Requiem-aligned | input | 656 rule rows (53 plugins); 883-row family (59 plugins), 16 % off-comparable | 3 rule rows follow. **Mage Core deviates:** a flat ×3 on 169 rows, all aMCDress* clothing variants, across every price tier (5 → 15 … 408 → 1,224, 2,563 → 7,689). That is 169 of the 173 held-out Value rows | med |
| C6 | Reclassify ArmorType to what the piece really is (light → clothing 88, heavy → light 75, light → heavy 41, heavy → clothing 22, clothing → light 11) | **house** (the decision rule is not stated) | input | 237 rows, 25 plugins | 0 held-out rows (Mage Core does not reclassify) | med |
| C7 | When a piece is reclassified, strip its old type keywords (ArmorLight `06BBD3`, ArmorHeavy `06BBD2`, shared REQ_ArmorSet_*, ArmorCuirass-class slot keywords, VendorItemArmor `08F959`) and add the new ones (ArmorClothing `06BBE8`, VendorItemClothing `08F95B`, ...) | Requiem-aligned (one consistent type) | input | 1,359 removed rows (60 plugins); the added slot / vendor variants run 9–122 rows each | 1 removed row follows | med |
| C8 | Add MagicDisallowEnchanting `0C27BD` to extra-slot accessories | Requiem-aligned: 222 Requiem-era ARMO winners reference `0C27BD` (e.g. REQ_Artifact_*); fact 26 | input | 348 rows | 40 held-out rows follow (all Mage Core) | high |
| C9 | Add RFTI_Exclusions_NoArmorTypeKeyword `AE35A8` to opt a piece out of the build's type keywords | house / deliberate input | input | 119 rows, 22 plugins | 0 held-out rows | med |
| C10 | Write in the armour keywords the build stamps: the ranged-tier keyword (cuirasses) and REQ_Tempering_* | **baked**, redundant but harmless (fact 6: RFI re-adds them even on AlreadyReqtified) | baked | 1,725 rows, 75 plugins | 4 rows follow | high (code) |
| C11 | Write in the keywords from `keywords_assign` | **baked** (config-proven) | baked | 1,448 rows, 70 plugins | 6 rows follow | high (config) |
| C12 | Carry the set / type / material keywords that the build's configs read as conditions | input (config-read) | input | 888 rows, 49 plugins | 2 rows follow | high |
| C13 | Clear source enchantments (null link) on REQ_NULL_ / unwanted armour. A few are swapped for a Requiem or MR enchantment instead (Kaidan hoods → `00785F:Requiem - Magic Redone.esp`) | mixed | input | 189 rows, 23 plugins (mostly FurSet REQ_NULL_) | 1 row | low (cannot-tell) |
| C14 | Put artifact rings back on the default race `000019` | Requiem-aligned: this **forwards Requiem.esp** (live: Requiem.esp already sets 000019 on `0275B7:Dragonborn.esm`; DBM_ISC_Patch reverted it to 013740) | input (a forward) | 4 rows, 1 plugin (LOTD) | none | high (live) |
| C15 | Strip stray weapon scripts from armour (SilverSwordScript on Vigilant hats / shields) | house (clean-up) | input | ~19 rows | none | low (only Kettle Hat verified) |

## 2. Upstream checklist (per ARMO, in order)

1. **Classify** the piece: heavy / light / clothing / jewellery / shield (C6). If the type changes, swap the type,
   slot and vendor keywords and drop any REQ_ArmorSet_* that no longer applies (C7).
2. **Carry the keywords the build reads:** material, set, type and part keywords (C12). If the piece must not receive
   the build's type keywords, add `AE35A8` (C9).
3. **AR:**
   - Real armour: tier it against a live Requiem comparable for the same material, slot and type (C1).
   - Accessories, extra-slot pieces, cosmetics and clothing: set AR to 0 (C2).
4. **Add `AD3B2B` NoArmorRatingRescale** (C3, house). It is load-bearing on AR-0 light / heavy pieces, where the
   rescale would add a flat +18 / +66, and on the ~17 records below the fact-5 thresholds. Everywhere else it is
   redundant but is the house habit. It is not needed on clothing: the build never rescales clothing (fact 5), and Mage
   Core omits it on its AR-0 clothing.
5. **Weight and Value** from the same comparable (C4, C5). Value must never be 0 on a sellable piece (see B1).
6. **Extra-slot accessories:** add `0C27BD` MagicDisallowEnchanting (C8).
7. **Enchantments:** null the source enchantment on REQ_NULL_ / unwanted pieces, or swap it for a Requiem / MR one
   (C13).
8. **Scripts:** keep functional VMAD. Remove only a script that is meaningless on armour (C15). Never drop a VMAD by
   accident through copy-as-override (B2).
9. **Forward Requiem's own edits** that intermediate patches revert, such as the Race fix (C14).
10. **TemplateArmor:** clearing it also removes the build's TemplateArmor rescale skip (fact 5). If you clear it, set
    the AR by hand and add `AD3B2B`.

## 3. Downstream bake package (what ARR writes in that the build would otherwise produce)

- **Build-stamped armour keywords**, C10 (ranged-tier keyword on cuirasses, REQ_Tempering_*). RFI re-adds these anyway
  (fact 6), so the bake is redundant.
- **`keywords_assign` keywords**, C11. These are config-proven build output, written in.
- **No AR bake exists.** The hand-set AR is a live-analogy *input*, not the output of the ×3.3 / ×6.6 rescale (the
  values match Requiem comparables, not the formula). `AD3B2B` is the input switch that keeps the build off that
  value. Contrast weapons, where the post-rescale damage plus the triple keyword is a proven bake (FACTS §C).
- A rebuilt skill can teach the bake package as optional: it is safe either way, because the build re-stamps both
  keyword sets.

## 4. Per field / family detail

**Keywords | added (3,893 rows, 93 plugins, 49 held-out; verified, fixed).** `AD3B2B` makes up 2,991 rows; the rest
is a tail of 30 single-keyword variants. The verifier joined this family to the ledger:
- 2,687 records carry `AD3B2B`.
- About 1,520 of the 1,555 slot-resolvable non-zero-AR heavy / light records end at or above the fact-5 thresholds
  (the flag is redundant there). Only 17 end below (the flag is needed).
- 412 records carry AR 0 (the flag is functional).
- 10 of the 80 AR-changing plugins never use the flag.

Candidate reasons for the redundancy: author habit (blanket application is simpler and safe). It is not patch age,
since old and new patches both use it.

Variants:
- `002ED9:Update.esm` Survival_ArmorWarm: added 61 rows, removed 69 rows. This is a Survival Mode keyword, not
  Requiem. It is unclear whether this is a warmth curation or an artefact of a source patch.
- `REQ_ArmorHorse` on saddles: Requiem-aligned.

**ArmorRating | changed (1,702 rows; verified, fixed).**
- Median goes from 14 to 54, max 1,200.
- Top end values: 0 (611 rows), 80, 50, 60, 150, 200.
- The off-comparable sample is 25 of the 136 rows, all from Vigilant. It shows deliberate sub-tiers: Molag Rusty
  variants at 0.83–0.92 of daedric, Renald / Pelinal cuirass at 500 against 600, and NPC_ / NULL_ copies with no
  comparable.
- `zzzCHArmorDaedricMageCuirass` at 380 against tier 600 cannot be judged.
- Caveat: off-comparable rows outside Vigilant were not sampled.

**Keywords | removed (1,359 rows; agree).**
- These mirror the 237 ArmorType changes. The top variants (100–126 rows) line up with the reclass directions.
- Apex Khajiit removes its own keyword `000F60` from 40 rows. This is mod-specific.
- Pattern 1434 removes `0C27BD` from 4 shields, while C8 adds it elsewhere. This is a lead: shields are not extra-slot
  pieces, so the removal is plausibly correct.

**Value | changed (883 rows; fixed).**
- Median goes from 135 to 250, max 75,000. No formula is visible in the non-held-out rows.
- The held-out Mage Core applies a flat ×3 to its clothing: 169 rows, all aMCDress* variants, across every price tier. Requiem itself touches no `Clothes*` ARMO (live: 0 matches),
  so vanilla clothing values stand in Requiem. The ×3 is therefore an **author / patch habit, not a convention**.
- In the live Hal's Casual Outfit sample (10 rows), Value is unchanged: light → clothing, AR 23 → 0.
- Candidate reason for the deviation: author habit or the mod's tier (Mage Core is a quality-tier mage outfit).

**Weight | changed (639 rows; agree).** Follows the comparable. The off-comparable rows are lower tiers (Renald /
Pelinal cuirass weight 37 against 50) and NPC_ copies.

**BodyTemplate.ArmorType (237 rows; agree).**
- Light → clothing (88) and heavy → clothing (22) all end at AR 0.
- Heavy → light: 72 of 75 rows keep a non-zero AR.
- Clothing → light (11) is the reverse direction (Thalmor helmets, Traveler Romper, Horse Whistle Key). Its reason is
  unknown.

**ObjectEffect (189 rows, 130 variants; cannot-tell).**
- Mostly FurSet REQ_NULL_ coats and ponchos, set to "(null link, subrecord present)".
- Two adds give vanilla enchantments `0BE027` / `0BE024` to Ysmir Armor and Penetrator pieces (pattern 2330 / 2331).
  These are adds, unlike the strip pattern. Lead.

**VMAD families (Scripts removed 61, added 38; whole VMAD dropped on 20 records).** The removed and added pairs on DBM
items (visage, crusader, archeology scripts) are the same items, so these are probably property re-saves (FACTS §C:
VMAD pairs are mostly re-orders). Cannot-tell. The 20 VMAD-dropped records are one act. It is deliberate on
SilverSwordScript-on-armour (Kettle Hat `0F476B:Vigilant.esm`, verified). It is accidental on Vampiric Ring (B2).

**BodyTemplate.FirstPersonFlags (43 rows; fixed).**
- Slots move to an intended slot: Amulet / Decapitate → 65536 on capes, Circlet → Hair + Circlet.
- The Rogue Stalker ring / band swap (pattern 3108 / 3110) is deliberate. The live ring goes to Ring (36) and its
  NULL_ copy is parked on slot 60. Both gain `0C27BD`.
- 18 null → null rows are rendering noise.

**TemplateArmor (5 rows; cannot-tell).**
- Unslaad templates an enchanted helmet to its base piece (normal practice).
- LOTD clears the template on three REPLICA museum copies.
- LOTD clears Requiem's own `ADDD8E` template on `REQ_Var_Light_AncientDragonscale_Head_Winterhold` `0940D5:Skyrim.esm`
  (B5). Clearing a template removes the rescale skip.

**Keywords | changed (42 rows):** the record had no list before; the content is covered by the added family. Noise.
**`*|defined` (2 rows, Scout Armor):** marker only.

## 5. Suspected bugs (for Aaron's review, not filed)

| # | Record | Plugin | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|---|
| B1 | `03D788:Vigilant.esm` zzzCHArmorDaedricMageCuirass | Authoria - Reqtificated - Vigilant.esp | Value | 1 (source placeholder) → **0** | a tier value (Requiem daedric body 25,000; AR 380, Wt 50, heavy daedric, tempering keyword) | verifier live read | outlier bug (confirmed) |
| B2 | `125B05:LegacyoftheDragonborn.esm` DBM_PrvtIVampiricRing | Authoria - CC Fishing Patches.esp | VMAD | absent | prvtivampirelordscript + prvtivampiricscript, as LOTD, DBM_ISC_Patch and DBM_CC_FishingHOWPatch all carry | live tree (this session) | suspected bug (copy-as-override dropped the VMAD). Confirm in game |
| B3 | Dint Hair wig `003BC3` | Authoria - Reqtificated - Dint Hair.esp | Keywords[0] | (null link) + `AD3B2B` | no null entry | verifier live read | outlier bug (confirmed) |
| B4 | NP_zzzCHArmorGorieusHelmet; Xtudo Nibenean hood | Vigilant patch; Xtudo patch | Value | 375 → 0 (AR → 0 too); 75 → 0 | non-zero unless NPC-only | verifier note | lead (may be NPC-only / clothing) |
| B5 | `0940D5:Skyrim.esm` REQ_Var_Light_AncientDragonscale_Head_Winterhold | Authoria - Reqtificated - LOTD.esp | TemplateArmor | `ADDD8E:Requiem.esp` → null | Requiem's template kept | family sample (not live-read) | lead. Un-templating a Requiem variant drops its inherited stats and the rescale skip |
| B6 | Ysmir / Penetrator pieces (`1YsmirBoots`, `penetratorF_Gauntlets`, ...) | Ysmir Armor, Penetrator patches | ObjectEffect | null → `0BE027` / `0BE024` | intended? | family sample | lead |

## 6. Open calls for Aaron

1. **`AD3B2B` everywhere.** Is the blanket use (not gated on the threshold) the convention to teach, or should the
   skill add it only where it is load-bearing (AR 0, or below the fact-5 thresholds)? Either is safe.
2. **Zero-AR on accessories (C2)** is a house convention across 44 plugins (43 non-held-out). Confirm it, and amend
   fact 8 ("one patch" is wrong).
3. **Reclassification rule (C6).** What decides heavy → light (75 rows) and clothing → light (11 rows)?
4. **Mage Core Value ×3 on clothing.** Deliberate for that mod, or a habit the skill should not learn? It is the main
   held-out divergence for ARMO.
5. **Survival_ArmorWarm `002ED9`** is added on 61 rows and removed on 69. What is the intent?
6. **VMAD drops.** Are they deliberate clean-up or a patch-tool artefact? B2 suggests the latter at least once.
7. **ArmorType keyword opt-out `AE35A8` (C9).** When is it used, as opposed to letting the build stamp the keywords?

## 7. Limits

- **Untouched records are invisible.** The census sees only what patches changed. A mod armour ARR left alone (for
  example a piece that still needs `AD3B2B` or an AR set) does not appear. The share of AR-0 light / heavy pieces that
  lack `AD3B2B` and so get the flat rescale is not measured: 611 pieces were zeroed and 412 of them carry the flag,
  and the gap may be clothing (lead).
- **Thin held-out validation.** Only 4 references touch ARMO. Most of their rows are Mage Core. The 49 held-out
  added-keyword rows split into 9 `AD3B2B` and 40 `0C27BD` (from a ledger recount by the verifier), so C3 rests on 9
  held-out rows from 3 plugins.
- **Samples skew to Vigilant.** The off-comparable samples for AR, Weight and Value are all Vigilant, so
  comparable misses in other plugins were not seen.
- **Unverified families.** The VMAD | changed family is unsampled; its sibling fields were verified as the same act.
  `*|defined` is unsampled. Race was unsampled in the evidence but was settled by the live read here. ObjectEffect,
  TemplateArmor and the DBM script pairs are cannot-tell, so the conclusions on them are leads.
- **ksws03_quest.esp failed to load.** Any ARMO it defines or overrides is absent from every live read.
- **No in-game check:** the effect of the B2 script loss and B5 is not tested in game.
