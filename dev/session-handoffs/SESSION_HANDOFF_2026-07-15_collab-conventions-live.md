# Session handoff — 2026-07-15 (collab conventions): CI + PR/issue conventions live, HCBR store retired to Issues

*Author: Aaron (+ Claude session) · 2026-07-15*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-15_F1-F8-fixes-1.2.2.md` as the latest — same session, second act. The
F1–F8 batch shipped first (PR #10 merged, v1.2.2, live install synced); this doc adds the
collaboration-infrastructure work that followed.*

## What this session added (this PR)

Aaron asked for the repo setup that lets him + Heisen work synchronously — the PR convention layer
houseCARL has and this repo lacked. Everything below rides this PR; the ruleset changes happen
right after it merges (they'd block the PR itself otherwise).

1. **CI (`.github/workflows/ci.yml`, job `conventions`)** — manifests parse + version is X.Y.Z
   (never a committed `-dev`), every SKILL.md ≤500 lines / `name:`==folder / description ≤1536
   chars, and (PRs only) a **publish-hygiene scrub of added lines** (no real machine paths, no
   emails). Added-lines-only by design: frozen ARCHIVE docs carry historical paths and are not
   re-litigated.
2. **PR template** — the landing checklist as convention: lane reference, version+CHANGELOG,
   the §6.5 re-measure tripwire on description changes, publish hygiene, rebase-before-merge
   (the plugin.json/CHANGELOG collision point), post-merge live-install sync.
3. **Issue forms** — *Skill bug report* (labels `bug` + `from-live-session`) and *Coverage gap /
   no owner* (label `gap`); a contact link routes houseCARL tool bugs to the houseCARL repo's
   Issues. Labels created.
4. **HCBR store RETIRED → GitHub Issues.** The seven live local reports were migrated verbatim as
   houseCARL issues **#195–#201** (3× 2026-07-15 gaps + the PerkPlacement note + the three older
   partially-built trackers); the local files moved to the store's archive with a retirement
   README. New reports: skill bugs/gaps → this repo's Issues; houseCARL tool bugs/gaps → houseCARL
   Issues. BACKLOG's VMAD entry now links #195–#198.
5. **CLAUDE.md updated in the same commit** — landing rule (other maintainer's review; admin
   PR-bypass as the visible solo-urgent hatch; `conventions` check required), the new lanes, and
   the parallel-landing collision rule.

## Ruleset changes (apply AFTER this PR merges — next session picks up here if not done)

On the `protect main` ruleset (currently: PR required, rebase-only, no bypass actors):

- `required_approving_review_count`: 0 → **1** (self-approval impossible ⇒ the other maintainer
  reviews — the synchronous-work handshake).
- Add required status check **`conventions`**.
- Add bypass actor: repository **admin**, mode **pull_request** (Aaron can still land solo-urgent
  via PR, visibly flagged; raw pushes stay impossible for everyone).

## Also noted (candor)

- The S4 "scrub verified zero-hit" claim was narrower than stated: frozen ARCHIVE docs (build-era
  phase handoffs, corpus notes, S4 lane JSONs) still carry Heisen's own machine paths and
  instance-drive paths. They're ARCHIVE (immutable) and the identity is already public; the new
  CI gate stops *new* leaks. Flag to Aaron/Heisen in case either wants a history rewrite instead
  (heavier call, not taken unilaterally).
- Heisen's live install was pre-1.1.0 at last word; current is **1.2.2** — he should `git pull` +
  re-sync.

## Standing

- Follow-up test candidate: regenerate the Gray Cowl patch with the 1.2.2 pack (validates F1–F8).
- Backlog: perk-assignment Q8 description tweak (§6.5 re-measure if taken); root README
  authority-model rewrite (needs Aaron); original-nine empirical re-validation.
