# Phase 4 handoff — `requiem-race-patching`

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL + anthropic-skills available). Self-contained. Mirrors the proven Phase 1–3 template. **Reordered ahead of NPCs** — NPCs inherit race templates/traits, so races must be understood first. Races are bounded → build an **exhaustive** catalog.*

---

You are authoring **Phase 4** of the Authoria Requiem Patching Skills project: the `requiem-race-patching` skill. Phases 1–3 (weapon/armor/ammo) established the template — **copy its shape exactly**. This is the first **actor-side** domain (the method shifts from item ladders to race stats, abilities, and the trait engine), and it precedes Phase 5 (`requiem-npc-patching`), which depends on it.

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL** at `D:\Wabbajack\Authoria-dev`.

## First, orient (before anything else)

1. Read, in order: `STATE.md` (esp. Phase 1–3 notes — the template, the houseCARL write gotchas, and the lesson that **the source-vs-Reqtificator-assigned split is a per-domain question — check the live config, don't assume**), `docs\scope-and-authority.md`, `docs\reqtificator-rules.md`, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Mirror the pilots: read one item-domain `SKILL.md` (e.g. `requiem-armor-patching`) + its `references/`/`evals/` for the exact shape.
3. Invoke `anthropic-skills:skill-creator` (standard §1).
4. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true` → winner MUST be `Requiem.esp`. If it's `Requiem for the Indifferent.esp` / an `Authoria - *` plugin, run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe.

## Cross-phase rules (confirmed over 3 domains — apply, don't rediscover)

- **Check the keyword/perk split live for THIS domain.** Don't assume the Reqtificator assigns racial perks/abilities — read `ActorAssignmentRules_Requiem.esp.conf` and confirm against real records which traits are **source-carried on the RACE** vs **assigned by the Reqtificator to actors at build time**.
- **Preserve author intent:** don't rewrite a sensible author value unprovoked; prefer strict normalization to the Requiem race standard; correct only what's provably off. (Memory `patching-preserve-author-intent`.)
- **houseCARL write gotchas** (memory `housecarl-patch-rename-gotcha`): author into a **fresh inactive** patch (active-patch write self-locks); a brand-new never-enabled patch isn't in houseCARL's registry, so the **`bulk_apply` per-op read-back is the only verification** until enable + `housecarl_set_mo2_instance` refresh; don't re-issue a Remove the active winner already applied (copy the winner, add only new deltas).

## The domain has TWO sub-domains — treat them distinctly

1. **Playable races** (the 10 vanilla player races + custom playable races from follower/race mods). Requiem rebalances their starting **Health/Magicka/Stamina**, **CarryWeight**, **skill bonuses**, **racial abilities/powers** (passive resistances, active powers), and **description**. Authoritative source = **`Requiem - Races Redone.esp`**; the design spec is `…\Requiem 6.0.2 …\documentation\Races.md`.
2. **Creature / NPC races** (animals, undead, daedra, dragons, dwarven automata, etc.). Requiem gives these **incoming-damage-modifier perks** and traits via the Reqtificator's `feature_racialTraits`, which matches the **race FormID** against hardcoded `races.*` lists and assigns the trait to the actors. Authoritative source = `Requiem.esp` + the config.

## THE central problem — the Reqtificator does not recognize NEW races

The Reqtificator keys racial traits (and playable-race perks) off **FormID lists it ships with**. A brand-new modded race is **not in those lists**, so the auto-pass assigns it **nothing**. This is exactly the creative gap these skills fill. The skill must teach the strategies (investigate which is correct live):

- **New playable race:** replicate a vanilla Requiem race's full structure — stats, skill bonuses, racial ability spells, the playable-race perk, and the race keywords — so it behaves as a Requiem race. Determine **how the Reqtificator recognizes a playable race** (race-FormID list vs a keyword/`ActorTypeNPC`+playable flag): if it can't see the new race, the patch must **hand-carry** the playable-race perk + abilities rather than rely on the auto-pass.
- **New creature race:** classify it to the **nearest vanilla Requiem trait category** (e.g. a new troll-type → the troll/werecreature trait). Because the incoming-damage-modifier perk is **applied to the actors by race-match** (not stored on the RACE), a new race's NPCs won't receive it — so either **template the creature's NPCs onto a known Requiem race**, or **hand-add that category's perk to the creature NPCs** (this bridges into Phase 5 — the race skill defines the classification + which perk; the NPC skill applies it per-actor). Note the `doNotInheritTraits` keyword (`AD3A4F` — verify) that opts a record out.

## Mining to do — be EXHAUSTIVE (bounded domain)

- Enumerate: `housecarl_cross_plugin_query type="RACE" plugins=["Requiem - Races Redone.esp"]` and `["Requiem.esp"]` (take the live winner per record).
- **Playable races (all ~10):** `housecarl_batch_record_detail ... conflict_tree=true` reading starting `Health/Magicka/Stamina`, `CarryWeight`/base carry, skill bonuses (`Skills`/skill-boost data), the racial ability/power spell list (`ActorEffect`/`Spells`), `Keywords`, unarmed damage, height/weight/speed. Tabulate against `documentation\Races.md`. Derive the Requiem playable-race **stat + skill + ability** standard.
- **Creature races (representative-to-exhaustive across categories):** read RACE records for animals, undead (skeleton/draugr/etc.), daedra (atronachs/dremora/lurkers/seekers), dragons, dwarven automata — capture `Keywords` (`ActorType*`) and confirm via `ActorAssignmentRules` which **trait category** each maps to and which **incoming-damage-modifier perk** that category gets. Build the exhaustive **race → trait-category → perk** table.
- **Verify the split:** read a creature NPC (Phase-5 preview) to confirm the trait perk is on the **actor**, not the RACE — this defines how a new race must be handled.
- **ARMA note:** for new **playable** races, capture how a race is added to armor so it can wear gear (race added to relevant `ARMA` `Additional Races` / `ArmorRace` set to a vanilla skeleton). Note it as an integration step.
- Pick **2–3 worked examples:** one playable race (e.g. a custom playable race mod) and one creature race (a modded beast that needs trait classification), plus optionally a beast-form/were-race.

## Build the skill (conform to the standard §2–§8)

Author into `authoria-requiem\skills\requiem-race-patching\`, mirroring the pilots:

- **`SKILL.md`** — `name: requiem-race-patching` + single-line action-first `description:` (lead "Patch a new playable or creature race for Requiem via houseCARL — derive racial stats, skills, abilities, keywords, and the creature trait classification from Requiem's own comparable race…"; ≥3 trigger nouns in "Use when…": *patch a race for Requiem, balance a custom playable race, a modded creature/beast race, set racial abilities/skill bonuses, a new enemy race takes wrong damage, race not recognized by the Reqtificator*; pushy tail "Load this before touching a RACE record, not after the Reqtificator skips the race it can't see"; ≤1,536 chars). Body ≤500 (aim <250): `## Overview` → `## First step` (freshness probe + identify playable vs creature) → `## Workflow` (the live-analogy loop per sub-domain, real houseCARL call shapes) → `## Judgment` (the new-race-not-recognized strategies; classify-to-nearest; when to template onto a vanilla race; ask when ambiguous) → `## Checklist` (stats, skills, abilities/spells, keywords incl. doNotInheritTraits, trait-category/perk for creatures, ARMA race-add for playable, masters, hand-off to Phase 5 for per-actor trait application) → `## Common mistakes` → `## Notes`.
- **`references/`** — `playable-races.md` (the exhaustive playable stat/skill/ability standard, from Races.md + live), `creature-traits.md` (the exhaustive race → trait-category → incoming-damage perk table), `keywords.md` (race keyword vocab + FormIDs verified live, the source-vs-assigned split), `housecarl-recipes.md` (copy-ready `create_record`/`set_field`/`bulk_apply` shapes for a RACE override + a creature-trait perk add), `worked-examples.md` (the 2–3 real cases), `index.jsonl`.
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss (near-misses: an NPC stat-block patch (that's Phase 5), a creature's *loot/leveled-list*, a custom-race *bodyslide/mesh* question, a race-menu preset, a follower's *dialogue*, a vampire/werewolf *power* design → magic). `manual_predicted_trigger` per query (§6.5). Predicted recall ≥80%, specificity ≥50%.

## Verify before finishing

- **Round-trip:** blind-derive one real Requiem **playable** race's stats/skills/abilities from a sibling race comparable + the Races.md standard, diff against the houseCARL winner; AND classify one **creature** race to its trait category and confirm the perk matches the config. Record in `evals/results-<date>.json`.
- Walk the §8 19-item checklist; note deferrals (16–18 → §6.5 manual fallback on Windows; 19 build-script registry N/A — auto-discover).

## Close out

- Update `STATE.md`: set Phase 4 ✅ and append a Phase-4 notes block (the playable-race standard, the creature race→trait→perk table, **how a new race is recognized/handled by the Reqtificator and the chosen strategy**, the race↔NPC boundary, anything Phase 5 must reuse — especially the per-actor trait-application bridge).
- Report back to the master session: what was built, the round-trip result, and any decision the master should fold into the Phase 5 (NPC) handoff. Do **not** start Phase 5.
