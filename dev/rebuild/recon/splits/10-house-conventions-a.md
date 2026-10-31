# Split hunt 10: house conventions A (NoArmorRatingRescale, encounter zones, LOTD Visage)

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. It covers three house conventions the census flagged:
- `../census/stage1/ARMO.md` C3: NoArmorRatingRescale on 2,991 rows in 83 plugins;
- `../census/stage2/LOCKS.md` conv 13 and open call 3: Gray Cowl's encounter-zone caps;
- `../census/stage2/PERK-AI.md` PERK-AI-8, plus the "Visage series" note in split 2: LOTD's Visage items.

Scripts and data stay local (`census/splits/hc10_*`). Live reads are on epoch `e2-58972ca8618e79ea`, the census
snapshot.*

## Scope

- **NoArmorRatingRescale.** All 4,538 patched ARMO records in 115 census plugins (`hc10_nar.jsonl`), each with
  its source version. Each record is checked against the Reqtificator's own rescale code (READ ONLY, in the
  instance's *Reqtificator And Requiem Tweak* mod) and its config (`Reqtificator.conf` in Unbroken Road BP2).
  Requiem-family patches were read live: WAR, MR, MR×WAR, Sons of Skyrim LOTD, New Legion LOTD, Campfire.
- **Encounter zones.** All 710 live ECZN records, every census delta that touches an encounter-zone link, and
  Gray Fox Cowl's cells, locations and actors.
- **Visage.** The 31 Visage records in the LOTD patch: 30 DBM_Visage* pieces plus Requiem's Visage of Mzund. Also
  the 12 set-bonus perks and abilities (source v ARR), Requiem's 3,101 REQ_ armour records, and its 40 dragon
  priest masks.

---

## A. NoArmorRatingRescale `AD3B2B`

### What the keyword does (from the code)

`ArmorRatingScaling.cs` changes an armour piece's AR only when all of these hold:
- the piece has no `AlreadyReqtified` (`AE3B83`), no `AD3B2B`, and no TemplateArmor;
- its type is Light or Heavy (clothing is never touched);
- its *own* keywords include one of the five part keywords (ArmorCuirass, Boots, Gauntlets, Helmet, Shield). The
  check runs before the build's keyword rules, so keywords the build adds later don't count;
- its AR is below the threshold for that type and part.

| Threshold | Body | Head | Hands | Feet | Shield |
|---|---|---|---|---|---|
| Heavy | 74 | 35 | 27 | 27 | 54 |
| Light | 62 | 26 | 18 | 18 | 44 |

When it fires, the new AR is: heavy AR × 6.6, light AR × 3.3, plus 66 on a cuirass or 18 on any other piece.

So `AD3B2B` only matters on a light or heavy piece that has a part keyword and a below-threshold AR. That is
mostly **AR-0 pieces**: without the keyword, the build would lift them to 18 or 66. It also covers deliberately low
pieces, such as Dwemer Armor's 14 / 36 / 20 or Dark Apprentice's 25 mantle.

### Answer

| # | Rule | Requiem / MR evidence | ARR evidence |
|---|---|---|---|
| 1 | **Mandatory wherever it changes the outcome:** a light or heavy piece, no template, a part keyword of its own, AR below threshold | The code gate above | **285 / 288** load-bearing pieces carry it. The 3 that don't are LOTD's `REQ_NULL_MS06ShieldL18 / L25 / L32` (AR 26). The build lifts them to 103.8, which is exactly the value ARR hand-baked into their sibling `L12`, so the outcome is the same |
| 2 | **Never on a templated piece** | The build skips TemplateArmor itself | 0 / 523. Clean |
| 3 | **Habit, not need, on everything else in the light / heavy range.** It marks "this AR is final" | Requiem-family patches do the same: 55 of their 57 `AD3B2B` armour records sit above the threshold (MR bound armour, WAR padding, Sons of Skyrim LOTD) | 2,100 / 2,350 above-threshold pieces carry it. Plugin stance: 64 blanket, 3 targeted, 10 none, 7 mixed (below) |
| 4 | **On clothing it travels with the tempering keyword.** It is inert there | The build never rescales clothing | Every clothing piece given `REQ_Tempering_*` carries it: 276 / 276. Without tempering, 44 / 861 carry it: 25 were armour reclassified to clothing, 11 are replicas |
| 5 | **Never inherited.** ARR adds it itself | n/a | 0 / 2,933 came from the source or from a Requiem patch below |

**Plugin stance on above-threshold pieces:**
- **Blanket:** 64 plugins (e.g. Vigilant, Glenmoril, Unslaad, Armours of Velothi).
- **None:** 10 plugins. None of them has a single load-bearing piece, so leaving it off changes nothing. Examples:
  Apex Khajiit 0 / 48, Gilded Doublet, Stormhold.
