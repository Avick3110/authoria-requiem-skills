# Description compression + full §6.5 revalidation — 1.10.0

*Author: Aaron (+ Claude session) · 2026-07-29*

*Class: ARCHIVE once executed (plan frozen at close). Chartered from issue
[#43](https://github.com/Avick3110/authoria-requiem-skills/issues/43); mirrors houseCARL
[PR #295](https://github.com/Avick3110/houseCARL/pull/295) (issue #294), which did the same job
there and amended the standard upstream.*

## Charter

The 11 skill descriptions total ~8,290 chars (~2,070 est. tokens) of always-resident context —
alone consuming Claude Code's ~2,000-token budget for the *entire* skill listing, which causes
entry truncation and degraded routing (#43). Compress all 11 to the amended
`HOUSECARL_SKILL_AUTHORING.md` §3.5 target (200–400 chars; action-first lead, distinctive
trigger nouns per §3.2, compact pushy tail per amended §3.3 only where an early-load hazard
exists), then revalidate every description via the full §6.5 fresh-context fan-out — mandatory
on any `description:` change, and never yet run empirically for the original nine (closing the
standing BACKLOG entry). Decisions settled with Aaron up front:

- **Standard**: already amended upstream in houseCARL (#294); this PR re-syncs the `standards/`
  copies rather than re-litigating. The old 60–180-word target predates the listing-budget
  measurement.
- **Fan-out method**: mirrors PR #295 — one fresh-context judge per eval query, shown the full
  lettered menu of all 11 *new* descriptions (under-test skill unflagged) + NONE, reasoning
  before verdict; one adjudicator per skill scores recall/specificity from the reasoning
  (§6.5 step 4). Gates: recall ≥ 80%, specificity ≥ 50% — binding; the char band is advisory
  (over-band lengths justified per §8 item 7).
- **Judge model**: Opus, high reasoning effort, no tools (Aaron's explicit pick — a deliberate
  upgrade over PR #295's sonnet-low judges; noted in the archived method string). Adjudicators:
  Opus. No Fable-model agents anywhere.
- **Contamination guard**: the live install is moved aside for the fan-out window so freshly
  spawned judges don't carry the OLD descriptions in their own available-skills listing;
  restored afterwards via the standard robocopy sync (the live copy is disposable by design).
- **Eval sets**: queries kept verbatim; heterogeneous schemas deliberately NOT restructured
  (PR #295 precedent). Results land as a uniform `revalidation_2026-07-29_43` block in each
  skill's `eval_set.json`; the two `results-2026-07-15.json` archives stay in place, superseded
  by note.
- **Folded in**: the standing BACKLOG description-tweak for `requiem-perk-assignment` (its
  2026-07-15 fan-out Q8 miss — the "custom perk clashes with a Requiem crafting gate" framing)
  rides the rewrite, so its re-measure comes for free.

## Close-out

Branch `claude/description-compression-43` → PR (Fixes #43): standards re-sync, 11 rewritten
descriptions, 11 revalidation blocks, `plugin.json` → 1.10.0, CHANGELOG entry mapping each
change to its driver, CLAUDE.md deviations paragraph refreshed (all 11 now validated under the
current standard), BACKLOG pruned of exactly the two entries this closes, session handoff.
Post-merge: live-install sync, then a fresh-session `/doctor` measurement posted to #43.

*(Filled at close:)* final per-skill lengths, fan-out scores, and any fix-loop iterations are
recorded in each skill's `evals/eval_set.json` revalidation block; the session handoff carries
the narrative.
