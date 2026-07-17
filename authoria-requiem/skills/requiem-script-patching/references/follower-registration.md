# Modded-follower runtime registration

Requiem treats followers specially (lockpicking minion, follower-aware systems). For a follower to be
*seen* by those systems, two things must be true: the NPC is in Requiem's follower factions
(record-side) **and** its alias is registered with Requiem's follower tracker (runtime-side). This
reference covers the runtime half.

## Ask first — registration is a decision, not a default

**A follower that ships its own controller framework is a user decision, not an automatic
registration.** Requiem's follower factions (`CurrentFollowerFaction` / `PotentialFollowerFaction`)
are a *prerequisite* for registration, and putting a follower in them can cut across a mod's own
recruit flow — the mod's framework and vanilla/Requiem following both try to own the actor.

Before registering, check whether the mod runs its own follower controller (a quest + script managing
recruitment/dismissal — look for a `*Controller`/`*Follower` quest script in its `Scripts/`, and read
how the mod says you recruit). If it does, **surface it and ask**: say what registration adds
(Requiem's follower-aware systems see the actor) and what it risks (faction membership crossing the
mod's recruit flow), and let the user decide. Register without asking only when the follower uses the
vanilla follow framework.

Live precedent (2026-07-16): a follower whose mod ships its own `*Controller` framework had the full
registration built — follower factions + a standalone bridge quest + compiled script + `.seq` — and
the user had it **removed in full**, because the mod's framework already owned recruitment and the
registration was never wanted. The record-side factions had to be stripped back to the author's
originals as well. Nothing in the build was wrong; the *default* was.

## The API — `REQ_FollowerRegistration`

Source: `…\Requiem <version>\Source\Scripts\REQ_FollowerRegistration.psc` (a `Quest Conditional`
script; the live instance is referred to as `REQ_FollowerControl`).

It keeps an array of `ReferenceAlias`es and checks each for the player's current follower(s):

```papyrus
ReferenceAlias Property vanillaFollower Auto      ; slot 0
Faction Property playerFollowerFaction Auto       ; alias must be in this faction to count as "current"
ReferenceAlias[] checkForFollowers                ; new ReferenceAlias[30]  (storage = 30 slots)

Bool Function RegisterFollowerAlias(ReferenceAlias toRegister)
   ; returns False if already registered or storage (30) exceeded; else appends + returns True
Bool Function UnregisterFollowerAlias(ReferenceAlias toUnregister)
Actor[] Function GetCurrentFollowers()            ; up to 4 current followers (must be IsPlayerTeammate)
```

Key facts:
- **Storage is 30 alias slots**, slot 0 reserved for the vanilla follower. Don't assume infinite room.
- An alias only counts as a *current* follower when its ref is a player teammate **and** in
  `playerFollowerFaction`. So the record-side faction work is a prerequisite, not optional.
- Vanilla followers and Requiem-aware followers are already tracked. You only need to register a
  **major custom follower** that ships its own follower quest/alias and isn't otherwise picked up.

## The reference implementation — the Authoria bridge (and why adding an alias does nothing)

`…\Authoria - Modded Follower Requiem Registration\` ships:
- `Source/Scripts/_Authoria_REQ_MajorFollowerBridge.psc` (+ compiled `.pex`), a `Quest` script.
- `Authoria - Master Patch - Requiem Follower Registration.esp`, the quest holding one
  `ReferenceAlias` per major follower and pointing the bridge's `REQ_FollowerControl` property at
  Requiem's tracker.

**Read the script before wiring anything to it.** It does **not** iterate a generic alias array. It
declares **ten fixed, named properties** and calls each one explicitly — verified by reading the
source (2026-07-16, during a live follower patch):

```papyrus
ReferenceAlias Property InigoFollower Auto
ReferenceAlias Property KaidanFollower Auto
…                                              ; ten in total, one per known follower

Event OnUpdate()
    …
    RegisterOne(InigoFollower, "Inigo")
    RegisterOne(KaidanFollower, "Kaidan")
    …                                          ; ten hardcoded calls — no loop, no array
EndEvent
```

So **adding an eleventh alias to that quest is a no-op** — the bridge never looks at it. Extending
Authoria's bridge means editing a third-party mod's script: declare a new property, add a matching
`RegisterOne` call, **recompile the `.pex`**, then fill the property — and then maintain that edit
across the mod's updates. Prefer the standalone quest below.

The bridge's *shape* is still the one to copy: `OnInit` → `RegisterForSingleUpdate(StartupDelay)`, a
single `OnUpdate` that attempts registration and retries a bounded number of passes
(`MaxRegistrationPasses`) until at least one alias registers. Event-driven, no polling loop, and a
`False` return treated as non-fatal (already registered / storage full).

**Load-order state (verify, never assume):** the bridge *mod* may be enabled while
`Authoria - Master Patch - Requiem Follower Registration.esp` is **inactive** — in which case the
quest carrying the aliases isn't loaded and the bridge is dormant, so nothing is auto-registered:

```
housecarl_load_order_status lookup="Authoria - Master Patch - Requiem Follower Registration.esp"
```

## The patch recipe — a standalone bridge quest (the primary pattern)

Once the user has agreed registration is wanted (above), ship your own quest rather than extending
Authoria's. Built and verified end-to-end on a live follower mod, 2026-07-16:

1. **Record-side first (via `requiem-npc-patching`):** put the follower NPC in
   `CurrentFollowerFaction 05C84D` (rank 0) and `PotentialFollowerFaction 05C84E` (rank -1), give it
   the Requiem follower class/outfit/level, and keep `PcLevelMult`. Without `playerFollowerFaction`
   membership the alias will never count as current.
2. **Author a quest in your patch** carrying one `ReferenceAlias` filled to the follower's unique
   actor reference (a `Unique Actor` fill, or find-by-ref).
3. **Author + compile a one-shot script** on that quest, mirroring the bridge's shape: a
   `REQ_FollowerRegistration Property REQ_FollowerControl Auto` pointed at Requiem's tracker instance,
   `OnInit` → `RegisterForSingleUpdate`, and an `OnUpdate` calling
   `REQ_FollowerControl.RegisterFollowerAlias(<your alias>)` with a bounded retry. Compile with
   `housecarl_compile_script`; treat a `False` return as non-fatal.
4. **Write the `.seq`** — the quest is start-game-enabled, so without one it won't run on an existing
   save (`housecarl_write_seq`).
5. **Verify:** the alias's actor is in `playerFollowerFaction`; Requiem's storage isn't already at 30;
   your quest ESP is active; `housecarl_validate_scripts` clean.

**If the user later declines the registration,** remove it in full — the factions come back off the
NPC override too, not just the quest. A half-removed registration leaves the actor in Requiem's
follower factions with nothing tracking it.

## Boundary

The record-side (factions, class, outfit, level, `PcLevelMult`) belongs to `requiem-npc-patching`.
This skill owns the runtime registration — the quest/alias/`RegisterFollowerAlias` hook. SPID is an
alternative distribution mechanism for *adding* the follower to a faction at runtime, but Requiem's
follower tracking specifically wants the alias registered, which is a quest-alias job, not a SPID one.
