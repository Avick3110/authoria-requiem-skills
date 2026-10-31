# authoria-requiem-skills

*Class: LIVING (the operating doc) — supersede, don't edit; keep it current, **updated in the same
commit** as the change it describes. Doc classes: see [Doc classes](#doc-classes).*

Source repo for the **`authoria-requiem`** Claude Code skill plugin — eleven skills that patch
newly-added Skyrim SE mods to play correctly with **Requiem** by *live-analogy*: derive every stat,
keyword, recipe, perk, and placement by comparing new content against Requiem's own records (read live
through **houseCARL**), then emit a direct ESP override the **Reqtificator** integrates at build time.
Carry the *inputs* the Reqtificator expects; never hand-stamp its outputs or hardcode numbers derivable
from a live comparable.

This file is **how a session operates here**. The capability story, install instructions, and the
skill roster live in `README.md` and `authoria-requiem/skills/README.md` — deliberately not restated
here (one home per fact). The current version lives in exactly one place:
`authoria-requiem/.claude-plugin/plugin.json`.

> **Requires [houseCARL](https://github.com/Avick3110/houseCARL)** — the MCP that gives Claude
> data-layer access to the load order (reads return the live conflict winner; writes go to a new ESP).
> FormIDs are `XXXXXX:Plugin.esp`. Nothing here runs without it pointed at a Requiem MO2 instance.

## Read in this order

0. **`git pull`. Don't install or sync the pack while the rebuild runs** (Aaron, 2026-10-31). The
   1.x skills load in every session once installed, which skews the B1–B4 patching logs and any
   no-skills eval run. Testing a skill means loading it on purpose, in that session or harness. A
   missing `~/.claude/skills/authoria-requiem` is intended, not drift. When installs come back,
   compare bytes, not just `plugin.json` versions: identical versions can hide different content
   (1.4.0), and a stale install invalidates every live conclusion drawn from it (the 1.3.0
   "consumables completely skipped" false alarm).
1. **This file** — how we operate.
2. **The latest handoff** in `dev/handoffs/` — what the last session did and what to pick up.
   Start here for "where are we." [`dev/README.md`](dev/README.md) maps everything else in `dev/`.
3. **The 2.0 rebuild is the live work** ([`dev/rebuild/INDEX.md`](dev/rebuild/INDEX.md)). Its
   `carried-forward.md` holds the small follow-ups: the 1.x backlog is frozen in `dev/archive/`.
4. **Before you analyse, edit, review, or create any skill:** read
   [Authoring guidance](#authoring-guidance). The old `standards/` folder is gone (deleted
   2026-10-31). Don't recreate its rules from memory or from the archive.

**Deep corpus, on demand** (the "why" — read, don't edit): `dev/archive/1.x-build/phase-handoffs/`
(per-domain build handoffs, the deepest 1.x rationale; ARCHIVE), `dev/reference/reqtificator-rules.md`
(the Reqtificator rulebook; LIVING reference), `dev/archive/1.x-build/STATE.md` (the frozen build-era
ledger; ARCHIVE).

## Repo map

```
README.md                          public overview + install (capability + roster live here)
CHANGELOG.md                       version history (the version itself lives in plugin.json)
LICENSE                            MIT © DrHeisen
.claude-plugin/marketplace.json    marketplace manifest (optional plugin-install path)
.github/                           the collaboration layer: CI (workflows/ci.yml — the required
                                   `conventions` check), the PR template (the landing checklist),
                                   and the issue forms (skill bug / coverage gap)
authoria-requiem/                  THE PLUGIN — the shipped artifact
  .claude-plugin/plugin.json       manifest; the `version` lives here (bump on release)
  skills/README.md                 skills index
  skills/<skill>/                  SKILL.md + references/ (+ index.jsonl) + evals/eval_set.json

dev/   (SHARED dev lane — tracked since 2026-07-15 so both maintainers + their agents see one state)
  README.md                        the front door: what's live and where everything is
  handoffs/                        rolling per-session handoffs, all eras — the "where are we" lane
  rebuild/                         the 2.0 rebuild project (INDEX, intent, recon, decisions, spec, plan)
    eval-2026-10/                  the control eval (recon pass 1)
    recon/                         anthropic/, arr-domains/, census/, splits/, questions, suspected bugs
    carried-forward.md             spec inputs from 1.x (frozen backlog items + closed issues)
  reference/reqtificator-rules.md  the Reqtificator rulebook (LIVING reference)
  archive/                         1.x material, frozen (ARCHIVE): build-era phase handoffs, STATE,
                                   corpora, S4 validation, release plan, 1.x plans, BACKLOG-1.x.md
```

## Working discipline

- **Worktree & merge discipline — start every change that will commit in a worktree.** Branch
  `claude/<name>` under `.claude/worktrees/<name>/`; the main repo folder stays on `main`, read-only
  except for landing. `main` is PR-protected by ruleset (raw pushes rejected; rebase-merge only);
  landing = push → PR (the template's checklist is the convention) → green **`conventions` CI
  check** (manifests, 500-line cap, `name:`==folder, description cap, and a publish-hygiene scrub
  of added lines) → merge → delete branch. **Review is by request, not by ruleset** (relaxed
  2026-07-15 so either maintainer can land without the other): each maintainer merges their own
  PRs freely, and *requests the other's review* when a change warrants eyes — doctrine/authority
  changes, new skills, anything you'd want to know shipped if you were the other person. Agents:
  a maintainer's explicit go is still the gate before you merge anything. Changes to this file get
  the same flow — surface them, never self-commit. Check your branch at session start.
- **Parallel landings collide in two files.** Two maintainers + agents cut worktrees from different
  points of `main`, and both `plugin.json` (the version bump) and `CHANGELOG.md` (the entry position)
  assume they know what shipped last. Rebase on latest `main` immediately before merge and re-check
  both — the PR template gates on it.
- **Lanes.** Skill bugs and coverage gaps — live-session or dev-noticed, either maintainer → **GitHub
  Issues on this repo** (forms provided: *Skill bug report*, *Coverage gap / no owner*); fixes
  reference `#N` in the CHANGELOG. houseCARL tool bugs/gaps → Issues on the houseCARL repo. (The old
  local HCBR store is retired — its live reports were migrated as houseCARL issues #195–#201 on
  2026-07-15; pre-existing `HCBR-<date>-<n>` ids in the CHANGELOG are historical.) Small dev-noticed
  follow-ups → `dev/rebuild/carried-forward.md` while the rebuild runs. Chartered work → its own
  project folder under `dev/` (like `dev/rebuild/`). Session state → `dev/handoffs/` (write one at
  end of session). Land `dev/` docs on `main` at every stage gate, so a long branch never hides them.
- **External reports (an .md report handed over via Discord or similar) don't get pasted straight
  into an issue.** Triage first (valid? reproducible? duplicate of an open issue?), **scrub** —
  a third party's report carries *their* machine paths, usernames, and setup details they never
  agreed to publish — then file it via the matching form/title convention, largely verbatim, with a
  provenance line (name the reporter only with their OK), and hand back the issue `#N` for the
  reply.
- **`dev/` is the SHARED dev lane — tracked and pushed (since 2026-07-15; Heisen is a collaborator).**
  Two maintainers + their agents work from one state, so:
  - **Authorship stamps are mandatory.** Every new handoff, plan, and findings doc carries, directly
    under its title: `*Author: <Aaron|Heisen> (+ Claude session) · YYYY-MM-DD*`. Every new BACKLOG
    entry ends its trailing parenthetical with the author, e.g. `(2026-07-15, S4 session, Aaron)`.
    Agents: stamp the human you operate for, never just the model. Docs predating 2026-07-15 are
    Aaron-lineage unless stamped otherwise.
  - **Read the latest handoff by DATE across BOTH authors** — "where are we" is now a merged lane;
    check for a sibling handoff from the other maintainer before assuming yours is latest.
  - `dev/` docs ride PRs like everything else: work-session docs ride the work's PR; doc-only
    updates go in small `docs:` PRs. Write durable docs in your WORKTREE now (they're tracked;
    the old main-checkout rule applied only while `dev/` was gitignored).
  - **Publish hygiene:** `dev/` is in a public repo — no local machine paths (use `<home>` or
    relative), no emails, no credentials. Scrub before commit.
- **This repo is the source of truth; the live install is a copy.** *Paused while the rebuild runs:
  there is no live install (step 0). The mechanics below apply once installs come back.* After
  editing here, sync and restart Claude Code to load changes:

  ```powershell
  robocopy authoria-requiem "$env:USERPROFILE\.claude\skills\authoria-requiem" /MIR
  ```

  (`/MIR` mirrors exactly — the live copy is disposable by design.) While testing unreleased edits,
  temp-bump `plugin.json` to `<next>-dev` (e.g. `1.0.3-dev`), **uncommitted** — CI rejects a
  committed `-dev`; the real bump happens at release. Same convention as houseCARL. **This cuts both
  ways in a two-maintainer repo:** after `git pull` lands the *other* maintainer's plugin changes,
  your live install is stale until you re-run the sync + restart. Dry-run the compare when picking
  up work (`robocopy … /MIR /L`); a version-equal but byte-different install is still stale.
- **A report is a lead, not a fact — re-derive every claim from the live record before acting on it.**
  This applies to field reports, issues, handoffs, and *our own* audit findings. Every one checked so
  far has been **partly wrong, in both directions, across both maintainers' lanes**: 1.3.0 corrected
  its own report's ENCH premise rather than encoding it (it was vanilla-autocalc thinking) and traced
  "consumables completely skipped" to a **stale live install**, not a skill gap; the 1.4.0 empirical
  close withdrew its own filed issue ([#19](https://github.com/Avick3110/authoria-requiem-skills/issues/19)
  — the blanket DNAM rule turned out deliberate), caught a wrong disambiguator in
  [#21](https://github.com/Avick3110/authoria-requiem-skills/issues/21) *before* it shipped (`_NPC`
  spells follow the **player** ladder, so "no tome teaches it" would have misrouted every one), and
  found [#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15)'s cause understated
  (extending the shared bridge needs a **recompile**, not one more alias). The inverse holds just as
  hard: **a patch that *reads* wrong and a patch that *is* wrong are nearly disjoint sets** — ten
  alarming findings against a shipped patch dissolved once `Configuration.TemplateFlags` was read and
  the records proved correctly Stats-templated. So: **verify before you file *and* before you fix**,
  expect the real fix to differ from the ask, and treat "the report says X" as the start of the
  derivation, never the end. This is the same live-analogy discipline the pack applies to records,
  turned on our own prose. Worked cases:
  `dev/handoffs/SESSION_HANDOFF_2026-07-17_empirical-close-valserano-1.4.0.md`.
- **Candor + no silent workarounds.** Hit a block that pressures a standard, the authority model, or a
  shipped behavior? Surface it with a recommendation; don't quietly work around it.

## Authoring guidance

The old `standards/` folder (the HOUSECARL_* authoring, naming and doc-hygiene standards) was
**deleted on 2026-10-31**. It was outdated, and it conflicted with Anthropic's current guidance in 13
places (recon A4). Archive docs still cite its sections (§3, §6.5, §8 …); treat those as history, not
rules.

Until the rebuild spec sets this repo's own rules, author to:
- **Anthropic's published skill-authoring guidance.** The summary with sources is in
  `dev/rebuild/recon/anthropic/A1-A5-anthropic-skill-guidance.md`; re-check the source when it matters.
- **The mechanical checks CI enforces** (`.github/workflows/ci.yml`): the 500-line SKILL.md cap,
  `name:` == folder name (kebab-case), the description cap, and the manifests.
- **Measurement** (Aaron, 2026-10-31):
  - Triggering is measured by real invocation: does Claude actually load the skill on the query
    (recon A5)?
  - Value is measured by recording transcripts of real use and grading the output, and how the
    skill performed, in detail.

## Doc classes

**LIVING** docs (this file, READMEs, INDEX, the rebuild's open working docs) are kept current and
updated in the same commit as the change they describe. **ARCHIVE** docs (handoffs, findings,
executed plans, `dev/archive/`) are frozen: supersede them with a new doc, don't edit them. Moves
may rewrite their links and paths only.

## Adding or reviewing a skill submission

First confirm it belongs in *this* pack — a Requiem-patching skill. A general modding skill (e.g. OAR
animation-config authoring) belongs in houseCARL, not here. Then check it against
[Authoring guidance](#authoring-guidance), place it under
`authoria-requiem/skills/<kebab-name>/`, list it in `skills/README.md` + the README table, and bump
`plugin.json` `version` + add a `CHANGELOG.md` entry. **Validate before release:**
`claude plugin validate --strict` (plugin + marketplace must pass).

Load **`requiem-patching`** first for a whole-mod job — it's the router / integration brain; the domain
skills also trigger directly (roster + coverage table: `README.md`).

## Authority model

The cross-cutting **authority** and **masters / `REQ_NULL`** doctrine is *binding* in
`authoria-requiem/skills/requiem-patching/references/` (`scope-and-authority.md`,
`masters-and-null-stripping.md`) — consult those files; this doc deliberately doesn't restate them.

Platform: Windows / PowerShell. Commit only when asked.
