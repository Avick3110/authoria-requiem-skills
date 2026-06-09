# Skills

One folder per skill (`<slug>/SKILL.md` + a bundled `references/` library + an archived `evals/` set), mirroring houseCARL's skill layout. Claude Code auto-discovers each `SKILL.md` — there is no build-script registry to update.

Load **`requiem-patching`** first for a whole-mod job; it routes to the domain skills below (which also trigger directly on their own record types).

- `requiem-patching` — integration brain / router + the gap mechanics
- `requiem-weapon-patching` — weapons (melee, bow/crossbow, staff frames)
- `requiem-armor-patching` — armor (light/heavy, shields, clothing/jewelry frames)
- `requiem-ammo-patching` — arrows & bolts
- `requiem-race-patching` — playable & creature races
- `requiem-npc-patching` — NPCs / enemies / followers
- `requiem-leveled-list-patching` — leveled lists, containers, encounter zones
- `requiem-magic-patching` — spells, effects, enchantments
- `requiem-script-patching` — the Papyrus / scripts layer

The cross-cutting masters/`REQ_NULL` hygiene rule and the scope/authority rule live in the `requiem-patching` skill's `references/` (`masters-and-null-stripping.md` and `scope-and-authority.md`).
