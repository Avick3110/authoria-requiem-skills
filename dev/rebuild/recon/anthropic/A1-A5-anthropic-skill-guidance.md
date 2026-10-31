# Recon A1–A5: Anthropic's skill-authoring guidance

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, targets A1–A5.*

Sources were fetched on 2026-10-31. Every claim cites a URL. "(inferred)" marks my reading, not a
stated rule. Quotes are short fragments; the rest is paraphrase. A7
([`2026-10-30-A7-install-path-and-plugin-hooks.md`](A7-install-path-and-plugin-hooks.md))
is taken as done; A3 builds on it.

The lens is the control-eval distillation: ~86% of lines unnecessary, absolutes harmful, value in
the router's plan, the boundary facts and "which comparable to read". Anthropic's guidance backs
that lens almost line for line.

## Answer

| Target | Answer | What it means for the rebuild | Sources |
|---|---|---|---|
| **A1** Structure | SKILL.md under 500 lines / ~5k tokens. Add only what Claude lacks. Progressive disclosure one level deep, each pointer saying *when* to read. Gotchas are the highest-value content. Coherent units: too narrow forces several skills to load and their instructions to conflict. Scripts for deterministic checks. | Matches the distillation. The ~1,500-line target is well inside guidance. Fewer, coherent skills beat many thin ones; the profile and ARR layer read as references of the router, not separate skills (inferred). Put the plan and boundary facts at the top of the router. | [best-practices], [agentskills-bp], [cc-skills], [eng-blog] |
| **A2** Descriptions | What + when, specific key terms, use case first. Hard caps: 1,024 chars (spec/API), 1,536 chars description + `when_to_use` in Claude Code's listing. Listing budget is 1% of context; least-invoked skills lose their descriptions first. **No listing mechanic changed since 2026-07-29.** New since then: `claude plugin eval`, `/skill-doctor`, `/doctor prompt-audit`, compaction resends the listing. Current models overtrigger on aggressive wording. | The #43 compression rules still hold. Soften pushy tails; negative scope ("not for X") is now Anthropic-endorsed. Context is 1M on current models, so the 1% budget is far larger than the standard's ~2,000-token assumption (inferred; check `/doctor` live). | [cc-skills], [best-practices], [prompting], [agentskills-desc], [changelog] |
| **A3** Hook gate | Feasible. A plugin ships `hooks/hooks.json` with a `PreToolUse` hook, regex matcher over tool names, exit 2 or JSON `permissionDecision: "deny"` to block, `"ask"` to put Aaron in the loop. Fails open unless `onFailure: "block"` (v2.1.295+). Windows PowerShell hooks failed open until v2.1.309–2.1.316. Lesson 11: a gate only works on a record the agent cannot write. | A gate on an agent-written "understood" file is advisory in practice. Gate on something the agent cannot forge: `"ask"` on the first write (Aaron approves the decision list), or a check of the transcript for Aaron's reply (inferred). Match both houseCARL tool-name forms. Needs a current Claude Code; the local `claude` CLI reported 2.1.280. | [hooks], [plugins-ref], [lesson11], [changelog], [steering] |
| **A4** Standards drift | Upstream is gone. houseCARL deleted its skill-authoring standard on 2026-09-05 ("predates houseCARL", #402) and its naming and doc-hygiene standards on 2026-09-04. Our copy equals houseCARL `e942910f` (2026-07-29), one line behind its last version. Against Anthropic: the §6 trigger method, pushy/negative-trigger rules, fixed section order, real-call-shape rule and Claude_MO2 archetypes conflict or are stale. | This repo now owns `standards/`. "Re-sync from upstream" can't happen. Revise the skill-authoring standard here, in the rebuild, against the list below. | houseCARL git history; [best-practices], [agentskills-bp], [prompting] |
| **A5** Evals | Evaluation-driven development: baseline without the skill first, write minimal content, measure pass rate, tokens and time, blind A/B between versions. Triggering is measured by **actual invocation**: ~20 queries (8–10 / 8–10 near-misses), 3 runs each, 0.5 rate threshold, 60/40 train/held-out split, ≤5 iterations, pick by held-out. `claude plugin eval` now does with/without runs and a `tool_used: Skill` grader. | Our control eval already is Anthropic's quality method; make it the binding gate. The §6.5 fan-out measures relevance, not invocation; add a real-invocation trigger test. Caveat: plugin-eval runs load no personal MCP servers or other plugins, so houseCARL must be mocked or loaded as a plugin under test. | [best-practices], [skill-creator], [agentskills-desc], [agentskills-eval], [plugin-evals], [sc-blog] |

---

## A1. Skill structure

**Size.**
- Keep the SKILL.md body under 500 lines; split into files when near it ([best-practices], [cc-skills]).
- The open spec adds under 5,000 tokens ([agentskills-bp]). The overview budgets the body at under 5k tokens ([overview]).
- The PDF guide says under 5,000 words, and flags 20–50+ enabled skills as a context problem ([guide-pdf]).

**Content.**
- "Claude is already very smart"; only add what it doesn't have ([best-practices]).
- Per line, ask whether the agent would get it wrong without it; if not, cut it ([agentskills-bp]).
- "Overly comprehensive skills can hurt" ([agentskills-bp]): instructions that don't apply get followed anyway.
- Gotchas are the highest-value content: facts that defy reasonable assumptions. Keep them in SKILL.md, not a reference the agent may not open ([agentskills-bp]).
- Provide a default, not a menu ([best-practices], [agentskills-bp]).
- Favour procedures over declarations: teach the method for a class of problems, not one answer ([agentskills-bp]). That is "which comparable to read", not "set X to 15".
- Avoid time-sensitive text; move it to an "old patterns" section ([best-practices]).
- Skills persist in context once loaded, so every line is a recurring token cost ([cc-skills]).

**Rules vs judgement.**
- Match freedom to fragility: high freedom where context decides, low freedom only for fragile sequences ([best-practices]).
- "Explain the why"; all-caps ALWAYS/NEVER is a "yellow flag" ([skill-creator]; same in [agentskills-eval]).
- Current models are "more responsive to the system prompt" and overtrigger on aggressive wording ([prompting]).
- Sonnet 5 follows instructions literally and doesn't generalise across items; state scope explicitly ([prompting-sonnet5]). An unscoped absolute is applied exactly as written (inferred: the distillation's out-of-scope absolutes).
- One Anthropic source still recommends `CRITICAL:` headers for buried instructions ([guide-pdf]). It is the older, outlier position.

