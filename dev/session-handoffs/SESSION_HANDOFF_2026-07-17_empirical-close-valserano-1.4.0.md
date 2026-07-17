# Session handoff — 2026-07-17: the 1.3.0 empirical close (Val Serano re-audit) → 1.4.0

*Author: Aaron (+ Claude session) · 2026-07-17*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-17_field-report-round2-1.3.0-PR.md` (Heisen's lane) as the latest — same
day, and it is the direct follow-on: that handoff's "empirical close candidate" is what this session
did.*

## What this session was

Heisen's 1.3.0 handoff left one item open: *"re-run one of the failing NPC/magic cases under 1.3.0
and check the new checklist items reconcile — the standing 'a plan reviewed is not a thing proven'
principle. Not done this session."*

Done now, against the ideal specimen: **the Val Serano patch, authored under 1.2.3, sitting live on
Aaron's instance** — a patch built before the doctrine that was supposed to catch its failures.
Synced 1.3.0 first (live install was stale at 1.2.3).

**Verdict: 1.3.0's magic corrections are validated — the rider doctrine caught a real,
play-affecting gap 1.2.3 missed entirely. The NPC DNAM correction is sound as a rule, but this
patch's exposure to it was a single record, which passed.** Two *new* skill gaps surfaced and are
fixed in 1.4.0 (this PR).

## The method lesson (the most transferable part)

**The audit's dramatic findings mostly dissolved under verification; the one real fix was in a lane
first assumed fine.** First-pass reading of the raw fields produced a damning list — mercenaries at
fixed `Level = 1` carrying `Health = 65533` / `Magicka = 65511`, Xivkyn still on `PcLevelMult`,
vanilla classes on named actors. **Every one was a false positive:**

- The `Level 1` + garbage-DNAM records are **Stats-templated** — the 1.2.3 lane had *added* the
  `Stats` template flag (the source records lacked it) so they inherit `REQ_Bandit_Generic_1H` →
  `LCharBanditMelee1H`, Requiem's own bandit ladder. Inert, and the doctrine's own prescribed
  integration.
- Vanilla classes are **correct** — Requiem keeps `CombatWarrior1H` on Lydia herself.
- Val on `PcLevelMult` is **correct** — followers keep it, per doctrine.

Only `TemplateFlags` and a live comparable told those apart from real misses. **Read the inheritance
before believing a field.** The 1.2.3 lane was considerably sharper than its raw output looks: it got
the template chaining, the class handling, and the whole cost ladder right (its shock spells match
`REQ_Destruction4_Shock_Aimed` byte-for-byte on cost/charge/flag).

Corollary, twice over: **both field reports this session were partly wrong, and verifying against the
live source rather than the report is what produced the fix.** See the two judgment calls below.

## What shipped (branch `claude/skill-fixes`, version 1.4.0)

Per-item detail: the 1.4.0 CHANGELOG entry. Headlines:

- **[#21](https://github.com/Avick3110/authoria-requiem-skills/issues/21) `requiem-magic-patching`:**
  creature-innate magic (`REQ_Creature_*`) is now its own step-2 branch with a live-mined
  `spell-archetypes.md` section, plus a caveat on the rider doctrine. A creature's breath run down
  the spell lane picked up the player cost ladder *and* the rider set; Requiem's innates carry
  neither (1–4 effects, BaseCost 0–794, `ManualCostCalc` inconsistent, hand-tuned per creature).
  **Not the harmless case** — unlike #19, erring toward the write actively mis-balances.
- **[#15](https://github.com/Avick3110/authoria-requiem-skills/issues/15) `requiem-script-patching`:**
  `follower-registration.md` rewritten around the standalone-bridge pattern, plus the consent gate
  the 2026-07-16 removal demanded (registration is a decision, not a default).

## Judgment calls the maintainers should know

1. **[#19](https://github.com/Avick3110/authoria-requiem-skills/issues/19) was filed, then withdrawn
   on the maintainers' ruling — correctly.** The proposal was to carve `Stats`-templated records out
   of 1.3.0's unconditional DNAM rule. Aaron + Heisen ruled the blanket rule is **deliberate**: the
   failure it corrects is the *previous* skill ignoring DNAM on **every** NPC regardless, including
   the AutoCalc-OFF ones where the block *is* the stats. A carve-out reintroduces the conditional
   reasoning that caused the original bug; a wasted write is cheap, a missed live record is not.
   **The ruling was then confirmed empirically:** `where=["Configuration.Flags has AutoCalcStats"]`
   over the patch returns **42 of 43** — exactly one record is AutoCalc-OFF, and it is precisely the
   one a carve-out-minded reviewer would wave through. Issue downgraded to *ergonomic*, holding only
   a reviewer-facing note; **fine to close.**
2. **The `_NPC` disambiguator was wrong before it shipped.** #21's first framing was
   "player-castable vs NPC-innate," disambiguated by "does a tome teach it." Checking the live
   records killed it: `_NPC` variants follow the **player** ladder exactly
   (`REQ_Destruction5_AbsorbHealth_Aimed_NPC` = 5 effects / 600 / 1.25). "No tome" would have
   misrouted every one. The shipped branch tests whether the magic **is the creature** instead.
3. **#15's report was right about the symptom, understated about the cause.** The claim was "adding
   an alias is a no-op because the bridge uses fixed named properties." Reading the shipped
   `.psc` confirms it exactly — ten hardcoded properties, ten hardcoded `RegisterOne` calls, no loop
   — so the reference's "no new script code is needed" is precisely backwards: extending the bridge
   means **recompiling a third-party mod's script**. That's why the standalone quest is now primary,
   not merely an alternative.
4. **Version framed 1.4.0**, not 1.3.1 — both changes alter default lane behavior (a new
   classification branch; a consent gate), the same reasoning Heisen used to frame 1.3.0.

## The Val Serano patch (fixed live, not part of this PR)

`AX ValSerano - Requiem Patch.esp` was edited in place on Aaron's instance via houseCARL:

- **Shock riders (the real fix).** Lightning Snare / Stream / Vortex sit at BaseCost 330 /
  ChargeTime 1.0 / `ManualCostCalc` — byte-identical to `REQ_Destruction4_Shock_Aimed`. The 1.2.3
  lane derived the economy onto it perfectly but copied only its primary effect of four. All three
  now carry the T4 rider set off Thunderbolt: `ElectrostaticDischarge4_Magicka` m30,
  `_Stagger` m0.25, `Impact_Stagger` m0.25.
- **Tier markers.** `AX_LightningSnareAimedME` + `AX_LightningVortexAimedME` `MinimumSkillLevel`
  0 → **75** (ladder confirmed live: T2 25 → T3 50 → T4 75).
- **DNAM, 3 of 10.** Arne + Fathis Indaryn ← `MS09Vidrald` (166/80/124 — a Blood Horker pirate
  captain at the identical class *and* level); Khovin ← `Melaran` (114/186/80, identical class).
- Masters 10 → **11** (`Requiem - Magic Redone.esp`, correctly — the riders reference it).
  `check_errors`: 0 dangling / 0 missing masters / 0 unparseable.
- **Aaron still owes the patch a Reqtificator re-run.**

## Open / next

- **The 7 remaining DNAM records** (Arlin, Lielle, Tells-No-Tales, both Fathis tiers, both Serano
  records) are **deliberately unwritten — no comparable exists.** Requiem hand-tunes named-actor DNAM
  with no derivable class+level formula (its ranger Froki at L21 reads 100/50/100 while Nagrub at L6
  reads 152/70/173 — race and story role drive it). Cosmetic anyway: all are AutoCalc-ON. Left as an
  honest gap rather than invented numbers, per the coffee-analogy precedent.
- **`AX_SparkySummon` investigated and cleared** — it is the patch's *only* AutoCalc-OFF record, so
  the one place DNAM is live. Its `Magicka = 65511` looked like the patch's one real bug; it isn't.
  Its entire kit is `AX_ValSparkyAbilitiesSPELL` (`Type=Ability`, `CastType=ConstantEffect`,
  `BaseCost=0`) — no castable spells, so magicka is inert regardless. Its Health/Stamina are a
  coherent flavour-pet design (`DoesNotBleed`, `BleedoutOverride`) with no Requiem pet archetype to
  derive from. Requiem itself ships inflated "never runs out" stats (`EncIceWraith` Stamina 10014,
  `SummonAtronachStormNPCPotent` Stamina 10057), so a large value is not per se a bug.
- **#19 awaiting a close** (see judgment call 1).
- houseCARL: **zero tool bugs across the session** (~45 read calls + 2 bulk writes). `where=` flag
  tests (`Configuration.Flags has AutoCalcStats`) and `composes` for `Effect` structs both behaved.
- BACKLOG untouched (perk-assignment Q8 description tweak; root README authority section;
  original-nine empirical re-validation).
- **The 1.3.0 charter is now closed** — the empirical close was its last open item.
