---
name: requiem-perk-assignment
description: Assign the right Requiem perks to a new or modded NPC via houseCARL — derive the source-carried perk set (weapon mastery, block, heavy armor, evasion, magic-school nodes) from the actor's equipment, spell kit, and tier against Requiem's own template ladders, write it as rank-chain PerkPlacements, and disposition a mod's own PERK records. Use when the user wants to give an NPC or follower Requiem perks, fix a boss or enemy whose perks are missing or wrong, build a perk loadout for a custom warrior, archer, mage, or spellsword from its gear and spells, ask which perks an NPC should carry in Requiem, or handle a mod's custom perk records. Never hand-place Reqtificator-stamped perks — load this before writing any Perks list, not after the build pass double-stamps the actor.
---

# Requiem Perk Assignment

## Overview

This skill derives **which existing Requiem perks an NPC should carry** — from the actor's
equipment, spell kit, and power tier, matched against Requiem's own perk ladders — and dispositions
a mod's **own shipped PERK records**. The output is the `Perks` list of a direct ESP override (one
`PerkPlacement` per rank FormID), authored with houseCARL. It **never authors a new PERK record**:
the assignable space already exists, mostly as vanilla FormIDs whose live winners are Requiem's
re-authored perks.

The division of labor with the `requiem-npc-patching` skill: that skill owns the **whole NPC frame**
(level, flags, class, combat style, outfit, factions, traits) and its first perk move is "copy the
comparable's set." This skill is the **perk lane in depth** — where the comparable's set comes from,
how to *derive* one when no clean analogue exists (custom gear mixes, hybrid casters, modded
followers), and what to do with a mod's own PERK records. A whole-NPC job starts there; a perk
question lands here.

**The boundary that anchors everything: an NPC's perks come from three lanes with three owners,**
and only one lane is yours (proof and full lists in `references/boundary-and-write-shapes.md`):

1. **Source-carried player-tree perks** — the small hand-picked combat/magic set on the source
   record (a Requiem bandit template carries **5**; a tier-7 warlock **8**). **This is the only
   lane a patch writes.**
2. **Reqtificator-stamped mechanics** — the ~45-perk chassis (`RFTI_All_*`, `Nox_Perk_Mechanics_*`,
   `RFTI_Ench_*`, playable-race perks, racial/state traits) added identically to every actor at
   build. Source 5 → build 50, measured. **Never hand-place any of it** — the sole exception is the
   new-race trait bridge, which stays with `requiem-npc-patching`/`requiem-race-patching`.
3. **Player-exclusive perks** — the `RFTI_Player_*` skill controllers (alchemy, sneak, speech,
   tempering, …). Zero Requiem source NPCs carry any (verified by reverse lookup). Never on an NPC.

## First step

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; fix with `housecarl_set_mo2_instance` if not. Then
   read Iron Sword `012EB7:Skyrim.esm` `conflict_tree=true` → `Requiem.esp` must appear in the
   chain. The live winner is the authority to derive from. Full doctrine: the `requiem-patching`
   skill's `references/scope-and-authority.md`.

