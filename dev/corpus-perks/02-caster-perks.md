# Corpus mining — Caster & hybrid perk assignment in Requiem

Empirical base for a future `requiem-perk-assignment` skill. Mined live through houseCARL against
the ARR 2.0 instance (`Authoria - Requiem Reforged - NSFW Profile`, 3558 plugins resolved).
FormIDs are `XXXXXX:Plugin.esp`. "Source" = the plugin's own record (`plugin=Requiem.esp`);
"winner" = live load-order winner, almost always `Requiem for the Indifferent.esp` (the Reqtificator
output, abbreviated **RftI** below). All reads verified live; inferences are marked **[INFER]**.

---

## HEADLINE RULE (the load-bearing finding)

**A caster's perks live in TWO disjoint layers with TWO different owners:**

1. **School-mastery layer — SOURCE-carried, hand-authored, tier-scaled.** Requiem.esp (and its
   boss records) hand-place a small list of `REQ_<School>_<SubTree>_<NNN>_<Name>` player-tree perks
   — the flavour/damage/utility nodes that match the actor's schools and tier. Count grows with
   tier: **0 perks at tier 1 → 6 at tier 4 → 8 at tier 7 → 11–13 at boss/Dragon-Priest tier.**
   This is the ONLY perk layer a patch author authors.

2. **Generic mechanics chassis — Reqtificator-STAMPED at build, identical on every actor.** RftI
   appends a fixed ~45-perk bundle of `RFTI_All_*`, `Nox_Perk_Mechanics_*`,
   `RFTI_All_PlayableRace_*`, and `RFTI_Ench_*` handler perks to every processed NPC. **A patch
   author NEVER hand-places these** — the Reqtificator adds them, exactly as it does the generic
   spell-rescaling and exhaustion ActorEffects.

**Proof (EncWarlock01TemplateFire `044CD7:Skyrim.esm`):** source `Perks = (absent)`; RftI winner
carries 45 perks, ALL of them the generic chassis (0 mastery perks — a tier-1 mage earns none).
**Proof (EncWarlock07TemplateNecro `1091AB:Skyrim.esm`):** source carries exactly 11 mastery perks;
RftI winner carries those SAME 11 (re-ordered) + 46 generic = 57. The Reqtificator is purely
additive over the source mastery list.

Corollary rules:
- **NPC casters do NOT carry the `REQ_<School>_Mastery_NNN` tier-gate perks** (Novice/Apprentice/
  Adept/Expert/Master). Those are player-only spell-unlock gates. Not one of the ~20 actors read
  carried a `_Mastery_` perk. NPCs cast freely and are balanced by `RFTI_NPC_PersistentSpellRescaling`.
- **The mastery-perk set expresses "tier" through the threshold suffix**, not just count:
  MagicResistance climbs `025 → 075` as the fire ladder goes tier-4 → tier-7.
- The whole `REQ_*` magic perk tree's winning definition is **`Requiem - Magic Redone.esp`** (it
  overrides every Skyrim.esm/Requiem.esp perk). When placing a perk, the FormID token to reuse is
  the origin one (e.g. `0581E7:Skyrim.esm`); the live winner just resolves through Magic Redone.

---

## Actor architecture

Requiem **defines no new caster NPCs of its own** — every caster ladder is a vanilla
Skyrim.esm/Dawnguard.esm/Dragonborn.esm actor that Requiem.esp overrides and RftI re-stamps. The
41 NPC_ records **defined in** `Requiem - Magic Redone.esp` are all **summonables** (conjured
spirits/atronachs/undead, illusion decoys) — the *targets* of conjuration/illusion spells, not
caster actors. They are out of scope for caster-perk assignment (relevant only to the magic skill).

Caster actors are **template chains**: concrete race/gender actors (`EncWarlockFire01HighElfF`)
set `Template → EncWarlock00Template` and inherit stats/perks/spells from the tiered
`EncWarlockNN Template<School>` records. **The Template records carry the balance data** — read
those, not the leaf race/gender actors.

Shared caster wiring (constant across the Warlock ladder):
- **CombatStyle** `044CD0:Skyrim.esm` (`csWarlock`) on the entire Warlock ladder; Dragon Priests use
  `csDragonPriest` `0BB365`, Hagravens `csHagraven` `02A54E`.
