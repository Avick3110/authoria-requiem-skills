# N3–N5: discovering scripts, assets and runtime layers (live probe)

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Input to the spec session's N1–N5 discussion (`../../carried-forward.md`). Probed live
against houseCARL **2.1.2** (`ed5a9fb`), the ARR instance, profile `arr-skill-eval-profile` (the blinded eval
state). Subject: **The Rot Below** (`CJ03Elroy.esp`, 25,941 records defined), an eval task with scripts, loose and
BSA assets, and runtime-layer touches. One mod, so these are leads for the spec, not a census.*

## The question

The record diff (N2) covers plugins only. For a whole-mod job, how does an agent **discover** what a mod ships
outside its records, **understand** what matters for Requiem, and **account** for it (patched, or a reason not)?
Which of that does houseCARL already give, and what would a coverage script have to add?

## Summary

| | Discover | Understand | Account | Verdict |
|---|---|---|---|---|
| **N3 scripts (.pex)** | Good: `asset_status provider=` lists every .pex (loose + BSA); the asset walk ties each to the record that attaches it | Weak at scale: decompile is one file per call and needs a BSA extract first; a string-table grep finds the scripts worth decompiling | No tool. Scripts that change stats or grant perks are found only by reading code | Script **triage** belongs in a script, decompile only the hits |
| **N4 assets** | Good: provider census, winners/shadowed, `walk=` from records to every file they reach | Mostly not Requiem's business (meshes, textures, sounds) | The walk's reached-vs-shipped diff gives orphans and absents mechanically | Mostly **out of scope** for Requiem patching; a script can flag the few that matter |
| **N5 runtime layers** | Split: SKSE configs (SkyPatcher, Experience, …) are searchable by referenced plugin; **Data-root distributors (SPID, KID, BOS, FLM) have no reader** | SkyPatcher has a post-state overlay; SPID/KID have authoring skills but no live read | No per-mod "which runtime lines touch these records" query | The biggest **gap**; a coverage script needs a raw-text sweep here |

## N3: scripts

**Discovery.** `housecarl_asset_status provider="The Rot Below" under=["scripts/*.pex"] counts_only=true` → 156
.pex, all BSA-packed. The asset walk (`walk=true`, records seeded) names the attaching record for each reached
script (edge `script`). Seeded with 561 base records (all non-dialogue types), it reached 24 of the 156; the 132
unreached are mostly `TIF__` / `QF_` fragments of the dialogue and scenes I did not seed, so seed INFO, DIAL and
SCEN too for a full join.

**VMAD binding.** `housecarl_check findings=["scripts"] plugins=["CJ03Elroy.esp"] counts_only=true` → 87
records with scripts, 104 unbound properties (42 names), 3 unverifiable. These are mostly vanilla trap and trigger
scripts with optional properties. It is a signal for broken wiring, not for Requiem relevance.

**Understanding.** The walk found one dangling attachment: `CJ03DisableJournal1.pex` is attached to the main quest
but nothing ships it (a lead, not checked further). Decompile works and reads clean (`all structured clean` on
both tries), but:
- a script inside a BSA needs `housecarl_bsa_extract` first (one call extracts all 156 with `under=`);
- `housecarl_decompile_script` takes **one .pex per call**, so 156 calls is not an agent workflow.

**Triage by string table.** A .pex keeps called function names in plain text, so a byte grep over the extracted
folder finds candidates without decompiling. On the Rot Below:

| Call | Scripts | What it was, on decompile |
|---|---|---|
| `AddPerk` | 1 (`CJ02_QF_CJ03RotBelowMQ`) | **Quest reward:** `Game.GetPlayer().AddPerk(AdabellePerk)` + `AddSpell(PerkAbility)`. The perk record is in the plugin, but its *grant path* is only in the script |
| `AddSpell` | 2 | the above, plus a cloak-apply script |
| `SetActorValue` | 8 | boss ambush controllers: `SetActorValue("Health", aMax * 0.75)`, a scripted heal relative to max, which scales with Requiem's health |
| `PlaceAtMe` | 3 | spawners: NPCs entering the world outside leveled lists |
| `GetFormFromFile` | 0 | no hard-coded cross-plugin lookups (these are invisible to every record read) |

So a cheap script can do triage: extract, grep for a short list of stat, perk, spell, item, spawn and
`GetFormFromFile` calls, then decompile only the hits (here 13 of 156). That is mechanical, which suits N2's
reasoning ("a script can flag what an agent skips").

## N4: assets

