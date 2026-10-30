# D — Census of ARR's handmade Requiem patches: method

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: LIVING until the census runs, then ARCHIVE. Recon pass 2, group D. **Draft 2, for Aaron to
agree.***

## Agreed so far (Aaron, 2026-10-30)

- **Corpus:** the 250 plugins in "Authoria - Requiem Patches" (24,329 records).
- **Read exhaustively:** every plugin is read for everything it changed.
- **Every agent has a verifier.**
- **One synthesis doc per record type**, not a single doc.
- **Hold out the 6 eval references** from rule-making and use them to validate (D5): Mage Core,
  Triumvate, Falkreath Shades, The Rot Below, Animated Carriages, Favor Quests Seperated
  (798 records).
- **Separate the baked layer** (Reqtificator output stamped into the patch) from the input layer
  (D3).
- **A wrong patch is not a convention.** The run separates house conventions from reference bugs at
  every stage (see "Bug v convention" below), because a consensus mined from buggy references
  would teach the bugs.
- **Readers are cut by record type across plugins, sized by measured work, not by plugin batch.**
  Plugins range from 1 record to 4,498 records.

## Pilot finding (2026-10-30)

`housecarl_records` with `project.form=delta`, `versus=previous_provider` and one patch as
`source=` returns **every differing field per record**, mechanically. Tried on Obi Hedge Knight
(11 records, 9 diffs).
- Records the patch *defines* return "no previous provider" and need `form=everything`.
- The text render elides list elements, so extraction uses `format=json`, enough `depth=` and
  `to_file=`. The manifest's `row_count == total` proves completeness.
- `previous_provider` can be a third-party patch (here, a UBE patch). Each row records which
  provider it was compared against.

## Phase 0 — Extract and measure (mechanical)

- **Extraction:** per plugin, write two files to the local census store:
  - `delta.jsonl`: every overridden record vs its previous provider;
  - `new.jsonl`: every record the patch defines, read in full.

  A few extraction agents run per plugin, since this step is mechanical.
- **Completeness check (a script, not an agent):**
  - every manifest has `row_count == total`;
  - per-plugin, per-type counts match the header parse (`rt.json` from B5);
  - only the expected "defines" errors;
  - one epoch throughout.
- **Plugin headers (a script):** per plugin, give the readers its context:
  - masters and previous providers;
  - file date and ESL flag;
  - record counts;
  - baked-layer markers, e.g. the share of records carrying Reqtificator-stamped keywords;
  - held-out status.
- **Measure (a script):** count the changed fields per record type and per sub-class. That count
  is the reader workload. It is known only after this phase.
- **Store:** local only, in the eval harness (`arr-requiem-eval/census/`), never published.

## Phase 1 — Read (record type across plugins)

**Work units** are cut from the Phase 0 field counts, not by plugin:

- **Big types are split by sub-class, so a reader sees one homogeneous slice across all plugins:**
  - COBJ: craft / temper / breakdown;
  - ARMO: heavy / light / clothing / jewellery / shield;
  - WEAP: by weapon type;
  - NPC_: templated / stat-set / creature / unique;
  - CONT: locked / unlocked;
  - and so on.

  A slice still over budget is split again by plugin.
- **Small types are bundled into one unit**, up to the same budget. Examples: AMMO + PROJ,
  ALCH + INGR, LVLN + LVSP, OTFT, QUST + DIAL + INFO.
- **The unit budget** (changed fields per reader) is set by the pilot. Agent count follows from it
  rather than being fixed up front. From record totals alone: COBJ 5,338, NPC_ 5,193, ARMO 3,573,
  WEAP 2,558, then a long tail.

**Each reader writes one ledger row per changed field of every record in its unit:**
- plugin, FormID, EditorID, type, sub-class;
- field, before → after, and the previous provider;
- **pattern:** what the change is;
- **comparable:** the Requiem record the value matches, read live, if any;
- **layer:** input, or baked Reqtificator output (per the pass-1 boundary facts R6 / U6 / R12);
- **correctness flag** (below);
- **confidence.**

The plugin headers travel with each unit, so a reader knows each row's mod context.

**A verifier per reader (fresh context):**
- coverage is checked by a script: every delta field in the unit has a ledger row;
- correctness: the verifier re-derives a stratified sample from live reads. The sample is ≥10% of
  rows, plus every row flagged baked or suspect, plus every pattern class.

A unit that falls short on coverage, or over the error threshold, is rerun on the flagged rows.

