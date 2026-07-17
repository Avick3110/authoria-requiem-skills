# Mining: Requiem - Magic Redone (MR) SPELLS — empirical doctrine

Source plugin: `Requiem - Magic Redone.esp` ("MR"). All reads are live load-order winners via houseCARL.
FormIDs are `XXXXXX:Plugin.esp`. Base FormIDs frequently sit in `Skyrim.esm`/`Requiem.esp` because MR
*overrides* the vanilla/Requiem record; `winner=Requiem - Magic Redone.esp` in every case sampled.

EditorID scheme (near-universal): **`REQ_<School><Tier>_<Archetype>_<Delivery>[_Modifier][_Hand]`**
Tomes mirror it: **`REQ_Tome_<School><Tier>_<Archetype>_<Delivery>`**. Effects: `REQ_Effect_<School><Tier>_...`
and a parallel **`REQ_Effect_<School>GM_...`** family for the perk-tier ("Grand Master") secondary effects.

---

## Job 1 — ManualCostCalc universality + cost / charge ladders

### 1a. ManualCostCalc is effectively universal on MR player spells

- Total SPEL records MR touches: **915**. With `Flags contains ManualCostCalc`: **876** (95.7%).
- Of those, `Type = Spell` (the castable-spell subtype): **833** total, **827** carry ManualCostCalc = **99.3%**.
  → Only ~6 `Type=Spell` records lack it.
- `Type = Ability`: 53; other types (Power/LesserPower/etc.) make up the rest. 49 non-`Spell` types also carry
  ManualCostCalc.
- **Exceptions** are NOT player spells: the non-scheme support records — `REQ_Ench_Fireburst/Frostburst/Shockburst_Explosion`
  (enchantment-driver spells, BaseCost 0), `REQ_Creature_*` innate creature abilities, and the two vanilla
  `DLC2Ignite/DLC2Freeze` (Miraak). 
- **DOCTRINE:** every castable player spell you emit for MR must set `Flags += ManualCostCalc`. Without it the engine
  auto-derives magicka cost from magnitude/duration and the whole hand-authored cost ladder below is ignored.

### 1b. BaseCost ladder (Destruction, fully mapped; other schools headline values)

Destruction is the cleanest ladder. Cost is **per delivery**, not one value per tier — concentration and
per-tick sub-spells carry small costs; AoE/rune/ritual deliveries multiply the base.

| Tier | Conc (ConcAimed) | FaF single (Aimed) | Touch | AimedExp / AoE | Rune | EnchSelf | Ritual | tick sub-spell |
|---|---|---|---|---|---|---|---|---|
| 1 | 40 | (40) | 40 | — | — | — | — | — |
| 2 | — | 90 | — | — | 180 | 135 | — | Hazard 15 |
| 3 | 128 | — | 128 | 240 (Exp) / 320 (Area,Cloak) | — | — | — | Cloak 20 |
| 4 | — | 330 | — | 660 (Area) | 660 | 495 | — | Hazard 30 |
| 5 | 480 | 600 | 480 | 1200 (Exp/Cloak/SelfHazard) | — | — | 1800 (RitualSelfExp) | Cloak 40 / 80 / 160 |

- ConcAimedHazard (T4) = 495; SelfExp (T4) = 396.
- Concentration "spine" cost by tier: **T1 40 → T3 128 → T5 480**. FaF single-target: **T2 90 → T4 330**.
- Multipliers off the tier base: Rune ≈ 2×, AimedArea/AoE ≈ 2×, AimedExp ≈ 0.7–1× of the AoE, EnchSelf ≈ 1.5×
  the FaF single, Ritual highest (1800 at T5).

Headline single-target BaseCost per school (for tiering a modded spell):

| School | T1 | T2 | T3 | T4 | T5 |
|---|---|---|---|---|---|
| Destruction | 40 | 90 | 128 (conc)/240 (bolt) | 330 | 480 (conc)/600 (bolt) |
| Restoration Healing | 40 | ~90 | ~150 | ~200 | 800/1500 ritual |
| Alteration Armor (flesh) | 100 | 150 | 200 | 250 | 500 / 800 ritual |
| Illusion | 75 (Frenzy) | 150 (Calm/Fear) | 225–300 | 300 (Invis)/450–600 | 1000–1200 ritual |
| Conjuration | 50–200 | 72–400 | 160–600 | 250–800 | 250–1800 (thralls scale by summon) |

