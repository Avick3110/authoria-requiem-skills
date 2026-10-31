# Questions for the list authors: Heisen's answers

*Author: Heisen (answers) and Aaron (+ Claude session) (questions) · 2026-10-31*

*Class: ARCHIVE (the answered copy, verbatim apart from link paths). Heisen answered inline, indented under each question, and replaced some sections with a note. He deleted question 36 (the Jillian lines), so his 36 is the sent doc's 37. The dispositions, by the sent doc's numbers, are in [`questions-for-list-authors.md`](questions-for-list-authors.md).*

## How these were produced

We mined every handmade ARR Requiem patch and tested hypotheses against the records first. Each question below is
what was left after that: a split that no record property explains, or a value that looks like a slip. The evidence
and the dead hypotheses are in each split's doc, linked per section.

50 questions so far, from 13 split hunts and one NPC family check (2026-10-30 and 2026-10-31). Where two splits
raised the same question, it is asked once and cites both.

Answer in a line where you can. "Don't know" or "feel" are useful answers too: they tell the rebuilt skills to
treat the case as a judgement call rather than a rule.

---

## NPCs ([split 1](splits/01-npc-workflow.md))

What the records already show: `AlreadyReqtified` plus the build's kit goes on an NPC exactly when it doesn't
inherit its SpellList from a template. That holds for 99.5% of 5,163 NPCs.

1. **Templates added to untemplated NPCs.** Some untemplated NPCs were given a template instead of a stat block:
   345 NPCs, mostly Grand Solitude (65) and Animated Carriages (109). Examples:
   - the Grand Solitude temple scribes on `0132A2:Skyrim.esm`;
   - the Grand Solitude Thieves of the Wolf on Requiem's bandit template `039CFD:Skyrim.esm`.

   What decided "template it" over "stat-set it"? Is the bandit template on those civilians intended?
   (Split 7 narrowed both halves: questions 22 and 25. Answer there.)

		HEISEN:
			This is very subjective, an NPC is given a template when his function 
			is exactly the same as another NPC in base Skyrim.
			Grand solitude for example has Citizen NPCs, giving them a citizen 
			will make reqtificator skip them, freeing a master entry from the 
			reqtificator Output.
			To be able to properly hand out templates, one should know the
			function of the NPC being balanced, as well as the function of the 
			template donor.
			Another use case is when an NPC is already using a template, but does 
			not have the "Use Stats" and "Use Spell List" ticked, if the NPC does 
			not have unique spells these can be ticked and the npc is patched this 
			way.
2. **Kit without the marker.** 21 NPCs carry the hand kit (47–56 perks) but no `AlreadyReqtified`, 14 of them
   Glenmoril Solstheim bosses and encounters (e.g. `zzzRevBossSEEldenKing`). The build adds nothing they already
   carry, so it's harmless. Oversight, or deliberate?
			HEISEN:
				just an oversight, these are NPCs added by an update in glenmoril and some other big mods, they were patched, but not given the req_AlreadyReqtificated keyword, i was simply to lazy to clean up the reqtificator output.
3. **Bake v upstream.** Every hand patch bakes NPCs, while the July 2026 skill-made patches (Gray Cowl, Val Serano,
   Apocalypse, Bruma) leave them for the build. Is baking the convention you want the rebuilt skills to follow?
			HEISEN:
				I dont understand this, see notes on perk packages and REQ_AlreadyReqtificated below.

