# Worked Examples

Three real in-scope Requiem weapons, derived end-to-end with the live-analogy loop. All values
mined from the conflict winners (2026-06-07).

## 1 — Plain melee: Steel Greatsword (from the Steel Sword comparable)

Goal: derive a steel **greatsword** knowing only the steel **sword** and the iron **greatsword**.

1. **Comparables.** Same-material, different-type: Steel Sword (`013989`, Dmg 8 / Val 45 / Wt 10).
   Same-type, different-material: Iron Greatsword (`01359D`, Dmg 16 / Val 50 / Wt 16 / Spd 0.75 /
   Reach 1.3 / Crit 8).
2. **Shape (type).** Greatsword shape: Speed 0.75, Reach 1.3, AnimationType TwoHandSword.
3. **Damage.** Iron greatsword 16 → steel is one tier up → +1 → **17**. Crit = floor(17/2) = **8**.
4. **Value.** Greatsword size-class ≈ 2× sword: steel sword 45 → **90**.
5. **Weight.** ~17 (iron greatsword 16, steel a touch heavier).
6. **Keywords.** `[WeapMaterialSteel 01E719, WeapTypeGreatsword 06D931, VendorItemWeapon 08F958]`.
   No damage-type keyword — `WeapTypeGreatsword` → the Reqtificator assigns `slash`.
7. **Sounds.** Copy a two-hand sword comparable's ImpactDataSet/EquipSound links.

**Verification:** the live Steel Greatsword winner (`013987`) is Dmg 17 / Val 90 / Wt 17 / Crit 8 /
Spd 0.75 / Reach 1.3 — an exact match. The loop reproduces Requiem's own numbers.

## 2 — Enchanted: Steel Sword of Burning (Fire2)

Real record `REQ_Ench_Weapon_Steel_Sword_Fire2` (04B452:Skyrim.esm).

1. **Base.** Plain Steel Sword `013989` (Dmg 8 / Val 45, keywords steel+sword+vendor).
2. **Template.** Point `Template` → `013989` (the plain variant the enchanted weapon derives from).
3. **Enchantment.** `ObjectEffect = 045C2A` (`REQ_Ench_Weapon_Fire2`), whose `EnchantmentCost` = 20.
4. **Charge.** Fire tier 2 → `EnchantmentAmount = 500 × 2 = 1000` (≈ 50 casts at cost 20).
5. **Everything else equals the base** — Value stays 45 (the enchantment adds charge, not gold),
   Damage 8, keywords unchanged, Name "Steel Sword of Burning".

**Verification:** the live winner has Template 013989, ObjectEffect 045C2A, EnchantmentAmount 1000,
Value 45 — match. (Fire1 = charge 500, Fire3 = charge 1500 confirm the 500×tier rule.)

## 3 — Unique artifact: Dawnbreaker (the deviation case)

Real record `REQ_Artifact_Dawnbreaker` (04E4EE:Skyrim.esm). Artifacts keep the *structure* but
deviate on value and enchantment — read a real `REQ_Artifact_*` comparable, don't force the ladder.

```
Name              = Dawnbreaker
BasicStats        = Value 80000 (bespoke!), Weight 10, Damage 13
Critical          = Damage 6 (still floor(13/2)), PercentMult 1
ObjectEffect      = 0FEE34:Skyrim.esm        # unique enchantment
EnchantmentAmount = (absent)                  # uncapped charge — artifacts skip the pool
Data.AnimationType = OneHandSword
Keywords:
  01E711:Skyrim.esm   WeapTypeSword            # type keyword → still gets 'slash'
  08F958:Skyrim.esm   VendorItemWeapon
  0917E8:Skyrim.esm   VendorItemDaedricArtifact
  0A8668:Skyrim.esm   DaedricArtifact
  0C27BD:Skyrim.esm   MagicDisallowEnchanting  # can't be re-enchanted
  10AA1A:Skyrim.esm   WeapMaterialSilver       # material used for a damage interaction (undead)
  AD3AF7:Requiem.esp  REQ_Tempering_LegendaryBlacksmithing  # tempering-perk gate as a keyword
```

What this teaches:
- **Damage still follows a tier** (13 = ebony-tier sword) and **crit is still floor(Dmg/2)** — the
  core ladder rules never break.
- **Value is hand-set** (80,000) — far off the material ladder. Don't ladder-derive an artifact's
  value; take the design intent (or the real comparable).
- **The material keyword can be repurposed** — Dawnbreaker is `WeapMaterialSilver` not for its tier
  but so it deals bonus damage to undead.
- **Tempering is carried as a keyword** (`REQ_Tempering_LegendaryBlacksmithing`) plus
  `MagicDisallowEnchanting`. Read the artifact comparable and replicate the whole keyword set; the
  per-artifact effects live in `…\Reqtificator\documentation\Artifacts.md`.

When patching a new unique, mirror this skeleton from the closest `REQ_Artifact_*` and let the
enchantment and value be bespoke — but say so explicitly rather than silently inventing numbers.

## 4 — Identifying an artifact (a negative): Moonblade

A named, lore-flavoured unique that is **not** a Requiem artifact — the case that shows why
record heuristics can't be trusted and the acquisition trace decides.

`Moonblade - Johnskyrim.esp` ships four WEAP: `JS_Moonblade1H` / `JS_Moonblade2H` (enchanted) and
`_NoEnch` plain forms. The enchanted ones carry `MagicDisallowEnchanting` and a bespoke
`ObjectEffect` (`MBEnchFX`) — the exact heuristics that *tempt* an "artifact" call.

Run the acquisition trace before deciding:

```
housecarl_cross_plugin_query type="ConstructibleObject" references="000807:Moonblade - Johnskyrim.esp"
  -> JS_Moonblade1HRecipe, JS_Moonblade1HTemper        # craftable + temperable
housecarl_cross_plugin_query type="LeveledItem"          references="000807:Moonblade - Johnskyrim.esp"
  -> 0 matches                                          # not in loot lists
```

Both forms (enchanted and plain) have **forge recipes** and are in **zero leveled lists**. A real
artifact is the inverse — not craftable, reached as a single fixed reward. So Moonblade is
**custom craftable gear**, and the `MagicDisallowEnchanting` signal was a false lead.

Patch decision (material keyword = `WeapMaterialEbony`):
- **Value tracks the material keyword → Ebony tier:** 1H 1800, 2H 3600 — for all four records (no
  artifact premium; the enchanted form gets no gold bump over the plain one).
- **Crit = floor(Damage/2):** 1H fixed 6 → 7 (damage 14); 2H 11 already correct (damage 23).
- **Speed normalised to the type standard:** 1H 1.1 → 1.0, 2H 0.8 → 0.75.
- Damage 14/23 left as-is — a +1 offset from Ebony (13/22) is within tolerance; don't rewrite a
  sensible author value unprovoked.
- Keywords already correct (`WeapMaterialEbony` + weapon-type + `VendorItemWeapon`), so the
  Reqtificator assigns `slash` from the weapon-type keyword; no damage-type keyword added.

Lesson: `MagicDisallowEnchanting` + a unique name nominate an artifact candidate; the
craftable/leveled-list trace overrules them.
