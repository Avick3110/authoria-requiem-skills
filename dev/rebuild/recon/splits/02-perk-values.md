# Split hunt 2: perk effect values

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`../census/stage2/PERK-AI.md`) found perk
multipliers "always shrink, toward ~1.1–1.3, only about half on Requiem steps", and left the landing value open.
This round tested hypotheses first. Scripts and data stay local in the eval harness (`census/splits/perk_*`).*

## Scope

- **Corpus:** 15 patches, 226 effect changes. Of these, 153 pair cleanly before → after. Pairing is by entry point,
  operation, rank, priority and condition set, so 1H / 2H and tiered entries don't cross-match.
- **Concentration:** Vigilant, Unslaad, Glenmoril and LOTD carry most of it.
- **Delivery:** almost every perk comes from an item (conditions on a weapon keyword, `WornHasKeyword` or
  `GetEquipped`), not a skill tree.
- **Held-out data:** none touches perks.

## Answer: four rules, one open residual

| # | Rule | Evidence | Strength |
|---|---|---|---|
| 1 | **Benefits are cut and drawbacks are kept.** A benefit's strength (`|v−1|`) is reduced. A drawback on the same items (more incoming damage, etc.) is left or even raised | Benefits weakened 95 / 100. The 4 left alone are already at 10% (×1.1), plus one falling-damage perk. Drawbacks kept or raised 3 / 4 (Clutch ×1.15 → ×1.2, ×1.25 and ×2 kept). Augmented Destruction's +50% incoming → +30% is the exception | Strong for benefits. Drawbacks n = 4 |
| 2 | **Benefits roughly halve onto a 5% step, with a 10% floor** | Source 20% → 10% in 11 / 12. Half the source rounded to 5% is exact on ~40% and within one step on ~77%. LOTD's Visage series is a clean proportional cut (stagger reduction exactly halved; crit-chance reduction × ⅔) | Strong as a tendency, not exact |
| 3 | **Armour penetration (`ModTargetDamageResistance`) snaps to Requiem's own penetration steps** {0.5, 0.65, 0.7, 0.75, 0.8, 0.9} | After-value on a Requiem step 16 / 17, against before-values 7 / 17. The miss is AP65 = 0.4 (60% penetration), which breaks the ladder next to AP60 = 0.65 and AP70 = 0.5 | Strong |
| 4 | **Flat "+X damage v a creature type" adds are raised to a table: 40 general, 45 v dragons, 50 for sun v undead and Hircine v beasts.** 1H and 2H variants get the same value | Glenmoril Falmer / Nerevarine / Jh / anti-beast / anti-undead / anti-hist 20–25 → 40. Dragon: Glenmoril 20 → 45 **and** Unslaad 20 → 45. Sunlight 25 → 50, Hircine spear 20 → 50. Unslaad Ice Breaker tiers 10 / 20 / 30 → 20 / 30 / 40. Requiem nulls vanilla's silver perk (`REQ_NULL_SilverPerk`, 0 effects), so there is no Requiem comparable; the raise matches Requiem's ×6 weapon damage | Strong (two plugins agree on 45) |

**Crit chance (flat Add):** 50 / 25 → 15 in 3 of 4 cases (Runic 2H → 20).

**Open residual:** benefits with the same source strength land on different steps. For a 50% source the result
ranges from 10% to 30%:
- 30%: the Clutch elemental gloves;
- 10%: Chef Hat alchemy, Critical Perk crit damage, Alessia's helm v dragons;
- 20–25%: most others.

No record property tested explains it (log below). It goes to the list authors.

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H0 | Halve the bonus exactly | Dead as exact (18 / 82). Revived as "halve, onto 5% steps" (rule 2) |
| H1 | Uptime: always-on v situational v target-type v progression | Dead: median kept 0.44–0.52 across classes |
| H2 | Source strength bands | Weak: big sources are cut more (>50%: 0.37 kept), which is mostly the rounding |
| H3 | Offence v defence | Dead (0.46 v 0.47) |
| H4 | Delivery: weapon keyword / worn armour / specific item / none | Dead (0.45–0.58) |
| H5 | Plugin (author pass) | Weak: Vigilant 0.43, Unslaad 0.53, LOTD 0.59; within-plugin scatter remains |
| H6 | Entry point | Explains AP (rule 3) and flat adds (rule 4); the rest overlaps |
| H7 | A target band rather than a ratio | Landings sit on {10, 15, 20, 25, 30}, which is rule 2's rounding |
| H8 | The carrying item's value (tier) | Dead: 44 resolved items; a 1,400-value item keeps 30%, a 3,500-value one keeps 10% |
| H9 | Unique carrier v several carriers; enchanted carrier | Dead (0.42 v 0.50; 0.60 v 0.50, small n) |
| H10 | Power budget per perk (number of effects in the perk) | Dead (0.40–0.50, flat) |
| H11 | Benefits changed per perk | Dead |
| H12 | Snap to the values Requiem uses on the same entry point | Dead overall (after 56% in set v before 63%), **except AP** (rule 3) |
| H13 | Drawback v benefit | **Kept** (rule 1) |
| H14 | 1H / 2H collapse to one value | **Kept** for flat adds and multipliers (Falmer, Nerevarine, Warcry, Fist-Hammer) |

## Suspected bugs (for review, not filed)

- **AP65 = 0.4** (`zzzRevPerkArmorPiercing65`, Glenmoril). It should sit between AP60 (0.65) and AP70 (0.5); 0.6
  would fit. Likely penetration typed as a multiplier.
- **Dac0da `zDcdPerkDragonKiller1H`: +15 → +80 v dragons.** Every other dragon perk lands at 45.
- **Vigilant `zzzCHcrExtraDamageNPCs` / `…Molag…`: ×2.5 / ×3 → ×1.0** (effect removed in place). Possibly
  deliberate; check.
