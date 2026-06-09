# Modded-follower runtime registration

Requiem treats followers specially (lockpicking minion, follower-aware systems). For a follower to be
*seen* by those systems, two things must be true: the NPC is in Requiem's follower factions
(record-side) **and** its alias is registered with Requiem's follower tracker (runtime-side). This
reference covers the runtime half.

## The API — `REQ_FollowerRegistration`

Source: `…\Requiem 6.0.2 …\Source\Scripts\REQ_FollowerRegistration.psc` (a `Quest Conditional`
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
  `playerFollowerFaction`. So the record-side faction work (below) is a prerequisite, not optional.
- Vanilla followers and Requiem-aware followers are already tracked. You only need to register a
  **major custom follower** that ships its own follower quest/alias and isn't otherwise picked up.

## The reference implementation — the Authoria bridge

`…\Authoria - Modded Follower Requiem Registration\` ships:
- `Source/Scripts/_Authoria_REQ_MajorFollowerBridge.psc` (+ compiled `.pex`), a `Quest` script.
- `Authoria - Master Patch - Requiem Follower Registration.esp`, the quest that holds one
  `ReferenceAlias` per major follower (Inigo, Kaidan, Auri, Xelzaz, Mrissi, Sahara, Thogra, Yoana,
  Remiel, Lucien) and points the bridge's `REQ_FollowerControl` property at Requiem's tracker.

The bridge logic (paraphrased): on `OnInit`, wait a short delay, then for each filled follower alias
call `REQ_FollowerControl.RegisterFollowerAlias(alias)`; retry a few passes until at least one
registers or `MaxRegistrationPasses` is hit. It's defensive (handles "already registered" / "storage
full") and event-driven (single `OnUpdate`, no polling loop).

**Current load-order state (verify before relying on it):** the bridge *mod* is enabled (its loose
script is present), but `Authoria - Master Patch - Requiem Follower Registration.esp` is **INACTIVE**
in this profile — so the quest that carries the aliases isn't loaded, and the bridge is dormant. That
means a freshly added major follower is *not* auto-registered right now. Check
`housecarl_load_order_status lookup="Authoria - Master Patch - Requiem Follower Registration.esp"`;
if it's inactive, registering a new follower means either enabling/extending that ESP or shipping an
equivalent quest+alias+bridge of your own.

## The patch recipe — registering a new follower

Per project direction: **if the plugin you are patching contains a follower, add that follower to
Authoria's follower-registration quest.** Concretely:

1. **Record-side first (via `requiem-npc-patching`):** put the follower NPC in
   `CurrentFollowerFaction 05C84D` (rank 0) and `PotentialFollowerFaction 05C84E` (rank -1), give it
   the Requiem follower class/outfit/level, and keep `PcLevelMult`. Without `playerFollowerFaction`
   membership the alias will never count as current.
2. **Add a `ReferenceAlias` for the follower** on the registration quest
   (`Authoria - Master Patch - Requiem Follower Registration.esp`'s quest, or your own equivalent
   quest), filled to the follower's unique actor reference (a `Unique Actor` fill or a find-by-ref
   alias). The bridge already iterates its aliases and registers each, so adding an alias is enough —
   no new script code is needed, just one more alias + a property hookup on the bridge.
3. **If you author your own quest instead** of extending Authoria's: a minimal quest with a
   `ReferenceAlias` on the follower and a one-shot script that, on init, calls
   `REQ_FollowerControl.RegisterFollowerAlias(self_alias)` (mirror the bridge). Point its
   `REQ_FollowerControl` property at Requiem's `REQ_FollowerRegistration` quest instance.
4. **Verify:** the alias is in `playerFollowerFaction`; storage isn't already at 30; the registration
   ESP is active. If the bridge ESP is inactive in the target profile, flag that the follower won't
   be registered until it's enabled or replaced.

## Boundary

The record-side (factions, class, outfit, level, `PcLevelMult`) belongs to `requiem-npc-patching`.
This skill owns the runtime registration — the quest/alias/`RegisterFollowerAlias` hook. SPID is an
alternative distribution mechanism for *adding* the follower to a faction at runtime, but Requiem's
follower tracking specifically wants the alias registered, which is a quest-alias job, not a SPID one.
