# Caster & hybrid perk assignment

Mined live from the ARR Requiem load order. A caster's derivation runs on the **magic axis** the
way a warrior's runs on the weapon axis: read the spell kit, place the matching school nodes at the
matching threshold, add the shared survivability set — and skip the player-only gates.

## Table of contents
- [The two rules that shape everything](#the-two-rules-that-shape-everything)
- [The comparable sources](#the-comparable-sources)
- [The derivation: spell kit → perks](#the-derivation-spell-kit--perks)
- [The REQ magic perk tree (assignable nodes per school)](#the-req-magic-perk-tree-assignable-nodes-per-school)
- [Boss-tier and hybrid shapes](#boss-tier-and-hybrid-shapes)
- [Magicka and attributes](#magicka-and-attributes)
- [Outliers (anti-extrapolation)](#outliers-anti-extrapolation)

## The two rules that shape everything

1. **NPC casters never carry the `REQ_<School>_Mastery_NNN` gate perks** (Novice/Apprentice/Adept/
   Expert/Master). Those gate *player* spell learning. Not one sampled Requiem caster carries one —
   NPCs cast freely, balanced at build by the Reqtificator's persistent-spell rescaling. The
   assignable caster nodes are the **SubTree** flavour/damage/utility perks.
2. **The mastery-perk count and thresholds scale with tier:** 0 perks at tier 1 → ~6 at tier 4 →
   ~8 at tier 7 → 11–13 at boss/dragon-priest tier; MagicResistance climbs its 025→075 thresholds
   up the same ladder. A tier-1 mage with zero perks is correct, not incomplete.

## The comparable sources

Requiem defines no new caster NPCs — every caster ladder is a vanilla actor Requiem overrides.
Read the **tiered Template records** (they carry the balance data), not the race/gender leaves:

- **`EncWarlock<NN>Template<School>`** — the caster spine, 115 records: Fire/Ice/Storm/Necro/
  Conjurer × tiers 01–07 (+ boss templates). Standalone `EncNecromancer`/`EncConjurer` EditorIDs
  do **not** exist — those roles are Warlock template variants.
- **`EncDragonPriest{,Fire,Frost,Shock}`** + named bosses (Morokei `0F496C`, …) — boss tier.
- **`EncVampire*` / `DLC1EncVampireTemplate{,Magic,Missile}`** — the hybrid family.
- **`EncWitch*`** (15), **Hagraven** (`023AB0`) — low-tier and natural-weapon casters.
- The 41 NPC_ records defined in `Requiem - Magic Redone.esp` are **summonables** (conjured
  spirits/atronachs/decoys), not casters — out of scope here (they belong to the magic skill).

Shared caster wiring, constant across the Warlock ladder: CombatStyle `csWarlock 044CD0:Skyrim.esm`
(dragon priests `csDragonPriest 0BB365`, hagravens `csHagraven 02A54E`); class scales tier-1
`EncClassBanditMelee 01CE17` → tier-4+ `CombatMageElemental 01317A` / `CombatMageConjurer 01CE14`;
fixed `[NpcLevel]` levels.

## The derivation: spell kit → perks

**The actor's spell list drives its perk selection** — confirmed across the fire ladder:

| Actor | Lvl | Mastery perks (source) | Damage node | MagRes tier | Highest spell |
|---|---|---|---|---|---|
| EncWarlock01TemplateFire `044CD7:Skyrim.esm` | 1 | **0** | — | — | Flames |
| EncWarlock04TemplateFire `045CA0:Skyrim.esm` | 35 | 6 | Pyromancy1 (025) | MagRes1 (025) | Fireball |
| EncWarlock07TemplateFire `1091A9:Skyrim.esm` | 46 | 8 | Pyromancy1+2 (050) | MagRes3 (075) | Incinerate |
| EncDragonPriestFire `02025A:Skyrim.esm` | 50 | 11 | Pyromancy2 + Cremation | MagRes1 | Incinerate |
| Morokei `0F496C:Skyrim.esm` | 120 | 13 | Pyromancy2 + Cremation | MagRes1 | Incinerate + lich summon |

So for a **new caster**: read its schools + highest spell tier → place the matching SubTree
damage/utility node(s) at the corresponding threshold + the shared survivability set → leave the
rest to the Reqtificator.

**The shared caster survivability set** (appears regardless of school):
`Restoration_Healing_025_ImprovedHealing 0581F8`, usually `_050_Respite 0581F9`, an Alteration
MageArmor proxy (actors carry the legacy `REQ_LEGACY_MageArmor50 0D799A` / `MageArmor70 0D799B`),
`Alteration_MagicResistance` at the tier's threshold (`053128`/`053129`/`05312A`),
`Alteration_Metamagic_050_Stability 0581FC`, `Restoration_Protection_050_ImprovedWards 068BCC`.

**Multi-school casters get both trees.** The tier-7 necromancer (`1091AB`, 11 source perks) casts
frost destruction *and* reanimation → Cryomancy1+2 **and** Necromancy `0581DD` + DarkInfusion
`0581DE`, plus Empower CognitiveFlexibility1 `185736` and the shared set.

## The REQ magic perk tree (assignable nodes per school)

`REQ_<School>_<SubTree>_<threshold>_<name>`; threshold = the skill level (000/025/050/075/100) a
player would need. Winning definitions live in `Requiem - Magic Redone.esp`; **reference the origin
FormID** (mostly `:Skyrim.esm`). The `_Mastery_` gate rows are listed only so you can recognize and
skip them.

- **Destruction** — Mastery gates (skip): `0F2CA8`/`0C44BF`/`0C44C0`/`0C44C1`/`0C44C2`.
  Pyromancy1/2 `0581E7`/`10FCF8`, Cremation `0F392E`, FireMastery `179121:Requiem.esp`;
  Cryomancy1/2 `0581EA`/`10FCF9`, DeepFreeze `0F3933`, FrostMastery `179123:Requiem.esp`;
  Electromancy1/2 `058200`/`10FCFA`, ElectrostaticDischarge `0F3F0E`, LightningMastery
  `179124:Requiem.esp`; Empower: EmpoweredDestruction `0153CF`, Impact `0153D2`. Magic Redone adds
  Arcane/Entropic/BloodMagic/Battlemage specialist subtrees.
- **Conjuration** — gates (skip): `0F2CA7`/`0C44BB`/`0C44BC`/`0C44BD`/`0C44BE`.
  Necromancy `0581DD`, Ritualism `17911B:Requiem.esp`, DarkInfusion `0581DE`; Bound:
  MysticBinding `0640B3`, MysticInfusion `0D799E`, MysticEmpowerment `0D799C`; Daedric:
  DaedricBinding `105F30`, GatesOfOblivion `0CB419`, WatersOfOblivion `0CB41A`; Empower:
  EmpoweredConjuration `0153CE`, CognitiveFlexibility1/2 `185736`/`185737:Requiem.esp`.
- **Restoration** — gates (skip): `0F2CAA`/`0C44C7`/`0C44C8`/`0C44C9`/`0C44CA`.
  ImprovedHealing `0581F8`, Respite `0581F9`; ImprovedProtection `005FBE:Requiem.esp`,
  ImprovedWards `068BCC`; FocusedMind `0581F4`, PowerOfLife `0A3F64`; ImprovedTurning
  `005FBD:Requiem.esp`, MysticTurning `0581E4`.
- **Alteration** — gates (skip): `0F2CA6`/`0C44B7`/`0C44B8`/`0C44B9`/`0C44BA`.
  MagicResistance1/2/3 `053128`/`053129`/`05312A`; ImprovedMageArmor `0D7999` (actors carry the
  `REQ_LEGACY_MageArmor50/70` proxies `0D799A`/`0D799B`); Stability `0581FC`; MagicalAbsorption
  `0581F7`.
- **Illusion** — gates (skip): `0F2CA9`/`0C44C3`/`0C44C4`/`0C44C5`/`0C44C6`. SubTrees
  (Perceptual/Emotional/Delusive/ShadowMagic) + EmpoweredIllusion `0153D0` — read an
  illusion-casting comparable live; none was full-read in the mining pass.

## Boss-tier and hybrid shapes

**Dragon-priest recipe** (read on Fire priest + Morokei): the shared survivability set +
GatesOfOblivion (they summon) + school damage nodes at 050 + Empower CognitiveFlexibility1.
Morokei adds `REQ_Trait_SummonBonus 0D5F1C:Requiem.esp` and CognitiveFlexibility2 — boss uniques
deepen the same shape rather than changing it.

**Hybrids stack; neither family is dropped.** `DLC1EncVampireTemplateMagic 003372:Dawnguard.esm`
combines school mastery perks + melee/tempering handlers + the vampire spell kit. Its sibling
`DLC1EncVampireTemplateMissile 003374` carries **zero** mastery perks — same race, same band.
**Perk assignment follows the template's role, not the race.**

## Magicka and attributes

Casters dump the attribute budget into `Configuration.MagickaOffset` (tier-7 fire mage: Magicka
+200, Health +100, Stamina 0 → computed 500/575/50). The offset climbs with tier alongside the
perk count (one tier read directly; verify the comparable's own offset live). Spell-power scaling
beyond that is the Reqtificator's persistent-spell rescaling at build — never per-record magnitude
edits, and another reason NPCs need no gate perks. Setting the offset itself is
`requiem-npc-patching`'s field; this skill only notes what the comparable carries.

## Outliers (anti-extrapolation)

- **Morokei follows his spells, not his template:** a shock-priest template carrying Pyromancy
  perks and a fire kit. Boss perks match the hand-authored spell kit — read the actual
  `ActorEffect`.
- **Hagraven (`023AB0`): 2 mastery perks at level 35** — a natural-weapon caster is deliberately
  under-perked. Match the archetype's real shape; don't formula it up.
- **A few boss templates are source-won** (e.g. `EncWarlock05TemplateBossFire 0E0FCE` wins in
  `Requiem.esp`, not the Reqtificator output) — their live winner shows raw mastery perks with no
  chassis. Don't read one as a "finished" build, and don't be surprised by chassis-free winners.
- **Tier 1 = zero perks is a real read**, not a data gap.
