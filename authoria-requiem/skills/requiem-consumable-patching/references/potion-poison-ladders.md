# Potion & Poison Ladders (Requiem - Alchemy Redone)

Mined live from the load-order winners (2026-07-15). Tables are a **map and cross-check** — the
live comparable read is always the authority; re-verify a tier before stamping it. FormIDs are
wire tokens `XXXXXX:Plugin.esp`; bare 6-hex FormIDs in tables are `:Skyrim.esm`.

## Shape invariants (every AR-corpus potion/poison/oil)

- Weight **0.5**. Flags: potions `NoAutoCalc`; poisons & oils `NoAutoCalc, Poison`.
- Exactly **one keyword**: `VendorItemPotion 08CDEC:Skyrim.esm` (potions) or
  `VendorItemPoison 08CDED:Skyrim.esm` (poisons AND oils). No Requiem keyword sits on the ALCH;
  the Requiem plumbing (duration keyword, conditions) lives on the MGEF.
- Magnitude/duration are carried on the **potion's** `Effects[0].Data`, not the MGEF.
- EditorIDs: `REQ_Potion_<Effect><1-5|Complete>` / `REQ_Poison_<Effect><1-5>` / `REQ_Oil_<Element>`.
  Name tier suffixes: 1 `(Diluted)` · 2 `(Faint)` · 3 `(Fair)` · 4 `(Good)` · 5 `(Remarkable)` ·
  Complete `(Surpassing)`.
- **No addiction fields anywhere** — no ALCH in the corpus uses Addiction/AddictionChance.
- **Value is hand-set** (NoAutoCalc is universal): MGEF BaseCost does NOT set gold. Never derive
  value from BaseCost.

## Potion ladders

**Restore Health/Magicka/Stamina** (dur 20; heal-over-time): Value 20/40/60/80/100, mag
3/5/8/10/12 per second. `Complete` tier: Value 500, mag 9999 dur 0 via a **distinct** `*Complete`
MGEF (ValueModifier+NoDuration — instant). MGEFs: Health `03EB15` / Magicka `03EB17` / Stamina
`03EB16`; Complete `0FFA03/0FFA04/0FFA05` (Complete MGEFs are won by `Requiem.esp`, not AR).
Health potion FormIDs: t1-5 `03EADD/03EADE/03EADF/03EAE3/039BE4`, Complete `039BE5`.

**Well-Being** (Restore all three in one, Dragonborn FormIDs `01AADD..01AAE2:Dragonborn.esm`):
mags mirror the Restore ladder; Value t1 60, Complete 1500.

**Fortify attribute** (dur 300): Health/Magicka/Stamina Value 40/80/120/160/200, mag
20/40/60/80/100. CarryWeight differs: Value 20..100, mag 10..50, dur **600** (`03EB01`).

**Fortify regeneration** (dur 300): Value 30/60/90/120/150, mag 40/80/120/160/200 (%).
HealthRegen MGEF `03EB06`.

**Fortify skill** (4 tiers, dur 60) — **two value classes, per-skill magnitudes**:
- Class A, Value 80/120/160/200: weapon skills + all magic schools.
- Class B, Value 60/90/120/150: utility skills (Enchanting, Speech, Unarmed, Lockpicking,
  ArmorRating, Block…).
- **Magnitude is effect-specific** — measured: OneHanded 10/15/20/25; Speed 6/9/12/15;
  Destruction 6/9/12/15; Enchanting 4/6/8/10; Speech 10/15/20/25; Lockpicking base 1;
  ArmorRating base **120**; Block base **16**. Never reuse a sibling family's magnitudes — read
  the exact family's tier.
- **Magic-school fortify potions + MGEFs are won by `Requiem - Magic Redone.esp`** — the live
  comparable for a school-fortify is Magic Redone's record.

**Resist** (4 tiers, dur 60): Fire/Frost/Shock Value 80/120/160/200, mag 20/30/40/50 (MGEFs
`03EAEA/03EAEB/03EAEC`). ResistMagic differs: Value 100/150/200/250, mag 10/15/20/25 (`039E51`).

**Duration-scaled potions** (mag 0 — the ladder is the duration):
- Waterbreathing: Value 40/60/80/100, dur 120/180/240/300 (`03AC2D`).
- Invisibility: Value 100/150/200/250, dur 20/30/40/50 (MGEF `03EB3D` — EditorID misspelled
  `REQ_Alch_Invisibillity`; the FormID is the vanilla Invisibility MGEF, overridden in place).

