# Vampirism & lycanthropy

The headline gap mechanic. Requiem rebuilds both as staged conditions with their own powers,
weaknesses, and trait perks. New content that touches them (a vampire/werewolf race, a vampiric NPC,
a custom feeding power, a beast-form ability) integrates by routing the records to the right domain
skill **and** carrying the right keyword/race so the Reqtificator assigns the trait. The comparables
are live: `Requiem - VampireCollection.esp` (active), the vanilla vampire/werewolf/werebear races (via
`requiem-race-patching`), and the vampire/werewolf spells (via `requiem-magic-patching`).

## The trait spine (FormIDs — Reqtificator-assigned, carry the input)

From `ActorAssignmentRules_Requiem.esp.conf`. These are **outputs** — you carry the keyword/race; the
Reqtificator assigns the perk at build. Never hand-stamp them.

| Trait | Perk | Assigned when the actor has… |
|---|---|---|
| Vampire incoming-damage trait | `RFTI_Trait_Vampire AD3A44` | the vampire keyword `0A82BB:Skyrim.esm` (`stateTraits.vampire`) |
| Werewolf incoming-damage trait | `RFTI_Trait_Lycanthrope_Werewolf AD3A43` | werewolf race `0CDD84:Skyrim.esm` (`racialTraits.werewolf`) |
| Werebear incoming-damage trait | `AE3599 Requiem.esp` | werebear race `01E17B:Dragonborn.esm` |
| Player vampire perk | `RFTI_Player_Vampire 035E31` | player + vampire (playerExclusive) |
| Player werewolf perk | `RFTI_Player_Werewolf AD3A42` | player + werewolf |
| Player werewolf abilities ("Bestial Blood") | spell `RFTI_Player_WerewolfAbilities 2C96D1` | player werewolf |

Control keyword: `keywords.vampire = 0A82BB Skyrim.esm`. A new vampiric actor just needs this keyword
(humanoid vampire) or the vampire race (full vampire) and the Reqtificator does the rest.

## Vampirism — the system (from the docs)

- **Staged by feeding/blood-starvation.** Visual distortion and weakness lessen when well-fed; worsen
  when starved. Feeding (e.g. Ring of Namira: corpse < 1 hour dead, 8-hour cooldown) restores.
- **Sun weakness:** ~**12 damage/sec to attributes** in direct sunlight (health damage caps at 30 HP —
  it won't kill, matching lore), mitigated when the sun is low or clouded.
- **Holy ground:** vampires lose powers inside temples and take ~**4 damage/sec** on holy ground
  (caps at 30 HP). Not penalized in Sovngarde / Hall of the Dead.
- **Dawnbreaker** can be touched without penalty (anti-pickpocket-cheese) but wielding it is suicidal.
- **Elder vampires** hit harder (power-attack multiplier up to 2.0×, heavy stagger).
- **Anti-vampire gear** (Dawnguard set) grants stacking "less vampire-drain damage" as its material
  bonus.

## Lycanthropy — the system

- **Beast races** (Argonian, Khajiit, Orc) have the highest starting attributes (320 points).
- **Werewolf form** health/stamina bonuses are **halved in human form**.
- **Human form penalties:** −50% poison resist, +15% spell cost.
- **Beast form:** −100% poison resist.
- **Predator's Might / Animal Allegiance**, **Bestial Blood** abilities (`2C96D1`), and Nord-exclusive
  **Kyne's Token** milestones (stacking stamina/health/damage/shout-cooldown boons) round it out.

## Integration recipes

**A new vampire/werewolf RACE** → `requiem-race-patching`. Clone the matching vanilla
vampire/werewolf/werebear race's structure (it carries the trait spells engine-inherited by its NPCs);
carry the vampire keyword or werewolf race lineage. The Reqtificator assigns the incoming-damage perk
to the race's NPCs (or hand-add per the race skill's trait bridge if the race is unrecognized). Strip
any free player-power the mod's race grants if it's playable.

**A vampiric/werewolf NPC** (not a new race) → `requiem-npc-patching`. Carry the vampire keyword
`0A82BB` (or set the race) so `RFTI_Trait_Vampire`/`Werewolf` is assigned; balance level/class/perks
as a normal actor. Don't stamp the trait perk yourself.

**A new vampire/werewolf POWER or feeding ability** → `requiem-magic-patching`. Design the MGEF/SPEL
against the comparable Requiem vampire/werewolf spell (cost, magnitude, `PowerAffectsMagnitude`,
keywords). A *special mechanic* (a scripted feed/transform) → `requiem-script-patching` (reuse a Nox/
REQ script if one matches; most powers are plain effects and need no script). For the player, the
power rides on the vampire/werewolf player perk family — don't grant a free always-on player ability.

**A disease vector** (a bite that turns the player) → see `diseases.md`; Sanguinare Vampiris /
lycanthropy contraction follows the staged-ability-spell pattern.

## Decision: scope

This stays a **capstone reference**, not a separate skill — the integration work is routing to the
existing race/npc/magic/script skills with the keyword/trait constraints above, not a new procedural
method with its own evals. If a future job needs a full vampire-clan overhaul with bespoke staging,
revisit promoting it. Not yet verified live: the per-stage power/weakness magnitudes here are from the
docs; confirm the exact MGEF magnitudes live against `Requiem - VampireCollection.esp` when patching a
mod that rebalances them.
