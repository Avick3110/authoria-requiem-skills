# Spell Archetypes — the sub-spell taxonomy within the five schools

The five schools are deep: each holds many **effect archetypes** (the `_<Type>_` token in the
EditorID `REQ_<School><Tier>_<Type>_<Delivery>`). To patch a modded spell, classify it to the closest
archetype here and read **that archetype's** same-tier comparable — not just "a destruction spell."
All editorids/FormIDs are live MR winners (2026-06-09; census re-verified 2026-07-17); representative
comparables are given to seed a query. `[Nox]` marks an archetype whose behavior is partly a `Nox_*`
runtime script → its record-side marker is authored here, the runtime in `requiem-script-patching`.

**Census (833 `Type=Spell` records in MR, parsed exhaustively 2026-07-17):** Destruction 213,
Conjuration 166, Restoration 143, Alteration 140, Illusion 114 scheme-conforming (+57 non-scheme,
below). **The tier band is 0–5** — tier 0 is the free/utility rung (`Alteration0_Equilibrium`,
`Illusion0_VisionOfTheTenthEye`, `Destruction0_Arcane` volleys), not a naming error. Roughly **37%
of Destruction is MR-new subclasses** (Arcane 28, Entropic 27, Absorb 24, Venom 5) — a modded spell
very often has an MR-new archetype as its true comparable, not an element.

**Naming tails that break the scheme (57 records — they still ride your coverage denominator):**
`REQ_IllusionGM_*` (37 — GM variants, the tier is a *trailing digit*: `REQ_IllusionGM_Calm4`),
`REQ_ConjurationGM_Bound_Shield_*` / `REQ_AlterationGM_*`, `REQ_Creature_*` (innate creature
spells — a **separate lane**, see below), `REQ_Ench_*_Explosion` (enchant drivers),
`REQ_Ability_Staff_*`, and vanilla
`DLC2Ignite`/`DLC2Freeze`. Also: `_LeftHand`/`_RightHand` mirrors duplicate about a third of all
spells (dual-cast hands) — **patch the pair together, never one hand**.

**`_NPC` variants stay on the ladder.** They are the NPC-cast copies of player spells and are built
*exactly* like the player original — same tier scheme, same cost, same ChargeTime, same riders.
Verified live 2026-07-17: `REQ_Destruction5_AbsorbHealth_Aimed_NPC 054FE1` is 5 effects at BaseCost
**600** / ChargeTime **1.25** (the FaF T5 ladder exactly); `REQ_Conjuration3_Spirit_Troll_NPC 10E15D`
is BaseCost **500** / ChargeTime **0.75** (Conjuration T3 exactly). **"An NPC casts it" is therefore
not the creature-innate test** — a boss casting Thunderbolt is on the ladder. The test is whether the
magic *is the creature* (below).

## Creature innates — `REQ_Creature_*` (a separate lane, off the ladder)

A creature's **bite, breath, cloak, or innate elemental attack** — magic that *is* the creature's
body, not a spell anyone learns. **~72 SPEL records live, and the family is mostly Requiem's, not
MR's: 64 are touched by `Requiem.esp`, only 8 by Magic Redone** (measured 2026-07-17) — which is why
they sit outside MR's 833-record census the ladders above are mined from.

**They follow neither the cost ladder nor the rider convention.** Measured live 2026-07-17:

| Record | Effects | BaseCost | `ManualCostCalc` |
|---|---|---|---|
| `REQ_Destruction2_Shock_Aimed 02DD29` *(player, for contrast)* | 4 (primary + ED2 pair + Impact_Stagger) | 90 | yes |
| `REQ_Destruction4_Shock_Aimed 10F7EE` *(player, for contrast)* | 4 (primary + ED4 pair + Impact_Stagger) | 330 | yes |
| `REQ_Creature_AtronachStorm_LightningBolt 02FB88` | **1** | **30** | yes |
| `REQ_Creature_Watcher_Sparks 007642:MR` | 1 | 31 | **no** |
| `REQ_Creature_Watcher_LightningCloak 007640:MR` | 1 | **794** | **no** |
| `REQ_Creature_Watcher_LightningNova 007643:MR` | 2 | 17 | **no** |
| `REQ_Creature_SpiritFireWyrm_Bite 00603F:MR` | 1 | 0 | yes |
| `REQ_Creature_ShadowWolf_Bite 007707:MR` | 3 | 0 | yes |

Across the family: effect counts **1–4**, `BaseCost` **0–794** with no tier scheme, `ManualCostCalc`
**inconsistent** (creature innates are among the 6-of-833 exceptions noted in
`cost-and-magnitude.md`). They are hand-tuned per creature.

**So: mirror the nearest same-creature-family comparable verbatim; derive nothing from the tier
ladder, and add no riders.** Requiem has a named innate set for many creature families (Watcher,
AtronachStorm, Seeker, ShadowWolf, SpiritFireWyrm/StormWyrm, …) — find the family first:

```
housecarl_cross_plugin_query plugins=["Requiem.esp","Requiem - Magic Redone.esp"] type="SPEL" \
  editorid_contains="REQ_Creature_" fields=["Effects","BaseCost","ChargeTime","Flags"]
```

