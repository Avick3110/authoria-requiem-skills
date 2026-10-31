# Session handoff — split hunts 7–13, NPC family verification, bugs and questions merged

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (frozen at close). Project: `dev/rebuild/`. Supersedes
`SESSION_HANDOFF_2026-10-31_census-split-hunts.md` as the latest handoff.*

## Where we are

- **Stage:** still recon, but every open split hunt from the last handoff is now worked. Recon's census lane is
  ready for Aaron's review, then spec once Heisen has answered the questions.
- **Branch:** `claude/skills-rebuild` (worktree `.claude/worktrees/skills-rebuild`), pushed. No PR yet: open one
  at spec time.
- **How it ran:** overnight, Aaron away. Eight parallel subagents on one shared brief (the hypothesise-first
  method). The orchestrator sent back any doc whose residual leaned on "judgement" or "provenance" before
  8+ hypotheses had been logged against it. Four went back for a second round (ammo special rounds, Glenmoril
  Jillian lines, Vigilant tomes and boss Resist Damage, Respawns, gun gravity and the follower-bridge signal). Then
  one more agent merged the questions and the bugs.
- **Aaron's reminder this session:** the key to finding consensus is testing hypotheses. Every doc below carries
  its hypothesis log, dead ones included.

## Done this session

| Split | Doc (`recon/`) | Headline |
|---|---|---|
| 7 NPC D1 (template added) | `2026-10-31-split-npc-d1-templates.md` | 360 NPCs, not 345. The **source mod** decides (98.3%), not anything on the NPC; children are never templated; mod-NPC templates are themselves patched (49/49). 59 NPC- and mod-level hypotheses dead; 10 of the 13 templater mods are unexplained |
| 8 Ammo damage | `2026-10-31-split-ammo-damage.md` | Not a ratio. The final damage comes from a same-material Requiem comparable, and AD3B2D is always set (55/55), so nothing gets ×4 twice. Two table eras are live (WAR v Requiem.esp), chosen per patch. Gun rounds = max(10, source ×5). 37/55 fit; 18 special rounds residual after 30 hypotheses |
| 9 Weapon reach / speed | `2026-10-31-split-weapon-reach.md` | NonPlayable weapons keep their own reach (48/49). Playable weapons get the class reach ×0.7, keyed by animation (127/170). Reach and speed move together, and speed follows the type keyword. WAR has more classes than the census assumed |
| 10 House conventions A | `2026-10-31-split-house-conventions-a.md` | AD3B2B is load-bearing only on pieces the build would rescale (285/288) and habit elsewhere. Encounter-zone caps are a Gray Cowl one-off (NoResetZone domain reason). Visage = LOTD's Septim set onto Ebony / Orcish |
| 11 House conventions B | `2026-10-31-split-house-conventions-b.md` | No Bruma +1000 offset: Bruma re-tiered by name, which **corrects hunt 4** (106 of the 133 ×500 rows are Bruma). Glenmoril gun charge is a shot count at cost 100. Bruma dominance is measured across 14 habits: two consensuses reverse without Bruma |
| 12 MAGIC / RACE calls | `2026-10-31-split-magic-race-calls.md` | One resist source per creature, and the mod's own profile wins (363/404). Resist Damage is dropped under an Armor trait (105/109). MGEF tags go by archetype on player spells. Tomes sit on the MR ladder (Vigilant's flatter second ladder is a question). 3 census calls withdrawn |
| 13 LOCKS / CONSUMABLES / QUEST-MISC calls | `2026-10-31-split-locks-consumables-quest-calls.md` | 15 calls triaged, 10 hunted. Filler stripped from artifact chests; Xarrian lock-break on wooden chests; potions NoAutoCalc (the census premise was wrong); gravity on the material ladder; Respawns copies the modelled vanilla container; the follower-bridge faction signal is a lead |
| NPC stage-2 families | `2026-10-31-npc-stage2-family-verification.md` | Bruma copies a role-picked Requiem donor's whole stat block (467/541). Level is WRONG as stated; skills, offsets and ActorEffect are confirmed. The families are ≥98.8% Bruma, which was a live run of the old skills' doctrine, not an independent house rule |

**Merged:**
- `recon/questions-for-list-authors.md`: 21 → 50 questions, one section per split, deduped.
- `recon/suspected-bugs.md` (new, LIVING): 95 candidates, 15 CONFIRMED and 80 PLAUSIBLE, by domain. It also has
  an "Errors in our own docs" section (3 rows, including the hunt 4 correction).

**Tidy:**
- Removed the two stale #43 worktrees and five merged local branches. All were checked patch-equivalent to
  `main` with `git cherry`.
- Updated the status lines in `INDEX.md` and `recon.md`, and added rows 7–13 to recon's findings index.

## Next (Aaron)

1. **Review the eight docs.** The rule tables matter most for spec, and the hypothesis logs show how hard each
   one was pushed.
2. **Send the questions doc to Heisen** (50 questions). Record his answers inline.
3. **Suspected bugs:** re-verify live before filing in `arr-release/BUGS.md` (per the hub convention), starting
   with the 15 CONFIRMED. One row's plugin is `Blended Shrooms.esp` itself, not an Authoria patch; check who owns
   it first.
4. **D5 decision** (scored held-out validation) is still open. It's Aaron's call.
5. At spec time: normalise the labels and add a dominance field when merging the 13 `conventions.json` files.
   Split 11's Bruma-dominance table is the input for that.

## Notes

- The remote branch `origin/claude/control-eval-results` is merged into `main` (patch-equivalent) and can be
  deleted. It was left alone because a remote delete is outward-facing.
- **Pre-existing publish-hygiene leftovers** in old ARCHIVE docs on `main`: the `dev/handoffs/phase-*.md`,
  `dev/archive/1.x-build/STATE.md`, `dev/reference/reqtificator-rules.md`, the corpus and S4 lane files carry `<workspace>\…`,
  `E:\Skyrim Modding\…` and a `C:\Users\Heisen\…` path. These are already public. Scrubbing them would mean
  editing ARCHIVE docs, so it's Aaron's call.
- `dev/archive/BACKLOG-1.x.md` open items are mostly pre-rebuild skill fixes. Prune them or fold them into the spec, rather
  than fixing them in 1.10.x.
- Epoch was `e2-58972ca8618e79ea` throughout (unchanged from the census). `ksws03_quest.esp` is still excluded by
  houseCARL (a parse failure). The eval profile's RFTI plugin is unticked, so the agents read it off-order.
- Live install still absent (parked).
- Scripts and spills are local in `arr-requiem-eval/census/splits/`, under the prefixes `npcd1_`, `ammo_`, `reach_`,
  `hc10_`, `hc11_`, `mr_`, `race_`, `lcq_` and `npcv_`. Split 12 also edited the shared `race_lib.py` so it skips
  rows that have no fields. The harness changes are not committed.
