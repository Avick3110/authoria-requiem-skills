# Session handoff — control eval wrap-up and skills rebuild intent

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (frozen at close). Project: `dev/plans/2026-10-30-requiem-skills-rebuild/`.*

## Where we are

- **The control eval is finished and re-mined.** The public summary (results, drill summary,
  per-skill distillation) is on `main` in `dev/control-eval-2026-10/`. PR #48 was merged on
  2026-10-30.
- **The rebuild project has started** under the AI-native SDLC method (Aaron's `new-project`
  skill: intent → recon → spec → plan → build, with Aaron agreeing each stage).
  - **Gate 1 (intent) was agreed on 2026-10-30.** The stage is now **Recon pass 2**.
  - Project docs are on branch `claude/skills-rebuild`, in the worktree
    `.claude/worktrees/skills-rebuild`: `INDEX.md`, `intent.md`, `decisions.md`, `recon.md`.
    They are committed locally, **not pushed**, with no PR.
- **Approver:** Aaron alone. Heisen is out of action.

## Read first (next session)

1. `dev/plans/2026-10-30-requiem-skills-rebuild/INDEX.md`
2. `intent.md` (agreed) and `decisions.md`
3. `recon.md`: the pass 1 reading order, and the proposed pass 2 targets
4. `dev/control-eval-2026-10/distillation/DISTILLATION.md` (on `main`).

## Next steps

1. **Aaron reviews `recon.md`'s pass 2 targets** and adds to them. Then run group A (Anthropic's
   skill-authoring guidance, hooks in plugins, standards drift) as an agent fan-out. Findings are
   dated notes under the project's `recon/`.
2. **Aaron's armour and quest patching sessions** (group B). Each keeps a decision log in the
   format given in `recon.md`.
3. When Aaron says recon is enough: **interview him on the hard parts**, then write `spec.md`
   (numbered requirements, design, constraint table, areas of concern, end-to-end check). Then
   `plan.md`, in plan mode.

## Don't redo / don't be misled

- **The first synthesis's "the router drove most gains" is wrong.** The wins are joint: router
  coverage + the Reqtificator boundary fact + domain comparable methods. The merged summary
  carries the correction.
- **Five skill facts are false, plus several smaller ones.** The corrected fact sheet is §6 of
  DISTILLATION.md.
- **Clothing AR 0 is not false to reverse.** ARR's own patch does it; it is a house convention to
  decide.
- **Reference-bug filing to `arr-release/BUGS.md` is separate, and still waits on Aaron's review.**
  Material: `drill/r2/U3-reference-bugs/` in the local eval harness.
- **Not part of this rebuild:** the general patching layer, a houseCARL dissect tool, and a review
  skill. All were discussed, and all are separate projects.