If the modded creature has no family analogue at all, **flag it** rather than pricing it off the
player ladder — an innate given the player ladder's cost can leave the creature unable to cast it,
and given the player's riders will drain and stagger the player in ways Requiem never intended.

## Destruction — direct damage & damage-over-time

| Archetype | What | Representative |
|---|---|---|
| Fire / Frost / Shock | the three elements; bolt, conc, AoE, wall | `REQ_Destruction2_Fire_Aimed 012FD0` |
| **Venom** | **poison damage** (a destruction line, not alchemy) | `REQ_Destruction3_Venom_ConcAimed 025F22`, `..4_Venom_Aimed 025F29` |
| **Arcane** | raw/unresistable magic damage | `REQ_Destruction2_Arcane_Aimed 02958A`, `..0_Arcane_ConcAimed_Volley 45D3AC` |
| **Entropic** | entropy/decay damage | `REQ_Destruction2_Entropic_Aimed 005ACA` |
| **Absorb** Health/Magicka/Stamina | drain + restore the caster | `REQ_Destruction3_AbsorbHealth_ConcAimed 027FA6` |

Deliveries seen on these: `ConcAimed`, `Aimed`, `AimedExp` (impact explosion), `AimedArea`, `Touch`,
`Cloak`/`CloakSelf` (damage aura), `Rune` (placed trap), `Hazard`/`ConcAimedHazard` (lingering ground
hazard), `Wall` (the tier-4 conc walls), `SelfExp`/`RitualSelfExp` (nova), `DoT`, `Steam`, `Volley`.

## Restoration — healing, warding & holy/poison damage

| Archetype | What | Representative |
|---|---|---|
| Healing | Self/Target/ConcTarget/SelfArea/Aura | `REQ_Restoration2_Healing_Self 02F3B8` |
| Ward | damage-absorbing ward (tiers 1–5) | `REQ_Restoration2_Ward_ConcSelf 013018` |
| **TurnUndead** | fear/destroy undead | `REQ_Restoration2_TurnUndead_Aimed 04B146` |
| **Sun** | sun/holy damage (strong vs undead) | `REQ_Restoration2_Sun_Aimed 003F52` |
| **Poison** | **offensive poison line** (witch/druid) | `REQ_Restoration2_Poison_Aimed 005C18`, full tier 1–5 set |
| Dispel | strip magic effects (Self/Target/SoulGem) | `REQ_Restoration3_Dispel_Self 02851A` |
| ResistPoison | defensive poison resist | `REQ_Restoration2_ResistPoison_Self 03281F` |
| WeaknessMagic | lower a target's magic resistance | `REQ_Restoration2_WeaknessMagic_Target 27CA80` |

## Conjuration — summons, raising & bound

| Archetype | What | Representative |
|---|---|---|
| **Bound** | bound weapon/bow/crossbow/shield/armor; GM elemental `[Nox]` | `REQ_Conjuration1_Bound_Sword 0211EB` |
| **Daedra** | atronachs, thralls, Dremora, Seeker | `REQ_Conjuration3_Daedra_FlameAtronach 0204C3` |
| **Undead** | Skeletal/Spectral/Ghostly-Lich/Boneman-Mistman-Wrathman/AshSpawn/Arvak | `REQ_Conjuration2_Undead_Boneman 0045BA` |
| **Reanimate** | raise a corpse (tiers 1–5, GM Necromancy) | `REQ_Conjuration2_Reanimate_Aimed 065BD7` |
| **Spirit** | animal/nature summons (wolf/bear/spider/spriggan/werewolf/steed…) | `REQ_Conjuration1_Spirit_Wolf 0640B6` |
| NecromanticHealing | heal undead minions | `REQ_Conjuration2_NecromanticHealing_ConcTarget 00E8D2` |
| SoulTrap | trap a soul | `REQ_Conjuration2_SoulTrap_Aimed 04DBA4` |
| Banish / CommandDaedra | banish or command summoned daedra | `REQ_Conjuration3_BanishDaedra_Target 06D22C` |
| Swarm | insect/creature swarm | `REQ_Conjuration2_Swarm_Aimed 029010` |
| Oblivion | elemental oblivion-rift AoE | `REQ_Conjuration4_Oblivion_AimedArea_Fire 005DB3` |
| Necrotic | necrotic damage line | `REQ_Conjuration3_Necrotic_ConcAimed 005F22` |
| Teleport / Blink `[Nox]` | self/target translocation | `REQ_Conjuration3_Blink_TargetLoc 005DC0` |

## Illusion — mind, stealth, shadow & sound

