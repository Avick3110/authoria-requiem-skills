# Split hunt 13: the LOCKS, CONSUMABLES and QUEST-MISC open calls

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The stage-2 census docs `../census/stage2/LOCKS.md`,
`CONSUMABLES.md` and `QUEST-MISC.md` each end with five "Open calls for Aaron" (15 in all). This round triaged
every call, hunted the ones that would change a skill rule, and logged hypotheses for each. Scripts and data stay
local (`census/splits/lcq_*`).*

## Scope

- **Read live**, same snapshot as the census (epoch `e2-58972ca8618e79ea`, eval profile, ksws03 excluded):
  - 269 arrow-type projectiles and 258 ammo winners;
  - 950 ingestible winners;
  - 350 containers defined in Glenmoril, Vigilant and Unslaad, plus 118 in Dac0da (source and winner);
  - Requiem's 17 own containers, 89 vanilla `Treas*` and 110 vanilla `Merchant*` chests;
  - 103 CONT / DOOR winners with the Xarrian script, and BSHeartland's 192 containers;
  - follow-up: S&S's 19 containers and their 106 placed refs; 88 Glenmoril projectiles, 21 ammo and 22 crossbow-type
    guns; 15 follower NPCs' factions.
- **Census data:** the stage-2 ledger rows for Container Items, Flora, Tree and Explosion.

## Triage

| Call | Origin | Kind | Where |
|---|---|---|---|
| L1 loot strip on mod chests | LOCKS 1 (conv 9) | split | §1 |
| L2 Xarrian on mod chests | LOCKS 2 (conv 3) | split | §2 |
| L3 encounter-zone caps | LOCKS 3 (conv 13) | preference (one plugin) | §8; question 5 |
| L4 lockpick script upstream | LOCKS 4 (conv 1) | settled by code | §8 |
| L5 vendor rebalances, Respawns | LOCKS 5 (conv 10, 12) | split | §3; Respawns §9 |
| C1 Blended Shrooms 000807 | CONSUMABLES 1 (C-10) | bug | bugs file |
| C2 Glenmoril nulled flora | CONSUMABLES 2 | preference / lead | §8; question 6 |
| C3 BSAssets head parts | CONSUMABLES 3 (C-12) | preference | question 7 |
| C4 ammo gold value | CONSUMABLES 4 | split | §4 |
| C5 potion side effects, NoAutoCalc | CONSUMABLES 5 (C-8, C-9) | split | §5 |
| Q1 / Q2 projectile gravity and speed | QUEST-MISC 1, 2 (conv 1, 4) | split + bug | §6; gun rounds §10 |
| Q3 follower bridge | QUEST-MISC 3 (conv 8) | preference / lead | §7; question 9 |
| Q4 Gray Fox gate 10 → 15 | QUEST-MISC 4 (conv 7) | Requiem precedent | §8 |
| Q5 Troll MCO SpawnProjectile nulls | QUEST-MISC 5 | source clean-up | §8 |

## Answer

