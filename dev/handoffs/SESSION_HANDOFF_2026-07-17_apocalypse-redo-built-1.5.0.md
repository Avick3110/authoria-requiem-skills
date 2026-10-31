# Session handoff — 2026-07-17: Apocalypse re-do BUILT (745-record patch, pending Reqtificator)

*Author: Aaron (+ Claude session) · 2026-07-17*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-17_closeout-1.5.0-next-apocalypse.md` as the latest "where are we."*

## Outcome

**`Authoria - Patch - Apocalypse Requiem.esp` is built and verified: 745 records
(Spell 266 · Book 175 · Scroll 138 · Weapon 93 · ObjectEffect 71 · Ammunition 1 · Npc 1).**
Masters: `Skyrim.esm, Update.esm, Praedy's StavesAIOLotd.esp, Requiem.esp, Requiem - Magic Redone.esp,
Apocalypse - Magic of Skyrim.esp` — esm-first, load-order-sorted, **no tool-output master**.
`check_errors` clean (0 dangling / 0 missing masters). Mod folder
`houseCARL - Authoria - Patch - Apocalypse Requiem`, **still DISABLED in MO2**.

**Remaining user steps:** enable the mod → re-sort (also clears the stale
`Authoria - Reqtificated - Apocalypse.esp` loadorder.txt warning) → **run the Reqtificator** →
in-game spot test (a T5 spell's cost, a tome price at a vendor, a summon fight).

## Method (the 1.5.0 stress test — it passed, with findings)

Whole-mod router job per `requiem-patching`, executed as a **two-wave read-only agent fan-out
(15 lanes + 1 writer, all Opus)**: wave 1 = 5 magic schools split by `WB_<Sch>_` EditorID prefix +
weapons/armor/NPC-race/misc; wave 2 = Destruction redo, magnitude balance, books+gates,
scrolls+ench, leftover reconciliation, script survey; then one serialized writer applying lane
JSONs (`bulk_apply`, flags-union with fresh winner re-reads, per-lane count verification). Lane
plans + WRITER-REPORT preserved in the session scratchpad (`apocalypse-lanes/*.json`).

**Coverage reconciliation closed**: every magic-plane type sums exactly
(claimed + leftover = defined for SPEL 373 / MGEF 556 / EXPL 87 / PROJ 149 / HAZD 13 / DUAL 7);
all other types dispositioned per-record or per-type-with-exceptions; brushed-override deltas
(e.g. Keyword 149 defined vs 176 touched) accounted. A server-side interruption hit after the
writer finished; damage triage found **zero loss** (ESP, JSONs, report all intact and re-verified).

## The headline findings

1. **The mod is vanilla (masters prove it), but THREE independent lane agents read it as "already
   Requiem-integrated" — vanilla-FormID aliasing.** Spell mods point `HalfCostPerk` at vanilla tier
   perks that Requiem overrides in place, and Enai's 0/25/75/100 skill-level convention coincides
   with MR's tier markers. **`ManualCostCalc` on SPEL Flags is the real is-it-patched test** (absent
   on all 373 spells). The aliasing also cuts benignly: riders and Respite gates auto-inherit
   Requiem's values through the same FormID reuse. → issue drafts (see below).
2. **The patch is one dominant fix, not 3,945 fixes.** ~170 castables: `ManualCostCalc` union +
   `BaseCost`/`ChargeTime` from live MR ladders. Magnitudes: audited ~160 live, **zero corrections
   warranted** (unique-mechanic pack; in-band where comparable; balance lever = cost — maintainer's
   "balance them" directive satisfied by derivation). Tomes/scrolls/ench: full repricing
   (scroll Value == staff per-cast EnchantmentCost, one live ladder anchors both).
3. **Verify-before-fix kept paying**: the "parallel tome economy" seam dissolved (Apocalypse's
   populate quest feeds Requiem's own gated tier-100/ritual lists → Master tomes already
   College-gated, `gate_edits:[]`); the Reqtificator natively integrates all 42 actors (near-empty
   NPC patch is correct); Requiem's own summon worn gear is AR-0 cloth → leave Apocalypse's as
   authored; script survey proved **zero** runtime cost/magnitude reads → economy patch can't break
   mechanics.

## Maintainer decisions taken (Aaron, this session)

Align tome gates (→ no-op by derivation) · balance magnitudes (→ cost lever, in-band retained) ·
summon gear per live Requiem convention (→ leave) · Kyrgar stat pass (de-level to 40 +
`REQ_NULL_crMagickaRecovery02` strip; **kit kept** — matches Requiem's combat-mage vendor pattern) ·
leave bag-summon PcLevelMult, self-cast staff LVLI, Daedric Crescent enchant · uniform tome ladder
incl. 8 Illusion tomes · Destruction T5 mappings as derived · tier-perk corrections shipped
(SpellTwine→Adept, MysticWind→Apprentice, DremoraMentor→Expert).

## Open / next

- **7 field-report issue drafts** ready to file (blocked on maintainer go for public posting):
  2× magic (aliasing/ManualCostCalc test + T5 charge example; `_NPC` cost convention), armor
  (conjured-ally worn-gear clause), weapons (**Reqtificator skips NonPlayable weapons — final-tier
  values required**, doctrine gap), npc (PcLevelMult carried by RftI winner = respect, don't
  de-level), leveled-list (self-cast utility list exception), **router (load
  `housecarl:tool-output-awareness` in First step + one reconciliation sentence in
  `scope-and-authority.md`** — doctrine change, request Heisen review). Drafts:
  session scratchpad `apocalypse-lanes/ISSUE-DRAFTS.md`.
- Reqtificator run + in-game verification (above) — then consider the old community patch
  (`Requiem - Apocalypse Compatibility Patch.esp`, disabled) permanently retired.
- houseCARL: zero tool bugs across ~400 calls / 16 agents; two param-name ergonomics notes
  (`formid=` vs `form_id`, `plugins=` array binding) — not worth filing.
