# The Race → NPC Trait Bridge (creatures of a new race)

This is the boundary the `requiem-race-patching` skill hands off (its references §G + `creature-
traits.md`). Read it there first; this file is the **NPC-side application**.

## The mechanism

A creature's combat toughness has two layers:

- **Layer A — trait SPELLS on the RACE's `ActorEffect`** (`REQ_Trait_Armor_*`, `Resist_*`,
  `Healing_*`). The engine gives every NPC of the race these automatically — they propagate to a new
  race's NPCs for free *once they're on the RACE*. **Nothing to do on the NPC.**
- **Layer B — the `incomingDamageModifier` PERK.** The Reqtificator adds this to each `NPC_` by
  matching the actor's **race FormID** against shipped lists at build. It is **not** inherited from
  the race and **not** on the source record.

So: a creature on a race the Reqtificator **recognizes** gets Layer B automatically — *don't touch
the NPC's perks.* A creature on a **new/unrecognized** race gets no Layer-B perk — that's the gap
this bridge fills.

## Recognized race → do nothing

If the creature's `Race` is in the Reqtificator lists (troll `013205`, draugr `000D53`, wolf
`01320A`, skeleton, atronachs, dragon, dragon priest, …), the auto-pass handles Layer B. Confirm the
NPC otherwise matches its comparable (fixed level, explicit Health/Stamina offsets, at most one combat
perk) and **leave the trait perk alone**. Requiem's own `EncWolf` is *identical to vanilla* for this
reason; `EncTroll` adds just one combat perk. Over-patching a recognized creature is the mistake.

## New race → one of two fixes

### Fix 1 — retarget the NPC's `Race` (cleanest)

Point the NPC at the nearest recognized Requiem race so the Reqtificator perks it automatically and
it inherits the race's trait spells:

```
housecarl_set_field formid="<creature npc>" into="Requiem NPC patching" \
  field_path="Race" value="013205:Skyrim.esm"   # e.g. TrollRace
```

Use this when the modded race is essentially a reskin and the vanilla race's skeleton/animations fit.
(If you instead patched the modded RACE in the `requiem-race-patching` skill to carry the trait spells, Layer A is
already covered; Fix 2 then supplies Layer B without changing the race.)

### Fix 2 — hand-add the physique perk

Keep the modded race, add the named `incomingDamageModifier` perk to its NPCs. Use the **Resist-and-
Regen-Tweak physique/supernatural taxonomy** (the live model — not base Requiem's per-category set):

| Build / origin | Perk | `Requiem - Resist and Regen Tweak.esp` |
|---|---|---|
| fur (bears, sabrecats, **trolls**, mammoth) | `000805` | |
| chitin (spiders, chaurus, mudcrab, ashhopper) | `000802` | |
| metal (dwarven automata) | `000807` | |
| stone (gargoyles) | `00080A` | |
| zombie (spectral draugr/warhound) | `00080D` | |
| ash | `000800` | |
| boneless (netch) | `000801` | |
| featherfall | `00080E` | |
| supernatural undead (durnehviir, keeper, mistman) | `00080B` | |
| supernatural dragon (skeletal dragon) | `000804` | |

Base Requiem per-category perks (`Requiem.esp`) are **additive** with these — e.g. a skeletal dragon
gets undead `AD3A45` *and* supernatural-dragon `000804`. Assign whatever the nearest analogue's NPCs
receive across both tables. Classify by **Name + Keywords**, never the EditorID.

```
housecarl_bulk_apply into="Requiem NPC patching" operations=[
  {formid:"<creature npc>", field_path:"Perks", verb:"Add", compose:{type:"PerkPlacement",
     sets:[{path:"Perk", value:"000805:Requiem - Resist and Regen Tweak.esp"},{path:"Rank", value:"1"}]}}  # physique.fur
]
```

Repeat the `Add` for each perk the analogue carries (e.g. a base-category perk + a physique perk).

## Which fix to choose

- **Many NPCs of one new race, race already patched (the race domain)** → Fix 2 in bulk (or just let the
  retarget happen if the `requiem-race-patching` skill pointed them at a known race).
- **A reskin that should just *be* a vanilla creature** → Fix 1 (`Race` retarget) — simplest, gets
  both layers.
- **A unique creature boss** → Fix 2 keeps its identity; pair with the boss buffs (`identification.md`).

This skill *applies* the bridge; the race skill *names* the classification + perk. Keep the two in
sync — if the `requiem-race-patching` skill chose an analogue, use the same one here.
