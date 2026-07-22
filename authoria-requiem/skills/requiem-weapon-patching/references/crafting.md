# Crafting & Tempering Recipes

A Requiem weapon has up to three `ConstructibleObject` (COBJ) recipes. Find them by reverse-lookup
on the weapon FormID — and `references=` takes a **list**, so look up every comparable at once
rather than one per weapon:

```
housecarl_cross_plugin_query type="COBJ" \
  references=["013986:Skyrim.esm","013989:Skyrim.esm","013987:Skyrim.esm"] \
  fields=["CreatedObject","WorkbenchKeyword"] resolve_names=true format="dense"
```

This returns every COBJ whose `CreatedObject` (or input) is any of those weapons. `resolve_names`
makes each `WorkbenchKeyword` self-identifying (`→ CraftingSmithingSharpeningWheel`), so the recipe
kind reads off the row, and the `matches` column names which weapon each recipe belongs to. Ignore
non-Requiem recipes (e.g. `Starting Choices`) unless in scope.

`cross_plugin_query` has no `depth=`, so `Items` and `Conditions` come back as `[list: N item(s)]`.
Expand them in a second call over the recipe FormIDs you just found:

```
housecarl_batch_record_detail formids=["<the recipe FormIDs>"] \
  fields=["Items","Conditions"] depth=4 resolve_names=true
```

**`depth=4` is the working depth** — `depth=2` shows only element *types* (`[ContainerEntry]`,
`[ConditionFloat]`) and `depth=3` stops before the values. At 4 you get `Items[i].Item.Item`
(→ `IngotSteel "Steel Ingot"`), `Items[i].Item.Count`, and `Conditions[0].Data.Perk` resolved to its
perk name. Two calls cover every comparable in the job.

## The three recipe kinds

Distinguished by `WorkbenchKeyword`:

| Recipe | WorkbenchKeyword | FormID | Purpose |
|---|---|---|---|
| Forge | CraftingSmithingForge | 088105:Skyrim.esm | craft the weapon from ingots |
| Temper | grindstone | 088108:Skyrim.esm | improve the weapon (1 ingot) |
| Smelter | (smelter) | 0A5CCE:Skyrim.esm | melt the weapon back to ingots |

## Disabling an existing recipe

Disable a recipe by setting `WorkbenchKeyword = AD3B01:Requiem.esp`
(`REQ_DisableRecipe`). Do not clear the nullable `WorkbenchKeyword` link and do not set it to null;
Requiem's own disabled COBJ records use the explicit keyword, which keeps the recipe deliberately
unavailable instead of leaving an ambiguous station field.

```
housecarl_set_field formid="<recipe>" field_path="WorkbenchKeyword" \
  value="AD3B01:Requiem.esp" into="<patch>"
```

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
`CompareOperator = EqualTo`, `ComparisonValue = 1`, `RunOnType = Subject`. The perk lives in
`Conditions[0].Data.Perk` — read it with `depth=4 resolve_names=true` (above) and it renders as the
named perk. A shallower read returns only `Conditions[0] = [ConditionFloat]`, which tells you
nothing about the gate.

**Author by cloning, not guessing.** Read the comparable recipe's `Conditions` to get its perk,
then compose the same gate onto the new recipe (the two-op compose grammar — `ConditionFloat`
shell, then its `Data` arm — is in `housecarl-recipes.md` § E), and swap only `CreatedObject` and
`Items`. That reproduces the exact perk gate without picking a perk by hand.

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
