# Core combat & resistance model

This is the "why" behind most balance calls. Damage in Requiem is not a flat number — it's weapon
output resolved against the target's **armor rating**, **elemental/magical resistances**, and
**armor-penetration**, shaped by **stagger** and **stamina/exhaustion**. New combat content has to
slot into this chain via keywords, which the Reqtificator reads to assign the derived perks.

## The all-actor combat perks (Reqtificator-assigned — never hand-stamp)

`ActorAssignmentRules_Requiem.esp.conf` → `gameMechanics.perks`, applied to **every actor** at build:

| Lever | Perk FormID | Role |
|---|---|---|
| Armor pen, slash | `AD394D Requiem.esp` | bypass armor with slashing weapons |
| Armor pen, blunt | `AD394E Requiem.esp` | bypass armor with blunt weapons |
| Armor pen, pierce | `AD394C Requiem.esp` | bypass armor with piercing weapons |
| Armor pen, ranged | `AD3948 Requiem.esp` | bypass armor with ranged weapons |
| Standard attacks | `AD394A Requiem.esp` | base attack damage handling |
| Power attacks | `AD394B Requiem.esp` | power-attack damage handling |
| Armor weight | `AD3A34 Requiem.esp` | encumbrance/movement from worn weight |
| Arrow recovery | `AD3A35 Requiem.esp` | recover fired arrows |
| Absorb rescaling | `962799 Requiem.esp` | rescale absorb effects |
| Poison rescaling | `962798 Requiem.esp` | rescale poison effects |
| Ward damage reduction | `682FB5 Requiem.esp` | ward block behavior |

Armor-penetration is keyed to the **weapon's damage type** — which is itself a Reqtificator output
(the slash/blunt/pierce/ranged keyword assigned from the weapon-type keyword; see
`requiem-weapon-patching`). So the chain is: you carry the **weapon-type** keyword → the Reqtificator
assigns the **damage-type** keyword → the matching **armor-penetration** perk resolves against the
target's armor. Get the weapon-type keyword right and the whole chain follows.

## Armor & physical resistance

- Requiem stores `ArmorRating` at ~10× vanilla, capped per slot/class (heavy body 74 / head 35 /
  hands·feet 27 / shield 54; light body 62 / head 26 / hands·feet 18 / shield 44, in the ÷10 scale).
  See `requiem-armor-patching`.
- **Material bonuses** grant stacking resistances (up to 4 worn pieces; shields don't count): e.g.
  Dwarven less blunt, Stalhrim frost resist, Glass/Ebony fire resist, Dragonplate/scale voice
  resistance (+ Unrelenting Force immunity at 3–4 pieces), Dawnguard less vampire-drain. These ride on
  the armor-set keyword and are assigned by the Reqtificator — carry the set keyword, don't stamp the
  resistance.
- The ranged-resistance tier keyword (`rangedResistance.tier1..5`) is likewise assigned from set+type.

## Magical resistance

- Magic resistance is **capped at 60 points per race**, and magic-resist values count **×3** toward
  that cap (so 20% magic resist ≈ the whole budget). Racial resistances (Nord frost/shock, Dunmer
  fire, Breton magic) live on the **race's** ability spells (`requiem-race-patching`), engine-inherited
  by every NPC of the race.
- Elemental resistance is per-school (`ResistFire/Frost/Shock/Poison/MagicResist`), set on the MGEF's
  `ResistValue`. New damage effects must declare the school they're resisted by (or be unresistable,
  like Arcane) — see `requiem-magic-patching`.
- **Absorb** is reduced by absorb-resistance; dwarven automata are immune. **Wards** block stamina
  damage and reduce stagger.

## Stagger / poise

Stagger is driven by weapon attack data + power-attack multipliers, mitigated by armor weight, wards,
and dragon-plate voice resistance. Crossbows have a dedicated stagger spell
(`gameMechanics.spells.crossbowStagger = AE3597 Requiem.esp`). Heavy hits stagger; light hits mostly
don't. New weapons inherit this from their type's attack data — don't invent stagger values.

## Stamina / exhaustion

Attacks cost stamina; low stamina (exhaustion) penalizes you. This is the
`gameMechanics.perks.stress` family + the `exhaustionController`/`baseAttackSpeed` spells — see
`exhaustion-stress.md`. All-actor, Reqtificator-assigned.

## The keyword chain (what ties it together)

```
weapon-type keyword (carried) ─▶ damage-type keyword (assigned) ─▶ armor-pen perk (all-actor)
armor-set keyword (carried)  ─▶ resistance + tempering tiers (assigned) ─▶ material resist bonus
race (carried)               ─▶ racial trait spells (inherited) + incoming-damage perk (assigned)
worn weight (record)         ─▶ armor-weight perk (all-actor) ─▶ stagger / stamina
```

Every box on the left is an **input you carry**; every "assigned/inherited" box is a **Reqtificator
output you must not hand-stamp**. That single discipline is most of what "patch for Requiem" means at
the combat layer.
