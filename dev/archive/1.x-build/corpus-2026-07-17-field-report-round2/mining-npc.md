# NPC_ mining for `requiem-npc-patching` — empirical doctrine

Load order: active Requiem MO2 instance. `Requiem for the Indifferent.esp` (Reqtificator output)
is INACTIVE, so winners are the hand-authored Requiem layers + the `Authoria - *` Reqtificated
patches (a Reqtificator-lane merge set). Every claim below is `FormID:Plugin.esp / EditorID`.

Total NPC_ records in order: **18526** across 379 winning plugins.

---

## JOB 1 — The Requiem-lane authority list (who touches NPC_)

### Core Requiem-lane plugins by NPC_ WIN count (from `group_by=winner`)

| Plugin | NPC_ wins |
|---|---|
| Requiem.esp | 1958 |
| Requiem_VampireCollection.esp | 296 |
| Requiem - Minor Arcana - Forsworn.esp | 219 |
| Requiem Magic Redone - Enemy Magic Expansion.esp | 107 |
| Requiem - Olenveld.esp | 85 |
| Requiem - Magic Redone.esp | 60 |
| Requiem - Weapons and Armor Redone.esp | 47 |
| Requiem - Wyrmstooth.esp | 46 |
| Requiem - Minor Arcana - Civil War.esp | 40 |
| Requiem - More Unique Enemies.esp | 24 |
| USMP - Requiem.esp | 17 |
| Requiem - ECSS.esp | 15 |
| Requiem - Siege at Icemoth.esp | 15 |
| Requiem - Minor Arcana - Roleplaying.esp | 14 |
| Requiem - More Unique Enemies - Magic Redone Addon.esp | 11 |
| Requiem - New Legion.esp | 9 |
| Requiem-betterForgottenVale.esp | 5 |
| Requiem - Resist and Regen Tweak.esp | 3 |
| Requiem - Additional Dremora Faces - Dremora Lines Expansion.esp | 2 |
| Requiem - Heart of Ice Astrid.esp | 2 |
| Requiem - Ryn's Standing Stones.esp | 2 |

Note: the *biggest* NPC winners after Requiem.esp are the **`Authoria - Reqtificated - *` and
`Authoria - Master Patch - NPCs Merge.esp`** plugins (Glenmoril 1508, Vigilant 1027, NPCs Merge 738,
etc.) — these are the Reqtificator-lane per-mod patches, i.e. the model this skill *produces*.

### What each Requiem-lane plugin TOUCHES (`group_by=defined_in`, count = records touched)

| Plugin | NPC_ touched | Defines (new) | Overrides (vanilla/DLC) | Role |
|---|---|---|---|---|
| Requiem.esp | 2702 | 339 | Skyrim 1755, Dragonborn 406, Dawnguard 193, ccFish 6, HF 3 | The base rebalance layer — overrides nearly every combat actor + defines its own templates/classes |
| Requiem_VampireCollection.esp | 371 | 231 | Skyrim 71, Dawnguard 69 | Vampire actor overhaul (own vampire templates) |
| Requiem - Minor Arcana - Forsworn.esp | 225 | 183 | Skyrim 42 | Forsworn faction overhaul (defines `RMA_Forsworn*` actors) |
| Requiem - Weapons and Armor Redone.esp | 59 | 15 | Skyrim 26, Requiem 12, Dawnguard 6 | Re-tempers a subset of actors' gear traits |
| Requiem - Magic Redone.esp | 66 | 41 | Requiem 21, Skyrim 3, Dragonborn 1 | Defines caster summons (`REQ_Actor_Conjuration_*` etc.) |
| Requiem - More Unique Enemies.esp | 35 | 35 | — | New unique enemies |
| USMP - Requiem.esp | 128 | 0 | Skyrim 79, Dragonborn 28, Dawnguard 18, HF 3 | Compat forward — re-applies Requiem deltas over USMP's civilian edits (no new records) |
| Requiem - Resist and Regen Tweak.esp | 4 | 0 | Dawnguard 2, Requiem 1, Skyrim 1 | **Pure perk-injection**: adds one perk `000805` to a few actors, nothing else |

### Sample reads (what the smaller layers actually change vs the layer beneath)

- **Requiem - Resist and Regen Tweak.esp** on `00E25A:Dawnguard.esm / DLC1FrostGiant` and
  `35214E:Requiem.esp / REQ_Actor_Ragnok`: the ONLY delta vs the Requiem.esp version is
  `Perks += 000805:Requiem - Resist and Regen Tweak.esp` (rank 1). It is a single-perk overlay.
