# Recon — Requiem skills rebuild

*Author: Aaron (+ Claude session) · 2026-10-30*

**Status:** pass 1 is done and accepted with the intent (2026-10-30). The pass 2 targets below are
**proposed by Claude, for Aaron to review and add to**. Once agreed, they run as an agent fan-out.
The pass 2 targets that need Aaron (his patching sessions) run as he does the work.

## Pass 1: what is already known (do not re-research)

The public summary is in `../../control-eval-2026-10/`. Read it in this order:

| Read | For |
|---|---|
| [`distillation/DISTILLATION.md`](../../control-eval-2026-10/distillation/DISTILLATION.md) | **Start here.** Master lists of what helped (42), hurt (55) and was unnecessary (~86% of the lines); per-skill verdicts; the corrected fact sheet (40 facts, each with status and source); open decisions |
| [`distillation/<skill>.md`](../../control-eval-2026-10/distillation/) | Per skill: helped, hurt and unnecessary tables, corrected facts, and a **rewrite brief** (keep, add, delete, target size, where each piece lives) |
| [`distillation/cross-cutting.md`](../../control-eval-2026-10/distillation/cross-cutting.md) | Coverage procedure, stance, fan-out and lane briefs, gates, contradictions; an **ownership map** (one owner per cross-skill decision) |
| [`drill-summary.md`](../../control-eval-2026-10/drill-summary.md) | How the first synthesis was corrected (the wins are joint) |
| [`README.md`](../../control-eval-2026-10/README.md), [`runs-table.md`](../../control-eval-2026-10/runs-table.md), [`tasks/`](../../control-eval-2026-10/tasks/) | Method, per-run numbers, per-task right/wrong |

**Raw evidence (local only, never published).** The eval harness `arr-requiem-eval` sits as a
sibling folder next to this repo's checkout. Its `CLAUDE.md` describes it. Use it only to check a
claim:

| Path in the harness | Holds |
|---|---|
| `drill/distill/` | The same distillation files (unpublished originals) |
| `drill/r3/INTEGRATED.md`, `drill/r3/CRITIC.md` | The final integrated read, with the fact sheet in §3, and the critic's verdict ("mined enough to choose a path") |
| `drill/r3/R10-checklist/` | The 95-item deciding-point checklist and its scorers. **Not frozen:** 12 fixes are listed |
| `drill/r3/R6-weapon-code/`, `drill/r2/U6-facts-live/`, `drill/r3/R12-csharp-build/` | The Reqtificator boundary, read from its C# source and the output's records |
| `drill/r2/U3-reference-bugs/` | 87 reference-bug clusters by judge count, for the separate BUGS.md review |
| `drill/absolutes/requiem-<skill>.json` | Every prescriptive line in the tested skills, with rule ids (`rp#14`, `npc#18`, …) used throughout the distillation |
| `drill/{coverage,absolutes,facts,rescore,consistency,process}/`, `drill/r2/U*`, `drill/r3/R*` | Round 1–3 findings, per task and synthesised |
| `scoring/`, `runs/` | Judgements, diffs, minings, transcripts, plugin dumps |
| `harness/` | The eval harness, to re-use for the re-run (C7) |

**The Reqtificator's C# source** ships inside the ARR instance (mod "Authoria - Reqtificator And
Requiem Tweak"). It is proven to be the build that produced the current output. Cite the code for
boundary facts.

## Pass 2: proposed targets

### A. Anthropic's current skill-authoring guidance (intent C2, C3)

| ID | Question | Where to look |
|---|---|---|
| A1 | What does Anthropic currently recommend on skill structure: SKILL.md size, progressive disclosure into references, when to split or merge skills, scripts in skills? | Anthropic docs: Agent Skills overview and best practices; the Claude Code skills docs; the skill-creator skill |
| A2 | Description and auto-triggering guidance: wording, length budget, and how the listing is presented and truncated. Has anything changed since the 2026-07-29 compression (#43)? | Same, plus Claude Code release notes |
| A3 | Hooks in plugins: can the authoria-requiem plugin ship a PreToolUse hook that gates houseCARL writes on an "understood the mod" artefact (intent open question 2)? What are the mechanics and limits? | Claude Code hooks and plugin docs; the AI-native SDLC playbook, lesson 11 |
| A4 | Has `standards/` (synced from houseCARL) drifted from houseCARL HEAD or from Anthropic's guidance? | houseCARL `standards/` vs this repo; A1–A3 |
| A5 | Current eval and trigger-measurement guidance (skill-creator evals, description optimisation) vs this repo's §6.5 fan-out | skill-creator skill, Anthropic docs |

### B. Aaron's own patching sessions (intent open question 5)

Aaron patches real mods for ARR with Claude in the normal interactive way. Planned: an armour mod
and a quest mod. Each session keeps a **decision log**: one file in the work folder, appended as
the session runs. Each entry holds:

- the question;
- the options;
- what Claude recommended, with its evidence;
- what Aaron chose and why;
- anything Claude missed that Aaron had to supply.

| ID | Question | Source |
|---|---|---|
| B1 | Which judgement calls recur in real ARR patching (the decision-list catalogue)? | Decision logs |
| B2 | Where did the agent lack knowledge Aaron had to supply? This finds the missing-skill content the eval couldn't see. | Decision logs, transcripts |
| B3 | Does the dissection (whole mod, scripts included) catch what matters on an armour pack and a quest mod? What did it miss? | Transcripts, Aaron's review |
| B4 | Which house conventions came up, and what did Aaron decide (intent open question 1)? | Decision logs |

### C. Pre-spec checks (no agents needed)

| ID | Question | Where to look |
|---|---|---|
| C1 | Which of the 12 checklist fixes are needed before freezing it as the re-run's primary metric (intent open question 7)? | `drill/r3/R10-checklist/SYNTHESIS.md` in the harness |
| C2 | Is the plan + boundary-fact arm worth running before the rebuild (intent open question 4)? | `drill/r3/INTEGRATED.md` §5 in the harness |

## For Aaron to add

| ID | Question | Where to look |
|---|---|---|

## Findings index (pass 2)

*(Dated, sourced notes go in a `recon/` subfolder as they land.)*
