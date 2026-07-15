# The Reqtificator boundary & the write shapes

The three-lane split with its live proof, the forbidden lists, the rank mechanics, and copy-ready
houseCARL call shapes.

## Table of contents
- [The three lanes (who owns which perks)](#the-three-lanes-who-owns-which-perks)
- [The before/after proof](#the-beforeafter-proof)
- [Assign vanilla FormIDs — the override mechanism](#assign-vanilla-formids--the-override-mechanism)
- [Forbidden output (recognition patterns + FormIDs)](#forbidden-output-recognition-patterns--formids)
- [Rank mechanics and the write shape](#rank-mechanics-and-the-write-shape)
- [houseCARL recipes](#housecarl-recipes)

## The three lanes (who owns which perks)

| Lane | Recognition | Owner |
|---|---|---|
| **Player-tree perks** (combat/magic tree nodes) | `Playable=True`, `Hidden=False`, has a `Name`, usually a `NextPerk` chain | **This skill** — the only lane a patch writes |
| **Mechanics chassis** | `Hidden=True`, no `Name` or a "GM:" name, EditorID `RFTI_All_*` / `Nox_Perk_Mechanics_*` / `RFTI_Ench_*` / `RFTI_Trait_*` / playable-race | **Reqtificator** — stamped at build, absent from source |
| **Player-exclusive controllers** | EditorID `RFTI_Player_*` | **Reqtificator, player record only** — zero source NPCs carry any (verified by reverse lookup) |

The one sanctioned hand-placement in the Reqtificator's lane is the **new-race trait bridge**
(a creature whose race isn't on the shipped lists gets the physique perk by hand) — owned by
`requiem-npc-patching` / `requiem-race-patching`, not here.

## The before/after proof

`REQ_Bandit_Template_SwordShield_01 86837E:Requiem.esp`, read twice:

- **Source** (`plugin="Requiem.esp"`): **5 perks**, all player-tree — WeaponMastery1 `0BABE4`,
  WeaponMastery2 `079343`, ImprovedBlocking `0BCCAE`, Conditioning `0BCD2A`, HandToHand `0AD7A3`.
  Zero mechanics perks.
- **Live winner** (`Requiem for the Indifferent.esp`, the Reqtificator output): **50 perks** — the
  identical 5 plus 45 machine-stamped (all 6 armor-penetration GMs, ArrowRecovery, ArmorWeight,
  Poison/Absorb rescaling, 4 stress GMs, ward reduction, WAR mechanics, all 7 Nox school
  mechanics, all 10 playable-race perks, the artifact-enchant handlers, AV/power modifiers).

**Source 5 → build 50.** The patch writes the 5-perk lane; the +45 are the Reqtificator's stamp.
The same pattern holds on casters (tier-1 warlock: source *absent* → winner 45; tier-7 necro:
source 11 → winner 57 — purely additive). Corollary for **reading**: an RftI-won comparable's perk
list must be split before deriving — read the source version, or discard everything matching the
forbidden patterns. A few boss templates are source-won and show the opposite (mastery perks, no
chassis) — expect both shapes.

## Assign vanilla FormIDs — the override mechanism

Requiem overrides the vanilla player trees **in place**: of the 599 PERK records `Requiem.esp`
touches, 367 are Skyrim.esm + 67 DLC overrides, 162 are Requiem-defined. Proof read —
`0BABE4:Skyrim.esm`: vanilla `Armsman00` ("20% more damage", ×1.2) → live winner
`REQ_OneHanded_WeaponMastery1` ("Weapon Mastery", ×1.25, wins in WAR, override depth 4).
**Assign `0BABE4:Skyrim.esm` and the actor gets WAR's Weapon Mastery.**

So: reference the **origin FormID** (`…:Skyrim.esm` for the vanilla trees, `…:Requiem.esp` for
REQ-defined nodes like HandToHand/DaggerFocus/Lethality) and let the resolver deliver the winner.
Requiem's own new perks are themselves re-overridden downstream (winners spread across 17 plugins —
Magic Redone wins the magic trees, WAR the weapon trees); the origin FormID is stable through all
of it. **Never copy a REQ EditorID onto a fresh FormID** — that authors a parallel perk the
Reqtificator and the trees know nothing about.

## Forbidden output (recognition patterns + FormIDs)

Never in a composed `Perks` value. Recognition first (the families generalize), spot FormIDs for
verification:

- **`RFTI_All_*`** — armor penetration `AD394A`/`AD394B`/`AD3948`/`AD394C`/`AD394D`/`AD394E`;
  ArmorWeight `AD3A34`; ArrowRecovery `AD3A35`; Poison/Absorb rescaling `962798`/`962799`; stress
  line `6B9709`/`703B25`/`95FFFB`/`755649`; `RFTI_All_WAR_Mechanics 000853:…WAR.esp`;
  `RFTI_All_PlayableRace_*`; AV/power modifiers `0CF788`/`0A725C:Skyrim.esm`. All stamped on every
  actor at build.
- **`Nox_Perk_Mechanics_*`** (Magic Redone, 7 school-mechanics perks) — same.
- **`RFTI_Ench_*`** — artifact-enchant handlers — same.
- **`RFTI_Trait_*` racial/state traits** — draugr `031285`, and kin by race/keyword; plus the
  physique taxonomy in `Requiem - Resist and Regen Tweak.esp`. Reqtificator-assigned by
  race/keyword match (trait bridge exception above).
- **`RFTI_Player_*`** — Alchemy `AD3A2F`, Enchanting `AD3A30`, Lockpicking `0BCC58`, Pickpocket
  `AD3A31`, Sneak `AD3A51`, SneakAttack `AD3A2E`, Speech `AD3A2D`, Tempering `8C8ED3` (all
  `:Requiem.esp`), UnarmedMechanics `000A84:…WAR.esp`. Player record only, and the Reqtificator
  places them there.
- **`REQ_NULL_*`** — retired inert stubs. Never a comparable, never a target; strip on sight (the
  `requiem-patching` masters/REQ_NULL doctrine).
- **`_Mastery_` school gates** — assignable *records*, but never on an NPC (see
  `caster-perks.md`).

## Rank mechanics and the write shape

Requiem's multi-rank lines are **NextPerk chains of separate single-rank FormIDs**
(WeaponMastery1 `0BABE4` → WeaponMastery2 `079343`; DaggerFocus1 `AD399A` → 2 `AD3999` → 3
`AD3998`), each `NumRanks=1`. An NPC carries each rank as its **own `PerkPlacement`** with
`Rank=1` — the SwordShield bandit holds WeaponMastery1 *and* WeaponMastery2 as two entries.
**To grant "N ranks": one PerkPlacement per rank FormID up the chain, each Rank=1.** Never one
FormID at Rank 2+.

## houseCARL recipes

**Identity reads (derivation inputs):**

```
# the actor, everything that decides its perks:
housecarl_read_record formid="<npc>" depth=2 resolve_names=true \
  fields=["Name","Configuration","Class","CombatStyle","Race","DefaultOutfit","Items",
          "Perks","ActorEffect","Template"]

# the comparable's SOURCE-carried set (split from the build chassis):
housecarl_read_record formid="86837E:Requiem.esp" plugin="Requiem.esp" \
  depth=2 resolve_names=true fields=["Perks"]
```

**Find comparables and perk nodes:**

```
housecarl_cross_plugin_query plugins=["Requiem.esp"] type="NPC_" \
  editorid_contains="REQ_Bandit_Template_Bow"           # the weapon ladder
housecarl_cross_plugin_query type="PERK" editorid_contains="REQ_Marksmanship" \
  plugins=["Requiem - Weapons and Armor Redone.esp"]    # tree nodes by family
```

**Reverse lookups — use `references=`, not `where=`.** A FormLink field does not support the
`where=` equality test (measured: `where=["Class = <formid>"]` returns the unfiltered set).
"Who carries this perk/class" is:

```
housecarl_cross_plugin_query type="NPC_" plugins=["Requiem.esp"] references=["0AD7A3:Requiem.esp"]
```

**The write** — one PerkPlacement per rank FormID:

```
housecarl_bulk_apply into="Requiem perk assignment" operations=[
  {formid:"<npc>", field_path:"Perks", verb:"ReplaceAll", composes:[
    {type:"PerkPlacement", sets:[{path:"Perk", value:"0BABE4:Skyrim.esm"},{path:"Rank", value:"1"}]},
    {type:"PerkPlacement", sets:[{path:"Perk", value:"079343:Skyrim.esm"},{path:"Rank", value:"1"}]},
    {type:"PerkPlacement", sets:[{path:"Perk", value:"0BCCAE:Skyrim.esm"},{path:"Rank", value:"1"}]},
    {type:"PerkPlacement", sets:[{path:"Perk", value:"0BCD2A:Skyrim.esm"},{path:"Rank", value:"1"}]},
    {type:"PerkPlacement", sets:[{path:"Perk", value:"0AD7A3:Requiem.esp"},{path:"Rank", value:"1"}]}
  ]}
]
# augmenting a kept modded set instead: verb:"Add" with the same compose shape, one per perk.
```

**Verify:** read the override back (`housecarl_read_record` on the patch plugin's version) — the
`Perks` list holds exactly the derived set, `masters:` includes every referenced plugin
(`Requiem.esp`/WAR/Magic Redone auto-master in when their forms are referenced), and no forbidden
prefix or `REQ_NULL_*` appears anywhere in the list.
