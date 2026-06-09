# Enchantments, Staves, Projectiles & Alchemy

The `ObjectEffect` (ENCH) and the special-delivery MGEF. All FormIDs verified live (2026-06-09). ENCH
fields: `EnchantType`, `CastType`, `TargetType`, `ChargeTime`, `EnchantmentCost`, `EnchantmentAmount`,
`Effects` (list of Effect → MGEF + magnitude), `WornRestrictions`, `Flags`, `BaseEnchantment`.

MR touches **1029 ENCH**, all `Flags = NoAutoCalc` (every magnitude/cost is hand-tuned, never
engine-autocalc'd). Family taxonomy (full survey in `mr-content-survey.md` §4): Staff **576**, Armor
**300**, Weapon **105**, Spell-delivered 21, Bound 13, Ammo 12, Artifact 2.

## Weapon enchantments (charge-based)

`EnchantType = Enchantment`, `CastType = FireAndForget`, `TargetType = Touch`, `ChargeTime = 0`. The
per-cast cost is `EnchantmentCost` on the ENCH; the **charge pool** is `EnchantmentAmount` on the WEAP
(≈ 500 × tier, set by `requiem-weapon-patching`). **For a single-effect family, `EnchantmentCost =
EnchantmentAmount = the effect Magnitude`** — one number, no separate knob. Tiers run **0–6**. Fire
ladder (`REQ_Ench_Weapon_Fire0..6`, MGEF `04605A`):

| Tier | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|---|---|
| Cost = Magnitude | 10 | 10 | 20 | 25 | 35 | 40 | 50 |
| FormID (`:Skyrim.esm`) | 10FB95 | 049BB7 | 045C2A | 045C2C | 045C2D | 045C30 | 045C35 |

This `10/(10)/20/25/35/40/50` ladder repeats per element. **Fire is a single ValueModifier(Health);
Frost and Shock are DualValueModifier** (element + a secondary: frost slows via `0B72A0`, shock has the
dwemer-shock interaction `19DC31`). The full weapon-enchant set: Fire/Frost/Shock, Magicka/Stamina
Drain, Soul Trap, Fear, Turn Undead, Absorb Health/Magicka/Stamina, Banish, Paralysis, plus MR-new
**Toxicity** (`03D4EB`, cost 15), **Arcane** (`184F06`, cost 15), Fireburst/Frostburst/Shockburst,
Annihilation, Spellbreaking; triple-effect **Chaos** (Dragonborn) and **Force**. Derive from the
same-element same-tier comparable: copy `EnchantType`/`CastType`/`TargetType`, take the tier's
`EnchantmentCost`, set the magnitude from the comparable, and let the weapon skill set the WEAP pool.

## Apparel enchantments (constant effect)

`CastType = ConstantEffect`, `TargetType = Self`, **no charge pool** — magnitude lives on the MGEF; the
buff is passive while worn. Families `REQ_Ench_Armor_<Effect>NN`, tiers **01–06**. Each numbered tier
sets `BaseEnchantment` → its `_Base` template, and **`WornRestrictions` (the slot keyword) lives only on
the `_Base` record** — the numbered tiers inherit the slot restriction through the base. `EnchantmentCost`
is **100 × tier** (a value figure, not a per-cast depletion). Magnitude ladders:

| Family | Tier 01 | 02 | 03 | 04 | 05 | 06 | MGEF / FormID |
|---|---|---|---|---|---|---|---|
| Resist `<Element>` (%) | 10 | 20 | 25 | 35 | 40 | 50 | ResistFire `048C8B` / `04950B` |
| Fortify Magicka/Health/Stamina (pts) | 20 | 30 | 50 | 70 | 80 | 100 | FortMagicka `049504` / `049508` |
| Fortify `<Skill>` (pts) | +4 | … | … | … | … | … | FortDestruction `07A0F6` |

Cost: resist = 100/200/300/400/500/600; attribute = 100/200/300/400/625/750. Families: Resist
{Fire,Frost,Shock,Magic,Poison,Disease}; Fortify {Health,Magicka,Stamina} (+ their `…Regeneration`,
`…RegenerationRobes`, `…College` variants); Fortify all 18 skills; FortifyArmorRating/CarryWeight/Speed/
Unarmed/Speech; `Potent<School>` spell-potency (+ `…College`); and **MR-new**: FortifyArmorPenetration
(`00784A`, +4 at 01), FortifySneakAttack, **SpellAbsorption** (`00784F`, +2 at 01). Derive from the
same-effect comparable; the difference from a weapon enchant is **constant + no charge pool**.

## Staves

The staff is the largest ENCH family (**576**). The staff **WEAP** (the `requiem-weapon-patching` frame)
carries `EnchantmentAmount` (the charge pool, ≈ 500–1000) and the `Nox_KW_Staff_<School><Tier>` keyword
(`0076E1`–`0076F9:MR`, see `keywords.md`). The staff's `ObjectEffect` is `EnchantType = StaffEnchantment`
(distinct from the plain `Enchantment` of weapon/apparel) and is a **wrapper around the equivalent
spell** — it references the **same MGEF(s) at the spell's own magnitude/duration**, with `TargetType`
following the spell (Aimed projectile / TargetActor / TargetLocation summon) and `EnchantmentCost`
tracking spell power (≈100–300). EditorID `REQ_Ench_Staff_<School><Tier>_<Spell>_<Delivery>`. Summon
staves carry per-effect placement `Conditions`. Examples: `REQ_Staff_Destruction1_Fire_ConcAimed 04DEE0`
"Staff of Flames"; `REQ_Ench_Staff_Alteration4_Paralyze_Aimed 029B4A` (Paralysis dur 3 + cost rider);
`REQ_Ench_Staff_Conjuration4_Daedra_FrostAtronach 029B52` (cost 300, location conditions). A staff is
**craftable two ways** (`mr-content-survey.md` §5): at a forge (`REQ_Forge_Staff_*`, HasPerk+HasSpell,
blank + soul gem) or at the DLC2 staff enchanter (`REQ_StaffEnchanter_Staff_*`, HasSpell only, blank + 2
heart stones). Design the staff's effect like the equivalent spell; the weapon skill sets the frame +
keyword + pool; add the COBJ if it should be craftable.

## Elemental projectile hit (arrow/bolt explosion)

A Requiem elemental arrow puts the elemental damage on the **projectile's explosion**, not on the AMMO
damage. The chain (fire arrow example):

```
AMMO  REQ_Ench_Arrow_Iron_Fire 0E9574:Requiem.esp
  └─ Projectile  0E9575:Requiem.esp
       └─ Explosion  REQ_Explosion_Ammo_Fire_1 010D90:Dawnguard.esm
            ├─ Force 30, Radius 150, Damage 0 (damage comes from the effect, not the EXPL)
            ├─ ObjectEffect  REQ_Ench_Ammo_Fire01 AD3818:Requiem.esp
            │     └─ Effect  04605A (REQ_Effect_Ench_Weapon_Fire)
            ├─ ImpactDataSet 026113:Skyrim.esm
            └─ Sound 03C8FC:Skyrim.esm
```

**You design the EXPL → ObjectEffect → MGEF** (the elemental hit: radius, force, the effect magnitude);
`requiem-ammo-patching` links the AMMO → PROJ → EXPL. To patch a new elemental arrow's hit, reuse a
tier-matched Requiem EXPL/effect or build one on this profile. Spell-cast explosions (Fireball etc.)
follow the same EXPL pattern from the spell's MGEF `Explosion` link.

## Alchemy (potions & poisons)

Authority `Requiem - Alchemy Redone.esp`. Alchemy MGEF differ from spell MGEF:

- **Archetype** `PeakValueModifier` (potency scales with the player's Alchemy skill + ingredient).
- `CastType = FireAndForget`, `TargetType = Self`, `BaseCost ≈ 0.025` (the cost-per-magnitude used for
  potion value), `MagicSkill = None` (no school), no projectile/FX.
- `Flags = PowerAffectsMagnitude, Recover` — magnitude scales, the value recovers.
- Keywords `MagicAlch*` — `MagicAlchHarmful` tags a **poison** (vs a beneficial potion).
- Examples: `REQ_Alch_FortifyDestruction 03EB26`, `REQ_Alch_FortifySpeed 03EB1F`, etc.; the Ingestible
  (ALCH) record links the MGEF and carries the tier (`REQ_Potion_Fortify<X>4`).

**`RFTI_All_PoisonRescaling 962798` (Layer 2) rescales harmful alchemy at build** — don't hand-stamp it.
Design a new potion/poison effect from the comparable Requiem alchemy MGEF: archetype, magnitude,
duration, the `MagicAlch*` keyword, and the flags; let the Reqtificator handle poison rescaling.
