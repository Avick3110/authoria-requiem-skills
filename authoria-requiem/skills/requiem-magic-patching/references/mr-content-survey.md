# Magic Redone — full content survey (every record class)

What `Requiem - Magic Redone.esp` (MR) actually contains, by record type — so you know the **whole**
shape of a Requiem magic implementation, not just the spell. All counts are MR's true totals (records
MR touches); FormIDs ending `:Requiem - Magic Redone.esp` are **MR-defined** (new), other masters are
records MR **overrides** (re-tunes). Verified live 2026-06-09.

## Contents

1. Inventory (record-class counts)
2. New damage types (the MGEF headline)
3. The mechanic-effect patterns (taper / GM / dummy-AV)
4. Enchantment families (ENCH taxonomy)
5. The crafting systems (staff enchanter, scroll crafting)
6. Pre-enchanted gear generation
7. The delivery/FX chain (PROJ → EXPL / HAZD)
8. Supporting classes (FormList, LeveledItem, MiscItem, Ingestible)

## 1. Inventory — what MR touches

| Class | Count | What MR does |
|---|---|---|
| MagicEffect (MGEF) | 1413 | ~550–600 **new** (damage types, tapers, ench effects, mechanics) + overrides of every vanilla effect |
| ObjectEffect (ENCH) | 1029 | staff/apparel/weapon/bound/ammo enchants (see §4) |
| ConstructibleObject (COBJ) | 2955 | the **crafting systems** — staff forging/enchanting + scroll crafting (see §5) |
| Weapon (WEAP) | 1250 | staves (= enchanted weapons), bound weapons, artifacts (shared with `requiem-weapon-patching`) |
| Scroll (SCRL) | 665 | one per spell×tier (single-use casts) |
| Book (BOOK) | 584 | spell tomes (`REQ_Tome_<School><Tier>_…`) |
| Armor (ARMO) | 340 | **pre-enchanted** robe/armor variants (see §6) |
| Perk (PERK) | 186 | the magic perk trees (see `perks-and-tomes.md`) |
| Keyword (KYWD) | 107 | the `Nox_KW_*` marker vocabulary (see `keywords.md`) |
| Projectile (PROJ) | 87 | spell + ammo projectiles (see §7) |
| LeveledItem (LVLI) | 327 | robe/staff/tome/scroll placement lists |
| FormList (FLST) | 51 | spell pools (`REQ_List_<School>_<Tier>Spells`), perk bundles |
| Explosion (EXPL) | 40 | AoE impact bursts (see §7) |
| Hazard (HAZD) | 39 | walls / runes / circles — lingering ground (see §7) |
| Ingestible (ALCH) | 24 | Fortify-skill potions (overlaps `Requiem - Alchemy Redone.esp`) |
| ArtObject (ARTO) | 22 | cloak/hand casting-FX art |
| MiscItem (MISC) | 11 | crafting reagents (paper roll, soul-gem fragments, scroll tool) |
| Ammunition (AMMO) | 6 | bound arrows (winner is the MR-WAR patch) |
| VisualEffect (RFCT) | 3 | incidental creature FX |
| DualCastData (DUAL) | 1 | magelight dual-cast override |
| (LeveledSpell, Shout, Quest) | 0 | MR defines none — shouts live in `Requiem.esp`; Nox runtime is the script mod |

## 2. New damage types — beyond Fire/Frost/Shock

MR adds six damage flavors. Each is a `ValueModifier`/`DualValueModifier` (or `Absorb`) on Health,
tagged with a school `Nox_KW_<School>_<Type>` keyword + `REQ_NoDurationScaling 412EDF`, flags
`Hostile, Detrimental, FXPersist, PowerAffectsMagnitude, NoDeathDispel`.

| Type | School (`MagicSkill`) | `ResistValue` | Marker keyword | Rep. MGEF |
|---|---|---|---|---|
| **Venom** | Destruction | **PoisonResist** (+ `AD3904 REQ_PoisonSpell`) | — | `005B0C` |
| **Poison** (offensive) | Restoration | **PoisonResist** (+ `AD3904`) | `005C22 Nox_KW_Restoration_Poison` | `005C20` |
| **Arcane** | Destruction | **None** (unresistable) | `005B00 Nox_KW_Destruction_Arcane` | `005ACD` |
| **Entropic** | Destruction | **ResistMagic** | `005B01 Nox_KW_Destruction_Entropic` | `005ACB` |
| **Sun** | Restoration | **ResistMagic** (+ `ADDDF6 REQ_SunDamage`) | — | `005C70` |
| **Necrotic** | Conjuration | **ResistMagic** | `005F21 Nox_KW_Conjuration_Necrotic` | `005F1B` |
| **Absorb** H/M/S | Destruction | **ResistMagic** (+ `ADDDF7 REQ_Absorb`) | `005DA8 Nox_KW_Destruction_Absorb_MS` | `005B78` |

