# Act 3 Kurast Sewers: Where Are the Stairs and Khalim's Heart?

Directions are **on screen** (isometric view). From D2MOO's map code (the open-source Diablo II 1.10 rebuild), D2R's map files, and a 60,000-game simulation. **Not tested in game yet.**

---

## 1. Sewers Level 1: a fixed ring with four entrances

The level always starts as a **ring of 16 rooms** (a 5×5 square with a hollow middle). Its four corners are the four entrances:

```
                                                 Upper Kurast
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                                            /                    \
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                   Kurast Bazaar  <       Sewers Level 1 (start ring)      >  Upper Kurast
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                                            \                    /
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
                                                 Kurast Bazaar
```

- **Kurast Bazaar** enters at the **left** or **bottom** corner (there are two trapdoors).
- **Upper Kurast** enters at the **top** or **right** corner.
- The rest of the maze (32 rooms total, 12×12 tiles each) grows randomly out of the ring.

### The stairs down to Level 2 (the room with the lever)
- **No position rule.** They're in each quarter of the map about equally often (23–24%), and about the same distance from every entrance (**6 rooms on average**).
- They're usually **outside the starting ring (92%)**, in the part that grew outward. So don't search around the ring itself: head out of it.
- Its facing is a random 25% roll after the maze is built. The (worthless) consolation chest room faces 90° clockwise from it.

### Earlier maps can't help here
The ring is fixed, and the stairs' roll comes after a variable number of maze draws, so nothing from Acts 1–3 lines up with it (see the "same-index" rule in [act5-caves.md](act5-caves.md)).

---

## 2. Sewers Level 2: a fixed map

Level 2 is **one fixed map** (`SewerTreasure1.ds1`, 18×22 tiles), the same in every game:

```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                       Arrival (mid-edge)   /                    \  Heart chest (mid-edge)
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <       Sewers Level 2 (fixed map)       >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                                            \                    /
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

- You arrive at the **middle of the top-left edge**.
- **Khalim's Heart chest** is near the **middle of the top-right edge**: on screen that's **straight to your right, about 11 tiles**. It's always there.

---

## 3. Source code and data

| What | Where |
|---|---|
| Ring of 16 rooms, 4 corner entrances, grow to 32 rooms | `D2Common/src/Drlg/DrlgMaze.cpp` → `LVLTYPE_ACT3_SEWER` case (`DRLGMAZE_InitBasicMazeLayout(pLevel, 5)`, 4 × `DRLGMAZE_ReplaceRoomPreset` for `SEWER_PREV_*`) |
| Stairs (drain) facing `R & 3`, chest room +90° | `DRLGMAZE_PlaceAct3SewerStuff` |
| Which corner goes where | warp tiles in `Act3/Sewer/Sewer{NE,NW,SE,SW}_Warp.ds1` → `Levels.txt` Sewers Level 1 `Vis0–3` (Kurast Bazaar ×2, Upper Kurast ×2); drain rooms hold objects 100 `BeneathTheCityLever` + 101 `BeneathTheCityStairs` |
| Level 2 is fixed | `Levels.txt` Sewers Level 2 = preset type, 18×22; `LvlPrest` "Act 3 - Sewer Treasure X" → `SewerTreasure1.ds1`, object 113 `KhalimHeartChest` |
| Room counts / merge chance | `LvlMaze.txt`: 32 rooms, 12×12, merge 800 |

## 4. Caveats
- **Source version:** D2MOO is the 1.10 code, not D2R's own program; the map files are D2R's.
- **Not tested in game.**