- **Class scales with tier:** tier 1 = `EncClassBanditMelee` `01CE17`; tier 4+ =
  `CombatMageElemental` `01317A` (destruction) / `CombatMageConjurer` `01CE14` (necro/conjurer).
- **Configuration.Level** is a fixed `[NpcLevel]` (not Pc-mult): 1 / 35 / 45 / 46 up the ladder.
- Every winner carries the RftI ActorEffect tail: `RFTI_NPC_PersistentSpellRescaling` `AD3977`,
  `RFTI_All_BaseAttackSpeed` `03E556`, `RFTI_All_CrossbowStagger` `AE3597`,
  `RFTI_All_ExhaustionController` `062275`. Source records do NOT carry these — RftI adds them.

---

## Census (families enumerated)

| Family | Query | Records Requiem.esp touches | Notes |
|---|---|---|---|
| Warlock ladder | `EncWarlock*` | **115** | The caster spine: Fire/Ice/Storm/Necro/Conjurer × tiers 01–07, each with a Template, Boss-Template, and concrete race/gender leaf actors |
| Witch | `EncWitch*` | 15 | Fire/Ice/Storm × tier 01–02; many leaves won by `Authoria - Master Patch - NPCs Merge.esp` |
| Vampire | `*Vampire*` | 32 | Tiered `EncVampire00–06Template` + `EncVampireNNTemplateBoss` + DLC1 `DLC1EncVampireTemplate{,Magic,Missile}` (hybrids) |
| Dragon Priest | `DragonPriest` | 15 | `EncDragonPriest{,Fire,Frost,Shock}` + named bosses (Morokei, Rahgot, Krosis, Otar…) |
| Hagraven | `Hagraven` | 4 | `EncHagraven` + 3 placed instances |
| Magic Redone NPCs | defined_in MR | 41 | **All summonables** (spirits/daedra/undead/illusions), NOT casters |

`EncNecromancer`/`EncConjurer`/`EncWizard` as standalone editorids **do not exist** — necromancers
and conjurers are folded into the Warlock template chain as `EncWarlockNNTemplateNecro` /
`…TemplateConjurer`.

---

## The REQ magic perk tree (full ladder, per school)

Every perk below is a `REQ_<School>_<SubTree>_<threshold>_<name>` player-tree node. Threshold =
the skill level (000/025/050/075/100 → Novice/Apprentice/Adept/Expert/Master) at which a *player*
would unlock it. **NPCs receive the SubTree flavour nodes matching their spells + a shared caster
survivability set; they skip the `_Mastery_` gate nodes.** Winning definitions all in Magic Redone.

### Destruction (34 perks)
- **Mastery gate (NPCs skip):** `_Mastery_000_Novice` `0F2CA8` · `_025_Apprentice` `0C44BF` ·
  `_050_Adept` `0C44C0` · `_075_Expert` `0C44C1` · `_100_Master` `0C44C2`
- **Pyromancy (fire):** `_Pyromancy_025_Pyromancy1` `0581E7` · `_050_Pyromancy2` `10FCF8` ·
  `_050_Cremation` `0F392E` · `_100_FireMastery` `179121`
- **Cryomancy (frost):** `_Cryomancy_025_Cyromancy1` `0581EA` · `_050_Cyromancy2` `10FCF9` ·
  `_050_DeepFreeze` `0F3933` · `_100_FrostMastery` `179123`
- **Electromancy (shock):** `_Electromancy_025_Electromancy1` `058200` · `_050_Electromancy2`
  `10FCFA` · `_050_ElectrostaticDischarge` `0F3F0E` · `_100_LightningMastery` `179124`
- **Empower:** `_Empower_025_EmpoweredDestruction` `0153CF` · `_050_Impact` `0153D2`
- **Specialist trees (Magic Redone):** Arcane 025/050/100, Entropic 025/050/100, BloodMagic
  025/050/100, Battlemage 050 (DestructiveBlow, ImprovedCloaks, RuneMastery)

### Conjuration (26 perks)
- **Mastery gate (skip):** `000` `0F2CA7` · `025` `0C44BB` · `050` `0C44BC` · `075` `0C44BD` · `100` `0C44BE`
- **Necromancy:** `_025_Necromancy` `0581DD` · `_050_Ritualism` `17911B` · `_050_Thrallmaster`
  `0060AE` · `_075_DarkInfusion` `0581DE`
- **Bound weapons:** `_Bound_025_MysticBinding` `0640B3` · `_050_MysticInfusion` `0D799E` ·
  `_075_MysticEmpowerment` `0D799C` · `_100_MysticDisruption` `17911A`
