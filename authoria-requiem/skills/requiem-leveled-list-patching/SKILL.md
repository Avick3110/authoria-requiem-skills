---
name: requiem-leveled-list-patching
description: Place a new or modded item or NPC into Requiem's world via houseCARL — add it to the right leveled list, container, or encounter zone at the correct tier, leaning on the Reqtificator's leveled-list merge so your entry joins Requiem's curated distribution instead of overwriting it. Use when the user wants to add an item to a Requiem leveled list, make modded loot or gear actually appear in the world, distribute a new enemy or creature spawn, fill or curate a container for Requiem, de-level a dungeon or encounter zone, or fix new gear that never shows up in game. Load this before editing any leveled list or container, not after the loot spawns at the wrong level or never drops at all.
---

# Requiem Leveled List Patching

## Overview

This skill is the **placement and distribution** layer. The item skills (`requiem-weapon-patching`,
`requiem-armor-patching`, `requiem-ammo-patching`) and the actor skill (`requiem-npc-patching`)
produce a correctly *statted* record; this skill makes that record actually **appear in the world** —
in dungeon loot, on enemies, in vendor stock, inside containers, and as spawns. It edits four record
types: `LeveledItem` (LVLI), `LeveledNpc`/LeveledCharacter (LVLN), `Container` (CONT), and
`EncounterZone` (ECZN). It **links** already-statted records into lists; it does **not** re-stat them.

The output is a **direct ESP override** authored with houseCARL `bulk_apply` / `set_field`, because the
Reqtificator's build pass operates on real records. Two things make this domain different from the item
domains, and both come straight from the Reqtificator's source (`Transformers/LeveledLists/`):

1. **The Reqtificator MERGES leveled lists** (`mergeLeveledLists` + `mergeLeveledCharacters` are on).
   So you **add** your entries and let the merge union them with Requiem's curated list, rather than
   rewriting the list. See `references/merge-behavior.md` for the exact algorithm — it has two hard
   gotchas (the patch must master `Requiem.esp`, and the merge only fires with ≥3 contributors).
2. **Requiem de-levels by distribution, not by the `Level` field.** Almost every Requiem list entry is
   `Level = 1`; the *tier* of an item is encoded by **which list** it sits in and by **how many times**
   it is repeated, not by a level gate. Putting a daedric sword in at `Level = 1` is correct — that is
   how Requiem does it. See `references/list-structure.md`.

## First step

Confirm authority is fresh, then identify what you are placing and where.

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; if it is the wrong instance, fix it with
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. Then sanity-check Requiem is present:

   ```
   housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true
   ```

   `Requiem.esp` must appear in the override chain. Either winner is valid: `Requiem.esp`
   (authoring-style profile, generated overlay disabled) or `Requiem for the Indifferent.esp` /
   a later patch (live profile — the normal consumer state; the Reqtificator output also carries
   the leveled-list merges). The live winner is the authority to derive from; **never** re-point
   houseCARL because the Reqtificator's output wins. Full doctrine: the `requiem-patching`
   skill's `references/scope-and-authority.md`.

2. **Identify the subject and the goal.** You are placing an *existing* record (already statted by an
   earlier phase). Decide which of the four jobs this is:
   - **Item into loot/vendor/reward lists** → LVLI, the `## Workflow` additive-add loop.
   - **NPC/creature into spawn lists** → LVLN, same additive loop (entries are NPC refs, not items).
   - **Fill or curate a container** → CONT, `## Containers` in `references/containers-and-zones.md`.
   - **De-level / open a new area's encounter zone** → ECZN, `## Encounter zones` (usually nothing to
     do — read the section before touching it).

3. **Find where Requiem already places the comparable.** The decisive move is a reverse-reference: ask
   which Requiem lists already contain the closest comparable item/NPC, then add yours to the same ones.

   ```
   housecarl_cross_plugin_query type="LeveledItem" references="<comparable item FormID>" plugins=["Requiem.esp"]
   ```

   A steel sword, for example, resolves into `REQ_LI_Loot_Weapon_Sword`, `REQ_LI_Town_Weapon_Sword`,
   `REQ_LI_Blacksmith_Weapon_Sword`, the faction pools (`LItemBanditSword`, `LItemVampireSword`), and
   the reward/special pools — that set *is* the placement plan for a new steel-tier sword.

## Workflow — add an entry to a leveled list

### 1 — Pick the right list(s) from the comparable

Don't guess list membership — derive it from the reverse-reference in step 3. Requiem's distribution
backbone is named `REQ_LI_{Loot|Town|Best|Blacksmith|Special}_{Weapon|Heavy|Light}_{Subtype}`
(full taxonomy in `references/list-structure.md`):

- **Loot** — dungeon/enemy drops (broad, ~20 entries spanning all materials).
- **Town** — vendor stock (narrow, ~3 entries, low-tier basics only).
- **Best** — the high-end pool used where strong gear belongs.
- **Blacksmith** — smith vendor stock. **Faction pools** (`LItemBandit*`, `LItemVampire*`, …) — themed
  drops on a given enemy type (these keep vanilla editorids; Requiem overrode their contents).

