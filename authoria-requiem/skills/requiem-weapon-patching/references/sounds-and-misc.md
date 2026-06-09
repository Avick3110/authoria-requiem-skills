# Sounds, Impact Data & Inherited Misc Fields

## Sound and impact fields are FormID links — copy them from the comparable

A weapon record does not contain audio; it carries **links** to sound and impact records. Copy
these verbatim from the same-type comparable and the weapon sounds correct:

| Field | Steel Sword value | What it is |
|---|---|---|
| ImpactDataSet | 013CAC:Skyrim.esm | hit-impact set (melee sword default) |
| EquipSound | 03C72E:Skyrim.esm | draw/sheathe sound |
| BlockBashImpact | 0183FF:Skyrim.esm | block/bash impact |
| AlternateBlockMaterial | 0774C2:Skyrim.esm | block material variant |

Bows use a different impact set (the Steel Light Bow uses `ImpactDataSet = 0193B9:Skyrim.esm`).
Always copy the link the **same-type** comparable carries — a sword's impact set on a bow is wrong.

## Who owns the actual sound

The audible sound is overridden at the **sound-descriptor (SNDR) level**, not the weapon record,
by `Immersive Sounds - Compendium.esp` and its Requiem patch
`Requiem - Immersive Sounds Compendium.esp`. For most weapons that means: reuse the comparable's
sound/impact links and the ISC layer handles the rest automatically.

For **battleaxes and warhammers**, `Requiem - Immersive Sounds Compendium.esp` is the actual
conflict **winner** of the WEAP record (it edits the swing sound at the record level). When you
read such a weapon's winner with `conflict_tree=true` you'll see that plugin last in the chain —
that's expected and correct; you're reading the authoritative post-ISC values. You don't author
against ISC separately; reading the winner already includes it.

## USSEP misc data Requiem inherits

Requiem's records sit on top of `unofficial skyrim special edition patch.esp` (USSEP) and inherit
its corrections — fixed `ObjectBounds`, model/name fixes, and similar misc fields. The conflict
chain for a typical weapon is `Skyrim.esm → unofficial skyrim special edition patch.esp →
Requiem.esp → …`. Because you always read and replicate the **winner**, these USSEP fixes are
already folded in — do not treat USSEP as a balance authority, and do not strip fields that trace
to it. If you clone a comparable's full record as your starting point, the inherited misc data
comes along for free.

## Bow-specific Data fields

Beyond `Data.Flags = NPCsUseAmmo`, WAR's bow frames set:

```
Data.Skill = Archery
Data.BaseVATStoHitChance = 5
Data.RangeMin = 500
Data.RangeMax = 2000
Data.Stagger = 0
```

Copy these from the bow comparable; they govern NPC usage and projectile range and are not part
of the melee ladder.
