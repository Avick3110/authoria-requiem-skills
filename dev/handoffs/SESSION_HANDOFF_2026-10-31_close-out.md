# Session handoff — 2026-10-31 close-out

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (frozen at close). Project: `dev/rebuild/`. Supersedes
`SESSION_HANDOFF_2026-10-31_heisen-answers.md` as the latest handoff. Read that one for the content of Heisen's
answers; this one only closes the session.*

## State at close

- **Stage:** recon, essentially done. The rebuild is on `main` (PRs #49, #50, #52). The repo has `main` only:
  no open PRs, no open issues, no worktrees.
- **Done after the last handoff:**
  - PR #52 is merged (Heisen's answers as dispositions, the doctrine, the design rule, the split fates).
  - The verbatim answers stay private in the local eval harness (`census/answers-heisen-2026-10-31.md`).
    PR #51, which held them, is closed and its branch deleted.
  - In the ARR release hub (a separate repo), Q44 is committed to `BUGS.md` as CONFIRMED. Two task edits left
    over from 2026-10-16 are committed too: controller-setup is re-opened for a user's turbo-scroll report, and the
    Infuse path is fixed.
- **houseCARL:** a release is being cut in another session. The asset walk, `links=` and the `provider=` view are
  unreleased on its `main`, and N3–N5 recon wants them.

## Next (pick up here)

1. **No input needed:**
   - **N2**, the coverage-diff probe: what houseCARL record reads already give.
   - **N3–N5**, mapping `.pex`, asset and runtime-layer discovery. Do it after the houseCARL release.
   - **A6**, the tool traps.
   - Merging the census `conventions.json` files, with a dominance field.
   - Re-verifying the rest of `recon/suspected-bugs.md`.
2. **Aaron's calls:** D5, C1, C2, the trigger-eval setup (mock houseCARL or load it), and Q29 (Ancient Falmer
   arrows).
3. **The spec session.** Open with the hook and coverage-script discussion (`rebuild/carried-forward.md` N1–N5).
   Apply the design rule and the reference stance in `rebuild/decisions.md` (2026-10-31).
4. **B1–B4:** Aaron's own patching sessions, with decision logs running.

## Lessons from today

- Asking authors: check every candidate rule against Requiem and its addons first. Ask only what no record can
  answer, and ask few questions, each with its context.
- A shell or classifier block on force-push and remote deletes: plan merges so neither is needed. A rebase-merged
  branch gets deleted, not reused.
