# Keywords, Flags & the Three-Layer Split

All FormIDs verified live (2026-06-09). Keywords are what the Reqtificator's rules and Requiem's perks
match on, so they are load-bearing — read them off the comparable and replicate.

## The three layers of magic (know which you are touching)

| Layer | What | Who owns it | Your job |
|---|---|---|---|
| 1. Record-side | MGEF/SPEL/ENCH design: school, cost, magnitude, keywords, flags, FX | **you author it** | design from the comparable |
| 2. Reqtificator-assigned | global `RFTI_All_*` rescaling perks | the Reqtificator (build time) | **never hand-stamp** |
| 3. Script runtime | `Nox_*` Papyrus for special mechanics | Magic Redone scripts | **route to `requiem-script-patching`** |

**Layer 2 — the `RFTI_*` family (do NOT add to a record):**

| Perk/Spell | FormID | Role |
|---|---|---|
| `RFTI_All_AbsorbRescaling` | `962799:Requiem.esp` | rescales absorb effects |
| `RFTI_All_PoisonRescaling` | `962798:Requiem.esp` | "REQ GM: Poison Magnitude Rescaling" |
| `RFTI_All_Magic_WardDamageReduction` | `682FB5:Requiem.esp` (won by MR) | ward damage reduction |
| `RFTI_NPC_PersistentSpellRescaling` | `AD3977:Requiem.esp` (a SPEL) | NPC persistent-spell rescaling |

The `RFTI_` prefix marks the **entire** Reqtificator-assigned family — including the creature
`RFTI_Trait_*` perks (Draugr `031285`, Skeleton `AD3A45`, atronachs, werewolf, vampire…) and the
`RFTI_Player_*` perks. None of these belong on a record you author; the Reqtificator adds them at build
from keywords/race/conditions. Adding one yourself double-assigns or fights the build pass — the magic
analog of hand-stamping the weapon damage-type keyword.

