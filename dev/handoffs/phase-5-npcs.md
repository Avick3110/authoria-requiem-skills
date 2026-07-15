# Phase 5 handoff — `requiem-npc-patching`

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL **1.2.1** + anthropic-skills available; fully restart Claude first so 1.2.1 is loaded). Self-contained. Mirrors the proven Phase 1–4 template. Builds directly on Phase 4 (races).*

---

You are authoring **Phase 5** of the Authoria Requiem Patching Skills project: the `requiem-npc-patching` skill. Phases 1–4 established the template — **copy its shape exactly**. NPCs are the **actor instance** layer that sits on top of the race layer you mapped in Phase 4, so read the race skill closely and reuse its trait bridge.

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL** at `D:\Wabbajack\Authoria-dev`.

## First, orient (before anything else)

1. Read, in order: `STATE.md` (esp. **Phase 4 notes** — the two-layer trait model and the Phase-4→5 perk bridge — and the **master note about houseCARL 1.2.1**), `docs\scope-and-authority.md` (incl. the **race-domain authority exception**), `docs\reqtificator-rules.md`, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Read the **race skill** in full: `authoria-requiem\skills\requiem-race-patching\SKILL.md` + `references\housecarl-recipes.md` (esp. §G, the per-NPC trait-perk recipe) + `references\creature-traits.md`. Also skim an item-domain skill for the exact reference/eval shape.
3. Invoke `anthropic-skills:skill-creator` (standard §1).
4. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true` → winner MUST be `Requiem.esp`. If it's `Requiem for the Indifferent.esp` / an `Authoria - *` plugin (other than the race Master Patch exception, which only applies to RACE records), run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe.

## Cross-phase rules (apply, don't rediscover)

- **houseCARL 1.2.1:** the active-patch write self-lock is **FIXED** — write **directly into active patches** via `into=`; no fresh-inactive-patch workaround. A brand-new never-enabled patch still isn't in the registry until a `housecarl_set_mo2_instance` refresh — verify a just-created patch via the `bulk_apply` read-back. New 1.2.1 tool feature you'll want: `cross_plugin_query where="Field op value"` (e.g. `where="Configuration.Level >= 30"`, operators `= != > >= < <= contains`, multiple ANDed).
- **Live > shipped docs**, always (Phase 4 found the Races doc drifts hard). Read every number from the live winner.
- **Per-domain split:** check how each effect propagates. For NPCs the key split is **visual vs balance** (below) and **engine/template-inherited vs Reqtificator-assigned** (the trait perk).
- **Preserve author intent** (memory `patching-preserve-author-intent`): normalize balance fields to the Requiem analog; don't gratuitously rewrite a sensible author value; classify by role/keywords, not EditorID.

## The visual-vs-balance split (the central NPC discipline)

`NPC_` records get overridden by **appearance** mods (face/body — visual) AND by Requiem (**balance** — stats, level, class, combat style, factions, perks, spells, AI). The Reqtificator's `actorVisualAutoMerge` reconciles them at build time. **Your patch sets only the Requiem-balance fields and must not clobber the appearance winner.** Use `conflict_tree` to see who wins which fields; derive balance from the Requiem comparable, leave appearance alone, let the Reqtificator merge. (This is the same "carry the inputs" discipline as the item domains.)

## Scope of this skill

- **In (balance fields):** `Configuration`/ACBS (level — Requiem uses **fixed levels, not PC-level-mult**; flags Essential/Protected/Respawn/Unique/Summonable; AutoCalcStats; speed; health/magicka/stamina offsets), `Class`, `CombatStyle`, `Race` link, `Factions` (crime faction, ally/enemy, follower, vendor), `DefaultOutfit` link, `DeathItem` link, `Perks` (the per-race trait perk + combat perks), `Spells`/`ActorEffect`, `AIData` (aggression/confidence/morality/assistance), `Template` + `TemplateFlags`, keywords. **Follower registration** (the record-side: follower factions, race/class, fixed level).
- **Out:** appearance/visuals (leave to the appearance winner; Reqtificator merges); the **contents** of `DeathItem`/outfit leveled lists → Phase 6 (leveled lists); caster **spell/effect design** → Phase 7 (link the Requiem spell, don't author it); **script-based** follower registration (a quest/SPID/Papyrus mechanism) → note it and route to Phase 8 if it's not record-side. Cross-reference these.

## The Phase-4 → 5 bridge (creatures of a new race)

The Reqtificator adds the per-race `incomingDamageModifier` **perk to NPC_ records by race-FormID match** — but only for races on its shipped lists. An NPC of a **new/unrecognized race gets no trait perk**. Two fixes (from the race skill §G — reuse them):

1. **Template/retarget onto a known Requiem race** (`set Race` to the analog) so the Reqtificator recognizes it; or
2. **Hand-add the named trait perk** to the NPC (`Perks` Add, compose `PerkPlacement`) using the **Resist-and-Regen-Tweak physique taxonomy** (fur `000805`, metal `000807`, chitin `000802`, stone `00080A`, zombie `00080D`, supernatural undead `00080B`/dragon `000804`, …) — the live model, not base Requiem's `feature_racialTraits` set. Verify each FormID live.

For NPCs of a **vanilla/recognized** race, the Reqtificator handles the trait perk automatically — don't hand-stamp it.

## Templates — the cleanest integration

Many Requiem NPCs use `Template` + `TemplateFlags` to inherit traits/stats/factions/spell-list/AI from a base NPC or leveled character. **Templating a new generic enemy onto a Requiem-patched base NPC** makes it inherit all Requiem balance for free (the actor analog of race-templating). Teach when to template vs. when to set fields directly (uniques/named NPCs set fields; generic spawns template).

## Mining to do

- Counts + authority: `cross_plugin_query type="NPC_" plugins=["Requiem.esp"]`; check whether an **enabled Authoria input-merge** wins NPC balance fields (analogous to the race Master Patch exception — verify per record, watch for `Authoria - Master Patch - *`; note `Authoria - NPC Merge` was *disabled*). Use `conflict_tree` to separate the **balance winner** from the **appearance winner**.
- Sample across archetypes with `batch_record_detail ... conflict_tree=true`: a bandit (melee), a bandit mage/caster, a city guard, a generic creature (wolf/troll/draugr), a dungeon boss (draugr deathlord), a follower/housecarl, a vendor. Read ACBS/Configuration (level, flags, autocalc, stats), `Class`, `CombatStyle`, `Race`, `Factions`, `DefaultOutfit`, `DeathItem`, `Perks`, `Spells`, `Template`+`TemplateFlags`, `AIData`, keywords.
- Derive the patterns: how Requiem sets **level + flags** (fixed level; PC-mult off; Essential/Protected usage), which **classes** and **combat styles** Requiem assigns by role, the **faction** patterns (crime/ally/enemy/follower/vendor), the **template** patterns, and **which perks/spells are source-carried vs Reqtificator-added** (cross-check `ActorAssignmentRules` gameMechanics + the Phase-4 finding that the trait perk is build-time).
- **Followers:** read how a Requiem follower is set up (PotentialFollower/CurrentFollower factions, race/class/combat-style, fixed level) and **how Requiem registers modded followers** (record-side vs script — `Authoria - Modded Follower Requiem Registration`); capture the record-side requirements and flag the script side for Phase 8 if needed.
- Pick **2–3 worked examples:** a new humanoid enemy, a new follower, and a new creature using the Phase-4 trait bridge.

## Build the skill (conform to the standard §2–§8)

Author into `authoria-requiem\skills\requiem-npc-patching\`, mirroring the pilots:

- **`SKILL.md`** — `name: requiem-npc-patching` + single-line action-first `description:` (lead "Patch a new NPC, enemy, or follower for Requiem via houseCARL — derive level, flags, class, combat style, factions, perks, and trait inheritance from Requiem's own comparable actor…"; ≥3 trigger nouns in "Use when…": *patch an NPC/enemy/follower for Requiem, balance a modded boss, set Requiem factions/class/combat style, an enemy is too strong/weak, register a follower with Requiem, a custom creature takes wrong damage*; pushy tail "Load this before touching an NPC record, not after the enemy spawns unbalanced"; ≤1,536 chars). Body ≤500 (aim <250): `## Overview` → `## First step` (freshness probe + identify role/race/visual-vs-balance) → `## Workflow` (live-analogy loop with real houseCARL call shapes; templating; the trait-perk bridge) → `## Judgment` (fixed-level/essential choices, follower registration, uniques vs generic-template, leave appearance alone, ask when ambiguous) → `## Checklist` (race, class, combat style, level+flags, factions, perks incl. trait bridge, spells, outfit/deathitem links, AI data, template, masters; route loot→Phase 6, spells→Phase 7, scripts→Phase 8) → `## Common mistakes` → `## Notes`.
- **`references/`** — `npc-fields.md` (the balance-field standard: level/flags/class/combat-style/faction patterns by archetype, from live), `trait-bridge.md` (the creature-of-new-race per-NPC perk recipe, cross-linked to the race skill), `followers.md` (follower record-side setup + registration), `housecarl-recipes.md` (copy-ready `set_field`/`bulk_apply`/`create_record` shapes for an NPC override + a templated NPC + a perk-add), `worked-examples.md` (the 2–3 cases), `index.jsonl`.
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss (near-misses: a RACE patch (Phase 4), an NPC *face/appearance* edit, the *contents* of a death-item leveled list (Phase 6), a caster's *spell* design (Phase 7), a follower *dialogue/quest*, a non-Requiem NPC AI mod). `manual_predicted_trigger` per query (§6.5). Predicted recall ≥80%, specificity ≥50%.

## Verify before finishing

- **Round-trip:** blind-derive one real Requiem NPC's **balance fields** (class, combat style, level, flags, key factions, and — if a recognized-race creature — confirm the Reqtificator-added trait perk vs source) from a comparable, diff against the houseCARL balance winner; record in `evals/results-<date>.json`. Confirm you did NOT alter appearance fields.
- Walk the §8 19-item checklist; note deferrals (16–18 → §6.5 manual fallback on Windows; 19 build-script registry N/A — auto-discover).

## Close out

- Update `STATE.md`: set Phase 5 ✅ and append a Phase-5 notes block (the balance-field standard, the visual-vs-balance split mechanics, the template patterns, the follower registration findings, the NPC authority winner per archetype incl. any Authoria input-merge exception, and anything Phases 6–8 must reuse).
- Report back to the master session: what was built, the round-trip result, and any decision the master should fold into later handoffs (esp. whether follower registration needs a Phase-8 script component). Do **not** start Phase 6.