## Bug v convention (runs through every phase)

| Stage | Check |
|---|---|
| **Seed** | Readers and synthesisers get the known-bug material: the 87 U3 reference-bug clusters (48 CONFIRMED / 39 PLAUSIBLE) and the corrected fact sheet (DISTILLATION §6) |
| **Ledger row** | `correctness` is one of four values: **ok**; **conflicts-comparable** (the value disagrees with the live Requiem comparable); **conflicts-code** (it contradicts the Reqtificator's code); **known-bug** (it matches a U3 cluster). Each flag carries its evidence |
| **Synthesis** | Every consensus is classed as one of four things. **Requiem-aligned**: Requiem's own records do the same. **House convention**: it diverges from Requiem, consistently and deliberately, so it's Aaron's call to confirm. **Suspected systemic bug**: a consensus that contradicts code or a comparable with no sign of intent. **Outlier bug**: an exception explained by nothing but error |
| **Rules** | A candidate rule is built only from rows flagged ok or house convention. Suspect rows are reported, never averaged in |
| **Validation (D5)** | Rules are scored on the held-out references' non-suspect fields. Where a rule "fails" on a known-bug field, that counts as a pass and is reported separately |
| **Output** | `census/suspected-bugs.md` collects every suspected bug with its evidence, for Aaron's review. Filing to `arr-release/BUGS.md` stays a separate step |

## Phase 2 — Synthesise (one doc per record type)

- **Docs:** one doc per record type, with the small ones grouped. A synthesiser merges its type's
  reader slices.
- **Each doc, per field:**
  - the consensus with counts, and the exceptions;
  - for each disagreement, the candidate reasons (patch age or Requiem version, author, the mod's
    tier, upstream v baked, bug) with evidence;
  - baked vs input, separated;
  - the bug-v-convention class;
  - a candidate rule;
  - the open calls for Aaron.
- **Validation:** rules are built from the 244 non-held-out plugins, then tested on the 6 held-out
  references (D5).
- **Locks (D4)** come out of the CONT, DOOR and REFR docs.
- **A verifier per doc:** it recounts every consensus figure from the ledgers, and checks each rule
  and each bug-v-convention class against its cited rows.
- **Output:** `recon/census/<TYPE>.md` (public: mod names and FormIDs are fine, no machine paths),
  plus an index and `suspected-bugs.md`.

## Models and usage (proposed 2026-10-30)

Two rules keep usage down:

1. **Scripts do anything mechanical.** No model is needed for it.
2. **Agents judge patterns, not rows.**
   - A script flattens the delta JSON into ledger rows.
   - It then groups identical changes, e.g. `WorkbenchKeyword 088105:Skyrim.esm → AD3B01:Requiem.esp`
     × every COBJ that carries it. The group is judged once.
   - Per-record judgement is kept only for values that need a comparable (AR, damage, value,
     weight, levels, magnitudes).

| Step | Who |
|---|---|
| Extraction (houseCARL `to_file` calls) | Haiku |
| Completeness check, plugin headers, field counts, row flattening, pattern grouping | Script |
| Coverage check (every delta field has a ledger row) | Script |
| Readers: comparable lookup, layer, correctness flag | Sonnet |
| Reader verifier: re-derive the sample from live reads | Sonnet (a fresh context; a second Sonnet is enough when the coverage half is scripted) |
| Consensus counts per type | Script, from the annotated ledger |
| Synthesis, small types | Sonnet |
| Synthesis, big or contested types (NPC_, COBJ, ARMO, WEAP, SPEL / MGEF / ENCH, CONT for locks), and every bug-v-convention class | Opus |
| Synthesis verifier: recount (script) + rule-v-citation check | Sonnet |
| Held-out validation (D5) scoring | Script + Sonnet for disputed fields |

The pilot runs its readers on Sonnet. If the verifier's error rate is too high on a slice, that
slice's sub-class moves to Opus for the full run.

## Pilot (before the full run)

- **What:** all three phases on a small slice:
  - Obi Hedge Knight (armour + COBJ, the modal shape);
  - one mid-size NPC patch;
  - Animated Carriages (held out, with known U3 bugs, to test the bug flags).
- **Produces:** ARMO and NPC_ docs from that slice.
- **Measures:**
  - cost per changed field;
  - the reader budget;
  - the verifier's error rate;
  - whether the bug flags catch the known AC bugs.
- **After it:** Aaron sees one example per-type doc, and the full run's unit count and cost are
  quoted from the measurements.
