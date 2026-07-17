# MGEF Conditions — when a patched effect needs them, and the exact patterns

Conditions live on the MGEF's own `Conditions` list (distinct from the per-effect `Conditions`
inside a SPEL's `Effects[i]`). Each row is `Data.Function`, `Data.RunOnType` (Subject vs Target),
`CompareOperator`, `ComparisonValue`, and `Flags` (`OR` chains adjacent rows; default = AND).
Functions MR actually uses: **HasKeyword, IsUndead, HasPerk, HasMagicEffectKeyword,
IsCommandedActor, GetActorValue.** All patterns below read live off MR (2026-07-17).

**When to add conditions to a patched MGEF** — three cases, each with its Requiem pattern:

1. the effect should only fire against a **sub-population** (only undead, only daedra, not
   automatons) → patterns A/B;
2. the effect is **perk-gated** (a grandmaster/bonus rider that needs a perk) → pattern C;
3. a **rider** (knockdown, paralyze) needs a resistance/immunity check → pattern D.

A plain damage/heal/buff effect carries **no** conditions — don't add any. And never hand-author a
function/parameter combo: copy the exact rows from the nearest Requiem comparable.

## Pattern A — single creature-type gate

`REQ_Effect_Conjuration3_DaedricHealing_Target` (`005DA1:Requiem - Magic Redone.esp`) — Daedric
healing only heals Daedra; one row:

```
HasKeyword(ActorTypeDaedra 013797:Skyrim.esm)  RunOn=Subject  == 1
```

Same shape on `REQ_Effect_Restoration2_TurnDaedra_Aimed` (`0073A7:MR`).

## Pattern B — OR-chained undead triad (Sun / Turn Undead)

`REQ_Effect_Restoration1_Sun_Touch` (`005C75:MR`) — three rows, all flagged **OR**:

```
[0] HasKeyword(ActorTypeUndead 013796:Skyrim.esm)  Subject == 1   OR
[1] HasKeyword(ActorTypeGhost  0D205E:Skyrim.esm)  Subject == 1   OR
[2] IsUndead()                                     Subject == 1
```

Sun damage fires only vs undead/ghost. Use the full triad, not just the keyword — `IsUndead()`
catches actors whose race carries no keyword. (`REQ_Effect_Ench_Weapon_TurnUndead_Banish 00761D:MR`
prepends an `IsCommandedActor == 1` row for the banish-reanimated case.)

## Pattern C — perk-gate + exclusions (the richest shape)

`REQ_Effect_Destruction5_AbsorbHealth_Aimed_ConsumeLife` (`005DA9:MR`) — three rows ANDed:

```
[0] HasPerk(REQ_Destruction_BloodMagic_050_ConsumeLife 005C68:MR)  RunOn=Target  == 1
[1] HasKeyword(ActorTypeDwarven 01397A:Skyrim.esm)                 Subject == 0    (NOT an automaton)
[2] HasMagicEffectKeyword(REQ_NoLifeDrainAllowed 2EA062:Requiem.esp) Subject == 0  (target not immune)
```

Two idioms to copy exactly: **`== 0` means "does NOT have"** (exclusion), and the perk check runs
**RunOn=Target** — in Requiem's convention that resolves to the *caster* context for perk gates,
while keyword tests on the victim use Subject. Perk-gated GM effects also usually set
`MinimumSkillLevel = 0` and hide via `HideInUI` (the perk, not the skill gate, controls them).

## Pattern D — resistance/immunity threshold on a rider

`REQ_Effect_Destruction4_Fire_TargetLocExp_Knockdown_Knockdown` (`005FF8:MR`) — three rows ANDed:

```
[0] HasKeyword(ImmuneStrongUnrelentingForce 0172AC:Skyrim.esm)  Subject == 0
[1] GetActorValue(ResistMagic)  Subject  LessThan 20
[2] GetActorValue(ResistFire)   Subject  LessThan 40
```

The knockdown rider lands only on non-immune, low-resist targets — the *damage* effect next to it
carries no conditions. When a modded spell has a control rider (knockdown/stagger/paralyze on a
damage spell), give the rider this shape from the element-matched comparable.
