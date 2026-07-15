<!-- What & why, briefly. Map each change to its driver (issue #N, BACKLOG entry, plan doc). -->

## Lane

- [ ] This PR names its driver: fixes issue #N / a `dev/BACKLOG.md` entry / a `dev/plans/` charter
- [ ] Session handoff rides along in `dev/session-handoffs/` (work-session PRs) — authorship-stamped

## Release checklist (skill/plugin changes — N/A for docs-only PRs)

- [ ] `plugin.json` version bumped + `CHANGELOG.md` entry mapping each change to its driver
- [ ] **No skill `description:` changed** — or the §6.5 fan-out was re-measured against the final
      text and the results are archived in that skill's `evals/`
- [ ] `claude plugin validate --strict` passed locally (CI re-checks the mechanical parts only)
- [ ] LIVING docs (CLAUDE.md, READMEs, rosters) updated **in this same PR** where the change
      touches what they describe

## Publish hygiene (`dev/` is public)

- [ ] No local machine paths (use `<home>` / placeholders), no emails, no credentials in added lines
      (CI gates this on added lines too)

## Before merging

- [ ] Rebased on latest `main`; version bump and CHANGELOG position re-checked against what landed
      since this branch was cut (two maintainers land in parallel — collisions live here)

**After merge:** sync the live install and restart Claude Code —
`robocopy authoria-requiem "$env:USERPROFILE\.claude\skills\authoria-requiem" /MIR`