So when a modded spell does poison/arcane/holy/drain damage, set the matching `MagicSkill` + `ResistValue`
+ marker keyword off the table — don't mis-type it as a vanilla element. (`REQ_PoisonSpell`,
`REQ_SunDamage`, `REQ_Absorb` are `Requiem.esp`-defined keywords used by MR; the `Nox_KW_*` are MR-defined.)

## 3. Mechanic-effect patterns (copy, don't reinvent)

- **`*_Taper` lingering DoT** (~13 effects, e.g. `0060F4` Arcane Taper): `Archetype.ActorValue = Fame`
  (a **dummy no-op AV**) + `HideInUI` + `Recover`, Concentration. The taper applies its keyword-driven
  damage tick over time without touching a real stat and stays off the active-effects list. Every
  damage line has one.
- **GM / Cremation** (`005AA8`–`005AAC`, 5 ranks): perk-gated execute/burn, gated at the perk-entry
  layer (not a flag on the MGEF).
- **Feedback / ParalyzingPoison / Disarray**: `Absorb`/`Paralysis` archetype + `HideInUI` mechanic procs.

The takeaway from §2–§3: a Requiem damage spell is **primary effect + taper + (perk-gated GM)** — three
effects — and the taper/GM use the dummy-`Fame`-AV + `HideInUI` trick. Mirror that structure.

## 4. Enchantment families (1029 ENCH) — all `Flags = NoAutoCalc`

| Family | Count | Type / Cast / Target | Notes |
|---|---|---|---|
| `REQ_Ench_Staff_*` | **576** | **StaffEnchantment** / FaF / (Aimed·Target·Loc) | wrappers around the spell's MGEF at the spell's magnitude; cost 100–300 by spell power; charge pool on the WEAP (≈1000) |
| `REQ_Ench_Armor_*` | **300** | Enchantment / **ConstantEffect** / Self | tiered `01`–`06`; numbered tier → `BaseEnchantment` `_Base`; `WornRestrictions` only on `_Base` |
| `REQ_Ench_Weapon_*` | **105** | Enchantment / FaF / **Touch** | charge-based; single-effect → **Cost = Amount = Magnitude**; tiers 0–6 |
| `REQ_Ench_Spell_*` | 21 | Enchantment / FaF / Touch | spell-delivered "enchant" effects (Empowered Weapon, etc.) |
| `REQ_Ench_Bound_*` | 13 | Enchantment / FaF / Touch | bound-weapon FX carriers, cost 0, up to 18 bundled effects |
| `REQ_Ench_Ammo_*` | 12 | Enchantment / FaF / Touch | elemental arrow/bolt (winner = MR-WAR patch) |
| `REQ_Ench_Artifact_*` | 2 | StaffEnchantment | Silds/Halldirs staff |

**Weapon-enchant ladder (Fire, tiers 0–6):** Cost/Magnitude `10/10/20/25/35/40/50` (MGEF `04605A`).
Repeats per element. Single-effect families set Cost = Amount = effect Magnitude (no separate knob).
Frost/Shock are **DualValueModifier** (element + stamina/magicka drain); **Chaos** (Dragonborn) and
**Force** are triple-effect. MR-new weapon families include **Toxicity** (`03D4EB`, cost 15), **Arcane**
(`184F06`, cost 15), Fireburst/Frostburst/Shockburst, Annihilation, Spellbreaking.

**Apparel ladders (constant-effect, tiers 01–06):** Resist `<Element>` = 10/20/25/35/40/50 %, cost
100×tier; Fortify Magicka/Health/Stamina = 20/30/50/70/80/100 pts; Fortify `<Skill>` = +4 at tier 01.
MR-new apparel families: **FortifyArmorPenetration**, **FortifySneakAttack**, **SpellAbsorption**
(`00784F`, +2 at tier 01) — plus `Potent<School>` spell-potency apparel and `…College` variants.

**Staff model:** `REQ_Ench_Staff_<School><Tier>_<Spell>_<Delivery>` is `EnchantType = StaffEnchantment`,
reuses the same MGEF(s) as the equivalent spell at the spell's own magnitude/duration; `TargetType`
follows the spell (Aimed/TargetActor/TargetLocation), cost tracks spell power (100–300). Summon staves
carry per-effect placement `Conditions`.

## 5. Crafting systems (2955 COBJ) — three furniture families

A magic-craft COBJ never embeds an enchantment; it **assembles an already-enchanted record** the player
has unlocked by knowing the spell. Shape: `Items[]` inputs, `Conditions[]` = `HasSpell` (± `HasPerk`),
`CreatedObject` = the finished item.

| Family | Count | Furniture (`WorkbenchKeyword`) | Gate | Inputs | Output |
|---|---|---|---|---|---|
| `REQ_Forge_Staff_*` | 575 | `CraftingSmithingForge 088105` | HasPerk **+** HasSpell | staff blank (`REQ_Staff_<School>0_Template` WEAP) + 1 filled soul gem | `REQ_Staff_*` |
| `REQ_StaffEnchanter_Staff_*` | 570 | `DLC2StaffEnchanter 017738` | HasSpell only | staff blank + 2× `DLC2HeartStone` | same `REQ_Staff_*` |
| `REQ_ScrollCrafting_Scroll_*` | 546 | `CraftingScrollCraftingTool 006105` | HasPerk **+** HasSpell | 1× `PaperRoll 033761` + 2× `REQ_Misc_SoulGemFragment_Filled 00610F` | `REQ_Scroll_*` |
| `REQ_Forge_Battlestaff_*` | 28 | forge | HasSpell | wood battlestaff blank | battlestaff |
| `REQ_ScrollCrafting_SoulGem_*` / `_Paper_*` | 8 | scroll tool | — | — | the reagent economy (fragments, paper) |

