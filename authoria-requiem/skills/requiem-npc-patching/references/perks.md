# The Perk Model (what to carry, what the Reqtificator adds)

Patching an NPC's perks correctly means knowing **which perks live on the source record** and which
the **Reqtificator stamps at build**. Get this wrong and you either fight the auto-pass (double perks)
or leave the NPC missing its combat kit. The split is verified live and against
`…\Reqtificator\Data\ActorAssignmentRules_Requiem.esp.conf`. Verify FormIDs live.

This file covers the model from the NPC side; the copy-the-analogue move below is the normal path.
When the *set itself* must be derived — no clean analogue, a custom gear mix, a hybrid caster, a
dedicated perk job, or the mod ships its own `PERK` records — load the `requiem-perk-assignment`
skill: it owns the equipment/spell-kit/tier derivation method and the mod-PERK disposition rule.

## Table of contents
- [The three perk sources](#the-three-perk-sources)
- [Source-carried: vanilla combat perks](#source-carried-vanilla-combat-perks)
- [Source-carried: Requiem skill-tree perks](#source-carried-requiem-skill-tree-perks)
- [Reqtificator-assigned: do NOT hand-stamp](#reqtificator-assigned-do-not-hand-stamp)
- [ActorEffect trait spells (not perks)](#actoreffect-trait-spells-not-perks)
- [Modded vs vanilla perk/spell — the rule](#modded-vs-vanilla-perkspell--the-rule)
- [How to apply perks](#how-to-apply-perks)

## The three perk sources

1. **Vanilla combat perks** — `Skyrim.esm` skill perks (Armsman, Juggernaut, Block, etc.). Requiem
   **places these on the NPC record itself**, escalating the count with the actor's tier. *Source-
   carried — replicate the analogue's set.*
2. **Requiem skill-tree perks** — `REQ_*` perks from `Requiem.esp` / WAR / Magic Redone
   (`REQ_OneHanded_HandToHand 0AD7A3`, `REQ_Conjuration_Empower_* 185736`, …). Appear on stronger
   actors and casters. *Source-carried — replicate the analogue's set.*
3. **Reqtificator-assigned perks** — the armor-penetration / weight / arrow-recovery perks (all
   actors), and the racial/state trait `incomingDamageModifier` perks (by race or keyword). *Added at
   build, ABSENT from source — never hand-stamp.*

Verified: Requiem's `EncDraugr02MissileHeadM00` carries **14 vanilla `Skyrim.esm` combat perks** and
**not** the draugr trait perk `031285` — the Reqtificator adds that one. A bandit carries vanilla
combat perks + one `REQ_*` perk; a guard ~17; a dragon priest 14 vanilla + `REQ_Conjuration_*`.

## Source-carried: vanilla combat perks

These are the bulk of an NPC's combat kit. Don't memorize the list — **read the comparable Requiem
actor of the same role/tier and copy its `Perks`** (the perk FormIDs are mostly `Skyrim.esm`, but
their EditorIDs resolve to `REQ_*` names — Requiem overhauls the vanilla perk records in place, so
**enemies wear the same perk tree the player earns**: bandit tier-2 carries
`REQ_OneHanded_WeaponMastery1/2`, `REQ_Block_ImprovedBlocking`, `REQ_HeavyArmor_Conditioning`…,
and a higher tier just sits deeper in the tree). The count escalates with tier (bandit tiers
sampled at 5 → 9 → 12; guard ~17). They cover the actor's weapon skill
(one-handed/two-handed/archery), armor (heavy/light), and block.

**The modded NPC's own lists — empty *or* populated — are not the verdict.** The derivation source
for perks and spells is the **analogue**, never the record you're patching. Two field failures, same
root:

- **Empty kit read as "nothing to patch."** Requiem's own low-tier sources ship empty (bandit `_Base`
  and `EncWolf` carry zero perks *and* zero `ActorEffect`; `Warlock01` has no perks; `Draugr01` has
  no spells — verified live 2026-07-17), and a modded combatant with no perks/spells "to begin with"
  is in exactly that state. It still gets the analogue's full kit for its role and tier — perks *and*
  castable spells *and* the tempering trait.
- **Sparse kit read as "already has perks."** A source record carrying a handful of mediocre vanilla
  perks (2, 3, 9 entries, none of them null) is not done — the analogue's kit escalates with tier
  (bandit 5/9/12, guard ~17, vampire tier-2 30), and the verdict on every combatant is the
  **comparison against that kit**: vanilla source perks are replaced by it (§ the modded-vs-vanilla
  rule below), modded ones kept and augmented. Leaving a 3-perk kit standing because it wasn't empty
  is the same failure as the empty-kit skip, one notch up.

## Source-carried: Requiem skill-tree perks

Stronger actors also carry Requiem's own perk-tree nodes — the same perks the player can take:

- **Combat** — `REQ_OneHanded_HandToHand 0AD7A3` (WAR), two-handed/archery/block nodes.
- **Magic (casters/bosses)** — `REQ_Conjuration_Empower_050_CognitiveFlexibility1 185736` (Magic
  Redone) and other school nodes; a caster boss gets empowered-casting perks here.

Copy the analogue's. A weak mob (Slighted class) has few or none; a boss has many.

## Reqtificator-assigned: do NOT hand-stamp

From `ActorAssignmentRules_Requiem.esp.conf`. These are assigned at build by condition — they are
**not** on source records, so adding them by hand double-stamps or fights the auto-pass.

- **`feature_gameMechanics` (every actor):** armor penetration by damage type
  (`AD394A`–`AD394E`, `AD3948`), `armorWeight AD3A34`, `arrowRecovery AD3A35`, magic/poison rescaling
  (`962798/962799`), stress perks (`703B25`, `6B9709`, `95FFFB`, `755649`). All `Requiem.esp`.
- **`feature_racialTraits` (by RACE FormID):** the `incomingDamageModifier` perk per creature race —
  draugr `031285`, skeleton `AD3A45`, dragon `AD3A62`, dragon priest `AD3A46`, atronachs
  `AD3A47/48/49`, dremora `AD3B27`, werewolf `AD3A43`, dwarven centurion `AD3A61`, lurker `AE3593`,
  seeker `AE3592`, … plus the Resist-and-Regen-Tweak **physique** taxonomy (fur `000805`, metal
  `000807`, chitin `000802`, stone `00080A`, zombie `00080D`, supernatural-undead `00080B`/dragon
  `000804`) in `Requiem - Resist and Regen Tweak.esp`. See `trait-bridge.md`.
- **`feature_stateTraits` (by KEYWORD):** ghost `RFTI_Trait_Ghost 031284` (kw
  `ActorTypeGhost 0D205E:Skyrim.esm` — a vanilla keyword carried on the **NPC record**; 236 vanilla
  carriers verified live, e.g. the MG07 ghosts and Soul Cairn souls), spirit `AD385B`
  (kw `spirit 10EAD7`), vampire `AD3A44` (kw `vampire 0A82BB`). A modded ghost/spectral actor
  missing the keyword gets the **keyword** added to its `Keywords` — that is the input the pass
  reads; hand-stamping the trait perk instead fights the auto-pass like any other
  Reqtificator-assigned perk.
- **`feature_playerExclusive` / `feature_npcExclusive`:** player-only skill perks + utility spells;
  NPC persistent-spell rescaling. Not your concern for a generic NPC.

`keywords.doNotInheritTraits AD3A4F` on a record opts it **out** of the racial/state trait pass — use
only to exclude a special actor with hand-tuned perks, never by default.

**The bridge:** the racial trait perk fires only for races on the shipped lists. A **recognized-race**
creature gets it automatically (don't stamp it). A **new-race** creature gets nothing → retarget its
`Race` or hand-add the physique perk (`trait-bridge.md`). This is the only case where you place a
trait perk by hand.

## ActorEffect trait spells (not perks)

Separate from perks, the NPC's `ActorEffect` holds source-carried Requiem ability spells:

- **`REQ_Trait_Tempering_<role>_<tier>`** — tempers the NPC's worn gear (bandit
  `…_Bandit_Heavy_Rank4 93369F`, guard `…_Guard_Soldier 929824`). Match the analogue's.
- **`REQ_Trait_ResistMagic30 ADDDC7`** and kin — resist traits on casters/bosses; reusable as a boss
  buff.

(The race's own trait spells — `REQ_Trait_Armor_*`, `Resist_*`, `Healing_*` — live on the **RACE**,
not the NPC; the engine inherits them. Don't copy those onto an NPC.)

## Modded vs vanilla perk/spell — the rule

- **Modded** (a perk/spell the mod defines, not in the base game): it usually drives a mod-specific
  mechanic → **never remove it; augment/patch it.** Don't add a redundant usable spell if the NPC
  already wields a modded one.
- **Vanilla** (a base-game perk/spell): **replace it** with the Requiem analogue — vanilla balance
  isn't preserved.

To tell them apart, check the perk/spell's defining plugin in its FormID (`…:Skyrim.esm`/DLC = vanilla;
`…:<ModPlugin>.esp` = modded). When unsure whether a modded perk is load-bearing, keep it and add
alongside.

## How to apply perks

`Perks` is a list of `PerkPlacement` (Perk + Rank). Replace the vanilla set with the analogue's, or
add the trait-bridge perk:

```
# replace combat perks wholesale with the analogue's set:
{formid:"<npc>", field_path:"Perks", verb:"ReplaceAll",
 values:[/* the analogue's PerkPlacement entries */]}

# add a single perk (e.g. the new-race physique perk) preserving the rest:
{formid:"<npc>", field_path:"Perks", verb:"Add", compose:{type:"PerkPlacement",
   sets:[{path:"Perk", value:"000805:Requiem - Resist and Regen Tweak.esp"},{path:"Rank", value:"1"}]}}
```

When you template onto a Requiem base (Workflow C), the base already carries the perks — don't also
hand-copy them. An already-templated Workflow A actor is a complete no-write skip: do not remove
masked local `REQ_NULL_*` perks or normalize its local list.

If the derived standalone result has zero perks, do not author `ReplaceAll` with an empty list. Leave
an already-empty `Perks` field unwritten; when a populated list genuinely must become empty, remove
its entries and verify xEdit shows the `Perks` list **and** `PRKZ - Perk Count` subrecord absent, not
`PRKZ = 0`. Treat a serialized zero count as an incomplete tool write, not a successful clear.
