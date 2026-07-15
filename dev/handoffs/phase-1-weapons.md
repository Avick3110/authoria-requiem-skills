# Phase 1 handoff — `requiem-weapon-patching` (pilot)

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (with houseCARL + the anthropic-skills plugin available). It is self-contained.*

---

You are authoring **Phase 1** of the Authoria Requiem Patching Skills project: the `requiem-weapon-patching` skill. This is the **pilot** — it proves the template every later domain skill copies, so do it carefully and completely.

**Working dir:** `D:\Wabbajack\Authoria-requiem` (the skill plugin repo). **Study source:** the live load order via **houseCARL**, pointed at `D:\Wabbajack\Authoria-dev`.

## First, orient (do this before anything else)

1. Read, in order: `D:\Wabbajack\Authoria-requiem\STATE.md`, `docs\scope-and-authority.md`, `docs\reqtificator-rules.md`, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (the standard you author to — every rule is binding).
2. Invoke `anthropic-skills:skill-creator` via the Skill tool now (standard §1 requires it at authoring time; it carries the live description-optimization guidance).
3. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true`. The winner MUST be `Requiem.esp`. If it is `Requiem for the Indifferent.esp` or any `Authoria - *` plugin, run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and probe again before proceeding.

## Scope of this skill

- **In:** melee weapons (sword, greatsword, waraxe, battleaxe, dagger, mace, warhammer, quarterstaff) and ranged weapon *frames* (bows, crossbows). Their material tier, damage, all weapon keywords, tempering recipe, crafting recipe (COBJ) + perk gate, gold value, and leveled-list placement note.
- **Out:** ammo (arrows/bolts) — that's Phase 3, `requiem-ammo-patching`. **Staves** — magic domain, Phase 7 (balanced by effect, not damage). Note these exclusions in the skill body so it routes them onward.

## Authority & method (from STATE.md)

Authoritative = houseCARL's **live conflict winner among active plugins**. For weapons the winning overrides come from `Requiem.esp` and **`Requiem - Weapons and Armor Redone.esp`** (WAR) plus its patches (`Requiem - MR Weapons and Armor Redone Patch.esp`, `Requiem - WAR … Patch.esp`). The skill must NOT hardcode damage numbers — it teaches the **live-analogy loop**: identify the new weapon → query Requiem's own comparable (same type + material tier) → derive stats/keywords/recipe/value → apply judgment for uniques → emit an ESP override via houseCARL.

## Mining to do (gather the real data the skill is built on)

- Enumerate the contested weapon set: `housecarl_cross_plugin_query type="WEAP" plugins=["Requiem - Weapons and Armor Redone.esp"]` and `plugins=["Requiem.esp"]` (note totals; Requiem touches ~4,340 WEAP).
- Sample one weapon per material tier across types — e.g. iron, steel, dwarven, elven, orcish, glass, ebony, daedric, dragonbone swords/greatswords/maces/bows — with `housecarl_batch_record_detail ... conflict_tree=true`, reading `BasicStats` (Damage, Value, Weight), `Data` (Speed/Reach), `Keywords`, `Critical`, and the tempering/crafting keywords. Derive the **damage ladder** (per type, per material) and the **value ladder** from the real winners, not memory.
- Read the live `WeaponKeywordAssignments_Requiem.esp.conf` for the damage-type keyword FormIDs; confirm each against a real record's `Keywords`.
- Find a tempering recipe (COBJ with the weapon as `CreatedObject`) and a crafting recipe via `housecarl_cross_plugin_query type="COBJ"` / `references=<weapon FormID>` to capture the perk gate (`Bench Keyword`, conditions) and material inputs.
- Pick **2–3 real uniques/bespoke weapons** the in-scope Requiem stack defines (or that the team has patterns for) as worked examples.

## Build the skill (conform to the standard, §2–§8)

Author into `D:\Wabbajack\Authoria-requiem\authoria-requiem\skills\requiem-weapon-patching\`:

- **`SKILL.md`** — frontmatter `name: requiem-weapon-patching` + a single-line action-first `description:` (lead with the action "Patch a new weapon for Requiem via houseCARL — derive damage/keywords/tempering/crafting/value from Requiem's own comparable…"; ≥3 user-utterance trigger nouns in a "Use when…": *patch a weapon for Requiem, balance a modded sword/bow/mace, set Requiem weapon keywords, weapon damage too high/low for Requiem, add a craftable weapon*; pushy tail "Load this before touching a weapon record, not after the Reqtificator rebalances it wrong"; ≤1,536 chars). Body ≤500 lines (aim <250): `## Overview` → `## First step` (the freshness probe + identify the weapon) → `## Workflow` (the live-analogy loop with **real houseCARL call shapes**) → `## Judgment` (uniques, novel damage types, value sanity in Requiem's tight economy) → `## Checklist` (damage-type keyword, material/tempering keyword, BasicStats, crafting recipe + perk gate, leveled-list placement, gold value) → `## Common mistakes` → `## Notes`.
- **`references/`** — `damage-ladder.md` (per-type/per-material, from mined data), `keywords.md` (the weapon keyword vocabulary + FormIDs, verified live), `crafting.md` (tempering + COBJ recipe pattern + perk gates), `housecarl-recipes.md` (copy-ready `create_record`/`set_field`/`bulk_apply` call shapes for a weapon override), `worked-examples.md` (the 2–3 real patches), and `index.jsonl` (topic → file + line).
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss should-not-trigger (near-misses: armor patch, ammo patch, a staff, a non-Requiem weapon-mesh question, an enchantment-only change). Add `manual_predicted_trigger` per query (§6.5 manual fallback — the empirical loop needs non-Windows). Confirm predicted recall ≥80%, specificity ≥50%.

## Verify before you finish

- **Round-trip:** pick one real in-scope Requiem weapon, have the skill's procedure derive its damage/keywords/value/tempering *from scratch* (using only a comparable, not the record itself), then diff against the houseCARL winner. Record the result in `evals/` (e.g. `results-<date>.json`). The skill is good only if it reproduces the real values.
- Walk the §8 19-item checklist against the skill; note any item you defer.

## Close out

- Update `STATE.md`: set Phase 1 to ✅ in the ledger and append a Phase-1 notes block (what the damage/value ladders turned out to be, any inconsistencies found, whether ammo should stay separate, anything the next phases should reuse).
- Report back to the master session: a short summary of what was built, the verification result, and any decision the master should fold into later handoffs. Do **not** start Phase 2 — the master session emits that prompt next.
