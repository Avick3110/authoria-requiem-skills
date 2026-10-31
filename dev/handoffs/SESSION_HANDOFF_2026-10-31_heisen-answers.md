# Session handoff — Heisen's answers recorded

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (frozen at close). Project: `dev/rebuild/`. Supersedes
`SESSION_HANDOFF_2026-10-31_restructure-and-recon-A.md` as the latest handoff.*

## Where we are

- **Stage:** recon, essentially done. What's left before the spec is listed under "Next".
- **Heisen answered** the questions doc the same day it was sent. His answers are verbatim in
  `rebuild/recon/answers-heisen-2026-10-31.md`. He deleted question 36, so his numbers from 36 on are the sent doc's
  minus one.

## Done this session

- **Dispositions** for every question are in `rebuild/recon/questions-for-list-authors.md`.
- **Each split's fate** is in `rebuild/recon/splits/README.md`. 3 of 14 change materially (01 and 07 NPC workflow,
  02 perk-value method). Encounter zones and the Xarrian scripts are dropped. The rest survive, because they
  already are Requiem's rule applied or a house convention he confirmed.
- **`decisions.md`, 2026-10-31:**
  - the reference stance: Requiem and its addons first, each addon winning in its own area, with the handmade
    patches kept for the house layer only;
  - the NPC doctrine: no AlreadyReqtified from the skill, never touch factions, flag modded perks and spells;
  - the other rules from the answers;
  - Aaron's design rule: a line must carry what the model can't get from the load order, and lines get deleted as
    models improve;
  - users are patching authors.
- **Q44 filed** in the ARR release hub's `BUGS.md` as CONFIRMED, after a live read: the Bruma master patch drops
  `PerkMasterTraderGold` from 3 inn chests. That edit sits in the arr-release repo, uncommitted.

## Aaron's view (for the spec)

Heisen's 1.x skills compensated for what he saw as limits of model experience and intelligence. Aaron thinks that
view is already partly stale and won't survive another six months. The balance: carry hidden knowledge and method,
not hand-holding. The value eval, graded transcripts of real patching, is the proof.

## Next

1. **Before the spec, no input needed:**
   - N2: probe what houseCARL already returns for a coverage diff;
   - N3–N5: map `.pex`, asset and runtime-layer discovery, which needs houseCARL's next release for the asset
     walk;
   - A6: root-cause the tool traps;
   - merge the census `conventions.json` files with labels and a dominance field;
   - re-verify the rest of the suspected bugs.
2. **Aaron's calls:** D5, C1, C2, the trigger-eval design (houseCARL mocked or loaded), and Q29 (Ancient Falmer
   arrows: about 100, or the Dawnguard comparison).
3. **The spec session**, with the hook and coverage-script discussion first (`rebuild/carried-forward.md`, N1–N5).
4. **B1–B4** need Aaron's own patching sessions with decision logs running.
