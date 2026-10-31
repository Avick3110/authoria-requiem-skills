# Session handoff — tool-surface refresh (1.9.0)

*Author: Aaron (+ Claude session) · 2026-07-22*

## What triggered this

A user reported that `requiem-armor-patching` burns a lot of tokens. The question put to the session
was whether the skill sends the agent to derive keywords and stats from live plugin reads every time,
or whether it serves them from the bundled reference tables.

Answer: live-derivation, deliberately. `SKILL.md` demotes the mined ladders to "a cross-check and a
starting point" and makes the live read of the comparable the authority.

## The doctrine was the first suspect — and it is cleared

The initial hypothesis was that the live-read rule was both the cost *and* possibly wrong: a patch
loads **before** the Reqtificator, so deriving numerics from a post-Reqtificator winner could
double-apply a rescale. The skill's answer to that is a two-item hand-exclusion list (resist tier,
tempering perk) plus the claim that a rescale "folds in automatically" — a bet that keyword
assignment is the whole delta.

**Verified against the live order.** Diffed `Requiem.esp` → `Requiem for the Indifferent.esp` on six
comparables — steel and ebony heavy cuirass, glass/elven/fur light cuirass, ebony shield:

- The **only** delta in every case is `Keywords`. `ArmorRating`, `Value`, `Weight` are identical.
- The added keywords are exactly `REQ_Armor_Resistance_Ranged_Tier*` and `REQ_Tempering_*`.
- The ebony **shield** took tempering but **no** resist tier — independently confirming the skill's
  claim that ranged resistance gates on the cuirass part keyword.

So there is no numeric pass to double-apply, the two-item exclusion list is complete for this
population, and "derive from the live winner" needs no revision. **No derivation rule moved in 1.9.0.**

Scope of that check: six records, all Requiem-native vanilla-derived pieces. That is the right
population — comparables are Requiem-native by definition.

Side probe, no finding: 11 `ARMO`s exceed the heavy-body cap of 740, but all 11 are `zzz*` boss and
creature shells (Jyggalag, Knight of Order, Dreugh, Glenmoril cyborgs) — the worn-NonPlayable
category the skill already carves out. No player-facing gear over cap.

## The actual cause: stale tool surface

The skills were written against an older houseCARL query surface. Audit by *usage* (not by date — date
misleads: `requiem-perk-assignment` is old but modern, `requiem-weapon-patching` is recent but stale):

| Skill | `resolve_names` | `composes` | `defined_in` | `group_by` | scalar `references=` |
|---|---|---|---|---|---|
| weapon | – | – | – | – | 6 |
| armor / ammo / leveled-list | – | – | – | – | 4 each |
| race | – | – | – | – | 1 |
| consumable / perk-assignment / magic / npc | yes | partly | partly | partly | 0 |

Pack-wide, nothing used `winner_fields`, `format="dense"`, `housecarl_diff_record`, or pointed at
houseCARL's `bulk-record-jobs`.

## What shipped (1.9.0, [#41](https://github.com/Avick3110/authoria-requiem-skills/issues/41))

Bodies + references across armor, weapon, ammo, leveled-list, race; references only for magic.
**No `description:` changed → no §6.5 fan-out re-measure owed.**

- **List-valued `references=`** everywhere it was scalar. A five-piece set's recipe lookup went from
  five calls to one, with the `matches` column naming which piece each hit belongs to.
- **The recipe read is a verified two-call shape.** Probing surfaced a constraint the skills never
  documented: `cross_plugin_query` has **no `depth=`**, so `Items`/`Conditions` return as
  `[list: N item(s)]` and must be expanded via `batch_record_detail ... depth=4 resolve_names=true`.
  **`depth=4` is the working depth** — `depth=2` yields only element types (`[ContainerEntry]`,
  `[ConditionFloat]`), `depth=3` stops short of the values. At 4 you get `Items[i].Item.Item`
  (resolved and named), `Items[i].Item.Count`, and `Conditions[0].Data.Perk` as its named perk.
- This replaced the pack-wide note "houseCARL 1.2.2+ renders the perk parameter as a readable
  FormID" — true but useless: it promised readability without saying what produces it, so an agent
  following it got `[ConditionFloat]` and had to probe for the depth.
- **`format="dense"` + `resolve_names=true`** on the whole-plugin triage sweeps; `limit=`/`offset=`
  paging named.
- **`conflict_tree` dropped** where only winner values are consumed; **kept** on the freshness probe
  and post-enable verification, where the chain is the point.
- Whole-plugin passes point at `bulk-record-jobs` when the product is a deliverable.

## Two judgment calls worth knowing about

1. **`defined_in=true` was deliberately NOT added to the triage sweeps.** It narrows the enumeration
   to records the plugin *defines*, dropping the vanilla records it *overrides* — which would silently
   shrink the coverage denominator the reconciliation count checks against. That is a doctrine change
   wearing a performance costume. It is documented as an explicit opt-out with its trade-off named.
2. **Every FormID in the new call shapes was read off the live order.** Mid-edit the session invented
   set-piece FormIDs from a plausible-looking pattern and caught it; the real steel armor set is
   `013951`–`013955` (feet/body/hands/head/shield) and the steel weapons are `013983`–`01398A`.
   Worth flagging as a recurring failure mode when authoring examples into a live-analogy skill.

## State at handoff

- Branch `claude/tool-surface-refresh`, cut from `f70a4aa` (= `origin/main` at PR time, no rebase owed).
- Local CI conventions checks pass; `claude plugin validate --strict` passes for both manifests.
- No stale `1.2.2+ renders` notes and no scalar `references="…"` remain anywhere in the pack.

## Follow-ups not taken

- `winner_fields` and `housecarl_diff_record` are documented where relevant but still unused in any
  worked example — a future pass could show them working rather than mentioning them.
- `requiem-consumable-patching`, `requiem-npc-patching`, `requiem-perk-assignment`,
  `requiem-script-patching` were not audited for `format="dense"` opportunities; they were already
  modern on the primitives that mattered for #41.
