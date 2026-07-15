# Requiem – Alchemy Redone: POTION + POISON system corpus

Live-mined via houseCARL against the ARR MO2 instance. READ-ONLY. All tokens are verbatim
`XXXXXX:Plugin.esp`. "Live winner" = the load-order-resolved winner houseCARL returns (the authority
to derive from), which in this instance is **not always AR** — see the doctrine section.

Source plugin scope: **Requiem - Alchemy Redone.esp** (hereafter **AR**).

---

## 1. Accounting (enumerated vs read)

**ALCH — 287 records AR touches** (`cross_plugin_query type=ALCH plugins=[AR]`, total=287, capped=false).
Live-winner distribution (`group_by=winner`):

| Winning plugin | ALCH count | What it wins |
|---|---:|---|
| Requiem - Alchemy Redone.esp | 261 | the bulk of potions/poisons/oils |
| Requiem - Magic Redone.esp | 24 | the 6 magic-school Fortify potions × 4 tiers (Alteration, Conjuration, Destruction, Illusion, Restoration, Enchanting) |
| Authoria - Patch - Requiem - SpookyPOP.esp | 1 | one potion (env-specific patch) |
| Authoria - Patch - Requiem - Wounds.esp | 1 | REQ_Potion_CureDisease (adds a 2nd "Cure Infection" effect) |

**MGEF — 80 records AR touches.** Live-winner distribution (`group_by=winner`):

| Winning plugin | MGEF count |
|---|---:|
| Requiem - Alchemy Redone.esp | 57 |
| BOOBIES_potions - Requiem Patch.esp | 17 |
| Requiem - Magic Redone.esp | 6 |

AR **defines** 19 ALCH of its own (`defined_in=true`, total=19) and 6 MGEF of its own (the 4
`REQ_Effect_Oil_*` + `REQ_Alch_Silence` + `REQ_Alch_DamageStrength`).

**Records read in full detail this session:** ~110 ALCH (every full ladder listed below) + 19 MGEF
(sample + one full conflict_tree). Families read at every tier are marked ✔; families read at a
subset of tiers are marked ~ and the un-read tiers are explicitly flagged as inferred, never asserted.

---

## 2. Naming + shape conventions

- **Potion EditorID:** `REQ_Potion_<Effect><tier>` where tier ∈ {1,2,3,4,(5),Complete}.
- **Poison EditorID:** `REQ_Poison_<Effect><tier>`.
- **Oil EditorID:** `REQ_Oil_<Element>` (no tiers).
- **Display Name tier suffix** (parenthetical), fixed across the whole pack:
  tier1 `(Diluted)` · tier2 `(Faint)` · tier3 `(Fair)` · tier4 `(Good)` · tier5 `(Remarkable)` ·
  Complete `(Surpassing)`. Potions read `Potion of <Effect> (…)`; poisons `Poison of <Effect> (…)`.
- **Weight:** `0.5` on every ingestible read (potions, poisons, oils). Invariant.
- **Flags:** potions = `NoAutoCalc`; poisons & oils = `NoAutoCalc, Poison`. Invariant.
  → **NoAutoCalc is universal**, so the ALCH `Value` (gold) is HAND-SET, not derived from MGEF BaseCost.
- **Keywords (ALCH):** exactly ONE — `VendorItemPotion` (`08CDEC:Skyrim.esm`) on potions,
  `VendorItemPoison` (`08CDED:Skyrim.esm`) on poisons AND oils. No Requiem-specific keyword sits on
  the ALCH record; the Requiem alchemy plumbing (duration keyword) lives on the MGEF (§6).
- **Effects:** single-effect for all ladder members except the multi-effect specials (WellBeing =
  Restore H+M+S; White Phial = Fortify OneHanded+TwoHanded+Marksman; CureDisease live = CureDisease +
  Wounds CureInfection). Effect duration/magnitude is carried on the **potion's** `Effects[0].Data`,
  not the MGEF.
- **No addiction fields.** No `REQ_` potion/poison in this set uses Addiction / AddictionChance
  (no skooma-class item is part of the AR ladder system).

---

## 3. POTION ladders (Value | mag | dur | MGEF)

### 3.1 Restore Health / Magicka / Stamina ✔ (all read)
Common shape: Value **20/40/60/80/100** (tiers 1–5), Complete **500**; dur 20; Complete mag 9999 dur 0
via a distinct `*Complete` MGEF. Weight 0.5, NoAutoCalc.