2. **Read the actor's identity signals — equipment first, class last:**

   ```
   housecarl_read_record formid="<npc>" depth=2 resolve_names=true \
     fields=["Name","Configuration","Class","CombatStyle","Race","DefaultOutfit","Items",
             "Perks","ActorEffect","Template"]
   ```

   What decides the perk set, in precedence order (evidence in
   `references/class-and-creatures.md`):
   - **Equipment** — every weapon in inventory/outfit (the set is the **union over the loadout**:
     Requiem's own archer carries One-Handed mastery for its sidearm), shield or not, heavy vs
     light armor.
   - **Spell kit** — the schools the actor actually casts and its highest spell tier
     (`ActorEffect` entries or the template's SpellList).
   - **Tier** — the intended power rank; it drives perk count and depth, and the class does not
     encode it.
   - **Class** — corroboration only. A CLAS record has **no perk field**; when class and equipment
     disagree, Requiem's own actors follow the equipment. On any `Lvl*`/template/placeholder actor
     the class is pure noise (4,229 records carry the vanilla Dremora class as filler).

3. **Derive from the comparable's SOURCE perk list — always scope the read.** Read the
   comparable's `Perks` with `plugin="Requiem.esp"` (or the defining addon) so you see the
   hand-picked source set, never the live winner's. A winner list on an RftI-won actor is ~80%
   machine-stamped chassis, and **prefix-filtering a winner list is not a safe substitute**: the
   build pass also re-carries tier perks that pass every prefix filter, which is how field runs have
   shipped 11–18-perk supersets the Reqtificator then double-stamps. The prefix screen (`RFTI_*`,
   `Nox_Perk_Mechanics_*`, `*GM_*`) is a last-resort cross-check when no source version exists at
   all — and that derivation gets flagged as unverified, not shipped silently.

## Bulk pass protocol (whole-plugin jobs)

Routed here from the `requiem-patching` skill, this skill owns one record type and one lane:

**The PERK queue.** Enumerate the mod's own perk records — this is the work queue, not a sample:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="PERK" defined_in=true \
  fields=["Name","Playable","Hidden","NextPerk","NumRanks"]
```

Every FormID gets a disposition per `references/mod-perk-disposition.md`:
**left alone** (Hidden runtime plumbing the mod's own scripts drive), **folded/rebalanced** (only
an obvious flat duplicate of a Requiem gate — rare, and say so), or **flagged to the user with a
concrete question** naming the overlap (the default for playable, balance-bearing perks). Close
with a reconciliation count — dispositioned = enumerated. An undispositioned PERK is the "entire
record types skipped" failure this protocol exists to kill.

**The NPC perk lane.** In a whole-plugin job the NPC enumeration and its reconciliation belong to
`requiem-npc-patching`'s bulk pass — its field gate already requires perks for a record to count
patched. This skill defines what "perks correct" means per record. When the job is perk-only
("give this mod's NPCs proper perks"), run that skill's enumeration discipline over `type="NPC_"`
yourself: every combatant dispositioned, perk sets derived per record, patched + skipped =
enumerated.

**Don't extrapolate across a uniform-looking family.** The perk-domain family shapes that tempt
one read applied to the whole group:

- **template tier ladders** — per-weapon variation is real inside them: the axe and battleaxe
  ladders add `REQ_HeavyArmor_PowerOfTheCombatant` and reach weapon-Focus rank 3 where sword and
  greatsword stop at rank 2; derive from the *matching weapon's* ladder, not a sibling's;
- **same-class NPC families** — one class spans tiers 01–06 with different perk sets per tier;
- **same-prefix mod perks** (`WB_*`, `_pRB_*`) — most are plumbing, but the one playable
  balance-bearing outlier is exactly what the disposition pass exists to catch;
- **vanilla EditorID weapon roles** — Requiem re-tasked some (`EncBandit06Melee2H*` carries a
  sword-and-board kit); trust the record's own Class + Outfit + Perks, never the name.

## Workflow

### 1 — Pick the comparable ladder

Match the actor to Requiem's own perk source by archetype (full maps with FormIDs in
`references/equipment-perk-map.md` and `references/caster-perks.md`):

- **Warrior/ranged humanoid** → the purpose-built `REQ_Bandit_Template_<Weapon>_<Base|01..06>`
  ladders (84 records, weapon-explicit, single-plugin — the canonical source). Pick the matching
  weapon ladder at the matching tier and read its source perks. The vanilla `Enc*` encounter
  templates work too but split their perks across Requiem.esp + Minor-Arcana addons.
- **Caster** → the `EncWarlockNNTemplate<School>` ladder (tiers 1–7 per school), dragon priests
  and vampire-Magic templates at boss tier.
- **Hybrid** (spellsword, Forsworn-style, vampire melee-caster) → a hybrid comparable; the melee
  family and the school family **stack — neither is dropped**.
- **Creature** → match by **race × tier**, not by the individual carried weapon: Requiem's
  creature perk sets are deliberate supersets spanning weapons the record doesn't equip (a skeleton
  carries both 1H and 2H focus lines), or trait-only kits (giant, troll). Copy the whole shape from
  the same-race same-tier comparable. Detail: `references/class-and-creatures.md`.

### 2 — Derive the set

**Warrior formula** (read out of the tier-06 matrix, `references/equipment-perk-map.md`):

> perk set = **[weapon-skill line]** (1H → `REQ_OneHanded_*` with the weapon's Focus family;
> 2H → `REQ_TwoHanded_*`; bow/crossbow → `REQ_Marksmanship_*`)
> **+ [defence line]** (shield **or** 2H weapon → Block tree; heavy armor → Heavy Armor tree,
> no Evasion; light/no-shield → Evasion tree + Lethality, no Heavy Armor)
> **+ the universal floor** (WeaponMastery1/2, HandToHand — even archers carry mastery for the
> sidearm), **at the depth of the actor's tier** (counts run ~5 at tier 01 → ~18 at tier 06,
> strictly additive up the ladder).

Bow vs crossbow differ structurally (QuickShot + Evasion vs RapidReload + Block); dual-wielders
carry **all three** 1H Focus lines + Flurry and the light shape. Archers add the ranged trait
perks (`REQ_Trait_Damage140_Bow`, armor-penetration) that melee never gets.

**Caster formula** (`references/caster-perks.md`):

> perk set = **school damage/utility nodes matching the schools the actor actually casts**, at the
> threshold matching its **highest spell tier** (casts up to Fireball → Pyromancy1; up to
> Incinerate → Pyromancy1+2) **+ the shared caster survivability set** (ImprovedHealing, Respite,
> MageArmor proxy, MagicResistance at the tier's threshold, Stability, ImprovedWards).
> **NPCs never carry the `REQ_<School>_Mastery_*` gate perks** — those are player-only spell
> unlocks; NPCs cast freely and the Reqtificator's persistent-spell rescaling balances them.

A multi-school caster gets both trees (a necromancer casting frost carries Cryomancy **and**
Necromancy nodes). Perks follow the **actual spell kit**, not the template's element — Requiem's
own Morokei is a shock-template priest with a fire kit and Pyromancy perks.

### 3 — Write the perks

Rank lines are **NextPerk chains of separate single-rank FormIDs** — an NPC with "two ranks of
Weapon Mastery" carries two `PerkPlacement` entries (`0BABE4` and `079343`), each `Rank=1`, never
one FormID at Rank 2. Reference the **origin FormID** (usually `…:Skyrim.esm`) — the live winner
delivers Requiem's re-authored perk (assigning vanilla Armsman `0BABE4:Skyrim.esm` lands WAR's 25%
Weapon Mastery). Copy-ready call shapes, the verify read-back, and the reverse-lookup caveat
(`references=`, not `where=["Class = …"]`, for "who carries this perk/class"):
`references/boundary-and-write-shapes.md`.

```
housecarl_bulk_apply into="Requiem perk assignment" operations=[
  {formid:"<npc>", field_path:"Perks", verb:"ReplaceAll", values:[/* one PerkPlacement per rank
     FormID from the derived set — see boundary-and-write-shapes.md for the compose shape */]}
]
```

`ReplaceAll` when the vanilla set is being replaced wholesale; `Add` composes when augmenting a
kept modded set. The modded-vs-vanilla rule is `requiem-npc-patching`'s: a **modded** perk drives a
mod mechanic — keep it and add alongside; a **vanilla** perk is replaced by the derived Requiem set.

### 4 — Disposition the mod's own PERK records

Walk the PERK queue (bulk protocol above) with the three-way rule — full calibration cases in
`references/mod-perk-disposition.md`:

- **Hidden + prefix-family + no Name** → the mod's own runtime plumbing (spell-mod internals,
  nemesis systems). Its scripts/MGEFs place it. **Leave it alone** — and never hand-place it on an
  NPC either.
- **Playable + Named + a balance magnitude that parallels a Requiem gate** → **flag to the user
  with the specific overlap named** ("+25% health regen parallels Requiem's standing-stone
  economy — fold, keep, or neutralize?"). Don't silently rebalance.
- **An obvious flat duplicate of a Requiem player-tree node with no mod mechanic** → fold onto the
  Requiem perk (retarget references, neutralize the duplicate). Rare — state that you did it.

### 5 — Route what isn't yours

- **The whole NPC frame** (level, class, style, outfit, stats, traits, factions) →
  `requiem-npc-patching`. This skill hands back a perk set; it doesn't set the frame.
- **The new-race trait bridge** (creature of an unrecognized race needs the physique perk) →
  `requiem-npc-patching`/`requiem-race-patching` own it; it is the one sanctioned hand-placement
  in the Reqtificator's lane.
- **A perk's *effect* design** (a mod perk whose entry points need re-authoring) →
  `requiem-magic-patching` for magic-side entry points; otherwise flag — this skill assigns and
  dispositions, it doesn't re-author PERK internals.
- **Follower perk scaling via script** → `requiem-script-patching`.

## Judgment

- **Class is corroboration, never source.** Use its skill weights at most to sanity-check the
  archetype bucket you read from equipment. When they disagree, the equipment is right — and on
  templated/placeholder actors the class is junk by construction. If both equipment and class are
  ambiguous (a robed actor with no spells, an empty inventory), say what you checked and ask.
- **Tier honestly, then depth follows.** The single biggest derivation input is the intended power
  tier. An under-tiered perk set makes a "boss" die like a mob; over-tiering a roadside bandit
  breaks the ladder the other way. Read the comparable at the actor's intended rank — level bands
  are family-specific, so match by tier index, not absolute level.
- **Deliberate under-perking exists.** Requiem's hagraven carries 2 mastery perks at level 35 —
  natural-weapon casters lean on claws and a small kit. Don't "fix" a lean comparable to a formula;
  match the archetype's real shape.
- **AutoCalc division of labor.** Class + AutoCalcStats drive skill *values*; perks are an
  independent placement. Requiem's hand-perked actors run AutoCalc **off** with a derived-attributes
  trait — on those, class drives nothing at all. Don't conflate the two systems when reading a
  comparable.
- **A perk list you didn't split is not evidence.** Winner lists on RftI-won actors mix 5 source
  perks with 45 stamped ones; a few source-won boss templates show the opposite (mastery perks, no
  chassis). Always establish which version you're reading before deriving.

## Common mistakes

- **Hand-stamping the Reqtificator's chassis** — any `RFTI_All_*`, `Nox_Perk_Mechanics_*`,
  `RFTI_Ench_*`, playable-race, or racial/state trait perk (draugr `031285`, …). They are absent
  from source records and stamped at build; hand-placing double-stamps or fights the pass.
- **Placing a player-exclusive perk on an NPC** (`RFTI_Player_*` — alchemy, sneak, speech,
  tempering, unarmed mechanics). Zero Requiem source NPCs carry any.
- **Giving an NPC caster the `_Mastery_` gate perks.** Those gate *player* spell learning; NPCs
  cast without them.
- **One PerkPlacement at Rank 2+** for a multi-rank line. Requiem's rank lines are separate
  FormIDs; carry each rank as its own entry at Rank 1.
- **Re-authoring a Requiem perk under a new FormID.** The space exists — assign the origin FormID
  and let the winner deliver Requiem's version.
- **Deriving from a live winner list at all — even prefix-filtered.** The filter catches the named
  chassis families but not every build-re-carried tier perk; the source-scoped read
  (`plugin="Requiem.esp"` / the defining addon) is the derivation input, and its absence is a flag,
  not a licence to filter.
- **Trusting a vanilla EditorID's weapon role** over the record's own Class/Outfit/Perks.
- **Tailoring a creature's perks to its one equipped weapon.** Requiem creatures carry race×tier
  supersets; copy the shape, don't trim it.
- **Silently rebalancing a mod's playable perk.** Unless it's an obvious flat duplicate, the
  disposition is a flagged question, not a quiet edit.
- **Carrying a `REQ_NULL_*` perk reference.** Inert retired stubs (`REQ_NULL_ExtraDamage25` sits on
  vanilla-left draugr); never a comparable, never a target — strip per the `requiem-patching`
  masters/REQ_NULL doctrine.

## Checklist

Before finishing, confirm:

- [ ] **Whole-plugin job:** every mod PERK record dispositioned (left alone / folded / flagged with
      a question) and the count reconciles; NPC perk work rides `requiem-npc-patching`'s
      enumeration gate (or this skill's own sweep on a perk-only job); no family extrapolation.
- [ ] **Derivation inputs read on this record:** full weapon union, armor class, shield, spell kit
      + highest spell tier, intended tier; class used only as corroboration.
- [ ] **Comparable read at the matching tier, source-scoped** — the perk set derived from the
      comparable's `plugin="Requiem.esp"`/defining-addon version, never from a live winner list
      (prefix-filtered included); a no-source derivation flagged, not silent.
- [ ] **Set shape correct for the archetype:** warrior weapon+defence+floor at tier depth; caster
      school nodes at spell-tier threshold + survivability set, no `_Mastery_` gates; hybrid stacks
      both; creature race×tier superset or trait-only kit.
- [ ] **No forbidden perk in the write:** no `RFTI_All_*`/`RFTI_Player_*`/`Nox_Perk_Mechanics_*`/
      `RFTI_Ench_*`/racial-state trait/`REQ_NULL_*` FormID anywhere in the composed `Perks` values.
- [ ] **Write shape:** one PerkPlacement per rank FormID, `Rank=1`, origin FormIDs referenced;
      modded perks kept/augmented, vanilla ones replaced.
- [ ] **Every carried FormID resolve-verified** — `housecarl_resolve` each perk FormID before the
      write; the classic slip is the right FormID under the wrong master suffix (perk lists render
      opaque below `depth=3`, which invites transcription slips).
- [ ] **Masters correct** on the read-back (houseCARL Add+Sorts from referenced forms; a Requiem
      perk reference pulls `Requiem.esp`/the addon in as master — verify the `masters:` line).
- [ ] Routed onward: NPC frame → `requiem-npc-patching`; trait bridge → npc/race skills; perk
      entry-point design → `requiem-magic-patching` or a flag; scripted scaling →
      `requiem-script-patching`.

## Notes

- **Authority** = houseCARL's live conflict winner, with one domain-specific reading rule: for
  *deriving* a source-carried set, the source record (Requiem.esp / the defining addon) is the
  clean signal, because the live NPC winner folds in the build chassis. For *perk records
  themselves* the live winner is authority as everywhere (WAR/Magic Redone win most of the trees).
- **Perk records to assign are read-only inputs.** This skill writes NPC `Perks` lists and (rarely)
  fold-dispositions on mod PERKs; it never edits Requiem's own PERK records to balance an actor.
- Whole-mod jobs arrive via `requiem-patching`; whole-NPC jobs via `requiem-npc-patching`, whose
  `references/perks.md` carries the same three-lane model from the NPC side.
- houseCARL writes go to a new patch plugin; the live load order is never modified.
