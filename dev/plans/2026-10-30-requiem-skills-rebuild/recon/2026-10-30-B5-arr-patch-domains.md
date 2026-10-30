# B5 — Which domains ARR actually patches

*Author: Aaron (+ Claude session) · 2026-10-30*

*Class: ARCHIVE (finding). Recon pass 2, target B5. Source: a read-only survey subagent of the live
ARR instance on 2026-10-30, using the shipped list profile `Authoria - Requiem Reforged` (3,350 of
3,644 modlist entries enabled). Nothing was written.*

## Method and confidence

- **Every plugin parsed.** Record types for all 250 plugins come from a structural parse of their
  GRUP/record headers. This shows which types each plugin overrides, not which fields it changes.
- **Field content checked on 3 plugins only:** Snazzy Resources, Witcher Horses and Grand
  Solitude, through houseCARL.
- **Two counts cover all 250:** REQ_ scripts on CONT/DOOR, and REQ_ perks on NPCs.
- **What is not verified:** each patch's domain *intent*, i.e. whether it derives stats or
  forwards fields. The sample above is all there is.

## Where ARR's patches live

- **Almost all of ARR's own patches are one mod, "Authoria - Requiem Patches": 250 plugins, all
  enabled.**
  - 245 are `Authoria - Reqtificated - <mod>.esp`.
  - The rest: the CC patches (Curios, Fishing, Saints & Seducers, Survival), Resource Pack,
    Werewolf Transformation Fix, and Praedy's Staves.
- **Requiem-adjacent mods outside it:**
  - an edited `Requiem.esp`, a full master replacement, excluded from the counts;
  - Modded Follower Requiem Registration (1 QUST);
  - Leveling (GMST);
  - Dragon Tweaks;
  - Modded Outfit Distribution.
- **About 45 third-party Requiem patches**, e.g. the Redone family, Magic Redone + MR patches,
  FIM, Hand To Hand, VampireCollection, Wyrmstooth, Headhunter.

## Domain counts (of 250 ARR patches; one patch can hit several)

| Domain | Patches | Records | Skill today |
|---|---|---|---|
| NPCs (NPC_/CLAS) | 115 | 5,504 | npc |
| Armour / clothing | 107 | 3,805 | armor |
| Crafting (COBJ) | 95 | 5,353 | inside armor / weapon |
| **Containers (locks)** | **88** | **1,408** | **none** |
| Weapons | 65 | 2,615 | weapon |
| Cells / placed refs | 57 | 550 | leveled-list / placement |
| **Doors (locks)** | **48** | **414** | **none** |
| Magic (SPEL/MGEF/SCRL/ENCH/LVSP) | 43 | 2,683 | magic |
| Races | 24 | 495 | race |
| Leveled items | 21 | 564 | leveled-list |
| Ammo / projectiles | 20 | 78 | ammo |
| Consumables (ALCH/INGR) | 17 | 44 | consumable |
| Books / tomes | 16 | 413 | magic (tomes) |
| Perks | 15 | 138 | perk-assignment |
| Quest / dialogue | 12 | 64 | (quest lines in G) |
| Flora / misc | 19 | 107 | none |
| Leveled NPCs | 6 | 51 | leveled-list |
| Outfits | 6 | 34 | npc |
| Shouts | 2 | 9 | none |

## Findings

1. **Locks are the hidden big domain, and no skill owns them.**
   - 136 patches touch CONT or DOOR, and every CONT/DOOR in scope carries a REQ_ script
     (`REQ_LockpickControl` in the sample).
   - 40 plugins are *only* CONT/DOOR/cell edits.
   - Ties in with the false fact "lockpicking as a DLL" (DISTILLATION §3a). The eval never tested
     a lock task.
2. **The commonest single shape is armour + COBJ only: 48 plugins**, mostly outfit and armour
   mods. Aaron's planned armour session matches the modal job.
3. **NPC patches are often forwarding or merge work, not stat derivation.**
   - Example: Witcher Horses re-merges Requiem's TemplateFlags with the mod's WornArmor.
   - About 20 creature/animal mod patches.
   - 4,899 winning NPCs in scope carry REQ_ perks.
4. **Big-mod patches cover every domain at once:** Vigilant, Glenmoril, Unslaad, Dac0da, LOTD,
   Apocalypse.
5. **Scripts are VMAD attachments only.** Nothing is compiled per patch, which supports retiring
   the script skill.

## Untested domains (intent open question 3; DISTILLATION §4d)

| Domain | ARR evidence | Suggests |
|---|---|---|
| Followers | NPC patches for 5 followers (Auri, Inigo, Lucien, Remiel, Kaidan) + the registration quest | Short; registration convention is a direct call (B6 #6) |
| Playable races | 24 race patches (playable vs creature not split) | Keep short; check the split |
| Vampirism / lycanthropy | 1 fix + third-party patches | Minimal |
| Shouts | 2 | Minimal or none |
| Diseases | 0 records | Drop |
| Standing stones | 1 (refs only) | Drop or 1 line |
| Food / alcohol / drugs, poisons | 17 consumable patches, 44 records; FIM handles food | Short |
| Scrolls | Inside the 43 magic patches | Fold into magic |
| Player ammo / forge | 20 ammo patches, 78 records | Short, inside weapon (as proposed) |
| Leveled-list backbone placement | 21 LVLI + 57 cell/ref patches | Keep |
| Script authoring | No compiled scripts | Drop (as proposed) |
| **Locks (new)** | **136 patches** | **Needs an owner: the biggest gap found** |

## Leads, not filed

- houseCARL reports `ksws03_quest.esp` as a load failure. It may be a list issue or a reader
  issue. Unverified, so not filed to BUGS.md.
