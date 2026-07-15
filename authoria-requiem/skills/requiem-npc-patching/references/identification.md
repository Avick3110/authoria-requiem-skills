# Identification — patch or skip, and which analogue

Identification is the hard part of NPC patching: deciding **whether** an actor should be patched at
all, **what role** it plays, and **which Requiem actor** to derive from. Modders rarely leave hints,
so use a layered read rather than any single field.

## Table of contents
- [Step 1 — combatant or skip?](#step-1--combatant-or-skip)
- [Step 2 — which patch-class?](#step-2--which-patch-class)
- [Step 3 — recognizing a boss](#step-3--recognizing-a-boss)
- [Step 4 — numbered NPCs](#step-4--numbered-npcs)
- [Step 5 — mapping to a Requiem analogue](#step-5--mapping-to-a-requiem-analogue)
- [When to ask](#when-to-ask)

## Step 1 — combatant or skip?

**Patch combatants. Skip everyone else.** Skipping the wrong actor (buffing a shopkeeper, de-levelling
a quest NPC) is as bad as missing an enemy. Signals that say **skip**:

- **Class** — a civilian/vendor/job class: `Citizen 01326B`, `FZR_Class_Citizen AE3989`, vendor and
  `Job*` classes, trainer classes on a non-combatant. (Requiem may still touch a vendor's record for
  non-combat reasons — that's not your cue to balance it as an enemy.)
- **AIData** — `Aggression = Unaggressive` (won't start a fight) with low `Confidence`/`Assistance`,
  or `Responsibility` high (law-abiding). Combatants are `Aggressive`/`VeryAggressive`,
  `Foolhardy`, `HelpsAllies`/`HelpsFriendsAndAllies`.
- **Factions** — only job/crime/town factions, no combat faction (bandit/guard/creature/military).
- **Race / flags** — a child race, or a clearly non-combat unique.

**Conjured/summoned actors are combatants, not skips.** The summon *spell* (tier, magicka cost)
routes to the `requiem-magic-patching` skill, but the spell only prices the summon — it does not
de-level the actor. The summoned `NPC_` record still gets the fixed-level/stats pass here like any
combatant; leaving it "balanced via the spell" ships it on `PCLevelMult`, the anti-Requiem pattern.

Signals that say **patch**: a combat class (`REQ_Class_*`, bandit/warrior/mage classes), a combat
style, `Aggressive`+ AI, a combat faction, combat perks, weapon/armor in the outfit.

When an actor is **both** (a guard who trades, a mercenary follower), treat the combat role as
primary but **don't touch its non-combat factions/AI** — patch only its combat balance.

## Step 2 — which patch-class?

Four ways a combatant is patched (see SKILL.md Workflow):

| Patch-class | What it is | How |
|---|---|---|
| **Generic combatant** | bandit, forsworn, marauder, mage, cultist, mercenary mob | template onto `REQ_<Role>_Template_*`, or replicate balance fields |
| **Creature** | animal / undead / daedra / automaton | recognized race → near-nothing; new race → trait bridge |
| **Follower** | recruitable companion | record-side follower factions; **keep PCLevelMult** (`followers.md`) |
| **Boss** | unique, heavily buffed | set fields directly, buff hard (Step 3) |

## Step 3 — recognizing a boss

A boss is a **unique, named** actor Requiem makes much stronger than the generic ladder. Signals:

- **`Unique` flag** set (named, single instance) — the strongest signal.
- **Named editorID**, not a generic `Enc*`/`…Template…`/numbered look-template.
- **High vanilla level** and/or a large stat offset (a caster boss carries a big `MagickaOffset` —
  dragon priests `+1300`; melee bosses high `HealthOffset`).
- **`BleedoutOverride`** flag, a boss-tier `DeathItem`, a dedicated boss combat style, placement as
  an encounter-zone boss or quest target.
- It already carries a `REQ_Trait_*` ability (e.g. `REQ_Trait_ResistMagic30 ADDDC7`) — Requiem's own
  bosses do.

Patch a boss by **setting fields directly** (never templating onto a generic base): high fixed level,
`Unique` (+ `Protected`/`Essential` only if the quest needs it), the strong role class + style, a
rich perk set (vanilla combat perks + Requiem skill perks), and a `REQ_Trait_*` for signature
toughness — **reuse** an existing one (`REQ_Trait_ResistMagic30`, a resist/healing trait) or **author
a bespoke `REQ_Trait_<Boss>`** for this NPC (link it here; design its MGEF in the `requiem-magic-patching` skill). A
named unique can template onto its **generic boss** of the same type for stats/spell-list while
keeping its own perks + trait (how `Rahgot` rides `EncDragonPriest`). Under-buffing a boss to a
generic tier is the failure mode.

## Step 4 — numbered NPCs

Two very different patterns share a numbered editorID:

- **Requiem-style intentional tiers** — `EncBandit01..06`, `REQ_Bandit_Template_*_{Base,01,02,03}`:
  the number encodes an *escalating power tier* (more perks, better gear). These are **meant to
  differ** — preserve the gradient, mapping each to the matching Requiem tier.
- **Mod leveling-copies** — `BanditA01..05` that differ *only* by the number and are fed to a
  `LeveledCharacter` so the game scales them to the player. Requiem is **de-levelled**, so these
  should **collapse to one Requiem tier**: make them identical, fixed-level, and remove `PCLevelMult`
  (the way Requiem flattens vanilla's leveled families). The leveled list that referenced them is a
  concern for the `requiem-leveled-list-patching` skill.

Tell them apart: do the numbered variants carry *different* class/perks/gear (real tiers) or the
*same* everything except the number (leveling-copies)? Are they members of a `LeveledCharacter` keyed
to player level (copies) or placed individually at fixed difficulty (tiers)?

## Step 5 — mapping to a Requiem analogue

Pick the Requiem actor the modded NPC is *meant to be*, then read its balance winner and replicate.
Order of reliability:

1. **Class** — the best giveaway of intended role. A `BanditMelee`/warrior class → a Requiem bandit;
   a conjurer class → a Requiem cultist/summoner; a draugr class → the draugr line. Map to the
   nearest `REQ_Class_*` (see `npc-fields.md`). **Caveat:** lazy mods slap a generic/`DremoraClass` on
   everything — if the class contradicts the gear/name/style, trust the others.
2. **Combat style + gear (outfit)** — confirm the weapon/armor archetype (sword-and-board vs archer
   vs caster) and pick the matching class + tier.
3. **Name + keywords** — decisive for **creatures** (classify by `ActorType*` keyword + Name, never
   the EditorID, which is often a recycled stub). A "MudCrab" named record on a `…Kannimarco…` editorID
   is a mudcrab.
4. **Strength cues** — boss-tier vs trash. The `REQ_Class_Slighted*` set is the weak-mob option; the
   boss path (Step 3) is the strong one.

For creatures specifically, the analogue choice also picks the trait classification — keep it in sync
with whatever `requiem-race-patching` chose for that race (`trait-bridge.md`).

## When to ask

Identification can't always be automated. When the analogue is genuinely ambiguous (an exotic enemy
with no clean Requiem counterpart, a "boss" that might be a quest NPC, a follower that might be
script-registered), **say what you checked and ask** rather than guessing — a wrong analogue gives an
enemy the wrong difficulty or buffs a non-combatant. When nothing fits, say so explicitly.
