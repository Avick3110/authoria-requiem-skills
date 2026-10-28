# Control eval against ARR's handmade patches

*Author: Aaron (+ Claude session) · 2026-10-28*

*Class: LIVING until executed, then ARCHIVE (plan frozen at close).*

## Question

The eleven skills were written for houseCARL 1.x and for the models of the time (Opus 4.8,
GPT 5.5). houseCARL 2.0 refuses the 1.x tool names, and current models may already know enough
Requiem to patch without help. Before deciding whether to update, rebuild, or drop them, measure
whether each skill still beats a bare **Opus 5.5 + houseCARL 2.x** on a real patching job.

ARR makes this unusually precise: it carries about 250 handmade Requiem patches. Hide one patch
from the agent, ask it to make that patch, and diff its plugin against the handmade one. The
answer key already exists.

## Decisions (Aaron, 2026-10-27/28)

- **Two arms, not three.** Control (no authoria-requiem skills) and the skills as they ship. A
  third "domain facts only" arm is held back. Mining the with-skill transcripts should show which
  skill content helped; the third arm is added for one skill only if that stays ambiguous.
- **Rebuild vs update is deferred** until the results are in.
- **Instance.** The eval runs on Aaron's ARR instance, which is now his personal instance (list
  dev starts from a fresh download), so instance changes for this eval need no release-ledger
  line. A dedicated test profile is used. Nothing destructive, no in-place edits; the tasks need
  neither.
- **Blinding by moving the patch out** and disabling every plugin after it, which simulates the
  state MO2 is in just before a new patch is written to load last. The patch goes back after the
  task.

## How the eval reaches the ARR instance

houseCARL picks its instance from, in order: the saved user config in `HOUSECARL_DATA_DIR`
(else beside the exe), then `HouseCarl__Mo2InstanceDir`. It reads the profile MO2 has selected
(`selected_profile` in `ModOrganizer.ini`).

The global saved config already points at ARR, but it also carries in-place consents for about
twenty ARR plugins, several of them `Authoria - Reqtificated - *.esp`. An agent on that config
could write in place to those without being asked. So the eval does not use it:

- **Eval harness folder** outside this repo (local only; holds machine paths). Sessions run from
  there, so no repo `CLAUDE.md`, skill source, or `dev/` corpus is in reach.
- Its own **`.mcp.json`**: the installed houseCARL exe, with `HouseCarl__Mo2InstanceDir` set to
  the ARR instance and `HOUSECARL_DATA_DIR` set to a fresh, empty eval data folder. No saved
  consents, so any in-place write hits the consent gate and, unattended, fails safe. Aaron's own
  saved config is never touched.
- Tool paths are not carried over (no Papyrus compiler). A task that needs one gets it added to
  the eval data folder only.
- Before each task: select the test profile in MO2.

## Blinding, per task

All ~250 patches share one mod folder, `Authoria - Requiem Patches`, so the move is one file, not
the folder.

1. In the test profile, the patched source mod stays enabled.
2. Move the target patch `.esp` out of the instance, along with any side file that belongs to it
   (e.g. `Authoria_Reqtificated_AnimatedShips_DISTR.ini`).
3. Disable every plugin after the patch's load position. This also drops Requiem for the
   Indifferent and every other generated output, which would otherwise carry the patch's values.
4. Check that no plugin still enabled lists the patch as a master. Prefer leaf patches.
5. **Leak sweep.** Grep the instance's mods folder for the source mod's name and the patch's
   name. Known risks in the shared folder: `readme.txt`, `To Do.txt`, `add to npc patcher.txt`,
   `meta.ini` notes, `Source/`, `Scripts/`. Move out anything that describes the patch.
6. After the task: put the patch and side files back, re-enable, restore the profile.

## Arms

