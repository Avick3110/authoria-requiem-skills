# S4 empirical close — findings ledger (Gray Fox Cowl.esm, pack 1.2.1, 2026-07-15)

*Author: Aaron (+ Claude session) · 2026-07-15*

*Class: ARCHIVE (frozen record of the S4 validation run). The run's raw artifacts sit beside this
file; the session handoff carries the narrative. Verdict summary: coverage gates PASS, field
accuracy good-with-findings — the findings below are the actionable output.*

## How to read this

The test: a fully blinded re-derivation of the Gray Cowl of Nocturnal patch by the 1.2.1 pack
(7 lane agents, router discipline, write to a disposable ESP), scored against the shipped curated
patch (`Authoria - Patch - Gray Cowl of Nocturnal Requiem.esp`) as oracle. 41,187-record
denominator; 389 lane-routed; 181 written; 84 oracle-shared records field-diffed.

**Charter gates (all PASS):**
1. Field-gated reconciliation closed in every lane and at the router top level — 0 undispositioned
   records of 41,187. The Heisen failure class ("entire types skipped", "records missed") did not
   reproduce.
2. Router catch-all fired correctly on unrouted types (SMQN flagged, not dropped; GLOB explicitly
   dispositioned).
3. PERK / LVSP / ALCH / INGR all landed with their new owners (perk-assignment three-way ran; LVSP
   confirmed absent by explicit check; consumables lane owned ALCH/INGR end-to-end).

**Oracle-style caveat:** the curated patch is authored in the FINAL-VALUES style (writes
post-Reqtificator numbers, stamps REQ_DamageType_*/REQ_Tempering_* output keywords). The pack's
input-carry doctrine diverges by design; verified equivalent on the steel tier (source 8/4 → RftI
final 48/48). Raw diffs were classified accordingly (see scoring-results.json).

**PROVENANCE CORRECTION (Aaron, 2026-07-15, after the run):** the oracle patch was itself authored
BY houseCARL — an earlier-generation session, before the 1.0.2→1.2.1 skill fixes and before the
houseCARL updates. So this test is old-pipeline vs new-pipeline, blinded, not skills-vs-human.
Consequences for reading this ledger: (1) the oracle's under-coverage (46/144 NPCs, 11/33 armors,
9/34 spells, 0 consumables, 0 races) IS the pre-fix failure class — the new run's breadth is the
primary before/after evidence, not a side observation; (2) the oracle's final-values +
output-stamping style is pre-doctrine behavior — the (D)-class divergences are improvements, and
(B)-class "judgment divergences" have no privileged side (open calls for Aaron, e.g. Umbra's tier);
(3) F1–F3 gain weight — the OLD pipeline got those right where the new one skipped, i.e. genuine
blind spots, not style drift; C-class errors were verified against Requiem's own ladder and stand
regardless. (4) Latent risk in the live order: the old patch's final-scale values carry no
rescale-exclusion — a fresh Reqtificator pass over Gray Cowl would double-scale them. Regenerating
the Gray Cowl patch with the current pack (post-F1–F8 fixes) would remove that and double as a
second field test.

## Findings — skill-body fixes (candidate next charter)

**F1 — Summoned actors have no owner (npc↔magic seam).** npc skill skips summonables ("balanced via
the summon spell"); magic skill owns only the SPEL. The actor's own level block goes untouched — two
of three summonables shipped PCLevelMult, the anti-Requiem pattern. Oracle sets fixed levels
(24/28/30). FIX: npc skill's summonable rule → "spell tier routes to magic; the ACTOR record still
gets the fixed-level/stats pass here."

**F2 — NonPlayable NPC-hand/worn combat gear wrongly cosmetic (weapons + armor skip taxonomy).**
5 NonPlayable weapons + 1 NonPlayable armor skipped as "never reaches the player"; the oracle
re-tiers every one (dmg 48–84, AR 180) because the player FEELS NPC-wielded damage/armor. Also
missed the oracle's NPCsUseAmmo functional fix on the NonPlayable bow. FIX: skip taxonomy →
NonPlayable combat copies derive the same tier as their playable twin / wielder tier.

**F3 — Unzoned dungeon cells never get zones (lists skill).** Oracle created 3 new ECZN tiers and
assigned 18 unzoned cells; the lists lane never surfaced cell→zone assignment. FIX: bulk pass adds
"enumerate the mod's dungeon cells' EncounterZone links; unzoned dungeon cells need a zone (create
tiered zones if none exist)."

**F4 — Weapon COBJ recipes defaulted to Steel+Craftsmanship (weapons skill).** 6/8 temper recipes
ignored the weapon's true material (oracle: Gold/Moonstone/Ebony/Silver ingots + matching smithing
perk gates). FIX: recipe derivation must read the product's material keyword and use that
material's ingot + Requiem's matching perk gate.