**Progressive disclosure.**
- References one level deep from SKILL.md. Deeper chains get partially read, e.g. `head -100` ([best-practices]).
- Table of contents for references over 100 lines ([best-practices]); skill-creator says over 300 ([skill-creator]).
- Say *when* to load each file, e.g. "read X if the API returns non-200" ([agentskills-bp]).
- After compaction only the first 5,000 tokens of each invoked skill are kept, within 25,000 tokens shared ([cc-skills]). Put the most important instructions at the top ([cc-skills]).

**Split or merge.**
- Organise by domain so irrelevant context stays unloaded ([best-practices]).
- Keep mutually exclusive contexts on separate paths ([eng-blog]).
- Skills scoped too narrowly "force multiple skills to load" and risk "conflicting instructions"; too broad are hard to trigger ([agentskills-bp]).
- Inferred for intent open question 6: the distillation's shape (router + Requiem profile + ARR layer + 7 domain skills) should become **fewer skills, more references**. The profile and ARR layer are read on every job, so they belong to the router: as its top section, or as references it always reads at plan time. Domain methods can be references of the router or slim skills. Make each a skill only where it must trigger on its own (C3). The cross-skill contradictions the distillation found are the "too narrow" failure Anthropic names.

**Scripts.**
- Prefer scripts for deterministic operations; only their output enters context ([best-practices], [overview]).
- For critical validations, bundle a script: "Code is deterministic" ([guide-pdf]).
- Use plan-validate-execute (structured plan file, validate against a source of truth, then execute) for batch or destructive work ([best-practices], [agentskills-bp]). That is close to the intent's understand → decision list → execute flow (inferred).
- Opus 5.5 "tends to get to work quickly". On loose tasks, tell it to explore the relevant sources before acting ([prompting-opus55]). That supports a short "understand the whole mod first" step.

