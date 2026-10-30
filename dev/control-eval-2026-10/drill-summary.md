# Re-mining the control eval: drill rounds 1–3

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (supersede, don't edit).*

After the first synthesis ([`synthesis.md`](synthesis.md)), the saved evidence was re-mined in
three rounds (~130 agents, no new patching runs), each closed by a completeness critic. A
per-skill distillation followed ([`distillation/`](distillation/DISTILLATION.md)). The raw outputs
stay local in the eval harness. Paths such as `drill/r2/U6-…` in the distillation refer to it.

> [!warning] Corrections to the first synthesis
> - **"Most of the gain came from the router" is wrong.** The wins are **joint**. Of the 17
>   deciding points: 9 are joint, 1 is router-only, 1 is domain-only, and the rest are domain-led,
>   the agent's own probe, or mixed. The ingredients are the router's coverage plan, one
>   corrective boundary fact, and a domain skill naming which live comparable to read.
> - **"Only 15% of decisions came from skill text" is wrong as a measure of influence.** Values
>   come from the load order. But 42–81% of skill-arm decisions were *prompted* by skill text: it
>   chose what to read and which comparable to adopt.
> - **"The skill arm makes more major wrong calls" is not supported** once judge-sensitive items
>   are removed.
> - **favor-quests is a tie plus one failed run, not a measured loss.** Two of the three skill
>   plugins are byte-identical to the control majority.
> - **Cost:** 3.87× the money (the first figure double-counted the resumed sessions), about 1.7×
>   per right major change.

## Round 1: six angles

Coverage and planning, an absolute-rule ledger, missing facts, a neutral re-score treating
upstream vs downstream as a per-patch choice, run-to-run consistency, and process.

- **The controls' failure.** Every run listed the mod's record types. The controls then dismissed
  records unread, on a wrong belief: "the Reqtificator handles it". In fact the Reqtificator does
  not tick template flags, de-level, give traits to custom races, or touch a mod's own leveled
  lists.
- **The stance changes no verdict.** All 42 runs went upstream, and none asked.
- **Harm sits in about 9 rules.** Every evidence-based break of an absolute rule was judged right.

## Round 2: open questions U1–U15

- **Blinding held.** The off-order Reqtificator output holds none of the records the references
  touch.
- **Judges are noisy on counts, not on verdicts.** Reference-label agreement is 60–76% on the hard
  tasks, but every deciding point is unanimous or mechanical.
- **The Reqtificator's C# source is in the instance.** Five skill facts are false: REQ_NULL =
  inert (only PERKs are emptied), clothing AR 0 as a Requiem rule, the "AR caps / ~×10" figure,
  "RegenHpInCombat is needed", and "lockpicking is an SKSE DLL".
- **The stat-mode gate added nothing.** One win was a side effect of the harmful REQ_NULL rule.
- **Reference bugs, counted by judge agreement:** 48 CONFIRMED, 39 PLAUSIBLE. They are filed
  separately after review.

## Round 3: open questions R1–R12

- **The C# source is the build that produced the Reqtificator output.** Predictions from the code
  match the output exactly for NPCs, weapons, containers, doors and leveled lists. Bows are ×4,
  bow speed is *set*, and ammo is ×4 with no keyword.
- **Strict attribution of the 17 deciding points.** This is the joint split above.
- **A mechanical checklist of 95 items agrees with the judges' verdicts.** It is built from the
  judgements, so it is consistent with them rather than independent. It is not yet frozen.
- **The six handmade references are all ESL-flagged; none of the 42 runs is.**

## What it means for the rebuild

See [`distillation/DISTILLATION.md`](distillation/DISTILLATION.md):

- **Helped:** 42 groups.
- **Hurt:** 55 groups.
- **Unnecessary:** about 86% of the ~12,660 lines.
- **Verdicts and briefs:** a verdict and a rewrite brief per skill.
- **Fact sheet:** the corrected facts, with the source of each.

The rebuild runs as a project in `../plans/2026-10-30-requiem-skills-rebuild/`.
