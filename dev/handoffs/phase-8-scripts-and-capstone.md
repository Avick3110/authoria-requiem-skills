# Phase 8 handoff — scripts + the integration capstone

*Class: ARCHIVE (per `standards/HOUSECARL_DOC_HYGIENE.md`) — build-era phase handoff, frozen 2026-06. The deep "why" corpus for this domain: read on demand, never edit. Current session state lives in `dev/session-handoffs/`.*

*Paste the block below into a new Claude Code session (houseCARL **1.2.1** + anthropic-skills available). Self-contained. This is the **capstone** — the largest phase. It authors TWO skills and fills every gap the domain phases left.*

---

You are authoring **Phase 8** — the **capstone** — of the Authoria Requiem Patching Skills project. Phases 1–7 built the seven domain skills (weapon, armor, ammo, race, npc, leveled-list, magic). Phase 8 does two things:

1. **`requiem-script-patching`** — the Papyrus/scripts domain (the Magic Redone `Nox_*` runtime, modded-follower runtime registration, `REQ_*` scripts, and how to attach/compile a script for a Requiem patch).
2. **`requiem-patching`** — the **integration brain / router**: it understands **every Requiem mechanic and why**, and given a new plugin it produces the **complete, exact plan to make that plugin play flawlessly with Requiem** (balance + gameplay) — routing each record to the right domain skill AND **filling the gap mechanics no earlier phase covered** (vampirism & lycanthropy, diseases, exhaustion/stress, stealth/lockpicking, alchemy, food, shouts, standing stones/birthsigns, perks/skills/leveling, economy, and the core combat/resistance model).

