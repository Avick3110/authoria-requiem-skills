# Field-report round 3 — live mining notes (NPC gaps: kits, templates, mounts, ghosts)

*Author: Heisen (+ Claude session) · 2026-07-17*

*Class: ARCHIVE (frozen from first write). Evidence trail for the 1.5.0 rules; all reads live via
houseCARL against the ARR instance (winner-authoritative). FormIDs verified at read time.*

## Context

Heisen's field report on a live NPC/race patch of *Val Serano* (`Authoria - Reqtificated -
Val Serano.esp`, 13 reported records). Cross-checked against the repo timeline: the live skill
install was mirrored at 1.3.0's landing minute (install mtime 12:13:48, `cd5b2cf` committed
12:13:36 the same day), so the failing session ran on a **pre-1.3.0 install** — items already fixed
by 1.3.0 (DNAM block, empty-kit rule, positive-evidence skips) are not re-derived here. The five
loopholes below survive 1.3.0's text and were mined fresh.

## 1 — Populated-but-sparse source kits (report items 1, 2, 7, 9, 10, 11)

Reported records read live (winners now carry Heisen's manual fixes; the source versions show what
the session had in hand):

| Record | Source kit | Post-fix winner kit | Note |
|---|---|---|---|
| `AX_ValSerano 00AA0F` | 11 perks (mixed vanilla + 1 modded) | 15 | follower — `PCLevelMult` correctly kept |
| `AX_TarekSerano 019D29` | 3 perks | 9, fixed L40 | vanilla class `NPCclassBelrand`, no combat style, no tempering trait remain |
| `AX_Khovin 20A60C` | 2 perks | 14, fixed L14 | |
| `AX_JenaAvarix 20A607` | 0 perks, class `CombatWarrior1H`, `Unaggressive` AI | 7, fixed L20 | theme signal: named pirate crew |
| `AX_Elin 3EEF68` | 2 perks, class `Priest` + `csHumanMagic` + 6 `ActorEffect` spells | — | caster signals present |
| `AX_MBSummoner 329072` | 9 perks, `DarkElfRaceVampire`, templates `EncWarlock07TemplateBossConjurer` (SpellList, AttackData) | fixed L50 | vampire-lane analogue applies |

None of the sparse kits contained `REQ_NULL_*` entries — the "none of them are null so they look
fine" read is exactly the reported loophole. Adequacy bar = the analogue's tier kit (Requiem counts
already mined in `npc-fields.md`: bandit 5/9/12, guard ~17, vampire tier-2 30).

## 2 — Template chains ending in the mod's own actors (report items 3, 4)

- `AX_TarekSeranoSoul 0A5B86` — **override_depth=1: the patch never touched it.**
  `TemplateFlags = Traits, Stats, SpellList, Inventory`, `Template = AX_TarekSerano` (the mod's own
  record, itself shipped on `PCLevelMult 1`), plus 3 own perks + 3 own spells. The Workflow A
  "already templated → skip" fires on flags alone; the chain ends in an unpatched mod actor.
- `AX_SeranoMarooned 01EE2B` — source: no template, 6 perks, own stat block drifting from Val's.
  Heisen's fix (the prescription the rule now encodes): `Template → AX_ValSerano 00AA0F`,
  `TemplateFlags = Stats, SpellList`.
- Engine precedent for state copies: Dawnguard's Soul Cairn souls (`DLC01SoulCairnSoul*`,
  `DLC01SoulCairnJiub`, `DLC01SoulCairnKeeperSoul`) — duplicates carrying `ActorTypeGhost`,
  templated where the state allows.
- Template-`Stats` semantics: the flag inherits the level block from the base, so a `Stats`-templated
  actor's own `LevelMult` is inert — it still matches the query-1 drain check (the live arm is
  readable on the record), hence the explicit keeper category *after* a chain walk.

## 3 — Mounts (report item 12)

- `EncHorseSaddledBrown 023AB2:Skyrim.esm` (representative of the `EncHorse*` family): fixed
  **level 4**, `AutoCalcStats` ON, DNAM H 289 / S 106, flags `Respawn, BleedoutOverride`,
  `EncClassHorse` + `csHorse`, zero perks, zero `ActorEffect`. Winner = a horse overhaul
  (`Witcher Horses 2.0.esp`) with **no deltas** on any of these fields vs `Skyrim.esm` — the horse
  lane is near-vanilla and the live winner is the authority either way.
- `Shadowmere 09CCD7:Skyrim.esm`: level 50, H 1637 (winner-buffed from vanilla 887), S 198,
  `Aggressive`, `ActorEffect = REQ_Trait_Healing_Shadowmere 10DE56` — the only boss-tier steed
  precedent in the order.
- Reported failure: `AX_HorseBronze 7D94A1` "Bronze Equus" (a `Summonable`, `DoesNotBleed` riding
  horse, `HorseRace`, `EncClassHorse`/`csHorse`, source `PCLevelMult 1`) patched to level 50 —
  the unique+summonable → boss misread. Note on the manual fix in place at read time: the winner
  templates it onto Shadowmere with `Stats, SpellList` + own fixed L1 — the `Stats` flag makes the
  own level inert, so it inherits Shadowmere's 50/H1637; if a mundane mount was intended, the
  `EncHorse*` tier (fixed 4) is the analogue.

## 4 — Ghost state (report items 8 + the race question)

- The state-trait keyword is **`ActorTypeGhost 0D205E:Skyrim.esm`** (vanilla; the old `perks.md`
  citation "kw ghost 0D205E" without a master was unresolvable as `Requiem.esp` — fixed).
- 236 NPC_ records reference it in the active order (samples: `MG07*Ghost` ×4, Soul Cairn
  `DLC01SoulCairn*` souls, `DLC01SoulCairnSoulHorseRider`) — it is carried on the **NPC record**,
  which is what the Reqtificator's `feature_stateTraits` matches.
- The assigned trait resolves live as `RFTI_Trait_Ghost 031284` (winner in the
  `Requiem - MR WAR Resist and Regen Tweak Patch.esp` lane).
- Reported record `AX_GhostFox 23A07A` "The Fox": `AX_GhostFoxRace`, `EncClassAnimalPredator` +
  `csWolf`, `Essential/Invulnerable/Summonable/DoesNotBleed`, source `PCLevelMult 1`, **no
  keywords**. The manual fix in place hand-stamps perk `031284` — works, but fights the auto-pass;
  the input-carrying fix is the `ActorTypeGhost` keyword on the NPC.
- `AX_GhostFoxRace 23A07B` vs the fox analogue `FoxRace 109C7C` (winner `Requiem.esp`): stat block
  **identical** (H 12 / M 0 / S 200, regen 0/10/5, unarmed 5); deltas are only `Swims` kept (Requiem
  removes it) and Requiem's added `REQ_DropsBloodKeyword 586728` (inappropriate on a ghost). So the
  race-side skip was defensible *by field comparison* — what was missing was the disposition naming
  the base analogue and routing the state keyword to the NPC lane. That routing is the new rule.

## 5 — Recognized-race creature skipped outright (report item 5)

`AX_SurpriseChaurus 0E5511` "Chaurus Hatchling": `ChaurusRace` (recognized), `EncClassChaurus` +
`csChaurus`, `VeryAggressive`, source `PCLevelMult` — completely untouched by the session
("recognized race → needs almost nothing" read as *nothing*). Manual fix in place: template onto
`EncChaurus 0A5600` (winner `Requiem.esp`) with `Stats, SpellList`. The rule now states the
near-nothing pass is still a pass (fixed level + offsets + drain).
