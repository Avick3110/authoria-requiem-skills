# LOCKS census: containers, doors, placed refs, cells, encounter zones

*Author: Aaron (+ Claude session) · 2026-10-30*

**Corpus (stage 2):** CONT 9,678 rows / 90 plugins (1,612 records); DOOR 2,032 rows / 48 plugins (414 records);
REFR 1,336 rows / 47 plugins; ACHR 157 / 16; CELL 74,963 / 59; ECZN 8 / 1; WRLD 1,389 / 38. 104 stage-2 plugins
override at least one CONT or DOOR (the brief's 136 is the whole corpus; this doc counts the stage-2 ledger).
**Held-out:** Animated Carriages, Falkreath Shades, The Rot Below, Triumvate (CONT); Falkreath Shades, Rot Below (DOOR);
Falkreath Shades (REFR, CELL: bookkeeping / geometry only). Held-out patches have **0 rows** in the REFR lock, disable
and ECZN families, so only the lockpick and container-list conventions get held-out validation.

No current skill owns this domain. Most of it is small: one baked script stamp, a Requiem-forward rule for REFR locks,
one disable idiom, and per-plugin container edits.

## Conventions

| # | Convention | Class | Layer | Support rows / plugins | Spread | Held-out | Conf. |
|---|---|---|---|---|---|---|---|
| 1 | Every overridden CONT / DOOR carries `REQ_LockpickControl` (Local; `dataStorage` = `AD3B22:Requiem.esp`, `FollowerControl` = `82F3AF:Requiem.esp`) | baked (`CustomLockpicking.cs` L19-48; fact 12) | baked | 1,996 records (9,654 script rows) / 104 | 101 / 104 plugins at 100 %; top LOTD 360, Glenmoril 238, Bruma 203; misses only LOTD 23, Miahil Hawk 1; ksws03 6 unreadable. RFTI output stamps the misses (02064F, 0774BF) | 6 / 6 held-out CONT/DOOR sets at 100 % (104 records) | high |
| 2 | Keep Requiem's own VMAD on a vanilla container (`AA000XarrianLockBreakScriptNormal`, on 76 Requiem.esp CONT/DOOR) | Requiem-aligned (live) | input | 5 records / 1 (LOTD) in corpus; Container Merge 3 / 4 | holds in LOTD 5 / 5; Container Merge (not in corpus) drops it on 1 | none | med |
| 3 | Bruma extends the Xarrian lock-break script to 28 BSHeartland treasure chests / safes | house convention (Requiem by analogy; Aaron confirms) | input | 28 / 1 | Bruma only; 0 Xarrian rows in Glenmoril / Vigilant / Unslaad chests | none | med |
| 4 | REFR lock level = Requiem.esp's override of that REFR (forward Requiem, not the mod or vanilla) | Requiem-aligned (29 / 29 equal, from the stage-2 family verifier) | input | 31 / 14 | 29 rows over 14 plugins; 2 exceptions | none | high |
| 5 | LockData presence follows Requiem: struct added with Key = Null when Requiem adds a lock; Flags bit 2 cleared to 0 as Requiem does | Requiem-aligned | input | 23 + 3 / 14 | 3 Flags rows: EEKs Whiterun, JK Palace, Snazzy Markarth | none | med |
| 6 | Keys: keep Requiem's level, add the source / third-party key (Locked Chests Have Keys, Snazzy) Requiem lacks | Requiem-aligned level + input key | input | 7 real / ~5 (16 more null-link noise) | small | none | med |
| 7 | Disable a placed ref / actor: Z = -30000 + EnableParent PlayerRef `000014` + SetEnableStateToOppositeOfParent | Requiem-aligned (Requiem.esp: 70 REFRs at Z -30000, 69 with that parent; 3 ACHRs) | input | REFR EnableParent 90 / 19 (82 of them the player-opposite disable, rest Kaidan real parents), Position 111 / 25; ACHR 7 / 6 | no plugin dominates | none | high (REFR), med (ACHR) |
| 8 | Merge-forward a vanilla container: Requiem's entry set + the source's added entries (the build does not merge CONT, fact 22) | Requiem-aligned | input | 64 / 1 (LOTD) | LOTD only | none | med |
| 9 | Strip vanilla loot lists (lockpicks, soul gems, potions, gold change, REQ_NULL_ lists) from a mod's own treasure containers | house convention, per plugin | input | 827 / 3 (+ small) | Glenmoril 512, Vigilant 197, Unslaad 118; not cross-patch | Rot Below 8 removed rows (not split) | low |
| 10 | Vendor-chest quantity rebalance (entry removed and re-added at lower Count) | house convention, per plugin | input | ~222 / 2 | Bruma (147), Dac0da (75) | none | low |
| 11 | Empty test / debug / start-room containers | house convention | input | ~89 / 4 | Alternate Perspective 78 + 11 | none | low |
| 12 | Respawns flag on CONT: no rule (S&S cleared on 6, LOTD added on 1; Requiem keeps it on 222 / 279) | noise / no convention | input | 7 / 2 | | none | low |
| 13 | Encounter zones: Gray Cowl moves 18 cells off NoResetZone onto 3 new NeverResets zones banded 15-22 / 18-26 / 22-30, and caps 3 source zones (MaxLevel 20 / 30) | house convention (one plugin) | input (build never writes Min/Max; `OpenCombatBoundaries.cs` only ORs a flag) | 8 ECZN + 18 CELL / 1 | Gray Cowl only | none | med |
| 14 | Re-point placed food / pelt / drink refs onto REQ_ bases | mixed (cannot-tell) | input | 225 / 11 | Bruma bulk | none | low |
| 15 | CELL / WRLD edits (geometry, lighting, location, regions, flags) carry no Requiem convention | noise | n/a | 75k + 1.4k / 59 | | | high |
| 16 | Lockpick-control property order / case variants (Kaidan reversed order 32; Become a Bard 11, EEKs 3 lowercase `datastorage`) | noise (Papyrus names are case-insensitive) | baked | 46 / 3 | | | med |

## Upstream checklist (what a patch must carry)

- [ ] **REFR lock**: if Requiem.esp overrides the REFR, copy its LockData exactly (Level, Key, Flags 0). If not, keep the
  source's lock; don't add or raise a lock unless you have a reason (bug 1 below).
- [ ] **Key**: keep the source's key, or a third-party key mod's, on top of Requiem's level.
- [ ] **Disable**: Z -30000 + EnableParent `000014:Skyrim.esm` with SetEnableStateToOppositeOfParent. Never delete a ref.
  The same idiom works on ACHRs.
- [ ] **Vanilla container a mod changes**: start from the live Requiem version (entries, *and its VMAD*, including the
  Xarrian lock-break script), then add the mod's entries. A copy of the mod's version loses Requiem's edits.
- [ ] **Mod-own containers**: no Requiem comparable. Loot strips, vendor rebalances and test-chest emptying are
  per-plugin choices; ask before applying them (call 1).
- [ ] **Encounter zones**: the build never writes levels, so any banding is input. Requiem's own zones are min-only
  (MaxLevel 0); a cap is a deliberate departure (call 3).
- [ ] Leave Respawns, cell lighting / location / regions and worldspace data as the source has them.

## Downstream bake package (what ARR writes that the build would produce)

- `REQ_LockpickControl` VMAD entry on every winning CONT / DOOR: script `REQ_LockpickControl`, Flags Local,
  properties `dataStorage` → `AD3B22:Requiem.esp`, `FollowerControl` → `82F3AF:Requiem.esp`. It's redundant while
  Requiem for the Indifferent.esp loads after the patches, which it does in the ARR profile (it sits after every Authoria
  patch). The build stamps any winner that lacks it, AlreadyReqtified or not: the RFTI output carries it on 02064F and
  0774BF, which the LOTD patch omits. Nothing else in this domain is baked: no
  build path writes REFR locks, container items, ECZN levels or cells.

## Per-type detail

**Container.** 9,678 rows: 7,662 lockpick script (rule), 252 order-only, 123 bookkeeping, 42 presentation; residual
families are Items removed 1,116 / added 460 / changed 11, Flags 7, defined 5. Items families were both overturned by the
verifier (`material_error`): "loot strip" is really two things (mod-own treasure strip in 3 plugins, and Bruma / Dac0da
vendor rebalances), and "merge-forward" holds only for LOTD's 64 rows onto vanilla chests (e.g. `02064F` TreasBanditChestBoss:
patch = Requiem's 14 entries + 5 LOTD). The rest of the added rows are the re-add half of rebalances on mod-own chests.
Live read (this session): Requiem.esp carries `AA000XarrianLockBreakScriptNormal` (1 property) on 76 CONT/DOOR. 67 are
Requiem winners, 5 LOTD (all keep it, read 020662 / 020665 / 020670 / 03AC21 / 05418E), 4 Container Merge (3 keep it).
`02064F` has no VMAD in Requiem, so Requiem's lock-break script is selective, not universal.

**Door.** 2,032 rows, almost all the lockpick script (1,992); 24 bookkeeping, 12 presentation, 2 ksws03 defined, 2 sound
noise. 412 / 414 doors carry the script; the other 2 are ksws03, unreadable. Climbable Door Ladder (149 rows) is pure lockpick.

**PlacedObject (REFR).** Lock families (Level 31, Lock 23, Key 23, Flags 20, Unused / Unused2 17 each): one rule,
forward Requiem. Verifier batch read all 31 Level FormIDs: 29 have a Requiem.esp REFR override and the patch matches it
29 / 29 (e.g. `016607` vanilla Adept → Expert; `09DA64` RequiresKey → Master; `093F97` none → Novice; `0DDC47` Expert →
Novice). Disable families (Position 111, EnableParent 90 plus sub-fields) are one idiom; the sub-field families double-count
it. Rotation (77) is -0 vs 0 noise. Base changed (225) cannot-tell. Kaidan's EnableParent rows (217E08) are real
functional parents, not disables.

**PlacedNpc (ACHR).** 157 rows / 16 plugins, no held-out. Disable idiom on Penetrator `000D73` and Gryphonknight
`00080E`, as Requiem does on ACHR `101004` / `101005` / `0CA587`. Base swaps (2), owner (4), EZ → NoResetZone (1, Sonic
Mage) are unsampled singletons.

**Cell.** 73,815 geometry rows and 638 empty-vs-absent (rule noise); residual Location 62, Owner 21, EncounterZone 19
(Gray Cowl 18 + Val Serano 1), Regions / Flags / Lighting noise.

**EncounterZone.** 8 rows, Gray Cowl only. New zones `000800`-`000802` (15/22, 18/26, 22/30, NeverResets); source zones
`0126F3` 12/20, `0126F4` / `0126F5` 20/30 (source 15/0). Requiem.esp has 8 ECZN, all MaxLevel 0 (7 DLC2 book zones min 25,
one min 0). It never touches vanilla level zones.

**Worldspace.** 1,083 geometry + 290 empty-vs-absent noise; 6 flag rows (NoGrass dropped on 4, CannotFastTravel on 3),
mod-dependent, no Requiem comparable.

## Suspected bugs (for Aaron's review, not filed)

| Record | Plugin | Field | Value | Expected | Evidence | Class |
|---|---|---|---|---|---|---|
| `087901:Skyrim.esm` (REFR of FFRiften08BrandSheiChest) | Authoria - Reqtificated - Armours of Velothi.esp | Lock | absent → Master, Key null | absent (vanilla; no Requiem override; only Skyrim.esm and this patch touch it) | tree read; verifier flagged different-convention | outlier-bug (a Master lock on a Riften quest chest, added by an unrelated armour patch) |
| `AD3AFC:Requiem.esp` REQ_Chest_GlassShield_Dawnstar | Authoria - Master Patch - Container Merge.esp (outside the census corpus) | VirtualMachineAdapter | absent | Requiem's and WAR's `AA000XarrianLockBreakScriptNormal` | tree read: Requiem and WAR both carry it, Container Merge drops it while adding the WAR item `000A09`; the RFTI output for AD3AFC carries only REQ_LockpickControl, so Xarrian is lost in the final build | outlier-bug (the build re-adds the lockpick script, not this one) |
| 22 vanilla containers (e.g. `02064F`, `020662`, `03AC21`, `0774BF`) + `14CF41` DBM_JARBarrel; `105821` fcHawkGO | LOTD; Miahil Hawk and Tern | REQ_LockpickControl | absent | present, as on the plugin's other 312 records | ledger: 0 script rows; 02064F / 105821 VMAD read | outlier (harmless while RFTI loads after; wrong only without the build) |

Cleared as deliberate: Vigilant `026E87` (player knapsack, lock removed); `09DA64` RequiresKey → Master (= Requiem).

## Open calls for Aaron

1. **Loot strip** on mod-own treasure chests (Glenmoril, Vigilant, Unslaad drop vanilla lockpick / soul-gem / potion
   lists): an ARR economy rule to teach, or a per-patch choice? Requiem's own boss chest keeps lockpicks (reader read, not
   re-verified).
