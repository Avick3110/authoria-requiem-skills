# Census: QUEST-MISC (quests, dialogue, globals, world/FX records)

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus:** 13 types, 408 diff rows: Quest 109, DialogResponses 66, DialogTopic 145, Projectile 30, Impact 15,
Activator 11, Explosion 8, Weather 8, ShaderParticleGeometry 5, ImpactDataSet 4, Furniture 3, StoryManagerQuestNode 2,
GlobalShort 2. One to nine plugins per type. Held-out (The Rot Below) has rows only in DialogResponses (6) and
DialogTopic; the other 11 types have 0 held-out rows. Script-decided rows (noise / bookkeeping / presentation): 75.

**Headline:** this domain is almost all **noise or per-mod compat edits**. Requiem has no quest / dialogue / FX
conventions to carry. The only Requiem-aligned conventions are in **Projectile** (speed, collision radius, sound level,
gravity tiers). The one ARR-authored quest is the follower bridge. Weather / shader / impact rows are Dragon Tweaks
master-patch visuals.

## Conventions

| # | Convention | Class | Layer | Rows / plugins | Per-plugin spread | Held-out | Conf |
|---|---|---|---|---|---|---|---|
| 1 | Bolt-class projectile Speed 5600 (Requiem bolts 5600, arrows 3600) | Requiem-aligned | input | 6 / 3 | Glenmoril 4 (Bullet01 + 3 Tonitrus, 5400 -> 5600), Remiel 1 (`237ED9` HLIOBoltProjectile 8100 -> 5600), Unslaad 1 (`17854E` arrow 3600 -> 5400, not aligned); 5 of 6 land on 5600 | none | med |
| 2 | Mod projectile CollisionRadius 0.5 (all Requiem arrows / bolts 0.5) | Requiem-aligned | input | 5 / 1 | Glenmoril only (1.0 -> 0.5) | none | med |
| 3 | Arrow projectile SoundLevel 1 (CWArrowProjectile `020DDE`, REQ_Projectile_Arrow_*; Requiem bolt SL 1 live, arrow comparable unsampled; Unslaad arrow left at 2) | Requiem-aligned | input | 2 / 1 | Vigilant only (2 -> 1) | none | med (unsampled) |
| 4 | Gravity mapped to Requiem tiers: 0.2 elven / glass, 0.35 iron-steel + bolts, 0.5 orcish / dwarven; 1.0 for heavy special | Requiem-aligned tier map, one outlier value | input | 15 / 3 | Glenmoril 0.15 -> 0.4 (off-tier), Tonitrus 0.35 -> 0.5, Snow Elf / Ivory 0.2, Remiel bolt 0.2 -> 1.0 (off the 0.35 bolt tier), Vigilant great / log arrows 1.25 (`0BAB16` `0BD35A` `0C7E3A` `0C7E3B` `0D1013`); Unslaad has no gravity row | none | low-med |
| 5 | Mod dialogue conditions hand-edited as deliberate input (price thresholds, quest-stage gates), not a revert to source | house convention | input | 34 (19 add, 15 rem) / 6 | Become a Bard 13, Val Serano 10, Inn Baths 4, Rot Below 4, Kaidan 2, Simply Order Summons 1 (Riften 0) | Rot Below: 2 add + 2 rem condition rows, not verified | low |
| 6 | Mod economy / difficulty globals retuned with their dialogue thresholds (0BathCost 10 -> 35 with GetItemCount 10 -> 35, 9 -> 34; zDcdgDifficulty 0 -> 15) | house convention | input | 2 / 2 | Inn Baths 1, Dac0da 1 | none | low-med |
| 7 | Mod quest level gate raised (Gray Fox node GetLevel >= 10 -> >= 15 on Subject) | house convention (reason plausible, unproven) | input | 2 / 1 | Gray Cowl of Nocturnal only | none | low |
| 8 | `Authoria_REQ_MajorFollowerBridge`: one script quest, 8 UniqueActor aliases (Inigo, Kaidan, Auri, Xelzaz, Remiel, Lucien, Ashe, Val Serano), property REQ_FollowerControl -> `82F3AF:Requiem.esp` | house convention (Requiem-linked) | input | 1 / 1 | `Authoria - Master Patch - Requiem Follower Registration.esp` only | none | med |
| 9 | Quest VMAD / alias / objective / stage "removed + added" pairs are re-saved elements | noise | input | 79 residual + 30 script / 9 | Vigilant 39, Alternate Perspective 34, Bruma 9, Moonpath 7, Werewolf Fix 7, Wyrmstooth 5 | n/a | med (VMAD scripts only) |
| 10 | Restore a mod's own crafting keyword over a compat patch's vanilla forge keyword `088105` (Zenithar anvil `0B1CCE:Vigilant.esm`; `0B1CCD` is the new keyword) | source restore, not a Requiem rule | input | 2 / 1 | Vigilant only | none | med |
| 11 | Presentation / serialisation edits (activator text, decals, light / sound refs, DialogTopic Timestamp / Subtype). NOT the 64 Kaidan whole-INFO Responses rows (60 removed, 4 added): cannot-tell, unverified | noise (INFO blocks: unclear) | input | ~125 + 64 unclear | Kaidan 99 (35 serialisation + 64 INFO), Dragon Tweaks 15, LOTD 9, Troll MCO 5, Dawnguard Arsenal 3 | n/a | med (low for DialogTopic Responses) |
| 12 | Dragon Tweaks master patch defines its own weathers (8), particle shaders (5), repoints MAGDragonFireImpactSet to DeadlySpellImpacts (2 pairs) | house convention (compat visual) | input | 17 / 1 | Dragon Tweaks master only | none | med |

