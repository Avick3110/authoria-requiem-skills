# Split hunt 12: the MAGIC and RACE census "Open calls for Aaron"

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census left eight open calls in
`../census/stage2/MAGIC.md` (conv 1, 2, 6, 10, 13, 18–20, 24, 26; bugs S1–S4) and seven in `../census/stage2/RACE.md`
(conv 1, 13, 14, 15, 18; Limits). This round triaged each call, then tested hypotheses by script against the
census ledger and live reads (same snapshot, epoch `e2-58972ca8618e79ea`). Scripts and data stay local
(`census/splits/mr_*`, `census/splits/race_*`).*

## Scope

- **Corpus:** the census ledger's 18,389 magic and race rows (Spell, MGEF, ENCH, Scroll, Book, Shout, Race), plus
  live winners for the 590 patched races, their 437 ability spells, 169 NPCs of the residual races, 4,041 MGEFs,
  1,404 spell tomes, 256 shouts and 424 carriers of the Resist Damage spells.
- **Comparables:** Requiem.esp's 151 races and 345 `REQ_Trait_*` spells; Magic Redone (MR) and vanilla spells,
  MGEFs and tomes (2,602 spells, 2,421 baseline MGEFs, 486 baseline tomes).

## Triage

| Call | Verdict | Section |
|---|---|---|
| MAGIC 1 Resist Damage: drop or convert | split, hunted | M1 |
| MAGIC 2 MGEF PowerMod keywords required? | split, hunted | M2 |
| MAGIC 3 Tome prices above 2000 / Glenmoril flat 500 | split, hunted | M3 |
| MAGIC 4 ENCH flat 100 and ×2 (Glenmoril) | split, hunted | M4 |
| MAGIC 5 HalfCostPerk tier fit / school match | split, hunted (short) | M5 |
| MAGIC 6 Perk-proc drop | rule check against Requiem | M6 |
| MAGIC 7 Shouts flat or escalating | split, hunted (short) | M7 |
| MAGIC 8 Vernaccus / Bourlor | bug (bugs file) | M8 |
| RACE 1 Resist via source ability or `Resist_<X>` | split, hunted | R1 |
| RACE 2 Wolf / bear RegenHpInCombat | split, hunted | R2 |
| RACE 3 AttackRace mapping | correctness check | R3 |
| RACE 4 TrollRace Health 300 | dissolved (source value) | R4 |
| RACE 5 Vigilant MovementTypes removed | dissolved (noise) | R5 |
| RACE 6 Waterwalking 0173C2 | dissolved (it is Requiem's trait) | R6 |
| RACE 7 Kaidan COTR race clones | explained | R7 |

## Answer

| # | Rule | Requiem / MR evidence | ARR evidence |
|---|---|---|---|
| R1 | **One resist source per creature, and the source mod's own profile wins.** If the mod gave its creature an ability carrying resists or weaknesses, keep that ability (swap `REQ_NULL_Ab*` to `REQ_AbHide_*` at the same magnitude) and add **no** `REQ_Trait_Resist_<X>`. If it gave none, add the closest Requiem race's `Resist_<X>` | Requiem's races carry their resists in one `Resist_<X>` trait (47 / 54 armoured Requiem races) | Source profile → no trait: **259 / 280**. No profile → trait: 74 / 124, plus 23 whose resists sit on the NPC and 7 whose Requiem comparable has none. Overall **363 / 404** armoured races. Both held-out patches follow it (Rot Below 12 / 12, Falkreath Shades 3 / 4) |
| M1 | **Resist Damage `0CAB90` (REQ_NULL_AbResistDamage): drop it when the race already has a natural-armour trait or its Requiem comparable has no natural armour; convert to `REQ_AbHide_FortifyArmorRating 0008CB` only when the creature would otherwise have no armour** | Requiem keeps 0CAB90 only on REQ_NULL spells. Wraiths, wisps, werewolves and Falmer carry no Armor trait in Requiem | Race carries an Armor trait → drop **105 / 109**. No Armor trait → convert 9 / 15 (the 6 drops are wraith / werewolf / Falmer-likes, Requiem-consistent) |
| M2 | **PowerMod keywords go on player-castable school spells, by archetype.** `REQ_NoDurationScaling` on magnitude-scaled effects that have a duration (damage, absorb, dual value); `REQ_NoMagnitudeScaling` on cloaks, weapon enchant effects and rally; `REQ_SpellConcentration` on concentration effects. Leave instant effects, buffs (PeakValueModifier), summons, abilities and NPC-only effects untagged | Requiem / MR: player school spells 419 / 913 tagged v 118 / 1,508 elsewhere; NoDur on duration ValueModifier 91 / 136, DualValue 41 / 46, Absorb 21 / 30; NoMag on Cloak 20 / 20, EnhanceWeapon 21 / 21, Rally 6 / 6, but PeakValueModifier only 19 / 203; instant NoDur 2 / 14 | Apocalypse tags 424 / 460 of its MGEFs (stricter than Requiem, harmless). Other mods' MGEFs are mostly NPC-only and stay bare, as Requiem's do. The held-out patches skipping them is consistent |
| M3 | **Tome Value = the taught spell's tier on the MR ladder: Novice 100–300, Apprentice 300–500, Adept 500–700, Expert 700–900, Master 1000–2000** (tier read from HalfCostPerk) | 483 baseline tomes, medians 100 / 400 / 650 / 900 / 1500 | Every patch except Vigilant and Become a Bard: **198 / 231** on the ladder. Glenmoril's "flat 500" is the Adept rung (8 / 9). Bard songs have no school tier (Voice equip type), so the ladder does not apply |
| M4 | **Glenmoril's flat Amount = Cost = 100 is the gun rule, not a staff rule**: one shot costs 100 charge; gun damage effects ×2 | Guns sit outside MR's staff tiers (split 4) | Amount 100 on guns **155 / 158** (the 3 heavy Dwemer guns 50 / 75). Gun effect magnitudes exactly ×2 in 83 / 185 pairs, median ×1.9. Non-gun Glenmoril enchantments are tuned per item |
| M5 | **HalfCostPerk school = the school of the spell's first effect; tier fits the cost loosely; NPC-only spells may carry none** | MR 365 / 365 school match; MR cost ranges overlap (Apprentice 20–250, Adept 50–500, Expert 200–800) | School match 32 / 36. Tome-taught spells keep a perk 20 / 20. All 38 nulled perks are on NPC-only spells |
| M6 | **Drop the vanilla perk-proc effects (Fireball fear 0F3930, PerkDisintegrate 0F3F0C / 0F3F0D) from patched spells** | 0 Requiem / MR spells carry them (one REQ_NULL dragon breath) | Dropped in 17 rows / 6 plugins; 3 patched winners still carry them (bugs file) |
| M7 | **Shout recovery: player-learnable shouts escalate per word, NPC-only shouts are flat** | Vanilla escalating 26 / 26 are player shouts; dragon, Greybeard and creature shouts flat; Requiem-defined NPC shouts flat 9 / 10 | Vigilant's 7 NPC shouts flat. Dac0da's 2 creature force shouts keep the player Unrelenting Force steps because they were re-pointed to its words |
| R2 | **RegenHpInCombat follows the comparable whose Healing trait is copied** (Troll, Werewolf, Lurker, Seeker have it; Spriggan, Werebear, Dragon Priest, Keeper do not) | Requiem's 16 Healing races | 57 / 72. Flag adds go with a Healing trait 23 / 25. The "wolves and bears" are lycanthrope beasts with Healing_Werewolf / Werebear |
| R7 | **A mod race shared by NPCs of different lore races is cloned per race**, each clone carrying that race's Requiem trait set | n/a | Kaidan: COTR_Race_Dremora → 0Dremora, _HighElf → Cyrelian, _Breton → Rennare (3 / 3) |

## Domain specifics

**R1, how the two routes arise.** The census read the split as "Glenmoril style v Rot Below style". It is not a
style. It follows the source. Glenmoril, Vigilant and Dac0da creatures mostly ship a vanilla-style ability with
`REQ_NULL_AbResist*` effects; the patch swaps those to AbHide and stops. Rot Below's creatures ship no such
ability, so the patch adds `Resist_<X>`. Its two races that do ship one (`CJ03AbAdabelleResistance`) took the
ability route, exactly like Glenmoril. The `Resist_<X>` picked shares the Armor trait's stem in 82 / 95.
Weakness-only abilities count as a profile too: 41 races keep a weakness-only ability and get no resist.

**R1 exceptions (21 with both, 20 with neither).**
- 10 of the "both" left the source ability unedited, with its REQ_NULL resists, and added the trait (Bruma 4,
  Val Serano 4, Tools of Kagrenac 2). The REQ_NULL resist MGEFs still work (live: ValueModifier on the
  resist value), so these resists stack. Bugs file.
- 7 have AbHide resists in the ability and a trait (Vigilant's 4 ebony giants and Mentana, AFDI's 2 lesser
  daedroths). Bugs file, low.
- Of the 50 with neither, 23 carry the resist on the NPC, 7 copy a Requiem comparable that has none (bears,
  husky-likes), 9 use Draugr / Spirit armour traits that no Requiem race carries (Requiem's DraugrRace has
  `Resist_Draugr` and no Armor trait), and 11 are clear gaps (bugs file).
- 11 Glenmoril / Bruma races keep REQ_NULL resists in an unedited ability and no trait (gryphon, manticore,
  khimera, the Ent races). The resist works, but conv 1's swap was missed. Bugs file.

**M1, the boss case.** Resist Damage on NPC-carried (humanoid boss) abilities is dropped 52 / 59. Requiem's own
`REQ_Trait_Boss_*` (Ebony Warrior 300, Morag Tong 300, Miraak 700) do carry 0008CB, so converting for a boss has a
precedent. A second round tested ten properties of the carrying NPCs (table M1b). None separates the 7
conversions. Only magnitude leans: at ≥ 200 the patches convert 4 / 7, at ≤ 125 2 / 42, and Requiem's boss traits
sit at 300–700. But identical cases split by plugin: unique humanoid bosses of playable races are converted by
Dac0da (3 / 3: `zDcdAbArcanist_Fever` Redguard 750, `zDcdAbBossSnowElf` High Elf 200, `zDcdAbAllyBuff` 400) and
dropped by Glenmoril (Aradia Breton 120, Jazel Lorkhaj Khajiit 120, Boltzmer High Elf 120). Vigilant converts
3 / 22. So this one is a genuine per-author call (questions file).

**M3, the premium.** Vigilant tomes sit about ×3.3 above the ladder (1000–8000, 66 / 69 off it). A second round
tested eleven properties (table M3b). What it found:
- **It is a summon premium, not a Vigilant-wide one.** 64 / 69 Vigilant tomes teach a summon, at ×3.4. Its 5
  non-summon tomes sit at ×1.85. Other mods' summon tomes sit on the ladder (median ×1.0), so "summon" alone does
  not explain it either.
- **It is not a fixed ratio.** The ×-ladder falls with tier (Novice ×16, Apprentice ×4.1, Adept ×3.1, Expert
  ×2.9, Master ×2.3; CV 1.0). Tier medians run 1625 / 1625 / 2000 / 2625 / 3500, so Vigilant is on a **flatter
  second ladder of its own**, about 1000–4000, that tracks the summoned creature loosely (corr 0.52 with the
  creature's level over 63 summons, corr 0.6 with tier). Its Novice-tier tomes summon level 15–35 creatures
  (bone horse, flame spirit) at 1000–3000. That reads as price-by-creature, with a HalfCostPerk tier set low.
- **No Requiem precedent above 2000.** Requiem.esp's own 99 tomes run 100–2000 and never exceed it.
- Not source ×N (median ×4, CV 1.1), not BaseCost (corr 0.5), not distribution, not ritual equip type (none are
  BothHands). Bard's tomes do not share it (flat 100, ×0.67 from source).

Three master-tier uniques elsewhere sit above 2000: Unslaad's Ysgrim Thunderbreath 5000 and Icicle Bomb 2500,
Dawnguard Arsenal's Gaze of Arkay 2500. Questions file.

**M5, a census bug withdrawn.** S4 (Glenmoril throwing knives with a Conjuration perk) is not a bug. The knives'
first effect, `zzzRevMgEThrowingKnife*`, is a Conjuration MGEF; the Destruction effects are MR impact riders.

**Three census calls dissolve.**
- R4: TrollRace Health 300 is `Troll_MCO.esp`'s value, carried by Races Merge (live tree). It is conv 15, not a
  hand tune.
- R5: Vigilant's 10 "MovementTypes removed" rows are add / remove pairs of the same movement type whose speed
  overrides differ only in float serialisation (3.141593 v 3.1415927). Noise.
- R6: `0173C2:Dragonborn.esm` is `REQ_Trait_Waterwalking`. Requiem puts it on 14 hovering races (dragon priests,
  wraiths, wisps, atronachs, seekers, the spectral dragon). ARR's 22 adds are all spirits, wraiths, liches and
  flyers. Requiem-aligned.

**R3, AttackRace.** All 34 targets are the matching vanilla body plan (sabre cat, wolf, chaurus, troll, mammoth,
giant, skeever, draugr, boar, frost atronach, werewolf, spriggan, goat, bear, mudcrab). The races' own Attacks
rows are only re-saves. Requiem sets AttackRace on 1 / 151 races, so this is an optional house idiom; the
mapping itself is correct.

## Open residual

- **R1:** the 11 clear gaps and 9 Draugr / Spirit-armour races with no resist anywhere (bugs file).
- **R2:** 15 / 72 flags differ from the comparable (Glenmoril Spriggan-healers with the flag, Lurker-healers
  without). Not chased further.
- **M1:** Dac0da converts on 4 wraith-likes whose Requiem comparable has no armour, and on 6 races that already
  have an Armor trait. Requiem's `Resist_DragonSkeletal` (0008CB 400 on top of Armor_Dragon) shows stacking has a
  precedent, so the 6 are not clearly wrong.
- **M1 bosses:** after ten tested properties, Dac0da converts and Glenmoril drops on same-shape bosses
  (questions file).
- **M3:** Vigilant's flatter summon ladder (about 1000–4000, loosely by creature) and the master uniques above
  2000 (questions file).
- **M5:** 8 / 42 tiered spells cost outside MR's range for their perk tier. Not chased.
- **Un-hunted:** MAGIC conv 19 (staff ENCH magnitudes outside Glenmoril) and conv 22 (BaseEnchantment nulling)
  were not open calls and were not reopened.

## Hypothesis log

**R1 (resist route).**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Plugin decides the route (provenance) | Last resort; superseded. Rot Below follows the same rule as Glenmoril once the source is checked |
| H2 | Comparable Requiem race carries a Resist trait → add it | Dead as a sole rule: 172 races with a resisting comparable keep only the source ability |
| H3 | Source ability carries resists (REQ_NULL pre-patch) → keep it, no trait | **Kept**: 224 / 248 |
| H4 | Source ability carries any resist **or weakness** → no trait | **Kept**: 259 / 280 |
| H5 | Race has any own ability at all | Weak: own ability without a profile still gets the trait 16 / 20 |
| H6 | Patch edited the ability spell | Explains 10 of the 21 "both" exceptions (unedited ability + trait) |
| H7 | No source profile → add `Resist_<X>` | **Kept**: 74 / 124; 30 more explained by H8 / H9 |
| H8 | Resist delivered at NPC level instead | **Kept** for 23 of the 50 left |
| H9 | Requiem comparable has no Resist trait | **Kept** for 7 (bears, husky-likes) |
| H10 | Actor type (undead, daedra, dwarven, animal) | Dwarven 19 / 20 trait; others mixed. Folded into H7 |
| H11 | Armor trait added by the patch v already in source | Dead (5 in-source cases, all Races Merge vanilla) |
| H12 | `Resist_<X>` matches the Armor stem | **Kept**: 82 / 95 |

**M1 (Resist Damage).**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Carrier race already has an Armor trait → drop | **Kept**: 105 / 109 |
| H2 | No Armor trait → convert | 9 / 15; the 6 drops fit H5 |
| H3 | Magnitude band decides | Dead: convert rates 8 / 81, 9 / 108, 5 / 14 by band |
| H4 | Race-carried v NPC-carried | NPC-carried drop 52 / 59; boss case open |
| H5 | Requiem comparable has no natural armour (wraith, werewolf, Falmer) → drop | **Kept** for the 6 |
| H6 | Boss ability → convert (REQ_Trait_Boss precedent) | 7 / 59 do; question |
| H7 | 0008CB also added where no Resist Damage existed | 4 cases (Dac0da 3, Vigilant 1), all boss / buff abilities |
| H8 | Plugin (provenance) | Dac0da converts 16 / 38, others ≤ 3. Explained by H1–H5 for race abilities |

**M1b (NPC-carried boss abilities, 59 spells, 7 converted; `mr_rd_boss.py`).**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Unique NPC → convert | Dead: unique 6 / 48, non-unique 1 / 11 |
| H2 | Level band | Dead: level 80+ 5 / 48, 40–79 2 / 7, < 40 0 / 4 |
| H3 | "Boss" in the NPC or spell EditorID | Dead: 4 / 43 v 3 / 16 |
| H4 | A boss / unique keyword on the NPC | Dead: no carrier has one |
| H5 | NPC already carries a Requiem boss trait | Dead: 1 case |
| H6 | NPC already gets 0008CB from another ability | Dead: 2 / 19 v 5 / 40 |
| H7 | NPC's race has a natural-armour trait | Dead: 3 / 32 v 4 / 27 |
| H8 | Magnitude at Requiem boss-trait scale | Lead: ≥ 200 4 / 7, 126–199 1 / 10, ≤ 125 2 / 42 |
| H9 | Shared by many NPCs v one | Dead: 4 / 34 v 3 / 25 |
| H10 | Plugin (provenance) | Dac0da 3 / 3, Vigilant 3 / 22, Glenmoril 1 / 31, Unslaad 0 / 3. Same-shape cases split by plugin; question |

**M2 (MGEF keywords).**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Player-reach: tome-taught spell carries it | **Kept**: Requiem 426 / 943 tome v 108 / 1,391 NPC-only |
| H2 | Scaling flag decides which keyword | **Kept**: PAM → NoDur, PAD → NoMag |
| H3 | Cast type: constant abilities untagged | **Kept**: 6 / 648 |
| H4 | School-less effects untagged | **Kept**: 27 / 1,110 |
| H5 | Archetype decides within PAD / PAM | **Kept** (table in Answer) |
| H6 | Instant (NoDuration) effects skip NoDur | **Kept**: 2 / 14 in Requiem |
| H7 | Defining mod (provenance) | Apocalypse 424 / 460, others ≤ 7: Apocalypse alone tags everything |
| H8 | ARR plugin touching the MGEF | Same as H7 |

**M3 (tome Value).**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Value by HalfCostPerk tier on the MR ladder | **Kept**: 198 / 231 outside Vigilant and Bard |
| H2 | Distribution (leveled list / placed) decides premium | Dead: undistributed non-Vigilant tomes on the ladder 23 / 25 |
| H3 | Source value ≥ 1000 carried over | Dead: Vigilant raised ×4 from source (median) |
| H4 | Up from source v down | No signal |
| H5 | Ritual (BothHands) spells priced higher | Weak: ×1.33 median, 25 / 30 still on the ladder |
| H6 | No school tier (songs) → off the ladder | **Kept**: Bard 28 / 28 |
| H7 | Master-tier uniques one rung above | Lead: 3 cases (5000, 2500, 2500) |
| H8 | Vigilant plugin premium (provenance) | The residual; question |

**M3b (Vigilant premium, second round, `mr_tome_vig*.py`).**

| # | Hypothesis | Result |
|---|---|---|
| H1 | Source value × a fixed N | Dead: median ×4, CV 1.11 |
| H2 | Fixed ratio to the MR ladder | Dead: ×16 / 4.1 / 3.1 / 2.9 / 2.3 by tier, CV 1.01 |
| H3 | A second, flatter ladder | **Kept** in shape: tier medians 1625 / 1625 / 2000 / 2625 / 3500 |
| H4 | Value tracks the taught spell's BaseCost | Weak: corr 0.5; value / cost ×8.2 v ×2.3 elsewhere |
| H5 | Multi-effect spells priced higher | Dead: ×3.42 v ×3.33 |
| H6 | Ritual (BothHands) spells | Dead: none of the 69 are BothHands |
| H7 | Summon spells carry the premium | **Kept** inside Vigilant (64 / 69, ×3.4 v ×1.85); dead across mods (others' summons ×1.0) |
| H8 | Value tracks the summoned creature's level | Lead: corr 0.52 over 63; Novice tomes summoning level 15–35 creatures at ×10–30 |
| H9 | Quest reward / vendor-only (distribution) | Dead: distributed ×3.53 v not ×3.33 |
| H10 | Matches Requiem's own unique tomes | Dead: Requiem.esp's 99 tomes cap at 2000 |
| H11 | Bard shares the multiplier | Dead: Bard flat 100 (×0.67) |
| H12 | Vigilant sub-mod prefix (zzzCH / zzzBM / zzzCO) | Dead: ×3.33 / ×3.8 / ×2.78, same band |

**M4, M5, M7, R2 (short hunts).**

| # | Hypothesis | Result |
|---|---|---|
| M4-H1 | Amount 100 = gun enchantments | **Kept**: 155 / 158 |
| M4-H2 | ×2 is a staff rule | Dead: ×2 sits on guns and unreferenced ENCH, not staves (7 non-gun pairs) |
| M4-H3 | Non-gun Glenmoril enchants tuned per item | **Kept** (Sun 1 / 2 / 3 → 54 / 70 / 90) |
| M5-H1 | Perk school = first-effect school | **Kept**: MR 365 / 365, ARR 32 / 36 |
| M5-H2 | Perk tier from cost | Loose in MR too; ARR 34 / 42 inside MR's range |
| M5-H3 | Nulled perk = NPC-only spell | **Kept**: 38 / 38 |
| M7-H1 | Player shout → escalating, NPC shout → flat | **Kept**: vanilla 26 / 26, Requiem NPC 9 / 10, ARR 7 / 9 |
| R2-H1 | Flag ⇔ any Healing trait | Dead in Requiem (Spriggan, Werebear lack it) |
| R2-H2 | Flag copies the Healing comparable's flag | **Kept**: 57 / 72 |
| R2-H3 | Flag added with an added Healing trait | 23 / 25 adds |
