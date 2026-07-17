# Session handoff — 2026-07-17: field-report round 3 (Val Serano NPC/race re-run) → 1.5.0 — PR open

*Author: Heisen (+ Claude session) · 2026-07-17*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-17_field-report-round2-1.3.0-PR.md` as the latest.*

## What this session was

Heisen's third field report: a fresh NPC/race patch of Val Serano
(`Authoria - Reqtificated - Val Serano.esp`, another session's output) shipped 13 bad records —
DNAM untouched, perks not added or badly balanced, whole records skipped (a soul copy, a chaurus
hatchling, a ghost fox + its race), a riding horse at level 50. Task: figure out why the skill
missed, fix the skill.

## The headline finding — most of the report was already fixed, but the session couldn't see it

The failing session ran on a **stale live install**. The live copy was mirrored at 12:13:48 on
2026-07-17 — twelve seconds after 1.3.0 (`cd5b2cf`) landed — so any patch session before that ran
pre-1.3.0 doctrine (and per the round-2 handoff, Heisen's install was pre-1.1.0 at last record).
That accounts for the DNAM omissions, empty-kit skips, and civilian misclassifications 1.3.0
already closed. **Standing lesson: after every `git pull` that lands pack changes, re-run the
robocopy sync + restart before patching — the round-2 handoff's "on BOTH machines" step is
load-bearing.** Consider a session-start check: live install `plugin.json` version == repo `main`.

## What shipped (branch `claude/field-report-round3`, version 1.5.0)

Five loopholes survive 1.3.0 and were closed, each with live-mined evidence
(`dev/corpus-2026-07-17-field-report-round3/mining-notes.md`); full per-item detail in the 1.5.0
CHANGELOG entry:

1. **Populated-but-sparse kits** — "has a handful of non-null perks" is not adequacy; the verdict is
   the comparison against the analogue's tier kit (SKILL.md Workflow C, `perks.md`, checklist).
2. **Template-skip chain walk** — Workflow A's already-templated skip credits only a chain ending in
   a Requiem-balanced or this-pass-patched base (the untouched `AX_TarekSeranoSoul` templated the
   mod's own `PCLevelMult` Tarek); drain-check keeper category for `Stats`-templated actors.
3. **Mounts/pets/livestock lane** — never the combat/boss ladder; ordinary horse fixed L4 / H289,
   Shadowmere (50/H1637 + Healing trait) the only supernatural-steed precedent (identification.md
   new section, npc-fields.md quick-ref rows, Judgment bullet, checklist).
4. **Alternate-state copies + ghost state** — patch the primary, template the copy onto it; spectral
   actors carry `ActorTypeGhost 0D205E:Skyrim.esm` on the NPC record (236 vanilla carriers) and the
   Reqtificator assigns `RFTI_Trait_Ghost 031284` at build — carry the keyword, never hand-stamp the
   perk; `perks.md`'s unresolvable ghost-keyword citation fixed. Recognized-race creatures:
   "almost nothing" is still a pass. Mod-theme named as a classification signal.
5. **`requiem-race-patching` state-variant races** — a spectral reskin classifies to its base
   creature analogue; the ghost state routes to the NPC layer via the keyword in the Layer-B
   handoff (the "Ghostly Fox" race answer: its stat block is field-identical to Requiem's FoxRace,
   so the skip was defensible — the missing piece was the disposition + keyword routing).

Body + references only — no descriptions changed, no §6.5 re-measure owed. NPC SKILL.md ~464 lines
(cap 500).

## Notes on Heisen's manual fixes (reviewed live, not modified)

- `AX_GhostFox`: the hand-stamped `RFTI_Trait_Ghost 031284` perk works but fights the auto-pass —
  swapping it for the `ActorTypeGhost` keyword is the input-carrying form.
- `AX_HorseBronze`: the fix templates onto Shadowmere with `Stats, SpellList` — the `Stats` flag
  makes the own fixed L1 inert, so it inherits Shadowmere's 50/H1637. If a mundane mount was
  intended, the `EncHorse*` tier (fixed 4) is the analogue; if a supernatural steed was intended,
  the current fix is coherent.
- `AX_TarekSeranoSoul` is **still untouched** in the live patch (the one reported record with no
  manual fix); once patched-Tarek is final it inherits via `Traits/Stats/SpellList/Inventory`, but
  its 3 own perks + 3 own spells still want the kit comparison, and a spectral/summon copy wants
  `ActorTypeGhost` reviewed against its intended state.
- `AX_TarekSerano` post-fix still carries the vanilla class (`NPCclassBelrand`), no combat style,
  and no tempering trait — a 1.5.0-doctrine re-run would set the role class/style/trait.

## Next / standing

- **PR open, awaiting maintainer go** (review-by-request: doctrine changes — worth Aaron's eyes).
  Rebase on latest `main` immediately before merge; re-check `plugin.json` + CHANGELOG position.
- **After landing: robocopy sync + restart on both machines**, then the empirical close: re-run the
  Val Serano NPC/race lanes under 1.5.0 and reconcile against the 13 reported records (the standing
  "a plan reviewed is not a thing proven" principle).
- houseCARL: zero tool bugs/gaps hit this session (~15 read calls, incl. batch conflict trees and
  a 236-match keyword reverse lookup). Nothing filed.
- BACKLOG items untouched (perk-assignment Q8 description tweak; root README authority section;
  original-nine empirical re-validation).
