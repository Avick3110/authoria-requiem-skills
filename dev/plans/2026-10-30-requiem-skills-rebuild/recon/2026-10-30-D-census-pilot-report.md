# D — Census pilot report

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, group D. Method: `D-census-method.md` (draft 2 + models section). Per-type
docs from the pilot: `census/pilot/ARMO.md`, `census/pilot/NPC_.md`. Raw data, scripts and agent outputs stay local in
the eval harness (`arr-requiem-eval/census/`).*

## Scope

| Plugin | Records | Role |
|---|---|---|
| Obi Hedge Knight | 11 (ARMO, COBJ, ARMA) | Fit: the modal armour + recipe shape |
| Grand Solitude | 103 (NPC_, CONT) | Fit: humanoid NPCs + containers |
| Animated Carriages | 229 (NPC_, CONT, ARMO) | **Held out:** one of the 6 eval references; its known reference bugs (U3) are the answer key |

## The pipeline worked end to end

1. **Extraction is exhaustive and mechanical.**
   - houseCARL `form=everything` (json, `to_file`) is read for the patch and for each previous provider.
   - A script diffs them structurally: 343 records → 1,576 change rows → 230 patterns.
   - The script diff matched houseCARL's own delta field-for-field on every record.
   - Every manifest was complete, and all reads came from one epoch.
   - houseCARL's delta *text* elides list elements, so it serves as the cross-check, not the source.
2. **Readers** (Sonnet) annotated 230/230 patterns. A coverage script proves that nothing was skipped.
3. **Verifiers** (Sonnet, fresh context) re-derived samples from live reads. Corrections were merged by script.
4. **Synthesis** (Opus) produced the two per-type docs. Each doc was verified by Sonnet.

## Error rates — every stage's verifier caught something real

| Stage | Checked | Material errors | Cause → fix |
|---|---|---|---|
| Reader: items + containers | 23 patterns | 1 (4%) | Called a disabled mod recipe "ok" because a Requiem iron recipe exists, but that recipe makes Requiem's item, not the mod's. **Brief now requires checking that a replacement produces the same thing** |
| Reader: templated NPCs | 69 | 61 (88%), all one mistake | Hand clean-up of masked perks labelled "baked". **Brief now requires code or output-plugin proof for "baked"**. Not a model limit |
| Reader: other NPCs | 56 | 0 | The baked perk/spell claims were proved from the C# source and the Reqtificator output plugin |
| Template-fit reader (added mid-pilot) | 45 non-fit flags | 6 (precision 87%); plus 3 misses among 13 "fits" | It skipped TemplateFlags (it called inert links misfits) and judged only the first template link. **Next brief: resolve the whole template chain, read the flags, read the template's level and provider, enumerate pools** |
| Synthesis: ARMO | ~30 claim groups | 1 | Called NoArmorRatingRescale load-bearing above the rescale threshold. Also mis-classed it as Requiem-aligned: Requiem.esp uses it on 0 ARMO |
| Synthesis: NPC_ | 34 claims | 1 | A guard flagged as a misfit actually inherits Requiem's guard list two links down the chain |

**Conclusion:** Sonnet is good enough as reader and verifier once the brief is precise. The two big error clusters were
brief ambiguities, not model weakness. Keep a verifier at every stage: each one paid for itself.

## Bug v convention: recall against the held-out answer key

- **The first pass missed the major AC bugs.** Patterns grouped "this NPC gets a template" into one judgement, so no
  reader asked whether *this* NPC's template fits it.
- **Fix (now in the method):** per-record **fit units** for link fields whose target must suit the record. These are an
  NPC's Template, Class and CombatStyle, a recipe's CreatedObject, and so on. Each is judged per row, like numeric
  values.
- **After the fix:**
  - **caught:** AC1 (armored frost troll on a plain troll), AC3 (L100 "Murder Rabbit" on a L1 hare), AC7 (civilians on
    combat pools), AC8 (casters on warrior and guard pools), AC11, and the minor AC2 (Tsun AR);
  - **partly caught:** AC6 and AC12;
  - **rated correct by code:** AC10, which is harmless clutter;
  - **surfaced:** AC14.
- **Structurally invisible:** AC9. The bug is records the patch *didn't* touch, and a census of changes can't see an
  omission. **Proposed addition to Phase 0:** per plugin, list the source mod's patchable records that the patch left
  untouched, so the synthesis can say what ARR leaves alone and spot misses. This also feeds the router's coverage
  procedure.

## Early findings (pilot-sized; the full run must confirm)

- **NPC templating is the core NPC convention.**
  - An NPC is either templated with Stats + SpellList inherited (existing templates kept, retargeted only where Requiem
    does the same), or untemplated with `REQ_KW_AlreadyReqtified` plus the hand-carried build kit.
  - Held out: 175/175 NPCs follow one of the two shapes.
- **The baked NPC layer is a large house convention.**
  - `REQ_KW_AlreadyReqtified` makes the Reqtificator skip an NPC (`ActorAlreadyReqtifiedSkip.cs`). The patch then
    carries the exact 45 perks + 4 spells the build would stamp.
  - Requiem.esp's own NPCs never carry the keyword. **3,239 NPCs across 97 Authoria patches do.**
  - This is the per-patch downstream stance, at scale.
