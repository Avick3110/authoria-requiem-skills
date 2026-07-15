# Perk-space map & the Reqtificator-assignment boundary

*Corpus-mined live through houseCARL against the ARR 2.0 Requiem load order (profile
"Authoria - Requiem Reforged", instance `E:\Skyrim Modding\ARR 2.0`, 3558
active plugins). Empirical base for the planned `requiem-perk-assignment` skill â€” a skill that
**assigns existing Requiem perks to NPCs and never authors a new PERK record.***

---

## 0. Headline

- A patch that wants an NPC to fight like a Requiem actor **assigns vanilla Skyrim FormIDs**
  (`0BABE4:Skyrim.esm` = Armsman) â€” the live conflict winner is Requiem's re-authored version
  (`REQ_OneHanded_WeaponMastery1`, 25% dmg, via WAR). **Verified.** Never copy a REQ editorid onto
  a fresh FormID; the whole assignable space already exists, mostly under vanilla FormIDs.
- Requiem source NPCs carry a **tiny hand-picked set of player-tree perks** (4â€“5). The Reqtificator
  **inflates that to ~50 at build time**, stamping every game-mechanic (`RFTI_All_*`) perk. That
  before/after is the assignment boundary, proven below with real FormIDs.
- The skill's write set = **player-tree perks only**. The `RFTI_All_*` / `RFTI_Player_*` /
  `Nox_Perk_Mechanics_*` machine perks are **forbidden output** â€” the Reqtificator owns them.

---

## 1. The perk space â€” enumerated totals

### 1a. What Requiem.esp touches (`type=PERK plugins=[Requiem.esp]`, group_by=defined_in)

`599` PERK records touched, split by defining master:

| Defining plugin | Count | What it is |
|---|---:|---|
| Skyrim.esm | 367 | vanilla perks Requiem **overrides in place** (the player trees + vanilla mechanics) |
| **Requiem.esp** | **162** | **new REQ_\* / RFTI_\* perks defined by Requiem** |
| Dawnguard.esm | 35 | DG perks Requiem overrides |
| Dragonborn.esm | 32 | DB perks Requiem overrides |
| HearthFires.esm / Update.esm / USSEP | 1 each | odd overrides |

The 367 Skyrim.esm + 67 DLC overrides are **the assignable player trees** â€” a patch references the
**vanilla** FormID and the live winner delivers Requiem's edit (see Â§2a).

### 1b. Requiem's own new perks live across several plugins

The 162 defined in Requiem.esp are re-overridden downstream (group_by=winner): winners split across
17 plugins â€” 56 still win in Requiem.esp, 28 in the Resist-and-Regen-tweak patch, 28 in Magic Redone,
23 in WAR, 9 in Races Redone, etc. **The origin FormID is `â€¦:Requiem.esp` regardless of who wins** â€”
always reference the origin FormID; the resolver returns the winner.

Additional new PERK defined **outside** Requiem.esp (still core Requiem framework):

| Plugin | New PERK defined | Contents |
|---|---:|---|
| **Requiem - Magic Redone.esp** | **50** | `Nox_Perk_Mechanics_*` (per-school caster mechanics) + REQ magic tier perks |
| **Requiem - Weapons and Armor Redone.esp (WAR)** | **28** | weapon-specialization tricks (Disarming Blade, Armsbreakerâ€¦), `RFTI_All_WAR_Mechanics`, and **`RFTI_Player_UnarmedMechanics` (000A84)** + `REQ_OneHanded_HandToHand` unarmed line â€” the known "HandToHand REQ perk lives in a WAR plugin" fact, **confirmed** |
| Requiem - Resist and Regen Tweak.esp | 15 | physique/trait perks |
| Requiem - Special Feats.esp | 42 | feats |

### 1c. Whole-order PERK census (`type=PERK`, group_by=defined_in)

`1876` PERK across **168 defining plugins**. Requiem-family is ~300; the rest are vanilla/DLC/CC and
**mod-shipped** perks (see Â§3). Largest non-Requiem definers: Apocalypse (57), LotD (56), Subclasses
of Skyrim (51), Biggie Traits (44), Sacrilege (34), Manbeast (31), Shadow of Skyrim (18).

---

## 2. The assignable-vs-forbidden split (verified live)

### 2a. PLAYER-TREE perks â€” the source-carried, assignable set

These are the perk-tree nodes. **A multi-rank line is NOT one FormID at Rank N â€” it is a chain of
separate single-rank FormIDs linked by `NextPerk`,** each `NumRanks=1`. An NPC carries the *specific
rank FormIDs* it has "spent points" on.

