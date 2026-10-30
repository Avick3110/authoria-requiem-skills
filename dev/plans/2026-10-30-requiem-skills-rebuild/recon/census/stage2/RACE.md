# RACE census: how ARR's handmade Requiem patches treat races

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 590 RACE records (590 distinct FormIDs), 6,002 ledger rows, 27 patch plugins. Held-out: 2 of the 6 eval
references carry races: **The Rot Below** (29 races, 330 rows) and **Falkreath Shades** (5 races, 57 rows).
Conventions below come from the other 25 plugins. Counts are from `consensus/Race.json` and the ledger. Script-decided
rows: 1,091 list reorders, 17 presentation, 4 bookkeeping (all noise). 7 live reads this session (cited as *live*).

**Plugin weight.** Glenmoril dominates: 1,615 rows, 181 races. Then Vigilant (1,234 / 106), Dac0da (971 / 85) and the
Bruma master patch (589 / 53). The Bruma patch dominates **no** major race family. Its weight is in AttackRace and
UnarmedReach only. The Races Merge master patch (162 rows / 35 races, all vanilla or DLC races) is a compatibility
merge, not Requiem patching (row 15).

**Headline.** For races, everything is **input**. Nothing ARR writes on a RACE is something the build would produce:
- the RFTI output has 0 RACE records (FACTS §D);
- `CustomRacePatching.cs` reaches only Playable races whose Starting stats sum to ≤ 200;
- *live*: only 6 of the 590 patched races are Playable (the vanilla Breton, Dunmer, Altmer, Imperial, Nord and
  Redguard, in Races Merge), and each sums to 300. The gate reads the final winner record, which for these 6 is Races Merge, so these are
  Races Merge's sums. The gate therefore never fires on any ARR-patched race. Unpatched Playable races (UBE ×8,
  HalfKhajiit, and others) are outside this claim.

**Bake package: empty.** ARR's race work is the same live-analogy the skills teach. Copy the closest Requiem race's
trait spells, keywords, stat scale and flags, then judge the values.

## Conventions

