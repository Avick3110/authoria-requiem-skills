# Questions for the list authors (from the census split hunts)

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: LIVING (append a section per split; record answers inline). For Heisen and Aaron.*

**Status:** all 50 questions sent to the list authors on 2026-10-31. Waiting for answers.

## How these were produced

We mined every handmade ARR Requiem patch and tested hypotheses against the records first. Each question below is
what was left after that: a split that no record property explains, or a value that looks like a slip. The evidence
and the dead hypotheses are in each split's doc, linked per section.

50 questions so far, from 13 split hunts and one NPC family check (2026-10-30 and 2026-10-31). Where two splits
raised the same question, it is asked once and cites both.

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
   (Split 7 narrowed both halves: questions 22 and 25. Answer there.)
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

## NPC templates ([split 7](2026-10-31-split-npc-d1-templates.md))

What the records already show: "template it" or "stat-set it" is decided per mod, not per NPC. 109 of 129 source
mods go all one way, and the mod predicts 98.3% of 3,326 NPCs. Nothing else tested separates the two groups
(creature share, civilians, uniques, AutoCalc, levels, size all overlap).

22. **What makes a mod a "template" mod?** (Sharpens question 1, split 1.) 13 mods get templates: Animated
    Carriages, Animated Ships, CFTO, Grand Solitude, Moonpath, Ashe, IMG Hagravens and several Nimwraith quest
    mods. 56 get a stat block, the marker and the kit: Vigilant, Glenmoril, LOTD, Dac0da, Unslaad, Wyrmstooth, Lux
    Via's Markarth farmers. Is it the kind of mod, the era the patch was made in, or time available?
23. **Nimwraith quest NPCs go both ways.** Dungeon Delver's Pike, Hunt for the Spectre's Ren and Once We Were
    Here's Sillas are templated (e.g. Pike → `REQ_LookTemplate_EncBandit03Boss1HNordM`). Slays-Many-Beasts,
    Walks-Distant-Sands, Dar'balag and War's Folly's preacher are stat-set with a kit (45–101 perks). All are unique
    quest NPCs with similar source stats. Were they patched at different times?
24. **Three Grand Solitude NPCs left stat-set.** `WSAlfbrand` (A69294), `WSEiruki` (A69366) and `WSOngmar` (A69368)
    have the same source shape as their 65 templated neighbours. `WSAlfbrand` also has a Template link (`039CFD`)
    with no template flags, so the link does nothing. Missed, or deliberate?
25. **Civilian or bandit template in Grand Solitude?** (Sharpens question 1's second half, split 1.) Civilians went
    either to a vanilla townsperson (Lisette, Beirand, EvetteSan) or to a bandit look template, and the split follows
    sex: women 16 / 26 on a vanilla NPC, men 24 / 34 on a bandit template. Was that the idea, or should the 26 on
    bandit `039CFD` (e.g. `WSEnbjorf` A68798) be on civilian templates too?

## Ammo damage ([split 8](2026-10-31-split-ammo-damage.md))

What the records already show: mod arrows and bolts are placed on a Requiem material table and carry
`RFTI_Exclusions_NoDamageRescale`. Each patch uses one table only.

26. **Which table: WAR's or Requiem.esp's?** The live winner for Requiem's arrows is Weapons and Armor Redone (Iron
    40, Steel 50, Glass 90, Ebony 100). Vigilant, Snow Elven Arsenal and AFDI use Requiem.esp's older values instead:
    - Vigilant iron arrows 54 v WAR 40;
    - SEA Snow Elven arrow 78 v WAR glass 90;
    - AFDI Ebony NP 90 v WAR 100.

    Were those three made before WAR joined the list? Should new patches use WAR's values, and should these move?
27. **Special arrows.** Requiem gives its own enchanted and named arrows their material's base damage. ARR moves
    special arrows 1–3 rungs up the ladder (7 of 9): Ebony Greatarrow 110 (Ebony 100), Auroran 70 (Elven 60),
    Shaman's 80 (Draugr 50), LOTD Ancient Falmer 70 (Falmer 40). Two go down: Kaidan's 80 (Ebony 100) and the Sword of
    Jyggalag 40 (Steel 50). No tested property (explosion, enchantment tier, speed, value, source damage) sets the
    step. A rule, or a feel call per item?
28. **Glenmoril gun rounds** are max(10, source × 5), with standard bullets at 84 (11 of 16). The three
    Daedric-prince rounds (Meridium, Oblivium, Herald of Herma-Mora) get a further +10, and the energy bolts get ×4
    (80). Are the +10 and the energy bolts deliberate?
