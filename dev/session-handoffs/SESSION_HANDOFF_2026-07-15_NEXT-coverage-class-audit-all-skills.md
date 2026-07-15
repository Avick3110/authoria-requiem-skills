# Session handoff — 2026-07-15 (later): NEXT SESSION = coverage-class audit of the entire skill surface

*Class: ARCHIVE once superseded (per `standards/HOUSECARL_DOC_HYGIENE.md`). Supersedes
`SESSION_HANDOFF_2026-07-15_dev-env-built-docs-audited-PR3.md` as the latest — that one's "nothing in
flight" is no longer true. Written by the dev-env session at Aaron's direction, chartering the next
session's work.*

## Why — Heisen's report (2026-07-15 morning)

**DrHeisen messaged Aaron this morning: the skills are failing to guide the agent to patch things
properly — more missed records, and entire record types skipped.** This is field evidence that
HCBR-2026-07-14-03 (the 1.0.2 fix) was one instance of a **class**, not a one-off: the 07-14 fix
tightened only `requiem-npc-patching` (the new *Bulk pass protocol*) and the router's checklist — the
other seven domain skills never got the equivalent hardening, and "entire record types skipped" points
at the router lane too, which the NPC fix only touched at checklist level.

### Heisen's report, verbatim (his message to his agent after a large patching job; relayed by Aaron)

> 1) you didnt patch perks for the npcs
> 2) you didnt patch stats for the npcs
> 3) you didn't delevel leveled lists
> 4) you skipped some combatant npcs and didnt patch them at all (non-templated ones)
> 4.5) you kept some npcs pc level mult
> 5) you didnt rebalance object effects
> 6) you didn't rebalance perks
> 7) you didn't balance all the races, you should include gimmik and visual races in patching
> 8) you didn't balance the value of spell books
> 9) you didn't rebalance all spells
> 10) you didnt rebalance/patch magic effects
> this is unrelated to this patch specifically, but the requiem skill also skips food, ingredient,
> potions etc.
> the patcher also frequently skips patching perks, spells, stats, base stats, and skill offsets for
> npcs. these are repeated across sessions when using the requiem skill.

**Mapping — item → lane → bug class** (classes defined in the taxonomy below):

| Item | Lane | Class |
|---|---|---|
| 1, 2 + the recurring "perks/spells/stats/base stats/skill offsets" | `requiem-npc-patching` | **6 — field-level incompleteness** (record dispositioned, balance fields not carried) |
| 4 (non-templated combatants skipped), 4.5 (PC mult kept) | `requiem-npc-patching` | 1–3 — the exact 1.0.2 class; **check his plugin version** — if he ran the 1.0.2 protocol, it failed and the fix needs re-examination, if pre-1.0.2 these two items are expected |
| 3 (no de-levelling) | `requiem-leveled-list-patching` | 1/3 — coverage gating / no work-queue |
| 9, 10 (spells, MGEFs), 5 (object effects/ENCH), 8 (spell-book BOOK value) | `requiem-magic-patching` | 1–4 coverage; 8 also cross-type (BOOK carried by the magic job) |
| 7 (gimmick + visual races excluded) | `requiem-race-patching` | scope rule too narrow — a skip *category* wrongly excluding patchable records |
| 6 (PERK records), food/ingredients/potions (ALCH/INGR/food) | `requiem-patching` router | **5 — whole types unrouted/unowned**: verify whether ANY domain skill owns PERK and consumables; if none, that's a coverage hole in the pack itself, not a skill bug |

"Repeated across sessions" = these are doctrine failures in the skill bodies, not one bad run.
Open question for Aaron/Heisen before fixing (not before auditing): which plugin version the job ran
on, and whether his live install was current — items 4/4.5 read differently on each answer.

## The task — audit all 9 skills for the coverage bug-class

The 1.0.2 bug, generalized — hunt for every place a skill permits any of:

1. **Per-TYPE coverage gating** — a checklist or flow that clears a record type on "some records
   patched" instead of *every record dispositioned*. (The 1.0.2 root cause.)
