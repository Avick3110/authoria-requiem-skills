# S4 router disposition table — Gray Fox Cowl.esm under pack 1.2.1

Denominator: **41,187** records the plugin touches = 41,166 defined-in + 21 brushed overrides
(Cell 15, NavigationMesh 2, Weapon 1, Worldspace 1, NavigationMeshInfoMap 1, StoryManagerBranchNode 1).

## Disposition 1 — ROUTED to domain skills (lane agents, blinded) — 389 records

| Type(s) | Count | Lane / skill |
|---|---|---|
| NPC_ | 144 | requiem-npc-patching + requiem-perk-assignment (perk derivation) |
| WEAP | 17 | requiem-weapon-patching |
| AMMO / PROJ | 1 / 2 | requiem-ammo-patching (ammo PROJ); spell PROJ → magic per link |
| ARMO / ARMA | 33 / 15 | requiem-armor-patching; race-add ARMA seam → requiem-race-patching (both lanes told to state the seam) |
| MGEF / SPEL / ENCH / EXPL | 41 / 34 / 14 / 7 | requiem-magic-patching |
| BOOK | 29 | magic lane dispositions all (tomes patched; non-tomes skipped with reason) |
| INGR / ALCH / FLOR | 4 / 1 / 1 | requiem-consumable-patching |
| COBJ | 10 | split by product across weapons/armor/consumables lanes (seam reconciled centrally) |
| LVLI / CONT / ECZN | 6 / 20 / 3 | requiem-leveled-list-patching |
| PERK | 2 | requiem-perk-assignment (three-way disposition) |
| RACE | 5 | requiem-race-patching |

## Disposition 2 — Gap-mechanic / router-owned checks (done in main loop)

- **MISC 10** → economy.md value check DONE: 6 valued (DesertWolfPelt 20, AnotherWorldCoin 200,
  WormmouthHide 10, AncestralCheetahPelt 75, PraetorianDynamoCore 500, FakeGemDiamondFlawless 1000).
  Pelts/cores plausible against the vanilla→Requiem pelt/salvage economy; the 1000-value fake gem is a
  deliberate quest prop (a fake that must appraise like a real flawless diamond). Values already
  round-to-5 compliant. **No patch needed.** 4 value-0 quest tokens → skip.
- **KEYM 29** → all Value=0 → quest plumbing, skip.
- **FLST 10** → contents routed per table: 9 `manny_GF_LockList_*` + 1 `manny_GF_FList_TortureVoices`
  = lock/voice script plumbing, no domain records inside → skip with reason.
- **KYWD 7** → ride their carrier records (dispositioned with carriers in lanes).
- **GLOB 28** (GlobalShort 18 + GlobalFloat 10) → not in routing table → **catch-all fired,
  explicitly dispositioned**: quest-script variables, no Requiem balance surface → skip with reason.

## Disposition 3 — Cosmetic / world-data skip (with stated reasons) — ~40.7k records

- **Placement/world**: PlacedObject 33,203; PlacedNpc 624 (base NPC_ records are the npc lane's
  denominator); Cell 2,168+15 (world structure; the cell→ECZN linkage balance surface belongs to the
  lists lane — see open check below); Landscape 2,099; NavigationMesh 604+2 (+InfoMap 1);
  Worldspace 4+1; Region 10; Location 65; LocationReferenceType 31; Water 1; Static 530;
  MoveableStatic 7; Door 6; Furniture 2; Activator 68 (no balance fields carried — flavor
  activators); Grass 10; LandscapeTexture 17; TextureSet 85; Light 84.
- **Visual/atmosphere**: Climate 3; Weather 6; LightingTemplate 12; ImageSpace 9;
  ImageSpaceAdapter 13; EffectShader 3; ShaderParticleGeometry 2; VisualEffect 3; ArtObject 5;
  LoadScreen 12.
- **Audio**: AcousticSpace 5; ReverbParameters 3; SoundDescriptor 23; SoundMarker 22; MusicTrack 20;
  MusicType 15; VoiceType 6; Impact 3; ImpactDataSet 3 (impact data is cosmetic here — no combat
  keyword carrier among them).
- **Dialogue/quest plumbing**: DialogResponses 279; DialogTopic 201; DialogBranch 81; DialogView 21;
  Scene 17; Package 156; Message 52; Relationship 20; Quest 33 (QUST carries no Requiem balance;
  script-layer boundary → requiem-script-patching only where a VMAD needs it).
- **Brushed WEAP 1** (`DLC2EnchNordicDaggerFire03`, Dragonborn): Requiem.esp already wins downstream
  of the mod's touch → no carry needed.

## Disposition 4 — FLAGGED (no owner / open questions to surface)

1. **StoryManagerQuestNode 1 (`manny_GF_Steal`) + brushed StoryManagerBranchNode 1** — SM nodes are
   not in the routing table → catch-all route 4. NOTE: the curated oracle patch DID touch
   `manny_GF_Steal` — investigate at scoring what Requiem-relevant field it carries.
2. **Class 3 / CombatStyle 3 / Outfit 10 / Faction 30** — assignment-side rides the NPC lane (the
   NPC skill derives class/CS/faction assignments per actor), but the RECORDS themselves (a custom
   Class's stat weights, a CombatStyle's combat parameters, an Outfit's contents) have no explicit
   routing-table row. Pending the npc lane's report; candidate router-table finding.

## Open checks pending lane returns

- Cell→ECZN assignment (18 oracle-touched cells) — does the lists lane surface it?
- COBJ seam: three lanes each claim their products; verify 10/10 covered, no double-claims.
- ARMA seam: armor lane (coverage) vs perk-race lane (race-add) — verify 15/15, no gaps.
- Reconciliation: 389 (routed) + router-owned dispositions = 41,187 with zero silent rows.