29. **"Ancient Falmer Arrow" has five values across mods:** Glenmoril ivory 60, LOTD 70, SEA 78, Glenmoril / Dac0da
    snow-elf 90. Should one Requiem comparable (glass 90?) cover all snow-elf / Falmer-make arrows?

## Weapon reach ([split 9](2026-10-31-split-weapon-reach.md))

What the records already show: most playable weapons get the Requiem class reach × 0.7 (127 of 170 where it makes a
difference). The other 43 keep their own reach × 0.7, and nearly always their own speed (40 / 43).

30. **When does a playable weapon keep its own reach?** Gleamstrike 1.8 → 1.26, Smithscript Greathammer 1.5 → 1.05
    and Vigilant's Molag Bal Spike 1.1 → 0.77 kept theirs, while the Molag Bal Sword (also 1.1) went to 0.7. Size,
    keywords and damage source don't separate them. Is there a rule (e.g. "signature weapon, keep its feel"), or is
    it a per-weapon call?
31. **Six LOTD replicas kept their source reach** while the real weapon got the class value: Honorblade of Chorrol
    0.91, replica 0.98; Chrysamere 0.91, replica 0.77; Daedric Crescent 0.91, replica 0.7. About 120 other replicas
    match class. Deliberate, or did these six miss the pass?
32. **Glenmoril trick weapons.** The whip forms go 3.0 → 1.4 (Threaded Beast Cutter ×4, Parasite of Ja'bal), the
    Threaded Cane 3.0 → 1.05 and the Elegant Cane 1.2 → 0.77. Neither the class nor the source × 0.7 gives these.
    How were they picked? (The NPC Parasite 2.0 → 1.4 suggests "match the NPC version".)

## Armour rescale, encounter zones and LOTD Visage ([split 10](2026-10-31-split-house-conventions-a.md))

What the records already show:
- `AD3B2B` NoArmorRatingRescale sits on 285 of the 288 patched pieces the Reqtificator would actually rescale. It
  never goes on a templated piece, which the build skips anyway.
- Gray Cowl moved its dungeon cells off vanilla's NoResetZone (the no-respawn zone homes use) and left its houses
  there.
- The Visage set was put on Requiem's Ebony heavy tier, and its per-piece set bonuses were cut.

33. **Encounter-zone bands: a rule or a one-off?** (Also [split 13](2026-10-31-split-locks-consumables-quest-calls.md),
    call 5.) Gray Cowl is the only ARR patch that edits encounter-zone levels (6 of 710 zones). It moved 17 dungeon
    cells and one Shanta Filibb house cell onto three new NeverResets zones by quest act: Cyrodiil 15–22, the Ben Erai
    side 18–26, the Alik'r desert dungeons 22–30. It also capped the mod's own zones (`0126F3` 12 / 20, `0126F4` and
    `0126F5` 20 / 30). Requiem's 8 zones have no maximum, and only 5 of 280 vanilla zones have one.
    - Should a skill re-zone any mod that leaves dungeons on NoResetZone? The other 60 NoResetZone cells are left
      alone. Most are homes and LOTD gallery rooms, but a few are dungeon-like: `DBMRkund06`, `DBMDeepholme` (LOTD),
      `AXHiddenGrotto01` (Val Serano).
    - Where do the numbers come from (15 / 18 / 22 minimums, maximums about 8 above, 30 as the ceiling)? And is the
      cap for this quest only, or for every mod dungeon?
34. **LOTD's own dragon priest masks.** Requiem gives every vanilla dragon priest mask three variants: clothing AR 0,
    light 96, heavy 180. The LOTD patch set Haldriin 170, Vosis 145 and Korthor (light) 75 instead. Those are the
    Reqtificator's own rescale of the source AR, rounded (23 × 6.6 + 18 = 169.8; 19 × 6.6 + 18 = 143.4;
    17 × 3.3 + 18 = 74.1). Vahlok got exactly Requiem's Ebony helmet (200 AR, weight 16, value 2000). Was skipping
    Requiem's mask tier deliberate, or should new dragon priest masks go to 96 / 180?
35. **Visage light variant.** The light pieces sit on Requiem's Orcish light tier (cuirass 250, gauntlets and boots
    75, cuirass weight 15). Two things don't fit: the helmet is 120 (Orcish 100, Dragonscale 120), and the gauntlets
    and boots weigh 6 (Orcish 4.5). Deliberate?

## Glenmoril gun charge and retired-record names ([split 11](2026-10-31-split-house-conventions-b.md))

