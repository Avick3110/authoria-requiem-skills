# authoria-requiem-skills — backlog (self-noticed follow-ups)

*Standing LIVING doc, on the houseCARL model (see that repo's `dev/BACKLOG.md`). The durable home for
**small, dev-noticed follow-ups** — skill papercuts, doc gaps, deferred checks — that would otherwise
get lost in a superseded handoff. Sibling to `session-handoffs/`: handoffs are meant to be superseded;
this list persists.*

**Not** for: skill bugs and coverage gaps (→ GitHub Issues on this repo; houseCARL tool bugs/gaps →
the houseCARL repo's Issues); chartered work (→ `dev/plans/`); in-flight PR state (→ latest handoff +
`git log`).

**How to use:** append when you notice one (enough context to act without the originating
conversation); prune when done — delete or move under *Done* with the resolving commit. Keep it short.

---

## Open

- **S4 findings ledger F1–F8 → fixes batch.** The Gray Cowl empirical close (2026-07-15) passed all
  charter gates but surfaced 8 skill-body findings — summoned-actor ownership seam, NonPlayable
  combat-gear skip taxonomy, unzoned-cell→ECZN, COBJ material recipes, flags-are-unions doctrine,
  perk-superset double-stamp, FormID-resolve gate, router-table rows. Full ledger with fix
  candidates: `dev/s4-graycowl-validation/FINDINGS.md`. Body-only edits expected (no §6.5
  re-measure unless a description changes). `(2026-07-15, S4 session)`

- **`requiem-perk-assignment` description tweak candidate (from the PR #6 review + eval Q8 miss).**
  The trigger list covers "handle a mod's custom perk records" but not the *clash/overlap-with-a-
  Requiem-gate* framing that `mod-perk-disposition.md` case 2 turns on — the Q8 eval query ("custom
  perk giving 30% more smithing yield — does that clash with Requiem's crafting gates?") missed for
  exactly that reason. If tightened, add that phrase — and note any description edit re-triggers the
  full §6.5 fan-out re-measure, so it's a deliberate follow-up, not a quick tweak.
  `(2026-07-15, S3 session; Aaron's review observation)`

- **Root README "Authority model" section looks stale vs the 1.0.1 doctrine.** It still says the
  Authoria overlay (`Requiem for the Indifferent.esp`, `Authoria - Reqtificated/*`) is "disabled …
  excluded" — the authoring-era scope. Since 1.0.1 the shipped doctrine is mode-aware: the live
  winner (RftI included) is the authority. Surface to Aaron and rewrite the paragraph to match
  `scope-and-authority.md`; deliberately NOT folded into the 1.1.0 consumables PR (a public
  doctrine statement needs his eyes). `(2026-07-15, S2 consumables session)`

- **Empirical trigger re-validation of all 9 skill descriptions.** Descriptions shipped validated via
  the §6.5 manual-prediction fallback + independent peer-prediction (empirical `run_loop` eval is
  blocked on Windows, §6.4) and are flagged for empirical re-validation when a non-Windows host is
  available. `(CLAUDE.md §Repo-specific deviations · standing since 1.0.0)`

- **Add an explicit "scan forwarded lists for `REQ_NULL_*`" step to domain-skill bulk passes**
  (npc first; any lane that forwards ActorEffect/Perks/list fields). Val Serano live run: lanes
  correctly authored no NULLs but *forwarded* 7 that rode in on NPC list fields; only the
  integration gate's whole-patch scan caught them. Finding F-VS2 in
  `session-handoffs/SESSION_HANDOFF_2026-07-16_valserano-first-live-run.md`.
  `(2026-07-16, Val Serano live run, Aaron)`
  **Update 2026-07-17 (1.3.0, Heisen):** done for the two biggest forwarders — npc (sweep 4 of the
  coverage audit, `resolve_names` over Perks/ActorEffect/Keywords) and magic (the NULL scan over
  Spell/ObjectEffect/Ingestible `Effects`). Still open for the remaining list-forwarding lanes
  (armor/weapon/ammo keywords + leveled-list entries) if a live run shows NULLs riding those.
## Done

- ~~Router caveat: patch-plugin enumeration counts ≠ distinct-record counts (F-VS3)~~ — **fixed
  2026-07-17** in the 1.3.0 batch: `requiem-patching` First step 2 now states per-plugin
  `group_by=type` counts overlap across compat patches and the lane's own enumeration is the
  true denominator. `(1.3.0, Heisen)`

- ~~Simplify `requiem-script-patching`'s VMAD sweep if houseCARL grows a struct-presence filter~~ —
  **done 2026-07-16** (the 1.2.3 batch): houseCARL shipped the `exists` / `missing` presence tests
  (issue [#197](https://github.com/Avick3110/houseCARL/issues/197) closed); re-probed live (struct
  match + one `plugins=`-scoped cross-type call, both confirmed) and sweep (a) collapsed to a single
  `where=["VirtualMachineAdapter exists"]` query in SKILL.md + `housecarl-recipes.md` + the index.
  Sibling issues [#195](https://github.com/Avick3110/houseCARL/issues/195) /
  [#196](https://github.com/Avick3110/houseCARL/issues/196) /
  [#198](https://github.com/Avick3110/houseCARL/issues/198) remain houseCARL-side.
  `(2026-07-16, Aaron)`

- ~~"Aaron" named in three shipped reference files~~ — **fixed 2026-07-15** in `2e4a17e` (PR #4, the
  1.0.3 batch): all three now read "the author" / "author-approved".

- ~~Verify the live install is at 1.0.2~~ — **verified 2026-07-15**: `~/.claude/skills/authoria-requiem/.claude-plugin/plugin.json` reads 1.0.2. No sync needed.

*(move resolved entries here with the resolving commit, or just delete them)*
