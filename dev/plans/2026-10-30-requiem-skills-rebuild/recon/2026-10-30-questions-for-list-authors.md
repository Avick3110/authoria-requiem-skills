# Questions for the list authors (from the census split hunts)

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: LIVING (append a section per split; record answers inline). For Heisen and Aaron.*

## How these were produced

We mined every handmade ARR Requiem patch and tested hypotheses against the records first. Each question below is
what was left after that: a split that no record property explains, or a value that looks like a slip. The evidence
and the dead hypotheses are in each split's doc, linked per section.

Answer in a line where you can. "Don't know" or "feel" are useful answers too: they tell the rebuilt skills to
treat the case as a judgement call rather than a rule.

---

## NPCs ([split 1](2026-10-30-split-npc-workflow.md))

What the records already show: `AlreadyReqtified` plus the build's kit goes on an NPC exactly when it doesn't
inherit its SpellList from a template. That holds for 99.5% of 5,163 NPCs.

1. **Templates added to untemplated NPCs.** Some untemplated NPCs were given a template instead of a stat block:
   345 NPCs, mostly Grand Solitude (65) and Animated Carriages (109). Examples:
   - the Grand Solitude temple scribes on `0132A2:Skyrim.esm`;
   - the Grand Solitude Thieves of the Wolf on Requiem's bandit template `039CFD:Skyrim.esm`.

   What decided "template it" over "stat-set it"? Is the bandit template on those civilians intended?
2. **Kit without the marker.** 21 NPCs carry the hand kit (47–56 perks) but no `AlreadyReqtified`, 14 of them
   Glenmoril Solstheim bosses and encounters (e.g. `zzzRevBossSEEldenKing`). The build adds nothing they already
   carry, so it's harmless. Oversight, or deliberate?
3. **Bake v upstream.** Every hand patch bakes NPCs, while the July 2026 skill-made patches (Gray Cowl, Val Serano,
   Apocalypse, Bruma) leave them for the build. Is baking the convention you want the rebuilt skills to follow?

## Perk values ([split 2](2026-10-30-split-perk-values.md))

What the records already show:
- item-perk benefits are roughly halved onto 5% steps with a 10% floor;
- drawbacks are kept;
- armour penetration snaps to Requiem's steps;
- flat damage v a creature type is raised to 40 (45 v dragons, 50 for sun / Hircine).

4. **How strong to keep a benefit.** Effects that started at +50% ended anywhere from 10% to 30%:
   - Clutch elemental gloves (Vigilant): ×1.5 → **×1.3**;
   - Chef Hat alchemy (Vigilant): ×1.5 → **×1.1**;
   - Critical Perk crit damage (Vigilant): ×1.5 → **×1.1**;
   - Unslaad Augmented Fire 3: ×1.5 → **×1.2**.

   What decided the strength: the item, what it stacks with, feel?
5. **The flat-damage table** (40 / 45 dragons / 50 sun and Hircine). Is that a table you use? If so, what else is on
   it?
6. **AP65 = 0.4** on Glenmoril `zzzRevPerkArmorPiercing65` sits between AP60 = 0.65 and AP70 = 0.5. Typo for 0.6?
7. **Dac0da dragon killer +15 → +80**, where every other dragon perk got 45. Intended?
8. **Drawbacks.** Downsides were left alone or raised (Clutch incoming damage ×1.15 → ×1.2). Is "cut the upside,
   keep the downside" the rule?

## Armour AR 0 ([split 3](2026-10-30-split-armour-ar0.md))

What the records already show, and Aaron's stated reason (modular sets mustn't stack past their tier), agree. A
piece keeps AR only in a core slot (body, head, hands, feet, shield, circlet). Extra slots, clothing, replicas and
wigs go to 0. This holds for 96.8% of 3,531 pieces.

9. **Extra-slot pieces that kept AR.** Each of these looks like a set's armour layer rather than an add-on:
   - Twilight Princess cloaks (slot 46, cuirass keyword, AR 77.55);
   - padding pieces in slot 56 (FVO Fur Padding 50, Sigrin Shoulder Padding 25, Sparrow's Mage Padding 50,
     Penetrator Padding 200, Blood Horker vest 50);
   - Vigilant's Witch Hunter's Mask in the amulet slot (60).

   Is "an extra-slot piece that is the set's real armour layer keeps AR" the exception rule, or were these missed?