What the records already show: the Glenmoril patch re-stepped its gun lines from +50 to +100 charge per upgrade
level (5 shots at +0, then +1 shot per level; 40 / 40 records, e.g. `zzzRevHunterPistol01` 550 → 600).

36. **The Jillian lines kept the old step.** The Jillian, Anti-Jillian and Histus Gravity Pistol lines (33 records,
    e.g. `zzzRevJillianPistol01 435E43:Glenmoril.esm`) are in the patch (Value, Weight, and enchantment cost
    10 → 100 like every gun) but keep the source +50 step (550 … 1000), i.e. half a shot per level. No record
    property separates them; they are the late Glenmoril batch (FormIDs 34F5xx–435xxx). Missed, or deliberate?
37. **Prefix for retired records.** Patches rename retired items and recipes with different prefixes: Vigilant
    `NULL_` (106), FurSet by Keung `REQ_NULL_` (99), LOTD both (65), Bruma `AUTH_NULL_` (225, incl. 129 recipes).
    Is there a prefix you want new patches to use, or is renaming optional?

## Magic and race ([split 12](2026-10-31-split-magic-race-calls.md))

What the records already show: when a creature ability carries vanilla Resist Damage, the patches drop it if the
race already has a `REQ_Trait_Armor_*` (105 / 109) and convert it to `REQ_AbHide_FortifyArmorRating` if not.

38. **Which bosses keep their armour bonus?** On humanoid boss abilities Resist Damage is dropped 52 / 59 times (e.g.
    Glenmoril `zzzRevAbChampionHircine` 100, Vigilant `zzzAoMAbmolagBal` 125) but converted 7 times (Vigilant
    `zzzCHAbMaraCultistHeavy` 500, Dac0da `zDcdAbArcanist_Fever` 750, `zDcdAbBossSnowElf` 200). Requiem's own boss
    traits carry it (Ebony Warrior 300, Miraak 700). Unique flag, level, boss naming, race armour and magnitude
    don't separate them, and the same kind of boss splits by patch: Dac0da converts its unique Redguard / High Elf
    bosses (3 / 3), Glenmoril drops it on Aradia (Breton), Jazel Lorkhaj (Khajiit) and Boltzmer (High Elf). What
    makes a boss keep it?
39. **Vigilant spell tome prices.** Every other patch prices tomes on the Magic Redone ladder by the spell's tier
    (198 / 231: Adept 500–700, Master 1000–2000). Vigilant's 69 tomes sit about ×3 above it at every tier (e.g.
    `zzzCHSpellTomeConjureFlameSpirit`, Novice, 2000; `zzzCHBalConjureDeathbringer`, Expert, 5000; Master up to
    8000). 64 of the 69 teach summons, and the prices loosely track the summoned creature's level. Other mods' summon
    tomes stay on the ladder, and Requiem's own tomes never pass 2000. Priced by creature power on purpose? Same
    question for three master uniques above 2000: Unslaad's Ysgrim Thunderbreath 5000 and Icicle Bomb 2500,
    Dawnguard Arsenal's Gaze of Arkay 2500.
40. **Apocalypse's MGEF keywords.** Requiem tags its player spells selectively: `REQ_NoDurationScaling` on damage
    effects with a duration, `REQ_NoMagnitudeScaling` on cloaks and weapon enchant effects, nothing on buffs,
    summons or instant effects (e.g. NoMag on 19 / 203 PeakValueModifier buffs). The Apocalypse patch tags 424 / 460
    of its MGEFs, instant and buff effects included. Was the blanket tagging for a known runtime need, or would
    Requiem's selective pattern have done the same job?

## Locks, consumables and quest misc ([split 13](2026-10-31-split-locks-consumables-quest-calls.md))

What the records already show: of the 15 open calls the stage-2 census left here, these are the ones the records
can't settle. (Its encounter-zone call is merged into question 33.)

41. **Arrow gravity 1.0.** Requiem's projectile gravity follows the arrow's material (0.2 / 0.35 / 0.5 / 0.65; all
    32 Requiem.esp material arrows and bolts), and Glenmoril's patch follows it. The Vigilant and Remiel patches set
    1.0 instead (Bourlor, Kyne and Flame arrows 0.35 → 1.0; Remiel bolt `237ED9` → 1.0), and Vigilant's great / log
    arrows go to 1.25 (e.g. `0BAB16` 0.15 → 1.25). Only Trad's S&S + Rare Curios Requiem patch also uses 1.0 (34 of
    34). Was 1.0 taken from Trad's patch, or chosen for feel? For a new mod arrow: the material value, or 1.0?
