# NPC stage-2 family verification

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The stage-2 census (`../census/stage2/GEAR-NPC-S2.md` conv
#3 Level, #4 skills, #5 offsets, #7 ActorEffect) labelled these four NPC families "requiem-aligned" but never sampled
#5 or #7 and never named a comparable for #3 or #4. Stage 1's versions of the same families were mostly mis-stated.
This round re-derived each family from the raw dumps and live reads. Scripts and data stay local
(`census/splits/npcv_*`).*

## Scope

- **Corpus:** every stage-2 NPC record (1,052) in the 5 patches that carry NPCs. Per family:

  | Family | Records changed | Bruma | Others |
  |---|---|---|---|
  | Level (incl. PcLevelMult → fixed) | 420 | 415 | Moonpath 5 |
  | Skill values | 505 | 504 | Moonpath 1 |
  | Health / Magicka / Stamina offsets | 367 | 365 | Moonpath 2 |
  | Skill offsets | 26 | 26 | — |
  | ActorEffect (not reorder-only) | 232 | 231 | IDE 1 |

- **No spread outside Bruma exists to sample.** All four families are ≥ 98.8 % `Authoria - Master Patch -
  Bruma.esp`. So every test below runs on the **whole population**, not a sample. Inside Bruma it is broken down by
  source plugin (BSHeartland 830, ARA Bruma NPC replacer 81, BSAssets 77, DBM 4, RSChildren 3) and by role
  (creature / encounter, named combat, named civilian). A stratified read of 36 records per family (6–14 strata)
  sits in `census/splits/npcv_sample.txt`.
- **Provenance caveat (split 1).** Bruma is one of the old authoria-requiem skills' own live runs (2026-07). These
  families show what that doctrine did, not an independent Heisen convention.
- **The Requiem side.** Live reads at epoch `e2-58972ca8618e79ea`:
  - all 2,702 NPCs Requiem.esp touches, at Requiem.esp's own values;
  - all 6,699 NPCs defined in the five vanilla masters + Requiem, at the live winner;
  - the Skyrim.esm originals of Requiem's 1,755 Skyrim NPC overrides, to see how Requiem itself converts levels.
- **Build mechanics.** Bruma carries no `AE3B83`, so the Reqtificator runs on every one of these NPCs. It never
  writes Level, skills, offsets or ActorEffect on an override (FACTS C). The values below are therefore what the
  game uses, unless a template mask hides them. `ActorPerksFromRules` / `ActorSpellsFromRules` return early on the
  **SpellList flag alone** (split 1). This doesn't touch these four families, but an ActorEffect edit on a
  SpellList-flagged NPC does nothing, because the template supplies the spells.

## Answer: one rule, the census saw its by-products

**Bruma clones a Requiem analogue's whole stat block onto each NPC.** That block is level, the AutoCalc flag, all 18
skill values, all 18 skill offsets, the H/M/S offsets and the base H/M/S, plus that analogue's ActorEffect kit.
- 467 / 541 changed NPCs match **one Requiem-side NPC on every one of those 43 fields**.
- 38 more match on the skill vector, with the level or offsets hand-tuned.
- The analogue is picked by role, often across species: Ayleid zombie → draugr, ogre → giant, goblin → riekling,
  scamp → flame atronach, sheep → goat, wereboar → werewolf, Bruma bandit → `REQ_LookTemplate_EncBandit*` of the
  same tier, named townsperson → Belethor.

| Family | Census said | Real convention | Requiem / MR evidence | ARR evidence | Verdict |
|---|---|---|---|---|---|
| Level (#3) | PcLevelMult → fixed level; AutoCalc kept on most | Level **and the AutoCalc flag are copied from the analogue**. AutoCalc follows the donor (514 / 520), not the source (276 / 520) | The donor is a Requiem-side record: 2,633 / 2,702 of Requiem's NPCs are fixed-level | Level = donor level: **367 / 420**. The rest are hand-set (goblins, guards L27, legionnaires L35, Moonpath 4) | **WRONG as stated** (the real rule is the clone). Requiem-sourced, with three defect groups (below) |
| Skills (#4) | 5 floor + tier values on combat skills | **The analogue's exact 18-value vector** | 472 / 505 final vectors exist verbatim on a Requiem.esp NPC | Vector = donor: **482 / 505**. The "5 floor" (493 / 505) is Requiem's own vector shape, carried along | **CONFIRMED** requiem-aligned (values are Requiem's own). The mechanism is the clone, not a floor rule |
| Offsets (#5) | H/M/S and offsets hand-set, mostly inert | **H/M/S offsets and skill offsets copied from the analogue** | Requiem writes HealthOffset on 211 of its 536 unmasked AutoCalc NPCs, so it treats offsets as live under AutoCalc | H/M/S offsets = donor: **343 / 367**. Skill offsets = donor: **26 / 26** (warlocks Conj 30; Thalmor magic 50/50/70/70; scamps 50–60) | **CONFIRMED** requiem-aligned. "Mostly inert" isn't supported: only Stats-masked rows are inert, and none are here |
| ActorEffect (#7) | One Tempering trait per armoured fighter, REQ kits for mages, Vampire_NPC | **Added spells come from the analogue's kit** (Requiem.esp or its live winner, incl. MR / Requiem addons). Encounter NPCs take the kit whole; named casters keep their own spells and gain the analogue's trait. REQ_NULL stubs are dropped | 334 / 389 added ids are on the donor. 51 more are REQ_Trait_* or other Requiem / addon spells. 4 are not Requiem | Rule holds **228 / 232**. Kit == donor exactly 133 / 176 with a donor. **68 of the 232 sit on SpellList-flagged NPCs (inert)** | **CONFIRMED** requiem-aligned. The census line describes the result, not the rule |

## Defects the clone produced (the three Level groups)

| Group | n | What | Requiem says | Read |
|---|---|---|---|---|
| Named **combat** NPCs given Belethor's civilian block (L4, AutoCalc) | 44 of the 84 Belethor clones | Legate Arilus Quenti (SoldierImperialNotGuard), Viscount Marcius Carvain (CombatWarrior1H, source L20), Count Desilus Carvain, Botram the Hammer (L13 → 4), the BrumaMS11 assassins Olaf / Caius / Servus, Mirich Ran (CombatAssassin, ×4.0 → 4) | Requiem's own PcLevelMult → fixed conversions of combat-class NPCs: median **35**, only 3 / 98 at ≤ 5 (Ralof / Hadvar 1.0 → 30) | The analogue was picked by "named NPC", not by class. Bug (PLAUSIBLE) |
| Masked donor, so its own (inert) numbers were copied | 103 donors are Stats-templated; 10 differ in level from what Requiem actually uses | Bruma vampires 00–05 L20–40 from `REQ_LookTemplate_Vampire0X*`. Bloodscale L7 from `EncForsworn01TemplateBerserk` | Requiem's effective levels: the LookTemplate vampires inherit EncVampire0X (L45–49); the forsworn berserker inherits EncForsworn01TemplateMelee (L25) | The clone read the donor's own row, not its template chain. Bug (PLAUSIBLE). 93 / 103 masked donors carry the same level as their template, so harmless there |
| Goblin tiers flattened | 35 BSK goblin templates | Every tier 00–06 → **L6** AutoCalc (source L1–51). Skills were cloned from riekling tiers 01–04, but the riekling level (18–27) wasn't. All BossMagic → L30 | Requiem's rieklings are L18 / 21 / 24 / 27, AutoCalc | With AutoCalc on, the cloned skills are inert, so every goblin tier fights as an L6 goblin. Live winner confirmed (Bruma wins 602608, 60265F at L6). Bug (PLAUSIBLE) |

Named **civilians** on Belethor (40 / 84: farmers, citizens, vendors, trainers) are defensible. Belethor's L4 is
vanilla, and Requiem keeps it. L4 is also the modal level of vanilla fixed-level civilians (140 at L4 in the live
winners). But when Requiem itself converts a PcLevelMult civilian, it uses 12–45 (Vekel, Mikael, Avrusa 25;
Arnskar, Roggi 20; n = 10), never 4. Which reading applies is a question for the list author (Q1).

## Domain specifics for the skills

- **Teach the clone, not its shape.** Pick a Requiem analogue by role and tier. Copy its level, AutoCalc flag,
  skills, skill offsets, H/M/S offsets and ActorEffect kit. Then check four things:
  1. **Resolve the donor's template chain first.** If the donor is Stats-masked, copy its template's values.
  2. **A named NPC takes an analogue of its class**, not a civilian default. Combat classes → a Requiem combat NPC of
     similar tier (Requiem's conversions centre on 25–35).
  3. **Keep the source tier ladder.** A family of tiers (01–06) needs rising levels. If the donor family has fewer
     tiers, interpolate, don't collapse.
  4. **A cloned skill vector does nothing under AutoCalc.** If you clone AutoCalc, the level and class carry the
     strength.
- **ActorEffect:** encounter templates take the analogue's whole kit. Named casters keep their own spells and add
  the analogue's `REQ_Trait_*` (tempering tier, vampire, armour). Drop `REQ_NULL_*` stubs (45 removals, mostly
  `REQ_NULL_crMagickaRecovery02`). Skip ActorEffect edits on SpellList-flagged NPCs, since the template supplies the
  spells.
- **Moonpath** (the only non-Bruma rows): 4 level writes on untemplated NPCs (Skullcat 35, Peryite monk 20, small
  Therium 15, Sload 50). None is a clone. The Peryite monk's vanilla analogue `DA13PeryiteMonk` is still PcLevelMult
  1.2 in Requiem. Everything else Moonpath did was Workflow A templating (Stats masked), so these families don't
  apply there.

## Open residual

- 74 changed Bruma NPCs have no full 43-field donor:
  - 13 match on level, skills and offsets, and differ only in base H/M/S (inert under AutoCalc, harmless);
  - 38 match on skills only, with level / offsets hand-tuned upward for stronger variants (dire wolf L10 on ice-wolf
    skills, hunting dogs L10 on EncDog skills, unicorn L50 on spirit-horse skills, minotaurs L50 / 100 on giant /
    frost-giant skills with HO 1000 / 2500);
  - 23 have no donor at all (guard / soldier templates and Stormcloak reinforcements at L27, legionnaires L35,
    vampire 06 bosses L55, goblin BossMagic L30, Bloodscale magic L7).
  These look like judgement on top of the clone. Nothing tested ties them to one Requiem record.
- `CYRDartaqto` is the one unmasked Bruma NPC still on PcLevelMult. The other 29 LevelMult NPCs are Stats-masked
  (inert).
- The engine's handling of H/M/S offsets under AutoCalc wasn't tested in game. "Live" rests on Requiem writing them
  there.

## Hypothesis log

**Level** (N = 420)

| # | Hypothesis | Result |
|---|---|---|
| L1 | Level = the stat-block donor's level | **Kept**: 367 / 420 |
| L2 | Source fixed level kept | Dead: 0 / 238 |
| L3 | Fixed → fixed within ±20 % of source | Dead: 49 / 238 |
| L4 | PcLevelMult → fixed = k × mult for one k | Dead: best k 26 / 181 |
| L5 | Level is one Requiem uses for that race | Weak: 257 / 420. Explained by L1 (the donor's race often differs: 199 / 480 same race) |
| L6 | Tier ladder preserved | 316 / 377; the breaks are goblins (5 families), wereboar, dremora warlock, werewolf → defect group 3 / donor ladders |
| L7a | AutoCalc kept from source | Dead: 276 / 520 |
| L7b | AutoCalc taken from donor | **Kept**: 514 / 520 |
| L8 | Belethor's L4 block only on non-combat classes | Dead: 40 / 84 → defect group 1 |
| L9 | Requiem converts PcLevelMult NPCs to a low fixed level | Dead: combat median 35 (3 / 98 ≤ 5); civil median 35 (0 / 28 ≤ 5) |

**Skills** (N = 505)

| # | Hypothesis | Result |
|---|---|---|
| S1 | Vector == donor vector | **Kept**: 482 / 505 |
| S2 | Floor of 5 | 493 / 505, a by-product of S1 (Requiem's vectors have it) |
| S3 | Non-combat skills all 5 | Dead: 103 / 505 |
| S4 | Source's top skill kept (scaled source) | Dead: 226 / 505 |
| S5 | Live (no AutoCalc, no Stats mask) | 236 / 505; 269 are on AutoCalc NPCs (inert, as on the donors) |
| S6 | Top skill ≤ 100 | 505 / 505 (trivial) |
| S7 | Some Requiem.esp NPC carries the exact vector | 472 / 505 |
| S8 | Changed only on AutoCalc NPCs (the census's "inert" reading) | Dead: 269 / 505 |

**Offsets** (N = 367 H/M/S, 26 skill)

| # | Hypothesis | Result |
|---|---|---|
| O1 | H/M/S offsets == donor | **Kept**: 343 / 367 |
| O2 | HealthOffset = 10 × level | Dead: 26 / 367 |
| O3 | Offsets only raised | Dead: 249 / 367 |
| O4 | Multiples of 5 | 367 / 367 (trivial; Requiem's are too) |
| O5 | MagickaOffset > 0 exactly on casters | 315 / 367, explained by O1 |
| O6 | Offsets written on AutoCalc NPCs | 128 / 367; Requiem does the same on 211 of its own |
| O7 | Skill offsets == donor | **Kept**: 26 / 26 |
| O8 | Skill offsets only on magic skills + Sneak | 26 / 26 (trivial, from O7) |

**ActorEffect** (N = 232)

| # | Hypothesis | Result |
|---|---|---|
| A1 | Final kit == donor kit (Requiem.esp or live winner) | 133 / 176 with a donor |
| A2 | Added ids ⊆ donor kit (own spells kept) | +35 → 168 / 176 |
| A3 | Every add is donor / REQ_Trait_* / Requiem-addon | **Kept**: 228 / 232 (4: ForswornOakfleshWolf ×3, DLC1RevenantLeftHand50) |
| A4 | Removals are REQ_NULL / LEGACY stubs | 45 / 203 removed ids; the rest are swapped kit spells (136) and source abilities (22, e.g. CYRcrAbVampire) |
| A5 | One Tempering trait per armoured fighter (census) | The visible half of A2. Not a rule of its own |
| A6 | Edits limited to live (non-SpellList) NPCs | Dead: 68 / 232 sit under a SpellList flag (inert) |
| A7 | IDE Steinolf 5 vanilla spells removed | Inert: SpellList-templated onto `REQ_Vigilant_Loc_Ruunvald_Charmed` |
| A8 | Vampires' non-donor adds (SMTV_*) come from a non-Requiem source | Dead: `Requiem_VampireCollection.esp`, the live winner of Requiem's LookTemplate vampires |
