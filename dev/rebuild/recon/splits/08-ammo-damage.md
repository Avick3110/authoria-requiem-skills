# Split hunt 8: ammo damage

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`../census/stage2/CONSUMABLES.md` conv C-3) found
mod arrows and bolts raised 2.6–10× (mostly 4–5×) with `RFTI_Exclusions_NoDamageRescale`, and no single ratio.
This round tested a material-table reading against live Requiem. Scripts and data stay local
(`census/splits/ammo_*`).*

## Scope

- **Corpus:** the 55 ARR ammo records whose Damage changed (19 plugins). Glenmoril has 20, Vigilant 10. Two are
  held out (Falkreath Shades, The Rot Below).
- **Comparables**, read live at epoch `e2-58972ca8618e79ea`:
  - Requiem.esp's own 116 AMMO records (`source=Requiem.esp`);
  - the load-order winner for the same records, mostly `Requiem - Weapons and Armor Redone.esp` (WAR);
  - vanilla values (`source=Skyrim.esm` / `Dawnguard.esm` / `Dragonborn.esm`);
  - the Reqtificator output `Requiem for the Indifferent.esp`, read off-order from mod `Authoria - RFTI Output`
    (it is unticked in the eval profile);
  - the projectiles of all 55 rows.

## Answer: a final value read off a Requiem comparable, not a ratio

The 4–5× is a shadow of Requiem's table. **WAR's table is exactly vanilla × 5 for 9 of its 16 generic arrows** (Iron,
Steel, Draugr, Dwarven, Quicksilver, Glass, Ebony, Stalhrim, Daedric). Elven (×3.75), Orcish (×6.7) and
Dragonbone (×4.4) break the factor. When a mod arrow's source damage looks vanilla, "the Requiem value for its
material" and "source × 5" give the same number.

| # | Rule | Requiem evidence | ARR evidence |
|---|---|---|---|
| 1 | **Write the final (post-Reqtificator) damage, and always add `AD3B2D` NoDamageRescale.** The build then leaves it alone | `AmmunitionTransformer` does ×4 unless the keyword is there. All 11 damaged AMMO winners without the keyword are in the RFTI output at ×4 (11 / 11). Every Requiem arrow and bolt carries `AD3B2D` | 55 / 55 carry `AD3B2D`. **0 / 55 are in the RFTI output**, so nothing is multiplied twice |
| 2 | **Damage = the Requiem arrow or bolt of the same material** (material from the keyword or the name; `WeapTypeBoundArrow` counts as Bound) | WAR arrows: Forsworn 30, Iron / Falmer 40, Steel / Draugr / Silver 50, Elven 60, Dwarven / Quicksilver 70, Orcish / Nord Hero / Skyforge / Bound 80, Glass 90, Ebony / Stalhrim 100, Dragonbone 110, Daedric 120. Enchanted variants carry the base damage | **27 / 55** match a Requiem table (next row). Of the 22 "plain" records (no special name), 21 / 22 match a table or ×5 |
| 3 | **Two eras of the table are live in ARR's patches** | Requiem.esp's own table: Iron 54, Steel 60, Elven 66, Dwarven 72, Glass / Orcish 78, Ebony 90, Bound 90. WAR overrides it to the 40 … 120 ladder | WAR value 18 / 55. Requiem.esp-only value 9 / 55: Vigilant iron arrows 54, Fire / Light (bound) arrows 90; SEA Snow Elven 78; AFDI Ebony NP 90; Glenmoril Tonitrus 70 (= Requiem.esp steel bolt) |
| 4 | **Bolts carry the bolt premium** | WAR bolt = arrow × 1.2 (Steel 60, Dwarven 84, Ebony 120, Daedric 144). Requiem.esp bolt = arrow × 7/6 | 9 / 23 bolt-keyworded rows: Remiel bolt and Glenmoril's standard bullets 84, Tools of Kagrenac sphere bolts 84 (= Requiem's creature sphere bolt 84), Black Soul bullet 60, Tonitrus 70. Val Serano's bolt (70) got the arrow value (bug list) |
| 5 | **No comparable material: source × 5** | WAR's own implicit factor (rule above) | 7 rows only fit ×5: Bruma Ancient Imperial 50, both thrown spears 50, Dac0da sphere arrow 60, Glenmoril shotgun shell 100 and Stalhrim bullet 80, Val Serano bolt 70. Layered rules 2–5: **34 / 55**; with the gun rule, **37 / 55** |
| 6 | **Special ammo is moved off its material by judgement, usually onto another rung of the ladder** | Requiem has no precedent: its enchanted, elemental and named specials keep the base damage (Bloodcursed / Sunhallowed 60 = Elven; enchanted sphere bolts 84; Dawnguard arrow 70) | 20 of the 21 residuals are special ammo. 15 / 21 land on some Requiem rung, but not their material's. Special arrows mostly go **up** 1–3 rungs (7 / 9). Gun rounds follow their own rule (sub-split below) |

