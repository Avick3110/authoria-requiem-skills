# Stealth — sneak, lockpicking, pickpocket, traps

`Requiem - Stealth Redone.esp` overhauls the stealth axis (~309 records). It splits into a
record-driven part (sneak/pickpocket perks + ability spells, and the whole trap ecosystem) and a
**DLL-driven** part (lockpicking).

## What it touches

- **Perks (19):** the sneak / pickpocket / lockpicking mastery trees, e.g.
  `REQ_Sneak_Mastery_000_Stealth1 0BE126:Skyrim.esm`, `_050_LightSteps 05820C`,
  `_075_Acrobatics 105F23`, `_100_Shadowrunner 058214` (has a cooldown MGEF). Collected in FormLists
  `REQ_Perks_Lockpicking` / `REQ_Perks_Pickpocket`. **These are player-exclusive** — they're assigned
  to the player by the Reqtificator (`playerExclusive`: `sneak AD3A51`, `sneakAttack AD3A2E`,
  `pickpocket AD3A31`, `lockpicking 0BCC58`), never to NPCs.
- **Sneak/stealth ability spells + effects (SPEL/MGEF)** and the perk-grant spells.
- **Traps:** Activators (pressure plates, dart traps, spikes), trap Weapons, trap Scrolls/Projectiles/
  Explosions, ConstructibleObjects (bear/mine traps craftable at the forge), MiscItems.
- **GameSettings:** pickpocket/lockpicking skill curves.

## Lockpicking is an SKSE DLL — not record-patchable

`Requiem - Lockpicking SKSE\SKSE\Plugins\RequiemLP.dll` runs lockpicking; the ESP is a tiny stub. You
**cannot** patch lockpicking behavior with record overrides — it's config/DLL. New locks behave
automatically. Don't try to route a "fix lockpicking" request to a record edit; it's out of the ESP
patch surface.

## Integration recipes

- **A new sneak/stealth perk or ability** → hook into the existing sneak mastery tree
  (`perks-skills.md`); never author a parallel sneak tree. Player-exclusive.
- **A new trap** → it's record content: Activator + trigger + a trap Weapon/Spell/Explosion +
  (optionally) a forge COBJ. Route the weapon/explosion via `requiem-weapon-patching` /
  `requiem-magic-patching`, the placement via `requiem-leveled-list-patching`. Mirror a Stealth Redone
  trap's structure.
- **An NPC that should sneak well** → that's class/skills, not the player sneak perks
  (`requiem-npc-patching`). Don't give an NPC the player-exclusive sneak perks.
- **Pickpocket content** → player-exclusive perk territory; route to `perks-skills.md`.