Conjuration BaseCost scales with **what is summoned**, not a clean tier value: e.g. T5 DremoraLord 1000,
FlameThrall 1200, FrostThrall 1500, StormThrall 1800.

### 1c. ChargeTime ladder (seconds)

| Tier | FaF Aimed/AimedExp/AimedArea/Rune | Touch | Concentration | SelfExp | Ritual |
|---|---|---|---|---|---|
| 1 | (0.125) | 0.125 | 0 | — | — |
| 2 | 0.5 | — | 0 | — | — |
| 3 | 0.75 | 0.375 | 0 | — | — |
| 4 | 1.0 | — | 0 (ConcAimedHazard 0.5) | 0.5 | — |
| 5 | 1.25 | 0.625 | 0 | 0.5 | **3.0** (RitualSelfExp) |

- **Concentration spells: ChargeTime = 0 always.**
- **FaF single-target ChargeTime = 0.25 × tier** (T1 0.25→ but Flames is conc; Touch T1=0.125). Practically:
  T2 0.5, T3 0.75, T4 1.0, T5 1.25.
- **Touch = half the tier's aimed ChargeTime** (T1 0.125, T3 0.375, T5 0.625).
- **Master rituals = 3.0** (RitualSelfExp across schools). SelfHazard master _Damage sub-spell = 0.5.
- Per-tick sub-spells (_Damage, Cloak_Damage) = 0.

---

## Job 2 — Multi-effect pattern (complementary secondary effects)

**Core doctrine:** an MR damage/utility spell is NOT a single MGEF. MR attaches a **primary effect**
(`REQ_Effect_<School><Tier>_...`) plus one or more **`REQ_Effect_<School>GM_...` secondaries** that deliver the
element's signature rider (burn, slow, magicka-burn, DoT), a near-universal **Impact Stagger**, and often
**perk-gated bonus effects** (conditioned on `HasPerk`). When patching a modded spell, ADD the same shape its
MR comparable carries.

Canonical shapes (primary listed first; magnitude/area/duration as `m/a/d`):

**FIRE — `REQ_Destruction3_Fire_AimedExp` (01C789:Skyrim.esm, "Fireball", cost 240, ct 0.75), 3 effects:**
- [0] `REQ_Effect_Destruction3_Fire_AimedExp` (01CEA1:Skyrim.esm) — primary fire dmg m40/a15/d0
- [1] `REQ_Effect_DestructionGM_Cremation3` (005AAA:MR) — **lingering burn DoT** m3/a15/d4  ← the fire rider
- [2] `REQ_Effect_DestructionGM_Impact_Stagger` (0153D3:Skyrim.esm) — stagger m0.25/a15/d0
- No Conditions (unconditional). *Yes — MR adds a lingering-burn secondary (Cremation) to fire spells.*

**FROST — `REQ_Destruction4_Frost_Aimed` (10F7EC:Skyrim.esm, "Icy Spear", cost 330, ct 1), 5 effects:**
- [0] primary frost dmg m60
- [1] `REQ_Effect_DestructionGM_Slow_Aimed` (0B729F:Skyrim.esm) — **Slow** m5/d4  ← the frost rider
- [2] `REQ_Effect_DestructionGM_DeepFreeze4` (005AA6:MR) — d2
- [3] `REQ_Effect_DestructionGM_DeepFreeze_Weakness` (00607E:MR) — d12
- [4] Impact Stagger (0153D3) m0.25
- *Yes — frost carries a Slow secondary (+ Deep Freeze perk riders).*

**SHOCK — `REQ_Destruction3_Shock_AimedExp` (005A57:MR, "Lightning Burst", cost 240, ct 0.75), 4 effects:**
- [0] primary shock dmg m40/a15
- [1] `REQ_Effect_DestructionGM_ElectrostaticDischarge3_Magicka` (005AAF:MR) — **magicka damage m20**/a15 ← shock rider
- [2] `REQ_Effect_DestructionGM_ElectrostaticDischarge3_Stagger` (005AB4:MR) m0.25/a15
- [3] Impact Stagger (0153D3) m0.25/a15
- *Yes — shock carries a magicka-damage secondary (Electrostatic Discharge).*

