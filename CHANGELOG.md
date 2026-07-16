# Changelog

All notable changes to the Authoria Requiem Patching Skills plugin. Versioning is [semantic](https://semver.org); the `version` in `authoria-requiem/.claude-plugin/plugin.json` is bumped on each release.

## 1.2.3 — 2026-07-16

- **`requiem-script-patching` — VMAD sweep (a) collapsed to one call.** houseCARL's `where=`
  presence tests (`exists` / `missing`) now match struct fields (houseCARL issue #197, closed),
  so the bulk-pass per-type fan-out (MGEF → BOOK → ACTI → FURN → QUST, one query each) is
  replaced by a single cross-type `where=["VirtualMachineAdapter exists"]` query, with hits
  expanded via `housecarl_batch_record_detail depth=3` for disposition. This also closes the
  residual gap the per-type list carried — a host type nobody thought to sweep could hide a
  script; the one-call sweep covers every record type by construction. Re-probed live before the
  edit (struct presence match + `plugins=`-scoped cross-type call both confirmed). Body-only —
  no description changed, no §6.5 re-measure owed. Closes the standing `dev/BACKLOG.md` entry.

## 1.2.2 — 2026-07-15

The S4 findings batch: all eight skill-body findings from the Gray Cowl empirical close
(`dev/s4-graycowl-validation/FINDINGS.md`, F1–F8) fixed in one pass. Body-only — no descriptions
changed, no §6.5 re-measure owed.

- **F1 — summoned actors get an owner (`requiem-npc-patching`).** Conjured/summoned actors were a
  skip category ("balanced via the summon spell"); the spell only *prices* the summon, so two of
  three summonables shipped on `PCLevelMult` in the S4 run. The rule is now: the summon spell routes
  to `requiem-magic-patching`, but the summoned `NPC_` record itself is a combatant — fixed level +
  stats here. First step, Judgment, checklist, and `references/identification.md` all updated.
- **F2 — `NonPlayable` combat gear is tiered, not skipped (`requiem-weapon-patching`,
  `requiem-armor-patching`).** NPC-hand weapon copies and worn NonPlayable armor were skipped as
  "never reaches the player" — but the player *feels* them (the S4 oracle re-tiered every one).
  Skip taxonomies rewritten: a NonPlayable combat copy derives at its playable twin's / wielder's
  tier (a NonPlayable bow still needs `NPCsUseAmmo`); only the player-economy surface (recipes,
  value) is exempt. True skips (traps/props, template/no-slot records) stay skips.
- **F3 — unzoned dungeon cells get zones (`requiem-leveled-list-patching`).** The Reqtificator's
  `openEncounterZones` opens *existing* zones; it cannot invent one, and no sweep surfaced cell→zone
  assignment (the S4 oracle created 3 tiered ECZNs and assigned 18 unzoned cells). The bulk pass
  gains a cell→zone sweep: enumerate the mod's cells' `EncounterZone` links; unzoned dungeon cells
  are assigned an existing tier-matched zone or newly-created tiered ECZNs. Judgment and checklist
  updated to "leave the existing ones; create the missing ones."
- **F4 — recipes track the product's material (`requiem-weapon-patching`).** 6/8 S4 temper recipes
  defaulted to the Steel ingot + Craftsmanship gate. Workflow step 6 now derives the ingot and
  `HasPerk` gate from the weapon's own material keyword via the same-material comparable; Common
  mistakes and the checklist name the anti-pattern.
- **F5 — flags are unions, pack-wide.** A literal `Flags` Set cleared unlisted bits in the S4 run
  (`ManualCostCalc` off all six authored-cost spells; `Female`/`Essential`/`Unique` off named NPCs).
  Every flag-writing skill's bulk protocol + checklist now carries the rule — read the winner's
  bits, write original-bits + your change — with the domain's classic casualties named (npc, weapon,
  armor, ammo, magic, consumable, race, leveled-list; the two example `Flags` writes in npc/race are
  annotated as unions). The houseCARL-side ask (flag-bit Add/Remove verbs) is filed as an HCBR gap.
- **F6 — perk derivation is source-scoped, full stop (`requiem-perk-assignment`).** Prefix-filtering
  a live winner list is no longer a sanctioned substitute for reading the comparable's
  `plugin="Requiem.esp"`/defining-addon perk list — the build pass re-carries tier perks that pass
  every prefix filter (S4 shipped 11–18-perk supersets). The filter is demoted to a last-resort
  cross-check that ships flagged, not silent. First step, Common mistakes, and checklist updated.
- **F7 — the resolve gate (`requiem-npc-patching`, `requiem-perk-assignment`, router).** Two S4
  FormIDs shipped dangling — one the right FormID under the wrong master suffix. Checklists now
  require `housecarl_resolve` on every carried FormID before write; the router's checklist gates on
  it across lanes. (The depth-2 PerkPlacement opacity that invited the slip is filed as an HCBR
  note.)
- **F8 — router-table rows (`requiem-patching` + `references/routing-table.md`).** (a) `ARMA` gets
  an explicit split rule — ARMO-linked addons disposition with their piece in the armor lane, race
  skin/body ARMA in the race lane, every ARMA assigned to exactly one lane up front (the armor
  skill's boundary note reconciled to match). (b) Quest-start level gating (`SMQN` / quest
  `GetLevel` start conditions) gets an explicit **flag-to-the-user** row — quest pacing in a
  de-levelled world is a judgment call, never auto-derived or silently kept. (c) The mod's own
  `CLAS`/`CSTY`/`OTFT`/`FACT` records get an owner — `requiem-npc-patching`'s bulk pass dispositions
  them (retarget the actor to the Requiem analogue, or rebalance a still-referenced custom record by
  live analogy). (d) Standing-stone-like ability SPELs co-route `standing-stones.md` (their
  magnitudes follow the stone model, not a combat tier ladder), stated in the gap table and the
  magic skill's classify step.

## 1.2.1 — 2026-07-15

Papercut fix: the router's missing LVSP row (the unfinished half of the 1.0.3 charter's D3 fold-in).

