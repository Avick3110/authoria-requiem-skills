# Split hunt 4: weapon enchantment charge

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`../census/stage1/WEAP.md` conv 16) found
charges "normalised to 3000 on uniques" next to 1500, 2000, cleared and a Glenmoril step table, and left the split
open. This round tested hypotheses first. Scripts and data stay local (`census/splits/weap_*`, `staves_live`,
`req_ench_weapons`).*

## Scope

- **Corpus:** 382 patched weapons whose charge or enchantment changed. 99 have the staff animation. Bruma,
  LOTD, Glenmoril and Vigilant carry most of them.
- **Comparables:** read live (same snapshot as the census) from Requiem's 5,102 enchanted weapons and the 780 staves
  that carry Magic Redone's tier keyword.

## Answer: three ladders and two exits

| # | Rule | Requiem / MR evidence | ARR evidence |
|---|---|---|---|
| 1 | **Generic enchanted weapons: charge = 500 × the enchantment's tier** (`REQ_Ench_Weapon_<Effect><N>`: 500 / 1000 / 1500 / 2000 / 2500 / 3000) | 3,654 / 3,654 Requiem generic enchanted weapons | Bruma's variants 93 / 102 (3 per tier sit +1000; not chased) |
| 2 | **Staves: charge by Magic Redone tier** (`Nox_KW_Staff_<School><N>`): **500 / 1000 / 2000 / 3000 / 6000** | 563 / 567 non-ARR staves (4 tier-5 staves have no charge) | Only 14 / 38 ARR staves sit on the ladder. Unique staves often sit **one rung above** their tier (Winterhold, Marukh, Ayleid and Adabelle staves tier 3 → 3000; Dragon Priest staff tier 4 → 6000). Bruma's goblin staves were raised off-ladder (1500, 1750) |
| 3 | **Named uniques: onto the same ladder, ceiling 3000** | Requiem's own artifacts sit on 500 … 3000, a few on 5000 | Source above 3000 → 3000 in 23 / 28 (Vigilant 10,000 / 7,500 / 5,000, LOTD 5,000). Low-charge uniques raised onto ladder values in 38 / 54 |
| 4 | **Exit A: no enchantment, no charge.** Removing the enchantment clears the charge | n/a | 15 / 15 |
| 5 | **Exit B: scripted (zero-cost) enchantments get unlimited charge** (charge cleared) | Requiem clears it on Dawnbreaker, the Mace of Molag Bal, Mehrunes' Razor, Volendrung, the Ebony Blade, Auriel's Bow and the bound weapons | 11 / 15 |

**Two domain specifics:**
- **Dawnguard Arsenal's elite weapons are exactly 2× the regular ones** (4000 v 2000). The regulars are already on
  the ×500 rule for tier 4.
- **Glenmoril's 33 "staves" without a tier keyword are guns** (staff animation plus `zzzRevWeapTypePistol`). For
  them, charge works as ammunition, raised by Glenmoril's own step table (+50 to +500). They are not staves for
  rule 2.

## Open residual

Named uniques that keep a non-scripted enchantment land on 1000–3000, or are cleared, with no property tested
explaining which.
- **Cleared:** LOTD's Honorblade, Black Ice, Shadowrend, Trinimac's Might.
- **Landed below 3000:** Vigilant's Umaril's Sword (10,000 → 1,500) and Sword of Prophet Marukh (5,000 → 2,000).
- **Above the ceiling:** Frost Sword of Mara (5,000 → 10,000).

These go to the list authors.

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H1 | Generic charge = 500 × enchantment tier | **Kept** (Requiem exact; Bruma 91%) |
| H2 | Staff charge by MR tier | **Kept** for MR (563 / 567); ARR uniques deviate (rule 2) |
| H3 | Unique staves one rung above tier | Lead: fits 6 of the off-ladder uniques, not Bruma's goblin staves or LOTD's Hrormir / Ancient Tongues |
| H4 | Enchantment removed → charge cleared | **Kept** (15 / 15) |
| H5 | Scripted, zero-cost enchantment → unlimited | **Kept** (11 / 15) |
| H6 | Unique's charge follows its generic enchantment tier (500 × N) | Weak: 30 / 62. LOTD uniques override to 3000 or cleared |
| H7 | Bespoke enchantment: charge by enchantment cost | Dead: 3000 carries costs from 10 to 300 |
| H8 | Uniques capped at 3000 | **Kept** for sources above 3000 (23 / 28) |
| H9 | Elite variant = 2× regular | **Kept** for Dawnguard Arsenal (7 / 7) |
| H10 | Gun "staves" outside the staff rule | **Kept** (Glenmoril pistol keyword) |
