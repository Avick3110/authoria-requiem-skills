# Session handoff — recon pass 2 and the handmade-patch census

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (frozen at close). Project: `dev/plans/2026-10-30-requiem-skills-rebuild/`. Supersedes
`SESSION_HANDOFF_2026-10-30_skills-rebuild-intent.md` as the latest handoff.*

## Where we are

- **Stage: recon pass 2 is complete enough for the spec. Recon is not yet formally closed:** Aaron agrees that gate.
- Everything is on branch `claude/skills-rebuild`, worktree `.claude/worktrees/skills-rebuild`. **Uncommitted and
  unpushed.**
- **Approver:** Aaron alone.

## What pass 2 produced (all in `dev/plans/2026-10-30-requiem-skills-rebuild/recon/`)

- **A7:** install path and plugin hooks. A gate hook would load on today's install, and it must fail closed. This is
  from the docs and untested.
- **A6:** root-causing the tool traps T1–T5. **Not run:** it was superseded by the census, which found T1 (PRKZ=0)
  skill-induced. Re-scope or drop it at spec time.
- **B5:** ARR's patch domains. Locks are the unowned big domain.
- **B6:** the house conventions the armour and quest sessions won't settle.
- **D, the census of ARR's handmade Requiem patches.** This is the main finding.
  - `D-census-method.md`.
  - Reports:
    - `2026-10-30-D-census-pilot-report.md` (pilot + mechanisation);
    - `2026-10-30-D-census-stage1-report.md` (NPC_, ARMO, WEAP, COBJ);
    - `2026-10-30-D-census-stage2-report.md` (the long tail).
  - Per-domain docs, **each with a `conventions.json`**:
    - `census/stage1/`: NPC_, ARMO, WEAP, COBJ;
    - `census/stage2/`: LOCKS, LISTS, MAGIC, RACE, PERK-AI, CONSUMABLES, QUEST-MISC, GEAR-NPC-S2, UNTOUCHED.
  - Every doc was checked by a fresh verifier and corrected. Every family-level reader annotation was verified too.
- **Group A** (Anthropic's skill guidance A1–A5, standards drift) **was not run** this session: the census displaced
  it. It is still open, cheap, and needed before the spec (intent C2 / C3).
- **Group B** (Aaron's armour and quest patching sessions) is still pending. B4 is now largely answered by the census.

## The census in one paragraph

ARR's handmade patches follow Requiem's own practice far more often than first readers thought. Many apparent house
conventions are Requiem's own: Level-1 list flattening, the REFR lock levels, the disable idiom, the scroll and tome
ladders. The commonest reader error was not checking Magic Redone and Requiem's overrides of vanilla records.

The **stance is chosen per patch**:
- **downstream (bake):** AlreadyReqtified with the build's rule kit by hand; post-rescale weapon / ammo values with
  the No*Rescale keywords;
- **upstream:** a skill-perk kit by hand plus the build's kit; source-scale values for the build to rescale.

The 14 stage-2 patches are upstream; the stage-1 corpus mostly bakes. All verified facts are in the harness's
`census/FACTS.md`, sections A–D. It is local, but its content is mirrored in the docs.

## Read first (next session)

1. `INDEX.md`, `intent.md`, `decisions.md`, `recon.md` (findings index).
2. The three census reports, then the `conventions.json` files (machine-readable, to merge for the spec).
3. Each doc's **Open calls for Aaron** and **Suspected bugs** sections.

## Next steps

1. **Close recon.** Aaron decides whether to run group A first (recommended: it is cheap and needed for C2 / C3), and
   whether group B's sessions still matter before the spec.
2. **Spec stage** (`new-project` skill), in a fresh session.
   - **Interview Aaron on the hard parts.** These come from the docs' open calls:
     - the stance default and the rule for choosing it;
     - which house conventions to encode (AR 0 on accessories, NoArmorRatingRescale, the GetLevel disable, loot
       stripping, the encounter-zone caps, the Bruma master-patch habits);
     - the skill shape, now that locks, lists, magic and races are evidenced;
     - the eval re-run: hold-out set, plus Aaron's sessions as new tasks.
   - Then write `spec.md`.
3. **Suspected bugs** (about 50 across the docs) go to Aaron's review. **BUGS.md filing stays a separate step,** as
   decided.

## Don't redo / don't be misled

- **The census scripts and raw data live in the harness** (`arr-requiem-eval/census/`: `scripts/`, `stage*/raw`,
  `work`, `STATE.md`). Re-use them; don't re-extract.
- **A "baked" label needs proof:** a code line, a config entry or the RFTI output. A post-rescale value without the
  matching skip keyword is not baked.
- **"Requiem's practice" means the live winner,** including Magic Redone and Requiem - Weapons and Armor Redone. Not
  Requiem.esp alone.
- **`ksws03_quest.esp` fails to load in houseCARL,** so that patch is unreadable.
- **Untested in game:** variant v template AR, and the follower-bridge script (never decompiled).
