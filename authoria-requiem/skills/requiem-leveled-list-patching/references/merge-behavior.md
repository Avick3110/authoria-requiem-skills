# Merge behavior — how the Reqtificator combines leveled lists

This is the underivable spec for the placement domain. It is transcribed from the live Reqtificator
source (`Source/Reqtificator/Reqtificator/Transformers/LeveledLists/` + `LeveledItems/` +
`EncounterZones/`) and its unit tests. Re-confirm against the source if the load order changes.

## Contents

- The two user toggles
- The merge algorithm (eligibility, trigger, the union/consensus rules)
- Why `verb="Add"` to the winner is correct either way (dual correctness)
- Compact lists (`*_CLI_`)
- The 255 cap
- REQ_NULL inside lists
- Reqtificator output lists (tempered quality) — don't author these
- Encounter zones — what `openEncounterZones` actually does

## The two user toggles (live `DefaultUserConfig.json`)

```json
{ "mergeLeveledLists": true, "mergeLeveledCharacters": true, "openEncounterZones": true, ... }
```

`mergeLeveledLists` governs LVLI, `mergeLeveledCharacters` governs LVLN. Both on. Containers (CONT) are
**not** merged — they resolve by normal conflict winner. If a user turned a toggle off, the merge below
simply doesn't run and the conflict winner is used as-is.

## The merge algorithm (`LeveledListMerging.cs`)

For each leveled list, at build time:

1. **Gather candidates.** Take every plugin version of the record, then keep only those from
   **`Requiem.esp` itself or a plugin that has `Requiem.esp` as a master** (`modsWithRequiemAsMaster`).
   A version from a plugin that does *not* master Requiem is **dropped from the merge entirely.**
   Master/child chains are de-duplicated (if both a mod and a patch that masters it edit the list, only
   the leaf is kept).

2. **Two guards — if either fails, the record is left as the plain conflict winner (no merge):**
   - `Requiem.esp` must itself define a version of the list (`baseVersion != null`). If Requiem never
     touched the list, no merge.
   - There must be **at least 3 candidates** (`toMerge.Count >= 3`) — i.e. Requiem **plus two or more**
     other eligible plugins. With only Requiem + one patch (count = 2), **no merge fires** and the
     load-order winner is used verbatim. (Test: `Should_not_change_a_record_if_there_are_not_enough_merge_candidates`.)

3. **Diff each non-Requiem version against Requiem's base.** Entry identity is the **whole tuple**
   `(Reference, Level, Count)` with multiplicity. Per version you get:
   - **Additions** — tuples present in the patch but not in Requiem's base.
   - **Modifications** — tuples whose multiplicity differs (a count delta).
   - **Deletions** — tuples in the base but absent from the patch.

4. **Combine across all versions:**
   - **Additions are unioned** — *every* contributor's additions are kept (`SelectMany`). This is the
     additive behavior you rely on. (Test: `Should_merge_additions_from_any_contributing_mods`.)
   - **Modifications and deletions require unanimous consensus** — a modification/removal is applied
     only if **every** eligible contributor made the same change (`All(m => m.Contains(c))`). One patch
     alone cannot remove or shrink a Requiem base entry. (Tests:
     `Should_merge_deletions_if_all_merge_candidates_apply_them`,
     `Should_not_merge_modifications_and_deletions_if_not_all_merge_candidates_apply_them`.)
   - Result = `base ∪ all-additions ∪ consensus-additions − consensus-removals`.

**Practitioner takeaway:** you can freely **ADD** entries; you cannot unilaterally **remove or change**
Requiem's entries through the merge. Placement is add-only by design — that is what protects Requiem's
curation from every contributing patch.

## Dual correctness — why `verb="Add"` to the winner always works

houseCARL `bulk_apply verb="Add" field_path="Entries"` overrides the **winning** record (which already
contains all of Requiem's curated entries) and appends your entry. So your emitted patch record is
**`Requiem-base + your entry`**. This is correct in both worlds:

- **Merge fires (≥3 contributors):** the Reqtificator diffs your version against Requiem's base, sees
  your one entry as an *addition*, and unions it in. Your copy of the base entries produces no diff, so
  nothing double-adds (additions only take tuples *not* already in the base).
- **Merge doesn't fire (<3, or you didn't master Requiem):** the plain conflict winner is used — and
  your winner is `base + your entry`, which is exactly what you want.

The failure mode this avoids: emitting a list that contains **only** your entry. If the merge doesn't
fire, that lone-entry record wins and **erases Requiem's whole curated list.** Always build on the
winner.

## Eligibility gotcha — master `Requiem.esp` (per plugin)

Eligibility is "Requiem or a plugin that has `Requiem.esp` in its `MAST` list," decided **per plugin**,
not per record. This has a sharp, verified edge:

- Requiem **renames** the core lists but most stay **defined in `Skyrim.esm`** (e.g.
  `REQ_LI_Loot_Weapon_Sword` is `016578:Skyrim.esm`). Overriding such a list and adding a **vanilla**
  item references only `Skyrim.esm`/`Update.esm`/`Dragonborn.esm` forms — so houseCARL does **not** add
  `Requiem.esp` as a master. Verified live: a patch doing exactly that came back with
  `masters: Skyrim.esm, Update.esm, Dragonborn.esm` — no Requiem. That patch is invisible to the merge,
  and its addition is **lost** when the merge rebuilds the list from the eligible contributors (the
  merge output loads last and wins).
- **Fix:** make the patch reference at least one **Requiem-defined** form. The reliable way is to also
  add your item to a `*:Requiem.esp`-defined pool — `REQ_LI_LootMidLevelContent 050EF1:Requiem.esp`, a
  `REQ_LI_TreasureHunter_<Theme>`, or a Requiem reward pool. Because masters are per-plugin, that single
  edit pulls `Requiem.esp` into the whole patch and makes **every** edit in it merge-eligible. Verified
  live: adding one `050EF1:Requiem.esp` edit flipped the patch's masters to
  `Skyrim.esm, Update.esm, Dragonborn.esm, Requiem.esp`.

There is no "add empty master" verb — the master follows from a real reference. Always confirm the
`masters:` read-back includes `Requiem.esp`. (See the masters/`REQ_NULL` rule carried by the
`requiem-patching` skill.)

## Compact lists (`*_CLI_`, `CompactLeveledItemUnroller.cs`)

A list whose EditorID matches `^[a-zA-Z0-9]+_CLI_` (e.g. `REQ_CLI_*`) is a **compact** list: the `Count`
field is repurposed as a **multiplicity** ("this entry, N times"), and the Reqtificator *unrolls* it
into N separate `Count = 1` entries before merging. Only lists from registered mods are unrolled. You
normally don't author `_CLI_` lists — just recognise that in them, `Count` means "how many copies,"
not "give the player N." Ordinary `REQ_LI_*` lists use the normal Skyrim meaning of `Count`.

## The 255 cap

A merged list is truncated to 255 entries with a log warning. Heavily-contested lists are already near
the cap; adding many copies wastes the budget. Add the minimum repetition needed for the tier.

## REQ_NULL inside lists (the nuance)

Requiem uses `REQ_NULL_*` two ways in this domain:
- **`REQ_NULL_*` sublists** — whole leveled lists Requiem retired (renamed from vanilla, e.g.
  `REQ_NULL_LootDraugrEnchWeapons100`, `REQ_NULL_SubCharBandit01Melee1H`). Kept so old references
  resolve.
- **`REQ_NULL_*` entries** — references to retired forms left inside a live list as a deliberate removal/
  placeholder marker.

The universal rule (the masters/`REQ_NULL` rule carried by the `requiem-patching` skill) is about **what
your patch introduces**: never
add an item to a `REQ_NULL_*` list, and never add a `REQ_NULL_*` reference. When you patch **additively**
you emit only your new entry, so there is nothing of Requiem's to strip — and you must **leave** its
intentional `REQ_NULL` removals in place (stripping them would re-introduce content Requiem deliberately
removed). The strip-everything action only applies if you fully **override** a list (rare) — then drop
`REQ_NULL` from the copy.

## Reqtificator output lists — don't author them (`TemperedItemGeneration.cs`)

A list named `<prefix>_..._Quality<tier>_<size H|N|D>_<dist fall|const|rise>` (e.g.
`REQ_LI_Weapon_SteelSword_Quality1_N_const`) is a **seed**: it holds a single entry, and the Reqtificator
expands it at build into many entries of the same item with different `ItemCondition` (tempering) values,
shaped by the size/distribution code. These are **outputs** — like the weapon damage-type keyword. Don't
hand-expand or replicate them. A new weapon gets into loot via the normal Loot/Town/faction lists; the
tempered-quality machinery is Requiem's internal mechanism. (Advanced: a mod can author one seed list and
register itself for the feature — out of scope for ordinary placement.)

## Encounter zones — what `openEncounterZones` actually does (`OpenCombatBoundaries.cs`)

`openEncounterZones = true` makes the Reqtificator set the **`DisableCombatBoundary`** flag on every
encounter zone at build (except zones listed in the `ClosedEncounterZones` FormList). That's all it does:
it lets combat cross zone borders. **It does NOT change `MinLevel`/`MaxLevel`.** Requiem's actual
de-leveling lives in the **fixed-level NPCs** (the NPC domain) and the **Level-1 leveled lists** — not the zone.

Consequences for placement:
- A new area's encounter zone almost never needs editing — the build pass opens it automatically.
- Requiem ships only ~8 ECZN overrides of its own (mostly DLC2 dungeon zones at `MinLevel = 25` with the
  `NeverResets` flag). Mirror that shape only if a modded zone genuinely needs it.
- If a modded zone hard-gates content with a high `MinLevel` that fights Requiem's flat design, you may
  set `MinLevel`/`MaxLevel` — but say why, and prefer fixing the NPCs' fixed level + list placement.