**F5 — Flags fields are replace-not-merge hazards (all bulk protocols).** Literal Set of a Flags
token cleared unlisted bits: ManualCostCalc dropped on all 6 authored-cost spells; Female /
Essential / Unique / IsGhost / Invulnerable dropped on named NPCs. Partly a write-layer artifact of
this test, but the skill bodies nowhere say "flags are unions — carry the original bits you aren't
changing." FIX: one sentence in every bulk protocol + field checklist.

**F6 — Perk-set derivation can double-stamp (perk-assignment).** Melee NPC perk sets were copied as
11–18-perk supersets from already-Reqtified comparables (oracle carries 0–2 source perks). The
"never hand-stamp RFTI_* chassis" rule held programmatically, but tier-perk supersets from live
winners re-carry what the Reqtificator will assign. FIX: derive from the comparable's SOURCE
(Requiem.esp-scoped) perk list, not its live winner.

**F7 — Two dangling derived FormIDs (npc lane QA).** `0D799A:Requiem.esp` (caster perk node, 7
NPCs) and `01CE0E:Skyrim.esm` (Imhotep class) don't resolve. The masters/check_errors gate caught
them post-write. FIX: field checklist adds "housecarl_resolve every carried FormID before write."
*Root cause found 2026-07-15 (post-run probe):* the perk is real — `0D799A:Skyrim.esm`, read
directly off the mod's own boss at depth=3 — the lane carried the right FormID with the WRONG
master suffix. A master-suffix transcription slip, invited by perk lists rendering opaque at the
documented depth=2 (HCBR note `2026-07-15_note_perkplacement-depth2-identity.md`). The resolve-gate
fix stands and would have caught it.

**F8 — Router-table rows.** (a) ARMA row ambiguity: armor and race lanes each pointed the other's
ARMA subset away — 11 records needed central disposition. Sharpen the row's split rule. (b) No
owner for quest-start level gating (SMQN GetLevel 10→15 in oracle) — candidate gap-mechanic
reference (quest pacing in a de-leveled world). (c) Class/CombatStyle/Outfit/Faction records:
assignment-side rides the NPC skill, record-level rebalance unowned (Imhotep's custom Class is
where F7's dangling class ref came from). (d) Standing-stone-like abilities (the Stone trio,
mag-5000 author typo; oracle: 50) should co-route standing-stones.md.

## What the run proved RIGHT (keep, don't touch)

- Source-carry vs output-stamp discipline: 7 weapons byte-clean vs oracle intent; every
  REQ_DamageType/RFTI omission was correct.
- Coverage machinery (1.0.3 bulk passes + router reconciliation + catch-all): 0 misses at
  41k-record scale; every seam either declared or caught.
- Flag-don't-guess: all 10 lane flags were genuinely Aaron-grade calls; recommendations
  tier-adjacent to the oracle's answers.
- Real curated UNDER-coverage found: consumables (value/weight/keyword corrections incl. a
  game-breaking 25-weight ingredient) and all 5 creature races (trait-model derivations) are
  Requiem-grounded work the shipped curated patch never did.
- Consumables/armor/magic/npc field checklists produced 843 field writes with zero write failures,
  masters sorted, REQ_NULL clean.

## Test artifacts

Raw lane derivations, coverage join, adjudication, write log, scoring: this folder.
The disposable test plugin: MO2 mod folder `houseCARL - S4 Validation - Gray Cowl`
(`S4 Validation - Gray Cowl.esp`, INACTIVE, never enabled) — keep for inspection or delete freely.
