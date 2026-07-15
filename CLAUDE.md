# authoria-requiem-skills

*Class: LIVING (the operating doc) — supersede, don't edit; keep it current, **updated in the same
commit** as the change it describes. Per `standards/HOUSECARL_DOC_HYGIENE.md`.*

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

1. **This file** — how we operate.
2. **The latest handoff** in `dev/session-handoffs/` — what the last session did and what to pick up.
   Start here for "where are we."
3. **Skim `dev/BACKLOG.md`** — standing small follow-ups (append when you notice one, prune when done).
4. **Before you analyse, edit, review, or create any skill: load [`standards/`](standards/) fully** —
   binding, exact, and they govern reviewing a submission as much as writing one (see
   [Authoring standards](#authoring-standards)).

**Deep corpus, on demand** (the "why" — read, don't edit): `dev/handoffs/phase-*.md` (per-domain
build handoffs, the deepest rationale + every derived rule; ARCHIVE), `dev/reqtificator-rules.md`
(the Reqtificator rulebook; LIVING reference), `dev/STATE.md` (the frozen build-era ledger; ARCHIVE).

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

standards/                         binding skill-authoring standards (synced from houseCARL — upstream;
                                   re-sync these copies if they change there)

dev/   (SHARED dev lane — tracked since 2026-07-15 so both maintainers + their agents see one state)
  session-handoffs/                rolling per-session handoffs — the "where are we" lane
  BACKLOG.md                       standing small follow-ups (the durable papercut ledger)
  plans/                           chartered work (one plan doc per undertaking)
  handoffs/phase-*.md              build-era phase handoffs (ARCHIVE — the deep "why" corpus)
  corpus-*/                        mined derivation corpora (consumables, perks — ARCHIVE)
  s4-graycowl-validation/          S4 empirical-close artifacts + FINDINGS ledger (ARCHIVE)
  STATE.md                         build-era cross-session ledger (ARCHIVE, frozen at ship)
  PUBLIC-RELEASE-PLAN.md           executed release plan (ARCHIVE)
  reqtificator-rules.md            the Reqtificator rulebook (LIVING reference)
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
  follow-ups → `dev/BACKLOG.md`. Chartered work → `dev/plans/`. Session state →
  `dev/session-handoffs/` (write one at end of session).
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
- **This repo is the source of truth; the live install is a copy.** After editing here, sync and
  restart Claude Code to load changes:

  ```powershell
  robocopy authoria-requiem "$env:USERPROFILE\.claude\skills\authoria-requiem" /MIR
  ```

  (`/MIR` mirrors exactly — the live copy is disposable by design.) While testing unreleased edits,
  temp-bump `plugin.json` to `<next>-dev` (e.g. `1.0.3-dev`), **uncommitted** — CI rejects a
  committed `-dev`; the real bump happens at release. Same convention as houseCARL. **This cuts both
  ways in a two-maintainer repo:** after `git pull` lands the *other* maintainer's plugin changes,
  your live install is stale until you re-run the sync + restart — check `plugin.json` against your
  installed copy's when picking up work.
- **Candor + no silent workarounds.** Hit a block that pressures a standard, the authority model, or a
  shipped behavior? Surface it with a recommendation; don't quietly work around it.

## Authoring standards

`standards/` holds the binding skill-authoring standards (authored by Aaron; the houseCARL standards
repo is upstream — re-sync these copies if they change there). **Load them fully before you analyse,
edit, or create a skill — including before reviewing a submission.** Don't paraphrase them from memory;
the rules are exact and a reviewer renders a binary conforms / doesn't-conform verdict.

- **`HOUSECARL_SKILL_AUTHORING.md`** — the load-bearing one: action-first description format (§3), body
  structure and section order (§4), the tool-vs-skill archetypes (§5), eval-set rules with the
  fresh-context **agent fan-out** as the primary validation (§6 — read agents' *reasoning*, not just
  verdicts; re-measure whenever a description changes), and the **§8 19-item reviewer checklist** every
  skill must pass.
- **`HOUSECARL_NAMING.md`** — kebab-case skill folders, `name:` == folder name, no version or brand in names.
- **`HOUSECARL_DOC_HYGIENE.md`** — LIVING vs ARCHIVE doc classes; supersede don't edit; update LIVING
  docs in the same commit.

**Repo-specific deviations** (note these when reviewing against the §8 checklist):

- **§8 item 19 (the `$Skills` array in `scripts/build-plugin.ps1`) is N/A here** — this plugin has no
  build script; Claude Code auto-discovers each `SKILL.md` by directory. (It *does* apply in houseCARL.)
- The original nine shipped descriptions were validated under the pre-2026-06-23 standard
  (manual-prediction fallback + independent peer-prediction; the then-prescribed empirical loop was
  Windows-blocked and has since been retired upstream); skills added since (`requiem-consumable-patching`
  onward) are validated by the current §6.5 fan-out, results archived in their `evals/`. Under the
  current standard, any description change triggers the §6.5 fan-out re-measure — which runs natively
  here.

## Adding or reviewing a skill submission

First confirm it belongs in *this* pack — a Requiem-patching skill. A general modding skill (e.g. OAR
animation-config authoring) belongs in houseCARL, not here. Then walk it through the **§8 reviewer
checklist** in `standards/HOUSECARL_SKILL_AUTHORING.md`, place it under
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
