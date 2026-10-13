# Ancients Invisible Wall: How It Really Works

> **Short version:** when Korlic starts a Leap Attack, the game reserves his landing spot as unwalkable.
> Opening a Town Portal on Arreat Summit **deletes the Ancients instantly**, so he never lands and the reservation is never cleared.
> What's left is an invisible block that stops walking but not missiles or Teleport. Repeat it to build a wall.
>
> **Untested variant:** the same should work with a Town Portal during **Talic's Whirlwind**, with a much wider timing window.

---

## 1. How the leak happens

### Step 1: Korlic reserves his landing spot when the leap starts
- Korlic (`ancientbarb3`) uses **MonLeapAttack** (server start function 41).
- At leap start (`SKILLS_SrvSt41_LeapAttack`) the game writes into the collision grid at his landing spot:
  - the **player bit** (`COLLIDE_PLAYER`, `0x80`) on a 5-subtile plus shape,
  - the **`NO_PATH`** bit (`0x1000`) on the centre subtile.
- **`NO_PATH` blocks walking** for both players (`COLLIDE_MASK_PLAYER_PATH`) and monsters (`COLLIDE_MASK_MONSTER_PATH`).

### Step 2: only a normal landing clears it
- The reservation is removed in exactly one place: the landing branch of `SKILLS_Leap`, which runs only when Korlic reaches the landing spot.

### Step 3: a Town Portal deletes the Ancients on the spot
- Opening a Town Portal on Arreat Summit (`LEVEL_ROCKYSUMMIT`) calls `ACT5Q5_OnPortalOpened`, which runs `ACT5Q5_DeactivateAncientMonsters`. For each of the three Ancients it:
  - puts him into **DEAD mode**,
  - **deletes him** and removes him from his room,
  - turns his statue back to stone.
- This happens **immediately, even mid-leap**.

### Result
Korlic never lands, so **the `NO_PATH` bit stays in the grid**. That's the invisible block.

---

## 2. How big one block is

- All three Ancients are **size 2** (`monstats2.txt`), which uses the **small collision pattern**.
- One leaked block is **`NO_PATH` on a single centre subtile**, plus a harmless player-bit plus around it.
- Walking units are tested with their **own 5-subtile plus shape**, so a unit can't stand anywhere that shape would overlap the block. In practice each block is a no-walk plus shape.
- To form a wall that can't be squeezed through, **blocks must be 3 subtiles apart or closer**.

That's why it takes many repeats, and why people build it across a **short gap**, such as between the pillar and the arena edge.

---

## 3. What the wall blocks and what it doesn't

| Thing | Blocked? | Why |
|---|---|---|
| Walking or running monsters (the Ancients, their minions) | ✅ Yes | The monster movement mask includes `NO_PATH`. |
| You, walking or running | ✅ Yes | The player movement mask includes `NO_PATH`. |
| Your missiles and spells (Blizzard, Hydra, arrows, traps…) | ❌ No | Missile collision masks don't include `NO_PATH`. |
| Your Teleport | ❌ No | The player flight check only looks at doors and missile barriers. |
| Korlic's Leap Attack | ❌ No, he can jump over | The flight path only checks doors and missile barriers. Only his **landing spot** has to be free of `NO_PATH` (check mask `0x1C09`). |
| Talic's Whirlwind | ⚠️ Partly | Whirlwind movement *is* tested against `NO_PATH`. He most likely slips through gaps between blocks, so a denser wall should help. (Inferred, not confirmed in the code.) |

---

## 4. How to do it

1. **Clear the area** and position yourself so Korlic will leap toward the spot you want blocked.
2. **Activate the altar.** The Ancients come to life.
3. **Watch for Korlic's leap.** **Start casting Town Portal just before he jumps.**
   - The block exists from the moment the leap **starts** until he **lands**.
   - The portal has to **appear** inside that window, and it appears at the end of your cast animation.
   - Leap Attack's minimum airtime is **12 frames (0.48 s)**, so the window is short.
4. The portal opens, all three Ancients vanish, and **the block stays where Korlic was going to land**.
5. **Close the portal** (see the next section), **re-activate the altar**, and repeat.

### Re-activating
- The altar **can't be activated while any portal of yours is still open on the summit**. The game tracks this with `nActivePortalCount` and resets `bActivatable` when a portal opens.
- Close the portal first. If you go through it, **be back within about 4.8 s** (see section 5).

---

## 5. Why the wall can disappear

- The blocks live in each room's **collision grid**. When a room unloads, its grid is thrown away and rebuilt from the map tiles next time, **without the blocks**.
- **Arreat Summit is special.** Its rooms can only unload once **no room on the whole level** is in your sight (`DRLGACTIVATE_TestRoomCanUnTile`, `LEVEL_ROCKYSUMMIT`). In practice that means leaving the level.
- After you leave, rooms unload after **11 empty checks, one every 12 frames: about 4.84–5.28 s**.
- A room with an **open portal never unloads**. The room your portal is in stays loaded while the portal is open.

So **a trip to town under about 4.8 s keeps the wall**. Anything longer and it's probably gone.

---

## 6. Untested variants the code predicts

