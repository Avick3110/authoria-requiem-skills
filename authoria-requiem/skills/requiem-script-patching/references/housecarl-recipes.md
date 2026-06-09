# houseCARL recipes — scripts domain

Copy-ready call shapes for the script layer. All writes go into a houseCARL patch mod (`into=` an
active patch); verify every write with a read-back.

## Freshness probe (always first)

```
read_record formid="012EB7:Skyrim.esm" conflict_tree=true
# winner must be Requiem.esp; else point houseCARL at your Requiem MO2 instance: housecarl_set_mo2_instance path="<your MO2 instance>" and re-probe
```

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