**VENOM (Destruction poison) — `REQ_Destruction4_Venom_Aimed` (025F29:Requiem.esp, "Venomous Strike", 330/1), 3:**
- [0] primary m60/d1
- [1] `REQ_Effect_DestructionGM_Venom_DoT` (005B0B:MR) — **poison DoT m6/d5**  ← venom rider
- [2] Impact Stagger

**POISON (Restoration school) — `REQ_Restoration3_Poison_AimedExp` (005C19:MR, "Poison Cloud", 240/0.75), 3:**
- [0] primary `REQ_Effect_Restoration3_Poison_AimedExp` (005C1F) m10/a15/d5
- [1] `REQ_Effect_RestorationGM_ParalyzingPoison3` (005D80:MR) a15/d2  ← perk rider (paralysis)
- [2] `REQ_Effect_RestorationGM_ParalyzingPoison_Weakness` (0060D3:MR) a15/d9

**HEALING — `REQ_Restoration1_Healing_ConcSelf` (012FCC:Skyrim.esm, "Healing", 40/0), 2:**
- [0] `REQ_Effect_Restoration1_Healing_ConcSelf` (01CEA4) restore health m16/d1
- [1] `REQ_Effect_RestorationGM_Respite_ConcSelf` (04250D:Skyrim.esm) — **Respite: stamina restore m8/d1** ← healing rider

**WARD — `REQ_Restoration2_Ward_ConcSelf` (013018:Skyrim.esm, "Steadfast Ward", 30/0), 2:**
- [0] `REQ_Effect_Restoration2_Ward_ConcSelf` (00014C) ward m50
- [1] `REQ_Effect_RestorationGM_Ward_Shield` (0FCC62:Skyrim.esm) — **secondary ward-shield m50** ← perk/GM rider

**FLESH / ARMOR (Alteration) — `REQ_Alteration2_Armor_Self` (05AD5D:Skyrim.esm, "Stoneflesh on Self", 150/0.5), 3 — THE PERK-GATE EXEMPLAR:**
- [0] `REQ_Effect_Alteration2_Armor_Self` (059B7A:Skyrim.esm) — base armor m150/d60, no conditions
- [1] `REQ_Effect_AlterationGM_Armor_Improved` (104AB5:Skyrim.esm) — **m75/d60, 4 Conditions:**
  - `HasPerk` `REQ_Alteration_MageArmor_025_ImprovedMageArmor` (0D7999:Skyrim.esm) [OR]
  - `HasPerk` `REQ_LEGACY_MageArmor50` (0D799A) [OR]
  - `HasPerk` `REQ_LEGACY_MageArmor70` (0D799B)
  - `WornHasKeyword ArmorCuirass` (06C0EC:Skyrim.esm) **== 0** → this m75 arm applies when **no cuirass worn**
- [2] same `REQ_Effect_AlterationGM_Armor_Improved` (104AB5) — **m30/d60**, identical perk OR-block but
  `WornHasKeyword ArmorCuirass == 1` → the smaller m30 arm applies when **wearing a cuirass**.
- **Pattern:** the Improved Mage Armor bonus is perk-gated (any of the three MageArmor perks) and split by
  whether a cuirass is worn (bigger bonus unarmored). This is the canonical perk-gated conditional secondary.

**CONJURATION SUMMON — `REQ_Conjuration4_Daedra_FrostAtronach` (0204C4:Skyrim.esm, "Conjure Frost Atronach", 600/1), 2:**
- [0] `REQ_Effect_..._FrostAtronach` (01CEAB) summon d15, **1 Condition** (perk gate)
- [1] `REQ_Effect_..._FrostAtronach_Potent` (04E946) summon **potent variant** d15, **1 Condition**
- **Pattern:** summon + a perk-gated "Potent" upgrade summon, each guarded by a HasPerk condition.

