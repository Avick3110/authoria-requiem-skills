# Requiem's vision — the system a patch has to fit into

Requiem is "the Roleplaying Overhaul": it rebuilds Skyrim into a **de-leveled, knowledge-gated,
economy-tight** world where power comes from perks, gear, and preparation rather than character level.
Every integration decision should serve that model. The deep mechanics live in the other references;
this is the frame.

## The five pillars

1. **De-leveled, fixed world.** Enemies and loot have fixed levels (see `requiem-npc-patching` /
   `requiem-leveled-list-patching`): a bandit is a bandit at level 1 or 50. A bear can end your run
   early game; a dragon is a campaign boss. Encounter zones get their combat boundary opened, not their
   levels rescaled. **Implication for patches:** never let new content scale with the player — fixed
   level, Level-1 list entries weighted by repetition.

2. **Knowledge- and perk-gated progression.** You can't use what you haven't learned. Spells require
   the school's mastery perk to cast (the `HalfCostPerk` tier classifier); crafting requires the
   smithing perk; even reading a spell tome is gated by the tier perk. Skills advance by use, but the
   *capability* is the perk. **Implication:** new spells/gear hook into the existing perk trees
   (`perks-skills.md`); never ship a parallel tree.

3. **Tight economy.** Gold is scarce and prices are standardized — weapon prices round to 5, divine
   amulets cost 200, artifacts follow a fixed value ladder (divine 80,000 / legendary 50,000 / mundane
   ≤5,000), innkeepers buy at base price, alchemists don't buy food. **Implication:** value new
   content off the comparable, not the mod author's number (`economy.md`).

4. **A real combat & resistance system.** Damage is weapon output *vs* the target's armor rating and
   elemental/magical resistances, modified by **armor-penetration** perks (one per damage type),
   **stagger/poise**, and **stamina/exhaustion**. Armor materials grant stacking resistance bonuses
   (up to 4 pieces). Magic resistance is capped (60 points/race, magic-resist counted ×3). This is the
   heart of the difficulty — see `combat-resistance.md`. **Implication:** new weapons/armor carry the
   right material/type keywords so the Reqtificator wires them into this system; new actors carry the
   right race/keywords so traits and penetration apply.

5. **Keyword-driven balance, assembled by the Reqtificator.** Requiem doesn't hand-tune every record;
   it tags records with keywords and lets its auto-patcher (the Reqtificator) assign the derived values
   at build time — damage-type keywords, resistance/tempering tiers, racial/state trait perks, the
   all-actor game-mechanics perks, leveled-list merges. **Implication (the cross-phase invariant):**
   a patch carries the **input keywords**; it must **never hand-stamp the assigned outputs**. Get the
   inputs right and the Reqtificator builds the rest.

## What this means operationally

- The patch is an **input** to the Reqtificator, not the finished article. Always end with "run it
  through the Reqtificator."
- Use **live analogy**, never invented numbers: find Requiem's own comparable record and derive from
  it. Every domain skill is built on this.
- **Live > shipped docs.** Requiem's `documentation\` states design intent but drifts from the live
  records (especially race attributes/skills). Read the live winner.
- **Preserve author intent where Requiem doesn't speak.** For uniques/artifacts, keep the bespoke
  effect and normalize only the Requiem-governed fields.

## Source of the "why"

`…\Requiem 6.0.2 …\documentation\` (`Changelog.md`, `Races.md`, `Artifacts.md`) for the design intent;
the Reqtificator config (`Reqtificator.conf`, `ActorAssignmentRules_Requiem.esp.conf`,
`Weapon/ArmorKeywordAssignments`) for the underivable rules; the seven domain skills for the derived
methods. `combat-resistance.md` and the other gap references turn the vision into FormIDs and recipes.