| # | Rule | Requiem / MR evidence | ARR evidence |
|---|---|---|---|
| 1 | **Mod "artifact" chests** (one unique item plus vanilla filler lists) **lose their lockpick, potion and soul-gem filler.** Gold often stays. Vendor and generic chests keep theirs | Requiem's own unique-item chests (`REQ_Chest_*`, `REQ_Container_Treas*`) carry 0 lockpick lists in 14 / 14. Its generic chests keep them: 59 / 279 Requiem CONT overrides carry a lockpick list | Lockpick lists on artifact chests: Glenmoril 53 → 0, Vigilant 20 → 0. Filler cut on 69 / 69 and 20 / 20 artifact chests; Unslaad 46 / 75 (partial). **Dac0da keeps it on 91 / 91** (residual). Merchants keep filler in 31 / 40 |
| 2 | **Xarrian lock-break script goes on plain wooden loot chests** (Chest01, UpperChest, NobleChest models), never boss chests, Dwemer metal, urns, corpses or sacks | Vanilla `Treas*` winners: wooden 15 / 24 carry it; boss `Ruins_LargeChest` 0 / 14, Dwemer 0 / 8, urns and jars 0 / 9, sacks 0 / 7, bodies 0 / ~20, StrongBox 0 / 1. Also 40 doors, mostly wooden | Bruma's 28: 16 follow the wooden-chest rule. 12 go further: 8 boss or Ayleid stone chests, 2 safes, 1 strongbox, 1 merchant. No other patch adds it (other patches override ~300 mod `Treas*` chests) |
| 3 | **Bruma's vendor rebalance is half Requiem alignment, half ARR economy** | Requiem's vanilla apothecaries carry `REQ_LI_VendorApothecary_Min*` (9 / 10); its inns carry REQ_ foods (18 / 21); `PerkMasterTraderGold` is on 110 / 110 vanilla merchant chests | Bruma adds the apothecary minimum lists (2 / 2 apothecaries) and REQ_ foods (3 / 3 inns). It also cuts enchanted jewelry, robes and enchanted blacksmith stock, lowers potion stacks (15 → 10 / 6), and **removes `PerkMasterTraderGold` from 3 inns** (against Requiem) |
| 4 | **Ammo gold value: bow ammo with a Requiem material goes onto Requiem's material ladder** (Iron / Steel 1, Elven 2, Dwarven / Orcish / Silver 3, Glass 14, Ebony / Stalhrim 22, Dragonbone 44, Daedric 55; bolts about ×1.2; enchanted +10 … +40). **Guns: Glenmoril's own step** (source +5 or +10) | WAR / Requiem.esp: 1 value per material and type, 56 / 56 groups | Bow ammo: 10 / 15 changed values on the ladder (±1). Guns: 13 / 14 = source +5 / +10. The census "15-30" band is the Glenmoril guns and Vigilant's great arrows |
| 5 | **Potions: NoAutoCalc on all; the primary effect goes on Requiem's rung; drawbacks are the source mod's design** | Live winners (mostly `Requiem - Alchemy Redone.esp`): 205 / 205 potions and 94 / 96 poisons NoAutoCalc. Restore = duration 20, magnitude 3 / 5 / 8 / 10 / 12. Only 7 / 251 Requiem potions are multi-effect, none with a drawback | ARR 18 / 18 NoAutoCalc. Remiel: 10 / 10 source potions already carry a harmful effect; ARR keeps one on 10 / 10. Primary magnitude on a Requiem rung 9 / 10. Resist durations 120 against Requiem 60 (3 / 3, question 8) |
| 6 | **Projectile gravity follows the material ladder of Requiem's base projectile** (0.2 Elven / Glass / Stalhrim / Forsworn, 0.35 Iron / Steel / Silver / Nordic, 0.5 Dwarven / Orcish, 0.65 Ebony / Daedric / Dragonbone). About 1.0 is only for a slow, thrown spear. **Speed: arrows 3600, bolts 5600, thrown 2500-2600** | Requiem.esp material arrows and bolts 32 / 32 on the ladder; WAR 125 / 134 overall (enchanted Glass / Stalhrim variants follow the weight keyword). Requiem moved the Riekling spear 1.3 / 2000 → 1.0 / 2500. Vanilla is 0.35 everywhere, bolts 5400 | Glenmoril arrows 2 / 2 on the ladder. **A second "1.0" school:** Vigilant's 3 normal arrows and Remiel's bolt 1.0, Vigilant's great / log arrows 1.25. It matches Trad's S&S + Curios Requiem patch (34 / 34 at 1.0). Speed: Remiel and Glenmoril's guns raised to 5600 (5 / 5), Unslaad arrow raised to 5400 (bug) |
| 7 | **The follower bridge holds the custom-framework followers.** Record signal: the follower NPC is outside `PotentialFollowerFaction`, or carries `BardAudienceExcludedFaction`, or ships with `DismissedFollowerFaction` | Requiem's `REQ_Quest_FollowerControl` (`82F3AF`) is referenced by 2 quests: Requiem's lock control and the bridge | The composite signal is 8 / 8 bridge followers v 0 / 7 vanilla-framework followers read (Wyrmstooth's Athir / Shargam, Viriya, Fish-Smell, Dar'taqto, Celann, Marcus Flynn). It is fitted on 15 NPCs, so it is a lead. No framework master in the headers (Inigo, Auri, Val Serano masters are vanilla only) |
| 8 | **Respawns: copy the flag of the vanilla container the record is modelled on** | Requiem changes Respawns on 1 / 279 CONT overrides (re-adds it to `TreasStrongBox`, which USSEP had cleared); vanilla Snow Elf chests 0 / 2 respawn, sacks 3 / 16, knapsacks 6 / 8 (incl. `TreasKnapsack`) | 7 / 7 ARR edits match their vanilla model class: S&S's 4 Snow-Elf-chest "MadRuins" chests, its Ayleid cask and its sack cleared; LOTD's Rain's Knapsack (`MiscPouch`) given Respawns. S&S chests with the same loot lists on a custom model keep Respawns (5 / 5) |
| 9 | **Gun rounds: only the main craftable bullets get the Requiem bolt kit** (speed 5600, radius 0.5, a gravity inside Requiem's bolt band 0.35-0.65) | Requiem bolts: speed 5600, radius 0.5, gravity 0.35 / 0.5 / 0.65 | 4 / 16 Glenmoril gun projectiles changed, all 4 get 5600 + 0.5 + a band gravity (Quicksilver 0.4, Tonitrus ×3 0.5). The other 12 keep source values (0.15 / 0, outside the band). The gravity value itself matches no Requiem bolt (0 / 4) |

## Domain specifics

### 1. The loot strip is an artifact-chest rule, with Dac0da the exception

- **Requiem's picture.** A Requiem unique-item chest holds the item plus at most one or two themed extras:
  - `REQ_Chest_GlassHelmet_Winterhold`: helmet + snow bear hide;
  - `REQ_Chest_GlassGauntlets_Morthal`: gauntlets + an enchanted-ring list;
  - `REQ_Chest_RingOfTheWind`: the ring alone.

  None of the 14 carries a lockpick, bandit-potion or soul-gem list.
- **What ARR strips.**
  - Glenmoril's 75 `zzzRevArtifact*` chests lose `LootBanditSoulGems100` (37), `LootBanditLockpick25` (35), bandit
    potions (26) and dead `REQ_NULL_` lists (46). Gold stays in 28.
  - Vigilant's 21 `zzzCHTreasArtifact_*` chests lose `LItemPotionAllBest` (20), the loose `Lockpick` (20) and lockpick
    lists (13). They keep a soul-gem list and `LItemCoffin75`.
  - Unslaad thins its `zzzCrbTreasQ*` chests to one filler list in 35 / 75. Usually the restore-potion list goes and
    the scrolls or gold stay.
- **Dac0da does something else.** It keeps all filler (91 / 91 artifact chests, lockpick lists included). Instead, it:
  - removes the bonus conjuration spell tome from 17 chests;
  - swaps the enchanted shield for its base version in 6.

  Both cut the chest's value too, by a different route.
- **Corpses lose lockpicks too.** Vigilant's corpses and coffins go from 16 lockpick lists to 2.

### 2. Xarrian follows the chest's material

The script lets the player break a lock by force. Requiem gives it only to wooden containers and wooden doors. Bruma
copies the idea to BSHeartland but also covers stone and iron boss chests and safes, which Requiem never does. Bruma
also misses `CYRTreasBanditChest` (Chest01). Its vanilla twin `TreasBanditChest` carries the script (bugs file).
Merchant chests: Requiem has one precedent (`VendorMiscChestSmall`), so Bruma's Snowstone merchant chest is in line.

### 3. Vendor rebalances

- **Bruma.** Its changes split into two groups:
  - **Requiem-aligned adds:** `REQ_LI_VendorApothecary_MinRestoreHMS` / `MinHealingPoultice`, and the REQ_ inn foods
    and Cyrodilic brandy.
  - **Economy cuts:** enchanted stock; potion and ingot stacks; `LItemMiscVendorLockpicks75` (2 chests);
    `Gold001 ×500` at the fence; `PerkMasterTraderGold` at 3 inns.
- **Dac0da.** Its vendors halve their own leveled-list counts (2 → 1, 3 → 1). Augur, Kannimarco and Kanra lose soul-gem
  stacks (10 to 16).

Requiem has no comparable for a mod vendor's stock levels.

### 4. Ammo gold value

- **On the ladder:**
  - Ayleid 2, Khajiiti 1, Ashlander 3, Auri's bone arrow 1;
  - Dac0da and Glenmoril Snow Elf 14, Glenmoril ivory 2;
  - LOTD MAASE 2 (Falmer 1), SEA 15 (glass 14), CC orcish bolt 5 (orcish bolt 4).
- **Off it:**
  - CC silver bolt 8 (silver 2-3);
  - Remiel's dwarven bolt 6 (3);
  - Vigilant's ebony great arrows 30 (ebony 22; the great arrow may carry a premium);
  - Glenmoril's Forsworn spear 5.
- **Ammo with no Requiem material keyword:** set by hand.
  - Vigilant lowers its own: log 18 → 1, Bourlor 5 → 1, Bourlor great 10 → 5, Kyne 12 → 6.
  - Unslaad's frost arrow 0 → 14 lands on the glass rung.

### 5. Potions

- **Correction to the census.** It said Requiem's Invisibility and Fortify Sneak potions are autocalc. The live
  winners (`Requiem - Alchemy Redone.esp`) are NoAutoCalc on all 8. NoAutoCalc on ARR potions is Requiem-aligned, not
  a split.
- **Where ARR differs:** the secondary effects. Remiel's potions are multi-effect by design in the source. ARR
  re-picks the drawback without removing it (Fire Resist: source Damage Stamina → ARR Damage Stamina + Damage Magicka).
  Falkreath's two conversion potions (Restore X + Damage Y, both 8 / 20) are drawbacks by concept.
- **Values** sit close to Requiem's rung (Remiel Restore Health 80 = rung 4 at 80), not on a formula.

### 6. Projectiles

- **Requiem's ladder is a material ladder.** Base Glass and Stalhrim arrows are 0.2 though their weight keyword says
  Medium / Heavy. WAR's enchanted Glass (0.35) and Stalhrim (0.5) variants follow the weight keyword instead. Read the
  base arrow of the same material.
- **ARR's 1.0 values are a second school.** Vigilant's normal arrows (source 0.35), Remiel's bolt (source 0.35 / DBM
  0.2) and the great arrows (source 0.15 / 0.25 → 1.25) were all raised to it. Only Trad's third-party S&S + Curios
  patch shares it (34 / 34). ARR's CC Curios bolts inherit 1.0 from it, since ARR does not override those projectiles.
