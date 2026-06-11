# Crafting & Tempering Recipes

A Requiem armor has up to three `ConstructibleObject` (COBJ) recipes. Find a comparable's recipes
by reverse-lookup on the armor FormID:

```
housecarl_cross_plugin_query type="COBJ" references="013952:Skyrim.esm" conflict_tree=true
```

This returns every COBJ whose `CreatedObject` (or input) is that armor. Ignore non-Requiem recipes
(e.g. `Starting Choices`) unless they are explicitly in scope.

## The three recipe kinds

Distinguished by `WorkbenchKeyword`. **Note the armor-specific difference: tempering happens at
the armor workbench, not the grindstone weapons use.**

| Recipe | WorkbenchKeyword | FormID | Purpose |
|---|---|---|---|
| Forge | CraftingSmithingForge | 088105:Skyrim.esm | craft the armor from ingots |
| Temper | CraftingSmithingArmorTable | 0ADB78:Skyrim.esm | improve the armor (1 ingot) |
| Smelter | CraftingSmelter | 0A5CCE:Skyrim.esm | melt the armor back to ingots |

## Worked recipe — Steel Cuirass (`REQ_Forge_Heavy_Steel_Body`, 0DD975:Skyrim.esm)

```
WorkbenchKeyword = 088105:Skyrim.esm           # forge
CreatedObject    = 013952:Skyrim.esm           # the Steel Cuirass
CreatedObjectCount = 1
Conditions[0] = HasPerk(<smithing perk>)  EqualTo 1.0  RunOnType=Subject
Items:
  05ACE4:Skyrim.esm x2   # Iron Ingot
  05ACE5:Skyrim.esm x5   # Steel Ingot
  0800E4:Skyrim.esm x4   # Leather Strips
```

Tempering (`REQ_Temper_Heavy_Steel_Body`, 0EB853:Skyrim.esm):

```
WorkbenchKeyword = 0ADB78:Skyrim.esm           # armor workbench (NOT grindstone)
CreatedObject    = 013952:Skyrim.esm
Conditions[0] = HasPerk(<smithing perk>)  EqualTo 1.0
Items:
  05ACE5:Skyrim.esm x1   # Steel Ingot
```

Smelter (`REQ_Smelter_Heavy_Steel_Body`, 3CB768:Requiem.esp): `WorkbenchKeyword = 0A5CCE`,
`CreatedObject = 05ACE5 x2` (melts the cuirass into 2 Steel Ingots).

## The perk gate is a HasPerk condition

Both forge and temper recipes carry a single condition: `Function = HasPerk`,
`CompareOperator = EqualTo`, `ComparisonValue = 1`, `RunOnType = Subject`. The perk lives in
`Conditions[0].Data.Perk`; houseCARL 1.2.2+ renders it as a readable FormID (older builds showed
`(floi: form mode but no readable FormKey)`).

**Author by cloning, not guessing.** Read the comparable recipe's `Conditions` to get its perk,
then compose the same gate onto the new recipe (the two-op compose grammar — `ConditionFloat`
shell, then its `Data` arm — is in `housecarl-recipes.md` § E), and swap only `CreatedObject` and
`Items`. That reproduces the exact perk gate without picking a perk by hand.

Which perk a set maps to is the `tempering.*` map in `ArmorKeywordAssignments_Requiem.esp.conf`
(see `keywords.md` § Tempering) — but you don't pick it manually for the recipe; you clone the
same-material comparable's condition. A steel piece gates behind whatever Requiem's steel armor
recipes use.

## Common ingredient FormIDs (vanilla)

| Item | FormID |
|---|---|
| Iron Ingot | 05ACE4:Skyrim.esm |
| Steel Ingot | 05ACE5:Skyrim.esm |
| Leather Strips | 0800E4:Skyrim.esm |

(Verified from the live Steel Cuirass recipe.) For other materials read the comparable's `Items` — Requiem's input lists (which ingots, how many
strips/leather) are part of what makes the recipe consistent. Don't invent quantities; clone them.

## Authoring shape

See `housecarl-recipes.md` § E for the `create_record` call that builds an armor COBJ with a cloned
condition list and swapped `CreatedObject`.
