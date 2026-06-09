# Cost & Magnitude — the spell economy (live)

All numbers are the live MR (`Requiem - Magic Redone.esp`) winners read on 2026-06-09. They are a
**cross-check and starting point** — always read the same-school, same-delivery, same-tier comparable
live before writing, because cost varies by archetype, not by tier alone.

## Cost is explicit — there is no formula

Every Requiem spell SPEL carries `Flags = ManualCostCalc`. That means `BaseCost` is the literal magicka
cost (Requiem turned off the vanilla auto-calc-from-effects). **Read `BaseCost` from the comparable and
set it directly.** There is no magicka-cost formula in the Reqtificator config or documentation — the
"underivable cost formula" is a non-thing; cost is per-record and hand-tuned. The same holds for
enchantments (`EnchantmentCost`) and MGEF (`BaseCost` is the enchanting-cost-per-magnitude, ≈ irrelevant
to spell cost).

## Tier = the EditorID number = the `HalfCostPerk` perk

`REQ_<School><Tier>_<Element/Type>_<Delivery>`. Tier **1 = Novice, 2 = Apprentice, 3 = Adept,
4 = Expert, 5 = Master**. The tier is also encoded in the spell's `HalfCostPerk` (the
`REQ_<School>_Mastery_<NNN>` perk — see `perks-and-tomes.md`). Cost and magnitude both climb with tier,
but the *rate* depends on the school and the delivery.

## Cost is per-school-per-delivery, not one ladder

Cost is a function of (school, delivery, tier), not tier alone. Live examples:

| Spell | School/Tier | Cast / Target | ChargeTime | BaseCost |
|---|---|---|---|---|
| Flames / Frostbite / Sparks `012FCD`/`02B96B`/`02DD2A` | Destruction 1 | Conc / Aimed | 0 | 40 |
| Firebolt / Ice Spike / Lightning Bolt `012FD0`/`02B96C`/`02DD29` | Destruction 2 | FaF / Aimed | 0.5 | 90 |
| Fireball `01C789` | Destruction 3 | FaF / Aimed | 0.75 | 240 |
| Fire Wall `035D7F` | Destruction 4 | Conc / Aimed | 0.5 | 495 |
| Healing `012FCC` | Restoration 1 | Conc / Self | 0 | 40 |
| Fast Healing `02F3B8` | Restoration 2 | FaF / Self | 0.5 | 90 |
| Turn Lesser Undead `04B146` | Restoration 2 | FaF / Aimed | 0.5 | 100 |
| Superior Ward `0211F0` | Restoration 4 | Conc / Self | 0 | 60 |
| Candlelight `043324` | Alteration 1 | FaF / Self | 0.25 | 50 |
| Telekinetic Hand `01A4CC` | Alteration 3 | Conc / Self | 0 | 50 |
| Clairvoyance `021143` | Illusion 1 | Conc / Self | 0 | 25 |
| Invisibility on Self `027EB6` | Illusion 3 | FaF / Self | 0.75 | 250 |
| Bound Sword `0211EB` | Conjuration 1 | FaF / Self | 0.25 | 150 |
| Conjure Flame Atronach `0204C3` | Conjuration 3 | FaF / TargetLocation | 0.75 | 500 |
| Reanimate Dread Zombie `016C3F` | Conjuration 4 | FaF / TargetActor | 1.0 | 800 |

Read off this: **Conjuration summons/reanimates are the most expensive** (150 → 500 → 800),
**Destruction direct damage** is mid (40 → 90 → 240 → 495), **wards** are cheap concentration (60),
**Alteration/Illusion utility** is cheap (25–50). So pick the comparable by *what the spell does*, not
"it's tier 3, so 240."

## ChargeTime ladder (clean across schools)

| Tier / delivery | ChargeTime |
|---|---|
| Tier 1, Concentration | 0 |
| Tier 1, FireAndForget | 0.25 |
| Tier 2 | 0.5 |
| Tier 3 | 0.75 |
| Tier 4 | 1.0 |
| Ritual / Master AoE | 3+ |

Concentration spells generally charge 0 (you pay continuously). Requiem uses **ChargeTime as the
anti-spam lever** on big spells — a master nuke gets a long charge (Supernova was set to 6). Set it to
the tier standard unless the comparable says otherwise.

## Spell = multi-effect (the shape to mirror)

Requiem spells almost always have **2–3 effects**, not one. The canonical pattern (Flames, tier-1 fire,
cost 40):

| # | BaseEffect | Role | Magnitude | Dur |
|---|---|---|---|---|
| 0 | `013CA9` (vanilla fire-conc MGEF, MR-overridden) | primary fire damage | 16 | 0 |
| 1 | `005AA8:MR` `REQ_Effect_DestructionGM_Cremation1` | grandmaster execute (perk-gated) | 3 | 0 |
| 2 | `005AF5:MR` `REQ_Effect_DestructionGM_Fire_Taper` | lingering burn | 0 | 1 |

Frostbite (tier-1 frost) mirrors it: `013CAA` main (mag 16) + `0B729D` slow/stamina (mag 5, dur 1) +
`00607E:MR` frost taper (dur 3). **Key technique:** MR **reuses the vanilla MGEF FormID** for the
primary effect (overriding it to set school/resist/keywords/flags) and **adds MR-defined secondary
effects** for the taper / grandmaster mechanic. The MR-defined mechanic-only effects use a **dummy
ActorValue** (`Fame`) + the `HideInUI` flag so they don't clutter the UI. Copy this structure: don't
collapse a multi-effect spell into one effect.

Each `Effect` carries its own `Data` (`Magnitude`, `Area`, `Duration`) and optional `Conditions`
(e.g. a `HasPerk` gate on the grandmaster effect). Base magnitude is the tier value; it scales at
runtime through the MGEF `PowerAffectsMagnitude` flag (the player's skill multiplies it) — so you set
the *base*, not the scaled number.

## What to set, in order

1. `CastType` + `TargetType` (from the comparable).
2. `BaseCost` (explicit, from the comparable) + `Flags = ManualCostCalc` (+ self-buff flags).
3. `ChargeTime` (tier ladder).
4. The `Effects` list: each effect's `BaseEffect` MGEF + `Data` magnitude/area/duration + conditions.
5. `HalfCostPerk` = the school+tier Mastery perk (`perks-and-tomes.md`).
6. Keywords + MGEF flags (`keywords.md`).