HEISEN NOTE:
REQ_AlreadyReqtificated, and how to handle NPC packages:
the skill should NOT add REQ_AlreadyReqtificated to any rebalanced NPC, the skill will rebalance the NPCs, and give them appropriate perks and stats based on their classification, then it FALLS ON THE USER to rerun reqtificator and add the generic perks that reqtificator adds, this does not meaaan that the skill doesnt add perks, it does, but not the generic ones.
Example: look at how requiem itself handles vanilla NPCs and Encounters, then look at the "Requiem for the indifferent.esp" which is the reqtificator tool output, you will notice that requiem gives NPCs and Encounters a certain perk, skill, and stat set, then reqtificator only adds the generic perks to every NPC.
The skill should NOT skip an NPC because he has an already empty perk package/ unmodified stats
The skill should be able to simultaneously remove null/unnecessary perks from NPCs and add new perk packages to the same record.
The skill should also handle actor effect (Spells), and it should NEVER, EVER touch factions.
The skill should only flag Modded spells/perks (any spell or perk that are not from the base game) and never remove them, asking the user what to do with them.
After the user runs reqtificator, he can choose to add the REQ_AlreadyReqtificated keyword then move the record into his own seperate plugin, making reqtificator skip them on the next run, or not, we do it for better housekeeping and to avoid the master limit, the user doesnt have to.

## Perk values ([split 2](splits/02-perk-values.md))

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
	   HEISEN:
	   this were done by hand, vigilant does these buffs in a very nontraditional way, so i balanced them by feel.
	   if they can be compared with another buff added by requiem and its addons, then that should be taken as a comparison.
5. **The flat-damage table** (40 / 45 dragons / 50 sun and Hircine). Is that a table you use? If so, what else is on
   it?
		HEISEN:
			I dont understand the question, and it shouldnt be pointed to me, you have requiem and it's addon to derive a proper damage table for these, and derive a proper balance table to compare with and match when you balance any mod.
6. **AP65 = 0.4** on Glenmoril `zzzRevPerkArmorPiercing65` sits between AP60 = 0.65 and AP70 = 0.5. Typo for 0.6?
		HEISEN:
			i dont remember, skip it.
7. **Dac0da dragon killer +15 → +80**, where every other dragon perk got 45. Intended?
		HEISEN:
			i dont remember, skip it.
8. **Drawbacks.** Downsides were left alone or raised (Clutch incoming damage ×1.15 → ×1.2). Is "cut the upside,
   keep the downside" the rule?
		HEISEN:
			No, the rule is balance, and vigilant, as well as the other VICN mods do not skim on extremely high buff values, so they were made to be closer to requiem's buffs.
			These will likely be remade anyways, see not below.

HEISEN NOTE:
	the strongest reference you have is NOT the hand made patches, it is requiem, and it's addons, these are the base of every patch made for requiem, obvousily.
		Addons take precedence when patching their family, for example:
			Requiem - Magic Redone.esp handles spells and magic effects and staves and other magic related things
			the Requiem Vampires Tweak mod handles vampire NPCs and their level, stats, perks, etc.
			etc.etc.etc.etc.
	the hand made patches tend to have inconsistencies, which is expected, u should look at the general rules instead of fixating on the exceptions.
## Armour AR 0 ([split 3](splits/03-armour-ar0.md))

What the records already show, and Aaron's stated reason (modular sets mustn't stack past their tier), agree. A
piece keeps AR only in a core slot (body, head, hands, feet, shield, circlet). Extra slots, clothing, replicas and
wigs go to 0. This holds for 96.8% of 3,531 pieces.

9. **Extra-slot pieces that kept AR.** Each of these looks like a set's armour layer rather than an add-on:
   - Twilight Princess cloaks (slot 46, cuirass keyword, AR 77.55);
	   - HEISEN:
		   - This cloak is not found in the game, if it is, it's only on serana, just an exception to the rule
   - padding pieces in slot 56 (FVO Fur Padding 50, Sigrin Shoulder Padding 25, Sparrow's Mage Padding 50,
     Penetrator Padding 200, Blood Horker vest 50);
	   - HEISEN:
		   - These need an author's eyes or an intelligent skill, things that can resemble a padding in game can be made to be like the paddings added by "Requiem - Weapons and Armors Redone.esp"
		   - They should share the equipment slot, the tier of the padding is subjective, but it should match the weight class of the rest of the armor.
		   - They should already take a non traditional slot, and should be an addon to the original armor set.
		   - It should preferably cover the waist/shoulder area of the body.
   - Vigilant's Witch Hunter's Mask in the amulet slot (60).
	   - HEISEN:
		   - Yeah, just an exception to the rule.

   Is "an extra-slot piece that is the set's real armour layer keeps AR" the exception rule, or were these missed?
