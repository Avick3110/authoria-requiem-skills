# Food, drink & cooking

`Requiem - Food and Beverages Redone.esp` makes food matter: meals give **long-lasting, stacking
buffs by nutrition category**, and the economy is closed (alchemists don't buy food; innkeepers sell
meat only as stews).

## Nutrition categories (the effect each gives)

| Category | Effect |
|---|---|
| Vegan | +40% disease resistance |
| Meat | +15 carry weight |
| Flour | +1 stamina regen/sec |
| Dairy | +1 magicka regen/sec |
| Sweets | +50 magicka & stamina (instant restore) |

Magnitude is equal across types; **duration** depends on the meal (stews last longer than raw/simple
food — a deliberate nudge toward cooking). Mammoth snout is ~33% more nourishing than normal meat;
fish is 5× more nourishing for Argonians (racial affinity). Healing poultices give +100% health regen.

## Drink & alcohol

Alcohol does **not** dispel food buffs but causes tumbling if your alcohol level exceeds your base
health. Skooma is reworked (euphoria → lethargy; far milder detriment for Khajiit). The White Phial
refills every 15 min / on sleep and rotates 5 strong buffs. Sleeping Tree Sap yields more than vanilla.

## Integration recipes

- **New food/drink** → ALCH records with a single category effect; route the effect via
  `requiem-magic-patching` using the category's MGEF, value off the comparable (`economy.md`). Tag it
  to the right nutrition category so it stacks/contributes correctly. Don't give food a vanilla
  restore-health-on-eat effect — Requiem food is buff-over-time, not instant heal (except sweets'
  instant restore).
- **A cooking recipe** → a `CraftingCookpot` COBJ; mirror Requiem's food recipes.
- **Alchemists buying food / instant-heal food** are the two anti-patterns to avoid (they break the
  survival economy). Keep food out of alchemist vendor lists.

Some fortify-skill consumables overlap `Requiem - Alchemy Redone.esp`; read the live winner before
cloning.
