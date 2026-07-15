# Phase 3 handoff — `requiem-ammo-patching`

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL + anthropic-skills available). Self-contained. Mirrors the proven Phase 1/2 template. Ammo is small and bounded → build an **exhaustive** catalog, not just samples.*

---

You are authoring **Phase 3** of the Authoria Requiem Patching Skills project: the `requiem-ammo-patching` skill. Phases 1–2 (`requiem-weapon-patching`, `requiem-armor-patching`) established the template — **copy its shape exactly**; this is the ammunition analog, and the smallest domain (arrows + bolts), so it's the one place to be **exhaustive** rather than sample-based.

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL** at `D:\Wabbajack\Authoria-dev`.

## First, orient (before anything else)

1. Read, in order: `STATE.md` (esp. the Phase 1 + Phase 2 notes — the derived-rules pattern, the source-vs-Reqtificator-assigned split, the houseCARL write gotchas, the template), `docs\scope-and-authority.md`, `docs\reqtificator-rules.md`, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Mirror the pilots: read `authoria-requiem\skills\requiem-weapon-patching\SKILL.md` (esp. its **bow** notes — `REQ_WeaponType_Bow{Light/Heavy}`, `Data.Flags=NPCsUseAmmo`, `REQ_BowBreakable`) and `requiem-armor-patching\SKILL.md`, plus their `references/` and `evals/`.
3. Invoke `anthropic-skills:skill-creator` (standard §1).
4. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true` → winner MUST be `Requiem.esp`. If it's `Requiem for the Indifferent.esp` / an `Authoria - *` plugin, run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe.

## Cross-phase rules (confirmed in 2 domains — apply, don't rediscover)

- **Source-carried vs Reqtificator-assigned split:** the patch carries the **input** keywords (material/type) and lets the Reqtificator assign **outputs**; never hand-stamp an assigned keyword. (Weapons: carry weapon-type, Reqtificator assigns damage-type. Armor: carry set+type+part, Reqtificator assigns resist+tempering.) **Determine the ammo split live** — read a real Requiem arrow's `Keywords` and compare to what the Reqtificator config assigns.
- **Preserve author intent:** value tracks the material keyword, not raw damage; don't rewrite a sensible author value unprovoked; **prefer strict normalization** of type-defined stats. (Memory `patching-preserve-author-intent`.)
- **Bespoke/special ammo by acquisition trace:** reverse-reference for `ConstructibleObject` (craftable?) and `LeveledItem` (in loot?). Craftable/leveled ⇒ normal tiered ammo; non-craftable + fixed reward + special effect (exploding/soul/elemental) ⇒ bespoke — keep structure, preserve author effect/value.
- **houseCARL write gotchas** (memory `housecarl-patch-rename-gotcha`): author into a **fresh inactive** patch (active-patch write self-locks). A brand-new never-enabled patch isn't in houseCARL's registry — even `read_record plugin="<patch>"` returns "does not touch"; the **`bulk_apply` per-op read-back is the only verification** until the patch is enabled + `housecarl_set_mo2_instance` refresh. Don't re-issue a Remove the active winner already applied — copy the winner, add only new deltas.

## Scope of this skill

- **In:** all `AMMO` records — **arrows and crossbow bolts**. Their `Damage`, `Value`, `Weight`, material/type keywords, `Projectile` (PROJ) link, `Flags` (bolt vs arrow, NonPlayable), and crafting recipe (COBJ → 24 ammo) + perk gate. Cover normal tiered ammo and special/elemental ammo.
- **Out:** the **bow/crossbow frames** themselves → that's `requiem-weapon-patching` (Phase 1) — cross-reference it, don't duplicate. Spell/effect design for magical arrows → magic phase (Phase 7); the ammo skill sets the AMMO frame + links the projectile/effect, and routes effect *authoring* onward.

## Authority & method

Authoritative = houseCARL's live winner among active plugins. Teach the **live-analogy loop**: identify the new ammo (arrow vs bolt, material tier, special?) → query Requiem's comparable (same type + material) → derive damage/value/weight/keywords/projectile/recipe → apply judgment for special ammo → emit an ESP override via houseCARL. **Never hardcode damage** — derive from the live comparable. Because the set is small, also produce the **full catalog**.

## Mining to do — be EXHAUSTIVE (this is the bounded domain)

- Enumerate every Requiem ammo: `housecarl_cross_plugin_query type="AMMO" plugins=["Requiem.esp"]` (and check WAR `Requiem - Weapons and Armor Redone.esp`, Magic Redone, and any CC/Dawnguard ammo patches for overrides — take the live winner per record). Expect the full vanilla+DLC+CC arrow/bolt set.
- For **every** arrow and bolt, `housecarl_batch_record_detail ... conflict_tree=true` reading `Damage`, `Value`, `Weight`, `Keywords`, `Projectile`, `Flags`. Build the **complete damage/value/weight ladder** (arrows: iron→steel→dwarven→elven→orcish→glass→ebony→daedric→dragonbone; bolts: steel→dwarven→elven→orcish→ebony + exploding/elemental variants). This is the exhaustive catalog.
- Determine the **keyword split** (which keywords are source-carried vs Reqtificator-assigned) and whether arrows carry a **bow-tier / light-heavy** classification that pairs with the Phase-1 bow weight classes (`REQ_WeaponType_Bow{Light/Heavy}`). Note how Requiem's ammo weight + value differs from vanilla (vanilla arrows weigh 0 / value 0–1; Requiem gives them weight + value).
- Inspect the **`Projectile` (PROJ)** a Requiem arrow points to — note speed/gravity and whether Requiem overrides PROJ; decide whether a new arrow reuses a tier-matched vanilla PROJ or needs its own.
- Capture a **crafting COBJ** for arrows (forge, firewood + ingot → 24 arrows) — the recipe pattern, output count, and perk gate (clone `Conditions` verbatim — perk renders as an unreadable form-index).
- Note **special ammo** examples (exploding bolts, soul-stealer/elemental arrows) as bespoke cases.

## Build the skill (conform to the standard §2–§8)

Author into `authoria-requiem\skills\requiem-ammo-patching\`, mirroring the pilots:

- **`SKILL.md`** — `name: requiem-ammo-patching` + single-line action-first `description:` (lead "Patch new arrows or crossbow bolts for Requiem via houseCARL — derive damage/value/weight/keywords/projectile and the craft recipe from Requiem's own comparable ammo…"; ≥3 trigger nouns in "Use when…": *patch arrows/bolts for Requiem, balance modded ammo, set Requiem ammo keywords, arrow damage too high/low for Requiem, add craftable arrows, weightless ammo*; pushy tail "Load this before touching an AMMO record, not after the Reqtificator mis-tiers it"; ≤1,536 chars). Body ≤500 (aim <250): `## Overview` → `## First step` (freshness probe + identify arrow/bolt + tier) → `## Workflow` (live-analogy loop, real houseCARL call shapes) → `## Judgment` (special/elemental ammo via acquisition trace; bow-tier pairing; weight/value sanity) → `## Checklist` (material/type keyword, damage, value, weight, projectile link, flags, craft recipe + perk gate, masters, route effect→magic) → `## Common mistakes` → `## Notes`.
- **`references/`** — `ammo-ladder.md` (the **exhaustive** per-material arrow + bolt catalog: damage/value/weight), `keywords.md` (ammo keyword vocab + FormIDs verified live, with the source-vs-assigned split), `crafting.md` (the 24-ammo forge COBJ pattern + perk gate; PROJ notes), `housecarl-recipes.md` (copy-ready `create_record`/`set_field`/`bulk_apply` shapes for an AMMO override), `worked-examples.md` (2–3 real cases incl. one special-ammo), `index.jsonl`.
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss (near-misses: bow/crossbow weapon patch, armor patch, a throwing-knife/melee case, a fletching mesh question, a magic-arrow *effect* design, a non-Requiem ammo retexture). `manual_predicted_trigger` per query (§6.5). Predicted recall ≥80%, specificity ≥50%.

## Verify before finishing

- **Round-trip:** blind-derive one real Requiem arrow's damage/value/weight/keywords from a comparable (not the record itself), diff against the houseCARL winner; record in `evals/results-<date>.json`.
- Walk the §8 19-item checklist; note deferrals (16–18 → §6.5 manual fallback on Windows; 19 build-script registry N/A — auto-discover).

## Close out

- Update `STATE.md`: set Phase 3 ✅ and append a Phase-3 notes block (the exhaustive ammo ladder, the keyword split, bow-tier pairing, PROJ handling, whether ammo stays standalone vs cross-refs weapons, anything later phases reuse).
- Report back to the master session: what was built, the round-trip result, decisions to fold into later handoffs. Do **not** start Phase 4.
