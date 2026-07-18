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
- **`AutoCalcStats`** — choose its authority **once with the user for the plugin**, before any stat
  write. AutoCalc mode ensures the flag is on and leaves `PlayerSkills` plus all three ACBS stat offsets
  untouched; class + level remain authoritative. Manual mode removes the flag everywhere that
  enters the stat pass, then writes the complete DNAM + ACBS block from the analogue. Do not mix
  modes record by record. The analogue's own flag is evidence about how Requiem built that actor,
  not permission to contradict the user's plugin-wide choice.
- **Flags by role:** `Respawn` on generic spawns and creatures; `Unique` on named actors/bosses;
  `Protected` (and rarely `Essential`) on followers and quest-critical actors; `BleedoutOverride` on
  bosses that should bleed out rather than die. Read the comparable — don't invent flags.

## PlayerSkills — base attributes, skill values, skill offsets (the DNAM block)

`PlayerSkills` is the NPC record's DNAM block and carries **three distinct balance payloads**, each
addressable as a dotted path (`PlayerSkills.Health`, `PlayerSkills.SkillValues[OneHanded]`,
`PlayerSkills.SkillOffsets[Conjuration]`). Requiem hand-sets all three on its own actors, so a
patched combatant that keeps its vanilla DNAM is **half-patched** — field reports repeatedly named
exactly this omission. All values below verified live 2026-07-17.

- **`PlayerSkills.Health` / `.Magicka` / `.Stamina` — the base attributes.** These are **not** the
  ACBS `Configuration.*Offset` fields — they are the DNAM base stats the offsets stack on, and they
  escalate with tier: bandit tiers H 104→118 / S 106→127 (M pinned 100), warlock H 50→142 /
  M 100→158, draugr H 300→387, wolf 85/0/205, dragon priest H 1490 / M 545. Mirror the analogue's
  numbers on every combatant you patch.
- **`PlayerSkills.SkillValues[<Skill>]` — the 18 per-skill values.** The actor's combat skills
  escalate with tier while irrelevant skills stay pinned at 5 (bandit 1H/Block/HeavyArmor
  10→21→29). On Requiem's named civilians the same pass runs **downward** (skills pulled to 5–20)
  — the stat pass corrects in both directions. These decide how hard a de-levelled enemy hits,
  blocks, and casts; a caster whose Destruction/Conjuration values weren't carried casts far below
  its tier even with the right level and `MagickaOffset`.
- **`PlayerSkills.SkillOffsets[<Skill>]` — the per-level scaling knob.** Zero on generic templated
  mobs; **actively used on named/unique humanoids and leveled summons** (11 actors verified:
  Alain Dufont 1H/Block offset 100, Warlock01 Conjuration offset 30, General Tullius, Soul Cairn
  wrathmen…). Carry the analogue's offsets when patching a named/scaling actor; leave 0 on
  generic mobs. The one-call finder for actors that use them:
  `housecarl_cross_plugin_query plugins=["<plugin>"] type="NPC_"
  where=["PlayerSkills.SkillOffsets[OneHanded] > 0"]` (bracket paths work in `where=`).

**Apply these fields only in manual mode.** Copy the analogue's full DNAM and ACBS payload after
removing `AutoCalcStats`; a partial explicit block is not a balance model. In AutoCalc mode, do not
"keep the record consistent" by stamping the analogue's visible DNAM values — those writes create a
second, contradictory stat authority and were the source of plugin-wide inconsistency.

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

## Archetype quick-reference (sampled live 2026-07-17)

These rows describe the live analogue shapes used for classification and manual-mode derivation.
They do not override the plugin-wide AutoCalc/manual choice.

| Archetype | Level | AutoCalc | DNAM H/M/S | ACBS offsets H/M/S | Perks | ActorEffect | Notes |
|---|---|---|---|---|---|---|---|
| Bandit `_Base` `86837D` | 1 | **off** | 25/25/50 | 0/0/0 | **0** | **0** | empty kit is normal on a source record — the analogue's tier supplies it |
| Bandit 01/02/03 | 3/7/10 | on | 104→118 / 100 / 106→127 | 0/0/0 | 5/9/12 | tempering R1→R3 | `REQ_Class_Bandit_*`; skills 1H/Block/Heavy 10→21→29 |
| Warlock 01/02 | 1/6 | **off** | 50→142 / 100→158 / 25 | 100→60 / **450→400** / 100 | 0/2 | 2/5 castable spells | Conjuration SkillOffset 30 on the summon-scaler |
| Draugr 01/02 | 1/6 | off | 300→387 / 0 / high | 200 / 0 / 0→100 | 10/14 | 0/1 (natural armor) | perks despite being a creature — read, don't assume |
| Wolf `023ABE` | 2 | off | 85/0/205 | 0/0/0 | **0** | **0** | recognized race — Reqtificator perks it |
| Troll `023ABA` | 14 | off | 280/0/340 | 350/0/175 | 1 | 0 | offsets carry the bulk |
| Dragon priest `023A93` (boss) | **50** | off | **1490/545/0** | 0/**1300**/0 | 15 | 5 (boss spells + ResistMagic30) | never template onto a generic base |
| Vampire tier-2 `03384E` | 27 | off | 224/169/112 | 270/270/135 | **30** | 8 (drain/claws/traits) | `Requiem_VampireCollection.esp` lane |
| Guard | fixed | on | — | 0 | ~17 | `REQ_Trait_Tempering_Guard_*` | `REQ_Class_Guard_*`, `REQ_CombatStyle_Guard_*` |
| Follower | **PcLevelMult kept** | on | — | small (Lydia H+50) | ~7 + 2 REQ perks | — | follower factions (see followers.md) |
| Named civilian (Belethor, Sven, Faendal) | fixed low | on | pushed UP | 0 | **0** | **0** | **stat-only pass**: SkillValues pulled down to 5–20, DNAM up — no perks, no kit |
| Generic civilian / child / beggar | — | — | — | — | — | — | Requiem doesn't override them at all — skip, with the reason verified on the record |
| Ordinary mount (`EncHorseSaddled*` `023AB2`) | **4** | on | 289/-/106 | 0 | **0** | **0** | mounts lane — never the combat ladder; the live horse winner may be a horse overhaul: derive from it |
| Supernatural steed (`Shadowmere 09CCD7`) | 50 | on | 1637/-/198 | 0 | 0 | `REQ_Trait_Healing_Shadowmere` | the only boss-tier mount precedent — earned by kind, not by `Unique`/`Summonable` |

Perk lists on enemies resolve to **Requiem's own player perk tree** (vanilla `Skyrim.esm` FormIDs
carrying `REQ_*` EditorIDs — `REQ_OneHanded_WeaponMastery1/2`, `REQ_Block_ImprovedBlocking`,
`REQ_HeavyArmor_Conditioning`…): enemies wear the same perks the player earns, and a higher tier
means deeper into the tree. Copy the analogue's list; don't compose one from memory.
