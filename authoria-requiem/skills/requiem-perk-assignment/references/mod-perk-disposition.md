# Disposition of a mod's own PERK records

A patched mod often ships its own PERK records. This skill **never authors PERK records and never
re-tunes Requiem's** — but every mod-shipped PERK must land in a disposition, or the type falls out
of the worklist silently (the router's PERK row sends them here). The whole-order reality: 1,876
PERK records across 168 defining plugins on the reference load order — most definers are mods.

## The three-way rule

Enumerate first (`cross_plugin_query plugins=["<Mod>.esp"] type="PERK" defined_in=true
fields=["Name","Playable","Hidden","NextPerk","NumRanks"]`), then walk every row:

1. **Hidden + prefix-family + no player-facing Name → the mod's own runtime plumbing. Leave it
   alone.** The owning system (the mod's scripts/MGEFs, or the Reqtificator for `RFTI_*`) places
   and removes it; hand-editing or hand-placing it breaks that machinery. This is also why these
   perks never belong on an NPC you're patching.
2. **Playable + Named + a balance-bearing magnitude that parallels a Requiem gate → flag to the
   user with the specific overlap named.** Not a generic "found a perk" — a concrete question:
   *"`<perk>` grants +25% health regeneration, which parallels Requiem's standing-stone/regen
   economy; its host mod drives it via <system>. Fold it onto the Requiem gate, keep it as the mod
   mechanic, or neutralize it?"* An explicit question beats a silent rebalance — nothing downstream
   audits perk magnitudes.
3. **An obvious flat duplicate of a Requiem player-tree node with no mod-specific mechanic → fold
   it** (retarget the mod's references to the Requiem perk, neutralize the duplicate) — the
   `requiem-patching` router's standing rule that a duplicate gate is folded, not stacked. This
   case is **rare**; state in the summary that you folded and why it was obvious.

Requiem-side context that calibrates "parallels a Requiem gate": Requiem's own perk economy is
closed — crafting gates (smithing/alchemy perks in COBJ conditions), school specialization gates,
regen/resist economies (standing stones, physique traits). A mod perk whose effect lives in one of
those economies is case 2 by default.

## Calibration cases (read live)

| Mod (PERKs defined) | Sampled | Signals | Disposition |
|---|---|---|---|
| Apocalypse - Magic of Skyrim (57) | `WB_StrengthOfEarth_Perk 0A12C0`, `WB_InvisibleManager_Perk`, … | Hidden, no description, spell-mod internals; several already carry a `Requiem - Apocalypse - Magic Redone Patch.esp` override | **Leave alone** (case 1). The spell mod attaches these via its MGEFs; a provably broken spell is a `requiem-magic-patching` job, not perk work |
| Subclasses of Skyrim (51) | `DAR_Perk03Constitution 00080B` — Playable, "Health Regeneration +25%" | Player-facing class system whose magnitudes parallel Requiem's regen/birthsign economy | **Flag with the overlap named** (case 2) |
| Shadow of Skyrim (18) | `_pRB_StalwartDefense 00090C`, `_pDB_FearOf*` | Hidden, nemesis/death-alternative runtime, script-driven | **Leave alone** (case 1) |

The rule generalizes by the *signals* (Hidden/Playable, Name, magnitude, owning system) — the
prefix conventions above are evidence from three mods, not an exhaustive registry; read the
records, don't pattern-match the plugin name.

## Reconciliation

Close the pass with the count: **left-alone + folded + flagged = enumerated**, and every flagged
perk's question actually surfaced to the user. A PERK with no disposition is the silent-skip
failure the router's catch-all exists to kill — this skill is that catch-all's owner for the type.