- **USMP - Requiem.esp** on `01347F:Skyrim.esm / Sven`: re-carries Requiem's full stat block
  (skills, DNAM, factions) on top of USMP's face/AI edits — a pure Requiem-forward, adds no records.
- **Requiem - Magic Redone.esp** defines conjuration-summon actors, e.g.
  `005D91 / REQ_Actor_Conjuration_Spirit_Bear`, `00594E / REQ_Actor_Illusion_Shadow_Wolf`.

---

## JOB 2 — What Requiem does (and does NOT do) to non-combatants

**Method:** conflict trees on the vanilla civilians. The decisive question is whether
**Requiem.esp** (or a Requiem-lane plugin) appears in each record's touching-plugin list.

| NPC | FormID | Level | Flags | Requiem.esp touches? | If yes, what Requiem changes |
|---|---|---|---|---|---|
| CarlottaValentia | 013B99:Skyrim.esm | fixed 4 | Female,AutoCalcStats,Unique | **NO** (only Skyrim+AI Overhaul) | — |
| Hulda | 013BA3:Skyrim.esm | fixed 4 | Female,AutoCalcStats,Unique | **NO** | — |
| Arcadia | 013BA4:Skyrim.esm | fixed 4 | Female,AutoCalcStats,Unique | **NO** | — |
| LucanValerius | 01347A:Skyrim.esm | fixed 4 | AutoCalcStats,Unique | **NO** | — |
| Brenuin (beggar) | 013BA7:Skyrim.esm | fixed 4 | AutoCalcStats,Unique | **NO** | — |
| Braith (child) | 013BA9:Skyrim.esm | fixed 4 | Female,AutoCalcStats,Unique | **NO** | — |
| SeverioPelagia (farmer) | 02C925:Skyrim.esm | fixed 4 | AutoCalcStats,Unique | **NO** | — |
| Belethor | 013BA1:Skyrim.esm | fixed 4 | AutoCalcStats,Unique | **YES** | lowers all SkillValues (OneHanded 17→7, combat skills →5); raises DNAM (H75→95, M60→135, S60→85); adds 1 item; NO perks, NO spells |
| Sven (bard/follower-lite) | 01347F:Skyrim.esm | fixed 4 | AutoCalcStats,Unique | **YES** | lowers SkillValues (OneHanded 24→14, magic skills →5); raises DNAM (H91→128, M67→88, S67→109); attaches WIDeadBodyCleanupScript; removes 1 faction; NO perks, NO spells |
| Faendal (follower) | 013480:Skyrim.esm | fixed | AutoCalcStats | (touched by USMP-Requiem lane; same civilian pattern) | same as Sven-class |
| GuardWhiterun (generic) | 0ABE18:Skyrim.esm | fixed 1 | Respawn | **NO** (Skyrim.esm depth-1, untouched) | — |

### Job 2 conclusions (the patch-vs-skip boundary)

1. **Requiem.esp does NOT individually override most plain civilians** (merchants, innkeepers,
   beggars, children, farmers). They stay vanilla: **AutoCalcStats ON, fixed Level 4, no perks,
   no ActorEffect.** Their Requiem balance (citizen perks etc.) is applied at build time by the
   **Reqtificator**, which is inactive in this profile — hence they read vanilla-ish here.
2. **The few named civilians Requiem DOES override** (Belethor, Sven, Faendal) get a *stat-only*
   edit: SkillValues pulled DOWN to the 5–20 band, DNAM base attributes pushed UP, occasional
   item/script/faction cleanup — **but still NO Perks and NO ActorEffect on the Requiem.esp record**
   (those come from the Reqtificator merge; e.g. Sven's citizen perks `270B76/270B77:Requiem.esp`
   are added by `Authoria - Master Patch - NPCs Merge.esp`, not by Requiem.esp).
3. **Generic guards and soldiers are NOT hand-edited by Requiem.esp** — the generic Whiterun guard
   wins at `Skyrim.esm` override_depth=1 (nothing touches it). They are template actors; Requiem
   balances them through their template/class + the Reqtificator, not per-actor.
4. **Everything uses a FIXED level** (the `[NpcLevel]` union arm, e.g. Level=4), never PCLevelMult.
   Requiem de-levels citizens to a fixed low level just like combatants.

**Doctrine for the skill:** a new *plain non-combatant* (vendor, quest-giver, child) needs at most
a light stat pass (fixed low level, AutoCalcStats, class/faction) and should be **left to the
Reqtificator for perks** — do NOT hand-stamp a perk/spell kit onto a shopkeeper. A new *combatant*
(any actor that fights) must carry the full hand-authored kit (below).

---

## JOB 3 — The stat model per archetype (load-bearing)

