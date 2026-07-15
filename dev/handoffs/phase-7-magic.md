# Phase 7 handoff — `requiem-magic-patching`

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL **1.2.1** + anthropic-skills available). Self-contained. Mirrors the proven Phase 1–6 template. Magic Redone is authoritative; staves were folded into Phase 1 (frame) so this phase designs the **effect**, not the staff item.*

---

You are authoring **Phase 7** of the Authoria Requiem Patching Skills project: the `requiem-magic-patching` skill. Phases 1–6 established the template — **copy its shape exactly**. This domain is **spell/effect/enchantment design** — the `MGEF`/`SPEL`/`ENCH` records behind spells, enchanted gear, staves, and elemental projectiles.

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL 1.2.1** at `D:\Wabbajack\Authoria-dev`.

## First, orient (before anything else)

1. Read, in order: `STATE.md` (esp. **Phase 1 notes** — the staff frame `Nox_KW_Staff_<School><Tier>` + the enchant charge model — and **Phase 3 notes** — the elemental-arrow PROJ→explosion routed here — plus the houseCARL 1.2.1 master note + the start-of-session checklist), `docs\scope-and-authority.md`, `docs\reqtificator-rules.md` (the magic rescaling perks + the magicka-cost formula noted as a Layer-1 underivable), **`docs\masters-and-null-stripping.md`**, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Skim a prior skill for the exact reference/eval shape, and re-read the **staff** section of `requiem-weapon-patching\SKILL.md` (this phase designs the staff's enchantment/effect that the Phase-1 frame links).
3. Invoke `anthropic-skills:skill-creator` (standard §1).
4. **Freshness probe:** `housecarl_read_record formid="012EB7:Skyrim.esm" conflict_tree=true` → winner MUST be `Requiem.esp`. If it's `Requiem for the Indifferent.esp` / an `Authoria - *` plugin, run `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe. (Magic winners come from Magic Redone — verify a known spell resolves to `Requiem - Magic Redone.esp`, not the disabled overlay.)

## Operating principles (apply, don't rediscover)

- **Be the master — make studied calls** (memory `be-decisive-master`): decide a spell's school/tier/cost/magnitude from the live comparable + the MR patch precedents; act and explain. Reserve questions for genuinely unknowable design intent.
- **houseCARL 1.2.1:** write **directly into active patches**; verify a brand-new patch via the `bulk_apply` read-back; use `where=` to find comparables (e.g. `cross_plugin_query type="MagicEffect" plugins=["Requiem - Magic Redone.esp"] where="MagicSkill = Destruction"`).
- **Live > shipped docs.** Read costs/magnitudes from the live winner; never trust a wiki.
- **Masters + REQ_NULL** per `docs\masters-and-null-stripping.md` (reference a real Requiem/MR form so `Requiem.esp`/MR masters in; strip `REQ_NULL_*`).
- **Check the per-domain split for magic** — it has THREE layers (record / Reqtificator / script), below.

## Authority — Magic Redone, and the MR-patch addons are your ground truth

Authoritative = `Requiem - Magic Redone.esp` (overrides base Requiem for spells/effects), + its patches (`Requiem - MR … Patch.esp`). **Crucially, the load order already contains addons that patch NEW spell packs for Magic Redone** — `Requiem - Constellation Magic - Magic Redone Patch.esp`, `… Obscure Magic …`, `… Dark Hierophant …`, `… Holy Templar …`, `… Sonic Mage …`, `Wildwaker Magic - Requiem Magic Redone Patch.esp`, `Requiem - Apocalypse Compatibility Patch.esp`. **These are real worked examples of "a new magic mod patched for Requiem"** — mine them to derive the recipe and as worked examples. They are the single best evidence for this whole skill.

## The three-layer magic split (determine each live)

1. **Record-side (source-carried):** the MGEF/SPEL/ENCH design — `MagicSkill` (school AV), `CastType`/`TargetType`, base cost, charge time, the effect list (magnitude/area/duration + conditions), keywords (`MagicDamage{Fire,Frost,Shock}`, school keywords, `Nox_KW_*`). This is what you author.
2. **Reqtificator-assigned (don't hand-stamp):** the global magic rescaling perks — `magic.absorbRescaling 962799`, `wardDamageReduction 682FB5`, `poisonRescaling 962798`, `npcExclusive persistentSpellRescaling AD3977` (verify live). These are added at build time.
3. **Script-driven (Magic Redone's `Nox_*` runtime):** Magic Redone ships `Nox_Destruction_*`, `Nox_Restoration_*`, `Nox_Conjuration_*`, etc. `.pex` that scale/empower effects at runtime, often keyed off a keyword on the spell/effect. **Determine how much of an effect's behavior is the Nox script vs the record**, and what keyword/marker a new spell must carry to be picked up. **Route the script side to Phase 8** (the script-patching skill) — this skill carries the record-side marker; Phase 8 covers the runtime.

## Scope of this skill

- **In:** `MagicEffect` (MGEF), `Spell` (SPEL — tomes' taught spells, abilities, powers), `ObjectEffect`/`Enchantment` (ENCH) for enchanted weapons/armor/jewelry and **staves**, and the **elemental projectile effect** (the explosion/MGEF a Phase-3 elemental arrow links). The cost / magnitude / school / keyword design. Shouts (`SHOU` + words) are a niche sub-case — handle briefly if encountered.
- **Out:** the weapon/armor/staff/ammo **frames** (Phases 1–3 link the `ObjectEffect`; you design it); the **placement** of spell tomes / staves in lists (Phase 6); **script runtime** scaling (Magic Redone `Nox_*` → Phase 8). Cross-reference these.

## Mining to do

- Authority + counts: `cross_plugin_query type="Spell"` / `"MagicEffect"` / `"ObjectEffect"` scoped to `plugins=["Requiem - Magic Redone.esp"]` and base `Requiem.esp`. Confirm winners resolve to Magic Redone.
- **Derive the cost/tier model:** sample destruction/restoration/alteration/conjuration/illusion spells across novice→master with `batch_record_detail ... conflict_tree=true`. Read `BaseCost`, `ChargeTime`, `CastType`, `TargetType`, the effect list (`Effects[i].Data` magnitude/area/duration), `Keywords`, and whether cost is explicit vs auto-calc. Build the **per-school, per-tier** cost + magnitude reference. Read the live magicka-cost formula source if cost is computed (Layer-1 underivable in `reqtificator-rules.md`).
- **Mine the MR-patch addons** (Constellation/Obscure/Dark Hierophant/etc.): read how each rebalanced its new spells' cost/magnitude/keywords vs the mod's originals (`conflict_tree` delta) — this IS the patching recipe. Capture 2–3 as worked examples.
- **Enchantments:** read a weapon ENCH (charge-based — per-cast cost; pairs with the Phase-1 `EnchantmentAmount = 500×tier` charge pool) and an apparel ENCH (constant effect, no charge). Derive the enchant effect-magnitude model by tier.
- **Staves:** read the ENCH/effect a Requiem/MR staff casts and how its `Nox_KW_Staff_<School><Tier>` tier maps to effect power (cross-ref Phase 1).
- Determine the **Nox script hooks** (which keyword/marker a spell needs for Magic Redone runtime scaling) → note for Phase 8.

## Build the skill (conform to the standard §2–§8)

Author into `authoria-requiem\skills\requiem-magic-patching\`, mirroring the pilots:

- **`SKILL.md`** — `name: requiem-magic-patching` + single-line action-first `description:` (lead "Patch a new spell, magic effect, or enchantment for Requiem (Magic Redone) via houseCARL — derive school, cost, magnitude, keywords, and the enchant/staff effect from Requiem's own comparable magic…"; ≥3 trigger nouns in "Use when…": *patch a spell for Requiem, balance a modded spell/enchantment, set Magic Redone cost/magnitude, a spell costs too little/much magicka, design a staff or enchanted weapon effect, an elemental arrow's hit effect*; pushy tail "Load this before touching an MGEF/SPEL/ENCH, not after the spell breaks Requiem's magic economy"; ≤1,536 chars). Body ≤500 (aim <250): `## Overview` → `## First step` (freshness probe + identify school/tier/delivery) → `## Workflow` (the live-analogy loop + mine-the-MR-patch precedent, real houseCARL call shapes) → `## Judgment` (cost/tier calls, the three-layer split, don't hand-stamp rescaling perks, route Nox runtime → Phase 8) → `## Checklist` (school AV, cast/target type, cost, charge, effect magnitude/area/duration, keywords incl. Nox marker, enchant charge model, masters, REQ_NULL, route frame→1-3 / placement→6 / script→8) → `## Common mistakes` → `## Notes`.
- **`references/`** — `cost-and-magnitude.md` (per-school/per-tier cost+magnitude+charge, from live), `keywords.md` (school/element/`Nox_KW_*` vocab + FormIDs verified live + the three-layer split), `enchantments.md` (weapon charge model + apparel constant + staff effect), `housecarl-recipes.md` (copy-ready `create_record`/`bulk_apply` shapes for an MGEF/SPEL/ENCH + composing the `Effects` list), `worked-examples.md` (2–3 real MR-patch-addon cases), `index.jsonl`.
- **`evals/eval_set.json`** — ≥8 should-trigger + ≥8 near-miss (near-misses: a staff *item* frame (Phase 1), a spell-tome's *value/placement* (Phases 1/6), an enemy caster's *spell list* assignment (Phase 5), a Nox *script* scaling question (Phase 8), a potion/poison (alchemy), a non-Requiem spell-VFX retexture). `manual_predicted_trigger` per query (§6.5). Predicted recall ≥80%, specificity ≥50%.

## Verify before finishing

- **Round-trip:** blind-derive one real Magic Redone spell's (or an MR-patch addon spell's) cost/magnitude/school/keywords from a same-school same-tier comparable, diff against the houseCARL winner; record in `evals/results-<date>.json`. Confirm you did not hand-stamp a Reqtificator rescaling perk.
- Walk the §8 19-item checklist; note deferrals (16–18 → §6.5 manual fallback on Windows; 19 build-script registry N/A — auto-discover).

## Close out

- Update `STATE.md`: set Phase 7 ✅ and append a Phase-7 notes block (the cost/magnitude model, the three-layer split, the enchant/staff model, the Nox script hooks to cover in Phase 8, the authority, and anything Phase 8 reuses).
- Report back to the master session: what was built, the round-trip result, and **specifically what the Phase-8 script handoff must cover for Magic Redone's `Nox_*` runtime** (plus the follower-registration scripts already flagged). Do **not** start Phase 8.