| Archetype | What | Representative |
|---|---|---|
| Calm / Fear / Frenzy / Rally | the four classic mind effects (+ GM variants) | `REQ_Illusion2_Fear_Aimed 04DEEA` |
| Charm `[Nox]` / Command `[Nox]` | charm, mind-control a target | `REQ_Illusion2_Charm_Target 005973` |
| Pain / Sleep `[Nox]` / Nightmare / Death `[Nox]` | damage-via-mind / incapacitate / kill | `REQ_Illusion2_Sleep_Aimed 40198C` |
| Silence / Blind | disable casting / sight | `REQ_Illusion2_Silence_Aimed 005A2B` |
| Invisibility / Chameleon / Muffle | concealment | `REQ_Illusion3_Invisibility_Self 027EB6` |
| NightEye / Clairvoyance / VisionOfTheTenthEye | perception | `REQ_Illusion1_Clairvoyance_ConcSelf 021143` |
| Sound / Noise | sonic lure/distraction | `REQ_Illusion1_Sound_Aimed 02852F` |
| Shadow (ShadowSummon/ShadowDrain/ShadowStride/Dampen/Sanctuary) | the shadow-magic line | `REQ_Illusion3_ShadowSummon_Wolf 005953` |

## Alteration — armor, control, transmute & force

| Archetype | What | Representative |
|---|---|---|
| Armor / Flesh | mage-armor (Oak→Dragonhide; Self/Target/Ritual) | `REQ_Alteration1_Armor_Self 05AD5C` |
| Elemental Shield | Fire/Frost/Shock shields | `REQ_Alteration2_FireShield_Self 000816` |
| Light | candlelight / magelight | `REQ_Alteration1_Light_Self 043324` |
| Detect | DetectLife / DetectDead | `REQ_Alteration3_DetectLife_ConcSelfArea 0211EE` |
| Telekinesis / Telekinetic | move objects; offensive blast/disarm | `REQ_Alteration3_Telekinesis_ConcSelf_Object 01A4CC` |
| Paralyze | hard CC | `REQ_Alteration4_Paralyze_Aimed 05AD5F` |
| Physical | telekinetic force damage | `REQ_Alteration2_Physical_Aimed 00083A` |
| WeaknessFire/Frost/Shock | elemental susceptibility debuff | `REQ_Alteration2_WeaknessFire_Target 000832` |
| Transmute | Ore / Muscles / NightEye / Disintegration | `REQ_Alteration4_TransmuteOre_Self 109111` |
| Wind | cyclone/cloak (Dragonborn) | `REQ_Alteration3_Wind_CloakSelf 01772D` |
| Ash | ash shell/rune (Dragonborn) | `REQ_Alteration4_Ash_Aimed 017731` |
| Enlarge / Shrink `[Nox]` / Polymorph `[Nox]` | size / form change | `REQ_Alteration4_Enlarge_Self 00084C` |
| Etherealize / SlowTime / ReflectDamage | defensive/control | `REQ_Alteration5_Etherealize_RitualSelf 029003` |
| Waterbreathing / Waterwalking / Feather / Burden / Speed / Open / Equilibrium / Mending | utility | `REQ_Alteration2_Waterbreathing_Self 05D175` |

## Cross-cutting — where an effect-type lives (don't assume one school)

- **Poison** appears in THREE places: **Destruction `Venom`** (arcane-flavored poison damage),
  **Restoration `Poison`** (the offensive witch/druid line) and **Alchemy** poison MGEF
  (`MagicAlchHarmful`, `enchantments.md`). Defensive **ResistPoison** is Restoration. Match the modded
  spell's *feel and school intent* to the right one before grabbing a comparable.
- **Undead** spans THREE: **Conjuration** (raise — Undead/Reanimate/Spectral/Ghostly/Skeletal),
  **Restoration** (anti-undead — TurnUndead + Sun), and **necromancy support** (NecromanticHealing,
  the GM Reanimate ability). A "summon a skeleton" mod is Conjuration; a "repel undead" mod is Restoration.
- **Raw/typeless damage** is **Destruction `Arcane`/`Entropic`** (not fire/frost/shock) — use it for a
  modded "force"/"void"/"true damage" spell rather than mis-typing it as an element.
- **Debuffs:** elemental susceptibility = **Alteration `Weakness<Element>`**; magic-resist down =
  **Restoration `WeaknessMagic`**.

**The new-damage-type table — `MagicSkill` / `ResistValue` / marker keyword for each** (Venom, Poison,
Arcane, Entropic, Sun, Necrotic, Absorb) is in `mr-content-survey.md` §2. Set the matching school AV +
resist + keyword off it: poison → `PoisonResist` + `REQ_PoisonSpell`; sun → `ResistMagic` +
`REQ_SunDamage`; arcane → unresistable (no `ResistValue`); entropic/necrotic → `ResistMagic`; absorb →
the `Absorb` archetype + `REQ_Absorb`.

## Delivery vocabulary (orthogonal to the archetype)

The `_<Delivery>` token is independent of the type: `ConcAimed` / `Aimed` / `AimedExp` (impact blast) /
`AimedArea` / `Touch` / `Self` / `ConcSelf` / `Target` / `ConcTarget` / `TargetLocation` /
`Cloak`/`CloakSelf` (aura) / `Rune` (placed) / `Hazard` (lingering ground) / `Wall` /
`SelfExp`/`RitualSelfExp` (nova) / `RitualSelf`/`RitualTarget` (master rituals) / `DoT` / `EnchSelf` /
`Volley`. Pick the comparable that matches **both** the archetype and the delivery — cost/charge follow
the delivery (`cost-and-magnitude.md`), the effect follows the archetype.
