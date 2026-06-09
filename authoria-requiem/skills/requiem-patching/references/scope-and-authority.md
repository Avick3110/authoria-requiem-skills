# Scope & Authority

*Read this before any record read. It defines what "Requiem's authoritative value" means for this project and how to make houseCARL report it correctly.*

## The MO2 instance

- houseCARL must be pointed at **your Requiem MO2 instance** (the author's was `D:\Wabbajack\Authoria-dev`, profile *Authoria - Requiem Reforged - Main Profile*).
- The study source is this live load order. (The reference corpus was mined on the author's Authoria instance.)
- After pointing, houseCARL sees **~2841 active plugins** (not 3410). If you see ~3410 active, the resolver is stale — see "Refresh" below.

## The authority rule (the one rule)

**Authoritative value of any record = houseCARL's live conflict winner among the active plugins.** Take the winner; that is what a patch must be consistent with. No manual exclusion math is needed — the out-of-scope overlay is already disabled in `plugins.txt`, so houseCARL excludes it once its resolver is fresh.

Read with `housecarl_read_record ... conflict_tree=true` to see the full override chain and the winner-relative field deltas (incl. vs. vanilla). For type-wide scans use `housecarl_cross_plugin_query` scoped with `plugins=[...]`.

## What is out of scope (and already disabled)

The top **Authoria overlay** is disabled in `plugins.txt` (listed without the `*` active prefix) and must NOT be treated as authoritative:

- The Reqtificator's generated output (`Requiem for the Indifferent.esp`) and tool outputs: `Authoria - Synthesis Output`, `RFTI Output`, `CK Output`, `xEdit Output`, `NPC Merge`, `Pandora Output`.
- The whole `Authoria - Reqtificated - <ModName>.esp` block and `Authoria - CC … Reqtificated.esp` block (begins ~`plugins.txt` line 2781, around load position ~2840 — this is the "priority 2840" boundary).
- `Authoria - *` patch plugins generally.

These represent the *final patched/merged* state. We study the **inputs** (Requiem + its addons), not the Authoria overlay, because our deliverable patches are themselves run through the Reqtificator afterward.

## The in-scope Requiem stack (where winners come from)

Core `Requiem.esp` plus its addons, each authoritative where it overrides:

- **Weapons / armor** → `Requiem - Weapons and Armor Redone.esp` (WAR) overrides core Requiem.
- **Magic / spells / effects** → `Requiem - Magic Redone.esp`.
- **Races** → `Requiem - Races Redone.esp`. **Stealth** → `Requiem - Stealth Redone.esp`. **Alchemy** → `Requiem - Alchemy Redone.esp`. **Food** → `Requiem - Food and Beverages Redone.esp`.
- Plus their cross-patches (`Requiem - MR/WAR/SR/AR … Patch.esp`).

For any record, the live winner among these is the comparable to replicate.

## Scope exception — race records (Aaron-approved)

`RACE` records legitimately resolve to **`Authoria - Master Patch - Races Merge.esp`** (the live winner for ~49 races: all playable + vampire variants + creatures). This is a **deliberate exception** to the "exclude `Authoria - *`" rule above. It is allowed because the Races Merge is a corrected/consolidated **input** merge — it faithfully carries Requiem's trait spells + knockdown `Attacks` data (superseding the dated `requiem_knockdown_tweak.esp`) plus a few Authoria tuning deltas — **not** the Reqtificated **output** (`Requiem for the Indifferent.esp`), which stays excluded. For the race domain, treat this Master Patch as authoritative; **do not re-point to escape it.** The Iron Sword probe still validates the weapon/item domains; races correctly resolve to the Master Patch. (Watch for the analogous case in other actor domains — verify the live winner per record rather than assuming an `Authoria - *` winner is always out of scope.)

## Refresh procedure (if the overlay reappears as winner)

If houseCARL shows ~3410 active, or a record's winner is `Requiem for the Indifferent.esp` / an `Authoria - *` plugin, the resolver is stale. Re-point houseCARL at your own Requiem MO2 instance to refresh (the author's was `D:\Wabbajack\Authoria-dev`):

```
housecarl_set_mo2_instance  path="<your MO2 instance>"
```

**Verification probe** (run after pointing): read Iron Sword `012EB7:Skyrim.esm` with `conflict_tree=true`. Correct state = winner is **`Requiem.esp`**, chain `Skyrim.esm → unofficial skyrim special edition patch.esp → Requiem.esp`. If the winner is `Requiem for the Indifferent.esp`, the resolver is still stale — do not proceed until it reads `Requiem.esp`.

*(Verified: after pointing, Iron Sword winner = `Requiem.esp`, override_depth 3. ✅)*
