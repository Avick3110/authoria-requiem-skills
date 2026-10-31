# A8 / A9: Codex compatibility and distribution (desk pass)

*Author: Aaron (+ Claude session) · 2026-10-31*

*Class: ARCHIVE (finding). Recon pass 2, targets A8 and A9 (added 2026-10-31). A desk synthesis of houseCARL's
research, not yet re-verified live. Sources:*
- *houseCARL's verified docs corpus `dev/references/skills-docs-2026-09-05/` (local-only, gitignored in houseCARL),
  fetched 2026-09-05. Every claim there was re-fetched by a verifier session.*
- *houseCARL's shipped code: `plugin/codex/`, `scripts/build-plugin.ps1`, `src/housecarl-setup/`, the CI tests, and
  the CHANGELOG.*

*Docs URLs are given where the corpus gives them. **[inference]** marks our own reading.*

## Why

The rebuild targeted Claude Code only (intent C2, C4). Aaron, 2026-10-31: the pack must also work in **OpenAI Codex
on GPT models**, and ship as a release users can install by hand or through their agent. houseCARL 2.0 already ships
to both hosts, so it is the starting point.

## Distribution (A9)

- **Claude Code.** A repo with `.claude-plugin/marketplace.json` (this repo has one), then `/plugin marketplace add
  owner/repo`, then `/plugin install`.
  - Updates follow the `plugin.json` version.
  - Tags: `{plugin}--v{version}`.
  - Auto-update is off by default for third-party marketplaces.
  - Zip routes: `--plugin-dir` (takes a `.zip`) and `archive` sources with SHA-256 pinning.
  - (code.claude.com plugins-reference, discover-plugins)
- **Codex has plugins and marketplaces.**
  - The manifest is `.codex-plugin/plugin.json`, with `skills/`, `.mcp.json` and `hooks/` at the plugin root.
  - `version` must be semver and must change on each release.
  - Marketplaces are read from `.agents/plugins/marketplace.json`, from a "legacy-compatible"
    `.claude-plugin/marketplace.json`, and from `~/.agents/plugins/marketplace.json`.
  - CLI: `codex plugin marketplace add owner/repo`. Installs are cached per version.
  - The IDE extension doesn't support plugins; standalone skills work there.
  - (developers.openai.com/plugins/build/plugins; learn.chatgpt.com/docs/plugins)
- **A Claude plugin in Codex works only partly as-is.**
  - Portal submission converts `.claude-plugin/plugin.json` and requires skills at `skills/<name>/SKILL.md`.
  - Dropped or refused: `marketplace.json`, `commands/`, `agents/`, `userConfig`, output styles, and Claude-only
    settings.
  - Local stdio MCP is refused at submission, but works for a local install.
  - A local `/import` maps skills to skills.
  - (developers.openai.com/plugins/guides/submit-claude-plugin; learn.chatgpt.com/docs/import)
- **Agent-driven install.** Codex ships `$skill-installer`, but OpenAI prefers plugins for distribution. The corpus
  has nothing on Claude installing a skill for the user, beyond `/plugin marketplace add`.
- **How houseCARL distributes.**
  - A release zip carrying the Claude plugin tree, a Codex tree under `codex/skills/`, and `houseCARL-Setup.exe`.
  - Setup copies skills flat into `~/.agents/skills/` for Codex, recording and pruning only its own folders because
    that directory is shared.
  - It registers `[mcp_servers.housecarl]` in `~/.codex/config.toml`.
  - It needs setup.exe because it ships a server.
  - **[inference]** This pack ships Markdown only, so the marketplace route plus a release zip may be enough, with no
    installer.
- **Dependency.** A Codex user needs houseCARL's Codex install (its setup's Codex path), which is houseCARL's job.

## Authoring for both hosts (A8)

