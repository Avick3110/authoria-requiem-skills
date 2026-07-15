# Perks, skills & leveling

Requiem's progression is perk-gated and skill-by-use. The integration rule is simple and strict:
**hook new content into the existing perk trees; never author a parallel tree.** A spell joins a
school through its `HalfCostPerk` tier perk; a weapon/armor joins crafting through the smithing perks;
a stealth ability joins the sneak tree. Adding a separate "Mod X perk tree" breaks the gated economy.

## Player-exclusive perk/spell assignment (Reqtificator)

Skill perks are assigned to the **player only** at build (`playerExclusive` in
`ActorAssignmentRules`): `alchemy AD3A2F`, `enchanting AD3A30`, `lockpicking 0BCC58`,
`pickpocket AD3A31`, `sneak AD3A51`, `sneakAttack AD3A2E`, `speech AD3A2D`, `tempering 8C8ED3`, plus
utility spells (`examine 0832CC`, `bagOfHolding 078AA8`, the true-yield cloaks, `werewolfAbilities
2C96D1`). NPCs get **combat** perks (source-carried) but not these player skill perks. So:

- Don't give an NPC a player skill perk (alchemy, lockpicking, sneak mastery, …).
- Don't hand-stamp a player skill perk onto the player record — the Reqtificator assigns it.

## How new content hooks in

| New content | Hook |
|---|---|
| A spell | its `HalfCostPerk` = the school+tier mastery perk (`requiem-magic-patching`). That perk gates learning and casting; no new perk needed. |
| A craftable weapon/armor | the material's smithing perk in the COBJ condition (`requiem-weapon-/armor-patching`). |
| A specialization effect (e.g. a fire spell's bonus) | the school's specialization perk gates the secondary MGEF (`PerkToApply`/condition) — copy the gated effect verbatim. |
| A sneak/stealth ability | the existing sneak mastery tree (`stealth.md`), player-exclusive. |
| An NPC's combat ability | source-carried combat perks on the NPC — derive the set from equipment/spell kit/tier via `requiem-perk-assignment`; the NPC frame stays with `requiem-npc-patching`. |

## If a mod ships its own perks

Route the PERK records to `requiem-perk-assignment` — its disposition rule (leave the mod's runtime
plumbing alone / fold an obvious duplicate of a Requiem gate / flag a balance-bearing overlap to the
user with a concrete question) is the owner method. If the mod ships no PERK record, route the
*capability* to the matching existing tree. Either way, a standalone perk duplicating a Requiem gate
is neutralized or folded, never stacked alongside it.

## Leveling

Player skills advance by use; there's an optional experience reduction perk
(`experienceOptionalReduction 07CB89`). The world is de-leveled (NPCs/lists fixed) — new content must
not reintroduce player-level scaling (`requiem-npc-patching` / `requiem-leveled-list-patching`).