42. **Filler loot in artifact chests.** Glenmoril and Vigilant strip the vanilla lockpick / potion / soul-gem lists
    from their "one unique item" chests (lockpick lists 53 → 0 and 20 → 0, e.g. `zzzRevArtifact*`,
    `zzzCHTreasArtifact_KatanaHell1HFrost`). Unslaad thins them to about one list. Dac0da keeps all filler in 91 of 91
    (e.g. `zDcdTreasDweArtifact_HeatSaber1H`, 13 lists) but removes the bonus spell tome in 17. Requiem's own unique
    chests carry no lockpick list (14 of 14). Strip by default, or each mod on its own? (The container side of split
    6's questions 19–21.)
43. **Xarrian lock-break on Bruma chests.** Requiem puts `AA000XarrianLockBreakScriptNormal` only on wooden chests
    and doors: never on its 14 boss chests, Dwemer chests or `TreasStrongBox`. Bruma adds it to 28 BSHeartland
    chests, 12 of them boss, stone or metal (`CYRTreasBanditChestBoss`, `CYRTreasAyleidChestBoss`,
    `CYRSafewithLock`, `CYRTreasStrongBox`), while the plain wooden `CYRTreasBanditChest` has none. Are the boss and
    safe ones meant to be breakable? Should other mods' wooden loot chests get the script too? No patch outside
    Bruma adds it.
44. **Master Trader gold at Bruma inns.** The Bruma patch removes `PerkMasterTraderGold` from 3 inn chests
    (`CYRMerchantBrumaRestfulWatchmanInnChest`, `CYRMerchantAleswellInnChest`,
    `CYRMerchantMountainWatchBunkhouseChest`). Requiem keeps it on all 110 vanilla merchant chests. Intended?
45. **Glenmoril harvest.** The patch nulls the ingredient on the only 4 Glenmoril plants that yielded one (maggot egg
    nest, kin cluster, the two WhiteShade trees), so all 14 Glenmoril flora now give nothing. The 3 ingredients stay
    unconverted (autocalc, source value), and Maggot Egg (`00DB59`) now has no source at all. Deliberate "harvest
    off", or should these have been converted?
46. **BSAssets head parts.** The Bruma patch removes Playable from all 219 playable BSAssets head parts, mouths and
    hairlines included, not only hairs. NPCs still use them. The reason: character-creation clutter, broken on
    player races, or something else?
47. **Remiel resist potions.** The magnitudes sit on Requiem's ladder (Resist Fire 30, Resist Shock 30, Resist
    Magic 20), but all three last 120 s where Requiem's last 60 s, and Fortify Sneak lasts 20 s against Requiem's
    60. Is the longer resist duration paying for the drawback each potion carries?
48. **Follower bridge.** `Authoria_REQ_MajorFollowerBridge` lists 8 followers that run their own follower systems
    (Inigo, Kaidan, Auri, Xelzaz, Remiel, Lucien, Ashe, Val Serano). The vanilla-framework followers (Wyrmstooth's 4,
    Viriya, Fish-Smell) are not in it. Is the rule "a follower with its own framework gets an alias here"?

## NPC stat blocks ([family check](2026-10-31-npc-stage2-family-verification.md))

What the records already show: in the Bruma master patch, 467 of 541 re-statted NPCs carry one Requiem NPC's stat
block copied field for field (level, AutoCalc, all skills, offsets, kit). Named townsfolk got Belethor's (L4).

49. **Named townsfolk at level 4.** 40 Bruma townsfolk who were PcLevelMult in the source (farmers, citizens,
    vendors, trainers: Gildin, Horon Maleus, Rolancano) are now fixed L4, copied from Belethor. Vanilla Belethor is
    L4 and Requiem keeps that. But where Requiem itself converts a PcLevelMult civilian it picks 20–25 (Vekel,
    Mikael, Avrusa Sarethi 25; Arnskar, Roggi 20). The same block also went onto 44 named fighters (e.g.
    `CYRArilusQuenti`, a Legate). For a mod's PcLevelMult townsperson: a low vanilla-style level, or Requiem's 20–25?
50. **Tougher-than-analogue creatures.** Some Bruma creatures copy a Requiem NPC's skills but take a hand-set higher
    level and health: hunting dogs L10 on EncDog skills (EncDog is L2), dire wolf L10 on ice-wolf skills (L6),
    minotaurs L50 / L100 with HealthOffset 1,000 / 2,500 on giant / frost-giant skills. When a mod creature is a
    tougher version of a Requiem one, is "copy the analogue, then raise level and health by eye" your practice, or
    should it take the nearest Requiem tier unchanged?