- **Daedric:** `_025_DaedricBinding` `105F30` · `_050_GatesOfOblivion` `0CB419` ·
  `_050_DarkChanneling` `0060AD`... `_075_WatersOfOblivion` `0CB41A`
- **Spirit (MR):** `_025_SpiritCalling` `006048` · `_050_SpiritualBond`/`SpiritGuardian` ·
  `_075_AncientSpirits` `AD385A`
- **Soul Magic (MR):** `_025_SoulMagic` `0060AD`... · `_075_Necropotence` `00604C`
- **Empower:** `_025_EmpoweredConjuration` `0153CE` · `_050_CognitiveFlexibility1` `185736` ·
  `_100_CognitiveFlexibility2` `185737` (the empower/dual-school nodes the task asked about)

### Restoration (20 perks)
- **Mastery gate (skip):** `000` `0F2CAA` · `025` `0C44C7` · `050` `0C44C8` · `075` `0C44C9` · `100` `0C44CA`
- **Healing:** `_Healing_025_ImprovedHealing` `0581F8` · `_050_Respite` `0581F9`
- **Protection/Wards:** `_Protection_025_ImprovedProtection` `005FBE` · `_050_ImprovedWards` `068BCC`
- **Recovery:** `_Recovery_025_FocusedMind` `0581F4` · `_050_PowerOfLife` `0A3F64` ·
  `_075_EssenceOfLife` `17E062`
- **Turning:** `_025_ImprovedTurning` `005FBD` · `_075_MysticTurning` `0581E4`
- **Specialist (MR):** Venomancy 025/075, Heliomancy 025/075
- **Empower:** `_Empower_025_EmpoweredRestoration` `0153D1`

### Alteration (22 perks)
- **Mastery gate (skip):** `000` `0F2CA6` · `025` `0C44B7` · `050` `0C44B8` · `075` `0C44B9` · `100` `0C44BA`
- **MagicResistance (tier ladder):** `_025_MagicResistance1` `053128` · `_050_MagicResistance2`
  `053129` · `_075_MagicResistance3` `05312A`
- **MageArmor:** `_025_ImprovedMageArmor` `0D7999` · `_050_VersatileArmor` `21792A`
  (legacy proxies `REQ_LEGACY_MageArmor50` `0D799A` / `MageArmor70` `0D799B` are what actors carry)
- **Metamagic:** `_050_Stability` `0581FC` · `_075_MetamagicalThesis` `21792B` ·
  `_100_MetamagicalEmpowerment` `21792C`
- **MagicalAbsorption:** `_100_MagicalAbsorption` `0581F7`
- **Specialist (MR):** Kinetomancy 025/050(NPC)/075, Transmutation 025/075, WeatherMagic 050/075
- **Empower:** `_Empower_025_EmpoweredAlteration` `0153CD`

### Illusion (19 perks)
- **Mastery gate (skip):** `000` `0F2CA9` · `025` `0C44C3` · `050` `0C44C4` · `075` `0C44C5` · `100` `0C44C6`
- **SubTrees:** Perceptual 025/050, Emotional 025/050(Mesmerize/Domination), Delusive
  050/075(Curse/ObliterateTheMind), ShadowMagic 050/075(MasterOfShadows), Overmind 050
- **Empower:** `_Empower_025_EmpoweredIllusion` `0153D0`

---

## School → perk mapping, read from real actors (source/mastery layer only)

Every caster carries a **shared survivability set** regardless of school, plus a **school-specific
damage node** matching its destruction element (or its conjuration sub-type). The shared set:
`Restoration_Healing_025_ImprovedHealing`, usually `_050_Respite`, `Alteration_MageArmor` (proxy),
`Alteration_MagicResistance_NNN`, `Alteration_Metamagic_050_Stability`,
`Restoration_Protection_050_ImprovedWards`.

### Fire-mage tier ladder (single school, watch the count + threshold climb)

