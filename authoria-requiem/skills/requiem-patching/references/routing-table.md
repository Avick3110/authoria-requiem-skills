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
| `INGR` | Ingredient | `requiem-consumable-patching` (+ `references/alchemy.md` constraints) |
| `ALCH` | Ingestible | `requiem-consumable-patching` — all classes: potions/poisons/oils, food, drink/alcohol, drugs (+ `alchemy.md`/`food.md` constraints) |
| `SHOU` / `WOOP` | Shout / WordOfPower | `references/shouts.md` (wrapper) + `requiem-magic-patching` (tier spells) |
| `PERK` | Perk | `requiem-perk-assignment` — disposition every one (leave plumbing / fold obvious duplicates / flag overlaps with a question); NPC perk derivation lives there too (+ `references/perks-skills.md` system constraints) |
| `FLST` | FormList | route by the list's *contents'* domain (a FLST of weapons → `requiem-weapon-patching`); the list record itself isn't rebalanced |
| `KYWD` | Keyword | no standalone patch — a keyword rides the domain of the records that carry it; disposition it with its carriers |
| `QUST` | Quest | `requiem-script-patching` for result scripts / aliases only; the quest record carries no Requiem balance to rebalance |
| `CELL` / `REFR` | Cell / placed reference | usually placement/cosmetic → skip with a reason; route the placed record's own type if it drops a combatant or container; zone balance → `requiem-leveled-list-patching` (`ECZN`) |
| `GMST` | GameSetting | Requiem owns the global tuning — skip a mod's GMST unless it's flagged for review; never carry a value that fights Requiem's settings |
| `ACTI` / `MISC` / `FURN` / `IDLE` | Activator / MiscItem / Furniture / IdleAnimation | usually flavor/cosmetic → skip with a reason; route only when it carries a balance field (a `MISC` with a gold value → `economy.md`) |
| `VirtualMachineAdapter` on any record | — | `requiem-script-patching` |
| *(any type not listed above)* | — | no owner by default → **flag to the user** (catch-all below); never drop on sight |

## By gap mechanic (apply alongside the domain skill)

vampire/werewolf → `vampirism-lycanthropy.md` · disease → `diseases.md` · stamina/attacks →
`exhaustion-stress.md` · sneak/lockpick/trap → `stealth.md` · ingredient/potion/poison →
`alchemy.md` (records → `requiem-consumable-patching`) · food/drink → `food.md` (records →
`requiem-consumable-patching`) · shout → `shouts.md` · standing stone → `standing-stones.md` ·
perk/skill → `perks-skills.md` (records + NPC perk sets → `requiem-perk-assignment`) ·
merchant/value → `economy.md` · any combat "why" →
`combat-resistance.md`.

## Catch-all disposition — no type is silently dropped

Every enumerated record type lands in exactly one disposition:

1. **Routed** to a domain skill (the signature table above), or
2. **Handled** via a gap-mechanic reference (the list above), or
3. **Skipped as cosmetic** with a stated reason (the section below), or
4. **Flagged "no owner"** to the user — an explicit "this type has no home here; here's what it is and
   what I'd need to disposition it" beats a silent skip.

A type absent from the tables above, or one you don't recognize, takes route 4 by default — surface it,
never drop it on sight. Silence is the failure this rule exists to kill: an unlisted type falling out
of the worklist with no disposition is exactly the "entire record types skipped" field failure. The
`requiem-patching` skill's integration checklist gates on this with a top-level reconciliation —
(routed + patched) + (skipped with a reason) + (flagged no-owner) = total enumerated.

## Skip / cosmetic (the Reqtificator auto-merge handles)

NPC appearance (head/face), race visuals, voice/dialogue, meshes/textures, and pure-flavor records.
Patch only **balance** fields; the `actorVisualAutoMerge` / `raceVisualAutoMerge` passes reconcile
appearance. Don't write appearance from a balance patch.
