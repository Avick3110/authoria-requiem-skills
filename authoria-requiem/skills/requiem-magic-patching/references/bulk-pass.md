# Bulk Pass Protocol (whole-plugin magic jobs)

The full procedure for patching a whole plugin's magic — routed here from `requiem-patching`, or any
job with more than a handful of magic records. `SKILL.md` carries the short form; this is the detail.

## The enumeration IS the work queue

Magic is the most uniform-*looking* domain in Requiem, and that surface uniformity is the trap. Its
records come in **family shapes**: rank chains (Novice→Master of one spell line), element triples
(the Fire/Frost/Shock variants of one spell), enchant tiers 01–06 of one effect, and tome values on a
fixed ladder. A modder hand-tunes individuals inside those families, so the fastest way to ship a
spell unbalanced is to read one member of a family and extrapolate to the rest.

**Never extrapolate across a family — read each member's own record before you patch it.** A rank
chain, an element triple, an enchant-tier run and a tome ladder each *look* mechanical, but the
tier-4 frost variant may carry its own cost, or one tome its own price. Reading the member costs one
query; extrapolating ships the T3 shock bolt with the fire bolt's magnitude, or an un-priced tome.

## The per-type sweeps

Sweep **each record type as its own coverage denominator** — one call per type, whole plugin at once,
none of these writes:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="Spell" \
  fields=["EditorID","HalfCostPerk","BaseCost","Effects"] format="dense"
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="MagicEffect" \
  fields=["EditorID","MagicSkill","ResistValue","Keywords","Flags"] format="dense"
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="ObjectEffect" \
  fields=["EditorID","EnchantType","CastType","EnchantmentCost","Effects"] format="dense"
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="Scroll" \
  fields=["EditorID","Value","Effects"] format="dense"
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="Book" \
  fields=["EditorID","Teaches","Value","Weight"] format="dense"
```

`format="dense"` returns one positional row per record under a single column header rather than a
labelled envelope per field — worth it on a spell-heavy mod, where each of these denominators can run
to hundreds. Page a big one with `limit=` + `offset=`; the windows tile exactly while the load order
is unchanged.

**Enumerate MGEF first-class.** The `MagicEffect` sweep is its own denominator — never reach an MGEF
only through the spells that reference it. A spell's effects are records in their own right, and the
single most common coverage failure is a spell whose cost + `HalfCostPerk` you patched while its
modded MGEF's magnitude/keywords/flags never got rebalanced. Filter the `Book` sweep to **tomes**
(those with `Teaches` present); a non-tome BOOK is out of scope.

**The sweep cannot answer the questions that matter.** `Keywords`, `Flags` and `Effects` all come
back as `[list: N item(s)]` — a count that reads like data. Every disposition that depends on their
*contents* needs a follow-up `housecarl_batch_record_detail` at `depth=2` (keywords/flags) or
`depth=4` (per-effect `Data.Magnitude/Area/Duration`).

**Two more sweeps ride the pass.** (1) The **NULL scan** — re-run the Spell/ObjectEffect/Ingestible
sweeps with `fields=["Effects"] resolve_names=true`; every `BaseEffect` resolving to `REQ_NULL_*` or
`REQ_DEPRECATED_*` is a silent no-op (modded resistance magic is the classic case) and gets re-pointed
same-lane, same-element per `resistance-map.md`. (2) **Hand pairs** — MR mirrors ~a third of spells as
`_LeftHand`/`_RightHand`; a modded pack with hand variants must have the pair dispositioned together,
or the spell behaves differently per hand.

## "Patched" has a definition, and scalars do not meet it

Every record gets a disposition: **patched** (note which workflow) or **skipped** (name the reason — a
pure-FX or art-carrier MGEF that carries no balance, a non-tome BOOK, a cosmetic-only effect). A skip
is verified on *that* record, never inherited from a neighbour.

A SPEL counts as **patched** only when **all** of these hold — anything less goes on the still-open
side of the reconciliation count:

| # | Requirement | Layer |
|---|---|---|
| 1 | `BaseCost` from the comparable **and** `ManualCostCalc` ticked as a flag union | economy |
| 2 | `ChargeTime` on the tier ladder | economy |
| 3 | correct `HalfCostPerk` | economy |
| 4 | **`Effects[i].Data` magnitude/area/duration derived from the comparable** — not the mod's originals | **balance** |
| 5 | the comparable's **subtype keyword(s)** carried on the MGEF, where it has any | **balance** |
| 6 | the comparable's **mechanical** subtype riders present, magnitudes copied | **balance** |
| 7 | primary MGEF's **behavioral keyword/flag signature** matching the archetype comparable | **balance** |
| 8 | no effect resolving to `REQ_NULL_*` / `REQ_DEPRECATED_*` | hygiene |
| 9 | teaching tome priced to the tier ladder | economy |

The failure mode this closes is a lane that sets cost + charge + flags + `HalfCostPerk`, leaves every
magnitude at the mod's value, and reconciles the type as complete. **A spell with the economy and none
of the balance is *cost-normalized*, which is not patched — it deals the mod's damage at Requiem's
price.** Items 4–6 are where a whole-plugin pass silently fails.

## Reconciliation

Close each type with a **count — patched + skipped = enumerated** — for Spell, MagicEffect,
ObjectEffect, Scroll and tome-BOOK each. If a type's two sides don't add up, a record fell through;
find it before calling the type done.

**Flags fields are unions, not scalars.** A SPEL or MGEF `Flags` write replaces the whole bitfield, so
read the winner's flags first and write original-bits + your change. A literal Set naming only the bits
you thought about silently strips `ManualCostCalc` (re-enabling auto-cost on every authored-cost
spell), `PowerAffectsMagnitude`, and their kin.

**MGEF cross-check** (closes "spell patched but its effects never rebalanced"). After the per-type
counts, reconcile the MGEFs **referenced by the spells you patched** against the enumerated
`MagicEffect` set: every modded effect a patched spell casts must itself be dispositioned. A spell is
not done while any of its own modded effects is undispositioned. (This is the per-record coverage the
`requiem-patching` skill's integration checklist gates on for high-count types.)

## The "already Requiem-correct" anti-pattern

The one that ships a whole plugin unpatched. The tempting shortcut is to scan the MGEF sweep, see
`MagicSkill` set, an element keyword present and a `MinimumSkillLevel` tier marker, and disposition the
record **skipped — already correct**. That trio is **necessary but not sufficient** — it is what a
competent vanilla-style mod ships anyway, and says nothing about whether Requiem's rules will match.

**"Already correct" requires a diff, not an eyeball:** name the record's **archetype comparable** in MR
and show its keyword set **equals** the comparable's (element swapped). Lacking a REQ behavioral keyword
the comparable carries, it is **unpatched** and belongs on the *patched* side with those keywords added.
Re-read candidates with `housecarl_batch_record_detail ... fields=["EditorID","Keywords","Flags"]
depth=2` and compare actual FormIDs. Method + verified archetype table: `keywords.md`.
