# houseCARL recipes — scripts domain

Copy-ready call shapes for the script layer. All writes go into a houseCARL patch mod (`into=` an
active patch); verify every write with a read-back.

## Freshness probe (always first)

```
housecarl_load_order_status
# must show YOUR Requiem MO2 instance/profile — the instance, not a record winner, establishes authority
read_record formid="012EB7:Skyrim.esm" conflict_tree=true
# sanity check: Requiem.esp must appear in the override chain. Winner = Requiem.esp (overlay disabled)
# or Requiem for the Indifferent.esp / a later patch (live profile) are BOTH valid; the live winner is
# the authority to derive from. Never set_mo2_instance because the Reqtificator's output wins — only
# when houseCARL is reading the wrong instance: housecarl_set_mo2_instance path="<your MO2 instance>"
```

## Coverage audit (whole-plugin bulk pass)

On a whole-plugin job the script layer has no "needs a script" field to enumerate, so build the
denominator with two sweeps before dispositioning anything (full doctrine: the skill body's *Bulk
pass protocol*). Neither call writes.

**Sweep (a) — records that already carry a script.** The `where=` `exists` presence test matches a
carried struct, so one query sweeps every record type at once — no per-type fan-out, no host type
missed:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] where=["VirtualMachineAdapter exists"]
# every match carries a script (QUST result scripts, ACTI activators, FURN stations included).
# Expand the hits for disposition:
housecarl_batch_record_detail formids=[<hits>] fields=["VirtualMachineAdapter"] depth=3
```

Disposition each hit: working modded script → preserve · reuse-Requiem-runtime candidate → clone path
· incidental vanilla carry (e.g. `MG01FireEffectScript`) → leave · `REQ_NULL_*` → strip.

**Sweep (b) — records with no script that need one.** These carry no VMAD, so sweep (a) can't see
them; enumerate from named proxies instead:

```
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="BOOK"     # every tome → multi-spell check
housecarl_cross_plugin_query plugins=["<NewMod>.esp"] type="SPEL" fields=["EffectList"]  # cross-check the magic pass's special-mechanic flags
# + the magic pass's flagged special-mechanic MGEF/SPEL hand-off list, and every follower NPC_
```

Disposition each candidate: script attached → clone path · marker-keyword reused (`Nox_KW_*`/FormList)
· confirmed no-script-needed (stated per record). Close each sweep with a reconciliation count:
dispositioned = enumerated. A special-mechanic record in neither queue is the "casts but does nothing"
failure.

## Read a record's script attachment

```
read_record formid="<MGEF/SPEL/BOOK>" fields=["VirtualMachineAdapter"] depth=3
# shows Scripts[0].Name, Flags, and every Properties[i] (Name + Object/Data)
```

Confirm a plain effect has none:

```
read_record formid="01CE9F:Skyrim.esm" fields=["VirtualMachineAdapter"]   # bound sword effect → (absent)
```

## Clone a Nox script onto a new effect, then repoint

1. Read the comparable's VMAD (e.g. the Teleport effect `02CBCE:Requiem.esp`) at `depth=3`.
2. Write the same `VirtualMachineAdapter` onto your MGEF, then set each object property to your form.
   The field paths mirror the read structure:

```
bulk_apply
  plugin_into="houseCARL - Requiem script patching"
  ops=[
    # set the script entry (name + flags); reproduce Version/ObjectFormat from the comparable
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Version","value":5},
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.ObjectFormat","value":2},
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Name","value":"Nox_Conjuration_Teleport"},
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Flags","value":"Local"},
    # repoint each ScriptObjectProperty to YOUR forms (name must match the .psc property exactly)
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Properties[0].Name","value":"Mark"},
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Properties[0].Object","value":"<MyMark>"},
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Properties[1].Name","value":"Park"},
    {"formid":"<MyEffect>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Properties[1].Object","value":"<MyPark>"}
    # …XarrianCell, SummonFX, etc.
  ]
```

If houseCARL's modeled write path for a freshly-built VMAD differs (e.g. it needs the whole
`Scripts` list created in one `create_record`/`Set` of the structured value), build the structured
value to match the read shape exactly — same `Name`/`Flags`/`Properties[i].Name`/`Object` keys. The
read at `depth=3` is the schema; mirror it. Use `mutagen-reference` to confirm the
`VirtualMachineAdapter` / `ScriptEntry` / `ScriptObjectProperty` field names if a path is rejected.

3. Read back and verify:

```
read_record formid="<MyEffect>" fields=["VirtualMachineAdapter"] depth=3
# every property Name present, every Object repointed to your forms, no REQ_NULL, masters correct
```

This winner read sees your VMAD only once the patch is enabled + sorted in MO2. Before that, the
write call is the verification: the per-op read-back, or `full_readback=true` (houseCARL 1.2.3+)
for the ENTIRE written record re-read from the patch file on disk. A `read_record
plugin="<patch>.esp"` against a not-yet-enabled patch fails with a named "not in the load order"
error — if the write reported success the edits landed; never re-issue them.

## Multi-spell tome (BOOK + Nox_TomeMultipleSpells)

```
# attach the script to the tome's record and fill the spell array
bulk_apply
  plugin_into="<patch>"
  ops=[
    {"formid":"<MyTome>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Name","value":"Nox_TomeMultipleSpells"},
    {"formid":"<MyTome>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Properties[0].Name","value":"SpellsToLearn"},
    {"formid":"<MyTome>","verb":"Set","path":"VirtualMachineAdapter.Scripts[0].Properties[0].Objects","value":["<Spell1>","<Spell2>"]}
  ]
# Teaches=none for a multi-tome (the script adds the spells on OnEquipped); value/weight via requiem-magic-patching
```

## Compile a new script (rare)

```
housecarl_compile_script script="D:\\...\\Source\\Scripts\\MyEffect.psc" into="houseCARL - Requiem script patching"
# on error: name(line,col): message → fix .psc (papyrus-reference for signatures) → recompile
```

## Masters & REQ_NULL stripping (every patch)

- A patch references a form ⇒ houseCARL adds + load-order-sorts that form's plugin as a master. Verify
  the `masters:` read-back line; it must be correct and load-order-sorted (the masters/`REQ_NULL`
  rule carried by the `requiem-patching` skill).
- Force `Requiem.esp` in by referencing a real Requiem form (no empty-master verb).
- Scan every property/reference for `REQ_NULL_*` (houseCARL resolves them live by EditorID). Strip or
  replace with the live form; **none may remain**. Re-read after the write to confirm.