**Domain specifics:**
- **Glenmoril guns are their own family** (16 rows, bolt flag, `zzzRevItemGunBullets`). Rule: **max(10, source ×
  5)**, and the standard bullets sit on the Requiem dwarven bolt (84). This covers 11 / 16. The material keyword on
  a round is cosmetic: Oblivium and Herald carry `WeapMaterialDaedric` (Daedric bolt 144) but sit at 90 and 60.
- **Requiem's tier keyword fixes the damage; ARR's does not.** In WAR, damage is a function of
  `REQ_ArmorPiercingArrow_TierN` (none 30–40, T1 50, T2 60, T3 70–80, T4 90, T5 100, T6 110, T7 120; bolts × 1.2).
  ARR damage matches its own tier keyword in only 16 / 55 (e.g. Arrow_Tier2 carries 50 … 90). The tier keyword is a
  separate judgement in ARR.
- **"Ancient Falmer Arrow" got five values.** Same name in different mods: Glenmoril ivory 60, LOTD 70, SEA 78,
  Glenmoril / Dac0da snow elf 90. It shows how loose the special-ammo placement is.

## Era: it tracks the patch

Of the 27 table-matched rows, 10 fit only WAR, 9 only Requiem.esp, and 8 fit both (Daedric 120, Dwarven bolt 84).
- **Requiem.esp era:** Vigilant (4 / 4), Snow Elven Arsenal (1 / 1), AFDI (its Ebony NP arrow 90; its Daedric arrow
  120 fits both).
- **WAR era:** Bruma, Apex Khajiit, Armours of Velothi, Dac0da, LOTD, Unslaad. Remiel and Tools of Kagrenac fit
  both (84).
- **The one mixed plugin is Glenmoril:** 4 WAR rows and 3 Requiem.esp rows. But all 3 Requiem.esp rows (Tonitrus
  70) are also source × 5 under the gun rule. Read that way, **0 / 12 plugins mix eras**.
- Patch and source mod are one-to-one here, so "tracks the patch" and "tracks the source mod" can't be told apart. No
  plugin mixes eras across its records, which points at when the patch was made. File dates can't confirm it (all
  2026-09-12).
- Third-party precedent: the Trad CC Saints and Seducers / Curios Requiem patch (not ARR) is in the Requiem.esp era
  (Iron bolt 63, Orcish bolt 91, Amber 108 = Requiem.esp Dragonbone).

## Sub-split: pricing of the 21 special rows

**Guns (8 of the residual).** Under the gun rule above, 5 rows stay off:
- Meridium and Oblivium bullets 90, Herald of Herma-Mora 60: each is exactly **source × 5 + 10**. All three are the
  Daedric-prince rounds (Meridia, Oblivion, Hermaeus Mora), 3 / 3. But other custom-enchanted rounds (Shotgun,
  Stalhrim, Black Soul) get +0, so this is a lead, not a rule.
- Histus / Jillian energy bolts 80: source × 4. They are the only rounds with no enchantment and no explosion. Their
  rifles were raised to 90 (others 70).

**Arrows and thrown (13).** Rung steps over the material's WAR value:
- **Up:** Auroran +1, Ebony Greatarrow ×2 +1, Bone +1.8, Bourlor's Greatarrow +2.5, Shaman's +3, LOTD Ancient
  Falmer +3.
- **Down:** Kaidan's −2, Sword of Jyggalag −1.
- **No material:** Log 80, Destructive Log 110, Kyne's Arrow 76, the Falkreath golem bolt 80 ("Ancient Nord Arrow" =
  Requiem's Nord Hero 80, a likely name match).

**The one property that separates them:** a slow (< 3600) or heavy (gravity > 0.5) projectile. That holds for
12 / 21 residual rows against 8 / 34 on-table rows. It marks the great arrows, logs, Kyne's arrow and the slow gun
rounds as off-table. It doesn't set the amount.

## Open residual

