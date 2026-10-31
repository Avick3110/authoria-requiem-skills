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
| A6 | **Root-cause the tool traps T1–T5** (DISTILLATION §3e) instead of trusting their "HC" fix column. Per trap: (a) what the agent was trying to do and which rule, fact or goal drove it; (b) was the goal needed (e.g. is PRKZ=0 a real defect for the engine or the Reqtificator?); (c) does a houseCARL 2.x route exist (another tool, composition or ordering); (d) verdict: skill-induced → delete the rule / wrong route → at most 1 line naming the right one / real houseCARL gap → houseCARL issue / non-problem → nothing. Added by Aaron 2026-10-30. **Not** a tool-name substitution: the rebuild ships no tool recipes or call blocks (HU-T6: 0 refused calls on 1.x names) | Harness transcripts (`runs/`) at the cited row refs; `drill/process/`, `drill/absolutes/` ledgers; houseCARL 2.x docs; live check on the test profile where needed |
| A7 | **Install path vs plugin hooks.** The live install is a folder copy into the user skills directory, not a plugin install. Do plugin-shipped hooks (A3) load at all on that path? What install path does a hook-gated rebuild need? Added 2026-10-30, agreed by Aaron | Claude Code plugin, hooks and skills docs; this repo's README install section |

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
| B5 | **Which domains does ARR actually patch?** Survey ARR's existing Reqtificated / Requiem patches by domain, to settle which untested domains keep a short version (intent open question 3). Added 2026-10-30, agreed by Aaron | The live ARR instance (modlist, patch mods, record types via houseCARL), read-only |
| B6 | **Which open house conventions will the armour and quest sessions not touch?** Those need Aaron's direct call at spec time. Added 2026-10-30, agreed by Aaron | intent open question 1; DISTILLATION §7 |

### C. Pre-spec checks (no agents needed)

| ID | Question | Where to look |
|---|---|---|
| C1 | Which of the 12 checklist fixes are needed before freezing it as the re-run's primary metric (intent open question 7)? | `drill/r3/R10-checklist/SYNTHESIS.md` in the harness |
| C2 | Is the plan + boundary-fact arm worth running before the rebuild (intent open question 4)? | `drill/r3/INTEGRATED.md` §5 in the harness |

### D. Census of ARR's handmade Requiem patches (added by Aaron, 2026-10-30)

Aaron's method, as used in `arr-ube-projects` for the UBE armour conventions:
- count what the authors did across the whole corpus, per class and per dimension;
- state the consensus with numbers and name the exceptions;
- where patches disagree, propose why;
- check that the candidate rules reproduce held-out patches.

In nearly every case a consensus is eventually found. The census is the evidence base for the
domain methods, the house conventions (intent open question 1) and the decision-list catalogue
(B1), and it covers the domains the eval never tested. **Method and brief to be agreed before it
runs.**

| ID | Question | Where to look |
|---|---|---|
| D1 | Per domain and per field: what do ARR's handmade patches change against the source mod, with what consensus and which exceptions? | The 250 plugins in "Authoria - Requiem Patches" (+ Aaron's other handmade patches: corpus to confirm), read-only via houseCARL |
| D2 | Where patches disagree, why? Candidates: patch age or Requiem version, author, the mod's tier, upstream v baked, a reference bug | D1 output; patch dates; the U3 reference-bug clusters |
| D3 | Which "conventions" are really baked Reqtificator output? They should be separated from the input layer the skills teach. | D1 output against the boundary facts (R6 / U6 / R12) |
| D4 | **Locks (CONT/DOOR)**: 136 patches, no owning skill (B5). The conventions for lock levels, REQ_ scripts and keys | D1, locks class |
| D5 | Do the candidate rules reproduce held-out patches (the 6 eval references held out from rule-making, as in the UBE battery)? | The census rules v the 6 references |

## For Aaron to add

| ID | Question | Where to look |
|---|---|---|

## Findings index (pass 2)

*(Dated, sourced notes go in a `recon/` subfolder as they land.)*

| Target | Note |
|---|---|
| A7 | [`recon/2026-10-30-A7-install-path-and-plugin-hooks.md`](recon/2026-10-30-A7-install-path-and-plugin-hooks.md) |
| B5 | [`recon/2026-10-30-B5-arr-patch-domains.md`](recon/2026-10-30-B5-arr-patch-domains.md) |
| B6 | [`recon/2026-10-30-B6-conventions-not-covered.md`](recon/2026-10-30-B6-conventions-not-covered.md) |
| D (method) | [`recon/D-census-method.md`](recon/D-census-method.md) |
| D (pilot) | [`recon/2026-10-30-D-census-pilot-report.md`](recon/2026-10-30-D-census-pilot-report.md); per-type pilot docs in `recon/census/pilot/` |
| D (stage 1: NPC_, ARMO, WEAP, COBJ) | [`recon/2026-10-30-D-census-stage1-report.md`](recon/2026-10-30-D-census-stage1-report.md); per-type docs + conventions.json in `recon/census/stage1/` |
| D (stage 2: locks, lists, magic, races, perks/AI, consumables, quests, gear/NPC addendum, untouched) | [`recon/2026-10-30-D-census-stage2-report.md`](recon/2026-10-30-D-census-stage2-report.md); domain docs + conventions.json in `recon/census/stage2/` |
| D (split hunt 1: NPC workflow) | [`recon/2026-10-30-split-npc-workflow.md`](recon/2026-10-30-split-npc-workflow.md): marker iff no inherited SpellList (99.5%); bake v upstream tracks the skills-era live runs |
| D (split hunt 2: perk values) | [`recon/2026-10-30-split-perk-values.md`](recon/2026-10-30-split-perk-values.md): benefits halved onto 5% steps (10% floor), drawbacks kept, AP snaps to Requiem steps, flat v-type adds to a 40 / 45 / 50 table |
| D (split hunt 3: armour AR 0) | [`recon/2026-10-30-split-armour-ar0.md`](recon/2026-10-30-split-armour-ar0.md): the slot decides; core slots keep AR, extra slots / clothing / replicas / wigs carry 0 (96.8%) |
| D (split hunt 4: enchant charge) | [`recon/2026-10-30-split-enchant-charge.md`](recon/2026-10-30-split-enchant-charge.md): 500 × enchant tier (generic), MR staff ladder 500–6000, uniques onto the ladder with a 3000 ceiling, removed / scripted enchants unlimited |
| D (split hunt 5: recipe retirement) | [`recon/2026-10-30-split-recipe-retirement.md`](recon/2026-10-30-split-recipe-retirement.md): per-piece curation (degraded copies, colour variants, armour-only mods); 689 retired items obtainable nowhere, 250 of them in COCKS-listed mods ([`census/recipe-orphans.csv`](recon/census/recipe-orphans.csv)) |
| D (split hunt 6: death-item loot) | [`recon/2026-10-30-split-death-item-loot.md`](recon/2026-10-30-split-death-item-loot.md): gold stays on humanoids, off creatures (Requiem's rule extended); generic gear off regular enemies, bosses keep rewards; species parts stay |
| Questions for the list authors | [`recon/2026-10-30-questions-for-list-authors.md`](recon/2026-10-30-questions-for-list-authors.md): what the split hunts couldn't settle from records (shareable with Heisen) |
