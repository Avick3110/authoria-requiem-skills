# VMAD attachment & compiling — the mechanics

## The VirtualMachineAdapter (VMAD)

A record's script attachment lives in its `VirtualMachineAdapter` field. Read it with:

```
read_record formid="02CBCE:Requiem.esp" fields=["VirtualMachineAdapter"] depth=3
```

Structure (from the live Teleport effect):

```
VirtualMachineAdapter
  Version = 5
  ObjectFormat = 2
  Scripts = [ list ]
    Scripts[0]
      Name  = Nox_Conjuration_Teleport          ; the .pex base name (must exist as a compiled script)
      Flags = Local
      Properties = [ list ]
        Properties[0]  Name=Mark        Object=02CBC8:Requiem.esp  Alias=-1  Flags=Edited   ; ScriptObjectProperty
        Properties[1]  Name=Park        Object=02CBCF:Requiem.esp  Alias=-1  Flags=Edited
        Properties[2]  Name=XarrianCell Object=02900B:Requiem.esp  Alias=-1  Flags=Edited
        Properties[3]  Name=SummonFX    Object=07CD55:Skyrim.esm   Alias=-1  Flags=Edited
```

Property kinds you'll meet: `ScriptObjectProperty` (a FormID — `Object=`), and primitive properties
(`Int`/`Float`/`Bool`/`String` with a `Data=` value). Array properties (`Spell[]`, `Weapon[]`,
`Weather[]`) hold a list of object values — match the count the script expects.

**The property names are fixed by the script source** — they must match the `Property` declarations
in the `.psc` exactly (`SpellsToLearn`, `BoundAbility`, `CharmFaction`, `Mark`/`Park`/`XarrianCell`,
…). Read the source under `…\Requiem - Magic Redone - rerun\scripts\source\` to get them right.

## Attaching a script with houseCARL

You don't write a new script to attach an existing one — you reproduce the VMAD on your record.
houseCARL writes the field via `set_field` / `bulk_apply`. The reliable pattern is **clone the
comparable's VMAD, then repoint the object properties** to your own forms (see
`housecarl-recipes.md`). After writing, **read it back** (`fields=["VirtualMachineAdapter"] depth=3`)
and confirm the script name, every property name, and every repointed `Object=` FormID.

Masters note: each property FormID pulls its defining plugin into the patch's masters. houseCARL
adds + load-order-sorts them automatically; verify the `masters:` read-back. The `.pex` itself is a
loose script file, not a master — it just has to be present in the load order (it is, since MR is
active) or shipped in your patch.

## Compiling a new script (`housecarl_compile_script`)

Only when authoring (step 4 of the workflow). Shape:

```
housecarl_compile_script
  script="D:\\...\\MyPatch\\Source\\Scripts\\MyNewEffect.psc"
  into="houseCARL - Requiem script patching"      ; optional: accumulate into an existing patch mod
  import_dirs="D:\\...\\SKSE\\Source;D:\\...\\OtherMod\\Source\\Scripts"   ; optional extra deps
```

- It invokes the CK `PapyrusCompiler.exe`. The vanilla source folder and the script's own folder are
  added to the import path automatically; add others with `import_dirs` (`;`-separated).
- Success → the `.pex` path (landed in a houseCARL patch mod you then enable in MO2).
- Failure → `name(line,col): message` per error. Fix the `.psc` (look unfamiliar calls up with
  `papyrus-reference`) and recompile.
- The CK compiler ships with the **vanilla Steam game install**, not a Wabbajack "Stock Game" copy —
  if houseCARL says the compiler path isn't set, it tells you exactly what to provide.

## Style guardrails for authored scripts

Match Requiem's own scripts — they are tiny and event-driven:

- Use `OnEffectStart`/`OnEffectFinish` (MGEF), `OnEquipped` (item), `OnInit`/`OnUpdate` one-shots
  (quest). Avoid `RegisterForUpdate` polling loops.
- Cache `Game.GetPlayer()`; don't call it every event.
- Don't store large persistent state on the script. Run it past `papyrus-optimization` before
  compiling.
- Gate player-only behavior explicitly (`akCaster == Game.GetPlayer()` and/or a perk `HasPerk` check),
  as `Nox_BoundWeapon` does with `MysticInfusion`.