10. **Creature and NPC gear.** Glenmoril's troll weapons and Gun Knight arms (modelled as armour in ring / amulet /
    unnamed slots), Draugr brute helms on the Hair slot, and Moonpath's creature skins all kept AR in extra slots.
    Does the AR 0 rule apply only to gear the player can wear?
			HEISEN:
				The skill should always skip any non-playable weapons and armors, the player cant use them, so they dont matter, reqtificator will buff their values appropriately.
11. **Circlet hats that were zeroed.** Vigilant's Alessian / Marukhati masks, Ashe's Cumulative Scaled Helmet,
    Unslaad's Blades Spy Headband and Glenmoril's Witch Hunter Hat went to 0, while most circlet-slot hats kept AR.
    What decided these?
			HEISEN:
				Likely an oversight, or an armor that is not found in game, or a clothing piece, cant tell.
12. **Mage Core** left token AR on its armlets (5) and mantles (0.5), which are clothing in extra slots. Intended?
			HEISEN:
				They either cant be found in game, or just an oversight.

## Weapon enchantment charge ([split 4](splits/04-enchant-charge.md))

What the records already show:
- generic enchanted weapons use Requiem's 500 × enchantment tier;
- staves use Magic Redone's 500 / 1000 / 2000 / 3000 / 6000 by tier;
- named uniques go onto the same ladder with a ceiling of 3000;
- removed or scripted enchantments get no charge (unlimited).

13. **Uniques: 3000, lower, or unlimited?** Some named uniques that keep a normal enchantment were cleared to
    unlimited (LOTD's Honorblade, Black Ice, Shadowrend, Trinimac's Might). Others landed below 3000 (Umaril's Sword
    10,000 → 1,500; Sword of Prophet Marukh 5,000 → 2,000). Frost Sword of Mara went *up* (5,000 → 10,000). What
    decides a unique's charge?
			HEISEN:
				Stave and enchantment balancing is extremely subjective.
				Rather than focusing on hand made patches here, study Requiem - Magic Redone.esp properly and derive proper enchantment and patterns.
				my thought process:
					Modded enchantments attached to weapons that have a similar vanilla effect get switched to it's vanilla counterpart.
					Enchantment amount (the 3000, 10000, etc) are extremely subjective, the enchantment cost needs to be looked at to decide an appropriate value, again, look at magic redone and derive proper formulation for these.
14. **Unique staves one rung up.** Several unique staves sit one step above their Magic Redone tier (Winterhold
    staves tier 3 → 3000, Dragon Priest staff tier 4 → 6000). Is "unique staff = one rung above its tier" the rule?
    Bruma's goblin staves went off the ladder (tier 1 → 1500, tier 2 → 1750). Intended?
			HEISEN:
				Yes, intended, as long as they dont go overboard, the stave can be a bit stronger than a vanilla counterpart, again, very subjective, and again, see Magic Redone for a proper patching workflow here.
15. **Dawnguard Arsenal elites at 2× the regular charge (4000 v 2000).** A rule for "elite" variants generally?
			HEISEN:
				No, it just made sense for this specific set and their elite counterparts, these things need an author's eye or an intelligent skill, or if possible, deriving a proper pattern to handle them by looking at requiem and it's addons.

## Recipe retirement ([split 5](splits/05-recipe-retirement.md))

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
			HEISEN:
				No, they are retired for a reason, keeping them in the COCKS configuration costs nothing, either we dont want them in the game, or they are unnecessary variants.
17. **Retired and obtainable nowhere, in mods not on the COCKS list (439 items).** FurSet by Keung (all 225),
    Vigilant (92, e.g. the Vigilant / Elite / Elder Vigilant sets, Knight of Stendarr), Bruma's BSAssets (69),
    BlanketScarf (18), Pig Iron (8). Deliberate, or no-crafting-era leftovers?
			HEISEN:
				they are retired for a reason, keeping them in the COCKS configuration costs nothing, either we dont want them in the game, or they are unnecessary variants.
