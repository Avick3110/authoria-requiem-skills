# Crafting & Tempering Recipes

A Requiem armor has up to three `ConstructibleObject` (COBJ) recipes. Find them by reverse-lookup
on the armor FormID — and `references=` takes a **list**, so look up the whole set at once rather
than once per piece:

```
housecarl_cross_plugin_query type="COBJ" \
  references=["013952:Skyrim.esm","013961:Skyrim.esm","013964:Skyrim.esm"] \
  fields=["CreatedObject","WorkbenchKeyword"] resolve_names=true format="dense"
```

This returns every COBJ whose `CreatedObject` (or input) is any of those armors. `resolve_names`
makes each `WorkbenchKeyword` self-identifying (`→ CraftingSmithingForge`), so you read the recipe
kind off the row instead of matching FormIDs by hand, and the `matches` column names which armor
each recipe belongs to. Ignore non-Requiem recipes (e.g. `Starting Choices`) unless in scope.

`cross_plugin_query` has no `depth=`, so `Items` and `Conditions` come back as
`[list: N item(s)]`. Expand them in a second call over the recipe FormIDs you just found:

```
housecarl_batch_record_detail formids=["0DD975:Skyrim.esm","0EB853:Skyrim.esm"] \
  fields=["Items","Conditions"] depth=4 resolve_names=true
```

**`depth=4` is the working depth** — `depth=2` shows only element *types* (`[ContainerEntry]`,
`[ConditionFloat]`) and `depth=3` stops before the values you need. At 4 you get
`Items[i].Item.Item` (→ `IngotSteel "Steel Ingot"`), `Items[i].Item.Count`, and
`Conditions[0].Data.Perk` resolved to its perk name. Two calls cover an entire set.

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
`Conditions[0].Data.Perk` — read it with `depth=4 resolve_names=true` (above) and it renders as
the named perk, e.g. `0CB40D:Skyrim.esm (→ REQ_Smithing_Craftsmanship "Craftsmanship")`. A
shallower read returns only `Conditions[0] = [ConditionFloat]`, which tells you nothing about the
gate.

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