**Models.**
- Test with every model you plan to use ([best-practices]).
- A skill whose techniques the base model already passes is "no longer necessary" ([sc-blog]). That is the 86% finding.

## A2. Descriptions and triggering

**Wording.**
- Say what the skill does and when to use it, with specific key terms ([best-practices], [overview]).
- Third person ([best-practices]); the open-standard guide prefers imperative "Use this skill when…" ([agentskills-desc]). Minor tension; either works.
- Lead with the key use case; listing text gets truncated ([cc-skills]).
- Under-triggering fix: more detail and key terms. Over-triggering fix: negative triggers ("Do NOT use for…") and narrower scope ([guide-pdf]); state what the skill does not do ([agentskills-desc]).
- skill-creator still says make descriptions "a little bit pushy" against undertriggering ([skill-creator]).
- The prompting guide says the opposite for current models: soften `CRITICAL: You MUST use this tool` to "Use this tool when…" ([prompting]).
- Claude consults skills only for tasks it can't easily do alone; simple one-step asks may not trigger ([skill-creator], [agentskills-desc]).

**Length and the listing (Claude Code).**
- Spec/API: description ≤1,024 chars, no XML tags; `name` ≤64 chars ([best-practices]).
- Claude Code listing: description + `when_to_use` capped at 1,536 chars ([cc-skills]). The cap is configurable with `skillListingMaxDescChars` ([cc-skills]).
- The listing budget is 1% of the context window. On overflow, descriptions are dropped starting with the least-invoked skills; names always stay ([cc-skills]).
- Levers: `skillListingBudgetFraction`, `SLASH_COMMAND_TOOL_CHAR_BUDGET`, `skillOverrides: "name-only"`. `skillOverrides` does not apply to plugin skills ([cc-skills]).
- Current Opus 5.5 and Sonnet 5.5 have 1M-token windows ([models]). If Claude Code runs them at 1M, the budget is ~10k tokens, not the ~2,000 the standard assumes (inferred; `/doctor` reports the live figure, [cc-skills]).

**What changed since 2026-07-29** (v2.1.221 shipped 2026-08-04; v2.1.220 on 2026-07-25; GitHub release dates). From [changelog]:
- Listing mechanics: **no change**. The 1,536 cap dates from v2.1.105 and `skillOverrides` from v2.1.129, both before #43.
- v2.1.269: `claude plugin eval`, with a `tool_used: Skill` grader ([plugin-evals]).
- v2.1.261: `/skill-doctor`, showing per-skill context cost and unused skills (the docs say v2.1.252, [cc-skills]).
- v2.1.283: `/doctor prompt-audit`, flagging "outdated or conflicting instructions" ([cc-skills]). `/claude-api prompt-audit` (v2.1.221) flags instructions "written for older models".
- v2.1.290: the listing shows both names when the folder and `name:` differ.
- v2.1.295: subagents preload at most 32 skills.
- v2.1.304: compaction resends the skill list. Skill hooks are capped per source at 1,024 hooks and 16,384 matcher characters.
- v2.1.312: a fix for `paths:` skills after `cd`.

## A3. A plugin hook that gates houseCARL writes

