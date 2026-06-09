# REQ_* core scripts — what runs Requiem, and what not to break

Requiem's framework is ~193 `REQ_*` Papyrus scripts (source:
`…\Requiem 6.0.2 …\Source\Scripts\REQ_*.psc`). You almost never touch these. The point of surveying
them is to know **what is load-bearing** so a patch doesn't override, detach, or starve it — and to
know **what a new piece of content must register with** to participate in a system.

## The load-bearing control scripts (never override/detach)

| Script | Role |
|---|---|
| `REQ_CoreScript` | Base class for Requiem's core scripts; raises the `Requiem_ScriptInit` / `Requiem_ScriptShutDown` mod events the rest of the framework keys off. |
| `REQ_Installation` / `QF_REQ_Quest_Installation` / `REQ_AutoUpdater` / `REQ_VersionController` | Install + version-migration flow. Breaking these breaks a save's Requiem state. |
| `REQ_AttributeSystem` | The attribute/skill backbone (health/magicka/stamina, regeneration, skill effects). |
| `REQ_DeathHandler` | Player death logic. |
| `REQ_MCM` (extends `SKI_ConfigBase`) | The Mod Configuration Menu — all user toggles. |
| `REQ_FollowerRegistration` | Follower tracking (see `follower-registration.md`). |
| `REQ_LockpickControl` / `REQ_LockpickHealth` | Lockpicking integration (works with the SKSE lockpicking DLL). |
| `REQ_Disarm` / `REQ_Disguise` / `REQ_DestroyRangedWeapons` | Combat/stealth mechanics. |
| `REQ_Magic_Spellchoices` / `REQ_Magic_FinishSpell` / `REQ_Magic_XPGain` / `REQ_Magic_Toggle` | Spell-learning, post-cast effects, magic XP, toggles. |

## How new content participates (register, don't replace)

Requiem's systems are **opt-in by marker**, not by script edit. To plug new content into a system you
add the marker it scans for, never a fork of the controller:

- **Follower system** → put the NPC in the follower factions and register its alias
  (`follower-registration.md`).
- **Exhaustion / stress** → the `exhaustionController` / `baseAttackSpeed` spells and the
  `gameMechanics.perks.stress` family are applied to **all actors** by the Reqtificator. A new actor
  needs nothing; a new *attack* type just needs the standard weapon keywords so stamina cost applies.
  (See the router's `exhaustion-stress.md`.)
- **Magic learning** → a new spell is gated by its `HalfCostPerk` tier (record-side, in
  `requiem-magic-patching`); the learning scripts read that, no script edit.
- **Lockpicking** → the SKSE DLL (`RequiemLP.dll`) + `REQ_LockpickControl` drive it; it is config/DLL,
  not record-patchable. New locks behave automatically.

## Diseases — the one script pattern worth knowing

A Requiem disease is a `Touch` / `ConstantEffect` ability **SPEL** (e.g.
`REQ_Disease_Attack_Rockjoint 0B8782:Skyrim.esm`, `…_Ataxia 0B877C`, `…_Witbane`,
`…_BoneBreakFever`, `…_BrainRot`) whose single payload **MGEF** carries the stage/progression logic
(a script on the MGEF, not the SPEL). New disease content reuses this shape: a contraction ability
spell pointing at a staged MGEF. Don't reinvent the staging — clone a Requiem disease MGEF and route
the effect *design* through `requiem-magic-patching`.

## Rule of thumb

If you find yourself wanting to edit a `REQ_*` control script, stop — the intended extension point is
almost always a keyword, a faction, a FormList membership, or an alias registration. Editing the
controller forks the framework and breaks on the next Requiem update.
