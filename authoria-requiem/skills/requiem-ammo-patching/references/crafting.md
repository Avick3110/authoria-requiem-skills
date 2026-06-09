# Ammo Crafting + Projectiles

## The forge recipe (uniform across all craftable ammo)

Every craftable Requiem arrow/bolt has exactly one `ConstructibleObject`, and they all share one
shape — read live off `REQ_Forge_Arrow_Steel` (00B650) and `REQ_Forge_Bolt_Iron` (964F3A):

- **`WorkbenchKeyword = CraftingSmithingForge 088105`** (the standard forge).
- **`Items`** = exactly two: **one metal ingot ×1** + **one firewood (`06F993:Skyrim.esm`) ×1**.
- **`CreatedObjectCount = 30`** — one ingot + one log yields 30 arrows (or 30 bolts).
- **`Conditions`** = one `HasPerk` condition (`ComparisonValue 1`, `EqualTo`) — the smithing-perk
  gate. The perk renders as an unreadable form-index (`floi: form mode but no readable FormKey`),
  so **clone the comparable recipe's `Conditions` verbatim** rather than retyping it.

Common ingot FormIDs: Iron `05ACE4`, Steel `05ACE5`, Dwarven `0DB8A2`, Elven (refined moonstone)
`05AD9F`, Orichalcum (orcish) `05AD99`, Glass (refined malachite) `05ADA1`, Ebony `05AD9D`, Gold
`05AD9E`, Quicksilver `05ADA0`, Stalhrim `02B06B:Dragonborn.esm`. **Read the comparable recipe's
`Items` to get the exact ingot** rather than trusting this list.

There is **no separate temper or smelter recipe** for ammo (unlike weapons/armor) — just the one
forge recipe.

Find a comparable's recipe by reverse-lookup on the ammo FormID:

```
housecarl_cross_plugin_query type="ConstructibleObject" references="01397F:Skyrim.esm"
```

(Filter out the `REQ_Forge_Ench_*` results — those are the elemental-ammo recipes.)

Bespoke/quest/creature ammo (Bloodcursed, Soulcairn, Dwarven Sphere Bolt) has **no** forge recipe;
don't add one.

## Projectiles (PROJ)

Every AMMO points at a `Projectile` carrying flight physics, the in-air mesh, and (for elemental
ammo) the impact effect. Requiem **standardizes the physics** and overrides even the vanilla arrow
projectiles (it renames `ArrowIronProjectile` → `REQ_Projectile_Arrow_Iron` and strips the
vanilla `Supersonic` flag from arrows).

| | Speed | Gravity | Flags | Type |
|---|---|---|---|---|
| **Arrow** | 3600 | 0.35 | `CanBePickedUp` (no Supersonic) | Arrow |
| **Bolt** | 5600 | 0.35 | `CanBePickedUp, Supersonic` | Arrow |

Other shared fields: `Range 60000`, `CollisionRadius 0.5`, `ImpactForce 1`. The PROJ `Type` is
`Arrow` even for bolts.

Reference projectiles to reuse or copy from:
- Arrows: `REQ_Projectile_Arrow_Iron 03BE11:Skyrim.esm`, `REQ_Projectile_Arrow_Silver 0FAB0F:Requiem.esp`.
- Bolts: `REQ_Projectile_Bolt_Iron 8B5200:Requiem.esp`, `REQ_Projectile_Bolt_Steel 000BB4:Dawnguard.esm`.

### Two ways to give a new arrow a projectile

1. **Reuse** a tier-matched Requiem projectile (point `Ammo.Projectile` at e.g.
   `REQ_Projectile_Arrow_Iron`) — correct when the new arrow doesn't need a unique flight mesh or
   trail. Cheapest and always Requiem-consistent.
2. **Patch the mod's own PROJ** when the mod ships one (it usually does, for a custom mesh). Bring
   it onto the profile above — modded arrow projectiles commonly keep vanilla's `Supersonic` and a
   non-Requiem speed:

   ```
   housecarl_bulk_apply patch_name="Authoria_Ammo" operations=[
     {formid:"<modded PROJ>", field_path:"Speed",   value:"3600"},
     {formid:"<modded PROJ>", field_path:"Gravity", value:"0.35"},
     {formid:"<modded PROJ>", field_path:"Flags",   value:"CanBePickedUp"}
   ]
   ```

   We care only about **arrow/bolt** projectiles here — leave spell, trap, and other projectile
   types alone.

### Elemental projectiles

An elemental arrow's fire/ice/shock hit lives on its **projectile** (a custom PROJ carrying an
`Explosion`), not on the AMMO `Damage`. The AMMO keeps base-material damage; you link the elemental
PROJ. **Designing** that explosion/MGEF (magnitude, the actual effect) is the `requiem-magic-patching`
skill — this skill links an existing one and routes the design onward.