- **Router (`requiem-patching`) gains the `LVSP` routing row** (SKILL.md table + `references/routing-table.md`) → `requiem-leveled-list-patching`, which has owned LeveledSpell since 1.0.3 (scope, workflow branch, bulk sweep, checklist). Until now an enumerated LVSP fell to the four-way catch-all and was flagged "no owner" — surfaced, never dropped, but the wrong disposition when an owner exists. The row carries the no-merge-toggle note (only LVLI/LVLN are Reqtificator-merged; LVSP resolves by plain conflict winner → hand de-level) and routes spell *design* to `requiem-magic-patching`. Body-only — no descriptions changed, no re-measure owed.

## 1.2.0 — 2026-07-15

New domain skill (S3 of the 1.0.3 coverage-audit charter, decision D1): perk assignment gets a first-class owner, closing the audit's "PERK is a pack-level hole" finding.

- **New skill: `requiem-perk-assignment`** — derives which existing Requiem perks an NPC should carry from its **equipment, spell kit, and tier** against Requiem's own template ladders, and dispositions a mod's own PERK records. It never authors a PERK record. Corpus mined live via houseCARL (4-way fan-out: 84 `REQ_Bandit_Template_*` weapon ladders + the 115-record Warlock caster spine + 43 Requiem classes + the 599-PERK Requiem space enumerated; ~90 actors/perks full-read). The doctrine anchors, all query-verified: **the three-lane boundary** (source-carried player-tree perks are the only writable lane; the Reqtificator's ~45-perk mechanics chassis is stamped at build — measured source 5 → build 50 on a Requiem bandit — and the `RFTI_Player_*` controllers appear on zero source NPCs); **assign origin FormIDs** (Requiem overrides the vanilla trees in place — vanilla Armsman `0BABE4` resolves live to WAR's Weapon Mastery); **rank chains are separate single-rank FormIDs**, carried as one PerkPlacement per rank at Rank 1; the **warrior formula** (weapon line + defence line keyed on shield/armor class + universal floor, additively deeper by tier), the **caster formula** (school nodes at the threshold of the actor's highest spell tier + the shared survivability set; NPCs never carry the player-only `_Mastery_` gates), hybrids stack both, creatures carry race×tier supersets or trait-only kits; and the **class caution made operational** (a CLAS record has no perk field; equipment out-resolves class; class is placeholder noise on 4,229 templated records). Mod-shipped PERKs get the author-approved three-way disposition: leave runtime plumbing alone, fold only an obvious duplicate of a Requiem gate, otherwise flag to the user with a concrete question. Bulk pass protocol (PERK queue + the NPC perk lane riding `requiem-npc-patching`'s enumeration) baked in from birth. Description validated per §6.5 fan-out; results archived in `evals/`.
- **Router (`requiem-patching`) hands PERK to the new skill.** The `PERK` routing row (SKILL.md + `references/routing-table.md`) now routes to `requiem-perk-assignment` (previously "no domain skill owns standalone PERK rebalancing — flag the remainder"); `references/perks-skills.md`'s mod-perk section points at the skill's disposition rule; `requiem-npc-patching` hands off perk-set *derivation* (no clean analogue, hybrids, dedicated perk jobs) while keeping the whole-NPC frame and the copy-the-analogue path. Body-only sibling edits — no sibling descriptions changed, so no re-measure owed for them.
- Rosters updated (README table + `skills/README.md`; CLAUDE.md skill count), plugin manifest bumped to 1.2.0.