18. **The two retire methods.** `GetLevel() == 0` is used for whole-mod retirement; Requiem's `AD3B01` workbench is
    used selectively. Is there a reason to prefer one, or should the rebuilt skills standardise on one?
		HEISEN:
			Using requiem's keyword is better.

	NOTE:
		The skill should should be able to simulatneously remove, add, and add from a blank perk condition list, same goes for ingredients.

## Death-item loot ([split 6](splits/06-death-item-loot.md))

What the records already show:
- gold and treasure sublists stay on humanoids and come off creatures, extending Requiem's own
  `LootSmallTreasure10` rule;
- generic humanoid gear (potions, scrolls, lockpicks, arrows) comes off regular enemies, while bosses keep more of
  it;
- species parts stay.

19. **Bosses keeping potions.** Boss creatures (Glenmoril's Wolf Brother, Wendigo, Skaven King) keep
    `LItemPotionAllBest` while regular creatures lose it. Is "bosses keep a reward drop" the rule?
		HEISEN:
			No, its not the rule, it's just how the mod author for these mods disstributed loot, and i thought its fine so i kept it, see Requiem for a proper derivation of how to handle BOSS LOOT.
20. **Humanoids losing gear.** Modded humanoid enemies also lose potions, scrolls and lockpicks (64%), while they
    keep their gold. Why strip the gear on humanoids? Is it a balance or economy choice?
			HEISEN:
				balance choice.
21. **Plugins that don't strip.** Dac0da leaves 25 of 35 death lists untouched, and LOTD all 6. Deliberate, or
    not revisited?
			HEISEN:
				Not revisited and likely unnecessary.

## NPC templates ([split 7](splits/07-npc-d1-templates.md))

HEISEN: See NPC section above.
## Ammo damage ([split 8](splits/08-ammo-damage.md))

What the records already show: mod arrows and bolts are placed on a Requiem material table and carry
`RFTI_Exclusions_NoDamageRescale`. Each patch uses one table only.

26. **Which table: WAR's or Requiem.esp's?** The live winner for Requiem's arrows is Weapons and Armor Redone (Iron
    40, Steel 50, Glass 90, Ebony 100). Vigilant, Snow Elven Arsenal and AFDI use Requiem.esp's older values instead:
    - Vigilant iron arrows 54 v WAR 40;
    - SEA Snow Elven arrow 78 v WAR glass 90;
    - AFDI Ebony NP 90 v WAR 100.

    Were those three made before WAR joined the list? Should new patches use WAR's values, and should these move?
	    HEISEN:
		    WAR wins, any requiem addon that directly changes requiem's values wins, these are either an oversight, or non playable variants so they dont matter, or deliberately buffed/nerfed (Exceptions)
27. **Special arrows.** Requiem gives its own enchanted and named arrows their material's base damage. ARR moves
    special arrows 1–3 rungs up the ladder (7 of 9): Ebony Greatarrow 110 (Ebony 100), Auroran 70 (Elven 60),
    Shaman's 80 (Draugr 50), LOTD Ancient Falmer 70 (Falmer 40). Two go down: Kaidan's 80 (Ebony 100) and the Sword of
    Jyggalag 40 (Steel 50). No tested property (explosion, enchantment tier, speed, value, source damage) sets the
    step. A rule, or a feel call per item?
			HEISEN:
				what do u mean when u say ARR moves them, what mod does it, my rule from the question above this still applies.
28. **Glenmoril gun rounds** are max(10, source × 5), with standard bullets at 84 (11 of 16). The three
    Daedric-prince rounds (Meridium, Oblivium, Herald of Herma-Mora) get a further +10, and the energy bolts get ×4
    (80). Are the +10 and the energy bolts deliberate?
			HEISEN:
				Ofcourse they are deliberate, they are higher tier than the other weapons, the entire balance ladder for glenmoril's guns is extremely subjective and done by hand anyways. 