**Discovery.** `asset_status provider="The Rot Below" counts_only=true` → 939 files, 642 won by the mod, 297
shadowed (247 by its own texture-replacer add-on, 21 by ARR's ParallaxGen output). One call, no disk walk.

**Accounting by walk.** The seeded walk (561 records) reached 1,539 nodes: 685 shipped by the mod, 772 won
elsewhere (vanilla or Requiem files the records reuse), **82 absent**. Diffing reached against shipped:
- **254 shipped but unreached:** the 132 unseeded script fragments, 84 textures, 18 meshes, 4 music tracks,
  6 sounds. Some are leftover dev files (`deathknighttest.nif`, `cj03adabellestaffprojectile - copy.nif`). Others
  hang off records I did not seed (cells, music types).
- **82 absent:** mostly facegen. Nine NPCs (`CJ03Livestock*`, `CJ03MoldCultist…DeadRingofLight`) have no baked
  head or tint, a possible dark-face lead for BUGS.md, not checked. The rest are missing voice `.lip`/`.wav` files.

**Requiem relevance.** Almost none. Requiem patches records, not files. The asset cases that touch a Requiem patch
are a model path on a weapon or armor record (a patch copies the record, so the path rides along), and facegen
when a patch edits an NPC's face fields (`check findings=["facegen"]` covers that). Assets are a
**completeness check**, not a patching domain.

**Friction.** The walk's `records=` takes FormIDs inline only. `@file` (the form every other list parameter takes)
is refused: `Malformed FormKey string`. A whole-mod walk had to inline 561 FormIDs. Worth a houseCARL issue.

## N5: runtime layers

**SKSE configs: covered.** `housecarl_skse findings="config" filter="CJ03Elroy"` matches configs by the plugin
they reference, and resolves every reference (OK, MISSING, DANGLING). It matched three configs (the render was cut
inside the first, a 10k-reference UBE INI, before naming the others). A raw search of the enabled mods' `SKSE`
folders found five: two UBE SkyPatcher armor INIs (`filterByArmors=CJ03Elroy.esp|…:armorAddonsToAdd=…`), two
`Experience` INIs (XP per actor and race, a generated Synthesis output, `[Plugin]` section headers with bare
FormIDs), and a Devotion JSON. Which three the audit's shapes catch is unconfirmed. The UBE lines point at a plugin
that is not active in the eval profile, and the audit flags them MISSING.

**SkyPatcher: partly.** `housecarl_skypatcher_layer filter=` matches INI, folder or mod **names**, not targets:
`filter="Rot Below"` found nothing, even though two INIs target the mod's armor. The post-state overlay
(`source={"overlay":"skypatcher","state":"post"}`) answers per record, but a delta over a scan is refused (*"Name
the records via formids="*). So "which SkyPatcher lines change this mod's records" is two steps: find the INIs by
config audit or grep, then read the named records' post state.

**Data-root distributors: no reader.** 470 loose `*_DISTR.ini` / `*_KID.ini` / `*_SWAP.ini` / `*_FLM.ini` win in
this profile, from 174 mods. `asset_status` lists them, but no houseCARL tool parses them; `housecarl_skse` says
outright that SPID and KID are out of its scope. A plain grep for the plugin name found two SPID lines that
distribute spells to the mod's NPCs:
- `DragonsWeatherNG_DISTR.ini`: a spell onto `B2C670:CJ03Elroy.esp` (the dragon-form boss);
- `WetAndColdSPIDPatch_DISTR.ini`: an exclusion naming one of the mod's NPCs.

Two limits on a grep:
- **EditorID lines are invisible to a plugin-name grep.** SPID and KID accept EditorIDs, so a full sweep must also
  match the mod's EditorIDs (or the resolver must parse the grammar).
- **The grep reads every mod folder, not winners.** Use `asset_status` to get the winning copy per path first.

**OAR, animations.** The Rot Below ships none. OAR configs are files under `meshes/…/OpenAnimationReplacer/`, so
`asset_status under=` finds them; the OAR skill reads them.

This confirms the census gotcha (`../census/`): runtime distribution doesn't show in record references unless it
goes through an outfit record.

## Outcome (same day)

houseCARL took this probe's gaps into a redesign of its asset and runtime layer (one asset door, one walk, one
select, one runtime-layer reader). It owns the readers in leads 2–4 below, and our runs that measure those calls
wait for its PRs. The rulings are in `../../decisions.md` (2026-10-31). The leads stand as the evidence; where they
say "coverage-script sweep", read "houseCARL capability".

## What this means for the spec (leads, not decisions)

1. **Coverage script scope (N2).** Scripts and runtime layers can share N2's mechanism: walk the mod's records,
   ship-list and runtime text, and flag every item with no disposition. Assets need only the orphan/absent diff,
   reported rather than patched.
2. **Script triage is mechanical.** Extract, string-grep a call list, decompile only the hits. The call list is a
   spec item. It decides what "a script that matters to Requiem" means (perk/spell grants, actor-value writes,
   spawns, item adds, `GetFormFromFile`).
3. **Distributor INIs need a reader.** Either a coverage-script sweep (plugin name + EditorIDs, winners only), or a
   houseCARL feature request for a parsed SPID/KID/BOS layer like `skypatcher_layer`. The second is houseCARL's
   call.
4. **houseCARL issues worth filing** (verify before filing, per CLAUDE.md):
   - `asset_status walk=true records=` refuses `@file` lists;
   - `skypatcher_layer filter=` matches names only, with no way to ask by target plugin;
   - no parsed reader for Data-root distributor INIs.

## Reproduce

All read-only. In order:
`load_order_status filter="CJ03Elroy.esp"` → `asset_status provider=… counts_only` → `records plugins=…
defined_in=true project=aggregate group_by=type` → `records … to_file=` (base records) → `asset_status walk=true
records=[…] source_provider=…` (spills the node list) → `check findings=["scripts"] plugins=[…] counts_only` →
`bsa_extract under=["scripts/*.pex"] out_path=…` → string grep → `decompile_script out_path=…` →
`skse findings="config" filter=…` → `skypatcher_layer filter=…` → `asset_status under=["*_DISTR.ini", …]
counts_only`.
