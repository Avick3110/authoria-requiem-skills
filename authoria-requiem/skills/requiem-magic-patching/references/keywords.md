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

## Requiem effect markers — the behavioral keywords

**Write the master suffix every time.** These live in `Requiem.esp` (and one in MR), *not* in
`Skyrim.esm` — the element keywords they sit next to are `Skyrim.esm`, and a bare `2FFEAD` copied out
of a mixed list gets guessed as `:Skyrim.esm`, which fails the write. FormIDs re-verified live
2026-07-18.

| Keyword | FormID (with master) | Meaning |
|---|---|---|
| `REQ_SpellConcentration` | `2FFEAD:Requiem.esp` | tags a concentration/channelled effect |
| `REQ_NoDurationScaling` | `412EDF:Requiem.esp` | duration fixed — opts out of duration scaling |
| `REQ_NoMagnitudeScaling` | `3FCA4C:Requiem.esp` | magnitude fixed (invisibility, paralyze, shadow-summons, cloaks) |
| `REQ_Absorb` | `ADDDF7:Requiem.esp` | absorb-archetype marker |
| `REQ_NoLifeDrainAllowed` | `2EA062:Requiem.esp` | immunity key life-drain effects test via conditions (`mgef-conditions.md`) |
| `Nox_KW_CloakDamage` | `007609:Requiem - Magic Redone.esp` | marks a cloak's per-tick damage effect |

## Subtype keywords — where MR classifies a spell's sub-archetype

**Two structural facts to get right before using this table** (both verified live 2026-07-18):

1. **Subtype keywords live on the MGEF, never on the SPEL.** MR's Spell records carry **no keywords at
   all** — 915 scanned, `Keywords` unset on every one. Do not try to write one onto a Spell.
2. **`Nox_KW_<School>_Perk_*` keywords do NOT go on plain damage effects.** MR's own Fire/Frost/Shock
   damage MGEFs carry only the element keyword + `REQ_NoDurationScaling`. The Pyromancy/Cryomancy/
   Electromancy keywords (`006015`/`006016`/`006017`) appear on **cloak, enchant and hazard** delivery
   forms only — e.g. `REQ_Effect_Destruction3_Shock_CloakSelf 03AEA1:Skyrim.esm` and
   `…Destruction5_Shock_CloakSelf 00751A:Requiem - Magic Redone.esp` both carry `006017`, while
   `…Destruction2/4_Shock_Aimed` carry none. Adding one to a damage spell mis-files it.

**Not every subtype has a keyword.** Where the table says *none*, the subtype is classified by a vanilla
class keyword and inventing a `Nox_KW_` name for it is a fabrication. Take the comparable's set as-is.

**Destruction** — element keyword carries the classification; `REQ_NoDurationScaling 412EDF:Requiem.esp`
is on all seven; concentration adds `REQ_SpellConcentration 2FFEAD:Requiem.esp`, Touch adds
`Nox_KW_Touch 006081`:

| Subtype | Classifying keyword |
|---|---|
| Fire / Frost / Shock | `MagicDamageFire 01CEAD` / `Frost 01CEAE` / `Shock 01CEAF` (`:Skyrim.esm`) |
| Venom | `REQ_PoisonSpell AD3904:Requiem.esp` |
| Arcane | `Nox_KW_Destruction_Arcane 005B00` |
| Entropic | `Nox_KW_Destruction_Entropic 005B01` |
| Absorb | `REQ_Absorb ADDDF7:Requiem.esp` — **replaces** the element slot, no element keyword |

**Restoration** — most lines have no subtype keyword:

| Subtype | Classifying keyword |
|---|---|
| Healing | *none* — `MagicRestoreHealth 01CEB0:Skyrim.esm` |
| Ward | *none* — `MagicWard 01EA69:Skyrim.esm`; **tier-graded** additions: `REQ_ProtectionFromParalysis 357085:Requiem.esp` from T3, `REQ_NoLifeDrainAllowed 2EA062:Requiem.esp` from T4 |
| TurnUndead | *none* — `MagicTurnUndead 0BD83F:Skyrim.esm` |
| Sun | *none* — `REQ_SunDamage ADDDF6:Requiem.esp` |
| Poison (offensive) | `Nox_KW_Restoration_Poison 005C22` + `REQ_PoisonSpell AD3904:Requiem.esp` |
| Cure (poison + disease) | `Nox_KW_Restoration_Cure 005FBF` |
| WeaknessMagic | `Nox_KW_Restoration_WeaknessMagic 005C67` |
| ResistMagic | `Nox_KW_Restoration_ResistMagic 006204` |
| FortifyAttributes | `Nox_KW_Restoration_FortifyAttributes 005D05` |

