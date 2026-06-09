# Masters & REQ_NULL stripping — a cross-cutting patch-hygiene rule

*Read this before emitting any patch, in every domain (weapons, armor, ammo, races, NPCs, leveled
lists, magic, scripts). It governs two linked concerns: a patch must declare the **right masters**,
and it must carry **no `REQ_NULL_*` reference**. The two are linked because the master list is what
makes a `REQ_NULL_*` EditorID *visible* in the first place.*

## How masters work (the xEdit mechanism we mirror)

Source: the Tome of xEdit (master-file handling) + xEdit's own `Add/Sort/Clean Masters`.

- A **FormID is two parts**: the first byte is the **module index**, the last 3 bytes are the
  **module-specific (local) ID**. The module index points into the plugin's ordered `MAST` list,
  with the plugin's own new records indexed last (after all masters).
- A plugin must list as a **master** every file whose forms any of its records reference. If a record
  in `Patch.esp` overrides or points at a form defined in `MasterA.esm`, then `MasterA.esm` must be in
  `Patch.esp`'s `MAST` list — otherwise that first FormID byte indexes the wrong file and the
  reference is meaningless (or shows only as bare hex).
- **Add Masters** inserts the `MAST` entry **and renumbers** existing local FormIDs so their meaning
  is preserved (e.g. inserting a master shifts `03xxxxxx`→`04xxxxxx`); the **local 3-byte ID is never
  changed**, only the module-index byte is remapped.
- **Sort Masters** reorders the `MAST` list into **load order** and renumbers all file-specific
  FormIDs. Masters *must* be load-order-sorted because the indices are sequential from `00`; an
  out-of-order list corrupts every reference.
- **Clean Masters** removes masters no record references (then renumbers). We rarely need this when
  *emitting* a patch, but it's the inverse operation.

**houseCARL does Add + Sort automatically.** When a `bulk_apply` / `set_field` / `create_record`
references a form, houseCARL adds that form's defining plugin to the patch's masters and orders them
("the patch spans masters automatically … cross-master merge"). You normally don't manage `MAST` by
hand — you just reference the right forms and houseCARL emits the correct, load-order-sorted master
list. Verify it in the write read-back (the `masters:` line).

## Force `Requiem.esp` as a master when you need it

houseCARL only adds a master you actually **reference**. So:

- A patch that references any Requiem form (a `REQ_Class_*`, `REQ_Trait_*`, a Requiem keyword/perk/
  spell) auto-masters `Requiem.esp` — the common case; nothing to do.
- A patch that happens to set **only vanilla forms** (e.g. a flat level + a vanilla class, or a
  vanilla `ghost` keyword) will **not** carry `Requiem.esp` as a master. Usually fine — the
  Reqtificator keys off the vanilla form at build (e.g. the `ghost` keyword drives the ghost trait).
  But when you specifically need `Requiem.esp` present (so the Reqtificator resolves it, or so a
  Requiem form on the record stays meaningful), **reference a real Requiem form** to bring it in —
  there is no "add an empty master" verb; the master follows from a reference. (a future master-add
  helper may simplify this; until then, reference a Requiem form.)

## REQ_NULL — see them, strip them, leave none

`REQ_NULL_*` records are **inert stubs Requiem retired** (e.g. `REQ_NULL_RaceKhajiitClaws`,
`REQ_NULL_EncBandit01MissileNordM`, NULLed spells/perks/keywords/look-templates). They exist only so
that old references resolve to *something*; they do nothing useful and are only meaningful with
`Requiem.esp` as a master.

**Why masters matter here:** a `REQ_NULL_*` form is defined in `Requiem.esp`. If `Requiem.esp` is not
a master of the plugin you're reading the reference through, it shows as a bare hex FormID and you
can't tell it's a NULL stub. houseCARL reads against the **live load order** (Requiem active), so it
**does resolve and display `REQ_NULL_*` EditorIDs** — that is exactly what lets you find them. (In a
standalone patch, the same form would be invisible without the master — which is the other half of
why correct mastering matters.)

**The rule (every domain): no `REQ_NULL_*` reference may remain in any record you patch.**

1. **Find them.** When you read a record you're about to patch, scan its reference fields — `Perks`,
   `ActorEffect`/`Spells`, `Keywords`, `Items`/inventory, `Template`, leveled-list `Entries`, COBJ
   conditions, any FormLink — for EditorIDs beginning `REQ_NULL`. houseCARL resolves these names
   live; if a reference shows as bare hex, suspect a NULL stub whose master is missing and read it
   against the chain.
2. **Strip them.** Remove the `REQ_NULL_*` reference (`verb:"Remove"` from the list), or replace it
   with the correct **live** Requiem form if the mod meant a real one that Requiem renamed/retired.
   Never carry a `REQ_NULL_*` forward, and never *add* one.
3. **Verify.** After the write, re-read the patched record and confirm no `REQ_NULL_*` remains in any
   field.

This generalizes the race-domain lesson ("never keep or add a `REQ_NULL_*` record") to **all**
domains — items, actors, leveled lists, magic. A retired stub left in a patch resolves to nothing
useful and can mask a form the mod actually needed.

## Checklist (fold into every skill)

- [ ] Patch declares the correct masters (verify the `masters:` read-back; load-order-sorted —
      houseCARL handles this from your references).
- [ ] `Requiem.esp` is a master whenever a Requiem form is referenced (and forced in, by referencing
      a real Requiem form, when you specifically need it).
- [ ] **No `REQ_NULL_*` reference remains** in any field of any patched record (found via the live
      EditorID resolution, stripped or replaced with the live form).
