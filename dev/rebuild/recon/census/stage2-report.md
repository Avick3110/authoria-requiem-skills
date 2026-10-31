# D — Census stage 2 report (the long tail, plus the patches stage 1 missed)

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, group D. Method and stage 1:
- `D-census-method.md`;
- `2026-10-30-D-census-pilot-report.md`;
- `2026-10-30-D-census-stage1-report.md`.

Stage-2 docs are in `stage2/`: LOCKS, LISTS, MAGIC, RACE, PERK-AI, CONSUMABLES, QUEST-MISC, GEAR-NPC-S2, UNTOUCHED.
Each comes with a `.conventions.json`, and stage 1's four have one too. Raw data and agent outputs stay local in the
eval harness (`arr-requiem-eval/census/`).*

## Scope

- **Corpus:** 183 plugins: 169 original patches plus 14 found by content.
  - The 14 found by content: 9 `Reqtificated` patches in other mod folders (including the misspelt `Reqticated -
    Lions Mane`) and 5 Requiem-relevant master patches (Bruma, Food and Drink, Races Merge, Dragon Tweaks, Follower
    Registration).
  - **Excluded:** the other master patches and the 116 `Authoria - Patch` plugins. They are merges and compatibility
    patches, and would mix mod-to-mod forwarding into the conventions.
- **Types:** the other 40 types, and all types for the 14 new plugins.
- **Extraction:** 163,243 change rows, all from one snapshot.
  - The diff matches houseCARL's delta, after two fixes for empty-list v absent.
  - About 40 records keep a minor Conditions mismatch.
- **Classified by script: 60% of rows.** That required two new rules:
  - cell geometry and child lists are noise;
  - door locks.
- **Untouched records:** 321 source mods were dumped. There are 316 partial cases, and an agent sorted misses from
  legitimate gaps (`UNTOUCHED.md`).

## Process quality

- **Readers:** 13 Sonnet family readers, each with a 20-read budget, then a verifier.
  - The verifiers still materially corrected about 25–50% of reader classes.
  - **The recurring error:** calling something an ARR house convention when Requiem already does it. Requiem's magic
    lives largely in Magic Redone on Skyrim.esm records. Its leveled-list flattening lives in its overrides of
    vanilla lists, not in the lists it defines.
  - The verifiers caught this every time by reading the live winner. That lesson is in the fact pack.
- **Synthesis:** 8 domain docs (5 Opus, 3 Sonnet), each checked by a doc verifier.
  - Material errors: 0 to 5 per doc. LOCKS and GEAR had none; QUEST-MISC had 4–5.
  - **Typical causes:** spread miscounts, a non-existent bug row, and wrong ratios (ammo ×4 is really mostly ×4–5;
    NPC spell damage is ×2–6, not ×2).
  - All corrected.
- **One process slip:** two agents shared a scratch script, and one re-ran another's JSON generator. The affected file
  was rebuilt and checked.

## Headline findings

- **Stance is per patch, not per list.** The 14 stage-2 patches take the upstream stance:
  - none of their 1,052 NPCs carries AlreadyReqtified;
  - a hand skill-perk kit plus the build's rule kit (complementary, never doubled; the RFTI output shows 9 → 54
    perks);
  - weapons at source scale for the build to ×6.

  The stage-1 corpus mostly bakes (59% AlreadyReqtified; the weapon bake on about 1,650 weapons). The rebuilt skill
  must teach both, plus how to choose.
- **Locks:**
  - The lockpick script is on 1,996 of 2,026 overridden containers and doors. It is redundant, because the build
    stamps it anyway.
  - Lock levels follow Requiem's own REFR overrides (29 / 29).
  - Disabling a ref (Z −30000 + EnableParent player, opposite) is Requiem's idiom.
  - New: Requiem's Xarrian lock-break script, which Container Merge drops on one container.
- **Lists:**
  - Level-1 flattening is Requiem's own rule (0 of 11,131 added entries above Level 1).
  - Nulled items are swapped for Requiem pieces.
  - Death items lose vanilla loot sublists (about 260 lists).
  - About 29 live spell tomes were culled from loot.
- **Magic:** all of it is input.
  - Abilities: REQ_NULL abilities become REQ_AbHide at the same magnitude.
  - Spells: costs follow Magic Redone's table.
  - NPC spells are rescaled to Requiem tiers.
  - Enchantments: Amount == Cost + NoAutoCalc (302 / 302).
  - Resist Damage is dropped from about 199 creature abilities. That is consistent with Requiem (it is a REQ_NULL
    effect).
- **Races:** all input, because the build's race patcher never reaches mod races.
  - Creatures get REQ_Trait_Armor (+ Healing).
  - Humanoids get the vanilla race's full trait set.
  - Requiem keywords and stats are added.
- **Consumables:** ammo is a bake (hand-set tier damage with NoDamageRescale). Ingredients and potions follow
  Requiem's alchemy shape only for restore effects.
- **Untouched:** the ARR-side miss class is template-linked enchanted armour variants left at vanilla AR (about 27 in
  Glenmoril, Vigilant, Bruma, Dac0da and Unslaad). Requiem sets a variant's AR to its template's, and the build skips
  template armour. Whether the engine reads the variant's AR or the template's is untested.

## Suspected bugs for Aaron's review (each doc has its table; none filed)

- Glenmoril's armour-pierce perk ladder runs backwards (AP65 0.4 against AP70 0.5).
- Vigilant Vernaccus' spell cost is ignored (no ManualCostCalc).
- Glenmoril's throwing knives have a wrong-school cost perk.
- A Moonpath citizen is on Requiem's bandit template (the same shape as the Grand Solitude stage-1 bug).
- The Dwemer Armor patch disables every recipe, so the armour can't be tempered.
- Brand-Shei's chest gets a Master lock.
- Container Merge drops the Xarrian script.
- Vigilant Shoggoth race has Health −150; the Scorpion race has BaseMass 0.25.
- The Bleeding Crown flora points at the White Cap list.
- LOTD curator rewards swap artifacts for gold.

## Usage (stage 2, subagent tokens as reported)

| Stage | Model | Tokens |
|---|---|---|
| Extraction (8) + untouched dumps (4) | Haiku | ~2.3M |
| Family readers (13) | Sonnet | ~2.1M |
| Family verifiers (12) | Sonnet | ~1.95M |
| Untouched analysis + verifier | Sonnet | ~0.25M |
| Syntheses (5 Opus, 3 Sonnet) incl. fixes, + stage-1 JSON backfill | Opus / Sonnet | ~1.07M / ~0.5M |
| Doc verifiers (8) | Sonnet | ~1.0M |
| **Stage 2 total** | | **~9.2M** (25% Haiku, 63% Sonnet, 12% Opus) |

**Whole census:** pilot about 2.1M + stage 1 about 4.8M + stage 2 about 9.2M ≈ **16M** subagent tokens, plus
orchestration in the main session.

## Limits

- `ksws03_quest.esp` fails to load in houseCARL, so the ksws03 patch is unreadable.
- Many small or noise families were unsampled by verifiers. Each doc says which conclusions rest on them.
- Held-out validation is thin outside locks, lists and magic: no held-out patch touches perks, races (partly) or the
  14 new patches.
- Behaviour that only the game can show is untested, e.g. variant v template AR, and follower registration (the
  script itself was never decompiled).

## Next

**Recon is complete enough for the spec.** The census conventions (stages 1 and 2, all with conventions.json), the
fact pack, and the open calls in each doc are the input for the spec interview. The interview's hard parts:
- the stance default and the choice rule;
- the house conventions to confirm;
- the suspected bugs;
- untested domains: now evidenced, and few.
