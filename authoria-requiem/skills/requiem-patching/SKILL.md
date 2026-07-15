---
name: requiem-patching
description: Plan and produce a complete patch that makes an entire mod play flawlessly with Requiem via houseCARL — enumerate every new record in the plugin, route each to the right Requiem domain skill, handle the gameplay systems no single domain owns, and finish with the masters/REQ_NULL/Reqtificator checklist. Use when the user wants to patch a whole mod for Requiem, make a new plugin Requiem-consistent, ask what a mod needs to work with Requiem, integrate a follower, quest, or creature mod, or handle Requiem's vampire, werewolf and lycanthropy, disease, exhaustion, stealth, alchemy, food, shout, standing-stone, or economy systems for new content. Load this first when starting any "patch mod X for Requiem" job, before diving into individual records, so nothing the plugin touches gets missed.
---

# Requiem Patching

## Overview

This is the **integration brain**: the skill you load when someone says *"patch mod X for Requiem."*
Its job is to take a whole plugin and produce the complete, exact plan that makes it play flawlessly
with Requiem — both **balance** (stats the Reqtificator expects) and **gameplay** (the mechanics that
make Requiem *Requiem*). It does that by enumerating every record the plugin touches, routing each to
the right domain skill, and personally owning the cross-cutting systems no single domain skill covers.

It does **not** re-derive what the seven domain skills already know. `requiem-weapon-patching`,
`requiem-armor-patching`, `requiem-ammo-patching`, `requiem-race-patching`, `requiem-npc-patching`,
`requiem-leveled-list-patching`, and `requiem-magic-patching` each own a record type and its live
analogy method; `requiem-script-patching` owns the Papyrus layer. This skill is the dispatcher over
them plus the gap mechanics (vampirism/lycanthropy, diseases, exhaustion, stealth, alchemy, food,
shouts, standing stones, perks/skills, economy, and the core combat/resistance model).

Read `references/requiem-vision.md` once to hold the "why" in your head — every routing call gets
better when you understand the system the patch has to fit into.

## First step

Confirm houseCARL is fresh, then enumerate the plugin. These are the canonical opening moves:

1. **Freshness probe.** Confirm houseCARL is reading the load order you are patching for — the
   instance, not any record's winner, is what establishes authority. `housecarl_load_order_status`
   must show your Requiem MO2 instance/profile; if it is the wrong instance, fix it with
   `housecarl_set_mo2_instance path="<your MO2 instance>"`. Then sanity-check Requiem is present:
   read Iron Sword `012EB7:Skyrim.esm` `conflict_tree=true` → `Requiem.esp` must appear in the
   override chain. Either winner is valid — `Requiem.esp` (authoring-style profile, generated
   overlay disabled) or `Requiem for the Indifferent.esp` / a later patch (live profile — the
   normal consumer state). The live winner is the authority to derive from; **never** re-point
   houseCARL because the Reqtificator's output wins. Full doctrine:
   `references/scope-and-authority.md`.
2. **Enumerate the plugin.** `cross_plugin_query plugins=["<NewMod>.esp"]` — this returns every record
   the plugin adds or overrides, with type and override depth. That list is your worklist. Group it
   by record type; the counts tell you the shape of the job (an armor pack vs a follower mod vs a
   spell pack vs a quest mod that touches everything).
3. **Triage — classify every row, drop none.** Tag each enumerated record as **new-content**
   (mod-defined, low override depth), **brushed-override** (a record the mod merely touches but doesn't
   own), or **cosmetic-skip** (a pure-appearance touch the Reqtificator's auto-merge handles — NPC
   face/body, race visuals — with the reason noted). Triage *finds* the work; it never *defines*
   coverage — the full enumeration stays the denominator every record is reconciled against at the end
   (the checklist below), so a "brushed" or "cosmetic" tag is a disposition recorded on the record, not
   a licence to drop it from the list. Note too which gap mechanics the content implies (a vampire NPC
   → vampire system; an ingredient → alchemy; a follower → follower registration; a merchant → economy).

## The integration workflow

Work the grouped worklist top to bottom:

1. **Route each record type to its domain skill** (the table below). Load that skill and patch the
   records of that type with it. The domain skills carry the live-analogy method, the keyword splits,
   and the "carry inputs / never hand-stamp Reqtificator outputs" discipline — don't reproduce it
   here, dispatch to it.
