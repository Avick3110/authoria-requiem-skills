# Changelog

All notable changes to the Authoria Requiem Patching Skills plugin. Versioning is [semantic](https://semver.org); the `version` in `authoria-requiem/.claude-plugin/plugin.json` is bumped on each release.

## 1.0.0 — 2026-06-09

First feature-complete release. Nine skills, each built by mining the live Authoria load order via houseCARL and authored to the houseCARL skill-authoring standard.

- **`requiem-patching`** — the integration brain/router: enumerate a plugin, route every record type to its domain skill, own the gap mechanics (vampirism/lycanthropy, diseases, exhaustion/stress, stealth, alchemy, food, shouts, standing stones, perks/skills, economy, the core combat/resistance model), and finish with the masters/`REQ_NULL`/Reqtificator checklist.
- **`requiem-weapon-patching`**, **`requiem-armor-patching`**, **`requiem-ammo-patching`** — the item domains: live-analogy stat/keyword/recipe/value derivation; carry inputs, never hand-stamp the Reqtificator's assigned outputs.
- **`requiem-race-patching`**, **`requiem-npc-patching`** — the actor domains: the two-layer race trait engine, the new-race recognition problem, fixed-level NPC balance, the creature trait bridge, follower setup.
- **`requiem-leveled-list-patching`** — placement: the Reqtificator's additive leveled-list merge, Level-1 de-leveling by repetition, containers, encounter zones.
- **`requiem-magic-patching`** — spells/effects/enchantments: the HalfCostPerk school+tier classifier, explicit costs, the three-layer (record / Reqtificator / Nox-script) split.
- **`requiem-script-patching`** — the Papyrus layer: the Magic Redone `Nox_*` runtime map, modded-follower runtime registration, VMAD attach; the script-vs-no-script gate.
- **Cross-cutting:** the masters + `REQ_NULL`-stripping hygiene gate is folded into every skill's checklist; the `requiem-patching` skill's `references/masters-and-null-stripping.md` holds the mechanism.

### Known follow-ups

- Empirical eval-loop re-validation on a non-Windows host (recall/specificity were validated via the §6.5 manual-prediction fallback; Windows blocks `run_loop`) + a peer prediction check.
- A few live-verification items tracked in `STATE.md` (Phase 8/9): a live VMAD-write round-trip, the `housecarl_compile_script` path, the disease payload MGEF winner, the standing-stone activation quest trace, and the (currently inactive) follower-registration ESP.