29. **"Ancient Falmer Arrow" has five values across mods:** Glenmoril ivory 60, LOTD 70, SEA 78, Glenmoril / Dac0da
    snow-elf 90. Should one Requiem comparable (glass 90?) cover all snow-elf / Falmer-make arrows?
			HEISEN:
				Imo, no, it should be stronger than glass, probably a 100 across all Ancient Flamer arrows is sufficient.
				Or just look at what requiem does to dawnguard gear.

## Weapon reach ([split 9](splits/09-weapon-reach.md))

HEISEN: so many unecessary bs, just looks what requiem does to weapon reach and apply it when rebalancing a weapon, based on the weapon type ofcourse, anything else is just an exclusion to the rule.

## Armour rescale, encounter zones and LOTD Visage ([split 10](splits/10-house-conventions-a.md))

What the records already show:
- `AD3B2B` NoArmorRatingRescale sits on 285 of the 288 patched pieces the Reqtificator would actually rescale. It
  never goes on a templated piece, which the build skips anyway.
- Gray Cowl moved its dungeon cells off vanilla's NoResetZone (the no-respawn zone homes use) and left its houses
  there.
- The Visage set was put on Requiem's Ebony heavy tier, and its per-piece set bonuses were cut.

33. **Encounter-zone bands: a rule or a one-off?** (Also [split 13](splits/13-locks-consumables-quest-calls.md),
 HEISEN: 
	 rebalancing encounter zones dones not matter, world is deleveled.
34. **LOTD's own dragon priest masks.** Requiem gives every vanilla dragon priest mask three variants: clothing AR 0,
    light 96, heavy 180. The LOTD patch set Haldriin 170, Vosis 145 and Korthor (light) 75 instead. Those are the
    Reqtificator's own rescale of the source AR, rounded (23 × 6.6 + 18 = 169.8; 19 × 6.6 + 18 = 143.4;
    17 × 3.3 + 18 = 74.1). Vahlok got exactly Requiem's Ebony helmet (200 AR, weight 16, value 2000). Was skipping
    Requiem's mask tier deliberate, or should new dragon priest masks go to 96 / 180?
			HEISEN:
				Intentional but only for the time being, because eventually i want to make the modded dragon priest mask behave like the vanilla ones, with multiple variants, again, requiem and it's addons are the gold standard.
35. **Visage light variant.** The light pieces sit on Requiem's Orcish light tier (cuirass 250, gauntlets and boots
    75, cuirass weight 15). Two things don't fit: the helmet is 120 (Orcish 100, Dragonscale 120), and the gauntlets
    and boots weigh 6 (Orcish 4.5). Deliberate?
			HEISEN:
				How do you expect me to know what the hell a visage light variant is from?
					either deliberate and kept as an exception, or the light variant is not found in game.

## Glenmoril gun charge and retired-record names ([split 11](splits/11-house-conventions-b.md))
HEISEN:
for glenmoril, see above, its highly subjective and deliberate.
36. **Prefix for retired records.** Patches rename retired items and recipes with different prefixes: Vigilant
    `NULL_` (106), FurSet by Keung `REQ_NULL_` (99), LOTD both (65), Bruma `AUTH_NULL_` (225, incl. 129 recipes).
    Is there a prefix you want new patches to use, or is renaming optional?
		HEISEN:
			Just go for REQ_NULL or whatever.

## Magic and race ([split 12](splits/12-magic-race-calls.md))

What the records already show: when a creature ability carries vanilla Resist Damage, the patches drop it if the
race already has a `REQ_Trait_Armor_*` (105 / 109) and convert it to `REQ_AbHide_FortifyArmorRating` if not.