| tier | mag | RestoreHealth MGEF | RestoreMagicka MGEF | RestoreStamina MGEF |
|---|---:|---|---|---|
| 1 | 3 | `03EB15:Skyrim.esm` REQ_Alch_RestoreHealth | `03EB17` REQ_Alch_RestoreMagicka | `03EB16` REQ_Alch_RestoreStamina |
| 2 | 5 | ″ | ″ | ″ |
| 3 | 8 | ″ | ″ | ″ |
| 4 | 10 | ″ | ″ | ″ |
| 5 | 12 | ″ | ″ | ″ |
| Complete | 9999 (dur 0) | `0FFA03` RestoreHealthComplete | `0FFA04` RestoreMagickaComplete | `0FFA05` RestoreStaminaComplete |

FormIDs: Health 1-5 `03EADD/03EADE/03EADF/03EAE3/039BE4`, Complete `039BE5`. Magicka 1-5
`03EAE0/03EAE1/03EAE2/03EAE4/039BE6`, Complete `039BE7`. Stamina 1-5
`03EAE5/039BE8/03EAE7/03EAE8/03EAE6`, Complete `039CF3`.

### 3.2 Well-Being (combined Restore-all) ✔ (t1 + Complete read; t2–5 inferred)
Multi-effect: Restore Health + Restore Magicka + Restore Stamina in one potion, mag mirrors the
Restore ladder (t1 = 3/3/3 dur 20; Complete = 9999/9999/9999 dur 0). Value t1 **60**, Complete **1500**.
FormIDs `01AADD..01AAE1` (Dragonborn.esm) + Complete `01AAE2:Dragonborn.esm`.
Uses the shared Restore MGEFs (`03EB15/16/17`, Complete `0FFA03/04/05`).

### 3.3 Fortify Attribute (5-tier) ✔ Health/Magicka/CarryWeight read; Stamina inferred
| family | Value ladder | mag ladder | dur | MGEF |
|---|---|---|---:|---|
| FortifyHealth | 40/80/120/160/200 | 20/40/60/80/100 | 300 | `03EAF3` REQ_Alch_FortifyHealth |
| FortifyMagicka | 40/80/120/160/200 | 20/40/60/80/100 | 300 | `03EAF8` REQ_Alch_FortifyMagicka |
| FortifyStamina ~ | 40/80/120/160/200 (inferred) | 20/40/60/80/100 (inferred) | 300 | (REQ_Alch_FortifyStamina — not read) |
| FortifyCarryWeight | 20/40/60/80/100 | 10/20/30/40/50 | **600** | `03EB01` REQ_Alch_FortifyCarryWeight |

### 3.4 Fortify Regeneration (5-tier) ✔ HealthRegen read; Magicka/Stamina regen inferred
| family | Value | mag (%) | dur | MGEF |
|---|---|---|---:|---|
| FortifyHealthRegeneration | 30/60/90/120/150 | 40/80/120/160/200 | 300 | `03EB06` REQ_Alch_FortifyHealthRegeneration |
| FortifyMagickaRegeneration ~ | 30/60/90/120/150 (inf.) | 40/80/120/160/200 (inf.) | 300 | (not read) |
| FortifyStaminaRegeneration ~ | 30/60/90/120/150 (inf.) | 40/80/120/160/200 (inf.) | 300 | (not read) |

### 3.5 Fortify Skill (4-tier) — TWO value-classes, per-skill magnitude
dur **60** across all. Two distinct Value ladders:
- **Class A (base 80, +40/tier → 80/120/160/200):** weapon skills + all magic schools.
- **Class B (base 60, +30/tier → 60/90/120/150):** the "utility" skills.

Magnitude ladders are **NOT uniform** — each skill has its own base+step; derive per-skill from the
matching Requiem comparable, never assume. Measured:

| family | class | Value | mag ladder | dur | MGEF | live winner |
|---|---|---|---|---:|---|---|
| FortifyOneHanded ✔ | A | 80/120/160/200 | 10/15/20/25 | 60 | `03EB19` | AR |
| FortifyMarksman ~ (t1) | A | 80/… | 10/… | 60 | `03EB1B` | AR |
| FortifySpeed ✔ | A | 80/120/160/200 | 6/9/12/15 | 60 | `03EB1F` | AR |
| FortifyDestruction ✔ | A | 80/120/160/200 | 6/9/12/15 | 60 | `03EB26` | **Magic Redone** |
| FortifyAlteration ~ (t1) | A | 80/… | 6/… | 60 | `03EB24` | **Magic Redone** |
| FortifyEnchanting ✔ | B | 60/90/120/150 | 4/6/8/10 | 60 | `03EB29` | **Magic Redone** |
| FortifySpeech ✔ | B | 60/90/120/150 | 10/15/20/25 | 60 | `0D6947` | AR |
| FortifyUnarmed ✔ (AR-own) | B | 60/90/120/150 | 6/9/12/15 | 60 | `020E2B:Requiem.esp` | AR |
| FortifyLockpicking ~ (t1) | B | 60/… | 1/… | 60 | `03EB21` | AR |
| FortifyArmorRating ~ (t1) | B | 60/… | **120**/… | 60 | `03EB1E` | AR |
| FortifyBlock ~ (t1) | B | 60/… | **16**/… | 60 | `03EB1C` | AR |

Other skill families present at 4 tiers (not individually read; class by pattern): TwoHanded (A),
Smithing, Sneak, Barter, Pickpocket, Illusion/Conjuration/Restoration (magic schools → A, Magic Redone),
Enchanting done above. FortifyArmorRating mag 120 and FortifyBlock mag 16 confirm the magnitude scale
is effect-specific — a strong reason to read the exact Requiem comparable tier rather than reuse a
sibling family's numbers.

### 3.6 Resist Elemental / Magic (4-tier) ✔ Fire & Magic read; Frost/Shock t1 read
dur 60. Fire/Frost/Shock share the value+mag ladder; Magic differs.

| family | Value | mag (%) | MGEF |
|---|---|---|---|
| ResistFire ✔ | 80/120/160/200 | 20/30/40/50 | `03EAEA` REQ_Alch_ResistFire |
| ResistFrost ~ (t1) | 80/… | 20/… | `03EAEB` REQ_Alch_ResistFrost |
| ResistShock ~ (t1) | 80/… | 20/… | `03EAEC` REQ_Alch_ResistShock |
| ResistMagic ✔ | 100/150/200/250 | 10/15/20/25 | `039E51` REQ_Alch_ResistMagic |

### 3.7 Waterbreathing (4-tier) ✔  — value scales, magnitude 0, DURATION is the ladder
Value 40/60/80/100; mag 0; dur **120/180/240/300**. MGEF `03AC2D` REQ_Alch_Waterbreathing.
FormIDs `03AC2E..03AC31`.

### 3.8 Invisibility (4-tier) ✔ — magnitude 0, DURATION is the ladder
Value 100/150/200/250; mag 0; dur **20/30/40/50**. MGEF `03EB3D` REQ_Alch_Invisibillity (sic).
FormIDs `03EB3E`(t1), `03EB40`(t2), `03EB3F`(t3), `03EB41`(t4).

### 3.9 Single / special utility potions ✔
| EditorID | FormID | Value | effect(s) | live winner |
|---|---|---:|---|---|
| REQ_Potion_CurePoison | `065A64:Skyrim.esm` | 50 | REQ_Alch_CurePoison `109ADD` (mag0 dur0) | AR |
| REQ_Potion_CureDisease | `0AE723:Skyrim.esm` | 100 | REQ_Alch_CureDisease `0AE722` **+** Wounds `01CBEC:Wounds.esp` CureInfection | **Authoria-Wounds patch** |
| REQ_Potion_ResistPoison | `AD3A53:Requiem.esp` | 150 | REQ_Alch_ResistPoison `090041` (mag 75, dur 60) | AR |
| REQ_Potion_Dispel | `AD3A54:Requiem.esp` | 200 | REQ_Alch_Dispel `AD3A55:Requiem.esp` (mag0 dur0) | AR |
| REQ_WhitePhial_FortifyWeapons | `10201D:Skyrim.esm` | **50000** | FortifyOneHanded+TwoHanded+Marksman, mag 25 dur 300 each (`03EB19/1A/1B`) | AR (quest item) |
| DLC2dunBloodskalPotionOfWaterWalking | `0390E0:Dragonborn.esm` | 250 | vanilla AlchWaterWalking `0390E1` (mag0 dur60) | AR (re-value only) |

---

## 4. POISON ladders (Value | mag | dur | MGEF)

