# Act 5 Maps: Frigid Highlands, Arreat Plateau, Frozen Tundra, the Caves, Worldstone Keep

Directions are **on screen** (isometric view). From D2MOO's map code (the open-source Diablo II 1.10 rebuild) and a simulation with the real random number generator. **Not tested in game yet.**

These four are maze levels. The maze is random, but the **special rooms** (entrance, exit, side area, waypoint) are all **dead-end rooms**, and the game picks **which way each dead end faces** with a single roll, then puts the others in a fixed turn order. That gives a real rule.

**"A room faces X"** = the room is a dead end whose only opening is on side X, so you leave it heading X. You arrive at it heading the opposite way.

---

## 0. Frigid Highlands, Arreat Plateau, Frozen Tundra: predicted from Act 1 ⭐

Every act uses **the same game seed**. The random numbers that place Act 1's maps come back in Act 5:

| Act 5 map | Shape decided by | Same number in Act 1 |
|---|---|---|
| **Frigid Highlands** | 2nd number `& 1` | the Cold Plains direction |
| **Frozen Tundra** | 2nd number `& 1` (its own link list starts over) | the Cold Plains direction |
| **Arreat Plateau** | 3rd number `& 1` | Blood Moor's first roll |

Each one is either **tall** (64×160, long along the top-right / bottom-left line) or **wide** (160×64, long along the top-left / bottom-right line), and the exit is always at the **far end**:

| Map | You come in at | Tall: go | Wide: go |
|---|---|---|---|
| Frigid Highlands | bottom-right edge (from Bloody Foothills) | **top-right** end (Arreat Plateau) | **top-left** end (Arreat Plateau) |
| Arreat Plateau | the side touching Frigid Highlands | **top-right** end (Crystalline Passage) | **top-left** end (Crystalline Passage) |
| Frozen Tundra | bottom-left end (tall) / bottom-right end (wide), from Glacial Trail | **top-right** end (The Ancients' Way) | **top-left** end (The Ancients' Way) |

### The rule, in one line each
- **Frigid Highlands and Frozen Tundra run along Stony Field's line (100%).** Look at where Stony Field is around Cold Plains:
  - Stony Field **top-right or bottom-left** of Cold Plains → both are **tall** → run **top-right**.
  - Stony Field **top-left or bottom-right** of Cold Plains → both are **wide** → run **top-left**.
- **Arreat Plateau runs along Blood Moor's line (100% in 75% of games).** Same idea, using where Blood Moor is around Cold Plains:
  - Blood Moor **top-right or bottom-left** → **tall** → run **top-right**.
  - Blood Moor **top-left or bottom-right** → **wide** → run **top-left**.
  - **Exception (25% of games):** if Blood Moor is **one step clockwise from Stony Field** (Stony Field bottom-left + Blood Moor top-left, Stony Field top-left + Blood Moor top-right, Stony Field top-right + Blood Moor bottom-right, or Stony Field bottom-right + Blood Moor bottom-left), it's 50/50.

The same Act 1 numbers also set Act 4: see [act4-maps.md](act4-maps.md).

**Source:** `D2Common/src/Drlg/DrlgOutPlace.cpp` (`gAct5OutdoorDrlgLink`, `gAct5TundraDrlgLink`, `sub_6FD823C0` copies the act seed for each link list), `DrlgOutRoom.cpp` (`DRLGOUTROOM_LinkLevelsByLevelCoords`, `DRLGOUTROOM_LinkLevelsByOffsetCoords`, `DRLGOUTROOM_LinkLevelsByLevelDef`: `& 1` picks 64×160 or 160×64), `DrlgOutSiege.cpp` (`DRLGOUTSIEGE_PlaceCaves`: cave at the far end). Checked with the real random number generator over 400,000 games.

---

## 1. Crystalline Passage and The Ancients' Way

One roll (25% each) decides which way the **entrance** faces. Then, clockwise on screen:

| Role | Faces |
|---|---|
| Entrance | the rolled direction |
| **Exit** | **90° clockwise** from the entrance |
| Side area (Frozen River / Icy Cellar) | **opposite** the entrance |
| **Waypoint** | **90° counterclockwise** from the entrance |

| Entrance | Exit (to next) | Side area | Waypoint |
|---|---|---|---|
| top-right | bottom-right | bottom-left | top-left |
| bottom-right | bottom-left | top-left | top-right |
| bottom-left | top-left | top-right | bottom-right |
| top-left | top-right | bottom-right | bottom-left |

```
                                                   /\
                                                 /    \
                                               /        \
                                             /            \
                                           /                \
                              Waypoint   /                    \  Entrance
                                       /                        \
                                     /                            \
                                   /                                \
                                 /                                    \
                               < Crystalline Passage / The Ancients' Way>
                                 \                                    /
                                   \                                /
                                     \                            /
                                       \                        /
             Frozen River / Icy Cellar   \                    /  Exit (to next)
                                           \                /
                                             \            /
                                               \        /
                                                 \    /
                                                   \/
```

Example: the entrance faces top-right. Then the exit faces bottom-right, the Frozen River (or Icy Cellar) entrance faces bottom-left, and the waypoint faces top-left.

## 2. Glacial Trail

