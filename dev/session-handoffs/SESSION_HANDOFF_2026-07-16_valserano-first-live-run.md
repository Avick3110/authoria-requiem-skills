# Session handoff — 2026-07-16: first full live run of the pack (Val Serano, 11-lane patch)

*Author: Aaron (+ Claude session) · 2026-07-16*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-16_vmad-sweep-one-call-1.2.3.md` as the latest.*

## What this session did

**First end-to-end live test of the shipped pack (v1.2.3, all 11 skills)** — patched *Val Serano -
Pirate Follower and Quest Adventure* (13 active plugins: `AX ValSerano.esp` + 12 compat patches,
BSA-packed) for Requiem on the live ARR 2.0 instance. Orchestrated exactly per `requiem-patching`:
freshness probe (winner = RftI, chain OK) → full enumeration → parallel domain lanes (one subagent
per lane, each loading its own skill) → gap sweep → integration gate. **Result: release-gate PASS;
patch shipped.**

- **Deliverable:** `AX ValSerano - Requiem Patch.esp` (+ compiled bridge `.pex`/`.psc` + `.seq`) in
  MO2 mod folder `houseCARL - AX ValSerano - Requiem Patch`. Handed to the user with enable/sort +
  run-the-Reqtificator instructions. No mod file touched; BSA never unpacked (houseCARL read
  scripts in-archive — the user's repack contingency was unnecessary by design).
- **Coverage math closed:** 16,190 records (73 types, main) + 739 (24 types, patches) — every type
  reconciled per-record or wholesale; zero unaccounted at the gate.

## How the patch went (per lane)

| Lane (skill) | Outcome |
|---|---|
| leveled-list | 0 writes needed — lists already flat L1, 26 containers all static authorial loot, ECZNs fine (Reqtificator opens boundaries). Found the orphaned `AX_MolagBalCaveZone` → cell wire done at integration. |
| script | 2,606 `.pex` surveyed (≈125 hand-authored), 2,470 VMAD carriers all PRESERVE; no Requiem-hostile patterns. **Follower registration = standalone bridge quest** (own quest + alias + event-driven script compiled against `REQ_FollowerControl` + `.seq`) — the shared Authoria bridge uses fixed named properties, so "add an alias" is a no-op (→ issue [#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15)). |
| race | 14/14 — Mihail creatures; several had already-Reqtificated identical-creature analogues in-list (ideal live comparables). 13 patched w/ REQ trait spells+keywords, Layer-B perk list handed to npc lane (new-race exception: Reqtificator conf can't see modded races). |
| consumables | 14/14 rebuilt — mod shipped food on **deprecated `REQ_LEGACY_*` + one `REQ_NULL` MGEF**; alcohol tiered per the live Alcohol Tweaks winner; INGRs set NoAutoCalc, one illegal non-alchemy effect remapped. Coffee = stimulant analogy (no comparable; flagged). |
| weapons+ammo | 9+1+1 PROJ + 9 COBJs — crossbow/bolt onto Requiem heavy-crossbow ladder (22→72 / 14→84, AP tier, RFTI exclusions). Every tempering COBJ had a broken `EPTemperingItemIsEnchanted`-OR perk-gate bypass — rebuilt with material gates. |
| armor | 140 ARMO (78 patched / 62 skip) + full 158-ARMA assignment table (0 race-skin — bodies route via skin ARMOs; 4 orphans flagged). Root finding: **author tagged real `REQ_ArmorSet_*` keywords but left vanilla AR** — Reqtificator caps, doesn't rescale, so re-tiered from live comparables. Fist perks stripped from light gloves; double set-keyword fixed. |
| npc+perk | 92 NPCs (45 patched / 12 skip-verified / 35 skip) — systemic gap = **PcLevelMult everywhere**; de-leveled to fixed levels (crew L15–20 + REQ bandit-ladder perk sets by archetype, bosses L35–50), Layer-B `RFTI_Trait_*` hand-added for 7 modded-race combatants (sanctioned), Val got follower factions (record-side prereq for the bridge). 9 PERKs dispositioned (5 plumbing, 4 user questions). |
| magic | 297 records reconciled — author built on Magic Redone (HalfCostPerks all correct); work was economy: 11 tomes repriced to tier ladder (one tome cost 0), 8 spell costs onto bands, 3 missing `ManualCostCalc` (one cost silently auto-recalced), 2 resistance-bypass MGEFs fixed (shock in-lane, frost at gate), Fortify CarryWeight **1000→75** on a ring. The mod's SHOU = degenerate creature-breath frame, correctly left (3-word model N/A). |
| gap sweep | 0 writes — no vampirism/disease/standing-stone gaps, **zero GetLevel quest gates** (quest layer already de-level-consistent), 780 globals all scene-state, no formlist injection, vendor = legitimate fence archetype. |
| integration | PASS after fixing **7 forwarded `REQ_NULL`s the domain lanes missed** (NPC ActorEffect/Perks forwards: `REQ_NULL_DLC2abForitifySneak` ×3, `REQ_NULL_AgileDefender80` on Val, `REQ_NULL_Silence` ×2, `REQ_NULL_DLC2crLurkerSpit01`). Masters 10/sorted/0 dangling; Reqtificator matrix clean (no hand-stamped outputs; Layer-B placements match race ledger exactly); exclusion kit verified. |

## Findings (skill-pack lessons from the run)

1. **F-VS1 — doctrine bug, filed:** `requiem-script-patching/references/follower-registration.md`
   "adding an alias is enough" is false for the shipped named-property bridge → **issue
   [#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15)** (doc-drift; the lane
   caught it by decompiling before wiring and built the standalone-bridge pattern that should
   become the reference's primary prescription).
2. **F-VS2 — REQ_NULL forwards survive domain bulk passes:** lanes correctly avoided *authoring*
   NULLs but **forwarded** 7 that rode in on NPC ActorEffect/Perks lists; only the integration
   gate's whole-patch scan caught them. The NPC skill's bulk pass (and any lane that forwards
   list fields) should carry an explicit "scan forwarded lists for `REQ_NULL_*`" step → BACKLOG.
3. **F-VS3 — router enumeration counts drift on patch plugins:** `group_by=type` rows for the 12
   compat patches counted overrides the patch *wins* as that plugin's rows, so the router briefed
   lanes with over-counts (PERK "10" vs 9 real; FormList "61" vs 59; CONT "29" vs 26 distinct).
   Harmless (lanes reconciled against their own enumeration) but the router text could warn that
   patch-plugin counts ≠ distinct-record counts → BACKLOG.
4. **F-VS4 — the pack's core value showed:** the mod was Requiem-*aware* (real REQ keywords,
   Magic Redone perks) but shipped vanilla numbers, PcLevelMult, deprecated MGEFs, and broken
   recipe gates — exactly the live-analogy gap the pack exists to close. Every lane derived from
   live winners; no invented numbers observed at the gate.
5. **F-VS5 — orchestration pattern validated:** parallel lane subagents (each `Skill`-loading its
   domain skill) + one shared pre-created output ESP + router-mediated handoffs (racial-SPEL
   exclusion list, PROJ split, ARMA table, scythe-nerf → boss-compensation) worked with zero
   write collisions. One lane stopped mid-report ("waiting for a sweep" that couldn't resume it)
   and needed a router nudge to finalize — subagent lanes should be told their report is due at
   stop, not after background children.

## Open items handed to the user (design calls, patch plays safe as-is)

Coldharbor/Xivkyn true-Daedric values; Val's light-Daedric set (top-light ladder, no ranged-resist
tier) vs re-tag; glass-tagged dresses; 4 player perks (Drowned Revelations +10% learning, Shadow
Veil near-invis, Abyssal Tide grants Aqua.esl spells = out of scope, Shadow Scythe); crew stays
non-recruitable; Crow Wraith unresistable raven attacks (boss design?); coffee analogy;
quest-only gear (no world distribution); SummonedWatcher Layer-B (its Aqua.esl race already
carries REQ traits on this instance — no action taken).

## State at close

- Patch shipped to the user's instance; **not yet enabled/sorted in MO2, Reqtificator not yet
  re-run** (user's steps).
- Issue #15 filed. This handoff + two BACKLOG entries ride PR from
  `claude/valserano-first-live-run`; `main` untouched. No pack edits made this session — no
  version bump, no live-install sync owed.

## Next

1. Merge this PR (rebase on latest `main` first per the collision rule), delete the branch.
2. Fix #15 (rewrite `follower-registration.md` around the standalone-bridge pattern; body-only
   unless the description changes — then §6.5 re-measure).
3. Take the two BACKLOG entries (F-VS2 NULL-forward scan in domain bulk passes; F-VS3 router
   count caveat) when convenient.
4. Standing items unchanged: Gray Cowl regeneration with the current pack; root README
   authority-model rewrite (needs Aaron); perk-assignment Q8 description tweak; original-nine
   empirical re-validation.
