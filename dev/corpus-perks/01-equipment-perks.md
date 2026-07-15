# Requiem Corpus â€” Equipment â†’ Combat-Perk Assignment

*Mined live through houseCARL against the ARR 2.0 Requiem load order (profile
"Authoria - Requiem Reforged", instance `E:\Skyrim Modding\ARR 2.0`).
FormIDs are `XXXXXX:Plugin.esp` (the defining master). Every perk/level below is a **read
value**; anything I did not read is marked **[INFERRED]**.*

This is the empirical base for the future `requiem-perk-assignment` skill.

---

## 0. The single most important finding â€” source vs. live winner

On this profile the load-order winner of almost every combatant NPC is
**`Requiem for the Indifferent.esp`** (RftI = the Reqtificator's generated output). The RftI
winner's `Perks` list is **~70â€“80 % Reqtificator boilerplate** that has *nothing* to do with the
actor's equipment. Example â€” `EncForsworn01TemplateMelee` (`023AA9:Skyrim.esm`): live winner = **56
perks**, of which only the **first 11** are the equipment-derived combat perks; items `[11..55]` are
universal injected infrastructure.

The boilerplate block (identifiable by prefix â€” **never hand-author any of these**; the Reqtificator
re-adds them at build time):

- `RFTI_All_*` â€” armor-penetration, resistances, stress/exhaustion, arrow recovery, armor weight,
  `RFTI_All_PlayableRace_<Race>`, `RFTI_All_ActorValueModifier`/`PowerModifier`
- `RFTI_Ench_*` â€” unique-artifact enchantment handlers (Wuuthrad, Ghostblade, Nettlebane, â€¦)
- `Nox_Perk_Mechanics_*` (Magic Redone: Physical/Destruction/Conjuration/â€¦)
- `REQ_*GM_*`, `REQ_IllusionGM_Sanctuary`, `RFTI_All_Magic_WardDamageReduction`, etc.

**Rule for the skill:** you author ONLY the combat-skill perks (the `REQ_OneHanded_*`,
`REQ_TwoHanded_*`, `REQ_Marksmanship_*`, `REQ_Block_*`, `REQ_HeavyArmor_*`, `REQ_Evasion_*`,
and school-perk lines). To read a comparable's *true* combat set, read the **source** version
(`plugin="Requiem.esp"` or the defining addon), OR take the leading combat perks off the live winner
and discard the `RFTI_/Nox_/â€¦GM_` tail. All examples in this doc use source-carried perks.

**Layering caveat:** the source combat perks are not always in one plugin. For the vanilla
`EncForsworn`/`EncBandit` encounter templates the set is split across `Requiem.esp` + the Minor-Arcana
addons (e.g. `EncForsworn01TemplateMelee` perks live in `Requiem - Minor Arcana - Forsworn.esp`, not
`Requiem.esp`). The **purpose-built `REQ_Bandit_Template_*` ladder (below) is the clean single-plugin
source** and the best comparable to derive from.

---

## 1. Actor taxonomy (how Requiem structures combatants)

Three layers, discovered by enumeration:

1. **`REQ_Bandit_Template_<Weapon>_<Base|01..06>`** â€” Requiem's **purpose-built, weapon-explicit
   perk ladders** (defined in `Requiem.esp`; DualWield/ThrowingKnife/Quarterstaff in
   `Requiem - Weapons and Armor Redone.esp`; Trickster in the Roleplaying addon). **84 records.**
   This is the canonical equipmentâ†’perk source. Each carries a `REQ_Class_Bandit_<Weapon>` class and a
   `REQ_Outfit_BanditArmor_<Heavy|Light>_Rank<n>[_Shield]` outfit.
2. **`REQ_LookTemplate_Enc<Family><NN><Role><Race><Sex>`** â€” appearance + base-combat templates for
   the vanilla encounter roster (Bandit/Forsworn/Thalmor/Guard/â€¦). `Role âˆˆ {Melee1H, Melee, Missile,
   Magic, â€¦}`.
3. **`REQ_NULL_Enc<Family><NN><Role><Race><Sex>`** â€” the null-stripped **leaf actors** that spawn in
   the world; each `Template`s a look-template and carries its own `Perks`+`Class`+`Outfit`.

Weapon-role **classes** (`CLAS`, all `Requiem.esp` unless noted) â€” the definitive weapon taxonomy:

| Class EditorID | FormID | Role |
|---|---|---|
| REQ_Class_Bandit_SwordShield | 85BCE3 | 1H sword + shield |
| REQ_Class_Bandit_MaceShield | 85BCE1 | 1H mace + shield |
| REQ_Class_Bandit_AxeShield | 85BCE2 | 1H war axe + shield |
| REQ_Class_Bandit_GreatSword | 86D2E8 | 2H greatsword |
| REQ_Class_Bandit_Warhammer | 86D2E7 | 2H warhammer |
| REQ_Class_Bandit_BattleAxe | 86D2E6 | 2H battleaxe |
| REQ_Class_Bandit_Bow | 879915 | bow |
| REQ_Class_Bandit_CrossBow / _CrossBowHeavy | 879916 / 879917 | crossbow |
| REQ_Class_Bandit_DualWield | 000B29:Requiem - Weapons and Armor Redone.esp | dual 1H |
| REQ_Class_Bandit_ThrowingKnife | 000B2A:Requiem - Weapons and Armor Redone.esp | thrown |
| REQ_Class_Bandit_Trickster | 8A8BE2 (Roleplaying addon winner) | dagger/stealth |

---

## 2. The equipment â†’ perk-family mapping (the core table)

Every combatant's combat-perk set = **[weapon-skill line] + [defence line keyed on shield & armor
class] + a small cross-pool of utility**. Derived from tier-06 reads (fullest signature):

