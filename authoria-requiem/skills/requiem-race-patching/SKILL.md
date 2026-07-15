---
name: requiem-race-patching
description: Patch a new or modded playable or creature race for Requiem via houseCARL — derive racial stats (health/magicka/stamina, regeneration, carry weight, unarmed damage), skill bonuses, racial ability spells, keywords, and the creature trait classification from Requiem's own comparable race, and emit a direct ESP override the Reqtificator can then rebalance. Use when the user wants to patch a race for Requiem, balance a custom playable or follower race, fix a modded creature or beast race that takes the wrong damage or has no Requiem traits, set racial abilities, resistances, or skill bonuses, give a creature natural armor or combat healing, or handle a new race the Reqtificator does not recognize. Load this before touching a RACE record, not after the Reqtificator skips the race it can't see.
---

# Requiem Race Patching

## Overview

This skill patches a `RACE` record so it is consistent with Requiem — the right starting
health/magicka/stamina, regeneration, carry weight and unarmed damage; the skill bonuses, racial
ability spells, and keywords Requiem expects; and, for creatures, the correct trait classification.
The output is a **direct ESP override** authored with houseCARL `set_field` / `bulk_apply` /
`create_record`, because the Reqtificator's auto-balance pass runs over real records.

Races split into **two sub-domains, and classifying which one you have is the first real decision:**

- **Playable / humanoid races** — the 10 vanilla races and custom playable/follower-race mods. A
  modded humanoid race is patched to become **a near-copy of the vanilla Requiem race it most
  represents** (stats, skill bonuses, ability spells, keywords).
- **Creature races** — animals, undead, daedra, dragons, dwarven automata, were-beasts. A modded
  creature is **classified to its nearest Requiem creature** and given that creature's trait spells
  and damage profile.

The method is **live-analogy, never hardcoded numbers.** The hard part is *identification* — deciding
humanoid vs creature and which vanilla race the mod represents. That needs judgment (see `## Judgment`);
once you have the analogue, you read its live winner and replicate.

**Two delivery layers carry a race's Requiem behaviour — know which is which:**

1. **Trait SPELLS on the RACE's `ActorEffect`.** The engine gives every NPC of a race the race's
   `ActorEffect` spells automatically, so these **propagate to a new race's NPCs for free** once you
   put them on the RACE. This is where resistances, natural armor, combat healing, and the playable
   Heritage/Blood/Cuisine abilities live.
2. **The `incomingDamageModifier` PERK**, which the **Reqtificator assigns to each NPC_ record** by
   matching the actor's race FormID against lists it ships with. This is **not** inherited from the
   RACE and a brand-new race isn't in those lists — so a new race's NPCs won't get it from the
   auto-pass. This is the central gap the skill fills (see `## Judgment`).

What this skill does **not** do: design the MGEF/effect on a trait or racial spell (route to the
`requiem-magic-patching` skill — here you only link existing spells), and apply the per-NPC trait perk
to individual actors (route to the `requiem-npc-patching` skill — here you *name* the classification +
perk). Set the RACE frame here; route those onward.

## First step

Confirm authority is fresh, note the race-domain authority, then classify what you're patching.

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; if it is the wrong instance, fix it with
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. Then sanity-check Requiem is present:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   `Requiem.esp` must appear in the override chain. Either winner is valid: `Requiem.esp`
   (authoring-style profile, generated overlay disabled) or `Requiem for the Indifferent.esp` /
   a later patch (live profile — the normal consumer state). The live winner is the authority to
   derive from; **never** re-point houseCARL because the Reqtificator's output wins. Full
   doctrine: the `requiem-patching` skill's `references/scope-and-authority.md`.