2. **Xarrian lock-break script** on mod treasure chests: Bruma adds it to 28; no other patch does. Should a skill teach it?
3. **Encounter-zone caps**: hand-banded NeverResets zones with MaxLevel caps (Gray Cowl), when Requiem zones are min-only?
4. **Lockpick script on upstream patches**: carry it (the downstream stance, 98.5 % of records) or leave it to the build
   (fact 12's upstream stance)? The rebuilt skill needs one answer per stance.
5. **Vendor-chest rebalances and Respawns clears** (Bruma, Dac0da, S&S): intended?

## Limits

- **Unsampled families (leads only):** REFR EnableParent.Reference / Unknown / Versioning, Rotation, Lock.Unused /
  Unused2, LocationReference, Scale, RagdollData, Owner, BoundHalfExtents, IsMultiBoundPrimitive; ACHR Unknown /
  Versioning, PersistentLocation, Owner, defined, LocationReference, VMAD, Base, ActivateParents, EncounterZone; CELL
  Flags / Lighting / Regions-added / removed. REFR Base (225 rows) is **cannot-tell**.
- **No held-out check** for conventions 4-8, 13 (held-out rows 0); conventions 9 / 10 rest on 2-3 plugins.
- The ledger only records differences. An override identical to its provider has no rows, so per-record lockpick counts
  use `headers.json` record totals.
- **ksws03 load failure:** `ksws03_quest.esp` is excluded by houseCARL, so its 4 CONT, 2 DOOR and 5 + 2 defined records
  can't be read.
- `UNTOUCHED.md` has no CONT / DOOR / REFR / ECZN cases. Untouched coverage for this domain was not audited.
- Read on the eval profile instance (RFTI inactive there), so "winner" here is the patch, not the build output.
