# Scope & Authority

*Read this before any record read. It defines what "Requiem's authoritative value" means for this project and how to make houseCARL report it correctly.*

## The MO2 instance (the real precondition)

- houseCARL must be pointed at **the MO2 instance and profile you are patching for** — the load order the player will actually run. This, not any record's winner, is what establishes authority.
- Verify with `housecarl_load_order_status` (instance path, profile, and plugin count should match what MO2 shows); fix with `housecarl_set_mo2_instance path="<your MO2 instance>"`.
- The study source is this live load order. (The reference corpus was mined on the author's Authoria instance; the author's profile was *Authoria - Requiem Reforged - Main Profile*.)

## The authority rule (the one rule)

**Authoritative value of any record = houseCARL's live conflict winner.** Take the winner; that is what a patch must be consistent with — its values are what the list actually plays. No manual exclusion math is needed. Two profile shapes produce two valid winner shapes:

- **Authoring-style profile** (generated overlay disabled — the author's mining setup): hand-authored Requiem plugins win directly (`Requiem.esp`, WAR, MR, …).
- **Live/consumer profile** (Reqtificator output enabled — the normal state): `Requiem for the Indifferent.esp` (or a later patch) wins, folding the build pass over the same hand-authored stack. Derive from it — if the Reqtificator rescaled the comparable, the read folds the rescale in automatically.

Either way, **never** run `set_mo2_instance` in response to a particular plugin winning — re-point only when houseCARL is reading the wrong instance or profile altogether (see "Wrong-instance procedure" below).

Read with `housecarl_read_record ... conflict_tree=true` to see the full override chain and the winner-relative field deltas (incl. vs. vanilla). For type-wide scans use `housecarl_cross_plugin_query` scoped with `plugins=[...]`.

## What you still never copy (build outputs as *inputs*)

Your deliverable patch is an **input** to the next Reqtificator run. Reading a build output as a comparable is fine; *authoring* build outputs into your patch is not:

- **Don't hand-copy Reqtificator-assigned forms** off a winner — the `REQ_DamageType_*` keyword, resist-tier and tempering keywords, `RFTI_All_*` rescaling perks. The build pass assigns those from your patch's *input* keywords (material, weapon-type, armor-set, race); copying them double-assigns or fights the pass.
- **Protect hand-tuned stats with the exclusion kit** where the domain calls for it (`RFTI_Exclusions_No{Damage,WeaponReach,BowSpeed}Rescale`, ammo's `RFTI_Exclusions_NoDamageRescale`) so the next run preserves them.

## The hand-authored Requiem stack (what feeds the winner)

Core `Requiem.esp` plus its addons, each owning its domain:

- **Weapons / armor** → `Requiem - Weapons and Armor Redone.esp` (WAR) overrides core Requiem.
- **Magic / spells / effects** → `Requiem - Magic Redone.esp`.
- **Races** → `Requiem - Races Redone.esp`. **Stealth** → `Requiem - Stealth Redone.esp`. **Alchemy** → `Requiem - Alchemy Redone.esp`. **Food** → `Requiem - Food and Beverages Redone.esp`.
- Plus their cross-patches (`Requiem - MR/WAR/SR/AR … Patch.esp`).

On an authoring-style profile the winner comes from these directly; on a live profile the Reqtificator output folds the build pass over them. Either way the live winner is the comparable to replicate.

## Scope note — merges and compatibility patches are in scope (Aaron-approved)

A hand-authored **merge** winning a record is legitimate authority, same as any other live winner. The reference case: on the author's instance, `RACE` records resolve to `Authoria - Master Patch - Races Merge.esp` (the live winner for ~49 races) — a corrected/consolidated input merge that faithfully carries Requiem's trait spells + knockdown `Attacks` data (superseding the dated `requiem_knockdown_tweak.esp`). Treat such a winner as authoritative; **do not re-point to escape it.** On other instances the race winners are typically `Requiem - Races Redone.esp` and its patches, the setup's own merge, or the Reqtificator output. The general rule stands: the live winner is the authority — verify the live chain per record rather than judging a plugin by its name.

## Wrong-instance procedure (if Requiem is missing from the chain)

The wrong-instance signal is `Requiem.esp` **absent** from a vanilla record's override chain (or `housecarl_load_order_status` reporting a different instance/profile than the one you are patching for) — it means houseCARL is reading the wrong load order. Re-point houseCARL at your own Requiem MO2 instance:

```
housecarl_set_mo2_instance  path="<your MO2 instance>"
```

**Verification probe** (run after pointing): read Iron Sword `012EB7:Skyrim.esm` with `conflict_tree=true`. Correct state = **`Requiem.esp` appears in the chain** (`Skyrim.esm → unofficial skyrim special edition patch.esp → Requiem.esp → …`). Either winner is valid: `Requiem.esp` (overlay disabled) or `Requiem for the Indifferent.esp` / a later patch (live profile — the normal state; the authority rule above applies). The only blocking condition is `Requiem.esp` missing from the chain.

*(Verified on the author's mining instance — generated overlay disabled — Iron Sword winner = `Requiem.esp`, override_depth 3. On a live profile with the Reqtificator output enabled, expect winner = `Requiem for the Indifferent.esp` with `Requiem.esp` in the chain — verified live 2026-06-11: winner RftI, depth 4.)*
