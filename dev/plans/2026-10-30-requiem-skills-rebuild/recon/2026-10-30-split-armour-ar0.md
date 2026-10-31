# Split hunt 3: AR 0 on extra armour pieces

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`census/stage1/ARMO.md` C2) found "AR 0 on
accessories and extra-slot pieces" across 44 plugins and asked whether it is policy. Aaron's reason
(2026-10-30): **balance modular sets, so extra pieces can be worn without lifting a set above its tier.** This
round turned that reason into a testable rule. Scripts and data stay local (`census/splits/armo_*`).*

## Answer: the slot decides

**The rule.** A piece keeps its armour rating only if it occupies a **core biped slot**:
- Body, Head, Hands, Feet, Shield or Circlet;
- or Hair, when the piece carries the helmet keyword.

Everything else goes to **AR 0**:
- extra slots (forearms, amulet, ring, ears, calves, tail, the unnamed slots 44–61);
- clothing;
- replicas;
- wigs.

| | Pieces (source AR > 0) | Rule holds |
|---|---|---|
| Rule-making plugins | 3,531 in 93 plugins | **3,417 (96.8%)**; 64 plugins with no misses |
| Held-out references | 21 | 15 (71%); all 6 misses are Mage Core clothing (below) |

**Details that matter for the skills:**
- **The keyword doesn't rescue an extra slot.** FurSet by Keung's open-finger gloves carry the gauntlets keyword in
  the forearm slot and go to 0. Their full gloves in the hands slot keep AR. The Cimmerian skirt and pauldron carry
  the cuirass keyword in extra slots and go to 0.
- **The circlet slot counts as a head piece.** Hats, masks and headbands in the circlet slot keep AR when they are
  the set's head piece.
- **Combined slot masks must be decoded.** A body piece that also fills an extra slot (e.g. mask 65540 = slot 46 +
  Body) is a core piece. Reading only the named slots misclassifies it.
- **Cosmetic and display items go to 0 even in core slots.** This covers the LOTD and Kaidan museum replicas, Dint
  Hair's and Lion's Mane's wigs (which carry a helmet keyword), and clothing (179 / 189 in core slots, 81 / 89 in
  extra slots).

## Exceptions (114 misses)

| Group | n | Examples | Read |
|---|---|---|---|
| Creature and NPC gear in extra slots keeps AR | ~45 | Glenmoril troll weapons modelled as armour, Gun Knight gatling / rifle arms, Draugr brute helms on Hair without a helmet keyword; Moonpath creature skins | No property separates them: NPC-only status (NonPlayable or non-default race) doesn't (28% v 19% kept). A list-author call |
| Extra-slot set pieces that kept AR | ~40 | Twilight Princess cloaks (slot 46, cuirass keyword) and tassets; padding layers in FVO, Sigrin, Sparrow's Mage, Penetrator, Blood Horker (slot 56); Witch Hunter's Mask (amulet slot) | Contradicts the stated reason. Either deliberate (the piece *is* the set's armour layer) or missed |
| Core-slot pieces zeroed | 15 | Circlet-slot masks / hats in Vigilant, Ashe, Glenmoril, Ruby Flower ("Hat OFF" toggles), Traveler Romper bra | Mostly toggle / alternate-state items and lingerie layers |
| Held-out Mage Core | 6 | Armlets (5), mantles (0.5) kept small AR | Clothing with token AR; the MC patch left it |

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H1 | Core slot keeps AR (Aaron's reason) | **Kept**: 89% with named slots only, 96.8% once masks are decoded and the cosmetic exclusions are added |
| H2 | The part keyword decides instead of the slot | Weaker (94.6%). FurSet shows the keyword doesn't rescue an extra slot |
| H3 | Slot AND keyword | Weaker (94.5%) |
| H4 | One AR-bearing piece per part per set (duplicates zeroed) | Dead: 51 / 102 on the extra-slot keyword pieces, worse than without it |
| H5 | NPC / creature gear exempt | Dead: 28% v 19% kept |
| H6 | Cosmetic / display (replica, wig) → 0 even in core slots | **Kept** (LOTD / Kaidan replicas, Dint Hair, Lion's Mane) |
| H7 | Clothing → 0 | **Kept** (~94%) |

## Relation to the census and the fact sheet

- The census's C2 ("AR 0 on accessories, 44 plugins") is this rule's visible half.
- DISTILLATION fact 7 still stands: Requiem itself keeps vanilla clothing AR. So this is an **ARR house rule with a
  stated reason**, not Requiem practice.
- The A layer should carry it as: "core slots carry the set's AR; extra slots, clothing, replicas and wigs carry 0".
