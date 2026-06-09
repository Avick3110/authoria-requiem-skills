# Worked Examples

Real records from the live load order. FormIDs and values verified 2026-06-08.

## 1 — Modded creature race the Reqtificator can't see: Wendigo Troll

**`zzzRevWendigoTrollRace 2E02F7:Glenmoril.esm`** — a new troll-type from a creature mod.
`conflict_tree` shows **one plugin touches it (Glenmoril.esm, override_depth 1)** — Requiem and the
Reqtificator never override it, so it gets **no Requiem traits** from the auto-pass. This is the
central problem.

**What it ships:** `Keywords = [ActorTypeAnimal 013798, ActorTypeCreature 013795, ActorTypeTroll
0F5D16]`; `ActorEffect = [0079A5:Glenmoril.esm]`; `Flags = Walks, NoCombatInWater, CantOpenDoors,
UseAdvancedAvoidance` (no `RegenHpInCombat`); `Starting` H100/M0/S100, `Regen` H2/S10,
`UnarmedDamage 35`; a troll `SkeletalModel`.

**Classify.** `ActorTypeTroll` + the name + the troll skeleton → the **troll** analogue, unambiguous.

**Check its own ability first.** `ActorEffect[0] = 0079A5` is `zzzLrhAbBeastPos` ("Beast Pos
Abilities", 6 effects) — an **`Ab`-prefixed ability bundle**, the mod's own resistances/buffs. Aaron's
rule: **keep it, don't replace.** So we add natural armor + healing but **not** `REQ_Trait_Resist_Troll`
(the race already carries its own resistances).

**The patch** (mirroring `TrollRace 013205`):

```
housecarl_bulk_apply patch_name="Authoria_Races" operations=[
  {formid:"2E02F7:Glenmoril.esm", field_path:"ActorEffect", verb:"Add", value:"AD39E6:Requiem.esp"},  # Armor_Troll
  {formid:"2E02F7:Glenmoril.esm", field_path:"ActorEffect", verb:"Add", value:"AE3AED:Requiem.esp"},  # Healing_Troll
  {formid:"2E02F7:Glenmoril.esm", field_path:"Flags",
   value:"Walks, NoCombatInWater, CantOpenDoors, RegenHpInCombat, UseAdvancedAvoidance"},
  {formid:"2E02F7:Glenmoril.esm", field_path:"Keywords", verb:"Add", value:"586728:Requiem.esp"},      # DropsBlood
  {formid:"2E02F7:Glenmoril.esm", field_path:"Keywords", verb:"Add", value:"5F367F:Requiem.esp"},      # MinorKnockdownImmunity
  {formid:"2E02F7:Glenmoril.esm", field_path:"UnarmedDamage", value:"60"}                              # troll standard (was 35)
]
```

**Layer B (the `requiem-npc-patching` skill).** The troll's `incomingDamageModifier` perk is `physique.fur 000805` (Resist
and Regen Tweak). The Wendigo's NPCs (e.g. `zzzGHM06EncTroll 015A2D:Glenmoril.esm`) won't get it from
the auto-pass — so either template their `Race` onto `TrollRace 013205`, or hand-add perk `000805` to
them. The race skill names it; the `requiem-npc-patching` skill applies it.

**Why each piece:** Armor gives the troll its bulk; Healing + the flag give it the troll's combat
regen (Aaron's flag↔healing rule); keeping `zzzLrhAbBeastPos` preserves the mod's own resistances; the
keywords mark it as bleeding + knockdown-resistant like a Requiem troll. No `FX_` spell was added.

## 2 — Creature round-trip (exact): Frost Troll from Troll

Blind-derive **`TrollFrostRace 013206`** from the normal-troll analogue **`TrollRace 013205`**, then
diff against the live winner.

**Method:** a frost troll is the frost variant of the troll, so predict the same trait *structure* —
natural armor (shared across troll variants) + a frost-flavoured resist + a frost-flavoured healing +
the troll keyword set + `RegenHpInCombat`.

| Field | Predicted | Actual (winner) | Match |
|---|---|---|---|
| `ActorEffect` armor | `Armor_Troll` (shared) | `AD39E6` Armor_Troll | ✓ (shared, not a frost variant) |
| `ActorEffect` resist | a frost-specific resist | `0B556A` Resist_TrollFrost | ✓ (variant) |
| `ActorEffect` healing | a frost-specific healing | `AE3AEE` Healing_TrollFrost | ✓ (variant) |
| `Keywords` | troll set | `[Creature, Animal, Troll, DropsBlood, MinorKnockdownImmunity]` | ✓ identical to normal troll |
| `Flags` | `RegenHpInCombat` set | set | ✓ |
| `Starting`/`UnarmedDamage` | (read from variant) | H750/S500, U65 (vs troll H300/U60) | read live |

**Finding:** natural **armor is shared** across troll variants (`Armor_Troll` on both), while
**resist and healing are per-variant** (`*_TrollFrost`). The trait *structure* transfers from the
analogue; the *stat numbers* (a frost troll is tankier, H750 vs H300) must be read from the variant,
not inferred. `Requiem.esp` is identical-to-winner — the Master Patch carries Requiem's troll faithfully.

## 3 — Playable round-trip: Redguard

Blind-derive **`RedguardRace 013748`** from the playable standard + a sibling, then diff vs the live
winner.

**Predicted ability bundle** (from the family pattern): universal `NoHealthRegeneration 609AF0` +
`MassEffect 82CC14`, + `Heritage_Redguard`, + Blood resistances (Redguard = disease + poison per
`Races.md`). **Actual `ActorEffect`:** `[609AF0, 82CC14, AE3B1B (Blood_ResistDisease_Redguard),
AE3B27 (Heritage_Redguard), AE3B34 (Blood_ResistPoison_Redguard)]` — **exact**.

**Attributes and skill boosts — the live>doc lesson.** `Races.md` lists Redguard **130/60/120** with
`+15 OneHanded`; the **live winner is 100/80/120** (carry 100, unarmed 8, regen 0.20/0.36/0.88) with
skill boosts `OneHanded +10, TwoHanded +10, LightArmor +10, Archery +5, Block +5, Smithing +5`.
**Both the attributes and the skill boosts drift from the doc** — Races Redone flattened them.

**Takeaway:** only the ability-spell **bundle structure** (universal + Heritage + Blood family
pattern) is predictable from the standard; the **attribute and skill-boost numbers must be read from
the live comparable**, never the doc. Clone the bundle by pattern; read every number live.