## Upstream checklist (what a patch must carry)

- **New mod ammo projectiles:** Speed 5600 for bolts, 3600 for arrows (Requiem values); CollisionRadius 0.5; SoundLevel 1;
  Gravity from Requiem's ladder (0.2 elven / glass, 0.35 iron-steel, 0.5 orcish / dwarven, 0.65 ebony / daedric /
  dragonbone, bolts 0.35 / 0.5); 1.0 only for heavy special projectiles (Requiem precedent exists). Sources:
  `000BB4:Dawnguard`, `00D09A:Dawnguard`, `03BE11` (Skyrim.esm-defined), `020DDE`, live.
- **Follower mods** meant to join Requiem follower control: add an alias for the NPC to the bridge quest (script property
  `REQ_FollowerControl` = `82F3AF`). The script was not decompiled; the build also sets 82F3AF as a script property (`StaticReferences.cs`, `CustomLockpicking.cs`), so the bridge may be redundant; confirm with Aaron (call 3).
- **Per-mod economy and quest gates** (dialogue prices, GlobalShort costs, story-manager level gates) are tuned by hand.
  Change a global and its dialogue conditions together.
- Everything else here (VMAD, aliases, objectives, stages, topics, FX) needs no Requiem input.

## Downstream bake package

None. No Reqtificator rule rewrites a record of these 13 types and no RFTI proof exists (the build only sets 82F3AF as a script property in `StaticReferences.cs` / `CustomLockpicking.cs`; no Quest record is rewritten). Nothing here
is **baked**; every convention is input. The absence is the finding.

## Per-type detail

- **Quest (109, 9 plugins).** 30 script-decided noise (18 re-saved pairs, 9 reorders, 3 bookkeeping). Residual: VMAD
  scripts 40 and aliases 10 (add / remove pairs), objectives 7, aliases 4, stages 4, EventConditions 12 (Vigilant
  re-listed; Bruma (9 Quest rows: VMAD + 4 EventConditions) adds GetInCurrentLoc / GetEventData / GetInWorldspace == 0 on WIChangeLocation09 to exclude
  BSHeartland: compat, not Requiem), QuestFormVersion 0 -> 65 on MS05Start (re-save stamp), 1 defined quest, in the Requiem Follower Registration master patch (conv 8).
  "ARR restores the source mod over an intermediate compat patch" holds for Wyrmstooth `028F01` (Reqtificated equals
  source; the NAT-ENB patch differs) and Vigilant. Verifiers agreed on VMAD scripts only; everything else is **unsampled**.
- **DialogResponses (66, 8 plugins, 1 held-out).** The reader's "revert to source" claim was **false in 3 of 3 new
  cases**: Inn Baths `000809` / `00080B` (GetItemCount 10 -> 35, 9 -> 34, paired with 0BathCost), Become a Bard
  `0534F2` (stage quest `053511` MS05 -> `042868` MS05Start, plus GetIsID gates; differs from Skyrim.esm, USSEP and
  BardsReborn), Val Serano `C20E5F` (differs from the source plugin itself, only 2 providers). Class fixed noise ->
  house convention. Response (8 + 8) and TIF VMAD (3 + 3) pairs are **unsampled**.
