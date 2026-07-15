# Reqtificator Rules — the underivable spec

*Class: LIVING reference (per `standards/HOUSECARL_DOC_HYGIENE.md`) — the Reqtificator rulebook, consulted on demand; update if Requiem/Reqtificator behavior is re-mined.*

*Requiem's auto-patcher (the Reqtificator) re-balances the load order at build time. Its config files encode Requiem's rules as data. We do **not** extend the Reqtificator — we read these rules as the **spec** of what a patched record must look like so the Reqtificator's pass fires correctly on our ESP overrides.*

**Discipline (bundled-or-warn):** the FormIDs and exact values below are transcribed from a Phase 0 read of the config. They are a map, not gospel — **each phase session must open the live config file and confirm the exact FormID/value before writing it into a skill or a patch.** Never invent a keyword or value; if it isn't in the config or a live record, say so.

## Config file locations

Base: `D:\Wabbajack\Authoria-dev\mods\Requiem 6.0.2 - Unbroken Road Bugfix Pack 2\Reqtificator\`

| File | Governs |
|---|---|
| `Config\Requiem\Reqtificator.conf` | Armor-rating hard caps; player record overrides (health/magicka/stamina offsets, spells to remove) |
| `Data\WeaponKeywordAssignments_Requiem.esp.conf` | Weapon damage-type keywords (blunt/pierce/slash/ranged) by weapon type |
| `Data\ArmorKeywordAssignments_Requiem.esp.conf` | Armor-set keywords, ranged-resistance tiers, tempering-perk requirements |
| `Data\ActorAssignmentRules_Requiem.esp.conf` | Perk/spell assignment for player / NPC / race / state / creature |
| `documentation\` | `Changelog.md`, `Races.md`, `Artifacts.md` — design intent, racial specs, unique-item effects |
| `BashTags\Requiem.txt` | Every record type Requiem modifies (see below) |

The `_Requiem.esp` suffix means these are per-plugin rule files (the Reqtificator loads a set keyed by source plugin). We do not author new ones — but the naming explains how addons layer rules.

## The rule grammar (HOCON)

Assignment rules are conditional. A `feature_*` block first clears a category then assigns one option by condition:

- `keywords_none = [...]` — strip these first
- `keywords_any / keywords_all = [...]` — fire if the record has any / all of these
- `keywords_assign = [...]` — keywords to add when the condition matches
- `race_any / race_all`, `perks_assign`, `spells_assign` — same idea for actors

This is why our patches must carry the **right input keywords** (material, weapon-type, armor-set, race) — they are what the Reqtificator's conditions match on.

## Weapons — damage types

Four mutually-exclusive damage-type keywords, assigned by weapon type:

- **blunt** ← mace, warhammer, quarterstaff
- **pierce** ← dagger
- **ranged** ← bow (crossbow handled alongside)
- **slash** ← sword, greatsword, waraxe, battleaxe

Damage-type keyword FormIDs live in `WeaponKeywordAssignments` (`damageTypes.{blunt,pierce,ranged,slash}`, Requiem.esp range ~`AD3954`–`AD3957` — *verify live*). The actual damage *number* is not in the config — it is derived from Requiem's comparable weapon records (live-analogy). Material/tempering keywords come from the armor/tempering map and weapon records.

## Armor — caps, resistance tiers, tempering

**AR hard caps** (`Reqtificator.conf`, `armors.armorRatingThresholds`):

| Part | Heavy | Light |
|---|---|---|
| body (cuirass) | 74 | 62 |
| feet (boots) | 27 | 18 |
| hands (gauntlets) | 27 | 18 |
| head (helmet) | 35 | 26 |
| shield | 54 | 44 |

**Ranged-resistance** = a 5-tier keyword system (`rangedResistance.tier1..tier5`). Heavy armor = tier5 (max). Light armor is tiered by set: low (fur/leather/hide/forsworn…) = tier1, mid (elven/dwarven/scale/chitin…) = tier2, high (glass/dragonscale/ancient-falmer/stalhrim-light) = tier3, faction-leader sets (nightingale/ancient-shrouded/penitus/tg-master) = tier4.

**Tempering perk gates** (`tempering.*`): each armor set maps to one required smithing perk — craftsmanship (default, most sets), advancedBlacksmithing, advancedLightArmors, dwarven/elven/glass/orcish/ebony/daedric/draconic Smithing, legendaryBlacksmithing. The full armor-set → keyword and set → tempering map is in `ArmorKeywordAssignments` (~270 lines). **Read it live per the armor phase.**

## Actors — perks & spells

`ActorAssignmentRules` (~544 lines) assigns:

- **gameMechanics.perks** — applied to *all* actors (armor penetration by damage type, armor weight, arrow recovery, magic/poison rescaling, stress/exhaustion, playable-race perks, artifact-enchant perks).
- **playerExclusive** — perks/spells only the player gets (skill perks: alchemy, enchanting, lockpicking, pickpocket, sneak, speech, tempering; plus utility spells: examine, bag-of-holding, true-yield cloaks…).
- **npcExclusive** — e.g. persistent spell rescaling.
- **racialTraits / stateTraits** — incoming-damage-modifier perks by creature race (atronachs, dragons, undead, dwarven automata, werewolves…) and by state (ghost/spirit/vampire). `keywords.doNotInheritTraits` (`AD3A4F` — *verify*) opts a record out.

## Record types Requiem touches (BashTags)

`Actors.ACBS, Actors.AIData, Actors.CombatStyle, Actors.DeathItem, Actors.Perks.Add/Remove, Actors.Spells, Actors.SpellsForceAdd, Actors.RecordFlags, C.Encounter, EffectStats, EnchantmentStats, Factions, Invent.Add/Change/Remove, Keywords, Names, NPC.AttackRace, NPC.Class, NPC.CrimeFaction, NPC.DefaultOutfit, NPC.Race, Outfits.Add/Remove, R.ChangeSpells, R.Description, R.Skills, R.Stats, Relations.*, SpellStats, Stats, Text.`

This list is the menu of fields an integration patch may need to set, by domain.

## User toggles (informational only — we don't change them)

`DefaultUserConfig.json`: `mergeLeveledLists`, `mergeLeveledCharacters`, `openEncounterZones`, `actorVisualAutoMerge`, `raceVisualAutoMerge`, `verboseLogging`. These shape what the Reqtificator's pass does to our patch downstream (e.g. leveled lists get merged), which the leveled-list phase must account for.