| Arm | Environment | Told |
|---|---|---|
| **control** | authoria-requiem moved out of the skills folder for the run (houseCARL's own skills stay: they are part of the baseline) | The task only |
| **with-skill** | authoria-requiem installed from this repo's `main` | The task, plus: load `requiem-patching` first and say what it changed about your plan |

- Forcing the router load measures the skills' **content**. Whether they trigger on their own is
  the trigger fan-out's job (§6.5), not this eval's.
- **Contamination.** The control must not see the skills in its own available-skills listing
  (the 2026-07-29 and houseCARL #295 lesson), nor read them from disk. Move the install aside and
  deny reads of this repo and of the installed copy for the control run.
- Neither arm is told about the other, the handmade patch, or that this is an eval against one.
- 1.x tool names in the skills will be refused by houseCARL 2.x. That is recorded, not fixed: it
  is part of what the skills cost today.

## Run shape

- **Model:** Opus 5.5 for both arms. A Sonnet or Haiku row is optional later. No Fable agents.
- **Brief:** Aaron's words for a real job, e.g. "patch `<mod>` for Requiem; write the patch as a
  new plugin". No method, no outcome shape.
- **Writes:** a NEW plugin only, in its own `HC_Eval_<task>_<arm>_<run>` mod folder. Nothing else
  written into the instance.
- **Runs:** 3 per arm per task.
- **Per run, recorded:** the full transcript, total tokens, duration, houseCARL call count, and
  (with-skill) which skill files were read.
- **Budget:** a call cap high enough for a whole-mod patch (start at 80; raise if the control and
  the skill arm both hit it).

## Task selection

1. Census the 250 patches through houseCARL from the eval harness: record types and counts per
   patch, masters, side files.
2. Pick one or two patches per skill domain (ammo, armor, consumable, leveled list, magic, NPC,
   perk, race, script, weapon, plus a whole-mod job for the router). Mix sizes, and include a few
   hard ones (multi-master, conflict resolution).
3. **Exclude patches the skills already name.** Their worked examples would turn the task into a
   recall test (the houseCARL 2.0 re-gate lesson). Named in skill text on 2026-10-28: Glenmoril,
   Apocalypse, Sonic Mage, USSEP, Xelzaz, Kaidan, Inigo, Vigilant, Shadow of Skyrim, Ryn's
   Standing Stones, Lucien. Also exclude any patch used in `dev/corpus-*` or
   `dev/s4-graycowl-validation/` (Gray Cowl of Nocturnal).
4. Aaron approves the pick list before any run.

## Scoring

The houseCARL cross-plugin diff compares the agent's plugin with the handmade one, record by
record and field by field:

- **Recall:** records and fields the handmade patch touches that the agent missed.
- **Precision:** records and fields the agent touched that the handmade patch did not.
- **Value agreement:** exact, within tolerance, or wrong.

The handmade patch is the reference, not automatically the truth. An Opus judge with the live
order sorts every mismatch into one of three buckets, and Aaron spot-checks them:

- **defensible alternative:** taste, or another valid Requiem reading
- **agent error**
- **handmade bug:** filed to `arr-release` `BUGS.md`

Per task, the arm verdict is **BEAT / TIED / LOST**. The skill arm beats the control when it
scores better on the diff across its runs, with the deciding mismatches named.

## Mining the with-skill transcripts

For each with-skill run, list which skill files were read and which lines were quoted or acted
on, and tie each to the fields it produced. Read it against the control's diff on the same task:

- A field the skill arm got right and the control got wrong, where the log cites a skill line,
  counts as **attribution**.
- A cited line on a field the control also got right is **noise**: the model already knew it.
- A skill line followed into a wrong field, a refused 1.x call, or a workaround rule that cost
  calls counts as **harm**.

Output: per skill, its sections labelled helped / noise / harmed / unused. That labelled list is
the input to the rebuild-vs-update decision, and the starting point for a rebuild if it comes to
that.

## Where results land

- Raw transcripts and logs stay in the eval harness folder (local; they carry machine paths).
- Scrubbed per-task verdicts, the diff tables, and the per-skill section labels land in this
  repo under `dev/control-eval-2026-10/`, with publish hygiene applied.

## Cleanup

Delete the `HC_Eval_*` mod folders, and the eval profile if it is no longer wanted. This is
done by Aaron, or on his word.
