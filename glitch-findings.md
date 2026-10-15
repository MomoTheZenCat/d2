# Glitch Findings: Full Summary

Source: D2MOO (the open-source Diablo II 1.10 server rebuild) + D2R 3.3.94012 data. **Nothing tested in game**, and D2R may have fixed some of it.
Reproducibility: 1 = hard, 5 = works every time.

---

## A. Most useful

| # | Finding | In one line | Repro | Details |
|---|---|---|---|---|
| A1 | **Combat Shrine AR compounds** ⭐ | Take another while one is active: total AR 3× → 7× → 15×. The bonus is a snapshot at click time, so stack AR first | 4 | `combat-shrine-stacking` |
| A2 | **Stamina Potion duration stacks** ⭐ | Drinking while active sets end = old end + 30 s, no cap; 10 potions = 5 min unlimited stamina (Antidote, Thawing too) | 5 | `stamina` |
| A3 | **A Normal Khalim's Will works in Nightmare and Hell** ⭐ | The orb only checks base `qf2`; deletion skips lower-difficulty quest items. Keep it in Normal by trading while a party member smashes the orb | 3 → 5 | §C1 |
| A4 | **Baal and Diablo peek at your selected skills** | With Blizzard or Meteor selected (or Fire Wall 4+, Immolation Arrow 8+; charges count): Baal teleports away more; Diablo lasers less and charges / Fire Walls more | 5 | `boss-skill-peek` |
| A5 | **Charge ignores all slows** | Chill, Decrepify, Holy Freeze and heavy armor can't slow it; ~2.5× run speed, no stamina cost (`SkillPal.cpp:696-702`) | 5 | §C2 |
| A6 | **Leap Attack has no range cap** | Any clickable monster on screen is a jump point across lava, rivers or void (`D2Skills.cpp:3271-3335`) | 4 | §C2 |
| A7 | **Baal: behind a barrier and no dangerous skill selected at think time** | He doesn't teleport or use any ranged attack, only tentacles and curse. Cast Blizzard or Meteor by hotkey, then swap back (need to confirm the Worldstone Chamber has missile-barrier tiles) | ? | §C3 |

---

## B. Design flaws: did something, didn't clean up (same class as Korlic's leap)

| Flaw | What's left behind | Where |
|---|---|---|
| Monster leap (Korlic and others) | a "player" spot on the landing point that eats monster missiles | `SkillBar.cpp` (test plan) |
| Sand Maggot emerges, then walks away or dies | a cross of blocked subtiles (walking, pathing, portal placement) | `SkillMonst.cpp:199/214` |
| Sand Leaper / Nest killed mid-action | a "monster" spot that eats player missiles | `SkillMonst.cpp:478/689` |
| Vulture-type monsters flying up or burrowing | only the centre subtile is cleared; 4 "monster" subtiles remain | `AiThink.cpp:1969/1995/4972` |
| Bone Armor's final hit | pool zeroed before subtracting, so that hit isn't absorbed (possibly a D2MOO error) | `SkillNec.cpp:2118-2127` |

---

## C. Details

### C1. Khalim's Will across difficulties
1. Normal: B holds the Will, is partied with A, is in Act 3, and is in a trade (or trade request).
2. A smashes the orb: B gets the credit and keeps the Will (`A3Q5.cpp:314-323`); the later Mephisto-portal purge is skipped (`Quests.cpp:2245`).
3. B avoids Cain's Travincal dialogue (message 626).
4. Nightmare / Hell: wield the Normal Will in the left hand and hit the orb twice. Durance opens and the Will survives (`Items.cpp:1613`).

### C2. Charge, Leap Attack
- Normal movement = base × (100 + FRW) + velocity modifier. Charge uses only the modifier, with anything below +50 treated as +50.
- Leap is range-capped (`SkillBar.cpp:1320`); Leap Attack isn't, and its path check only tests doors and the no-jump flag, not walls. Act 4 alone has 6,318 subtiles that block walking but allow jumping.

### C3. Baal's weights (behind a missile barrier, solo, near his spawn)
| Action | No dangerous skill | Dangerous skill selected |
|---|---|---|
| Teleport away | **0** | 50 |
| Cold missiles | **0** | 50 |
| Nova, Inferno | 0 | 0 |
| Idle / walking / curse / tentacles | 200 / 105 / 80 / 70 | same |

---

## D. Lower value, but holds up

| Finding | In one line |
|---|---|
| Stamina: stop 0.16 s when empty | At 0, walking doesn't regenerate; standing 4 frames gets you past 1 point, then it does (`EVENTS_StaminaRegen`) |
| Slower stamina drain at 100% | only 1/256 point per step; Treads of Cthon + Hustle + Soulfeast Tine = 105% |
| Static Field | cancels negative lightning resist, so Lower Resist first adds nothing |
| Mephisto targeting | the "closest player" branch is broken; ~1/3 of spells go to the lowest-HP unit, summons included |
| Nihlathak | within 14 tiles and no corpses near you, he mostly uses Arctic Blast |
| Clicking stairs mid-cast | skips the exit-walk; at most 5–8 frames |
| Charge / Whirlwind into town | the start check only looks at your own position |
| Handing in the Horadric Malus | wrong iterator: the party gets Blood Raven credit (free Kashya merc) but not the imbue |
| Killing Nihlathak | wrong iterator: party members get Anya credit (Malah's resist scroll) |
| Non-combat shrines | different types can be active together |
| Town portals | the quest check is skipped when the owner can't be found (`ObjMode.cpp:3212`); only useful if a portal can lose its pair; unverified |

---

## E. Ruled out (the code checks properly)
- **Progression gates:** re-checked on warp (`QUESTS_LevelWarpCheck`), covering Duriel's Lair, Durance of Hate, the Ancients and the Worldstone Chamber.
- **Waypoints:** destination activated, same act, distance (Sorceress 22 is Telekinesis).
- **Compelling Orb:** base `qf2`, two hits.
- **Diablo:** 5 seals plus the seal-boss counter; only those 3 bosses count, once each.
- **Ancients:** the counter is guarded; kills don't count while a portal is open.
- **Baal waves:** already known.
- **Other:**
  - Act travel needs your own quest flags.
  - Rewards can't be claimed twice.
  - The same shrine only refreshes, doesn't stack.
  - Player states can't become permanent: freezing doesn't affect players, and Save & Exit clears them.
  - Leap crossing terrain is intended.
  - XP formulas are all known rules.
  - Bosses can't be stunned or frozen.
  - Charge and Leap are cleared on area change.

## F. Probably D2MOO transcription errors (don't test)
- Level-1 skills charging minimum mana (`Skills.cpp:1084-1094`)
- The inverted "slows target" check (`SkillItem.cpp:1565`)
- Lightning Strike's first-jump coordinates (`SkillAma.cpp:909`)

## G. Packet-only (not legal, not counted)
- Clicking a stairs tile from range (warps within 4 subtiles)
- Packet 0x44 making an object eat an item
- Warriv's travel option missing one check