2. **Handle the gap mechanics** the content touches (the second table). Each has a
   `references/<mechanic>.md` with the system explained and the integration recipe — usually "route
   the records to a domain skill, but with these Requiem-specific constraints," plus the FormIDs that
   only this skill knows.
3. **Run the final cross-cutting checklist** (`references/integration-checklist.md`): masters correct
   and load-order-sorted, no `REQ_NULL_*` left anywhere, then the **Reqtificator-handles-this vs
   needs-manual** matrix (so you carry exactly the inputs and leave the assigned outputs alone), and
   finally **"run it through the Reqtificator"** — the patch is an input to its auto-balance pass, not
   the finished product.

## Routing table — record type → domain skill

| Record type(s) | Skill | Notes |
|---|---|---|
| `WEAP` (incl. staff frames), weapon `COBJ`/`PROJ` | `requiem-weapon-patching` | melee/bow/staff frame, damage, value, charge, tempering |
| `ARMO` (incl. clothing/jewelry frames) | `requiem-armor-patching` | AR ladder, set/part keywords, fist perk, shields |
| `AMMO` + ammo `PROJ` | `requiem-ammo-patching` | arrow/bolt ladders, AP tier, AmmoWeight, PROJ profile |
| `RACE`, racial `SPEL`/`ARMA` | `requiem-race-patching` | playable vs creature, two-layer trait model, new-race recognition |
| `NPC_` | `requiem-npc-patching` | fixed level, class, perks, trait bridge, bosses, followers (record-side) |
| `LVLI`, `LVLN`, `CONT`, `ECZN` | `requiem-leveled-list-patching` | additive merge, tier by repetition, containers, zones |
| `MGEF`, `SPEL`, `ENCH`, `BOOK` (tomes), `SCRL`, `EXPL`, `HAZD` | `requiem-magic-patching` | school/tier/cost, HalfCostPerk classifier, three-layer split |
| `INGR`, `ALCH` (potions, poisons, food, drink) | `requiem-magic-patching` + `references/alchemy.md` / `references/food.md` | per-record disposition; effects reuse Requiem's `REQ_Alch_*` / nutrition MGEFs. Where those references don't settle a record's balance, flag the remainder to the user — never skip it |
| `SHOU`, `WOOP` | `references/shouts.md` + `requiem-magic-patching` | shout wrapper (3 words → 3 tier `SPEL` + recovery); the SHOU/WOOP frame is owned by `Requiem.esp` |
| `PERK` | `references/perks-skills.md` | no domain skill owns standalone `PERK` rebalancing — still disposition every mod `PERK`: apply `perks-skills.md`'s constraints where they reach and flag what remains to the user; never silently skip the type |
| `VirtualMachineAdapter` / script need; follower runtime registration | `requiem-script-patching` | reuse Nox/REQ scripts; most patches need none |

**Every enumerated record type lands in exactly one disposition** — routed to a domain skill, handled
via a gap-mechanic reference, skipped as cosmetic *with a stated reason*, or flagged "no owner" to the
user. A type absent from these tables, or one you don't recognize, is never dropped on sight — surface
it and assign one of those four, because a silently skipped type is the field failure this rule kills.
The full signature map and the same catch-all live in `references/routing-table.md`.

When a record implies a **gap mechanic**, route the record to its domain skill *and* apply the gap
reference's constraints. Example: a new ingredient is an `INGR` whose effects are `MGEF` → patch via
`requiem-magic-patching`, but `references/alchemy.md` says the effects must reuse Requiem's
`REQ_Alch_*` MGEFs and carry the alchemy keywords. Vampire/werewolf content is `RACE` + `NPC_` +
`SPEL` → route to those skills, with `references/vampirism-lycanthropy.md`'s trait/keyword constraints.

## Gap-mechanic routing — system → reference

