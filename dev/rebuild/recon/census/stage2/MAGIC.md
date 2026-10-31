# MAGIC census: Spell, MagicEffect, ObjectEffect, Scroll, Book, Shout

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 3,222 records · 12,387 ledger rows (Spell 6,218 · ENCH 2,810 · MGEF 1,422 · Book 1,247 · Scroll 636 · Shout 54)
· 47 plugins · held-out 225 rows in 2 plugins (Falkreath Shades, The Rot Below; the other 4 eval references touch no magic).
Dominant plugins: Spell = Vigilant / Glenmoril / Dac0da / Apocalypse; ENCH = Glenmoril (44 %); MGEF, Scroll, Book = Apocalypse
(76 % / 90 % / 14 % + LOTD 62 % presentation). The Bruma master patch is minor here (≤ 5 % of any type).

**Layer:** the Reqtificator holds no Spell / MGEF / ENCH / SCRL / SHOU / BOOK logic (fact 32; verifier grep of 157 C# files,
0 hits for the MGEF keywords). **Everything in this domain is upstream input; the bake package is empty.**
"Requiem's practice" here mostly means **Magic Redone (MR) on Skyrim.esm FormIDs** (FACTS §D), so comparables are live winners.

Spread figures below are my ledger counts (per-plugin row counts from `ledger.jsonl`); family counts come from the consensus files.

## Conventions

