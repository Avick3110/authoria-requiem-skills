# D — Census stage 1 report (NPC_, ARMO, WEAP, COBJ)

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, group D. Method: `D-census-method.md`, with the mechanisation and lessons in
`2026-10-30-D-census-pilot-report.md`. Per-type docs: `census/stage1/NPC_.md`, `ARMO.md`, `WEAP.md`, `COBJ.md`. Raw
data, scripts, briefs and agent outputs are local in the eval harness (`arr-requiem-eval/census/`).*

## Scope and pipeline

- **Corpus:** 197 of the 250 ARR patches that carry these four types. That is 17,222 records, about 71% of the corpus.
  The 6 eval references are held out from rule-making.
- **Extraction:** 8 Haiku agents ran houseCARL dumps.
  - A script check confirmed 197 / 197 plugins complete, one load-order snapshot.
  - The structural diff gives **350,883 change rows / 6,481 patterns**.
  - It matches houseCARL's own delta on every record. That took two fixes: empty list v absent, and list-root notes.
- **Mechanical classification (scripts, no LLM) decided 59.6% of rows.** The rules come from code, the
  Reqtificator's own rule configs (parsed: `ActorAssignmentRules_*`, `ArmorKeywordAssignments`,
  `WeaponKeywordAssignments`), engine template-flag semantics, and Requiem.esp comparables. They cover:
  - baked perk / spell kits (156k rows);
  - masked edits;
  - presentation fields;
  - build-assigned and recognised keywords;
  - comparable values;
  - recipes disabled with no replacement;
  - order-only re-saves.
- **Template fit:** a script resolves every inheritance chain (about 2,700 records in 6 closure rounds) and decides
  208 / 571 NPCs.
- **LLM residual:**
  - Families: 9 Sonnet family readers judged about 120 change families (3,203 patterns, families of the same field
    and op), each from pre-resolved facts.
  - Template fit: 2 Sonnet readers judged 363 NPCs.
  - Verification: Sonnet verifiers checked every unit, and their corrections were merged by script.
- **Synthesis:** 4 Opus docs, each checked by a Sonnet verifier and corrected.

## Verifiers keep paying for themselves

| Stage | Material error rate found | Typical cause |
|---|---|---|
| Family readers | about 25–50% of families per unit (Npc-0: 10/13; Npc-1: 4/8; Weapon-0 + Npc-2: 6/20; Armor-0: 2/7 re-derived; COBJ: 3/9; tails: 6/25) | Calling hand-set NPC stats "baked" with no code proof (refuted from the C#); calling things "house convention" without checking Requiem's own records; not recognising the weapon bake |
| Template fit | LLM non-fit 17/20 held; **script non-fit only 2/6** (`fits` 9/9) | The script misreads magic-bandit and civilian templates as combat pools. Its misfits are leads only |
| Synthesis docs | 1–4 material per doc (ARMO 2, WEAP 2, COBJ 1, NPC 4) | Held-out miscounts; reach snapping mislabelled as baked; "every NPC gets AE3B83" (really 59%) |

**Lesson:** a family reader reading pre-resolved data, with few live reads, overclaims. The verifier's live reads and
code reads are where the truth gets settled. For stage 2, either give readers a little more read budget for
baked / Requiem-aligned claims, or keep the verifier as the authority (as now). Do not drop it.

## Headline conventions (detail and evidence in the four docs)

- **NPCs — two workflows plus a hybrid.**
  - **(B) Stat-set:** add AlreadyReqtified `AE3B83` (3,221 NPCs / 93 plugins), so the build skips the NPC. Then
    hand-carry the build's rule-assigned perks and spells (the only proven bake, by config), rebuild the source's perk
    kit, give it a fixed level, drop AutoCalc and hand-set the stat block. All of that except the rule kit is
    **input**.
  - **(A) Templated:** Template + Stats,SpellList onto a Requiem or vanilla-civilian template. Stats-only and
    SpellList-only also occur, as in Requiem.
  - **Hybrid:** 976 templated NPCs also carry AE3B83 + the kit.
  - **Suspected bugs:**
    - 26 Grand Solitude civilians on Requiem's L3 bandit template;
    - Tools of Kagrenac Ayleid skeletons collapsed to L13;
    - Dac0da melee worms on a necromancer list.
- **Armour.**
  - AR is hand-tiered to the Requiem comparable (input).
  - AR 0 on accessories and extra-slot pieces: a house convention across 44 plugins. Fact 8's "one patch" is wrong.
  - NoArmorRatingRescale goes on everything (83 plugins), though it only matters on AR-0 pieces.
  - Build keywords are written in (a harmless bake).
- **Weapons — a whole downstream bake.**
  - Damage is set to the comparable ×6 (bows ×4), and crit to equal damage.
  - Reach is the class value ×0.7. The ×0.7 is baked; the class value is input.
  - The three No*Rescale keywords are added so the build skips the weapon.
  - The held-out patches follow it.
- **Recipes.**
  - Requiem's temper gate: one HasPerk + one material.
  - Two ways to retire a recipe: Requiem's `AD3B01` workbench, and ARR's own `GetLevel() == 0` (about 680 real
    disables).
  - Some items end up with no live craft recipe. These are suspected bugs.

## For the skills rebuild

- **Upstream v downstream is concrete now.** The weapon bake, the NPC rule kit and the armour build keywords are the
  downstream package. Everything else is input. Each doc has an upstream checklist and a bake package.
- **Many "Aaron calls" are now evidence-backed questions.** Examples:
  - AR 0 on accessories;
  - NoArmorRatingRescale everywhere;
  - the GetLevel disable;
  - weapon value tiers;
  - the NPC workflow split.

  These go to the spec interview.
- **Suspected bugs** (each doc has a table) are for Aaron's review. They are not filed in BUGS.md.

## Usage (subagent tokens, as reported)

| Stage | Model | Tokens |
|---|---|---|
| Extraction (8 agents) | Haiku | ~1.44M |
| Family + fit readers (11) | Sonnet | ~1.26M |
| Unit verifiers (7) | Sonnet | ~0.95M |
| Synthesis (4, incl. fixes) | Opus | ~0.65M |
| Doc verifiers (4) | Sonnet | ~0.54M |
| **Total** | | **~4.8M** (30% Haiku, 57% Sonnet, 13% Opus) + main-session orchestration |

That is well under the 5–25M estimate, for 71% of the corpus's records. The scripts did the heavy lifting.

## Gaps for stage 2

- **Corpus misses.** Two ARR patches don't follow the naming pattern: `Authoria - Reqticated - Lions Mane.esp` (typo)
  and `Authoria - Master Patch - Bruma.esp`. Re-scan by content, not name.
- **`ksws03_quest.esp` fails to load** in houseCARL, so the ksws03 patch's records look like new records.
- **Untouched records** (what ARR leaves alone) are still not listed. This is the pilot's proposed Phase 0 addition.
- Family variants are capped at 40 per family in the reader units. The verifiers covered some of the omitted ones,
  not all.
- **Stage 2 types:** the long tail — CONT / DOOR (locks), magic, LVLI, RACE, BOOK, PERK, cells. Their comparable rules
  are not written yet.
