# Session handoff — description compression + full §6.5 revalidation (1.10.0, #43)

*Author: Aaron (+ Claude session) · 2026-07-29*

*Class: ARCHIVE (frozen at close). Charter: `dev/archive/1.x-plans/2026-07-29-description-compression-43.md`.*

## What shipped

**1.10.0** — addresses [#43](https://github.com/Avick3110/authoria-requiem-skills/issues/43) (held
open pending the post-merge `/doctor` measurement — review issue 1: with houseCARL and the other
sources counted, the combined listing plausibly stays over budget even after this PR; a second ~25%
compression round on both packs is the named lever if the number comes back over). The 11
descriptions cost 8,292 chars (~2,073 est. tokens), alone consuming Claude Code's ~2,000-token budget
for the entire skill listing. Mirrors houseCARL [PR #295](https://github.com/Avick3110/houseCARL/pull/295)
(issue #294), which Aaron ran the day before and which amended the standard upstream.

1. **Standards re-sync** — `HOUSECARL_SKILL_AUTHORING.md` from houseCARL HEAD (the #294 amendment:
   §3.5 → 200–400 chars keyed to the listing budget; §3.3 → compact tail only for early-load hazards;
   §8 items 5/7). NAMING and DOC_HYGIENE verified byte-identical upstream (EOL aside).
2. **All 11 descriptions rewritten**: 8,292 → 4,266 chars (~1,066 est. tokens, avg 388). Distinctive
   record-type vocabulary kept; synonym chains and capability inventories cut. The BACKLOG
   perk-assignment gate-clash trigger folded in.
3. **Full §6.5 fan-out, all 11 pass** (details below) — the first empirical measurement ever for the
   original nine, closing that standing BACKLOG entry too.

## How the fan-out ran (precedent for next time)

Menu variant per houseCARL PR #295, judges upgraded to **Opus high** (Aaron's explicit choice over
PR #295's sonnet-low; recorded in each archive's method string). One fresh-context judge per query —
only its query + the anonymized lettered menu of all 11 NEW descriptions + NONE, forced single pick,
reasoning first; one Opus adjudicator per skill scoring from reasoning (§6.5 step 4). **Contamination
guard:** the live install was renamed aside for the fan-out window (fresh judges otherwise carry the
OLD descriptions in their own available-skills listing — §6.5 step 2's warning, which also means the
2026-07-15 archives' judges may have carried the then-current install), restored after. 265 judge
queries over three rounds, zero agent errors. Orchestrated as a Workflow (pipeline per skill,
schema-forced structured output); scores archived per skill in `evals/results-2026-07-29.json`
(repo-precedent sibling files — eval query sets untouched, heterogeneous schemas deliberately not
restructured, mirroring PR #295's call).

## Results

| Skill | Recall | Specificity | Note |
|---|---:|---:|---|
| requiem-patching | 10/10 | 10/10 | router framing intact |
| requiem-weapon-patching | 9/10 | 10/10 | staff-value query → magic; defensible, carry noted |
| requiem-armor-patching | 9/9 | 9/9 | |
| requiem-ammo-patching | 9/9 | 9/9 | |
| requiem-race-patching | 9/9 | 9/9 | clean in all 3 rounds |
| requiem-npc-patching | 8/9 | 9/9 | **3-round fix loop** (below) |
| requiem-leveled-list-patching | 9/9 | 9/9 | |
| requiem-magic-patching | 8/10 | 9/9 | at-gate; both misses are record-class fences (AMMO, ALCH) |
| requiem-consumable-patching | 10/10 | 9/10 | q12 MGEF-retune fired here; matches doctrine |
| requiem-perk-assignment | 10/10 | 10/10 | old Q8 gate-clash miss now hits |
| requiem-script-patching | 10/10 | 10/10 | |

**The npc fix loop** (the release's one below-gate case, like PR #295's tool-output-awareness):
round 1 (396 chars) failed recall 7/9 — the compression had ceded the old "custom creature takes wrong
damage" claim to the race skill. Round 2's disambiguator won one query back but "set only the balance
fields" read as disclaiming the Perks list, flipping a perks-framed query to perk-assignment — against
the perk-assignment eval set's own boundary note ("whole-NPC frame → npc; perks are one lane of it").
Round 3's **whole-actor framing** (perks listed as one lane; 437 chars, over-band justified per §8
item 7) passed 8/9 + 9/9, with race and perk-assignment re-measured clean each round. **Carry-forward
(recorded in the npc archive):** the NPC/RACE boundary stays coin-flippy where an unclassified creature
race causes actor-level symptoms — q4 and q9 are the same collision splitting on target-vs-symptom
framing; candidate hardening is a defer-to-RACE clause in npc or a pointer in race.

## Also learned

- The `re.sub`-into-generated-JS pipeline eats `\n` escapes unless the replacement is a function, and
  Windows Python writes CRLF into files the Workflow tool then rejects (control-character guard) —
  both bitten and fixed this session; keep `newline='\n'` on generated workflow scripts.
- `EnterWorktree` names branches `worktree-<name>`; rename to `claude/<name>` after creation.
- A `: ` inside an unquoted single-line YAML description ("Use for any WEAP record: a modded sword")
  silently kills the whole frontmatter at load ("all frontmatter fields silently dropped") — only
  `claude plugin validate --strict` caught it. Five descriptions now carry YAML double quotes; the
  quoted scalar parses back to exactly the fan-out-measured text (verified).

## Drafted for houseCARL: the §8 item 4 / §3.2 amendment (review issue 2)

Four compressed descriptions (consumable, perk-assignment, router, script) carry no "Use when..."
clause yet score 10/10 recall — item 4 (unamended by #294) fails them on the letter. Open this as a
houseCARL PR (a `[Docs]` issue + `Fixes #N`; cross-repo writes are blocked from this repo's
sessions — a stray empty branch `claude/checklist-item4-compressed-format` already exists there from
the blocked attempt, reusable or deletable):

- **§3.2 operational rule**, append: *"(Amended 2026-07-29, authoria-requiem #43: under the
  compressed § 3.5 format the three trigger nouns may sit anywhere in the description — the
  action-first lead often carries them — and a separate 'Use when…' clause is optional; where one
  exists, this rule applies to it as written. Evidence: the #43 fan-out measured four descriptions
  with no 'Use when…' clause at 10/10 recall.)"*
- **§8 item 4**, replace with: *"Description contains ≥ 3 concrete user-utterance trigger nouns —
  in the action-first lead or a 'Use…' clause (§ 3.2 as amended 2026-07-29)."*

Then re-sync `standards/` here (BACKLOG tracks it) and drop the interim deviation note from
CLAUDE.md.

## Open / next

- Post-merge: live-install robocopy sync + restart, then a fresh-session `/doctor` measurement posted
  to #43 (the remaining empirical check — offline math says the listing drops by ~1,000 est. tokens).
- The npc↔race defer-clause hardening (carry-forward above) if a live session hits the boundary.
- houseCARL's live install looked stale relative to PR #295 during this session (the old long
  descriptions still showing) — worth a sync check on that repo's side.
