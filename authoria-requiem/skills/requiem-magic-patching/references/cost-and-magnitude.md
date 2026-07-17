# Cost & Magnitude — the spell economy (live)

All numbers are the live MR (`Requiem - Magic Redone.esp`) winners read on 2026-06-09. They are a
**cross-check and starting point** — always read the same-school, same-delivery, same-tier comparable
live before writing, because cost varies by archetype, not by tier alone.

## Cost is explicit — there is no formula

Every Requiem spell SPEL carries `Flags = ManualCostCalc` — measured 2026-07-17: **827 of 833
castable (`Type=Spell`) MR records, 99.3%**; the exceptions are non-castable support records
(enchant drivers, creature innates). That means `BaseCost` is the literal magicka cost (Requiem
turned off the vanilla auto-calc-from-effects). **On a modded spell this flag is a WRITE, not just
a read: mods ship auto-calc spells, and without `ManualCostCalc` the engine ignores the `BaseCost`
you set and re-derives cost from magnitudes — the whole ladder below silently stops applying. Tick
the flag (as a union — keep the winner's other bits) and set `BaseCost` from the comparable.** There is no magicka-cost formula in the Reqtificator config or documentation — the
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

**The delivery multipliers (mapped exhaustively on Destruction, 2026-07-17).** Cost is per
(school, tier, delivery); the deliveries multiply the tier's single-target base: **Rune ≈ 2×,
AimedArea/AoE ≈ 2×, EnchSelf ≈ 1.5×, master Ritual is the ceiling (RitualSelfExp 1800 at T5).**
Destruction's spine: Conc T1 40 → T3 128 → T5 480; FaF single T2 90 → T4 330 → T5 600.
Single-target headline per school: Restoration Healing 40/~90/~150/~200/800+; Alteration flesh
100/150/200/250/500–800; Illusion 75/150/225–300/300–600/1000+; **Conjuration prices by what is
summoned, not by tier alone** (T5: Dremora Lord 1000, Flame Thrall 1200, Frost 1500, Storm 1800).
Per-tick sub-spells (`_Damage`, cloak ticks, hazards) carry their own small costs (15–160).

## ChargeTime ladder (clean across schools — re-verified 2026-07-17)

| Delivery | ChargeTime |
|---|---|
| Concentration (any tier) | **0, always** |
| FaF single-target (Aimed/AimedExp/Rune) | **0.25 × tier** — T2 0.5, T3 0.75, T4 1.0, T5 1.25 |
| Touch | **half the tier's aimed** — T1 0.125, T3 0.375, T5 0.625 |
| SelfExp (master novas) | 0.5 |
| Master Ritual (RitualSelfExp) | **3.0** |

Requiem uses **ChargeTime as the anti-spam lever** on big spells — a master nuke gets a long charge
(Supernova was set to 6). Set it to the tier standard unless the comparable says otherwise.

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

**When you patch a modded spell, ADD the riders its MR comparable carries** — a modded fireball
with one effect is under-built, not "already fine." The signature riders per archetype (all
verified live 2026-07-17; magnitudes `m`, durations `d` at the sampled tier):

| Archetype | Rider(s) the comparable adds | Example FormIDs |
|---|---|---|
| Fire | **Cremation** lingering burn DoT (m3/d4 at T3) | `REQ_Effect_DestructionGM_Cremation3 005AAA:MR` |
| Frost | **Slow** (m5/d4) + DeepFreeze pair | `…GM_Slow_Aimed 0B729F:Skyrim.esm`, `…GM_DeepFreeze4 005AA6:MR` + `…_Weakness 00607E:MR` |
| Shock | **Electrostatic Discharge** magicka damage (m20) + stagger | `…GM_ElectrostaticDischarge3_Magicka 005AAF:MR` |
| Venom | **Venom DoT** (m6/d5) | `…GM_Venom_DoT 005B0B:MR` |
| Any FaF Destruction | **Impact Stagger** (m0.25) — near-universal | `REQ_Effect_DestructionGM_Impact_Stagger 0153D3:Skyrim.esm` |
| Healing | **Respite** stamina restore (m8 alongside m16 heal) | `REQ_Effect_RestorationGM_Respite_ConcSelf 04250D:Skyrim.esm` |
| Ward | secondary **Ward_Shield** (m50) | `REQ_Effect_RestorationGM_Ward_Shield 0FCC62:Skyrim.esm` |
| Restoration Poison | **ParalyzingPoison** pair (perk rider) | `…GM_ParalyzingPoison3 005D80:MR` + `…_Weakness 0060D3:MR` |
| Alteration flesh | perk-gated **Armor_Improved**, split by cuirass | `REQ_Effect_AlterationGM_Armor_Improved 104AB5:Skyrim.esm` (see below) |
| Conjuration summon | perk-gated **_Potent** upgrade summon | `…Daedra_FrostAtronach_Potent 04E946:Skyrim.esm` |
| Illusion mind | **Break1/Break2** level caps (single vs dual-cast) + `_Desc_Improved` riders | `Frenzy Break1 3FF1EE:Requiem.esp`, `Break2 401989:Requiem.esp` |

The flesh-spell rider is the **perk-gate exemplar**: `Armor_Improved` appears TWICE on Stoneflesh —
once m75 with `WornHasKeyword(ArmorCuirass) == 0` (unarmored bonus) and once m30 with `== 1` —
both arms gated by an OR-block of the three MageArmor perks. Copy shapes like this verbatim from
the comparable (condition doctrine: `mgef-conditions.md`). Many riders are unconditional — the
perk description makes them inert without the perk — so presence of an effect entry ≠ it fires.

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
