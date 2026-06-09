# Economy — value, vendors, barter

Requiem's economy is tight and **record-driven** (no dynamic-pricing JSON/SKSE config — that was a
mis-assumption; verified there is no pricing config in `Requiem - Vendor tweaks.esp`, ~422 records,
all CONT/LVLI/FACT/keyword). Two layers: per-item **value** and per-vendor **stock**.

## Item value (the standardized ladders)

- Weapon prices **round to the nearest 5 gold**.
- **Artifacts** follow a fixed ladder: divine 80,000 / legendary-historic 50,000 / mundane ≤5,000.
- Divine amulets cost 200. Material tiers drive weapon/armor value (jumping hard at glass+); see the
  value ladders in `requiem-weapon-patching` / `requiem-armor-patching`.
- **Derive value from the comparable, not the mod author's number.** A modded "epic sword" priced
  9,999 gets normalized to its material/type comparable's value.

## Vendor stock (record-driven)

`Requiem - Vendor tweaks.esp` curates merchant inventories as **containers** referencing leveled
lists, per vendor NPC: e.g. `REQ_VendorChest_Blacksmith_Whiterun 09CAFD:Skyrim.esm` →
`REQ_CLI_VendorStocks_Weapons_Blacksmith` (LVLI). Pricing emerges from item values + the merchant's
container, not a global config. Barter rules: innkeepers buy at base price (no Speech manipulation);
alchemists don't buy food; Khajiit caravans/fences stock thief gear.

## Integration recipes

- **A new sellable item** → value it via its domain skill's ladder (rounded to 5 for weapons), then
  **place it into the right vendor chest / LVLI** via `requiem-leveled-list-patching` so a merchant
  actually stocks it. Value alone doesn't put it in a shop.
- **A new merchant NPC** → set up the vendor faction + a vendor `CONT` (mirroring a Requiem vendor
  chest) referencing themed LVLIs (`requiem-leveled-list-patching`); route the NPC via
  `requiem-npc-patching`. Don't expect a pricing config to tune them — it's the container + item
  values.
- **An over-priced/over-rich mod merchant** → normalize item values to comparables and re-curate the
  vendor container/LVLI to Requiem's stock conventions (don't leave a vendor selling daedric at level 1).
- **Vendor gold pools** are on the merchant faction/chest — keep them in Requiem's range; don't inflate.

Not yet verified live: if a future job needs fine vendor-gold or buy/sell-multiplier tuning, confirm where
those live (merchant faction `VendorValues` vs game settings) before editing — this pass confirmed
*pricing is record-driven*, but didn't enumerate every gold-pool field.
