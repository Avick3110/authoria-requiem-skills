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
- **Heisen answered the questions doc** (same day). The answers and their dispositions are in
  `recon/questions-for-list-authors.md`. The verbatim copy stays private in the local eval harness, because it
  was written as a working note, not for publication. (Heisen; Aaron decided where it lives)
- **Reference stance.** Values and patterns come from **Requiem and its addons**, and an addon wins in its own area
  (Magic Redone for magic, WAR for weapons, armour and ammo, Vampires Tweak for vampires, and so on). The handmade
  patches are inconsistent by nature: the census keeps their **house layer** (retirement, the bridge, NonPlayable
  handling, AR 0 by slot, loot stripping) and drops their exceptions. A record that lacks Requiem's keywords,
  conditions and flags is not patched, however close its numbers look. (Heisen's answers, agreed by Aaron)
- **NPC doctrine.** The skill gives NPCs role-appropriate perks, stats and spells the way Requiem does for vanilla
  NPCs and encounters. It does **not** add `REQ_AlreadyReqtificated`: the user reruns the Reqtificator for the generic
  perks, then may add the marker and move the record as housekeeping. Never skip an NPC for an empty perk list;
  remove null perks and add the package in one pass; handle ActorEffect; never touch factions; flag modded spells
  and perks and ask, never remove them. Template an NPC only when its role exactly matches a vanilla NPC.
  (Heisen, agreed by Aaron)
- **Other rules from the answers:**
  - skip NonPlayable gear, because the build rescales it (a NonPlayable bow still needs NPCsUseAmmo);
  - never attach scripts to doors or chests;
  - encounter zones are out of scope (the world is de-levelled);
  - retire with `AD3B01` and the `REQ_NULL_` prefix;
  - only major followers with their own framework go in the bridge;
  - paddings follow WAR's padding pieces.

  (Heisen, agreed by Aaron)
- **Design rule for the rebuild: a skill line must carry something the model can't get from the load order.**
  - **Keep** what is hidden or non-obvious: boundary facts (what the Reqtificator does and doesn't do), house
    conventions, which addon owns which area, coverage discipline, the "never" rules.
  - **Cut** what exists only because the model "can't be trusted": value recipes, tool call shapes, restated
    Requiem ladders, stop-to-ask gates.
  - **Judgement calls** (paddings, uniques, boss resists) get the method and the comparable, not an answer. The
    model decides and surfaces a decision list for the author.
  - As models improve, lines get deleted, not added. The value eval (graded transcripts of real patching) shows
    where a line is missed, and only that gets added back. Aaron's view: the older "models need hand-holding" stance
    is already partly stale. (Aaron)
- **Users are the patching authors** (Aaron, later Heisen), not end users of the list. This confirms intent. (Aaron)
- **No live install of the pack while the rebuild runs.** The 1.x skills load in every session and would skew the
  B1–B4 logs and the no-skill runs. CLAUDE.md step 0 is changed (PR #54). (Aaron)
- **The new skills are graded against the spec, not against 1.x.** Beating the old scores is near-certain and tells
  us little. Graded runs score each skill against the documented conventions (census + the authors' answers). Each
  skill's dev loop always includes a fresh no-skill run, to find lines the model doesn't need. (Aaron)
- **C1: no frozen checklist.** The R10 checklist isn't frozen as the re-run metric. Its checks get rewritten from the
  spec. Reusable plumbing from its fix list: one scoring format and JSON schema, full subrecord dumps in the harness,
  and no free credit for a do-nothing plugin (lift over the empty-plugin floor). (Aaron)
- **D5 dropped.** No formal scored validation on the 6 held-out patches. The per-doc held-out checks stand. Running the
  new skills on those 6 and diffing against the authors' patches checks the spec itself: a skill that matches the
  spec but not the authors means the spec is wrong. (Aaron)
- **C2: no separate plan + boundary-fact arm.** It moves into the build order: the router (coverage procedure +
  boundary fact) is built first and graded against the spec, and domain content is added only where the router alone
  misses. (Aaron)
- **Trigger evals load the real houseCARL plugin** alongside ours, so its skills and server instructions compete as in
  real use. Mocks, if any, are decided for full graded runs at spec time. `claude plugin eval` runs under WSL2 (no
  Bash sandbox on native Windows). (Aaron)
- **Q29: every Ancient Falmer / snow-elf arrow is about 100** (Ebony level, above WAR glass 90). A house value: Requiem's
  own Dawnguard snow-elf arrows stay at Elven 60. (Aaron, from Heisen's proposal)
- **The pack also targets OpenAI Codex (GPT models).** Intent C2 and C4 are amended: the skills load and work in Claude
  Code and in Codex, and conform to both Anthropic's and OpenAI's published skill-authoring guidance. Recon A8 added.
  (Aaron)
- **The pack ships as an installable release.** Users install it by hand or through their agent on either host.
  Recon A9 added. (Aaron)
- **Review gate: every written skill gets a review pass against the Anthropic and OpenAI docs** before it lands. It
  is a review per skill, not a sample, and it comes on top of the evals. (Aaron)
- **The skills project waits for houseCARL's asset-layer project.** The 2.1.2 live test (N3–N5) led houseCARL to
  redesign its asset and runtime side as one project (one asset door, one walk, one select, one runtime-layer
  reader; about 10–13 PRs, runtime reader last). Our needs are an input; the correct design for the layer decides.
  (Aaron, via the houseCARL advisor's note)
- **N3–N5 ownership: houseCARL owns the readers.** The SPID / KID / BOS / FLM reader (houseCARL#934), the SkyPatcher
  target filter (#1286), winning-copy batch decompile (#1287) and, later, a script call-site scan are houseCARL's.
  We build none of them: a grep is a stopgap only (88% of KID's winning lines target by EditorID, and a grep ignores
  which copy wins). The spec writes N3–N5's runtime-layer and script-discovery steps against the **capability**
  ("houseCARL answers which lines target plugin X"), not today's tool calls. (houseCARL advisor, 2026-10-31)
- **Hold graded and B1–B4 runs that measure asset or runtime calls** until the relevant houseCARL PRs land. Work
  resting on record reads and `asset_status` selection (provider, `under=`, globs, `counts_only`) proceeds now; the
  walk's parameters, `skse` config and `skypatcher_layer` filtering, `decompile_script` input and the `links=` render
  will change shape, so don't hard-code them. (houseCARL advisor, 2026-10-31)