| # | Convention | Class | Layer | Support rows / plugins | Spread | Held-out | Conf |
|---|---|---|---|---|---|---|---|
| 1 | Creature-ability spells: swap REQ_NULL_Ab* (Skyrim.esm resist / weakness / waterbreathing / blind) for REQ_AbHide_* (Requiem.esp 0008CC-D1, 0008DD/DE, 0008F8, 000A15-18) at the **same magnitude** | Requiem-aligned (212 Requiem spells carry AbHide, 8 still REQ_NULL; fact 3) | input | ~1,000 AbHide adds / 14 | Glenmoril 362, Vigilant 236, Dac0da 172, Unslaad 112, AFDI 58, Lucien 17; holds in every creature mod | **follows** (RB Adabelle / FungalDraugr 3-for-3; FS PyromancerGhost, ScarletSurge; magnitude kept) | high |
| 2 | Resist Damage 0CAB90 → REQ_AbHide_FortifyArmorRating 0008CB, **re-valued** ×2–6 (Requiem's REQ_Trait_Armor_* scale): a minority variant | Requiem-aligned (variant) | input | 24 adds / 5 | Dac0da 16; elsewhere 0CAB90 is **dropped**, which is the Requiem-consistent majority (see S3) | FS Putridman 500 → 0008CB 800: follows the re-value form | med |
| 3 | NPC / creature attack spells: mod-owned damage magnitudes raised (ARR median ~×2; per spell, same MGEF) | Requiem-aligned (Requiem rescales vanilla creature spells to tier values, ×2 to ~×6, no fixed factor: Storm Atronach bolt → 50, chain → 60, Dwarven Spider spit 15 / 25 → 85 = ×3.4–5.7, Centurion steam 75 → 150, Falmer poison 5–12 → 33 ≥ ×3) | input | ~580 paired re-saves / 12 | median ratio Glenmoril 2.14, Dac0da 2.0, Unslaad 2.0, AFDI 2.0, Vigilant 1.5 | **follows** (RB 1.82 over 26 pairs, FS 2.0 over 7) | med |
| 4 | Player-tome spells and scrolls rescaled **down** to MR scale | Requiem-aligned (MR tiers) | input | ~200 pairs / 4 | Apocalypse 0.53 (99 spell + 69 scroll pairs), Become a Bard 0.5, LOTD 0.37 | n/a (held-out add no player spells) | low |
| 5 | Destruction projectiles / staves gain MR impact effects: 0153D3 (MR's REQ_Effect_DestructionGM_Impact_Stagger), 005AAx slow / burn / cremation, 00760A | Requiem-aligned (MR stacks these on its own spells and staves) | input | ~710 adds / 15+ (Spell, ENCH, Scroll) | Vigilant 226, Apocalypse 196, Glenmoril 146, Dac0da 47, Bruma 16, Unslaad 14 | **follows** (RB 12 + 2 ENCH, FS 10 + 3 ENCH) | high |
| 6 | Vanilla perk-proc effects copied into NPC spells are dropped (0F3930 Fireball Fear, 0F3F0C / 0F3F0D PerkDisintegrate*) | house convention (lead; Aaron confirms) | input | 17 / 6 | small but in 6 plugins | **follows** (RB 2, FS 4) | low |
| 7 | Spell BaseCost hand-set on the MR school / tier table **with ManualCostCalc** | Requiem-aligned (MR 540 / 571 SPEL; Requiem.esp 216) | input | 348 BaseCost + ~315 MCC adds / 13 | BaseCost rows: Apocalypse 173, Vigilant 83, Bruma 29, Glenmoril 18, Dac0da 13, Unslaad 9; the 94 BaseCost rows without a flag row already had MCC in source (2 exceptions) | **follows** (FS 6 MCC / 4 costs, RB 9 MCC / 2 costs) | high |
| 8 | Spell ChargeTime by tier: 0.25 / 0.5 / 0.75 / 1 / 1.25 summons / 3 Master BothHands rituals | Requiem-aligned (MR distribution) | input | 233 / 12 | Apocalypse 142 (61 %); Bruma 22 on-ladder; Vigilant 43 mostly 0.5; 13 off-ladder (0.2, 0.3, 0.375, 1.5, 2) | follows (FS 0.75 ×3) | med |
| 9 | NPC / creature attack spells gain NoAbsorbOrReflect and / or IgnoreResistance | Requiem-aligned for summons / bound / necro (MR); creature-attack use unverified | input | ~55 + ~37 / 10 | Apocalypse 18 / 22, Vigilant 7, Dac0da 5 / 3, Bruma 6 / 6, Remiel 5 | **follows** (RB 7 / 3, FS 1 / 1) | med |
| 10 | HalfCostPerk = REQ_<School>_Mastery_* (0C44B7–0C44CA), tier fits cost loosely; **school must match** | Requiem-aligned (MR is loose too) | input | 80 / 7 | Vigilant 48 (60 %), Glenmoril 11; untouched audit: 0 non-REQ perks in 8 sources | not exercised | low |
| 11 | Spell Range: summons 20, area banish / command 30, bound / reanimate 0; Master ritual summons BothHands 013F45 | Requiem-aligned (MR reads) | input | 5 + 2 / 3 | CC S&S 3, Vigilant 2 | FS SparkofLife 0 → 30 follows | low |
| 12 | New ability spells copy Requiem's REQ_Trait_* shape (UnarmedDamage 0008C7, Boss) | Requiem-aligned (REQ_Trait_Boss_* ×6 in Requiem.esp) | input | 11 / 4 | Dragon Tweaks 8 = Authoria_DragonWeatherMarker_* (no Requiem precedent) | not exercised | low |
| 13 | MGEF PowerMod keywords: REQ_NoDurationScaling 412EDF on magnitude-scaled, REQ_NoMagnitudeScaling 3FCA4C on duration / cloak / summon, REQ_SpellConcentration 2FFEAD on Concentration, REQ_PoisonSpell AD3904, REQ_Absorb ADDDF7, Nox_KW_* | Requiem-aligned in kind (runtime-consumed, Requiem 6.0.2 changelog), **stricter than Requiem** (NoMag on 140 / 148 PAD v Requiem 19 / 226) | input | 767 / 9 | **Apocalypse 88 %** (636 / 719); outside it Bruma 46, Dac0da 16, others ≤ 5 | **not followed**: neither held-out patch tags its mod MGEFs | med |
| 14 | REQ_NoDurationScaling on instant (NoDuration-flag) effects | noise (redundant; Requiem 0 / 164) | input | 75 of 91 instant / 1 | Apocalypse only | not followed | med |
| 15 | Absorb / drain MGEFs gated: HasKeyword ActorTypeDwarven 01397A == 0 + HasMagicEffectKeyword REQ_NoLifeDrainAllowed 2EA062 == 0 (Subject) | Requiem-aligned (Requiem 027F96) | input | 13 / 4 | Vigilant 8, Dac0da 3, LOTD 1 | **follows** (FS GRVEScarletFFAimed) | med |
| 16 | Destruction damage MGEFs get NoDeathDispel (school, not "hostile", is the criterion); cloak MGEFs go PowerAffectsDuration + NoMag keyword, drop DispelWithKeywords | Requiem-aligned (Requiem Destruction 69 / 76; Requiem cloaks) | input | 28 + 1 pattern / 3 | Apocalypse only for NoDeathDispel; cloak Vigilant / Dac0da | not exercised | low |
| 17 | Condition lists re-saved identically (added / removed pairs), MinimumSkillLevel zeroed, FX / sound swaps | noise (MinSkill does not match Requiem, which tracks tier; spellmaking-only) | input | ~110 / 6 | Apocalypse dominant | n/a | med |
| 18 | ENCH hand-valued: EnchantmentAmount == EnchantmentCost and **NoAutoCalc** | Requiem-aligned (MR 10FCF6 165 / 165 NoAutoCalc; Requiem 07217D, 072180) | input | 302 equal pairs (0 unequal; 15 ENCH change one field only, e.g. RB 9A39CC Amount only) + 146 flag rows / 12 | Glenmoril 161, Apocalypse 72, Vigilant 28, LOTD 22, Bruma 11 | **follows** (FS 135 → 175, RB 410 → 150 and 21 → 115, all NoAutoCalc) | high |
| 19 | Staff / weapon ENCH effects retuned per item to MR staff comparables (MR: IcySpear 60 → 150, slow 50 → 12.5) | Requiem-aligned in method; **no uniform factor** | input | 1,850 / 23 | Glenmoril ×1.83 (299 pairs), Vigilant ×2.4 up; LOTD 0.6, Unslaad 0.56, Dac0da 0.68, Apocalypse 0.67 down | RB ×0.65, FS ×1.1: per item | low |
| 20 | ENCH flat Amount 100 on gun / staff enchants | house convention (Aaron confirms) | input | 156 / 2 | Glenmoril 152, Bruma 4 | not followed (FS 175, RB 150) | low |
| 21 | ENCH ChargeTime set per staff (0.5 default, 1 for tier-4 / undead) | Requiem-aligned (Requiem 0 / 0.5 / 1 / 3) | input | 155 / 5 | Glenmoril 97, Apocalypse 52 | not exercised | low |
| 22 | ENCH BaseEnchantment nulled or re-pointed when effects are replaced | house convention (unconfirmed; Requiem's Banish0 / Fear0 bases are null) | input | 21 / 3 | LOTD 15 | not exercised | low |
| 23 | Scroll: Value == BaseCost, ManualCostCalc, ChargeTime on the 0.25–3 ladder, Weight 0.5 | Requiem-aligned (MR 40 / 40 SCRL; Requiem scrolls 0.5 wt) | input | 127 Value, 89 Cost, 77 CT, 56 flags, 6 wt / 4 | **Apocalypse 87–90 %**; Unslaad 2 equal; Glenmoril 3 unequal; others ChargeTime flat 0.5 | n/a (no held-out scrolls) | med |
| 24 | Spell tomes priced on the MR ladder: 100 / 300–400 / 500–700 / 800–900 / 2000 | Requiem-aligned (40 MR REQ_Tome_* read) | input | 359 / 15 | Apocalypse 175, Bruma 47, ToK 10, Glenmoril flat 500, Bard flat 100; **Vigilant 69 at 1000–4000, Unslaad up to 5000, Dawnguard Arsenal 2500 sit above** | **follows** (FS 345 → 500 ×3, 645 → 700) | med |
| 25 | LOTD display support on books: VendorItem* keywords, dbm_treasmapscript, inventory art | house convention (LOTD compat, not Requiem) | input | ~190 / 2 | LOTD only | n/a | med |
| 26 | Custom shouts: flat RecoveryTime on all three words (Vigilant 5 → 10, RockSpear 8); Dac0da creature force shouts re-pointed to Unrelenting Force 013F39 / 013F3A (Requiem winners) | Requiem-aligned (Requiem custom shouts flat; escalation is MR's vanilla shouts) | input | 27 / 2 | Vigilant 21, Dac0da 6 | n/a | low |

## Upstream checklist (what a magic patch must carry)

- [ ] Creature abilities: every REQ_NULL_Ab* effect → matching REQ_AbHide_* at the same magnitude (conv. 1); Resist Damage 0CAB90
      (REQ_NULL_AbResistDamage) is usually dropped, which is consistent with Requiem; converting to 0008CB is the Dac0da / FS variant (conv. 2, Aaron call).
- [ ] NPC attack spells: damage to Requiem creature scale (~×2, per spell against a Requiem comparable); add MR stagger / slow impact
      effects to destruction projectiles; drop vanilla perk-proc effects; NoAbsorbOrReflect / IgnoreResistance where MR uses them.
- [ ] Player spells: BaseCost on the MR school / tier table **and** ManualCostCalc (never one without the other); ChargeTime on the
      tier ladder; HalfCostPerk = the right school's REQ_ mastery perk; Range 20 / 30 / 0 by kind; magnitudes to MR scale.
- [ ] New MGEFs: Requiem PowerMod keywords by scaling type (NoDur on PAM non-instant, NoMag on PAD / cloak / summon, Concentration
      keyword), absorb gates, NoDeathDispel on Destruction damage. Skip NoDur on instant effects.
- [ ] Enchantments: Amount == Cost, NoAutoCalc, effects retuned per item to an MR staff / Requiem enchant comparable; ChargeTime set.
- [ ] Scrolls: Value == BaseCost, ManualCostCalc, tier ChargeTime, Weight 0.5. Tomes: MR price ladder by the taught spell's tier.
- [ ] Shouts: flat recovery per word.

**Bake package (downstream): none.** No magic field is written by the build, so nothing here is redundant with it and nothing is
"baked". (Spell-carrying NPCs are covered in the NPC domain.)

## Per-type detail

**Spell (6,218 rows / 1,412 records / 31 plugins).** Effects added / removed is 81 % of rows: 742 of 1,156 touched spells swap an
effect. The swap (conv. 1) is magnitude-preserving in 134 / 136 Poison, 153 / 153, 149 / 149, 72 / 72, 26 / 26, 25 / 25, 137 / 137 pairs
(consensus support). The removed side's biggest id is 0CAB90 (201 rows) and is not one-for-one (verifier, material error fixed).
Fourteen spells keep a REQ_NULL effect at the winner (consensus evidence; not re-listed). FS GRVEAbShadowman shows a source value
(Weakness to Magic 90000) normalised to Drain Magic Resistance 000A18 100: the swap fixes absurd source values too. Summon
durations are not uniformly 15 (fact 28): MR itself has Thralls 300, Reanimate 60, Bound 60.

**MagicEffect (1,422 / 569 / 19).** Almost all substance is Apocalypse's keyword pass (conv. 13). 252 rows are re-ordered pairs (noise
by script). Outside Apocalypse, MGEF edits are few and per-effect: absorb gates, cloak flags, one archetype re-point to Requiem's
Ancient Spirit Bear AD3861. UNTOUCHED: Vigilant 527, Glenmoril 449, Dac0da 234, Unslaad 215 MGEFs untouched, judged legitimate.

**ObjectEffect (2,810 / 612 / 27).** Glenmoril staves (1,225 rows) dominate. Rule-level: Amount == Cost + NoAutoCalc holds in every
plugin and both held-out. Value-level (flat 100, ×2) is Glenmoril-only.

**Scroll (636 / 153 / 5).** Apocalypse is 90 %. The reader's "Requiem 25–500 Value ladder" was wrong (verifier: MR Value == BaseCost on
40 / 40). Requiem.esp-only scrolls have looser values.

**Book (1,247 / 467 / 17).** 606 presentation rows (script) + LOTD art / keywords. The substantive family is tome Value (359).

**Shout (54 / 9 / 2).** Two plugins only; low confidence.

## Suspected bugs (for Aaron's review, not filed)

| # | Record (plugin) | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| S1 | 10F6B4:Vigilant.esm zzzCHConjureVernaccus (Reqtificated - Vigilant) | Flags / BaseCost | BaseCost 350, Flags NoAbsorbOrReflect only | + ManualCostCalc (else 350 is ignored, engine autocalcs) | verifier live read; Bourlor 13BAAF has MCC | outlier-bug |
| S2 | 10F6B4 Vernaccus, 13BAAF Bourlor (Vigilant) | CastDuration / ChargeTime | CastDuration 3, ChargeTime 0 / 0.5 | ChargeTime 3 (Master BothHands ritual); CastDuration is inert on FireAndForget | MR 571 / 571 CastDuration 0; perk raised to Master + BothHands in same edit | outlier-bug |
| S3 | 199 creature abilities / 201 rows, e.g. 00795F:Glenmoril.esm zzzLrhAbGhoul (Mite 60, Gulakhan 50, Ghoul 30, Flyman 30) | Effects | Resist Damage 0CAB90 removed, no replacement | drop is the **Requiem-consistent reading**: 0CAB90 is REQ_NULL_AbResistDamage, and Requiem leaves it only on REQ_NULL_* spells (7 live); converting to 0008CB (Dac0da / FS) is the alternative | 0CAB90 removed: Glenmoril 86, Vigilant 53, Dac0da 35, Unslaad 22 v 0008CB added 24; live 00795F: the source has no armour effect, the winner keeps its 5 AbHide-able effects and drops Resist Damage 30 | suspected bug (weak; probably intended) |
| S4 | 372295, 372298:Glenmoril.esm throwing knives (Reqtificated - Glenmoril) | HalfCostPerk | 0C44BC REQ_Conjuration_Mastery_050 | a Destruction mastery perk (effects are Destruction / MR DestructionGM) | verifier live read | outlier-bug |
| S5 | 03AF3B, 03AF41, 1A7926, 083FAB:Apocalypse - Magic of Skyrim.esp (MysticWind, CircleOfTheMoons, Willpower, Transcendence scrolls) | BaseCost | 98 / 131 / 120 / 1264 → 0, Value 45 / 120 / 200 / 500; none carry ManualCostCalc (engine autocalcs) | BaseCost == Value with ManualCostCalc (rest of Apocalypse, 66 / 66) | ledger rows; live flags 0 / NoAbsorbOrReflect | outlier-bug (low impact) |
| S6 | Blazing Cow ability (Glenmoril) | Effects | 064459 (mag 10) removed, no replacement | a mapped AbHide effect | verifier note; FormID of the spell not recorded | outlier-bug (unverified) |
| S7 | 13 spells with off-ladder ChargeTime (Glenmoril knives 0.2, Vigilant Firefly 0.3, Dac0da HealthDrain 0.3, ToK Shapeshift 1.5, GCoN Recall 2) | ChargeTime | as listed | ladder value or deliberate | ledger | noise / outlier (Aaron call) |

## Open calls for Aaron

1. **Resist Damage (S3):** drop it or convert to 0008CB at trait scale? Both happen. Drop is the majority (199 spells) and fits Requiem: 0CAB90 is REQ_NULL_AbResistDamage, Requiem's non-null spells carry none (it stays only on 7 REQ_NULL_* spells), and 00795F's source has no armour effect. Conversion is Dac0da / FS only.
2. **MGEF keywords (conv. 13):** required on every new MGEF? The held-out patches skip them; Apocalypse applies them stricter than Requiem.
3. **Tome prices above 2000** (Vigilant 1000–4000, Unslaad 5000, DGA 2500): deliberate premium, or should they follow the MR ladder?
   Glenmoril flat 500: ladder or deliberate?
4. **ENCH flat 100 and ×2 staves** (Glenmoril): rule or per-item tuning?
5. **HalfCostPerk:** rough tier fit acceptable for NPC / creature spells, school-match mandatory?
6. **Perk-proc drop (conv. 6):** confirm as a rule.
7. **Shouts:** flat recovery for custom shouts, or escalate like MR's vanilla shouts?
8. **Vernaccus / Bourlor** (S1, S2): deliberate 3 s cast + BothHands?

## Limits

- **Unsampled families (leads only):** MGEF ResistValue, MagicSkill, BaseCost, Keywords removed, SpellmakingCastingTime, VMAD
  (REQ_PoisonISM on Dac0da poisons), *defined; Scroll Weight and Keywords (VendorItemScroll on CC S&S); Book Keywords changed;
  ENCH *defined, WornRestrictions. Scroll effect rebuild is **cannot-tell** (verifier). Conv. 6, 9 (creature half), 20, 22 rest on
  my ledger counts or the reader alone.
- **Held-out coverage:** only 2 of 6 references touch magic; no held-out Scroll, Shout, MGEF-keyword or HalfCostPerk rows, so conv.
  10–13, 16, 21–23, 26 are unvalidated.
- **Untouched (UNTOUCHED.md):** source MGEFs and spells of Vigilant / Glenmoril / Unslaad / Dac0da / Apocalypse judged legitimate
  (sources already carry REQ_ perks). Glenmoril's 255 untouched books include spell tomes (`zzzRevSpellTome*`) not priced: unclear.
- **ksws03:** `ksws03_quest.esp` fails to load in the current order; its 9 ENCH rows and 1 defined spell (cloak) cannot be read live.
- Reads this session: 2 (`housecarl_records`: 7 MGEF identities; 25 Requiem creature-spell deltas v Skyrim.esm for conv. 3).