**Conjuration** — summons are classified by vanilla `MagicSummon*`, not by a Nox keyword:

| Subtype | Classifying keyword |
|---|---|
| Bound weapon (incl. shield) | `Nox_KW_Conjuration_BoundWeapon 005DE4` |
| Bound armor | `Nox_KW_Conjuration_BoundArmor 005E72` |
| Daedra summon | `MagicSummonFire 024823` / `MagicSummonShock 02482A` (`:Skyrim.esm`) |
| Undead summon | `MagicSummonUndead 02482B:Skyrim.esm` |
| Reanimate | `MagicSummonUndead` + `REQ_Reanimate 068391:Requiem.esp` (the discriminator vs a plain undead summon) |
| Spirit summon | `MagicSummonSpirit 1091CF:Skyrim.esm` — alone |
| Banish | `REQ_NoDurationScaling` + branch perk kw (`Perk_Daedric 005F57` / `Perk_Necromancy 00732C`) |
| SoulTrap | `Nox_KW_Conjuration_Perk_SoulMagic 00732D` |
| Oblivion | element kw + `REQ_NoMagnitudeScaling 3FCA4C:Requiem.esp` + `Perk_Daedric 005F57` |
| Necrotic | `Nox_KW_Conjuration_Necrotic 005F21` |
| Teleport / Blink | `REQ_NoDurationScaling` + `REQ_NoMagnitudeScaling` + `Perk_Daedric 005F57` |

**No summon MGEF carries a `Nox_KW_Conjuration_Perk_*` keyword** — the four Perk keywords appear only on
Banish/Command, SoulTrap, Oblivion, Teleport/Blink, Swarm and Necrotic riders.

**Illusion** — the richest namespace, and the one with the `:Requiem.esp` exceptions:

| Subtype | Classifying keyword |
|---|---|
| Calm / Fear / Frenzy | ***none*** — vanilla `MagicInfluenceCharm 0424EE` / `MagicInfluenceFear 0424E0` / `MagicInfluenceFrenzy 0C44B6` + `MagicInfluence 078098` (all `:Skyrim.esm`) |
| Pain | **`Nox_KW_Illusion_Pain 482636:Requiem.esp`** |
| Sleep | **`Nox_KW_Illusion_Sleep 484DDB:Requiem.esp`** (the Rune adds `Nox_KW_Illusion_Sleeping 00619D`) |
| Nightmare | `Nox_KW_Illusion_Nightmare 005F4A` + `Weakness1 00598C` **or** `Weakness2 3C130A:Requiem.esp` |
| Rally | `Nox_KW_Illusion_Rally 005A39` |
| Charm | `Nox_KW_Illusion_Charm 007332`; CharmingAura `005983` |
| Command / Control | `Nox_KW_Illusion_Command 005EC9` (both share it) |
| Death | `Nox_KW_Illusion_Death 005F02` |
| Shadow family | Drain `005F03` · Summon `005F04` · Stride `005F5A` · Step `007333` · Blade `0078C5` |
| Sound / Noise | `Sound 005F58` / `Noise 005EAD` — **separate keywords** |
| Silence / Blind / Muffle / Chameleon | `005EBC` / `005EA9` / `005ED1` / `005ED3` |
| Invisibility | *none* — `MagicInvisibility 01EA6F:Skyrim.esm` |
| Sanctuary | `005EB4` (T3/T4); T5 uses `SanctuaryEtherealize 007328` instead |
| Hallucination / Dampen | `007331` / `007334` |

**Alteration** — mage armor uses a vanilla keyword; the elemental shields use a shared family keyword:

