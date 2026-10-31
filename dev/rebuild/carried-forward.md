# Carried forward from 1.x — spec inputs

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: LIVING (until the spec absorbs it). Every open 1.x follow-up, in one place, so the rebuild spec
can take each one or drop it on purpose. Sources: the 7 GitHub issues that were open on 2026-10-31 (all
closed that day as superseded by the rebuild, each pointing here) and the open section of the frozen
[`../archive/BACKLOG-1.x.md`](../archive/BACKLOG-1.x.md). New small follow-ups noticed during the rebuild
go at the bottom.*

Each item is a lead, not a fact. Re-derive it against the recon (the census, the split hunts, the corrected
fact sheet in `eval-2026-10/distillation/`) before it becomes a requirement. Several are 1.x patches to
rules the rebuild deletes outright.

## From GitHub issues (closed 2026-10-31)

| # | Issue | Skill | What it asked | Recon to check it against |
|---|---|---|---|---|
| I1 | [#27](https://github.com/Avick3110/authoria-requiem-skills/issues/27) | magic | Vanilla-FormID aliasing reads as "already Requiem-integrated"; use ManualCostCalc as the is-it-patched test | split 12 (MGEF tagging); eval distillation, magic |
| I2 | [#29](https://github.com/Avick3110/authoria-requiem-skills/issues/29) | armour | Conjured-ally worn gear: an AR rule for player summons, not just enemies | split 3 (AR 0 by slot) |
| I3 | [#30](https://github.com/Avick3110/authoria-requiem-skills/issues/30) | weapon | The Reqtificator skips NonPlayable weapons, so they need final-tier values | split 9 (NonPlayable keeps its own reach); R-round C# reads |
| I4 | [#31](https://github.com/Avick3110/authoria-requiem-skills/issues/31) | npc | PcLevelMult on the RftI winner is a Reqtificator decision to respect | split 1 / 7 / 14 (NPC workflow) |
| I5 | [#32](https://github.com/Avick3110/authoria-requiem-skills/issues/32) | leveled list | Self-cast utility gear-scaling lists are an exception to de-levelling | census stage 2, LISTS |
| I6 | [#33](https://github.com/Avick3110/authoria-requiem-skills/issues/33) | router | Load tool-output-awareness and reconcile it with scope-and-authority (doctrine; it had asked for Heisen's review) | recon A1 (gotchas, lean router); eval distillation, router |
| I7 | [#46](https://github.com/Avick3110/authoria-requiem-skills/issues/46) | script | Taught write-call spellings that don't bind on houseCARL 2.x | moot if the rebuild ships no tool recipes (eval HU-T6); recon A6 |

## From the 1.x backlog (frozen 2026-10-31)

| # | Item | Note |
|---|---|---|
| B1 | S4 Gray Cowl findings F1–F8: summoned-actor ownership seam, NonPlayable combat-gear skip, unzoned cell → ECZN, COBJ material recipes, flags-are-unions, perk-superset double stamp, FormID-resolve gate, router-table rows | Ledger: `../archive/1.x-build/s4-graycowl-validation/FINDINGS.md`. Split 10 covers ECZN; split 9 covers NonPlayable |
| B2 | Root README "Authority model" paragraph is stale against the 1.0.1 live-winner doctrine | Rewrite it with the 2.0 README |
| B3 | Scan forwarded lists for `REQ_NULL_*` in the armour / weapon / ammo / list lanes | The eval found REQ_NULL = inert is false (only PERKs are emptied); re-derive |
| B4 | Staff-frame routing seam: magic-led jobs skip the weapon rules on staff WEAP frames | Router coverage plan |
| B5 | Check `gh pr list` before picking a version number (two 1.4.0s collided silently) | Process: one line in the PR template |
| B6 | NPC↔RACE routing coin-flip on race-caused creature symptoms | Fewer, wider skills (recon A1) may dissolve it |
| B7 | `evals/` schemas are heterogeneous | Moot if the rebuild replaces the evals (recon A5) |

## New during the rebuild

*(append here: item, why, source, date, author)*

### For the spec session: discuss first (Aaron, 2026-10-31)

| # | Topic | Aaron's current thinking | Inputs |
|---|---|---|---|
| N1 | **Write-gate hook** | It may cause problems and be overkill. Discuss before adopting. | Recon A3 and A7: works mechanically, but must check something the agent can't write; Windows PowerShell hooks only fixed in 2.1.309–316 |
| N2 | **Coverage script** | Probably the logical thing to do, depending on what houseCARL already returns. An agent can ignore parts of a tool response; a script can mechanically flag every record that isn't patched and has no reason against it. | Recon A1 ("bundle a script for critical validation"); the eval's main control failure (records dismissed unread); open question: how much of the diff houseCARL record reads already give |
| N3 | **Scripts (.pex)**: discovery, understanding, accounting | Not discussed yet | 1.x `requiem-script-patching`; split 13 (follower bridge); houseCARL decompile |
| N4 | **Assets** (meshes, textures, sounds, animations): discovery, understanding, accounting | Not discussed yet | houseCARL asset tools |
| N5 | **Runtime layers** (SPID, KID, SkyPatcher, OAR, SKSE configs): discovery, understanding, accounting | Not discussed yet | houseCARL `skypatcher_layer` and the SPID / KID / SkyPatcher skills; census gotcha: runtime distribution is invisible to record references unless it goes through an outfit record |

N3–N5 may belong in the coverage script's scope (N2): the record diff covers plugins only. Live probe of what
houseCARL 2.1.2 gives for each (2026-10-31): [`recon/coverage/N3-N5-scripts-assets-runtime.md`](recon/coverage/N3-N5-scripts-assets-runtime.md).
Ownership is settled: houseCARL owns the runtime-layer reader, the SkyPatcher target filter and batch decompile
(`decisions.md`, 2026-10-31); spec those steps against the capability, not today's calls.