- **Glenmoril's guns** (Tonitrus 0.35 → 0.5, bullet 0.15 → 0.4) have no Requiem comparable. They land near, not on,
  the ladder.
- **Speed:** bolt projectiles go to Requiem's 5600 where ARR touches them. Val Serano's bolt (5400) is untouched.

### 7. Follower bridge: a record signal

- **What separates the 8 from the rest** is how each NPC ships, not the plugin header. Each bridge follower shows at
  least one of three marks:
  - **outside `PotentialFollowerFaction`:** Inigo, Ashe and Val Serano recruit through their own dialogue (3 / 8);
  - **`BardAudienceExcludedFaction`:** Inigo, Kaidan, Xelzaz, Remiel, Lucien and Val Serano (6 / 8);
  - **`DismissedFollowerFaction` already set:** Auri, Xelzaz, Remiel and Lucien (4 / 8).
- **None of the 7 vanilla-framework followers read has any of the three marks.** They sit in `PotentialFollowerFaction`
  (most also in `CurrentFollowerFaction`) and nothing else follower-specific.
- **Weaker marks:** a faction named for the follower (`InigoGiftFaction`, `KaidanFaction`, `MM_AsheFaction`,
  `AX_ValFaction`, ...) fits 7 / 8, but Wyrmstooth's followers carry mod factions too.