| The plugin adds… | Reference | Core constraint |
|---|---|---|
| A vampire/werewolf race, NPC, or power | `vampirism-lycanthropy.md` | carry the vampire/werewolf keyword/race; Reqtificator assigns the `RFTI_Trait_Vampire`/`Werewolf` perk — don't hand-stamp |
| A contractable disease | `diseases.md` | clone the staged-MGEF-under-a-Touch-ability-spell pattern |
| New attacks / stamina / casting behavior | `exhaustion-stress.md` | the stress/exhaustion perks are all-actor Reqtificator outputs; carry standard weapon keywords |
| Sneak/lockpick/pickpocket/trap content | `stealth.md` | sneak perks are player-exclusive; lockpicking is an SKSE DLL, not record-patchable; traps are records |
| Ingredients / potions / poisons | `alchemy.md` | ingredient effects must use Requiem `REQ_Alch_*` MGEFs; poison rescaling is a Reqtificator output |
| Food / drink / alcohol | `food.md` | food gives long stacking effects by nutrition category; alchemists don't buy food |
| A new shout / words of power | `shouts.md` | SHOU = 3 words → 3 SPEL + recovery; owned by `Requiem.esp` |
| A standing stone / birthsign / doomstone | `standing-stones.md` | ConstantEffect ability spell with stacked MGEFs; player-only |
| New perks / skills / a perk tree | `perks-skills.md` | hook into existing trees; never author a parallel tree; player-exclusive assignment |
| A merchant / vendor / prices | `economy.md` | record-driven vendor chests + LVLI; round values to 5; no dynamic-pricing config |
| (the "why" for any combat call) | `combat-resistance.md` | damage vs resistance/armor, armor-penetration by damage type, stagger, the keyword chain |

## Common mistakes

- **Patching records in isolation and missing the system.** A vampire follower is not just an `NPC_`
  — it implies the vampire trait keyword, the follower registration, maybe a unique power. Enumerate
  first, then route; the worklist is what stops you missing a record type.
- **Re-deriving a domain skill's rules here.** If you're computing armor ratings or spell costs in
  this skill, stop and load the domain skill — it has the live-analogy method and the keyword splits.
- **Hand-stamping a Reqtificator output.** The whole load order is balanced by the Reqtificator's
  pass. Carry the *inputs* (material/type/set/race keywords, class, the vampire keyword) and leave the
  *assigned outputs* (damage-type keyword, resistance/tempering tiers, trait/`RFTI_*` perks, merged
  lists) for the build. The checklist's matrix is the source of truth per domain.
- **Treating the patch as the finished product.** It is an *input* to the Reqtificator. End every job
  with "run it through the Reqtificator," and check the masters + no-`REQ_NULL` gate first.
- **Assuming a mechanic is record-patchable when it's a DLL/config.** Lockpicking is an SKSE DLL;
  vendor pricing is record-driven but has no JSON to edit. Know which systems you can touch.

## Checklist

- [ ] Freshness probe passed; plugin enumerated with `cross_plugin_query plugins=[...]`.
- [ ] Worklist grouped by record type; every row classified (new-content / brushed-override /
      cosmetic-skip) with the full enumeration kept as the coverage denominator, not filtered down.
- [ ] Every record type routed to its domain skill and patched there. **Per-record disposition applies
      to every type, not a closed high-count list** — for any type carrying more than a handful of
      records, run that domain skill's bulk pass protocol (each domain skill carries one); never sample
      one record and extrapolate to the rest, for any type.
- [ ] **Top-level reconciliation across all types:** (routed + patched) + (skipped with a stated
      reason) + (flagged "no owner" to the user) = the total enumerated at the First step. The job is
      not done while any enumerated record lacks a disposition (per
      `references/integration-checklist.md` item 1).
- [ ] Every gap mechanic the content implies handled per its reference (constraints applied).
- [ ] `references/integration-checklist.md` run: masters correct + load-order-sorted; **no
      `REQ_NULL_*` anywhere**; Reqtificator-vs-manual matrix respected.
- [ ] Plan ends with "run the patch through the Reqtificator."

## Notes

- **Shape:** this is a router (First step + routing table)
  *combined with* the operational integration workflow the job needs. The body is kept tight and the
  deep "why" + the gap mechanics live in `references/`; that hybrid is deliberate and justified by the
  skill's dual role (dispatcher + owner of the cross-cutting systems).
- **Authority** is the live houseCARL conflict winner — on a live profile that includes
  `Requiem for the Indifferent.esp`, the Reqtificator's output, which folds the build pass over
  the hand-authored Requiem stack (see `references/scope-and-authority.md`). Races legitimately
  resolve to a hand-authored race merge where one exists.
- **Boundary:** this skill plans and routes; the domain skills do the record work. When a gap can't be
  fully resolved live, document what you found and flag it — an explicit "I cannot" beats a confident
  wrong answer.