**ILLUSION FURY — `REQ_Illusion1_Frenzy_Aimed` (04DEEB:Skyrim.esm, "Fury", 75/0.25), 4:**
- [0] `..._Break1` (3FF1EE:Requiem.esp) "Frenzy (Single)" d5 — single-target break threshold
- [1] `..._Break2` (401989:Requiem.esp) "Frenzy (Dual)" d5 — dual-cast break threshold
- [2] `REQ_Effect_IllusionGM_Frenzy1_Desc_Improved1` (005EB1:MR) m2 — perk descriptor
- [3] `REQ_Effect_IllusionGM_Frenzy1_Desc_Improved2` (007665:MR) m20 — perk descriptor
- **Pattern (mind-affecting):** Break1/Break2 pair (single vs dual-cast level cap) + GM "Improved" descriptor riders.

**ILLUSION CALM — `REQ_Illusion2_Calm_Aimed` (04DEE9:Skyrim.esm, "Calm", 150/0.5), 3:** Break1 + Break2 + `IllusionGM_Calm2_Desc_Improved` (005EAE) m3. Same shape as Fury.

**INVISIBILITY — `REQ_Illusion3_Invisibility_Self` (027EB6:Skyrim.esm, "Invisibility on Self", 250/0.75), 2:**
- [0] `REQ_Effect_Illusion3_Invisibility_Self` (3FCA4D:Requiem.esp) d10
- [1] `REQ_Effect_Illusion3_Invisibility_Self_Improved` (3FF1E9:Requiem.esp) d10 — perk-improved rider

**Summary of the rider doctrine:**
- Nearly every FaF Destruction spell carries `REQ_Effect_DestructionGM_Impact_Stagger` (0153D3:Skyrim.esm, m0.25).
- Element signature riders: Fire→Cremation DoT, Frost→Slow + DeepFreeze, Shock→Electrostatic magicka-burn, Venom→Venom DoT.
- Restoration: Healing→Respite (stamina), Ward→Ward_Shield, Poison→ParalyzingPoison.
- The `...GM_..._Improved` / `_Desc_Improved` / `_Potent` secondaries are the **perk-tier upgrades**; where gated
  they use `HasPerk` conditions (OR-grouped across the relevant Requiem magic-perk tiers), sometimes ANDed with a
  `WornHasKeyword`/state check (mage-armor cuirass split). Many riders are *unconditional* (the perk description
  itself is inert without the perk) — presence of the effect entry ≠ it fires.

---

## Job 3 — Subclass / archetype census (833 `Type=Spell` records, parsed exhaustively)

Per-school totals (scheme-conforming): Destruction 213, Conjuration 166, Restoration 143, Alteration 140,
Illusion 114 = 776 conforming; + 57 non-conforming = 833.

Tier band is **0–5** (tier 0 = free/utility: `Alteration0_Equilibrium`, `Illusion0_VisionOfTheTenthEye`,
some `Destruction0_Arcane`).

**NEW MR subclasses beyond vanilla (🔴), by school:**

- **Destruction** (vanilla Fire/Frost/Shock only): 🔴 **Arcane** (28, T0-5), **Entropic** (27), **AbsorbHealth** (20),
  **Venom** (5), **AbsorbMagicka** (2), **AbsorbStamina** (2). ~37% of the school is new.
- **Conjuration**: 🔴 **Spirit** (29, spirit animals), **Necrotic** (20), **NecromanticHealing** (6), **DaedricHealing** (6),
  **Oblivion** (3), **Sacrifice** (3), **Swarm** (3), **Teleport** (2), **Blink** (2), **SpectralArrow** (1),
  **TeleportVitals** (1). Vanilla kept: Daedra 26, Undead 25, Bound 16, Reanimate 12, Banish/Command/SoulTrap.
- **Restoration**: 🔴 **Poison** (27 — a full Restoration-school poison line), **TurnDaedra** (11),
  **FortifyAttributes** (4), **ResistMagic** (4), **ResistPoison** (4), **Stamina** (4), **Dispel** (3),
  **Abjuration** (2), **CureDisease** (2), **CurePoison** (2), **DispelSoulGem** (2), **Purify** (2),
  **WeaknessMagic** (2), **WeaknessPoison** (2), **Intervention** (1). Vanilla: Sun 29, Healing 18, TurnUndead 16, Ward 8.
