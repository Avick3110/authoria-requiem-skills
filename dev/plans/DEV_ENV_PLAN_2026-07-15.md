# Plan — a proper local dev environment for authoria-requiem-skills, on the houseCARL model

*Class: ARCHIVE — **executed in full 2026-07-15** (same day). All 5 steps done; PR #3 merged on
Aaron's go; the robocopy loop walked end-to-end (dry-run `/L` then live: 92 files re-stamped, 0
extras deleted, live install at 1.0.2). Kept as the record of the design and its rationale.*

**Status 2026-07-15 (Aaron's calls, same day):** GO on the shape, with an added **docs audit** — Aaron
judged CLAUDE.md substandard vs houseCARL's ("contains stuff it shouldn't") and wants all project docs
checked before the rewrite. §2.3 decided: **option (a) robocopy sync**, live install =
`~/.claude/skills/authoria-requiem/`. Execution: steps 1–3 done (dev/ reshaped, 07-14 handoff
backfilled, BACKLOG seeded); audit ran as a 4-agent Opus fan-out (CLAUDE.md · public docs ·
standards drift · skills scrub); step 4 (CLAUDE.md rewrite + audit fixes, via PR) folds the findings in.

**Audit findings feeding step 4** (see the session's PR for the full set): standards drift —
`HOUSECARL_SKILL_AUTHORING.md` copy predates upstream's 2026-06-23 §6 rework (run_loop → fan-out
validation); re-sync from houseCARL. Public docs — README.md:60 says v1.0.0 (shipped is 1.0.2);
CHANGELOG cites gitignored `STATE.md` + bare internal `HCBR-` IDs. CLAUDE.md audit — stale
"current: 1.0.1", tactical status in the LIVING doc, triple-homed skills table, restated authority
doctrine, operating layer missing. Skills scrub — clean except three "Aaron"-by-name mentions (→
BACKLOG, LOW).

**Step 4 EXECUTED as [PR #3](https://github.com/Avick3110/authoria-requiem-skills/pull/3)** (3 commits:
standards re-sync · CLAUDE.md operating-manual rewrite · README/CHANGELOG public fixes) — **awaiting
Aaron's go.** Live install verified at 1.0.2 (backlog item closed). Remaining after merge: step 5, one
end-to-end walk of the robocopy test loop.

## 0. Goal

Give this repo the same **session-operating environment** houseCARL has, so any Claude session can boot
here, know where it is, work safely, and hand off — without inventing process per session. The repo was
built in one intensive phase-arc (June 2026) and shipped; since then it gets episodic maintenance
sessions (e.g. the HCBR-2026-07-14-03 fix) that have no standing lanes to land their state in.

**Matching, not cloning.** houseCARL is a C# product with a build; this is a docs-only skill pack with
no build artifact. The plan ports the *operating design* (boot protocol, dev/ lanes, worktree
discipline, handoff convention, backlog) and deliberately skips the parts that only make sense for a
compiled product (release/ zips, generator, probes, CI beyond validate).

## 1. Gap analysis — what already matches, what's missing

Verified against the tree and GitHub on 2026-07-15.

| houseCARL element | Status here | Note |
|---|---|---|
| CLAUDE.md operating manual | **Partial** | Exists and is good on *what the repo is* + authoring standards; has almost nothing on *how a session operates* (no boot order, no worktree rule, no handoff convention) |
| `standards/` (binding, synced) | **Present** | Synced copies of the three houseCARL standards |
| `dev/` gitignored private corpus | **Present** | But shaped as a frozen build ledger, not a rolling workspace |
| `dev/session-handoffs/` rolling lane | **Missing** | `dev/handoffs/phase-*.md` are the *build-era* arc — closed, effectively ARCHIVE. Nothing receives new session state; the 07-14 fix session had nowhere to hand off |
| `dev/plans/` | **Missing** | (this doc creates it) |
| `dev/BACKLOG.md` | **Missing** | Same lost-follow-up failure mode houseCARL just fixed on 07-15 |
| Worktree & merge discipline | **De facto, not codified** | `main` is PR-protected (active "protect main" ruleset); both post-ship fixes landed via `claude/<name>` → PR. But CLAUDE.md never says so — a fresh session could edit on `main` locally and only find out at push |
| Boot read-order (§2 analog) | **Missing** | CLAUDE.md says "load standards fully" but not *where am I / what happened last* |
| Bug lane | **Present, implicit** | HCBR reports in the external store (`E:\Skyrim Modding\ARR Workspace\houseCARL Bug Reports`) already carry authoria items (HCBR-2026-06-11-03, -02 wave (c), 2026-07-14-03). Not named in CLAUDE.md |
| Local dev-install / test loop | **Undefined** | CLAUDE.md says "re-sync the live install and restart Claude Code" but names no mechanic. houseCARL's analog is the `-dev` package convention |
| Release conventions | **Partial** | plugin.json bump + CHANGELOG + `validate --strict` are documented; no dev-version convention for testing unreleased skill edits |

**Not ported (deliberately):** build scripts / `release/` zips (no build artifact — the plugin dir IS
the artifact), generator/probe CI, PRFAQ corpus (this repo's "why" corpus already exists:
`dev/handoffs/phase-*.md` + `dev/reqtificator-rules.md` + the shipped references), memory-index changes
(houseCARL's memory already tracks this repo at the right altitude).

## 2. The design

### 2.1 dev/ — reshape from build-ledger to rolling workspace

```
dev/
  STATE.md                  → reclassify ARCHIVE (build-era ledger; superseded by session-handoffs)
  PUBLIC-RELEASE-PLAN.md    → already effectively ARCHIVE (release shipped 2026-06-09)
  handoffs/phase-*.md       → keep as-is, reclassify ARCHIVE — this is the deep "why" corpus,
                              the PRFAQ-analog: read on demand, never edited
  reqtificator-rules.md     → stays LIVING reference (the rulebook is consulted, not historical)
  session-handoffs/         → NEW rolling lane, one file per session, houseCARL naming:
                              SESSION_HANDOFF_<date>_<slug>.md
  plans/                    → NEW (this doc is its first occupant)
  BACKLOG.md                → NEW, seeded from known floating items (§3 step 4)
```

Reclassification = a one-line class header on each ARCHIVE doc per `HOUSECARL_DOC_HYGIENE.md`, not
moving files (links in CLAUDE.md and handoffs keep resolving).

### 2.2 CLAUDE.md — add a "how a session operates" layer

A new section (or sections) mirroring houseCARL §2/§5/§8, scaled down. Content:

1. **Boot order:** (1) CLAUDE.md; (2) latest `dev/session-handoffs/` handoff — where are we; (3) skim
   `dev/BACKLOG.md`; (4) deep corpus on demand: `dev/handoffs/phase-*.md` (domain why),
   `dev/reqtificator-rules.md` (rulebook), shipped skill references.
2. **Worktree & merge discipline** — codify what's already enforced remotely: every change starts in a
   worktree (`.claude/worktrees/<name>/`, branch `claude/<name>`), main folder read-only, land via
   push → PR → Aaron's explicit go → merge (the ruleset already rejects raw pushes). Same self-editing
   gate: CLAUDE.md changes are surfaced, never self-committed.
3. **Lanes:** bug reports = the external HCBR store (named explicitly, with the path); small
   dev-noticed follow-ups = `dev/BACKLOG.md`; chartered work = `dev/plans/`; session state = handoffs.
4. **End-of-session:** write a handoff to `dev/session-handoffs/`.
5. **Local test loop** (§2.3's outcome, one paragraph).

This is the only step that touches tracked files → worktree + PR + Aaron's go.

### 2.3 Local dev-install / test loop — the one real fork

houseCARL's convention: local test builds are `<next>-dev` via a temp, uncommitted plugin.json bump.
The analog here is simpler because there's no build — the question is just *how the live install gets
the working copy*. Options:

- **(a) Plugin-dir sync (recommended).** One documented robocopy line:
  `robocopy authoria-requiem "<live install path>" /MIR` — repo → live install, then restart Claude
  Code. Matches "repo is the source of truth", zero tooling, matches the don't-over-engineer rule.
  Adopt the houseCARL `-dev` convention as-is: while testing unreleased edits, temp-bump plugin.json to
  `<next>-dev`, uncommitted; the version reverts/finalizes at release.
- **(b) Marketplace local-path install.** Point `claude plugin install` at the local repo via
  marketplace.json. More "proper" plugin-shaped, but adds moving parts for zero gain on a docs-only
  pack, and diverges from how Aaron actually uses it today (drag-drop copy).
- **(c) Symlink the live install to the repo.** Zero-sync, but edits go live mid-session (a
  half-edited SKILL.md can load), and symlinks + Windows + Claude Code discovery is an untested
  surface. Not worth the risk for the sync it saves.

**Recommendation: (a).** One open fact to confirm with Aaron first: *where the live install actually
lives on this machine* (CLAUDE.md says `~/.claude/skills/authoria-requiem/`; if he's since moved to a
plugin install, the target path differs). Ask, don't guess.

### 2.4 Explicitly out of scope

- No CI/GitHub Actions (validate --strict stays a release-time manual gate; add CI only if a real
  slip demonstrates the need — guardrails earn their place).
- No changes to skills content, standards, or the release process itself.
- No memory-index additions (existing `project_authoria_requiem_skills` entry stays the pointer).

## 3. Execution steps (single session, sequenced)

1. **dev/ reshape** — create `session-handoffs/`, `plans/` (done by this doc), `BACKLOG.md`; add
   ARCHIVE headers to STATE.md, PUBLIC-RELEASE-PLAN.md, handoffs/phase-*.md. All gitignored → direct
   writes, no PR.
2. **Backfill one handoff** — write `SESSION_HANDOFF_2026-07-14_npc-bulk-pass-fix-1.0.2.md`
   reconstructing yesterday's HCBR-2026-07-14-03 session from the merged PR #2 + commit 01ab413, so
   the rolling lane starts with real state, not empty.
3. **Seed BACKLOG.md** — known floating items: the §6 empirical trigger re-validation flag (descriptions
   shipped on the §6.5 manual fallback, flagged for empirical re-validation when a non-Windows host is
   available), plus anything Aaron names.
4. **CLAUDE.md operating layer** — worktree → PR → Aaron-go (the one gated step). Includes the local
   test loop paragraph per the §2.3 decision.
5. **Verify the loop end-to-end** — make a trivial change on a worktree, sync to the live install per
   §2.3, confirm the skill loads, revert. Empirical-first: the environment isn't "done" until a walk
   through it worked.

## 4. Decisions Aaron owns before execution

1. **Go/no-go on the shape overall** (§2).
2. **Local test loop:** option (a)/(b)/(c) — recommendation is (a) — and *where the live install
   lives* on this machine.
3. **Backfill depth:** just yesterday's session (step 2) or none — the phase-era needs no backfill,
   it's already the archive corpus.
