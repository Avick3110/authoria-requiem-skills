# MR Magic Effects (MGEF) + Object Effects (ENCH) — empirical mining

Source: live Requiem load order via houseCARL (read-only). Plugins: `Requiem - Magic Redone.esp`
(MR), `Requiem.esp`, base `Skyrim.esm`. Every row carries FormID + EditorID + defining plugin.
All FormIDs are `XXXXXX:Plugin.esp`. Winners noted where an override plugin (Visual Tweaks / Vibrant
Weapon Infusions / Praedy's Staves patch) supersedes MR's own version.

Scale: MR **defines 996 MGEF** and **574 ENCH (ObjectEffect)**; the tiered vanilla-lineage weapon &
apparel enchantments are defined at **Skyrim.esm FormIDs but overridden by MR** (renamed `REQ_Ench_*`).

## Naming grammar (the tier is IN the EditorID)

`REQ_Effect_<School><Tier>_<Type>_<Delivery>[_<Sub>]`
- School: Destruction / Alteration / Restoration / Conjuration / Illusion
- Tier: `1..5` or `GM` (Grandmaster). Tier `N` in the name == `MinimumSkillLevel` gate (see Job 1c).
- Delivery: Touch / Aimed / AimedArea / AimedDoT / AimedHazard / ConcAimed / Self / SelfExp /
  Target / TargetLocation(Rune) / Cloak(Self/Target) / RitualSelfExp / EnchSelf(weapon-infusion).
- `REQ_Effect_Ench_*` = the effect version used by an ENCH (weapon/staff). `REQ_Ench_*` = the ENCH.

---

# JOB 1 — MGEF keyword + flags + values taxonomy

## (a) Keyword vocabulary

### Element / school-damage keywords (Skyrim.esm — the visible-damage-type tag)
| Keyword | FormID | Carried by |
|---|---|---|
| MagicDamageFire | 01CEAD:Skyrim.esm | all Fire damage/DoT/rune/cloak effects |
| MagicDamageFrost | 01CEAE:Skyrim.esm | all Frost damage effects |
| MagicDamageShock | 01CEAF:Skyrim.esm | all Shock damage effects |
| MagicRestoreHealth | 01CEB0:Skyrim.esm | Healing / DaedricHealing effects |
| MagicInvisibility | 01EA6F:Skyrim.esm | Invisibility |
| MagicParalysis | 01EA70:Skyrim.esm | Paralyze (spell + weapon-ench) |
| MagicInfluenceFear | 0424E0:Skyrim.esm | Fear |
| MagicInfluence | 078098:Skyrim.esm | Fear (+ other mind effects) |
| MagicCloak | 0B62E4:Skyrim.esm | every Cloak archetype effect |
| MagicFlameCloak | 002EDA:Update.esm | Fire cloak (Flame Aspect) |
| MagicRune | 109D79:Skyrim.esm | Rune effects (TargetLocation) |
| MagicEnchFortifyOneHanded | 074F6B:Skyrim.esm | weapon fortify-penetration ench effect |

### REQ_* scaling markers (Requiem.esp — tell the Reqtificator NOT to scale a dimension)
| Keyword | FormID | Meaning |
|---|---|---|
| REQ_NoDurationScaling | 412EDF:Requiem.esp | duration is fixed, don't perk/skill-scale it (damage/DoT/rune effects) |
| REQ_NoMagnitudeScaling | 3FCA4C:Requiem.esp | magnitude fixed (Invisibility, Paralyze, ShadowSummon, Cloaks) |
| REQ_SpellConcentration | 2FFEAD:Requiem.esp | marks a Concentration-cast effect |
| REQ_Absorb | ADDDF7:Requiem.esp | Absorb archetype marker |
| REQ_NoLifeDrainAllowed (as MGEF-keyword condition target) | 2EA062:Requiem.esp | gates life-drain effects off |

### Nox_KW_* — MR's specialization / perk-gate / mechanic keywords (defined in MR)
These are the "which perk tree / classifier" markers and the shield-dispel Association keys.
| Keyword | FormID | Role |
|---|---|---|
| Nox_KW_Touch | 006081:Requiem - Magic Redone.esp | melee-range spell marker |
| Nox_KW_Alteration_Shield | 000813:Requiem - Magic Redone.esp | mage-armor family (also the PeakValueMod Association) |
| Nox_KW_Alteration_Shield_Improved | 00081F:Requiem - Magic Redone.esp | GM improved mage-armor (DispelWithKeywords key) |
| Nox_KW_Alteration_Weakness | 005B3F:Requiem - Magic Redone.esp | Weakness-to-element debuffs |
| Nox_KW_Alteration_Telekinetic | 005C64:Requiem - Magic Redone.esp | Telekinesis line |
| Nox_KW_Restoration_FortifyAttributes | 005D05:Requiem - Magic Redone.esp | Fortify H/M/S family |
| Nox_KW_Restoration_ResistMagic | 006204:Requiem - Magic Redone.esp | Resist Magic |
| Nox_KW_Destruction_Perk_Pyromancy | 006015:Requiem - Magic Redone.esp | Pyromancy perk-gate (fire cloak) |
| Nox_KW_Destruction_Perk_BloodMagic | 006012:Requiem - Magic Redone.esp | Blood Magic perk-gate (absorb cloak) |
| Nox_KW_Conjuration_Perk_Daedric | 005F57:Requiem - Magic Redone.esp | Daedric conjuration perk-gate |
| Nox_KW_Illusion_ShadowSummon | 005F04:Requiem - Magic Redone.esp | Shadow-summon line |

### Vanilla AI/UI hint keywords seen: WISpellColorful (0A9B1E), WISpellDangerous (0A9B1F).

Pattern: a damage effect carries **[element keyword] + [REQ_NoDurationScaling]** and nothing else.
A buff/shield/resist effect carries **one Nox_KW_* family keyword** which is ALSO its
`Archetype.Association` (PeakValueModifier association key). Perk-gated effects add a
`Nox_KW_<School>_Perk_<Name>` keyword. Cloaks carry MagicCloak (+ element-specific cloak KW).

## (b) Flags per archetype

| Archetype / role | Typical Flags |
|---|---|
| Direct damage (Fire/Frost/Shock aimed/touch) | Hostile, Detrimental, FXPersist, **PowerAffectsMagnitude**, NoDeathDispel (+NoArea on single-target) |
| DoT damage | same + (TaperWeight=1,Curve0 for pure DoT) |
| Absorb (health) | Hostile, Detrimental, **PowerAffectsMagnitude** (Concentration adds nothing extra) |
| Heal self | **NoDuration**, NoArea, FXPersist, PowerAffectsMagnitude (NOT Hostile/Detrimental) |
| Shield / mage-armor (PeakValueMod) | **Recover**, NoArea, FXPersist, **PowerAffectsDuration** |
| GM "improved" shield | Recover, **DispelWithKeywords**, NoArea, FXPersist, **HideInUI**, PowerAffectsDuration |
| Fortify attribute (self buff) | Recover, PowerAffectsDuration (+HideInUI on the 0-cost hidden sub-effects) |
| Resist (magic/poison) | Recover, PowerAffectsDuration |
| Weakness debuff | Hostile, Recover, Detrimental, NoArea, FXPersist, PowerAffectsDuration |
| Fear (PeakValueMod on Fame AV) | Hostile, Recover, NoHitEvent, DispelWithKeywords, FXPersist, PowerAffectsDuration |
| Invisibility | Recover, **NoMagnitude**, FXPersist, PowerAffectsDuration |
| Paralyze | Hostile, Recover, **NoMagnitude**, FXPersist, PowerAffectsDuration |
| Summon (SummonCreature) | NoMagnitude, NoArea, FXPersist, PowerAffectsDuration, NoHitEffect |
| Cloak | NoArea, FXPersist, PowerAffectsDuration (Fire adds NoRecast) |
| Weapon-ench effect (HideInUI worker) | Hostile, FXPersist, HideInUI, PowerAffectsMagnitude (or NoDeathDispel for bound) |
| Reanimate debuff (constant) | Recover, Detrimental, NoDuration, NoArea, PowerAffectsMagnitude |

Rule of thumb: **damage → PowerAffectsMagnitude; timed buff/debuff → PowerAffectsDuration**;
`NoMagnitude` for binary states (invis/paralyze/summon); `NoDuration` for instantaneous heals &
constant effects; `Hostile+Detrimental` only on things that harm; `NoDeathDispel` on damage so the
killing tick still lands; `FXPersist` nearly universal.

## (c) Which balance VALUES matter on an MGEF

- **MinimumSkillLevel = the tier gate, and MR uses it as the primary tier marker.** Confirmed:
  tier1→0, tier2→25, tier3→50, tier4→75, tier5→100. Examples: Destruction1_Fire_Touch=0,
  Destruction3_Fire_AimedArea=50, Destruction4_Frost_AimedDoT=75, Destruction5_Fire_CloakSelf=100,
  Restoration2_ResistMagic=25. **GM effects usually set MinimumSkillLevel=0** and gate via
  keyword/perk/HideInUI instead.
- **BaseCost is meaningful but is NOT the spell's magicka cost** (that lives on the SPEL). On MR
  effects BaseCost is the vanilla per-magnitude cost weight, small for damage (Fire_Touch=1.2,
  Fire_AimedArea=2.3, Frost_DoT=3.55, Shock_AimedExp=2.7) and large for binary/utility effects
  (Invisibility=25, Paralyze=450, ShadowSummon_Wolf=26.7, Telekinetic=170). Hidden worker
  effects (bound weapons, some fortify sub-effects) set BaseCost=0. Treat it as a relative
  autocalc weight per archetype, not a number to invent — copy the comparable.
- **ResistValue** = the ActorValue the effect is resisted by: ResistFire/Frost/Shock on elemental
  damage, ResistMagic on absorb/shock-ish, None on buffs/heals/utility.
- **Taper (Weight/Curve/Duration)**: only meaningful on damage/DoT/cloak effects.
  Instantaneous-hit fire: TaperWeight 0.3 / Curve 2 / Dur 1. Pure DoT: Weight 1 / Curve 0 / Dur 0.
  Shock (fast): Weight 0.01 / Curve 0 / Dur 0.1. Buffs/heals/summons: all 0. Copy from comparable.
- **Archetype**: ValueModifier (direct dmg/heal), DualValueModifier (dmg to two AVs — Frost→Health
  +Stamina via SecondActorValue=Stamina; Shock→Health+Magicka), Absorb, PeakValueModifier
  (shields/fortify/resist/fear — needs Association==the family keyword), Cloak (Association→the
  hazard/damage MGEF), SummonCreature (Association→the ACHR/NPC), Paralysis, Invisibility, Script
  (bound weapons, penetration), Stagger.
- **SecondActorValue** only on DualValueModifier (Frost=Stamina, Shock=Magicka, Reanimate=MagickaRateMult).

---

# JOB 2 — MGEF Conditions

Conditions live on the MGEF's own `Conditions` list. Structure per row: `Data.Function`,
`Data.RunOnType` (Subject vs Target), `CompareOperator`, `ComparisonValue`, and `Flags` (OR chains
adjacent rows; default 0 = AND). Functions seen: **HasKeyword, IsUndead, HasPerk,
HasMagicEffectKeyword, IsCommandedActor, GetActorValue**.

### Pattern A — creature-type gate (heal/turn only affects a race-type)
`REQ_Effect_Conjuration3_DaedricHealing_Target` (005DA1): one row —
`HasKeyword(ActorTypeDaedra 013797:Skyrim.esm) on Subject == 1`. Daedric healing only heals Daedra.
`REQ_Effect_Restoration2_TurnDaedra_Aimed` (0073A7): same, HasKeyword ActorTypeDaedra == 1.

### Pattern B — OR-chained undead test (Sun / Turn Undead)
`REQ_Effect_Restoration1_Sun_Touch` (005C75), 3 rows all **Flags=OR**:
```
[0] HasKeyword(ActorTypeUndead 013796:Skyrim.esm) Subject == 1   OR
[1] HasKeyword(ActorTypeGhost  0D205E:Skyrim.esm) Subject == 1   OR
[2] IsUndead()                                    Subject == 1
```
Sun damage fires only vs undead/ghost. Same triad on `REQ_Effect_Restoration1_Sun_ConcAimed`.
`REQ_Effect_Ench_Weapon_TurnUndead_Banish` (00761D) adds an `IsCommandedActor == 1` row up front.

### Pattern C — perk-gated + exclusion (the richest example)
`REQ_Effect_Destruction5_AbsorbHealth_Aimed_ConsumeLife` (005DA9), 3 rows ANDed:
```
[0] HasPerk(REQ_Destruction_BloodMagic_050_ConsumeLife 005C68:MR) RunOn=Target == 1
[1] HasKeyword(ActorTypeDwarven 01397A:Skyrim.esm) Subject == 0     (NOT a dwarven automaton)
[2] HasMagicEffectKeyword(REQ_NoLifeDrainAllowed 2EA062:Requiem.esp) Subject == 0  (target not immune)
```
Note the "== 0" idiom = "does NOT have". `HasPerk` uses the CASTER via RunOn=Target here (Requiem's
convention: Target is the spell's caster context for perk checks), keyword tests use Subject.

### Pattern D — GetActorValue threshold (knockdown resistance)
`REQ_Effect_Destruction4_Fire_TargetLocExp_Knockdown_Knockdown` (005FF8), 3 rows ANDed:
```
[0] HasKeyword(ImmuneStrongUnrelentingForce 0172AC:Skyrim.esm) Subject == 0
[1] GetActorValue(ResistMagic) Subject  LessThan 20
[2] GetActorValue(ResistFire)  Subject  LessThan 40
```
Knockdown only lands on non-immune, low-resist targets.

**Doctrine for a patched MGEF:** add Conditions when the effect should only fire against a
sub-population (undead → OR triad; daedra → single HasKeyword; automaton exclusion → `== 0`), when
it's perk-gated (HasPerk on RunOn=Target), or when a rider (knockdown/paralyze) needs a
resistance/immunity check (GetActorValue / ImmuneX keyword `== 0`). Copy the exact rows from the
nearest Requiem comparable; do not hand-author function/param combos.

---

# JOB 3 — Weapon-enchant cost model + gear-value coupling

## (a) The tiered enchant ladders (defined at Skyrim.esm FormIDs, overridden by MR)

### Weapon Fire damage — `REQ_Ench_Weapon_Fire0..6` (EnchantType=Enchantment, FireAndForget/Touch)
| Tier | FormID | EnchantmentCost | EnchantmentAmount | Effect Magnitude |
|---|---|---|---|---|
| Fire1 | 049BB7:Skyrim.esm | 10 | 10 | 10 |
| Fire2 | 045C2A:Skyrim.esm | 20 | 20 | 20 |
| Fire3 | 045C2C:Skyrim.esm | 25 | 25 | 25 |
| Fire4 | 045C2D:Skyrim.esm | 35 | 35 | 35 |
| Fire5 | 045C30:Skyrim.esm | 40 | 40 | 40 |
| Fire6 | 045C35:Skyrim.esm | 50 | 50 | 50 |

All share base effect `REQ_Effect_Ench_Weapon_Fire` (04605A:Skyrim.esm). **On the ENCH,
EnchantmentCost == EnchantmentAmount == effect Magnitude** (1:1) — this is the per-hit charge draw /
enchant-menu weight, NOT the weapon's charge pool (that's on the WEAP, see (b)).

### Apparel Fortify One-Handed — `REQ_Ench_Armor_FortifyOneHanded01..06` (ConstantEffect/Self, NoAutoCalc)
| Tier | FormID | EnchantmentCost = Amount | Magnitude (% dmg) |
|---|---|---|---|
| 01 | 07A114:Skyrim.esm | 100 | 5 |
| 02 | 0AD471:Skyrim.esm | 200 | 10 |
| 03 | 0AD472:Skyrim.esm | 300 | 15 |
| 04 | 0BE00F:Skyrim.esm | 400 | 20 |
| 05 | 0BE010:Skyrim.esm | 500 | 25 |
| 06 | 0BE011:Skyrim.esm | 600 | 30 |

Base effect `REQ_Ench_FortifyOneHanded` (07A0FF:Skyrim.esm). **Cost = magnitude × 20; tier ×100.**

### Apparel Resist Fire — `REQ_Ench_Armor_ResistFire01..06` (ConstantEffect/Self, NoAutoCalc)
| Tier | FormID | Cost = Amount | Magnitude (% resist) |
|---|---|---|---|
| 01 | 04950B:Skyrim.esm | 100 | 10 |
| 02 | 0AD483:Skyrim.esm | 200 | 20 |
| 03 | 0AD484:Skyrim.esm | 300 | 25 |
| 04 | 0BE028:Skyrim.esm | 400 | 35 |
| 05 | 0BE029:Skyrim.esm | 500 | 40 |
| 06 | 0BE02A:Skyrim.esm | 600 | 50 |

Base effect `REQ_Ench_ResistFire` (048C8B:Skyrim.esm). Cost = tier ×100; magnitude ladder is
hand-tuned (10/20/25/35/40/50), NOT strictly linear — copy the tier, don't compute.

## (b) THE GEAR-VALUE COUPLING (verifies/corrects the doctrine)

The doctrine "cost is tied to the VALUE of the enchantment on the gear" is **only true in the vanilla
autocalc sense and is NOT how Requiem builds its pre-enchanted loot.** Requiem uses `NoAutoCalc` on
every ENCH and sets each enchanted item's gold Value MANUALLY to the **base unenchanted item's
value** — the enchantment adds ZERO gold value. What scales with tier instead is the effect
**Magnitude**, the ENCH **EnchantmentCost** (enchant-menu weight), and the WEAP's own
**EnchantmentAmount = the charge pool**.

### Weapon tier run (Iron Sword, Fire)
| Item | FormID | BasicStats.Value | Damage | WEAP EnchantmentAmount (pool) | ObjectEffect (mag) |
|---|---|---|---|---|---|
| REQ_Weapon_Iron_Sword (base) | 012EB7:Skyrim.esm | **25** | 7 | — | none |
| Iron Sword of Embers (Fire1) | 049BB8:Skyrim.esm | **25** | 7 | **500** | Fire1 (mag 10) |
| Iron Sword of Scorching (Fire3) | 046017:Skyrim.esm | **25** | 7 | **1500** | Fire3 (mag 25) |
| REQ_Weapon_Iron_Dagger (base) | 01397E:Skyrim.esm | **10** | 4 | — | none |
| Iron Dagger of Scorching (Fire3) | 0896B5:Skyrim.esm | **10** | 4 | **1500** | Fire3 (mag 25) |

→ Enchanted Value **equals base Value** (25=25, 10=10); it does NOT move with enchant tier.
→ The **charge pool lives on the WEAP** (`EnchantmentAmount`), and scales with tier: Fire1=500,
  Fire3=1500 (≈ tier ×500). The ENCH's own `EnchantmentAmount` (10/25) is the per-cast draw.

### Apparel tier run (Iron Gauntlets, Fortify One-Handed)
| Item | FormID | Value | ArmorRating | ObjectEffect |
|---|---|---|---|---|
| REQ_Heavy_Iron_Hands (base) | 012E46:Skyrim.esm | **37** | 75 | none |
| Iron Gauntlets of Minor Wielding (t1) | 07A12E:Skyrim.esm | **37** | 75 | FortifyOneHanded01 (mag 5) |
| Iron Gauntlets of Wielding (t2) | 0AD57D:Skyrim.esm | **37** | 75 | FortifyOneHanded02 (mag 10) |
| Iron Gauntlets of Major Wielding (t3) | 0AD57E:Skyrim.esm | **37** | 75 | FortifyOneHanded03 (mag 15) |

→ Enchanted apparel Value **equals base Value (37) across all tiers**; apparel is ConstantEffect so
  there is NO charge pool (WEAP EnchantmentAmount absent). Only Magnitude scales.

**Naming of the enchanted items** encodes base + effect + tier:
`REQ_Ench_Weapon_Iron_Sword_Fire3`, `REQ_Ench_Heavy_Iron_Hands_OneHanded3`. Requiem also has
per-skill apparel families on the same body slot (OneHanded / TwoHanded / Marksman / Smithing /
Alchemy / Destruction …), each with tiers 1–6.

## (c) Apparel enchant model — verified
CastType=**ConstantEffect**, TargetType=**Self**, Flags=**NoAutoCalc**, no ChargeTime, no charge pool.
EnchantmentCost=EnchantmentAmount=tier×100. Magnitude ladder per family (FortifyOneHanded
5/10/15/20/25/30; ResistFire 10/20/25/35/40/50). ENCH keyword tag on effect = the vanilla
MagicEnchFortifyX / a REQ_Ench_ResistFire family effect; MGEF archetype = PeakValueModifier for
fortify/resist.

## Other ENCH archetypes in MR (for completeness)
- `REQ_Ench_Spell_<School><Tier>_<Elem>_Ench` — weapon spell-infusion (Vibrant Weapon Infusions).
  EnchantType=Enchantment, Touch, **Cost=0/Amount=0, NoAutoCalc** (pool on the WEAP). Magnitude
  ladder e.g. Fire tier2=15, tier4=30 (base effect REQ_Effect_DestructionGM_Fire_Ench_Damage 005BBA).
- `REQ_Ench_Weapon_ShadowSummon5` (00598D): Enchantment/Touch, Cost0/Amount0, three effects
  (DamageHealth/Magicka/Stamina) each mag 15.
- `REQ_Ench_Bound_*` (armor/shield): ConstantEffect/Self, Cost0/Amount0, effects carry Conditions.
- Artifact/unique weapon enchants live in Requiem.esp: `REQ_Ench_Weapon_FireBurst/FrostBurst/
  ShockBurst/ArcanePower/Annihilation/Soulreaping/…` (03D4E8-range).

---

# JOB 4 — Staff + scroll models

## Staves
Staff WEAP `REQ_Staff_Destruction1_Fire_Touch` (006912; winner = Praedy's Staves AIO patch):
BasicStats.Value=**100**, Damage=1, **EnchantmentAmount=500** (charge pool on the WEAP),
ObjectEffect → `REQ_Ench_Staff_Destruction1_Fire_Touch` (006757). Base staff value is a fixed 100.

Staff ENCH (`REQ_Ench_Staff_*`, EnchantType=**StaffEnchantment**): casts the **school spell effect**
at an amplified magnitude, ChargeTime and per-cast EnchantmentCost scale with tier:
| Staff ENCH | FormID | Tier | ChargeTime | EnchantmentCost = Amount | casts the effect | staff Magnitude |
|---|---|---|---|---|---|---|
| Staff_Destruction1_Fire_Touch | 006757 | 1 | 0.25 | 25 | REQ_Effect_Destruction1_Fire_Touch | 20 (+ stagger riders) |
| Staff_Destruction3_Fire_AimedArea | 006779 | 3 | 0.75 | 160 | REQ_Effect_Destruction3_Fire_AimedArea | 80 |
| Staff_Destruction5_Fire_AimedExp | 0067B1 | 5 | 1.25 | 600 | REQ_Effect_Destruction5_Fire_AimedExp | 240 |

Conventions: staff casts the same MGEF the school SPELL uses (BaseEffect points at the
`REQ_Effect_<School><Tier>_…`), at a **higher Magnitude than the hand spell** (staff = force
multiplier); EnchantmentCost = per-cast charge draw from the WEAP's pool (tier1 staff: 500/25 = 20
casts); ChargeTime rises with tier (0.25→1.25). A tier-1 staff carries multiple stagger/impact rider
effects; higher tiers are single big-magnitude blasts.

## Scrolls (type=Scroll, `REQ_Scroll_<School><Tier>_<Name>_<Delivery>`)
MR defines ~592 scrolls. One-shot, no charge. Constant Weight=0.5, one keyword **VendorItemScroll
(0A0E57:Skyrim.esm)**, Value scales with tier/power:
| Scroll | FormID | Tier | Value | Weight |
|---|---|---|---|---|
| Scroll of Knock (Alteration1 Physical Touch) | 0062D4 | 1 | 20 | 0.5 |
| Scroll of Corrode (Alteration2 Disintegrate) | 0062E8 | 2 | 75 | 0.5 |
| Scroll of Fire Shell on Self (Alteration2 FireShield) | 0062DA | 2 | 100 | 0.5 |

Scrolls carry the full effect set (often 3–5 effects incl. riders), Value roughly tier-banded
(t1≈20, t2≈75–100). No charge/cost fields (single use).