2. **Race-domain authority (important).** For `RACE` records read the **live winner** as the
   comparable. The hand-authored race layers are `Requiem - Races Redone.esp` and its patches
   (`Requiem - Food and Beverages Redone - Races Redone.esp`,
   `Requiem - Resist and Regen Tweak.esp`, USMP), often consolidated by a race merge where the
   setup has one (the author's instance uses `Authoria - Master Patch - Races Merge.esp`, which
   folds all of those in while carrying Requiem's trait spells faithfully). A race whose winner is
   a merge/compatibility patch downstream of Races Redone is **expected and correct** — do *not*
   re-point to escape it. Never derive from the dated standalone knockdown tweak, which drops some
   of Requiem's trait spells.

3. **Classify — humanoid vs creature.** Read the modded race and decide from, in order of reliability:
   - **Keywords** — `ActorTypeNPC 013794` → humanoid; `ActorTypeCreature 013795` /
     `ActorTypeAnimal 013798` / `ActorTypeTroll`, `ActorTypeUndead`, `ActorTypeDaedra`, … → creature.
   - **Which NPCs use the race** (`cross_plugin_query type="NPC_" references="<race>"`) — a follower
     mod's race used by humanoid NPCs is humanoid.
   - **Name** and the **`SkeletalModel`** path (a skeleton under `actors\troll\…` or `actors\draugr\…`
     is the strongest creature hint). The skeleton/model path can be a *red herring* — a Khajiit
     furstock can ship a Bosmer-shaped model yet still be a Khajiit; weigh the name + lore over the mesh.

   Then check the **`Playable` flag** in `Flags`: **checked** → a player-selectable race (the
   player-balance lens applies — see Workflow A step 3); **unchecked** → an NPC-only humanoid race.
   Either way a humanoid is patched to its analogue; the flag only sharpens *why* you strip custom
   powers. Then pick the **vanilla Requiem analogue** — the race the mod is *meant to be* — read its
   Master-Patch winner, and branch to `## Workflow` A (humanoid) or B (creature).

## Bulk pass protocol (whole-plugin jobs)

When you're patching a whole plugin — routed here from the `requiem-patching` skill, or any job with
more than a handful of races — the enumeration **is the work queue**, not a sample of it. Race packs
hide their divergent record inside a uniform-looking family: the one furstock re-tiered off its
siblings, the vampire counterpart carrying its own trait spell, the "cosmetic" race that still ships a
full stat block. Those are exactly what a rebalance pass exists to catch.

Open with the enumeration — one call, the whole plugin at once, and it doesn't write. Its fields are
the triage matrix that drives the humanoid-vs-creature classification (First step §3) per row:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="RACE" \
  fields=["Keywords","Flags","Starting","ActorEffect"]
```

Read `Keywords` per row to classify (`ActorTypeNPC 013794` → humanoid → Workflow A;
`ActorTypeCreature`/`ActorTypeAnimal`/`ActorTypeTroll`… → creature → Workflow B), and read
`Starting`/`ActorEffect`/`Flags` to see what balance-bearing content the record actually carries.

**Every RACE record gets a disposition** — patched (note which workflow) or skipped (note a
**per-record** reason). The only valid skip reason is that **no balance-bearing field differs from
this record's vanilla or Requiem base, verified on that record** — none of `Starting` stats, regen,
`UnarmedDamage`, `ActorEffect` abilities, `SkillBoost`s, resistances, or keywords. A skip is earned by
a field comparison on that record, never inherited from a neighbour.

**Visual, gimmick, reskin, and chargen-only races are not an exemption bin.** A race's cosmetic purpose
does not exempt it — it still carries the same balance-bearing fields (a reskinned Nord still has a stat
block, skill boosts, and an ability bundle that must match the analogue). The skip decision is per
record by field comparison, never per category by label. This is the field failure this protocol closes:
excluding "just a visual race" by unstated judgment ships it on non-Requiem stats.

**`<Race>RaceVampire` counterparts enumerate as their own rows.** Each vampire variant is its own RACE
record carrying its own `REQ_Trait_Vampire_<Race>` spell, so it gets its own disposition — patched
against the analogue's *vampire* record, or skipped by its own field comparison. Never fold it in as a
rider on the base-race patch; a base race that's field-complete does not make its vampire counterpart so.

**Patched means field-complete, not merely touched.** A record counts as *patched* only when the
per-record field **Checklist** (below) passes for it — every balance-bearing field brought onto the
analogue's standard. Touching one field and moving on leaves it half-patched, which the reconciliation
count won't surface unless "patched" is held to the checklist.

Close the pass with a **reconciliation count — patched + skipped = enumerated.** If the two sides don't
add up, a record fell through; find it before you call the type done. **Flags fields are unions, not
scalars:** a RACE `Flags` write replaces the whole bitfield, so read the winner's flags first and write
original-bits + your change (the Workflow B example's `Flags` value is that union, not a fresh set) —
a literal Set silently strips `Playable`, `Walks`, `Swims`, and their kin.

**Never extrapolate across same-prefix race variants or playable/vampire pairs.** A run of
`…FurstockRace0#`, a `<Race>Race`/`<Race>RaceVampire` pair, or a troll family *look* uniform — but the
divergent sibling is the whole point of the pass. Read each variant's **own** record: the frost troll
(`TrollFrostRace 013206`) carries **H750** where the base troll (`TrollRace 013205`) carries **H300**,
with its own per-variant resist and healing traits (`Resist_TrollFrost`/`Healing_TrollFrost`, not the
base troll's). The trait *structure* transfers across the family; the *numbers and per-variant spells*
must be read off the sibling, never inferred from it. Reading the outlier costs one query; missing it
ships a race on the wrong stats.

## Workflow

Real call shapes below; the full copy-ready set is in `references/housecarl-recipes.md`, and
end-to-end records (a modded creature, a creature round-trip, a playable round-trip) are worked in
`references/worked-examples.md`.

### A — Humanoid race (playable or NPC)

A modded humanoid race becomes a **faithful, full copy of its vanilla analogue** — match *everything*
to the analogue: starting stats, regen, carry weight, unarmed damage, **skill boosts**, ability
spells, and keywords. Don't preserve the mod's own stat block or skill spread as a "sensible author
value" — for races the analogue *is* the standard, so normalize fully (that caution belongs to
weapons/armor, where a wrong edit breaks crafting or balance). Requiem's per-race standard is in
`references/playable-races.md`.

1. **Read the analogue's winner** (e.g. Nord):

   ```
   housecarl_read_record formid="013746:Skyrim.esm" conflict_tree=true depth=2 \
     fields=["Starting","Regen","BaseCarryWeight","UnarmedDamage","ActorEffect","Keywords","Flags",
             "SkillBoost0","SkillBoost1","SkillBoost2","SkillBoost3","SkillBoost4","SkillBoost5","SkillBoost6"]
   ```

   Use the **live winner**, not `documentation\Races.md` — the doc drifts (it lists Nord 120/80/100;
   the live winner is 110/80/110). The doc is a cross-check; the live read decides.

2. **Replicate onto the modded race.** Set `Starting` (Health/Magicka/Stamina), `Regen`,
   `BaseCarryWeight`, `UnarmedDamage`, the seven `SkillBoost0..6` (each a `.Skill` ActorValue +
   `.Boost`; an unused slot is `Skill=255`/None), `Keywords`, `Flags`, and — for playable — the
   race-menu `Description`.

3. **Copy the ability spells — as a full replacement, not an addition.** The race's abilities ride on
   `ActorEffect` spells (inherited by every member of the race, **including the player**): the universal
   `REQ_Trait_NoHealthRegeneration 609AF0` + `REQ_Trait_MassEffect 82CC14`, plus the analogue's
   `REQ_Trait_Heritage_<Race>` (named racial perks), `REQ_Trait_Blood_<Race>_*` (resistances),
   `REQ_Trait_Physique_*` (beast races: Argonian/Khajiit natural armor/claws), and
   `REQ_Trait_Cuisine_<Race>` (diet). **`ReplaceAll` the `ActorEffect` with the analogue's exact list**
   (do the same for the vampire variant against the analogue's vampire record) — for a *playable* race,
   any bespoke spell the mod left on it (a free heal, a summon, a custom vampire power) becomes a **free
   player ability the moment the player is that race** — a balance break. Mirror the analogue wholesale;
   don't preserve the mod's powers. **And never carry a `REQ_NULL_*` record** (see Common mistakes).

4. **The playable-race-perk caveat.** Requiem also has an `RFTI_All_PlayableRace_<Race>` perk whose
   bonuses are **per-effect `GetIsRace`-gated**, so a *new* race's FormID won't match it — its
   mechanics come from the cloned `ActorEffect` spells, not the perk. If the race must keep its own
   FormID *and* you need the perk bonuses, that's a perk-condition edit; usually mirroring the
   analogue's spells is enough. State which you did.

5. **ARMA race-add (so it can wear gear).** Cleanest: set the new race's `ArmorRace` to the vanilla
   analogue, so every ArmorAddon that already covers the analogue covers the new race too. The
   exhaustive alternative is adding the race FormID to each relevant ARMA's `AdditionalRaces` list.

### B — Creature race

A creature is patched to be **a near-copy of its Requiem analogue** — like the humanoid branch, you
match *everything* to the analogue, not just one trait. **Every creature maps:** classify to the
nearest Requiem creature, and when nothing fits, fall back to the **Elite Draugr** (the most basic
undead) — never leave a creature unpatched.

1. **Classify to the nearest Requiem creature** (troll, draugr, skeleton, atronach, dwarven automaton,
   spider, gargoyle, lurker, were-beast, …) — **by Name + Keywords, never the EditorID**, which is
   often a recycled/misleading stub (a race called `…KannimarcoRace` whose Name is "MudCrab" is a
   mudcrab). Use `ActorType*` keywords, the Name, the skeleton path, and what the creature obviously is.
   Fallback when genuinely unclear: **Elite Draugr** (`DraugrRace 000D53` + `REQ_Trait_Armor_Draugr_Elite
   03280F`). The full race → trait-category → perk table is in `references/creature-traits.md`.

2. **Read the analogue's full record and copy it** — `Starting` (Health/Magicka/Stamina), `Regen`,
   `UnarmedDamage`, the `SkillBoost` slots (constructs/atronachs carry `Illusion 95` = fear/illusion
   immunity; draugr `Destruction 5`/`Illusion 35`; gargoyle `Sneak 10`), and the analogue's Requiem
   keywords (below). **Patch the creature's attack data by setting `AttackRace` = the analogue** — the
   single-field way to make it knock down / poison / stagger like the Requiem creature (these mod
   creatures reuse the vanilla skeleton, so the analogue's attacks fire). Then set the `ActorEffect`
   traits:

   - **Add `REQ_Trait_Armor_<analogue>`** (natural armor vs physical) — the one thing almost every
     creature needs.
   - **Add `REQ_Trait_Resist_<analogue>` only if the modded race ships no resistances of its own.**
     Modded creature races commonly carry their own resistance abilities (often `Ab*`-named, e.g.
     `AbResistFrost`) — **keep those, don't replace them; just add armor.**
   - **Add `REQ_Trait_Healing_<analogue>` if the analogue regenerates in combat — and tick the
     `RegenHpInCombat` flag.** The flag is what makes a Healing trait actually fire; a Healing spell
     without it does nothing.
   - **Never add `REQ_Trait_FX_*`** (visual-effect spells) to a modded race — they don't take them
     well.
   - **Don't duplicate** a spit/breath/cloak the modded race already has. If it has its own, leave it
     and patch *that* to match Requiem later (the `requiem-magic-patching` skill).

   ```
   # full copy onto a modded troll-type (analogue = troll 013205):
   housecarl_bulk_apply into="Authoria_Races" operations=[
     {formid:"<modded race>", field_path:"Starting", verb:"Set", key:"Health",  value:"300"},
     {formid:"<modded race>", field_path:"Starting", verb:"Set", key:"Stamina", value:"500"},
     {formid:"<modded race>", field_path:"Regen",    verb:"Set", key:"Health",  value:"2"},
     {formid:"<modded race>", field_path:"UnarmedDamage", value:"60"},
     {formid:"<modded race>", field_path:"AttackRace",    value:"013205:Skyrim.esm"},      # attack data
     {formid:"<modded race>", field_path:"Keywords",  verb:"Add", value:"586728:Requiem.esp"}, # DropsBlood
     {formid:"<modded race>", field_path:"Keywords",  verb:"Add", value:"5F367F:Requiem.esp"}, # KnockdownImmunity
     {formid:"<modded race>", field_path:"ActorEffect", verb:"Add", value:"AD39E6:Requiem.esp"}, # Armor_Troll
     {formid:"<modded race>", field_path:"ActorEffect", verb:"Add", value:"AE3AED:Requiem.esp"}, # Healing_Troll
     {formid:"<modded race>", field_path:"Flags", value:"Walks, NoCombatInWater, RegenHpInCombat, UseAdvancedAvoidance"}  # UNION: winner's bits + RegenHpInCombat — never just the bits named here
   ]
   ```

3. **Keywords.** Carry the analogue's `ActorType*`, plus `REQ_DropsBloodKeyword 586728` (if it
   bleeds) and `REQ_MinorKnockdownImmunity 5F367F` (if the analogue has it). `doNotInheritTraits AD3A4F`
   opts a record *out* of the Reqtificator's per-NPC trait pass — use it only to exclude, never by default.
   The full race-keyword vocabulary is in `references/keywords.md`.

4. **Attacks.** If the modded creature has special attacks (knockdown/poison live on
   `Attacks[i].AttackData.Spell` + `Stagger`), preserve them and match Requiem's profile rather than
   adding a second one.

5. **Layer B — name the trait perk for the `requiem-npc-patching` skill.** The `incomingDamageModifier` perk (e.g. troll
   → `physique.fur` → perk `000805`) is assigned by the **Reqtificator to NPC_ records** by race-FormID
   match, so a new race's NPCs won't receive it automatically. Resolve it one of two ways:
   - **Template the creature's NPCs onto a known Requiem race** (they then inherit everything), or
   - **Hand-add the named perk to the creature's NPCs** — this is the bridge into the
     **`requiem-npc-patching` skill**. The race skill *names* the classification + perk; the
     `requiem-npc-patching` skill *applies* it per-actor.

## Judgment

The ladder-work is easy; the judgment is identification and the new-race gap.

### Humanoid vs creature, and which analogue

Decide humanoid vs creature from keywords → NPC users → name/skeleton (above). Then choose the
vanilla analogue the mod is *meant to be*: a new elf-like follower race → the closest mer race; a new
troll-type → the troll; a reskinned draugr → the draugr. The skeleton path and the race's own
resistance abilities are strong hints. **This can't be automated — when it's genuinely ambiguous,
say what you checked and ask** rather than guessing (a wrong analogue gives a creature the wrong
resistances). When you can't find a clean analogue, say so explicitly.

### The new-race-not-recognized problem (the central gap)

The Reqtificator keys racial traits off FormID lists it ships with, so a brand-new race gets nothing
from the auto-pass. The trait **spells** you put on the RACE still propagate to its NPCs (the engine
does that), but the **`incomingDamageModifier` perk** does not. Three strategies, by situation:

- **Replicate a vanilla race** (playable) — make the modded race a faithful copy of its analogue;
  the cloned `ActorEffect` spells deliver its abilities.
- **Template the NPCs onto a known race** (creature) — point the creature's NPCs at a vanilla
  Requiem race so they inherit both layers.
- **Hand-assign the trait perk per-NPC** (creature) — keep the modded race but add the named
  category perk to its NPCs in the `requiem-npc-patching` skill.

### Normalize fully to the analogue; keep only abilities, asymmetrically

For races, **the analogue is the standard** — match the modded race's stats, regen, carry, unarmed
damage, and **skill boosts** to it in full. (The item-domain "don't change a sensible author value
unprovoked" caution is a guard against breaking crafting/balance on a *weapon or armor*; it does
**not** apply to race stat/skill normalization — a Khajiit furstock should be a Khajiit, skill boosts
included.) What you *do* weigh case-by-case is the **abilities**, and that rule is **asymmetric by who
benefits**:

- **Creature race:** keep its own `Ab*` resistance abilities (add armor, don't replace) — the player
  isn't the one gaining them.
- **Playable race:** **do not** keep the mod's custom power spells. A free heal / summon / vampire
  power on a playable race is a free *player* ability — strip it and `ReplaceAll` the `ActorEffect`
  with the analogue's exact bundle. Make a playable race a true copy of its analogue (and its vampire
  variant a copy of the analogue's vampire), not a superset.

**Never keep or add a `REQ_NULL_*` record** of any kind (see Common mistakes). The **live Master-Patch
winner is authority over `Races.md`** when they disagree.

## Common mistakes

- **Keeping (or adding) a `REQ_NULL_*` record.** Requiem NULLs out records it retired — e.g.
  `REQ_NULL_RaceKhajiitClaws 0AA01E`, which Requiem strips from Khajiit. They're inert stubs only
  resolvable when Requiem is a master, and carrying one does nothing useful. **Never keep or add
  anything whose EditorID starts with `REQ_NULL`** — strip them when you replicate a race. (If a spell
  on a modded race won't resolve, suspect a `REQ_NULL_*` — you can only see the name with Requiem as
  master.)
- **Preserving a playable race's custom power spells.** On a *playable* race, a bespoke spell the mod
  left on it (a free heal, a summon, a custom vampire power) becomes a **free player ability** — a
  balance break, worst of all on the vampire variant the player triggers at will. For playable races,
  `ReplaceAll` the `ActorEffect` with the analogue's exact bundle; don't preserve the mod's powers.
- **Under-patching a creature to only the armor trait.** A creature needs the *whole* analogue copied
  — `Starting` stats, `Regen`, `UnarmedDamage`, skill boosts, the Requiem keywords, and the attack data
  (`AttackRace`) — not just `REQ_Trait_Armor_*`. Armor alone leaves it on its modded (non-Requiem) stats.
- **Classifying by the EditorID.** EditorIDs in creature packs are recycled/misleading (`zdcdKannimarcoRace`
  = a MudCrab). Classify by **Name + Keywords**; leave nothing unmapped (Elite-Draugr fallback).
- **Adding `REQ_Trait_FX_*` to a modded race.** These visual-effect spells misbehave on non-vanilla
  races — never attach them.
- **Stamping the Layer-B `incomingDamageModifier` perk on the RACE.** It lives on NPC_ records and is
  Reqtificator-assigned; putting it on the RACE does nothing.
- **A Healing trait without `RegenHpInCombat`.** The flag is what fires the combat regen; the spell
  alone is inert.
- **Replacing a creature's own `Ab*` resistances** instead of just adding `REQ_Trait_Armor_*`.
- **Trusting `Races.md` over the live winner.** The doc drifts; read the live winner.
- **Re-pointing because the race winner is a merge patch or the Reqtificator's output.** The live
  winner *is* the authority — a merge downstream of `Requiem - Races Redone.esp`, or
  `Requiem for the Indifferent.esp` on a live profile, is the healthy state; never
  `set_mo2_instance` to escape it.
- **Duplicating a spit/breath the modded race already has** instead of patching its own to match.
- **Forgetting the ARMA race-add** — a new playable race then can't wear any armor.

## Checklist

Before finishing a race override, confirm:

- [ ] **Whole-plugin job:** every enumerated RACE dispositioned — patched (the field checklist below
      passed for it) or skipped with a per-record field-comparison reason (visual/gimmick is not a
      reason); `<Race>RaceVampire` counterparts dispositioned as their own rows; counts reconcile
      (patched + skipped = enumerated); no variant-pair extrapolation.
- [ ] **Stats** on the analogue's standard: `Starting` (H/M/S), `Regen`, `BaseCarryWeight`,
      `UnarmedDamage` (`UnarmedReach` matters only for humanoids).
- [ ] **Skill bonuses** (playable) — the six/seven `SkillBoost` entries match the analogue.
- [ ] **Ability spells** in `ActorEffect` — playable: Heritage/Blood/(Physique)/(Cuisine) + universal
      NoHealthRegeneration + MassEffect; creature: Armor (+ Resist if it has none of its own) (+ Healing).
- [ ] **`RegenHpInCombat` flag** set whenever a Healing trait is present.
- [ ] **Flags written as unions** — every RACE `Flags` write carries the winner's original bits plus
      your change; no bit silently dropped.
- [ ] **Keywords** — `ActorType*`, `REQ_DropsBloodKeyword`/`REQ_MinorKnockdownImmunity` as the
      analogue carries; `doNotInheritTraits` only to opt a record out.
- [ ] **Creature**: trait-category + Layer-B perk **named** for the `requiem-npc-patching` skill's pass.
- [ ] **Playable**: ARMA race-add (`ArmorRace` delegation or `AdditionalRaces`) + race-menu `Description`.
- [ ] **`Requiem.esp` is a master** of the patch (auto when a Requiem trait spell/keyword is referenced)
      — masters list correct + load-order-sorted (verify the `masters:` read-back).
- [ ] **No `REQ_NULL_*` reference remains** in any patched field (strip or replace with the live form) —
      see Common mistakes; race packs commonly carry retired `REQ_NULL_*` stubs.
- [ ] **Per-actor trait-perk application** routed to the `requiem-npc-patching` skill; effect/spell *design* to the `requiem-magic-patching` skill.

## Notes

- **Authority** = houseCARL's live conflict winner. The hand-authored race layers are
  `Requiem - Races Redone.esp` plus `Requiem - Food and Beverages Redone - Races Redone.esp`,
  `Requiem - Resist and Regen Tweak.esp`, the knockdown attack-data, and USMP — often consolidated
  by a race merge (the author's instance uses `Authoria - Master Patch - Races Merge.esp`). Read
  the live winner and you fold them all in.
- **`Requiem - Resist and Regen Tweak.esp`** owns the live `Resist_`/`Armor_`/`Healing_` trait spells
  and the **physique/supernatural** Layer-B perk taxonomy — see `references/creature-traits.md`.
- **Spell/MGEF effect design** → the `requiem-magic-patching` skill. **Per-NPC trait perks** → the
  `requiem-npc-patching` skill. This skill sets the RACE frame and names the creature classification.
- houseCARL writes go to a new patch plugin; the live load order is never modified. The patch is later
  run through the Reqtificator, which applies the auto-balance pass over it.
