# Coverage-class audit → 1.0.3 fix plan — 2026-07-15

*Class: ARCHIVE — closed 2026-07-15. All four stages shipped: S1 (1.0.3 coverage hardening, PR #4),
S2 (requiem-consumable-patching 1.1.0, PR #5), S3 (requiem-perk-assignment 1.2.0/1.2.1, PRs #6–7),
S4 (empirical close on Gray Fox Cowl.esm — all charter gates PASS; findings ledger:
`dev/s4-graycowl-validation/FINDINGS.md`; handoff:
`SESSION_HANDOFF_2026-07-15_S4-empirical-close-graycowl-PASS.md`). Original charter:
`dev/session-handoffs/SESSION_HANDOFF_2026-07-15_NEXT-coverage-class-audit-all-skills.md`. Audit ran
as a 9-agent Opus fan-out (one per skill), each judging against the handoff's 6-class taxonomy after
loading `standards/` + the 1.0.2 reference implementation (`01ab413`). Aaron confirmed Heisen's
failing job ran on PRE-1.0.2, so items 4/4.5 are the fixed class — and the NPC audit traced both
through the shipped protocol and confirmed 1.0.2 would have caught them.*

## Audit verdict — one sentence

The 1.0.2 bug was one instance of a pack-wide class: **seven of nine skills have no bulk-pass layer
at all** (single-record voice end to end — no enumeration-as-work-queue, no per-record disposition,
no reconciliation count, no anti-extrapolation rule), the **router can clear with whole types
unrouted** (its skip rule is a cosmetic whitelist, not a catch-all; ALCH/INGR/PERK/SHOU have no
routing rows), and the one protected skill (NPC) is **field-blind**: "patched" is an atomic token,
so the reconciliation closes green over records that got a level but never got perks/spells/stats/
skill offsets — `PlayerSkills` is named nowhere in the skill.

## Per-skill findings (severity-ranked within each; full detail in the agents' transcripts)

### requiem-patching (router)
1. **HIGH — class 5a/5b:** `SKILL.md:69-78` routing table omits ALCH, INGR, PERK, SHOU/WOOP; no
   catch-all disposition rule — `routing-table.md:43-47` skip rule is a cosmetic *whitelist*, so
   unlisted non-cosmetic types (INGR, PERK, FLST, QUST, KYWD, FACT, GMST…) fall out silently.
   = Heisen "skips food, ingredient, potions". Fuller `references/routing-table.md` also misses
   FLST/QUST/CELL/KYWD/FACT/GMST/ACTI/MISC.
2. **HIGH — class 4:** integration checklist has no top-level reconciliation and no
   can't-clear-while-undispositioned gate; a type with no routing row never becomes a row to tick.
3. **HIGH — class 1:** the 1.0.2 per-record tightening names a **closed list** ("high-count types
   (`NPC_`, `ARMO`, `BOOK`, `CONT`, `LVLI`, …)") — SPEL/MGEF/ENCH/WEAP/RACE/ALCH/INGR escape by the
   text's own wording. = Heisen items 9/10/5 at the router tier.
4. **HIGH (blocked on scope decision) — class 5c:** **PERK is a pack-level hole** — no skill owns
   standalone PERK-record rebalancing; `references/perks-skills.md:29-33` punts with no derivation
   method. = Heisen item 6.
5. **MED — class 5d:** ALCH/INGR **record shell** (value/keywords/structure/placement) has no
   first-class owner (magic owns effects, economy.md the value) — diffuse ownership is the skip
   mechanism.
6. **MED — class 2/3:** First step item 3 "Triage" is a judgment filter over the enumeration; the
   denominator isn't preserved through to reconciliation for most types.

### requiem-npc-patching (1.0.2 protocol HOLDS for touch-coverage; class 6 is the open wound)
1. **HIGH — 6A:** `PlayerSkills` (skill values / skill offsets) named **nowhere** (SKILL.md,
   npc-fields.md, perks.md). Matches Heisen's verbatim recurring complaint.
2. **HIGH — 6B:** "patched" disposition is workflow-tagged, not field-gated (`SKILL.md:117-118`);
   field checklist (`:298-322`) is per-single-record and never bound to the enumeration →
   reconciliation certifies touch, not field carry. The structural root of items 1/2.
3. **MED-HIGH — 6C:** de-levelling work list (query 1) has no drain post-condition — re-run query 1
   after the pass; must return only followers/empty.
4. **MED — 6D:** replicate path never says to carry a caster's actual spell list (ActorEffect SPLO).
5. **LOW — class 2:** no-extrapolation rule anchored only on EditorID prefix; shared-Template and
   shared-Class families unnamed.
Classes 1/3/4 CLEAN (for disposition); items 4/4.5 traced → 1.0.2 catches both.

### requiem-magic-patching (heaviest Heisen load: items 5, 8, 9, 10 — all confirmed)
1. **CRIT — classes 1/3:** no whole-plugin mode at all; zero coverage vocabulary (grep-verified).
   Only cross_plugin_query calls enumerate *Requiem*, never the mod. = items 9/10/5.
2. **HIGH — MGEF not first-class (item 10):** MGEFs only reached via spells that reference them; a
   spell counts "patched" (cost/perk) while its own modded effect was never rebalanced. Fix: MGEF
   enumerated as its own denominator + cross-check vs effects referenced by patched spells.
3. **HIGH — class 4:** no reconciliation count.
4. **HIGH/MED — class 2:** most uniform domain (rank chains, Fire/Frost/Shock triples, enchant tiers
   01-06, tome ladders); `worked-examples.md:70-89` actively models extrapolation (Ice Spike
   round-trip) with no caveat; tome ladder invites pricing without reading each BOOK.
5. **MED — item 8:** tome BOOK named in checklist but never **gated per spell** — need "for every
   SPEL patched, reverse-resolve its teaching BOOK and price it" + tome/scroll reconciliation line.
Class 6 essentially clean (real per-field gate at `SKILL.md:273-285`).

### requiem-leveled-list-patching (item 3 confirmed at root)
1. **CRIT — classes 1/3:** skill is placement-only ("place my new record"); the inverse job —
   enumerate the mod's OWN LVLI/LVLN/LVSP and de-level each — does not exist anywhere. = item 3.
2. **HIGH — escape hatch:** merge doctrine (`SKILL.md:21-24`) + ECZN "usually leave them" teach
   "the Reqtificator handles it" — but a mod's own lists **escape the merge** (`baseVersion == null`,
   `merge-behavior.md:38-44`) and ship verbatim, level-gates and all. The skill documents the guard
   but never draws the de-level conclusion.
3. **HIGH — class 4:** no reconciliation.
4. **MED — class 5:** **LVSP unowned pack-wide** (structurally identical to LVLI/LVLN; neither this
   skill, magic, nor NPC claims it).
5. **MED — class 2:** no anti-extrapolation rule (sublist trees, same-prefix families, tier
   variants); latent until the sweep exists.
Class 6 clean for the add path.

### requiem-weapon-patching / requiem-armor-patching / requiem-ammo-patching (no Heisen item;
same structural verdict each)
1. **CRIT/HIGH — classes 1/3/4:** no bulk-pass protocol; workflow opens "classify the <item>"
   (singular); only enumeration queries target Requiem; checklists gate one override; no skip
   taxonomy (weapon: non-playable/trap/unarmed-placeholder; armor: non-playable/skin/naked-body/
   creature-skin; ammo: trap/creature/NonPlayable); no reconciliation.
2. **HIGH/MED — class 2:** each domain's family shapes named per audit — weapon: material tiers,
   type families, prefix lines, enchanted variants (skill *itself* supplies `+1/tier`, `500×tier`
   formulas with no read-each-sibling rule); armor: same-SET pieces (ratio table can substitute for
   reading the mod's actual sibling), light/heavy pairs, tiers, enchanted variants; ammo: tier
   lines, arrow/bolt pairs (`×1.2` rule), elemental variants.
3. **MED (weapon only) — class 5:** modded `ObjectEffect` on an enchanted weapon never explicitly
   routed to magic — each lane can assume the other rebalanced it. = Heisen item 5's weapon side.
   (Armor states this boundary correctly both ways — use its wording as the model. Ammo clean.)
Class 6 CLEAN in all three (real per-record field gates; wire them in as the bulk disposition gate).
Ammo PROJ note (LOW): PROJ rides the AMMO link — acceptable; make PROJ disposition ride each AMMO's
disposition in the bulk pass.

### requiem-race-patching (item 7 confirmed — by absence, not by a bad rule)
1. **CRIT — class 3:** no RACE enumeration; workflow opens at "read the modded race" (singular).
   Gimmick/visual races are excluded by unstated judgment before ever being named.
2. **HIGH — item-7 guard:** no per-record skip rule; humanoid branch has no anti-fallthrough
   closure (creature branch has one). Fix shape: skip must be per-RECORD with the stated reason
   "no balance-bearing field differs from its vanilla/Requiem base" — visual/gimmick/reskin/
   chargen-only is **not** an exemption bin.
3. **HIGH — classes 1/4:** no whole-plugin gate, no reconciliation.
4. **MED — class 2:** frost-troll worked example (H750 vs H300) proves divergence but the lesson
   never became a body rule; playable/vampire pairs, same-prefix variants unguarded.
5. **LOW — class 5:** `<Race>RaceVampire` treated as a rider on the base patch, not its own
   enumerable row.
Class 6 CLEAN (strong field gate `SKILL.md:290-304`).

### requiem-script-patching (decision-tree shape; taxonomy translated)
1. **HIGH — classes 1/3/4/5:** candidate-finder is 100% judgment, no denominator. Router's only
   handoff ("VMAD on any record") can only find records that *already* ship a script — the skill's
   raison d'être is records that need one ADDED. Fix: two sweeps — (a) VMAD-presence enumeration
   across all types (also closes scripted QUST/ACTI/FURN falling between type-routing and the
   catch-all); (b) needs-a-script candidate list from named proxies (magic-flagged special
   mechanics, every BOOK, follower NPC_), each dispositioned incl. "confirmed no-script-needed",
   with reconciliation.
2. **LOW-MED — class 2:** N siblings sharing one Nox script with per-record properties
   (Mark/Park, BoundAbility, CharmFaction) — no rule against verifying one and cloning fills.
3. **LOW — class 6:** property gate is blanket ("every property repointed") not enumerated against
   the .psc's declared Property set (array counts; intentionally-shared vs repointed).

## The fix pattern (applies per domain, adapted — an armor triage matrix is not an NPC one)

Every new "Bulk pass protocol (whole-plugin jobs)" section mirrors `requiem-npc-patching`
`SKILL.md:78-122` and contains:
1. **Enumeration = work queue:** one `housecarl_cross_plugin_query plugins=["<NewMod>.esp"]
   type="<T>"` sweep per owned type, with a domain triage-matrix field list.
2. **Per-record disposition** — patched → which workflow branch, or skipped → named skip-category
   reason, verified on THAT record.
3. **"Patched" = field-gate passed** (the NPC 6B lesson, baked into every domain): a record counts
   patched only when the skill's existing per-record field checklist passes for it. (The field
   checklists are already good in weapon/armor/ammo/race/magic — wire, don't rewrite.)
4. **Anti-extrapolation rule naming the domain's family shapes** (per-domain lists above).
5. **Reconciliation count:** patched + skipped = enumerated, per type.
6. Mirror sweep in `references/housecarl-recipes.md` ("Coverage audit" section, as NPC's) + a
   whole-plugin line in the skill Checklist.

Domain extras: magic MGEF-first-class + spell↔MGEF cross-check + per-spell tome gate; leveled-list
de-level pass + merge-escape-hatch correction (Fix B) + LVSP; NPC PlayerSkills field + field-gated
"patched" + query-1 drain post-condition + caster spell list + template/class extrapolation axes;
weapon ObjectEffect→magic handshake; script two-sweep protocol + enumerated property gate; race
per-record skip rule + vampire-counterpart row; router complete table + catch-all + count-keyed
per-record rule + top-level can't-clear reconciliation.

## Scope decisions — DECIDED by Aaron, 2026-07-15

- **D1 — PERK: build a real perk derivation method — as a NEW SKILL.** Aaron's scope: **NOT
  creating new PERK records** — the method derives which (existing Requiem) perks an NPC should
  carry, from **the equipment they use** and/or **their class** — "careful when assigning by
  class" (Aaron, verbatim). Open sub-questions deferred to that skill's build session: the
  boundary against Reqtificator auto-assignment (the 1.0.1 doctrine — never hand-stamp
  Reqtificator-assigned perks — must shape what this skill may assign), and whether the skill also
  dispositions a mod's OWN shipped PERK records (rebalance vs flag) or leaves those to the router
  catch-all.
- **D2 — ALCH/INGR + ingestibles: fuller fix — as a NEW SKILL** (not a magic-skill extension).
  A consumables domain skill covering ALCH (potions, poisons, food, drink — ingestibles) + INGR,
  deriving by live-analogy against **Alchemy Redone** and **Food and Beverages Redone** (the
  Requiem addons the authoria list runs) as the comparable corpus.
- **D3 — LVSP: fold into `requiem-leveled-list-patching`** (type list + sweep) + router row;
  body-only, descriptions untouched. Rides the fixes session.

Both new skills need the full new-skill discipline: corpus mining against the live load order
(the weapons-pilot method), SKILL.md + references + eval set (§6.2), the §8 checklist, and —
because they are NEW descriptions — the **§6.5 fan-out trigger validation is mandatory**, not
optional. Router rows for ALCH/INGR/PERK land with their owning skill's session (until then, the
fixes-session router catch-all dispositions them as "flag to user — owner skill in build").

## Session plan (multi-session charter — Aaron's split, agreed 2026-07-15)

- **S1 — the 1.0.3 fix batch** — **EXECUTED 2026-07-15 (audit session), PR #4 open awaiting Aaron's
  go.** All per-skill audit fixes + router hardening + NPC class-6 + D3 LVSP fold-in + "Aaron"-naming
  ride-along landed on `claude/coverage-1.0.3`. All body-only → no re-measure. validate --strict
  passes (plugin + marketplace); all bodies ≤500 lines. Extra beyond charter: new sections indexed in
  every `references/index.jsonl` (the 1.0.2 NPC sections were never indexed — now are).
  **CLOSED 2026-07-15: PR #4 MERGED (rebase) on Aaron's go — main tip `278c061` (was 502c907/2e4a17e/
  4b3fb7f pre-rebase); worktree + branch removed; live install robocopy-synced and verified at 1.0.3.**
- **S2 — consumables skill** (D2): corpus-mine Alchemy Redone + Food and Beverages Redone live via
  houseCARL; author `<kebab-name>` skill; §6.5 fan-out; router rows + README roster + version bump.
- **S3 — perk-assignment skill** (D1): equipment→perk and class→perk derivation doctrine (class
  with caution); Reqtificator-boundary question settled first; §6.5 fan-out; router row + roster +
  version bump.
- **S4 — integration + empirical close:** re-run one of Heisen's failing cases (or the Gray Cowl
  pass) under the full fixed pack; reconciliation counts must close field-gated; release notes;
  live-install sync verified. A plan reviewed is not a thing proven.

Each authoring session: worktree; invoke `anthropic-skills:skill-creator` at session start (§8
prerequisite); SKILL.md ≤500 lines (§4.1); imperative + why (§4.3);
`claude plugin validate --strict` before PR.
