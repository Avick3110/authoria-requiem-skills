# Session handoff — 2026-07-16: script-patching VMAD sweep collapsed to one `exists` query (1.2.3)

*Author: Aaron (+ Claude session) · 2026-07-16*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-15_collab-conventions-live.md` as the latest.*

## What this session did

Executed the standing `dev/BACKLOG.md` entry "Simplify `requiem-script-patching`'s VMAD sweep if
houseCARL grows a struct-presence filter" — the condition landed: houseCARL issue
[#197](https://github.com/Avick3110/houseCARL/issues/197) is closed and `cross_plugin_query`'s
`where=` now carries `exists` / `missing` presence tests that match struct fields.

**Re-probed live before editing** (per the entry's own gate):

- `type="ACTI" where=["VirtualMachineAdapter exists"]` → 6766 matches (struct presence matches).
- `plugins=["Requiem.esp"] defined_in=true where=[…exists] group_by=type` → one call sweeps all
  record types at once (305 VMAD carriers across 11 types).
- `where=` + `fields=` combine; struct fields render as an overlay marker (no `depth=` on
  `cross_plugin_query`), so hits expand via `housecarl_batch_record_detail depth=3`.

**The edit (branch `claude/vmad-sweep-simplify`, v1.2.3):** sweep (a) of the Bulk pass protocol —
per-type fan-out (MGEF/BOOK/ACTI/FURN/QUST) → one cross-type
`where=["VirtualMachineAdapter exists"]` query, in `SKILL.md` (protocol + checklist),
`references/housecarl-recipes.md`, and the two `index.jsonl` topic rows. The old inline
"no single cross-type sweep is derivable" rationale dropped. Side benefit stated in the CHANGELOG:
the one-call sweep removes the unswept-host-type gap by construction. Body-only — no description
changed, no §6.5 re-measure owed. `claude plugin validate --strict` passed. BACKLOG entry moved to
Done (siblings #195/#196/#198 remain houseCARL-side).

## State at close

- PR open from `claude/vmad-sweep-simplify`, awaiting merge; `main` untouched.
- **Live install NOT yet synced** — sync (`robocopy … /MIR`) after the PR merges.

## Next

1. Merge the PR (rebase on latest `main` first per the collision rule), sync the live install,
   delete the branch.
2. Standing items unchanged: Gray Cowl regeneration with the current pack (validates F1–F8);
   root README authority-model rewrite (needs Aaron); perk-assignment Q8 description tweak
   (triggers §6.5 if taken); original-nine empirical re-validation.
