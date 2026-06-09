# Crafting & Tempering Recipes

A Requiem weapon has up to three `ConstructibleObject` (COBJ) recipes. Find a comparable's
recipes by reverse-lookup on the weapon FormID:

```
housecarl_cross_plugin_query type="COBJ" references="013989:Skyrim.esm" conflict_tree=true
```

This returns every COBJ whose `CreatedObject` (or input) is that weapon. Ignore non-Requiem
recipes (e.g. `Starting Choices`) unless they are explicitly in scope.

## The three recipe kinds

Distinguished by `WorkbenchKeyword`:

| Recipe | WorkbenchKeyword | FormID | Purpose |
|---|---|---|---|
| Forge | CraftingSmithingForge | 088105:Skyrim.esm | craft the weapon from ingots |
| Temper | grindstone | 088108:Skyrim.esm | improve the weapon (1 ingot) |
| Smelter | (smelter) | 0A5CCE:Skyrim.esm | melt the weapon back to ingots |

## Worked recipe — Steel Sword (`REQ_Forge_Weapon_Steel_Sword`, 0DB5D7:Skyrim.esm)

```
WorkbenchKeyword = 088105:Skyrim.esm           # forge
CreatedObject    = 013989:Skyrim.esm           # the Steel Sword
CreatedObjectCount = 1
Conditions[0] = HasPerk(<smithing perk>)  EqualTo 1.0  RunOnType=Subject
Items:
  05ACE4:Skyrim.esm x1   # Iron Ingot
  05ACE5:Skyrim.esm x2   # Steel Ingot
  0800E4:Skyrim.esm x1   # Leather Strips
```

Tempering (`REQ_Temper_Weapon_Steel_Sword`, 0F46AE:Skyrim.esm):

```
WorkbenchKeyword = 088108:Skyrim.esm           # grindstone
CreatedObject    = 013989:Skyrim.esm
Conditions[0] = HasPerk(<smithing perk>)  EqualTo 1.0
Items:
  05ACE5:Skyrim.esm x1   # Steel Ingot
```

## The perk gate is a HasPerk condition

Both forge and temper recipes carry a single condition: `Function = HasPerk`,
`CompareOperator = EqualTo`, `ComparisonValue = 1`, `RunOnType = Subject`. The perk itself is
stored in `Conditions[0].Data.Parameter1` as a form-index that **does not always render as a
readable FormID** through houseCARL (`(floi: form mode but no readable FormKey)`).

**Author by cloning, not retyping.** Read the comparable recipe and copy its whole `Conditions`
list onto the new recipe verbatim (a `bulk_apply` `ReplaceAll`/`Add` of the conditions), then
swap only `CreatedObject` and `Items`. That reproduces the exact perk gate without needing to
resolve the FormID by hand.

Which perk a tier maps to follows Requiem's smithing tree (the `tempering.*` map in
`ArmorKeywordAssignments_Requiem.esp.conf` is the armor analog): most basic weapons gate behind
Craftsmanship, with material-specific perks (Dwarven/Elven/Glass/Orcish/Ebony/Daedric/Draconic
Smithing, Advanced Blacksmithing, Legendary Blacksmithing) for higher tiers. Always take the gate
from the same-material comparable — a steel weapon's gate is whatever Requiem's steel weapons use.

## Common ingredient FormIDs (vanilla)

| Item | FormID |
|---|---|
| Iron Ingot | 05ACE4:Skyrim.esm |
| Steel Ingot | 05ACE5:Skyrim.esm |
| Leather Strips | 0800E4:Skyrim.esm |

For other materials read the comparable's `Items` — Requiem's input lists (ingots + strips, and
how many) are part of what makes the recipe consistent. Don't invent quantities; clone them.

## Authoring shape

See `housecarl-recipes.md` for the `create_record` call that builds a COBJ with a cloned
condition list and swapped `CreatedObject`.