- **The lockpick script is baked on containers.** `REQ_LockpickControl` is attached to every touched container: 53/53
  held out, an exact copy of what the Reqtificator stamps.
- **Armour copies the Requiem comparable.**
  - Obi Hedge Knight = Requiem's iron heavy set, with AR / value / weight summed over slots for a multi-slot suit.
  - Build-stamped keywords (tempering, ranged tier) are written into the patch.
- **Suspected bugs for Aaron's review** (detail and evidence in the two docs). None is filed; BUGS.md stays separate.
  - the Obi suit and main helmet can't be crafted (forge recipe disabled, nothing replaces it);
  - an ArmorAddon first-person mesh path ends in CR LF;
  - a temper recipe asks for 4 iron where Requiem's per-piece sum is 3;
  - 23 Grand Solitude commoners inherit Requiem's bandit template `039CFD`;
  - plus the AC list above.

## Usage (subagent tokens, as reported by the harness)

| Stage | Model | Tokens |
|---|---|---|
| Readers (3 units + template fit) | Sonnet | ~765k |
| Reader verifiers (3 + fit) | Sonnet | ~705k |
| Synthesis (2 docs, incl. fixes) | Opus | ~300k+ |
| Synthesis verifiers (2) | Sonnet | ~333k |
| **Pilot total** | | **~2.1M (about 85% Sonnet)**, plus orchestration in the main session |

**Extrapolation to the full run is uncertain.**
- A naive per-record scaling (343 → 24,329 records, 71×) gives ~100M+ tokens. That overstates it:
  - much of each agent's cost is fixed (briefs, fact sources);
  - patterns repeat across plugins: the lockpick script on 88 plugins is judged once.
- A realistic range is **tens of millions of Sonnet tokens plus a few million Opus**. The per-record fit units (NPC
  templates, armour and weapon stats) are the expensive part, and they are also where the bugs are.

**Levers to cut usage before the full run:**
1. **Condensed fact pack:** precompute about 5k tokens of Requiem / Reqtificator facts once, instead of every agent
   reading the distillation and three drill reports.
2. **Cap the verifier sample** at about 3 examples per (type, path, op) cluster. "Every baked pattern" pulled 61 of 69
   near-identical patterns.
3. **Stage the run:**
   - first COBJ, NPC_, ARMO and WEAP (about 68% of records);
   - Aaron reviews those docs;
   - then the long tail.
4. Keep the per-record fit units only for fields that decide gameplay.

## Mechanisation test (2026-10-30, after the pilot)

Two scripts, measured against the verified pilot labels (`classify.py`, `fit.py` in the harness):
- `classify.py` labels change patterns.
- `fit.py` resolves the template chains and judges fit.

Their rules come from code, the Reqtificator output, engine template-flag semantics, or Requiem.esp data, never from
the pilot's answers. The inputs are houseCARL dumps:
- Requiem.esp's 3,106 ARMO;
- the RFTI output's kit;
- recipes by created item;
- the full template chains (about 2,000 records, in 8 calls).

| | Decided by script | Agreement with verified labels |
|---|---|---|
| Change patterns | **136 / 230 patterns (59%), 1,180 / 1,576 rows (75%)** | **132 / 136 (97%)**. The 4 disagreements are trivial or the script is righter: VMAD header ok v noise; a masked level edit the script calls noise, as U3 AC12 does; the uncraftable-recipe flag, stated more firmly |
| NPC template fit | 61 / 246 NPCs (25%) | 53 / 61 binary fit / non-fit (87%). 2 of the 8 "disagreements" are inert links the verifier itself corrected. It catches the held-out troll, rabbit, Telvanni mage and civilian bugs |

The rules that carry it:
- **template-mask** (64 patterns);
- **the Reqtificator kit on AlreadyReqtified NPCs** (49);
- **lockpick VMAD**;
- **build-stamped armour keywords**;
- **armour AR / value / weight v the Requiem comparable**, including multi-slot sums (the Obi suit = Requiem iron
  cuirass + gauntlets + boots);
- **disabled recipe with no replacement**;
- **noise** (bookkeeping, reorders).

**Still residual:**
- 44 patterns (recipe ingredients and conditions, odd keyword adds, mesh swaps, renames, script swaps);
- 185 fit records, where the role is ambiguous or the chain is partial.

Next rules that look mechanisable:
- recipe ingredients v the comparable's recipe;
- keyword adds v the comparable's keyword set;
- add + remove pairs that differ only in order.

The LLM then handles only the residual, and gets **pre-resolved facts** (chain, levels, archetypes, comparables)
instead of making live reads. That was most of each reader's cost. Verifiers check each rule once plus a residual
sample, not per row.

**Revised estimate: LLM tokens fall by roughly 70–80%** against the pilot method, i.e. the full run moves from
"tens of millions" to **~5–25M**. That is still uncertain. The untested types (WEAP, magic, LVLI, races) need their own
comparable rules, and their residual share is unknown.

## Recommendation

**Go to the full run, staged.** Apply the four brief and pipeline fixes and the untouched-records addition first, and
re-measure tokens on stage 1 before committing to the tail.