Add your item to the **same families** the comparable lives in. A craftable steel-tier sword wants
Loot + Town + Blacksmith + the relevant faction pools; a rare artifact-tier item wants Best or a reward
pool, not Town.

### 2 — Tier by repetition + list choice, NOT by Level

Read the comparable's entries to see how Requiem weights it (every entry is `Level = 1`):

```
housecarl_read_record formid="016578:Skyrim.esm" fields=["Entries[0].Data.Level","Entries[0].Data.Count","Entries[0].Data.Reference"]
```

A common low-tier item appears **multiple times** in a Loot list (iron sword 3×); a rare high-tier item
appears **once**. To place your item at "steel tier," add it the same number of times the steel
comparable appears, at `Level = 1`, `Count = 1`. The level field stays 1 — tier is the repetition and
the list. (See `references/list-structure.md` for why this is Requiem's whole de-leveling model.)

### 3 — Add additively (the merge does the rest)

Use `verb="Add"` against the list's winner. houseCARL overrides the winning record (which already holds
all of Requiem's curated entries) and appends yours — so the emitted patch is *base + your entry*. This
is correct **whether or not** the Reqtificator merge fires (see `references/merge-behavior.md`):

```
housecarl_bulk_apply into="Requiem leveled list patching" operations=[
  {formid:"016578:Skyrim.esm", field_path:"Entries", verb:"Add",
   compose:{type:"LeveledItemEntry", sets:[
     {path:"Data.Level", value:"1"},
     {path:"Data.Count", value:"1"},
     {path:"Data.Reference", value:"<your item FormID>"}]}}
]
```

For an NPC spawn list it is identical except the type is `LeveledNpcEntry` and `Data.Reference` is the
NPC's FormID. Repeat the op (or add several) to weight a common item higher. Copy-ready shapes,
including the LVLN form and a container fill, are in `references/housecarl-recipes.md`.

### 4 — Master Requiem, verify the read-back

The merge **ignores any patch that does not master `Requiem.esp`** — eligibility is **per plugin**.
This trips people: most of Requiem's core lists are *renamed but still defined in `Skyrim.esm`*
(`REQ_LI_Loot_Weapon_Sword` is `016578:Skyrim.esm`), so overriding one and adding a *vanilla* item
does **not** auto-master Requiem (verified live: such a patch's masters came back
`Skyrim.esm, Update.esm, Dragonborn.esm` — no Requiem). A non-mastering patch is dropped from the
merge, and its addition can be **lost** when the merge rebuilds from the eligible contributors.

Fix it by ensuring the patch references at least one **Requiem-defined** form — easiest is to also add
your item to a `*:Requiem.esp` pool, e.g. `REQ_LI_LootMidLevelContent 050EF1:Requiem.esp` or a Requiem
reward/treasure-hunter pool. Because masters are per-plugin, one such edit pulls `Requiem.esp` into the
whole patch and makes every edit in it eligible (verified: adding that one edit flipped the patch's
masters to include `Requiem.esp`). Confirm the `masters:` read-back, then re-read the list and confirm
your entry is present. Details in `references/merge-behavior.md` and the masters/`REQ_NULL` rule carried by
the `requiem-patching` skill.

## Judgment

- **Which lists, and how rare.** The reverse-reference tells you the *set* of lists; you still decide
  rarity. Match the comparable's repetition for an item of the same tier; for a genuine artifact or
  quest reward, use the reward/special/Best pools (or hand-placement) and a single low-weight entry, not
  the common Loot/Town spread. State the call and why.

- **Containers reference lists, not raw gear.** Requiem curates a boss chest down to ~10 entries, each a
  **reference to a themed leveled list** (`LootDraugrGems15`, `LootDraugrPotions25`, …) plus its own
  generic pools (`REQ_LI_LootMidLevelContent`, `REQ_LI_TreasureHunter_<Theme>`), weighted by repetition.
  Gear comes from the enemy's death-item/outfit, not stuffed into the chest. Fill a new container the
  same way — reference the curated lists, don't dump items. **CONT is not in the merge** (only LVLI/LVLN
  are): a container override fully replaces its `Items`, so build on the winner and keep its contents.
  See `references/containers-and-zones.md`.

- **Encounter zones — usually leave them.** Requiem's de-leveling is the fixed-level NPCs (the NPC domain) plus
  the Level-1 lists, **not** the encounter zone. The Reqtificator's `openEncounterZones` pass only sets
  the `DisableCombatBoundary` flag at build (it does **not** change min/max level), and it does this to
  every zone automatically. So a new area's ECZN normally needs **nothing**. Only touch `MinLevel` if a
  modded zone hard-gates content in a way that fights Requiem's flat design, and say why.

