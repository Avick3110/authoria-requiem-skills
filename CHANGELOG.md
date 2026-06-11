# Changelog

All notable changes to the Authoria Requiem Patching Skills plugin. Versioning is [semantic](https://semver.org); the `version` in `authoria-requiem/.claude-plugin/plugin.json` is bumped on each release.

## 1.0.1 — 2026-06-11

Bugfix (HCBR-2026-06-11-03): the freshness probe tested a machine-specific fingerprint, not the real invariant.

- **Freshness probe (all nine skills + `scope-and-authority.md`)** — the probe required Iron Sword's conflict *winner* to be `Requiem.esp` and prescribed a `housecarl_set_mo2_instance` re-point for anything else. That fingerprint only matched the author's mining profile, where the Reqtificator's generated output was disabled. On a live profile `Requiem for the Indifferent.esp` is enabled and legitimately wins, so the probe false-alarmed every time; its remedy would re-point houseCARL *away from* the load order being patched, and `scope-and-authority.md` escalated to a hard "do not proceed" deadlock (plus a plugin-count staleness heuristic that was also a machine fingerprint — both removed). The probe now verifies the real precondition — `housecarl_load_order_status` shows the instance/profile you are patching for, with Iron Sword's chain containing `Requiem.esp` as a sanity check — and documents both healthy winner shapes (overlay disabled ⇒ `Requiem.esp`; live profile ⇒ the Reqtificator output or a later patch). Re-pointing is prescribed only for a genuinely wrong instance, never as a response to the Reqtificator's output winning.
- **Authority doctrine made mode-aware; the races correction propagated pack-wide** — the live conflict winner is the authority to derive from on any profile; on a live profile that includes `Requiem for the Indifferent.esp`, whose values are what the list actually plays (a Reqtificator rescale of the comparable folds into the read automatically). What a patch still never *copies* are the Reqtificator-assigned outputs (damage-type / resist-tier / tempering keywords, `RFTI_All_*` perks), and hand-tuned stats still take the `RFTI_Exclusions_*` kit. The race skill's "the live winner is correct — don't re-point to escape it" rule, which already contradicted the old probe inside the same file, is now the shared doctrine in every skill.

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
