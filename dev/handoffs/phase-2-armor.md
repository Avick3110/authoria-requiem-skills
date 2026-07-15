# Phase 2 handoff — `requiem-armor-patching`

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL + anthropic-skills available). Self-contained. Mirrors the Phase 1 template, which is proven.*

---

You are authoring **Phase 2** of the Authoria Requiem Patching Skills project: the `requiem-armor-patching` skill. Phase 1 (`requiem-weapon-patching`) established the template — **copy its shape exactly**; this phase is the armor analog.

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL** at `D:\Wabbajack\Authoria-dev`.

## First, orient (before anything else)

1. Read, in order: `STATE.md` (esp. the Phase 1 notes — they hold the derived-rules pattern, the houseCARL gotchas, and the established template), `docs\scope-and-authority.md`, `docs\reqtificator-rules.md`, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Read the existing pilot for the concrete shape to mirror: `authoria-requiem\skills\requiem-weapon-patching\SKILL.md` and its `references/` (esp. `housecarl-recipes.md`, `index.jsonl`) and `evals/`.
3. Invoke `anthropic-skills:skill-creator` (standard §1).
4. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true` → winner MUST be `Requiem.esp`. If it's `Requiem for the Indifferent.esp` / an `Authoria - *` plugin, run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe.

## Cross-phase rules (already learned — apply, don't rediscover)

- **Preserve author intent:** don't rewrite a sensible author value unprovoked; correct only what's provably off-tier. **Value tracks the armor-set/material keyword, not raw armor rating.** Prefer **strict normalization of type-defined stats** (weight/AR to the Requiem tier standard). (Memory: `patching-preserve-author-intent`.)
- **Identify artifacts/uniques by acquisition trace**, never record heuristics alone: reverse-reference the ARMO for `ConstructibleObject` (craftable?) and `LeveledItem` (in loot?). Craftable or leveled ⇒ custom gear, keep structure + bespoke value; non-craftable + not-leveled + single fixed reward ⇒ artifact. When ambiguous, ask.
- **Reqtificator-OUTPUT vs source keyword split:** Phase 1 found the *damage-type* keyword is a Reqtificator output (absent from source) — the patch carries the *weapon-type* keyword and lets the Reqtificator assign the rest. **For armor, verify the analogous split live:** read a real WAR armor record's `Keywords` and compare against `ArmorKeywordAssignments_Requiem.esp.conf` to learn which keywords are **source-carried** (armor-set, armor-type light/heavy, armor-part, VendorItemArmor) vs **Reqtificator-assigned** (ranged-resistance tier `feature_damageResistances`, tempering perk `feature_tempering`). Do NOT hand-stamp the assigned ones.
- **houseCARL write gotchas** (memory `housecarl-patch-rename-gotcha`): you **cannot write into an ACTIVE patch** (self-lock) — author into a **fresh inactive** patch (`create_record`/`bulk_apply patch_name="..."`), then move the `.esp` into place if consolidating. A freshly written patch isn't auto-enabled — **verify patch content via the write read-back or `read_record plugin="<patch>.esp"`, not the winner read.** Don't re-issue a Remove the active winner already applied (Remove-by-index throws on an empty list) — copy the winner and add only new deltas.

## Scope of this skill

- **In:** all `ARMO` records — armored pieces (cuirass/body, helmet, gauntlets, boots, **shields**), in both **light and heavy**, plus clothing and jewelry *frames* (AR 0 ARMO). Set/material keyword, armor-type + armor-part keywords, base **ArmorRating**, **Value**, **Weight**, tempering recipe, crafting recipe (COBJ) + perk gate, and masters.
- **Out:** the *enchantment effect design* on enchanted armor/jewelry → magic phase (Phase 7); race-model/ARMA additions (which races can wear it) → races phase (Phase 5). The armor skill sets the ARMO frame and links an `ObjectEffect`; it routes effect *authoring* onward. Note these in the body so it routes them.

## Authority & method

Authoritative = houseCARL's live winner among active plugins. Armor winners come from **`Requiem - Weapons and Armor Redone.esp`** (WAR) and its patches (`Requiem - WAR Races Redone Patch.esp`, `Requiem - WAR Special Feats Patch.esp`, `Requiem - MR Weapons and Armor Redone Patch.esp`…), over base `Requiem.esp`. Teach the **live-analogy loop**: identify the new armor (part + material/set + weight class) → query Requiem's comparable (same part + same set/material) → derive AR/value/weight/keywords/tempering → apply judgment for uniques → emit an ESP override via houseCARL. **Never hardcode AR numbers** — derive from the live comparable.

## Mining to do (build the skill on real data)

- Counts + sets: `housecarl_cross_plugin_query type="ARMO" plugins=["Requiem - Weapons and Armor Redone.esp"]` and `plugins=["Requiem.esp"]`.
- Sample one piece per **part × material/weight** with `housecarl_batch_record_detail ... conflict_tree=true`, reading `ArmorRating`, `Value`, `Weight`, `Keywords`, `BodyTemplate` (ArmorType light/heavy + biped slots), tempering keywords:
  - **Heavy:** iron, steel, steel-plate, dwarven, orcish, ebony, dragonplate, daedric — cuirass/helmet/gauntlets/boots; plus heavy shields.
  - **Light:** hide, leather, elven, scale, glass, dragonscale, chitin, stalhrim-light — cuirass/helmet/gauntlets/boots; plus light shields.
  - Derive the **AR ladder** (per part, per material, per weight class) and the **value ladder** from the real winners.
- Read the live `ArmorKeywordAssignments_Requiem.esp.conf`: capture the armor-set → set-keyword map, the ranged-resistance tiers (`rangedResistance.tier1..5`), and the tempering map (set → perk keyword). **Confirm each FormID against a real record** (verify the source-vs-assigned split above).
- Read `Reqtificator.conf` AR caps (heavy body 74 / light 62; feet 27/18; hands 27/18; head 35/26; shield 54/44) and **sanity-check against the heaviest real records** (e.g. daedric vs dragonplate cuirass) to learn whether the record AR sits at/under the cap and how the Reqtificator treats it.
- Find a tempering COBJ (grindstone, `WorkbenchKeyword` for armor temper) and a crafting COBJ (forge) for one armor; capture the **perk gate** (clone the comparable's `Conditions` verbatim — the perk renders as an unreadable form-index).
- Pick **2–3 real worked examples**: a heavy set, a light set, and a unique/faction armor (artifact-style) — covering AR ladder, tempering, and a bespoke-value case.

## Masters note

Armor patches carry Requiem-defined keywords (ranged-resistance + tempering live in `Requiem.esp`), so `Requiem.esp` is pulled in as a master automatically. If a given patch happens to reference only vanilla keywords, add `Requiem.esp` as a master manually (note this in the skill).

## Build the skill (conform to the standard §2–§8)

Author into `authoria-requiem\skills\requiem-armor-patching\`, mirroring the weapon skill:

- **`SKILL.md`** — `name: requiem-armor-patching` + single-line action-first `description:` (lead "Patch a new armor piece for Requiem via houseCARL — derive armor rating/keywords/tempering/crafting/value from Requiem's own comparable…"; ≥3 trigger nouns in "Use when…": *patch armor for Requiem, balance a modded cuirass/shield/helmet, set Requiem armor keywords, armor rating too high/low for Requiem, add craftable armor, light vs heavy armor tier*; pushy tail "Load this before touching an armor record, not after the Reqtificator caps it wrong"; ≤1,536 chars). Body ≤500 (aim <250): `## Overview` → `## First step` (freshness probe + identify part/material/weight) → `## Workflow` (live-analogy loop, real houseCARL call shapes) → `## Judgment` (uniques via acquisition trace, AR-cap interaction, light/heavy choice, value sanity) → `## Checklist` (set/material keyword, armor-type + part keyword, ArmorRating, value, weight, tempering + crafting recipe + perk gate, masters, route enchant→magic) → `## Common mistakes` → `## Notes`.
- **`references/`** — `ar-ladder.md` (per-part/per-material/per-weight, from mined data), `keywords.md` (armor-set + type + part + resist + tempering vocab + FormIDs, verified live, with the source-vs-Reqtificator-assigned split called out), `crafting.md` (tempering + forge COBJ pattern + perk gates), `housecarl-recipes.md` (copy-ready `create_record`/`set_field`/`bulk_apply` shapes for an armor override), `worked-examples.md` (the 2–3 real patches), `index.jsonl` (topic → file + line).
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss (near-misses: weapon patch, ammo patch, enchant-effect-only change, a clothing-mesh/texture question, a body-slot/ARMA conflict, a non-Requiem armor mod install). `manual_predicted_trigger` per query (§6.5). Predicted recall ≥80%, specificity ≥50%.

## Verify before finishing

- **Round-trip:** blind-derive one real in-scope Requiem armor piece's AR/value/weight/keywords/tempering from a comparable (not the record itself), diff against the houseCARL winner, record in `evals/results-<date>.json`. The skill is good only if it reproduces the real values.
- Walk the §8 19-item checklist; note deferrals (items 16–18 use the §6.5 manual fallback on Windows; item 19 build-script registry is N/A in this repo — skills auto-discover by directory).

## Close out

- Update `STATE.md`: set Phase 2 ✅ and append a Phase-2 notes block (the AR + value ladders, the source-vs-assigned keyword split for armor, tempering perk gates, any inconsistencies, anything Phases 3–8 should reuse).
- Report back to the master session: what was built, the round-trip result, and any decision the master should fold into later handoffs. Do **not** start Phase 3.