**Specials**: CurePoison `065A64` V50 · CureDisease `0AE723` V100 — live winner is a downstream
Wounds patch that adds a 2nd Cure-Infection effect (`01CBEC:Wounds.esp`); read the winner ·
ResistPoison `AD3A53:Requiem.esp` V150 · Dispel `AD3A54:Requiem.esp` V200 · White Phial
`10201D` V**50000** (quest unique, off-ladder — never a ladder comparable).

## Poison ladders

**Damage Health/Magicka/Stamina** (instant, dur 0): Value 20/40/60/80/100. Mag: Health
20/40/60/80/100; **Magicka & Stamina run 2×** (40..200). MGEFs `03EB42/03A2B6/03A2C6`.

**Lingering Damage** (dur 15): Value 20..100. Mag/sec: Health base **2**/tier; Magicka & Stamina
base **4**/tier — the families do NOT share a base. MGEFs `10AA4A/10DE5F/10DE5E`.

**Weakness elemental** (dur 30): Fire/Frost/Shock Value 30/60/90/120/150, mag 10..50%
(`073F2D/073F2E/073F2F`). **WeaknessMagic breaks the sub-pattern**: Value base 40, mag base 5
(`073F51`).

**Fear / Frenzy** (dur 15): both Value 40/80/120/160/200, mag 5/10/15/20/25 (`073F20`/`073F29`).

**Paralysis** (mag 0, duration-scaled): Value 40..200, dur **1/2/3/4/5** (`073F30`).

**Drain Regeneration** (dur 60): Value 20..100, mag 80..400%. Naming split: the ALCH family is
`REQ_Poison_Drain…` but the MGEFs are `REQ_Alch_Damage…Regeneration` (`073F2C`/`073F2B`).

**AR-own families**: Silence — mag 0, dur 5/10/15/20/25, Value 40..200 (MGEF
`000815:Requiem - Alchemy Redone.esp`, Script archetype). DrainStrength — Value 30..150, mag
10..50, dur 30 (MGEF `000816:…AR.esp`, DualValueModifier). Poison of Soul Trap
`20B310:Requiem.esp` V100, dur 60 (MGEF `20B30F:Requiem.esp`).

**Oils** (`REQ_Oil_*`, AR-defined `000807..00080A:…AR.esp`, single-tier, dur 5): Fire V150 m30 ·
Frost V200 m30 · Shock V250 m30 · Silver V100 m45. Poison-flagged, VendorItemPoison.

## MGEF conversion pattern

The vanilla→Requiem MGEF conversion is **Requiem.esp's** work (archetype swap, duration keyword,
undead-fail conditions, per-second descriptions); AR's marginal delta is typically only a BaseCost
drop. Archetype map (live winners):

| class | Archetype | key flags |
|---|---|---|
| Beneficial tiered (restore/fortify/resist) | PeakValueModifier | Recover, PowerAffectsMagnitude; restore adds undead-fail Conditions |
| Restore Complete | ValueModifier + NoDuration | instant |
| Damage/Lingering (poison) | ValueModifier | Hostile, Detrimental, **ResistValue=PoisonResist**, TargetType Self |
| Weakness | PeakValueModifier | Hostile, Detrimental, PoisonResist |
| Paralysis / Silence / (duration-scaled) | Paralysis / Script | NoMagnitude, PowerAffectsDuration, PoisonResist |
| Fear / Frenzy | Demoralize / Frenzy | Hostile, Detrimental, PoisonResist |
| Oils | ValueModifier | Hostile, Detrimental, NoDeathDispel, ResistValue=Resist<Element> |

Poison MGEFs channel `PoisonResist` — that is how Requiem poison resistance mitigates them; a new
harmful consumable MGEF missing it bypasses the resistance system (that's a
`requiem-magic-patching` concern when designing one).

**Live-winner traps measured on this corpus**: poison/harmful MGEFs (incl. two AR-defined ones)
won by a downstream compatibility patch; magic-school fortifies by Magic Redone; Restore-Complete
MGEFs by Requiem.esp; CureDisease ALCH by a Wounds patch. AR authorship ≠ live authority — always
read the winner.

## Known pattern-breaks (flag, don't smooth)

- `Invisibillity` EditorID misspelling (MGEF + all 4 potions).
- Drain(ALCH)/Damage(MGEF) naming split on regen + strength poisons.
- Lingering mag bases differ per family (2 vs 4); DamageMagicka/Stamina 2× DamageHealth;
  WeaknessMagic 40/5 vs the 30/10 elemental base.
- Split tier ownership: DrainStaminaRegeneration t1-4 are Skyrim.esm overrides, t5 AR-defined —
  one ladder spans two defining plugins.
- Some upper tiers of low-traffic families were corpus-verified only at endpoints; spot-verify a
  tier live before treating its exact magnitude as authoritative.