So: every staff is craftable two ways (forge w/ perk, or DLC2 enchanter w/ heart stones); every scroll
is craftable at the scroll tool from paper + soul-gem fragments; all gated by **knowing the spell**.

## 6. Pre-enchanted gear generation

MR's enchanted-armor catalog = the cross-product **{base item} × {ENCH effect} × {magnitude tier 1–3}**,
each a distinct ARMO override. EditorID `REQ_Ench_<Weight>_<Material>_<Part>[_<Variant>]_<EnchKind><Tier>`
(e.g. `REQ_Ench_Light_Imperial_Body_Studded_LightArmor1/2/3` `046BB7-9`): same base cuirass, `ObjectEffect`
→ `REQ_Ench_Armor_FortifySpeed01/02/03` (mag 4/7/10), Name "of Minor/…/Major Speed". Armor enchants have
**no `EnchantmentAmount`** (constant, no charge). Enchanted **weapons** = the staves (WEAP + staff ENCH +
charge ≈1000). **To add a new enchanted item:** define/reuse an ENCH (one per magnitude tier), point the
item's `ObjectEffect` at it (armor: no charge; staff: ~1000 charge), optionally add a COBJ to craft it and
an LVLI to place it.

## 7. Delivery / FX chain — MGEF → PROJ → EXPL / HAZD

The MGEF carries the wiring (`Projectile`, `Explosion`, `Hazard`, `HitEffectArt`, `ImpactData`). The PROJ
flies; the EXPL/HAZD is the impact event. Two routes: **MGEF-driven** (the common case — EXPL/HAZD live on
the MGEF, the PROJ's own `Explosion` is null) and **PROJ-driven** (the projectile has `Flags=Explosion` +
an `Explosion` link, e.g. the tier-5 "Vortex" family).

- **Projectiles (87):** profiled by `Type` — concentration **Cone** (Flames-like, Speed 512, ConeSpread
  10), **Beam** hitscan (Speed 20000, FreezingRay/AbsorbHealth), **Missile** bolts (Speed 5000). Spell
  PROJs have `Gravity 0`, a `RelaunchInterval`, no Name/Destructible. **Ammo** PROJs differ sharply
  (`Type=Arrow`, Speed 5600, Range 60000, `Supersonic, CanBeDisabled`, a Name + Destructible) — that's
  the `requiem-ammo-patching` profile.
- **Explosions (40):** **Damage = 0 on every one** — the EXPL is a force+visual shell (`Force`, `Radius`,
  `ImageSpaceModifier`, `Sound`, `ImpactDataSet`); the AoE damage comes from the MGEF's area, not the EXPL.
  Big weather AoEs (Blizzard `0B7A05` radius 1000 + ISMod, Earthquake radius 4000) vs plain bursts.
- **Hazards (39):** the lingering-ground volume for walls/runes/circles. A HAZD repeatedly applies its
  `Spell` link to actors in `Radius` every `TargetInterval` for `Lifetime` seconds; that spawned SPEL's
  MGEF carries the damage (HAZD → SPEL → MGEF). Walls use `AlignToImpactNormal`; circles use
  `InheritDurationFromSpawnSpell` + a `Light`. `_Drop` variants = ground-deposited placement.

So a new AoE/wall/rune spell needs the matching delivery record(s): an AimedExp spell links an EXPL on its
MGEF; a wall/circle links a HAZD whose `Spell` applies the damage MGEF. Reuse a tier-matched Requiem
PROJ/EXPL/HAZD or clone its profile.

## 8. Supporting classes

- **FormList (51):** spell pools `REQ_List_<School>_<Tier>Spells` (Novice→Master, what tomes/NPCs/staff
  recipes draw from), themed sub-lists, and `REQ_Perks_<School>` perk bundles.
- **LeveledItem (327):** world distribution — `LItemRobesCollege<School>`, `LItem{Necromancer,Warlock}Robes<School>`,
  `LItemStaff<School>{00,25,50,75}` (number = NPC skill bracket, `…NPC` for actor inventories). A new
  enchanted robe/staff enters the world by joining the matching list (→ `requiem-leveled-list-patching`).
- **MiscItem (11):** the reagent economy — `PaperRoll`, `REQ_Misc_SoulGemFragment_Filled`, the scroll
  tool object, soul-gem shards, + spell-mechanic helpers (LightningHook, ShadowDoor).
- **Ingestible (24):** Fortify-`<School>`/Enchanting potions (tiers 1–4) — overlaps `Requiem - Alchemy
  Redone.esp`, expect AR contention there.
