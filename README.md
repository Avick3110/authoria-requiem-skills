# Authoria Requiem Patching Skills

A Claude Code skill plugin that patches newly-added mods to play flawlessly with **Requiem** — deriving every stat, keyword, recipe, perk, and placement by comparing new content **live** against Requiem's own records, and emitting a direct ESP override the **Reqtificator** integrates at build time.

Built for the Authoria Requiem Reforged modlist. **Requires [houseCARL](https://github.com/Avick3110/houseCARL)** (the MCP that gives Claude data-layer access to the load order) pointed at the Authoria MO2 instance.

## What it does

Say *"patch mod X for Requiem"* and the plugin's router enumerates every record the plugin adds, routes each to the right domain skill, handles the cross-cutting gameplay systems, and finishes with a masters / `REQ_NULL` / Reqtificator checklist. The output is an ESP override; you then run it through the Reqtificator.

The method throughout is **live-analogy, never hardcoded numbers**: find Requiem's own comparable record via houseCARL, derive from it, and carry only the *inputs* the Reqtificator's auto-balance pass expects (never hand-stamp its outputs).

## The skills

Load `requiem-patching` first for a whole-mod job; the domain skills also trigger directly.

| Skill | Covers |
|---|---|
| **`requiem-patching`** | The integration brain/router — enumerate a plugin, route every record, own the gap mechanics (vampirism/lycanthropy, diseases, exhaustion, stealth, alchemy, food, shouts, standing stones, perks, economy, the combat/resistance model) |
| `requiem-weapon-patching` | Weapons (melee, bow/crossbow, staff frames) — damage ladder, keywords, tempering/crafting, value, enchant charge |
| `requiem-armor-patching` | Armor (light/heavy, shields, clothing/jewelry frames) — AR ladder, set/part keywords, tempering, fist perk |
| `requiem-ammo-patching` | Arrows & bolts — damage/value ladders, armor-piercing tier, AmmoWeight, projectile profile |
| `requiem-race-patching` | Playable & creature races — stats, skills, ability spells, the two-layer trait engine |
| `requiem-npc-patching` | NPCs/enemies/followers — fixed level, class, combat style, factions, perks, the creature trait bridge |
| `requiem-leveled-list-patching` | Placement — leveled lists (additive merge), containers, encounter zones |
| `requiem-magic-patching` | Spells/effects/enchantments — school/tier/cost, the HalfCostPerk classifier, the three-layer split |
| `requiem-script-patching` | The Papyrus layer — Magic Redone `Nox_*` runtime, follower registration, VMAD attach (most patches need no script) |

Each skill bundles a `references/` library (live-mined ladders, keyword vocab, copy-ready houseCARL call shapes, worked examples) and an archived `evals/` set.

## Install

Requires **houseCARL** installed and configured first — these skills read your Skyrim SE load order through it.

**Easiest — drop it in (no terminal, just like a Skyrim mod):**

1. Extract the download.
2. Copy the `authoria-requiem` folder into your Claude Code skills folder. On Windows that's `C:\Users\<your-username>\.claude\skills\`.
3. Fully restart Claude Code.

The nine skills load automatically, namespaced as `/authoria-requiem:…`. That's it — no commands.

**Alternative — Claude Code's plugin marketplace** (if you'd rather manage it in-app): in a Claude Code session run `/plugin marketplace add <repo-or-local-path>`, then `/plugin install authoria-requiem@authoria-requiem-skills`, and restart. These are in-app slash commands, not a terminal.

## Authority model

Authoritative = houseCARL's live conflict winner among the in-scope Requiem stack (Requiem + its addons). The disabled Authoria overlay (`Requiem for the Indifferent.esp`, the `Authoria - Reqtificated/*` outputs) is excluded; one deliberate exception — `RACE` records resolve to `Authoria - Master Patch - Races Merge.esp`. See the `requiem-patching` skill's `references/scope-and-authority.md`.

## Layout

```
.claude-plugin/marketplace.json   marketplace manifest (for the optional plugin-install path)
authoria-requiem/                 the plugin — drop this folder into ~/.claude/skills/
  .claude-plugin/plugin.json
  skills/<skill>/{SKILL.md, references/, evals/}
```

## Status

**Feature-complete (v1.0.0)** — all nine skills authored and verified by round-trip against live Requiem records. Skill descriptions passed the skill-authoring standard's checks via its manual-prediction fallback (the empirical eval loop needs a non-Windows host); **empirical eval re-validation + a peer prediction check remain as a follow-up**, alongside a handful of live-verification items.

Authored to the houseCARL skill-authoring standard.