## 1.1.0 — 2026-07-15

New domain skill (S2 of the 1.0.3 coverage-audit charter, decision D2): consumables get a first-class owner instead of the router's "flag the remainder to the user" punt.

- **New skill: `requiem-consumable-patching`** — potions, poisons, oils, food, drink/alcohol, drugs, and ingredients (ALCH + INGR + their COBJ recipes), derived by live-analogy against `Requiem - Alchemy Redone.esp` and `Requiem - Food and Beverages Redone.esp` as refined by the load order's hand-authored patch layers. Corpus mined live via houseCARL (all 287 AR ALCH + 183 INGR + 196 FaB ALCH touches enumerated; ~200 records read in full). The doctrine anchor, verified by query: **the Reqtificator never touches consumables** (`Requiem for the Indifferent.esp` defines only NPC/LeveledNpc and touches zero ALCH/INGR/COBJ) — a consumable override is the final balanced record, with no build-time safety net. The skill carries: the five class kits and their opposite techniques (potions **keep** effect links to Requiem-overridden MGEFs and rebalance the shell; food gets its effect list **rebuilt** to the FaB kit — themed fortifies + exactly one hunger tag + exactly one nutrition tag, raw-meat/fish paralysis dropped on cooking; alcohol = the `REQ_Alcohol_*` pair by strength tier; drugs = potion-flagged with no hunger/nutrition; ingredients = the full AR payload with exactly-4 `REQ_Alch_*` quartets, per-MGEF standardized durations, magnitude as the tier knob, `Value == IngredientValue`); the full potion/poison value-and-magnitude ladders; the cookpot-station COBJ shapes with perk/skill-band gates (yield-tiered, not potency); the racial-cuisine/Green-Pact keyword system; a Bulk pass protocol with per-record dispositions and reconciliation (1.0.3 doctrine from birth); and both boundary handshakes (new effect design ↔ `requiem-magic-patching`; placement ↔ `requiem-leveled-list-patching`). Description validated per §6.5 fan-out: 20/20 (recall 10/10, specificity 10/10), results archived in `evals/results-2026-07-15.json`.
- **Router (`requiem-patching`) hands consumables to the new skill.** The `INGR`/`ALCH` routing row (SKILL.md + `references/routing-table.md`) now routes to `requiem-consumable-patching` (previously `requiem-magic-patching` + gap references with a flag-the-remainder rule); the `alchemy.md`/`food.md` gap references keep the system constraints but point record work at the new skill; the magic skill's alchemy note states the boundary in both directions. Body-only edits — no sibling descriptions changed, so no re-measure owed for them.
- Rosters updated (README table + `skills/README.md`), plugin manifest bumped to 1.1.0.

Pack-wide coverage hardening (field report, DrHeisen 2026-07-15): the 1.0.2 fix was one instance of a class. A whole-mod job could still ship records unpatched three ways — seven of nine skills had no whole-plugin coverage layer at all, the router could clear a job with entire record types silently unrouted, and the one protected skill's reconciliation proved every record was *touched*, not that its balance fields were *carried*. A 9-way audit of every skill against that taxonomy drove these fixes; descriptions are unchanged, so no trigger re-validation is required.

