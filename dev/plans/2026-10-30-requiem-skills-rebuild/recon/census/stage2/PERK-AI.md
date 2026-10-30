# PERK-AI census: Perk, Class, CombatStyle, MovementType, IdleAnimation

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus.** 5 types, **564 delta rows** (Perk 415, CombatStyle 118, IdleAnimation 17, MovementType 12, Class 2) across
**17 distinct plugins**. Held-out (eval-reference) rows: **0 in every type**. The consensus files do not tally records;
rows are the authoritative unit. Perk is the only multi-plugin type (15 plugins). CombatStyle, MovementType and
IdleAnimation each come from **one** master patch, so "per-plugin spread" there is a single plugin by construction.

**Headline.** A small domain, mostly *not* Requiem-aligned: Requiem has almost no analog (Requiem.esp touches 9 CSTY,
3 MOVT (all Horse), 5 KillMove idles, no Class). The only Requiem-anchored convention is the Perk armour-pierce snap.
The rest is single-patch tuning or a compat merge. **No held-out validation is possible**: no eval reference touches
these types.

## Conventions

| id | Convention | Class | Layer | Rows / plugins | Per-plugin spread | Held-out | Conf |
|---|---|---|---|---|---|---|---|
| PERK-AI-1 | Glenmoril armour-pierce perks (6: AP75 / 50 / 55 / 60 / 65 / 70) re-valued: AP75 -> 0.5, AP50 -> 0.75, AP55 -> 0.7 match Requiem steps (not a uniform halving); AP60 / 65 / 70 (0.65 / 0.4 / 0.5) are off-scale | Requiem-aligned for 3 of 6 (0FAB0A AP25 = 0.75, 0FAB0B AP50 = 0.5, 033869 AP30 = 0.7, all Requiem.esp ModTargetDamageResistance) | input | 12 rows (6 perks, both halves) / 1 | Glenmoril only for the AP ladder (its other 49 Effects rows are other perks) | n/a (0 rows) | med |
| PERK-AI-2 | Source-mod damage-type multipliers (ModAttackDamage / CalculateWeaponDamage / ModSpellMagnitude / ModBashing, Multiply) **always shrink** (46 of 46 paired rows, 0 grow), toward ~1.1-1.3. Landing values: 1.1 x14, 1.15 x12, 1.2 x8, 1.25 x3, 1.3 x3, 1.5 x2, 1.0 x2. Only 24 / 46 land on 1.1 / 1.2 / 1.5; 1.15 is the commonest off-step value; none land on 1.4 | Partly Requiem-aligned: Requiem steps seen live are 1.1 (03FFFA), 1.25 (0BABE4 REQ_OneHanded_WeaponMastery1), 1.5 (079343, 079349); 03FFFA / 0BABE4 / 079343 are won by Requiem - Weapons and Armor Redone, not Requiem.esp. 1.2 and 1.4 steps are not shown by those records. 1.25 is itself a step, so LOTD 1.25 -> 1.15 moves off one | input | 46 paired rows / 8 | LOTD, Fishing, Unslaad, Vigilant, Remiel, Dibella, UEGD plus one more (not itemised); examples LOTD 1.25 -> 1.15, Fishing 1.25 -> 1.1, Unslaad 1.5 -> 1.2, Vigilant 1.5 -> 1.3, Remiel 1.5 + 2 -> 1.2 | n/a | med |
| PERK-AI-11 | ModTargetDamageResistance (armour-pierce style) perks elsewhere are also **weakened**, mostly to 0.65-0.9 (0.8 / 0.9 common): Vigilant 0.6 -> 0.8, 0.5 -> 0.9, 0.75 -> 0.9; Remiel 0.4 -> 0.8; Dac0da 0.5 / 0.25 -> 0.75; Unslaad 0.01 -> 0.65; Glenmoril Moonlight / Lb66Dark / Chaplain 0.01 -> 0.75, 0.5 / 0.6 -> 0.8 | house judgement (0.8 / 0.9 are not Requiem steps 0.75 / 0.7 / 0.5) | input | not itemised / 5 | Vigilant, Remiel, Dac0da, Unslaad, Glenmoril (non-AP perks) | n/a | med |
| PERK-AI-12 | Flat CalculateWeaponDamage **Add** values were *raised*, not shrunk: Glenmoril 20 -> 40 / 45 / 50, Dac0da 15 -> 80, Unslaad 20 -> 45 (~18 rows). "ARR shrinks perk numbers" is false for Add-mode | house judgement; no Requiem comparison done | input | ~18 / 3 | Glenmoril, Dac0da, Unslaad | n/a | low |
| PERK-AI-3 | Source-mod **NPC extra-damage perks neutralised to x1** (Vigilant `0D2F45` 2.5 -> 1; Molag `09D82F` 3 -> 1); the patch defines its own `VIG_REQ_` perks instead | house convention | input | 2 perks (pattern 10413, added half) / 1 | Vigilant only | n/a | med |
| PERK-AI-4 | Patches define their own `GLEN_` / `VIG_` / Unslaad `REQ_` perks to hand-carry what the build would stamp on AlreadyReqtified NPCs: boss resist (CalculateWeaponDamage x0.9 + ModIncomingDamage x0.7), ExtraDamage tiers (ModAttackDamage x4 / x6 / x2.5, each conditioned) | house convention (no Requiem twin: Requiem's ModAttackDamage perks >= 2 are only `REQ_Trait_Damage300` (3), NetchHunter (3), `MS02ForswornEscape` (10), `PowerOrcBerserk` (2)) | input | 10 defined perks / 3 | Glenmoril, Unslaad, Vigilant (split not itemised) | n/a | med |
| PERK-AI-5 | Dragon combat retune: CircleMult / FallbackMult / DefensiveMult -> 0; Hover / FlyingAttack / GroundAttack chances up; EquipmentScoreMult Shout / Magic / Ranged -> 4-10 | house convention (mod-ported tuning; no Requiem analog; not baked) | input | 118 rows incl. 30 bookkeeping, 88 field rows (19 families) / 1 | Dragon Tweaks master patch only | n/a | med |
| PERK-AI-6 | `Dragon_Default_MT` `01BCF9`: all run / walk directions 384 -> 650 (side moves from 0), rotate-in-place 33.75 -> 55 | house convention (companion to PERK-AI-5; Requiem touches only Horse_* MOVT) | input | 10 field rows (2 more are bookkeeping) / 1 | Dragon Tweaks only | n/a | med |
| PERK-AI-7 | KillMove idles: race-gate conditions from Dragonborn, BSAssets, BSHeartland, VioLens merged onto vanilla idles with OR flags (`100E95` KillMoveDraugrRoot00: 7 providers, 7 conditions) | **conflict-resolution merge** (compat; Requiem is not a provider of these idles) | input | 13 rows / 1 | Bruma master patch only | n/a | med |
| PERK-AI-8 | LOTD Visage perk conditions (17 rows per half) and an artificer fragment script (1 per half) re-emitted unchanged | noise | n/a | 36 rows / 1 | LOTD only | n/a | high |
| PERK-AI-9 | Class skill weights changed on two mod NPC classes (Inigo HeavyArmor 2 -> 0; Dac0da Sneak 0 -> 3) | noise (unclear, low) | n/a | 2 rows / 2 | Inigo 1, Dac0da 1 | n/a | low |
| PERK-AI-10 | Presentation (16 Perk rows / 5 plugins) and bookkeeping (30 CSTY + 4 IDLE rows) | noise | n/a | 50 rows | script-labelled | n/a | high |

## Upstream checklist (what a patch must carry)

1. Perk: re-value a source mod's entry-point numbers with Requiem's ladder in view (AP25 0.75 / AP30 0.7 / AP50 0.5 / AP75 0.25;
   damage 1.1 / 1.25 / 1.5 seen live) by reading the live Requiem perk, never by halving. Multipliers always shrank (46 / 46) but flat Add values rose; resistance perks were weakened to 0.8 / 0.9 off-step. Other fields (Add values, SkillUse, Lockpick, Stagger, crit Add, buy / sell) were edited too; the
   condition lists are left as the source has them.
2. Perk: a mod perk meant to hit NPCs for extra damage is neutralised (x1) and replaced by a patch-owned `*_REQ_` perk
   that goes into the AlreadyReqtified NPC's hand kit (FACTS section B).
3. CombatStyle / MovementType / Class / IdleAnimation: **no Requiem-derivable rule exists.** Edit only to resolve a compat
   conflict (KillMove gates) or to deliver a deliberate behaviour tweak (dragons).
4. For vanilla CSTY / MOVT / IDLE, compare against the live winner (Skyrim.esm or a compat mod), not Requiem.esp.

## Bake package (downstream)

**None identified.** No code line, Reqtificator config entry or RFTI-output proof exists for any row in these types, so
nothing is class `baked`. The only link to the build is PERK-AI-4: the hand-carried perks that the build would otherwise
stamp on non-AlreadyReqtified NPCs. That is an Npc matter and is recorded under Npc.

## Per-type detail

**Perk (415 rows, 15 plugins).** LOTD 118, Vigilant 94, Unslaad 77, Glenmoril 63, Remiel 14, Dibella 12, Dac0da 12, the
other 8 plugins <= 4 each. Effects removed / added (185 / 168 rows) are two halves of one edit, so ~175 edits. The reader
claim "AP perks uniformly halve" was **overturned** by the verifier (material error). Glenmoril originals
AP75 / 50 / 55 / 60 / 65 / 70 = 0.25 / 0.5 / 0.45 / 0.4 / 0.35 / 0.3 became 0.5 / 0.75 / 0.7 / 0.65 / 0.4 / 0.5.
Heart of Dibella `REQ_Boon_AgentOfDibella_Perk` `0727FB` merges Requiem / WAR 1.1 ModSpellMagnitude + ModAttackDamage with
the mod's per-school entries into 10 effects at 1.1 / 1.05 (Requiem scale kept). No row empties a perk to REQ_NULL.

**Class (2 rows, 2 plugins).** Inigo `InigoCombatThief` HeavyArmor weight 2 -> 0; Dac0da `zDcdEncClassArcanist` Sneak
0 -> 3 (both in the Reqtificated patches). Skill weights only drive auto-calc distribution, and AlreadyReqtified NPCs
hand-carry stats (FACTS section C), so the effect is probably nil. Class: unclear / noise, low confidence.

**CombatStyle (118 rows, 1 plugin).** All 19 changed-field families verified "agree" (medium). Records: csDragon `048C83`,
MQ305Alduin, MQ206 Alduin / Paarthurnax, csDragonCharGen*, csDragonDLC1Durnehviir. Largest: CircleMult 9, FallbackMult 9,
AvoidThreatChance 8, DefensiveMult 8, FlyingAttackChance 8. Two records (`1040D8`, `10FF34`) keep DefensiveMult but differ
in other fields. Intent is plain: dragons stay airborne and aggressive, never circle or defend, and favour shout / magic.

**MovementType (12 rows, 1 plugin).** One record (`Dragon_Default_MT` `01BCF9`); providers are Skyrim.esm and the patch.

**IdleAnimation (17 rows, 1 plugin).** 13 gate rows (9 added / 4 removed) plus 4 bookkeeping. Requiem.esp's 5 KillMove
overrides (`024CD4`, `0C1F1F`, `0E8458`, `100E8B`, `100F16`) are different idles; `Requiem - VioLens` wins those.

## Suspected bugs (for Aaron's review; not filed)

| Record / plugin | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|
| `zzzRevPerkArmorPiercing65` `012015:Glenmoril.esm` / Reqtificated - Glenmoril.esp | AP ModifyValue | 0.4 (orig 0.35) | Weaker than AP70, ideally a Requiem step | AP65 (0.4) is stronger than AP70 (0.5): ladder inversion. Patterns 17594 (removed) / 17595 (added) | outlier-bug |
| `zzzRevPerkArmorPiercing70` `012017:Glenmoril.esm` / same | AP ModifyValue | 0.5 (orig 0.3) | At most the AP65 value (0.4 or 0.45) | Same inversion; live read by the verifier. Patterns 17596 / 17597 | outlier-bug |
| `zzzRevPerkArmorPiercing60` Glenmoril.esm / same | AP ModifyValue | 0.65 (orig 0.4) | A Requiem step (0.7 or 0.5) | Off-scale; no Requiem 0.65 step. May be an intended in-between | outlier-bug (low) |
| `zzzCHcrExtraDamageNPCs` `0D2F45`, `zzzCHcrMolagExtraDamageNPCs` `09D82F` / Reqtificated - Vigilant.esp | ModAttackDamage | x1 (orig 2.5 / 3) | Intended? | Neutralised on purpose, `VIG_REQ_` perks replace; check they reach the NPCs | outlier-bug (unconfirmed) |
| `GLEN_REQ_ExtraDamage06` `000801` / Reqtificated - Glenmoril.esp | ModAttackDamage | 4 / 6 / 2.5 | Requiem's normal steps are 1.1-1.5 | 4 and 6 exceed every Requiem damage-perk step; exclusivity of the three conditioned entries not checked | outlier-bug (unconfirmed) |

No **suspected systemic** bug: the AP inversion is one plugin's ladder (60 / 65 / 70), not a repeated pattern.

## Open calls for Aaron

1. Vigilant NPC extra-damage perks neutralised to x1: intended no-op, with `VIG_REQ_` perks as the real effect?
2. Stacked ExtraDamage x4 / x6 / x2.5 (Glenmoril): intended on top of Requiem's 1.1-1.5 scale?
3. Glenmoril AP60 / 65 / 70 (0.65 / 0.4 / 0.5): fix to a monotonic ladder?
4. Dragon Tweaks (CombatStyle + MovementType): ported from a standalone mod or your own tuning? Needed to label house v ported.
5. Class weight edits (Inigo, Dac0da): deliberate, or incidental to the Reqtificated pass?

## Limits

- **Unsampled:** the CombatStyle, MovementType and Class families rest on the reader plus a verifier who read one or two live
  records each ("agree", no family-wide sample). The Perk effect families are `fixed` (checked), but the damage re-values
  were "not disproved", not proved against each Requiem step.
- **No held-out validation:** zero eval-reference rows in all five types; nothing here can be tested on held-out patches.
- **Dominant plugins:** every non-Perk type is one patch; do not generalise Dragon Tweaks or the Bruma KillMove merge to
  "ARR practice". LOTD is 28 % of Perk rows and mostly noise; Glenmoril carries the AP-ladder evidence.
- **Untouched records:** `stage2/out/UNTOUCHED.md` samples Perk only by name (Vigilant and LOTD perks "not read") and covers no
  CombatStyle, MovementType, IdleAnimation or Class record, so unpatched mod-added combat styles are unmeasured.
- **ksws03:** `Authoria - Reqtificated - ksws03.esp` (armour mod) has 9 records in the coverage file and no rows here; I found
  no evidence its load failure touches these types, but did not retest it.
- Glenmoril FormIDs come from pattern samples; Unslaad / Remiel edit FormIDs were not resolved individually.
