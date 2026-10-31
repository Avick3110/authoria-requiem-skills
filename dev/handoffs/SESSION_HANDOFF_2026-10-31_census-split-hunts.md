# Session handoff — census review and split hunts 1–6

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (frozen at close). Project: `dev/rebuild/`. Supersedes
`SESSION_HANDOFF_2026-10-30_recon-pass2-census.md` as the latest handoff.*

## Where we are

- **Stage:** still recon. The census is done. This session reviewed its quality, then started **split hunts**:
  resolving the census's "no consensus" splits by hypothesising first. Six are done. **The next session continues
  the split hunts.**
- **Branch:** `claude/skills-rebuild` (worktree `.claude/worktrees/skills-rebuild`), **pushed**, tracking origin.
  No PR: open one at spec time.
- **Approver:** Aaron alone.

## The method (Aaron's rule, 2026-10-30)

A split with no apparent consensus usually has a good **domain** reason that just needs working out. Aaron's
example from arr-ube-projects: SMP hair collision v stops turned out to be decided by **hair length**.

So for every split:
1. Read examples from each side.
2. Write **8+ domain hypotheses**, each a property a script can measure.
3. Test them by script against the census raw data and live houseCARL reads of the Requiem / Magic Redone
   comparable.
4. Chase the exceptions.
5. **Log the dead hypotheses too.**

Provenance (era, tool, author) is the **last** resort. "No consensus" is not a verdict until the log exists.

Whatever the records can't settle becomes a question in the shareable questions doc. Aaron wants it short, real
and evidence-backed, so it can go to Heisen without looking lazy.

## Done this session

| Split | Doc (`recon/`) | Headline |
|---|---|---|
| 1 NPC workflow | `2026-10-30-split-npc-workflow.md` | `AlreadyReqtified` + kit iff the NPC doesn't inherit SpellList (99.5%, from the Reqtificator's flag-only early return). Bake v upstream tracks the skills-era live runs (Gray Cowl, Val Serano, Apocalypse, Bruma) |
| 2 Perk values | `2026-10-30-split-perk-values.md` | Item-perk benefits roughly halved onto 5% steps (10% floor); drawbacks kept; AP snaps to Requiem steps; flat v-type adds to a 40 / 45 / 50 table |
| 3 Armour AR 0 | `2026-10-30-split-armour-ar0.md` | The **slot** decides (96.8%): core slots keep AR; extra slots, clothing, replicas and wigs carry 0. Matches Aaron's modular-set reason |
| 4 Enchant charge | `2026-10-30-split-enchant-charge.md` | 500 × enchantment tier; MR staff ladder 500 / 1000 / 2000 / 3000 / 6000; uniques capped at 3000; removed or scripted enchantments → unlimited |
| 5 Recipe retirement | `2026-10-30-split-recipe-retirement.md` + `census/recipe-orphans.csv` | Per-piece curation (degraded copies, variants, armour-only mods). **689 retired items obtainable nowhere, 250 in COCKS-listed mods** (accidental candidates) |
| 6 Death-item loot | `2026-10-30-split-death-item-loot.md` | Gold stays on humanoids, comes off creatures (Requiem's rule extended); generic gear off regular enemies, bosses keep rewards; species parts stay |

**Questions doc:** `recon/questions-for-list-authors.md` (21 questions), LIVING. Append a section per
split; record answers inline. Aaron shares it with Heisen.

**Aaron's facts from this session** (already folded into the docs):
- Heisen made about 99% of the patches.
- Heisen probably moved from a Synthesis-style patcher to agent patching plus hand tweaks.
- AR 0 on extra pieces exists to keep modular sets from out-tiering their tier.
- In an older ARR version every mod-added armour was uncraftable. COCKS (`Authoria - Crafting Catergories` KID)
  then let many back in, and some may have been left uncraftable by accident.
- Armour-only mods were sometimes distributed instead of being made craftable.

**Also this session:**
- **Census quality review:** Claude Output note `2026-10-30-census-mining-review.md`. §6 is the hypothesise-first
  plan; §5 is superseded.
- **Harness committed:** `arr-requiem-eval` `65b1d93` (local only). `census/splits/` is committed at close.

## Next: split hunts still open (pick in this order, or Aaron's choice)

1. **NPC D1: why ARR added a template to 345 untemplated NPCs.** Mostly Grand Solitude and the held-out Animated
   Carriages. This needs data the census didn't extract: placement (cell, faction, leveled-list membership).
2. **Ammo damage 4–5×.** Hypothesis: a material tier table rather than a ratio. Compare with live Requiem arrows,
   bolts and the creature ammo (`REQ_Creature_*`).
3. **Weapon reach, the 8% off-class snap.** Hypotheses: weapon types outside Requiem's classes (spears, katanas,
   rapiers), animation v keyword mismatch.
