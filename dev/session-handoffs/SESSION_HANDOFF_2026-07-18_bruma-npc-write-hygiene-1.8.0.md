# Session handoff — 2026-07-18: Bruma NPC write hygiene (1.8.0)

*Author: Heisen (+ Codex session) · 2026-07-18*

*Class: ARCHIVE (frozen from first write). Supersedes
`SESSION_HANDOFF_2026-07-18_spell-coverage-1.7.0.md` as the latest "where are we."*

## Outcome

**`authoria-requiem` 1.8.0 release candidate.** The Bruma whole-mod pass exposed malformed or
unnecessary NPC/COBJ overrides after the patch had already been manually corrected. The skill layer
now makes inherited balance templates no-write skips, uses one user-selected stat authority for the
whole plugin, emits sparse NPC fields/lists with exact flag deltas, and disables COBJ recipes through
`REQ_DisableRecipe`. No existing load-order patch was changed.

Drivers: [#38](https://github.com/Avick3110/authoria-requiem-skills/issues/38) and
[#39](https://github.com/Avick3110/authoria-requiem-skills/issues/39).

## Decisions locked

1. **Template inheritance wins.** A resolved NPC template chain inheriting the relevant stats and
   spell-list balance is a complete no-write skip. Masked local NULL perks/spells do not justify an
   override.
2. **Stat authority is plugin-wide.** Ask once: AutoCalc means the flag is on and no explicit
   DNAM/ACBS stat fields are written; manual means the flag is off and the complete derived block is
   written. Templated skips remain untouched; followers still retain `PcLevelMult`.
3. **Absence is serialized as absence.** Nullable FormLinks use Remove, not null. Empty perk/effect
   results have no list or count subrecord. A surviving `PRKZ = 0` or `SPCT = 0` is an incomplete tool
   write and must not ship.
4. **Flags are exact deltas.** Preserve every unrelated winner bit. Never copy
   `LoopedScript`/`LoopedAudio` merely because an analogue carries them.
5. **Disabled recipes use the Requiem keyword.** `WorkbenchKeyword =
   AD3B01:Requiem.esp` (`REQ_DisableRecipe`), never null or removed.

## Evidence

- NPC examples: `CYRThalmorAdjutant 065092:BSHeartland.esm`,
  `CYREncPenitus00Template 0AB49F:BSHeartland.esm`,
  `CYREncDremoraTemplate 0DDB2A:BSHeartland.esm`, and
  `CYRHannIrving 0F96D6:BSHeartland.esm`.
- COBJ example: `CYRTemperWeaponAyleidBattleAxe 05F07D:BSHeartland.esm`; the manually corrected
  winner resolves its workbench as `REQ_DisableRecipe AD3B01:Requiem.esp`.
- The current 1.7.0 initial-triage wording was audited: skip requires positive evidence, so the
  earlier blanket "Requiem-shaped" assumption was already fixed before this release.

## Validation

- All manifests and JSONL files parse.
- Repository CI-equivalent gates pass: all names match folders, descriptions stay below the cap,
  all skill bodies stay at or below 500 lines, and added-line publish hygiene is clean.
- `requiem-npc-patching/SKILL.md` is exactly 500 lines; further detail belongs in references.
- `git diff --check` passes. No skill `description:` changed, so no trigger fan-out was required.
- The installed Claude CLI no longer accepts the repository's documented `--strict` option and its
  non-strict validator did not return; the mechanical checks above match the repository CI gate.

## Open / next

- Rebase on current `main`, commit, push `Codex/npc-write-hygiene`, and open the issue-linked PR.
- Maintainer merge approval remains a separate gate. After merge, mirror the released plugin to the
  live skill install and restart the host before the next NPC pass.
