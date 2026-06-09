# Enchanted Weapons & Staves

Two special cases that branch off the main workflow. All numbers mined live (2026-06-07).

## Enchanted weapons

An enchanted Requiem weapon is the **plain weapon plus three things**: a `Template` link to the
non-enchanted base, an `ObjectEffect` (the enchantment), and an `EnchantmentAmount` (the charge
pool). It otherwise carries the **same stats and keywords as the base** — including the same gold
value (the enchantment does not raise the price).

Worked example — `REQ_Ench_Weapon_Steel_Sword_Fire2` (04B452:Skyrim.esm), "Steel Sword of Burning":

```
Name             = Steel Sword of Burning
Template         = 013989:Skyrim.esm        # the plain Steel Sword (CNAM base variant)
ObjectEffect     = 045C2A:Skyrim.esm        # REQ_Ench_Weapon_Fire2
EnchantmentAmount = 1000                     # charge pool
BasicStats.Value = 45  (= base)  Damage = 8 (= base)
Keywords         = [WeapMaterialSteel, WeapTypeSword, VendorItemWeapon]  (= base)
```

### The charge-pool rule

The weapon's `EnchantmentAmount` is its total charge; the ENCH's `EnchantmentCost` is the per-cast
cost. Charges ≈ amount ÷ cost. Mined Fire ladder for the Steel Sword:

| Tier | Weapon EnchantmentAmount | ENCH EnchantmentCost | Casts |
|---|---|---|---|
| Fire1 (Embers) | 500 | 10 | 50 |
| Fire2 (Burning) | 1000 | 20 | 50 |
| Fire3 (Scorching) | 1500 | 25 | 60 |

So: **weapon charge pool ≈ 500 × tier**, sized for ~50–60 casts at the tier's per-cast cost.
Read the ENCH cost with:

```
housecarl_read_record formid="045C2A:Skyrim.esm"   # EnchantmentCost on the ObjectEffect
```

Then set the weapon's `EnchantmentAmount` to match the tier (or to `casts × cost` for a custom
enchantment). To author: clone the plain base weapon, add `Template` → base, set `ObjectEffect`,
set `EnchantmentAmount`.

## Staves

A staff is balanced by its **spell, not a material ladder**. The frame is nearly inert:
`Damage = 1`, `Data.AnimationType = Staff`. Its keywords and value come from the bound spell.

Worked example — `REQ_Staff_Destruction3_Fire_AimedExp` (029B82:Skyrim.esm), "Staff of Fireball":

```
Name              = Staff of Fireball
BasicStats        = Value 100, Weight 8, Damage 1
ObjectEffect      = 029B59:Skyrim.esm        # the staff spell
EnchantmentAmount = 2000                       # charge pool
Keywords:
  01E716:Skyrim.esm                  # WeapTypeStaff
  0937A4:Skyrim.esm                  # VendorItemStaff
  0076ED:Requiem - Magic Redone.esp  # Nox_KW_Staff_Destruction3  (school + tier)
```

### Deriving a staff's keywords

The third keyword is an MR `Nox_KW_Staff_<School><Tier>` keyword that encodes the spell's school
(Destruction, Conjuration, Alteration, …) and magnitude tier. Read the staff's `ObjectEffect`,
identify the school and tier of its effect, then find the matching `Nox_KW_Staff_*` keyword that
Requiem's own staves of that school+tier carry:

```
housecarl_cross_plugin_query type="KYWD" editorid_contains="Nox_KW_Staff" plugins=["Requiem - Magic Redone.esp"]
```

Pick the comparable staff (same school + tier) and copy its keyword + value + charge. Because the
staff balance is the spell's job, the live-analogy is "find Requiem's staff of the closest
spell," not "find the closest material."

> Staff *spell/effect design* (authoring a new MGEF/SPEL) is the `requiem-magic-patching` skill's job — this skill
> handles the staff **frame**: keywords, value, charge, and the `ObjectEffect` link to an existing
> Requiem spell.
