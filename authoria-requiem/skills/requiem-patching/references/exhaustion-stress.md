# Exhaustion & stress

Requiem makes stamina a combat resource: attacks cost stamina, casting in heavy armor is penalized,
and being over-encumbered or low-stamina (exhausted) degrades performance. The whole system is
**all-actor and Reqtificator-assigned** — new content rarely touches it directly; it just has to
carry the standard keywords so the system applies.

## The levers (FormIDs — `gameMechanics`, applied to every actor)

| Lever | FormID | Role |
|---|---|---|
| Attack stamina cost | `attackStaminaCost 6B9709 Requiem.esp` | attacks drain stamina |
| Exhaustion penalties | `exhaustionPenalties 95FFFB Requiem.esp` | penalties when stamina is depleted |
| Armored casting | `armoredCasting 703B25 Requiem.esp` | spell-cost penalty for heavy/worn armor |
| Mass effect | `massEffect 755649 Requiem.esp` | encumbrance/weight effect on movement |
| Base attack speed (spell) | `baseAttackSpeed 03E556 Requiem.esp` | normalizes attack speed |
| Exhaustion controller (spell) | `exhaustionController 062275 Requiem.esp` | runs the exhaustion state |
| Crossbow stagger (spell) | `crossbowStagger AE3597 Requiem.esp` | crossbow stagger handling |

These are assigned to **all** actors by the Reqtificator (`feature_gameMechanics`, no condition). You
never carry or stamp them.

## Integration recipe

- **A new weapon / attack type** → just carry the standard weapon-type + material keywords
  (`requiem-weapon-patching`). Stamina cost, attack speed, and stagger follow from the type. Don't add
  a bespoke stamina-cost effect.
- **A new actor** → nothing special; the controller applies to it once it's a balanced actor
  (`requiem-npc-patching`).
- **A spell that interacts with stamina/exhaustion** (e.g. a fatigue spell) → design via
  `requiem-magic-patching` using `Stamina`/`Fame`-style ActorValues like Requiem's comparables; don't
  duplicate the exhaustion controller.
- **Armor that should ease casting penalties** → that's the armored-casting interaction; it keys off
  armor weight/type keywords (`requiem-armor-patching`), assigned — not stamped.

If a mod ships its own stamina/fatigue system, it will likely conflict with Requiem's; the integration
call is usually to let Requiem's system govern and neutralize the mod's parallel one, not to merge both.
