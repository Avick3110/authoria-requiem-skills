# Session handoff — 2026-07-17 (closeout): pack at 1.5.0, next test = Apocalypse re-do (gated on houseCARL 1.9)

*Author: Aaron (+ Claude session) · 2026-07-17*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-17_field-report-round3-npc-gaps-1.4.0.md` (Heisen's, merged as 1.5.0) as
the latest "where are we." Reads the whole 2026-07-17 day across both lanes.*

## Where the pack is

- **`main` at `509f6c4`, plugin `1.5.0`, live install synced + verified.** Zero open PRs, no
  lingering worktrees.
- **Three releases shipped today**, both maintainers' lanes reconciled:
  - **1.4.0** (`ba966de`, Aaron) — the 1.3.0 empirical close: creature-innate spell lane
    ([#21](https://github.com/Avick3110/authoria-requiem-skills/issues/21)) + follower-registration
    rewrite ([#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15)).
  - **1.5.0** (`509f6c4`, Heisen) — field-report round 3: NPC kit adequacy, template chain-walk,
    mounts lane, ghost state, state-variant races.
  - Two docs commits between them: the *"a report is a lead, not a fact"* operating principle
    (`c8fe220`, now in `CLAUDE.md`) and a BACKLOG note on the version-collision hazard (`a39411c`).
- **Issues #15, #19, #21 all closed.** #19 (the DNAM/`TemplateFlags` note) was *withdrawn* on the
  maintainers' ruling — the blanket DNAM rule is deliberate; Heisen's 1.5.0 "walk the chain to a
  patched base, never flags alone" is the strict-improvement version of what #19 was groping at.

## What happened with the version collision (so it's not a mystery)

1.4.0 was authored **twice** from the same `cd5b2cf` base — Heisen's #20 (open 11:32) and Aaron's #22
(merged ~14:40 **without checking for open PRs first**). Resolved by rebasing #20 to **1.5.0** and
repositioning its CHANGELOG entry; Heisen's skill content came through byte-identical. Root-cause note
in `dev/BACKLOG.md`: **`plugin.json` does not conflict when both sides write the identical version
string — it auto-merges silently**, so "rebase and re-check both files" isn't enough; run
`gh pr list --state open` and read each open PR's CHANGELOG *before* picking a number. **One residual
cosmetic quirk:** `509f6c4`'s commit *subject* still reads `…(1.4.0)` while its contents correctly
bump to 1.5.0 — Heisen's authored subject, left unrewritten (rewriting a collaborator's merged commit
message on protected `main` isn't worth it). `plugin.json` + the CHANGELOG heading are the
authorities and both say 1.5.0.

## Val Serano — it was the test harness, not a deliverable

The Val Serano patch existed only to **test the pack's own output** against new doctrine (1.3.0, then
1.4.0). It is **not a shipping deliverable and needs no further work:**

- **Heisen fixed his side by hand independently** — the hand-fix, not our ESP edits, is the reference.
- The 1.4.0 session made in-place houseCARL edits to the patch ESP **on Aaron's instance** (3 DNAM
  records, the T4 shock riders on 3 spells, 2 MGEF tier markers). Those live on Aaron's copy but are
  **moot** — no reconciliation with Heisen's hand-fix is owed, and **the earlier "run the
  Reqtificator for the Val Serano edits" TODO is dropped.** It was a test bed; the test is done.

## Next — get current, then re-do Apocalypse

**Gate: houseCARL 1.9 is about to release. Bring both houseCARL and the skills current before the
next test.** Order of operations for whoever picks this up:

1. **Update houseCARL to 1.9** when it lands (per its own repo). Then:
   - **Re-sync `standards/`** — the three skill-authoring standards here are *copies* synced from the
     houseCARL standards repo (upstream). If 1.9 changed them, re-sync the copies (`CLAUDE.md` →
     Authoring standards). A changed §6 fan-out or §8 checklist changes how we review the next skill.
   - **Re-check the skills against any changed/new houseCARL tool signatures** — the skills' recipes
     name specific tools (`bulk_apply` composes, `where=` predicates, `cross_plugin_query` flags). A
     1.9 signature change could silently break a recipe. Spot-check the magic + npc recipe files.
2. **Confirm both maintainers are on skills 1.5.0** (`robocopy … /MIR` + restart Claude Code). Heisen
   was several releases behind at last note — he specifically needs the catch-up sync.
3. **Then the live test: re-do the Apocalypse - Magic of Skyrim patch.** `Apocalypse - Magic of
   Skyrim.esp` is ACTIVE on Aaron's instance. It is a **magic-dominant behemoth — the ideal stress
   test for everything the magic lane shipped today.** Shape (records *defined in* the plugin,
   measured 2026-07-17):

   | Lane | Count | Notes |
   |---|---|---|
   | **Spell** | **373** | the main event — cost ladders, rider doctrine, `_NPC` vs creature-innate branch (#21) |
   | **MagicEffect** | **556** | tier markers, keywords, conditions, resistance-map NULL sweep |
   | Scroll | 144 | mirror their spell |
   | Book | 176 | spell tomes — value ladder |
   | ObjectEffect (ENCH) | 72 | enchantment gear-value coupling |
   | Perk | 57 | HalfCostPerk classifiers + any custom PERKs |
   | Projectile | 149 | delivery/FX chains |
   | *(secondary)* | | Weapon 95, Armor 87, NPC 42, Race 9, COBJ 67, LVLI 12 |

   ~3915 records defined across 59 types. This is a **whole-mod router job** — load `requiem-patching`
   first, enumerate, route per-lane. It will exercise the creature-innate lane, the rider doctrine,
   the resistance map, and the cost/charge ladders harder than any patch to date. **"Re-do" implies a
   prior patch exists** — check for an existing `Apocalypse … Requiem Patch.esp` and decide rebuild
   vs. diff before starting.

## Standing items (unchanged, none blocking)

- perk-assignment Q8 description tweak (BACKLOG); root README authority-model section (needs Aaron);
  original-nine empirical re-validation; F-VS2 NULL-forward scan still open for armor/weapon/ammo +
  leveled-list lanes if a live run shows NULLs riding those.
- **houseCARL:** zero tool bugs across this whole session (~60 read calls + 2 bulk writes). Nothing
  to file.
