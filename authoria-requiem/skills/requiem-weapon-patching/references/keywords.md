# Weapon Keyword Vocabulary

Keywords are how Requiem's Reqtificator rules find a record — a wrong or missing keyword silently
changes how the weapon is balanced. All FormIDs verified live (2026-06-07). Where a material
keyword isn't listed below, **read it off the comparable** rather than guessing — the vanilla
`WeapMaterial*` set is large and the live read is the authority.

## The damage-type keywords are a Reqtificator OUTPUT

Four mutually-exclusive damage-type keywords (all `Requiem.esp`):

| Damage type | FormID | Assigned to (weapon-type) |
|---|---|---|
| slash | AD3954 | sword, greatsword, war axe, battleaxe (+ katana/dai-katana/shortsword) |
| ranged | AD3955 | bow (crossbow alongside) |
| pierce | AD3956 | dagger |
| blunt | AD3957 | mace, warhammer, quarterstaff (+ club, battlestaff) |

**Do not put these on a source record.** They are absent from every Requiem source weapon (the
Iron Sword winner carries only `[WeapMaterialIron, WeapTypeSword, VendorItemWeapon]`). The
Reqtificator assigns them at build time from the **weapon-type** keyword, via the
`feature_DamageType` block in
`…\Reqtificator\Data\WeaponKeywordAssignments_Requiem.esp.conf`:

```hocon
feature_DamageType {
    keywords_none = [ blunt, pierce, slash, ranged ]   # cleared first
    slash { keywords_assign = [slash]; keywords_any = [sword, greatsword, battleaxe, waraxe] }
    blunt { keywords_assign = [blunt]; keywords_any = [mace, warhammer, quarterstaff] }
    pierce{ keywords_assign = [pierce];keywords_any = [dagger] }
    ranged{ keywords_assign = [ranged];keywords_any = [bow] }
}
```

So: get the **weapon-type keyword** right and the damage type follows automatically.

## Weapon-type keywords (set these)

From the config's `weaponTypes` block + live reads:

| Type | EditorID | FormID |
|---|---|---|
| sword | WeapTypeSword | 01E711:Skyrim.esm |
| war axe | WeapTypeWarAxe | 01E712:Skyrim.esm |
| dagger | WeapTypeDagger | 01E713:Skyrim.esm |
| mace | WeapTypeMace | 01E714:Skyrim.esm |
| bow | WeapTypeBow | 01E715:Skyrim.esm |
| staff | WeapTypeStaff | 01E716:Skyrim.esm |
| warhammer | WeapTypeWarhammer | 06D930:Skyrim.esm |
| greatsword | WeapTypeGreatsword | 06D931:Skyrim.esm |
| battleaxe | WeapTypeBattleaxe | 06D932:Skyrim.esm |
| quarterstaff | WeapTypeQuarterstaff | ADDF81:Requiem.esp |

Requiem-added types reuse a base weapon-type keyword for damage-type purposes: shortsword/katana
use `WeapTypeSword`; dai-katana uses the greatsword/sword family; club uses the mace family;
battlestaff uses `WeapTypeQuarterstaff`. Confirm against the comparable.

## Material keywords (verified examples)

Vanilla `WeapMaterial*` keywords; copy the right one from the comparable. Verified:

| Material | EditorID | FormID |
|---|---|---|
| Iron | WeapMaterialIron | 01E718:Skyrim.esm |
| Steel | WeapMaterialSteel | 01E719:Skyrim.esm |
| Silver | WeapMaterialSilver | 10AA1A:Skyrim.esm |

The rest (Dwarven, Elven, Orcish, Glass, Ebony, Daedric, DraugrHoned, Falmer, DragonBone, …) are
vanilla keywords — read the comparable's `Keywords` and reuse the FormID it carries.

## Standard keyword sets by class

- **Melee weapon:** `[WeapMaterial<X>, WeapType<Y>, VendorItemWeapon 08F958]` — three keywords.
- **Bow / crossbow:**
  `[WeapTypeBow 01E715, WeapMaterial<X>, VendorItemWeapon 08F958, REQ_BowBreakable 2E232E,
  REQ_WeaponType_Bow{Light 9F9915 | Heavy}, RFTI_Exclusions_NoDamageRescale AD3B2D,
  RFTI_Exclusions_NoBowSpeedRescale AD3B2F]`. Plus `Data.Flags = NPCsUseAmmo`.
  The two `RFTI_Exclusions_*` keywords stop the Reqtificator rescaling WAR's hand-tuned bow
  damage/draw speed — they ARE stored in source (unlike damage-type keywords).
- **Staff:** `[WeapTypeStaff 01E716, VendorItemStaff 0937A4, Nox_KW_Staff_<School><Tier>]`. The
  `Nox_KW_Staff_*` keyword (MR, e.g. `Nox_KW_Staff_Destruction3` = `0076ED:Requiem - Magic
  Redone.esp`) is chosen from the staff's spell school + magnitude tier. See
  `enchanted-and-staves.md`.
- **Unique artifact:** bespoke — typically `[WeapType<Y>, VendorItemWeapon, <artifact keywords>,
  REQ_Tempering_<Perk>]` and sometimes a material keyword used for a damage interaction. See
  `worked-examples.md`.

## Useful shared keywords

| EditorID | FormID | Use |
|---|---|---|
| VendorItemWeapon | 08F958:Skyrim.esm | all sellable weapons |
| VendorItemStaff | 0937A4:Skyrim.esm | staves |
| REQ_BowBreakable | 2E232E:Requiem.esp | bows |
| REQ_WeaponType_BowLight | 9F9915:Requiem.esp | light-frame bows |
| RFTI_Exclusions_NoDamageRescale | AD3B2D:Requiem.esp | opt out of damage rescale |
| RFTI_Exclusions_NoBowSpeedRescale | AD3B2F:Requiem.esp | opt out of bow-speed rescale |
| REQ_Tempering_LegendaryBlacksmithing | AD3AF7:Requiem.esp | tempering-perk gate (artifacts) |
| MagicDisallowEnchanting | 0C27BD:Skyrim.esm | already-enchanted uniques |
| DaedricArtifact | 0A8668:Skyrim.esm | daedric artifacts |
