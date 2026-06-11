# Scope & Authority

*Read this before any record read. It defines what "Requiem's authoritative value" means for this project and how to make houseCARL report it correctly.*

## The MO2 instance

- houseCARL must be pointed at **your Requiem MO2 instance** (the author's profile was *Authoria - Requiem Reforged - Main Profile*).
- The study source is this live load order. (The reference corpus was mined on the author's Authoria instance.)

## The authority rule (the one rule)

**Authoritative value of any record = houseCARL's live conflict winner among the hand-authored plugins.** Generated build outputs — above all `Requiem for the Indifferent.esp`, the Reqtificator's output, which is enabled on every playable Requiem setup and wins many records — are **never** authority: when one is the raw winner, step down the chain to the last hand-authored override and take that. That hand-authored winner is what a patch must be consistent with; no other exclusion math is needed.

Read with `housecarl_read_record ... conflict_tree=true` to see the full override chain and the winner-relative field deltas (incl. vs. vanilla). For type-wide scans use `housecarl_cross_plugin_query` scoped with `plugins=[...]`.

## What is out of scope (never authoritative)

Generated / merged build outputs must NOT be treated as authoritative:

- The Reqtificator's generated output (`Requiem for the Indifferent.esp`) — on a live instance it is **enabled** and is usually the raw winner; step past it in the chain.
- Other tool outputs if your setup has them (Synthesis output, Bashed/Smashed patches, CK/xEdit scratch outputs, merge outputs). On the author's mining instance these formed a disabled `Authoria - *` overlay; on a play instance they are enabled — exclude them by reading the chain, not by expecting them to be absent.

These represent the *final patched/merged* state. We study the **inputs** (Requiem + its addons + hand-authored patches), not the generated outputs, because our deliverable patches are themselves run through the Reqtificator afterward — deriving from its output would bake build-time assignments into source data.

## The in-scope Requiem stack (where winners come from)

Core `Requiem.esp` plus its addons, each authoritative where it overrides:

- **Weapons / armor** → `Requiem - Weapons and Armor Redone.esp` (WAR) overrides core Requiem.
- **Magic / spells / effects** → `Requiem - Magic Redone.esp`.
- **Races** → `Requiem - Races Redone.esp`. **Stealth** → `Requiem - Stealth Redone.esp`. **Alchemy** → `Requiem - Alchemy Redone.esp`. **Food** → `Requiem - Food and Beverages Redone.esp`.
- Plus their cross-patches (`Requiem - MR/WAR/SR/AR … Patch.esp`).

For any record, the live winner among these is the comparable to replicate.

## Scope note — hand-authored merges are in scope (Aaron-approved)

A hand-authored **input merge** is legitimate authority, unlike a generated output. The reference case: on the author's instance, `RACE` records resolve to `Authoria - Master Patch - Races Merge.esp` (the live winner for ~49 races) — a corrected/consolidated input merge that faithfully carries Requiem's trait spells + knockdown `Attacks` data (superseding the dated `requiem_knockdown_tweak.esp`). Treat such a merge as authoritative; **do not re-point to escape it.** On your instance the race winners are typically `Requiem - Races Redone.esp` and its patches, or your own merge if you have one. The rule generalizes: judge a winner by *what it is* (hand-authored input vs generated output), not by its name — verify the live chain per record.

## Wrong-instance procedure (if Requiem is missing from the chain)

The stale/wrong-instance signal is `Requiem.esp` **absent** from a vanilla record's override chain (or the record resolving with no Requiem stack at all) — it means houseCARL is reading a non-Requiem load order. Re-point houseCARL at your own Requiem MO2 instance to refresh:

```
housecarl_set_mo2_instance  path="<your MO2 instance>"
```

**Verification probe** (run after pointing): read Iron Sword `012EB7:Skyrim.esm` with `conflict_tree=true`. Correct state = **`Requiem.esp` appears in the chain** (`Skyrim.esm → unofficial skyrim special edition patch.esp → Requiem.esp → …`). The invariant is chain presence, not winner identity: the winner being `Requiem for the Indifferent.esp` or another later patch is the normal state of a playable instance — derive from the last hand-authored override beneath it (the authority rule above). Do not proceed only while `Requiem.esp` is missing from the chain.

*(Verified on the author's mining instance — generated overlay disabled — Iron Sword winner = `Requiem.esp`, override_depth 3. On a playable instance with the Reqtificator output enabled, expect winner = `Requiem for the Indifferent.esp` with `Requiem.esp` in the chain.)*
