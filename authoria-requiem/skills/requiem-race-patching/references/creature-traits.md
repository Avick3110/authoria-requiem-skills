# Creature Trait Engine

How Requiem gives a creature race its damage profile, and how to classify a modded creature to the
right Requiem analogue. Two delivery layers (recap from `SKILL.md`):

- **Layer A — trait SPELLS on the RACE's `ActorEffect`** (engine-inherited by every NPC of the race;
  propagate to a new race for free). Families: `Resist_`, `Armor_`, `Healing_`, `FX_`, `Cloak_`,
  `UnarmedDamage_`. **Almost all `Resist_`/`Armor_`/`Healing_` spells' live winner is
  `Requiem - Resist and Regen Tweak.esp`** — read the winner.
- **Layer B — the `incomingDamageModifier` PERK** the Reqtificator assigns to NPC_ records by
  race-FormID match (not inherited from the RACE; a new race isn't matched). Two **additive** config
  taxonomies are live: base Requiem's per-category perks and the Resist-and-Regen-Tweak's
  physique/supernatural perks.

## Table of contents
- [Trait-spell families (Layer A)](#trait-spell-families-layer-a)
- [Layer-B perks — base Requiem per-category](#layer-b-perks--base-requiem-per-category)
- [Layer-B perks — Resist and Regen Tweak (physique / supernatural)](#layer-b-perks--resist-and-regen-tweak-physique--supernatural)
- [doNotInheritTraits](#donotinherittraits)
- [The rules (what to add, what to keep, what to never touch)](#the-rules-what-to-add-what-to-keep-what-to-never-touch)
- [Worked classification: the troll](#worked-classification-the-troll)

## Trait-spell families (Layer A)

Don't memorize FormIDs — **read the analogue's `ActorEffect` live** and copy the relevant spells. The
families and how to find them (`cross_plugin_query type="SPEL" editorid_contains="REQ_Trait_<family>_"`):

| Family | EditorID pattern | What it does | Add to a modded race? |
|---|---|---|---|
| Armor | `REQ_Trait_Armor_<X>` | natural armor vs physical damage | **Yes — almost always** |
| Resist | `REQ_Trait_Resist_<X>` | elemental/magic/poison resistances | Only if the race ships none of its own |
| Healing | `REQ_Trait_Healing_<X>` | combat health regen | Yes if the analogue regenerates — **also set `RegenHpInCombat`** |
| Cloak | `REQ_Trait_Cloak_<X>` | damage aura (atronachs, spiders, ash spawn) | Only if the analogue has one and the race doesn't already |
| UnarmedDamage | `REQ_Trait_UnarmedDamage_<X>` | scales the creature's brawl damage by rank | Match the analogue's tier |
| FX | `REQ_Trait_FX_<X>` | visual shader/effect | **Never** — modded races mishandle them |

Representative anchors (verify live): troll `Resist 02F617` / `Armor AD39E6` / `Healing AE3AED`;
draugr `Resist AE3AD2` / `Armor_Draugr_Armored 03280E`; skeleton `Resist AE3AD6` / `Armor(ArmoredSkeleton) AD39F2`;
frost atronach `Resist 02F3B6` / `Armor AD39D7` / `Cloak(Frost) AE3AE2`; dragon `Resist 057B48` /
`Armor AD39DB`; werewolf `Resist 0A1A3E` / `Healing AE3B0C`. The full set is the `REQ_Trait_*`
enumeration (~352 spells) — query it; don't hardcode.

## Layer-B perks — base Requiem per-category

`feature_racialTraits` in `…\Reqtificator\Data\ActorAssignmentRules_Requiem.esp.conf` (all
`Requiem.esp`). The Reqtificator assigns the perk to the NPCs of the listed races at build:

| Category | Perk | Races |
|---|---|---|
| ash spawn | AE359A | ashSpawn |
| chaurus hunter | AE3594 | chaurus.hunter |
| spriggan | 109D0D (Skyrim) | spriggan |
| werebear | AE3599 | werebear |
| werewolf | AD3A43 | werewolf |
| dwarven centurion | AD3A61 | centurion, forgeMaster |
| atronach flame | AD3A49 | atronachFire |
| atronach frost | AD3A48 | atronachFrost |
| atronach storm | AD3A47 | atronachStorm, atronachThunder, ashGuardian |
| dremora | AD3B27 | dremora |
| lurker | AE3593 | lurker |
| seeker | AE3592 | seeker |
| dragon | AD3A62 | dragon.normal, durnehviir, alduin |
| skeleton (undead) | AD3A45 | skeleton normal/armored, boneman, mistman, wrathman, dragon.skeleton |
| death hound | AD3A50 | deathHound |
| draugr | 031285 | draugr |
| dragon priest | AD3A46 | dragonPriest |
| mistman | AE3595 | mistman |
| **state: ghost** | 031284 | iceWraith, magicAnomaly, wisp, wispMother, corrupted shade/keeper (+skeleton) |
| **state: spirit** | AD385B | actors with the `spirit` keyword `10EAD7` |
| **state: vampire** | AD3A44 | actors with the `vampire` keyword `0A82BB` |

State traits fire off keywords (`ghost 0D205E`, `spirit 10EAD7`, `vampire 0A82BB`), not race lists.

## Layer-B perks — Resist and Regen Tweak (physique / supernatural)

`Requiem - Resist and Regen Tweak.esp` adds its **own** `feature_racialTraits` (a material/origin
taxonomy) on top of the base, perking many creatures the base left bare. Perks `000800`–`00080E` are
defined in `Requiem - Resist and Regen Tweak.esp`:

| Category | Perk | Races (race_any) |
|---|---|---|
| physique.ash | 000800 | ashGuardian |
| physique.boneless | 000801 | netch calf/normal |
| physique.chitin | 000802 | ashHopper, frostbiteSpider (all), mudcrab, chaurus (all) |
| physique.fur | 000805 | bears (all), mammoth, sabreCat (all), Karstaag, **troll (normal/frost), trollArmored (normal/frost)** |
| physique.metal | 000807 | dwarven ballista, sphere, spider |
| physique.stone | 00080A | gargoyle (all) |
| physique.zombie | 00080D | spectral draugr, spectral warhound |
| physique.featherfall | 00080E | ashGuardian |
| supernatural.dragon | 000804 | dragon.skeleton |
| supernatural.undead | 00080B | durnehviir, keeper, mistman |
| physique.scale / .skeleton / .wood; supernatural.daedra / .lycanthrope | 000808 / 000809 / 00080C / 000803 / 000806 | (defined but empty race lists) |

The two tables are **additive** — a creature can get a base perk *and* a tweak perk (e.g. the skeletal
dragon gets undead-skeleton `AD3A45` from base + supernatural-dragon `000804` from the tweak). To
classify a new creature, pick its nearest analogue and assign **whatever perk(s) that analogue's NPCs
receive across both tables.** The physique materials (ash/boneless/chitin/fur/metal/scale/stone/wood/
zombie) are the more granular, modern axis — match by the creature's material/build.

## doNotInheritTraits

`doNotInheritTraits` keyword `AD3A4F:Requiem.esp` on an NPC or RACE opts it **out** of the
Reqtificator's per-NPC trait pass (`feature_racialTraits` has `keywords_none = [doNotInheritTraits]`).
Use it only to *exclude* a special actor (e.g. a unique boss with hand-tuned perks) — never by default.

## The rules (what to add, what to keep, what to never touch)

- **Add `REQ_Trait_Armor_<analogue>`** — the natural-armor spell is what gives a creature its physical
  bulk; almost every creature needs it.
- **Keep the modded race's own resistances.** Modded creature races commonly ship their own resistance
  abilities (often `Ab*`-named, e.g. `AbResistFrost`). Leave them and **only add armor**; don't stack a
  second Requiem `Resist_` on top unless the race truly has none.
- **Healing implies the flag.** If you add a `REQ_Trait_Healing_<X>`, set the RACE `Flags` to include
  `RegenHpInCombat` — without it the healing trait is inert.
- **Never add `REQ_Trait_FX_<X>`** to a modded race.
- **Don't duplicate a spit/breath/cloak** the modded race already has — patch its own to match Requiem
  later (the `requiem-magic-patching` skill) rather than adding Requiem's alongside.
- **Layer B is per-NPC.** Name the analogue's category + perk, then either template the creature's NPCs
  onto a known Requiem race or hand-add the perk to them in the `requiem-npc-patching` skill. Don't put
  a Layer-B perk on the RACE — it does nothing there.

## Worked classification: the troll

`TrollRace 013205` (winner = Master Patch) carries `ActorEffect` = `[Resist_Troll 02F617,
Armor_Troll AD39E6, Healing_Troll AE3AED]`; `Flags` include `RegenHpInCombat`; `Keywords` =
`[ActorTypeCreature, ActorTypeAnimal, ActorTypeTroll, REQ_DropsBloodKeyword, REQ_MinorKnockdownImmunity]`;
`Starting` H/S high, `UnarmedDamage 60`. Its NPCs get Layer-B `physique.fur 000805` from the tweak
(base Requiem perks no troll). A new troll-type race → mirror exactly that: add Armor + Healing
(+ flag), keep its own resistances if any, classify Layer B as `fur 000805` for the NPC pass.