Same turn order, but there is one extra **themed room**, and the waypoint ends up **facing the same way as the entrance** (in a different dead end).

| Entrance | Exit (to next) | Side area | Themed room | Waypoint |
|---|---|---|---|---|
| top-right | bottom-right | bottom-left | top-left | top-right |
| bottom-right | bottom-left | top-left | top-right | bottom-right |
| bottom-left | top-left | top-right | bottom-right | bottom-left |
| top-left | top-right | bottom-right | bottom-left | top-left |

```
                                                   /\
                                                 /    \
                                               /        \
                                             /            \
                                           /                \
                           Themed room   /                    \  Entrance + Waypoint
                                       /                        \
                                     /                            \
                                   /                                \
                                 /                                    \
                               <              Glacial Trail             >
                                 \                                    /
                                   \                                /
                                     \                            /
                                       \                        /
                        Drifter Cavern   \                    /  Exit (to next)
                                           \                /
                                             \            /
                                               \        /
                                                 \    /
                                                   \/
```

## 3. Worldstone Keep

### 3.1 The start room
- It opens on three sides; the fourth side is a **wall**. The wall side is a 25% roll on each level.
- **No new paths ever grow from the start room**, so you only ever have those three ways out.

### 3.2 The wall moves clockwise from level to level
The three levels' seeds are consecutive (level id + game seed), and the wall is each level's **2nd random number**. The math makes the next level's wall **always 1 or 2 steps clockwise**: **1 step 58%, 2 steps 42%**, never the same side, never counterclockwise. (The 58% is exactly 1 − 0x6AC690C5 / 2³², from the random number generator itself.)

| Wall on this level | Next level: 1 step clockwise | 2 steps clockwise |
|---|---|---|
| top-left | top-right 58% | bottom-right 42% |
| top-right | bottom-right 58% | bottom-left 42% |
| bottom-right | bottom-left 58% | top-left 42% |
| bottom-left | top-left 58% | top-right 42% |

### 3.3 Know all three walls from Act 2 ⭐
The Act 2 tombs build their start room with **the same code**, and their wall is also the 2nd random number. Because the level ids differ by exactly 72, the link is almost certain:

| Look at the start-room wall of | It predicts | Chance |
|---|---|---|
| **Halls of the Dead Level 1** | Worldstone Keep **Level 1**: wall on the **opposite** side | **97%** |
| **Halls of the Dead Level 2** | Worldstone Keep **Level 2**: wall on the **opposite** side | **97%** |
| **Claw Viper Temple Level 1** | Worldstone Keep **Level 3**: wall on the **opposite** side | **97%** |

Example: Halls of the Dead 1's start room has its wall on the top-left → Worldstone Keep 1's wall is on the bottom-right.

### 3.4 Where the stairs down are
- **Close:** steps from the start room: 1 step 6%, **2 steps 27%, 3 steps 37%, 4 steps 23%**, 5+ steps 8%. So 87% are within 2–4 rooms.
- **Which of the three ways:** facing away from the wall, **left 36%, right 36%, straight 28%**. No real left/right preference, so knowing the wall only tells you which three ways exist. Try straight last.
- The stairs' facing is a separate 25% roll after the maze is built: it can't be predicted.
- **Level 2 waypoint:** faces 90° clockwise from the stairs down.

### 3.5 What doesn't link
- The **caves** (Crystalline Passage, Glacial Trail, The Ancients' Way): their exit roll comes after a variable number of maze draws. Tested over 500,000 games against everything visible in Acts 1 and 4: no effect.
- **Act 1 / Act 4 / Act 5 outdoor maps vs. Worldstone Keep:** outdoor maps use the act's link numbers; Worldstone Keep uses level-seed numbers. Those two streams don't line up, so the outdoor maps don't predict Worldstone Keep.

---

## 4. Caves: what the earlier maps can't tell you
- The four caves use their **own seeds** (level id + game seed), and the exit roll comes after a variable number of maze draws, so **earlier maps (Arreat Plateau, Frigid Highlands) don't predict them**. For where the cave exits usually are, see [act5-ice-routes.md](act5-ice-routes.md).

## 5. Source code (D2MOO)

| What | Where |
|---|---|
| Entrance / exit / side area / waypoint in turn order | `D2Common/src/Drlg/DrlgMaze.cpp` → `DRLGMAZE_PlaceAct5IceStuff` |
| Worldstone Keep start-room hub (3 rooms, 2nd level-seed number `& 3`; same function as the Act 2 tombs) | `DRLGMAZE_PlaceAct2TombPrev_Act5BaalPrev` |
| Worldstone Keep stairs down and level 2 waypoint | `DRLGMAZE_PlaceAct5BaalStuff` |
| Level seed = level id + game start seed | `DrlgDrlg.cpp:420` |
| Room preset names and sizes | D2R `LvlPrest.txt` (16×16 tiles per room) |

## 6. Caveats
- **Source version:** D2MOO is the 1.10 code, not D2R's own program.
- **Not tested in game.** The easiest checks: note which line Stony Field is on around Cold Plains, then which way Frigid Highlands runs; or note the start-room wall in Halls of the Dead 1 and in Worldstone Keep 1.
- If the maze has no dead end with the needed facing, the game adds a new room on that side. The rule still holds.
