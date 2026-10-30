# B6 — Open house conventions the armour and quest sessions won't settle

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, target B6. Sources: `../intent.md` open question 1;
`../../../control-eval-2026-10/distillation/DISTILLATION.md` §7.*

**Method.** Each open item is checked against what an armour pack and a quest mod normally carry.
"Likely" means the item comes up by itself in that kind of job; "only if" means it depends on that
mod's content. This is a prediction: B4 records what actually came up.

| §7 # | Open item | Armour session | Quest session | Needs a direct call? |
|---|---|---|---|---|
| 1 | Clothing / extra-slot AR 0 | Likely | Only if it adds wearables | No: armour should settle it |
| 2 | ESL flag on override-only patches | Likely | Likely | No |
| 3 | Mastery perks on NPC casters | No | Only if it adds caster NPCs | **Yes** |
| 4 | Stat-mode list default (AutoCalc v manual) | No | Only if it adds NPCs | **Yes**, unless the quest adds NPCs |
| 5a | NoArmorRatingRescale in baked patches | Likely, but only on a downstream (baked) patch | No | **Probably**: the sessions default to upstream |
| 5b | Baked ranged / tempering keywords | Tempering only, and only if baked | No | **Yes** for ranged (weapons/bows) |
| 6 | Follower registration via the Authoria bridge | No | Only if it adds a follower | **Yes** |
| 7a | Master robes at L1 | Only if robes are in the pack | No | Probably |
| 7b | Altar Shield heavy v light | Indirectly (it sets the classification rule) | No | Partly |
| 7c | RB bow 68 @ 0.9375 | No | No | **Yes** |
| 7d | Saint's Elixir 3600 s | No | Only if it rewards consumables | **Yes** |

**Not conventions, but also needing Aaron's direct call** (§7 items 8–14: stance package,
fan-out default, price-rule priority, skill merges, unverified lines, untested domains, the P+B
arm). B5 covers untested domains (#13) with evidence. The rest are spec-interview questions.

## Result

- **Expect the two sessions to settle:** #1, #2, and the armour classification rule behind #7b.
- **Bring to the spec interview whatever happens:** #3, #5b (ranged), #6, #7c, #7d.
- **Depends on what the chosen mods contain:** #4, #5a, #7a. If the quest mod adds NPCs or a
  follower, #3, #4 and #6 may come up there. Choosing a quest mod with caster NPCs and a follower
  would cover three more.
- **#5 only arises downstream.** If both sessions run upstream (the default), #5a/#5b need a
  direct call, or one session should deliberately bake.
