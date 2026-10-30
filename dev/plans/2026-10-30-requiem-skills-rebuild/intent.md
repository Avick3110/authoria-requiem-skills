# Intent — Requiem skills rebuild

*Author: Aaron (+ Claude session) · 2026-10-30*

**Status: agreed by Aaron, 2026-10-30 (draft 1, unchanged).**
Drafted by Claude from the 2026-10-30 discussion and interview, which followed the control eval
(`../2026-10-28-control-eval-against-handmade-patches.md`) and three rounds of re-mining its
evidence.

## Problem

The 11 `authoria-requiem` skills were written in 2026-07 for weaker models (Opus 4.8, GPT 5.5) and
houseCARL 1.x. The control eval (6 real ARR patching jobs, 42 runs) shows they still beat a bare
Opus 5.5 + houseCARL 2.x on 5 of 6 tasks, but:

- **Cost.** The skill arm cost 3.9× the money and ~1.7× per right major change.
- **Dead weight.** About 86% of the ~12,660 lines did nothing. That covers duplication across
  skills, houseCARL 1.x recipes, generic craft the model does unaided, and content the tasks never
  exercised.
- **Wrong calls of their own.** Five facts in the skills are false (REQ_NULL = inert, the AR caps,
  RegenHpInCombat, lockpicking as a DLL, no ×4 bow rescale). Absolute "always / never" rules get
  applied out of scope, rules contradict each other across skills, and stop-to-ask gates add
  nothing.
- **Wrong doctrine for ARR.** The skills forbid the downstream stance (baking the Reqtificator
  layer), which ARR uses per patch.
- **Wrong workflow.** The skills don't support the way patching is actually done here: understand
  the whole mod (scripts included) → agree the judgement calls → execute. They neither require
  that understanding before writing nor produce a decision list.

What the skills do well is narrow: the router's coverage plan, one corrective fact about where the
Reqtificator stops, domain methods that name which live comparable to read, and a handful of value
frames. Today these are buried in the other 86% and undercut by the harms.

## Proposed outcome

A rebuilt `authoria-requiem` skill set for the ARR list that:

1. **Keeps every measured win:**
   - the coverage procedure (enumerate, read each record before deciding, route, reconcile);
   - the Reqtificator boundary facts;
   - the domain comparable methods;
   - the value frames that decided points.
2. **Removes every measured harm.** False facts are corrected from the Reqtificator source and live
   reads, each with a probe that re-checks it. Absolutes are scoped with their counter-cases. Each
   cross-skill decision has one owner. Gates are replaced by stated defaults.
3. **Puts understanding first.** The agent understands the whole mod, scripts included, before it
   writes. It ends planning with a decision list for Aaron's calls. Upstream is the default stance,
   and the downstream bake package is stated as the difference.
4. **Is much smaller:** on the order of 1,500 lines, a single agent by default, and houseCARL 2.x
   only.
5. **Triggers on its own** when an ARR patching job comes up, and conforms to Anthropic's current
   skill-authoring guidance and this repo's standards.

## Affected users and systems

- **ARR list maintainers.** Aaron for now (Heisen is out of action, so Aaron holds every gate), and
  Heisen later.
- **This repo:** the `authoria-requiem` plugin, its CHANGELOG and version, its evals, and `standards/`.
- **The live install** (`~/.claude/skills/authoria-requiem`), synced after release.
- **houseCARL 2.x:** a dependency only. Its tool names and write shapes are the contract; nothing is
  changed there in this project.
- **The eval harness** (local, `arr-requiem-eval`): re-used to measure the rebuild.
- **The ARR instance:** read-only for verification and evals; test profile only.

## Constraints

- **C1.** ARR only. ARR's conventions (baked "Authoria - Reqtificated" patches, merging off,
  per-patch stance, house conventions) are first-class. Other Requiem lists are not a target.
- **C2.** It conforms to Anthropic's latest published skill-authoring guidance, and to this repo's
  `standards/` (HOUSECARL_SKILL_AUTHORING §3–§8, the 500-line cap, name == folder, the description
  cap, the §6.5 trigger fan-out).
- **C3.** It must trigger on its own, measured with the §6.5 fan-out (fresh-context agents, menu
  variant).
- **C4.** It works on current Opus and Sonnet. No scaffolding for weaker models.
- **C5.** houseCARL 2.x tool names and write shapes only. Every recipe kept is tested against 2.x.
- **C6.** Every fact stated as fact is verified (Reqtificator code, live read, or 2+ judges) and
  carries a probe query. House conventions are labelled as such.
- **C7.** Re-running the eval with the existing harness (blinding, judges, the frozen deciding-point
  checklist) must keep the 5 wins, close the favor-quests failure mode, and fix the known harms.
- **C8.** Public repo: publish hygiene (no machine paths, no emails). Raw eval evidence stays local.
- **C9.** Aaron agrees every stage gate. Claude never grades its own work: a fresh-context verifier
  reviews against the spec and plan.

## Out of scope

- A game-agnostic general patching layer for other overhauls or games. The "understand the mod
  first" step is built here for ARR patching only.
- New houseCARL tools (for example a mechanical dissect tool) and houseCARL code changes. Gaps are
  filed as houseCARL issues.
- A patch-review skill. It is a separate project, and this one may feed it.
- Weaker models (Haiku, older).
- Filing the eval's reference bugs to `arr-release/BUGS.md`, and fixing ARR's handmade patches.
- Domains the eval never exercised beyond short keep-versions (followers, playable races,
  vampirism, shouts, diseases, food, scrolls, and so on), unless the second recon pass shows they
  matter.

## Open questions

1. **House conventions to encode** (Aaron's call):
   - clothing / extra-slot AR 0;
   - the ESL flag on override-only patches;
   - Mastery perks on NPC casters;
   - the redundant NoArmorRatingRescale and ranged/temper keywords in baked patches;
   - follower registration through the Authoria bridge.
2. **Is the "write only after the mod is understood" rule enforced by a hook** shipped in the
   plugin (deterministic, per Anthropic's "skills are advisory; gates need hooks"), or is it advice
   only? A hook is in this repo, so it fits the scope.
3. **Which untested domains keep a short version, and how short.**
4. **Run the plan + boundary-fact arm** (no domain skills) before or alongside the rebuild? It tests
   how much the domain methods add beyond coverage and the boundary fact.
5. **Recon pass 2:**
   - Anthropic's current skill-authoring documentation: what changed since `standards/` was
     synced.
   - Aaron's armour and quest patching sessions, each with a decision log: which judgement calls
     recur, and what an agent lacks there.
6. **Skill structure.** The distillation proposes a router, a Requiem profile, an ARR layer and 7
   slim domain skills, with perk-assignment folded into npc, ammo into weapon, and script retired.
   Does that shape hold under Anthropic's guidance on skill size and triggering?
7. **Eval re-run.** Fix and freeze the deciding-point checklist (12 fixes listed in the drill)
   before the rebuild, so the target isn't moved after the fact. Does the re-run add Sonnet?

## Evidence (recon pass 1)

Summarised in `../../control-eval-2026-10/` (PR #48: README, synthesis, per-task analyses, per-run
table). The re-mining (drill rounds 1–3, the distillation and the corrected fact sheet) is local in
the eval harness. Its public summary will be added with the spec. PR #48's headline "the router
drove most gains" was later corrected: the wins are joint (router coverage + boundary fact + domain
method).