**Layer 3 — `Nox_*` scripts cover only special mechanics, not scaling.** Base damage/heal/buff
magnitude scales through the engine `PowerAffectsMagnitude` flag — no script. The MR magic scripts
(in `Requiem - Magic Redone - rerun\scripts\`) implement: bound items (`Nox_BoundWeapon/BoundArmor`),
teleport/blink (`Nox_Conjuration_Teleport/Blink/GreaterTeleport`), mind-control
(`Nox_Illusion_Command/Control/Frenzy/Charm`, `Nox_Enthrall`), weather (`Nox_Alteration_ControlWeather`),
scroll-crafting (`Nox_Enchanting_ScrollCrafting*`), multi-spell tomes (`Nox_TomeMultipleSpells`),
debuffs (`Nox_Destruction_Weakness`, `Nox_Destruction_RuneMastery`), dispels, illusion FX
(`Nox_Illusion_*`). If a modded spell does one of these, carry the record-side marker and route the
Papyrus to the `requiem-script-patching` skill.

## Element & resistance keywords (Skyrim.esm)

| Keyword | FormID | On |
|---|---|---|
| `MagicDamageFire` | `01CEAD` | fire-damage MGEF |
| `MagicDamageFrost` | `01CEAE` | frost-damage MGEF |
| `MagicDamageShock` | `01CEAF` | shock-damage MGEF |
| `MagicDamageResist` | `0F81E3` | resist/armor interactions |

Pair the element keyword with the matching MGEF `ResistValue`: `ResistFire` / `ResistFrost` /
`ResistShock` (and `Poison` for poison MGEF, `MagicResist` for raw magic). `MagicSkill` is the school
ActorValue (`Destruction` / `Restoration` / `Alteration` / `Conjuration` / `Illusion`).

## Requiem effect markers (Requiem.esp)

| Keyword | FormID | Meaning |
|---|---|---|
| `REQ_SpellConcentration` | `2FFEAD` | tags a concentration effect |
| `REQ_NoDurationScaling` | `412EDF` | duration fixed — opts out of duration scaling (damage/DoT/rune) |
| `REQ_NoMagnitudeScaling` | `3FCA4C` | magnitude fixed (invisibility, paralyze, shadow-summons, cloaks) |
| `REQ_Absorb` | `ADDDF7` | absorb-archetype marker |
| `REQ_NoLifeDrainAllowed` | `2EA062` | immunity key life-drain effects test via conditions (`mgef-conditions.md`) |

A tier-1 fire effect carries `[MagicDamageFire 01CEAD, REQ_SpellConcentration 2FFEAD,
REQ_NoDurationScaling 412EDF]`. Copy the exact set off the comparable; the frost/shock analogues swap
only the element keyword. Vanilla effect-class tags MR keeps on the matching archetypes:
`MagicRestoreHealth 01CEB0`, `MagicInvisibility 01EA6F`, `MagicParalysis 01EA70`,
`MagicInfluenceFear 0424E0`, `MagicInfluence 078098`, `MagicCloak 0B62E4` (+ `MagicFlameCloak
002EDA:Update.esm`), `MagicRune 109D79` (all `:Skyrim.esm` unless noted).

**The Association doubling rule (verified live 2026-07-17):** on a `PeakValueModifier` buff
(shield/mage-armor, fortify, resist, fear) the family's `Nox_KW_*` keyword is **also the MGEF's
`Archetype.Association`** — e.g. `Nox_KW_Alteration_Shield 000813` sits in `Keywords` *and* as the
Association on the mage-armor effects, and the GM "improved" variants dispel-by-keyword against it
(`DispelWithKeywords` + `Nox_KW_Alteration_Shield_Improved 00081F`). When you patch a buff MGEF into
a family, carry the family keyword in **both** places, exactly as the comparable does.

## Tier and cost markers on the MGEF itself

- **`MinimumSkillLevel` is the tier marker on effects**: 0 / 25 / 50 / 75 / 100 = tiers 1–5
  (verified across schools). Set it to match the spell's tier. **GM/perk-gated effects sit at 0**
  and gate through the perk + `HideInUI` instead.
- **MGEF `BaseCost` is NOT the spell's magicka cost** (that lives on the SPEL, `ManualCostCalc`).
  It is the engine's per-magnitude autocalc weight — small on damage (Fire touch 1.2, frost DoT
  3.55), large on binary/utility (Invisibility 25, Paralyze 450), 0 on hidden worker effects.
  Copy the comparable's; never invent it (it feeds enchanting/potion pricing math).

## The `Nox_KW_*` marker vocabulary (Magic Redone — 107 keywords)

MR's entire KYWD contribution is the `Nox_KW_*` namespace (+ `CraftingScrollCraftingTool 006105`).
Split into **record-side** markers (you place these in a record's `Keywords` to classify it statically)
and **Nox-runtime** markers (read by the Magic Redone script/perk layer at cast time → `requiem-script-
patching`). Use record-side ones when authoring; carry a runtime marker only if the comparable does.

**Record-side — staff/enchanting classification:**
- `Nox_KW_Staff_<School><Tier>` — 5 schools × 5 tiers, `0076E1`–`0076F9` (Alt 1–5 `…E1–E5`, Conj `…E6–EA`,
  Dest `…EB–EF`, Illu `…F0–F4`, Resto `…F5–F9`). Rides on the staff **WEAP** frame, keys power/charge.
- `Nox_KW_Enchanting_Battlestaff 006185`, `Nox_KW_Enchanting_Corpus 0060EC`, `Nox_KW_Wand 007335`,
  `CraftingScrollCraftingTool 006105` (the scroll-tool furniture keyword).
- `Nox_KW_Physical_NN` (`00`/`10`/`20`/`30`/`40`/`50` = `000802`/`005F94`–`005F98`) — physical-damage bucket.

**Nox-runtime — school-mechanic markers** (classify a spell's behavior; the runtime/perks read them):
- Destruction: `Arcane 005B00`, `Entropic 005B01`, `Absorb_MS 005DA8`, `DeepFreeze 00607F`
- Restoration: `Poison 005C22`, `WeaknessMagic 005C67`, `Cure 005FBF`, `ParalyzingPoison 0060D4`, `ResistMagic 006204`
- Alteration: `Shield 000813`, `Telekinetic 005C64`, `Transmutation 005DAB`, `Wind 005F3B`, `Physical 00732B`,
  `Disintegrate 005B3E`, `Weakness 005B3F`, `ReflectDamage 005B54`, `Size 000851`, `Speed 005B32`, `Jump 006090`
- Conjuration: `BoundWeapon 005DE4`, `BoundArmor 005E72`, `Necrotic 005F21`, `DaedricAura 00739B`, `NecromanticAura 00739C`
- Illusion: `Charm 007332`, `Command 005EC9`, `Pain 482636`, `Sleep 484DDB`, `Death 005F02`, `Silence 005EBC`,
  `Blind 005EA9`, `Muffle 005ED1`, `Chameleon 005ED3`, `ShadowDrain 005F03`, `ShadowSummon 005F04`,
  `ShadowStride 005F5A`, `Nightmare 005F4A`, `Sound 005F58`, `Sanctuary 005EB4`, `Dampen 007334`, … (26 total)

**Nox-runtime — perk-gate markers** `Nox_KW_<School>_Perk_<Tree>` (the perk routes read these off the
casting spell): Dest Pyromancy `006015`/Cryomancy `006016`/Electromancy `006017`/ArcaneFocus `006018`/
EntropicFocus `006019`/BloodMagic `006012`; Resto Venomancy `00601A`/Heliomancy `00601B`; Alt Kinetomancy
`006013`/WeatherMagic `006014`; Conj Daedric `005F57`/Spirit `006006`/Necromancy `00732C`/SoulMagic `00732D`.

**Nox-runtime — delivery/shape markers:** `Nox_KW_Touch 006081`, `Nox_KW_Nova 006086`, `Nox_KW_CloakDamage 007609`.

## Damage-type markers used by MR (defined in `Requiem.esp`, not MR)

These `REQ_*` keywords are Requiem.esp-defined but carried by MR's new damage effects (see
`mr-content-survey.md` §2): `REQ_PoisonSpell AD3904` (Venom/Poison), `REQ_SunDamage ADDDF6` (Sun),
`REQ_Absorb ADDDF7` (Absorb H/M/S). Pair the right one with the matching `ResistValue` (poison →
`PoisonResist`; sun/entropic/necrotic → `ResistMagic`; arcane → none/unresistable).

## MGEF Flags (load-bearing — mirror the comparable)

| Flag | Meaning |
|---|---|
| `Hostile` | offensive (enemy-targeting) |
| `Detrimental` | reduces the actor value (damage) |
| `Recover` | the value recovers after the effect ends |
| `PowerAffectsMagnitude` | skill scales the **magnitude** at runtime |
| `PowerAffectsDuration` | skill scales the **duration** — Requiem's switch on timed buffs/debuffs |
| `NoMagnitude` | binary state — no magnitude at all (invisibility, paralyze, summons) |
| `NoDuration` | instantaneous / constant (heals, constant drains) |
| `FXPersist` | the visual persists for the effect's duration (near-universal) |
| `NoDeathDispel` | not dispelled on death — on damage so the killing tick still lands |
| `NoArea` | single-target (no area falloff) |
| `HideInUI` | mechanic-only effect, hidden from the magic menu (taper/GM/worker effects) |
| `DispelWithKeywords` | replaces same-family effects on apply (GM shields — see Association rule) |
| `IgnoreResistance`, `NoAbsorbOrReflect` | self-buffs, heals, summons (can't be resisted/absorbed) |

The archetype decides the set — the rule of thumb, verified per-archetype live (2026-07-17):
**damage → `PowerAffectsMagnitude`; timed buff/debuff → `PowerAffectsDuration`**; `NoMagnitude` for
binary states; `NoDuration` for instantaneous heals and constants. Canonical sets:

| Archetype | Flags |
|---|---|
| Direct damage | `Hostile, Detrimental, FXPersist, PowerAffectsMagnitude, NoDeathDispel` (+`NoArea` single-target) |
| Heal (self) | `NoDuration, NoArea, FXPersist, PowerAffectsMagnitude` — *not* Hostile/Detrimental |
| Shield / mage-armor / fortify / resist | `Recover, NoArea, FXPersist, PowerAffectsDuration` (GM adds `DispelWithKeywords, HideInUI`) |
| Weakness debuff | `Hostile, Recover, Detrimental, NoArea, FXPersist, PowerAffectsDuration` |
| Fear (PeakValueMod) | `Hostile, Recover, NoHitEvent, DispelWithKeywords, FXPersist, PowerAffectsDuration` |
| Invisibility / Paralyze | `Recover, NoMagnitude, FXPersist, PowerAffectsDuration` (+`Hostile` on paralyze) |
| Summon | `NoMagnitude, NoArea, FXPersist, PowerAffectsDuration, NoHitEffect` |
| Cloak | `NoArea, FXPersist, PowerAffectsDuration` |
| Weapon-ench worker | `Hostile, FXPersist, HideInUI, PowerAffectsMagnitude` |

Read and copy the comparable's exact set — the flag set is part of the design, not boilerplate.
`Taper*` values matter only on damage/DoT/cloak (instant fire hit 0.3/2/1; pure DoT 1/0/0;
shock 0.01/0/0.1) — copy them with the flags.
