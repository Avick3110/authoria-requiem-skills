# A7 — Install path vs plugin hooks

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, target A7. Source: a docs-research subagent reading
code.claude.com on 2026-10-30 (URLs below). The agent cited the docs; nothing here was tested
locally yet. Treat it as a lead until the deny test below passes.*

## Answer

**Hooks would load on the current install.** The current sync mirrors the plugin root
`authoria-requiem/` (which holds `.claude-plugin/plugin.json`) into the user skills directory.
The docs call a folder there with `plugin.json` at its root a *skills-directory plugin*:
- it loads in place;
- it keeps all its components, including `hooks/hooks.json`;
- skills load as `/authoria-requiem:<skill>`.

([plugins/loading](https://code.claude.com/docs/en/plugins/loading.md))

Caveats:
- Managed `strictPluginOnlyCustomization` / `strictKnownMarketplaces` silently skip the whole
  folder.
- A same-name marketplace install shadows it.
- Without `plugin.json`, a nested `skills/<skill>/` layout is undocumented.

## Install paths

| Path | Loads | Updates |
|---|---|---|
| Skills-directory plugin (today's mirror) | In place | Re-mirror; stale-copy drift risk (the repo CLAUDE.md's step-0 problem) |
| `--plugin-dir` | In place, session only | Live; dev use |
| Local-path marketplace (`claude plugin marketplace add <repo>` + `install`) | In place from source | Live at next session start / `/reload-plugins`; no version bump |
| Remote / GitHub marketplace | Copied to the plugin cache | Needs a version bump |

([create-marketplace](https://code.claude.com/docs/en/plugins/create-marketplace.md))

## Hook mechanics (for the open-question-2 gate)

([hooks](https://code.claude.com/docs/en/hooks.md),
[manifest-reference](https://code.claude.com/docs/en/plugins/manifest-reference.md))

- **Where it lives:** `hooks/hooks.json` inside the plugin, or `"hooks"` in `plugin.json`.
- **Matcher:** a regex over the tool name, unanchored. Anchor it:
  `^mcp__housecarl__housecarl_(apply|create|…)$`.
- **Denying a call:** two forms, both shown to Claude.
  - Stdout JSON with `hookSpecificOutput.permissionDecision: "deny"` plus a
    `permissionDecisionReason`.
  - Or exit 2, with the reason on stderr.
- **Fails open by default.** Any other non-zero exit lets the call through, and so does a
  timeout. A gate needs `"onFailure": "block"`, and its script must exit 2 on its own errors.
- **What the hook script gets:**
  - env: `CLAUDE_PLUGIN_ROOT`, `CLAUDE_PROJECT_DIR`, `CLAUDE_PLUGIN_DATA`;
  - stdin: `tool_name`, `tool_input`, `cwd`, `agent_id`.
- **Windows:** use `"shell": "powershell"`, or exec form.
- **Subagents:** plugin hooks fire there too, so lanes are gated as well.
- **Users can switch it off:** through `/plugin` / `enabledPlugins`, `disableAllHooks`, or the
  managed `allowManagedHooksOnly`.

## Alternatives

| Option | Scope | Verdict |
|---|---|---|
| Skill-frontmatter `hooks:` | Registers when the skill is invoked and persists for the session | Doesn't gate writes made without the skill loaded, which is the very case a gate is for |
| Project `.claude/settings.json` hooks | Everyone who clones the project; absolute script paths | Wrong scope for a plugin |

## Implications for the spec

1. A plugin-shipped gate is feasible on the current install path. A7's worry (hooks never
   firing) does not hold, provided `plugin.json` stays at the mirrored root.
2. A **local-path marketplace** for maintainers is worth considering. It reads live from the repo
   and removes the stale-install drift. That is an install-convention change, so it's Aaron's
   call and outside this rebuild unless he adds it.
3. **The gate must fail closed.** Otherwise a broken script silently disables it.
4. **Still to verify** before anything relies on this:
   - `claude plugin validate --strict` with a `hooks/hooks.json`;
   - a deliberate-deny test on a test profile;
   - that `onFailure` behaves as documented under PowerShell.