38. **Which bosses keep their armour bonus?** On humanoid boss abilities Resist Damage is dropped 52 / 59 times (e.g.
    Glenmoril `zzzRevAbChampionHircine` 100, Vigilant `zzzAoMAbmolagBal` 125) but converted 7 times (Vigilant
    `zzzCHAbMaraCultistHeavy` 500, Dac0da `zDcdAbArcanist_Fever` 750, `zDcdAbBossSnowElf` 200). Requiem's own boss
    traits carry it (Ebony Warrior 300, Miraak 700). Unique flag, level, boss naming, race armour and magnitude
    don't separate them, and the same kind of boss splits by patch: Dac0da converts its unique Redguard / High Elf
    bosses (3 / 3), Glenmoril drops it on Aradia (Breton), Jazel Lorkhaj (Khajiit) and Boltzmer (High Elf). What
    makes a boss keep it?
			HEISEN:
				These are likely repurposed and added to the spell for the resistances directly, the NULL resist damage is removed and replaced by a fortify armor rating, and then either kept in the NPC record or added to the race record.
				You will not find a consistent method for these by looking at the handmade patches, every author does his mod differently and then we have to improvise to achieve the same result.
				again, the gold standard is requiem and it's addon, you can also look at requiem more enemies, or unique enemies, or something similar to get a better idea of how to deal with boss races and boss NPCs.
				
39. **Vigilant spell tome prices.** Every other patch prices tomes on the Magic Redone ladder by the spell's tier
    (198 / 231: Adept 500–700, Master 1000–2000). Vigilant's 69 tomes sit about ×3 above it at every tier (e.g.
    `zzzCHSpellTomeConjureFlameSpirit`, Novice, 2000; `zzzCHBalConjureDeathbringer`, Expert, 5000; Master up to
    8000). 64 of the 69 teach summons, and the prices loosely track the summoned creature's level. Other mods' summon
    tomes stay on the ladder, and Requiem's own tomes never pass 2000. Priced by creature power on purpose? Same
    question for three master uniques above 2000: Unslaad's Ysgrim Thunderbreath 5000 and Icicle Bomb 2500,
    Dawnguard Arsenal's Gaze of Arkay 2500.
			HEISEN:
				Most vigilant spell tomes are not found in the game, and their prices are random based on feel.
40. **Apocalypse's MGEF keywords.** Requiem tags its player spells selectively: `REQ_NoDurationScaling` on damage
    effects with a duration, `REQ_NoMagnitudeScaling` on cloaks and weapon enchant effects, nothing on buffs,
    summons or instant effects (e.g. NoMag on 19 / 203 PeakValueModifier buffs). The Apocalypse patch tags 424 / 460
    of its MGEFs, instant and buff effects included. Was the blanket tagging for a known runtime need, or would
    Requiem's selective pattern have done the same job?
			HEISEN:
				A bug arised that caused spells to scale much more than intended when the player reaches a high skill level, so there keywords were added.
				you should study how magic redone handles magic effects and apply that.
				NOTE: Nothing is "too similar" or "already balanced" how can something be too similar and already balanced if it lacks keywords and conditions and flags added by requiem it self, dont be lazy.

## Locks, consumables and quest misc ([split 13](splits/13-locks-consumables-quest-calls.md))

What the records already show: of the 15 open calls the stage-2 census left here, these are the ones the records
can't settle. (Its encounter-zone call is merged into question 33.)

41. **Arrow gravity 1.0.** Requiem's projectile gravity follows the arrow's material (0.2 / 0.35 / 0.5 / 0.65; all
    32 Requiem.esp material arrows and bolts), and Glenmoril's patch follows it. The Vigilant and Remiel patches set
    1.0 instead (Bourlor, Kyne and Flame arrows 0.35 → 1.0; Remiel bolt `237ED9` → 1.0), and Vigilant's great / log
    arrows go to 1.25 (e.g. `0BAB16` 0.15 → 1.25). Only Trad's S&S + Rare Curios Requiem patch also uses 1.0 (34 of
    34). Was 1.0 taken from Trad's patch, or chosen for feel? For a new mod arrow: the material value, or 1.0?
			HEISEN:
				just copy whatever projectile data requiem has and dont bother us.