- **Alteration** (longest tail, 43 archetypes): 🔴 **Physical** (20 — physical-damage Alteration), **Wind** (13),
  **Telekinetic** (7, weaponized), **Ash** (5), **Enlarge** (5), **Open** (5), **Feather** (4), **FireShield/FrostShield/ShockShield**
  (4 each), **Shrink** (4), **Speed** (4), **TransmuteDisintegration** (3), **Burden/Disintegrate/Jump/ReflectDamage**
  (2 each), **Etherealize** (2), **WeaknessFire/Frost/Shock** (2 each), **TransmuteMuscles** (2), plus singletons
  **ControlWeather, DimensionDoor, Excavate, MarkRecall, Mending, Petrify, Polymorph, SlowTime, TransmuteNightEye,
  Waterwalking, Featherwalking**. Vanilla kept: Armor 14, Paralyze 3, Detect 3, Telekinesis 2, Light 2, TransmuteOre 1,
  Waterbreathing 1, Featherfall 1, Equilibrium 2.
- **Illusion** (most re-imagined): a whole 🔴 **Shadow** family — **ShadowDrain** (5), **ShadowSummon** (5),
  **ShadowStride** (4), **ShadowStep** (3), **ShadowBlade** (2); plus **Pain** (6), **Sleep** (6), **Sanctuary** (6),
  **Silence** (4), **Blind** (4), **Chameleon** (4), **Noise** (4), **Command** (3), **Dampen** (3),
  **Hallucination** (3), **Sound** (3), **Charm** (2), **CharmingAura** (2), **Nightmare** (2), plus singletons
  **Control, Death, Vanish**. Vanilla kept: Invisibility 7, Calm/Fear/Frenzy/Rally 6 each, Muffle 5, NightEye 2, Clairvoyance 1.

**Two parallel naming schemes that DON'T match `REQ_<School><Tier>_` (57 records):**
- `REQ_IllusionGM_*` (37) — Illusion Grand-Master variants, tier is a trailing digit on the archetype
  (`REQ_IllusionGM_Calm4`, `REQ_IllusionGM_Sleep2`, `..._Frenzy3_Rune`).
- `REQ_ConjurationGM_Bound_Shield_*` (6) and `REQ_AlterationGM_*` (Enlarge/Shrink abilities, Dispel_Waterwalking/Jump).
- `REQ_Creature_*` (8) — innate creature spells (Seeker, Watcher, SpiritWyrm, ShadowWolf).
- `REQ_Ench_*_Explosion` (3) — enchantment-driver spells.
- `REQ_Perk_Illusion_ShadowsOfConflict_*` and `REQ_Ability_Staff_<School>` — support/staff records.
- `DLC2Ignite`, `DLC2Freeze` — vanilla Miraak spells MR overrides.

**NPC/cut variants** (still scheme-conforming, but flagged tails): `_NPC` (7, e.g. `REQ_Conjuration5_Daedra_StormThrall_NPC`,
`REQ_Destruction5_AbsorbHealth_Aimed_NPC`), `_Unused` (4 Spirit summons), `_Serana`/`_Isran`/`_Ildari` (unique-NPC spells).
Hand variants `_LeftHand`/`_RightHand` duplicate ~a third of all spells (off-hand/dual-cast mirrors); Requiem
typo `RighHand` exists in one EditorID.

---

## Job 4 — Resistance spells + the REQ_NULL replacement map (CRITICAL)

**All 8 named vanilla resist MGEFs are NULLed** — Requiem.esp overrides each and renames it `REQ_NULL_*`
(MagicSkill=None, gutted). A modded spell/ench/potion pointing at these is a **silent no-op**:

| Element | NULLed vanilla MGEF (dead) |
|---|---|
| Fire | `024314:Skyrim.esm` REQ_NULL_AbResistFire |
| Frost | `024315:Skyrim.esm` REQ_NULL_AbResistFrost |
| Shock | `024318:Skyrim.esm` REQ_NULL_AbResistShock |
| Poison | `0AA024:Skyrim.esm` REQ_NULL_AbResistPoison |
| Disease | `0E40D3:Skyrim.esm` REQ_NULL_AbResistDisease |
| Magic | `053124:Skyrim.esm` REQ_NULL_AbResistMagic |
| Damage/physical | `0CAB90:Skyrim.esm` REQ_NULL_AbResistDamage |
| Waterbreathing | `0AA01C:Skyrim.esm` REQ_NULL_AbWaterbreathing |

