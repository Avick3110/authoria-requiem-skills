# Public Release Plan â€” Authoria Requiem Patching Skills (P2 + P3)

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) â€” executed; the release shipped 2026-06-09 (GitHub public). Kept as the record of how; not a live plan.*

*Private dev doc (lives in `dev/`, gitignored â€” never pushed). Written 2026-06-09, after the
container-repo cleanup. This is the "where we're going" companion to `STATE.md` ("how it was built").*

---

## Status

- **Container repo cleaned + re-rooted** to a single commit (`1622c89`) = the feature-complete
  **v1.0.0** (9 skills). Old phase-1 scaffold history is recoverable via the **`pre-cleanup-scaffold`**
  tag. Working tree clean; plugin + marketplace both pass `claude plugin validate --strict`.
- This repo has **no git remote yet** â€” nothing has ever been pushed. That's load-bearing for the
  privacy step below (we can still rewrite history freely).

## Locked decisions (2026-06-09)

| Decision | Choice | Notes |
|---|---|---|
| Repo identity | **This repo becomes the public repo** | The fresh-single-commit cleanup removed the only reason ("messy scaffold history") the old note wanted a *separate* repo. One source of truth. |
| Dev docs | **Private â€” gitignored `dev/`, kept local, never pushed** | Mirrors houseCARL (its `dev/` has 0 tracked files / was never committed). |
| License | **MIT** | Pure markdown â†’ not GPL-forced like houseCARL. Aaron's proposal; **confirm with Heisen** before publishing. |
| Positioning | **Authoria-flavored, honestly documented** | Keep the Authoria-specific bits; do NOT generalize. Works for any Requiem instance (user points houseCARL at their own), but the bundled reference ladders/keywords were mined on the Authoria list â€” say so. |

## OPEN â€” needs Heisen (blocks the public push, not the local prep)

- **Authorship / credit.** `plugin.json` + `marketplace.json` currently credit "Authoria Requiem
  Reforged team", not Heisen by name. Decide how Heisen is credited (author line, README credit).
- **MIT copyright holder.** The `LICENSE` copyright line needs a name â€” Heisen / Aaron / the team.
  This is the same conversation as the credit line.
- **MIT sign-off.** Heisen authored the skills; he should agree to MIT before they go public.

---

## Progress (2026-06-09, session 2)

> âœ… **P2 COMPLETE â€” SHIPPED PUBLIC 2026-06-09** â†’ github.com/Avick3110/authoria-requiem-skills (MIT Â© DrHeisen, branded "houseCARL - Authoria Requiem Skills"; 7 commits, `dev/` never in public history, no tags pushed, fresh-clone validates `--strict`; live install re-synced byte-identical). The "Remaining = Heisen-gated" bullet below is RESOLVED. **Only P3 remains** (Nexus zip + optional `install.bat` + the Nexus page).

- âœ… **Step 1 done** â€” dev docs split into gitignored `dev/`; re-rooted to a fresh commit (`790038a`)
  that excludes them (recoverable via `pre-public` tag). History is dev-doc-free from here.
- âœ… **Step 5 done** â€” `.gitattributes` (`*.jsonl text eol=lf`) added; `index.jsonl` spot-checked
  (no section references a de-phased heading â†’ keep as-is, no regeneration).
- âœ… **Step 4 (install-independent parts) done** â€” README rewritten around the drag-drop install;
  dead `D:\Wabbajack` path fixed; Layout/Status de-internalized; "Not for public redistribution"
  dropped; marketplace renamed `authoria-requiem-local` â†’ `authoria-requiem-skills` and de-"private"d.
- ðŸ”‘ **INSTALL METHOD RESOLVED â€” drag-drop, no CLI.** A pure-markdown skill plugin installs by copying
  the `authoria-requiem` folder into `C:\Users\<user>\.claude\skills\` + restarting Claude Code;
  it loads namespaced as `/authoria-requiem:â€¦`. **Proven empirically** â€” that's exactly how the
  existing live install loads (all 9 skills show up namespaced in-session). No setup.exe needed
  (unlike houseCARL, which has one only to register its .NET MCP server). `/plugin marketplace add`
  is an *in-app* slash command (not shell CLI) â†’ keep as the optional advanced path. `plugin.json`
  is load-bearing (makes the drop load as a clean namespaced plugin) â†’ keep it.
- â³ **Remaining = Heisen-gated:** Step 2 (LICENSE) + Step 3 (author/credit) wait on the Heisen
  decision; then Step 6 (push) + P3 (packaging).

## Target structure (what's public vs. local)

```
PUBLIC (pushed):
  authoria-requiem/            the plugin (9 skills, references/, evals/)
  .claude-plugin/marketplace.json
  README.md                    public framing
  CHANGELOG.md
  LICENSE                      MIT
  .gitignore                   (+ dev/ ignored)
  .gitattributes               *.jsonl text eol=lf