All poisons: Weight 0.5, Flags `NoAutoCalc, Poison`, keyword `VendorItemPoison`.

### 4.1 Damage Health / Magicka / Stamina (5-tier, instant dur 0) ✔ H & M read; S t1 read
| family | Value | mag | dur | MGEF |
|---|---|---|---:|---|
| DamageHealth ✔ | 20/40/60/80/100 | 20/40/60/80/100 | 0 | `03EB42` REQ_Alch_DamageHealth |
| DamageMagicka ✔ | 20/40/60/80/100 | **40/80/120/160/200** | 0 | `03A2B6` REQ_Alch_DamageMagicka |
| DamageStamina ~ (t1) | 20/… | 40/… | 0 | `03A2C6` REQ_Alch_DamageStamina |

Note: Damage Magicka/Stamina magnitude is **2×** the Damage Health magnitude at equal value/tier.

### 4.2 Lingering Damage Health / Magicka / Stamina (5-tier, dur 15) ✔ Health read; M/S t1 read
| family | Value | mag/sec | dur | MGEF |
|---|---|---|---:|---|
| LingeringDamageHealth ✔ | 20/40/60/80/100 | **2**/4/6/8/10 | 15 | `10AA4A` REQ_Alch_LingeringDamageHealth |
| LingeringDamageMagicka ~ (t1) | 20/… | **4**/… | 15 | `10DE5F` REQ_Alch_LingeringDamageMagicka |
| LingeringDamageStamina ~ (t1) | 20/… | **4**/… | 15 | `10DE5E` REQ_Alch_LingeringDamageStamina |

⚠ Health lingering starts at mag 2/tier; Magicka & Stamina lingering start at mag 4/tier — the
families do NOT share a magnitude base. Flagged.

### 4.3 Weakness to Fire / Frost / Shock / Magic (5-tier, dur 30) ✔ Fire read; F/S/M t1 read
| family | Value | mag (%) | dur | MGEF |
|---|---|---|---:|---|
| WeaknessFire ✔ | 30/60/90/120/150 | 10/20/30/40/50 | 30 | `073F2D` REQ_Alch_WeaknessFire |
| WeaknessFrost ~ (t1) | 30/… | 10/… | 30 | `073F2E` REQ_Alch_WeaknessFrost |
| WeaknessShock ~ (t1) | 30/… | 10/… | 30 | `073F2F` REQ_Alch_WeaknessShock |
| WeaknessMagic ~ (t1) | **40**/… | **5**/… | 30 | `073F51` REQ_Alch_WeaknessMagic |

⚠ WeaknessMagic breaks the elemental sub-pattern: Value base 40 (not 30), mag base 5 (not 10). Flagged.

### 4.4 Fear / Frenzy (5-tier, dur 15) ✔ both read
Identical ladder: Value 40/80/120/160/200; mag 5/10/15/20/25; dur 15.
Fear MGEF `073F20` REQ_Alch_Fear (Demoralize archetype). Frenzy MGEF `073F29` REQ_Alch_Frenzy.

### 4.5 Paralysis (5-tier) ✔ — magnitude 0, DURATION is the ladder
Value 40/80/120/160/200; mag 0; dur **1/2/3/4/5**. MGEF `073F30` REQ_Alch_Paralysis.
FormIDs `065A6A`(t1), `074A38/39/3A/3B`(t2–5).

### 4.6 Drain Regeneration (5-tier, dur 60) ✔ Stamina read; Magicka t1+t5 read
| family | Value | mag (%) | dur | MGEF |
|---|---|---|---:|---|
| DrainStaminaRegeneration ✔ | 20/40/60/80/100 | 80/160/240/320/400 | 60 | `073F2C` REQ_Alch_DamageStaminaRegeneration |
| DrainMagickaRegeneration ✔ (t1,t5) | 20/…/100 | 80/…/400 | 60 | `073F2B` REQ_Alch_DamageMagickaRegeneration |

⚠ Naming split: potion family is `REQ_Poison_Drain…Regeneration` but the linked MGEF is
`REQ_Alch_Damage…Regeneration` ("Drain" on the ALCH, "Damage" on the MGEF). Flagged.
⚠ Tier5 of Drain**Stamina**Regeneration is AR-DEFINED (`00082E:Requiem - Alchemy Redone.esp`) while
tiers 1–4 are Skyrim.esm overrides. Flagged.