Schema (full read of `AD399A REQ_OneHanded_DaggerFocus1`): fields are `Playable`, `Hidden`,
`NextPerk`, `NumRanks`, `Name`, `Description`, `Effects[]`, `Conditions[]`. Player-tree markers:
`Playable=True`, `Hidden=False`, has a `Name`, usually a `NextPerk` chain.

**Vanillaâ†’Requiem delta proof** â€” `0BABE4:Skyrim.esm` read with conflict_tree:

- Vanilla: EditorID `Armsman00`, Name "Armsman", "One-Handed weapons do 20% more damage",
  Effect `ModAttackDamage Ã—1.2`.
- **Live winner** (Requiem - Weapons and Armor Redone.esp, override_depth 4): EditorID
  `REQ_OneHanded_WeaponMastery1`, Name "Weapon Mastery", "â€¦swing one-handed weapons or fistsâ€¦
  [25% more damage]", Effect `ModAttackDamage Ã—1.25`, `NextPerk=079343:Skyrim.esm`, `NumRanks=1`.
- 4 plugins touch it (Skyrim.esm â†’ Requiem.esp Ã—1.2 â†’ Minor Arcana Roleplaying â†’ WAR Ã—1.25 winner).
- **This is the mechanism:** assign `0BABE4:Skyrim.esm`, the actor gets WAR's 25% Weapon Mastery.

Sample of the assignable player-tree space (all confirmed live, FormID â†’ live winner editorid):

| Assign this FormID | Live winner editorid | Tree |
|---|---|---|
| `0BABE4:Skyrim.esm` | REQ_OneHanded_WeaponMastery1 | One-Handed |
| `079343:Skyrim.esm` | REQ_OneHanded_WeaponMastery2 | One-Handed (rank 2 = separate FormID) |
| `0BABED:Skyrim.esm` | REQ_Marksmanship_RangedCombatTraining | Archery |
| `0BCCAE:Skyrim.esm` | REQ_Block_ImprovedBlocking | Block |
| `0BCD2A:Skyrim.esm` | REQ_HeavyArmor_Conditioning | Heavy Armor |
| `0BE123:Skyrim.esm` | REQ_Evasion_Agility | Evasion (light) |
| `0AD7A3:Requiem.esp` | REQ_OneHanded_HandToHand | unarmed (REQ-defined, wins in WAR) |
| `AD399A/AD3999/AD3998:Requiem.esp` | REQ_OneHanded_DaggerFocus 1/2/3 | Dagger (REQ 3-rank chain) |

REQ-defined tree nodes (from the 162): `REQ_Destruction_FireMastery` (179121),
`REQ_TwoHanded_MightyStrike` (182F9B), `REQ_HeavyArmor_BattleMage` (187ED2),
`REQ_Alchemy_Regeneration` (1CD48F), `REQ_Speech_Leadership` (427139), the `REQ_TwoHanded_Quarterstaff
Focus1/2/3` chain (ADDFB0-2), etc. WAR adds the weapon-specialization tricks (`REQ_OneHanded_Sword_
DisarmingBlade` 000888, `REQ_TwoHanded_Warhammer_Onslaught` 00089B, `REQ_Marksmanship_Sniper1/2`).

### 2b. REQTIFICATOR-ASSIGNED mechanics perks â€” FORBIDDEN output

These are `Hidden=True`, `Name` absent, no `NextPerk`, editorid `RFTI_All_*` / named `GM:` / `REQ GM:`.
Verified sample (read from Requiem.esp):

| FormID | editorid | Name (GM = game mechanic) |
|---|---|---|
| AD394A | RFTI_All_ArmorPenetration_StandardAttacks | "GM: ArmorPenetration - Standard Attack Penetration" |
| AD394B | RFTI_All_ArmorPenetration_PowerAttacks | "GM: â€¦ Power Attack Penetration" |
| AD3948/AD394C/D/E | RFTI_All_ArmorPenetration_Resistances_Ranged/Pierce/Slash/Blunt | resistance GMs |
| AD3A34 | RFTI_All_ArmorWeight | (Hidden, unnamed) |
| AD3A35 | RFTI_All_ArrowRecovery | (Hidden, unnamed) |
| 962798 / 962799 | RFTI_All_PoisonRescaling / RFTI_All_AbsorbRescaling | "REQ GM: Poison/Absorb â€¦ Rescaling" |
| 6B9709 / 703B25 / 95FFFB / 755649 | RFTI_All_Stress_AttackStaminaCost / ArmoredCasting / ExhaustionPenalties / MassEffect | "REQ GM: â€¦" stress line |
| 031285 | RFTI_Trait_ZombieUndead_Draugr (src `RFTI_Trait_Draugr`) | racial incoming-damage trait |