**Mechanics** ([hooks], [plugins-ref]).
- **Where.** `hooks/hooks.json` at the plugin root, with a top-level `"hooks"` wrapper. Or a `hooks` key in `plugin.json` (path, inline object, or array); it merges with the default file.
- **Event.** `PreToolUse`. Its stdin carries `tool_name`, `tool_input`, `tool_use_id`, `cwd`, `permission_mode` and `transcript_path`, plus `agent_id` / `agent_type` in subagents.
- **Matcher.** Letters, digits, `_`, `-` and `|` mean an exact name or list. Any other character makes it a JavaScript regex, **unanchored**. Use `^…$`.
- **MCP tool names.**
  - A user or project server is `mcp__<server>__<tool>`.
  - A plugin-bundled server is `mcp__plugin_<plugin>_<server>__<tool>`.
  - houseCARL ships both ways: its plugin carries `.mcp.json` with server `housecarl`, and it is also registered as a user MCP. The eval harness registers it at project level.
  - So the matcher must cover `mcp__housecarl__housecarl_*` and `mcp__plugin_housecarl_housecarl__housecarl_*` (inferred from the naming rules plus houseCARL's plugin files). A wrong name never matches and silently fails open.
- **Which tools.** houseCARL tags its read tools `ReadOnly = true` (records, check, load_order_status, nif_inspect, skse, skypatcher_layer, asset_status, bsa_list, nexus_*, update_status). Gate the rest by name: apply, copy, create, create_plugin, forward, place, remove, write_seq, merge_plugins, compact_plugin, nif_set, bsa_extract/repack, compile/decompile_script, set_mo2_instance, set_tool_path. This comes from the houseCARL source; derive the list from `ToolNames.cs` at spec time.
- **Handler.** `type: "command"` (also `http`, `mcp_tool`, `prompt`, `agent`).
  - `timeout` defaults to 600 s for command hooks.
  - `shell` is `"bash"` by default, `"powershell"` on Windows without Git Bash.
  - Exec form (`args`) avoids quoting. Use `"${CLAUDE_PLUGIN_ROOT}"` for script paths.
  - Hook processes get `CLAUDE_PLUGIN_ROOT`, `CLAUDE_PLUGIN_DATA` (persists across updates) and `CLAUDE_PROJECT_DIR` ([plugins-ref]).
- **Decisions.**
  - Exit 0 with a JSON `hookSpecificOutput` (`hookEventName`, `permissionDecision` = `allow` / `deny` / `ask` / `defer`, `permissionDecisionReason`, optional `updatedInput`, `additionalContext`).
  - Exit 2 blocks, with stderr as the reason, and JSON cannot override it.
  - Any other exit code lets the call through.
  - Deny reasons are shown to Claude; ask reasons are shown to the user.
  - Across hooks, `deny` beats `defer`, `ask` and `allow`. Deny and ask permission rules still apply whatever a hook returns.
- **Fail-open default.**
  - A timeout, crash or bad exit continues the call unless `onFailure: "block"` (v2.1.295+).
  - With `block`, failures to start, odd exit codes, timeouts and invalid JSON all block.
  - On Windows without Git Bash, a non-0/2 exit is treated as a failure.
- **Limits.**
  - Output is capped at 10,000 chars ([hooks]).
  - `defer` works only under `-p`.
  - In non-interactive runs an `ask` is denied, since nobody can answer ([lesson11]).
  - Users can disable hooks via `/plugin`, `disableAllHooks`, or managed `allowManagedHooksOnly` ([hooks]).
  - Plugin hooks also fire in subagents ([hooks]).
- **Skill-frontmatter hooks.** They register only when the skill is invoked ([cc-skills]). They miss the case a gate exists for: a write made without the skill loaded (A7 already noted this).

**Windows / version facts** ([changelog]).
- v2.1.299: no-shell PreToolUse hooks failed open without Git for Windows; fixed.
- v2.1.309: PowerShell hooks did not block on exit 2; fixed.
- v2.1.310: hooks whose command was only quoted strings failed open; fixed.
- v2.1.316 (2026-10-31): PowerShell exit-2 failures when the script starts with a type cast or `param`; fixed.
- The `claude` CLI on this machine reported **2.1.280** during recon. That is older than `onFailure`. The desktop app's bundled build may differ; not checked.

**The design problem** ([lesson11], [steering]).
- Anthropic treats skills and CLAUDE.md as followed "most of the time". Hooks are the deterministic tool for rules that must hold ([steering]); the skills page says the same ([cc-skills]).
- Lesson 11 states the condition: the gate depends on "the agent having no way to write" the records it reads ([lesson11]).
- An "I understood the mod" file written by the agent fails that test. It can only check that the file exists and has the right shape (inferred).
- Options that pass the test (inferred):
  - **`ask` on the first gated write of the session.** Aaron sees the decision list and approves in the permission prompt. Cheap, honest. Interactive only, and denied in `-p` / evals.
  - **Transcript check.** The hook reads `transcript_path` and allows writes only after a user turn that follows the decision list. The agent cannot forge user turns. More code; test it hard.
  - **Ask-then-remember.** After the first approved `ask`, the hook records the approval in `CLAUDE_PLUGIN_DATA` and allows later writes. The hook writes that state, not the agent.
- `additionalContext` on the first read can also inject a one-line reminder of the plan step. It is cheap and advisory.

**With A7.**
- The current mirror is a skills-directory plugin, so `hooks/hooks.json` and `${CLAUDE_PLUGIN_ROOT}` resolve in place (A7).
- `claude plugin eval` accepts skills-directory plugins (`name@skills-dir`) and loads plugin hooks in runs ([plugin-evals]), so the gate can be tested headlessly.
- Plugin `dependencies` can require another *plugin* to be enabled ([plugins-ref]). Declaring `housecarl` there would enforce that houseCARL is present (inferred; it needs both in a resolvable marketplace).
- Observation: during this recon, no `authoria-requiem` folder was present in the user skills directory, and no `authoria-requiem:` skills were listed. Likely deliberate for the eval; not investigated.

## A4. `standards/` drift

**Against houseCARL HEAD** (pulled 2026-10-31, read-only).
- `HOUSECARL_SKILL_AUTHORING.md` was **deleted** in `912a3663` (2026-09-05). The commit message gives the reason: it "predates houseCARL" and would "anchor" a ground-up skill-surface audit (#402).
- `HOUSECARL_NAMING.md` and `HOUSECARL_DOC_HYGIENE.md` were deleted in `c3ca8f68` (2026-09-04). They were replaced by a half-page `NAMING.md` and a one-page `TESTING.md`, written against "what a reasonable senior engineer would set up".
- Our `HOUSECARL_SKILL_AUTHORING.md` equals houseCARL `e942910f` (2026-07-29). houseCARL's last version differs by one line (the §4.4 tool-name example).
- Our `HOUSECARL_NAMING.md` equals houseCARL's pre-deletion copy.
- Our `HOUSECARL_DOC_HYGIENE.md` is older than houseCARL's last copy. It lacks the decision-record addendum exception and the "one home per fact" section.
- **Result:** there is no upstream any more. The repo CLAUDE.md's "re-sync these copies if they change there" is dead.

**Against Anthropic:** see the next section.

## A5. Evals and trigger measurement

**Quality evals.**
- Build evals before writing much: run Claude without the skill, record the failures, write minimal instructions, compare to the baseline ([best-practices]).
- At least three evals; test on real usage ([best-practices]).
- Run with and without the skill (or against the previous version) in clean contexts. Record tokens and time, because a skill that triples tokens for a small gain may not pay ([agentskills-eval]).
- Drop assertions that pass in both arms; study the ones that pass only with the skill ([agentskills-eval]).
- "If pass rates plateau despite adding more rules", try removing instructions ([agentskills-eval]).
- skill-creator automates this: subagent per case, grader, benchmark, blind comparator, HTML review ([skill-creator], [sc-blog]).
- Our control eval (blinded, judged, cost-tracked, with vs without) is this method at larger scale (inferred).

**Trigger evals.**
- About 20 queries, 8–10 should-trigger and 8–10 near-miss should-not ([skill-creator], [agentskills-desc]).
- Queries must be realistic and substantive, since simple asks don't trigger skills at all ([skill-creator]).
- Run each 3 times and compute a trigger rate; a query passes at above 0.5 ([agentskills-desc]).
- Split 60/40 train / held-out, iterate ≤5 times, and pick the description by held-out score. Then confirm with 5–10 fresh queries ([agentskills-desc], [skill-creator]).
- The detection is real invocation: the `Skill` tool call in `claude -p` JSON output ([agentskills-desc]).
- The PDF guide targets triggering on 90% of relevant queries, from 10–20 tests ([guide-pdf]).

**`claude plugin eval`** (v2.1.269+, [plugin-evals]).
- Each case is a prompt plus graders: `regex`, `tool_used`, `tool_order`, `file_exists`, `llm`, `baseline`.
- 3 runs per case by default, plus a no-plugin arm; it reports WITH, W/OUT and Δ.
- `tool_used: Skill` reports whether the skill fired. It is excluded from the score in two-arm runs.
- Isolation: no user settings, MCP servers, other plugins or skills load. The plugin's own MCP servers are mocked unless real ones are allowed.
- Inferred consequences: houseCARL (a user MCP) is absent unless mocked or loaded as a second plugin under test (the `plugins` case field takes directories). Competing houseCARL skills are absent from the listing unless loaded the same way.
- Native Windows has no sandbox for Bash grants; use WSL2.

**Our §6.5 fan-out vs this.**
- The fan-out asks fresh agents whether a query fits an *anonymised capability statement*. It measures relevance, not whether Claude picks the skill from the real listing next to houseCARL's skills.
- §6.4 rejected `run_loop` because `claude -p` answered directly. Anthropic documents exactly that, and its answer is substantive queries, not a different measure ([skill-creator], [agentskills-desc]).
- The ARR patching prompts are multi-step and specialised, the case Anthropic says reliably triggers (inferred).

## Conflicts with our standards

Sections are in `standards/HOUSECARL_SKILL_AUTHORING.md` unless named.

1. **§3.3 pushy tail and §3.4 ban on negative-gated triggers.**
   - Anthropic: soften aggressive wording on current models; they overtrigger ([prompting]).
   - Fix overtriggering with negative triggers and scope ([guide-pdf], [agentskills-desc]).
   - skill-creator still says "a little bit pushy" ([skill-creator]). Net: pushy is allowed, but a ban on "not for X" contradicts current guidance.
2. **§6.1 / §6.4 / §6.5 trigger method.**
   - Anthropic measures actual invocation, 3 runs per query, a 60/40 held-out split, and best-of-held-out selection ([agentskills-desc], [skill-creator]).
   - Claude Code now ships `claude plugin eval` with `tool_used: Skill` ([plugin-evals]).
   - Our fan-out measures anonymised relevance only.
3. **§6.3 thresholds.** Recall ≥80% / specificity ≥50% vs a per-query 0.5 trigger rate ([agentskills-desc]) and a 90% recall target ([guide-pdf]). Specificity is not optional for Anthropic: near-misses are "the most valuable" ([agentskills-desc]).
4. **§6 has no quality gate with a no-skill baseline** (§6.6 covers only investigation-flow skills). Anthropic makes the with/without baseline and token cost the core loop ([best-practices], [agentskills-eval], [plugin-evals]).
5. **§4.2 fixed section order** (Overview, First step, …, Common mistakes, Checklist, Real example, Notes).
   - Anthropic sets no order. It asks for moderate detail, gotchas and only what is needed ([agentskills-bp]); mandatory slots invite padding (inferred).
   - The distillation found checklists ×11 and examples (~620 lines) were dead weight.
6. **§4.5 "show the real call shape".**
   - Anthropic: favour procedures over declarations ([agentskills-bp]). Don't restate the tool surface; tools carry their own descriptions (also our §4.5 row 3).
   - The intent ships no tool recipes (HU-T6).
7. **§4.3 exception for bolded warnings.** Fine, but the current prompting guide says even "CRITICAL / MUST" overtriggers ([prompting]); the distillation found absolutes harmful. Scope every absolute with its counter-case ([prompting-sonnet5]).
8. **§3.5 listing-budget arithmetic.** It assumes ~2,000 tokens on a 200k window. Current models have 1M ([models]), so the 1% budget is larger (inferred). The char band is still sound. Re-measure with `/doctor` or `/skill-doctor` ([cc-skills]).
9. **§5.1 / §5.2 archetypes and §1 / §7 Claude_MO2 history.** These are pre-houseCARL scaffolding, and houseCARL deleted the standard for that reason (#402). Anthropic's unit is the "coherent unit" ([agentskills-bp]).
10. **§1 / §8 skill-creator pointer.** It cites an old local install path and quotes skill-creator by line number. skill-creator now ships as a plugin in the official marketplace and in `anthropics/skills` ([cc-skills], [skill-creator]); line references rot.
11. **Missing from the standard** (all in Anthropic guidance):
    - hooks for rules that must hold ([cc-skills], [steering]);
    - scripts for deterministic validation ([guide-pdf], [best-practices]);
    - gotchas section ([agentskills-bp]);
    - defaults, not menus ([best-practices]);
    - important content first, because of compaction ([cc-skills]);
    - test on every target model ([best-practices]);
    - "add only what Claude lacks" as the per-line test ([agentskills-bp]).
12. **CLAUDE.md "re-sync from houseCARL".** The upstream no longer exists (A4).
13. **`HOUSECARL_NAMING.md`.** No conflict. Anthropic prefers gerunds but accepts noun phrases ([best-practices]).

## Implications for the spec

1. Treat `standards/` as owned here. Revise `HOUSECARL_SKILL_AUTHORING.md` in this project (or a sibling PR) against the conflict list. Drop the re-sync line from CLAUDE.md.
2. Per-line test for every kept line: would Opus or Sonnet get this wrong without it? Back it with the control-eval evidence.
3. Keep each SKILL.md well under 500 lines. The router carries the plan and the boundary facts in its first screen, so they survive compaction.
4. Prefer fewer, coherent skills. Fold the Requiem profile and ARR layer into the router (top section or always-read references). Make a domain method a separate skill only if it must trigger alone; measure that.
5. Write gotchas, not rules. Scope each absolute with its counter-case. No all-caps. Give a default plus the escape hatch.
6. Name *which comparable to read* (a procedure), not values, except verified facts with probes (C6).
7. Descriptions: use case first, 200–400 chars, distinctive ARR/Requiem nouns. A short "not for…" clause is allowed where near-misses exist. No aggressive wording.
8. Gate writes with a plugin `PreToolUse` hook only if it checks something the agent cannot write:
   - default: `ask` on the first gated houseCARL write, then ask-then-remember;
   - the matcher covers both tool-name forms and is anchored;
   - `onFailure: "block"`;
   - require a Claude Code version ≥2.1.316 on Windows.
9. Test the gate with a deliberate deny, under PowerShell and Bash, with `claude plugin validate --strict`, and once via `claude plugin eval`.
10. Make the control-eval re-run (with vs without, cost per right change) the binding quality gate (C7). Add the plan + boundary-fact arm as the "would less do?" check.
11. Replace or supplement the §6.5 fan-out with a real-invocation trigger test:
    - 20 substantive ARR queries, 3 runs each, 60/40 split;
    - via `claude plugin eval` (`tool_used: Skill`, houseCARL mocked or loaded) or skill-creator's loop;
    - keep the fan-out as a cheap pre-check.
12. Run both gates on Opus and Sonnet (C4); Sonnet's literal reading is where unscoped rules bite.
13. Run `/doctor prompt-audit` (or `/claude-api prompt-audit`) over the rebuilt skills before review, as a cheap check for older-model patterns.

## Sources

- [best-practices]: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
- [overview]: https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- [cc-skills]: https://code.claude.com/docs/en/skills
- [hooks]: https://code.claude.com/docs/en/hooks
- [plugins-ref]: https://code.claude.com/docs/en/plugins-reference (the plugin manifest reference)
- [plugin-evals]: https://code.claude.com/docs/en/plugin-evals
- [changelog]: https://code.claude.com/docs/en/changelog and https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md; release dates from the GitHub releases API
- [eng-blog]: https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills (2025-10-16)
- [sc-blog]: https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills (2026-03-03)
- [steering]: https://claude.com/resources/articles/steering-claude-code-skills-hooks-rules-subagents-and-more (2026-06-18)
- [guide-pdf]: https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf (Jan 2026; landing page https://claude.com/blog/complete-guide-to-building-skills-for-claude)
- [skill-creator]: https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md (the installed copy in the official plugin marketplace matches on the points cited)
- [agentskills-bp]: https://agentskills.io/skill-creation/best-practices
- [agentskills-desc]: https://agentskills.io/skill-creation/optimizing-descriptions
- [agentskills-eval]: https://agentskills.io/skill-creation/evaluating-skills
- [prompting]: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices
- [prompting-sonnet5]: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5
- [prompting-opus55]: https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5-5
- [models]: https://platform.claude.com/docs/en/about-claude/models/overview
- [lesson11]: https://academy.claude.com/courses/ai-native-sdlc-playbook/hooks-as-approval-gates
- houseCARL git history (local clone, read-only): commits `912a3663`, `c3ca8f68`, `e942910f`; issue Avick3110/houseCARL#402
