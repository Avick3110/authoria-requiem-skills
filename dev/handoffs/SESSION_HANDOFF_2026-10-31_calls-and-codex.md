# Session handoff — 2026-10-31 Aaron's calls, Codex and release targets

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (frozen at close). Project: `dev/rebuild/`. Supersedes `SESSION_HANDOFF_2026-10-31_close-out.md` as
the latest handoff.*

## State at close

- **Stage:** still recon. `main` holds everything below, from PRs #54, #55 and #56. No open PRs, no worktrees.
- **houseCARL:** its release has been cut. Aaron installs it and restarts Claude Code after this session, which
  unblocks N3–N5.

## Done this session

- **No live install while the rebuild runs** (#54). The 1.x skills were found missing from the user skills folder.
  That was intended: once installed, they load in every session and would skew the B1–B4 logs and the no-skill runs.
  CLAUDE.md step 0, the sync bullet and the PR template now say so. Don't re-sync.
- **Aaron's five calls** (#55, in `rebuild/decisions.md`):
  - **Grading:** the new skills are graded against the spec (census plus the authors' answers), not against 1.x.
    Each skill's dev loop always includes a fresh no-skill run.
  - **C1:** no frozen R10 checklist. Its checks get rewritten from the spec, and some scoring plumbing is reused.
  - **D5:** dropped. Running the new skills on the 6 handmade patches and diffing against the authors' versions
    checks the spec itself.
  - **C2:** no separate plan + boundary-fact arm. Build order: router first, graded against the spec; domain content
    only where it misses.
  - **Trigger evals:** load the real houseCARL plugin alongside ours. Run under WSL2.
  - **Q29:** Ancient Falmer / snow-elf arrows get about 100, as a house value.
- **Codex and an installable release** (#56):
  - Intent C2 / C4 are amended: the pack targets Claude Code **and** OpenAI Codex (GPT models), and follows both
    vendors' guidance.
  - The pack ships as a release users install by hand or through their agent.
  - **Review gate:** every written skill gets a review pass against the Anthropic and OpenAI docs before it lands.
  - The desk pass is in `rebuild/recon/hosts/A8-A9-codex-and-distribution.md`. It synthesises houseCARL's verified
    docs corpus (its `dev/references/skills-docs-2026-09-05/`, local-only) and its Codex packaging.
  - The likely shape: one repo with both manifests (`.claude-plugin/` and `.codex-plugin/`), so `marketplace add` works
    on either host, plus a release zip with a `skills/` root. Unconfirmed until the live pass.

## Next (pick up here)

1. **After the houseCARL install and restart:** N3–N5 (`.pex`, asset and runtime-layer discovery mapping).
2. **No input needed:**
   - N2, the coverage-diff probe.
   - A6, the tool traps.
   - Merge the census `conventions.json` files, with a dominance field.
   - Re-verify the rest of `recon/suspected-bugs.md`.
3. **A8 / A9 live pass:**
   - Re-check the 2026-09-05 claims against the live docs. The corpus contradicts itself on the user skills path and
     on whether `.claude-plugin/marketplace.json` is supported.
   - Install from scratch on each host.
   - Run a Codex / GPT smoke test (a few trigger queries plus one patching task). houseCARL never ran a skill on GPT.
4. **Spec session:** open with the hook and coverage-script topics (`carried-forward.md` N1–N5). Spec questions from
   today:
   - Whether graded runs also use a GPT model.
   - Mocks for full graded runs.
5. **B1–B4:** Aaron's own patching sessions, with decision logs running.

## Lessons

- A missing install isn't drift by default. Ask before "fixing" it.
- Explain a call from the ground up, without IDs: Aaron needs the why and the options in plain terms.
- The auto-mode classifier blocks a merge without an explicit go ("file it" isn't one). Ask for the merge in words.
- `gh pr create --body` breaks on PowerShell quoting. Use `--body-file`.