### 4.7 AR-own poison families (`defined_in=true`)
**Silence (5-tier)** ✔ — mag 0, DURATION is the ladder. Value 40/80/120/160/200; dur 5/10/15/20/25.
MGEF `000815:Requiem - Alchemy Redone.esp` REQ_Alch_Silence (Script archetype). FormIDs `000817..00081B`.

**Drain Strength (5-tier)** ✔ Value 30/60/90/120/150; mag 10/20/30/40/50; dur 30.
MGEF `000816:Requiem - Alchemy Redone.esp` REQ_Alch_**Damage**Strength (DualValueModifier).
FormIDs `00081C..00081F`(t1–4) + t5 `00082F:Requiem - Alchemy Redone.esp`.
⚠ Same Drain(ALCH)/Damage(MGEF) naming split as §4.6.

**Poison of Soul Trap** — Value 100; mag 0 dur 60; MGEF `20B30F:Requiem.esp` REQ_Alch_SoulTrap.
FormID `20B310:Requiem.esp`. Single tier.

### 4.8 Oils (AR-own, `REQ_Oil_*`, no tiers) ✔
Weight 0.5, `NoAutoCalc, Poison`, keyword VendorItemPoison, dur 5. Each links its own AR-defined MGEF.
| EditorID | FormID | Value | mag | MGEF |
|---|---|---:|---:|---|
| REQ_Oil_Fire | `000807:…AR.esp` | 150 | 30 | `00080B` REQ_Effect_Oil_Fire (ValueMod, ResistFire) |
| REQ_Oil_Frost | `000808` | 200 | 30 | `00080C` REQ_Effect_Oil_Frost |
| REQ_Oil_Shock | `000809` | 250 | 30 | `00080D` REQ_Effect_Oil_Shock |
| REQ_Oil_Silver | `00080A` | 100 | 45 | `00080E` REQ_Effect_Oil_Silver |

---

## 5. AR's 19 OWN defined ALCH (`defined_in=true`, total=19)

4 Oils (`000807-00080A`) · 5 Silence (`000817-00081B`) · 4 DrainStrength t1–4 (`00081C-00081F`) ·
4 FortifyUnarmed (`000824-000827`) · DrainStaminaRegeneration t5 (`00082E`) · DrainStrength t5 (`00082F`).
These are AR's net-new content on top of the vanilla/Requiem ALCH set. All follow the ladder
conventions above (FortifyUnarmed rides the Requiem-defined MGEF `020E2B:Requiem.esp`; the rest ride
AR-defined MGEFs `00080B-00080E`, `000815`, `000816`).

---

## 6. MGEF layer — the conversion pattern

The ALCH record carries per-potion magnitude/duration/value; the MGEF carries archetype, actor value,
BaseCost, flags, resist channel, conditions, and the Requiem duration keyword. **Because every potion
is NoAutoCalc, MGEF BaseCost does NOT set potion gold** — it is legacy/autocalc metadata only. Value is
hand-set on the ALCH.

### 6.1 Who actually did the vanilla→Requiem conversion (conflict tree, REQ_Alch_RestoreHealth `03EB15`)
4 plugins touch it: Skyrim.esm → USSEP → **Requiem.esp** → **AR** (winner).
- Vanilla/USSEP: Archetype `ValueModifier` (Type ValueModifier), BaseCost **0.5**, Flags
  `NoDuration,NoArea,PowerAffectsMagnitude`, EditorID AlchRestoreHealth, **no** conditions, **no**
  Requiem keyword.
- **Requiem.esp does the heavy lift:** converts to `PeakValueModifier`, sets
  `Archetype.Association = 66CB38:Requiem.esp` (= keyword **REQ_Alchemy_RestoreHealthDuration**), adds
  it as a 3rd Keyword, adds 2 Conditions (IsUndead + HasKeyword `ActorTypeUndead 013796:Skyrim.esm` —
  the undead-fail gate), rewrites Description to the "per second for <dur>" form, renames EditorID to
  REQ_Alch_RestoreHealth, and drops BaseCost 0.5 → **0.25**.
- **AR's marginal delta is ONLY BaseCost 0.25 → 0.025.** (Refinement of prior "KNOWN SO FAR": the
  archetype/keyword/condition conversion is *Requiem's*, not AR's; AR just lowers BaseCost an order of
  magnitude.)