Also dead: the robe-enchant resist set `REQ_NULL_EnchRobesResist{Fire,Frost,Magic,Shock}ConstantSelf`
(109635-109638:Skyrim.esm), `REQ_NULL_EnchFortifyDmgResistVampire` (0F81E2), `REQ_NULL_DLC2ResistMagicFFSelf`
(03BD01:Dragonborn.esm), and a batch of `REQ_DEPRECATED_Food_Resist*` (AD38F9–AD3901:Requiem.esp — easy to miss,
no NULL prefix).

### The REPLACEMENT MAP — live MGEF per element per LANE

Lanes: **ABIL** = ability/racial-trait (used by `REQ_Trait_*` / `REQ_AbHide_*` passive abilities) ·
**ENCH** = enchantment (used by ENCH) · **POT** = potion/alchemy (used by ALCH) · **SPELL** = Magic-Redone cast effect.

| Element | ABILITY lane (live MGEF) | ENCHANT lane (live MGEF) | POTION lane (live MGEF) | Representative carrier + magnitude |
|---|---|---|---|---|
| **Fire** | `0008CD:Requiem.esp` REQ_AbHide_FortifyFireResistance (+ `000A15` Drain) | `048C8B:Skyrim.esm` REQ_Ench_ResistFire | `03EAEA:Skyrim.esm` REQ_Alch_ResistFire | Ench: REQ_Ench_AncientHelmetOfTheUnburned m100; Deathbrand Boots m50. Potion: REQ_Potion_ResistFire1/2/3/4 = **m20/30/40/50, dur60** |
| **Frost** | `0008CF:Requiem.esp` REQ_AbHide_FortifyFrostResistance (+ `000A17` Drain; `10DE19` VampireAbResistFrost) | `048F45:Skyrim.esm` REQ_Ench_ResistFrost | `03EAEB:Skyrim.esm` REQ_Alch_ResistFrost | Potion tiers m20/30/40/50 dur60 (same ladder as fire) |
| **Shock** | `0008CE:Requiem.esp` REQ_AbHide_FortifyShockResistance (+ `000A16` Drain) | `049295:Skyrim.esm` REQ_Ench_ResistShock | `03EAEC:Skyrim.esm` REQ_Alch_ResistShock | Potion tiers m20/30/40/50 dur60 |
| **Poison** | `0008CC:Requiem.esp` REQ_AbHide_FortifyPoisonResistance (+ `000A14` Drain) | `0FF15E:Skyrim.esm` REQ_Ench_ResistPoison | `090041:Skyrim.esm` REQ_Alch_ResistPoison | Restoration SPELL: REQ_Effect_Restoration2/3_ResistPoison_Self/Target (032820/032823/005CF6/005CF7) |
| **Disease** | `0008D1:Requiem.esp` REQ_AbHide_FortifyDiseaseResistance (+ `000A19` Drain) | `100E60:Skyrim.esm` REQ_Ench_ResistDisease | *(no REQ_Alch disease potion)* — food: `AE371D:Requiem.esp` REQ_Food_ResistDisease | Disease resist is ability/ench/food only — **no potion, no spell** |
| **Magic** | `0008D0:Requiem.esp` REQ_AbHide_FortifyMagicResistance (+ `000A18` Drain; `0D397E` SailorsRepose boon) | `0B7A35:Skyrim.esm` REQ_Ench_ResistMagic | `039E51:Skyrim.esm` REQ_Alch_ResistMagic | SPELL: REQ_Effect_Restoration2/3_ResistMagic_Self/Target (006200-006203) |
| **Physical / armor** | `REQ_Trait_NaturalArmor_AmorResistance_{Blunt,Slash,Pierce,Ranged}{1-5}` (AD39C6–AD3A4D:Requiem.esp, 20 records — creature natural armor, 5 tiers × 4 damage types) | *(no ENCH — physical mitigation is armor-rating, not an MGEF)* | `AD3A6D` REQ_Ability_HelgenPotion_ResistDamage | Creature natural-armor traits are the physical-resist lane |
| **Waterbreathing** | (vanilla NULLed) — MR spell `REQ_Alteration2_Waterbreathing_Self` (05D175) provides it as an Alteration effect | — | — | Use the live Alteration effect, not AbWaterbreathing |