- **Dead marks:** framework masters (none), a follower-named quest alias (Wyrmstooth has 21), plugin size.

### 9. Respawns follows the vanilla model the container copies

- **What S&S's patch cleared:** six "MadRuins" containers (4 chests on the Dawnguard `DLC01SnowElfChest` model, the
  Ayleid `ARChest01` cask, a cloth sack).
- **What it left on:** the `SITreas*` chests on S&S's own `chest.nif` model. They hold the **same loot lists**
  (`EC_SS_SICommonTreasListAll` + the Mania / Dementia list) and sit in the **same dungeons** (Xedex, Borogove).
  Barrels and vendor chests also stay on. So location, loot, boss status and vendor status do not decide it.
- **The model does.** Vanilla Snow Elf chests do not respawn (`DLC01TreasSnowElfChest`, `...Boss`: 0 / 2), and most
  vanilla sacks do not (13 / 16). The cleared S&S records copy those models.
- **LOTD added Respawns to Rain's Knapsack** (`MiscPouch` model, Clearspring Tarn exterior). Vanilla knapsacks
  respawn (6 / 8, including Requiem's `TreasKnapsack`). Side effect: the knapsack also holds LOTD museum fragments and
  Greater Research Notes, which now refill (bugs file).
- **Requiem itself barely touches the flag:** 1 / 279 overrides (re-adds it to `TreasStrongBox`).

### 10. Gun-round gravity

- **Rifles are crossbows.** 22 Glenmoril crossbow-animation weapons (21 `zzzRevWeapTypeRifle`) fire the bolt-flagged
  bullets, so the bullet projectile's gravity is live. Pistols are staves and fire spells, so the split-11 shot-count
  charge rule never meets these records.
- **ARR touched 4 of the 16 gun projectiles:** Quicksilver Bullet (`0035BF`) and the three Tonitrus rounds
  (`004D36-38`). Each got speed 5400 → 5600, collision radius 1 → 0.5, and gravity 0.15 → 0.4 / 0.35 → 0.5.
  Aetherium got the radius only.
- **The values are Requiem's bolt kit, except gravity.** Speed and radius are exactly Requiem's. The gravity lands
  inside Requiem's bolt band but on no matching rung: the Quicksilver bullet carries the Dwarven keyword (bolt 0.5)
  and got 0.4; Tonitrus carries Steel (bolt 0.35) and was moved off 0.35 to 0.5.
- **Untouched:** the special rounds (Meridia, Oblivion, Black Soul, Stalhrim, Shotgun 0.15 / 5400; gas shells 0.75 /
  1800; flare 0.15 / 512; Jillian / Histus energy bolts 0 / 2000).

## Open residual

- **Dac0da keeps artifact-chest filler**, while Glenmoril and Vigilant strip it (question 2). It is not explained by
  container type, filler size, or dead (`REQ_NULL_`) lists.
- **Unslaad's partial thinning** (keep one of the potion / scroll pair) has no rule beyond "fewer lists".
- **The 1.0 gravity school** (question 1). Provenance only: it matches Trad's patch. File dates can't order the
  patches (install dates).
- **Bruma's extra Xarrian targets** (boss, stone, metal; question 3).
- **Gun gravity values** (Quicksilver 0.4, Tonitrus 0.5) fit no Requiem rung, keyword or damage. Only "inside the
  bolt band" holds. Stalhrim Bullet has an ARR recipe but its projectile is untouched.
- **The Respawns model rule rests on 7 records.** The `ARChest01` cask's vanilla comparable is a CC model and was not
  read.
- **The follower signal is fitted on 15 NPCs.** Check it against the next follower mod.
- **Not hunted:**
  - Vigilant `zzzCHLogArrowEx` value 18 left beside its twin's 1 (bugs file);
  - DialogResponses / quest alias rows (census says noise).

## Hypothesis log

**L1 loot strip (§1)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Strip targets artifact chests (one unique + filler) | **Kept** for Glenmoril 69 / 69 and Vigilant 20 / 20; Unslaad 46 / 75; dead for Dac0da 0 / 91 |
| H2 | Strip removes only dead `REQ_NULL_` / emptied lists | Dead: live lockpick, soul-gem and potion lists go too; Dac0da keeps its `REQ_NULL_` lists |
| H3 | Lockpicks are the target (Requiem lockpick economy) | Partial: lockpick lists 73 → 0 on Glenmoril / Vigilant artifact chests and 16 → 2 on Vigilant corpses; merchants keep them (9 / 9 Vigilant); Dac0da keeps them |
| H4 | Gold is kept | Lead: Glenmoril keeps gold in 28, drops it in 10 |
| H5 | Merchants are left alone | **Kept**: filler kept in 31 / 40 mod merchant chests |
| H6 | Requiem's own unique chests carry no filler | **Kept**: 0 lockpick lists in 14 / 14 |
| H7 | Target is "≤ 1 filler list" | Glenmoril 65 / 69; Unslaad 35 / 75; Vigilant 0 / 20 (keeps 2). Dead as a count rule |
| H8 | Generic treasure chests in the same mods are stripped too | Dead: Glenmoril treasure 6 / 13 partial, Vigilant 1 / 12, lockpick lists kept |
| H9 | Requiem's generic chests have no lockpicks either | Dead: 59 / 279 Requiem CONT overrides carry one |
| H10 | Same author family (Dac0da / Glenmoril / Unslaad / Vigilant use one artifact-chest scheme) → same patch treatment | Dead: Dac0da differs (residual) |

**L2 Xarrian (§2)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Requiem puts Xarrian on every vanilla treasure chest | Dead: 17 / 89 vanilla `Treas*` |
| H2 | On wooden chest models only | **Kept**: Chest01 / UpperChest / NobleChest 15 / 24 v other models 2 / 65 (one Ruins_SmallChest, one Falmer) |
| H3 | Never on boss chests | **Kept** for Requiem: `Ruins_LargeChest` 0 / 14, CW boss-small chests 0 / 2. Bruma breaks it (8 boss chests) |
| H4 | Never on metal (Dwemer, strongbox, safe) | **Kept** for Requiem (0 / 9). Bruma breaks it (3) |
| H5 | Wooden doors too | Mostly: 40 doors, ~33 of them town / house / mine doors (Farmhouse, Imperial, Nordic, Riften, Whiterun, Orc); also 4 glass display cases, a fence gate and a portal door |
| H6 | Bruma mirrors each vanilla twin exactly | Dead: `CYRTreasBanditChestBoss` has it, vanilla `TreasBanditChestBoss` has not; `CYRTreasBanditChest` lacks it, vanilla twin has it |
| H7 | Bruma covers all non-empty `Treas*` loot chests | Lead: 24 / 30; misses BanditChest, AyleidChestSmall, Frostfire chest and two Snow boss variants |
| H8 | Other patches add it to mod chests | Dead: 0 outside Bruma (~300 mod `Treas*` chests overridden) |
| H9 | Merchant chests are excluded | Dead: Requiem's `VendorMiscChestSmall` carries it; Bruma's Snowstone merchant chest too |

**L5 vendor rebalance (§3)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Bruma adds Requiem's vendor minimum lists | **Kept**: 2 / 2 apothecaries (Requiem 9 / 10) |
| H2 | Bruma adds Requiem foods to inns | **Kept**: 3 / 3 (Requiem 18 / 21) |
| H3 | Bruma keeps Requiem's Master Trader gold | Dead: removed at 3 inns (Requiem 110 / 110) |
| H4 | Bruma removes enchanted stock as Requiem does | Dead: Requiem keeps `VendorBlacksmithEnch*` in 8 / 19 smiths |
| H5 | Dac0da's 75 rows are vendor rebalances | Dead: most are artifact-chest edits (tome removal 17, enchanted → base shield 6); 4 vendor chests have count cuts |
| H6 | Counts cut to a fixed ratio | Dead: Bruma 15 → 10, 15 → 6, 5 → 2, 3 → 5 (raised for smith stock) |
| H7 | Bruma cuts the lockpick vendor lists | Lead: 2 / 2 where present (fence, Ice Wind) |
| H8 | Bruma's QA / test chests are emptied | **Kept** as conv 11: QA containers lose `AUTH_NULL_` items, gain REQ_ equivalents |

**C4 ammo value (§4)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Value = Requiem material ladder (exact) | 9 / 42 changed rows; 10 / 15 for playable bow ammo with a material (±1) |
| H2 | Within 2× the ladder | 23 / 42 |
| H3 | Value = damage / 4 | Dead: 1 / 42 |
| H4 | ARR always raises values | Dead: raised 30, lowered 12 |
| H5 | Guns: source + 5 / + 10 | **Kept**: 13 / 14 |
| H6 | Guns on the ladder | Dead: 0 / 14 |
| H7 | Census "15-30 band" is a rule | Dead: it is guns + Vigilant great arrows; 18 / 47 playable ammo sit in it |
| H8 | Bolts carry ×1.2 | Lead: CC orcish 5 (bolt 4) yes; Remiel 6 and CC silver 8 no |
| H9 | Enchanted / special ammo gets the enchanted step (+10 … +40) | Dead: Glenmoril's enchanted guns are +5 / +10 over source |

**C5 potions (§5)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Requiem's potions are autocalc for non-restore effects | Dead: 205 / 205 NoAutoCalc on the live winner |
| H2 | ARR sets NoAutoCalc on all patched ingestibles | **Kept**: 18 / 18 |
| H3 | ARR adds drawbacks Requiem lacks | Dead: 10 / 10 Remiel source potions already carry one |
| H4 | ARR removes drawbacks to match Requiem's single-effect potions | Dead: 10 / 10 keep one |
| H5 | Primary magnitude on a Requiem rung | **Kept**: 9 / 10 (Weakness to Magic poison 100 off, Requiem max 25) |
| H6 | Restore duration 20 | **Kept**: 3 / 3 Remiel, 2 / 2 Falkreath |
| H7 | Resist duration = Requiem 60 | Dead: 3 / 3 at 120 (question 8) |
| H8 | Fortify Sneak duration = Requiem 60 | Dead: 2 / 2 at 20 |
| H9 | Value = Requiem rung value | Weak: Restore Health 80 = rung 4; resists 100 v rung 2 at 120 |

**Q1 / Q2 projectiles (§6)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Gravity = ammo weight keyword tier | 125 / 134 Requiem; misses are base Glass / Stalhrim and creature bolts |
| H2 | Gravity = material of the base projectile | **Kept**: Requiem.esp 32 / 32 |
| H3 | Gravity ≥ 1 for slow thrown spears | **Kept**: Requiem Riekling spear 1.0 / 2500; Rot Below's gremlin spear flies on a 1.0 / 2500 projectile |
| H4 | Vigilant great arrows are thrown-class (speed 2600) | Lead: speed matches throwing knives (2600), gravity 1.25 has no Requiem match |
| H5 | ARR uses one gravity school | Dead: Glenmoril material ladder (2 / 2) v Vigilant / Remiel 1.0 (4 / 4) |
| H6 | 1.0 copied from a Requiem precedent | Weak: only the REQ_NULL blood arrow and the spear |
| H7 | 1.0 copied from Trad's Requiem patch | Lead: 34 / 34 Trad projectiles at 1.0 |
| H8 | Bolt speed 5600 wherever ARR touches a bolt | **Kept**: 5 / 5 (Remiel, Glenmoril guns); Val Serano untouched at 5400 |
| H9 | Arrow speed 3600 | Kept for Requiem (every material arrow; thrown 2500-2600); ARR breaks it once (Unslaad 3600 → 5400, bug) |

**Q3 follower bridge (§7)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Bridge = all unique followers in ARR | Dead: 8 in, 9+ vanilla-framework followers out |
| H2 | Bridge = followers outside `PotentialFollowerFaction` | Dead: Kaidan, Auri, Lucien, Xelzaz and Remiel are in the faction and in the bridge |
| H3 | Bridge = custom-framework followers | **Kept** as the reading: 8 / 8 by mod knowledge; record signal H8 |
| H4 | Something else references Requiem's follower control | Dead: only Requiem's lock-control quest and the bridge |
| H5 | A follower-framework master in the plugin header | Dead: Inigo, Auri and Val Serano have only vanilla masters |
| H6 | The plugin defines a quest with a follower-named alias (own follow quest) | Dead: Inigo 1, Auri 1, but Wyrmstooth 21, BSHeartland 6, CC Fishing 1, the other 6 bridge plugins 0 |
| H7 | `BardAudienceExcludedFaction` on the NPC | Strong: 6 / 8 bridge v 0 / 7 others |
| H8 | Outside `PotentialFollowerFaction`, or Bard-excluded, or `DismissedFollowerFaction` preset | **Kept (lead)**: 8 / 8 v 0 / 7, fitted on 15 NPCs |
| H9 | A faction named for the follower | Weak: 7 / 8 bridge (not Auri); Wyrmstooth's followers also carry mod factions |
| H10 | `CurrentFollowerFaction` preset | Dead: 4 / 8 bridge v 4 / 7 others |

**L5b Respawns (§9)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Location type (dungeon / clearable cleared, settlement kept) | Dead: kept `SITreas*` chests sit in the same Xedex / Borogove dungeons (LocTypeDungeon + Clearable) as the cleared ones |
| H2 | Encounter zone NeverResets → flag moot | Dead: `EC_SS_DrownedRuinEZ` and `EC_SS_BorogoveEZ` have Flags 0 |
| H3 | Merchant or owned chests keep it | Partly true but not the cut: vendor chests keep it, and so do 4 unowned `SITreas*` loot chests |
| H4 | Boss / unique chests cleared | Dead: `SITreasManiaBossChest` / `DementiaBossChest` keep it; non-boss MadRuins chests are cleared |
| H5 | Loot content decides | Dead: cleared and kept chests hold identical lists |
| H6 | Quest-related containers cleared | Dead as read: the cleared ones hold only generic loot lists; no quest script name was returned for any of the 19 (quest aliases not checked) |
| H7 | Copy the vanilla container of the same model | **Kept**: 7 / 7 (Snow Elf chest 0 / 2 vanilla respawn, sacks 13 / 16 not, knapsacks 6 / 8 do) |
| H8 | Follow Requiem's own respawning containers | Neutral: Requiem keeps vanilla's flag (changes 1 / 279), so "copy vanilla" is the Requiem stance too |
| H9 | Exterior containers given Respawns | Lead only: Rain's Knapsack is exterior (Clearspring Tarn); one case |

**Q1b gun-round gravity (§10)**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Gravity = Requiem bolt of the round's material keyword | Dead: 0 / 4 (Dwarven 0.5 → 0.4; Steel 0.35 → 0.5) |
| H2 | Gravity = Requiem bolt of the weight keyword | Dead: 0 / 4 (Heavy 0.5 → 0.4; Medium 0.35 → 0.5) |
| H3 | Gravity follows the split-8 damage rule (max(10, source ×5)) | Dead: Tonitrus ×5 damage but gravity +0.15; bullet fixed 84 but gravity +0.25 |
| H4 | Gravity follows the split-11 shot-count charge rule | Dead: the charge rule is on pistols (staves, spell-fired); gravity is on rifle (crossbow) rounds; no shared record |
| H5 | Calibre: small rounds lighter | Dead: the "Tiny" Tonitrus rounds got the heavier 0.5 |
| H6 | Gravity scales with damage | Dead: 84-damage bullet 0.4 v 70-damage Tonitrus 0.5; 80-100-damage specials untouched at 0.15 |
| H7 | Touched rounds get Requiem's bolt speed and radius | **Kept**: 4 / 4 to 5600 and 0.5 |
| H8 | Touched rounds' gravity lands in the Requiem bolt band 0.35-0.65 | **Kept**: 4 / 4; the 12 untouched sit outside it (0.15, 0.75, 0) |
| H9 | The touched rounds are the craftable ones | Weak: 3 of the 4 craftable families (Stalhrim is craftable, untouched; Aetherium radius only) |
| H10 | WAR crossbow weapons set it | Dead: gravity is a projectile field; crossbows carry none |

## Settled or minor calls (§8)

- **L4 lockpick script upstream.** The build stamps `REQ_LockpickControl` on every winning CONT / DOOR. The RFTI
  output carries it on the records LOTD omits (02064F, 0774BF; census fact 12). So the skill answer is per stance:
  - **upstream:** leave it to the build;
  - **downstream:** carry it (98.5 % of ARR records do).
- **L3 encounter zones.** One plugin (Gray Cowl), no Requiem comparable: Requiem's 8 ECZN are min-only. Question 5.
- **C2 Glenmoril flora.** After the patch, 14 / 14 Glenmoril flora and trees harvest nothing. ARR nulled the 4 that did.
  - Their 3 ingredients (Maggot Egg, Roots of the Blue Star, White Flower) stay unpatched: autocalc, source values.
    ARR converted only 2 / 10 Glenmoril ingredients.
  - Maggot Egg now has no source: 0 referencers.
  - This reads as a deliberate "Glenmoril harvest off", with one stranded ingredient. Question 6.
- **Q4 Gray Fox gate.** Requiem retunes story-manager level gates itself, in both directions:
  - adds GetLevel ≥ 20 on DA10 (House of Horrors);
  - adds a level gate to Dawnguard's vampire attacks;
  - lowers the Ebony Warrior gate 80 → 50.

  So raising a mod quest's gate is a per-quest judgement with Requiem precedent. It is not a blanket rule.
- **Q5 Troll MCO.** The source slam explosions spawned `DA09BeamProjectile` (Mehrunes' Razor's crystal beam) and
  `ShockBoltAimExpert` (a shock beam with its own explosion). The patch nulls both and blanks the names. These look
  like copy-paste vanilla templates in the source, so this is a clean-up, not a bug.
