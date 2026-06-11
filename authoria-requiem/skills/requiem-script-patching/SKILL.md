---
name: requiem-script-patching
description: Patch the Papyrus/script layer of a mod for Requiem via houseCARL — decide whether new content even needs a script, reuse Requiem's existing Nox_* or REQ_* runtime by attaching it to a record's VirtualMachineAdapter and filling its properties, register a modded follower into Requiem's follower system, or compile a new .psc only when truly required. Use when the user wants to make a bound-weapon, teleport, mind-control, weather, or multi-spell-tome spell actually work in Requiem, register a custom follower like Inigo or Kaidan for Requiem, attach or set a script property on a magic effect, ask whether a spell needs Papyrus at all, fix a modded spell whose special mechanic does nothing in game, or compile a script into a Requiem patch. Load this before wiring a script onto a record, not after the spell casts but does nothing.
---

# Requiem Script Patching

## Overview

This skill covers the **Papyrus layer** of making a mod play well with Requiem: when a new
spell, item, or follower needs a script to behave correctly, and how to wire it up with
houseCARL. It is the runtime counterpart to the seven record-side domain skills (`requiem-weapon-patching`,
`requiem-magic-patching`, `requiem-npc-patching`, …). Those set fields; this one attaches behavior.

The single most important thing to internalize: **most patches need no script at all.** Requiem
scales damage, healing, buffs, and resistances through the engine `PowerAffectsMagnitude` flag and
keyword-driven Reqtificator rules — not Papyrus. Reach for a script only when the content has a
*special mechanic* the engine can't express (summon a bound item, teleport, charm/command an NPC,
change the weather, teach several spells from one tome, register a follower). Even then, the right
move is almost always to **reuse Requiem's existing script and its marker** rather than write your
own. Authoring a fresh `.psc` is the rare last resort.

## First step