- **Targeted:** 3 plugins (FurSet by Keung, Cimmerian, Traveler Romper). They put it only on their AR-0 pieces, e.g.
  FurSet's earmuffs and open-finger gloves, and leave it off the real pieces.
- **Mixed:** 7 plugins. Their gaps follow whole sets: 161 of 166 edid-stem groups are all-in or all-out, e.g.
  Vigilant's Ayleid and Laza sets, and Snow Elven's Ancient One set.

**For the skill:** add `AD3B2B` to every light / heavy piece whose AR you hand-set and which has no TemplateArmor.
That is harmless above the threshold and required below it. Don't add it to templated pieces. On clothing it does
nothing, so it is optional.

**Side finding (feeds census C1).** Some hand-set ARs are just the build's own formula applied by hand. Of 2,099
pieces whose source AR sat below the threshold, 167 match `src × f + o` exactly and 166 match it rounded to 5.
Examples: Saints & Seducers' `EC_SS_DarkPriestArmor` 42 → 343.2, and the MS06 shields above. With `AD3B2B` on,
those pieces end up where the build would have put them anyway.

### Hypothesis log (A)

| # | Hypothesis | Result |
|---|---|---|
| A1 | On every armour record (the census reading) | Weak: 2,933 / 4,538 (65%) |
| A2 | Type decides | Partly: light / heavy 2,613 / 3,290 v clothing 320 / 1,248 |
| A3 | Never on templated pieces | **Kept** (0 / 523) |
| A4 | Always where the build would rescale (code simulation) | **Kept** (285 / 288). The 3 misses produce the same outcome |
| A5 | AR-0 pieces | **Kept** within A4: 406 / 416. The 10 misses have no part keyword, so the build wouldn't touch them |
| A6 | Added when the patch changed the AR | Weak: 75% v 31% |
| A7 | NPC-only (NonPlayable) gear | Confounded: 88% v 58%, driven by Vigilant / Glenmoril NP_ copies |
| A8 | Per-plugin stance | Descriptive: blanket / none / targeted. All 10 "none" plugins have zero load-bearing pieces |
| A9 | Set-granular gaps | **Kept**: 161 / 166 stem groups in the mixed plugins are pure |
| A10 | Inherited from the source or a Requiem patch | Dead: 0 / 2,933 |
| A11 | On clothing it goes with REQ_Tempering_* | **Kept**: 276 / 276. Without tempering, 44 / 861 |
| A12 | On clothing it marks a piece reclassified from armour | Dead: 25 / 121 reclassified v 295 / 1,016 |
| A13 | Requiem-family usage is threshold-only | Dead: 55 / 57 of their uses sit above the threshold |

---

## B. Encounter-zone caps

### What the records show

Gray Cowl of Nocturnal is the **only** ARR patch in the whole load order that edits encounter-zone levels. Its
ECZN winners are 6 of the 710 live zones. The census corpus has only two other encounter-zone deltas, and neither
is a level change:
- Sonic Mage forwards USSEP's NoResetZone onto one Dragonborn actor;
- Val Serano restores a zone that the Lux patch dropped from `AXMolagBalCave`.

Gray Fox Cowl ships 22 interior cells on vanilla's **NoResetZone** (`0F90B1:Skyrim.esm`: min 1, no max,
NeverResets). Across the order that zone is what homes and museums use: player houses, the LOTD gallery, Breezehome,
Honeyside. Gray Cowl used it for its quest dungeons too, so those dungeons would level with the player without any
limit.

### Answer

| # | Rule | Requiem / vanilla evidence | ARR evidence |
|---|---|---|---|
| 1 | **Move dungeon cells off NoResetZone. Leave homes on it** | Vanilla and mods keep NoResetZone for houses (60 remaining users, mostly homes and LOTD gallery rooms) | Moved 18: all 17 dungeon-typed cells (LocTypeDungeon, Temple, or a child of a dungeon location) plus Shanta Filibb 02. Kept 4: Shanta Filibb 01, Moonlight Home, Seviana's House, Service Room, all houses or service rooms. 21 / 22 |
| 2 | **Band by quest act, not by any record property** | n/a | New zones, all NeverResets: **Early 15–22** (11 Cyrodiil / Coldharbour cells: Sancre Tor, Imperial City Prison, Mausoleum, Cave of Hollow Time, Halls of the West); **Desert side 18–26** (Ben Erai sewers, Tempted Centurions, Shanta Filibb 02); **Deep 22–30** (the four Alikr-desert-parented dungeons: Unknown Gate, Another World, Hamunaptra, Draukiir) |
| 3 | **Cap the mod's own zones the same way** | Requiem never sets a max: its 8 zones all have MaxLevel 0, and its only edits add NeverResets to 4 Black Book zones. Vanilla caps only 5 / 280 | Khenzedum 12 / – → 12 / 20. Tomb of the Forgotten Draugr and Al Shedim 15 / – → 20 / 30 |
| 4 | **Shape: max ≈ min + 8, ceiling 30** | n/a | Gaps 7, 8, 8 (new) and 8, 10, 10 (source zones). All 6 maxima ≤ 30 |