4. **Remaining house conventions:**
   - `NoArmorRatingRescale` on everything (83 plugins);
   - the encounter-zone caps;
   - the Bruma master-patch habits;
   - LOTD Visage scaling;
   - Glenmoril's pistol charge step table;
   - Bruma's +1000 generic-charge offsets (3 per tier).
5. **The other census docs' "Open calls for Aaron"** that look like splits: MAGIC, RACE, LOCKS, CONSUMABLES,
   QUEST-MISC.

## Other open items (not split hunts)

- Verify the **unsampled stage-2 NPC families** (Level, offsets, skills, ActorEffect). These were labelled
  "requiem-aligned" but never verified; stage 1's same families were wrong 10 / 13 times.
- Merge the census **suspected bugs** into one `suspected-bugs.md` (promised by the method, never written).
- Decide on **D5** (scored held-out validation): run it where held-out data exists, or drop it in `decisions.md`.
- When merging the 13 `conventions.json` files for the spec, **normalise the labels** and **add a dominance field**.
  Bruma, Apocalypse and Glenmoril dominate some "consensus" findings.
- **Live install:** `<home>/.claude/skills/authoria-requiem` is absent (parked since the eval). Sync it only when a
  skill run needs it.

## Where the tools and data are (local harness: `arr-requiem-eval/census/`)

- `splits/*.py`: one script per test, rerunnable. Inputs are the census raw dumps (`stage*/raw/<slug>.subject` and
  `.ref<n>.jsonl`, `stage*/work/headers.json`) plus live houseCARL `to_file` spills saved beside the scripts
  (`requiem_perks.jsonl`, `staves_live.jsonl`, `req_ench_weapons.jsonl`, `recipes_live.jsonl`, `items_dist.jsonl`,
  `death_*`, …).
- All live reads are from the census snapshot, epoch `e2-58972ca8618e79ea`. If the epoch changes, re-read before
  reusing the spills.

## Gotchas learned

- **Decode biped slot bitmasks.** `BodyTemplate.FirstPersonFlags` mixes slot names with raw numbers (65540 =
  slot 46 + Body). Reading only the names misclassifies core pieces.
- **A template link can be `(null link)` while TemplateFlags are set.** The Reqtificator tests the **flag** only.
- **houseCARL fields at depth 1 don't expand lists.** Use `depth` 2+ for Keywords and Entries, and the `matches`
  field on `references=` rows to map targets back.
- **`resolve_names` doesn't name a link inside `ObjectEffect`.** Take the tier from the record's EditorID, or read
  the targets separately.
- **Requiem v vanilla deltas** can be muddied by a plugin between them (e.g. Simple Hunting Overhaul). Read
  `source=Requiem.esp` and `source=Skyrim.esm` directly.
- **Shells:**
  - PowerShell 5.1 mangles `gh … -q` jq strings with inner quotes; use `--json` plus Python.
  - The Bash tool halves backslashes, so script files go through the Write tool.
  - `cd` inside a command moves the session's working directory; use absolute paths.
- **Runtime distribution** (SPID, SkyPatcher) is invisible to record references unless it goes through an outfit
  record. `Authoria - Modded Outfit Distribution` does go through outfits. SPID `Item =` lines by EditorID were
  not checked.
