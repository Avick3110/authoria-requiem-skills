# Standing stones / birthsigns / doomstones

`Requiem - Birthsigns Redone.esp` rebuilds the standing-stone blessings (~74 records: 33 SPEL, 40
MGEF, 8 PERK, a control quest). (`Requiem - Ryn's Standing Stones.esp` is a world/mesh mod — it holds
only a couple of NPC records, not the abilities; the abilities live in Birthsigns Redone.)

## The shape

A standing stone grants a **ConstantEffect, Self, Ability SPEL** with several stacked MGEFs (a main
flavor effect + attribute/skill modifiers). Example — `REQ_Ability_Birthsign_Thief 0E5F45:Skyrim.esm`
(winner Birthsigns Redone): Type=Ability, CastType=ConstantEffect, TargetType=Self, with 5 effects:
the main `REQ_Effect_Birthsign_Thief 0E5F44` + four Requiem-defined sub-effects (`000933`, `00090C`,
`00090B`, `00090D Requiem.esp`). The blessing is applied/removed by the birthsign control quest when
the player activates a stone; **player-only**.

## Integration recipe

- **A new standing stone / birthsign / doomstone power** → build a ConstantEffect Self ability SPEL
  with the stacked-MGEF shape, cloning a Birthsigns Redone ability for the structure; design the MGEFs
  via `requiem-magic-patching`. Magnitudes should sit in the same range as the existing signs (these
  are persistent passives — overscaling them is a balance break).
- **Hooking it to a world stone** → the activation/swap is driven by the birthsign control quest
  (`REQ_Quest_Birthsign`); a new stone needs to register its blessing with that quest, or reuse an
  existing slot. If a mod adds a physical stone, the patch routes the *ability* here and the
  *activation wiring* to the quest/script side (`requiem-script-patching` if a script hook is needed).
- **Don't** make a standing-stone power something an NPC carries — these are player-exclusive
  blessings.

Not yet verified live: if a mod adds a genuinely new stone activator, confirm how `REQ_Quest_Birthsign`
registers/swaps blessings live before wiring a new one (the registration mechanism wasn't fully traced
here).
