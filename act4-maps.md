# Act 4 Maps: Exit Chances

All directions are **on screen** (isometric view). From simulating D2MOO's map code (the open-source Diablo II 1.10 rebuild) with the real random number generator, 600,000 games. **Not tested in game yet.**

---

## 1. Pandemonium Fortress → Outer Steppes

**Always bottom-right.** The only random part is a small shift (8 tiles up or down), and that's decided by the same random number as the Cold Plains direction.

## 2. Outer Steppes → Plains of Despair

| Top-right | Bottom-right | Bottom-left | Top-left |
|---|---|---|---|
| **50%** | 25% | 25% | never (the Fortress is there) |

Same rule as Cold Plains: the roll is 25% per side, and a roll on the Fortress side moves to the next side clockwise (top-right).

```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                   = Pandemonium Fortress   /                    \  Plains of Despair 50%
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <              Outer Steppes             >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                    Plains of Despair 25%   \                    /  Plains of Despair 25%
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

## 3. Plains of Despair → City of the Damned

Same rule again. The side clockwise after the one you came in from is the most likely.

#### Plains of Despair at Outer Steppes' top-right
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                   City of the Damned 50%   /                    \  City of the Damned 25%
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <            Plains of Despair           >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                          = Outer Steppes   \                    /  City of the Damned 25%
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```
#### Plains of Despair at Outer Steppes' bottom-right
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                          = Outer Steppes   /                    \  City of the Damned 50%
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <            Plains of Despair           >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                   City of the Damned 25%   \                    /  City of the Damned 25%
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```
#### Plains of Despair at Outer Steppes' bottom-left
```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                   City of the Damned 12%   /                    \  = Outer Steppes
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <            Plains of Despair           >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
                   City of the Damned 25%   \                    /  City of the Damned 63%
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

## 4. Predict Plains of Despair from Act 1 ⭐

**Every act is generated from the same game seed.** The random number that places Blood Moor in Act 1 is the same number that places Plains of Despair in Act 4.
So once you note **the exits of Blood Moor and Cold Plains**, Plains of Despair is **100% decided in 75% of games** and 50/50 in the rest.

| Blood Moor → Cold Plains exit (side of Blood Moor) | Cold Plains → Stony Field exit (side of Cold Plains) | Outer Steppes → Plains of Despair exit (side of Outer Steppes) |
|---|---|---|
| top-left | top-left | **bottom-right 100%** |
| top-left | top-right | bottom-right 50% / top-right 50% |
| top-left | bottom-left | **bottom-right 100%** |
| top-right | top-left | **bottom-left 100%** |
| top-right | top-right | **bottom-left 100%** |
| top-right | bottom-right | bottom-right 50% / bottom-left 50% |
| bottom-right | top-right | **top-right 100%** |
| bottom-right | bottom-right | **top-right 100%** |
| bottom-right | bottom-left | top-right 50% / bottom-left 50% |
| bottom-left | top-left | **top-right 100%** |
| bottom-left | bottom-right | **top-right 100%** |
| bottom-left | bottom-left | **top-right 100%** |

**Short version (in running order):** the Outer Steppes → Plains of Despair exit is **opposite the Blood Moor exit**. Exceptions:
- If the Blood Moor exit is **bottom-right**, the Plains exit is **top-right** (the opposite side, top-left, is the Fortress).
- If the Cold Plains exit is **one step clockwise** from the Blood Moor exit (the three 50% rows), it's 50/50.

City of the Damned can't be predicted from Act 1 beyond its own rule in section 3.

## 5. City of the Damned → River of Flame → Chaos Sanctuary ⭐

### 5.1 City of the Damned: find the stairs (the waypoint is right next to them)
- The stairs down to River of Flame and **City of the Damned's waypoint are in the same room** (`warplava.ds1`). Find one and you've found the other.
- It's the **first thing placed** in City of the Damned, using the same biased cell shuffle as Act 1's Forgotten Tower, so it leans toward the **top corner of the map**:

```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \                ← check here first
                                            /       top 35%      \
                                          /    \              /    \
                                        /        \          /        \
                                      /            \      /            \
                                    /                \  /                \
                                  <     left 22%       X      right 22%    >
                                    \               /    \               /
                                      \           /        \           /
                                        \       /            \       /
                                          \   /                \   /
                                            \     bottom 22%     /
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

- The single most likely spot is **the cell just inside the top corner (14%)**, then along the top-right edge.
- Halves: **top-right half 56%**, **top-left half 57%**.
- It's never within 8 tiles of the map edge.

### 5.2 River of Flame: go top-right
River of Flame is a small maze (6 rooms of 24×24 tiles) with three special ends:

```
                                                      /\
                                                    /    \
                                                  /        \
                                                /            \
                                              /                \
                     Hephasto's forge 50%   /                    \  Waypoint + bridge to Chaos Sanctuary
                                          /                        \
                                        /                            \
                                      /                                \
                                    /                                    \
                                  <             River of Flame             >
                                    \                                    /
                                      \                                /
                                        \                            /
                                          \                        /
       Entrance (from City of the Damned)   \                    /  Hephasto's forge 50%
                                              \                /
                                                \            /
                                                  \        /
                                                    \    /
                                                      \/
```

- **Entrance from City of the Damned: always at the bottom-left end** of the maze (the room furthest bottom-left gets the stairs on its bottom-left side).
- **Waypoint: always at the top-right end.** It's in the first room of the bridge that runs top-right into the Chaos Sanctuary.
- **Hephasto's forge: a side branch, top-left or bottom-right (50/50).** It replaces a dead-end room that faces east or west.

**So: from the entrance, keep heading top-right.** A branch going off top-left or bottom-right may be the forge.

**When top-right is a dead end: turn left (top-left) or right (bottom-right)?**

| The route to the waypoint | Chance |
|---|---|
| Goes top-right all the way | 22% |
| First turns **left** (top-left) | 39% |
| First turns **right** (bottom-right) | 39% |

- **No preference**: left and right are equal.
- **One small clue, the forge:** if you've already seen Hephasto's forge **on one side of the entrance**, the waypoint route turns **toward that same side** more often (**44%** vs 32%). The maze tends to spread out to one side, and both the forge and the waypoint end up there.
- The route is short: usually **5–6 rooms** (24 tiles each). It never needs to go back toward the bottom-left.
- **Where the waypoint is, compared with the entrance:** usually **4 rooms toward the top-right** (3: 19%, 4: 54%, 5: 24%), and usually **shifted sideways**:

  | Shifted sideways by | 0 rooms (in line) | 1 room | 2 rooms | 3+ rooms |
  |---|---|---|---|---|
  | Chance | 28% | **42%** | 22% | 8% |

  So it's in a straight line only 28% of the time (and a fully straight walk only 22%). The usual case is **about 4 rooms top-right and 1 room to one side**.

**Source:** `D2Common/src/Drlg/DrlgMaze.cpp` → `DRLGMAZE_PlaceAct4Lava` (entrance via `DRLGMAZE_GetFreeLocationForRoomNorth`, which picks the largest Y; bridge via `DRLGMAZE_GetFreeLocationForRoomSouth`, which picks the smallest Y, then 3 rooms toward Y−; forge `R & 1` picks Forge W / Forge E); D2R map files `Act4/Diab/BridgeLava.ds1` and `Act4/Mesa/WarpLava.ds1` both contain object 0 = `WaypointOutsideAct4`; `Act4/Lava/ForgeE.ds1` / `ForgeW.ds1` contain object 42 = `SoulStoneForge`. City placement simulated over 300,000 games.

---

## 6. Source code (D2MOO)

| What | Where |
|---|---|
| Act 4 link table (Fortress → Outer Steppes → Plains of Despair → City of the Damned) | `D2Common/src/Drlg/DrlgOutPlace.cpp:81` → `gAct4OutdoorDrlgLink` |
| Outer Steppes: always east (side 3), shift `R & 1` | `sub_6FD81CA0` |
| Plains / City: side `R & 3`, then next side clockwise | `sub_6FD81380` |
| No overlap with earlier areas, backtracking | `DRLGOUTPLACE_LinkAct4Outdoors`, `sub_6FD823C0` |
| Sizes (D2R `Levels.txt`) | Fortress 32×24, Outer Steppes 80×64, Plains 64×80, City 80×64 |

## 7. Caveats
- **Source version:** D2MOO is the 1.10 code, not D2R's own program. The Act 1 → Act 4 link needs both acts to use the same seed and draw in the same order, which is true in D2MOO.
- **Not tested in game.** The Act 1 → Act 4 table is the easiest to check: note the Blood Moor and Cold Plains exits, then look at the Outer Steppes exit.