This is a **one-mod judgement with a clear domain reason**: the source misused NoResetZone. It is not a list-wide
convention. Other big mods' zones (Vigilant 99, Glenmoril 94, BS Bruma 57, Unslaad 27) are untouched, and only BS
Bruma caps its own zones (max 25–70).

### Open residual (B)

- **Shanta Filibb 02** is a player-house location whose only actors are 3 mannequins. It was moved onto the
  desert-side zone while its sibling cell stayed home-zoned. That is harmless, because the new zone also has
  NeverResets. Logged as a plausible slip.
- **Where the band numbers come from** (15 / 18 / 22, about +8, ceiling 30), and whether a skill should apply the
  same treatment to other mods that put dungeons on NoResetZone. The question matters for LOTD's `DBMRkund06` and
  `DBMDeepholme`, and Val Serano's `AXHiddenGrotto01`. Both go to the list authors.

### Hypothesis log (B)

| # | Hypothesis | Result |
|---|---|---|
| B1 | A list-wide ARR zone convention | Dead: 1 plugin, 6 / 710 zones |
| B2 | Follows Requiem's zone edits | Dead: Requiem sets no maxima and changes no levels |
| B3 | Follows vanilla's capped zones | Dead: 5 / 280 vanilla zones are capped, with gaps 4 / 14 / 24 |
| B4 | Triggered by cells on NoResetZone | **Kept**: 18 / 22 of Gray Fox Cowl's NoResetZone cells moved; no cell on any other zone moved |
| B5 | Location type decides what moves | **Kept**: dungeon-typed 17 / 17 moved, houses / service rooms 4 / 5 kept (Shanta Filibb 02 is the miss) |
| B6 | Band by quest act / region | **Kept**: Alikr-desert-parented 4 / 4 → deep; Cyrodiil / Coldharbour 11 / 11 → early; Ben Erai side 3 / 3 → desert |
| B7 | Band by placed hostiles (ACHR count) | Dead: the early band runs 0–7 placed actors (Int03A–D 1 each, Imperial City Prison 7), the deep band 0–85 (Another World / Unknown Gate 0, Draukiir 85) |
| B8 | Cap = min + 8, ceiling 30 | **Kept** as shape (6 / 6 ≤ 30; gaps 7–10) |
| B9 | NeverResets kept or added | **Kept**: 5 / 6; Khenzedum stays 0 as in the source |
| B10 | Applied to every mod with dungeons on NoResetZone | Dead / open: LOTD, Val Serano and Blackreach dungeon-like cells were left alone |

---

## C. LOTD Visage

"Visage" in LOTD is the **Visage of the Septims** set, not a dragon priest mask. It has 8 slot pieces: Tiber helm,
cuirass, gauntlets, boots, Reman shield, Amulet of Kings, Arctus ring, and the "Unknown" shield. The body pieces
come in Heavy, Light and Robes forms (plus BASIC, DBM-display and REPLICA copies). A global, `DBM_VisageEquipped`,
counts the pieces worn, and each piece carries a perk whose bonus grows with that count. The one dragon priest
mask called "Visage" is Requiem's own `REQ_Artifact_VisageOfMzund`. ARR only forwarded LOTD's armature onto it;
the stats stay Requiem's (160).

### Answer