| Actor (FormID) | Lvl | # mastery perks (source) | School-damage node | MagicResistance tier | Highest dmg spell |
|---|---|---|---|---|---|
| EncWarlock01TemplateFire `044CD7` | 1 | **0** | — | — | Flames (Dest1) |
| EncWarlock04TemplateFire `045CA0` | 35 | 6 | Pyromancy1(025) | MagRes1(025) | Fireball (Dest3) |
| EncWarlock07TemplateFire `1091A9` | 46 | 8 | Pyromancy1(025)+Pyromancy2(050) | MagRes3(075) | Incinerate (Dest4) |
| EncWarlock05TemplateBossFire `0E0FCE`† | 45 | 8 | Pyromancy1+2 | (via ImprovedWards) | Fireball (Dest3) |
| EncDragonPriestFire `02025A` | 50 | 11 | Pyromancy2+Cremation(050) | MagRes1(025) | Incinerate |
| MG07Labyrinthian **Morokei** `0F496C` | 120 | 13 | Pyromancy2+Cremation | MagRes1 | Incinerate + Ghostly Frost Lich summon |

† `0E0FCE` is **won by Requiem.esp, not RftI** (a source-defined boss template RftI didn't
re-process in this order) — so its live winner shows the 8 raw mastery perks with **no** generic
chassis. A rare case where the live winner == source; do not treat its 8-perk list as a
"finished" build.

### Other schools (source/mastery perks read live)

- **Storm/Electromancer (EncWarlock07TemplateStorm `1091AC`, 8 perks):** Electromancy1(025) +
  Electromancy2(050) + the shared survivability set (Healing, Respite, Stability, MagRes3,
  ImprovedWards, MageArmor70). Spells: Chain Lightning + Thunderbolt + Lightning Cloak.
- **Necromancer (EncWarlock07TemplateNecro `1091AB`, 11 perks):** Necromancy(025) +
  DarkInfusion(075) + **Cryomancy1+2** (it casts Ice Sphere/Icy Spear) + Empower
  CognitiveFlexibility1(050) + shared set. Spells: Reanimate Revenant/Dread Zombie, Conjure
  Spectral Warhound/Warrior, Turn Greater Undead. **Perks track BOTH its schools** (frost
  destruction + reanimation conjuration).
- **Conjurer (EncWarlock07TemplateConjurer `1091A8`):** Class `CombatMageConjurer`; spells Conjure
  Storm Atronach, Dremora Archmage, Command/Expel Daedra + Chain Lightning. **[INFER]** its source
  mastery set centres on Daedric (GatesOfOblivion) + Empower, mirroring the Dragon-Priest pattern.
- **Hagraven (`023AB0`, only 2 mastery perks):** Pyromancy2(050) + ImprovedHealing(025), despite
  level 35 — leans on natural weapons + Fireball/Firebolt, minimal perk investment. **Outlier: a
  caster can be deliberately under-perked when its kit is claw-plus-a-few-spells.**

### Dragon Priest (boss-tier signature set)

`EncDragonPriestFire` (11) and Morokei (13) share a fixed boss recipe: MagicResistance1,
`REQ_LEGACY_Recovery50`, ImprovedHealing+Respite, Stability, ImprovedWards, **GatesOfOblivion**
(Daedric conjuration), MageArmor50, Pyromancy2+Cremation, **Empower CognitiveFlexibility1**. Morokei
adds `REQ_Trait_SummonBonus` `0D5F1C` + **CognitiveFlexibility2(100)**. **Outlier:** Morokei is
templated off `EncDragonPriestShock` yet carries **Pyromancy** perks and an Incinerate/fire kit —
a hand-authored boss whose perks match its *actual spells*, not its template's element.

---

## Hybrid actors (melee + magic)

Vampires are the canonical hybrids. `DLC1EncVampireTemplateMagic` `003372:Dawnguard.esm` (Level 52,
Class `EncClassBanditWizard`, CombatStyle `csVampireMelee1H_Lvl1`) carries **80 perks** on its RftI
winner — the largest read. It combines:
- **Magic-school mastery perks** (destruction frost/shock, the shared survivability set),
- **the generic chassis** (~45),
- **vampire-trait & melee handler perks** (Guard-Captain tempering trait via ActorEffect, weapon
  perks from the WAR chassis),
- **vampire spell kit** ActorEffects: Vampiric Drain, Vampire's Shadow, Vampire Claws (unarmed),
  Frost Cloak, Ice Sphere, Chain Lightning, Revenant reanimation, Summon Frost Atronach.

