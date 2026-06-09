# Worked Examples

Three real cases, all derived from live Requiem comparables via houseCARL.

## 1 — A plain tiered bolt from the same-material arrow (Orcish Bolt)

You have an arrow comparable but need the bolt — the arrow→bolt rule. Derive `REQ_Bolt_Orcish`
from `REQ_Arrow_Orcish` (`0139BB`: Damage 80, Value 3, AmmoWeight Heavy, AP Arrow Tier3).

**Derivation:**
- Damage = arrow × 1.2 = 80 × 1.2 = **96**.
- Value ≈ arrow × 1.2 ≈ **4** (small values round; prefer the comparable's exact figure).
- Weight = **0**.
- `Flags` = **0** (bolt).
- AmmoWeight class is unchanged by type → **Heavy `9D1F4B`**.
- AP series swaps Arrow→Bolt at the **same tier number** → Tier3 `REQ_ArmorPiercingBolt_Tier3
  AD397F`.
- Material keyword `WeapMaterialOrcish 01E71C`, `VendorItemArrow 0917E7`,
  `RFTI_Exclusions_NoDamageRescale AD3B2D`.

**Live winner `REQ_Bolt_Orcish` (899DB9):** Damage 96, Value 4, Weight 0, keywords
`[WeapMaterialOrcish, VendorItemArrow, REQ_AmmoWeight_Heavy, REQ_ArmorPiercingBolt_Tier3,
RFTI_NoDamageRescale]`. **Exact match** — the ×1.2 damage rule, the unchanged weight class, and the
Arrow→Bolt tier swap all reproduce the real record.

## 2 — Elemental ammo: Steel Arrow of Fire

A mod adds "Flaming Steel Arrows" with 90 damage and a fire effect baked into the arrow's damage.
Bring it onto Requiem's elemental-ammo model. Comparable: `REQ_Ench_Arrow_Steel_Fire` (`0E958F`).

**What the comparable shows:**
- Damage **50** — the *base steel-arrow damage, unchanged*. Requiem does **not** add the elemental
  bonus to the AMMO damage.
- Value **11** (vs plain steel 1) — a modest enchant bump, not a tier jump.
- The fire hit lives on the **projectile** `0E95A7` (a custom PROJ carrying an `Explosion`), not on
  `Damage`.
- Keywords are the steel-arrow set plus the enchant marker.

**The patch:**
- Set Damage **50** (steel base), Value **11**, Weight 0.
- Keywords = the steel-arrow set (`WeapMaterialSteel`, `VendorItemArrow`, `REQ_AmmoWeight_Medium`,
  `REQ_ArmorPiercingArrow_Tier1`, `RFTI_NoDamageRescale`).
- Point `Projectile` at the elemental PROJ that carries the fire explosion.
- **Route the effect design onward:** the magnitude/shape of the fire explosion (the `Explosion` +
  its `MGEF`) is the `requiem-magic-patching` skill's job. This skill sets the AMMO frame and links the projectile; it
  does not design the explosion. Note it for the magic-patching pass.

The mistake to avoid: folding the elemental bonus into `Damage` (90). In Requiem the arrow does its
material damage and the element is a separate on-hit explosion — keep them separate.

## 3 — A modded "heavy war arrow": choosing the weight class and fixing the projectile

A mod adds a chunky black-iron war arrow meant for heavy war bows, shipped at Damage 25 (vanilla-
ish), Weight 0.2, Value 10, with its own projectile that flies supersonic at vanilla speed.

**Judgment calls:**
- **Tier:** the author intends a heavy, hard-hitting arrow above steel but it's "iron-ish" black
  iron. Treat as a high tier by intent — say, ebony-class (Damage 100). State the assumption; if
  the user wants it steel-tier, that's their call.
- **Weight class:** it's a dense, heavy arrow for war bows → `REQ_AmmoWeight_Massive 9D1F4C` (or
  Heavy), **not** chosen from damage but from the heft the author describes. This is the bow-tier
  pairing — it tells Requiem the arrow needs a strong draw.
- **AP tier:** match the chosen tier → ebony-class `REQ_ArmorPiercingArrow_Tier5 0FD2AE`.

**The fixes:**
- Damage **100**, **Weight 0** (Requiem ammo never carries weight — the heft is the keyword),
  Value onto the ebony tier (**22**).
- Keywords: `WeapMaterialEbony 01E71E`, `VendorItemArrow 0917E7`, `REQ_AmmoWeight_Massive 9D1F4C`,
  `REQ_ArmorPiercingArrow_Tier5 0FD2AE`, `RFTI_Exclusions_NoDamageRescale AD3B2D`.
- **Projectile:** the modded PROJ flies wrong (vanilla speed + Supersonic). Either point the AMMO at
  `REQ_Projectile_Arrow_Iron 03BE11` (if the custom mesh isn't needed) or patch the modded PROJ to
  `Speed 3600 / Gravity 0.35 / Flags CanBePickedUp` (arrow profile, drop Supersonic).

This is the case that exercises all three independent ladders at once (damage, AP tier, weight
class) plus the projectile fix — and shows the weight class is an authorial-intent call, not a
formula.