| # | Convention | Class | Layer | Support (rows / plugins) | Per-plugin spread | Held-out | Conf. |
|---|---|---|---|---|---|---|---|
| 1 | **Creature races:** add `REQ_Trait_Armor_<closest Requiem creature>`; add `REQ_Trait_Healing_<X>` for regenerators; keep the source mod's own ability spell; leave out `REQ_Trait_FX_*`. **Resistances come through the source ability** (patch-edited, REQ_AbHide fortify / drain resists) rather than `REQ_Trait_Resist_<X>`. Armor without Resist: Glenmoril 140 / with 10; Vigilant 84 / 4; Dac0da 68 / 2 | Requiem-aligned for Armor / Healing (GiantRace, TrollRace, ChaurusRace, BearBlackRace reads). The resist-via-source-ability route is a **house variant** (doc verifier: zzzGHAbMudSpawn 012E07 carries Fire 50 / Poison 100 / drain Shock 50 / Frost 25; 107 Glenmoril-patch spells carry *Resistance effects) | input | ActorEffect added 808 / 25, changed 73 / 15; 313 / 590 races carry an Armor trait | Glenmoril 210, Vigilant 118, Dac0da 103, Bruma 78, Tools of Kagrenac 57; holds in 20+ plugins | follows on Armor. Resist_<X> is used more in held-out: Rot Below 8 / 12 armoured races, Shades 1 / 4 | high (verifier-fixed twice) |
| 2 | **Humanoid clones** get the full ActorEffect set of the closest vanilla race: NoHealthRegen `609AF0`, MassEffect `82CC14`, plus that race's Blood / Heritage / Physique / Cuisine spells | Requiem-aligned (Requiem NordRace / HighElfRace / KhajiitRace reads). Input: the build gate never fires | input | 609AF0 33 rows, 82CC14 32; TOK Polymorph* = KhajiitRace etc. exactly | Tools of Kagrenac, Bruma, Kaidan, Penitus Occulatus, Frozen Heart, Auri, Snow Elven Arsenal | follows. Rot Below: 10 races get the Nord set (609AF0, 82CC14, AE3B19 / 25 / 35) | high |
| 3 | **Strip the vanilla ability** a clone inherits (`REQ_NULL_Race*`, `REQ_NULL_Ab*`, `REQ_LEGACY_*`, `REQ_DEPRECATED_*`) and swap in `REQ_Trait_*` | Requiem-aligned (Requiem NordRace holds only REQ_Trait_*; DragonRace = Races Merge). 090357 LEGACY Dwemer resist is replaced by `REQ_Trait_Resist_Dwarven*`, so no resist is lost | input | 78 / 12 (76 / 78 paired with adds) | TOK 19, Glenmoril 13, Lucien 8, Bruma 7, Races Merge 7 | follows (Rot Below 7) | med-high |
| 4 | **Starting Health / Magicka / Stamina at Requiem scale**, copied from or scaled off the comparable: creature Health 100–1,000; Stamina 250–10,000 (10,000 = Draugr / undead); Magicka 0 for non-casters | Requiem-aligned in scale. Values are judgement: copies where a clone exists (MudGiant = GiantRace 250 / 0 / 800; TOK Khajiit = KhajiitRace), otherwise hand-picked | input | 690 / 21 (Health 312, Stamina 309, Magicka 69) | Glenmoril 276 of 690; Dac0da, Vigilant, Bruma, AFDI each 30–100 | follows (Rot Below 27, Shades 3) | med |
| 5 | **Requiem race keywords:** `REQ_DropsBloodKeyword 586728` (flesh), `REQ_MinorKnockdownImmunity 5F367F` (large), `REQ_SneakAttackImmunity 4F6D1E` (undead / constructs), `REQ_ActorTypeDaedraNoFlesh 44BE5E`, ActorType* and immunity keywords | Requiem-aligned (Draugr / Giant / Troll / Bear / HighElf reads; FrostbiteSpiderRaceGiant has 5F367F, *live*) | input | 651 / 19 (4F6D1E 196, 586728 176, 5F367F 163) | Glenmoril 190, Dac0da 131, Vigilant 130, Bruma 60 | follows (Rot Below 37, Shades 4) | med |
| 6 | **Narrow EquipmentFlags** from the source catch-all to the weapon types the race really uses: HandToHand for beasts, TwoHandSword for giants, the full set for humanoids | Requiem-aligned (Troll / Bear / SabreCat / Giant / Draugr / HighElf reads); no code touches it | input | 438 / 15 (→ HandToHand 157) | Glenmoril 181, Vigilant 105, Dac0da 85 | follows (Rot Below 29, Shades 5) | med |
| 7 | **UnarmedDamage at Requiem scale:** beasts and giants 50–90, undead 20, automata 300+, humanoids 5–8 | Requiem-aligned in scale only. Values are per-race judgement (MudGiant 90 vs GiantRace 60) | input | 360 / 16 (345 up) | Glenmoril 138, Dac0da 70, Vigilant 68 | follows (Rot Below 9, Shades 4) | med |
| 8 | **UnarmedReach** at the 96 / 64 default → 73 | Requiem-aligned (Requiem writes 96 → 73 on 24 races; 73 is its commonest value) | input | 89 / 5; 56 end at 73 | Glenmoril 50, Bruma 20; **5 plugins only** | not seen | med (narrow) |
| 9 | **Regen:** creatures 0 / 0 / 10; magical beings (atronach-like) Stamina regen 1,000 with Starting Stamina 10,000; non-casters Magicka regen → 0 | Requiem-aligned (verifier: AtronachFlame / Storm, Ash Guardian 0 / 15 / 1000; 23 Requiem winners) | input | 164 / 14 | Vigilant, Dac0da, AFDI lead | little (Rot Below 4) | low-med |
| 10 | **BaseMass at Requiem values:** Giant 10, Dragon 20, Draugr 1.5, wisp-like 2.2, small creatures 0.25–0.5 | Requiem-aligned (fact 19; the C# never sets mass) | input | 132 / 16 (4 → 10 ×23, 1 → 2.2 ×10) | Dac0da 44, Vigilant 27 | follows (Rot Below 14, Shades 2) | med (family **unsampled**; see bug 2) |
| 11 | **Skill boosts:** a creature gets one or two proficiency boosts, about 35 for the lead skill and 5–20 for minor ones. Slot position is cosmetic, so re-slots are noise | Requiem-aligned for the adds (DraugrMagicRace Illusion 35, MG07DogRace Sneak 35, DwarvenSphere Illusion 95); re-slots = noise | input | SkillBoost0–6 ≈ 500 rows / 12 | Vigilant, Dac0da, Glenmoril (genuine adds); Bruma re-slots only | follows (Shades 13 rows, Rot Below 16) | med |
| 12 | **BaseCarryWeight:** clones of playable races 300 → the Requiem value of the matching race (85–125); creatures 200, with 9,999 / 2,000 for Giant- / Werewolf-likes | Requiem-aligned (TOK Khajiit 90 = KhajiitRace; Requiem GiantRace 9999, WerewolfBeast 2000) | input | 39 / 9 | TOK 10, Rot Below 11 (held-out) | follows | med |
| 13 | **RegenHpInCombat flag** on regenerating monsters (trolls). Glenmoril also adds it to wolves and bears | Mixed: trolls are Requiem-aligned. Wolf / bear adds are house (Requiem's Wolf / Bear / SabreCat lack it; fact 15) | input | Flags 48 / 8, 31 single-use patterns | mostly Glenmoril and Races Merge | Rot Below 7 Flags rows (incl. 'Test'-race leftovers) | low |
| 14 | **AttackRace** set on modded creatures to their vanilla analogue (wolf, sabrecat, bear, mammoth, ogre) | house convention? No Requiem / code source (**unsampled**) | input | 34 / 2 | Bruma and Moonpath only | not seen | low |
| 15 | **Races Merge master patch** carries source-mod values over Requiem: TNT-BiggerTrolls Height / Weight, custom skeletons, biped-slot repairs, the TDM boar BPTD, Races Redone stats on playable races | house convention (compatibility merge, not a Requiem rule) | input | Weight 8, SkeletalModel 8, Biped 6, BodyPartData 1 / 1–2 | Races Merge only | n/a | med |
| 16 | **Noise:** attack / movement-name / equipment-slot reorders (1,091 rows). HeadData pairs re-serialised (242). Attacks added / removed: 39 / 44 races are multiset-equal. Biped names, voice, skin, hair colour | noise | n/a | ~1,400 / 25 | all | same | high |
| 17 | **No downstream bake.** No race row is build output, so a patch carries everything. The six vanilla Playable races sum to 300, so even CustomRacePatching (+50 stats, carry 110, unarmed 8, the mass / regen spells) never runs here | code fact (not a bake: `CustomRacePatching.cs` gate on the winner record; RFTI 0 RACE) + *live* Playable census | n/a | 6 Playable / 590 | — | — | high |
| 18 | **Waterwalking** `0173C2:Dragonborn.esm` on spirit / wraith races | unclear (no Requiem race read for it) | input | 22 rows | Vigilant, Glenmoril | follows (Rot Below 4) | low |

**Held-out validation.** Both held-out patches follow 1–7, 10–12 and 16:
- **Falkreath Shades** golems get Armor_Giant-style traits. Only 1 of its 4 armoured races also gets Resist_<X>.
- **The Rot Below** adds Resist_<X> on 8 of 12 armoured races. Resist is used more outside the Glenmoril / Vigilant /
  Dac0da style.
- **The Rot Below** humanoids (Malkifer, Knight of Rot and others) get exactly the Nord ActorEffect set. Its 'Test'
  races pick up flag edits.

Neither shows AttackRace (14) or UnarmedReach (8), which are narrow conventions anyway.

## Upstream checklist (what a race patch must carry)

1. Pick the closest Requiem race (read the **live winner**). For a creature, use the matching Requiem creature (Giant,
   Troll, Chaurus, Draugr, FrostbiteSpider…). For a humanoid clone, use the vanilla playable race it copies.
2. ActorEffect:
   - creature: `REQ_Trait_Armor_<X>`, plus `Healing_<X>` if it regenerates, and keep the mod's own abilities;
   - humanoid: the vanilla race's full REQ_Trait set (609AF0, 82CC14, Blood / Heritage / …);
   - drop any inherited REQ_NULL / LEGACY / DEPRECATED ability;
   - resistances: either `Resist_<X>` or the source ability's AbHide resists. Check parity with the comparable's
     resist (open call 1).
3. Keywords: DropsBlood / MinorKnockdownImmunity / SneakAttackImmunity / ActorType* as the comparable has them.
4. Starting Health / Magicka / Stamina, Regen, UnarmedDamage, BaseMass and BaseCarryWeight at the comparable's scale.
5. EquipmentFlags narrowed to the weapon types the race uses. UnarmedReach 73 when the source left 96 / 64.
6. Skill boosts: one lead skill at ~35 where Requiem's analogue has one.
7. Don't stamp anything for the build. Nothing on RACE is re-derived (unless the race is Playable and sums to ≤ 200;
   none in ARR do).

## Per-type detail (Race)

- **ActorEffect** is the substance, with 132 add patterns. Top ids: AD39E9 Armor_Giant 45, 609AF0 33, 82CC14 32,
  03280E Armor_DraugrArmored 30, AD39F0 Gargoyle 26, AD39E7 Chaurus 26.
- 106 / 590 races get no ActorEffect change, which is not the same as having no trait. Live winners with no
  REQ_Trait_* in the five biggest plugins: 60 (Glenmoril 23, Vigilant 13, Dac0da 10, Bruma 9, Races Merge 5). The
  other 22 plugins were not measured. Some are legit, since DogRace has no trait (*live*); the rest are bug 4 leads.
- Stats mostly copy an existing comparable when there is a clone. Otherwise the author picks Requiem-scale numbers;
  no formula was found.
- The TrollRace winner (Races Merge) has Starting Health 300, against 150 in Requiem.esp and TNT. That is a hand
  tune (open call 4).

## Suspected bugs (for Aaron's review, not filed)

| # | Record (plugin) | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| 1 | zzzCHShoggothRace `080D5D:Vigilant.esm` (Reqtificated - Vigilant) | Starting[Health] | **−150** (source 125); also Stamina 100 → 1000 | a positive Requiem-scale base, e.g. 125–300 | *live* winner read: Health −150, Magicka 0, Stamina 1000. Traits Armor_Chaurus. The sign looks like a slip | outlier-bug |
| 2 | zzzChScorpionGiantRace `534133:Vigilant.esm`; also zzzChScorpionLargeRace `534134`, zzzCHShoggothMotherRace `080D5E` (Vigilant) | BaseMass | 0.25 (source 4 / 3.5 / 5) | ~4 for a giant scorpion: its traits are copied from FrostbiteSpiderRaceGiant, BaseMass 4 | *live*: FrostbiteSpiderRaceGiant BaseMass 4; FrostbiteSpiderRace (small) 0.25. The small-spider mass looks copied onto the giant | suspected outlier |
| 3 | ~140 Glenmoril creature races (e.g. zzzGHMudGiantRace `012E09`, zzzGHMudWormRace `012EAE`) | ActorEffect | Armor_<X> without Resist_<X> | resist parity with the comparable (GiantRace 0F2595, ChaurusRace AE3ACD, TrollRace 02F617) | **Not a gap.** The resists arrive through the patched source ability (zzzGHAbMudSpawn: Fire 50 / Poison 100; 107 Glenmoril-patch spells carry *Resistance effects). Per-race parity is unverified, e.g. MudWorm's ability was not read | lead (per-race parity check), not a bug |
| 4 | zzzRevAUFalmerRace `2E0322:Glenmoril.esm`; zzzCHMagicBroomRace `0E04DF:Vigilant.esm`; 60+ races whose winner carries no REQ_Trait_* (5 plugins measured: Glenmoril 23, Vigilant 13, Dac0da 10, Bruma 9, Races Merge 5) | ActorEffect | no REQ_Trait_* | the Falmer analogue carries `REQ_Trait_Resist_Falmer AE3AE5` + `REQ_Trait_Falmer_TheBetrayed`; constructs or daedra likely have an Armor trait | *live*: FalmerRace (Requiem) vs the patched race. Some misses are legit (DogRace has none) | suspected (low; per-race check needed) |
| 5 | CJ03RotBelowArcherTest, CJ03WispRaceTest, CJ03DwarvenBallistaRaceTest (Rot Below, held-out) | Flags, ActorEffect | Test races patched; Ballista keeps LEGACY 090357 | probably unused source dev races | family flags; references scan (verifier) | noise / outlier |
| 6 | zzzGHDweGearRace `Glenmoril.esm` | ActorEffect | keeps REQ_LEGACY_AbDwarvenAutomatons 090357 | Resist_Dwarven* like its siblings | verifier | outlier (harmless: the legacy spell still works) |

## Open calls for Aaron

1. **Resist via the source ability.** Glenmoril, Vigilant and Dac0da deliver resists through the mod's own
   (patch-edited) ability instead of `REQ_Trait_Resist_<X>`; Rot Below mostly uses Resist_<X>. Should the skill teach
   either route, with a parity check against the comparable?
2. **Wolf / bear RegenHpInCombat** (Glenmoril). Deliberate?
3. **AttackRace mapping** (Bruma, Moonpath; 34 rows). Correct per race, e.g. CYRMudcrab → 0BA545, BSKOgre → 0131F9?
4. **TrollRace Health 300** in Races Merge, against 150 in Requiem / TNT. Intended?
5. **Vigilant MovementTypes removed** (10 rows; creatures fall back to base movement speeds). Why?
6. **Waterwalking 0173C2** on spirit races. A house idiom?
7. The **3 COTR_Race clones defined** in Reqtificated - Kaidan (Dremora / HighElf / Breton). Why new records?

## Limits

- **Unsampled families (leads only):** BaseMass, Attacks added / removed (the 5 / 44 races that really differ:
  DLC2DremoraRace, VorstagRace, Othriel, 2 CC S&S), AttackRace, Kaidan-defined races, MovementTypes added,
  MaterialType, EquipmentSlots removed, and the locomotion tunings (acceleration / deceleration / angular, ≤ 4 rows
  each). Rows 10 and 14 rest on these.
- **cannot-tell:** MovementTypes removed, AccelerationRate, AngularAccelerationRate, Keywords removed (4 rows, e.g.
  013797 off GRVEGolemRace).
- **Untouched races:** `UNTOUCHED.md` covers no RACE case. Races a source adds that no ARR patch overrides were not
  audited here.
- The Requiem comparisons are reader / verifier picks of the "closest race", which is itself a judgement.
- The **ksws03_quest.esp** load failure excludes that plugin from every live read. No race evidence depends on it.