| Subtype | Classifying keyword |
|---|---|
| Flesh / mage armor | *none* — `MagicArmorSpell 01EA72:Skyrim.esm` + `REQ_NoMagnitudeScaling 3FCA4C:Requiem.esp` |
| Mage-armor GM rider | `MagicArmor30Perk 104AB6:Skyrim.esm` |
| Elemental shields (Fire/Frost/Shock) | `Nox_KW_Alteration_Shield 000813` — one keyword for all three |
| Shield GM "improved" riders | `Nox_KW_Alteration_Shield_Improved 00081F` |
| Telekinesis (grab) | `MagicTelekinesis 07F404:Skyrim.esm` |
| Telekinetic (damage) | `Nox_KW_Alteration_Telekinetic 005C64` — **never co-occurs with `MagicTelekinesis`** |
| Paralyze | `MagicParalysis 01EA70:Skyrim.esm` + `REQ_NoMagnitudeScaling` — **no `Nox_KW_Alteration_Paralyze` exists** |
| Physical force | `Nox_KW_Alteration_Physical 00732B` + a `Nox_KW_Physical_<NN>` tier kw + `REQ_DamageType_Blunt AD3957:Requiem.esp` |
| Elemental Weakness | `Nox_KW_Alteration_Weakness 005B3F` |
| Transmute / Ash | `Nox_KW_Alteration_Transmutation 005DAB` (Ash also carries `MagicParalysis`; **no `_Ash` keyword exists**) |
| Wind (aimed) | `Nox_KW_Alteration_Wind 005F3B`; the **cloak** form instead carries `MagicCloak 0B62E4:Skyrim.esm` + `Perk_WeatherMagic 006014` |
| Size / Speed / Jump / Weight | `000851` / `005B32` / `006090` / `005B44` |
| ReflectDamage / Disintegrate | `005B54` / `005B3E` |
| Etherealize / Waterwalking | *none* — Waterwalking carries **no keywords at all** |

## The MGEF signature is BEHAVIORAL, not elemental or tier-keyed

This is the rule that decides whether a modded MGEF is patched. **The keyword+flag set is determined by
what the effect *does* — its delivery behavior — not by its element and not by its tier.** Two fire
effects at the same tier carry *different* signatures if one is a burst bolt and the other a cloak tick.
The element keyword only swaps `MagicDamageFire`↔`Frost`↔`Shock`; everything else follows the behavior.

So **a modded MGEF that has `MagicSkill` + the vanilla element keyword + a `MinimumSkillLevel` tier
marker is NOT thereby Requiem-correct.** That trio is what a competent vanilla-style mod ships on its
own; it is *necessary but not sufficient*. Requiem-correct means it additionally carries the **REQ
behavioral keywords its archetype's MR comparable carries**. Missing those, the effect is **unpatched** —
Requiem's scaling rules never match it, so it scales as vanilla inside Requiem's economy.

**Method — classify, then read that archetype's exemplar:**

1. Decide what the effect *does*: burst FaF bolt · concentration stream · aimed DoT · lingering taper
   rider · self-buff · magicka-burn / discharge rider · cloak tick · hazard tick.
2. Query MR for the exemplar of **that archetype** (`editorid_contains` on the shape — `_Aimed`,
   `_ConcAimed`, `_AimedDoT`, `_Taper`, `_Cloak_Damage`, `_Hazard_Damage`, `…Discharge…_Magicka`).
3. Read its `Keywords` at `depth=2` and its `Flags`, and copy that exact set (swapping only the element).

**Do not apply a flat "damage → `REQ_NoDurationScaling`, concentration → `REQ_SpellConcentration`"
rule.** The live data contradicts it in both directions: a lingering **taper** rider carries
`REQ_NoDurationScaling` *and* `REQ_SpellConcentration` even though it is neither a plain damage hit nor
a channelled spell, while a **magicka-burn rider** carries **no REQ keyword at all**.

**Verified shock ladder (live, 2026-07-18)** — the element keyword below is `MagicDamageShock
01CEAF:Skyrim.esm`; **mirror the shape per element but re-derive live**, because the flag sets do *not*
mirror cleanly (see the caveats under the table):