2. **Sample-and-extrapolate** — reading representative members of a group (same-prefix EditorIDs,
   same-material tiers, same-slot armor families, shared-template NPCs, leveled-list sublists…) and
   extrapolating to unread siblings. Each domain has its own "uniform-looking family" shape — name it
   per skill.
3. **No enumeration-as-work-queue** — workflows that start from "find the records that need X" (a
   filtered query, or worse, judgment) rather than *enumerate the type, then disposition every row*.
   A filter is a work-list *finder*; only the full enumeration is the coverage *denominator*.
4. **No reconciliation count** — a pass that can end without proving patched + skipped = enumerated.
5. **Whole types never routed** (the router, `requiem-patching`) — Heisen's "entire record types
   skipped": check the router's enumerate→route table covers every record type a mod plugin can
   carry, that types without a domain skill get an explicit disposition ("cosmetic — skipped, reason")
   rather than silence, and that its integration checklist can't clear with an unrouted type. Check the
   worklist-building step itself for sampling. Per Heisen's report, **PERK and consumables
   (ALCH/INGR/food)** are the concrete suspects — determine whether any domain skill owns them at all.
6. **Field-level incompleteness within a dispositioned record** — a record counts as "patched" while
   only some balance fields were carried (Heisen: NPCs got levels but not perks, spells, stats, base
   stats, skill offsets — *repeatedly, across sessions*). The 1.0.2 reconciliation count is blind to
   this by construction: it proves every record was touched, not that every field the domain's doctrine
   names was derived. The audit must check each skill's workflow for a **per-record field checklist**
   (the set of fields the live-analogy derivation MUST carry, stated as a gate, not prose) — and
   whether the skill's "replicate the analogue's fields" step enumerates the fields or leaves them to
   judgment.

**Reference implementation:** the *Bulk pass protocol* in `requiem-npc-patching/SKILL.md` +
`references/housecarl-recipes.md` (added 1.0.2, commit `01ab413`) — one-call coverage sweeps,
enumeration-as-work-queue, per-record disposition verified per record, reconciliation count,
no-extrapolation rule. The audit's likely output is a per-domain equivalent (each domain's sweep
queries and skip-categories differ — an armor triage matrix is not an NPC one), plus router hardening.

## How (suggested — next session's call)

- Read order: this handoff → Heisen's report (see provenance note) → the 1.0.2 fix diff (`01ab413`)
  → then the skills.
- A per-skill fan-out works well (one agent per domain skill, each reporting findings against the
  6-class taxonomy above, with the mapping table naming each skill's known suspects) — **use Opus subagents, not Fable** (Aaron's standing instruction:
  `model: "opus"` on Agent calls, don't burn Fable on fan-out reading).
- Findings → fixes is one lane: worktree `claude/<name>` → PR → **Aaron's go** (main is PR-protected).
  A fix batch = version bump (1.0.3) + CHANGELOG entry per repo convention.
- Standards note: if any skill *description* changes, the re-synced standard (2026-07-15) requires the
  §6.5 fan-out re-measure. Body-only changes (likely, as in 1.0.2) need no trigger re-validation.
- Empirical close (principle: a plan reviewed is not a thing proven): re-run one of Heisen's failing
  cases — or the Gray Cowl pass — under the fixed skills and show the reconciliation counts close.

## Standing context (from earlier today, still true)

- Dev environment is in place: this lane, `dev/BACKLOG.md` (2 open items — the "Aaron"-naming fix
  could ride the same 1.0.3 content bump), `dev/plans/`, PR #3 merged (`c1cc885` = main tip).
- Live install at `~/.claude/skills/authoria-requiem/` is a copy — after a fix lands, robocopy sync +
  restart Claude Code (loop documented in CLAUDE.md), or Heisen keeps seeing the old behavior. Note the
  1.0.2 lesson from houseCARL's side: a fixed repo with a stale live install *generates fresh bug
  reports of already-fixed bugs*.
- Repo = source of truth; version lives in `plugin.json` only.