**Working dir:** `D:\Wabbajack\Authoria-requiem`. **Study source:** live load order via **houseCARL 1.2.1** at `D:\Wabbajack\Authoria-dev`. Both skill folders already exist (`authoria-requiem\skills\requiem-script-patching\`, `…\requiem-patching\`).

## First, orient (read widely — this phase synthesizes the whole project)

1. Read `STATE.md` **in full** (every phase's notes — this skill must know what each domain skill already covers and route to it), then `docs\scope-and-authority.md`, `docs\reqtificator-rules.md`, `docs\masters-and-null-stripping.md`, then `C:\Users\Heisen\Downloads\HOUSECARL_SKILL_AUTHORING.md` (binding standard).
2. Read **all seven** existing `SKILL.md`s (weapon/armor/ammo/race/npc/leveled-list/magic) — the router must dispatch to them accurately, by name and by trigger.
3. Read Requiem's design docs for the "why": `…\Requiem 6.0.2 …\documentation\` (`Changelog.md`, `Races.md`, `Artifacts.md`) and the full `ActorAssignmentRules_Requiem.esp.conf` (the perk/spell/trait engine — it's the spine of most mechanics).
4. Invoke `anthropic-skills:skill-creator` (standard §1).
5. **Freshness probe:** Iron Sword `012EB7:Skyrim.esm` `conflict_tree=true` → winner MUST be `Requiem.esp`; if not, `housecarl_set_mo2_instance path="D:\Wabbajack\Authoria-dev"` and re-probe.

## Operating principles

- **Be the master — make studied calls** (memory `be-decisive-master`); derive from the live winner + config + addon precedent; **never invent — if a mechanic can't be fully resolved live, document what you found and flag the gap** (don't guess; per the standard's "explicit I-cannot beats a confident wrong answer").
- **houseCARL 1.2.1:** write directly into active patches; `where=` filters help; verify new patches via read-back. For scripts, `housecarl_compile_script` compiles a `.psc`→`.pex` (CK compiler) and `read_record` shows a record's `VirtualMachineAdapter` (VMAD) script attachments. Use the `papyrus-reference` and `papyrus-optimization` skills.
- **Live > shipped docs**, masters + REQ_NULL discipline per the docs.

## Part A — `requiem-script-patching`

Cover the Papyrus layer. Build it on Phase 7's handoff spec (STATE Phase-7 notes, "what the script handoff must cover"):

- **The split is clean — most patches need NO script.** Base damage/heal/buff scaling is the engine `PowerAffectsMagnitude` flag, not Papyrus; core MGEFs carry no VMAD. Teach **when a script is and isn't needed.**
- **Magic Redone `Nox_*` runtime** (`Requiem - Magic Redone - rerun\scripts\`, ~50 `Nox_*.pex`): document, per special-mechanic script, **which MGEF/spell archetype it attaches to, the properties it needs, and the marker keyword a new spell must carry to be picked up** — bound items, teleport/blink, mind-control (command/frenzy/sleep/silence/vanish), shadow magic, weather, telekinetic disarm, enthrall, weakness/rune mastery, enchanting/scroll-craft furniture, and `Nox_TomeMultipleSpells` (multi-spell tomes). Locate the staff-keyword consumer (the quest/MGEF that reads `Nox_KW_Staff_<School><Tier>`).
- **Modded-follower runtime registration** (Phase 5 did the record-side): the runtime side is script — `Authoria - Modded Follower Requiem Registration` is enabled but ships only `Scripts/Source` Papyrus, no plugin. Document how a modded follower is registered into Requiem's follower system at runtime, and what a patch must do (record-side faction/quest-alias vs the script/SPID hook).
- **`REQ_*` core scripts:** survey the load-bearing ones (the control quests, the exhaustion controller, the MCM) enough to know **what NOT to break** and when new content must register with one.
- **How to attach/author a script in a patch:** the `VirtualMachineAdapter` on a record (attach an existing script + set properties via houseCARL), and compiling a new `.psc` via `housecarl_compile_script` when genuinely required (rare — prefer reusing Requiem's scripts + keywords/markers).

## Part B — `requiem-patching` (the integration brain / router)

This is the skill someone loads when they say *"patch mod X for Requiem."* It must:

1. **Encode Requiem's vision + every mechanic and why** (synthesized from the docs + config + the seven domains). The core model: de-leveled fixed world; knowledge/perk-gated progression; tight economy; the physical/magical **resistance + armor-penetration + stagger/exhaustion** combat system; keyword-driven balance assembled by the Reqtificator. Keep the SKILL.md body a tight overview; put the deep "why" in references.
2. **The master integration workflow** (the heart): given a plugin → `cross_plugin_query plugins=["<NewMod>.esp"]` to enumerate every new record by type → for each type **route to the right domain skill** (WEAP→weapon, ARMO→armor, AMMO→ammo, RACE→race, NPC_→npc, LVLI/LVLN/CONT/ECZN→leveled-list, MGEF/SPEL/ENCH→magic, scripts→script) → **handle the gap mechanics** (below) → run the **final cross-cutting checklist** (masters correct + sorted, no `REQ_NULL` left, Reqtificator-handles-this vs needs-manual matrix, then "run it through the Reqtificator"). This is the router table the standard's §5.2 describes, made operational.
3. **Fill the gap mechanics** — for each, derive the system live (winner + config + the addon + Reqtificator source) and give the integration recipe; one `references/<mechanic>.md` each. Prioritize:
   - **Vampirism & lycanthropy** (the headline gap): stages/powers/weaknesses/feeding, the `stateTraits.vampire` incoming-damage perk, the player vampire/werewolf perks, the disease vectors; comparables = `Requiem_VampireCollection.esp` + the vanilla vampire/werewolf races (Phase 4) + the vampire/werewolf spells (Phase 7). *(If this turns out large, you MAY split it into its own `requiem-vampire-lycanthropy` skill folder — adding a focused skill is fine per the standard; otherwise keep it a capstone reference.)*
   - **Diseases** (contraction/progression/cure; the ability+MGEF+vector pattern).
   - **Exhaustion / stress** (the `gameMechanics.perks.stress` family + `exhaustionController`/`baseAttackSpeed` spells — stamina-driven attacks, armored-casting, mass-effect).
   - **Stealth / lockpicking / pickpocket** (`Requiem - Stealth Redone.esp`; sneak perks player-exclusive; the SKSE lockpicking config).
   - **Alchemy** (`Requiem - Alchemy Redone.esp`; INGR/ALCH; ingredient effects must use Requiem MGEFs; poison rescaling — extends Phase 7's alchemy note).
   - **Food & cooking** (`Requiem - Food and Beverages Redone.esp`; food/alcohol effects).
   - **Shouts** (SHOU/WOOP owned by `Requiem.esp`; words → SPEL + cooldowns).
   - **Standing stones / birthsigns** (`Requiem - Birthsigns Redone.esp`).
   - **Perks / skills / leveling** (hook into existing trees, don't author new — per Phase 7; player-exclusive perk assignment).
   - **Economy / vendors** (vendor gold, prices, `DynamicPricing` JSON, barter; new merchant setup).
   - **Core combat & resistance model** (the "why" reference: damage = weapon vs the target's resistance/armor; armor-penetration perks by damage type; stagger; the keyword chain that ties it all together).

## Build both skills (conform to the standard §2–§8)

- Each `SKILL.md`: `name:` = folder; action-first pushy `description:` (the router's must trigger on the broad entry phrase — "Patch a new mod for Requiem", "make a mod Requiem-consistent", "what does this plugin need for Requiem", plus the gap-mechanic nouns: vampire, werewolf, disease, exhaustion, stealth, alchemy, shout, standing stone, economy); body ≤500 (router uses the §5.2 router shape — short body + the routing table + the integration workflow; scripts skill uses the procedural shape); real houseCARL/houseCARL-compile call shapes; `references/` + `index.jsonl`; `evals/eval_set.json` (≥8+≥8, §6.5 manual fallback).
- The router's `references/` = the mechanic deep-dives (one per gap mechanic) + `requiem-vision.md` (the philosophy/why) + `integration-checklist.md` (the cross-cutting final checklist) + `routing-table.md` (record type → domain skill) + `index.jsonl`.

## Verify before finishing

- **Scripts:** round-trip — confirm a plain new spell needs no script (no VMAD on its MGEF), and document one real `Nox_*` attachment end-to-end (record + properties + marker keyword). Optionally compile a trivial `.psc` via `housecarl_compile_script` to prove the path.
- **Capstone:** **end-to-end integration dry-run** on one real multi-record plugin already in the load order — enumerate its new records, route each, name the gap mechanics it touches, and produce the full integration plan; check it against what Requiem/the addons actually did. Record in `evals/results-<date>.json`.
- Walk the §8 19-item checklist for both skills; note deferrals.

## Close out

- Update `STATE.md`: set Phase 8 ✅; append a Phase-8 notes block (the script-vs-no-script rule, the Nox runtime map, follower registration, the gap-mechanic findings — esp. vampire/lycanthropy — and whether any gap was promoted to its own skill; plus anything Phase 9 must audit).
- Report back to the master session: what was built, the dry-run result, any gap you flagged as **not fully resolvable live** (so Phase 9 can finish it), and whether you split out a vampire/lycanthropy skill. The master session then runs **Phase 9** (the `masters-and-null-stripping` back-fill into all skills, the §8 audit of all skills, the no-`REQ_NULL`/masters-sorted gate, and packaging). Do **not** do Phase 9 yourself.