- **DialogTopic (145, 8 plugins, 1 held-out).** Kaidan 99 rows: Responses removed 60 + added 4 (whole INFO blocks, cannot-tell, not serialisation), Timestamp 10, Name 7, Version2 4, other 14. Overall Responses 77 removed vs 17 added; verifiers could not re-read the removals.
- **StoryManagerQuestNode (2) / GlobalShort (2).** Conv 7 and 6.
- **Activator (11).** 10 presentation + one text override ("Dispel Curse" -> "Boop", Frozen Heart): noise.
- **Furniture (3).** One presentation row + the Zenithar anvil keyword pair (conv 10).
- **Explosion (8).** Troll MCO nulls SpawnProjectile on 2 slam explosions (cannot-tell, probably visual); Dawnguard
  Arsenal Vampire Bane: Light nulled (`03C5A5` -> null), Sound1 / Sound2 swapped to DawnguardArsenal.esp (record is Requiem-named `REQ_Explosion_`, edit is visual).
- **Impact / ImpactDataSet (19).** Dragon Tweaks decals MaxHeight 32 -> 300/400, MaxWidth 128 -> 300/400, Min 8/100 -> 225-300, PlacementRadius 16 -> 0.01 on 3 impacts; set
  repoint `020DE2:Skyrim` and `00097E:DeadlyDragons` -> `000D68:DeadlySpellImpacts`. Visual; verifier calls the repoint
  noise rather than convention.
- **Projectile (30, 4 plugins).** Glenmoril 15, Vigilant 12, Remiel 2, Unslaad 1; conv 1-4. Requiem has 1.0 precedent
  (REQ_NULL elven blood arrow, DLC2 riekling spear); only 1.25 has none.
- **Weather (8) / ShaderParticleGeometry (5).** New master-patch records (conv 12).

## Held-out validation

Only The Rot Below has rows here (DialogResponses 6, plus a share of DialogTopic's 145). Its condition edits are
consistent with conv 5 but the verifiers did not sample them: **weak validation**. No held-out patch touches Projectile,
the only Requiem-aligned family, so conv 1-4 are **unvalidated** (no data, not a contradiction).

## Suspected bugs (for Aaron's review, not filed)

| Record | Plugin | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| Vigilant great / log arrows `0BAB16` `0BD35A` `0C7E3A` `0C7E3B` `0D1013` | Reqtificated - Vigilant | Gravity | 1.25 (speed 2600-2800) | a Requiem tier, or 1.0 | no Requiem arrow exceeds 1.0 (live) | outlier-bug (or deliberate heavy feel) |
| Unslaad frost arrow `17854E` | Reqtificated - Unslaad | Speed | 3600 -> 5400 | 3600 (arrow) | Requiem arrows 3600, bolts 5600 | outlier-bug, low |
| Glenmoril Bullet01 | Reqtificated - Glenmoril | Gravity | 0.15 -> 0.4 | 0.35 or 0.5 | 0.05 off the tier | noise |
| Remiel bolt `237ED9` | Reqtificated - Remiel | Gravity | 0.2 -> 1.0 | 0.35 (Requiem bolt `000BB4`) | bolt off the bolt tier | outlier-bug, low |
| Troll MCO slam explosions `00080B` `00080E` | Reqtificated - Troll MCO | SpawnProjectile | nulled | source projectile | cannot-tell; check the effect still spawns | outlier-bug, unconfirmed |

No suspected systemic bug in this domain. FormIDs are from the ledger.

## Open calls for Aaron

1. Gravity 1.25 (Vigilant great / log arrows) and 1.0 elsewhere: keep as heavy-projectile exceptions, or move to a tier?
2. Unslaad frost arrow Speed 5400: arrow, so 3600 per Requiem. Intended?
3. Is `_Authoria_REQ_MajorFollowerBridge` the supported follower registration route (script not decompiled)? Should the
   skill tell new follower mods to join it?
4. Gray Fox gate 10 -> 15: general "raise mod quest gates for Requiem scale" rule, or one-off?
5. Troll MCO SpawnProjectile nulls: intentional visual cleanup?

## Limits

- Unsampled: Quest aliases / objectives / stages / EventConditions, DialogResponses response and TIF pairs, DialogTopic
  Responses added, Projectile SoundLevel, Explosion Sound1 / Sound2, most Impact decal fields. Those rest on reader
  labels (noise, low confidence).
- Dominant plugins: Kaidan (DialogTopic 99 / 145), Vigilant + Alternate Perspective (Quest 73 / 109), Dragon Tweaks
  (every Impact, Weather, Shader, ImpactDataSet row), Glenmoril (Projectile 15 / 30).
- Requiem has no comparable for quests, dialogue, globals, weather or impacts, so "house convention" here means
  "deliberate per-mod edit", not an ARR-wide rule.
- Untouched records and the ksws03 load failure were not examined for these types; `UNTOUCHED.md` lists no case for them.