All records read from their Requiem-lane winner. `SkillOffsets` were 0 on every templated
combatant read (see Q3c for where they are NOT). "Perks" = REQ player-tree perks (see resolved
list below). Level is always the fixed `[NpcLevel]` arm.

### (a) Bandit tiers — `REQ_Bandit_Template_SwordShield_*` (defined in Requiem.esp)

| Tier | FormID | Flags | Level | DNAM H/M/S | HealthOff/MagOff/StamOff | key SkillValues | Perks | ActorEffect |
|---|---|---|---|---|---|---|---|---|
| Base | 86837D | Respawn,LoopedScript,LoopedAudio (**no AutoCalcStats**) | 1 | 25/25/50 | 0/0/0 | 1H 10, 2H 15, Block 10 | **0 (absent)** | **0 (absent)** |
| 01 | 86837E | +AutoCalcStats | 3 | 104/100/106 | 0/0/0 | 1H 10, Block 10, HeavyArmor 11 | 5 | 1 (Tempering Heavy Rank1) |
| 02 | 86837F | +AutoCalcStats | 7 | 112/100/118 | 0/0/0 | 1H 21, Block 21, HeavyArmor 21 | 9 | 1 (Tempering Heavy Rank2) |
| 03 | 868380 | +AutoCalcStats | 10 | 118/100/127 | 0/0/0 | 1H 29, Block 29, HeavyArmor 29 | 12 | 1 (Tempering Heavy Rank3) |

Class = `85BCE3:Requiem.esp / REQ_Class_Bandit_SwordShield` for all tiers. Non-combat skills stay 5.
The **Base** record is a bare scaffold (no AutoCalc, no perks, no spells); the numbered tiers are the
real spawns. Health + Stamina + the three combat skills (1H/Block/HeavyArmor) + perk count + tempering
rank ALL escalate monotonically with tier.

**Resolved perk list, tier 02 (`86837F`)** — these are Requiem's own PLAYER perk-tree perks:
`REQ_OneHanded_SwiftStrikes (052D50)`, `REQ_Block_StrongGrip (058F68)`,
`REQ_OneHanded_WeaponMastery2 (079343)`, `REQ_Block_ExperiencedBlocking (079355)`,
`REQ_HeavyArmor_RelentlessOnslaught (07935E)`, `REQ_OneHanded_WeaponMastery1 (0BABE4)`,
`REQ_Block_ImprovedBlocking (0BCCAE)`, `REQ_HeavyArmor_Conditioning (0BCD2A)`,
`REQ_OneHanded_HandToHand (0AD7A3:Requiem.esp)`. → Enemies wear the SAME perks the player earns;
higher tier = deeper into the tree.

### (b) Caster (warlock) at 2 tiers — Skyrim.esm actors overridden by Requiem.esp

| Actor | FormID | Flags | Level | DNAM H/M/S | HOff/MOff/SOff | magic SkillValues | Perks | ActorEffect (spells) |
|---|---|---|---|---|---|---|---|---|
| EncWarlock01TemplateFire | 044CD7 | Respawn,LoopedScript,LoopedAudio (**no AutoCalc**) | 1 | 50/100/25 | 100/450/100 | Destr 5 (all low) | **0 (absent)** | 2: Flames, Steadfast Ward |
| EncWarlock02TemplateFire | 045C5F | Respawn,LoopedScript,LoopedAudio | 6 | 142/158/25 | 60/400/100 | Destr 26, Alt 17, Rest 16, Conj 12 | 2 | 5: Flames, Healing, Oakflesh, Ward, Firebolt |