After the gun rule, **18 / 55** stay unexplained (rules 1–5 plus the gun rule cover 37 / 55). They are 13 special
arrows and thrown items, and 5 gun rounds (3 fit the S9b lead). The rung step on the arrows (−2 … +3) follows no
property tested. Vigilant's rows sit at ~×3.6–3.75 of source (6 / 10 of
its rows within ±2 of ×3.67). That fits a per-mod ratio, but not its logs or Bourlor's Greatarrow. The special-ammo step and
the era choice go to the list authors.

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H1 | Damage = source × 4 (the Reqtificator's factor baked in) | Dead: 4 / 55 |
| H1b | Damage = source × 5 | Partial: 16 / 55. It is WAR's implicit factor, so it overlaps H2 |
| H2 | = WAR value for the material keyword (bound arrows included) | 16 / 55; 18 / 55 with the material read from the name too |
| H2b | = Requiem.esp (pre-WAR) value for the material | 12 / 55; 17 / 55 with the name too; 9 hit only this era |
| H2e | Either era's table | **Kept**: 27 / 55; 21 / 22 on plain records (with ×5) |
| H3 | = the damage the tier keyword implies in WAR | Dead as a rule: 16 / 55 |
| H4 | Round step (×10 arrows, ×12 bolts) | 33 / 55: describes, decides nothing |
| H5 | Damage hand-set with `AD3B2D` | **Kept**: 55 / 55 |
| H6 | Patched ammo also lands in the RFTI output (double ×4) | Dead: 0 / 55. The RFTI output holds only the 11 un-keyworded vanilla / REQ_NULL records |
| H7 | Live winner equals ARR's value (nothing overrides it) | **Kept**: 55 / 55 |
| H8 | = the WAR value of the vanilla arrow with the same source damage | Dead: 14 / 55 |
| H9 | Bolt premium (× 1.2 / × 7/6) applied to bolt-keyworded ammo | Partial: 9 / 23 sit on a bolt table. The plain misses are Val Serano's bolt (70) and Glenmoril's Forsworn spear (50, the arrow value); the rest are gun specials |
| H10 | Arrow v bolt decides the scale | Folded into H9; not a separate split |
| H11 | Within ±10 of the WAR material value | 31 / 55: the specials sit just above |
| H12 | Projectile explosion → raised | Weak: off-table 12 / 26 with an explosion v 9 / 29 without |
| H13 | Enchanted (`REQ_NoArrowRecovery`) → raised | Weak: 9 / 19 off v 12 / 36 |
| H14 | Heavy projectile (gravity > 0.5) → raised | Weak: 8 / 16 off v 13 / 39 |
| H15 | Special / named ammo → off table | **Kept** as the residual's shape: 20 / 33 special off-table v 1 / 22 plain (name pattern, so judged, not measured) |
| H16 | Gold value tracks damage | Dead: value 0 … 40 at every damage |
| H17 | AmmoWeight keyword tracks damage | Dead: Light carries 58 … 80, Medium 50 … 90 |
| H18 | Era by patch file date | Untestable: every patch file dates from the install (2026-09-12) |

**Special-ammo sub-split (over the 21 residual rows unless stated):**

| # | Hypothesis | Result |
|---|---|---|
| S1 | Offset = Requiem's own special offset (its enchanted / named / elemental ammo keep the base: 0) | Dead: 0 / 21 |
| S2 | Next rung up the material's ladder (either era) | Dead: 3 / 21 (Auroran, both Ebony Greatarrows) |
| S2b | Two rungs up | Dead: 0 / 21 |
| S3 | Base + explosion damage | Dead: 0 / 21 (the Vigilant explosions do 50) |
| S3b | Base + 10 × the explosion enchantment's tier | Dead: 0 / 21 |
| S4 | Heavy arrows priced as the material's bolt | Dead: 0 / 21 |
| S5 | Base + 5 × (source − vanilla source of the material) | Dead: 0 / 21 |
| S5b | Base + 1 × that delta | Dead: 4 / 21 |
| S5c | Base × source / vanilla source (keep the mod's own ratio) | Dead: 4 / 21, all gun rounds the gun rule already covers |
| S6 | Slow (< 3600) or heavy (gravity > 0.5) projectile → off-table | **Kept as a marker**: 12 / 21 v 8 / 34 on-table. Sets no amount |
| S7 | Value ratio = damage ratio v the material | Dead: 2 / 21 |
| S8 | Lands on some Requiem rung, any material | **Kept as shape**: 15 / 21. Off every rung: Bone 58, Bourlor's 65, Kyne's 76, the three 10s |
| S9 | Guns: max(10, source × 5), standard bullet 84 | **Kept**: 11 / 16 gun rows |
| S9b | Daedric-prince gun rounds: source × 5 + 10 | Lead: 3 / 3, but other custom-enchanted rounds get +0 |
| S9c | Gun round priced from its gun's damage | Dead: rifles sit at 70 (Hist / Jillian 90); rounds run 10 … 100 with no link |
| S10 | Named / unique ammo → next material rung | Dead: Kaidan's −2, Jyggalag −1 |
| S11 | A per-mod ratio (Vigilant ×3.67) | Weak: 6 / 10 Vigilant rows; logs and Bourlor's Greatarrow miss |
| S12 | Era tracks the patch | **Kept**: 0 / 12 plugins mix eras once Tonitrus is read as ×5. Patch and source mod are one-to-one, so the two can't be told apart |
