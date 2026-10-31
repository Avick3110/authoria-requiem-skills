# S4 adjudication — oracle-patched records my lanes skipped/flagged (21 records)

Frame: (a) semantic agreement, different carry strategy · (b) genuine judgment divergence ·
(c) genuine field/coverage miss on our side · (d) oracle stamps Reqtificator OUTPUTS (pack doctrine
says leave to build — our omission is doctrine-correct). Note: the oracle patch is authored in the
FINAL-VALUES style throughout (writes post-Reqtificator-scale numbers + REQ_DamageType_* /
REQ_Tempering_* keywords, which are Reqtificator outputs per requiem-patching doctrine). The pack's
input-carry strategy is different-by-design; raw-number diffs are NOT automatically errors.

## (c) GENUINE MISSES — findings against the pack

1. **Summoned actors keep PCLevelMult (2 of 3) / low fixed level (1 of 3).**
   mihailgoronSUMMON: PCLevelMult×1 → oracle fixed 30. SadraakaDraugrSummonable: PCLevelMult×1.1 →
   oracle fixed 28. CorruptedRedguardSummonable: fixed 15 → oracle 24.
   npc lane skipped all three as "balanced via its summon spell"; magic lane tiered the SPELLs but
   never touched the actors. **A summoned NPC_'s own level/stat block has no owner** — Requiem
   doctrine wants fixed levels (PCLevelMult is the anti-pattern the mod carries). FINDING: doctrine
   seam gap between requiem-npc-patching (skips summonables) and requiem-magic-patching (owns only
   the spell). FIX candidate: npc skill's summonable rule should say "the summon SPELL tier routes to
   magic; the ACTOR record still gets the fixed-level/stats pass here."

2. **NonPlayable NPC-hand weapons skipped (5 records: BladesSwordGuardian, BenSala_Scimitar,
   UmbraNonPlayable, ForgottenChaosBowNonPlayable, DraukiirTorch).**
   Oracle re-tiers all of them (dmg 48-84 final-scale, reach 0.7, speed fixes, +REQ_DamageType).
   Weapons lane skip reason "never reaches the player as loot, so no material tier applies" ignores
   that the player FEELS these weapons' damage from the NPC's hand — outgoing NPC damage is balance.
   FINDING: requiem-weapon-patching's skip taxonomy treats NonPlayable as cosmetic; should treat
   NPC-wielded combat copies as patchable (derive same tier as the playable twin / wielder tier).
   (DraukiirTorch is arguable — trap/prop, dmg 2 — but oracle tiers even it, 48.)
   NOTE: oracle also adds NPCsUseAmmo to ChaosBowNonPlayable — a real functional fix the lane's skip
   missed (the playable twin DID get NPCsUseAmmo from our lane).

## (b) JUDGMENT DIVERGENCES — flag was the right move, recommendation differs

3. **3 ECZN zones.** Lane flagged (per doctrine: zone floors are the author's call; Reqtificator
   auto-opens boundaries) with Min-raise recommendation (~25 from lore-dungeon comparable). Oracle:
   Khenzedum Max 0→20 (early-dungeon cap); AlShedim + ForgottenDraugrTomb Min 15→20 AND Max 0→30.
   Same band-thinking, different bands per dungeon tier. Flag = correct behavior; the skill could
   name the "cap early dungeons' Max" pattern explicitly.
4. **Umbra (playable).** Flagged as internally-inconsistent artifact (steel kw, dmg 24, crit 24);
   recommendation was Dawnbreaker-tier source 13/6. Oracle: ebony-tier final 84/84, value 15000,
   swaps WeapMaterialSteel→VendorItemWeapon, +REQ_Tempering_LegendaryBlacksmithing (output-stamp).
   Tier disagreement (Dawnbreaker-unique vs ebony-final); flag surfaced exactly the right question.
5. **3 armor artifacts.** ImhotepCrown AR 23→200 (heavy final), DraukiirMask AR 1→96 + value 5000,
   SpringheelJak AR 0→53 + value 7500 (+tempering-kw output-stamps). Lane flagged all three with
   on-ladder recommendations in the same direction (SpringheelJak rec ~45 vs oracle 53). Flags
   correct; recommendations tier-adjacent.

## (a)/(b) borderline

6. **3 Stone abilities (magnitude 5000).** Lane flagged "no clean in-lane comparable." Oracle: 50
   (author scale typo ÷100). Honest flag, but the router's gap table has standing-stones.md (the
   Stones are standing-stone-like abilities) — the lane never consulted it. Half doctrine-gap
   (router should have co-routed the gap reference), half agent judgment. Oracle's 50 was derivable.

## (d) ORACLE OUTPUT-STAMPS (our omission is doctrine-correct)

- REQ_DamageType_Slash added on every blade; REQ_Tempering_LegendaryBlacksmithing/AD3AF5 tempering
  tiers on artifacts. Pack doctrine: these are Reqtificator-assigned outputs — carried by the build,
  not the patch. No action.

## Router-level

7. **manny_GF_Steal SMQN**: oracle raises quest-start GetLevel 10→15. Router catch-all flagged it
   (correct — not dropped); no skill owns quest-start level gating. FINDING: candidate gap-mechanic
   reference (quest pacing gates in a de-leveled world).
8. **YokudaNonPlayable armor**: skipped as display-duplicate; oracle re-tiers (AR 20→180 + material
   keyword swap). Same class as finding 2 (NPC-worn display armor still balance-bearing when worn).
9. **18 Cell records + 3 novel ECZNs (oracle)**: curated patch created 3 zone tiers
   (EarlyDungeons/DesertSide/DeepDungeons) and assigned 18 unzoned cells. Lists lane never surfaced
   cell→zone assignment for unzoned dungeon cells. FINDING: requiem-leveled-list-patching gap —
   "an unzoned dungeon cell in a quest mod needs a zone (create one if none exists)".