| Equipment | Weapon-skill tree | Defence tree(s) | Armor class | Reads |
|---|---|---|---|---|
| **Sword+shield** | One-Handed (SwordFocus) | **Block (full) + Heavy Armor** | Heavy | SwordShield_06 |
| **Axe+shield** | One-Handed (WarAxeFocus, to rk3) | Block (full) + Heavy Armor (+PowerOfTheCombatant) | Heavy | AxeShield_06 `899DD3` |
| **Mace+shield** | One-Handed (MaceFocus) **[INFERRED â€” class read, perks not read]** | Block + Heavy Armor | Heavy | class only |
| **Greatsword (2H)** | Two-Handed (GreatswordFocus) | **Block + Heavy Armor** | Heavy | Greatsword_06 `899DDF` |
| **Warhammer (2H)** | Two-Handed (WarhammerFocus, to rk3, +Cleave) | Block + Heavy Armor | Heavy | Warhammer_06 `899DE2` |
| **Battleaxe (2H)** | Two-Handed (BattleaxeFocus, to rk3, +Cleave) | Block + Heavy Armor (+PowerOfTheCombatant) | Heavy | Battleaxe_06 `899DDC` |
| **Dual-wield 1H** | One-Handed (Sword+Mace+WarAxe focus, +Flurry) | **Evasion (+Lethality)** â€” no Block, no Heavy | Light | DualWield_06 `000B4C:â€¦WAR.esp` |
| **Bow** | Marksmanship (+QuickShot) | **Evasion (+Dodge, +Lethality)** + token Block | Light | Bow_06 `89ED40` |
| **Crossbow** | Marksmanship (+RapidReload, no QuickShot) | Block (more than bow) + Evasion | Light | Crossbow_06 `8A14DD` |

### Rules read out of the matrix

1. **Weapon line follows the weapon.** 1H â†’ `REQ_OneHanded_*`; 2H â†’ `REQ_TwoHanded_*`; bow/crossbow â†’
   `REQ_Marksmanship_*`. Within a class the **weapon-Focus family swaps by weapon and nothing else
   swaps**: SwordFocusâ†”MaceFocusâ†”WarAxeFocus (1H), GreatswordFocusâ†”WarhammerFocusâ†”BattleaxeFocus (2H).
   Verified by reading AxeShield vs SwordShield and Battleaxe vs Greatsword vs Warhammer â€” the
   mastery/strikes/defence shell is identical, only the Focus line differs.
2. **Shield OR 2H weapon â‡’ Block tree.** Requiem's Block skill covers weapon-blocking, so 2H heavy
   hitters get the full Block line (ImprovedBlockingâ†’PowerfulBashesâ†’StrongGripâ†’OverpoweringBashesâ†’
   ExperiencedBlocking) even with no shield.