| Archetype | Exemplar | Keywords (beyond the element kw) | Flags |
|---|---|---|---|
| Burst bolt (single-target) | `REQ_Effect_Destruction2_Shock_Aimed` `01CEA8:Skyrim.esm` | `REQ_NoDurationScaling` | `Hostile, Detrimental, NoArea, PowerAffectsMagnitude, NoDeathDispel` |
| Concentration stream | `REQ_Effect_Destruction1_Shock_ConcAimed` `013CAB:Skyrim.esm` | `+ REQ_SpellConcentration` | *above* `+ FXPersist` |
| Aimed DoT | `REQ_Effect_Destruction2_Shock_AimedDoT` `029AFE:Requiem.esp` | `REQ_NoDurationScaling` only | `Hostile, Detrimental, NoArea, FXPersist, PowerAffectsMagnitude, NoDeathDispel` |
| Lingering taper rider | `REQ_Effect_DestructionGM_Shock_Taper` `005FAE:Requiem - Magic Redone.esp` | `REQ_SpellConcentration + REQ_NoDurationScaling` | *DoT* `+ Recover, + HideInUI` |
| Magicka-burn / discharge rider | `REQ_Effect_DestructionGM_ElectrostaticDischarge1_Magicka` `005AAD:Requiem - Magic Redone.esp` | **none** — element kw only | `Hostile, Detrimental, FXPersist, HideInUI, PowerAffectsMagnitude, NoDeathDispel` (**no** `NoArea`) |
| Cloak tick | `REQ_Effect_DestructionGM_Shock_Cloak_Damage` `10CBDF:Skyrim.esm` | `REQ_NoMagnitudeScaling + REQ_NoDurationScaling + Nox_KW_CloakDamage` | `Hostile, Detrimental, NoArea, FXPersist, PowerAffectsMagnitude, NoDeathDispel` |
| Hazard tick | `REQ_Effect_DestructionGM_Shock_Hazard_Damage` `045D5A:Skyrim.esm` | **none** — element kw only | `Hostile, Detrimental, NoArea, FXPersist, HideInUI, PowerAffectsMagnitude` (**no** `NoDeathDispel`) |

**Three live-verified caveats that make re-deriving mandatory:**

- **DoT and taper are two archetypes, not one.** The aimed DoT (the spell's own damage-over-time
  primary) carries *only* `REQ_NoDurationScaling`; the GM taper *rider* adds `REQ_SpellConcentration`
  plus `Recover`/`HideInUI`. Reading one and writing the other under-builds or over-builds the effect.
- **Flags do not mirror across elements.** The *shock* hazard tick omits `NoDeathDispel`; the *fire*
  hazard tick (`REQ_Effect_DestructionGM_Fire_Hazard_Damage 08F3F2:Skyrim.esm`) carries **both**
  `NoDeathDispel` **and** `NoRecast`. Keywords mirrored across elements in every pair checked; flags
  did not.
- **Flags vary within an archetype by tier.** `REQ_Effect_Destruction2_Shock_Aimed` has no `FXPersist`;
  the tier-4 `REQ_Effect_Destruction4_Shock_Aimed 10F7EF:Skyrim.esm` does, on identical keywords.

**Worked negative (the failure this section exists to prevent).** Apocalypse's Bolide, a tier-3 fire
burst: `WB_Des_Fire3_Effect_Bolide 028F67:Apocalypse - Magic of Skyrim.esp` carries
`MagicSkill = Destruction`, `MinimumSkillLevel = 50`, `ResistValue = ResistFire`, and keywords
`[MagicDamageFire 01CEAD:Skyrim.esm, WISpellDangerous 0A9B1F:Skyrim.esm, WB_Destruction_Fire, WB_Destruction]`
— school, element, and tier all present, flags nearly identical to the comparable. Its archetype
comparable `REQ_Effect_Destruction3_Fire_AimedExp 01CEA1:Skyrim.esm` (same `MinimumSkillLevel = 50`)
carries `[MagicDamageFire, REQ_NoDurationScaling 412EDF:Requiem.esp]`. Bolide has **no REQ keyword**.
It is unpatched, and it reads as "already Requiem-correct" to anyone checking school + element + tier.

Vanilla effect-class tags MR keeps on the matching archetypes:
`MagicRestoreHealth 01CEB0`, `MagicInvisibility 01EA6F`, `MagicParalysis 01EA70`,
`MagicInfluenceFear 0424E0`, `MagicInfluence 078098`, `MagicCloak 0B62E4` (+ `MagicFlameCloak
002EDA:Update.esm`), `MagicRune 109D79` (all `:Skyrim.esm` unless noted).

**The Association doubling rule (re-verified live 2026-07-18, 14/14 records — with two corrections):**
on a `PeakValueModifier` buff (shield, mage-armor, fortify, resist, weakness, speed) the **family
keyword** sits in `Keywords` **and** as the MGEF's `Archetype.Association`/`AssociationKey`. Carry it in
both places, exactly as the comparable does.

- **It is not necessarily a `Nox_KW_*`.** The elemental shields double `Nox_KW_Alteration_Shield 000813`
  (GM variants `Nox_KW_Alteration_Shield_Improved 00081F`), but **mage armor doubles a vanilla keyword**
  — `MagicArmorSpell 01EA72:Skyrim.esm`, GM rider `MagicArmor30Perk 104AB6:Skyrim.esm` — and
  TransmuteMuscles doubles `REQ_TransmuteMuscles 682FB1:Requiem.esp`. The rule is "the family keyword,
  whatever its origin". (An earlier revision of this file attributed `000813` to mage armor; it belongs
  to the elemental shields.)