Rule for hybrids: **the melee family and the school family stack — neither is dropped.** The melee
combat style + weapon/tempering perks coexist with the full mage survivability + school-damage set.
Contrast `DLC1EncVampireTemplateMissile` `003374` (source, Requiem.esp winner): `Perks = (absent)`,
PcLevelMult level, pure vampire-trait ActorEffects — a **non-caster** vampire carries zero mastery
perks. So within one family, the "Magic" template is a caster and the "Missile"/melee templates are
not; perk assignment follows the template's role, not the race.

---

## Spell-kit ↔ perk correlation (confirmed)

The actor's **ActorEffect spell list drives its perk selection**: perks are placed for the schools
whose spells the actor actually casts, and the school-damage node's threshold tracks the actor's
highest spell tier.
- Fire01 casts only Flames (Dest1) + Steadfast Ward → 0 damage perks.
- Fire04 casts up to Fireball (Dest3) → Pyromancy1(025).
- Fire07 casts up to Incinerate (Dest4) → Pyromancy1(025)+Pyromancy2(050).
- Necro07 casts frost (Ice Sphere/Icy Spear) AND reanimation → Cryomancy1+2 AND Necromancy+DarkInfusion.

So the derivation for a NEW caster is: **read its spell schools + highest spell tier → place the
matching SubTree damage node(s) at the corresponding threshold + the shared survivability set →
leave the rest to the Reqtificator.**

## Magicka & attribute handling

Magicka is NOT a per-record `MagickaOffset` on the NPC root — it lives at
**`Configuration.MagickaOffset`**, and it scales with caster tier. Fire07 (`1091A9`):
`Configuration.MagickaOffset = 200`, `HealthOffset = 100`, `StaminaOffset = 0` — a caster dumps its
attribute budget into magicka. Computed `PlayerSkills.Magicka = 500`, `Health = 575`, `Stamina =
50`. **[INFER]** MagickaOffset climbs with tier alongside the perk count (tier-1 templates carry a
much smaller offset). Base spell scaling on top of this is handled at runtime by the
`RFTI_NPC_PersistentSpellRescaling` ActorEffect, not by per-record magnitude edits — which is why
NPCs need no `_Mastery_` gate perks to cast high-tier spells.

---

## Anti-extrapolation checks & outliers

- **First/middle/last of the Warlock-Fire family** read (tier 1/4/7) — confirms monotonic perk-count
  and threshold climb; tier 1 genuinely carries **zero** mastery perks (not a read error — source is
  `(absent)`).
- **Morokei outlier:** Shock-priest template, Pyromancy perks + fire kit. Boss perks follow the
  hand-authored spell kit, not the template element.
- **Hagraven outlier:** only 2 mastery perks at level 35 — deliberate under-investment for a
  natural-weapon caster.
- **`0E0FCE` boss won by Requiem.esp (not RftI):** live winner lacks the generic chassis. Any audit
  that samples "the live winner" must expect a few source-won boss templates that look
  perk-light because RftI never re-stamped them in this load order.
- **Vampire hybrids** prove role-not-race: Magic template = 80-perk caster, Missile template =
  0 mastery perks, same vampire race and level band.

---

## Accounting

**Enumerated (census queries):** Warlock 115, Witch 15, Vampire 32, DragonPriest 15, Hagraven 4,
Magic-Redone-defined NPC_ 41 (all summonables), plus full PERK-tree sweeps: Destruction 34,
Conjuration 26, Restoration 20, Alteration 22, Illusion 19 = **121 perk records enumerated**.

**Full-read with resolved perks (~22 actors):** Warlock templates Fire01/04/07, Ice01/07,
Storm01/07, Necro01/07, Conjurer01/07 (12, batch, live winner); source (mastery-only) reads for
Fire01, Fire04, Fire07, Storm07, Necro07, BossFire05; Dragon Priest Fire (winner+source), Morokei
(winner+source), Hagraven (winner+source), VampireMagic (winner), VampireMissile (source); one full
field dump (Fire07) for magicka. Both **live winner AND Requiem.esp source** read for the key
actors (Fire01, Necro07, DragonPriestFire, Morokei, Hagraven) to isolate source-carried vs
RftI-added — the isolation that produced the headline rule.

**Reads vs inference:** All perk lists, spell kits, class/combatstyle/level, MagickaOffset, and the
source-vs-winner diff are **direct reads**. Marked **[INFER]**: Conjurer07 source mastery set (not
source-read — inferred from its winner + Dragon-Priest analogy), MagickaOffset tier-scaling curve
(one data point read; trend inferred), and the specialist-tree → actor mapping for MR sub-trees not
seen on the sampled actors.