- **Frontmatter.** Both hosts read only `name` and `description`.
  - Codex requires both, non-empty. The description is ≤ 1,024 characters.
  - The agentskills.io spec, which OpenAI defers to: `name` is ≤ 64 characters, lowercase and hyphens, and equals the
    folder name.
  - Codex is silent on every other key. Claude-only fields (`allowed-tools`, `disable-model-invocation`,
    `when_to_use`, `paths`, …) don't carry over.
  - The Codex-only sidecar `agents/openai.yaml` sets the display name, the invocation policy and
    `dependencies.tools` (the MCP server).
- **Listing budget.**
  - Codex: about 2 % of context (8,000 characters when context size is unknown). On overflow it shortens descriptions
    first and may omit skills, so front-load the trigger words.
  - Claude Code: 1 % of context, with a 1,536-character per-entry cap.
- **Invocation.**
  - Codex: implicit from the description, explicit with `$skill`. The opt-out is
    `policy.allow_implicit_invocation: false` in openai.yaml.
  - Claude Code's opt-out is the frontmatter field `disable-model-invocation`.
- **Naming other skills and tools.**
  - houseCARL's rule: MCP tools are written bare.
  - Siblings are written `plugin:skill` for Claude Code, plus one line saying Codex sees the bare folder name.
  - Codex's namespacing for plugin skills is undocumented.
- **Body style.** Both vendors agree on: imperative steps, a concise SKILL.md with detail one hop away, relative
  forward-slash paths, and no contradictions. Codex adds:
  - Provider-neutral language: "the model", not a vendor name.
  - State the inputs, the steps, the output, which facts the model must not infer, and when to ask, stop or decline.
  - GPT-6 Astra "can be more sensitive to instructions contained in skills", and unclear or conflicting guidance may
    make it block early. OpenAI recommends the line "The user's instructions take precedence over guidelines
    provided in a skill" and warns against unsolicited approval flows.
  - (developers.openai.com/plugins/build/skills; api/docs/guides/latest-model)
- **The vendors differ on:** explaining the why (Anthropic's skill-creator yes, Claude Code docs no, OpenAI silent),
  all-caps (Anthropic contradicts itself), and negatives (OpenAI writes prohibitions; Anthropic says state what to
  do).
- **Evals.**
  - Codex documents a manual five-case matrix only: direct, indirect, incomplete input, should-not-trigger, edge. It
    has no trigger-rate harness and no validate command.
  - **houseCARL never ran a skill on Codex or GPT.** Its 2026-09 evals graded skills against the docs, using Opus.

## houseCARL's lessons (its CHANGELOG)

- Its Codex router taught 17 dead tool names, because a guard checked that live tools were listed, not that listed
  tools were live. The fix was to delete the catalogue and defer to the tool descriptions. (This rebuild already ships
  no tool recipes.)
- A Codex tree without a `skills/` root is refused.
- `claude plugin validate` never sees Codex-only files, so they need their own CI check.
- Upgrades left stale folders in the shared `~/.agents/skills`. Record what you install and prune only that.

## Gaps and re-checks

- **Silent in the corpus:**
  - Codex's handling of other frontmatter keys.
  - Which skills its trimming drops first.
  - Plugin-skill namespacing at invocation.
  - How a skill should spell MCP tool names.
  - Body-length guidance.
- **Contradictory in the corpus:**
  - The user skills path: `~/.agents/skills` vs `~/.codex/skills`.
  - `.claude-plugin/marketplace.json`: "don't rely on" vs "legacy-compatible".
  - The listing budget, stated in characters on one page and tokens on another.
  - Local MCP: works locally, refused at submission.
- **Stale:** the corpus is from 2026-09-05. Re-check every figure and path above against the live docs before the
  spec relies on it.

## Next (live pass)

1. Re-verify the claims above against the live Anthropic and OpenAI docs.
2. Prove the install: one repo, both manifests, `marketplace add` from scratch on each host, plus a release zip
   installed by hand.
3. A smoke test on Codex/GPT: a few trigger queries and one real patching task, with houseCARL's Codex install
   loaded.