### 6.2 Archetype map by family (live winner)
| effect class | Archetype.Type | ActorValue | key flags |
|---|---|---|---|
| Restore H/M/S (ladder) | PeakValueModifier | Health/Magicka/Stamina | Recover?, PowerAffectsMagnitude, undead conditions |
| Restore *Complete* | **ValueModifier** + `NoDuration` | Health/… | instant, no duration |
| Fortify attribute/skill/regen/resist | PeakValueModifier | the specific AV (OneHandedPowerModifier, ResistFire, HealRateMult, CarryWeight, DestructionPowerModifier…) | Recover, PowerAffectsMagnitude |
| Resist elemental/magic | PeakValueModifier | ResistFire/ResistMagic | Recover, FXPersist |
| Weakness elemental/magic | PeakValueModifier | Resist<Element> (drains resist) | Hostile, Detrimental, FXPersist, ResistValue=PoisonResist |
| Damage H/M/S (instant) | ValueModifier | Health/Magicka/Stamina | Hostile, Detrimental, ResistValue=PoisonResist |
| Lingering Damage | ValueModifier | Health/… | Hostile, Detrimental, ResistValue=PoisonResist |
| Paralysis | Paralysis | Paralysis | NoMagnitude, PowerAffectsDuration, PoisonResist |
| Fear | Demoralize | Confidence | Hostile, Detrimental, PoisonResist |
| Frenzy | Frenzy (family) | — | (poison, PoisonResist) |
| Silence | **Script** | None | NoMagnitude, PowerAffectsDuration, PoisonResist |
| Drain Strength | DualValueModifier | Fame | Hostile, Detrimental, PoisonResist |
| Oil (elemental/silver) | ValueModifier | Health | Hostile, Detrimental, FXPersist, **NoDeathDispel**, ResistValue=Resist<Element> |

**Systematic rule:** beneficial effects → PeakValueModifier + `Recover` + `PowerAffectsMagnitude`,
low BaseCost, no resist channel. Harmful/poison effects → `Hostile`+`Detrimental` +
`ResistValue = PoisonResist` (so Requiem poison resistance mitigates them), `TargetType = Self`
(poison is coated then self-applied). "Instant" effects use ValueModifier+NoDuration; magnitude-less
effects (Paralysis, Silence, Invisibility, Waterbreathing) set `NoMagnitude`/mag 0 and scale by duration.

### 6.3 BaseCost table (live winner values)
| MGEF | FormID | BaseCost | winner |
|---|---|---:|---|
| RestoreHealth | `03EB15` | 0.025 | AR |
| RestoreHealthComplete | `0FFA03` | 0.01 | **Requiem.esp** |
| FortifyHealth | `03EAF3` | 0.02 | AR |
| FortifyCarryWeight | `03EB01` | 0.00725 | AR |
| FortifyHealthRegeneration | `03EB06` | 0.005 | AR |
| FortifyOneHanded | `03EB19` | 0.025 | AR |
| FortifyDestruction | `03EB26` | 0.025 | **Magic Redone** |
| FortifyEnchanting | `03EB29` | 0.025 | **Magic Redone** |
| ResistFire | `03EAEA` | 0.025 | AR |
| ResistMagic | `039E51` | 0.05 | AR |
| DamageHealth | `03EB42` | 0.15 | **BOOBIES patch** |
| LingeringDamageHealth | `10AA4A` | 0.15 | **BOOBIES patch** |
| DamageMagicka | `03A2B6` | 0.11 | **BOOBIES patch** |
| WeaknessFire | `073F2D` | 0.03 | AR |
| Fear | `073F20` | 0.25 | AR |
| Paralysis | `073F30` | 25 | AR |
| Silence | `000815` | 0.25 | **BOOBIES patch** (AR-defined!) |
| DamageStrength | `000816` | 0.025 | **BOOBIES patch** (AR-defined!) |
| Oil_Fire | `00080B` | 0 | AR |

BaseCost is NOT a clean per-family constant (beneficial 0.005–0.05, harmful 0.11–0.25, Paralysis 25);
and it is not load-bearing under NoAutoCalc. Do not derive potion gold from it.

---

## 7. LIVE-WINNER doctrine findings (critical for the skill)

houseCARL returns the load-order winner, which in THIS instance is frequently a downstream plugin, not
AR. A derivation MUST read the live winner (whatever plugin) as the comparable:

1. **Magic-school Fortify potions + their MGEFs are won by `Requiem - Magic Redone.esp`** (24 ALCH,
   6 MGEF). If a new mod adds a Fortify-Destruction-type potion, the comparable is Magic Redone's live
   record, not AR's underlying override.
2. **Harmful/poison MGEFs are won by `BOOBIES_potions - Requiem Patch.esp`** (17 of 80 MGEF), including
   AR's OWN defined `REQ_Alch_Silence` and `REQ_Alch_DamageStrength`. The live authority for
   DamageHealth/DamageMagicka/LingeringDamageHealth/Silence/DamageStrength MGEF values is this patch.
3. **REQ_Potion_CureDisease is won by `Authoria - Patch - Requiem - Wounds.esp`**, which layers a 2nd
   effect (Wounds "Cure Infection" `01CBEC:Wounds.esp`) on top of the base Cure Disease. A single-effect
   read of AR would miss the infection cure.
4. **Restore *Complete* MGEFs are won by `Requiem.esp`** (not AR); AR only wins the tiered Restore MGEFs.
   `SpookyPOP` patch wins 1 ALCH.

Takeaway for the consumables skill: derive tier/value/mag/dur/MGEF from the **live comparable Requiem
ladder member of the same family+tier**, read fresh each time — never hardcode, never assume AR is the
winner. The ladder conventions (value classes, tier suffixes, dur-vs-mag scaling axis, keyword,
flags) are stable and portable; the exact magnitude per effect is effect-specific and must be copied
from the matching comparable.

---

## 8. Inconsistencies / pattern-breaks (called out, not smoothed)

- **`REQ_Alch_Invisibillity` / `REQ_Potion_Invisibillity*`** — EditorID misspelled (double-L) on both
  the MGEF and all 4 potions; display Name is correctly "Invisibility". Purely cosmetic in EditorID.
- **Drain vs Damage naming split** — `REQ_Poison_Drain{Strength,StaminaRegeneration,MagickaRegeneration}`
  potions link MGEFs named `REQ_Alch_Damage{Strength,StaminaRegeneration,MagickaRegeneration}`.
- **Lingering magnitude bases differ** — LingeringDamageHealth t1 mag 2; LingeringDamage Magicka/Stamina
  t1 mag 4. Not a shared base.
- **WeaknessMagic off-pattern** — Value base 40 & mag base 5 vs the 30/10 elemental weakness base.
- **DamageMagicka/Stamina magnitude 2× DamageHealth** at equal value/tier (40 vs 20 at t1).
- **Fortify skill magnitudes are effect-specific** — Lockpicking mag 1, ArmorRating mag 120, Block
  mag 16, weapon skills 10-step, magic schools 6-step, Enchanting 4-step — all at the same tier structure.
  Do NOT reuse a sibling family's magnitude.
- **Split tier ownership** — DrainStaminaRegeneration t1–4 are Skyrim.esm overrides but t5 is AR-defined
  (`00082E`); same for DrainStrength t5 (`00082F`). The ladder spans two defining plugins.
- **AR-defined MGEFs overridden downstream** — `REQ_Alch_Silence`/`REQ_Alch_DamageStrength` are defined
  by AR yet won by the BOOBIES patch in this load order (override_depth 2). AR authorship ≠ live winner.
- **White Phial Value 50000** — unique quest item, far outside the ladder economy; multi-effect
  (Fortify OneHanded+TwoHanded+Marksman). Do not treat as a ladder member.

---

## 9. Un-read gaps (honesty ledger — inferred, not measured)
Tiers 2–4/5 of these families were not individually read; values shown ~ above are inferred from the
value-class + measured endpoints, and should be spot-verified before being used as authoritative:
FortifyStamina, Fortify Magicka/Stamina-Regeneration, ResistFrost/Shock t2–4, Fortify
Marksman/Alteration/ArmorRating/Block/Lockpicking t2–4, DamageStamina t2–5, LingeringDamage
Magicka/Stamina t2–5, Weakness Frost/Shock/Magic t2–5, DrainMagickaRegeneration t2–4, WellBeing t2–5,
and the ~40 Fortify-skill members (TwoHanded/Smithing/Sneak/Barter/Pickpocket/Conjuration/Illusion/
Restoration) not opened at all. Their family shape is established; only the exact per-tier magnitude
is unverified.
