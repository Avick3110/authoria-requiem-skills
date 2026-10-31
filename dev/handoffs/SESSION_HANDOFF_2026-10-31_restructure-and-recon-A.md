# Session handoff — dev/ restructure, recon A1–A5, issues closed

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (frozen at close). Project: `dev/rebuild/`. Supersedes
`SESSION_HANDOFF_2026-10-31_split-hunts-7-13.md` as the latest handoff.*

## Where we are

- **Stage:** recon, close to done. Waiting on the list authors' answers (questions sent 2026-10-31).
- **This PR lands the whole rebuild so far on `main`**: intent, both recon passes, the census, the 14 split
  docs, the questions, the suspected bugs, and recon A1–A5. Until now all of it sat on
  `claude/skills-rebuild`, invisible from the main checkout.

## Done this session

- **Recon A1–A5** (Anthropic's skill-authoring guidance): `rebuild/recon/anthropic/A1-A5-anthropic-skill-guidance.md`.
  - Anthropic's guidance backs the distillation: lean SKILL.md, gotchas over rules, fewer and wider skills,
    plan and boundary facts at the top.
  - Listing mechanics are unchanged since #43.
  - A hook gate is mechanically possible, but an agent-written artefact is no gate.
  - `standards/` has no upstream any more, and 13 of its rules conflict with Anthropic's guidance.
  - The control eval is the right quality gate; triggering should be measured by real invocation.
- **`dev/` restructure** (Aaron agreed every decision):
  - The rebuild moved to `rebuild/` and the eval into it as `rebuild/eval-2026-10/`.
  - Recon is sorted into `anthropic/`, `arr-domains/`, `census/` and `splits/01–14`.
  - The handoff lane is now `handoffs/`; the rulebook is in `reference/`.
  - The 1.x material is frozen in `archive/`, which has its own README.
  - `dev/README.md` is the front door.
  - Links and repo paths were rewritten by script (94 links, 139 mentions, 0 unresolved). The 1.x machine
    paths were scrubbed (52).
  - CLAUDE.md, the PR template and CHANGELOG now point at the new paths. CLAUDE.md also records that
    `standards/` is owned here now.
- **BACKLOG frozen** as `archive/BACKLOG-1.x.md`. Its 7 open items and the 7 open issues are in
  `rebuild/carried-forward.md` as spec inputs.
- **Issues #27, #29–#33 and #46** are closed as not planned (superseded by the rebuild), each pointing at its
  carried-forward row. This happens once the PR has merged.

## Next

1. The authors' answers: record them inline in `rebuild/recon/questions-for-list-authors.md`, then update the
   affected split rules.
2. Aaron decides the remaining recon items: D5, A6 (root-causing the tool traps), and C1 / C2.
3. Then the spec. A4 says the spec has to settle which authoring standard binds.

## Gotchas

- The rewriter's first two passes were wrong:
  - A folder whose files were re-sorted into subfolders didn't map until it was given an explicit mapping.
  - A lone `/` between two code spans was read as a path.
  - `dev/…` mentions of *houseCARL's* paths were rewritten as ours (2 fixed by hand).
  - Check any future bulk relink for all three.
- PowerShell `Set-Location` inside a command moves the session's working directory. Use `git -C`.
