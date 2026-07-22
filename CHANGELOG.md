# Changelog

All notable changes to the Authoria Requiem Patching Skills plugin. Versioning is [semantic](https://semver.org); the `version` in `authoria-requiem/.claude-plugin/plugin.json` is bumped on each release.

## 1.9.0 — 2026-07-22

Tool-surface refresh (Aaron, 2026-07-22): a user reported `requiem-armor-patching` burning a lot of
tokens. Investigation cleared the live-analogy doctrine and traced the cost to skills written against
an older houseCARL query surface. Closes
[#41](https://github.com/Avick3110/authoria-requiem-skills/issues/41). Bodies + references only — no
descriptions changed, so no §6.5 fan-out re-measure is owed, and no derivation rule moved.

- **Doctrine verified, not changed.** Diffed `Requiem.esp` → `Requiem for the Indifferent.esp` across
  six armor comparables (steel/ebony heavy cuirass, glass/elven/fur light cuirass, ebony shield): the
  only delta is `Keywords` — `ArmorRating`/`Value`/`Weight` are identical. The build pass adds exactly
  `REQ_Armor_Resistance_Ranged_Tier*` and `REQ_Tempering_*`, the two families the skills already say
  never to hand-stamp, and the shield took tempering but no resist tier, confirming that ranged
  resistance gates on the cuirass part keyword. "Derive from the live winner" is sound; there is no
  numeric pass to double-apply.
- **List-valued `references=` across `requiem-{armor,weapon,ammo,leveled-list,race}-patching`.** Every
  reverse-lookup (recipe COBJ, leveled-list placement, artifact acquisition trace, race→NPC) took one
  FormID per call; they now take the whole set at once, with the `matches` column naming which record
  each hit belongs to. A five-piece set's recipes went from five calls to one.
- **The recipe read is now a verified two-call shape.** `cross_plugin_query` has no `depth=`, so
  `Items`/`Conditions` return as `[list: N item(s)]`; they are expanded via
  `batch_record_detail ... depth=4 resolve_names=true`. **`depth=4` is documented as the working
  depth** — `depth=2` yields only element types and `depth=3` stops short of the values. This replaces
  the old "houseCARL 1.2.2+ renders the perk parameter as a readable FormID" note, which promised
  readability without saying what makes it readable, leaving agents to probe for the depth.
- **`format="dense"` and `resolve_names=true` on the whole-plugin triage sweeps.** Columnar rows
  instead of a labelled envelope per field, and link fields annotated with what they point at, so
  triage reads from identities. Paging via `limit=`/`offset=` is named. Coverage scope is deliberately
  unchanged: the sweeps still enumerate every record the plugin *touches*, with `defined_in=true`
  called out as a narrowing that would drop overridden vanilla records the reconciliation count
  expects.
- **`conflict_tree=true` dropped where only winner values are consumed** (comparable reads in armor,
  weapon, ammo). It is retained on the freshness probe and post-enable verification, where the chain
  is the point.
- **Whole-plugin passes now point at houseCARL's `bulk-record-jobs`** when the product is a
  deliverable (catalogue, audit, conflict survey) rather than a set of edits.

## 1.8.0 — 2026-07-18

Bruma whole-mod pass follow-up (Heisen, 2026-07-18): manually corrected NPC/COBJ overrides exposed
write-shape and authority gaps in the skills. Closed [#38](https://github.com/Avick3110/authoria-requiem-skills/issues/38)
and [#39](https://github.com/Avick3110/authoria-requiem-skills/issues/39) in the skill layer only; no
existing load-order patch was regenerated or edited. Body + references only — no descriptions
changed, so no §6.5 fan-out re-measure is owed.

- **`requiem-npc-patching` — inherited balance templates are no-write skips (#38).** NPCs whose
  resolved template chain inherits the relevant stats/spell-list balance stay entirely untouched,
  including masked local `REQ_NULL_*` perks or spells. The forwarded-NULL sweep is now explicitly a
  candidate scan, not permission to create cleanup overrides. The existing positive-evidence initial
  triage rule was audited and already prevents the earlier "assume Requiem-shaped" skip.
- **`requiem-npc-patching` — one user-selected stat authority per plugin (#38).** Before the first
  write the user chooses AutoCalc mode (`AutoCalcStats` on; class + level authoritative; no DNAM or
  ACBS stat-offset writes) or manual mode (flag off; complete derived DNAM + ACBS block). The choice
  applies to every non-templated NPC entering the plugin's stat pass, including followers without
  changing their `PcLevelMult` exception.
- **`requiem-npc-patching` — sparse writes and exact flag deltas (#38).** Optional `CombatStyle`,
  `AttackRace`, and other nullable links are removed rather than set to null. Empty `Perks` and
  `ActorEffect` results are never emitted via empty `ReplaceAll`; verification now requires the list
  and its count subrecord to be absent (`PRKZ`/`SPCT`, not serialized zero). Flag writes preserve all
  unrelated winner bits and may not acquire `LoopedScript`/`LoopedAudio` from a comparable.
- **`requiem-weapon-patching` + whole-mod integration — disabled COBJ convention (#39).** An existing
  recipe is disabled with `WorkbenchKeyword = REQ_DisableRecipe AD3B01:Requiem.esp`, never a removed
  or null workbench link. Copy-ready houseCARL calls and the final integration gate now enforce it.

## 1.7.0 — 2026-07-18

Field report round 2 (Heisen, 2026-07-18) on the same Apocalypse patch: spells received only
cost/charge/flags/`HalfCostPerk` while the **balance-bearing** work was skipped, and the skill's own
checklist let that reconcile as "patched". Closed by defining "patched", making magnitude derivation
mandatory, and deriving the real subtype/rider vocabulary live across all five schools (five parallel
read-only agent lanes + maintainer spot-verification of every load-bearing claim). Body + references
only — no descriptions changed, so no §6.5 fan-out re-measure owed.

- **`requiem-magic-patching` — magnitudes are mandatory, and "patched" now has a definition.** A SPEL
  counts as patched only with **all** of: cost + `ManualCostCalc` union, tier `ChargeTime`, correct
  `HalfCostPerk`, **`Effects[i].Data` magnitudes derived from the comparable**, the comparable's
  **subtype keyword(s)** on the MGEF, its **mechanical tier-resolved riders**, the primary MGEF's
  **behavioral keyword/flag signature**, no `REQ_NULL_*`, and its tome priced. Items 1–3 are the
  *economy*, 4–6 the *balance*; a spell with the economy and none of the balance is **cost-normalized,
  not patched — it deals the mod's damage at Requiem's price**. Named as the #1 field failure in
  Common mistakes and gated in both the Checklist and the bulk-pass reconciliation.
- **`requiem-magic-patching` — subtype keywords: premise corrected, table derived.** The report asked
  for a SPEL-side subtype keyword table; **MR Spell records carry no keywords at all** (915 scanned,
  `Keywords` unset on every one), so the classification lives on the **MGEF** and the checklist line is
  written against the MGEF. Full per-school table added to `references/keywords.md` with verified
  masters. Also corrected: **`Nox_KW_<School>_Perk_*` keywords do NOT belong on plain damage effects** —
  MR's own Fire/Frost/Shock damage MGEFs carry none; Pyromancy/Cryomancy/Electromancy
  (`006015`/`006016`/`006017`) sit on **cloak/enchant/hazard** forms only. The prior SKILL.md advice to
  "carry that school's specialization keyword" was wrong for damage spells and is now a Common mistake.
  Many subtypes have **no** subtype keyword (Calm/Fear/Frenzy, Healing, Ward, TurnUndead, mage armor).
- **`requiem-magic-patching` — rider doctrine rebuilt: mechanical vs cosmetic, delivery, tier.**
  A cosmetic rider does **not** satisfy the requirement, and two distinct cosmetic shapes are now
  named: Illusion `_Desc_` tooltip stubs (`_Desc` + no `Keywords` + `MinimumSkillLevel 0`) and
  Destruction `_Taper` FX carriers (`ActorValue = Fame`, magnitude 0). **`Archetype.Type = Script` is
  explicitly refuted as the discriminator** — it fails both ways (Destruction `Slow_Aimed 0B729F` is
  Script *and* mechanical; Conjuration's cosmetic `Bound_Weapon_<Element>` are Script with no VMAD).
  Riders are **delivery-gated** (Impact Stagger on projectiles only; Taper on concentration only) and
  magnitudes are **copied, never guessed** — the shipped bug's `Slow` 50 (real: **5**) and
  `Impact_Stagger` 25 (real: **0.25**) are now called out by value.
- **`requiem-magic-patching` — "tier-matched riders" corrected: the rule is school-dependent.** The
  report asked for a flat tier-match rule; live data shows two families. Destruction riders are
  **tier-indexed records** (`Cremation2/3/5`, `ElectrostaticDischarge1–5`), but Illusion `_Improved`
  riders are **shared tier-2 stubs** — `REQ_IllusionGM_Pain4` *and* `Pain5` both point at
  `GM_Pain2_Improved 00594C`, with the tier carried in the spell's per-effect `Data.Magnitude` and the
  rider's own `MinimumSkillLevel` frozen at 25. So **never infer a spell's tier from its rider's tier
  marker**; copy both the rider FormID and its Data from the comparable.
- **`requiem-magic-patching` — magnitude anchors added** (the cross-check, not a substitute for
  reading): Destruction base damage is **identical across fire/frost/shock at a tier** (T2 30, T3 32,
  T5 64); Healing's Respite rider is **exactly 50%** of the heal; Ward's `Ward_Shield` is **1:1**;
  mage-armor riders are **50% and 20%** of base at Duration 60; summons are **Magnitude 0, Area 0,
  Duration 15** (thralls 300, a subtype property not a tier one).
- **`requiem-magic-patching` — the Association doubling rule corrected.** Re-verified 14/14, but the
  doubled form is **not necessarily a `Nox_KW_*`**: mage armor doubles vanilla `MagicArmorSpell
  01EA72:Skyrim.esm`, TransmuteMuscles doubles `REQ_TransmuteMuscles 682FB1:Requiem.esp`. A prior
  revision attributed `Nox_KW_Alteration_Shield 000813` to mage armor; it belongs to the **elemental
  shields**. Two live exceptions recorded (Waterwalking, `Wind_Cloak_Speed`), plus *why* the doubling
  exists — MR's `Script`-archetype dispel effects match on the Association.
- **`requiem-magic-patching` — 1.6.0's blanket master note fixed (regression).** 1.6.0 asserted every
  FormID in the `Nox_KW_*` section takes `:Requiem - Magic Redone.esp`. A full 106-keyword census shows
  **three are `:Requiem.esp`** — `Nox_KW_Illusion_Pain 482636`, `Nox_KW_Illusion_Sleep 484DDB`,
  `Nox_KW_Illusion_Weakness2 3C130A`. Replaced with an exception table plus the shape tell (MR's own are
  `00xxxx`; the exceptions are high six-digit). Section header count corrected 107 → 106 + 1.
- **`requiem-magic-patching` — bulk-pass detail moved to `references/bulk-pass.md`** (new) to hold
  SKILL.md under the 500-line cap per authoring standard §4.1; SKILL.md keeps a pointer and the
  load-bearing summary. Also records that the sweep's `Keywords`/`Flags`/`Effects` columns return
  `[list: N item(s)]` — a count that reads like data — so contents need `depth=2`/`depth=4` follow-ups.

## 1.6.0 — 2026-07-18

Field report (Heisen, 2026-07-18): the shipped Apocalypse patch left modded MGEFs unpatched because
agents dispositioned them as **"already Requiem-correct"** on the strength of `MagicSkill` + a vanilla
element keyword + a `MinimumSkillLevel` tier marker — while the effects lacked Requiem's own behavioral
keywords. Traced live on ARR and closed with a re-derived rule. Body + references only — no descriptions
changed, so no §6.5 fan-out re-measure owed.

- **`requiem-magic-patching` — the MGEF keyword+flag signature is BEHAVIORAL, not elemental or
  tier-keyed.** New `references/keywords.md` section: classify each effect by what it *does* (burst FaF
  bolt / concentration stream / aimed DoT / lingering taper rider / self-buff / magicka-burn or
  discharge rider / cloak tick / hazard tick), then read MR's exemplar for *that archetype* and copy its
  exact keyword+flag set. Same element and same tier, different behavior = different signature. Includes
  a live-verified shock archetype table with the derivation caveats found while deriving it.
- **`requiem-magic-patching` — "school + element keyword + tier marker" named as necessary but NOT
  sufficient.** That trio is what a competent vanilla-style mod ships anyway; an MGEF carrying it while
  lacking the REQ behavioral keywords its archetype comparable carries is **unpatched**, not "already
  correct". Worked negative mined live: Apocalypse's `WB_Des_Fire3_Effect_Bolide
  028F67:Apocalypse - Magic of Skyrim.esp` carries `MagicDamageFire` + `MinimumSkillLevel 50` and **no**
  REQ keyword, where its archetype comparable `REQ_Effect_Destruction3_Fire_AimedExp 01CEA1:Skyrim.esm`
  (same tier) carries `REQ_NoDurationScaling`.
- **`requiem-magic-patching` — the flat rule is explicitly refuted.** "Damage → `REQ_NoDurationScaling`,
  concentration → `REQ_SpellConcentration`" fails in both directions live: a lingering **taper** rider
  carries *both* keywords, while a **magicka-burn/discharge rider** and a **hazard tick** carry **none**.
- **`requiem-magic-patching` — three caveats that make re-deriving per record mandatory** (all mined
  while verifying the table, none of them assumable): **DoT and taper are two archetypes** (aimed DoT
  carries only `REQ_NoDurationScaling`; the GM taper rider adds `REQ_SpellConcentration` + `Recover` +
  `HideInUI`); **flags do not mirror across elements** (the *shock* hazard tick omits `NoDeathDispel`,
  the *fire* one carries it **plus** `NoRecast`); **flags vary within an archetype by tier**
  (`Destruction2_Shock_Aimed` has no `FXPersist`, `Destruction4_Shock_Aimed` does, on identical keywords).
- **`requiem-magic-patching` — behavioral derivation promoted into SKILL.md step 5** ("Set keywords and
  flags") as a three-step classify → read-archetype-exemplar → diff procedure, rather than the previous
  flat list of markers to copy.
- **`requiem-magic-patching` — bulk-pass anti-pattern + Checklist tightened.** "Already correct" is now
  a disposition that requires a diff, not an eyeball: skipping a modded MGEF means naming its archetype
  comparable and showing the keyword sets match; an effect missing a REQ behavioral keyword belongs on
  the *patched* side of the reconciliation count, never the skipped side. Also flags the `depth=1` trap
  — `Keywords` prints as `[list: N item(s)]`, so the count looks like data while hiding the gap.
- **`requiem-magic-patching` — explicit master suffixes on the behavioral keywords.** All four re-verified
  live and now written with their masters everywhere they appear: `REQ_SpellConcentration
  2FFEAD:Requiem.esp`, `REQ_NoDurationScaling 412EDF:Requiem.esp`, `REQ_NoMagnitudeScaling
  3FCA4C:Requiem.esp`, `Nox_KW_CloakDamage 007609:Requiem - Magic Redone.esp`. These sat as bare FormIDs
  beside `Skyrim.esm` element keywords, which is how a run guessed `:Skyrim.esm` and failed the write.
  A full resolve-audit of every other FormID in `keywords.md` found **no wrong master suffixes** — the
  defect was bare FormIDs in mixed-master company, not incorrect ones. `Nox_KW_CloakDamage` also
  reclassified from "Nox-runtime" to **record-side** (it is part of the cloak-tick signature).

## 1.5.0 — 2026-07-17

Field-report round 3 (Heisen, 2026-07-17): the live Val Serano NPC/race patch re-run surfaced
five loopholes that survive 1.3.0's doctrine — each traced on the reported records and closed with
a live-mined rule (evidence: `dev/corpus-2026-07-17-field-report-round3/`). Most of the report's
other items (DNAM never patched, empty-kit skips, civilian misclassification) were already fixed in
1.3.0; the failing session ran on a pre-1.3.0 live install (synced only at 1.3.0's landing minute).
Body + references only — no descriptions changed, no §6.5 re-measure owed.

- **`requiem-npc-patching` — a populated source kit is not adequacy.** The 1.3.0 empty-kit rule
  generalized: the source kit — empty *or* populated — never caps the derivation; the perk/spell
  verdict on every combatant is a comparison against the analogue's kit (bandit 5/9/12, guard ~17,
  vampire tier-2 30), vanilla source perks replaced by it, modded ones augmented. (The report's
  "skips adjusting perks on NPCs that already have a handful of mediocre perks when none are null.")
- **`requiem-npc-patching` — template-skip requires walking the chain.** Workflow A's
  already-templated skip is valid only when the chain ends in a Requiem-balanced or this-pass-patched
  base — a soul/simulacrum copy templating its own mod's `PCLevelMult` original inherits unpatched
  balance (the report's wholly-skipped `…Soul` actor). Drain-check keepers now name the
  `Stats`-templated case explicitly (inert own `LevelMult`, chain walked, never flags alone).
- **`requiem-npc-patching` — mounts/pets/livestock lane.** A mount never enters the combat/boss
  ladder regardless of `Unique`/`Summonable`/name: ordinary saddled horse = fixed level 4, H 289,
  AutoCalc ON (mined live); `Shadowmere` (50, H 1637, `REQ_Trait_Healing_*`) is the only
  supernatural-steed precedent. (The report's level-50 riding horse.)
- **`requiem-npc-patching` — alternate-state copies + the ghost state input.** Soul / simulacrum /
  ghost / flashback duplicates of a named actor: patch the primary, template the copy onto it
  (`Stats, SpellList` — vanilla's own Soul Cairn pattern); a spectral actor carries
  `ActorTypeGhost 0D205E:Skyrim.esm` on the NPC record (236 vanilla carriers verified) so the
  Reqtificator's state-trait pass assigns `RFTI_Trait_Ghost 031284` at build — the stale
  `perks.md` keyword citation fixed, hand-stamping named as the anti-pattern. Recognized-race
  creatures clarified: "almost nothing" is still a pass (fixed level + offsets — the report's
  skipped chaurus hatchling); mod-theme (a pirate crew) named as a combat classification signal.
- **`requiem-race-patching` — state-variant races.** A ghostly/spectral/skeletal reskin race
  classifies to its base creature's analogue; the spectral state is delivered at the NPC layer via
  `ActorTypeGhost` (named in the Layer-B handoff), not on the RACE record. A field-identical variant
  may skip on the field comparison, but the disposition still names the analogue and routes the
  state keyword. (The report's skipped "Ghostly Fox" race.)

## 1.4.0 — 2026-07-17

Two doctrine fixes found by the **1.3.0 empirical close** — the first live re-run of the pack's own
output (the Val Serano patch, authored under 1.2.3) against the 1.3.0 rules. Body + references only —
no descriptions changed, no §6.5 re-measure owed. Both rules were verified against the live source
rather than the handoff that reported them; both reports turned out to be *partly* wrong, and the
corrections are the interesting part.

- **`requiem-magic-patching` — creature-innate magic is its own lane, off the ladder
  ([#21](https://github.com/Avick3110/authoria-requiem-skills/issues/21)).** Step 2's classification
  put every SPEL "the player/NPC casts" in one branch, and every archetype representative is a player
  spell — so a creature's breath or bite classified down the spell lane picked up the tier cost ladder
  *and* the rider set. Measured live: `REQ_Destruction4_Shock_Aimed` is 4 effects at BaseCost 330,
  while `REQ_Creature_AtronachStorm_LightningBolt` is **1 effect at 30**, and across the family effect
  counts run **1–4**, `BaseCost` **0–794**, `ManualCostCalc` inconsistent — hand-tuned per creature.
  The family is also **mostly Requiem's, not MR's** (64 of ~72 SPELs touched by `Requiem.esp`, 8 by
  Magic Redone), so it sits outside the 833-record MR census the ladders are mined from — which is how
  it stayed a footnote. New `spell-archetypes.md` → *Creature innates* section (live table + the
  find-the-family query), a step-2 branch, and a caveat on the rider doctrine itself: **riders are a
  scheme-spell convention, and adding them to an innate is not a harmless over-write** — it hands the
  creature a magicka drain and an extra stagger. Unlike the DNAM rule (#19), erring toward the write
  is *not* safe here, which is why this one needed the branch rather than a blanket rule.
  - **The obvious disambiguator was wrong and is now stated as such:** `_NPC`-suffixed spells are
    **not** creature innates — they are NPC-cast copies built exactly like the player original
    (`REQ_Destruction5_AbsorbHealth_Aimed_NPC` = 5 effects / BaseCost 600 / ChargeTime 1.25, the FaF
    T5 ladder exactly; `REQ_Conjuration3_Spirit_Troll_NPC` = 500 / 0.75, Conjuration T3 exactly). "An
    NPC casts it" and "no tome teaches it" both misroute them. The test is whether the magic **is the
    creature**.
- **`requiem-script-patching` — `follower-registration.md` rewritten
  ([#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15)).** The reference claimed the
  Authoria bridge "already iterates its aliases, so adding an alias is enough — no new script code is
  needed." Reading the shipped `_Authoria_REQ_MajorFollowerBridge.psc` confirms the opposite: it
  declares **ten fixed named properties** and calls `RegisterOne(InigoFollower, "Inigo")` … ten times,
  hardcoded — **no array, no loop**. An eleventh alias is a no-op; extending the bridge means editing
  and **recompiling a third-party mod's script**. The **standalone bridge quest** (own quest + alias +
  compiled script + `.seq`) is now the primary prescription, with the bridge kept as the shape to copy
  (`OnInit` → single `OnUpdate` → bounded retry, `False` return non-fatal).
  - **Plus the consent gate the live run demanded:** registration is **a decision, not a default**. A
    follower shipping its own `*Controller` framework is a user call — Requiem's follower factions are
    a registration prerequisite and can cut across the mod's own recruit flow. Precedent: a fully-built
    registration (factions + quest + script + `.seq`) was **removed in full** at the user's request
    because the mod already owned recruitment. Nothing built was wrong; the default was. Removal is
    all-or-nothing — the factions come back off the NPC too, or the actor sits in Requiem's follower
    factions with nothing tracking it.

## 1.3.0 — 2026-07-17

Field-report round 2 (Heisen, 2026-07-17): per-record *derivation* depth across NPC, magic, and
consumables, every new rule backed by a live-mined reference (evidence trail:
`dev/corpus-2026-07-17-field-report-round2/`). Body + references only — no descriptions changed,
no §6.5 re-measure owed.

- **`requiem-npc-patching` — the DNAM block is now first-class.** The NPC record's base attributes
  (`PlayerSkills.Health/Magicka/Stamina` — distinct from the ACBS offsets), the 18 `SkillValues`,
  and `SkillOffsets` were absent from the field standard, so patched NPCs shipped with vanilla
  DNAM (the report's "base stats and skill offsets never patched"). Mined live: Requiem hand-sets
  all three on every archetype, escalating with tier, **even on AutoCalc-ON actors**; SkillOffsets
  are the per-level scaling knob on named actors (11 verified). New doctrine + recipes + a third
  coverage sweep (the stat/kit matrix) + checklist items; the wrong "AutoCalc ON for humanoids /
  don't hand-stamp PlayerSkills" claims corrected (bandit `_Base` and warlocks are OFF — read the
  analogue's flag).
- **`requiem-npc-patching` — empty kits are not a skip signal.** Requiem's own low-tier sources
  ship zero perks *and* zero `ActorEffect` (bandit `_Base`, `EncWolf`); a modded combatant with no
  perks/spells still gets the analogue's full kit — the analogue, never the patched record, is the
  derivation source. Stated in SKILL.md, `perks.md`, and the recipes; enemy perk lists identified
  as Requiem's player perk tree (vanilla FormIDs, `REQ_*` EditorIDs — tier = tree depth).
- **`requiem-npc-patching` — classification: a skip needs positive evidence.** The recurring field
  failure is combatants misclassified as civilians. Any combat signal now overrides a
  civilian-looking class; skip requires all skip signals agreeing on *that* record; ambiguity →
  patch the combat-consistent minimum. Non-combatants split into two mined lanes: generic
  civilians (Requiem ships no override — skip) vs named civilians with wrong numbers (Requiem's
  Belethor/Sven/Faendal precedent: a stat-only pass — fixed low level, skills 5–20, DNAM — no kit).
- **`requiem-npc-patching` — new `references/npc-authority.md`.** The authority enumerated live:
  all seven Requiem-lane plugins touching NPC_ records with counts and per-lane analogue guidance
  (vampires → VampireCollection, forsworn → Minor Arcana, summons → Magic Redone, named forwards →
  USMP-Requiem), plus the tool-output warning for the Reqtificator-lane winners.
- **`requiem-magic-patching` — spells: cost-only patching killed.** `ManualCostCalc` measured
  99.3% universal and named a WRITE (tick it as a union or the authored cost is ignored);
  ChargeTime rebalance ladder (conc 0 / FaF 0.25×tier / touch half / rituals 3.0) and delivery
  cost multipliers (Rune ≈2×, AoE ≈2×, ritual ceiling; Conjuration prices by summon) mined and
  documented; **the rider doctrine** — patching means ADDING the comparable's complementary
  secondary effects (Fire→Cremation, Frost→Slow+DeepFreeze, Shock→Electrostatic, Venom DoT,
  Impact Stagger, Respite, Ward_Shield, perk-gated `_Improved`/`_Potent`, Illusion Break1/Break2)
  — full FormID table in `cost-and-magnitude.md`; the 833-spell subclass census (tier band 0–5,
  MR-new archetypes flagged) and the `_LeftHand`/`_RightHand` pair rule in `spell-archetypes.md`;
  tome values corrected to per-role bands within each tier.
- **`requiem-magic-patching` — new `references/resistance-map.md`.** All 8 vanilla resist MGEFs
  are `REQ_NULL`ed — a modded resistance spell/enchant/potion pointing at them is a silent no-op
  (the report's "resistance spells completely skipped"). The live replacement map per element per
  lane (ability `REQ_AbHide_Fortify*` / enchant `REQ_Ench_Resist*` / potion `REQ_Alch_Resist*` /
  castable spells / physical natural-armor traits), the AbShow display-only trap, the
  `REQ_DEPRECATED_*` no-prefix batch, and the resolve_names NULL sweep now in the bulk pass,
  workflow, checklist, and recipes.
- **`requiem-magic-patching` — MGEF: tier + conditions doctrine.** `MinimumSkillLevel` identified
  as the tier marker on effects (0/25/50/75/100); MGEF `BaseCost` semantics corrected (autocalc
  weight, not spell cost); per-archetype flags table (`PowerAffectsDuration` on timed buffs,
  `NoMagnitude` binary states, `DispelWithKeywords`) and the Association-doubling rule in
  `keywords.md`; new `references/mgef-conditions.md` with the four mined condition patterns
  (creature-type gate, OR-undead triad, perk-gate + `== 0` exclusions, resistance-threshold rider).
- **`requiem-magic-patching` — ENCH: the gear-value coupling corrected.** Requiem's pre-enchanted
  items keep the **base item's** gold value (enchant adds zero — all ENCH `NoAutoCalc`); the tier
  lives in magnitude / `EnchantmentCost` (= Amount = Magnitude, 1:1 on weapon enchants) / the
  WEAP charge pool (≈ tier×500). Staff per-tier cost/charge/amplified-magnitude ladder and scroll
  conventions (`VendorItemScroll`, Weight 0.5, tier value bands) documented.
- **Manifests brought back to `claude plugin validate` clean** — the current CLI rejects
  `$schema`/`displayName` in plugin.json and a root `description` in marketplace.json (all
  pre-existing); dropped the first two, moved the marketplace description under `metadata`.
  Both manifests validate again.
- **Val Serano follow-ups closed en route** (`dev/BACKLOG.md` F-VS2/F-VS3): the forwarded-`REQ_NULL`
  scan added to the npc bulk pass (sweep 4) and the magic NULL sweep (above); the router's First
  step now states per-plugin `group_by=type` counts overlap across compat patches — the lane's own
  enumeration is the reconciliation denominator.
- **`requiem-consumable-patching` — live re-verify (29/30 claims confirmed byte-exact).** Fixed the
  one wrong FormID (`VendorItemFood` is `08CDEA`, not `0A0E55` = GiftUniversallyValuable); added
  the missed conventions: FLOR harvest-link sweep in the bulk pass (FaB overrides 52 Flora), thrown
  powders as AR SCRL delivery (routes to magic), FaB's own race-side cuisine records noted, the
  COBJ racial-OR gate demoted to potion-class-only (read the comparable's conditions).

## 1.2.3 — 2026-07-16

- **`requiem-script-patching` — VMAD sweep (a) collapsed to one call.** houseCARL's `where=`
  presence tests (`exists` / `missing`) now match struct fields (houseCARL issue #197, closed),
  so the bulk-pass per-type fan-out (MGEF → BOOK → ACTI → FURN → QUST, one query each) is
  replaced by a single cross-type `where=["VirtualMachineAdapter exists"]` query, with hits
  expanded via `housecarl_batch_record_detail depth=3` for disposition. This also closes the
  residual gap the per-type list carried — a host type nobody thought to sweep could hide a
  script; the one-call sweep covers every record type by construction. Re-probed live before the
  edit (struct presence match + `plugins=`-scoped cross-type call both confirmed). Body-only —
  no description changed, no §6.5 re-measure owed. Closes the standing `dev/BACKLOG.md` entry.

## 1.2.2 — 2026-07-15

The S4 findings batch: all eight skill-body findings from the Gray Cowl empirical close
(`dev/s4-graycowl-validation/FINDINGS.md`, F1–F8) fixed in one pass. Body-only — no descriptions
changed, no §6.5 re-measure owed.

- **F1 — summoned actors get an owner (`requiem-npc-patching`).** Conjured/summoned actors were a
  skip category ("balanced via the summon spell"); the spell only *prices* the summon, so two of
  three summonables shipped on `PCLevelMult` in the S4 run. The rule is now: the summon spell routes
  to `requiem-magic-patching`, but the summoned `NPC_` record itself is a combatant — fixed level +
  stats here. First step, Judgment, checklist, and `references/identification.md` all updated.
- **F2 — `NonPlayable` combat gear is tiered, not skipped (`requiem-weapon-patching`,
  `requiem-armor-patching`).** NPC-hand weapon copies and worn NonPlayable armor were skipped as
  "never reaches the player" — but the player *feels* them (the S4 oracle re-tiered every one).
  Skip taxonomies rewritten: a NonPlayable combat copy derives at its playable twin's / wielder's
  tier (a NonPlayable bow still needs `NPCsUseAmmo`); only the player-economy surface (recipes,
  value) is exempt. True skips (traps/props, template/no-slot records) stay skips.
- **F3 — unzoned dungeon cells get zones (`requiem-leveled-list-patching`).** The Reqtificator's
  `openEncounterZones` opens *existing* zones; it cannot invent one, and no sweep surfaced cell→zone
  assignment (the S4 oracle created 3 tiered ECZNs and assigned 18 unzoned cells). The bulk pass
  gains a cell→zone sweep: enumerate the mod's cells' `EncounterZone` links; unzoned dungeon cells
  are assigned an existing tier-matched zone or newly-created tiered ECZNs. Judgment and checklist
  updated to "leave the existing ones; create the missing ones."
- **F4 — recipes track the product's material (`requiem-weapon-patching`).** 6/8 S4 temper recipes
  defaulted to the Steel ingot + Craftsmanship gate. Workflow step 6 now derives the ingot and
  `HasPerk` gate from the weapon's own material keyword via the same-material comparable; Common
  mistakes and the checklist name the anti-pattern.
- **F5 — flags are unions, pack-wide.** A literal `Flags` Set cleared unlisted bits in the S4 run
  (`ManualCostCalc` off all six authored-cost spells; `Female`/`Essential`/`Unique` off named NPCs).
  Every flag-writing skill's bulk protocol + checklist now carries the rule — read the winner's
  bits, write original-bits + your change — with the domain's classic casualties named (npc, weapon,
  armor, ammo, magic, consumable, race, leveled-list; the two example `Flags` writes in npc/race are
  annotated as unions). The houseCARL-side ask (flag-bit Add/Remove verbs) is filed as an HCBR gap.
- **F6 — perk derivation is source-scoped, full stop (`requiem-perk-assignment`).** Prefix-filtering
  a live winner list is no longer a sanctioned substitute for reading the comparable's
  `plugin="Requiem.esp"`/defining-addon perk list — the build pass re-carries tier perks that pass
  every prefix filter (S4 shipped 11–18-perk supersets). The filter is demoted to a last-resort
  cross-check that ships flagged, not silent. First step, Common mistakes, and checklist updated.
- **F7 — the resolve gate (`requiem-npc-patching`, `requiem-perk-assignment`, router).** Two S4
  FormIDs shipped dangling — one the right FormID under the wrong master suffix. Checklists now
  require `housecarl_resolve` on every carried FormID before write; the router's checklist gates on
  it across lanes. (The depth-2 PerkPlacement opacity that invited the slip is filed as an HCBR
  note.)
- **F8 — router-table rows (`requiem-patching` + `references/routing-table.md`).** (a) `ARMA` gets
  an explicit split rule — ARMO-linked addons disposition with their piece in the armor lane, race
  skin/body ARMA in the race lane, every ARMA assigned to exactly one lane up front (the armor
  skill's boundary note reconciled to match). (b) Quest-start level gating (`SMQN` / quest
  `GetLevel` start conditions) gets an explicit **flag-to-the-user** row — quest pacing in a
  de-levelled world is a judgment call, never auto-derived or silently kept. (c) The mod's own
  `CLAS`/`CSTY`/`OTFT`/`FACT` records get an owner — `requiem-npc-patching`'s bulk pass dispositions
  them (retarget the actor to the Requiem analogue, or rebalance a still-referenced custom record by
  live analogy). (d) Standing-stone-like ability SPELs co-route `standing-stones.md` (their
  magnitudes follow the stone model, not a combat tier ladder), stated in the gap table and the
  magic skill's classify step.

## 1.2.1 — 2026-07-15

Papercut fix: the router's missing LVSP row (the unfinished half of the 1.0.3 charter's D3 fold-in).

- **Router (`requiem-patching`) gains the `LVSP` routing row** (SKILL.md table + `references/routing-table.md`) → `requiem-leveled-list-patching`, which has owned LeveledSpell since 1.0.3 (scope, workflow branch, bulk sweep, checklist). Until now an enumerated LVSP fell to the four-way catch-all and was flagged "no owner" — surfaced, never dropped, but the wrong disposition when an owner exists. The row carries the no-merge-toggle note (only LVLI/LVLN are Reqtificator-merged; LVSP resolves by plain conflict winner → hand de-level) and routes spell *design* to `requiem-magic-patching`. Body-only — no descriptions changed, no re-measure owed.

## 1.2.0 — 2026-07-15

New domain skill (S3 of the 1.0.3 coverage-audit charter, decision D1): perk assignment gets a first-class owner, closing the audit's "PERK is a pack-level hole" finding.

- **New skill: `requiem-perk-assignment`** — derives which existing Requiem perks an NPC should carry from its **equipment, spell kit, and tier** against Requiem's own template ladders, and dispositions a mod's own PERK records. It never authors a PERK record. Corpus mined live via houseCARL (4-way fan-out: 84 `REQ_Bandit_Template_*` weapon ladders + the 115-record Warlock caster spine + 43 Requiem classes + the 599-PERK Requiem space enumerated; ~90 actors/perks full-read). The doctrine anchors, all query-verified: **the three-lane boundary** (source-carried player-tree perks are the only writable lane; the Reqtificator's ~45-perk mechanics chassis is stamped at build — measured source 5 → build 50 on a Requiem bandit — and the `RFTI_Player_*` controllers appear on zero source NPCs); **assign origin FormIDs** (Requiem overrides the vanilla trees in place — vanilla Armsman `0BABE4` resolves live to WAR's Weapon Mastery); **rank chains are separate single-rank FormIDs**, carried as one PerkPlacement per rank at Rank 1; the **warrior formula** (weapon line + defence line keyed on shield/armor class + universal floor, additively deeper by tier), the **caster formula** (school nodes at the threshold of the actor's highest spell tier + the shared survivability set; NPCs never carry the player-only `_Mastery_` gates), hybrids stack both, creatures carry race×tier supersets or trait-only kits; and the **class caution made operational** (a CLAS record has no perk field; equipment out-resolves class; class is placeholder noise on 4,229 templated records). Mod-shipped PERKs get the author-approved three-way disposition: leave runtime plumbing alone, fold only an obvious duplicate of a Requiem gate, otherwise flag to the user with a concrete question. Bulk pass protocol (PERK queue + the NPC perk lane riding `requiem-npc-patching`'s enumeration) baked in from birth. Description validated per §6.5 fan-out; results archived in `evals/`.
- **Router (`requiem-patching`) hands PERK to the new skill.** The `PERK` routing row (SKILL.md + `references/routing-table.md`) now routes to `requiem-perk-assignment` (previously "no domain skill owns standalone PERK rebalancing — flag the remainder"); `references/perks-skills.md`'s mod-perk section points at the skill's disposition rule; `requiem-npc-patching` hands off perk-set *derivation* (no clean analogue, hybrids, dedicated perk jobs) while keeping the whole-NPC frame and the copy-the-analogue path. Body-only sibling edits — no sibling descriptions changed, so no re-measure owed for them.
- Rosters updated (README table + `skills/README.md`; CLAUDE.md skill count), plugin manifest bumped to 1.2.0.

## 1.1.0 — 2026-07-15

New domain skill (S2 of the 1.0.3 coverage-audit charter, decision D2): consumables get a first-class owner instead of the router's "flag the remainder to the user" punt.

- **New skill: `requiem-consumable-patching`** — potions, poisons, oils, food, drink/alcohol, drugs, and ingredients (ALCH + INGR + their COBJ recipes), derived by live-analogy against `Requiem - Alchemy Redone.esp` and `Requiem - Food and Beverages Redone.esp` as refined by the load order's hand-authored patch layers. Corpus mined live via houseCARL (all 287 AR ALCH + 183 INGR + 196 FaB ALCH touches enumerated; ~200 records read in full). The doctrine anchor, verified by query: **the Reqtificator never touches consumables** (`Requiem for the Indifferent.esp` defines only NPC/LeveledNpc and touches zero ALCH/INGR/COBJ) — a consumable override is the final balanced record, with no build-time safety net. The skill carries: the five class kits and their opposite techniques (potions **keep** effect links to Requiem-overridden MGEFs and rebalance the shell; food gets its effect list **rebuilt** to the FaB kit — themed fortifies + exactly one hunger tag + exactly one nutrition tag, raw-meat/fish paralysis dropped on cooking; alcohol = the `REQ_Alcohol_*` pair by strength tier; drugs = potion-flagged with no hunger/nutrition; ingredients = the full AR payload with exactly-4 `REQ_Alch_*` quartets, per-MGEF standardized durations, magnitude as the tier knob, `Value == IngredientValue`); the full potion/poison value-and-magnitude ladders; the cookpot-station COBJ shapes with perk/skill-band gates (yield-tiered, not potency); the racial-cuisine/Green-Pact keyword system; a Bulk pass protocol with per-record dispositions and reconciliation (1.0.3 doctrine from birth); and both boundary handshakes (new effect design ↔ `requiem-magic-patching`; placement ↔ `requiem-leveled-list-patching`). Description validated per §6.5 fan-out: 20/20 (recall 10/10, specificity 10/10), results archived in `evals/results-2026-07-15.json`.
- **Router (`requiem-patching`) hands consumables to the new skill.** The `INGR`/`ALCH` routing row (SKILL.md + `references/routing-table.md`) now routes to `requiem-consumable-patching` (previously `requiem-magic-patching` + gap references with a flag-the-remainder rule); the `alchemy.md`/`food.md` gap references keep the system constraints but point record work at the new skill; the magic skill's alchemy note states the boundary in both directions. Body-only edits — no sibling descriptions changed, so no re-measure owed for them.
- Rosters updated (README table + `skills/README.md`), plugin manifest bumped to 1.1.0.

Pack-wide coverage hardening (field report, DrHeisen 2026-07-15): the 1.0.2 fix was one instance of a class. A whole-mod job could still ship records unpatched three ways — seven of nine skills had no whole-plugin coverage layer at all, the router could clear a job with entire record types silently unrouted, and the one protected skill's reconciliation proved every record was *touched*, not that its balance fields were *carried*. A 9-way audit of every skill against that taxonomy drove these fixes; descriptions are unchanged, so no trigger re-validation is required.

- **Seven domain skills gain a *Bulk pass protocol*** (`requiem-weapon-patching`, `requiem-armor-patching`, `requiem-ammo-patching`, `requiem-magic-patching`, `requiem-leveled-list-patching`, `requiem-race-patching`, `requiem-script-patching`), mirrored as a *Coverage audit* recipe and gated by a whole-plugin checklist line: the type enumeration is the work queue, every FormID gets a per-record disposition (patched, or skipped with a verified reason and named skip categories), the pass closes on a reconciliation count, and each domain's uniform-looking families (armor sets, material-tier lines, rank chains and element triples, sublist trees, race variant pairs, shared-script property families…) are named in an explicit no-extrapolation rule.
- **"Patched" is a field verdict pack-wide.** A record counts as patched only when the skill's per-record field checklist passes for it — field-complete, not merely touched — so a reconciliation can no longer close green over an NPC that got a level but never its perks, spells, or stats.
- **`requiem-npc-patching` — the field-carry gaps behind the recurring misses.** `PlayerSkills` (per-skill values/offsets — the repeatedly-missed "skill offsets") is now named in the doctrine, the checklist, and `references/npc-fields.md` (AutoCalc-ON humanoids derive them; AutoCalc-OFF creatures/bosses carry the analogue's); casters must carry the analogue's actual castable spell list, not just the tempering trait; the de-levelling work list gains a drain post-condition (re-run query 1 — it must return empty or followers-only); the no-extrapolation rule now also names shared-`Template` and shared-`Class` families.
- **`requiem-magic-patching` — effects and tomes can no longer ride shotgun.** MGEFs are enumerated first-class (never reached only via the spells that cast them) with a closing cross-check — every effect a patched spell casts must itself be dispositioned; ENCH object effects, scrolls, and spell tomes each get their own sweep; and the tome is a per-spell must-carry — every patched SPEL has its teaching BOOK reverse-resolved and priced to the tier ladder.
- **`requiem-leveled-list-patching` — the de-levelling inverse job existed nowhere; now it does.** A mod's *own* leveled lists escape the Reqtificator merge (Requiem never defined them → `baseVersion == null`) and ship verbatim, level-gates and all — the skill now says so at the merge doctrine, the encounter-zone judgment, and a de-levelling triage that hand-flattens every `Level > 1` entry on the mod's own lists while keeping Requiem-defined overrides add-only. `LeveledSpell` (LVSP) joins the skill's scope (design stays with the magic skill; LVSP also has no merge toggle, making the hand de-level the only lever).
- **`requiem-weapon-patching` — the enchanted-weapon ownership handshake.** A modded `ObjectEffect` is a new enchantment that itself needs Requiem balancing — the skill now routes its ENCH/MGEF design to `requiem-magic-patching` at the workflow branch, in Common mistakes, and on the checklist; the weapon-side frame alone never counts as balanced.
- **`requiem-patching` (router) — no type is silently dropped.** Routing rows added for `INGR`/`ALCH` (ingestibles), `SHOU`/`WOOP`, and `PERK` (stated plainly: no domain skill owns standalone PERK rebalancing — disposition each record and flag the remainder to the user); a four-way catch-all disposition rule (routed / gap-reference / cosmetic-skip with reason / flagged no-owner) in both the skill body and `references/routing-table.md`, with honest rows for FLST/KYWD/QUST/CELL/GMST and friends; the per-record coverage rule now applies to **every** type (the closed "high-count types" list is gone); triage classifies rows instead of dropping them; and the job closes on a top-level reconciliation across all types — it cannot clear while any enumerated record lacks a disposition.
- **Housekeeping.** The new sections are indexed in every skill's `references/index.jsonl` (the 1.0.2 NPC sections are now indexed too), and three "Aaron"-by-name mentions in shipped references now read "the author", matching the rest of the corpus.

## 1.0.2 — 2026-07-14

Bugfix (HCBR-2026-07-14-03): a whole-plugin NPC pass gated coverage at record-**type** granularity, so per-record misses shipped silently.

- **`requiem-npc-patching` — new *Bulk pass protocol* section.** A Gray Cowl of Nocturnal rebalance shipped with 7 of 144 NPCs unpatched: the pass read representative members of same-prefix EditorID families and extrapolated, so the non-uniform outliers (PC-scaling vendors, an over-tier summon, a level-10 Thalmor pair, a level-1 quest attacker) — the exact records a rebalance exists to catch — were never dispositioned. The skill now opens whole-plugin jobs with two one-call coverage sweeps: the PC-mult **de-levelling work list** (`where=["Configuration.Level.LevelMult >= 0"]` — a scalar compare on the union arm doubles as an arm-presence test, returning exactly the actors still on a multiplier) and a full-type **triage matrix**. The enumeration is the work queue: every FormID gets a disposition (patched, or skipped with a reason verified per record), the pass closes on a reconciliation count (patched + skipped = enumerated), and extrapolating across same-prefix EditorIDs is prohibited. The copy-ready sweep is mirrored in `references/housecarl-recipes.md` and a whole-plugin line was added to the skill's checklist.
- **`requiem-patching` — coverage gate tightened from per-type to per-record.** `references/integration-checklist.md` item 1 and the skill's own checklist bullet no longer clear a high-count type (`NPC_`, `ARMO`, `BOOK`, `CONT`, `LVLI`, …) on a nonzero patch count; they require every record dispositioned with counts that reconcile, and cross-reference the NPC skill's *Bulk pass protocol*.
- **No houseCARL defect.** The coverage primitive was confirmed working live on `Gray Fox Cowl.esm`; the report's optional houseCARL ergonomic (documenting the union-arm `where=` presence test) is tracked separately in that repo. Skill descriptions are unchanged, so no trigger re-validation is required.

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
- A few live-verification items: a live VMAD-write round-trip, the `housecarl_compile_script` path, the disease payload MGEF winner, the standing-stone activation quest trace, and the (currently inactive) follower-registration ESP.
