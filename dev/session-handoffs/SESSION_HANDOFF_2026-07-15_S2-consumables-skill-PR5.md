# Session handoff — 2026-07-15 (late): S2 consumables skill SHIPPED — PR #5 merged, 1.1.0 live

*Class: ARCHIVE once superseded (per `standards/HOUSECARL_DOC_HYGIENE.md`). Supersedes
`SESSION_HANDOFF_2026-07-15_coverage-audit-1.0.3-shipped.md` for "where are we"; the living charter
remains `dev/plans/COVERAGE_AUDIT_1.0.3_PLAN_2026-07-15.md` (S3–S4 outstanding).*

## What the session did

Executed **S2 of the coverage-audit charter (D2)** end to end: the NEW consumables domain skill.

1. **Discipline honored:** standards loaded fully; `anthropic-skills:skill-creator` invoked at
   session start (§8 prerequisite); worktree `claude/consumables`; freshness probe passed (ARR 2.0
   instance, Iron Sword chain contains Requiem.esp, winner RftI). **Aaron's new standing rule
   (saved to memory `agent-spawns-use-opus`): every Agent/Workflow spawn sets `model: opus`,
   `effort: high|xhigh` explicitly — never fable.**
2. **Corpus mined live** via a 4-agent Opus fan-out (potions/poisons · ingredients · food/drink ·
   integration/COBJ/Reqtificator-boundary) → 4 docs in `dev/corpus-consumables/` (main checkout,
   keep). Full accounting: 287 AR ALCH + 183 INGR + 196 FaB ALCH touches enumerated; ~200 records
   full-read. Note: the weapons-pilot `docs/CORPUS-MINING.md` + `docs/corpus/` are GONE with the
   old Downloads dev repo — method reconstructed from memory + the weapons skill template.
3. **Headline doctrine (query-verified):** the Reqtificator NEVER touches consumables — RftI
   defines only Npc (48,295) + LeveledNpc (2,649), zero ALCH/INGR/COBJ even as overrides. A
   consumable override IS the final balanced record. Other keepers: potions KEEP effect links
   (vanilla MGEF FormIDs are Requiem-overridden in place) and only rebalance the shell, while food
   gets its list REBUILT to the FaB kit — opposite techniques, deciding factor is whether the
   effects point at Requiem-overridden MGEFs; ingredient quartets have per-MGEF standardized
   durations with magnitude as tier knob; alcohol has no ALCH addiction field (script MGEF);
   AR/FaB are often NOT the live winner (Magic Redone, BOOBIES patch, Wounds patch, ATweaks etc.
   win) — live winner is authority, but the ECSS-won Curios set is a convention-broken baseline to
   flag, not propagate; alchemy crafts happen at the COOKPOT.
4. **Authored `authoria-requiem/skills/requiem-consumable-patching/`** — SKILL.md 303 lines,
   5 references + index.jsonl, eval_set (10+10). Bulk pass protocol baked in from birth (1.0.3
   doctrine). Boundary handshakes both ways: magic (new MGEF design), leveled-list (placement),
   script (VMAD carriers).
5. **§6.5 fan-out (mandatory): 20/20** — recall 10/10, specificity 10/10, zero misfires; 20
   fresh-context Opus/high judges, description verbatim as anonymized capability statement,
   adjudicated from reasoning. Archived: `evals/results-2026-07-15.json`.
6. **Integration:** router INGR/ALCH rows → new skill (SKILL.md + routing-table.md); alchemy.md/
   food.md keep constraints, point record work at the skill; magic skill's alchemy note states the
   boundary both ways; rosters (README table + skills/README) + CLAUDE.md "ten skills" +
   plugin.json **1.1.0** + CHANGELOG. All sibling edits body-only → no re-measure owed.
   `claude plugin validate --strict` passes (both manifests).
7. **Landed:** commit `ba21d96` + review-fix `2e2a1d1` (Aaron's review, 3 findings — two stale
   "seven domain skills" counts, CLAUDE.md deviation note, eval `_meta` phrasing — all fixed) on
   `claude/consumables` → **PR #5 MERGED (rebase) on Aaron's go — main tip `12b94c8`** (21 files,
   +1082/−33). Worktree + branch removed. **Live install robocopy-synced, verified 1.1.0**, the new
   skill's SKILL.md present. **S2 of the charter is DONE.**

## Where the next session picks up
- **S3 — perk-assignment skill** (D1): settle the Reqtificator-assignment boundary FIRST (1.0.1
  doctrine: never hand-stamp Reqtificator-assigned perks; the skill assigns existing Requiem perks
  from equipment/class, cautious by class, NEVER authors PERK records). Full new-skill discipline +
  §6.5 fan-out. Then router PERK row.
- **S4 — empirical close:** re-run a Heisen case (or Gray Cowl) under the fixed pack;
  reconciliation must close field-gated.
- **Backlog additions this session:** root README "Authority model" paragraph is stale vs the
  1.0.1 mode-aware doctrine (surfaced in PR #5 body, logged in BACKLOG — Aaron's call).
- **Tell Heisen to update his install** once 1.1.0 lands (standing item from S9).
