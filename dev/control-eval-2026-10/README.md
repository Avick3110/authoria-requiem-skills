# Control eval against ARR's handmade patches: results

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (results of an executed plan; supersede, don't edit).*

Results of the plan in [`../plans/2026-10-28-control-eval-against-handmade-patches.md`](../plans/2026-10-28-control-eval-against-handmade-patches.md):
do the authoria-requiem skills (1.10.0, `main` at 74a30e9) still beat a bare **Opus 5.5 +
houseCARL 2.x** on real Requiem patching jobs?

## Answer

Yes, but only part of the set is worth keeping, and the set as written costs too much and states its rules too absolutely.

- **Verdicts:** the skill arm won 5 of 6 tasks and lost 1 (favor-quests).
- **Quality:** on the tasks it won, the median judge quality was about 66 for the skill arm against about 30 for the control.
- **Cost:** about 4.2× the money and 2.2× the time (sum of per-task medians).
- **What drove the wins (corrected after re-mining, see [`drill-summary.md`](drill-summary.md)):** joint effects. The router's coverage plan, one corrective fact about where the Reqtificator stops, and a domain skill naming which live comparable to read. Values come from the load order. The first synthesis credited the router alone, which was wrong.
- **What added nothing:** generic patching craft (weapon/armor ladders, keywords, COBJ, masters, live-winner authority). The control did it just as well.
- **What caused the skill arm's own wrong calls:** absolute, unscoped rules.

Recommended action per skill:

| Action | Skills |
|---|---|
| Rewrite | router (`requiem-patching`), weapon, script |
| Update | npc, race, magic, leveled-list |
| Keep as short fact sheets | ammo, armor, consumable |
| Fold into npc | perk-assignment |

The full argument is in [`synthesis.md`](synthesis.md). It was later corrected by three rounds of re-mining ([`drill-summary.md`](drill-summary.md)) and a per-skill distillation of what helped, what hurt and what was unnecessary ([`distillation/DISTILLATION.md`](distillation/DISTILLATION.md)). Where they disagree, the later documents win.

| Task | Source mod | Verdict | Judge median control → skill |
|---|---|---|---|
| [animated-carriages](tasks/animated-carriages.md) | Animated Carriage | **BEAT** | 28 → 66 |
| [falkreath-shades](tasks/falkreath-shades.md) | Gravewind (Falkreath Shades) | **BEAT** | 30 → 68 |
| [favor-quests](tasks/favor-quests.md) | Favor Quests Separated | **LOST** | 90 → 90 (one skill run 45) |
| [mage-core](tasks/mage-core.md) | Magecore | **BEAT** | 35 → 62 |
| [rot-below](tasks/rot-below.md) | The Rot Below | **BEAT** | 30 → 42 |
| [triumvirate](tasks/triumvirate.md) | Triumvirate - Mage Archetypes | **BEAT** | 38 → 72 |

## What's here

| File | What |
|---|---|
| [`synthesis.md`](synthesis.md) | Cross-task answer, per-skill recommendations, the cross-cutting findings with evidence, every skill section labelled helped / noise / harmed / mixed / unused with an action, missing knowledge, costs, caveats |
| [`tasks/<task>.md`](tasks/) | Per-task analysis: right and wrong per arm (major first), verdict, per-section labels with evidence, missing knowledge, costs |
| [`drill-summary.md`](drill-summary.md) | The three re-mining rounds and the corrections they made to the first synthesis |
| [`distillation/`](distillation/DISTILLATION.md) | What helped, what hurt and what was unnecessary, per skill, with rewrite briefs and the corrected fact sheet |
| [`runs-table.md`](runs-table.md), [`runs.csv`](runs.csv) | One row per run: diff checklist against the handmade patch, judge scores, cost, wall time, houseCARL calls, subagents, skills loaded, stop-to-ask |

The following stay local because they hold machine paths or raw content: transcripts, mining output, the 42 per-run judgements, the field-level diffs and the harness itself. Trace references in the docs (`L1064`, `R3588`, `G29`, `D71`) point into them.

## Method, as run

- **Tasks.** We took a census of ARR's ~250 handmade Requiem patches and picked six. None of them is named in skill text or used in the `dev/` corpora.
- **Blinding.** For each task, the handmade patch (and any side files) was moved out of the instance, every plugin after its load position was disabled, and the mods folder was swept for leaks. One shared blinded state served all six tasks, in a dedicated test profile.
- **Arms.**
  - Control: the authoria-requiem skills are absent from the listing and from disk. houseCARL's own skills stay, because they are part of the baseline.
  - Skill: the skills are installed from `main`, and the agent is told to load `requiem-patching` first and say what it changed about its plan.
  - A contamination audit confirmed that no control run saw the skills.
- **Brief.** Each run got one line: *"Patch `<mod>` for Requiem. Write the patch as a new plugin in a new mod folder named `HC_Eval_<task>_<rid>`; don't change any existing plugin or mod."* The run id is opaque, so the agent can't see which arm it is in.
- **Runs.** 3 per arm per task on Opus 5.5, all headless, with houseCARL pointed at the instance through an eval-only config that holds no in-place consents.
  - The first six runs logged no thinking summaries. They were re-run, and the originals are kept as extra `-nothink` rows. They are excluded from the medians.
  - 4 skill runs stopped to ask, all at the NPC skill's stat-mode gate. Each was resumed with a neutral *"Your call — use your best judgement"*. Their cost and time include both sessions.
- **Scoring.**
  1. **Diff checklist.** Each plugin was diffed record by record and field by field against the handmade patch. This is a checklist of what to look at, not a score, because the reference is often wrong or incomplete. favor-quests shows why: the correct runs share no records with the reference.
  2. **Arm-blind Opus judge.** One judge per run, reading the live load order, ruled every change in both plugins (the candidate and the reference) right / wrong / debatable with a severity, plus does-the-job and works-in-game.
  3. **Miner.** An Opus miner traced every value to its source: skill text, a load-order analogue, or the agent's own reasoning. It also recorded skill usage and process.
  4. **Analyst.** A per-task analyst joined the judgements to the mining output to produce the arm verdict and the section labels.
  5. **Synthesis.** One cross-task synthesis pulled the six analyses together.
- **Reference bugs.** The judges also found bugs in the handmade patches. They concern ARR list content, so they're reviewed and filed in ARR's release tracker. This copy leaves them out, though the inline "reference wrong" calls stay in the analyses as evidence.

## Costs of the eval itself

- Agent runs: about $315.
- Mining: about $249.
- Judging and analysis: on top of those figures.

Per-run costs are in [`runs-table.md`](runs-table.md). Each is the transcript's final result line, which is cumulative when background subagents run, so it is never summed.
