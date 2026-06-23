# authoria-requiem-skills

*Class: LIVING (the operating doc) — supersede, don't edit; keep it current. Per `standards/HOUSECARL_DOC_HYGIENE.md`.*

Source repo for the **`authoria-requiem`** Claude Code skill plugin — nine skills that patch
newly-added Skyrim SE mods to play correctly with **Requiem** by *live-analogy*: derive every stat,
keyword, recipe, perk, and placement by comparing new content against Requiem's own records (read live
through **houseCARL**), then emit a direct ESP override the **Reqtificator** integrates at build time —
never hardcode numbers derivable from a live comparable; carry the *inputs* the Reqtificator expects,
never hand-stamp its outputs.

> **Requires [houseCARL](https://github.com/Avick3110/houseCARL)** — the MCP that gives Claude
> data-layer access to the load order (reads return the live conflict winner; writes go to a new ESP).
> FormIDs are `XXXXXX:Plugin.esp`. Nothing here runs without it pointed at a Requiem MO2 instance.

**Before you analyse, edit, or create any skill, load the standards in [`standards/`](standards/) fully** —
they are binding and govern reviewing a submission as much as writing one (see [Authoring standards](#authoring-standards)).

## Repo map

```
README.md                          public overview + drag-drop install (into ~/.claude/skills/)
CHANGELOG.md                       version history (current: 1.0.1)
LICENSE                            MIT © DrHeisen
.claude-plugin/marketplace.json    marketplace manifest (optional plugin-install path)
authoria-requiem/                  THE PLUGIN — the shipped artifact
  .claude-plugin/plugin.json       manifest; the `version` lives here (bump on release)
  skills/README.md                 skills index
  skills/<skill>/
    SKILL.md                       the skill (frontmatter `name` == folder name)
    references/<topic>.md          live-mined rules + a grep-friendly index.jsonl
    evals/eval_set.json            archived trigger / near-miss eval set

standards/                         skill-authoring standards (synced from the houseCARL standards repo; binding)
  HOUSECARL_SKILL_AUTHORING.md     the binding standard — description format, body structure, evals, the §8 reviewer checklist
  HOUSECARL_NAMING.md              naming conventions (skill folders, files, docs)
  HOUSECARL_DOC_HYGIENE.md         LIVING vs ARCHIVE doc classes

dev/   (local-only, gitignored — NOT in the public repo)
  STATE.md                         cross-session build ledger — the build's source of truth
  PUBLIC-RELEASE-PLAN.md           public-release status + steps
  handoffs/phase-*.md              per-phase build handoffs — deepest domain context + every derived rule
  reqtificator-rules.md            the Reqtificator rulebook (how the build-time auto-balance pass behaves)
```

For the *why* behind a domain's rules, `dev/handoffs/phase-*.md` and `dev/STATE.md` are richer than the
shipped references. Public-facing facts live in `README.md`.

## The skills

Load **`requiem-patching`** first for a whole-mod job — it's the router / integration brain (enumerate
a plugin, route each record to a domain skill, own the cross-cutting systems, finish with the
masters / `REQ_NULL` / Reqtificator checklist). The domain skills also trigger directly:

| Skill | Covers |
|---|---|
| `requiem-patching` | router + gap mechanics (vampirism/lycanthropy, disease, exhaustion, stealth, alchemy, food, shouts, standing stones, perks, economy, combat/resistance) |
| `requiem-weapon-patching` | weapons (melee, bow/crossbow, staff frames) |
| `requiem-armor-patching` | armor (light/heavy, shields, clothing/jewelry) |
| `requiem-ammo-patching` | arrows & bolts |
| `requiem-race-patching` | playable & creature races |
| `requiem-npc-patching` | NPCs / enemies / followers |
| `requiem-leveled-list-patching` | leveled lists, containers, encounter zones |
| `requiem-magic-patching` | spells, effects, enchantments |
| `requiem-script-patching` | the Papyrus layer (most patches need no script) |

The cross-cutting **authority** and **masters / `REQ_NULL`** rules live in
`authoria-requiem/skills/requiem-patching/references/` (`scope-and-authority.md`,
`masters-and-null-stripping.md`). In short: the authority to derive from is houseCARL's **live conflict
winner** among the in-scope Requiem stack — one exception, `RACE` records resolve to
`Authoria - Master Patch - Races Merge.esp`.

## Authoring standards

`standards/` holds the binding skill-authoring standards (authored by Aaron; the houseCARL standards repo
is upstream — re-sync these copies if they change there). **Load them fully before you analyse, edit, or
create a skill — including before reviewing a submission.** Don't paraphrase them from memory; the rules
are exact and a reviewer renders a binary conforms / doesn't-conform verdict.

- **`HOUSECARL_SKILL_AUTHORING.md`** — the load-bearing one: action-first description format (§3), body
  structure and section order (§4), the tool-vs-skill archetypes (§5), eval-set rules and the recall ≥80% /
  specificity ≥50% gate with the §6.5 manual-prediction fallback (§6), and the **§8 19-item reviewer
  checklist** every skill must pass.
- **`HOUSECARL_NAMING.md`** — kebab-case skill folders, `name:` == folder name, no version or brand in names.
- **`HOUSECARL_DOC_HYGIENE.md`** — LIVING vs ARCHIVE doc classes; supersede don't edit; update LIVING docs in the same commit.

**Repo-specific deviations** (note these when reviewing against the §8 checklist):

- **§8 item 19 (the `$Skills` array in `scripts/build-plugin.ps1`) is N/A here** — this plugin has no build
  script; Claude Code auto-discovers each `SKILL.md` by directory. (It *does* apply in houseCARL.)
- Empirical eval validation (§6.1) needs a non-Windows host (`run_loop`'s `select()` is blocked on Windows,
  §6.4), so descriptions ship validated via the §6.5 manual-prediction fallback plus an independent
  peer-prediction check, and are flagged for empirical re-validation.

## Working on this repo

- **This repo is the source of truth.** The live install at `~/.claude/skills/authoria-requiem/` is a
  copy — after editing here, re-sync it and restart Claude Code to load changes.
- **Skills follow the standards in [`standards/`](standards/)** (see [Authoring standards](#authoring-standards)) —
  load them fully before authoring or reviewing. Match an existing skill (e.g. `requiem-weapon-patching`)
  as the working template.
- **Adding or reviewing a skill submission:** first confirm it belongs in *this* pack — a Requiem-patching
  skill. A general modding skill (e.g. OAR animation-config authoring) belongs in houseCARL, not here. Then
  walk it through the **§8 reviewer checklist** in `standards/HOUSECARL_SKILL_AUTHORING.md`, place it under
  `authoria-requiem/skills/<kebab-name>/`, list it in `skills/README.md` + the README table, and bump
  `plugin.json` `version` + add a `CHANGELOG.md` entry.
- **Validate** before release: `claude plugin validate --strict` (plugin + marketplace must pass).
- Platform: Windows / PowerShell. Commit only when asked; `dev/` is gitignored and must never be pushed.