Notes:
- The `REQ_AbShow_Resist*` MGEFs (000828-00082D:Requiem.esp) are **display-only** UI abilities; the functional
  resist is `REQ_AbHide_Fortify*Resistance` / `Drain*Resistance`. Racial/standing-stone/boon abilities plug the
  Fortify variants into `REQ_Trait_*`/`REQ_Boon_*` SPELs.
- **The alchemy-lane MGEFs are won by `Requiem - Alchemy Redone.esp`, not Requiem.esp.** Base FormIDs are Skyrim.esm
  (`03EAEA` etc.); the potion carriers `REQ_Potion_ResistFire1-4` (039B4A/03EAE9/039B4B/03EAEF) are **m20/30/40/50
  dur60**. If patching a resist *potion*, target `REQ_Alch_Resist<Element>`.
- **Castable resist-granting SPELLS do exist in MR** (Restoration school): `REQ_Restoration2_ResistPoison_Self/Target`,
  `REQ_Restoration3_ResistPoison_*`, `REQ_Restoration2/3_ResistMagic_Self/Target`, plus the "*_Shield" Alteration
  spells (`FireShield`/`FrostShield`/`ShockShield`) and the elemental-cloak resist riders
  (`REQ_Effect_DestructionGM_<Element>_Cloak_Resistance`). There is **no** castable Disease-resist spell.
- No generic `WeaknessToFire/Frost/Shock` MGEF is Requiem-won here; the only resistance-*lowering* Requiem ench
  is `REQ_Ench_SkullOfCorruption_ReduceResistances` (0A0D97). MR does ship Alteration Weakness spells
  (`REQ_Alteration2/3_WeaknessFire/Frost/Shock_Target`) as their own MGEFs.

---

## Job 5 — Spell tomes cross-check

Tomes follow `REQ_Tome_<School><Tier>_<Archetype>_<Delivery>`, Weight = 1 (uniform), Value tracks tier band but
varies within a tier by spell utility (summons/utility cost more than damage at the same tier):

| Tier (mastery) | Value range | Examples |
|---|---|---|
| 1 Novice | 100 (damage) — 200/300 (utility/summon) | Flames/Frostbite/Sparks/Candlelight/Healing = 100; Oakflesh/Bound Sword/Spirit Wolf/Fury/Courage = 200; Reanimate Zombie = 300 |
| 2 Apprentice | 300–500 | Soul Trap/Ward/Magelight/Waterbreathing = 300; Stoneflesh/Firebolt/Ice Spike/Lightning Bolt/Bound Battleaxe = 400; Reanimate Corpse = 500 |
| 3 Adept | 500–700 | Telekinesis/DetectLife/DetectDead = 500; Ironflesh/Banish Daedra = 600; Flame Atronach/Bound Bow/Reanimate Revenant = 700 |
| 4 Expert | 800–900 | Paralyze/Ebonyflesh/Command Daedra/Expel Daedra = 800; Frost & Storm Atronach/Reanimate Dread Zombie = 900 |
| 5 Master | 2000 | Dead Thrall, Flame/Frost/Storm Thrall |

- **Ladder holds** with the expected shape (Novice ~100, Apprentice ~300-400, Adept ~600, Expert ~800, Master 2000).
- **Deviations from a strict tier→value:** T1 non-damage spells are 200 (not 100) and Reanimate is 300; Adept
  summons hit 700 (not a flat 600); Expert summons hit 900 (not flat 800). Value is set per-spell within the
  tier band, not a single value per tier. Weight uniformly 1.
- MR **overrides the vanilla BOOK records** (base FormIDs 09CD51.. / 0A26xx:Skyrim.esm) — the tomes carry
  `Teaches = [BookSpell]` pointing at the matching `REQ_<School><Tier>_..._` spell. 584 BOOK records touched.

**DOCTRINE for a modded spell tome:** set Weight 1, Teaches → your patched spell, and Value to the tier band
of the spell's Requiem comparable (match a same-tier same-role MR tome, not a flat tier number).
