# Session handoff — 2026-07-16 (follow-up): Val Serano follower registration REMOVED

*Author: Aaron (+ Claude session) · 2026-07-16*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-16_valserano-first-live-run.md` as the latest — same session, one
correction to its shipped state.*

## The correction

The first-live-run handoff records Val Serano's Requiem follower registration as **built**
(follower factions on the NPC override + a standalone `AX_ValReqFollowerBridgeQuest` +
compiled bridge script + `.seq`). **Aaron didn't want Val in the Requiem registration** — the
mod runs its own follower framework (`AX_ValController`), which the registration was never
asked to touch — so it was removed in full, same day:

- `CurrentFollowerFaction` + `PotentialFollowerFaction` stripped from Val's NPC override
  (`00AA0F:AX ValSerano.esp`) — faction list back to the author's original four, read-back
  verified.
- Quest `AX_ValReqFollowerBridgeQuest` deleted from `AX ValSerano - Requiem Patch.esp`
  (188 records remain).
- Loose files deleted from the patch mod folder (`Scripts/`, `Source/`, `SEQ/`) — it ships the
  bare ESP again.
- `housecarl_check_errors` after removal: 0 dangling refs, 0 missing masters. The rest of Val's
  override (the `REQ_NULL_AgileDefender80` perk strip) is intact and wanted.

## Lesson for the pack (candor note, no doc edit yet)

The router + npc/script skills prescribe follower registration *by default* for a follower mod.
This run shows the prescription needs a consent/authorial-framework gate: **a follower with its
own controller framework is a user decision, not a default registration** — vanilla/Requiem
follower factions can interfere with a custom recruit flow. Fold that into the #15 fix
(`follower-registration.md` rewrite) rather than as a separate issue: the same reference that
gets the standalone-bridge pattern should say "ask before registering a custom-framework
follower."

## State at close

Everything else from the first-live-run handoff stands (findings F-VS1..F-VS5, issue
[#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15) open, two BACKLOG entries
landed). Patch still awaiting the user's enable + LOOT sort + Reqtificator run.
