# Routing table — record type → domain skill (grep reference)

The operational dispatch map. Enumerate the plugin with `cross_plugin_query plugins=["<NewMod>.esp"]`,
group by type, route each group.

## By record signature

| Signature | Catalog name | Skill |
|---|---|---|
| `WEAP` | Weapon | `requiem-weapon-patching` (melee, bow/crossbow frame, staff frame) |
| `COBJ` (weapon/armor/ammo recipe) | ConstructibleObject | the item's skill (weapon/armor/ammo) |
| `ARMO` | Armor | `requiem-armor-patching` (incl. clothing/jewelry AR-0 frames) |
| `AMMO` | Ammo | `requiem-ammo-patching` |
| `PROJ` | Projectile | ammo PROJ → `requiem-ammo-patching`; spell PROJ → `requiem-magic-patching` |
| `RACE` | Race | `requiem-race-patching` |
| `ARMA` | ArmorAddon | `requiem-race-patching` (race-add) / `requiem-armor-patching` (coverage) |
| `NPC_` | Npc | `requiem-npc-patching` |
| `LVLI` | LeveledItem | `requiem-leveled-list-patching` |
| `LVLN` | LeveledNpc | `requiem-leveled-list-patching` |
| `CONT` | Container | `requiem-leveled-list-patching` (incl. vendor chests) |
| `ECZN` | EncounterZone | `requiem-leveled-list-patching` |
| `MGEF` | MagicEffect | `requiem-magic-patching` |
| `SPEL` | Spell | `requiem-magic-patching` (incl. abilities, shouts' tier spells) |
| `ENCH` | ObjectEffect | `requiem-magic-patching` |
| `BOOK` | Book | spell tomes → `requiem-magic-patching`; multi-spell tome script → `requiem-script-patching` |
| `SCRL` | Scroll | `requiem-magic-patching` |
| `EXPL` | Explosion | `requiem-magic-patching` |
| `HAZD` | Hazard | `requiem-magic-patching` |
| `INGR` | Ingredient | `requiem-magic-patching` + `references/alchemy.md` constraints |
| `ALCH` | Ingestible | potions/poisons → `requiem-magic-patching` + `alchemy.md`; food → `food.md` |
| `SHOU` / `WOOP` | Shout / WordOfPower | `references/shouts.md` (wrapper) + `requiem-magic-patching` (tier spells) |
| `PERK` | Perk | `references/perks-skills.md` (hook into existing trees) |
| `VirtualMachineAdapter` on any record | — | `requiem-script-patching` |

## By gap mechanic (apply alongside the domain skill)

vampire/werewolf → `vampirism-lycanthropy.md` · disease → `diseases.md` · stamina/attacks →
`exhaustion-stress.md` · sneak/lockpick/trap → `stealth.md` · ingredient/potion/poison →
`alchemy.md` · food/drink → `food.md` · shout → `shouts.md` · standing stone → `standing-stones.md` ·
perk/skill → `perks-skills.md` · merchant/value → `economy.md` · any combat "why" →
`combat-resistance.md`.

## Skip / cosmetic (the Reqtificator auto-merge handles)

NPC appearance (head/face), race visuals, voice/dialogue, meshes/textures, and pure-flavor records.
Patch only **balance** fields; the `actorVisualAutoMerge` / `raceVisualAutoMerge` passes reconcile
appearance. Don't write appearance from a balance patch.