| # | Rule | Requiem evidence | ARR evidence |
|---|---|---|---|
| 1 | **Heavy = Requiem Ebony exactly** | `REQ_Heavy_Ebony_*`: 500 / 200 / 150 / 150 / 300 AR, weight 40 / 16 / 12 / 12 / 20, helm value 2000 | Heavy cuirass, helm, gauntlets, boots and Reman shield: AR 5 / 5, weight 5 / 5. Helm value 850 → 2000 on all 4 helms, Robes helm included. BASIC copies = Heavy (4 / 4) |
| 2 | **Light = Requiem's light 250 tier (Orcish / Ancient Dragonscale)** | Orcish light: 250 / 100 / 75 / 75, weight 15 / 6 / 4.5 / 4.5 | Cuirass 250, gauntlets and boots 75, cuirass weight 15: 4 / 4. Helm 120 (that is Dragonscale's helm) and gauntlet / boot weight 6 are off-tier (question) |
| 3 | **Robes, display and replica copies → AR 0** (split 3's rule) | n/a | Robes cuirass 8 → 0. All 10 DBM display and REPLICA copies → 0, and they carry `AD3B2B` because they are load-bearing AR-0 pieces |
| 4 | **The "Unknown" shield sits one tier below the Reman shield** | Heavy 210 / 12.5 = Akaviri / Dawnguard / Improved Bonemold shield | 26 → 210, weight 0 → 12.5 |
| 5 | **Set bonuses: per-piece steps cut, defence kept** | Requiem's own Conditioning ladder also uses 0.76 / 0.88 / 0.94 | Incoming damage 3% per piece (24% at 8) **kept**. Enemy crit 3% → **2%** per piece (×⅔). Stagger 10% → **5%** per piece (×½). Speed 5% → **2%** per piece (×⅖). Spell cost at 2 / 4 / 6 / 8 pieces: 5 / 10 / 15 / 20% → 3 / 5 / 10 / 15%. Melee damage (3–12%), enchantment power and soul-gem enchanting (5% per piece) kept |
| 6 | **Off-theme or exploitable mechanics swapped for a 15% full-set bonus, or for a Requiem / MR effect** | MR's `REQ_Ench_SpellAbsorption` (`00784F`); `ER_Effect_Featherfalling` from *Requiem - Enchanted Rings* | Arctus ring: +1 enchantment slot → 15% magic resist at 8 pieces. Amulet of Kings: soul-gem recharge removed, +15% attack damage at 8. Gloves: lockpick sweet spot removed. Boots: fall-damage ladder removed, featherfalling added. Reman shield: LOTD reflection 20–80 → MR spell absorption 5–20 (×¼) |
| 7 | **No enchantment to scale** | n/a | 0 / 31 Visage records carry an ObjectEffect, so "enchant magnitude" doesn't apply |

**LOTD's own dragon priest masks** (the "mask style" in the brief) don't use Requiem's mask tier. Requiem gives
every vanilla mask three variants: clothing 0, light 96 (weight 4), heavy 180 (weight 13), all valued 25,000.
ARR set:
- Haldriin 170, Vosis 145 and Korthor (light) 75. These are the Reqtificator's rescale of the source AR, rounded:
  169.8, 143.4 and 74.1.
- Vahlok to Requiem's Ebony helm: 200 AR, weight 16, value 2000.

Whether that was deliberate goes to the list authors.

### Hypothesis log (C)

| # | Hypothesis | Result |
|---|---|---|
| V1 | Heavy pieces = a Requiem heavy tier | **Kept**: Ebony, AR 5 / 5 and weight 5 / 5. Dragonplate matches the AR but not the weight |
| V2 | Light = heavy × ½ | Weak: 3 / 4; the helm is 120, not 100 |
| V3 | Light = a Requiem light tier | **Kept** on cuirass, gauntlets, boots (Orcish / Ancient Dragonscale 250 tier); the helm is off-tier |
| V4 | Display / replica / robes → 0 | **Kept** (11 / 11) |
| V5 | BASIC = the full piece's stats | **Kept** (4 / 4 AR and weight). The source's weight-0 placeholders were fixed |
| V6 | Enchantment magnitude scaled | Dead: no Visage piece is enchanted |
| V7 | Set-bonus benefits halved (split 2 rule 2) | Partly: stagger ×½, crit ×⅔, speed ×⅖, spell cost ×0.6–0.75. **Incoming-damage reduction and melee / enchant bonuses were left unchanged** |
| V8 | Defensive bonuses kept, utility trimmed | **Kept**: damage reduction kept; crit / stagger reduced but kept; fall damage, lockpick, recharge and the extra enchantment slot were removed |
| V9 | Removed mechanics replaced by full-set 15% bonuses | **Kept** (2 / 2: magic resist, attack damage) |
| V10 | Replacements use Requiem / MR effects | **Kept** (2 / 2: MR spell absorption, Enchanted Rings featherfalling) |
| V11 | Value from the comparable | Partly: helms → Ebony helm value 2000 (4 / 4); other values kept from source. GlovesBASIC 400 → 2500 is the odd one (bug file) |
| V12 | Masks follow Requiem's dragon priest tier (96 / 180) | Dead: 0 / 4 LOTD masks. 3 / 4 sit on the build formula rounded to 5, and Vahlok is the Ebony helm |
| V13 | Visage of Mzund rescaled | Dead: armature forward only |

## Open residual

- **A:** none that changes an outcome. Rule 3's blanket-v-none stance is author habit: provenance, and harmless.
- **B:** the band numbers, and whether the treatment generalises (questions file).
- **C:** the light helm (120) and light gauntlet / boot weight (6); LOTD's masks using the formula rather than
  Requiem's tier; and GlovesBASIC's value (bug file).
