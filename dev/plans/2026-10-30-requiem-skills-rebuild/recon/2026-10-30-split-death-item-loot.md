# Split hunt 6: death-item loot

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, census follow-up. The census (`census/stage2/LISTS.md` #8) found
creature death-item lists dropping vanilla generic loot sublists (~260 lists, 6 plugins). It classed the
`LootSmallTreasure10` part as Requiem-aligned and "the wider strip" as house. This round asked what decides which
sublist goes, on which list. Scripts and data stay local (`census/splits/death_*`).*

## Scope

- **ARR's lists:** 386 death-item lists in ARR patches; 356 of them carried a vanilla sublist before the patch.
  Glenmoril, Vigilant, Unslaad, Dac0da and Bruma carry most of them.
- **Who dies:** the actor type of the NPCs that use each list (via race keywords), read live for 2,898 NPCs.
- **Comparable:** Requiem.esp's version of the 49 vanilla `DeathItem*` lists against Skyrim.esm.

## What Requiem does

On vanilla lists, Requiem removes essentially one thing:
- **`LootSmallTreasure10` (`0F961F`), from 26 / 26 lists that had it.**
- A handful of single removals besides: Imperial Luck and the Golden Touch gold change from 3 each, and the
  Draugr gold list.

It adds species drops such as bone meal and Requiem ingredients.

## What ARR does

ARR goes well beyond Requiem. It also strips potions, poisons, scrolls, lockpicks, arrows and child misc items.
Two domain reasons hold.

| Sublist kind | Who dies | Removed |
|---|---|---|
| Treasure / gold / gems | humanoid | **11–17%** (kept) |
| | non-humanoid, regular | **56%** |
| | non-humanoid, boss / named | 40% |
| Humanoid gear (potions, scrolls, picks, arrows) | regular enemy | **71–75%** |
| | boss / named | 44–48% |
| Body parts / ingredients | any | 14–33% (mostly kept) |

1. **Gold and treasure stay on humanoids and come off creatures.** This extends Requiem's own
   `LootSmallTreasure10` rule from vanilla creatures to modded ones.
2. **Generic humanoid gear comes off regular enemies; bosses keep more of it as a reward.** Boss wolves, the
   Wendigo and the Skaven King keep `LItemPotionAllBest`.
3. **Species parts stay** (bone meal, hearts, flesh): 14–33% removed.

**Plugin behaviour differs:**
- Glenmoril, Vigilant, Unslaad and Bruma strip.
- Dac0da leaves 25 of 35 lists untouched.
- LOTD leaves all 6 untouched.

Most lists are only partly stripped (213 of 356). The two rules above set the direction, not every entry.

## Hypothesis log

| # | Hypothesis | Result |
|---|---|---|
| H1 | Follow Requiem: remove `LootSmallTreasure10` only | Part: ARR removes it 35, keeps it 26, and strips much more |
| H2 | Who dies (actor type) decides treasure | **Kept**: humanoid 11–17% v creature / undead / animal 56–66% |
| H3 | Who dies decides humanoid gear | Dead as stated: humanoids lose gear too (64%), animals keep it (81%, all bosses) |
| H4 | Boss / named lists keep rewards | **Kept** for gear (44–48% v 71–75%) and partly for treasure |
| H5 | Species parts are kept | **Kept** (14–33% removed) |
| H6 | Stripped generics replaced by the creature's own drop | Weak (lists with additions strip 53% v 44%) |
| H7 | Per plugin | Strong confound: Dac0da and LOTD barely strip; Glenmoril / Vigilant / Unslaad / Bruma do |
