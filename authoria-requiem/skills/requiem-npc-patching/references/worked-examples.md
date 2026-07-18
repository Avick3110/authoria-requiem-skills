# Worked Examples

Three real cases mined from the live load order. They show the identification → analogue → apply loop
for the main patch-classes. FormIDs are live (verify before reuse).

## 1 — Generic humanoid enemy: a modded bandit → a Requiem tier

**The Requiem bandit ladder (live).** Requiem's melee bandits form a fixed-level power ladder, all
sharing `Class REQ_Class_Bandit_SwordShield 85BCE3`, `CombatStyle 03BE1B`, `Race NordRace 013746`,
`DeathItem 0C3C9B`, flags `Respawn, AutoCalcStats, LoopedScript, LoopedAudio`, and one
`REQ_Trait_Tempering_*` ActorEffect. Only level, perk count, gear, and template-base escalate:

Those flags report the live analogue; they are not a bundle to transplant. Preserve the target's
unrelated bits, apply the plugin-wide AutoCalc choice, and never add `LoopedScript`/`LoopedAudio`
merely because a comparable carries them.

| Tier | Level | Perks | Outfit | Template base |
|---|---|---|---|---|
| 01 | 3 | 5 | 9336AC | 039CFD (vanilla) |
| 02 | 7 | 9 | 9336AD | `REQ_Bandit_Template_SwordShield_02` 86837F |
| 04 | 12 | 12 | 9336AF | 899DD7 |
| 05 | 19 | 18 | 9336B0 | 899DD8 |
| 06 | 24 | 18 | 9336B1 | 899DD9 |

**Patching a modded sword-and-board bandit:** identify the role (melee 1H + shield bandit) → pick the
matching tier by intended strength → either **template onto** `REQ_Bandit_Template_SwordShield_<tier>`
(inherit stats + spell list + perks), or replicate the row's fields (fixed level, the class, the
outfit, the tier's perks, the tempering trait). **Remove `PCLevelMult`, set the flat level.** If the
mod shipped `BanditA01..05` leveling-copies, collapse them all to **one** tier (identical, fixed).

## 2 — Creature trait bridge

**Recognized race → touch almost nothing.** Requiem's `EncDraugr02MissileHeadM00` (race
`DraugrRace 000D53`, recognized) carries 14 **vanilla** `Skyrim.esm` combat perks and **not** the
draugr trait perk `031285` — the Reqtificator adds that by race match at build. Its toughness comes
from the RACE's trait spells (engine-inherited) + the build-time perk. So a modded draugr **on the
draugr race** needs only its fixed level + explicit Health/Stamina offsets + the comparable's combat
perks; **don't stamp the trait perk**, and don't replicate the race's armor/resist spells onto the
NPC. (Requiem's `EncWolf` is *identical to vanilla* for the same reason.)

**New/unrecognized race → bridge it.** A modded creature on a brand-new race gets no trait perk from
the auto-pass. Classify it to the nearest Requiem creature (by Name + Keywords) and either retarget
its `Race` to the recognized analogue, or hand-add the physique perk (e.g. a troll-type → fur
`000805:Requiem - Resist and Regen Tweak.esp`). Keep the analogue in sync with whatever
`requiem-race-patching` chose for that race. See `trait-bridge.md`.

## 3 — Boss: dragon priest

**Generic boss (`EncDragonPriest 023A93`):** fixed **Level 50**, flags `Respawn, BleedoutOverride`,
**`MagickaOffset 1300`** (caster), `Class 01CE1C`, 15 perks (14 vanilla + the Requiem conjuration
node `REQ_Conjuration_Empower_050_CognitiveFlexibility1 185736`), and an `ActorEffect` carrying
**`REQ_Trait_ResistMagic30 ADDDC7`** plus the priest's shouts. The Requiem trait + the big magicka
offset + the high level are what make it a boss rather than a generic caster.

**Named unique (`DunForelhostDragonPriestRahgot 035351`):** flags `Unique, BleedoutOverride`, same
Level 50 + MagickaOffset 1300, and it **templates onto** `EncDragonPriestFrost 02025A`
(`TemplateFlags = Traits, Stats, Factions, SpellList, …`) to inherit the generic boss's stats/spells
while keeping its **own** perks + the same `REQ_Trait_ResistMagic30` + its signature spell.

**Patching a modded boss:** recognize it (Unique flag, named, high level, big stat offset, boss combat
style) → set a high fixed level + `Unique` (+ `Protected` only if a quest needs it) + the role stat
offset → strong role class + style → a rich perk set (vanilla + Requiem skill perks) → a `REQ_Trait_*`
for signature toughness (**reuse** `REQ_Trait_ResistMagic30` or author a bespoke `REQ_Trait_<Boss>`;
design its MGEF in the `requiem-magic-patching` skill). Optionally template a named unique onto its generic boss. **Never
template a boss onto a generic mob base.**

## 4 — Follower (record-side): housecarl pattern

Requiem's `HousecarlWhiterun 0A2C8E` (live winner `USMP - Requiem.esp`): **`Level = PcLevelMult`
KEPT** (CalcMin 6 / CalcMax 50 — followers scale with the player), flags `Unique, Protected,
AutoCalcStats`, follower factions `PotentialFollowerFaction 05C84E` (rank -1) + `CurrentFollowerFaction
05C84D` (rank 0), Requiem outfit `99B7FF`, 7 combat perks (5 vanilla + 2 Requiem follower perks). A
modded follower keeps the scaling level and role shape, while its `AutoCalcStats` bit follows the
one plugin-wide stat-authority choice. Its runtime registration (affinity/dialogue) is
**script-side** → route to the `requiem-script-patching` skill (`followers.md`).