LOCAL ONLY (dev/, gitignored â€” never pushed):
  dev/STATE.md                 build ledger
  dev/PUBLIC-RELEASE-PLAN.md   this file
  dev/handoffs/                8 per-phase build handoffs
  dev/reqtificator-rules.md    the Reqtificator rulebook (dev reference)
```

Note: `docs/handoffs/` + `docs/reqtificator-rules.md` move **into `dev/`** (so the whole private set
is one gitignored folder). The README/CHANGELOG already cite the shipping copies inside the skill,
not `docs/`, so nothing public dangles.

---

## P2 â€” steps

**1. Split dev docs private + re-root for a clean public history.**
   - `git mv` (or move) `STATE.md` â†’ `dev/`, `docs/handoffs/` â†’ `dev/handoffs/`,
     `docs/reqtificator-rules.md` â†’ `dev/reqtificator-rules.md`. `docs/` is then empty â†’ remove it.
   - Add `dev/` to `.gitignore`.
   - Tag the current state (`pre-public` or similar), then **re-root to a fresh single commit that
     excludes `dev/`** â€” so the dev docs are present locally (untracked) but were never in any commit
     that will be pushed. (Same fresh-single-commit move as the cleanup; pre-cleanup-scaffold +
     pre-public tags keep everything recoverable.)

**2. LICENSE (MIT).** Add a standard MIT `LICENSE` at root. Copyright holder = the name settled with
   Heisen (see OPEN). Reference it from the README footer.

**3. Heisen credit.** Apply the settled author/credit decision to `plugin.json` (`author`),
   `marketplace.json` (`owner`), the README credits block, and the LICENSE copyright line.

**4. Public framing (README + manifests).**
   - README: remove "**Not for public redistribution**" (last line). Fix the dead install path
     (`/plugin marketplace add D:\Wabbajack\Authoria-requiem`) â†’ the public GitHub marketplace-add
     form (`/plugin marketplace add <owner>/<repo>` + a local-clone fallback). Keep the honest
     "built for / corpus mined on Authoria; re-point houseCARL at your own Requiem instance" caveat.
   - `marketplace.json`: rename `authoria-requiem-local` â†’ a public marketplace name.
   - Add a **Credits** block: Requiem / Magic Redone (NoxCrab) + the Reqtificator; houseCARL
     (the required MCP); the houseCARL skill-authoring standard.

**5. Hygiene.**
   - Add `.gitattributes` with `*.jsonl text eol=lf` (mirrors houseCARL's targeted rule; stops the
     LFâ†”CRLF churn on the `index.jsonl` files without a noisy global renormalize).
   - `index.jsonl`: these are `topic â†’ file + section` aids (named sections, **no line numbers**), so
     they survived the fix-batch edits fine. Spot-check that the handful of **de-phased headings**
     (e.g. "Scope & Authority") still match their index entries; keep them (they match the standard's
     reference-layer shape). Drop only if you'd rather rely on plain grep.

**6. Create the public GitHub repo + push.** *(Outward-facing â€” needs an explicit go, and the Heisen
   items settled first.)* New **public** repo under Avick3110; commit email already `<email-redacted>` âœ“.
   Push the re-rooted clean history. Verify `claude plugin marketplace add Avick3110/<repo>` installs.

---

## P3 â€” packaging + Nexus

- **Install is drag-drop (resolved â€” see Progress).** So there's no complex installer to build. P3 is:
  (a) a small **packaging script** that zips the `authoria-requiem/` plugin folder for Nexus
  (decide whether to strip `evals/` from the shipped zip); and (b) **optionally** a one-click
  `install.bat` that copies the folder into `%USERPROFILE%\.claude\skills\` for users who'd rather not
  navigate to the hidden `.claude` folder â€” weigh against SmartScreen/AV-warning friction (lean: ship
  the plain folder + clear instructions first; add the `.bat` only if users ask).
- **Nexus page.** "Authoria-flavored" framing; requires-houseCARL prominently; MIT permission mapping;
  credit Requiem (NoxCrab) + houseCARL. (Aaron drives the manual Nexus upload, as with houseCARL.)

---

## Deferred / not a repo task

- **Live install refresh.** `~/.claude/skills/authoria-requiem/` is still the pre-fix content; it
  needs re-installing from the cleaned repo (a file copy + a Claude Code restart to re-read). Aaron's
  local hygiene â€” independent of the public-repo work.
