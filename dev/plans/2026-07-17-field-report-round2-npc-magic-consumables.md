# Plan — field-report round 2: NPC stats/classification, magic overhaul, consumables re-verify

*Author: Heisen (+ Claude session) · 2026-07-17*

**Class:** ARCHIVE — closed 2026-07-17, all steps executed in the same session; the PR and the
session handoff (`SESSION_HANDOFF_2026-07-17_field-report-round2-1.3.0-PR.md`) carry the state.

## Why

Heisen's second field report (2026-07-17, direct instruction to this session). The 1.0.3 coverage
audit and the 1.2.2 F1–F8 batch hardened *enumeration* (every record dispositioned, reconciliation
counts), but field evidence shows per-record *derivation* is still incomplete in three domains, and
one instruction stands over all of them: **every patching rule must be backed by a reference mined
from the live authority** — no rule that floats free of a reference.

The report, mapped:

| # | Report item | Skill | Nature |
|---|---|---|---|
| 1a | Perks/spells skipped on NPCs that had none to begin with | `requiem-npc-patching` | doctrine gap — "derive from the analogue" never states that an empty source list is not a skip signal |
| 1b | Base Stats + Skill Offsets in the NPC record never patched (DNAM `PlayerSkills.Health/Magicka/Stamina` + `SkillOffsets` — NOT the ACBS starting offsets) | `requiem-npc-patching` | field-model gap — references never name these fields. **Probe result 2026-07-17: fully readable via houseCARL** (`PlayerSkills.Health` etc.), so this is a skill fix, not a houseCARL gap |
| 1c | Many NPCs skipped for no reason | `requiem-npc-patching` | classification — skip taxonomy fires on weak single signals; needs positive-evidence skip rules |
| 1d | Authority = **all Requiem addons touching NPCs** | `requiem-npc-patching` | authority list must be enumerated live and carried as a reference |
| 2 | Spells: cost-only patching isn't enough — `ManualCostCalc` flag ticked, charge time, magnitudes/duration/area, ADD complementary effects per MR patterns, identify MR's new subclasses | `requiem-magic-patching` | doctrine depth — the body says these but the derivation isn't procedural/reference-backed enough to execute |
| 2b | Resistance spells entirely skipped; their resistance MGEFs are `REQ_NULL_*` and must be re-pointed to Requiem analogues | `requiem-magic-patching` | missing replacement map — 259 NULLed MGEFs confirmed live incl. all `AbResist*` |
| 3 | MGEFs: keywords, balance values, conditions | `requiem-magic-patching` | reference gap — no conditions doctrine at all |
| 4 | ENCH: magnitude + cost; cost tied to the enchantment's value-on-gear | `requiem-magic-patching` | reference gap — gear-value coupling never documented |
| 5 | Consumables "completely skipped; almost everything overhauled — study again" | `requiem-consumable-patching` + router | re-verify refs vs live AR/FaB; check router coverage + triggering (note: skill shipped 1.1.0; a stale live install may explain "skipped" — verify, don't assume) |

## Method

1. Live mining via houseCARL against the Authoria-dev instance (RftI inactive → hand-authored
   winners). Four parallel mining lanes: NPC doctrine, MR spells, MR MGEF/ENCH, consumables verify.
   Raw findings land in session scratchpad; distilled doctrine lands in skill `references/`.
2. Standards loaded (skill-creator invoked; §8 checklist governs). Body edits + reference
   rewrites; description edits only where triggering itself failed → §6.5 fan-out re-measure.
3. One worktree (`claude/field-report-round2`), one PR. Version bump 1.3.0 (behavior changes).

## Steps

- [x] Probe: `PlayerSkills` (base stats + skill offsets) readable → yes; civilian treatment
  (Requiem doesn't touch Nazeem); `REQ_NULL` MGEF scale (259).
- [x] Mining lane 1 — NPC (corpus: `dev/corpus-2026-07-17-field-report-round2/mining-npc.md`).
  Key: DNAM hand-set on every archetype even AutoCalc-ON; SkillOffsets used on 11 named actors;
  empty kits normal on source records; 7-plugin authority census; named-civilian stat-only lane.
- [x] Mining lane 2 — MR spells (`mining-mr-spells.md`). ManualCostCalc 827/833; ladders +
  delivery multipliers; rider shapes; 833-spell census (tier 0–5, hand pairs); the full
  resistance NULL→analogue map; tome value bands.
- [x] Mining lane 3 — MR MGEF/ENCH (`mining-mr-mgef-ench.md`). MinimumSkillLevel = tier marker;
  flags per archetype; conditions patterns A–D; ENCH 1:1 cost=amount=magnitude; **gear-value
  coupling corrected — enchant adds zero gold in Requiem** (report item 4's premise is vanilla
  autocalc, off in Requiem).
- [x] Mining lane 4 — consumables (`mining-consumables-verify.md`): 29/30 confirmed; 1 FormID bug
  fixed; 4 missed conventions folded in. "Completely skipped" is consistent with a stale live
  install (skill shipped 1.1.0; Heisen's install was pre-1.1.0 at last report) — verify install.
- [x] References rewritten/extended (2 new: `npc-authority.md`, `resistance-map.md`,
  `mgef-conditions.md` — three new); SKILL.md bodies + checklists updated; all under the 500 cap.
- [x] Router check: SPEL/MGEF/ENCH/ALCH/INGR rows present and correct — no router change needed.
- [x] No description changed → no §6.5 re-measure owed. CHANGELOG + plugin.json 1.3.0. Handoff. PR.

## Non-goals

- Leveled-list, race, weapon/armor/ammo skill bodies (beyond routing rows they already carry).
- The Reqtificator rulebook (`dev/reqtificator-rules.md`) — only touched if mining contradicts it.
- Fixing Heisen's live-install staleness (operational, not repo).
