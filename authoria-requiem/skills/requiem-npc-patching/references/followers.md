# Followers

A follower is a recruitable companion. Two parts: the **record-side** balance + faction setup (in
scope for this skill) and the **runtime registration** that wires the follower into Requiem's
follower system (script-side → route to the `requiem-script-patching` skill). Verify FormIDs live.

## Record-side setup (in scope)

Derive from a Requiem-patched vanilla follower (housecarl Lydia `HousecarlWhiterun 0A2C8E`, whose
live balance winner is `USMP - Requiem.esp` carrying Requiem's values). The follower-defining fields:

- **Level — keep `PCLevelMult`.** Unlike every other combatant, a follower **scales with the player**:
  `Configuration.Level` is a `PcLevelMult` with `CalcMinLevel`/`CalcMaxLevel` (Lydia 6 / 50). **Do not
  de-level a follower or strip the multiplier.**
- **Flags** — `Unique` + `Protected` (a follower shouldn't die permanently to a stray hit, but isn't
  immortal like `Essential`), `AutoCalcStats` on. Add `Female`/`OppositeGenderAnims` as the actor is.
- **Factions (the registration record-side):**

  | Faction | FormID | Rank | Meaning |
  |---|---|---|---|
  | `CurrentFollowerFaction` | `05C84D:Skyrim.esm` | 0 | follower system membership |
  | `PotentialFollowerFaction` | `05C84E:Skyrim.esm` | **-1** | recruitable, not yet recruited |

  Lydia also carries her housecarl + city factions (`HousecarlWhiterunFaction 0A2C8D`, etc.) — leave
  the mod's own faction membership; just ensure the two follower factions are present at the right
  ranks. These are the **record-side exception** to "don't patch factions".
- **Class + CombatStyle** — the follower's combat role class (Lydia uses a vanilla warrior class) +
  matching style; or a `REQ_Class_*` if you're normalizing it to a Requiem role.
- **Perks** — combat perks like any humanoid (Lydia: 5 vanilla + 2 Requiem follower perks
  `270B76`/`270B77`); copy a comparable Requiem follower's set.
- **DefaultOutfit** — the Requiem follower outfit (Lydia `99B7FF:Requiem.esp`).

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<follower>", field_path:"Configuration.Flags", value:"AutoCalcStats, Unique, Protected"},  # keep PcLevelMult level
  {formid:"<follower>", field_path:"Factions", verb:"Add", compose:{type:"RankPlacement",
     sets:[{path:"Faction", value:"05C84E:Skyrim.esm"},{path:"Rank", value:"-1"}]}},                  # PotentialFollower
  {formid:"<follower>", field_path:"Factions", verb:"Add", compose:{type:"RankPlacement",
     sets:[{path:"Faction", value:"05C84D:Skyrim.esm"},{path:"Rank", value:"0"}]}},                   # CurrentFollower
  {formid:"<follower>", field_path:"Class",        value:"<role class>"},
  {formid:"<follower>", field_path:"DefaultOutfit",value:"<REQ follower outfit>"}
]
```

Do **not** set `PCLevelMult`→fixed here. The de-level rule is for non-followers.

## Runtime registration is script-side → the `requiem-script-patching` skill

Requiem registers *modded* followers into its follower/affinity system through a **script** mechanism,
not a record override. The live evidence: the mod `Authoria - Modded Follower Requiem Registration` is
**enabled but contributes no active plugin** (`load_order_status` shows it as a mod with no esp in the
load order) and ships `Scripts/` + `Source/` Papyrus — i.e. the registration runs in Papyrus/SPID, not
on the `NPC_` record.

So this skill does the **record-side** part (factions, level scheme, class, outfit, perks) and **flags
the runtime registration for the `requiem-script-patching` skill** when a modded follower needs the full Requiem follower
behavior (affinity, dialogue hooks, the registration quest). Don't try to reproduce that on the record
— say it needs the script component and route it onward.
