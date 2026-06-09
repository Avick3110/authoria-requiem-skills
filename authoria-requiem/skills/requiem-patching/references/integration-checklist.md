# Integration checklist & the Reqtificator-vs-manual matrix

Run this at the end of every "patch mod X for Requiem" job. It is the cross-cutting gate that no
single domain skill owns.

## The final checklist

1. **Coverage.** Every record type from `cross_plugin_query plugins=["<NewMod>.esp"]` was either
   routed to a domain skill and patched, or deliberately skipped as cosmetic. No type left unhandled.
2. **Gap mechanics.** Every system the content implies (vampire, disease, alchemy, economy, …) had its
   reference's constraints applied — not just the raw record.
3. **Carry inputs, not outputs.** Verify against the matrix below that you carried the input keywords
   and left every Reqtificator-assigned output alone.
4. **Masters.** Each patch's `masters:` read-back is correct and **load-order-sorted** (houseCARL adds
   + sorts from referenced forms). `Requiem.esp` is a master wherever a Requiem form is referenced;
   force it in (reference a real Requiem form) where you specifically need it. See
   `masters-and-null-stripping.md`.
5. **No `REQ_NULL_*`.** Scan every patched record's reference fields (Perks, Spells/ActorEffect,
   Keywords, inventory, Template, list Entries, COBJ conditions, VMAD properties). houseCARL resolves
   `REQ_NULL_*` EditorIDs live — strip or replace every one; **none may remain**, and never add one.
6. **Run it through the Reqtificator.** The patch is an input to the auto-balance pass. State that
   explicitly as the last step; the player runs the Reqtificator after installing the patch.

## The Reqtificator-handles-this vs needs-manual matrix

The cross-domain rollup of "what you carry" vs "what the build assigns." Carrying an output by hand is
the most common integration error — it double-applies or fights the build.

| Domain | You CARRY (input) | The Reqtificator ASSIGNS (don't stamp) |
|---|---|---|
| Weapons | weapon-type + material keywords, damage/value/speed (live analogy), tempering/forge COBJ | damage-type keyword (slash/blunt/pierce/ranged) → armor-pen chain |
| Armor | armor-type + set + part keywords, AR/value/weight, heavy-gauntlet fist perk | ranged-resistance tier, tempering perk, material resist bonus (cuirass) |
| Ammo | **everything** — material/AP/AmmoWeight keywords, damage, PROJ, forge COBJ | *(nothing — ammo has no assignment rule; even RFTI exclusions are carried)* |
| Race | trait spells on the RACE (engine-inherited), stats, skills, keywords | the per-NPC incoming-damage trait perk (by race/keyword) |
| NPC | fixed level, class, combat perks (source-carried), tempering/resist trait spells, follower factions | trait + state perks (vampire/ghost/…), all-actor game-mechanics perks |
| Leveled lists | the new entry (additive, Level 1, weighted by repetition) | the list merge across eligible plugins (if ≥3 candidates) |
| Magic | MGEF/SPEL/ENCH design, explicit cost, HalfCostPerk classifier, keywords/flags | `RFTI_All_*` rescaling perks (absorb/poison/ward/persistent) |
| Vampire/were | the vampire keyword `0A82BB` / werewolf race | `RFTI_Trait_Vampire AD3A44` / `RFTI_Trait_Lycanthrope_Werewolf AD3A43` |
| Exhaustion/stress | standard weapon/armor keywords | all-actor stress/exhaustion perks + controller spells |
| Alchemy | 4 ingredient effects pointing at `REQ_Alch_*` MGEFs, harmful keyword | poison rescaling (`962798`) |
| Combat | weapon-type/armor-set/race keywords (the chain) | armor-pen perks, resistance tiers, racial/state traits |

Player skill perks (alchemy/enchanting/lockpicking/pickpocket/sneak/speech/tempering) are
**player-exclusive Reqtificator assignments** — never put them on an NPC or hand-stamp on the player.

## Quick "is this patchable at all?" notes

- **Lockpicking** = SKSE DLL, not record-patchable.
- **Vendor pricing** = record-driven (item value + vendor container), no config file.
- **Appearance** = handled by the visual auto-merge; don't write it from a balance patch.
- **Reqtificator output records** (tempered-quality seed lists, the damage-type keyword, `RFTI_*`
  perks) = don't author; they're build artifacts.
