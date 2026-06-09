# Diseases

Requiem diseases are **contractable staged ability spells**. A disease is a `Disease`-type SPEL,
`CastType=ConstantEffect`, `TargetType=Touch` (contracted on hit), whose single payload **MGEF**
carries the stages and progression logic. The SPEL is the carrier; the MGEF is where the staging
lives (often a small script on the MGEF — see `requiem-script-patching`'s `req-core-scripts.md`).

## The live disease set (Requiem.esp; some win via SunHelm patch)

| Disease | SPEL FormID |
|---|---|
| Ataxia | `0B877C:Skyrim.esm` |
| Bone Break Fever | `0B877E:Skyrim.esm` |
| Brain Rot | `0B877F:Skyrim.esm` |
| Rockjoint | `0B8782:Skyrim.esm` |
| Witbane | `0B8783:Skyrim.esm` |

(EditorIDs `REQ_Disease_Attack_<Name>`. In this load order several resolve to a SunHelm survival patch
that re-points the payload MGEF — read the live winner.)

## Resistance

Disease resistance is racial/material: Argonian, Bosmer, Redguard, Altmer ~50% disease resist; Vegan
food gives +40% disease resist; Falmer armor adds poison resist. These ride on race ability spells and
food/material effects — assigned, not stamped.

## Integration recipe

- **A new contractable disease** → design the staged MGEF and the `Touch` `Disease` ability SPEL via
  `requiem-magic-patching`, cloning a Requiem disease (`REQ_Disease_Attack_*`) for the stage structure
  and magnitudes. If the staging needs a script, reuse the disease MGEF's script pattern
  (`requiem-script-patching`) — don't reinvent staging.
- **A vector** (a creature whose attack infects, or an item that cures) → the attacking NPC carries the
  disease spell as a contact/ability effect (route the NPC via `requiem-npc-patching`); a cure is an
  ALCH/effect via `requiem-magic-patching` / `alchemy.md`.
- **Vampirism/lycanthropy contraction** (Sanguinare Vampiris, lycanthropy) follows the same
  staged-ability pattern; see `vampirism-lycanthropy.md`.

Not yet verified live: confirm the live payload-MGEF winner (base Requiem vs the SunHelm survival patch)
before cloning a disease, since the survival patch changes the stage effects.
