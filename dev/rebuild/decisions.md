# Decisions — Requiem skills rebuild

*Author: Aaron (+ Claude session) · 2026-10-30*

Append-only. Never edit a past entry; supersede it with a new one.

## 2026-10-30

- **Method.** The rebuild follows the AI-native SDLC method (the `new-project` skill), with Aaron
  agreeing each stage. (Aaron)
- **Location.** The project lives in this repo at `dev/rebuild/`.
  (Aaron)
- **Scope.** Only the Requiem skills rebuild. The general patching layer, a houseCARL dissect tool
  and a review skill are separate projects. (Aaron)
- **Approver.** Aaron holds every gate. Heisen is out of action. (Aaron)
- **Recon.** The control eval and its re-mining count as recon pass 1. Aaron's armour and quest
  patching sessions, with decision logs, form recon pass 2 before the spec is final. (Aaron)
- **Audience.** ARR only. (Aaron)
- **Success.** Re-run the eval, conform to Anthropic's latest skill-authoring documentation, and
  trigger well on its own. (Aaron)
- **Models.** Current Opus and Sonnet. (Aaron)
- **Stance.** The skills state both upstream and downstream, with upstream as the default. (Aaron)
- **Gate 1: intent agreed.** `intent.md` draft 1 was accepted as written. The project moves to
  Recon pass 2. (Aaron)
- **No tool recipes.** The rebuild ships no houseCARL recipes or call blocks, and does not
  substitute 2.x names for 1.x ones: agents navigate the 2.x tool surface unaided (0 refused calls
  on 1.x names in 6/6 tasks). (Aaron)
- **Recon A6 added: root-cause T1–T5.** The tool traps are diagnosed (skill-induced, wrong route,
  real gap or non-problem) before any fix is chosen. T1 (PRKZ=0) already traces to the false
  REQ_NULL fact plus an unverified "zero count = incomplete write" rule, not to a houseCARL gap.
  (Aaron)
- **Recon A7, B5, B6 added and run.** Findings in `recon/`. (Aaron)
- **Locks added.** CONT/DOOR locks (136 of 250 ARR patches, no owning skill) become a recon target,
  D4. (Aaron)
- **Recon D added: census of ARR's handmade patches.** Mine all handmade Requiem patches for
  per-field consensus, explain the disagreements, and validate on held-out patches (the method from
  `arr-ube-projects`). Method and brief to be agreed before it runs. (Aaron)
- **Census design (`recon/census/method.md`, draft 2):**
  - **Corpus:** the 250 plugins, read exhaustively, with the 6 eval references held out.
  - **Verification:** a verifier per agent.
  - **Output:** one synthesis doc per record type.
  - **Readers:** cut by record type and sub-class across plugins, sized by changed-field counts
    measured in Phase 0, not by plugin batch.
  - **Bug v convention:** reference bugs are separated from house conventions at every stage
    (flags on ledger rows, consensus classes, rules built from clean rows only).
  - **Baked layer:** separated from the input layer.

  (Aaron)
- **Census pilot run, then mechanised.**
  - Scripts decide what code, configs, engine rules and comparables can settle.
  - Haiku does the extraction.
  - Sonnet reads the residual and verifies.
  - Opus writes the syntheses.

  (Aaron, on usage)
- **Census stage 1 run (NPC_, ARMO, WEAP, COBJ).** About 4.8M subagent tokens. Report in `recon/`. (Aaron)
- **No review between stages.** House calls are surfaced only if they affect a later stage; they go to the spec
  interview. The doc format is Claude's call: every census doc also gets a `conventions.json`. (Aaron)
- **Census stage 2 run.** About 9.2M subagent tokens.
  - **Corpus:** extended by content-based discovery (+9 misnamed Reqtificated patches, +5 Requiem master patches).
    Merges and compatibility patches stay excluded (Claude's scope call, stated).
  - **Untouched-records analysis added.**
  - **Result:** recon is complete enough for the spec.

  (Aaron)

## 2026-10-31

- **Split hunts 7-13 and the NPC stage-2 family check run** overnight as a subagent fan-out, all on the
  hypothesise-first method. The questions doc grew to 50 and `recon/suspected-bugs.md` was started. (Aaron, who
  asked for all the splits in one session)
- **Questions doc sent to the list authors.** Spec waits on the answers. (Aaron)
- **`standards/` deleted.** It was outdated (houseCARL had deleted its own copies in 2026-09) and conflicted with Anthropic's current guidance in 13 places (recon A4), so it would confuse agents building the new skills. Intent C2 is amended: author to Anthropic's guidance plus the CI checks until the spec sets this repo's own rules. (Aaron)
- **Measurement (intent C3 amended).** Triggering is measured by real invocation, as recon A5 recommended. Value is measured by recording transcripts of real use and grading the output, and how the skill performed, in detail. (Aaron)
- **No second-maintainer review for now.** Heisen is out of action, so Aaron reviews and merges alone; PR #49 lands without his review. (Aaron)