Class escalates `EncClassBanditMelee → CombatMageElemental`. Casters carry a **large MagickaOffset
(400–450)** in ACBS; DNAM Magicka also rises. Their kit is the spell loadout in **ActorEffect**
(Requiem's `REQ_Destruction*/Restoration*/Alteration*` spells), scaling in number + tier with level.

### (c) Draugr at 2 tiers — Skyrim.esm actors overridden by Requiem.esp

| Actor | FormID | Flags | Level | DNAM H/M/S | HOff/MOff/SOff | key SkillValues | Perks | ActorEffect |
|---|---|---|---|---|---|---|---|---|
| EncDraugr01MissileHeadM00 | 0387C3 | Respawn | 1 | 300/0/**10000** | 200/0/0 | Destr 10, Illu 10 | 10 | 0 (absent) |
| EncDraugr02MissileHeadM00 | 022400 | Respawn | 6 | 387/0/**10113** | 200/0/100 | 1H 16, 2H 16, Block 12, HeavyArmor 12 | 14 | 1: Natural Armor: Armored Draugr (`03280E`) |

Class = `EncClassDraugrMissile/Melee`. **Draugr carry ~10,000 Stamina** — a deliberate anti-stagger /
no-exhaustion mechanic, hand-set in DNAM. Health + perk count escalate with tier; tier 2 gains a
"Natural Armor" trait spell in ActorEffect.

### (d) Animals / big creatures — Skyrim.esm actors overridden by Requiem.esp

| Actor | FormID | Flags | Level | DNAM H/M/S | HOff/MOff/SOff | key SkillValues | Perks | ActorEffect |
|---|---|---|---|---|---|---|---|---|
| EncWolf | 023ABE | Respawn (**no AutoCalc**) | 2 | 85/0/205 | 0/0/0 | Sneak 22, 1H/Archery/Block 7 | **0 (absent)** | **0 (absent)** |
| EncTroll | 023ABA | Respawn | 14 | 280/0/340 | 350/0/175 | Block 36, Sneak 36, 1H 26, Archery 26 | 1 | 0 (absent) |

Class = `EncClassAnimalPredator`. Creatures = **AutoCalcStats OFF**; Magicka 0; all stats live in
DNAM (Health/Stamina) + ACBS offsets on the bigger ones (troll H+350/S+175). Wolf is the clean
"empty kit" case: NO perks, NO spells — stats alone. Bigger creatures get 1 trait perk (troll:
regen/armor).

### (e) Boss — DragonPriest `023A93:Skyrim.esm` (winner = Authoria merge, Requiem-lane)

Flags Respawn,BleedoutOverride; **Level 50**; MagickaOffset **1300**; DNAM **H 1490 / M 545 / S 0**;
magic SkillValues Conj 100, Destr 100, Rest 100, Alt 89, Sneak 45; **15 Perks**; **5 ActorEffect**
(`LSpellDragonPriestDestruction`, `LSpellWalls`, `LSpellDestructionArea`, `LSpellCloak50`, +
`REQ_Trait_ResistMagic30`). Class `EncClassDragonPriest`. Boss model = huge fixed level + big DNAM +
big MagickaOffset + deep perk stack + boss spell list + a resist trait.

### (f) Vampire — `REQ_Template_Vampire02` (03384E:Skyrim.esm, winner Requiem_VampireCollection.esp)

Flags Female,Respawn; Level 27; offsets 270/270/135; DNAM 224/169/112; SkillValues 1H 50, Sneak 45,
Conj 40, HeavyArmor 37; **30 Perks**; **8 ActorEffect** (Vampire's Shadow, Stoneflesh, Vampire Claws,
Vampiric Drain, Vampirism resist ab, SearingSun trait, tempering trait, a conjuration LS spell).
Class `EncClassVampire`. Vampires get the richest trait/spell bundle + a very deep perk stack.

### (g) Guard — generic `GuardWhiterunHoldImperialGeneric` (0ABE18:Skyrim.esm)

Flags Respawn only; fixed Level 1; **no override by any Requiem plugin** (Skyrim.esm depth-1). A
template actor — balanced through its template/class + Reqtificator, never hand-edited per-guard.

### Explicit answers

**Q3a — AutoCalcStats-ON actors: are DNAM + SkillValues meaningful or CK defaults? Do they escalate?**
Meaningful and hand-set. Across bandit tiers the DNAM base (Health 25→104→112→118, Stamina
50→106→118→127) and the *relevant* combat SkillValues (1H/Block/HeavyArmor 10→~11→21→29) escalate
monotonically with tier, while irrelevant skills stay pinned at 5. These are deliberate authored
values, not auto-calc artifacts. (Even the AutoCalcStats flag being ON does not stop Requiem writing
explicit DNAM/skills — it always writes them.)

**Q3b — AutoCalcStats-OFF actors (creatures): where do the stats live?**
In **DNAM base attributes** (PlayerSkills.Health/Magicka/Stamina) **plus ACBS offsets**
(Configuration.HealthOffset/MagickaOffset/StaminaOffset) — a mix. Wolf: pure DNAM (offsets 0,
H85/S205). Troll: DNAM (280/340) + big offsets (H+350/S+175). Draugr: DNAM (H300, S10000) + HealthOffset
200. Magicka 0 on non-casters. SkillValues carry the "skill" contribution; SkillOffsets stay 0 on
these templated creatures.

**Q3c — Does Requiem ever use SkillOffsets?**
**Yes.** The bracket-path `where=["PlayerSkills.SkillOffsets[OneHanded] > 0"]` on Requiem.esp NPC_
worked and returned **11 actors** with a nonzero OneHanded offset — all *named/unique humanoids or
leveled summons*, not the templated mob tiers: `GeneralTullius (01327E)`, `Alvor (013475)`,
`Hod (01347D)`, `AlainDufont (01B074)`, `MS14LaeletteVampire (0274A0)`, `CWBattleTullius (0D0577)`,
and Soul Cairn Wrathmen (`004575/0045B4/0071AD:Dawnguard.esm`), `DLC1EncUndeadSummon1 (01A16A)`.
Confirmed on read: AlainDufont carries SkillOffsets OneHanded=100, Block=100 (vanilla 0);
EncWarlock01 carries SkillOffsets Conjuration=30 (vanilla 0). So Requiem uses SkillOffsets to give
*named/scaling* actors per-level skill growth, on top of the base SkillValues. Templated mob tiers
use SkillValues only (offsets 0).

**Q3d — Do low-tier combatants ever have EMPTY Perks / EMPTY ActorEffect?**
**Yes, and it is meaningful.** `REQ_Bandit_Template_SwordShield_Base` (86837D) has both Perks and
ActorEffect *absent*; `EncWolf` (023ABE) has both *absent*; `EncWarlock01TemplateFire` (044CD7) has
Perks *absent* (spells present); `EncDraugr01MissileHeadM00` has ActorEffect *absent* (perks present).
So a source NPC arriving with no perks and/or no spells is normal and the skill must SUPPLY the
analogue's kit rather than assume the record already carries one. The "Base" scaffold pattern (empty
kit, no AutoCalc) also tells you the un-tiered template is not itself a balanced spawn.

---

## JOB 4 — The field-checklist: what Requiem actually edits on an NPC

Union of fields changed across `diff Skyrim.esm → Requiem.esp` on Sven (01347F), Belethor (013BA1),
EncWarlock01TemplateFire (044CD7), and AlainDufont (01B074, a named boss: Level 4→40, H75→465,
+18 perks, +2 spells, +SkillOffsets):

**Balance fields (the skill MUST set these):**
- `Configuration.Level.Level` — fixed level (Alain 4→40; civilians→4). Always the `[NpcLevel]` arm.
- `Configuration.Flags` — toggles AutoCalcStats / Unique / Protected etc. (Alain: drops AutoCalcStats;
  bandit tiers: adds it). 
- `Configuration.HealthOffset`, `Configuration.MagickaOffset`, `Configuration.StaminaOffset` — ACBS offsets.
- `Configuration.SpeedMultiplier` — bumped on some (Alain 100→135).
- `Configuration.TemplateFlags` — Requiem adds `Stats` to the inherit mask on template actors (warlock).
- `Class` — swapped to a Requiem/vanilla combat class (warlock `CombatMageElemental → EncClassBanditMelee`... i.e. Requiem picks the class).
- `PlayerSkills.Health`, `.Magicka`, `.Stamina` — DNAM base attributes.
- `PlayerSkills.SkillValues[*]` — all 18 skills rewritten.
- `PlayerSkills.SkillOffsets[*]` — set on named/scaling actors (Alain 1H/Block=100; warlock Conj=30).
- `Perks` — the big one: adds Requiem's player-tree perks (Alain +18).
- `ActorEffect` — adds Requiem spells + trait abilities (tempering, natural armor, resist-magic, vampirism, racial traits).

**Incidental fields Requiem also touches (situational, mostly cleanup — usually leave to the merge):**
- `Name` (occasionally, e.g. "Novice Fire Mage"→"Fire Mage")
- `DefaultOutfit` (Alain → a Requiem outfit)
- `Items` (Belethor +1 item)
- `Factions` (add/remove one)
- `AIData.Confidence/Responsibility/Mood` (occasional)
- `VirtualMachineAdapter` (attaches WIDeadBodyCleanupScript on some)
- `DeathItem`, `WornArmor` (on templated creatures)

**Bookkeeping fields (Requiem writes them but they are NOT balance — do not hand-derive):**
- `PlayerSkills.Unused` (→16743) and `PlayerSkills.Unused2` — CK-recalced bytes.
- `Version2` / `VersionControl` — record versioning. `TintLayers` — cosmetic.

**Net field checklist the skill carries:** Level (fixed), Flags, {Health,Magicka,Stamina}Offset,
SpeedMultiplier, TemplateFlags (template actors), Class, PlayerSkills.{Health,Magicka,Stamina},
PlayerSkills.SkillValues[18], PlayerSkills.SkillOffsets (named/scaling only), Perks, ActorEffect —
plus optional Name/Outfit/Faction/DeathItem/WornArmor per case. Perks + ActorEffect + the stat block
are the load-bearing derivations from the live comparable.