### A. Talic's Whirlwind plus a Town Portal ⭐ (strongest lead)
- Talic (`ancientbarb1`) uses **Whirlwind** (server start function 38).
- At whirlwind start (`SKILLS_SrvSt38_Whirlwind`) the game reserves the **end point of the whirlwind path** the same way: the unit bit plus **`NO_PATH`**.
- That reservation is only released when the whirlwind **finishes normally**.
- A Town Portal opening mid-whirlwind deletes Talic, so **the block should leak just like Korlic's**.
- **Why it's easier:** Talic whirlwinds often, and each whirlwind lasts far longer than Korlic's roughly 0.5 s leap, so the timing window is much wider.
- **Where it lands:** at the end of his whirlwind path, which is usually past your position.

### B. Two blocks from one portal
- One portal deletes **all three Ancients at once**.
- If Korlic is mid-leap **and** Talic is mid-whirlwind at that moment, you should get **both blocks from one portal**.

### C. Ghost spots after normal leaps block monster missiles (corrected)
- When a **monster** lands, the code clears the **monster** bit (`0x100`) instead of the **player** bit (`0x80`) that the leap set at takeoff.
- So every normal Korlic leap leaves a 5-subtile "player" spot where he landed. **It doesn't block walking.**
- *Correction:* an earlier version of this file said missiles probably keep going through the spot. A fuller read shows the opposite. Monster missiles aimed at players (collide types 1, 3 and 8) are traced against that bit, and a path cut short by it **bursts with no target** (`MissMode.cpp:664-667`, `PathMisc.cpp:377-405`).
- **Effect:** each landing spot works as an invisible **shield against monster missiles**. **Madawc's "ancient throwing axe" is collide type 3**, so his axes should burst on Korlic's old landing spots.
- Probably erased if you walk over the spot (your own footprint uses the same bit). That's inferred, not traced.
- **Confirmed in the code, not tested in-game.** See `glitch-test-plan.md`, Test 2.

---

## 7. Reference

### D2R data (3.3.94012)

| Ancient | Monster ID | Skill | Server start function |
|---|---|---|---|
| Talic | `ancientbarb1` | Whirlwind (`seq_ancientwhirlwind`) | 38 |
| Madawc | `ancientbarb2` | Shout | n/a |
| Korlic | `ancientbarb3` | MonLeapAttack (`seq_ancientleap`) | 41 |
| (D2R only) | | `Korlic's Leap Attack` (407), `Talic's Whirlwind` (410) | 41 / 38, same leak |

| Constant | Value |
|---|---|
| Ancients' size | 2, which uses the small collision pattern |
| Leap Attack minimum airtime | 12 frames = 0.48 s (`Param5`) |
| Room unload after you leave the level | 4.84–5.28 s |
| Server frame rate | 25 frames/s |
| Ancients' kill XP | 0 (no `Exp` value in `monstats.txt`) |
| Quest credit level | Normal 20 / Nightmare 40 / Hell 60, checked on **base level**; items and auras don't count |

### Collision bits

| Bit | Name | Blocks |
|---|---|---|
| `0x0080` | `COLLIDE_PLAYER` | Some monster missiles; doesn't block walking |
| `0x0100` | `COLLIDE_MONSTER` | Some player missiles; doesn't block walking |
| `0x1000` | `COLLIDE_NO_PATH` | **Walking for players and monsters** |

### Source code (D2MOO, Diablo II 1.10 server code)

| What | Location |
|---|---|
| Leap Attack reserves its landing spot | `D2Game/src/SKILLS/SkillBar.cpp` → `SKILLS_SrvSt41_LeapAttack` |
| Landing clears the reservation (monster branch clears `0x100`) | `D2Game/src/SKILLS/SkillBar.cpp` → `SKILLS_Leap` |
| Whirlwind reserves its end point | `D2Game/src/SKILLS/SkillBar.cpp` → `SKILLS_SrvSt38_Whirlwind` |
| A portal on the summit deletes the Ancients | `D2Game/src/QUESTS/ACT5/A5Q5.cpp` → `ACT5Q5_OnPortalOpened`, `ACT5Q5_DeactivateAncientMonsters` |
| Altar locked while a portal is open | `D2Game/src/QUESTS/ACT5/A5Q5.cpp` → `nActivePortalCount`, `bActivatable` |
| Level check and the Worldstone Keep door | `D2Game/src/QUESTS/ACT5/A5Q5.cpp` → last-kill handler, `OBJECTS_OperateFunction66_AncientsDoor` |
| Collision bit and mask definitions | `D2Common/include/D2Collision.h` |
| Collision patterns (set and reset) | `D2Common/src/D2Collision.cpp` → `COLLISION_SetMaskWithPattern`, `COLLISION_ResetMaskWithPattern` |
| Unit size to collision pattern | `D2Common/src/Path/Path.cpp` |
| Summit rooms unload only when the whole level is out of sight; portal rooms never unload | `D2Common/src/Drlg/DrlgActivate.cpp` → `DRLGACTIVATE_TestRoomCanUnTile` |
| Room unload timer | `D2Game/src/GAME/Game.cpp` → `GAME_UpdateProgress` |
| Missile collision masks | `D2Game/src/MISSILES/MissMode.cpp` → `stru_6FD2E5F8` |

---

## 8. Caveats

- **Source version:** the logic comes from **D2MOO**, the open-source rebuild of the **Diablo II 1.10** server code, not from D2R's own program. Your D2R 3.3.94012 data matches it on every value this depends on: the Ancients' skills and server start functions (41 and 38) and their size (2). That makes it **very likely** D2R behaves the same, but it isn't proven.
- **Section 6 is predicted from the code only** and hasn't been tried in-game.
- **Real-world delay:** input lag, cast speed and online latency affect the timing on top of these server-frame values.
