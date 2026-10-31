# Split hunt 1: the NPC workflow (templated v stat-set v hybrid)

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`../census/stage1/NPC_.md`) left this split open
("no evidence for which NPC gets which workflow"). This round tested hypotheses first and resolved it. Scripts and
the per-NPC feature table stay local in the eval harness (`census/splits/npc_*.py`, `npc.jsonl`).*

## Answer

- **The marker rule is mechanical, not a judgement call.** Inside the patches that bake, `REQ_KW_AlreadyReqtified`
  (`AE3B83`) goes on an NPC **exactly when the NPC does not inherit its SpellList from a template**. That is,
  exactly when the Reqtificator would otherwise stamp its rule kit on it.
  - Measured: 5,137 / 5,163 NPCs (**99.5%**) across 94 baking plugins.
- **Workflows A, B and C are one rule applied to different source states,** not three choices:

  | Source NPC (before ARR) | ARR's patch | Count |
  |---|---|---|
  | No inheriting template | marker + the hand-carried build kit (**B**) | 2,971 |
  | Template inheriting SpellList | kept as is, no marker (**A**) | 2,315 |
  | Template inheriting Stats but **not** SpellList | marker + kit (**C**, "the hybrid") | 220 |
  | No template; ARR **adds** one | template inherits SpellList, so no marker (**A**) | 345 (see open question) |

  The hybrid (C) is predicted by "template flag Stats without SpellList" at 96.8% v 0.3%.
- **The stance split (bake v upstream) has no domain reason in the records.** It tracks how the patch was made.
  - 11 plugin-level properties were tested; every median overlaps between the two groups.
  - The upstream plugins with a real choice are Bruma, Gray Cowl of Nocturnal, Apocalypse and Val Serano (plus
    Moonpath, Tools of Kagrenac, TGV Kynesgrove). The first four are the **authoria-requiem skills' own live runs**
    of 2026-07-15 to 07-18 (this repo's session handoffs). The skills taught "never hand-stamp", which is upstream.
  - Every other NPC patch bakes. **ARR's hand-made convention is bake;** upstream is the old skills' doctrine.

## Mechanism (Reqtificator C#, the ARR build's source)

- `ActorPerksFromRules` / `ActorSpellsFromRules` return early when `TemplateFlags` has `SpellList`. They test **the
  flag only**, not whether the template link resolves.
- Otherwise the build adds each rule perk **only if it is not already present**.
  - So a hand-carried kit without the marker never doubles up.
  - The marker's real effect is to skip the whole actor pipeline (`ActorAlreadyReqtifiedSkip` runs first).

## Exceptions (26 of 5,163)

| Group | n | What | Read |
|---|---|---|---|
| Own SpellList, kit carried, no marker | 21 | Glenmoril Solstheim-expansion bosses / encounters (14, 47–56 perks), Ashe 2, Witcher Horses 2, Nimwraith 1, Become a Bard 1, The Cursed Tribe 1 | Inconsistent but harmless: the build adds nothing they already carry. A missed marker, most likely |
| Inherited SpellList, marker anyway | 5 | Mihail Giant Family 3, Kaidan pre-Thalmor 1, Nimwraith (Sithis) 1 | Redundant marker, harmless |
| Template flags set on a null template link | 5 (Nimwraith 4, Aqua 1) | Marker present; the flag alone already makes the build skip | Consistent with the build's flag-only test |

## Hypothesis log

**Decision D2** (templated source: keep, or add marker and kit):

| Hypothesis | Result |
|---|---|
| Template inherits Stats but not SpellList → marker | **Kept**: 96.8% v 0.3%, holds within every mixed plugin (4/4) |
| Summonable, IsGhost, Unique, has scripts, has name, source not Respawn | Correlates (gaps 22–54 points), but explained by the rule above: these NPCs tend to have Stats-only templates |
| Vanilla race, face present, factions, items, packages, PCLevelMult | Dead (gaps < 12 points or inconsistent within plugins) |

**Decision D1** (untemplated source: stat-set, or add a template):

| Hypothesis | Result |
|---|---|
| PCLevelMult → add template | Weak: 43% v 8.6%, 4/6 within plugins |
| Has packages / AutoCalc / 3+ factions / Essential → template | Weak (gaps 15–19 points, within-plugin mixed) |
| Unique, face, name, voice, race, scripts, source perks | Dead (gaps < 8 points) |

**Stance** (per plugin): vanilla-race share, face share, unique share, summonable share, source AutoCalc share,
source perks, final AutoCalc, scripts share, NPC count, final and source perk medians. **All dead.** Provenance (the
skills-era live runs) explains the four big upstream plugins.

## Open

1. **D1, "ARR adds a template" (345 NPCs).**
   - 174 of them are two plugins: Grand Solitude (65) and the held-out Animated Carriages (109). The targets are
     mostly vanilla templates, incl. the census's suspected-bug bandit template `039CFD` (25).
   - No record property tested explains it. The next hypotheses need data the census didn't extract: placement
     (cell, faction, leveled-list membership), whether the NPC is a reused generic, and whether the source's own
     stats were broken.
   - It may be two plugins' habit, part of it buggy.
2. **The spec decision this hands Aaron:** the rebuilt skills should follow ARR's hand-made convention (bake, by the
   rule above) or keep the old skills' upstream stance. The evidence says ARR bakes. The upstream patches are the
   skills' own output, not an independent convention.
3. Small leads: the 21 missing markers (harmless, list hygiene), and the TGV Kynesgrove kit without a marker.
