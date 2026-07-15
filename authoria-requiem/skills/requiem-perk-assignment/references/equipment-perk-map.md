# Equipment → perk map (the warrior/ranged axis)

Mined live from the ARR Requiem load order. Every perk and ladder value below is a **read** value
from Requiem's own actors; verify FormIDs live before writing — the map tells you which family to
read, the live comparable is always the authority.

## Table of contents
- [The comparable sources (actor taxonomy)](#the-comparable-sources-actor-taxonomy)
- [The core matrix: equipment → perk families](#the-core-matrix-equipment--perk-families)
- [Tier escalation (the ladder shape)](#tier-escalation-the-ladder-shape)
- [Perk FormID reference by tree](#perk-formid-reference-by-tree)
- [Per-weapon variation (anti-extrapolation)](#per-weapon-variation-anti-extrapolation)

## The comparable sources (actor taxonomy)

Requiem structures its combatants in three layers:

1. **`REQ_Bandit_Template_<Weapon>_<Base|01..06>`** — purpose-built, weapon-explicit perk ladders;
   **84 records**, the canonical comparable source. Defined in `Requiem.esp` (DualWield /
   ThrowingKnife / Quarterstaff in `Requiem - Weapons and Armor Redone.esp`; Trickster in the
   Roleplaying addon). Each carries a `REQ_Class_Bandit_<Weapon>` class and a rank-tiered outfit.
   `_Base` is a **perk-less skeleton** (class + null outfit) — the numbered tiers carry the perks;
   never read Base expecting the shared set.
2. **`REQ_LookTemplate_Enc<Family><NN><Role>…`** — appearance + base-combat templates for the
   vanilla encounter roster (Bandit/Forsworn/Thalmor/Guard), `Role ∈ {Melee1H, Melee, Missile,
   Magic, …}`. Their perk sets are split across `Requiem.esp` + Minor-Arcana addons — usable, but
   read the right defining plugin.
3. **`REQ_NULL_Enc…` leaf actors** — the null-stripped spawn leaves; each carries its own
   Perks/Class/Outfit.

The weapon-role classes (`CLAS`, `Requiem.esp` unless noted) — the weapon taxonomy the ladders hang
off:

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

## The core matrix: equipment → perk families

Every combatant's source-carried set = **[weapon-skill line] + [defence line keyed on shield &
armor class] + the universal floor**, at the depth of its tier. Read from tier-06 exemplars:

| Equipment | Weapon-skill tree | Defence tree(s) | Armor class |
|---|---|---|---|
| Sword+shield | One-Handed (SwordFocus) | Block (full) + Heavy Armor | Heavy |
| Axe+shield | One-Handed (WarAxeFocus, to rank 3) | Block + Heavy Armor (+PowerOfTheCombatant) | Heavy |
| Mace+shield | One-Handed (MaceFocus) *(ladder enumerated; perks not full-read — verify live)* | Block + Heavy Armor | Heavy |
| Greatsword (2H) | Two-Handed (GreatswordFocus) | Block + Heavy Armor | Heavy |
| Warhammer (2H) | Two-Handed (WarhammerFocus, to rank 3, +Cleave) | Block + Heavy Armor | Heavy |
| Battleaxe (2H) | Two-Handed (BattleaxeFocus, to rank 3, +Cleave) | Block + Heavy Armor (+PowerOfTheCombatant) | Heavy |
| Dual-wield 1H | One-Handed (**all three** Focus lines, +Flurry) | Evasion (+Lethality) — no Block, no Heavy | Light |
| Bow | Marksmanship (+QuickShot) | Evasion (+Dodge, +Lethality) + token Block | Light |
| Crossbow | Marksmanship (+RapidReload, no QuickShot) | Block (more than bow) + Evasion | Light |

The rules the matrix encodes:

1. **Weapon line follows the weapon.** 1H → `REQ_OneHanded_*`; 2H → `REQ_TwoHanded_*`;
   bow/crossbow → `REQ_Marksmanship_*`. Within a weapon class only the **Focus family swaps**
   (SwordFocus↔MaceFocus↔WarAxeFocus; GreatswordFocus↔WarhammerFocus↔BattleaxeFocus) — the
   mastery/strikes/defence shell is shared.
2. **Shield OR 2H weapon ⇒ Block tree.** Requiem's Block covers weapon-blocking, so 2H hitters get
   the full Block line with no shield.
3. **Armor class picks the survival tree.** Heavy → `REQ_HeavyArmor_*`, **no Evasion**; light or
   no shield → `REQ_Evasion_*` (+Lethality), **no Heavy Armor**.
4. **Dual-wielders carry all three 1H Focus lines** (they may roll any pair) and the light shape.
5. **Bow vs crossbow are structurally different:** bow = QuickShot + more Evasion; crossbow =
   RapidReload + more Block. Both keep a token 1H WeaponMastery for the melee sidearm.
6. **The universal floor:** `WeaponMastery1/2` + `HandToHand` appear across melee and ranged alike
   — the perk set is the **union over every weapon the actor actually carries**.
7. **Archers additionally get ranged trait perks** melee never carries:
   `REQ_Trait_Damage140_Bow 079354`, `REQ_Trait_ArmorPenetration50_Marksman 105F1F`.

## Tier escalation (the ladder shape)

Tiers are **strictly additive** — each tier's set contains the previous; perks are added, never
removed. Count grows ~linearly (~5 at tier 01 → ~18 at tier 06); Focus/mastery ranks deepen with
tier. Read example, the SwordShield ladder (source `Requiem.esp`):

| Tier | FormID | Level | # perks | Added at this rung |
|---|---|---|---|---|
| Base | 86837D | 1 | 0 | *(skeleton)* |
| 01 | 86837E | 3 | 5 | WeaponMastery1+2, ImprovedBlocking, Conditioning, HandToHand |
| 03 | 868380 | 10 | 12 | +SwiftStrikes, +SwordFocus1, +Block(PowerfulBashes, StrongGrip, ExperiencedBlocking), +HeavyArmor(CombatTraining, RelentlessOnslaught) |
| 06 | 899DD9 | 24 | 18 | +PowerfulStrike, +PowerfulCharge, +SwordFocus2, +Block(ElementalProtection, OverpoweringBashes), +HeavyArmor(Fortitude) |

Bow ladder, same shape: Bow_01 (`879914`, Lv3, 4 perks: WeaponMastery1, RangedCombatTraining,
ImprovedBlocking, Agility) → Bow_06 (`89ED40`, Lv24, 17 perks: full Marksmanship + Evasion).

**Level bands are family-specific; the tier index is universal.** Bandits run t1≈Lv3 → t6≈Lv24;
Forsworn t1=Lv7 → t6=Lv46 (~+8/tier). Match a new actor to the comparable by **tier index**
(intended power rank), not by absolute level.

## Perk FormID reference by tree

All `…:Skyrim.esm` unless noted — vanilla FormIDs whose live winners are Requiem's re-authored
perks (see `boundary-and-write-shapes.md` for the mechanism). Verify live before writing.

**One-Handed** — WeaponMastery1 `0BABE4`, WeaponMastery2 `079343`; SwordFocus1/2/3
`05F56F`/`0C1E90`/`0C1E91`; WarAxeFocus1/2/3 `03FFFA`/`0C3678`/`0C3679`; MaceFocus1/2
`05F592`/`0C1E92`; DaggerFocus1/2/3 `AD399A`/`AD3999`/`AD3998` (all `:Requiem.esp`); SwiftStrikes
`052D50`; PowerfulStrike `03AF81`; PowerfulCharge `0CB406`; Flurry1/2 `106256`/`106257`;
StormOfSteel `106258`; HandToHand `0AD7A3:Requiem.esp` (wins in WAR).

**Two-Handed** — GreatWeaponMastery1/2 `0BABE8`/`079346`; GreatswordFocus1/2/3
`03AF83`/`0C1E94`/`0C1E95`; WarhammerFocus1/2/3 `03AF84`/`0C1E96`/`0C1E97`; BattleaxeFocus1/2/3
`0C5C05`/`0C5C06`/`0C5C07`; QuarterstaffFocus1/2/3 `ADDFB0`–`ADDFB2:Requiem.esp`; Cleave `03AF9E`;
BarbaricMight `052D51`; DevastatingStrike `052D52`; DevastatingCharge `0CB407`.

**Marksmanship** — RangedCombatTraining `0BABED`; EagleEye `058F61`; PowerShot `058F62`; Ranger1
`058F63`; PreciseAim `07934A`; MarksmansFocus `103ADA`; QuickShot1 `105F19` (bow); PiercingShot
`105F1C`; RapidReload1 `17B8C1:Requiem.esp` (crossbow). Ranged traits: Damage140_Bow `079354`;
ArmorPenetration50_Marksman `105F1F`.

**Block** — ImprovedBlocking `0BCCAE`; PowerfulBashes `058F67`; StrongGrip `058F68`;
ElementalProtection `058F69`; OverpoweringBashes `05F594`; ExperiencedBlocking `079355`.

**Heavy Armor** — Conditioning `0BCD2A`; Fortitude `058F6C`; CombatTraining `058F6F`;
RelentlessOnslaught `07935E`; PowerOfTheCombatant `107832`.

**Evasion (light)** — Agility `0BE123`; Finesse `051B1B`; Dexterity `051B1C`; CombatReflexes
`051B17`; Dodge `079376`; WindWalker `105F22`; Lethality `18F5A8:Requiem.esp`.

WAR also defines weapon-specialization tricks on stronger actors (`REQ_OneHanded_Sword_
DisarmingBlade 000888`, `REQ_TwoHanded_Warhammer_Onslaught 00089B`, `REQ_Marksmanship_Sniper1/2`,
all `:Requiem - Weapons and Armor Redone.esp`) — read a same-weapon high-tier comparable to see
where they land.

## Per-weapon variation (anti-extrapolation)

Cross-weapon reads prove the shell is shared **but the variation is real** — derive from the
matching weapon's ladder, never a generic sibling:

- **AxeShield and Battleaxe** tier-06 add `REQ_HeavyArmor_PowerOfTheCombatant 107832` that the
  Sword/Greatsword tier-06 sets lack.
- **War Axe / Warhammer / Battleaxe reach Focus rank 3** by tier 06; Sword / Greatsword stop at
  rank 2.
- **Vanilla EditorID weapon roles were re-tasked:** `REQ_NULL_EncBandit06Melee2HNordM 03DE6A`
  carries a sword+shield perk set and `Class=REQ_Class_Bandit_SwordShield` despite "Melee2H" in
  its name. Trust the record's own Class + Outfit + Perks; the clean 2H exemplars are the
  `REQ_Bandit_Template_{Greatsword,Warhammer,Battleaxe}_*` ladders, not the `EncBandit*Melee2H*`
  leaves.
- Ladders not yet full-read (MaceShield, ThrowingKnife, Quarterstaff, Trickster; Thalmor/Guard
  families): the weapon-role axis is confirmed by class/EditorID enumeration, but read the actual
  perks live before deriving from them.
