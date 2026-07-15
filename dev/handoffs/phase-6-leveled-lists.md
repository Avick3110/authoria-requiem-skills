# Phase 6 handoff — `requiem-leveled-list-patching`

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL **1.2.1** + anthropic-skills available). Self-contained. Mirrors the proven Phase 1–5 template. This is the **placement/distribution** layer — it puts the items (Phases 1–3) and actors (Phase 5) into the world.*

---

You are authoring **Phase 6** of the Authoria Requiem Patching Skills project: the `requiem-leveled-list-patching` skill. Phases 1–5 established the template — **copy its shape exactly**. This domain is about **placement and distribution** (leveled lists, containers, encounter zones), not item stats — it's how a statted weapon or a balanced NPC actually appears in the game world.

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL 1.2.1** at `D:\Wabbajack\Authoria-dev`.

## First, orient (before anything else)

1. Read, in order: `STATE.md` (esp. **Phase 5 notes**, the **master note on houseCARL 1.2.1**, and the **start-of-session checklist**), `docs\scope-and-authority.md` (incl. the race-domain authority exception — watch for an analogous leveled-list/container master patch), `docs\reqtificator-rules.md` (esp. the `mergeLeveledLists` / `mergeLeveledCharacters` / `openEncounterZones` toggles), **`docs\masters-and-null-stripping.md`** (governs every domain — masters mechanism + universal `REQ_NULL` stripping), then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Skim two prior skills for the exact reference/eval shape — `requiem-weapon-patching` (the item it places) and `requiem-npc-patching` (the actor it places).
3. Invoke `anthropic-skills:skill-creator` (standard §1).
4. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true` → winner MUST be `Requiem.esp`. If it's `Requiem for the Indifferent.esp` / an `Authoria - *` plugin (other than the race Master Patch, which only applies to RACE records), run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe.

## Operating principles (apply, don't rediscover)

- **Be the master — make studied calls** (memory `be-decisive-master`): decide list placement, tier/level, and container loot from the live comparables + config; act and explain. Reserve questions for genuinely unknowable design intent, and even then state a sensible default.
- **houseCARL 1.2.1:** write **directly into active patches** via `into=`; verify a brand-new patch via the `bulk_apply` read-back until a `housecarl_set_mo2_instance` refresh.
- **Live > shipped docs.** Read every list's structure and level-gating from the live winner.
- **Masters + REQ_NULL** (per `docs\masters-and-null-stripping.md`): ensure `Requiem.esp` is mastered (reference a real Requiem form; verify the `masters:` read-back is correct + load-order-sorted) and **strip `REQ_NULL_*` references** — see the leveled-list nuance below.

## THE central mechanic — the Reqtificator MERGES leveled lists

`mergeLeveledLists = true` and `mergeLeveledCharacters = true` (live in `DefaultUserConfig.json`). At build time the Reqtificator **merges every plugin's leveled-list edits** against the master. This changes the patch strategy fundamentally vs. the item domains:

- **You do not fully rewrite a list — you ADD your entries**, and the Reqtificator merges your additions with Requiem's curated version. This is the distribution analog of "carry the inputs, let the Reqtificator assemble." **Determine the exact merge behavior live** (additive union vs. relev/delev semantics) before settling the patch shape — read how Requiem's own list-touching patches are authored.
- **Add to the RIGHT list at the RIGHT level.** Requiem **de-levels and curates** lists; an added entry's `Level`/`Count` must match Requiem's tier for that item's material/power (reuse the Phase 1–3 ladders). Placing a daedric sword at level 1 breaks the curation.

## REQ_NULL nuance for leveled lists (resolve and document this)

Requiem legitimately uses `REQ_NULL_*` **inside its own lists** as a removal/placeholder mechanism. The universal "strip every `REQ_NULL`" rule is about **what your patch emits**: when you patch additively you emit only your new entries (no `REQ_NULL`, nothing to strip), and you leave Requiem's list entries — including its deliberate `REQ_NULL`s — untouched. Only if you must **override a whole list** do you strip `REQ_NULL` from the copy. Confirm this behavior live and write it explicitly into the skill so later sessions don't wrongly strip Requiem's intentional `REQ_NULL` removals.

## Scope of this skill

- **In:** `LeveledItem` (LVLI), `LeveledNpc`/LeveledCharacter (LVLN), `Container` (CONT), `EncounterZone` (ECZN). **Placing** new items into loot/vendor/crafting lists at the correct tier; **placing** new NPCs into spawn lists; **filling** new containers via Requiem-appropriate lists; **de-leveling** new areas' encounter zones to Requiem's fixed approach.
- **Out:** the item/NPC **stats** themselves (Phases 1–5 — this skill consumes an already-statted record and distributes it). Link, don't re-stat. Cross-reference the item/NPC skills.

## Mining to do

- Authority + counts: `cross_plugin_query type="LeveledItem"` / `"LeveledNpc"` / `"Container"` / `"EncounterZone"` scoped to `plugins=["Requiem.esp"]` and the addons; confirm the in-scope winner per record (watch for an enabled Authoria input-merge analogous to the race exception — verify, don't assume).
- Sample, with `batch_record_detail ... conflict_tree=true`:
  - **LVLI:** a weapon/armor loot list, a vendor list, a gem/ingot list — read `Entries` (Level/Count/Reference), flags (`CalculateForEachItemInCount`, `CalculateFromAllLevelsLTEPlayerLevel`, `UseAll`), and how entries tier by level. Derive Requiem's level-gating per material/power tier.
  - **LVLN:** a creature/bandit spawn list — how tiers + chance-none are structured (cross-check the Phase-5 fixed-level tiers).
  - **CONT:** a dungeon chest / urn — which leveled lists it references and counts (Requiem's curated-loot pattern vs vanilla overstuffing).
  - **ECZN:** a dungeon zone — min/max level, flags; how Requiem de-levels (fixed vs `openEncounterZones`).
- Determine the **merge semantics** (read `DefaultUserConfig.json` + how Requiem's list patches are shaped) and settle the **additive patch pattern** (houseCARL `bulk_apply verb=Add field_path="Entries" compose={type:"LeveledItemEntry", sets:[Level,Count,Reference]}`).
- Pick **2–3 worked examples:** place a new weapon into the right loot + vendor lists at the right tier; add a new creature to its spawn list; fill/curate a new mod's container (and/or de-level its encounter zone).

## Build the skill (conform to the standard §2–§8)

Author into `authoria-requiem\skills\requiem-leveled-list-patching\`, mirroring the pilots:

- **`SKILL.md`** — `name: requiem-leveled-list-patching` + single-line action-first `description:` (lead "Place a new item or NPC into Requiem's world via houseCARL — add it to the right leveled list, container, or encounter zone at the correct tier, leaning on the Reqtificator's list merge…"; ≥3 trigger nouns in "Use when…": *add an item to a Requiem leveled list, make modded loot/gear appear in the world, distribute a new enemy spawn, fill a container for Requiem, de-level a dungeon/encounter zone, new gear never shows up in game*; pushy tail "Load this before editing any leveled list or container, not after the loot spawns at the wrong level"; ≤1,536 chars). Body ≤500 (aim <250): `## Overview` → `## First step` (freshness probe + identify what's being placed and where) → `## Workflow` (the additive-merge loop with real houseCARL `Add`/`compose` call shapes; tier the entry from the item/NPC ladder) → `## Judgment` (which lists, what level, container curation, encounter-zone de-leveling, the REQ_NULL nuance) → `## Checklist` (right list(s), correct Level/Count, additive vs override, masters, REQ_NULL handling, link not re-stat) → `## Common mistakes` → `## Notes`.
- **`references/`** — `list-structure.md` (Requiem's LVLI/LVLN tiering + level-gating, from live), `containers-and-zones.md` (container loot patterns + encounter-zone de-leveling), `merge-behavior.md` (the Reqtificator merge semantics + additive-patch rules + the REQ_NULL nuance), `housecarl-recipes.md` (copy-ready `Add`/`compose` entry shapes + container-fill + ECZN edit), `worked-examples.md` (the 2–3 cases), `index.jsonl`.
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss (near-misses: re-stat a weapon (Phase 1), balance an NPC's own stats (Phase 5), a SkyPatcher/SPID *runtime* distribution, a quest-reward placement that's hand-placed not leveled, a merchant *gold* tweak, a non-Requiem loot-overhaul mod). `manual_predicted_trigger` per query (§6.5). Predicted recall ≥80%, specificity ≥50%.

## Verify before finishing

- **Round-trip:** pick a real Requiem item and confirm, from the live lists, the tier/level + which lists it sits in; then show your additive patch reproduces that placement for a comparable. Confirm the Reqtificator-merge assumption holds (your patch adds, doesn't clobber Requiem's entries). Record in `evals/results-<date>.json`.
- Walk the §8 19-item checklist; note deferrals (16–18 → §6.5 manual fallback on Windows; 19 build-script registry N/A — auto-discover).

## Close out

- Update `STATE.md`: set Phase 6 ✅ and append a Phase-6 notes block (the list-tiering standard, the merge semantics + additive pattern, the REQ_NULL-in-lists resolution, container/encounter-zone patterns, the authority winner, and anything Phases 7–8 reuse).
- Report back to the master session: what was built, the round-trip result, and any decision for later handoffs. Do **not** start Phase 7.