10. **Creature and NPC gear.** Glenmoril's troll weapons and Gun Knight arms (modelled as armour in ring / amulet /
    unnamed slots), Draugr brute helms on the Hair slot, and Moonpath's creature skins all kept AR in extra slots.
    Does the AR 0 rule apply only to gear the player can wear?
11. **Circlet hats that were zeroed.** Vigilant's Alessian / Marukhati masks, Ashe's Cumulative Scaled Helmet,
    Unslaad's Blades Spy Headband and Glenmoril's Witch Hunter Hat went to 0, while most circlet-slot hats kept AR.
    What decided these?
12. **Mage Core** left token AR on its armlets (5) and mantles (0.5), which are clothing in extra slots. Intended?

## Weapon enchantment charge ([split 4](2026-10-30-split-enchant-charge.md))

What the records already show:
- generic enchanted weapons use Requiem's 500 × enchantment tier;
- staves use Magic Redone's 500 / 1000 / 2000 / 3000 / 6000 by tier;
- named uniques go onto the same ladder with a ceiling of 3000;
- removed or scripted enchantments get no charge (unlimited).

13. **Uniques: 3000, lower, or unlimited?** Some named uniques that keep a normal enchantment were cleared to
    unlimited (LOTD's Honorblade, Black Ice, Shadowrend, Trinimac's Might). Others landed below 3000 (Umaril's Sword
    10,000 → 1,500; Sword of Prophet Marukh 5,000 → 2,000). Frost Sword of Mara went *up* (5,000 → 10,000). What
    decides a unique's charge?
14. **Unique staves one rung up.** Several unique staves sit one step above their Magic Redone tier (Winterhold
    staves tier 3 → 3000, Dragon Priest staff tier 4 → 6000). Is "unique staff = one rung above its tier" the rule?
    Bruma's goblin staves went off the ladder (tier 1 → 1500, tier 2 → 1750). Intended?
15. **Dawnguard Arsenal elites at 2× the regular charge (4000 v 2000).** A rule for "elite" variants generally?

## Recipe retirement ([split 5](2026-10-30-split-recipe-retirement.md))

What the records already show, and Aaron's account, agree on the shape:
- retirement is a per-piece curation call;
- degraded copies (Rusty, etc.) and colour variants are thinned;
- armour-only mods are retired more;
- 326 items are retired for crafting but can still be tempered;
- 457 retired items can be obtained elsewhere (outfits, lists, containers, NPCs).

16. **Retired and obtainable nowhere, in COCKS-listed mods (250 items).** The full list is in
    [`census/recipe-orphans.csv`](census/recipe-orphans.csv), sorted with these first. COCKS gives them a forge
    category, but their recipes are retired and nothing distributes them. The largest groups are Twilight Princess
    (123, incl. the Alt Textures addon), Apex Khajiit (24), FVO Cloth (21), Wine Duchess (11), Arcane Mage (10),
    Sigrin (9) and Moon Monk (7). Left over from before COCKS, i.e. should they be craftable?
17. **Retired and obtainable nowhere, in mods not on the COCKS list (439 items).** FurSet by Keung (all 225),
    Vigilant (92, e.g. the Vigilant / Elite / Elder Vigilant sets, Knight of Stendarr), Bruma's BSAssets (69),
    BlanketScarf (18), Pig Iron (8). Deliberate, or no-crafting-era leftovers?
18. **The two retire methods.** `GetLevel() == 0` is used for whole-mod retirement; Requiem's `AD3B01` workbench is
    used selectively. Is there a reason to prefer one, or should the rebuilt skills standardise on one?

## Death-item loot ([split 6](2026-10-30-split-death-item-loot.md))

What the records already show:
- gold and treasure sublists stay on humanoids and come off creatures, extending Requiem's own
  `LootSmallTreasure10` rule;
- generic humanoid gear (potions, scrolls, lockpicks, arrows) comes off regular enemies, while bosses keep more of
  it;
- species parts stay.

19. **Bosses keeping potions.** Boss creatures (Glenmoril's Wolf Brother, Wendigo, Skaven King) keep
    `LItemPotionAllBest` while regular creatures lose it. Is "bosses keep a reward drop" the rule?
20. **Humanoids losing gear.** Modded humanoid enemies also lose potions, scrolls and lockpicks (64%), while they
    keep their gold. Why strip the gear on humanoids? Is it a balance or economy choice?
21. **Plugins that don't strip.** Dac0da leaves 25 of 35 death lists untouched, and LOTD all 6. Deliberate, or
    not revisited?