Also machine-owned: `Nox_Perk_Mechanics_{Physical,Conjuration,Destruction,Restoration,Illusion,
Alteration,Enchanting}` (Magic Redone), `RFTI_All_WAR_Mechanics` (WAR 000853), `RFTI_All_
PlayableRace_*` (all 10 races), the `RFTI_Ench_*` artifact perks, and `RFTI_All_ActorValueModifier`
/ `â€¦PowerModifier` (Skyrim.esm 0CF788 / 0A725C).

#### The boundary before/after proof â€” REQ_Bandit_Template_SwordShield_01 (`86837E:Requiem.esp`)

Read the **same NPC** two ways:

- **Source** (`plugin="Requiem.esp"`): **5 perks**, ALL player-tree:
  `079343` WeaponMastery2, `0BABE4` WeaponMastery1, `0BCCAE` Improved Blocking, `0BCD2A` Conditioning,
  `0AD7A3` Hand to Hand. Every one `Rank=1`. **Zero `RFTI_All_*` mechanics perks present.**
- **Live winner** (`plugin="Requiem for the Indifferent.esp"` = Reqtificator output): **50 perks** â€”
  the identical 5 **plus 45 machine-stamped**: all 6 armor-penetration GMs (AD394A/B/C/D/E, AD3948),
  ArrowRecovery (AD3A35), ArmorWeight (AD3A34), Absorb/Poison Rescaling (962799/962798), the 4 Stress
  GMs (6B9709/703B25/95FFFB/755649), WardDamageReduction (682FB5), `RFTI_All_WAR_Mechanics` (000853),
  all 7 `Nox_Perk_Mechanics_*`, all 10 `RFTI_All_PlayableRace_*`, the `RFTI_Ench_*` artifact perks
  (Bolar's Oathblade, Ghostblade, Nettlebane, Trollsbane, Wuuthrad, DawnguardShield), and the skill/AV
  modifiers (0CF788, 0A725C, 0A725C).

**Precise boundary: source 5 â†’ build 50.** The skill writes into the 5-perk source lane; the +45 are
the Reqtificator's exclusive stamp and must never appear in a hand-authored patch. Second NPC checked
(`879914 REQ_Bandit_Template_Bow_01`, source): 4 perks â€” WeaponMastery1, RangedCombatTraining,
Improved Blocking, Agility â€” same pattern.

### 2c. PLAYER-EXCLUSIVE skill perks â€” exist, never on an NPC

The `RFTI_Player_*` skill perks are the player's control-center perks. Verified they resolve and are
**absent from every Requiem source NPC**: a reverse lookup â€”
`type=NPC_ defined_in=[Requiem.esp] references=[all 8 exclusive FormIDs]` â€” returned **0 matches**.

| FormID | editorid | Name |
|---|---|---|
| AD3A2F | RFTI_Player_Alchemy | (winner Alchemy Redone) |
| AD3A30 | RFTI_Player_Enchanting | (winner Magic Redone) |
| 0BCC58 | RFTI_Player_Lockpicking | "REQ GM: Control Center for lock picking" |
| AD3A31 | RFTI_Player_Pickpocket | (winner Stealth Redone) |
| AD3A51 | RFTI_Player_Sneak | (Requiem.esp) |
| AD3A2E | RFTI_Player_SneakAttack | (winner WAR Stealth patch) |
| AD3A2D | RFTI_Player_Speech | (winner Trade and Barter) |
| 8C8ED3 | RFTI_Player_Tempering | "REQ GM: size-based rescaling" |

**Rule: the skill never writes any `RFTI_Player_*` perk onto an NPC.** (`RFTI_Player_UnarmedMechanics`
000A84 in WAR is likewise player-side.)

---

## 3. Mod-shipped PERK disposition corpus

Sampled three non-Requiem definers to calibrate the disposition rule ("rebalance only when obvious,
else flag to the user"):

| Mod (defines) | Sampled perk | Flags | Disposition |
|---|---|---|---|
| **Apocalypse - Magic of Skyrim** (57) | `WB_StrengthOfEarth_Perk` (0A12C0) â€” Hidden=True, no desc; `WB_ConjureXivilaiLord_Perk`, `WB_InvisibleManager_Perk`, `WB_TestPerk` | Hidden, spell-mod internal plumbing that **drives a mod-specific spell mechanic**. Several already carry a `Requiem - Apocalypse - Magic Redone Patch.esp` override. | **Leave alone.** Not NPC-assignable content; the spell mod attaches these itself via its MGEFs. Don't touch unless the community patch is absent and a spell is provably broken â†’ then it's a magic-skill job, not perk-assignment. |
| **Subclasses of Skyrim** (51) | `DAR_Perk03Constitution` (00080B) â€” Playable=True, Hidden=False, "Health Regeneration +25%" | Player-facing, **balance-bearing magnitude**, parallels Requiem's standing-stone/birthsign economy | **FLAG to user.** A player class/perk system that overlaps Requiem's own gates; a magnitude like +25% regen may double-dip. Ask before assigning or rebalancing. |
| **Shadow of Skyrim** (18) | `_pRB_StalwartDefense` (00090C) â€” Hidden=True, "10% dmg reduction frontal 45Â°"; `_pDB_FearOf*` family | Hidden, nemesis/death-alternative mechanic driven by the mod's own scripts | **Leave alone.** Mod-specific runtime system; the mod assigns/removes these itself. Not for manual NPC placement. |

Disposition rule the corpus supports:
1. **Hidden + editorid prefix = mod's own runtime plumbing** (`WB_`, `_pDB_`/`_pRB_`, `Nox_`, `RFTI_`)
   â†’ never hand-place; the owning system (mod script or Reqtificator) stamps it.
2. **Playable + Named + a balance magnitude that parallels a Requiem gate** (Subclasses' regen/armor)
   â†’ **flag to the user with the specific overlap named**; don't silently rebalance.
3. Only rebalance inline when it's an obvious flat duplicate of a Requiem player-tree node with no
   mod-specific mechanic â€” rare.

---

## 4. Rank mechanics â€” the write shape

Confirmed on real actors (Â§2a schema + Â§2b NPC reads):

- Requiem multi-rank lines are **NextPerk chains of separate single-rank FormIDs**
  (`WeaponMastery1 0BABE4 â†’ WeaponMastery2 079343 â†’ â€¦`; `DaggerFocus1 AD399A â†’ 2 AD3999 â†’ 3 AD3998`),
  each `NumRanks=1`.
- On an NPC, each rank is its **own `PerkPlacement` entry** with `Perk=<that rank's FormID>` and
  `Rank=1`. The SwordShield bandit carries `079343`(WeaponMastery2) **and** `0BABE4`(WeaponMastery1)
  as two separate entries, both Rank=1 â€” **not** one FormID at Rank 2.
- **Write shape the skill prescribes:** to give an NPC "N ranks" of a line, add one `PerkPlacement`
  per rank FormID up the `NextPerk` chain, each `Rank=1`. `Fluff` = `000000`. Do not set `Rank` >1;
  Requiem's split-FormID model means the Rank field is effectively always 1 for these.

---

## 5. Accounting

**Enumerated (query totals, all live):**
- `type=PERK plugins=[Requiem.esp]` group_by=defined_in â†’ **599** across 7 groups (162 Requiem-defined).
- `type=PERK plugins=[Requiem.esp] defined_in` group_by=winner â†’ 162 across 17 winner plugins.
- `type=PERK plugins=[Requiem - Magic Redone.esp] defined_in` â†’ **50**.
- `type=PERK plugins=[Requiem - Weapons and Armor Redone.esp] defined_in` â†’ **28** (incl. HandToHand line).
- `type=PERK` group_by=defined_in (whole order) â†’ **1876** across **168** plugins.
- Reverse lookup `type=NPC_ defined_in=[Requiem.esp] references=[8 player-exclusive perks]` â†’ **0**.
- Enumerated 148 of 162 Requiem-defined perk editorids (limit truncation at 200 returned 148 lines;
  the classification pattern â€” REQ_\* tree / RFTI_Player_\* / RFTI_All_\* / RFTI_Trait_\* â€” holds
  across the full set; the 14 unshown are within these families).

**Records full-read:** `AD399A` DaggerFocus1 (schema), `AD3A34` ArmorWeight (hidden-mechanic schema),
`0BABE4` Armsman/WeaponMastery1 (conflict_tree, 4-plugin delta), `86837E` bandit **source (5)** +
**winner (50)** with resolved names (the boundary proof), `879914` bow bandit source (4), an 11-record
batch of mechanics+player-exclusive perks, a 3-record batch of mod perks (Subclasses/Apocalypse/Shadow).

**Inferences (marked):**
- The Reqtificator stamps all 10 `RFTI_All_PlayableRace_*` and the full `RFTI_Ench_*` artifact set on
  every actor (observed on one bandit; **inferred** to be uniform across actors from the "All"
  semantics â€” not exhaustively swept).
- The disposition rule (Â§3) is calibrated from 3 sampled mods; **inferred** to generalize by prefix
  convention, not proven across all 168 definers.
- "Reqtificator owns `RFTI_All_*`/`Nox_Perk_Mechanics_*`" is proven by their **absence in source /
  presence in the RftI winner**; that the Reqtificator (not another patch) is the stamper is inferred
  from the winner being `Requiem for the Indifferent.esp` (the RftI output plugin).
