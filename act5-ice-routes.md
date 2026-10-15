# Crystalline Passage, Glacial Trail, The Ancients' Way: Where Is the Exit?

Short answer: **the exit is almost always ahead and to the left of the entrance**, usually **close**: about **one room ahead, then one room left** is the single most common spot. Only about 1 game in 10 puts it far away.

From a simulation of D2MOO's maze code (the open-source Diablo II 1.10 rebuild) with the real random number generator, 80,000 games per level. **Not tested in game yet.** One room = 16×16 tiles.

---

## 1. How to read it

- **Ahead** = the direction the entrance corridor leads (the way you walk out of the first room).
- **Left** = 90° to the left of that, on screen.
- The entrance room is a dead end, so you always start by walking **ahead**.

On screen, "ahead + left" points toward a different corner depending on where the entrance faces:

| Entrance corridor leads | Left of it is | Exit is toward the |
|---|---|---|
| top-right | top-left | top corner |
| bottom-right | top-right | right corner |
| bottom-left | bottom-right | bottom corner |
| top-left | bottom-left | left corner |

So if the corridor leads **top-right**, look to the **top** of the map; if it leads **bottom-right**, to the **right**; and so on. The exit is that direction rotated 45° counterclockwise from the corridor.

## 2. Crystalline Passage and Glacial Trail (10 rooms)

| Where the exit room is | Chance |
|---|---|
| Ahead **and** to the left | **77%** |
| Straight ahead, in line | 8% |
| Ahead and to the right | 5% |
| Level with the entrance, to the left | 5% |
| Anywhere behind | 5% |

Exact spots, in rooms (rows = rooms ahead, columns = rooms to the left):

| ahead \ left | 1 | 2 | 3 | 4 | 5+ |
|---|---|---|---|---|---|
| 1 | 17% | 6% | 4% | 2% | 1% |
| 2 | 6% | 6% | 5% | 2% | 1% |
| 3 | 5% | 5% | 4% | 2% | 0% |
| 4 | 3% | 3% | 2% | 1% | 0% |
| 5+ | 1% | 1% | 0% | 0% | 0% |

The same picture on the isometric map, for an entrance corridor leading **top-right** (`E` = entrance room, numbers = % chance the exit room is there; rotate the picture for the other three directions):

```

                                 .
                           .           .
                     .          [ 1]         .
              [ 1]        [ 2]        [ 2]        [ 1]
        [ 1]        [ 2]        [ 4]        [ 3]        [ 1]
   .          [ 2]        [ 5]        [ 5]        [ 3]        [ 1]
        [ 1]        [ 4]        [ 6]        [ 5]        [ 2]         .
              [ 2]        [ 6]        [ 6]        [ 3]        [ 1]
                    [ 1]        [17]        [ 2]        [ 2]
                           .           .          [ 1]
                                [ E]         .
                                      [ 1]


```

- Most likely single spot: **1 ahead, 1 left, 17%**.
- Within 3 rooms ahead and 3 left: **about 58%**.
- Average route: **4.6 rooms** (about 74 tiles).

## 3. The Ancients' Way (15 rooms)

Bigger maze, so the exit is a bit farther and more spread out.

| Where the exit room is | Chance |
|---|---|
| Ahead **and** to the left | **72%** |
| Straight ahead, in line | 7% |
| Ahead and to the right | 7% |
| Level with the entrance, to the left | 5% |
| Anywhere behind | 8% |

| ahead \ left | 1 | 2 | 3 | 4 | 5+ |
|---|---|---|---|---|---|
| 1 | 11% | 4% | 3% | 2% | 2% |
| 2 | 4% | 4% | 4% | 3% | 2% |
| 3 | 3% | 4% | 4% | 2% | 1% |
| 4 | 3% | 3% | 3% | 1% | 1% |
| 5+ | 2% | 2% | 1% | 1% | 0% |

```

                                 .
                          [ 1]        [ 1]
                    [ 1]        [ 1]        [ 1]
              [ 2]        [ 2]        [ 3]        [ 2]
        [ 2]        [ 3]        [ 4]        [ 3]        [ 2]
  [ 1]        [ 2]        [ 4]        [ 4]        [ 3]        [ 2]
        [ 2]        [ 3]        [ 4]        [ 3]        [ 2]        [ 1]
              [ 1]        [ 4]        [ 4]        [ 2]        [ 2]
                    [ 1]        [11]        [ 2]        [ 2]
                           .           .          [ 1]
                                [ E]         .
                                      [ 2]


```

- Most likely single spot: **1 ahead, 1 left, 11%**.
- Within 3 rooms ahead and 3 left: **about 42%**.
- Average route: **5.6 rooms** (about 89 tiles).

## 4. The route

The maze has loops, so there can be more than one way, but the shortest route usually looks like this (F = ahead, L = left, one letter per room):

| Route shape | Crystalline / Glacial | Ancients' Way |
|---|---|---|
| Straight ahead some rooms, then left some rooms (`FL`, `FFL`, `FLL`...) | **48%** | 38% |
| Zig-zags ahead and left (`FLFL`...) | 24% | 26% |
| Needs a right turn or a step back | 28% | 36% |

Rule of thumb: **leave the entrance, then turn left at the first chance and keep bearing left.** But about 1 game in 4 (1 in 3 on the Ancients' Way) needs a right turn or a step back, so if the left side is a dead end, try the other way.

## 5. Other rooms, for reference
The waypoint is **ahead and to the right** (Crystalline Passage 87%, Ancients' Way 84% on the right), the side area (Frozen River / Icy Cellar) is **straight ahead, 2–4 rooms away**, and Glacial Trail's waypoint sits **beside** the entrance (level, left or right about half each). See [act5-caves.md](act5-caves.md) for the facing rule.

## 6. Source code (D2MOO)

| What | Where |
|---|---|
| Starting 2×2 loop of 4 rooms, then rooms added until 10 (Crystalline, Glacial) or 15 (Ancients' Way) | `D2Common/src/Drlg/DrlgMaze.cpp` → `DRLGMAZE_InitBasicMazeLayout`, `DRLGMAZE_BuildBasicMaze` |
| 50% chance to link touching rooms (loops) | `DRLGMAZE_MergeMazeRooms` (`Merge` 500 in `LvlMaze.txt`) |
| Entrance / exit / side area / waypoint picked among dead ends | `DRLGMAZE_PlaceAct5IceStuff` |
| Room counts and sizes | D2R `LvlMaze.txt` (10 / 10 / 15 rooms, 16×16 tiles) |

## 7. Caveats
- **Source version:** D2MOO is the 1.10 code, not D2R's own program.
- **Not tested in game.** To check it, note which way the entrance corridor leads and where the exit turns out to be, over about 20 runs.
- The route shapes use the shortest path in the room graph. Doors inside a room can make the real walk a bit different.