3. **Armor class picks the survival tree.** Heavy â†’ `REQ_HeavyArmor_*` (Conditioningâ†’Fortitudeâ†’
   CombatTrainingâ†’RelentlessOnslaught[â†’PowerOfTheCombatant]); Light/no-shield â†’ `REQ_Evasion_*`
   (Finesse/Dexterity/Agility/Dodge/WindWalker/**Lethality**). Heavy actors get **no** Evasion; light
   actors get **no** Heavy Armor.
4. **Dual-wield & archers are the light shape:** weapon line + Evasion, `REQ_Evasion_Lethality`
   (`18F5A8:Requiem.esp`) is their signature. Dual-wielders carry **all three 1H focus lines** (they
   may roll any 1H pair) and no Block/Heavy.
5. **Bow vs crossbow differ structurally:** bow gets `REQ_Marksmanship_QuickShot1` (105F19) and more
   Evasion; crossbow gets `REQ_Marksmanship_RapidReload1` (17B8C1:Requiem.esp) and more Block, no
   QuickShot. Both keep a token One-Handed WeaponMastery for melee backup.
6. **Everyone melee gets** `REQ_OneHanded_WeaponMastery1/2` (`0BABE4`/`079343`) and
   `REQ_OneHanded_HandToHand` (`0AD7A3:Requiem.esp`) as the universal floor â€” even 2H and archer
   actors carry WeaponMastery.
7. **Pure-vanilla `Skyrim.esm` perks do not appear.** Every combat perk is a Requiem perk. The FormID
   is often `â€¦:Skyrim.esm` (Requiem overrides the vanilla perk record **in place**) but the EditorID is
   `REQ_*` and the winner is `Requiem.esp`/WAR.esp. Treat "defining plugin" as Requiem regardless of
   the FormID's master.

---

## 3. Tier / level escalation ladder (monotonic, additive)

Read from the dedicated **SwordShield** ladder (source `Requiem.esp`). Each tier's set strictly
**contains** the previous â€” perks are added, never removed. `_Base` is a **skeleton** (Class + null
outfit, **Perks absent**); the numbered tiers carry the perks.

| Tier | FormID | Level | # perks | Perks added at this rung (cumulative) |
|---|---|---|---|---|
| Base | 86837D | 1 | 0 | *(skeleton â€” Class=SwordShield, no perks, null outfit)* |
| 01 | 86837E | 3 | 5 | WeaponMastery1, WeaponMastery2, ImprovedBlocking, Conditioning, HandToHand |
| 03 | 868380 | 10 | 12 | +SwiftStrikes, +SwordFocus1, +Block{PowerfulBashes, StrongGrip, ExperiencedBlocking}, +HeavyArmor{CombatTraining, RelentlessOnslaught} |
| 06 | 899DD9 | 24 | 18 | +PowerfulStrike, +PowerfulCharge, +SwordFocus2, +Block{ElementalProtection, OverpoweringBashes}, +HeavyArmor{Fortitude} |

Bow ladder (same additive shape): **Bow_01** (`879914`, Lvl 3, **4 perks**: WeaponMastery1,
RangedCombatTraining, ImprovedBlocking, Agility) â†’ **Bow_06** (`89ED40`, Lvl 24, **17 perks**: full
Marksmanship + Evasion). 

**Escalation rules:**
- **Perk count grows ~ linearly with tier** (Bandit: ~5 â†’ ~18 across tiers 01â†’06).
- **Focus/mastery ranks deepen with tier:** SwordFocus1 (t3) â†’ +Focus2 (t6); axe/warhammer/battleaxe
  reach **Focus rank 3** by t6. WeaponMastery1+2 present from t1.
- **Defence trees fill in with tier:** Block/HeavyArmor start at one perk (t1) and add 2â€“3 more nodes
  by t6.
- **Level bands are family-specific, tier shape is universal.** Bandits: t1â‰ˆLv3 â€¦ t6â‰ˆLv24. Forsworn:
  t1=Lv7, t2=15, t3=23, t4=31, t5=39, t6=46 (~+8/tier). The **tier index**, not the absolute level,
  drives the perk ladder.

---

## 4. Hybrid & caster families (perks beyond the warrior axis)

**Forsworn** (`EncClassForsworn`, light/unarmored shaman-warriors) are hybrids â€” they carry the 1H
melee line **plus** magic-school perks and spells, which pure bandits never get:

- `EncForsworn__TemplateMelee` (source, cumulative): 1H (SwordFocus+WarAxeFocus **both**, since the
  outfit rolls either weapon; +SwiftStrikes/PowerfulStrike/Flurry/StormOfSteel) + Evasion
  (Finesse/CombatReflexes/WindWalker) + **Destruction** (Pyromancy1/2, Impact) + **Restoration**
  (ImprovedHealing, Respite, FocusedMind) + **Alteration** (MagicResistance1â†’3) + ImprovedBlocking.
  Ladder: 01=11 perks (Lv7) â†’ 03=9 â†’ 04=14 â†’ 05=16 â†’ 06=20 (Lv46). Also gains bound-weapon / conjure
  spells and rising `REQ_Trait_Tempering_*` armor-tempering effects up the ladder
  (Bandit_Heavy_Rank4 â†’ Guard_Captain â†’ Guard_Elite).
- `EncForsworn06TemplateMissile` (source, 13 perks): **Marksmanship** (PowerShot, Ranger1, QuickShot1)
  + `REQ_Trait_Damage140_Bow` (079354) + `REQ_Trait_ArmorPenetration50_Marksman` (105F1F) + 1H backup
  (SwordFocus, SwiftStrikes) + Evasion + Restoration. Archer role adds bow **damage/penetration
  trait perks** that melee never gets.
- `EncForsworn06TemplateMagic` (source, 11 perks): **no weapon focus at all** â€” pure
  Destruction (Electromancy1/2, ElectrostaticDischarge, Empower, Impact) + Conjuration
  (GatesOfOblivion, WatersOfOblivion) + Restoration (ImprovedHealing, ImprovedWards) + Alteration
  MagicResistance. Confirms the **Magic role drops the weapon+defence trees and substitutes the
  school perks matched to the actor's `ActorEffect` spell list**.

**Takeaway for the skill:** a caster/hybrid NPC's perks are derived from its **spell list (school +
tier)**, exactly as a warrior's are derived from its weapon â€” same live-analogy method, different axis.

---

## 5. Perk reference (EditorID â†’ FormID, by tree)

*All `â€¦:Skyrim.esm` unless noted; all are Requiem-overridden perks (winner Requiem.esp / WAR.esp).*

**One-Handed** â€” WeaponMastery1 `0BABE4`, WeaponMastery2 `079343`; SwordFocus1/2/3 `05F56F`/`0C1E90`/
`0C1E91`; WarAxeFocus1/2/3 `03FFFA`/`0C3678`/`0C3679`; MaceFocus1/2 `05F592`/`0C1E92`; SwiftStrikes
`052D50`, PowerfulStrike `03AF81`, PowerfulCharge `0CB406`, Flurry1/2 `106256`/`106257`, StormOfSteel
`106258`; HandToHand `0AD7A3:Requiem.esp`.

**Two-Handed** â€” GreatWeaponMastery1/2 `0BABE8`/`079346`; GreatswordFocus1/2/3 `03AF83`/`0C1E94`/
`0C1E95`; WarhammerFocus1/2/3 `03AF84`/`0C1E96`/`0C1E97`; BattleaxeFocus1/2/3 `0C5C05`/`0C5C06`/
`0C5C07`; Cleave `03AF9E`, BarbaricMight `052D51`, DevastatingStrike `052D52`, DevastatingCharge
`0CB407`. (Quarterstaff focus line exists: `ADDFB0-B2:Requiem.esp`.)

**Marksmanship** â€” RangedCombatTraining `0BABED`, EagleEye `058F61`, PowerShot `058F62`, Ranger1
`058F63`, PreciseAim `07934A`, MarksmansFocus `103ADA`, QuickShot1 `105F19` (bow), PiercingShot
`105F1C`, RapidReload1 `17B8C1:Requiem.esp` (crossbow). Ranged trait perks: Damage140_Bow `079354`,
ArmorPenetration50_Marksman `105F1F`.

**Block** â€” ImprovedBlocking `0BCCAE`, PowerfulBashes `058F67`, StrongGrip `058F68`, ElementalProtection
`058F69`, OverpoweringBashes `05F594`, ExperiencedBlocking `079355`.

**Heavy Armor** â€” Conditioning `0BCD2A`, Fortitude `058F6C`, CombatTraining `058F6F`, RelentlessOnslaught
`07935E`, PowerOfTheCombatant `107832`.

**Evasion (light)** â€” Agility `0BE123`, Finesse `051B1B`, Dexterity `051B1C`, CombatReflexes `051B17`,
Dodge `079376`, WindWalker `105F22`, Lethality `18F5A8:Requiem.esp`.

**Caster schools (hybrid/mage)** â€” Destruction: Pyromancy1/2 `0581E7`/`10FCF8`, Electromancy1/2
`058200`/`10FCFA`, ElectrostaticDischarge `0F3F0E`, EmpoweredDestruction `0153CF`, Impact `0153D2`.
Restoration: ImprovedHealing `0581F8`, Respite `0581F9`, FocusedMind `0581F4`, ImprovedWards `068BCC`,
NoviceRestoration `0F2CAA`. Alteration: MagicResistance1/2/3 `053128`/`053129`/`05312A`,
NoviceAlteration `0F2CA6`. Conjuration: GatesOfOblivion `0CB419`, WatersOfOblivion `0CB41A`.

---

## 6. Anti-extrapolation checks

- **Read Base + first + middle + last of the SwordShield ladder** (Base/01/03/06): monotonic-additive,
  no removals, no reordering surprises. Base carries **zero** perks (skeleton) â€” do **not** assume the
  Base template holds the shared set.
- **Read both ends of the Forsworn Melee ladder + two middles** (00/01/02/03/04/05/06): additive; the
  magic/spell and tempering-trait content grows with tier (not just weapon perks).
- **Cross-weapon reads (SwordShield vs AxeShield; Greatsword vs Warhammer vs Battleaxe):** the shell is
  shared, the Focus line swaps â€” **but minor per-weapon variation exists** and must not be extrapolated
  away: AxeShield & Battleaxe add `REQ_HeavyArmor_PowerOfTheCombatant` (107832) that Sword/Greatsword
  t6 lack; War Axe / Warhammer / Battleaxe reach Focus **rank 3** where Sword / Greatsword stop at
  rank 2. Derive the exact set from the *matching weapon's* comparable, not a generic sibling.
- **Outlier â€” vanilla "Melee2H" leaf mismatch:** `REQ_NULL_EncBandit06Melee2HNordM` (`03DE6A`) carries
  a **sword+shield** perk set and `Class=REQ_Class_Bandit_SwordShield` despite the "2H" in its
  EditorID â€” Requiem re-tasked the vanilla weapon-role split. **Trust the `Class` + `Outfit`, not the
  vanilla EditorID**, when reading a comparable. The clean 2H exemplars are the
  `REQ_Bandit_Template_Greatsword/Warhammer/Battleaxe_*` ladder, not the `EncBandit*Melee2H*` leaves.

---

## 7. Enumeration / read accounting

**Enumerated (true totals reported by the tool):**
- NPCs Requiem touches with `EncBandit` in EditorID: **466** (Requiem.esp 414 / Authoria merge 45 / RftI 7).
- `EncForsworn*`: 32 templates (+ boss variants in the Forsworn Minor-Arcana addon).
- `EncThalmor*`: 59 (Melee1H/Melee1HShield/Melee1HDual/Missile/Magic Ã— tier 01â€“06 Ã— M/F + boss).
- `EncGuard*`: 17 (Imperial/Sons look-templates + 2 base templates).
- `REQ_NULL_EncBandit*Melee2H*`: 71 (weapon-role Ã— race Ã— sex Ã— tier, incl. Berserk).
- `REQ_Bandit_Template_*` (purpose-built ladders): **84** â€” SwordShield/MaceShield/AxeShield,
  Greatsword/Warhammer/Battleaxe, Bow/Crossbow, DualWield/ThrowingKnife/Quarterstaff, Trickster,
  each Base+01..06.
- `NPC_*Template` overall (Requiem-touched): **810**.
- Weapon-role classes: 12 enumerated (table Â§1).
- `REQ_TwoHanded_*` perks: 25; plus full One-Handed/Marksmanship/Block/HeavyArmor/Evasion lines read.

**Full-read with perk lists (~34 actor-records):** Forsworn 00/01/02/03/04/05/06 Melee (live + source),
Forsworn 06 Missile, Forsworn 06 Magic; Bandit EncBandit 01 & 06 Melee1H templates; REQ_NULL Bandit 01
& 06 Melee2H leaves; dedicated ladder SwordShield Base/01/03/06, AxeShield 06, Greatsword 06, Warhammer
06, Battleaxe 06, Bow 01 & 06, Crossbow 06, DualWield 06.

**Not read (marked [INFERRED] where used):** MaceShield, ThrowingKnife, Quarterstaff, Trickster perk
lists (classes + ladder enumerated, perks not opened); Thalmor/Guard/Soldier perk lists (families
enumerated only â€” Thalmor confirms the Melee1H/Shield/Dual/Missile/Magic weapon-role axis by EditorID
but their perks were not full-read). No extrapolated value is presented as a read value anywhere above.