- **Seven domain skills gain a *Bulk pass protocol*** (`requiem-weapon-patching`, `requiem-armor-patching`, `requiem-ammo-patching`, `requiem-magic-patching`, `requiem-leveled-list-patching`, `requiem-race-patching`, `requiem-script-patching`), mirrored as a *Coverage audit* recipe and gated by a whole-plugin checklist line: the type enumeration is the work queue, every FormID gets a per-record disposition (patched, or skipped with a verified reason and named skip categories), the pass closes on a reconciliation count, and each domain's uniform-looking families (armor sets, material-tier lines, rank chains and element triples, sublist trees, race variant pairs, shared-script property families…) are named in an explicit no-extrapolation rule.
- **"Patched" is a field verdict pack-wide.** A record counts as patched only when the skill's per-record field checklist passes for it — field-complete, not merely touched — so a reconciliation can no longer close green over an NPC that got a level but never its perks, spells, or stats.
- **`requiem-npc-patching` — the field-carry gaps behind the recurring misses.** `PlayerSkills` (per-skill values/offsets — the repeatedly-missed "skill offsets") is now named in the doctrine, the checklist, and `references/npc-fields.md` (AutoCalc-ON humanoids derive them; AutoCalc-OFF creatures/bosses carry the analogue's); casters must carry the analogue's actual castable spell list, not just the tempering trait; the de-levelling work list gains a drain post-condition (re-run query 1 — it must return empty or followers-only); the no-extrapolation rule now also names shared-`Template` and shared-`Class` families.
- **`requiem-magic-patching` — effects and tomes can no longer ride shotgun.** MGEFs are enumerated first-class (never reached only via the spells that cast them) with a closing cross-check — every effect a patched spell casts must itself be dispositioned; ENCH object effects, scrolls, and spell tomes each get their own sweep; and the tome is a per-spell must-carry — every patched SPEL has its teaching BOOK reverse-resolved and priced to the tier ladder.
- **`requiem-leveled-list-patching` — the de-levelling inverse job existed nowhere; now it does.** A mod's *own* leveled lists escape the Reqtificator merge (Requiem never defined them → `baseVersion == null`) and ship verbatim, level-gates and all — the skill now says so at the merge doctrine, the encounter-zone judgment, and a de-levelling triage that hand-flattens every `Level > 1` entry on the mod's own lists while keeping Requiem-defined overrides add-only. `LeveledSpell` (LVSP) joins the skill's scope (design stays with the magic skill; LVSP also has no merge toggle, making the hand de-level the only lever).
- **`requiem-weapon-patching` — the enchanted-weapon ownership handshake.** A modded `ObjectEffect` is a new enchantment that itself needs Requiem balancing — the skill now routes its ENCH/MGEF design to `requiem-magic-patching` at the workflow branch, in Common mistakes, and on the checklist; the weapon-side frame alone never counts as balanced.
- **`requiem-patching` (router) — no type is silently dropped.** Routing rows added for `INGR`/`ALCH` (ingestibles), `SHOU`/`WOOP`, and `PERK` (stated plainly: no domain skill owns standalone PERK rebalancing — disposition each record and flag the remainder to the user); a four-way catch-all disposition rule (routed / gap-reference / cosmetic-skip with reason / flagged no-owner) in both the skill body and `references/routing-table.md`, with honest rows for FLST/KYWD/QUST/CELL/GMST and friends; the per-record coverage rule now applies to **every** type (the closed "high-count types" list is gone); triage classifies rows instead of dropping them; and the job closes on a top-level reconciliation across all types — it cannot clear while any enumerated record lacks a disposition.
- **Housekeeping.** The new sections are indexed in every skill's `references/index.jsonl` (the 1.0.2 NPC sections are now indexed too), and three "Aaron"-by-name mentions in shipped references now read "the author", matching the rest of the corpus.

## 1.0.2 — 2026-07-14

Bugfix (HCBR-2026-07-14-03): a whole-plugin NPC pass gated coverage at record-**type** granularity, so per-record misses shipped silently.

- **`requiem-npc-patching` — new *Bulk pass protocol* section.** A Gray Cowl of Nocturnal rebalance shipped with 7 of 144 NPCs unpatched: the pass read representative members of same-prefix EditorID families and extrapolated, so the non-uniform outliers (PC-scaling vendors, an over-tier summon, a level-10 Thalmor pair, a level-1 quest attacker) — the exact records a rebalance exists to catch — were never dispositioned. The skill now opens whole-plugin jobs with two one-call coverage sweeps: the PC-mult **de-levelling work list** (`where=["Configuration.Level.LevelMult >= 0"]` — a scalar compare on the union arm doubles as an arm-presence test, returning exactly the actors still on a multiplier) and a full-type **triage matrix**. The enumeration is the work queue: every FormID gets a disposition (patched, or skipped with a reason verified per record), the pass closes on a reconciliation count (patched + skipped = enumerated), and extrapolating across same-prefix EditorIDs is prohibited. The copy-ready sweep is mirrored in `references/housecarl-recipes.md` and a whole-plugin line was added to the skill's checklist.
- **`requiem-patching` — coverage gate tightened from per-type to per-record.** `references/integration-checklist.md` item 1 and the skill's own checklist bullet no longer clear a high-count type (`NPC_`, `ARMO`, `BOOK`, `CONT`, `LVLI`, …) on a nonzero patch count; they require every record dispositioned with counts that reconcile, and cross-reference the NPC skill's *Bulk pass protocol*.
- **No houseCARL defect.** The coverage primitive was confirmed working live on `Gray Fox Cowl.esm`; the report's optional houseCARL ergonomic (documenting the union-arm `where=` presence test) is tracked separately in that repo. Skill descriptions are unchanged, so no trigger re-validation is required.

## 1.0.1 — 2026-06-11

Bugfix (HCBR-2026-06-11-03): the freshness probe tested a machine-specific fingerprint, not the real invariant.

- **Freshness probe (all nine skills + `scope-and-authority.md`)** — the probe required Iron Sword's conflict *winner* to be `Requiem.esp` and prescribed a `housecarl_set_mo2_instance` re-point for anything else. That fingerprint only matched the author's mining profile, where the Reqtificator's generated output was disabled. On a live profile `Requiem for the Indifferent.esp` is enabled and legitimately wins, so the probe false-alarmed every time; its remedy would re-point houseCARL *away from* the load order being patched, and `scope-and-authority.md` escalated to a hard "do not proceed" deadlock (plus a plugin-count staleness heuristic that was also a machine fingerprint — both removed). The probe now verifies the real precondition — `housecarl_load_order_status` shows the instance/profile you are patching for, with Iron Sword's chain containing `Requiem.esp` as a sanity check — and documents both healthy winner shapes (overlay disabled ⇒ `Requiem.esp`; live profile ⇒ the Reqtificator output or a later patch). Re-pointing is prescribed only for a genuinely wrong instance, never as a response to the Reqtificator's output winning.
- **Authority doctrine made mode-aware; the races correction propagated pack-wide** — the live conflict winner is the authority to derive from on any profile; on a live profile that includes `Requiem for the Indifferent.esp`, whose values are what the list actually plays (a Reqtificator rescale of the comparable folds into the read automatically). What a patch still never *copies* are the Reqtificator-assigned outputs (damage-type / resist-tier / tempering keywords, `RFTI_All_*` perks), and hand-tuned stats still take the `RFTI_Exclusions_*` kit. The race skill's "the live winner is correct — don't re-point to escape it" rule, which already contradicted the old probe inside the same file, is now the shared doctrine in every skill.

## 1.0.0 — 2026-06-09

First feature-complete release. Nine skills, each built by mining the live Authoria load order via houseCARL and authored to the houseCARL skill-authoring standard.

- **`requiem-patching`** — the integration brain/router: enumerate a plugin, route every record type to its domain skill, own the gap mechanics (vampirism/lycanthropy, diseases, exhaustion/stress, stealth, alchemy, food, shouts, standing stones, perks/skills, economy, the core combat/resistance model), and finish with the masters/`REQ_NULL`/Reqtificator checklist.
- **`requiem-weapon-patching`**, **`requiem-armor-patching`**, **`requiem-ammo-patching`** — the item domains: live-analogy stat/keyword/recipe/value derivation; carry inputs, never hand-stamp the Reqtificator's assigned outputs.
- **`requiem-race-patching`**, **`requiem-npc-patching`** — the actor domains: the two-layer race trait engine, the new-race recognition problem, fixed-level NPC balance, the creature trait bridge, follower setup.
- **`requiem-leveled-list-patching`** — placement: the Reqtificator's additive leveled-list merge, Level-1 de-leveling by repetition, containers, encounter zones.
- **`requiem-magic-patching`** — spells/effects/enchantments: the HalfCostPerk school+tier classifier, explicit costs, the three-layer (record / Reqtificator / Nox-script) split.
- **`requiem-script-patching`** — the Papyrus layer: the Magic Redone `Nox_*` runtime map, modded-follower runtime registration, VMAD attach; the script-vs-no-script gate.
- **Cross-cutting:** the masters + `REQ_NULL`-stripping hygiene gate is folded into every skill's checklist; the `requiem-patching` skill's `references/masters-and-null-stripping.md` holds the mechanism.

### Known follow-ups

- Empirical eval-loop re-validation on a non-Windows host (recall/specificity were validated via the §6.5 manual-prediction fallback; Windows blocks `run_loop`) + a peer prediction check.
- A few live-verification items: a live VMAD-write round-trip, the `housecarl_compile_script` path, the disease payload MGEF winner, the standing-stone activation quest trace, and the (currently inactive) follower-registration ESP.