- **Two live exceptions:** `REQ_Effect_Alteration3_Waterwalking_Self 000821` (no keywords at all) and
  `REQ_Effect_AlterationGM_Wind_Cloak_Speed 005B59` (keyworded, Association null). A bare utility PVM
  can skip the doubling — so read, don't assume.
- **Why the doubling exists:** MR ships dedicated `Script`-archetype **dispel effects** whose `Keywords`
  list is the dispel target set — `AlterationGM_Dispel_Shield 000814` carries **both** `000813` and
  `00081F` with `DispelWithKeywords`. Same shape for `Dispel_Weakness 000820`, `Dispel_Size 000852`,
  `Dispel_Jump 006091`. The Association is what those effects match on.
- `DispelWithKeywords` is on every `_Improved` GM variant (plus `HideInUI`), but is **not exclusive to
  them** — `REQ_Effect_Alteration3_Speed_Self 000869` carries it on a plain tier.

## Tier and cost markers on the MGEF itself

- **`MinimumSkillLevel` is the tier marker on effects**: 0 / 25 / 50 / 75 / 100 = tiers 1–5
  (verified across schools). Set it to match the spell's tier. **GM/perk-gated effects sit at 0**
  and gate through the perk + `HideInUI` instead.
- **MGEF `BaseCost` is NOT the spell's magicka cost** (that lives on the SPEL, `ManualCostCalc`).
  It is the engine's per-magnitude autocalc weight — small on damage (Fire touch 1.2, frost DoT
  3.55), large on binary/utility (Invisibility 25, Paralyze 450), 0 on hidden worker effects.
  Copy the comparable's; never invent it (it feeds enchanting/potion pricing math).

## The `Nox_KW_*` marker vocabulary (106 keywords + `CraftingScrollCraftingTool`)

**Masters in this section are MIXED — check before you write.** A full census (106 `Nox_KW_*` keywords,
2026-07-18) shows **103 are `:Requiem - Magic Redone.esp`** and **exactly three are `:Requiem.esp`**:

| Exception | FormID | Winner |
|---|---|---|
| `Nox_KW_Illusion_Pain` | `482636:Requiem.esp` | MR (override) |
| `Nox_KW_Illusion_Sleep` | `484DDB:Requiem.esp` | MR (override) |
| `Nox_KW_Illusion_Weakness2` | `3C130A:Requiem.esp` | MR (override) |

Every other bare six-digit ID below takes `:Requiem - Magic Redone.esp`. Note the shape of the
exceptions — they are the **six-digit-high** FormIDs (`4xxxxx`, `3Cxxxx`); MR's own are `00xxxx`. When
an ID in this namespace doesn't start `00`, resolve it before writing.

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
- Illusion: `Charm 007332`, `Command 005EC9`, **`Pain 482636:Requiem.esp`**, **`Sleep 484DDB:Requiem.esp`**, `Death 005F02`, `Silence 005EBC`,
  `Blind 005EA9`, `Muffle 005ED1`, `Chameleon 005ED3`, `ShadowDrain 005F03`, `ShadowSummon 005F04`,
  `ShadowStride 005F5A`, `Nightmare 005F4A`, `Sound 005F58`, `Sanctuary 005EB4`, `Dampen 007334`, … (26 total)

**Nox-runtime — perk-gate markers** `Nox_KW_<School>_Perk_<Tree>` (the perk routes read these off the
casting spell): Dest Pyromancy `006015`/Cryomancy `006016`/Electromancy `006017`/ArcaneFocus `006018`/
EntropicFocus `006019`/BloodMagic `006012`; Resto Venomancy `00601A`/Heliomancy `00601B`; Alt Kinetomancy
`006013`/WeatherMagic `006014`; Conj Daedric `005F57`/Spirit `006006`/Necromancy `00732C`/SoulMagic `00732D`.

**Delivery/shape markers:** `Nox_KW_Touch 006081`, `Nox_KW_Nova 006086`. **`Nox_KW_CloakDamage 007609`
is record-side, not runtime** — it is part of the cloak-tick behavioral signature above, so a modded
cloak's damage effect must carry it alongside `REQ_NoMagnitudeScaling` + `REQ_NoDurationScaling`.

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
