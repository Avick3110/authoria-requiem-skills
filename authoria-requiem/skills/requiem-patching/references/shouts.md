# Shouts (Thu'um)

Shouts are owned by `Requiem.esp` (~29 SHOU). A shout is a fixed structure: **3 Words of Power, each
mapping to one SPEL, each with its own recovery (cooldown) time** that grows with the word tier.

## The shape

Example — Become Ethereal (`BecomeEtherealShout 032920:Skyrim.esm`, winner Requiem.esp):

```
WordsOfPower[0]  Word=032917  Spell=05F6EB  RecoveryTime=30
WordsOfPower[1]  Word=032918  Spell=05F6EC  RecoveryTime=40
WordsOfPower[2]  Word=032919  Spell=05F6ED  RecoveryTime=50
```

So a shout = a `Shout` record (SHOU) + 3 `WordOfPower` (WOOP) + 3 `Spell` (SPEL, the actual effect at
each tier) + the per-word recovery. The SPELs carry the MGEFs (the actual magic).

## Shout tuning in Requiem (design notes)

- Cooldowns are reduced by specific sources only: North Heritage (+20% strength/duration), Ancient
  Nord Helmet of the Tongue (−10% cooldown), Kyne's Token milestones (−10% each, up to −40%), Voice of
  the Sky (−10% for a day). New shout content shouldn't add free global cooldown reduction.
- Some vanilla shouts are re-scoped (Kyne's Peace restores attributes instead of pacifying animals;
  certain NPCs know a curated word set).

## Integration recipe

- **A new shout** → create the SHOU with 3 WOOP + 3 SPEL; design the 3 tier SPELs/MGEFs via
  `requiem-magic-patching` (cost is generally N/A — shouts use recovery time, not magicka), set
  RecoveryTimes climbing by tier off a comparable Requiem shout. Place the word walls / teaching via
  the quest/world side (out of the balance patch surface) or grant the shout directly if that's the
  mod's intent.
- **A modded shout that's too strong** → rescale its 3 tier SPELs to a Requiem comparable and fix the
  recovery times; don't leave vanilla-strength magnitudes.
- This is a niche path — most "patch for Requiem" jobs won't include shouts. Route the SPEL/MGEF work
  to `requiem-magic-patching`; this reference just supplies the SHOU/WOOP wrapper shape.
