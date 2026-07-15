# Alchemy — ingredients, potions, poisons

`Requiem - Alchemy Redone.esp` rebuilds alchemy (~717 records: 183 INGR, 287 ALCH, 85 MGEF). The
governing rule for integration: **new ingredient/potion effects must reuse Requiem's `REQ_Alch_*`
MGEFs**, not vanilla effects — otherwise the effect is unbalanced and ignored by the system.

## The shape

- **Ingredient (INGR):** exactly **4 effects**, each pointing at a `REQ_Alch_*` MGEF, plus a Requiem
  alchemy keyword. Example — Thistle (`0134AA:Skyrim.esm`): effects =
  `REQ_Alch_ResistFrost 03EAEB`, `REQ_Alch_DrainStamina 073F23`, `REQ_Alch_ResistPoison 090041`,
  `REQ_Alch_FortifyArmorRating 03EB1E`. The 4-effect mapping is how the discovery/combination system
  works — keep exactly four.
- **Potion/poison (ALCH):** typically a single `REQ_Alch_*` effect + a keyword. Harmful potions are
  poisons (`MagicAlchHarmful`). Example — `REQ_Potion_FortifyBlock2` → effect `REQ_Alch_FortifyBlock
  03EB1C`.
- **MGEF (`REQ_Alch_*`):** Archetype `PeakValueModifier`, FaF/Self, `Flags=PowerAffectsMagnitude,Recover`,
  `BaseCost ≈ 0.025`, `MagicSkill=None`, carries `MagicAlch*` keywords (`MagicAlchHarmful` = poison).
  Requiem alchemy keywords: `REQ_Alchemy_Oil`, `REQ_Alchemy_FortifySpeed`.
- **Poison rescaling** (`gameMechanics.poisonRescaling 962798 Requiem.esp`) is an all-actor
  Reqtificator output — carry the harmful keyword; don't stamp the rescale.

## Integration recipes

- **A new ingredient** → `requiem-consumable-patching` maps the 4 effects; **point all four at
  existing `REQ_Alch_*` MGEFs** (find them by `cross_plugin_query type=MagicEffect editorid_contains="REQ_Alch"
  plugins=["Requiem - Alchemy Redone.esp"]`). Don't invent a new alchemy MGEF unless the effect truly
  doesn't exist — then its design routes to `requiem-magic-patching` (clone a `REQ_Alch_*` profile).
- **A new potion/poison** → `requiem-consumable-patching`: single `REQ_Alch_*` effect + the right
  keyword; value off Requiem's comparable potion ladder. Place via `requiem-leveled-list-patching` /
  vendor chests.
- **A modded ingredient using vanilla effects** → replace each vanilla effect with the `REQ_Alch_*`
  analogue; otherwise it bypasses Requiem's alchemy economy.
- **Fortify-skill potions** overlap food/Alchemy; some are shared with `Requiem - Food and Beverages
  Redone.esp` or won by `Requiem - Magic Redone.esp` — read the live winner.

The record work (ALCH/INGR shells, ladders, kits, cookpot recipes) is owned by
`requiem-consumable-patching`; this reference carries the system-level constraints.
