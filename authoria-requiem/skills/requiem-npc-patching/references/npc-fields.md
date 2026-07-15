# NPC Balance-Field Standard

The Requiem-balance fields on an `NPC_` record, the values they take by archetype, and what is
source-carried vs Reqtificator-assigned. **Read the live comparable for every number** — this is a
map of the *shape*, not a substitute for the analogue. All FormIDs are `Requiem.esp` unless noted;
verify live.

## Table of contents
- [The two-winner model (balance vs appearance)](#the-two-winner-model-balance-vs-appearance)
- [Configuration: level + flags](#configuration-level--flags)
- [PlayerSkills — per-skill combat values](#playerskills--per-skill-combat-values)
- [Class — the balance spine](#class--the-balance-spine)
- [Combat style](#combat-style)
- [Factions, outfit, death item](#factions-outfit-death-item)
- [ActorEffect — tempering + resist traits](#actoreffect--tempering--resist-traits)
- [Templates — the chain](#templates--the-chain)
- [Archetype quick-reference (sampled live)](#archetype-quick-reference-sampled-live)

## The two-winner model (balance vs appearance)

An `NPC_` record is overridden by **appearance** mods (face/body/tint/headparts — `MOSRefined.esp`,
`TSOSRefined.esp`, NPC overhauls) and by **balance** sources (`Requiem.esp`, `USMP - Requiem.esp`,
Requiem addons). `conflict_tree` shows both; the diff tells you which plugin set which field. Derive
balance from the Requiem winner; **never write the appearance fields** — the Reqtificator's
`actorVisualAutoMerge` merges face/body with Requiem's balance at build. A patch that re-writes the
whole record (or any visual field) buries the appearance winner.

`USMP - Requiem.esp` (Unofficial Skyrim Modders Patch – Requiem) is **in scope** — it carries
Requiem's balance forward over the USMP appearance/bugfix layer and is the live winner for many named
actors (Ahtar, Mjoll, Cosnach, Housecarl Lydia). When `Requiem.esp` reads "identical to winner" under
it, USMP-Requiem is just forwarding Requiem's values.

## Configuration: level + flags

- **Level — fixed, de-levelled.** `Configuration.Level` is an `NpcLevel` with a flat
  `Configuration.Level.Level` integer (sampled: EncWolf 2, EncDraugr 6, bandit tier-4 12, dragon
  priest 50). **When you patch a combatant, set a fixed level and remove `PCLevelMult`.** A
  `PcLevelMult` level (with `CalcMinLevel`/`CalcMaxLevel`) is the player-scaling form — **followers
  keep it** (Lydia is `PcLevelMult`, CalcMin 6 / CalcMax 50); everyone else loses it.
- **`AutoCalcStats`** — **ON for humanoids** (bandits, guards, followers derive stats from
  level + class `StatWeights` + race). **OFF for creatures and bosses**, which carry explicit
  `HealthOffset` / `MagickaOffset` / `StaminaOffset` instead (EncTroll H+350/S+175; dragon priest
  M+1300; Lydia H+50).
- **Flags by role:** `Respawn` on generic spawns and creatures; `Unique` on named actors/bosses;
  `Protected` (and rarely `Essential`) on followers and quest-critical actors; `BleedoutOverride` on
  bosses that should bleed out rather than die. Read the comparable — don't invent flags.

## PlayerSkills — per-skill combat values

`PlayerSkills` holds the actor's per-skill values (one-handed, block, destruction, restoration, …) —
the numbers that, alongside level and perks, decide how hard a de-levelled enemy actually **hits,
blocks, and casts**. A record shipped with a fixed level but wrong skills fights or defends nothing
like its Requiem analogue, so this field is part of "patched," not an afterthought. It pairs with
`AutoCalcStats`:

- **`AutoCalcStats` ON (humanoids — bandits, guards, followers):** `PlayerSkills` are **derived** from
  the actor's `Class` (its skill weightings) + level. Don't hand-stamp them — set the right class and
  level and let the derivation produce the skills; **verify** the read-back rather than authoring values.
- **`AutoCalcStats` OFF (creatures and bosses):** `PlayerSkills` are **explicit** on the record, the
  same as `HealthOffset`/`MagickaOffset`/`StaminaOffset`. Derive them from the live analogue like any
  other stat — read the comparable creature/boss and carry its per-skill values. A caster boss whose
  Destruction/Conjuration skills weren't carried casts far below its intended tier even with the right
  level and `MagickaOffset`.

## Class — the balance spine

Requiem assigns its own `REQ_Class_*` taxonomy by **role + weapon**, replacing the vanilla class. The
class's `StatWeights` (Health/Magicka/Stamina) distribute AutoCalc stats, so the class is what makes
an actor a tank, a glass cannon, or a weakling. Sampled weights:

| Class | EditorID | FormID | H / M / S | Role |
|---|---|---|---|---|
| Bandit sword+shield | `REQ_Class_Bandit_SwordShield` | 85BCE3 | 4 / 0 / 6 | melee bruiser |
| Guard 1H | `REQ_Class_Guard_Onehanded` | 7F6492 | 5 / 0 / 5 | tanky soldier |
| Slighted 1H | `REQ_Class_SlightedOneHanded` | AD3A80 | **1 / 0 / 0** | weak/trash tier |

Other live `REQ_Class_*` families (verify the exact member by role):
`REQ_Class_Bandit_{MaceShield 85BCE1, AxeShield 85BCE2, BattleAxe 86D2E6, Warhammer 86D2E7,
GreatSword 86D2E8, Bow 879915, CrossBow 879916, CrossBowHeavy 879917, Trickster 8A8BE2(Minor Arcana)}`;
`REQ_Class_Guard_{Twohanded 7F6493, Archer 7F6491, Marksman 7F6494, Irileth 93D535}`;
`REQ_Class_Slighted{TwoHanded AD3A81, Ranged AD3A82, Conjurer AD3A83}` (the weak tier);
`REQ_Class_Vigilant_*`, `REQ_Class_CommanderCaius 9182CB`, `REQ_Class_Haldyn ADE440`; and
`FZR_Class_*` (cultist summoner/mage, ashspawn, hagrava, frostmoon, citizen — a Requiem addon block).
**Map the modded NPC's intended role to the nearest class; the Slighted tier is the weak-mob option.**
Some modders apply a generic/Dremora class to everything — trust the gear + combat style + name over
a lazy class.

## Combat style

Requiem mostly **reuses vanilla `CSTY`** (e.g. `03BE1B`, `068848`) and adds a handful:
`REQ_CombatStyle_Guard_{Sword 91AA67, Mace 814000, Ranged 91AA66}`, `REQ_CS_AllAroundWarrior ADDDE7`,
`REQ_CS_SlightedArcher AD3ADD`, `REQ_CombatStyle_CommanderCaius 91AA68`. Pick the role's style; for
most generic enemies the vanilla style the analogue uses is correct.

## Factions, outfit, death item

- **Factions** — Requiem keeps the actor's gameplay factions; you generally **don't patch factions**
  (out of scope) except the record-side **follower** factions (`references/followers.md`).
- **DefaultOutfit** — Requiem swaps to a `REQ_*` outfit; **gear is a large part of the difficulty**
  (the bandit tiers differ mainly by outfit + perks). Link the analogue's outfit; its *contents* are
  a concern for the `requiem-leveled-list-patching` skill.
- **DeathItem** — links a leveled list (loot). Link the analogue's; the list's *contents* → the
  `requiem-leveled-list-patching` skill.

## ActorEffect — tempering + resist traits

The `ActorEffect` list holds **source-carried** Requiem ability spells:

- **`REQ_Trait_Tempering_<role>_<tier>`** — a "GM" marker that tempers the NPC's worn gear to a rank
  (bandit `REQ_Trait_Tempering_Bandit_Heavy_Rank4 93369F`, guard `REQ_Trait_Tempering_Guard_Soldier
  929824`). Match the analogue's tempering trait to its tier/armor-class.
- **`REQ_Trait_ResistMagic30 ADDDC7`** and similar resist traits — carried on casters/bosses (dragon
  priests). Reusable as a **boss buff** (see `identification.md`).

These are abilities Requiem *places on the NPC*, distinct from the race trait spells (which live on
the RACE and are engine-inherited). Don't confuse them with Reqtificator-assigned perks.

## Templates — the chain

Requiem's actors use a template chain:

- **`REQ_LookTemplate_*`** — the appearance base. Its `TemplateFlags` inherit `Stats, SpellList,
  Factions, AIData…` from its `Template` but **omit `Traits`**, so it keeps its own race/appearance.
- **`REQ_<Role>_Template_<Weapon>_<Tier>`** — the balance base that carries class + perks + factions
  per tier. The full bandit set: `REQ_Bandit_Template_<Weapon>_{Base,01,02,03}` for
  `{MaceShield, SwordShield, AxeShield, Greatsword, Warhammer, Battleaxe, Bow, Crossbow}`
  (e.g. `SwordShield_Base 86837D / _01 86837E / _02 86837F / _03 868380`). Tier ↑ = stronger.
- **Named bosses** template onto the **generic boss** of their type (`Rahgot` → `EncDragonPriestFrost`)
  with `Traits, Stats, SpellList…` inherited, while keeping their own perks + signature `REQ_Trait_*`.

To integrate a new generic enemy: set `Template` to the matching `REQ_<Role>_Template_<Weapon>_<Tier>`
and `Configuration.TemplateFlags` to inherit `Stats` + `SpellList` (+ Factions/AIData as the analogue
does). Everything Requiem-balanced then flows from the base.

## Archetype quick-reference (sampled live)

| Archetype | Level | AutoCalc | Flags | Class | Perks (source) | ActorEffect | Notes |
|---|---|---|---|---|---|---|---|
| Bandit (tiered) | fixed 6–12+ | on | Respawn(+AutoCalc) | `REQ_Class_Bandit_*` | vanilla combat ×5→12, escalating + 1 REQ skill perk | `REQ_Trait_Tempering_Bandit_*` | templates onto `REQ_Bandit_Template_*` |
| Guard | fixed | on | Respawn | `REQ_Class_Guard_*` | ~17 (heavily perked) | `REQ_Trait_Tempering_Guard_*` | `REQ_CombatStyle_Guard_*` |
| Caster/mage | fixed | on | Respawn | mage/`FZR_Class_Cultist_*` | vanilla magic + `REQ_Conjuration_*`/destruction perks | resist trait | big `MagickaOffset` |
| Creature (recognized race) | fixed low | **off** | Respawn | vanilla creature class | 0–1 | absent (race carries traits) | Reqtificator adds trait perk; touch almost nothing |
| Follower | **PcLevelMult kept** | on | Unique, Protected | role class | ~7 + 2 REQ perks | — | follower factions (see followers.md) |
| Boss | fixed high (≈50) | off | Unique/Respawn, BleedoutOverride | strong role class | rich, + REQ skill perks | `REQ_Trait_*` (e.g. ResistMagic30) | big stat offset; never template onto a generic base |
| Civilian / vendor / child | — | — | — | `*Citizen`/`Job*`/child | — | — | **skip — not a combatant** |