42. **Filler loot in artifact chests.** Glenmoril and Vigilant strip the vanilla lockpick / potion / soul-gem lists
    from their "one unique item" chests (lockpick lists 53 → 0 and 20 → 0, e.g. `zzzRevArtifact*`,
    `zzzCHTreasArtifact_KatanaHell1HFrost`). Unslaad thins them to about one list. Dac0da keeps all filler in 91 of 91
    (e.g. `zDcdTreasDweArtifact_HeatSaber1H`, 13 lists) but removes the bonus spell tome in 17. Requiem's own unique
    chests carry no lockpick list (14 of 14). Strip by default, or each mod on its own? (The container side of split
    6's questions 19–21.)
			HEISEN:
				Very subjective thing, just try to apply how requiem handles loot, if the chest contains an artifact, it makes sense for just the artifact to be found in the chest.
43. **Xarrian lock-break on Bruma chests.** Requiem puts `AA000XarrianLockBreakScriptNormal` only on wooden chests
    and doors: never on its 14 boss chests, Dwemer chests or `TreasStrongBox`. Bruma adds it to 28 BSHeartland
    chests, 12 of them boss, stone or metal (`CYRTreasBanditChestBoss`, `CYRTreasAyleidChestBoss`,
    `CYRSafewithLock`, `CYRTreasStrongBox`), while the plain wooden `CYRTreasBanditChest` has none. Are the boss and
    safe ones meant to be breakable? Should other mods' wooden loot chests get the script too? No patch outside
    Bruma adds it.
			HEISEN:
				The list used to have requiem lockpicking SKSE (bade by ivy) which doesnt listen to that script at all, that mod was removed but it will be added back, you should skip attaching scripts to doors and chests.
44. **Master Trader gold at Bruma inns.** The Bruma patch removes `PerkMasterTraderGold` from 3 inn chests
    (`CYRMerchantBrumaRestfulWatchmanInnChest`, `CYRMerchantAleswellInnChest`,
    `CYRMerchantMountainWatchBunkhouseChest`). Requiem keeps it on all 110 vanilla merchant chests. Intended?
			HEISEN:
				Not intended, again, the gold standard is requiem.
45. **Glenmoril harvest.** The patch nulls the ingredient on the only 4 Glenmoril plants that yielded one (maggot egg
    nest, kin cluster, the two WhiteShade trees), so all 14 Glenmoril flora now give nothing. The 3 ingredients stay
    unconverted (autocalc, source value), and Maggot Egg (`00DB59`) now has no source at all. Deliberate "harvest
    off", or should these have been converted?
				HEISEN:
					Intended, these extra INGR are just more clutter.
46. **BSAssets head parts.** The Bruma patch removes Playable from all 219 playable BSAssets head parts, mouths and
    hairlines included, not only hairs. NPCs still use them. The reason: character-creation clutter, broken on
    player races, or something else?
				HEISEN:
					Intended, they are not meant for player use.
47. **Remiel resist potions.** The magnitudes sit on Requiem's ladder (Resist Fire 30, Resist Shock 30, Resist
    Magic 20), but all three last 120 s where Requiem's last 60 s, and Fortify Sneak lasts 20 s against Requiem's
    60. Is the longer resist duration paying for the drawback each potion carries?
		    HEISEN:
				intended, and specific to this follower only.
48. **Follower bridge.** `Authoria_REQ_MajorFollowerBridge` lists 8 followers that run their own follower systems
    (Inigo, Kaidan, Auri, Xelzaz, Remiel, Lucien, Ashe, Val Serano). The vanilla-framework followers (Wyrmstooth's 4,
    Viriya, Fish-Smell) are not in it. Is the rule "a follower with its own framework gets an alias here"?
			HEISEN:
				Yes, major followers get added to the framework only.

## NPC stat blocks ([family check](splits/14-npc-stage2-family-verification.md))

HEISEN: Stop babbling and just see what requiem does, or its addons, and apply it here.