Confirm houseCARL resolves Requiem's live winners before reading any record. Read the Iron Sword
`012EB7:Skyrim.esm` with `conflict_tree=true`; `Requiem.esp` must appear in the override chain
(`Skyrim.esm → unofficial skyrim special edition patch.esp → Requiem.esp → …`). The invariant is
chain presence, not winner identity — on a live instance the winner is normally
`Requiem for the Indifferent.esp` (the Reqtificator's generated output) or another patch loading
after Requiem, which is healthy; derive reference values from the last hand-authored override in
the chain, never from the generated output. Only if `Requiem.esp` appears nowhere in the chain,
point houseCARL at your Requiem MO2 instance with
`housecarl_set_mo2_instance path="<your MO2 instance>"` and re-probe before continuing.

Then **decide whether a script is even needed** (the gate below). Don't attach Papyrus reflexively.

## Workflow

### 1. The script decision gate

Walk this before touching a `VirtualMachineAdapter`:

- **Plain damage / heal / buff / resistance spell, enchantment, or item?** → **No script needed.**
  The magnitude is **not** script-driven — it scales via the MGEF's `PowerAffectsMagnitude` flag, and
  the Reqtificator assigns the rescaling perks (`RFTI_All_*`). Finish it record-side in
  `requiem-magic-patching` and stop. Two live shapes you'll see, both meaning "don't add a script":
  many Requiem effects carry **no** `VirtualMachineAdapter` at all (e.g.
  `REQ_Effect_Conjuration1_Bound_Sword 01CE9F:Skyrim.esm` — VMAD absent); some that *reuse a vanilla
  MGEF FormID* inherit an **incidental vanilla FX/quest script** (e.g.
  `REQ_Effect_Destruction2_Fire_Aimed 012F03:Skyrim.esm` carries `MG01FireEffectScript` — a vanilla
  leftover, and note its Flags still include `PowerAffectsMagnitude`). That incidental script is not a
  mechanic you add, replicate, or remove, and it is not what scales the damage. Either way: a plain
  new spell needs no script of yours.
- **Special mechanic** (bound item, teleport/blink, command/frenzy/sleep/silence/vanish, shadow
  magic, control weather, telekinetic disarm, enthrall, rune mastery, weakness, multi-spell tome,
  scroll/artifact crafting)? → A script is needed. Go to step 2 (reuse). See
  `references/nox-runtime-map.md` for which `Nox_*` script implements each mechanic and the
  properties it needs.
- **A follower** the mod adds and the user wants usable under Requiem? → step 3.
- **A genuinely new behavior** no Requiem script covers? → step 4 (author + compile). This is rare;
  exhaust reuse first.

### 2. Reuse path — attach Requiem's script (the common case)

Requiem's special spells already ship with their script attached to the effect. To make a new spell
behave like one of them, you have two complementary levers, and you usually want both:

1. **Clone the comparable's VMAD onto your record.** Find Requiem's equivalent effect (e.g. for a
   teleport spell, `REQ_Effect_Conjuration4_Teleport_RitualSelf 02CBCE:Requiem.esp`), read its
   `VirtualMachineAdapter` with `read_record … fields=["VirtualMachineAdapter"] depth=3`, and
   reproduce the same script name + property set on your MGEF, repointing object properties to your
   own forms where the script expects them. The shape is real and small — one `ScriptEntry` (Name,
   `Flags=Local`) plus a handful of `ScriptObjectProperty` entries. See
   `references/housecarl-recipes.md` for the `bulk_apply` shape.
2. **Carry the marker the runtime scans for.** Some mechanics are driven by a control quest/MGEF that
   periodically scans for spells or items carrying a marker keyword or sitting in a FormList, rather
   than by a per-record script. In those cases the new record just needs the **keyword** (the
   `Nox_KW_*` family — see `requiem-magic-patching`'s `keywords.md`) or membership in the right
   `REQ_List_*`/FormList, and the existing runtime picks it up. Prefer this when it exists — no VMAD
   to manage.

Most Nox scripts use levers in combination: the script on the effect does the work, but it reads
properties (spell arrays, perks, activators) that you must point at the right forms. Read the script
source under `…\Requiem - Magic Redone - rerun\scripts\source\Nox_*.psc` to see exactly which
properties matter. **Always verify with a VMAD read-back** after writing.

### 3. Follower registration

Two halves, and a patch usually needs both:

- **Record-side (already covered by `requiem-npc-patching`):** put the follower NPC in Requiem's
  follower factions — `CurrentFollowerFaction 05C84D` (rank 0) and `PotentialFollowerFaction 05C84E`
  (rank -1) — plus class/outfit/level (keep `PcLevelMult` for followers). This is what makes the game
  treat them as a follower at all.
- **Runtime-side (this skill):** Requiem tracks followers in the `REQ_FollowerRegistration` quest
  script (the `REQ_FollowerControl` instance) so its features — lockpicking minion, follower-aware
  systems — see them. The API is `Bool RegisterFollowerAlias(ReferenceAlias toRegister)` (storage =
  30 alias slots; slot 0 is the vanilla follower; the alias must be in `playerFollowerFaction` to
  count as *current*). Vanilla and Requiem-aware followers are picked up automatically; a **major
  custom follower** with its own quest/alias should be explicitly registered. The reference
  implementation is `_Authoria_REQ_MajorFollowerBridge` (a `Quest` script with one `ReferenceAlias`
  per follower that calls `REQ_FollowerControl.RegisterFollowerAlias(alias)` on init, retrying until
  it succeeds). **Per project direction: if the plugin you are patching contains a follower, add that
  follower to Authoria's follower-registration quest** — add a `ReferenceAlias` on the follower's
  ref to the bridge quest and let the bridge register it. See `references/follower-registration.md`
  for the full mechanism, the current load-order state, and the patch recipe.

### 4. Author path — compile a new script (rare)

Only when reuse genuinely can't express the behavior:

- Write the `.psc`. Look up every function signature with the `papyrus-reference` skill (vanilla +
  SKSE + ~45 plugin APIs) — never invent a signature. Run it past `papyrus-optimization` to keep it
  event-driven and light (no `OnUpdate` polling loops, cache `Game.GetPlayer()`, prefer `OnEffectStart`
  / `OnEquipped` / `OnInit`). Requiem's own scripts are the style model: tiny, event-driven, no
  persistence churn.
- Compile with `housecarl_compile_script script="<full path>.psc"`. It invokes the CK
  `PapyrusCompiler.exe`, auto-adds the vanilla source folder and the script's own folder to the
  import path, and lands the `.pex` in a new houseCARL patch mod (or `into="<existing patch>"` to
  accumulate). Pass `import_dirs="<dir1>;<dir2>"` for SKSE or other-mod sources. On failure it returns
  `name(line,col): message` per error — fix and recompile.
- Attach the compiled script to its record via VMAD exactly as in step 2.

See `references/vmad-and-compile.md` for the `VirtualMachineAdapter` field structure, the
`housecarl_compile_script` call shape and errors, and the Papyrus style guardrails.

## Judgment

- **Bias hard toward no-script, then toward reuse.** A scripted patch is heavier to maintain and more
  fragile than a record-side one. If the engine flag or a keyword/FormList marker can do it, use that.
- **Don't confuse a record-side keyword with a script.** The `Nox_KW_Staff_<School><Tier>` keywords
  classify a staff's frame and charge **on the record** (handled in `requiem-weapon-patching` /
  `requiem-magic-patching`); there is **no** Papyrus script that reads them to scale staff power. A
  plain new staff needs no script — only a bound-item-casting staff (which carries a Nox bound-weapon
  effect) does. Don't go hunting for a staff-scaling script; it doesn't exist.
- **Preserve a mod's working script.** If a modded spell already ships a functioning script for its
  mechanic, don't rip it out to bolt on a Nox equivalent — that risks breaking it. Rebalance the
  record-side numbers (cost, magnitude via `requiem-magic-patching`) and leave the behavior script
  alone unless it conflicts with a Requiem system.
- **Know what not to break.** The load-bearing `REQ_*` control scripts (the install/version quests,
  `REQ_AttributeSystem`, the exhaustion controller, `REQ_MCM`, `REQ_FollowerRegistration`,
  `REQ_LockpickControl`) run the whole framework. Never override or detach them; new content
  *registers* with them (a keyword, a faction, an alias), it doesn't replace them. See
  `references/req-core-scripts.md`.

## Common mistakes

- Attaching a script to a plain damage/heal spell "to be safe." It does nothing and adds save-game
  weight — the magnitude already scales via `PowerAffectsMagnitude`.
- Copying a Nox script's name onto a record but leaving its object properties pointing at the
  comparable's forms (or empty). The script then teleports to *Requiem's* marker, summons the wrong
  weapon, or no-ops. Repoint every property to your forms and read it back.
- Putting a follower in the follower factions but never registering the alias, then wondering why
  Requiem's follower features ignore them — or registering an alias for an NPC that isn't in
  `playerFollowerFaction` (it won't count as current).
- Hand-writing a `.psc` for a mechanic Requiem already ships (`Nox_TomeMultipleSpells` for multi-spell
  tomes, `Nox_BoundWeapon` for bound weapons, …). Reuse it.
- Inventing a Papyrus or SKSE function signature. Always confirm with `papyrus-reference`; an unknown
  call fails to compile.
- Leaving a `REQ_NULL_*` form in a property or repointed reference. Strip it (the masters/`REQ_NULL`
  rule carried by the `requiem-patching` skill), and verify the patch's `masters:` read-back is correct
  and load-order-sorted.

## Checklist

- [ ] Ran the freshness probe (Iron Sword's chain contains `Requiem.esp`).
- [ ] Confirmed a script is actually needed (special mechanic / follower) — not a plain effect.
- [ ] Reused Requiem's `Nox_*`/`REQ_*` script + marker where one exists, before considering authoring.
- [ ] Every VMAD object property repointed to the patch's own forms; read back and verified.
- [ ] Follower: in the Requiem follower factions (record-side) **and** registered with the bridge
      quest/alias (runtime-side) if it's a major custom follower.
- [ ] Any compiled `.pex` produced by `housecarl_compile_script` with no errors; `.psc` checked
      against `papyrus-reference`.
- [ ] No `REQ_NULL_*` reference left in any property/reference; `masters:` correct + load-order-sorted.

## Notes

- Nox script source lives at `…\Requiem - Magic Redone - rerun\scripts\source\Nox_*.psc`; the
  compiled `.pex` next to it. Requiem core scripts: `…\Requiem 6.0.2 …\Source\Scripts\REQ_*.psc`.
- Nox scripts attach mostly to **MGEF** records (extend `ActiveMagicEffect`, fire `OnEffectStart`);
  `Nox_TomeMultipleSpells` is the exception — it extends `ObjectReference` and fires `OnEquipped` on
  the **BOOK** (tome) record.
- Diseases progress via a script on the disease **MGEF** (stages embedded there), not the SPEL; the
  SPEL is a `Touch`/`ConstantEffect` carrier. New disease content reuses the disease MGEF pattern.
- houseCARL `read_record … fields=["VirtualMachineAdapter"] depth=3` shows the live attachment +
  property values; that read shape is also the template for the write (`references/housecarl-recipes.md`).
- Boundary: record stats/keywords → the matching domain skill; this skill only adds/attaches behavior.