- **REQ_NULL inside lists — leave Requiem's, never add your own.** Requiem deliberately keeps
  `REQ_NULL_*` entries and retired `REQ_NULL_*` sublists inside its lists as a removal/placeholder
  mechanism. When you patch **additively** you only emit your new entry, so there is nothing to strip —
  and you must **not** strip Requiem's intentional `REQ_NULL` removals. The universal "no `REQ_NULL`"
  rule applies to what *you* introduce: never add an item to a `REQ_NULL_*` list and never add a
  `REQ_NULL_*` reference. Only if you must fully override a list do you strip `REQ_NULL` from the copy.
  See `references/merge-behavior.md`.

- **Don't author the Reqtificator's output lists.** Lists named `<prefix>_..._Quality<N>_<H/N/D>_<dist>`
  are tempered-quality lists the Reqtificator **generates** at build from a single seed entry — they are
  outputs, like the weapon damage-type keyword. Don't hand-expand or hand-author them. (Advanced: a mod
  can seed one and register itself for the feature — out of scope for normal placement.)

## Common mistakes

- **Emitting a bare list with only your entry.** If the merge doesn't fire (fewer than 3 Requiem-
  mastering contributors), the conflict *winner* — your patch — is used verbatim, wiping Requiem's
  curation. Always `Add` to the winner so your record is *base + addition*, never a lone entry.
- **Not mastering `Requiem.esp`.** A patch without Requiem as a master is invisible to the merge and its
  addition can be dropped. The trap: the core `REQ_LI_*` lists are renamed but still `Skyrim.esm`-defined,
  so adding a vanilla item to one does **not** auto-master Requiem. Reference a Requiem-defined form
  (e.g. also add to `REQ_LI_LootMidLevelContent 050EF1:Requiem.esp`) and verify the `masters:` read-back.
- **Setting a real level on the entry.** Requiem lists are `Level = 1` across the board; a level gate
  breaks the curated distribution. Encode tier by repetition + list choice.
- **Re-stating the item here.** This skill links an already-statted record. Damage/AR/keywords belong to
  the item skills (`requiem-weapon-patching`/`requiem-armor-patching`/`requiem-ammo-patching`) — set them
  there, place them here.
- **Stuffing gear directly into a container.** Reference the themed Requiem leveled lists; raw items
  bypass Requiem's curation and over-fill the chest.
- **Stripping Requiem's `REQ_NULL` list entries**, or **adding an item to a `REQ_NULL_*` list.** Leave
  Requiem's; never introduce your own.
- **Touching an encounter zone's level to "de-level" it.** That's not where Requiem's de-leveling lives;
  the build pass handles the zone. Fix the NPCs' fixed level and the list placement instead.

## Checklist

Before finishing a placement, confirm:

- [ ] **Right list family** chosen from the comparable's reverse-reference (Loot/Town/Best/Blacksmith/
      faction/reward), matching the item's intended availability.
- [ ] **`Level = 1`, `Count = 1`**, repeated to match the comparable's tier weighting.
- [ ] **Additive `Add` to the winner** (record = base + your entry), not a bare rewritten list.
- [ ] **`Requiem.esp` is a master** (reference a Requiem form; verify the `masters:` read-back).
- [ ] **No `REQ_NULL_*` introduced**; Requiem's own `REQ_NULL` entries left intact.
- [ ] **Containers** reference themed leveled lists (built on the winner's `Items`), not raw gear.
- [ ] **Encounter zone** left alone unless a real level-gate conflict demands a `MinLevel` fix.
- [ ] **Linked, not re-statted** — the item/NPC record itself is unchanged here.
- [ ] **Read-back verified** — re-read each list/container and confirm your entry is present.

## Notes

- **Authority** = houseCARL's live conflict winner. The Reqtificator merges leveled lists into
  `Requiem for the Indifferent.esp`, so on a live profile it wins most lists and shows the list
  as actually played — the merge folds in. The hand-authored layers beneath it are mostly
  `Requiem.esp` and `Requiem - Weapons and Armor Redone.esp` (WAR), with Requiem addons
  (`Requiem - Minor Arcana - *`, MR) winning their themed pools. Independent overhauls that do
  **not** master Requiem (LegacyoftheDragonborn, trade & barter, Sons of Skyrim) win some lists by
  load order but are invisible to the Reqtificator merge — read the live chain per record; verify,
  don't assume.
- **houseCARL writes directly into active patches via `into=`**; verify a brand-new patch via the
  `bulk_apply` read-back until a `housecarl_set_mo2_instance` refresh. The `where=` filter helps scans.
- This skill places already-statted records. **Item stats** → `requiem-weapon-patching` /
  `requiem-armor-patching` / `requiem-ammo-patching`; **NPC balance** → `requiem-npc-patching`;
  **spell/MGEF design** → the `requiem-magic-patching` skill; **script hooks** → the `requiem-script-patching` skill.
- **Worked end-to-end placements** — a steel-tier sword, a creature spawn, and a boss container, plus a
  round-trip proof — are in `references/worked-examples.md`.
